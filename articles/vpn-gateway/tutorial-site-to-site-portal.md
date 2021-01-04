---
title: 'Tutorial: Verbinden eines lokalen Netzwerks mit einem virtuellen Netzwerk: Azure-Portal'
description: Hier erfahren Sie, wie Sie unter Verwendung des Portals eine Site-to-Site-VPN Gateway-IPsec-Verbindung zwischen Ihrem lokalen Netzwerk und einem virtuellen Azure-Netzwerk über das öffentliche Internet herstellen.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: d078d4b106136c95d67eb467bc16e22e2bb91c5c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96747289"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Tutorial: Erstellen einer Site-to-Site-Verbindung im Azure-Portal

Azure VPN-Gateways ermöglichen standortübergreifende Konnektivität zwischen lokalen Kundenumgebungen und Azure. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal zum Erstellen einer Site-to-Site-VPN-Gateway-Verbindung zwischen Ihrem lokalen Netzwerk und dem VNET verwenden. Diese Konfiguration kann auch per [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) oder mithilfe der [Azure CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md) erstellt werden.

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagramm für die standortübergreifende Site-to-Site-VPN Gateway-Verbindung":::

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks
> * Erstellen eines VPN-Gateways
> * Erstellen eines Gateways für das lokale Netzwerk
> * Erstellen einer VPN-Verbindung
> * Überprüfen der Verbindung
> * Herstellen einer Verbindung mit einem virtuellen Computer

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sollten Sie über keine Organisation verfügen, [können Sie kostenlos eine erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Achten Sie darauf, dass Sie ein kompatibles VPN-Gerät nutzen (und über eine Person verfügen, die es konfigurieren kann). Weitere Informationen zu kompatiblen VPN-Geräten und zur Gerätekonfiguration finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md).
* Vergewissern Sie sich, dass Sie über eine externe öffentliche IPv4-Adresse für Ihr VPN-Gerät verfügen.
* Falls Sie nicht mit den IP-Adressbereichen in Ihrer lokalen Netzwerkkonfiguration vertraut sind, wenden Sie sich an eine Person, die Ihnen diese Informationen zur Verfügung stellen kann. Beim Erstellen dieser Konfiguration müssen Sie die Präfixe für die IP-Adressbereiche angeben, die Azure an Ihren lokalen Standort weiterleitet. Kein Subnetz Ihres lokalen Netzwerks darf sich mit den Subnetzen des virtuellen Netzwerks überschneiden, mit dem Sie eine Verbindung herstellen möchten.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie ein virtuelles Netzwerk (VNET) mit den folgenden Werten:

* **Ressourcengruppe:** TestRG1
* **Name:** VNet1
* **Region:** (USA) USA, Osten
* **IPv4-Adressraum**: 10.1.0.0/16
* **Subnetzname:** FrontEnd
* **Subnetzadressraum**: 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Erstellen eines VPN-Gateways

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

### <a name="about-the-gateway-subnet"></a>Informationen zum Gatewaysubnetz

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Erstellen des Gateways

Erstellen Sie ein VPN-Gateway mit den folgenden Werten:

* **Name:** VNet1GW
* **Region:** East US
* **Gatewaytyp**: VPN
* **VPN-Typ:** routenbasiert
* **SKU**: VpnGw1
* **Generation**: Generation1
* **Virtuelles Netzwerk:** VNet1
* **Adressbereich für Gatewaysubnetz**: 10.1.255.0/27
* **Öffentliche IP-Adresse**: Neu erstellen
* **Öffentliche IP-Adresse:** VNet1GWpip
* **Aktiv/Aktiv-Modus aktivieren**: Disabled
* **BGP konfigurieren:** Disabled

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="view-the-public-ip-address"></a><a name="view"></a>Anzeigen der öffentlichen IP-Adresse

Sie können die öffentliche IP-Adresse des Gateways auf der Seite **Übersicht** für Ihr Gateway anzeigen.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Seite „Übersicht“":::

Klicken Sie auf den Link „Name/IP-Adresse“ neben **Öffentliche IP-Adresse**, um zusätzliche Informationen zum öffentlichen IP-Adressobjekt anzuzeigen.

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Erstellen eines Gateways für das lokale Netzwerk

Das lokale Netzwerkgateway ist ein spezielles Objekt, das Ihrem lokalen Standort für Routingzwecke entspricht. Sie geben dem Standort einen Namen, über den Azure darauf verweisen kann, und geben dann die IP-Adresse des lokalen VPN-Geräts an, mit dem Sie eine Verbindung herstellen. Außerdem geben Sie die IP-Adresspräfixe an, die über das VPN-Gateway an das VPN-Gerät weitergeleitet werden. Die von Ihnen angegebenen Adresspräfixe befinden sich in Ihrem lokalen Netzwerk. Wenn bei dem lokalen Netzwerk Änderungen vorgenommen werden oder Sie die öffentliche IP-Adresse des VPN-Geräts ändern müssen, können Sie dies Werte später bequem aktualisieren.

Erstellen Sie ein lokales Netzwerkgateway mit den folgenden Werten:

* **Name:** Site1
* **Ressourcengruppe:** TestRG1
* **Standort:** East US

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Konfigurieren des VPN-Geräts

Für Site-to-Site-Verbindungen mit einem lokalen Netzwerk ist ein VPN-Gerät erforderlich. In diesem Schritt konfigurieren Sie Ihr VPN-Gerät. Beim Konfigurieren des VPN-Geräts benötigen Sie die folgenden Werte:

* Einen gemeinsam verwendeten Schlüssel. Dies ist derselbe gemeinsame Schlüssel, den Sie beim Erstellen Ihrer Site-to-Site-VPN-Verbindung angeben. In unseren Beispielen verwenden wir einen einfachen gemeinsamen Schlüssel. Es wird empfohlen, einen komplexeren Schlüssel zu generieren.
* Die öffentliche IP-Adresse Ihres Gateways für virtuelle Netzwerke. Sie können die öffentliche IP-Adresse mit dem Azure-Portal, mit PowerShell oder mit der CLI anzeigen. Im Azure-Portal ermitteln Sie die öffentliche IP-Adresse Ihres VPN-Gateways, indem Sie zu **Gateways für virtuelle Netzwerke** navigieren und auf den Namen Ihres Gateways klicken.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Erstellen einer VPN-Verbindung

Erstellen Sie die Site-to-Site-VPN-Verbindung zwischen dem Gateway Ihres virtuellen Netzwerks und Ihrem lokalen VPN-Gerät.

Erstellen Sie eine Verbindung mit den folgenden Werten:

* **Name des Gateways für das lokale Netzwerk**: Site1
* **Verbindungsname**: VNet1toSite1
* **Gemeinsam verwendeter Schlüssel:** In diesem Beispiel verwenden wir „abc123“. Sie können jedoch einen beliebigen, mit Ihrer VPN-Hardware kompatiblen Wert verwenden. Wichtig ist, dass die Werte auf beiden Seiten der Verbindung übereinstimmen.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Überprüfen der VPN-Verbindung

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Herstellen einer Verbindung mit einem virtuellen Computer

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Optionale Schritte

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Hinzufügen zusätzlicher Verbindungen zum Gateway

Sie können weitere Verbindungen hinzufügen. Voraussetzung dafür ist, dass sich Adressbereiche zwischen Verbindungen nicht überlappen.

1. Um eine zusätzliche Verbindung hinzuzufügen, navigieren Sie zum VPN-Gateway, und klicken Sie auf **Verbindungen**, um die Seite „Verbindungen“ zu öffnen.
1. Klicken Sie auf **+ Hinzufügen**, um die Verbindung hinzuzufügen. Passen Sie den Verbindungstyp an, um VNET-to-VNET (falls Sie eine Verbindung mit einem anderen VNET-Gateway herstellen) oder Site-to-Site widerzuspiegeln.
1. Wenn Sie eine Verbindung über Site-to-Site herstellen und noch kein lokales Netzwerkgateway für die Website erstellt haben, mit der Sie eine Verbindung herstellen möchten, können Sie ein neues erstellen.
1. Geben Sie den gemeinsam verwendeten Schlüssel ein, den Sie verwenden möchten, und klicken Sie auf **OK**, um die Verbindung zu erstellen.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ändern der Größe einer Gateway-SKU

Es gibt bestimmte Regeln bezüglich der Größenänderung im Vergleich zur Änderung einer Gateway-SKU. In diesem Abschnitt wird die Größe der SKU geändert. Weitere Informationen finden Sie unter [Gatewayeinstellungen: Ändern der Größe und Ändern anderer Eigenschaften von SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Zurücksetzen eines Gateways

Das Zurücksetzen von Azure VPN Gateway-Instanzen ist nützlich, wenn die standortübergreifende VPN-Verbindung bei mindestens einem Site-to-Site-VPN-Tunnel unterbrochen ist. In diesem Fall funktionieren Ihre lokalen VPN-Geräte ordnungsgemäß, können jedoch keine IPsec-Tunnelverbindungen mit Azure VPN Gateway-Instanzen herstellen.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="connectVM"></a>Weitere Überlegungen zur Konfiguration

S2S-Konfigurationen können auf verschiedene Arten angepasst werden. Weitere Informationen finden Sie in den folgenden Artikeln:

* Informationen zu BGP finden Sie in der [Übersicht über BGP](vpn-gateway-bgp-overview.md) und unter [Konfigurieren von BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Weitere Informationen zur Tunnelerzwingung finden Sie unter [Konfigurieren der Tunnelerzwingung mit dem Azure Resource Manager-Bereitstellungsmodell](vpn-gateway-forced-tunneling-rm.md).
* Informationen zu hochverfügbaren Aktiv/Aktiv-Verbindungen finden Sie unter [Standortübergreifende Verbindungen und VNET-zu-VNET-Verbindungen mit hoher Verfügbarkeit](vpn-gateway-highlyavailable.md).
* Informationen zum Beschränken von Netzwerkdatenverkehr auf Ressourcen in einem virtuellen Netzwerk finden Sie unter [Netzwerksicherheit](../virtual-network/network-security-groups-overview.md).
* Informationen zur Weiterleitung von Datenverkehr zwischen Azure, lokalen Speicherorten und Internetressourcen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiter verwenden oder zum nächsten Tutorial wechseln möchten, löschen Sie diese Ressourcen mithilfe der folgenden Schritte:

1. Geben Sie oben im Portal den Namen Ihrer Ressourcengruppe im Suchfeld **Suche** ein, und wählen Sie ihn in den Suchergebnissen aus.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** Ihre Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie eine Site-to-Site-Verbindung konfiguriert haben, können Sie dem gleichen Gateway eine Point-to-Site-Verbindung hinzufügen.

> [!div class="nextstepaction"]
> [Point-to-Site-VPN-Verbindungen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
