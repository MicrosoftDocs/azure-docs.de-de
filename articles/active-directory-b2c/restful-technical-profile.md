---
title: Definieren eines technischen RESTful-Profils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie ein technisches RESTful-Profil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 891991fa938ad3dcfacae6d02e40efd6d6e9689e
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97386849"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen RESTful-Profils in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) bietet Unterstützung für die Integration Ihres eigenen RESTful-Diensts. Azure AD B2C sendet Daten an den RESTful-Dienst in einer Sammlung von Eingabeansprüchen und erhält Daten in einer Sammlung von Ausgabeansprüchen zurück. Weitere Informationen finden Sie unter [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre benutzerdefinierte Azure AD B2C-Richtlinie](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `Proprietary` festgelegt werden. Das **handler**-Attribut muss den vollqualifizierten Namen der Protokollhandlerassembly, die von Azure AD B2C verwendet wird, enthalten: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

Das folgende Beispiel zeigt ein technisches RESTful-Profil:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Eingabeansprüche

Das **InputClaims**-Element enthält eine Liste von Ansprüchen, die an die REST-API gesendet werden sollen. Sie können auch den Namen Ihres Anspruchs dem in der REST-API definierten Namen zuordnen. Das folgende Beispiel zeigt die Zuordnung zwischen Ihrer Richtlinie und der REST-API. Der **givenName**-Anspruch wird als **firstName** und der **surname**-Anspruch wird als **lastName** an die REST-API gesendet. Der **email**-Anspruch wird unverändert festgelegt.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

Das **InputClaimsTransformations**-Element darf eine Sammlung von **InputClaimsTransformation**-Elementen, die zum Ändern der Eingabeansprüche oder zum Generieren neuer verwendet werden, enthalten, bevor es an die REST-API gesendet wird.

## <a name="send-a-json-payload"></a>Senden einer JSON-Nutzlast

Das technische REST-API-Profil ermöglicht es Ihnen, eine komplexe JSON-Nutzlast an einen Endpunkt zu senden.

So senden Sie eine komplexe JSON-Nutzlast:

1. Erstellen Sie die JSON-Nutzlast mit der [GenerateJson](json-transformations.md)-Anspruchstransformation.
1. Gehen Sie im technische REST-API-Profil folgendermaßen vor:
    1. Fügen Sie eine Eingabeanspruchstransformation mit einem Verweis auf die `GenerateJson`-Anspruchstransformation hinzu.
    1. Legen Sie die `SendClaimsIn`-Metadatenoption auf `body` fest.
    1. Legen Sie die `ClaimUsedForRequestPayload`-Metadatenoption auf den Namen des Anspruchs fest, der die JSON-Nutzlast enthält.
    1. Fügen Sie im Eingabeanspruch einen Verweis auf den Eingabeanspruch hinzu, der die JSON-Nutzlast enthält.

Im folgenden `TechnicalProfile`-Beispiel wird eine Überprüfungs-E-Mail mit einem Drittanbieter-E-Mail-Dienst (in diesem Fall SendGrid) gesendet.

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Ausgabeansprüche

Das **OutputClaims**-Element enthält eine Liste von Ansprüchen, die von der REST-API zurückgegeben wurden. Sie müssen den Namen des Anspruchs, der in Ihrer Richtlinie definiert ist, dem Namen, der in der REST-API definiert wurde, zuordnen. Sie können auch Ansprüche, die nicht vom Identitätsanbieter der REST-API zurückgegeben wurden, einfügen, sofern Sie das `DefaultValue`-Attribut festlegen.

Das **OutputClaimsTransformations**-Element darf eine Sammlung von **OutputClaimsTransformation**-Elementen, die zum Ändern der Ausgabeansprüche oder zum Generieren neuer verwendet werden, enthalten.

Im folgenden Beispiel wird der von der REST-API zurückgegebene Anspruch gezeigt:

- Der **MembershipId**-Anspruch wird dem Namen des **loyaltyNumber**-Anspruchs zugeordnet.

Das technische Profil gibt auch Ansprüche zurück, die vom Identitätsanbieter nicht zurückgegeben werden:

- Der **loyaltyNumberIsNew**-Anspruch hat den Standardwert `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadaten

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ServiceUrl | Ja | Die URL des REST-API-Endpunkts. |
| AuthenticationType | Ja | Der Typ der Authentifizierung, die vom RESTful-Anspruchsanbieter ausgeführt wird. Mögliche Werte: `None`, `Basic`, `Bearer`, `ClientCertificate` oder `ApiKeyHeader`. <br /><ul><li>Der Wert `None` gibt an, dass die REST-API anonym ist. </li><li>Der Wert `Basic` gibt an, dass die REST-API mit HTTP-Standardauthentifizierung geschützt ist. Nur verifizierte Benutzer, einschließlich Azure AD B2C, haben Zugriff auf Ihre API. </li><li>Der (empfohlene) Wert `ClientCertificate` gibt an, dass die REST-API den Zugriff mithilfe von Clientzertifikatauthentifizierung einschränkt. Nur Dienste mit den richtigen Zertifikaten (z.B. Azure AD B2C) erhalten Zugriff auf Ihre API. </li><li>Der Wert `Bearer` gibt an, dass die REST-API den Zugriff mithilfe des OAuth2-Bearertokens des Clients beschränkt. </li><li>Der Wert `ApiKeyHeader` gibt an, dass die REST-API mit einem API-Schlüssel im HTTP-Header (z. B. *x-functions-key*) gesichert ist. </li></ul> |
| AllowInsecureAuthInProduction| Nein| Gibt an, ob der `AuthenticationType` in der Produktionsumgebung auf `none` festgelegt werden kann (`DeploymentMode` von [TrustFrameworkPolicy](trustframeworkpolicy.md) ist auf `Production` festgelegt oder nicht angegeben). Mögliche Werte: TRUE oder FALSE (Standard). |
| SendClaimsIn | Nein | Gibt an, wie Eingabeansprüche an den RESTful-Anspruchsanbieter gesendet werden. Mögliche Werte: `Body` (Standard), `Form`, `Header`, `Url` oder `QueryString`. Der Wert `Body` ist der Eingabeanspruch, der im Anforderungstext im JSON-Format gesendet wird. Der Wert `Form` ist der Eingabeanspruch, der im Anforderungstext in einem durch kaufmännische Und-Zeichen (&) getrenntes Schlüssel-Wert-Format gesendet wird. Der Wert `Header` ist der Eingabeanspruch, der im Anforderungsheader gesendet wird. Der Wert `Url` ist der Eingabeanspruch, der in der URL gesendet wird, zum Beispiel https://{Anspruch1}.example.com/{Anspruch2}/{Anspruch3}?{Anspruch4}={Anspruch5}. Der Wert `QueryString` ist der Eingabeanspruch, der in der Abfragezeichenfolge der Anforderung gesendet wird. Die jeweils aufgerufenen HTTP-Verben lauten wie folgt:<br /><ul><li>`Body`: POST</li><li>`Form`: POST</li><li>`Header`: GET</li><li>`Url`: GET</li><li>`QueryString`: GET</li></ul> |
| ClaimsFormat | Nein | Derzeit nicht verwendet, kann ignoriert werden. |
| ClaimUsedForRequestPayload| Nein | Der Name eines Zeichenfolgenanspruchs, der die an die REST-API zu sendende Nutzlast enthält. |
| DebugMode | Nein | Führt das technische Profil im Debugmodus aus. Mögliche Werte sind `true` oder `false` (Standardwert). Im Debugmodus kann die REST-API mehr Informationen zurückgeben. Die entsprechenden Informationen finden Sie im Abschnitt [Zurückgegebene Fehlermeldung](#returning-validation-error-message). |
| IncludeClaimResolvingInClaimsHandling  | Nein | Gibt bei Eingabe- und Ausgabeansprüchen an, ob die [Anspruchsauflösung](claim-resolver-overview.md) im technischen Profil enthalten ist. Mögliche Werte sind `true` oder `false` (Standardwert). Wenn Sie im technischen Profil eine Anspruchsauflösung verwenden möchten, legen Sie für diese Einstellung den Wert `true` fest. |
| ResolveJsonPathsInJsonTokens  | Nein | Gibt an, ob das technische Profil JSON-Pfade auflöst. Mögliche Werte sind `true` oder `false` (Standardwert). Verwenden Sie diese Metadaten, um Daten aus einem geschachtelten JSON-Element zu lesen. Legen Sie in einem Ausgabeanspruch ([OutputClaim](technicalprofiles.md#output-claims)) den Partneranspruchstyp (`PartnerClaimType`) auf das auszugebende JSON-Pfadelement fest. Beispiel: `firstName.localized` oder `data.0.to.0.email`|
| UseClaimAsBearerToken| Nein| Der Name des Anspruchs, der das Bearertoken enthält.|

## <a name="error-handling"></a>Fehlerbehandlung

Die folgenden Metadaten können verwendet werden, um die Fehlermeldungen zu konfigurieren, die bei einem REST-API-Fehler angezeigt wird. Die Fehlermeldungen können [lokalisiert](localization-string-ids.md#restful-service-error-messages) werden.

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | Nein | Eine angepasste Standardfehlermeldung für alle REST-API-Ausnahmen.|
| UserMessageIfCircuitOpen | Nein | Fehlermeldung bei Nichterreichbarkeit der REST-API. Wenn hier nichts angegeben ist, wird die DefaultUserMessageIfRequestFailed-Meldung zurückgegeben. |
| UserMessageIfDnsResolutionFailed | Nein | Fehlermeldung für eine Ausnahme bei der DNS-Auflösung. Wenn hier nichts angegeben ist, wird die DefaultUserMessageIfRequestFailed-Meldung zurückgegeben. | 
| UserMessageIfRequestTimeout | Nein | Fehlermeldung bei einem Timeout der Verbindung. Wenn hier nichts angegeben ist, wird die DefaultUserMessageIfRequestFailed-Meldung zurückgegeben. | 

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Wenn als Typ der Authentifizierung `None` festgelegt ist, wird das **CryptographicKeys**-Element nicht verwendet.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `Basic` festgelegt ist, enthält das **CryptographicKeys**-Element die folgenden Attribute:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Ja | Der zur Authentifizierung verwendete Benutzername. |
| BasicAuthenticationPassword | Ja | Das zur Authentifizierung verwendete Kennwort. |

Das folgende Beispiel zeigt ein technisches Profil mit Standardauthentifizierung:

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `ClientCertificate` festgelegt ist, enthält das **CryptographicKeys**-Element das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| ClientCertificate | Ja | Das X509 Zertifikat (RSA-Schlüsselsatz) für die Authentifizierung. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `Bearer` festgelegt ist, enthält das **CryptographicKeys**-Element das folgende Attribut:

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Nein | Das OAuth 2.0-Bearertoken. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

Wenn als Typ der Authentifizierung `ApiKeyHeader` festgelegt ist, enthält das **CryptographicKeys**-Element das folgende Attribut:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| Der Name des HTTP-Headers, z. B. `x-functions-key` oder `x-api-key`. | Ja | Der zur Authentifizierung verwendete Schlüssel. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Rückgabe einer Validierungsfehlermeldung

Ihre REST-API muss möglicherweise eine Fehlermeldung zurückgeben (z.B. „Der Benutzer wurde nicht im CRM-System gefunden“). Wenn ein Fehler auftritt, sollte die REST-API eine HTTP-Fehlermeldung mit dem Antwortstatuscode 4xx zurückgeben, z B. 400 (Ungültige Anforderung) oder 409 (Konflikt). Der Antworttext enthält eine Fehlermeldung im JSON-Format:

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| attribute | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| version | Ja | Die Version Ihrer REST-API. Beispiel: 1.0.1 |
| status | Ja | Muss 409 sein |
| code | Nein | Ein Fehlercode vom RESTful-Endpunktanbieter, der angezeigt wird, wenn `DebugMode` aktiviert ist. |
| requestId | Nein | Eine Anforderungs-ID vom RESTful-Endpunktanbieter, die angezeigt wird, wenn `DebugMode` aktiviert ist. |
| userMessage | Ja | Eine Fehlermeldung, die dem Benutzer angezeigt wird. |
| developerMessage | Nein | Die ausführliche Beschreibung des Problems und Informationen zur Behebung, die angezeigt werden, wenn `DebugMode` aktiviert ist. |
| moreInfo | Nein | Ein URI, der auf zusätzliche Informationen verweist, die angezeigt werden, wenn `DebugMode` aktiviert ist. |


Das folgende Beispiel zeigt eine C#-Klasse, die eine Fehlermeldung zurückgibt:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie Beispiele für die Verwendung eines technischen RESTful-Profils:

- [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre benutzerdefinierte Azure AD B2C-Richtlinie](custom-policy-rest-api-intro.md)
- [Exemplarische Vorgehensweise: Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey zur Validierung der Benutzereingabe](custom-policy-rest-api-claims-validation.md)
- [Exemplarische Vorgehensweise: Hinzufügen von REST-API-Anspruchsaustauschvorgängen zu benutzerdefinierten Richtlinien in Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Sichern von REST-API-Diensten](secure-rest-api.md)

