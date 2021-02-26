---
title: Azure-Dienste mit Unterstützung für Verfügbarkeitszonen
description: Zur Erstellung hoch verfügbarer und robuster Anwendungen in Azure bieten Verfügbarkeitszonen physisch voneinander getrennte Orte für die Ressourcenausführung.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 16bb173bdbe390da46f2dc105baa9bc73d25a3dc
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557019"
---
# <a name="azure-services-that-support-availability-zones"></a>Azure-Dienste mit Unterstützung für Verfügbarkeitszonen

Die globale Microsoft Azure-Infrastruktur wurde auf allen Ebenen so entworfen und konstruiert, dass für Kunden der höchste Grad an Redundanz und Resilienz erzielt wird. Die Azure-Infrastruktur setzt sich aus geografischen Bereichen, Regionen und Verfügbarkeitszonen zusammen, mit denen sichergestellt wird, dass die Auswirkungen eines Fehlers begrenzt bleiben und die Anwendungen und Daten der Kunden nicht stärker beeinträchtigt werden. Das Konstrukt mit Azure-Verfügbarkeitszonen wurde entwickelt, um eine Software- und Netzwerklösung als Schutz vor Rechenzentrumsfehlern bereitzustellen und für unsere Kunden eine bessere Hochverfügbarkeit (HA) sicherzustellen.

Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig voneinander funktionieren. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region bewirkt die Auswirkungen von Zonenausfällen auf Anwendungen und Daten, z. B. größere Überflutungen, Stürme bzw. Orkane und andere Ereignisse, die eine Störung in Bezug auf den Standortzugriff, die sichere Übertragung, den Betrieb von Versorgungseinrichtungen und die Verfügbarkeit von Ressourcen darstellen können. Verfügbarkeitszonen und die zugehörigen Rechenzentren sind so konzipiert, dass Dienste, Kapazität und Verfügbarkeit bei Kompromittierung einer Zone von den anderen Verfügbarkeitszonen der Region unterstützt werden.

Alle Azure-Verwaltungsdienste sind so konstruiert, dass Sie gegenüber regionsweiten Ausfällen resilient sind. Im Spektrum möglicher Ausfälle besitzen ein oder mehrere Verfügbarkeitszonenausfälle innerhalb einer Region einen geringeren Ausfallradius als der Ausfall einer ganzen Region. Azure kann von einem zonenweiten Ausfall von Verwaltungsdiensten innerhalb der Region wiederhergestellt werden. Azure führt wichtige Wartungsaufgaben immer zonenweise innerhalb einer Region durch, um Ausfälle zu verhindern, die sich auf Kundenressourcen auswirken, die über Verfügbarkeitszonen innerhalb einer Region hinweg bereitgestellt sind.


![Konzeptionelle Ansicht einer Azure-Region mit drei Zonen](./media/az-region/azure-region-availability-zones.png)


Azure-Dienste, die Verfügbarkeitszonen unterstützen, können in drei Kategorien unterteilt werden: **zonale**, **zonenredundante** und **nicht-regionale** Dienste. Kundenworkloads können so kategorisiert werden, dass jedes dieser Architekturszenarios verwendet werden kann, um die erforderliche Anwendungsleistung und -stabilität zu erzielen.

- **Zonale Dienste:** Eine Ressource kann in einer bestimmten selbst ausgewählten Verfügbarkeitszone bereitgestellt werden, um strengere Latenz- oder Leistungsvorgaben zu erfüllen.  Die Resilienz wird quasi von selbst erzielt, indem Anwendungen und Daten in einer oder mehreren Zonen der Region repliziert werden.  Ressourcen können an eine bestimmte Zone angeheftet werden. Beispielsweise können virtuelle Computer, verwaltete Datenträger oder IP-Standardadressen an eine bestimmte Zone angeheftet werden. So können Sie eine bessere Resilienz erzielen, da eine oder mehrere Ressourceninstanzen auf mehrere Zonen verteilt werden.

- **Zonenredundante Dienste:** Die Azure-Plattform repliziert Ressourcen und Daten zonenübergreifend.  Microsoft verwaltet die Bereitstellung der Hochverfügbarkeit, da Azure-Instanzen automatisch in der Region repliziert und verteilt werden.  Bei ZRS (zonenredundanter Speicher) werden die Daten beispielsweise in drei Zonen repliziert, damit der Ausfall einer Zone keine Auswirkungen auf die Hochverfügbarkeit der Daten hat. 

- **Nicht regionale Dienste**: Dienste sind immer in Azure-Geografien verfügbar und sowohl resilient gegen zonenweite als auch regionsweite Ausfälle. 


Um eine umfassende Geschäftskontinuität in Azure zu erreichen, erstellen Sie die Anwendungsarchitektur über die Kombination von Verfügbarkeitszonen mit Azure-Regionspaaren. Sie können Anwendungen und Daten mithilfe von Verfügbarkeitszonen für hohe Verfügbarkeit innerhalb einer Azure-Region synchron replizieren und für Notfallwiederherstellungen über Azure-Regionen asynchron replizieren. Weitere Informationen finden Sie unter [Entwickeln von Lösungen für Hochverfügbarkeit mit Verfügbarkeitszonen](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Azure-Dienste mit Unterstützung für Verfügbarkeitszonen

 - Die VMs älterer Generationen sind nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../virtual-machines/sizes-previous-gen.md).
 - Wie bereits in [Regionen und Verfügbarkeitszonen in Azure](az-overview.md) erwähnt, sind einige Dienste nicht regional. Bei diesen Diensten besteht keine Abhängigkeit von einer bestimmten Azure-Region. Deshalb sind sie sowohl resilient gegen zonenweite als auch regionsweite Ausfälle.  Eine Liste nicht-regionaler Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Azure-Regionen mit Verfügbarkeitszonen


| Amerika           | Europa               | Afrika              | Asien-Pazifik   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Kanada, Mitte     | Frankreich, Mitte       | Südafrika, Norden* | Japan, Osten     |
| USA (Mitte)         | Deutschland, Westen-Mitte |                     | Asien, Südosten |
| East US            | Nordeuropa         |                     | Australien (Osten) |
| USA (Ost) 2          | UK, Süden             |                     |                |
| USA, Süden-Mitte | Europa, Westen          |                     |                |
| US Government, Virginia    |                      |                     |                |
| USA, Westen 2        |                      |                     |                |


\* Weitere Informationen zur Unterstützung von Verfügbarkeitszonen und verfügbaren Diensten in diesen Regionen erhalten Sie von Ihrem Microsoft-Vertriebs- oder Kundenbeauftragten. Informationen zu den Regionen, die zukünftig Verfügbarkeitszonen unterstützen werden, finden Sie unter [Azure-Geografien](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Azure-Dienste mit Unterstützung für Verfügbarkeitszonen

- VMs älterer Generationen werden im Folgenden nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von VM-Größen](../virtual-machines/sizes-previous-gen.md).

- Einige Dienste sind nicht regional. Weitere Informationen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](az-overview.md). Bei diesen Diensten besteht keine Abhängigkeit von einer bestimmten Azure-Region. Deshalb sind sie resilienter gegen zonenweite oder regionsweite Ausfälle.  Eine Liste nicht-regionaler Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Zonenresiliente Dienste 

:globe_with_meridians: Nicht regionale Dienste: Dienste sind immer in Azure-Geografien verfügbar und sowohl resilient gegen zonenweite als auch regionsweite Ausfälle.

:large_blue_diamond:   Resilient gegenüber zonenweiten Ausfällen 

**Grundlegende Dienste**

|     Produkte                                                    | Resilienz             |
|-----------------------------------------------------------------|:----------------------------:|
|     Speicherkonto                                           | :large_blue_diamond:  |
|     Application Gateway (V2)                                  | :large_blue_diamond:  |
|     Azure Backup                                                | :large_blue_diamond:  |
|     Azure Cosmos DB                                           | :large_blue_diamond:  |
|     Azure Data Lake Storage Gen2                             | :large_blue_diamond:  |
|     Azure Express Route                                       | :large_blue_diamond:  |
|     Azure: öffentliche IP-Adresse                                           | :large_blue_diamond:  |
|     Azure SQL-Datenbank (Tarif „Universell“)                 | :large_blue_diamond:  |
|     Azure SQL-Datenbank (Tarife „Premium“ und „Unternehmenskritisch“)     | :large_blue_diamond:  |
|     Disk Storage                                                | :large_blue_diamond:  |
|     Event Hubs                                                  | :large_blue_diamond:  |
|     Key Vault                                                   | :large_blue_diamond:  |
|     Load Balancer                                               | :large_blue_diamond:  |
|     Service Bus                                                 | :large_blue_diamond:  |
|     Service Fabric                                            | :large_blue_diamond:  |
|     Speicher:   Heiße/Kalte Blobspeicherebenen                      | :large_blue_diamond:  |
|     Speicher:   Managed Disks                                    | :large_blue_diamond:  |
|     VM-Skalierungsgruppen                               | :large_blue_diamond:  |
|     Virtual Machines                                          | :large_blue_diamond:  |
|     Virtual Machines: Av2-Serie                              | :large_blue_diamond:  |
|     Virtual Machines: Bs-Serie                               | :large_blue_diamond:  |
|     Virtual Machines: DSv2-Serie                             | :large_blue_diamond:  |
|     Virtual Machines: DSv3-Serie                             | :large_blue_diamond:  |
|     Virtual Machines: Dv2-Serie                              | :large_blue_diamond:  |
|     Virtual Machines: Dv3-Serie                              | :large_blue_diamond:  |
|     Virtual Machines: ESv3-Serie                             | :large_blue_diamond:  |
|     Virtual Machines: Ev3-Serie                              | :large_blue_diamond:  |
|     Virtual Network                                           | :large_blue_diamond:  |
|     VPN Gateway                                                 | :large_blue_diamond:  |


**Hauptdienste**

| Produkte                                        | Resilienz |
|-------------------------------------------------|:------------:|
| App Service-Umgebungen                        |      :large_blue_diamond:  |
| Azure Active Directory Domain Services          |      :large_blue_diamond:  |
| Azure Bastion                                   |      :large_blue_diamond:  |
| Azure Cache for Redis                           |      :large_blue_diamond:  |
| Azure Cognitive Services: Textanalyse        |      :large_blue_diamond:  |
| Azure-Daten-Explorer                             |      :large_blue_diamond:  |
| Azure Database for MySQL: Flexibler Server      |      :large_blue_diamond:  |
| Azure Database for PostgreSQL: Flexibler Server |      :large_blue_diamond:  |
| Azure DDoS Protection                           |      :large_blue_diamond:  |
| Azure Firewall                                  |      :large_blue_diamond:  |
| Azure Firewall Manager                          |      :large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      :large_blue_diamond:  |
| Azure Private Link                              |      :large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      :large_blue_diamond:  |
| Azure Site Recovery                             |      :large_blue_diamond:  |
| Container Registry                              |      :large_blue_diamond:  |
| Event Grid                                      |      :large_blue_diamond:  |
| Network Watcher                                 |      :large_blue_diamond:  |
| Power BI Embedded                               |      :large_blue_diamond:  |
| Blob Storage Premium                            |      :large_blue_diamond:  |
| VMs: Ddsv4-Serie                  |      :large_blue_diamond:  |
| VMs: Ddv4-Serie                   |      :large_blue_diamond:  |
| VMs: Dsv4-Serie                   |      :large_blue_diamond:  |
| VMs: Dv4-Serie                    |      :large_blue_diamond:  |
| VMs: Edsv4-Serie                  |      :large_blue_diamond:  |
| VMs: Edv4-Serie                   |      :large_blue_diamond:  |
| VMs: Esv4-Serie                   |      :large_blue_diamond:  |
| VMs: Ev4-Serie                    |      :large_blue_diamond:  |
| VMs: Fsv2-Serie                   |      :large_blue_diamond:  |
| VMs: M-Serie                      |      :large_blue_diamond:  |
| Virtual WAN                                     |      :large_blue_diamond:  |


**Nicht regional**

|     Produkte                                  |     Resilienz    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     :globe_with_meridians:             |
|     Azure Active Directory                  |     :globe_with_meridians:             |
|     Azure Advisor                             |     :globe_with_meridians:             |
|     Azure Bot Services                        |     :globe_with_meridians:             |
|     Azure Defender für IoT                  |     :globe_with_meridians:             |
|     Azure Information Protection            |     :globe_with_meridians:             |
|     Azure Lighthouse                        |     :globe_with_meridians:             |
|     Azure Managed Applications              |     :globe_with_meridians:             |
|     Azure Maps                                |     :globe_with_meridians:             |
|     Azure Policy                              |     :globe_with_meridians:             |
|     Azure Resource Graph                    |     :globe_with_meridians:             |
|     Azure Stack                               |     :globe_with_meridians:             |
|     Azure Stack Edge                        |     :globe_with_meridians:             |
|     Cloud Shell                               |     :globe_with_meridians:             |
|     Kunden-Lockbox für Microsoft Azure    |     :globe_with_meridians:             |
|     Microsoft Azure Peering Service         |     :globe_with_meridians:             |
|     Microsoft Azure-Portal                  |     :globe_with_meridians:             |
|     Security Center                         |     :globe_with_meridians:             |
|     Traffic Manager                         |     :globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Preise für VMs in Verfügbarkeitszonen

Es fallen keine zusätzlichen Kosten für virtuelle Computer an, die in einer Verfügbarkeitszone bereitgestellt werden. Weitere Informationen finden Sie auf der Seite [Bandbreite – Preisdetails](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Erste Schritte mit Verfügbarkeitszonen

- [Erstellen eines virtuellen Computers](../virtual-machines/windows/create-portal-availability-zone.md)
- [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Erstellen einer zonenredundanten VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Zonenübergreifender Lastenausgleich für virtuelle Computer mithilfe eines Standardlastenausgleichs mit einem zonenredundanten Front-End](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Lastenausgleich für virtuelle Computer innerhalb einer Zone mithilfe eines Standardlastenausgleichs mit einem zonalen Front-End](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zonenredundanter Speicher](../storage/common/storage-redundancy.md)
- [Dienstebene „Universell“ für SQL-Datenbank](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Georedundante Notfallwiederherstellung in Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Georedundante Notfallwiederherstellung in Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Erstellen eines zonenredundanten Gateways für virtuelle Netzwerke](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Hinzufügen einer zonenredundanten Region für Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Erste Schritte mit Azure Cache für Redis-Verfügbarkeitszonen](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Erstellen einer Azure Active Directory Domain Services-Instanz](../active-directory-domain-services/tutorial-create-instance.md)
- [Erstellen eines Azure Kubernetes Service-Clusters (AKS), der Verfügbarkeitszonen verwendet](../aks/availability-zones.md)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Regionen und Verfügbarkeitszonen in Azure](az-overview.md)
