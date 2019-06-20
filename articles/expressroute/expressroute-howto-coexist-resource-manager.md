---
title: 'Konfigurieren von parallel bestehenden ExpressRoute- und Site-to-Site-VPN-Verbindungen: PowerShell: Azure | Microsoft-Dokumentation'
description: Konfiguration von ExpressRoute- und Site-to-Site-VPN-Verbindungen, die für das Resource Manager-Modell gleichzeitig bestehen können, mithilfe von PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 4a1f9556413df7ad8954171d2b446419d3bc2975
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60366567"
---
# <a name="configure-expressroute-and-site-to-site-coexisting-connections-using-powershell"></a>Konfigurieren von parallel bestehenden ExpressRoute- und Standort-zu-Standort-Verbindungen mithilfe von PowerShell
> [!div class="op_single_selector"]
> * [PowerShell – Resource Manager](expressroute-howto-coexist-resource-manager.md)
> * [PowerShell – klassisch](expressroute-howto-coexist-classic.md)
> 
> 

In diesem Artikel wird beschrieben, wie Sie parallel bestehende ExpressRoute- und Site-to-Site-VPN-Verbindungen konfigurieren. Die Möglichkeit zum Konfigurieren von Standort-zu-Standort-VPN und ExpressRoute bietet mehrere Vorteile. Sie können ein Site-to-Site-VPN als sicheren Failoverpfad für ExpressRoute konfigurieren oder für Verbindungen mit Websites nutzen, die nicht über ExpressRoute verbunden sind. Wir werden die Schritte zum Konfigurieren von beiden Szenarien in diesem Artikel behandeln. Dieser Artikel gilt für das Resource Manager-Bereitstellungsmodell.

Das Konfigurieren paralleler Site-to-Site-VPN- und ExpressRoute-Verbindungen bietet mehrere Vorteile:

* Sie können ein Site-to-Site-VPN als sicheren Failoverpfad für ExpressRoute konfigurieren. 
* Alternativ hierzu können Sie Site-to-Site-VPNs nutzen, um Standorte zu verbinden, die nicht per ExpressRoute verbunden sind. 

Dieser Artikel enthält die Schritte für die Konfiguration beider Szenarien. Dieser Artikel gilt für das Resource Manager-Bereitstellungsmodell und für die Verwendung von PowerShell. Sie können diese Szenarien auch über das Azure-Portal konfigurieren – für diese Vorgehensweise steht jedoch noch keine Dokumentation zur Verfügung. Sie können ein beliebiges Gateway zuerst konfigurieren. Beim Hinzufügen eines neuen Gateways oder einer Gatewayverbindung treten in der Regel keine Ausfallzeiten auf.

>[!NOTE]
>Wenn Sie ein Site-to-Site-VPN über eine ExpressRoute-Verbindung erstellen möchten, lesen Sie [diesen Artikel](site-to-site-vpn-over-microsoft-peering.md).
>

## <a name="limits-and-limitations"></a>Grenzwerte und Einschränkungen
* **Transitrouting wird nicht unterstützt.** Ein Routing (über Azure) zwischen Ihrem lokalen Netzwerk mit Standort-zu-Standort-VPN-Verbindung und Ihrem lokalen Netzwerk mit ExpressRoute-Verbindung ist nicht möglich.
* **Das einfache SKU-Gateway wird nicht unterstützt.** Sie müssen für das [ExpressRoute-Gateway](expressroute-about-virtual-network-gateways.md) und das [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) jeweils ein anderes Gateway als ein einfaches SKU-Gateway verwenden.
* **Nur das routenbasierte VPN-Gateway wird unterstützt.** Sie müssen ein routenbasiertes [VPN-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) verwenden.
* **Für das VPN-Gateway sollte eine statische Route konfiguriert werden.** Wenn Ihr lokales Netzwerk mit ExpressRoute und einem Standort-zu-Standort-VPN verbunden ist, müssen Sie eine statische Route in Ihrem lokalen Netzwerk konfiguriert haben, um die Standort-zu-Standort-VPN-Verbindung an das öffentliche Internet weiterzuleiten.
* **Ohne Angabe verwendet VPN Gateway standardmäßig ASN 65515.** Azure VPN Gateway unterstützt das BGP-Routingprotokoll. Sie können die AS-Nummer (ASN) für ein virtuelles Netzwerk angeben, indem Sie den Switch „-Asn“ hinzufügen. Ohne Angabe dieses Parameters wird standardmäßig die AS-Nummer 65515 verwendet. Sie können eine beliebige ASN für die Konfiguration verwenden. Sollten Sie sich für einen anderen Wert als 65515 entscheiden, müssen Sie allerdings das Gateway zurücksetzen, damit die Einstellung wirksam wird.

## <a name="configuration-designs"></a>Konfigurationsentwürfe
### <a name="configure-a-site-to-site-vpn-as-a-failover-path-for-expressroute"></a>Konfigurieren eines Standort-zu-Standort-VPN als Failoverpfad für ExpressRoute
Sie können eine Standort-zu-Standort-VPN-Verbindung als Sicherung für ExpressRoute konfigurieren. Diese Verbindung gilt nur für virtuelle Netzwerke, die mit dem privaten Azure-Peeringpfad verknüpft sind. Es gibt keine VPN-basierte Failoverlösung für Dienste, auf die per Azure Microsoft-Peering zugegriffen wird. Die ExpressRoute-Verbindung ist immer der primäre Link. Daten durchlaufen den Pfad des Site-to-Site-VPNs nur, wenn bei der ExpressRoute-Verbindung ein Fehler auftritt. Zur Vermeidung des asymmetrischen Routings sollte für die Konfiguration Ihres lokalen Netzwerks auch die ExpressRoute-Verbindung dem Site-to-Site-VPN vorgezogen werden. Sie können dem ExpressRoute-Pfad Vorrang einräumen, indem Sie für die Routen, die über ExpressRoute verlaufen, einen höheren lokalen Präferenzwert festlegen. 

> [!NOTE]
> Die ExpressRoute-Verbindung wird dem Site-to-Site-VPN vorgezogen, doch wenn beide Verbindungen identisch sind, verwendet Azure die längste Präfixübereinstimmung, um die Route zum Ziel des Pakets auszuwählen.
> 
> 

![Koexistenz](media/expressroute-howto-coexist-resource-manager/scenario1.jpg)

### <a name="configure-a-site-to-site-vpn-to-connect-to-sites-not-connected-through-expressroute"></a>Konfigurieren einer Standort-zu-Standort-VPN zum Herstellen einer Verbindung mit Websites, die nicht über ExpressRoute verbunden sind
Sie können Ihr Netzwerk so konfigurieren, dass einige Sites direkt mit Azure über ein Standort-zu-Standort-VPN und andere über ExpressRoute verbunden sind. 

![Koexistenz](media/expressroute-howto-coexist-resource-manager/scenario2.jpg)

> [!NOTE]
> Sie können ein virtuelles Netzwerk nicht als Transitrouter konfigurieren.
> 
> 

## <a name="selecting-the-steps-to-use"></a>Auswählen der zu verwendenden Schritte
Sie haben die Wahl zwischen zwei Vorgehensweisen. Welches Konfigurationsverfahren Sie wählen, hängt davon ab, ob Sie über ein vorhandenes virtuelles Netzwerk verfügen, mit dem Sie eine Verbindung herstellen möchten, oder ob Sie ein neues virtuelles Netzwerk erstellen möchten.

* Ich verfüge nicht über ein VNET und muss eines erstellen.
  
    Wenn Sie noch nicht über ein virtuelles Netzwerk verfügen, werden Sie in diesem Verfahren durch die Schritte zum Erstellen eines neuen virtuellen Netzwerks mithilfe des Resource Manager-Bereitstellungsmodells und zum Erstellen neuer ExpressRoute- sowie Site-to-Site-VPN-Verbindungen geführt. Führen Sie zum Konfigurieren eines virtuellen Netzwerks die Schritte in [So erstellen Sie ein neues virtuelles Netzwerk und parallele Verbindungen](#new) aus.
* Ich verfüge bereits über ein VNET, das auf dem Resource Manager-Bereitstellungsmodell basiert.
  
    Möglicherweise haben Sie bereits ein virtuelles Netzwerk mit einer vorhandenen Standort-zu-Standort-VPN-Verbindung oder einer ExpressRoute-Verbindung. Bei diesem Szenario müssen Sie das vorhandene Gateway löschen, wenn die Gatewaysubnetzmaske /28 oder kleiner (/28, /29 usw.) ist. Im Abschnitt [So konfigurieren Sie parallele Verbindungen für ein bereits vorhandenes VNET](#add) werden Sie durch die Schritte zum Löschen des Gateways und zum anschließenden Erstellen neuer ExpressRoute- und Site-to-Site-VPN-Verbindungen geführt.
  
    Wenn Sie Ihr Gateway löschen und neu erstellen, kommt es für Ihre standortübergreifenden Verbindungen zu Ausfallzeiten. Die VMs und Dienste können aber immer noch über den Load Balancer kommunizieren, während Sie Ihr Gateway konfigurieren, wenn sie entsprechend konfiguriert sind.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]


## <a name="new"></a>So erstellen Sie ein neues virtuelles Netzwerk und parallele Verbindungen
Dieses Verfahren führt Sie durch das Erstellen eines VNETs sowie durch das Erstellen paralleler Site-to-Site- und ExpressRoute-Verbindungen. Für diese Konfiguration werden unter Umständen Cmdlets verwendet, mit denen Sie nicht so vertraut sind. Achten Sie darauf, die in dieser Anleitung angegebenen Cmdlets zu verwenden.

1. Melden Sie sich an, und wählen Sie Ihr Abonnement aus.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Legen Sie Variablen fest.

   ```azurepowershell-interactive
   $location = "Central US"
   $resgrp = New-AzResourceGroup -Name "ErVpnCoex" -Location $location
   $VNetASN = 65515
   ```
3. Erstellen Sie ein virtuelles Netzwerk mit einem Gatewaysubnetz. Weitere Informationen zum Erstellen von virtuellen Netzwerken finden Sie unter [Create a virtual network](../virtual-network/manage-virtual-network.md#create-a-virtual-network) (Erstellen eines virtuellen Netzwerks). Weitere Informationen zum Erstellen von Subnetzen finden Sie unter [Erstellen eines Subnetzes](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
   
   > [!IMPORTANT]
   > Das Gatewaysubnetz muss die Größe /27 haben oder ein kürzeres Präfix aufweisen (z.B. /26 oder /25).
   > 
   > 
   
    Erstellen Sie ein neues VNET.

   ```azurepowershell-interactive
   $vnet = New-AzVirtualNetwork -Name "CoexVnet" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AddressPrefix "10.200.0.0/16"
   ```
   
    Fügen Sie Subnetze hinzu.

   ```azurepowershell-interactive
   Add-AzVirtualNetworkSubnetConfig -Name "App" -VirtualNetwork $vnet -AddressPrefix "10.200.1.0/24"
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Speichern Sie die VNET-Konfiguration.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. <a name="vpngw"></a>Erstellen Sie als Nächstes Ihr Standort-zu-Standort-VPN Gateway. Weitere Informationen zur VPN Gateway-Konfiguration finden Sie unter [Erstellen eines VNet mit einer Site-to-Site-Verbindung mithilfe von PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md). Die GatewaySku wird nur für die VPN-Gateways *VpnGw1*, *VpnGw2*, *VpnGw3*, *Standard* und *HighPerformance* unterstützt. Die gemeinsame Verwendung von VPN Gateway und ExpressRoute wird in der Basic-SKU nicht unterstützt. Für VpnType muss *RouteBased* verwendet werden.

   ```azurepowershell-interactive
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
   $gwIP = New-AzPublicIpAddress -Name "VPNGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
   $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "VPNGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
   New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1"
   ```
   
    Das Azure-VPN-Gateway unterstützt das BGP-Routingprotokoll. Sie können die AS-Nummer (ASN) für dieses virtuelle Netzwerk angeben, indem Sie den -Asn-Switch im folgenden Befehl hinzufügen. Wenn Sie diesen Parameter nicht angeben, wird standardmäßig die AS-Nummer 65515 verwendet.

   ```azurepowershell-interactive
   $azureVpn = New-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "Vpn" -VpnType "RouteBased" -GatewaySku "VpnGw1" -Asn $VNetASN
   ```
   
    Sie finden die BGP-Peering-IP und die AS-Nummer, die Azure für das VPN-Gateway verwendet, unter „$azureVpn.BgpSettings.BgpPeeringAddress“ und „$azureVpn.BgpSettings.Asn“. Weitere Informationen finden Sie unter [Konfigurieren von BGP auf Azure VPN Gateways mithilfe von Azure Resource Manager und PowerShell](../vpn-gateway/vpn-gateway-bgp-resource-manager-ps.md) für das Azure VPN Gateway.
5. Erstellen Sie eine VPN-Gateway-Entität für einen lokalen Standort. Mit diesem Befehl wird das lokale VPN-Gateway nicht konfiguriert. Stattdessen können Sie mit ihm die Einstellungen des lokalen Gateway bereitstellen, wie z. B. die öffentliche IP-Adresse und der lokale Adressraum, sodass eine Verbindung mit dem Azure-VPN-Gateway hergestellt werden kann.
   
    Falls Ihr lokales VPN-Gerät nur statisches Routing unterstützt, können Sie die statischen Routen wie folgt konfigurieren:

   ```azurepowershell-interactive
   $MyLocalNetworkAddress = @("10.100.0.0/16","10.101.0.0/16","10.102.0.0/16")
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress *<Public IP>* -AddressPrefix $MyLocalNetworkAddress
   ```
   
    Falls Ihr lokales VPN-Gerät das BGP unterstützt und Sie das dynamische Routing aktivieren möchten, müssen Sie die BGP-Peering-IP und die AS-Nummer für Ihr lokales VPN-Gerät kennen.

   ```azurepowershell-interactive
   $localVPNPublicIP = "<Public IP>"
   $localBGPPeeringIP = "<Private IP for the BGP session>"
   $localBGPASN = "<ASN>"
   $localAddressPrefix = $localBGPPeeringIP + "/32"
   $localVpn = New-AzLocalNetworkGateway -Name "LocalVPNGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -GatewayIpAddress $localVPNPublicIP -AddressPrefix $localAddressPrefix -BgpPeeringAddress $localBGPPeeringIP -Asn $localBGPASN
   ```
6. Konfigurieren Sie Ihr lokales VPN-Gerät für die Verbindung zum neuen Azure VPN Gateway. Weitere Informationen zu VPN-Gerätekonfiguration finden Sie unter [VPN-Gerätekonfiguration](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

7. Verknüpfen Sie das Standort-zu-Standort-VPN-Gateway in Azure mit dem lokalen Gateway.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   New-AzVirtualNetworkGatewayConnection -Name "VPNConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $azureVpn -LocalNetworkGateway2 $localVpn -ConnectionType IPsec -SharedKey <yourkey>
   ```
 

8. Überspringen Sie die Schritte 8 und 9, und springen Sie zu Schritt 10, wenn Sie eine Verbindung mit einer vorhandenen ExpressRoute-Verbindung herstellen. Konfigurieren Sie ExpressRoute-Verbindungen. Weitere Informationen zum Konfigurieren von ExpressRoute-Verbindungen finden Sie unter [Erstellen und Ändern einer ExpressRoute-Verbindung mit PowerShell](expressroute-howto-circuit-arm.md).


9. Konfigurieren Sie das private Azure-Peering über die ExpressRoute-Verbindung. Weitere Informationen zur Konfiguration des privaten Azure-Peerings über die ExpressRoute-Verbindung finden Sie unter [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung mithilfe von PowerShell](expressroute-howto-routing-arm.md).

10. <a name="gw"></a>Erstellen Sie ein ExpressRoute-Gateway. Weitere Informationen zur Konfiguration des ExpressRoute-Gateways finden Sie unter [ExpressRoute-Gatewaykonfiguration](expressroute-howto-add-gateway-resource-manager.md). GatewaySKU muss *Standard*, *HighPerformance* oder *UltraPerformance* lauten.

    ```azurepowershell-interactive
    $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
    $gwIP = New-AzPublicIpAddress -Name "ERGatewayIP" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -AllocationMethod Dynamic
    $gwConfig = New-AzVirtualNetworkGatewayIpConfig -Name "ERGatewayIpConfig" -SubnetId $gwSubnet.Id -PublicIpAddressId $gwIP.Id
    $gw = New-AzVirtualNetworkGateway -Name "ERGateway" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -IpConfigurations $gwConfig -GatewayType "ExpressRoute" -GatewaySku Standard
    ```
11. Verknüpfen Sie das ExpressRoute-Gateway mit dem ExpressRoute-Schaltkreis. Nachdem dieser Schritt abgeschlossen ist, wird die Verbindung zwischen dem lokalen Netzwerk und Azure durch ExpressRoute eingerichtet. Weitere Informationen zum Verknüpfungsvorgang finden Sie unter [Verknüpfen von VNETs mit ExpressRoute](expressroute-howto-linkvnet-arm.md).

    ```azurepowershell-interactive
    $ckt = Get-AzExpressRouteCircuit -Name "YourCircuit" -ResourceGroupName "YourCircuitResourceGroup"
    New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName $resgrp.ResourceGroupName -Location $location -VirtualNetworkGateway1 $gw -PeerId $ckt.Id -ConnectionType ExpressRoute
    ```

## <a name="add"></a>So konfigurieren Sie parallele Verbindungen für ein bereits vorhandenes VNET
Wenn Sie über virtuelles Netzwerk mit nur einem zugehörigen Gateway (Standort-zu-Standort-VPN-Gateway) verfügen und ein weiteres Gateway eines anderen Typs (z.B. ExpressRoute-Gateway) hinzufügen möchten, überprüfen Sie die Größe des Gateway-Subnetzes. Bei einer Gateway-Subnetzgröße von mindestens /27 können Sie die folgenden Schritte überspringen und die Schritte im vorherigen Abschnitt ausführen, um entweder ein Standort-zu-Standort-VPN-Gateway oder ein ExpressRoute-Gateway hinzuzufügen. Wenn das Gatewaysubnetz die Größe /28 oder /29 hat, müssen Sie zunächst das Gateway des virtuellen Netzwerks löschen, um die Größe des Gatewaysubnetzes zu erhöhen. Führen Sie dazu die in diesem Abschnitt beschriebenen Schritte aus.

Für diese Konfiguration werden unter Umständen Cmdlets verwendet, mit denen Sie nicht so vertraut sind. Achten Sie darauf, die in dieser Anleitung angegebenen Cmdlets zu verwenden.

1. Löschen Sie das vorhandene ExpressRoute- oder Standort-zu-Standort-VPN Gateway.

   ```azurepowershell-interactive 
   Remove-AzVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
   ```
2. Löschen Sie das Gatewaysubnetz.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup> Remove-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
   ```
3. Fügen Sie ein Gatewaysubnetz hinzu, das mindestens die Größe /27 hat.
   
   > [!NOTE]
   > Wenn in Ihrem virtuellen Netzwerk nicht mehr ausreichend IP-Adressen vorhanden sind, um die Größe des Gateway-Subnetzes zu erhöhen, müssen Sie mehr IP-Adressraum hinzufügen.
   > 
   > 

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
   Add-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.0/24"
   ```
   
    Speichern Sie die VNET-Konfiguration.

   ```azurepowershell-interactive
   $vnet = Set-AzVirtualNetwork -VirtualNetwork $vnet
   ```
4. Sie verfügen nun über ein virtuelles Netzwerk ohne Gateways. Zum Erstellen neuer Gateways und Einrichten der Verbindungen führen Sie die Schritte im vorherigen Abschnitt aus.

## <a name="to-add-point-to-site-configuration-to-the-vpn-gateway"></a>So fügen Sie dem VPN Gateway eine Punkt-zu-Standort-Konfiguration hinzu

Sie können die unten angegebenen Schritte ausführen, um dem VPN-Gateway bei einer parallelen Einrichtung eine Punkt-zu-Standort-Konfiguration hinzuzufügen. Um das VPN-Stammzertifikat hochzuladen, müssen Sie entweder PowerShell lokal auf Ihrem Computer installieren oder das Azure-Portal verwenden.

1. Fügen Sie einen VPN-Clientadresspool hinzu.

   ```azurepowershell-interactive
   $azureVpn = Get-AzVirtualNetworkGateway -Name "VPNGateway" -ResourceGroupName $resgrp.ResourceGroupName
   Set-AzVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $azureVpn -VpnClientAddressPool "10.251.251.0/24"
   ```
2. Laden Sie das VPN-Stammzertifikat für Ihr VPN Gateway in Azure hoch. In diesem Beispiel wird davon ausgegangen, dass das Stammzertifikat auf dem lokalen Computer gespeichert wird, auf dem die folgenden PowerShell-Cmdlets ausgeführt werden. Es wird ebenfalls davon ausgegangen, dass Sie PowerShell lokal ausführen. Sie können das Zertifikat auch über das Azure-Portal hochladen.

   ```powershell
   $p2sCertFullName = "RootErVpnCoexP2S.cer" 
   $p2sCertMatchName = "RootErVpnCoexP2S" 
   $p2sCertToUpload=get-childitem Cert:\CurrentUser\My | Where-Object {$_.Subject -match $p2sCertMatchName} 
   if ($p2sCertToUpload.count -eq 1){write-host "cert found"} else {write-host "cert not found" exit} 
   $p2sCertData = [System.Convert]::ToBase64String($p2sCertToUpload.RawData) 
   Add-AzVpnClientRootCertificate -VpnClientRootCertificateName $p2sCertFullName -VirtualNetworkGatewayname $azureVpn.Name -ResourceGroupName $resgrp.ResourceGroupName -PublicCertData $p2sCertData
   ```

Weitere Informationen zu Punkt-zu-Standort-VPN-Verbindungen finden Sie unter [Konfigurieren einer Punkt-zu-Standort-Verbindung](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
