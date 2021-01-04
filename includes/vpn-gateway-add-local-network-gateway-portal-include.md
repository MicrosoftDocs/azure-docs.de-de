---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5358bbbca716f5152a943c90cb7a5f735ae12047
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "92479617"
---
1. Geben Sie im [Azure-Portal](https://portal.azure.com) unter **Search resources, services, and docs (G+/)** (Nach Ressourcen, Diensten und Dokumenten suchen (G+/)) **Loakles Netzwerkgateway** ein. Suchen Sie unter **Marketplace** in den Suchergebnissen nach **Lokales Netzwerkgateway**, und wählen Sie es aus. Die Seite **Lokales Netzwerkgateway erstellen** wird geöffnet.
1. Geben Sie auf der Seite **Lokales Netzwerkgateway erstellen** die Werte für das lokale Netzwerkgateway ein.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-ip.png" alt-text="Lokales Netzwerkgateway mit IP-Adresse erstellen":::

   * **Name:** Geben Sie einen Namen für das lokale Netzwerkgatewayobjekt ein.
   * **Endpunkt:** Wählen Sie den Endpunkttyp für das lokale VPN-Gerät aus: **IP-Adresse** oder **FQDN (vollqualifizierter Domänen Name)** .
      * **IP-Adresse**: Wenn Sie über eine statische öffentliche IP-Adresse verfügen, die Ihrem VPN-Gerät von Ihrem Internetdienstanbieter zugeordnet wurde, wählen Sie die Option „IP-Adresse“ aus, und geben Sie die IP-Adresse wie im Beispiel gezeigt ein. Dies ist die öffentliche IP-Adresse des VPN-Geräts, mit dem Azure VPN Gateway eine Verbindung herstellen soll. Falls Sie die IP-Adresse gerade nicht zur Hand haben, können Sie die Werte aus dem Beispiel verwenden. In diesem Fall müssen Sie allerdings später die Platzhalter-IP-Adresse durch die öffentliche IP-Adresse Ihres VPN-Geräts ersetzen. Andernfalls kann Azure keine Verbindung herstellen.
      * **FQDN:** Wenn Sie über eine dynamische öffentliche IP-Adresse verfügen, die sich nach einem bestimmten Zeitraum ändern könnte, der normalerweise von Ihrem Internetdienstanbieter festgelegt wird, können Sie einen konstanten DNS-Namen mit einem dynamischen DNS-Dienst verwenden, um auf die aktuelle öffentliche IP-Adresse Ihres VPN-Geräts zu verweisen. Azure VPN Gateway löst den FQDN dann auf, um die öffentliche IP-Adresse für die Verbindungsherstellung zu ermitteln. 
   * **Adressraum** bezieht sich auf die Adressbereiche für das Netzwerk, das dieses lokale Netzwerk darstellt. Sie können mehrere Adressraumbereiche hinzufügen. Achten Sie darauf, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke überschneiden, mit denen Sie eine Verbindung herstellen möchten. Azure leitet den Adressbereich, den Sie angeben, an die lokale IP-Adresse des VPN-Geräts weiter. *Verwenden Sie hier anstelle der Werte aus dem Beispiel Ihre eigenen Werte, wenn Sie eine Verbindung mit Ihrem lokalen Standort herstellen möchten.*
   * **BGP-Einstellungen konfigurieren:** Nur beim Konfigurieren von BGP verwenden. Lassen Sie sie andernfalls deaktiviert.
   * **Abonnement:** Vergewissern Sie sich, dass das richtige Abonnement angezeigt wird.
   * **Ressourcengruppe:** Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten. Sie können entweder eine neue Ressourcengruppe erstellen oder eine auswählen, die Sie bereits erstellt haben.
   * **Standort:** Der Speicherort ist der gleiche wie **Region** in anderen Einstellungen. Wählen Sie den Standort aus, an dem dieses Objekt erstellt wird. Es empfiehlt sich unter Umständen, den gleichen Ort auszuwählen, an dem sich auch Ihr VNet befindet, dies ist aber nicht zwingend erforderlich.

   > [!NOTE]
   >
   > * Azure VPN unterstützt nur eine IPv4-Adresse für jeden FQDN. Wenn der Domänenname in mehrere IP-Adressen aufgelöst wird, verwendet Azure VPN Gateway die erste von den DNS-Servern zurückgegebene IP-Adresse. Um die Ungewissheit zu beseitigen, wird empfohlen, dass Ihr FQDN immer in eine einzelne IPv4-Adresse aufgelöst wird. IPv6 wird nicht unterstützt.
   > * Azure VPN Gateway verwaltet einen DNS-Cache, der alle fünf Minuten aktualisiert wird. Das Gateway versucht, die vollqualifizierten Domänennamen nur für getrennte Tunnel aufzulösen. Durch das Zurücksetzen des Gateways wird auch die Auflösung eines FQDN ausgelöst.
   >

1. Wenn Sie die Werte angegeben haben, klicken Sie unten auf der Seite auf die Schaltfläche **Erstellen**, um das lokale Netzwerkgateway zu erstellen.
