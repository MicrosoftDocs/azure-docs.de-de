---
title: Zuverlässige Ereignisverarbeitung in Azure Functions
description: Vermeiden, Event Hub-Nachrichten in Azure Functions zu verpassen
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379586"
---
# <a name="azure-functions-reliable-event-processing"></a>Zuverlässige Ereignisverarbeitung in Azure Functions

Bei der Ereignisverarbeitung handelt es sich um eines der häufigsten Szenarien im Zusammenhang mit serverloser Architektur. In diesem Artikel wird beschrieben, wie ein zuverlässiger Nachrichtenprozessor mit Azure Functions erstellt wird, um den Verlust von Nachrichten zu vermeiden.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Herausforderungen bei Ereignisdatenströmen in verteilten Systemen

Betrachten Sie ein System, das Ereignisse mit konstanter Rate von 100 Ereignissen pro Sekunde sendet. Bei dieser Geschwindigkeit können mehrere parallele Functions-Instanzen innerhalb weniger Minuten die eingehenden 100 Ereignisse pro Sekunde verbrauchen.

Allerdings sind die folgenden weniger optimalen Bedingungen ebenfalls möglich:

- Was geschieht, wenn der Ereignisherausgeber ein beschädigtes Ereignis sendet?
- Was geschieht, wenn in Ihrer Functions-Instanz nicht behandelte Ausnahmen auftreten?
- Was geschieht, wenn ein Downstreamsystem offline geschaltet wird?

Wie verarbeiten Sie diese Situationen und erhalten gleichzeitig den Durchsatz Ihrer Anwendung?

Mit Warteschlangen erzielen Sie zuverlässiges Messaging auf natürliche Weise. Wenn dies mit einem Functions-Triggern kombiniert wird, erstellt die Funktion eine Sperre für die Warteschlangennachricht. Wenn die Verarbeitung fehlschlägt, wird die Sperre freigegeben, damit eine andere Instanz die Verarbeitung erneut versuchen kann. Die Verarbeitung wird dann fortgesetzt, bis die Nachricht entweder erfolgreich ausgewertet oder einer Warteschlange für nicht verarbeitete Nachrichten hinzugefügt wurde.

Auch wenn eine einzelne Warteschlangennachricht in einem Wiederholungszyklus verbleiben kann, werden andere parallele Ausführungen weiterhin ausgeführt, um verbleibende Nachrichten aus der Warteschlange zu entfernen. Das Ergebnis ist, dass der Gesamtdurchsatz durch eine einzelne ungültige Nachricht weitestgehend unbeeinträchtigt bleibt. Speicherwarteschlangen garantieren jedoch keine Reihenfolge und sind nicht für die hohen Durchsatzanforderungen optimiert, die für Event Hubs gelten.

Im Gegensatz dazu beinhaltet Azure Event Hubs kein Sperrkonzept. Um Funktionen wie hohen Durchsatz, mehrere Consumergruppen und Wiedergabefähigkeit zu ermöglichen, verhalten sich Event Hubs-Ereignisse eher wie ein Videoplayer. Ereignisse werden an einem einzelnen Punkt pro Partition im Datenstrom gelesen. Von dem Zeiger an diesem Ort aus können Sie vorwärts oder rückwärts lesen, aber Sie müssen den Zeiger verschieben, damit Ereignisse verarbeitet werden.

Wenn Fehler in einem Datenstrom auftreten, und Sie beschlossen haben, den Zeiger an derselben Stelle zu belassen, wird die Ereignisverarbeitung blockiert, bis der Zeiger vorwärtsbewegt wird. Anders ausgedrückt: Wenn der Zeiger angehalten wird, um Probleme bei der Verarbeitung eines einzelnen Ereignisses zu behandeln, beginnen die nicht verarbeiteten Ereignisse sich zu stauen.

Azure Functions vermeidet Deadlocks, indem der Zeiger des Datenstroms unabhängig von Erfolg oder Fehlschlag vorwärtsbewegt wird. Da der Zeiger immer weiter voranschreitet, müssen Ihre Funktionen mit Fehlern ordnungsgemäß umgehen.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Nutzung von Event Hubs-Ereignissen durch Azure Functions

Azure Functions nutzt Event Hub-Ereignisse, während die folgenden Schritte durchlaufen werden:

1. Ein Zeiger wird in Azure Storage für jede Partition des Event Hubs erstellt und persistent gespeichert.
2. Wenn neue Nachrichten empfangen werden (standardmäßig in einem Batch), versucht der Host, die Funktion mit dem Batch von Nachrichten auszulösen.
3. Wenn die Funktion die Ausführung abschließt (mit oder ohne Ausnahme), wird der Zeiger vorwärtsbewegt, und ein Prüfpunkt wird im Speicherkonto gespeichert.
4. Wenn Umstände den Abschluss der Ausführung der Funktion verhindern, kann der Host den Zeiger nicht vorwärtsbewegen. Wenn der Zeiger nicht vorwärtsbewegt wird, beginnen spätere Überprüfungen dieselben Nachrichten zu verarbeiten.
5. Wiederholen der Schritte 2 bis 4

Dieses Verhalten offenbart einige wichtige Aspekte:

- *Unbehandelte Ausnahmen können zum Verlust von Nachrichten führen.* Bei Ausführungen, die zu einer Ausnahme führen, wird der Zeiger weiter vorwärtsbewegt.  Das Festlegen einer [Wiederholungsrichtlinie](./functions-bindings-error-pages.md#retry-policies-preview) verzögert die Verarbeitung des Zeigers so lange, bis die gesamte Wiederholungsrichtlinie ausgewertet wurde.
- *Funktionen garantieren eine mindestens einmalige Zustellung.* Ihr Code sowie Ihre abhängigen Systeme müssen möglicherweise [die Tatsache berücksichtigen, dass dieselbe Nachricht zweimal empfangen werden könnte](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Behandeln von Ausnahmen

Als allgemeine Regel gilt, dass jede Funktion einen [try/catch-Block](./functions-bindings-error-pages.md) auf der höchsten Codeebene enthalten sollte. Insbesondere sollten alle Funktionen, die Event Hubs-Ereignisse nutzen, über einen `catch`-Block verfügen. Auf diese Weise verarbeitet der catch-Block bei Auftreten einer Ausnahme den Fehler, bevor der Zeiger vorwärtsbewegt wird.

### <a name="retry-mechanisms-and-policies"></a>Wiederholungsmechanismen und -richtlinien

Einige Ausnahmen sind vorübergehender Natur und treten nicht erneut auf, wenn ein Vorgang ein wenig später noch mal versucht wird. Aus diesem Grund besteht der erste Schritt immer darin, den Vorgang zu wiederholen.  Sie können die [Wiederholungsrichtlinien](./functions-bindings-error-pages.md#retry-policies-preview) der Funktions-App nutzen oder Wiederholungslogik innerhalb der Funktionsausführung schreiben.

Durch die Einführung von Verhaltensweisen zur Fehlerbehandlung in Ihre Funktionen können Sie sowohl einfache als auch komplexe Wiederholungsrichtlinien definieren. Beispielsweise könnten Sie eine Richtlinie implementieren, die einem Workflow folgt, der durch die folgenden Regeln veranschaulicht wird:

- Versuche, eine Nachricht dreimal einzufügen (möglicherweise mit einer Verzögerung zwischen den Wiederholungen).
- Wenn das Endergebnis aller Wiederholungen ein Fehler ist, füge eine Nachricht zu einer Warteschlange hinzu, damit die Verarbeitung im Datenstrom fortgesetzt werden kann.
- Beschädigte oder nicht verarbeitete Nachrichten werden dann zu einem späteren Zeitpunkt behandelt.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) ist ein Beispiel für eine Bibliothek für Resilienz und die Behandlung vorübergehender Fehler für C#-Anwendungen.

## <a name="non-exception-errors"></a>Fehler ohne Ausnahmen

Manche Probleme treten auch dann auf, wenn kein Fehler vorhanden ist. Stellen Sie sich z. B. einen Fehler vor, der in der Mitte einer Ausführung auftritt. In diesem Fall wird der Offsetzeiger niemals vorwärtsbewegt, wenn die Ausführung einer Funktion nicht abgeschlossen wird. Wenn der Zeiger nicht vorwärtsbewegt wird, liest jede Instanz, die nach einer fehlgeschlagenen Ausführung ausgeführt wird, weiterhin dieselben Nachrichten. Diese Situation bietet eine „At-Least-Once“-Garantie (mindestens einmalig).

Die Zusicherung, dass jede Nachricht mindestens einmal verarbeitet wird, impliziert, dass einige Nachrichten mehrmals verarbeitet werden können. Ihre Funktions-Apps müssen diese Möglichkeit kennen und gemäß den [Prinzipien der Idempotenz](./functions-idempotent.md) konstruiert werden.

## <a name="stop-and-restart-execution"></a>Beenden und Neustarten der Ausführung

Zwar können einige Fehler akzeptabel sein, doch was passiert, wenn es in Ihrer App zu signifikanten Fehlern kommt? Möglicherweise sollten Sie das Auslösen von Ereignissen beenden, bis das System einen fehlerfreien Zustand erreicht hat. Die Möglichkeit zum Anhalten der Verarbeitung wird häufig durch ein „Trennschalter“-Muster erreicht. Das Trennschalter-Muster ermöglicht Ihrer App, aus dem Zyklus der Ereignisverarbeitung auszubrechen und diesen zu einem späteren Zeitpunkt fortzusetzen.

Zum Implementieren eines Trennschalters in einem Ereignisprozess sind zwei Komponenten erforderlich:

- Ein über alle Instanzen geteilter Zustand, um die Integrität des Zyklus nachzuverfolgen und zu überwachen.
- Ein Masterprozess, der den Zustand des Zyklus (offen oder geschlossen) verwalten kann.

Die Implementierungsdetails können variieren, aber um den Status zwischen Instanzen zu teilen, benötigen Sie einen Speichermechanismus. Sie können sich entschließen, den Zustand in Azure Storage, einem Redis Cache oder einem beliebigen anderen Konto zu speichern, auf das eine Reihe von Funktionen zugreifen kann.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) oder [dauerhafte Funktionen](./durable/durable-functions-overview.md) sind für die Verwaltung des Workflow- und Verbindungsstatus natürliche Methoden. Andere Dienste funktionieren möglicherweise auch, aber für dieses Beispiel werden Logik-Apps verwendet. Mithilfe von Logik-Apps können Sie die Ausführung einer Funktion anhalten und neu starten, sodass Sie die Kontrolle erhalten, die erforderlich ist, um das Trennschalter-Muster zu implementieren.

### <a name="define-a-failure-threshold-across-instances"></a>Definieren eines Fehlerschwellenwerts über Instanzen hinweg

Um mehrere Instanzen, die gleichzeitig Ereignisse verarbeiten, berücksichtigen zu können, ist die persistente Speicherung des geteilten externen Zustands erforderlich, um die Integrität des Zyklus zu überwachen.

Eine Regel, die Sie implementieren können, könnte Folgendes erzwingen:

- Wenn mehr als 100 endgültige Fehler innerhalb von 30 Sekunden über alle Instanzen hinweg auftreten, verlasse den Zyklus und beende die Auslösung neuer Nachrichten.

Die Implementierungsdetails unterscheiden sich je nach Ihren Anforderungen, aber im Allgemeinen können Sie ein System erstellen, das Folgendes bietet:

1. Protokollieren von Fehlern in einem Speicherkonto (Azure Storage, Redis usw.)
1. Wenn ein neuer Fehler protokolliert wird, Untersuchen des akkumulierenden Zählers, um festzustellen, ob der Schwellenwert erreicht wird (z. B. mehr als 100 in den letzten 30 Sekunden).
1. Wird der Schwellenwert erreicht, Ausgeben eines Ereignisses an Azure Event Grid, das das System anweist, den Zyklus zu verlassen.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Verwalten des Zykluszustands mit Azure Logic Apps

In der folgenden Beschreibung wird eine Möglichkeit hervorgehoben, wie Sie eine Azure Logic App erstellen können, um die Verarbeitung einer Functions-App anzuhalten.

Azure Logic Apps verfügt über integrierte Connectors zu verschiedenen Dienste, besitzt zustandsbehaftete Orchestrierungen und ist die natürliche Wahl für die Verwaltung des Zykluszustands. Nachdem Sie erkannt haben, dass der Zyklus unterbrochen werden muss, können Sie eine Logik-App erstellen, um den folgenden Workflow zu implementieren:

1. Auslösen eines Event Grid-Workflows und Beenden der Azure-Funktion (mit dem Azure Resource-Connector).
1. Senden einer Benachrichtigungs-E-Mail, die eine Option zum Neustarten des Workflows enthält

Der E-Mail-Empfänger kann die Integrität des Zyklus untersuchen und ggf. den Zyklus über einen Link in der Benachrichtigungs-E-Mail neu starten. Während der Workflow die Funktion neu startet, werden die Nachrichten ab dem letzten Event Hub-Prüfpunkt verarbeitet.

Bei dieser Vorgehensweise gehen keine Nachrichten verloren, alle Nachrichten werden in der richtigen Reihenfolge verarbeitet, und Sie können den Zyklus so lange wie nötig unterbrechen.

## <a name="resources"></a>Ressourcen

- [Beispiele für zuverlässige Ereignisverarbeitung](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Durable Entity-Trennschalter](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

- [Azure Functions – Fehlerbehandlung](./functions-bindings-error-pages.md)
- [Automatisieren der Größenänderung von hochgeladenen Images per Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Erstellen einer Funktion, die in Azure Logic Apps integriert ist](./functions-twitter-email.md)
