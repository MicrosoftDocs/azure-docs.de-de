---
title: Hinzufügen von Pushbenachrichtigungen zu Ihrer App für die universelle Windows-Plattform (UWP) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Mobile App Service-Apps und Azure Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre App für die universelle Windows-Plattform (UWP) zu senden.
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 7efd853e7b66933cac811625d7510139864f41f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128031"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Hinzufügen von Pushbenachrichtigungen zu Ihrer Windows-App

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Übersicht

In diesem Tutorial fügen Sie Pushbenachrichtigungen zum [Windows-Schnellstartprojekt](app-service-mobile-windows-store-dotnet-get-started.md) hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung an das Gerät gesendet wird.

Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Erweiterungspaket für Pushbenachrichtigungen hinzufügen. Weitere Informationen finden Sie unter [Verwenden des .NET-Back-End-Server-SDKs für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="configure-hub"></a>Konfigurieren eines Notification Hubs

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>Registrieren der App für Pushbenachrichtigungen

Sie müssen Ihre App an den Microsoft Store übermitteln und dann Ihr Serverprojekt für die Integration in die [Windows-Benachrichtigungsdienste (Windows Notification Services, WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) konfigurieren, um Pushbenachrichtigungen senden zu können.

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das UWP-App-Projekt, und klicken Sie dann auf **Store** > **App mit Store verknüpfen...** .

    ![Verknüpfen der App mit Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto an, geben Sie unter **App-Namen reservieren** einen Namen für Ihre App ein, und klicken Sie dann auf **Reservieren**.
3. Nachdem die App-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der neuen App aus, klicken Sie auf **Weiter** und dann auf **Zuordnen**. Dadurch werden die erforderlichen Microsoft Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.
4. Navigieren Sie zum [Portal für die Anwendungsregistrierung](https://apps.dev.microsoft.com/), und melden Sie sich mit Ihrem Microsoft-Konto an. Klicken Sie auf die Windows Store-App, die Sie im vorherigen Schritt zugeordnet haben.
5. Notieren Sie sich den auf der Registrierungsseite unter **Anwendungsgeheimnisse** angezeigten Wert sowie die **Paket-SID**. Sie benötigen diese Daten später beim Konfigurieren des Back-Ends für die mobile App.

    ![Verknüpfen der App mit Microsoft Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung. Die **Anwendungs-ID** wird zusammen mit dem geheimen Schlüssel zum Konfigurieren der Microsoft-Kontoauthentifizierung verwendet.

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) bietet außerdem Anleitungen zum Konfigurieren von UWP-Apps für Pushbenachrichtigungen.

## <a name="configure-the-backend-to-send-push-notifications"></a>Konfigurieren des Back-Ends zum Senden von Pushbenachrichtigungen

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

Verwenden Sie das passende Verfahren für Ihren Back-End-Projekttyp &mdash; entweder [.NET-Back-End](#dotnet) oder [Node.js-Back-End](#nodejs).

### <a name="dotnet"></a>.NET-Back-End-Projekt

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**. Suchen Sie „Microsoft.Azure.Mobile.NotificationHubs“, und klicken Sie anschließend auf **Installieren**. Dadurch wird die Notification Hubs-Clientbibliothek installiert.
2. Erweitern Sie **Controller**, öffnen Sie „TodoItemController.cs“, und fügen Sie die folgenden using-Anweisungen hinzu:

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

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

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

    Dieser Code weist das Benachrichtigungshub an, nach dem Einfügen eines neuen Elements eine Pushbenachrichtigung zu senden.

4. Veröffentlichen Sie das Serverprojekt erneut.

### <a name="nodejs"></a>Node.js-Back-End-Projekt
1. Falls nicht bereits geschehen, [laden Sie das Schnellstartprojekt herunter](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart), oder verwenden Sie den [Online-Editor im Azure-Portal](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
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

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
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

    Damit wird eine WNS-Popupbenachrichtigung gesendet, die „item.text“ enthält, wenn ein neues Todo-Element eingefügt wird.

3. Beim Bearbeiten der Datei auf Ihrem lokalen Computer veröffentlichen Sie das Serverprojekt erneut.

## <a id="update-app"></a>Hinzufügen von Pushbenachrichtigungen zur App
Als nächstes muss die App beim Start für Pushbenachrichtigungen registriert werden. Wenn Sie die Authentifizierung bereits aktiviert haben, achten Sie darauf, dass sich der Benutzer anmeldet, bevor die Registrierung für Pushbenachrichtigungen ausgeführt wird.

1. Öffnen Sie die Projektdatei **App.xaml.cs**, und fügen Sie die folgenden `using`-Anweisungen hinzu:

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. Fügen Sie der **App**-Klasse in der gleichen Datei die folgende **InitNotificationsAsync**-Methodendefinition hinzu:

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    Dieser Code ruft "ChannelURI" für die App aus WNS ab und registriert anschließend "ChannelURI" für Ihre mobile App Service-App.

3. Fügen Sie in der Datei **App.xaml.cs** am Anfang des **OnLaunched**-Ereignishandlers den **async**-Modifizierer der Methodendefinition hinzu, und fügen Sie den folgenden Aufruf wie im folgenden Beispiel der neuen **InitNotificationsAsync**-Methode hinzu:

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    Dadurch wird sichergestellt, dass der kurzlebige ChannelURI-Wert bei jedem Anwendungsstart registriert wird.

4. Erstellen Sie Ihr UWP-App-Projekt erneut. Ihre App kann jetzt Popupbenachrichtigungen empfangen.

## <a id="test"></a>Testen von Pushbenachrichtigungen in der App

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>Nächste Schritte

Informieren Sie sich ausführlicher über Pushbenachrichtigungen:

* [Verwenden des verwalteten Clients für Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications): Vorlagen bieten Ihnen die Flexibilität, plattformübergreifende und lokalisierte Pushbenachrichtigungen zu senden. Hier erfahren Sie, wie Sie Vorlagen registrieren.
* [Diagnostizieren von Problemen bei Pushbenachrichtigungen](../notification-hubs/notification-hubs-push-notification-fixer.md): Benachrichtigungen können aus verschiedenen Gründen verloren gehen oder nicht bei Geräten ankommen. In diesem Thema wird gezeigt, wie Sie die Grundursache von Pushbenachrichtigungsfehlern analysieren und ermitteln.

Sie können mit einem der folgenden Tutorials fortfahren:

* [Hinzufügen von Authentifizierung zu Ihrer App](app-service-mobile-windows-store-dotnet-get-started-users.md): Erfahren Sie, wie Sie Benutzer Ihrer App bei einem Identitätsanbieter authentifizieren.
* [Aktivieren der Offlinesynchronisierung für Ihre App](app-service-mobile-windows-store-dotnet-get-started-offline-data.md): Erfahren Sie, wie Sie mithilfe eines Mobile App-Back-Ends Ihrer App Offlineunterstützung hinzufügen. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (also das Anzeigen, Hinzufügen oder Ändern von Daten), auch wenn keine Netzwerkverbindung besteht.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
