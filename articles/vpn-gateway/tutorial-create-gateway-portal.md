---
title: 'Tutorial: Erstellen und Verwalten eines VPN Gateways: Azure-Portal'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe des Portals eine Azure VPN Gateway-Instanz erstellen, bereitstellen und verwalten.
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 933b71d75eacdca015a38524870f25a345e76d22
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746164"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Tutorial: Erstellen und Verwalten eines VPN-Gateways über das Azure-Portal

Azure VPN-Gateways ermöglichen standortübergreifende Konnektivität zwischen lokalen Kundenumgebungen und Azure. In diesem Tutorial werden grundlegende Aufgaben der Azure VPN-Gatewaybereitstellung beschrieben, z.B. Erstellen und Verwalten eines VPN-Gateways. Sie können ein Gateway auch mit der [Azure CLI](create-routebased-vpn-gateway-cli.md) oder mit [Azure PowerShell](create-routebased-vpn-gateway-powershell.md) erstellen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Netzwerks
> * Erstellen eines VPN-Gateways
> * Anzeigen der öffentlichen IP-Adresse des Gateways
> * Ändern der Größe eines VPN-Gateways (ändern der Größe der SKU)
> * Zurücksetzen eines VPN-Gateways

Im folgenden Diagramm sind das virtuelle Netzwerk und das VPN-Gateway dargestellt, die im Rahmen dieses Tutorials erstellt wurden.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagramm zum VNet- und VPN-Gateway":::

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sollten Sie über keine Organisation verfügen, [können Sie kostenlos eine erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie ein VNet unter Verwendung der folgenden Werte:

* **Ressourcengruppe:** TestRG1
* **Name:** VNet1
* **Region:** (USA) USA, Osten
* **IPv4-Adressraum**: 10.1.0.0/16
* **Subnetzname:** FrontEnd
* **Subnetzadressraum**: 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Erstellen eines VPN-Gateways

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. Häufig kann die Erstellung eines Gateways je nach ausgewählter Gateway-SKU mindestens 45 Minuten dauern.

Erstellen Sie ein virtuelles Netzwerkgateway mit den folgenden Werten:

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

## <a name="view-the-public-ip-address"></a><a name="view"></a>Anzeigen der öffentlichen IP-Adresse

Sie können die öffentliche IP-Adresse des Gateways auf der Seite **Übersicht** für Ihr Gateway anzeigen.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Seite „Übersicht“":::

Klicken Sie auf den Link „Name/IP-Adresse“ neben **Öffentliche IP-Adresse**, um zusätzliche Informationen zum öffentlichen IP-Adressobjekt anzuzeigen.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Ändern der Größe einer Gateway-SKU

Es gibt bestimmte Regeln bezüglich der Größenänderung im Vergleich zur Änderung einer Gateway-SKU. In diesem Abschnitt wird die Größe der SKU geändert. Weitere Informationen finden Sie unter [Gatewayeinstellungen: Ändern der Größe und Ändern anderer Eigenschaften von SKUs](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Zurücksetzen eines Gateways

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese Anwendung nicht weiter verwenden oder zum nächsten Tutorial wechseln möchten, löschen Sie diese Ressourcen mithilfe der folgenden Schritte:

1. Geben Sie oben im Portal den Namen Ihrer Ressourcengruppe im Suchfeld **Suche** ein, und wählen Sie ihn in den Suchergebnissen aus.

1. Wählen Sie die Option **Ressourcengruppe löschen**.

1. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN** Ihre Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie über ein VPN-Gateway verfügen, können Sie Verbindungen konfigurieren. Die folgenden Artikel helfen Ihnen, einige der häufigsten Konfigurationen zu erstellen:

> [!div class="nextstepaction"]
> [Site-to-Site-VPN-Verbindungen](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

> [!div class="nextstepaction"]
> [Point-to-Site-VPN-Verbindungen](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
