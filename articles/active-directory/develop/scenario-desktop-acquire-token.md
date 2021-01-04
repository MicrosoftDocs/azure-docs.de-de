---
title: Abrufen eines Tokens zum Aufrufen einer Web-API (Desktop-App) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine Desktop-App erstellen, die Web-APIs aufruft, um ein Token für die App abzurufen
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: fd341a4f6e2402ce934bdffd4f024e0ef569eec1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340916"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Desktop-App, die Web-APIs aufruft: Abrufen eines Token

Wenn Sie eine Instanz der öffentlichen Clientanwendung erstellt haben, rufen Sie damit ein Token ab, mit dem Sie anschließend eine Web-API aufrufen.

## <a name="recommended-pattern"></a>Empfohlenes Muster

Die Web-API wird durch ihr `scopes` definiert. Verwenden Sie ungeachtet der Benutzeroberfläche, die Sie in Ihrer Anwendung bereitstellen, folgendes Muster:

- Versuchen Sie systematisch, ein Token aus dem Tokencache abzurufen, indem Sie `AcquireTokenSilent` aufrufen.
- Schlägt dieser Aufruf fehl, verwenden Sie den gewünschten `AcquireToken`-Flow, der hier durch `AcquireTokenXX` dargestellt wird.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>In MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL für iOS und macOS

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift:

```swift
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
}
```
---

Im Folgenden werden die verschiedenen Möglichkeiten zum Abrufen von Token in einer Desktopanwendung beschrieben.

## <a name="acquire-a-token-interactively"></a>Interaktives Abrufen eines Tokens

Das folgende Beispiel enthält den mindestens erforderlichen Code zum interaktiven Abrufen eines Tokens zum Lesen des Benutzerprofils mit Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)
### <a name="in-msalnet"></a>In MSAL.NET

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

### <a name="mandatory-parameters"></a>Erforderliche Parameter

`AcquireTokenInteractive` verfügt über nur einen obligatorischen Parameter (``scopes``) mit einer Enumeration von Zeichenfolgen, die die Bereiche definieren, für die ein Token erforderlich ist. Wenn das Token für Microsoft Graph bestimmt ist, finden Sie die erforderlichen Bereiche in der API-Referenz der einzelnen Microsoft Graph-APIs im Abschnitt „Berechtigungen“. Zum [Auflisten der Kontakte des Benutzers](/graph/api/user-list-contacts) muss beispielsweise der Bereich „User.Read“, „Contacts.Read“ verwendet werden. Weitere Informationen finden Sie in der [Microsoft Graph-Referenz zu Berechtigungen](/graph/permissions-reference).

Bei Android müssen Sie außerdem mit `.WithParentActivityOrWindow` die übergeordnete Aktivität angeben (siehe Abbildung), sodass das Token nach der Interaktion wieder an die betreffende übergeordnete Aktivität zurückgegeben wird. Wenn Sie diese nicht angeben, wird beim Aufrufen von `.ExecuteAsync()` eine Ausnahme ausgelöst.

### <a name="specific-optional-parameters-in-msalnet"></a>Spezifische optionale Parameter in MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Die Benutzeroberfläche ist wichtig, da sie interaktiv ist. `AcquireTokenInteractive` hat einen speziellen optionalen Parameter, der für die unterstützenden Plattformen die übergeordnete Benutzeroberfläche angeben kann. Bei Verwendung in einer Desktopanwendung weist `.WithParentActivityOrWindow` je nach Plattform einen anderen Typ auf. Alternativ können Sie beim Erstellen eines Fensters auf den optionalen Parameter für das übergeordnete Fenster verzichten, wenn Sie nicht steuern möchten, wo das Anmeldedialogfeld auf dem Bildschirm angezeigt wird. Diese Vorgehensweise würde sich für befehlszeilenbasierte Anwendungen eignen und zum Weiterleiten von Aufrufen an andere Back-End-Dienste verwendet werden. Dabei werden keine Fenster für die Benutzerinteraktion benötigt.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Anmerkungen:

- In .NET Standard ist das erwartete `object` `Activity` für Android, `UIViewController` für iOS, `NSWindow` für MAC und `IWin32Window` oder `IntPr` für Windows.
- Unter Windows müssen Sie `AcquireTokenInteractive` aus dem UI-Thread aufrufen, sodass der eingebettete Browser den entsprechenden UI-Synchronisierungskontext abruft. Erfolgt kein Aufruf aus dem UI-Thread, werden Meldungen möglicherweise nicht fehlerfrei übertragen, oder es treten Deadlock-Szenarien in der Benutzeroberfläche auf. Sie können Microsoft-Authentifizierungsbibliotheken (MSALs) aus dem UI-Thread aufrufen, wenn Sie sich nicht bereits im UI-Thread befinden, indem Sie `Dispatcher` in WPF verwenden.
- Wenn Sie mit WPF ein Fenster aus einem WPF-Steuerelement abrufen, können Sie die `WindowInteropHelper.Handle`-Klasse verwenden. Dann erfolgt der Aufruf aus einem WPF-Steuerelement (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

Mit `WithPrompt()` wird durch Angabe einer Eingabeaufforderung die Interaktivität mit dem Benutzer gesteuert.

![Abbildung mit den Feldern in der Struktur der Eingabeaufforderung. Diese konstanten Werte steuern die Interaktivität mit dem Benutzer, indem sie den Typ der Eingabeaufforderung definieren, der mit der „WithPrompt ()“-Methode angezeigt wird.](https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png)

Die Klasse definiert die folgenden Konstanten:

- ``SelectAccount``: Hiermit wird STS gezwungen, das Dialogfeld zur Kontoauswahl mit Konten anzuzeigen, für die der Benutzer über eine Sitzung verfügt. Diese Option ist nützlich, wenn Anwendungsentwickler Benutzern die Auswahl unter verschiedenen Identitäten ermöglichen möchten. Diese Option bewirkt, dass MSAL ``prompt=select_account`` an den Identitätsanbieter sendet. Diese Option ist die Standardeinstellung. Damit wird die bestmögliche Benutzererfahrung auf Grundlage der verfügbaren Informationen bereitgestellt (z. B. Konto, Vorhandensein einer Sitzung für den Benutzer). Ändern Sie diese Option nicht ohne triftigen Grund.
- ``Consent``: Hiermit kann der Anwendungsentwickler erzwingen, dass der Benutzer zur Zustimmung aufgefordert wird, selbst wenn die Zustimmung zuvor erteilt wurde. In diesem Fall sendet MSAL `prompt=consent` an den Identitätsanbieter. Diese Option kann in bestimmten sicherheitsorientierten Anwendungen verwendet werden, wenn die Governance des Unternehmens vorschreibt, dass für den Benutzer bei jeder Verwendung der Anwendung das Zustimmungsdialogfeld angezeigt wird.
- ``ForceLogin``: Hiermit kann der Anwendungsentwickler angeben, dass der Dienst den Benutzer zur Eingabe von Anmeldeinformationen auffordert, selbst wenn eine solche Benutzerabfrage nicht erforderlich ist. Diese Option kann nützlich sein, um dem Benutzer das erneute Anmelden zu ermöglichen, wenn das Abrufen eines Tokens fehlschlägt. In diesem Fall sendet MSAL `prompt=login` an den Identitätsanbieter. Diese Option wird gelegentlich in sicherheitsorientierten Anwendungen verwendet, wenn die Governance des Unternehmens vorschreibt, dass sich Benutzer beim Zugriff auf bestimmte Teile einer Anwendung stets erneut anmelden müssen.
- ``Never`` (nur für .NET 4.5 und WinRT) fragt den Benutzer nicht ab, sondern versucht stattdessen, das in der ausgeblendeten eingebetteten Webansicht gespeicherte Cookie zu verwenden. Weitere Informationen finden Sie unter „Webansichten“ in MSAL.NET. Die Verwendung dieser Option schlägt möglicherweise fehl. In diesem Fall löst `AcquireTokenInteractive` eine Ausnahme aus mit der Meldung, dass eine Interaktion auf der Benutzeroberfläche erforderlich ist. Sie müssen einen anderen `Prompt`-Parameter verwenden.
- ``NoPrompt`` sendet keine Eingabeaufforderung an den Identitätsanbieter. Diese Option empfiehlt sich nur für Azure Active Directory (Azure AD) B2C-Richtlinien für die Profilbearbeitung. Weitere Informationen finden Sie unter [Spezifische Informationen zu Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Dieser Modifizierer wird in einem erweiterten Szenario verwendet, in dem der Benutzer vorab seine Einwilligung für mehrere Ressourcen erteilen soll (und nicht die schrittweise Einwilligung, die normalerweise bei MSAL.NET/der Microsoft Identity Platform verwendet wird). Weitere Informationen finden Sie unter [Einholen der Vorauseinwilligung des Benutzers für verschiedene Ressourcen](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Eine Webbenutzeroberfläche ist ein Mechanismus zum Aufrufen eines Browsers. Dieser Mechanismus kann ein dediziertes Benutzeroberflächen-Webbrowser-Steuerelement oder eine Möglichkeit sein, das Öffnen des Browsers zu delegieren.
MSAL stellt zwar für die meisten Plattformen Implementierungen von Webbenutzeroberflächen bereit, dennoch gibt es Fälle, in denen Sie den Browser möglicherweise selbst hosten möchten:

- Plattformen, die nicht explizit von MSAL abgedeckt werden, z. B. Blazor, Unity, Mono auf Desktops.
- Sie möchten die Benutzeroberfläche Ihrer Anwendung testen und einen automatisierten Browser nutzen, der mit Selenium verwendet werden kann.
- Der Browser und die App, die MSAL ausführen, befinden sich in separaten Prozessen.

##### <a name="at-a-glance"></a>Auf einen Blick

Um dies zu erreichen, übergeben Sie eine `start Url` an MSAL, die in einem Browser Ihrer Wahl angezeigt werden muss, damit der Endbenutzer Eingaben (z. B. seinen Benutzernamen) vornehmen kann.
Nach Abschluss der Authentifizierung muss Ihre App die `end Url`, die einen von Azure AD bereitgestellten Code enthält, an MSAL zurückgeben.
Der Host der `end Url` ist immer der `redirectUri`. Zum Abfangen der `end Url` können Sie folgende Aktionen ausführen:

- Überwachen von Browserumleitungen, bis die `redirect Url` erreicht wird.
- Umleiten des Browsers zu einer URL, die Sie überwachen.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi ist ein Erweiterungspunkt

`WithCustomWebUi` ist ein Erweiterungspunkt, mit dem Sie Ihre eigene Benutzeroberfläche in öffentlichen Clientanwendungen bereitstellen können. Sie können auch angeben, dass der Benutzer den /Authorize-Endpunkt des Identitätsanbieters durchlaufen, sich anmelden und einwilligen muss. MSAL.NET kann dann den Authentifizierungscode einlösen und ein Token abrufen. Er wird beispielsweise in Visual Studio verwendet, damit Electron-Anwendungen (z. B. Visual Studio-Feedback) die Webinteraktion bereitstellen, während der Großteil der Arbeiten von MSAL.NET ausgeführt wird. Die Verwendung empfiehlt sich auch, wenn Sie UI-Automatisierung bereitstellen möchten. In öffentlichen Clientanwendungen verwendet MSAL.NET den PKCE-Standard (Proof Key for Code Exchange), um die Einhaltung der Sicherheit zu gewährleisten. Der Code kann nur von MSAL.NET eingelöst werden. Weitere Informationen finden Sie unter [RFC 7636 – Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Verwenden von WithCustomWebUi

Führen Sie folgende Schritte aus, um `.WithCustomWebUI` zu verwenden.

  1. Implementieren Sie die `ICustomWebUi`-Schnittstelle. Weitere Informationen finden Sie auf [dieser Website](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementieren Sie eine `AcquireAuthorizationCodeAsync`-Methode, und übernehmen Sie die von MSAL.NET berechnete URL für den Autorisierungscode. Lassen Sie dann den Benutzer die Interaktion mit dem Identitätsanbieter durchlaufen und die URL zurückgeben, über die der Identitätsanbieter Ihre Implementierung zusammen mit dem Autorisierungscode wieder aufgerufen hätte. Bei auftretenden Problemen löst Ihre Implementierung eine `MsalExtensionException`-Ausnahme aus, um ordnungsgemäß mit MSAL zusammenzuarbeiten.
  2. Verwenden Sie in Ihrem `AcquireTokenInteractive`-Aufruf den `.WithCustomUI()`-Modifizierer, der die Instanz Ihrer benutzerdefinierten Webbenutzeroberfläche übergibt.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Beispiele für die Implementierung von „ICustomWebUi“ in einer Testautomatisierung: SeleniumWebUI

Das MSAL.NET-Team hat die Benutzeroberflächentests so umgeschrieben, dass Sie diesen Erweiterungsmechanismus verwenden können. Schauen Sie sich bei Interesse die [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)-Klasse im MSAL.NET-Quellcode an.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Mehr Benutzerfreundlichkeit mit SystemWebViewOptions

Von MSAL.NET 4.1 [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) können Sie Folgendes angeben:

- URI, zu dem navigiert werden soll (`BrowserRedirectError`), oder das anzuzeigende HTML-Fragment (`HtmlMessageError`), wenn Anmelde- oder Einwilligungsfehler im Systemwebbrowser auftreten.
- URI, zu dem navigiert werden soll (`BrowserRedirectSuccess`), oder das bei erfolgreicher Anmeldung oder Einwilligung anzuzeigende HTML-Fragment (`HtmlMessageSuccess`).
- Aktion, die zum Starten des Systembrowsers ausgeführt werden soll. Sie können Ihre eigene Implementierung bereitstellen, indem Sie den `OpenBrowserAsync`-Delegaten festlegen. Die Klasse stellt außerdem eine Standardimplementierung für zwei Browser bereit: `OpenWithEdgeBrowserAsync` und `OpenWithChromeEdgeBrowserAsync` für Microsoft Edge bzw. [Microsoft Edge unter Chromium](https://www.windowscentral.com/faq-edge-chromium).

Zum Verwenden dieser Struktur können Sie etwas Ähnliches wie im folgenden Beispiel schreiben:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Andere optionale Parameter

Informationen zu allen anderen optionalen Parametern für `AcquireTokenInteractive` finden Sie unter [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python stellt keine direkte interaktive Methode zum Abrufen von Token bereit. Stattdessen muss die Anwendung in ihrer Implementierung des Benutzerinteraktionsflows eine Autorisierungsanforderung senden, um einen Autorisierungscode zu erhalten. Dieser Code kann dann zum Abrufen des Tokens an die `acquire_token_by_authorization_code`-Methode übergeben werden.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>In MSAL für iOS und macOS

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
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

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```
---

## <a name="integrated-windows-authentication"></a>Integrierte Windows-Authentifizierung

Wenn Sie einen Domänenbenutzer in einer Domäne oder einen in Azure AD eingebundenen Computer anmelden möchten, müssen Sie die integrierte Windows-Authentifizierung verwenden.

### <a name="constraints"></a>Einschränkungen

- Die integrierte Windows-Authentifizierung kann nur für Benutzer des Typs *Federated+* verwendet werden, d. h. für Benutzer, die in Azure Active Directory erstellt und von Azure AD unterstützt werden. Direkt in Azure AD erstellte Benutzer ohne Azure Active Directory-Unterstützung (d. h. *verwaltete* Benutzer) können diesen Authentifizierungsflow nicht verwenden. Diese Einschränkung wirkt sich nicht auf den Flow mit Benutzername und Kennwort aus.
- IWA ist für Apps bestimmt, die für die .NET Framework-, .NET Core- und die UWP-Plattform (Universal Windows Platform) geschrieben wurden.
- Die [mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) wird von der IWA nicht umgangen. Wenn MFA konfiguriert ist, kann IWA fehlschlagen, wenn eine MFA-Abfrage erforderlich ist, da bei MFA eine Benutzerinteraktion benötigt wird.
  > [!NOTE]
  > Dies ist eine komplizierte Situation. IWA ist nicht interaktiv, die mehrstufige Authentifizierung erfordert jedoch eine Benutzerinteraktion. Wann der Identitätsanbieter eine mehrstufige Authentifizierung anfordert, wird nicht von Ihnen gesteuert, sondern vom Mandantenadministrator. Nach unserer Erfahrung ist die mehrstufige Authentifizierung erforderlich, wenn Sie sich aus einem anderen Land/einer anderen Region anmelden, nicht über ein VPN mit einem Unternehmensnetzwerk verbunden sind und gelegentlich sogar dann, wenn eine VPN-Verbindung besteht. Erwarten Sie keinen deterministischen Satz von Regeln. Azure AD greift auf KI zurück, um kontinuierlich zu lernen, wann MFA erforderlich ist. Greifen Sie beim Fehlschlagen von IWA auf eine Eingabeaufforderung für Benutzer wie die interaktive Authentifizierung oder den Gerätecodeflow zurück.

- Für die in `PublicClientApplicationBuilder` übergebene Autorität gelten folgende Voraussetzungen:
  - Sie muss auf Mandanten beruhen (im Format `https://login.microsoftonline.com/{tenant}/`, wobei `tenant` entweder die GUID ist, die die Mandanten-ID darstellt, oder eine Domäne, die dem Mandanten zugeordnet ist).
  - Sie muss für ein Geschäfts-, Schul- oder Unikonto bestimmt sein: `https://login.microsoftonline.com/organizations/`.
  - Persönliche Microsoft-Konten werden nicht unterstützt. Sie können keine Mandanten vom Typ „/common“ oder „/consumers“ verwenden.

- Da die integrierte Windows-Authentifizierung einen automatischen Flow darstellt:
  - Der Benutzer muss vorher in die Nutzung Ihrer Anwendung eingewilligt haben.
  - Oder der Mandantenadministrator muss zuvor für alle Benutzer im Mandanten zugestimmt haben, die Anwendung zu nutzen.
  - Anders gesagt:
    - Entweder haben Sie als Entwickler im Azure-Portal die Schaltfläche **Gewähren** für sich selbst ausgewählt.
    - Oder ein Mandantenadministrator hat bei der Anwendungsregistrierung auf der Registerkarte **API-Berechtigungen** die Schaltfläche **Administratoreinwilligung für {Mandantendomäne} erteilen/widerrufen** ausgewählt. Weitere Informationen finden Sie unter [Hinzufügen von Zugriffsberechtigungen für Ihre Web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Oder Sie haben eine Möglichkeit für Benutzer eingeräumt, der Anwendung zuzustimmen. Weitere Informationen finden Sie unter [Anfordern der Zustimmung einzelner Benutzer](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Oder Sie haben eine Möglichkeit für den Mandantenadministrator eingeräumt, der Anwendung zuzustimmen. Weitere Informationen finden Sie unter [Zustimmung des Administrators](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Dieser Flow ist aktiviert für .NET Desktop-, .NET Core- und UWP-Apps.

Weitere Informationen zur Zustimmung finden Sie unter [Berechtigungen und Zustimmung in Microsoft Identity Platform](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Informationen zur Verwendung

# <a name="net"></a>[.NET](#tab/dotnet)

In MSAL.NET müssen Sie Folgendes verwenden:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalerweise benötigen Sie nur einen Parameter (`scopes`). Je nach Einrichtung der Richtlinien durch Ihren Windows-Administrator kann es jedoch möglich sein, dass Anwendungen auf dem Windows-Computer den angemeldeten Benutzer nicht abfragen dürfen. Verwenden Sie in diesem Fall eine zweite Methode (`.WithUsername()`), und übergeben Sie den Benutzernamen des angemeldeten Benutzers im UPN-Format, z. B. `joe@contoso.com`. Für .NET Core ist nur die Überladung mit dem Benutzernamen verfügbar, da die .NET Core-Plattform den Benutzernamen für das Betriebssystem nicht anfordern kann.

Im folgenden Beispiel wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu den Ausnahmearten und den entsprechenden Problembehandlungen.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

Die Liste der möglichen Modifizierer für AcquireTokenByIntegratedWindowsAuthentication finden Sie unter [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Dies ist ein Auszug aus den [MSAL Java-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Dieser Flow wird in MSAL Python noch nicht unterstützt.

# <a name="macos"></a>[macOS](#tab/macOS)

Dieser Flow gilt nicht für macOS.

---

## <a name="username-and-password"></a>Benutzername und Kennwort

Sie können ein Token auch abrufen, indem Sie Benutzername und Kennwort angeben. Dieser Flow ist begrenzt und wird nicht empfohlen, in bestimmten Anwendungsfällen kann er jedoch erforderlich sein.

### <a name="this-flow-isnt-recommended"></a>Dieser Flow wird nicht empfohlen.

Dieser Flow wird *nicht empfohlen*, da es nicht sicher ist, wenn die Anwendung das Kennwort des Benutzers abfragt. Weitere Informationen finden Sie unter [What's the solution to the growing problem of passwords? (Wie sich das zunehmende Problem der Passwörter lösen lässt.)](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Der bevorzugte Flow für das automatische Abrufen eines Tokens auf Computern in Windows-Domänen ist die [integrierte Windows-Authentifizierung](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Sie können auch den [Gerätecodeflow](https://aka.ms/msal-net-device-code-flow) verwenden.

> [!NOTE]
> In manchen Fällen ist auch die Verwendung eines Benutzernamens und Kennworts hilfreich, z. B. in DevOps-Szenarien. Wenn Sie jedoch Benutzernamen und Kennwort in interaktiven Szenarien verwenden möchten, in denen Sie Ihre eigene Benutzeroberfläche bereitstellen, sollten Sie sich Gedanken darüber machen, wie Sie das ändern können. Durch die Verwendung von Benutzername und Kennwort geben Sie eine Reihe von Vorteilen auf:
>
> - Grundsätze moderner Identitäten. Ein Kennwort kann ausgespäht und wiedergegeben werden, da ein gemeinsames Geheimnis abgefangen werden kann. Dies ist inkompatibel mit einem Szenario ohne Kennwort.
> - Benutzer, die MFA durchführen müssen, können sich nicht anmelden, da keine Interaktion besteht.
> - Benutzer können die einmalige Anmeldung (Single Sign-On, SSO) nicht nutzen.

### <a name="constraints"></a>Einschränkungen

Außerdem gelten die folgenden Einschränkungen:

- Der Flow mit Benutzername und Kennwort ist nicht mit dem bedingten Zugriff und der mehrstufigen Authentifizierung kompatibel. Wenn also Ihre App in einem Azure AD-Mandanten ausgeführt wird, für den der Mandantenadministrator die mehrstufige Authentifizierung fordert, können Sie diesen Flow nicht nutzen. Dies ist aber in vielen Organisationen der Fall.
- Er funktioniert nur für Geschäfts-, Schul- oder Unikonten (nicht für MSA).
- Der Flow ist für .NET Desktop und .NET Core, jedoch nicht für UWP verfügbar.

### <a name="b2c-specifics"></a>B2C-Besonderheiten

Weitere Informationen finden Sie unter [Gewährung der Kennwortanmeldeinformationen des Ressourcenbesitzers mit B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Nutzen Sie sie.

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` enthält die Methode `AcquireTokenByUsernamePassword`.

Im folgenden Beispiel wird ein vereinfachtes Szenario dargestellt.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

Im folgenden Beispiel wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu den Ausnahmearten und den entsprechenden Problembehandlungen.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

Weitere Informationen zu allen Modifizierern, die auf `AcquireTokenByUsernamePassword` angewendet werden können, finden Sie unter [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Dies ist ein Auszug aus den [MSAL Java-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Dies ist ein Auszug aus den [MSAL Python-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

# <a name="macos"></a>[macOS](#tab/macOS)

Dieser Flow wird in MSAL für macOS nicht unterstützt.

---

## <a name="command-line-tool-without-a-web-browser"></a>Befehlszeilentool ohne Webbrowser

### <a name="device-code-flow"></a>Gerätecodefluss

Wenn Sie ein Befehlszeilentool schreiben, das keine Websteuerelemente enthält, und die obigen Flows nicht verwenden können oder möchten, müssen Sie den Gerätecodeflow verwenden.

Für die interaktive Authentifizierung mit Azure AD wird ein Webbrowser benötigt. Weitere Informationen finden Sie unter [Verwenden von Webbrowsern](https://aka.ms/msal-net-uses-web-browser). Für die Authentifizierung von Benutzern bei Geräten oder Betriebssystemen ohne Webbrowser ermöglicht der Gerätecodeflow dem Benutzer, ein anderes Gerät wie einen Computer oder ein Mobiltelefon zu verwenden, um sich interaktiv anzumelden. Mithilfe des Gerätecodeflows ruft die Anwendung Token in einem zweistufigen Prozess ab, der für diese Geräte oder Betriebssysteme entwickelt wurde. Beispiele für solche Anwendungen sind iOT-Anwendungen oder Befehlszeilentools (CLI). Dahinter steckt folgender Gedanke:

1. Wenn eine Benutzerauthentifizierung erforderlich ist, stellt die App einen Code für den Benutzer bereit. Der Benutzer wird aufgefordert, ein anderes Gerät (z. B. ein Smartphone mit Internetverbindung) zu verwenden, um zu einer URL (z. B. `https://microsoft.com/devicelogin`) zu wechseln. Anschließend wird der Benutzer aufgefordert, den Code einzugeben. Danach wird der Benutzer auf der Webseite durch einen normalen Authentifizierungsprozess geführt, der ggf. auch Zustimmungsaufforderungen und die mehrstufige Authentifizierung umfasst.

2. Nach erfolgreicher Authentifizierung empfängt die Befehlszeilen-App die erforderlichen Token über einen Backchannel und führt damit die benötigten Web-API-Aufrufe aus.

### <a name="use-it"></a>Nutzen Sie sie.

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication` enthält die Methode `AcquireTokenWithDeviceCode`.

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Diese Methode nimmt folgende Parameter an:

- `scopes`, für den ein Zugriffstoken angefordert werden soll.
- Ein Rückruf, der `DeviceCodeResult` empfängt.

  ![Eigenschaften von DeviceCodeResult](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Im folgenden Beispielcode wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu möglicherweise ausgelösten Ausnahmen und den zugehörigen Problembehandlungen.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }
    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
# <a name="java"></a>[Java](#tab/java)

Dies ist ein Auszug aus den [MSAL Java-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="python"></a>[Python](#tab/python)

Dies ist ein Auszug aus den [MSAL Python-Entwicklungsbeispielen](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/).

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

# <a name="macos"></a>[macOS](#tab/macOS)

Dieser Flow gilt nicht für macOS.

---

## <a name="file-based-token-cache"></a>Dateibasierter Tokencache

In MSAL.NET wird standardmäßig ein InMemory-Tokencache bereitgestellt.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>Serialisierung kann in Windows-Desktop-Apps und Web-Apps oder Web-APIs angepasst werden.

Wenn Sie unter .NET Framework und .NET Core keine zusätzlichen Anforderungen haben, bleibt der InMemory-Tokencache für die Dauer der Anwendung bestehen. Die Serialisierung wird nicht standardmäßig bereitgestellt. Dies liegt daran, dass MSAL .NET-Desktop- oder Core-Anwendungen Konsolen- oder Windows-Anwendungen sein können (die über Zugriff auf das Dateisystem verfügen könnten), *aber auch* Webanwendungen oder Web-APIs. Diese Web-Apps und Web-APIs nutzen u. U. bestimmte Mechanismen zum Zwischenspeichern wie Datenbanken, verteilte Caches und Redis Caches. Um eine einheitliche Nutzung des Tokencaches in .NET Desktop oder .NET Core sicherzustellen, müssen Sie die Serialisierung anpassen.

Klassen und Schnittstellen, die an der Serialisierung über den Tokencache beteiligt sind, weisen die folgenden Typen auf:

- ``ITokenCache`` definiert Ereignisse zum Abonnieren von Anforderungen zur Tokencacheserialisierung sowie Methoden zum Serialisieren oder Deserialisieren des Caches in verschiedenen Formaten (ADAL v3.0, MSAL 2.x und MSAL 3.x = ADAL v5.0).
- ``TokenCacheCallback`` ist ein an die Ereignisse übergebener Rückruf, damit Sie die Serialisierung verarbeiten können. Sie werden mit Argumenten vom Typ ``TokenCacheNotificationArgs`` aufgerufen.
- ``TokenCacheNotificationArgs`` stellt nur die ``ClientId`` der Anwendung und einen Verweis auf den Benutzer bereit, für den das Token verfügbar ist.

  ![Diagramm der Tokencacheserialisierung](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET erstellt automatisch Tokencaches und stellt den `IToken`-Cache für Sie bereit, wenn Sie die `UserTokenCache`-Methode und die `AppTokenCache`-Eigenschaften einer Anwendung aufrufen. Sie müssen die Schnittstelle nicht selbst implementieren. Beim Implementieren einer benutzerdefinierten Serialisierung des Tokencaches müssen Sie folgende Aufgaben ausführen:
>
> - Sie müssen auf Ereignisse vom Typ `BeforeAccess` und `AfterAccess` (oder auf deren *Async*-Pendant) reagieren. Der `BeforeAccess`-Delegat ist für das Deserialisieren des Caches verantwortlich. Der `AfterAccess`-Delegat ist für das Serialisieren des Caches verantwortlich.
> - Denken Sie daran, dass einige dieser Ereignisse Blobs speichern oder laden, die über das Ereignisargument an den gewünschten Speicher übergeben werden.

Die Strategien variieren, je nachdem, ob Sie eine Tokencacheserialisierung für eine öffentliche Clientanwendung (z. B. ein Desktop) oder eine vertrauliche Clientanwendung (z. B. Web-App oder Web-API bzw. Daemon-App) schreiben.

Ab MSAL v2.x stehen verschiedene Optionen zur Auswahl. Ihre Auswahl hängt davon ab, ob Sie den Cache nur im MSAL.NET-Format serialisieren möchten. Dabei handelt es sich um einen Cache mit einheitlichem Formatierungscache, der für MSAL, jedoch auch plattformübergreifend verwendet werden kann. Vielleicht möchten Sie aber auch die [ältere](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Serialisierung des Tokencaches von ADAL v3 unterstützen.

Die Anpassung der Tokencacheserialisierung für die gemeinsame Verwendung des SSO-Status von ADAL.NET 3.x, ADAL.NET 5.x und MSAL.NET wird in einem Teil des Beispiels [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) erläutert.

### <a name="simple-token-cache-serialization-msal-only"></a>Einfache Serialisierung des Tokencaches (nur MSAL)

Das folgende Beispiel zeigt eine einfache Implementierung der benutzerdefinierten Serialisierung eines Tokencaches für Desktopanwendungen. Hier befindet sich der Benutzertokencache in einer Datei im selben Ordner wie die Anwendung.

Nach dem Erstellen der Anwendung aktivieren Sie die Serialisierung durch den Aufruf von ``TokenCacheHelper.EnableSerialization()`` und übergeben den `UserTokenCache` der Anwendung.

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Diese Hilfsklasse ähnelt dem folgenden Codeausschnitt:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

  private static readonly object FileLock = new object();


  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

Die Vorschau eines dateibasierten Serialisierungsmoduls für einen Tokencache in Produktionsqualität, das für öffentliche Clientanwendungen (für Desktopanwendungen unter Windows, Mac und Linux) verwendet wird, ist in der Open-Source-Bibliothek [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) verfügbar. Sie können das Modul aus dem folgenden NuGet-Paket in Ihre Anwendungen einbeziehen: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Haftungsausschluss: Die Bibliothek Microsoft.Identity.Client.Extensions.Msal ist eine Erweiterung für MSAL.NET. Klassen in diesen Bibliotheken werden möglicherweise künftig in MSAL.NET eingebunden, unverändert oder mit Breaking Changes.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Duale Serialisierung des Tokencaches (einheitlicher MSAL-Cache + ADAL v3)

Möglicherweise möchten Sie die Serialisierung des Tokencaches mit dem einheitlichen Cacheformat implementieren. Dieses Format kann für ADAL.NET 4. x und MSAL.NET 2. x und mit anderen MSALs derselben Generation oder älter auf derselben Plattform verwendet werden. Lassen Sie sich von folgendem Code inspirieren:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

In diesem Fall sieht die Hilfsklasse wie im folgenden Code aus:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>(Erweitert) Zugreifen auf die zwischengespeicherten Token des Benutzers in Hintergrund-Apps und Diensten

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Aufrufen einer Web-API von der Desktop-App](scenario-desktop-call-api.md).
