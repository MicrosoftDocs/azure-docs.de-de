---
title: 'Senden und Empfangen von Ereignissen unter Verwendung von Java: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine exemplarische Vorgehensweise für die Erstellung einer Java-Anwendung, die Ereignisse an Azure Event Hubs sendet.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 0487cac6a0cf7d37befdf0d7cfab33ad6a62cf7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822908"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-java"></a>Senden von Ereignissen an oder Empfangen von Ereignissen von Event Hubs mithilfe von Java

Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In diesem Tutorial wird gezeigt, wie Sie Java-Anwendungen erstellen, die Ereignisse an einen Event Hub senden oder von diesem empfangen. 

> [!NOTE]
> Sie können diese Schnellstartvorlage als Beispiel aus [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) herunterladen, die Zeichenfolgen `EventHubConnectionString` und `EventHubName` durch die Werte für Ihren Event Hub ersetzen und das Beispiel dann ausführen. Alternativ können Sie auch die Schritte in diesem Tutorial ausführen, um eine eigene Vorlage zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- Ein aktives Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
- Eine Java-Entwicklungsumgebung. In diesem Lernprogramm wird [Eclipse](https://www.eclipse.org/) verwendet.
- **Erstellen eines Event Hubs-Namespace und eines Event Hubs**. Verwenden Sie zunächst das [Azure-Portal](https://portal.azure.com), um einen Namespace vom Typ „Event Hubs“ zu erstellen, und beschaffen Sie die Verwaltungsanmeldeinformationen, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. Gehen Sie anschließend wie im Artikel beschrieben vor, um den Wert des Zugriffsschlüssels für den Event Hub abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Sie verwenden den Zugriffsschlüssel im Code, den Sie später in diesem Tutorial schreiben. Der Standardschlüsselname lautet: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Senden von Ereignisse 
In diesem Abschnitt erfahren Sie, wie Sie eine Java-Anwendung zum Senden von Ereignissen an einen Event Hub erstellen. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Hinzufügen von Verweisen auf die Azure Event Hubs-Bibliothek

Die Java-Clientbibliothek für Event Hubs steht für die Verwendung in Maven-Projekten über das [zentrale Maven-Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) bereit. Sie können über die folgende Abhängigkeitsdeklaration in Ihrer Maven-Projektdatei auf diese Bibliothek verweisen:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Für unterschiedliche Arten von Buildumgebungen können Sie explizit die zuletzt veröffentlichten JAR-Dateien aus dem [zentralen Maven-Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) abrufen.  

Um einen einfachen Ereignisherausgeber zu erhalten, importieren Sie das Paket *com.microsoft.azure.eventhubs* für die Event Hubs-Clientklassen und das Paket *com.microsoft.azure.servicebus* für Hilfsklassen, z.B. häufige Ausnahmen für den Azure Service Bus-Messagingclient. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Schreiben von Code zum Senden von Nachrichten an den Event Hub

Erstellen Sie für das folgende Beispiel zuerst ein neues Maven-Projekt für eine Konsolen-/Shellanwendung in Ihrer bevorzugten Java-Entwicklungsumgebung. Fügen Sie eine Klasse namens `SimpleSend` sowie den folgenden Code zur Klasse hinzu:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Erstellen einer Verbindungszeichenfolge

Verwenden Sie die ConnectionStringBuilder-Klasse, um einen Verbindungszeichenfolgenwert an die Event Hubs-Clientinstanz zu übergeben. Ersetzen Sie die Platzhalter in Klammern durch die Werte, die beim Erstellen des Namespace und von Event Hub abgerufen wurden:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("speventhubns") 
                .setEventHubName("speventhub")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("2+WMsyyy1XmUtEnRsfOmTTyGasfJgsVjGAOIN20J1Y8=");
```

### <a name="write-code-to-send-events"></a>Schreiben von Code zum Senden von Ereignissen

Erstellen Sie ein einzelnes Ereignis, indem Sie eine Zeichenfolge in die entsprechende UTF-8-Bytecodierung umwandeln. Erstellen Sie dann auf der Grundlage der Verbindungszeichenfolge eine neue Event Hubs-Clientinstanz, und senden Sie die Nachricht:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Erstellen Sie das Programm, führen Sie es aus, und stellen Sie sicher, dass keine Fehler ausgelöst werden.

Glückwunsch! Sie haben jetzt Nachrichten an einen Event Hub gesendet.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Anhang: So werden Nachrichten an Event Hub-Partitionen weitergeleitet

Bevor die Nachrichten von Consumern abgerufen werden, müssen sie zuerst von den Herausgebern an die Partitionen veröffentlicht werden. Wenn Nachrichten mit der sendSync()-Methode auf dem Objekt „com.microsoft.azure.eventhubs.EventHubClient“ synchron an Event Hub veröffentlichen werden, kann die Nachricht je nachdem, ob der Partitionsschlüssel angegeben ist oder nicht, an eine bestimmte Partition gesendet oder anhand der Roundrobin-Methode an alle verfügbaren Partitionen verteilt werden.

Wenn eine Zeichenfolge angegeben ist, die den Partitionsschlüssel darstellt, wird der Schlüssel angegeben wird, wird ein Hashwert für den Schlüssel erstellt, um zu bestimmen, an welche Partition das Ereignis gesendet wird.

Wenn der Partitionsschlüssel nicht festgelegt ist, werden Nachrichten per Roundrobin an alle verfügbaren Partitionen gesendet.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Empfangen von Ereignissen
Der Code in diesem Tutorial basiert auf dem [EventProcessorSample-Code auf GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), den Sie untersuchen können, um die vollständige funktionierende Anwendung zu sehen.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Empfangen von Nachrichten mit EventProcessorHost in Java

**EventProcessorHost** ist eine Java-Klasse, die das Empfangen von Ereignissen von Event Hubs durch die Verwaltung von permanenten Prüfpunkten und parallelen Empfangsvorgängen von diesen Event Hubs vereinfacht. Mit EventProcessorHost können Sie Ereignisse selbst dann auf mehrere Empfänger aufteilen, wenn diese auf verschiedenen Knoten gehostet werden. Dieses Beispiel zeigt, wie EventProcessorHost für einen einzelnen Empfänger verwendet wird.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Zum Verwenden von EventProcessorHost benötigen Sie ein [Azure Storage-Konto][Azure Storage-Konto]:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf der linken Seite des Bildschirms auf **+ Ressource** erstellen.
2. Klicken Sie auf **Storage** und anschließend auf **Speicherkonto**. Geben Sie im Fenster **Speicherkonto erstellen** einen Namen für das Speicherkonto ein. Füllen Sie die restlichen Felder aus, wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Erstellen**.
   
    ![Speicherkonto erstellen](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Klicken Sie auf das neu erstellte Speicherkonto und anschließend auf **Zugriffsschlüssel**:
   
    ![Abrufen von Zugriffsschlüsseln](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Kopieren Sie den Wert von „key1“ an einen temporären Speicherort. Sie benötigen ihn später in diesem Tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Erstellen eines Java-Projekts mit EventProcessorHost

Die Java-Clientbibliothek für Event Hubs steht für die Verwendung in Maven-Projekten im [zentralen Maven-Repository](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22) zur Verfügung. Sie können darauf verweisen, indem Sie in der Maven-Projektdatei die folgende Abhängigkeitsdeklaration verwenden: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Für unterschiedliche Arten von Buildumgebungen können Sie explizit die zuletzt veröffentlichten JAR-Dateien aus dem [zentralen Maven-Repository][https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22 ] abrufen.  

1. Erstellen Sie für das folgende Beispiel zuerst ein neues Maven-Projekt für eine Konsolen-/Shellanwendung in Ihrer bevorzugten Java-Entwicklungsumgebung. Die Klasse hat die Bezeichnung `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Verwenden Sie den folgenden Code, um eine neue Klasse mit dem Namen `EventProcessorSample`zu erstellen. Ersetzen Sie die Platzhalter durch die Werte, die Sie beim Erstellen des Event Hubs und des Speicherkontos verwendet haben:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Erstellen Sie eine weitere Klasse mit dem Namen `EventProcessor` und dem folgenden Code:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Dieses Lernprogramm verwendet eine einzelne Instanz von EventProcessorHost. Zur Erhöhung des Durchsatzes wird empfohlen, mehrere Instanzen von EventProcessorHost auszuführen, wenn möglich auf verschiedenen Computern.  Auf diese Weise wird ebenfalls Redundanz erzielt. In diesen Fällen koordinieren sich die verschiedenen automatisch untereinander, um die Last der eingegangenen Ereignisse ausgeglichen zu verteilen. Wenn mehrere Empfänger für jeden Prozess *alle* Ereignisse verarbeiten sollen, müssen Sie das **ConsumerGroup** -Konzept verwenden. Wenn Ereignisse von anderen Computern empfangen werden, kann es hilfreich sein, die EventProcessorHost-Instanzen nach den Computern (oder Rollen) zu benennen, auf denen sie bereitgestellt worden sind.

### <a name="publishing-messages-to-eventhub"></a>Veröffentlichen von Nachrichten an EventHub

Bevor die Nachrichten von Consumern abgerufen werden, müssen sie zuerst von den Herausgebern an die Partitionen veröffentlicht werden. Hinweis: Wenn Nachrichten mit der sendSync()-Methode auf dem Objekt „com.microsoft.azure.eventhubs.EventHubClient“ synchron an Event Hub veröffentlichen werden, kann die Nachricht je nachdem, ob der Partitionsschlüssel angegeben ist oder nicht, an eine bestimmte Partition gesendet oder anhand der Roundrobin-Methode an alle verfügbaren Partitionen verteilt werden.

Wenn eine Zeichenfolge angegeben ist, die den Partitionsschlüssel darstellt, wird der Schlüssel angegeben wird, wird ein Hashwert für den Schlüssel erstellt, um zu bestimmen, an welche Partition das Ereignis gesendet wird.

Wenn der Partitionsschlüssel nicht festgelegt ist, werden Nachrichten mithilfe von Roundrobin an alle verfügbaren Partitionen gesendet.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementieren eines benutzerdefinierten Prüfpunkt-Managers für EventProcessorHost (EPH)

Die API bietet einen Mechanismus zum Implementieren Ihres benutzerdefinierten Prüfpunkt-Managers für Szenarien, in denen die Standardimplementierung nicht mit Ihrem Anwendungsfall kompatibel ist.

Der standardmäßige Prüfpunkt-Manager verwendet Blobspeicher, aber wenn Sie den von EPH verwendeten Prüfpunkt-Manager durch Ihre eigene Implementierung ersetzen, können Sie Speicher Ihrer Wahl zum Sichern Ihrer Prüfpunkt-Manager-Implementierung verwenden.

Erstellen Sie eine Klasse, die die Schnittstelle „com.microsoft.azure.eventprocessorhost.ICheckpointManager“ implementiert.

Verwenden Sie Ihre benutzerdefinierte Implementierung des Prüfpunkts-Managers (com.microsoft.azure.eventprocessorhost.ICheckpointManager).

In Ihrer Implementierung können Sie den standardmäßigen Prüfpunktmechanismus außer Kraft setzen und Ihre eigenen Prüfpunkte basierend auf Ihrem eigenen Datenspeicher (wie SQL Server, CosmosDB und Azure-Cache für Redis) implementieren. Es wird empfohlen, dass der zum Sichern Ihrer Prüfpunkt-Manager-Implementierung verwendete Speicher für alle EPH-Instanzen zugänglich ist, die Ereignisse für die Consumergruppe verarbeiten.

Sie können einen beliebigen Datenspeicher verwenden, der in Ihrer Umgebung verfügbar ist.

Die Klasse „com.microsoft.azure.eventprocessorhost.EventProcessorHost“ bietet Ihnen zwei Konstruktoren, mit denen Sie den Prüfpunkt-Manager für Ihre EventProcessorHost-Instanz außer Kraft setzen können.


## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Features und Terminologie in Azure Event Hubs](event-hubs-features.md)
- [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)

