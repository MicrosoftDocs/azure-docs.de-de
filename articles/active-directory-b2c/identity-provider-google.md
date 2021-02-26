---
title: Einrichten der Registrierung und Anmeldung mit einem Google-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Google-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
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
ms.openlocfilehash: c89e7e948b8a48c7e8d43950ffc2748efff6f51b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953868"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Google-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-google-application"></a>Erstellen einer Google-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem Google-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung in der [Google Developers Console](https://console.developers.google.com/) erstellen. Weitere Informationen finden Sie unter [Einrichten von OAuth 2.0](https://support.google.com/googleapi/answer/6158849). Wenn Sie noch über kein Google-Konto verfügen, können Sie sich unter [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) registrieren.

1. Melden Sie sich bei der [Google Developers Console](https://console.developers.google.com/) mit den Anmeldeinformationen für Ihr Google-Konto an.
1. Wählen Sie in der oberen linken Ecke der Seite die Projektliste aus, und wählen Sie dann **Neues Projekt** aus.
1. Geben Sie einen **Projektnamen** ein, und wählen Sie **Erstellen** aus.
1. Stellen Sie sicher, dass Sie das neue Projekt verwenden. Wählen Sie dazu oben links auf dem Bildschirm die Dropdownliste „Projekt“ aus. Wählen Sie Ihr Projekt (anhand des Namens) aus, und wählen Sie dann **Öffnen** aus.
1. Wählen Sie im linken Menü die Option **OAuth-Zustimmungsbildschirm** aus, wählen Sie **Extern** aus, und wählen Sie dann **Erstellen** aus.
Geben Sie einen **Namen** für Ihre Anwendung ein. Geben Sie im Abschnitt *Autorisierte Domänen* die Zeichenfolge **b2clogin.com** ein, und wählen Sie **Speichern** aus.
1. Wählen Sie im linken Menü die Option **Credentials** (Anmeldeinformationen) und anschließend **Create credentials** > **Oauth client ID** (Anmeldeinformationen erstellen > OAuth-Client-ID) aus.
1. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus.
1. Geben Sie einen **Namen** für die Anwendung an, und geben Sie `https://your-tenant-name.b2clogin.com` unter **Authorized JavaScript origins** (Autorisierte JavaScript-Quellen) und `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **Authorized redirect URIs** (Autorisierte Umleitungs-URIs) ein. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Verwenden Sie bei der Eingabe Ihres Mandantennamens nur Kleinbuchstaben, auch wenn der Mandant in Azure AD B2C Großbuchstaben enthält.
1. Klicken Sie auf **Erstellen**.
1. Kopieren Sie die Werte für **Client-ID** und **Clientgeheimnis**. Sie benötigen beide Angaben, um Google als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. Das **Clientgeheimnis** ist eine wichtige Anmeldeinformation.

::: zone pivot="b2c-user-flow"

## <a name="configure-google-as-an-identity-provider"></a>Konfigurieren von Google als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **Google** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *Google*.
1. Geben Sie für die **Client-ID** die Client-ID der Google-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie für **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-google-identity-provider-to-a-user-flow"></a>Hinzufügen von Google als Identitätsanbieter zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Google als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Google** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Manual`.
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `GoogleSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
9. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
10. Klicken Sie auf **Erstellen**.

## <a name="configure-google-as-an-identity-provider"></a>Konfigurieren von Google als Identitätsanbieter

Damit sich Benutzer mit einem Google-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein Google-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAuth2">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
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

4. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
5. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [Google-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).