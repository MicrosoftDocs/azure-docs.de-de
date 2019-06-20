---
title: 'Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Testeinrichtung | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt eine Testeinrichtung, mit der Sie die Interoperabilität zwischen ExpressRoute, einem Site-to-Site-VPN und Peering zwischen virtuellen Netzwerken in Azure analysieren können.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 8be546c5dba4c6c694c8cef03a4bdd6005d68189
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60811106"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-setup"></a>Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Testeinrichtung

In diesem Artikel wird eine Testeinrichtung beschrieben, mit der Sie analysieren können, wie Azure-Netzwerkdienste auf der Steuerungs- und Datenebene zusammen arbeiten. Sehen wir uns kurz die Azure-Netzwerkkomponenten an:

-   **Azure ExpressRoute:** Mit dem privaten Peering in Azure ExpressRoute können Sie eine direkte Verbindung zwischen privaten IP-Adressbereichen Ihres lokalen Netzwerks und Ihren Azure Virtual Network-Bereitstellungen herstellen. Mithilfe von ExpressRoute können Sie eine höhere Bandbreite erzielen und private Verbindungen herstellen. Viele ExpressRoute-Ökosystempartner bieten ExpressRoute-Konnektivität mit SLAs. Weitere Informationen zu ExpressRoute und zur Konfiguration von ExpressRoute finden Sie unter [Übersicht über ExpressRoute][ExpressRoute].
-   **Site-to-Site-VPN:** Mit Azure VPN Gateway als Site-to-Site-VPN können Sie über das Internet oder über ExpressRoute eine sichere Verbindung zwischen einem lokalen Netzwerk und Azure herstellen. Informationen zum Konfigurieren eines Site-to-Site-VPN zum Verbinden mit Azure finden Sie unter [Was ist VPN Gateway?][VPN].
-   **VNET-Peering:** Verwenden Sie das Peering virtueller Netzwerke (VNET), um Konnektivität zwischen VNETs in Azure Virtual Network herzustellen. Weitere Informationen zum VNET-Peering finden Sie unter [Tutorial: Herstellen einer Verbindung zwischen virtuellen Netzwerken mittels Peering virtueller Netzwerke über das Azure-Portal][VNet].

## <a name="test-setup"></a>Testeinrichtung

Die folgende Abbildung veranschaulicht die Testeinrichtung:

[![1]][1]

Das Herzstück einer Testeinrichtung bildet das Hub-VNET in der Azure-Region 1. Das Hub-VNET wird wie folgt mit anderen Netzwerken verbunden:

-   Das Hub-VNET wird per VNET-Peering mit dem Spoke-VNET verbunden. Das Spoke-VNET verfügt über Remotezugriff auf beide Gateways im Hub-VNET.
-   Das Hub-VNET wird mittels Site-to-Site-VPN mit dem VNET der Zweigniederlassung verbunden. Bei der Konnektivität wird eBGP zum Austausch von Routen verwendet.
-   Das Hub-VNET ist über privates ExpressRoute-Peering als primärer Pfad mit dem lokalen Netzwerk von Standort 1 verbunden. Als Sicherungspfad wird Site-to-Site-VPN-Konnektivität verwendet. Im vorliegenden Artikel wird diese ExpressRoute-Verbindung als „ExpressRoute 1“ bezeichnet. Standardmäßig bieten ExpressRoute-Verbindungen redundante Konnektivität zur Bereitstellung von Hochverfügbarkeit. Für ExpressRoute 1 ist die Unterschnittstelle des sekundären Customer Edge-Routers (CE) deaktiviert, die für den sekundären Microsoft Enterprise Edge Router (MSEE) zuständig ist. Eine rote Linie im Pfeil mit zwei Linien in der obigen Abbildung stellt die deaktivierte Unterschnittstelle des CE-Routers dar.
-   Das Hub-VNET ist über ein anderes privates ExpressRoute-Peering mit dem lokalen Netzwerk von Standort 2 verbunden. Im vorliegenden Artikel wird diese zweite ExpressRoute-Verbindung als „ExpressRoute 2“ bezeichnet.
-   ExpressRoute 1 stellt zudem eine Verbindung zwischen dem Hub-VNET sowie dem lokalen Netzwerk von Standort 1 und einem Remote-VNET in der Azure-Region 2 her.

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

Erfahren Sie mehr über die [Konfigurationsdetails][Configuration] für die Testtopologie.

Erfahren Sie mehr über die [Analyse auf Steuerungsebene][Control-Analysis] der Testeinrichtung sowie die Ansichten von anderen VNETs oder VLANs in der Topologie.

Die Analyse der Testeinrichtung auf Datenebene und die Ansichten der Überwachungsfeatures für Azure-Netzwerke können Sie unter [Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Analyse auf Datenebene][Data-Analysis] einsehen.

Unter [ExpressRoute – FAQ][ExR-FAQ] finden Sie Informationen zu folgenden Themen:
-   Erfahren Sie, wie viele ExpressRoute-Verbindungen Sie mit einem ExpressRoute-Gateway verbinden können.
-   Anzahl der ExpressRoute-Gateways, die Sie per ExpressRoute verbinden können
-   Erfahren Sie mehr über andere Skalierungslimits von ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "Abbildung zur Testtopologie"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: connectivty-interoperability-configuration.md
[Control-Analysis]: connectivty-interoperability-control-plane.md
[Data-Analysis]: connectivty-interoperability-data-plane.md
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha


