---
title: Erstellen und Zusammenführen einer Zertifikatsignieranforderung in Azure Key Vault
description: Erstellen und Zusammenführen einer Zertifikatsignieranforderung in Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 6d66648680aa14baa53372732df52a6c247a0117
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483762"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Erstellen und Zusammenführen einer Zertifikatsignieranforderung in Key Vault

Azure Key Vault unterstützt die Speicherung eines digitalen Zertifikats, das von einer Zertifizierungsstelle Ihrer Wahl ausgestellt wurde, in Ihrem Schlüsseltresor. Hierbei wird die Erstellung der Zertifikatsignieranforderung mit einem Schlüsselpaar aus einem privaten und einem öffentlichen Schlüssel unterstützt, das von einer beliebigen ausgewählten Zertifizierungsstelle signiert sein kann. Hierbei kann es sich um eine interne Zertifizierungsstelle des Unternehmens oder eine externe öffentliche Zertifizierungsstelle handeln. Eine Zertifikatsignieranforderung (auch CSR oder Zertifizierungsanforderung genannt) ist eine Nachricht, die vom Benutzer an eine Zertifizierungsstelle gesendet wird, um die Ausstellung eines digitalen Zertifikats anzufordern.

Weitere allgemeine Informationen zu Zertifikaten finden Sie unter [Azure Key Vault-Zertifikate](./about-certificates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="adding-certificate-in-key-vault-issued-by-partnered-ca"></a>Hinzufügen eines von einer Partnerzertifizierungsstelle ausgestellten Zertifikats in Key Vault
Key Vault ist eine Partnerschaft mit den folgenden zwei Zertifizierungsstellen eingegangen, um die Erstellung von Zertifikaten zu vereinfachen: 

|Anbieter|Zertifikattyp|Konfigurationssetup  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault bietet OV- oder EV-SSL-Zertifikate mit DigiCert| [Integrationsleitfaden](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault bietet OV- oder EV-SSL-Zertifikate mit GlobalSign| [Integrationsleitfaden](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="adding-certificate-in-key-vault-issued-by-non-partnered-ca"></a>Hinzufügen eines Zertifikats einer Zertifizierungsstelle (kein Partner) in Key Vault

Mithilfe der folgenden Schritte können Sie ein Zertifikat mit einer nicht mit Key Vault verbundenen Zertifizierungsstelle erstellen (beispielsweise ist GoDaddy keine vertrauenswürdige Key Vault-Zertifizierungsstelle). 


### <a name="azure-powershell"></a>Azure PowerShell



1. Zunächst **erstellen Sie die Zertifikatrichtlinie**. Das Zertifikat vom Aussteller wird nicht von Key Vault im Namen des Benutzers angemeldet oder erneuert, da die in diesem Szenario ausgewählte Zertifizierungsstelle nicht unterstützt wird und daher der Ausstellername (IssuerName) als unbekannt (Unknown) festgelegt ist.

   ```azurepowershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
    
   > [!NOTE]
   > Wenn Sie einen RDN (Relative Distinguished Name) nutzen, dessen Wert ein Komma (,) enthält, verwenden Sie einfache Anführungszeichen, und setzen Sie den Wert, der das Sonderzeichen enthält, in doppelte Anführungszeichen. Beispiel: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. In diesem Beispiel ist **Docs, Contoso** der Wert für `OU`. Dieses Format funktioniert für alle Werte, die ein Komma enthalten.

2. Erstellen Sie eine **Zertifikatsignieranforderung**.

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Abrufen der **von der Zertifizierungsstelle signierten CSR-Anforderung**: `$csr.CertificateSigningRequest` ist die Base4-codierte Zertifikatsignieranforderung für das Zertifikat. Sie können dieses Blob in die Website für Zertifikatanforderungen des Ausstellers übernehmen. Dieser Schritt ist je nach Zertifizierungsstelle unterschiedlich. Am besten sehen Sie in den Richtlinien der Zertifizierungsstelle nach, wie dieser Schritt auszuführen ist. Sie können auch Tools wie Certreq oder OpenSSL verwenden, um die Zertifikatanforderung signieren zu lassen und den Vorgang zum Generieren eines Zertifikats abzuschließen.


4. **Zusammenführen der signierte Anforderung** in Key Vault: Nachdem die Zertifikatanforderung vom Aussteller signiert wurde, können Sie das signierte Zertifikat zurückführen und mit dem anfänglich in Azure Key Vault erstellten Schlüsselpaar mit einem privaten und einem öffentlichen Schlüssel zusammenführen.

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Die Zertifikatanforderung wurde nun erfolgreich zusammengeführt.

### <a name="azure-portal"></a>Azure-Portal

1.  Um eine Zertifikatsignieranforderung (CSR) für die Zertifizierungsstelle Ihrer Wahl zu generieren, navigieren Sie zu der Key Vault-Instanz, der Sie das Zertifikat hinzufügen möchten.
2.  Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Zertifikate** aus.
3.  Wählen Sie die Registerkarte **Generieren/importieren** aus.
4.  Wählen Sie auf dem Bildschirm **Zertifikat erstellen** folgende Werte aus:
    - **Methode der Zertifikaterstellung:** Generieren.
    - **Zertifikatname:** ContosoManualCSRCertificate.
    - **Typ der Zertifizierungsstelle (ZS):** Zertifikat wurde durch nicht integrierte Zertifizierungsstelle ausgestellt
    - **Antragsteller:** `"CN=www.contosoHRApp.com"`
    - Wählen Sie die anderen Werte nach Wunsch aus. Klicken Sie auf **Erstellen**.

    ![Zertifikateigenschaften](../media/certificates/create-csr-merge-csr/create-certificate.png)  


6.  Sie werden feststellen, dass der Zertifikatliste jetzt ein Zertifikat hinzugefügt ist. Wählen Sie dieses neue Zertifikat aus, das Sie soeben erstellt haben. Der aktuelle Status des Zertifikats ist „Deaktiviert“, da es noch nicht von der Zertifizierungsstelle ausgestellt wurde.
7. Klicken Sie auf die Registerkarte **Zertifikatvorgang**, und wählen Sie **CSR herunterladen** aus.

   ![Screenshot: Hervorgehobene Schaltfläche „CSR herunterladen“](../media/certificates/create-csr-merge-csr/download-csr.png)
 
8.  Übergeben Sie die CSR-Datei an die Zertifizierungsstelle, damit die Anforderung signiert wird.
9.  Nachdem die Anforderung von der Zertifizierungsstelle signiert wurde, führen Sie die Zertifikatsdatei zurück, um die **signierte Anforderung auf demselben Bildschirm „Zertifikatvorgang“ zusammenzuführen**.

Die Zertifikatanforderung wurde nun erfolgreich zusammengeführt.

> [!NOTE]
> Wenn Ihre RDN-Werte Kommas aufweisen, können Sie sie auch im Feld **Antragsteller** hinzufügen, indem Sie den Wert in doppelte Anführungszeichen setzen, wie in Schritt 4 gezeigt.
> Beispieleintrag für „Antragsteller“: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`. In diesem Beispiel enthält der `OU`-Wert des RDN einen Wert mit einem Komma im Namen. Die resultierende Ausgabe für `OU` lautet **Docs, Contoso**.


## <a name="adding-more-information-to-csr"></a>Hinzufügen von weiteren Informationen zur Zertifikatsignieranforderung

Sie können beim Erstellen einer Zertifikatsignieranforderung (CSR) noch weitere Informationen hinzufügen, z. B.: 
    - Land:
    - Ort/Standort:
    - Bundesland/Kanton:
    - Organisation:
    - Organisationseinheit: Sie können diese Informationen beim Erstellen einer Zertifikatsignieranforderung hinzufügen, indem Sie dies in „subjectName“ definieren.

Beispiel
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

> [!NOTE]
> Wenn Sie ein DV-Zertifikat mit diesen Details in der Zertifikatsignieranforderung anfordern, wird diese von der Zertifizierungsstelle ggf. abgelehnt, da die Zertifizierungsstelle möglicherweise nicht alle in der Anforderung enthaltenen Informationen auf ihre Gültigkeit überprüfen kann. Wenn Sie ein OV-Zertifikat anfordern, ist es besser, alle diese Informationen der Zertifikatsignieranforderung hinzuzufügen.


## <a name="troubleshoot"></a>Problembehandlung

- **Fehlertyp „Der öffentliche Schlüssel des Endentitätszertifikats im angegebenen X.509-Zertifikatinhalt stimmt nicht mit dem öffentlichen Teil des angegebenen privaten Schlüssels überein. Überprüfen Sie die Gültigkeit des Zertifikats.“** . Dieser Fehler kann auftreten, wenn Sie die CSR nicht mit derselben initialisierten CSR-Anforderung zusammenführen. Bei jeder CSR-Erstellung wird ein privater Schlüssel erstellt, der beim Zusammenführen der signierten Anforderung abgeglichen werden muss.
    
- Wird beim Zusammenführen einer CSR die gesamte Kette zusammengeführt?
    Ja, die gesamte Kette wird zusammengeführt, vorausgesetzt, der Benutzer hat eine P7B-Datei zum Zusammenführen zurückgegeben.

- Wenn das ausgestellte Zertifikat im Azure-Portal den Status „Deaktiviert“ hat, fahren Sie mit der Anzeige des **Zertifikatvorgangs** fort, um die Fehlermeldung für dieses Zertifikat zu überprüfen.

Weitere Informationen finden Sie unter den [Zertifikatvorgängen in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy).

- **Fehlertyp „Beim angegebenen Antragstellernamen handelt es sich nicht um einen gültigen X.500-Namen“** : Dieser Fehler kann auftreten, wenn die Werte für „SubjectName“ Sonderzeichen enthalten. Weitere Informationen finden Sie jeweils in den Hinweisen in den Anweisungen zum Azure-Portal und zu PowerShell. 

## <a name="next-steps"></a>Nächste Schritte

- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
