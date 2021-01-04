---
title: Definieren eines technischen ID-Tokenhinweisprofils in einer benutzerdefinierten Richtlinie
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, wie Sie ein technisches ID-Tokenhinweisprofil in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C definieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 79a99d9f0ca117d8f47d56d76399210a72b91bb7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951654"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definieren eines technischen ID-Tokenhinweisprofils in einer benutzerdefinierten Azure Active Directory B2C-Richtlinie

Azure AD B2C ermöglicht es Anwendungen der vertrauenden Seite, ein eingehendes JSON Web Token als Teil der OAuth2-Autorisierungsanforderung zu senden. Das JWT-Token kann von einer Anwendung der vertrauenden Seite oder einem Identitätsanbieter ausgegeben werden, und es kann einen Hinweis zum Benutzer oder zur Autorisierungsanforderung übergeben. Azure AD B2C überprüft die Signatur, den Ausstellernamen und die Tokenzielgruppe und extrahiert den Anspruch aus dem eingehenden Token.

## <a name="use-cases"></a>Anwendungsfälle

Mit dieser Lösung können Sie Daten an Azure AD B2C senden, die in einem einzelnen JWT-Token gekapselt sind. Die [Lösung zum Anmelden mit einer E-Mail-Einladung](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), bei der Ihr Systemadministrator eine signierte Einladung an Benutzer senden kann, basiert auf „id_token_hint“. Nur Benutzer mit Zugriff auf die Einladungs-E-Mail können das Konto im Verzeichnis erstellen.

## <a name="token-signing-approach"></a>Tokensignaturansatz

Bei „id_token_hint“ verfasst der Tokenaussteller (eine App der vertrauenden Seite oder ein Identitätsanbieter) das Token und signiert es dann mithilfe eines Signaturschlüssels, um nachzuweisen, dass es von einer vertrauenswürdigen Quelle stammt. Der Signaturschlüssel kann symmetrisch oder asymmetrisch sein. Bei der symmetrischen Kryptografie oder Kryptografie mit privatem Schlüssel wird ein gemeinsames Geheimnis verwendet, um die Signatur zu signieren und zu überprüfen. Die asymmetrische Kryptografie oder Kryptografie mit öffentlichem Schlüssel ist ein Kryptografiesystem, das sowohl einen privaten Schlüssel als auch einen öffentlichen Schlüssel verwendet. Der private Schlüssel ist nur dem Tokenaussteller bekannt und wird verwendet, um das Token zu signieren. Der öffentliche Schlüssel wird für die Azure AD B2C-Richtlinie freigegeben, um die Signatur des Tokens zu überprüfen.

## <a name="token-format"></a>Tokenformat

„id_token_hint“ muss ein gültiges JWT-Token sein. In den folgenden Tabelle werden die erforderlichen Ansprüche aufgeführt. Zusätzliche Ansprüche sind optional.

| Name | Anspruch | Beispielwert | BESCHREIBUNG |
| ---- | ----- | ------------- | ----------- |
| Zielgruppe | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identifiziert den vorgesehenen Empfänger des Tokens. Dies ist eine beliebige Zeichenfolge, die vom Tokenaussteller definiert wird. Azure AD B2C überprüft diesen Wert und lehnt das Token ab, wenn es nicht mit der Vorgabe übereinstimmt.  |
| Issuer (Aussteller) | `iss` |`https://localhost` | Hiermit wird der Sicherheitstokendienst (Tokenaussteller) identifiziert. Dies ist ein beliebiger URI, der vom Tokenaussteller definiert wird. Azure AD B2C überprüft diesen Wert und lehnt das Token ab, wenn es nicht mit der Vorgabe übereinstimmt.  |
| Ablaufzeit | `exp` | `1600087315` | Die Zeit, zu der das Token ungültig wird (dargestellt als Epochenzeit). Azure AD B2C überprüft diesen Anspruch nicht. |
| Nicht vor | `nbf` | `1599482515` | Die Zeit, zu der das Token gültig wird (dargestellt als Epochenzeit). Diese Zeit entspricht in der Regel dem Ausstellungszeitpunkt des Tokens. Azure AD B2C überprüft diesen Anspruch nicht. |

 Das folgende Token ist ein Beispiel für ein gültiges ID-Token:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protocol

Das **Name**-Attribut des **Protocol**-Elements muss auf `None` festgelegt werden. Das Protokoll für das technische Profil **IdTokenHint_ExtractClaims** ist beispielsweise `None`:

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

Das technische Profil wird von einem Orchestrierungsschritt mit dem Typ von `GetClaims` aufgerufen.

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Ausgabeansprüche

Das Element **OutputClaims** enthält eine Liste mit Ansprüchen, die aus dem JWT-Token extrahiert werden sollen. Sie müssen den Namen des in Ihrer Richtlinie definierten Anspruchs möglicherweise dem im JWT-Token definierten Namen zuordnen. Sie können auch Ansprüche einfügen, die nicht vom JWT-Token zurückgegeben werden, sofern Sie das `DefaultValue`-Attribut festlegen.

## <a name="metadata"></a>Metadaten

Die folgenden Metadaten sind relevant, wenn der symmetrische Schlüssel verwendet wird. 

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| Issuer (Aussteller) | Ja | Hiermit wird der Sicherheitstokendienst (Tokenaussteller) identifiziert. Dieser Wert muss mit dem `iss`-Anspruch innerhalb des JWT-Tokenanspruchs identisch sein. | 
| IdTokenAudience | Ja | Identifiziert den vorgesehenen Empfänger des Tokens. Dieser Wert muss mit dem `aud`-Anspruch innerhalb des JWT-Tokenanspruchs identisch sein. | 

Die folgenden Metadaten sind relevant, wenn ein asymmetrischer Schlüssel verwendet wird. 

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| METADATA| Ja | Dies ist eine URL, die auf ein Konfigurationsdokument eines Tokenausstellers verweist, das auch als bekannter OpenID-Konfigurationsendpunkt bekannt ist.   |
| Issuer (Aussteller) | Nein | Hiermit wird der Sicherheitstokendienst (Tokenaussteller) identifiziert. Dieser Wert kann verwendet werden, um den in den Metadaten konfigurierten Wert zu überschreiben. Er muss mit dem `iss`-Anspruch innerhalb des JWT-Tokenanspruchs identisch sein. |  
| IdTokenAudience | Nein | Identifiziert den vorgesehenen Empfänger des Tokens. Dieser Wert muss mit dem `aud`-Anspruch innerhalb des JWT-Tokenanspruchs identisch sein. |  

## <a name="cryptographic-keys"></a>Kryptografische Schlüssel

Bei Verwendung eines symmetrischen Schlüssels enthält das Element **CryptographicKeys** die folgenden Attribute:

| attribute | Erforderlich | Beschreibung |
| --------- | -------- | ----------- |
| client_secret | Ja | Dies ist der Kryptografieschlüssel, der zum Überprüfen der JWT-Tokensignatur verwendet wird.|


## <a name="how-to-guide"></a>Schrittanleitung

### <a name="issue-a-token-with-symmetric-keys"></a>Ausstellen eines Tokens mit symmetrischen Schlüsseln

#### <a name="step-1-create-a-shared-key"></a>Schritt 1: Erstellen eines gemeinsam verwendeten Schlüssels 

Erstellen Sie einen Schlüssel, der zum Signieren des Tokens verwendet werden kann. Verwenden Sie beispielsweise den folgenden PowerShell-Code, um einen Schlüssel zu generieren.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Mit diesem Code wird ein Geheimnis wie `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` erstellt.

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Schritt 2: Hinzufügen des Signaturschlüssels zu Azure AD B2C

Der Schlüssel, der vom Tokenaussteller verwendet wird, muss in Ihren Azure AD B2C-Richtlinienschlüsseln erstellt werden.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie auf der Übersichtsseite unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Klicken Sie auf **Richtlinienschlüssel**. 
1. Wählen Sie **Manuell** aus.
1. Verwenden Sie für **Name** den Wert `IdTokenHintKey`.  
   Das Präfix `B2C_1A_` wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den zuvor generierten Anmeldeschlüssel ein.
1. Verwenden Sie für **Schlüsselverwendung** die Option **Verschlüsselung**.
1. Klicken Sie auf **Erstellen**.
1. Vergewissern Sie sich, dass Sie den Schlüssel `B2C_1A_IdTokenHintKey` erstellt haben.


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Schritt 3: Hinzufügen des technischen ID-Tokenhinweisprofils

Das folgende technische Profil überprüft das Token und extrahiert die Ansprüche. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Schritt 4: Vorbereiten Ihrer Richtlinie

Schließen Sie den Schritt [Konfigurieren Sie Ihre Richtlinie](#configure-your-policy) ab.

#### <a name="step-5-prepare-the-code"></a>Schritt 5. Vorbereiten des Codes  

Das [GitHub-Beispiel](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) ist eine ASP.NET-Webanwendung und Konsolen-App, die ein ID-Token generiert, das mithilfe eines symmetrischen Schlüssels signiert wird. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Ausstellen eines Tokens mit asymmetrischen Schlüsseln

Mit einem asymmetrischen Schlüssel wird das Token mithilfe von RSA-Zertifikaten signiert. Diese Anwendung hostet einen OpenID Connect-Metadatenendpunkt und JWK-Endpunkt (JSON Web Key), die von Azure AD B2C zum Überprüfen der Signatur des ID-Tokens verwendet werden.

Der Tokenaussteller muss folgende Endpunkte bereitstellen:

* `/.well-known/openid-configuration`: Dies ist ein bekannter Konfigurationsendpunkt mit relevanten Informationen über das Token (z. B. Name des Tokenausstellers und Link zum JWK-Endpunkt). 
* `/.well-known/keys`: Dies ist der JWK-Endpunkt (JSON Web Key) mit dem öffentlichen Schlüssel, der zum Signieren des Schlüssels (mit dem privaten Schlüsselteil des Zertifikats) verwendet wird.

Sehen Sie sich das .NET-MVC-Controllerbeispiel [TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) an.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Schritt 1: Vorbereiten eines selbstsignierten Zertifikats

Wenn Sie noch nicht über ein Zertifikat verfügen, können Sie für diese Schrittanleitung ein selbstsigniertes Zertifikat verwenden. Unter Windows können Sie das PowerShell-Cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) verwenden, um ein Zertifikat zu generieren.

Führen Sie diesen PowerShell-Befehl aus, um ein selbstsigniertes Zertifikat zu generieren. Ändern Sie das Argument `-Subject` entsprechend Ihrer Anwendung und des Azure AD B2C-Mandantennamens. Sie können auch das `-NotAfter`-Datum anpassen, um einen anderen Ablaufzeitpunkt für das Zertifikat anzugeben.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Schritt 2: Hinzufügen des technischen ID-Tokenhinweisprofils 

Das folgende technische Profil überprüft das Token und extrahiert die Ansprüche. Ändern Sie den Metadaten-URI in den bekannten Konfigurationsendpunkt des Tokenausstellers.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Schritt 3: Vorbereiten Ihrer Richtlinie

Schließen Sie den Schritt [Konfigurieren Sie Ihre Richtlinie](#configure-your-policy) ab.

#### <a name="step-4-prepare-the-code"></a>Schritt 4: Vorbereiten des Codes 

Diese ASP.NET-Webanwendung im [GitHub-Beispiel](https://github.com/azure-ad-b2c/id-token-builder) generiert ID-Token und hostet die Metadatenendpunkte, die für die Verwendung des Parameters „id_token_hint“ in Azure AD B2C erforderlich sind.


### <a name="configure-your-policy"></a>Konfigurieren der Richtlinie

Sowohl beim symmetrischen als auch beim asymmetrischen Ansatz wird das technische Profil `id_token_hint` über einen Orchestrierungsschritt des Typs `GetClaims` aufgerufen und muss die Eingabeansprüche der Richtlinie für die vertrauende Seite angeben.

1. Fügen Sie Ihrer Erweiterungsrichtlinie das technische Profil „IdTokenHint_ExtractClaims“ hinzu.
1. Fügen Sie Ihrer User Journey den folgenden Orchestrierungsschritt als erstes Element hinzu.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. Wiederholen Sie in der Richtlinie für die vertrauende Seite dieselben Eingabeansprüche, die Sie im technischen Profil „IdTokenHint_ExtractClaims “ konfiguriert haben. Beispiel:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

Abhängig von Ihren geschäftlichen Anforderungen müssen Sie möglicherweise Tokenüberprüfungen hinzufügen, um beispielsweise die Ablaufzeit von Token, das Format der E-Mail-Adresse und vieles mehr zu überprüfen. Fügen Sie hierzu Orchestrierungsschritte hinzu, die ein [technisches Profil zur Anspruchstransformation](claims-transformation-technical-profile.md) aufrufen. Fügen Sie auch ein [selbstbestätigtes technisches Profil](self-asserted-technical-profile.md) hinzu, um eine Fehlermeldung anzuzeigen. 

### <a name="create-and-sign-a-token"></a>Erstellen und Signieren eines Tokens

In den GitHub-Beispielen wird veranschaulicht, wie ein solches Token in einem JWT-Token erstellt wird, das später als `id_token_hint`-Abfragezeichenfolgenparameter gesendet wird. Im Folgenden finden Sie ein Beispiel für eine Autorisierungsanforderung mit dem Parameter „id_token_hint“.
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Lösung zum [Registrieren mit einer Einladungs-E-Mail](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) im Azure AD B2C-Repository der GitHub-Community an.