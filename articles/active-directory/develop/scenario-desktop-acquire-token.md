---
title: Web-APIs aufrufende Desktop-App (Aufrufen von Token für die App) – Microsoft Identity Platform
description: Erfahren Sie, wie Sie eine Desktop-App erstellen, die Web-APIs aufruft (Aufruf eines Tokens für die App).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d18c92cccac6bfb0bd359767ecdb51951268735
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962541"
---
# <a name="desktop-app-that-calls-web-apis---acquire-a-token"></a>Web-APIs aufrufende Desktop-App – Aufruf eines Tokens

Wenn Sie Ihre `IPublicClientApplication` erstellt haben, rufen Sie damit ein Token ab, mit dem Sie eine Web-API aufrufen.

## <a name="recommended-pattern"></a>Empfohlenes Muster

Die Web-API wird durch ihr `scopes` definiert. Egal, welche Benutzeroberfläche Sie in Ihrer Anwendung bereitstellen, Sie verwenden folgendes Muster:

- Systematischer Versuch des Abrufs eines Tokens aus dem Token-Cache durch Aufrufen von `AcquireTokenSilent`
- Schlägt dieser Aufruf fehl, verwenden Sie den gewünschten `AcquireToken`-Flow (hier dargestellt durch `AcquireTokenXX`)

```CSharp
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

Im Folgenden werden die verschiedenen Möglichkeiten zum Abrufen von Token in einer Desktop-Anwendung ausführlich beschrieben.

## <a name="acquiring-a-token-interactively"></a>Interaktives Abrufen eines Tokens

Das folgende Beispiel enthält den mindestens erforderlichen Code zum interaktiven Abrufen eines Tokens zum Lesen des Benutzerprofils mit Microsoft Graph.

```CSharp
string[] scopes = new string["user.read"];
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

`AcquireTokenInteractive` verfügt über nur einen obligatorischen Parameter ``scopes`` mit einer Enumeration von Zeichenfolgen, die die Bereiche definieren, für die ein Token erforderlich ist. Wenn das Token für Microsoft Graph bestimmt ist, finden sich die erforderlichen Bereiche in der API-Referenz der einzelnen Microsoft Graph-APIs im Abschnitt „Berechtigungen“. Zum [Auflisten der Kontakte des Benutzers](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts) muss beispielsweise der Bereich „User.Read“, „Contacts.Read“ verwendet werden. Siehe auch [Referenz zu Microsoft Graph-Berechtigungen](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Für Android müssen Sie außerdem die übergeordnete Aktivität angeben (mit `.WithParentActivityOrWindow`, siehe unten), sodass das Token nach der Interaktion an die betreffende übergeordnete Aktivität zurückgegeben wird. Wenn Sie sie nicht angeben, wird beim Aufrufen von `.ExecuteAsync()` eine Ausnahme ausgelöst.

### <a name="specific-optional-parameters"></a>Spezifische optionale Parameter

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

Für Interaktivität ist die Benutzeroberfläche besonders wichtig. `AcquireTokenInteractive` hat einen spezifischen optionalen Parameter, mit dem für die unterstützenden Plattformen die übergeordnete Benutzeroberfläche angegeben werden kann. Bei Verwendung in einer Desktopanwendung weist `.WithParentActivityOrWindow` je nach Plattform einen anderen Typ auf:

```CSharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .Net Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Anmerkungen:

- In .NET Standard ist der erwartete `object` ein `Activity` für Android, ein `UIViewController` für iOS, ein `NSWindow` für MAC und ein `IWin32Window` für `IntPr` unter Windows.
- Unter Windows müssen Sie `AcquireTokenInteractive` aus dem UI-Thread aufrufen, sodass der eingebettete Browser den entsprechenden UI-Synchronisierungskontext abruft.  Erfolgt kein Aufruf aus dem UI-Thread, werden möglicherweise Meldungen zu fehlerhafter Übertragung ausgegeben, oder es treten Deadlock-Szenarien in der Benutzeroberfläche auf. Sie können MSAL aus dem UI-Thread aufrufen, wenn Sie sich nicht bereits im UI-Thread befinden, indem Sie `Dispatcher` in WPF verwenden.
- Wenn Sie mit WPF ein Fenster aus einem WPF-Steuerelement abrufen, können Sie die `WindowInteropHelper.Handle`-Klasse verwenden. Der Aufruf erfolgt dann aus einem WPF-Steuerelement (`this`):
  
  ```CSharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

Mit `WithPrompt()` wird die Interaktivität mit dem Benutzer gesteuert, indem eine Eingabeaufforderung angegeben wird.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

Die Klasse definiert die folgenden Konstanten:

- ``SelectAccount``: Erzwingt STS, um das Kontoauswahl-Dialogfeld mit Konten anzuzeigen, für die der Benutzer über eine Sitzung verfügt. Diese Option ist nützlich, wenn Anwendungsentwickler Benutzern das Auswählen unter verschiedenen Identitäten ermöglichen möchten. Diese Option bewirkt, dass MSAL ``prompt=select_account`` an den Identitätsanbieter sendet. Diese Option ist die Standardeinstellung. Damit wird die bestmögliche Benutzererfahrung auf Grundlage der verfügbaren Informationen bereitgestellt (Konto, Vorhandensein einer Sitzung für den Benutzer usw. ). Ändern Sie diese Option nicht ohne triftigen Grund.
- ``Consent``: Hiermit kann der Anwendungsentwickler erzwingen, dass der Benutzer um Einwilligung gebeten wird, selbst wenn die Einwilligung zuvor erteilt wurde. In diesem Fall sendet MSAL `prompt=consent` an den Identitätsanbieter. Diese Option kann in bestimmten sicherheitsorientierten Anwendungen verwendet werden, wenn die Governance des Unternehmens vorschreibt, dass für den Benutzer bei jeder Verwendung der Anwendung das Einwilligungsdialogfeld angezeigt wird.
- ``ForceLogin``: Hiermit kann der Anwendungsentwickler angeben, dass der Dienst den Benutzer nach Anmeldeinformationen abfragt, selbst wenn eine solche Benutzerabfrage nicht erforderlich ist. Diese Option kann nützlich sein, wenn das Abrufen eines Tokens fehlschlägt, um dem Benutzer das erneute Anmelden zu ermöglichen. In diesem Fall sendet MSAL `prompt=login` an den Identitätsanbieter. Diese Option wird gelegentlich in sicherheitsorientierten Anwendungen verwendet, wenn die Governance des Unternehmens vorschreibt, dass sich Benutzer beim Zugriff auf bestimmte Teile der Anwendung stets erneut anmelden müssen.
- ``Never`` (nur für .NET 4.5 und WinRT) fragt den Benutzer nicht ab; stattdessen wird versucht, dass in der ausgeblendeten eingebetteten Webansicht gespeicherte Cookie zu verwenden (siehe unten: Webansichten in MSAL.NET). Die Verwendung dieser Option kann fehlschlagen, und in diesem Fall löst `AcquireTokenInteractive` eine Ausnahme aus. Diese weist darauf hin, dass eine UI-Interaktion erforderlich ist und dass Sie einen anderen `Prompt`-Parameter verwenden müssen.
- ``NoPrompt``: Es wird keine Eingabeaufforderung an den Identitätsanbieter gesendet. Diese Option empfiehlt sich nur Azure AD B2C-Richtlinien für die Profilbearbeitung (siehe [B2C-Besonderheiten](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Dieser Modifizierer wird in erweiterten Szenarien verwendet, in denen der Benutzer vorab seine Einwilligung für verschiedene Ressourcen erteilen soll (und nicht die schrittweise Einwilligung, die normalerweise mit MSAL.NET/Microsoft-Identity Platform v2.0 verwendet wird). Einzelheiten finden Sie unter [Einholen der Vorauseinwilligung des Benutzers für verschiedene Ressourcen](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi ist ein Erweiterungspunkt

`WithCustomWebUi` ist ein Erweiterungspunkt, mit dem Sie eine eigene Benutzeroberfläche in öffentlichen Clientanwendungen bereitstellen und angeben können, dass der Benutzer den /Authorize-Endpunkt des Identitätsanbieters durchlaufen, sich anmelden und einwilligen muss. MSAL.NET kann dann den Authentifizierungscode einlösen und ein Token abrufen. Er wird beispielsweise in Visual Studio verwendet, damit Electron-Anwendungen (z.B. VS Feedback) die Webinteraktion bereitstellen, während der Großteil der Arbeiten von MSAL.NET ausgeführt wird. Die Verwendung empfiehlt sich auch, wenn Sie UI-Automatisierung bereitstellen möchten. In öffentlichen Clientanwendungen verwendet MSAL.NET den PKCE-Standard ([RFC 7636 – Proof Key for Code Exchange by OAuth Public Clients](https://tools.ietf.org/html/rfc7636)), um die Einhaltung der Sicherheit zu gewährleisten: Der Code kann nur von MSAL.NET eingelöst werden.

  ```CSharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="how-to-use-withcustomwebui"></a>Verwenden von WithCustomWebUi

Zum Verwenden von `.WithCustomWebUI` müssen Sie Folgendes ausführen:
  
  1. Implementieren Sie die `ICustomWebUi`-Schnittstelle (siehe [hier](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Sie müssen im Wesentlichen eine `AcquireAuthorizationCodeAsync`-Methode implementieren, die die Autorisierungscode-URL akzeptiert (berechnet von MSAL.NET), den Benutzer die Interaktion mit dem Identitätsanbieter durchlaufen lässt und anschließend die URL zurückgibt, anhand derer der Identitätsanbieter Ihre Implementierung zurückruft (einschließlich des Autorisierungscodes). Bei auftretenden Problemen löst Ihre Implementierung eine `MsalExtensionException`-Ausnahme aus, um ordnungsgemäß mit MSAL zusammenzuarbeiten.
  2. In Ihrem `AcquireTokenInteractive`-Aufruf können Sie den `.WithCustomUI()`-Modifizierer verwenden, der die Instanz Ihrer benutzerdefinierten Web-UI übergibt.

     ```CSharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation---seleniumwebui"></a>Beispiele für die Implementierung ICustomWebUi in einer Testautomatisierung – SeleniumWebUI

Das MSAL.NET-Team hat unsere UI-Tests umgeschrieben, um diesen Erweiterungsmechanismus zu nutzen. Bei Interesse können Sie sich die [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160)-Klasse im MSAL.NET-Quellcode anschauen.

#### <a name="other-optional-parameters"></a>Andere optionale Parameter

Informationen zu allen anderen optionalen Parametern für `AcquireTokenInteractive` finden Sie in der Referenzdokumentation für [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

## <a name="integrated-windows-authentication"></a>Integrierte Windows-Authentifizierung

Wenn Sie einen Domänenbenutzer in einer Domäne oder bei einem Azure AD-Computer anmelden möchten, müssen Sie Folgendes verwenden:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

### <a name="constraints"></a>Einschränkungen

- AcquireTokenByIntegratedWindowsAuth (IWA) kann nur für **Federated**-Benutzer verwendet werden, d.h. für Benutzer, die in Active Directory erstellt und von Azure Active Directory unterstützt werden. Direkt in AAD erstellte Benutzer ohne AD-Unterstützer (d.h. **verwaltete** Benutzer) können diesen Authentifizierungsflow nicht verwenden. Diese Einschränkung wirkt sich nicht auf den Benutzername/Kennwort-Flow aus.
- IWA ist für Apps bestimmt, die für die Plattformen .NET Framework, .NET Core und UWP geschrieben wurden.
- IWA umgeht NICHT die MFA (mehrstufige Authentifizierung). Wenn MFA konfiguriert ist, kann IWA fehlschlagen, wenn eine MFA-Abfrage erforderlich ist, da bei MFA eine Benutzerinteraktion benötigt wird.
  > [!NOTE]
  > Dies ist eine komplizierte Situation. IWA ist nicht interaktiv, 2FA erfordert jedoch eine Benutzerinteraktion. Die Anforderung der Ausführung von 2FA durch den Identitätsanbieter wird nicht von Ihnen gesteuert, sondern vom Mandantenadministrator. Wir haben festgestellt, dass die zweistufige Authentifizierung erforderlich ist, wenn Sie sich aus dem Ausland anmelden, wenn Sie nicht über ein VPN mit einem Unternehmensnetzwerk verbunden sind und gelegentlich sogar dann, wenn eine VPN-Verbindung besteht. Sie sollten keine verbindlichen Regeln erwarten, da Azure Active Directory anhand von KI kontinuierlich ermittelt, ob eine zweistufige Authentifizierung erforderlich ist. Bei Fehlschlagen von IWA sollten Sie auf eine Eingabeaufforderung für Benutzer zurückgreifen (interaktive Authentifizierung oder Gerätecodeflow).

- Für die in `PublicClientApplicationBuilder` übergebene Autorität gilt Folgendes:
  - Sie muss auf Mandanten beruhen (im Format `https://login.microsoftonline.com/{tenant}/`, wobei `tenant` die GUID ist, die die Mandanten-ID oder eine Domäne darstellt, die dem Mandanten zugeordnet ist.
  - Sie muss für Geschäfts- und Schulkonten (`https://login.microsoftonline.com/organizations/`) bestimmt sein.

  > Persönliche Microsoft-Konten werden nicht unterstützt (Sie können keine Mandanten vom Typ /common oder /consumers verwenden).

- Da die integrierte Windows-Authentifizierung einen automatischen Flow darstellt:
  - muss der Benutzer Ihrer Anwendung dem Verwenden der Anwendung bereits zugestimmt haben,
  - oder der Mandantenadministrator muss für alle Benutzer im Mandanten der Verwendung der Anwendung zugestimmt haben.
  - Anders gesagt:
    - Entweder Sie als Entwickler haben im Azure-Portal die Schaltfläche **Gewähren** für sich selbst ausgewählt,
    - oder ein Mandantenadministrator hat die Schaltfläche **Administratoreinwilligung für {Mandantendomäne} erteilen/widerrufen** auf der Registerkarte **API-Berechtigungen** der Registrierung für die Anwendung ausgewählt (siehe [Hinzufügen von Zugriffsberechtigungen für Web-APIs](https://docs.microsoft.com/azure/active-directory/develop/quickstart-configure-app-access-web-apis#add-permissions-to-access-web-apis)),
    - oder Sie haben eine Möglichkeit für Benutzer eingeräumt, der Anwendung zuzustimmen (siehe [Anfordern der Zustimmung einzelner Benutzer](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-individual-user-consent)),
    - oder Sie haben eine Möglichkeit für den Mandantenadministrator eingeräumt, der Anwendung zuzustimmen (siehe [Zustimmung des Administrators](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#requesting-consent-for-an-entire-tenant)).

- Dieser Flow ist aktiviert für .NET Desktop-, .NET Core- und UWP (Windows Universal)-Apps. Für .NET Core ist nur die Überladung verfügbar, die den Benutzernamen annimmt, da die .NET Core-Plattform nicht den Benutzernamen für das Betriebssystem anfordern kann.
  
Weitere Informationen zur Zustimmung finden Sie unter [Berechtigungen und Zustimmung für v2.0](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)

### <a name="how-to-use-it"></a>Verwendung

Normalerweise benötigen Sie nur einen Parameter (`scopes`). Je nach Einrichtung der Richtlinien durch Ihren Windows-Administrator kann es jedoch möglich sein, dass Anwendungen auf dem Windows-Computer nicht den angemeldeten Benutzer abfragen dürfen. Verwenden Sie in diesem Fall eine zweite `.WithUsername()`-Methode, und übergeben Sie den Benutzernamen des angemeldeten Benutzers im UPN-Format – `joe@contoso.com`.

Im folgenden Beispiel wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu möglicherweise ausgelösten Ausnahmen und den zugehörigen Problembehandlungen.

```CSharp
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

Die Liste der möglichen Modifizierer für AcquireTokenByIntegratedWindowsAuthentication finden Sie unter [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="username--password"></a>Benutzername und Kennwort

Sie können ein Token auch abrufen, indem Sie Benutzername und Kennwort angeben. Dieser Flow ist begrenzt und wird nicht empfohlen, in bestimmten Anwendungsfällen kann er jedoch erforderlich sein.

### <a name="this-flow-isnt-recommended"></a>Dieser Flow wird nicht empfohlen.

Dieser Flow wird **nicht empfohlen**, da die Anwendung, die das Kennwort des Benutzers abfragt, nicht sicher ist. Weitere Informationen zu diesem Problem erhalten Sie in [diesem Artikel](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). Der bevorzugte Flow für das automatische Abrufen eines Tokens auf Computern in Windows-Domänen ist die [integrierte Windows-Authentifizierung](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Andernfalls können Sie auch den [Gerätecodeflow](https://aka.ms/msal-net-device-code-flow) verwenden.

> Dies ist u.U. gelegentlich hilfreich (in DevOps-Szenarien). Wenn Sie jedoch Benutzername/Kennwort in interaktiven Szenarien verwenden möchten, in denen Sie eine eigene Benutzeroberfläche bereitstellen, sollten Sie eine Umstellung erwägen. Die Verwendung von Benutzername/Kennwort birgt eine Reihe von Nachteilen und Risiken:

> - Core-Mandanten mit moderner Identität: Kennwörter werden durch Phishing entwendet und wiedergegeben. Dies liegt am Konzept des gemeinsamen geheimen Schlüssels, der abgefangen werden kann.
> Dies ist inkompatibel mit einem Szenario ohne Kennwort.
> - Benutzer, die die MFA ausführen müssen, können sich nicht anmelden (da keine Interaktion gegeben ist).
> - Einmaliges Anmelden (SSO) ist für Benutzer nicht möglich.

### <a name="constraints"></a>Einschränkungen

Außerdem gelten die folgenden Einschränkungen:

- Der Benutzername/Kennwort-Flow nicht kompatibel mit dem bedingten Zugriff und der mehrstufigen Authentifizierung: Wenn also Ihre App in einem Azure AD-Mandanten ausgeführt wird, für den der Mandantenadministrator die mehrstufige Authentifizierung fordert, können Sie diesen Flow nicht nutzen. Dies ist aber in vielen Organisationen der Fall.
- Er funktioniert nur für Geschäfts-, Schul- oder Unikonten (nicht für MSA).
- Der Flow ist für .NET Desktop und .NET Core, jedoch nicht für UWP verfügbar.

### <a name="b2c-specifics"></a>B2C-Besonderheiten

[Weitere Informationen zum Verwenden von ROPC mit B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="how-to-use-it"></a>Verwendung

`IPublicClientApplication`enthält die Methode `AcquireTokenByUsernamePassword`

Im folgenden Beispiel wird ein vereinfachtes Szenario dargestellt.

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
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

Im folgenden Beispiel wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu möglicherweise ausgelösten Ausnahmen und den zugehörigen Problembehandlungen.

```CSharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuild.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAync();
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

Ausführliche Informationen zu allen Modifizierern, die auf `AcquireTokenByUsernamePassword` angewendet werden können, finden Sie unter [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder?view=azure-dotnet-preview#methods)

## <a name="command-line-tool-without-web-browser"></a>Befehlszeilentool (ohne Webbrowser)

### <a name="device-code-flow-why-and-how"></a>Gerätecodefluss – warum und wie?

Wenn Sie ein Befehlszeilentool schreiben (das keine Websteuerelemente enthält) und die obigen Flows nicht verwenden können oder möchten, müssen Sie `AcquireTokenWithDeviceCode` verwenden.

Für die interaktive Authentifizierung mit Azure AD wird ein Webbrowser benötigt (Einzelheiten finden Sie unter [Verwendung von Webbrowsern](https://aka.ms/msal-net-uses-web-browser)). Für die Authentifizierung von Benutzer bei Geräten oder Betriebssystemen ohne Webbrowser ermöglicht der Gerätecodeflow, dass der Benutzer ein anderes Gerät (wie einen Computer oder ein Mobiltelefon) verwendet, um sich interaktiv anzumelden. Durch Nutzung des Gerätecodeflows ruft die Anwendung Token in einem Prozess mit zwei Schritten ab, der speziell für diese Geräte/Betriebssysteme entwickelt wurde. Beispiele für solche Anwendungen sind iOT-Anwendungen oder Befehlszeilentools (CLI). Dahinter steckt folgender Gedanke:

1. Jedes Mal, wenn eine Authentifizierung erforderlich ist, gibt die App einen Code an und bittet den Benutzer, ein anderes Gerät zu verwenden (z.B. ein Smartphone mit Internetverbindung), um zu einer URL (z.B. `https://microsoft.com/devicelogin`) zu navigieren, unter welcher der Benutzer den Code eingeben muss. Anschließend wird der Benutzer auf der Webseite durch einen normalen Authentifizierungsprozess geführt, u.a. mit Zustimmungsaufforderung und mehrstufiger Authentifizierung, sofern erforderlich.

2. Nach erfolgreicher Authentifizierung empfängt die Befehlszeilen-App die erforderlichen Token über einen Backchannel und führt damit die benötigten API-Aufrufe aus.

### <a name="code"></a>Code

`IPublicClientApplication`enthält die Methode `AcquireTokenWithDeviceCode`

```CSharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

Diese Methode nimmt folgende Parameter an:

- `scopes`, für den ein Zugriffstoken angefordert werden soll
- Ein Rückruf, der den `DeviceCodeResult` empfängt

  ![image](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Im folgenden Beispielcode wird der aktuelle Fall veranschaulicht. Außerdem erhalten Sie Erläuterungen zu möglicherweise ausgelösten Ausnahmen und den zugehörigen Problembehandlungen.

```CSharp
static async Task<AuthenticationResult> GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication pca = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 AuthenticationResult result = null;
 var accounts = await app.GetAccountsAsync();

 // All AcquireToken* methods store the tokens in the cache, so check the cache first
 try
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
       .ExecuteAsync();
 }
 catch (MsalUiRequiredException ex)
 {
  // A MsalUiRequiredException happened on AcquireTokenSilent.
  // This indicates you need to call AcquireTokenInteractive to acquire a token
  System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");
 }

 try
 {
  result = await app.AcquireTokenWithDeviceCode(scopes,
      deviceCodeCallback =>
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
  // If you use a CancellationToken, and call the Cancel() method on it, then this may be triggered
  // to indicate that the operation was cancelled.
  // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
  // for more detailed information on how C# supports cancellation in managed threads.
 }
 catch (MsalClientException ex)
 {
  // Verification code expired before contacting the server
  // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
  // call to `AcquireTokenWithDeviceCode` is not cancelled in between
 }
}
```

## <a name="file-based-token-cache"></a>Dateibasierter Tokencache

In MSAL.NET wird standardmäßig ein InMemory-Tokencache bereitgestellt.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-appsweb-apis"></a>Serialisierung kann in Windows-Desktop-Apps und Webanwendungen/Web-APIs angepasst werden.

Wenn Sie unter .NET Framework und .NET Core keine zusätzlichen Anforderungen haben, bleibt der InMemory-Tokencache für die Dauer der Anwendung bestehen. Die Serialisierung wird nicht standardmäßig bereitgestellt. Dies liegt daran, dass MSAL .NET-Desktop-/Core-Anwendungen Konsolenanwendungen oder Windows-Anwendungsanwendungen sein können (die über Zugriff auf das Dateisystem verfügen), **jedoch auch** Webanwendungen oder Web-APIs. Diese Webanwendungen und Web-APIs nutzen u.U. spezifische Mechanismen zum Zwischenspeichern, beispielsweise Datenbanken, verteilte Caches und Redis Caches. Um eine beständige Tokencache-Anwendung .NET Desktop oder Core sicherzustellen, müssen Sie die Serialisierung anpassen.

Klassen und Schnittstellen, die an der Serialisierung über den Tokencache beteiligt sind, weisen die folgenden Typen auf:

- ``ITokenCache``, der Ereignisse zum Abonnieren von Tokencache-Serialisierungsanforderungen definiert, sowie Methoden zum Serialisieren oder Deserialisieren des Caches in diversen Formaten (ADAL v3.0, MSAL 2.x, and MSAL 3.x = ADAL v5.0)
- ``TokenCacheCallback`` ist ein an die Ereignisse übergebener Rückruf, sodass Sie die Serialisierung verarbeiten können. Sie werden mit Argumenten vom Typ ``TokenCacheNotificationArgs`` aufgerufen.
- ``TokenCacheNotificationArgs`` stellt nur den ``ClientId`` der Anwendung und einen Verweis für den Benutzer bereit, für den das Token verfügbar ist.

  ![image](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> MSAL.NET erstellt automatisch Tokencaches und stellt den `IToken`-Cache für Sie bereit, wenn Sie die `GetUserTokenCache`-Methode und die `GetAppTokenCache`-Methode der Anwendung aufrufen. Sie müssen die Schnittstelle nicht selbst implementieren. Beim Implementieren einer benutzerdefinierten Tokencache-Serialisierung müssen Sie folgende Aufgaben ausführen:
>
> - Reagieren Sie auf die „Ereignisse“ `BeforeAccess` und `AfterAccess`. Der `BeforeAccess`-Delegat ist zuständig für die Deserialisierung des Caches, während der `AfterAccess`-Delegat den Cache serialisiert.
> - Einige dieser Ereignisse speichern oder laden Blobs, die über das Ereignisargument an den gewünschten Speicher übergeben werden.

Die Strategien variieren, je nachdem, ob Sie eine Tokencache-Serialisierung für eine öffentliche Clientanwendung (Desktop) oder eine vertrauliche Clientanwendung (Webanwendung/Web-API, Daemon-App) schreiben.

Ab MSAL V2.x verfügen Sie über verschiedene Optionen, in Abhängigkeit davon, ob Sie den Cache nur im MSAL.NET-Format (Cache mit vereinheitlichtem Format, der für MSAL, jedoch auch plattformübergreifend verwendet werden kann) serialisieren möchten, oder ob auch die [ältere](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) Tokencache-Serialisierung von ADAL V3 unterstützt werden soll.

Eine Anpassung der Tokencache-Serialisierung, sodass der SSO-Zustand von ADAL.NET 3.x, ADAL.NET 5.x und MSAL.NET gemeinsam verwendet werden kann, wird teilweise im folgenden Beispiel erläutert: [active-directory-dotnet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)

### <a name="simple-token-cache-serialization-msal-only"></a>Einfache Tokencache-Serialisierung (nur MSAL)

Das untenstehende Beispiel zeigt eine einfache Implementierung der benutzerdefinierten Serialisierung eines Tokencaches für Desktopanwendungen. Hier befindet sich der Benutzertokencache in einer Datei im selben Ordner wie die Anwendung.

Nach dem Erstellen der Anwendung aktivieren Sie die Serialisierung durch einen Aufruf von ``TokenCacheHelper.EnableSerialization()``, wobei der `UserTokenCache` der Anwendung übergeben wird.

```CSharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Diese Hilfsklasse ähnelt dem folgenden Codeausschnitt:

```CSharp
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

Eine Vorschau eines dateibasierten Serialisierungsmoduls für einen Produktqualität-Tokencache für öffentliche Clientanwendungen (für Desktopanwendungen unter Windows, Mac und Linux) ist in der Open-Source-Bibliothek [Microsoft.Identity.Client.Extensions.Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) verfügbar. Sie können es aus dem folgenden Nuget-Paket in Ihre Anwendungen einschließen: [Microsoft.Identity.Client.Extensions.Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> Haftungsausschluss. Die Bibliothek Microsoft.Identity.Client.Extensions.Msal ist eine Erweiterung für MSAL.NET. Klassen in diesen Bibliotheken werden möglicherweise künftig in MSAL.NET eingebunden, unverändert oder mit Breaking Changes.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Duale Tokencache-Serialisierung (vereinheitlichter MSAL-Cache + ADAL V3)

Wenn Sie die Tokencache-Serialisierung sowohl mit dem vereinheitlichten Cacheformat (einheitlich für ADAL.NET 4.x und MSAL.NET 2.x sowie mit anderen MSALs derselben Generation oder älter, auf derselben Plattform), können Sie den folgenden Code als Anregung nutzen:

```CSharp
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

```CSharp
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
   usertokenCache = cache;
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   usertokenCache.SetBeforeAccess(BeforeAccessNotification);
   usertokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Token cache
  /// </summary>
  static ITokenCache usertokenCache;

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

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aufrufen einer Web-API aus der Desktop-App](scenario-desktop-call-api.md)
