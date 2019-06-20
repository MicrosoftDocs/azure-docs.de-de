---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/24/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35f987f26ce47c19e3d5eb1ca5d2bb32d0c7ad1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177948"
---
1. Klicken Sie im Portal auf der linken Seite auf **+** , und geben Sie „Gateway für virtuelle Netzwerke“ für die Suche ein. Suchen Sie unter **Ergebnisse** nach **Gateway für virtuelle Netzwerke**, und klicken Sie auf den Eintrag.
2. Klicken Sie unten auf der Seite „Gateway für virtuelle Netzwerke“ auf **Erstellen**. Die Seite **Gateway für virtuelle Netzwerke erstellen** wird geöffnet.
3. Geben Sie auf der Seite **Gateway für virtuelle Netzwerke erstellen** die Werte für das Gateway für virtuelle Netzwerke an.

    ![Felder der Seite „Gateway für virtuelle Netzwerke erstellen“](./media/vpn-gateway-add-gateway-portal-include/create-gateway.png "Neues Gateway")

   - **Name**: Benennen Sie Ihr Gateway. Dies ist nicht das Gleiche wie das Benennen eines Gatewaysubnetzes. Hierbei handelt es sich um den Namen des Gatewayobjekts, das Sie erstellen.
   - **Gatewaytyp**: Wählen Sie **VPN** aus. Bei VPN-Gateways wird ein virtuelles Netzwerkgateway vom Typ **VPN** verwendet. 
   - **VPN-Typ:** Wählen Sie den für Ihre Konfiguration angegebenen VPN-Typ aus. Bei den meisten Konfigurationen wird ein routenbasierter VPN-Typ benötigt.
   - **SKU**: Wählen Sie in der Dropdownliste die Gateway-SKU aus. Welche SKUs in der Dropdownliste aufgeführt werden, hängt vom ausgewählten VPN-Typ ab. Weitere Informationen zu Gateway-SKUs finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

     Aktivieren Sie **Aktiv/Aktiv-Modus aktivieren** nur, wenn Sie eine Aktiv/Aktiv-Gatewaykonfiguration erstellen. Lassen Sie diese Einstellung andernfalls deaktiviert.
   - **Standort**: Unter Umständen müssen Sie scrollen, um „Standort“ anzuzeigen. Passen Sie das Feld **Standort** an, um auf den Standort zu verweisen, an dem sich das virtuelle Netzwerk befindet. Beispiel: USA, Westen Wenn der Standort nicht auf die Region verweist, in der sich Ihr virtuelles Netzwerk befindet, wird das virtuelle Netzwerk nicht in der Dropdownliste angezeigt, wenn Sie im nächsten Schritt ein virtuelles Netzwerk auswählen.
   - **Virtuelles Netzwerk:** Wählen Sie das virtuelle Netzwerk aus, dem Sie dieses Gateway hinzufügen möchten. Klicken Sie auf **Virtuelles Netzwerk**, um die Seite „Virtuelles Netzwerk auswählen“ zu öffnen. Wählen Sie das VNet aus. Sollte Ihr VNet nicht angezeigt werden, vergewissern Sie sich, dass das Feld „Speicherort“ auf die Region verweist, in der sich Ihr virtuelles Netzwerk befindet.
   - **Adressbereich für Gatewaysubnetz**: Diese Einstellung wird nur angezeigt, wenn Sie zuvor kein Gatewaysubnetz für Ihr virtuelles Netzwerk erstellt haben. Falls Sie zuvor ein gültiges Gatewaysubnetz erstellt haben, wird diese Einstellung nicht angezeigt.
   - **Öffentliche IP-Adresse:** Mit dieser Einstellung wird das Objekt für die öffentliche IP-Adresse angegeben, das dem VPN-Gateway zugeordnet wird. Die öffentliche IP-Adresse wird bei der Erstellung des VPN-Gateways diesem Objekt dynamisch zugewiesen. VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Das bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse ändert sich nur, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.

     - Lassen Sie **Neu erstellen** aktiviert.
     - Geben Sie im Textfeld **Name** einen Namen für die öffentliche IP-Adresse ein.

4. Aktivieren Sie **BGP-ASN konfigurieren** nur, wenn diese Einstellung für Ihre Konfiguration speziell erforderlich ist. Sollte diese Einstellung erforderlich sein, lautet die Standard-ASN 65515. (Dieser Wert kann jedoch geändert werden.)
5. Überprüfen Sie die Einstellungen. Sie können unten auf der Seite auf **An Dashboard anheften** klicken, wenn das Gateway auf dem Dashboard angezeigt werden soll. 
6. Klicken Sie auf **Erstellen**, um das VPN-Gateway zu erstellen. Die Einstellungen werden überprüft, und auf dem Dashboard wird die Kachel mit dem Hinweis angezeigt, dass das Gateway des virtuellen Netzwerks bereitgestellt wird. Die Erstellung eines Gateways kann bis zu 45 Minuten dauern. Unter Umständen müssen Sie die Portalseite aktualisieren, um den Status „Abgeschlossen“ anzuzeigen.
   Gateway.

Zeigen Sie nach der Erstellung des Gateways unter dem virtuellen Netzwerk im Portal die zugewiesene IP-Adresse an. Das Gateway wird als verbundenes Gerät angezeigt. Sie können auf das verbundene Gerät (Ihr virtuelles Netzwerkgateway) klicken, um weitere Informationen anzuzeigen.