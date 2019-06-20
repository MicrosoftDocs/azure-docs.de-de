---
title: VPN Gateway-Einstellungen für standortübergreifende Azure-Verbindungen | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu VPN Gateway-Einstellungen für virtuelle Azure-Netzwerkgateways.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: cherylmc
ms.openlocfilehash: 4030c196d6a4de721b640f5da0b692f4d8157d12
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64859089"
---
# <a name="about-vpn-gateway-configuration-settings"></a>Informationen zu VPN Gateway-Einstellungen

Ein VPN Gateway ist eine Art von Gateway für virtuelle Netzwerke, mit dem verschlüsselter Datenverkehr zwischen Ihrem virtuellen Netzwerk und Ihrem lokalen Standort über eine öffentliche Verbindung gesendet wird. Über ein VPN Gateway können Sie auch Datenverkehr zwischen virtuellen Netzwerken über den Azure-Backbone senden.

Für eine VPN Gateway-Verbindung müssen mehrere Ressourcen konfiguriert werden, die jeweils konfigurierbare Einstellungen enthalten. In den Abschnitten in diesem Artikel werden die Ressourcen und Einstellungen beschrieben, die sich auf ein im Resource Manager-Bereitstellungsmodell erstelltes VPN-Gateway beziehen. Beschreibungen und Topologiediagramme für jede Verbindungslösung finden Sie im Artikel [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md) .

Die Werte in diesem Artikel gelten für VPN-Gateways (Gateways für virtuelle Netzwerke, die den Gatewaytyp „Vpn“ verwenden). Dieser Artikel deckt nicht alle Gatewaytypen oder zonenredundanten Gateways ab.

* Werte, die für den Gatewaytyp „ExpressRoute“ gelten, finden Sie unter [Gateways für virtuelle Netzwerke für ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

* Informationen zu zonenredundanten Gateways finden Sie unter [Informationen zu zonenredundanten Gateways für das virtuelle Netzwerk in Azure-Verfügbarkeitszonen](about-zone-redundant-vnet-gateways.md).

* Informationen zu Virtual WAN finden Sie unter [Was ist Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="gwtype"></a>Gatewaytypen

Jedes virtuelle Netzwerk kann nur über ein einziges virtuelles Netzwerkgateway jedes Typs verfügen. Beim Erstellen eines Gateways des virtuellen Netzwerks müssen Sie sicherstellen, dass der Gatewaytyp für Ihre Konfiguration korrekt ist.

Die verfügbaren Werte für -GatewayType lauten:

* VPN
* ExpressRoute

Ein VPN-Gateway erfordert den `-GatewayType` *Vpn*.

Beispiel:

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased
```

## <a name="gwsku"></a>Gateway-SKUs

[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configure-a-gateway-sku"></a>Konfigurieren einer Gateway-SKU

#### <a name="azure-portal"></a>Azure-Portal

Wenn Sie das Azure-Portal verwenden, um ein Resource Manager-Gateway für virtuelle Netzwerke zu erstellen, können Sie die Gateway-SKU in der Dropdownliste auswählen. Die angegebenen Optionen entsprechen dem ausgewählten Gateway- und VPN-Typ.

#### <a name="powershell"></a>PowerShell

Im folgenden PowerShell-Beispiel wird `-GatewaySku` als „VpnGw1“ angegeben. Wenn Sie PowerShell verwenden, um ein Gateway zu erstellen, müssen Sie zuerst die IP-Konfiguration erstellen und dann mittels einer Variablen darauf verweisen. In diesem Beispiel ist die Konfigurationsvariable „$gwipconfig“.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'US East' -IpConfigurations $gwipconfig -GatewaySku VpnGw1 `
-GatewayType Vpn -VpnType RouteBased
```

#### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWPIP --resource-group TestRG1 --vnet VNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait
```

###  <a name="resizechange"></a>Ändern der Größe oder Wechseln der SKU

Wenn Sie über ein VPN-Gateway verfügen und eine andere Gateway-SKU verwenden möchten, können Sie entweder die Größe Ihrer Gateway-SKU ändern oder zu einer anderen SKU wechseln. Wenn Sie zu einer anderen Gateway-SKU wechseln, löschen Sie das vorhandene Gateway vollständig und erstellen ein neues. Die Erstellung eines Gateways kann bis zu 45 Minuten dauern. Wenn Sie hingegen die Größe einer Gateway-SKU ändern, gibt es nur eine geringe Ausfallzeit, da Sie das Gateway nicht löschen und neu erstellen müssen. Wenn Sie die Möglichkeit haben, die Größe der Gateways-SKU zu ändern anstatt zu einer anderen SKU zu wechseln, sollten Sie diese Option bevorzugen. Es gibt jedoch auch Regeln hinsichtlich der Größenänderung:

1. Sie können die SKU-Größe zwischen VpnGw1, VpnGw2 und VpnGw3 ändern.
2. Bei Verwendung der alten Gateway-SKUs kann die Größe weiterhin zwischen Basic-, Standard- und HighPerformance-SKUs geändert werden.
3. Sie können die Größe **nicht** von Basic/Standard/HighPerformance-SKUs in die neuen VpnGw1/VpnGw2/VpnGw3-SKUs ändern. Sie müssen stattdessen zu den neuen SKUs [wechseln](#change).

#### <a name="resizegwsku"></a>So ändern Sie die Größe eines Gateways

[!INCLUDE [Resize a SKU](../../includes/vpn-gateway-gwsku-resize-include.md)]

####  <a name="change"></a>So wechseln Sie von einer alten SKU (Legacy) zu einer neuen SKU

[!INCLUDE [Change a SKU](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="connectiontype"></a>Verbindungstypen

Im Resource Manager-Bereitstellungsmodell ist für jede Konfiguration ein bestimmter Typ der Verbindung mit dem Gateway eines virtuellen Netzwerks erforderlich. Die verfügbaren Resource Manager-PowerShell-Werte für `-ConnectionType` sind:

* IPsec
* Vnet2Vnet
* ExpressRoute
* VPNClient

Im folgenden PowerShell-Beispiel erstellen wir eine S2S-Verbindung, die den Verbindungstyp *IPsec*erfordert.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

## <a name="vpntype"></a>VPN-Typen

Wenn Sie das Gateway des virtuellen Netzwerks für eine VPN-Gatewaykonfiguration erstellen, müssen Sie einen VPN-Typ angeben. Der ausgewählte VPN-Typ hängt von der Verbindungstopologie ab, die Sie erstellen möchten. Beispielsweise erfordert eine P2S-Verbindung den VPN-Typ „RouteBased“. Ein VPN-Typ kann zudem von der verwendeten Hardware abhängen. S2S-Konfigurationen erfordern ein VPN-Gerät. Einige VPN-Geräte unterstützen nur einen bestimmten VPN-Typ.

Der ausgewählte VPN-Typ muss alle Verbindungsanforderungen für die Lösung erfüllen, die Sie erstellen möchten. Beispiel: Wenn Sie eine S2S-VPN-Gatewayverbindung und eine P2S-VPN-Gatewayverbindung für dasselbe virtuelle Netzwerk erstellen möchten, verwenden Sie den VPN-Typ *RouteBased* , da P2S diesen VPN-Typ erfordert. Sie müssen auch sicherstellen, dass Ihr VPN-Gerät eine RouteBased-VPN-Verbindung unterstützt. 

Nachdem ein Gateway des virtuellen Netzwerks erstellt wurde, können Sie den VPN-Typ nicht mehr ändern. Sie müssen das Gateway des virtuellen Netzwerks löschen und ein neues erstellen. Es gibt zwei VPN-Typen:

[!INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Das folgende PowerShell-Beispiel gibt `-VpnType` als *RouteBased*an. Beim Erstellen eines Gateways müssen Sie sicherstellen, dass -VpnType für Ihre Konfiguration korrekt ist.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
-Location 'West US' -IpConfigurations $gwipconfig `
-GatewayType Vpn -VpnType RouteBased
```

## <a name="requirements"></a>Gatewayanforderungen

[!INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## <a name="gwsub"></a>Gatewaysubnetz

Bevor Sie ein VPN-Gateway erstellen, müssen Sie ein Gatewaysubnetz erstellen. Das Gatewaysubnetz enthält die IP-Adressen, die von den VMs und Diensten des virtuellen Netzwerkgateways verwendet werden. Bei der Erstellung des Gateways des virtuellen Netzwerks, werden Gateway-VMs für das Gatewaysubnetz bereitgestellt und mit den erforderlichen VPN Gateway-Einstellungen konfiguriert. Stellen Sie für das Gatewaysubnetz nie etwas anderes bereit (beispielsweise zusätzliche virtuelle Computer). Das Gatewaysubnetz muss den Namen „GatewaySubnet“ aufweisen, damit es einwandfrei funktioniert. Wenn Sie dem Gatewaysubnetz den Namen „GatewaySubnet“ zugewiesen haben, erkennt Azure, dass es sich dabei um das Subnetz handelt, für das die VMs und Dienste des virtuellen Netzwerkgateways bereitgestellt werden sollen.

>[!NOTE]
>[!INCLUDE [vpn-gateway-gwudr-warning.md](../../includes/vpn-gateway-gwudr-warning.md)]
>

Bei der Gatewayerstellung geben Sie die Anzahl der im Subnetz enthaltenen IP-Adressen an. Die IP-Adressen im Gatewaysubnetz werden den Gatewaydiensten und -VMs zugeordnet. Einige Konfigurationen erfordern mehr IP-Adressen als andere. Sehen Sie sich die Anweisungen für die Konfiguration an, die Sie erstellen möchten, und vergewissern Sie sich, dass das Gatewaysubnetz, das sie erstellen möchten, diese Anforderungen erfüllt. Stellen Sie zusätzlich sicher, dass Ihr Gatewaysubnetz über genügend IP-Adressen verfügt, und berücksichtigen Sie dabei auch mögliche zukünftige Konfigurationen. Sie können zwar ein Gatewaysubnetz mit einer Größe von nur /29 erstellen, aber es empfiehlt sich, ein Gatewaysubnetz mit einer Größe von mindestens /28 oder größer (/28, /27, /26 usw.) zu erstellen. Wenn Sie künftig Funktionen hinzufügen, müssen Sie Ihr Gateway nicht beenden und anschließend das Gatewaysubnetz löschen und erneut erstellen, um weitere IP-Adressen zu ermöglichen.

Im folgenden Resource Manager-PowerShell-Beispiel ist ein Gatewaysubnetz mit dem Namen GatewaySubnet zu sehen. Sie erkennen, das mit der CIDR-Notation die Größe /27 angegeben wird. Dies ist für eine ausreichende Zahl von IP-Adressen für die meisten Konfigurationen, die derzeit üblich sind, groß genug.

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="lng"></a>Gateways des lokalen Netzwerks

 Ein Gateway des lokalen Netzwerks unterscheidet sich von einem Gateway für virtuelle Netzwerke. Wenn Sie eine VPN-Gatewaykonfiguration erstellen, stellt das Gateway des lokalen Netzwerks in der Regel Ihren lokalen Standort dar. Beim klassischen Bereitstellungsmodell wurde das Gateway des lokalen Netzwerks als „Lokaler Standort“ bezeichnet.

Sie geben dem Gateway des lokalen Netzwerks einen Namen, stellen die öffentliche IP-Adresse des lokalen VPN-Geräts bereit und geben die Adresspräfixe an, die sich am lokalen Standort befinden. Azure sucht unter den Zieladresspräfixen nach Netzwerkdatenverkehr, sieht in der Konfiguration nach, die Sie für das Gateway des lokalen Netzwerks angegeben haben, und leitet die Pakete entsprechend weiter. Sie geben auch Gateways des lokalen Netzwerks für VNet-zu-VNet-Konfigurationen an, die eine VPN-Gatewayverbindung verwenden.

Mit dem folgenden PowerShell-Beispiel wird ein neues Gateway des lokalen Netzwerks erstellt:

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Manchmal müssen Sie die Einstellungen des Gateways des lokalen Netzwerks ändern, beispielsweise wenn Sie den Adressbereich erweitern oder ändern oder wenn sich die IP-Adresse des VPN-Geräts ändert. Weitere Informationen hierzu finden Sie im Artikel [Ändern der Einstellungen des lokalen Netzwerkgateways mit PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>REST-APIs, PowerShell-Cmdlets und CLI

Zusätzliche technische Ressourcen und spezielle Syntaxanforderungen bei der Verwendung von REST-APIs, PowerShell-Cmdlets oder die Azure-CLI für VPN-Gatewaykonfigurationen finden Sie auf den folgenden Seiten:

| **Klassisch** | **Ressourcen-Manager** |
| --- | --- |
| [PowerShell](/powershell/module/az.network/#networking) |[PowerShell](/powershell/module/az.network#vpn) |
| [REST-API](https://msdn.microsoft.com/library/jj154113) |[REST-API](/rest/api/network/virtualnetworkgateways) |
| Nicht unterstützt | [Azure-Befehlszeilenschnittstelle](/cli/azure/network/vnet-gateway)|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu verfügbaren Verbindungskonfigurationen finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).
