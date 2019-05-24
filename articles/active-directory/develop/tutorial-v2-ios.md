---
title: 'Erste Schritte mit iOS: Microsoft Identity Platform | Azure'
description: So können iOS-Anwendungen (Swift) über Microsoft Identity Platform eine API aufrufen, für die Zugriffstoken erforderlich sind
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/26/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 780da9359aaf645abc9b685fa9d90bbea9199759
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962162"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-app"></a>Anmelden von Benutzern und Aufrufen von Microsoft Graph in einer iOS-App

In diesem Tutorial wird beschrieben, wie Sie eine iOS-Anwendung erstellen und diese in Microsoft Identity Platform integrieren. Mit dieser App wird ein Benutzer angemeldet, ein Zugriffstoken zum Aufrufen der Microsoft Graph-API abgerufen und eine einfache Anforderung an die Microsoft Graph-API gesendet.  

Am Ende dieses Leitfadens akzeptiert Ihre Anwendung Anmeldungen von persönlichen Microsoft-Konten (z.B. outlook.com, live.com u.a.) sowie von Geschäfts,- Schul- oder Unikonten von allen Unternehmen oder Organisationen, die Azure Active Directory nutzen.

## <a name="how-this-guide-works"></a>Funktionsweise dieser Anleitung

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Mit der App in diesem Beispiel werden Benutzer angemeldet und Daten in deren Namen abgerufen.  Auf diese Daten wird über eine geschützte API (hier: Microsoft Graph-API) zugegriffen, für die eine Autorisierung erforderlich ist und die über Microsoft Identity Platform geschützt ist.

Dies gilt insbesondere in folgenden Fällen:

* Die App meldet den Benutzer entweder über einen Browser oder über Microsoft Authenticator an.
* Der Endbenutzer akzeptiert die von Ihrer Anwendung angeforderten Berechtigungen. 
* Ihre App stellt ein Zugriffstoken für die Microsoft Graph-API aus.
* Das Zugriffstoken ist in der HTTP-Anforderung an die Web-API enthalten.
* Die Microsoft Graph-Antwort wird verarbeitet.

Dieses Beispiel verwendet Microsoft Authentication Library (MSAL) zum Implementieren der Authentifizierung. Mit MSAL werden Token automatisch verlängert, das einmalige Anmelden zwischen anderen Apps auf dem Gerät wird bereitgestellt, und die Konten werden verwaltet.

## <a name="prerequisites"></a>Voraussetzungen

- Für das Beispiel, das in diesem Leitfaden erstellt wird, ist XCode Version 10.x erforderlich. Können Sie XCode von der [iTunes-Website](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode-Download-URL") herunterladen.
- Microsoft Authentication Library ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Sie können den Abhängigkeitsmanager verwenden oder manuell hinzufügen. In einem Abschnitt weiter unten finden Sie [weitere Informationen](#add-msal). 

## <a name="set-up-your-project"></a>Einrichten des Projekts

In diesem Tutorial wird ein neues Projekt erstellt. Wenn Sie stattdessen das abgeschlossene Tutorial herunterladen möchten, [laden Sie den Code](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) herunter.

### <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

1. Starten Sie Xcode, und wählen Sie **Create a new Xcode project** (Neues Xcode-Projekt erstellen) aus.
2. Wählen Sie **iOS > Single view Application** (iOS > Einzelansicht-App) und dann **Next** (Weiter) aus.
3. Geben Sie einen Produktnamen ein, und wählen Sie **Next** (Weiter) aus.
4. Wählen Sie einen Ordner für das Erstellen Ihrer App aus, und klicken Sie auf *Erstellen*.

## <a name="register-your-application"></a>Anwendung registrieren 

Es gibt zwei Methoden für die Registrierung Ihrer Anwendung. Diese werden in den beiden folgenden Abschnitten beschrieben.

### <a name="register-your-app"></a>Registrieren der App

1. Rufen Sie das [Azure-Portal](https://aka.ms/MobileAppReg) auf, und wählen Sie `New registration` aus. 
2. Geben Sie einen **Namen** für Ihre App ein, und wählen Sie `Register`. **Legen Sie zu diesem Zeitpunkt keinen Umleitungs-URI fest**. 
3. Wechseln Sie im Abschnitt `Manage` zu `Authentication` > `Add a platform` > `iOS`.
    - Geben Sie die Bündel-ID Ihres Projekts ein. Wenn Sie den Code heruntergeladen haben, lautet diese `com.microsoft.identitysample.MSALiOS`.
4. Wählen Sie `Configure`, und speichern Sie `MSAL Configuration` zur späteren Verwendung. 

## <a name="add-msal"></a>Hinzufügen von MSAL

### <a name="get-msal"></a>Abrufen von MSAL

#### <a name="cocoapods"></a>CocoaPods

Sie können [CocoaPods](https://cocoapods.org/) zum Installieren von `MSAL` verwenden, indem Sie es unter dem folgenden Ziel Ihrer `Podfile`-Datei hinzufügen:

```
use_frameworks!

target '<your-target-here>' do
   pod 'MSAL', '~> 0.4.0'
end
```

#### <a name="carthage"></a>Carthage

Sie können [Carthage](https://github.com/Carthage/Carthage) zum Installieren von `MSAL` verwenden, indem Sie es Ihrer `Cartfile`-Datei hinzufügen: 

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

#### <a name="manually"></a>Manuell

Sie können auch ein Git-Submodul verwenden oder das neueste Release als Framework in Ihrer Anwendung verwenden.

### <a name="add-your-app-registration"></a>Hinzufügen der App-Registrierung

Als nächstes fügen Sie dem Code die App-Registrierung hinzu. Fügen Sie Ihre ***Client-/Anwendungs-ID*** zu `ViewController.swift` hinzu:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/"
let kScopes: [String] = ["https://graph.microsoft.com/user.read"]
let kAuthority = "https://login.microsoftonline.com/common"
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
```

### <a name="configure-url-schemes"></a>Konfigurieren von URL-Schemas

Registrieren Sie `CFBundleURLSchemes`, um einen Rückruf zu ermöglichen, sodass der Benutzer nach der Anmeldung zurück zur App geleitet werden kann.

`LSApplicationQueriesSchemes` ermöglicht die Verwendung von Microsoft Authenticator (falls verfügbar) mit Ihrer App. 

Öffnen Sie dazu `Info.plist` als Quellcode, und fügen Sie folgenden Code hinzu, wobei Sie ***BUNDLE_ID*** durch den im Azure-Portal konfigurierten Wert ersetzen.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauth</string>
    <string>msauthv2</string>
</array>
```

### <a name="import-msal"></a>Importieren von MSAL

Importieren Sie das MSAL-Framework in die Dateien `ViewController.swift` und `AppDelegate.swift`:

```swift
import MSAL
```

## <a name="create-your-apps-ui"></a>Erstellen der Benutzeroberfläche Ihrer App

Für dieses Tutorial müssen Sie Folgendes erstellen:

- Schaltfläche zum Aufrufen der Graph-API
- Schaltfläche zum Abmelden
- TextView-Protokollierung

Definieren Sie in `ViewController.swift` Eigenschaften und `initUI()` wie folgt:

```swift

var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
        callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
        callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        
        // Add sign out button
        signOutButton = UIButton()
        signOutButton.translatesAutoresizingMaskIntoConstraints = false
        signOutButton.setTitle("Sign Out", for: .normal)
        signOutButton.setTitleColor(.blue, for: .normal)
        signOutButton.setTitleColor(.gray, for: .disabled)
        signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
        self.view.addSubview(signOutButton)
        
        signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
        signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
        signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

Als Nächstes fügen Sie folgenden Code zu `viewDidLoad()` von „ViewController.swift“ hinzu:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.loggingText.text = "Unable to create Application Context \(error)"
        }
    }
```

## <a name="use-msal"></a>Verwendung von MSAL

### <a name="initialize-msal"></a>MSAL initialisieren

Zuerst müssen Sie für Ihre Anwendung ein `MSALPublicClientApplication`-Element mit einer Instanz von `MSALPublicClientConfiguration` erstellen:

```swift
    func initMSAL() throws {
        
        guard let authorityURL = URL(string: kAuthority) else {
            self.loggingText.text = "Unable to create authority URL"
            return
        }
        
        let authority = try MSALAADAuthority(url: authorityURL)
        
        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
    }
```

### <a name="handle-the-callback"></a>Verarbeiten des Rückrufs

Zur Verarbeitung des Rückrufs nach der Anmeldung fügen Sie `MSALPublicClientApplication.handleMSALResponse` in `appDelegate` hinzu:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

#### <a name="acquire-tokens"></a>Abrufen von Token

Jetzt können wir die Verarbeitungslogik der Benutzeroberfläche der Anwendung implementieren und Token interaktiv über MSAL beziehen. 

MSAL macht zwei primäre Methoden zum Abrufen von Token verfügbar: `acquireTokenSilently` und `acquireTokenInteractively`.  

`acquireTokenSilently()` versucht, einen Benutzer anzumelden und Token ohne Benutzerinteraktion abzurufen, wenn ein Konto vorhanden ist.

`acquireTokenInteractively` zeigt die Benutzeroberfläche immer an, wenn versucht wird, den Benutzer anzumelden und Token abzurufen. Möglicherweise werden jedoch Sitzungscookies im Browser oder in einem Konto in Microsoft Authenticator verwendet, um eine interaktive SSO-Umgebung bereitzustellen. 

```swift
    @objc func callGraphAPI(_ sender: UIButton) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Interaktives Abrufen eines Tokens

Zum erstmaligen Abrufen eines Tokens müssen Sie ein `MSALInteractiveTokenParameters`-Element erstellen und `acquireToken` aufrufen.

1. Erstellen Sie `MSALInteractiveTokenParameters` mit Bereichen.
2. Rufen Sie `acquireToken` mit den erstellten Parametern auf.
3. Behandeln Sie Fehler entsprechend. Weitere Informationen finden Sie im [iOS-Leitfaden zur Fehlerbehandlung](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Error-Handling).
4. Verarbeiten Sie die erfolgreiche Situation. 

```swift
    func acquireTokenInteractively() {
   
        guard let applicationContext = self.applicationContext else { return }
     // #1    
        let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
     // #2        
        applicationContext.acquireToken(with: parameters) { (result, error) in
     // #3            
            if let error = error {
                self.updateLogging(text: "Could not acquire token: \(error)")
                return
            }
            guard let result = result else {   
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
     // #4            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```



#### <a name="get-a-token-silently"></a>Automatisches Abrufen eines Tokens

Zum automatischen Abrufen eines aktualisiertes Tokens im Hintergrund müssen Sie ein `MSALSilentTokenParameters`-Element erstellen und `acquireTokenSilent` aufrufen:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
                if (nsError.domain == MSALErrorDomain) {
                    if (nsError.code == MSALError.interactionRequired.rawValue) {
                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }
                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }
            
            guard let result = result else {
                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }
            
            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Aufrufen der Microsoft Graph-API 

Sobald Sie ein Token über `self.accessToken` erhalten haben, kann Ihre Anwendung diesen Wert im HTTP-Header verwenden, um eine autorisierte Anforderung an Microsoft Graph zu stellen:

| Headerschlüssel    | value                 |
| ------------- | --------------------- |
| Autorisierung | Bearer <access-token> |

Fügen Sie Folgendes zu `ViewController.swift` hinzu:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)
        
        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")
               
        URLSession.shared.dataTask(with: request) { data, response, error in
               
        if let error = error {
            self.updateLogging(text: "Couldn't get graph result: \(error)")
            return
        }
               
        guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
               
        self.updateLogging(text: "Couldn't deserialize result JSON")
            return
        }
               
        self.updateLogging(text: "Result from Graph: \(result))")
        
        }.resume()
    }
```

Weitere Informationen zur [Microsoft Graph-API](https://graph.microsoft.com)

### <a name="use-msal-for-sign-out"></a>Verwenden von MSAL zur Abmeldung

Als nächstes fügen wir unserer App Unterstützung für die Abmeldung hinzu. 

Es ist wichtig zu beachten, dass die Abmeldung mit MSAL alle bekannten Informationen über einen Benutzer aus dieser Anwendung entfernt. Der Benutzer verfügt dennoch über eine aktive Sitzung auf seinem Gerät. Wenn der Benutzer versucht, sich erneut anzumelden, werden möglicherweise Interaktionen angezeigt, er muss jedoch nicht unbedingt seine Anmeldeinformationen erneut eingeben, da die Gerätesitzung aktiv ist. 

Zum Hinzufügen der Abmeldefunktion kopieren Sie die folgende Methode in Ihre `ViewController.swift`-Datei:

```swift 
    @objc func signOut(_ sender: UIButton) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.loggingText.text = ""
            self.signOutButton.isEnabled = false
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Aktivieren der Zwischenspeicherung von Token

MSAL speichert die Token Ihrer App standardmäßig im iOS-Schlüsselbund zwischen. 

Um das Zwischenspeichern von Token zu aktivieren, rufen Sie in Ihren Xcode-Projekteinstellungen `Capabilities tab` > `Enable Keychain Sharing` auf, klicken Sie auf `Plus`, und geben Sie **com.microsoft.adalcache** ein.

### <a name="add-helper-methods"></a>Hinzufügen von Hilfsmethoden

Fügen Sie diese Hilfsmethoden hinzu, um das Beispiel zu vervollständigen:

``` swift
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }
    
    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
```

### <a name="multi-account-applications"></a>Anwendungen mit mehreren Konten

Diese App ist für ein Szenario mit einem einzelnen Konto konzipiert. MSAL unterstützt auch Szenarien mit mehreren Konten, die Apps müssen dann jedoch einige zusätzliche Schritte ausführen. Sie müssen eine Benutzeroberfläche erstellen, um dem Benutzer bei der Auswahl des Kontos zu helfen, das er für die einzelnen Aktionen verwenden möchte, die Token erfordern. Als Alternative kann Ihre Anwendung eine Heuristik implementieren, um das zu verwendende Konto über die `getAllAccounts(...)`-Methode auszuwählen.

## <a name="test-your-app"></a>Testen Ihrer App

### <a name="run-locally"></a>Lokales Ausführen

Wenn Sie den obigen Code verwendet haben, versuchen Sie, die App zu erstellen und auf einem Testgerät oder Emulator bereitzustellen. Sie sollten sich anmelden und Token für Azure AD oder persönliche Microsoft-Konten abrufen können! Nach der Anmeldung eines Benutzers zeigt diese App die vom Microsoft Graph `/me`-Endpunkt zurückgegebenen Daten an. 

Wenn irgendwelche Probleme auftreten, melden Sie gerne ein Problem mit diesem Dokument oder der MSAL-Bibliothek, und geben Sie uns Bescheid. 

### <a name="consent-to-your-app"></a>Zustimmung zur App

Wenn sich ein Benutzer zum ersten Mal bei Ihrer App anmeldet, wird er von Microsoft aufgefordert, den angeforderten Berechtigungen zuzustimmen.  Die meisten Benutzer können zustimmen, allerdings haben einige Azure AD-Mandanten die Zustimmung durch Benutzer deaktiviert, sodass Administratoren im Namen aller Benutzer zustimmen müssen.  Zur Unterstützung dieses Szenarios müssen Sie die Bereiche Ihrer App im Azure-Portal registrieren.

## <a name="help-and-support"></a>Hilfe und Support

Hatten Sie Probleme mit diesem Tutorial oder mit Microsoft I Platform? Siehe [Hilfe und Support](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)

