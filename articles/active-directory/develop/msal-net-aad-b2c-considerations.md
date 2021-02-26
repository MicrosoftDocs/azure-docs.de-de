---
title: Azure AD B2C und MSAL.NET
titleSuffix: Microsoft identity platform
description: Überlegungen bei der Verwendung von Azure AD B2C mit Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b28454e9b60654541d4f62ec1d8455b30cfc2906
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580826"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Verwenden von MSAL.NET zur Anmeldung von Benutzern mit Identitäten sozialer Netzwerke

Sie können MSAL.NET zur Anmeldung von Benutzern mit Identitäten sozialer Netzwerke über [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md) verwenden. Azure AD B2C basiert auf dem Konzept von Richtlinien. In MSAL.NET bedeutet die Angabe einer Richtlinie die Bereitstellung einer Autorität.

- Wenn Sie eine öffentliche Clientanwendung instanziieren, müssen Sie die Richtlinie als Bestandteil der zuständigen Stelle angeben.
- Wenn Sie eine Richtlinie anwenden möchten, rufen Sie eine Überschreibung von `AcquireTokenInteractive` auf, die den Parameter `authority` akzeptiert.

Dieser Artikel gilt für MSAL.NET 3.x. Informationen zu MSAL.NET 2.x finden Sie unter [Besonderheiten von Azure AD B2C bei MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) im Wiki für MSAL.NET auf GitHub.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Autorität für einen Azure AD B2C-Mandanten und eine Richtlinie

Das Autoritätsformat für Azure AD B2C lautet: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname`: Der Name des Azure AD B2C-Mandanten zzgl. Host, z. B. *contosob2c.b2clogin.com*.
- `tenant`: Der Domänenname oder die Verzeichnis-ID (Mandanten-ID) des Azure AD B2C-Mandanten, z. B. *contosob2c.onmicrosoft.com* bzw. eine GUID.
- `policyName`: Der Name des anzuwendenden Benutzerflows oder der benutzerdefinierten Richtlinie, z. B. eine Registrierungs-/Anmeldungsrichtlinie wie *b2c_1_susi*.

Weitere Informationen zu Azure AD B2C-Autoritäten finden Sie unter [Festlegen von Umleitungs-URLs auf b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Instanziieren der Anwendung

Geben Sie die Autorität an, indem Sie beim Erstellen des Anwendungsobjekts `WithB2CAuthority()` aufrufen:

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Abrufen eines Tokens zum Anwenden einer Richtlinie

Beim Anfordern eines Tokens für eine geschützte Azure AD B2C-API in einer öffentlichen Clientanwendung müssen Sie die Überschreibungen mit einer Autorität verwenden:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

Im vorherigen Codeausschnitt gilt Folgendes:

- `policy` ist eine Zeichenfolge, die den Namen des Azure AD B2C-Benutzerflows oder der benutzerdefinierten Richtlinie (z. B. `PolicySignUpSignIn`) enthält.
- `ParentActivityOrWindow` ist erforderlich für Android (die Aktivität) und optional für andere Plattformen, die eine übergeordnete Benutzeroberfläche (z. B. Fenster in Microsoft Windows und UIViewController in iOS) unterstützen. Weitere Informationen zum Dialogfeld für die Benutzeroberfläche finden Sie unter [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) im MSAL-Wiki.
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` ist eine Methode, die ein Konto für eine bestimmte Richtlinie findet. Beispiel:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
      foreach (var account in accounts)
      {
          string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
          if (userIdentifier.EndsWith(policy.ToLower()))
              return account;
      }
      return null;
  }
  ```

Das Anwenden eines Benutzerflows bzw. einer benutzerdefinierten Richtlinie (z. B. um dem Benutzer das Bearbeiten seines Profils oder das Zurücksetzen seines Kennworts zu ermöglichen) erfolgt derzeit durch einen Aufruf von `AcquireTokenInteractive`. Bei diesen beiden Richtlinien wird das zurückgegebene Token/Authentifizierungsergebnis nicht verwendet.

## <a name="profile-edit-policies"></a>Richtlinien zum Bearbeiten von Profilen

Damit Benutzer sich mit einer Social Media-Identität anmelden und ihr Profil bearbeiten können, wenden Sie die Azure AD B2C-Profilbearbeitungsrichtlinie an.

Rufen Sie hierzu `AcquireTokenInteractive` mit der Autorität für diese Richtlinie auf. Da der Benutzer bereits angemeldet ist und über eine aktive Cookie-Sitzung verfügt, können Sie mit `Prompt.NoPrompt` verhindern, dass das Dialogfeld für die Kontoauswahl angezeigt wird.

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
    try
    {
        var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                            .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                            .WithPrompt(Prompt.NoPrompt),
                            .WithB2CAuthority(App.AuthorityEditProfile)
                            .ExecuteAsync();
        DisplayBasicTokenInfo(authResult);
    }
    catch
    {
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Kennwortanmeldeinformationen des Ressourcenbesitzers (ROPC)

Weitere Informationen zum ROPC-Flow (Resource Owner Password Credentials) finden Sie unter [Gewährung für Kennwortanmeldeinformationen des Ressourcenbesitzers (ROPC)](v2-oauth-ropc.md).

Der ROPC-Flow wird **nicht empfohlen**, da die Abfrage des Benutzerkennworts durch eine Anwendung nicht sicher ist. Weitere Informationen zu diesem Problem finden Sie unter [Wie lautet die Lösung für die zunehmenden Probleme mit Kennwörtern?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)

Durch die Verwendung von Benutzername/Kennwort in einem ROPC-Flow setzen Sie vieles aufs Spiel:

- Grundsätze moderner Identitäten – Das Kennwort kann ausgespäht oder wiedergegeben werden, da das gemeinsame Geheimnis abgefangen werden kann. Definitionsgemäß ist ROPC nicht mit kennwortlosen Flows kompatibel.
- Benutzer, die eine mehrstufige Authentifizierung (MFA) benötigen, können sich nicht anmelden (da es keine Interaktion gibt).
- Benutzer können das einmalige Anmelden (SSO) nicht verwenden.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurieren des ROPC-Flows in Azure AD B2C

Erstellen Sie in Ihrem Azure AD B2C-Mandanten einen neuen Benutzerflow, und wählen Sie **Mit ROPC anmelden** aus, um ROPC für den Benutzerflow zu aktivieren. Weitere Informationen finden Sie unter [Konfigurieren des ROPC-Flows](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` enthält die Methode `AcquireTokenByUsernamePassword`:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Die Methode `AcquireTokenByUsernamePassword` verwendet die folgenden Parameter:

- Die *Bereiche*, für die ein Zugriffstoken abgerufen werden soll.
- Einen *Benutzernamen*.
- Ein *Kennwort* als SecureString für den Benutzer.

### <a name="limitations-of-the-ropc-flow"></a>Einschränkungen für den ROPC-Flow

Der ROPC-Flow **funktioniert nur bei lokalen Konten**, für die sich der Benutzer mit E-Mail-Adresse oder Benutzername bei Azure AD B2C registriert hat. Dieser Flow funktioniert nicht bei einem Verbund mit einem von Azure AD B2C unterstützten externen Identitätsanbieter (Facebook, Google usw.).

## <a name="google-auth-and-embedded-webview"></a>Google-Authentifizierung und eingebettete Webansicht

Wenn Sie Google als Identitätsanbieter verwenden, sollten Sie den Systembrowser verwenden, da Google [Authentifizierungen über eingebettete Webansichten](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) nicht zulässt. Derzeit ist `login.microsoftonline.com` eine vertrauenswürdige Autorität für Google und funktioniert mit eingebetteten Webansichten. `b2clogin.com` ist jedoch keine vertrauenswürdige Autorität für Google, sodass sich die Benutzer nicht authentifizieren können.

Wenn sich Änderungen ergeben, werden wir ein Update zu diesem [Problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) bereitstellen.

## <a name="token-caching-in-msalnet"></a>Zwischenspeichern von Token in MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Bekanntes Problem mit Azure AD B2C

MSAL.NET unterstützt einen [Tokencache](/dotnet/api/microsoft.identity.client.tokencache). Der Schlüssel für den Tokencache basiert auf den Ansprüchen, die vom Identitätsanbieter (IdP) zurückgegeben werden.

Derzeit benötigt MSAL.NET zwei Ansprüche, um einen Tokencacheschlüssel zu erstellen:

- `tid` (die Azure AD-Mandanten-ID)
- `preferred_username`

Beide Ansprüche fehlen möglicherweise in Azure AD B2C-Szenarien, da sie nicht von allen Social Media-Identitätsanbietern (Facebook, Google und andere) in den an Azure AD B2C zurückgegebenen Token zurückgegeben werden.

Die Rückgabe der Meldung von MSAL.NET `Missing from the token response`, wenn Sie in von Azure AD B2C ausgegebenen Token auf den Anspruchswert `preferred_username` zugreifen, ist ein Zeichen für ein solches Szenario. MSAL verwendet den Wert `Missing from the token response` für `preferred_username`, um die bibliotheksübergreifende Cachekompatibilität zu bewahren.

### <a name="workarounds"></a>Problemumgehungen

#### <a name="mitigation-for-missing-tenant-id"></a>Lösung bei fehlender Mandanten-ID

Als Problemumgehung wird das zuvor beschriebene [Zwischenspeichern nach Richtlinie](#acquire-a-token-to-apply-a-policy) vorgeschlagen.

Alternativ können Sie den Anspruch `tid` verwenden, wenn Sie [benutzerdefinierte Richtlinien](../../active-directory-b2c/custom-policy-get-started.md) in Azure AD B2C verwenden. Benutzerdefinierte Richtlinien können mithilfe von [Anspruchstransformationen](../../active-directory-b2c/claims-transformation-technical-profile.md) zusätzliche Ansprüche an Ihre Anwendung zurückgeben.

#### <a name="mitigation-for-missing-from-the-token-response"></a>Problemumgehung für „Fehlt in der Tokenantwort“

Eine Möglichkeit besteht darin, anstelle von `preferred_username` den Anspruch `name` zu verwenden. Wenn Sie den Anspruch `name` in von Azure AD B2C ausgestellten ID-Token einschließen möchten, wählen Sie beim Konfigurieren des Benutzerflows **Anzeigename** aus.

Weitere Informationen zum Angeben der Ansprüche, die von Ihrem Benutzerflow zurückgegeben werden sollen, finden Sie im [Tutorial: Erstellen von Benutzerflows in Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum interaktiven Abrufen von Token mit MSAL.NET für Azure AD B2C-Anwendungen finden Sie im folgenden Beispiel.

| Beispiel | Plattform | BESCHREIBUNG|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Eine Xamarin Forms-App, die MSAL.NET zum Authentifizieren von Benutzern über Azure AD B2C verwendet und dann mit den zurückgegebenen Token eine Web-API aufruft.|
