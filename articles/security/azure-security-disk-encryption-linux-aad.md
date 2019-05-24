---
title: Azure Disk Encryption mit Azure AD-Apps für Linux-IaaS-VMs (früheres Release)
description: Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für virtuelle Linux-IaaS-Computer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 1e535ed92305d124499fd0ce9933b7edd19df32e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66118096"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms-previous-release"></a>Aktivieren von Azure Disk Encryption für virtuelle Linux-IaaS-Computer (früheres Release)

**Beim neuen Release von Azure Disk Encryption muss kein Azure AD-Anwendungsparameter mehr angegeben werden, um die VM-Datenträgerverschlüsselung zu aktivieren. Sie müssen beim neuen Release während des Schritts zum Aktivieren der Verschlüsselung keine Azure AD-Anmeldeinformationen mehr angeben. Alle neuen virtuellen Computer müssen mit dem neuen Release und ohne die Azure AD-Anwendungsparameter verschlüsselt werden. Eine Anleitung zum Aktivieren der VM-Datenträgerverschlüsselung mit dem neuen Release finden Sie unter [Azure Disk Encryption für virtuelle Linux-Computer](azure-security-disk-encryption-linux.md). Virtuelle Computer, die bereits mit Azure AD-Anwendungsparametern verschlüsselt wurden, werden weiterhin unterstützt und sollten weiterhin mit der AAD-Syntax gepflegt werden.**

Es gibt viele Szenarien für die Aktivierung der Datenträgerverschlüsselung. Die Schritte können je nach Szenario variieren. In den folgenden Abschnitten werden diese Szenarien für virtuelle Linux-IaaS-Computer ausführlicher beschrieben. Bevor Sie die Datenträgerverschlüsselung verwenden können, müssen die [Azure Disk Encryption-Voraussetzungen](azure-security-disk-encryption-prerequisites-aad.md) erfüllt werden, und es ist ratsam, den Abschnitt [Additional prerequisites for Linux IaaS VMs](azure-security-disk-encryption-prerequisites-aad.md#bkmk_LinuxPrereq) (Zusätzliche Voraussetzungen für virtuelle Linux-IaaS-Computer) durchzulesen.

Erstellen Sie eine [Momentaufnahme](../virtual-machines/windows/snapshot-copy-managed-disk.md), und sichern Sie die Datenträger, bevor diese verschlüsselt werden. Durch Sicherungen wird sichergestellt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Für VMs mit verwalteten Datenträgern ist eine Sicherung erforderlich, bevor die Verschlüsselung durchgeführt wird. Nach dem Erstellen einer Sicherung können Sie das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwenden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Weitere Informationen zum Sichern und Wiederherstellen von verschlüsselten VMs finden Sie im Artikel [Azure Backup](../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
 > - Wenn Sie zuvor [Azure Disk Encryption mit der Azure AD-App](azure-security-disk-encryption-prerequisites-aad.md) zum Verschlüsseln dieser VM verwendet haben, müssen Sie diese Verschlüsselungsoption auch weiterhin für Ihre VM verwenden. Sie können [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) auf dieser verschlüsselten VM nicht verwenden, da dies kein unterstütztes Szenario ist. Das bedeutet, das Verlassen der AAD-Anwendung für diese verschlüsselte VM wird noch nicht unterstützt.
 > - Um sicherzustellen, dass die Verschlüsselungsgeheimnisse die Regionsgrenzen nicht verlassen, müssen den Schlüsseltresor und die VMs für Azure Disk Encryption sich in derselben Region angeordnet sein. Erstellen und verwenden Sie eine Key Vault-Instanz, die sich in derselben Region wie die zu verschlüsselnde VM befindet.
 > - Beim Verschlüsseln von Linux-Betriebssystemvolumes kann der Vorgang einige Stunden dauern. Es ist normal, dass die Verschlüsselung von Linux-Betriebssystemvolumes länger als für Datenvolumes dauert.
> - Beim Verschlüsseln von Linux-Betriebssystemvolumes sollte die VM als nicht verfügbar angesehen werden. Es wird dringend empfohlen, SSH-Anmeldungen zu vermeiden, während die Verschlüsselung ausgeführt wird, damit es nicht aufgrund von Problemen zur Blockierung geöffneter Dateien kommt, auf die während des Verschlüsselungsvorgangs zugegriffen werden muss. Um den Status zu überprüfen, können die Befehle [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) oder [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) verwendet werden. Es ist zu erwarten, dass dieser Prozess bei einem Betriebssystemvolume mit 30 GB einige Stunden in Anspruch nimmt, zuzüglich der Zeit für die Verschlüsselung von Datenvolumes. Die Verschlüsselungszeit für das Datenvolume hängt proportional von der Größe und Menge der Datenvolumes ab, es sei denn, es wird die Verschlüsselungsformatoption „all“ verwendet. 
 > - Das Deaktivieren der Verschlüsselung auf virtuellen Linux-Computern wird nur für Datenvolumes unterstützt. Dies wird nicht auf Daten- oder Betriebssystemvolumes unterstützt, wenn das Betriebssystemvolume verschlüsselt wurde.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Aktivieren der Verschlüsselung auf einem vorhandenen oder ausgeführten virtuellen IaaS-Linux-Computer

In diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die Resource Manager-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. 

>[!IMPORTANT]
 >Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Der Befehl „Set-AzVMDiskEncryptionExtension“ führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
>
>Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Aktivieren der Verschlüsselung auf einem vorhandenen bzw. ausgeführten virtuellen Linux-Computer mit der Azure CLI 
Sie können die Datenträgerverschlüsselung auf Ihrer verschlüsselten VHD aktivieren, indem Sie das Befehlszeilentool [Azure CLI 2.0](/cli/azure) installieren und verwenden. Azure PowerShell kann mit [Azure Cloud Shell](../cloud-shell/overview.md) im Browser verwendet oder auf dem lokalen Computer installiert und in einer beliebigen PowerShell-Sitzung verwendet werden. Verwenden Sie die folgenden CLI-Befehle, um die Verschlüsselung auf vorhandenen oder ausgeführten virtuellen IaaS-Linux-Computern in Azure zu aktivieren:

Verwenden Sie den Befehl [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), um die Verschlüsselung auf einem ausgeführten virtuellen IaaS-Computer in Azure zu aktivieren.

-  **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie den Befehl [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show), um den Verschlüsselungsstatus eines virtuellen IaaS-Computers zu überprüfen. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Deaktivieren der Verschlüsselung:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. Die Deaktivierung der Verschlüsselung ist nur auf Datenvolumes für virtuelle Linux-Computer zulässig.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Aktivieren der Verschlüsselung auf einem vorhandenen oder ausgeführten virtuellen Linux-Computer mit PowerShell
Verwenden Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension), um die Verschlüsselung auf einem ausgeführten virtuellen IaaS-Computer in Azure zu aktivieren. Erstellen Sie eine [Momentaufnahme](../backup/backup-azure-vms-encryption.md), und/oder sichern Sie den virtuellen Computer mit [Azure Backup](../virtual-machines/windows/snapshot-copy-managed-disk.md), bevor Datenträger verschlüsselt werden. Der Parameter „-skipVmBackup“ ist bereits in den PowerShell-Skripts zum Verschlüsseln einer ausgeführten Linux-VM angegeben.

- **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, die VM, der Schlüsseltresor, die AAD-App und der geheime Clientschlüssel sollten bereits beim Erfüllen der Voraussetzungen erstellt worden sein. Ersetzen Sie „MyVirtualMachineResourceGroup“, „MyKeyVaultResourceGroup“, „MySecureVM“, „MySecureVault“, „My-AAD-client-ID“ und „My-AAD-client-secret“ durch Ihre Werte. Ändern Sie den Parameter „-VolumeType“, um anzugeben, welche Datenträger Sie verschlüsseln.

    ```azurepowershell
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $vmName = 'MySecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
    ```
- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:** Für Azure Disk Encryption können Sie einen vorhandenen Schlüssel in Ihrem Schlüsseltresor angeben, um Geheimnisse für die Datenträgerverschlüsselung zu umschließen, die beim Aktivieren der Verschlüsselung generiert wurden. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen. Ändern Sie den Parameter „-VolumeType“, um anzugeben, welche Datenträger Sie verschlüsseln. 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> </br>
  Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus), um den Verschlüsselungsstatus eines virtuellen IaaS-Computers zu überprüfen. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **Deaktivieren der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. Die Deaktivierung der Verschlüsselung ist nur auf Datenvolumes für virtuelle Linux-Computer zulässig.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Aktivieren der Verschlüsselung auf einem vorhandenen oder ausgeführten virtuellen IaaS-Linux-Computer mit einer Vorlage

Sie können die Datenträgerverschlüsselung auf einer vorhandenen oder ausgeführten virtuellen IaaS-Linux-VM in Azure aktivieren, indem Sie die [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm) verwenden.

1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Ressourcengruppenstandort, die Parameter, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Erstellen**, um die Verschlüsselung auf dem vorhandenen oder ausgeführten virtuellen IaaS-Computer zu aktivieren.

In der folgenden Tabelle werden Parameter der Resource Manager-Vorlage für vorhandene oder ausgeführte VMs aufgeführt, die eine Azure AD-Client-ID verwenden:

| Parameter | BESCHREIBUNG |
| --- | --- |
| AADClientID | Client-ID der Azure AD-Anwendung, die über Berechtigungen zum Schreiben von Geheimnissen in den Schlüsseltresor verfügt. |
| AADClientSecret | Clientgeheimnis der Azure AD-Anwendung, die über Berechtigungen zum Schreiben von Geheimnissen in den Schlüsseltresor verfügt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der Schlüssel hochgeladen werden soll. Sie können den Namen abrufen, indem Sie den Azure CLI-Befehl `az keyvault show --name "MySecureVault" --query KVresourceGroup` verwenden. |
|  keyEncryptionKeyURL | Die URL des KEK, der zum Verschlüsseln des generierten Schlüssels verwendet wird. Dieser Parameter ist optional, wenn Sie in der Dropdownliste „UseExistingKek“ die Option **nokek** auswählen. Wenn Sie in der Dropdownliste „UseExistingKek“ die Option **kek** auswählen, müssen Sie den Wert _keyEncryptionKeyURL_ eingeben. |
| volumeType | Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Gültige unterstützte Werte sind _OS_ und _All_. (Weitere Informationen finden Sie weiter oben im Abschnitt mit den Voraussetzungen unter den unterstützten Linux-Distributionen und deren Versionen für Betriebssystemdatenträger und reguläre Datenträger.) |
| sequenceVersion | Sequenzversion des BitLocker-Vorgangs. Diese Versionsnummer sollte jedes Mal erhöht werden, wenn auf demselben virtuellen Computer ein Vorgang für eine Datenträgerverschlüsselung durchgeführt wird. |
| vmName | Der Name des virtuellen Computers, auf dem der Verschlüsselungsvorgang durchgeführt wird. |
| Passphrase | Geben Sie eine sichere Passphrase als Datenverschlüsselungsschlüssel ein. |



## <a name="bkmk_EFA"> </a>Verwenden des Features EncryptFormatAll für Datenträger auf virtuellen Linux-IaaS-Computern
Mit dem Parameter **EncryptFormatAll** wird die Zeit reduziert, die zum Verschlüsseln von Linux-Datenträgern benötigt wird. Partitionen, die bestimmte Kriterien erfüllen, werden formatiert (mit dem aktuellen Dateisystem). Anschließend werden sie wieder an dem Ort bereitgestellt, an dem sie sich vor der Ausführung des Befehls befunden haben. Wenn Sie einen Datenträger ausschließen möchten, der die Kriterien erfüllt, können Sie die Bereitstellung vor dem Ausführen des Befehls aufheben.

 Nach der Ausführung dieses Befehls werden alle zuvor bereitgestellten Laufwerke formatiert. Anschließend wird die Verschlüsselungsebene für das Laufwerk gestartet, das nun leer ist. Bei Auswahl dieser Option wird auch der kurzlebige Ressourcendatenträger verschlüsselt, der an die VM angefügt ist. Nachdem das kurzlebige Laufwerk zurückgesetzt wurde, wird es erneut formatiert und bei der nächsten Gelegenheit von der Azure Disk Encryption-Lösung für die VM erneut verschlüsselt.

>[!WARNING]
> EncryptFormatAll sollte nicht verwendet werden, wenn sich auf den Datenvolumes der VM erforderliche Daten befinden. Sie können Datenträger von der Verschlüsselung ausschließen, indem Sie deren Bereitstellung aufheben. Sie sollten EncryptFormatAll zuerst auf einer Test-VM ausprobieren und sich mit dem Featureparameter und dessen Auswirkungen vertraut machen, bevor Sie das Feature auf einer VM für die Produktion einsetzen. Mit der EncryptFormatAll-Option wird der Datenträger formatiert, und alle darauf befindlichen Daten gehen verloren. Stellen Sie vor dem Fortfahren sicher, dass die Bereitstellung der auszuschließenden Datenträger ordnungsgemäß aufgehoben wird. </br></br>
 >Wenn Sie diesen Parameter festlegen, während Sie die Verschlüsselungseinstellungen aktualisieren, wird vor der eigentlichen Verschlüsselung ggf. ein Neustart durchgeführt. In diesem Fall sollten Sie auch den Datenträger, der formatiert werden soll, aus der FSTAB-Datei entfernen. Entsprechend sollten Sie die Partition, die formatiert und verschlüsselt werden soll, der FSTAB-Datei hinzufügen, bevor Sie den Verschlüsselungsvorgang initiieren. 

### <a name="bkmk_EFACriteria"> </a> EncryptFormatAll-Kriterien
Der Parameter durchläuft alle Partitionen und verschlüsselt sie, solange **alle** hier aufgeführten Kriterien erfüllt sind: 
- Ist keine Stamm-, Betriebssystem- oder Startpartition
- Ist nicht bereits verschlüsselt
- Ist kein BEK-Volume
- Ist kein RAID-Volume
- Ist kein LVM-Volume
- Ist bereitgestellt

Verschlüsselt die Datenträger, aus denen sich das RAID- oder LVM-Volume zusammensetzt, nicht das RAID- oder LVM-Volume selbst.

### <a name="bkmk_EFATemplate"> </a> Verwenden des Parameters EncryptFormatAll mit einer Vorlage
Nutzen Sie für die Verwendung der EncryptFormatAll-Option eine bereits vorhandene Azure Resource Manager-Vorlage, mit der ein virtueller Linux-Computer verschlüsselt wird, und ändern Sie das Feld **EncryptionOperation** für die AzureDiskEncryption-Ressource.

1. Verwenden Sie als Beispiel die [Resource Manager-Vorlage zum Verschlüsseln eines ausgeführten virtuellen Linux-IaaS-Computers](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm). 
2. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).
3. Ändern Sie **EncryptionOperation** von **EnableEncryption** in **EnableEncryptionFormatAl**.
4. Wählen Sie das Abonnement, die Ressourcengruppe, den Ressourcengruppenstandort, weitere Parameter, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Erstellen**, um die Verschlüsselung auf dem vorhandenen oder ausgeführten virtuellen IaaS-Computer zu aktivieren.


### <a name="bkmk_EFAPSH"> </a> Verwenden des Parameters EncryptFormatAll mit einem PowerShell-Cmdlet
Verwenden Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) mit dem Parameter `EncryptFormatAll`.

**Verschlüsseln eines ausgeführten virtuellen Computers mit einem geheimen Clientschlüssel und „EncryptFormatAll“:** Als Beispiel werden mit dem unten angegebenen Skript Ihre Variablen initialisiert und das Cmdlet „Set-AzVMDiskEncryptionExtension“ mit dem Parameter „EncryptFormatAll“ ausgeführt. Die Ressourcengruppe, die VM, der Schlüsseltresor, die AAD-App und der geheime Clientschlüssel sollten bereits beim Erfüllen der Voraussetzungen erstellt worden sein. Ersetzen Sie „MyKeyVaultResourceGroup“, „MyVirtualMachineResourceGroup“, „MySecureVM“, „MySecureVault“, „My-AAD-client-ID“ und „My-AAD-client-secret“ durch Ihre Werte.
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="bkmk_EFALVM"> </a> Verwenden des Parameters EncryptFormatAll mit Logical Volume Manager (LVM) 
Wir empfehlen Ihnen, ein LVM-on-crypt-Setup zu verwenden. Ersetzen Sie für alle folgenden Beispiele den Gerätepfad und die Bereitstellungspunkte durch die Werte für Ihren jeweiligen Anwendungsfall. Dieses Setup kann wie folgt durchgeführt werden:

- Fügen Sie die Datenträger hinzu, aus denen sich die VM zusammensetzt.
- Formatieren Sie diese Datenträger, stellen Sie sie bereit, und fügen Sie sie der FSTAB-Datei hinzu.

    1. Formatieren Sie den neu hinzugefügten Datenträger. Hier verwenden wir von Azure generierte symlinks. Durch die Verwendung von symlinks werden Probleme in Bezug auf sich ändernde Gerätenamen vermieden. Weitere Informationen finden Sie im Artikel zur [Behandlung von Problemen mit Gerätenamen](../virtual-machines/linux/troubleshoot-device-names-problems.md).
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Stellen Sie die Datenträger bereit.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Fügen Sie sie der FSTAB-Datei hinzu.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Führen Sie das PowerShell-Cmdlet „Set-AzVMDiskEncryptionExtension“ mit „-EncryptFormatAll“ aus, um diese Datenträger zu verschlüsseln.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Richten Sie LVM für diese neuen Datenträger ein. Beachten Sie, dass die verschlüsselten Laufwerke entsperrt werden, nachdem das Starten der VM abgeschlossen wurde. Die LVM-Bereitstellung muss nachfolgend also auch verzögert werden.




## <a name="bkmk_VHDpre"> </a> Neue virtuelle IaaS-Computer, die aus einer vom Kunden verschlüsselten VHD und mit Verschlüsselungsschlüsseln erstellt wurden
In diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die Resource Manager-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. In den folgenden Abschnitten werden die Resource Manager-Vorlage und die CLI-Befehle ausführlicher erläutert. 

Verwenden Sie im Anhang die Anleitung zum Vorbereiten von vorverschlüsselten Images, die in Azure verwendet werden können. Nach dem Erstellen des Images können Sie die Schritte im nächsten Abschnitt ausführen, um eine verschlüsselte Azure-VM zu erstellen.

* [Vorbereiten einer vorverschlüsselten Windows-VHD](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Vorbereiten einer vorverschlüsselten Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Der Befehl „Set-AzVMDiskEncryptionExtension“ führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
>
>Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 



### <a name="bkmk_VHDprePSH"> </a> Verwenden von Azure PowerShell zum Verschlüsseln von virtuellen IaaS-Computern mit vorverschlüsselten VHDs 
Sie können die Datenträgerverschlüsselung auf einer verschlüsselten VHD aktivieren, indem Sie das PowerShell-Cmdlet [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) verwenden. Im folgenden Beispiel sind einige häufig verwendete Parameter angegeben. 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivieren der Verschlüsselung auf einem neu hinzugefügten Datenträger
Sie können einen neuen Datenträger hinzufügen, indem Sie den Befehl [az vm disk attach](../virtual-machines/linux/add-disk.md) oder das [Azure-Portal](../virtual-machines/linux/attach-disk-portal.md) verwenden. Bevor Sie die Verschlüsselung durchführen können, müssen Sie den neu angefügten Datenträger bereitstellen. Sie müssen die Verschlüsselung des Datenträgers anfordern, da das Laufwerk nicht genutzt werden kann, während die Verschlüsselung durchgeführt wird. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger per Azure CLI
 Wenn der virtuelle Computer zuvor mit „All“ verschlüsselt war, sollte der Parameter „-VolumeType“ auf „All“ belassen werden. Bei „All“ sind sowohl Betriebssystem als auch Datenträger enthalten. Wenn der virtuelle Computer zuvor mit dem Volumetyp „OS“ verschlüsselt war, sollte der Parameter „-VolumeType“ in „All“ geändert werden, damit sowohl das Betriebssystem als auch der neue Datenträger enthalten sind. Wenn der virtuelle Computer nur mit dem Volumetyp „Data“ verschlüsselt war, kann er auf „Data“ belassen werden, wie es nachfolgend gezeigt ist. Das Hinzufügen und Anfügen eines neuen Datenträgers an einen virtuellen Computer ist keine ausreichende Vorbereitung für die Verschlüsselung. Der neu angefügte Datenträger muss außerdem formatiert und vor dem Aktivieren der Verschlüsselung ordnungsgemäß im virtuellen Computer bereitgestellt werden. Unter Linux muss der Datenträger in „/etc/fstab“ mit einem [persistenten Blockgerätenamen](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems) bereitgestellt werden.  

Im Gegensatz zur PowerShell-Syntax erfordert die Befehlszeilenschnittstelle vom Benutzer keine Angabe einer eindeutigen Sequenzversion beim Aktivieren der Verschlüsselung. Die Befehlszeilenschnittstelle wird automatisch generiert und verwendet einen eigenen eindeutigen Sequenzversionswert.

-  **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:** 

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger mit Azure PowerShell
 Bei Verwendung von PowerShell zum Verschlüsseln eines neuen Datenträgers für Linux muss eine neue Sequenzversion angegeben werden. Die Sequenzversion muss eindeutig sein. Mit dem unten angegebenen Skript wird eine GUID für die Sequenzversion generiert. 
 

-  **Verschlüsseln einer ausgeführten VM mit einem geheimen Clientschlüssel:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, die VM, der Schlüsseltresor, die AAD-App und der geheime Clientschlüssel sollten bereits beim Erfüllen der Voraussetzungen erstellt worden sein. Ersetzen Sie „MyVirtualMachineResourceGroup“, „MyKeyVaultResourceGroup“, „MySecureVM“, „MySecureVault“, „My-AAD-client-ID“ und „My-AAD-client-secret“ durch Ihre Werte. Der Parameter „-VolumeType“ wird für reguläre Datenträger und nicht für Betriebssystemdatenträger festgelegt. Wenn der virtuelle Computer zuvor mit dem Volumetyp „OS“ oder „All“ verschlüsselt war, sollte der Parameter „-VolumeType“ in „All“ geändert werden, damit sowohl das Betriebssystem als auch der neue Datenträger enthalten sind.

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
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **Verschlüsseln einer ausgeführten VM per KEK zum Umschließen des geheimen Clientschlüssels:** Für Azure Disk Encryption können Sie einen vorhandenen Schlüssel in Ihrem Schlüsseltresor angeben, um Geheimnisse für die Datenträgerverschlüsselung zu umschließen, die beim Aktivieren der Verschlüsselung generiert wurden. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen. Der Parameter „-VolumeType“ wird für reguläre Datenträger und nicht für Betriebssystemdatenträger festgelegt. Wenn der virtuelle Computer zuvor mit dem Volumetyp „OS“ oder „All“ verschlüsselt war, sollte der Parameter „-VolumeType“ in „All“ geändert werden, damit sowohl das Betriebssystem als auch der neue Datenträger enthalten sind.

     ```azurepowershell
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
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] </br> </br>
Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

## <a name="disable-encryption-for-linux-vms"></a>Deaktivieren der Verschlüsselung für virtuelle Linux-Computer
Sie können die Verschlüsselung mit Azure PowerShell, der Azure CLI oder einer Resource Manager-Vorlage deaktivieren. 

>[!IMPORTANT]
>Das Deaktivieren der Verschlüsselung mit Azure Disk Encryption auf Linux-VMs wird nur für Datenvolumes unterstützt. Dies wird nicht auf Daten- oder Betriebssystemvolumes unterstützt, wenn das Betriebssystemvolume verschlüsselt wurde.  

- **Deaktivieren der Datenträgerverschlüsselung mit Azure PowerShell:** Verwenden Sie das Cmdlet [Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Deaktivieren der Verschlüsselung mit der Azure CLI:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Deaktivieren der Verschlüsselung mit einer Resource Manager-Vorlage:** Verwenden Sie zum Deaktivieren der Verschlüsselung die Vorlage zum [Deaktivieren der Verschlüsselung auf einem ausgeführten virtuellen Linux-Computer](https://aka.ms/decrypt-linuxvm).
     1. Klicken Sie auf Schaltfläche zum **Bereitstellen in Azure**.
     2. Wählen Sie das Abonnement, die Ressourcengruppe, den Standort, die VM, die rechtlichen Bedingungen und die Vereinbarung aus.
     3.  Klicken Sie auf **Kaufen**, um die Datenträgerverschlüsselung auf einer ausgeführten Windows-VM zu deaktivieren. 


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Aktivieren von Azure Disk Encryption für Windows](azure-security-disk-encryption-windows-aad.md)
