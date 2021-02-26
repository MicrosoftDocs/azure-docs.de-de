---
title: Einrichten der Registrierung und Anmeldung mit einem Azure AD B2C-Konto von einem anderen Azure AD B2C-Mandanten
titleSuffix: Azure AD B2C
description: Stellen Sie die Registrierung und Anmeldung für Kunden mit Azure AD B2C-Konten von einem anderen Mandanten in Ihren Anwendungen mithilfe von Azure Active Directory B2C bereit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ea4def3cfaa19e27dc05e955bf97b41976ec2190
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98953919"
---
# <a name="set-up-sign-up-and-sign-in-with-an-azure-ad-b2c-account-from-another-azure-ad-b2c-tenant"></a>Einrichten der Registrierung und Anmeldung mit einem Azure AD B2C-Konto von einem anderen Azure AD B2C-Mandanten

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="overview"></a>Übersicht

In diesem Artikel wird beschrieben, wie Sie einen Verbund mit einem anderen Azure AD B2C-Mandanten einrichten. Wenn Ihre Anwendungen durch Ihren Azure AD B2C-Mandanten geschützt sind, ermöglicht dies Benutzern aus anderen Azure AD B2C-Mandanten die Anmeldung mit ihren vorhandenen Konten. Im folgenden Diagramm können sich Benutzer mit einem Konto, das vom Azure AD B2C-Mandanten von *Fabrikam* verwaltet wird, bei einer durch den Azure AD B2C-Mandanten von *Contoso* geschützten Anwendung anmelden. 

![Azure AD B2C-Verbund mit einem anderen Azure AD B2C-Mandanten](./media/identity-provider-azure-ad-b2c/azure-ad-b2c-federation.png)


## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-azure-ad-b2c-application"></a>Erstellen einer Azure AD B2C-Anwendung

Gehen Sie wie folgt vor, um die Anmeldung für Benutzer mit einem Konto von einem anderen Azure AD B2C-Mandanten (z. B. Fabrikam) in Ihrem Azure AD B2C-Mandanten (z. B. Contoso) zu ermöglichen:

1. Erstellen Sie einen [Benutzerflow](tutorial-create-user-flows.md)oder eine [benutzerdefinierte Richtlinie](custom-policy-get-started.md).
1. Erstellen Sie dann eine Anwendung in Azure AD B2C, wie in diesem Abschnitt beschrieben. 

Gehen Sie wie folgt vor, um eine Anwendung zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren anderen Azure AD B2C-Mandanten (z. B. „Fabrikam.com“) enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name** einen Namen für die Anwendung ein. Beispielsweise *ContosoApp*.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerflows)** aus.
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie dann die folgende URL in Kleinbuchstaben ein, und ersetzen Sie dabei `your-B2C-tenant-name` durch den Namen Ihres Azure AD B2C-Mandanten (z. B. Contoso).

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Beispiel: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Aktivieren Sie unter „Berechtigungen“ das Kontrollkästchen **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.
1. Wählen Sie auf der Seite **Azure AD B2C – App-Registrierungen** die von Ihnen erstellte Anwendung (z. B. *ContosoApp*) aus.
1. Notieren Sie die **Anwendungs-ID (Client)** , die auf der Seite „Übersicht“ der Anwendung angezeigt wird. Sie benötigen diesen Wert beim Konfigurieren des Identitätsanbieters im nächsten Abschnitt.
1. Wählen Sie im linken Menü unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie **Neuer geheimer Clientschlüssel**.
1. Geben Sie im Feld **Beschreibung** eine Beschreibung für das Clientgeheimnis ein. Beispielsweise *clientsecret1*.
1. Wählen Sie unter **Läuft ab** einen Zeitraum aus, für den das Geheimnis gültig ist, und wählen Sie dann **Hinzufügen** aus.
1. Notieren Sie den **Wert** des Geheimnisses. Sie benötigen diesen Wert beim Konfigurieren des Identitätsanbieters im nächsten Abschnitt.


::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurieren von Azure AD B2C als Identitätsanbieter

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält, für den Sie den Verbund konfigurieren möchten (z. B. Contoso). Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten (z. B. Contoso) enthält.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Geben Sie einen **Namen** ein. Geben Sie beispielsweise *Fabrikam* ein.
1. Geben Sie für **Metadaten-URL** die folgende URL ein, und ersetzen Sie dabei `{tenant}` durch den Domänennamen Ihres Azure AD B2C-Mandanten (z. B. Fabrikam). Ersetzen Sie `{policy}` durch den Namen der im anderen Mandanten konfigurierten Richtlinie:

    ```
    https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration
    ```

    Beispiel: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.

1. Geben Sie für **Client-ID** die zuvor notierte Anwendungs-ID ein.
1. Geben Sie im Feld **Geheimer Clientschlüssel** den zuvor notierten geheimen Clientschlüssel ein.
1. Geben Sie für den **Bereich** das `openid` ein.
1. Übernehmen Sie die Standardwerte für **Antworttyp** und **Antwortmodus**.
1. (Optional) Geben Sie für den **Domänenhinweis** den Namen der Domäne ein, die Sie für die [direkte Anmeldung](direct-signin.md#redirect-sign-in-to-a-social-provider) verwenden möchten. Beispielsweise *fabrikam.com*.
1. Wählen Sie unter **Zuordnung von Identitätsanbieteransprüchen** die folgenden Ansprüche aus:

    - **Benutzer-ID**: *Abonnement*
    - **Anzeigename**: *name*
    - **Vorname**: *given_name*
    - **Nachname**: *family_name*
    - **E-Mail**: *email*

1. Wählen Sie **Speichern** aus.

## <a name="add-azure-ad-b2c-identity-provider-to-a-user-flow"></a>Hinzufügen von Azure AD B2C als Identitätsanbieter zu einem Benutzerflow 

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten die Option **Benutzerflows** aus.
1. Klicken Sie auf den Benutzerflow, dem Sie Azure AD B2C als Identitätsanbieter hinzufügen möchten.
1. Wählen Sie unter **Soziales Netzwerk als Identitätsanbieter** die Option **Fabrikam** aus.
1. Wählen Sie **Speichern** aus.
1. Um die Richtlinie zu testen, wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *testapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**.
1. Wählen Sie auf der Registrierungs- oder Anmeldeseite die Option *Fabrikam* aus, um sich mit dem anderen Azure AD B2C-Mandanten anzumelden.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den zuvor von Ihnen erstellten Anwendungsschlüssel in Ihrem Azure AD B2C-Mandanten speichern.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält, für den Sie den Verbund konfigurieren möchten (z. B. Contoso). Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten (z. B. Contoso) enthält.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Wählen Sie **Richtlinienschlüssel** und dann **Hinzufügen** aus.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `FabrikamAppSecret`.  Das Präfix `B2C_1A_` wird dem Namen Ihres Schlüssels bei der Erstellung automatisch hinzugefügt. Im XML-Code im folgenden Abschnitt lautet der entsprechende Verweis darauf daher *B2C_1A_FabrikamAppSecret*.
1. Geben Sie im Feld **Geheimnis** den zuvor notierten geheimen Clientschlüssel ein.
1. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-azure-ad-b2c-as-an-identity-provider"></a>Konfigurieren von Azure AD B2C als Identitätsanbieter

Um Benutzern die Anmeldung mit einem Konto von einem anderen Azure AD B2C-Mandanten (Fabrikam) zu ermöglichen, müssen Sie den anderen Azure AD B2C-Mandanten als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde.

Sie können Azure AD B2C als Anspruchsanbieter definieren, indem Sie in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProvider** den Namen „Azure AD B2C“ hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
1. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
1. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:
    ```xml
    <ClaimsProvider>
      <Domain>fabrikam.com</Domain>
      <DisplayName>Federation with Fabrikam tenant</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AzureADB2CFabrikam-OpenIdConnect">
        <DisplayName>Fabrikam</DisplayName>
        <Protocol Name="OpenIdConnect"/>
        <Metadata>
          <!-- Update the Client ID below to the Application ID -->
          <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
          <!-- Update the metadata URL with the other Azure AD B2C tenant name and policy name -->
          <Item Key="METADATA">https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration</Item>
          <Item Key="UsePolicyInRedirectUri">false</Item>
          <Item Key="response_types">code</Item>
          <Item Key="scope">openid</Item>
          <Item Key="response_mode">form_post</Item>
          <Item Key="HttpBinding">POST</Item>
        </Metadata>
        <CryptographicKeys>
          <Key Id="client_secret" StorageReferenceId="B2C_1A_FabrikamAppSecret"/>
        </CryptographicKeys>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
          <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
          <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
          <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
          <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
          <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss"  />
          <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          <OutputClaim ClaimTypeReferenceId="otherMails" PartnerClaimType="emails"/>    
        </OutputClaims>
        <OutputClaimsTransformations>
          <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
          <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
          <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
      </TechnicalProfile>
     </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Aktualisieren Sie die folgenden XML-Elemente mit dem entsprechenden Wert:

    |XML-Element  |Wert  |
    |---------|---------|
    |ClaimsProvider\Domain  | Der Name der Domäne, die für die [direkte Anmeldung](direct-signin.md?pivots=b2c-custom-policy#redirect-sign-in-to-a-social-provider) verwendet wird. Geben Sie den Namen der Domäne ein, die Sie für die direkte Anmeldung verwenden möchten. Beispielsweise *fabrikam.com*. |
    |TechnicalProfile\DisplayName|Dieser Wert wird auf dem Anmeldebildschirm auf der Anmeldeschaltfläche angezeigt. Beispielsweise *Fabrikam*. |
    |Metadata\client_id|Die Anwendungs-ID des Identitätsanbieters. Aktualisieren Sie die Client-ID mit der Anwendungs-ID, die Sie zuvor im anderen Azure AD B2C-Mandanten erstellt haben.|
    |Metadata\METADATA|Eine URL, die auf ein Konfigurationsdokument für den OpenID Connect-Identitätsanbieter verweist, das auch als bekannter OpenID-Konfigurationsendpunkt bezeichnet wird. Geben Sie die folgende URL ein, und ersetzen Sie dabei `{tenant}` durch den Domänennamen des anderen Azure AD B2C-Mandanten (Fabrikam). Ersetzen Sie `{tenant}` durch den Namen der Richtlinie, die Sie im anderen Mandanten konfiguriert haben, und ersetzen Sie `{policy]` durch den Richtliniennamen: `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/v2.0/.well-known/openid-configuration`. Beispiel: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/B2C_1_susi/v2.0/.well-known/openid-configuration`.|
    |CryptographicKeys| Ändern Sie den Wert von **StorageReferenceId** in den Namen des zuvor erstellten Richtlinienschlüssels. Beispiel: `B2C_1A_FabrikamAppSecret`.| 
    

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADB2CFabrikamExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADB2CFabrikamExchange" TechnicalProfileReferenceId="AzureADB2CFabrikam-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

[!INCLUDE [active-directory-b2c-test-relying-party-policy](../../includes/active-directory-b2c-test-relying-party-policy-user-journey.md)]

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [das andere Azure AD B2C-Token an Ihre Anwendung](idp-pass-through-user-flow.md) übergeben.
