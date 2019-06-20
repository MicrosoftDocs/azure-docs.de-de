---
title: Senden von Pushbenachrichtigungen an Swift-iOS-Apps mit Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an Swift-iOS-Apps senden.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: 64a05c6cbd412953ae10b31efc1d141c9cefd062
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994250"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-using-the-notification-hubs-rest-api"></a>Tutorial: Senden von Pushbenachrichtigungen an Swift-iOS-Apps über die Notification Hubs-REST-API

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

In diesem Tutorial verwenden Sie Azure Notification Hubs, um Pushbenachrichtigungen mithilfe der [REST-API](/rest/api/notificationhubs/) an eine Swift-basierte iOS-Anwendung zu senden. Sie erstellen eine leere iOS-App, die Pushbenachrichtigungen mithilfe des Apple-Pushbenachrichtigungsdiensts ([Apple Push Notification Service, APNs](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)) empfängt.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Generieren der Datei für die Anforderung der Zertifikatsignierung
> * Anfordern von Pushbenachrichtigungen für Ihre App
> * Erstellen eines Bereitstellungsprofils für die App
> * Erstellen eines Notification Hubs
> * Konfigurieren Ihres Benachrichtigungshubs mit APNs-Informationen
> * Verbinden der iOS-App mit Notification Hubs
> * Testen der Lösung

## <a name="prerequisites"></a>Voraussetzungen
Um dies nachvollziehen zu können, benötigen Sie Folgendes:

- Sehen Sie sich die [Übersicht über Azure Notification Hubs](notification-hubs-push-notification-overview.md) an, wenn Sie mit dem Dienst noch nicht vertraut sind. 
- Erfahren Sie etwas über Registrierungen und die Installation: [Registrierungsverwaltung](notification-hubs-push-notification-registration-management.md)
- Ein aktives [Apple-Entwicklerkonto](https://developer.apple.com) 
- Einen Mac mit Xcode zusammen mit einem gültigen in Ihrer Keychain installierten Entwicklerzertifikat
- Ein physisches iPhone-Gerät für das Ausführen und Debuggen (Pushbenachrichtigungen können nicht mit dem Simulator getestet werden)
- Registrierung Ihres physischen iPhone-Geräts im [Apple-Portal](https://developer.apple.com) und Zuordnung zu Ihrem Zertifikat
- Ein [Azure-Abonnement](https://portal.azure.com), in dem Sie Ressourcen erstellen und verwalten können

Es sollte möglich sein, dieses Beispiel zu den Grundprinzipien ohne vorherige Erfahrung anhand der Anleitung nachzuvollziehen. Kenntnisse zu den folgenden Konzepten sind jedoch von Vorteil:

- Erstellen von iOS-Apps mit Xcode und Swift
- Konfigurieren einer [Azure Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md)-Instanz für iOS
- Vertrautheit mit dem [Apple Developer Portal](https://developer.apple.com) und dem [Azure-Portal](https://portal.azure.com)

> [!NOTE]
> Der Benachrichtigungshub wird für die ausschließliche Verwendung des *Sandbox*-Authentifizierungsmodus konfiguriert. Sie sollten diesen Authentifizierungsmodus nicht für Produktionsworkloads verwenden.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-notification-hubs"></a>Verbinden der iOS-App mit Notification Hubs
In diesem Abschnitt erstellen Sie die iOS-App, die die Verbindung mit dem Benachrichtigungshub herstellt.  

### <a name="create-an-ios-project"></a>Erstellen eines iOS-Projekts
1. Erstellen Sie in **Xcode** ein neues iOS-Projekt, und wählen Sie die Vorlage **Single View Application** (Anwendung in Einzelansicht) aus.
2. Gehen Sie zum Festlegen der Optionen für das neue Projekt wie folgt vor:
    1. Verwenden den gleichen **Produktnamen** (d. h. **PushDemo**) und die gleiche **Organisations-ID** (d. h. `com.<organization>`), die Sie beim Festlegen der **Bündel-ID** im **Apple Developer Portal** verwendet haben. 
    2. Wählen Sie das **Team** aus, für das die **App-ID** festgelegt wurde.
    3. Legen Sie die **Sprache** auf **Swift** fest.
    4. Klicken Sie unten auf der Seite auf **Weiter**
3. Erstellen Sie einen neuen Ordner namens **SupportingFiles**.
4. Erstellen Sie eine neue **plist**-Datei mit dem Namen **devsettings.plist** unter dem Ordner **SupportingFiles**. Fügen Sie diesen Ordner in Ihre Datei **gitignore** ein, damit er bei der Arbeit mit einem **Git-Repository** nicht committet wird. In einer Produktions-App würden Sie diese Geheimnisse wahrscheinlich im Rahmen eines automatisierten Buildprozesses mit einer Bedingung festlegen. In dieser exemplarischen Vorgehensweise wird dies jedoch nicht behandelt.
5. Bearbeiten Sie **devsettings.plist** so, dass die folgenden Konfigurationseinträge enthalten sind (mit Ihren eigenen Werten aus dem bereitgestellten **Benachrichtigungshub**):

   | Schlüssel                            | Type                     | Wert                     |               
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Zeichenfolge                   | \<hubKey\>                |
   | notificationHubKeyName         | Zeichenfolge                   | \<hubKeyName\>            |
   | notificationHubName            | Zeichenfolge                   | \<hubName\>               |
   | notificationHubNamespace       | Zeichenfolge                   | \<hubNamespace\>          |
    
   Die erforderlichen Werte finden Sie durch Navigieren zur Ressource **Benachrichtigungs-Hub** im **Azure-Portal**. Sie finden die Werte **notificationHubName** und **notificationHubNamespace** in der rechten oberen Ecke der **Zusammenfassung** auf der Seite **Übersicht**.

   ![Zusammenfassung „Zusammenfassung“ unter „Notification Hubs“](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Sie finden die Werte **notificationHubKeyName** und **notificationHubKey**, indem Sie zu **Zugriffsrichtlinien** navigieren und auf die jeweilige **Zugriffsrichtlinie** klicken. Beispiel: `DefaultFullSharedAccessSignature`. Kopieren Sie dann unter **Primäre Verbindungszeichenfolge** den Wert mit dem Präfix `SharedAccessKeyName=` für `notificationHubKeyName` und den Wert mit dem Präfix `SharedAccessKey=` für `notificationHubKey`. Die Verbindungszeichenfolge sollte das folgende Format aufweisen:
    
   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Zur Vereinfachung verwenden Sie *DefaultFullSharedAccessSignature*, damit können Sie das Token auch zum Senden von Benachrichtigungen verwenden. In der Praxis wäre `DefaultListenSharedAccessSignature` jedoch eine bessere Wahl für Situationen, in denen Sie nur Benachrichtigungen empfangen möchten.       
6. Klicken Sie unter **Project Navigator** (Projektnavigation) auf den **Projektnamen**, und klicken Sie auf die Registerkarte **Allgemein**.
7. Suchen Sie nach **Identität**, und legen Sie den Wert **Bündel-ID** so fest, dass er mit dem für die **App-ID** (aus dem vorhergehenden Schritt) übereinstimmt, d. h. `'com.<organization>.PushDemo'`.
8. Suchen Sie nach **Signatur**, und wählen Sie das richtige **Team** für Ihr **Apple Developer-Konto** aus (das, mit dem Sie zuvor Ihre Zertifikate und Profile erstellt haben).  **Xcode** sollte auf der Grundlage Ihrer **Bündel-ID** automatisch das entsprechende **Bereitstellungsprofil** abrufen. Wenn das neue **Bereitstellungsprofil** nicht angezeigt wird, aktualisieren Sie die Profile für die **Signaturidentität** (*Xcode > Einstellungen > Konto > Details anzeigen*). Durch Klicken auf die **Signierungsidentität** und anschließendes Klicken auf die Schaltfläche **Aktualisieren** in der rechten unteren Ecke sollten die Profile heruntergeladen werden.
9. Vergewissern Sie sich auf der Registerkarte **Funktionen**, dass **Pushbenachrichtigungen** aktiviert sind.
10. Öffnen Sie Ihre Datei **AppDelegate.swift**, um das Protokoll *UNUserNotificationCenterDelegate* zu implementieren, und fügen Sie am Anfang der Klasse folgenden Code hinzu:
    
    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {
        
        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")
        
        ...
    }
    ```

    Sie verwenden diese Member später noch einmal. *Tags* und *genericTemplate* werden im Rahmen der Registrierung verwendet. Weitere Informationen zu Tags finden Sie unter [Tags für Registrierungen](notification-hubs-tags-segment-push-message.md) und [Vorlagenregistrierungen](notification-hubs-templates-cross-platform-push-messages.md).
 
11. Fügen Sie in derselben Datei folgenden Code in die *didFinishLaunchingWithOptions*-Funktion ein:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }
        
    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in
                
            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Dieser Code ruft die Einstellungswerte aus **devsettings.plist** ab, legt die **AppDelegate**-Klasse als Delegat *UNUserNotificationCenter* fest, fordert Autorisierung für Pushbenachrichtigungen an und ruft dann *registerForRemoteNotifications* auf.
    
    Zur Vereinfachung unterstützt der Code **nur iOS 10 und höher**. Sie können Unterstützung für ältere Betriebssystemversionen hinzufügen, indem Sie die entsprechenden APIs und Ansätze wie gewohnt mit Bedingungen verwenden.

12. Fügen Sie in der gleichen Datei die folgenden Funktionen hinzu:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    Der Code verwendet die Werte *installationId* und *pushChannel*, um eine Registrierung beim **Benachrichtigungshub** durchzuführen. In diesem Fall verwenden Sie *UIDevice.current.identifierForVendor*, um einen eindeutigen Wert zum Identifizieren des Geräts bereitzustellen, und formatieren dann *deviceToken*, um den gewünschten Wert *pushChannel* bereitzustellen. Die *showAlert*-Funktion zeigt zu Demonstrationszwecken einfach einen Meldungstext an.

13. Fügen Sie immer noch in **AppDelegate.swift** die **UNUserNotificationCenterDelegate**-Funktionen *willPresent* und *didReceive* hinzu, um eine Warnung anzuzeigen, wenn die Benachrichtigung eingegangen ist, als die App im Vordergrund bzw. Hintergrund ausgeführt wurde.
    
    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }
    
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```    

14. Fügen Sie am Ende der *didRegisterForRemoteNotificationsWithDeviceToken*-Funktion print-Anweisungen ein, um zu überprüfen, ob *installationId* und *pushChannel* Werte zugewiesen werden.
15. Erstellen Sie Ordner (**Models**, **Services** und **Utilities**) für die grundlegenden Komponenten, die Sie dem Projekt später hinzufügen.
16. Vergewissern Sie sich, dass das Projekt erstellt und auf einem physischen Gerät ausgeführt wird. (Pushbenachrichtigungen können nicht mit dem Simulator getestet werden.)

### <a name="create-models"></a>Erstellen von Modellen
In diesem Schritt erstellen Sie eine Gruppe von Modellen, die die Nutzlasten der [Notification Hubs-REST-API](/rest/api/notificationhubs/) darstellen und in denen die erforderlichen **SAS-Tokendaten** gespeichert werden.


1.  Fügen Sie unter **Models** eine neue Swift-Datei mit dem Namen **PushTemplate.swift** hinzu. Dieses Modell bietet eine Struktur für den **Hauptteil** einer einzelnen Vorlage als Teil der **DeviceInstallation**-Nutzlast:
    
    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String
    
        init(withBody body : String) {
            self.body = body
        }
    }
    ```

2. Fügen Sie dem Ordner **Models** eine neue Swift-Datei mit dem Namen **DeviceInstallation.swift** hinzu. Diese Datei definiert eine Struktur, die die Nutzlast zum Erstellen oder Aktualisieren einer **Geräteinstallation** darstellt. Fügen Sie der Datei den folgenden Code hinzu:
    
    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>
    
        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

3.  Fügen Sie unter **Models** eine neue Swift-Datei mit dem Namen **TokenData.swift** hinzu. Dieses Modell wird zum Speichern eines **SAS-Tokens** gemeinsam mit seinem Ablauf verwendet.

    ```swift
    import Foundation

    struct TokenData {
    
        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Generieren eines SAS-Tokens
**Notification Hubs** verwendet die gleiche Sicherheitsinfrastruktur wie **Azure Service Bus**. Um die REST-API aufzurufen, müssen Sie [programmgesteuert ein SAS-Token generieren](/rest/api/eventhub/generate-sas-token), das im **Autorisierungsheader** der Anforderung verwendet werden kann.  

Das resultierende Token weist das folgende Format auf: 

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

Der eigentliche Vorgang umfasst die gleichen sechs Hauptschritte:  

1.  Berechnung des Ablaufs als [UNIX-Zeit (Epoch-Format)](https://en.wikipedia.org/wiki/Unix_time) – verstrichene Sekunden seit 00:00:00 Uhr UTC am 1. Januar 1970
2.  Formatieren der **ResourceUrl** (für die Ressource, auf die Sie zugreifen möchten, d. h. `'https://\<namespace\>.servicebus.windows.net/\<hubName\>'`), sodass sie mit Prozentzeichen und Kleinbuchstaben codiert ist
3.  Vorbereiten der Zeichenfolge **StringToSign**, die sich aus `'\<**UrlEncodedResourceUrl**\>\n\<**ExpiryEpoch**\>'` zusammensetzt
4.  Berechnen (und Base64-Codieren) der **Signatur** mithilfe des **HMAC-SHA256**-Werts von **StringToSign** mit dem Teil **Key** der **Verbindungszeichenfolge** (für die jeweilige **Autorisierungsregel**)
5.  Formatieren der Base64-codierten **Signatur**, sodass sie mit Prozentzeichen codiert ist
6.  Erstellen des **Tokens** im erwarteten Format mithilfe der Werte **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName** und **UrlEncodedResourceUrl**

Eine ausführlichere Übersicht über die **Shared Access Signature** und ihre Verwendung durch **Azure Service Bus** und **Notification Hubs** finden Sie in der [Dokumentation zu Azure Service Bus](../service-bus-messaging/service-bus-sas.md).

Im Rahmen dieses Swift-Beispiels verwenden Sie die Open-Source-Bibliothek **CommonCrypto** von Apple für den Hashwert der Signatur. Da es sich um eine C-Bibliothek handelt, kann nicht direkt in Swift darauf zugegriffen werden. Sie können sie jedoch über einen Bridgingheader zur Verfügung stellen. So fügen Sie den Bridgingheader hinzu und konfigurieren ihn

1. Wechseln Sie in **Xcode** zu  **File** (Datei), **New** (Neu), **File**  (Datei), und wählen Sie **Header File** (Headerdatei) aus. Geben Sie dabei den Namen *BridgingHeader.h* an.
2. Bearbeiten Sie die zu importierende Datei **CommonHMAC**.

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

3. Aktualisieren Sie die **Buildeinstellungen** des Ziels so, dass sie auf den Bridgingheader verweisen. Öffnen Sie die Registerkarte  **Building Settings**  (Erstellungsinstellungen), und scrollen Sie nach unten zum Abschnitt  **Swift Compiler** . Stellen Sie sicher, dass die Option  **Install Objective-C Compatibility Header**  (Objective-C-Kompatibilitätsheader installieren) auf  **Yes** (Ja) festgelegt ist, und geben Sie in der Option **Objective-C bridging Header**  (Objective-C-Brigdeheader) den Dateipfad zu unserem Bridgingheader ein: `'\<ProjectName\>/BridgingHeader.h'`. Wenn Sie diese Optionen nicht finden können, vergewissern Sie sich, dass die Ansicht **All** (Alle) ausgewählt ist und nicht **Basic** (Grundlegend) oder **Customized** (Angepasst).
    
   Es sind viele Open-Source-Wrapperbibliotheken von Drittanbietern verfügbar, die die Verwendung von **CommonCrypto** vereinfachen können. Dies geht jedoch über den Rahmen dieses Artikels hinaus.

4. Fügen Sie im Ordner **Utilities** eine neue Swift-Datei mit dem Namen **TokenUtility.swift** hinzu, und fügen Sie dort den folgenden Code ein:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>
    
        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)
        
            return tokenData
        }
    
        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)
        
            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()
        
            return result as Data
        }
    
        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)
        
            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }
        
            for i in 0..<length {
                let currentChar = sourceUtf8![i]
            
                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }
        
            return encodedString
        }
    }
   ```
    
    Dieses Hilfsprogramm kapselt die Logik für das Generieren des **SAS-Tokens**. Die *getSasToken*-Funktion orchestriert die allgemeinen Schritte zum Vorbereiten des Tokens, wie zuvor beschrieben, und wird in den späteren Schritten dieses Tutorials vom Installationsdienst aufgerufen. Die beiden anderen Funktionen werden von der *getSasToken*-Funktion sowie *sha256HMac* zum Berechnen der Signatur und der *urlEncodedString*-Funktion zum Codieren der entsprechenden URL-Zeichenfolge aufgerufen. Die *urlEncodedString*-Funktion war erforderlich, da die erforderliche Ausgabe nicht mithilfe der integrierten *addingPercentEncoding*-Funktion erreicht werden konnte. Das [Azure Storage SDK für iOS](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) diente als ausgezeichnetes Beispiel für das Umsetzen dieser Vorgänge, wenn auch in Objective-C. Weitere Informationen zu **Azure Service Bus-SAS-Token** finden Sie in der [Dokumentation zu Azure Service Bus](../service-bus-messaging/service-bus-sas.md). 

### <a name="verify-the-sas-token"></a>Überprüfen des SAS-Tokens
Bevor Sie den Installationsdienst im Client implementieren, können Sie sich mithilfe eines HTTP-Hilfsprogramms Ihrer Wahl vergewissern, dass unsere App die **SAS-Token** ordnungsgemäß generiert. Im Rahmen dieses Beitrags verwenden wir **Postman**.

Notieren Sie sich mithilfe einer entsprechend platzierten print-Anweisung oder eines Breakpoints die von der App generierten Werte *installationId* und *token*. 

Gehen Sie zum Aufrufen der *Installations*-API folgendermaßen vor:

1. Öffnen Sie in **Postman** eine neue Registerkarte.
2. Legen Sie die Anforderung auf **GET** und die folgende Adresse fest:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

3. Konfigurieren Sie die Anforderungsheader wie folgt:
    
   | Schlüssel           | Wert            |
   | ------------- | ---------------- |
   | Content-Typ  | Anwendung/json |
   | Autorisierung | \<sasToken\>     |
   | x-ms-version  | 2015-01          |

4. Klicken Sie auf die Schaltfläche **Code** (rechts oben unter der Schaltfläche **Save** (Speichern)). Die Anforderung sollte dem nachstehenden Beispiel ähneln:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

5. Klicken Sie auf die Schaltfläche **Send** (Senden).

Für die angegebene *installationId* ist bisher keine Registrierung vorhanden, sie sollte jedoch zur Antwort **404 Nicht gefunden** führen und nicht zu **401 Nicht autorisiert**. Dieses Ergebnis sollte sicherstellen, dass das **SAS-Token** akzeptiert wurde.

### <a name="implement-the-installation-service-class"></a>Implementieren der Installationsdienstklasse
Als Nächstes implementieren Sie unseren einfachen Wrapper um die [Installations-REST-API](/rest/api/notificationhubs/create-overwrite-installation).  

Fügen Sie eine neue Swift-Datei im Ordner **Services** mit dem Namen **NotificationRegistrationService.swift** hinzu, und fügen Sie in diese Datei folgenden Code ein:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil
    
    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }
    
    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {
        
        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)
        
        if let tags = tags {
            deviceInstallation.tags = tags
        }
        
        if let templates = templates {
            deviceInstallation.templates = templates
        }
        
        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"
            
            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"
            
            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }
            
            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)
            
            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }
    
    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }
    
    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }
    
    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```
 
Die erforderlichen Informationen werden im Rahmen der Initialisierung angegeben. Tags und Vorlagen werden optional an die *register*-Funktion übergeben und bilden einen Teil der JSON-Nutzlast für die **Geräteinstallation**.  

Die *register*-Funktion führt Aufrufe an die anderen privaten Funktionen aus, um die Anforderung vorzubereiten. Nach dem Empfang einer Antwort erfolgt ein Abschlussaufruf, der angibt, ob die Registrierung erfolgreich war.  

Der Anforderungsendpunkt wird während der Initialisierung über die *getBaseAddress*-Funktion mit den **Notification Hubs**-Parametern **namespace** und **name** erstellt.  

Die *getSasToken*-Funktion überprüft, ob das derzeit gespeicherte Token gültig ist. Andernfalls ruft sie **TokenUtility** auf, um ein neues zu erstellen und zu speichern, bevor sie einen Wert zurückgibt. 

Schließlich konvertiert *encodeToJson* die jeweilige Modellobjekte in JSON, damit sie im Hauptteil der Anforderung verwendet werden können.

### <a name="invoke-the-notification-hubs-rest-api"></a>Aufrufen der Notification Hubs-REST-API
Der letzte Schritt besteht im Aktualisieren von **AppDelegate** für die Verwendung von **NotifiationRegistrationService** zum Registrieren unseres **NotificationHub**. 

1. Öffnen Sie **AppDelegate.swift**, und fügen Sie eine Variable auf Klassenebene hinzu, in der ein Verweis auf **NoficiationRegistrationService** gespeichert wird:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

2. Aktualisieren Sie in derselben die *didRegisterForRemoteNotificationsWithDeviceToken*-Funktion zum Initialisieren von **NotificationRegistrationService** mit den erforderlichen Parametern, und rufen Sie dann die *register*-Funktion auf.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)
    
        // Call register passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Einfach ausgedrückt aktualisieren Sie mit einer Reihe von print-Anweisungen das Ausgabefenster mit dem Ergebnis des *register*-Vorgangs. 

3. Erstellen Sie nun die App, und führen Sie sie aus (auf einem physischen Gerät). Im Ausgabefenster sollte **Registered** angezeigt werden.

## <a name="test-the-solution"></a>Testen der Lösung
In dieser Phase ist unsere App bei **NotificationHub** registriert und kann Pushbenachrichtigungen empfangen. Beenden Sie in **Xcode** den Debugger, und schließen Sie die App (sofern Sie derzeit ausgeführt wird). Als Nächstes überprüfen Sie, ob die Details der **Geräteinstallation** den Erwartungen entsprechen und ob unsere App nun tatsächlich Pushbenachrichtigungen empfangen kann.  

### <a name="verify-the-device-installation"></a>Überprüfen der Geräteinstallation
Sie können jetzt zum [Überprüfen des SAS-Tokens](#verify-the-sas-token) die gleichen Anforderungen ausführen wie zuvor mit **Postman**. Sofern das **SAS-Token** noch nicht abgelaufen ist, sollte die Antwort jetzt die angegebenen Installationsdetails enthalten, z. B. die Vorlagen und Tags.  

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Senden einer Testbenachrichtigung (Azure-Portal)
Die schnellste Möglichkeit zum Testen, ob Sie nun Benachrichtigungen empfangen können, besteht im Navigieren zum **Benachrichtigungshub** im **Azure-Portal**.

1. Navigieren Sie im **Azure-Portal** zur Registerkarte **Übersicht** Ihres **Benachrichtigungshubs**.
2. Klicken Sie auf **Testsendevorgang** über der Zusammenfassung **Essentials** (links oben)

    ![Schaltfläche „Testsendevorgang“ über der Zusammenfassung „Zusammenfassung“ unter „Notification Hubs“](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)
3. Wählen Sie in der Liste der **Plattformen** die Option **Benutzerdefinierte Vorlage** aus.
4. Geben Sie **12345** für **An Tagausdruck senden** ein (dieses Tag haben Sie in unserer Installation angegeben).
5. Bearbeiten Sie optional die **Meldung** in der JSON-Nutzlast.
    
    ![Testsendevorgang in Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)
6. Wenn Sie auf **Senden** klicken, sollte das Portal angeben, ob die Benachrichtigung erfolgreich an das Gerät gesendet wurde.

    ![Ergebnis des Testsendevorgangs in Notification Hubs](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Es sollte auch eine Benachrichtigung in der **Mitteilungszentrale** auf Ihrem Gerät angezeigt werden, sofern die App nicht im Vordergrund ausgeführt wird. Durch Tippen auf die Benachrichtigung sollte die App mit der angezeigten Warnung geöffnet werden.

    ![Beispiel einer empfangenen Benachrichtigung](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-postman"></a>Senden einer Testbenachrichtigung (Postman)
Sie können Benachrichtigungen auch per **Postman** über die entsprechende [REST-API](/rest/api/notificationhubs/) senden. Dies ist möglicherweise eine bequemere Testmethode. 

1. Öffnen Sie in **Postman** eine neue Registerkarte.
2. Legen Sie die Anforderung auf **POST** fest, und geben Sie die folgende Adresse ein:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

3. Konfigurieren Sie die Anforderungsheader wie folgt:
    
   | Schlüssel                            | Wert                          |
   | ------------------------------ | ------------------------------ |
   | Content-Typ                   | application/json;charset=utf-8 |
   | Autorisierung                  | \<sasToken\>                   |
   | ServiceBusNotification-Format  | Vorlage                       |
   | `Tags`                           | "12345"                        |

4. Konfigurieren Sie den **Hauptteil** der Anforderung so, dass **RAW – JSON (application.json)** mit der folgenden JSON-Nutzlast verwendet wird:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

5. Klicken Sie auf die Schaltfläche **Code** (rechts oben unter der Schaltfläche **Save** (Speichern)). Die Anforderung sollte dem nachstehenden Beispiel ähneln:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

5. Klicken Sie auf die Schaltfläche **Send** (Senden).

Sie sollten einen Erfolgsstatuscode und die Benachrichtigung auf dem Clientgerät empfangen.

## <a name="next-steps"></a>Nächste Schritte
Sie verfügen nun über eine einfache iOS-Swift-App, die über die [REST-API](/rest/api/notificationhubs/) mit einem **Benachrichtigungshub** verbunden ist und Benachrichtigungen senden und empfangen kann. Weitere Informationen finden Sie in den folgenden Artikeln: 

- [Übersicht über Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [REST-APIs für Notification Hubs](/rest/api/notificationhubs/)
- [Notification Hubs-SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs-SDK auf GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrieren bei einem Anwendungs-Back-End](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Registrierungsverwaltung](notification-hubs-push-notification-registration-management.md)
- [Arbeiten mit Tags](notification-hubs-tags-segment-push-message.md) 
- [Arbeiten mit benutzerdefinierten Vorlagen](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus-Zugriffssteuerung mit Shared Access Signatures](../service-bus-messaging/service-bus-sas.md)
- [Programmgesteuertes Generieren von SAS-Token](/rest/api/eventhub/generate-sas-token)
- [Apple-Sicherheit: allgemeine Kryptografie](https://developer.apple.com/security/)
- [UNIX-ZEIT (Epoch)](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
