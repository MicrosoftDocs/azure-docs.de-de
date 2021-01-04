---
title: Anspruchskonfliktlöser in benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Anspruchskonfliktlöser in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C verwendet werden.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 682b83d7016a89b27b5c936853abda1438f59c28
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508015"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informationen zu Anspruchskonfliktlösern in benutzerdefinierten Azure Active Directory B2C-Richtlinien

Anspruchskonfliktlöser in [benutzerdefinierten Richtlinien](custom-policy-overview.md) von Azure Active Directory B2C (Azure AD B2C) bieten Kontextinformationen über eine Autorisierungsanforderung, z. B. den Namen der Richtlinie, die Korrelations-ID der Anforderung, die Sprache der Benutzeroberfläche und mehr.

Um einen Anspruchskonfliktlöser in einem Ein- oder Ausgabeanspruch zu verwenden, definieren Sie eine Zeichenfolge **ClaimType** unter dem [ClaimsSchema](claimsschema.md)-Element, und dann Sie legen Sie den **DefaultValue** auf den Anspruchskonfliktlöser in dem Ein- oder Ausgabeanspruchselement fest. Azure AD B2C liest den Wert des Anspruchskonfliktlösers und verwendet den Wert in dem technischen Profil.

Im folgenden Beispiel, wird ein Anspruchstyp namens `correlationId` definiert mit einem **DataType** von `string`.

```xml
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Ordnen Sie im technischen Profil den Anspruchskonfliktlöser dem Anspruchstyp zu. Azure AD B2C füllt den Wert des Anspruchskonfliktlösers `{Context:CorrelationId}` in den Anspruch `correlationId` auf, und sende den Anspruch an den technische Profil.

```xml
<InputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
```

## <a name="claim-resolver-types"></a>Typen von Anspruchskonfliktlösern

In den folgenden Abschnitten werden die verfügbaren Anspruchskonfliktlöser aufgelistet.

### <a name="culture"></a>Kultur

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {Culture:LanguageName} | Der aus zwei Buchstaben bestehende ISO-Code für die Sprache. | en |
| {Culture:LCID}   | Die LCID des Sprachcodes. | 1033 |
| {Culture:RegionName} | Der aus zwei Buchstaben bestehende ISO-Code für die Region. | US |
| {Culture:RFC5646} | Der Sprachcode RFC5646. | de-DE |

### <a name="policy"></a>Richtlinie

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {Policy:PolicyId} | Der Name der Richtlinie für die vertrauende Seite. | B2C_1A_signup_signin |
| {Policy:RelyingPartyTenantId} | Die Mandanten-ID der Richtlinie für die vertrauende Seite. | Ihr-Mandan.onmicrosoft.com |
| {Policy:TenantObjectId} | Die Mandantenobjekt-ID der Richtlinie für die vertrauende Seite. | 00000000-0000-0000-0000-000000000000 |
| {Policy:TrustFrameworkTenantId} | Die Mandanten-ID des Vertrauensframeworks. | Ihr-Mandan.onmicrosoft.com |

### <a name="openid-connect"></a>OpenID Connect

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {OIDC:AuthenticationContextReferences} |Der Abfragezeichenfolgen-Parameter `acr_values`. | – |
| {OIDC:ClientId} |Der Abfragezeichenfolgen-Parameter `client_id`. | 00000000-0000-0000-0000-000000000000 |
| {OIDC:DomainHint} |Der Abfragezeichenfolgen-Parameter `domain_hint`. | facebook.com |
| {OIDC:LoginHint} |  Der Abfragezeichenfolgen-Parameter `login_hint`. | someone@contoso.com |
| {OIDC:MaxAge} | Das `max_age`. | – |
| {OIDC:Nonce} |Der Abfragezeichenfolgen-Parameter `Nonce`. | defaultNonce |
| {OIDC:Password}| Das Benutzerkennwort aus dem [Flow für Kennwortanmeldeinformationen von Ressourcenbesitzern](ropc-custom.md)| password1| 
| {OIDC:Prompt} | Der Abfragezeichenfolgen-Parameter `prompt`. | login |
| {OIDC:RedirectUri} |Der Abfragezeichenfolgen-Parameter `redirect_uri`. | https://jwt.ms |
| {OIDC:Resource} |Der Abfragezeichenfolgen-Parameter `resource`. | – |
| {OIDC:Scope} |Der Abfragezeichenfolgen-Parameter `scope`. | openid |
| {OIDC:Username}| Der Benutzername des Benutzers aus dem [Flow für Kennwortanmeldeinformationen von Ressourcenbesitzern](ropc-custom.md)| emily@contoso.com| 

### <a name="context"></a>Kontext

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Die Version des Frameworks für die Identitätsfunktion (Buildnummer).  | 1.0.507.0 |
| {Context:CorrelationId} | Die Korrelations-ID.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Datum und Uhrzeit in UTC.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |Die Methode zur Richtlinienbereitstellung.  | Bereitstellung |
| {Context:IPAddress} | Die Benutzer-IP-Adresse. | 11.111.111.11 |
| {Context:KMSI} | Gibt an, ob das Kontrollkästchen [Angemeldet bleiben](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) aktiviert ist. |  true |

### <a name="claims"></a>Ansprüche 

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {Claim:claim type} | Ein Bezeichner eines Anspruchstyps, der bereits im Abschnitt „ClaimsSchema“ in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist.  Beispiel: `{Claim:displayName}` oder `{Claim:objectId}` | Ein Wert eines Anspruchstyps.|


### <a name="oauth2-key-value-parameters"></a>OAuth2-Schlüssel-Wert-Parameter

Jeder Parametername, der als Bestandteil einer OIDC- oder OAuth2-Anforderung eingeschlossen wird, kann einem Anspruch in der User Journey zugeordnet werden. Beispielsweise kann die Anforderung von der Anwendung einen Abfragezeichenfolgen-Parameter mit einem der Namen `app_session` oder `loyalty_number` oder eine beliebige benutzerdefinierte Abfragezeichenfolge enthalten.

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Ein Abfragezeichenfolgen-Parameter. | Hawaii |
| {OAUTH-KV:app_session} | Ein Abfragezeichenfolgen-Parameter. | A3C5R |
| {OAUTH-KV:loyalty_number} | Ein Abfragezeichenfolgen-Parameter. | 1234 |
| {OAUTH-KV:beliebige benutzerdefinierte Abfragezeichenfolge} | Ein Abfragezeichenfolgen-Parameter. | – |

### <a name="oauth2"></a>OAuth2

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Zugriffstoken | – |
| {oauth2:refresh_token} | Das Aktualisierungstoken. | NICHT ZUTREFFEND |


### <a name="saml"></a>SAML

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {SAML:AuthnContextClassReferences} | Der Wert des `AuthnContextClassRef`-Elements aus der SAML-Anforderung. | urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport |
| {SAML:NameIdPolicyFormat} | Das `Format`-Attribut aus dem `NameIDPolicy`-Element der SAML-Anforderung. | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| {SAML:Issuer} |  Der Wert des SAML-`Issuer`-Elements aus der SAML-Anforderung.| `https://contoso.com` |
| {SAML:AllowCreate} | Der Wert des `AllowCreate`-Attributs aus dem `NameIDPolicy`-Element der SAML-Anforderung. | True |
| {SAML:ForceAuthn} | Der Wert des `ForceAuthN`-Attributs aus dem `AuthnRequest`-Element der SAML-Anforderung. | True |
| {SAML:ProviderName} | Der Wert des `ProviderName`-Attributs aus dem `AuthnRequest`-Element der SAML-Anforderung.| Contoso.com |
| {SAML:RelayState} | Der Abfragezeichenfolgen-Parameter `RelayState`.| 
| {SAML:Subject} | Der Antragsteller (`Subject`) aus dem Element „NameId“ der SAML-AuthN-Anforderung.| 

## <a name="using-claim-resolvers"></a>Verwenden von Anspruchskonfliktlösern

Sie können Anspruchskonfliktlöser mit den folgenden Elementen verwenden:

| Element | Element | Einstellungen |
| ----- | ----------------------- | --------|
|Technisches Profil „Application Insights“ |`InputClaim` | |
|[Azure Active Directory](active-directory-technical-profile.md): technisches Profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OAuth2](oauth2-technical-profile.md): technisches Profil| `InputClaim`, `OutputClaim`| 1, 2|
|[OpenID Connect](openid-connect-technical-profile.md): technisches Profil| `InputClaim`, `OutputClaim`| 1, 2|
|[Anspruchstransformation](claims-transformation-technical-profile.md): technisches Profil| `InputClaim`, `OutputClaim`| 1, 2|
|[RESTful-Anbieter](restful-technical-profile.md): technisches Profil| `InputClaim`| 1, 2|
|[SAML-Identitätsanbieter](saml-identity-provider-technical-profile.md): technisches Profil| `OutputClaim`| 1, 2|
|[Selbstbestätigt](self-asserted-technical-profile.md): technisches Profil| `InputClaim`, `OutputClaim`| 1, 2|
|[ContentDefinition](contentdefinitions.md)| `LoadUri`| |
|[ContentDefinitionParameters](relyingparty.md#contentdefinitionparameters)| `Parameter` | |
|[RelyingParty](relyingparty.md#technicalprofile): technisches Profil| `OutputClaim`| 2 |

Einstellungen:
1. Die `IncludeClaimResolvingInClaimsHandling`-Metadaten müssen auf `true` festgelegt werden.
1. Das Eingabe- oder Ausgabeanspruchsattribut `AlwaysUseDefaultValue` muss auf `true` festgelegt werden.

## <a name="claim-resolvers-samples"></a>Beispiele für Anspruchskonfliktlöser

### <a name="restful-technical-profile"></a>Technisches Profil „RESTful“

In einem technischen [RESTful](restful-technical-profile.md)-Profil können Sie die Sprache des Benutzers, den Richtliniennamen, den Bereich und die Client-ID senden. Basierend auf den Ansprüchen kann die REST-API eine benutzerdefinierte Geschäftslogik ausführen und bei Bedarf eine lokalisierte Fehlermeldung auslösen.

Das folgende Beispiel zeigt ein technisches RESTful-Profil mit diesem Szenario:

```xml
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:Scope}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" AlwaysUseDefaultValue="true" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direkte Anmeldung

Bei der Verwendung von Anspruchskonfliktlösern können Sie den Anmeldenamen auffüllen oder sich direkt bei einem bestimmten sozialen Netzwerk als Identitätsanbieter anmelden, wie z. B. bei einem Facebook-, LinkedIn- oder Microsoft-Konto. Weitere Informationen finden Sie unter [Einrichten einer direkten Anmeldung mit Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamische Benutzeroberflächenanpassung

Mit Azure AD B2C können Sie Abfragezeichenfolgen-Parameter an Ihre HTML-Inhaltsdefinitions-Endpunkte übergeben, um den Seiteninhalt dynamisch zu rendern. Mit diesem Feature können Sie z. B. das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf der Basis eines benutzerdefinierten Parameters ändern, den Sie von der Web- oder Mobilanwendung übergeben. Weitere Informationen finden Sie unter [Dynamisches Konfigurieren der Benutzeroberfläche mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri). Sie können Ihre HTML-Seite auch auf Grundlage eines Sprachparameters lokalisieren, oder Sie können den Inhalt basierend auf der Client-ID ändern.

Im folgenden Beispiel wird in der Abfragezeichenfolge ein Parameter namens **campaignId** mit einem Wert von `Hawaii`, einem **language**-Code (Sprache) von `en-US` und **app** für die Client-ID übergeben:

```xml
<UserJourneyBehaviors>
  <ContentDefinitionParameters>
    <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
    <Parameter Name="language">{Culture:RFC5646}</Parameter>
    <Parameter Name="app">{OIDC:ClientId}</Parameter>
  </ContentDefinitionParameters>
</UserJourneyBehaviors>
```

Als Resultat sendet Azure AD B2C die oben genannten Parameter an die HTML-Inhaltsseite:

```
/selfAsserted.aspx?campaignId=hawaii&language=en-US&app=0239a9cc-309c-4d41-87f1-31288feb2e82
```

### <a name="content-definition"></a>Inhaltsdefinition

In einer [ContentDefinition](contentdefinitions.md) `LoadUri` können Sie Anspruchskonfliktlöser senden, um Inhalt von verschiedenen Speicherorten basierend auf den verwendeten Parametern abzurufen.

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

### <a name="application-insights-technical-profile"></a>Technisches Profil „Application Insights“

Mit Azure Application Insights und Anspruchskonfliktlösern können Sie Einblicke in Benutzerverhalten gewinnen. Im technischen Profil „Application Insights“ senden Sie Eingabeansprüche, die in Azure Application Insights persistent gespeichert werden. Weitere Informationen finden Sie unter [Nachverfolgen des Benutzerverhaltens in Azure AD B2C-Journeys mithilfe von Application Insights](analytics-with-application-insights.md). Im folgende Beispiel wird die Richtlinien-ID, die Korrelations-ID, die Sprache und die Client-ID an Azure Application Insights gesendet.

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Alternate Email</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
    <InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
    <InputClaim ClaimTypeReferenceId="AppId" PartnerClaimType="{property:App}" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
</TechnicalProfile>
```

### <a name="relying-party-policy"></a>Richtlinie für die vertrauende Seite

In einem technischen Profil der Richtlinie für die [vertrauende Seite](relyingparty.md) können Sie die Mandanten-ID oder die Korrelations-ID an die Anwendung der vertrauenden Seite innerhalb des JWT senden.

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
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
        <OutputClaim ClaimTypeReferenceId="correlationId" AlwaysUseDefaultValue="true" DefaultValue="{Context:CorrelationId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
```
