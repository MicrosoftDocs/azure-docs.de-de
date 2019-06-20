---
title: Leistung und Skalierbarkeit in Durable Functions – Azure
description: Einführung in die Durable Functions-Erweiterung für Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: e6ae4cc527ae0828f530ab7f3904d2b3c64c910b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60733238"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Leistung und Skalierbarkeit in Durable Functions (Azure Functions)

Um die Leistung und Skalierbarkeit zu optimieren, ist es wichtig die eindeutigen Merkmale von [Durable Functions](durable-functions-overview.md) zu verstehen.

Zum Verständnis des Skalierungsverhaltens müssen Sie einige Details der zugrunde liegenden Azure Storage-Anbieter verstehen.

## <a name="history-table"></a>Verlaufstabelle

Die **Verlaufstabelle** ist eine Azure Storage-Tabelle, die die Verlaufsereignisse für alle Orchestrierungsinstanzen innerhalb eines Aufgabenhubs enthält. Der Name dieser Tabelle hat das Format „*Aufgabenhubname*History“. Während Instanzen ausgeführt werden, werden dieser Tabelle neue Zeilen hinzugefügt. Der Partitionsschlüssel dieser Tabelle wird von der Instanzen-ID der Orchestrierung abgeleitet. Eine Instanz-ID wird in den meisten Fällen auf Zufallsbasis generiert, um die optimale Verteilung von internen Partitionen in Azure Storage sicherzustellen.

Wenn eine Orchestrierungsinstanz ausgeführt werden muss, werden die entsprechenden Zeilen der Verlaufstabelle in den Speicher geladen. Diese *Verlaufsereignisse* werden dann im Orchestratorfunktionscode erneut wiedergegeben, um diesen wieder in den Zustand des vorherigen Prüfpunkts zu versetzen. Die Verwendung des Ausführungsverlaufs zum Neuerstellen des Zustands auf diese Weise hängt vom [Ereignissourcingmuster](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) ab.

## <a name="instances-table"></a>Instanzentabellen

Die **Instanzentabelle** ist eine andere Azure Storage-Tabelle, die die Statuswerte aller Orchestrierungsinstanzen innerhalb eines Aufgabenhubs enthält. Während der Erstellung von Instanzen werden dieser Tabelle neue Zeilen hinzugefügt. Der Partitionsschlüssel dieser Tabelle ist die Orchestrierungsinstanz-ID, und der Zeilenschlüssel ist eine feste Konstante. Es gibt eine Zeile pro Orchestrierungsinstanz.

Diese Tabelle wird verwendet, um Instanzabfrageanforderungen über die [GetStatusAsync-API](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_System_String_) (.NET), die `getStatus`-API (JavaScript) und über die [HTTP-Statusabfrage-API](durable-functions-http-api.md#get-instance-status) zu erfüllen. Sie wird letztendlich konsistent mit dem Inhalt der oben genannten **Verlaufstabelle** gehalten. Die Verwendung einer separaten Azure Storage-Tabelle zur wirksamen Erfüllung von Instanzabfragevorgängen auf diese Weise hängt vom [CQRS-Muster (Command and Query Responsibility Segregation)](https://docs.microsoft.com/azure/architecture/patterns/cqrs) ab.

## <a name="internal-queue-triggers"></a>Interne Warteschlangentrigger

Sowohl Orchestrator- als auch Aktivitätsfunktionen werden durch interne Warteschlangen im Aufgabenhub der Funktions-App ausgelöst. Diese Verwendung von Warteschlangen bietet eine zuverlässige „At-Least-Once“-Garantie bei der Nachrichtenübermittlung. Es gibt zwei Arten von Warteschlangen in Durable Functions: die **Steuerelement-Warteschlange** ( control queue) und die **Warteschlange für Arbeitsaufgaben** (work-item queue).

### <a name="the-work-item-queue"></a>Warteschlange für Arbeitsaufgaben

In Durable Functions gibt es eine Warteschlange für Arbeitsaufgaben pro Aufgabenhub. Dies ist eine grundlegende Warteschlange, und sie verhält sich ähnlich wie jede andere `queueTrigger`-Warteschlange in Azure Functions. Diese Warteschlange wird verwendet, um zustandslose *Aktivitätsfunktionen* auszulösen. Dabei wird jeweils eine einzelne Nachricht aus der Warteschlange entfernt. Jede dieser Nachrichten enthält Eingaben der Aktivitätsfunktionen und zusätzliche Metadaten, z.B. welche Funktion auszuführen ist. Wenn eine Durable Functions-Anwendung für mehrere virtuelle Computer horizontal hochskaliert wird, konkurrieren alle diese virtuellen Computer miteinander, um Arbeitsaufgaben aus der Warteschlange für Arbeitsaufgaben abzurufen.

### <a name="control-queues"></a>Steuerelement-Warteschlange(n)

In Durable Functions gibt es mehrere *Steuerelement-Warteschlangen* pro Aufgabenhub. Eine *Steuerelement-Warteschlange* ist komplexer als die einfachere Warteschlange für Arbeitsaufgaben. Steuerelement-Warteschlangen werden verwendet, um zustandsbehaftete Orchestratorfunktionen auszulösen. Da die Orchestratorfunktionsinstanzen zustandsbehaftete Singletons sind, ist es nicht möglich, ein konkurrierendes Consumermodell zu verwenden, um die Last zwischen virtuellen Computern zu verteilen. Vielmehr wird bei Orchestratornachrichten die Last auf die Steuerelement-Warteschlangen verteilt. Weitere Informationen zu diesem Verhalten finden Sie in den folgenden Abschnitten.

Steuerelement-Warteschlangen enthalten eine Vielzahl von Orchestrierung-Lebenszyklus-Nachrichtentypen. Beispiele hierfür sind [orchestrator control messages](durable-functions-instance-management.md) (Orchestratorsteuerungsmeldungen), Aktivitätsfunktion-*Antwort*-Nachrichten und Timernachrichten. Bis zu 32 Nachrichten werden in einer einzelnen Abfrage aus einer Steuerelement-Warteschlange entfernt. Diese Nachrichten enthalten Nutzlastdaten sowie Metadaten, einschließlich der Orchestrierungsinstanz, für die eine Nachricht jeweils vorgesehen ist. Wenn mehrere aus der Warteschlange entfernte Nachrichten für dieselbe Orchestrierungsinstanz vorgesehen sind, werden sie als Batch verarbeitet.

### <a name="queue-polling"></a>Abrufen von Warteschlangen

Die Durable Task-Erweiterung implementiert einen zufälligen exponentiellen Backoffalgorithmus, um die Auswirkungen des Abrufs von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren. Wenn eine Nachricht gefunden wird, überprüft die Runtime sofort, ob eine andere Nachricht vorhanden ist. Wenn keine Nachricht gefunden wird, wird der Vorgang nach einem bestimmten Zeitraum wiederholt. Nach aufeinander folgenden fehlerhaften Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit (standardmäßig eine 30 Sekunden) erreicht ist.

Die maximale Abrufverzögerung kann über die Eigenschaft `maxQueuePollingInterval` in der Datei [host.json](../functions-host-json.md#durabletask) konfiguriert werden. Die Festlegung dieser Eigenschaft auf einen höheren Wert kann zu höherer Latenz bei der Nachrichtenverarbeitung führen. Eine höhere Latenz wird nur nach Zeiträumen ohne Aktivität erwartet. Die Festlegung der Eigenschaft auf einen niedrigeren Wert kann aufgrund verstärkter Speichertransaktionen zu höheren Speicherkosten führen.

> [!NOTE]
> Bei der Ausführung in Verbrauchs- und Premium-Tarifen von Azure Functions ruft der [Azure Functions-Skalierungscontroller](../functions-scale.md#how-the-consumption-and-premium-plans-work) alle Steuerelement- und Arbeitselement-Warteschlangen einmal alle 10 Sekunden ab. Dieser zusätzliche Abruf ist erforderlich, um zu ermitteln, wann die Instanzen der Funktions-Apps aktiviert und Skalierungsentscheidungen getroffen werden sollen. Zum Zeitpunkt der Erstellung dieses Artikels ist dieses Intervall von 10 Sekunden konstant und kann nicht konfiguriert werden.

## <a name="storage-account-selection"></a>Auswahl des Speicherkontos

Die in Durable Functions verwendeten Warteschlangen, Tabellen und Blobs werden in einem konfigurierten Azure Storage-Konto erstellt. Das zu verwendende Konto kann über die Einstellung `durableTask/azureStorageConnectionStringName` in der Datei **host.json** angegeben werden.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

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

Aktivitätsfunktionen sind zustandslos, und die horizontale Hochskalierung wird automatisch durch Hinzufügen von virtuellen Computern ausgeführt. Orchestratorfunktionen dagegen, sind über mindestens eine oder mehr Steuerelement-Warteschlangen *partitioniert*. Die Anzahl der Steuerelement-Warteschlangen wird in der Datei **host.json** definiert. Mit dem folgenden Beispielcodeausschnitt in „host.json“ wird die `durableTask/partitionCount`-Eigenschaft auf `3` festgelegt.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "partitionCount": 3
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

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

Wenn mehrere Funktionshostinstanzen (in der Regel auf verschiedenen virtuellen Computern) horizontal hochskaliert werden, erhält jede Instanz eine Sperre auf einer der Steuerelement-Warteschlangen. Diese Sperren werden intern als Blob Storage-Leases implementiert und stellen sicher, dass eine Orchestrierungsinstanz jeweils nur auf einer einzelnen Hostinstanz ausgeführt wird. Wenn ein Aufgabenhub mit drei Steuerelement-Warteschlangen konfiguriert ist, kann die Last bei Orchestrierungsinstanzen auf bis zu drei virtuelle Computer verteilt werden. Zusätzliche virtuelle Computer können zum Erhöhen der Kapazität für die Ausführung der Aktivitätsfunktion hinzugefügt werden.

Das folgende Diagramm veranschaulicht, wie der Azure Functions-Host mit den Speicherentitäten in einer horizontal skalierten Umgebung interagiert.

![Skalierungsdiagramm](./media/durable-functions-perf-and-scale/scale-diagram.png)

Wie im Diagramm oben dargestellt, konkurrieren alle virtuellen Computer um Nachrichten in der Warteschlange für Arbeitsaufgaben. Allerdings können nur drei virtuelle Computer Nachrichten aus Steuerelement-Warteschlangen abrufen, und jeder virtuelle Computer sperrt eine einzige Steuerelement-Warteschlange.

Die Orchestrierungsinstanzen sind auf alle Steuerelement-Warteschlangeninstanzen verteilt. Die Verteilung erfolgt durch Ausführen einer Hashfunktion mit der Instanz-ID der Orchestrierung. Instanz-IDs sind standardmäßig zufällige GUIDs, sodass sichergestellt ist, dass Instanzen gleichmäßig auf alle Steuerelement-Warteschlangen verteilt werden.

Im Allgemeinen sollten Orchestratorfunktionen einfach sein und keine große Rechenleistung in Anspruch nehmen. Daher ist es nicht erforderlich, eine große Anzahl von Steuerelement-Warteschlange-Partitionen zu erstellen, um einen erhöhten Durchsatz zu erzielen. Der größte Teil der intensiven Vorgänge sollte in zustandslosen Aktivitätsfunktionen erfolgen, die unbegrenzt horizontal hochskaliert werden können.

## <a name="auto-scale"></a>Automatische Skalierung

Wie bei allen Azure Functions-Vorgängen, die im Nutzungsplan ausgeführt werden, unterstützt Durable Functions die automatische Skalierung über den [Azure Functions-Skalierungscontroller](../functions-scale.md#runtime-scaling). Der Skalierungscontroller überwacht die Latenz aller Warteschlangen, indem regelmäßig _Peek_-Befehle ausgegeben werden. Basierend auf den Latenzen der eingesehenen Nachrichten legt der Skalierungscontroller fest, ob virtuelle Computer hinzugefügt oder entfernt werden.

Wenn der Skalierungscontroller bestimmt, dass die Latenzen der Nachrichten der Steuerelement-Warteschlangen zu hoch sind, werden VM-Instanzen hinzugefügt, bis die Nachrichtenlatenz auf ein annehmbares Maß verringert ist oder bis die Partitionsanzahl der Steuerelement-Warteschlangen erreicht ist. Auf ähnliche Weise fügt der Skalierungscontroller kontinuierlich VM-Instanzen hinzu, wenn Latenzen der Warteschlangen für Arbeitsaufgaben hoch sind, jedoch unabhängig von der Partitionsanzahl.

## <a name="thread-usage"></a>Verwendung von Threads

Orchestratorfunktionen werden in einem einzelnen Thread ausgeführt, um sicherzustellen, dass die Ausführung in vielen Wiedergaben deterministisch sein kann. Aufgrund dieser Singlethreadausführung ist es wichtig, dass Orchestratorfunktionsthreads auf keinen Fall CPU-intensive Aufgaben, E/A-Vorgänge oder Blockierungsvorgänge ausführen. Alle Vorgänge, die möglicherweise E/A, die Blockierung oder mehrere Threads erfordern, sollten in die Aktivitätsfunktion verschoben werden.

Aktivitätsfunktionen haben alle dasselbe Verhalten, als reguläre, durch Warteschlangen ausgelöste Funktionen. Sie können problemlos E/A-Vorgänge und CPU-intensive Vorgänge ausführen sowie mehrere Threads verwenden. Da Aktivitätsauslöser zustandslos sind, können sie jederzeit auf eine unbegrenzte Anzahl von virtuellen Computern horizontal hochskaliert werden.

## <a name="concurrency-throttles"></a>Drosselungen der Parallelität

In Azure Functions können mehrere Funktionen gleichzeitig in einer einzelnen App-Instanz ausgeführt werden. Diese gleichzeitige Ausführung trägt zur Erhöhung der Parallelität und Verringerung der Anzahl von „Kaltstarts“ bei, die bei einer typischen App mit der Zeit auftreten. Hohe Parallelität kann jedoch zu einer hohen Speicherauslastung pro virtuellem Computer führen. Je nach den Anforderungen der Funktions-App kann es erforderlich sein, die Parallelität pro Instanz zu drosseln, um zu verhindern, dass in Situationen mit hohen Auslastungen nicht genügend Speicher vorhanden ist.

Die Parallelitätslimits für Aktivitätsfunktionen und Orchestratorfunktionen können jeweils in der Datei **host.json** konfiguriert werden. Die relevanten Einstellungen sind `durableTask/maxConcurrentActivityFunctions` bzw. `durableTask/maxConcurrentOrchestratorFunctions`.

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

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

Im vorherigen Beispiel können maximal 10 Orchestratorfunktionen und 10 Aktivitätsfunktionen gleichzeitig auf einem einzelnen virtuellen Computer ausgeführt werden. Wenn kein Wert angegeben ist, ist die Anzahl der gleichzeitigen Ausführungen von Aktivitäts- und Orchestratorfunktionen auf das Zehnfache der Anzahl von Kernen auf dem virtuellen Computer begrenzt.

> [!NOTE]
> Diese Einstellungen sind nützlich bei der Verwaltung der Speicher- und CPU-Auslastung auf einem einzelnen virtuellen Computer. Bei der horizontalen Hochskalierung auf mehrere VMs sind jedoch für jede VM jeweils eigene Limits festgelegt. Mit diesen Einstellungen kann die Parallelität nicht auf globaler Ebene gesteuert werden.

## <a name="orchestrator-function-replay"></a>Wiedergabe von Orchestratorfunktionen

Wie zuvor erwähnt, werden Orchestratorfunktionen unter Verwendung der Inhalte der **Verlaufstabelle** wiedergegeben. Standardmäßig wird der Orchestratorfunktionscode immer dann wiedergegeben, wenn ein Batch von Nachrichten aus einer Steuerelement-Warteschlange entfernt wird.

Dieses aggressive Wiedergabeverhalten kann durch Aktivieren von **erweiterten Sitzungen** deaktiviert werden. Wenn erweiterte Sitzungen aktiviert sind, verbleiben Orchestratorfunktionsinstanzen länger im Arbeitsspeicher, und neue Nachrichten können ohne eine vollständige Wiedergabe verarbeitet werden. Erweiterte Sitzungen werden aktiviert, indem `durableTask/extendedSessionsEnabled` in der Datei **host.json** auf `true` festgelegt wird. Mit der Einstellung `durableTask/extendedSessionIdleTimeoutInSeconds` wird gesteuert, wie lange eine Leerlaufsitzung im Arbeitsspeicher verbleibt:

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

### <a name="functions-2x"></a>Functions 2.x

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

Die typischen Auswirkungen der Aktivierung von erweiterten Sitzungen sind reduzierte E/A-Vorgänge für das Azure Storage-Konto und ein allgemeiner verbesserter Durchsatz.

Ein möglicher Nachteil dieses Features besteht jedoch darin, dass Orchestratorfunktionsinstanzen im Leerlauf länger im Speicher verbleiben. Zwei Auswirkungen sind zu beachten:

1. Allgemeine Zunahme der Speicherauslastung von Funktions-Apps
2. Allgemeine Verringerung des Durchsatzes bei der Ausführung von vielen gleichzeitigen, kurz dauernden Orchestratorfunktionen

Wenn `durableTask/extendedSessionIdleTimeoutInSeconds` beispielsweise auf 30 Sekunden festgelegt ist, belegt eine kurz dauernde Orchestratorfunktionsfolge, die in weniger als 1 Sekunde ausgeführt wird, dennoch 30 Sekunden lang den Arbeitsspeicher. Zudem wird sie auf das zuvor erwähnte Kontingent `durableTask/maxConcurrentOrchestratorFunctions` angerechnet, sodass möglicherweise verhindert wird, dass andere Orchestratorfunktionen ausgeführt werden.

> [!NOTE]
> Diese Einstellungen sollten nur verwendet werden, nachdem eine Orchestratorfunktion vollständig entwickelt und getestet wurde. Das standardmäßige aggressive Wiedergabeverhalten ist nützlich bei der Erkennung von Idempotenzfehlern in Orchestratorfunktionen zur Entwicklungszeit.

## <a name="performance-targets"></a>Leistungsziele

Bei der Planung der Verwendung von Durable Functions für eine Produktionsanwendung müssen die Leistungsanforderungen frühzeitig im Planungsprozess berücksichtigt werden. In diesem Abschnitt werden einige allgemeine Verwendungsszenarien und die erwarteten maximalen Durchsatzzahlen behandelt.

* **Sequenzielle Aktivitätsausführung:** Dieses Szenario beschreibt eine Orchestratorfunktion, mit der nacheinander eine Reihe von Aktivitätsfunktionen ausgeführt wird. Am ehesten ähnelt es dem Beispiel zur [Funktionsverkettung](durable-functions-sequence.md).
* **Parallele Aktivitätsausführung:** Dieses Szenario beschreibt eine Orchestratorfunktion, mit der mithilfe des Musters [Auffächern nach außen/innen](durable-functions-cloud-backup.md) viele Aktivitätsfunktionen parallel ausgeführt werden.
* **Parallele Antwortverarbeitung:** Dieses Szenario stellt die zweite Hälfte des Musters [Auffächern nach außen/innen](durable-functions-cloud-backup.md) dar. Es konzentriert sich auf die Leistung der Auffächerung nach innen. Es muss beachtet werden, dass im Unterschied zur Auffächerung nach außen die Auffächerung nach innen durch eine einzelne Orchestratorfunktionsinstanz erfolgt und daher nur auf einem einzelnen virtuellen Computer ausgeführt werden kann.
* **Externe Ereignisverarbeitung:** Dieses Szenario stellt eine einzelne Orchestratorfunktionsinstanz dar, die nacheinander auf [externe Ereignisse](durable-functions-external-events.md) wartet.

> [!TIP]
> Im Gegensatz zur Auffächerung nach außen sind Vorgänge zur Auffächerung nach innen auf einen einzelnen virtuellen Computer beschränkt. Wenn in Ihrer Anwendung das Muster „Auffächern nach außen/innen“ verwendet wird und Sie die Leistung im Hinblick auf die Auffächerung nach innen optimieren möchten, können Sie die Auffächerung der Aktivitätsfunktionen nach außen in mehrere [untergeordnete Orchestrierungen](durable-functions-sub-orchestrations.md) unterteilen.

In der folgenden Tabelle sind die erwarteten *maximalen* Durchsatzzahlen für die zuvor beschriebenen Szenarien aufgeführt. „Instanz“ bezieht sich auf eine einzelne Instanz einer Orchestratorfunktion, die auf einem einzelnen kleinen ([A1](../../virtual-machines/windows/sizes-previous-gen.md#a-series)) virtuellen Computer in Azure App Service ausgeführt wird. In allen Fällen wird angenommen, dass [erweiterte Sitzungen](#orchestrator-function-replay) aktiviert sind. Die tatsächlichen Ergebnisse können je nach den durch den Funktionscode ausgeführten CPU- oder E/A-Vorgängen variieren.

| Szenario | Maximaler Durchsatz |
|-|-|
| Sequenzielle Aktivitätsausführung | 5 Aktivitäten pro Sekunde pro Instanz |
| Parallele Aktivitätsausführung (Auffächern nach außen) | 100 Aktivitäten pro Sekunde pro Instanz |
| Parallele Antwortverarbeitung (Auffächern nach innen) | 150 Antworten pro Sekunde pro Instanz |
| Externe Ereignisverarbeitung | 50 Ereignisse pro Sekunde pro Instanz |

> [!NOTE]
> Diese Zahlen gelten ab der Durable Functions-Erweiterung v1.4.0 (allgemeine Verfügbarkeit). Diese Zahlen können sich im Verlauf der Entwicklung und Optimierung des Features ändern.

Wenn Sie nicht die erwarteten Durchsatzzahlen erreichen und die CPU- und die Speicherauslastung fehlerfrei zu sein scheinen, überprüfen Sie, ob die Ursache auf die [Integrität Ihres Speicherkontos](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance) zurückzuführen ist. Die Durable Functions-Erweiterung kann eine beträchtliche Auslastung eines Azure Storage-Kontos verursachen, und entsprechend hohe Auslastungen können zu einer Drosselung des Speicherkontos führen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten dauerhafte Funktion in C#](durable-functions-create-first-csharp.md)
