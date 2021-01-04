---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit Jumio
titleSuffix: Azure AD B2C
description: In diesem Tutorial konfigurieren Sie Azure Active Directory B2C mit Jumio für automatisierte ID-Überprüfung und Schutz von Kundendaten.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 66ec0d4b09dc983eb898d63d45b3dd7cab291c4c
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928663"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von Jumio mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Integrieren von Azure Active Directory B2C (Azure AD B2C) in [Jumio](https://www.jumio.com/). Jumio ist ein ID-Überprüfungsdienst, der eine automatische ID-Überprüfung in Echtzeit ermöglicht, um den Schutz von Kundendaten zu unterstützen.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die Jumio-Integration umfasst die folgenden Komponenten:

- Azure AD B2C: Der Autorisierungsserver, mit dem die Anmeldeinformationen des Benutzers überprüft werden. Er wird auch als Identitätsanbieter bezeichnet.

- Jumio: Der Dienst, der die vom Benutzer bereitgestellten ID-Details übernimmt und überprüft.

- Zwischen-REST-API: Diese API implementiert die Integration zwischen Azure AD B2C und dem Jumio-Dienst.

- Azure Blob Storage: Der Dienst, der benutzerdefinierte Benutzeroberflächendateien für die Azure AD B2C-Richtlinien bereitstellt.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Diagramm der Architektur einer Azure AD B2C-Integration in Jumio.](./media/partner-jumio/jumio-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Der Benutzer trifft auf einer Seite ein, um sich anzumelden oder sich zu registrieren, um ein Konto zu erstellen. Azure AD B2C erfasst die Benutzerattribute.
| 2. | Azure AD B2C ruft die API der mittleren Ebene auf und übergibt die Benutzerattribute.
| 3. | Die API der mittleren Ebene erfasst die Benutzerattribute und wandelt sie in ein Format um, das von der Jumio-API genutzt werden kann. Anschließend werden die Attribute an Jumio gesendet.
| 4. | Nachdem Jumio die Informationen genutzt und verarbeitet hat, wird das Ergebnis an die API der mittleren Ebene zurückgegeben.
| 5. | Die API der mittleren Ebene verarbeitet die Informationen und sendet relevante Informationen zurück an Azure AD B2C.
| 6. | Azure AD B2C empfängt die Informationen von der API der mittleren Ebene. Bei einer Fehlerantwort wird dem Benutzer eine Fehlermeldung angezeigt. Bei einer Erfolgsantwort wird der Benutzer authentifiziert und in das Verzeichnis geschrieben.

## <a name="sign-up-with-jumio"></a>Registrieren bei Jumio

Zum Erstellen eines Jumio-Kontos wenden Sie sich an [Jumio](https://www.jumio.com/contact/).

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurieren von Azure AD B2C mit Jumio

Nachdem Sie ein Jumio-Konto erstellt haben, verwenden Sie das Konto, um Azure AD B2C zu konfigurieren. In den folgenden Abschnitten wird der Ablauf des Prozesses beschrieben.

### <a name="deploy-the-api"></a>Bereitstellen der API

Stellen Sie den angegebenen [API-Code](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) für einen Azure-Dienst bereit. Sie können den Code gemäß [dieser Anleitung](/visualstudio/deployment/quickstart-deploy-to-azure) aus Visual Studio veröffentlichen.

>[!NOTE]
>Sie benötigen die URL des bereitgestellten Diensts, um Azure AD mit den erforderlichen Einstellungen zu konfigurieren.

### <a name="deploy-the-client-certificate"></a>Bereitstellen des Clientzertifikats

1. Ein Clientzertifikat hilft, den Aufruf der Jumio-API zu schützen. Erstellen Sie mithilfe des folgenden PowerShell-Beispielcodes ein selbstsigniertes Zertifikat:

   ``` PowerShell
   $cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
   $cert.Thumbprint
   $pwdText = "Your password"
   $pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
   Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

   ```

   Das Zertifikat wird dann an den für ``{your-local-path}`` angegebenen Speicherort exportiert.

3. Importieren Sie das Zertifikat gemäß den Anweisungen in [diesem Artikel](../app-service/configure-ssl-certificate.md#upload-a-private-certificate) in Azure App Service.

### <a name="create-a-signingencryption-key"></a>Erstellen eines Signatur-/Verschlüsselungsschlüssels

Erstellen Sie eine zufällige Zeichenfolge mit einer Länge von mehr als 64 Zeichen, die nur Buchstaben und Zahlen enthält.

Beispiel: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Verwenden Sie das folgende PowerShell-Skript, um die Zeichenfolge zu erstellen:

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="configure-the-api"></a>Konfigurieren der API

Sie können [Anwendungseinstellungen in Azure App Service konfigurieren](../app-service/configure-common.md#configure-app-settings). Mit dieser Methode können Sie Einstellungen sicher konfigurieren, ohne sie in ein Repository einzuchecken. Sie müssen die folgenden Einstellungen für die REST-API angeben:

| Anwendungseinstellungen | `Source` | Notizen |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Jumio-Kontokonfiguration |     |
|JumioSettings:AuthPassword | Jumio-Kontokonfiguration |     |
|AppSettings:SigningCertThumbprint|Fingerabdruck des erstellten selbstsignierten Zertifikats|  |
|AppSettings:IdTokenSigningKey| Mithilfe von PowerShell erstellter Signaturschlüssel | |
| AppSettings:IdTokenEncryptionKey |Mithilfe von PowerShell erstellter Verschlüsselungsschlüssel
| AppSettings:IdTokenIssuer | Für das JWT-Token zu verwendender Aussteller (ein GUID-Wert wird bevorzugt) |
| AppSettings:IdTokenAudience  | Für das JWT-Token zu verwendende Zielgruppe (ein GUID-Wert wird bevorzugt) |
|AppSettings:BaseRedirectUrl | Basis-URL der Azure AD B2C-Richtlinie | https://{Ihr Mandantenname}.b2clogin.com/{Ihre Anwendungs-ID}|
| WEBSITE_LOAD_CERTIFICATES| Fingerabdruck des erstellten selbstsignierten Zertifikats |

### <a name="deploy-the-ui"></a>Bereitstellen der Benutzeroberfläche

1. Richten Sie einen [Blob Storage-Container in Ihrem Speicherkonto](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) ein.

2. Speichern Sie die Dateien für die Benutzeroberfläche aus dem [Benutzeroberflächenordner](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) in Ihrem Blobcontainer.

#### <a name="update-ui-files"></a>Aktualisieren von Dateien für die Benutzeroberfläche

1. Wechseln Sie in den Dateien für die Benutzeroberfläche zum Ordner [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue).

2. Öffnen Sie jede HTML-Datei.

3. Suchen und ersetzen Sie `{your-ui-blob-container-url}` durch die URL Ihres Blobcontainers.

4. Suchen und ersetzen Sie `{your-intermediate-api-url}` durch die URL des Zwischen-API-App-Diensts.

>[!NOTE]
> Als bewährte Methode empfehlen wir Ihnen, auf der Seite für die Sammlung der Attribute eine Benachrichtigung zur Einwilligung hinzufügen. Informieren Sie Benutzer darüber, dass die Informationen zur Identitätsüberprüfung an Drittanbieterdienste gesendet werden.

### <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurieren der Azure AD B2C-Richtlinie

1. Wechseln Sie im Richtlinienordner zur [Azure AD B2C-Richtlinie](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies).

2. Folgen Sie den Anweisungen in diesem [Artikel](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack), um das [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) herunterzuladen.

3. Konfigurieren Sie die Richtlinie für den Azure AD B2C-Mandanten.

>[!NOTE]
>Aktualisieren Sie die bereitgestellten Richtlinien, sodass sie sich auf Ihren genauen Mandanten beziehen.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.

2. Wählen Sie den zuvor erstellten Flow **SignUpSignIn** aus.

3. Wählen Sie **Benutzerflow ausführen** aus, und gehen Sie dann wie folgt vor:

   a. Als **Anwendung** wählen Sie die registrierte App aus (im Beispiel JWT).

   b. Als **Antwort-URL** wählen Sie die **Umleitungs-URL** aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsablauf aus, und erstellen Sie ein Konto.

5. Der Jumio-Dienst wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
