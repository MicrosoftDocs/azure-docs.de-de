---
title: 'Konfigurieren der Routingpräferenz für einen virtuellen Computer: Azure PowerShell'
description: Hier erfahren Sie, wie Sie mithilfe von Azure PowerShell einen virtuellen Computer mit öffentlicher IP-Adresse und Routingpräferenz erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: mnayak
ms.openlocfilehash: 65f5b27ecc7b526c4b3e4f5ed62ac90149df1ce1
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510606"
---
# <a name="configure-routing-preference-for-a-vm-using-azure-powershell"></a>Konfigurieren der Routingpräferenz für einen virtuellen Computer mithilfe von Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie die Routingpräferenz für einen virtuellen Computer konfigurieren. Der Internetdatenverkehr vom virtuellen Computer wird über das ISP-Netzwerk geleitet, wenn Sie **Internet** als Option für die Routingpräferenz auswählen. Das Standardrouting erfolgt über das globale Netzwerk von Microsoft.

In diesem Artikel wird gezeigt, wie Sie einen virtuellen Computer mit einer öffentlichen IP-Adresse erstellen, die zum Weiterleiten von Datenverkehr über das ISP-Netzwerk unter Verwendung von Azure PowerShell festgelegt ist.

> [!IMPORTANT]
> „Routingpräferenz“ ist zurzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
1. Wenn Sie Cloud Shell bereits verwenden, fahren Sie mit Schritt 2 fort. Öffnen Sie eine Befehlssitzung, und melden Sie sich mit `Connect-AzAccount` in Azure an.
2. Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe in der Azure-Region „USA, Osten“ erstellt:

    ```azurepowershell
    $rg = New-AzResourceGroup -Name MyResourceGroup -Location EastUS
    ```

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Um über das Internet auf Ihre virtuellen Computer zugreifen zu können, benötigen Sie öffentliche IP-Adressen. Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) öffentliche IP-Adressen. Im folgenden Beispiel wird eine öffentliche IPv4-IP-Adresse mit dem Namen *MyPublicIP* und dem Routingpräferenztyp *Internet* in der Ressourcengruppe *MyResourceGroup* und der Region *USA, Osten* erstellt:

```azurepowershell-interactive
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

## <a name="create-network-resources"></a>Erstellen von Netzwerkressourcen

Bevor Sie einen virtuellen Computer bereitstellen, müssen Sie unterstützende Netzwerkressourcen erstellen: Netzwerksicherheitsgruppe, virtuelles Netzwerk und eine virtuelle NIC.

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe. Im folgenden Beispiel wird eine NSG (Network Security Group, Netzwerksicherheitsgruppe) namens *myNSG* erstellt:

```azurepowershell
$nsg = New-AzNetworkSecurityGroup `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myNSG"
```

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVNET* mit einem Subnetz namens *mySubNet* erstellt:

### <a name="create-a-subnet"></a>Erstellen eines Subnetzes

```azurepowershell
$subnet = New-AzVirtualNetworkSubnetConfig `
-Name "mySubnet" `
-AddressPrefix "10.0.0.0/24"
```

```azurepowershell
# Create a virtual network
$vnet = New-AzVirtualNetwork `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-Name "myVNET" `
-AddressPrefix "10.0.0.0/16" `
-Subnet $subnet
```

### <a name="create-a-nic"></a>Erstellen einer NIC

Erstellen Sie virtuelle NICs mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface. Das folgende Beispiel erstellt eine virtuelle NIC:

```azurepowershell
# Create an IP Config
$ipconfig=New-AzNetworkInterfaceIpConfig `
-Name myIpConfig `
-Subnet $vnet.subnets[0] `
-PrivateIpAddressVersion IPv4 `
-PublicIpAddress  $publicIp

# Create a NIC
$nic = New-AzNetworkInterface `
-Name "mynic" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location  `
-NetworkSecurityGroupId $nsg.Id `
-IpConfiguration $ipconfig 
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Legen Sie mit [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) den Benutzernamen und das Kennwort des Administrators der VMs fest:

```azurepowershell
 $cred = get-credential -Message "Routing Preference SAMPLE:  Please enter the Administrator credential to log into the VM."
```

Nun können Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer erstellen. Im folgenden Beispiel werden zwei virtuelle Computer und die erforderlichen Komponenten des virtuellen Netzwerks erstellt, falls sie nicht bereits vorhanden sind.

```azurepowershell
 $vmsize = "Standard_A2"
 $ImagePublisher = "MicrosoftWindowsServer"
 $imageOffer = "WindowsServer"
 $imageSKU = "2019-Datacenter"

 $vmName= "myVM"
 $vmconfig = New-AzVMConfig -VMName $vmName -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate | Set-AzVMSourceImage -PublisherName $ImagePublisher -Offer $imageOffer -Skus $imageSKU -Version "latest" | Set-AzVMOSDisk -Name "$vmName.vhd" -CreateOption "FromImage" | Add-AzVMNetworkInterface -Id $nic.Id 
 $VM1 = New-AzVM -ResourceGroupName $rg.ResourceGroupName  -Location $rg.Location  -VM $vmconfig
```

## <a name="allow-network-traffic-to-the-vm"></a>Zulassen von Netzwerkdatenverkehr zum virtuellen Computer

Bevor Sie eine Internetverbindung mit der öffentlichen IP-Adresse herstellen können, müssen Sie sicherstellen, dass die erforderlichen Ports geöffnet sind. Dies gilt für alle der Netzwerkschnittstelle zugeordneten Netzwerksicherheitsgruppen bzw. das Subnetz, in dem sich die Netzwerkschnittstelle befindet. Sie können die Sicherheitsregeln, die für eine Netzwerkschnittstelle und das zugehörige Subnetz angewendet werden, im [Portal](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), mit der [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli) oder mithilfe von [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell) anzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen.

 ```azurepowershell
 Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Routingpräferenz in öffentlichen IP-Adressen](routing-preference-overview.md).
* Lesen Sie mehr über [öffentliche IP-Adressen](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) in Azure.
* Weitere Informationen zu den [Einstellungen für öffentliche IP-Adressen](virtual-network-public-ip-address.md#create-a-public-ip-address)
