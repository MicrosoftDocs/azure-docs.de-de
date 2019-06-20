---
title: Benachrichtigungshubs - Pusharchitektur für Unternehmen
description: Anleitung zur Verwendung von Azure Notification Hubs (Benachrichtigungshubs) in einer Unternehmensumgebung
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 938801148b175456553865b54d59271021811401
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60873344"
---
# <a name="enterprise-push-architectural-guidance"></a>Anleitung für eine unternehmensbezogene Pusharchitektur

Unternehmen gehen mehr und mehr dazu über, mobile Anwendungen entweder für ihre Endbenutzer (extern) oder für ihre Mitarbeiter (intern) zu erstellen. Sie verfügen über Back-End-Systeme – beispielsweise Mainframecomputer oder Branchenanwendungen –, die in die Architektur der mobilen Anwendungen integriert werden müssen. Dieser Leitfaden zeigt, wie sich diese Integration am besten umsetzen lässt, und empfiehlt mögliche Lösungen für allgemeine Szenarien.

Eine häufige Anforderung besteht darin, Pushbenachrichtigung an die Benutzer über deren mobile Anwendung zu senden, wenn in den Back-End-Systemen ein interessierendes Ereignis auftritt. Ein Beispiel: Eine Bankkundin, die die Banking-App ihrer Bank auf ihrem iPhone installiert hat, möchte benachrichtigt werden, wenn ihr Konto mit einem Betrag belastet wird, der über eine bestimmte Summe hinausgeht. Ein anderes Beispiel ist ein Intranetszenario, in dem ein Mitarbeiter aus der Finanzabteilung, der auf seinem Windows Phone über eine App zur Budgetgenehmigung verfügt, benachrichtigt werden möchte, wenn er eine Genehmigungsanforderung erhält.

Die Bankkonto- oder Genehmigungsverarbeitung erfolgt wahrscheinlich in einem Back-End-System, das ein Pushbenachrichtigung an den Benutzer auslösen muss. Möglicherweise gibt es mehrere solcher Back-End-Systeme, die alle die gleiche Logik für Pushbenachrichtigungen verwenden müssen, wenn ein Ereignis eine Benachrichtigung auslöst. Die Komplexität besteht hier darin, dass mehrere Back-End-Systeme in ein einziges Pushsystem integriert werden müssen, in dem die Endbenutzer möglicherweise unterschiedliche Benachrichtigungen abonniert haben. Möglicherweise gibt es sogar mehrere mobile Anwendungen. Ein Beispiel hierfür sind mobile Intranetumgebungen, in denen eine einzige mobile Anwendung Benachrichtigungen von mehreren solcher Back-End-Systeme empfangen soll. Die Back-End-Systeme haben keinerlei Kenntnis der Pushsemantik oder Pushtechnologie, also besteht eine häufige herkömmliche Lösung darin, eine Komponente bereitzustellen, die die Back-End-Systeme ständig nach Ereignissen von Interesse abfragt und dafür zuständig ist, die Pushnachrichten an die Clients zu senden.

Eine bessere Lösung ist das Modell „Azure Service Bus – Thema/Abonnement“, das die Komplexität reduziert und gleichzeitig die Lösung skalierbar gestaltet.

Die allgemeine Architektur der Lösung (verallgemeinert mit mehreren mobilen Apps, jedoch gleichermaßen anwendbar, wenn es nur eine mobile App gibt) sieht wie folgt aus.

## <a name="architecture"></a>Architecture

![][1]

Kernstück dieses Architekturdiagramms ist der Dienst Azure Service Bus, der ein auf Themen und Abonnements basierendes Programmiermodell bereitstellt (mehr dazu erfahren Sie unter [WindowsAzure.ServiceBus]). Der Empfänger, bei dem es sich in diesem Fall um das mobile Back-End handelt (üblicherweise [Azure Mobile Service] zum Auslösen einer Pushbenachrichtigung an die mobilen Apps), erhält Nachrichten nicht direkt von den Back-End-Systemen, sondern von einer dazwischen liegenden Abstraktionsschicht. Diese wird von [Azure Service Bus] bereitgestellt und ermöglicht es den mobilen Back-End-Systemen, Nachrichten von einem oder mehreren Back-End-Systemen zu empfangen. Für jedes der Back-End-Systeme, z.B. für Abrechnung, Personalwesen oder Finanzen, muss ein Service Bus-Thema erstellt werden. Bei diesen Systemen handelt es sich im Grunde um „Themen von Interesse“, die bewirken, dass Nachrichten als Pushbenachrichtigungen gesendet werden. Die Back-End-Systeme senden Nachrichten an diese Themen. Ein Mobil-Back-End kann eines oder mehrere solcher Themen durch Erstellen eines Service Bus-Abonnements abonnieren. Dadurch erhält das mobile Back-End die Berechtigung, eine Benachrichtigung vom entsprechenden Back-End-System zu empfangen. Das Mobil-Back-End lauscht weiterhin auf Nachrichten in seinen Abonnements, und sobald eine Nachricht eingetroffen ist, sendet es diese als Benachrichtigung an seinen Benachrichtigungshub. Notification Hubs senden die Nachricht dann schließlich an die mobile App. Hier finden Sie die Liste der wichtigsten Komponenten:

1. Back-End-System (Branchen- oder Legacysystem)
   * Erstellt Service Bus-Themen
   * Sendet Nachrichten
1. Mobil-Back-End
   * Erstellt dienstbezogene Abonnements
   * Empfängt Nachrichten (von Back-End-System)
   * Sendet Benachrichtigungen an Clients (über Azure-Benachrichtigungshub)
1. Mobile Anwendung
   * Empfängt Benachrichtigungen und zeigt diese an

### <a name="benefits"></a>Vorteile

1. Die Entkopplung von Empfänger (mobile App/mobiler Dienst über Benachrichtigunghub) und Sender (Back-End-Systeme) ermöglicht es, zusätzliche Back-End-Systeme bei minimalen Änderungen zu integrieren.
1. Hiermit lassen sich auch Szenarios umsetzen, in denen mehrere mobile Apps in der Lage sind, Ereignisse von mehreren Back-End-Systemen zu empfangen.  

## <a name="sample"></a>Beispiel

### <a name="prerequisites"></a>Voraussetzungen

Arbeiten Sie die folgenden Tutorials durch, um sich mit den Konzepten sowie den allgemeinen Erstellungs- und Konfigurationsschritten vertraut zu machen:

1. [WindowsAzure.ServiceBus]: Dieses Tutorial bietet Informationen zum Verwenden von Service Bus-Themen und -Abonnements, zum Erstellen eines Namespace, der Themen und Abonnements enthält, und zum Senden und Empfangen von Nachrichten an und von Themen und Abonnements.
2. [Erste Schritte mit Notification Hubs]: Dieses Tutorial erläutert, wie Sie eine Windows Store-App einrichten und wie Sie Notification Hubs verwenden, um sich für Benachrichtigungen zu registrieren und diese zu empfangen.

### <a name="sample-code"></a>Beispielcode

Der vollständige Beispielcode ist unter [Notification Hubs Samples] verfügbar. Der Code ist in drei Komponenten aufgeteilt:

1. **EnterprisePushBackendSystem**

    a. Dieses Projekt verwendet das NuGet-Paket **WindowsAzure.ServiceBus** und basiert auf [WindowsAzure.ServiceBus].

    b. Bei der Anwendung handelt es sich um eine einfache C#-Konsolen-App zum Simulieren eines Branchensystems, mit dem das Senden einer Nachricht an eine mobile App veranlasst wird.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic` wird zum Erstellen des Service Bus-Themas verwendet.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage` wird verwendet, um die Nachrichten an dieses Service Bus-Thema zu senden. Dieser Code sendet für dieses Beispiel einfach regelmäßig einen Satz von zufälligen Nachrichten an das Thema. Normalerweise ist ein Back-End-System vorhanden, das die Nachrichten sendet, wenn ein Ereignis auftritt.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. Dieses Projekt verwendet die NuGet-Pakete *WindowsAzure.ServiceBus* und **Microsoft.Web.WebJobs.Publish** und basiert auf [WindowsAzure.ServiceBus].

    b. Die folgende Konsolen-App ist als [Azure WebJob] implementiert, weil sie kontinuierlich ausgeführt werden muss, um auf Nachrichten aus den Branchen- bzw. Back-End-Systemen zu lauschen. Diese Anwendung ist Teil Ihres mobilen Back-Ends.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription` wird zum Erstellen eines Service Bus-Abonnements für das Thema verwendet, an das das Back-End-System Nachrichten sendet. Je nach Geschäftsszenario erstellt diese Komponente mindestens ein Abonnement für die jeweiligen Themen (z.B. empfangen einige Themen Nachrichten aus dem Personalsystem, einige aus dem Finanzsystem usw.).

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification` wird verwendet, um die Nachricht über das dazugehörige Abonnement aus dem Thema zu lesen und, wenn der Lesevorgang erfolgreich war, eine Benachrichtigung zu erstellen (im Beispielszenario eine native Windows-Popupbenachrichtigung), die über Azure Notification Hubs an die mobile Anwendung gesendet werden soll.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Um diese App als **WebJob** zu veröffentlichen, klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Als WebJob veröffentlichen** aus.

    ![][2]

    f. Wählen Sie Ihr Veröffentlichungsprofil aus, und erstellen Sie eine neue Azure-Website, sofern noch keine vorhanden ist, die diesen WebJob hostet. Sobald die Website vorhanden ist, klicken Sie auf **Veröffentlichen**.

    ![][3]

    g. Konfigurieren Sie den WebJob mit „Dauerhaft ausführen“, sodass in etwa Folgendes angezeigt wird, wenn Sie sich beim [Azure-Portal] angemeldet haben:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Dies ist eine Windows Store-Anwendung, die Popupbenachrichtigungen von dem WebJob empfängt, der als Teil Ihres mobilen Back-Ends ausgeführt wird, und diese Benachrichtigungen anzeigt. Dieser Code basiert auf [Erste Schritte mit Notification Hubs].  

    b. Stellen Sie sicher, dass Ihre Anwendung so konfiguriert ist, dass sie Popupbenachrichtigungen empfangen kann.

    c. Stellen Sie sicher, dass der folgende Notification Hubs-Registrierungscode beim App-Start aufgerufen wird (nachdem `HubName` und `DefaultListenSharedAccessSignature` ersetzt wurden):

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Ausführen des Beispiels

1. Stellen Sie sicher, dass Ihr WebJob erfolgreich ausgeführt wird und für die kontinuierliche Ausführung geplant ist.
2. Führen Sie die **EnterprisePushMobileApp** aus, mit der die Windows Store-App gestartet wird.
3. Führen Sie die Konsolenanwendung **EnterprisePushBackendSystem** aus, die das Branchen-Back-End simuliert und damit beginnt, Nachrichten zu senden. Es sollten Popupbenachrichtigungen ähnlich der folgenden angezeigt werden:

    ![][5]

4. Die Nachrichten wurden ursprünglich an Service Bus-Themen gesendet, die von Service Bus-Abonnements in Ihrem WebJob überwacht wurden. Sobald eine Nachricht empfangen wurde, wurde eine Benachrichtigung erstellt und an die mobile App gesendet. Sie können die WebJob-Protokolle durchsuchen, um die Verarbeitung zu bestätigen. Navigieren Sie dazu im [Azure-Portal] für Ihren WebJob zum Link „Protokolle“:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Notification Hubs Samples]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[WindowsAzure.ServiceBus]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Erste Schritte mit Notification Hubs]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure-Portal]: https://portal.azure.com/
