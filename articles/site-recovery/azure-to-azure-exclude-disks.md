---
title: Azure Site Recovery – Ausschließen von Datenträgern während der Replikation virtueller Azure-Computer mit Azure PowerShell | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Datenträger für virtuelle Azure-Computer mit Azure Site Recovery über Azure PowerShell ausschließen.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160303"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Ausschließen von Datenträgern von der PowerShell-Replikation von Azure-VMs

In diesem Artikel wird beschrieben, wie Datenträger von der Replikation virtueller Azure-Computer ausgeschlossen werden. Durch diesen Ausschluss können die beanspruchte Replikationsbandbreite oder die zielseitigen Ressourcen optimiert werden, die solche Datenträger verwenden. Derzeit wird diese Funktion nur über Azure PowerShell bereitgestellt.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Vorbereitung:

- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten zur Notfallwiederherstellung](azure-to-azure-architecture.md) verstehen.
- Überprüfen Sie die [Supportanforderungen](azure-to-azure-support-matrix.md) für alle Komponenten.
- Stellen Sie sicher, dass Sie über das PowerShell-Az-Modul AzureRm verfügen. Informationen zum Installieren oder Aktualisieren von PowerShell finden Sie unter [Installieren des Azure PowerShell-Moduls](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Stellen Sie sicher, dass Sie mindestens einmal einen Recovery Services-Tresor erstellt und virtuelle Computer geschützt haben. Wenn Sie diese Schritte noch nicht ausgeführt haben, führen Sie das Verfahren unter [Einrichten der Notfallwiederherstellung für virtuelle Azure-Computer über Azure PowerShell](azure-to-azure-powershell.md) aus.

## <a name="why-exclude-disks-from-replication"></a>Gründe für das Ausschließen von Datenträgern von der Replikation
Es kann folgende Gründe für das Ausschließen von Datenträgern von der Replikation geben:

- Ihr virtueller Computer hat die [Azure Site Recovery-Grenzwerte zur Replikation von Datenänderungsraten](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix) erreicht.

- Die auf dem ausgeschlossenen Datenträger verarbeiteten Daten sind nicht wichtig oder müssen nicht repliziert werden.

- Sie können Speicher- und Netzwerkressourcen sparen, indem Sie diese Daten nicht replizieren.

## <a name="how-to-exclude-disks-from-replication"></a>Ausschließen von Datenträgern von der Replikation

In unserem Beispiel replizieren wir einen virtuellen Computer mit einem Datenträger mit dem Betriebssystem sowie drei Datenträgern mit Daten von der Region „USA, Osten“ in die Region „USA, Westen 2“. Der Name des virtuellen Computers lautet *AzureDemoVM*. Wir schließen den Datenträger 1 aus und behalten die Datenträger 2 und 3 bei.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Abrufen von Details der zu replizierenden virtuellen Computer

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Rufen Sie Details zu den Datenträgern des virtuellen Computers ab. Diese Informationen werden später verwendet, wenn Sie die Replikation des virtuellen Computers starten.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replizieren virtueller Azure-Computer

Im folgenden Beispiel wird davon ausgegangen, dass Sie bereits über ein Cachespeicherkonto, eine Replikationsrichtlinie und Zuordnungen verfügen. Wenn Sie diese Schritte noch nicht ausgeführt haben, führen Sie das Verfahren unter [Einrichten der Notfallwiederherstellung für virtuelle Azure-Computer über Azure PowerShell](azure-to-azure-powershell.md) aus.

Replizieren Sie einen virtuellen Azure-Computer mit *verwalteten Datenträgern*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Nachdem der Replikationsvorgang erfolgreich gestartet wurde, werden Daten des virtuellen Computers in die Wiederherstellungsregion repliziert.

Sie finden die replizierten VMs im Azure-Portal unter „Replizierte Elemente“.

Zu Beginn des Replikationsprozesses wird durch Seeding eine Kopie der zu replizierenden Datenträger des virtuellen Computers in die Wiederherstellungsregion übernommen. Diese Phase wird als erste Replikationsphase bezeichnet.

Nachdem die erste Replikation abgeschlossen ist, beginnt die Phase der differenziellen Synchronisierung. Jetzt ist der virtuelle Computer geschützt. Wählen Sie den geschützten virtuellen Computer aus, um festzustellen, ob Datenträger ausgeschlossen wurden.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](site-recovery-test-failover-to-azure.md) zum Ausführen eines Testfailovers.
