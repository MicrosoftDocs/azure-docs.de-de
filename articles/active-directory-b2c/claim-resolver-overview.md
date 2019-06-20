---
title: Informationen zu Anspruchskonfliktlösern in benutzerdefinierten Azure Active Directory B2C-Richtlinien | Microsoft-Dokumentation
description: Erfahren Sie, wie Anspruchskonfliktlöser in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C verwendet werden.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a13d4b0b44c51f78a068b1619fe083a08756af6b
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511608"
---
# <a name="about-claim-resolvers-in-azure-active-directory-b2c-custom-policies"></a>Informationen zu Anspruchskonfliktlösern in benutzerdefinierten Azure Active Directory B2C-Richtlinien

Anspruchskonfliktlöser in [benutzerdefinierten Richtlinien](active-directory-b2c-overview-custom.md) von Azure Active Directory (Azure AD) B2C bieten Kontextinformationen über eine Autorisierungsanforderung, z. B. den Namen der Richtlinie, die Korrelations-ID der Anforderung, die Sprache der Benutzeroberfläche und mehr.

Um einen Anspruchskonfliktlöser in einem Ein- oder Ausgabeanspruch zu verwenden, definieren Sie eine Zeichenfolge **ClaimType**unter dem [ClaimsSchema](claimsschema.md)-Element, und dann Sie legen Sie den **DefaultValue** auf den Anspruchskonfliktlöser in dem Ein- oder Ausgabeanspruchselement fest. Azure AD B2C liest den Wert des Anspruchskonfliktlösers und verwendet den Wert in dem technischen Profil. 

Im folgenden Beispiel, wird ein Anspruchstyp namens `correlationId` definiert mit einem **DataType** von `string`.  

```XML
<ClaimType Id="correlationId">
  <DisplayName>correlationId</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Request correlation Id</UserHelpText>
</ClaimType>
```

Ordnen Sie im technischen Profil den Anspruchskonfliktlöser dem Anspruchstyp zu. Azure AD B2C füllt den Wert des Anspruchskonfliktlösers `{Context:CorrelationId}` in den Anspruch `correlationId` auf, und sende den Anspruch an den technische Profil.

```XML
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
| {Culture:RFC5646} | Der Sprachcode RFC5646. | en-US |

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
| {OIDC:Prompt} | Der Abfragezeichenfolgen-Parameter `prompt`. | Anmeldung |
| {OIDC:Resource} |Der Abfragezeichenfolgen-Parameter `resource`. | – |
| {OIDC:scope} |Der Abfragezeichenfolgen-Parameter `scope`. | openid |

### <a name="context"></a>Kontext

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------- | --------|
| {Context:BuildNumber} | Die Version des Frameworks für die Identitätsfunktion (Buildnummer).  | 1.0.507.0 |
| {Context:CorrelationId} | Die Korrelations-ID.  | 00000000-0000-0000-0000-000000000000 |
| {Context:DateTimeInUtc} |Datum und Uhrzeit in UTC.  | 10/10/2018 12:00:00 PM |
| {Context:DeploymentMode} |Die Methode zur Richtlinienbereitstellung.  | Bereitstellung |
| {Context:IPAddress} | Die Benutzer-IP-Adresse. | 11.111.111.11 |


### <a name="non-protocol-parameters"></a>Nicht-Protokoll-Parameter

Jeder Parametername, der als Bestandteil einer OIDC- oder OAuth2-Anforderung eingeschlossen wird, kann einem Anspruch in der User Journey zugeordnet werden. Beispielsweise kann die Anforderung von der Anwendung einen Abfragezeichenfolgen-Parameter mit einem der Namen `app_session` oder `loyalty_number` oder eine beliebige benutzerdefinierte Abfragezeichenfolge enthalten.

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------------------- | --------|
| {OAUTH-KV:campaignId} | Ein Abfragezeichenfolgen-Parameter. | hawaii |
| {OAUTH-KV:app_session} | Ein Abfragezeichenfolgen-Parameter. | A3C5R |
| {OAUTH-KV:loyalty_number} | Ein Abfragezeichenfolgen-Parameter. | 1234 |
| {OAUTH-KV:beliebige benutzerdefinierte Abfragezeichenfolge} | Ein Abfragezeichenfolgen-Parameter. | – |

### <a name="oauth2"></a>OAuth2

| Anspruch | BESCHREIBUNG | Beispiel |
| ----- | ----------------------- | --------|
| {oauth2:access_token} | Zugriffstoken | – |

## <a name="how-to-use-claim-resolvers"></a>Verwenden von Anspruchskonfliktlösern

### <a name="restful-technical-profile"></a>Technisches Profil „RESTful“

In einem technischen [RESTful](restful-technical-profile.md)-Profil können Sie die Sprache des Benutzers, den Richtliniennamen, den Bereich und die Client-ID senden. Basierend auf diesen Ansprüchen kann die REST-API eine benutzerdefinierte Geschäftslogik ausführen und bei Bedarf eine lokalisierte Fehlermeldung auslösen. 

Das folgende Beispiel zeigt ein technisches RESTful-Profil:

```XML
<TechnicalProfile Id="REST">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" />
    <InputClaim ClaimTypeReferenceId="policyName" DefaultValue="{Policy:PolicyId}" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="{OIDC:scope}" />
    <InputClaim ClaimTypeReferenceId="clientId" DefaultValue="{OIDC:ClientId}" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="direct-sign-in"></a>Direkte Anmeldung

Bei der Verwendung von Anspruchskonfliktlösern können Sie den Anmeldenamen auffüllen oder sich direkt bei einem bestimmten sozialen Netzwerk als Identitätsanbieter anmelden, wie z. B. bei einem Facebook-, LinkedIn- oder Microsoft-Konto. Weitere Informationen finden Sie unter [Einrichten einer direkten Anmeldung mit Azure Active Directory B2C](direct-signin.md).

### <a name="dynamic-ui-customization"></a>Dynamische Benutzeroberflächenanpassung

Mit Azure AD B2C können Sie Abfragezeichenfolgen-Parameter an Ihre HTML-Inhaltsdefinitions-Endpunkte übergeben, sodass Sie den Seiteninhalt dynamisch rendern können. Sie können z.B. das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf der Basis eines benutzerdefinierten Parameters ändern, den Sie von der Web- oder Mobilanwendung übergeben. Weitere Informationen finden Sie unter [Dynamisches Konfigurieren der Benutzeroberfläche mithilfe von benutzerdefinierten Richtlinien in Azure Active Directory B2C](active-directory-b2c-ui-customization-custom-dynamic.md). Sie können Ihre HTML-Seite auch auf Grundlage eines Sprachparameters lokalisieren, oder Sie können den Inhalt basierend auf der Client-ID ändern.

Im folgenden Beispiel wird in der Abfragezeichenfolge ein Parameter namens **campaignId** mit einem Wert von `hawaii`, einem **language**-Code (Sprache) von `en-US` und **app**, das die Client-ID darstellt, übergeben:

```XML
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

### <a name="application-insights-technical-profile"></a>Technisches Profil „Application Insights“

Mit Azure Application Insights und Anspruchskonfliktlösern können Sie Einblicke in Benutzerverhalten gewinnen. Im technischen Profil „Application Insights“ senden Sie Eingabeansprüche, die in Azure Application Insights persistent gespeichert werden. Weitere Informationen finden Sie unter [Nachverfolgen des Benutzerverhaltens in Azure AD B2C-Journeys mithilfe von Application Insights](active-directory-b2c-custom-guide-eventlogger-appins.md). Im folgende Beispiel wird die Richtlinien-ID, die Korrelations-ID, die Sprache und die Client-ID an Azure Application Insights gesendet.

```XML
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
