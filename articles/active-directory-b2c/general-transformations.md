---
title: Beispiele für die Transformation allgemeiner Ansprüche für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier finden Sie Beispiele für die Transformation allgemeiner Ansprüche für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a5f8068ea7e97343749c719d2d0800e20701079c
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510986"
---
# <a name="general-claims-transformations"></a>Transformationen allgemeiner Ansprüche

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel werden Beispiele für die Verwendung von Transformationen allgemeiner Ansprüche für das Schema des Frameworks für die Identitätsfunktion in Azure Active Directory B2C veranschaulicht. Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="doesclaimexist"></a>DoesClaimExist

Überprüft, ob **inputClaim** vorhanden ist oder nicht, und legt **outputClaim** auf TRUE bzw. FALSE fest.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Beliebig | Der Eingabeanspruch, dessen Vorhandensein überprüft werden muss. |
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch vorhanden ist oder einen beliebigen Wert enthält. Der Rückgabewert ist ein boolescher Wert, der angibt, ob der Anspruch vorhanden ist. Im folgenden Beispiel wird geprüft, ob die E-Mail-Adresse vorhanden ist.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>                    
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputClaim**: someone@contoso.com
- Ausgabeansprüche: 
    - **outputClaim**: true

## <a name="hash"></a>Hash

Hash des angegebenen Nur-Texts, der Salt und einen geheimen Schlüssel verwendet.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | Zeichenfolge | Der zu verschlüsselnde Eingabeanspruch |
| InputClaim | Salt | Zeichenfolge | Der Salt-Parameter. Sie können mit der `CreateRandomString`-Anspruchstransformation einen Zufallswert erstellen. |
| InputParameter | randomizerSecret | Zeichenfolge | Verweist auf einen vorhandenen **Richtlinienschlüssel** von Azure AD B2C. Neuen Export erstellen: Wählen Sie in Ihrem Azure AD B2C-Mandanten **B2C-Einstellungen > Identity Experience Framework** aus. Wählen Sie **Richtlinienschlüssel** aus, um die in Ihrem Mandanten verfügbaren Schlüssel anzuzeigen. Wählen Sie **Hinzufügen**. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus. Geben Sie einen Namen an (das Präfix „B2C_1A_“ wird möglicherweise automatisch hinzugefügt). Geben Sie in das Feld „Geheimer Schlüssel“ einen geheimen Schlüssel ein, der verwendet werden soll, wie z.B. 1234567890. Wählen Sie für „Schlüsselverwendung“ die Option **Geheimer Schlüssel** aus. Klicken Sie auf **Erstellen**. |
| OutputClaim | hash | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. Der im Eingabeanspruch `plaintext` konfigurierte Anspruch. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **plaintext**: MyPass@word1
    - **salt**: 487624568
    - **randomizerSecret**: B2C_1A_AccountTransformSecret
- Ausgabeansprüche: 
    - **outputClaim:** CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=



