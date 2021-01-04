---
title: 'Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Konfigurationsdetails | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Konfigurationsdetails für die Testeinrichtung, mit der Sie die Interoperabilität zwischen ExpressRoute, einem Site-to-Site-VPN und Peering zwischen virtuellen Netzwerken in Azure analysieren können.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: 7be326e0f01ed6a00244c0f5b9ed6a960b2b6e0b
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "86171855"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-test-configuration-details"></a>Interoperabilität in Azure-Back-End-Konnektivitätsfeatures: Konfigurationsdetails zur Testeinrichtung

In diesem Artikel werden die Konfigurationsdetails der [Testeinrichtung][Setup] beschrieben. Die Testeinrichtung hilft Ihnen bei der Analyse, wie Azure-Netzwerkdienste auf der Steuerungs- und Datenebene zusammen arbeiten.

## <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke-VNET-Konnektivität per VNET-Peering

Die folgende Abbildung zeigt die Details zum Peering von virtuellen Azure-Netzwerken eines virtuellen Spoke-Netzwerks (VNET). Weitere Informationen zur Einrichtung eines Peerings zwischen zwei VNETs finden Sie unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke][VNet-Config]. Wenn das Spoke-VNET die Gateways verwenden soll, die mit dem Hub-VNET verbunden sind, aktivieren Sie **Remotegateways verwenden**.

[![1]][1]

Die folgende Abbildung zeigt die Details zum VNET-Peering des Hub-VNET. Wenn das Hub-VNET dem Spoke-VNET die Verwendung der Hub-Gateways gestatten soll, wählen Sie **Gatewaytransit zulassen** aus.

[![2]][2]

## <a name="branch-vnet-connectivity-by-using-a-site-to-site-vpn"></a>Konnektivität mit dem VNET der Zweigniederlassung über ein Site-to-Site-VPN

Die Einrichtung von Site-to-Site-VPN-Konnektivität zwischen dem Hub-VNET und dem VNET der Zweigniederlassung wird mithilfe von VPN-Gateways in Azure-VPN-Gateway konfiguriert. VPN-Gateways und Azure ExpressRoute-Gateways verwenden standardmäßig den Wert **65515** für eine private autonome Systemnummer (ASN). Sie können den ASN-Wert im VPN-Gateway ändern. In der Testeinrichtung wird der ASN-Wert für das VPN-Gateway des VNET der Zweigniederlassung in **65516** geändert, um das eBGP-Routing zwischen dem Hub-VNET und dem VNET der Zweigniederlassung zu unterstützen.


[![3]][3]


## <a name="on-premises-location-1-connectivity-by-using-expressroute-and-a-site-to-site-vpn"></a>Konnektivität mit dem lokalen Netzwerk von Standort 1 mithilfe von ExpressRoute und eines Site-to-Site-VPN

### <a name="expressroute-1-configuration-details"></a>Konfigurationsdetails zu ExpressRoute 1

Die folgende Abbildung zeigt die Konfiguration der ExpressRoute-Verbindung in der Azure-Region 1 mit den lokalen CE-Routern (Customer Edge) von Standort 1:

[![4]][4]

Die folgende Abbildung zeigt die Verbindungskonfiguration zwischen der ExpressRoute 1-Verbindung und dem Hub-VNET:

[![5]][5]

Die folgende Liste enthält die primäre CE-Routerkonfiguration für die private ExpressRoute-Peeringkonnektivität. (Als CE-Router werden Cisco ASR1000-Router in der Testeinrichtung verwendet.) Wenn das Site-to-Site-VPN und die ExpressRoute-Verbindungen parallel konfiguriert sind, um ein lokales Netzwerk mit Azure herzustellen, gibt Azure standardmäßig der ExpressRoute-Verbindung den Vorzug. Um ein asymmetrisches Routing zu vermeiden, sollte das lokale Netzwerk auch ExpressRoute-Konnektivität über Site-to-Site-VPN-Konnektivität priorisieren. Die folgende Konfiguration richtet Priorisierung mithilfe des BGP-Attributs **lokale-preference** ein:

```config
interface TenGigabitEthernet0/0/0.300
 description Customer 30 private peering to Azure
 encapsulation dot1Q 30 second-dot1q 300
 ip vrf forwarding 30
 ip address 192.168.30.17 255.255.255.252
!
interface TenGigabitEthernet1/0/0.30
 description Customer 30 to south bound LAN switch
 encapsulation dot1Q 30
 ip vrf forwarding 30
 ip address 192.168.30.0 255.255.255.254
 ip ospf network point-to-point
!
router ospf 30 vrf 30
 router-id 10.2.30.253
 redistribute bgp 65021 subnets route-map BGP2OSPF
 network 192.168.30.0 0.0.0.1 area 0.0.0.0
default-information originate always
 default-metric 10
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 192.168.30.18 remote-as 12076
  neighbor 192.168.30.18 activate
  neighbor 192.168.30.18 next-hop-self
  neighbor 192.168.30.18 soft-reconfiguration inbound
  neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
  neighbor 192.168.30.18 prefix-list Cust30_to_Private out
 exit-address-family
!
route-map prefer-ER-over-VPN permit 10
 set local-preference 200
!
ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
!
```

### <a name="site-to-site-vpn-configuration-details"></a>Details der Konfiguration einer Site-to-Site-VPN-Verbindung

Die folgende Liste enthält die primäre CE-Routerkonfiguration für die Site-to-Site-VPN-Konnektivität:

```config
crypto ikev2 proposal Cust30-azure-proposal
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy Cust30-azure-policy
 match address local 66.198.12.106
 proposal Cust30-azure-proposal
!
crypto ikev2 keyring Cust30-azure-keyring
 peer azure
  address 52.168.162.84
  pre-shared-key local IamSecure123
  pre-shared-key remote IamSecure123
!
crypto ikev2 profile Cust30-azure-profile
 match identity remote address 52.168.162.84 255.255.255.255
 identity local address 66.198.12.106
 authentication local pre-share
 authentication remote pre-share
 keyring local Cust30-azure-keyring
!
crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile Cust30-azure-ipsec-profile
 set transform-set Cust30-azure-ipsec-proposal-set
 set ikev2-profile Cust30-azure-profile
!
interface Loopback30
 ip address 66.198.12.106 255.255.255.255
!
interface Tunnel30
 ip vrf forwarding 30
 ip address 10.2.30.125 255.255.255.255
 tunnel source Loopback30
 tunnel mode ipsec ipv4
 tunnel destination 52.168.162.84
 tunnel protection ipsec profile Cust30-azure-ipsec-profile
!
router bgp 65021
 !
 address-family ipv4 vrf 30
  network 10.2.30.0 mask 255.255.255.128
  neighbor 10.10.30.254 remote-as 65515
  neighbor 10.10.30.254 ebgp-multihop 5
  neighbor 10.10.30.254 update-source Tunnel30
  neighbor 10.10.30.254 activate
  neighbor 10.10.30.254 soft-reconfiguration inbound
 exit-address-family
!
ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30
```

## <a name="on-premises-location-2-connectivity-by-using-expressroute"></a>Konnektivität mit dem lokalen Netzwerk von Standort 2 mithilfe von ExpressRoute

Eine zweite ExpressRoute-Verbindung in größerer Nähe zum lokalen Netzwerk von Standort 2 wird zwischen dem lokalen Netzwerk von Standort 2 und dem Hub-VNET aufgebaut. Die folgende Abbildung zeigt die zweite ExpressRoute-Konfiguration:

[![6]][6]

Die folgende Abbildung zeigt die Verbindungskonfiguration zwischen der zweiten ExpressRoute-Verbindung und dem Hub-VNET:

[![7]][7]

ExpressRoute 1 stellt eine Verbindung zwischen dem Hub-VNET sowie dem lokalen Netzwerk von Standort 1 und einem Remote-VNET in einer anderen Azure-Region her:

[![8]][8]

## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Gemeinsame ExpressRoute- und Site-to-Site-VPN-Konnektivität

###  <a name="site-to-site-vpn-over-expressroute"></a>Site-to-Site-VPN über ExpressRoute

Sie können ein Site-to-Site-VPN mithilfe von ExpressRoute-Microsoft-Peering konfigurieren, um Daten privat zwischen Ihrem lokalen Netzwerk und Ihren Azure-VNETs auszutauschen. Mit dieser Konfiguration können Sie Daten mit Vertraulichkeit, Authentizität und Integrität austauschen. Der Datenaustausch ist außerdem Anti-Replay-konform. Weitere Informationen zur Konfiguration eines Site-to-Site-IPsec-VPN im Tunnelmodus per ExpressRoute-Microsoft-Peering finden Sie unter [Konfigurieren eines Site-to-Site-VPN über ExpressRoute-/Microsoft-Peering][S2S-Over-ExR]. 

Die wesentliche Einschränkung für die Konfiguration eines Site-to-Site-VPN mit Microsoft-Peering ist der Durchsatz. Der Durchsatz des IPsec-Tunnels wird durch die Kapazität des VPN-Gateways eingeschränkt. Der Durchsatz des VPN-Gateways ist niedriger als der ExpressRoute-Durchsatz. In diesem Szenario stellt die Verwendung des IPsec-Tunnels für hoch sicheren Datenverkehr und des privaten Peerings für sämtlichen anderen Datenverkehr einen Beitrag zur Optimierung der ExpressRoute-Bandbreitenauslastung dar.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>Site-to-Site-VPN als sicherer Failoverpfad für ExpressRoute

ExpressRoute dient als redundantes Verbindungspaar, um Hochverfügbarkeit sicherzustellen. Sie können die georedundante ExpressRoute-Konnektivität in unterschiedlichen Azure-Regionen konfigurieren. Sie können außerdem wie in unserer Testeinrichtung demonstriert in einer Azure-Region mithilfe eines Site-to-Site-VPN einen Failoverpfad für Ihre ExpressRoute-Verbindung einrichten. Wenn über ExpressRoute und das Site-to-Site-VPN die gleichen Präfixe angekündigt werden, priorisiert Azure ExpressRoute. Zur Vermeidung von asymmetrischem Routing zwischen ExpressRoute und dem Site-to-Site-VPN sollte in der lokalen Netzwerkkonfiguration die ExpressRoute-Verbindung ebenfalls den Vorzug vor Site-to-Site-VPN-Verbindungen erhalten.

Weitere Informationen zur Konfiguration von parallelen ExpressRoute- und Site-to-Site-VPN-Verbindungen finden Sie unter [Konfigurieren von parallel bestehenden ExpressRoute- und Standort-zu-Standort-Verbindungen mithilfe von PowerShell][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Erweitern der Back-End-Konnektivität auf Spoke-VNETs und Branchstandorte

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Spoke-VNET-Konnektivität per VNET-Peering

Die Hub-Spoke-VNET-Architektur wird häufig genutzt. Bei einem Hub handelt es sich um ein VNET in Azure, das als zentraler Konnektivitätspunkt zwischen Ihren Spoke-VNETs und Ihrem lokalen Netzwerk fungiert. Die Spokes sind VNETs, die eine Peeringverbindung mit dem Hub herstellen und zur Isolierung von Workloads verwendet werden können. Der Datenverkehr wird über eine ExpressRoute- oder VPN-Verbindung zwischen dem lokalen Rechenzentrum und dem Hub weitergeleitet. Weitere Informationen zu dieser Architektur finden Sie unter [Implementieren einer Hub-Spoke-Netzwerktopologie in Azure][Hub-n-Spoke].

Beim VNET-Peering in einer Region können Spoke-VNETs Hub-VNET-Gateways verwenden (sowohl VPN- als auch ExpressRoute-Gateways), um mit Remotenetzwerken zu kommunizieren.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Konnektivität mit dem VNET der Zweigniederlassung über das Site-to-Site-VPN

VNETs in verschiedenen Regionen und lokale Netzwerke sollten miteinander über ein Hub-VNET kommunizieren. Die native Azure-Lösung für diese Konfiguration ist Site-to-Site-VPN-Konnektivität über ein VPN. Eine Alternative ist die Verwendung eines virtuellen Netzwerkgeräts (NVA) für das Routing im Hub.

Weitere Informationen finden Sie unter [Was ist VPN Gateway?][VPN] und [Bereitstellen hochverfügbarer virtueller Netzwerkgeräte][Deploy-NVA].

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die [Analyse auf Steuerungsebene][Control-Analysis] für die Testeinrichtung sowie über die Ansichten verschiedener VNETs oder VLANs in der Topologie.

Informieren Sie sich über die [Analyse auf Datenebene][Data-Analysis] für die Testeinrichtung und die Ansichten des Azure-Netzwerküberwachungsfeatures.

Unter [ExpressRoute – FAQ][ExR-FAQ] finden Sie Informationen zu folgenden Themen:
-   Erfahren Sie, wie viele ExpressRoute-Verbindungen Sie mit einem ExpressRoute-Gateway verbinden können.
-   Anzahl der ExpressRoute-Gateways, die Sie per ExpressRoute verbinden können
-   Erfahren Sie mehr über andere Skalierungslimits von ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "VNET-Peering des Spoke-VNET"
[2]: ./media/backend-interoperability/HubVNet-peering.png "VNET-Peering des Hub-VNET"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "VPN Gateway-Konfiguration eines VNET der Zweigniederlassung"
[4]: ./media/backend-interoperability/ExR1.png "Konfiguration von ExpressRoute 1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Verbindungskonfiguration zwischen ExpressRoute 1 und dem ExpressRoute-Gateway des Hub-VNET"
[6]: ./media/backend-interoperability/ExR2.png "Konfiguration von ExpressRoute 2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Verbindungskonfiguration zwischen ExpressRoute 2 und dem ExpressRoute-Gateway des Hub-VNET"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Verbindungskonfiguration zwischen ExpressRoute 2 und dem ExpressRoute-Gateway des Remote-VNET"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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


