---
title: Senden von Pushbenachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure Notification Hubs Pushbenachrichtigungen an bestimmte iOS-Benutzer senden.
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016942"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>Tutorial: Senden von Pushbenachrichtigungen an bestimmte Benutzer mit Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

Ein ASP.NET WebAPI-Back-End wird verwendet, um Clients zu authentifizieren. Zum Authentifizieren von Clients und Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Thema [Registrieren von App-Back-End aus](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) gezeigt.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen des WebAPI-Projekts
> * Authentifizieren von Clients beim WebAPI-Back-End
> * Registrieren für Benachrichtigungen unter Verwendung des WebAPI-Back-Ends
> * Senden von Benachrichtigungen über das WebAPI-Back-End
> * Veröffentlichen des neuen WebAPI-Back-Ends
> * Ändern der iOS-App
> * Testen der Anwendung

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs](ios-sdk-get-started.md) beschrieben erstellt und konfiguriert haben. Dieses Lernprogramm ist außerdem die Voraussetzung für das Lernprogramm [Sichere Pushbenachrichtigungen (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) .
Wenn Sie Mobile Apps als Back-End-Dienst verwenden möchten, lesen Sie [Mobile Apps: Erste Schritte mit Push](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>Ändern der iOS-App

1. Öffnen Sie die Einzelseitenansicht-App, die Sie in [Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs](ios-sdk-get-started.md) erstellt haben.

   > [!NOTE]
   > In diesem Abschnitt wird davon ausgegangen, dass Sie Ihr Projekt mit einem leeren Organisationsnamen konfiguriert haben. Falls nicht, stellen Sie allen Klassennamen Ihren Organisationsnamen voran.

2. Fügen Sie in der Datei `Main.storyboard` die im folgenden Screenshot abgebildeten Komponenten aus der Objektbibliothek hinzu.

    ![Bearbeiten des Storybards in Xcode Interface Builder][1]

   * **Benutzername**: Ein UITextField mit dem Platzhaltertext *Enter Username*, direkt unter der Bezeichnung zum Senden der Ergebnisse und durch den linken und rechten Rand und die Anordnung unter der Bezeichnung zum Senden der Ergebnisse beschränkt.
   * **Kennwort**: Ein UITextField mit dem Platzhaltertext *Enter Password*, direkt unter dem Textfeld „Username“ und durch den linken und rechten Rand und die Anordnung unter dem Textfeld „Username“ beschränkt. Aktivieren Sie die Option **Secure Text Entry** im Attribute Inspector unter *Return Key*.
   * **Log in:** Ein bezeichneter UIButton direkt unter dem Kennwortfeld. Deaktivieren Sie die Option **Enabled** im Attributes Inspector unter *Control-Content*.
   * **WNS:** Bezeichnung und Switch, um das Senden der Benachrichtigung an den Windows-Benachrichtigungsdienst zu aktivieren, wenn er auf dem Hub eingerichtet wurde. Siehe dazu das Tutorial [Erste Schritte für Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).
   * **GCM**: Bezeichnung und Switch, um das Senden der Benachrichtigung an Google Cloud Messaging zu aktivieren, wenn es auf dem Hub eingerichtet wurde. Siehe dazu das Lernprogramm [Erste Schritte für Android](notification-hubs-android-push-notification-google-gcm-get-started.md) .
   * **APNS:** Bezeichnung und Switch, um das Senden der Benachrichtigung an den Apple Platform Notification Service zu aktivieren.
   * **Recipient Username:** ein UITextField mit dem Platzhaltertext *Recipient username tag*, direkt unter der GCM-Bezeichnung und durch den linken und rechten Rand und die Anordnung unter der GCM-Bezeichnung beschränkt.

     In [Tutorial: Senden von Pushbenachrichtigungen an iOS-Apps mit Azure Notification Hubs](ios-sdk-get-started.md) wurden einige Komponenten hinzugefügt.

3. Ziehen Sie bei gedrückter **STRG**-Taste die Komponenten in der Ansicht zu `ViewController.h`, und fügen Sie diese neuen Outlets hinzu:

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. Fügen Sie in `ViewController.h` folgende `#define`-Zeile direkt hinter den import-Anweisungen hinzu. Ersetzen Sie den Platzhalter `<Your backend endpoint>` durch die Ziel-URL, die Sie im vorherigen Abschnitt zum Bereitstellen Ihres App-Back-Ends verwendet haben. Zum Beispiel `http://your_backend.azurewebsites.net`:

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. Erstellen Sie in Ihrem Projekt eine neue „Cocoa Touch“-Klasse mit dem Namen `RegisterClient` als Schnittstelle mit dem erstellten ASP.NET-Back-End. Erstellen Sie die Klasse, die von `NSObject`erbt. Fügen Sie anschließend in `RegisterClient.h` den folgenden Code hinzu:

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. Aktualisieren Sie in `RegisterClient.m` den `@interface`-Abschnitt:

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. Ersetzen Sie den `@implementation`-Abschnitt in „RegisterClient.m“ durch den folgenden Code:

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    Dieser Code implementiert die im Artikel [Registrieren über ein App-Back-End](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend) erläuterte Logik unter Verwendung von NSURLSession zum Durchführen von REST-Aufrufen an das App-Back-End und von NSUserDefaults zum lokalen Speichern der vom Notification Hub zurückgegebenen registrationId.

    Die `authorizationHeader`-Eigenschaft dieser Klasse muss festgelegt werden, damit die Klasse ordnungsgemäß funktioniert. Diese Eigenschaft wird von der `ViewController`-Klasse nach der Anmeldung festgelegt.

8. Fügen Sie in `ViewController.h` eine `#import`-Anweisung für `RegisterClient.h` hinzu. Fügen Sie dann eine Deklaration für das Gerätetoken und einen Verweis auf eine `RegisterClient`-Instanz im `@interface`-Abschnitt hinzu:

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. Fügen Sie in "ViewController.m" eine private Methodendeklaration im `@interface` -Abschnitt hinzu:

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > Der folgende Codeausschnitt ist kein sicheres Authentifizierungsschema. Sie sollten die Implementierung von `createAndSetAuthenticationHeaderWithUsername:AndPassword:` durch Ihren spezifischen Authentifizierungsmechanismus ersetzen, der ein von der RegisterClient-Klasse verwendetes Authentifizierungstoken generiert, z.B. OAuth, Active Directory.

10. Fügen Sie dann im `@implementation`-Abschnitt von `ViewController.m` den folgenden Code ein, der die Implementierung zum Festlegen des Gerätetokens und Authentifizierungsheaders hinzugefügt.

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    Beachten Sie, dass durch Festlegen des Gerätetokens die Schaltfläche **Log in** (Anmelden) aktiviert wird. Dies liegt daran, dass sich der Ansichtscontroller bei der Anmeldeaktion für Pushbenachrichtigungen beim App-Back-End registriert. Daher soll erst auf die Anmeldeaktion (**Log in**) zugegriffen werden können, wenn das Gerätetoken ordnungsgemäß eingerichtet wurde. Sie können die Anmeldung von der Pushregistrierung entkoppeln, solange die Anmeldung vor der Registrierung erfolgt.

11. Verwenden Sie in „ViewController.m“ die folgenden Codeausschnitte, um die Aktionsmethode für die Schaltfläche **Log in** (Anmelden) und eine Methode zum Senden der Benachrichtigungsmeldung mithilfe des ASP.NET-Back-Ends zu implementieren.

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. Aktualisieren Sie die Aktion für die Schaltfläche **Send Notification** , sodass das ASP.NET-Back-End verwendet wird und der Sendevorgang an ein beliebiges durch einen Switch aktiviertes PNS erfolgt.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. Fügen Sie in der Funktion `ViewDidLoad` Folgendes hinzu, um die `RegisterClient`-Instanz zu instanziieren und den Delegat für Ihre Textfelder festzulegen.

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. Entfernen Sie jetzt in `AppDelegate.m` den gesamten Inhalt der `application:didRegisterForPushNotificationWithDeviceToken:`-Methode, und ersetzen Sie ihn durch Folgendes, um sicherzustellen, dass der Ansichtscontroller das neueste Gerätetoken aus APNs abgerufen hat:

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. Stellen Sie schließlich sicher, dass `AppDelegate.m` die folgende Methode enthält:

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>Testen der Anwendung

1. Unter XCode führen Sie die App auf einem physischen iOS-Gerät aus (im Simulator funktionieren Pushbenachrichtigungen nicht).
2. Geben Sie in der Benutzeroberfläche der iOS-App denselben Wert für den Benutzernamen und das Kennwort ein. Klicken Sie dann auf **Log in** (Anmelden).

    ![iOS-Testanwendung][2]

3. Ein Popupfenster sollte angezeigt werden, in dem Sie über eine erfolgreiche Registrierung informiert werden. Klicken Sie auf **OK**.

    ![iOS-Testbenachrichtigung][3]

4. Geben Sie im Textfeld *Recipient username tag* das Benutzernamenstag ein, das bei der Anmeldung von einem anderen Gerät verwendet wird.
5. Geben Sie eine Benachrichtigungsmeldung ein, und klicken Sie auf **Send Notification**. Die Benachrichtigungsmeldung wird nur auf den Geräten empfangen, die für das Empfänger-Benutzernamenstag registriert sind. Sie wird nur an diese Benutzer gesendet.

    ![Mit Tag versehene iOS-Testbenachrichtigung][4]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Pushbenachrichtigungen an bestimmte Benutzer senden, deren Registrierungen Tags zugeordnet sind. Um zu erfahren, wie Sie standortbasierte Pushbenachrichtigungen senden, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
>[Senden standortbasierter Pushbenachrichtigungen](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
