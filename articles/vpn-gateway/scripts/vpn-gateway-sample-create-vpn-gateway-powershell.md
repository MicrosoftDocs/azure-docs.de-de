---
title: 'Azure PowerShell-Skriptbeispiel: Erstellen eines VPN-Gateways | Microsoft-Dokumentation'
description: Erstellen Sie ein VPN-Gateway mithilfe von PowerShell.
services: vpn-gateway
documentationcenter: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 04/17/2018
ms.author: alzam
ms.openlocfilehash: 433899c2b558a5182ca1a5c7c0e0ac76c87e0007
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66113755"
---
# <a name="create-a-vpn-gateway-with-powershell"></a>Erstellen eines VPN-Gateways mithilfe von PowerShell

Dieses Skript erstellt ein routenbasiertes VPN-Gateway.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name TestRG1 -Location EastUS
# Create a virtual network
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
# Create a subnet configuration
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
# Set the subnet configuration for the virtual network
$virtualNetwork | Set-AzVirtualNetwork
# Add a gateway subnet
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
# Set the subnet configuration for the virtual network
$vnet | Set-AzVirtualNetwork
# Request a public IP address
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
# Create the gateway IP address configuration
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
# Create the VPN gateway
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Damit löschen Sie die Ressourcengruppe mit allen enthaltenen Ressourcen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Dieses Skript verwendet die folgenden Befehle zum Erstellen der Bereitstellung. Jedes Element in der Tabelle ist mit der befehlsspezifischen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Fügt eine Subnetzkonfiguration hinzu. Diese Konfiguration wird mit dem Prozess der Erstellung des virtuellen Netzwerks verwendet. |
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Ruft Details zu einem virtuellen Netzwerk ab. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt eine Subnetzkonfiguration. Diese Konfiguration wird mit dem Prozess der Erstellung des virtuellen Netzwerks verwendet. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse. |
|[New-AzVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkgateway) | Erstellt ein VPN-Gateway. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Legt die Subnetzkonfiguration für das virtuelle Netzwerk fest. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure PowerShell-Modul finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).
