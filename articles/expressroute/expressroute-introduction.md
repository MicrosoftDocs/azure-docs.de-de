---
title: 'Erweitern Ihres lokalen Netzwerks auf Azure über eine private Verbindung: ExpressRoute-Übersicht – Azure | Microsoft-Dokumentation'
description: In der technischen Übersicht über ExpressRoute wird beschrieben, wie eine ExpressRoute-Verbindung funktioniert und wie Sie Ihr lokales Netzwerk über eine private Verbindung auf Azure ausdehnen.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: overview
ms.date: 05/20/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 6d83cb76abad3923dc7f0473f4a609938093d990
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730495"
---
# <a name="expressroute-overview"></a>ExpressRoute-Übersicht
Mit ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf die Microsoft Cloud ausdehnen. Mit ExpressRoute können Sie Verbindungen zu Microsoft-Clouddiensten herstellen, z.B. Microsoft Azure, Office 365 und Dynamics 365.

Die Konnektivität kann über ein Any-to-Any-Netzwerk (IP VPN), ein Point-to-Point-Ethernet-Netzwerk oder eine virtuelle Querverbindung über einen Konnektivitätsanbieter in einer Co-Location-Einrichtung bereitgestellt werden. ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Auf diese Weise können ExpressRoute-Verbindungen gleichmäßige Latenz sowie höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit als herkömmliche Verbindungen über das Internet bieten. Informationen zum Verbinden Ihres Netzwerks mit Microsoft mithilfe von ExpressRoute finden Sie unter [ExpressRoute-Konnektivitätsmodelle](expressroute-connectivity-models.md).

![Übersicht über ExpressRoute-Verbindungen](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Hauptvorteile

* Layer 3-Konnektivität zwischen Ihrem lokalen Netzwerk und der Microsoft Cloud über einen Konnektivitätsanbieter. Die Konnektivität kann über ein Any-to-Any-Netzwerk (IPVPN), eine Point-to-Point-Ethernet-Verbindung oder eine virtuelle Querverbindung über einen Ethernet-Exchange bereitgestellt werden.
* Verbindung mit Microsoft-Clouddiensten in allen Regionen einer geopolitischen Region.
* Globale Konnektivität mit Microsoft-Diensten in allen Regionen mit dem ExpressRoute Premium-Add-On.
* Dynamisches Routing zwischen Ihrem Netzwerk und Microsoft per BGP.
* Integrierte Redundanz an jedem Peeringort, um eine höhere Zuverlässigkeit zu erzielen.
* [SLA](https://azure.microsoft.com/support/legal/sla/)zur Verbindungsbetriebszeit.
* QoS-Unterstützung für Skype for Business.

Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

## <a name="features"></a>Features

### <a name="layer-3-connectivity"></a>Layer 3-Konnektivität
Microsoft verwendet BGP, ein Branchenstandardprotokoll für dynamisches Routing, um Routen zwischen Ihrem lokalen Netzwerk, Ihren Instanzen in Azure und öffentlichen Microsoft-Adressen auszutauschen. Wir richten für Ihr Netzwerk mehrere BGP-Sitzungen für unterschiedliche Datenverkehrsprofile ein. Weitere Informationen finden Sie im Artikel [ExpressRoute-Verbindung und Routingdomänen](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redundanz
Jede ExpressRoute-Verbindung besteht aus zwei Verbindungen mit zwei Microsoft Enterprise-Edgeroutern (MSEEs) vom Konnektivitätsanbieter/Ihrem Netzwerk-Edge. Microsoft erfordert eine BGP-Dualverbindung vom Konnektivitätsanbieter/von Ihrem Netzwerk-Edge (jeweils eine pro MSEE). Sie können sich auch dafür entscheiden, auf Ihrer Seite keine redundanten Geräte/Ethernet-Verbindungen bereitzustellen. Allerdings verwenden Konnektivitätsanbieter redundante Geräte, um sicherzustellen, dass Ihre Verbindungen auf redundante Weise an Microsoft übergeben werden. Eine redundante Layer 3-Konnektivitätskonfiguration ist eine Anforderung, die erfüllt sein muss, damit unsere [SLA](https://azure.microsoft.com/support/legal/sla/) gültig ist.

### <a name="connectivity-to-microsoft-cloud-services"></a>Verbindung mit Microsoft-Clouddiensten
ExpressRoute-Verbindungen ermöglichen den Zugriff auf die folgenden Dienste:
* Microsoft Azure-Dienste
* Microsoft Office 365-Dienste
* Microsoft Dynamics 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Auf der Seite [ExpressRoute – FAQ](expressroute-faqs.md) finden Sie eine ausführliche Liste mit Diensten, die über ExpressRoute unterstützt werden.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Verbindung mit allen Regionen einer geopolitischen Region
Sie können über einen unserer [Peeringstandorte](expressroute-locations.md) eine Verbindung mit Microsoft herstellen und so auf alle Regionen innerhalb der jeweiligen geopolitischen Region zugreifen.

Wenn Sie beispielsweise in Amsterdam eine ExpressRoute-Verbindung mit Microsoft herstellen, haben Sie Zugriff auf alle Microsoft-Clouddienste, die in Nord- und Westeuropa gehostet werden. Der Artikel [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md) enthält eine Übersicht über die geopolitischen Regionen, dazugehörigen Microsoft-Cloudregionen und entsprechenden ExpressRoute-Peeringstandorte.

### <a name="global-connectivity-with-expressroute-premium"></a>Globale Konnektivität mit ExpressRoute Premium
Sie können [ExpressRoute Premium](expressroute-faqs.md) aktivieren, um die Konnektivität über geopolitische Grenzen hinweg zu erweitern. Wenn Sie beispielsweise in Amsterdam eine ExpressRoute-Verbindung mit Microsoft herstellen, haben Sie Zugriff auf alle Microsoft-Clouddienste, die in allen Regionen weltweit gehostet werden (ausgenommen nationale Clouds). Sie können auf Dienste, die in Südamerika oder Australien bereitgestellt werden, auf die gleiche Weise zugreifen wie auf Regionen in Nord- und Westeuropa.

### <a name="local-connectivity-with-expressroute-local"></a>Lokale Konnektivität mit ExpressRoute Local
Sie können Daten kostengünstig übertragen, indem Sie die [lokale SKU](expressroute-faqs.md) aktivieren, wenn Sie Ihre Daten an einen ExpressRoute-Speicherort in der Nähe Ihrer gewünschten Azure-Region bringen können. Bei Local ist die Datenübertragung in der ExpressRoute-Portgebühr enthalten. 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Ausdehnen der lokalen Konnektivität mit ExpressRoute Global Reach
Sie können ExpressRoute Global Reach zum Austausch von Daten zwischen Ihren lokalen Standorten aktivieren, indem Sie Ihre ExpressRoute-Leitungen verbinden. Wenn Sie beispielsweise über ein privates Rechenzentrum in Kalifornien verfügen, das mit ExpressRoute im Silicon Valley verbunden ist, und über ein weiteres privates Rechenzentrum in Texas, das mit ExpressRoute in Dallas verbunden ist, können Sie mit ExpressRoute Global Reach Ihre privaten Rechenzentren über zwei ExpressRoute-Leitungen miteinander verbinden. Ihr rechenzentrumsübergreifender Datenverkehr durchläuft das Netzwerk von Microsoft.

Weitere Informationen finden Sie unter [ExpressRoute Global Reach](expressroute-global-reach.md).
### <a name="rich-connectivity-partner-ecosystem"></a>Partner-Ökosystem mit umfassender Konnektivität
ExpressRoute verfügt über ein ständig wachsendes Ökosystem aus Konnektivitätsanbietern und Systemintegratorpartnern. Aktuelle Informationen hierzu finden Sie im Artikel [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md).

### <a name="connectivity-to-national-clouds"></a>Verbindung mit nationalen Clouds
Microsoft betreibt isolierte Cloudumgebungen für spezielle geopolitische Regionen und Kundensegmente. Eine Liste mit nationalen Clouds und Anbietern finden Sie auf der Seite [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md).

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct bietet Kunden die Möglichkeit, sich direkt mit dem globalen Netzwerk von Microsoft zu verbinden, und zwar an strategisch über die ganze Welt verteilten Peeringstandorten. ExpressRoute Direct bietet duale 100-Gbit/s-Konnektivität, die eine Aktiv/Aktiv-Konnektivität nach Maß unterstützt.

ExpressRoute Direct bietet u.a. folgende Leistungsmerkmale:

* Umfangreiche Datenerfassung in Diensten wie Azure Storage und Cosmos DB
* Physische Isolierung für Branchen, die Regulierung sowie dedizierte und isolierte Konnektivität erfordern, beispielsweise das Bankwesen, Behörden und der Einzelhandel.
* Präzise Steuerung der Leitungsverteilung basierend auf Unternehmensbereichen

Weitere Informationen über ExpressRoute finden Sie unter [Informationen zu ExpressRoute Direct](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Bandbreitenoptionen
Sie können ExpressRoute-Verbindungen für einen großen Bandbreitenbereich erwerben. Im Anschluss finden Sie die unterstützten Bandbreiten. Fragen Sie Ihren Konnektivitätsanbieter nach den Bandbreiten, die er unterstützt.

* 50 MBit/s
* 100 MBit/s
* 200 MBit/s
* 500 MBit/s
* 1 GBit/s
* 2 GBit/s
* 5 GBit/s
* 10 GBit/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamische Skalierung der Bandbreite
Sie können die ExpressRoute-Verbindungsbandbreite erhöhen (Best Effort-Basis), ohne Ihre Verbindungen zu unterbrechen. Weitere Informationen finden Sie unter [Ändern einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md#modify).

### <a name="flexible-billing-models"></a>Flexible Abrechnungsmodelle
Sie können ein Abrechnungsmodell auswählen, das für Sie am besten geeignet ist. Wählen Sie zwischen den unten aufgeführten Abrechnungsmodellen. Weitere Informationen finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

* **Datenflatrate:** Die Abrechnung basiert auf einer monatlichen Gebühr, in der alle eingehenden und ausgehenden Datenübertragungen enthalten sind.
* **Datentaktung:** Die Abrechnung basiert auf einer monatlichen Gebühr, in der alle eingehenden Datenübertragungen enthalten sind. Ausgehende Datenübertragungen werden pro GB der Datenübertragung in Rechnung gestellt. Die Datenübertragungsraten variieren je nach Region.
* **ExpressRoute Premium-Add-On:** ExpressRoute Premium ist ein Add-On für die ExpressRoute-Leitung. Mit dem ExpressRoute Premium-Add-On werden die folgenden Funktionen bereitgestellt: 
  * Erhöhte Routengrenzwerte für öffentliches und privates Azure-Peering von 4.000 Routen auf 10.000 Routen.
  * Globale Konnektivität für Dienste. Eine ExpressRoute-Verbindung, die in einer beliebigen Region (ausgenommen nationale Clouds) erstellt wird, verfügt über Zugriff auf Ressourcen aller anderen Regionen weltweit. Beispielsweise kann auf ein virtuelles Netzwerk, das in Westeuropa erstellt wird, über eine ExpressRoute-Verbindung zugegriffen werden, die im Silicon Valley bereitgestellt wird.
  * Erhöhte Anzahl von VNet-Links pro ExpressRoute-Verbindung von 10 auf einen höheren Grenzwert, je nach Bandbreite der Verbindung.

## <a name="faq"></a>Häufig gestellte Fragen
Häufig gestellte Fragen zu ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über [ExpressRoute-Konnektivitätsmodelle](expressroute-connectivity-models.md).
* Informieren Sie sich über ExpressRoute-Verbindungen und Routingdomänen. Siehe [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md).
* Suchen Sie nach einem Service Provider. Siehe [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md).
* Stellen Sie sicher, dass alle Voraussetzungen erfüllt werden. Informationen finden Sie unter [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).
* Sehen Sie sich die Anforderungen für [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) und [QoS](expressroute-qos.md) an.
* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  * [Erstellen und Ändern einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md)
  * [Erstellen und Ändern des Peerings für eine ExpressRoute-Verbindung](expressroute-howto-routing-portal-resource-manager.md)
  * [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-portal-resource-manager.md)
* Erfahren Sie mehr über die anderen zentralen [Netzwerkfunktionen](../networking/networking-overview.md) in Azure.
