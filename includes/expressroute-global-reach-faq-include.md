---
title: include file
description: include file
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 03/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317a480c13c5c6e00653fd61878a379df3f65ac4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177827"
---
### <a name="what-is-expressroute-global-reach"></a>Was ist ExpressRoute Global Reach?

ExpressRoute Global Reach ist ein Azure-Dienst, der Ihre lokalen Netzwerke über das globale Netzwerk von Microsoft mit dem ExpressRoute-Dienst verbindet. Wenn Sie beispielsweise ein privates Rechenzentrum in Kalifornien haben, das mit ExpressRoute im Silicon Valley verbunden ist, und ein weiteres privates Rechenzentrum in Texas haben, das mit ExpressRoute in Dallas mit ExpressRoute Global Reach verbunden ist, können Sie Ihre privaten Rechenzentren über die zwei ExpressRoute-Verbindungen miteinander verbinden, und Ihr rechenzentrumsübergreifender Datenverkehr durchläuft das Netzwerk-Backbone von Microsoft.

### <a name="how-do-i-enable-or-disable-expressroute-global-reach"></a>Wie aktiviere oder deaktiviere ich ExpressRoute Global Reach?

Sie aktivieren ExpressRoute Global Reach, indem Sie Ihre ExpressRoute-Leitungen miteinander verbinden. Sie deaktivieren das Feature durch Trennen der Leitungen. [Hier finden Sie Informationen zur Konfiguration](../articles/expressroute/expressroute-howto-set-global-reach.md).

### <a name="do-i-need-expressroute-premium-for-expressroute-global-reach"></a>Benötige ich für ExpressRoute Premium für ExpressRoute Global Reach?

Wenn sich Ihre ExpressRoute-Leitungen in derselben geopolitischen Region befinden, benötigen Sie ExpressRoute Premium nicht, um sie miteinander zu verbinden. Wenn sich zwei ExpressRoute-Leitungen in verschiedenen geopolitischen Regionen befinden, benötigen Sie ExpressRoute Premium für beide Leitungen, um Konnektivität zwischen ihnen zu ermöglichen. 

### <a name="how-will-i-be-charged-for-expressroute-global-reach"></a>Wie wird mir ExpressRoute Global Reach in Rechnung gestellt?

ExpressRoute ermöglicht die Konnektivität zwischen Ihrem lokalen Netzwerk und Microsoft Cloud Services. ExpressRoute Global Reach ermöglicht die Konnektivität zwischen Ihren lokalen Netzwerken über Ihre vorhandenen ExpressRoute-Leitungen und unter Nutzung des globalen Netzwerks von Microsoft. ExpressRoute Global Reach wird getrennt vom vorhandenen ExpressRoute-Dienst in Rechnung gestellt. Für die Aktivierung dieser Funktion für jede ExpressRoute-Leitung fällt eine Add-On-Gebühr an. Durch ExpressRoute Global Reach ermöglichter Datenverkehr zwischen Ihren lokalen Netzwerken anhand einer Ausgangsrate an der Quelle und einer Eingangsrate am Ziel in Rechnung gestellt. Die Preise basieren auf die Zone, in der sich die Leitungen befinden.

### <a name="where-is-expressroute-global-reach-supported"></a>Wo wird ExpressRoute Global Reach unterstützt?

ExpressRoute Global Reach wird in/an [ausgewählten Ländern/Regionen oder Orten unterstützt](../articles/expressroute/expressroute-global-reach.md). Die ExpressRoute-Leitungen müssen an den Peeringstandorten in diesen Ländern/Regionen oder an diesen Orten eingerichtet werden.

### <a name="i-have-more-than-two-on-premises-networks-each-connected-to-an-expressroute-circuit-can-i-enable-expressroute-global-reach-to-connect-all-of-my-on-premises-networks-together"></a>Ich habe mehr als zwei lokale Netzwerke, die jeweils mit einer ExpressRoute-Leitungen verbunden sind. Kann ich ExpressRoute Global Reach aktivieren, um alle meine lokalen Netzwerke miteinander zu verbinden?

Ja, das ist möglich, sofern sich die Leitungen in den unterstützten Ländern/Regionen befinden. Sie müssen jeweils zwei ExpressRoute-Leitungen verbinden. Zum Erstellen eines voll vermaschten Netzwerks müssen Sie alle Leitungspaare aufzählen und die Konfiguration wiederholen. 

### <a name="can-i-enable-expressroute-global-reach-between-two-expressroute-circuits-at-the-same-peering-location"></a>Kann ich ExpressRoute Global Reach zwischen zwei ExpressRoute-Leitungen am gleichen Peeringstandort aktivieren?

Nein. Die zwei Leitungen müssen sich an verschiedenen Peeringstandorten befinden. Verfügt eine Metro in einem unterstützten Land bzw. einer unterstützten Region über mehr als einen Peeringstandort, können Sie die an verschiedenen Peeringstandorten in dieser Metro erstellten ExpressRoute-Leitungen miteinander verbinden. 

### <a name="if-expressroute-global-reach-is-enabled-between-circuit-x-and-circuit-y-and-between-circuit-y-and-circuit-z-will-my-on-premises-networks-connected-to-circuit-x-and-circuit-z-talk-to-each-other-via-microsofts-network"></a>Wenn ExpressRoute Global Reach zwischen Leitung X und Leitung Y sowie zwischen Leitung Y und Leitung Z aktiviert ist, können dann meine lokalen Netzwerke, die mit Leitung X und Leitung Z verbunden sind, über das Microsoft-Netzwerk miteinander kommunizieren?

Nein. Um die Konnektivität zwischen zwei beliebigen Ihrer lokalen Netzwerke zu aktivieren, müssen Sie die entsprechenden ExpressRoute-Leitungen explizit verbinden. Im obigen Beispiel müssen Sie Leitung X und Leitung Z verbinden. 

### <a name="what-is-the-network-throughput-i-can-expect-between-my-on-premises-networks-after-i-enable-expressroute-global-reach"></a>Welchen Netzwerkdurchsatz zwischen meinen lokalen Netzwerken kann ich nach dem Aktivieren von ExpressRoute Global Reach erwarteten?

Der Netzwerkdurchsatz zwischen Ihren lokalen Netzwerken, die durch ExpressRoute Global Reach aktiviert sind, ist durch die kleinere der beiden ExpressRoute-Leitungen begrenzt. Für Datenverkehr vom lokalen Standort zu Azure und von Standort zu Standort wird dieselbe Verbindung genutzt, und er unterliegen derselben Obergrenze für die Bandbreite. 

### <a name="with-expressroute-global-reach-what-are-the-limits-on-the-number-of-routes-i-can-advertise-and-the-number-of-routes-i-will-receive"></a>Welche Grenzwerte zur Anzahl von Routen, die angekündigt werden können und die ich erhalten kann, gelten für ExpressRoute Global Reach?

Die Anzahl von Routen, die Sie für Microsoft beim privaten Azure-Peering ankündigen können, liegt weiterhin bei 4.000 für eine Standard-Leitung und 10.000 für eine Premium-Leitung. Die Anzahl von Routen, die Sie von Microsoft für das private Azure-Peering erhalten, ist die Summe der Routen Ihrer virtuellen Azure-Netzwerke und der Routen Ihrer anderen lokalen Netzwerke, die per ExpressRoute Global Reach verbunden sind. Stellen Sie sicher, dass Sie für Ihren lokalen Router einen geeigneten Höchstwert für Präfixe festlegen. 

### <a name="what-is-the-sla-for-expressroute-global-reach"></a>Welche SLA gilt für ExpressRoute Global Reach?

Für ExpressRoute Global Reach gilt die gleiche [Verfügbarkeits-SLA](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/) wie für den regulären ExpressRoute-Dienst.
