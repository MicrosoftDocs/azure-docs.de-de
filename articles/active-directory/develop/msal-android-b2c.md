---
title: Azure AD B2C (MSAL für Android) | Azure
titleSuffix: Microsoft identity platform
description: Lernen Sie, was es bei der Verwendung von Azure AD B2C mit der Microsoft Authentication Library für Android (MSAL.Android) besonders zu beachten gilt.
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.openlocfilehash: 1c4f47fd771cfb92b3896963c96b39d9eb7d97b8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344877"
---
# <a name="use-msal-for-android-with-b2c"></a>Verwendung von MSAL für Android mit B2C

Microsoft Authentication Library (MSAL) ermöglicht es Anwendungsentwicklern, Benutzer mit Social Media-Identitäten und lokalen Identitäten mithilfe von [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/index.yml) zu authentifizieren. Azure AD B2C ist ein Identitätsverwaltungsdienst. Mit diesem Dienst können Sie anpassen und steuern, wie sich Kunden für Ihre Apps registrieren, anmelden und ihre Profile verwalten.

## <a name="configure-known-authorities-and-redirect-uri"></a>Konfigurieren bekannter Autoritäten und des Umleitungs-URI

In MSAL für Android werden B2C-Richtlinien (User Journeys) als einzelne Autoritäten konfiguriert.

Bei einer B2C-Anwendung mit zwei Richtlinien:
- Registrierung/Anmelden
    * `B2C_1_SISOPolicy` aufgerufen
- Profil bearbeiten
    * `B2C_1_EditProfile` aufgerufen

Die Konfigurationsdatei für die App würde zwei `authorities` deklarieren. Jeweils eine pro Richtlinie. Die `type`-Eigenschaft jeder Autorität ist `B2C`.

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri` muss sowohl in der APP-Konfiguration registriert sein als auch in  `AndroidManifest.xml`, um die Umleitung während des [Flusses zum Erteilen eines Autorisierungscodes](../../active-directory-b2c/authorization-code-flow.md) zu unterstützen.

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication initialisieren

`IPublicClientApplication` wird nach einer Factorymethode erstellt, damit die Anwendungskonfiguration asynchron analysiert werden kann.

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>Token interaktiv abrufen

Wenn Sie ein Token interaktiv mit MSAL abrufen möchten, erstellen Sie eine `AcquireTokenParameters`-Instanz, und stellen Sie diese für die `acquireToken`-Methode bereit. Die nachfolgende Tokenanforderung verwendet die Autorität `default`.

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>Token automatisch erneuern

Wenn Sie ein Token automatisch mit MSAL abrufen möchten, erstellen Sie eine `AcquireTokenSilentParameters`-Instanz, und stellen Sie diese für die `acquireTokenSilentAsync`-Methode bereit. Anders als bei der `acquireToken`-Methode muss die `authority` angegeben werden, um ein Token automatisch abzurufen.

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>Richtlinie angeben

Da Richtlinien in B2C als separate Autoritäten dargestellt werden, bedarf es zum Aufrufen einer nicht standardmäßigen Richtlinie entweder einer `fromAuthority`-Klausel beim Erstellen von `acquireToken` oder `acquireTokenSilent`-Parametern.  Beispiel:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>Umgang mit Richtlinien zur Kennwortänderung

Der Benutzerflow zur Registrierung oder Anmeldung für das lokale Konto zeigt „**Kennwort vergessen?** “ an. . Durch Klicken auf diesen Link wird nicht automatisch ein Benutzerflow zum Zurücksetzen des Kennworts ausgelöst.

Stattdessen wird der Fehlercode `AADB2C90118` an Ihre App zurückgegeben. Ihre Anwendung sollte diesen Fehlercode verarbeiten, indem sie einen bestimmten Benutzerflow zum Zurücksetzen des Kennworts ausführt.

Zum Abfangen eines Fehlercodes für die Kennwortzurücksetzung kann innerhalb Ihrer `AuthenticationCallback` die folgende Implementierung verwendet werden:

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>|AuthenticationResult verwenden

Die erfolgreiche Erfassung eines Tokens führt zu einem `IAuthenticationResult`-Objekt. Dies enthält das Zugriffstoken, die Benutzeransprüche und Metadaten.

### <a name="get-the-access-token-and-related-properties"></a>Abrufen des Zugriffstokens und damit verknüpfter Eigenschaften

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>Autorisiertes Konto erhalten

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken-Ansprüche

Die im IdToken zurückgegebenen Ansprüche werden vom Sicherheitstokendienst (Security Token Service, STS) und nicht durch MSAL aufgefüllt. Je nach dem verwendetem Identitätsanbieter (IdP) fehlen möglicherweise einige Ansprüche. Einige IdPs stellen den `preferred_username`-Anspruch derzeit nicht bereit. Da die MSAL diesen Anspruch zum Zwischenspeichern verwendet, wird stattdessen der Platzhalterwert `MISSING FROM THE TOKEN RESPONSE` benutzt. Weitere Informationen zu Ansprüchen für B2C IdToken finden Sie in der [Übersicht über Tokens in Azure Active Directory B2C](../../active-directory-b2c/tokens-overview.md#claims).

## <a name="managing-accounts-and-policies"></a>Konten und Richtlinien verwalten

B2C behandelt jede Richtlinie als separate Autorität. Daher sind die Zugriffs-, Aktualisierungs- und ID-Tokens, die von den einzelnen Richtlinien zurückgegeben werden, nicht austauschbar. Jede Richtlinie gibt also ein separates `IAccount`-Objekt zurück, dessen Tokens nicht zum Aufrufen anderer Richtlinien verwendet werden können.

Jede Richtlinie fügt dem Cache pro Benutzer eine `IAccount` hinzu. Wenn sich ein Benutzer bei einer App anmeldet und zwei Richtlinien aufruft, hat der Benutzer auch zwei `IAccount`s. Um diesen Benutzer aus dem Cache zu entfernen, müssen Sie für jede Richtlinie `removeAccount()` aufrufen.

Beim Erneuern von Tokens für eine Richtlinie mit `acquireTokenSilent` muss daher dieselbe `IAccount`verwendet werden, die bei vorherigen Aufrufen der Richtlinie an `AcquireTokenSilentParameters` zurückgegeben wurde. Wird ein von einer anderen Richtlinie zurückgegebenes Konto angegeben, führt dies zu einem Fehler.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory B2C (Azure AD B2C) finden Sie unter [Was ist Azure Active Directory B2C?](../../active-directory-b2c/overview.md)