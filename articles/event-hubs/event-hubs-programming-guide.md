---
title: Programmierhandbuch – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zum Schreiben von Code für Azure Event Hubs mit dem Azure .NET SDK.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.custom: seodec18
ms.topic: article
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 29814cb8aef09a8ead30d6daa615554dd55135dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60764363"
---
# <a name="programming-guide-for-azure-event-hubs"></a>Programmierhandbuch für Azure Event Hubs
Dieser Artikel erörtert einige gängige Szenarien zum Schreiben von Code mit Azure Event Hubs. Hierbei wird ein grundlegendes Verständnis von Event Hubs vorausgesetzt. Eine konzeptuelle Übersicht über Event Hubs finden Sie unter [Übersicht über Event Hubs](event-hubs-what-is-event-hubs.md).

## <a name="event-publishers"></a>Ereignisherausgeber

Sie senden Ereignisse entweder mit HTTP POST oder über eine AMQP 1.0-Verbindung an einen Event Hub. Welches Verfahren gewählt wird, hängt vom jeweils vorliegenden Szenario ab. AMQP 1.0-Verbindungen werden als vermittelte Verbindungen in Service Bus gemessen und sind besser für Fälle mit häufigeren höheren Nachrichtenvolumen und geringeren Latenzanforderungen geeignet, da sie über einen dauerhaften Messagingkanal verfügen.

Beim Verwenden der per .NET verwalteten APIs sind die Hauptkonstrukte für die Veröffentlichung von Daten auf Event Hubs die Klassen [EventHubClient][] und [EventData][]. [EventHubClient][] stellt den AMQP-Kommunikationskanal bereit, über den Ereignisse an Event Hub gesendet werden. Die [EventData][]-Klasse stellt ein Ereignis dar und wird verwendet, um Nachrichten auf einem Event Hub zu veröffentlichen. Diese Klasse enthält den Text, einige Metadaten sowie Headerinformationen zum Ereignis. Dem [EventData][]-Objekt werden weitere Eigenschaften hinzugefügt, wenn es einen Event Hub durchläuft.

## <a name="get-started"></a>Erste Schritte
Die .NET-Klassen, die Event Hubs unterstützen, werden im NuGet-Paket [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) bereitgestellt. Die Installation kann mithilfe des Projektmappen-Explorers von Visual Studio oder über die [Paket-Manager-Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) in Visual Studio erfolgen. Geben Sie hierzu im Fenster der [Paket-Manager-Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) den folgenden Befehl ein:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Sie können das Azure-Portal, Azure PowerShell oder die Azure CLI zum Erstellen von Event Hubs verwenden. Ausführliche Informationen finden Sie unter [Erstellen eines Event Hubs-Namespace und eines Event Hubs mithilfe des Azure-Portals](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Erstellen eines Event Hubs-Clients

Die Hauptklasse für die Interaktion mit Event Hubs ist [Microsoft.Azure.EventHubs.EventHubClient][EventHubClient]. Sie können diese Klasse mit der [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring)-Methode instanziieren. Dies wird im folgenden Beispiel veranschaulicht:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Senden von Ereignissen an einen Event Hub

Sie senden Ereignisse an einen Event Hub, indem Sie eine [EventHubClient][]-Instanz erstellen und diese asynchron mit der [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)-Methode senden. Diese Methode verwendet einen einzelnen [EventData][]-Instanzparameter und sendet ihn asynchron an einen Event Hub.

## <a name="event-serialization"></a>Ereignisserialisierung

Die [EventData][]-Klasse verfügt über [zwei überladene Konstruktoren](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor), die verschiedene Parameter, Bytes oder ein Bytearray zur Darstellung der Ereignisdatennutzlast akzeptieren. Wenn Sie JSON mit [EventData][] verwenden, können Sie mit **Encoding.UTF8.GetBytes()** das Bytearray für eine JSON-codierte Zeichenfolge abrufen. Beispiel:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partitionsschlüssel

Beim Senden von Daten können Sie einen gehashten Wert angeben, um eine Partitionszuweisung zu generieren. Sie geben die Partition mithilfe der Eigenschaft [PartitionSender.PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) an. Wenn Sie sich für Partitionen entscheiden, müssen Sie jedoch zwischen Verfügbarkeit und Konsistenz wählen. 

### <a name="availability-considerations"></a>Überlegungen zur Verfügbarkeit

Die Verwendung eines Partitionsschlüssels ist optional, und Sie sollten dies sorgfältig abwägen. Wenn Sie beim Veröffentlichen eines Ereignisses keinen Partitionsschlüssel angeben, wird eine Roundrobinzuordnung verwendet. In vielen Fällen ist die Verwendung eines Partitionsschlüssels empfehlenswert, wenn die Ereignisreihenfolge wichtig ist. Wenn Sie einen Partitionsschlüssel verwenden, ist für diese Partitionen die Verfügbarkeit auf einem einzelnen Knoten erforderlich, und mit der Zeit kann es zu Ausfällen kommen, beispielsweise beim Neustart und Patchen von Computeknoten. Wenn Sie daher eine Partitions-ID festlegen und diese Partition aus irgendeinem Grund nicht verfügbar ist, führt der Zugriff auf die Daten in dieser Partition zu einem Fehler. Wenn die Hochverfügbarkeit an erster Stelle steht, geben Sie keinen Partitionsschlüssel an. In diesem Fall werden Ereignisse über das zuvor beschriebene Roundrobin-Modell an Partitionen gesendet. In diesem Szenario treffen Sie explizit die Wahl zwischen Verfügbarkeit (keine Partitions-ID) und Konsistenz (Anheften von Ereignissen an eine Partitions-ID).

Ein weiterer Aspekt ist der Umgang mit Verzögerungen bei der Ereignisverarbeitung. In einigen Fällen kann es besser sein, Daten zu löschen und den Vorgang zu wiederholen, anstatt zu versuchen, mit der Verarbeitung Schritt zu halten, wodurch möglicherweise weitere Verzögerungen bei der Downstreamverarbeitung verursacht werden. Bei einem Börsenticker ist es z.B. besser, auf vollständige aktuelle Daten zu warten. In einem Livechat oder VOIP-Szenario, möchten Sie die Daten hingegen schnell erhalten, selbst wenn sie nicht vollständig sind.

Vor dem Hintergrund dieser Überlegungen zur Verfügbarkeit können Sie eine der folgenden Strategien zur Fehlerbehandlung wählen:

- Beenden (Auslesen von Event Hubs beenden, bis die Fehler behoben sind)
- Löschen (Nachrichten sind unwichtig und können gelöscht werden)
- Wiederholen (Nachrichtenübermittlung angemessen wiederholen)

Weitere Informationen und eine Erläuterung zu den Vor-und Nachteilen zwischen Verfügbarkeit und Konsistenz finden Sie unter [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Sendevorgänge für Batchereignisse

Das Senden von Ereignissen in Batches kann den Durchsatz erhöhen. Mithilfe der [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch)-API können Sie einen Batch erstellen, dem später Datenobjekte für einen [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync)-Aufruf hinzugefügt werden können.

Ein einzelner Batch darf den Grenzwert von 1 MB für ein Ereignis nicht überschreiten. Darüber hinaus wird für jede Nachricht im Batch die gleiche Herausgeberidentität (Publisher Identity) verwendet. Der Absender ist dafür verantwortlich sicherzustellen, dass die maximale Ereignisgröße für den Batch nicht überschritten wird. Bei einer Überschreitung wird ein **Send** -Fehler für den Client generiert. Sie können mit der Hilfsprogrammmethode [EventHubClient.CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) sicherstellen, dass der Batch 1 MB nicht überschreitet. Sie erhalten von der [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch)-API ein leeres [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch)-Element, und verwenden dann [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd), um Ereignisse hinzuzufügen und den Batch zu erstellen. 

## <a name="send-asynchronously-and-send-at-scale"></a>Asynchrones Senden und Senden mit Skalierung

Sie senden Ereignisse asynchron an einen Event Hub. Beim asynchronen Senden wird die Rate erhöht, mit der ein Client Ereignisse senden kann. [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) gibt ein [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx)-Objekt zurück. Sie können die [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy)-Klasse auf dem Client verwenden, um Wiederholungsversuche des Clients zu steuern.

## <a name="event-consumers"></a>Ereignisconsumer
Die [EventProcessorHost][] -Klasse verarbeitet Daten aus Event Hubs. Sie sollten diese Implementierung verwenden, wenn Sie Ereignisleser auf der .NET-Plattform erstellen. [EventProcessorHost][] wird eine threadsichere Laufzeitumgebung mit mehreren Prozessen für Ereignisprozessorimplementierungen bereitgestellt, die auch die Erstellung von Prüfpunkten und die Leaseverwaltung für Partitionen ermöglicht.

Sie können [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementieren, um die [EventProcessorHost][]-Klasse zu verwenden. Diese Schnittstelle enthält vier Methoden:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Instanziieren Sie zum Starten der Ereignisverarbeitung die [EventProcessorHost][]-Klasse, und geben Sie die entsprechenden Parameter für Ihren Event Hub an. Beispiel:

> [!NOTE]
> EventProcessorHost und seine verwandten Klassen werden im **Microsoft.Azure.EventHubs.Processor**-Paket bereitgestellt. Fügen Sie das Paket anhand der Anweisungen in [diesem Artikel](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) oder durch Eingabe des folgenden Befehls im Fenster der [Paket-Manager-Konsole](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) Ihrem Visual Studio-Projekt hinzu: `Install-Package Microsoft.Azure.EventHubs.Processor`.

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Rufen Sie anschließend [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) auf, um Ihre [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-Implementierung bei der Laufzeit zu registrieren:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

An diesem Punkt versucht der Host, einen Lease für jede Partition im Event Hub zu erhalten, indem er einen „gierigen“ Algorithmus verwendet. Diese Leases gelten für einen bestimmten Zeitraum und müssen anschließend erneuert werden. Wenn neue Knoten (hier: Workerinstanzen) in den Onlinezustand versetzt werden, geben sie Leasereservierungen heraus. Im Laufe der Zeit wird die Arbeitsauslastung dann auf die Knoten verteilt, da jeder Knoten versucht, mehr Leases zu erlangen.

![Ereignisprozessorhost](./media/event-hubs-programming-guide/IC759863.png)

Im Laufe der Zeit wird somit ein Gleichgewicht erreicht. Diese dynamische Funktion ermöglicht, dass die CPU-basierte automatische Skalierung sowohl beim zentralen Herunterskalieren als auch beim zentralen Hochskalieren auf Consumer angewendet wird. Da Event Hubs nicht über ein direktes Konzept in Bezug auf die Nachrichtenanzahl verfügen, ist die durchschnittliche CPU-Auslastung häufig das beste Verfahren zum Messen der Back-End- bzw. Consumerskalierung. Wenn Herausgeber beginnen, mehr Ereignisse zu veröffentlichen, als von den Consumern verarbeitet werden können, kann der CPU-Anstieg auf den Consumern verwendet werden, um eine automatische Skalierung in Bezug auf die Anzahl der Workerinstanzen auszulösen.

Außerdem implementiert die [EventProcessorHost][] -Klasse ein auf Azure Storage-basiertes Verfahren für die Prüfpunktausführung. Bei diesem Verfahren wird der Offset pro Partition gespeichert, damit jeder Consumer ermitteln kann, wie der letzte Prüfpunkt des vorherigen Consumers lautete. Da Partitionen per Lease zwischen Knoten wechseln, ist dies das Synchronisierungsverfahren, das die Auslastungsverteilung ermöglicht.

## <a name="publisher-revocation"></a>Herausgebersperrung

Zusätzlich zu den erweiterten Laufzeitfunktionen von [EventProcessorHost][] ermöglichen Event Hubs die Herausgebersperrung, damit bestimmte Herausgeber blockiert werden und keine Ereignisse an einen Event Hub senden können. Diese Funktionen sind nützlich, wenn das Token eines Herausgebers gefährdet ist oder ein Softwareupdate ein unangemessenes Verhalten bewirkt. In diesen Fällen kann die Identität des Herausgebers, die Teil des SAS-Tokens ist, blockiert werden, um das Veröffentlichen von Ereignissen zu verhindern.

Weitere Informationen zum Sperren von Herausgebern und zum Senden an Event Hubs als Herausgeber finden Sie im Beispiel [Service Bus Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Service Bus Event Hubs – Sicheres Veröffentlichen in größerem Umfang).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs-Szenarien finden Sie unter diesen Links:

* [Übersicht über die Event Hubs-API](event-hubs-api-overview.md)
* [Was sind Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Verfügbarkeit und Konsistenz in Event Hubs](event-hubs-availability-and-consistency.md)
* [Referenz zur Ereignisprozessorhost-API](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
