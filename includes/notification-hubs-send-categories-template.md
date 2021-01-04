---
title: include file
description: include file
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027480"
---
In diesem Abschnitt senden Sie Neuigkeiten als Vorlagenbenachrichtigungen mit Tags über eine .NET-Konsolen-App.

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolenanwendung:
    1. Wählen Sie im Menü die Optionen **Datei** > **Neu** > **Projekt** aus.
    1. Wählen Sie unter **Neues Projekt erstellen** die Option **Console App (.NET Framework)** (Konsolen-App (.NET Framework)) für C# in der Liste der Vorlagen und dann **Weiter** aus.
    1. Geben Sie einen Namen für die App ein.
    1. Wählen Sie für **Projektmappe** die Option **Zur Projektmappe hinzufügen** aus, und wählen Sie dann **Erstellen** aus, um das Projekt zu erstellen.

1. Wählen Sie die Optionen **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus, und führen Sie im Konsolenfenster den folgenden Befehl aus:

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   Dadurch wird mithilfe des [Microsoft.Azure.NotificationHubs]-Pakets ein Verweis auf das Azure Notification Hubs-SDK hinzugefügt.

1. Öffnen Sie die Datei *Program.cs*, und fügen Sie die folgende `using`-Anweisung hinzu:

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. Fügen Sie in der `Program` -Klasse die folgende Methode hinzu, oder ersetzen Sie sie, falls sie bereits vorhanden ist:

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   Dieser Code sendet eine Vorlagenbenachrichtigung für jedes der sechs Tags im Zeichenfolgenarray. Durch die Verwendung von Tags wird sichergestellt, dass Geräte nur Benachrichtigungen für die registrierten Kategorien erhalten.

1. Ersetzen Sie im obigen Code die Platzhalter `<hub name>` und `<connection string with full access>` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* aus dem Dashboard für Ihren Notification Hub.

1. Fügen Sie in der `Main()`-Methode die folgenden Zeilen hinzu:

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. Erstellen Sie die Konsolenanwendung.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/