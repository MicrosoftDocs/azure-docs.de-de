---
title: Peering in virtuellen Azure-Netzwerken
titlesuffix: Azure Virtual Network
description: Erfahren Sie mehr über das Peering virtueller Netzwerke in Azure, einschließlich wie es Ihnen ermöglicht, Netzwerke in Azure Virtual Network zu verbinden.
services: virtual-network
documentationcenter: na
author: altambaw
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: kumud
ms.openlocfilehash: 61617777efa8241c93b2b5ffe42fc1d914b6ebcf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004975"
---
# <a name="virtual-network-peering"></a>Peering in virtuellen Netzwerken

Durch das Peering virtueller Netzwerke können mehrere [virtuelle Netzwerke](virtual-networks-overview.md) nahtlos mit Azure verbunden werden. Die virtuellen Netzwerke werden für Verbindungszwecke als einzelnes Element angezeigt. Der Datenverkehr zwischen virtuellen Computern in virtuellen Netzwerken mit Peering erfolgt über die Microsoft-Backboneinfrastruktur. Wie der Datenverkehr zwischen virtuellen Computern im selben Netzwerk wird auch der Datenverkehr nur über das *private* Netzwerk von Microsoft geleitet.

Azure unterstützt die folgenden Arten von Peering:

* Peering virtueller Netzwerke: Herstellen von Verbindungen zwischen virtuellen Netzwerken in derselben Azure-Region.
* Globales Peering virtueller Netzwerke: Herstellen von Verbindungen zwischen virtuellen Netzwerken über Azure-Regionen hinweg.

Die Verwendung von VNET-Peering (lokal oder global) bietet unter anderem folgende Vorteile:

* Niedrige Latenz, Verbindung mit hoher Bandbreite zwischen Ressourcen in unterschiedlichen virtuellen Netzwerken
* Die Möglichkeit zur Kommunikation für Ressourcen in einem virtuellen Netzwerk mit Ressourcen in einem anderen virtuellen Netzwerk.
* Die Möglichkeit, Daten zwischen virtuellen Netzwerken über Azure-Abonnements, Azure Active Directory-Mandanten, Bereitstellungsmodelle und Azure-Regionen hinweg zu übertragen.
* Die Möglichkeit zum Peering virtueller Netzwerke, die über Azure Resource Manager erstellt wurden.
* Die Möglichkeit zum Peering eines über Resource Manager erstellten virtuellen Netzwerks mit einem über das klassische Bereitstellungsmodell erstellten virtuellen Netzwerks. Weitere Informationen zu den Azure-Bereitstellungsmodellen finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Keine Ausfallzeiten für Ressourcen in beiden virtuellen Netzwerken beim Erstellen des Peerings oder nachdem das Peering erstellt wurde

Netzwerkdatenverkehr zwischen virtuellen Netzwerken, die mittels Peering verknüpft sind, ist privat. Datenverkehr zwischen den virtuellen Netzwerken bleibt innerhalb des Microsoft-Backbone-Netzwerks. Die Kommunikation zwischen den virtuellen Netzwerken kommt ganz ohne öffentliches Internet, Gateways oder Verschlüsselung aus.

## <a name="connectivity"></a>Konnektivität

Für virtuelle Netzwerke mit Peering kann für Ressourcen in einem der virtuellen Netzwerke eine direkte Verbindung mit den Ressourcen im virtuellen Peernetzwerk hergestellt werden.

Die Netzwerklatenz zwischen virtuellen Computern in per Peering verknüpften virtuellen Netzwerken in der gleichen Region entspricht der Netzwerklatenz in einem einzelnen virtuellen Netzwerk. Der Netzwerkdurchsatz basiert auf der Bandbreite, die für den virtuellen Computer proportional zu seiner Größe zulässig ist. Im Peering bestehen keine weiteren Bandbreiteneinschränkungen.

Der Datenverkehr zwischen virtuellen Computern in mittels Peering verknüpften virtuellen Netzwerken wird nicht über ein Gateway oder das öffentliche Internet, sondern direkt über die Microsoft-Backbone-Infrastruktur geleitet.

Sie können Netzwerksicherheitsgruppen in beiden virtuellen Netzwerken anwenden, um den Zugriff auf andere virtuelle Netzwerke oder Subnetze zu blockieren.
Beim Konfigurieren des VNET-Peerings können Sie die Regeln für Netzwerksicherheitsgruppen zwischen den virtuellen Netzwerken öffnen oder schließen. Wenn Sie sich für das Öffnen der vollständigen Konnektivität zwischen den mittels Peering verknüpften virtuellen Netzwerken entscheiden, können Sie Netzwerksicherheitsgruppen verwenden, um den spezifischen Zugriff jeweils zu blockieren oder zu verweigern. „Vollständige Konnektivität“ ist die Standardoption. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Sicherheitsgruppen](security-overview.md).

## <a name="service-chaining"></a>Dienstverkettung

Eine Dienstverkettung ermöglicht es, Datenverkehr über benutzerdefinierte Routingtabellen aus einem virtuellen Netzwerk an ein virtuelles Gerät oder Gateway in einem Netzwerk mit Peering zu leiten.

Um die Dienstverkettung zu aktivieren, konfigurieren Sie benutzerdefinierte Routen, die auf virtuelle Computer in mittels Peering verknüpften virtuellen Netzwerken als IP-Adresse für den *nächsten Hop* verweisen. Benutzerdefinierte Routen können auch auf Gateways für virtuelle Netzwerke verweisen, um die Dienstverkettung zu aktivieren.

Sie können auch Hub-and-Spoke-Netzwerke bereitstellen, in denen das virtuelle Hubnetzwerk Infrastrukturkomponenten wie etwa ein virtuelles Netzwerkgerät oder ein VPN-Gateway hostet. Alle virtuellen Spoke-Netzwerke können dann mittels Peering mit dem virtuellen Hubnetzwerk verknüpft werden. Der Datenverkehr durchläuft virtuelle Netzwerkgeräte oder VPN-Gateways im virtuellen Hubnetzwerk.

Beim VNET-Peering kann es sich bei dem nächsten Hop einer benutzerdefinierten Route um die IP-Adresse eines virtuellen Computers im mittels Peering verknüpften virtuellen Netzwerk oder um ein VPN-Gateway handeln. Zwischen virtuellen Netzwerken ist jedoch kein Routing über eine benutzerdefinierte Route möglich, die als Art des nächsten Hops ein Azure ExpressRoute-Gateway angibt. Für weitere Informationen zu benutzerdefinierten Routen können Sie sich die [Übersicht zu benutzerdefinierten Routen](virtual-networks-udr-overview.md#user-defined) ansehen. Informationen zum Erstellen einer Hub-and-Spoke-Netzwerktopologie finden Sie unter [Hub-and-Spoke-Netzwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateways und lokale Konnektivität

Jedes virtuelle Netzwerk, einschließlich eines mittels Peering verknüpften virtuellen Netzwerks, kann ein eigenes Gateway besitzen. Ein virtuelles Netzwerk kann sein Gateway verwenden, um eine Verbindung mit einem lokalen Netzwerk herzustellen. Sie können auch [VNET-zu-VNET-Verbindungen](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) unter Verwendung von Gateways konfigurieren, selbst für mittels Peering verknüpfte virtuelle Netzwerke.

Wenn Sie beide Optionen für Verbindungen zwischen virtuellen Netzwerken konfigurieren, fließt der Datenverkehr zwischen den virtuellen Netzwerken über die Peeringkonfiguration. Der Datenverkehr verwendet den Azure-Backbone.

Sie können auch das Gateway im mittels Peering verknüpften virtuellen Netzwerk als Transitpunkt für ein lokales Netzwerk konfigurieren. In diesem Fall kann das virtuelle Netzwerk, das ein Remotegateway verwendet, kein eigenes Gateway besitzen. Ein virtuelles Netzwerk hat nur ein Gateway. Bei diesem Gateway handelt es sich um ein lokales Gateway oder ein Remotegateway im mittels Peering verknüpften virtuellen Netzwerk, wie im folgenden Diagramm zu sehen ist:

![VNET-Peering – Transit](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Sowohl das Peering virtueller Netzwerke als auch das globale Peering virtueller Netzwerke unterstützen den Gatewaytransit.

Gatewaytransit zwischen virtuellen Netzwerken, die mit unterschiedlichen Bereitstellungsmodellen erstellt wurden, wird unterstützt. Das Gateway muss sich im Resource Manager-Modell im virtuellen Netzwerk befinden. Weitere Informationen zur Verwendung eines Gateways für den Transit finden Sie unter [Konfigurieren eines VPN-Gateways für den Transit in einem Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Wenn Sie virtuelle Netzwerke, die sich eine einzelne Azure ExpressRoute-Verbindung teilen, mittels Peering verknüpfen, durchläuft der Datenverkehr zwischen ihnen die Peeringbeziehung. Der Datenverkehr verwendet das Azure-Backbone-Netzwerk. Sie können in den einzelnen virtuellen Netzwerken weiterhin lokale Gateways verwenden, um eine Verbindung mit der lokalen Umgebung herzustellen. Andernfalls können Sie ein gemeinsam genutztes Gateway verwenden und den Transit für lokale Konnektivität konfigurieren.

## <a name="troubleshoot"></a>Problembehandlung

Sie können effektive Routen überprüfen, um zu bestätigen, dass virtuelle Netzwerke mittels Peering verknüpft sind. Überprüfen Sie die Routen für eine Netzwerkschnittstelle in einem beliebigen Subnetz in einem virtuellen Netzwerk. Ist ein VNET-Peering vorhanden, verfügen alle Subnetze innerhalb des virtuellen Netzwerks über Routen, deren Art des nächsten Hops *VNET-Peering* lautet. Dies gilt für jeden Adressraum in jedem mittels Peering verknüpften virtuellen Netzwerk. Weitere Informationen finden Sie unter [Diagnose des Routingproblems einer VM](diagnose-network-routing-problem.md).

Probleme mit der Konnektivität eines virtuellen Computers in einem mittels Peering verknüpften virtuellen Netzwerk können auch mithilfe von Azure Network Watcher behandelt werden. Mit der Konnektivitätsprüfung können Sie ermitteln, wie Datenverkehr von der Netzwerkschnittstelle eines virtuellen Quellcomputers an die Netzwerkschnittstelle eines virtuellen Zielcomputers geleitet wird. Weitere Informationen finden Sie unter [Problembehandlung für Verbindungen mit Azure Network Watcher und dem Azure-Portal](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

Sie können auch die Anleitung zum [Beheben von Problemen beim Peering in virtuellen Netzwerken](virtual-network-troubleshoot-peering-issues.md) ausprobieren.

## <a name="constraints-for-peered-virtual-networks"></a>Einschränkungen für virtuelle Netzwerke mit Peering<a name="requirements-and-constraints"></a>

Die folgenden Einschränkungen gelten nur, wenn virtuelle Netzwerke über globales Peering verbunden werden:

* Ressourcen in einem virtuellen Netzwerk können nicht mit der Front-End-IP-Adresse eines internen Load Balancers (ILB) im Tarif „Basic“ in einem per globalem Peering verbundenen virtuellen Netzwerk kommunizieren.
* Einige Dienste, die einen Load Balancer im Tarif „Basic“ verwenden, funktionieren nicht über per globalem Peering verbundenen virtuellen Netzwerken. Weitere Informationen finden Sie unter [Welche Einschränkungen gibt es im Zusammenhang mit globalem VNET-Peering und Load Balancern?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Weitere Informationen finden Sie unter [Anforderungen und Einschränkungen](virtual-network-manage-peering.md#requirements-and-constraints). Weitere Informationen zur unterstützten Peeringanzahl finden Sie unter [Grenzwerte für Netzwerke](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Berechtigungen

Informationen zu erforderlichen Berechtigungen für die Erstellung eines Peerings virtueller Netzwerke finden Sie unter [Berechtigungen](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Preise

Für ein- und ausgehenden Datenverkehr, der eine VNET-Peeringverbindung verwendet, fällt eine Gebühr an. Weitere Informationen finden Sie unter [Virtual Network – Preise](https://azure.microsoft.com/pricing/details/virtual-network).

Gatewaytransit ist eine Peeringeigenschaft, die es einem virtuellen Netzwerk ermöglicht, ein VPN/ExpressRoute-Gateway im virtuellen Netzwerk mit Peeringbeziehung zu nutzen. Gatewaytransit funktioniert sowohl für die standortübergreifende als auch für Netzwerk-zu-Netzwerk-Verbindungen. Für ein- oder ausgehenden Gatewaydatenverkehr im per Peering verbundenen virtuellen Netzwerk fallen im Spoke-VNET (oder Nicht-Gateway-VNET) Gebühren für mittels Peering verbundene virtuelle Netzwerke an. Weitere Informationen zu den Gebühren für VPN Gateways und zu den ExpressRoute-Gateway-Preisen für ExpressRoute-Gateway-Gebühren finden Sie unter [VPN Gateway – Preise](https://azure.microsoft.com/pricing/details/vpn-gateway/).

>[!NOTE]
> In einer früheren Version dieses Dokuments war angegeben, dass im Spoke-VNET (oder Nicht-Gateway-VNET) mit Gatewaytransit keine Gebühren für mittels Peering verbundene virtuelle Netzwerke anfallen. Es spiegelt jetzt die genauen Preise gemäß der Preisübersicht wider.

## <a name="next-steps"></a>Nächste Schritte

* Sie können das Peering zwischen zwei virtuellen Netzwerken erstellen. Die Netzwerke können zu demselben Abonnement, zu verschiedenen Bereitstellungsmodellen innerhalb desselben Abonnements oder zu verschiedenen Abonnements gehören. Arbeiten Sie ein Tutorial für eines der folgenden Szenarien durch:

    |Azure-Bereitstellungsmodell             | Subscription  |
    |---------                          |---------|
    |Beide mit Resource Manager              |[Gleich](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Unterschiedlich](create-peering-different-subscriptions.md)|
    |Einmal Resource Manager, einmal klassisch  |[Gleich](create-peering-different-deployment-models.md)|
    |                                   |[Unterschiedlich](create-peering-different-deployment-models-subscriptions.md)|

* Informationen zum Erstellen einer Hub-and-Spoke-Netzwerktopologie finden Sie unter [Hub-and-Spoke-Netzwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Informationen zu allen Einstellungen für das Peering virtueller Netzwerke finden Sie unter [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md).
* Antworten auf allgemeine Fragen zum Peering virtueller Netzwerke und zum globalen Peering virtueller Netzwerke finden Sie unter [VNET-Peering](virtual-networks-faq.md#vnet-peering).
