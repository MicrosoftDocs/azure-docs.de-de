---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/19/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03a4005da7794a989166f914e4ba4d03f93ae8b9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177947"
---
1. Klicken Sie im Portal auf **Ressource erstellen**.
2. Geben Sie im Suchfeld den Text **Lokales Netzwerkgateway** ein, und drücken Sie die **EINGABETASTE**. Daraufhin wird eine Liste mit Ergebnissen zurückgegeben. Klicken Sie auf **Lokales Netzwerkgateway** und anschließend auf die Schaltfläche **Erstellen**, um die Seite **Lokales Netzwerkgateway erstellen** zu öffnen.

   ![Erstellen des lokalen Netzwerkgateways](./media/vpn-gateway-add-local-network-gateway-portal-include/create-local-network-gateway.png "Erstellen des lokalen Netzwerkgateways")

3. Geben Sie auf der Seite **Lokales Netzwerkgateway erstellen** die Werte für das lokale Netzwerkgateway ein.

   - **Name:** Geben Sie einen Namen für das lokale Netzwerkgatewayobjekt ein.
   - **IP-Adresse:** Dies ist die öffentliche IP-Adresse des VPN-Geräts, mit dem Azure eine Verbindung herstellen soll. Geben Sie eine gültige öffentliche IP-Adresse ein. Falls Sie die IP-Adresse gerade nicht zur Hand haben, können Sie die Werte aus dem Beispiel verwenden. In diesem Fall müssen Sie allerdings später die Platzhalter-IP-Adresse durch die öffentliche IP-Adresse Ihres VPN-Geräts ersetzen. Andernfalls kann Azure keine Verbindung herstellen.
   - **Adressraum** bezieht sich auf die Adressbereiche für das Netzwerk, das dieses lokale Netzwerk darstellt. Sie können mehrere Adressraumbereiche hinzufügen. Achten Sie darauf, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke überschneiden, mit denen Sie eine Verbindung herstellen möchten. Azure leitet den Adressbereich, den Sie angeben, an die lokale IP-Adresse des VPN-Geräts weiter. *Verwenden Sie hier anstelle der Werte aus dem Beispiel Ihre eigenen Werte, wenn Sie eine Verbindung mit Ihrem lokalen Standort herstellen möchten.*
   - **BGP-Einstellungen konfigurieren:** Nur beim Konfigurieren von BGP verwenden. Lassen Sie sie andernfalls deaktiviert.
   - **Abonnement:** Vergewissern Sie sich, dass das richtige Abonnement angezeigt wird.
   - **Ressourcengruppe:** Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten. Sie können entweder eine neue Ressourcengruppe erstellen oder eine auswählen, die Sie bereits erstellt haben.
   - **Standort:** Wählen Sie den Standort aus, an dem dieses Objekt erstellt wird. Es empfiehlt sich unter Umständen, den gleichen Ort auszuwählen, an dem sich auch Ihr VNet befindet, dies ist aber nicht zwingend erforderlich.

4. Wenn Sie die Werte angegeben haben, klicken Sie unten auf der Seite auf die Schaltfläche **Erstellen**, um das lokale Netzwerkgateway zu erstellen.
