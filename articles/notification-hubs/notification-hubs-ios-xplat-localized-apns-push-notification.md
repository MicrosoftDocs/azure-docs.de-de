---
title: Senden von lokalisierten Benachrichtigungen an iOS-Geräte mit Azure Notification Hubs | Microsoft-Dokumentation
description: Informationen zum Senden von lokalisierten Benachrichtigungen an iOS-Geräte mit Azure Notification Hubs.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: a293f0b656c075ae3b21ccf98e602e43ed761958
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428459"
---
# <a name="tutorial-push-localized-notifications-to-ios-devices-using-azure-notification-hubs"></a>Tutorial: Senden von lokalisierten Benachrichtigungen an iOS-Geräte mit Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Store C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

In diesem Tutorial wird gezeigt, wie Sie mit dem Feature [Vorlagen](notification-hubs-templates-cross-platform-push-messages.md) von Azure Notification Hubs Benachrichtigungen senden können, die je nach Sprache und Gerät lokalisiert wurden. In diesem Tutorial beginnen Sie mit der iOS-App, die Sie in [Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten] erstellt haben. Anschließend können Sie sich für Kategorien registrieren, die Sie interessieren, eine Sprache für die Benachrichtigungen auszuwählen und nur Pushbenachrichtigungen für diese Kategorien in der jeweiligen Sprache empfangen.

Dieses Szenario besteht aus zwei Teilen:

* Mit der iOS-App können Client-Geräte eine Sprache auswählen und verschiedene Nachrichtenkategorien abonnieren.
* Das Back-End überträgt die Benachrichtigungen mithilfe des **Tag**- und **Vorlagen**-Features von Azure Notification Hubs.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Aktualisieren der App-Benutzeroberfläche
> * Erstellen der iOS-App
> * Senden von lokalisierten Vorlagenbenachrichtigungen aus der .NET-Konsolen-App
> * Senden von lokalisierten Vorlagenbenachrichtigungen vom Gerät

## <a name="overview"></a>Übersicht

In [Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten] haben Sie eine App erstellt, in der Benutzer mit **Tags** Nachrichten aus verschiedenen Kategorien abonnieren können. Viele Apps richten sich jedoch an verschiedene Märkte und müssen lokalisiert werden. In diesen Fällen muss auch der Inhalt der Benachrichtigungen lokalisiert und an die korrekten Geräte ausgeliefert werden. Dieses Tutorial zeigt Ihnen, wie Sie mit dem Feature für **Vorlagen** von Notification Hubs lokalisierte Benachrichtigungen über Nachrichten verschicken.

> [!NOTE]
> Sie können mehrere Versionen der einzelnen Tags erstellen, um lokalisierte Benachrichtigungen zu verschicken. Für Englisch, Französisch und Mandarin müssen Sie z.B. drei verschiedene Markierungen für Weltnachrichten erstellen: „world_en“, „world_fr“ und „world_ch“. Anschließend müssten Sie eine lokalisierte Version der Nachrichten an die einzelnen Tags schicken. In diesem Thema verwenden Sie Vorlagen, um die Anzahl der Tags einzugrenzen und den Versand mehrerer Nachrichten zu vermeiden.

Mit Vorlagen können Sie festlegen, wie ein bestimmtes Gerät eine Benachrichtigung empfangen soll. Die Vorlage gibt das exakte Format der Nutzlast anhand von Eigenschaften an, die Teil der von Ihrem Back-End verschickten Nachricht sind. In Ihrem Fall senden Sie eine sprachenunabhängige Nachricht, die alle unterstützten Sprachen enthält:

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

Anschließend stellen Sie sicher, dass sich die Geräte mit einer Vorlage registrieren, die auf die korrekte Eigenschaft verweist. Eine iOS-App, die eine Registrierung für französische Nachrichten durchführen möchte, registriert beispielsweise mit folgender Syntax:

```json
{
    aps: {
        alert: "$(News_French)"
    }
}
```

Weitere Informationen zu Vorlagen finden Sie im Artikel [Vorlagen](notification-hubs-templates-cross-platform-push-messages.md).

## <a name="prerequisites"></a>Voraussetzungen

* Arbeiten Sie das Tutorial [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten](notification-hubs-ios-xplat-segmented-apns-push-notification.md) durch, und halten Sie den Code verfügbar, da dieses Tutorial direkt auf diesem Code aufbaut.
* Visual Studio 2019 ist optional.

## <a name="update-the-app-user-interface"></a>Aktualisieren der App-Benutzeroberfläche

In diesem Abschnitt modifizieren Sie die App zu aktuellen Nachrichten, die Sie in dem Thema [Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten] erstellt haben, derart, dass lokalisierte aktuelle Nachrichten mit Vorlagen verschickt werden.

Fügen Sie in Ihrem `MainStoryboard_iPhone.storyboard`-Element ein segmentiertes Steuerelement mit den drei Sprachen hinzu: Englisch, Französisch und Mandarin.

![Erstellen der iOS-Storyboard-Benutzeroberfläche][13]

Fügen Sie dann auf jeden Fall wie in der folgenden Abbildung gezeigt ein IBOutlet in „ViewController.h“ hinzu:

![Erstellen der Outlets für Switches][14]

## <a name="build-the-ios-app"></a>Erstellen der iOS-App

1. Fügen Sie in Ihrem `Notification.h`-Element die `retrieveLocale`-Methode hinzu. Ändern Sie dann die store- und subscribe-Methoden wie im folgenden Code gezeigt:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;

    - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;

    - (NSSet*) retrieveCategories;

    - (int) retrieveLocale;
    ```
    Ändern Sie in Ihrem `Notification.m`-Element die `storeCategoriesAndSubscribe`-Methode, indem Sie den `locale`-Parameter hinzufügen und in den Benutzerstandardwerten speichern:

    ```objc
    - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
        [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
        [defaults synchronize];

        [self subscribeWithLocale: locale categories:categories completion:completion];
    }
    ```

    Ändern Sie dann die *subscribe* -Methode, um das Gebietsschema einzuschließen:

    ```objc
    - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
        SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];

        NSString* localeString;
        switch (locale) {
            case 0:
                localeString = @"English";
                break;
            case 1:
                localeString = @"French";
                break;
            case 2:
                localeString = @"Mandarin";
                break;
        }

        NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Sie verwenden die Methode `registerTemplateWithDeviceToken` anstelle von `registerNativeWithDeviceToken`. Bei der Registrierung für eine Vorlage muss die json-Vorlage und ein Name für die Vorlage bereitgestellt werden (da die App möglicherweise unterschiedliche Vorlagen registrieren möchte). Registrieren Sie Ihre Kategorien auf jeden Fall als Tags, da Sie so sicherstellen, dass Sie Benachrichtigungen für diese Nachrichten erhalten.

    Fügen Sie eine Methode hinzu, um das Gebietsschema aus den Benutzerstandardeinstellungen abzurufen:

    ```objc
    - (int) retrieveLocale {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        int locale = [defaults integerForKey:@"BreakingNewsLocale"];

        return locale < 0?0:locale;
    }
    ```

2. Nachdem Sie nun die `Notifications`-Klasse geändert haben, müssen Sie sicherstellen, dass `ViewController` das neue `UISegmentControl`-Element verwendet. Fügen Sie in der `viewDidLoad`-Methode die folgende Zeile hinzu, um sicherzustellen, dass das aktuell ausgewählte Gebietsschema angezeigt wird:

    ```objc
    self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
    ```

    Ändern Sie dann in Ihrer `subscribe`-Methode den Aufruf von `storeCategoriesAndSubscribe` in den folgenden Code:

    ```objc
    [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                    @"Subscribed!" delegate:nil cancelButtonTitle:
                                    @"OK" otherButtonTitles:nil, nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

3. Abschließend müssen Sie die `didRegisterForRemoteNotificationsWithDeviceToken`-Methode in „AppDelegate.m“ aktualisieren, damit Ihre Registrierung beim Starten der App korrekt aktualisiert wird. Ändern Sie den Aufruf der `subscribe`-Methode für Benachrichtigungen in folgenden Code:

    ```obj-c
    NSSet* categories = [self.notifications retrieveCategories];
    int locale = [self.notifications retrieveLocale];
    [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(Optional) Senden von lokalisierten Vorlagenbenachrichtigungen aus der .NET-Konsolen-App

[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(Optional) Senden von lokalisierten Vorlagenbenachrichtigungen vom Gerät

Wenn Sie keinen Zugriff auf Visual Studio haben oder einfach die lokalisierten Vorlagenbenachrichtigungen direkt aus der App auf dem Gerät senden möchten, Sie können die lokalisierten Vorlagenparameter der `SendNotificationRESTAPI`-Methode hinzufügen, die Sie im vorherigen Tutorial definiert haben.

```objc
- (void)SendNotificationRESTAPI:(NSString*)categoryTag
{
    NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                defaultSessionConfiguration] delegate:nil delegateQueue:nil];

    NSString *json;

    // Construct the messages REST endpoint
    NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                        HUBNAME, API_VERSION]];

    // Generated the token to be used in the authorization header.
    NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

    //Create the request to add the template notification message to the hub
    NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
    [request setHTTPMethod:@"POST"];

    // Add the category as a tag
    [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

    // Template notification
    json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
            \"News_English\":\"Breaking %@ News in English : %@\","
            \"News_French\":\"Breaking %@ News in French : %@\","
            \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
            categoryTag, self.notificationMessage.text,
            categoryTag, self.notificationMessage.text,  // insert English localized news here
            categoryTag, self.notificationMessage.text,  // insert French localized news here
            categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

    // Signify template notification format
    [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

    // JSON Content-Type
    [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

    //Authenticate the notification message POST request with the SaS token
    [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

    //Add the notification message body
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

    // Send the REST request
    NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
        NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
            }
            if (data != NULL)
            {
                //xmlParser = [[NSXMLParser alloc] initWithData:data];
                //[xmlParser setDelegate:self];
                //[xmlParser parse];
            }
        }];

    [dataTask resume];
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie lokalisierte Benachrichtigungen an iOS-Geräte gesendet. Um zu erfahren, wie Sie Pushbenachrichtigungen an bestimmte Benutzer von iOS-Apps senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von Pushbenachrichtigungen an bestimmte Benutzer](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)

<!-- Images. -->
[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten]: notification-hubs-ios-xplat-segmented-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript and HTML]: ../get-started-with-push-js.md
[Windows Developer Preview registration steps for Mobile Services]: ../mobile-services-windows-developer-preview-registration.md
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
