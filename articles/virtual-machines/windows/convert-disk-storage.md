---
title: Konvertieren verwalteter Azure-Datenträger von Storage Standard in Storage Premium (oder umgekehrt) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie verwaltete Azure-Datenträger mithilfe von Azure PowerShell von Storage Standard in Storage Premium konvertieren (oder umgekehrt).
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 5687e6d0094083a9ee58455cc72b0b2e4da32d65
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417152"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Aktualisieren des Speichertyps eines verwalteten Datenträgers

Es gibt vier Typen verwalteter Azure-Datenträger: Azure SSD Ultra (Vorschauversion), SSD Premium, SSD Standard und HDD Standard. Sie können basierend auf Ihren Leistungsanforderungen zwischen den drei allgemein verfügbaren Datenträgertypen (SSD Premium, SSD Standard und HDD Standard) wechseln. Derzeit ist noch kein Wechsel von oder zu SSD Ultra möglich. Sie müssen in diesem Fall einen neuen Datenträgertyp bereitstellen.

Diese Funktion wird für nicht verwaltete Datenträger nicht unterstützt. Sie können jedoch problemlos [nicht verwaltete Datenträger in verwaltete Datenträger konvertieren](convert-unmanaged-to-managed-disks.md), um zwischen den Datenträgertypen wechseln zu können.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Da für die Konvertierung ein Neustart des virtuellen Computers (Virtual Machine, VM) erforderlich ist, sollten Sie die Migration Ihres Datenträgerspeichers während eines bereits vorhandenen Wartungsfensters durchführen.
* Handelt es sich bei Ihrem Datenträger um einen nicht verwalteten Datenträger, müssen Sie ihn zunächst [in einen verwalteten Datenträger konvertieren](convert-unmanaged-to-managed-disks.md), um zwischen den Speicheroptionen wechseln zu können.

## <a name="switch-all-managed-disks-of-a-vm-between-premium-and-standard"></a>Umstellen aller verwalteten Datenträger eines virtuellen Computers auf Premium oder Standard

Dieses Beispiel zeigt, wie Sie alle Datenträger eines virtuellen Computers von Storage Standard in Storage Premium konvertieren (oder umgekehrt). Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel erfolgt auch ein Wechsel zu einer Größe, die Storage Premium unterstützt:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'

# Premium capable size
# Required only if converting storage from Standard to Premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzDiskUpdateConfig –AccountType $storageType
        Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="switch-individual-managed-disks-between-standard-and-premium"></a>Umstellen einzelner verwalteter Datenträger auf Standard oder Premium

Für Ihre Entwicklungs-/Testworkload sollten Sie eine Kombination aus Standard- und Premium-Datenträgern verwenden, um Kosten zu sparen. Sie haben auch die Möglichkeit, nur die Datenträger upzugraden, die eine höhere Leistung erfordern. Dieses Beispiel zeigt, wie Sie einen einzelnen VM-Datenträger von Storage Standard in Storage Premium konvertieren (oder umgekehrt). Zur Verwendung von verwalteten Premium-Datenträgern muss Ihre VM eine [VM-Größe](sizes.md) verwenden, die Premium Storage unterstützt. In diesem Beispiel wird auch gezeigt, wie Sie zu einer Größe wechseln, die Storage Premium unterstützt:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and Premium_LRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Change the VM size to a size that supports Premium storage
# Skip this step if converting storage from Premium to Standard
$vm.HardwareProfile.VmSize = $size
Update-AzVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="convert-managed-disks-from-standard-to-premium-in-the-azure-portal"></a>Konvertieren verwalteter Datenträger von Standard in Premium über das Azure-Portal

Folgen Sie diesen Schritten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Portal in der Liste **Virtuelle Computer** den virtuellen Computer aus.
3. Sollte der virtuelle Computer nicht beendet sein, wählen Sie im oberen Bereich der **Übersicht** für den virtuellen Computer die Option **Beenden** aus, und warten Sie, bis der virtuelle Computer beendet wurde.
3. Wählen Sie im Bereich für den virtuellen Computer im Menü die Option **Datenträger** aus.
4. Wählen Sie den Datenträger aus, den Sie konvertieren möchten.
5. Wählen Sie im Menü die Option **Konfiguration** aus.
6. Ändern Sie den **Kontotyp** von **HDD Standard** in **SSD Premium**.
7. Klicken Sie auf **Speichern**, und schließen Sie den Datenträgerbereich.

Der Datenträgertyp wird umgehend konvertiert. Sie können Ihren virtuellen Computer nach der Konvertierung neu starten.

## <a name="switch-managed-disks-between-standard-hdd-and-standard-ssd"></a>Umstellen verwalteter Datenträger von HDD Standard auf SSD Standard (oder umgekehrt) 

Dieses Beispiel zeigt, wie Sie einen einzelnen VM-Datenträger von HDD Standard in SSD Standard konvertieren (oder umgekehrt).

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine schreibgeschützte Kopie eines virtuellen Computers mithilfe einer [Momentaufnahme](snapshot-copy-managed-disk.md).
