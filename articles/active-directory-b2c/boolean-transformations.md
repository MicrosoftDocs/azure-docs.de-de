---
title: Beispiele für die Transformation von booleschen Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier finden Sie Beispiele für die Transformation von booleschen Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a08849340d19055a03f85ca401757a81cd2c95d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511743"
---
# <a name="boolean-claims-transformations"></a>Transformationen von booleschen Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel werden Beispiele für die Verwendung von Transformationen von booleschen Ansprüchen für das Schema des Frameworks für die Identitätsfunktion in Azure Active Directory B2C veranschaulicht. Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="andclaims"></a>AndClaims

Führt einen And-Vorgang für zwei boolesche Eingabeansprüche aus und legt den Ausgabeanspruch mit dem Ergebnis des Vorgangs fest.

| Item  | TransformationClaimType  | Datentyp  | Notizen |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | Der erste auszuwertende Anspruchstyp |
| InputClaim | inputClaim2  | boolean | Der zweite auszuwertende Anspruchstyp |
|OutputClaim | outputClaim | boolean | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde (TRUE oder FALSE). |

Die folgende Anspruchstransformation veranschaulicht, wie Sie einen And-Vorgang für zwei boolesche Anspruchstypen (`isEmailNotExist` und `isSocialAccount`) durchführen. `true` wird für den Ausgabeanspruch `presentEmailSelfAsserted` festgelegt, wenn beide Eingabeansprüche den Wert `true` aufweisen. In einem Orchestrierungsschritt können Sie eine Vorbedingung nur verwenden, um eine Seite mit Selbstbestätigung im Voraus einzustellen, wenn eine E-Mail-Adresse eines Social Media-Kontos leer ist.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccount" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim1**: TRUE
    - **inputClaim2**: FALSE
- Ausgabeansprüche:
    - **outputClaim**: FALSE


## <a name="assertbooleanclaimisequaltovalue"></a>AssertBooleanClaimIsEqualToValue

Überprüft, ob die booleschen Werte von zwei Ansprüchen identisch sind und löst eine Ausnahme aus, wenn sie es nicht sind.

| Item | TransformationClaimType  | Datentyp  | Notizen |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | Der Anspruchstyp, der bestätigt werden soll. |
| InputParameter |valueToCompareTo | boolean | Der Wert, der verglichen werden soll (TRUE oder FALSE). |

Die Anspruchstransformation **AssertBooleanClaimIsEqualToValue** wird immer über ein [technisches Validierungsprofil](validation-technical-profile.md) ausgeführt, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) aufgerufen wird. Die Metadaten des selbstbestätigten technischen Profils **UserMessageIfClaimsTransformationBooleanValueIsNotEqual** steuern die Fehlermeldung, die das technische Profil dem Benutzer anzeigt.

![Ausführung von AssertStringClaimsAreEqual](./media/boolean-transformations/assert-execution.png)

Die folgende Anspruchstransformation veranschaulicht, wie Sie den Wert eines booleschen Anspruchstyps mit einem `true`-Wert überprüfen. Wenn der Wert des Anspruchstyps `accountEnabled` FALSE ist, wird eine Fehlermeldung ausgelöst.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```


Das technische Validierungsprofil `login-NonInteractive` ruft die Anspruchstransformation `AssertAccountEnabledIsTrue` auf.
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertAccountEnabledIsTrue" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Das selbstbestätigte technische Profil ruft das technische Validierungsprofil **login-NonInteractive** auf.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Custom error message if account is disabled.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim**: FALSE
    - **valueToCompareTo**: TRUE
- Ergebnis: Fehler wird ausgelöst.

## <a name="notclaims"></a>NotClaims

Führt einen Not-Vorgang für den booleschen Eingabeanspruch durch und legt den Ausgabeanspruch mit dem Ergebnis des Vorgangs fest.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | Der auszuführende Anspruch |
| OutputClaim | outputClaim | boolean | Die Anspruchstypen, die erstellt werden, nachdem die Anspruchstransformation aufgerufen wurde (TRUE oder FALSE). |

Verwenden Sie diese Anspruchstransformation, um die logische Negation für einen Anspruch auszuführen.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="NotClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim**: FALSE
- Ausgabeansprüche:
    - **outputClaim**: TRUE

## <a name="orclaims"></a>OrClaims 

Berechnet einen Or-Vorgang für zwei boolesche Eingabeansprüche und legt den Ausgabeanspruch mit dem Ergebnis des Vorgangs fest.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | Der erste auszuwertende Anspruchstyp |
| InputClaim | inputClaim2 | boolean | Der zweite auszuwertende Anspruchstyp |
| OutputClaim | outputClaim | boolean | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde (TRUE oder FALSE). |

Die folgende Anspruchstransformation veranschaulicht, wie Sie einen `Or`-Vorgang für zwei boolesche Anspruchstypen durchführen. Im Orchestrierungsschritt können Sie eine Vorbedingung verwenden, um eine Seite mit Selbstbestätigung im Voraus einzustellen, wenn einer der Ansprüche den Wert `true` aufweist.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedNotExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim1**: TRUE
    - **inputClaim2**: FALSE
- Ausgabeansprüche:
    - **outputClaim**: TRUE

