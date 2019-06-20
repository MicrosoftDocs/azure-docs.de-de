---
title: 'Hinzufügen eines Gateways zu einem Azure-VNET für ExpressRoute: Portal | Microsoft-Dokumentation'
description: In diesem Artikel wird beschrieben, wie Sie einem bereits erstellten Resource Manager-VNet für ExpressRoute ein virtuelles Netzwerkgateway hinzufügen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 68376751a3c673b2d89d028312f992aec40d4dee
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60366025"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurieren eines virtuellen Netzwerkgateways für ExpressRoute mit dem Azure-Portal
> [!div class="op_single_selector"]
> * [Resource Manager – Azure-Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager – PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klassisch – PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video – Azure-Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Dieser Artikel führt Sie durch die Schritte, die zum Hinzufügen eines virtuellen Netzwerkgateways für ein vorhandenes VNet erforderlich sind. Dieser Artikel führt Sie durch die Schritte, die zum Hinzufügen, Ändern der Größe und Entfernen eines virtuellen Netzwerkgateways für ein vorhandenes virtuelles Netzwerk (VNet) erforderlich sind. Die Schritte für diese Konfiguration gelten speziell für VNets, die gemäß dem Resource Manager-Bereitstellungsmodell erstellt wurden, das in einer ExpressRoute-Konfiguration verwendet wird. Weitere Informationen zu virtuellen Netzwerkgateways und Gateway-Konfigurationseinstellungen für ExpressRoute finden Sie unter [Informationen zu Gateways für virtuelle Netzwerke für ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Vorbereitungen

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

## <a name="create-the-gateway-subnet"></a>Erstellen Sie des Gatewaysubnetzes

1. Navigieren Sie im [Portal](https://portal.azure.com) zum virtuellen Resource Manager-Netzwerk, für das Sie ein virtuelles Netzwerkgateway erstellen möchten.
2. Klicken Sie auf dem VNet-Blatt im Abschnitt **Einstellungen** auf **Subnetze**, um das Blatt „Subnetze“ zu erweitern.
3. Klicken Sie auf dem Blatt **Subnetze** auf **+Gatewaysubnetz**, um das Blatt **Subnetz hinzufügen** zu öffnen. 
   
    ![Hinzufügen des Gatewaysubnetzes](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Hinzufügen des Gatewaysubnetzes")


4. Als **Name** für Ihr Subnetz wird automatisch der Wert „GatewaySubnet“ eingefügt. Dieser Wert ist erforderlich, damit Azure das Subnetz als Gatewaysubnetz erkennt. Passen Sie die automatisch ausgefüllten Werte für **Adressbereich** an Ihre Konfigurationsanforderungen an. Es empfiehlt sich, ein Gatewaysubnetz mit /27 oder größer (/ 26, / 25 usw.) zu erstellen. Klicken Sie dann auf **OK**, um die Werte zu speichern und das Gatewaysubnetz zu erstellen.

    ![Hinzufügen des Subnetzes](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Hinzufügen des Subnetzes")

## <a name="create-the-virtual-network-gateway"></a>Erstellen des Gateways für das lokale Netzwerk

1. Klicken Sie im Portal auf der linken Seite auf **+** , und geben Sie für die Suche „Virtuelles Netzwerkgateway“ ein. Suchen Sie in der Ausgabe nach **Virtuelles Netzwerkgateway**, und klicken Sie auf den Eintrag. Klicken Sie am unteren Rand des Blatts **Virtuelles Netzwerkgateway** auf **Erstellen**. Das Blatt **Virtuelles Netzwerkgateway erstellen** wird geöffnet.
2. Geben Sie auf dem Blatt **Virtuelles Netzwerkgateway erstellen** die Werte für das virtuelle Netzwerkgateway an.

    ![Felder des Blatts „Virtuelles Netzwerkgateway erstellen“](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Felder des Blatts „Virtuelles Netzwerkgateway erstellen“")
3. **Name**: Benennen Sie Ihr Gateway. Dies ist nicht das Gleiche wie das Benennen eines Gatewaysubnetzes. Hierbei handelt es sich um den Namen des Gatewayobjekts, das Sie erstellen.
4. **Gatewaytyp**: Wählen Sie **ExpressRoute** aus.
5. **SKU**: Wählen Sie in der Dropdownliste die Gateway-SKU aus.
6. **Standort**: Passen Sie das Feld **Standort** an, um auf den Standort zu verweisen, an dem sich das virtuelle Netzwerk befindet. Wenn der Standort nicht auf die Region verweist, in der sich Ihr virtuelles Netzwerk befindet, wird das virtuelle Netzwerk nicht in der Dropdownliste „Virtuelles Netzwerk auswählen“ angezeigt.
7. Wählen Sie das virtuelle Netzwerk aus, dem Sie dieses Gateway hinzufügen möchten. Klicken Sie auf **Virtuelles Netzwerk**, um das Blatt **Virtuelles Netzwerk auswählen** zu öffnen. Wählen Sie das VNet aus. Sollte Ihr VNet nicht angezeigt werden, vergewissern Sie sich, dass das Feld **Speicherort** auf die Region verweist, in dem sich Ihr virtuelles Netzwerk befindet.
9. Wählen Sie eine öffentliche IP-Adresse aus. Klicken Sie auf **Öffentliche IP-Adresse**, um das Blatt **Öffentliche IP-Adresse wählen** zu öffnen. Klicken Sie auf **+ Neu erstellen**, um das Blatt **Öffentliche IP-Adresse erstellen** zu öffnen. Geben Sie einen Namen für die öffentliche IP-Adresse ein. Auf diesem Blatt wird ein Objekt für eine öffentliche IP-Adresse erstellt, dem eine öffentliche IP-Adresse dynamisch zugewiesen wird. Klicken Sie zum Speichern der Änderungen an diesem Blatt auf **OK**.
10. **Abonnement**: Vergewissern Sie sich, dass das richtige Abonnement ausgewählt ist.
11. **Ressourcengruppe**: Diese Einstellung wird auf der Grundlage des ausgewählten virtuellen Netzwerks festgelegt.
12. Passen Sie den **Standort** nach Angabe der vorherigen Einstellungen nicht mehr an.
13. Überprüfen Sie die Einstellungen. Sie können die Option **An Dashboard anheften** unten auf dem Blatt auswählen, wenn das Gateway auf dem Dashboard angezeigt werden soll.
14. Klicken Sie auf **Erstellen** , um das Gateway zu erstellen. Die Einstellungen werden überprüft, und das Gateway wird bereitgestellt. Das vollständige Erstellen des virtuellen Netzwerkgateways kann bis zu 45 Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie das VNet-Gateway erstellt haben, können Sie Ihr VNet mit einer ExpressRoute-Verbindung verknüpfen. Weitere Informationen finden Sie unter [Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-portal-resource-manager.md).
