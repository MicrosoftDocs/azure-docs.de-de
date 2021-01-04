---
title: Beispiele für die Transformation von Social Media-Kontoansprüchen für benutzerdefinierte Richtlinien
titleSuffix: Azure AD B2C
description: Hier finden Sie Beispiele für die Transformation von Social Media-Kontoansprüchen für das Schema des Frameworks für die Identitätsfunktion (Identity Experience Framework, IEF) von Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c60cecb9d6bfeeefc0569a1a57185d13f0c6442f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953116"
---
# <a name="social-accounts-claims-transformations"></a>Anspruchstransformationen für Social Media-Konten

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In Azure Active Directory B2C (Azure AD B2C) werden Identitäten von Social Media-Konten in einem `userIdentities`-Attribut eines Anspruchstyps **alternativeSecurityIdCollection** gespeichert. Jedes Element in der **alternativeSecurityIdCollection** gibt den Aussteller (Name des Identitätsanbieters, z.B. „facebook.com“), und `issuerUserId` an, eine eindeutige Benutzer-ID für den Aussteller.

```json
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

Dieser Artikel enthält Beispiele für die Verwendung von Anspruchstransformationen für Social Media-Konten des Schemas des Frameworks für die Identitätsfunktion von Azure AD B2C. Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Erstellt eine JSON-Darstellung der alternativeSecurityId-Eigenschaft des Benutzers, die in Aufrufen an Azure Active Directory verwendet werden kann. Weitere Informationen finden Sie im [AlternativeSecurityId-Schema](/graph/api/resources/alternativesecurityid).

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Schlüssel | string | Der Anspruchstyp, der die eindeutige Benutzer-ID, die vom Identitätsanbieter eines sozialen Netzwerks verwendet wird, angibt. |
| InputClaim | identityProvider | string | Der Anspruchstyp, der den Identitätsanbieter eines Social Media-Kontos, z.B. „facebook.com“, angibt. |
| OutputClaim | alternativeSecurityId | string | Der Anspruchstyp, der erstellt wird, nachdem die Anspruchstransformation aufgerufen wurde. Enthält Informationen zur Identität eines Benutzers mit einem Social Media-Konto. **issuer** ist der Wert des `identityProvider`-Anspruchs. **issuerUserId** ist der Wert des `key`-Anspruchs im Base64-Format. |

Verwenden Sie diese Anspruchstransformation zum Generieren des Anspruchstyps `alternativeSecurityId`. Sie wird von allen technischen Profilen für Social Media-Identitätsanbieter wie z.B. `Facebook-OAUTH` verwendet. Die folgende Anspruchstransformation empfängt die ID des Social Media-Kontos des Benutzers und den Namen des Identitätsanbieters. Die Ausgabe dieses technischen Profils ist eine Zeichenfolge im JSON-Format, die in Azure AD-Verzeichnisdiensten verwendet werden kann.

```xml
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **key:** 12334
    - **identityProvider:** Facebook.com
- Ausgabeansprüche:
    - **alternativeSecurityId:** { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

Fügt eine `AlternativeSecurityId` an einen `alternativeSecurityIdCollection`-Anspruch an.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | item | string | Der Anspruchstyp, der dem Ausgabeanspruch hinzugefügt werden soll. |
| InputClaim | collection | alternativeSecurityIdCollection | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, sofern in der Richtlinie vorhanden. Bei einer Angabe fügt die Anspruchstransformation das `item` am Ende der Sammlung hinzu. |
| OutputClaim | collection | alternativeSecurityIdCollection | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Die neue Sammlung, die die Elemente aus der Eingabe-`collection` und das `item` enthält. |

Das folgende Beispiel verknüpft eine neue Social Media-Identität mit einem vorhandenen Konto. So verknüpfen Sie eine neue Social Media-Identität
1. Geben Sie in den technischen Profilen **AAD-UserReadUsingAlternativeSecurityId** und **AAD-UserReadUsingObjectId** den **alternativeSecurityIds**-Anspruch des Benutzers aus.
1. Bitten Sie den Benutzer, sich mit einem der Identitätsanbieter, die nicht mit diesem Benutzer verknüpft sind, anzumelden.
1. Erstellen Sie mithilfe der Anspruchstransformation **CreateAlternativeSecurityId** einen neuen **alternativeSecurityId**-Anspruchstyp mit dem Namen `AlternativeSecurityId2`.
1. Rufen Sie die Anspruchstransformation **AddItemToAlternativeSecurityIdCollection** auf, um den **AlternativeSecurityId2**-Anspruch dem vorhandenen **AlternativeSecurityIds**-Anspruch hinzuzufügen.
1. Behalten Sie den **alternativeSecurityIds**-Anspruch im Benutzerkonto bei.

```xml
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **item:** { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection:** [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- Ausgabeansprüche:
    - **collection:** [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

Gibt eine Liste mit den Ausstellern aus dem **alternativeSecurityIdCollection**-Anspruch in einem neuen **stringCollection**-Anspruch zurück.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | Der Anspruchstyp, der zum Abrufen der Liste der Identitätsanbieter (Aussteller) verwendet werden soll. |
| OutputClaim | identityProvidersCollection | stringCollection | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Liste der Identitätsanbieter, die dem Eingabeanspruch alternativeSecurityIdCollection zugeordnet sind |

Die folgende Anspruchstransformation liest den **alternativeSecurityIds**-Anspruch des Benutzers und extrahiert die Liste der Identitätsanbieternamen, die diesem Konto zugeordnet sind. Verwenden Sie die ausgegebene **identityProvidersCollection**, um dem Benutzer die Liste der Identitätsanbieter, die dem Konto zugeordnet sind, anzuzeigen. Oder filtern Sie auf der Auswahlseite für Identitätsanbieter die Liste der Identitätsanbieter basierend auf dem ausgegebenen **identityProvidersCollection**-Anspruch. Dann kann der Benutzer eine neue Social Media-Identität auswählen, die noch nicht mit dem Konto verknüpft ist.

```xml
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **alternativeSecurityIdCollection:** [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Ausgabeansprüche:
    - **identityProvidersCollection:** [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

Entfernt eine **AlternativeSecurityId** aus einem **alternativeSecurityIdCollection**-Anspruch.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | string | Der Anspruchstyp mit dem Namen des Identitätsanbieters, der aus der Sammlung entfernt werden soll. |
| InputClaim | collection | alternativeSecurityIdCollection | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden. Die Anspruchstransformation entfernt den identityProvider aus der Sammlung. |
| OutputClaim | collection | alternativeSecurityIdCollection | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Die neue Sammlung, nachdem der identityProvider aus der Sammlung entfernt wurde. |

Das folgende Beispiel hebt die Verknüpfung der Social Media-Identität mit einem vorhandenen Konto auf. So heben Sie die Verknüpfung einer Social Media-Identität auf
1. Geben Sie in den technischen Profilen **AAD-UserReadUsingAlternativeSecurityId** und **AAD-UserReadUsingObjectId** den **alternativeSecurityIds**-Anspruch des Benutzers aus.
2. Bitten Sie den Benutzer, das Social Media-Konto auszuwählen, das aus der Liste der diesem Benutzer zugeordneten Identitätsanbietern entfernt werden soll.
3. Rufen Sie ein technisches Profil für eine Anspruchstransformation auf, das die Anspruchstransformation **RemoveAlternativeSecurityIdByIdentityProvider** aufruft, die die ausgewählte Social Media-Identität anhand des Identitätsanbieternamens entfernt.
4. Behalten Sie den **alternativeSecurityIds**-Anspruch im Benutzerkonto bei.

```xml
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **identityProvider:** facebook.com
    - **collection:** [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- Ausgabeansprüche:
    - **collection:** [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]