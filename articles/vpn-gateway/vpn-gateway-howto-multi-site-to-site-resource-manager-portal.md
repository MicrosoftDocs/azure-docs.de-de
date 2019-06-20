---
title: 'Hinzufügen mehrerer VPN-Gateway-Site-to-Site-Verbindungen zu einem VNET: Azure-Portal: Resource Manager | Microsoft-Dokumentation'
description: Fügen Sie mehrere S2S-Verbindungen zu einem VPN-Gateway hinzu, für das bereits eine Verbindung besteht
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: f3e8b165-f20a-42ab-afbb-bf60974bb4b1
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 4b9f007e00d0912687b723bd4f7e747da893948d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60760465"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Hinzufügen einer Site-to-Site-Verbindung (S2S) zu einem VNet mit einer vorhandenen VPN-Gatewayverbindung

> [!div class="op_single_selector"]
> * [Azure-Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klassisch)](vpn-gateway-multi-site.md)
>
> 

Dieser Artikel hilft Ihnen dabei, mithilfe des Azure-Portals einem VPN-Gateway, für das bereits eine Verbindung besteht, Site-to-Site-Verbindungen (S2S) hinzuzufügen. Diese Art der Verbindung wird häufig als Multi-Site-Konfiguration bezeichnet. Sie können eine Standort-zu-Standort-Verbindung einem VNet hinzufügen, für das bereits eine Standort-zu-Standort-, Punkt-zu-Standort-oder VNet-zu-VNet-Verbindung besteht. Beim Hinzufügen von Verbindungen gibt es einige Einschränkungen. Überprüfen Sie den Abschnitt [Voraussetzungen](#before) in diesem Artikel, bevor Sie die Konfiguration beginnen. 

Dieser Artikel gilt für Resource Manager-VNETs, die ein VPN-Gateway des Typs RouteBased aufweisen. Diese Schritte gelten nicht für Konfigurationen von parallel bestehenden ExpressRoute- und S2S-Verbindungen. Informationen zu parallel bestehenden Verbindungen finden Sie unter [Konfigurieren von parallel bestehenden ExpressRoute- und Standort-zu-Standort-Verbindungen für das klassische Bereitstellungsmodell](../expressroute/expressroute-howto-coexist-resource-manager.md).

### <a name="deployment-models-and-methods"></a>Bereitstellungsmodelle und -methoden
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Die folgende Tabelle wird aktualisiert, wenn neue Artikel und weitere Tools für diese Konfiguration verfügbar werden. Wenn ein Artikel verfügbar ist, fügen wir in der Tabelle einen direkten Link dazu ein.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before"></a>Voraussetzungen
Überprüfen Sie folgende Maßnahmen:

* Sie erstellen keine parallel bestehende ExpressRoute/S2S-Verbindung.
* Sie verfügen über ein virtuelles Netzwerk, das mithilfe des Resource Manager-Bereitstellungsmodells mit einer bestehenden Verbindung erstellt wurde.
* Das Gateway für virtuelle Netzwerke für Ihr VNet ist RouteBased. Wenn Sie über ein PolicyBased-VPN Gateway verfügen, müssen Sie das Gateway für virtuelle Netzwerke löschen, und ein neues VPN-Gateway als RouteBased erstellen.
* Keine der Adressbereiche überlappen sich mit einem der VNets, mit der dieses VNet eine Verbindung herstellt.
* Sie haben ein kompatibles VPN-Gerät (und eine Person, die es konfigurieren kann). Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md). Wenn Sie sich mit dem Konfigurieren des VPN-Geräts oder mit den IP-Adressbereichen Ihrer lokalen Netzwerkkonfiguration nicht auskennen, müssen Sie sich an eine Person wenden, die Ihnen diese Details liefern kann.
* Sie haben eine externe öffentliche IP-Adresse für Ihr VPN-Gerät. Diese IP-Adresse darf sich nicht hinter einer NAT befinden.

## <a name="part1"></a>Teil 1 – Konfigurieren einer Verbindung
1. Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich, falls erforderlich, mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Alle Ressourcen**, suchen Sie Ihr **Gateway für virtuelle Netzwerke** in der Liste der Ressourcen, und klicken Sie darauf.
3. Klicken Sie auf der Seite **Gateway für virtuelle Netzwerke** auf **Verbindungen**.
   
    ![Seite „Verbindungen“](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Seite „Verbindungen“")<br>
4. Klicken Sie auf der Seite **Verbindungen** auf **+ Hinzufügen**.
   
    ![Schaltfläche „Verbindung hinzufügen“](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Add connection button")<br>
5. Füllen Sie auf der Seite **Verbindung hinzufügen** folgende Felder aus:
   
   * **Name:** Der Name, den Sie dem Standort, mit dem Sie die Verbindung herstellen, zuweisen möchten.
   * **Verbindungstyp:** Wählen Sie **Site-to-Site (IPsec)** aus.
     
     ![Seite „Verbindung hinzufügen“](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Seite „Verbindung hinzufügen“")<br>

## <a name="part2"></a>Teil 2 – Lokales Netzwerkgateway hinzufügen
1. Klicken Sie auf **Lokales Netzwerkgateway** ***Ein lokales Netzwerkgateway auswählen***. Daraufhin wird die Seite **Lokales Netzwerkgateway auswählen** geöffnet.
   
    ![Auswählen eines lokalen Netzwerkgateways](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Choose local network gateway")<br>
2. Klicken Sie auf **Neu erstellen**, um die Seite **Lokales Netzwerkgateway erstellen** zu öffnen.
   
    ![Seite „Lokales Netzwerkgateway erstellen“](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Lokales Netzwerkgateway erstellen")<br>
3. Füllen Sie auf der Seite **Lokales Netzwerkgateway erstellen** die folgenden Felder aus:
   
   * **Name:** Der Name, den Sie der lokalen Netzwerkgateway-Ressource zuweisen möchten.
   * **IP-Adresse:** Die öffentliche IP-Adresse des VPN-Geräts an dem Standort, mit dem Sie eine Verbindung herstellen möchten.
   * **Adressraum:** Der Adressraum, der an den Standort des lokalen Netzwerks weitergeleitet werden soll.
4. Klicken Sie auf der Seite **Lokales Netzwerkgateway erstellen** auf **OK**, um die Änderungen zu speichern.

## <a name="part3"></a>Teil 3 – Hinzufügen des gemeinsam verwendeten Schlüssels, und erstellen der Verbindung
1. Fügen Sie auf der Seite **Verbindung hinzufügen** den gemeinsam verwendeten Schlüssel hinzu, mit dem Sie Ihre Verbindung erstellen möchten. Sie können den gemeinsam verwendeten Schlüssel entweder über Ihr VPN-Gerät erhalten, oder hier einen Schüssel erstellen, und Ihr VPN-Gerät dann so konfigurieren, dass es den gleichen gemeinsam verwendeten Schlüssel verwendet. Wichtig ist, dass die Schlüssel genau übereinstimmen.
   
    ![Gemeinsam verwendeter Schlüssel](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Gemeinsam verwendeter Schlüssel")<br>
2. Klicken Sie unten auf der Seite auf **OK**, um die Verbindung zu erstellen.

## <a name="part4"></a>Teil 4 – Überprüfen der VPN-Verbindung


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Weitere Informationen finden Sie unter dem [Lernpfad für virtuelle Computer](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
