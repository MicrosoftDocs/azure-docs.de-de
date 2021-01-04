---
title: Häufig gestellte Fragen zu Azure ExpressRoute | Microsoft-Dokumentation
description: Die FAQs zu ExpressRoute enthalten Informationen zu unterstützten Azure-Diensten, Kosten, Daten und Verbindungen, SLAs, Anbietern und Standorten, Bandbreite sowie zusätzliche technische Details.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: duau
ms.openlocfilehash: ef5b065425fa05d016c1b1c1688cc28508f32d30
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462040"
---
# <a name="expressroute-faq"></a>ExpressRoute – FAQ

## <a name="what-is-expressroute"></a>Was ist ExpressRoute?

ExpressRoute ist ein Azure-Dienst, der es Ihnen ermöglicht, private Verbindungen zwischen Microsoft-Datencentern und der Infrastruktur bei Ihnen vor Ort oder in einer Kollokationsumgebung zu erstellen. ExpressRoute-Verbindungen erfolgen nicht über das öffentliche Internet und bieten eine höhere Sicherheit, größere Zuverlässigkeit und schnellere Geschwindigkeit bei weniger Latenz als herkömmliche Verbindungen über das Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Was sind die Vorteile von ExpressRoute und privaten Netzwerkverbindungen?

ExpressRoute-Verbindungen verlaufen nicht über das öffentliche Internet. Sie bieten eine höhere Sicherheit, größere Zuverlässigkeit und höhere Geschwindigkeiten bei konstanter Latenz als herkömmliche Verbindungen über das Internet. In einigen Fällen können durch die Verwendung von ExpressRoute-Verbindungen zum Übertragen von Daten zwischen lokalen Geräten und Azure auch drastische Kosteneinsparungen erzielt werden.

### <a name="where-is-the-service-available"></a>Wo ist der Dienst verfügbar?

Auf dieser Seite finden Sie Informationen zu Standorten und Verfügbarkeit des Diensts: [ExpressRoute-Partner und Peeringstandorte](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Wie kann ich ExpressRoute verwenden, um eine Verbindung mit Microsoft herzustellen, wenn ich keine Partnerschaft mit einem der ExpressRoute-Anbieterpartner habe?

Sie können einen regionalen Netzbetreiber auswählen und Ethernetverbindungen mit einem der unterstützten Exchange-Anbieterstandorte herstellen. Am Anbieterstandort können Sie dann eine Peeringverbindung mit Microsoft herstellen. Überprüfen Sie den letzten Abschnitt von [ExpressRoute-Partner und -Standorte](expressroute-locations.md) , um herauszufinden, ob Ihr Dienstanbieter an einem der Exchange-Standorte vorhanden ist. Sie können dann eine ExpressRoute-Verbindung über den Dienstanbieter anfordern, um eine Verbindung mit Azure herzustellen.

### <a name="how-much-does-expressroute-cost"></a>Wie viel kostet ExpressRoute?

Preisinformationen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-this-bandwidth-allocated-for-ingress-and-egress-traffic-separately"></a>Wenn ich für eine ExpressRoute-Leitung mit einer bestimmten Bandbreite bezahle, wird mir diese Bandbreite für eingehenden und ausgehenden Datenverkehr separat zugeordnet?

Ja, die Bandbreite für die ExpressRoute-Leitung gilt für beide Richtungen. Beispiel: Wenn Sie eine ExpressRoute-Leitung mit 200 MBit/s erwerben, erhalten Sie 200 MBit/s für eingehenden und 200 MBit/s für ausgehenden Datenverkehr.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Wenn ich für eine ExpressRoute-Verbindung mit einer bestimmten Bandbreite bezahle, muss die VPN-Verbindung, die ich von meinem Netzwerkdienstanbieter erworben habe, die gleiche Geschwindigkeit haben?

Nein. Sie können eine VPN-Verbindung beliebiger Geschwindigkeit bei Ihrem Dienstanbieter erwerben. Die Verbindung mit Azure ist jedoch auf die von Ihnen erworbene Bandbreite der ExpressRoute-Verbindung begrenzt.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-use-more-than-my-procured-bandwidth"></a>Wenn ich für eine ExpressRoute-Leitung mit einer bestimmten Bandbreite zahle, habe ich die Möglichkeit, mehr als die erworbene Bandbreite zu nutzen?

Ja, Sie können die erworbene Bandbreite verdoppeln, indem Sie die Bandbreite der sekundären Verbindung Ihrer ExpressRoute-Leitung nutzen. Die integrierte Redundanz Ihrer Leitung ist mit der primären und der sekundären Verbindung (die jeweils die erworbene Bandbreite bereitstellen) mit zwei Microsoft Enterprise Edge-Routern (MSEEs) konfiguriert. Die über die sekundäre Verbindung verfügbare Bandbreite kann bei Bedarf für zusätzlichen Datenverkehr genutzt werden. Da die sekundäre Verbindung zur Bereitstellung von Redundanz gedacht ist, bietet sie jedoch keine Garantien und sollte nicht über einen längeren Zeitraum für zusätzlichen Datenverkehr genutzt werden. Weitere Informationen zur Verwendung beider Verbindungen für die Übertragung von Datenverkehr finden Sie unter [Voranstellen von AS PATH](./expressroute-optimize-routing.md#solution-use-as-path-prepending).

Wenn Sie planen, nur die primäre Verbindung für die Übertragung von Datenverkehr zu verwenden, ist die Bandbreite für die Verbindung nicht veränderlich, und der Versuch einer Überbelegung führt zu vermehrten Paketverlusten. Wenn der Datenverkehr durch ein ExpressRoute-Gateway geleitet wird, ist die Bandbreite für die Gateway-SKU festgelegt und nicht burstfähig. Informationen zur Bandbreite der einzelnen Gateway-SKUs finden Sie unter [Informationen zu ExpressRoute-Gateways für virtuelle Netzwerke](expressroute-about-virtual-network-gateways.md#aggthroughput).

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Kann ich dieselbe private Netzwerkverbindung gleichzeitig mit virtuellen Netzwerken und anderen Azure-Diensten verwenden?

Ja. Sobald eine ExpressRoute-Verbindung eingerichtet ist, können Sie gleichzeitig auf Dienste in einem virtuellen Netzwerk und in anderen Azure-Diensten zugreifen. Über den privaten Peeringpfad können Sie eine Verbindung mit virtuellen Netzwerken herstellen, und über den Microsoft-Peeringpfad stellen Sie eine Verbindung mit anderen Diensten her.

### <a name="how-are-vnets-advertised-on-expressroute-private-peering"></a>Wie werden virtuelle Netzwerke beim privaten Peering in ExpressRoute angekündigt?

Das ExpressRoute-Gateway kündigt die *Adressräume* des virtuellen Azure-Netzwerks an. Auf Subnetzebene können Sie keine Adressen einschließen oder ausschließen. Es wird immer der Adressraum des virtuellen Netzwerks angekündigt. Wenn VNET-Peering verwendet wird und für das über Peering verbundene virtuelle Netzwerk das Verwenden eines Remotegateway möglich ist, wird der Adressraum des über Peering verbundenen virtuellen Netzwerks ebenfalls angekündigt.

### <a name="how-many-prefixes-can-be-advertised-from-a-vnet-to-on-premises-on-expressroute-private-peering"></a>Wie viele Präfixe können beim privaten Peering in ExpressRoute von einem virtuellen Netzwerk zu den lokalen Standorten angekündigt werden?

Es werden maximal 200 Präfixe für eine einzelne ExpressRoute-Verbindung oder über VNet-Peering mit Gatewaytransit angekündigt. Wenn Sie z. B. über 199 Adressräume in einem einzelnen virtuellen Netzwerk verfügen, die mit einer ExpressRoute-Verbindung verbunden sind, werden alle 199 Präfixe lokal angekündigt. Wenn Sie ein virtuelles Netzwerk aktiviert haben, das den Gatewaytransit mit einem Adressraum zulässt, und 150 virtuelle Spoke-Netzwerke über die Option „Remotegateway zulassen“ aktiviert haben, wird das mit dem Gateway bereitgestellte virtuelle Netzwerk 151 Präfixe für lokale Standorte ankündigen.

### <a name="what-happens-if-i-exceed-the-prefix-limit-on-an-expressroute-connection"></a>Was passiert, wenn ich das Präfixlimit für eine ExpressRoute-Verbindung überschreite?

Die Verbindung zwischen der ExpressRoute-Verbindung und dem Gateway (und ggf. den virtuellen Peeringnetzwerken mit Gatewaytransit) wird unterbrochen. Sie wird wieder eingerichtet, wenn das Präfixlimit nicht mehr überschritten wird.  

### <a name="can-i-filter-routes-coming-from-my-on-premises-network"></a>Kann ich Routen filtern, die von meinem lokalen Netzwerk ausgehen?

Die einzige Möglichkeit, Routen zu filtern/einzuschließen, ist über den lokalen Edgerouter. Benutzerdefinierte Routen, die sich auf bestimmte Weiterleitungen auswirken, können im virtuellen Netzwerk hinzugefügt werden. Dies ist jedoch statisch und ist nicht Bestandteil der BGP-Ankündigung.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Bietet ExpressRoute eine Service Level Agreement (SLA)?

Weitere Informationen finden Sie auf der Seite [ExpressRoute – SLA](https://azure.microsoft.com/support/legal/sla/).

## <a name="supported-services"></a>Unterstützte Dienste

ExpressRoute unterstützt [drei Routingdomänen](expressroute-circuit-peerings.md) für verschiedene Diensttypen: privates Peering, Microsoft-Peering und öffentliches Peering (veraltet).

### <a name="private-peering"></a>Privates Peering

**Unterstützt:**

* Virtuelle Netzwerke, einschließlich aller virtuellen Computer und Clouddienste

### <a name="microsoft-peering"></a>Microsoft-Peering

Wenn Ihre ExpressRoute-Verbindung für öffentliches Azure Microsoft-Peering aktiviert ist, können Sie über die Verbindung auf die [in Azure verwendeten öffentlichen IP-Adressbereiche](../virtual-network/public-ip-addresses.md#public-ip-addresses) zugreifen. Azure Microsoft-Peering ermöglicht den Zugriff auf Dienste, die derzeit in Azure gehostet werden (abhängig von der SKU Ihrer Verbindung mit geografischen Einschränkungen). Um die Verfügbarkeit für einen bestimmten Dienst zu überprüfen, können Sie die Dokumentation für diesen Dienst lesen, um festzustellen, ob ein reservierter Bereich für ihn veröffentlicht wurde. Überprüfen Sie anschließend die IP-Adressbereiche des Zieldiensts, und vergleichen Sie sie mit den Bereichen, die in der XML-Datei [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Azure-IP-Bereiche und -Diensttags – öffentliche Cloud) aufgeführt sind. Alternativ können Sie ein Supportticket für den betreffenden Dienst erstellen.

**Unterstützt:**

* [Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* Power BI ist über eine regionale Azure-Community verfügbar. Die Region Ihres Power BI-Mandanten können Sie [hier](/power-bi/service-admin-where-is-my-tenant-located) ermitteln.
* Azure Active Directory
* [Azure DevOps](https://blogs.msdn.microsoft.com/devops/2018/10/23/expressroute-for-azure-devops/) (Community der globalen Azure-Dienste)
* Öffentliche Azure-IP Adressen für IaaS (z. B. VMs, Virtual Network-Gateways, Lastenausgleiche)  
* Die meisten anderen Azure-Dienste werden ebenfalls unterstützt. Überprüfen Sie dies direkt für den Dienst, den Sie verwenden möchten.

**Nicht unterstützt:**

* CDN
* Azure Front Door
* [Windows Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/)
* Multi-Factor Authentication-Server (Vorversion)
* Traffic Manager

### <a name="public-peering"></a>Öffentliches Peering

Öffentliches Peering wurde für neue ExpressRoute-Verbindungen deaktiviert. Azure-Dienste sind jetzt über Microsoft-Peering verfügbar. Wenn Sie eine Verbindung haben, die vor dem Veralten des öffentlichen Peerings erstellt wurde, können Sie je nach den gewünschten Diensten Microsoft-Peering oder öffentliches Peering verwenden.

Weitere Informationen und Konfigurationsschritte für öffentliches Peering finden Sie unter [Erstellen und Verwalten von öffentlichem Peering in ExpressRoute](about-public-peering.md).

### <a name="why-i-see-advertised-public-prefixes-status-as-validation-needed-while-configuring-microsoft-peering"></a>Warum wird beim Konfigurieren des Microsoft-Peerings der Status „Angekündigte öffentliche Präfixe“ als „Überprüfung erforderlich“ angezeigt?

Microsoft überprüft, ob Ihnen die angegebenen „Angekündigten öffentlichen Präfixe“ und „Peer-ASN" (oder „Kunden-ASN“) in der Internet Routing Registry zugewiesen werden. Wenn Sie die öffentlichen Präfixe aus einer anderen Entität erhalten und die Zuweisung nicht mit der Routing Registry aufgezeichnet wird, wird die automatische Überprüfung nicht durchgeführt, sodass eine manuelle Überprüfung erforderlich ist. Wenn bei der automatischen Überprüfung ein Fehler auftritt, wird die Meldung „Überprüfung erforderlich“ angezeigt.

Wenn die Meldung „Überprüfung erforderlich“ angezeigt wird, sammeln Sie die Dokumente, in denen die öffentlichen Präfixe angezeigt werden, die Ihrer Organisation von der Entität zugewiesen wurden, die als Besitzer der Präfixe in der Routing Registry aufgeführt ist, und senden Sie diese Dokumente durch Öffnen eines Supporttickets zur manuellen Überprüfung, wie unten gezeigt.

![Screenshot: Neue Supportanfrage (Supportticket) für „Proof of ownership for public prefixes“ (Besitznachweis für öffentliche Präfixe)](./media/expressroute-faqs/ticket-portal-msftpeering-prefix-validation.png)

### <a name="is-dynamics-365-supported-on-expressroute"></a>Wird Dynamics 365 in ExpressRoute unterstützt?

Dynamics 365- und Common Data Service (CDS)-Umgebungen werden in Azure gehostet, sodass Kunden von der zugrunde liegenden ExpressRoute-Unterstützung für Azure-Ressourcen profitieren. Sie können eine Verbindung mit ihren Dienstendpunkten herstellen, wenn Ihr Routerfilter die Azure-Regionen umfasst, in denen Ihre Dynamics 365/CDS-Umgebungen gehostet werden.

> [!NOTE]
> [ExpressRoute Premium](#expressroute-premium) ist **nicht** für Dynamics 365-Konnektivität über Azure ExpressRoute erforderlich, wenn die ExpressRoute-Leitung innerhalb derselben [geopolitischen Region](./expressroute-locations-providers.md#expressroute-locations) bereitgestellt wird.

## <a name="data-and-connections"></a>Daten und Verbindungen

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Gibt es Limits hinsichtlich der Datenmenge, die ich mithilfe von ExpressRoute übertragen kann?

Wir haben keine Limits hinsichtlich der übertragenen Datenmenge festgelegt. In der [Preisübersicht](https://azure.microsoft.com/pricing/details/expressroute/) finden Sie Informationen zu Bandbreitentarifen.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>Welche Verbindungsgeschwindigkeiten werden von ExpressRoute unterstützt?

Unterstützte Bandbreiten:

50 MBit/s, 100 MBit/s, 200 MBit/s, 500 MBit/s, 1 GBit/s, 2 GBit/s, 5 GBit/s, 10 GBit/s

### <a name="which-service-providers-are-available"></a>Welche Dienstanbieter sind verfügbar?

Eine Liste der Dienstanbieter und Standorte finden Sie unter [ExpressRoute-Partner und -Standorte](expressroute-locations.md) .

## <a name="technical-details"></a>Technische Details

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Was sind die technischen Voraussetzungen für die Verbindung meines lokalen Standorts mit Azure?

Die Voraussetzungen finden Sie auf der Seite [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md).

### <a name="are-connections-to-expressroute-redundant"></a>Sind Verbindungen mit ExpressRoute redundant?

Ja. Jede ExpressRoute-Verbindung verfügt über ein redundantes Paar von Querverbindungen mit Hochverfügbarkeit.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Geht die Konnektivität verloren, wenn einer meiner ExpressRoute-Links ausfällt?

Wenn eine der Querverbindungen ausfällt, bleibt die Konnektivität bestehen. Es steht eine redundante Verbindung zur Verfügung, um die Last des Netzwerks zu bewältigen und die Hochverfügbarkeit Ihrer ExpressRoute-Verbindung zu gewährleisten. Sie können zudem eine Verbindung an einem anderen Peeringstandort erstellen, um Resilienz auf der Verbindungsebene zu erreichen.

### <a name="how-do-i-implement-redundancy-on-private-peering"></a>Wie implementiere ich Redundanz für privates Peering?

Mehrere ExpressRoute-Verbindungen von verschiedenen Peeringstandorten oder bis zu vier Verbindungen vom gleichen Peeringstandort können mit demselben virtuellen Netzwerk verbunden werden, um eine hohe Verfügbarkeit für den Fall zu gewährleisten, dass eine einzelne Verbindung nicht mehr verfügbar ist. Sie können der lokaler Verbindung eine [höhere Gewichtung zuweisen](./expressroute-optimize-routing.md#solution-assign-a-high-weight-to-local-connection), um eine spezifische Verbindung zu bevorzugen. Es wird dringend empfohlen, dass Kunden mindestens zwei ExpressRoute-Verbindungen einrichten, um Single Points of Failure zu vermeiden. 

Weitere Informationen zum Entwerfen für Hochverfügbarkeit finden Sie [hier](./designing-for-high-availability-with-expressroute.md) und zum Entwerfen für Notfallwiederherstellung [hier](./designing-for-disaster-recovery-with-expressroute-privatepeering.md).  

### <a name="how-i-do-implement-redundancy-on-microsoft-peering"></a>Wie implementiere ich Redundanz für Microsoft-Peering?

Es wird dringend empfohlen, dass Kunden, die Microsoft-Peering für den Zugriff auf öffentliche Azure-Dienste wie Azure Storage oder Azure SQL verwenden, sowie Kunden, die Microsoft-Peering für Microsoft 365 verwenden, mehrere Verbindungen an verschiedenen Peeringstandorten implementieren, um einen Single Point of Failure zu vermeiden. Kunden können entweder das gleiche Präfix auf beiden Verbindungen ankündigen und [AS PATH vorangestellt](./expressroute-optimize-routing.md#solution-use-as-path-prepending) verwenden, oder verschiedene Präfixe ankündigen, um den Pfad von lokalen Standorten aus zu bestimmen.

Weitere Informationen zum Entwerfen für Hochverfügbarkeit finden Sie [hier](./designing-for-high-availability-with-expressroute.md).

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Wie stelle ich Hochverfügbarkeit in einem mit ExpressRoute verbundenen virtuellen Netzwerk sicher?

Hochverfügbarkeit erreichen Sie, indem Sie bis zu vier ExpressRoute-Verbindungen vom gleichen Peeringstandort mit Ihrem virtuellen Netzwerk verbinden oder ExpressRoute-Verbindungen an verschiedenen Peeringstandorten (z. B. Singapur, Singapur2) mit Ihrem virtuellen Netzwerk verbinden. Sollte eine ExpressRoute-Verbindung ausfallen, erfolgt ein Failover auf eine andere ExpressRoute-Verbindung. Standardmäßig wird Datenverkehr, der Ihr virtuelles Netzwerk verlässt, auf der Grundlage von ECMP (Equal Cost Multi-path Routing) weitergeleitet. Mit der Verbindungsgewichtung können Sie einer Verbindung den Vorzug geben. Weitere Informationen finden Sie unter [Optimieren von ExpressRoute-Routing](expressroute-optimize-routing.md).

### <a name="how-do-i-ensure-that-my-traffic-destined-for-azure-public-services-like-azure-storage-and-azure-sql-on-microsoft-peering-or-public-peering-is-preferred-on-the-expressroute-path"></a>Wie stelle ich sicher, dass mein Datenverkehr mit öffentlichen Azure-Diensten wie Azure Storage und Azure SQL über Microsoft-Peering oder öffentliches Peering auf dem ExpressRoute-Pfad bevorzugt wird?

Sie müssen das Attribut *Local Preference* (Lokale Einstellung) auf Ihrem Router implementieren, um sicherzustellen, dass der Pfad vom lokalen Standort zu Azure immer von Ihrer ExpressRoute-Verbindung bevorzugt wird.

Weitere Informationen zur BGP-Pfadauswahl und allgemeinen Routerkonfigurationen finden Sie [hier](./expressroute-optimize-routing.md#path-selection-on-microsoft-and-public-peerings). 

### <a name="if-im-not-co-located-at-a-cloud-exchange-and-my-service-provider-offers-point-to-point-connection-do-i-need-to-order-two-physical-connections-between-my-on-premises-network-and-microsoft"></a><a name="onep2plink"></a>Wenn ich mich nicht am selben Standort wie ein Cloud Exchange befinde und mein Dienstanbieter Punkt-zu-Punkt-Verbindungen bereitstellt, muss ich zwei physische Verbindungen zwischen meinem lokalen Netzwerk und Microsoft anfordern?

Wenn Ihr Dienstanbieter zwei virtuelle Ethernetverbindungen über die physische Verbindung herstellen kann, benötigen Sie nur eine einzige physische Verbindung. Die physische Verbindung (z.B. Glasfaser) wird auf einem Layer 1-Gerät (L1) beendet (siehe Abbildung). Die zwei virtuellen Ethernet-Verbindungen werden mit unterschiedlichen VLAN-IDs markiert, eine für die primäre Verbindung und eine für die sekundäre Verbindung. Diese VLAN-IDs befinden sich im äußeren 802.1Q-Ethernet-Header. Der innere 802.1Q-Ethernet-Header (nicht dargestellt) wird einer bestimmten [ExpressRoute-Routingdomäne](expressroute-circuit-peerings.md) zugeordnet.

![Diagramm, in dem primäre und sekundäre virtuelle Verbindungen der Ebene 1 (L1) dargestellt sind, die die physische Verbindung zwischen den Switches am Kundenstandort und an einem ExpressRoute-Standort bilden](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Kann ich eines meiner VLANs mithilfe von ExpressRoute auf Azure erweitern?

Nein. Wir unterstützen keine Schicht-2-Konnektivitätserweiterungen in Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Kann mein Abonnement mehr als eine ExpressRoute-Verbindung umfassen?

Ja. Ihr Abonnement kann mehr als eine ExpressRoute-Verbindung enthalten. Standardmäßig sind nicht mehr als 10 zulässig. Wenden Sie sich an den Microsoft-Support, um das Limit bei Bedarf zu erhöhen.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Kann ich ExpressRoute-Verbindungen von verschiedenen Dienstanbietern haben?

Ja. Sie können ExpressRoute-Verbindungen von verschiedenen Dienstanbietern verwenden. Jede ExpressRoute-Verbindung ist nur einem Dienstanbieter zugeordnet. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-for-example-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Mir werden zwei ExpressRoute-Peeringstandorte in der gleichen Metro angezeigt (beispielweise Singapur und Singapur2). Welchen Peeringstandort sollte ich für die Erstellung meiner ExpressRoute-Verbindung verwenden?
Falls Ihr Dienstanbieter ExpressRoute an beiden Standorten anbietet, können Sie sich bei der Einrichtung von ExpressRoute in Abstimmung mit Ihrem Anbieter für einen beliebigen der beiden Standorte entscheiden. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Kann ich über mehrere ExpressRoute-Verbindungen am gleichen Standort verfügen? Kann ich sie mit dem gleichen virtuellen Netzwerk verknüpfen?

Ja. Sie können über mehrere ExpressRoute-Verbindungen bei den gleichen oder unterschiedlichen Dienstanbietern verfügen. Wenn der Standort über mehrere ExpressRoute-Peeringstandorte verfügt und die Verbindungen an verschiedenen Peeringstandorten erstellt werden, können Sie sie mit dem gleichen virtuellen Netzwerk verknüpfen. Wenn die Verbindungen an demselben Peeringstandort erstellt werden, können Sie bis zu vier Verbindungen mit demselben virtuellen Netzwerk verknüpfen.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Wie kann ich meine Virtual Networks mit einer ExpressRoute-Verbindung verbinden?

Die grundlegenden Schritte lauten wie folgt:

* Stellen Sie eine ExpressRoute-Verbindung her, und lassen Sie sie vom Dienstanbieter aktivieren.
* Die BGP-Peerings müssen von Ihnen oder vom Anbieter konfiguriert werden.
* Verknüpfen Sie das virtuelle Netzwerk mit der ExpressRoute-Verbindung.

Weitere Informationen finden Sie unter [ExpressRoute-Workflows für die Verbindungsbereitstellung und Verbindungszustände](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Gibt es Konnektivitätsgrenzen für meine ExpressRoute-Verbindung?

Ja. Der Artikel [ExpressRoute-Partner und -Standorte](expressroute-locations.md) bietet eine Übersicht über die Konnektivitätsgrenzen für eine ExpressRoute-Verbindung. Die Konnektivität für eine ExpressRoute-Verbindung ist auf eine einzelne geopolitische Region beschränkt. Die Konnektivität kann auf verschiedene geopolitische Regionen erweitert werden, wenn Sie das ExpressRoute Premium-Feature aktivieren.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Kann ich mehr als ein virtuelles Netzwerk mit einer ExpressRoute-Verbindung verknüpfen?

Ja. Bei einer standardmäßigen ExpressRoute-Verbindung sind bis zu 10 Verbindungen mit virtuellen Netzwerken möglich. Bei einer [ExpressRoute Premium-Verbindung](#expressroute-premium) sind es bis zu 100. 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Ich habe mehrere Azure-Abonnements, die Virtual Networks enthalten. Kann ich Virtual Networks, die sich in unterschiedlichen Abonnements befinden, mit einer ExpressRoute-Verbindung verbinden?

Ja. Sie können bis zu 10 virtuelle Netzwerke im selben Abonnement wie die Verbindung oder verschiedene Abonnements über eine einzige ExpressRoute-Leitung verbinden. Dieses Limit kann durch das Aktivieren des ExpressRoute Premium-Features erhöht werden. Beachten Sie, dass Verbindungs- und Bandbreitengebühren für die dedizierte Verbindung dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt werden. Außerdem nutzen alle virtuellen Netzwerke dieselbe Bandbreite.

Weitere Informationen finden Sie unter [Freigeben einer ExpressRoute-Verbindung für mehrere Abonnements](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Ich verfüge über mehrere Azure-Abonnements, die unterschiedlichen Azure Active Directory-Mandanten oder Enterprise Agreement-Registrierungen zugeordnet sind. Kann ich virtuelle Netzwerke in separaten Mandanten und Registrierungen mit einer einzelnen ExpressRoute-Verbindung verbinden, die sich nicht im gleichen Mandanten bzw. nicht in der gleichen Registrierung befindet?

Ja. ExpressRoute-Autorisierungen können ohne zusätzliche Konfigurationsschritte über die Grenzen von Abonnements, Mandanten und Registrierungen hinweg verwendet werden. Beachten Sie, dass Verbindungs- und Bandbreitengebühren für die dedizierte Verbindung dem Besitzer der ExpressRoute-Verbindung in Rechnung gestellt werden. Außerdem nutzen alle virtuellen Netzwerke dieselbe Bandbreite.

Weitere Informationen finden Sie unter [Freigeben einer ExpressRoute-Verbindung für mehrere Abonnements](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Sind virtuelle Netzwerke, die mit der gleichen Verbindung verbunden sind, voneinander isoliert?

Nein. Aus Routingsicht sind alle virtuellen Netzwerke, die mit derselben ExpressRoute-Verbindung verknüpft sind, Teil derselben Routingdomäne und nicht voneinander isoliert. Wenn Sie eine Isolierung der Routen benötigen, müssen Sie zuerst eine separate ExpressRoute-Verbindung erstellen.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Kann mehr als ein virtuelles Netzwerk mit mehr als einer ExpressRoute-Verbindung verbunden sein?

Ja. Sie können ein virtuelles Netzwerk mit bis zu vier ExpressRoute-Verbindungen an demselben oder an verschiedenen Peeringstandorten verknüpfen. 

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Kann ich von meinen virtuellen Netzwerken aus, die mit ExpressRoute-Verbindungen verbunden sind, ins Internet gehen?

Ja. Wenn Sie über die BGP-Sitzung keine Standardrouten (0.0.0.0/0) oder Internetroutenpräfixe angekündigt haben, können Sie von einem virtuellen Netzwerk aus, das mit einer ExpressRoute-Verbindung verknüpft ist, eine Internetverbindung herstellen.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Kann ich die Internetkonnektivität für virtuelle Netzwerke blockieren, die mit ExpressRoute-Verbindungen verbunden sind?

Ja. Sie können Standardrouten (0.0.0.0/0) ankündigen, um die Internetkonnektivität von virtuellen Computern in einem virtuellen Netzwerk zu blockieren und den gesamten Datenverkehr über die ExpressRoute-Verbindung zu leiten.

> [!NOTE]
> Wenn die angekündigte Route 0.0.0.0/0 aus den angekündigten Routen entfernt wird (beispielsweise aufgrund eines Ausfalls oder einer Fehlkonfiguration), stellt Azure eine [Systemroute](../virtual-network/virtual-networks-udr-overview.md#system-routes) zu den Ressourcen im verbundenen virtuellen Netzwerk bereit, um Konnektivität mit dem Internet zu bieten.  Damit sichergestellt ist, dass ausgehender Datenverkehr in das Internet blockiert ist, empfiehlt es sich, in allen Subnetzen eine Netzwerksicherheitsgruppe mit einer Verweigerungsregel für ausgehenden Internet-Datenverkehr zu platzieren.

Wenn Sie Standardrouten ankündigen, wird der Datenverkehr an Dienste, die über Microsoft-Peering (z. B. Azure Storage und SQL-Datenbank) angeboten werden, wieder an Ihren lokalen Standort zurückgeleitet. Sie müssen Ihre Router so konfigurieren, dass der Datenverkehr über den Microsoft-Peeringpfad oder über das Internet an Azure zurückgegeben wird. Wenn Sie einen Dienstendpunkt für den Dienst aktiviert haben, wird keine Weiterleitung des für den Dienst bestimmten Datenverkehrs an Ihren lokalen Standort erzwungen. Der Datenverkehr bleibt innerhalb des Azure-Backbonenetzwerks. Weitere Informationen zu Dienstendpunkten finden Sie unter [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json).

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Können virtuelle Netzwerke mit der gleichen ExpressRoute-Verbindung verknüpft sein und miteinander kommunizieren?

Ja. Virtuelle Maschinen in virtuellen Netzwerken, die mit der gleichen ExpressRoute-Verbindung verbunden sind, können miteinander kommunizieren.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Kann ich für Virtual Networks Standort-zu-Standort-Konnektivität zusammen mit ExpressRoute verwenden?

Ja. ExpressRoute kann zusammen mit Standort-zu-Standort-VPNs verwendet werden. Informationen finden Sie unter [Konfigurieren von parallel bestehenden ExpressRoute- und Site-to-Site-Verbindungen](expressroute-howto-coexist-resource-manager.md).

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Weshalb ist dem ExpressRoute-Gateway in einem virtuellen Netzwerk eine öffentliche IP-Adresse zugeordnet?

Die öffentliche IP-Adresse wird nur zur internen Verwaltung benötigt und stellt für Ihr virtuelles Netzwerk kein Sicherheitsrisiko dar.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Gibt es Limits hinsichtlich der Anzahl von Routen, die ich ankündigen kann?

Ja. Wir akzeptieren bis zu 4.000 Routenpräfixe für privates Peering und 200 für Microsoft-Peering. Sie können diesen Wert auf 10.000 Routen für privates Peering erhöhen, wenn Sie für ExpressRoute das Premium-Feature aktivieren.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Gibt es Einschränkungen hinsichtlich der IP-Adressbereiche, die ich über die BGP-Sitzung ankündigen kann?

Wir akzeptieren keine privaten Präfixe (RFC1918) in der Microsoft-BGP-Peeringsitzung. Wir akzeptieren für das Microsoft- und das private Peering alle Präfixgrößen (bis zu /32).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Was geschieht, wenn ich die BGP-Limits überschreite?

BGP-Sitzungen werden abgebrochen. Sie werden zurückgesetzt, sobald die Präfixanzahl unter das Limit fällt.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Wie lang ist die BGP-Haltezeit für ExpressRoute? Kann sie angepasst werden?

Die Haltezeit ist 180. Die Keep-Alive-Nachrichten werden alle 60 Sekunden gesendet. Diese Einstellungen werden auf Microsoft-Seite festgelegt und können nicht geändert werden. Sie haben die Möglichkeit, verschiedene Zeitgeber zu konfigurieren. Die BGP-Sitzungsparameter werden daraufhin entsprechend ausgehandelt.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Kann ich die Bandbreite einer ExpressRoute-Verbindung ändern?

Ja, Sie können versuchen, die Bandbreite Ihrer ExpressRoute-Verbindung im Azure-Portal oder mithilfe von PowerShell zu erhöhen. Wenn auf dem physischen Port, auf dem die Verbindung erstellt wurde, Kapazität verfügbar ist, ist die Änderung erfolgreich. 

Wenn bei der Änderung ein Fehler auftritt, bedeutet dies, dass die Kapazität auf dem aktuellen Port nicht ausreicht und dass Sie eine neue ExpressRoute-Verbindung mit der höheren Bandbreite erstellen müssen. Es kann aber auch bedeuten, dass an dem Standort keine zusätzliche Kapazität verfügbar ist. In dem Fall können Sie die Bandbreite nicht erhöhen. 

Wenden Sie sich auch an Ihren Konnektivitätsanbieter, um sicherzustellen, dass er die Drosselungen in seinen Netzwerken aktualisiert, damit die Erhöhung der Bandbreite unterstützt wird. Sie können Bandbreite Ihrer ExpressRoute-Verbindung jedoch nicht verringern. Stattdessen müssen Sie eine neue ExpressRoute-Verbindung mit der geringeren Bandbreite erstellen und die alte Verbindung löschen.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Wie kann ich die Bandbreite einer ExpressRoute-Verbindung ändern?

Sie können die Bandbreite der ExpressRoute-Verbindung mithilfe der REST-API oder des PowerShell-Cmdlets aktualisieren.

## <a name="expressroute-premium"></a>ExpressRoute Premium

### <a name="what-is-expressroute-premium"></a>Was ist ExpressRoute Premium?

ExpressRoute Premium ist eine Sammlung der folgenden Features:

* Erhöhung des Grenzwerts für Routingtabellen von 4.000 auf 10.000 Routen für privates Peering.
* Erhöhung der Anzahl von VNETs und ExpressRoute Global Reach-Verbindungen, die für eine ExpressRoute-Verbindung aktiviert werden können (Standardwert: 10). Weitere Informationen finden Sie in der Tabelle [ExpressRoute-Grenzwerte](#limits).
* Konnektivität mit Microsoft 365
* Globale Konnektivität über das Microsoft-Kernnetzwerk. Jetzt können Sie ein VNET in einer geopolitischen Region mit einer ExpressRoute-Verbindung in einer anderen Region verknüpfen.<br>
    **Beispiele:**

    *  Sie können ein in „Europa, Westen“ erstelltes VNET mit einer ExpressRoute-Verbindung verknüpfen, die in Silicon Valley erstellt wurde. 
    *  Beim Microsoft-Peering werden Präfixe anderer geopolitischer Regionen so angekündigt, dass Sie sich z. B. über eine Verbindung im Silicon Valley mit SQL Azure in der Region „Europa, Westen“ verbinden können.


### <a name="how-many-vnets-and-expressroute-global-reach-connections-can-i-enable-on-an-expressroute-circuit-if-i-enabled-expressroute-premium"></a><a name="limits"></a>Wie viele VNETs und ExpressRoute Global Reach-Verbindungen kann ich für eine ExpressRoute-Verbindung aktivieren, wenn ich über ExpressRoute Premium verfüge?

In den folgenden Tabellen sind die ExpressRoute-Grenzwerte und die Anzahl von VNETs und ExpressRoute Global Reach-Verbindungen pro ExpressRoute-Verbindung angegeben:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Wie aktiviere ich ExpressRoute Premium?

Die Features von ExpressRoute Premium können aktiviert werden, sobald Sie das Feature selbst aktiviert haben. Durch Aktualisieren des Verbindungszustand können sie beendet werden. Sie können ExpressRoute Premium zum Zeitpunkt der Verbindungserstellung aktivieren, oder Sie können die REST-API oder das PowerShell-Cmdlet aufrufen.

### <a name="how-do-i-disable-expressroute-premium"></a>Wie deaktiviere ich ExpressRoute Premium?

Sie können ExpressRoute Premium deaktivieren, indem Sie die REST-API oder das PowerShell-Cmdlet aufrufen. Stellen Sie sicher, dass Sie eine Skalierung gemäß Ihrer Konnektivitätsanforderungen durchgeführt haben, um die Standardgrenzwerte einzuhalten, bevor Sie ExpressRoute Premium deaktivieren. Wenn Ihre Nutzung über den Standardgrenzwerten liegt, tritt bei der Anforderung zur Deaktivierung von ExpressRoute Premium ein Fehler auf.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Kann ich die Features der Premium-Features auswählen, die ich nutzen möchte?

Nein. Sie können die Features nicht auswählen. Wenn Sie ExpressRoute Premium aktivieren, werden alle Features aktiviert.

### <a name="how-much-does-expressroute-premium-cost"></a>Wie viel kostet ExpressRoute Premium?

Angaben zu den Preisen finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/expressroute/) .

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Fallen für ExpressRoute Premium neben dem standardmäßigen ExpressRoute-Tarif zusätzliche Gebühren an?

Ja. Für ExpressRoute Premium werden neben den ExpressRoute-Verbindungsgebühren und den Gebühren, die vom Konnektivitätsanbieter erhoben werden, zusätzliche Gebühren berechnet.

## <a name="expressroute-local"></a>ExpressRoute Local
### <a name="what-is-expressroute-local"></a>Was ist ExpressRoute Local?
Expressroute Local ist neben der Standard-SKU und der Premium-SKU eine SKU der ExpressRoute-Verbindung. Ein wichtiges Feature von ExpressRoute Local ist, dass Sie über eine Local-Verbindung an einem ExpressRoute-Peeringstandort nur Zugriff auf eine oder zwei Azure-Regionen an demselben Standort bzw. in dessen Nähe haben. Mit einer Standard-Verbindung haben Sie dagegen Zugriff auf alle Azure-Regionen eines geopolitischen Gebiets, und mit einer Premium-Verbindung auf alle Azure-Regionen weltweit. 

### <a name="what-are-the-benefits-of-expressroute-local"></a>Welche Vorteile hat ExpressRoute Local?
Sie müssen für Ihre ExpressRoute-Standard- bzw. -Premium-Verbindung zwar für ausgehende Datenübertragungen zahlen, aber für Ihre ExpressRoute Local-Verbindung fallen hierfür keine separaten Kosten an. Anders ausgedrückt: Die Gebühren für die Datenübertragung sind im Preis von ExpressRoute Local enthalten. ExpressRoute Local ist eine wirtschaftlichere Lösung, falls Sie über eine große Menge von zu übertragenden Daten verfügen und Ihre Daten über eine private Verbindung an einen ExpressRoute-Peeringstandort in der Nähe der gewünschten Azure-Regionen verlagern können. 

### <a name="what-features-are-available-and-what-are-not-on-expressroute-local"></a>Welche Features sind für ExpressRoute Local verfügbar?
Eine Local-Verbindung verfügt über die gleichen Features wie eine ExpressRoute-Standard-Verbindung, aber es gelten folgende Ausnahmen:
* Zugriffsbereich für Azure-Regionen (wie oben beschrieben)
* ExpressRoute Global Reach ist für Local nicht verfügbar

Für ExpressRoute Local gelten auch die gleichen Einschränkungen in Bezug auf Ressourcen (z. B. die Anzahl von VNETs pro Verbindung) wie für Standard. 

### <a name="where-is-expressroute-local-available-and-which-azure-regions-is-each-peering-location-mapped-to"></a>Wo ist ExpressRoute Local verfügbar, und welchen Azure-Regionen sind die einzelnen Peeringstandorte zugeordnet?
ExpressRoute Local ist an den Peeringstandorten verfügbar, in deren Nähe sich ein oder zwei Azure-Regionen befinden. Dies gilt nicht für einen Peeringstandort, für den sich im Bundesland/Kanton bzw. im Land/in der Region keine Azure-Region befindet. Die genauen Zuordnungen finden Sie auf der [Seite mit den Standorten](expressroute-locations-providers.md).  

## <a name="expressroute-for-microsoft-365"></a>ExpressRoute für Microsoft 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-microsoft-365-services"></a>Wie erstelle ich eine ExpressRoute-Verbindung mit Microsoft 365-Diensten?

1. Überprüfen Sie die Anforderungen auf der Seite mit den [ExpressRoute-Voraussetzungen](expressroute-prerequisites.md), um sicherzustellen, dass Sie die Anforderungen erfüllen.
2. Um sicherzustellen, dass Ihre Konnektivitätsanforderungen erfüllt werden, überprüfen Sie die Liste der Dienstanbieter und Standorte im Artikel [ExpressRoute-Partner und -Standorte](expressroute-locations.md).
3. Planen Sie Ihre Kapazitätsanforderungen mithilfe der Informationen unter [Netzwerkplanung und Leistungsoptimierung für Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance).
4. Befolgen Sie die Schritte in den Workflows zum Einrichten der Konnektivität unter [ExpressRoute-Workflows für die Verbindungsbereitstellung und Verbindungszustände](expressroute-workflows.md).

> [!IMPORTANT]
> Stellen Sie sicher, dass das ExpressRoute Premium-Add-On aktiviert ist, wenn Sie die Verbindung mit Microsoft 365-Diensten konfigurieren.
> 
> 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-microsoft-365-services"></a>Unterstützen meine vorhandenen ExpressRoute-Verbindungen die Konnektivität mit Microsoft 365-Diensten?

Ja. Ihre bestehende ExpressRoute-Verbindung kann so konfiguriert werden, dass sie eine Konnektivität mit Microsoft 365-Diensten unterstützt. Stellen Sie sicher, dass Sie über ausreichend Kapazität für die Verbindung mit Microsoft 365-Diensten verfügen, und dass das Premium-Add-On aktiviert ist. Unter [Netzwerkplanung und Leistungsoptimierung für Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance) finden Sie Hilfe zur Planung Ihrer Konnektivitätsanforderungen. Siehe auch [Erstellen und Ändern einer ExpressRoute-Verbindung](expressroute-howto-circuit-classic.md).

### <a name="what-microsoft-365-services-can-be-accessed-over-an-expressroute-connection"></a>Welche Microsoft 365-Dienste sind über eine ExpressRoute-Verbindung zugänglich?

Eine aktuelle Liste der von ExpressRoute unterstützten Dienste finden Sie unter [URLs und IP-Adressbereiche von Microsoft 365](/microsoft-365/enterprise/urls-and-ip-address-ranges).

### <a name="how-much-does-expressroute-for-microsoft-365-services-cost"></a>Wie viel kostet ExpressRoute für Microsoft 365-Dienste?

Microsoft 365-Dienste erfordern ein aktiviertes Premium-Add-On. Informationen zu den Kosten finden Sie auf der Seite mit den [Preisdetails](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="what-regions-is-expressroute-for-microsoft-365-supported-in"></a>In welchen Regionen wird ExpressRoute für Microsoft 365 unterstützt?

Weitere Informationen finden Sie unter [ExpressRoute-Partner und Standorte](expressroute-locations.md).

### <a name="can-i-access-microsoft-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Kann ich über das Internet auf Microsoft 365 zugreifen, selbst wenn für meine Organisation ExpressRoute konfiguriert wurde?

Ja. Microsoft 365-Dienstendpunkte sind über das Internet erreichbar, selbst wenn ExpressRoute für Ihr Netzwerk konfiguriert wurde. Erkundigen Sie sich beim Netzwerkteam Ihrer Organisation, ob das Netzwerk an Ihrem Standort für die Herstellung einer ExpressRoute-Verbindung mit Microsoft 365-Diensten konfiguriert ist.

### <a name="how-can-i-plan-for-high-availability-for-microsoft-365-network-traffic-on-azure-expressroute"></a>Wie kann ich bei Microsoft 365-Netzwerkdatenverkehr über Azure ExpressRoute für Hochverfügbarkeit sorgen?
Lesen Sie die Empfehlung unter [Hohe Verfügbarkeit und Failover mit Azure ExpressRoute](/microsoft-365/enterprise/network-planning-with-expressroute).

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Kann ich über eine Azure US Government-ExpressRoute-Verbindung auf Government Community Cloud-Dienste (GCC) von Office 365 US Government zugreifen?

Ja. Office 365 GCC-Dienstendpunkte sind über Azure US Government-ExpressRoute erreichbar. Sie müssen jedoch zunächst im Azure-Portal ein Supportticket öffnen, um die Präfixe bereitzustellen, die Sie Microsoft ankündigen möchten. Nachdem das Support-Ticket erfolgreich bearbeitet wurde, wird die Konnektivität mit den GCC-Diensten von Office 365 hergestellt. 

## <a name="route-filters-for-microsoft-peering"></a>Routenfilter für das Microsoft-Peering

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>Ich aktiviere das Microsoft-Peering zum ersten Mal. Welche Routen werden angezeigt?

Es werden keine Routen angezeigt. Sie müssen Ihrer Verbindung einen Routenfilter zuordnen, um Präfixankündigungen zu starten. Weitere Anweisungen finden Sie unter [Konfigurieren von Routenfiltern für das Microsoft-Peering](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Ich habe das Microsoft-Peering aktiviert und möchte nun Exchange Online aktivieren, allerdings wird die Fehlermeldung angezeigt, dass ich dazu nicht autorisiert bin.

Jeder Kunde kann bei der Verwendung von Routenfiltern das Microsoft-Peering aktivieren. Um Microsoft 365-Dienste nutzen zu können, müssen Sie jedoch nach wie vor von Microsoft 365 autorisiert werden.

### <a name="i-enabled-microsoft-peering-prior-to-august-1-2017-how-can-i-take-advantage-of-route-filters"></a>Ich habe Microsoft-Peering vor dem 1. August 2017 aktiviert. Wie kann ich Routenfilter nutzen?

Ihre vorhandene Verbindung kündigt weiterhin die Präfixe für Microsoft 365 an. Wenn Sie Ankündigungen für öffentliche Azure-Präfixe über das gleiche Microsoft-Peering hinzufügen möchten, können Sie einen Routenfilter erstellen, die anzukündigenden Dienste auswählen (einschließlich der benötigten Microsoft 365) und den Filter an Ihr Microsoft-Peering anfügen. Weitere Anweisungen finden Sie unter [Konfigurieren von Routenfiltern für das Microsoft-Peering](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>An einem Standort besitze ich die Möglichkeit zum Microsoft-Peering. Nun versuche ich, es an einem anderen Standort zu aktivieren, sehe jedoch keine Präfixe.

* Beim Microsoft-Peering von ExpressRoute-Verbindungen, die vor dem 1. August 2017 konfiguriert wurden, werden alle Dienstpräfixe über das Microsoft-Peering angekündigt, auch wenn keine Routenfilter definiert sind.

* Beim Microsoft-Peering von ExpressRoute-Verbindungen, die am oder nach dem 1. August 2017 konfiguriert wurden, werden Präfixe erst angekündigt, wenn der Verbindung ein Routenfilter hinzugefügt wurde. Standardmäßig werden dabei keine Präfixe angezeigt.

## <a name="expressroute-direct"></a><a name="expressRouteDirect"></a>ExpressRoute Direct

[!INCLUDE [ExpressRoute Direct](../../includes/expressroute-direct-faq-include.md)]

## <a name="global-reach"></a><a name="globalreach"></a>Global Reach

[!INCLUDE [Global Reach](../../includes/expressroute-global-reach-faq-include.md)]

## <a name="privacy"></a>Datenschutz

### <a name="does-the-expressroute-service-store-customer-data"></a>Speichert der ExpressRoute-Dienst Kundendaten?

Nein.