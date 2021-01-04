---
title: 'Verbinden eines lokalen Netzwerks mit einem virtuellen Netzwerk: Site-to-Site-VPN: Befehlszeilenschnittstelle (CLI)'
description: Erstellen Sie eine IPsec-Site-to-Site-VPN-Gateway-Verbindung zwischen Ihrem lokalen Netzwerk und einem virtuellen Azure-Netzwerk über das öffentliche Internet mithilfe der CLI.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/23/2020
ms.author: cherylmc
ms.openlocfilehash: 3a29c4522812b728f553bf52543ac3307f0ffbda
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94739936"
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung per CLI

In diesem Artikel wird beschrieben, wie Sie die Azure-CLI zum Erstellen einer Site-to-Site-VPN Gateway-Verbindung aus Ihrem lokalen Netzwerk mit dem VNet verwenden. Die Schritte in diesem Artikel gelten für das Resource Manager-Bereitstellungsmodell. Sie können diese Konfiguration auch mit einem anderen Bereitstellungstool oder -modell erstellen. Wählen Sie hierzu in der folgenden Liste eine andere Option:<br>

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Azure-Portal (klassisch)](vpn-gateway-howto-site-to-site-classic-portal.md)

![Diagramm für die standortübergreifende Site-to-Site-VPN Gateway-Verbindung](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

Eine Site-to-Site-VPN-Gateway-Verbindung wird verwendet, um Ihr lokales Netzwerk über einen IPsec/IKE-VPN-Tunnel (IKEv1 oder IKEv2) mit einem virtuellen Azure-Netzwerk zu verbinden. Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist. Weitere Informationen zu VPN-Gateways finden Sie unter [Informationen zu VPN Gateway](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Voraussetzungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass die folgenden Voraussetzungen erfüllt sind bzw. Folgendes vorhanden ist:

* Achten Sie darauf, dass Sie ein kompatibles VPN-Gerät nutzen (und über eine Person verfügen, die es konfigurieren kann). Weitere Informationen zu kompatiblen VPN-Geräten und zur Gerätekonfiguration finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).
* Vergewissern Sie sich, dass Sie über eine externe öffentliche IPv4-Adresse für Ihr VPN-Gerät verfügen.
* Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, wenden Sie sich an eine Person, die Ihnen diese Informationen zur Verfügung stellen kann. Beim Erstellen dieser Konfiguration müssen Sie die Präfixe für die IP-Adressbereiche angeben, die Azure an Ihren lokalen Standort weiterleitet. Kein Subnetz Ihres lokalen Netzwerks darf sich mit den Subnetzen des virtuellen Netzwerks überschneiden, mit dem Sie eine Verbindung herstellen möchten.
[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
* Für diesen Artikel ist mindestens Version 2.0 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

### <a name="example-values"></a><a name="example"></a>Beispielwerte

Sie können die folgenden Werte zum Erstellen einer Testumgebung oder zum besseren Verständnis der Beispiele in diesem Artikel nutzen:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Verbinden mit Ihrem Abonnement

Stellen Sie eine Verbindung mit Ihrem Abonnement her, wenn Sie die CLI lokal ausführen möchten. Wenn Sie Azure Cloud Shell im Browser verwenden, müssen Sie keine Verbindung mit Ihrem Abonnement herstellen. Die Verbindung wird in Azure Cloud Shell automatisch hergestellt. Vergewissern Sie sich nach der Verbindungsherstellung aber, dass Sie das richtige Abonnement nutzen.

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a><a name="rg"></a>2. Erstellen einer Ressourcengruppe

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen „TestRG1“ am Standort „eastus“ erstellt. Falls Sie in dieser Region bereits über eine Ressourcengruppe verfügen, die Sie zum Erstellen des VNet nutzen möchten, können Sie sie verwenden.

```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="3-create-a-virtual-network"></a><a name="VNet"></a>3. Erstellen eines virtuellen Netzwerks

Falls Sie noch kein virtuelles Netzwerk besitzen, erstellen Sie eins mit dem Befehl [az network vnet create](/cli/azure/network/vnet). Stellen Sie beim Erstellen eines virtuellen Netzwerks sicher, dass sich die angegebenen Adressräume und die Adressräume im lokalen Netzwerk nicht überschneiden.

>[!NOTE]
>Damit für dieses VNet eine Verbindung mit einem lokalen Speicherort hergestellt werden kann, müssen Sie in Zusammenarbeit mit Ihrem Administrator des lokalen Netzwerks einen IP-Adressbereich festlegen, den Sie speziell für dieses virtuelle Netzwerk verwenden können. Falls ein Adressbereich auf beiden Seiten der VPN-Verbindung und somit doppelt vorhanden ist, wird Datenverkehr unter Umständen nicht wie erwartet weitergeleitet. Wenn Sie für dieses VNet eine Verbindung mit einem anderen VNet herstellen möchten, darf sich der Adressraum außerdem nicht mit dem anderen VNet überlappen. Achten Sie darauf, dass Sie Ihre Netzwerkkonfiguration entsprechend planen.
>
>

Im folgenden Beispiel werden ein virtuelles Netzwerk mit dem Namen „TestVNet1“ und ein Subnetz mit dem Namen „Subnet1“ erstellt.

```azurecli-interactive
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## <a name="4-create-the-gateway-subnet"></a>4. <a name="gwsub"></a>Erstellen des Gatewaysubnetzes


[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

Erstellen Sie das Gatewaysubnetz mit dem Befehl [az network vnet subnet create](/cli/azure/network/vnet/subnet).

```azurecli-interactive
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="5-create-the-local-network-gateway"></a><a name="localnet"></a>5. Erstellen des Gateways des lokalen Netzwerks

Mit dem Gateway des lokalen Netzwerks ist normalerweise Ihr lokaler Standort gemeint. Sie geben dem Standort einen Namen, über den Azure darauf verweisen kann, und geben dann die IP-Adresse des lokalen VPN-Geräts an, mit dem Sie eine Verbindung herstellen. Außerdem geben Sie die IP-Adresspräfixe an, die über das VPN-Gateway an das VPN-Gerät weitergeleitet werden. Die von Ihnen angegebenen Adresspräfixe befinden sich in Ihrem lokalen Netzwerk. Sie können diese Präfixe leicht aktualisieren, wenn sich Ihr lokales Netzwerk ändert.

Verwenden Sie folgende Werte:

* *--gateway-ip-address* ist die IP-Adresse Ihres lokalen VPN-Geräts.
* *--local-address-prefixes* steht für Ihre lokalen Adressräume.

Fügen Sie mit dem Befehl [az network local-gateway create](/cli/azure/network/local-gateway) ein Gateway des lokalen Netzwerks mit mehreren Adresspräfixen hinzu:

```azurecli-interactive
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="6-request-a-public-ip-address"></a><a name="PublicIP"></a>6. Anfordern einer öffentlichen IP-Adresse

Ein VPN-Gateway muss über eine öffentliche IP-Adresse verfügen. Sie fordern zuerst die IP-Adressressource an und verweisen dann beim Erstellen des Gateways des virtuellen Netzwerks darauf. Die IP-Adresse wird bei der Erstellung des VPN-Gateways der Ressource dynamisch zugewiesen. VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Die Zuweisung einer statischen öffentlichen IP-Adresse kann nicht angefordert werden. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

Fordern Sie mit dem Befehl [az network public-ip create](/cli/azure/network/public-ip) eine dynamische öffentliche IP-Adresse an.

```azurecli-interactive
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="7-create-the-vpn-gateway"></a><a name="CreateGateway"></a>7. Erstellen des VPN-Gateways

Erstellen Sie das VPN-Gateway für das virtuelle Netzwerk. Die Erstellung eines VPN-Gateways kann bis zu 45 Minuten dauern.

Verwenden Sie folgende Werte:

* *--gateway-type* für eine Site-to-Site-Konfiguration lautet *Vpn*. Der Gatewaytyp ist immer spezifisch für die Konfiguration, die Sie implementieren. Weitere Informationen finden Sie unter [Gatewaytypen](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* *--vpn-type* kann *RouteBased* (in einigen Dokumentationen als dynamisches Gateway bezeichnet) oder *PolicyBased* lauten (in einigen Dokumentationen als statisches Gateway bezeichnet). Die Einstellung richtet sich nach den Anforderungen des Geräts, mit dem Sie eine Verbindung herstellen. Weitere Informationen zu VPN-Gatewaytypen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* Wählen Sie die gewünschte Gateway-SKU aus. Für bestimmte SKUs gelten Einschränkungen bei der Konfiguration. Weitere Informationen finden Sie unter [Gateway-SKUs](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Erstellen Sie das VPN-Gateway mit dem Befehl [az network vnet-gateway create](/cli/azure/network/vnet-gateway). Wenn Sie diesen Befehl mit dem Parameter „--no-wait“ ausführen, wird kein Feedback bzw. keine Ausgabe angezeigt. Dieser Parameter ermöglicht die Erstellung des Gateways im Hintergrund. Die Erstellung eines Gateways dauert ca. 45 Minuten.

```azurecli-interactive
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="8-configure-your-vpn-device"></a><a name="VPNDevice"></a>8. Konfigurieren des VPN-Geräts

Für Site-to-Site-Verbindungen mit einem lokalen Netzwerk ist ein VPN-Gerät erforderlich. In diesem Schritt konfigurieren Sie Ihr VPN-Gerät. Beim Konfigurieren des VPN-Geräts benötigen Sie Folgendes:

- Einen gemeinsam verwendeten Schlüssel. Dies ist derselbe gemeinsame Schlüssel, den Sie beim Erstellen Ihrer Site-to-Site-VPN-Verbindung angeben. In unseren Beispielen verwenden wir einen einfachen gemeinsamen Schlüssel. Es wird empfohlen, einen komplexeren Schlüssel zu generieren.
- Die öffentliche IP-Adresse Ihres Gateways für virtuelle Netzwerke. Sie können die öffentliche IP-Adresse mit dem Azure-Portal, mit PowerShell oder mit der CLI anzeigen.  Verwenden Sie den Befehl [az network public-ip list](/cli/azure/network/public-ip), um die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks zu ermitteln. Zur besseren Lesbarkeit wird die Ausgabe formatiert, damit die Liste mit den öffentlichen IP-Adressen in Tabellenform angezeigt wird.

  ```azurecli-interactive
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="9-create-the-vpn-connection"></a><a name="CreateConnection"></a>9. Erstellen der VPN-Verbindung

Erstellen Sie die Site-to-Site-VPN-Verbindung zwischen dem Gateway Ihres virtuellen Netzwerks und Ihrem lokalen VPN-Gerät. Achten Sie besonders auf den Wert des gemeinsam verwendeten Schlüssels. Er muss mit dem konfigurierten Wert des gemeinsam verwendeten Schlüssels für Ihr VPN-Gerät übereinstimmen.

Erstellen Sie die Verbindung mit dem Befehl [az network vpn-connection create](/cli/azure/network/vpn-connection).

```azurecli-interactive
az network vpn-connection create --name VNet1toSite2 --resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

Die Verbindung wird nach kurzer Zeit hergestellt.

## <a name="10-verify-the-vpn-connection"></a><a name="toverify"></a>10. Überprüfen der VPN-Verbindung

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Falls Sie ein anderes Verfahren zum Überprüfen Ihrer Verbindung verwenden möchten, helfen Ihnen die Informationen unter [Überprüfen einer VPN Gateway-Verbindung](vpn-gateway-verify-connection-resource-manager.md) weiter.

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="common-tasks"></a><a name="tasks"></a>Häufige Aufgaben

Dieser Abschnitt enthält allgemeine Befehle, die beim Arbeiten mit Site-to-Site-Konfigurationen hilfreich sind. Die vollständige Liste der CLI-Netzwerkbefehle finden Sie unter [Networking - az network](/cli/azure/network) (Netzwerke – az network).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Nächste Schritte

* Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtuelle Computer](../index.yml) .
* Informationen zu BGP finden Sie in der [Übersicht über BGP](vpn-gateway-bgp-overview.md) und unter [Konfigurieren von BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Weitere Informationen zur Tunnelerzwingung finden Sie unter [Informationen zur Tunnelerzwingung](vpn-gateway-forced-tunneling-rm.md).
* Informationen zu hochverfügbaren Aktiv/Aktiv-Verbindungen finden Sie unter [Standortübergreifende Verbindungen und VNET-zu-VNET-Verbindungen mit hoher Verfügbarkeit](vpn-gateway-highlyavailable.md).
* Eine Liste mit Azure-CLI-Netzwerkbefehlen finden Sie unter [Azure-CLI](/cli/azure/network).
* Informationen zum Erstellen einer Site-to-Site-VPN-Verbindung mithilfe einer Azure Resource Manager-Vorlage finden Sie unter [Create a Site-to-Site VPN Connection](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/) (Erstellen einer Site-to-Site-VPN-Verbindung).
* Informationen zum Erstellen einer VNet-zu-VNet-VPN-Verbindung mithilfe einer Azure Resource Manager-Vorlage finden Sie unter [Deploy HBase geo replication](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/) (Bereitstellen der HBase-Georeplikation).