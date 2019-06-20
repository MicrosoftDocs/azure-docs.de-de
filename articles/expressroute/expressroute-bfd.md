---
title: Konfigurieren von BFD über ExpressRoute – Azure | Microsoft-Dokumentation
description: Dieser Artikel enthält Anweisungen zum Konfigurieren von BFD (Bidirectional Forwarding Detection) über das private Peering einer ExpressRoute-Leitung.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 8/17/2018
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 14f65851e50ed25024524f6d988ba2b2f2b3aeba
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60367665"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurieren von BFD über ExpressRoute

ExpressRoute unterstützt Bidirectional Forwarding Detection (BFD) über privates Peering. Durch die Aktivierung von BFD über ExpressRoute können Sie die Erkennung von Verbindungsfehlern zwischen MSEE-Geräten (MSEE = Microsoft Enterprise-Edge) und den Routern beschleunigen, auf denen Sie die ExpressRoute-Leitung (PE) beenden. Sie können ExpressRoute Customer-Edge-Routinggeräte oder Partner Edge-Routinggeräte beenden (wenn Sie den verwalteten Layer 3-Verbindungsdienst verwendet haben). In diesem Dokument werden die Notwendigkeit von BFD und die Vorgehensweise zur Aktivierung von BFD über ExpressRoute erläutert.

## <a name="need-for-bfd"></a>Notwendigkeit von BFD

Das folgende Diagramm zeigt den Vorteil der Aktivierung von BFD über die ExpressRoute-Leitung: [![1]][1]

Sie können die ExpressRoute-Leitung entweder über Layer 2-Verbindungen oder über verwaltete Layer 3-Verbindungen aktivieren. In beiden Fällen ist das darüber liegende BGP für die Erkennung von Verbindungsfehlern in dem Pfad verantwortlich, wenn sich im ExpressRoute-Verbindungspfad mehrere Layer 2-Geräte befinden.

Auf den MSEE-Geräten sind keepalive und hold-time im BGP in der Regel für eine Dauer von 60 bzw. 180 Sekunden konfiguriert. Folglich würde es nach Auftreten eines Verbindungsfehlers bis zu drei Minuten dauern, um den Verbindungsfehler zu erkennen und den Datenverkehr über eine alternative Verbindung weiterzuleiten.

Sie können die BGP-Zeitgeber steuern, indem Sie für keepalive und hold-time im BGP auf dem Customer Edge-Peeringgerät niedrigere Werte konfigurieren. Wenn die BGP-Zeitgeber zwischen den beiden Peeringgeräten nicht übereinstimmen, würden in der BGP-Sitzung niedrigere Zeitgeberwerte zwischen den Peers verwendet. Der Wert für keepalive im BGP kann auf nur drei Sekunden festgelegt werden und der Wert für hold-time in der Größenordnung auf mehr als zehn Sekunden. Das dynamische Festlegen von BGP-Zeitgebern wird jedoch weniger bevorzugt, da das Protokoll prozessintensiv ist.

In diesem Szenario kann BFD helfen. Mit BFD können Verbindungsfehler mit geringem Mehraufwand in einem Zeitintervall von unter 1 Sekunde erkannt werden. 


## <a name="enabling-bfd"></a>Aktivieren von BFD

BFD wird standardmäßig unter allen neu erstellten privaten ExpressRoute-Peeringschnittstellen auf den MSEE-Geräten konfiguriert. Zum Aktivieren von BFD müssen Sie folglich nur BFD auf Ihren PEs konfigurieren. BFD wird in zwei Schritten konfiguriert: Sie müssen BFD in der Schnittstelle konfigurieren und anschließend mit der BGP-Sitzung verknüpfen.

Eine PE-Beispielkonfiguration (unter Verwendung von Cisco IOS XE) wird unten dargestellt. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Zum Aktivieren von BFD unter einem bereits vorhandenen privaten Peering müssen Sie das Peering zurücksetzen. Siehe [Zurücksetzen von ExpressRoute-Peerings][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Aushandlung des BFD-Zeitgebers

Zwischen BFD-Peers bestimmt der langsamere der beiden Peers die Übertragungsrate. BFD-Übertragung auf MSEE-Geräten/Empfangsintervalle werden auf 300 Millisekunden festgelegt. In bestimmten Szenarien kann das Intervall auf einen höheren Wert von 750 Millisekunden festgelegt werden. Durch das Konfigurieren höherer Werte können Sie erzwingen, dass diese Intervalle länger sind; kürzere Intervalle können jedoch nicht erzwungen werden.

>[!NOTE]
>Wenn Sie georedundante Leitungen für das private ExpressRoute-Peering konfiguriert haben oder IPSec-VPN-Konnektivität zwischen Standorten als Sicherung für das private ExpressRoute-Peering verwenden; durch die Aktivierung von BFD über das private Peering könnte der Failover nach einem ExpressRoute-Konnektivitätsfehler schneller erfolgen. 
>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen oder Hilfe finden Sie unter den folgenden Links:

- [Erstellen und Ändern einer ExpressRoute-Verbindung][CreateCircuit]
- [Erstellen und Ändern des Routings für eine ExpressRoute-Verbindung][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD beschleunigt die Zeit zur Erkennung von Verbindungsfehlern"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






