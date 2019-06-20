---
title: 'Azure Active Directory B2C: ClaimsTransformations | Microsoft-Dokumentation'
description: Definition des ClaimsTransformations-Elements im Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c904ac9d4c59a467dd8402ec44682c3cbd03fd8d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511532"
---
# <a name="claimstransformations"></a>ClaimsTransformations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Das **ClaimsTransformations**-Element enthält eine Liste von Funktionen für das Transformieren von Ansprüchen, die als Teil einer [benutzerdefinierten Richtlinie](active-directory-b2c-overview-custom.md) in User Journeys verwendet werden können. Eine Anspruchstransformation konvertiert einen Anspruch in einen anderen. In der Anspruchstransformation legen Sie die Transformationsmethode fest, z.B. das Hinzufügen eines Elements zu einer Zeichenfolgensammlung oder das Ändern der Groß-/Kleinschreibung einer Zeichenfolge.

Ein ClaimsTransformations-XML-Element muss im BuildingBlocks-Abschnitt der Richtlinie deklariert werden, damit die Liste von Anspruchstransformationsfunktionen enthalten wird, die in User Journeys verwendet werden können.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
    ...
  </ClaimsTransformation>
</ClaimsTransformations>
```

Das **ClaimsTransformation**-Element enthält die folgenden Attribute:

| Attribut |Erforderlich | BESCHREIBUNG |
| --------- |-------- | ----------- |
| id |Ja | Ein Bezeichner, der zur eindeutigen Identifizierung der Anspruchstransformation verwendet wird. Andere XML-Elemente in der Richtlinie verweisen auf den Bezeichner. |
| Transformationsmethode | Ja | Die Transformationsmethode, die für die Anspruchstransformation verwendet werden soll. Jede Anspruchstransformation verfügt über eigene Werte. Eine vollständige Liste der verfügbaren Werte finden Sie in der [Referenz zu Anspruchstransformationen](#claims-transformations-reference). |

## <a name="claimstransformation"></a>ClaimsTransformation

Das **ClaimsTransformation**-Element enthält die folgenden Elemente:

```xml
<ClaimsTransformation Id="<identifier>" TransformationMethod="<method>">
  <InputClaims>
    ...
  </InputClaims>
  <InputParameters>
    ...
  </InputParameters>                
  <OutputClaims>
    ...
  </OutputClaims>
</ClaimsTransformation>
```


| Element | Vorkommen | BESCHREIBUNG |
| ------- | -------- | ----------- |
| InputClaims | 0:1 | Eine Liste von **InputClaim**-Elementen, die Anspruchstypen angeben, die als Eingabe in die Anspruchstransformation eingefügt werden. Jedes dieser Elemente enthält einen Verweis auf ein ClaimType-Element, das bereits im ClaimsSchema-Abschnitt der Richtlinie definiert wurde. |
| InputParameters | 0:1 | Eine Liste von **InputParameter**-Elementen, die für die Anspruchstransformation als Eingabe bereitgestellt werden.  
| OutputClaims | 0:1 | Eine Liste von **OutputClaim**-Elementen, die Anspruchstypen angeben, die nach dem Aufruf des ClaimsTransformation-Elements erstellt werden. Jedes dieser Elemente enthält einen Verweis auf ein ClaimType-Element, das bereits im ClaimsSchema-Abschnitt definiert wurde. |

### <a name="inputclaims"></a>InputClaims

Das **InputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | Ein erwarteter Eingabeanspruchstyp. |

#### <a name="inputclaim"></a>InputClaim

Das **InputClaim**-Element enthält die folgenden Attribute:

| Attribut |Erforderlich | BESCHREIBUNG |
| --------- | ----------- | ----------- |
| ClaimTypeReferenceId |Ja | Ein Verweis auf ein ClaimType-Element, das bereits im ClaimsSchema-Abschnitt der Richtlinie definiert wurde. |
| TransformationClaimType |Ja | Ein Bezeichner zum Verweisen auf den Anspruchstransformationstyp. Jede Anspruchstransformation verfügt über eigene Werte. Eine vollständige Liste der verfügbaren Werte finden Sie in der [Referenz zu Anspruchstransformationen](#claims-transformations-reference). |

### <a name="inputparameters"></a>InputParameters

Das **InputParameters**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| InputParameter | 1:n | Ein erwarteter Eingabeparameter. |

#### <a name="inputparameter"></a>InputParameter

| Attribut | Erforderlich |BESCHREIBUNG |
| --------- | ----------- |----------- |
| id | Ja | Ein Bezeichner, der einen Verweis auf einen Parameter der Anspruchstransformationsmethode darstellt. Jede Anspruchstransformationsmethode verfügt über eigene Werte. Eine vollständige Liste der verfügbaren Werte finden Sie in der Tabelle für Anspruchstransformationen. |
| DataType | Ja | Der Datentyp des Parameters, z.B. „String“, „Boolean“, „Int“ oder „DateTime“, gemäß der DataType-Enumeration im XML-Schema der benutzerdefinierten Richtlinie. Dieser Typ wird dazu verwendet, arithmetische Operationen ordnungsgemäß auszuführen. Jede Anspruchstransformation verfügt über eigene Werte. Eine vollständige Liste der verfügbaren Werte finden Sie in der [Referenz zu Anspruchstransformationen](#claims-transformations-reference). |
| Wert | Ja | Ein Wert der wörtlich an die Transformation übergeben wird. Einige der Werte sind arbiträr, andere wählen Sie hingegen gemäß der Anspruchstransformationsmethode aus. |

### <a name="outputclaims"></a>OutputClaims

Das **OutputClaims**-Element enthält das folgende Element:

| Element | Vorkommen | BESCHREIBUNG |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | Ein erwarteter Ausgabeanspruchstyp. |

#### <a name="outputclaim"></a>OutputClaim 

Das **OutputClaim**-Element enthält die folgenden Attribute:

| Attribut |Erforderlich | BESCHREIBUNG |
| --------- | ----------- |----------- |
| ClaimTypeReferenceId | Ja | Ein Verweis auf ein ClaimType-Element, das bereits im ClaimsSchema-Abschnitt der Richtlinie definiert wurde.
| TransformationClaimType | Ja | Ein Bezeichner zum Verweisen auf den Anspruchstransformationstyp. Jede Anspruchstransformation verfügt über eigene Werte. Eine vollständige Liste der verfügbaren Werte finden Sie in der [Referenz zu Anspruchstransformationen](#claims-transformations-reference). |
 
Wenn die Typen des Eingabeanspruchs und des Ausgabeanspruchs übereinstimmen (String oder Boolean), können Sie den Eingabeanspruch auch als Ausgabeanspruch verwenden. In diesem Fall ändert die Anspruchstransformation den Eingabeanspruch mit dem Ausgabewert.

## <a name="example"></a>Beispiel

Sie können beispielsweise die neueste Version Ihrer Nutzungsbedingungen speichern, denen der Benutzer zugestimmt hat. Wenn Sie die Nutzungsbedingungen aktualisieren, können Sie den Benutzer dazu auffordern, der neuen Version zuzustimmen. Im folgenden Beispiel vergleicht die Anspruchstransformation **HasTOSVersionChanged** den Wert des Anspruchs **TOSVersion** mit dem Wert des Anspruchs **LastTOSAcceptedVersion**. Daraufhin wird der boolesche Anspruch **TOSVersionChanged** zurückgegeben.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="TOSVersionChanged">
      <DisplayName>Indicates if the TOS version accepted by the end user is equal to the current version</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="TOSVersion">
      <DisplayName>TOS version</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="LastTOSAcceptedVersion">
      <DisplayName>TOS version accepted by the end user</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <ClaimsTransformation Id="HasTOSVersionChanged" TransformationMethod="CompareClaims">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="TOSVersion" TransformationClaimType="inputClaim1" />
        <InputClaim ClaimTypeReferenceId="LastTOSAcceptedVersion" TransformationClaimType="inputClaim2" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="TOSVersionChanged" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

## <a name="claims-transformations-reference"></a>Referenzen zu Transformationen von Ansprüchen

In den folgenden Referenzseiten finden Sie weitere Beispiele für Anspruchstransformationen:

- [Boolescher Wert](boolean-transformations.md)
- [Date](date-transformations.md)
- [Integer](integer-transformations.md)
- [JSON](json-transformations.md)
- [Allgemein](general-transformations.md)
- [Social Media-Konto](social-transformations.md)
- [String](string-transformations.md)
- [StringCollection](stringcollection-transformations.md)

