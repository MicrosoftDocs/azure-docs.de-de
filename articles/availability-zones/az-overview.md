---
title: Regionen und Verfügbarkeitszonen in Azure
description: In diesem Artikel erhalten Sie Informationen zu Regionen und Verfügbarkeitszonen in Azure, um technische und gesetzliche Anforderungen erfüllen zu können.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 32cd6c23e1fe465a801e56fad5dd3461df860503
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182732"
---
# <a name="regions-and-availability-zones-in-azure"></a>Regionen und Verfügbarkeitszonen in Azure

Microsoft Azure-Dienste sind global verfügbar, um Ihre Cloudvorgänge optimal zu unterstützen. Sie können basierend auf den technischen und gesetzlichen Aspekten die jeweils beste Region für Ihre Anforderungen auswählen: Dienstfunktionen, Data Residency, Konformitätsanforderungen und Wartezeit.

## <a name="terminology"></a>Begriff

Für ein besseres Verständnis von Regionen und Verfügbarkeitszonen in Azure ist ein Verständnis der zentralen Begriffe und Konzepte erforderlich.

| Begriff oder Konzept | BESCHREIBUNG |
| --- | --- |
| region | Mehrere Rechenzentren werden innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und sind über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden. |
| geography | Hierbei handelt es sich um einen Bereich auf der Welt, in dem mindestens eine Azure-Region enthalten ist. Geografien definieren einen separaten Markt, der Data Residency- und Konformitätsgrenzen bewahrt. In Geografien können Kunden mit spezifischen Anforderungen an Datenresidenz und Compliance ihre Daten und Anwendungen eng zusammenhalten. Geografien sind fehlertolerant und erhalten dank ihrer Verbindung mit unserer dedizierten Netzwerkinfrastruktur mit sehr hohen Kapazitäten die Verfügbarkeit auch beim Ausfall einer ganzen Region. |
| Verfügbarkeitszone | Hierbei handelt es sich um physische Orte innerhalb einer Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. |
| Empfohlene Region | Hierbei handelt es sich um eine Region, in der die größte Bandbreite an Dienstfunktionen zur Verfügung gestellt werden kann. Sie ist so entworfen, dass sie Verfügbarkeitszonen jetzt oder in der Zukunft unterstützen kann. Diese Regionen werden im Azure-Portal als **Empfohlen** markiert. |
| Alternative (andere) Region | Hierbei handelt es sich um eine Region, die den Speicherbedarf von Azure innerhalb einer Data Residency-Grenze ausweitet, in der auch eine empfohlene Region vorhanden ist. Alternative Regionen unterstützen die Optimierung von Wartezeit und stellen eine zweite Region für den Fall einer Notfallwiederherstellung bereit. Sie sind nicht so entworfen, dass sie Verfügbarkeitszonen unterstützen, obwohl Azure regelmäßig Bewertungen dieser Regionen vornimmt, um festzustellen, ob sie als empfohlene Regionen deklariert werden können. Diese Regionen werden im Azure-Portal als **Andere** markiert. |
| Grundlegender Dienst | Hierbei handelt es sich um einen Azure-Kerndienst, der in allen Regionen verfügbar ist, wenn die Region allgemein verfügbar ist. |
| Hauptdienst | Hierbei handelt es sich um einen Azure-Dienst, der in allem empfohlenen Regionen innerhalb von 12 Monaten nach allgemeiner Verfügbarkeit einer Region/eines Diensts verfügbar gemacht wird. In alternativen Regionen ist die Verfügbarkeit von der Nachfrage abhängig. |
| Spezialisierter Dienst | Hierbei handelt es sich um einen Dienst, dessen Verfügbarkeit von der Nachfrage in Regionen abhängt und der von angepasster/spezieller Hardware unterstützt wird. |
| Regionaler Dienst | Hierbei handelt es sich um einen Dienst, der regional bereitgestellt wird und es Kunden ermöglicht, die Region anzugeben, in der der Dienst bereitgestellt werden soll. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| Nicht-regionaler Dienst | Hierbei handelt es sich um einen Azure-Dienst, der keine Abhängigkeit von einer bestimmten Azure-Region aufweist. Nicht-regionale Dienste werden in zwei oder mehr Regionen bereitgestellt, und bei einem regionalen Ausfall wird der Dienst für die Kunden über eine Instanz in einer anderen Region weiterhin bereitgestellt. Eine vollständige Liste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Regions

Eine Region ist eine Reihe von Rechenzentren, die innerhalb eines durch Wartezeit definierten Umkreises bereitgestellt und über ein dediziertes regionales Netzwerk mit geringer Wartezeit verbunden sind. Dank Azure haben Sie die Flexibilität, Anwendungen dort bereitzustellen, wo es für Sie erforderlich ist, auch in mehreren Regionen, um so eine regionsübergreifende Resilienz bieten zu können. Weitere Informationen finden Sie in der [Übersicht über die Säule „Resilienz“](/azure/architecture/framework/resiliency/overview).

## <a name="availability-zones"></a>Verfügbarkeitszonen

Verfügbarkeitszonen sind ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Verfügbarkeitszonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Ausfällen von Rechenzentren. Zonenredundante Dienste replizieren Ihre Anwendungen und Daten zum Schutz vor einzelnen Fehlerquellen über Verfügbarkeitszonen hinweg. Mit Verfügbarkeitszonen bietet Azure die branchenweit beste Betriebszeit-SLA von 99,99 % für VMs. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

Eine Verfügbarkeitszone in einer Azure-Region ist eine Kombination aus einer Fehlerdomäne und einer Updatedomäne. Wenn Sie z.B. drei oder mehr virtuelle Computer über drei Zonen verteilt in einer Azure-Region erstellen, werden Ihre virtuellen Computer effektiv auf drei Fehlerdomänen und drei Updatedomänen verteilt. Die Azure-Plattform erkennt diese Updatedomänen übergreifende Verteilung, um sicherzustellen, dass die Aktualisierung von virtuellen Computern in unterschiedlichen Zonen nicht für den gleichen Zeitraum geplant wird.

Integrieren Sie hohe Verfügbarkeit in Ihre Anwendungsarchitektur, indem Sie Ihre Compute-, Speicher-, Netzwerk- und Datenressourcen in eine Zone aufnehmen und in anderen Zonen replizieren. Azure-Dienste, die Verfügbarkeitszonen unterstützen, können in zwei Kategorien unterteilt werden:

- **Zonendienste:** Sie binden die Ressource in eine bestimmte Zone ein (z. B. VMs, verwaltete Datenträger, Standard-IP-Adressen).
- **Zonenredundante Dienste:** Die Azure-Plattform repliziert automatisch zonenübergreifend (z. B. zonenredundanter Speicher, SQL-Datenbank).

Um eine umfassende Geschäftskontinuität in Azure zu erreichen, erstellen Sie die Anwendungsarchitektur über die Kombination von Verfügbarkeitszonen mit Azure-Regionspaaren. Sie können Anwendungen und Daten mithilfe von Verfügbarkeitszonen für hohe Verfügbarkeit innerhalb einer Azure-Region synchron replizieren und für Notfallwiederherstellungen über Azure-Regionen asynchron replizieren.
 
![Konzeptionelle Ansicht einer Zone, die in einer Region ausfällt](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Die Verfügbarkeitszonenbezeichner (die Zahlen 1, 2 und 3 in der vorherigen Abbildung) werden den tatsächlichen physischen Zonen der einzelnen Abonnements unabhängig logisch zugeordnet. Dies bedeutet, dass die Verfügbarkeitszone 1 in einem bestimmten Abonnement möglicherweise auf eine andere physische Zone als die Verfügbarkeitszone 1 in einem anderen Abonnement verweist. Folglich empfiehlt es sich, in Verfügbarkeitszonen-IDs nicht über verschiedene Abonnements hinweg bei der Platzierung virtueller Computer ein Relay durchzuführen.

## <a name="region-and-service-categories"></a>Regions- und Dienstkategorien

Der Ansatz an die Verfügbarkeit von Azure für Azure-Dienste in Regionen kann am besten dadurch beschrieben werden, dass Dienste in empfohlenen und alternativen Regionen verfügbar gemacht werden.

- **Empfohlene Region:** Hierbei handelt es sich um eine Region, in der die größte Bandbreite an Dienstfunktionen zur Verfügung gestellt werden kann. Sie ist so entworfen, dass sie Verfügbarkeitszonen jetzt oder in der Zukunft unterstützen kann. Diese Regionen werden im Azure-Portal als **Empfohlen** markiert.
- **Alternative (andere) Region:** Hierbei handelt es sich um eine Region, die den Speicherbedarf von Azure innerhalb einer Data Residency-Grenze ausweitet, in der auch eine empfohlene Region vorhanden ist. Alternative Regionen unterstützen die Optimierung von Wartezeit und stellen eine zweite Region für den Fall einer Notfallwiederherstellung bereit. Sie sind nicht so entworfen, dass sie Verfügbarkeitszonen unterstützen, obwohl Azure regelmäßig Bewertungen dieser Regionen vornimmt, um festzustellen, ob sie als empfohlene Regionen deklariert werden können. Diese Regionen werden im Azure-Portal als **Andere** markiert.

### <a name="comparing-region-types"></a>Vergleich von Regionstypen

Azure-Dienste werden in drei Kategorien gruppiert: grundlegende, Haupt- und spezialisierte Dienste. Die allgemeine Richtlinie von Azure für die Bereitstellung von Diensten in eine beliebige Region wird hauptsächlich vom Typ der Region, der Kategorie eines Diensts und der Nachfrage durch Kunden gesteuert:

- **Grundlegend:** Dies bedeutet, der Dienst ist in allem empfohlenen und alternativen Regionen verfügbar, wenn die Region allgemein verfügbar ist, oder spätestens innerhalb von 12 Monaten nach dem Zeitpunkt, an dem ein neuer grundlegender Dienst allgemein verfügbar gemacht wird.
- **Hauptdienst:** Diese Dienste sind in allen empfohlenen Regionen innerhalb von 12 Monaten nach der allgemeinen Verfügbarkeit einer Region/eines Dienst verfügbar. In alternativen Regionen ist die Verfügbarkeit von der Nachfrage abhängig. Viele Hauptdienste wurden bereits in einer großen Teilmenge alternativer Regionen bereitgestellt.
- **Spezialisiert:** Hier handelt es sich um gezielte Dienstangebote, die oft auf eine bestimmte Branche abzielen oder die von einer angepassten oder spezialisierten Hardware unterstützt werden. Die Verfügbarkeit hier wird von der Nachfrage in den einzelnen Regionen gesteuert. Viele dieser Dienste wurden bereits in einer großen Teilmenge empfohlener Regionen bereitgestellt.

Wenn Sie erfahren möchten, welche Dienste in einer bestimmten Region bereitgestellt wurden und Informationen zu zukünftigen Vorschauversionen oder der allgemeinen Verfügbarkeit von Diensten in einer Region erhalten möchten, sehen Sie sich die Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all) an.

Wenn ein Dienstangebot in einer bestimmten Region nicht verfügbar ist, können Sie Ihr Interesse an einem Dienst mitteilen, indem Sie sich an einen Vertriebsmitarbeiter von Microsoft wenden.

| Regionstyp | Nicht regional | Grundlegend | Hauptdienst | Spezialisiert | Verfügbarkeitszonen | Datenresidenz |
| --- | --- | --- | --- | --- | --- | --- |
| Empfohlen | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Nachfragegesteuert | :heavy_check_mark: | :heavy_check_mark: |
| Alternativ | :heavy_check_mark: | :heavy_check_mark: | Nachfragegesteuert | Nachfragegesteuert | – | :heavy_check_mark: |

### <a name="services-by-category"></a>Dienste nach Kategorie

Wie bereits erwähnt wurde, klassifiziert Azure Dienste in drei Kategorien: grundlegende, Haupt- und spezialisierte Dienste. Dienstkategorien werden zusammen mit der allgemeinen Verfügbarkeit zugewiesen. Oft beginnen Dienste ihren Lebenszyklus als spezialisierter Dienst. Wenn die Nachfrage und die Nutzung dann zunehmen, können sie zum Haupt- oder grundlegenden Dienst erklärt werden. In der folgenden Tabelle sind die Kategorien von Diensten als grundlegender, Haupt- oder spezialisierter Dienst aufgeführt. Beachten Sie die folgenden Hinweise zur Tabelle:

- Einige Dienste sind nicht-regional. Weitere Informationen und eine Liste nicht-regionaler Dienste finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).
- VMs älterer Generationen sind nicht aufgeführt. Weitere Informationen finden Sie unter [Vorherige Generationen von Größen virtueller Computer](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Grundlegend | Hauptdienst | Spezialisiert |
> | --- | --- | --- |
> | Azure Storage | API Management | Azure-API für FHIR |
> | Application Gateway | App Configuration | Azure Analysis Services |
> | Azure Backup | App Service | Azure Blockchain-Dienst |
> | Azure Cosmos DB | Automation | Azure Blueprints |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure Database for MariaDB |
> | Azure ExpressRoute | Azure Bastion | Dediziertes HSM von Azure |
> | Azure SQL-Datenbank | Azure Cache for Redis | Azure Dev Spaces |
> | Cloud Services | Azure Cognitive Search | Azure Digital Twins |
> | Cloud Services: Av2-Serie | Azure-Daten-Explorer | Azure Lab Services |
> | Cloud Services: Dv2-Serie | Azure Data Share | Azure NetApp Files |
> | Cloud Services: Dv3-Serie | Azure Database for MySQL | Azure Quantum |
> | Cloud Services: Ev3-Serie | Azure Database for PostgreSQL | Azure Spring Cloud-Dienst |
> | Cloud Services: IPs auf Instanzebene | Azure Database Migration Service | Azure Time Series Insights |
> | Cloud Services: Reservierte IP | Azure Databricks | Azure VMware Solution by CloudSimple |
> | Disk Storage | Azure DDoS Protection | Cloud Services: G-Serie |
> | Event Hubs | Azure DevTest Labs | Cloud Services: H-Serie |
> | Key Vault | Azure Firewall | Cognitive Services: Custom Vision |
> | Load Balancer | Azure Firewall Manager | Cognitive Services: Sprechererkennung |
> | Service Bus | Azure-Funktionen | Data Box Heavy |
> | Service Fabric | Azure HPC Cache | Data Catalog |
> | Virtual Machine Scale Sets | Azure IoT Hub | Data Factory: Data Factory (V1) |
> | Virtual Machines | Azure Kubernetes Service (AKS) | Data Lake Analytics |
> | VMs: Av2-Serie | Azure Machine Learning | Azure Machine Learning Studio (klassisch)|
> | VMs: Bs-Serie | Azure Private Link | Microsoft Genomics |
> | VMs: DSv2-Serie | Azure Red Hat OpenShift | Remote Rendering |
> | VMs: DSv3-Serie | Azure SignalR-Dienst | Spatial Anchors |
> | VMs: Dv2-Serie | Azure Site Recovery | StorSimple |
> | VMs: Dv3-Serie | Azure Stack Hub | Video Indexer |
> | VMs: ESv3-Serie | Azure Stream Analytics | VMs: DASv4-Serie |
> | VMs: Ev3-Serie | Azure Synapse Analytics | VMs: DAv4-Serie |
> | VMs: F-Serie | Batch | VMs: DCsv2-Serie |
> | VMs: FS-Serie | Cloud Services: M-Serie | VMs: EASv4-Serie |
> | VMs: IPs auf Instanzebene | Cognitive Services | VMs: EAv4-Serie |
> | VMs: Reservierte IP | Cognitive Services: Maschinelles Sehen | VMs: G-Serie |
> | Virtual Network | Cognitive Services: Content Moderator | VMs: GS-Serie |
> | VPN Gateway | Cognitive Services: Gesicht | VMs: HBv1-Serie |
> |  | Cognitive Services: Formularerkennung | VMs: HBv2-Serie |
> |  | Cognitive Services: Language Understanding | VMs: HCv1-Serie |
> |  | Cognitive Services: QnA Maker | VMs: H-Serie |
> |  | Cognitive Services: Spracherkennungsdienste | VMs: LS-Serie |
> |  | Container Instances | VMs: LSv2-Serie |
> |  | Containerregistrierung | VMs: Mv2-Serie |
> |  | Data Factory | VMs: NC-Serie |
> |  | Event Grid | VMs: NCv2-Serie |
> |  | HDInsight | VMs: NCv3-Serie |
> |  | Logic Apps | VMs: NDs-Serie |
> |  | Media Services | VMs: NDv2-Serie |
> |  | Network Watcher | VMs: NV-Serie |
> |  | Notification Hubs | VMs: NVv3-Serie |
> |  | Power BI Embedded | VMs: NVv4-Serie |
> |  | Blob Storage Premium | VMs: SAP HANA in Azure (große Instanzen) |
> |  | File Storage (Premium) | Visual Studio App Center |
> |  | Speicher: Archivspeicher |  |
> |  | Disk Storage Ultra |  |
> |  | VMs: Ddsv4-Serie |  |
> |  | VMs: Ddv4-Serie |  |
> |  | VMs: Dsv4-Serie |  |
> |  | VMs: Dv4-Serie |  |
> |  | VMs: Edsv4-Serie |  |
> |  | VMs: Edv4-Serie |  |
> |  | VMs: Esv4-Serie |  |
> |  | VMs: Ev4-Serie |  |
> |  | VMs: Fsv2-Serie |  |
> |  | VMs: M-Serie |  |
> |  | Virtuelles WAN |  |

###  <a name="services-resiliency"></a>Resilienz von Diensten

Alle Azure-Verwaltungsdienste sind so konstruiert, dass Sie gegenüber regionsweiten Ausfällen resilient sind. Im Spektrum möglicher Ausfälle besitzen ein oder mehrere Verfügbarkeitszonenausfälle innerhalb einer Region einen geringeren Ausfallradius als der Ausfall einer ganzen Region. Azure kann von einem zonenweiten Ausfall von Verwaltungsdiensten innerhalb der Region oder aus einer anderen Azure-Region wiederhergestellt werden. Azure führt wichtige Wartungsaufgaben immer zonenweise innerhalb einer Region durch, um Ausfälle zu verhindern, die sich auf Kundenressourcen auswirken, die über Verfügbarkeitszonen innerhalb einer Region hinweg bereitgestellt sind.

### <a name="pricing-for-vms-in-availability-zones"></a>Preise für VMs in Verfügbarkeitszonen

Es fallen keine zusätzlichen Kosten für virtuelle Computer an, die in einer Verfügbarkeitszone bereitgestellt werden. Die Betriebszeit-SLA von 99,99% für VMs wird angeboten, wenn mindestens zwei virtuelle Computer in zwei oder mehr Verfügbarkeitszonen innerhalb einer Azure-Region bereitgestellt werden. Es fallen zusätzliche Gebühren für Datenübertragungen zwischen VMs in unterschiedlichen Verfügbarkeitszonen an. Weitere Informationen finden Sie auf der Seite [Bandbreite – Preisdetails](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="get-started-with-availability-zones"></a>Erste Schritte mit Verfügbarkeitszonen

- [Erstellen eines virtuellen Computers](../virtual-machines/windows/create-portal-availability-zone.md)
- [Anfügen eines Datenträgers an einen virtuellen Windows-Computer mithilfe von PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Erstellen einer zonenredundanten VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Zonenübergreifender Lastenausgleich für virtuelle Computer mithilfe eines Standardlastenausgleichs mit einem zonenredundanten Front-End](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Lastenausgleich für virtuelle Computer innerhalb einer Zone mithilfe eines Standardlastenausgleichs mit einem zonalen Front-End](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Zonenredundanter Speicher](../storage/common/storage-redundancy.md)
- [Dienstebene „Universell“ für SQL-Datenbank](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Georedundante Notfallwiederherstellung in Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Georedundante Notfallwiederherstellung in Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Erstellen eines zonenredundanten Gateways für virtuelle Netzwerke](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Hinzufügen einer zonenredundanten Region für Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Erste Schritte mit Azure Cache für Redis-Verfügbarkeitszonen](https://aka.ms/redis/az/getstarted)
- [Erstellen einer Azure Active Directory Domain Services-Instanz](../active-directory-domain-services/tutorial-create-instance.md)
- [Erstellen eines Azure Kubernetes Service-Clusters (AKS), der Verfügbarkeitszonen verwendet](../aks/availability-zones.md)

## <a name="next-steps"></a>Nächste Schritte

- [Regionen, die Verfügbarkeitszonen in Azure unterstützen](az-region.md)
- [Schnellstartvorlagen](https://aka.ms/azqs)