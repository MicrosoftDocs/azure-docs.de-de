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
ms.openlocfilehash: c538c510eeafacd1596fdeb1b4593919e6236cf0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487092"
---
1. Geben Sie im [Azure-Portal](https://portal.azure.com) unter **Search resources, services, and docs (G+/)** (Nach Ressourcen, Diensten und Dokumentation suchen (G+/)) **Lokales Netzwerkgateway** ein. Suchen Sie unter **Marketplace** in den Suchergebnissen nach **Lokales Netzwerkgateway** , und wählen Sie es aus. Die Seite **Lokales Netzwerkgateway erstellen** wird geöffnet.
1. Geben Sie auf der Seite **Lokales Netzwerkgateway erstellen** die Werte für das lokale Netzwerkgateway ein.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-ip-empty/create-ip.png" alt-text="Lokales Netzwerkgateway mit IP-Adresse erstellen":::

   * **Name:** Geben Sie einen Namen für das lokale Netzwerkgatewayobjekt ein.
   * **Endpunkt:** Wählen Sie den Endpunkttyp für das lokale VPN-Gerät aus: **IP-Adresse** oder **FQDN (vollqualifizierter Domänen Name)** .
      * **IP-Adresse** : Wenn Sie über eine statische öffentliche IP-Adresse verfügen, die Ihrem VPN-Gerät von Ihrem Internetdienstanbieter zugeordnet wurde, wählen Sie die Option „IP-Adresse“ aus, und geben Sie die IP-Adresse wie im Beispiel gezeigt ein. Dies ist die öffentliche IP-Adresse des VPN-Geräts, mit dem Azure VPN Gateway eine Verbindung herstellen soll. Falls Sie die IP-Adresse gerade nicht zur Hand haben, können Sie die Werte aus dem Beispiel verwenden. In diesem Fall müssen Sie allerdings später die Platzhalter-IP-Adresse durch die öffentliche IP-Adresse Ihres VPN-Geräts ersetzen. Andernfalls kann Azure keine Verbindung herstellen.
   * **Adressraum** bezieht sich auf die Adressbereiche für das Netzwerk, das dieses lokale Netzwerk darstellt. Sie können mehrere Adressraumbereiche hinzufügen. Achten Sie darauf, dass sich die hier angegebenen Bereiche nicht mit den Bereichen anderer Netzwerke überschneiden, mit denen Sie eine Verbindung herstellen möchten. Azure leitet den Adressbereich, den Sie angeben, an die lokale IP-Adresse des VPN-Geräts weiter. *Verwenden Sie hier anstelle der Werte aus dem Beispiel Ihre eigenen Werte, wenn Sie eine Verbindung mit Ihrem lokalen Standort herstellen möchten.*
   * **BGP-Einstellungen konfigurieren:** Nur beim Konfigurieren von BGP verwenden. Lassen Sie sie andernfalls deaktiviert.
   * **Abonnement:** Vergewissern Sie sich, dass das richtige Abonnement angezeigt wird.
   * **Ressourcengruppe:** Wählen Sie die Ressourcengruppe aus, die Sie verwenden möchten. Sie können entweder eine neue Ressourcengruppe erstellen oder eine auswählen, die Sie bereits erstellt haben.
   * **Standort:** Der Speicherort ist der gleiche wie **Region** in anderen Einstellungen. Wählen Sie den Standort aus, an dem dieses Objekt erstellt wird. Es empfiehlt sich unter Umständen, den gleichen Ort auszuwählen, an dem sich auch Ihr VNet befindet, dies ist aber nicht zwingend erforderlich.

1. Wenn Sie die Werte angegeben haben, klicken Sie unten auf der Seite auf die Schaltfläche **Erstellen** , um das lokale Netzwerkgateway zu erstellen.
