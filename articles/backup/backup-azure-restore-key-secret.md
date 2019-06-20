---
title: Wiederherstellen von Key Vault-Schlüssel und -Geheimschlüssel für verschlüsselte virtuelle Computer mithilfe von Azure Backup
description: Informationen zum Wiederherstellen von Key Vault-Schlüssel und -Geheimschlüssel in Azure Backup mithilfe von PowerShell
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geetha
ms.openlocfilehash: 13eb800cd64e0de736b1fdea308a03d8a8d0f046
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127901"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Wiederherstellen von Key Vault-Schlüssel und -Geheimschlüssel für verschlüsselte virtuelle Computer mithilfe von Azure Backup

Dieser Artikel befasst sich mit Azure VM Backup für die Wiederherstellung von verschlüsselten Azure-VMs, wenn Schlüssel und Geheimschlüssel nicht im Schlüsseltresor vorhanden sind. Diese Schritte können auch verwendet werden, wenn Sie eine separate Kopie von Schlüssel (Key Encryption Key) und Geheimschlüssel (BitLocker-Verschlüsselungsschlüssel) für den wiederhergestellten virtuellen Computer verwalten möchten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

* **Sicherung verschlüsselter VMs** – Verschlüsselte Azure-VMs wurden mithilfe von Azure Backup gesichert. Finden Sie im Artikel [Sichern und Wiederherstellen von Azure-VMs mithilfe von PowerShell](backup-azure-vms-automation.md) ausführliche Informationen zur Sicherung verschlüsselter Azure-VMs.
* **Konfigurieren von Azure Key Vault** – Stellen Sie sicher, dass der Schlüsseltresor, für den Schlüssel und Geheimschlüssel wiederhergestellt werden müssen, bereits vorhanden ist. Finden Sie im Artikel [Erste Schritte mit Azure Key Vault](../key-vault/key-vault-get-started.md) ausführliche Informationen zum Verwalten des Schlüsseltresors.
* **Wiederherstellen von Datenträgern** – Stellen Sie sicher, dass Sie gemäß den Anweisungen unter [Schritte zu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) die Wiederherstellung von Aufträgen ausgelöst haben, um Datenträger für verschlüsselte virtuelle Computer wiederherzustellen. Dies ist notwendig, da bei diesem Auftrag eine JSON-Datei in Ihrem Speicherkonto generiert wird, die Schlüssel und Geheimnisse für die verschlüsselte wiederherzustellende VM enthält.

## <a name="get-key-and-secret-from-azure-backup"></a>Abrufen von Schlüsseln und Geheimnissen aus Azure Backup

> [!NOTE]
> Nachdem der Datenträger für den verschlüsselten virtuellen Computer wiederhergestellt wurde, überprüfen Sie Folgendes:
> * „$details“ ist mit Auftragsdetails zur Wiederherstellung von Datenträgern aufgefüllt, wie unter [Schritte zu PowerShell im Abschnitt zur Wiederherstellung von Datenträgern](backup-azure-vms-automation.md#restore-an-azure-vm) beschrieben wird.
> * Die VM sollte erst über wiederhergestellte Datenträger erstellt werden, **wenn der Schlüssel und das Geheimnis im Schlüsseltresor wiederhergestellt wurden**.

Fragen Sie die Eigenschaften des wiederhergestellten Datenträgers für die Auftragsdetails ab.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Legen Sie den Kontext für Azure Storage fest, und stellen Sie die JSON-Konfigurationsdatei wieder her, die Details zu Schlüsseln und Geheimnissen für verschlüsselte VMs enthält.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Schlüssel wiederherstellen

Nachdem die JSON-Datei unter dem oben genannten Zielpfad generiert wurde, generieren Sie die Schlüsselblobdatei aus der JSON-Datei. Geben Sie sie dann in das Cmdlet zum Wiederherstellen von Schlüsseln ein, um den Schlüssel (KEK) wieder im Schlüsseltresor zu platzieren.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Geheimen Schlüssel wiederherstellen

Rufen Sie mithilfe der zuvor generierten JSON-Datei den Geheimnisnamen und -wert ab, und fügen Sie sie in das Cmdlet zum Festlegen von Geheimnissen ein, um das Geheimnis (BEK) wieder im Schlüsseltresor zu platzieren. Verwenden Sie diese Cmdlets, wenn Ihr **virtueller Computer mit BEK und KEK verschlüsselt ist**.

Verwenden Sie diese Cmdlets, wenn Ihr **virtueller Windows-Computer mit BEK und KEK verschlüsselt ist**.

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Verwenden Sie diese Cmdlets, wenn Ihr **virtueller Linux-Computer mit BEK und KEK verschlüsselt ist**.

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Rufen Sie mithilfe der zuvor generierten JSON-Datei den Geheimnisnamen und -wert ab, und fügen Sie sie in das Cmdlet zum Festlegen von Geheimnissen ein, um das Geheimnis (BEK) wieder im Schlüsseltresor zu platzieren. Verwenden Sie diese Cmdlets, wenn Ihr **virtueller Computer nur mit BEK verschlüsselt ist**.

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * Der Wert für $secretname kann über die Ausgabe von $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl und den Text nach secrets/ abgerufen werden, z.B. lautet die Geheimnis-URL der Ausgabe https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 und der Geheimnisname „B3284AAA-DAAA-4AAA-B393-60CAA848AAAA“.
> * Der Wert der Markierung „DiskEncryptionKeyFileName“ ist mit dem Namen des Geheimnisses identisch.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Erstellen eines virtuellen Computers über einen wiederhergestellten Datenträger

Wenn Sie verschlüsselte virtuelle Computer mit der Azure-VM-Sicherung gesichert haben, unterstützen die oben aufgeführten PowerShell-Cmdlets Sie beim Wiederherstellen des Schlüssels und Geheimnisses im Schlüsseltresor. Nachdem Sie diese wiederhergestellt haben, lesen Sie den Artikel [Verwalten von Sicherungen und Wiederherstellungen von Azure-VMs mithilfe von PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Dieser enthält ausführliche Informationen zum Erstellen verschlüsselter virtueller Computer über wiederhergestellte Datenträger, Schlüssel und Geheimnisse.

## <a name="legacy-approach"></a>Legacy-Methode

Der oben genannte Ansatz kann für alle Wiederherstellungspunkte verwendet werden. Jedoch wäre für Wiederherstellungspunkte, die vor dem 11. Juli 2017 erstellt wurden, und mit BEK und KEK verschlüsselte virtuelle Computer der ältere Ansatz zum Abrufen von Informationen zu Schlüsseln und Geheimnissen über Wiederherstellungspunkte gültig. Nachdem der Auftrag zur Wiederherstellung von Datenträgern für die verschlüsselte VM mithilfe der Anweisungen unter [Schritte zu PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm) abgeschlossen wurde, stellen Sie sicher, dass „$rp“ mit einem gültigen Wert aufgefüllt ist.

### <a name="restore-key"></a>Schlüssel wiederherstellen

Rufen Sie mithilfe der folgenden Cmdlets Informationen über Schlüssel (KEK) vom Wiederherstellungspunkt ab, und fügen Sie sie in das Cmdlet zum Wiederherstellen von Schlüsseln ein, um ihn wieder in den Schlüsseltresor zu platzieren.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Geheimen Schlüssel wiederherstellen

Rufen Sie mithilfe der folgenden Cmdlets Informationen über Geheimnisse (BEK) vom Wiederherstellungspunkt ab, und fügen Sie sie in das Cmdlet zum Festlegen von Geheimnissen ein, um es wieder im Schlüsseltresor zu platzieren.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * Der Wert für $secretname kann über die Ausgabe von $rp1.KeyAndSecretDetails.SecretUrl und den Text nach secrets/ abgerufen werden, z.B. lautet die Geheimnis-URL der Ausgabe https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 und der Geheimnisname „B3284AAA-DAAA-4AAA-B393-60CAA848AAAA“.
> * Der Wert der Markierung „DiskEncryptionKeyFileName“ ist mit dem Namen des Geheimnisses identisch.
> * Der Wert für „DiskEncryptionKeyEncryptionKeyURL“ kann mithilfe des Cmdlet [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) aus dem Schlüsseltresor abgerufen werden, nachdem die Schlüssel wiederhergestellt wurden.
>
>

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie den Schlüssel und das Geheimnis wieder im Schlüsseltresor wiederhergestellt haben, lesen Sie den Artikel [Verwalten von Sicherungen und Wiederherstellungen von Azure-VMs mithilfe von PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). Dieser enthält ausführliche Informationen zum Erstellen verschlüsselter VMs über wiederhergestellte Datenträger, Schlüssel und Geheimnisse.
