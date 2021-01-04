---
title: 'Tutorial: Aktualisieren der Frequenz der automatischen Zertifikatrotation in Key Vault | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie die Frequenz der automatischen Zertifikatrotation in Azure Key Vault über das Azure-Portal aktualisieren.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 591d71e8cd6af2801540f5a1a41ad88b1f538e81
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844487"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Tutorial: Konfigurieren der automatischen Zertifikatrotation in Key Vault

Mit Azure Key Vault können Sie mühelos digitale Zertifikate bereitstellen und verwalten. Bei den Zertifikaten kann es sich um öffentliche und private SSL-/TLS-Zertifikate (Secure Sockets Layer/Transport Layer Security), die von einer Zertifizierungsstelle signiert wurden, oder um ein selbst signiertes Zertifikat handeln. Darüber hinaus kann Key Vault über Partnerschaften mit Zertifizierungsstellen Zertifikate anfordern und verlängern und bietet somit eine zuverlässige Lösung für die Verwaltung des Zertifikatlebenszyklus. In diesem Tutorial aktualisieren Sie die Gültigkeitsdauer, die Frequenz der automatischen Rotation und die Zertifizierungsstellenattribute eines Zertifikats.

Das Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Verwalten eines Zertifikats über das Azure-Portal
> * Hinzufügen des Kontos eines Zertifizierungsstellenanbieters
> * Aktualisieren der Gültigkeitsdauer eines Zertifikats
> * Aktualisieren der Frequenz der automatischen Zertifikatrotation
> * Aktualisieren der Zertifikatattribute mithilfe von Azure PowerShell

Machen Sie sich zunächst mit den [grundlegenden Konzepten von Key Vault](../general/basic-concepts.md) vertraut.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-vault"></a>Erstellen eines Tresors

Sie können eine Azure Key Vault-Instanz im [Azure-Portal](../general/quick-create-portal.md), mit der [Azure-Befehlszeilenschnittstelle](../general/quick-create-cli.md) oder mit [Azure PowerShell](../general/quick-create-powershell.md) erstellen. In diesem Beispiel heißt der Schlüsseltresor **Example-Vault**.

![Ausgabe nach Erstellung des Schlüsseltresors](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Erstellen eines Zertifikats in Key Vault

Erstellen Sie ein Zertifikat, oder importieren Sie ein Zertifikat in den Schlüsseltresor. (Die Schritte zum Erstellen eines Zertifikats in Key Vault finden Sie [hier](../secrets/quick-create-portal.md).) In diesem Beispiel wird das Zertifikat **ExampleCertificate** verwendet.

## <a name="update-certificate-lifecycle-attributes"></a>Aktualisieren der Attribute des Zertifikatlebenszyklus

In Azure Key Vault können Sie die Attribute des Zertifikatlebenszyklus sowohl vor als auch nach der Erstellung des Zertifikats aktualisieren.

Bei einem in Key Vault erstellten Zertifikat kann es sich um Folgendes handeln:

- Ein selbstsigniertes Zertifikat.
- Ein Zertifikat, das mit einer Zertifizierungsstelle (Certificate Authority, CA) erstellt wurde, die über eine Key Vault-Partnerschaft verfügt
- Ein Zertifikat mit einer Zertifizierungsstelle, die nicht über eine Key Vault-Partnerschaft verfügt

Die folgenden Zertifizierungsstellen sind derzeit Key Vault-Partneranbieter:

- DigiCert: Key Vault bietet OV-TLS-/SSL-Zertifikate.
- GlobalSign: Key Vault bietet OV-TLS-/SSL-Zertifikate.

Zertifikate werden von Key Vault durch bewährte Partnerschaften mit Zertifizierungsstellen automatisch rotiert. Da Key Vault Zertifikate über die Partnerschaft automatisch anfordert und verlängert, gilt die Funktion für die automatische Rotation nicht für Zertifikate, die mit Zertifizierungsstellen erstellt wurden, für die keine Partnerschaft mit Key Vault besteht.

> [!NOTE]
> Ein Kontoadministrator für einen Zertifizierungsstellenanbieter erstellt Anmeldeinformationen, die von Key Vault verwendet werden, um TSL-/SSL-Zertifikate zu erstellen, zu verlängern und zu verwenden.
![Zertifizierungsstelle](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Aktualisieren der Attribute des Zertifikatlebenszyklus zum Zeitpunkt der Erstellung

1. Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Zertifikate** aus.
1. Wählen Sie die Option **Generieren/Importieren** aus.
1. Aktualisieren Sie auf dem Bildschirm **Zertifikat erstellen** die folgenden Werte:

   - **Gültigkeitsdauer**: Geben Sie den Wert in Monaten ein. Die Erstellung kurzlebiger Zertifikate ist eine empfohlene Sicherheitsmethode. Der Standardwert für die Gültigkeitsdauer eines neu erstellten Zertifikats beträgt zwölf Monate.
   - **Lebensdauer-Aktionstyp**: Wählen Sie die Aktion für die automatische Verlängerung des Zertifikats bzw. für die Benachrichtigung aus, und aktualisieren Sie dann den Wert für **Prozentsatz der Lebensdauer** oder **Anzahl von Tagen vor Ablauf**. Die automatische Verlängerung eines Zertifikats ist standardmäßig auf 80 Prozent der Lebensdauer festgelegt. Wählen Sie eine der folgenden Optionen aus der Dropdownliste aus:

        |  Bei Erreichen des vorgegebenen Prozentsatzes der Lebensdauer automatisch erneuern| Bei Erreichen des vorgegebenen Prozentsatzes der Lebensdauer eine E-Mail an alle Kontakte senden |
        |-----------|------|
        |Wenn Sie diese Option auswählen, wird die automatische Rotation *aktiviert*. | Wenn Sie diese Option auswählen, werden nur die Kontakte benachrichtigt. Die automatische Rotation wird dadurch *nicht* aktiviert.|

1. Klicken Sie auf **Erstellen**.

![Zertifikatlebenszyklus](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Aktualisieren der Lebenszyklusattribute eines gespeicherten Zertifikats

1. Wählen Sie den Schlüsseltresor aus.
1. Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Zertifikate** aus.
1. Wählen Sie das Zertifikat aus, das Sie aktualisieren möchten. In diesem Beispiel wird das Zertifikat **ExampleCertificate** verwendet.
1. Wählen Sie auf der oberen Menüleiste die Option **Ausstellungsrichtlinie** aus.

   ![Screenshot: Hervorgehobene Schaltfläche „Ausstellungsrichtlinie“](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Aktualisieren Sie im Bildschirm **Ausstellungsrichtlinie** die folgenden Werte:

   - **Gültigkeitsdauer**: Aktualisieren Sie den Wert (in Monaten).
   - **Lebensdauer-Aktionstyp**: Wählen Sie die Aktion für die automatische Verlängerung des Zertifikats bzw. für die Benachrichtigung aus, und aktualisieren Sie dann den Wert für **Prozentsatz der Lebensdauer** oder **Anzahl von Tagen vor Ablauf**.

   ![Zertifikateigenschaften](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Wählen Sie **Speichern** aus.

> [!IMPORTANT]
> Wenn Sie den Lebensdauer-Aktionstyp eines Zertifikats ändern, werden Änderungen für die vorhandenen Zertifikate umgehend erfasst.


### <a name="update-certificate-attributes-by-using-powershell"></a>Aktualisieren der Zertifikatattribute mithilfe von PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Wenn Sie die Verlängerungsrichtlinie für eine Liste von Zertifikaten ändern möchten, verwenden Sie `File.csv` mit `VaultName,CertName` als Eingabe wie im folgenden Beispiel:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Weitere Informationen zu den Parametern finden Sie unter [az keyvault certificate](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Weitere Key Vault-Tutorials bauen auf diesem Tutorial auf. Wenn Sie diese Tutorials verwenden möchten, sollten Sie die vorhandenen Ressourcen behalten.
Wenn Sie sie nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden der Schlüsseltresor und die dazugehörigen Ressourcen gelöscht.

So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das **Suchfeld** am oberen Rand des Portals ein. Wählen Sie in den Suchergebnissen die Ressourcengruppe aus dieser Schnellstartanleitung aus.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und wählen Sie anschließend **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Lebenszyklusattribute eines Zertifikats aktualisiert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- Informieren Sie sich ausführlicher über die [Verwaltung der Zertifikaterstellung in Azure Key Vault](./create-certificate-scenarios.md).
- Lesen Sie die [Übersicht über Key Vault](../general/overview.md).
