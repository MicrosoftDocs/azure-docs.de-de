---
title: Azure Disk Encryption mit Azure AD für virtuelle Windows-Computer (vorheriges Release)
description: Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für virtuelle Windows-IaaS-Computer.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 352c8848b98bfb463c03ceea89ebe3f4b6ad6d5b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742423"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Azure Disk Encryption mit Azure AD für virtuelle Windows-Computer (vorheriges Release)

**Beim neuen Release von Azure Disk Encryption muss kein Azure AD-Anwendungsparameter mehr angegeben werden, um die VM-Datenträgerverschlüsselung zu aktivieren. Sie müssen beim neuen Release während des Schritts zum Aktivieren der Verschlüsselung keine Azure AD-Anmeldeinformationen mehr angeben. Alle neuen virtuellen Computer müssen mit dem neuen Release und ohne die Azure AD-Anwendungsparameter verschlüsselt werden. Eine Anleitung zum Aktivieren der VM-Datenträgerverschlüsselung mit dem neuen Release finden Sie unter [Azure Disk Encryption für virtuelle Windows-Computer](disk-encryption-windows.md). Virtuelle Computer, die bereits mit Azure AD-Anwendungsparametern verschlüsselt wurden, werden weiterhin unterstützt und sollten weiterhin mit der AAD-Syntax gepflegt werden.**


Es gibt viele Szenarien für die Aktivierung der Datenträgerverschlüsselung. Die Schritte können je nach Szenario variieren. In den folgenden Abschnitten werden diese Szenarien für virtuelle Windows-IaaS-Computer ausführlicher beschrieben. Bevor Sie die Datenträgerverschlüsselung verwenden können, müssen die [Voraussetzungen für Azure Disk Encryption](disk-encryption-overview-aad.md) erfüllt sein. 


>[!IMPORTANT]
> - In allen Fällen sollten Sie [eine Momentaufnahme](snapshot-copy-managed-disk.md) oder eine Sicherung erstellen, bevor Datenträger verschlüsselt werden. Durch Sicherungen wird sichergestellt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Für VMs mit verwalteten Datenträgern ist eine Sicherung erforderlich, bevor die Verschlüsselung durchgeführt wird. Nach dem Erstellen einer Sicherung können Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) verwenden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Weitere Informationen zum Sichern und Wiederherstellen von verschlüsselten VMs finden Sie unter [Sichern und Wiederherstellen eines verschlüsselten virtuellen Azure-Computers](../../backup/backup-azure-vms-encryption.md). 
>
> - Beim Verschlüsseln oder Deaktivieren der Verschlüsselung kann eine VM neu gestartet werden.

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Aktivieren der Verschlüsselung auf neuen IaaS-VMs, die über den Marketplace erstellt wurden.
Die Datenträgerverschlüsselung kann auf neuen virtuellen IaaS-Windows-VMs aus dem Azure Marketplace aktiviert werden, indem eine Resource Manager-Vorlage genutzt wird. Mit der Vorlage wird ein neuer verschlüsselter virtueller Windows-Computer erstellt, indem das Windows Server 2012-Katalogimage verwendet wird.

1. Klicken Sie in der [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) auf **Deploy to Azure** (In Azure bereitstellen).

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Ressourcengruppenstandort, die Parameter, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Kaufen** , um einen neuen virtuellen IaaS-Computer bereitzustellen, für den die Verschlüsselung aktiviert ist.

3. Nachdem Sie die Vorlage bereitgestellt haben, können Sie den Verschlüsselungsstatus der VM überprüfen, indem Sie Ihre bevorzugte Methode verwenden:
     - Führen Sie die Überprüfung mit der Azure CLI durch, indem Sie den Befehl [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) verwenden. 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Führen Sie die Überprüfung mit Azure PowerShell durch, indem Sie das Cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) verwenden. 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  Wählen Sie die VM aus, und klicken Sie dann unter der Überschrift **Einstellungen** auf **Datenträger** , um den Verschlüsselungsstatus im Portal zu überprüfen. Im Diagramm unter **Verschlüsselung** wird angezeigt, ob die Verschlüsselung aktiviert ist. 
           ![Azure-Portal – Datenträgerverschlüsselung aktiviert](../media/disk-encryption/disk-encryption-fig2.png)

In der folgenden Tabelle sind die Parameter der Resource Manager-Vorlage für das Szenario neuer virtuelle Computer aus dem Marketplace mit Azure AD-Client-ID aufgeführt:

| Parameter | BESCHREIBUNG | 
| --- | --- |
| adminUserName | Administratorbenutzername für den virtuellen Computer. |
| adminPassword | Administratorkennwort für den virtuellen Computer. |
| newStorageAccountName | Name des Speicherkontos zum Speichern von Betriebssystem- und Daten-VHDs. |
| vmSize | Die Größe des virtuellen Computers. Derzeit werden nur die standardmäßigen A-, D- und G-Serien unterstützt. |
| virtualNetworkName | Der Name des VNET, dem die VM-NIC angehören soll. |
| subnetName | Name des Subnetzes im VNET, dem die VM-NIC angehören soll. |
| AADClientID | Client-ID der Azure AD-Anwendung, die über Berechtigungen zum Schreiben von Geheimnissen in den Schlüsseltresor verfügt. |
| AADClientSecret | Clientgeheimnis der Azure AD-Anwendung, die über Berechtigungen zum Schreiben von Geheimnissen in den Schlüsseltresor verfügt. |
| keyVaultURL | Die URL des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden soll. Sie können sie abrufen, indem Sie das Cmdlet `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` oder die Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` verwenden. |
| keyEncryptionKeyURL | Die URL des KEK, der zum Verschlüsseln des generierten BitLocker-Schlüssels verwendet wird (optional). </br> </br>„KeyEncryptionKeyURL“ ist ein optionaler Parameter. Sie können Ihren eigenen KEK nutzen, um den Schutz für den Datenverschlüsselungsschlüssel (Passphrasegeheimnis) im Schlüsseltresor zu erhöhen. |
| keyVaultResourceGroup | Die Ressourcengruppe des Schlüsseltresors. |
| vmName | Der Name des virtuellen Computers, auf dem der Verschlüsselungsvorgang durchgeführt wird. |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten virtuellen Windows-IaaS-Computern
In diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie eine Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. In den folgenden Abschnitten wird ausführlicher erläutert, wie Sie Azure Disk Encryption aktivieren. 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten VMs mit Azure PowerShell 
Verwenden Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension), um die Verschlüsselung auf einem ausgeführten virtuellen IaaS-Computer in Azure zu aktivieren. Informationen dazu, wie die Verschlüsselung mit Azure Disk Encryption über PowerShell-Cmdlets aktiviert wird, finden Sie in den Blogbeiträgen [Explore Azure Disk Encryption with Azure PowerShell - Part 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell) (Erkunden von Azure Disk Encryption mit Azure PowerShell – Teil 1) und [Explore Azure Disk Encryption with Azure PowerShell - Part 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2) (Erkunden von Azure Disk Encryption mit Azure PowerShell – Teil 2).

-  **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, die VM, der Schlüsseltresor, die AAD-App und der geheime Clientschlüssel sollten bereits beim Erfüllen der Voraussetzungen erstellt worden sein. Ersetzen Sie „MyKeyVaultResourceGroup“, „MyVirtualMachineResourceGroup“, „MySecureVM“, „MySecureVault“, „My-AAD-client-ID“ und „My-AAD-client-secret“ durch Ihre Werte.
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:** Für Azure Disk Encryption können Sie einen vorhandenen Schlüssel in Ihrem Schlüsseltresor angeben, um Geheimnisse für die Datenträgerverschlüsselung zu umschließen, die beim Aktivieren der Verschlüsselung generiert wurden. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus), um den Verschlüsselungsstatus eines virtuellen IaaS-Computers zu überprüfen. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Deaktivieren der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a> Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten VMs mit der Azure CLI
Verwenden Sie den Befehl [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), um die Verschlüsselung auf einem ausgeführten virtuellen IaaS-Computer in Azure zu aktivieren.

- **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie den Befehl [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show), um den Verschlüsselungsstatus eines virtuellen IaaS-Computers zu überprüfen. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Deaktivieren der Verschlüsselung:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Verwenden der Resource Manager-Vorlage
Sie können die Datenträgerverschlüsselung auf vorhandenen oder ausgeführten virtuellen IaaS-Windows-VMs in Azure aktivieren, indem Sie die [Resource Manager-Vorlage zum Verschlüsseln eines ausgeführten virtuellen Windows-Computers](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) verwenden.


1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Ressourcengruppenstandort, die Parameter, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Kaufen** , um die Verschlüsselung auf dem vorhandenen oder ausgeführten virtuellen IaaS-Computer zu aktivieren.

In der folgenden Tabelle werden die Parameter der Resource Manager-Vorlage für vorhandene oder ausgeführte VMs aufgeführt, die eine Azure AD-Client-ID verwenden:

| Parameter | BESCHREIBUNG |
| --- | --- |
| AADClientID | Client-ID der Azure AD-Anwendung, die über Berechtigungen zum Schreiben von Geheimnissen in den Schlüsseltresor verfügt. |
| AADClientSecret | Clientgeheimnis der Azure AD-Anwendung, die über Berechtigungen zum Schreiben von Geheimnissen in den Schlüsseltresor verfügt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden soll. Sie können ihn abrufen, indem Sie das Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` oder den Azure CLI-Befehl `az keyvault list --resource-group "MySecureGroup"` verwenden.|
|  keyEncryptionKeyURL | Die URL des KEK, der zum Verschlüsseln des generierten BitLocker-Schlüssels verwendet wird. Dieser Parameter ist optional, wenn Sie in der Dropdownliste „UseExistingKek“ die Option **nokek** auswählen. Wenn Sie in der Dropdownliste „UseExistingKek“ die Option **kek** auswählen, müssen Sie den Wert _keyEncryptionKeyURL_ eingeben. |
| volumeType | Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Gültige Werte sind _OS_ , _Data_ und _All_ . |
| sequenceVersion | Sequenzversion des BitLocker-Vorgangs. Diese Versionsnummer sollte jedes Mal erhöht werden, wenn auf demselben virtuellen Computer ein Vorgang für eine Datenträgerverschlüsselung durchgeführt wird. |
| vmName | Der Name des virtuellen Computers, auf dem der Verschlüsselungsvorgang durchgeführt wird. |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>Neue IaaS-VMs, die aus einer kundenseitig verschlüsselten VHD und mit Verschlüsselungsschlüsseln erstellt wurden
In diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die Resource Manager-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. In den folgenden Abschnitten werden die Resource Manager-Vorlage und die CLI-Befehle ausführlicher erläutert. 

Verwenden Sie im Anhang die Anleitung zum Vorbereiten von vorverschlüsselten Images, die in Azure verwendet werden können. Nach dem Erstellen des Images können Sie die Schritte im nächsten Abschnitt ausführen, um eine verschlüsselte Azure-VM zu erstellen.

* [Vorbereiten einer vorverschlüsselten Windows-VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Verschlüsseln von VMs mit vorverschlüsselten VHDs mithilfe von Azure PowerShell
Sie können die Datenträgerverschlüsselung auf einer verschlüsselten VHD aktivieren, indem Sie das PowerShell-Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) verwenden. Im folgenden Beispiel sind einige häufig verwendete Parameter angegeben. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivieren der Verschlüsselung auf einem neu hinzugefügten Datenträger
Sie können [einer Windows-VM einen neuen Datenträger mit PowerShell](attach-disk-ps.md) oder [über das Azure-Portal](attach-managed-disk-portal.md) hinzufügen. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger mit Azure PowerShell
 Bei Verwendung von PowerShell zum Verschlüsseln eines neuen Datenträgers für Windows-VMs sollte eine neue Sequenzversion angegeben werden. Die Sequenzversion muss eindeutig sein. Mit dem unten angegebenen Skript wird eine GUID für die Sequenzversion generiert. In einigen Fällen kann ein neu hinzugefügter Datenträger ggf. automatisch über die Azure Disk Encryption-Erweiterung verschlüsselt werden. Die automatische Verschlüsselung tritt normalerweise auf, wenn der virtuelle Computer neu gestartet wird, nachdem die neue Festplatte online ist. Dies wird in der Regel dadurch verursacht, dass für den Datenträgertyp „Alle“ angegeben wurde, wenn die Festplattenverschlüsselung zuvor auf dem virtuellen Computer ausgeführt wurde. Wenn die automatische Verschlüsselung auf einem neu hinzugefügten Datenträger auftritt, wird empfohlen, das Cmdlet „Set-AzVmDiskEncryptionExtension“ erneut mit der neuen Sequenzversion auszuführen. Wenn Ihr neuer Datenträger automatisch verschlüsselt ist und Sie diese Verschlüsselung nicht wünschen, entschlüsseln Sie zuerst alle Laufwerke und verschlüsseln Sie dann mit einer neuen Sequenzversion, die das Betriebssystem für den Datenträgertyp angibt. 
 

-  **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, die VM, der Schlüsseltresor, die AAD-App und der geheime Clientschlüssel sollten bereits beim Erfüllen der Voraussetzungen erstellt worden sein. Ersetzen Sie „MyKeyVaultResourceGroup“, „MyVirtualMachineResourceGroup“, „MySecureVM“, „MySecureVault“, „My-AAD-client-ID“ und „My-AAD-client-secret“ durch Ihre Werte. In diesem Beispiel wird „All“ für den Parameter „-VolumeType“ verwendet, wodurch Betriebssystem- und Datenvolumes enthalten sind. Wenn Sie nur das Betriebssystemvolume verschlüsseln möchten, verwenden Sie „OS“ für den Parameter „-VolumeType“. 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:** Für Azure Disk Encryption können Sie einen vorhandenen Schlüssel in Ihrem Schlüsseltresor angeben, um Geheimnisse für die Datenträgerverschlüsselung zu umschließen, die beim Aktivieren der Verschlüsselung generiert wurden. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen. In diesem Beispiel wird „All“ für den Parameter „-VolumeType“ verwendet, wodurch Betriebssystem- und Datenvolumes enthalten sind. Wenn Sie nur das Betriebssystemvolume verschlüsseln möchten, verwenden Sie „OS“ für den Parameter „-VolumeType“. 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger per Azure CLI
  Mit dem Azure CLI-Befehl wird automatisch eine neue Sequenzversion für Sie bereitgestellt, wenn Sie den Befehl zum Aktivieren der Verschlüsselung ausführen. Zulässige Werte für den Parameter „-VolumeType“ sind „All“, „OS“ und „Data“. Möglicherweise müssen Sie den Parameter „-VolumeType“ in „OS“ oder „Data“ ändern, wenn Sie nur einen Datenträgertyp für den virtuellen Computer verschlüsseln. In den Beispielen wird „All“ für den Parameter „-VolumeType“ verwendet. 

-  **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Aktivieren Sie die Verschlüsselung, indem Sie die auf dem Azure AD-Clientzertifikat basierende Authentifizierung verwenden.
Sie können die Clientzertifikatauthentifizierung mit oder ohne KEK verwenden. Bevor Sie die PowerShell-Skripts verwenden, sollten Sie das Zertifikat bereits in den Schlüsseltresor hochgeladen und auf der VM bereitgestellt haben. Wenn Sie auch einen KEK nutzen, sollte der KEK bereits vorhanden sein. Weitere Informationen finden Sie im Abschnitt [Zertifikatbasierte Authentifizierung für Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional) des Artikels mit den Voraussetzungen.


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Aktivieren der Verschlüsselung mit der zertifikatbasierten Authentifizierung mit Azure PowerShell

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Aktivieren der Verschlüsselung mit der zertifikatbasierten Authentifizierung und einem KEK mit Azure PowerShell

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>Deaktivieren der Verschlüsselung
Sie können die Verschlüsselung mit Azure PowerShell, der Azure CLI oder einer Resource Manager-Vorlage deaktivieren. 

- **Deaktivieren der Datenträgerverschlüsselung mit Azure PowerShell:** Verwenden Sie das Cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Deaktivieren der Verschlüsselung mit der Azure CLI:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Deaktivieren der Verschlüsselung mit einer Resource Manager-Vorlage:** 

    1. Klicken Sie in der Vorlage unter [Disable disk encryption on running Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) (Deaktivieren der Datenträgerverschlüsselung auf einer ausgeführten Windows-VM) auf **Deploy to Azure** (In Azure bereitstellen).
    2. Wählen Sie das Abonnement, die Ressourcengruppe, den Standort, die VM, die rechtlichen Bedingungen und die Vereinbarung aus.
    3.  Klicken Sie auf **Kaufen** , um die Datenträgerverschlüsselung auf einer ausgeführten Windows-VM zu deaktivieren. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)
