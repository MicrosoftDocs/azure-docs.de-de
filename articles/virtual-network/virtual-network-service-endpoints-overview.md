---
title: Azure-VNET-Dienstendpunkte
titlesuffix: Azure Virtual Network
description: Es wird beschrieben, wie Sie den direkten Zugriff auf Azure-Ressourcen aus einem virtuellen Netzwerk über Dienstendpunkte ermöglichen.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8926e99db926fc8182e98509c3deff0ccc3d1612
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576666"
---
# <a name="virtual-network-service-endpoints"></a>Virtual Network-Dienstendpunkte

Der Virtual Network (VNet)-Dienstendpunkt bietet eine sichere und direkte Verbindung mit Azure-Diensten über eine optimierte Route über das Azure-Backbonenetzwerk. Endpunkte ermöglichen es Ihnen, Ihre kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Dienstendpunkte ermöglichen es privaten IP-Adressen im VNet, den Endpunkt eines Azure-Diensts zu erreichen, ohne dass eine öffentliche IP-Adresse im VNet benötigt wird.

Dieses Feature ist für die folgenden Azure-Dienste und -Regionen verfügbar. Die *Microsoft.\** -Ressource ist in Klammern angegeben. Aktivieren Sie diese Ressource über die Subnetzseite, während Sie die Dienstendpunkte für Ihren Dienst konfigurieren:

**Allgemein verfügbar**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage*): Allgemein in allen Azure-Regionen verfügbar.
- **[Azure SQL-Datenbank](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allgemein in allen Azure-Regionen verfügbar.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allgemein verfügbar in allen Azure-Regionen für dedizierte SQL-Pools (früher SQL DW).
- **[Azure Database for PostgreSQL-Server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allgemein verfügbar in Azure-Regionen, in denen der Datenbankdienst verfügbar ist.
- **[Azure Database for MySQL-Server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Allgemein verfügbar in Azure-Regionen, in denen der Datenbankdienst verfügbar ist.
- **[Azure Database for MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft.Sql*): Allgemein verfügbar in Azure-Regionen, in denen der Datenbankdienst verfügbar ist.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB*): Allgemein in allen Azure-Regionen verfügbar.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault*): Allgemein in allen Azure-Regionen verfügbar.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus*): Allgemein in allen Azure-Regionen verfügbar.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub*): Allgemein in allen Azure-Regionen verfügbar.
- **[Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureActiveDirectory*): Allgemein verfügbar in allen Azure-Regionen, in denen ADLS Gen1 verfügbar ist.
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft.Web*): Allgemein verfügbar in allen Azure-Regionen, in denen App Service verfügbar ist.
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft.CognitiveServices*): Allgemein verfügbar in allen Azure-Regionen, in denen Cognitive Services verfügbar sind.

**Public Preview**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft.ContainerRegistry*): Vorschauversion in eingeschränkten Azure-Regionen verfügbar, in denen Azure Container Registry verfügbar ist.

Aktuelle Benachrichtigungen finden Sie auf der Seite [Azure Virtual Network-Updates](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Hauptvorteile

Dienstendpunkte bieten folgende Vorteile:

- **Verbesserte Sicherheit für Ihre Azure-Dienstressourcen**: Private Adressräume von virtuellen Netzwerken können sich überlappen. Sie können keine überlappenden Bereiche verwenden, um den Datenverkehr, der von Ihrem VNET stammt, eindeutig zu identifizieren. Dienstendpunkte ermöglichen es, Azure-Dienstressourcen für Ihr virtuelles Netzwerk zu schützen, indem die VNET-Identität auf den Dienst erweitert wird. Nachdem Sie Dienstendpunkte in Ihrem virtuellen Netzwerk aktiviert haben, können Sie eine virtuelle Netzwerkregel hinzufügen, um die Azure-Dienstressourcen in Ihrem virtuellen Netzwerk zu schützen. Die Regelergänzung bietet eine erhöhte Sicherheit, da der Ressourcenzugriff über das öffentliche Internet vollständig verhindert und nur Datenverkehr aus Ihrem virtuellen Netzwerk zugelassen wird.
- **Optimale Weiterleitung des Datenverkehrs für Azure-Dienste aus Ihrem virtuellen Netzwerk**: Heutzutage wird für alle Routen Ihres virtuellen Netzwerks, die für Internetdatenverkehr den Weg über Ihre lokalen bzw. virtuellen Geräte erzwingen, auch für den Datenverkehr von Azure-Diensten die gleiche Route wie für den Internetdatenverkehr erzwungen. Dienstendpunkte ermöglichen eine optimale Weiterleitung für Azure-Datenverkehr. 

  Endpunkte leiten den Datenverkehr der Dienste direkt aus Ihrem virtuellen Netzwerk an den Dienst im Microsoft Azure-Backbone-Netzwerk. Die Verwaltung von Datenverkehr im Azure-Backbonenetzwerk ermöglicht Ihnen weiterhin die Überwachung und Überprüfung von ausgehendem Internet-Datenverkehr aus Ihren virtuellen Netzwerken durch die Tunnelerzwingung, ohne dass sich dies auf den Datenverkehr der Dienste auswirkt. Weitere Informationen zu benutzerdefinierten Routen und Tunnelerzwingung finden Sie unter [Routing von Datenverkehr für virtuelle Azure-Netzwerke](virtual-networks-udr-overview.md).
- **Einfache Einrichtung mit weniger Verwaltungsaufwand**: Sie benötigen in Ihren virtuellen Netzwerken keine reservierten öffentlichen IP-Adressen mehr, um Azure-Ressourcen über die IP-Firewall zu schützen. Es sind keine NAT- (Netzwerkadressübersetzung) oder Gatewaygeräte erforderlich, um die Dienstendpunkte einzurichten. Sie können Dienstendpunkte einfach per Klick in einem Subnetz konfigurieren. Es entsteht kein zusätzlicher Aufwand für die Verwaltung der Endpunkte.

## <a name="limitations"></a>Einschränkungen

- Das Feature ist nur für virtuelle Netzwerke verfügbar, für die das Azure Resource Manager-Bereitstellungsmodell verwendet wird.
- Endpunkte sind für Subnetze aktiviert, die in virtuellen Azure-Netzwerken konfiguriert sind. Endpunkte können nicht für Datenverkehr verwendet werden, der aus Ihrer lokalen Umgebung an Azure-Dienste fließt. Weitere Informationen finden Sie unter [Schützen des Zugriffs auf Azure-Dienste aus der lokalen Umgebung](#secure-azure-services-to-virtual-networks).
- Bei Azure SQL gilt ein Dienstendpunkt nur für Datenverkehr von Azure-Diensten in der Region eines virtuellen Netzwerks. Für Azure Storage werden Endpunkte auch auf Regionspaare ausgedehnt, in denen Sie das virtuelle Netzwerk bereitstellen, um den Datenverkehr für georedundanten Speicher mit Lesezugriff (RA-GRS) und georedundanten Speicher (GRS) zu unterstützen. Weitere Informationen finden Sie unter [Azure-Regionspaare](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Für Azure Data Lake Storage (ADLS) Gen1 ist die Funktion für die VNET-Integration nur für virtuelle Netzwerke in der gleichen Region verfügbar. Beachten Sie auch, dass die Integration virtueller Netzwerke für ADLS Gen1 die Sicherheit von VNET-Dienstendpunkten zwischen Ihrem virtuellen Netzwerk und Azure Active Directory (Azure AD) nutzt, um zusätzliche Sicherheitsansprüche im Zugriffstoken zu generieren. Diese Ansprüche werden dann genutzt, um Ihr virtuelles Netzwerk mit Ihrem Data Lake Storage Gen1-Konto zu authentifizieren und den Zugriff zu ermöglichen. Das *Microsoft.AzureActiveDirectory*-Tag, das in den dienstunterstützenden Dienstendpunkten aufgeführt ist, wird nur für die Unterstützung von Dienstendpunkten für ADLS Gen1 verwendet. Azure AD unterstützt nativ keine Dienstendpunkte. Weitere Informationen zur VNET-Integration von Azure Data Lake Store Gen 1 finden Sie unter [Netzwerksicherheit in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Schützen von Azure-Diensten in virtuellen Netzwerken

- Der VNET-Dienstendpunkt stellt die Identität Ihres virtuellen Netzwerks für den Azure-Dienst bereit. Nachdem Sie Dienstendpunkte in Ihrem virtuellen Netzwerk aktiviert haben, können Sie eine virtuelle Netzwerkregel hinzufügen, um die Azure-Dienstressourcen in Ihrem virtuellen Netzwerk zu schützen.
- Heutzutage werden für Datenverkehr von Azure-Diensten aus einem virtuellen Netzwerk öffentliche IP-Adressen als Quell-IP-Adressen verwendet. Bei Verwendung von Dienstendpunkten wird für den Dienstdatenverkehr zu privaten virtuellen Netzwerk-Adressen als Quell-IP-Adressen gewechselt, wenn aus einem virtuellen Netzwerk auf den Azure-Dienst zugegriffen wird. Dieser Wechsel ermöglicht Ihnen den Zugriff auf die Dienste, ohne dass reservierte öffentliche IP-Adressen in IP-Firewalls verwendet werden müssen.

   >[!NOTE]
   > Wenn Sie Dienstendpunkte verwenden, werden die Quell-IP-Adressen der virtuellen Computer im Subnetz für Dienstdatenverkehr von öffentlichen IPv4-Adressen auf private IPv4-Adressen umgestellt. Bereits vorhandene Firewallregeln für Azure-Dienste, bei denen öffentliche Azure-IP-Adressen verwendet werden, funktionieren dadurch nicht mehr. Stellen Sie sicher, dass die Firewallregeln für Azure-Dienste diese Umstellung unterstützen, bevor Sie Dienstendpunkte einrichten. Unter Umständen kommt es während der Konfiguration von Dienstendpunkten auch zu einer vorübergehenden Unterbrechung des Dienstdatenverkehrs aus diesem Subnetz. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Schützen des Zugriffs auf Azure-Dienste aus der lokalen Umgebung

  Standardmäßig sind Azure-Dienstressourcen, die auf virtuelle Netzwerke beschränkt und so geschützt sind, über lokale Netzwerke nicht erreichbar. Wenn Sie Datenverkehr aus der lokalen Umgebung zulassen möchten, müssen Sie auch öffentliche IP-Adressen (meist NAT) aus der lokalen Umgebung bzw. per ExpressRoute zulassen. Sie können diese IP-Adressen über die Konfiguration der IP-Firewall für Azure-Dienstressourcen hinzufügen.

  ExpressRoute: Wenn Sie [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) für öffentliches Peering oder für lokales Microsoft-Peering verwenden, müssen Sie die verwendeten NAT-IP-Adressen identifizieren. Beim öffentlichen Peering werden für jede ExpressRoute-Verbindung standardmäßig zwei NAT-IP-Adressen verwendet. Diese werden auf den Datenverkehr der Azure-Dienste angewendet, wenn der Datenverkehr im Microsoft Azure-Netzwerk-Backbone eintrifft. Beim Microsoft-Peering werden die NAT-IP-Adressen entweder vom Kunden oder vom Dienstanbieter bereitgestellt.  Um den Zugriff auf Ihre Dienstressourcen zuzulassen, müssen Sie diese öffentlichen IP-Adressen in der Ressourceneinstellung der IP-Firewall zulassen. [Öffnen Sie über das Azure-Portal ein Supportticket für ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview), um die IP-Adressen Ihrer ExpressRoute-Verbindung für öffentliches Peering zu ermitteln. Weitere Informationen zur NAT für öffentliches ExpressRoute-Peering und Microsoft-Peering finden Sie unter [NAT-Anforderungen für ExpressRoute](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Schützen von Azure-Diensten in virtuellen Netzwerken](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfiguration

- Konfigurieren Sie Dienstendpunkte für ein Subnetz in einem virtuellen Netzwerk. Für Endpunkte können alle Arten von Computeinstanzen verwendet werden, die in diesem Subnetz ausgeführt werden.
- Sie können mehrere Dienstendpunkte für alle unterstützten Azure-Dienste (z. B. Azure Storage oder Azure SQL-Datenbank) eines Subnetzes konfigurieren.
- Bei Azure SQL-Datenbank müssen sich virtuelle Netzwerke in derselben Region wie die Ressource des Azure-Diensts befinden. Für Azure Storage muss sich das primäre Konto bei Verwendung von GRS- und RA-GRS-Konten in derselben Region wie das virtuelle Netzwerk befinden. Für alle anderen Dienste können die Ressourcen des Azure-Diensts in virtuellen Netzwerken in jeder Region gesichert werden. 
- Das virtuelle Netzwerk, in dem der Endpunkt konfiguriert ist, kann sich unter demselben oder einem anderen Abonnement wie die Ressource des Azure-Diensts befinden. Weitere Informationen zu den erforderlichen Berechtigungen zum Einrichten von Endpunkten und Schützen von Azure-Diensten finden Sie unter [Bereitstellung](#provisioning).
- Für unterstützte Dienste können Sie neue oder vorhandene Ressourcen in virtuellen Netzwerken schützen, indem Sie Dienstendpunkte verwenden.

### <a name="considerations"></a>Überlegungen

- Nach der Aktivierung eines Dienstendpunkts verwenden die IP-Quelladressen bei der Kommunikation mit dem Dienst aus diesem Subnetz keine öffentlichen IPv4-Adressen mehr, sondern ihre privaten IPv4-Adressen. Alle vorhandenen geöffneten TCP-Verbindungen mit dem Dienst werden während dieses Wechselvorgangs geschlossen. Achten Sie darauf, dass keine kritischen Aufgaben ausgeführt werden, wenn Sie einen Dienstendpunkt eines Diensts für ein Subnetz aktivieren oder deaktivieren. Stellen Sie außerdem sicher, dass Ihre Anwendungen nach der Umstellung dieser IP-Adresse automatisch eine Verbindung mit Azure-Diensten herstellen können.

  Die Umstellung der IP-Adresse wirkt sich nur auf Dienstdatenverkehr aus Ihrem virtuellen Netzwerk aus. Es ergeben sich keine Auswirkungen auf anderen Datenverkehr an bzw. von den öffentlichen IPv4-Adressen, die Ihren virtuellen Computern zugewiesen sind. Wenn Sie über vorhandene Firewallregeln mit öffentlichen Azure-IP-Adressen verfügen, funktionieren diese Regeln für Azure-Dienste nicht mehr, nachdem die Umstellung auf private virtuelle Netzwerk-Adressen durchgeführt wurde.
- Bei Verwendung von Dienstendpunkten bleiben DNS-Einträge für Azure-Dienste unverändert und werden weiterhin in öffentliche IP-Adressen aufgelöst, die dem Azure-Dienst zugewiesen werden.

- Netzwerksicherheitsgruppen (NSGs) mit Dienstendpunkten:
  - NSGs gestatten standardmäßig den ausgehenden Internetdatenverkehr und auch den Datenverkehr aus Ihrem VNET zu Azure-Diensten. In Verbindung mit Dienstendpunkten funktioniert dieser Datenverkehr wie bisher. 
  - Falls Sie den gesamten ausgehenden Internetdatenverkehr verweigern und nur Datenverkehr für bestimmte Azure-Dienste zulassen möchten, können Sie hierfür [Diensttags](./network-security-groups-overview.md#service-tags) in Ihren NSGs verwenden. Sie können unterstützte Azure-Dienste als Ziel in Ihren NSG-Regeln angeben, und die Wartung der zugrunde liegenden IP-Adressen der einzelnen Tags wird von Azure bereitgestellt. Weitere Informationen finden Sie unter [Azure-Diensttags für NSGs](./network-security-groups-overview.md#service-tags). 

### <a name="scenarios"></a>Szenarien

- **Mittels Peering verknüpfte, verbundene oder mehrere virtuelle Netzwerke**: Zum Schützen von Azure-Diensten in mehreren Subnetzen innerhalb eines virtuellen Netzwerks oder mehrerer virtueller Netzwerke können Sie Dienstendpunkte unabhängig voneinander in den einzelnen Subnetzen aktivieren und Ressourcen von Azure-Diensten in allen diesen Subnetzen schützen.
- **Filtern von ausgehendem Datenverkehr, der aus dem virtuellen Netzwerk an Azure-Dienste fließt**: Wenn Sie den Datenverkehr, der aus dem virtuellen Netzwerk an einen Azure-Dienst gesendet wird, untersuchen und filtern möchten, können Sie in diesem virtuellen Netzwerk ein virtuelles Netzwerkgerät bereitstellen. Anschließend können Sie Dienstendpunkte auf das Subnetz anwenden, in dem das virtuelle Netzwerkgerät bereitgestellt wurde, und Ressourcen des Azure-Diensts auf dieses Subnetz beschränken und so schützen. Dieses Szenario kann hilfreich sein, wenn Sie die Filterung von virtuellen Netzwerkgeräten verwenden möchten, um den Zugriff auf Azure-Dienste aus Ihrem virtuellen Netzwerk nur auf bestimmte Azure-Ressourcen zu beschränken. Weitere Informationen finden Sie unter [egress with network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha) (Ausgehender Datenverkehr mit virtuellen Netzwerkgeräten).
- **Schützen von Azure-Ressourcen für Dienste, die direkt in virtuellen Netzwerken bereitgestellt werden**: Sie können verschiedene Azure-Dienste direkt in bestimmten Subnetzen in einem virtuellen Netzwerk bereitstellen. Sie können Ressourcen von Azure-Diensten für Subnetze mit [verwalteten Diensten](virtual-network-for-azure-services.md) schützen, indem Sie im Subnetz des verwalteten Diensts einen Dienstendpunkt einrichten.
- **Datenträger-Datenverkehr von einem virtuellen Azure-Computer**: Datenträger-Datenverkehr eines virtuellen Computers für verwaltete und nicht verwaltete Datenträger wird durch Dienstendpunkte, die Änderungen für Azure Storage weiterleiten, nicht beeinflusst. Dieser Datenverkehr umfasst „diskIO“ sowie „mount“ und „unmount“. Der REST-Zugriff auf Seitenblobs kann über Dienstendpunkte und [Azure Storage-Netzwerkregeln](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) auf bestimmte Netzwerke beschränkt werden. 

### <a name="logging-and-troubleshooting"></a>Protokollierung und Problembehandlung

Nachdem Dienstendpunkte für einen bestimmten Dienst konfiguriert wurden, können Sie wie folgt überprüfen, ob die Dienstendpunktroute eingerichtet wurde: 
 
- Überprüfen der Quell-IP-Adresse einer Dienstanforderung in der Dienstdiagnose. Alle neuen Anforderungen mit Dienstendpunkten zeigen die Quell-IP-Adresse für die Anforderung als private IP-Adresse eines virtuellen Netzwerks an, die dem Client zugewiesen ist, über den die Anforderung aus Ihrem virtuellen Netzwerk gesendet wird. Ohne den Endpunkt handelt es sich bei der Adresse um eine öffentliche Azure-IP-Adresse.
- Anzeigen der effektiven Routen auf einer Netzwerkschnittstelle in einem Subnetz. Die Route zum Dienst:
  - Zeigt eine spezifischere Standardroute zu den Adresspräfixbereichen der einzelnen Dienste an
  - Weist *VirtualNetworkServiceEndpoint* als „nextHopType“ auf
  - Gibt an, dass – gegenüber Routen mit Tunnelerzwingung – eine direktere Verbindung mit dem Dienst vorhanden ist.

>[!NOTE]
> Die Dienstendpunktroute setzt alle BGP- oder UDR-Routen für die Übereinstimmung mit dem Adresspräfix eines Azure-Diensts außer Kraft. Weitere Informationen finden Sie unter [Behandeln von Problemen bei effektiven Routen](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Bereitstellung

Dienstendpunkte können in virtuellen Netzwerken einzeln von einem Benutzer konfiguriert werden, der über Schreibzugriff auf ein virtuelles Netzwerk verfügt. Um Azure-Dienstressourcen in einem VNET zu schützen, muss der Benutzer für die hinzuzufügenden Subnetze über die Berechtigung *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* verfügen. Die integrierten Dienstadministratorrollen enthalten diese Berechtigung standardmäßig. Sie können die Berechtigung ändern, indem Sie benutzerdefinierte Rollen erstellen.

Weitere Informationen zu integrierten Rollen finden Sie unter [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Weitere Informationen zum Zuweisen spezifischer Berechtigungen zu benutzerdefinierten Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuelle Netzwerke und Ressourcen von Azure-Diensten können sich in demselben oder in unterschiedlichen Abonnements befinden. Bestimmte Azure-Dienste (nicht alle) wie Azure Storage und Azure Key Vault unterstützen auch Dienstendpunkte für verschiedene Active Directory-Mandanten (AD), d. h., das virtuelle Netzwerk und die Azure-Dienstressource können sich auf verschiedenen Active Directory-Mandanten (AD) befinden. Ausführliche Informationen finden Sie in den einzelnen Dienstdokumentationen.  

## <a name="pricing-and-limits"></a>Preise und Einschränkungen

Für die Nutzung von Dienstendpunkten fallen keine zusätzlichen Gebühren an. Das aktuelle Preismodell für Azure-Dienste (Azure Storage, Azure SQL-Datenbank usw.) gilt unverändert.

Für die Gesamtzahl von Dienstendpunkten in einem virtuellen Netzwerk gilt keine Beschränkung.

Bestimmte Azure-Dienste, wie etwa Azure-Speicherkonten, erzwingen möglicherweise Beschränkungen der Anzahl der zum Schützen der Ressource verwendeten Subnetze. Ausführliche Informationen finden Sie in der Dokumentation zu den verschiedenen Diensten im Abschnitt [Nächste Schritte](#next-steps).

## <a name="vnet-service-endpoint-policies"></a>Richtlinien zu VNET-Dienstendpunkten 

Mithilfe von VNET-Dienstendpunkten können Sie den Datenverkehr virtueller Netzwerke zu Azure-Diensten filtern. Dieser Filter gestattet nur bestimmte Azure-Dienstressourcen über Dienstendpunkte. Dienstendpunkt-Richtlinien bieten eine differenzierte Zugriffssteuerung für virtuellen Netzwerkdatenverkehr zu Azure-Diensten. Weitere Informationen finden Sie unter [Richtlinien für VNET-Dienstendpunkte](./virtual-network-service-endpoint-policies-overview.md).

## <a name="faqs"></a>Häufig gestellte Fragen

Häufig gestellte Fragen finden Sie unter [Azure Virtual Network-Endpunkte – häufig gestellte Fragen](./virtual-networks-faq.md#virtual-network-service-endpoints).

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von VNET-Dienstendpunkten](tutorial-restrict-network-access-to-resources.md)
- [Sichern eines Azure Storage-Kontos in einem virtuellen Netzwerk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Sichern einer Azure SQL-Datenbank in einem virtuellen Netzwerk](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Schützen von Azure Synapse Analytics in einem virtuellen Netzwerk](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure-Dienstintegration in virtuellen Netzwerken](virtual-network-for-azure-services.md)
- [Richtlinien für VNET-Dienstendpunkte](./virtual-network-service-endpoint-policies-overview.md)
- [Azure Resource Manager-Vorlage](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
