---
title: Einrichten der Registrierung und Anmeldung mit einem Twitter-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Twitter-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 65d3badc02efbb02df50189885c28a8abe851415
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050446"
---
# <a name="set-up-sign-up-and-sign-in-with-a-twitter-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Twitter-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]
::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-application"></a>Erstellen einer Anwendung

Um die Benutzeranmeldung mit einem Twitter-Konto in Azure AD B2C zu aktivieren, müssen Sie eine Twitter-Anwendung erstellen. Wenn Sie noch über kein Twitter-Konto verfügen, können Sie eines unter [https://twitter.com/signup](https://twitter.com/signup) registrieren. Außerdem müssen Sie [ein Entwicklerkonto beantragen](https://developer.twitter.com/en/apply/user.html). Weitere Informationen finden Sie unter [Zugriff beantragen](https://developer.twitter.com/en/apply-for-access).

1. Melden Sie sich im [Twitter-Entwicklerportal](https://developer.twitter.com/portal/projects-and-apps) mit den Anmeldeinformationen für Ihr Twitter-Konto an.
1. Wählen Sie unter **Eigenständige Apps** die Option **+App erstellen** aus.
1. Geben Sie einen **App-Namen** ein, und wählen Sie dann **Fertig** aus.
1. Kopieren Sie den Wert des **App-Schlüssels** und des **geheimen API-Schlüssels**.  Sie benötigen beide Angaben, um Twitter als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. 
1. Wählen Sie unter **App einrichten** die Option **App-Einstellungen** aus.
1. Wählen Sie unter **Authentifizierungseinstellungen** die Option **Bearbeiten** aus.
    1. Aktivieren Sie das Kontrollkästchen **Dreibeiniges OAuth aktivieren**.
    1. Aktivieren Sie das Kontrollkästchen **E-Mail-Adresse von Benutzern anfordern**.
    1. Geben Sie als **Rückruf-URLs** Folgendes ein: `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/your-user-flow-Id/oauth1/authresp`. Ersetzen Sie `your-tenant` durch den Namen Ihres Mandanten und `your-user-flow-Id` durch den Bezeichner Ihres Benutzerflows. Beispiel: `b2c_1a_signup_signin_twitter`. Geben Sie den Mandantennamen und die Benutzerflow-ID selbst dann in Kleinbuchstaben ein, wenn sie in Azure AD B2C Großbuchstaben enthalten.
    1. Geben Sie für die **Website-URL** die URL `https://your-tenant.b2clogin.com` ein. Ersetzen Sie `your-tenant` durch den Namen Ihres Mandanten. Beispiel: `https://contosob2c.b2clogin.com`.
    1. Geben Sie eine URL für die **Nutzungsbedingungen** ein, z. B. `http://www.contoso.com/tos`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Nutzungsbedingungen Ihrer Anwendung enthält.
    1. Geben Sie eine URL für die **Datenschutzrichtlinie** ein, z. B. `http://www.contoso.com/privacy`. Die Richtlinien-URL ist eine von Ihnen verwaltete Seite, die Datenschutzinformationen zu Ihrer Anwendung enthält.
    1. Wählen Sie **Speichern** aus.

::: zone pivot="b2c-user-flow"

## <a name="configure-twitter-as-an-identity-provider"></a>Konfigurieren von Twitter als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Twitter** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *Twitter*.
1. Geben Sie für die **Client-ID** den *API-Schlüssel* der Twitter-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie den zuvor notierten *geheimen API-Schlüssel* unter **Geheimer Clientschlüssel** ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-twitter-identity-provider-to-a-user-flow"></a>Hinzufügen von Twitter als Identitätsanbieter zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow aus, dem Sie Twitter als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Twitter** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Schlüssel, den Sie zuvor notiert haben, in Ihrem Azure AD B2C-Mandanten speichern.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Manual`.
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `TwitterSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
9. Wählen Sie für **Schlüsselverwendung** die Option `Encryption` aus.
10. Klicken Sie auf **Erstellen**.

## <a name="configure-twitter-as-an-identity-provider"></a>Konfigurieren von Twitter als Identitätsanbieter

Wenn Sie möchten, dass sich Benutzer mit einem Twitter-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein Twitter-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>twitter.com</Domain>
      <DisplayName>Twitter</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Twitter-OAuth1">
          <DisplayName>Twitter</DisplayName>
          <Protocol Name="OAuth1" />
          <Metadata>
            <Item Key="ProviderName">Twitter</Item>
            <Item Key="authorization_endpoint">https://api.twitter.com/oauth/authenticate</Item>
            <Item Key="access_token_endpoint">https://api.twitter.com/oauth/access_token</Item>
            <Item Key="request_token_endpoint">https://api.twitter.com/oauth/request_token</Item>
            <Item Key="ClaimsEndpoint">https://api.twitter.com/1.1/account/verify_credentials.json?include_email=true</Item>
            <Item Key="ClaimsResponseFormat">json</Item>
            <Item Key="client_id">Your Twitter application API key</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_TwitterSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Ersetzen Sie den Wert von **client_id** durch den *geheimen API-Schlüssel*, den Sie zuvor notiert haben.
5. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAuth1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]


::: zone-end
