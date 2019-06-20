---
title: Beispiele für die Transformation von Zeichenfolgen-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C | Microsoft-Dokumentation
description: Beispiele für die Transformation von Zeichenfolgen-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a06447aaa6579052285e7e2cd93bf40183ed173f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512586"
---
# <a name="string-claims-transformations"></a>Transformationen von Zeichenfolgen-Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel werden Beispiele für die Verwendung von Transformationen von Zeichenfolgen-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion in Azure Active Directory B2C veranschaulicht. Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="assertstringclaimsareequal"></a>AssertStringClaimsAreEqual 

Zwei Ansprüche werden miteinander verglichen, und es wird eine Ausnahme ausgelöst, wenn diese gemäß dem angegebenen Vergleich inputClaim1, inputClaim2 und stringComparison nicht gleich sind.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | Zeichenfolge | Der Typ des ersten Anspruchs, der verglichen werden soll. |
| inputClaim | inputClaim2 | Zeichenfolge | Der Typ des zweiten Anspruchs, der verglichen werden soll. |
| InputParameter | stringComparison | Zeichenfolge | Zeichenfolgenvergleich, einer der Werte: Ordinal, OrdinalIgnoreCase. |

Die Anspruchstransformation **AssertStringClaimsAreEqual** wird immer über ein [technisches Validierungsprofil](validation-technical-profile.md) ausgeführt, das von einem [selbstbestätigten technischen Profil](self-asserted-technical-profile.md) aufgerufen wird. Die Metadaten des selbstbestätigten technischen Profils **UserMessageIfClaimsTransformationStringsAreNotEqual** steuern die Fehlermeldung, die dem Benutzer anzeigt wird.

![Ausführung von AssertStringClaimsAreEqual](./media/string-transformations/assert-execution.png)

Mithilfe dieser Anspruchstransformation können Sie sicherstellen, dass zwei Anspruchstypen den gleichen Wert aufweisen. Ist dies nicht der Fall, wird eine Fehlermeldung ausgelöst. Im folgenden Beispiel wird überprüft, ob der Anspruchstyp **StrongAuthenticationEmailAddress** gleich dem Anspruchstyp **email** ist. Andernfalls wird eine Fehlermeldung ausgelöst. 

```XML
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
```XML
<TechnicalProfile Id="login-NonInteractive">
  ...
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAndStrongAuthenticationEmailAddressAreEqual" />
  </OutputClaimsTransformations>
</TechnicalProfile>
```

Das selbstbestätigte technische Profil ruft das technische Validierungsprofil **login-NonInteractive** auf.

```XML
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

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | Zeichenfolge | Der Anspruchstyp, der geändert wird. |
| InputParameter | toCase | Zeichenfolge | Einer der folgenden Werte: `LOWER` oder `UPPER`. |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieses Anspruchstyps können Sie jeden Zeichenfolgen-Anspruchstyp in Groß- oder Kleinbuchstaben ändern.  

```XML
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

Erstellt einen Zeichenfolgen-Anspruch aus dem angegebenen Eingabeparameter in der Richtlinie.

| Item | TransformationClaimType | Datentyp | Notizen |
|----- | ----------------------- | --------- | ----- |
| InputParameter | value | Zeichenfolge | Die festzulegende Zeichenfolge |
| OutputClaim | createdClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation ausgelöst wurde. Es handelt sich um den Wert, der im Eingabeparameter angegeben ist. |

Mithilfe dieser Anspruchstransformation können Sie den Wert für den Zeichenfolgen-Anspruchstyp festlegen.

```XML
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

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | Zeichenfolge | Der erste Anspruchstyp, der verglichen werden soll. |
| inputClaim | inputClaim2 | Zeichenfolge | Der zweite Anspruchstyp, der verglichen werden soll. |
| InputParameter | operator | Zeichenfolge | Mögliche Werte: `EQUAL` oder `NOT EQUAL`. |
| InputParameter | ignoreCase | boolean | Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den Zeichenfolgen, die miteinander verglichen werden, ignoriert werden soll. |
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch gleich einem anderen Anspruch ist. Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **email** gleich dem Wert des Anspruchs **Verified.Email** ist.

```XML
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

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim1 | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | operator | Zeichenfolge | Mögliche Werte: `EQUAL` oder `NOT EQUAL`. |
| InputParameter | compareTo | Zeichenfolge | Zeichenfolgenvergleich, einer der Werte: Ordinal, OrdinalIgnoreCase. |
| InputParameter | ignoreCase | boolean | Gibt an, ob bei diesem Vergleich die Groß-/Kleinschreibung in den Zeichenfolgen, die miteinander verglichen werden, ignoriert werden soll. |
| OutputClaim | outputClaim | boolean | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch gleich einem von Ihnen angegebenen Wert ist. Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **termsOfUseConsentVersion** gleich `v1` ist.

```XML
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

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputParameter | randomGeneratorType | Zeichenfolge | Gibt den zufälligen Wert an, der generiert werden soll: `GUID` (globale eindeutige ID) oder `INTEGER` (eine Ziffer). |
| InputParameter | stringFormat | Zeichenfolge | [Optional] Formatiert den Zufallswert. |
| InputParameter | base64 | boolean | [Optional] Konvertiert den Zufallswert in base64. Wenn das Zeichenfolgenformat angewendet wird, wird der Wert nach dem Zeichenfolgenformat mit base64 codiert. |
| InputParameter | maximumNumber | int | [Optional] Nur für `INTEGER` randomGeneratorType. Gibt die maximale Anzahl an. |
| InputParameter | seed  | int | [Optional] Nur für `INTEGER` randomGeneratorType. Gibt den Seed für den Zufallswert an. Hinweis: Der gleiche Seed ergibt die gleiche Sequenz von Zufallszahlen. |
| OutputClaim | outputClaim | Zeichenfolge | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Der Zufallswert. |

Im folgenden Beispiel wird eine globale eindeutige ID generiert. Mithilfe dieser Anspruchstransformation wird der zufällige UPN (User Principal Name, Benutzerprinzipalname) erstellt.

```XML
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

```XML
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

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Zeichenfolge |Der Anspruchstyp, der als Parameter {0} im Zeichenfolgenformat fungiert. |
| InputParameter | stringFormat | Zeichenfolge | Das Zeichenfolgenformat, einschließlich des Parameters {0}. |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie eine beliebige Zeichenfolge mit dem Parameter {0} formatieren. Im folgenden Beispiel wird ein **userPrincipalName** erstellt. Sämtliche technische Profile sozialer Identitätsanbieter, wie z.B. `Facebook-OAUTH`, rufen den **CreateUserPrincipalName** zum Generieren eines **userPrincipalName** auf.   

```XML
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

Formatiert zwei Ansprüche anhand der angegebenen Formatzeichenfolge. Bei dieser Transformation wird die C#-Methode **String.Format** verwendet.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |Zeichenfolge | Der Anspruchstyp, der als Parameter {0} im Zeichenfolgenformat fungiert. |
| InputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der als Parameter {1} im Zeichenfolgenformat fungiert. |
| InputParameter | stringFormat | Zeichenfolge | Das Zeichenfolgenformat, einschließlich der Parameter {0} und {1}. |
| OutputClaim | outputClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. |

Mithilfe dieser Anspruchstransformation können Sie eine beliebige Zeichenfolge mit zwei Parametern, {0} und {1}, formatieren. Im folgenden Beispiel wird ein **displayName** mit dem angegebenen Format erstellt:

```XML
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

## <a name="getmappedvaluefromlocalizedcollection"></a>GetMappedValueFromLocalizedCollection

Suche nach einem Element aus der Sammlung **Restriction** eines Anspruchs.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | mapFromClaim | Zeichenfolge | Der Anspruch mit dem Text, nach dem in den **restrictionValueClaim**-Ansprüchen über die Sammlung **Restriction** gesucht werden soll.  |
| OutputClaim | restrictionValueClaim | Zeichenfolge | Der Anspruch mit der Sammlung **Restriction**. Nach dem Aufrufen der Anspruchstransformation enthält der Wert dieses Anspruchs den Wert des ausgewählten Elements. |

Im folgenden Beispiel wird anhand des Schlüssels des Fehlers nach der Beschreibung der Fehlermeldung gesucht. Der Anspruch **responseMsg** enthält eine Sammlung von Fehlermeldungen, die dem Benutzer angezeigt oder der vertrauenden Seite gesendet werden sollen.

```XML
<ClaimType Id="responseMsg">
  <DisplayName>Error message: </DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
  <Restriction>
    <Enumeration Text="B2C_V1_90001" Value="You cant sign in because you are a minor" />
    <Enumeration Text="B2C_V1_90002" Value="This action can only be performed by gold members" />
    <Enumeration Text="B2C_V1_90003" Value="You have not been enabled for this operation" />
  </Restriction>
</ClaimType>
```
Bei der Anspruchstransformation wird nach dem Text des Elements gesucht und der zugehörige Wert zurückgegeben. Wenn die Einschränkung mithilfe von `<LocalizedCollection>` lokalisiert wird, wird der lokalisierte Wert von der Anspruchstransformation zurückgegeben.

```XML
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
    - **restrictionValueClaim:** Sie können sich nicht anmelden, da Sie nicht volljährig sind.

## <a name="lookupvalue"></a>LookupValue

Sucht in einer Liste von Werten basierend auf dem Wert eines anderen Anspruchs nach einem Anspruchswert.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputParameterId | Zeichenfolge | Der Anspruch, der den Suchwert enthält |
| InputParameter | |Zeichenfolge | Die Sammlung von inputParameters. |
| InputParameter | errorOnFailedLookup | boolean | Steuert, ob bei keinem übereinstimmenden Suchergebnis ein Fehler zurückgegeben wird. |
| OutputClaim | inputParameterId | Zeichenfolge | Die Anspruchstypen, die erstellt werden, nachdem diese Anspruchstransformation aufgerufen wurde. Der Wert der übereinstimmenden ID. |

Im folgenden Beispiel wird in einer der Sammlungen von inputParameter nach dem Domänennamen gesucht. Bei der Anspruchstransformation wird in dem Bezeichner nach dem Domänennamen gesucht und der zugehörige Wert (eine Anwendungs-ID) zurückgegeben.

```XML
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
    - **outputClaim**:  c7026f88-4299-4cdb-965d-3f166464b8a9

## <a name="nullclaim"></a>NullClaim

Bereinigt den Wert eines angegebenen Anspruchs.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | claim_to_null | Zeichenfolge | Der Anspruch, dass der zugehörige Wert gleich NULL ist. |

Mithilfe dieser Anspruchstransformation können Sie unnötige Daten aus der Eigenschaftensammlung eines Anspruchs entfernen. Folglich wird das Sitzungscookie kleiner. Im folgenden Beispiel wird der Wert des Anspruchstyps `TermsOfService` entfernt.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```

- Eingabeansprüche:
    - **outputClaim:** Willkommen bei der Contoso-App. If you continue to browse and use this website, you are agreeing to comply with and be bound by the following terms and conditions...
- Ausgabeansprüche:
    - **outputClaim:** NULL

## <a name="parsedomain"></a>ParseDomain

Ruft den Domänenteil einer E-Mail-Adresse ab.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | emailAddress | Zeichenfolge | Der Anspruchstyp, der die E-Mail-Adresse enthält. |
| OutputClaim | Domäne | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde (die Domäne). |

Mithilfe dieser Anspruchstransformation können Sie den Domänennamen hinter dem Symbol @ des Benutzers analysieren. Dies kann beim Entfernen personenbezogener Informationen aus Überwachungsdaten hilfreich sein. Die folgende Anspruchstransformation veranschaulicht, wie der Domänenname aus einem Anspruch vom Typ **email** analysiert wird.

```XML
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

## <a name="setclaimsifstringsareequal"></a>SetClaimsIfStringsAreEqual

Überprüft, ob ein Zeichenfolgen-Anspruch und der Eingabeparameter `matchTo` gleich sind, und legt die Ausgabeansprüche mit dem Wert in den Eingabeparametern `stringMatchMsg` und `stringMatchMsgCode` fest, zusammen mit dem Ausgabeanspruch „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | inputClaim | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | matchTo | Zeichenfolge | Die Zeichenfolge, die mit `inputClaim` verglichen werden soll. |
| InputParameter | stringComparison | Zeichenfolge | Mögliche Werte: `Ordinal` oder `OrdinalIgnoreCase`. |
| InputParameter | stringMatchMsg | Zeichenfolge | Der erste Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| InputParameter | stringMatchMsgCode | Zeichenfolge | Der zweite Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| OutputClaim | outputClaim1 | Zeichenfolge | Wenn Zeichenfolgen gleich sind, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `stringMatchMsg`. |
| OutputClaim | outputClaim2 | Zeichenfolge | Wenn Zeichenfolgen gleich sind, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `stringMatchMsgCode`. |
| OutputClaim | stringCompareResultClaim | boolean | Der Ausgabeanspruchstyp „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss. |

Mithilfe dieser Anspruchstransformation können Sie überprüfen, ob ein Anspruch gleich einem von Ihnen angegebenen Wert ist. Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **termsOfUseConsentVersion** gleich `v1` ist. Ist dies der Fall, ändern Sie den Wert in `v2`. 

```XML
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

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| inputClaim | claimToMatch | Zeichenfolge | Der Anspruchstyp, der verglichen werden soll. |
| InputParameter | matchTo | Zeichenfolge | Die Zeichenfolge, die mit „inputClaim“ verglichen werden soll. |
| InputParameter | stringComparison | Zeichenfolge | Mögliche Werte: `Ordinal` oder `OrdinalIgnoreCase`. |
| InputParameter | outputClaimIfMatched | Zeichenfolge | Der Wert, der festgelegt werden soll, wenn Zeichenfolgen gleich sind. |
| OutputClaim | outputClaim | Zeichenfolge | Wenn Zeichenfolgen gleich sind, enthält dieser Ausgabeanspruch den Wert des Eingabeparameters `outputClaimIfMatched`. Oder NULL, wenn die Zeichenfolgen nicht übereinstimmen. |
| OutputClaim | stringCompareResultClaim | boolean | Der Ausgabeanspruchstyp „CompareResult“, der basierend auf dem Ergebnis des Vergleichs auf `true` oder `false` festgelegt werden muss. |

Bei der folgenden Anspruchstransformation wird beispielsweise überprüft, ob der Wert des Anspruchs **ageGroup** gleich `Minor` ist. Ist dies der Fall, wird der Wert `B2C_V1_90001` zurückgegeben. 

```XML
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

