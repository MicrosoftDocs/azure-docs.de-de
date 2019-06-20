---
title: Definieren eines selbstbestätigten technischen Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C | Microsoft-Dokumentation
description: Definieren Sie ein selbstbestätigtes technisches Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3c728660f1a77c02f1e4b5fdeb467a7dbba4e36a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512659"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines selbstbestätigten technischen Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Alle Interaktionen in Azure Active Directory (Azure AD) B2C, bei denen vom Benutzer eine Eingabe erwartet wird, sind selbstbestätigte technische Profile. Beispiele: Registrierungsseite, Anmeldeseite oder Seite für die Kennwortzurücksetzung.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly als selbstbestätigt enthalten, die von Azure AD B2C verwendet wird: `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Das folgende Beispiel zeigt ein selbstbestätigtes technisches Profil für eine Registrierung per E-Mail:

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```
 
## <a name="input-claims"></a>Eingabeansprüche

In einem selbstbestätigten technischen Profil können Sie die Elemente **InputClaims** und **InputClaimsTransformations** verwenden, um den Wert der Ansprüche vorab aufzufüllen, die auf der selbstbestätigten Seite angezeigt werden (Ausgabeansprüche). In der Richtlinie zur Profilbearbeitung liest beispielsweise die User Journey zunächst das Benutzerprofil aus dem Azure AD B2C-Verzeichnisdienst. Anschließend legt das selbstbestätigte technische Profil die Eingabeansprüchen mit den im Benutzerprofil gespeicherten Daten fest. Diese Ansprüche werden aus dem Benutzerprofil gesammelt und anschließend dem Benutzer vorgelegt, der die vorhandenen Daten dann bearbeiten kann.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```


## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste mit vorzulegenden Ansprüchen zum Sammeln von Daten vom Benutzer. Um die Ausgabeansprüche mit einigen Werten vorab aufzufüllen, verwenden Sie die weiter oben beschriebenen Eingabeansprüche. Das Element kann darüber hinaus auch einen Standardwert enthalten. Die Reihenfolge der Ansprüche in **OutputClaims** bestimmt die Reihenfolge, in der Azure AD B2C die Ansprüche berechnet und auf dem Bildschirm ausgibt. Das **DefaultValue**-Attribut wird erst wirksam, wenn der Anspruch zuvor nicht bereits festgelegt wurde. Wenn der Anspruch jedoch bereits in einem früheren Orchestrierungsschritt festgelegt wurde, wird der Standardwert nicht wirksam, auch wenn der Benutzer keinen Wert angibt. Um die Verwendung eines Standardwerts zu erzwingen, legen Sie das Attribut **AlwaysUseDefaultValue** auf `true` fest. Um den Benutzer zur Eingabe eines Werts für einen bestimmten Ausgabeanspruch zu zwingen, legen Sie das Attribut **Required** des **OutputClaims**-Elements auf `true` fest.

Das **ClaimType**-Element in der **OutputClaims**-Sammlung muss das **UserInputType**-Element auf einen von Azure AD B2C unterstützten Benutzereingabetyp festlegen, wie etwa `TextBox` oder `DropdownSingleSelect`. Alternativ muss das **OutputClaim**-Element einen **DefaultValue** festlegen.  

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Der folgende Ausgabeanspruch ist immer auf `live.com` festgelegt:

```XML
<OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" AlwaysUseDefaultValue="true" />
```

### <a name="use-case"></a>Anwendungsfall

Für Ausgabeansprüche gibt es vier Szenarios:

- **Sammlung der Ausgabeansprüche vom Benutzer**: Wenn Sie Informationen vom Benutzer wie etwa das Geburtsdatum sammeln müssen, sollten Sie den Anspruch zur Sammlung **OutputClaims** hinzufügen. Die Ansprüche, die dem Benutzer vorgelegt werden, müssen den **UserInputType** (`TextBox` oder `DropdownSingleSelect`) angeben. Wenn das selbstbestätigte technische Profil ein technisches Validierungsprofil enthält, das denselben Anspruch ausgibt, legt Azure AD B2C den Anspruch dem Benutzer nicht vor. Wenn kein Ausgabeanspruch vorhanden ist, der dem Benutzer vorgelegt werden könnte, überspringt Azure AD B2C das technische Profil.
- **Festlegen eines Standardwerts in einem Ausgabeanspruch**: Ohne Daten vom Benutzer zu sammeln oder die Daten aus dem technischen Validierungsprofil zurückgeben. Das selbstbestätigte technische Profil `LocalAccountSignUpWithLogonEmail` legt den Anspruch **executed-SelfAsserted-Input** auf `true` fest.
- **Ein technisches Validierungsprofil gibt die Ausgabeansprüche zurück**: Ihr technisches Profil ruft möglicherweise ein technisches Validierungsprofil auf, das einige Ansprüche zurückgibt. Sie sollten die Ansprüche sammeln und an die nächsten Orchestrierungsschritte in der User Journey zurückzugeben. Bei der Anmeldung mit einem lokalen Konto ruft beispielsweise das selbstbestätigte technische Profil mit dem Namen `SelfAsserted-LocalAccountSignin-Email` das technische Validierungsprofil mit dem Namen `login-NonInteractive` auf. Dieses technische Profil überprüft die Anmeldeinformationen des Benutzers und gibt das Benutzerprofil zurück. Beispiele: „userPrincipalName“, „displayName“, „givenName“ und „surName“.
- **Ausgabe der Ansprüche mittels Ausgabeanspruchstransformation**

Im folgenden Beispiel zeigt das selbstbestätigte technische Profil `LocalAccountSignUpWithLogonEmail` die Verwendung von Ausgabeansprüchen und legt **executed-SelfAsserted-Input** auf `true` fest. Die Ansprüche `objectId`, `authenticationSource` und `newUser` werden vom technischen Validierungsprofil `AAD-UserWriteUsingLogonEmail` ausgegeben und werden dem Benutzer nicht vorgelegt.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
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
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

## <a name="persist-claims"></a>Beibehalten von Ansprüchen

Wenn das **PersistedClaims**-Element nicht vorhanden ist, speichert das selbstbestätigte technische Profil die Daten nicht in Azure AD B2C. Stattdessen wird ein technisches Validierungsprofil aufgerufen, das für die Beibehaltung der Daten verantwortlich ist. So verwendet beispielsweise die Registrierungsrichtlinie das selbstbestätigte technische Profil `LocalAccountSignUpWithLogonEmail`, um das neue Benutzerprofil zu erfassen. Das technische Profil `LocalAccountSignUpWithLogonEmail` ruft das technische Validierungsprofil auf, um das Konto in Azure AD B2C zu erstellen.

## <a name="validation-technical-profiles"></a>Verwenden von technischen Validierungsprofilen

Ein technisches Validierungsprofil wird zur Überprüfung von einigen oder allen Ausgabeansprüchen des verweisenden technischen Profils verwendet. Die Eingabeansprüche des technischen Validierungsprofils müssen in den Ausgabeansprüchen des selbstbestätigten technischen Profils enthalten sein. Das technische Validierungsprofil überprüft die Benutzereingabe und kann einen Fehler an den Benutzer zurückgeben. 

Das technische Validierungsprofil kann ein technisches Profil in der Richtlinie sein. Beispiele hierfür sind [Azure Active Directory](active-directory-technical-profile.md) oder ein technisches [REST-API](restful-technical-profile.md)-Profil. Im vorherigen Beispiel überprüft das technische Profil `LocalAccountSignUpWithLogonEmail`, ob der „signinName“ im Verzeichnis vorhanden ist. Ist dies nicht der Fall, erstellt das technische Validierungsprofil ein lokales Konto und gibt „objectId“, „authenticationSource“ und „newUser“ zurück. Das technische Profil `SelfAsserted-LocalAccountSignin-Email` ruft das technische Validierungsprofil `login-NonInteractive` auf, um die Anmeldeinformationen des Benutzers zu überprüfen.

Mit Ihrer Geschäftslogik können Sie durch eine weitere Integration in die Branchenanwendung auch ein technisches REST-API-Profil aufrufen, Eingabeansprüche überschreiben oder Benutzerdaten erweitern. Weitere Informationen finden Sie unter [Technisches Validierungsprofil](validation-technical-profile.md).

## <a name="metadata"></a>Metadaten

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| setting.showContinueButton | Nein | Zeigt die Schaltfläche „Weiter“ an. Mögliche Werte: `true` (Standard) oder `false` |
| setting.showCancelButton | Nein | Zeigt die Schaltfläche „Abbrechen“ an. Mögliche Werte: `true` (Standard) oder `false` |
| setting.operatingMode | Nein | Bei einer Anmeldeseite steuert diese Eigenschaft das Verhalten des Benutzernamensfelds also z.B. die Eingabeüberprüfung und Fehlermeldungen. Erwartete Werte: `Username` oder `Email`. |
| ContentDefinitionReferenceId | Ja | Der Bezeichner der [Inhaltsdefinition](contentdefinitions.md), die diesem technischen Profil zugeordnet ist. |
| EnforceEmailVerification | Nein | Für die Registrierungs- oder Profilbearbeitung, erzwingt eine E-Mail-Überprüfung. Mögliche Werte: `true` (Standard) oder `false`. | 
| setting.showSignupLink | Nein | Zeigt die Schaltfläche „Registrieren“ an. Mögliche Werte: `true` (Standard) oder `false` |
| setting.retryLimit | Nein | Legt fest, wie oft ein Benutzer die Daten eingeben kann, die anhand des technischen Validierungsprofils überprüft werden. Beispiel: Ein Benutzer versucht, ein Konto zu registrieren, das bereits vorhanden ist, und wiederholt den Vorgang, bis der Grenzwert erreicht ist.
| SignUpTarget | Nein | Der Austauschbezeichner für das Registrierungsziel. Wenn der Benutzer auf die Schaltfläche „Registrieren“ klickt, führt Azure AD B2C den angegebenen Austauschbezeichner aus. |

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Das Element **CryptographicKeys** wird nicht verwendet.













