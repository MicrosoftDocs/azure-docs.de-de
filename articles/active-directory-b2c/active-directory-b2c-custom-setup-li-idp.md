---
title: Einrichten der Anmeldung mit einem LinkedIn-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C | Microsoft-Dokumentation
description: Richten Sie die Anmeldung mit einem LinkedIn-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C ein.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b336428592a4897319725782c994c3fae26bfae0
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510418"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Einrichten der Anmeldung mit einem LinkedIn-Konto mithilfe benutzerdefinierter Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel wird erläutert, wie Sie die Anmeldung für Benutzer über ein LinkedIn-Konto mithilfe [benutzerdefinierter Richtlinien](active-directory-b2c-overview-custom.md) in Azure Active Directory (Azure AD) B2C aktivieren.

## <a name="prerequisites"></a>Voraussetzungen

- Führen Sie die unter [Erste Schritte mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-get-started-custom.md) beschriebenen Schritte aus.
- Falls Sie noch kein LinkedIn-Konto besitzen, erstellen Sie eins auf der [LinkedIn-Anmeldeseite](https://www.linkedin.com/start/join).
- Für eine LinkedIn-Anwendung muss ein Logobild mit 80 x 80 Pixeln bereitgestellt werden, das Ihre Anwendung darstellt.

## <a name="create-an-application"></a>Erstellen einer Anwendung

Um LinkedIn als Identitätsanbieter in Azure AD B2C zu nutzen, müssen Sie eine LinkedIn-Anwendung erstellen.

1. Melden Sie sich auf der Website für die [LinkedIn-Anwendungsverwaltung](https://www.linkedin.com/secure/developer?newapp=) mit den Anmeldeinformationen für Ihr LinkedIn-Konto an.
2. Wählen Sie **Create Application** (Anwendung erstellen) aus.
3. Geben Sie Ihren **Unternehmensnamen**, einen **Anwendungsnamen** und eine **Anwendungsbeschreibung** ein.
4. Laden Sie das von Ihnen erstellte **Anwendungslogo** hoch.
5. Wählen Sie in der Liste unter **Anwendungsverwendung** einen Eintrag aus.
6. Geben Sie unter **Website URL** (Website-URL) die URL `https://your-tenant.b2clogin.com` ein.  Ersetzen Sie `your-tenant` durch den Namen des Azure AD B2C-Mandanten. Beispiel: contoso.b2clogin.com
7. Machen Sie Angaben für **Business Email** (Geschäftliche E-Mail-Adresse) und **Business Phone** (Geschäftliche Telefonnummer).
8. Lesen und akzeptieren Sie die Nutzungsbedingungen am Ende der Seite, und wählen Sie dann **Submit** (Übermitteln) aus.
9. Wählen Sie **Authentication** (Authentifizierung) aus, und notieren Sie die Werte für **Client ID** (Client-ID) und **Client Secret** (Clientgeheimnis).
10. Geben Sie **unter**Authorized Redirect URLs`https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp` (Autorisierte Umleitungs-URLs) ein. Ersetzen Sie `your-tenant` durch den Namen Ihres Mandanten. Bei der Eingabe Ihres Mandantennamens dürfen Sie nur Kleinbuchstaben verwenden, auch wenn der Mandant in Azure AD B2C Großbuchstaben enthält. 
11. Wählen Sie **Update** aus.
12. Wählen Sie **Settings** (Einstellungen) aus, ändern Sie **Application status** (Anwendungsstatus) in **Live**, und wählen Sie dann **Update** aus.

## <a name="create-a-policy-key"></a>Erstellen eines Richtlinienschlüssels

Sie müssen den geheimen Clientschlüssel speichern, den Sie zuvor in Ihrem Azure AD B2C-Mandanten notiert haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Klicken Sie im oberen Menü auf **Verzeichnis- und Abonnementfilter**, und wählen Sie das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
5. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
6. Klicken Sie unter **Optionen** auf `Manual`.
7. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `LinkedInSecret`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
8. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
9. Wählen Sie für **Schlüsselverwendung** die Option `Signature` aus.
10. Klicken Sie auf **Create**.

## <a name="add-a-claims-provider"></a>Hinzufügen eines Anspruchsanbieters

Wenn Sie möchten, dass sich Benutzer mit einem LinkedIn-Konto anmelden, müssen Sie das Konto als Anspruchsanbieter definieren, mit dem Azure AD B2C über einen Endpunkt kommunizieren kann. Der Endpunkt bietet eine Reihe von Ansprüchen, mit denen Azure AD B2C überprüft, ob ein bestimmter Benutzer authentifiziert wurde. 

Sie können ein LinkedIn-Konto als Anspruchsanbieter definieren, indem Sie es in der Erweiterungsdatei Ihrer Richtlinie dem Element **ClaimsProvider** hinzufügen.

1. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*.
2. Suchen Sie nach dem Element **ClaimsProviders**. Falls das Element nicht vorhanden sein sollte, fügen Sie es unter dem Stammelement hinzu.
3. Fügen Sie ein neues **ClaimsProvider**-Element wie folgt hinzu:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
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

4. Ersetzen Sie den Wert von **client_id** durch die Client-ID, die Sie zuvor notiert haben.
5. Speichern Sie die Datei .

### <a name="add-the-claims-transformations"></a>Hinzufügen von Ansprüchen von Transformationen

Das technische Profil für LinkedIn erfordert das Hinzufügen von Transformationen der Ansprüche **ExtractGivenNameFromLinkedInResponse** und **ExtractSurNameFromLinkedInResponse** zur Liste „ClaimsTransformations“. Wenn in Ihrer Datei kein **ClaimsTransformations**-Element definiert ist, fügen Sie die übergeordneten XML-Elemente wie unten gezeigt hinzu. Für Transformationen von Ansprüchen muss auch ein neuer Anspruchstyp namens **nullStringClaim** definiert werden. 

Das **BuildingBlocks**-Element muss am Anfang der Datei hinzugefügt werden. Siehe *TrustframeworkBase.xml* als Beispiel.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```
### <a name="upload-the-extension-file-for-verification"></a>Hochladen der Erweiterungsdatei zur Überprüfung

Nun haben Sie Ihre Richtlinie so konfiguriert, dass Azure AD B2C mit Ihrem Twitter-Konto kommunizieren kann. Versuchen Sie, die Erweiterungsdatei Ihrer Richtlinie hochzuladen, um sich zu vergewissern, dass soweit keine Probleme vorliegen.

1. Wählen Sie in Ihrem Azure AD B2C-Mandanten auf der Seite **Benutzerdefinierte Richtlinien** die Option **Richtlinie hochladen** aus.
2. Aktivieren Sie **Richtlinie überschreiben, sofern vorhanden**, navigieren Sie dann zur Datei *TrustFrameworkExtensions.xml*, und wählen Sie die Datei aus.
3. Klicken Sie auf **Hochladen**.

## <a name="register-the-claims-provider"></a>Registrieren des Anspruchsanbieters

Der Identitätsanbieter wurde nun eingerichtet, ist aber auf keinem der Registrierungs- oder Anmeldebildschirme vorhanden. Um ihn verfügbar zu machen, erstellen Sie ein Duplikat einer vorhandenen User Journey-Vorlage und ändern diese dann so, dass sie ebenfalls das LinkedIn-Konto als Identitätsanbieter aufweist.

1. Öffnen Sie die Datei *TrustFrameworkBase.xml* aus dem Startpaket.
2. Suchen und kopieren Sie den gesamten Inhalt des **UserJourney**-Elements, das `Id="SignUpOrSignIn"` enthält.
3. Öffnen Sie die Datei *TrustFrameworkExtensions.xml*, und suchen Sie nach dem **UserJourneys**-Element. Wenn das Element nicht vorhanden ist, fügen Sie ein solches hinzu.
4. Fügen Sie den gesamten Inhalt des kopierten **UserJourney**-Element als untergeordnetes Element des **UserJourneys**-Elements ein.
5. Benennen Sie die ID der User Journey um. Beispiel: `SignUpSignInLinkedIn`.

### <a name="display-the-button"></a>Anzeigen der Schaltfläche

Das **ClaimsProviderSelection**-Element entspricht einer Schaltfläche für einen Identitätsanbieter auf einem Registrierungs- oder Anmeldebildschirm. Wenn Sie ein **ClaimsProviderSelection**-Element für ein LinkedIn-Konto hinzufügen, wird eine neue Schaltfläche angezeigt, wenn ein Benutzer zu der Seite gelangt.

1. Suchen Sie nach dem Element **OrchestrationStep**, das `Order="1"` in der User Journey enthält, die Sie erstellt haben.
2. Fügen Sie unter **ClaimsProviderSelects** das folgende Element hinzu. Legen Sie den Wert von **TargetClaimsExchangeId** auf einen geeigneten Wert (z.B. auf `LinkedInExchange`) fest:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Verknüpfen der Schaltfläche mit einer Aktion

Nachdem Sie eine Schaltfläche implementiert haben, müssen Sie sie mit einer Aktion verknüpfen. In diesem Fall ist die Aktion die Kommunikation von Azure AD B2C mit einem LinkedIn-Konto, um ein Token zu empfangen.

1. Suchen Sie nach dem **OrchestrationStep**-Element, das `Order="2"` in der User Journey enthält.
2. Fügen Sie das folgende **ClaimsExchange**-Element hinzu, und stellen Sie dabei sicher, dass Sie denselben Wert für die ID verwenden, den Sie für **TargetClaimsExchangeId** verwendet haben:

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```
    
    Ändern Sie den Wert von **TechnicalProfileReferenceId** in die ID des technischen Profils, das Sie zuvor erstellt haben. Beispiel: `LinkedIn-OAUTH`.

3. Speichern Sie die Datei *TrustFrameworkExtensions.xml*, und laden Sie die Datei zur Überprüfung erneut hoch.

## <a name="create-an-azure-ad-b2c-application"></a>Erstellen einer Azure AD B2C-Anwendung

Die Kommunikation mit Azure AD B2C erfolgt über eine Anwendung, die Sie in Ihrem Mandanten erstellen. In diesem Abschnitt werden optionale Schritte aufgeführt, die Sie ausführen können, um eine Testanwendung zu erstellen, falls Sie dies noch nicht getan haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Klicken Sie im oberen Menü auf **Verzeichnis- und Abonnementfilter**, und wählen Sie das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein, z.B. *testapp1*.
6. Wählen Sie für **Web-App/Web-API** die Option `Yes` aus, und geben Sie dann für die **Antwort-URL** die Zeichenfolge `https://jwt.ms` ein.
7. Klicken Sie auf **Create**.

## <a name="update-and-test-the-relying-party-file"></a>Aktualisieren und Testen der Datei der vertrauenden Seite

Aktualisieren Sie als Nächstes die Datei der vertrauenden Seite, mit der die erstellte User Journey initiiert wird.

1. Erstellen Sie in Ihrem Arbeitsverzeichnis eine Kopie der Datei *SignUpOrSignIn.xml*, und benennen Sie sie um. Benennen Sie die Datei beispielsweise in *SignUpSignInLinkedIn.xml* um.
2. Öffnen Sie die neue Datei, und aktualisieren Sie den Wert des Attributs **PolicyId** für **TrustFrameworkPolicy** mit einem eindeutigen Wert. Beispiel: `SignUpSignInLinkedIn`.
3. Aktualisieren Sie den Wert von **PublicPolicyUri** mit dem URI für die Richtlinie. Beispiel: `http://contoso.com/B2C_1A_signup_signin_linkedin`
4. Ändern Sie den Wert des Attributs **ReferenceId** im Element **DefaultUserJourney** so, dass er der ID der neuen User Journey entspricht, die Sie erstellt haben (SignUpSignLinkedIn).
5. Speichern Sie Ihre Änderungen, laden Sie die Datei hoch, und wählen Sie dann in der Liste die neue Richtlinie aus.
6. Stellen Sie sicher, dass die von Ihnen erstellte Azure AD B2C-Anwendung im Feld **Anwendung auswählen** ausgewählt ist, und klicken Sie dann auf **Jetzt ausführen**, um sie zu testen.

## <a name="migration-from-v10-to-v20"></a>Migration von Version 1.0 zu 2.0

Vor kurzem hat LinkedIn [seine APIs von Version 1.0 auf 2.0 aktualisiert](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Um Ihre bestehende Konfiguration in die neue Konfiguration zu migrieren, verwenden Sie die Informationen in den folgenden Abschnitten, um die Elemente im technischen Profil zu aktualisieren.

### <a name="replace-items-in-the-metadata"></a>Ersetzen von Elementen in den Metadaten

Ändern Sie im vorhandenen **Metadata**-Element von **TechnicalProfile** die folgenden **Item**-Elemente von:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

in:

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Hinzufügen von Elementen zu den Metadaten

Fügen Sie im **Metadata**-Element von **TechnicalProfile** die folgenden **Item**-Elemente hinzu:

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Ändern von OutputClaim-Elementen

Ändern Sie in den vorhandenen **OutputClaim**-Elementen von **TechnicalProfile** die folgenden **OutputClaim**-Elemente von:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

in:

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Hinzufügen neuer OutputClaimsTransformation-Elemente

Ändern Sie in den vorhandenen **OutputClaimsTransformation**-Elementen von **TechnicalProfile** die folgenden **OutputClaimsTransformation**-Elemente:

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definieren der neuen Transformationen von Ansprüchen und des Anspruchstyps

Im letzten Schritt haben Sie neue Transformationen von Ansprüchen hinzugefügt, die definiert werden müssen. Um die Transformationen von Ansprüchen zu definieren, fügen Sie sie der Liste **ClaimsTransformations** hinzu. Wenn in Ihrer Datei kein **ClaimsTransformations**-Element definiert ist, fügen Sie die übergeordneten XML-Elemente wie unten gezeigt hinzu. Für Transformationen von Ansprüchen muss auch ein neuer Anspruchstyp namens **nullStringClaim** definiert werden. 

Das **BuildingBlocks**-Element muss am Anfang der Datei hinzugefügt werden. Siehe *TrustframeworkBase.xml* als Beispiel.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Abrufen einer E-Mail-Adresse

Im Rahmen der LinkedIn-Migration von Version 1.0 zu 2.0 ist ein zusätzlicher Aufruf einer weiteren API erforderlich, um die E-Mail-Adresse abzurufen. Wenn Sie die E-Mail-Adresse während der Anmeldung abrufen müssen, gehen Sie wie folgt vor:

1. Führen Sie die vorangehenden Schritte aus, damit Azure AD B2C zum Zweck der Benutzeranmeldung einen Verbund mit LinkedIn herstellen kann. Im Rahmen des Verbunds empfängt Azure AD B2C das Zugriffstoken für LinkedIn.
2. Speichern Sie das LinkedIn-Zugriffstoken in einem Anspruch. [Anweisungen finden Sie hier](idp-pass-through-custom.md).
3. Fügen Sie den folgenden Anspruchsanbieter, der die Anforderung ausführt, der `/emailAddress`-API von LinkedIn hinzu. Um diese Anforderung zu autorisieren, benötigen Sie das LinkedIn-Zugriffstoken.

    ```XML
    <ClaimsProvider> 
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Fügen Sie Ihrer User Journey den folgenden Orchestrierungsschritt hinzu, damit der API-Anspruchsanbieter ausgelöst wird, wenn sich ein Benutzer über LinkedIn anmeldet. Stellen Sie sicher, dass die Zahl für `Order` entsprechend aktualisiert wird. Fügen Sie diesen Schritt unmittelbar nach dem Orchestrierungsschritt ein, durch den das technische Profil für LinkedIn ausgelöst wird.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

Das Abrufen der E-Mail-Adresse aus LinkedIn während der Anmeldung ist optional. Wenn Sie die E-Mail nicht von LinkedIn abrufen möchten, aber für die Registrierung eine Adresse benötigen, muss der Benutzer die E-Mail-Adresse manuell eingeben und bestätigen.

Ein vollständiges Beispiel für eine Richtlinie, die den LinkedIn-Identitätsanbieter verwendet, finden Sie unter [Starter Pack für benutzerdefinierte Richtlinien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).
