---
title: 'Asymmetrisches Routing: Azure ExpressRoute | Microsoft-Dokumentation'
description: Dieser Artikel behandelt Probleme, die bei asymmetrischem Routing in einem Netzwerk mit mehreren Verbindungen mit einem Ziel auftreten können.
documentationcenter: na
services: expressroute
author: osamazia
ms.service: expressroute
ms.topic: article
ms.date: 10/10/2016
ms.author: osamam
ms.custom: seodec18
ms.openlocfilehash: 2b2b678cad50e45660fb763c2a1f9194500edf8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730199"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>Asymmetrisches Routing mit mehreren Netzwerkpfaden
In diesem Artikel erfahren Sie, wie ausgehender und eingehender Netzwerkdatenverkehr über unterschiedliche Routen abgewickelt werden kann, wenn zwischen Netzwerkquelle und -ziel mehrere Pfade verfügbar sind.

Zum besseren Verständnis des asymmetrischen Routings müssen wir uns mit zwei Konzepten vertraut machen: Ein Konzept ist die Auswirkung mehrerer Netzwerkpfade. Das andere Konzept ist die Art und Weise, wie Geräte, z.B. eine Firewall, den Status beibehalten. Geräte dieser Art werden als „zustandsbehaftete Geräte“ bezeichnet. Eine Kombination aus diesen beiden Faktoren führt zu Szenarien, in denen Netzwerkdatenverkehr von einem zustandsbehafteten Gerät verworfen wird, da das zustandsbehaftete Gerät nicht erkennt, dass der Datenverkehr vom Gerät selbst stammt.

## <a name="multiple-network-paths"></a>Mehrere Netzwerkpfade
Wenn ein Unternehmensnetzwerk nur über eine einzelne Internetdienstanbieter-Verbindung mit dem Internet verfügt, wird der gesamte ein- und ausgehende Internetdatenverkehr über den gleichen Pfad abgewickelt. Zur Verbesserung der Netzwerkverfügbarkeit erwerben Unternehmen häufig mehrere Verbindungen als redundante Pfade. In diesem Fall ist es möglich, dass Datenverkehr, der aus dem Netzwerk in das Internet fließt, über eine Verbindung verläuft, während der Antwortdatenverkehr über eine andere Verbindung fließt. Dies wird häufig als asymmetrisches Routing bezeichnet. Beim asymmetrischen Routing wird für den Antwortdatenverkehr ein anderer Pfad als für den ursprünglichen Datenfluss verwendet.

![Netzwerk mit mehreren Pfaden](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

Das asymmetrische Routing wird hauptsächlich im Internet verwendet, kann aber auch für andere Kombinationen mit mehreren Pfaden genutzt werden. Es ist beispielsweise sowohl für einen Internetpfad als auch für einen privaten Pfad geeignet, der zu demselben Ziel führt, sowie für mehrere private Pfade, die ebenfalls zu demselben Ziel führen.

Jeder Router berechnet auf dem Weg von der Quelle zum Ziel den bestmöglichen Pfad, auf dem ein Ziel erreicht werden kann. Die Bestimmung des optimalen Pfads durch den Router basiert auf zwei zentralen Faktoren:

* Das Routing zwischen externen Netzwerken basiert auf einem Routingprotokoll, und zwar dem Border Gateway Protocol (BGP). BGP nimmt Ankündigungen von Nachbarn entgegen und führt eine Reihe von Schritten aus, um den optimalen Pfad zum Ziel zu bestimmen. Der optimale Pfad wird in die eigene Routingtabelle aufgenommen.
* Die Länge der Subnetzmaske einer Route wirkt sich auf die Routingpfade aus. Wenn ein Router mehrere Ankündigungen für dieselbe IP-Adresse, aber mit unterschiedlichen Subnetzmasken, empfängt, zieht der Router die Ankündigung mit einer längeren Subnetzmaske vor, da dies als die spezifischere Route angesehen wird.

## <a name="stateful-devices"></a>Zustandsbehaftete Geräte
Router analysieren zu Routingzwecken den IP-Header eines Pakets. Einige Geräte sehen sich das Paket noch eingehender an. Normalerweise werden bei diesen Geräten Layer4- (Transmission Control Protocol (TCP) oder User Datagram Protocol (UDP)) oder auch Layer7-Header (Anwendungsschicht) untersucht. Bei dieser Art von Geräten handelt es sich entweder um Sicherheitsgeräte oder um Geräte zur Bandbreitenoptimierung. 

Eine Firewall ist ein gängiges Beispiel für ein zustandsbehaftetes Gerät. Sie gewährt oder verhindert die Übermittlung eines Pakets über ihre Schnittstellen anhand von verschiedenen Aspekten wie Protokoll, TCP/UDP-Port und URL-Header. Dieser Grad der Paketuntersuchung führt für das Gerät zu einer hohen Verarbeitungsbelastung. Zur Verbesserung der Leistung prüft die Firewall das erste Paket eines Datenflusses. Ist das Paket zulässig, werden die Datenflussinformationen in der Zustandstabelle gespeichert. Alle nachfolgenden Pakete dieses Datenflusses werden auf Grundlage der ursprünglichen Entscheidung zugelassen. Beispielsweise erreicht ein Paket, das Teil eines vorhandenen Flusses ist, die Firewall. Wenn die Firewall nicht über vorherige Statusinformationen verfügt, wird das Paket von der Firewall verworfen.

## <a name="asymmetric-routing-with-expressroute"></a>Asymmetrisches Routing mit ExpressRoute
Wenn Sie mit Azure ExpressRoute eine Verbindung mit Microsoft herstellen, ändert sich Ihr Netzwerk wie folgt:

* Sie verfügen über mehrere Verbindungen mit Microsoft: Ihre vorhandene Internetverbindung und die ExpressRoute-Verbindung. Ein Teil des Datenverkehrs an Microsoft kann über die Internetverbindung gesendet und über ExpressRoute empfangen werden (oder umgekehrt).
* Über ExpressRoute erhalten Sie spezifischere IP-Adressen. Daher bevorzugen Router bei Datenverkehr von Ihrem Netzwerk an Microsoft für Dienste, die über ExpressRoute angeboten werden, immer ExpressRoute.

Wir sehen uns nun einige Fälle an, um die Auswirkung dieser beiden Änderungen auf das Netzwerk zu verdeutlichen. Angenommen, Sie verfügen lediglich über eine einzelne Verbindung mit dem Internet und nutzen alle Microsoft-Dienste über die Internetverbindung. Der Datenverkehr zwischen Ihrem Netzwerk und Microsoft wird über die gleiche Internetverbindung abgewickelt und durchläuft die Firewall. Die Firewall erfasst den Datenfluss beim Kontakt mit dem ersten Paket und lässt zurückgegebene Pakete zu, da der Datenfluss in der Zustandstabelle enthalten ist.

![Asymmetrisches Routing mit ExpressRoute](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

Anschließend aktivieren Sie ExpressRoute und nutzen Dienste, die von Microsoft über ExpressRoute angeboten werden. Alle anderen Dienste von Microsoft werden über die Internetverbindung genutzt. Sie stellen im Edgebereich der ExpressRoute-Verbindung eine separate Firewall bereit. Für bestimmte Dienste kündigt Microsoft Ihrem Netzwerk gegenüber per ExpressRoute spezifischere Präfixe an. Für diese Präfixe verwendet Ihre Routinginfrastruktur ExpressRoute als bevorzugten Pfad. Wenn Sie Ihre öffentlichen IP-Adressen Microsoft gegenüber nicht per ExpressRoute ankündigen, kommuniziert Microsoft mit Ihren öffentlichen IP-Adressen über die Internetverbindung. Beim Weiterleiten von Datenverkehr aus Ihrem Netzwerk an Microsoft wird ExpressRoute verwendet, und für den Datenverkehr von Microsoft in umgekehrter Richtung wird die Internetverbindung genutzt. Falls bei der Edgefirewall ein Antwortpaket für einen Datenfluss eingeht, der nicht in der Zustandstabelle gefunden wird, wird der eingehende Datenverkehr verworfen.

Wenn Sie für ExpressRoute und Internet den gleichen Pool für die Netzwerkadressübersetzung (NAT) ankündigen, treten ähnliche Probleme für die Clients in Ihrem Netzwerk mit privaten IP-Adressen auf. Anforderungen für Dienste wie Windows Update werden über die Internetverbindung abgewickelt, da IP-Adressen für diese Dienste nicht per ExpressRoute angekündigt werden. Der eingehende Datenverkehr wird hingegen über ExpressRoute zurückgegeben. Wenn Microsoft über die Internet- und die ExpressRoute-Verbindung eine IP-Adresse mit der gleichen Subnetzmaske erhält, wird ExpressRoute der Internetverbindung vorgezogen. Wenn einer Firewall oder einem anderen zustandsbehafteten Gerät im ExpressRoute zugewandten Edgebereich des Netzwerks noch keine Informationen zu dem Datenfluss vorliegen, werden die Pakete des betreffenden Datenflusses verworfen.

## <a name="asymmetric-routing-solutions"></a>Lösungen für asymmetrisches Routing
Sie haben zwei Möglichkeiten, wie Sie das Problem des asymmetrischen Routings lösen können. Eine Lösung basiert auf dem Routing, und die andere Lösung ist die quellenbasierte NAT (SNAT).

### <a name="routing"></a>Routing
Stellen Sie sicher, dass Ihre öffentlichen IP-Adressen gegenüber den entsprechenden WAN-Verbindungen (Wide Area Network) angekündigt werden. Wenn Sie beispielsweise die Internetverbindung für den Datenverkehr der Authentifizierung und ExpressRoute für Ihren E-Mail-Datenverkehr verwenden möchten, sollten Sie Ihre öffentlichen IP-Adressen für Active Directory-Verbunddienste (AD FS) nicht über ExpressRoute ankündigen. Stellen Sie auch sicher, dass Sie keinen lokalen AD FS-Server für IP-Adressen verfügbar machen, die der Router über ExpressRoute empfängt. Über ExpressRoute eingehende Routen sind spezifischer, sodass ExpressRoute als Pfad für Authentifizierungsdatenverkehr bevorzugt wird, der für Microsoft bestimmt ist. Dies führt zum asymmetrischen Routing.

Wenn Sie ExpressRoute für die Authentifizierung verwenden möchten, müssen öffentliche AD FS-IP-Adressen über ExpressRoute ohne NAT angekündigt werden. Datenverkehr, der von Microsoft stammt und für einen lokalen AD FS-Server bestimmt ist, verläuft dann über ExpressRoute. Für den Datenverkehr vom Kunden zu Microsoft in umgekehrter Richtung wird ExpressRoute verwendet, da dies die bevorzugte Route über die Internetverbindung ist.

### <a name="source-based-nat"></a>Quellenbasierte NAT
Eine weitere Möglichkeit zur Behandlung von Problemen durch asymmetrisches Routing ist die quellenbasierte NAT (SNAT). Beispiel: Sie haben die öffentliche IP-Adresse eines lokalen SMTP-Servers (Simple Mail Transfer Protocol) nicht über ExpressRoute angekündigt, da Sie für diese Art der Kommunikation die Internetverbindung nutzen möchten. Eine Anforderung, die von Microsoft stammt und für Ihren lokalen SMTP-Server bestimmt ist, wird über die Internetverbindung abgewickelt. Die eingehende Anforderung wird von der SNAT verarbeitet und an eine interne IP-Adresse weitergeleitet. Der Datenverkehr in umgekehrter Richtung vom SMTP-Server verläuft über die Edgefirewall (für die NAT) und nicht über ExpressRoute. Der Antwortdatenverkehr fließt über die Internetverbindung zurück.

![Netzwerkkonfiguration mit quellenbasierter NAT](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>Erkennung von asymmetrischem Routing
Wenn Sie sich vergewissern möchten, dass der Netzwerkdatenverkehr den erwarteten Pfad durchläuft, verwenden Sie am besten Traceroute. Soll an Microsoft gerichteter Datenverkehr von Ihrem lokalen SMTP-Server über den Internetpfad abgewickelt werden, verläuft die erwartete Traceroute zwischen SMTP-Server und Office 365. Das Ergebnis gibt Aufschluss darüber, ob der Datenverkehr Ihr Netzwerk tatsächlich in Richtung Internet (und nicht Richtung ExpressRoute) verlässt.

