---
title: 'Konfigurieren von Aktiv/Aktiv-S2S-VPN-Verbindungen für VPN-Gateways: Azure Resource Manager: PowerShell | Microsoft-Dokumentation'
description: In diesem Artikel wird die Konfiguration von Aktiv/Aktiv-Verbindungen mit Azure VPN Gateways mithilfe von Azure Resource Manager und PowerShell beschrieben.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang, cherylmc
ms.openlocfilehash: 7ba4fb32ddfb8b3eb88d2dbfce265b070d521414
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66119423"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurieren von Aktiv/Aktiv-S2S-VPN-Verbindungen mit Azure VPN Gateways

In diesem Artikel werden die Schritte zur Herstellung von Aktiv/Aktiv-Verbindungen (lokal und VNet zu VNet) mit dem Resource Manager-Bereitstellungsmodell und PowerShell beschrieben.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Informationen zu standortübergreifenden Verbindungen mit hoher Verfügbarkeit
Um für lokale und VNET-zu-VNET-Verbindungen Hochverfügbarkeit zu erzielen, sollten Sie mehrere VPN-Gateways bereitstellen und mehrere parallele Verbindungen zwischen Ihren Netzwerken und Azure herstellen. Eine Übersicht über Verbindungsoptionen und die Topologie finden Sie unter [Standortübergreifende Verbindungen und VNET-zu-VNET-Verbindungen mit hoher Verfügbarkeit](vpn-gateway-highlyavailable.md).

Dieser Artikel enthält die Anleitung zum Einrichten einer standortübergreifenden Aktiv/Aktiv-VPN-Verbindung und einer Aktiv/Aktiv-Verbindung zwischen zwei virtuellen Netzwerken.

* [Teil 1: Erstellen und Konfigurieren des Azure VPN Gateway im Aktiv/Aktiv-Modus](#aagateway)
* [Teil 2: Herstellen von standortübergreifenden Aktiv/Aktiv-Verbindungen](#aacrossprem)
* [Teil 3: Herstellen von Aktiv/Aktiv-VNet-zu-VNet-Verbindungen](#aav2v)

Wenn Sie bereits über ein VPN-Gateway verfügen, haben Sie folgende Möglichkeit:
* [Aktualisieren des vorhandenen Gateways zwischen Aktiv/Aktiv und Aktiv/Standby](#aaupdate)

Sie können dies kombinieren, um eine komplexere Netzwerktopologie mit hoher Verfügbarkeit zu erstellen, die Ihre Anforderungen erfüllt.

> [!IMPORTANT]
> Im Aktiv/Aktiv-Modus werden nur die folgenden SKUs verwendet: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * HighPerformance (für alte SKUs)

## <a name ="aagateway"></a>Teil 1: Erstellen und Konfigurieren von Aktiv/Aktiv-VPN-Gateways
Mit den folgenden Schritten wird Ihr Azure VPN Gateway in Aktiv/Aktiv-Modi konfiguriert. Die wichtigsten Unterschiede zwischen Aktiv/Aktiv- und Aktiv/Standby-Gateways sind:

* Sie müssen zwei Gateway-IP-Konfigurationen mit zwei öffentlichen IP-Adressen erstellen.
* Sie müssen das EnableActiveActiveFeature-Flag festlegen.
* Die Gateway-SKU muss VpnGw1, VpnGw2, VpnGw3 oder HighPerformance (alte SKU) sein.

Die anderen Eigenschaften sind mit den Eigenschaften von anderen Gateways identisch, die nicht den Aktiv/Aktiv-Modus aufweisen. 

### <a name="before-you-begin"></a>Voraussetzungen
* Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Sie müssen die PowerShell-Cmdlets für Azure-Ressourcen-Manager installieren. Weitere Informationen zur Installation der PowerShell-Cmdlets finden Sie unter [Übersicht über Azure PowerShell](/powershell/azure/overview).

### <a name="step-1---create-and-configure-vnet1"></a>Schritt 1: Erstellen und Konfigurieren von VNet1
#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen
Bei dieser Übung beginnen wir mit dem Deklarieren der Variablen. Im folgenden Beispiel werden die Variablen mit den Werten für diese Übung deklariert. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen, wenn Sie die Konfiguration für die Produktion durchführen. Sie können diese Variablen verwenden, wenn Sie die Schritte durchgehen, um sich mit dieser Art der Konfiguration vertraut zu machen. Ändern Sie die Variablen, kopieren Sie sie, und fügen Sie sie in die PowerShell-Konsole ein.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Herstellen einer Verbindung mit Ihrem Abonnement und Erstellen einer neuen Ressourcengruppe
Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die Ressourcen-Manager-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../powershell-azure-resource-manager.md).

Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Erstellen von TestVNet1
Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen TestVNet1 und drei Subnetzen erstellt. Diese haben die Namen „GatewaySubnet“, „FrontEnd“ und „Backend“. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Schritt 2: Erstellen des VPN-Gateways für TestVNet1 mit Aktiv/Aktiv-Modus
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Erstellen der öffentlichen IP-Adressen und Gateway-IP-Konfigurationen
Fordern Sie zwei öffentliche IP-Adressen zur Zuordnung zu dem Gateway an, das Sie für Ihr VNet erstellen. Definieren Sie auch die erforderlichen Subnetz- und IP-Konfigurationen.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Erstellen des VPN-Gateways mit Aktiv/Aktiv-Konfiguration
Erstellen Sie das virtuelle Netzwerkgateway für Ihr TestVNet1. Beachten Sie, dass zwei GatewayIpConfig-Einträge vorhanden sind und das EnableActiveActiveFeature-Flag festgelegt wurde. Das Erstellen eines Gateways kann einige Zeit dauern (45 Minuten oder mehr).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Abrufen der öffentlichen IP-Adressen für das Gateway und der BGP-Peer-IP-Adresse
Nachdem das Gateway erstellt wurde, müssen Sie die BGP-Peer-IP-Adresse auf dem Azure VPN Gateway abrufen. Diese Adresse ist erforderlich, um das Azure VPN Gateway als BGP-Peer für Ihre lokalen VPN-Geräte zu konfigurieren.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Verwenden Sie die folgenden Cmdlets zum Anzeigen der beiden öffentlichen IP-Adressen, die für Ihr VPN-Gateway zugeordnet wurden, und der entsprechenden BGP-Peer-IP-Adressen für jede Gatewayinstanz:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Die Reihenfolge der öffentlichen IP-Adressen für die Gatewayinstanzen und die entsprechenden BGP-Peering-Adressen ist identisch. In diesem Beispiel wird für die Gateway-VM mit der öffentlichen IP-Adresse 40.112.190.5 die Adresse 10.12.255.4 als BGP-Peering-Adresse verwendet, und für das Gateway mit der IP-Adresse 138.91.156.129 wird die Adresse 10.12.255.5 verwendet. Diese Informationen werden benötigt, wenn Sie Ihre lokalen VPN-Geräte für die Verbindung mit dem Aktiv/Aktiv-Gateway einrichten. Das Gateway wird unten im Diagramm mit allen Adressen angezeigt:

![Aktiv/Aktiv-Gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Nachdem das Gateway erstellt wurde, können Sie es verwenden, um eine standortübergreifende Aktiv/Aktiv-Verbindung oder eine VNet-zu-VNet-Verbindung herzustellen. In den folgenden Abschnitten werden die Übungsschritte beschrieben.

## <a name ="aacrossprem"></a>Teil 2: Herstellen einer standortübergreifenden Aktiv/Aktiv-Verbindung
Um eine standortübergreifende Verbindung herzustellen, müssen Sie ein lokales Netzwerkgateway erstellen, um das lokale VPN-Gerät darzustellen, sowie eine Verbindung, um das Azure VPN Gateway mit dem lokalen Netzwerkgateway zu verbinden. In diesem Beispiel befindet sich das Azure VPN Gateway im Aktiv/Aktiv-Modus. Auch wenn nur ein lokales VPN-Gerät (lokales Netzwerkgateway) und eine Verbindungsressource vorhanden sind, richten daher beide Azure VPN Gateway-Instanzen S2S-VPN-Tunnel zum lokalen Gerät ein.

Schließen Sie zunächst [Teil 1](#aagateway) dieser Übung ab, bevor Sie fortfahren.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Schritt 1: Erstellen und Konfigurieren des lokalen Netzwerkgateways
#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen
In dieser Übung fahren wir mit dem Erstellen der im Diagramm gezeigten Konfiguration fort. Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

In Bezug auf die lokalen Netzwerkgateway-Parameter sind ein paar Dinge zu beachten:

* Der Speicherort und die Ressourcengruppe für das lokale Netzwerkgateway und für das VPN Gateway können gleich oder auch unterschiedlich sein. In diesem Beispiel werden sie in unterschiedlichen Ressourcengruppen angezeigt, aber an demselben Azure-Standort.
* Falls wie oben dargestellt nur ein lokales VPN-Gerät vorhanden ist, kann die Aktiv/Aktiv-Verbindung mit oder ohne BGP-Protokoll funktionieren. In diesem Beispiel wird BGP für die lokale Verbindung verwendet.
* Bei Aktivierung von BGP müssen Sie als Präfix für das lokale Netzwerkgateway die Hostadresse der BGP-Peer-IP-Adresse auf Ihrem VPN-Gerät deklarieren. In diesem Fall ist es das /32-Präfix „10.52.255.253/32“.
* Zur Erinnerung: Sie müssen zwischen Ihren lokalen Netzwerken und Azure VNet unterschiedliche BGP-ASNs verwenden. Wenn sie gleich sind, müssen Sie Ihre VNet-ASN ändern, falls Ihr lokales VPN-Gerät bereits die ASN für eine Peerverbindung mit anderen BGP-Nachbarn verwendet.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Erstellen des lokalen Netzwerkgateways für Site5
Stellen Sie vor dem Fortfahren sicher, dass die Verbindung mit Abonnement 1 noch besteht. Erstellen Sie die Ressourcengruppe, falls dies noch nicht durchgeführt wurde.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Schritt 2: Verbinden des VNet Gateways und des lokalen Netzwerkgateways
#### <a name="1-get-the-two-gateways"></a>1. Abrufen der beiden Gateways

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Erstellen der Verbindung von TestVNet1 zu Site5
In diesem Schritt erstellen Sie die Verbindung zwischen „TestVNet1“ und „Site5_1“ mit der Einstellung „$True“ für „EnableBGP“.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN- und BGP-Parameter für Ihr lokales VPN-Gerät
Das folgende Beispiel listet die Parameter auf, die Sie in den BGP-Konfigurationsbereich auf dem lokalen VPN-Gerät für diese Übung eingeben:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Die Verbindung sollte nach einigen Minuten hergestellt sein, und die BGP-Peeringsitzung wird gestartet, sobald die IPsec-Verbindung hergestellt wurde. In diesem Beispiel wurde bisher nur ein lokales VPN-Gerät konfiguriert. Dies führt zu folgendem Diagramm:

![Aktiv/Aktiv, standortübergreifend](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Schritt 3: Verbinden von zwei lokalen VPN-Geräten mit dem Aktiv/Aktiv-VPN-Gateway
Wenn Sie über zwei VPN-Geräte in demselben lokalen Netzwerk verfügen, können Sie duale Redundanz erzielen, indem Sie für das Azure VPN Gateway eine Verbindung mit dem zweiten VPN-Gerät herstellen.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Erstellen des zweiten lokalen Netwerkgateways für Site5
Die Gateway-IP-Adresse, das Adresspräfix und die BGP-Peeringadresse für das zweite lokale Netzwerkgateway dürfen sich nicht mit dem vorherigen lokalen Netzwerkgateway für dasselbe lokale Netzwerk überschneiden.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Verbinden des VNet-Gateways mit dem zweiten lokalen Netzwerkgateway
Erstellen Sie die Verbindung von TestVNet1 mit „Site5_2“ mit der Einstellung „$True“ für „EnableBGP“.

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN- und BGP-Parameter für Ihr zweites lokales VPN-Gerät
Unten sind wieder die Parameter aufgeführt, die Sie für das zweite VPN-Gerät eingeben:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Nachdem die Verbindung (Tunnel) hergestellt wurde, verfügen Sie für die Verbindung Ihres lokalen Netzwerks mit Azure über VPN-Geräte und Tunnel mit dualer Redundanz:

![Duale Redundanz, standortübergreifend](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Teil 3: Herstellen einer Aktiv/Aktiv-VNet-zu-VNet-Verbindung
In diesem Abschnitt wird eine Aktiv/Aktiv-VNet-zu-VNet-Verbindung mit BGP hergestellt. 

Die Anleitung unten ist eine Fortsetzung der vorherigen obigen Schritte. Sie müssen [Teil 1](#aagateway) ausführen, um TestVNet1 und das VPN-Gateway mit BGP zu erstellen und zu konfigurieren. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Schritt 1: Erstellen von TestVNet2 und des VPN Gateways
Es ist wichtig sicherzustellen, dass sich der IP-Adressbereich des neuen virtuellen Netzwerks (TestVNet2) nicht mit einem Ihrer VNet-Bereiche überschneidet.

In diesem Beispiel gehören die virtuellen Netzwerke zum gleichen Abonnement. Sie können VNet-zu-VNet-Verbindungen zwischen verschiedenen Abonnements einrichten. Informationen dazu finden Sie unter [Konfigurieren einer VNet-zu-VNet-Verbindung mit Azure Resource Manager und PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Fügen Sie beim Erstellen der Verbindungen zum Aktivieren von BGP „-EnableBgp $True“ hinzu.

#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen
Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Erstellen von TestVNet2 in der neuen Ressourcengruppe

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Erstellen des Aktiv/Aktiv-VPN-Gateways für TestVNet2
Fordern Sie zwei öffentliche IP-Adressen zur Zuordnung zu dem Gateway an, das Sie für Ihr VNet erstellen. Definieren Sie auch die erforderlichen Subnetz- und IP-Konfigurationen.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Erstellen Sie das VPN-Gateway mit der ASN und dem Flag „EnableActiveActiveFeature“. Beachten Sie, dass Sie die Standard-ASN auf Ihren Azure VPN Gateways überschreiben müssen. Die ASNs für die verbundenen VNets müssen für die Aktivierung von BGP und Transitrouting unterschiedlich sein.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Schritt 2: Verbinden der TestVNet1 und TestVNet2-Gateways
In diesem Beispiel befinden sich beide Gateways im selben Abonnement. Sie können diesen Schritt in der gleichen PowerShell-Sitzung ausführen.

#### <a name="1-get-both-gateways"></a>1. Abrufen der beiden Gateways
Stellen Sie sicher, dass Sie sich bei Abonnement 1 anmelden und die Verbindung mit diesem Abonnement herstellen.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Erstellen der beiden Verbindungen
In diesem Schritt erstellen Sie die Verbindung von TestVNet1 zu TestVNet2 sowie die Verbindung von TestVNet2 zu TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Aktivieren Sie BGP für BEIDE Verbindungen.
> 
> 

Danach wird die Verbindung innerhalb weniger Minuten hergestellt, und die BGP-Peeringsitzung ist eingerichtet, sobald die VNet-zu-VNet-Verbindung mit dualer Redundanz fertiggestellt ist:

![Aktiv/Aktiv, VNet zu VNet](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Aktualisieren eines vorhandenen VPN-Gateways

Dieser Abschnitt zeigt Ihnen, wie Sie den Modus eines vorhandenen Azure-VPN-Gateways von Aktiv/Standby in Aktiv/Aktiv ändern (oder umgekehrt).

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Ändern eines Aktiv/Standby-Gateways in ein Aktiv/Aktiv-Gateway

Im folgenden Beispiel wird ein Aktiv/Standby-Gateway in ein Aktiv/Aktiv-Gateway konvertiert. Wenn Sie ein Aktiv/Standby- in ein Aktiv/Aktiv-Gateway ändern, erstellen Sie eine weitere öffentliche IP-Adresse und fügen anschließend eine zweite Gateway-IP-Konfiguration hinzu.

#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen

Ersetzen Sie die folgenden für die Beispiele verwendeten Parameter durch die Einstellungen, die Sie für Ihre eigene Konfiguration benötigen, und deklarieren Sie diese Variablen.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Nachdem Sie die Variablen deklariert haben, können Sie dieses Beispiel kopieren und in Ihre PowerShell-Konsole einfügen.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Erstellen der öffentlichen IP-Adresse und anschließendes Hinzufügen der zweiten Gateway-IP-Konfiguration

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Aktivieren des Aktiv/Aktiv-Modus und Aktualisieren des Gateways

In diesem Schritt aktivieren Sie den Aktiv/Aktiv-Modus und aktualisieren das Gateway. Im Beispiel verwendet das VPN-Gateway derzeit eine ältere Standard-SKU. Die Standard-SKU wird im Aktiv/Aktiv-Modus jedoch nicht unterstützt. Um die Größe der alten SKU zu einer unterstützten SKU (in diesem Fall HighPerformance) zu ändern, geben Sie einfach die unterstützte alte SKU an, die Sie verwenden möchten.

* Mit diesem Schritt können Sie eine alte SKU nicht in eine der neuen SKUs ändern. Sie können lediglich die Größe einer alten SKU zu einer anderen unterstützten alten SKU ändern. Beispielsweise können Sie die SKU nicht von „Standard“ zu „VpnGw1“ ändern (obwohl „VpnGw1“ für den Aktiv/Aktiv-Modus unterstützt wird), da es sich bei „Standard“ um eine alte und bei „VpnGw1“ um eine aktuelle SKU handelt. Weitere Informationen zur Größenänderung und Migration von SKUs finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpngateways.md#gwsku).

* Wenn Sie die Größe einer aktuellen SKU ändern möchten, z.B. „VpnGw1“ in „VpnGw3“, ist dies mit diesem Schritt möglich, da die SKUs zu derselben SKU-Familie gehören. Dazu würden Sie folgenden Wert verwenden: ```-GatewaySku VpnGw3```

Wenn Sie dies in Ihrer Umgebung verwenden und die Größe des Gateways nicht ändern müssen, brauchen Sie „-GatewaySku“ nicht anzugeben. Beachten Sie, dass Sie in diesem Schritt das Gatewayobjekt in PowerShell festlegen müssen, um das eigentliche Update auszulösen. Dieses Update kann 30 bis 45 Minuten in Anspruch nehmen, selbst wenn Sie Ihr Gateway nicht ändern.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Ändern eines Aktiv/Aktiv-Gateways in ein Aktiv/Standby-Gateway
#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen

Ersetzen Sie die folgenden für die Beispiele verwendeten Parameter durch die Einstellungen, die Sie für Ihre eigene Konfiguration benötigen, und deklarieren Sie diese Variablen.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Nach dem Deklarieren der Variablen rufen Sie den Namen der IP-Konfiguration auf, die Sie entfernen möchten.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Entfernen der Gateway-IP-Konfiguration und Deaktivieren des Aktiv/Aktiv-Modus

Richten Sie sich nach diesem Beispiel, um die Gateway-IP-Konfiguration zu entfernen und den Aktiv/Aktiv-Modus zu deaktivieren. Beachten Sie, dass Sie das Gatewayobjekt in PowerShell festlegen müssen, um das eigentliche Update auszulösen.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Diese Aktualisierung kann 30 bis 45 Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Für diese Schritte finden Sie Informationen unter [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .
