---
title: Azure Disk Encryption-Beispielskripts
description: Dieser Artikel enthält den Anhang zu Microsoft Azure Disk Encryption für virtuelle Windows-Computer.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 255e284cf8d54a9be59f09f5613cb2728417d234
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912037"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption-Beispielskripts 

Dieser Artikel bietet Beispielskripts für die Vorbereitung vorab verschlüsselter VHDs und andere Aufgaben.

> [!NOTE]
> Alle Skripte beziehen sich auf die neueste, Nicht-AAD-Version von ADE, sofern nicht anders angegeben.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>PowerShell-Beispielskripts für Azure Disk Encryption 


- **Auflisten aller verschlüsselten VMs Ihres Abonnements**

  Sie finden alle mit ADE verschlüsselten VMs und die Erweiterungsversion in allen Ressourcengruppen, die in einem Abonnement vorhanden sind, mit [diesem PowerShell-Skript](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Alternativ zeigen diese Cmdlets alle mit ADE verschlüsselten VMs an (jedoch nicht die Erweiterungsversion):

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **Auflisten aller verschlüsselten VMSS-Instanzen in Ihrem Abonnement**
    
    Sie finden alle mit ADE verschlüsselten VMSS-Instanzen und die Erweiterungsversion in allen Ressourcengruppen, die in einem Abonnement vorhanden sind, mit [diesem PowerShell-Skript](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).
 
- **Auflisten aller Geheimnisse der Datenträgerverschlüsselung, die zum Verschlüsseln von VMs in einem Schlüsseltresor verwendet werden**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Verwenden des PowerShell-Skripts für die Voraussetzungen für Azure Disk Encryption

Wenn Sie bereits mit den Voraussetzungen für Azure Disk Encryption vertraut sind, können Sie das [PowerShell-Skript zur Überprüfung der Azure Disk Encryption-Voraussetzungen](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ) verwenden. Ein Beispiel für die Verwendung dieses PowerShell-Skripts finden Sie im [Schnellstart: Verschlüsseln einer VM](disk-encryption-powershell-quickstart.md). Sie können die Kommentare aus einem Abschnitt des Skripts, beginnend ab Zeile 211, entfernen, um alle Datenträger für vorhandene virtuelle Computer in einer vorhandenen Ressourcengruppe zu verschlüsseln. 

Die folgende Tabelle zeigt, welche Parameter im PowerShell-Skript verwendet werden können: 

|Parameter|BESCHREIBUNG|Erforderlich?|
|------|------|------|
|$resourceGroupName| Name der Ressourcengruppe, zu der der Schlüsseltresor gehört.  Sofern noch nicht vorhanden, wird eine neue Ressourcengruppe mit diesem Namen erstellt.| True|
|$keyVaultName|Name des Schlüsseltresors, in dem Verschlüsselungsschlüssel platziert werden sollen. Sofern noch nicht vorhanden, wird ein neuer Tresor mit diesem Namen erstellt.| True|
|$location|Standort des Schlüsseltresors. Der Schlüsseltresor und die zu verschlüsselnden virtuellen Computer müssen sich am gleichen Standort befinden. Mit `Get-AzLocation` können Sie eine Standortliste abrufen.|True|
|$subscriptionId|Bezeichner des zu verwendenden Azure-Abonnements.  Die Abonnement-ID kann mit `Get-AzSubscription` abgerufen werden.|True|
|$aadAppName|Name der Azure AD-Anwendung, die zum Schreiben von Geheimnissen in den Schlüsseltresor verwendet wird. Sofern noch nicht vorhanden, wird eine neue Anwendung mit diesem Namen erstellt. Wenn diese App bereits vorhanden ist, übergeben Sie den Parameter aadClientSecret an das Skript.|False|
|$aadClientSecret|Clientgeheimnis der Azure AD-Anwendung, die zuvor erstellt wurde.|False|
|$keyEncryptionKeyName|Name des optionalen Schlüssels für die Schlüsselverschlüsselung in Key Vault. Sofern noch nicht vorhanden, wird ein neuer Schlüssel mit diesem Namen erstellt.|False|

## <a name="resource-manager-templates"></a>Resource Manager-Vorlagen

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Ver- oder Entschlüsseln von virtuellen Computer ohne eine Azure AD-App

- [Enable disk encryption on an existing or running Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad) (Aktivieren der Datenträgerverschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Windows-Computer)  
- [Disable encryption on a running Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) (Deaktivieren der Verschlüsselung auf einem ausgeführten virtuellen Windows-Computer) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Ver- oder Entschlüsseln von virtuellen Computer mit einer Azure AD-App (früheres Release) 
 
- [Enable disk encryption on an existing or running Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) (Aktivieren der Datenträgerverschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Windows-Computer)    
- [Disable encryption on a running Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) (Deaktivieren der Verschlüsselung auf einem ausgeführten virtuellen Windows-Computer) 
- [Create a new encrypted managed disk from a pre-encrypted VHD/storage blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk) (Erstellen eines neuen verschlüsselten verwalteten Datenträgers auf der Grundlage einer vorverschlüsselten VHD/eines Speicherblobs)
    - Erstellt einen neuen verschlüsselten verwalteten Datenträger, wenn eine vorverschlüsselte VHD und die entsprechenden Verschlüsselungseinstellungen vorhanden sind

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Vorbereiten einer vorverschlüsselten Windows-VHD
Die folgenden Abschnitte sind erforderlich, um eine vorverschlüsselte Windows-VHD für die Bereitstellung als verschlüsselte VHD in Azure IaaS vorzubereiten. Verwenden Sie die Informationen, um einen neuen virtuellen Windows-Computers (VHD) in Azure Site Recovery oder Azure vorzubereiten und zu starten. Weitere Informationen zum Vorbereiten und Hochladen einer VHD finden Sie unter [Hochladen einer generalisierten VHD und Verwendung dieser zum Erstellen neuer VMs in Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualisieren der Gruppenrichtlinie, um Schutz ohne TPM für das Betriebssystem zu ermöglichen
Sie müssen die BitLocker-Gruppenrichtlinieneinstellung mit dem Namen **BitLocker-Laufwerkverschlüsselung** konfigurieren, die sich unter **Lokale Computerrichtlinie** > **Computerkonfiguration** > **Administrative Vorlagen** > **Windows-Komponenten** befindet. Ändern Sie diese Einstellung wie folgt: **Betriebssystemlaufwerke** > **Zusätzliche Authentifizierung beim Start anfordern** > **BitLocker ohne kompatibles TPM zulassen**. Dies wird in der folgenden Abbildung dargestellt:

![Microsoft-Antischadsoftware in Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Installieren der Komponenten der BitLocker-Funktion
Verwenden Sie für Windows Server 2012 und höher den folgenden Befehl:

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Verwenden Sie für Windows Server 2008 R2 den folgenden Befehl:

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Vorbereiten des Betriebssystemvolumes für BitLocker mit `bdehdcfg`
Führen Sie den Befehl [bdehdcfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) aus, falls erforderlich, um die Betriebssystempartition zu komprimieren und den Computer für BitLocker vorzubereiten:

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Schützen des Betriebssystemvolumes mit BitLocker
Verwenden Sie den Befehl [`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)), um die Verschlüsselung auf dem Startvolume mit einer externen Schlüsselschutzvorrichtung zu aktivieren. Platzieren Sie außerdem den externen Schlüssel (BEK-Datei) auf dem externen Laufwerk oder Volume. Die Verschlüsselung wird nach dem nächsten Neustart auf dem System-/Startvolume aktiviert.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> Bereiten Sie den virtuellen Computer mit einer separaten Daten-/Ressourcen-VHD zum Abrufen des externen Schlüssels mit BitLocker vor.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Hochladen einer verschlüsselten VHD in ein Azure-Speicherkonto
Nachdem die DM-Crypt-Verschlüsselung aktiviert wurde, muss die lokale verschlüsselte VHD in Ihr Speicherkonto hochgeladen werden.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Hochladen des Geheimnisses für den vorab verschlüsselten virtuellen Computer in Ihren Schlüsseltresor
Das zuvor abgerufene Geheimnis der Datenträgerverschlüsselung muss als Geheimnis in den Schlüsseltresor hochgeladen werden.  Hierfür müssen dem Konto, mit dem die geheimen Schlüssel hochgeladen werden sollen, die Berechtigung zum Festlegen von geheimen Schlüsseln und die wrapkey-Berechtigung erteilt werden.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Geheimnis der Datenträgerverschlüsselung ohne Verschlüsselung per KEK
Verwenden Sie [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret), um das Geheimnis im Schlüsseltresor einzurichten. Die Passphrase wird als Base64-Zeichenfolge codiert und dann in den Schlüsseltresor hochgeladen. Stellen Sie außerdem sicher, dass die folgenden Tags festgelegt sind, während das Geheimnis im Schlüsseltresor erstellt wird.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Verwenden Sie im nächsten Schritt `$secretUrl`, um [den Betriebssystemdatenträger ohne KEK anzufügen](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Geheimnis der Datenträgerverschlüsselung mit Verschlüsselung per KEK
Das Geheimnis kann optional mit einem KEK verschlüsselt werden, bevor er in den Schlüsseltresor hochgeladen wird. Verwenden Sie die Wrapper-[API](/rest/api/keyvault/wrapkey), um das Geheimnis zuerst mit dem KEK zu verschlüsseln. Die Ausgabe dieses Wrapper-Vorgangs ist eine Base64-codierte URL-Zeichenfolge, die dann als Geheimnis mit dem [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret)-Cmdlet hochgeladen wird.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Verwenden Sie im nächsten Schritt `$KeyEncryptionKey` und `$secretUrl`, um den [ Betriebssystemdatenträger mit KEK](#using-a-kek) anzufügen.

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Angeben einer Geheimnis-URL beim Anfügen eines Betriebssystemdatenträgers

###  <a name="without-using-a-kek"></a>Ohne KEK
Beim Anfügen des Betriebssystemdatenträgers muss `$secretUrl` übergeben werden. Die URL wurde im Abschnitt „Geheimnis der Datenträgerverschlüsselung ohne Verschlüsselung per KEK“ generiert.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Mit KEK
Beim Anfügen des Betriebssystemdatenträgers müssen `$KeyEncryptionKey` und `$secretUrl` übergeben werden. Die URL wurde im Abschnitt „Geheimnis der Datenträgerverschlüsselung mit Verschlüsselung per KEK“ generiert.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
