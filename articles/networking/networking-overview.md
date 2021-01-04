---
title: Übersicht über Azure-Netzwerkdienste
description: Erfahren Sie etwas über Netzwerkdienste in Azure, darunter Konnektivitäts-, Anwendungsschutz-, Anwendungsbereitstellungs- und Netzwerküberwachungs-Dienste.
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: kumud
ms.openlocfilehash: f49a340a004a4aef37bcae9e3ae1c2b02ae030b9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913081"
---
# <a name="azure-networking-services-overview"></a>Übersicht über Azure-Netzwerkdienste

Die Azure-Netzwerkdienste bieten eine Vielzahl von Netzwerkfunktionen, die einzeln oder zusammen verwendet werden können. Klicken Sie auf eine der folgenden Schlüsselfunktionen, um weitere Informationen zu erhalten:
- [**Konnektivitätsdienste:**](#connect) Vernetzen Sie Azure-Ressourcen und lokale Ressourcen mithilfe einer Kombination folgender Netzwerkdienste in Azure: Virtual Network (VNET), Virtual WAN, ExpressRoute, VPN Gateway, Virtual Network NAT Gateway, Azure DNS, Peering Service und Azure Bastion.
- [**Anwendungsschutzdienste**](#protect): Diese Dienste schützen Ihrer Anwendungen mithilfe einer Kombination folgender Netzwerkdienste in Azure: Private Link, DDoS Protection, Firewall, Netzwerksicherheitsgruppen, Web Application Firewall und Virtual Network-Endpunkte.
- [**Dienste zur Anwendungsbereitstellung**](#deliver): Stellen Sie Anwendungen im Azure-Netzwerk mithilfe einer Kombination aus folgenden Netzwerkdiensten in Azure bereit: Content Delivery Network (CDN), Azure Front Door Service, Traffic Manager, Application Gateway, Internet Analyzer und Load Balancer.
- [**Netzwerküberwachung**](#monitor): Überwachen Sie Ihre Netzwerkressourcen mithilfe einer Kombination folgender Netzwerkdienste in Azure: Network Watcher, ExpressRoute-Monitor, Azure Monitor oder Terminalzugangspunkte für virtuelle Netzwerke.

## <a name="connectivity-services"></a><a name="connect"></a>Konnektivitätsdienste
 
In diesem Abschnitt werden Dienste beschrieben, die Konnektivität zwischen Azure-Ressourcen, einem lokalen Netzwerk und Azure-Ressourcen sowie Konnektivität zwischen Filialen in Azure ermöglichen: virtuelles Netzwerk (VNET), ExpressRoute, VPN Gateway, Virtual WAN, NAT Gateway für virtuelles Netzwerk, Azure DNS, Azure Peering-Dienst und Azure Bastion.


### <a name="virtual-network"></a><a name="vnet"></a>Virtual Network

Azure Virtual Network (VNET) ist der grundlegende Baustein für Ihr privates Netzwerk in Azure. VNETs können für folgende Zwecke verwendet werden:
- **Kommunikation zwischen Azure-Ressourcen** Sie können virtuelle Computer und verschiedene andere Arten von Azure-Ressourcen in einem virtuellen Netzwerk bereitstellen, wie beispielsweise Azure App Service-Umgebungen, den Azure Kubernetes Service (AKS) und Azure Virtual Machine Scale Sets. Eine vollständige Liste mit Azure-Ressourcen, die Sie in einem virtuellen Netzwerk bereitstellen können, finden Sie unter [Integration virtueller Netzwerke für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md).
- **Gegenseitige Kommunikation:** Sie können virtuelle Netzwerke mittels VNet-Peering miteinander verbinden und so die Kommunikation zwischen Ressourcen in beiden virtuellen Netzwerken ermöglichen. Die verbundenen virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen befinden. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md).
- **Kommunikation mit dem Internet:** Alle Ressourcen in einem VNET können standardmäßig in ausgehender Richtung mit dem Internet kommunizieren. Zur Kommunikation in eingehender Richtung muss der entsprechenden Ressource eine öffentliche IP-Adresse oder eine öffentliche Load Balancer-Instanz zugewiesen werden. Die [öffentlichen IP-Adressen](../virtual-network/virtual-network-public-ip-address.md) und die öffentliche [Load Balancer-Instanz](../load-balancer/load-balancer-overview.md) können auch zum Verwalten der ausgehenden Verbindungen verwendet werden.
- **Kommunikation mit lokalen Netzwerken:** Sie können Ihre lokalen Computer und Netzwerke mithilfe von [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../expressroute/expressroute-introduction.md) mit einem virtuellen Netzwerk vernetzen.

Weitere Informationen finden Sie unter [Was ist Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
Mit ExpressRoute können Sie Ihre lokalen Netzwerke über eine private Verbindung, die von einem Konnektivitätsanbieter bereitgestellt wird, auf die Microsoft-Cloud erweitern. Diese Verbindung ist privat. Der Datenverkehr wird nicht über das Internet übertragen. Mit ExpressRoute können Sie Verbindungen zu Microsoft Cloud Services wie Microsoft Azure, Microsoft 365 und Dynamics 365 herstellen.  Weitere Informationen finden Sie unter [Was ist ExpressRoute?](../expressroute/expressroute-introduction.md).

:::image type="content" source="./media/networking-overview/expressroute-connection-overview.png" alt-text="Azure ExpressRoute" border="false":::

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
VPN Gateway unterstützt Sie beim Herstellen verschlüsselter lokaler Verbindungen mit Ihrem virtuellen Netzwerk von lokalen Standorten aus. Sie können auch verschlüsselte Verbindungen zwischen VNETs herstellen. Für VPN Gateway-Verbindungen sind verschiedene Konfigurationen wie Site-to-Site, Point-to-Site oder VNet-to-VNet verfügbar.
Das folgende Diagramm veranschaulicht mehrere Site-to-Site-VPN-Verbindungen mit demselben virtuellen Netzwerk.

:::image type="content" source="./media/networking-overview/vpngateway-multisite-connection-diagram.png" alt-text="Site-to-Site-Verbindungen über Azure VPN Gateway":::

Weitere Informationen zu den verschiedenen VPN-Verbindungen finden Sie unter [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Virtual WAN
Der Netzwerkdienst Azure Virtual WAN bietet optimierte und automatisierte Branch-Konnektivität mit und durch Azure. Azure-Regionen dienen als Hubs, die Sie auswählen können, um Branches mit ihnen zu verbinden. Sie können das Azure-Backbone nutzen, um auch Verbindungen mit Branches herzustellen und die Branch-to-VNET-Konnektivität zu nutzen. Azure Virtual WAN vereint zahlreiche Azure-Cloudkonnektivitätsdienste wie Site-to-Site-VPN, ExpressRoute und Point-to-Site-Benutzer-VPN auf einer einzigen Betriebsoberfläche. Die Konnektivität mit Azure-VNets wird mithilfe virtueller Netzwerkverbindungen hergestellt. Weitere Informationen finden Sie unter [Was ist Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md).

:::image type="content" source="./media/networking-overview/virtualwan1.png" alt-text="Virtual WAN-Diagramm":::

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
Azure DNS ist ein Hostingdienst für DNS-Domänen, der eine Namensauflösung mittels Microsoft Azure-Infrastruktur bietet. Indem Sie Ihre Domänen in Azure hosten, können Sie Ihre DNS-Einträge unter Verwendung der gleichen Anmeldeinformationen, APIs, Tools und Abrechnungsabläufe wie bei Ihren anderen Azure-Diensten verwalten. Weitere Informationen finden Sie unter [Was ist Azure DNS?](../dns/dns-overview.md)

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion ist ein neuer vollständig verwalteter PaaS-Dienst, den Sie in Ihrem virtuellen Netzwerk bereitstellen können. Dieser Dienst bietet sichere und nahtlose RDP- und SSH-Verbindungen mit Ihren virtuellen Computern über TLS direkt im Azure-Portal. Beim Herstellen einer Verbindung über Azure Bastion benötigen Ihre virtuellen Computer keine öffentliche IP-Adresse. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](../bastion/bastion-overview.md).

:::image type="content" source="./media/networking-overview/architecture.png" alt-text="Azure Bastion-Architektur":::

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Virtual Network NAT Gateway
Virtual Network NAT (Network Address Translation, Netzwerkadressenübersetzung) vereinfacht für virtuelle Netzwerke die Einrichtung von ausschließlich ausgehenden Internetverbindungen. Bei der Konfiguration in einem Subnetz werden für die gesamte Konnektivität in ausgehender Richtung die von Ihnen angegebenen statischen öffentlichen IP-Adressen verwendet. Die ausgehende Konnektivität ist möglich, ohne dass ein Lastenausgleich oder öffentliche IP-Adressen direkt virtuellen Computern zugeordnet werden. Weitere Informationen finden Sie unter [Was ist Virtual Network NAT?](../virtual-network/nat-overview.md).

:::image type="content" source="./media/networking-overview/flow-map.png" alt-text="Virtual Network NAT Gateway":::

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a> Azure Peering Service
Azure Peering Service verbessert die Kundenkonnektivität mit Microsoft Cloud Services wie Microsoft 365, Dynamics 365, SaaS-Diensten, Azure oder sämtlichen anderen Microsoft-Diensten, auf die über das öffentliche Internet zugegriffen werden kann. Weitere Informationen finden Sie unter [Was ist Azure Peering Service?](../peering-service/about.md).

### <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zones

Azure Edge Zone umfasst eine Gruppe von Angeboten aus Microsoft Azure, die eine Datenverarbeitung in der Nähe des Benutzers ermöglichen. Sie können virtuelle Computer (VMs), Container und andere ausgewählte Azure-Dienste in Edge Zones bereitstellen, um die Anforderungen von Anwendungen hinsichtlich niedriger Wartezeit und hohem Durchsatz zu erfüllen. Weitere Informationen finden Sie unter [Was ist Azure Edge Zones?](edge-zones-overview.md).

### <a name="azure-orbital"></a><a name="orbital"></a>Azure Orbital

Azure Orbital ist eine vollständig verwaltete, cloudbasierte Erdfunkstelle-as-a-Service, mit der Sie mit Ihrem Raumfahrzeug oder mit Satellitenkonstellationen kommunizieren, Datendownlinks und -uplinks erstellen, Daten in der Cloud verarbeiten, Dienste mit Azure-Diensten in individuellen Szenarien verketten und Produkte für Ihre Kunden generieren können. Dieses System baut auf der globalen Infrastruktur und dem globalen Glasfasernetzwerk mit geringer Latenz von Azure auf. Weitere Informationen finden Sie unter [Was ist Azure Orbital?](azure-orbital-overview.md).

:::image type="content" source="./media/azure-orbital-overview/orbital-communications-use-flow.png" alt-text="Abbildung der Kommunikation in Azure Orbital":::

## <a name="application-protection-services"></a><a name="protect"></a>Anwendungsschutzdienste

In diesem Abschnitt werden die Netzwerkdienste in Azure beschrieben, mit denen Sie Ihre Netzwerkressourcen schützen können. Schützen Sie Ihre Anwendungen mithilfe einer Kombination folgender Netzwerkdienste in Azure: DDoS-Schutz, Private Link, Firewall, Web Application Firewall, Netzwerksicherheitsgruppen und VNET-Dienstendpunkte.

### <a name="ddos-protection"></a><a name="ddosprotection"></a>DDoS Protection 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) bietet Gegenmaßnahmen gegen die komplexesten DDoS-Bedrohungen. Der Dienst bietet erweiterte DDoS-Risikominderungsfunktionen für die Anwendungen und Ressourcen, die in Ihren virtuellen Netzwerken bereitgestellt werden. Außerdem haben Kunden, die Azure DDoS Protection verwenden, Zugriff auf DDoS Rapid Response-Support, durch den sich DDoS-Experten bei einem aktiven Angriff einschalten können.

:::image type="content" source="./media/networking-overview/ddos-protection.png" alt-text="DDoS Protection":::

### <a name="azure-private-link"></a><a name="privatelink"></a>Azure Private Link
Mit [Azure Private Link](../private-link/private-link-overview.md) können Sie über einen privaten Endpunkt in Ihrem virtuellen Netzwerk auf Azure-PaaS-Dienste (beispielsweise Azure Storage und SQL Database) sowie auf in Azure gehostete kundeneigene Dienste/Partnerdienste zugreifen.
Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst verläuft über das Microsoft-Backbone-Netzwerk. Es ist nicht mehr erforderlich, dass Sie Ihren Dienst über das öffentliche Internet verfügbar machen. Sie können Ihren eigenen Private Link-Dienst in Ihrem virtuellen Netzwerk erstellen und Ihren Kunden zur Verfügung stellen.

:::image type="content" source="./media/networking-overview/private-endpoint.png" alt-text="Privater Endpunkt – Übersicht":::

### <a name="azure-firewall"></a><a name="firewall"></a>Azure Firewall
Azure Firewall ist ein verwalteter, cloudbasierter Netzwerksicherheitsdienst, der Ihre Azure Virtual Network-Ressourcen schützt. Mit Azure Firewall können Sie Richtlinien zur Anwendungs- und Netzwerkkonnektivität übergreifend für Abonnements und virtuelle Netzwerke zentral erstellen, erzwingen und protokollieren. Azure Firewall verwendet eine statische öffentliche IP-Adresse für Ihre virtuellen Netzwerkressourcen, die es außenstehenden Firewalls ermöglicht, Datenverkehr aus Ihrem virtuellen Netzwerk zu identifizieren. 

Weitere Informationen zu Azure Firewall finden Sie in der [zugehörigen Dokumentation](../firewall/overview.md).

:::image type="content" source="./media/networking-overview/firewall-threat.png" alt-text="Firewallübersicht":::

### <a name="web-application-firewall"></a><a name="waf"></a>Web Application Firewall
[Azure Web Application Firewall](../web-application-firewall/overview.md) (WAF) schützt Ihre Webanwendungen vor gängigen Webexploits und Sicherheitsrisiken wie die Einschleusung von SQL-Befehlen und Cross-Site Scripting. WAF bietet über verwaltete Regeln integrierten Schutz vor den zehn häufigsten Websicherheitslücken (laut OWASP). Darüber hinaus können Kunden benutzerdefinierte Regeln konfigurieren, bei denen es sich um vom Kunden verwaltete Regeln handelt, um zusätzlichen Schutz anhand des Quell-IP-Adressbereichs und der Anforderungsattribute wie Header, Cookies, Formulardatenfelder oder Abfragezeichenfolgenparameter zu bieten.

Kunden können [Azure WAF mit Application Gateway](../application-gateway/waf-overview.md) einsetzen, um Entitäten im öffentlichen und privaten Adressraum regional zu schützen. Kunden können auch [Azure WAF mit Front Door](../frontdoor/waf-overview.md) einsetzen, um öffentliche Endpunkte am Netzwerkedge zu schützen.

:::image type="content" source="./media/networking-overview/waf-overview.png" alt-text="Web Application Firewall":::

### <a name="network-security-groups"></a><a name="nsg"></a>Netzwerksicherheitsgruppen
Sie können Netzwerkdatenverkehr von und zu Azure-Ressourcen in einem virtuellen Azure-Netzwerk mithilfe einer Netzwerksicherheitsgruppe filtern. Weitere Informationen finden Sie unter [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Dienstendpunkte
Mit Dienstendpunkten von virtuellen Netzwerken (VNETs) werden der Bereich privater Adressen Ihres virtuellen Netzwerks und die Identität Ihres VNET über eine direkte Verbindung auf die Azure-Dienste erweitert. Endpunkte ermöglichen es Ihnen, Ihre kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Der Datenverkehr aus Ihrem VNET an den Azure-Dienst verbleibt immer im Backbone-Netzwerk von Microsoft Azure. Weitere Informationen finden Sie unter [VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md).

:::image type="content" source="./media/networking-overview/vnet-service-endpoints-overview.png" alt-text="VNET-Dienstendpunkte":::

## <a name="application-delivery-services"></a><a name="deliver"></a>Anwendungsbereitstellungsdienste

In diesem Abschnitt werden die Netzwerkdienste in Azure beschrieben, die bei der Bereitstellung von Anwendungen helfen – Content Delivery Network, Azure Front Door Service, Traffic Manager, Load Balancer und Application Gateway.

### <a name="content-delivery-network"></a><a name="cdn"></a>Content Delivery Network
Azure Content Delivery Network (CDN) bietet Entwicklern eine globale Lösung zur schnellen Übermittlung von Inhalten mit hoher Bandbreite an Benutzer. Hierzu werden Inhalte auf physischen Knoten zwischengespeichert, die strategisch auf der ganzen Welt verteilt sind. Weitere Informationen zu Azure CDN finden Sie unter [Azure Content Delivery Network](../cdn/cdn-overview.md).

:::image type="content" source="./media/networking-overview/cdn-overview.png" alt-text="Azure CDN":::

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Azure Front Door Service
Über Azure Front Door Service können Sie das globale Routing für Ihren Webdatenverkehr definieren, verwalten und überwachen, indem Sie es für beste Leistung und sofortiges globales Failover bei Hochverfügbarkeit optimieren. Mit Front Door können Sie Ihre globalen (mehrere Regionen) Consumer- und Unternehmensanwendungen in stabile und hochleistungsfähige personalisierte moderne Anwendungen, APIs und Inhalte transformieren, die eine globale Zielgruppe mit Azure erreichen. Weitere Informationen finden Sie unter [Azure Front Door](../frontdoor/front-door-overview.md).

:::image type="content" source="./media/networking-overview/front-door-visual-diagram.png" alt-text="Front Door Service: Übersicht":::

### <a name="traffic-manager"></a><a name="trafficmanager"></a>Traffic Manager

Azure Traffic Manager ist ein DNS-basierter Lastenausgleich für Datenverkehr, der es Ihnen ermöglicht, den Datenverkehr optimal auf Dienste in den globalen Azure-Regionen zu verteilen, und gleichzeitig Hochverfügbarkeit und Reaktionsfähigkeit sicherzustellen. Traffic Manager bietet verschiedene Methoden für das Datenverkehrsrouting, um den Datenverkehr zu verteilen, z. B. nach Priorität, Gewichtung, Leistung, geografischen Daten, mehr Mehrwert oder Subnetz. Weitere Informationen zu diesen Methoden finden Sie unter [Traffic Manager-Methoden für das Datenverkehrsrouting](../traffic-manager/traffic-manager-routing-methods.md).

Das folgende Diagramm veranschaulicht das auf Endpunktpriorität basierende Routing mit Traffic Manager:

:::image type="content" source="./media/networking-overview/priority.png" alt-text="Prioritätsbasierte Methode für das Datenverkehrsrouting in Azure Traffic Manager":::

Weitere Informationen zu Azure-Traffic Manager finden Sie unter [Was ist Azure Traffic Manager?](../traffic-manager/traffic-manager-overview.md).

### <a name="load-balancer"></a><a name="loadbalancer"></a>Load Balancer
Der Azure Load Balancer ist äußerst leistungsfähig und bietet Lastenausgleich der Ebene 4 mit niedriger Latenz für alle UDP- und TCP-Protokolle. Der Dienst verwaltet eingehende und ausgehende Verbindungen. Sie können öffentliche und interne Endpunkte mit Lastenausgleich konfigurieren. Sie können Regeln definieren, um eingehende Verbindungen anhand von TCP- und HTTP-Integritätstestoptionen zu Back-End-Pool-Zielen zuzuordnen, um die Dienstverfügbarkeit zu verwalten. Weitere Informationen zum Load Balancer finden Sie im Artikel mit der [Übersicht über den Load Balancer](../load-balancer/load-balancer-overview.md).

Die folgende Abbildung zeigt eine Multi-Tier-Anwendung mit Internetzugriff, die sowohl externen als auch internen Lastenausgleich nutzt:

:::image type="content" source="./media/networking-overview/load-balancer.png" alt-text="Beispiel für Azure Load Balancer":::

### <a name="application-gateway"></a><a name="applicationgateway"></a>Application Gateway
Azure Application Gateway ist ein Lastenausgleich für Webdatenverkehr, mit dem Sie eingehenden Datenverkehr für Ihre Webanwendungen verwalten können. Es handelt sich um ein ADC-as-a-Service-Angebot (Application Delivery Controller), das verschiedene Schicht-7-Lastenausgleichsfunktionen für Ihre Anwendung bietet. Weitere Informationen finden Sie unter [Was ist Azure Application Gateway?](../application-gateway/overview.md).

Im folgenden Diagramm wird das auf URL-Pfaden basierende Routing mit Application Gateway veranschaulicht.

:::image type="content" source="./media/networking-overview/figure1-720.png" alt-text="Application Gateway-Beispiel":::

## <a name="network-monitoring-services"></a><a name="monitor"></a>Netzwerküberwachungsdienste
In diesem Abschnitt werden die Azure-Netzwerkdienste beschrieben, mit denen Sie Ihre Netzwerkressourcen überwachen können: Network Watcher, Azure Monitor für Netzwerke, ExpressRoute-Monitor, Azure Monitor oder Terminalzugangspunkte für virtuelle Netzwerke.

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Mit Azure Network Watcher werden Tools für die Überwachung, Diagnose, Metrikanzeige und Aktivierung/Deaktivierung von Protokollen für Ressourcen in einem virtuellen Azure-Netzwerk bereitgestellt. Weitere Informationen finden Sie unter [Was ist Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="azure-monitor-for-networks-preview"></a>Azure Monitor für Netzwerke (Vorschau)
Azure Monitor für Netzwerke bietet eine umfassende Ansicht der Integrität und Metriken für alle bereitgestellten Netzwerkressourcen ohne jegliche Konfiguration. Außerdem erhalten Sie Zugriff auf Netzwerküberwachungsfunktionen wie [Verbindungsmonitor](../network-watcher/connection-monitor-preview.md), [Datenflussprotokollierung für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-overview.md) und [Traffic Analytics](../network-watcher/traffic-analytics.md). Weitere Informationen finden Sie unter [Azure Monitor für Netzwerke (Vorschau)](../azure-monitor/insights/network-insights-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>ExpressRoute-Monitor
Weitere Informationen zum Anzeigen von ExpressRoute-Verbindungsmetriken, -Ressourcenprotokollen und -Warnungen finden Sie unter [ExpressRoute-Überwachung, -Metriken und -Warnungen](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Azure Monitor
Azure Monitor maximiert die Verfügbarkeit und Leistung Ihrer Anwendungen durch die Bereitstellung einer umfassenden Lösung für das Sammeln, Analysieren und Reagieren auf Telemetriedaten aus Ihren cloudbasierten und lokalen Umgebungen. Diese Lösung hilft Ihnen, die Leistung Ihrer Anwendungen zu verstehen, und erkennt proaktiv Probleme, die sich auf sie auswirken, und Ressourcen, von denen sie abhängen. Weitere Informationen finden Sie unter [Überblick über Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="virtual-network-tap"></a><a name="vnettap"></a>Terminalzugangspunkt für virtuelle Netzwerke
Mit dem TAP (Terminal Access Point) für virtuelle Azure-Netzwerke können Sie Ihren VM-Netzwerkdatenverkehr kontinuierlich an einen Netzwerkpaketcollector oder ein Analysetool streamen. Der Collector oder das Analysetool wird von einem Partner für [virtuelle Netzwerkappliances](https://azure.microsoft.com/solutions/network-appliances/) bereitgestellt.

In der folgenden Abbildung wird gezeigt, wie der Terminalzugangspunkt für virtuelle Netzwerke funktioniert:

:::image type="content" source="./media/networking-overview/virtual-network-tap-architecture.png" alt-text="Funktionsweise eines TAP eines virtuellen Netzwerks":::

Weitere Informationen finden Sie unter [Was sind Terminalzugangspunkte für virtuelle Netzwerke?](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie Ihr erstes virtuelles Netzwerk, und verbinden Sie einige VMs damit, indem Sie die Schritte des Artikels [Erstellen Ihres ersten virtuellen Netzwerks](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) ausführen.
- Verbinden Sie Ihren Computer über eine Point-to-Site-Verbindung mit einem virtuellen Netzwerk, indem Sie die Schritte im Artikel [Konfigurieren einer Point-to-Site-Verbindung](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) befolgen.
- Führen Sie einen Lastenausgleich des Internetdatenverkehrs zu öffentlichen Servern durch, indem Sie die Schritte im Artikel [Erstellen eines Load Balancers mit Internetzugriff](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json) ausführen.
