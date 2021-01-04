---
title: Schützen eines RESTful-Diensts in Ihrer Azure AD B2C-Instanz
titleSuffix: Azure AD B2C
description: Schützen Sie Ihre benutzerdefinierten REST-API-Anspruchsaustauschvorgänge in Ihrer Azure AD B2C-Instanz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6907db7f6e53247a8f2fc0042e8c8e6b081dbd3
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516375"
---
# <a name="secure-your-restful-services"></a>Schützen Ihrer RESTful-Dienste 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wenn Sie eine REST-API in eine Azure AD B2C-User Journey integrieren, müssen Sie Ihren REST-API-Endpunkt mittels Authentifizierung schützen. Dadurch wird sichergestellt, dass nur Dienste mit richtigen Anmeldeinformationen, wie z. B. Azure AD B2C, Aufrufe an Ihren REST-API-Endpunkt tätigen können.

Informationen zum Integrieren einer REST-API in Ihre Azure AD B2C-User Journey finden Sie in den Artikeln [Validierung der Benutzereingabe](custom-policy-rest-api-claims-validation.md) und [Hinzufügen von REST-API-Anspruchsaustauschvorgängen zu benutzerdefinierten Richtlinien](custom-policy-rest-api-claims-exchange.md).

In diesem Artikel wird beschrieben, wie Sie Ihre REST-API mithilfe der HTTP-Standard-, Clientzertifikat- oder OAuth2-Authentifizierung schützen. 

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die in einer der folgenden Anleitungen aufgeführten Schritte aus:

- [Integrieren von REST-API-Anspruchsaustauschvorgängen in Ihre Azure AD B2C-User Journey zum Validieren der Benutzereingabe](custom-policy-rest-api-claims-validation.md)
- [Hinzufügen von REST-API-Anspruchsaustauschvorgängen zu benutzerdefinierten Richtlinien](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP-Standardauthentifizierung

Die HTTP-Standardauthentifizierung ist in [RFC 2617](https://tools.ietf.org/html/rfc2617) definiert. Die Standardauthentifizierung funktioniert wie folgt: Azure AD B2C sendet eine HTTP-Anforderung mit den Clientanmeldeinformationen im Autorisierungsheader. Die Anmeldeinformationen werden als Base64-codierte Zeichenfolge im Format „Benutzername:Kennwort“ formatiert.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Hinzufügen von Richtlinienschlüsseln für REST-API-Benutzernamen und -Kennwörter

Um ein technisches REST-API-Profil mit HTTP-Standardauthentifizierung zu konfigurieren, müssen Sie die folgenden kryptografischen Schlüssel zum Speichern des Benutzernamens und des Kennworts erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den Filter **Verzeichnis und Abonnement** und dann Ihr Azure AD B2C-Verzeichnis aus.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **RestApiUsername** ein.
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den REST-API-Benutzernamen ein.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie erneut **Richtlinienschlüssel** aus.
1. Wählen Sie **Hinzufügen**.
1. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **RestApiPassword** ein.
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** das REST-API-Kennwort ein.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Konfigurieren Ihres technischen REST-API-Profils für die Verwendung der HTTP-Standardauthentifizierung

Konfigurieren Sie nach dem Erstellen der erforderlichen Schlüssel die Metadaten Ihres technischen REST-API-Profils so, dass sie auf die Anmeldeinformationen verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `Basic`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Nachfolgend finden Sie ein Beispiel für ein mit der HTTP-Standardauthentifizierung konfiguriertes technisches RESTful-Profi:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS-Clientzertifikatauthentifizierung

Bei der Clientzertifikatauthentifizierung handelt es sich um eine gegenseitige zertifikatbasierte Authentifizierung, bei der der Client (Azure AD B2C) sein Clientzertifikat für den Server bereitstellt, um seine Identität nachzuweisen. Dies erfolgt im Rahmen des SSL-Handshakes. Nur Dienste mit den richtigen Zertifikaten, z. B. Azure AD B2C, können auf Ihren REST-API-Dienst zugreifen. Bei dem Clientzertifikat handelt es sich um ein digitales X.509-Zertifikat. In Produktionsumgebungen muss es von einer Zertifizierungsstelle signiert werden.

### <a name="prepare-a-self-signed-certificate-optional"></a>Vorbereiten eines selbstsignierten Zertifikats (optional)

Wenn Sie noch nicht über ein Zertifikat verfügen, können Sie in Nicht-Produktionsumgebungen ein selbstsigniertes Zertifikat verwenden. Unter Windows können Sie das PowerShell-Cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) verwenden, um ein Zertifikat zu generieren.

1. Führen Sie diesen PowerShell-Befehl aus, um ein selbstsigniertes Zertifikat zu generieren. Ändern Sie das Argument `-Subject` entsprechend Ihrer Anwendung und des Azure AD B2C-Mandantennamens. Sie können auch das `-NotAfter`-Datum anpassen, um einen anderen Ablaufzeitpunkt für das Zertifikat anzugeben.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Öffnen Sie **Benutzerzertifikate verwalten** > **Aktueller Benutzer** > **Persönlich** > **Zertifikate** > *NameIhrerApp.IhrMandant.onmicrosoft.com*.
1. Wählen Sie das Zertifikat und dann **Aktion** > **Alle Aufgaben** > **Exportieren** aus.
1. Wählen Sie **Ja** > **Weiter** > **Ja, privaten Schlüssel exportieren** > **Weiter** aus.
1. Übernehmen Sie die Standardeinstellungen für **Format der zu exportierenden Datei**.
1. Geben Sie ein Kennwort für das Zertifikat an.

### <a name="add-a-client-certificate-policy-key"></a>Hinzufügen eines Richtlinienschlüssels für das Clientzertifikat

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den Filter **Verzeichnis und Abonnement** und dann Ihr Azure AD B2C-Verzeichnis aus.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie im Feld **Optionen** die Option **Upload** aus.
1. Geben Sie im Feld **Name** die Zeichenfolge **RestApiClientCertificate** ein.
    Das Präfix *B2C_1A_* wird automatisch hinzugefügt.
1. Wählen Sie im Feld **Dateiupload** die PFX-Datei Ihres Zertifikats mit dem privaten Schlüssel aus.
1. Geben Sie im Feld **Kennwort** das Kennwort des Zertifikats ein.
1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Konfigurieren Ihres technischen REST-API-Profils für die Verwendung der Clientzertifikatauthentifizierung

Konfigurieren Sie nach dem Erstellen des erforderlichen Schlüssels die Metadaten Ihres technischen REST-API-Profils so, dass sie auf das Clientzertifikat verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `ClientCertificate`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Nachfolgend finden Sie ein Beispiel für ein mit einem HTTP-Clientzertifikat konfiguriertes technisches RESTful-Profi:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2-Bearerauthentifizierung 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Die Definition der Bearertokenauthentifizierung finden Sie unter [OAuth2.0 Authorization Framework: Verwendung von Bearertoken (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Bei der Bearertokenauthentifizierung sendet Azure AD B2C eine HTTP-Anforderung mit einem Token im Autorisierungsheader.

```http
Authorization: Bearer <token>
```

Ein Bearertoken ist eine nicht transparente Zeichenfolge. Dabei kann es sich um ein JWT-Zugriffstoken oder eine beliebige Zeichenfolge handeln, von dem/der die REST-API erwartet, dass es/sie von Azure AD B2C im Autorisierungsheader gesendet wird. Azure AD B2C unterstützt die folgenden Typen:

- **Bearertoken**. Damit das Bearertoken im technischen RESTful-Profil gesendet werden kann, muss Ihre Richtlinie das Bearertoken zuerst abrufen und dann im technischen RESTful-Profil verwenden.  
- **Statisches Bearertoken**. Verwenden Sie diesen Ansatz, wenn Ihre REST-API ein langfristiges Zugriffstoken ausgibt. Wenn Sie ein statisches Bearertoken verwenden möchten, müssen Sie einen Richtlinienschlüssel und dann einen Verweis vom technischen RESTful-Profil auf Ihren Richtlinienschlüssel erstellen. 


## <a name="using-oauth2-bearer"></a>Verwenden eines OAuth2-Bearertokens  

In den folgenden Schritten wird veranschaulicht, wie Sie mithilfe von Clientanmeldeinformationen ein Bearertoken abrufen und es im Autorisierungsheader der REST-API-Aufrufe übergeben.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definieren eines Anspruchs zum Speichern des Bearertokens

Ein Anspruch ermöglicht die temporäre Speicherung von Daten während der Ausführung einer Azure AD B2C-Richtlinie. Im [Anspruchsschema](claimsschema.md) deklarieren Sie Ihre Ansprüche. Das Zugriffstoken muss in einem Anspruch gespeichert werden, um später verwendet werden zu können. 

1. Öffnen Sie die Erweiterungsdatei Ihrer Richtlinie. Beispiel: <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Suchen Sie nach dem Element [BuildingBlocks](buildingblocks.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Suchen Sie nach dem Element [ClaimsSchema](claimsschema.md). Wenn das Element nicht vorhanden ist, fügen Sie es hinzu.
1. Fügen Sie dem Element **ClaimsSchema** die folgenden Ansprüche hinzu.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Abrufen eines Zugriffstokens 

Sie können ein Zugriffstoken auf eine von mehreren Arten abrufen: durch Abrufen des Tokens [von einem Verbundidentitätsanbieter](idp-pass-through-user-flow.md), durch Aufrufen einer REST-API, die ein Zugriffstoken zurückgibt, mithilfe eines [ROPC-Flows](../active-directory/develop/v2-oauth-ropc.md) oder mithilfe des [Clientanmeldeinformationsflows](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).  

Im folgenden Beispiel wird ein technisches REST-API-Profil verwendet, um eine Anforderung an den Azure AD-Tokenendpunkt zu senden, wobei die Clientanmeldeinformationen als HTTP-Standardauthentifizierung verwendet werden. Informationen zur entsprechenden Konfiguration in Azure AD finden Sie unter [Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Möglicherweise müssen Sie Änderungen daran vornehmen, um die Interaktion mit Ihrem Identitätsanbieter zu ermöglichen. 

Ersetzen Sie im „ServiceUrl“-Element den Platzhalter „your-tenant-name“ durch den Namen Ihres Azure AD-Mandanten. Alle verfügbaren Optionen finden Sie unter [Definieren eines technischen RESTful-Profils](restful-technical-profile.md).

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Ändern des technischen REST-Profils zur Verwendung der Bearertokenauthentifizierung

Um die Bearertokenauthentifizierung in Ihrer benutzerdefinierten Richtlinie zu unterstützen, müssen Sie das technische REST-API-Profil wie folgt ändern:

1. Öffnen Sie die Erweiterungsrichtliniendatei *TrustFrameworkExtensions.xml* in Ihrem Arbeitsverzeichnis.
1. Suchen Sie den `<TechnicalProfile>`-Knoten, der `Id="REST-API-SignUp"` enthält.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) wie folgt in *Bearer*:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Ändern Sie das Element *UseClaimAsBearerToken* in *bearerToken*, oder fügen Sie es wie folgt hinzu. *bearerToken* ist der Name des Anspruchs, von dem das Bearertoken abgerufen wird (der Ausgabeanspruch von `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Stellen Sie sicher, dass Sie den oben verwendeten Anspruch als Eingabeanspruch hinzufügen:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Nachdem Sie den obigen Codeausschnitt hinzugefügt haben, sollte Ihr technisches Profil wie der folgende XML-Code aussehen:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Verwenden eines statischen OAuth2-Bearertokens 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Hinzufügen des Richtlinienschlüssels für das OAuth2-Bearertoken

Rufen Sie zum Konfigurieren eines technischen REST-API-Profils mit einem OAuth2-Bearertoken ein Zugriffstoken vom REST-API-Besitzer ab. Erstellen Sie dann den folgenden kryptografischen Schlüssel zum Speichern des Bearertokens.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den Filter **Verzeichnis und Abonnement** und dann Ihr Azure AD B2C-Verzeichnis aus.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie einen **Namen** für den Richtlinienschlüssel ein. Beispiel: `RestApiBearerToken`. Dem Namen Ihres Schlüssels wird automatisch das Präfix `B2C_1A_` hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den geheimen Clientschlüssel ein, den Sie zuvor notiert haben.
1. Wählen Sie für **Schlüsselverwendung** die Option `Encryption` aus.
1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Konfigurieren Ihres technischen REST-API-Profils für die Verwendung des Bearertoken-Richtlinienschlüssels

Konfigurieren Sie nach dem Erstellen des erforderlichen Schlüssels die Metadaten Ihres technischen REST-API-Profils so, dass sie auf das Bearertoken verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `Bearer`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Nachfolgend finden Sie ein Beispiel für ein mit der Bearertokenauthentifizierung konfiguriertes technisches RESTful-Profi:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>Authentifizierung mit API-Schlüssel

Ein API-Schlüssel ist ein eindeutiger Bezeichner, der zum Authentifizieren eines Benutzers für den Zugriff auf einen Rest-API-Endpunkt verwendet wird. Der Schlüssel wird in einem benutzerdefinierten HTTP-Header gesendet. Der [HTTP-Trigger in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) beispielsweise verwendet `x-functions-key` im HTTP-Header, um den Anforderer zu identifizieren.  

### <a name="add-api-key-policy-keys"></a>Hinzufügen von Richtlinienschlüsseln als API-Schlüssel

Um ein technisches REST-API-Profil für die Authentifizierung mit API-Schlüssel zu konfigurieren, müssen Sie den folgenden kryptografischen Schlüssel zum Speichern des API-Schlüssels erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie im Hauptmenü den Filter **Verzeichnis und Abonnement** und dann Ihr Azure AD B2C-Verzeichnis aus.
1. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
1. Wählen Sie auf der Seite „Übersicht“ die Option **Framework für die Identitätsfunktion** aus.
1. Wählen Sie **Richtlinienschlüssel** aus, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie unter **Optionen** den Eintrag **Manuell** aus.
1. Geben Sie **RestApiKey** als **Name** ein.
    Das Präfix *B2C_1A_* wird möglicherweise automatisch hinzugefügt.
1. Geben Sie im Feld **Geheimnis** den REST-API-Schlüssel ein.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Konfigurieren des technischen REST-API-Profils für die Authentifizierung mit API-Schlüssel

Konfigurieren Sie nach dem Erstellen des erforderlichen Schlüssels die Metadaten Ihres technischen REST-API-Profils so, dass sie auf die Anmeldeinformationen verweisen.

1. Öffnen Sie in Ihrem Arbeitsverzeichnis die Erweiterungsrichtliniendatei („TrustFrameworkExtensions.xml“).
1. Suchen Sie das technische REST-API-Profil. Beispiel: `REST-ValidateProfile` oder `REST-GetProfile`.
1. Suchen Sie das `<Metadata>`-Element.
1. Ändern Sie den Authentifizierungstyp (*AuthenticationType*) in `ApiKeyHeader`.
1. Ändern Sie den Wert von *AllowInsecureAuthInProduction* in `false`.
1. Fügen Sie den folgenden XML-Codeausschnitt unmittelbar hinter dem schließenden `</Metadata>`-Element hinzu:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

Die **ID** des kryptografischen Schlüssels definiert den HTTP-Header. In diesem Beispiel wird der API-Schlüssel als **x-functions-key** gesendet.

Nachfolgend finden Sie ein Beispiel für ein technisches RESTful-Profil, das für die Authentifizierung mit API-Schlüssel zum Aufrufen einer Azure-Funktion konfiguriert wurde:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Element für das [technische RESTful-Profil](restful-technical-profile.md) finden Sie in der IEF-Referenz.