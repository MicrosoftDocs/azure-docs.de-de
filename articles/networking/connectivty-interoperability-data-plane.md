---
title: 'Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Analyse der Datenebene | Microsoft-Dokumentation'
description: Dieser Artikel bietet eine Datenebenenanalyse der Testeinrichtung, mit der Sie die Interoperabilität zwischen ExpressRoute, einem Site-to-Site-VPN und Peering zwischen virtuellen Netzwerken in Azure analysieren können.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: f4d94536a8c1b509e0ce435a764e69984b5d415e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60425545"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Analyse der Datenebene

In diesem Artikel wird die Datenebenenanalyse der [Testeinrichtung][Setup] beschrieben. Sie können auch die [Testeinrichtungskonfiguration][Configuration] und die [Steuerebenenanalyse][Control-Analysis] der Testeinrichtung überprüfen.

Bei der Datenebenenanalyse wird der Pfad untersucht, den Pakete in einer Topologie von einem lokalen Netzwerk (LAN oder virtuelles Netzwerk) zu einem anderen durchlaufen. Der Datenpfad zwischen zwei lokalen Netzwerken ist nicht unbedingt symmetrisch. Aus diesem Grund analysieren wir in diesem Artikel den Weiterleitungspfad von einem lokalen Netzwerk zu einem anderen separat vom Pfad in umgekehrter Richtung.

## <a name="data-path-from-the-hub-vnet"></a>Datenpfad vom Hub-VNET

### <a name="path-to-the-spoke-vnet"></a>Pfad zum Spoke-VNET

Beim VNET-Peering (virtuelles Netzwerk) wird die Funktionalität der Netzwerkbrücke zwischen den beiden VNETs emuliert, die per Peering verbunden sind. Die Traceroute-Ausgabe von einem Hub-VNET zu einer VM im Spoke-VNET:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

Die folgende Abbildung zeigt die grafische Verbindung des Hub-VNET und des Spoke-VNET aus der Perspektive von Azure Network Watcher:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>Pfad zum Branch-VNET

Die Traceroute-Ausgabe von einem Hub-VNET zu einer VM im Branch-VNET:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop das VPN-Gateway in Azure zum VPN-Gateway des Hub-VNET. Der zweite Hop ist das VPN-Gateway des Branch-VNET. Die IP-Adresse des VPN-Gateways des Branch-VNET ist im Hub-VNET nicht angekündigt. Der dritte Hop ist die VM im Branch-VNET.

Die folgende Abbildung zeigt die grafische Verbindung des Hub-VNET und des Branch-VNET von Network Watcher aus betrachtet:

[![2]][2]

Die folgende Abbildung zeigt die Rasteransicht in Network Watcher für dieselbe Verbindung:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>Pfad zum lokalen Standort 1

Die Traceroute-Ausgabe von einem Hub-VNET zu einer VM am lokalen Standort 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop der Azure ExpressRoute-Gateway-Tunnelendpunkt zu einem Microsoft Enterprise Edge-Router (MSEE). Die zweite und der dritte Hop sind der CE-Router (kundenseitiger Edge) und die LAN-IP-Adressen des lokalen Standorts 1. Diese IP-Adressen sind im Hub-VNET nicht angekündigt. Der vierte Hop ist die VM am lokalen Standort 1.


### <a name="path-to-on-premises-location-2"></a>Pfad zum lokalen Standort 2

Die Traceroute-Ausgabe von einem Hub-VNET zu einer VM am lokalen Standort 2:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop der ExpressRoute-Gatewaytunnel-Endpunkt zu einem MSEE. Der zweite und dritte Hop sind der CE-Router und die LAN-IP-Adressen des lokalen Standorts 2. Diese IP-Adressen sind im Hub-VNET nicht angekündigt. Der vierte Hop ist die VM am lokalen Standort 2.

### <a name="path-to-the-remote-vnet"></a>Pfad zum Remote-VNET

Die Traceroute-Ausgabe von einem Hub-VNET zu einer VM im Remote-VNET:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop der ExpressRoute-Gatewaytunnel-Endpunkt zu einem MSEE. Der zweite Hop ist die Gateway-IP-Adresse des Remote-VNET. Der IP-Adressbereich des zweiten Hops wird im Hub-VNET nicht angekündigt. Der dritte Hop ist die VM im Remote-VNET.

## <a name="data-path-from-the-spoke-vnet"></a>Datenpfad aus dem Spoke-VNET

Für das Spoke-VNET wird dieselbe Netzwerkansicht wie im Hub-VNET genutzt. Über das VNET-Peering nutzt das Spoke-VNET die Remotegatewayverbindung des Hub-VNET so, als ob eine direkte Verbindung mit dem Spoke-VNET vorhanden wäre.

### <a name="path-to-the-hub-vnet"></a>Pfad zum Hub-VNET

Die Traceroute-Ausgabe vom Spoke-VNET zu einer VM im Hub-VNET:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Pfad zum Branch-VNET

Die Traceroute-Ausgabe vom Spoke-VNET zu einer VM im Branch-VNET:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop das VPN-Gateway des Hub-VNET. Der zweite Hop ist das VPN-Gateway des Branch-VNET. Die IP-Adresse des VPN-Gateways des Branch-VNET ist im Hub-/Spoke-VNET nicht angekündigt. Der dritte Hop ist die VM im Branch-VNET.

### <a name="path-to-on-premises-location-1"></a>Pfad zum lokalen Standort 1

Die Traceroute-Ausgabe vom Spoke-VNET zu einer VM am lokalen Standort 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop der ExpressRoute-Gatewaytunnel-Endpunkt des Hub-VNET zu einem MSEE. Die zweite und der dritte Hop sind der CE-Router und die LAN-IP-Adressen des lokalen Standorts 1. Diese IP-Adressen sind im Hub-/Spoke-VNET nicht angekündigt. Der vierte Hop ist die VM am lokalen Standort 1.

### <a name="path-to-on-premises-location-2"></a>Pfad zum lokalen Standort 2

Die Traceroute-Ausgabe vom Spoke-VNET zu einer VM am lokalen Standort 2:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop der ExpressRoute-Gatewaytunnel-Endpunkt des Hub-VNET zu einem MSEE. Der zweite und dritte Hop sind der CE-Router und die LAN-IP-Adressen des lokalen Standorts 2. Diese IP-Adressen sind in den Hub-/Spoke-VNETs nicht angekündigt. Der vierte Hop ist die VM am lokalen Standort 2.

### <a name="path-to-the-remote-vnet"></a>Pfad zum Remote-VNET

Die Traceroute-Ausgabe vom Spoke-VNET zu einer VM im Remote-VNET:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop der ExpressRoute-Gatewaytunnel-Endpunkt des Hub-VNET zu einem MSEE. Der zweite Hop ist die Gateway-IP-Adresse des Remote-VNET. Der IP-Adressbereich des zweiten Hops wird im Hub-/Spoke-VNET nicht angekündigt. Der dritte Hop ist die VM im Remote-VNET.

## <a name="data-path-from-the-branch-vnet"></a>Datenpfad vom Branch-VNET

### <a name="path-to-the-hub-vnet"></a>Pfad zum Hub-VNET

Die Traceroute-Ausgabe vom Branch-VNET zu einer VM im Hub-VNET:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop das VPN-Gateway des Branch-VNET. Der zweite Hop ist das VPN-Gateway des Hub-VNET. Die IP-Adresse des VPN-Gateways des Hub-VNET ist im Remote-VNET nicht angekündigt. Der dritte Hop ist die VM im Hub-VNET.

### <a name="path-to-the-spoke-vnet"></a>Pfad zum Spoke-VNET

Die Traceroute-Ausgabe vom Branch-VNET zu einer VM im Spoke-VNET:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop das VPN-Gateway des Branch-VNET. Der zweite Hop ist das VPN-Gateway des Hub-VNET. Die IP-Adresse des VPN-Gateways des Hub-VNET ist im Remote-VNET nicht angekündigt. Der dritte Hop ist die VM im Spoke-VNET.

### <a name="path-to-on-premises-location-1"></a>Pfad zum lokalen Standort 1

Die Traceroute-Ausgabe vom Branch-VNET zu einer VM am lokalen Standort 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

In dieser Traceroute-Ausgabe ist der erste Hop das VPN-Gateway des Branch-VNET. Der zweite Hop ist das VPN-Gateway des Hub-VNET. Die IP-Adresse des VPN-Gateways des Hub-VNET ist im Remote-VNET nicht angekündigt. Der dritte Hop ist der Beendigungspunkt des VPN-Tunnels auf dem primären CE-Router. Der vierte Hop ist eine interne IP-Adresse des lokalen Standorts 1. Diese LAN-IP-Adresse ist außerhalb des CE-Routers nicht angekündigt. Der fünfte Hop ist die Ziel-VM am lokalen Standort 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Pfad zum lokalen Standort 2 und zum Remote-VNET

Wie für die Steuerebenenanalyse beschrieben, besteht gemäß Netzwerkkonfiguration für das Branch-VNET keine Sichtbarkeit für den lokalen Standort 2 oder das Remote-VNET. Dies wird durch die folgenden Ping-Ergebnisse bestätigt: 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>Datenpfad vom lokalen Standort 1

### <a name="path-to-the-hub-vnet"></a>Pfad zum Hub-VNET

Die Traceroute-Ausgabe vom lokalen Standort 1 zu einer VM im Hub-VNET:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

In dieser Traceroute-Ausgabe sind die ersten beiden Hops Teil des lokalen Netzwerks. Der dritte Hop ist die primäre MSEE-Schnittstelle für den CE-Router. Der vierte Hop ist das ExpressRoute-Gateway des Hub-VNET. Der IP-Adressbereich des ExpressRoute-Gateways des Hub-VNET wird im lokalen Netzwerk nicht angekündigt. Der fünfte Hop ist die Ziel-VM.

Network Watcher stellt nur eine Azure-bezogene Ansicht bereit. Für eine lokale Perspektive verwenden wir den Azure-Netzwerkleistungsmonitor. Der Netzwerkleistungsmonitor bietet Agents, die Sie für Pfaddatenanalysen auf Servern in Netzwerken außerhalb von Azure installieren können.

Die folgende Abbildung zeigt die Topologieansicht der Konnektivität zwischen der VM am lokalen Standort 1 und der VM im Hub-VNET per ExpressRoute:

[![4]][4]

Wie weiter oben erläutert, wird für die Testeinrichtung ein Site-to-Site-VPN als Reserveverbindung für ExpressRoute zwischen dem lokalen Standort 1 und dem Hub-VNET genutzt. Zum Testen des Datenpfads in umgekehrter Richtung lösen wir einen ExpressRoute-Verbindungsausfall zwischen dem primären CE-Router am lokalen Standort 1 und dem zugehörigen MSEE aus. Um den ExpressRoute-Verbindungsausfall herbeizuführen, fahren Sie die für die MSEE-Instanz bestimmte CE-Schnittstelle herunter:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

Die folgende Abbildung zeigt die Topologieansicht der Konnektivität zwischen der VM am lokalen Standort 1 und der VM im Hub-VNET per Site-to-Site-VPN-Verbindung, wenn keine ExpressRoute-Konnektivität vorhanden ist:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>Pfad zum Spoke-VNET

Die Traceroute-Ausgabe vom lokalen Standort 1 zu einer VM im Spoke-VNET:

Wir stellen jetzt die primäre ExpressRoute-Verbindung wieder her, um die Datenpfadanalyse für das Spoke-VNET durchzuführen:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Stellen Sie die primäre ExpressRoute 1-Verbindung für den Rest der Datenpfadanalyse her.

### <a name="path-to-the-branch-vnet"></a>Pfad zum Branch-VNET

Die Traceroute-Ausgabe vom lokalen Standort 1 zu einer VM im Branch-VNET:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Pfad zum lokalen Standort 2

Wie für die [Steuerebenenanalyse][Control-Analysis] beschrieben, besteht gemäß Netzwerkkonfiguration für den lokalen Standort 1 keine Sichtbarkeit für den lokalen Standort 2. Dies wird durch die folgenden Ping-Ergebnisse bestätigt: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Pfad zum Remote-VNET

Die Traceroute-Ausgabe vom lokalen Standort 1 zu einer VM im Remote-VNET:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Datenpfad vom lokalen Standort 2

### <a name="path-to-the-hub-vnet"></a>Pfad zum Hub-VNET

Die Traceroute-Ausgabe vom lokalen Standort 2 zu einer VM im Hub-VNET:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Pfad zum Spoke-VNET

Die Traceroute-Ausgabe vom lokalen Standort 2 zu einer VM im Spoke-VNET:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Pfad zum Branch-VNET, zum lokalen Standort 1 und zum Remote-VNET

Wie für die [Steuerebenenanalyse][Control-Analysis] beschrieben, besteht gemäß Netzwerkkonfiguration für den lokalen Standort 1 keine Sichtbarkeit für das Branch-VNET, den lokalen Standort 1 oder das Remote-VNET. 

## <a name="data-path-from-the-remote-vnet"></a>Datenpfad vom Remote-VNET

### <a name="path-to-the-hub-vnet"></a>Pfad zum Hub-VNET

Die Traceroute-Ausgabe vom Remote-VNET zu einer VM im Hub-VNET:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Pfad zum Spoke-VNET

Die Traceroute-Ausgabe vom Remote-VNET zu einer VM im Spoke-VNET:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Pfad zum Branch-VNET und zum lokalen Standort 2

Wie für die [Steuerebenenanalyse][Control-Analysis] beschrieben, besteht gemäß Netzwerkkonfiguration für das Remote-VNET keine Sichtbarkeit für das Branch-VNET oder den lokalen Standort 2. 

### <a name="path-to-on-premises-location-1"></a>Pfad zum lokalen Standort 1

Die Traceroute-Ausgabe vom Remote-VNET zu einer VM am lokalen Standort 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Gemeinsame ExpressRoute- und Site-to-Site-VPN-Konnektivität

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site-VPN über ExpressRoute

Sie können ein Site-to-Site-VPN mithilfe von ExpressRoute-Microsoft-Peering konfigurieren, um Daten privat zwischen Ihrem lokalen Netzwerk und Ihren Azure-VNETs auszutauschen. Mit dieser Konfiguration können Sie Daten mit Vertraulichkeit, Authentizität und Integrität austauschen. Der Datenaustausch ist außerdem Anti-Replay-konform. Weitere Informationen zur Konfiguration eines Site-to-Site-IPsec-VPN im Tunnelmodus per ExpressRoute-Microsoft-Peering finden Sie unter [Site-to-Site-VPN über ExpressRoute-Microsoft-Peering][S2S-Over-ExR]. 

Die wesentliche Einschränkung für die Konfiguration eines Site-to-Site-VPN mit Microsoft-Peering ist der Durchsatz. Der Durchsatz des IPsec-Tunnels wird durch die Kapazität des VPN-Gateways eingeschränkt. Der Durchsatz des VPN-Gateways ist niedriger als der ExpressRoute-Durchsatz. In diesem Szenario stellt die Verwendung des IPsec-Tunnels für hoch sicheren Datenverkehr und des privaten Peerings für sämtlichen anderen Datenverkehr einen Beitrag zur Optimierung der ExpressRoute-Bandbreitenauslastung dar.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site-VPN als sicherer Failoverpfad für ExpressRoute

ExpressRoute dient als redundantes Verbindungspaar, um Hochverfügbarkeit sicherzustellen. Sie können die georedundante ExpressRoute-Konnektivität in unterschiedlichen Azure-Regionen konfigurieren. Sie können außerdem wie in unserer Testeinrichtung demonstriert in einer Azure-Region mithilfe eines Site-to-Site-VPN einen Failoverpfad für Ihre ExpressRoute-Verbindung einrichten. Wenn über ExpressRoute und das Site-to-Site-VPN die gleichen Präfixe angekündigt werden, priorisiert Azure ExpressRoute. Zur Vermeidung von asymmetrischem Routing zwischen ExpressRoute und dem Site-to-Site-VPN sollte in der lokalen Netzwerkkonfiguration die ExpressRoute-Verbindung ebenfalls den Vorzug vor Site-to-Site-VPN-Verbindungen erhalten.

Weitere Informationen zur Konfiguration von parallelen ExpressRoute- und Site-to-Site-VPN-Verbindungen finden Sie unter [Parallele ExpressRoute- und Site-to-Site-Verbindungen][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Erweitern der Back-End-Konnektivität auf Spoke-VNETs und Branchstandorte

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke-VNET-Konnektivität per VNET-Peering

Die Hub-Spoke-VNET-Architektur wird häufig genutzt. Bei einem Hub handelt es sich um ein VNET in Azure, das als zentraler Konnektivitätspunkt zwischen Ihren Spoke-VNETs und Ihrem lokalen Netzwerk fungiert. Die Spokes sind VNETs, die eine Peeringverbindung mit dem Hub herstellen und zur Isolierung von Workloads verwendet werden können. Der Datenverkehr wird über eine ExpressRoute- oder VPN-Verbindung zwischen dem lokalen Rechenzentrum und dem Hub weitergeleitet. Weitere Informationen zu dieser Architektur finden Sie unter [Implementieren einer Hub-Spoke-Netzwerktopologie in Azure][Hub-n-Spoke].

Beim VNET-Peering in einer Region können Spoke-VNETs Hub-VNET-Gateways verwenden (sowohl VPN- als auch ExpressRoute-Gateways), um mit Remotenetzwerken zu kommunizieren.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Konnektivität mit dem VNET der Zweigniederlassung über das Site-to-Site-VPN

VNETs in verschiedenen Regionen und lokale Netzwerke sollten miteinander über ein Hub-VNET kommunizieren. Die native Azure-Lösung für diese Konfiguration ist Site-to-Site-VPN-Konnektivität über ein VPN. Eine Alternative ist die Verwendung eines virtuellen Netzwerkgeräts (NVA) für das Routing im Hub.

Weitere Informationen finden Sie unter [Was ist VPN-Gateway?][VPN] und [Bereitstellen eines hoch verfügbaren virtuellen Netzwerkgeräts][Deploy-NVA].


## <a name="next-steps"></a>Nächste Schritte

Unter [ExpressRoute – FAQ][ExR-FAQ] finden Sie Informationen zu folgenden Themen:
-   Erfahren Sie, wie viele ExpressRoute-Verbindungen Sie mit einem ExpressRoute-Gateway verbinden können.
-   Anzahl der ExpressRoute-Gateways, die Sie per ExpressRoute verbinden können
-   Erfahren Sie mehr über andere Skalierungslimits von ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg " Network Watcher-Ansicht zur Konnektivität von einem Hub-VNET zu einem Spoke-VNET"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg " Network Watcher-Ansicht zur Konnektivität von einem Hub-VNET zu einem Branch-VNET"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg " Network Watcher-Rasteransicht zur Konnektivität von einem Hub-VNET zu einem Branch-VNET"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg " Netzwerkleistungsmonitor-Ansicht zur Konnektivität von der VM am Standort 1 zum Hub-VNET über ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg " Netzwerkleistungsmonitor-Ansicht zur Konnektivität von der VM am Standort 1 zum Hub-VNET ein Site-to-Site-VPN"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


