---
title: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an iOS-Anwendungen senden.
services: notification-hubs
documentationcenter: ios
keywords: Pushbenachrichtigung,Pushbenachrichtigungen,iOS-Pushbenachrichtigungen
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/21/2019
ms.author: jowargo
ms.openlocfilehash: c5793d2388ddd7bb59d68f8f7fd7af773179ed41
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988202"
---
# <a name="tutorial-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)


In diesem Tutorial verwenden Sie Azure Notification Hubs, um Pushbenachrichtigungen an eine iOS-Anwendung zu senden. Sie erstellen eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts ([Apple Push Notification Service, APNs](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)) empfängt.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Generieren der Datei für die Anforderung der Zertifikatsignierung
> * Anfordern von Pushbenachrichtigungen für Ihre App
> * Erstellen eines Bereitstellungsprofils für die App
> * Konfigurieren Ihres Notification Hubs für iOS-Pushbenachrichtigungen
> * Verbinden der iOS-App mit Notification Hubs
> * Senden von Test-Pushbenachrichtigungen
> * Überprüfen, ob die App Benachrichtigungen empfängt

Den vollständigen Code für dieses Tutorial finden Sie [auf GitHub](https://github.com/Azure/azure-notificationhubs-ios/tree/master/Samples). 

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie kein Konto besitzen, können Sie in nur wenigen Minuten ein [kostenloses Azure-Konto](https://azure.microsoft.com/free) erstellen.
* [Microsoft Azure-Messaging-Framework]
* Neueste Version von [Xcode]
* Für iOS 10 (oder eine neuere Version) geeignetes Gerät
* [Apple-Entwicklerprogramm-Mitgliedschaft](https://developer.apple.com/programs/)
  
  > [!NOTE]
  > Pushbenachrichtigungen müssen aufgrund von Konfigurationsanforderungen auf einem physischen iOS-Gerät (iPhone oder iPad) anstatt im iOS-Simulator bereitgestellt und getestet werden.
  
Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hubs-Lernprogramme für iOS-Apps.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Verbinden der iOS-App mit Notification Hubs

1. Erstellen Sie in Xcode ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** aus.

    ![Xcode – Single View Application][8]

2. Achten Sie beim Festlegen der Optionen für das neue Projekt darauf, dass Sie den gleichen **Produktnamen** und die gleiche **Organisations-ID** verwenden wie beim Festlegen der Paket-ID im Apple-Entwicklerportal.

    ![Xcode – Projektoptionen][11]

3. Klicken Sie in der Projektnavigation auf Ihren Projektnamen, klicken Sie auf die Registerkarte **Allgemein**, und suchen Sie nach **Signierung**. Wählen Sie das passende Team für Ihr Apple-Entwicklerkonto aus. Xcode sollte auf der Grundlage Ihrer Paket-ID automatisch das Bereitstellungsprofil abrufen, das Sie zuvor erstellt haben.

    Wenn das neue in Xcode erstellte Bereitstellungsprofil nicht angezeigt wird, versuchen Sie, die Profile für Ihre Signaturidentität zu aktualisieren. Klicken Sie in der Menüleiste auf **Xcode**, dann auf **Voreinstellungen**, auf die Registerkarte **Konto**, auf die Schaltfläche **Details anzeigen**, auf die Signaturidentität und anschließend rechts unten auf die Schaltfläche „Aktualisieren“.

    ![Xcode – Bereitstellungsprofil][9]

4. Vergewissern Sie sich auf der Registerkarte **Funktionen**, dass Pushbenachrichtigungen aktiviert sind.

    ![Xcode – Pushfunktionen][12]

5. Fügen Sie die Module für das Azure Notification Hubs SDK hinzu.

   Sie können das Azure Notification Hubs SDK in Ihre App integrieren. Verwenden Sie dazu [CocoaPods](https://cocoapods.org), oder fügen Sie die Binärdateien Ihrem Projekt manuell hinzu.

   - Integration über CocoaPods

     Fügen Sie die folgenden Abhängigkeiten zu `podfile` hinzu, um das Azure Notification Hubs SDK in Ihre App zu integrieren.

     ```ruby
     pod 'AzureNotificationHubs-iOS'
     ```

     Führen Sie `pod install` aus, um Ihren neu definierten Pod zu installieren, und öffnen Sie `.xcworkspace`.

     > [!NOTE]
     > Wenn bei der Ausführung von `pod install` ein Fehler wie ```[!] Unable to find a specification for `AzureNotificationHubs-iOS` ``` angezeigt wird, führen Sie `pod repo update` aus, um die aktuellen Pods aus dem Cocoapods-Repository abzurufen, und führen Sie anschließend `pod install` aus.

   - Integration über Carthage

     Fügen Sie die folgenden Abhängigkeiten zu `Cartfile` hinzu, um das Azure Notification Hubs SDK in Ihre App zu integrieren.

     ```ruby
     github "Azure/azure-notificationhubs-ios"
     ```

     Aktualisieren und erstellen Sie als Nächstes die Abhängigkeiten:

     ```shell
     $ carthage update
     ```

     Weitere Informationen zur Verwendung von Carthage finden Sie im [GitHub-Repository für Carthage](https://github.com/Carthage/Carthage).

   - Integration durch Kopieren der Binärdateien in Ihr Projekt

     1. Laden Sie das als ZIP-Datei bereitgestellte Framework für das [Azure Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs-ios/releases) herunter, und entzippen Sie es.

     2. Klicken Sie in Xcode mit der rechten Maustaste auf Ihr Projekt, und klicken Sie dann auf die Option **Add Files to** zum Hinzufügen des Ordners **WindowsAzureMessaging.framework** zum Xcode-Projekt. Klicken Sie auf **Optionen**, vergewissern Sie sich, dass die Option **Copy items if needed** (Elemente kopieren, wenn nötig) aktiviert ist, und klicken Sie dann auf **Hinzufügen**.

        ![Azure SDK entzippen][10]

6. Fügen Sie dem Projekt eine neue Headerdatei mit dem Namen `HubInfo.h`hinzu. Diese Datei enthält die Konstanten für Ihren Notification Hub. Fügen Sie die folgenden Definitionen hinzu, und ersetzen Sie die Platzhalter für Zeichenfolgenliterale durch Ihren *Hubnamen* und den zuvor notierten Wert für *DefaultListenSharedAccessSignature*.

    ```objc
    #ifndef HubInfo_h
    #define HubInfo_h

        #define HUBNAME @"<Enter the name of your hub>"
        #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"

    #endif /* HubInfo_h */
    ```

7. Öffnen Sie die Datei `AppDelegate.h` , und fügen Sie die folgenden Importdirektiven hinzu:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    #import <UserNotifications/UserNotifications.h>
    #import "HubInfo.h"
    ```
8. Fügen Sie in der Datei `AppDelegate.m` in der `didFinishLaunchingWithOptions`-Methode basierend auf Ihrer iOS-Version folgenden Code hinzu. Dieser Code registriert Ihr Gerätehandle bei APNS:

    ```objc
    UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
        UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];

    [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    [[UIApplication sharedApplication] registerForRemoteNotifications];
    ```

9. Fügen Sie in der gleichen Datei die folgenden Methoden hinzu:

    ```objc
        - (void) application:(UIApplication *) application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                    notificationHubPath:HUBNAME];

        [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
            else {
                [self MessageBox:@"Registration Status" message:@"Registered"];
            }
        }];
        }

    -(void)MessageBox:(NSString *) title message:(NSString *)messageText
    {
        UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:messageText preferredStyle:UIAlertControllerStyleAlert];
        UIAlertAction *okAction = [UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil];
        [alert addAction:okAction];
        [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }
    ```

    Mit diesem Code wird eine Verbindung mit dem Notification Hub mithilfe der in „HubInfo.h“ angegebenen Verbindungsinformationen hergestellt. Dieser Code übergibt das Gerätetoken an den Notification Hub, sodass dieser Benachrichtigungen senden kann.

10. Fügen Sie in derselben Datei folgende Methode hinzu, um einen **UIAlert** anzuzeigen, wenn die Benachrichtigung empfangen wird, während die App aktiv ist:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

11. Erstellen Sie die App auf Ihrem Gerät, und führen Sie sie dort aus, um Fehler auszuschließen.

## <a name="send-test-push-notifications"></a>Senden von Test-Pushbenachrichtigungen

Mit der Option *Testsendevorgang* im [Azure-Portal] können Sie den Empfang von Benachrichtigungen in Ihrer App testen. Diese Option sendet zu Testzwecken eine Pushbenachrichtigung an Ihr Gerät.

![Azure-Portal – Testsendung][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="verify-that-your-app-receives-push-notifications"></a>Überprüfen, ob die App Pushbenachrichtigungen empfängt

Zum Testen von Pushbenachrichtigungen unter iOS müssen Sie die App auf einem physischen iOS-Gerät bereitstellen. Sie können keine Apple-Pushbenachrichtigungen mit dem iOS-Simulator senden.

1. Führen Sie die App aus, und überprüfen Sie, ob die Registrierung erfolgreich ist. Drücken Sie dann **OK**.

    ![Registrierungstest für iOS-App-Pushbenachrichtigungen][33]

2. Als Nächstes senden Sie zu Testzwecken eine Pushbenachrichtigung über das [Azure-Portal], wie im vorherigen Abschnitt beschrieben.

3. Die Pushbenachrichtigung wird an alle Geräte gesendet, die dafür registriert sind, die Benachrichtigungen vom jeweiligen Notification Hub zu erhalten.

    ![iOS-App-Pushbenachrichtigung – Testempfang][35]

## <a name="next-steps"></a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Pushbenachrichtigungen an alle Ihre registrierten iOS-Geräte übertragen. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte iOS-Geräte senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Geräte](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png
[12]: ./media/notification-hubs-ios-get-started/notification-hubs-enable-push.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png

<!-- URLs. -->
[Microsoft Azure-Messaging-Framework]: https://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Azure Notification Hubs Notify Users for iOS with .NET backend]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure-Portal]: https://portal.azure.com
