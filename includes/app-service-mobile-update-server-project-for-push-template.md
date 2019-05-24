---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: c664b089f316255fabc4c8dc36b291d7d63e6280
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140151"
---
In diesem Abschnitt aktualisieren Sie Code in Ihrem vorhandenen Mobile Apps-Back-End-Projekt so, dass bei jedem Hinzufügen eines neuen Elements eine Pushbenachrichtigung gesendet wird. Dieser Vorgang beruht auf dem [Vorlagenfeature](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) von Azure Notification Hubs, das plattformübergreifende Pushbenachrichtigungen ermöglicht. Die verschiedenen Clients werden mithilfe von Vorlagen für Pushbenachrichtigungen registriert. Eine universelle Pushbenachrichtigung kann dann von sämtlichen Clientplattformen empfangen werden.

Wählen Sie ein geeignetes Verfahren für die Art Ihres Back-End-Projekts ([.NET-Back-End](#dotnet) oder [Node.js-Back-End](#nodejs)).

### <a name="dotnet"></a>.NET-Back-End-Projekt

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt. Wählen Sie dann **NuGet-Pakete verwalten** aus. Suchen Sie nach `Microsoft.Azure.NotificationHubs`, und wählen Sie dann **Installieren** aus. Dadurch wird die Notification Hubs-Bibliothek zum Senden von Benachrichtigungen vom Back-End installiert.
2. Öffnen Sie im Serverprojekt **Controller** > **TodoItemController.cs**. Fügen Sie dann die folgenden using-Anweisungen hinzu:

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. Fügen Sie in der **PostTodoItem**-Methode nach dem Aufruf von **InsertAsync** den folgenden Code hinzu:  

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    Mit diesem Vorgang wird eine Vorlagenbenachrichtigung gesendet, die „item.Text“ enthält, wenn ein neues Element eingefügt wird.

4. Veröffentlichen Sie das Serverprojekt erneut.

### <a name="nodejs"></a>Node.js-Back-End-Projekt

1. Falls nicht bereits geschehen, [laden Sie das Schnellstart-Back-End-Projekt herunter](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), oder verwenden Sie den [Online-Editor im Azure-Portal](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Ersetzen Sie den bestehenden Code in „todoitem.js“ durch den folgenden Code:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    Mit diesem Vorgang wird eine Vorlagenbenachrichtigung gesendet, die „item.Text“ enthält, wenn ein neues Element eingefügt wird.

3. Beim Bearbeiten der Datei auf Ihrem lokalen Computer veröffentlichen Sie das Serverprojekt erneut.
