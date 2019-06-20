---
title: TechnicalProfiles | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das TechnicalProfiles-Element einer benutzerdefinierten Richtlinie in Azure Active Directory B2C angeben.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f712634c83fa290ab24d5e8437a82d5f93af0b7f
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512278"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ein **TechnicalProfiles**-Element enthält eine Reihe technischer Profile, die vom Anspruchsanbieter unterstützt werden. Jeder Anspruchsanbieter muss über mindestens ein technisches Profil verfügen, mit dem die Endpunkte und die Protokolle bestimmt werden, die für die Kommunikation mit diesem Anspruchsanbieter erforderlich sind. Ein Anspruchsanbieter kann über mehrere technische Profile verfügen.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Das **TechnicalProfile**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
|---------|---------|---------|
| id | Ja | Ein eindeutiger Bezeichner des technischen Profils. Auf das technische Profil kann mit diesem Bezeichner von anderen Elementen in der Richtliniendatei verwiesen werden. Beispiele: **OrchestrationSteps** und **ValidationTechnicalProfile**. |

**TechnicalProfile** enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Domäne | 0:1 | Der Domänenname für das technische Profil. Wenn das technische Profil z.B. als Identitätsanbieter Facebook angibt, lautet der Domänenname „Facebook.com“. |
| DisplayName | 0:1 | Der Name des technischen Profils, der Benutzern angezeigt werden kann. |
| BESCHREIBUNG | 0:1 | Die Beschreibung des technischen Profils, die Benutzern angezeigt werden kann. |
| Protocol | 0:1 | Das Protokoll, das für die Kommunikation mit der anderen Seite verwendet wird. |
| Metadaten | 0:1 | Eine Sammlung von Schlüssel-Wert-Paaren, die vom Protokoll im Verlauf einer Transaktion für die Kommunikation mit dem Endpunkt verwendet werden. |
| InputTokenFormat | 0:1 | Das Format des Eingabetokens. Mögliche Werte: `JSON`, `JWT`, `SAML11` oder `SAML2`. Der Wert `JWT` stellt ein JSON-Webtoken gemäß IETF-Spezifikation dar. Der Wert `SAML11` stellt ein SAML 1.1-Sicherheitstoken gemäß OASIS-Spezifikation dar.  Der Wert `SAML2` stellt ein SAML 2.0-Sicherheitstoken gemäß OASIS-Spezifikation dar. |
| OutputTokenFormat | 0:1 | Das Format des Ausgabetokens. Mögliche Werte: `JSON`, `JWT`, `SAML11` oder `SAML2`. |
| CryptographicKeys | 0:1 | Eine Liste kryptografischer Schlüssel, die im technischen Profil verwendet werden. |
| InputClaimsTransformations | 0:1 | Eine Liste zuvor definierter Verweise auf Anspruchstransformationen, die ausgeführt werden sollen, bevor Ansprüche an den Anspruchsanbieter oder die vertrauende Seite gesendet werden. |
| InputClaims | 0:1 | Eine Liste von zuvor definierten Verweisen auf Anspruchstypen, die als Eingabe für das technische Profil verwendet werden. |
| PersistedClaims | 0:1 | Eine Liste von zuvor definierten Verweisen auf Anspruchstypen, die vom Anspruchsanbieter für das technische Profil beibehalten werden. |
| OutputClaims | 0:1 | Eine Liste von zuvor definierten Verweisen auf Anspruchstypen, die als Ausgabe für das technische Profil verwendet werden. |
| OutputClaimsTransformations | 0:1 | Eine Liste zuvor definierter Verweise auf Anspruchstransformationen, die ausgeführt werden sollen, nachdem Ansprüche vom Anspruchsanbieter empfangen wurden. |
| ValidationTechnicalProfiles | 0:n | Eine Liste der Verweise auf andere technische Profile, die das technische Profil für die Überprüfung verwendet. Weitere Informationen finden Sie unter [Technisches Validierungsprofil](validation-technical-profile.md).|
| SubjectNamingInfo | 0:1 | Steuert die Erzeugung von Antragstellernamen in Token, wenn der Name des Antragstellers getrennt von den Ansprüchen angegeben wird. Beispiele: OAuth oder SAML.  |
| IncludeClaimsFromTechnicalProfile | 0:1 | Ein Bezeichner eines technischen Profils, von dem alle Eingabe- und Ausgabeansprüche diesem technischen Profil hinzugefügt werden sollen. Das referenzierte technische Profil muss in derselben Richtliniendatei definiert sein. |
| IncludeTechnicalProfile |0:1 | Ein Bezeichner eines technischen Profils, von dem alle Daten diesem technischen Profil hinzugefügt werden sollen. Das referenzierte technische Profil muss in derselben Richtliniendatei enthalten sein. |
| UseTechnicalProfileForSessionManagement | 0:1 | Ein anderes technische Profil, das für die Sitzungsverwaltung verwendet werden soll. |
|EnabledForUserJourneys| 0:1 |Steuert, ob das technische Profil in einer User Journey ausgeführt wird.  |

### <a name="protocol"></a>Protocol

Das **Protocol**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Name | Ja | Der Name eines gültigen Protokolls, das von Azure AD B2C unterstützt und als Teil des technischen Profils verwendet wird. Mögliche Werte: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `WsFed`, `WsTrust`, `Proprietary`, `session management`, `self-asserted` oder `None`. |
| Handler | Nein | Wenn der Name des Protokolls auf `Proprietary` festgelegt ist, geben Sie den vollqualifizierten Namen der Assembly an, die von Azure AD B2C zum Bestimmen des Protokollhandlers verwendet wird. |

### <a name="metadata"></a>Metadaten

Das **Metadata**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Item | 0:n | Die Metadaten zu dem technischen Profil. Jeder Typ von technischem Profil verfügt über einen anderen Satz von Metadatenelementen. Weitere Informationen finden Sie im Abschnitt zu den Typen technischer Profile. |

#### <a name="item"></a>Item

Das **Item**-Element des **Metadata**-Elements enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| Schlüssel | Ja | Der Metadatenschlüssel. Eine Liste der Metadatenelemente finden Sie unter den einzelnen Typen von technischen Profilen. |

### <a name="cryptographickeys"></a>CryptographicKeys

Das **CryptographicKeys**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| Schlüssel | 1:n | Ein kryptografischer Schlüssel, der in diesem technischen Profil verwendet wird. |

#### <a name="key"></a>Schlüssel

Das **Key**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| id | Nein | Ein eindeutiger Bezeichner eines bestimmten Schlüsselpaars, auf das von anderen Elementen in der Richtliniendatei verwiesen wird. |
| StorageReferenceId | Ja | Ein Bezeichner eines Speicherschlüsselcontainers, auf den von anderen Elementen in der Richtliniendatei verwiesen wird. |

### <a name="inputclaimstransformations"></a>InputClaimsTransformations

Das **InputClaimsTransformations**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | Der Bezeichner einer Anspruchstransformation, die ausgeführt werden soll, bevor Ansprüche an den Anspruchsanbieter oder die vertrauende Seite gesendet werden. Eine Anspruchstransformation kann verwendet werden, um vorhandene ClaimsSchema-Ansprüche zu ändern oder neue zu generieren. |

#### <a name="inputclaimstransformation"></a>InputClaimsTransformation

Das **InputClaimsTransformation**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner einer Anspruchstransformation, die bereits in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |

### <a name="inputclaims"></a>InputClaims

Das **InputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Ein erwarteter Eingabeanspruchstyp. |

#### <a name="inputclaim"></a>InputClaim

Das **InputClaim**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Der Bezeichner eines Anspruchstyps, der bereits im ClaimsSchema-Abschnitt der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |
| DefaultValue | Nein | Ein Standardwert, der verwendet wird, um einen Anspruch zu erstellen, wenn der von ClaimTypeReferenceId angegebene Anspruch nicht vorhanden ist, sodass der resultierende Anspruch vom technischen Profil als Eingabeanspruch verwendet werden kann. |
| PartnerClaimType | Nein | Der Bezeichner des Anspruchstyps des externen Partners, dem der angegebene Anspruchstyp der Richtlinie zugeordnet ist. Wenn das PartnerClaimType-Attribut nicht angegeben wurde, wird der angegebene Anspruchstyp der Richtlinie dem Partneranspruchstyp mit dem gleichen Namen zugeordnet. Verwenden Sie diese Eigenschaft, wenn sich der Name des Anspruchstyps vom dem der anderen Partei unterscheidet. Der erste Anspruchsname lautet beispielsweise „givenName“, während der Partner einen Anspruch mit dem Namen „first_name“ verwendet. |

### <a name="persistedclaims"></a>PersistedClaims

Das **PersistedClaims**-Element enthält die folgenden Elemente:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | Der Anspruchstyp, der beibehalten werden soll. |

#### <a name="persistedclaim"></a>PersistedClaim

Das **PersistedClaim**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Der Bezeichner eines Anspruchstyps, der bereits im ClaimsSchema-Abschnitt der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |
| DefaultValue | Nein | Ein Standardwert, der verwendet wird, um einen Anspruch zu erstellen, wenn der von ClaimTypeReferenceId angegebene Anspruch nicht vorhanden ist, sodass der resultierende Anspruch vom technischen Profil als Eingabeanspruch verwendet werden kann. |
| PartnerClaimType | Nein | Der Bezeichner des Anspruchstyps des externen Partners, dem der angegebene Anspruchstyp der Richtlinie zugeordnet ist. Wenn das PartnerClaimType-Attribut nicht angegeben wurde, wird der angegebene Anspruchstyp der Richtlinie dem Partneranspruchstyp mit dem gleichen Namen zugeordnet. Verwenden Sie diese Eigenschaft, wenn sich der Name des Anspruchstyps vom dem der anderen Partei unterscheidet. Der erste Anspruchsname lautet beispielsweise „givenName“, während der Partner einen Anspruch mit dem Namen „first_name“ verwendet. |

### <a name="outputclaims"></a>OutputClaims

Das **OutputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | Ein erwarteter Ausgabeanspruchstyp. |

#### <a name="outputclaim"></a>OutputClaim

Das **OutputClaim**-Element enthält die folgenden Attribute:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Ja | Der Bezeichner eines Anspruchstyps, der bereits im ClaimsSchema-Abschnitt der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |
| DefaultValue | Nein | Ein Standardwert, der verwendet wird, um einen Anspruch zu erstellen, wenn der von ClaimTypeReferenceId angegebene Anspruch nicht vorhanden ist, sodass der resultierende Anspruch vom technischen Profil als Eingabeanspruch verwendet werden kann. |
|AlwaysUseDefaultValue |Nein |Erzwingt die Verwendung des Standardwerts.  |
| PartnerClaimType | Nein | Der Bezeichner des Anspruchstyps des externen Partners, dem der angegebene Anspruchstyp der Richtlinie zugeordnet ist. Wenn das PartnerClaimType-Attribut nicht angegeben wurde, wird der angegebene Anspruchstyp der Richtlinie dem Partneranspruchstyp mit dem gleichen Namen zugeordnet. Verwenden Sie diese Eigenschaft, wenn sich der Name des Anspruchstyps vom dem der anderen Partei unterscheidet. Der erste Anspruchsname lautet beispielsweise „givenName“, während der Partner einen Anspruch mit dem Namen „first_name“ verwendet. |

### <a name="outputclaimstransformations"></a>OutputClaimsTransformations

Das **OutputClaimsTransformations**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | Die Bezeichner von Anspruchstransformationen, die ausgeführt werden sollen, bevor Ansprüche an den Anspruchsanbieter oder die vertrauende Seite gesendet werden. Eine Anspruchstransformation kann verwendet werden, um vorhandene ClaimsSchema-Ansprüche zu ändern oder neue zu generieren. |

#### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

Das **OutputClaimsTransformation**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner einer Anspruchstransformation, die bereits in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |

### <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Das **ValidationTechnicalProfiles**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | Die Bezeichner von technischen Profilen, mit denen einige oder alle Ausgabeansprüche des verweisenden technischen Profils überprüft werden. Alle Eingabeansprüche des technischen Profils, auf das verwiesen wird, müssen in den Ausgabeansprüchen des verweisenden technischen Profils enthalten sein. |

#### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

Das **ValidationTechnicalProfile**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner eines technischen Profils, das bereits in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |

###  <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo** enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClaimType | Ja | Ein Bezeichner eines Anspruchstyps, der bereits im ClaimsSchema-Abschnitt der Richtliniendatei definiert ist. |

### <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

Das **IncludeTechnicalProfile**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner eines technischen Profils, das bereits in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |

### <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

Das **UseTechnicalProfileForSessionManagement**-Element enthält das folgende Attribut:

| Attribut | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ReferenceId | Ja | Ein Bezeichner eines technischen Profils, das bereits in der Richtliniendatei oder der übergeordneten Richtliniendatei definiert ist. |

### <a name="enabledforuserjourneys"></a>EnabledForUserJourneys
Das **ClaimsProviderSelections**-Element in einer User Journey definiert die Liste der Auswahloptionen für Anspruchsanbieter und deren Reihenfolge. Mit dem **EnabledForUserJourneys**-Element filtern Sie, welcher Anspruchsanbieter für den Benutzer verfügbar ist. Das **EnabledForUserJourneys**-Element enthält einen der folgenden Werte:

- **Immer** – das technische Profil wird ausgeführt.
- **Nie** – das technische Profil wird übersprungen.
- **OnClaimsExistence** – die Ausführung erfolgt nur, wenn ein angegebener Anspruch im technischen Profil vorhanden ist.
- **OnItemExistenceInStringCollectionClaim** – die Ausführung erfolgt nur, wenn ein Element in einem Anspruch mit einer Zeichenfolgensammlung vorhanden ist.
- **OnItemAbsenceInStringCollectionClaim** – die Ausführung erfolgt nur, wenn ein Element in einem Anspruch mit einer Zeichenfolgensammlung nicht enthalten ist.

Bei Verwendung von **OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** oder **OnItemAbsenceInStringCollectionClaim** müssen die folgenden Metadaten angegeben werden: **ClaimTypeOnWhichToEnable** gibt den Anspruchstyp an, der ausgewertet werden soll, **ClaimValueOnWhichToEnable** gibt den Wert für den Vergleich an.

Das folgende technische Profil wird nur ausgeführt, wenn die **identityProviders**-Zeichenfolgensammlung den Wert `facebook.com` enthält:

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
