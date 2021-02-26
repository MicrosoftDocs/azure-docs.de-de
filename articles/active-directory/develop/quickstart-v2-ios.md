---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer iOS- oder macOS-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie aus einer iOS- oder macOS-App Benutzer angemeldet werden und ein Zugriffstoken aus der Microsoft Identity Platform abgerufen und die Microsoft Graph-API aufgerufen wird.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: d2c5af6aeccfbae0851513ff575bde3c39e3ca5f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103786"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Schnellstart: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer iOS- oder macOS-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine native iOS- oder macOS-Anwendung Benutzer anmelden und ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen.

Die Schnellstartanleitung gilt für iOS- und macOS-Apps. Einige Schritte sind nur für iOS-Apps erforderlich und entsprechend gekennzeichnet.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* XCode 10 oder höher
* iOS 10 oder höher
* macOS 10.12 oder höher

## <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Registrieren und Herunterladen Ihrer Schnellstart-App
> Die Schnellstartanwendung kann auf zwei Arten gestartet werden:
> * [Express] [Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Manuell] [Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>Option 1: Registrieren und automatisches Konfigurieren Ihrer App und anschließendes Herunterladen des Codebeispiels
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> So registrieren Sie Ihre App:
> 1. Navigieren Sie zur Umgebung des Schnellstarts <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs" target="_blank">Azure-Portal – App-Registrierungen</a>.
> 1. Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Registrieren** aus.
> 1. Befolgen Sie die Anweisungen, um Ihre neue Anwendung mit nur einem Klick herunterzuladen und automatisch zu konfigurieren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Registrieren und manuelles Konfigurieren Ihrer Anwendung und des Codebeispiels
>
> #### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren
> Führen Sie die folgenden Schritte aus, um Ihre Anwendung zu registrieren und Ihrer Projektmappe manuell die Registrierungsinformationen Ihrer App hinzuzufügen:
>
> 1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
> 1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
> 1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.    
> 1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
> 1. Geben Sie einen **Namen** für Ihre Anwendung ein. Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
> 1. Wählen Sie **Registrieren**.
> 1. Wählen Sie unter **Verwalten** die Optionen **Authentifizierung** > **Plattform hinzufügen** > **iOS** aus.
> 1. Geben Sie die **Paket-ID** für Ihre Anwendung ein. Die Paket-ID ist eine eindeutige Zeichenfolge, die Ihre Anwendung eindeutig identifiziert (z. B. `com.<yourname>.identitysample.MSALMacOS`). Notieren Sie sich den verwendeten Wert. Die iOS-Konfiguration ist auch bei macOS-Anwendungen anwendbar.
> 1. Wählen Sie **Konfigurieren** aus, und speichern Sie die Details der **MSAL-Konfiguration** zur späteren Verwendung in diesem Schnellstart.
> 1. Wählen Sie **Fertig** aus.

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>Schritt 1: Konfigurieren der Anwendung
> Fügen Sie einen mit dem Authentifizierungsbroker kompatiblen **Umleitungs-URI** hinzu, damit das Codebeispiel für diese Schnellstartanleitung funktioniert.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Diese Änderung für mich vornehmen]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Bereits konfiguriert](media/quickstart-v2-ios/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.
>
> #### <a name="step-2-download-the-sample-project"></a>Schritt 2: Herunterladen des Beispielprojekts
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Herunterladen des Codebeispiels für iOS]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Herunterladen des Codebeispiels für macOS]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>Schritt 2: Herunterladen des Beispielprojekts
>
> - [Herunterladen des Codebeispiels für iOS](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Herunterladen des Codebeispiels für macOS](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>Schritt 3: Installieren von Abhängigkeiten

Navigieren Sie in einem Terminalfenster zu dem Ordner, der das heruntergeladene Codebeispiel enthält, und führen Sie `pod install` zum Installieren der MSAL-Bibliothek aus.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>Schritt 4: Ihre App ist konfiguriert und betriebsbereit
> Wir haben das Projekt mit Werten Ihrer App-Eigenschaften konfiguriert. Es ist nun ausführungsbereit.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>Schritt 4: Konfigurieren des Projekts
> Wenn Sie weiter oben die erste Option ausgewählt haben, können Sie diese Schritte überspringen.
> 1. Extrahieren Sie die ZIP-Datei, und öffnen Sie das Projekt in XCode.
> 1. Bearbeiten Sie **ViewController.swift**, und ersetzen Sie die Zeile, die mit „let kClientID“ beginnt, durch den folgenden Codeausschnitt. Sie müssen den Wert für `kClientID` durch die Client-ID ersetzen, die Sie zuvor im Schnellstart beim Registrieren Ihrer App im Portal gespeichert haben:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Ersetzen Sie beim Erstellen einer App für [nationale Azure AD-Clouds](/graph/deployments#app-registration-and-token-service-root-endpoints) die Zeile, die mit „let kGraphEndpoint“ und „let kAuthority“ beginnt, durch richtige Endpunkte. Verwenden Sie für globalen Zugriff die Standardwerte:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. Informationen zu anderen Endpunkten finden Sie [hier](/graph/deployments#app-registration-and-token-service-root-endpoints). Verwenden Sie beispielsweise zum Ausführen der Schnellstartanleitung mit Azure AD Deutschland Folgendes:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Öffnen Sie die Projekteinstellungen. Geben Sie im Abschnitt **Identität** den Wert für **Bundle Identifier** (Paket-ID) ein, den Sie im Portal angegeben haben.
> 1. Klicken Sie mit der rechten Maustaste auf **Info.plist**, und wählen Sie **Öffnen als** > **Quellcode** aus.
> 1. Ersetzen Sie unter dem Stammknoten „dict“ `Enter_the_bundle_Id_Here` durch den Wert für ***Bundle Id*** (Paket-ID), den Sie im Portal verwendet haben.
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Erstellen Sie die App, und führen Sie die App aus!

## <a name="more-information"></a>Weitere Informationen

Lesen Sie diese Abschnitte, um mehr über diesen Schnellstart zu erfahren.

### <a name="get-msal"></a>Abrufen von MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) ist die Bibliothek zum Anmelden von Benutzern und zum Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet werden. Sie können Ihrer Anwendung MSAL mithilfe des folgenden Vorgangs hinzufügen:

```
$ vi Podfile

```
Fügen Sie der Podfile-Datei den folgenden Code (mit dem Ziel Ihres Projekts) hinzu:

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

Ausführen des CocoaPods-Installationsbefehls:

```pod install```

### <a name="initialize-msal"></a>MSAL initialisieren

Sie können den Verweis auf MSAL hinzufügen, indem Sie den folgenden Code hinzufügen:

```swift
import MSAL
```

Initialisieren Sie MSAL anschließend mit dem folgenden Code:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Hierbei gilt: | BESCHREIBUNG |
> |---------|---------|
> | `clientId` | Die Anwendungs-ID der in *portal.azure.com* registrierten Anwendung. |
> | `authority` | Microsoft Identity Platform. In den meisten Fällen ist dies `https://login.microsoftonline.com/common`. |
> | `redirectUri` | Der Umleitungs-URI der Anwendung. Sie können „nil“ übergeben, um den Standardwert zu verwenden, oder Ihren benutzerdefinierten Umleitungs-URI angeben. |

### <a name="for-ios-only-additional-app-requirements"></a>Nur iOS: zusätzliche App-Anforderungen

Ihre App muss auch Folgendes in `AppDelegate` enthalten. Dadurch kann das MSAL SDK die Tokenantwort der Authentifizierungsbroker-App verarbeiten, wenn Sie die Authentifizierung ausführen.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Wenn Sie unter iOS 13 oder höher `UISceneDelegate` anstelle von `UIApplicationDelegate` übernehmen, platzieren Sie diesen Code stattdessen im `scene:openURLContexts:`-Rückruf (siehe [Dokumentation von Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Wenn Sie sowohl UISceneDelegate als auch UIApplicationDelegate für die Kompatibilität mit älteren iOS-Versionen unterstützen, muss der MSAL-Rückruf für beide Optionen zur Verfügung gestellt werden.

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

Ihre App muss neben `CFBundleURLTypes` über einen `LSApplicationQueriesSchemes`-Eintrag in ***Info.plist*** verfügen. Im Beispiel ist dies enthalten.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Anmelden von Benutzern und Anfordern von Token

MSAL verfügt über zwei Methoden, die zum Abrufen von Token verwendet werden: `acquireToken` und `acquireTokenSilent`.

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: Interaktives Abrufen eines Tokens

In einigen Situationen müssen Benutzer mit Microsoft Identity Platform interagieren. In diesen Fällen muss der Endbenutzer möglicherweise sein Konto auswählen, seine Anmeldeinformationen eingeben oder den Berechtigungen Ihrer App zustimmen. Beispiel:

* Erstmaliges Anmelden von Benutzern bei der Anwendung.
* Wenn ein Benutzer sein Kennwort zurücksetzt, muss er seine Anmeldeinformationen eingeben.
* Wenn Ihre Anwendung zum ersten Mal Zugriff auf eine Ressource anfordert
* Wenn MFA oder andere Richtlinien für bedingten Zugriff erforderlich sind

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Hierbei gilt:| BESCHREIBUNG |
> |---------|---------|
> | `scopes` | Enthält die angeforderten Bereiche (`[ "user.read" ]` für Microsoft Graph oder `[ "<Application ID URL>/scope" ]` für benutzerdefinierte Web-APIs (`api://<Application ID>/access_as_user`)) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: Abrufen eines Zugriffstokens im Hintergrund

Apps sollten nicht verlangen, dass sich die Benutzer jedes Mal anmelden, wenn sie ein Token anfordern. Wenn sich der Benutzer bereits angemeldet hat, haben Apps durch diese Methode die Möglichkeit, Token im Hintergrund anzufordern.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Hierbei gilt: | BESCHREIBUNG |
> |---------|---------|
> | `scopes` | Enthält die angeforderten Bereiche (`[ "user.read" ]` für Microsoft Graph oder `[ "<Application ID URL>/scope" ]` für benutzerdefinierte Web-APIs (`api://<Application ID>/access_as_user`)) |
> | `account` | Das Konto, für das ein Token angefordert wird. In dieser Schnellstartanleitung wird eine einzelne Kontoanwendung angezeigt. Wenn Sie eine App mit mehreren Konten erstellen möchten, müssen Sie die Logik zum Identifizieren des gewünschten Kontos für Tokenanforderungen mit `accountsFromDeviceForParameters:completionBlock:` definieren und den richtigen `accountIdentifier` übergeben. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem Schritt-für-Schritt-Tutorial fort, in dem Sie eine iOS- oder macOS-App erstellen, aus der ein Zugriffstoken von der Microsoft Identity Platform abgerufen wird, das dann in der App verwendet wird, um die Microsoft Graph-API aufzurufen.

> [!div class="nextstepaction"]
> [Tutorial: Anmelden von Benutzern und Aufrufen von Microsoft Graph aus einer iOS- oder macOS-App](tutorial-v2-ios.md)
