---
title: Konfigurieren der Kennwortänderung mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzern das Ändern ihres Kennworts mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C ermöglichen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a49f62b6fc1ea00084266d4c5405f8bf96d034cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509254"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurieren der Kennwortänderung mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory (Azure AD) B2C können Sie es mit einem lokalen Konto angemeldeten Benutzern ermöglichen, ihr Kennwort zu ändern, ohne die Echtheit durch E-Mail-Überprüfung nachweisen zu müssen. Wenn die Sitzung abläuft, bevor der Benutzer den Kennwortänderungsflow erreicht, wird er aufgefordert, sich erneut anzumelden. In diesem Artikel wird beschrieben, wie Sie die Kennwortänderung in [benutzerdefinierten Richtlinien](active-directory-b2c-overview-custom.md) konfigurieren. Es ist auch möglich, die [Self-Service-Kennwortzurücksetzung](active-directory-b2c-reference-sspr.md) für Benutzerflows zu konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) beschriebenen Schritte aus.

## <a name="add-the-elements"></a>Hinzufügen der Elemente 

1. Öffnen Sie Ihre Datei *TrustframeworkExtensions.xml*, und fügen Sie das folgende **ClaimType**-Element mit dem Bezeichner `oldPassword` zum [ClaimsSchema](claimsschema.md)-Element hinzu: 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Ein [ClaimsProvider](claimsproviders.md)-Element enthält das technische Profil, das den Benutzer authentifiziert. Fügen Sie dem **ClaimsProviders**-Element die folgenden Anspruchsanbieter hinzu:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <Protocol Name="OpenIdConnect" />
            <Metadata>
              <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
              <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
              <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
              <Item Key="ProviderName">https://sts.windows.net/</Item>
              <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
              <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
              <Item Key="response_types">id_token</Item>
              <Item Key="response_mode">query</Item>
              <Item Key="scope">email openid</Item>
              <Item Key="UsePolicyInRedirectUri">false</Item>
              <Item Key="HttpBinding">POST</Item>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
              <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
              <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
              <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
              <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
              <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
              <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
              <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
              <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
            </OutputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
      <ClaimsProvider>
        <DisplayName>Local Account Password Change</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
            <DisplayName>Change password (username)</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
              <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
            </Metadata>
            <CryptographicKeys>
              <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
            </CryptographicKeys>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="objectId" />
            </InputClaims>
            <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
              <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
            </OutputClaims>
            <ValidationTechnicalProfiles>
              <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
              <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
            </ValidationTechnicalProfiles>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

    Ersetzen Sie `IdentityExperienceFrameworkAppId` durch die Anwendungs-ID der IdentityExperienceFramework-Anwendung, die Sie im entsprechenden Tutorial erstellt haben. Ersetzen Sie `ProxyIdentityExperienceFrameworkAppId` durch die Anwendungs-ID der ProxyIdentityExperienceFramework-Anwendung, die Sie ebenfalls zuvor erstellt haben.

3. Das [UserJourney](userjourneys.md)-Element definiert den Pfad, den der Benutzer beim Interagieren mit Ihrer Anwendung durchläuft. Fügen Sie das **UserJourneys**-Element hinzu (sofern es noch nicht vorhanden ist), und identifizieren Sie dabei die **UserJourney** als `PasswordChange`:

    ```XML
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Speichern Sie die Richtliniendatei *TrustFrameworkExtensions.xml*.
5. Kopieren Sie die Datei *ProfileEdit.xml*, die Sie mit dem Starter Pack heruntergeladen haben, und weisen Sie ihr den Namen *ProfileEditPasswordChange.xml* zu.
6. Öffnen Sie die neue Datei, und aktualisieren Sie das Attribut **PolicyId** mit einem eindeutigen Wert. Dieser Wert ist der Name Ihrer Richtlinie. Beispiel: *B2C_1A_profile_edit_password_change*.
7. Ändern Sie das Attribut **ReferenceId** in `<DefaultUserJourney>`, sodass es der ID der neuen von Ihnen erstellten User Journey entspricht. Beispiel: *PasswordChange*.
8. Speichern Sie die Änderungen.

Die Beispielrichtlinie finden Sie [hier](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 

## <a name="test-your-policy"></a>Testen Ihrer Richtlinie

Wenn Sie Ihre Anwendungen in Azure AD B2C testen, kann es nützlich sein, das Azure AD B2C-Token an `https://jwt.ms` zurückzugeben, um die darin enthaltenen Ansprüche zu überprüfen.

### <a name="upload-the-files"></a>Hochladen der Dateien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das entsprechende Verzeichnis auswählen.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Framework für die Identitätsfunktion** aus.
5. Klicken Sie auf der Seite „Benutzerdefinierte Richtlinien“ auf **Richtlinie hochladen**.
6. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, suchen Sie nach der Datei *TrustframeworkExtensions.xml*, und wählen Sie die Datei aus.
7. Klicken Sie auf **Hochladen**.
8. Wiederholen Sie die Schritte 5 bis 7 für die Datei der vertrauenden Seite, z. B. *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Ausführen der Richtlinie

1. Öffnen Sie die Richtlinie, die Sie geändert haben. Beispiel: *B2C_1A_profile_edit_password_change*.
2. Wählen Sie für **Anwendung** Ihre Anwendung aus, die Sie zuvor registriert haben. Um das Token anzuzeigen, muss als **Antwort-URL** der Wert `https://jwt.ms` angegeben werden.
3. Klicken Sie auf **Jetzt ausführen**. Melden Sie sich mit dem Konto an, das Sie zuvor erstellt haben. Jetzt sollten Sie die Möglichkeit haben, das Kennwort zu ändern. 

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über das [Konfigurieren der Kennwortkomplexität mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-reference-password-complexity-custom.md). 
