---
title: Angeben von Marketplace-Erwerbsplaninformationen mithilfe von Azure PowerShell
description: Hier erfahren Sie, wie Sie Azure Marketplace-Erwerbsplaninformationen angeben, wenn Sie Images in einer Shared Image Gallery-Instanz erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/07/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2a971f6e56f441ab05a6a9b483eeef990d3ea31f
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903750"
---
# <a name="supply-azure-marketplace-purchase-plan-information-when-creating-images"></a>Bereitstellen von Azure Marketplace-Erwerbsplaninformationen beim Erstellen von Images

Wenn Sie ein Image in einem Katalog für freigegebenen Images erstellen und dabei eine Quelle verwenden, die ursprünglich aus einem Azure Marketplace-Image erstellt wurde, müssen Sie möglicherweise die Erwerbsplaninformationen nachverfolgen. In diesem Artikel wird erläutert, wie Sie Erwerbsplaninformationen für eine VM suchen und diese dann beim Erstellen einer Imagedefinition verwenden. Es wird ebenfalls beschrieben, wie Sie die Informationen aus der Imagedefinition verwenden, um die Bereitstellung der Erwerbsplaninformationen beim Erstellen einer VM für ein Image zu vereinfachen.

Weitere Informationen zum Suchen und Verwenden von Marketplace-Images finden Sie unter [Suchen und Verwenden von Azure Marketplace-Images](./windows/cli-ps-findimage.md).


## <a name="get-the-source-vm-information"></a>Abrufen der Informationen zur Quell-VM
Wenn Sie noch über die ursprüngliche VM verfügen, können Sie Informationen zum Plannamen, Herausgeber und dem Produkt mithilfe von „Get-AzVM“ von dieser VM abrufen. Dieses Beispiel ruft eine VM namens *myVM* in der Ressourcengruppe *myResourceGroup* ab und zeigt dann die Erwerbsplaninformationen für die VM an.

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

## <a name="create-the-image-definition"></a>Erstellen der Imagedefinition

Rufen Sie den Imagekatalog ab, in dem Sie das Image speichern möchten. Sie können zuerst aller Kataloge auflisten.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

Erstellen Sie dann Variablen für den Katalog, den Sie verwenden möchten. In diesem Beispiel erstellen wir die Variable `$gallery` für *myGallery* in der Ressourcengruppe *myGalleryRG*.

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myGalleryRG
```

Erstellen Sie mithilfe der Parameter `-PurchasePlanPublisher`, `-PurchasePlanProduct` und `-PurchasePlanName` die Imagedefinition.

```azurepowershell-interactive
 $imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Linux `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU' `
   -PurchasePlanPublisher $vm.Plan.Publisher `
   -PurchasePlanProduct $vm.Plan.Product `
   -PurchasePlanName  $vm.Plan.Name
```

Erstellen Sie dann mit [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion) Ihre Imageversion. Sie können eine Imageversion aus einer [VM](image-version-vm-powershell.md#create-an-image-version), einem [verwalteten Image](image-version-managed-image-powershell.md#create-an-image-version), einer [VHD bzw. Momentaufnahme](image-version-snapshot-powershell.md#create-an-image-version) oder einer [anderen Imageversion](image-version-another-gallery-powershell.md#create-the-image-version) erstellen. 


## <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Wenn Sie die VM aus dem Image erstellen, können Sie die Informationen aus der Imagedefinition verwenden, um mithilfe von [Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) die Herausgeberinformationen zu übergeben.


```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "mySIGPubVM"
$location = "West Central US"
$vmName = "mySIGPubVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version. Set-AZVMPlan is used to pass the plan information in for the VM.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2   | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Set-AzVMPlan `
     -Publisher $imageDefinition.PurchasePlan.Publisher `
     -Product $imageDefinition.PurchasePlan.Product `
     -Name $imageDefinition.PurchasePlan.Name | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create the virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Suchen und Verwenden von Marketplace-Images finden Sie unter [Suchen und Verwenden von Azure Marketplace-Images](./windows/cli-ps-findimage.md).
