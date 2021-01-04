---
title: 'Verbinden eines VNets mit einem anderen VNet über eine Azure VPN Gateway-VNet-zu-VNet-Verbindung: PowerShell'
description: Stellen Sie mithilfe von PowerShell eine VNet-zu-VNet-Verbindung zwischen virtuellen Netzwerken her.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 7de83302dd91d7d679b9c35718d184a9767ba436
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655356"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-powershell"></a>Konfigurieren einer VNet-zu-VNet-VPN-Gatewayverbindung mithilfe von PowerShell

In diesem Artikel erfahren Sie, wie Sie zwischen virtuellen Netzwerken eine VNet-zu-VNet-Verbindung herstellen. Die virtuellen Netzwerke können sich in derselben oder in unterschiedlichen Regionen befinden und aus demselben oder unterschiedlichen Abonnements stammen. Beim Verbinden von VNets aus unterschiedlichen Abonnements müssen die Abonnements nicht demselben Active Directory-Mandanten zugeordnet sein.

Die Schritte in diesem Artikel gelten für das Resource Manager-Bereitstellungsmodell und für die Verwendung von PowerShell. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinden von verschiedenen Bereitstellungsmodellen – Azure-Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinden von virtuellen Netzwerken aus verschiedenen Bereitstellungsmodellen mit PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

## <a name="about-connecting-vnets"></a><a name="about"></a>Informationen zum Verbinden von VNets

VNets können auf unterschiedliche Weise verbunden werden. In den folgenden Abschnitten werden unterschiedliche Verbindungsmethoden für virtuelle Netzwerke beschrieben.

### <a name="vnet-to-vnet"></a>VNet-zu-VNet

Durch Konfigurieren einer VNet-zu-VNet-Verbindung können Sie sehr einfach eine Verbindung zwischen VNets herstellen. Eine VNet-zu-VNet-Verbindung (VNet2VNet) zwischen zwei virtuellen Netzwerken ist vergleichbar mit einer S2S-IPsec-Verbindung mit einem lokalen Standort.  Bei beiden Verbindungstypen wird ein VPN-Gateway verwendet, um per IPsec/IKE einen sicheren Tunnel zu erstellen, und auch die Kommunikation läuft jeweils gleich ab. Der Unterschied zwischen den Verbindungstypen liegt in der Konfiguration des Gateways für das lokale Netzwerk. Beim Erstellen einer VNet-zu-VNet-Verbindung wird der Adressraum des Gateways für das lokale Netzwerk nicht angezeigt. Dieser wird automatisch erstellt und mit Adressen gefüllt. Wenn Sie den Adressraum für ein VNet aktualisieren, leitet das andere VNet den Datenverkehr automatisch an den aktualisierten Adressraum weiter. Das Erstellen einer VNet-zu-VNet-Verbindung ist in der Regel schneller und einfacher als das Erstellen einer Site-to-Site-Verbindung zwischen VNets.

### <a name="site-to-site-ipsec"></a>Site-to-Site (IPsec)

Wenn Sie mit einer komplizierten Netzwerkkonfiguration arbeiten, empfiehlt es sich unter Umständen, die Verbindung zwischen VNets nicht mit den VNet-zu-VNet-Schritten, sondern mit den [Site-to-Site](vpn-gateway-create-site-to-site-rm-powershell.md)-Schritten herzustellen. Bei Verwendung der Site-to-Site-Schritte erstellen und konfigurieren Sie die lokalen Netzwerkgateways manuell. Das lokale Netzwerkgateway für die einzelnen VNets behandelt das andere VNet als lokalen Standort. Hierbei können Sie einen zusätzlichen Adressraum für das Gateway des lokalen Netzwerks angeben, um Datenverkehr weiterzuleiten. Wenn sich der Adressraum für ein VNet ändert, müssen Sie das dazugehörige lokale Netzwerkgateway entsprechend aktualisieren. Es wird nicht automatisch aktualisiert.

### <a name="vnet-peering"></a>VNet-Peering

Es empfiehlt sich unter Umständen, VNets per VNet-Peering zu verbinden. Beim VNet-Peering wird kein VPN-Gateway verwendet, und es gelten andere Einschränkungen. Außerdem werden die [Preise für VNet-Peering](https://azure.microsoft.com/pricing/details/virtual-network) anders berechnet als die [Preise für VPN Gateway (VNet-zu-VNet)](https://azure.microsoft.com/pricing/details/vpn-gateway). Weitere Informationen finden Sie unter [VNet-Peering](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Gründe für die Erstellung einer VNet-zu-VNet-Verbindung

Folgende Gründe sprechen für das Herstellen einer VNet-zu-VNet-Verbindung zwischen virtuellen Netzwerken:

* **Regionsübergreifende Georedundanz und Geopräsenz**

  * Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endpunkte mit Internetzugriff verwenden zu müssen.
  * Mit Azure Traffic Manager und Load Balancer können Sie eine hoch verfügbare Workload mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Ein wichtiges Beispiel ist die Einrichtung von SQL Always On mit Verfügbarkeitsgruppen, die sich über mehrere Azure-Regionen erstrecken.
* **Regionale Anwendungen mit mehreren Ebenen und Isolation oder Verwaltungsgrenze**

  * In derselben Region können Sie Anwendungen mit mehreren Ebenen und mehreren virtuellen Netzwerken einrichten, die aufgrund von Isolations- oder Verwaltungsanforderungen miteinander verbunden sind.

Die VNET-zu-VNET-Kommunikation kann mit Konfigurationen für mehrere Standorte kombiniert werden. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Welche VNet-zu-VNet-Schritte soll ich verwenden?

In diesem Artikel finden Sie zwei unterschiedliche Anleitungen: Schritte für [VNets, die sich unter demselben Abonnement befinden](#samesub), und Schritte für [VNets, die sich unter verschiedenen Abonnements befinden](#difsub).
Der Hauptunterschied zwischen den beiden Vorgehensweisen besteht darin, dass Sie separate PowerShell-Sitzungen verwenden müssen, wenn Sie die Verbindungen für VNets konfigurieren, die sich unter verschiedenen Abonnements befinden. 

Für diese Übung können Sie Konfigurationen kombinieren oder nur die gewünschte Konfiguration auswählen. Für alle Konfigurationen wird der Verbindungstyp „VNet-zu-VNet“ verwendet. Der Netzwerkdatenverkehr fließt zwischen den VNets, die direkt miteinander verbunden sind. In dieser Übung wird Datenverkehr von TestVNet4 nicht an TestVNet5 weitergeleitet.

* [VNETs im gleichen Abonnement](#samesub): Die Schritte für diese Konfiguration verwenden TestVNet1 und TestVNet4.

  ![Diagramm mit der Darstellung der VNet-zu-VNet-Schritte für VNets, die sich im gleichen Abonnement befinden](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

* [VNETs in unterschiedlichen Abonnements](#difsub): In den Schritten für diese Konfiguration werden TestVNet1 und TestVNet5 verwendet.

  ![v2v-Diagramm](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

## <a name="how-to-connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Verbinden von VNets in demselben Abonnement

### <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Da es bis zu 45 Minuten dauert, ein Gateway zu erstellen, kommt es während dieser Übung regelmäßig zu Timeouts bei Azure Cloud Shell. Sie können Cloud Shell neu starten, indem Sie auf die obere linke Ecke des Terminals klicken. Stellen Sie sicher, dass Sie alle Variablen erneut deklarieren, wenn Sie das Terminal neu starten.

* Wenn Sie lieber die neueste Version des Azure PowerShell-Moduls lokal installieren möchten, und sie Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

### <a name="step-1---plan-your-ip-address-ranges"></a><a name="Step1"></a>Schritt 1: Planen der IP-Adressbereiche

In den folgenden Schritten erstellen Sie zwei virtuelle Netzwerke sowie die jeweiligen Gatewaysubnetze und Konfigurationen. Anschließend erstellen Sie eine VPN-Verbindung zwischen den beiden VNets. Es ist wichtig, die IP-Adressbereiche für Ihre Netzwerkkonfiguration zu planen. Denken Sie daran, dass Sie sicherstellen müssen, dass keiner der VNet-Bereiche oder der Bereiche des lokalen Netzwerks Überschneidungen aufweist. In diesen Beispielen verwenden wir keinen Namenserver. Wenn Sie eine Namensauflösung für die virtuellen Netzwerke möchten, finden Sie unter [Namensauflösung](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Informationen dazu.

In den Beispielen werden die folgenden Werte verwendet:

**Werte für TestVNet1:**

* VNET-Name: TestVNet1
* Ressourcengruppe: TestRG1
* Standort: East US
* TestVNet1: 10.11.0.0/16 und 10.12.0.0/16
* Front-End: 10.11.0.0/24
* Back-End: 10.12.0.0/24
* Gatewaysubnetz: 10.12.255.0/27
* Gatewayname: VNet1GW
* Öffentliche IP-Adresse: VNet1GWIP
* VPN-Typ: RouteBased
* Verbindung (1 bis 4): VNet1toVNet4
* Verbindung (1 bis 5): VNet1toVNet5 (für VNETs in verschiedenen Abonnements)
* Verbindungstyp: VNet2VNet

**Werte für TestVNet4:**

* VNET-Name: TestVNet4
* TestVNet2: 10.41.0.0/16 und 10.42.0.0/16
* Front-End: 10.41.0.0/24
* Back-End: 10.42.0.0/24
* Gatewaysubnetz: 10.42.255.0/27
* Ressourcengruppe: TestRG4
* Standort: USA (Westen)
* Gatewayname: VNet4GW
* Öffentliche IP-Adresse: VNet4GWIP
* VPN-Typ: RouteBased
* Verbindung: VNet4toVNet1
* Verbindungstyp: VNet2VNet


### <a name="step-2---create-and-configure-testvnet1"></a><a name="Step2"></a>Schritt 2: Erstellen und Konfigurieren von TestVNet1

1. Überprüfen Sie Ihre Abonnementeinstellungen.

   Stellen Sie eine Verbindung mit Ihrem Konto her, wenn Sie PowerShell lokal auf Ihrem Computer ausführen. Wenn Sie Azure Cloud Shell verwenden, werden Sie automatisch verbunden.

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Überprüfen Sie die Abonnements für das Konto.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Wenn Sie über mehrere Abonnements verfügen, geben Sie das Abonnement an, das Sie verwenden möchten.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName nameofsubscription
   ```
2. Deklarieren Sie Ihre Variablen. Im Beispiel werden die Variablen mit den Werten für diese Übung deklariert. In den meisten Fällen sollten Sie die Werte durch Ihre eigenen Werte ersetzen. Sie können diese Variablen jedoch verwenden, wenn Sie die Schritte nur durchgehen, um sich mit dieser Art von Konfiguration vertraut zu machen. Ändern Sie die Variablen ggf., kopieren Sie sie, und fügen Sie sie in die PowerShell-Konsole ein.

   ```azurepowershell-interactive
   $RG1 = "TestRG1"
   $Location1 = "East US"
   $VNetName1 = "TestVNet1"
   $FESubName1 = "FrontEnd"
   $BESubName1 = "Backend"
   $VNetPrefix11 = "10.11.0.0/16"
   $VNetPrefix12 = "10.12.0.0/16"
   $FESubPrefix1 = "10.11.0.0/24"
   $BESubPrefix1 = "10.12.0.0/24"
   $GWSubPrefix1 = "10.12.255.0/27"
   $GWName1 = "VNet1GW"
   $GWIPName1 = "VNet1GWIP"
   $GWIPconfName1 = "gwipconf1"
   $Connection14 = "VNet1toVNet4"
   $Connection15 = "VNet1toVNet5"
   ```
3. Erstellen Sie eine Ressourcengruppe.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
4. Erstellen Sie die Subnetzkonfigurationen für „TestVNet1“. In diesem Beispiel wird ein virtuelles Netzwerk mit dem Namen TestVNet1 und drei Subnetzen namens „GatewaySubnet“, „FrontEnd“ und „Backend“ erstellt. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf. Aus diesem Grund wird der Name nicht mithilfe der folgenden Variable zugewiesen.

   Im folgenden Beispiel werden die zuvor festgelegten Variablen verwendet. Im Beispiel wird ein Gatewaysubnetz mit einem Subnetz der Größe /27 verwendet. Es ist zwar möglich, ein Gatewaysubnetz von /29 zu erstellen, es wird jedoch empfohlen, ein größeres Subnetz mit mehr Adressen zu erstellen und mindestens /28 oder /27 auszuwählen. Dadurch steht eine ausreichend hohe Anzahl von Adressen für mögliche zusätzliche Konfigurationen zur Verfügung, die Sie zukünftig vielleicht benötigen.

   ```azurepowershell-interactive
   $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
   $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
   $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix1
   ```
5. Erstellen Sie „TestVNet1“.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
   ```
6. Fordern Sie eine öffentliche IP-Adresse an, die dem Gateway zugewiesen wird, das Sie für Ihr VNet erstellen. Beachten Sie, dass die Zuordnungsmethode (AllocationMethod) dynamisch ist. Es ist nicht möglich, die IP-Adresse selbst anzugeben. Sie wird Ihrem Gateway dynamisch zugewiesen. 

   ```azurepowershell-interactive
   $gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
   -Location $Location1 -AllocationMethod Dynamic
   ```
7. Erstellen Sie die Gatewaykonfiguration. Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das Beispiel, um Ihre Gatewaykonfiguration zu erstellen.

   ```azurepowershell-interactive
   $vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
   $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
   -Subnet $subnet1 -PublicIpAddress $gwpip1
   ```
8. Erstellen Sie das Gateway für „TestVNet1“. In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für TestVNet1. VNet-zu-VNet-Konfigurationen erfordern einen routenbasierten VPN-Typ. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
   -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

Nachdem Sie die Befehle fertig gestellt haben, dauert es bis zu 45 Minuten, um dieses Gateway zu erstellen. Wenn Sie Azure Cloud Shell verwenden, können Sie Ihre Cloud Shell-Sitzung neu starten, indem Sie auf die linke obere Ecke des Cloud Shell-Terminals klicken und dann „TestVNet4“ konfigurieren. Sie müssen nicht warten, bis das Gateway „TestVNet1“ fertig ist.

### <a name="step-3---create-and-configure-testvnet4"></a>Schritt 3: Erstellen und Konfigurieren von TestVNet4

Nachdem Sie TestVNet1 konfiguriert haben, erstellen Sie TestVNet4. Führen Sie die unten angegebenen Schritte aus, und ersetzen Sie die Werte bei Bedarf durch Ihre eigenen Werte.

1. Verbinden Sie, und deklarieren Sie Ihre Variablen. Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

   ```azurepowershell-interactive
   $RG4 = "TestRG4"
   $Location4 = "West US"
   $VnetName4 = "TestVNet4"
   $FESubName4 = "FrontEnd"
   $BESubName4 = "Backend"
   $VnetPrefix41 = "10.41.0.0/16"
   $VnetPrefix42 = "10.42.0.0/16"
   $FESubPrefix4 = "10.41.0.0/24"
   $BESubPrefix4 = "10.42.0.0/24"
   $GWSubPrefix4 = "10.42.255.0/27"
   $GWName4 = "VNet4GW"
   $GWIPName4 = "VNet4GWIP"
   $GWIPconfName4 = "gwipconf4"
   $Connection41 = "VNet4toVNet1"
   ```
2. Erstellen Sie eine Ressourcengruppe.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG4 -Location $Location4
   ```
3. Erstellen Sie die Subnetzkonfigurationen für „TestVNet4“.

   ```azurepowershell-interactive
   $fesub4 = New-AzVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
   $besub4 = New-AzVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
   $gwsub4 = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $GWSubPrefix4
   ```
4. Erstellen Sie „TestVNet4“.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4
   ```
5. Fordern Sie eine öffentliche IP-Adresse an.

   ```azurepowershell-interactive
   $gwpip4 = New-AzPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
   -Location $Location4 -AllocationMethod Dynamic
   ```
6. Erstellen Sie die Gatewaykonfiguration.

   ```azurepowershell-interactive
   $vnet4 = Get-AzVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
   $subnet4 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
   $gwipconf4 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4
   ```
7. Erstellen Sie das Gateway „TestVNet4“. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
   -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
   -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-4---create-the-connections"></a>Schritt 4: Erstellen der Verbindungen

Warten Sie, bis beide Gateways fertig sind. Starten Sie Ihre Azure Cloud Shell-Sitzung neu, kopieren Sie die Variablen vom Anfang in Schritt2 und Schritt 3, und fügen Sie sie in die Konsole ein, um Werte erneut zu deklarieren.

1. Rufen Sie die beiden virtuellen Netzwerkgateways ab.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   $vnet4gw = Get-AzVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4
   ```
2. Erstellen Sie die Verbindung zwischen TestVNet1 und TestVNet4. In diesem Schritt erstellen Sie die Verbindung von TestVNet1 mit TestVNet4. In den Beispielen wird auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können eigene Werte für den gemeinsam verwendeten Schlüssel verwenden. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
   -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
3. Erstellen Sie die Verbindung zwischen TestVNet4 und TestVNet1. Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von TestVNet4 mit TestVNet1. Stellen Sie sicher, dass die freigegebenen Schlüssel übereinstimmen. Nach einigen Minuten wird die Verbindung hergestellt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
   -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
   -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. Überprüfen Sie die Verbindung. Informationen hierzu finden Sie im Abschnitt [Überprüfen der Verbindung](#verify).

## <a name="how-to-connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Verbinden von VNets aus unterschiedlichen Abonnements

In diesem Szenario verbinden Sie TestVNet1 und TestVNet5. TestVNet1 und TestVNet5 befinden sich in unterschiedlichen Abonnements. Die Abonnements müssen nicht demselben Active Directory-Mandanten zugeordnet werden.

Der Unterschied zwischen diesen und den vorherigen Schritten besteht darin, dass ein Teil der Konfigurationsschritte in einer separaten PowerShell-Sitzung im Kontext des zweiten Abonnements ausgeführt werden muss. Dies gilt insbesondere dann, wenn die beiden Abonnements unterschiedlichen Organisationen gehören.

Wegen des sich ändernden Abonnementkontexts in dieser Übung könnte es einfacher für Sie sein, PowerShell lokal auf Ihrem Computer zu verwenden, statt Azure Cloud Shell zu verwenden, wenn Sie zu Schritt 8 gelangen.

### <a name="step-5---create-and-configure-testvnet1"></a>Schritt 5: Erstellen und Konfigurieren von TestVNet1

Sie müssen [Schritt 1](#Step1) und [Schritt 2](#Step2) aus dem vorherigen Abschnitt ausführen, um „TestVNet1“ und das VPN Gateway für „TestVNet1“ zu erstellen und zu konfigurieren. Für diese Konfiguration ist es nicht erforderlich „TestVNet4“ aus dem vorherigen Abschnitt zu erstellen. Wenn Sie es dennoch erstellen, entstehen jedoch keine Konflikte mit den folgenden Schritten. Nachdem Sie die Schritte 1 und 2 ausgeführt haben, fahren Sie mit Schritt 6 fort, um TestVNet5 zu erstellen.

### <a name="step-6---verify-the-ip-address-ranges"></a>Schritt 6: Überprüfen der IP-Adressbereiche

Es ist wichtig sicherzustellen, dass sich der IP-Adressbereich des neuen virtuellen Netzwerks (TestVNet5) nicht mit einem Ihrer VNet-Bereiche oder Gatewaybereiche des lokalen Netzwerks überlappt. In diesem Beispiel können die virtuellen Netzwerke unterschiedlichen Organisationen gehören. Bei dieser Übung können Sie für TestVNet5 die folgenden Werte verwenden:

**Werte für TestVNet5:**

* VNET-Name: TestVNet5
* Ressourcengruppe: TestRG5
* Standort: Japan, Osten
* TestVNet5: 10.51.0.0/16 und 10.52.0.0/16
* Front-End: 10.51.0.0/24
* Back-End: 10.52.0.0/24
* Gatewaysubnetz: 10.52.255.0.0/27
* Gatewayname: VNet5GW
* Öffentliche IP-Adresse: VNet5GWIP
* VPN-Typ: RouteBased
* Verbindung: VNet5toVNet1
* Verbindungstyp: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a>Schritt 7: Erstellen und Konfigurieren von „TestVNet5“

Dieser Schritt muss im Kontext des neuen Abonnements ausgeführt werden. Dieser Teil kann vom Administrator in einer anderen Organisation ausgeführt werden, in deren Besitz sich das Abonnement befindet.

1. Deklarieren Sie Ihre Variablen. Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

   ```azurepowershell-interactive
   $Sub5 = "Replace_With_the_New_Subscription_Name"
   $RG5 = "TestRG5"
   $Location5 = "Japan East"
   $VnetName5 = "TestVNet5"
   $FESubName5 = "FrontEnd"
   $BESubName5 = "Backend"
   $GWSubName5 = "GatewaySubnet"
   $VnetPrefix51 = "10.51.0.0/16"
   $VnetPrefix52 = "10.52.0.0/16"
   $FESubPrefix5 = "10.51.0.0/24"
   $BESubPrefix5 = "10.52.0.0/24"
   $GWSubPrefix5 = "10.52.255.0/27"
   $GWName5 = "VNet5GW"
   $GWIPName5 = "VNet5GWIP"
   $GWIPconfName5 = "gwipconf5"
   $Connection51 = "VNet5toVNet1"
   ```
2. Stellen Sie eine Verbindung mit Abonnement 5 her. Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```

   Überprüfen Sie die Abonnements für das Konto.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

   Geben Sie das Abonnement an, das Sie verwenden möchten.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName $Sub5
   ```
3. Erstellen Sie eine neue Ressourcengruppe.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG5 -Location $Location5
   ```
4. Erstellen Sie die Subnetzkonfigurationen für „TestVNet5“.

   ```azurepowershell-interactive
   $fesub5 = New-AzVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
   $besub5 = New-AzVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
   $gwsub5 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5
   ```
5. Erstellen Sie „TestVNet5“.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
   -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5
   ```
6. Fordern Sie eine öffentliche IP-Adresse an.

   ```azurepowershell-interactive
   $gwpip5 = New-AzPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
   -Location $Location5 -AllocationMethod Dynamic
   ```
7. Erstellen Sie die Gatewaykonfiguration.

   ```azurepowershell-interactive
   $vnet5 = Get-AzVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
   $subnet5  = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
   $gwipconf5 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5
   ```
8. Erstellen Sie das TestVNet5-Gateway.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
   -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1
   ```

### <a name="step-8---create-the-connections"></a>Schritt 8: Erstellen der Verbindungen

Da sich die Gateways in diesem Beispiel in unterschiedlichen Abonnements befinden, haben wir diesen Schritt in zwei PowerShell-Sitzungen mit den Bezeichnungen [Abonnement 1] und [Abonnement 5] aufgeteilt.

1. **[Abonnement 1]** Rufen Sie das virtuelle Netzwerkgateway für Abonnement 1 ab. Melden Sie sich an, und stellen Sie eine Verbindung mit Abonnement 1 her, bevor Sie das folgende Beispiel ausführen:

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
   ```

   Kopieren Sie die Ausgabe der folgenden Elemente, und senden Sie diese per E-Mail oder auf anderem Wege an den Administrator von Abonnement 5.

   ```azurepowershell-interactive
   $vnet1gw.Name
   $vnet1gw.Id
   ```

   Diese beiden Elemente weisen Werte auf, die den Werten in der folgenden Beispielausgabe ähneln:

   ```
   PS D:\> $vnet1gw.Name
   VNet1GW
   PS D:\> $vnet1gw.Id
   /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```
2. **[Abonnement 5]** Rufen Sie das virtuelle Netzwerkgateway für Abonnement 5 ab. Melden Sie sich an, und stellen Sie eine Verbindung mit Abonnement 5 her, bevor Sie das folgende Beispiel ausführen:

   ```azurepowershell-interactive
   $vnet5gw = Get-AzVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5
   ```

   Kopieren Sie die Ausgabe der folgenden Elemente, und senden Sie diese per E-Mail oder auf anderem Wege an den Administrator von Abonnement 1.

   ```azurepowershell-interactive
   $vnet5gw.Name
   $vnet5gw.Id
   ```

   Diese beiden Elemente weisen Werte auf, die den Werten in der folgenden Beispielausgabe ähneln:

   ```
   PS C:\> $vnet5gw.Name
   VNet5GW
   PS C:\> $vnet5gw.Id
   /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```
3. **[Abonnement 1]** Erstellen Sie die Verbindung zwischen „TestVNet1“ und „TestVNet5“. In diesem Schritt erstellen Sie die Verbindung von TestVNet1 mit TestVNet5. Der Unterschied hierbei ist, dass „$vnet5gw“ nicht direkt abgerufen werden kann, da es sich in einem anderen Abonnement befindet. Sie müssen ein neues PowerShell-Objekt mit den Werten erstellen, die in den obigen Schritten aus Abonnement 1 übermittelt wurden. Sehen Sie sich das Beispiel unten an. Ersetzen Sie Name, ID und gemeinsam verwendete Schlüssel durch Ihre eigenen Werte. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

   Stellen Sie eine Verbindung mit Abonnement 1 her, bevor Sie das folgende Beispiel ausführen:

   ```azurepowershell-interactive
   $vnet5gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet5gw.Name = "VNet5GW"
   $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
   $Connection15 = "VNet1toVNet5"
   New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```
4. **[Abonnement 5]** Erstellen Sie die Verbindung zwischen „TestVNet5“ und „TestVNet1“. Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von TestVNet5 mit TestVNet1. Auch hier gilt der gleiche Prozess zur Erstellung eines PowerShell-Objekts basierend auf den Werten, die aus Abonnement 1 abgerufen wurden. Stellen Sie in diesem Schritt sicher, dass die freigegebenen Schlüssel übereinstimmen.

   Stellen Sie eine Verbindung mit Abonnement 5 her, bevor Sie das folgende Beispiel ausführen:

   ```azurepowershell-interactive
   $vnet1gw = New-Object -TypeName Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
   $vnet1gw.Name = "VNet1GW"
   $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
   $Connection51 = "VNet5toVNet1"
   New-AzVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
   ```

## <a name="how-to-verify-a-connection"></a><a name="verify"></a>Überprüfen einer Verbindung

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Häufig gestellte Fragen zu VNet-zu-VNet-Verbindungen

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Dokumentation zu Virtual Machines](../index.yml).
* Informationen zu BGP finden Sie in der [Übersicht über BGP](vpn-gateway-bgp-overview.md) und unter [Konfigurieren von BGP](vpn-gateway-bgp-resource-manager-ps.md).