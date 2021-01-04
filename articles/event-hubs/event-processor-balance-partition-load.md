---
title: 'Ausgleichen der Partitionsauslastung über mehrere Instanzen hinweg: Azure Event Hubs | Microsoft-Dokumentation'
description: Beschreibt, wie Sie die Partitionsauslastung über mehrere Instanzen Ihrer Anwendung hinweg mithilfe eines Ereignisprozessors und des Azure Event Hubs SDK ausgleichen können.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025043"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Ausgleichen der Partitionsauslastung über mehrere Instanzen der Anwendung hinweg
Um die Ereignisverarbeitungsanwendung zu skalieren, können Sie mehrere Instanzen der Anwendung ausführen und die Auslastung zwischen diesen untereinander ausgleichen. In älteren Versionen konnte [EventProcessorHost](event-hubs-event-processor-host.md) die Last zwischen mehreren Instanzen Ihres Programms und Prüfpunktereignissen bei deren Empfang ausgleichen. In den neueren Versionen (5.0 oder höher) ermöglichen **EventProcessorClient** (.NET und Java) oder **EventHubConsumerClient** (Python und JavaScript) die gleiche Funktionalität. Das Entwicklungsmodell wird durch die Verwendung von-Ereignissen vereinfacht. Sie abonnieren die Ereignisse, an denen Sie interessiert sind, indem Sie einen Ereignishandler registrieren. Wenn Sie die alte Version der Clientbibliothek verwenden, finden Sie weitere Informationen in den folgenden Migrationsleitfäden: [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md) und [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

In diesem Artikel wird ein Beispielszenario für die Verwendung mehrerer Instanzen zum Lesen von Ereignissen aus einem Event Hub beschrieben. Anschließend werden Details zu den Funktionen des Ereignisprozessorclients vorgestellt, mit denen Sie Ereignisse von mehreren Partitionen gleichzeitig empfangen und einen Lastenausgleich mit anderen Consumern ausführen können, die denselben Event Hub und dieselbe Consumergruppe verwenden.

> [!NOTE]
> Der Schlüssel zur Skalierung in Event Hubs ist das Konzept der partitionierten Consumer. Im Gegensatz zum Muster der [konkurrierenden Consumer](/previous-versions/msp-n-p/dn568101(v=pandp.10)) ermöglicht das Muster der partitionierten Consumer hohe Skalierbarkeit durch Beseitigen des Konfliktengpasses und Vereinfachen der End-to-End-Parallelität.

## <a name="example-scenario"></a>Beispielszenario

Als Beispielszenario soll ein Unternehmen für Alarmsysteme dienen, das 100.000 Häuser überwacht. Jede Minute gehen Daten von verschiedenen Sensoren im Unternehmen ein, z. B. von Bewegungsmeldern, Tür- und Fensteröffnungsmeldern oder Glasbruchsensoren, die in jedem Haus montiert sind. Das Unternehmen stellt für Kunden eine Website zum Überwachen der Aktivitäten im Haus nahezu in Echtzeit bereit.

Jeder Melder überträgt Daten an einen Event Hub. Der Event Hub ist mit 16 Partitionen konfiguriert. Auf der Consumerseite benötigen Sie einen Mechanismus, der diese Ereignisse lesen und konsolidieren (filtern, aggregieren usw.) und das Aggregat in einem Speicherblob sichern kann, das dann an eine benutzerfreundliche Webseite übertragen wird.

## <a name="write-the-consumer-application"></a>Schreiben der Consumeranwendung

Beim Entwerfen des Consumers in einer verteilten Umgebung muss das Szenario die folgenden Anforderungen erfüllen:

1. **Skalierung**: Erstellen mehrerer Consumer, wobei jeder Consumer die Besitzrechte für das Lesen einiger Event Hubs-Partitionen übernimmt.
2. **Lastenausgleich**: Dynamisches Erhöhen oder Verringern der Consumer. Wenn beispielsweise in jedem Haus ein neuer Melder (z.B. ein Kohlenmonoxidmelder) montiert wird, erhöht sich die Anzahl der Ereignisse. In diesem Fall erhöht der (menschliche) Bediener die Anzahl der Consumerinstanzen. Anschließend kann der Pool von Consumern die Anzahl der Partitionen in ihrem Besitz neu ausgleichen, um die Last auch auf die neu hinzugefügten Consumer zu verteilen.
3. **Nahtloses Fortsetzen nach Fehlern**: Wenn bei einem Consumer (**Consumer A**) ein Fehler auftritt (der virtuelle Computer, der den Consumer hostet, stürzt z.B. plötzlich ab), müssen andere Consumer die Partitionen im Besitz von **Consumer A** aufnehmen und fortfahren können. Außerdem muss der Fortsetzungspunkt, der als *Prüfpunkt* oder *Offset* bezeichnet wird, genau an dem Punkt oder etwas früher liegen, an dem der Fehler bei **Consumer A** aufgetreten ist.
4. **Nutzen von Ereignissen**: Während die drei vorherigen Punkte die Verwaltung des Consumers betreffen, ist außerdem Code zum Nutzen und sinnvollen Verarbeiten der Ereignisse erforderlich. Beispielsweise zum Aggregieren und Hochladen in Blobspeicher.

## <a name="event-processor-or-consumer-client"></a>Ereignisprozessor oder Consumerclient

Sie müssen keine eigene Lösung erstellen, um diese Anforderungen zu erfüllen. Diese Funktionalität wird von den Azure-Event Hubs SDKs bereitgestellt. In .NET SDKs oder Java SDKs verwenden Sie einen Ereignisprozessorclient (EventProcessorClient) und in Python- und JavaScript-SDKs EventHubConsumerClient. In der alten Version des SDK war es der Ereignisprozessorhost (EventProcessorHost), der diese Funktionen unterstützt hat.

In den meisten Produktionsszenarien wird empfohlen, den Ereignisprozessorclient zum Lesen und Verarbeiten von Ereignissen zu verwenden. Der Prozessorclient soll eine robuste Umgebung für die Verarbeitung von Ereignissen über alle Partitionen eines Event Hub hinweg in einer leistungsstarken und fehlertoleranten Weise bereitstellen und gleichzeitig die Möglichkeit bieten, den Fortschritt anhand von Prüfpunkten festzustellen. Ereignisprozessorclients können auch im Kontext einer Consumergruppe für einen bestimmten Event Hub zusammenarbeiten. Verteilung und Lastenausgleich werden von Clients automatisch verwaltet, wenn Instanzen für die Gruppe verfügbar werden oder nicht verfügbar sind.

## <a name="partition-ownership-tracking"></a>Nachverfolgen des Partitionsbesitzes

Eine Ereignisprozessorinstanz ist in der Regel Besitzer von Ereignissen und verarbeitet Ereignisse aus mindestens einer Partition. Der Besitz von Partitionen wird gleichmäßig auf alle aktiven Ereignisprozessorinstanzen verteilt, die mit einer Kombination aus Event Hub und Consumergruppe verknüpft sind. 

Jeder Ereignisprozessor erhält einen eindeutigen Bezeichner und beansprucht den Besitz von Partitionen, indem ein Eintrag in einem Prüfpunktspeicher hinzugefügt oder aktualisiert wird. Alle Ereignisprozessorinstanzen kommunizieren in regelmäßigen Abständen mit diesem Speicher, um den eigenen Verarbeitungsstatus zu aktualisieren und Informationen zu anderen aktiven Instanzen zu erhalten. Diese Daten werden dann verwendet, um die Last zwischen den aktiven Prozessoren auszugleichen. Neue Instanzen können dem Verarbeitungspool zum Aufskalieren beitreten. Wenn Instanzen aufgrund von Fehlern oder durch Herunterskalieren ausfallen, wird der Partitionsbesitz ordnungsgemäß auf andere aktive Prozessoren übertragen.

Die Partitionsbesitz-Datensätze im Prüfpunktspeicher verfolgen den Event Hubs-Namespace, den Event Hub-Namen, die Consumergruppe, den Bezeichner des Ereignisprozessors (auch als Besitzer bezeichnet), die Partitions-ID und den Zeitpunkt der letzten Änderung nach.



| Event Hubs-Namespace               | Event Hub-Name | **Consumergruppe** | Besitzer                                | Partitions-ID | Zeitpunkt der letzten Änderung  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | decodiert werden:                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Jede Ereignisprozessorinstanz übernimmt den Besitz einer Partition und beginnt mit der Verarbeitung der Partition ab dem letzten bekannten [Prüfpunkt](# Checkpointing). Wenn ein Prozessor ausfällt (VM wird heruntergefahren), erkennen andere Instanzen dies, indem sie den Zeitpunkt der letzten Änderung untersuchen. Andere Instanzen versuchen, den Besitz der Partitionen zu erhalten, die sich zuvor im Besitz der inaktiven Instanz befanden, und der Prüfpunktspeicher garantiert, dass nur eine der Instanzen erfolgreich den Besitz einer Partition beanspruchen kann. Daher gibt es zu einem beliebigen Zeitpunkt höchstens einen Prozessor, der Ereignisse von einer Partition empfängt.

## <a name="receive-messages"></a>Empfangen von Nachrichten

Wenn Sie einen Ereignisprozessor erstellen, geben Sie die Funktionen an, mit denen Ereignisse und Fehler verarbeitet werden. Jeder Aufruf der Funktion, die Ereignisse verarbeitet, liefert ein einzelnes Ereignis aus einer bestimmten Partition. Die Verarbeitung dieses Ereignisses liegt in Ihrer Verantwortung. Wenn Sie sicherstellen möchten, dass der Consumer jede Nachricht mindestens ein Mal verarbeitet, müssen Sie Ihren eigenen Code mit Wiederholungslogik schreiben. Lassen Sie bei nicht verarbeitbaren Nachrichten jedoch Vorsicht walten.

Es wird empfohlen, relativ schnell vorzugehen. Das heißt: Führen Sie so wenig Verarbeitung wie möglich durch. Wenn Ereignisse in den Speicher geschrieben und Routingvorgänge durchgeführt werden müssen, empfiehlt es sich, zwei Consumergruppen zu verwenden und zwei Ereignisprozessoren einzusetzen.

## <a name="checkpointing"></a>Setzen von Prüfpunkten

Das *Festlegen von Prüfpunkten* ist ein Vorgang, durch den ein Ereignisprozessor die Position des letzten erfolgreich verarbeiteten Ereignisses innerhalb einer Partition markiert oder committet. Das Markieren eines Prüfpunkts erfolgt in der Regel innerhalb der Funktion, die die Ereignisse verarbeitet, und wird pro Partition innerhalb einer Consumergruppe ausgeführt. 

Wenn ein Ereignisprozessor die Verbindung mit einer Partition trennt, kann eine andere Instanz die Verarbeitung der Partition am Prüfpunkt fortsetzen, für den zuvor vom letzten Prozessor dieser Partition in dieser Consumergruppe ein Commit ausgeführt wurde. Wenn der Prozessor eine Verbindung herstellt, übergibt er den Offset an den Event Hub, um die Position für den Beginn des nächsten Lesevorgangs anzugeben. Auf diese Weise können mithilfe von Prüfpunkten Ereignisse von Downstreamanwendungen als abgeschlossen markiert werden. Darüber hinaus sorgen Prüfpunkte für Resilienz, wenn ein Ereignisprozessor ausfällt. Sie können ältere Daten zurückgeben, indem Sie einen niedrigeren Offset aus diesem Prüfpunktprozess angeben. 

Wenn der Prüfpunkt ausgeführt wird, um ein Ereignis als verarbeitet zu markieren, wird ein Eintrag im Prüfpunktspeicher mit dem Offset und der Sequenznummer des Ereignisses hinzugefügt oder aktualisiert. Benutzer sollten die Häufigkeit festlegen, mit der der Prüfpunkt aktualisiert wird. Das Aktualisieren nach jedem erfolgreich verarbeiteten Ereignis kann Auswirkungen auf die Leistung und die Kosten haben, da es einen Schreibvorgang in den zugrunde liegenden Prüfpunktspeicher auslöst. Außerdem ist das Versehen mit Prüfpunkten aller Ereignisse ein Hinweis auf ein Messagingmuster mit Warteschlangen, für das eine Service Bus-Warteschlange möglicherweise besser als ein Event Hub geeignet ist. Das Konzept hinter Event Hubs ist, dass mindestens eine Übermittlung in großem Umfang erfolgt. Indem Sie Ihre Downstreamsysteme idempotent machen, ist es einfach, nach Fehlern oder Neustarts, die dazu führen, dass dieselben Ereignisse mehrmals empfangen werden, eine Wiederherstellung durchzuführen.

> [!NOTE]
> Wenn Sie Azure Blob Storage als Prüfpunktspeicher in einer Umgebung verwenden, die eine andere Version des Storage Blob SDK unterstützt als diejenigen, die in der Regel in Azure verfügbar sind, müssen Sie Code verwenden, um die Version der Speicherdienst-API in die von dieser Umgebung unterstützte Version zu ändern. Wenn Sie z. B. [Event Hubs mit einer Azure Stack Hub-Version 2002](/azure-stack/user/event-hubs-overview) ausführen, ist die höchste verfügbare Version für den Speicherdienst Version 2017-11-09. In diesem Fall müssen Sie Code verwenden, um Version 2017-11-09 der Storage Service-API als Ziel zu nutzen. Ein Beispiel für die Verwendung einer bestimmten Storage-API-Version als Ziel finden Sie in den folgenden Beispielen auf GitHub: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) oder [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Threadsicherheit und Prozessorinstanzen

Standardmäßig wird die Funktion zur Verarbeitung der Ereignisse für eine bestimmte Partition sequenziell aufgerufen. Nachfolgende Ereignisse und Aufrufe dieser Funktion über eine Partition sammeln sich im Hintergrund in der Warteschlange an, da das Ereignissystem weiterhin im Hintergrund in anderen Threads ausgeführt wird. Ereignisse unterschiedlicher Partitionen können gleichzeitig verarbeitet werden. Freigegebene Status, auf die über Partitionen hinweg zugegriffen wird, müssen synchronisiert werden.

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die folgenden Schnellstarts an:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
