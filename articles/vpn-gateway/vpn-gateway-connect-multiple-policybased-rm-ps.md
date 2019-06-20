---
title: 'Herstellen einer Verbindung zwischen Azure-VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten: Azure Resource Manager: PowerShell | Microsoft-Dokumentation'
description: Konfigurieren Sie routenbasierte Azure-VPN-Gateways für mehrere richtlinienbasierte VPN-Geräte mit Azure Resource Manager und PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 9085d5ee21b1e955b7d9416a379ee730ba26ad3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150077"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Herstellen einer Verbindung zwischen Azure-VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten mit PowerShell

In diesem Artikel wird beschrieben, wie Sie ein routenbasiertes Azure-VPN-Gateway konfigurieren, um eine Verbindung mit mehreren lokalen richtlinienbasierten VPN-Geräten herzustellen, indem benutzerdefinierte IPsec/IKE-Richtlinien für S2S-VPN-Verbindungen genutzt werden.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Grundlegendes zu richtlinien- und routenbasierten VPN-Gateways

Richtlinien- *und* routenbasierte VPN-Geräte unterscheiden sich darin, wie die IPsec-Datenverkehrsselektoren für eine Verbindung festgelegt werden:

* Für **richtlinienbasierte** VPN-Geräte werden die Kombinationen aus Präfixen beider Netzwerke verwendet, um zu definieren, wie Datenverkehr mithilfe von IPsec-Tunneln verschlüsselt bzw. entschlüsselt wird. Dies wird normalerweise basierend auf Firewallgeräten erstellt, für die eine Paketfilterung durchgeführt wird. Die Ver- und Entschlüsselung von IPsec-Tunneln wird der Engine für die Paketfilterung und -verarbeitung hinzugefügt.
* Für **routenbasierte** VPN-Geräte werden Any-to-Any-Datenverkehrsselektoren (mit Platzhalter) verwendet, und mit Routing-/Weiterleitungstabellen wird das Leiten von Datenverkehr an verschiedene IPsec-Tunnel ermöglicht. Die Basis für die Erstellung bilden normalerweise Routerplattformen, auf denen jeder IPsec-Tunnel als Netzwerkschnittstelle oder virtuelle Tunnelschnittstelle (VTI) modelliert ist.

Die beiden Modelle sind in den folgenden Diagrammen dargestellt:

### <a name="policy-based-vpn-example"></a>Beispiel für richtlinienbasiertes VPN
![richtlinienbasiert](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Beispiel für routenbasiertes VPN
![routenbasiert](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Azure-Unterstützung für richtlinienbasiertes VPN
Azure unterstützt derzeit beide Modi von VPN-Gateways: routenbasierte VPN-Gateways und richtlinienbasierte VPN-Gateways. Diese werden auf unterschiedlichen internen Plattformen erstellt, und dies führt zu unterschiedlichen Spezifikationen:

|                          | **Richtlinienbasiertes VPN Gateway** | **Routenbasiertes VPN Gateway**               |
| ---                      | ---                         | ---                                      |
| **Azure-Gateway-SKU**    | Basic                       | Basic, Standard, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **IKE-Version**          | IKEv1                       | IKEv2                                    |
| **Max. S2S-Verbindungen** | **1**                       | Basic/Standard: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Mit der benutzerdefinierten IPsec/IKE-Richtlinie können Sie jetzt routenbasierte Azure-VPN-Gateways konfigurieren, um mithilfe von auf Präfixen basierenden Datenverkehrsselektoren mit der Option „**PolicyBasedTrafficSelectors**“ eine Verbindung mit lokalen richtlinienbasierten VPN-Geräten herzustellen. Mit dieser Funktion können Sie eine Verbindung von einem virtuellen Azure-Netzwerk und VPN-Gateway mit mehreren lokalen richtlinienbasierten VPN-/Firewallgeräten herstellen, indem Sie die Einzelverbindungsbeschränkung für die aktuellen VPN-Gateways aufheben, die auf Azure-Richtlinien basieren.

> [!IMPORTANT]
> 1. Zur Aktivierung dieser Konnektivität müssen Ihre lokalen richtlinienbasierten VPN-Geräte **IKEv2** unterstützen, damit eine Verbindung mit den routenbasierten Azure-VPN-Gateways hergestellt werden kann. Überprüfen Sie die Spezifikationen Ihres VPN-Geräts.
> 2. Für die lokalen Netzwerke, für die Verbindungen über richtlinienbasierte VPN-Geräte mit diesem Mechanismus hergestellt werden, sind nur Verbindungen mit dem virtuellen Azure-Netzwerk möglich. **Die Übertragung auf andere lokale Netzwerke oder virtuelle Netzwerke über dasselbe Azure-VPN-Gateway ist nicht möglich**.
> 3. Die Konfigurationsoption ist Teil der benutzerdefinierten IPsec/IKE-Verbindungsrichtlinie. Wenn Sie die Option für den richtlinienbasierten Datenverkehrsselektor aktivieren, müssen Sie die vollständige Richtlinie angeben (IPsec/IKE-Verschlüsselungsalgorithmen und -Integritätsalgorithmen, Schlüsselstärken und SA-Gültigkeitsdauern).

Im folgenden Diagramm ist dargestellt, warum das Transitrouting per Azure-VPN-Gateway mit der Option für die richtlinienbasierte Vorgehensweise nicht funktioniert:

![richtlinienbasierter Transit](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Wie im Diagramm zu sehen ist, verfügt das Azure-VPN-Gateway über Datenverkehrsselektoren vom virtuellen Netzwerk bis zu den einzelnen lokalen Netzwerkpräfixen, aber nicht über die verbindungsübergreifenden Präfixe. Beispielsweise können die lokalen Standorte 2, 3 und 4 jeweils mit VNET1 kommunizieren, aber untereinander keine Verbindung über das Azure-VPN-Gateway herstellen. Im Diagramm sind die verbindungsübergreifenden Datenverkehrsselektoren zu sehen, die bei dieser Konfiguration nicht im Azure-VPN-Gateway verfügbar sind.

## <a name="configurepolicybased"></a>Konfigurieren von richtlinienbasierten Datenverkehrsselektoren für eine Verbindung

Die Anleitungen in diesem Artikel basieren auf dem gleichen Beispiel wie unter [Konfigurieren der IPsec/IKE-Richtlinie für S2S-VPN- oder VNET-zu-VNET-Verbindungen](vpn-gateway-ipsecikepolicy-rm-powershell.md), um eine S2S-VPN-Verbindung herzustellen. Dies ist im folgenden Diagramm dargestellt:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Workflow zum Aktivieren dieser Konnektivität:
1. Erstellen Sie das virtuelle Netzwerk, das VPN-Gateway und das lokale Netzwerkgateway für Ihre standortübergreifende Verbindung.
2. Erstellen Sie eine IPsec/IKE-Richtlinie.
3. Wenden Sie die Richtlinie beim Erstellen einer S2S- oder VNet-zu-VNet-Verbindung an, und **aktivieren Sie die richtlinienbasierten Datenverkehrsselektoren** für die Verbindung.
4. Wenn die Verbindung bereits erstellt wurde, können Sie die Richtlinie für eine vorhandene Verbindung anwenden oder aktualisieren.

## <a name="before-you-begin"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial) registrieren.

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Aktivieren von richtlinienbasierten Datenverkehrsselektoren für eine Verbindung

Stellen Sie sicher, dass Sie [Teil 3 des Artikels „Konfigurieren einer IPsec/IKE-Richtlinie“](vpn-gateway-ipsecikepolicy-rm-powershell.md) für diesen Abschnitt durchgearbeitet haben. Im folgenden Beispiel werden die gleichen Parameter und Schritte verwendet:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Schritt 1: Erstellen des virtuellen Netzwerks, VPN-Gateways und Gateways des lokalen Netzwerks

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Herstellen einer Verbindung mit Ihrem Abonnement und Deklarieren Ihrer Variablen

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Deklarieren Sie Ihre Variablen. Für diese Übung werden die folgenden Variablen verwendet:

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Erstellen des virtuellen Netzwerks, VPN-Gateways und Gateways des lokalen Netzwerks

Erstellen Sie eine Ressourcengruppe.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Verwenden Sie das folgende Beispiel, um das virtuelle Netzwerk „TestVNet1“ mit drei Subnetzen und das VPN-Gateway zu erstellen. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Schritt 2: Erstellen einer S2S-VPN-Verbindung mit einer IPsec/IKE-Richtlinie

#### <a name="1-create-an-ipsecike-policy"></a>1. Erstellen Sie eine IPsec/IKE-Richtlinie.

> [!IMPORTANT]
> Sie müssen eine IPsec/IKE-Richtlinie erstellen, um für die Verbindung die Option „UsePolicyBasedTrafficSelectors“ zu aktivieren.

Im folgenden Beispielskript wird eine IPsec/IKE-Richtlinie mit diesen Algorithmen und Parametern erstellt:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, ohne PFS, SA-Gültigkeitsdauer 14400 Sekunden und 102.400.000 KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Erstellen der S2S-VPN-Verbindung mit richtlinienbasierten Datenverkehrsselektoren und IPsec/IKE-Richtlinie
Erstellen Sie eine S2S-VPN-Verbindung, und wenden Sie die im vorherigen Schritt erstellte IPsec/IKE-Richtlinie an. Achten Sie auf den zusätzlichen Parameter „-UsePolicyBasedTrafficSelectors $True“, der richtlinienbasierte Datenverkehrsselektoren für die Verbindung ermöglicht.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Nach dem Ausführen der Schritte wird für die S2S-VPN-Verbindung die definierte IPsec/IKE-Richtlinie verwendet, und für die Verbindung werden richtlinienbasierte Datenverkehrsselektoren aktiviert. Sie können die gleichen Schritte wiederholen, um lokalen richtlinienbasierten VPN-Geräten weitere Verbindungen über das Azure-VPN-Gateway hinzuzufügen.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Aktualisieren von richtlinienbasierten Datenverkehrsselektoren für eine Verbindung
Im letzten Abschnitt wird veranschaulicht, wie Sie die Option für richtlinienbasierte Datenverkehrsselektoren für eine vorhandene S2S-VPN-Verbindung aktualisieren.

### <a name="1-get-the-connection"></a>1. Abrufen der Verbindung
Rufen Sie die Verbindungsressource ab.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Überprüfen der Option für richtlinienbasierte Datenverkehrsselektoren
Mit der folgenden Zeile können Sie ermitteln, ob die richtlinienbasierten Datenverkehrsselektoren für die Verbindung verwendet werden:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Wenn für die Zeile **True** zurückgegeben wird, sind richtlinienbasierte Datenverkehrsselektoren für die Verbindung konfiguriert. Andernfalls wird **False** zurückgegeben.

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Aktivieren/Deaktivieren von richtlinienbasierten Datenverkehrsselektoren für eine Verbindung
Nachdem Sie die Verbindungsressource erhalten haben, können Sie die Option aktivieren oder deaktivieren.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>So aktivieren Sie „UsePolicyBasedTrafficSelectors“
Im folgenden Beispiel wird die Option für die richtlinienbasierten Datenverkehrsselektoren aktiviert, aber die IPsec/IKE-Richtlinie bleibt unverändert:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>So deaktivieren Sie „UsePolicyBasedTrafficSelectors“
Im folgenden Beispiel wird die Option für die richtlinienbasierten Datenverkehrsselektoren deaktiviert, aber die IPsec/IKE-Richtlinie bleibt unverändert:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Nächste Schritte
Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Für diese Schritte finden Sie Informationen unter [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

Ausführliche Informationen zu benutzerdefinierten IPsec/IKE-Richtlinien finden Sie unter [Konfigurieren der IPsec/IKE-Richtlinie für S2S-VPN- oder VNet-zu-VNet-Verbindungen](vpn-gateway-ipsecikepolicy-rm-powershell.md).
