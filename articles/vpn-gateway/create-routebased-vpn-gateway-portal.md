---
title: 'Erstellen eines routenbasierten VPN-Gateways: Azure-Portal | Microsoft-Dokumentation'
description: Erstellen eines routenbasierten VPN-Gateways mit dem Azure-Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: ddc42023bae3403e7778327a40316462c85222c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390023"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Erstellen eines routenbasierten VPN-Gateways mit dem Azure-Portal

In diesem Artikel erfahren Sie, wie Sie schnell ein routenbasiertes Azure-VPN-Gateway mit dem Azure-Portal erstellen.  Ein VPN-Gateway wird beim Herstellen einer VPN-Verbindung mit Ihrem lokalen Netzwerk verwendet. Sie können Verbindungen mit VNets auch mit einem VPN-Gateway herstellen. 

Mit den Schritten in diesem Artikel werden ein VNet, ein Subnetz, ein Gatewaysubnetz und ein routenbasiertes VPN-Gateway (Gateway des virtuellen Netzwerks) erstellt. Wenn die Gatewayerstellung abgeschlossen ist, können Sie Verbindungen herstellen. Diese Schritte erfordern ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="vnet"></a>Erstellen eines virtuellen Netzwerks

1. Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.
2. Klicken Sie auf **Ressource erstellen**. Geben Sie im Feld **Marketplace durchsuchen** die Zeichenfolge „Virtuelles Netzwerk“ ein. Klicken Sie in der zurückgegebenen Liste auf **Virtuelles Netzwerk**, um die Seite **Virtual Network** zu öffnen.
3. Wählen Sie unten auf der Seite „Virtuelles Netzwerk“ in der Liste **Bereitstellungsmodell auswählen** die Option **Resource Manager** in der Dropdownliste aus, und klicken Sie dann auf **Erstellen**. Die Seite **Virtuelles Netzwerk erstellen** wird geöffnet.
4. Konfigurieren Sie auf der Seite **Virtuelles Netzwerk erstellen** die VNET-Einstellungen. Beim Ausfüllen der Felder ändert sich das rote Ausrufezeichen in ein grünes Häkchen, sofern die in das Feld eingegebenen Zeichen zulässig sind. Verwenden Sie die folgenden Werte:

   - **Name**: TestVNet1
   - **Adressraum:** 10.1.0.0/16
   - **Abonnement**: Vergewissern Sie sich, dass das aufgelistete Abonnement das Abonnement ist, das Sie verwenden möchten. Das Abonnement kann über die Dropdownliste geändert werden.
   - **Ressourcengruppe**: TestRG1
   - **Standort**: USA (Ost)
   - **Subnetz**: Front-End
   - **Adressbereich**: 10.1.0.0/24

   ![Seite „Virtuelles Netzwerk erstellen“](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "Seite „Virtuelles Netzwerk erstellen“")
5. Wählen Sie nach der Eingabe der Werte **An Dashboard anheften**, damit Sie Ihr VNet leicht auf dem Dashboard finden, und klicken Sie dann auf **Erstellen**. Nach dem Klicken auf **Erstellen** wird auf dem Dashboard eine Kachel angezeigt, auf der der Status des VNet angegeben wird. Die Kachel verändert sich, wenn das VNet erstellt wird.

## <a name="gwsubnet"></a>Hinzufügen eines Gatewaysubnetzes

Das Gatewaysubnetz enthält die reservierten IP-Adressen, die von den Diensten des virtuellen Netzwerkgateways verwendet werden. Erstellen Sie ein Gatewaysubnetz.

1. Navigieren Sie im Portal zum virtuellen Netzwerk, für das Sie ein Gateway des virtuellen Netzwerks erstellen möchten.
2. Klicken Sie auf der Seite „Virtuelles Netzwerk“ auf **Subnetze**, um die Seite **VNet1 – Subnetze** zu erweitern.
3. Klicken Sie oben auf der Seite auf **+Gatewaysubnetz**, um die Seite **Subnetz hinzufügen** zu öffnen.

   ![Hinzufügen des Gatewaysubnetzes](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "Hinzufügen des Gatewaysubnetzes")
4. Als **Name** für Ihr Subnetz wird automatisch der erforderliche Wert „GatewaySubnet“ eingefügt. Passen Sie die automatisch ausgefüllten Werte für **Adressbereich** so an, dass sie folgenden Werten entsprechen:

   **Adressbereich (CIDR-Block)** : 10.1.255.0/27

   ![Hinzufügen des Gatewaysubnetzes](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "Hinzufügen des Gatewaysubnetzes")
5. Klicken Sie unten auf der Seite auf **OK**, um das Gatewaysubnetz zu erstellen.

## <a name="gwvalues"></a>Konfigurieren der Gatewayeinstellungen

1. Klicken Sie links auf der Portalseite auf **+ Ressource erstellen**, und geben Sie „Gateway für virtuelle Netzwerke“ im Suchfeld ein. Drücken Sie anschließend die **EINGABETASTE**. Suchen Sie unter **Ergebnisse** nach **Gateway für virtuelle Netzwerke**, und klicken Sie auf den Eintrag.
2. Klicken Sie unten auf der Seite „Gateway für virtuelle Netzwerke“ auf **Erstellen**, um die Seite **Gateway für virtuelle Netzwerke erstellen** zu öffnen.
3. Geben Sie auf der Seite **Gateway für virtuelle Netzwerke erstellen** die Werte für das Gateway für virtuelle Netzwerke an.

   - **Name**: Vnet1GW
   - **Gatewaytyp**: VPN 
   - **VPN-Typ:** routenbasiert
   - **SKU**: VpnGw1
   - **Standort**: USA (Ost)
   - **Virtuelles Netzwerk:** Klicken Sie auf **Virtuelles Netzwerk/Virtuelles Netzwerk auswählen**, um die Seite **Virtuelles Netzwerk auswählen** zu öffnen. Wählen Sie **VNet1** aus.
   - **Öffentliche IP-Adresse:** Mit dieser Einstellung wird das Objekt für die öffentliche IP-Adresse angegeben, das dem VPN-Gateway zugeordnet wird. Die öffentliche IP-Adresse wird bei der Erstellung des VPN-Gateways diesem Objekt dynamisch zugewiesen. VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

     - Lassen Sie **Neu erstellen** aktiviert.
     - Geben Sie im Textfeld **Name** einen Namen für die öffentliche IP-Adresse ein. Verwenden Sie für diese Übung **VNet1GWIP**.<br>

     ![Gatewayeinstellungen konfigurieren](./media/create-routebased-vpn-gateway-portal/gw.png "Gatewayeinstellungen konfigurieren")

## <a name="creategw"></a>Erstellen des VPN-Gateways

1. Überprüfen Sie die Einstellungen auf der Seite **Gateway für virtuelle Netzwerke erstellen**. Passen Sie ggf. die Werte an.
2. Klicken Sie am unteren Rand der Seite auf **Erstellen**.

   Nach dem Klicken auf **Erstellen** werden die Einstellungen überprüft, und auf dem Dashboard wird die Kachel **Gateway des virtuellen Netzwerks wird bereitgestellt** angezeigt. Die Erstellung eines VPN-Gateways kann bis zu 45 Minuten dauern. Unter Umständen müssen Sie die Portalseite aktualisieren, um den Status „Abgeschlossen“ anzuzeigen.

## <a name="viewgw"></a>Anzeigen des VPN-Gateways

1. Nachdem das Gateway erstellt wurde, navigieren Sie im Portal zu VNet1. Das VPN-Gateway wird auf der Seite „Übersicht“ als verbundenes Gerät angezeigt.

   ![Verbundene Geräte](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Verbundene Geräte")

2. Klicken Sie in der Geräteliste auf **VNet1GW**, um weitere Informationen anzuzeigen.

   ![VPN-Gateway anzeigen](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN-Gateway anzeigen")

## <a name="next-steps"></a>Nächste Schritte

Sobald das Gateway fertig gestellt ist, können Sie eine Verbindung Ihres virtuellen Netzwerks mit einem anderen VNet herstellen. Alternativ können Sie eine Verbindung zwischen dem virtuellen Netzwerk und einem lokalen Standort erstellen.

> [!div class="nextstepaction"]
> [Herstellen einer Site-to-Site-Verbindung](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Herstellen einer Point-to-Site-Verbindung](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Herstellen einer Verbindung mit einem anderen VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
