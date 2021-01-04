---
title: Azure Disk Encryption-Szenarien auf virtuellen Linux-Computern
description: Dieser Artikel enthält eine Anleitung zur Aktivierung von Microsoft Azure Disk Encryption für virtuelle Linux-Computer für verschiedene Szenarien.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 70ebe8ede75935bcc8d8db8729fef165a5616fe7
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779803"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Azure Disk Encryption-Szenarien auf virtuellen Linux-Computern


Azure Disk Encryption für virtuelle Linux-Computer (VMs) bietet mithilfe des DM-Crypt-Features von Linux eine vollständige Datenträgerverschlüsselung des Betriebssystemdatenträgers und der Datenträger für Daten. Darüber hinaus wird bei Verwendung des EncryptFormatAll-Features Verschlüsselung des temporären Datenträgers bereitstellt.

Azure Disk Encryption ist [mit Azure Key Vault integriert](disk-encryption-key-vault.md), um Ihnen die Steuerung und Verwaltung der Datenträger-Verschlüsselungsschlüssel und -geheimnisse zu erleichtern. Eine Übersicht über den Dienst finden Sie unter [Azure Disk Encryption für Linux-VMs](disk-encryption-overview.md).

Sie können Datenträgerverschlüsselung nur auf virtuelle Computer mit [unterstützten VM-Größen und Betriebssystemen](disk-encryption-overview.md#supported-vms-and-operating-systems) anwenden. Außerdem müssen die folgenden Voraussetzungen erfüllt sein:

- [Weitere Anforderungen für VMs](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netzwerkanforderungen](disk-encryption-overview.md#networking-requirements)
- [Speicheranforderungen für Verschlüsselungsschlüssel](disk-encryption-overview.md#encryption-key-storage-requirements)

In allen Fällen sollten Sie [eine Momentaufnahme](snapshot-copy-managed-disk.md) und/oder eine Sicherung erstellen, bevor Datenträger verschlüsselt werden. Durch Sicherungen wird sichergestellt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Für VMs mit verwalteten Datenträgern ist eine Sicherung erforderlich, bevor die Verschlüsselung durchgeführt wird. Nach dem Erstellen einer Sicherung können Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) verwenden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Weitere Informationen zum Sichern und Wiederherstellen von verschlüsselten VMs finden Sie im Artikel [Azure Backup](../../backup/backup-azure-vms-encryption.md). 

>[!WARNING]
> - Wenn Sie zuvor Azure Disk Encryption mit Azure AD zum Verschlüsseln eines virtuellen Computers verwendet haben, müssen Sie diese Option auch weiterhin zum Verschlüsseln Ihres virtuellen Computers verwenden. Weitere Informationen finden Sie unter [Azure Disk Encryption mit Azure AD (vorheriges Release)](disk-encryption-overview-aad.md). 
>
> - Beim Verschlüsseln von Linux-Betriebssystemvolumes sollte die VM als nicht verfügbar angesehen werden. Es wird dringend empfohlen, SSH-Anmeldungen zu vermeiden, während die Verschlüsselung ausgeführt wird, damit es nicht aufgrund von Problemen zur Blockierung geöffneter Dateien kommt, auf die während des Verschlüsselungsvorgangs zugegriffen werden muss. Verwenden Sie zum Überprüfen des Fortschritts das PowerShell-Cmdlet [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) oder den CLI-Befehl [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show). Es ist zu erwarten, dass dieser Prozess bei einem Betriebssystemvolume mit 30 GB einige Stunden in Anspruch nimmt, zuzüglich der Zeit für die Verschlüsselung von Datenvolumes. Die Verschlüsselungszeit für das Datenvolume hängt proportional von der Größe und Menge der Datenvolumes ab, es sei denn, es wird die Verschlüsselungsformatoption „all“ verwendet. 
> - Das Deaktivieren der Verschlüsselung auf virtuellen Linux-Computern wird nur für Datenvolumes unterstützt. Dies wird nicht auf Daten- oder Betriebssystemvolumes unterstützt, wenn das Betriebssystemvolume verschlüsselt wurde.  

## <a name="install-tools-and-connect-to-azure"></a>Installieren von Tools und Herstellen einer Verbindung mit Azure

Azure Disk Encryption kann über die [Azure CLI](/cli/azure) und [Azure PowerShell](/powershell/azure/new-azureps-module-az) aktiviert und verwaltet werden. Zu diesem Zweck müssen Sie die Tools lokal installieren und eine Verbindung mit Ihrem Azure-Abonnement herstellen.

### <a name="azure-cli"></a>Azure CLI

Die [Azure CLI 2.0](/cli/azure) ist ein Befehlszeilentool zum Verwalten von Azure-Ressourcen. Sie wurde entwickelt, um Daten flexible abzufragen, Vorgänge mit langer Ausführungsdauer als nicht blockierende Prozesse zu unterstützen und das Erstellen von Skripts zu vereinfachen. Sie können sie lokal installieren, indem Sie die Schritte unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ausführen.

 

Um [sich bei Ihrem Azure-Konto mit der Azure CLI anzumelden](/cli/azure/authenticate-azure-cli), verwenden Sie den Befehl [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login).

```azurecli
az login
```

Verwenden Sie Folgendes, falls Sie für die Anmeldung einen Mandanten auswählen möchten:
    
```azurecli
az login --tenant <tenant>
```

Wenn Sie über mehrere Abonnements verfügen und ein bestimmtes Abonnement angeben möchten, können Sie Ihre Abonnementliste mit [az account list](/cli/azure/account#az-account-list) abrufen und den Befehl [az account set](/cli/azure/account#az-account-set) zum Angeben verwenden.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Weitere Informationen finden Sie unter [Erste Schritte mit Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
Das [az-Modul von Azure PowerShell](/powershell/azure/new-azureps-module-az) bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden. Azure PowerShell kann mit [Azure Cloud Shell](../../cloud-shell/overview.md) im Browser verwendet oder auf dem lokalen Computer mithilfe der Anleitungen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) installiert werden. 

Falls Sie die lokale Installation bereits durchgeführt haben, verwenden Sie die neueste Version des Azure PowerShell SDKs, um Azure Disk Encryption zu konfigurieren. Laden Sie die neueste Version von [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) herunter.

Um [sich mit Azure PowerShell bei Ihrem Azure-Konto anzumelden](/powershell/azure/authenticate-azureps?view=azps-2.5.0), verwenden Sie das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0).

```powershell
Connect-AzAccount
```

Wenn Sie über mehrere Abonnements verfügen und eines davon angeben möchten, verwenden Sie das Cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription), um die Abonnements aufzulisten, gefolgt vom Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0):

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Wenn Sie das Cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) ausführen, wird überprüft, ob das richtige Abonnement ausgewählt wurde.

Um zu bestätigen, dass die Azure Disk Encryption-Cmdlets installiert sind, verwenden Sie das Cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6):
     
```powershell
Get-command *diskencryption*
```
Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps). 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>Aktivieren der Verschlüsselung auf einem vorhandenen oder aktuell ausgeführten virtuellen Linux-Computer
In diesem Szenario können Sie die Verschlüsselung aktivieren, indem Sie die Resource Manager-Vorlage, PowerShell-Cmdlets oder CLI-Befehle verwenden. Wenn Sie Schemainformationen für die Erweiterung des virtuellen Computers benötigen, finden Sie diese im Artikel [Azure Disk Encryption für Linux](../extensions/azure-disk-enc-linux.md).

>[!IMPORTANT]
 >Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Der Befehl „Set-AzVMDiskEncryptionExtension“ führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
>
>Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Aktivieren der Verschlüsselung auf einem vorhandenen oder aktuell ausgeführten virtuellen Linux-Computer mit der Azure CLI 

Sie können die Datenträgerverschlüsselung auf Ihrer verschlüsselten VHD aktivieren, indem Sie das Befehlszeilentool [Azure CLI](/cli/azure/?view=azure-cli-latest) installieren und verwenden. Azure PowerShell kann mit [Azure Cloud Shell](../../cloud-shell/overview.md) im Browser verwendet oder auf dem lokalen Computer installiert und in einer beliebigen PowerShell-Sitzung verwendet werden. Verwenden Sie die folgenden CLI-Befehle, um die Verschlüsselung auf vorhandenen oder aktuell ausgeführten virtuellen Linux-Computern in Azure zu aktivieren:

Verwenden Sie den Befehl [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-show), um die Verschlüsselung auf einem aktuell ausgeführten virtuellen Computer in Azure zu aktivieren.

- **Verschlüsseln eines ausgeführten virtuellen Computers:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Verschlüsseln eines ausgeführten virtuellen Computers mit KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie den Befehl [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show), um den Verschlüsselungsstatus eines virtuellen Computers zu überprüfen. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Deaktivieren der Verschlüsselung:** Verwenden Sie den Befehl [az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable), um die Verschlüsselung zu deaktivieren. Die Deaktivierung der Verschlüsselung ist nur auf Datenvolumes für virtuelle Linux-Computer zulässig.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>Aktivieren der Verschlüsselung auf einem vorhandenen oder aktuell ausgeführten virtuellen Linux-Computer mit PowerShell
Verwenden Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension), um die Verschlüsselung auf einem aktuell ausgeführten virtuellen Computer in Azure zu aktivieren. Erstellen Sie eine [Momentaufnahme](../../backup/backup-azure-vms-encryption.md), und/oder sichern Sie den virtuellen Computer mit [Azure Backup](snapshot-copy-managed-disk.md), bevor Datenträger verschlüsselt werden. Der Parameter „-skipVmBackup“ ist bereits in den PowerShell-Skripts zum Verschlüsseln einer ausgeführten Linux-VM angegeben.

-  **Verschlüsseln eines ausgeführten virtuellen Computers:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, der virtuelle Computer und der Schlüsseltresor wurden bereits als Voraussetzungen erstellt. Ersetzen Sie „MyVirtualMachineResourceGroup“, „MySecureVM“ und „MySecureVault“ durch Ihre eigenen Werte. Ändern Sie den Parameter „-VolumeType“, um anzugeben, welche Datenträger Sie verschlüsseln.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Verschlüsseln eines ausgeführten virtuellen Computers mit KEK:** Unter Umständen müssen Sie den Parameter „-VolumeType“ hinzufügen, wenn Sie normale Datenträger und nicht den Betriebssystemdatenträger verschlüsseln. 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Überprüfen der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus), um den Verschlüsselungsstatus eines virtuellen Computers zu überprüfen. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Deaktivieren der Datenträgerverschlüsselung:** Verwenden Sie das Cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption), um die Verschlüsselung zu deaktivieren. Die Deaktivierung der Verschlüsselung ist nur auf Datenvolumes für virtuelle Linux-Computer zulässig.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>Aktivieren der Verschlüsselung auf einem vorhandenen oder aktuell ausgeführten virtuellen Linux-Computer mit einer Vorlage

Sie können die Datenträgerverschlüsselung auf einer vorhandenen oder aktuell ausgeführten virtuellen Linux-VM in Azure aktivieren, indem Sie die [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad) verwenden.

1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).

2. Wählen Sie das Abonnement, die Ressourcengruppe, den Ressourcengruppenstandort, die Parameter, die rechtlichen Bedingungen und die Vereinbarung aus. Klicken Sie auf **Erstellen**, um die Verschlüsselung auf dem vorhandenen oder aktuell ausgeführten virtuellen Computer zu aktivieren.

Die folgende Tabelle enthält Resource Manager-Vorlagenparameter für vorhandene oder ausgeführte virtuelle Computer:

| Parameter | BESCHREIBUNG |
| --- | --- |
| vmName | Der Name des virtuellen Computers, der den Verschlüsselungsvorgang ausführt. |
| keyVaultName | Der Name des Schlüsseltresors, in den der Verschlüsselungsschlüssel hochgeladen werden soll. Sie können ihn mit dem Cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` oder dem Azure CLI-Befehl `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` abrufen.|
| keyVaultResourceGroup | Der Name der Ressourcengruppe, die den Schlüsseltresor enthält. |
|  keyEncryptionKeyURL | Die URL des KEK, der zum Verschlüsseln des Verschlüsselungsschlüssels verwendet wird. Dieser Parameter ist optional, wenn Sie in der Dropdownliste „UseExistingKek“ die Option **nokek** auswählen. Wenn Sie in der Dropdownliste „UseExistingKek“ die Option **kek** auswählen, müssen Sie den Wert _keyEncryptionKeyURL_ eingeben. |
| volumeType | Der Typ des Volumes, auf dem der Verschlüsselungsvorgang durchgeführt wird. Gültige Werte sind _OS_, _Data_ und _All_. 
| forceUpdateTag | Dient zum Übergeben eines eindeutigen Werts (beispielsweise einer GUID), wenn die Ausführung des Vorgangs erzwungen werden muss. |
| location | Der Standort für alle Ressourcen. |

Weitere Informationen zum Konfigurieren zur Datenträgerverschlüsselungsvorlage für Linux-VMs finden Sie unter [Azure Disk Encryption für Linux-VMs](../extensions/azure-disk-enc-linux.md).

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Verwenden des Features EncryptFormatAll für Datenträger auf virtuellen Linux-Computern

Mit dem Parameter **EncryptFormatAll** wird die Zeit reduziert, die zum Verschlüsseln von Linux-Datenträgern benötigt wird. Partitionen, die bestimmte Kriterien erfüllen, werden formatiert (zusammen mit ihrem aktuellen Dateisystem) und dann erneut an dem Speicherort eingebunden, an dem sie sich vor der Befehlsausführung befunden haben. Wenn Sie einen Datenträger ausschließen möchten, der die Kriterien erfüllt, können Sie die Bereitstellung vor dem Ausführen des Befehls aufheben.

 Nach der Ausführung dieses Befehls werden alle zuvor eingebundenen Laufwerke formatiert, und die Verschlüsselungsschicht wird über dem jetzt leeren Laufwerk gestartet. Bei Auswahl dieser Option wird auch der temporäre Datenträger verschlüsselt, der an die VM angefügt ist. Nachdem der temporäre Datenträger zurückgesetzt wurde, wird er erneut formatiert und bei der nächsten Gelegenheit von der Azure Disk Encryption-Lösung für die VM erneut verschlüsselt. Nachdem der Ressourcendatenträger verschlüsselt wurde, kann der [Microsoft Azure-Agent für Linux](../extensions/agent-linux.md) den Ressourcendatenträger nicht mehr verwalten und die Auslagerungsdatei nicht mehr aktivieren. Sie können die Auslagerungsdatei jedoch manuell konfigurieren.

>[!WARNING]
> EncryptFormatAll sollte nicht verwendet werden, wenn sich auf den Datenvolumes der VM erforderliche Daten befinden. Sie können Datenträger von der Verschlüsselung ausschließen, indem Sie deren Bereitstellung aufheben. Sie sollten EncryptFormatAll zuerst auf einer Test-VM ausprobieren und sich mit dem Featureparameter und dessen Auswirkungen vertraut machen, bevor Sie das Feature auf einer VM für die Produktion einsetzen. Mit der EncryptFormatAll-Option wird der Datenträger formatiert, und alle darauf befindlichen Daten gehen verloren. Stellen Sie vor dem Fortfahren sicher, dass die Bereitstellung der auszuschließenden Datenträger ordnungsgemäß aufgehoben wird. </br></br>
 >Wenn Sie diesen Parameter festlegen, während Sie die Verschlüsselungseinstellungen aktualisieren, wird vor der eigentlichen Verschlüsselung ggf. ein Neustart durchgeführt. In diesem Fall sollten Sie auch den Datenträger, der formatiert werden soll, aus der FSTAB-Datei entfernen. Entsprechend sollten Sie die Partition, die formatiert und verschlüsselt werden soll, der FSTAB-Datei hinzufügen, bevor Sie den Verschlüsselungsvorgang initiieren. 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll-Kriterien
Der Parameter durchläuft alle Partitionen und verschlüsselt sie, solange **alle** hier aufgeführten Kriterien erfüllt sind:
- Ist keine Stamm-, Betriebssystem- oder Startpartition
- Ist nicht bereits verschlüsselt
- Ist kein BEK-Volume
- Ist kein RAID-Volume
- Ist kein LVM-Volume
- Ist bereitgestellt

Verschlüsselt die Datenträger, aus denen sich das RAID- oder LVM-Volume zusammensetzt, nicht das RAID- oder LVM-Volume selbst.

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Verwenden des Parameters EncryptFormatAll mit der Azure CLI
Verwenden Sie den Befehl [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable), um die Verschlüsselung auf einem aktuell ausgeführten virtuellen Computer in Azure zu aktivieren.

-  **Verschlüsseln eines ausgeführten virtuellen Computers unter Verwendung von „EncryptFormatAll“:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>Verwenden des Parameters EncryptFormatAll mit einem PowerShell-Cmdlet
Verwenden Sie das Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) mit dem Parameter „EncryptFormatAll“. 

**Verschlüsseln eines ausgeführten virtuellen Computers unter Verwendung von „EncryptFormatAll“:** Als Beispiel werden mit dem unten angegebenen Skript Ihre Variablen initialisiert und das Cmdlet „Set-AzVMDiskEncryptionExtension“ mit dem Parameter „EncryptFormatAll“ ausgeführt. Die Ressourcengruppe, der virtuelle Computer und der Schlüsseltresor wurden bereits als Voraussetzungen erstellt. Ersetzen Sie „MyVirtualMachineResourceGroup“, „MySecureVM“ und „MySecureVault“ durch Ihre eigenen Werte.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>Verwenden des Parameters EncryptFormatAll mit Logical Volume Manager (LVM) 
Wir empfehlen Ihnen, ein LVM-on-crypt-Setup zu verwenden. Ersetzen Sie für alle folgenden Beispiele den Gerätepfad und die Bereitstellungspunkte durch die Werte für Ihren jeweiligen Anwendungsfall. Dieses Setup kann wie folgt durchgeführt werden:

1.  Fügen Sie die Datenträger hinzu, aus denen sich die VM zusammensetzt.

1. Formatieren Sie diese Datenträger, stellen Sie sie bereit, und fügen Sie sie der FSTAB-Datei hinzu.

1. Wählen Sie einen Partitionsstandard aus, erstellen Sie eine Partition, die das gesamte Laufwerk umfasst, und formatieren Sie dann die Partition. Hier verwenden wir von Azure generierte symlinks. Durch die Verwendung von symlinks werden Probleme in Bezug auf sich ändernde Gerätenamen vermieden. Weitere Informationen finden Sie im Artikel zur [Behandlung von Problemen mit Gerätenamen](../troubleshooting/troubleshoot-device-names-problems.md).
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. Stellen Sie die Datenträger bereit:

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    Fügen Sie zur fstab-Datei hinzu:

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. Führen Sie das Azure PowerShell-Cmdlet [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-3.8.0) mir „-EncryptFormatAll“ aus, um diese Datenträger zu verschlüsseln.

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    Wenn Sie einen Schlüssel-Verschlüsselungsschlüssel (Key Encryption Key, KEK) verwenden möchten, übergeben Sie den URI Ihres KEK und die ResourceID Ihres Schlüsseltresors an die Parameter „-KeyEncryptionKeyUrl“ bzw. „-KeyEncryptionKeyVaultId“:

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. Richten Sie LVM für diese neuen Datenträger ein. Beachten Sie, dass die verschlüsselten Laufwerke entsperrt werden, nachdem das Starten der VM abgeschlossen wurde. Die LVM-Bereitstellung muss nachfolgend also auch verzögert werden.

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>Neue virtuelle Computer, die aus einer vom Kunden verschlüsselten VHD und mit Verschlüsselungsschlüsseln erstellt wurden
In diesem Szenario können Sie die Verschlüsselung mithilfe von PowerShell-Cmdlets oder CLI-Befehlen aktivieren. 

Verwenden Sie die Anleitungen in den Azure Disk Encryption-Skripts zum Vorbereiten von vorverschlüsselten Images, die in Azure verwendet werden können. Nach dem Erstellen des Images können Sie die Schritte im nächsten Abschnitt ausführen, um eine verschlüsselte Azure-VM zu erstellen.

* [Vorbereiten einer vorverschlüsselten Linux-VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Es ist obligatorisch, außerhalb von Azure Disk Encryption und vor der Aktivierung von Azure Disk Encryption eine Momentaufnahme bzw. Sicherung einer VM zu erstellen, die auf einem verwalteten Datenträger basiert. Sie können über das Portal eine Momentaufnahme des verwalteten Datenträgers erstellen oder [Azure Backup](../../backup/backup-azure-vms-encryption.md) verwenden. Mit Sicherungen ist dafür gesorgt, dass eine Wiederherstellungsoption verfügbar ist, falls während der Verschlüsselung ein unerwarteter Fehler auftritt. Nach dem Erstellen einer Sicherung kann das Cmdlet „Set-AzVMDiskEncryptionExtension“ verwendet werden, um verwaltete Datenträger durch das Angeben des Parameters „-skipVmBackup“ zu verschlüsseln. Der Befehl „Set-AzVMDiskEncryptionExtension“ führt für VMs, die auf verwalteten Datenträgern basieren, zu einem Fehler, bis eine Sicherung erstellt und dieser Parameter angegeben wurde. 
>
> Das Verschlüsseln bzw. Deaktivieren der Verschlüsselung kann dazu führen, dass die VM neu gestartet wird. 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Verwenden von Azure PowerShell zum Verschlüsseln von virtuellen Computern mit vorverschlüsselten VHDs 
Sie können die Datenträgerverschlüsselung auf einer verschlüsselten VHD aktivieren, indem Sie das PowerShell-Cmdlet [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) verwenden. Im folgenden Beispiel sind einige häufig verwendete Parameter angegeben. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivieren der Verschlüsselung auf einem neu hinzugefügten Datenträger

Sie können einen neuen Datenträger hinzufügen, indem Sie den Befehl [az vm disk attach](add-disk.md) oder das [Azure-Portal](attach-disk-portal.md) verwenden. Bevor Sie die Verschlüsselung durchführen können, müssen Sie den neu angefügten Datenträger bereitstellen. Sie müssen die Verschlüsselung des Datenträgers anfordern, da das Laufwerk nicht genutzt werden kann, während die Verschlüsselung durchgeführt wird. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger per Azure CLI

 Wenn der virtuelle Computer zuvor mit „All“ verschlüsselt war, sollte der Parameter --volume-type auf „All“ belassen werden. Bei „All“ sind sowohl Betriebssystem als auch Datenträger enthalten. Wenn der virtuelle Computer zuvor mit dem Volumetyp „OS“ verschlüsselt war, sollte der Parameter --volume-type in „All“ geändert werden, damit sowohl das Betriebssystem als auch der neue Datenträger enthalten sind. Wenn der virtuelle Computer nur mit dem Volumetyp „Data“ verschlüsselt war, kann er auf „Data“ belassen werden, wie es nachfolgend gezeigt ist. Das Hinzufügen und Anfügen eines neuen Datenträgers an einen virtuellen Computer ist keine ausreichende Vorbereitung für die Verschlüsselung. Der neu angefügte Datenträger muss außerdem formatiert und vor dem Aktivieren der Verschlüsselung ordnungsgemäß im virtuellen Computer bereitgestellt werden. Unter Linux muss der Datenträger in „/etc/fstab“ mit einem [persistenten Blockgerätenamen](../troubleshooting/troubleshoot-device-names-problems.md) bereitgestellt werden.  

Im Gegensatz zur PowerShell-Syntax erfordert die Befehlszeilenschnittstelle vom Benutzer keine Angabe einer eindeutigen Sequenzversion beim Aktivieren der Verschlüsselung. Die Befehlszeilenschnittstelle wird automatisch generiert und verwendet einen eigenen eindeutigen Sequenzversionswert.

-  **Verschlüsseln von Datenvolumes eines ausgeführten virtuellen Computers:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Verschlüsseln von Datenvolumes eines ausgeführten virtuellen Computers mit KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivieren der Verschlüsselung für einen neu hinzugefügten Datenträger mit Azure PowerShell
 Bei Verwendung von PowerShell zum Verschlüsseln eines neuen Datenträgers für Linux muss eine neue Sequenzversion angegeben werden. Die Sequenzversion muss eindeutig sein. Mit dem unten angegebenen Skript wird eine GUID für die Sequenzversion generiert. Erstellen Sie eine [Momentaufnahme](../../backup/backup-azure-vms-encryption.md), und/oder sichern Sie den virtuellen Computer mit [Azure Backup](snapshot-copy-managed-disk.md), bevor Datenträger verschlüsselt werden. Der Parameter „-skipVmBackup“ ist bereits in den PowerShell-Skripts zum Verschlüsseln eines neu hinzugefügten Datenträgers angegeben.
 

-  **Verschlüsseln von Datenvolumes eines ausgeführten virtuellen Computers:** Das folgende Skript initialisiert Ihre Variablen und führt das Cmdlet „Set-AzVMDiskEncryptionExtension“ aus. Die Ressourcengruppe, der virtuelle Computer und der Schlüsseltresor wurden bereits bei der Vorbereitung erstellt. Ersetzen Sie „MyVirtualMachineResourceGroup“, „MySecureVM“ und „MySecureVault“ durch Ihre eigenen Werte. Zulässige Werte für den Parameter „-VolumeType“ sind „All“, „OS“ und „Data“. Wenn der virtuelle Computer zuvor mit dem Volumetyp „OS“ oder „All“ verschlüsselt war, sollte der Parameter --Volume-type in „All“ geändert werden, damit sowohl das Betriebssystem als auch der neue Datenträger enthalten sind.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Verschlüsseln von Datenvolumes eines ausgeführten virtuellen Computers mit KEK:** Zulässige Werte für den Parameter „-VolumeType“ sind „All“, „OS“ und „Data“. Wenn der virtuelle Computer zuvor mit dem Volumetyp „OS“ oder „All“ verschlüsselt war, sollte der Parameter „-VolumeType“ in „All“ geändert werden, damit sowohl das Betriebssystem als auch der neue Datenträger enthalten sind.

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Die Syntax für den Wert des Parameters „disk-encryption-keyvault“ ist die vollständige Bezeichnerzeichenfolge: /subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Die Syntax für den Wert des Parameters „key-encryption-key“ ist der vollständige URI für den KEK, z.B.: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Deaktivieren der Verschlüsselung für virtuelle Linux-Computer
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien

Die folgenden Linux-Szenarien,- Features und -Technologien werden von Azure Disk Encryption nicht unterstützt:

- Verschlüsseln von virtuellen Computern der Ebene „Standard“ und von virtuellen Computern, die mithilfe der klassischen Erstellungsmethode für virtuelle Computer erstellt wurden
- Deaktivieren der Verschlüsselung für ein Betriebssystemlaufwerk oder Datenlaufwerk auf einem virtuellen Linux-Computer, wenn das Betriebssystemlaufwerk verschlüsselt ist
- Verschlüsseln des Betriebssystemlaufwerks für Linux-VM-Skalierungsgruppen
- Verschlüsseln benutzerdefinierter Images auf virtuellen Linux-Computern
- Integration mit einem lokalen Schlüsselverwaltungssystem
- Azure Files (freigegebenes Dateisystem)
- Network File System (NFS)
- Dynamische Volumes
- Kurzlebige Betriebssystemdatenträger
- Verschlüsselung freigegebener/verteilter Dateisysteme, einschließlich der folgenden, aber nicht auf diese begrenzt: DFS, GFS, DRDB und CephFS.
- Verschieben eines verschlüsselten virtuellen Computers in ein anderes Abonnement oder in eine andere Region
- Erstellen eines Images oder einer Momentaufnahme einer verschlüsselten VM und dessen oder deren Verwendung zum Bereitstellen weiterer VMs
- Kernel-Absturzabbild (kdump).
- Oracle-ACFS (ASM-Clusterdateisystem).
- Gen2-VMs (siehe: [Unterstützung für VMs der Generation 2 in Azure](../generation-2.md#generation-1-vs-generation-2-capabilities)).
- Die NVMe-Datenträger der VMs der Lsv2-Serie (siehe: [LSv2-Serie](../lsv2-series.md)).
- Eine VM mit „geschachtelten Bereitstellungspunkten“, also mehrere Bereitstellungspunkte in einem einzelnen Pfad (z. B. „/1stmountpoint/data/2stmountpoint“).
- Eine VM mit einem Datenlaufwerk, das über einem Betriebssystemordner eingebunden ist
- VMs der M-Serie mit Datenträgern mit Schreibbeschleunigung
- Anwenden von ADE auf eine VM, die mit [serverseitiger Verschlüsselung mit kundenseitig verwalteten Schlüsseln](../disk-encryption.md) (SSE + CMK) verschlüsselte Datenträger aufweist. Das Anwenden von SSE + CMK auf einen Datenträger auf einer mit ADE verschlüsselten VM ist ebenfalls ein nicht unterstütztes Szenario.
- Migrieren einer VM, die mit ADE verschlüsselt ist oder **jemals** mit ADE verschlüsselt war, zur [serverseitigen Verschlüsselung mit kundenseitig verwalteten Schlüsseln](../disk-encryption.md).
- [Azure-VM-Größen ohne lokalen temporären Datenträger](../azure-vms-no-temp-disk.md), insbesondere Dv4, Dsv4, Ev4 und Esv4.
- Verschlüsseln von VMs in Failoverclustern.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)
- [Azure Disk Encryption-Beispielskripts](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption-Problembehandlung](disk-encryption-troubleshooting.md)