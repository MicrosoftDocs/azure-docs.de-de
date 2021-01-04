---
title: 'Tutorial: Azure ExpressRoute – Hinzufügen eines Gateways zu einem VNET (Azure-Portal)'
description: In diesem Tutorial wird beschrieben, wie Sie einem VNET für ExpressRoute über das Azure-Portal ein Gateway für virtuelle Netzwerke hinzufügen.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "91761792"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Tutorial: Konfigurieren eines virtuellen Netzwerkgateways für ExpressRoute mit dem Azure-Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure-Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisch – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Dieses Tutorial führt Sie durch die Schritte, die zum Hinzufügen eines Gateways für virtuelle Netzwerke für ein vorhandenes VNET erforderlich sind. Dieser Artikel führt Sie durch die Schritte, die zum Hinzufügen, Ändern der Größe und Entfernen eines virtuellen Netzwerkgateways für ein vorhandenes virtuelles Netzwerk (VNet) erforderlich sind. Die Schritte für diese Konfiguration gelten speziell für VNets, die gemäß dem Resource Manager-Bereitstellungsmodell erstellt wurden, das in einer ExpressRoute-Konfiguration verwendet wird. Weitere Informationen zu virtuellen Netzwerkgateways und Gateway-Konfigurationseinstellungen für ExpressRoute finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](expressroute-about-virtual-network-gateways.md). 

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:
> [!div class="checklist"]
> - Erstellen Sie ein Gatewaysubnetz.
> - Erstellen eines Gateways für virtuelle Netzwerke

## <a name="prerequisites"></a>Voraussetzungen

Bei den Schritten für diese Aufgabe wird ein VNet basierend auf den Werten verwendet, die in der folgenden Referenzliste für die Konfiguration enthalten sind. Diese Liste wird in den Beispielschritten verwendet. Sie können diese Liste als Referenz verwenden und die Werte durch Ihre eigenen Werte ersetzen.

**Referenzliste für Konfiguration**

* Name des virtuellen Netzwerks = TestVNet
* Adressraum des virtuellen Netzwerks: 192.168.0.0/16
* Subnetzname = FrontEnd 
    * Subnetzadressraum = 192.168.1.0/24
* Ressourcengruppe = TestRG
* Standort = USA, Osten
* Name des Gatewaysubnetzes: GatewaySubnet. Sie müssen ein Gatewaysubnetz immer *GatewaySubnet* nennen.
    * Adressraum des Gatewaysubnetzes = 192.168.200.0/26
* Gatewayname = ERGW
* Öffentlicher Gateway-IP-Name = MyERGWVIP
* Gatewaytyp = ExpressRoute. Dieser Typ ist für eine ExpressRoute-Konfiguration erforderlich.

Sie können sich ein [Video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) über diese Schritte ansehen, bevor Sie mit der Konfiguration beginnen.

## <a name="create-the-gateway-subnet"></a>Erstellen des Gatewaysubnetzes

1. Navigieren Sie im [Portal](https://portal.azure.com) zum virtuellen Resource Manager-Netzwerk, für das Sie ein virtuelles Netzwerkgateway erstellen möchten.
1. Wählen Sie im Abschnitt **Einstellungen** Ihres VNET die Option **Subnetze** aus, um die Subnetzeinstellungen zu erweitern.
1. Wählen Sie in den **Subnetzeinstellungen** die Option **+ Gatewaysubnetz** aus, um ein Gatewaysubnetz hinzuzufügen. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Hinzufügen des Gatewaysubnetzes":::

1. Als **Name** für Ihr Subnetz wird automatisch der Wert „GatewaySubnet“ eingefügt. Dieser Wert ist erforderlich, damit Azure das Subnetz als Gatewaysubnetz erkennt. Passen Sie die automatisch ausgefüllten Werte für **Adressbereich** an Ihre Konfigurationsanforderungen an. Es empfiehlt sich, ein Gatewaysubnetz mit /27 oder größer (/26, /25 usw.) zu erstellen. Wählen Sie dann **OK** aus, um die Werte zu speichern und das Gatewaysubnetz zu erstellen.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Hinzufügen des Subnetzes":::

## <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

1. Wählen Sie im Portal auf der linken Seite **Ressource erstellen** aus, und geben Sie als Suchbegriff „Gateway für virtuelle Netzwerke“ ein. Suchen Sie in der Ausgabe nach **Gateway für virtuelle Netzwerke**, und wählen Sie diesen Eintrag aus. Wählen Sie auf der Seite **Gateway für virtuelle Netzwerke** die Option **Erstellen** aus.
1. Geben Sie auf der Seite **Gateway für virtuelle Netzwerke erstellen** diese Einstellungen ein, bzw. wählen Sie sie aus:

    | Einstellung | Wert |
    | --------| ----- |
    | Abonnement | Vergewissern Sie sich, dass das richtige Abonnement ausgewählt ist. |
    | Ressourcengruppe | Die Ressourcengruppe wird automatisch festgelegt, wenn Sie das virtuelle Netzwerk auswählen. | 
    | name | Benennen Sie Ihr Gateway. Dies ist nicht das Gleiche wie das Benennen eines Gatewaysubnetzes. Hierbei handelt es sich um den Namen des Gatewayobjekts, das Sie erstellen.|
    | Region | Ändern Sie das Feld **Region**, um auf den Standort zu verweisen, an dem sich das virtuelle Netzwerk befindet. Wenn der Standort nicht auf die Region verweist, in der sich Ihr virtuelles Netzwerk befindet, wird das virtuelle Netzwerk nicht in der Dropdownliste „Virtuelles Netzwerk auswählen“ angezeigt. |
    | Gatewaytyp | Wählen Sie **ExpressRoute** aus.|
    | SKU | Wählen Sie in der Dropdownliste die Gateway-SKU aus. |
    | Virtuelles Netzwerk | Wählen Sie *TestVNet* aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**.|
    | Name der öffentlichen IP-Adresse | Geben Sie einen Namen für die öffentliche IP-Adresse ein. |

1. Wählen Sie **Bewerten + erstellen** und dann **Erstellen** aus, um mit der Erstellung des Gateways zu beginnen. Die Einstellungen werden überprüft, und das Gateway wird bereitgestellt. Das vollständige Erstellen des virtuellen Netzwerkgateways kann bis zu 45 Minuten dauern.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Felder der Seite „Gateway für virtuelle Netzwerke erstellen“":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das ExpressRoute-Gateway nicht mehr benötigen, suchen Sie das Gateway in der Ressourcengruppe des virtuellen Netzwerks, und wählen Sie **Löschen** aus. Stellen Sie sicher, dass das Gateway über keine Verbindungen mit einer Leitung verfügt.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Löschen eines Gateways für virtuelle Netzwerke":::

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie das VNET-Gateway erstellt haben, können Sie Ihr VNET mit einer ExpressRoute-Leitung verknüpfen. 

> [!div class="nextstepaction"]
> [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung mithilfe des Portals](expressroute-howto-linkvnet-portal-resource-manager.md)
