---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/03/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72ddd0b6cd6c3e12417d3698c403f89312b531f4
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "67177912"
---
Die häufig gestellten Fragen zu VNET-zu-VNET-Verbindungen gelten für VPN Gateway-Verbindungen. Informationen zum VNET-Peering finden Sie unter [Peering virtueller Netzwerke](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="does-azure-charge-for-traffic-between-vnets"></a>Fallen bei Azure Kosten für den Datenverkehr zwischen VNets an?

VNET-zu-VNET-Datenverkehr innerhalb einer Region ist bei Verwendung einer VPN-Gatewayverbindung in beide Richtungen kostenlos. Für regionsübergreifenden VNET-zu-VNET-Datenverkehr in ausgehender Richtung werden Gebühren für ausgehende Datenübertragungen zwischen VNETs basierend auf den Quellregionen berechnet. Weitere Informationen finden Sie unter [VPN Gateway – Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/). Wenn Sie eine Verbindung zwischen Ihren VNETs mittels VNET-Peering anstelle eines VPN Gateways herstellen, finden Sie weitere Informationen auf der Seite [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="does-vnet-to-vnet-traffic-travel-across-the-internet"></a>Wird VNET-zu-VNET-Datenverkehr über das Internet übertragen?

Nein. VNET-zu-VNET-Datenverkehr wird über den Microsoft Azure-Backbone übertragen, nicht über das Internet.

### <a name="can-i-establish-a-vnet-to-vnet-connection-across-azure-active-directory-aad-tenants"></a>Kann ich eine VNET-zu-VNET-Verbindung zwischen Azure Active Directory-Mandanten (AAD) herstellen?

Ja. VNET-zu-VNET-Verbindungen mit Azure-VPN-Gateways können übergreifend für AAD-Mandanten verwendet werden.

### <a name="is-vnet-to-vnet-traffic-secure"></a>Ist VNet-zu-VNet-Datenverkehr sicher?

Ja, er wird mittels IPsec-/IKE-Verschlüsselung geschützt.

### <a name="do-i-need-a-vpn-device-to-connect-vnets-together"></a>Benötige ich ein VPN-Gerät, umVNets miteinander zu verbinden?

Nein. Für das Verbinden mehrerer virtueller Azure-Netzwerke sind keine VPN-Geräte erforderlich. Diese werden nur benötigt, wenn standortübergreifende Konnektivität erforderlich ist.

### <a name="do-my-vnets-need-to-be-in-the-same-region"></a>Müssen sich meine VNets in der gleichen Region befinden?

Nein. Die virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen (Standorte) befinden.

### <a name="if-the-vnets-arent-in-the-same-subscription-do-the-subscriptions-need-to-be-associated-with-the-same-active-directory-tenant"></a>Müssen die Abonnements demselben Active Directory-Mandanten zugeordnet sein, wenn sich die VNETs nicht im selben Abonnement befinden?

Nein.

### <a name="can-i-use-vnet-to-vnet-to-connect-virtual-networks-in-separate-azure-instances"></a>Kann ich VNet-zu-VNet verwenden, um virtuelle Netzwerke in separaten Azure-Instanzen zu verbinden? 

Nein. Für VNet-zu-VNet wird die Verbindungsherstellung von virtuellen Netzwerken in derselben Azure-Instanz unterstützt. Beispielsweise ist es nicht möglich, eine Verbindung zwischen Azure global und den Azure-Instanzen für China, Deutschland oder die US-Regierung herzustellen. Erwägen Sie für diese Szenarien die Verwendung einer Site-to-Site-VPN-Verbindung.

### <a name="can-i-use-vnet-to-vnet-along-with-multi-site-connections"></a>Kann ich VNet-zu-VNet zusammen mit Verbindungen mit mehreren Standorten verwenden?

Ja. Virtuelle Netzwerkverbindungen können simultan mit VPNs mit mehreren Standorten verwendet werden.

### <a name="how-many-on-premises-sites-and-virtual-networks-can-one-virtual-network-connect-to"></a>Mit wie vielen lokalen Standorten und virtuellen Netzwerken kann ein virtuelles Netzwerk verbunden werden?

Informationen dazu finden Sie in der Tabelle [Gatewayanforderungen](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#requirements).

### <a name="can-i-use-vnet-to-vnet-to-connect-vms-or-cloud-services-outside-of-a-vnet"></a>Kann ich VNet-zu-VNet für die Verbindung von virtuellen Computern oder Clouddiensten außerhalb eines VNet verwenden?

Nein. VNet-zu-VNet unterstützt das Verbinden virtueller Netzwerke. Nicht unterstützt werden Verbindungen virtueller Computer oder Clouddienste, die sich nicht in einem virtuellen Netzwerk befinden.

### <a name="can-a-cloud-service-or-a-load-balancing-endpoint-span-vnets"></a>Kann sich ein Clouddienst oder ein Lastenausgleichs-Endpunkt über mehrere VNETs erstrecken?

Nein. Ein Clouddienst oder Endpunkt mit Lastenausgleich kann nicht mehrere virtuelle Netzwerke umfassen, selbst wenn diese verbunden sind.

### <a name="can-i-use-a-policybased-vpn-type-for-vnet-to-vnet-or-multi-site-connections"></a>Kann ich den VPN-Typ „PolicyBased“ für VNET-zu-VNET- oder standortübergreifende Verbindungen verwenden?

Nein. VNET-zu-VNET- und standortübergreifende Verbindungen erfordern Azure-VPN Gateways mit routenbasierten VPN-Typen (zuvor als „dynamisches Routing“ bezeichnet).

### <a name="can-i-connect-a-vnet-with-a-routebased-vpn-type-to-another-vnet-with-a-policybased-vpn-type"></a>Kann ich ein VNet mit einem routenbasierten VPN-Typ (RouteBased) mit einem anderen VNet mit einem richtlinienbasierten VPN (PolicyBased) verbinden?

Nein. Beide virtuellen Netzwerke müssen routenbasierte VPNs (zuvor als „dynamisches Routing“ bezeichnet) verwenden.

### <a name="do-vpn-tunnels-share-bandwidth"></a>Verwenden VPN-Tunnel Bandbreite gemeinsam?

Ja. Alle VPN-Tunnel des virtuellen Netzwerks verwenden die verfügbare Bandbreite auf dem Azure-VPN-Gateway und die gleiche SLA für die Verfügbarkeit des VPN-Gateways in Azure gemeinsam.

### <a name="are-redundant-tunnels-supported"></a>Werden redundante Tunnel unterstützt?

Redundante Tunnel zwischen einem Paar virtueller Netzwerke werden unterstützt, wenn ein virtuelles Netzwerkgateway als „Aktiv-Aktiv“ konfiguriert ist.

### <a name="can-i-have-overlapping-address-spaces-for-vnet-to-vnet-configurations"></a>Kann ich für VNet-zu-VNet-Konfigurationen Adressräume verwenden, die sich überschneiden?

Nein. IP-Adressbereiche dürfen sich nicht überschneiden.

### <a name="can-there-be-overlapping-address-spaces-among-connected-virtual-networks-and-on-premises-local-sites"></a>Dürfen sich die Adressräume der verbundenen virtuellen Netzwerke und der lokalen Standorte überschneiden?

Nein. IP-Adressbereiche dürfen sich nicht überschneiden.



