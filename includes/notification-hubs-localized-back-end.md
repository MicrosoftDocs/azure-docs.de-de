---
title: include file
description: include file
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c15d695e072e72c6e7be6dcf49f3ea049a9b70b7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178682"
---
Wenn Sie Vorlagenbenachrichtigungen senden, müssen Sie nur einen Satz von Eigenschaften bereitstellen. In diesem Szenario enthält der Satz von Eigenschaften die lokalisierte Version der aktuellen Nachrichten.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>Senden von Benachrichtigungen mit einer C#-Konsolen-App

Dieser Abschnitt zeigt, wie Benachrichtigungen mit einer Konsolen-App gesendet werden. Der Code überträgt Benachrichtigungen an Windows Store- und iOS-Geräte. Ändern Sie die `SendTemplateNotificationAsync`-Methode in der zuvor erstellten Konsolen-App mit dem folgenden Code:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

Die SendTemplateNotificationAsync-Methode übermittelt den lokalisierten Teil der Nachrichten an **alle** Ihre Geräte, unabhängig von der Plattform. Ihr Benachrichtigungshub erstellt und übermittelt die richtige native Nutzlast für alle Geräte, die einen bestimmten Tag abonniert haben.

### <a name="sending-notification-with-mobile-services"></a>Senden von Benachrichtigungen mit Mobile Services

Verwenden Sie im Mobile Service-Scheduler das folgende Skript:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
