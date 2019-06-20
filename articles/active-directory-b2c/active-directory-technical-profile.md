---
title: Definieren eines technischen Azure Active Directory-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein technisches Azure Active Directory-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8b8bbe540d9e296b0f6a0c11a62d3b861e0115d3
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507444"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen Azure Active Directory-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C bietet Unterstützung für die Benutzerverwaltung von Azure Active Directory. In diesem Artikel werden die Einzelheiten eines technischen Profils für die Interaktion mit einem Anspruchsanbieter thematisiert, der dieses standardisierte Protokoll unterstützt.

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null` enthalten.

Alle technischen Azure AD-Profile enthalten das technische Profil **AAD-Common**. Die folgenden technischen Profile geben nicht das Protokoll an, da das Protokoll im technischen Profil **AAD-Common** konfiguriert ist:

- **AAD-UserReadUsingAlternativeSecurityId** und **AAD-UserReadUsingAlternativeSecurityId-NoError**: Suchen eines Social Media-Kontos im Verzeichnis.
- **AAD-UserWriteUsingAlternativeSecurityId:** Erstellen eines neuen Social Media-Kontos.
- **AAD-UserReadUsingEmailAddress:** Suchen eines lokalen Kontos im Verzeichnis. 
- **AAD-UserWriteUsingLogonEmail:** Erstellen eines neuen lokalen Kontos.
- **AAD-UserWritePasswordUsingObjectId:** Aktualisieren des Kennworts eines lokalen Kontos.
- **AAD-UserWriteProfileUsingObjectId:** Aktualisieren eines Benutzerprofils eines lokalen oder Social Media-Kontos.
- **AAD-UserReadUsingObjectId:** Lesen eines Benutzerprofils eines lokalen oder Social Media-Kontos.
- **AAD-UserWritePhoneNumberUsingObjectId:** Schreiben der MFA-Telefonnummer eines lokalen oder Social Media-Kontos

Das folgende Beispiel zeigt das technische Profil **AAD-Common**:

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>Eingabeansprüche

Die folgenden technischen Profile enthalten **InputClaims** für Social Media- und lokale Konten:

- Das technische Profil für Social Media-Konten **AAD-UserReadUsingAlternativeSecurityId** und **AAD-UserWriteUsingAlternativeSecurityId** enthält den Anspruch **AlternativeSecurityId**. Dieser Anspruch enthält die Benutzer-ID des Social Media-Kontos.
- Die technischen Profile für lokale Konten **AAD-UserReadUsingEmailAddress** und **AAD-UserWriteUsingLogonEmail** enthalten den Anspruch **email**. Dieser Anspruch enthält den Anmeldenamen des lokalen Kontos.
- Die gemeinsamen technischen Profile (für lokale und Social Media-Konten) **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId** und **AAD-UserWritePhoneNumberUsingObjectId** enthalten den Anspruch **objectId**. Der eindeutige Bezeichner eines Kontos.

Das **InputClaimsTransformations**-Element darf eine Sammlung von **InputClaimsTransformation**-Elementen, die zum Ändern der Eingabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste von Ansprüchen, die vom technischen Azure AD-Profil zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der in Azure Active Directory definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht von Azure Active Directory zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Das technische Profil **AAD-UserWriteUsingLogonEmail** erstellt z.B. ein lokales Konto und gibt die folgenden Ansprüche zurück:

- **objectId** ist der Bezeichner des neuen Kontos.
- **newUser** gibt an, ob der Benutzer neu ist.
- **authenticationSource** legt die Authentifizierung auf `localAccountAuthentication` fest.
- **userPrincipalName** ist der Benutzerprinzipalname des neuen Kontos.
- **signInNames.emailAddress** ist der Anmeldename des Kontos, ähnlich dem **email**-Eingabeanspruch.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

Das **PersistedClaims**-Element enthält alle Werte, die von Azure AD beibehalten werden sollen, zusammen mit möglichen Zuordnungsinformationen zwischen einem Anspruchstyp, der bereits im Abschnitt ClaimsSchema in der Richtlinie definiert ist, und dem Azure AD-Attributnamen. 

Das technische Profil **AAD-UserWriteUsingLogonEmail** erstellt ein lokales Konto und behält die folgenden Ansprüche bei:

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Der Name des Anspruchs ist der Name des Azure AD-Attributs, sofern nicht das **PartnerClaimType**-Attribut angegeben wurde, das den Namen des Azure AD-Attribut enthält.

## <a name="requirements-of-an-operation"></a>Anforderungen für einen Vorgang

- Es muss genau ein **InputClaim**-Element im Anspruchsbehälter für alle technischen Azure AD-Profile vorhanden sein. 
- Wenn der Vorgang `Write` oder `DeleteClaims` ist, muss er auch im **PersistedClaims**-Element enthalten sein.
- Der Wert des **userPrincipalName**-Anspruchs muss das Format `user@tenant.onmicrosoft.com` aufweisen.
- Der **displayName**-Anspruch ist erforderlich und darf keine leere Zeichenfolge sein.

## <a name="azure-ad-technical-provider-operations"></a>Vorgänge des technischen Azure AD-Anbieters

### <a name="read"></a>Lesen

Der **Read**-Vorgang liest die Daten zu einem einzelnen Benutzerkonto. Für das Lesen von Benutzerdaten müssen Sie einen Schlüssel als Eingabeanspruch angeben, z.B. **objectId**, **userPrincipalName**, **signInNames** (beliebiger Typ, Benutzername und E-Mail-basiertes Konto) oder **alternativeSecurityId**.  

Das folgende technische Profil liest Daten über ein Benutzerkonto mithilfe der Objekt-ID des Benutzers:

```XML
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

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>Schreiben

Der **Write**-Vorgang erstellt oder aktualisiert ein einzelnes Benutzerkonto. Für das Schreiben in einem Benutzerkonto müssen Sie einen Schlüssel als Eingabeanspruch angeben, z.B. **objectId**, **userPrincipalName**, **signInNames.emailAddress** oder **alternativeSecurityId**.  

Das folgende technische Profil erstellt neues Social Media-Konto:

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

Der **DeleteClaims**-Vorgang löscht die Informationen aus einer angegebenen Liste von Ansprüchen. Für das Löschen von Informationen aus Ansprüchen müssen Sie einen Schlüssel als Eingabeanspruch angeben, z.B. **objectId**, **userPrincipalName**, **signInNames.emailAddress** oder **alternativeSecurityId**.  

Das folgende technische Profil löscht Ansprüche:

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

Der **DeleteClaimsPrincipal**-Vorgang löscht ein einzelnes Benutzerkonto aus dem Verzeichnis. Für das Löschen eines Benutzerkontos müssen Sie einen Schlüssel als Eingabeanspruch angeben, z.B. **objectId**, **userPrincipalName**, **signInNames.emailAddress** oder **alternativeSecurityId**.  

Das folgende technische Profil löscht ein Benutzerkonto anhand des Benutzerprinzipalnamens aus dem Verzeichnis:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

Das folgende technische Profil löscht ein Social Media-Benutzerkonto mit **alternativeSecurityId**:

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>Metadaten

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Vorgang | Ja | Der Vorgang, der ausgeführt werden soll. Mögliche Werte: `Read`, `Write`, `DeleteClaims` oder `DeleteClaimsPrincipal`. | 
| RaiseErrorIfClaimsPrincipalDoesNotExist | Nein | Es wird ein Fehler ausgelöst, wenn das Benutzerobjekt im Verzeichnis nicht vorhanden ist. Mögliche Werte: `true` oder `false`. | 
| UserMessageIfClaimsPrincipalDoesNotExist | Nein | Wenn ein Fehler ausgelöst werden soll (siehe Beschreibung zum RaiseErrorIfClaimsPrincipalDoesNotExist-Attribut), geben Sie die Meldung an, die dem Benutzer angezeigt werden soll, wenn das Benutzerobjekt nicht vorhanden ist. Der Wert kann [lokalisiert](localization.md) sein.| 
| RaiseErrorIfClaimsPrincipalAlreadyExists | Nein | Es wird ein Fehler ausgelöst, wenn das Benutzerobjekt bereits vorhanden ist. Mögliche Werte: `true` oder `false`.| 
| UserMessageIfClaimsPrincipalAlreadyExists | Nein | Wenn ein Fehler ausgelöst werden soll (siehe Beschreibung zum RaiseErrorIfClaimsPrincipalAlreadyExists-Attribut), geben Sie die Meldung an, die dem Benutzer angezeigt werden soll, wenn das Benutzerobjekt bereits vorhanden ist. Der Wert kann [lokalisiert](localization.md) sein.| 
| ApplicationObjectId | Nein | Die Anwendungsobjekt-ID für vorhandene Attribute. Wert: ObjectId einer Anwendung. Weitere Informationen finden Sie unter [Verwenden von benutzerdefinierten Attributen in einer benutzerdefinierten Richtlinie für die Profilbearbeitung](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 
| ClientId | Nein | Die Client-ID für den Zugriff auf den Mandanten als Drittanbieter. Weitere Informationen finden Sie unter [Verwenden von benutzerdefinierten Attributen in einer benutzerdefinierten Richtlinie für die Profilbearbeitung](active-directory-b2c-create-custom-attributes-profile-edit-custom.md). | 














