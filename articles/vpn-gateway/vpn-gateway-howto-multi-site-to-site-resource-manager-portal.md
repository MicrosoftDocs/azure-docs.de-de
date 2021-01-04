---
title: 'Hinzufügen mehrerer VPN-Gateway-Site-to-Site-Verbindungen zu einem VNET: Azure-Portal'
description: Fügen Sie mehrere S2S-Verbindungen zu einem VPN-Gateway hinzu, für das bereits eine Verbindung besteht
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92890147"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Hinzufügen zusätzlicher Site-to-Site-Verbindungen zu einem virtuellen Netzwerk: Azure-Portal

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisch)](vpn-gateway-multi-site.md)
>

Dieser Artikel hilft Ihnen dabei, einem VPN Gateway, für das bereits eine Verbindung besteht, weitere Site-to-Site-Verbindungen (S2S) hinzuzufügen. Diese Architektur wird häufig als Konfiguration für mehrere Standorte bezeichnet. Sie können eine Standort-zu-Standort-Verbindung einem VNet hinzufügen, für das bereits eine Standort-zu-Standort-, Punkt-zu-Standort-oder VNet-zu-VNet-Verbindung besteht. Beim Hinzufügen von Verbindungen gibt es einige Einschränkungen. Überprüfen Sie den Abschnitt [Voraussetzungen](#before) in diesem Artikel, bevor Sie die Konfiguration beginnen.

Dieser Artikel gilt für Resource Manager-VNETs, die ein VPN-Gateway des Typs RouteBased aufweisen. Diese Schritte gelten nicht für neue Konfigurationen von parallel bestehenden ExpressRoute- und Site-to-Site-Verbindungen. Wenn Sie jedoch einer bereits vorhandenen Konfiguration lediglich eine neue VPN-Verbindung hinzufügen, können Sie diese Schritte ausführen. Informationen zu parallel bestehenden Verbindungen finden Sie unter [Konfigurieren von parallel bestehenden ExpressRoute- und Standort-zu-Standort-Verbindungen für das klassische Bereitstellungsmodell](../expressroute/expressroute-howto-coexist-resource-manager.md).

## <a name="prerequisites"></a><a name="before"></a>Voraussetzungen

Überprüfen Sie folgende Maßnahmen:

* Sie konfigurieren keine neue Konfiguration für parallel bestehende ExpressRoute- und VPN Gateway-Verbindungen.
* Sie verfügen über ein virtuelles Netzwerk, das mithilfe des Resource Manager-Bereitstellungsmodells mit einer bestehenden Verbindung erstellt wurde.
* Das Gateway für virtuelle Netzwerke für Ihr VNet ist RouteBased. Wenn Sie über ein PolicyBased-VPN Gateway verfügen, müssen Sie das Gateway für virtuelle Netzwerke löschen, und ein neues VPN-Gateway als RouteBased erstellen.
* Keine der Adressbereiche überlappen sich mit einem der VNets, mit der dieses VNet eine Verbindung herstellt.
* Sie haben ein kompatibles VPN-Gerät (und eine Person, die es konfigurieren kann). Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md). Wenn Sie sich mit dem Konfigurieren des VPN-Geräts oder mit den IP-Adressbereichen Ihrer lokalen Netzwerkkonfiguration nicht auskennen, müssen Sie sich an eine Person wenden, die Ihnen diese Details liefern kann.
* Sie haben eine externe öffentliche IP-Adresse für Ihr VPN-Gerät.

## <a name="configure-a-connection"></a><a name="configure"></a>Konfigurieren einer Verbindung

1. Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
1. Wählen Sie **Alle Ressourcen** aus, und suchen Sie Ihr **Gateway für virtuelle Netzwerke** in der Liste der Ressourcen, und wählen Sie es dann aus.
1. Wählen Sie auf der Seite **Gateway für virtuelle Netzwerke** die Option **Verbindungen** aus.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN Gateway-Verbindungen":::
1. Wählen Sie auf der Seite **Verbindungen** die Option **+Hinzufügen** aus.
1. Dadurch wird die Seite **Verbindung hinzufügen** geöffnet.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Seite „Verbindung hinzufügen“":::
1. Füllen Sie auf der Seite **Verbindung hinzufügen** folgende Felder aus:

   * **Name:** Der Name, den Sie dem Standort zuweisen möchten, zu dem Sie die Verbindung herstellen.
   * **Verbindungstyp:** Wählen Sie **Site-to-Site (IPsec)** aus.

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Hinzufügen eines lokalen Netzwerkgateways

1. Wählen Sie für das Feld **Lokales Netzwerkgateway** die Option **_Ein lokales Netzwerkgateway auswählen_ *_. Die Seite _* Ein lokales Netzwerkgateway auswählen** wird geöffnet.
1. Wählen Sie **+ Neu erstellen** aus, um die Seite **Lokales Netzwerkgateway erstellen** zu öffnen.

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Seite „Lokales Netzwerkgateway erstellen“":::
1. Füllen Sie auf der Seite **Lokales Netzwerkgateway erstellen** die folgenden Felder aus:

   * **Name:** Der Name, den Sie der lokalen Netzwerkgateway-Ressource geben möchten.
   * **Endpunkt:** Die öffentliche IP-Adresse des VPN-Geräts auf dem Standort, mit dem Sie eine Verbindung herstellen möchten, oder der FQDN des Endpunkts.
   * **Adressraum:** Der Adressraum, der an den Standort des lokalen Netzwerks weitergeleitet werden soll.
1. Wählen Sie auf der Seite **Lokales Netzwerkgateway erstellen** die Option **OK** aus, um die Änderungen zu speichern.

## <a name="add-the-shared-key"></a><a name="part3"></a>Hinzufügen des gemeinsam verwendeten Schlüssels

1. Kehren Sie nach dem Erstellen des lokalen Netzwerkgateways zur Seite **Verbindung hinzufügen** zurück.
1. Füllen Sie die übrigen Felder aus. Für den **gemeinsam verwendeten Schlüssel (PSK)** können Sie den gemeinsam verwendeten Schlüssel entweder über Ihr VPN-Gerät erhalten, oder hier einen Schüssel erstellen, und Ihr VPN-Gerät dann so konfigurieren, dass es den gleichen gemeinsam verwendeten Schlüssel verwendet. Wichtig ist, dass die Schlüssel genau übereinstimmen.

## <a name="create-the-connection"></a><a name="create"></a>Erstellen der Verbindung

1. Klicken Sie unten auf der Seite auf **OK**, um die Verbindung zu erstellen. Das Herstellen der Verbindung startet sofort.
1. Sobald die Verbindung hergestellt ist, können Sie sie anzeigen und überprüfen.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Anzeigen und Überprüfen der VPN-Verbindung

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter [Virtual Machines-Lernpfade](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
