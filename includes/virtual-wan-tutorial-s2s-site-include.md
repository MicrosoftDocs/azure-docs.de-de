---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627703"
---
1. Klicken Sie auf der Portalseite für Ihr virtuelles WAN im Abschnitt **Konnektivität** auf **VPN-Standorte**, um die Seite mit VPN-Standorten zu öffnen.
2. Klicken Sie auf der Seite **VPN sites** (VPN-Standorte) auf **+Create site** (+ Standort erstellen).

   ![Screenshot: Site-to-Site-Fenster mit geöffneten Bereich „VPN-Standort erstellen“](./media/virtual-wan-tutorial-site-include/basics.png "Grundlagen")
3. Füllen Sie auf der Seite **VPN-Standort erstellen** auf der Registerkarte **Grundlagen** die folgenden Felder aus:

    * **Region**: Zuvor als Standort bezeichnet. Dies ist der Standort, an dem Sie diese Standortressource erstellen möchten.
    * **Name**: Der Name, den Sie Ihrer lokalen Site geben möchten.
    * **Gerätehersteller**: Der Name des VPN-Geräteherstellers (z. B. Citrix, Cisco, Barracuda). Das Hinzufügen des Geräteherstellers kann dem Azure-Team helfen, Ihre Umgebung besser zu verstehen, um in Zukunft weitere Optimierungsmöglichkeiten hinzuzufügen oder Sie bei der Problembehandlung zu unterstützen.
    * **Border Gateway Protocol**: Mit der Einstellung **Aktivieren** werden alle Verbindungen vom Standort für BGP aktiviert. Sie richten die BGP-Informationen für jede Verbindung vom VPN-Standort im Abschnitt für Verbindungen ein. Die Vorgehensweise zum Konfigurieren von BGP für eine Virtual WAN-Instanz ist dieselbe wie beim Konfigurieren von BGP für ein VPN mit einem Azure-Gateway für virtuelle Netzwerke. Ihre lokale BGP-Peeradresse darf nicht der öffentlichen IP-Adresse Ihres VPN-Geräts oder dem VNET-Adressraum des VPN-Standorts entsprechen. Verwenden Sie als BGP-Peer-IP-Adresse eine andere IP-Adresse für das VPN-Gerät. Dabei kann es sich um eine Adresse handeln, die der Loopback-Schnittstelle des Geräts zugewiesen ist. Geben Sie diese Adresse im entsprechenden VPN-Standort an, der den Standort darstellt. Informationen zu den Voraussetzungen für BGP finden Sie unter [Übersicht über BGP mit Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Sie können eine VPN-Verbindung jederzeit bearbeiten, um die zugehörigen BGP-Parameter zu aktualisieren (Peering-IP-Adresse für die Verbindung und Adressraumnummer), nachdem die BGP-Einstellung für den VPN-Standort aktiviert wurde.
    * **Privater Adressraum**: Dies ist der IP-Adressraum Ihres lokalen Standorts. Der Datenverkehr, der für diesen Adressraum bestimmt ist, wird an Ihre lokale Site geleitet. Dies ist erforderlich, wenn BGP nicht für den Standort aktiviert ist.
    
      >[!NOTE]
      >Wenn Sie den Adressraum nach dem Erstellen des Standorts bearbeiten (indem Sie z. B. einen weiteren Adressraum hinzufügen), kann es 8-10 Minuten dauern, bis die tatsächlichen Routen aktualisiert sind, während die Komponenten neu erstellt werden.
      >
    * **Hubs**: Der Hub, mit dem Sie Ihren Standort verbinden möchten. Ein Standort kann nur mit Hubs verbunden werden, die über ein VPN-Gateway verfügen. Wenn ein Hub nicht angezeigt wird, erstellen Sie zunächst ein VPN-Gateway in diesem Hub.
4. Wählen Sie **Links** aus, um Informationen über die physischen Verbindungen an der Zweigstelle hinzuzufügen. Falls Sie über ein CPE-Gerät eines Virtual WAN-Partners verfügen, können Sie sich beim Partner erkundigen, ob diese Informationen im Rahmen des im Partnersystem eingerichteten Uploads von Zweigstelleninformationen an Azure übermittelt werden.

   ![Screenshot: Bereich „VPN-Standort erstellen“ mit ausgewählter Registerkarte „Links“](./media/virtual-wan-tutorial-site-include/links.png "Links")

    * **Linkname**: Ein Name, den Sie für die physische Verbindung am VPN-Standort angegeben möchten. Beispiel: meinLink1.
    * **Anbietername**: Der Name des Anbieters der physischen Verbindung am VPN-Standort. Beispiel: ATT, Verizon.
    * **Geschwindigkeit**: Die Geschwindigkeit des VPN-Geräts am Zweigstellenstandort. Beispiel: Der Wert 50 entspricht einer Geschwindigkeit von 50 MBit/s für das VPN-Gerät am Zweigstellenstandort.
    * **IP-Adresse/FDQN**: Die öffentliche IP-Adresse des lokalen Geräts, das diese Verbindung verwendet. Optional können Sie die private IP-Adresse Ihres lokalen VPN-Geräts hinter ExpressRoute angeben. Sie können auch einen vollqualifizierten Domänennamen einbeziehen, z. B. *something.contoso.com*. Der vollqualifizierte Domänenname muss vom VPN-Gateway aufgelöst werden können. Dies ist möglich, wenn der DNS-Server, der diesen vollqualifizierten Domänennamen hostet, über das Internet erreichbar ist. Die IP-Adresse hat Vorrang, wenn sowohl die IP-Adresse als auch der vollqualifizierte Domänenname angegeben sind.

      >[!NOTE]
      >
      >* Unterstützt eine IPv4-Adresse pro vollqualifiziertem Domänennamen. Wenn der vollqualifizierte Domänenname in mehrere IP-Adressen aufgelöst werden soll, übernimmt das VPN-Gateway die erste IP4-Adresse aus der Liste. IPv6-Adressen werden derzeit nicht unterstützt.
      >
      >* Das VPN-Gateway verwaltet einen DNS-Cache, der alle fünf Minuten aktualisiert wird. Das Gateway versucht, vollqualifizierte Domänennamen nur für getrennte Tunnel aufzulösen. Ein Zurücksetzen des Gateways oder eine Konfigurationsänderung können ebenfalls eine Auflösung des vollqualifizierten Domänennamens auslösen.
      >
5. Mithilfe des Kontrollkästchens können Sie Verbindungen löschen oder weitere Verbindungen hinzufügen. Pro VPN-Standort werden vier Verbindungen unterstützt. Wenn Sie beispielsweise vier Internetdienstanbieter am Zweigstellenstandort einsetzen, können Sie vier Verbindungen erstellen (einen pro Internetdienstanbieter) und Informationen für jede Verbindung angeben.
6. Nachdem Sie die Felder ausgefüllt haben, klicken Sie auf **Überprüfen + erstellen**, um die Eingaben zu überprüfen und den Standort zu erstellen.
7. Zeigen Sie den Status auf der Seite „VPN-Standorte“ an. Der Standort wechselt in den Zustand **Verbindung erforderlich**, weil der Standort noch nicht mit dem Hub verbunden wurde.
