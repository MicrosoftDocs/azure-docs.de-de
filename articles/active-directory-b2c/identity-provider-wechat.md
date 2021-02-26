---
title: Einrichten der Registrierung und Anmeldung mit einem WeChat-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit WeChat-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C.
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
ms.openlocfilehash: ddd41448820984497ae96142ca409774af7c7bf9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98954054"
---
# <a name="set-up-sign-up-and-sign-in-with-a-wechat-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem WeChat-Konto mithilfe von Azure Active Directory B2C


[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-wechat-application"></a>Erstellen einer WeChat-Anwendung

Wenn Sie die Anmeldung für Benutzer mit einem WeChat-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren möchten, müssen Sie eine Anwendung im [WeChat Management Center](https://open.weixin.qq.com/) erstellen. Wenn Sie noch über kein WeChat-Konto verfügen, können Sie unter [https://kf.qq.com](https://kf.qq.com/faq/161220Brem2Q161220uUjERB.html) eines erstellen.

### <a name="register-a-wechat-application"></a>Registrieren einer WeChat-Anwendung

1. Melden Sie sich unter [https://open.weixin.qq.com/](https://open.weixin.qq.com/) mit Ihren WeChat-Anmeldeinformationen an.
1. Wählen Sie **管理中心** (Verwaltungscenter) aus.
1. Führen Sie die Schritte zum Registrieren einer neuen Anwendung aus.
1. Geben Sie `https://your-tenant_name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` unter **授权回调域** (Rückruf-URL) ein. Wenn der Name Ihres Mandanten beispielsweise „contoso“ lautet, legen Sie die URL auf `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` fest.
1. Kopieren Sie die **APP-ID** und den **APP-SCHLÜSSEL**. Sie benötigen beide Angaben, um den Identitätsanbieter für Ihren Mandanten zu konfigurieren.

::: zone pivot="b2c-user-flow"

## <a name="configure-wechat-as-an-identity-provider"></a>Konfigurieren von WeChat als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Azure AD B2C-Mandanten an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
1. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **Azure AD B2C**, und klicken Sie darauf.
1. Wählen Sie **Identitätsanbieter** und dann **WeChat (Vorschau)** aus.
1. Geben Sie einen **Namen** ein. Beispiel: *WeChat*.
1. Geben Sie für die **Client-ID** die App-ID der WeChat-Anwendung ein, die Sie zuvor erstellt haben.
1. Geben Sie das zuvor notierte App-Geheimnis als **Geheimer Clientschlüssel** ein.
1. Wählen Sie **Speichern** aus.

## <a name="add-wechat-identity-provider-to-a-user-flow"></a>Hinzufügen von WeChat als Identitätsanbieter zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie WeChat als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **WeChat** aus.
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
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `WeChatSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
9. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
10. Klicken Sie auf **Erstellen**.

## <a name="configure-wechat-as-an-identity-provider"></a>Konfigurieren von WeChat als Identitätsanbieter

Damit sich Benutzer mit einem WeChat-Konto anmelden können, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein WeChat-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>wechat.com</Domain>
      <DisplayName>WeChat (Preview)</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="WeChat-OAuth2">
          <DisplayName>WeChat</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">wechat</Item>
            <Item Key="authorization_endpoint">https://open.weixin.qq.com/connect/qrconnect</Item>
            <Item Key="AccessTokenEndpoint">https://api.weixin.qq.com/sns/oauth2/access_token</Item>
            <Item Key="ClaimsEndpoint">https://api.weixin.qq.com/sns/userinfo</Item>
            <Item Key="scope">snsapi_login</Item>
            <Item Key="HttpBinding">GET</Item>
            <Item Key="AccessTokenResponseFormat">json</Item>
            <Item Key="ClientIdParamName">appid</Item>
            <Item Key="ClientSecretParamName">secret</Item>
            <Item Key="ExtraParamsInAccessTokenEndpointResponse">openid</Item>
            <Item Key="ExtraParamsInClaimsEndpointRequest">openid</Item>
            <Item Key="ResponseErrorCodeParamName">errcode</Item>
            <Item Key="external_user_identity_claim_id">unionid</Item>
          <Item Key="client_id">Your WeChat application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_WeChatSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="UserId" PartnerClaimType="unionid" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="wechat.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
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
    <ClaimsProviderSelection TargetClaimsExchangeId="WeChatExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="WeChatExchange" TechnicalProfileReferenceId="WeChat-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end
