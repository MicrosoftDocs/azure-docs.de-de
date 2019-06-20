---
title: 'Erstellen eines routenbasierten Azure-VPN-Gateways: PowerShell | Microsoft-Dokumentation'
description: Schnelles Erstellen eines routenbasierten VPN-Gateways mit PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2019
ms.author: cherylmc
ms.openlocfilehash: 3ad391aae72bec392ae75b333fc3bfc1ff3f95f1
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66727319"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Erstellen eines routenbasierten VPN-Gateways mit PowerShell

In diesem Artikel erfahren Sie, wie Sie schnell ein routenbasiertes Azure-VPN-Gateway mit PowerShell erstellen. Ein VPN-Gateway wird beim Herstellen einer VPN-Verbindung mit Ihrem lokalen Netzwerk verwendet. Sie können Verbindungen mit VNets auch mit einem VPN-Gateway herstellen.

Mit den Schritten in diesem Artikel werden ein VNet, ein Subnetz, ein Gatewaysubnetz und ein routenbasiertes VPN-Gateway (Gateway des virtuellen Netzwerks) erstellt. Wenn die Gatewayerstellung abgeschlossen ist, können Sie Verbindungen herstellen. Diese Schritte erfordern ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen **VNet1** am Standort **USA, Osten** erstellt:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Erstellen Sie mit dem [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)-Cmdlet eine Subnetzkonfiguration.

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Legen Sie die Subnetzkonfiguration für das virtuelle Netzwerk mithilfe des [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)-Cmdlets fest.


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="gwsubnet"></a>Hinzufügen eines Gatewaysubnetzes

Das Gatewaysubnetz enthält die reservierten IP-Adressen, die von den Diensten des virtuellen Netzwerkgateways verwendet werden. Verwenden Sie die folgenden Beispiele, um ein Gatewaysubnetz hinzufügen:

Legen Sie eine Variable für das VNet fest.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Erstellen Sie das Gatewaysubnetz mit dem [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig)-Cmdlet.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Legen Sie die Subnetzkonfiguration für das virtuelle Netzwerk mithilfe des [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)-Cmdlets fest.

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="PublicIP"></a>Anfordern einer öffentlichen IP-Adresse

Ein VPN-Gateway muss über eine dynamisch zugewiesene öffentliche IP-Adresse verfügen. Wenn Sie eine Verbindung mit einem VPN-Gateway herstellen, geben Sie diese IP-Adresse an. Fordern Sie gemäß des folgenden Beispiels eine öffentliche IP-Adresse an:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Erstellen der Gateway-IP-Adresskonfiguration

Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>Erstellen des VPN-Gateways

Das Erstellen eines VPN-Gateways kann 45 Minuten oder länger dauern. Sobald das Gateway fertig gestellt ist, können Sie eine Verbindung Ihres virtuellen Netzwerks mit einem anderen VNet herstellen. Alternativ können Sie eine Verbindung zwischen dem virtuellen Netzwerk und einem lokalen Standort erstellen. Erstellen Sie mit dem Cmdlet [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) ein VPN-Gateway.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Anzeigen des VPN-Gateways

Sie können das VPN-Gateway mit dem [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway)-Cmdlet anzeigen.

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Die Ausgabe sieht etwa wie in diesem Beispiel aus:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="viewgwpip"></a>Anzeigen der öffentlichen IP-Adresse

Verwenden Sie zum Anzeigen der öffentlichen IP-Adresse für Ihr VPN-Gateway das [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress)-Cmdlet.

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

In der Beispielantwort ist der Wert für IpAddress die öffentliche IP-Adresse.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Damit löschen Sie die Ressourcengruppe mit allen enthaltenen Ressourcen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Nächste Schritte

Sobald das Gateway fertig gestellt ist, können Sie eine Verbindung Ihres virtuellen Netzwerks mit einem anderen VNet herstellen. Alternativ können Sie eine Verbindung zwischen dem virtuellen Netzwerk und einem lokalen Standort erstellen.

> [!div class="nextstepaction"]
> [Herstellen einer Site-to-Site-Verbindung](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Herstellen einer Point-to-Site-Verbindung](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Herstellen einer Verbindung mit einem anderen VNet](vpn-gateway-vnet-vnet-rm-ps.md)
