---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 192a6f4841e9dc3a478da5e4b53594362955ca71
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119515"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Wird BGP von allen Azure-VPN-Gateway-SKUs unterstützt?
Nein, BGP wird von Azure-VPN-Gateways vom Typ **VpnGw1**, **VpnGw2**, **VpnGw3**, **Standard** und **HighPerformance** unterstützt. Die SKU **Basic** wird NICHT unterstützt.

### <a name="can-i-use-bgp-with-azure-policy-based-vpn-gateways"></a>Kann ich BGP mit richtlinienbasierten Azure-VPN-Gateways verwenden?
Nein. BGP wird nur für routenbasierte VPN-Gateways unterstützt.

### <a name="can-i-use-private-asns-autonomous-system-numbers"></a>Kann ich private ASNs (Autonome Systemnummern) verwenden?
Ja. Sie können eigene öffentliche ASNs oder private ASNs verwenden – sowohl für Ihre lokalen Netzwerke als auch für Ihre virtuellen Azure-Netzwerke.

### <a name="can-i-use-32-bit-asns-autonomous-system-numbers"></a>Kann ich 32-Bit-ASNs (Autonome Systemnummern) verwenden?
Nein, Azure-VPN-Gateways unterstützen derzeit 16-Bit-ASNs.

### <a name="are-there-asns-reserved-by-azure"></a>Werden ASNs von Azure reserviert?
Ja. Die folgenden ASNs werden von Azure sowohl für interne als auch für externe Peerings reserviert:

* Öffentliche ASNs: 8074, 8075, 12076
* Private ASNs: 65515, 65517, 65518, 65519, 65520

Sie können diese ASNs beim Herstellen einer Verbindung mit Azure VPN Gateways nicht für Ihre lokalen VPN-Geräte angeben.

### <a name="are-there-any-other-asns-that-i-cant-use"></a>Gibt es noch andere ASNs, die ich nicht verwenden kann?
Ja. Die folgenden ASNs sind [von IANA reserviert](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) und können nicht für Ihr Azure VPN Gateway konfiguriert werden:

23456, 64496-64511, 65535-65551 und 429496729

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-vnets"></a>Kann ich die gleiche ASN sowohl für lokale VPN-Netzwerke als auch für Azure-VNETs verwenden?
Nein. Lokalen Netzwerken und Azure-VNETs muss jeweils eine unterschiedliche ASN zugewiesen werden, wenn diese per BGP miteinander verbunden werden. Azure-VPN-Gateways ist standardmäßig die ASN 65515 zugewiesen. Dabei spielt es keine Rolle, ob BGP für Ihre standortübergreifende Konnektivität aktiviert ist. Diesen Standardwert können Sie überschreiben, indem Sie beim Erstellen des VPN-Gateways eine andere ASN zuweisen oder die ASN nach der Gatewayerstellung ändern. Ihre lokalen ASNs müssen den entsprechenden lokalen Azure-Netzwerkgateways zugewiesen werden.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Welche Adresspräfixe kündigen Azure-VPN-Gateways mir gegenüber an?
Das Azure-VPN-Gateway kündigt Ihren lokalen BGP-Geräten gegenüber folgende Routen an:

* Ihre VNET-Adresspräfixe
* Adresspräfixe für die einzelnen lokalen Netzwerkgateways, die mit dem Azure-VPN-Gateway verbunden sind
* Routen, die in anderen, mit dem Azure VPN Gateway verbundenen BGP-Peeringsitzungen ermittelt wurden ( **mit Ausnahme der Standardrouten oder Routen, die sich mit VNET-Präfixen überschneiden**)

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Wie viele Präfixe kann ich Azure-VPN-Gateways ankündigen?
Wir unterstützen bis zu 4.000 Präfixe. Die BGP-Sitzung wird verworfen, wenn die Anzahl von Präfixen den Grenzwert überschreitet.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kann ich die Standardroute (0.0.0.0/0) für Azure VPN Gateways ankündigen?
Ja.

Beachten Sie Folgendes: Es wird erzwungen, dass der gesamte ausgehende VNet-Datenverkehr über Ihren lokalen Standort verläuft. Außerdem wird verhindert, dass die VNet-VMs öffentlichen Kommunikationsverkehr aus dem Internet direkt akzeptieren, z.B. per RDP oder SSH aus dem Internet zu den VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kann ich die gleichen Präfixe wie meine Virtual Network-Präfixe ankündigen?

Nein. Das Ankündigen der gleichen Präfixe wie Ihre Virtual Network-Adresspräfixe wird von der Azure-Plattform blockiert oder gefiltert. Sie können jedoch ein Präfix ankündigen, das eine Obermenge der Inhalte Ihres virtuellen Netzwerks ist. 

Sie können beispielsweise 10.0.0.0/8 ankündigen, wenn für Ihr virtuelles Netzwerk der Adressraum 10.0.0.0/16 verwendet wird. Das Ankündigen von 10.0.0.0/16 oder 10.0.0.0/24 ist dagegen nicht möglich.

### <a name="can-i-use-bgp-with-my-vnet-to-vnet-connections"></a>Kann ich BGP mit meinen VNET-zu-VNET-Verbindungen verwenden?
Ja. BGP kann sowohl für standortübergreifende Verbindungen als auch für VNET-zu-VNET-Verbindungen verwendet werden.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kann ich BGP-Verbindungen mit BGP-fremden Verbindungen für meine Azure-VPN-Gateways kombinieren?
Ja. Für ein Azure-VPN-Gateway kann eine Kombination aus BGP-Verbindungen und BGP-fremden Verbindungen verwendet werden.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Wird BGP-Transitrouting vom Azure-VPN-Gateway unterstützt?
Ja. BGP-Transitrouting wird unterstützt. Einzige Einschränkung: Azure-VPN-Gateways kündigen gegenüber anderen BGP-Peers **KEINE** Standardrouten an. Wenn Sie Transitrouting über mehrere Azure-VPN-Gateways hinweg nutzen möchten, müssen Sie BGP für alle VNET-zu-VNET-Zwischenverbindungen aktivieren. Weitere Informationen finden Sie in der [Übersicht über BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-azure-vpn-gateway-and-my-on-premises-network"></a>Kann ich zwischen Azure-VPN-Gateway und meinem lokalen Netzwerk mehrere Tunnel verwenden?
Ja. Zwischen einem Azure-VPN-Gateway und Ihrem lokalen Netzwerk können mehrere S2S-VPN-Tunnel eingerichtet werden. Beachten Sie, dass diese Tunnel alle auf die Gesamtzahl von Tunneln für Ihre Azure-VPN-Gateways angerechnet werden und dass Sie BGP für beide Tunnel aktivieren müssen.

Wenn also etwa zwischen Ihrem Azure-VPN-Gateway und einem Ihrer lokalen Netzwerke zwei redundante Tunnel bestehen, werden dadurch zwei Tunnel des Gesamtkontingents für Ihr Azure-VPN-Gateway (10 für „Standard“, 30 für „HighPerformance“) belegt.

### <a name="can-i-have-multiple-tunnels-between-two-azure-vnets-with-bgp"></a>Kann ich zwischen zwei Azure-VNETs mit BGP mehrere Tunnel verwenden?
Ja. Aber mindestens eines der virtuellen Netzwerkgateways muss über eine Aktiv/Aktiv-Konfiguration verfügen.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-expressroutes2s-vpn-co-existence-configuration"></a>Kann ich BGP für S2S-VPN-Verbindungen in einer Konfiguration mit ExpressRoute und S2S-VPN verwenden?
Ja. 

### <a name="what-address-does-azure-vpn-gateway-use-for-bgp-peer-ip"></a>Welche Adresse verwendet das Azure-VPN-Gateway als BGP-Peer-IP-Adresse?
Das Azure-VPN-Gateway ordnet eine einzelne IP-Adresse aus dem für das virtuelle Netzwerk definierten GatewaySubnet-Bereich zu. Dabei handelt es sich standardmäßig um die vorletzte Adresse des Bereichs. Wenn also beispielsweise der GatewaySubnet-Bereich 10.12.255.0/27 von 10.12.255.0 bis 10.12.255.31 reicht, wird 10.12.255.30 als BGP-Peer-IP-Adresse für das Azure-VPN Gateway verwendet. Diese Information können Sie der Liste mit den Azure-VPN-Gateway-Informationen entnehmen.

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Welche Anforderungen müssen die BGP-Peer-IP-Adressen auf meinem VPN-Gerät erfüllen?
Ihre lokale BGP-Peeradresse darf **NICHT** der öffentlichen IP-Adresse Ihres VPN-Geräts entsprechen. Verwenden Sie als BGP-Peer-IP-Adresse eine andere IP-Adresse für das VPN-Gerät. Dabei kann es sich um eine der Loopbackschnittstelle auf dem Gerät zugewiesene Adresse handeln. Beachten Sie jedoch, dass keine APIPA-Adresse (169.254.x.x) verwendet werden kann. Geben Sie diese Adresse im entsprechenden lokalen Netzwerkgateway an, das den Standort darstellt.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Was muss ich bei Verwendung von BGP als Adresspräfixe für das lokale Netzwerkgateway angeben?
Das lokale Azure-Netzwerkgateway gibt die anfänglichen Adresspräfixe für das lokale Netzwerk an. Mit BGP müssen Sie das Hostpräfix (/32-Präfix) Ihrer BGP-Peer-IP-Adresse als Adressraum für das lokale Netzwerk zuordnen. Wenn Ihre BGP-Peer-IP-Adresse 10.52.255.254 lautet, müssen Sie 10.52.255.254/32 als LocalNetworkAddressSpace für das lokale Netzwerkgateway angeben, das dieses lokale Netzwerkgateway darstellt. Dadurch wird sichergestellt, dass das Azure-VPN-Gateway die BGP-Sitzung über den S2S-VPN-Tunnel herstellt.

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Was muss ich meinem lokalen VPN-Gerät für die BGP-Peeringsitzung hinzufügen?
Sie müssen eine Hostroute der Azure-BGP-Peer-IP-Adresse auf Ihrem VPN-Gerät hinzufügen, die auf den IPsec-S2S-VPN-Tunnel verweist. Lautet die Azure-VPN-Peer-IP-Adresse also etwa 10.12.255.30, müssen Sie eine Hostroute für 10.12.255.30 mit einer Nexthop-Schnittstelle der entsprechenden IPSec-Tunnelschnittstelle auf Ihrem VPN-Gerät hinzufügen.
