---
title: Anfügen eines Datenträgers an einen virtuellen Windows-Computer in Azure mithilfe von PowerShell | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Anfügen eines neuen oder vorhandenen Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell und dem Resource Manager-Bereitstellungsmodell.
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
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 6a20dac0f89390f1229c7a71793814dc9f9397c1
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66727846"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell

In diesem Artikel wird beschrieben, wie Sie mithilfe von PowerShell sowohl neue als auch vorhandene Datenträger an einen virtuellen Windows-Computer anfügen. 

Lesen Sie zunächst diese Tipps:

* Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Für die Verwendung von SSD Premium-Datenträgern benötigen Sie einen [virtuellen Computer mit einem für Storage Premium geeigneten Typ](sizes-memory.md), z. B. die DS- oder GS-Serie.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Hinzufügen eines leeren Datenträgers zu einem virtuellen Computer

Dieses Beispiel zeigt, wie Sie einem vorhandenen virtuellen Computer einen leeren Datenträger hinzufügen.

### <a name="using-managed-disks"></a>Verwenden verwalteter Datenträger

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Verwenden von verwalteten Datenträgern in einer Verfügbarkeitszone

Verwenden Sie zum Erstellen eines Datenträgers in einer Verfügbarkeitszone [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) mit dem `-Zone`-Parameter. Im folgenden Beispiel wird ein Datenträger in Zone *1* erstellt.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Datenträger initialisieren

Nachdem Sie einen leeren Datenträger hinzugefügt haben, müssen Sie ihn initialisieren. Um den Datenträger zu initialisieren, können Sie sich bei einem virtuellen Computer anmelden und die Datenträgerverwaltung verwenden. Wenn Sie bei der Erstellung [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) aktiviert und ein Zertifikat auf dem virtuellen Computer installiert haben, können Sie den Datenträger mit einem PowerShell-Remoteaufruf initialisieren. Alternativ können Sie auch eine benutzerdefinierte Skripterweiterung verwenden:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

Die Skriptdatei kann Code für die Initialisierung der Datenträger enthalten. Beispiel:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Hinzufügen eines vorhandenen Datenträgers zu einem virtuellen Computer

Sie können einen vorhandenen verwalteten Datenträger als Datenträger für Daten an einen virtuellen Computer anfügen.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Nächste Schritte

Erstellen einer [Momentaufnahme](snapshot-copy-managed-disk.md)
