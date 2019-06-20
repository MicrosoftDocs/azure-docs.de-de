---
title: Ändern der Größe von virtuellen Azure-Computern in Azure mithilfe von PowerShell | Microsoft-Dokumentation
description: Ändern der Größe eines virtuellen Windows-Computers, der im Resource Manager-Bereitstellungsmodell erstellt wurde, mithilfe von Azure Powershell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 057ff274-6dad-415e-891c-58f8eea9ed78
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: cynthn
ms.openlocfilehash: 3dc6ea7419e3ee1f24d19447a7a33e5556ffc70f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64688720"
---
# <a name="resize-a-windows-vm"></a>Ändern der Größe eines virtuellen Windows-Computers

In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure Powershell einen virtuellen Computer auf eine andere [VM-Größe](sizes.md) verschieben.

Nachdem Sie einen virtuellen Computer (virtual machine; VM) erstellen, können Sie den virtuellen Computer zentral hoch- oder herunterskalieren, indem Sie die VM-Größe ändern. In einigen Fällen müssen Sie zuerst die Zuordnung des virtuellen Computers aufheben. Dies ist möglicherweise der Fall, falls die neue Größe auf dem Hardwarecluster nicht verfügbar ist, auf dem die VM aktuell gehostet wird.

Wenn Ihr virtueller Computer Storage Premium verwendet, stellen Sie sicher, dass Sie für die Größe eine **s**-Version auswählen, damit Storage Premium unterstützt wird. Wählen Sie z. B. Standard_E4**s**_v3 statt Standard_E4_v3 aus.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Ändern der Größe eines virtuellen Windows-Computers nicht in einer Verfügbarkeitsgruppe

Legen Sie einige Variablen fest. Ersetzen Sie die Werte durch Ihre eigenen Informationen.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Listet die VM-Größen auf, die auf dem Hardwarecluster verfügbar sind, auf dem die VM gehostet wird. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Führen Sie die folgenden Befehle aus, um die Größe des virtuellen Computers zu ändern, wenn die gewünschte Größe aufgelistet ist. Wenn die gewünschte Größe nicht aufgeführt ist, fahren Sie mit Schritt 3 fort.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Führen Sie die folgenden Befehle aus, wenn die gewünschte Größe nicht aufgelistet ist. Damit heben Sie die Zuordnung des virtuellen Computers auf, ändern seine Größe, und starten ihn neu. Ersetzen Sie **\<newVMsize>** durch die gewünschte Größe.
   
```powershell
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Die Aufhebung der Zuordnung der VM gibt alle dynamischen IP-Adressen frei, die der VM zugewiesen sind. Das Betriebssystem und die Datenträger sind nicht betroffen. 
> 
> 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Ändern der Größe eines virtuellen Windows-Computers in einer Verfügbarkeitsgruppe

Wenn die neue Größe für einen virtuellen Computer in einer Verfügbarkeitsgruppe nicht im Hardwarecluster verfügbar ist, auf dem der virtuelle Computer derzeit gehostet wird, muss bei allen virtuellen Computern in der Verfügbarkeitsgruppe die Zuordnung aufgehoben werden, um die Größe des virtuellen Computers ändern zu können. Sie müssen möglicherweise auch die Größe der anderen virtuellen Computer in der Verfügbarkeitsgruppe aktualisieren, nachdem die Größe eines virtuellen Computers geändert wurde. Führen Sie die folgenden Schritte zum Ändern der Größe eines virtuellen Computers in einer Verfügbarkeitsgruppe aus.

```powershell
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Listet die VM-Größen auf, die auf dem Hardwarecluster verfügbar sind, auf dem die VM gehostet wird. 
   
```powershell
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Führen Sie die folgenden Befehle aus, um die Größe der VM zu ändern, sofern die gewünschte Größe aufgelistet ist. Wenn sie nicht aufgelistet ist, fahren Sie mit dem nächsten Abschnitt fort.
   
```powershell
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```
    
Wenn die gewünschte Größe nicht aufgelistet ist, fahren Sie mit den folgenden Schritten fort. Damit heben Sie die Zuordnung aller virtuellen Computer in der Verfügbarkeitsgruppe auf, skalieren die VMs, und starten sie neu.

Beenden Sie alle virtuellen Computer in der Verfügbarkeitsgruppe.
   
```powershell
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
    } 
```

Skalieren Sie alle virtuellen Computer in der Verfügbarkeitsgruppe und starten sie neu.
   
```powershell
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup
$vmIds = $as.VirtualMachinesReferences
  foreach ($vmId in $vmIDs){
    $string = $vmID.Id.Split("/")
    $vmName = $string[8]
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    $vm.HardwareProfile.VmSize = $newSize
    Update-AzVM -ResourceGroupName $resourceGroup -VM $vm
    Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
    }
```

## <a name="next-steps"></a>Nächste Schritte

Führen Sie mehrere VM-Instanzen aus, und skalieren Sie diese zentral hoch, um zusätzliche Skalierbarkeit zu erhalten. Weitere Informationen finden Sie unter [Automatisches Skalieren von Windows-Computern in einer VM-Skalierungsgruppe](../../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).

