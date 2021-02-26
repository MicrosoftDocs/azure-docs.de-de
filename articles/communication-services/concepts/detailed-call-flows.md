---
title: Topologien für Anrufabläufe in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Enthält eine Beschreibung von Topologien für Anrufabläufe in Azure Communication Services.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 12/11/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 1333bd08f8a79969817bcb21aa4580d1994d09ce
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594659"
---
# <a name="call-flow-topologies"></a>Topologien für Anrufabläufe
Dieser Artikel enthält eine Beschreibung der Topologien für Anrufabläufe in Azure Communication Services. Die Informationen in diesem Artikel sind gut für Sie geeignet, wenn Sie als Unternehmenskunde Communication Services in ein von Ihnen verwaltetes Netzwerk integrieren möchten. Eine Einführung in Communication Services-Anrufabläufe finden Sie in der [Dokumentation zu den Konzepten von Anrufabläufen](./call-flows.md).

## <a name="background"></a>Hintergrund

### <a name="network-concepts"></a>Netzwerkkonzepte

Bevor es um die Topologien von Anrufabläufen geht, sollen zunächst einige Begriffe definiert werden, die in diesem Dokument vorkommen.

Ein **Kundennetzwerk** enthält alle von Ihnen verwalteten Netzwerksegmente. Hierbei kann es sich um kabelgebundene und drahtlose Netzwerke in Ihrer Niederlassung oder zwischen Standorten, Rechenzentren und Internetdienstanbietern handeln.

Ein Kundennetzwerk verfügt normalerweise über mehrere Netzwerkgrenzen mit Firewalls bzw. Proxyservern, mit denen die Sicherheitsrichtlinien Ihrer Organisation durchgesetzt werden. Wir empfehlen Ihnen, eine [umfassende Netzwerkbewertung](https://docs.microsoft.com/microsoftteams/3-envision-evaluate-my-environment) durchzuführen, um die optimale Leistung und Qualität Ihrer Kommunikationslösung sicherzustellen.

Das **Communication Services-Netzwerk** ist das Netzwerksegment, von dem Azure Communication Services unterstützt wird. Dieses weltweite Netzwerk wird von Microsoft verwaltet und verfügt über Edgebereiche in der Nähe der meisten Kundennetzwerke. Das Netzwerk wird für die Transportvermittlung, Medienverarbeitung für Gruppenanrufe und andere Komponenten genutzt, für die eine umfassende Medienkommunikation in Echtzeit unterstützt wird.

### <a name="types-of-traffic"></a>Arten von Datenverkehr

Communication Services basiert hauptsächlich auf zwei Arten von Datenverkehr: **Echtzeitmedien** und **Signalisierung**.

**Echtzeitmedien** werden per RTP (Real-Time Transport Protocol) übertragen. Dieses Protokoll unterstützt die Übertragung von Audio-, Video- und Bildschirmfreigabedaten. Diese Daten sind anfällig für Probleme aufgrund der Netzwerklatenz. Echtzeitmedien können zwar auch per TCP oder HTTP übertragen werden, aber wir empfehlen Ihnen die Verwendung von UDP als Transportschichtprotokoll, wenn Endbenutzerumgebungen mit hoher Leistung unterstützt werden sollen. Mediennutzdaten, die per RTP übertragen werden, werden mit SRTP geschützt.

Benutzer Ihrer Communication Services-Lösung stellen über ihre Clientgeräte eine Verbindung mit Ihren Diensten her. Die Kommunikation zwischen diesen Geräten und Ihren Servern wird per **Signalisierung** (Englisch: Signaling) verarbeitet. Beispiel: Die Anrufinitiierung und Echtzeitchats werden basierend auf der Signalisierung zwischen Geräten und Ihrem Dienst unterstützt. Für den Großteil des Signalisierungsdatenverkehrs wird HTTPS REST genutzt, aber in einigen Fällen kann auch SIP als Protokoll eingesetzt werden. Diese Art von Datenverkehr ist weniger anfällig für Latenzprobleme, und eine Signalisierung mit geringer Latenz führt dazu, dass die Benutzer Ihrer Lösung eine benutzerfreundliche Oberfläche erhalten.

### <a name="interoperability-restrictions"></a>Einschränkungen der Interoperabilität

Für Medien, die Communication Services durchlaufen, gelten die folgenden Einschränkungen:

**Drittanbieter-Mediarelays:** Interoperabilität mit Mediarelays von Drittanbietern wird nicht unterstützt. Wenn Communication Services als einer Ihrer Medienendpunkt fungiert, können Ihre Medien nur Microsoft-native Mediarelays durchlaufen – einschließlich derjenigen, die Microsoft Teams und Skype for Business unterstützen.

Ein Session Border Controller (SBC) eines Drittanbieters an der Grenze zum Telefonfestnetz sollte als Abschluss des RTP/RTCP-Datenstroms dienen, per SRTP geschützt sein und keine Weitergabe an den nächsten Hop durchführen. Wenn Sie den Ablauf (Flow) an den nächsten Hop weitergeben, ist dieser dort unter Umständen nicht verständlich.

**SIP-Proxyserver eines Drittanbieters:** Ein Communication Services-SIP-Signalisierungsdialog mit einem Drittanbieter-SBC bzw. Gateway kann Microsoft-native SIP-Proxys durchlaufen (wie bei Teams). Die Interoperabilität mit SIP-Proxys von Drittanbietern wird nicht unterstützt.

**Drittanbieter-B2BUA (oder SBC):** Der Communication Services-Medienablauf für das Telefonfestnetz endet auf einem Drittanbieter-SBC. Die Interoperabilität mit einem Drittanbieter-SBC innerhalb des Communication Services-Netzwerks (bei dem ein Drittanbieter-SBC zwischen zwei Communication Services-Endpunkten vermittelt) wird nicht unterstützt.

### <a name="unsupported-technologies"></a>Nicht unterstützte Technologien

**VPN-Netzwerk:** Für Communication Services wird die Medienübertragung über VPNs nicht unterstützt. Wenn Ihre Benutzer VPN-Clients verwenden, sollte Mediendatenverkehr vom Client aufgeteilt und über eine andere Verbindung als eine VPN-Verbindung weitergeleitet werden. Dies ist unter [Aktivieren von Lync-Medien zum Umgehen eines VPN-Tunnels](https://techcommunity.microsoft.com/t5/skype-for-business-blog/enabling-lync-media-to-bypass-a-vpn-tunnel/ba-p/620210) beschrieben.

*Hinweis: Der Titel enthält zwar „Lync“, aber die Informationen gelten auch für Azure Communication Services und Teams.*

**Paketstrukturierung:** Geräte für „Packet Snipping“, „Packet Inspection“ oder „Packet Shaping“ werden nicht unterstützt und können zu einer erheblichen Beeinträchtigung der Qualität führen.

### <a name="call-flow-principles"></a>Prinzipien von Anrufabläufen

Für Communication Services-Anrufabläufe gelten vier allgemeine Prinzipien:

* **Der erste Teilnehmer eines Communication Services-Gruppenanrufs bestimmt die Region, in der der Anruf gehostet wird**. Für einige Topologien gelten Ausnahmen von dieser Regel. Dies ist unten beschrieben.
* **Der Medienendpunkt, der für die Unterstützung eines Communication Services-Anrufs verwendet wird, wird basierend auf den Anforderungen der Medienverarbeitung ausgewählt**. Die Anzahl von Teilnehmern eines Anrufs hat hierauf keinerlei Auswirkung. Bei einem Punkt-zu-Punkt-Anruf kann beispielsweise ein Medienendpunkt in der Cloud genutzt werden, um Medien für die Transkription oder Aufzeichnung zu verarbeiten, während für einen Anruf mit zwei Teilnehmern ggf. keine Medienendpunkte verwendet werden. Bei Gruppenanrufen wird ein Medienendpunkt für Misch- und Weiterleitungszwecke genutzt. Dieser Endpunkt wird basierend auf der Region ausgewählt, in der der Anruf gehostet wird. Mediendatenverkehr, der von einem Client an den Medienendpunkt gesendet wird, kann direkt weitergeleitet werden. Alternativ kann auch ein Transportrelay in Azure genutzt werden, falls dies aufgrund der Firewalleinschränkungen des Kundennetzwerks erforderlich ist.
* **Für Mediendatenverkehr für Peer-to-Peer-Anrufe wird der direkte und kürzeste Weg verwendet, der verfügbar ist** – vorausgesetzt, dass für den Anruf kein Medienendpunkt in der Cloud benötigt wird. Die bevorzugte Route ist der direkte Weg zum Remotepeer (Client). Falls keine direkte Route verfügbar ist, wird der Datenverkehr über ein oder mehrere Transportrelays weitergeleitet. Mediendatenverkehr sollte keine Server durchlaufen, die als „Packet Shapers“ für die Strukturierung, VPN-Server oder andere Einheiten fungieren und zu einer Verzögerung der Verarbeitung und einer geringeren Benutzerfreundlichkeit für Endbenutzer führen können.
* **Signalisierungsdatenverkehr wird immer an den Server geleitet, der am wenigsten weit vom Benutzer entfernt ist.**

Weitere Informationen zu den Details des ausgewählten Medienpfads finden Sie in der [Dokumentation zu den Konzepten von Anrufabläufen](./call-flows.md).


## <a name="call-flows-in-various-topologies"></a>Anrufabläufe in unterschiedlichen Topologien

### <a name="communication-services-internet"></a>Communication Services (Internet)

Diese Topologie wird von Kunden genutzt, die Communication Services aus der Cloud ohne lokale Bereitstellung nutzen, z. B. per SIP-Schnittstelle. Bei dieser Topologie fließt Datenverkehr für Communication Services über das Internet.

:::image type="content" source="./media/call-flows/detailed-flow-general.png" alt-text="Azure Communication Services-Topologie":::

*Abbildung 1: Communication Services-Topologie*

Die Richtung der Pfeile im obigen Diagramm gibt die Initiierungsrichtung der Kommunikation an, die sich auf die Konnektivität an den Unternehmensgrenzen auswirkt. Bei Verwendung von UDP für Medien fließen die ersten Pakete ggf. in umgekehrter Richtung, aber diese können blockiert werden, bis Pakete in der anderen Richtung fließen.

Beschreibungen von Abläufen:
* Ablauf 2*: Ein Ablauf, der von einem Benutzer im Kundennetzwerk über die Communication Services-Benutzeroberfläche ins Internet initiiert wurde. Beispiele für diese Abläufe sind die DNS- und die Peer-to-Peer-Medienübertragung.
* Ablauf 2: Ein Ablauf, der von einem Communication Services-Remotebenutzer mit einem Mobilgerät per VPN zum Kundennetzwerk initiiert wurde.
* Ablauf 3: Ein Ablauf, der von einem Communication Services-Remotebenutzer mit einem Mobilgerät zu Communication Services-Endpunkten initiiert wurde.
* Ablauf 4: Ein Ablauf, der von einem Benutzer im Kundennetzwerk zu Communication Services initiiert wurde.
* Ablauf 5: Ein Peer-to-Peer-Medienablauf zwischen einem Communication Services-Benutzer und einem anderen Benutzer im Kundennetzwerk.
* Ablauf 6: Ein Peer-to-Peer-Medienablauf zwischen zwei Communication Services-Remotebenutzern mit Mobilgeräten über das Internet.

### <a name="use-case-one-to-one"></a>Anwendungsfall: 1:1

Bei 1:1-Anrufen wird ein gängiges Modell genutzt, bei dem dem Anrufer einige Kandidaten präsentiert werden, bei denen es sich um IP-Adressen und Ports handelt. Dies können lokale, Relay- und reflexive Kandidaten (öffentliche IP-Adresse des Clients aus Sicht des Relays) sein. Der Anrufer sendet diese Kandidaten an die angerufene Partei. Die angerufene Partei erhält ebenfalls eine ähnliche Gruppe von Kandidaten und sendet sie an den Anrufer. Mit STUN-Nachrichten für die Konnektivitätsprüfung wird ermittelt, welche Medienpfade für die anrufende bzw. angerufene Partei funktionieren, und der am besten geeignete Pfad wird ausgewählt. Medien (per SRTP geschützte RTP/RTCP-Pakete) werden dann gesendet, indem das ausgewählte Kandidatenpaar verwendet wird. Das Transportrelay wird im Rahmen von Azure Communication Services bereitgestellt.

Wenn für die lokalen Kandidaten (IP-Adresse/Port) oder die reflexiven Kandidaten Konnektivität besteht, wird für Medien der direkte Pfad zwischen den Clients ausgewählt (oder NAT genutzt). Falls sich beide Clients im Kundennetzwerk befinden, sollte der direkte Pfad ausgewählt werden. Hierfür ist eine direkte UDP-Konnektivität innerhalb des Kundennetzwerks erforderlich. Wenn beide Clients Nomadic-Cloudbenutzer sind, kann für Medien je nach NAT/Firewall die direkte Konnektivität genutzt werden.

Wenn sich ein Client im Kundennetzwerk und ein anderer außerhalb befindet (z. B. ein mobiler Cloudbenutzer), ist die Wahrscheinlichkeit gering, dass die direkte Konnektivität zwischen den lokalen bzw. den reflexiven Kandidaten funktioniert. In diesem Fall ist eine Option die Nutzung eines Transportrelay-Kandidaten von einem der beiden Clients. (Beispiel: Der interne Client hat einen Relaykandidaten vom Transportrelay in Azure erhalten, und der externe Client muss STUN/RTP/RTCP-Pakete an das Transportrelay senden können.) Eine andere Möglichkeit ist ein Sendevorgang des internen Clients an den vom Mobilcloudclient erhaltenen Relaykandidaten. Für Medien wird dringend die Verwendung von UDP-Konnektivität empfohlen, aber TCP wird ebenfalls unterstützt.

**Allgemeine Schritte:**
1.  Communication Services-Benutzer A löst den URL-Domänennamen (DNS) mit Ablauf 2 auf.
2.  Benutzer A ordnet mit Ablauf 4 einen Mediarelay-Port auf dem Teams-Transportrelay zu.
3.  Communication Services-Benutzer A sendet eine Einladung mit ICE-Kandidaten per Ablauf 4 an Communication Services.
4.  Communication Services benachrichtigt Benutzer B per Ablauf 4.
5.  Benutzer B ordnet mit Ablauf 4 einen Mediarelay-Port auf dem Teams-Transportrelay zu.
6.  Benutzer B sendet die Antwort mit ICE-Kandidaten per Ablauf 4, die wiederum per Ablauf 4 an Benutzer A geleitet wird.
7.  Benutzer A und Benutzer B rufen ICE-Konnektivitätstests auf, und der beste verfügbare Medienpfad wird ausgewählt (siehe Anwendungsfälle in den Diagrammen unten).
8.  Beide Benutzer senden Telemetriedaten per Ablauf 4 an Communication Services.

### <a name="customer-network-intranet"></a>Kundennetzwerk (Intranet)

:::image type="content" source="./media/call-flows/one-to-one-internal.png" alt-text="Datenverkehrsablauf im Kundennetzwerk":::

*Abbildung 2: Innerhalb des Kundennetzwerks*

In Schritt 7 ist der Peer-to-Peer-Medienablauf (Ablauf 5) ausgewählt.

Diese Medienübertragung ist bidirektional. Die Richtung von Ablauf 5 gibt an, dass die Kommunikation von einer Seite aus Konnektivitätssicht initiiert wird. In diesem Fall spielt es keine Rolle, welche Richtung verwendet wird, da sich beide Endpunkte im Kundennetzwerk befinden.

### <a name="customer-network-to-external-user-media-relayed-by-teams-transport-relay"></a>Aus dem Kundennetzwerk zum externen Benutzer (von Teams-Transportrelay übertragene Medien)

:::image type="content" source="./media/call-flows/one-to-one-via-relay.png" alt-text="Ablauf eines 1:1-Anrufs über ein Relay":::

*Abbildung 3: Aus dem Kundennetzwerk zum externen Benutzer (von Azure-Transportrelay übertragene Medien)*

In Schritt 7 werden Ablauf 4 (vom Kundennetzwerk zu Communication Services) und Ablauf 3 (von Communication Services-Remotebenutzer mit Mobilgerät zu Azure Communication Services) ausgewählt.

Diese Abläufe werden vom Teams-Transportrelay in Azure vermittelt.

Diese Medienübertragung ist bidirektional. Die Richtung gibt an, von welcher Seite die Kommunikation aus Konnektivitätssicht initiiert wird. In diesem Fall werden diese Abläufe für die Signalisierung und Medien verwendet, indem verschiedene Transportprotokolle und Adressen genutzt werden.

### <a name="customer-network-to-external-user-direct-media"></a>Vom Kundennetzwerk zum externen Benutzer (direkte Medien)

:::image type="content" source="./media/call-flows/one-to-one-with-extenal.png" alt-text="Ablauf eines 1:1-Anrufs mit einem externen Benutzer":::

*Abbildung 4: Vom Kundennetzwerk zum externen Benutzer (direkte Medien)*

In Schritt 7 wird Ablauf 2 (vom Kundennetzwerk zum Peer des Clients über das Internet) ausgewählt.

Die Verwendung von direkten Medien mit einem Remotebenutzer mit einem Mobilgerät (keine Vermittlung über Azure) ist optional. Anders ausgedrückt: Sie können diesen Pfad blockieren, um einen Medienpfad über ein Transportrelay in Azure zu erzwingen.

Diese Medienübertragung ist bidirektional. Die Richtung von Ablauf 2 für mobile Remotebenutzer gibt an, dass die Kommunikation von einer Seite aus Konnektivitätssicht initiiert wird.

### <a name="vpn-user-to-internal-user-media-relayed-by-teams-transport-relay"></a>Von VPN-Benutzer zu internem Benutzer (Vermittlung von Medien über Teams-Transportrelay)

:::image type="content" source="./media/call-flows/vpn-to-internal-via-relay.png" alt-text="Ablauf eines 1:1-Anrufs mit einem VPN-Benutzer per Relay":::

*Abbildung 5: Von VPN-Benutzer zu internem Benutzer (Vermittlung von Medien über Azure Relay)*

Bei der Signalisierung zwischen dem VPN und dem Kundennetzwerk wird Ablauf 2* verwendet. Bei der Signalisierung zwischen dem Kundennetzwerk und Azure wird Ablauf 4 verwendet. Die Medien umgehen aber das VPN und werden per Ablauf 3 und 4 über das Azure-Mediarelay geleitet.

### <a name="vpn-user-to-internal-user-direct-media"></a>Von VPN-Benutzer zu internem Benutzer (direkte Medien)

:::image type="content" source="./media/call-flows/vpn-to-internal-direct-media.png" alt-text="Ablauf eines 1:1-Anrufs per VPN mit direkten Medien":::

*Abbildung 6: Von VPN-Benutzer zu internem Benutzer (direkte Medien)*

Bei der Signalisierung zwischen dem VPN und dem Kundennetzwerk wird Ablauf 2* verwendet. Bei der Signalisierung zwischen dem Kundennetzwerk und Azure wird Ablauf 4 verwendet. Die Medien umgehen aber das VPN und werden per Ablauf 2 aus dem Kundennetzwerk ins Internet geleitet.

Diese Medienübertragung ist bidirektional. Die Richtung von Ablauf 2 für den mobilen Remotebenutzer gibt an, dass die Kommunikation von einer Seite aus Konnektivitätssicht initiiert wird.

### <a name="vpn-user-to-external-user-direct-media"></a>Von VPN-Benutzer zu externem Benutzer (direkte Medien)

:::image type="content" source="./media/call-flows/vpn-user-to-external-user.png" alt-text="Ablauf eines 1:1-Anrufs per VPN mit direkten Medien":::

*Abbildung 7: Von VPN-Benutzer zu externem Benutzer (direkte Medien)*

Bei der Signalisierung zwischen dem VPN-Benutzer und dem Kundennetzwerk werden Ablauf 2* und Ablauf 4 zu Azure verwendet. Die Medien umgehen aber das VPN und werden per Ablauf 6 weitergeleitet.

Diese Medienübertragung ist bidirektional. Die Richtung von Ablauf 6 für den mobilen Remotebenutzer gibt an, dass die Kommunikation von einer Seite aus Konnektivitätssicht initiiert wird.

### <a name="use-case-communication-services-client-to-pstn-through-communication-services-trunk"></a>Anwendungsfall: Vom Communication Services-Client zum Telefonfestnetz über den Communication Services-Trunk

Communication Services ermöglicht das Tätigen und Annehmen von Anrufen über das Telefonfestnetz (PSTN). Wenn der PSTN-Trunk über von Communication Services bereitgestellte Telefonnummern verbunden wird, gelten für diesen Anwendungsfall keine besonderen Konnektivitätsanforderungen. Wenn Sie Ihren eigenen lokalen PSTN-Trunk mit Azure Communication Services verbinden möchten, können Sie die SIP-Schnittstelle nutzen (verfügbar in Kalenderjahr 2021).

:::image type="content" source="./media/call-flows/acs-to-pstn.png" alt-text="1:1-Anruf mit PSTN-Teilnehmer":::

*Abbildung 8: Von Communication Services-Benutzer zu PSTN über Azure-Trunk*

In diesem Fall wird für die Signalisierung und die Medien vom Kundennetzwerk zu Azure Ablauf 4 verwendet.

### <a name="use-case-communication-services-group-calls"></a>Anwendungsfall: Communication Services-Gruppenanrufe

Der Dienst für Audio, Video und Bildschirmfreigabe (VBSS) ist Teil von Azure Communication Services. Er verfügt über eine öffentliche IP-Adresse, die aus dem Kundennetzwerk und von einem Nomadic-Cloudclient erreichbar sein muss. Jeder Client bzw. Endpunkt muss eine Verbindung mit dem Dienst herstellen können.

Interne Clients erhalten die lokalen, reflexiven und Relaykandidaten auf die gleiche Weise, wie dies für 1:1-Anrufe beschrieben wurde. Die Clients senden diese Kandidaten per Einladung an den Dienst. Vom Dienst wird kein Relay verwendet, da er über eine öffentlich erreichbare IP-Adresse verfügt. Er reagiert daher mit seiner lokalen IP-Adresse als Kandidaten. Der Client und der Dienst überprüfen die Konnektivität auf die gleiche Weise, wie dies für 1:1-Anrufe beschrieben wurde.

:::image type="content" source="./media/call-flows/acs-group-calls.png" alt-text="OACS-Gruppenanruf":::

*Abbildung 9: Communication Services-Gruppenanrufe*

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit Anrufen](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Die Artikel zu den folgenden Themen könnten Sie auch interessieren:

- [Anruftypen](../concepts/voice-video-calling/about-call-types.md)
- [Client-Server-Architektur](./client-and-server-architecture.md)

