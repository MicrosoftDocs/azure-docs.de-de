---
title: Erste Schritte mit Azure Service Bus-Warteschlangen | Microsoft-Dokumentation
description: In diesem Tutorial erstellen Sie .NET Core-Konsolenanwendungen, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen.
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.date: 09/01/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c862fda5ee2bb5b7418fda1b4e45cd06742cf95
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95819040"
---
# <a name="get-started-with-service-bus-queues"></a>Erste Schritte mit Service Bus-Warteschlangen
In diesem Tutorial erstellen Sie .NET Core-Konsolenanwendungen, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen.

> [!WARNING]
> In dieser Schnellstartanleitung wird das alte Microsoft.Azure.ServiceBus-Paket verwendet. Eine Schnellstartanleitung, in der das neueste Azure.Messaging.ServiceBus-Paket verwendet wird, finden Sie unter [Senden und Empfangen von Ereignissen mit einem Azure.Messaging.ServiceBus-Paket](service-bus-dotnet-get-started-with-queues.md). 

## <a name="prerequisites"></a>Voraussetzungen

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows) ab Version 2.0
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen.

  - Lesen Sie die kurze Übersicht über Service Bus-Warteschlangen.
  - Erstellen Sie einen Service Bus-Namespace.
  - Abrufen der Verbindungszeichenfolge.
  - Erstellen einer Service Bus-Warteschlange

## <a name="send-messages"></a>Senden von Nachrichten

Erstellen Sie mithilfe von Visual Studio eine C#-Konsolenanwendung, um Nachrichten an die Warteschlange senden zu können.

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

Starten Sie Visual Studio, und erstellen Sie ein neues Projekt vom Typ **Konsolen-App (.NET Core)** für C#. In diesem Beispiel wird die Anwendung *CoreSenderApp* verwendet.

### <a name="add-the-service-bus-nuget-package"></a>Hinzufügen des NuGet-Pakets "Service Bus"

1. Klicken Sie mit der rechten Maustaste auf das neu erstellte Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.
1. Wählen Sie **Durchsuchen** aus. Suchen Sie nach dem Element **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**, und wählen Sie es aus.
1. Klicken Sie auf **Installieren**, um die Installation abzuschließen, und schließen Sie danach den NuGet-Package-Manager.

    ![Auswählen eines NuGet-Pakets][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Schreiben von Code für das Senden von Nachrichten an die Warteschlange

1. Fügen Sie in *Program.cs* am Beginn der Namespacedefinition die folgenden `using`-Anweisungen vor der Klassendeklaration hinzu:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Deklarieren Sie in der Klasse `Program` die folgenden Variablen:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Geben Sie die Verbindungszeichenfolge für den Namespace als `ServiceBusConnectionString`-Variable ein. Geben Sie den Warteschlangennamen ein.

1. Ersetzen Sie die `Main()`-Methode durch die folgende **asynchrone** `Main`-Methode. Sie ruft die `SendMessagesAsync()`-Methode auf, die Sie im nächsten Schritt hinzufügen, um Nachrichten an die Warteschlange zu senden. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```
1. Fügen Sie direkt nach der `MainAsync()`-Methode die folgende `SendMessagesAsync()`-Methode hinzu. Diese Methode dient zum Senden der durch `numberOfMessagesToSend` angegebenen Anzahl von Nachrichten. Die Anzahl ist aktuell auf 10 festgelegt:

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```

Die Datei *Program.cs* sollte nun wie folgt aussehen:

```csharp
namespace CoreSenderApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        public static async Task Main(string[] args)
        {    
            const int numberOfMessages = 10;
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);
    
            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after sending all the messages.");
            Console.WriteLine("======================================================");
    
            // Send messages.
            await SendMessagesAsync(numberOfMessages);
    
            Console.ReadKey();
    
            await queueClient.CloseAsync();
        }

        static async Task SendMessagesAsync(int numberOfMessagesToSend)
        {
            try
            {
                for (var i = 0; i < numberOfMessagesToSend; i++)
                {
                    // Create a new message to send to the queue
                    string messageBody = $"Message {i}";
                    var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                    // Write the body of the message to the console
                    Console.WriteLine($"Sending message: {messageBody}");

                    // Send the message to the queue
                    await queueClient.SendAsync(message);
                }
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
            }
        }
    }
}
```

Führen Sie das Programm aus, und überprüfen Sie das Azure-Portal.

Wählen Sie den Namen der Warteschlange im Namespace-Fenster **Übersicht** aus, um die Warteschlange **Essentials** anzuzeigen.

![Empfangene Nachrichten mit Anzahl und Größe][queue-message]

Unter **Anzahl aktiver Nachrichten** ist für die Warteschlange nun der Wert **10** angegeben. Jedes Mal, wenn Sie diese Absenderanwendung ausführen, ohne Nachrichten abzurufen, erhöht sich dieser Wert um 10.

Bei der aktuellen Warteschlangengröße erhöht sich der Wert **AKTUELL** unter **Zusammenfassung** jeweils, wenn der Warteschlange durch die App Nachrichten hinzugefügt werden.

Im nächsten Abschnitt wird beschrieben, wie diese Nachrichten abgerufen werden.

## <a name="receive-messages"></a>Empfangen von Nachrichten

Um die gesendeten Nachrichten zu empfangen, erstellen Sie eine weitere Anwendung vom Typ **Konsolen-App (.NET Core)**. Installieren Sie das **Microsoft.Azure.ServiceBus**-NuGet-Paket wie bei der Absenderanwendung.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Schreiben von Code für das Empfangen von Nachrichten aus der Warteschlange

1. Fügen Sie in *Program.cs* am Beginn der Namespacedefinition die folgenden `using`-Anweisungen vor der Klassendeklaration hinzu:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Deklarieren Sie in der Klasse `Program` die folgenden Variablen:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Geben Sie die Verbindungszeichenfolge für den Namespace als `ServiceBusConnectionString`-Variable ein. Geben Sie den Warteschlangennamen ein.

1. Ersetzen Sie die `Main()`-Methode durch folgenden Code:

    ```csharp
    static void Main(string[] args)
    {
        MainAsync().GetAwaiter().GetResult();
    }

    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register QueueClient's MessageHandler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

1. Fügen Sie direkt nach der `MainAsync()`-Methode die folgende Methode hinzu, die den Nachrichtenhandler registriert und die von der Absenderanwendung gesendeten Nachrichten empfängt:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

1. Fügen Sie direkt nach der vorherigen Methode die folgende `ProcessMessagesAsync()`-Methode für die Verarbeitung der empfangenen Nachrichten hinzu:

    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

1. Fügen Sie abschließend die folgende Methode zur Behandlung möglicherweise auftretender Ausnahmen hinzu:

    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }
    ```

Die Datei *Program.cs* sollte nun wie folgt aussehen:

```csharp
namespace CoreReceiverApp
{
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;

    class Program
    {
        // Connection String for the namespace can be obtained from the Azure portal under the 
        // 'Shared Access policies' section.
        const string ServiceBusConnectionString = "<your_connection_string>";
        const string QueueName = "<your_queue_name>";
        static IQueueClient queueClient;

        static void Main(string[] args)
        {
            MainAsync().GetAwaiter().GetResult();
        }

        static async Task MainAsync()
        {
            queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

            Console.WriteLine("======================================================");
            Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
            Console.WriteLine("======================================================");

            // Register QueueClient's MessageHandler and receive messages in a loop
            RegisterOnMessageHandlerAndReceiveMessages();
 
            Console.ReadKey();

            await queueClient.CloseAsync();
        }

        static void RegisterOnMessageHandlerAndReceiveMessages()
        {
            // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
            var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
            {
                // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                // Set it according to how many messages the application wants to process in parallel.
                MaxConcurrentCalls = 1,

                // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                AutoComplete = false
            };

            // Register the function that will process messages
            queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
        }

        static async Task ProcessMessagesAsync(Message message, CancellationToken token)
        {
            // Process the message
            Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

            // Complete the message so that it is not received again.
            // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
            await queueClient.CompleteAsync(message.SystemProperties.LockToken);

            // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
            // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
            // to avoid unnecessary exceptions.
        }

        static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
        {
            Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
            var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
            Console.WriteLine("Exception context for troubleshooting:");
            Console.WriteLine($"- Endpoint: {context.Endpoint}");
            Console.WriteLine($"- Entity Path: {context.EntityPath}");
            Console.WriteLine($"- Executing Action: {context.Action}");
            return Task.CompletedTask;
        }
    }
}
```

Führen Sie das Programm aus, und überprüfen Sie die Angaben im Portal erneut. Die Werte für **Anzahl aktiver Nachrichten** und **AKTUELL** lauten jetzt **0**.

![Warteschlange nach dem Empfang von Nachrichten][queue-message-receive]

Herzlichen Glückwunsch! Sie haben eine Warteschlange erstellt, eine Reihe von Nachrichten an diese Warteschlange gesendet und diese Nachrichten aus der Warteschlange empfangen.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen und Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich das [GitHub-Repository mit Beispielen](https://github.com/Azure/azure-service-bus/tree/master/samples) an, mit denen einige erweiterte Features des Service Bus Messaging veranschaulicht werden.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues-legacy/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues-legacy/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues-legacy/queue-message-receive-in-essentials.png

