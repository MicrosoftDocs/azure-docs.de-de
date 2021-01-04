---
title: 'Verbinden eines VNets mit einem VNet mit einer VNet-zu-VNet-Verbindung: Azure-Befehlszeilenschnittstelle'
description: Stellen Sie unter Verwendung der Azure-Befehlszeilenschnittstelle eine VNet-zu-VNet-Verbindung zwischen virtuellen Netzwerken her.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9502f3fbd50aad756e15daa4db1badda2abf9ab
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660065"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung mithilfe der Azure CLI

In diesem Artikel erfahren Sie, wie Sie zwischen virtuellen Netzwerken eine VNet-zu-VNet-Verbindung herstellen. Die virtuellen Netzwerke können sich in derselben oder in unterschiedlichen Regionen befinden und aus demselben oder unterschiedlichen Abonnements stammen. Beim Verbinden von VNets aus unterschiedlichen Abonnements müssen die Abonnements nicht demselben Active Directory-Mandanten zugeordnet sein.

Die Schritte in diesem Artikel gelten für das Resource Manager-Bereitstellungsmodell und die Azure-Befehlszeilenschnittstelle. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-Befehlszeilenschnittstelle](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinden von verschiedenen Bereitstellungsmodellen – Azure-Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinden von virtuellen Netzwerken aus verschiedenen Bereitstellungsmodellen mit PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a><a name="about"></a>Informationen zum Verbinden von VNets

VNets können auf unterschiedliche Weise verbunden werden. In den folgenden Abschnitten werden unterschiedliche Verbindungsmethoden für virtuelle Netzwerke beschrieben.

### <a name="vnet-to-vnet"></a>VNet-zu-VNet

Durch Konfigurieren einer VNet-zu-VNet-Verbindung können Sie sehr einfach eine Verbindung zwischen VNets herstellen. Eine VNet-zu-VNet-Verbindung zwischen zwei virtuellen Netzwerken ist vergleichbar mit einer S2S-IPsec-Verbindung mit einem lokalen Standort. Bei beiden Verbindungstypen wird ein VPN-Gateway verwendet, um per IPsec/IKE einen sicheren Tunnel zu erstellen, und auch die Kommunikation läuft jeweils gleich ab. Der Unterschied zwischen den Verbindungstypen liegt in der Konfiguration des Gateways für das lokale Netzwerk. Beim Erstellen einer VNet-zu-VNet-Verbindung wird der Adressraum des Gateways für das lokale Netzwerk nicht angezeigt. Dieser wird automatisch erstellt und mit Adressen gefüllt. Wenn Sie den Adressraum für ein VNet aktualisieren, leitet das andere VNet den Datenverkehr automatisch an den aktualisierten Adressraum weiter. Das Erstellen einer VNet-zu-VNet-Verbindung ist in der Regel schneller und einfacher als das Erstellen einer Site-to-Site-Verbindung zwischen VNets.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Herstellen einer Verbindung zwischen VNets mithilfe von Site-to-Site-Schritten (IPsec)

Wenn Sie mit einer komplizierten Netzwerkkonfiguration arbeiten, empfiehlt es sich unter Umständen, die Verbindung zwischen VNets nicht mit den VNet-zu-VNet-Schritten, sondern mit den [Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-cli.md)-Schritten herzustellen. Bei Verwendung der Site-to-Site-Schritte erstellen und konfigurieren Sie die lokalen Netzwerkgateways manuell. Das lokale Netzwerkgateway für die einzelnen VNets behandelt das andere VNet als lokalen Standort. Hierbei können Sie einen zusätzlichen Adressraum für das Gateway des lokalen Netzwerks angeben, um Datenverkehr weiterzuleiten. Wenn sich der Adressraum für ein VNet ändert, müssen Sie das entsprechende lokale Netzwerkgateway manuell aktualisieren. Es wird nicht automatisch aktualisiert.

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

Dieser Artikel enthält zwei unterschiedliche Anleitungen für VNet-zu-VNet-Verbindungen: Schritte für [VNets, die sich unter demselben Abonnement befinden](#samesub), und Schritte für [VNets, die sich unter verschiedenen Abonnements befinden](#difsub). 

Für diese Übung können Sie Konfigurationen kombinieren oder nur die gewünschte Konfiguration auswählen. Für alle Konfigurationen wird der Verbindungstyp „VNet-zu-VNet“ verwendet. Der Netzwerkdatenverkehr fließt zwischen den VNets, die direkt miteinander verbunden sind. In dieser Übung wird Datenverkehr von TestVNet4 nicht an TestVNet5 weitergeleitet.

* [VNETs im gleichen Abonnement:](#samesub) Die Schritte für diese Konfiguration verwenden TestVNet1 und TestVNet4.

  ![Diagramm mit der Darstellung der VNet-zu-VNet-Schritte für VNets, die sich im gleichen Abonnement befinden.](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [VNETs in unterschiedlichen Abonnements:](#difsub) In den Schritten für diese Konfiguration werden TestVNet1 und TestVNet5 verwendet.

  ![v2v-Diagramm](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>Verbinden von VNETs im selben Abonnement

### <a name="before-you-begin"></a>Voraussetzungen

Installieren Sie als Vorbereitung die aktuelle Version der CLI-Befehle (2.0 oder höher). Informationen zur Installation der CLI-Befehle finden Sie unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

### <a name="plan-your-ip-address-ranges"></a><a name="Plan"></a>Planen der IP-Adressbereiche

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

### <a name="step-1---connect-to-your-subscription"></a><a name="Connect"></a>Schritt 1: Herstellen einer Verbindung mit Ihrem Abonnement

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="step-2---create-and-configure-testvnet1"></a><a name="TestVNet1"></a>Schritt 2: Erstellen und Konfigurieren von TestVNet1

1. Erstellen Sie eine Ressourcengruppe.

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. Erstellen Sie „TestVNet1“ und die Subnetze für „TestVNet1“. In diesem Beispiel werden ein virtuelles Netzwerk mit dem Namen „TestVNet1“ und ein Subnetz mit dem Namen „FrontEnd“ erstellt.

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. Erstellen Sie einen zusätzlichen Adressraum für das Back-End-Subnetz. Hinweis: In diesem Schritt geben wir neben dem Adressraum, den wir zuvor erstellt haben, auch den zusätzlichen Adressraum an, der hinzugefügt werden soll. Dies ist erforderlich, weil der Befehl [az network vnet update](/cli/azure/network/vnet) die vorherigen Einstellungen überschreibt. Achten Sie darauf, bei Verwendung dieses Befehls alle Adresspräfixe anzugeben.

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. Erstellen Sie das Back-End-Subnetz.
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. Erstellen Sie das Gatewaysubnetz. Beachten Sie, dass das Gatewaysubnetz den Namen „GatewaySubnet“ hat. Dieser Name ist erforderlich. Im Beispiel wird ein Gatewaysubnetz mit einem Subnetz der Größe /27 verwendet. Es ist zwar möglich, ein Gatewaysubnetz von /29 zu erstellen, es wird jedoch empfohlen, ein größeres Subnetz mit mehr Adressen zu erstellen und mindestens /28 oder /27 auszuwählen. Dadurch steht eine ausreichend hohe Anzahl von Adressen für mögliche zusätzliche Konfigurationen zur Verfügung, die Sie zukünftig vielleicht benötigen.

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. Fordern Sie eine öffentliche IP-Adresse an, die dem Gateway zugewiesen wird, das Sie für Ihr VNet erstellen. Beachten Sie, dass die Zuordnungsmethode (AllocationMethod) dynamisch ist. Es ist nicht möglich, die IP-Adresse selbst anzugeben. Sie wird Ihrem Gateway dynamisch zugewiesen.

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. Erstellen Sie das virtuelle Netzwerkgateway für Ihr TestVNet1. VNet-zu-VNet-Konfigurationen erfordern einen routenbasierten VPN-Typ. Wenn Sie diesen Befehl mit dem Parameter „--no-wait“ ausführen, wird kein Feedback bzw. keine Ausgabe angezeigt. Der Parameter „--no-wait“ ermöglicht die Erstellung des Gateways im Hintergrund. Das bedeutet nicht, dass die Erstellung des VPN Gateways sofort abgeschlossen ist. Das Erstellen eines Gateways kann je nach verwendeter Gateway-SKU häufig mindestens 45 Minuten dauern.

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-3---create-and-configure-testvnet4"></a><a name="TestVNet4"></a>Schritt 3: Erstellen und Konfigurieren von „TestVNet4“

1. Erstellen Sie eine Ressourcengruppe.

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. Erstellen Sie „TestVNet4“.

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. Erstellen Sie zusätzliche Subnetze für „TestVNet4“.

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. Erstellen Sie das Gatewaysubnetz.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. Fordern Sie eine öffentliche IP-Adresse an.

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. Erstellen Sie das virtuelle Netzwerkgateway für „TestVNet4“.

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-4---create-the-connections"></a><a name="createconnect"></a>Schritt 4: Erstellen der Verbindungen

Sie verfügen jetzt über zwei VNETs mit VPN Gateways. Im nächsten Schritt werden VPN Gatewayverbindungen zwischen den Gateways des virtuellen Netzwerks erstellt. Wenn Sie die Beispiele oben verwendet haben, befinden sich Ihre VNET-Gateways in verschiedenen Ressourcengruppen. Wenn Gateways sich in verschiedenen Ressourcengruppen befinden, müssen Sie beim Herstellen einer Verbindung die Ressourcen-IDs der einzelnen Gateways identifizieren und angeben. Wenn sich Ihre VNETs in derselben Ressourcengruppe befinden, können Sie den [zweiten Satz von Anweisungen](#samerg) verwenden, da Sie die Ressourcen-IDs nicht angeben müssen.

### <a name="to-connect-vnets-that-reside-in-different-resource-groups"></a><a name="diffrg"></a>So verbinden Sie VNETs, die sich in verschiedenen Ressourcengruppen befinden

1. Rufen Sie die Ressourcen-ID von „VNet1GW“ aus der Ausgabe des folgenden Befehls ab:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Suchen Sie in der Ausgabe nach der Zeile "id:". Die Werte innerhalb der Anführungszeichen sind erforderlich, um im nächsten Abschnitt die Verbindung zu erstellen. Kopieren Sie diese Werte in einen Text-Editor wie Windows-Editor, damit Sie sie beim Erstellen der Verbindung problemlos einfügen können.

   Beispielausgabe:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   Kopieren Sie die Werte nach **"id":** innerhalb der Anführungszeichen.

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. Rufen Sie die Ressourcen-ID von „VNet4GW“ ab, und kopieren Sie die Werte in einen Text-Editor.

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. Erstellen Sie die Verbindung zwischen TestVNet1 und TestVNet4. In diesem Schritt erstellen Sie die Verbindung von TestVNet1 mit TestVNet4. In den Beispielen wird auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können eigene Werte für den gemeinsam verwendeten Schlüssel verwenden. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. Erstellen Sie die Verbindung zwischen TestVNet4 und TestVNet1. Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von TestVNet4 mit TestVNet1. Stellen Sie sicher, dass die freigegebenen Schlüssel übereinstimmen. Das Herstellen der Verbindung dauert einige Minuten.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. Überprüfen Sie Ihre Verbindungen. Siehe [Überprüfen der Verbindung](#verify).

### <a name="to-connect-vnets-that-reside-in-the-same-resource-group"></a><a name="samerg"></a>So verbinden Sie VNETs, die sich in derselben Ressourcengruppe befinden

1. Erstellen Sie die Verbindung zwischen TestVNet1 und TestVNet4. In diesem Schritt erstellen Sie die Verbindung von TestVNet1 mit TestVNet4. Beachten Sie, dass die Ressourcengruppen in den Beispielen identisch sind. In den Beispielen wird auch auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können Ihre eigenen Werte für den gemeinsam verwendeten Schlüssel nutzen, der gemeinsam verwendete Schlüssel muss jedoch für beide Verbindungen übereinstimmen. Das Erstellen einer Verbindung kann etwas dauern.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. Erstellen Sie die Verbindung zwischen TestVNet4 und TestVNet1. Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von TestVNet4 mit TestVNet1. Stellen Sie sicher, dass die freigegebenen Schlüssel übereinstimmen. Das Herstellen der Verbindung dauert einige Minuten.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. Überprüfen Sie Ihre Verbindungen. Siehe [Überprüfen der Verbindung](#verify).

## <a name="connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>Verbinden von VNETs aus unterschiedlichen Abonnements

In diesem Szenario verbinden Sie TestVNet1 und TestVNet5. Die VNETs befinden sich verschiedenen Abonnements. Die Abonnements müssen nicht demselben Active Directory-Mandanten zugeordnet werden. In den Schritten für diese Konfiguration wird eine zusätzliche VNet-zu-VNet-Verbindung hinzugefügt, um TestVNet1 mit TestVNet5 zu verbinden.

### <a name="step-5---create-and-configure-testvnet1"></a><a name="TestVNet1diff"></a>Schritt 5: Erstellen und Konfigurieren von „TestVNet1“

Diese Anweisungen sind die Fortsetzung der Schritte in den vorangehenden Abschnitten. Sie müssen [Schritt 1](#Connect) und [Schritt 2](#TestVNet1) ausführen, um „TestVNet1“ und das VPN Gateway für „TestVNet1“ zu erstellen und zu konfigurieren. Für diese Konfiguration ist es nicht erforderlich „TestVNet4“ aus dem vorherigen Abschnitt zu erstellen. Wenn Sie es dennoch erstellen, entstehen jedoch keine Konflikte mit den folgenden Schritten. Nachdem Sie die Schritte 1 und 2 ausgeführt haben, fahren Sie mit Schritt 6 (unten) fort.

### <a name="step-6---verify-the-ip-address-ranges"></a><a name="verifyranges"></a>Schritt 6: Überprüfen der IP-Adressbereiche

Beim Erstellten zusätzlicher Verbindungen ist es wichtig sicherzustellen, dass sich der IP-Adressbereich des neuen virtuellen Netzwerks nicht mit einem Ihrer anderen VNET-Bereiche oder Gatewaybereiche des lokalen Netzwerks überlappt. Bei dieser Übung können Sie für TestVNet5 die folgenden Werte verwenden:

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

### <a name="step-7---create-and-configure-testvnet5"></a><a name="TestVNet5"></a>Schritt 7: Erstellen und Konfigurieren von „TestVNet5“

Dieser Schritt muss im Kontext des neuen Abonnements, Abonnement 5, ausgeführt werden. Dieser Teil kann vom Administrator in einer anderen Organisation ausgeführt werden, in deren Besitz sich das Abonnement befindet. Zum Wechseln zwischen Abonnements verwenden Sie `az account list --all`, um alle für Ihr Konto verfügbaren Abonnements aufzulisten, und verwenden Sie dann `az account set --subscription <subscriptionID>`, um zu dem Abonnement zu wechseln, das Sie verwenden möchten.

1. Stellen Sie sicher, dass Sie mit Abonnement 5 verbunden sind, und erstellen Sie dann eine Ressourcengruppe.

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. Erstellen Sie „TestVNet5“.

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. Fügen Sie Subnetze hinzu.

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. Fügen Sie das Gatewaysubnetz hinzu.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. Fordern Sie eine öffentliche IP-Adresse an.

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. Erstellen des TestVNet5-Gateways

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-8---create-the-connections"></a><a name="connections5"></a>Schritt 8: Erstellen der Verbindungen

Dieser Schritt ist in zwei CLI-Sitzungen mit den Bezeichnungen **[Abonnement 1]** und **[Abonnement 5]** aufgeteilt, da sich die Gateways in unterschiedlichen Abonnements befinden. Zum Wechseln zwischen Abonnements verwenden Sie `az account list --all`, um alle für Ihr Konto verfügbaren Abonnements aufzulisten, und verwenden Sie dann `az account set --subscription <subscriptionID>`, um zu dem Abonnement zu wechseln, das Sie verwenden möchten.

1. **[Abonnement 1]** Melden Sie sich an, und stellen Sie eine Verbindung mit Abonnement 1 her. Führen Sie den folgenden Befehl aus, um den Namen und die ID des Gateways aus der Ausgabe abzurufen:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Kopieren Sie die Ausgabe für "id:". Senden Sie die ID und den Namen des VNET-Gateways (VNet1GW) per E-Mail oder mit einer anderen Methode an den Administrator von Abonnement 5.

   Beispielausgabe:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[Abonnement 5]** Melden Sie sich an, und stellen Sie eine Verbindung mit Abonnement 5 her. Führen Sie den folgenden Befehl aus, um den Namen und die ID des Gateways aus der Ausgabe abzurufen:

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   Kopieren Sie die Ausgabe für "id:". Senden Sie die ID und den Namen des VNET-Gateways (VNet5GW) per E-Mail oder mit einer anderen Methode an den Administrator von Abonnement 1.

3. **[Abonnement 1]** In diesem Schritt erstellen Sie die Verbindung von „TestVNet1“ mit „TestVNet5“. Sie können Ihre eigenen Werte für den gemeinsam verwendeten Schlüssel nutzen, der gemeinsam verwendete Schlüssel muss jedoch für beide Verbindungen übereinstimmen. Das Erstellen einer Verbindung kann etwas dauern.  Stellen Sie sicher, dass Sie eine Verbindung mit Abonnement 1 herstellen.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[Abonnement 5]** Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von „TestVNet5“ mit „TestVNet1“. Vergewissern Sie sich, dass die gemeinsam verwendeten Schlüssel übereinstimmen, und dass Sie die Verbindung mit Abonnement 5 herstellen.

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify-the-connections"></a><a name="verify"></a>Überprüfen der Verbindungen
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Häufig gestellte Fragen zu VNet-zu-VNet-Verbindungen
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie in der [Dokumentation zu virtuellen Computern](../index.yml).
* Informationen zu BGP finden Sie in der [Übersicht über BGP](vpn-gateway-bgp-overview.md) und unter [Konfigurieren von BGP](vpn-gateway-bgp-resource-manager-ps.md).