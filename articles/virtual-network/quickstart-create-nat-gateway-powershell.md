---
title: Erstellen eines NAT-Gateways – Azure PowerShell
titlesuffix: Azure Virtual Network NAT
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein NAT-Gateway mithilfe von Azure PowerShell erstellen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: de4e32d79cf4dfb3a5f54544c65544297a2c0232
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993566"
---
# <a name="tutorial-create-a-nat-gateway-using-azure-powershell"></a>Tutorial: Erstellen eines NAT-Gateways mithilfe von Azure PowerShell

In diesem Tutorial erfahren Sie, wie Sie den Azure Virtual Network NAT-Dienst verwenden. Sie erstellen ein NAT-Gateway, um ausgehende Konnektivität für einen virtuellen Computer in Azure bereitzustellen. 


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Sie können dieses Tutorial mithilfe von Azure Cloud Shell absolvieren oder die Befehle lokal ausführen.  Falls Sie nicht Azure Cloud Shell verwendet haben, [melden Sie sich jetzt an](https://shell.azure.com).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=latest) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen **myResourceGroupNAT** am Standort **eastus2** erstellt:

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
  
New-AzResourceGroup -Name $rsg -Location $loc
```

## <a name="create-the-nat-gateway"></a>Erstellen des NAT-Gateways

Optionen für öffentliche IP-Adressen für das NAT-Gateway:

* **Öffentliche IP-Adressen**
* **Präfixe für öffentliche IP-Adressen**

Beide können mit dem NAT-Gateway verwendet werden.

In diesem Szenario werden zur Veranschaulichung eine öffentliche IP-Adresse und ein Präfix für öffentliche IP-Adressen hinzugefügt.

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Für den Zugriff auf das Internet benötigen Sie mindestens eine öffentliche IP-Adresse für das NAT-Gateway. Verwenden Sie [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) zum Erstellen einer öffentlichen IP-Ressource namens **myPublicIP** in **myResourceGroupNAT**. Das Ergebnis dieses Befehls wird zur späteren Verwendung in der Variablen **$publicIP** gespeichert.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$pbnm = 'myPublicIP'
  
$publicIP = 
New-AzPublicIpAddress -Name $pbnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-a-public-ip-prefix"></a>Erstellen des Präfixes für öffentliche IP-Adressen

Verwenden Sie [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipprefix?view=latest), um eine Präfixressource für öffentliche IP-Adressen mit dem Namen **myPublicIPprefix** in **myResourceGroupNAT** zu erstellen.  Das Ergebnis dieses Befehls wird zur späteren Verwendung in einer Variablen namens **$publicIPPrefix** gespeichert.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pxnm = 'myPublicIPprefix'

$publicIPPrefix = 
New-AzPublicIpPrefix -Name $pxnm -ResourceGroupName $rsg -Location $loc -PrefixLength 31
```

### <a name="create-a-nat-gateway-resource"></a>Erstellen einer NAT-Gatewayressource

In diesem Abschnitt erfahren Sie, wie Sie mit der NAT-Gatewayressource die folgenden Komponenten des NAT-Diensts erstellen und konfigurieren:
  - Öffentlicher IP-Adresspool und Präfix für öffentliche IP-Adressen für ausgehende Datenflüsse, die von der NAT-Gatewayressource übersetzt werden
  - Ändern des Leerlauftimeouts von vier Minuten (Standardwert) in zehn Minuten

Erstellen Sie mit [New-AzNatGateway](https://docs.microsoft.com/powershell/module/az.network/new-aznatgateway) ein globales Azure-NAT-Gateway. Als Ergebnis dieses Befehls wird eine Gatewayressource namens **myNATgateway** erstellt, die die öffentliche IP-Adresse **myPublicIP** und das Präfix für öffentliche IP-Adressen **myPublicIPprefix** verwendet. Das Leerlauftimeout wird auf zehn Minuten festgelegt.  Das Ergebnis dieses Befehls wird zur späteren Verwendung in einer Variablen namens **$natGateway** gespeichert.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$sku = 'Standard'
$gnm = 'myNATgateway'

$natGateway = 
New-AzNatGateway -Name $gnm -ResourceGroupName $rsg -PublicIpAddress $publicIP -PublicIpPrefix $publicIPPrefix -Location $loc -Sku $sku -IdleTimeoutInMinutes 10
  ```

Das NAT-Gateway ist nun verwendungsbereit, und Sie müssen nur noch konfigurieren, welche Subnetze eines virtuellen Netzwerks das Gateway verwenden sollen.

## <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Erstellen Sie das virtuelle Netzwerk, und weisen Sie das Subnetz dem Gateway zu.

Erstellen Sie ein virtuelles Netzwerk namens **myVnet** mit einem Subnetz namens **mySubnet** mit [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig?view=latest) in **myResourceGroup**, indem Sie [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork?view=latest) verwenden. Der IP-Adressraum für das virtuelle Netzwerk ist **192.168.0.0/16**. Das Subnetz im virtuellen Netzwerk ist **192.168.0.0/24**.  Das Ergebnis der Befehle wird zur späteren Verwendung in Variablen mit den Namen **$subnet** und **$vnet** gespeichert.

```azurepowershell-interactive
$sbnm = 'mySubnet'
$vnnm = 'myVnet'
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$pfxsub = '192.168.0.0/24'
$pfxvn = '192.168.0.0/16'

$subnet = 
New-AzVirtualNetworkSubnetConfig -Name $sbnm -AddressPrefix $pfxsub -NatGateway $natGateway

$vnet = 
New-AzVirtualNetwork -Name $vnnm -ResourceGroupName $rsg -Location $loc -AddressPrefix $pfxvn -Subnet $subnet
```

Der gesamte ausgehende Datenverkehr an Ziele im Internet verwendet jetzt den NAT-Dienst.  Eine UDR muss nicht konfiguriert werden.

## <a name="create-a-vm-to-use-the-nat-service"></a>Erstellen eines virtuellen Computers zur Verwendung des NAT-Diensts

Sie erstellen nun einen virtuellen Computer zur Verwendung des NAT-Diensts.  Dieser virtuelle Computer verfügt über eine öffentliche IP-Adresse, die als öffentliche IP auf Instanzebene verwendet wird, um den Zugriff auf den virtuellen Computer zu ermöglichen.  Der NAT-Dienst erkennt die Flussrichtung und ersetzt in Ihrem Subnetz das Standardziel im Internet. Die öffentliche IP-Adresse des virtuellen Computers wird nicht für ausgehende Verbindungen verwendet.

### <a name="create-public-ip-for-source-vm"></a>Erstellen einer öffentlichen IP-Adresse für den virtuellen Quellcomputer

Sie erstellen eine öffentliche IP-Adresse, die für den Zugriff auf den virtuellen Computer verwendet werden soll.  Verwenden Sie [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress?view=latest) zum Erstellen einer öffentlichen IP-Ressource namens **myPublicIPVM** in **myResourceGroupNAT**.  Das Ergebnis dieses Befehls wird zur späteren Verwendung in einer Variablen namens **$publicIpVM** gespeichert.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'
$ipnm = 'myPublicIPVM'
$sku = 'Standard'

$publicIpVM = 
New-AzPublicIpAddress -Name $ipnm -ResourceGroupName $rsg -AllocationMethod Static -Location $loc -Sku $sku
```

### <a name="create-an-nsg-and-expose-ssh-endpoint-for-vm"></a>Erstellen einer NSG und Verfügbarmachen des SSH-Endpunkts für den virtuellen Computer

Öffentliche Standard-IP-Adressen sind standardmäßig sicher, und Sie müssen eine NSG erstellen, um den SSH-Zugriff in eingehender Richtung zuzulassen. Verwenden Sie [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup?view=latest), um eine NSG-Ressource mit dem Namen **myNSG** zu erstellen. Verwenden Sie [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig?view=latest), um in **myResourceGroupNAT** eine NSG-Regel namens **ssh** für den SSH-Zugriff zu erstellen.  Das Ergebnis dieses Befehls wird zur späteren Verwendung in einer Variablen namens **$nsg** gespeichert.

```azurepowershell-interactive
$rnm = 'ssh'
$rdesc = 'SSH access'
$acc = 'Allow'
$pro = 'Tcp'
$dir = 'Inbound'
$pri = '100'
$prt = '22'
$rsg = 'myResourceGroupNAT'
$rnm = 'myNSG'
$loc = 'eastus2'

$sshrule = 
New-AzNetworkSecurityRuleConfig -Name $rnm -Description $rdesc -Access $acc -Protocol $pro -Direction $dir -Priority $pri -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange $prt

$nsg = 
New-AzNetworkSecurityGroup -ResourceGroupName $rsg -Name $rnm -Location $loc -SecurityRules $sshrule 
```

### <a name="create-nic-for-vm"></a>Erstellen einer NIC für virtuelle Computer

Erstellen Sie mit [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface?view=azps-2.8.0) eine Netzwerkschnittstelle namens **myNic**. Mit diesem Befehl wird die öffentliche IP-Adresse der Netzwerksicherheitsgruppe zugeordnet. Das Ergebnis dieses Befehls wird zur späteren Verwendung in einer Variablen namens **$nic** gespeichert.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myNic'
$loc = 'eastus2'

$nic = 
New-AzNetworkInterface -ResourceGroupName $rsg -Name $nmn -NetworkSecurityGroupID $nsg.Id -PublicIPAddressID $publicIPVM.Id -SubnetID $vnet.Subnets[0].Id -Location $loc
```

### <a name="create-vm"></a>Erstellen eines virtuellen Computers

#### <a name="create-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars

Für diesen Schnellstart benötigen Sie ein SSH-Schlüsselpaar. Falls Sie bereits über ein SSH-Schlüsselpaar verfügen, können Sie diesen Schritt überspringen.

Verwenden Sie „ssh-keygen“, um ein SSH-Schlüsselpaar zu erstellen.

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```
Ausführlichere Informationen zum Erstellen von SSH-Schlüsselpaaren, u.a. zur Verwendung von PuTTy, finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows).

Wenn Sie das SSH-Schlüsselpaar mit Cloud Shell erstellen, wird es in einem Containerimage gespeichert. Dieses [Speicherkonto wird automatisch erstellt](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage). Löschen Sie das Speicherkonto oder die darin enthaltene Dateifreigabe nicht, bis Sie Ihre Schlüssel abgerufen haben.

#### <a name="create-vm-configuration"></a>Erstellen der VM-Konfiguration

Wenn Sie einen virtuellen Computer in PowerShell erstellen möchten, erstellen Sie eine Konfiguration mit Einstellungen für das zu verwendende Image, die Größe und die Authentifizierungsoptionen. Die Konfiguration wird zum Erstellen des virtuellen Computers verwendet.

Definieren Sie die SSH-Anmeldeinformationen, die Betriebssysteminformationen und die VM-Größe. In diesem Beispiel befindet sich der SSH-Schlüssel in „~/.ssh/id_rsa.pub“.

```azurepowershell-interactive
#Define a credential object

$securePassword = 
ConvertTo-SecureString ' ' -AsPlainText -Force

$cred = 
New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration

$vnm = 'myVM'
$vsz = 'Standard_D1'
$pub = 'Canonical'
$off = 'UbuntuServer'
$sku = '18.04-LTS'
$ver = 'latest'

$vmConfig = 
New-AzVMConfig -VMName $vnm -VMSize $vsz

Set-AzVMOperatingSystem -VM $vmConfig -Linux -ComputerName $vnm -Credential $cred -DisablePasswordAuthentication

Set-AzVMSourceImage -VM $vmConfig -PublisherName $pub -Offer $off -Skus $sku -Version $ver

Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Configure the SSH key

$sshPublicKey = cat ~/.ssh/id_rsa.pub

Add-AzVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

```
Kombinieren Sie die Konfigurationsdefinitionen, um mit [New-AzVM](/powershell/module/az.compute/new-azvm?view=azps-2.8.0) einen virtuellen Computer namens **myVM** in **myResourceGroupNAT** zu erstellen.

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$loc = 'eastus2'

New-AzVM -ResourceGroupName $rsg -Location $loc -VM $vmconfig
```

Warten Sie, bis der virtuelle Computer auf die Bereitstellung vorbereitet wurde, und fahren Sie dann mit den übrigen Schritten fort.

## <a name="discover-the-ip-address-of-the-vm"></a>Ermitteln der IP-Adresse des virtuellen Computers

Als Erstes müssen Sie die IP-Adresse des virtuellen Computers ermitteln, den Sie erstellt haben. Verwenden Sie [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=latest) zum Abrufen der öffentlichen IP-Adresse des virtuellen Computers: 

```azurepowershell-interactive
$rsg = 'myResourceGroupNAT'
$nmn = 'myPublicIPVM'

Get-AzPublicIpAddress -ResourceGroupName $rsg -Name $nmn | select IpAddress
``` 

>[!IMPORTANT]
>Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in einen Editor ein, damit Sie sie für den Zugriff auf den virtuellen Computer verwenden können.

### <a name="sign-in-to-vm"></a>Anmelden beim virtuellen Computer

Die SSH-Anmeldeinformationen sollten in der Cloud Shell-Instanz aus dem vorherigen Vorgang gespeichert werden.  Öffnen Sie [Azure Cloud Shell](https://shell.azure.com) im Browser. Verwenden Sie die im vorherigen Schritt abgerufene IP-Adresse, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen.

```bash
ssh azureuser@<ip-address-destination>
```

Sie können den NAT-Dienst nun verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigt werden, können Sie sie mit dem Befehl [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) entfernen.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein NAT-Gateway und einen virtuellen Computer für seine Verwendung erstellt. 

Sehen Sie sich die Metriken in Azure Monitor an, um Informationen zum Betrieb Ihres NAT-Diensts anzuzeigen. Diagnostizieren Sie Probleme wie die Ressourcenüberlastung von verfügbaren SNAT-Ports.  Die Ressourcenüberlastung von SNAT-Ports wird durch Hinzufügen zusätzlicher Ressourcen für öffentliche IP-Adressen und/oder zusätzlicher Präfixressourcen für öffentliche IP-Adressen behoben.


- Erfahren Sie mehr über [Azure Virtual Network NAT](./nat-overview.md).
- Informieren Sie sich über [NAT-Gatewayressourcen](./nat-gateway-resource.md).
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe der Azure CLI](./quickstart-create-nat-gateway-cli.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe von Azure PowerShell](./quickstart-create-nat-gateway-powershell.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe des Azure-Portals](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]


