---
title: Technische Profile für den bedingten Zugriff in benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Referenz zu benutzerdefinierten Richtlinien für technische Profile für den bedingten Zugriff in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc811ab3cab4b79b81b16dd94a2c72225046e35a
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488277"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Profils für den bedingten Zugriff in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Über den bedingten Zugriff in Azure Active Directory (Azure AD) werden in Azure AD B2C Signale zusammengeführt, Entscheidungen getroffen und Organisationsrichtlinien erzwungen. Die Automatisierung der Risikobewertung mit Richtlinienbedingungen bedeutet, dass riskante Anmeldungen sofort erkannt und behoben oder blockiert werden.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>Protocol

Das **Name** -Attribut des **Protocol** -Elements muss auf `Proprietary` festgelegt werden. Das **handler** -Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly, die von Azure AD B2C verwendet wird, enthalten:

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

Das folgende Beispiel zeigt ein technisches Profil für den bedingten Zugriff:

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>Auswertung des bedingten Zugriffs

Bei jeder Anmeldung werden in Azure AD B2C alle Richtlinien ausgewertet, und es wird sichergestellt, dass alle Anforderungen erfüllt sind, bevor dem Benutzer der Zugriff gewährt wird. „Zugriff blockieren“ setzt alle übrigen Konfigurationseinstellungen außer Kraft. Im Modus **Evaluation** des technischen Profils für den bedingten Zugriff werden die bei der Anmeldung mit einem lokalen Konto in Azure AD B2C erfassten Signale ausgewertet. Das Ergebnis des technischen Profils für den bedingten Zugriff ist eine Reihe von Ansprüchen, die sich aus der Auswertung des bedingten Zugriffs ergeben. In der Azure AD B2C-Richtlinie werden diese Ansprüche in einem nächsten Orchestrierungsschritt verwendet, um eine Aktion auszuführen, z. B. den Benutzer zu blockieren oder den Benutzer zur mehrstufigen Authentifizierung aufzufordern. Für diesen Modus können folgende Optionen konfiguriert werden:

### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| OperationType | Ja | Muss **Evaluation** sein.  |

### <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste von Ansprüchen, die an den bedingten Zugriff gesendet werden. Sie können auch den Namen des Anspruchs dem im technischen Profil für den bedingten Zugriff definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Datentyp | BESCHREIBUNG |
| --------- | -------- | ----------- |----------- |
| UserId | Ja | Zeichenfolge | Der Bezeichner des Benutzers, der sich anmeldet. |
| AuthenticationMethodsUsed | Ja |stringCollection | Die Liste der Methoden, die der Benutzer für die Anmeldung verwendet hat. Mögliche Werte: `Password` und `OneTimePasscode`. |
| IsFederated | Ja |boolean | Gibt an, ob ein Benutzer mit einem Verbundkonto angemeldet ist oder nicht. Der Wert muss `false` sein. |
| IsMfaRegistered | Ja |boolean | Gibt an, ob der Benutzer bereits eine Telefonnummer für die mehrstufige Authentifizierung registriert hat. |


Das Element **InputClaimsTransformations** enthält ggf. eine Sammlung von Elementen vom Typ **InputClaimsTransformation** , die vor dem Senden an den Dienst für den bedingten Zugriff zum Ändern der Eingabeansprüche oder zum Generieren neuer Eingabeansprüche verwendet werden.

### <a name="output-claims"></a>Ausgabeansprüche

Das Element **OutputClaims** enthält eine Liste der Ansprüche, die von ConditionalAccessProtocolProvider generiert werden. Sie können auch den Namen Ihres Anspruchs dem unten definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Datentyp | BESCHREIBUNG |
| --------- | -------- | ----------- |----------- |
| Herausforderungen | Ja |stringCollection | Liste der Aktionen zum Korrigieren der ermittelten Bedrohung. Möglicher Wert: `block` |
| MultiConditionalAccessStatus | Ja | stringCollection |  |

Das **OutputClaimsTransformations** -Element darf eine Sammlung von **OutputClaimsTransformation** -Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

### <a name="example-evaluation"></a>Beispiel: Auswertung

Das folgende Beispiel zeigt ein technisches Profil für den bedingten Zugriff, das zur Auswertung der Anmeldebedrohung verwendet wird.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegisteredCT" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>Wartung

Im Modus **Remediation** des technischen Profils für den bedingten Zugriff wird in Azure AD B2C angegeben, dass die bei der Anmeldung identifizierte Bedrohung korrigiert wurde. Für den Modus „Remediation“ können folgende Optionen konfiguriert werden.

### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| OperationType | Ja | Muss **Remediation** sein.  |

### <a name="input-claims"></a>Eingabeansprüche

Das Element **InputClaims** enthält eine Liste von Ansprüchen, die an den bedingten Zugriff gesendet werden. Sie können auch den Namen des Anspruchs dem im technischen Profil für den bedingten Zugriff definierten Namen zuordnen.

| ClaimReferenceId | Erforderlich | Datentyp | BESCHREIBUNG |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | Ja | stringCollection| Die im Modus „Evaluation“ im Rahmen des Anforderungsanspruchs zurückgegebene Liste der erfüllten Anforderungen zum Korrigieren der identifizierten Bedrohung.|


Das Element **InputClaimsTransformations** enthält ggf. eine Sammlung von Elementen vom Typ **InputClaimsTransformation** , die vor dem Aufrufen des Diensts für den bedingten Zugriff zum Ändern der Eingabeansprüche oder zum Generieren neuer Eingabeansprüche verwendet werden.

### <a name="output-claims"></a>Ausgabeansprüche

Da vom Protokollanbieter für den bedingten Zugriff keine Elemente vom Typ **OutputClaims** zurückgegeben werden, müssen auch keine Ausgabeansprüche angegeben werden. Sie können jedoch Ansprüche einfügen, die nicht vom Protokollanbieter für den bedingten Zugriff zurückgegeben werden (vorausgesetzt, Sie legen das Attribut `DefaultValue` fest).

Das **OutputClaimsTransformations** -Element darf eine Sammlung von **OutputClaimsTransformation** -Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.


### <a name="example-remediation"></a>Beispiel: Wiederherstellung

Das folgende Beispiel zeigt ein technisches Profil für den bedingten Zugriff, das zum Korrigieren der identifizierten Bedrohung verwendet wird:

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

Damit das Beispiel funktioniert, fügen Sie die im folgenden Beispiel gezeigten Ansprüche im Abschnitt „ClaimsSchema“ in der Erweiterungsdatei hinzu:

```XML
<!-- Conditional Access claims  -->
   <ClaimType Id="conditionalAccessClaimCollection">
      <DisplayName>Conditional Access claims</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of claims which are the result of CA check</AdminHelpText>
   </ClaimType>
   <ClaimType Id="ConditionalAccessStatus">
      <DisplayName>The status of CA evaluation</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The status of CA evaluation</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodsUsed">
      <DisplayName>Authentication methods used</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of authentication methods used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodUsed">
      <DisplayName>Authentication method used</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>The authentication method used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsFederated">
      <DisplayName>IsFederated</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user authenticated via an external identity provider</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsMfaRegistered">
      <DisplayName>IsMfaRegistered</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user registered for MFA</AdminHelpText>
    </ClaimType> 
    <ClaimType Id="CAChallengeIsMfa">
      <DisplayName>CAChallengeIsMfa</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsChgPwd">
      <DisplayName>CAChallengeIsChgPwd</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsBlock">
      <DisplayName>CAChallengeIsBlock</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="responseMsg">
      <DisplayName>responseMsg</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
      <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
      <UserInputType>Paragraph</UserInputType>
    </ClaimType>
<!-- End of Conditional Access claims -->
```

Fügen Sie die folgenden Anspruchstransformationen hinzu:

```xml
<!--Conditional Access Transformations-->
  <ClaimsTransformation Id="AddToAuthenticationMethodsUsed" TransformationMethod="AddItemToStringCollection">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="item" />
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreatePasswordAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="Password" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreateOneTimePasscodeAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="OneTimePasscode" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsMfaRegisteredCT" TransformationMethod="DoesClaimExist">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="IsMfaRegistered" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="SetCAChallengeIsMfa" TransformationMethod="StringCollectionContains">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="item" DataType="string" Value="mfa" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" TransformationClaimType="outputClaim" />
    </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsChgPwd" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="chg_pwd" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsBlock" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="block" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
```

Fügen Sie das folgende technische Profil hinzu:

```xml
<TechnicalProfile Id="GenerateCAClaimFlags">
  <DisplayName>GenerateCAClaimFlags</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsMfa" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsChgPwd" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsBlock" />
  </OutputClaimsTransformations>
</TechnicalProfile>

<!--This is a self-asserted technical profile that we will use to show a block screen-->
<TechnicalProfile Id="ShowBlockPage">
  <DisplayName>Show Block message</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
    <Item Key="TokenLifeTimeInSeconds">3600</Item>
    <Item Key="AllowGenerationOfClaimsWithNullValues">true</Item>
    <Item Key="setting.showContinueButton">false</Item>
    <Item Key="setting.showCancelButton">false</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseMsg" DefaultValue="The user is blocked due to conditional access check." />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
  <EnabledForUserJourneys>Always</EnabledForUserJourneys>
</TechnicalProfile>

```

Fügen Sie im Element „TrustFrameworkPolicy“ wie im folgenden Beispiel gezeigt diese SubJourneys-Elemente hinzu:

```xml
  <SubJourneys>
    <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>

    <SubJourney Id="ConditionalAccess_Remediation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessRemediation" TechnicalProfileReferenceId="ConditionalAccessRemediation" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>
  </SubJourneys>

```

Fügen Sie wie im folgenden Beispiel gezeigt eine User Journey hinzu, die die neuen Ansprüche verwendet:

```xml
  <UserJourneys>
    <UserJourney Id="SignUpOrSignInWithCA">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />

          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Check if the user has selected to sign in using one of the social providers -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!-- For social IDP authentication, attempt to find the user account in the directory. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>socialIdpAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="4" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
          </JourneyList>
        </OrchestrationStep>

        <!--MFA based on Conditional Access-->
        <OrchestrationStep Order="5" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsMfa</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>CAChallengeIsMfa</Value>
              <Value>False</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--Save MFA phone number: The precondition verifies whether the user provided a new number in the previous step. If so, the phone number is stored in the directory for future authentication requests.-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newPhoneNumberEntered</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserWriteWithObjectId" TechnicalProfileReferenceId="AAD-UserWritePhoneNumberUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="7" Type="ClaimsExchange" >
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Value>True</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ShowBlockPage" TechnicalProfileReferenceId="ShowBlockPage" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--If a user has reached this point, this means a remediation was applied-->
        <!--  You can add a precondition here to call remediation only if a Conditional Access challenge was issued-->
        <OrchestrationStep Order="8" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Remediation" />
          </JourneyList>
        </OrchestrationStep>
        <OrchestrationStep Order="9" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>

```

Im folgenden Beispiel wird eine Datei der vertrauenden Seite gezeigt, die auf dieses UserJourney-Element verweist:

```xml
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
                      xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
                      xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
                      PolicySchemaVersion="0.3.0.0" TenantId="contoso.onmicrosoft.com" 
                      PolicyId="ha-sam-signup_signin-CA" 
                      PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_CA"
                      DeploymentMode="Development"
                      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignInWithCA" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>604800</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="<add your app insights instrumentation key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="signInName" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
        <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="next-steps"></a>Nächste Schritte

- Ein Beispiel für eine Richtlinie für bedingten Zugriff finden Sie auf [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/conditional-access).
