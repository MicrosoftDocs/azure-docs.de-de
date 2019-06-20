---
title: Beispiele für die Transformation von Integer-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier finden Sie Beispiele für die Transformation von Integer-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 976cf55dd8f1c11d1ea605b25086fa644afec980
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510802"
---
# <a name="integer-claims-transformations"></a>Transformationen von Integer-Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dieser Artikel enthält Beispiele für die Verwendung von Integer-Anspruchstransformationen des Schemas des Frameworks für die Identitätsfunktion von Azure Active Directory (Azure AD) B2C. Weitere Informationen finden Sie unter [Anspruchstransformationen](claimstransformations.md).

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim 

Konvertiert einen long-Datentyp in einen string-Datentyp.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | lang | Der Anspruchstyp, der in eine Zeichenfolge konvertiert werden soll. |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

In diesem Beispiel wird der Anspruch `numericUserId` mit dem Werttyp long in einen `UserId`-Anspruch mit dem Werttyp string konvertiert.

```XML
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim:** 12334 (long)
- Ausgabeansprüche: 
    - **outputClaim:** 12334 (string)

