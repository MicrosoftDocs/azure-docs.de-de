---
title: Abrufen eines Tokens zum Aufrufen einer Web-API (mobile Apps) | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft. (Abrufen eines Tokens für die App).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6e6eda3d711710ea7450165ab02d7a260067bfcb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582552"
---
# <a name="get-a-token-for-a-mobile-app-that-calls-web-apis"></a>Abrufen eines Tokens für eine mobile App, die Web-APIs aufruft

Damit Ihre App geschützte Web-APIs aufrufen kann, benötigt sie ein Zugriffstoken. In diesem Artikel werden Sie durch den Prozess zum Abrufen eines Tokens mit der Microsoft Authentication Library (MSAL) geführt.

## <a name="define-a-scope"></a>Definieren eines Bereichs

Wenn Sie ein Token anfordern, definieren Sie einen Bereich. Der Gültigkeitsbereich bestimmt, auf welche Daten Ihre App zugreifen kann.

Die einfachste Vorgehensweise zum Definieren eines Bereichs besteht darin, den `App ID URI` der Web-API mit dem Bereich `.default` zu kombinieren. Diese Definition informiert Microsoft Identity Platform darüber, dass Ihre App alle im Portal festgelegten Bereiche benötigt.

### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

### <a name="ios"></a>iOS
```swift
let scopes = ["https://graph.microsoft.com/.default"]
```

### <a name="xamarin"></a>Xamarin
```csharp
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Abrufen von Token

### <a name="acquire-tokens-via-msal"></a>Abrufen von Token über MSAL

Mit der MSAL können Apps Token stillschweigend und interaktiv abrufen. Wenn Sie `AcquireTokenSilent()` oder `AcquireTokenInteractive()` aufrufen, gibt MSAL ein Zugriffstoken für die angeforderten Bereiche zurück. Die korrekte Vorgehensweise ist es, eine automatische Anforderung und anschließend eine interaktive Anforderung auszuführen.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed requests.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());
```

#### <a name="ios"></a>iOS

Versuchen Sie zunächst, ein Token automatisch abzurufen:

```objc

NSArray *scopes = @[@"https://graph.microsoft.com/.default"];
NSString *accountIdentifier = @"my.account.id";

MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        // Access token to call the web API
        NSString *accessToken = result.accessToken;
    }

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
        return;
    }
}];
```

```swift

let scopes = ["https://graph.microsoft.com/.default"]
let accountIdentifier = "my.account.id"

guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

    if (nsError.domain == MSALErrorDomain &&
        nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
         }
         return
     }

    // You'll want to get the account identifier to retrieve and reuse the account
    // for later acquireToken calls
    let accountIdentifier = authResult.account.identifier

    // Access token to call the web API
    let accessToken = authResult.accessToken
}
```

Wenn MSAL `MSALErrorInteractionRequired` zurückgibt, versuchen Sie, Token interaktiv abzurufen:

```objc
UIViewController *viewController = ...; // Pass a reference to the view controller that should be used when getting a token interactively
MSALWebviewParameters *webParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:viewController];
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:webParameters];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

```swift
let viewController = ... // Pass a reference to the view controller that should be used when getting a token interactively
let webviewParameters = MSALWebviewParameters(authPresentationViewController: viewController)
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: webviewParameters)
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

MSAL für iOS und macOS unterstützt verschiedene Modifizierer, um Token interaktiv oder automatisch abzurufen:
* [Allgemeine Parameter zum Abrufen eines Tokens](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALTokenParameters.html#/Configuration%20parameters)
* [Parameter zum Abrufen eines interaktiven Tokens](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALInteractiveTokenParameters.html#/Configuring%20MSALInteractiveTokenParameters)
* [Parameter zum automatischen Abrufen eines Tokens](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALSilentTokenParameters.html)

#### <a name="xamarin"></a>Xamarin

Das folgende Beispiel zeigt den minimal erforderlichen Code zum interaktiven Abrufen eines Codes. Das Beispiel verwendet Microsoft Graph zum Lesen des Profils des Benutzers.

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

#### <a name="mandatory-parameters-in-msalnet"></a>Erforderliche Parameter in MSAL.NET

`AcquireTokenInteractive` weist nur einen obligatorischen Parameter auf: `scopes`. Der `scopes`-Parameter enumeriert Zeichenfolgen , die die Bereiche definieren, für die ein Token erforderlich ist. Wenn das Token für Microsoft Graph bestimmt ist, finden Sie die erforderlichen Bereiche in der API-Referenz der einzelnen Microsoft Graph-APIs. Navigieren Sie in der Referenz zum Abschnitt „Berechtigungen“.

Um beispielsweise die [Kontakte des Benutzers aufzulisten](/graph/api/user-list-contacts), verwenden Sie den Bereich „User.Read“, „Contacts.Read“. Weitere Informationen finden Sie in der [Microsoft Graph-Referenz zu Berechtigungen](/graph/permissions-reference).

Unter Android können Sie beim Erstellen der App mithilfe von `PublicClientApplicationBuilder` eine übergeordnete Aktivität angeben. Wenn Sie die übergeordnete Aktivität nicht zu diesem Zeitpunkt festlegen, können Sie sie später mithilfe von `.WithParentActivityOrWindow` angeben, wie im folgenden Abschnitt zu sehen. Wenn Sie die übergeordnete Aktivität angeben, wird das Token nach der Interaktion wieder an diese übergeordnete Aktivität übergeben. Wenn Sie sie nicht angeben, wird durch den `.ExecuteAsync()`-Aufruf eine Ausnahme ausgelöst.

#### <a name="specific-optional-parameters-in-msalnet"></a>Spezifische optionale Parameter in MSAL.NET

In den folgenden Abschnitten werden die optionalen Parameter in MSAL.NET erläutert.

##### <a name="withprompt"></a>WithPrompt

Der `WithPrompt()`-Parameter steuert die Interaktivität mit dem Benutzer durch Angabe einer Eingabeaufforderung.

![Abbildung mit den Feldern in der Struktur der Eingabeaufforderung Diese konstanten Werte steuern die Interaktivität mit dem Benutzer, indem sie den Typ der Eingabeaufforderung definieren, der vom Parameter „WithPrompt ()“ angezeigt wird.](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Die Klasse definiert die folgenden Konstanten:

- `SelectAccount` zwingt den Sicherheitstokendienst (Security Token Service, STS), das Dialogfeld zur Kontoauswahl darzustellen. Das Dialogfeld enthält die Konten, für die der Benutzer über eine Sitzung verfügt. Sie können diese Option verwenden, wenn Sie dem Benutzer die Wahl zwischen verschiedenen Identitäten geben möchten. Diese Option bewirkt, dass MSAL `prompt=select_account` an den Identitätsanbieter sendet.

    Die Konstante `SelectAccount` stellt den Standardwert dar, und sie bietet in der Praxis die bestmögliche Benutzererfahrung auf der Grundlage der vorhandenen Informationen. Zu den verfügbaren Informationen können etwa das Konto, das Vorhandensein einer Sitzung für den Benutzer usw. zählen. Ändern Sie diesen Standardwert nicht ohne triftigen Grund.
- `Consent` ermöglicht es Ihnen, die Zustimmung des Benutzers anzufordern, auch wenn die Zustimmung bereits erteilt wurde. In diesem Fall sendet MSAL `prompt=consent` an den Identitätsanbieter.

    Es kann sinnvoll sein, die `Consent`-Konstante in Anwendungen mit einem starken Schwerpunkt auf der Sicherheit zu verwenden, wenn die Governance der Organisation es erfordert, den Benutzern bei jedem Verwenden der Anwendung ein Zustimmungsdialogfeld anzuzeigen.
- `ForceLogin` ermöglicht es dem Dienst, den Benutzer zur Eingabe von Anmeldeinformationen aufzufordern, auch wenn keine Eingabeaufforderung erforderlich ist.

    Diese Option kann nützlich sein, wenn beim Tokenerwerb ein Fehler auftritt, und Sie dem Benutzer die erneute Anmeldung ermöglichen wollen. In diesem Fall sendet MSAL `prompt=login` an den Identitätsanbieter. Die Verwendung dieser Option kann in sicherheitsorientierten Anwendungen sinnvoll sein, wenn die Governance der Organisation vorschreibt, dass sich Benutzer beim Zugriff auf bestimmte Teile einer Anwendung stets erneut anmelden müssen.
- `Never` ist nur für .NET 4.5 und Windows Runtime (WinRT) verfügbar. Mit dieser Konstante ergeht keine Aufforderung an den Benutzer, vielmehr wird versucht, das in der verborgenen eingebetteten Webansicht gespeicherte Cookie zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Webbrowsern mit MSAL.NET](./msal-net-web-browsers.md).

    Wenn bei dieser Option ein Fehler auftritt, löst `AcquireTokenInteractive` eine Ausnahme aus, um Sie zu informieren, dass ein Eingriff an der Benutzeroberfläche erforderlich ist. Verwenden Sie dann einen anderen `Prompt`-Parameter.
- `NoPrompt` sendet keine Eingabeaufforderung an den Identitätsanbieter.

    Diese Option ist nur für Richtlinien für die Profilbearbeitung in Azure Active Directory B2C nützlich. Weitere Informationen finden Sie unter [Spezifische Informationen zu B2C](https://aka.ms/msal-net-b2c-specificities).

##### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Verwenden Sie den `WithExtraScopeToConsent`-Modifizierer in einem erweiterten Szenario, in dem Sie möchten, dass der Benutzer vorab seine Zustimmung zu mehreren Ressourcen erteilt. Sie können diesen Modifizierer verwenden, wenn Sie keine inkrementelle Zustimmung verwenden möchten, die normalerweise mit MSAL.NET oder Microsoft Identity Platform verwendet wird. Weitere Informationen finden Sie unter [Einholen der Vorauseinwilligung des Benutzers für verschiedene Ressourcen](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

Hier sehen Sie ein Codebeispiel:

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

##### <a name="other-optional-parameters"></a>Andere optionale Parameter

Informationen zu den anderen optionalen Parametern für `AcquireTokenInteractive` finden Sie in der [Referenzdokumentation für AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

### <a name="acquire-tokens-via-the-protocol"></a>Abrufen von Token über das Protokoll

Die direkte Verwendung des Protokolls zum Abrufen von Token wird nicht empfohlen. Wenn Sie dies tun, werden einige Szenarien, die einmaliges Anmelden (Single Sign-On, SSO), die Geräteverwaltung und den bedingten Zugriff beinhalten, von der App nicht unterstützt.

Wenn Sie das Protokoll verwenden, um Token für mobile Apps abzurufen, machen Sie zwei Anforderungen:

* Abrufen eine Autorisierungscodes
* Austausch des Codes gegen ein Token

#### <a name="get-an-authorization-code"></a>Abrufen von Autorisierungscodes

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-the-token"></a>Erhalten des Zugriffs und Aktualisieren des Tokens

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API](scenario-mobile-call-api.md).