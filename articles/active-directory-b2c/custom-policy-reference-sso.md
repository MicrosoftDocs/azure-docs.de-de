---
title: Sitzungsverwaltung für einmaliges Anmelden mit benutzerdefinierten Richtlinien
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie SSO-Sitzungen mithilfe benutzerdefinierter Richtlinien in Azure AD B2C verwalten.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3e47eda29b0ad1c47edad08195b2ffd0fe3835af
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750474"
---
# <a name="single-sign-on-session-management-in-azure-active-directory-b2c"></a>Sitzungsverwaltung für einmaliges Anmelden in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Die [SSO-Sitzungsverwaltung](session-overview.md) verwendet dieselbe Semantik wie jedes andere technische Profil in benutzerdefinierten Richtlinien. Wenn ein Orchestrierungsschritt ausgeführt wird, wird das technische Profil, das dem Schritt zugeordnet ist, nach einem `UseTechnicalProfileForSessionManagement`-Verweis abgefragt. Falls vorhanden, wird der referenzierte SSO-Sitzungsanbieter überprüft, um festzustellen, ob der Benutzer ein Sitzungsteilnehmer ist. In diesem Fall wird der SSO-Sitzungsanbieter dazu verwendet, um die Sitzung wieder aufzufüllen. Wenn die Ausführung eines Orchestrierungsschritts abgeschlossen ist, wird entsprechend der Anbieter verwendet, um Informationen in der Sitzung zu speichern, wenn ein SSO-Sitzungsanbieter angegeben wurde.

Azure AD B2C hat eine Reihe von SSO-Sitzungsanbietern definiert, die verwendet werden können:

|Sitzungsanbieter  |`Scope`  |
|---------|---------|
|[NoopSSOSessionProvider](#noopssosessionprovider)     |  Keine       |       
|[DefaultSSOSessionProvider](#defaultssosessionprovider)    | Interner Azure AD B2C-Sitzungs-Manager      |       
|[ExternalLoginSSOSessionProvider](#externalloginssosessionprovider)     | Identitätsanbieter zwischen Azure AD B2C und OAuth1, OAuth2 oder OpenID Connect        |         |
|[OAuthSSOSessionProvider](#oauthssosessionprovider)     | Zwischen einer Anwendung der vertrauenden Seite mit OAuth2 oder OpenID Connect und Azure AD B2C        |        
|[SamlSSOSessionProvider](#samlssosessionprovider)     | Zwischen Azure AD B2C und SAML-Identitätsanbieter. Und zwischen einem SAML-Dienstanbieter (Anwendung der vertrauenden Seite) und Azure AD B2C.  |        




SSO Verwaltungsklassen werden mithilfe des `<UseTechnicalProfileForSessionManagement ReferenceId="{ID}" />`-Elements eines technischen Profils angegeben.

## <a name="input-claims"></a>Eingabeansprüche

Das `InputClaims`-Element ist leer oder nicht vorhanden.

## <a name="persisted-claims"></a>Persistente Ansprüche

Ansprüche, die an die Anwendung zurückgegeben oder von Vorbedingungen in nachfolgenden Schritten verwendet werden müssen, sollten in der Sitzung gespeichert oder durch einen Lesevorgang vom Profil des Benutzers im Verzeichnis ergänzt werden. Die Verwendung persistenter Ansprüche stellt sicher, dass Ihre Authentifizierungsmethoden bei fehlenden Ansprüchen nicht fehlschlagen. Verwenden Sie das `<PersistedClaims>`-Element des technischen Profils, um Ansprüche in der Sitzung hinzuzufügen. Wenn der Anbieter dazu verwendet wird, um die Sitzung erneut aufzufüllen, werden die bestehen gebliebenen Ansprüche zum Anspruchsbehälter hinzugefügt.

## <a name="output-claims"></a>Ausgabeansprüche

`<OutputClaims>` dienen zum Abrufen von Ansprüchen aus der Sitzung.

## <a name="session-providers"></a>Sitzungsanbieter

### <a name="noopssosessionprovider"></a>NoopSSOSessionProvider

Wie der Name besagt, ist dieser Anbieter untätig. Dieser Anbieter kann zum Unterdrücken des SSO-Verhaltens für ein bestimmtes technisches Profil verwendet werden. Das folgende technische `SM-Noop`-Profil ist im [Starter Pack für benutzerdefinierte Richtlinien](custom-policy-get-started.md#custom-policy-starter-pack) enthalten.

```xml
<TechnicalProfile Id="SM-Noop">
  <DisplayName>Noop Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.NoopSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="defaultssosessionprovider"></a>DefaultSSOSessionProvider

Dieser Anbieter kann zum Speichern von Ansprüchen in einer Sitzung verwendet werden. Auf diesen Anbieter wird in der Regel in einem technischen Profil verwiesen, über das lokale und Verbundkonten verwaltet werden. Das folgende technische `SM-AAD`-Profil ist im [Starter Pack für benutzerdefinierte Richtlinien](custom-policy-get-started.md#custom-policy-starter-pack) enthalten.

```xml
<TechnicalProfile Id="SM-AAD">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="signInName" />
    <PersistedClaim ClaimTypeReferenceId="authenticationSource" />
    <PersistedClaim ClaimTypeReferenceId="identityProvider" />
    <PersistedClaim ClaimTypeReferenceId="newUser" />
    <PersistedClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectIdFromSession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```


Das folgende technische `SM-MFA`-Profil ist im [Starter Pack für benutzerdefinierte Richtlinien](custom-policy-get-started.md#custom-policy-starter-pack) `SocialAndLocalAccountsWithMfa`enthalten. Dieses technische Profil verwaltet die Multi-Factor Authentication-Sitzung.

```xml
<TechnicalProfile Id="SM-MFA">
  <DisplayName>Session Mananagement Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.DefaultSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isActiveMFASession" DefaultValue="true"/>
  </OutputClaims>
</TechnicalProfile>
```

### <a name="externalloginssosessionprovider"></a>ExternalLoginSSOSessionProvider

Dieser Anbieter wird verwendet, um den Bildschirm „Identitätsanbieter auswählen“ und die Abmeldung bei einem Verbundidentitätsanbieter zu unterdrücken. Er wird in der Regel in einem technischen Profil referenziert, das für einen Verbundidentitätsanbieter wie Facebook oder Azure Active Directory konfiguriert wurde. Das folgende technische `SM-SocialLogin`-Profil ist im [Starter Pack für benutzerdefinierte Richtlinien](custom-policy-get-started.md#custom-policy-starter-pack) enthalten.

```xml
<TechnicalProfile Id="SM-SocialLogin">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.ExternalLoginSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="AlternativeSecurityId" />
  </PersistedClaims>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG|
| --- | --- | --- |
| AlwaysFetchClaimsFromProvider | Nein | Derzeit nicht verwendet, kann ignoriert werden. |

### <a name="oauthssosessionprovider"></a>OAuthSSOSessionProvider

Dieser Anbieter wird zum Verwalten der Azure AD B2C-Sitzungen zwischen einer vertrauenden Seite mit OAuth2 oder OpenID Connect und Azure AD B2C verwendet.

```xml
<TechnicalProfile Id="SM-jwt-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

### <a name="samlssosessionprovider"></a>SamlSSOSessionProvider

Dieser Anbieter wird in Azure AD B2C zum Verwalten der SAML-Sitzungen zwischen einer Anwendung der vertrauenden Seite oder einem SAML-Verbundidentitätsanbieter verwendet. Wenn der SSO-Anbieter zum Speichern einer SAML-Identitätsanbietersitzung verwendet wird, muss `RegisterServiceProviders` auf `false` eingestellt sein. Das folgende technische `SM-Saml-idp`-Profil wird vom [technischen Profil des SAML-Identitätsanbieters](saml-identity-provider-technical-profile.md) verwendet.

```xml
<TechnicalProfile Id="SM-Saml-idp">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="RegisterServiceProviders">false</Item>
  </Metadata>
</TechnicalProfile>
```

Wenn der Anbieter zum Speichern der B2C-SAML-Sitzung verwendet wird, muss `RegisterServiceProviders` auf `true` eingestellt sein. Für die Abmeldung von der SAML-Sitzung ist erforderlich, dass `SessionIndex` und `NameID` abgeschlossen sind.

Das folgende technische `SM-Saml-issuer`-Profil wird vom [technischen Profil des SAML-Ausstellers](saml-issuer-technical-profile.md) verwendet.

```xml
<TechnicalProfile Id="SM-Saml-issuer">
  <DisplayName>Session Management Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
</TechnicalProfile>
```

#### <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG|
| --- | --- | --- |
| IncludeSessionIndex | Nein | Derzeit nicht verwendet, kann ignoriert werden.|
| RegisterServiceProviders | Nein | Gibt an, dass der Anbieter alle SAML-Dienstanbieter registrieren soll, die eine Assertion ausgestellt haben. Mögliche Werte: `true` (Standard) oder `false`.|


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Azure AD B2C-Sitzung](session-overview.md).
- Informationen zum [Konfigurieren des Sitzungsverhaltens in benutzerdefinierten Richtlinien](session-behavior-custom-policy.md).
