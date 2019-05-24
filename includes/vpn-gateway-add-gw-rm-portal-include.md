---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150258"
---
1. Melden Sie sich beim Azure-Portal an, und wählen Sie **Ressource erstellen** aus. Die Seite **Neu** wird geöffnet.

2. Geben Sie im Feld **Marketplace durchsuchen** die Zeichenfolge *Gateway für virtuelle Netzwerke* ein, und wählen Sie in der zurückgegebenen Liste den Eintrag **Gateway für virtuelle Netzwerke** aus. 

3. Wählen Sie auf der Seite **Gateway für virtuelle Netzwerke** die Option **Erstellen** aus, um die Seite **Gateway für virtuelle Netzwerke erstellen** zu öffnen.

   ![Felder der Seite „Gateway für virtuelle Netzwerke erstellen“](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Felder der Seite „Gateway für virtuelle Netzwerke erstellen“")

4. Geben Sie auf der Seite **Gateway für virtuelle Netzwerke erstellen** die Werte für das Gateway für virtuelle Netzwerke an.

   - **Name:** Geben Sie einen Namen für das zu erstellende Gatewayobjekt ein. Dieser Name unterscheidet sich vom Namen des Gatewaysubnetzes. 

   - **Gatewaytyp:** Wählen Sie für VPN-Gateways die Option **VPN** aus. 

   - **VPN-Typ:** Wählen Sie den für Ihre Konfiguration angegebenen VPN-Typ aus. Bei den meisten Konfigurationen wird ein **routenbasierter** VPN-Typ benötigt.

   - **SKU:** Wählen Sie in der Dropdownliste die Gateway-SKU aus. Welche SKUs in der Dropdownliste aufgeführt werden, hängt vom ausgewählten VPN-Typ ab. Weitere Informationen zu Gateway-SKUs finden Sie unter [Gateway-SKUs](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      Aktivieren Sie **Aktiv/Aktiv-Modus aktivieren** nur, wenn Sie eine Aktiv/Aktiv-Gatewaykonfiguration erstellen. Lassen Sie diese Einstellung andernfalls deaktiviert.
  
   - **Standort:** Unter Umständen müssen Sie scrollen, um die Option **Standort** anzuzeigen. Legen Sie **Standort** auf den Standort fest, an dem sich Ihr virtuelles Netzwerk befindet. Beispiel: **USA, Westen**. Wenn Sie den Standort nicht auf die Region festlegen, in der sich Ihr virtuelles Netzwerk befindet, wird das virtuelle Netzwerk beim Auswählen eines virtuellen Netzwerks nicht in der Dropdownliste angezeigt.

   - **Virtuelles Netzwerk:** Wählen Sie das virtuelle Netzwerk aus, dem Sie dieses Gateway hinzufügen möchten. Wählen Sie **Virtuelles Netzwerk** aus, um die Seite **Virtuelles Netzwerk auswählen** zu öffnen und das VNET auszuwählen. Sollte Ihr VNET nicht angezeigt werden, vergewissern Sie sich, dass das Feld **Standort** auf die Region festgelegt ist, in der sich Ihr virtuelles Netzwerk befindet.

   - **Adressbereich für Gatewaysubnetz:** Diese Einstellung wird nur angezeigt, wenn Sie zuvor kein Gatewaysubnetz für Ihr virtuelles Netzwerk erstellt haben. Falls Sie zuvor ein gültiges Gatewaysubnetz erstellt haben, wird diese Einstellung nicht angezeigt.

   - **Öffentliche IP-Adresse:** Mit dieser Einstellung wird das Objekt für die öffentliche IP-Adresse angegeben, das dem VPN-Gateway zugeordnet ist. Die öffentliche IP-Adresse wird bei der Erstellung des VPN-Gateways diesem Objekt dynamisch zugewiesen. Das VPN Gateway unterstützt derzeit nur die *dynamische* Zuweisung öffentlicher IP-Adressen. Dynamische Zuweisung bedeutet jedoch nicht, dass sich die IP-Adresse ändert, nachdem sie Ihrem VPN-Gateway zugewiesen wurde. Die öffentliche IP-Adresse wird nur geändert, wenn das Gateway gelöscht und neu erstellt wird. Sie ändert sich nicht, wenn die Größe geändert wird, das VPN-Gateway zurückgesetzt wird oder andere interne Wartungs-/Upgradevorgänge für das VPN-Gateway durchgeführt werden.
    
      - Lassen Sie **Neu erstellen** aktiviert.

      - Geben Sie im Textfeld einen Namen für die öffentliche IP-Adresse ein.

   - **BGP-ASN konfigurieren:** Aktivieren Sie diese Einstellung nur, wenn sie für Ihre Konfiguration ausdrücklich erforderlich ist. Sollte diese Einstellung erforderlich sein, lautet die Standard-ASN *65515*. Dieser Wert kann jedoch geändert werden.
     
5. Überprüfen Sie die Einstellungen, und wählen Sie **Erstellen** aus, um mit dem Erstellen des VPN-Gateways zu beginnen. Die Einstellungen werden überprüft, und auf dem Dashboard wird die Kachel **Deploying Virtual network gateway** (Gateway für virtuelles Netzwerk wird bereitgestellt) angezeigt. Die Erstellung eines Gateways kann bis zu 45 Minuten dauern. Unter Umständen müssen Sie die Portalseite aktualisieren, um den Status „Abgeschlossen“ anzuzeigen.

6. Überprüfen Sie nach dem Erstellen des Gateways die zugewiesene IP-Adresse, indem Sie das virtuelle Netzwerk im Portal anzeigen. Das Gateway wird als verbundenes Gerät angezeigt. Wenn Sie auf das verbundene Gerät (Ihr Gateway für virtuelle Netzwerke) klicken, werden weitere Informationen angezeigt.