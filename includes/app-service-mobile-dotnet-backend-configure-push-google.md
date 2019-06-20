---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: d1dcd7895025ea608e5f6c4db5e0967817934f2a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178604"
---
Verwenden Sie das passende Verfahren für die Art Ihres Back-End-Projekts (.[NET-Back-End](#dotnet) oder [Node.js-Back-End](#nodejs)).

### <a name="dotnet"></a>.NET-Back-End-Projekt

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie anschließend auf **NuGet-Pakete verwalten**. Suchen Sie nach `Microsoft.Azure.NotificationHubs`, und klicken Sie dann auf **Installieren**. Dadurch wird die Notification Hubs-Clientbibliothek installiert.
2. Öffnen Sie im Ordner „Controller“ die Datei „TodoItemController.cs“, und fügen Sie die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using Microsoft.Azure.Mobile.Server.Config;
    using Microsoft.Azure.NotificationHubs;
    ```

3. Ersetzen Sie die `PostTodoItem`-Methode durch den folgenden Code:  

    ```csharp
    public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
    {
        TodoItem current = await InsertAsync(item);
        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;

        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Android payload
        var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }
        return CreatedAtRoute("Tables", new { id = current.Id }, current);
    }
    ```

4. Veröffentlichen Sie das Serverprojekt erneut.

### <a name="nodejs"></a>Node.js-Back-End-Projekt

1. Falls nicht bereits geschehen, [laden Sie das Schnellstartprojekt herunter](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), oder verwenden Sie den [Online-Editor im Azure-Portal](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
2. Ersetzen Sie den bestehenden Code in der Datei „todoitem.js“ durch den folgenden Code:

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the GCM payload.
    var payload = {
        "data": {
            "message": context.item.text
        }
    };

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a GCM native notification.
                context.push.gcm.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    Damit wird eine GCM-Benachrichtigung gesendet, die „item.text“ enthält, wenn ein neues Todo-Element eingefügt wird.

3. Beim Bearbeiten der Datei auf Ihrem lokalen Computer veröffentlichen Sie das Serverprojekt erneut.
