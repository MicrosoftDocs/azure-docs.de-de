---
title: 'Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Analyse der Steuerungsebene | Microsoft-Dokumentation'
description: Dieser Artikel bietet eine Analyse auf Steuerungsebene der Testeinrichtung, mit der Sie die Interoperabilität zwischen ExpressRoute, einem Site-to-Site-VPN und Peering zwischen virtuellen Netzwerken in Azure analysieren können.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 28ce4cfd0c62586510a6f7dfdeca8b552fe9638e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60425647"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-control-plane-analysis"></a>Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Analyse der Steuerungsebene

In diesem Artikel wird die Analyse auf Steuerungsebene der [Testeinrichtung][Setup] beschrieben. Sie können auch die [Testeinrichtungskonfiguration][Configuration] und die [Analyse auf Datenebene][Data-Analysis] der Testeinrichtung überprüfen.

Bei der Analyse auf Steuerungsebene werden im Wesentlichen Routen untersucht, die zwischen Netzwerken einer Topologie ausgetauscht werden. Mit der Analyse auf Steuerungsebene können Sie herausfinden, wie die Topologie von unterschiedlichen Netzwerken betrachtet wird.

## <a name="hub-and-spoke-vnet-perspective"></a>Hub-Spoke-VNET-Perspektive

Die folgende Abbildung veranschaulicht das Netzwerk aus der Perspektive eines virtuellen Hubnetzwerks (Hub-VNET) und eines Spoke-VNET (in Blau dargestellt). Darüber hinaus sind in der Abbildung auch die autonome Systemnummer (Autonomous System Number, ASN) der unterschiedlichen Netzwerke und die zwischen den Netzwerken ausgetauschten Routen dargestellt: 

[![1]][1]

Die ASN für das Azure ExpressRoute-Gateway des VNET unterscheidet von der ASN für Microsoft Enterprise Edge-Router (MSEEs). Für ein ExpressRoute-Gateway wird eine private ASN (mit dem Wert **65515**) und für MSEE-Einheiten global eine öffentliche ASN (mit dem Wert **12076**) verwendet. Wenn Sie das ExpressRoute-Peering konfigurieren, weil die MSEE-Einheit als Peer fungiert, nutzen Sie **12076** als Peer-ASN. Auf Azure-Seite richtet die MSEE-Einheit das eBGP-Peering mit dem ExpressRoute-Gateway ein. Das duale eBGP-Peering, das die MSEE-Einheit für jedes ExpressRoute-Peering einrichtet, ist auf der Steuerungsebene transparent. Wenn Sie eine ExpressRoute-Routingtabelle anzeigen, wird die ExpressRoute-Gateway-ASN des VNET für die VNET-Präfixe eingeblendet. 

Die folgende Abbildung zeigt eine ExpressRoute-Beispielroutentabelle: 

[![5]][5]

In Azure hat die ASN nur aus Peeringperspektive eine Bedeutung. Standardmäßig lautet die ASN des ExpressRoute-Gateways und des VPN-Gateways im Azure-VPN-Gateway **65515**.

## <a name="on-premises-location-1-and-the-remote-vnet-perspective-via-expressroute-1"></a>Perspektive des lokalen Netzwerks von Standort 1 und des Remote-VNET über ExpressRoute 1

Sowohl das lokale Netzwerk von Standort 1 als auch das Remote-VNET sind über ExpressRoute 1 mit dem Hub-VNET verbunden. Sie teilen die gleiche Perspektive der Topologie, wie in der folgenden Abbildung dargestellt wird:

[![2]][2]

## <a name="on-premises-location-1-and-the-branch-vnet-perspective-via-a-site-to-site-vpn"></a>Perspektive des lokalen Netzwerks von Standort 1 und des VNET der Zweigniederlassung per Site-to-Site-VPN

Sowohl das lokale Netzwerk von Standort 1 als auch das VNET der Zweigniederlassung sind mit dem VPN-Gateway eines Hub-VNET über eine Site-to-Site-VPN-Verbindung verbunden. Sie teilen die gleiche Perspektive der Topologie, wie in der folgenden Abbildung dargestellt wird:

[![3]][3]

## <a name="on-premises-location-2-perspective"></a>Perspektive des lokalen Netzwerks von Standort 2

Der lokale Netzwerk von Standort 2 ist über das private Peering von ExpressRoute 2 mit dem Hub-VNET verbunden: 

[![4]][4]

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

Die Analyse der Testeinrichtung auf Datenebene und die Ansichten der Überwachungsfeatures für Azure-Netzwerke können Sie unter [Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Analyse auf Datenebene][Data-Analysis] einsehen.

Unter [ExpressRoute – FAQ][ExR-FAQ] finden Sie Informationen zu folgenden Themen:
-   Erfahren Sie, wie viele ExpressRoute-Verbindungen Sie mit einem ExpressRoute-Gateway verbinden können.
-   Anzahl der ExpressRoute-Gateways, die Sie per ExpressRoute verbinden können
-   Erfahren Sie mehr über andere Skalierungslimits von ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "Hub-Spoke-VNET-Perspektive der Topologie"
[2]: ./media/backend-interoperability/Loc1ExRView.png "Perspektive von Standort 1 und des Remote-VNET der Topologie über ExpressRoute 1"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Perspektive von Standort 1 und des VNET der Zweigniederlassung der Topologie über ein Site-to-Site-VPN"
[4]: ./media/backend-interoperability/Loc2View.png "Perspektive des Standorts 2 der Topologie"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "Routingtabelle von ExpressRoute 1"

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


