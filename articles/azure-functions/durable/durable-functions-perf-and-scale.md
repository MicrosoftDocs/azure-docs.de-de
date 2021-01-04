---
title: Leistung und Skalierbarkeit in Durable Functions – Azure
description: Erfahren Sie mehr über die einmaligen Skalierungsmerkmale der Durable Functions-Erweiterung für Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: b9fc465b5e5f132264fd36e004fa3ee7623b87a5
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854987"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Leistung und Skalierbarkeit in Durable Functions (Azure Functions)

Um die Leistung und Skalierbarkeit zu optimieren, ist es wichtig die eindeutigen Merkmale von [Durable Functions](durable-functions-overview.md) zu verstehen.

Zum Verständnis des Skalierungsverhaltens müssen Sie einige Details der zugrunde liegenden Azure Storage-Anbieter verstehen.

## <a name="history-table"></a>Verlaufstabelle

Die **Verlaufstabelle** ist eine Azure Storage-Tabelle, die die Verlaufsereignisse für alle Orchestrierungsinstanzen innerhalb eines Aufgabenhubs enthält. Der Name dieser Tabelle hat das Format „*Aufgabenhubname* History“. Während Instanzen ausgeführt werden, werden dieser Tabelle neue Zeilen hinzugefügt. Der Partitionsschlüssel dieser Tabelle wird von der Instanzen-ID der Orchestrierung abgeleitet. Eine Instanz-ID wird in den meisten Fällen auf Zufallsbasis generiert, um die optimale Verteilung von internen Partitionen in Azure Storage sicherzustellen.

Wenn eine Orchestrierungsinstanz ausgeführt werden muss, werden die entsprechenden Zeilen der Verlaufstabelle in den Speicher geladen. Diese *Verlaufsereignisse* werden dann im Orchestratorfunktionscode erneut wiedergegeben, um diesen wieder in den Zustand des vorherigen Prüfpunkts zu versetzen. Die Verwendung des Ausführungsverlaufs zum Neuerstellen des Zustands auf diese Weise hängt vom [Ereignissourcingmuster](/azure/architecture/patterns/event-sourcing) ab.

## <a name="instances-table"></a>Instanzentabellen

Die **Instanzentabelle** ist eine andere Azure Storage-Tabelle, die die Statuswerte aller Orchestrierungs- und Entitätsinstanzen innerhalb eines Aufgabenhubs enthält. Während der Erstellung von Instanzen werden dieser Tabelle neue Zeilen hinzugefügt. Der Partitionsschlüssel dieser Tabelle ist die Orchestrierungsinstanz-ID oder der Entitätsschlüssel, und der Zeilenschlüssel ist eine leere Zeichenfolge. Es gibt eine Zeile pro Orchestrierungs- oder Entitätsinstanz.

Diese Tabelle wird verwendet, um Instanzabfrageanforderungen von `GetStatusAsync`- (.NET) und `getStatus`-APIs (JavaScript) sowie der [Statusabfrage-HTTP-API](durable-functions-http-api.md#get-instance-status) zu verarbeiten. Sie wird letztendlich konsistent mit dem Inhalt der oben genannten **Verlaufstabelle** gehalten. Die Verwendung einer separaten Azure Storage-Tabelle zur wirksamen Erfüllung von Instanzabfragevorgängen auf diese Weise hängt vom [CQRS-Muster (Command and Query Responsibility Segregation)](/azure/architecture/patterns/cqrs) ab.

## <a name="internal-queue-triggers"></a>Interne Warteschlangentrigger

Sowohl Orchestrator- als auch Aktivitätsfunktionen werden durch interne Warteschlangen im Aufgabenhub der Funktions-App ausgelöst. Diese Verwendung von Warteschlangen bietet eine zuverlässige „At-Least-Once“-Garantie bei der Nachrichtenübermittlung. Es gibt zwei Arten von Warteschlangen in Durable Functions: die **Steuerelement-Warteschlange** ( control queue) und die **Warteschlange für Arbeitsaufgaben** (work-item queue).

### <a name="the-work-item-queue"></a>Warteschlange für Arbeitsaufgaben

In Durable Functions gibt es eine Warteschlange für Arbeitsaufgaben pro Aufgabenhub. Dies ist eine grundlegende Warteschlange, und sie verhält sich ähnlich wie jede andere `queueTrigger`-Warteschlange in Azure Functions. Diese Warteschlange wird verwendet, um zustandslose *Aktivitätsfunktionen* auszulösen. Dabei wird jeweils eine einzelne Nachricht aus der Warteschlange entfernt. Jede dieser Nachrichten enthält Eingaben der Aktivitätsfunktionen und zusätzliche Metadaten, z.B. welche Funktion auszuführen ist. Wenn eine Durable Functions-Anwendung für mehrere virtuelle Computer horizontal hochskaliert wird, konkurrieren alle diese virtuellen Computer miteinander, um Arbeitsaufgaben aus der Warteschlange für Arbeitsaufgaben abzurufen.

### <a name="control-queues"></a>Steuerelement-Warteschlange(n)

In Durable Functions gibt es mehrere *Steuerelement-Warteschlangen* pro Aufgabenhub. Eine *Steuerelement-Warteschlange* ist komplexer als die einfachere Warteschlange für Arbeitsaufgaben. Steuerwarteschlangen werden verwendet, um zustandsbehaftete Orchestrator- und Entitätsfunktionen auszulösen. Da die Orchestrator- und Entitätsfunktionsinstanzen zustandsbehaftete Singletons sind, ist es nicht möglich, ein konkurrierendes Consumermodell zu verwenden, um die Last zwischen virtuellen Computern zu verteilen. Vielmehr wird bei Orchestrator- und Entitätsnachrichten die Last auf die Steuerwarteschlangen verteilt. Weitere Informationen zu diesem Verhalten finden Sie in den folgenden Abschnitten.

Steuerelement-Warteschlangen enthalten eine Vielzahl von Orchestrierung-Lebenszyklus-Nachrichtentypen. Beispiele hierfür sind [orchestrator control messages](durable-functions-instance-management.md) (Orchestratorsteuerungsmeldungen), Aktivitätsfunktion-*Antwort*-Nachrichten und Timernachrichten. Bis zu 32 Nachrichten werden in einer einzelnen Abfrage aus einer Steuerelement-Warteschlange entfernt. Diese Nachrichten enthalten Nutzlastdaten sowie Metadaten, einschließlich der Orchestrierungsinstanz, für die eine Nachricht jeweils vorgesehen ist. Wenn mehrere aus der Warteschlange entfernte Nachrichten für dieselbe Orchestrierungsinstanz vorgesehen sind, werden sie als Batch verarbeitet.

### <a name="queue-polling"></a>Abrufen von Warteschlangen

Die Durable Task-Erweiterung implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abrufs von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren. Wenn eine Nachricht gefunden wird, überprüft die Runtime sofort, ob eine andere Nachricht vorhanden ist. Wenn keine Nachricht gefunden wird, wird der Vorgang nach einem bestimmten Zeitraum wiederholt. Nach aufeinander folgenden fehlerhaften Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit (standardmäßig eine 30 Sekunden) erreicht ist.

Die maximale Abrufverzögerung kann über die Eigenschaft `maxQueuePollingInterval` in der Datei [host.json](../functions-host-json.md#durabletask) konfiguriert werden. Die Festlegung dieser Eigenschaft auf einen höheren Wert kann zu höherer Latenz bei der Nachrichtenverarbeitung führen. Eine höhere Latenz wird nur nach Zeiträumen ohne Aktivität erwartet. Die Festlegung dieser Eigenschaft auf einen niedrigeren Wert kann aufgrund verstärkter Speichertransaktionen zu höheren Speicherkosten führen.

> [!NOTE]
> Bei der Ausführung in Verbrauchs- und Premium-Tarifen von Azure Functions ruft der [Azure Functions-Skalierungscontroller](../functions-scale.md#how-the-consumption-and-premium-plans-work) alle Steuerelement- und Arbeitselement-Warteschlangen einmal alle 10 Sekunden ab. Dieser zusätzliche Abruf ist erforderlich, um zu ermitteln, wann die Instanzen der Funktions-Apps aktiviert und Skalierungsentscheidungen getroffen werden sollen. Zum Zeitpunkt der Erstellung dieses Artikels ist dieses Intervall von 10 Sekunden konstant und kann nicht konfiguriert werden.

### <a name="orchestration-start-delays"></a>Verzögerungen beim Starten der Orchestrierung
Orchestrierungsinstanzen werden gestartet, indem eine `ExecutionStarted`-Nachricht zu einer der Steuerungswarteschlangen des Taskhubs hinzugefügt wird. Unter bestimmten Umständen sind möglicherweise Verzögerungen von mehreren Sekunden zwischen der geplanten Ausführung einer Orchestrierung und der tatsächlichen Ausführung zu beobachten. Während dieses Zeitraums verbleibt die Orchestrierungsinstanz im Zustand `Pending`. Für diese Verzögerungen gibt es zwei mögliche Gründe:

1. **Steuerungswarteschlangen im Rückstand:** Wenn die Steuerungswarteschlange für diese Instanz eine große Anzahl von Nachrichten enthält, kann es eine Weile dauern, bis die `ExecutionStarted`-Nachricht von der Runtime empfangen und verarbeitet wird. Nachrichtenrückstände können entstehen, wenn Orchestrierungen viele Ereignisse gleichzeitig verarbeiten. Ereignisse, die zur Steuerungswarteschlange hinzugefügt werden, umfassen Startereignisse für Orchestrierungen, den Abschluss von Aktivitäten, permanente Timer, die Beendigung und externe Ereignisse. Wenn diese Verzögerungen unter normalen Umständen auftreten, sollten Sie das Erstellen eines neuen Taskhubs mit einer größeren Anzahl von Partitionen in Erwägung ziehen. Das Konfigurieren von mehr Partitionen bewirkt, dass die Runtime weitere Steuerungswarteschlangen für die Lastverteilung erstellt.

2. **Verzögerungen durch Backoffabruf:** Eine weitere häufige Ursache für Verzögerungen bei Orchestrierungen ist das [zuvor beschriebene Backoffabrufverhalten für Steuerungswarteschlangen](#queue-polling). Diese Verzögerung wird jedoch nur erwartet, wenn eine App auf zwei oder mehr Instanzen aufskaliert wird. Wenn nur eine App-Instanz vorhanden ist oder die App-Instanz, die die Orchestrierung startet, die Instanz ist, die auch die Zielsteuerungswarteschlange abruft, entstehen bei Warteschlangenabrufen keine Verzögerungen. Sie können Verzögerungen durch Backoffabruf reduzieren, indem Sie die Einstellungen für **host.json** wie zuvor beschrieben aktualisieren.

## <a name="storage-account-selection"></a>Auswahl des Speicherkontos

Die in Durable Functions verwendeten Warteschlangen, Tabellen und Blobs werden in einem konfigurierten Azure Storage-Konto erstellt. Das zu verwendende Konto kann über die Einstellung `durableTask/storageProvider/connectionStringName` (oder die Einstellung `durableTask/azureStorageConnectionStringName` in Durable Functions 1.x) in der Datei **host.json** angegeben werden.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

Wenn kein Konto angegeben ist, wird das Standardspeicherkonto `AzureWebJobsStorage` verwendet. Bei leistungsabhängigen Workloads wird jedoch die Konfiguration eines nicht standardmäßigen Speicherkontos empfohlen. In Durable Functions wird Azure Storage stark ausgelastet. Durch die Nutzung eines dedizierten Speicherkontos wird die Speicherverwendung durch Durable Functions von der internen Verwendung durch den Azure Functions-Host getrennt.

## <a name="orchestrator-scale-out"></a>Horizontales Skalieren des Orchestrators

Aktivitätsfunktionen sind zustandslos, und die horizontale Hochskalierung wird automatisch durch Hinzufügen von virtuellen Computern ausgeführt. Orchestratorfunktionen und Entitäten dagegen sind über mindestens eine Steuerwarteschlange *partitioniert*. Die Anzahl der Steuerelement-Warteschlangen wird in der Datei **host.json** definiert. Mit dem folgenden Beispielcodeausschnitt in „host.json“ wird die `durableTask/storageProvider/partitionCount`-Eigenschaft (oder `durableTask/partitionCount` in Durable Functions 1.x) auf `3` festgelegt.

### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
          "partitionCount": 3
      }
    }
  }
}
```

### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

Ein Aufgabenhub kann mit 1 bis 16 Partitionen konfiguriert werden. Wenn keine Anzahl angegeben ist, werden standardmäßig **4** Partitionen verwendet.

Wenn mehrere Funktionshostinstanzen (in der Regel auf verschiedenen virtuellen Computern) horizontal hochskaliert werden, erhält jede Instanz eine Sperre auf einer der Steuerelement-Warteschlangen. Diese Sperren werden intern als Blob Storage-Leases implementiert und stellen sicher, dass eine Orchestrierungsinstanz oder Entität jeweils nur in einer einzelnen Hostinstanz ausgeführt wird. Wenn ein Aufgabenhub mit drei Steuerwarteschlangen konfiguriert ist, kann die Last bei Orchestrierungsinstanzen und Entitäten auf bis zu drei virtuelle Computer verteilt werden. Zusätzliche virtuelle Computer können zum Erhöhen der Kapazität für die Ausführung der Aktivitätsfunktion hinzugefügt werden.

Das folgende Diagramm veranschaulicht, wie der Azure Functions-Host mit den Speicherentitäten in einer horizontal skalierten Umgebung interagiert.

![Skalierungsdiagramm](./media/durable-functions-perf-and-scale/scale-diagram.png)

Wie im Diagramm oben dargestellt, konkurrieren alle virtuellen Computer um Nachrichten in der Warteschlange für Arbeitsaufgaben. Allerdings können nur drei virtuelle Computer Nachrichten aus Steuerelement-Warteschlangen abrufen, und jeder virtuelle Computer sperrt eine einzige Steuerelement-Warteschlange.

Orchestrierungsinstanzen und Entitäten sind auf alle Steuerwarteschlangeninstanzen verteilt. Die Verteilung erfolgt durch Ausführen einer Hashfunktion mit der Instanz-ID der Orchestrierung oder des Name-Schlüssel-Paars der Entität. Orchestrierungsinstanz-IDs sind standardmäßig zufällige GUIDs, sodass sichergestellt ist, dass Instanzen gleichmäßig auf alle Steuerearteschlangen verteilt werden.

Im Allgemeinen sollten Orchestratorfunktionen einfach sein und keine große Rechenleistung in Anspruch nehmen. Daher ist es nicht erforderlich, eine große Anzahl von Steuerwarteschlange-Partitionen zu erstellen, um einen erhöhten Durchsatz für Orchestrierungen zu erzielen. Der größte Teil der intensiven Vorgänge sollte in zustandslosen Aktivitätsfunktionen erfolgen, die unbegrenzt horizontal hochskaliert werden können.

## <a name="auto-scale"></a>Automatische Skalierung

Wie bei allen Azure Functions-Vorgängen, die im Nutzungsplan oder Tarifen des Typs „Elastisch Premium“ ausgeführt werden, unterstützt Durable Functions die automatische Skalierung über den [Azure Functions-Skalierungscontroller](../functions-scale.md#runtime-scaling). Der Skalierungscontroller überwacht die Latenz aller Warteschlangen, indem regelmäßig _Peek_-Befehle ausgegeben werden. Basierend auf den Latenzen der eingesehenen Nachrichten legt der Skalierungscontroller fest, ob virtuelle Computer hinzugefügt oder entfernt werden.

Wenn der Skalierungscontroller bestimmt, dass die Latenzen der Nachrichten der Steuerelement-Warteschlangen zu hoch sind, werden VM-Instanzen hinzugefügt, bis die Nachrichtenlatenz auf ein annehmbares Maß verringert ist oder bis die Partitionsanzahl der Steuerelement-Warteschlangen erreicht ist. Auf ähnliche Weise fügt der Skalierungscontroller kontinuierlich VM-Instanzen hinzu, wenn Latenzen der Warteschlangen für Arbeitsaufgaben hoch sind, jedoch unabhängig von der Partitionsanzahl.

> [!NOTE]
> Ab Durable Functions 2.0 können Funktions-Apps so konfiguriert werden, dass Sie in VNET-geschützten Dienstendpunkten im Tarif „Elastisch Premium“ ausgeführt werden. In dieser Konfiguration initiieren die Durable Functions-Trigger Skalierungsanforderungen anstelle des Skalierungscontrollers.

## <a name="thread-usage"></a>Verwendung von Threads

Orchestratorfunktionen werden in einem einzelnen Thread ausgeführt, um sicherzustellen, dass die Ausführung in vielen Wiedergaben deterministisch sein kann. Aufgrund dieser Singlethreadausführung ist es wichtig, dass Orchestratorfunktionsthreads auf keinen Fall CPU-intensive Aufgaben, E/A-Vorgänge oder Blockierungsvorgänge ausführen. Alle Vorgänge, die möglicherweise E/A, die Blockierung oder mehrere Threads erfordern, sollten in die Aktivitätsfunktion verschoben werden.

Aktivitätsfunktionen haben alle dasselbe Verhalten, als reguläre, durch Warteschlangen ausgelöste Funktionen. Sie können problemlos E/A-Vorgänge und CPU-intensive Vorgänge ausführen sowie mehrere Threads verwenden. Da Aktivitätsauslöser zustandslos sind, können sie jederzeit auf eine unbegrenzte Anzahl von virtuellen Computern aufskaliert werden.

Entitätsfunktionen werden ebenfalls in einem einzelnen Thread ausgeführt, und Vorgänge werden nacheinander verarbeitet. Für Entitätsfunktionen gibt es jedoch keine Einschränkungen für den Typ des Codes, der ausgeführt werden kann.

## <a name="concurrency-throttles"></a>Drosselungen der Parallelität

In Azure Functions können mehrere Funktionen gleichzeitig in einer einzelnen App-Instanz ausgeführt werden. Diese gleichzeitige Ausführung trägt zur Erhöhung der Parallelität und Verringerung der Anzahl von „Kaltstarts“ bei, die bei einer typischen App mit der Zeit auftreten. Hohe Parallelität kann jedoch Systemressourcen pro VM (z.B. Netzwerkverbindungen oder den verfügbaren Arbeitsspeicher) erschöpfen. Je nach den Anforderungen der Funktions-App kann es erforderlich sein, die Parallelität pro Instanz zu drosseln, um zu verhindern, dass in Situationen mit hohen Auslastungen nicht genügend Speicher vorhanden ist.

Parallelitätslimits für Aktivitäts-, Orchestrator- und Entitätsfunktionen können in der Datei **host.json** konfiguriert werden. Die relevanten Einstellungen sind `durableTask/maxConcurrentActivityFunctions` für Aktivitätsfunktionen und `durableTask/maxConcurrentOrchestratorFunctions` sowohl für Orchestrator- als auch für Entitätsfunktionen.

### <a name="functions-20"></a>Functions 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

Im vorherigen Beispiel können maximal 10 Orchestrator- oder Entitätsfunktionen und 10 Aktivitätsfunktionen gleichzeitig auf einem einzelnen virtuellen Computer ausgeführt werden. Wenn kein Wert angegeben wird, ist die Anzahl der gleichzeitigen Ausführungen von Orchestrator- oder Aktivitätsfunktionen auf das Zehnfache der Anzahl von Kernen auf dem virtuellen Computer begrenzt.

> [!NOTE]
> Diese Einstellungen sind nützlich bei der Verwaltung der Speicher- und CPU-Auslastung auf einem einzelnen virtuellen Computer. Bei der horizontalen Hochskalierung auf mehrere VMs sind jedoch für jede VM jeweils eigene Limits festgelegt. Mit diesen Einstellungen kann die Parallelität nicht auf globaler Ebene gesteuert werden.

## <a name="extended-sessions"></a>Erweiterte Sitzungen

„Erweiterte Sitzungen“ ist eine Einstellung, die Orchestrierungen und Entitäten auch im Speicher beibehält, nachdem die Verarbeitung von Nachrichten abgeschlossen wurde. Die typischen Auswirkungen der Aktivierung von erweiterten Sitzungen sind reduzierte E/A-Vorgänge für das Azure Storage-Konto und ein allgemeiner verbesserter Durchsatz.

Sie können erweiterte Sitzungen aktivieren, indem `durableTask/extendedSessionsEnabled` in der Datei **host.json** auf `true` festgelegt wird. Mit der Einstellung `durableTask/extendedSessionIdleTimeoutInSeconds` kann gesteuert werden, wie lange eine Leerlaufsitzung im Arbeitsspeicher verbleibt:

**Functions 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

Es gibt zwei mögliche Nachteile dieser Einstellung, die Sie beachten sollten:

1. Es erfolgt eine allgemeine Zunahme der Speicherauslastung von Funktions-Apps.
2. Es kann eine allgemeine Verringerung des Durchsatzes bei der Ausführung von vielen gleichzeitigen, Orchestrator- oder Entitätsfunktionen mit kurzer Dauer auftreten.

Wenn `durableTask/extendedSessionIdleTimeoutInSeconds` beispielsweise auf 30 Sekunden festgelegt ist, belegt eine Orchestrator- oder Entitätsfunktionsfolge mit kurzer Dauer, die in weniger als 1 Sekunde ausgeführt wird, dennoch 30 Sekunden lang den Arbeitsspeicher. Zudem wird sie auf das zuvor erwähnte Kontingent `durableTask/maxConcurrentOrchestratorFunctions` angerechnet, sodass möglicherweise verhindert wird, dass andere Orchestrator- oder Entitätsfunktionen ausgeführt werden.

Die spezifischen Auswirkungen von erweiterten Sitzungen auf Orchestrator- und Entitätsfunktionen werden in den nächsten Abschnitten beschrieben.

> [!NOTE]
> Erweiterte Sitzungen werden derzeit nur in .NET-Programmiersprachen wie C# oder F# unterstützt. Wenn Sie `extendedSessionsEnabled` für andere Plattformen auf `true` festlegen, kann dies zu Laufzeitproblemen führen. So kann z. B. die Ausführung von durch Aktivitäten und Orchestrierung ausgelöste Funktionen ohne Meldung Fehler verursachen.


### <a name="orchestrator-function-replay"></a>Wiedergabe von Orchestratorfunktionen

Wie zuvor erwähnt, werden Orchestratorfunktionen unter Verwendung der Inhalte der **Verlaufstabelle** wiedergegeben. Standardmäßig wird der Orchestratorfunktionscode immer dann wiedergegeben, wenn ein Batch von Nachrichten aus einer Steuerelement-Warteschlange entfernt wird. Auch wenn Sie das Muster „Auffächern nach außen/innen“ verwenden und den Abschluss aller Aufgaben erwarten (z. B. mit `Task.WhenAll` in .NET oder `context.df.Task.all` in JavaScript), wird es zu Wiedergaben kommen, die auftreten, wenn Batches von Aufgaben im Laufe der Zeit verarbeitet werden. Wenn erweiterte Sitzungen aktiviert sind, verbleiben Orchestratorfunktionsinstanzen länger im Arbeitsspeicher, und neue Nachrichten können ohne eine vollständige Verlaufswiedergabe verarbeitet werden.

Die Leistungsverbesserung erweiterter Sitzungen wird in den folgenden Situationen am häufigsten beobachtet:

* Wenn eine begrenzte Anzahl von Orchestrierungsinstanzen gleichzeitig ausgeführt wird.
* Wenn Orchestrierungen eine große Anzahl sequenzieller Aktionen (z.B. Hunderte von Aktivitätsfunktionsaufrufen) aufweisen, die schnell abgeschlossen werden.
* Wenn Orchestrierungen eine große Anzahl von Aktionen nach außen und innen auffächern, die ungefähr zur gleichen Zeit abgeschlossen werden.
* Wenn Orchestratorfunktionen große Nachrichten verarbeiten oder CPU-intensive Datenverarbeitung ausführen müssen.

In allen anderen Fällen gibt es in der Regel keine beobachtbare Leistungsverbesserung für Orchestratorfunktionen.

> [!NOTE]
> Diese Einstellungen sollten nur verwendet werden, nachdem eine Orchestratorfunktion vollständig entwickelt und getestet wurde. Das standardmäßige aggressive Wiedergabeverhalten kann nützlich sein, um Verstöße gegen die [Einschränkungen des Orchestratorfunktionscodes](durable-functions-code-constraints.md) zur Entwicklungszeit zu erkennen, und ist daher standardmäßig deaktiviert.

### <a name="entity-function-unloading"></a>Entladen von Entitätsfunktionen

Entitätsfunktionen verarbeiten bis zu 20 Vorgänge in einem einzelnen Batch. Sobald eine Entität die Verarbeitung eines Batches von Vorgängen beendet, behält sie ihren Zustand bei und wird aus dem Arbeitsspeicher entladen. Mithilfe der Einstellung für erweiterte Sitzungen können Sie das Entladen von Entitäten aus dem Arbeitsspeicher verzögern. Entitäten behalten ihre Zustandsänderungen weiterhin wie zuvor bei, verbleiben jedoch für den konfigurierten Zeitraum im Arbeitsspeicher, um die Anzahl der Ladevorgänge aus Azure Storage zu verringern. Durch diese Verringerung der Ladevorgänge aus Azure Storage kann der Gesamtdurchsatz von häufig verwendeten Entitäten verbessert werden.

## <a name="performance-targets"></a>Leistungsziele

Bei der Planung der Verwendung von Durable Functions für eine Produktionsanwendung müssen die Leistungsanforderungen frühzeitig im Planungsprozess berücksichtigt werden. In diesem Abschnitt werden einige allgemeine Verwendungsszenarien und die erwarteten maximalen Durchsatzzahlen behandelt.

* **Sequenzielle Aktivitätsausführung:** Dieses Szenario beschreibt eine Orchestratorfunktion, mit der nacheinander eine Reihe von Aktivitätsfunktionen ausgeführt wird. Am ehesten ähnelt es dem Beispiel zur [Funktionsverkettung](durable-functions-sequence.md).
* **Parallele Aktivitätsausführung:** Dieses Szenario beschreibt eine Orchestratorfunktion, mit der mithilfe des Musters [Auffächern nach außen/innen](durable-functions-cloud-backup.md) viele Aktivitätsfunktionen parallel ausgeführt werden.
* **Parallele Antwortverarbeitung:** Dieses Szenario stellt die zweite Hälfte des Musters [Auffächern nach außen/innen](durable-functions-cloud-backup.md) dar. Es konzentriert sich auf die Leistung der Auffächerung nach innen. Es muss beachtet werden, dass im Unterschied zur Auffächerung nach außen die Auffächerung nach innen durch eine einzelne Orchestratorfunktionsinstanz erfolgt und daher nur auf einem einzelnen virtuellen Computer ausgeführt werden kann.
* **Externe Ereignisverarbeitung:** Dieses Szenario stellt eine einzelne Orchestratorfunktionsinstanz dar, die nacheinander auf [externe Ereignisse](durable-functions-external-events.md) wartet.
* **Verarbeitung von Entitätsvorgängen**: In diesem Szenario wird getestet, wie schnell eine _einzelne_ [Indikatorentität](durable-functions-entities.md) einen konstanten Stream von Vorgängen verarbeiten kann.

> [!TIP]
> Im Gegensatz zur Auffächerung nach außen sind Vorgänge zur Auffächerung nach innen auf einen einzelnen virtuellen Computer beschränkt. Wenn in Ihrer Anwendung das Muster „Auffächern nach außen/innen“ verwendet wird und Sie die Leistung im Hinblick auf die Auffächerung nach innen optimieren möchten, können Sie die Auffächerung der Aktivitätsfunktionen nach außen in mehrere [untergeordnete Orchestrierungen](durable-functions-sub-orchestrations.md) unterteilen.

In der folgenden Tabelle sind die erwarteten *maximalen* Durchsatzzahlen für die zuvor beschriebenen Szenarien aufgeführt. „Instanz“ bezieht sich auf eine einzelne Instanz einer Orchestratorfunktion, die auf einem einzelnen kleinen ([A1](../../virtual-machines/sizes-previous-gen.md)) virtuellen Computer in Azure App Service ausgeführt wird. In allen Fällen wird angenommen, dass [erweiterte Sitzungen](#orchestrator-function-replay) aktiviert sind. Die tatsächlichen Ergebnisse können je nach den durch den Funktionscode ausgeführten CPU- oder E/A-Vorgängen variieren.

| Szenario | Maximaler Durchsatz |
|-|-|
| Sequenzielle Aktivitätsausführung | 5 Aktivitäten pro Sekunde pro Instanz |
| Parallele Aktivitätsausführung (Auffächern nach außen) | 100 Aktivitäten pro Sekunde pro Instanz |
| Parallele Antwortverarbeitung (Auffächern nach innen) | 150 Antworten pro Sekunde pro Instanz |
| Externe Ereignisverarbeitung | 50 Ereignisse pro Sekunde pro Instanz |
| Verarbeitung von Entitätsvorgängen | 64 Vorgänge pro Sekunde |

> [!NOTE]
> Diese Zahlen gelten ab der Durable Functions-Erweiterung v1.4.0 (allgemeine Verfügbarkeit). Diese Zahlen können sich im Verlauf der Entwicklung und Optimierung des Features ändern.

Wenn Sie nicht die erwarteten Durchsatzzahlen erreichen und die CPU- und die Speicherauslastung fehlerfrei zu sein scheinen, überprüfen Sie, ob die Ursache auf die [Integrität Ihres Speicherkontos](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) zurückzuführen ist. Die Durable Functions-Erweiterung kann eine beträchtliche Auslastung eines Azure Storage-Kontos verursachen, und entsprechend hohe Auslastungen können zu einer Drosselung des Speicherkontos führen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über Notfallwiederherstellung und geografische Verteilung](durable-functions-disaster-recovery-geo-distribution.md)
