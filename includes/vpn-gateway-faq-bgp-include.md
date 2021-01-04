---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 649c5805c600b6282be6d05fefb59cecaf249f4f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92526101"
---
### <a name="is-bgp-supported-on-all-azure-vpn-gateway-skus"></a>Wird BGP von allen Azure-VPN-Gateway-SKUs unterstützt?
BGP wird mit Ausnahme der SKU „Basic“ für alle Azure-VPN-Gateway-SKUs unterstützt.

### <a name="can-i-use-bgp-with-azure-policy-vpn-gateways"></a>Kann ich BGP mit Azure Policy-VPN-Gateways verwenden?
Nein. BGP wird nur für routenbasierte VPN-Gateways unterstützt.

### <a name="what-asns-autonomous-system-numbers-can-i-use"></a>Welche autonomen Systemnummern (ASNs) kann ich verwenden?
Sie können eigene öffentliche ASNs oder private ASNs sowohl für Ihre lokalen Netzwerke als auch für Ihre virtuellen Azure-Netzwerke verwenden. Die von Azure oder IANA reservierten Bereiche können nicht verwendet werden.

Die folgenden ASNs sind für Azure oder IANA reserviert:
* Von Azure reservierte ASNs:
  * Öffentliche ASNs: 8074, 8075, 12076
  * Private ASNs: 65515, 65517, 65518, 65519, 65520
* [Von IANA reservierte](http://www.iana.org/assignments/iana-as-numbers-special-registry/iana-as-numbers-special-registry.xhtml) ASNs:
   * 23456, 64496-64511, 65535-65551 und 429496729

Diese ASNs können beim Herstellen einer Verbindung mit Azure-VPN-Gateways nicht für Ihre lokalen VPN-Geräte angegeben werden.

### <a name="can-i-use-32-bit-4-byte-asns"></a>Kann ich 32-Bit-ASNs (4 Bytes) verwenden?
Ja, VPN Gateway unterstützt nun 32-Bit-ASNs (4 Bytes). Verwenden Sie zum Konfigurieren mithilfe von ASNs im Dezimalformat PowerShell, die Azure CLI oder das Azure SDK.

### <a name="what-private-asns-can-i-use"></a>Welche privaten ASNs kann ich verwenden?
Die folgenden Bereiche privater ASNs können verwendet werden:

* 64512-65514 und 65521-65534

Diese ASNs sind nicht für die Verwendung durch IANA oder Azure reserviert und können daher Ihrem Azure-VPN-Gateway zugewiesen werden.

### <a name="what-address-does-vpn-gateway-use-for-bgp-peer-ip"></a>Welche Adresse verwendet VPN Gateway als BGP-Peer-IP-Adresse?

VPN Gateway ordnet standardmäßig eine einzelne IP-Adresse aus dem *GatewaySubnet* -Bereich für Aktiv/Standby-VPN-Gateways oder zwei IP-Adressen für Aktiv/Aktiv-VPN-Gateways zu. Diese Adressen werden automatisch zugeordnet, wenn Sie das VPN-Gateway erstellen. Die tatsächlich zugeordnete BGP-IP-Adresse kann mithilfe von PowerShell oder über das Azure-Portal ermittelt werden. Verwenden Sie in PowerShell **Get-AzVirtualNetworkGateway** , und suchen Sie nach der Eigenschaft **bgpPeeringAddress** . Sehen Sie im Azure-Portal auf der Seite **Gatewaykonfiguration** unter der Eigenschaft **BGP-ASN konfigurieren** nach.

Wenn Ihre lokalen VPN-Router IP-Adressen vom Typ **APIPA** (169.254.x.x) als BGP-IP-Adressen verwenden, müssen Sie für Ihr Azure-VPN-Gateway eine zusätzliche **Azure-APIPA-BGP-IP-Adresse** angeben. Von Azure VPN Gateway wird die APIPA-Adresse für die Verwendung mit dem lokalen, im lokalen Netzwerkgateway angegebenen APIPA-BGP-Peer oder die private IP-Adresse für einen APIPA-fremden lokalen BGP-Peer ausgewählt. Weitere Informationen finden Sie unter [Konfigurieren von BGP für Azure-VPN-Gateways](../articles/vpn-gateway/bgp-howto.md).

### <a name="what-are-the-requirements-for-the-bgp-peer-ip-addresses-on-my-vpn-device"></a>Welche Anforderungen müssen die BGP-Peer-IP-Adressen auf meinem VPN-Gerät erfüllen?
Ihre lokale BGP-Peeradresse darf nicht der öffentlichen IP-Adresse Ihres VPN-Geräts entsprechen oder aus dem VNET-Adressraum des VPN-Gateways stammen. Verwenden Sie als BGP-Peer-IP-Adresse eine andere IP-Adresse für das VPN-Gerät. Dabei kann es sich um eine Adresse handeln, die der Loopbackschnittstelle auf dem Gerät zugewiesen ist (reguläre IP-Adresse oder APIPA-Adresse). Wenn von Ihrem Gerät eine APIPA-Adresse für BGP verwendet wird, müssen Sie wie unter [Konfigurieren von BGP für Azure-VPN-Gateways](../articles/vpn-gateway/bgp-howto.md) beschrieben eine APIPA-BGP-IP-Adresse für Ihr Azure-VPN-Gateway angeben. Geben Sie diese Adresse im entsprechenden lokalen Netzwerkgateway an, das den Standort darstellt.

### <a name="what-should-i-specify-as-my-address-prefixes-for-the-local-network-gateway-when-i-use-bgp"></a>Was muss ich bei Verwendung von BGP als Adresspräfixe für das lokale Netzwerkgateway angeben?

> [!IMPORTANT]
>
>Hierbei handelt es sich um eine Änderung gegenüber der zuvor dokumentierten Anforderung. Wenn Sie BGP für eine Verbindung verwenden, lassen Sie das Feld **Adressraum** für die zugehörige lokale Netzwerkgatewayressource leer. Von Azure VPN Gateway wird intern eine Hostroute zur lokalen BGP-Peer-IP-Adresse über den IPsec-Tunnel hinzugefügt. Fügen Sie die /32-Route nicht dem Feld **Adressraum** hinzu. Sie ist redundant und kann diesem Feld nicht hinzugefügt werden, wenn als BGP-IP-Adresse des lokalen VPN-Geräts eine APIPA-Adresse verwendet wird. Wenn Sie im Feld **Adressraum** andere Präfixe hinzufügen, werden diese zusätzlich zu den über BGP ermittelten Routen als statische Routen für das Azure-VPN-Gateway hinzugefügt.
>

### <a name="can-i-use-the-same-asn-for-both-on-premises-vpn-networks-and-azure-virtual-networks"></a>Kann ich die gleiche ASN sowohl für lokale VPN-Netzwerke als auch für Azure-VNETs verwenden?
Nein. Lokalen Netzwerken und virtuellen Azure-Netzwerken muss jeweils eine andere ASN zugewiesen werden, wenn diese per BGP miteinander verbunden werden. Azure-VPN-Gateways ist standardmäßig die ASN 65515 zugewiesen. Dabei spielt es keine Rolle, ob BGP für Ihre standortübergreifende Konnektivität aktiviert ist. Diesen Standardwert können Sie überschreiben, indem Sie beim Erstellen des VPN-Gateways eine andere ASN zuweisen, oder Sie können die ASN nach der Gatewayerstellung ändern. Ihre lokalen ASNs müssen den entsprechenden lokalen Azure-Netzwerkgateways zugewiesen werden.

### <a name="what-address-prefixes-will-azure-vpn-gateways-advertise-to-me"></a>Welche Adresspräfixe kündigen Azure-VPN-Gateways mir gegenüber an?
Die Gateways kündigen Ihren lokalen BGP-Geräten gegenüber folgende Routen an:

* Ihre Adresspräfixe des virtuellen Netzwerks
* Adresspräfixe für die einzelnen lokalen Netzwerkgateways, die mit dem Azure-VPN-Gateway verbunden sind
* Routen, die in anderen, mit dem Azure-VPN-Gateway verbundenen BGP-Peeringsitzungen ermittelt wurden (mit Ausnahme der Standardrouten, die sich mit VNET-Präfixen überschneiden)

### <a name="how-many-prefixes-can-i-advertise-to-azure-vpn-gateway"></a>Wie viele Präfixe kann ich Azure VPN Gateway ankündigen?
Azure VPN Gateway unterstützt bis zu 4.000 Präfixe. Die BGP-Sitzung wird verworfen, wenn die Anzahl von Präfixen den Grenzwert überschreitet.

### <a name="can-i-advertise-default-route-00000-to-azure-vpn-gateways"></a>Kann ich die Standardroute (0.0.0.0/0) für Azure VPN Gateways ankündigen?
Ja. Beachten Sie, dass dadurch der gesamte ausgehende Datenverkehr des virtuellen Netzwerks zwangsweise an den lokalen Standort geleitet wird. Außerdem wird verhindert, dass die VMs des virtuellen Netzwerks die öffentliche Kommunikation aus dem Internet direkt akzeptieren, etwa RDP oder SSH aus dem Internet an die VMs.

### <a name="can-i-advertise-the-exact-prefixes-as-my-virtual-network-prefixes"></a>Kann ich die gleichen Präfixe wie meine Präfixe des virtuellen Netzwerks ankündigen?

Nein. Das Ankündigen der gleichen Präfixe wie Ihre Adresspräfixe des virtuellen Netzwerks wird von Azure blockiert oder gefiltert. Sie können jedoch ein Präfix ankündigen, das eine Obermenge der Inhalte Ihres virtuellen Netzwerks ist. 

Sie können beispielsweise 10.0.0.0/8 ankündigen, wenn für Ihr virtuelles Netzwerk der Adressraum 10.0.0.0/16 verwendet wird. Das Ankündigen von 10.0.0.0/16 oder 10.0.0.0/24 ist dagegen nicht möglich.

### <a name="can-i-use-bgp-with-my-connections-between-virtual-networks"></a>Kann ich BGP für meine Verbindungen zwischen virtuellen Netzwerken verwenden?
Ja. BGP kann sowohl für standortübergreifende Verbindungen als auch für Verbindungen zwischen virtuellen Netzwerken verwendet werden.

### <a name="can-i-mix-bgp-with-non-bgp-connections-for-my-azure-vpn-gateways"></a>Kann ich BGP-Verbindungen mit BGP-fremden Verbindungen für meine Azure-VPN-Gateways kombinieren?
Ja. Für ein Azure-VPN-Gateway kann eine Kombination aus BGP-Verbindungen und BGP-fremden Verbindungen verwendet werden.

### <a name="does-azure-vpn-gateway-support-bgp-transit-routing"></a>Wird BGP-Transitrouting von Azure VPN Gateway unterstützt?
Ja. BGP-Transitrouting wird unterstützt. Einzige Einschränkung: Azure-VPN-Gateways kündigen gegenüber anderen BGP-Peers keine Standardrouten an. Wenn Sie Transitrouting über mehrere Azure-VPN-Gateways hinweg nutzen möchten, müssen Sie BGP für alle Zwischenverbindungen für virtuelle Netzwerke aktivieren. Weitere Informationen finden Sie in der [Übersicht über BGP](../articles/vpn-gateway/vpn-gateway-bgp-overview.md).

### <a name="can-i-have-more-than-one-tunnel-between-an-azure-vpn-gateway-and-my-on-premises-network"></a>Kann ich zwischen einem Azure-VPN-Gateway und meinem lokalen Netzwerk mehrere Tunnel verwenden?
Ja. Zwischen einem Azure-VPN-Gateway und Ihrem lokalen Netzwerk können mehrere Site-to-Site-VPN-Tunnel (S2S) eingerichtet werden. Beachten Sie, dass diese Tunnel alle auf die Gesamtanzahl von Tunneln für Ihre Azure-VPN-Gateways angerechnet werden und dass Sie BGP für beide Tunnel aktivieren müssen.

Wenn also etwa zwischen Ihrem Azure-VPN-Gateway und einem Ihrer lokalen Netzwerke zwei redundante Tunnel bestehen, werden dadurch zwei Tunnel des Gesamtkontingents für Ihr Azure-VPN-Gateway belegt.

### <a name="can-i-have-multiple-tunnels-between-two-azure-virtual-networks-with-bgp"></a>Kann ich zwischen zwei virtuellen Azure-Netzwerken mit BGP mehrere Tunnel verwenden?
Ja. Aber mindestens eines der virtuellen Netzwerkgateways muss über eine Aktiv/Aktiv-Konfiguration verfügen.

### <a name="can-i-use-bgp-for-s2s-vpn-in-an-azure-expressroute-and-s2s-vpn-coexistence-configuration"></a>Kann ich BGP für S2S-VPN-Verbindungen in einer Konfiguration mit Azure ExpressRoute und S2S-VPN verwenden?
Ja. 

### <a name="what-should-i-add-to-my-on-premises-vpn-device-for-the-bgp-peering-session"></a>Was muss ich meinem lokalen VPN-Gerät für die BGP-Peeringsitzung hinzufügen?
Fügen Sie auf Ihrem VPN-Gerät eine Hostroute der IP-Adresse des Azure-BGP-Peers hinzu. Diese Route verweist auf den IPsec-S2S-VPN-Tunnel. Lautet die Azure-VPN-Peer-IP-Adresse also etwa 10.12.255.30, müssen Sie eine Hostroute für 10.12.255.30 mit einer Nexthop-Schnittstelle der entsprechenden IPSec-Tunnelschnittstelle auf Ihrem VPN-Gerät hinzufügen.

### <a name="does-the-virtual-network-gateway-support-bfd-for-s2s-connections-with-bgp"></a>Unterstützt das Gateway des virtuellen Netzwerks BFD für Site-to-Site-Verbindungen mit BGP?
Nein. Bidirectional Forwarding Detection (BFD) ist ein Protokoll, das mit BGP verwendet werden kann, um Nachbar-Downtime schneller zu erkennen als bei Verwendung standardmäßiger BGP-Keepalives. BFD verwendet Zeitgeber im Sekundenbruchteilbereich. Diese sind für den Einsatz in LAN-Umgebungen konzipiert, aber nicht für öffentliche Internetverbindungen oder für WAN-Verbindungen geeignet.

Bei Verbindungen über das öffentliche Internet ist es nicht ungewöhnlich, dass bestimmte Pakete verzögert oder sogar verworfen werden. Daher würde die Einführung dieser aggressiven Zeitgeber zu mehr Instabilität führen. Dies hätte unter Umständen eine Dämpfung von Routen durch BGP zur Folge. Alternativ können Sie Ihr lokales Gerät mit Zeitgebern konfigurieren, die niedriger sind als das standardmäßige Keepalive-Intervall von 60 Sekunden und der Hold-Zeitgeber von 180 Sekunden. Damit wird eine schnellere Konvergenz erreicht.
