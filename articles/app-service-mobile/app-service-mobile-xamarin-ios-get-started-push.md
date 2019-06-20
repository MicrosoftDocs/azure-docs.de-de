---
title: Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin iOS-App mit Azure App Service
description: Erfahren Sie, wie Azure App Service zum Senden von Pushbenachrichtigungen an Ihre Xamarin iOS-App verwendet wird.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: de24b64ecd95eec79d7508f978acda9f0ae5a8d6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097522"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Hinzufügen von Pushbenachrichtigungen zu Ihrer Xamarin iOS-App

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Übersicht

In diesem Tutorial fügen Sie Pushbenachrichtigungen zum [Xamarin.iOS-Schnellstartprojekt](app-service-mobile-xamarin-ios-get-started.md) hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung an das Gerät gesendet wird.

Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Erweiterungspaket für Pushbenachrichtigungen hinzufügen. Weitere Informationen finden Sie unter [Verwenden des .NET-Back-End-Server-SDKs für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="prerequisites"></a>Voraussetzungen

* Durchlaufen Sie das [Xamarin.iOS-Schnellstart-Tutorial](app-service-mobile-xamarin-ios-get-started.md) .
* Ein physisches iOS-Gerät. Pushbenachrichtigungen werden vom iOS-Simulator nicht unterstützt.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registrieren der App für Pushbenachrichtigungen im Apple-Entwicklerportal

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>Konfigurieren Ihrer mobilen App für das Senden von Pushbenachrichtigungen

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>Aktualisieren des Serverprojekts zum Senden von Pushbenachrichtigungen

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Konfigurieren des Xamarin.iOS-Projekts

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>Hinzufügen von Pushbenachrichtigungen zur App

1. Fügen Sie in **QSTodoService** die folgende Eigenschaft hinzu, damit **AppDelegate** den mobilen Client abrufen kann:

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. Fügen Sie am Anfang der Datei **AppDelegate.cs** die folgende `using`-Anweisung hinzu.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. Überschreiben Sie in **AppDelegate** das Ereignis **FinishedLaunching**:

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. Überschreiben Sie in derselben Datei das Ereignis `RegisteredForRemoteNotifications`. In diesem Code registrieren Sie sich für eine einfache Vorlagenbenachrichtigung, die vom Server an alle unterstützten Plattformen gesendet wird.

    Weitere Informationen zu Vorlagen mit Notification Hubs finden Sie unter [Vorlagen](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. Setzen Sie anschließend das Ereignis **DidReceivedRemoteNotification** außer Kraft:

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

Ihre App kann Pushbenachrichtigungen nun unterstützen.

## <a name="test"></a>Testen von Pushbenachrichtigungen in der App

1. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen. Starten Sie die App auf einem iOS-fähigen Gerät, und klicken Sie dann auf **OK**, um Pushbenachrichtigungen zu akzeptieren.

   > [!NOTE]
   > Sie müssen Pushbenachrichtigungen von Ihrer App ausdrücklich akzeptieren. Diese Anforderung tritt nur beim ersten Lauf der App auf.

2. Geben Sie in der App eine Aufgabe ein, und klicken Sie dann auf das Pluszeichen ( **+** ).
3. Stellen Sie sicher, dass Sie eine Benachrichtigung erhalten haben, und klicken Sie dann auf **OK** , um diese zu schließen.
4. Wiederholen Sie Schritt 2, schließen Sie die App unverzüglich, und überprüfen Sie dann, ob eine Benachrichtigung angezeigt wird.

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.
