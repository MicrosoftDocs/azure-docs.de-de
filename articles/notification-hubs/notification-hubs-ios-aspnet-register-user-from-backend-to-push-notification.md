---
title: Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe einer Web-API | Microsoft Docs
description: Hier erfahren Sie, wie Sie in einer iOS-App eine Pushbenachrichtigungsregistrierung bei Azure Notification Hubs anfordern, wenn die Registrierung von der ASP.NET-Web-API durchgeführt wird.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: ff77a955c34941d87a1f653726ab3f19e84aa440
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458327"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mit ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Übersicht

In diesem Artikel erfahren Sie, wie Sie Pushbenachrichtigungs-Registrierungen mit Azure Notification Hubs anfordern können, wenn die Registrierung von einer ASP.NET-Web-API durchgeführt wird. Dieses Lernprogramm baut auf dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs]auf. Sie müssen zuvor die Schritte in diesem Lernprogramm abgeschlossen haben, in denen der authentifizierte Mobile Service erstellt wird. Weitere Informationen zum Benachrichtigen von Benutzern finden Sie unter [Benachrichtigen von Benutzern mit Notification Hubs].

## <a name="update-your-app"></a>Aktualisieren Ihrer App

1. Fügen Sie die folgenden Komponenten aus der Objektbibliothek zu MainStoryboard_iPhone.storyboard hinzu:

   * **Bezeichnung:** Push to User with Notification Hubs
   * **Bezeichnung:** InstallationId
   * **Bezeichnung:** User
   * **Textfeld:** User
   * **Bezeichnung:** Password
   * **Textfeld:** Password
   * **Schaltfläche:** Login

     Ihr Storyboard sieht nun wie folgt aus:

     ![][0]

2. Erstellen Sie im Assistant Editor Outlets für alle Switches, und rufen Sie sie auf. Verbinden Sie die Textfelder mit dem View Controller (delegate) und erstellen Sie eine **Action** für die **login**-Schaltfläche.

    ![][1]

    Die Datei "BreakingNewsViewController.h" sollte nun den folgenden Code enthalten:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Erstellen Sie eine Klasse namens `DeviceInfo`, und kopieren Sie den folgenden Code in den Schnittstellenbereich der Datei „DeviceInfo.h“:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Fügen Sie den folgenden Code im Implementierungsbereich der Datei DeviceInfo.m ein:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. Fügen Sie in der Datei "PushToUserAppDelegate.h" die folgende Singleton-Eigenschaft hinzu:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. Fügen Sie in „PushToUserAppDelegate.m“ in der Methode `didFinishLaunchingWithOptions` den folgenden Code hinzu:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    Die erste Zeile initialisiert den Singleton `DeviceInfo`. Die zweite Zeile startet die Registrierung für Pushbenachrichtigungen, die bereits vorhanden ist, wenn Sie das Tutorial [Erste Schritte mit Notification Hubs] abgeschlossen haben.
7. Implementieren Sie in „PushToUserAppDelegate.m“ die Methode `didRegisterForRemoteNotificationsWithDeviceToken` in Ihrem AppDelegate-Element, und fügen Sie den folgenden Code ein:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    Dieser Code legt das Geräte-Token für die Anfrage fest.

   > [!NOTE]
   > Die Methode sollte nun keinen weiteren Code mehr enthalten. Falls Sie aus dem Tutorial [Erste Schritte mit Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md) bereits über einen Aufruf der Methode `registerNativeWithDeviceToken` verfügen, müssen Sie diesen Aufruf auskommentieren oder entfernen.

8. Fügen Sie in der Datei `PushToUserAppDelegate.m` die folgende Handlermethode hinzu:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Diese Methode zeigt eine Warnung in der GUI an, wenn Ihre App während der Ausführung eine Benachrichtigung empfängt.

9. Öffnen Sie die Datei `PushToUserViewController.m`, und blenden Sie in der folgenden Implementierung die Tastatur wieder ein:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. Initialisieren Sie in der Datei `PushToUserViewController.m` die Bezeichnung `installationId` in der Methode `viewDidLoad` wie folgt:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Fügen Sie in `PushToUserViewController.m` die folgenden Eigenschaften in der Schnittstelle hinzu:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Fügen Sie anschließend die folgende Implementierung ein:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Kopieren Sie den folgenden Code in die von XCode generierte Handlermethode `login`:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Diese Methode ruft eine Installations-ID und einen Channel für Pushbenachrichtigungen ab und sendet diese zusammen mit dem Gerätetyp an die authentifizierte Web-API-Methode, die wiederum eine Registrierung in Notification Hubs erstellt. Diese Web-API wurde in [Benachrichtigen von Benutzern mit Notification Hubs]definiert.

Nun haben Sie die Client-App aktualisiert und können zum Thema [Benachrichtigen von Benutzern mit Notification Hubs] zurückkehren und den mobilen Dienst aktualisieren, um Benachrichtigungen mit Notification Hubs zu senden.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Benachrichtigen von Benutzern mit Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Erste Schritte mit Notification Hubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
