---
title: Übersicht über die .NET Framework-APIs in Azure Event Hubs | Microsoft-Dokumentation
description: Hier finden Sie eine Zusammenfassung der wichtigsten .NET Framework-Client-APIs in Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: dotnet
ms.topic: article
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: f56882d2d73c85434c6f81972a06e5ea9ccdff99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822043"
---
# <a name="event-hubs-net-framework-api-overview"></a>Übersicht über die .NET Framework-API in Event Hubs

In diesem Artikel werden einige der wichtigsten [.NET Framework-Client-APIs](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) von Azure Event Hubs zusammengefasst. Es gibt zwei Kategorien: Verwaltungs- und Laufzeit-APIs. Laufzeit-APIs umfassen alle Vorgänge, die zum Senden und Empfangen einer Nachricht erforderlich sind. Verwaltungsvorgänge ermöglichen das Verwalten des Entitätszustands von Event Hubs durch das Erstellen, Aktualisieren und Löschen von Entitäten.

[Überwachungsszenarien](event-hubs-metrics-azure-monitor.md) umfassen sowohl Verwaltung als auch Laufzeit. Eine ausführliche Referenzdokumentation zu den .NET-APIs finden Sie in den Referenzen zu [.NET Framework](/dotnet/api/microsoft.servicebus.messaging.eventhubclient), [.NET Standard](/dotnet/api/microsoft.azure.eventhubs) und zur [EventProcessorHost-API](/dotnet/api/microsoft.azure.eventhubs.processor).

## <a name="management-apis"></a>Verwaltungs-APIs

Um die folgenden Verwaltungsvorgänge auszuführen, benötigen Sie **Verwaltungsberechtigungen** für den Event Hubs-Namespace:

### <a name="create"></a>Erstellen

```csharp
// Create the event hub
var ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
await namespaceManager.CreateEventHubAsync(ehd);
```

### <a name="update"></a>Aktualisieren

```csharp
var ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
var ruleName = "myeventhubmanagerule";
var ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
await namespaceManager.UpdateEventHubAsync(ehd);
```

### <a name="delete"></a>Löschen

```csharp
await namespaceManager.DeleteEventHubAsync("event hub name");
```

## <a name="run-time-apis"></a>Laufzeit-APIs
### <a name="create-publisher"></a>Herausgeber erstellen

```csharp
// EventHubClient model (uses implicit factory instance, so all links on same connection)
var eventHubClient = EventHubClient.Create("event hub name");
```

### <a name="publish-message"></a>Meldung veröffentlichen

```csharp
// Create the device/temperature metric
var info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
var data = new EventData(new byte[10]); // Byte array
var data = new EventData(Stream); // Stream 
var data = new EventData(info, serializer) //Object and serializer 
{
    PartitionKey = info.DeviceId.ToString()
};

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Consumer erstellen

```csharp
// Create the Event Hubs client
var eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
var defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index);

// From one day ago
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));

// From specific offset, -1 means oldest
var consumer = await defaultConsumerGroup.CreateReceiverAsync(partitionId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Meldung verarbeiten

```csharp
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)

// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Ereignisprozessorhost-APIs

Diese APIs bieten Flexibilität für Arbeitsprozesse, die möglicherweise nicht mehr verfügbar sind, indem Partitionen auf verfügbare Worker aufgeteilt werden.

```csharp
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

var eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
var blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

var eventHubDescription = new EventHubDescription(EventHubName);
var host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
await host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
await host.UnregisterEventProcessorAsync();
```

Die [IEventProcessor](/dotnet/api/microsoft.servicebus.messaging.ieventprocessor)-Schnittstelle wird wie folgt definiert:

```csharp
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            // Process messages here
        }

        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }

    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs-Szenarien finden Sie unter diesen Links:

* [Was ist Azure Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md)

Die .NET-API-Referenzen sind hier verfügbar:

* [Microsoft.ServiceBus.Messaging](/dotnet/api/microsoft.servicebus.messaging)
* [Microsoft.Azure.EventHubs.EventProcessorHost](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost)
