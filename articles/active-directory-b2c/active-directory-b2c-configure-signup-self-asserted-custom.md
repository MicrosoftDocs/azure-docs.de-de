---
title: Hinzufügen von Ansprüchen und Anpassen von Benutzereingaben mit benutzerdefinierten Richtlinien – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benutzereingaben anpassen und Ansprüche zur User Journey bei Registrierung bzw. Anmeldung in Azure Active Directory B2C hinzufügen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c15d315d186af5622d9fa988945750d93e8eb11f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507556"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Hinzufügen von Ansprüchen und Anpassen von Benutzereingaben mit benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel fügen Sie der User Journey für die Registrierung in Azure Active Directory (Azure AD) B2C eine neue Benutzereingabe (einen Anspruch) hinzu.  Sie konfigurieren die Eingabe als Dropdownliste und legen fest, ob sie erforderlich ist.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte im Artikel [Erste Schritte mit benutzerdefinierten Richtlinien](active-directory-b2c-get-started-custom.md) aus. Testen Sie die User Journey für die Registrierung bzw. Anmeldung, um ein neues lokales Konto zu registrieren, bevor Sie fortfahren.

## <a name="add-claims"></a>Hinzufügen von Ansprüchen

Das Sammeln der anfänglichen Daten von Ihren Benutzern erfolgt mithilfe der User Journey für die Registrierung bzw. Anmeldung. Weitere Ansprüche können später mit einer User Journey für die Profilbearbeitung gesammelt werden. Jedes Mal, wenn Azure AD B2C auf interaktive Weise Informationen direkt vom Benutzer erfasst, wird vom Identity Experience Framework der selbstbestätigte Anbieter verwendet.


### <a name="define-the-claim"></a>Definieren des Anspruchs

Sie fragen zum Beispiel den Benutzer nach seinem Wohnort. Fügen Sie dem Element **ClaimsSchema** in der Richtliniendatei „TrustFrameworkBase“ das folgende Element hinzu:

```xml
<ClaimType Id="city">
  <DisplayName>city</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

Die folgenden Elemente werden zum Definieren des Anspruchs verwendet:

- **DisplayName**: Zeichenfolge, mit der die Bezeichnung für den Benutzer definiert wird.
- **UserHelpText**: Verdeutlicht dem Benutzer, was erforderlich ist.
- **UserInputType**: Kann ein Textfeld, eine Optionsfeldauswahl, eine Dropdownliste oder eine Mehrfachauswahl sein.

#### <a name="textbox"></a>TextBox

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Your city</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

#### <a name="radiosingleselect"></a>RadioSingleSelect

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>RadioSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="dropdownsingleselect"></a>DropdownSingleSelect

![Screenshot der Dropdownoption](./media/active-directory-b2c-configure-signup-self-asserted-custom/dropdown-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>city where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

#### <a name="checkboxmultiselect"></a>CheckboxMultiSelect

![Screenshot der Option für die Mehrfachauswahl](./media/active-directory-b2c-configure-signup-self-asserted-custom/multiselect-menu-example.png)

```xml
<ClaimType Id="city">
  <DisplayName>Receive updates from which cities?</DisplayName>
  <DataType>string</DataType>
  <UserInputType>CheckboxMultiSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

### <a name="add-the-claim-to-the-user-journey"></a>Hinzufügen des Anspruchs zur User Journey

1. Fügen Sie dem technischen Profil `LocalAccountSignUpWithLogonEmail` in der Richtliniendatei „TrustFrameworkBase“ den Anspruch als `<OutputClaim ClaimTypeReferenceId="city"/>` hinzu. Dieses technische Profil verwendet den SelfAssertedAttributeProvider.

    ```xml
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
      </CryptographicKeys>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
        <!-- Optional claims, to be collected from the user -->
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surName" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

2. Fügen Sie den Anspruch zum technischen Profil „AAD-UserWriteUsingLogonEmail“ als `<PersistedClaim ClaimTypeReferenceId="city" />` hinzu, um den Anspruch nach der Erfassung vom Benutzer in das AAD-Verzeichnis zu schreiben. Sie können diesen Schritt überspringen, wenn Sie es vorziehen, den Anspruch im Verzeichnis nicht zur späteren Verwendung beizubehalten.

    ```xml
    <!-- Technical profiles for local accounts -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <!-- Required claims -->
        <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
        <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password" />
        <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />
        <!-- Optional claims. -->
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />
        <PersistedClaim ClaimTypeReferenceId="city" />
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
    ```

3. Fügen Sie den Anspruch `<OutputClaim ClaimTypeReferenceId="city" />` den technischen Profilen hinzu, die im Verzeichnis lesen, wenn sich ein Benutzer anmeldet.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">An account could not be found for the provided user ID.</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Required claims -->
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Optional claims -->
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```
   
4. Fügen Sie den Anspruch `<OutputClaim ClaimTypeReferenceId="city" />` der Datei „SignUporSignIn.xml“ hinzu, sodass dieser Anspruch nach einer erfolgreichen User Journey an die Anwendung im Token gesendet wird.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="identityProvider" />
          <OutputClaim ClaimTypeReferenceId="city" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD-Mandanten enthält, indem Sie im oberen Menü auf den **Verzeichnis- und Abonnementfilter** klicken und das Verzeichnis auswählen, das Ihren Azure AD-Mandanten enthält.
3. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**, suchen Sie nach **App-Registrierungen**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Identity Experience Framework (Preview)** aus.
5. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus, und laden Sie dann die beiden geänderten Richtliniendateien hoch.
2. Wählen Sie die hochgeladene Registrierungs- oder Anmelderichtlinie aus, und klicken Sie auf die Schaltfläche **Jetzt ausführen**.
3. Sie sollten sich mit einer E-Mail-Adresse registrieren können.

Der Registrierungsbildschirm sollte etwa wie folgt aussehen:

![Screenshot der geänderten Registrierungsoption](./media/active-directory-b2c-configure-signup-self-asserted-custom/signup-with-city-claim-dropdown-example.png)

Das Token, das an die Anwendung zurückgesendet wird, enthält den Anspruch `city`.

```json
{
  "exp": 1493596822,
  "nbf": 1493593222,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "9c2a3a9e-ac65-4e46-a12d-9557b63033a9",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustf_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1493593222,
  "auth_time": 1493593222,
  "email": "joe@outlook.com",
  "given_name": "Joe",
  "family_name": "Ras",
  "city": "Bellevue",
  "name": "unknown"
}
```

## <a name="optional-remove-email-verification"></a>Optional: Entfernen der E-Mail-Überprüfung

Um die E-Mail-Überprüfung zu überspringen, können Sie sich für das Entfernen von `PartnerClaimType="Verified.Email"` entscheiden. In diesem Fall ist die E-Mail-Adresse zwar erforderlich, wird aber nicht überprüft, sofern „Required“ = „true“ entfernt wird.  Überlegen Sie sorgfältig, ob diese Option für Ihre Anwendungsfälle geeignet ist.

Im `<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">`-Element in der Richtliniendatei „TrustFrameworkBase“ ist die E-Mail-Überprüfung standardmäßig aktiviert:

```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie [Verwenden von benutzerdefinierten Attributen in einer benutzerdefinierten Richtlinie für die Profilbearbeitung](active-directory-b2c-create-custom-attributes-profile-edit-custom.md).
