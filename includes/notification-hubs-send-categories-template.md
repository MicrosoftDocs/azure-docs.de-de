---
title: include file
description: include file
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/30/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f0ff729084d194ff2e05e89eadc45782f775b1c5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178336"
---
In diesem Abschnitt senden Sie Neuigkeiten als Vorlagenbenachrichtigungen mit Tags über eine .NET-Konsolen-App. 

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolenanwendung: a. Wählen Sie im Menü die Optionen **Datei** > **Neu** > **Projekt** aus.
    b. Erweitern Sie **Visual C#** , und wählen Sie **Windows-Desktop** aus. 
    c. Wählen Sie in der Liste der Vorlagen **Konsolen-App (.NET Framework)** aus. 
    d. Geben Sie einen **Namen** für die App ein. 
    e. Wählen Sie einen **Ordner** für die App aus.
    f. Wählen Sie **OK** aus, um das Projekt zu erstellen. 
2. Wählen Sie im Hauptmenü von Visual Studio die Optionen **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus, und geben Sie im Konsolenfenster die folgende Zeichenfolge ein:
   
    ```
    Install-Package Microsoft.Azure.NotificationHubs
    ```
   
3. Drücken Sie die **EINGABETASTE**.  
    Dadurch wird mithilfe des [Microsoft.Azure.NotificationHubs-NuGet-Pakets] ein Verweis auf das Azure Notification Hubs-SDK hinzugefügt.

4. Öffnen Sie die Datei „Program.cs“, und fügen Sie die folgende `using`-Anweisung hinzu:
   
    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

5. Fügen Sie in der `Program` -Klasse die folgende Methode hinzu, oder ersetzen Sie sie, falls sie bereits vorhanden ist:
   
    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```   
   
    Dieser Code sendet eine Vorlagenbenachrichtigung für jedes der sechs Tags im Zeichenfolgenarray. Durch die Verwendung von Tags wird sichergestellt, dass Geräte nur Benachrichtigungen für die registrierten Kategorien erhalten.

5. Ersetzen Sie im obigen Code die Platzhalter `<hub name>` und `<connection string with full access>` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* aus dem Dashboard für Ihren Notification Hub.

6. Fügen Sie in der **Main**-Methode die folgenden Zeilen hinzu:
   
    ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

7. Erstellen Sie die Konsolenanwendung.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs-NuGet-Pakets]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
