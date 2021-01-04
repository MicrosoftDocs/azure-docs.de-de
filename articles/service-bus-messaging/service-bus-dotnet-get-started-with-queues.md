---
title: Erste Schritte mit Azure Service Bus-Warteschlangen (Azure.Messaging.ServiceBus)
description: In diesem Tutorial erstellen Sie eine .NET Core-Anwendung in C#, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f55af61a061bf3a3897569058aace728f7465b64
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862121"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Senden und Empfangen von Nachrichten für Azure Service Bus-Warteschlangen (.NET)
In diesem Tutorial erstellen Sie eine .NET Core-Konsolenanwendung, um Nachrichten für eine Service Bus-Warteschlange zu senden und zu empfangen, indem Sie das Paket **Azure.Messaging.ServiceBus** verwenden. 

> [!Important]
> In dieser Schnellstartanleitung wird das neue Paket „Azure.Messaging.ServiceBus“ verwendet. Eine Schnellstartanleitung, in der das alte Paket „Microsoft.Azure.ServiceBus“ verwendet wird, finden Sie im Artikel mit den Informationen zum [Senden und Empfangen von Ereignissen mit dem Paket „Microsoft.Azure.ServiceBus“](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen. Notieren Sie sich die **Verbindungszeichenfolge** für Ihren Service Bus-Namespace und den Namen der **Warteschlange**, die Sie erstellt haben.

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
In dieser Schnellstartanleitung erstellen Sie eine .NET Core-Konsolenanwendung in C#, um Nachrichten an die Warteschlange zu senden.

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung
Starten Sie Visual Studio, und erstellen Sie ein neues Projekt vom Typ **Konsolen-App (.NET Core)** für C#. 

### <a name="add-the-service-bus-nuget-package"></a>Hinzufügen des NuGet-Pakets "Service Bus"

1. Klicken Sie mit der rechten Maustaste auf das neu erstellte Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Wählen Sie **Durchsuchen** aus. Suchen Sie nach dem Paket **[Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)** , und wählen Sie es aus.
1. Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie danach den NuGet-Package-Manager.

### <a name="add-code-to-send-messages-to-the-queue"></a>Hinzufügen von Code für das Senden von Nachrichten an die Warteschlange

1. Fügen Sie in *Program.cs* am Beginn der Namespacedefinition die folgenden `using`-Anweisungen vor der Klassendeklaration hinzu:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. Deklarieren Sie in der Klasse `Program` die folgenden Variablen:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Geben Sie die Verbindungszeichenfolge für den Namespace als `connectionString`-Variable ein. Geben Sie den Warteschlangennamen ein.

1. Fügen Sie direkt nach der Methode `Main()` die folgende Methode vom Typ `SendMessagesAsync()` hinzu, um eine Nachricht zu senden:

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Fügen Sie eine Methode mit dem Namen `CreateMessages` hinzu, um eine Warteschlange (.NET-Warteschlange) mit Nachrichten für die `Program`-Klasse zu erstellen. Normalerweise erhalten Sie diese Nachrichten von den verschiedenen Teilen Ihrer Anwendung. Hier erstellen wir eine Warteschlange mit Beispielnachrichten.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. Fügen Sie der `Program`-Klasse eine Methode mit dem Namen `SendMessageBatchAsync` und den folgenden Code hinzu. Bei dieser Methode wird eine Warteschlange mit Nachrichten verwendet, und es werden ein oder mehrere Batches für das Senden an die Service Bus-Warteschlange vorbereitet. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Ersetzen Sie die `Main()`-Methode durch die folgende **asynchrone** `Main`-Methode. Hiermit werden die beiden Sendemethoden aufgerufen, um eine einzelne Nachricht und einen Batch mit Nachrichten an die Warteschlange zu senden. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Führen Sie die Anwendung aus. Die unten angegebenen Meldungen sollten angezeigt werden. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. Gehen Sie im Azure-Portal wie folgt vor:
    1. Navigieren Sie zu Ihrem Service Bus-Namespace. 
    1. Wählen Sie auf der Seite **Übersicht** die Warteschlange unten im mittleren Bereich aus. 
    1. Beachten Sie die Werte im Abschnitt **Essentials**.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Empfangene Nachrichten mit Anzahl und Größe" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Achten Sie auf die folgenden Werte:
    - Unter **Anzahl aktiver Nachrichten** ist für die Warteschlange nun der Wert **4** angegeben. Jedes Mal, wenn Sie diese Absender-App ausführen, ohne die Nachrichten abzurufen, erhöht sich dieser Wert um 4.
    - Bei der aktuellen Warteschlangengröße erhöht sich der Wert **AKTUELL** unter **Zusammenfassung** jeweils, wenn der Warteschlange durch die App Nachrichten hinzugefügt werden.
    - Im unteren Abschnitt **Metriken** im Diagramm **Nachrichten** können Sie sehen, dass für die Warteschlange vier eingehende Nachrichten vorhanden sind. 

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
In diesem Abschnitt fügen Sie Code hinzu, mit dem Nachrichten aus der Warteschlange abgerufen werden.

1. Fügen Sie der `Program`-Klasse die folgenden Methoden hinzu, mit denen Nachrichten und Fehler verarbeitet werden. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Fügen Sie der `Program`-Klasse eine Methode mit dem Namen `ReceiveMessagesAsync` und den folgenden Code hinzu, um Nachrichten zu empfangen. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Fügen Sie einen Aufruf der `ReceiveMessagesAsync`-Methode über die `Main`-Methode hinzu. Kommentieren Sie die `SendMessagesAsync`-Methode aus, falls Sie nur das Empfangen von Nachrichten testen möchten. Wenn Sie dies nicht tun, sehen Sie, dass weitere vier Nachrichten an die Warteschlange gesendet werden. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Ausführen der App
Führen Sie die Anwendung aus. Warten Sie eine Minute lang, und drücken Sie dann eine beliebige Taste, um den Empfang von Nachrichten zu beenden. Die folgende Ausgabe sollte angezeigt werden (Leertaste für Schlüssel). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Sehen Sie sich erneut die Informationen im Portal an. 

- Die Werte für **Anzahl aktiver Nachrichten** und **AKTUELL** lauten jetzt **0**.
- Im unteren Abschnitt **Metriken** im Diagramm **Nachrichten** können Sie sehen, dass für die Warteschlange acht ein- und acht ausgehende Nachrichten vorhanden sind. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Aktive Nachrichten und Größe nach dem Empfang" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in der folgenden Dokumentation bzw. unter den folgenden Beispielen:

- [Azure Service Bus-Clientbibliothek für .NET: Infodatei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Beispiele bei GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET-API-Referenz](/dotnet/api/azure.messaging.servicebus?preserve-view=true&view=azure-dotnet-preview)