---
title: Azure Disk Encryption-Szenarien auf virtuellen Windows-Computern
description: Dieser Artikel enthält eine Anleitung zum Aktivieren von Microsoft Azure Disk Encryption für virtuelle Windows-Computer für verschiedene Szenarien.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: df74a15a84ba04addb75b021d3b77b06cfe2ea48
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781112"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Azure Disk Encryption-Szenarien auf virtuellen Windows-Computern

Azure Disk Encryption für virtuelle Windows-Computer (VMs) bietet mithilfe des BitLocker-Features von Windows eine vollständige Datenträgerverschlüsselung des Betriebssystemdatenträgers und des Datenträgers für Daten. Darüber hinaus wird die Verschlüsselung des temporären Datenträgers bereitgestellt, wenn der VolumeType-Parameter auf „All“ festgelegt ist.

Azure Disk Encryption ist [mit Azure Key Vault integriert](disk-encryption-key-vault.md), um Ihnen die Steuerung und Verwaltung der Datenträger-Verschlüsselungsschlüssel und -geheimnisse zu erleichtern. Eine Übersicht über den Dienst finden Sie unter [Azure Disk Encryption für virtuelle Windows-Computer](disk-encryption-overview.md).

Sie können Datenträgerverschlüsselung nur auf virtuelle Computer mit [unterstützten VM-Größen und Betriebssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems) anwenden. Außerdem müssen die folgenden Voraussetzungen erfüllt sein:

- [Netzwerkanforderungen](disk-encryption-overview.md#networking-requirements)
- [Gruppenrichtlinienanforderungen](disk-encryption-overview.md#group-policy-requirements)
- [Speicheranforderungen für Verschlüsselungsschlüssel](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - Wenn Sie zuvor Azure Disk Encryption mit Azure AD zum Verschlüsseln eines virtuellen Computers verwendet haben, müssen Sie diese Option auch weiterhin zum Verschlüsseln Ihres virtuellen Computers verwenden. Weitere Informationen finden Sie unter [Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-overview-aad.md). 
>
> - In allen Fällen sollten Sie [eine Momentaufnahme](snapshot-copy-managed-disk.md) oder eine Sicherung erstellen, bevor Datenträger verschlüsselt werden. Durch Sicherungen wird sichergestellt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Für VMs mit verwalteten Datenträgern ist eine Sicherung erforderlich, bevor die Verschlüsselung durchgeführt wird. Nach dem Erstellen einer Sicherung können Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) verwenden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Weitere Informationen zum Sichern und Wiederherstellen von verschlüsselten VMs finden Sie unter [Sichern und Wiederherstellen eines verschlüsselten virtuellen Azure-Computers](../../backup/backup-azure-vms-encryption.md). 
>
> - Beim Verschlüsseln oder Deaktivieren der Verschlüsselung kann eine VM neu gestartet werden.

## <a name="install-tools-and-connect-to-azure"></a>Installieren von Tools und Herstellen einer Verbindung mit Azure

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>Aktivieren der Verschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Windows-Computer
In diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die Resource Manager-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. Wenn Sie Schemainformationen für die Erweiterung des virtuellen Computers benötigen, finden Sie diese im Artikel [Azure Disk Encryption für Windows](../extensions/azure-disk-enc-windows.md).

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten VMs mit Azure PowerShell 
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

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Aktivieren der Verschlüsselung auf vorhandenen oder ausgeführten VMs mit der Azure-Befehlszeilenschnittstelle
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

### <a name="using-the-resource-manager-template"></a>Erstellen der Resource Manager-Vorlage

Sie können die Datenträgerverschlüsselung auf vorhandenen oder ausgeführten virtuellen IaaS-Windows-VMs in Azure aktivieren, indem Sie die [Resource Manager-Vorlage zum Verschlüsseln eines ausgeführten virtuellen Windows-Computers](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad) verwenden.


1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Standort, die Einstellungen, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Kaufen**, um die Verschlüsselung auf dem vorhandenen oder ausgeführten virtuellen IaaS-Computer zu aktivieren.

Die folgende Tabelle enthält die Resource Manager-Vorlagenparameter für vorhandene oder ausgeführte virtuelle Computer:

| Parameter | BESCHREIBUNG |
| --- | --- |
| vmName | Der Name des virtuellen Computers, der den Verschlüsselungsvorgang ausführt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der BitLocker-Schlüssel hochgeladen werden soll. Sie können ihn abrufen, indem Sie das Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` oder den Azure CLI-Befehl `az keyvault list --resource-group "MyKeyVaultResourceGroup"` verwenden.|
| keyVaultResourceGroup | Der Name der Ressourcengruppe mit dem Schlüsseltresor|
|  keyEncryptionKeyURL | Die URL des Schlüsselverschlüsselungsschlüssels im Format „https://&lt;keyvault-name&gt;.vault.azure.net/key/&lt;key-name&gt;. Wenn Sie keinen Schlüsselverschlüsselungsschlüssels verwenden möchten, lassen Sie dieses Feld leer. |
| volumeType | Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Gültige Werte sind _OS_, _Data_ und _All_. 
| forceUpdateTag | Dient zum Übergeben eines eindeutigen Werts (beispielsweise einer GUID), wenn die Ausführung des Vorgangs erzwungen werden muss. |
| resizeOSDisk | Gibt an, ob die Größe der Betriebssystempartition angepasst werden soll, um die gesamte Betriebssystem-VHD einzunehmen, bevor das Systemvolume aufgeteilt wird. |
| location | Der Standort für alle Ressourcen. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Aktivieren der Verschlüsselung auf NVMe-Datenträgern für Lsv2-VMs

In diesem Szenario wird die Aktivierung von Azure Disk Encryption auf NVMe-Datenträgern für virtuelle Computer der Lsv2-Serie beschrieben.  Die Lsv2-Serie unterstützt lokalen NVMe-Speicher. Lokale NVMe-Datenträger sind temporär und die Daten auf diesen Datenträgern gehen verloren, wenn Sie den virtuellen Computer beenden oder seine Zuordnung aufheben (Siehe: [LSv2-Serie](../lsv2-series.md)).

So aktivieren Sie die Verschlüsselung auf NVMe-Datenträgern

1. Initialisieren Sie die NVMe-Datenträger, und erstellen Sie NTFS-Volumes.
1. Aktivieren Sie die Verschlüsselung auf dem virtuellen Computer mit dem VolumeType-Parameter, der auf „All“ festgelegt ist. Dadurch wird die Verschlüsselung für alle Datenträger für Betriebssysteme und Daten ermöglicht, einschließlich der von NVMe-Datenträgern gesicherten Volumes. Weitere Informationen finden Sie unter [Aktivieren der Verschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Windows-Computer](#enable-encryption-on-an-existing-or-running-windows-vm).

In den folgenden Szenarien bleibt die Verschlüsselung auf den NVMe-Datenträgern bestehen:
- VM-Neustart
- VMSS-Reimaging
- Wechsel des Betriebssystems

NVMe-Datenträger werden in den folgenden Szenarien nicht initialisiert:

- Starten der VM nach Belegungsfreigabe
- Dienstreparatur
- Backup

In diesen Szenarien müssen die NVMe-Datenträger nach dem Start der VM initialisiert werden. Führen Sie zum Aktivieren der Verschlüsselung auf den NVMe-Datenträgern den Befehl zum erneuten Aktivieren von Azure Disk Encryption aus, nachdem die NVMe-Datenträger initialisiert wurden.

Zusätzlich zu den im Abschnitt [Nicht unterstützte Szenarien](#unsupported-scenarios) aufgeführten Szenarien wird die Verschlüsselung von NVMe-Datenträgern für Folgendes nicht unterstützt:

- Mit Azure Disk Encryption mit AAD (vorherige Version) verschlüsselte VMs
- NVMe-Datenträger mit Speicherplätzen
- Azure Site Recovery von SKUs mit NVMe-Datenträgern (siehe [Unterstützungsmatrix für die Notfallwiederherstellung von Azure-VMs zwischen Azure-Regionen: Replizierte Computer – Speicher](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage)).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Neue virtuelle IaaS-Computer, die aus einer vom Kunden verschlüsselten VHD und mit Verschlüsselungsschlüsseln erstellt wurden

In diesem Szenario können Sie mithilfe von PowerShell-Cmdlets oder CLI-Befehlen eine neue VM aus einer vorverschlüsselten VHD und den zugehörigen Verschlüsselungsschlüsseln erstellen. 

Befolgen Sie die Anweisungen unter [Vorbereiten einer vorverschlüsselten Windows-VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd). Nach dem Erstellen des Images können Sie die Schritte im nächsten Abschnitt ausführen, um eine verschlüsselte Azure-VM zu erstellen.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Verschlüsseln von VMs mit vorverschlüsselten VHDs mithilfe von Azure PowerShell
Sie können die Datenträgerverschlüsselung auf einer verschlüsselten VHD aktivieren, indem Sie das PowerShell-Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) verwenden. Im folgenden Beispiel sind einige häufig verwendete Parameter angegeben. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivieren der Verschlüsselung auf einem neu hinzugefügten Datenträger
Sie können [einer Windows-VM einen neuen Datenträger mit PowerShell](attach-disk-ps.md) oder [über das Azure-Portal](attach-managed-disk-portal.md) hinzufügen. 

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
[!INCLUDE [disk-encryption-disable-encryption-powershell](../../../includes/disk-encryption-disable-powershell.md)]

## <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Die folgenden Szenarios, Features und Technologien werden von Azure Disk Encryption nicht unterstützt:

- Verschlüsseln von virtuellen Computern der Ebene „Standard“ und von virtuellen Computern, die mithilfe der klassischen Erstellungsmethode für virtuelle Computer erstellt wurden
- Verschlüsseln virtueller Computer, die mit softwarebasierten RAID-Systemen konfiguriert sind
- Verschlüsselung virtueller Computer, die mit Storage Spaces Direct (S2D) konfiguriert wurden, oder von Windows Server-Versionen vor 2016, die mit Windows-Speicherplätzen konfiguriert wurden.
- Integration mit einem lokalen Schlüsselverwaltungssystem
- Azure Files (freigegebenes Dateisystem)
- Network File System (NFS)
- Dynamische Volumes
- Windows Server-Container, die dynamische Volumes für jeden Container erstellen.
- Kurzlebige Betriebssystemdatenträger
- Verschlüsselung freigegebener/verteilter Dateisysteme, einschließlich u. a. DFS, GFS, DRDB, und CephFS.
- Verschieben von verschlüsselten virtuellen Computern in ein anderes Abonnement oder in eine andere Region
- Erstellen eines Images oder einer Momentaufnahme einer verschlüsselten VM und dessen oder deren Verwendung zum Bereitstellen weiterer VMs
- Gen2-VMs (siehe: [Unterstützung für VMs der Generation 2 in Azure](../generation-2.md#generation-1-vs-generation-2-capabilities))
- VMs der M-Serie mit Datenträgern mit Schreibbeschleunigung
- Anwenden von ADE auf eine VM, die mit [serverseitiger Verschlüsselung mit kundenseitig verwalteten Schlüsseln](../disk-encryption.md) (SSE + CMK) verschlüsselte Datenträger aufweist. Das Anwenden von SSE + CMK auf einen Datenträger auf einer mit ADE verschlüsselten VM ist ebenfalls ein nicht unterstütztes Szenario.
- Migrieren einer VM, die mit ADE verschlüsselt ist oder **jemals** mit ADE verschlüsselt war, zur [serverseitigen Verschlüsselung mit kundenseitig verwalteten Schlüsseln](../disk-encryption.md).
- [Azure-VM-Größen ohne lokalen temporären Datenträger](../azure-vms-no-temp-disk.md), insbesondere Dv4, Dsv4, Ev4 und Esv4.
- Verschlüsseln von VMs in Failoverclustern.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)
- [Azure Disk Encryption-Beispielskripts](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption-Problembehandlung](disk-encryption-troubleshooting.md)