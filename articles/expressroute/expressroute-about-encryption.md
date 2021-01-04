---
title: 'Azure ExpressRoute: Informationen zur Verschlüsselung'
description: In diesem Artikel wird die ExpressRoute-Verschlüsselung erläutert.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 693d2304324bdfcac298b3e20ddd0d882a16533c
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899872"
---
# <a name="expressroute-encryption"></a>ExpressRoute-Verschlüsselung
 
ExpressRoute unterstützt eine Reihe von Verschlüsselungstechnologien, um Vertraulichkeit und Integrität der Daten zu gewährleisten, die zwischen Ihrem Netzwerk und dem Netzwerk von Microsoft übertragen werden.

## <a name="point-to-point-encryption-by-macsec-faq"></a>Häufig gestellte Fragen zur Point-to-Point-Verschlüsselung durch MACsec
MACsec ist ein [IEEE-Standard](https://1.ieee802.org/security/802-1ae/). Er verschlüsselt Daten auf der MAC-Ebene (Media Access Control) oder Netzwerkebene 2. Sie können mit MACsec die physischen Verbindungen zwischen Ihren Netzwerkgeräten und den Netzwerkgeräten von Microsoft verschlüsseln, wenn Sie über [ExpressRoute Direct](expressroute-erdirect-about.md) eine Verbindung mit Microsoft herstellen. MACsec ist standardmäßig für ExpressRoute Direct-Ports deaktiviert. Sie geben Ihren eigenen MACsec-Schlüssel für die Verschlüsselung an und speichern diesen in [Azure Key Vault](../key-vault/general/overview.md). Sie entscheiden, wann der Schlüssel gedreht werden soll. Weitere häufig gestellte Fragen finden Sie unten.
### <a name="can-i-enable-macsec-on-my-expressroute-circuit-provisioned-by-an-expressroute-provider"></a>Kann ich MACsec für meine ExpressRoute-Verbindung aktivieren, die von einem ExpressRoute-Anbieter bereitgestellt wurde?
Nein. MACsec verschlüsselt den gesamten Datenverkehr in einer physischen Verbindung mit einem Schlüssel, deren Besitzer eine Entität ist (d. h. der Kunde). Daher ist es ausschließlich für ExpressRoute Direct verfügbar.
### <a name="can-i-encrypt-some-of-the-expressroute-circuits-on-my-expressroute-direct-ports-and-leave-other-circuits-on-the-same-ports-unencrypted"></a>Kann ich einige der ExpressRoute-Verbindungen an meinen ExpressRoute Direct-Ports verschlüsseln und andere Verbindungen an denselben Ports unverschlüsselt lassen? 
Nein. Wenn MACsec aktiviert ist, werden der gesamte Netzwerkkontrolldatenverkehr (z. B. der BGP-Datenverkehr) und der Kundendatenverkehr verschlüsselt. 
### <a name="when-i-enabledisable-macsec-or-update-macsec-key-will-my-on-premises-network-lose-connectivity-to-microsoft-over-expressroute"></a>Wird für mein lokales Netzwerk die Verbindung mit Microsoft über ExpressRoute unterbrochen, wenn ich MACsec aktiviere/deaktiviere oder den MACsec-Schlüssel aktualisiere?
Ja. Für die MACsec-Konfiguration unterstützen wir ausschließlich den Modus „Vorinstallierter Schlüssel“. Das heißt, Sie müssen den Schlüssel sowohl auf Ihren Geräten als auch (über unsere API) auf den Geräten von Microsoft aktualisieren. Diese Änderung ist nicht atomisch, daher geht die Verbindung verloren, wenn ein Schlüsselkonflikt zwischen den beiden Seiten vorliegt. Es wird dringend empfohlen, ein Wartungsfenster für die Konfigurationsänderung einzuplanen. Zum Minimieren der Ausfallzeit empfehlen wir, die Konfiguration für jeweils einen Link von ExpressRoute Direct zu aktualisieren, nachdem Sie Ihren Netzwerkdatenverkehr auf den anderen Link umgestellt haben.  
### <a name="will-traffic-continue-to-flow-if-theres-a-mismatch-in-macsec-key-between-my-devices-and-microsofts"></a>Fließt der Datenverkehr weiter, wenn ein Konflikt zwischen dem MACsec-Schlüssel auf meinen Geräten und denen von Microsoft vorliegt?
Nein. Wenn MACsec konfiguriert ist und ein Schlüsselkonflikt auftritt, geht die Verbindung mit Microsoft verloren. Anders ausgedrückt: Wir führen keinen Fallback auf eine unverschlüsselte Verbindung aus und legen Ihre Daten auf diese Weise offen. 
### <a name="will-enabling-macsec-on-expressroute-direct-degrade-network-performance"></a>Wird durch das Aktivieren von MACsec für ExpressRoute Direct die Netzwerkleistung beeinträchtigt?
Die Verschlüsselung und Entschlüsselung von MACsec erfolgt auf Hardware auf den von uns verwendeten Routern. Auf unserer Seite wird die Leistung nicht beeinträchtigt. Sie sollten sich jedoch mit dem Netzwerkanbieter der von Ihnen verwendeten Geräte in Verbindung setzen und ermitteln, ob sich MACsec auf die Leistung auswirkt.
### <a name="which-cipher-suites-are-supported-for-encryption"></a>Welche Verschlüsselungssammlungen werden für die Verschlüsselung unterstützt?
Wir unterstützen nur die [XPN](https://1.ieee802.org/security/802-1aebw/)-Version (Extended Packet Numbering) von AES-128 und AES-256. Sie müssen außerdem [SCI](https://wikipedia.org/wiki/IEEE_802.1AE) (Secure Channel Identifier) in der MACsec-Konfiguration auf Ihrem Gerät deaktivieren. 

## <a name="end-to-end-encryption-by-ipsec-faq"></a>Häufig gestellte Fragen zur End-to-End-Verschlüsselung durch IPsec
IPsec ist ein [IETF-Standard](https://tools.ietf.org/html/rfc6071). Daten werden auf der IP-Ebene (Internet Protocol) oder auf der Netzwerkebene 3 verschlüsselt. Sie können mit IPsec eine End-to-End-Verbindung zwischen Ihrem lokalen Netzwerk und Ihrem virtuellen Netzwerk (VNet) in Azure verschlüsseln. Weitere häufig gestellte Fragen finden Sie unten.
### <a name="can-i-enable-ipsec-in-addition-to-macsec-on-my-expressroute-direct-ports"></a>Kann ich IPsec zusätzlich zu MACsec an meinen ExpressRoute Direct-Ports aktivieren?
Ja. MACsec sichert die physischen Verbindungen zwischen Ihnen und Microsoft. IPsec sichert die End-to-End-Verbindung zwischen Ihnen und Ihren virtuellen Netzwerken in Azure. Sie können sie unabhängig voneinander aktivieren. 
### <a name="can-i-use-azure-vpn-gateway-to-set-up-the-ipsec-tunnel-over-azure-private-peering"></a>Kann ich das Azure-VPN-Gateway verwenden, um den IPsec-Tunnel über das private Azure-Peering einzurichten?
Ja. Wenn Sie Azure Virtual WAN einführen, können Sie [diese Schritte](../virtual-wan/vpn-over-expressroute.md) zum Verschlüsseln der Ende-zu-Ende-Verbindung verwenden. Wenn Sie über ein reguläres Azure VNET verfügen, können Sie [diese Schritte](../vpn-gateway/site-to-site-vpn-private-peering.md) ausführen, um einen IPsec-Tunnel zwischen Azure VPN Gateway und Ihrem lokalen VPN-Gateway einzurichten.
### <a name="what-is-the-throughput-i-will-get-after-enabling-ipsec-on-my-expressroute-connection"></a>Welchen Durchsatz erhalte ich nach dem Aktivieren von IPsec für meine ExpressRoute-Verbindung?
Sehen Sie bei Verwendung des Azure-VPN-Gateway [hier die Leistungswerte](../vpn-gateway/vpn-gateway-about-vpngateways.md) ein. Wenn ein VPN-Gateway eines Drittanbieters verwendet wird, wenden Sie sich an den Anbieter, um die Leistungswerte zu erfragen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur MACsec-Konfiguration finden Sie unter [Konfigurieren von MACsec](expressroute-howto-macsec.md).

Weitere Informationen zur IPsec-Konfiguration finden Sie unter [Konfigurieren von IPsec](site-to-site-vpn-over-microsoft-peering.md).
