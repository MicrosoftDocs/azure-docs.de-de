---
title: Konfigurieren der Tunnelerzwingung für Site-to-Site-Verbindungen
description: 'Gewusst wie: Umleiten des gesamten Internetdatenverkehrs zurück zum lokalen Standort (per Erzwingung).'
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 12/07/2020
ms.author: cherylmc
ms.openlocfilehash: c12297019b49d7b3cb644ae9c7a904e4ca697f0b
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855038"
---
# <a name="configure-forced-tunneling"></a>Konfigurieren der Tunnelerzwingung

Über die Tunnelerzwingung können Sie die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort „erzwingen“. Sie verwenden dazu einen Standort-zu-Standort-VPN-Tunnel für die Kontrolle und Überwachung. Dies ist eine wichtige Sicherheitsvoraussetzung der IT-Richtlinien für die meisten Unternehmen. Wenn Sie die Tunnelerzwingung nicht konfigurieren, wird der Internetdatenverkehr Ihrer virtuellen Computer in Azure immer direkt von der Azure-Netzwerkinfrastruktur an das Internet geleitet, ohne dass Sie die Möglichkeit haben, diesen zu überprüfen oder zu überwachen. Nicht autorisierter Zugriff auf das Internet kann potenziell zur Offenlegung von Informationen oder anderen Arten von Sicherheitsverletzungen führen.

Die Tunnelerzwingung kann mithilfe von Azure PowerShell konfiguriert werden. Eine Konfiguration über das Azure-Portal ist nicht möglich. Dieser Artikel hilft Ihnen dabei, die Tunnelerzwingung für virtuelle Netzwerke zu konfigurieren, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden. Informationen zum Konfigurieren der Tunnelerzwingung für das klassische Bereitstellungsmodell finden Sie unter [Konfigurieren der Tunnelerzwingung mit dem klassischen Bereitstellungsmodell](vpn-gateway-about-forced-tunneling.md).

## <a name="about-forced-tunneling"></a>Informationen zur Tunnelerzwingung

Das folgende Diagramm veranschaulicht die Funktionsweise der Tunnelerzwingung.

:::image type="content" source="./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png" alt-text="Diagramm der Tunnelerzwingung":::

In diesem Beispiel besteht keine Tunnelerzwingung für das Front-End-Subnetz. Die Workloads im Frontend-Subnetz können weiterhin Kundenanfragen direkt aus dem Internet akzeptieren und darauf reagieren. Für die Subnetze "Midtier" und "Backend" wird das Tunneln erzwungen. Bei allen ausgehenden Internetverbindungen aus diesen zwei Subnetzen wird über einen der S2S-VPN-Tunnel eine Umleitung an einen lokalen Standort erzwungen.

Dadurch können Sie den Internetzugriff über die virtuellen Computer oder Clouddienste in Azure einschränken und überprüfen, während die erforderliche mehrschichtige Dienstarchitektur weiterhin in Betrieb bleibt. Wenn in diesen keine Workloads mit Internetverbindung erforderlich sind, können Sie das erzwungene Tunneln auch auf alle virtuellen Netzwerke anwenden.

## <a name="requirements-and-considerations"></a>Anforderungen und Überlegungen

Die Tunnelerzwingung in Azure wird über benutzerdefinierte Routen im virtuellen Netzwerk konfiguriert. Das Umleiten von Datenverkehr an einen lokalen Standort wird als eine Standardroute zum Azure-VPN-Gateway umgesetzt. Weitere Informationen zu benutzerdefiniertem Routing und virtuellen Netzwerken finden Sie unter [Benutzerdefiniertes Routing](../virtual-network/virtual-networks-udr-overview.md#user-defined).

* Jedes Subnetz des virtuellen Netzwerks verfügt über eine integrierte Systemroutingtabelle. Die Systemroutingtabelle verfügt über die folgenden drei Gruppen von Routen:
  
  * **Lokale VNET-Routen:** direkt zu den virtuellen Zielcomputern im gleichen virtuellen Netzwerk.
  * **Lokale Routen:** zum Azure VPN Gateway.
  * **Standardroute:** direkt zum Internet. Pakete an private IP-Adressen, die nicht durch die vorherigen beiden Routen abgedeckt sind, werden verworfen.
* In diesem Verfahren werden benutzerdefinierte Routen (User Defined Routes, UDR) verwendet, um eine Routingtabelle zu erstellen, damit eine Standardroute hinzugefügt wird. Anschließend verknüpfen Sie die Routingtabelle mit den VNet-Subnetzen, um die Tunnelerzwingung in diesen Subnetzen zu aktivieren.
* Die Tunnelerzwingung muss einem VNet zugeordnet werden, das über ein routenbasiertes VPN-Gateway verfügt. Sie müssen einen "Standardstandort" unter den standortübergreifenden lokalen Standorten auswählen, der mit dem virtuellen Netzwerk verbunden ist. Darüber hinaus muss das lokale VPN-Gerät für die Verwendung von „0.0.0.0/0“ als Datenverkehrsauswahl konfiguriert werden. 
* Die ExpressRoute-Tunnelerzwingung kann über diesen Mechanismus nicht konfiguriert werden. Sie wird stattdessen durch Anfordern einer Standardroute über die ExpressRoute-BGP-Peeringsitzungen aktiviert. Weitere Informationen finden Sie in der [Dokumentation zu ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/).
* Wenn sowohl VPN Gateway als auch ExpressRoute-Gateway im gleichen VNet bereitgestellt werden, sind benutzerdefinierte Routen (UDR) nicht mehr erforderlich, da ExpressRoute-Gateway konfigurierte „Standardstandorte“ im VNet anzeigt.

## <a name="configuration-overview"></a>Konfigurationsübersicht

Das folgende Verfahren unterstützt Sie beim Erstellen einer Ressourcengruppe und eines VNets. Anschließend erstellen Sie ein VPN-Gateway und konfigurieren die Tunnelerzwingung. In dieser Vorgehensweise verfügt das virtuelle Netzwerk „MultiTier-VNet“ über die drei Subnetze „Frontend“, „Midtier“ und „Backend“ mit vier standortübergreifenden Verbindungen: „DefaultSiteHQ“ und drei Verzweigungen.

Mithilfe der Schritte wird festgelegt, dass „DefaultSiteHQ“ die Standard-Standortverbindung für die Tunnelerzwingung ist und die Subnetze „Midtier“ und „Backend“ die Tunnelerzwingung verwenden müssen.

## <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

Installieren Sie die aktuelle Version der PowerShell-Cmdlets für Azure Resource Manager. Weitere Informationen zur Installation der PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) .

> [!IMPORTANT]
> Es muss die neueste Version der PowerShell-Cmdlets installiert sein. Andernfalls können bei der Ausführung einiger Cmdlets Überprüfungsfehler auftreten.
>
>

### <a name="to-sign-in"></a>So melden Sie sich an

[!INCLUDE [Sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

## <a name="configure-forced-tunneling"></a>Konfigurieren der Tunnelerzwingung

> [!NOTE]
> Eventuell werden Warnungen wie die Folgende angezeigt: „Der Ausgabeobjekttyp dieses Cmdlets wird in einer zukünftigen Version geändert.“ Dies ist das erwartete Verhalten. Sie können diese Warnungen einfach ignorieren.
>
>


1. Erstellen Sie eine Ressourcengruppe.

   ```powershell
   New-AzResourceGroup -Name 'ForcedTunneling' -Location 'North Europe'
   ```
2. Erstellen Sie ein virtuelles Netzwerks, und geben Sie Subnetze an.

   ```powershell 
   $s1 = New-AzVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
   $s2 = New-AzVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
   $s3 = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
   $s4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
   $vnet = New-AzVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4
   ```
3. Erstellen Sie die lokalen Netzwerkgateways.

   ```powershell
   $lng1 = New-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
   $lng2 = New-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
   $lng3 = New-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
   $lng4 = New-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
   ```
4. Erstellen Sie die Routingtabelle und die Routingregel.

   ```powershell
   New-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
   $rt = Get-AzRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
   Add-AzRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
   Set-AzRouteTable -RouteTable $rt
   ```
5. Ordnen Sie die Routingtabelle den Subnetzen „Midtier“ und „Backend“ zu.

   ```powershell
   $vnet = Get-AzVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
   Set-AzVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
   Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
6. Erstellen Sie das virtuelle Netzwerkgateway. Dieser Schritt dauert einige Zeit (manchmal 45 Minuten oder länger), da Sie das Gateway erstellen und konfigurieren. Wenn ValidateSet-Fehler zum GatewaySKU-Wert angezeigt werden, sollten Sie überprüfen, ob Sie die [aktuelle Version der PowerShell-Cmdlets](#before) installiert haben. Die aktuelle Version der PowerShell-Cmdlets enthält die neuen überprüften Werte für die neuesten Gateway-SKUs.

   ```powershell
   $pip = New-AzPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
   $gwsubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $ipconfig = New-AzVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
   New-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $false
   ```
7. Weisen Sie einen Standardstandort für das virtuelle Netzwerkgateway zu. Der Cmdlet-Parameter **-GatewayDefaultSite** sorgt dafür, dass die erzwungene Routingkonfiguration funktioniert. Achten Sie daher darauf, diese Einstellung korrekt zu konfigurieren. 

   ```powershell
   $LocalGateway = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling"
   $VirtualGateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   Set-AzVirtualNetworkGatewayDefaultSite -GatewayDefaultSite $LocalGateway -VirtualNetworkGateway $VirtualGateway
   ```
8. Richten Sie die Standort-zu-Standort-VPN-Verbindungen ein.

   ```powershell
   $gateway = Get-AzVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
   $lng1 = Get-AzLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
   $lng2 = Get-AzLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
   $lng3 = Get-AzLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
   $lng4 = Get-AzLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 
    
   New-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
   New-AzVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"
    
   Get-AzVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
   ```
