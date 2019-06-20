---
title: 'Azure Active Directory B2C: RelyingParty | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das RelyingParty-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2b5e9bfe6eaa9b84e259d941760792635a2994f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512851"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das **RelyingParty**-Element legt die User Journey fest, die für die aktuelle Anforderung an Azure Active Directory (Azure AD) B2C erzwungen werden soll. Es legt außerdem die Liste von Ansprüchen fest, die die Anwendung der vertrauenden Seite als Teil des ausgestellten Tokens benötigt. Eine Anwendung der vertrauenden Seite, z.B. eine Web-, mobile oder Desktopanwendung, ruft die Richtliniendatei der vertrauenden Seite auf. Die Richtliniendatei der vertrauenden Seite führt eine bestimmte Aufgabe aus, z.B. eine Anmeldung, das Zurücksetzen eines Kennworts oder eine Profilbearbeitung. Mehrere Anwendungen können die gleiche Anwendung der vertrauenden Seite verwenden, und eine einzelne Anwendung kann mehrere Richtlinien verwenden. Alle Anwendungen der vertrauenden Seite erhalten das gleiche Token mit Ansprüchen, und der Benutzer durchläuft die gleiche User Journey.

Im folgenden Beispiel wird ein **RelyingParty**-Element in der Richtliniendatei *B2C_1A_signup_signin* gezeigt:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description> 
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

Das optionale **RelyingParty**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | Die standardmäßige User Journey für die Anwendung der vertrauenden Seite. |
| UserJourneyBehaviors | 0:1 | Der Bereich für die Verhalten der User Journey. |
| TechnicalProfile | 1:1 | Ein technisches Profil, das von der Anwendung der vertrauenden Seite unterstützt wird. Das technische Profil stellt einen Vertrag für das Kontaktieren von Azure AD B2C durch die Anwendung der vertrauenden Seite bereit. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

Das `DefaultUserJourney`-Element gibt einen Verweis auf den Bezeichner der User Journey an, die in der Regel in der Basis- oder Erweiterungsrichtlinie definiert wird. In den folgenden Beispielen wird die im **RelyingParty**-Element angegebene User Journey für die Registrierung oder Anmeldung dargestellt:

*B2C_1A_signup_signin*-Richtlinie:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* oder *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Das **DefaultUserJourney**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner der User Journey in der Richtlinie. Weitere Informationen finden Sie unter [User Journeys](userjourneys.md). |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

Das **UserJourneyBehaviors**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Der Bereich des SSO-Sitzungsverhaltens (Einmaliges Anmelden, Single Sign-On) einer User Journey. |
| SessionExpiryType |0:1 | Das Authentifizierungsverhalten der Sitzung. Mögliche Werte: `Rolling` oder `Absolute`. Der (Standard-) Wert `Rolling` gibt an, dass der Benutzer angemeldet bleibt, solange der Benutzer ständig in der Anwendung aktiv ist. Der Wert `Absolute` gibt an, dass der Benutzer zur erneuten Authentifizierung gezwungen wird, nachdem der Zeitraum abgelaufen ist, der von der Sitzungsdauer der Anwendung festgelegt wird. |
| SessionExpiryInSeconds | 0:1 | Die Lebensdauer des als Integer angegebenen Azure AD B2C-Sitzungscookies, das bei erfolgreicher Authentifizierung im Benutzerbrowser gespeichert wird. |
| JourneyInsights | 0:1 | Kopieren des Azure Application Insights-Instrumentierungsschlüssels, der verwendet werden soll. |
| ContentDefinitionParameters | 0:1 | Die Liste von Schlüssel-Wert-Paaren, die an der LoadUri-Parameter der ContentDefinition angefügt wird. |

### <a name="singlesignon"></a>SingleSignOn

Das **SingleSignOn**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| `Scope` | Ja | Der Bereich des SSO-Verhaltens. Mögliche Werte: `Suppressed`, `Tenant`, `Application` oder `Policy`. Der Wert `Suppressed` gibt an, dass das Verhalten unterdrückt wird. Bei einer SSO-Sitzung wird beispielsweise keine Sitzung für den Benutzer beibehalten, und er wird immer dazu aufgefordert, einen Identitätsanbieter auszuwählen. Der Wert `TrustFramework` gibt an, dass das Verhalten für alle Richtlinien im Vertrauensframework angewendet wird. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien eines Vertrauensframeworks navigiert, nicht dazu aufgefordert, einen Identitätsanbieter auszuwählen. Der Wert `Tenant` gibt an, dass das Verhalten auf alle Richtlinien im Mandanten angewendet wird. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien eines Mandanten navigiert nicht, dazu aufgefordert, einen Identitätsanbieter auszuwählen. Der Wert `Application` gibt an, dass das Verhalten auf alle Richtlinien für die Anwendung angewendet wird, die die Anforderung stellt. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien einer Anwendung navigiert, nicht dazu aufgefordert, einen Identitätsanbieter auszuwählen. Der Wert `Policy` gibt an, dass das Verhalten nur auf eine Richtlinie angewendet wird. Beispielsweise wird ein Benutzer, der durch zwei User Journeys für Richtlinien eines Vertrauensframeworks navigiert, dazu aufgefordert, einen Identitätsanbieter auszuwählen, wenn er zwischen Richtlinien wechselt. |
| KeepAliveInDays | Ja | Steuert, wie lange der Benutzer angemeldet bleibt. Durch Festlegen des Werts auf 0 wird die Funktion „Angemeldet bleiben“ deaktiviert. Weitere Informationen finden Sie unter [Angemeldet bleiben](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

Das **JourneyInsights**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| TelemetryEngine | Ja | Der Wert muss `ApplicationInsights` sein. | 
| InstrumentationKey | Ja | Die Zeichenfolge, die den Instrumentierungsschlüssel für das Application Insights-Element enthält. |
| DeveloperMode | Ja | Mögliche Werte: `true` oder `false`. Wenn `true`, beschleunigt Application Insights die Telemetriedaten durch die Verarbeitungspipeline. Diese Einstellung eignet sich für die Entwicklung, bei höheren Volumen ist sie jedoch eingeschränkt. Die detaillierten Aktivitätsprotokolle sind lediglich zur Unterstützung bei der Entwicklung benutzerdefinierter Richtlinien konzipiert. Verwenden Sie den Entwicklungsmodus nicht in der Produktion. Protokolle erfassen alle Ansprüche, die während der Entwicklung an die Identitätsanbieter und von Ihnen gesendet werden. Bei Verwendung in der Produktion übernehmen Entwickler die Verantwortung für PII (Privately Identifiable Information, privat identifizierbare Informationen), die in dem App Insights-Protokoll gesammelt werden, das sie besitzen. Diese detaillierten Protokolle werden nur gesammelt, wenn dieser Wert auf `true` festgelegt ist.|
| ClientEnabled | Ja | Mögliche Werte: `true` oder `false`. Wenn `true`, wird das clientseitige Application Insights-Skript zum Nachverfolgen der Seitenansicht und von clientseitigen Fehlern gesendet. | 
| ServerEnabled | Ja | Mögliche Werte: `true` oder `false`. Wenn `true`, wird die vorhandene JSON-Datei „UserJourneyRecorder“ als benutzerdefiniertes Ereignis an Application Insights übermittelt. | 
| TelemetryVersion | Ja | Der Wert muss `1.0.0` sein. | 

Weitere Informationen finden Sie unter [Erfassen von Protokollen](active-directory-b2c-troubleshoot-custom.md).

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Mithilfe benutzerdefinierter Richtlinien in Azure AD B2C können Sie einen Parameter in einer Abfragezeichenfolge senden. Durch Übergeben des Parameters an Ihren HTML-Endpunkt können Sie den Seiteninhalt dynamisch ändern. Sie können z.B. das Hintergrundbild auf der Azure AD B2C-Registrierungs- oder Anmeldeseite auf der Basis eines Parameters ändern, den Sie von der Web- oder Mobilanwendung übergeben. Azure AD B2C übergibt die Abfragezeichenfolgenparameter an Ihre dynamische HTML-Datei, z.B. eine ASPX-Datei. 

Im folgenden Beispiel wird ein Parameter namens `campaignId` mit dem Wert `hawaii` in der Abfragezeichenfolge übergeben:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

Das **ContentDefinitionParameters**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Eine Zeichenfolge, die das Schlüssel-Wert-Paar enthält, das der Abfragezeichenfolge einer Inhaltsdefinition des URI zum Laden angefügt wird. |

Das **ContentDefinitionParameter**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Name | Ja | Der Name des Schlüssel-Wert-Paars. |

Weitere Informationen finden Sie unter [Konfigurieren der Benutzeroberfläche mit dynamischen Inhalten mithilfe von benutzerdefinierten Richtlinien](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="technicalprofile"></a>TechnicalProfile

Das **TechnicalProfile**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- | 
| id | Ja | Der Wert muss `PolicyProfile` sein. |

**TechnicalProfile** enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Die Zeichenfolge, die den Namen des technischen Profils enthält, der Benutzern angezeigt wird. |
| BESCHREIBUNG | 0:1 | Die Zeichenfolge, die die Beschreibung des technischen Profils enthält, die Benutzern angezeigt wird. |
| Protocol | 1:1 | Das Protokoll, das für den Verbund verwendet wird. |
| Metadaten | 0:1 | Die Sammlung von *Elementen* von Schlüssel-Wert-Paaren, die vom Protokoll für die Kommunikation mit dem Endpunkt im Verlauf einer Transaktion verwendet wird, um die Interaktion zwischen der vertrauenden Seite und anderen Teilnehmern der Community zu konfigurieren. |
| OutputClaims | 0:1 | Eine Liste von Anspruchstypen, die im technischen Profil als Ausgabe verwendet werden. Jedes dieser Elemente enthält einen Verweis auf ein **ClaimType**-Element, das bereits im **ClaimsSchema**-Abschnitt oder in einer Richtlinie, die diese Richtliniendatei erbt, definiert wurde. |
| SubjectNamingInfo | 0:1 | Der Antragstellername, der in Tokens verwendet wird. |

Das **Protocol**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Name | Ja | Der Name eines gültigen Protokolls, das von Azure AD B2C unterstützt und als Teil des technischen Profils verwendet wird. Mögliche Werte: `OpenIdConnect` oder `SAML2`. Der Wert `OpenIdConnect` stellt den Protokollstandard „OpenID Connect 1.0“ gemäß der Vorgaben der OpenID Foundation dar. Der Wert `SAML2` stellt den Protokollstandard „SAML 2.0“ gemäß der Vorgaben von OASIS dar. Verwenden Sie in der Produktion keine SAML-Token. |

## <a name="outputclaims"></a>OutputClaims

Das **OutputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Der Name eines erwarteten Anspruchstyps in der Liste der unterstützten Richtlinien, die die vertrauende Seite abonniert. Dieser Anspruch dient dem technischen Profil als Ausgabe. |

Das **OutputClaim**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Ein Verweis auf ein **ClaimType**-Element, das bereits im **ClaimsSchema**-Abschnitt der Richtliniendatei definiert wurde. |
| DefaultValue | Nein | Ein Standardwert, der verwendet werden kann, wenn der Wert des Anspruchs leer ist. |
| PartnerClaimType | Nein | Sendet den Anspruch wie in der ClaimType-Definition konfiguriert über einen anderen Namen. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Mit dem **SubjectNameingInfo**-Element steuern Sie den Wert des Tokenantragstellers:
- **JWT-Token:** Der `sub`-Anspruch. Dies ist ein Prinzipal, für den das Token Informationen zusichert, z.B. der Benutzer einer Anwendung. Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Er kann für die Durchführung von sicheren Autorisierungsüberprüfungen verwendet werden, z.B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Der Anspruch „Antragsteller“ wird standardmäßig mit der Objekt-ID des Benutzers im Verzeichnis aufgefüllt. Weitere Informationen finden Sie unter [Token, Sitzung und einmaliges Anmelden – Konfiguration](active-directory-b2c-token-session-sso.md).
- **SAML-Token:** Das `<Subject><NameID>`-Element, das das Subject-Element identifiziert.

Das **SubjectNamingInfo**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimType | Ja | Ein Verweis auf das **PartnerClaimType**-Element eines Ausgabeanspruchs. Die Ausgabeansprüche müssen in der Richtlinie der **OutputClaims**-Sammlung der vertrauenden Seite definiert werden. |

Im folgenden Beispiel wird gezeigt, wie eine vertrauende OpenId Connect-Seite definiert wird. Die Informationen zum Namen des Antragstellers werden als `objectId` konfiguriert:

```XML
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
Das JWT-Token enthält den `sub`-Anspruch mit der Objekt-ID des Benutzers:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


