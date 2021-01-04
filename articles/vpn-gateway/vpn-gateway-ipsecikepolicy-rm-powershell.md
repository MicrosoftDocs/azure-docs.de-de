---
title: IPsec/IKE-Richtlinie für S2S-VPN- oder VNet-zu-VNet-Verbindungen
titleSuffix: Azure VPN Gateway
description: Konfigurieren Sie die IPsec-/IKE-Richtlinie für S2S- oder VNET-zu-VNET-Verbindungen mithilfe von Azure-VPN-Gateways per Azure Resource Manager und PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 96931d2dd94a8a31021ebe62caaefc54f643b007
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649261"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>Konfigurieren der IPsec/IKE-Richtlinie für S2S-VPN- oder VNet-zu-VNet-Verbindungen

In diesem Artikel werden die Schritte zum Konfigurieren der IPsec/IKE-Richtlinie für Site-to-Site-VPN- oder VNet-zu-VNet-Verbindungen mit dem Resource Manager-Bereitstellungsmodell und PowerShell beschrieben.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>IPsec- und IKE-Richtlinienparameter für Azure-VPN-Gateways
Der IPsec- und IKE-Protokollstandard unterstützt ein breites Spektrum von Kryptografiealgorithmen in verschiedenen Kombinationen. Informationen dazu, wie Sie damit erreichen können, dass die standortübergreifende und VNet-zu-VNet-Konnektivität Ihre Konformitäts- oder Sicherheitsanforderungen erfüllt, finden Sie unter [Kryptografische Anforderungen und Azure-VPN-Gateways](vpn-gateway-about-compliance-crypto.md).

Dieser Artikel enthält eine Anleitung zum Erstellen und Konfigurieren einer IPsec/IKE-Richtlinie und Anwenden auf eine neue oder vorhandene Verbindung:

* [Teil 1: Workflow zum Erstellen und Festlegen der IPsec/IKE-Richtlinie](#workflow)
* [Teil 2: Unterstützte Kryptografiealgorithmen und Schlüsselstärken](#params)
* [Teil 3: Erstellen einer neuen S2S-VPN-Verbindung mit einer IPsec/IKE-Richtlinie](#crossprem)
* [Teil 4: Erstellen einer neuen VNet-zu-VNet-Verbindung mit einer IPsec/IKE-Richtlinie](#vnet2vnet)
* [Teil 5: Verwalten (Erstellen/Hinzufügen/Entfernen) der IPsec/IKE-Richtlinie für eine Verbindung](#managepolicy)

> [!IMPORTANT]
> 1. Die IPsec/IKE-Richtlinie kann nur für folgende Gateways verwendet werden:
>    * ***VpnGw1, VpnGw2, VpnGw3** _ (routenbasiert) _ ***Standard** _ und _*_HighPerformance_*_ (routenbasiert)
> 2. Pro Verbindung kann jeweils nur _*_eine_*_ Richtlinienkombination angegeben werden.
> 3. Sie müssen alle Algorithmen und Parameter für IKE (Hauptmodus) und IPsec (Schnellmodus) angeben. Partielle Richtlinien sind nicht zulässig.
> 4. Vergewissern Sie sich in den Spezifikationen Ihres VPN-Geräteanbieters, dass die Richtlinie von Ihren lokalen VPN-Geräten unterstützt wird. S2S- bzw. VNet-zu-VNet-Verbindungen können nicht hergestellt werden, wenn die Richtlinien inkompatibel sind.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Teil 1: Workflow zum Erstellen und Festlegen der IPsec/IKE-Richtlinie
In diesem Abschnitt wird der Workflow zum Erstellen und Aktualisieren der IPsec/IKE-Richtlinie für eine S2S-VPN- oder VNet-zu-VNet-Verbindung beschrieben:
1. Erstellen eines virtuelles Netzwerks und eines VPN-Gateways
2. Erstellen eines Gateways des lokalen Netzwerks für eine standortübergreifende Verbindung oder eines anderen virtuellen Netzwerks und Gateways für eine VNet-zu-VNet-Verbindung
3. Erstellen einer IPsec/IKE-Richtlinie mit ausgewählten Algorithmen und Parametern
4. Erstellen einer Verbindung (IPsec oder VNet2VNet) mit der IPsec/IKE-Richtlinie
5. Hinzufügen/Aktualisieren/Entfernen einer IPsec/IKE-Richtlinie für eine vorhandene Verbindung

Die Anleitung in diesem Artikel dient zum Einrichten und Konfigurieren von IPsec/IKE-Richtlinien, wie im folgenden Diagramm dargestellt:

![IPsec/IKE-Richtlinie](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Teil 2: Unterstützte Kryptografiealgorithmen und Schlüsselstärken

Die folgende Tabelle gibt Aufschluss über die unterstützten Kryptografiealgorithmen und Schlüsselstärken, die von den Kunden konfiguriert werden können:

| _ *IPsec/IKEv2**  | **Optionen**    |
| ---  | --- 
| IKEv2-Verschlüsselung | AES256, AES192, AES128, DES3, DES  
| IKEv2-Integrität  | SHA384, SHA256, SHA1, MD5  |
| DH-Gruppe         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, keine |
| IPsec-Verschlüsselung | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, keine    |
| IPsec-Integrität  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-Gruppe        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, keine 
| QM-SA-Gültigkeitsdauer   | (**Optional**: Wenn kein Wert angegeben wird, werden die Standardwerte verwendet)<br>Sekunden (ganze Zahl; **min. 300**/Standard: 27.000 Sekunden)<br>KB (ganze Zahl; **min. 1024**/Standard: 102.400.000 KB)   |
| Datenverkehrsselektor | UsePolicyBasedTrafficSelectors** ($True/$False; **Optional**, default $False if not specified)    |
|  |  |

> [!IMPORTANT]
> 1. **Ihre lokale VPN-Gerätekonfiguration muss folgenden Algorithmus- und Parameterangaben für die Azure-IPsec-/IKE-Richtlinie entsprechen oder selbige enthalten:**
>    * IKE-Verschlüsselungsalgorithmus (Hauptmodus/Phase 1)
>    * IKE-Integritätsalgorithmus (Hauptmodus/Phase 1)
>    * DH-Gruppe (Hauptmodus/Phase 1)
>    * IPsec-Verschlüsselungsalgorithmus (Schnellmodus/Phase 2)
>    * IPsec-Integritätsalgorithmus (Schnellmodus/Phase 2)
>    * PFS-Gruppe (Schnellmodus/Phase 2)
>    * Datenverkehrsselektor (bei Verwendung von „UsePolicyBasedTrafficSelectors“)
>    * Bei der SA-Gültigkeitsdauer handelt es sich lediglich um eine lokale Angabe. Diese muss nicht übereinstimmen.
>
> 2. **Wenn GCMAES als IPsec-Verschlüsselungsalgorithmus verwendet wird, müssen Sie für die IPsec-Integrität denselben GCMAES-Algorithmus und dieselbe Schlüssellänge auswählen (beispielsweise „GCMAES128“ für beides).**
> 3. In der obigen Tabelle:
>    * IKEv2 entspricht Hauptmodus oder Phase 1.
>    * IPsec entspricht Hauptmodus oder Phase 2.
>    * Die DH-Gruppe gibt die im Hauptmodus oder in Phase 1 verwendete Diffie-Hellmen-Gruppe an.
>    * Die PFS-Gruppe gibt die im Schnellmodus oder in Phase 2 verwendete Diffie-Hellmen-Gruppe an.
> 4. Die SA-Gültigkeitsdauer von IKEv2 (Hauptmodus) ist für die Azure-VPN-Gateways auf 28.800 Sekunden festgelegt.
> 5. Wenn „UsePolicyBasedTrafficSelectors“ für eine Verbindung auf „$True“ festgelegt wird, wird das Azure-VPN-Gateway zum Herstellen einer Verbindung mit einer richtlinienbasierten VPN-Firewall an einem lokalen Standort konfiguriert. Wenn Sie „PolicyBasedTrafficSelectors“ aktivieren, müssen für Ihr VPN-Gerät die entsprechenden Datenverkehrsselektoren mit allen Präfixkombinationen zwischen Ihrem lokalen Netzwerk (lokalen Netzwerkgateway) und den Präfixen des virtuellen Azure-Netzwerks definiert sein (anstelle von Any-to-Any). Wenn die Präfixe Ihres lokalen Netzwerks also beispielsweise 10.1.0.0/16 und 10.2.0.0/16 lauten und für Ihr virtuelles Netzwerk die Präfixe 192.168.0.0/16 und 172.16.0.0/16 verwendet werden, müssen folgende Datenverkehrsselektoren angegeben werden:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Weitere Informationen zu richtlinienbasierten Datenverkehrsselektoren finden Sie unter [Herstellen einer Verbindung zwischen Azure-VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten mit PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Die folgende Tabelle enthält die entsprechenden Diffie-Hellman-Gruppen, die von der benutzerdefinierten Richtlinie unterstützt werden:

| **Diffie-Hellman-Gruppe**  | **DHGroup**              | **PFSGroup** | **Schlüssellänge** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-Bit-MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-Bit-MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-Bit-MODP  |
| 19                        | ECP256                   | ECP256       | 256-Bit-ECP    |
| 20                        | ECP384                   | ECP384       | 384-Bit-ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-Bit-MODP  |

Ausführlichere Informationen finden Sie unter [RFC3526](https://tools.ietf.org/html/rfc3526) und [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Teil 3: Erstellen einer neuen S2S-VPN-Verbindung mit einer IPsec/IKE-Richtlinie

In diesem Abschnitt wird das Erstellen einer S2S-VPN-Verbindung mit einer IPsec/IKE-Richtlinie schrittweise beschrieben. Mit den folgenden Schritten wird die Verbindung erstellt, wie im Diagramm dargestellt:

![s2s-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Eine ausführlichere Schritt-für-Schritt-Anleitung zum Erstellen einer S2S-VPN-Verbindung finden Sie unter [Erstellen eines VNET mit einer Site-to-Site-VPN-Verbindung per PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md).

### <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

* Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Installieren Sie die Azure Resource Manager PowerShell-Cmdlets. Weitere Informationen zur Installation der PowerShell-Cmdlets finden Sie unter [Übersicht über Azure PowerShell](/powershell/azure/).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Schritt 1: Erstellen des virtuellen Netzwerks, VPN-Gateways und Gateways des lokalen Netzwerks

#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen

Bei dieser Übung beginnen wir mit dem Deklarieren der Variablen. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen, wenn Sie die Konfiguration für die Produktion durchführen.

```powershell
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Herstellen einer Verbindung mit Ihrem Abonnement und Erstellen einer neuen Ressourcengruppe

Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die Ressourcen-Manager-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Erstellen des virtuellen Netzwerks, VPN-Gateways und Gateways des lokalen Netzwerks

Im folgenden Beispiel werden das virtuelle Netzwerk „TestVNet1“ mit drei Subnetzen und das VPN-Gateway erstellt. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Schritt 2: Erstellen einer S2S-VPN-Verbindung mit einer IPsec/IKE-Richtlinie

#### <a name="1-create-an-ipsecike-policy"></a>1. Erstellen Sie eine IPsec/IKE-Richtlinie.

Im folgenden Beispielskript wird eine IPsec/IKE-Richtlinie mit den folgenden Algorithmen und Parametern erstellt:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, ohne PFS, SA-Gültigkeitsdauer 14400 Sekunden und 102.400.000 KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Wenn Sie GCMAES für IPsec verwenden, müssen für die IPsec-Verschlüsselung und -Integrität derselbe GCMAES-Algorithmus und dieselbe Schlüssellänge verwendet werden. Oben lauten die entsprechenden Parameter bei Verwendung von GCMAES256 beispielsweise „-IpsecEncryption GCMAES256“ und „-IpsecIntegrity GCMAES256“.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. Erstellen der S2S-VPN-Verbindung mit der IPSec/IKE-Richtlinie

Erstellen Sie eine S2S-VPN-Verbindung, und wenden Sie die zuvor erstellte IPsec/IKE-Richtlinie an.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Optional können Sie „-UsePolicyBasedTrafficSelectors $True“ dem Cmdlet für die Erstellung hinzufügen, um wie oben beschrieben für das Azure-VPN-Gateway das lokale Herstellen einer Verbindung mit richtlinienbasierten VPN-Geräten zu ermöglichen.

> [!IMPORTANT]
> Nachdem für eine Verbindung eine IPsec/IKE-Richtlinie angegeben wurde, sendet bzw. akzeptiert das Azure-VPN-Gateway den IPsec/IKE-Vorschlag mit angegebenen Kryptografiealgorithmen und Schlüsselstärken nur für die jeweilige Verbindung. Stellen Sie sicher, dass Ihr lokales VPN-Gerät für die Verbindung die exakte Richtlinienkombination nutzt bzw. akzeptiert, da der S2S-VPN-Tunnel andernfalls nicht hergestellt wird.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Teil 4: Erstellen einer neuen VNet-zu-VNet-Verbindung mit einer IPsec/IKE-Richtlinie

Die Schritte zum Erstellen einer VNet-zu-VNet-Verbindung mit einer IPsec/IKE-Richtlinie ähneln denen für eine S2S-VPN-Verbindung. Mit den folgenden Beispielskripts wird die Verbindung erstellt, wie im Diagramm zu sehen:

![v2v-policy](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Ausführlichere Informationen zum Erstellen einer VNet-zu-VNet-Verbindung finden Sie unter [Konfigurieren einer VNet-zu-VNet-VPN-Gatewayverbindung mithilfe von PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Sie müssen [Teil 3](#crossprem) ausführen, um TestVNet1 und das VPN-Gateway zu erstellen und zu konfigurieren.

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Schritt 1: Erstellen des zweiten virtuellen Netzwerks und VPN-Gateways

#### <a name="1-declare-your-variables"></a>1. Deklarieren von Variablen

Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Erstellen des zweiten virtuellen Netzwerks und VPN-Gateways in der neuen Ressourcengruppe

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Schritt 2: Erstellen einer VNet-zu-VNet-Verbindung mit der IPsec/IKE-Richtlinie

Erstellen Sie ähnlich wie bei der S2S-VPN-Verbindung eine IPsec/IKE-Richtlinie, und wenden Sie die Richtlinie dann auf die neue Verbindung an.

#### <a name="1-create-an-ipsecike-policy"></a>1. Erstellen Sie eine IPsec/IKE-Richtlinie.

Im folgenden Beispielskript wird eine andere IPsec/IKE-Richtlinie mit den folgenden Algorithmen und Parametern erstellt:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, SA-Gültigkeitsdauer 14.400 Sekunden und 102.400.000 KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. Erstellen von VNet-zu-VNet-Verbindungen mit der IPsec/IKE-Richtlinie

Erstellen Sie eine VNet-zu-VNet-Verbindung, und wenden Sie die von Ihnen erstellte IPsec/IKE-Richtlinie an. In diesem Beispiel befinden sich beide Gateways im selben Abonnement. Daher ist es möglich, beide Verbindungen mit derselben IPsec/IKE-Richtlinie in derselben PowerShell-Sitzung zu erstellen und zu konfigurieren.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Nachdem für eine Verbindung eine IPsec/IKE-Richtlinie angegeben wurde, sendet bzw. akzeptiert das Azure-VPN-Gateway den IPsec/IKE-Vorschlag mit angegebenen Kryptografiealgorithmen und Schlüsselstärken nur für die jeweilige Verbindung. Stellen Sie sicher, dass die IPsec-Richtlinien für beide Verbindungen identisch sind, da die VNet-zu-VNet-Verbindung ansonsten nicht hergestellt wird.

Nachdem Sie diese Schritte ausgeführt haben, wird die Verbindung innerhalb weniger Minuten hergestellt, und Sie verfügen über die gleiche Netzwerktopologie wie am Anfang des Artikels gezeigt:

![IPsec/IKE-Richtlinie](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Teil 5: Aktualisieren der IPsec/IKE-Richtlinie für eine Verbindung

Im diesem letzten Abschnitt erfahren Sie, wie Sie die IPsec/IKE-Richtlinie für eine vorhandene S2S- oder VNet-zu-VNet-Verbindung verwalten. In der Übung unten werden die folgenden Vorgänge einer Verbindung schrittweise beschrieben:

1. Anzeigen der IPsec/IKE-Richtlinie einer Verbindung
2. Hinzufügen oder Aktualisieren der IPsec/IKE-Richtlinie für eine Verbindung
3. Entfernen der IPsec/IKE-Richtlinie aus einer Verbindung

Für S2S- und VNet-zu-VNet-Verbindungen gelten die gleichen Schritte.

> [!IMPORTANT]
> Die IPsec-/IKE-Richtlinie wird nur für routenbasierte VPN-Gateways vom Typ *Standard* und *HighPerformance* unterstützt. Sie funktioniert nicht für die Gateway-SKU „Basic“ oder das richtlinienbasierte VPN-Gateway.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. Anzeigen der IPsec/IKE-Richtlinie einer Verbindung

Im folgenden Beispiel wird veranschaulicht, wie Sie die für eine Verbindung konfigurierte IPsec/IKE-Richtlinie abrufen. Die Skripts der obigen Übungen werden fortgesetzt.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Mit dem letzten Befehl wird die aktuelle IPsec/IKE-Richtlinie für die Verbindung konfiguriert (sofern vorhanden). Nachfolgend sehen Sie eine Beispielausgabe für die Verbindung:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Ist keine IPsec/IKE-Richtlinie konfiguriert, generiert der Befehl (PS> $connection6.IpsecPolicies) eine leere Rückgabe. Dies bedeutet nicht, dass IPsec/IKE nicht für die Verbindung konfiguriert wurde, sondern dass keine benutzerdefinierte IPsec/IKE-Richtlinie vorhanden ist. Für die eigentliche Verbindung wird die Standardrichtlinie verwendet, die zwischen Ihrem lokalen VPN-Gerät und dem Azure-VPN-Gateway ausgehandelt wurde.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Hinzufügen oder Aktualisieren einer IPsec/IKE-Richtlinie für eine Verbindung

Die Schritte zum Hinzufügen einer neuen Richtlinie oder Aktualisieren einer vorhandenen Richtlinie für eine Verbindung sind identisch: Erstellen Sie eine neue Richtlinie, und wenden Sie sie anschließend auf die Verbindung an.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Fügen Sie zum Aktivieren von „UsePolicyBasedTrafficSelectors“ beim Herstellen einer Verbindung mit einem lokalen richtlinienbasierten VPN-Gerät dem Cmdlet den Parameter „-UsePolicyBaseTrafficSelectors“ hinzu, oder legen Sie dafür „$False“ fest, um die Option zu deaktivieren:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

Sie können die Verbindung erneut abrufen, um zu überprüfen, ob die Richtlinie aktualisiert wird.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Die Ausgabe der letzten Zeile sollte wie im folgenden Beispiel aussehen:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. Entfernen einer IPsec/IKE-Richtlinie aus einer Verbindung

Wenn Sie die benutzerdefinierte Richtlinie für eine Verbindung entfernen, verwendet das Azure-VPN-Gateway wieder die [Standardliste mit IPsec-/IKE-Vorschlägen](vpn-gateway-about-vpn-devices.md) und beginnt eine neue Aushandlung mit Ihrem lokalen VPN-Gerät.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Mit dem gleichen Skript können Sie prüfen, ob die Richtlinie für die Verbindung entfernt wurde.

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zu richtlinienbasierten Datenverkehrsselektoren finden Sie unter [Herstellen einer Verbindung zwischen Azure-VPN-Gateways und mehreren lokalen richtlinienbasierten VPN-Geräten mit PowerShell](vpn-gateway-connect-multiple-policybased-rm-ps.md).

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Für diese Schritte finden Sie Informationen unter [Erstellen eines virtuellen Computers](../virtual-machines/windows/quick-create-portal.md) .