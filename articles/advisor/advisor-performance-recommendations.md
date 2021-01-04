---
title: Verbessern der Leistung von Azure-Anwendungen mit Advisor
description: Anhand der Empfehlungen zur Leistung in Azure Advisor können Sie die Geschwindigkeit und Reaktionszeit Ihrer unternehmenskritischen Anwendungen verbessern.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 55f5ac6784bf613170fd10060799ab5ad1290a62
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183344"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Verbessern der Leistung von Azure-Anwendungen mit Azure Advisor

Anhand der Empfehlungen zur Leistung in Azure Advisor können Sie die Geschwindigkeit und Reaktionszeit Ihrer unternehmenskritischen Anwendungen verbessern. Empfehlungen des Advisor zur Leistung erhalten Sie auf dem Advisor-Dashboard auf der Registerkarte **Leistung**.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Reduzieren der DNS-Gültigkeitsdauer des Traffic Manager-Profils für ein schnelleres Failover auf fehlerfreie Endpunkte

Verwenden Sie die [Gültigkeitsdauereinstellungen](../traffic-manager/traffic-manager-performance-considerations.md) Ihres Traffic Manager-Profils, um anzugeben, wie schnell Endpunkte gewechselt werden, wenn ein bestimmter Endpunkt nicht mehr auf Abfragen reagiert. Wenn Sie die Werte der Gültigkeitsdauer verringern, werden Clients schneller an funktionierende Endpunkte weitergeleitet.

Azure Advisor ermittelt Traffic Manager-Profile, für die eine längere Gültigkeitsdauer konfiguriert ist. Es wird empfohlen, eine Gültigkeitsdauer von 20 oder 60 Sekunden zu konfigurieren, je nachdem, ob das Profil für [Schnelles Failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/) konfiguriert ist.

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Verbessern der Datenbankleistung mithilfe von SQL Database Advisor (vorübergehend deaktiviert)

In Azure Advisor erhalten Sie eine einheitliche, konsolidierte Übersicht über die Empfehlungen für alle Ihre Azure-Ressourcen. Integriert ist SQL Database Advisor, um Empfehlungen zum Verbessern der Leistung Ihrer Datenbank zu geben.  SQL Database Advisor bewertet die Leistung Ihrer Datenbank durch eine Analyse des Nutzungsverlaufs. Anschließend werden Empfehlungen gegeben, die für die typische Workload der Datenbank am besten geeignet sind.

> [!NOTE]
> Bevor Sie Empfehlungen erhalten können, muss die Datenbank ca. eine Woche lang verwendet werden, und innerhalb dieser Woche muss konsistent Aktivität bestehen. SQL Database Advisor kann leichter für einheitliche Abfragemuster als für zufällige Aktivitätsspitzen optimiert werden.

Weitere Informationen finden Sie unter [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Verbessern der Zuverlässigkeit und der Leistung durch Upgraden Ihrer Speicherclientbibliothek auf die neueste Version

In der neuesten Version des Speicherclientbibliothek-SDK werden Probleme behoben, die von Kunden gemeldet oder proaktiv im Rahmen unseres Qualitätssicherungsprozesses erkannt werden. Darüber hinaus bietet die neueste Version neben Optimierungen hinsichtlich der Zuverlässigkeit und der Leistung auch neue Features, die Azure Storage noch benutzerfreundlicher machen. Advisor bietet Empfehlungen und Anweisungen zum Upgrade auf die neueste Version des SDK, wenn Sie eine veraltete Version verwenden. Die Empfehlungen beziehen sich auf die unterstützten Sprachen: C++ und .NET.

## <a name="improve-app-service-performance-and-reliability"></a>Verbessern der Leistung und Zuverlässigkeit von App Services

Azure Advisor folgt Empfehlungen zum Verbessern der App Service-Leistung und zum Erkennen relevanter Plattformfunktionen. Beispielempfehlungen für App Service sind u. a.:
* Erkennen von Instanzen, bei denen Arbeitsspeicher- oder CPU-Ressourcen durch App-Laufzeiten ausgeschöpft sind, mit Möglichkeiten zur Abhilfe
* Erkennen von Instanzen, bei denen durch Zusammenstellen von Ressourcen wie Web-Apps und Datenbanken die Leistung verbessert und Kosten gesenkt werden können

Weitere Informationen finden Sie unter [Bewährte Methoden für Azure App Service](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Verwenden von verwalteten Datenträgern zum Verhindern einer Datenträger-E/A-Drosselung

Advisor identifiziert virtuelle Computer, die zu einem Speicherkonto gehören, das das Skalierbarkeitsziel erreicht. Durch diese Bedingung werden diese virtuellen Computer anfällig für eine E/A-Drosselung. Advisor empfiehlt, dass diese virtuellen Computer verwaltete Datenträger verwenden, um Leistungseinbußen zu verhindern.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Verbessern der Leistung und Zuverlässigkeit von Datenträgern virtueller Computer mit Storage Premium

Advisor ermittelt virtuelle Computer mit Standarddatenträgern, die eine große Anzahl von Transaktionen auf Ihrem Speicherkonto ausgeführt haben. Ein Upgrade auf Premium-Datenträger wird empfohlen. 

Azure Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für virtuelle Computer mit E/A-intensiven Workloads. Datenträger von virtuellen Computern, die Storage Premium-Konten nutzen, speichern Daten auf SSD-Datenträgern (Solid State Drive). Um eine optimale Leistung Ihrer Anwendung zu erzielen, empfehlen wir, alle Datenträger von virtuellen Computern, die eine hohe IOPS-Leistung erfordern, zu Storage Premium zu migrieren.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Beseitigen der Datenschiefe in Ihren Azure Synapse Analytics-Tabellen zum Verbessern der Abfrageleistung

Datenschiefe kann unnötige Datenverschiebungen oder Ressourcenengpässe beim Ausführen Ihrer Workload verursachen. Advisor ermittelt eine Datenschiefe bei der Verteilung von mehr als 15 %. Es wird empfohlen, die Daten neu zu verteilen und die ausgewählten Schlüssel für die Tabellenverteilung zu überprüfen. Weitere Informationen zum Identifizieren und Entfernen von Datenschiefe finden Sie unter [Problembehandlung bei Datenschiefe](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Erstellen oder Aktualisieren von Tabellenstatistiken für Ihre Azure Synapse Analytics-Tabellen zum Verbessern der Abfrageleistung

Advisor identifiziert die Tabellen, die keine aktuellen [Tabellenstatistiken](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) aufweisen, und empfiehlt das Erstellen oder Aktualisieren der Statistiken. Der Abfrageoptimierer in Azure Synapse Analytics verwendet aktuelle Statistiken, um die Kardinalität oder die Anzahl von Zeilen in Abfrageergebnissen zu schätzen. Diese Schätzungen ermöglichen es dem Abfrageoptimierer, einen Abfrageplan zu erstellen, um die optimale Leistung zu erzielen.

## <a name="improve-mysql-connection-management"></a>Verbessern der MySQL-Verbindungsverwaltung

Die Advisor-Analyse kann ergeben, dass Ihre Anwendung, die eine Verbindung mit einem MySQL-Server herstellt, Verbindungen möglicherweise nicht effizient verwaltet. Dieser Zustand kann zu unnötigem Ressourcenverbrauch und zu einer insgesamt höheren Anwendungslatenz führen. Zur Verbesserung der Verbindungsverwaltung empfehlen wir, die Anzahl kurzlebiger Verbindungen zu verringern und unnötige Leerlaufverbindungen zu beseitigen. Sie können diese Verbesserungen durch Konfigurieren einer serverseitigen Verbindungspoolfunktion wie ProxySQL vornehmen.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Hochskalieren zum Optimieren der Cachenutzung in Ihrem Azure Synapse Analytics-Tabellen zum Verbessern der Abfrageleistung

Azure Advisor erkennt, ob Ihre Azure Synapse Analytics-Tabellen eine hohe prozentuale Cachenutzung und eine niedrige prozentuale Trefferquote aufweisen. Dieser Zustand deutet darauf hin, dass viele Objekte aus dem Cache entfernt werden, was sich auf die Leistung Ihrer Azure Synapse Analytics-Instanz auswirken kann. Advisor empfiehlt, die Azure Synapse Analytics-Instanz hochzuskalieren, um sicherzustellen, dass ausreichend Cachekapazität für Ihre Workload zugeordnet ist.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Konvertieren von Azure Synapse Analytics-Tabellen in replizierte Tabellen zum Verbessern der Abfrageleistung

Advisor identifiziert Tabellen, die keine replizierten Tabellen sind, die von einer Konvertierung jedoch profitieren würden. Es empfiehlt sich, diese Tabellen zu konvertieren. Die Empfehlungen berücksichtigen Folgendes:
- Die Größe der replizierten Tabelle. 
- Die Anzahl der Spalten. 
- Den Tabellenverteilungstyp. 
- Die Anzahl von Partitionen in der Azure Synapse Analytics-Tabelle. 

Möglicherweise werden zusätzliche Heuristiken in der Empfehlung für den Kontext bereitgestellt. Weitere Informationen zu den Faktoren, die zu dieser Empfehlung führen, finden Sie unter [Azure Synapse Analytics-Empfehlungen](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Migrieren Ihres Speicherkontos zu Azure Resource Manager, um von den neuesten Azure-Features zu profitieren

Migrieren Sie Ihr Speicherkonto-Bereitstellungsmodell zu Azure Resource Manager, um von folgenden Features zu profitieren:
- Vorlagenbereitstellungen
- Zusätzlichen Sicherheitsoptionen
- Der Möglichkeit eines Upgrades auf ein GPv2-Konto, sodass Sie die neuesten Azure Storage-Features verwenden können 

Advisor identifiziert eigenständige Speicherkonten, die das klassische Bereitstellungsmodell verwenden, und empfiehlt eine Migration zum Resource Manager-Bereitstellungsmodell.

> [!NOTE]
> Klassische Warnungen in Azure Monitor wurden im August 2019 eingestellt. Es wird empfohlen, ein Upgrade Ihres klassischen Speicherkontos durchzuführen, damit Resource Manager verwendet werden kann, um die Warnungsfunktion auf der neuen Plattform beizubehalten. Weitere Informationen finden Sie unter [Einstellung klassischer Warnungen](../azure-monitor/platform/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Speicherkontenentwurf zum Vermeiden der maximalen Abonnementgrenze

In einer Azure-Region werden maximal 250 Speicherkonten pro Abonnement unterstützt. Wenn dieses Limit erreicht wird, können Sie keine weiteren Speicherkonten mit dieser Kombination aus Region und Abonnement erstellen. Advisor überprüft Ihre Abonnements und bietet Empfehlungen, damit Sie weniger Speicherkonten für Abonnements/Regionen entwerfen, bei denen das Höchstlimit bald erreicht ist.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Erhöhen der Größe Ihrer VPN-Gateway-SKU, um eine hohe P2S-Nutzung zu vermeiden

Jede Azure-VPN-Gateway-SKU kann nur eine bestimmte Anzahl von P2S-Verbindungen gleichzeitig unterstützen. Wenn Ihre Verbindungsanzahl den Grenzwert Ihres Gateways fast erreicht hat, können möglicherweise keine weiteren Verbindungen hergestellt werden. Durch Erhöhen der Größe Ihres Gateways können Sie mehr gleichzeitige P2S-Benutzer unterstützen. Advisor gibt Ihnen Empfehlungen und Anweisungen zum Vergrößern Ihres Gateways.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Erhöhen der Größe Ihrer VPN-Gateway-SKU, um eine hohe CPU-Auslastung zu vermeiden

Bei hohem Datenverkehr werden Pakete vom VPN-Gateway aufgrund hoher CPU-Auslastung möglicherweise gelöscht. Sie sollten ein Upgrade Ihrer VPN-Gateway-SKU in Erwägung ziehen. Durch Erhöhen der Größe Ihres VPN-Gateways stellen Sie sicher, dass Verbindungen nicht aufgrund hoher CPU-Auslastung unterbrochen werden. Advisor gibt Ihnen Empfehlungen zum proaktiven Vermeiden dieses Problems. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Erhöhen der Batchgröße beim Laden, um Durchsatz, Datenkomprimierung und Abfrageleistung zu verbessern

Advisor erkennt, ob Sie Leistung und Durchsatz bei Ladevorgängen verbessern können, indem Sie die Batchgröße beim Laden von Daten in Ihre Datenbank erhöhen. Erwägen Sie die COPY-Anweisung. Wenn Sie die COPY-Anweisung nicht verwenden können, sollten Sie ggf. die Batchgröße bei Verwendung von Ladehilfsprogrammen wie der SqlBulkCopy-API oder BCP erhöhen. Als allgemeine Regel hat sich eine Batchgröße zwischen 100.000 und 1 Million Zeilen bewährt. Durch Erhöhen der Batchgröße lassen sich Ladedurchsatz, Datenkomprimierung und Abfrageleistung verbessern.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Bereitstellen des Speicherkontos in derselben Region, um die Latenz beim Laden zu minimieren

Advisor erkennt, ob Sie Daten aus einer anderen Region als der Ihres dedizierten SQL-Pools laden. Sie sollten Daten aus einem Speicherkonto laden, das sich in derselben Region wie der dedizierte SQL-Pool befindet, um die Wartezeit beim Laden zu minimieren. Dadurch werden die Latenz verringert und die Leistung beim Laden verbessert.

## <a name="use-a-supported-kubernetes-version"></a>Verwenden einer unterstützten Kubernetes-Version

Advisor ermittelt nicht unterstützte Versionen von Kubernetes.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Optimieren der Leistung von Servern für Azure Database for MySQL, Azure Database for PostgreSQL und Azure Database for MariaDB

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Korrigieren einer hohen CPU-Auslastung bei Servern für Azure Database for MySQL, Azure Database for PostgreSQL und Azure Database for MariaDB mit CPU-Engpässen
Eine länger andauernde hohe CPU-Auslastung kann die Abfrageleistung für Ihre Workload beeinträchtigen. Durch Erhöhen der CPU-Größe können Sie die Laufzeit von Datenbankabfragen optimieren und die Gesamtleistung verbessern. Advisor ermittelt Server mit einer hohen CPU-Auslastung, auf denen wahrscheinlich Workloads mit eingeschränkter CPU-Leistung ausgeführt werden, und empfiehlt die Skalierung der Computegröße.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Reduzieren der Speicherbeschränkungen auf den Servern für Azure Database for MySQL, Azure Database for PostgreSQL und Azure Database for MariaDB oder Verschieben in arbeitsspeicheroptimierte SKU
Eine niedrige Cachetrefferquote kann die Abfrageleistung beeinträchtigen und zu erhöhtem IOPS führen. Dieser Zustand kann durch einen ungültigen Abfrageplan oder eine speicherintensive Workload verursacht werden. Durch Korrigieren des Abfrageplans oder [Vergrößern des Speichers](../postgresql/concepts-pricing-tiers.md) des Servers für Azure Database for PostgreSQL, Azure Database for MySQL oder Azure Database for MariaDB kann die Ausführung der Datenbankworkload optimiert werden. Azure Advisor ermittelt Server, die von einer solchen hohen Änderungsrate des Pufferpools betroffen sind. Es wird empfohlen, eine der folgenden Maßnahmen zu ergreifen: 
- Korrigieren des Abfrageplans
- Verschieben zu einer SKU mit mehr Arbeitsspeicher 
- Erhöhen der Speichergröße, um den IOPS-Wert zu erhöhen

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Verwenden eines Lesereplikats für Azure Database for MySQL oder Azure Database for PostgreSQL zum Aufskalieren von Lesevorgängen für leseintensive Workloads
Advisor nutzt eine workloadbasierte Heuristik, z. B. das Verhältnis von Lese- zu Schreibvorgängen auf dem Server in den letzten sieben Tagen, um leseintensive Workloads zu identifizieren. Eine Azure Database for PostgreSQL-Ressource oder Azure Database for MySQL-Ressource mit einem hohen Verhältnis von Lese- zu Schreibvorgängen kann zu CPU- und/oder Speicherkonflikten führen, durch die die Abfrageleistung beeinträchtigt wird. Durch Hinzufügen eines [Replikats](../postgresql/howto-read-replicas-portal.md) können Schreibvorgänge auf den Replikatserver aufskaliert und dadurch CPU- oder Speicherkonflikte auf dem primären Server verhindert werden. Advisor ermittelt Server mit diesen leseintensiven Workloads und empfiehlt, dass ein [Lesereplikat](../postgresql/concepts-read-replicas.md) hinzugefügt wird, um einige der Leseworkloads auszulagern.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Skalieren des Servers für Azure Database for MySQL, Azure Database for PostgreSQL oder Azure Database for MariaDB auf eine höhere SKU zur Verhinderung von Verbindungseinschränkungen
Jede neue Verbindung mit dem Datenbankserver belegt Speicher. Die Leistung des Datenbankservers verschlechtert sich, wenn aufgrund einer [Obergrenze](../postgresql/concepts-limits.md) des Speichers Fehler bei Verbindungen mit dem Server auftreten. Azure Advisor ermittelt Server mit vielen Verbindungsfehlern. Es empfiehlt sich, ein Upgrade der Verbindungsbeschränkungen des Servers vorzunehmen, sodass dem Server mehr Arbeitsspeicher zur Verfügung steht, indem Sie eine der folgenden Aktionen ausführen:
- Hochskalieren der Computeressourcen 
- Verwenden von arbeitsspeicheroptimierten SKUs mit mehr Computekapazität pro Kern

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Skalieren Ihres Caches auf eine andere Größe oder SKU, um die Cache- und Anwendungsleistung zu verbessern

Cache-Instanzen erzielen die optimale Leistung, wenn Sie nicht mit hoher Arbeitsspeicherauslastung, hoher Serverauslastung oder hoher Netzwerkbandbreite ausgeführt werden. Diese Zustände können dazu führen, dass sie nicht mehr reagieren, nicht mehr verfügbar sind oder dass Datenverluste auftreten. Advisor ermittelt Cache-Instanzen mit diesen Zuständen. Es wird empfohlen, eine der folgenden Maßnahmen zu ergreifen:
- Wenden Sie bewährte Methoden an, um die Arbeitsspeicherauslastung, die Serverauslastung oder die Netzwerkbandbreite zu reduzieren.
- Führen Sie eine Skalierung auf eine andere Größe oder SKU mit mehr Kapazität aus.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Hinzufügen von Regionen mit Datenverkehr zu Ihrem Azure Cosmos DB-Konto

Advisor ermittelt Azure Cosmos DB-Konten mit Datenverkehr aus einer derzeit nicht konfigurierten Region. Es wird empfohlen, diese Region hinzuzufügen. Dies dient der Verbesserung der Latenz für Anforderungen aus der betreffenden Region und stellt die Verfügbarkeit im Falle von Regionsausfällen sicher. [Weitere Informationen zur globalen Datenverteilung mit Azure Cosmos DB](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Konfigurieren der Azure Cosmos DB-Indizierungsrichtlinie mit benutzerdefinierten eingeschlossenen oder ausgeschlossenen Pfaden

Advisor ermittelt Azure Cosmos DB-Container, die die standardmäßige Indizierungsrichtlinie verwenden, aber von einer benutzerdefinierten Indizierungsrichtlinie profitieren könnten. Diese Einschätzung basiert auf dem Workloadmuster. Die Standardindizierungsrichtlinie indiziert alle Eigenschaften. Mit einer benutzerdefinierten Indizierungsrichtlinie mit expliziten ein- bzw. ausgeschlossenen Pfaden für Abfragefilter lassen sich die Anforderungseinheiten und der Speicherbedarf für die Indizierung verringern. [Weitere Informationen zum Ändern von Indizierungsrichtlinien](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Festlegen der Azure Cosmos DB-Abfrageseitengröße (MaxItemCount) auf „–1“ 

Azure Advisor ermittelt Azure Cosmos DB-Container, die eine Abfrageseitengröße von 100 verwenden. Zum Beschleunigen von Scans wird empfohlen, eine Seitengröße von –1 zu verwenden. [Weitere Informationen über MaxItemCount](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>Verwenden Sie zum Verbessern der Clusterleistung ggf. die Funktion für beschleunigte Schreibvorgänge in Ihrem HBase-Cluster.
Azure Advisor analysiert die Systemprotokolle aus den letzten 7 Tagen und identifiziert, ob in Ihrem Cluster die folgenden Szenarien aufgetreten sind:
1. Hohe Latenz bei der WAL-Synchronisierung 
2. Hohe Anzahl von Schreibanforderungen (mindestens 3 Ein-Stunden-Fenster mit mehr als 1.000 avg_write_requests/s/Knoten)

Diese Umstände sind Hinweise darauf, dass der Cluster eine hohe Schreiblatenz aufweist. Dies kann auf eine hohe Arbeitsauslastung in Ihrem Cluster zurückzuführen sein. Zur Leistungsverbesserung beim Cluster empfiehlt es sich, die von Azure HDInsight HBase bereitgestellte Funktion für beschleunigte Schreibvorgänge zu nutzen. Mit der Funktion für beschleunigte Schreibvorgänge für HDInsight-Apache HBase-Cluster werden jedem RegionServer (Workerknoten) verwaltete SSD Premium-Datenträger hinzugefügt, anstatt auf Cloudspeicher zurückzugreifen. Dies führt zu einer niedrigen Schreiblatenz und höherer Resilienz für Ihre Anwendungen. Weitere Informationen zu dieser Funktion finden Sie [hier](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight).

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>Überprüfen des Cachezeitraums von Azure Data Explorer-Tabellen (Richtlinie) zur Leistungsverbesserung (Vorschau)
Bei dieser Empfehlung werden Azure Data Explorer-Tabellen mit einer hohen Zahl von Abfragen ermittelt, die über den konfigurierten Cachezeitraum (Richtlinie) hinausgehen. (Es werden die obersten zehn Tabellen nach Abfrageprozentsatz angezeigt, von denen auf Daten außerhalb des Caches zugegriffen wird.) Die empfohlene Aktion zum Verbessern der Leistung des Clusters lautet: Beschränken Sie die Abfragen für diese Tabelle auf den erforderlichen Mindestzeitbereich (innerhalb der definierten Richtlinie). Falls Daten des gesamten Zeitbereichs erforderlich sind, können Sie als Alternative den Cachezeitraum auch auf den empfohlenen Wert erhöhen.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>Verbessern der Leistung durch Optimierung der Größenanpassung für die temporäre MySQL-Tabelle
Die Advisor-Analyse zeigt, dass es für Ihren MySQL-Server ggf. zu unnötigem E/A-Mehraufwand kommt, weil die Parametereinstellungen für temporäre Tabellen zu niedrig sind. Dies kann zu unnötigen Datenträgertransaktionen und einer verringerten Leistung führen. Wir empfehlen Ihnen, die Parameterwerte „tmp_table_size“ und „max_heap_table_size“ zu erhöhen, um die Anzahl von Datenträgertransaktionen zu reduzieren. [Weitere Informationen](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Verteilen der Daten in einer Servergruppe, um eine Workload auf Knoten zu verteilen
Advisor identifiziert die Servergruppen, in denen die Daten nicht verteilt wurden, aber im Koordinator bleiben. Basierend darauf empfiehlt Advisor, dass für alle Vorteile von Hyperscale (Citus) Daten auf Workerknoten für Ihre Servergruppen verteilt werden sollten. Dies wird die Abfrageleistung durch Nutzung der Ressourcen der einzelnen Knoten in der Servergruppe verbessern. [Weitere Informationen](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>Verbessern der Benutzerfreundlichkeit und der Konnektivität, indem Sie virtuelle Computer näher am Standort der Windows Virtual Desktop-Bereitstellung bereitstellen
Wir haben festgestellt, dass sich Ihre virtuellen Computer nicht in der Region befinden, von der aus Ihre Benutzer eine WVD-Verbindung (Windows Virtual Desktop) herstellen, oder dass Ihre virtuellen Computer weit von dieser Region entfernt sind. Dies kann zu längeren Verbindungsreaktionszeiten führen und wirkt sich insgesamt negativ auf die Benutzerfreundlichkeit von WVD aus. Verwenden Sie bei der Erstellung virtueller Computer für Ihre Hostpools nach Möglichkeit eine Region, die sich näher beim Benutzer befindet. Eine geringe Entfernung verbessert die Zufriedenheit mit dem WVD-Dienst sowie die allgemeine Benutzerfreundlichkeit. [Hier](../virtual-desktop/connection-latency.md) erfahren Sie mehr zur Verbindungswartezeit.

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Upgrade auf die neueste Version des SDK des plastischen Readers
Wir haben unter diesem Abonnement Ressourcen mit veralteten Versionen des SDK des plastischen Readers identifiziert. Die Verwendung der neuesten Version des SDK des plastischen Readers bietet Ihnen aktualisierte Sicherheit, Leistung und einen erweiterten Satz von Features, mit denen Sie Ihre Integrationsmöglichkeiten anpassen und verbessern können.
Weitere Informationen zum [Immersive Reader SDK](../cognitive-services/immersive-reader/index.yml)


## <a name="how-to-access-performance-recommendations-in-advisor"></a>Zugreifen auf Advisor-Empfehlungen zur Leistung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie [Advisor](https://aka.ms/azureadvisordashboard).

2.  Klicken Sie auf dem Advisor-Dashboard auf die Registerkarte **Leistung**.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:

* [Einführung in Advisor](advisor-overview.md)
* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Advisor-Bewertung](azure-advisor-score.md)
* [Reduzieren der Dienstkosten mithilfe des Azure Advisors](advisor-cost-recommendations.md)
* [Zuverlässigkeitsempfehlungen von Advisor](advisor-high-availability-recommendations.md)
* [Sicherheitsempfehlungen von Advisor](advisor-security-recommendations.md)
* [Sicherstellen des optimalen Betriebs mit dem Azure Advisor](advisor-operational-excellence-recommendations.md)
* [Advisor-REST-API](/rest/api/advisor/)