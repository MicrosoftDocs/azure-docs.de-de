---
title: Verbessern der Leistung von Azure-Anwendungen mit Azure Advisor | Microsoft-Dokumentation
description: Nutzen Sie den Advisor, um die Leistung Ihrer Azure-Bereitstellungen zu optimieren.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.author: kasparks
ms.openlocfilehash: 8fdae1e12e56dcbcb56941726b0c089ad59b8fc8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254647"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Verbessern der Leistung von Azure-Anwendungen mit Azure Advisor

Dank der Empfehlungen vom Azure Advisor zur Leistung können Sie die Geschwindigkeit und Reaktionsfähigkeit Ihrer unternehmenskritischen Anwendungen verbessern. Empfehlungen des Advisor zur Leistung erhalten Sie auf dem Advisor-Dashboard auf der Registerkarte **Leistung**.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduzieren der DNS-Gültigkeitsdauer des Traffic Manager-Profils für ein schnelleres Failover auf fehlerfreie Endpunkte

In den [Gültigkeitsdauereinstellungen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) Ihres Traffic Manager-Profils können Sie angeben, wie schnell Endpunkte gewechselt werden, wenn ein bestimmter Endpunkt nicht mehr auf Abfragen reagiert. Wenn Sie die Werte der Gültigkeitsdauer reduzieren, können Clients schneller an funktionierende Endpunkte weitergeleitet werden.

Azure Advisor ermittelt Traffic Manager-Profile mit einer längeren Gültigkeitsdauer und empfiehlt, die Gültigkeitsdauer auf 20 oder 60 Sekunden zu konfigurieren, je nachdem, ob das Profil für [Schnelles Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/) konfiguriert ist.

## <a name="improve-database-performance-with-sql-db-advisor"></a>Verbessern der Datenbankleistung mit SQL DB Advisor

Advisor bietet Ihnen eine einheitliche, konsolidierte Ansicht der Empfehlungen für alle Ihre Azure-Ressourcen. Azure Advisor und Azure SQL Database Advisor sind integriert, um Ihnen Empfehlungen zum Verbessern der Leistung Ihrer SQL Azure-Datenbank zu bieten. SQL Database Advisor bewertet die Leistung durch eine Analyse des Nutzungsverlaufs Ihrer SQL Azure-Datenbank. Anschließend werden Empfehlungen gegeben, die für die typische Workload der Datenbank am besten geeignet sind.

> [!NOTE]
> Um Empfehlungen zu erhalten, muss eine Datenbank ungefähr eine Woche lang genutzt werden, und innerhalb dieser Woche müssen durchweg Aktivitäten erfolgen. SQL Database Advisor kann leichter für einheitliche Abfragemuster als für zufällige Aktivitätsspitzen optimiert werden.

Weitere Informationen zu SQL Database Advisor finden Sie unter [SQL Database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/).

## <a name="improve-app-service-performance-and-reliability"></a>Verbessern der Leistung und Zuverlässigkeit von App Services

Azure Advisor befolgt empfohlene bewährte Methoden zum Verbessern der App Services-Leistung und Erkennen relevanter Plattformfunktionen. Beispielempfehlungen für App Services sind u.a.:
* Erkennen von Instanzen, bei denen Arbeitsspeicher- oder CPU-Ressourcen durch App-Laufzeiten ausgeschöpft sind, samt Abhilfeoptionen
* Erkennen von Instanzen, bei denen durch Zusammenstellen von Ressourcen wie Web-Apps und Datenbanken die Leistung verbessert und Kosten gesenkt werden können

Weitere Informationen zu App Services-Empfehlungen finden Sie unter [Bewährte Methoden für Azure App Service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Verwenden Sie zum Verhindern einer Datenträger-E/A-Drosselung Managed Disks.

Advisor identifiziert virtuelle Computer, die zu einem Speicherkonto gehören, das das Skalierbarkeitsziel erreicht. Durch diese Bedingung werden diese virtuellen Computer anfällig für eine E/A-Drosselung. Advisor empfiehlt, dass diese virtuellen Computer Managed Disks verwenden, um Leistungseinbußen zu verhindern.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Verbessern der Leistung und Zuverlässigkeit von Datenträgern virtueller Computer mit Storage Premium

Advisor ermittelt virtuelle Computer mit Standarddatenträgern, die eine große Anzahl von Transaktionen auf Ihrem Speicherkonto ausgeführt haben, und empfiehlt ein Upgrade auf Premium-Datenträger. 

Azure Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für virtuelle Computer mit E/A-intensiven Workloads. Datenträger von virtuellen Computern, die Storage Premium-Konten nutzen, speichern Daten auf Solid State Drives (SSD). Für eine optimale Leistung Ihrer Anwendung empfehlen wir, alle Datenträger von virtuellen Computern, die eine hohe IOPS-Leistung erfordern, zu Storage Premium zu migrieren.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Entfernen von Datenschiefe in Ihrer SQL Data Warehouse-Tabelle zum Erhöhen der Abfrageleistung

Datenschiefe kann unnötige Datenverschiebungen oder Ressourcenengpässe beim Ausführen Ihrer Workload verursachen. Advisor erkennt eine Datenschiefe mit einer Verteilung von über 15 % und empfiehlt, Ihre Daten neu zu verteilen und die Hauptauswahl für Ihre Tabellenverteilung erneut zu überprüfen. Weitere Informationen zum Identifizieren und Entfernen von Datenschiefe finden Sie unter [Problembehandlung bei Datenschiefe](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Erstellen von Datenstatistiken oder Aktualisieren von veralteten Datenstatistiken für Ihre SQL Data Warehouse-Tabelle zum Erhöhen der Abfrageleistung

Advisor identifiziert die Tabellen, die keine aktuellen [Tabellenstatistiken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) aufweisen, und empfiehlt das Erstellen oder Aktualisieren der Tabellenstatistiken. Der SQL Data Warehouse-Abfrageoptimierer verwendet aktuelle Statistiken zur Schätzung der Kardinalität oder Zeilenanzahl im Abfrageergebnis. Dadurch kann der Abfrageoptimierer einen hochwertigen Abfrageplan für die schnellste Leistung erstellen.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Zentrales Hochskalieren zum Optimieren der Cachenutzung durch Ihre SQL Data Warehouse-Tabellen, um die Abfrageleistung zu verbessern

Azure Advisor erkennt, ob Ihre SQL Data Warehouse-Instanz eine hohe prozentuale Cachenutzung und einen niedrigen Trefferprozentsatz aufweist. Diese Bedingung gibt an, dass die hohe Cachenutzung aufgehoben wird, was sich auf die Leistung Ihrer SQL Data Warehouse-Instanz auswirken kann. Advisor schlägt vor, dass Sie Ihre SQL Data Warehouse-Instanz zentral hochskalieren, um sicherzustellen, dass Sie ausreichend Cachekapazität für Ihre Workload zuweisen.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>Konvertieren von SQL Data Warehouse-Tabellen in replizierte Tabellen, um die Abfrageleistung zu erhöhen

Advisor identifiziert die Tabellen, die keine replizierten Tabellen sind, würde jedoch von der Konvertierung profitieren und schlägt Ihnen vor, diese Tabellen zu konvertieren. Die Empfehlungen basieren auf der Größe der replizierten Tabelle, der Anzahl der Spalten, dem Verteilungstyp der Tabelle und der Anzahl der Partitionen der SQL Data Warehouse-Tabelle. Zusätzliche Heuristiken können in der Empfehlung für den Kontext bereitgestellt werden. Weitere Informationen dazu, was diese Empfehlung bestimmt, finden Sie unter [SQL Data Warehouse-Empfehlungen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migrieren Ihres Speicherkontos zu Azure Resource Manager, um die neuesten Azure-Features zu erhalten

Migrieren Sie Ihr Speicherkonto-Bereitstellungsmodell zu Azure Resource Manager (Resource Manager), um von Vorlagenbereitstellungen, zusätzlichen Sicherheitsoptionen und der Möglichkeit zum Upgrade auf ein Konto vom Typ „Allgemein v2“ zur Nutzung der neuesten Features von Azure Storage zu profitieren. Advisor identifiziert eigenständige Speicherkonten, die das klassische Bereitstellungsmodell verwenden, und empfiehlt eine Migration zum Resource Manager-Bereitstellungsmodell.

> [!NOTE]
> Es ist geplant, klassische Warnungen in Azure Monitor im Juni 2019 einzustellen. Es wird empfohlen, ein Upgrade Ihres klassischen Speicherkontos durchzuführen, damit Resource Manager verwendet werden kann, um die Warnungsfunktion auf der neuen Plattform beizubehalten. Weitere Informationen finden Sie unter [Einstellung klassischer Warnungen](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Entwerfen Ihrer Speicherkonten, damit die maximale Abonnementgrenze nicht erreicht wird

In einer Azure-Region können maximal 250 Speicherkonten pro Abonnement unterstützt werden. Wenn das Limit erreicht ist, können Sie keine weiteren Speicherkonten in dieser Region-Abonnement-Kombination mehr erstellen. Advisor überprüft Ihre Abonnements und zeigt Empfehlungen an, damit Sie weniger Speicherkonten für Abonnements entwerfen, bei denen das maximale Limit bald erreicht ist.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimieren der Leistung der Azure MySQL-, Azure PostgreSQL- und Azure MariaDB-Server 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>Korrigieren der CPU-Auslastung der Azure MySQL-, Azure PostgreSQL- und Azure MariaDB-Server mit CPU-Engpässen
Eine sehr hohe CPU-Auslastung über einen längeren Zeitraum kann die Abfrageleistung für Ihre Workload beeinträchtigen. Durch Erhöhen der CPU-Größe können die Runtime der Datenbankabfragen optimiert und die Gesamtleistung verbessert werden. Azure Advisor identifiziert Server mit einer hohen CPU-Auslastung, auf denen wahrscheinlich Workloads mit eingeschränkter CPU-Leistung ausgeführt werden, und empfiehlt die Skalierung der Computegröße.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduzieren der Speicherbeschränkungen auf den Azure MySQL-, Azure PostgreSQL- und Azure MariaDB-Servern oder Verschieben in arbeitsspeicheroptimierte SKU
Eine niedrige Cachetrefferquote kann die Abfrageleistung beeinträchtigen und zu erhöhtem IOPS führen. Dies kann auf einen ungültigen Abfrageplan oder die Ausführung einer speicherintensiven Workload zurückzuführen sein. Durch Korrigieren des Abfrageplans oder  [Vergrößern des Speichers](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) des Azure Database for PostgreSQL-Datenbankservers, Azure MySQL-Datenbankservers oder Azure MariaDB-Servers kann die Ausführung der Datenbankworkload optimiert werden. Azure Advisor identifiziert Server, die durch diese hohe Änderungsrate des Pufferpools betroffen sind, und empfiehlt entweder die Korrektur des Abfrageplans, den Wechsel zu einer höheren SKU mit mehr Speicher oder die Vergrößerung des Speichers, um mehr IOPS zu erhalten.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Verwenden eines MySQL- oder Azure PostgreSQL-Lesereplikats zum horizontalen Skalieren von Lesevorgängen für leseintensive Workloads
Azure Advisor nutzt workloadbasierte Heuristik, z. B. das Verhältnis von Lese-zu Schreibvorgängen auf dem Server in den letzten sieben Tagen, um leseintensive Workloads zu identifizieren. Eine Azure Database for PostgreSQL-Ressource oder Azure Database for MySQL-Ressource mit einem sehr hohen Verhältnis von Lese- zu Schreibvorgängen kann zu CPU- und/oder Speicherkonflikten führen, durch die die Abfrageleistung beeinträchtigt wird. Durch Hinzufügen eines  [Replikats](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) können Schreibvorgänge horizontal auf den Replikatserver skaliert werden, sodass CPU- und oder Speicherkonflikte auf dem primären Server verhindert werden. Advisor identifiziert Server mit diesen leseintensiven Workloads und empfiehlt, dass ein [Lesereplikat](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) hinzugefügt wird, um einige der Leseworkloads auszulagern.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skalieren des Azure MySQL-, Azure PostgreSQL- oder Azure MariaDB-Servers auf eine höhere SKU zur Verhinderung von Verbindungseinschränkungen
Jede neue Verbindung mit dem Datenbankserver belegt Speicher. Die Leistung des Datenbankservers verschlechtert sich, wenn aufgrund einer [Obergrenze](https://docs.microsoft.com/azure/postgresql/concepts-limits) des Speichers Fehler bei Verbindungen mit dem Server auftreten. Azure Advisor identifiziert Server, bei denen viele Verbindungsfehler auftreten, und empfiehlt die Aktualisierung der Verbindungslimits des Servers durch zentrales Hochskalieren der Computegröße oder Verwendung von arbeitsspeicheroptimierten SKUs, die über eine höhere Computegröße pro Kern verfügen, um auf dem Server mehr Speicher bereitzustellen.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skalieren Ihres Caches auf eine andere Größe oder SKU, um die Cache- und Anwendungsleistung zu verbessern

Cache-Instanzen funktionieren am besten, wenn sie nicht unter hoher Arbeitsspeicherauslastung, hoher Serverlast oder hoher Netzwerkbandbreite ausgeführt werden, da diese Faktoren dazu führen können, dass die Instanzen nicht mehr reagieren oder nicht mehr verfügbar sind. Auch Datenverluste sind möglich. Advisor erkennt entsprechende Cache-Instanzen und empfiehlt entweder die Anwendung bewährter Methoden, um die Arbeitsspeicherauslastung, Serverlast oder Netzwerkbandbreite zu verringern, oder die Skalierung auf eine andere Größe oder SKU mit mehr Kapazität.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Hinzufügen von Regionen mit Datenverkehr zu Ihrem Azure Cosmos DB-Konto

Advisor erkennt Azure Cosmos DB-Konten mit Datenverkehr aus einer derzeit nicht konfigurierten Region und empfiehlt, diese Region hinzuzufügen. Dies dient zur Verbesserung der Wartezeit für Anforderungen aus der betreffenden Region und gewährleistet die Verfügbarkeit im Falle von Regionsausfällen. Weitere Informationen zur globalen Datenverteilung mit Azure Cosmos DB finden Sie [hier](https://aka.ms/cosmos/globaldistribution).

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Konfigurieren Ihrer Azure Cosmos DB-Indizierungsrichtlinie mit benutzerdefinierten eingeschlossenen oder ausgeschlossenen Pfaden

Azure Advisor erkennt Cosmos DB-Container, die die standardmäßige Indizierungsrichtlinie verwenden, aber von einer benutzerdefinierten Indizierungsrichtlinie profitieren könnten (basierend auf dem Workloadmuster). Die standardmäßige Indizierungsrichtlinie indiziert sämtliche Eigenschaften. Durch die Verwendung einer benutzerdefinierten Indizierungsrichtlinie mit expliziten ein- bzw. ausgeschlossenen Pfaden für Abfragefilter lassen sich die Anforderungseinheiten und der Speicherbedarf für die Indizierung verringern. Weitere Informationen zum Ändern von Indizierungsrichtlinien finden Sie [hier](https://aka.ms/cosmosdb/modify-index-policy).

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Festlegen der Azure Cosmos DB-Abfrageseitengröße (MaxItemCount) auf „-1“ 

Azure Advisor erkennt Azure Cosmos DB-Container, die die Abfrageseitengröße „100“ verwenden, und empfiehlt die Verwendung der Seitengröße „-1“, um Scanvorgänge zu beschleunigen. Weitere Informationen zur maximalen Elementanzahl finden Sie [hier](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count).

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Zugreifen auf Advisor-Empfehlungen zur Leistung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicken Sie auf dem Advisor-Dashboard auf die Registerkarte **Leistung**.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:

* [Einführung in Advisor](advisor-overview.md)
* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Advisor-Empfehlungen zu Kosten](advisor-performance-recommendations.md)
* [Advisor-Empfehlungen für Hochverfügbarkeit](advisor-high-availability-recommendations.md)
* [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md)
