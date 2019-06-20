---
title: Senden von Pushbenachrichtigungen an Xamarin.iOS-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an eine Xamarin.iOS-Anwendung senden.
services: notification-hubs
keywords: iOS-Pushbenachrichtigungen,Pushnachrichten,Pushbenachrichtigungen,Pushnachricht
documentationcenter: xamarin
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/23/2019
ms.author: jowargo
ms.openlocfilehash: cd6d22e7c689bce5c325863b914c5ee8abcbf40a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240774"
---
# <a name="tutorial-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an Xamarin.iOS-Apps mit Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an eine iOS-App senden. Sie erstellen eine leere Xamarin.iOS-App, die Pushbenachrichtigungen mithilfe des [Apple-Pushbenachrichtigungsdiensts (Apple Push Notification Service, APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) empfängt.

Sobald Sie dieses Tutorial abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen. Der komplette Code ist in der [NotificationHubs-Beispiel-App][GitHub] verfügbar.

In diesem Tutorial erstellen/aktualisieren Sie Code, um die folgenden Aufgaben auszuführen:

> [!div class="checklist"]
> * Erstellen der Zertifikatsignierungsanforderungs-Datei
> * Registrieren der App für Pushbenachrichtigungen
> * Erstellen eines Bereitstellungsprofils für die App
> * Konfigurieren Ihres Notification Hubs für iOS-Pushbenachrichtigungen
> * Senden von Test-Pushbenachrichtigungen

## <a name="prerequisites"></a>Voraussetzungen

* **Azure-Abonnement**. Falls Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.
* Neueste Version von [Xcode][Install Xcode]
* Mit iOS 10 (oder einer neueren Version) kompatibles Gerät
* [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/)
* [Visual Studio für Mac]
  
  > [!NOTE]
  > Die Beispielanwendung muss aufgrund von Konfigurationsanforderungen für iOS-Pushbenachrichtigungen auf einem physischem iOS-Gerät (iPhone oder iPad) bereitgestellt und getestet werden, anstatt im Simulator.

Die Absolvierung dieses Tutorials ist eine Voraussetzung für alle anderen Notification Hubs-Tutorials für Xamarin.iOS-Apps.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurieren Ihres Notification Hubs für iOS-Pushbenachrichtigungen

Dieser Abschnitt führt Sie durch die Schritte zum Erstellen eines neuen Notification Hubs sowie zum Konfigurieren der Authentifizierung mit APNs unter Verwendung des zuvor erstellten Pushzertifikats vom Typ **.p12**. Wenn Sie einen bereits erstellten Notification Hub verwenden möchten, können Sie zu Schritt 5 springen.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-ios-settings-for-the-notification-hub"></a>Konfigurieren von iOS-Einstellungen für den Notification Hub

1. Wählen Sie **Apple (APNS)** in der Gruppe **BENACHRICHTIGUNGSEINSTELLUNGEN** aus.
2. Wählen Sie **Zertifikat** aus, klicken Sie auf das **Dateisymbol**, und wählen Sie die **P12**-Datei aus, die Sie zuvor exportiert haben.
3. Geben Sie das **Kennwort** für das Zertifikat ein.
4. Wählen Sie den Modus **Sandbox** aus. Verwenden Sie den Modus **Produktion** nur dann, wenn Sie Pushbenachrichtigungen an Benutzer senden möchten, die Ihre App im Store erworben haben.

    ![Konfigurieren von APNs im Azure-Portal][6]

    ![Konfigurieren der APNs-Zertifizierung im Azure-Portal][7]

Ihr Notification Hub ist jetzt für die Verwendung mit APNs konfiguriert, und Sie verfügen über die Verbindungszeichenfolge, um die App zu registrieren und Pushbenachrichtigungen zu senden.

## <a name="connect-your-app-to-the-notification-hub"></a>Verbinden Ihrer App mit dem Notification Hub

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

1. Erstellen Sie in Visual Studio ein neues iOS-Projekt, wählen Sie die Vorlage **Einzelansicht-App** aus, und klicken Sie auf **Weiter**.

     ![Visual Studio: Auswählen des Anwendungstyps][31]

2. Geben Sie Ihren App-Namen und die Organisations-ID ein, und klicken Sie anschließend auf **Weiter** > **Erstellen**

3. Doppelklicken Sie in der Projektmappenansicht auf *Info.plist*, und vergewissern Sie sich unter **Identität**, dass Ihre Paket-ID der ID entspricht, die beim Erstellen Ihres Bereitstellungsprofils verwendet wurde. Vergewissern Sie sich unter **Signierung**, dass unter **Team** Ihr Entwicklerkonto ausgewählt ist. Vergewissern Sie sich außerdem, dass das Kontrollkästchen für die automatische Signierungsverwaltung aktiviert ist und dass Ihr Signaturzertifikat und Ihr Bereitstellungsprofil automatisch ausgewählt wurden.

    ![Visual Studio: iOS-App-Konfiguration][32]

4. Doppelklicken Sie in der Projektmappenansicht auf `Entitlements.plist`, und vergewissern Sie sich, dass **Pushbenachrichtigungen aktivieren** aktiviert ist.

    ![Visual Studio: Entitlements-Konfiguration für iOS][33]

5. Fügen Sie das Azure-Messaging-Paket hinzu. Klicken Sie in der Projektmappenansicht mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **NuGet-Pakete hinzufügen** aus. Suchen Sie nach **Xamarin.Azure.NotificationHubs.iOS**, und fügen Sie das Paket dem Projekt hinzu.

6. Fügen Sie Ihrer Klasse eine neue Datei hinzu, und nennen Sie sie `Constants.cs`. Fügen Sie anschließend die folgenden Variablen hinzu, und ersetzen Sie die Platzhalter für Zeichenfolgenliterale durch Ihren Hubnamen (`hubname`) und den zuvor notierten Wert für `DefaultListenSharedAccessSignature`.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. Fügen Sie in `AppDelegate.cs` die folgende using-Anweisung hinzu:

    ```csharp
    using WindowsAzure.Messaging;
    using UserNotifications
    ```

8. Deklarieren Sie eine Instanz von `SBNotificationHub`:

    ```csharp
    private SBNotificationHub Hub { get; set; }
    ```

9. Aktualisieren Sie in `AppDelegate.cs` das Element `FinishedLaunching()` so, dass es dem folgenden Code entspricht:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Sound | UNAuthorizationOptions.Sound,
                                                                    (granted, error) =>
            {
                if (granted)
                    InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications);
            });
        } else if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                    new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        } else {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
        }

        return true;
    }
    ```

10. Überschreiben Sie in `AppDelegate.cs` die Methode `RegisteredForRemoteNotifications()`:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);

        Hub.UnregisterAllAsync (deviceToken, (error) => {
            if (error != null)
            {
                System.Diagnostics.Debug.WriteLine("Error calling Unregister: {0}", error.ToString());
                return;
            }

            NSSet tags = null; // create tags if you want
            Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                if (errorCallback != null)
                    System.Diagnostics.Debug.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
            });
        });
    }
    ```

11. Überschreiben Sie in `AppDelegate.cs` die Methode `ReceivedRemoteNotification()`:

    ```csharp
    public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
    {
        ProcessNotification(userInfo, false);
    }
    ```

12. Erstellen Sie in `AppDelegate.cs` die Methode `ProcessNotification()`:

    ```csharp
    void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
    {
        // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
        if (null != options && options.ContainsKey(new NSString("aps")))
        {
            //Get the aps dictionary
            NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;

            //Extract the alert text
            // NOTE: If you're using the simple alert by just specifying
            // "  aps:{alert:"alert msg here"}  ", this will work fine.
            // But if you're using a complex alert with Localization keys, etc.,
            // your "alert" object from the aps dictionary will be another NSDictionary.
            // Basically the JSON gets dumped right into a NSDictionary,
            // so keep that in mind.
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //If this came from the ReceivedRemoteNotification while the app was running,
            // we of course need to manually process things like the sound, badge, and alert.
            if (!fromFinishedLaunching)
            {
                //Manually show an alert
                if (!string.IsNullOrEmpty(alert))
                {
                    UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                    avAlert.Show();
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Sie können `FailedToRegisterForRemoteNotifications()` überschreiben, um Situationen wie beispielsweise eine fehlende Netzwerkverbindung zu behandeln. Dies ist besonders wichtig, wenn Benutzer Ihre Anwendung ggf. im Offlinemodus starten (beispielsweise im Flugzeug) und Sie spezifische Pushnachrichtenszenarien für Ihre App behandeln möchten.

13. Führen Sie die App auf Ihrem Gerät aus.

## <a name="send-test-push-notifications"></a>Senden von Test-Pushbenachrichtigungen

Mit der Option *Testsendevorgang* im [Azure-Portal] können Sie den Empfang von Benachrichtigungen in Ihrer App testen. Diese Option sendet zu Testzwecken eine Pushbenachrichtigung an Ihr Gerät.

![Azure-Portal – Testsendung][30]

Pushbenachrichtigungen werden normalerweise mithilfe einer kompatiblen Bibliothek über einen Back-End-Dienst wie Mobile Apps oder ASP.NET gesendet. Falls für Ihr Back-End keine Bibliothek verfügbar ist, können Sie Benachrichtigungsmeldungen auch direkt über die REST-API senden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Broadcastbenachrichtigungen an alle iOS-Geräte gesendet, die beim Back-End registriert sind. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte iOS-Geräte senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Geräte](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio für Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure-Portal]: https://portal.azure.com