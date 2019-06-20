---
title: Azure Front Door Service – Routingarchitektur | Microsoft-Dokumentation
description: In diesem Artikel wird der Aspekt der globalen Ansicht der Architektur von Azure Front Door Service erläutert.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 6af5e7c7d8788dffa8f144b2ee77c291ceda86c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736280"
---
# <a name="routing-architecture-overview"></a>Übersicht über die Routingarchitektur

Wenn Azure Front Door Service Ihre Clientanforderungen empfängt, werden diese entweder beantwortet (wenn die Zwischenspeicherung aktiviert ist) oder an das entsprechende Anwendungs-Back-End (als Reverseproxy) weitergeleitet.

</br>Sowohl bei der Weiterleitung an Azure Front Door Service als auch der Weiterleitung an Back-Ends besteht die Möglichkeit, den Datenverkehr zu optimieren.

## <a name = "anycast"></a>Auswählen der Azure Front Door Service-Umgebung für das Datenverkehrsrouting (Anycast)

Beim Routing zu den Azure Front Door Service-Umgebungen wird [Anycast](https://en.wikipedia.org/wiki/Anycast) sowohl für DNS-Datenverkehr (Domain Name System) als auch HTTP-Datenverkehr (Hypertext Transfer-Protokoll) genutzt. Benutzerdatenverkehr wird somit im Hinblick auf die Netzwerktopologie in die nächstgelegene Umgebung geleitet (geringste Anzahl von Hops). Diese Architektur bietet in der Regel bessere Roundtripzeiten für Endbenutzer (Maximierung der Vorteile von Split TCP). Azure Front Door Service unterteilt die Umgebungen in Primär- und Fallback-„Ringe“.  Der äußere Ring umfasst Umgebungen, die sich näher bei Benutzern befinden und daher niedrigere Wartezeiten bieten.  Der innere Ring enthält Umgebungen, die im Fall eines Problems das Failover für die Umgebung im äußeren Ring behandeln können. Der äußere Ring ist das bevorzugte Ziel für den gesamten Datenverkehr, der innere Ring ist jedoch zum Behandeln des Datenverkehrsüberlaufs vom äußeren Ring erforderlich. In Bezug auf die VIPs (virtuelle Internetprotokoll-Adressen) sind allen Front-End-Hosts oder Domänen, deren Anforderungen von Azure Front Door Service verarbeitet werden, eine von Umgebungen im inneren und äußeren Ring angekündigte primäre VIP sowie eine nur von Umgebungen im inneren Ring angekündigte Fallback-VIP zugewiesen. 

</br>Durch diese Gesamtstrategie wird sichergestellt, dass Anforderungen von Ihren Endbenutzern immer die nächstgelegene Azure Front Door Service-Umgebung erreichen und Datenverkehr bei einem Fehler in der bevorzugten Azure Front Door Service-Umgebung automatisch in die folgende nächstgelegene Umgebung geleitet wird.

## <a name = "splittcp"></a>Herstellen einer Verbindung mit der Azure Front Door Service-Umgebung (Split TCP)

[Split TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) ist ein Verfahren, bei dem eine Verbindung, die eine hohe Roundtripzeit haben würde, in kleine Teile aufgeteilt wird, um die Wartezeiten und TCP-Probleme zu reduzieren.  Indem die Azure Front Door Service-Umgebungen näher beim Endbenutzer platziert und TCP-Verbindungen innerhalb der Azure Front Door Service-Umgebung beendet werden, wird eine TCP-Verbindung mit hoher Roundtripzeit (RTT) zum Anwendungs-Back-End in zwei TCP-Verbindungen aufgeteilt. Aufgrund der kurzen Verbindung zwischen dem Endbenutzer und der Azure Front Door Service-Umgebung wird die Verbindung nicht über drei lange Roundtrips hergestellt, sondern über drei kurze Roundtrips, wodurch die Wartezeit reduziert wird.  Die lange Verbindung zwischen der Azure Front Door Service-Umgebung und dem Back-End kann vorab eingerichtet und bei mehreren Aufrufen von Endbenutzern wiederverwendet werden, wodurch sich wiederum die TCP-Verbindungszeit verkürzt.  Beim Herstellen einer SSL-/TLS-Verbindung (Transport Layer Security) vervielfacht sich der Effekt aufgrund der größeren Anzahl von Roundtrips zum Sichern der Verbindung.

## <a name="processing-request-to-match-a-routing-rule"></a>Verarbeiten der Anforderung zum Zuordnen einer Routingregel
Wenn eine Anforderung nach dem Herstellen der Verbindung und Durchführen eines SSL-Handshakes in eine Azure Front Door Service-Umgebung gelangt, ist die Zuordnung einer passenden Routingregel der erste Schritt. Bei dieser Zuordnung wird auf Grundlage aller Konfigurationen in Azure Front Door Service bestimmt, welcher spezifischen Routingregel die Anforderung entspricht. Erfahren Sie mehr darüber, wie Azure Front Door Service die [Routenzuordnung](front-door-route-matching.md) vornimmt.

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>Identifizieren der verfügbaren Back-Ends im Back-End-Pool für die Routingregel
Wenn Azure Front Door Service basierend auf der eingehenden Anforderung eine übereinstimmende Routingregel ermittelt hat und die Zwischenspeicherung nicht aktiviert ist, besteht der nächste Schritt darin, den Integritätsteststatus für den Back-End-Pool der zugeordneten Route zu pullen. Erfahren Sie, wie Azure Front Door Service die Back-End-Integrität mithilfe von [Integritätstests](front-door-health-probes.md) überwacht.

## <a name="forwarding-the-request-to-your-application-backend"></a>Weiterleiten der Anforderung an das Anwendungs-Back-End
Zum Schluss wird die Benutzeranforderung (sofern keine Zwischenspeicherung konfiguriert ist) basierend auf Ihrer Konfiguration der [Azure Front Door Service-Routingmethode](front-door-routing-methods.md) zum „besten“ Back-End weitergeleitet.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
