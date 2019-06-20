---
title: Senden von Pushbenachrichtigungen an bestimmte iOS-Geräte mit Azure Notification Hubs | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an bestimmte iOS-Geräte senden.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: dd625dba0e125ccf993af524a0ab0c0cc66555fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60873141"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an bestimmte iOS-Geräte mit Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Übersicht

In diesem Tutorial wird gezeigt, wie Sie mit Azure Notification Hubs Benachrichtigungen zu aktuellen Nachrichten an eine iOS-Apps senden. Anschließend können Sie sich für Kategorien aktueller Nachrichten registrieren, die Sie interessieren, und nur Pushbenachrichtigungen für diese Kategorien empfangen. Dieses Szenario ist ein häufiges Muster für viele Apps, bei denen Benachrichtigungen an Benutzergruppen gesendet werden müssen, die zuvor Interesse daran bekundet haben, zum Beispiel RSS-Feeds, Apps für Musikfans usw.

Übertragungsszenarien werden durch das Einfügen von einem oder mehreren *Tags* möglich, wenn eine Registrierung im Notification Hub erstellt wird. Wenn Benachrichtigungen an ein Tag gesendet werden, erhalten Geräte, die für das Tag registriert wurden, diese Benachrichtigung. Da es sich bei Tags um Zeichenfolgen handelt, müssen diese nicht im Voraus bereitgestellt werden. Weitere Informationen zu Tags finden Sie unter [Notification Hubs – Weiterleitung und Tagausdrücke](notification-hubs-tags-segment-push-message.md).

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Hinzufügen einer Kategorieauswahl zur App
> * Senden von Benachrichtigungen mit Tags
> * Senden von Benachrichtigungen vom Gerät
> * Ausführen der App und Erzeugen von Benachrichtigungen

## <a name="prerequisites"></a>Voraussetzungen

Dieses Thema baut auf der App auf, die Sie im folgenden Tutorial erstellt haben: [Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs][get-started]. Bevor Sie dieses Tutorial beginnen, müssen Sie das folgende Tutorial abgeschlossen haben: [Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Hinzufügen der Kategorieauswahl zur App

Zunächst werden Sie Benutzeroberflächenelemente zum vorhandenen Storyboard hinzufügen, mit denen Benutzer Kategorien für die Registrierung auswählen können. Die durch den Benutzer ausgewählten Kategorien werden auf dem Gerät gespeichert. Wenn die App gestartet wird, wird eine Geräteregistrierung in Ihrem Notification Hub mit den ausgewählten Kategorien als Tags erstellt.

1. Fügen Sie die folgenden Komponenten aus der Objektbibliothek zu **MainStoryboard_iPhone.storyboard** hinzu:

   * Eine Beschriftung mit dem Text "Breaking News",
   * Beschriftungen mit den Kategorien "World", "Politics", "Business", "Technology", "Science", "Sports",
   * Sechs Switches, einen pro Kategorie, wobei für jeden Switch **State** standardmäßig auf **Off** festgelegt ist.
   * Eine Schaltfläche mit der Beschriftung "Subscribe"

     Ihr Storyboard sollte nun folgendermaßen aussehen:

     ![Xcode Interface Builder][3]

2. Erstellen Sie im Assistant Editor Outlets für alle Switches und nennen Sie diese "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch", "SportsSwitch"
3. Erstellen Sie eine Aktion für Ihre Schaltfläche mit dem Namen `subscribe`. `ViewController.h` sollte den folgenden Code enthalten:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Erstellen Sie eine neue **Cocoa Touch**-Klasse namens `Notifications`. Fügen Sie den folgenden Code im Schnittstellenbereich der Datei Notifications.h ein:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Fügen Sie die folgende Import-Anweisung zu Notifications.h hinzu:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Fügen Sie den folgenden Code im Implementierungsbereich der Datei „Notifications.m“ ein.

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Diese Klasse verwendet den lokalen Speicher, um die Nachrichtenkategorien zu speichern und abzurufen, die das Gerät empfangen wird. Sie enthält außerdem eine Methode zum Registrieren dieser Kategorien mithilfe einer [Vorlagenregistrierung](notification-hubs-templates-cross-platform-push-messages.md) .

7. Fügen Sie in der `AppDelegate.h`-Datei eine Importanweisung für `Notifications.h` und eine Eigenschaft für eine Instanz der `Notifications`-Klasse hinzu:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. Fügen Sie in der `didFinishLaunchingWithOptions`-Methode in `AppDelegate.m` den Code zum Initialisieren der Notifications-Instanz am Anfang der Methode hinzu.  
    In `HUBNAME` und `HUBLISTENACCESS` (definiert in `hubinfo.h`) sollten die Platzhalter `<hub name>` und `<connection string with listen access>` bereits durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für die zuvor abgerufene *DefaultListenSharedAccessSignature* ersetzt sein.

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Da Anmeldenamen, die mit einer Client-App verteilt werden, nicht sehr sicher sind, sollten Sie nur den Schlüssel für den Abhörzugriff mit Ihrer Client-App verteilen. Der Abhörzugriff ermöglicht der App, sich für Benachrichtigungen zu registrieren, aber es können keine vorhandenen Registrierungen geändert und keine Benachrichtigungen versendet werden. Der Vollzugriffsschlüssel wird in einem gesicherten Back-End-Dienst für das Versenden von Benachrichtigungen und das Ändern vorhandener Benachrichtigungen verwendet.

9. Ersetzen Sie in der `didRegisterForRemoteNotificationsWithDeviceToken`-Methode in `AppDelegate.m` den Code in der Methode durch den folgenden Code, um das Gerätetoken an die `notifications`-Klasse zu übergeben. Die `notifications`-Klasse führt die Registrierung für Benachrichtigungen mit den Kategorien durch. Wenn der Benutzer die Auswahl der Kategorien ändert, wird die `subscribeWithCategories`-Methode als Antwort auf die Schaltfläche **subscribe** aufgerufen, um sie zu aktualisieren.

    > [!NOTE]
    > Da sich der durch den Apple Push Notification Service (APNS) zugeteilte Geräte-Token jederzeit ändern kann, sollten Sie sich regelmäßig für Benachrichtigungen registrieren, um Benachrichtigungsfehler zu vermeiden. Dieses Beispiel registriert sich jedes Mal für Benachrichtigungen, wenn die App gestartet wird. Für häufig ausgeführte Anwendungen (öfters als einmal täglich) können Sie die Registrierung wahrscheinlich überspringen, wenn weniger als ein Tag seit der letzten Registrierung vergangen ist, um Bandbreite einzusparen.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    Die Methode `didRegisterForRemoteNotificationsWithDeviceToken` sollte nun keinen anderen Code mehr enthalten.

10. Die folgenden Methoden sollten durch Abschließen des Tutorials [Erste Schritte mit Notification Hubs][get-started] bereits in `AppDelegate.m` vorhanden sein. Wenn dies nicht der Fall ist, fügen Sie sie hinzu.

    ```objc
    -(void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    * (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Diese Methode verarbeitet Benachrichtigungen, die während der Ausführung der App empfangen werden, durch Anzeige eines einfachen **UIAlert**.

11. Fügen Sie in `ViewController.m` eine `import`-Anweisung für `AppDelegate.h` hinzu, und kopieren Sie den folgenden Code in die von XCode generierte `subscribe`-Methode. Dieser Code aktualisiert die Benachrichtigungsregistrierung so, dass die neuen Kategorietags verwendet werden, die der Benutzer auf der Benutzeroberfläche ausgewählt hat.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            [(AppDelegate*)[[UIApplication sharedApplication]delegate] MessageBox:@"Notification" message:@"Subscribed!"];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Diese Methode erstellt ein `NSMutableArray` mit Kategorien und verwendet die Klasse `Notifications`, um die Liste im lokalen Speicher zu speichern und die entsprechenden Tags bei Ihrem Benachrichtigungs-Hub zu registrieren. Wenn Kategorien geändert werden, wird die Registrierung mit neuen Kategorien neu erstellt.

12. Fügen Sie in `ViewController.m` den folgenden Code in der `viewDidLoad`-Methode hinzu, um die Benutzeroberfläche auf Grundlage der zuvor gespeicherten Kategorien festzulegen.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

Die App kann jetzt verschiedene Kategorien im lokalen Speicher des Geräts speichern, die für die Registrierung beim Notification Hub verwendet werden, wenn die App gestartet wird. Der Benutzer kann zur Laufzeit die Auswahl der Kategorien ändern und auf die `subscribe`-Methode klicken, um die Registrierung für das Gerät zu aktualisieren. Als Nächstes aktualisieren Sie die App zum Senden von Benachrichtigungen zu aktuellen Nachrichten direkt in der App selbst.

## <a name="optional-send-tagged-notifications"></a>(Optional) Senden von Benachrichtigungen mit Tags

Wenn Sie keinen Zugriff auf Visual Studio haben, können Sie den nächsten Abschnitt überspringen und Benachrichtigungen direkt über die App senden. Sie können auch die richtige Vorlagenbenachrichtigung aus dem [Azure-Portal] mithilfe der Registerkarte „Debuggen“ für den Notification Hub senden.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(Optional) Senden von Benachrichtigungen vom Gerät

Normalerweise würden die Benachrichtigungen von einem Back-End-Dienst gesendet werden, aber Sie können Benachrichtigungen zu aktuellen Nachrichten direkt über die App senden. Dazu wird die `SendNotificationRESTAPI`-Methode aktualisiert, die im Tutorial [Erste Schritte mit Notification Hubs][get-started] definiert wurde.

1. Aktualisieren Sie in `ViewController.m` die `SendNotificationRESTAPI`-Methode wie folgt, sodass ein Parameter für das Kategorietag akzeptiert und die richtige [Vorlagenbenachrichtigung](notification-hubs-templates-cross-platform-push-messages.md) gesendet wird.

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
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

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

2. Aktualisieren Sie in `ViewController.m` die Aktion `Send Notification`, wie im folgenden Code gezeigt. Dadurch werden die Benachrichtigungen gesendet, indem jedes Tag einzeln verwendet wird, und sie werden an mehrere Plattformen gesendet.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Erstellen Sie das Projekt neu, und stellen Sie sicher, dass keine Buildfehler vorliegen.

## <a name="run-the-app-and-generate-notifications"></a>Ausführen der Anwendung und Erzeugen von Benachrichtigungen

1. Klicken Sie auf die Schaltfläche Ausführen, um das Projekt zu erstellen und die App zu starten. Wählen Sie einige Optionen zum Abonnieren aktueller Nachrichten aus, und klicken Sie dann auf die Schaltfläche **Subscribe** . Es sollte in einem Dialogfeld angegeben werden, dass die Benachrichtigungen abonniert wurden.

    ![Beispielbenachrichtigung unter iOS][1]

    Wenn Sie **Subscribe**auswählen, konvertiert die App die ausgewählten Kategorien in Tags und fordert eine neue Geräteregistrierung für die ausgewählten Tags vom Notification Hub an.

2. Geben Sie eine Nachricht ein, die als aktuelle Nachricht gesendet werden soll, und klicken Sie dann auf die Schaltfläche **Benachrichtigung senden** . Führen Sie alternativ die .NET-Konsolen-App aus, um Benachrichtigungen zu generieren.

    ![Benachrichtigungseinstellungen in iOS ändern][2]

3. Jedes Gerät, das aktuelle Nachrichten abonniert hat, erhält die Benachrichtigungen, die Sie gerade gesendet haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Broadcastbenachrichtigungen an bestimmte iOS-Geräte gesendet, die für die entsprechenden Kategorien registriert wurden. Um zu erfahren, wie Sie lokalisierte Pushbenachrichtigungen senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden von lokalisierten Pushbenachrichtigungen](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure-Portal]: https://portal.azure.com
