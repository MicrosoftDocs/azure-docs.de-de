---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d9825ea41937dc9436fe8b465b48b378e13407c1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177963"
---
1. Klicken Sie im Portal im Abschnitt **Alle Ressourcen** auf **+Hinzufügen**.
2. Geben Sie im Suchfeld der Seite **Alles** den Text **Lokales Netzwerkgateway** ein, und klicken Sie, damit eine Liste mit Ressourcen zurückgegeben wird. Klicken Sie auf **Lokales Netzwerkgateway**, um die Seite zu öffnen. Klicken Sie dann auf **Erstellen**, um die Seite **Lokales Netzwerkgateway erstellen** zu öffnen.

   ![Erstellen eines Gateways für das lokale Netzwerk](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. Geben Sie auf der Seite **Lokales Netzwerkgateway erstellen** die Werte für das lokale Netzwerkgateway ein.

   - **Name:** Geben Sie einen Namen für das lokale Netzwerkgatewayobjekt ein. Verwenden Sie nach Möglichkeit einen intuitiven Namen, z.B. **ClassicVNetLocal** oder **TestVNet1Local**. Dies erleichtert Ihnen im Portal die Identifizierung des lokalen Netzwerkgateways.
   - **IP-Adresse:** Geben Sie eine gültige öffentliche **IP-Adresse** für das VPN-Gerät oder virtuelle Netzwerkgateway ein, mit dem Sie eine Verbindung herstellen möchten.

     * **Wenn dieses lokale Netzwerk einen lokalen Speicherort darstellt:** Geben Sie die öffentliche IP-Adresse des VPN-Geräts an, mit dem Sie eine Verbindung herstellen möchten. Sie darf sich nicht hinter einer NAT befinden und muss für Azure erreichbar sein.
     * **Falls es sich bei diesem lokalen Netzwerk um ein anderes VNet handelt:** Geben Sie die öffentliche IP-Adresse an, die dem Gateway des virtuellen Netzwerks für dieses VNet zugewiesen wurde.
     * **Falls Sie die IP-Adresse noch nicht kennen:** Sie können eine beliebige gültige IP-Platzhalteradresse verwenden und diese Einstellung dann später ändern, bevor Sie die Verbindung herstellen.
   - **Adressraum** bezieht sich auf die Adressbereiche für das Netzwerk, das dieses lokale Netzwerk darstellt. Sie können mehrere Adressraumbereiche hinzufügen. Achten Sie darauf, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke überlappen, mit denen Sie eine Verbindung herstellen.
   - **BGP-Einstellungen konfigurieren:** Nur beim Konfigurieren von BGP verwenden. Lassen Sie sie andernfalls deaktiviert.
   - **Abonnement:** Vergewissern Sie sich, dass das richtige Abonnement angezeigt wird.
   - **Ressourcengruppe:** Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten. Sie können entweder eine neue Ressourcengruppe erstellen oder eine auswählen, die Sie bereits erstellt haben.
   - **Standort:** Wählen Sie den Standort aus, an dem dieses Objekt erstellt wird. Es empfiehlt sich unter Umständen, den gleichen Ort auszuwählen, an dem sich auch Ihr VNet befindet, dies ist aber nicht zwingend erforderlich.

4. Klicken Sie auf **Erstellen** , um das lokale Netzwerkgateway zu erstellen.
