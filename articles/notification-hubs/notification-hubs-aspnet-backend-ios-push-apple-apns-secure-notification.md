---
title: Azure Notification Hubs – Sichere Pushbenachrichtigungen
description: Erfahren Sie mehr über das Senden sicherer Pushbenachrichtigungen an eine iOS-App von Azure. Die Codebeispiele wurden in Objekctive-C und C# geschrieben.
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d88bdb1eaeb95413df84bf69ed4fc763b6d4901f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458490"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs – Sichere Pushbenachrichtigungen

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Übersicht

Durch die Unterstützung von Pushbenachrichtigungen in Microsoft Azure haben Sie Zugriff auf eine benutzerfreundliche, plattformübergreifende und horizontal skalierte Pushinfrastruktur, die die Implementierung von Pushbenachrichtigungen sowohl für Endbenutzer- als auch für Unternehmensanwendungen für mobile Plattformen erheblich vereinfacht.

Es kann vorkommen, dass eine Anwendung etwas in die Benachrichtigung einschließen möchte, dass aufgrund gesetzlicher oder sicherheitsbedingter Einschränkungen nicht über die Standardinfrastruktur von Pushbenachrichtigungen übertragen werden kann. In diesem Lernprogramm wird beschrieben, wie Sie die gleiche Funktionalität erzielen, indem Sie vertrauliche Informationen über eine sichere authentifizierte Verbindung zwischen dem Clientgerät und dem App-Back-End senden.

Der generelle Ablauf sieht folgendermaßen aus:

1. Das App-Back-End:
   * Speichert die sichere Nutzlast in einer Back-End-Datenbank.
   * Sendet die ID dieser Benachrichtigung an das Gerät (es werden keine sicheren Informationen gesendet).
2. Bei Erhalt der Benachrichtigung reagiert die App auf dem Gerät folgendermaßen:
   * Das Gerät kontaktiert das Back-End und fordert die sichere Nutzlast an.
   * Die App kann die Nutzlast als Benachrichtigung auf dem Gerät anzeigen.

Beachten Sie, dass im obigen Ablauf (und in diesem Tutorial) angenommen wird, dass das Gerät ein Authentifizierungstoken im lokalen Speicher speichert, nachdem sich der Benutzer angemeldet hat. Dies gewährleistet einen nahtlosen Ablauf, da das Gerät mit diesem Token die sichere Nutzlast der Benachrichtigung abrufen kann. Wenn Ihre Anwendung keine Authentifizierungstoken auf dem Gerät speichert oder diese Token ablaufen können, sollte die Geräte-App nach Erhalt der Benachrichtigung eine generische Benachrichtigung anzeigen, in der der Benutzer zum Starten der App aufgefordert wird. Anschließend authentifiziert die App den Benutzer und zeigt die Nutzlast der Benachrichtigung an.

Dieses Lernprogramm zu sicheren Pushbenachrichtigungen veranschaulicht das sichere Senden einer Pushbenachrichtigung. Es baut auf dem Lernprogramm [Benachrichtigen von Benutzern](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) auf, daher sollten Sie die Schritte in diesem Lernprogramm zuerst durchführen.

> [!NOTE]
> In diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md)beschrieben erstellt und konfiguriert haben.

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>Ändern des iOS-Projekts

Nachdem Sie Ihr App-Back-End so geändert haben, dass lediglich die *ID* einer Benachrichtigung gesendet wird, müssen Sie Ihre iOS-App so ändern, dass sie diese Benachrichtigung verarbeitet und einen Rückruf an das Back-End sendet, um die anzuzeigende sichere Nachricht abzurufen.

Hierzu müssen wir die Logik zum Abrufen des sicheren Inhalts aus dem App-Back-End schreiben.

1. Stellen Sie in `AppDelegate.m` sicher, dass die App für automatische Benachrichtigungen registriert ist, damit sie die vom Back-End gesendete Benachrichtigungs-ID verarbeiten kann. Fügen Sie in didFinishLaunchingWithOptionsAdd die Option `UIRemoteNotificationTypeNewsstandContentAvailability` hinzu:

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. Fügen Sie am Anfang von `AppDelegate.m` einen implementation-Abschnitt mit der folgenden Deklaration hinzu:

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. Fügen Sie dann im implementation-Abschnitt den folgenden Code hinzu, und ersetzen Sie dabei den Platzhalter `{back-end endpoint}` durch den Back-End-Endpunkt, den Sie zuvor erhalten haben:

    ```objc
    NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    {
        // check if authenticated
        ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
        NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                    sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                    delegate:nil
                                    delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                completion([json objectForKey:@"Payload"], nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }
    ```

    Diese Methode ruft Ihr Back-End auf, um den Benachrichtigungsinhalt unter Verwendung der in den freigegebenen Einstellungen gespeicherten Anmeldeinformationen abzurufen.

4. Nun müssen wird die eingehende Benachrichtigung verarbeiten und den anzuzeigenden Inhalt mit der vorstehenden Methode abrufen. Zunächst müssen festlegen, dass die iOS-App im Hintergrund ausgeführt werden kann, wenn eine Pushbenachrichtigung empfangen wird. Wählen Sie in **XCode** Ihr App-Projekt im linken Bereich aus, und klicken Sie dann im Abschnitt **Targets** des mittleren Fensters auf Ihr Haupt-App-Ziel.
5. Klicken Sie dann oben im mittleren Fenster auf die Registerkarte **Capabilities**, und aktivieren Sie das Kontrollkästchen **Remote Notifications**.

    ![][IOS1]

6. Fügen Sie in `AppDelegate.m` die folgende Methode zur Verarbeitung von Pushbenachrichtigungen hinzu:

    ```objc
    -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
    {
        NSLog(@"%@", userInfo);

        [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
            if (!error) {
                // show local notification
                UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                localNotification.alertBody = payload;
                localNotification.timeZone = [NSTimeZone defaultTimeZone];
                [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                completionHandler(UIBackgroundFetchResultNewData);
            } else {
                completionHandler(UIBackgroundFetchResultFailed);
            }
        }];

    }
    ```

    Beachten Sie, dass eine fehlende Authentifizierungsheader-Eigenschaft oder eine Ablehnung durch das Back-End nach Möglichkeit behandelt werden sollte. Wie diese Fälle im Einzelnen zu behandeln sind, hängt größtenteils von der Umgebung des Zielbenutzers ab. Eine Möglichkeit besteht darin, eine Benachrichtigung mit einer generischen Eingabeaufforderung anzuzeigen, damit sich der Benutzer zum Abrufen der eigentlichen Benachrichtigung authentifiziert.

## <a name="run-the-application"></a>Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1. Unter XCode führen Sie die App auf einem physischen iOS-Gerät aus (im Simulator funktionieren Pushbenachrichtigungen nicht).
2. Geben Sie in der iOS-App-UI einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.
3. Klicken Sie in der iOS-App-UI auf **Log in**. Klicken Sie anschließend auf **Send push**. Die sichere Benachrichtigung sollte nun in der Mitteilungszentrale angezeigt werden.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
