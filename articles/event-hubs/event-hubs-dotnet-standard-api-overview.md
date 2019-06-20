---
title: Übersicht über die .NET Standard-APIs in Azure Event Hubs | Microsoft-Dokumentation
description: .NET Standard-API – Übersicht
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/13/2018
ms.author: shvija
ms.openlocfilehash: b09f39f45936a7c43dbc1ef109780315d62c768f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821905"
---
# <a name="event-hubs-net-standard-api-overview"></a>Event Hubs .NET Standard-API – Übersicht

In diesem Artikel werden einige der wichtigsten [.NET Standard-Client-APIs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) von Azure Event Hubs zusammengefasst. Derzeit gibt es zwei .NET Standard-Clientbibliotheken für Event Hubs:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs): Enthält alle grundlegenden Laufzeitvorgänge.
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor): Dient zum Hinzufügen von zusätzlichen Funktionen, die es ermöglichen, verarbeitete Ereignisse nachzuverfolgen. Dies ist die einfachste Möglichkeit zum Lesen aus einem Event Hub.

## <a name="event-hubs-client"></a>Event Hubs-Client

[EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) ist das primäre Objekt zum Senden von Ereignissen, Erstellen von Empfängern und Abrufen von Laufzeitinformationen. Dieser Client ist mit einem bestimmten Event Hub verknüpft und erstellt eine neue Verbindung mit dem Event Hubs-Endpunkt.

### <a name="create-an-event-hubs-client"></a>Erstellen eines Event Hubs-Clients

Ein [EventHubClient](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)-Objekt wird aus einer Verbindungszeichenfolge erstellt. Die einfachste Möglichkeit zum Instanziieren eines neuen Clients sehen Sie im folgenden Beispiel:

```csharp
var eventHubClient = EventHubClient.CreateFromConnectionString("Event Hubs connection string");
```

Sie können die Verbindungszeichenfolge programmgesteuert bearbeiten, indem Sie die [EventHubsConnectionStringBuilder](/dotnet/api/microsoft.azure.eventhubs.eventhubsconnectionstringbuilder)-Klasse verwenden und die Verbindungszeichenfolge als Parameter an [EventHubClient.CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient) übergeben.

```csharp
var connectionStringBuilder = new EventHubsConnectionStringBuilder("Event Hubs connection string")
{
    EntityPath = EhEntityPath
};

var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

### <a name="send-events"></a>Senden von Ereignisse

Mithilfe der [EventData](/dotnet/api/microsoft.azure.eventhubs.eventdata)-Klasse können Sie Ereignisse an einen Event Hub senden. Der Text muss ein `byte`-Array oder ein `byte`-Arraysegment sein.

```csharp
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set user properties if needed
data.Properties.Add("Type", "Informational");
// Send single message async
await eventHubClient.SendAsync(data);
```

### <a name="receive-events"></a>Empfangen von Ereignissen

Zum Empfangen von Ereignissen von Event Hubs empfiehlt sich die Verwendung eines [Ereignisprozessorhosts](#event-processor-host-apis), der eine Funktion zum automatischen Nachverfolgen von Offset und Partitionsinformationen des Event Hubs verfügbar macht. In bestimmten Situationen bevorzugen Sie jedoch unter Umständen die Flexibilität der Event Hubs-Kernbibliothek, um Ereignisse zu empfangen.

#### <a name="create-a-receiver"></a>Erstellen eines Receivers

Receiver sind an bestimmte Partitionen gebunden. Damit alle Ereignisse in einem Event Hub empfangen werden können, müssen Sie mehrere Instanzen erstellen. Es empfiehlt sich, die Partitionsinformationen programmgesteuert abzurufen und nicht die Partitions-IDs fest zu programmieren. Hierfür können Sie die [GetRuntimeInformationAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient)-Methode verwenden.

```csharp
// Create a list to keep track of the receivers
var receivers = new List<PartitionReceiver>();
// Use the eventHubClient created above to get the runtime information
var runTimeInformation = await eventHubClient.GetRuntimeInformationAsync();
// Loop over the resulting partition IDs
foreach (var partitionId in runTimeInformation.PartitionIds)
{
    // Create the receiver
    var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);
    // Add the receiver to the list
    receivers.Add(receiver);
}
```

Da Ereignisse niemals aus einem Event Hub entfernt werden, sondern nur ablaufen, müssen Sie den korrekten Startpunkt festlegen. Das folgende Beispiel zeigt mögliche Kombinationen:

```csharp
// partitionId is assumed to come from GetRuntimeInformationAsync()

// Using the constant PartitionReceiver.EndOfStream only receives all messages from this point forward.
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, PartitionReceiver.EndOfStream);

// All messages available
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, "-1");

// From one day ago
var receiver = eventHubClient.CreateReceiver(PartitionReceiver.DefaultConsumerGroupName, partitionId, DateTime.Now.AddDays(-1));
```

#### <a name="consume-an-event"></a>Nutzen eines Ereignisses

```csharp
// Receive a maximum of 100 messages in this call to ReceiveAsync
var ehEvents = await receiver.ReceiveAsync(100);
// ReceiveAsync can return null if there are no messages
if (ehEvents != null)
{
    // Since ReceiveAsync can return more than a single event you will need a loop to process
    foreach (var ehEvent in ehEvents)
    {
        // Decode the byte array segment
        var message = UnicodeEncoding.UTF8.GetString(ehEvent.Body.Array);
        // Load the custom property that we set in the send example
        var customType = ehEvent.Properties["Type"];
        // Implement processing logic here
    }
}       
```

## <a name="event-processor-host-apis"></a>Ereignisprozessorhost-APIs

Diese APIs bieten Flexibilität für Arbeitsprozesse, die möglicherweise nicht mehr verfügbar sind, indem Partitionen auf verfügbare Worker aufgeteilt werden:

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.

// Read these connection strings from a secure location
var ehConnectionString = "{Event Hubs connection string}";
var ehEntityPath = "{event hub path/name}";
var storageConnectionString = "{Storage connection string}";
var storageContainerName = "{Storage account container name}";

var eventProcessorHost = new EventProcessorHost(
    ehEntityPath,
    PartitionReceiver.DefaultConsumerGroupName,
    ehConnectionString,
    storageConnectionString,
    storageContainerName);

// Start/register an EventProcessorHost
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

// Disposes the Event Processor Host
await eventProcessorHost.UnregisterEventProcessorAsync();
```

Im Folgenden finden Sie eine Beispielimplementierung der [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor)-Schnittstelle:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    public Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
        return Task.CompletedTask;
    }

    public Task OpenAsync(PartitionContext context)
    {
        Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
        return Task.CompletedTask;
    }

    public Task ProcessErrorAsync(PartitionContext context, Exception error)
    {
        Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
        return Task.CompletedTask;
    }

    public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (var eventData in messages)
        {
            var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
            Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
        }

        return context.CheckpointAsync();
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs-Szenarien finden Sie unter diesen Links:

* [Was ist Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Verfügbare Event Hubs-APIs](event-hubs-api-overview.md)

Die .NET-API-Referenzen sind hier verfügbar:

* [Microsoft.Azure.EventHubs](/dotnet/api/microsoft.azure.eventhubs)
* [Microsoft.Azure.EventHubs.Processor](/dotnet/api/microsoft.azure.eventhubs.processor)
