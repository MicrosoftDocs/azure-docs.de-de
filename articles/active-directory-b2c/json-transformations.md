---
title: Beispiele für die Transformation von JSON-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C | Microsoft-Dokumentation
description: Hier finden Sie Beispiele für die Transformation von JSON-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion von Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a026d205d3ab855ecbb51048e7464df6fb4a094
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510750"
---
# <a name="json-claims-transformations"></a>Transformationen von JSON-Ansprüchen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

In diesem Artikel werden Beispiele für die Verwendung von Transformationen von JSON-Ansprüchen für das Schema des Frameworks für die Identitätsfunktion in Azure Active Directory B2C veranschaulicht. Weitere Informationen finden Sie unter [ClaimsTransformations](claimstransformations.md).

## <a name="getclaimfromjson"></a>GetClaimFromJson

Abrufen eines angegebenen JSON-Datenelements

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Zeichenfolge | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, um das Element abzurufen |
| InputParameter | claimToExtract | Zeichenfolge | Der Name des JSON-Elements, das extrahiert werden soll |
| OutputClaim | extractedClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation ausgelöst wurde. Es handelt sich um den Elementwert, der im Eingabeparameter _claimToExtract_ angegeben ist. |

Im folgenden Beispiel hat die Anspruchstransformation das `emailAddress`-Element aus dem JSON-Datenelement extrahiert: `{"emailAddress": "someone@example.com", "displayName": "Someone"}`

```XML
<ClaimsTransformation Id="GetEmailClaimFromJson" TransformationMethod="GetClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="customUserData" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="emailAddress" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="extractedEmail" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone"}
- Eingabeparameter:
    - **claimToExtract**: emailAddress
- Ausgabeansprüche: 
  - **extractedClaim**: someone@example.com


## <a name="getclaimsfromjsonarray"></a>GetClaimsFromJsonArray

Abrufen einer Liste der angegebenen JSON-Datenelemente

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | jsonSourceClaim | Zeichenfolge | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, um die Ansprüche abzurufen. |
| InputParameter | errorOnMissingClaims | boolean | Gibt an, ob ein Fehler ausgelöst werden soll, wenn einer der Ansprüche fehlt. |
| InputParameter | includeEmptyClaims | Zeichenfolge | Gibt an, dass leere Ansprüche hinzugefügt werden sollen. |
| InputParameter | jsonSourceKeyName | Zeichenfolge | Name des Elementschlüssels |
| InputParameter | jsonSourceValueName | Zeichenfolge | Name des Elementwerts |
| OutputClaim | Sammlung | string, int, boolean und datetime |Liste mit Ansprüchen, die extrahiert werden sollen. Der Name des Anspruchs soll dem Namen entsprechen, der im Eingabeanspruch _jsonSourceClaim_ angegeben ist. |

In diesem Beispiel extrahiert die Anspruchstransformation die folgenden Ansprüche aus dem JSON-Datenelement: email (string), displayName (string), membershipNum (int), active (boolean) und birthdate (datetime).

```JSON
[{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value":true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
```

```XML
<ClaimsTransformation Id="GetClaimsFromJson" TransformationMethod="GetClaimsFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="jsonSourceClaim" TransformationClaimType="jsonSource" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="errorOnMissingClaims" DataType="boolean" Value="false" />
    <InputParameter Id="includeEmptyClaims" DataType="boolean" Value="false" />
    <InputParameter Id="jsonSourceKeyName" DataType="string" Value="key" />
    <InputParameter Id="jsonSourceValueName" DataType="string" Value="value" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="membershipNum" />
    <OutputClaim ClaimTypeReferenceId="active" />
    <OutputClaim ClaimTypeReferenceId="birthdate" />
  </OutputClaims>
</ClaimsTransformation>
```    

- Eingabeansprüche:
  - **jsonSourceClaim**: [{"key":"email","value":"someone@example.com"}, {"key":"displayName","value":"Someone"}, {"key":"membershipNum","value":6353399}, {"key":"active","value": true}, {"key":"birthdate","value":"1980-09-23T00:00:00Z"}]
- Eingabeparameter:
    - **errorOnMissingClaims**: FALSE
    - **includeEmptyClaims**: FALSE
    - **jsonSourceKeyName**: Schlüssel
    - **jsonSourceValueName**: Wert
- Ausgabeansprüche:
  - **email**: "someone@example.com"
  - **displayName**: "Someone"
  - **membershipNum**: 6353399
  - **active**: TRUE
  - **birthdate**: 1980-09-23T00:00:00Z

## <a name="getnumericclaimfromjson"></a>GetNumericClaimFromJson

Abrufen eines angegebenen numerisches JSON-Datenelements (long)

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJson | Zeichenfolge | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, um den Anspruch abzurufen. |
| InputParameter | claimToExtract | Zeichenfolge | Der Name des JSON-Elements, das extrahiert werden soll |
| OutputClaim | extractedClaim | lang | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation ausgelöst wurde. Es handelt sich um den Elementwert, der im Eingabeparameter _claimToExtract_ angegeben ist. |

Im folgenden Beispiel extrahiert die Anspruchtransformation das `id`-Element aus dem JSON-Datenelement.

```JSON
{
    "emailAddress": "someone@example.com", 
    "displayName": "Someone", 
    "id" : 6353399
}
```

```XML
<ClaimsTransformation Id="GetIdFromResponse" TransformationMethod="GetNumericClaimFromJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="exampleInputClaim" TransformationClaimType="inputJson" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="claimToExtract" DataType="string" Value="id" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="membershipId" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputJson**: {"emailAddress": "someone@example.com", "displayName": "Someone", "id" : 6353399}
- Eingabeparameter
    - **claimToExtract**:  id
- Ausgabeansprüche: 
    - **extractedClaim**: 6353399

## <a name="getsinglevaluefromjsonarray"></a>GetSingleValueFromJsonArray

Abrufen des ersten Elements aus dem JSON-Datenarray.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputJsonClaim | Zeichenfolge | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, um das Element aus dem JSON-Array abzurufen. |
| OutputClaim | extractedClaim | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. Es handelt sich hierbei um das erste Element im JSON-Array. |

Im folgenden Beispiel extrahiert die Anspruchstransformation das erste Element (die E-Mail-Adresse) aus dem JSON-Array `["someone@example.com", "Someone", 6353399]`.

```XML
<ClaimsTransformation Id="GetEmailFromJson" TransformationMethod="GetSingleValueFromJsonArray">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userData" TransformationClaimType="inputJsonClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="extractedClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>Beispiel

- Eingabeansprüche:
  - **inputJsonClaim**: ["someone@example.com", "Someone", 6353399]
- Ausgabeansprüche: 
  - **extractedClaim**: someone@example.com

## <a name="xmlstringtojsonstring"></a>XmlStringToJsonString

Konvertieren von XML-Daten ins JSON-Format.

| Item | TransformationClaimType | Datentyp | Notizen |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | Xml | Zeichenfolge | Die Anspruchstypen, die von der Anspruchstransformation verwendet werden, um das Datenelement von XML ins JSON-Format zu konvertieren. |
| OutputClaim | json | Zeichenfolge | Der Anspruchstyp, der erstellt wird, nachdem diese Anspruchstransformation aufgerufen wurde. Es handelt sich hierbei um die Daten im JSON-Format. |

```XML
<ClaimsTransformation Id="ConvertXmlToJson" TransformationMethod="XmlStringToJsonString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="intpuXML" TransformationClaimType="xml" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="outputJson" TransformationClaimType="json" />
  </OutputClaims>
</ClaimsTransformation>
```

Im folgenden Beispiel konvertiert die Anspruchstransformation die folgenden XML-Daten ins JSON-Format.

#### <a name="example"></a>Beispiel
Eingabeanspruch:

```XML
<user>
  <name>Someone</name>
  <email>someone@example.com</email>
</user>
```

Ausgabeanspruch:

```JSON
{
  "user": {
    "name":"Someone",
    "email":"someone@example.com"
  }
}
```

