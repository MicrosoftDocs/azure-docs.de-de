---
title: Aktivieren von Azure Disk Encryption für virtuelle Windows-IaaS-Computer
description: Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für virtuelle Windows-IaaS-Computer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/12/2019
ms.custom: seodec18
ms.openlocfilehash: 376df206d75780a4b814873d72d9c56554f6b0b8
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956617"
---
# <a name="enable-azure-disk-encryption-for-windows-iaas-vms"></a>Aktivieren von Azure Disk Encryption für virtuelle Windows-IaaS-Computer

Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für virtuelle Windows-IaaS-Computer (VMs). Bevor Sie die Datenträgerverschlüsselung verwenden können, müssen die [Voraussetzungen für Azure Disk Encryption](../security/azure-security-disk-encryption-prerequisites.md) erfüllt sein. 

Es wird zudem dringend empfohlen, [eine Momentaufnahme zu erstellen](../virtual-machines/windows/snapshot-copy-managed-disk.md) und/oder Ihre Datenträger vor der Verschlüsselung zu sichern. Durch Sicherungen wird sichergestellt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Für VMs mit verwalteten Datenträgern ist eine Sicherung erforderlich, bevor die Verschlüsselung durchgeführt wird. Nach dem Erstellen einer Sicherung können Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) verwenden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Weitere Informationen zum Sichern und Wiederherstellen von verschlüsselten VMs finden Sie im Artikel [Sichern und Wiederherstellen eines verschlüsselten virtuellen Azure-Computers](../backup/backup-azure-vms-encryption.md).

>[!WARNING]
> - Wenn Sie zuvor [Azure Disk Encryption mit Azure AD-App](azure-security-disk-encryption-prerequisites-aad.md) zum Verschlüsseln dieses virtuellen Computers verwendet haben, müssen Sie diese Verschlüsselungsoption auch weiterhin für Ihren virtuellen Computer verwenden. Sie können [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) auf dieser verschlüsselten VM nicht verwenden, da dies kein unterstütztes Szenario ist. Das bedeutet, das Verlassen der AAD-Anwendung für diese verschlüsselte VM wird noch nicht unterstützt. 
> - Für Azure Disk Encryption müssen sich der Schlüsseltresor und die virtuellen Computer in der gleichen Region befinden. Erstellen und verwenden Sie einen Schlüsseltresor, der sich in derselben Region wie die zu verschlüsselnde VM befindet. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningWinVM"></a> Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten virtuellen Windows-IaaS-Computern
Sie können die Verschlüsselung aktivieren, indem Sie eine Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. Wenn Sie Schemainformationen für die Erweiterung des virtuellen Computers benötigen, finden Sie diese im Artikel [Azure Disk Encryption für Windows](../virtual-machines/extensions/azure-disk-enc-windows.md).

>[!IMPORTANT]
 > Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Der Befehl „Set-AzVMDiskEncryptionExtension“ führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
>
> Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 
>

### <a name="bkmk_RunningWinVMPSH"></a> Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten VMs mit Azure PowerShell 
Verwenden Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension), um die Verschlüsselung auf einem ausgeführten virtuellen IaaS-Computer in Azure zu aktivieren. 

-  **Verschlüsseln eines ausgeführten virtuellen Computers:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, der virtuelle Computer und der Schlüsseltresor wurden bereits bei der Vorbereitung erstellt. Ersetzen Sie „MyKeyVaultResourceGroup“, „MyVirtualMachineResourceGroup“, „MySecureVM“ und „MySecureVault“ durch Ihre eigenen Werte.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **Verschlüsseln eines ausgeführten virtuellen Computers mit KEK:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus), um den Verschlüsselungsstatus eines virtuellen IaaS-Computers zu überprüfen. 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Deaktivieren der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. Das Deaktivieren der Datenträgerverschlüsselung auf einem virtuellen Windows-Computer funktioniert nicht wie erwartet, wenn sowohl das Betriebssystem als auch Datenträger verschlüsselt wurden. Deaktivieren Sie stattdessen die Verschlüsselung auf allen Datenträgern.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="bkmk_RunningWinVMCLI"></a> Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten VMs mit der Azure CLI
Verwenden Sie den Befehl [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), um die Verschlüsselung auf einem ausgeführten virtuellen IaaS-Computer in Azure zu aktivieren.

- **Verschlüsseln eines ausgeführten virtuellen Computers:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **Verschlüsseln eines ausgeführten virtuellen Computers mit KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie den Befehl [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show), um den Verschlüsselungsstatus eines virtuellen IaaS-Computers zu überprüfen. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Deaktivieren der Verschlüsselung:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. Das Deaktivieren der Datenträgerverschlüsselung auf einem virtuellen Windows-Computer funktioniert nicht wie erwartet, wenn sowohl das Betriebssystem als auch Datenträger verschlüsselt wurden. Deaktivieren Sie stattdessen die Verschlüsselung auf allen Datenträgern.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
 
  > [!NOTE]
  >Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Dieser Befehl führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
  >
  >Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 

### <a name="bkmk_RunningWinVMwRM"> </a>Verwenden der Resource Manager-Vorlage

Sie können die Datenträgerverschlüsselung auf vorhandenen oder ausgeführten virtuellen IaaS-Windows-VMs in Azure aktivieren, indem Sie die [Resource Manager-Vorlage zum Verschlüsseln eines ausgeführten virtuellen Windows-Computers](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad) verwenden.


1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Standort, die Einstellungen, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Kaufen**, um die Verschlüsselung auf dem vorhandenen oder ausgeführten virtuellen IaaS-Computer zu aktivieren.

Die folgende Tabelle enthält die Resource Manager-Vorlagenparameter für vorhandene oder ausgeführte virtuelle Computer:

| Parameter | BESCHREIBUNG |
| --- | --- |
| vmName | Der Name des virtuellen Computers, der den Verschlüsselungsvorgang ausführt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden soll. Sie können ihn abrufen, indem Sie das Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` oder den Azure CLI-Befehl `az keyvault list --resource-group "MyKeyVaultResourceGroup"` verwenden.|
| keyVaultResourceGroup | Der Name der Ressourcengruppe mit dem Schlüsseltresor|
|  keyEncryptionKeyURL | Die URL des KEK, der zum Verschlüsseln des generierten BitLocker-Schlüssels verwendet wird. Dieser Parameter ist optional, wenn Sie in der Dropdownliste „UseExistingKek“ die Option **nokek** auswählen. Wenn Sie in der Dropdownliste „UseExistingKek“ die Option **kek** auswählen, müssen Sie den Wert _keyEncryptionKeyURL_ eingeben. |
| volumeType | Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Gültige Werte sind _OS_, _Data_ und _All_. 
| forceUpdateTag | Dient zum Übergeben eines eindeutigen Werts (beispielsweise einer GUID), wenn die Ausführung des Vorgangs erzwungen werden muss. |
| resizeOSDisk | Gibt an, ob die Größe der Betriebssystempartition angepasst werden soll, um die gesamte Betriebssystem-VHD einzunehmen, bevor das Systemvolume aufgeteilt wird. |
| location | Der Standort für alle Ressourcen. |

## <a name="encrypt-virtual-machine-scale-sets"></a>Verschlüsseln von VM-Skalierungsgruppen

Mit [Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md) können Sie eine Gruppe identischer virtueller Computer mit Lastenausgleich erstellen und verwalten. Die Anzahl von VM-Instanzen kann automatisch erhöht oder verringert werden, wenn sich der Bedarf ändert, oder es kann ein Zeitplan festgelegt werden. Verwenden Sie zum Verschlüsseln von VM-Skalierungsgruppen die Befehlszeilenschnittstelle oder Azure PowerShell.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Verschlüsseln von VM-Skalierungsgruppen unter Verwendung von Azure PowerShell

Verwenden Sie das Cmdlet [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension), um die Verschlüsselung für eine VM-Skalierungsgruppe unter Windows zu aktivieren. Die Ressourcengruppe, die VM-Skalierungsgruppe und der Schlüsseltresor wurden bereits bei der Vorbereitung erstellt.

-  **Verschlüsseln einer ausgeführten VM-Skalierungsgruppe:**
    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;


-  **Encrypt a running virtual machine scale set using KEK to wrap the key**:

    ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
     $VmssName = "MySecureVmss";
     $KeyVaultName= "MySecureVault";
     $keyEncryptionKeyName = "MyKeyEncryptionKey";
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     Set-AzRmVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
    ```

   >[!NOTE]
   > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Abrufen des Verschlüsselungsstatus für eine VM-Skalierungsgruppe:** Verwenden Sie das Cmdlet [Get-AzVmssDiskEncryption](/powershell/module/az.compute/get-azvmssdiskencryption).
    
    ```azurepowershell-interactive
    get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Deaktivieren der Verschlüsselung für eine VM-Skalierungsgruppe:** Verwenden Sie das Cmdlet [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption). 

    ```azurepowershell-interactive
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Verschlüsseln von VM-Skalierungsgruppen unter Verwendung der Azure-Befehlszeilenschnittstelle

Verwenden Sie [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable), um die Verschlüsselung für eine VM-Skalierungsgruppe unter Windows zu aktivieren. Ist die Upgraderichtlinie für die Skalierungsgruppe auf „Manuell“ festgelegt, starten Sie die Verschlüsselung mit [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Die Ressourcengruppe, die VM-Skalierungsgruppe und der Schlüsseltresor wurden bereits bei der Vorbereitung erstellt.

-  **Verschlüsseln einer ausgeführten VM-Skalierungsgruppe**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" 
    ```

-  **Verschlüsseln einer ausgeführten VM-Skalierungsgruppe unter Verwendung von KEK zum Umschließen des Schlüssel**
    ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault" 

     ```
     
   >[!NOTE]
   > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Abrufen des Verschlüsselungsstatus für eine VM-Skalierungsgruppe:** Verwenden Sie [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show).

    ```azurecli-interactive
     az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Deaktivieren der Verschlüsselung für eine VM-Skalierungsgruppe:** Verwenden Sie [az vmss encryption disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable).
    ```azurecli-interactive
     az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-windows-virtual-machine-scale-sets"></a>Azure Resource Manager-Vorlagen für Skalierungsgruppen für virtuelle Windows-Computer

Wenn Sie Skalierungsgruppen für virtuelle Windows-Computer verschlüsseln oder entschlüsseln möchten, verwenden Sie die Azure Resource Manager-Vorlagen und die folgenden Anweisungen:

- [Aktivieren der Verschlüsselung für eine Skalierungsgruppe für virtuelle Windows-Computer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)
- [Disable encryption on a Windows virtual machine scale set (Deaktivieren der Verschlüsselung für eine Windows-VM-Skalierungsgruppe)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

     1. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.
     2. Füllen Sie die erforderlichen Felder aus, und stimmen Sie dann den Geschäftsbedingungen zu.
     3. Klicken Sie auf **Kaufen**, um die Vorlage bereitzustellen.

## <a name="bkmk_VHDpre"> </a> Neue virtuelle IaaS-Computer, die aus einer vom Kunden verschlüsselten VHD und mit Verschlüsselungsschlüsseln erstellt wurden

In diesem Szenario können Sie die Verschlüsselung mithilfe von PowerShell-Cmdlets oder CLI-Befehlen aktivieren. 

Verwenden Sie im Anhang die Anleitung zum Vorbereiten von vorverschlüsselten Images, die in Azure verwendet werden können. Nach dem Erstellen des Images können Sie die Schritte im nächsten Abschnitt ausführen, um eine verschlüsselte Azure-VM zu erstellen.

* [Vorbereiten einer vorverschlüsselten Windows-VHD](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Vorbereiten einer vorverschlüsselten Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Der Befehl „Set-AzVMDiskEncryptionExtension“ führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
>
>Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 


### <a name="bkmk_VHDprePSH"> </a> Verschlüsseln von VMs mit vorverschlüsselten VHDs per Azure PowerShell
Sie können die Datenträgerverschlüsselung auf einer verschlüsselten VHD aktivieren, indem Sie das PowerShell-Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) verwenden. Im folgenden Beispiel sind einige häufig verwendete Parameter angegeben. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivieren der Verschlüsselung auf einem neu hinzugefügten Datenträger
Sie können [einer Windows-VM einen neuen Datenträger mit PowerShell](../virtual-machines/windows/attach-disk-ps.md) oder [über das Azure-Portal](../virtual-machines/windows/attach-managed-disk-portal.md) hinzufügen. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger mit Azure PowerShell
 Bei Verwendung von PowerShell zum Verschlüsseln eines neuen Datenträgers für Windows-VMs sollte eine neue Sequenzversion angegeben werden. Die Sequenzversion muss eindeutig sein. Mit dem unten angegebenen Skript wird eine GUID für die Sequenzversion generiert. In einigen Fällen kann ein neu hinzugefügter Datenträger ggf. automatisch über die Azure Disk Encryption-Erweiterung verschlüsselt werden. Die automatische Verschlüsselung tritt normalerweise auf, wenn der virtuelle Computer neu gestartet wird, nachdem die neue Festplatte online ist. Dies wird in der Regel dadurch verursacht, dass für den Datenträgertyp „Alle“ angegeben wurde, wenn die Festplattenverschlüsselung zuvor auf dem virtuellen Computer ausgeführt wurde. Wenn die automatische Verschlüsselung auf einem neu hinzugefügten Datenträger auftritt, wird empfohlen, das Cmdlet „Set-AzVmDiskEncryptionExtension“ erneut mit der neuen Sequenzversion auszuführen. Wenn Ihr neuer Datenträger automatisch verschlüsselt ist und Sie diese Verschlüsselung nicht wünschen, entschlüsseln Sie zuerst alle Laufwerke und verschlüsseln Sie dann mit einer neuen Sequenzversion, die das Betriebssystem für den Datenträgertyp angibt. 
  
 

-  **Verschlüsseln eines ausgeführten virtuellen Computers:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, der virtuelle Computer und der Schlüsseltresor wurden bereits bei der Vorbereitung erstellt. Ersetzen Sie „MyKeyVaultResourceGroup“, „MyVirtualMachineResourceGroup“, „MySecureVM“ und „MySecureVault“ durch Ihre eigenen Werte. In diesem Beispiel wird „All“ für den Parameter „-VolumeType“ verwendet, wodurch Betriebssystem- und Datenvolumes enthalten sind. Wenn Sie nur das Betriebssystemvolume verschlüsseln möchten, verwenden Sie „OS“ für den Parameter „-VolumeType“. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **Verschlüsseln eines ausgeführten virtuellen Computers mit KEK:** In diesem Beispiel wird „All“ für den Parameter „-VolumeType“ verwendet, wodurch Betriebssystem- und Datenvolumes enthalten sind. Wenn Sie nur das Betriebssystemvolume verschlüsseln möchten, verwenden Sie „OS“ für den Parameter „-VolumeType“.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger per Azure CLI
 Mit dem Azure CLI-Befehl wird automatisch eine neue Sequenzversion für Sie bereitgestellt, wenn Sie den Befehl zum Aktivieren der Verschlüsselung ausführen. Im Beispiel wird „All“ für den Parameter „-VolumeType“ verwendet. Möglicherweise müssen Sie den Parameter „-VolumeType“ in „OS“ ändern, wenn Sie nur den Betriebssystemdatenträger verschlüsseln. Im Gegensatz zur PowerShell-Syntax erfordert die Befehlszeilenschnittstelle vom Benutzer keine Angabe einer eindeutigen Sequenzversion beim Aktivieren der Verschlüsselung. Die Befehlszeilenschnittstelle wird automatisch generiert und verwendet einen eigenen eindeutigen Sequenzversionswert.   

-  **Verschlüsseln eines ausgeführten virtuellen Computers:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **Verschlüsseln eines ausgeführten virtuellen Computers mit KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>Deaktivieren der Verschlüsselung
Sie können die Verschlüsselung mit Azure PowerShell, der Azure CLI oder einer Resource Manager-Vorlage deaktivieren. Das Deaktivieren der Datenträgerverschlüsselung auf einem virtuellen Windows-Computer funktioniert nicht wie erwartet, wenn sowohl das Betriebssystem als auch Datenträger verschlüsselt wurden. Deaktivieren Sie stattdessen die Verschlüsselung auf allen Datenträgern.

- **Deaktivieren der Datenträgerverschlüsselung mit Azure PowerShell:** Verwenden Sie das Cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Deaktivieren der Verschlüsselung mit der Azure CLI:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Deaktivieren der Verschlüsselung mit einer Resource Manager-Vorlage:** 

    1. Klicken Sie in der Vorlage unter [Disable disk encryption on running Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) (Deaktivieren der Datenträgerverschlüsselung auf einer ausgeführten Windows-VM) auf **Deploy to Azure** (In Azure bereitstellen).
    2. Wählen Sie das Abonnement, die Ressourcengruppe, den Standort, die VM, den Volumetyp, die rechtlichen Bedingungen und die Vereinbarung aus.
    3.  Klicken Sie auf **Kaufen**, um die Datenträgerverschlüsselung auf einer ausgeführten Windows-VM zu deaktivieren. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Aktivieren von Azure Disk Encryption für Linux](azure-security-disk-encryption-linux.md)
