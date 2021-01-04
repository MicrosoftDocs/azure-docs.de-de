---
title: Netzwerkdienste von Azure Deutschland | Microsoft-Dokumentation
description: Vergleich der Features sowie Anleitungen für private Anbindung mit Azure Deutschland
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: aa1d648cf3e3f43d4a29c5dc199420f8d731848a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018490"
---
# <a name="azure-germany-networking-services"></a>Netzwerkdienste von Azure Deutschland

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="expressroute-private-connectivity"></a>ExpressRoute (private Verbindung)
Azure ExpressRoute ist in Azure Deutschland allgemein verfügbar. Weitere Informationen (u.a. zu Partnern und Peeringstandorten) finden Sie in der [globalen Dokumentation zu ExpressRoute](../expressroute/index.yml).

### <a name="variations"></a>Abweichungen

* Azure Deutschland-Kunden nutzen eine physisch isolierte Instanz über eine dedizierte Azure Deutschland ExpressRoute-Verbindung.
* Azure Deutschland bietet hohe lokale Verfügbarkeit und Stabilität, indem mehrere Regionspaare genutzt werden, die mindestens 400 km voneinander entfernt sind. 
* Die Azure Deutschland ExpressRoute-Anbindung wird standardmäßig aktiv/aktiv-redundant und mit Unterstützung für Bursting und einer Übertragungsrate von bis zu 10 GBit/s bereitgestellt.
* Azure Deutschland ExpressRoute-Standorte bieten optimierte Pfade (u.a. kürzeste Hops, niedrige Latenz und hohe Bandbreite) für Kunden und georedundante Azure Deutschland-Regionen.
* Weder nutzt die private Verbindung von Azure Deutschland ExpressRoute das Internet, noch ist sie darauf angewiesen.
* Die physische und logische Infrastruktur von Azure Deutschland ist physisch dediziert und vom internationalen Microsoft-Cloudnetzwerk getrennt.
* Azure Deutschland ExpressRoute stellt private Verbindungen mit Microsoft Azure Cloud Services bereit, jedoch nicht mit Microsoft 365- oder Dynamics 365-Clouddiensten.

### <a name="considerations"></a>Überlegungen
Es gibt zwei grundlegende Dienste, die private Netzwerkverbindungen für Azure Deutschland bereitstellen: ExpressRoute und VPN (in der Regel ein Site-to-Site-VPN).

Mit ExpressRoute können Sie private Verbindungen zu den Azure Deutschland-Rechenzentren und Ihrer lokalen Infrastruktur oder einer Housingumgebung herstellen. ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Sie bieten mehr Zuverlässigkeit, höhere Geschwindigkeiten und niedrigere Latenzen als konventionelle Internetverbindungen. In bestimmten Fällen lassen sich durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen lokalen Systemen und Azure erhebliche Kosteneinsparungen erzielen.   

Mit ExpressRoute stellen Sie an einem ExpressRoute-Standort, z.B. einer Einrichtung eines ExpressRoute-Exchange-Anbieters, Verbindungen mit Azure Deutschland her. Alternativ stellen Sie direkt aus Ihrem vorhandenen WAN eine Verbindung mit Azure Deutschland her, z.B. ein MPLS-VPN (Multi-Protocol Label Switching), das von einem Netzwerkdienstanbieter bereitgestellt wird.

Damit Netzwerkdienste Azure Deutschland-Kundenanwendungen und -lösungen unterstützen, wird dringend empfohlen, die private ExpressRoute-Anbindung für Verbindungen mit Azure Deutschland zu implementieren. Wenn Sie VPN-Verbindungen verwenden, beachten Sie Folgendes:

* Wenden Sie sich an die zuständige Person oder Stelle in Ihrem Unternehmen, um herauszufinden, ob Sie eine private Anbindung oder einen anderen sicheren Verbindungsmechanismus benötigen, und weitere mögliche Beschränkungen zu identifizieren.
* Entscheiden Sie, ob z.B. das Site-to-Site-VPN über eine Zone für private Verbindungen weitergeleitet werden soll.
* Bestellen Sie bei einem zugelassenen Netzwerkanbieter eine MPLS-Verbindung oder ein VPN.

Wenn Sie eine Architektur mit privaten Verbindungen nutzen, überprüfen Sie, ob die Anbindung an den Demarkationspunkt zum GN/I-Edgerouter-Demarkationspunkt (Gateway Network/Internet) für Azure Deutschland wie erforderlich implementiert wurde und gewartet wird. Dementsprechend muss Ihre Organisation die Netzwerkverbindung zwischen Ihrer lokalen Umgebung und dem GN/C-Edgerouter-Demarkationspunkt (Gateway Network/Customer) für Azure Deutschland einrichten.

Wenn Sie an einem Peeringstandort innerhalb der Azure Deutschland-Region per ExpressRoute eine Verbindung mit Microsoft herstellen, haben Sie Zugriff auf sämtliche Microsoft Azure-Clouddienste in allen Regionen innerhalb der deutschen Grenzen. Wenn Sie beispielsweise in Berlin per ExpressRoute eine Verbindung mit Microsoft Azure Deutschland herstellen, haben Sie Zugriff auf alle Microsoft-Clouddienste, die in Azure Deutschland gehostet werden.

Ausführliche Informationen zu Standorten und Partnern sowie eine detaillierte Liste mit ExpressRoute-Peeringstandorten für Azure Deutschland finden Sie auf der Registerkarte **Übersicht** in der [globalen Dokumentation zu ExpressRoute](../expressroute/index.yml).

Sie können mehrere ExpressRoute-Verbindungen erwerben. Wenn Sie über mehrere Verbindungen verfügen, ergeben sich für Sie aufgrund der Georedundanz daraus erhebliche Vorteile in Bezug auf Hochverfügbarkeit. Bei Verwendung mehrerer ExpressRoute-Verbindungen erhalten Sie dieselben Präfixe, die von Microsoft für die öffentlichen Peeringpfade bekannt gegeben werden. Dies bedeutet, dass Sie mehrere Pfade aus dem Netzwerk zu Microsoft nutzen können. Diese Situation kann unter Umständen dazu führen, dass in Ihrem Netzwerk suboptimale Routingentscheidungen getroffen werden. Daraus können sich für verschiedene Dienste suboptimale Konnektivitätsleistungen ergeben. Um weitere Informationen zu erhalten, wechseln Sie in der [globalen Dokumentation zu ExpressRoute](../expressroute/index.yml) zur Registerkarte **Anleitungen > Best Practices** und wählen **Routing optimieren** aus.

## <a name="support-for-load-balancer"></a>Load Balancer-Unterstützung
Azure Load Balancer ist in Azure Deutschland allgemein verfügbar. Weitere Informationen finden Sie in der [globalen Dokumentation zu Load Balancer](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manager"></a>Unterstützung für den Traffic Manager
Der Azure Traffic Manager ist in Azure Deutschland allgemein verfügbar. Weitere Informationen finden Sie in der [globalen Dokumentation zu Traffic Manager](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-virtual-network-peering"></a>Unterstützung für das Peering virtueller Netzwerke 
Das Peering von virtuellen Netzwerken ist in Azure Deutschland allgemein verfügbar. Weitere Informationen finden Sie in der [globalen Dokumentation zum Peering von virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>VPN Gateway-Unterstützung 
Azure VPN Gateway ist in Azure Deutschland allgemein verfügbar. Weitere Informationen finden Sie in der [globalen Dokumentation zu VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Nächste Schritte
Abonnieren Sie den [Azure Deutschland-Blog](/archive/blogs/azuregermany/), um weitere Informationen und Updates zu erhalten.