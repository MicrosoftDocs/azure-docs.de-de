---
title: Beispiele für Transformation von Zeichenfolgenansprüchen für benutzerdefinierte Richtlinien
titleSuffix: Azure AD B2C
description: Beispiele für die Transformation von Zeichenfolgenansprüchen für das Identity Experience Framework (IEF) von Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/03/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4e74c33a18baff3e1cb39328ce265f16975ef1b5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994841"
---
# <a name="string-claims-transformations"></a>Transformationen von Zeichenfolgen-Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dieser Artikel enthält Beispiele für die Verwendung von Transformationen von Zeichenfolgenansprüchen des Identity Experience Framework-Schemas in Azure Active Directory B2C (Azure AD B2C). Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual

Zwei Ansprüche werden miteinander verglichen, und es wird eine Ausnahme ausgelöst, wenn diese gemäß dem angegebenen Vergleich inputClaim1, inputClaim2 und stringComparison nicht gleich sind.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Zeichenfolge | Der Typ des ersten Anspruchs, der verglichen werden soll. |
| InputClaim | inputClaim2 | Zeichenfolge | Der Typ des zweiten Anspruchs, der verglichen werden soll. |
| InputParameter | stringComparison | Zeichenfolge | Zeichenfolgenvergleich, einer der Werte: Ordinal, OrdinalIgnoreCase. |

Die Anspruchstransformation **AssertStringClaimsAreEqual** wird immer über ein [technisches Validierungsprofil](validation-technical-profile.md) ausgeführt, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) aufgerufen wird (oder von einem Element vom Typ [DisplayControl](display-controls.md)). Die `UserMessageIfClaimsTransformationStringsAreNotEqual`-Metadaten eines selbstbestätigten technischen Profils steuern die Fehlermeldung, die dem Benutzer angezeigt wird. Die Fehlermeldungen können [lokalisiert](localization-string-ids.md#claims-transformations-error-messages) werden.


![Ausführung von AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Mithilfe dieser Anspruchstransformation können Sie sicherstellen, dass zwei Anspruchstypen den gleichen Wert aufweisen. Ist dies nicht der Fall, wird eine Fehlermeldung ausgelöst. Im folgenden Beispiel wird überprüft, ob der Anspruchstyp **StrongAuthenticationEmailAddress** gleich dem Anspruchstyp **email** ist. Andernfalls wird eine Fehlermeldung ausgelöst.

```xml
<ClaimsTransformation Id="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="strongAuthenticationEmailAddress" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
  </InputParameters>
</ClaimsTransformation>
```


Das technische Validierungsprofil **login-NonInteractive** ruft die Anspruchstransformation **AssertEmailAndStrongAuthenticationEmailAddressAreEqual** auf.
```xml
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Das selbstbestätigte technische Profil ruft das technische Validierungsprofil **login-NonInteractive** auf.

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">Custom error message the email addresses you provided are not the same.</Item>
  </Metadata>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Eingabeparameter:
  - **stringComparison**:  ordinalIgnoreCase
- Ergebnis: Fehler wird ausgelöst.

## <a name="changecase"></a>ChangeCase

Ändert die Groß-/Kleinschreibung des bereitgestellten Anspruchs je nach Operator in Groß- oder Kleinbuchstaben.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Zeichenfolge | Der Anspruchstyp, der geändert werden soll. |
| InputParameter | toCase | Zeichenfolge | Einer der folgenden Werte: `LOWER` oder `UPPER`. |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieses Anspruchstyps können Sie jeden Zeichenfolgen-Anspruchstyp in Groß- oder Kleinbuchstaben ändern.

```xml
<ClaimsTransformation Id="ChangeToLower" TransformationMethod="ChangeCase">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="toCase" DataType="string" Value="LOWER" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **email**: SomeOne@contoso.com
- Eingabeparameter:
    - **toCase:** LOWER
- Ausgabeansprüche:
  - **email**: someone@contoso.com

## <a name="createstringclaim"></a>CreateStringClaim

Erstellt einen Zeichenfolgenanspruch aus dem angegebenen Eingabeparameter in der Transformation.

| Element | TransformationClaimType | Datentyp | Notizen |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | Zeichenfolge | Die festzulegende Zeichenfolge. Dieser Eingabeparameter unterstützt [Transformationsausdrücke für Zeichenfolgenansprüche](string-transformations.md#string-claim-transformations-expressions). |
| OutputClaim | createdClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation ausgelöst wurde. Es handelt sich um den Wert, der im Eingabeparameter angegeben ist. |

Mithilfe dieser Anspruchstransformation können Sie den Wert für den Zeichenfolgen-Anspruchstyp festlegen.

```xml
<ClaimsTransformation Id="CreateTermsOfService" TransformationMethod="CreateStringClaim">
  <InputParameters>
    <InputParameter Id="value" DataType="string" Value="Contoso terms of service..." />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="TOS" TransformationClaimType="createdClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeparameter:
    - **value:** Contoso-AGB ...
- Ausgabeansprüche:
    - **createdClaim:** Der Anspruchstyp für Vertragsbedingungen enthält den Wert „Contoso-AGB ...“.

## <a name="compareclaims"></a>CompareClaims

Bestimmt, ob ein Zeichenfolgen-Anspruch einem anderen entspricht. Das Ergebnis ist ein neuer boolescher Anspruchstyp mit dem Wert `true` oder `false`.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Zeichenfolge | Der erste Anspruchstyp, der verglichen werden soll. |
| InputClaim | inputClaim2 | Zeichenfolge | Der zweite Anspruchstyp, der verglichen werden soll. |
| InputParameter | Operator | Zeichenfolge | Mögliche Werte: `EQUAL` oder `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den Zeichenfolgen, die miteinander verglichen werden, ignoriert werden soll. |
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch gleich einem anderen Anspruch ist. Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **email** gleich dem Wert des Anspruchs **Verified.Email** ist.

```xml
<ClaimsTransformation Id="CheckEmail" TransformationMethod="CompareClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="Email" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="Verified.Email" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="operator" DataType="string" Value="NOT EQUAL" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="SameEmailAddress" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputClaim1**: someone@contoso.com
  - **inputClaim2**: someone@outlook.com
- Eingabeparameter:
    - **operator:**  NOT EQUAL
    - **ignoreCase**: true
- Ausgabeansprüche:
    - **outputClaim**: true

## <a name="compareclaimtovalue"></a>CompareClaimToValue

Bestimmt, ob der Wert eines Anspruchs gleich dem Wert des Eingabeparameters ist.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | Operator | Zeichenfolge | Mögliche Werte: `EQUAL` oder `NOT EQUAL`. |
| InputParameter | compareTo | Zeichenfolge | Zeichenfolgenvergleich, einer der Werte: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den Zeichenfolgen, die miteinander verglichen werden, ignoriert werden soll. |
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch gleich einem von Ihnen angegebenen Wert ist. Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **termsOfUseConsentVersion** gleich `v1` ist.

```xml
<ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="compareTo" DataType="string" Value="V1" />
    <InputParameter Id="operator" DataType="string" Value="not equal" />
    <InputParameter Id="ignoreCase" DataType="string" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel
- Eingabeansprüche:
    - **inputClaim1**: v1
- Eingabeparameter:
    - **compareTo:** V1
    - **operator:** EQUAL
    - **ignoreCase**:  true
- Ausgabeansprüche:
    - **outputClaim**: true

## <a name="createrandomstring"></a>CreateRandomString

Erstellt mithilfe des Zufallszahlengenerators eine zufällige Zeichenfolge. Wenn der Zufallszahlengenerator vom Typ `integer` ist, können optional ein Seedparameter und die maximale Anzahl angegeben werden. Mit einem optionalen Parameter im Zeichenfolgenformat kann die Ausgabe formatiert werden, und ein optionaler Parameter mit base64-Codierung gibt an, ob die Ausgabe base64-codiert ist und wie folgt lautet: randomGeneratorType [guid, integer] outputClaim (String).

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | Zeichenfolge | Gibt den zufälligen Wert an, der generiert werden soll: `GUID` (globale eindeutige ID) oder `INTEGER` (eine Ziffer). |
| InputParameter | stringFormat | Zeichenfolge | [Optional] Formatiert den Zufallswert. |
| InputParameter | base64 | boolean | [Optional] Konvertiert den Zufallswert in base64. Wenn das Zeichenfolgenformat angewendet wird, wird der Wert nach dem Zeichenfolgenformat mit base64 codiert. |
| InputParameter | maximumNumber | INT | [Optional] Nur für `INTEGER` randomGeneratorType. Gibt die maximale Anzahl an. |
| InputParameter | seed  | INT | [Optional] Nur für `INTEGER` randomGeneratorType. Gibt den Seed für den Zufallswert an. Hinweis: Der gleiche Seed ergibt die gleiche Sequenz von Zufallszahlen. |
| OutputClaim | outputClaim | Zeichenfolge | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Der Zufallswert. |

Im folgenden Beispiel wird eine globale eindeutige ID generiert. Mithilfe dieser Anspruchstransformation wird der zufällige UPN (User Principal Name, Benutzerprinzipalname) erstellt.

```xml
<ClaimsTransformation Id="CreateRandomUPNUserName" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="GUID" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Beispiel

- Eingabeparameter:
    - **randomGeneratorType:** GUID
- Ausgabeansprüche:
    - **outputClaim**: bc8bedd2-aaa3-411e-bdee-2f1810b73dfc

Im folgenden Beispiel wird ein ganzzahliger Zufallswert zwischen 0 und 1.000 generiert. Der Wert ist wie folgt formatiert: OTP_{random value}.

```xml
<ClaimsTransformation Id="SetRandomNumber" TransformationMethod="CreateRandomString">
  <InputParameters>
    <InputParameter Id="randomGeneratorType" DataType="string" Value="INTEGER" />
    <InputParameter Id="maximumNumber" DataType="int" Value="1000" />
    <InputParameter Id="stringFormat" DataType="string" Value="OTP_{0}" />
    <InputParameter Id="base64" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="randomNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeparameter:
    - **randomGeneratorType:** INTEGER
    - **maximumNumber:** 1000
    - **stringFormat:** OTP_{0}
    - **base64**: false
- Ausgabeansprüche:
    - **outputClaim:** OTP_853


## <a name="formatstringclaim"></a>FormatStringClaim

Formatiert einen Anspruch anhand der angegebenen Formatzeichenfolge. Bei dieser Transformation wird die C#-Methode `String.Format` verwendet.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Zeichenfolge |Der Anspruchstyp, der als Parameter {0} im Zeichenfolgenformat fungiert. |
| InputParameter | stringFormat | Zeichenfolge | Das Zeichenfolgenformat, einschließlich des Parameters {0}. Dieser Eingabeparameter unterstützt [Transformationsausdrücke für Zeichenfolgenansprüche](string-transformations.md#string-claim-transformations-expressions).  |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie eine beliebige Zeichenfolge mit dem Parameter {0} formatieren. Im folgenden Beispiel wird ein **userPrincipalName** erstellt. Sämtliche technische Profile sozialer Identitätsanbieter, wie z.B. `Facebook-OAUTH`, rufen den **CreateUserPrincipalName** zum Generieren eines **userPrincipalName** auf.

```xml
<ClaimsTransformation Id="CreateUserPrincipalName" TransformationMethod="FormatStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="upnUserName" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="cpim_{0}@{RelyingPartyTenantId}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim:** 5164db16-3eee-4629-bfda-dcc3326790e9
- Eingabeparameter:
    - **stringFormat**:  cpim_{0}@{RelyingPartyTenantId}
- Ausgabeansprüche:
  - **outputClaim**: cpim_5164db16-3eee-4629-bfda-dcc3326790e9@b2cdemo.onmicrosoft.com

## <a name="formatstringmultipleclaims"></a>FormatStringMultipleClaims

Formatiert zwei Ansprüche anhand der angegebenen Formatzeichenfolge. Bei dieser Transformation wird die C#-Methode `String.Format` verwendet.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Zeichenfolge | Der Anspruchstyp, der als Parameter {0} im Zeichenfolgenformat fungiert. |
| InputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der als Parameter {1} im Zeichenfolgenformat fungiert. |
| InputParameter | stringFormat | Zeichenfolge | Das Zeichenfolgenformat, einschließlich der Parameter {0} und {1}. Dieser Eingabeparameter unterstützt [Transformationsausdrücke für Zeichenfolgenansprüche](string-transformations.md#string-claim-transformations-expressions).   |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie eine beliebige Zeichenfolge mit zwei Parametern, {0} und {1}, formatieren. Im folgenden Beispiel wird ein **displayName** mit dem angegebenen Format erstellt:

```xml
<ClaimsTransformation Id="CreateDisplayNameFromFirstNameAndLastName" TransformationMethod="FormatStringMultipleClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="surName" TransformationClaimType="inputClaim2" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="stringFormat" DataType="string" Value="{0} {1}" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="displayName" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim1:** Joe
    - **inputClaim2:** Fernando
- Eingabeparameter:
    - **stringFormat**: {0} {1}
- Ausgabeansprüche:
    - **outputClaim:** Joe Fernando

## <a name="getlocalizedstringstransformation"></a>GetLocalizedStringsTransformation

Kopiert lokalisierte Zeichenfolgen in Ansprüche.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | Der Name der lokalisierten Zeichenfolge. | Zeichenfolge | Die Liste der Anspruchstypen, die nach dem Aufrufen dieser Anspruchstransformation erstellt werden. |

Verwenden Sie die Anspruchstransformation „GetLocalizedStringsTransformation“ wie folgt:

1. Definieren Sie eine [Lokalisierungszeichenfolge](localization.md), und ordnen Sie sie einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) zu.
1. Der `ElementType` des `LocalizedString`-Elements muss auf `GetLocalizedStringsTransformationClaimType` festgelegt sein.
1. `StringId` ist ein eindeutiger Bezeichner, den Sie definieren und später in Ihrer Anspruchstransformation nutzen.
1. Geben Sie in der Anspruchstransformation die Liste mit den Ansprüchen an, die für die lokalisierte Zeichenfolge festgelegt werden sollen. `ClaimTypeReferenceId` ist ein Verweis auf ein „ClaimType“-Element, das bereits im Abschnitt „ClaimsSchema“ der Richtlinie definiert wurde. `TransformationClaimType` ist der Name der lokalisierten Zeichenfolge gemäß der Definition in der `StringId` des `LocalizedString`-Elements.
1. Verweisen Sie in einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) oder einer Eingabe- oder Ausgabeanspruchstransformation des [Anzeigesteuerelements](display-controls.md) auf Ihre Anspruchstransformation.

![GetLocalizedStringsTransformation](./media/string-transformations/get-localized-strings-transformation.png)

Im folgenden Beispiel werden der E-Mail-Betreff, der Text, Ihre Codemeldung und die Signatur der E-Mail für lokalisierte Zeichenfolgen nachgeschlagen. Diese Ansprüche werden später von der benutzerdefinierten Vorlage für die E-Mail-Überprüfung verwendet.

Definieren Sie lokalisierte Zeichenfolgen für Englisch (Standardeinstellung) und Spanisch.

```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
   </SupportedLanguages>

  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for verifying your account!</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
     </LocalizedStrings>
   </LocalizedResources>
   <LocalizedResources Id="api.localaccountsignup.es">
     <LocalizedStrings>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
      <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Atentamente</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

Die Anspruchstransformation legt den Wert des Anspruchstyps *subject* mit dem Wert von `StringId` *email_subject* fest.

```xml
<ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
    <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
    <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
    <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
   </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Ausgabeansprüche:
  - **subject**: E-Mail-Prüfcode für Contoso-Konto
  - **message**: Vielen Dank für die Bestätigung Ihres Kontos!
  - **codeIntro**: Ihr Code lautet:
  - **signature**: Mit freundlichen Grüßen


## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Suche nach einem Element aus der Sammlung **Restriction** eines Anspruchs.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | Zeichenfolge | Der Anspruch mit dem Text, nach dem in den **restrictionValueClaim**-Ansprüchen über die Sammlung **Restriction** gesucht werden soll.  |
| OutputClaim | restrictionValueClaim | Zeichenfolge | Der Anspruch mit der Sammlung **Restriction**. Nach dem Aufrufen der Anspruchstransformation enthält der Wert dieses Anspruchs den Wert des ausgewählten Elements. |

Im folgenden Beispiel wird anhand des Schlüssels des Fehlers nach der Beschreibung der Fehlermeldung gesucht. Der Anspruch **responseMsg** enthält eine Sammlung von Fehlermeldungen, die dem Benutzer angezeigt oder der vertrauenden Seite gesendet werden sollen.

```xml
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cannot sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Bei der Anspruchstransformation wird nach dem Text des Elements gesucht und der zugehörige Wert zurückgegeben. Wenn die Einschränkung mithilfe von `<LocalizedCollection>` lokalisiert wird, wird der lokalisierte Wert von der Anspruchstransformation zurückgegeben.

```xml
<ClaimsTransformation Id="GetResponseMsgMappedToResponseCode" TransformationMethod="GetMappedValueFromLocalizedCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseCode" TransformationClaimType="mapFromClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" TransformationClaimType="restrictionValueClaim" />        
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **mapFromClaim:** B2C_V1_90001
- Ausgabeansprüche:
    - **restrictionValueClaim:** Sie können sich nicht anmelden, weil Sie nicht volljährig sind.

## <a name="lookupvalue"></a>LookupValue

Sucht in einer Liste von Werten basierend auf dem Wert eines anderen Anspruchs nach einem Anspruchswert.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | Zeichenfolge | Der Anspruch, der den Suchwert enthält |
| InputParameter | |Zeichenfolge | Die Sammlung von inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Steuert, ob bei keinem übereinstimmenden Suchergebnis ein Fehler zurückgegeben wird. |
| OutputClaim | inputParameterId | Zeichenfolge | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Der Wert der übereinstimmenden `Id`. |

Im folgenden Beispiel wird in einer der Sammlungen von inputParameter nach dem Domänennamen gesucht. Bei der Anspruchstransformation wird in dem Bezeichner nach dem Domänennamen gesucht und der zugehörige Wert (eine Anwendungs-ID) zurückgegeben.

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="false" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputParameterId**: test.com
- Eingabeparameter:
    - **contoso.com:** 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com:** 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: false
- Ausgabeansprüche:
    - **outputClaim**: c7026f88-4299-4cdb-965d-3f166464b8a9

Wenn der Eingabeparameter `errorOnFailedLookup` auf `true` festgelegt ist, wird die Anspruchstransformation **LookupValue** immer über ein [technisches Validierungsprofil](validation-technical-profile.md) ausgeführt, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) aufgerufen wird (oder von einem Element vom Typ [DisplayControl](display-controls.md)). Die `LookupNotFound`-Metadaten eines selbstbestätigten technischen Profils steuern die Fehlermeldung, die dem Benutzer angezeigt wird.

![Ausführung von AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Im folgenden Beispiel wird in einer der Sammlungen von inputParameter nach dem Domänennamen gesucht. Bei der Anspruchstransformation wird in dem Bezeichner nach dem Domänennamen gesucht und der zugehörige Wert (eine Anwendungs-ID) oder eine Fehlermeldung zurückgegeben.

```xml
 <ClaimsTransformation Id="DomainToClientId" TransformationMethod="LookupValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="inputParameterId" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="contoso.com" DataType="string" Value="13c15f79-8fb1-4e29-a6c9-be0d36ff19f1" />
    <InputParameter Id="microsoft.com" DataType="string" Value="0213308f-17cb-4398-b97e-01da7bd4804e" />
    <InputParameter Id="test.com" DataType="string" Value="c7026f88-4299-4cdb-965d-3f166464b8a9" />
    <InputParameter Id="errorOnFailedLookup" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainAppId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputParameterId**: live.com
- Eingabeparameter:
    - **contoso.com:** 13c15f79-8fb1-4e29-a6c9-be0d36ff19f1
    - **microsoft.com:** 0213308f-17cb-4398-b97e-01da7bd4804e
    - **test.com**: c7026f88-4299-4cdb-965d-3f166464b8a9
    - **errorOnFailedLookup**: true
- Error:
    - In der Liste der Eingabeparameter-IDs wurde keine Entsprechung für den Eingabeanspruchswert gefunden, und „errorOnFailedLookup“ ist „true“.


## <a name="nullclaim"></a>NullClaim

Bereinigt den Wert eines angegebenen Anspruchs.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | Zeichenfolge | Der Wert des Anspruchs ist auf NULL festgelegt. |

Mithilfe dieser Anspruchstransformation können Sie unnötige Daten aus der Eigenschaftensammlung eines Anspruchs entfernen, sodass das Sitzungscookie kleiner wird. Im folgenden Beispiel wird der Wert des Anspruchstyps `TermsOfService` entfernt.

```xml
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **outputClaim:** Willkommen bei der Contoso-App. Wenn Sie diese Website weiterhin aufrufen und nutzen, erklären Sie sich mit den folgenden Bedingungen einverstanden...
- Ausgabeansprüche:
    - **outputClaim:** NULL

## <a name="parsedomain"></a>ParseDomain

Ruft den Domänenteil einer E-Mail-Adresse ab.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | Zeichenfolge | Der Anspruchstyp, der die E-Mail-Adresse enthält. |
| OutputClaim | Domäne | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde (die Domäne). |

Mithilfe dieser Anspruchstransformation können Sie den Domänennamen hinter dem Symbol @ des Benutzers analysieren. Die folgende Anspruchstransformation veranschaulicht, wie der Domänenname aus einem Anspruch vom Typ **email** analysiert wird.

```xml
<ClaimsTransformation Id="SetDomainName" TransformationMethod="ParseDomain">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="emailAddress" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="domainName" TransformationClaimType="domain" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **emailAddress**: joe@outlook.com
- Ausgabeansprüche:
    - **domain**: outlook.com

## <a name="setclaimsifregexmatch"></a>SetClaimsIfRegexMatch

Überprüft, ob ein Zeichenfolgenanspruch `claimToMatch` und der Eingabeparameter `matchTo` gleich sind, und legt die Ausgabeansprüche mit dem Wert im Eingabeparameter `outputClaimIfMatched` fest, zusammen mit dem Ausgabeanspruch des Vergleichsregebnisses, der anhand des Vergleichsergebnisses auf `true` oder `false` festgelegt werden muss.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | matchTo | Zeichenfolge | Der reguläre Ausdruck, mit dem eine Übereinstimmung bestehen soll. |
| InputParameter | outputClaimIfMatched | Zeichenfolge | Der Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| InputParameter | extractGroups | boolean | [Optional] Gibt an, ob die Regex-Übereinstimmung Gruppenwerte extrahieren soll. Mögliche Werte sind `true` oder `false` (Standardwert). | 
| OutputClaim | outputClaim | Zeichenfolge | Wenn es eine Übereinstimmung mit dem regulären Ausdruck gibt, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `outputClaimIfMatched`. Oder Null, wenn keine Übereinstimmung vorliegt. |
| OutputClaim | regexCompareResultClaim | boolean | Der Ausgabeanspruchstyp des Übereinstimmungsergebnisses mit dem regulären Ausdruck, der anhand des Übereinstimmungsergebnisses auf `true` oder `false` festgelegt werden muss. |
| OutputClaim| Der Name des Anspruchs| Zeichenfolge | Die Liste der Anspruchstypen, die nach dem Aufrufen dieser Anspruchstransformation erstellt werden, wenn der Eingabeparameter „extractGroups“ auf „true“ festgelegt ist. Der Name des Anspruchstyps (ClaimType) muss mit dem Regex-Gruppennamen übereinstimmen. | 

### <a name="example-1"></a>Beispiel 1

Anhand des Musters des regulären Ausdrucks für Telefonnummern wird überprüft, ob die angegebene Telefonnummer gültig ist.

```xml
<ClaimsTransformation Id="SetIsPhoneRegex" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phone" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="^[0-9]{4,16}$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isPhone" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isPhoneBoolean" TransformationClaimType="regexCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **claimToMatch:** "64854114520"
- Eingabeparameter:
    - **matchTo**: "^[0-9]{4,16}$"
    - **outputClaimIfMatched**:  "isPhone"
- Ausgabeansprüche:
    - **outputClaim**: "isPhone"
    - **regexCompareResultClaim**: true

### <a name="example-2"></a>Beispiel 2

Überprüft, ob die angegebene E-Mail-Adresse gültig ist, und gibt den E-Mail-Alias zurück.

```xml
<ClaimsTransformation Id="GetAliasFromEmail" TransformationMethod="SetClaimsIfRegexMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="(?&lt;mailAlias&gt;.*)@(.*)$" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="isEmail" />
    <InputParameter Id="extractGroups" DataType="boolean" Value="true" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="validationResult" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isEmailString" TransformationClaimType="regexCompareResultClaim" />
    <OutputClaim ClaimTypeReferenceId="mailAlias" />
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **claimToMatch**: „emily@contoso.com“
- Eingabeparameter:
    - **matchTo**: `(?&lt;mailAlias&gt;.*)@(.*)$`
    - **outputClaimIfMatched**: „isEmail“
    - **extractGroups**: true
- Ausgabeansprüche:
    - **outputClaim**: „isEmail“
    - **regexCompareResultClaim**: true
    - **mailAlias**: emily
    
## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Überprüft, ob ein Zeichenfolgen-Anspruch und der Eingabeparameter `matchTo` gleich sind, und legt die Ausgabeansprüche mit dem Wert in den Eingabeparametern `stringMatchMsg` und `stringMatchMsgCode` fest, zusammen mit dem Ausgabeanspruch „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | matchTo | Zeichenfolge | Die Zeichenfolge, die mit `inputClaim` verglichen werden soll. |
| InputParameter | stringComparison | Zeichenfolge | Mögliche Werte: `Ordinal` oder `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | Zeichenfolge | Der erste Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| InputParameter | stringMatchMsgCode | Zeichenfolge | Der zweite Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| OutputClaim | outputClaim1 | Zeichenfolge | Wenn Zeichenfolgen gleich sind, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `stringMatchMsg`. |
| OutputClaim | outputClaim2 | Zeichenfolge | Wenn Zeichenfolgen gleich sind, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | boolean | Der Ausgabeanspruchstyp „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch gleich einem von Ihnen angegebenen Wert ist. Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **termsOfUseConsentVersion** gleich `v1` ist. Ist dies der Fall, ändern Sie den Wert in `v2`.

```xml
<ClaimsTransformation Id="CheckTheTOS" TransformationMethod="SetClaimsIfStringsAreEqual">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="v1" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="stringMatchMsg" DataType="string" Value="B2C_V1_90005" />
    <InputParameter Id="stringMatchMsgCode" DataType="string" Value="The TOS is upgraded to v2" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersion" TransformationClaimType="outputClaim1" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeCode" TransformationClaimType="outputClaim2" />
    <OutputClaim ClaimTypeReferenceId="termsOfUseConsentVersionUpgradeResult" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim**: v1
- Eingabeparameter:
    - **matchTo:** V1
    - **stringComparison**: ordinalIgnoreCase
    - **stringMatchMsg:**  B2C_V1_90005
    - **stringMatchMsgCode:**  Die TOS wurden auf v2 aktualisiert.
- Ausgabeansprüche:
    - **outputClaim1:** B2C_V1_90005
    - **outputClaim2:** Die TOS wurden auf v2 aktualisiert.
    - **stringCompareResultClaim**: true

## <a name="setclaimsifstringsmatch"></a>SetClaimsIfStringsMatch

Überprüft, ob ein Zeichenfolgen-Anspruch und der Eingabeparameter `matchTo` gleich sind, und legt die Ausgabeansprüche mit dem Wert im Eingabeparameter `outputClaimIfMatched` fest, zusammen mit dem Ausgabeanspruch „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | claimToMatch | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | matchTo | Zeichenfolge | Die Zeichenfolge, die mit „inputClaim“ verglichen werden soll. |
| InputParameter | stringComparison | Zeichenfolge | Mögliche Werte: `Ordinal` oder `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | Zeichenfolge | Der Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| OutputClaim | outputClaim | Zeichenfolge | Wenn Zeichenfolgen gleich sind, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `outputClaimIfMatched`. Oder NULL, wenn die Zeichenfolgen nicht übereinstimmen. |
| OutputClaim | stringCompareResultClaim | boolean | Der Ausgabeanspruchstyp „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss. |

Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **ageGroup** gleich `Minor` ist. Ist dies der Fall, wird der Wert `B2C_V1_90001` zurückgegeben.

```xml
<ClaimsTransformation Id="SetIsMinor" TransformationMethod="SetClaimsIfStringsMatch">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="ageGroup" TransformationClaimType="claimToMatch" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="matchTo" DataType="string" Value="Minor" />
    <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    <InputParameter Id="outputClaimIfMatched" DataType="string" Value="B2C_V1_90001" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isMinor" TransformationClaimType="outputClaim" />
    <OutputClaim ClaimTypeReferenceId="isMinorResponseCode" TransformationClaimType="stringCompareResultClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **claimToMatch:** Nebenversion
- Eingabeparameter:
    - **matchTo:** Nebenversion
    - **stringComparison**: ordinalIgnoreCase
    - **outputClaimIfMatched:**  B2C_V1_90001
- Ausgabeansprüche:
    - **isMinorResponseCode:** B2C_V1_90001
    - **isMinor**: true


## <a name="stringcontains"></a>StringContains

Bestimmt, ob eine angegebene Teilzeichenfolge im Eingabeanspruch vorhanden ist. Das Ergebnis ist ein neuer boolescher Anspruchstyp mit dem Wert `true` oder `false`. `true`, wenn der Wert des Parameters in dieser Zeichenfolge vorkommt, andernfalls `false`.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der gesucht werden soll. |
|InputParameter|contains|Zeichenfolge|Der zu suchende Wert.|
|InputParameter|ignoreCase|Zeichenfolge|Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den zu vergleichenden Zeichenfolgen ignoriert werden soll.|
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. Ein boolescher Indikator, wenn die Teilzeichenfolge innerhalb des Eingabeanspruchs auftritt. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Zeichenfolgenanspruchstyp eine Teilzeichenfolge enthält. Im folgenden Beispiel wird überprüft, ob der Zeichenfolgenanspruchstyp `roles` den Wert **admin** enthält.

```xml
<ClaimsTransformation Id="CheckIsAdmin" TransformationMethod="StringContains">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <InputParameters>
    <InputParameter  Id="contains" DataType="string" Value="admin"/>
    <InputParameter  Id="ignoreCase" DataType="string" Value="true"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isAdmin" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim:** "Admin, Approver, Editor"
- Eingabeparameter:
    - **contains**: "admin,"
    - **ignoreCase**: true
- Ausgabeansprüche:
    - **outputClaim**: true

## <a name="stringsubstring"></a>StringSubstring

Extrahiert Teile eines Zeichenfolgenanspruchstyps ab dem Zeichen an der angegebenen Position und gibt die angegebene Anzahl von Zeichen zurück.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der die Zeichenfolge enthält. |
| InputParameter | startIndex | INT | Die nullbasierte Anfangsposition einer Teilzeichenfolge in dieser Instanz. |
| InputParameter | length | INT | Die Anzahl der Zeichen in der Teilzeichenfolge. |
| OutputClaim | outputClaim | boolean | Eine Zeichenfolge, die der Teilzeichenfolge mit der Länge ab „startIndex“ in dieser Instanz entspricht, oder „Empty“, wenn „startIndex“ der Länge dieser Instanz entspricht und „length“ Null ist. |

Ruft z. B. die Länder-/Regionsvorwahl der Telefonnummer ab.


```xml
<ClaimsTransformation Id="GetPhonePrefix" TransformationMethod="StringSubstring">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="startIndex" DataType="int" Value="0" />
  <InputParameter Id="length" DataType="int" Value="2" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phonePrefix" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim**: "+1644114520"
- Eingabeparameter:
    - **startIndex**: 0
    - **length**:  2
- Ausgabeansprüche:
    - **outputClaim**: "+1"

## <a name="stringreplace"></a>StringReplace

Durchsucht die Zeichenfolge eine Anspruchstyps nach einem bestimmten Wert und gibt eine neue Zeichenfolge für den Anspruchstyp zurück, in der alle Vorkommen einer bestimmten Zeichenfolge in der aktuellen Zeichenfolge durch eine andere bestimmte Zeichenfolge ersetzt werden.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der die Zeichenfolge enthält. |
| InputParameter | oldValue | Zeichenfolge | Zu suchende Zeichenfolge. |
| InputParameter | newValue | Zeichenfolge | Die Zeichenfolge, die alle Vorkommen von `oldValue` ersetzen soll |
| OutputClaim | outputClaim | boolean | Eine Zeichenfolge, die der aktuellen Zeichenfolge entspricht, nur dass alle Instanzen von „oldValue“ durch „newValue“ ersetzt werden. Wenn „oldValue“ in der aktuellen Instanz nicht gefunden wird, gibt die Methode die aktuelle Instanz unverändert zurück. |

Beispielsweise können Sie eine Telefonnummer normalisieren, indem Sie die Zeichen `-` entfernen.


```xml
<ClaimsTransformation Id="NormalizePhoneNumber" TransformationMethod="StringReplace">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="inputClaim" />
  </InputClaims>
<InputParameters>
  <InputParameter Id="oldValue" DataType="string" Value="-" />
  <InputParameter Id="newValue" DataType="string" Value="" />
</InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```
### <a name="example"></a>Beispiel

- Eingabeansprüche:
    - **inputClaim**: "+164-411-452-054"
- Eingabeparameter:
    - **oldValue**: "-"
    - **newValue**: ""
- Ausgabeansprüche:
    - **outputClaim**: "+164411452054"

## <a name="stringjoin"></a>StringJoin

Verkettet die Elemente eines bestimmten stringCollection-Anspruchstyps und verwendet dazu das angegebene Trennzeichen zwischen jedem Element bzw. Member.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | stringCollection | Eine Auflistung, die die zu verkettenden Zeichenfolgen enthält. |
| InputParameter | Trennzeichen | Zeichenfolge | Die als Trennzeichen zu verwendende Zeichenfolge, z. B. Komma `,`. |
| OutputClaim | outputClaim | Zeichenfolge | Eine Zeichenfolge, die aus den Membern der Zeichenfolgenauflistung `inputClaim` besteht, getrennt durch den Eingabeparameter `delimiter`. |

Im folgenden Beispiel wird eine Zeichenfolgenauflistung von Benutzerrollen in eine Zeichenfolge mit Kommatrennzeichen konvertiert. Mit dieser Methode können Sie eine Zeichenfolgenauflistung im Azure AD-Benutzerkonto speichern. Wenn Sie später das Konto aus dem Verzeichnis lesen, konvertieren Sie die Zeichenfolge mit Kommatrennzeichen mithilfe von `StringSplit` zurück in die Zeichenfolgenauflistung.

```xml
<ClaimsTransformation Id="ConvertRolesStringCollectionToCommaDelimiterString" TransformationMethod="StringJoin">
  <InputClaims>
   <InputClaim ClaimTypeReferenceId="roles" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter DataType="string" Id="delimiter" Value="," />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="rolesCommaDelimiterConverted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputClaim**: [ "Admin", "Author", "Reader" ]
- Eingabeparameter:
  - **delimiter**: ","
- Ausgabeansprüche:
  - **outputClaim:** "Admin,Author,Reader"


## <a name="stringsplit"></a>StringSplit

Gibt ein Zeichenfolgenarray zurück, das die Teilzeichenfolgen in dieser Instanz enthält, die durch Elemente einer bestimmten Zeichenfolge getrennt sind.

| Element | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | Zeichenfolge | Ein Zeichenfolgenanspruchstyp, der die zu unterteilenden Teilzeichenfolgen enthält. |
| InputParameter | Trennzeichen | Zeichenfolge | Die als Trennzeichen zu verwendende Zeichenfolge, z. B. Komma `,`. |
| OutputClaim | outputClaim | stringCollection | Eine Zeichenfolgenauflistung, deren Elemente die Teilzeichenfolgen in dieser Zeichenfolge enthalten, die durch den Eingabeparameter `delimiter` getrennt sind. |

Im folgenden Beispiel wird eine Zeichenfolge mit durch Kommas getrennten Benutzerrollen in eine Zeichenfolgenauflistung konvertiert.

```xml
<ClaimsTransformation Id="ConvertRolesToStringCollection" TransformationMethod="StringSplit">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="rolesCommaDelimiter" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
  <InputParameter DataType="string" Id="delimiter" Value="," />
    </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="roles" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputClaim:** "Admin,Author,Reader"
- Eingabeparameter:
  - **delimiter**: ","
- Ausgabeansprüche:
  - **outputClaim**: [ "Admin", "Author", "Reader" ]

## <a name="string-claim-transformations-expressions"></a>Transformationsausdrücke für Zeichenfolgenansprüche
Anspruchstransformationsausdrücke in benutzerdefinierten Azure AD B2C-Richtlinien liefern Kontextinformationen zur Mandanten-ID und zur ID des technischen Profils.

  | Ausdruck | BESCHREIBUNG | Beispiel |
 | ----- | ----------- | --------|
 | `{TechnicalProfileId}` | Der Name der ID des technischen Profils. | Facebook-OAUTH |
 | `{RelyingPartyTenantId}` | Die Mandanten-ID der Richtlinie für die vertrauende Seite. | Ihr-Mandan.onmicrosoft.com |
 | `{TrustFrameworkTenantId}` | Die Mandanten-ID des Vertrauensframeworks. | Ihr-Mandan.onmicrosoft.com |
