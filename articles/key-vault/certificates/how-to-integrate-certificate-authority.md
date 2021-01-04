---
title: Integrieren von Key Vault in die DigiCert-Zertifizierungsstelle
description: Informationen, wie Sie Key Vault in die DigiCert-Zertifizierungsstelle integrieren.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4635bcc51d2ab626b16ddcf02e772bf3df3cad4d
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763739"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Integrieren von Key Vault in die DigiCert-Zertifizierungsstelle

Azure Key Vault ermöglicht das einfache Bereitstellen und Verwalten von digitalen Zertifikaten für Ihr Netzwerk und das Aktivieren der sicheren Kommunikation für Anwendungen. Ein digitales Zertifikat ist eine elektronische Anmeldeinformation, um einen Identitätsnachweis in einer elektronischen Transaktion zu erbringen. 

Azure Key Vault-Benutzer können DigiCert-Zertifikate direkt aus ihrem Key Vault generieren. Key Vault würde die Verwaltung des End-to-End-Zertifikatlebenszyklus für solche Zertifikate sicherstellen, die von DigiCert über die vertrauenswürdige Partnerschaft von Key Vault mit der DigiCert-Zertifizierungsstelle ausgestellt wurden.

Weitere allgemeine Informationen zu Zertifikaten finden Sie unter [Azure Key Vault-Zertifikate](./about-certificates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Leitfaden benötigen Sie die folgenden Ressourcen.
* Einen Schlüsseltresor. Sie können einen vorhandenen Schlüsseltresor verwenden oder einen neuen Schlüsseltresor erstellen, indem Sie die Schritte in einem der folgenden Schnellstarts ausführen:
   - [Erstellen eines Schlüsseltresors mit der Azure CLI](../general/quick-create-cli.md)
   - [Erstellen eines Schlüsseltresors mit Azure PowerShell](../general/quick-create-powershell.md)
   - [Erstellen eines Schlüsseltresors im Azure-Portal](../general/quick-create-portal.md)
*   Sie müssen das DigiCert CertCentral-Konto aktivieren. [Registrieren Sie sich](https://www.digicert.com/account/signup/) für Ihr CertCentral-Konto.
*   Berechtigungen auf Administratorebene in Ihren Konten.


### <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die folgenden Informationen aus Ihrem DigiCert CertCentral-Konto zur Hand haben:
-   CertCentral-Konto-ID
-   Organisations-ID
-   API-Schlüssel

## <a name="adding-certificate-authority-in-key-vault"></a>Hinzufügen einer Zertifizierungsstelle in Key Vault 
Nachdem Sie die Informationen aus dem DigiCert CertCentral-Konto gesammelt haben, können Sie nun DigiCert der Liste der Zertifizierungsstellen im Schlüsseltresor hinzufügen.

### <a name="azure-portal"></a>Azure-Portal

1.  Um die DigiCert-Zertifizierungsstelle hinzuzufügen, navigieren Sie zu dem Schlüsseltresor, dem Sie DigiCert hinzufügen möchten. 
2.  Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Zertifikate** aus.
3.  Wählen Sie die Registerkarte **Zertifizierungsstellen** aus. ![Auswählen von „Zertifizierungsstellen“](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Wählen Sie die Option **Hinzufügen** aus.
 ![Hinzufügen von Zertifizierungsstellen](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Wählen Sie im Bildschirm **Zertifizierungsstelle erstellen** die folgenden Werte aus:
    -   **Name**: Fügen Sie einen identifizierbaren Ausstellernamen hinzu. Beispiel: DigicertCA
    -   **Anbieter**: Wählen Sie DigiCert aus dem Menü aus.
    -   **Account ID (Konto-ID)** : Geben Sie Ihre DigiCert CertCentral-Konto-ID ein.
    -   **Kontokennwort**: Geben Sie den API-Schlüssel ein, den Sie in Ihrem DigiCert CertCentral-Konto erstellt haben.
    -   **Organisations-ID**: Geben Sie die aus dem DigiCert CertCentral-Konto gesammelte OrgID ein. 
    -   Klicken Sie auf **Erstellen**.
   
6.  Sie werden feststellen, dass DigicertCA jetzt in der Liste der Zertifizierungsstellen hinzugefügt ist.


### <a name="azure-powershell"></a>Azure PowerShell

Mit Azure PowerShell können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. Azure hostet Azure Cloud Shell, eine interaktive Shell-Umgebung, die Sie über Ihr Azure-Portal im Browser selbst nutzen können.

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 1.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```azurepowershell-interactive
Login-AzAccount
```

1.  Erstellen Sie eine **Ressourcengruppe**.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Erstellen Sie einen **Schlüsseltresor**.

Sie müssen einen eindeutigen Namen für Ihren Schlüsseltresor verwenden. Hier ist „Contoso-Vaultname“ der Name für den Schlüsseltresor in diesem Handbuch.

- **Tresorname**: Contoso-Vaultname.
- **Ressourcengruppenname:** ContosoResourceGroup
- **Standort**: EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Definieren Sie Variablen für aus dem DigiCert CertCentral-Konto gesammelte Informationen.

- Definieren Sie die Variable **Account ID** (Konto-ID).
- Definieren Sie die Variable **Org-ID** (Organisations-ID).
- Definieren Sie die Variable **API Key** (API-Schlüssel).

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Legen Sie den **Issuer** (Aussteller) fest. Hierdurch wird DigiCert als Zertifizierungsstelle im Schlüsseltresor hinzugefügt. Weitere Informationen zu den Parametern finden Sie [hier](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer).
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Festlegen der Richtlinie für das Zertifikat und Ausstellen eines Zertifikats** von DigiCert direkt im Schlüsseltresor.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

Das Zertifikat wurde nun erfolgreich von Digicert CA innerhalb des angegebenen Schlüsseltresors durch diese Integration ausgestellt.

## <a name="troubleshoot"></a>Problembehandlung

Wenn das ausgestellte Zertifikat im Azure-Portal den Status „Deaktiviert“ hat, fahren Sie mit der Anzeige des **Zertifikatvorgangs** fort, um die DigiCert-Fehlermeldung für dieses Zertifikat zu überprüfen.

 ![Zertifikatvorgang](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Fehlermeldung „Führen Sie eine Zusammenführung aus, um diese Zertifikatanforderung abzuschließen.“
Für die von der Zertifizierungsstelle signierte Zertifikatsignieranforderung muss eine Zusammenführung ausgeführt werden, um diese Anforderung abzuschließen. [Hier](https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request) erhalten Sie weitere Informationen.

Weitere Informationen finden Sie unter den [Zertifikatvorgängen in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

- Kann ich mit Key Vault ein DigiCert-Platzhalterzertifikat generieren? 
   Ja. Dies hängt davon ab, wie Sie Ihr DigiCert-Konto konfiguriert haben.
- Wie kann ich ein **OV-SSL- oder EV-SSL**-Zertifikat mit DigiCert erstellen? 
   Schlüsseltresore unterstützen das Erstellen von OV- und EV-SSL-Zertifikaten. Klicken Sie beim Erstellen eines Zertifikats auf „Erweiterte Richtlinienkonfiguration“, und geben Sie anschließend den Zertifikattyp an. Die folgenden Werte werden unterstützt: OV-SSL, EV-SSL
   
   Sie können diese Art von Zertifikat in Schlüsseltresoren erstellen, wenn Ihr DigiCert-Konto dies zulässt. Bei dieser Art von Zertifikat wird die Überprüfung von DigiCert durchgeführt, und das Supportteam wäre in der Lage, Sie zu unterstützen, wenn die Überprüfung fehlschlägt. Sie können beim Erstellen eines Zertifikats zusätzliche Informationen hinzufügen, indem Sie sie in „subjectName“ definieren.

Beispiel
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Kommt es beim Erstellen eines DigiCert-Zertifikats per Integration gegenüber der direkten Beschaffung des Zertifikats per DigiCert zu einer zeitlichen Verzögerung?
   Nein. Beim Erstellen eines Zertifikats ist es die Verifizierung, die einige Zeit in Anspruch nehmen kann, und diese Verifizierung hängt vom DigiCert-Prozess ab.


## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
