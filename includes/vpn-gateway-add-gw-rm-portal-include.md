---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6bc8b6cc18f4a4bc41cabf7f8fefe78a5aaf7827
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94427846"
---
1. Geben Sie im [Azure-Portal](https://portal.azure.com) unter **Nach Ressourcen, Diensten und Dokumenten suchen (G+/)** als Suchbegriff **VNET-Gateway** ein. Suchen Sie in den Suchergebnissen nach **VNET-Gateway**, und wählen Sie diesen Eintrag aus.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/search.png" alt-text="Suchen (Feld)" lightbox="./media/vpn-gateway-add-gw-rm-portal-include/search-expand.png":::

1. Wählen Sie auf der Seite **VNET-Gateway** die Option **+ Hinzufügen** aus. Die Seite **Gateway für virtuelle Netzwerke erstellen** wird geöffnet.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/add.png" alt-text="Seite „VNET-Gateway“":::
1. Geben Sie auf der Registerkarte **Grundlagen** die Werte für Ihr Gateway für virtuelle Netzwerke an.

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway.png" alt-text="Gatewayfelder":::

   :::image type="content" source="./media/vpn-gateway-add-gw-rm-portal-include/gateway-vnet.png" alt-text="Weitere Gatewayfelder":::

   * **Abonnement**: Wählen Sie in der Dropdownliste das zu verwendende Abonnement aus.
   * **Ressourcengruppe**: Diese Einstellung wird automatisch angegeben, wenn Sie auf dieser Seite Ihr virtuelles Netzwerk auswählen.

   **Instanzendetails**

   * **Name**: Benennen Sie Ihr Gateway. Das Benennen eines Gateways ist nicht das Gleiche wie das Benennen eines Gatewaysubnetzes. Hierbei handelt es sich um den Namen des Gatewayobjekts, das Sie erstellen.
   * **Region**: Wählen Sie die Region aus, in der Sie diese Ressource erstellen möchten. Die Region für das Gateway muss der des virtuellen Netzwerks entsprechen.
   * **Gatewaytyp**: Wählen Sie **VPN** aus. Bei VPN-Gateways wird ein virtuelles Netzwerkgateway vom Typ **VPN** verwendet.
   * **VPN-Typ:** Wählen Sie den für Ihre Konfiguration angegebenen VPN-Typ aus. Bei den meisten Konfigurationen wird ein routenbasierter VPN-Typ benötigt.
   * **SKU**: Wählen Sie in der Dropdownliste die Gateway-SKU aus. Welche SKUs in der Dropdownliste aufgeführt werden, hängt vom ausgewählten VPN-Typ ab. Weitere Informationen zu Gateway-SKUs finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).
   * **Generation:** Informationen zur Generierung von VPN-Gateways finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku).
   * **Virtuelles Netzwerk:** Wählen Sie in der Dropdownliste das virtuelle Netzwerk aus, dem Sie dieses Gateway hinzufügen möchten.
   * **Adressbereich für Gatewaysubnetz**: Dieses Feld wird nur angezeigt, wenn Ihr VNet nicht über ein Gatewaysubnetz verfügt. Legen Sie den Bereich möglichst als /27 oder größer (/26, /25 usw.) fest. Es empfiehlt sich nicht, einen Bereich zu erstellen, der kleiner als /28 ist. Wenn Sie bereits über ein Gatewaysubnetz verfügen, können Sie die Gatewaysubnetzdetails anzeigen, indem Sie zu Ihrem virtuellen Netzwerk navigieren. Klicken Sie auf **Subnetze**, um den Bereich anzuzeigen. Wenn Sie den Bereich ändern möchten, können Sie das GatewaySubnet löschen und neu erstellen.

   **Öffentliche IP-Adresse**

   Mit dieser Einstellung wird das Objekt für die öffentliche IP-Adresse angegeben, das dem VPN-Gateway zugeordnet wird. Die öffentliche IP-Adresse wird bei der Erstellung des VPN-Gateways diesem Objekt dynamisch zugewiesen. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

     * **Öffentliche IP-Adresse:** Lassen Sie **Neu erstellen** aktiviert.
     * **Öffentliche IP-Adresse**: Geben Sie im Textfeld einen Namen für die öffentliche IP-Adressinstanz ein.
     * **Zuweisung**: VPN Gateway unterstützt nur die dynamische Zuweisung.
     * **Aktiv/Aktiv-Modus aktivieren**: Aktivieren Sie **Aktiv/Aktiv-Modus aktivieren** nur, wenn Sie eine Aktiv/Aktiv-Gatewaykonfiguration erstellen. Lassen Sie diese Einstellung andernfalls **deaktiviert**.
     * Belassen Sie für **BGP konfigurieren** den Wert **Deaktiviert**, es sei denn, Ihre Konfiguration erfordert diese Einstellung ausdrücklich. Sollte diese Einstellung erforderlich sein, lautet die Standard-ASN 65515. (Dieser Wert kann jedoch geändert werden.)
1. Wählen Sie zum Ausführen der Validierung **Bewerten + erstellen** aus.
1. Wählen Sie nach Abschluss der Validierung **Erstellen** aus, um das VPN-Gateway bereitzustellen.

Die gesamte Erstellung und Bereitstellung eines Gateways kann bis zu 45 Minuten dauern. Der Bereitstellungsstatus wird auf der Übersichtsseite für Ihr Gateway angezeigt. Nach der Erstellung des Gateways können Sie die zugewiesene IP-Adresse unter dem Virtual Network im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt.
