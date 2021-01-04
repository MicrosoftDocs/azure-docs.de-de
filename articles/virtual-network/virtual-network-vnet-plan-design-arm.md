---
title: Planen virtueller Azure-Netzwerke | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie virtuelle Netzwerke basierend auf Ihren Anforderungen in Bezug auf Isolierung, Verbindung und Standort planen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: kumud
ms.openlocfilehash: 29ea65e94e97b69e24c6935328cc01c2295adc5a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518635"
---
# <a name="plan-virtual-networks"></a>Planen virtueller Netzwerke

Das Erstellen eines virtuellen Netzwerks zum Testen ist einfach. Aber die Wahrscheinlichkeit ist hoch, dass Sie im Lauf der Zeit mehrere virtuelle Netzwerke bereitstellen, um die Produktionsanforderungen Ihrer Organisation zu erfüllen. Mit etwas Planung können Sie beim Bereitstellen von virtuellen Netzwerken und beim Herstellen einer Verbindung mit den Ressourcen effektiver vorgehen. Die Informationen in diesem Artikel sind besonders hilfreich, wenn Sie bereits mit virtuellen Netzwerken vertraut sind und etwas Erfahrung mit deren Verwendung haben. Wenn Sie nicht mit virtuellen Netzwerken vertraut sind, empfiehlt es sich, den Artikel zur [Übersicht über virtuelle Netzwerke](virtual-networks-overview.md) zu lesen.

## <a name="naming"></a>Benennung

Alle Azure-Ressourcen haben einen Namen. Der Name muss innerhalb eines Bereichs eindeutig sein, der für jeden Ressourcentyp unterschiedlich sein kann. Der Name eines virtuellen Netzwerks muss beispielsweise innerhalb einer [Ressourcengruppe](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) eindeutig sein, kann jedoch innerhalb eines [Abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) oder einer Azure-[Region](https://azure.microsoft.com/regions/#services) dupliziert werden. Das Definieren einer Namenskonvention, die Sie beim Benennen von Ressourcen konsistent verwenden können, ist hilfreich, wenn mit der Zeit mehrere Netzwerkressourcen verwaltet werden. Empfehlungen finden Sie unter [Namenskonventionen](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork).

## <a name="regions"></a>Regions

Alle Azure-Ressourcen werden in einer Azure-Region und unter einem Abonnement erstellt. Eine Ressource kann nur in einem virtuellen Netzwerk erstellt werden, das in derselben Region und unter demselben Abonnement wie die Ressource vorhanden ist. Sie können jedoch virtuelle Netzwerke verbinden, die in unterschiedlichen Abonnements und Regionen vorhanden sind. Weitere Informationen finden Sie unter [Konnektivität](#connectivity). Bei der Festlegung, in welchen Regionen Ressourcen bereitgestellt werden, sollte berücksichtigt werden, wo sich die Nutzer der Ressourcen physisch befinden:

- Nutzer von Ressourcen erwarten normalerweise die geringstmögliche Netzwerklatenz für ihre Ressourcen. Informationen zum Ermitteln der relativen Latenz zwischen einem bestimmten Standort und Azure-Regionen finden Sie unter [Anzeigen der relativen Latenz](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Haben Sie Anforderungen in Bezug auf Datenresidenz, Datenhoheit, Datenkonformität oder Datenresilienz festgelegt? Wenn ja, ist die Auswahl der Region, die den Anforderungen entspricht, entscheidend. Weitere Informationen finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).
- Ist Resilienz in den Azure-Verfügbarkeitszonen innerhalb derselben Azure-Region für die bereitgestellten Ressourcen erforderlich? Sie können Ressourcen, z.B. virtuelle Computer, in unterschiedlichen Verfügbarkeitszonen innerhalb desselben virtuellen Netzwerks bereitstellen. Verfügbarkeitszonen werden jedoch nicht in allen Azure-Regionen unterstützt. Weitere Informationen zu Verfügbarkeitszonen und zu den Regionen, in denen sie unterstützt werden, finden Sie unter [Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Abonnements

Innerhalb jedes Abonnements können Sie bis zum geltenden [Limit](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) beliebig viele virtuelle Netzwerke bereitstellen. Einige Organisationen verfügen beispielsweise für unterschiedliche Abteilungen über unterschiedliche Abonnements. Weitere Informationen und Überlegungen zu Abonnements finden Sie unter [Abonnementgovernance](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy).

## <a name="segmentation"></a>Segmentierung

Sie können mehrere virtuelle Netzwerke pro Abonnement und pro Region erstellen. Sie können in jedem virtuellen Netzwerk mehrere Subnetze erstellen. Unter Berücksichtigung der folgenden Überlegungen können Sie festlegen, wie viele virtuelle Netzwerke und Subnetze Sie benötigen:

### <a name="virtual-networks"></a>Virtuelle Netzwerke

Ein virtuelles Netzwerk ist ein virtueller und isolierter Bereich des öffentlichen Azure-Netzwerks. Jedes virtuelle Netzwerk ist Ihrem Abonnement zugeordnet. Bei der Entscheidung, ob Sie ein virtuelles Netzwerk oder mehrere virtuelle Netzwerke unter einem Abonnement erstellen, sollten folgende Aspekte berücksichtigt werden:

- Liegen Sicherheitsanforderungen der Organisation zur Isolierung von Datenverkehr in separaten virtuellen Netzwerken vor? Sie können auswählen, ob virtuelle Netzwerke verbunden oder nicht verbunden werden. Wenn Sie virtuelle Netzwerke verbinden, können Sie ein virtuelles Netzwerkgerät (z.B. eine Firewall) implementieren, um den ein- und ausgehenden Datenverkehr zwischen den virtuellen Netzwerken zu steuern. Weitere Informationen finden Sie unter [Sicherheit](#security) und [Konnektivität](#connectivity).
- Liegen Sicherheitsanforderungen der Organisation zur Isolierung von virtuellen Netzwerken in separaten [Abonnements](#subscriptions) oder [Regionen](#regions) vor?
- Eine [Netzwerkschnittstelle](virtual-network-network-interface.md) ermöglicht die Kommunikation zwischen einem virtuellen Computer und anderen Ressourcen. Jeder Netzwerkschnittstelle sind private IP-Adressen zugewiesen. Wie viele Netzwerkschnittstellen und [private IP-Adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) benötigen Sie in einem virtuellen Netzwerk? Die Anzahl der Netzwerkschnittstellen und privaten IP-Adressen, die in einem virtuellen Netzwerk festgelegt werden können, ist [begrenzt](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).
- Möchten Sie das virtuelle Netzwerk mit einem anderen virtuellen Netzwerk oder einem lokalen Netzwerk verbinden? Sie können einige virtuelle Netzwerke untereinander oder mit lokalen Netzwerken verbinden, jedoch nicht alle. Weitere Informationen finden Sie unter [Konnektivität](#connectivity). Jedes virtuelle Netzwerk, das Sie mit einem anderen virtuellen Netzwerk oder einem lokalen Netzwerk verbinden, muss einen eindeutigen Adressraum aufweisen. Jedes virtuelle Netzwerk verfügt über einen oder mehrere dem Adressraum zugewiesene öffentliche oder private Adressbereiche. Ein Adressbereich wird im CIDR-Format (Classless Interdomain Routing, klassenloses domänenübergreifendes Routing) angegeben, z.B. 10.0.0.0/16. Weitere Informationen zu Adressbereichen für virtuelle Netzwerke finden Sie [hier](manage-virtual-network.md#add-or-remove-an-address-range).
- Liegen Verwaltungsanforderungen der Organisation in Bezug auf Ressourcen in unterschiedlichen virtuellen Netzwerken vor? Wenn ja, können Sie Ressourcen in separaten virtuellen Netzwerken trennen, um die [Zuweisung von Berechtigungen](#permissions) zu Personen in Ihrer Organisation zu vereinfachen oder um unterschiedlichen virtuellen Netzwerken unterschiedliche Richtlinien zuzuweisen.
- Wenn Sie Azure-Dienstressourcen in einem virtuellen Netzwerk bereitstellen, wird jeweils ein zugehöriges virtuelles Netzwerk erstellt. In den jeweiligen Informationen zu jedem [Azure-Dienst, der in einem virtuellen Netzwerk bereitgestellt werden kann](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), können Sie einsehen, ob für einen Azure-Dienst ein eigenes zugehöriges virtuelles Netzwerk erstellt wird.

### <a name="subnets"></a>Subnetze

Ein virtuelles Netzwerk kann bis zu den geltenden [Limits](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Subnetze segmentiert werden. Bei der Entscheidung, ob Sie ein Subnetz oder mehrere virtuelle Netzwerke in einem Abonnement erstellen, sollten folgende Aspekte berücksichtigt werden:

- Jedes Subnetz muss über einen eindeutigen im CIDR-Format angegebenen Adressbereich innerhalb des Adressraums des virtuellen Netzwerks verfügen. Der Adressbereich darf keine Überlappungen mit anderen Subnetzen innerhalb des virtuellen Netzwerks aufweisen.
- Wenn Sie Azure-Dienstressourcen in einem virtuellen Netzwerk bereitstellen möchten, ist es möglich, dass für diese ein eigenes Subnetz erforderlich ist oder erstellt wird. Dafür muss ausreichend nicht zugewiesener Speicherplatz zur Verfügung stehen. In den jeweiligen Informationen zu jedem [Azure-Dienst, der in einem virtuellen Netzwerk bereitgestellt werden kann](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network), können Sie einsehen, ob für einen Azure-Dienst ein eigenes zugehöriges Subnetz erstellt wird. Wenn Sie z.B ein virtuelles Netzwerk über ein Azure VPN Gateway mit einem lokalen Netzwerk verbinden, muss das virtuelle Netzwerk über ein dediziertes Subnetz für das Gateway verfügen. Weitere Informationen zu Gatewaysubnetzen finden Sie [hier](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Standardmäßig leitet Azure Netzwerkdatenverkehr zwischen allen Subnetzen in einem virtuellen Netzwerk weiter. Sie können das Standardrouting in Azure außer Kraft setzen, um das Azure-Routing zwischen Subnetzen zu verhindern oder um Datenverkehr zwischen Subnetzen z.B. über ein virtuelles Netzwerkgerät weiterzuleiten. Wenn Sie festlegen möchten, dass Datenverkehr zwischen Ressourcen im selben virtuellen Netzwerk über ein virtuelles Netzwerkgerät übermittelt wird, stellen Sie die Ressourcen in unterschiedlichen Subnetzen bereit. Weitere Informationen finden Sie unter [Sicherheit](#security).
- Sie können den Zugriff auf Azure-Ressourcen, z. B. auf ein Azure Storage-Konto oder eine Azure SQL-Datenbank, auf bestimmte Subnetze mit einem VNET-Dienstendpunkt einschränken. Zudem können Sie den Zugriff auf die Ressourcen über das Internet verweigern. Sie können mehrere Subnetze erstellen und für bestimmte Subnetze einen Dienstendpunkt aktivieren, für andere hingegen nicht. Erfahren Sie mehr über [Dienstendpunkte](virtual-network-service-endpoints-overview.md) und die Azure-Ressourcen, für die Sie sie aktivieren können.
- Sie können jedem Subnetz in einem virtuellen Netzwerk eine Netzwerksicherheitsgruppe zuordnen, dies ist jedoch nicht erforderlich. Sie können jedem Subnetz dieselbe oder eine unterschiedliche Netzwerksicherheitsgruppe zuordnen. Jede Netzwerksicherheitsgruppe enthält Regeln, mit denen Datenverkehr zu und von Quellen und Zielen zugelassen oder verweigert wird. Weitere Informationen zu [Netzwerksicherheitsgruppen](#traffic-filtering).

## <a name="security"></a>Sicherheit

Sie können den Netzwerkdatenverkehr zu und von Ressourcen in einem virtuellen Netzwerk mithilfe von Netzwerksicherheitsgruppen und virtuellen Netzwerkgeräten filtern. Sie können steuern, wie in Azure Datenverkehr aus Subnetzen weitergeleitet wird. Zudem können Sie einschränken, welche Benutzer in Ihrer Organisation Ressourcen in virtuellen Netzwerken verwenden können.

### <a name="traffic-filtering"></a>Filtern von Datenverkehr

- Sie können den Netzwerkdatenverkehr zwischen Ressourcen in einem virtuellen Netzwerk mithilfe einer Netzwerksicherheitsgruppe filtern oder mithilfe eines virtuellen Netzwerkgeräts, das dazu in der Lage ist. Informationen zum Bereitstellen eines virtuellen Netzwerkgeräts (z.B. einer Firewall) zum Filtern von Netzwerkdatenverkehr finden Sie im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Bei Verwendung eines virtuellen Netzwerkgeräts erstellen Sie auch benutzerdefinierte Routen zur Weiterleitung des Datenverkehrs aus Subnetzen an das virtuelle Netzwerkgerät. Weitere Informationen zu [Routing von Datenverkehr](#traffic-routing).
- Eine Netzwerksicherheitsgruppe enthält mehrere Standardsicherheitsregeln, mit denen Datenverkehr zu und von Ressourcen zugelassen oder verweigert wird. Eine Netzwerksicherheitsgruppe kann einer Netzwerkschnittstelle oder dem Subnetz, in dem sich die Netzwerkschnittstelle befindet, zugeordnet werden. Zur vereinfachten Verwaltung von Sicherheitsregeln empfiehlt es sich, eine Netzwerksicherheitsgruppe nach Möglichkeit einzelnen Subnetzen und nicht einzelnen Netzwerkschnittstellen in einem Subnetz zuzuordnen.
- Wenn auf unterschiedliche virtuelle Computer innerhalb eines Subnetzes unterschiedliche Sicherheitsregeln angewandt werden sollen, können Sie die Netzwerkschnittstelle im virtuellen Computer Anwendungssicherheitsgruppen zuordnen. In einer Sicherheitsregel kann eine Anwendungssicherheitsgruppe als Quelle und Ziel angegeben werden. Diese Regel gilt dann nur für die Netzwerkschnittstellen, die Mitglieder der Anwendungssicherheitsgruppe sind. Weitere Informationen zu [Netzwerksicherheitsgruppen](security-overview.md) und [Anwendungssicherheitsgruppen](security-overview.md#application-security-groups).
- In Azure werden in jeder Netzwerksicherheitsgruppe mehrere Standardsicherheitsregeln erstellt. Eine Standardregel lässt die Weiterleitung des gesamten Datenverkehrs zwischen allen Ressourcen in einem virtuellen Netzwerk zu. Dieses Verhalten können Sie mithilfe von Netzwerksicherheitsgruppen oder benutzerdefiniertem Routing zum Weiterleiten von Datenverkehr an ein virtuelles Netzwerkgerät außer Kraft setzen. Es wird empfohlen, sich mit allen [Standardsicherheitsregeln](security-overview.md#default-security-rules) von Azure sowie mit der Anwendung von Netzwerksicherheitsgruppen-Regeln auf eine Ressource vertraut zu machen.

Sie können sich Beispielentwürfe für die Implementierung eines Umkreisnetzwerks (auch als DMZ bezeichnet) zwischen Azure und dem Internet mithilfe eines [virtuellen Netzwerkgeräts](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) ansehen.

### <a name="traffic-routing"></a>Routing von Datenverkehr

In Azure werden mehrere Standardrouten für ausgehenden Datenverkehr aus einem Subnetz erstellt. Sie können das Azure-Standardrouting außer Kraft setzen, indem Sie eine Routingtabelle erstellen und einem Subnetz zuordnen. Häufige Gründe für das Außerkraftsetzen des Azure-Standardroutings:
- Der Datenverkehr zwischen Subnetzen soll über ein virtuelles Netzwerkgerät weitergeleitet werden. Hier finden Sie weitere Informationen zum [Konfigurieren von Routingtabellen zum Weiterleiten von Datenverkehr über ein virtuelles Netzwerkgerät](tutorial-create-route-table-portal.md).
- Der gesamte Internetdatenverkehr soll über ein virtuelles Netzwerkgerät oder lokal über ein Azure VPN Gateway erfolgen. Die Erzwingung der lokalen Weiterleitung von Internetdatenverkehr zur Überprüfung und Protokollierung wird häufig als Tunnelerzwingung bezeichnet. Weitere Informationen zum Konfigurieren der [Tunnelerzwingung](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Wenn Sie das benutzerdefinierte Routing implementieren möchten, empfiehlt es sich, sich mit dem [Routing in Azure](virtual-networks-udr-overview.md) vertraut zu machen.

## <a name="connectivity"></a>Konnektivität

Sie können ein virtuelles Netzwerk mittels VNET-Peering mit anderen virtuellen Netzwerken oder über ein Azure VPN Gateway mit dem lokalen Netzwerk verbinden.

### <a name="peering"></a>Peering

Bei Verwendung des [Peerings virtueller Netzwerke](virtual-network-peering-overview.md) können sich die virtuellen Netzwerke in derselben oder in unterschiedlichen unterstützten Azure-Regionen befinden. Die virtuellen Netzwerke können sich im selben oder in unterschiedlichen Azure-Abonnements befinden (auch in Abonnements, die zu verschiedenen Azure Active Directory-Mandanten gehören). Es wird empfohlen, sich mit den [Peeringanforderungen und -einschränkungen](virtual-network-manage-peering.md#requirements-and-constraints) vertraut zu machen, bevor Sie ein Peering erstellen. Die Bandbreite zwischen Ressourcen in virtuellen Netzwerken, die mittels Peering in derselben Region miteinander verknüpft sind, ist dieselbe wie zwischen Ressourcen, die sich im selben virtuellen Netzwerk befinden.

### <a name="vpn-gateway"></a>VPN-Gateway

Mit einem Azure [VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) können Sie ein virtuelles Netzwerk über eine [Site-to-Site-VPN-Verbindung](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder über eine dedizierte Verbindung mit Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mit dem lokalen Netzwerk verbinden.

Sie können das Peering und ein VPN-Gateway kombinieren und [Hub-Spoke-Netzwerke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json) erstellen, bei denen beispielsweise virtuelle Spoke-Netzwerke mit einem virtuellen Hub-Netzwerk verbunden werden und das virtuelle Hub-Netzwerk mit einem lokalen Netzwerk verbunden wird.

### <a name="name-resolution"></a>Namensauflösung

Ressourcen in einem virtuellen Netzwerk können die Namen von Ressourcen in dem mittels Peering verknüpften virtuellen Netzwerk nicht mit dem in Azure [integrierten DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) auflösen. Zur Auflösung von Namen in einem mittels Peering verknüpften virtuellen Netzwerk müssen Sie [einen eigenen DNS-Server bereitstellen](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) oder [private Azure DNS-Domänen](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) verwenden. Für die Auflösung von Namen zwischen Ressourcen in einem virtuellen Netzwerk und lokalen Netzwerken müssen Sie außerdem einen eigenen DNS-Server bereitstellen.

## <a name="permissions"></a>Berechtigungen

In Azure wird die [rollenbasierte Zugriffssteuerung](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Azure Role-Based Access Control, Azure RBAC) für Ressourcen verwendet. Berechtigungen werden einem [Bereich](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) in der folgenden Hierarchie zugewiesen: Verwaltungsgruppe, Abonnement, Ressourcengruppe und einzelne Ressource. Weitere Informationen zu dieser Hierarchie finden Sie unter [Organisieren Ihrer Ressourcen](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Um virtuelle Azure-Netzwerke und alle zugehörigen Funktionen, z.B. Peering, Netzwerksicherheitsgruppen, Dienstendpunkte und Routingtabellen, verwenden zu können, können Sie Mitglieder Ihrer Organisation den integrierten Rollen [Besitzer](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Mitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor) oder [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) und dann die einzelnen Rollen dem entsprechenden Bereich zuweisen. Wenn Sie bestimmte Berechtigungen für eine Teilmenge der Funktionen des virtuellen Netzwerks zuweisen möchten, erstellen Sie eine [benutzerdefinierte Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und weisen der Rolle diese Berechtigungen für [virtuelle Netzwerke](manage-virtual-network.md#permissions), [ Subnetze und Dienstendpunkte](virtual-network-manage-subnet.md#permissions), [Netzwerkschnittstellen](virtual-network-network-interface.md#permissions), [Peering](virtual-network-manage-peering.md#permissions), [Netzwerksicherheitsgruppen und Anwendungssicherheitsgruppen](manage-network-security-group.md#permissions) oder [Routingtabellen](manage-route-table.md#permissions) zu.

## <a name="policy"></a>Richtlinie

Mit Azure Policy können Sie Richtliniendefinitionen erstellen, zuweisen und verwalten. Richtliniendefinitionen erzwingen unterschiedliche Regeln für Ihre Ressourcen, damit diese stets mit den Standards Ihrer Organisation und Vereinbarungen zum Servicelevel konform bleiben. Azure Policy führt eine Auswertung Ihrer Ressourcen durch, um zu prüfen, welche Ressourcen nicht den festgelegten Richtliniendefinitionen entsprechen. Sie können beispielsweise eine Richtlinie definieren und anwenden, welche die Erstellung von virtuellen Netzwerken nur in einer bestimmten Ressourcengruppe oder Region zulässt. In einer anderen Richtlinie können Sie festlegen, dass jedem Subnetz eine Netzwerksicherheitsgruppe zugeordnet werden muss. Die Richtlinien werden beim Erstellen und Aktualisieren von Ressourcen ausgewertet.

Richtlinien werden in der folgenden Hierarchie angewendet: Verwaltungsgruppe, Abonnement und Ressourcengruppe. Erfahren Sie mehr über [Azure Policy](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), oder stellen Sie einige [Azure Policy-Definitionen](policy-samples.md) für virtuelle Netzwerke bereit.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über alle Aufgaben, Einstellungen und Optionen für [ein virtuelles Netzwerk](manage-virtual-network.md), [Subnetz und Dienstendpunkt](virtual-network-manage-subnet.md), [Netzwerkschnittstelle](virtual-network-network-interface.md), [Peering](virtual-network-manage-peering.md), [Netzwerk- und Anwendungssicherheitsgruppe](manage-network-security-group.md) oder [Routingtabelle](manage-route-table.md).
