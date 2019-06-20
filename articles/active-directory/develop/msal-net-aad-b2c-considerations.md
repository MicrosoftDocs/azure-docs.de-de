---
title: Azure AD B2C (Microsoft-Authentifizierungsbibliothek für .NET) | Azure
description: Lernen Sie bestimmte Überlegungen zur Verwendung von Azure AD B2C mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) kennen.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8240a487bdb01cdbe9017ddc7cb95ce4fc0e1503
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052368"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Verwenden von MSAL.NET zur Anmeldung von Benutzern mit Identitäten sozialer Netzwerke

Sie können MSAL.NET zur Anmeldung von Benutzern mit Identitäten sozialer Netzwerke über [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c) verwenden. Azure AD B2C basiert auf dem Konzept von Richtlinien. In MSAL.NET bedeutet die Angabe einer Richtlinie die Bereitstellung einer Autorität.

- Wenn Sie eine öffentliche Clientanwendung instanziieren, müssen Sie die Richtlinie in der Autorität angeben.
- Wenn Sie eine Richtlinie anwenden möchten, müssen Sie eine Überschreibung von `AcquireTokenInteractive` aufrufen, die einen `authority`-Parameter enthält.

Diese Seite gilt für MSAL 3.x. Wenn Sie Informationen zu MSAL 2.x suchen, lesen Sie unter [AAD B2C Specifics in MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) (Besonderheiten von Azure AD B2C in MSAL 2.x) nach.

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorität für Azure AD B2C-Mandanten und -Richtlinie

Die zu verwendende Autorität ist `https://login.microsoftonline.com/tfp/{tenant}/{policyName}`. Dabei gilt Folgendes:

- `tenant` ist der Name des Azure AD B2C-Mandanten. 
- `policyName` ist der Name der Richtlinie (z. B. „b2c_1_susi“ für die Registrierung/Anmeldung), die angewandt werden soll.

Derzeit wird für Azure AD B2C empfohlen, `b2clogin.com` als Autorität zu verwenden. Beispiel: `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Weitere Informationen finden Sie in dieser [Dokumentation](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Instanziieren der Anwendung

Sie müssen beim Erstellen der Anwendung die Autorität angeben.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Abrufen eines Tokens zum Anwenden einer Richtlinie

Das Anfordern eines Tokens für eine durch Azure AD B2C geschützte API in einer öffentlichen Clientanwendung erfordert die Verwendung der Überschreibungen mit einer Autorität:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

durch:

- `policy` ist eine der vorherigen Zeichenfolgen (z. B. `PolicySignUpSignIn`).
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

Das Anwenden einer Richtlinie (z. B. um Endbenutzern das Bearbeiten des eigenen Profils oder das Zurücksetzen des eigenen Kennworts zu erlauben) erfolgt derzeit durch einen Aufruf von `AcquireTokenInteractive`. Im Fall dieser beiden Richtlinien verwenden Sie das zurückgegebene Token/Authentifizierungsergebnis nicht.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Sonderfall der Richtlinien EditProfile und ResetPassword

Wenn Sie eine Umgebung bereitstellen möchten, in der Ihre Endbenutzer sich mit der Identität eines sozialen Netzwerks anmelden und dann ihr Profil bearbeiten können, sollten Sie die EditProfile-Richtlinie von Azure AD B2C anwenden. Rufen Sie dazu `AcquireTokenInteractive` mit der Autorität für diese Richtlinie auf, und legen Sie die Aufforderung auf `Prompt.NoPrompt` fest, damit den Benutzern kein Dialogfeld zur Kontoauswahl angezeigt wird (da die Benutzer bereits angemeldet sind).

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
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Ressourcenbesitzer-Kennwortanmeldeinformationen (ROPC) mit Azure AD B2C
Weitere Informationen zum ROPC-Flow finden Sie in dieser [Dokumentation](v2-oauth-ropc.md).

Dieser Flow wird **nicht empfohlen**, da die Abfrage des Kennworts von einem Benutzer durch eine Anwendung nicht sicher ist. Weitere Informationen zu diesem Problem finden Sie in [diesem Artikel](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Die Verwendung von Benutzername/Kennwort birgt eine Reihe von Nachteilen und Risiken:
- Core-Mandanten mit moderner Identität: Kennwörter werden durch Phishing entwendet und wiedergegeben. Dies liegt am Konzept des gemeinsamen geheimen Schlüssels, der abgefangen werden kann. Dies ist inkompatibel mit einem Szenario ohne Kennwort.
- Benutzer, die eine mehrstufige Authentifizierung (MFA) benötigen, können sich nicht anmelden (da es keine Interaktion gibt).
- Die Benutzer können einmaliges Anmelden nicht verwenden.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurieren des ROPC-Flows in Azure AD B2C
Erstellen Sie in Ihrem Azure AD B2C-Mandanten einen neuen Benutzerflow, und wählen Sie **Mit ROPC anmelden** aus. Dadurch wird die ROPC-Richtlinie für Ihren Mandanten aktiviert. Weitere Einzelheiten finden Sie unter [Konfigurieren des ROPC-Flows](/azure/active-directory-b2c/configure-ropc).

`IPublicClientApplication` enthält eine Methode:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Diese Methode nimmt folgende Parameter an:
- Die *Bereiche* zum Anfordern eines Zugriffstokens.
- Einen *Benutzernamen*.
- Ein *Kennwort* als SecureString für den Benutzer.

Denken Sie daran, die Autorität mit der ROPC-Richtlinie zu verwenden.

### <a name="limitations-of-the-ropc-flow"></a>Einschränkungen für den ROPC-Flow
 - Der ROPC-Flow **funktioniert nur für lokale Konten** (die Anmeldung bei Azure AD B2C erfolgt mit E-Mail-Adresse oder Benutzername). Dieser Flow funktioniert nicht bei einem Verbund mit einem von Azure AD B2C unterstützten Identitätsanbieter (Facebook, Google usw.).
 - Derzeit wird **kein ID-Token Azure AD B2C zurückgegeben**, wenn Sie den ROPC-Flow von MSAL implementieren. Dies bedeutet, dass kein Kontoobjekt erstellt werden kann. Aus diesem Grund liegen im Cache kein Konto und kein Benutzer vor. Der AcquireTokenSilent-Flow funktioniert bei diesem Szenario nicht. Allerdings zeigt ROPC keine Benutzeroberfläche an, sodass dies keine Auswirkungen auf die Benutzerfreundlichkeit hat.

## <a name="google-auth-and-embedded-webview"></a>Google-Authentifizierung und eingebettete Webansicht

Wenn Sie ein Azure AD B2C-Entwickler sind und Google als Identitätsanbieter verwenden, sollten Sie den Systembrowser verwenden, da Google [Authentifizierungen über eingebettete Webansichten](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) nicht zulässt. Derzeit ist `login.microsoftonline.com` eine vertrauenswürdige Autorität für Google. Die Verwendung dieser Autorität funktioniert bei eingebetteten Webansichten. `b2clogin.com` ist jedoch keine vertrauenswürdige Autorität für Google, sodass sich die Benutzer nicht authentifizieren können.

Wir stellen im Wiki und unter diesem [Problem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) ein Update bereit, wenn dies geändert wurde.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Zwischenspeicherung mit Azure AD B2C in MSAL.NET 

### <a name="known-issue-with-azure-ad-b2c"></a>Bekanntes Problem mit Azure AD B2C

MSAL.NET unterstützt einen [Tokencache](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Der Schlüssel für den Tokencache basiert auf den Ansprüchen vom Identitätsanbieter. Derzeit erfordert MSAL.NET zwei Ansprüche, um einen Tokencacheschlüssel zu erstellen:  
- `tid` (die Azure AD-Mandanten-ID) und 
- `preferred_username` 

Diese beiden Ansprüche fehlen in vielen Azure AD B2C-Szenarien. 

Für Kunden bedeutet dies, dass beim Versuch, das Feld mit dem Benutzernamen anzuzeigen, als Wert zurückgegeben wird, dass dieser in der Tokenantwort fehlt. Wenn dies der Fall ist, hat Azure AD B2C im ID-Token keinen Wert für „preferred_username“ zurückgegeben, da für Konten sozialer Netzwerke und externe Identitätsanbieter (IdP) einige Einschränkungen gelten. Azure AD gibt einen Wert für „preferred_username“ zurück, wenn bekannt ist, wer der Benutzer ist. Da sich ein Benutzer aber bei Azure AD B2C mit einem lokalen Konto oder mit Facebook, Google, GitHub usw. anmelden kann, gibt es für Azure AD B2C keinen einheitlichen Wert für „preferred_username“. Um Cachekompatibilität zwischen MSAL und ADAL erreichen zu können, haben wir beschlossen, das „Fehlen in der Tokenantwort“ auf unserer Seite bei Azure AD B2C-Konten zu verwenden, wenn im ID-Token nicht für „preferred_username“ zurückgegeben wird. MSAL muss einen Wert für „preferred_username“ zurückgeben, um Cachekompatibilität zwischen den Bibliotheken zu erzielen.

### <a name="workarounds"></a>Problemumgehungen

#### <a name="mitigation-for-the-missing-tenant-id"></a>Lösung bei fehlenden Mandanten-IDs

Zur Umgehung dieses Problems sollten Sie das [Zwischenspeichern nach Richtlinie](#acquire-a-token-to-apply-a-policy) verwenden.

Alternativ können Sie den `tid`-Anspruch verwenden, wenn Sie die [benutzerdefinierten B2C-Richtlinien](https://aka.ms/ief) verwenden, da er die Möglichkeit bietet, zusätzliche Ansprüche an die Anwendung zurückzugeben. Erfahren Sie mehr über die [Transformation von Ansprüchen](/azure/active-directory-b2c/claims-transformation-technical-profile).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Problemumgehung für „Fehlt in der Tokenantwort“
Eine Möglichkeit ist es, den „Name“-Anspruch als bevorzugten Benutzernamen zu verwenden. Die Vorgehensweise wird in diesem [B2C-Dokument](../../active-directory-b2c/active-directory-b2c-reference-policies.md) wie folgt beschrieben: „Wählen Sie in der Spalte mit den zurückgegebenen Ansprüchen diejenigen aus, die nach einer erfolgreichen Profilbearbeitung in den Autorisierungstoken an die Anwendung zurückgegeben werden sollen. Wählen Sie z. B. den Anzeigenamen oder die Postleitzahl aus.“

## <a name="next-steps"></a>Nächste Schritte 

Weitere Informationen zum interaktiven Abrufen von Token mit MSAL.NET für Azure AD B2C-Anwendungen finden Sie im folgenden Beispiel.

| Beispiel | Plattform | BESCHREIBUNG|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Eine einfache Xamarin.Forms-App, die das Verwenden von MSAL.NET zum Authentifizieren von Benutzern über Azure AD B2C und den Zugriff auf eine Web-API mit den daraus resultierenden Token veranschaulicht.|
