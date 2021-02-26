---
title: Einrichten der Registrierung und Anmeldung mit einem Salesforce-Konto
titleSuffix: Azure AD B2C
description: Bereitstellen von Registrierung und Anmeldung für Kunden mit Salesforce-Konten in Ihren Anwendungen mithilfe von Azure Active Directory B2C
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
ms.openlocfilehash: 0981687b03344daf7a447cc4d9e50f0923341340
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98952290"
---
# <a name="set-up-sign-up-and-sign-in-with-a-salesforce-account-using-azure-active-directory-b2c"></a>Einrichten der Registrierung und Anmeldung mit einem Salesforce-Konto mithilfe von Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="create-a-salesforce-application"></a>Erstellen einer Salesforce-Anwendung

Damit Sie die Anmeldung für Benutzer mit einem Salesforce-Konto in Azure Active Directory B2C (Azure AD B2C) aktivieren können, müssen Sie in Ihrem [Anwendungs-Manager](https://login.salesforce.com/) in Salesforce eine Anwendung erstellen. Weitere Informationen finden Sie unter [Konfigurieren der grundlegenden Einstellungen für verbundene Anwendungen](https://help.salesforce.com/articleView?id=connected_app_create_basics.htm) und [Aktivieren von OAuth-Einstellungen für die API-Integration](https://help.salesforce.com/articleView?id=connected_app_create_api_integration.htm).

1. [Anmelden Sie sich bei Salesforce an](https://login.salesforce.com/).
1. Klicken Sie im Menü auf **Setup**.
1.  Erweitern Sie **Anwendungen**, und wählen Sie dann **Anwendungs-Manager** aus.
1. Klicken Sie auf **Neue verbundene Anwendung**.
1. Geben Sie unter **Basic Information** (Grundlegende Informationen) Folgendes ein:
    1. **Connected App Name** (Name der verbundenen Anwendung): Der Name der verbundenen Anwendung wird im Anwendungs-Manager und auf der zugehörigen App Launcher-Kachel angezeigt. Der Name muss innerhalb Ihrer Organisation eindeutig sein. 
    1. **API-Name** 
    1. **Contact Email** (Kontakt-E-Mail-Adresse): Geben Sie hier die Kontakt-E-Mail-Adresse für Salesforce an.
1. Wählen Sie unter **API (OAuth-Einstellungen aktivieren)** die Option **OAuth-Einstellungen aktivieren** aus.
    1. Geben Sie unter **Rückmeldungs-URL** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` an. Ersetzen Sie `your-tenant-name` durch den Namen Ihres Mandanten. Bei der Eingabe Ihres Mandantennamens dürfen Sie nur Kleinbuchstaben verwenden, auch wenn der Mandant in Azure AD B2C Großbuchstaben enthält.
    1. Wählen Sie unter **Selected OAuth Scopes** (Ausgewählte OAuth-Bereiche) **Zugriff auf Ihre grundlegenden Informationen (id, profile, email, address, phone)** und **Zugriff auf Ihre eindeutige Kennung zulassen (openid)** aus.
    1. Wählen Sie **Geheimnis für Webserver-Flow erforderlich** aus.
1. Wählen Sie **Configure ID Token** (ID-Token konfigurieren) aus. 
    1. Legen Sie für **Token Valid for** (Token gültig für) 5 Minuten fest.
    1. Wählen Sie **Include Standard Claims** (Standardansprüche einschließen) aus.
1. Klicken Sie auf **Speichern**.
1. Kopieren Sie die Werte für **Consumer Key** (Consumerschlüssel) und **Consumer Secret** (Consumergeheimnis). Diese beiden Angaben benötigen Sie, um Salesforce als Identitätsanbieter in Ihrem Mandanten zu konfigurieren. Das **Clientgeheimnis** ist eine wichtige Anmeldeinformation.

::: zone pivot="b2c-user-flow"

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurieren von Salesforce als Identitätsanbieter

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den **Verzeichnis- und Abonnementfilter** aus, und wählen Sie das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Geben Sie einen **Namen** ein. Geben Sie z. B. *Salesforce* ein.
1. Geben Sie für **Metadaten-URL** die URL des [Salesforce OpenID Connect Configuration-Dokuments](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm) ein. Bei einer Sandbox wird „login.salesforce.com“ durch „test.salesforce.com“ ersetzt. Für eine Community wird „login.salesforce.com“ durch die Community-URL ersetzt, z. B. „username.force.com/.well-known/openid-configuration“. Die URL muss eine HTTPS-URL sein.

    ```
    https://login.salesforce.com/.well-known/openid-configuration
    ```

1. Geben Sie für **Client-ID** die zuvor notierte Anwendungs-ID ein.
1. Geben Sie im Feld **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Geben Sie für den **Bereich** das `openid id profile email` ein.
1. Übernehmen Sie die Standardwerte für **Antworttyp** und **Antwortmodus**.
1. (Optional) Geben Sie als **Domänenhinweis** die Zeichenfolge `contoso.com` ein. Weitere Informationen finden Sie unter [Einrichten einer direkten Anmeldung mit Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Wählen Sie unter **Zuordnung von Identitätsanbieteransprüchen** die folgenden Ansprüche aus:

    - **Benutzer-ID**: *Abonnement*
    - **Anzeigename**: *name*
    - **Vorname**: *given_name*
    - **Nachname**: *family_name*
    - **E-Mail**: *email*

1. Wählen Sie **Speichern** aus.

## <a name="add-salesforce-identity-provider-to-a-user-flow"></a>Hinzufügen des Salesforce-Identitätsanbieters zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie den Salesforce-Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Social identity providers** (Soziales Netzwerk als Identitätsanbieter) die Option **Salesforce** aus.
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
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `SalesforceSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
9. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
10. Klicken Sie auf **Erstellen**.

## <a name="configure-salesforce-as-an-identity-provider"></a>Konfigurieren von Salesforce als Identitätsanbieter

Wenn Sie Benutzern die Anmeldung mit einem Salesforce-Konto ermöglichen möchten, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können ein Salesforce-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem **ClaimsProviders**-Element hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>salesforce.com</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Salesforce-OpenIdConnect">
          <DisplayName>Salesforce</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://login.salesforce.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid id profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">Your Salesforce application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_SalesforceSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="salesforce.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Für **METADATA** ist die URL des [Salesforce OpenID Connect Configuration-Dokuments](https://help.salesforce.com/articleView?id=remoteaccess_using_openid_discovery_endpoint.htm) festgelegt. Bei einer Sandbox wird „login.salesforce.com“ durch „test.salesforce.com“ ersetzt. Für eine Community wird „login.salesforce.com“ durch die Community-URL ersetzt, z. B. „username.force.com/.well-known/openid-configuration“. Die URL muss eine HTTPS-URL sein.
5. Legen Sie **client_id** auf die Anwendungs-ID aus der Anwendungsregistrierung fest.
6. Speichern Sie die Datei .

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="Salesforce-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie das [Salesforce-Token an Ihre Anwendung übergeben](idp-pass-through-user-flow.md).
