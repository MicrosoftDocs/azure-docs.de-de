---
title: Azure SQL-Datenbank Hyperscale – Häufig gestellte Fragen (FAQs)
description: In diesem Artikel werden häufig gestellte Fragen beantwortet, die Kunden zu einer Datenbank in SQL-Datenbank in der Dienstebene „Hyperscale“ haben (allgemein als Hyperscale-Datenbank bezeichnet).
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 4ea1982e7545f4ac39a5ecd15dc9e19a582ae31c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459629"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL-Datenbank Hyperscale – Häufig gestellte Fragen (FAQs)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Artikel enthält Antworten auf häufig gestellte Fragen für Kunden, die in Erwägung ziehen, eine Datenbank in Azure SQL-Datenbank mit der Dienstebene „Hyperscale“ zu verwenden, die im weiteren Verlauf dieses Artikels einfach als „Hyperscale“ bezeichnet wird. In diesem Artikel werden die von Hyperscale unterstützten Szenarien und die Features beschrieben, die mit Hyperscale kompatibel sind.

- Diese FAQs richten sich an Leser, die einen allgemeinen Überblick über die Dienstebene „Hyperscale“ besitzen und auf der Suche nach Antworten auf ihre Fragen und Probleme sind.
- Diese FAQs sind nicht als Handbuch oder zur Beantwortung von Fragen im Zusammenhang mit der Verwendung einer Hyperscale-Datenbank zu verstehen. Eine Einführung in Hyperscale finden Sie in der Dokumentation zu [Azure SQL-Datenbank Hyperscale](service-tier-hyperscale.md).

## <a name="general-questions"></a>Allgemeine Fragen

### <a name="what-is-a-hyperscale-database"></a>Was ist eine Hyperscale-Datenbank?

Eine Hyperscale-Datenbank ist eine Datenbank in SQL-Datenbank mit der Dienstebene „Hyperscale“, die durch die Hyperscaletechnologie zur horizontalen Skalierung unterstützt wird. Eine Hyperscale-Datenbank unterstützt bis zu 100 TB Daten, zeichnet sich durch einen hohen Durchsatz sowie eine hohe Leistung aus und bietet eine schnelle Skalierung, um sich rasch an die Workloadanforderungen anzupassen. Eine Skalierung ist für die Anwendung transparent – d. h., Konnektivität, Abfrageverarbeitung usw. funktionieren wie bei jeder anderen Datenbank in Azure SQL-­Datenbank.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Welche Ressourcentypen und Kaufmodelle unterstützt Hyperscale?

Die Dienstebene „Hyperscale“ ist nur für Einzeldatenbanken verfügbar, bei denen das vCore-basierte Kaufmodell in Azure SQL-Datenbank zum Einsatz kommt.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Inwiefern unterscheidet sich die Dienstebene „Hyperscale“ von den Dienstebenen „Universell“ und „Unternehmenskritisch“?

Die auf virtuellen Kernen basierenden Dienstebenen unterscheiden sich im Hinblick auf Datenbankverfügbarkeit, Speichertyp, Leistung und maximaler Größe, wie in der folgenden Tabelle beschrieben wird.

| | Ressourcentyp | Universell |  Hyperscale | Unternehmenskritisch |
|:---:|:---:|:---:|:---:|:---:|
| **Am besten geeignet für** |All|Bietet budgetorientierte ausgewogene Compute- und Speicheroptionen.|Die meisten geschäftlichen Workloads. Automatische Skalierung der Speichergröße bis zu 100 TB, schnelle vertikale und horizontale Computeskalierung, schnelle Datenbankwiederherstellung.|OLTP-Anwendungen mit hoher Transaktionsrate und geringen Latenzen bei E/A-Vorgängen. Bietet mit mehreren synchron aktualisierten Replikaten höchste Resilienz gegenüber Fehlern und schnelle Failover.|
|  **Ressourcentyp** ||SQL-Datenbank/SQL Managed Instance | Einzeldatenbank | SQL-Datenbank/SQL Managed Instance |
| **Computegröße**|SQL-Datenbank* | 1 bis 80 virtuelle Kerne | 1 bis 80 virtuelle Kerne* | 1 bis 80 virtuelle Kerne |
| **Computegröße**|Verwaltete SQL-Instanz | 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne | – | 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne |
| **Speichertyp** | All |Storage Premium (remote, pro Instanz) | Entkoppelter Speicher mit lokalem SSD-Cache (pro Instanz) | Äußerst schneller lokaler SSD-Speicher (pro Instanz) |
| **Speichergröße** | SQL-Datenbank*| 5 GB – 4 TB | Bis zu 100 TB | 5 GB – 4 TB |
| **Speichergröße** | Verwaltete SQL-Instanz  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **IOPS** | Einzeldatenbank | 500 IOPS pro V-Kern mit maximal 7.000 IOPS | Hyperscale ist eine mehrstufige Architektur mit Caching auf mehreren Ebenen. Der tatsächliche IOPS-Wert hängt von der Workload ab. | 5\.000 IOPS mit maximal 200.000 IOPS|
| **IOPS** | Verwaltete SQL-Instanz | Hängt von der Dateigröße ab | – | 1\.375 IOPS/V-Kern |
|**Verfügbarkeit**|All|1 Replikat, keine horizontale Leseskalierung, kein lokaler Cache | Mehrere Replikate, bis zu 4 Replikate mit horizontaler Leseskalierung, teilweise lokaler Cache | 3 Replikate, 1 Replikat mit horizontaler Leseskalierung, zonenredundante Hochverfügbarkeit, vollständiger lokaler Cache |
|**Sicherungen**|All|RA-GRS, Aufbewahrung 7–35 Tage (standardmäßig 7 Tage)| RA-GRS, Aufbewahrung 7 Tage, konstante Zeitpunktwiederherstellung (Point-in-Time Recovery, PITR) | RA-GRS, Aufbewahrung 7–35 Tage (standardmäßig 7 Tage) |

\* Pools für elastische Datenbanken werden in der Dienstebene „Hyperscale“ nicht unterstützt.

### <a name="who-should-use-the-hyperscale-service-tier"></a>Wer sollte die Dienstebene „Hyperscale“ verwenden?

Die Dienstebene „Hyperscale“ ist für Kunden konzipiert, die große lokale SQL Server-Datenbanken verwenden und ihre Anwendungen durch eine Migration zur Cloud modernisieren möchten, oder für Kunden, die bereits Azure SQL-Datenbank verwenden und das Potenzial zum Datenbankwachstum noch umfassender ausschöpfen möchten. Außerdem eignet sich Hyperscale für Kunden, die eine hohe Leistung und Skalierbarkeit wünschen. Mit Hyperscale erhalten Sie Folgendes:

- Datenbankgröße bis zu 100 TB
- Schnelle Datenbanksicherungen unabhängig von der Datenbankgröße (Sicherungen basieren auf Speichermomentaufnahmen)
- Schnelle Datenbankwiederherstellungen unabhängig von der Datenbankgröße (Wiederherstellungen stammen aus Speichermomentaufnahmen)
- Höheren Protokolldurchsatz unabhängig von der Datenbankgröße und der Anzahl der virtuellen Kerne
- Horizontale Leseskalierung mit einem oder mehreren schreibgeschützten Replikaten, die für Abladelesevorgänge und als Hot Standbys verwendet werden.
- Schnelles Hochskalieren der Computeleistung in konstanter Zeit, um eine höhere Leistung bei der Verarbeitung umfangreicher Workloads bereitzustellen, und anschließend Herunterskalieren in konstanter Zeit. Dieser Vorgang ist mit dem zentralen Hoch- und Herunterskalieren zwischen beispielsweise P6 und P11 vergleichbar, aber viel schneller, da er nicht von der Datengröße abhängig ist.

### <a name="what-regions-currently-support-hyperscale"></a>Welche Regionen unterstützen derzeit Hyperscale?

Die Dienstebene „Hyperscale“ ist derzeit in den unter [Hyperscale für Azure SQL-Datenbank – Übersicht](service-tier-hyperscale.md#regions) aufgeführten Regionen verfügbar.

### <a name="can-i-create-multiple-hyperscale-databases-per-server"></a>Können mehrere Hyperscale-Datenbanken pro Server erstellt werden?

Ja. Weitere Informationen und Beschränkungen hinsichtlich der Anzahl von Hyperscale-Datenbanken pro Server finden Sie unter [Ressourceneinschränkungen in SQL-Datenbank für Einzeldatenbanken und Pooldatenbanken auf einem Server](resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Welche Leistungsmerkmale weist eine Hyperscale-Datenbank auf?

Die Hyperscale-Architektur bietet eine hohe Leistung und einen hohen Durchsatz und unterstützt außerdem große Datenbankgrößen.

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Was beinhaltet die Skalierbarkeit einer Hyperscale-Datenbank?

Hyperscale bietet schnelle Skalierbarkeit basierend auf Ihrem Workloadbedarf.

- **Zentrales Hoch-/Herunterskalieren**

  Bei Hyperscale können Sie die primäre Computegröße im Hinblick auf Ressourcen wie CPU und Arbeitsspeicher in konstanter Zeit hochskalieren und anschließend herunterskalieren. Da der Speicher gemeinsam genutzt wird, ist das zentrale Hoch- und Herunterskalieren kein Vorgang, der von der Datengröße abhängig ist.  
- **Horizontal herunter-/hochskalieren**

  Mit Hyperscale erhalten Sie auch die Möglichkeit, mindestens ein zusätzliches Computereplikat bereitzustellen, mit dem Sie Ihre Leseanforderungen verarbeiten können. Dies bedeutet, dass Sie diese zusätzlichen Computereplikate als schreibgeschützte Replikate verwenden können, um Ihre Leseworkload vom primären Computeknoten auszulagern. Diese Replikate sind nicht nur schreibgeschützt, sondern dienen außerdem als Hot Standbys im Falle eines Failovers vom primären Knoten.

  Die Bereitstellung der einzelnen zusätzlichen Computereplikate kann in konstanter Zeit erfolgen und ist ein Onlinevorgang. Sie können eine Verbindung mit diesen zusätzlichen schreibgeschützten Computereplikaten herstellen, indem Sie das Argument `ApplicationIntent` in Ihrer Verbindungszeichenfolge auf `ReadOnly` festlegen. Verbindungen mit dem Anwendungszweck `ReadOnly` werden automatisch an eines der zusätzlichen schreibgeschützten Computereplikate weitergeleitet.

## <a name="deep-dive-questions"></a>Vertiefende Fragen

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-server"></a>Können Hyperscale- und Einzeldatenbanken in einem einzelnen Server kombiniert werden?

Ja, das ist möglich.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Müssen für Hyperscale Änderungen am Anwendungsprogrammiermodell vorgenommen werden?

Nein, Ihr Anwendungsprogrammiermodell muss nicht geändert werden. Sie verwenden Ihre Verbindungszeichenfolge wie gewohnt und die anderen regulären Modi, um mit Ihrer Hyperscale-Datenbank zu interagieren.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Welche Transaktionsisolationsstufe ist die Standardeinstellung in einer Hyperscale-Datenbank?

Für das primäre Replikat gilt die RCSI-Transaktionsisolationsstufe (Read Committed Snapshot Isolation, Isolation der READ COMMITTED-Momentaufnahme). In den sekundären Replikaten mit horizontaler Leseskalierung ist die Standardisolationsstufe die Momentaufnahme.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Kann ich meine lokalen oder IaaS-basierten SQL Server-Lizenzen zu Hyperscale migrieren?

Ja, der [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) ist für Hyperscale verfügbar. Jede SQL Server Standard-Kern kann 1 virtuellen Hyperscale-Kern zugeordnet werden. Jede SQL Server Enterprise-Kern kann 4 virtuellen Hyperscale-Kernen zugeordnet werden. Sie benötigen keine SQL-Lizenz für sekundäre Replikate. Der Preis für den Azure-Hybridvorteil wird automatisch auf (sekundäre) Replikate mit horizontaler Leseskalierung angewendet.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Für welche Art von Workloads ist Hyperscale konzipiert?

Hyperscale unterstützt alle SQL Server-Workloads, wurde aber in erster Linie für OLTP optimiert. Zudem können Sie Hybrid- (HTAP-) und Analyseworkloads (Data Mart) migrieren.

### <a name="how-can-i-choose-between-azure-synapse-analytics-and-azure-sql-database-hyperscale"></a>Wie kann ich zwischen Azure Synapse Analytics und Azure SQL-Datenbank Hyperscale auswählen?

Wenn Sie zurzeit interaktive Analyseabfragen mit SQL Server als Data Warehouse ausführen, stellt Hyperscale eine hervorragende Option dar, weil Sie kleine und mittelgroße Data Warehouses (z.B. von wenigen TB bis hin zu 100 TB) zu geringeren Kosten hosten und Ihre SQL Server Data Warehouse-Workloads mit nur minimalen Änderungen am T-SQL-Code zu Hyperscale migrieren können.

Wenn Sie Datenanalysen im großen Umfang mit komplexen Abfragen und nachhaltigen Erfassungsraten von mehr als 100 MB/Sek. oder aber mithilfe von Parallel Data Warehouse (PDW), Teradata oder anderen Data Warehouses mit MPP-Design (Massively Parallel Processing) durchführen, ist Azure Synapse Analytics möglicherweise die beste Wahl.
  
## <a name="hyperscale-compute-questions"></a>Fragen zu Hyperscale Compute

### <a name="can-i-pause-my-compute-at-any-time"></a>Kann ich die Computebereitstellung jederzeit anhalten?

Derzeit leider nicht. Sie können Ihre Computeleistung und die Anzahl von Replikaten jedoch herunterskalieren, um außerhalb von Spitzenzeiten Kosten zu senken.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Kann ich ein Computereplikat mit zusätzlichem RAM für meine speicherintensive Workload bereitstellen?

Nein. Um mehr RAM zu erhalten, müssen Sie ein Upgrade auf eine höhere Computegröße durchführen. Weitere Informationen finden Sie unter [Dienstebene „Hyperscale“ (Vorschau)](resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Kann ich mehrere Computereplikate unterschiedlicher Größe bereitstellen?

Nein.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Wie viele Replikate mit horizontaler Leseskalierung werden unterstützt?

Die Hyperscale-Datenbanken werden standardmäßig mit einem Replikat mit horizontaler Leseskalierung (zwei Replikate, einschließlich des primären Replikats) erstellt. Sie können die Anzahl der schreibgeschützten Replikate zwischen 0 und 4 über das [Azure Portal](https://portal.azure.com) oder die [REST-API](/rest/api/sql/databases/createorupdate) skalieren.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Muss ich zur Erzielung von Hochverfügbarkeit zusätzliche Computereplikate bereitstellen?

Bei Hyperscale-Datenbanken wird Datenresilienz auf Speicherebene bereitgestellt. Sie benötigen nur ein Replikat, um Resilienz bereitzustellen. Wenn das Computereplikat ausfällt, wird automatisch und ohne Datenverluste ein neues Replikat erstellt.

Ist allerdings nur ein Replikat vorhanden, kann das Erstellen des lokalen Caches im neuen Replikat nach einem Failover einige Zeit in Anspruch nehmen. Während der Cacheneuerstellung ruft die Datenbank Daten direkt von den Seitenservern ab. Das führt zu einer höheren Speicherlatenz und einer Beeinträchtigung der Abfrageleistung.

Bei unternehmenskritischen Apps, die hohe Verfügbarkeit bei minimaler Failoverauswirkung erfordern, sollten Sie mindestens zwei Computereplikate bereitstellen, einschließlich des primären Computereplikats. Dies ist die Standardkonfiguration. Auf diese Weise steht ein Hot-Standby-Replikat zur Verfügung, das als Failoverziel dient.

## <a name="data-size-and-storage-questions"></a>Fragen zur Datengröße und Speicherkapazität

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Was ist die maximale Datenbankgröße, die mit Hyperscale unterstützt wird?

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Wie groß ist das Transaktionsprotokoll bei Hyperscale?

Das Transaktionsprotokoll bei Hyperscale ist praktisch unendlich. Sie müssen sich keine Gedanken darum machen, dass der Protokollspeicherplatz bei einem System mit hohem Protokolldurchsatz ausgeschöpft sein könnte. Allerdings wird bei kontinuierlichen intensiv schreibenden Workloads die Protokollgenerierungsrate möglicherweise gedrosselt. Die dauerhafte Protokollgenerierungsrate liegt bei 100 MB/Sek.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Kann meine `tempdb` mit zunehmendem Wachstum skaliert werden?

Ihre `tempdb`-Datenbank befindet sich im lokalen SSD-Speicher. Ihre Größe ist proportional zu der Computegröße, die Sie bereitstellen. Ihre `tempdb` ist für die Bereitstellung von maximaler Leistung optimiert. Die Größe von `tempdb` kann nicht konfiguriert werden und wird für Sie verwaltet.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Wird die Datenbankgröße automatisch skaliert, oder muss ich die Größe von Datendateien verwalten?

Die Datenbankgröße nimmt automatisch zu, je mehr Daten Sie einfügen bzw. erfassen.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Was ist die kleinste Datenbankgröße, die Hyperscale unterstützt oder mit der sie beginnt?

40 GB. Eine Hyperscale-Datenbank wird mit einer Startgröße von 10 GB erstellt. Anschließend wird sie alle 10 Minuten um 10 GB vergrößert, bis eine Größe von 40 GB erreicht ist. Jeder dieser Blöcke von 10 GB wird auf einem anderen Seitenserver zugeordnet, um mehr IOPS und höhere E/A-Parallelität zu bieten. Aufgrund dieser Optimierung wird die Datenbank automatisch auf mindestens 40 GB vergrößert, auch wenn Sie eine anfängliche Datenbankgröße auswählen, die kleiner als 40 GB ist.

### <a name="in-what-increments-does-my-database-size-grow"></a>In welchen Schritten wird die Datenbankgröße erhöht?

Jede Datendatei wächst um 10 GB. Mehrere Datendateien können gleichzeitig wachsen.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Ist der Speicher in Hyperscale lokal oder remote?

In Hyperscale werden Datendateien im Azure-Standardspeicher gespeichert. Daten werden im lokalen SSD-Speicher auf Seitenservern, die sich in der Nähe der Computereplikate befinden, vollständig zwischengespeichert. Außerdem verfügen Computereplikate über Datencaches auf lokalem SSD und im Arbeitsspeicher, damit Daten von Remoteseitenservern seltener abgerufen werden müssen.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Können bei Hyperscale Dateien oder Dateigruppen verwaltet oder definiert werden?

Nein. Datendateien werden automatisch hinzugefügt. Die häufigsten Gründe für das Erstellen zusätzlicher Dateigruppen gelten nicht für die Hyperscale-Speicherarchitektur.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kann für eine Datenbank eine feste Obergrenze für das Datenwachstum festgelegt werden?

Nein.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Wie werden Datendateien mit Hyperscale angelegt?

Die Datendateien werden von Seitenservern mit einem Seitenserver pro Datendatei gesteuert. Wenn die Datengröße zunimmt, werden Datendateien und zugeordnete Seitenserver hinzugefügt.

### <a name="is-database-shrink-supported"></a>Wird die Verkleinerung von Datenbanken unterstützt?

Nein.

### <a name="is-data-compression-supported"></a>Wird Datenkomprimierung unterstützt?

Ja, einschließlich Zeilen-, Seiten- und Columnstorekomprimierung.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Werden Daten in einer großen Tabelle auf mehrere Datendateien verteilt?

Ja. Die Datenseiten einer bestimmten Tabelle können auf mehrere Datendateien, die Teil der gleichen Dateigruppe sind, verteilt werden. Bei SQL Server kommt eine [proportionale Füllstrategie](/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) zum Einsatz, um Daten auf Datendateien zu verteilen.

## <a name="data-migration-questions"></a>Fragen zur Datenmigration

### <a name="can-i-move-my-existing-databases-in-azure-sql-database-to-the-hyperscale-service-tier"></a>Können vorhandene Datenbanken in Azure SQL-Datenbank zur Dienstebene „Hyperscale“ migriert werden?

Ja. Sie können Ihre vorhandenen Datenbanken in Azure SQL-Datenbank zu Hyperscale migrieren. Dies erfolgt im Rahmen einer unidirektionalen Migration. Datenbanken können nicht von Hyperscale zu einer anderen Dienstebene migriert werden. Für Proof of Concepts (POCs) wird empfohlen, eine Kopie Ihrer Datenbank zu erstellen und die Kopie zu Hyperscale zu migrieren. 

Die Zeit zum Verschieben einer vorhandenen Datenbank zu Hyperscale umfasst die Zeit zum Kopieren der Daten und die Zeit zum Wiedergeben der Änderungen, die beim Kopieren von Daten in der Quelldatenbank vorgenommen wurden. Die Zeit für das Kopieren der Daten verhält sich proportional zur Datenmenge. Die Zeit für die Wiedergabe von Änderungen ist kürzer, wenn die Verschiebung während geringer Schreibaktivitäten erfolgt.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Kann ich meine Hyperscale-Datenbanken zu anderen Dienstebenen migrieren?

Nein. Eine Hyperscale-Datenbank kann derzeit nicht zu einer anderen Dienstebene migriert werden.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Müssen bei der Migration zur Dienstebene „Hyperscale“ Einbußen hinsichtlich des Funktionsumfangs in Kauf genommen werden?

Ja. Einige Features von Azure SQL-Datenbank werden in Hyperscale noch nicht unterstützt, wie u. a. die langfristige Sicherungsaufbewahrung. Nach der Migration Ihrer Datenbanken zu Hyperscale funktionieren diese Features nicht mehr.  Wir gehen davon aus, dass diese Einschränkungen nur temporär bestehen.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Kann ich meine lokale SQL Server-Datenbank oder meine SQL Server-Datenbank auf einem virtuellen Computer der Cloud zu Hyperscale migrieren?

Ja. Sie können alle vorhandenen Migrationstechnologien verwenden, um zu Hyperscale zu migrieren, einschließlich Transaktionsreplikation und anderen Technologien zur Datenverschiebung (Massenkopieren, Azure Data Factory, Azure Databricks, SSIS). Lesen Sie auch die Informationen zum [Azure Database Migration Service](../../dms/dms-overview.md), der viele Migrationsszenarien unterstützt.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Wie lange ist die Ausfallzeit bei der Migration aus einer lokalen Umgebung oder der Umgebung eines virtuellen Computers zu Hyperscale, und wie kann ich sie minimieren?

Die Ausfallzeit bei der Migration zu Hyperscale ist identisch mit der Ausfallzeit bei der Migration Ihrer Datenbanken zu anderen Dienstebenen von Azure SQL-Datenbank. Mithilfe von [Transaktionsreplikationen](replication-to-sql-database.md#data-migration-scenario
) können Sie die Ausfallzeit bei der Migration von Datenbanken mit einer Größe von mehreren TB minimieren. Bei sehr großen Datenbanken (über 10 TB) empfiehlt es sich eventuell, Daten mit ADF, Spark oder anderen Datenverschiebungstechnologien zu migrieren.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Wie lange würde es dauern, bis eine bestimmte Datenmenge in Hyperscale migriert ist?

Hyperscale kann 100 MB/Sek. neuer/geänderter Daten verarbeiten, aber die Zeit für das Migrieren von Daten in Datenbanken in Azure SQL-Datenbank wird auch durch den verfügbaren Netzwerkdurchsatz, die Lesegeschwindigkeit der Quelle und das Servicelevelziel der Zieldatenbank beeinflusst.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-azure-synapse-analytics"></a>Können Daten aus Blob Storage gelesen und schnell geladen werden (wie PolyBase in Azure Synapse Analytics)?

Sie können festlegen, dass eine Clientanwendung Daten aus Azure Storage lesen und Daten in eine Hyperscale-Datenbank laden soll (genauso, wie dies bei jeder anderen Datenbank in Azure SQL-Datenbank möglich ist). PolyBase wird in Azure SQL-Datenbank derzeit nicht unterstützt. Als Alternative zum schnellen Laden können Sie entweder [Azure Data Factory](../../data-factory/index.yml) oder einen Spark-Auftrag in [Azure Databricks](/azure/azure-databricks/) mit dem [Spark-Connector für SQL](spark-connector.md) verwenden. Der Spark-Connector für SQL unterstützt Importe mit BULK INSERT.

Es ist auch möglich, Daten aus einem Azure-Blobspeicher mithilfe von BULK INSERT oder OPENROWSET zu lesen: [Beispiele für den Massenzugriff auf Daten in Azure Blob Storage.](/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)

Einfache Wiederherstellungen oder Massenprotokollierungsmodelle werden in Hyperscale nicht unterstützt. Ein vollständiges Wiederherstellungsmodell ist erforderlich, um Hochverfügbarkeit und Zeitpunktwiederherstellung bereitzustellen. Allerdings bietet die Hyperscale-Protokollarchitektur im Vergleich zu anderen Dienstebenen für Azure SQL-Datenbank eine bessere Datenerfassungsrate.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Ermöglicht Hyperscale die Bereitstellung mehrerer Knoten für die parallele Erfassung von großen Datenmengen?

Nein. Hyperscale ist eine SMP-Architektur (symmetrisches Multiprocessing) und keine MPP-Architektur (Massively Parallel Processing, parallele Massenverarbeitung) oder Multimasterarchitektur. Sie können nur mehrere Replikate erstellen, um schreibgeschützte Workloads aufzuskalieren.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Was ist die älteste SQL Server-Version, die für die Migration zu Hyperscale unterstützt wird?

SQL Server 2005. Weitere Informationen finden Sie unter [Migrieren zu einer Einzeldatenbank oder Pooldatenbank](migrate-to-database-from-sql-server.md#migrate-to-a-single-database-or-a-pooled-database). Informationen zu Kompatibilitätsproblemen finden Sie unter [Beheben von Kompatibilitätsproblemen mit der Datenbankmigration](migrate-to-database-from-sql-server.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Unterstützt Hyperscale die Migration aus anderen Datenquellen wie Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 und anderen Datenbankplattformen?

Ja. [Azure Database Migration Service](../../dms/dms-overview.md) unterstützt viele Migrationsszenarien.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Fragen zur Geschäftskontinuität und Notfallwiederherstellung

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Welche SLAs werden für eine Hyperscale-Datenbank bereitgestellt?

Lesen Sie dazu [SLA für Azure SQL-Datenbank](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Zusätzliche sekundäre Computereplikate erhöhen die Verfügbarkeit bis zu 99,99 % bei einer Datenbank mit mindestens zwei Computereplikaten.

### <a name="are-the-database-backups-managed-for-me-by-azure-sql-database"></a>Werden Datenbanksicherungen von Azure SQL-Datenbank verwaltet?

Ja.

### <a name="how-often-are-the-database-backups-taken"></a>Wie oft werden Datenbanksicherungen durchgeführt?

Bei Hyperscale gibt es keine konventionellen vollständigen differenziellen Sicherungen oder Protokollsicherungen. Stattdessen gibt es reguläre Speichermomentaufnahmen von Datendateien. Das generierte Protokoll wird während des konfigurierten Aufbewahrungszeitraums einfach unverändert aufbewahrt, sodass die Wiederherstellung bis zu jedem beliebigen Zeitpunkt innerhalb dieses Zeitraums möglich ist.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Unterstützt Hyperscale Point-in-Time-Wiederherstellungen?

Ja.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Wie lange dauert die Recovery Point Objective (RPO)/Recovery Time Objective (RTO) bei einer Datenbankwiederherstellung in Hyperscale?

Der RPO-Wert beträgt 0 Minuten. Die meisten Wiederherstellungsvorgänge werden unabhängig von der Datenbankgröße innerhalb von 60 Minuten abgeschlossen. Die Wiederherstellungszeit kann für größere Datenbanken und dann länger sein, wenn für die Datenbank vor und bis zum Wiederherstellungszeitpunkt beträchtliche Schreibaktivitäten aufgetreten sind.

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Wirkt sich die Datenbanksicherung auf die Computeleistung auf primären oder sekundären Replikaten aus?

Nein. Sicherungen werden vom Speichersubsystem verwaltet und nutzen Speichermomentaufnahmen. Sie wirken sich nicht auf Benutzerworkloads aus.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Kann ich bei einer Hyperscale-Datenbank eine Geowiederherstellung durchführen?

Ja. Die Geowiederherstellung wird vollständig unterstützt. Anders als bei der Point-in-Time-Wiederherstellung erfordert die Geowiederherstellung einen zeitintensiven Vorgang. Weil Datendateien parallel kopiert werden, hängt die Dauer dieses Vorgangs also hauptsächlich von der Größe der größten Datei in der Datenbank und nicht von der Gesamtgröße der Datenbank ab. Die Geowiederherstellungszeit ist deutlich kürzer, wenn die Datenbank in der Azure-Region wiederhergestellt wird, die mit der Region der Quelldatenbank [gekoppelt](../../best-practices-availability-paired-regions.md) ist.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Kann ich für eine Hyperscale-Datenbank Georeplikation einrichten?

Derzeit leider nicht.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Kann ich eine Hyperscale-Datenbanksicherung auf meinem lokalen Server oder unter SQL Server auf einer VM wiederherstellen?

Nein. Das Speicherformat für Hyperscale-Datenbanken unterscheidet sich von jeder veröffentlichten SQL Server-Version. Dabei steuern Sie keine Sicherungen und können nicht darauf zugreifen. Wenn Sie Ihre Daten aus einer Hyperscale-Datenbank entfernen möchten, können Sie sie mithilfe von Datenverschiebungstechnologien extrahieren, z.B. Azure Data Factory, Azure Databricks, SSIS usw.

## <a name="cross-feature-questions"></a>Featureübergreifende Fragen

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Müssen bei der Migration zur Dienstebene „Hyperscale“ Einbußen hinsichtlich des Funktionsumfangs in Kauf genommen werden?

Ja. Einige Features von Azure SQL-Datenbank werden in Hyperscale nicht unterstützt, wie u. a. die langfristige Sicherungsaufbewahrung. Nach der Migration Ihrer Datenbanken zu Hyperscale funktionieren diese Features nicht mehr.

### <a name="will-polybase-work-with-hyperscale"></a>Funktioniert PolyBase bei Hyperscale?

Nein. PolyBase wird in Azure SQL-Datenbank nicht unterstützt.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Bietet Hyperscale Unterstützung für R und Python?

Derzeit leider nicht.

### <a name="are-compute-nodes-containerized"></a>Sind Computeknoten containerbasiert?

Nein. Hyperscale-Prozesse werden auf [Service Fabric](https://azure.microsoft.com/services/service-fabric/)-Knoten (VMs) und nicht in Containern ausgeführt.

## <a name="performance-questions"></a>Fragen zur Leistung

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Wie viel Schreibdurchsatz kann ich in einer Hyperscale-Datenbank pushen?

Das Durchsatzlimit für Transaktionsprotokolle ist auf 100 MB/Sek. für jede beliebige Hyperscale-Computegröße festgelegt. Die Möglichkeit zur Erzielung dieser Rate hängt von mehreren Faktoren ab, wie u.a. von Workloadtyp, Clientkonfiguration und einer ausreichenden Computekapazität im primären Computereplikat, um ein Protokoll mit dieser Rate generieren zu können.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Wie viele IOPS erhalte ich im größten Computevorgang?

Die IOPS- bzw. IO-Wartezeit variiert abhängig von den Workloadmustern. Wenn die Daten, auf die zugegriffen wird, im Computereplikat zwischengespeichert werden, ist die E/A-Leistung ähnlich wie mit lokalem SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Beeinträchtigen Sicherungen meinen Durchsatz?

Nein. Compute ist von der Speicherebene entkoppelt. Dadurch kommt es zu keiner Leistungsbeeinträchtigung bei Sicherungen.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Beeinträchtigt die Bereitstellung zusätzlicher Computereplikate meinen Durchsatz?

Weil der Speicher gemeinsam genutzt wird und es keine direkte physische Replikation zwischen primären und sekundären Computereplikaten gibt, wird der Durchsatz im primären Replikat durch das Hinzufügen von sekundären Replikaten nicht direkt beeinträchtigt. Allerdings können kontinuierliche intensiv schreibende Workloads auf dem primären Replikat gedrosselt werden, damit Protokolle auf sekundäre Replikate angewendet werden und Seitenserver mit der Verarbeitung aufholen können sowie eine schlechte Leseleistung in sekundären Replikaten verhindert werden kann.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Wie diagnostiziere und behebe ich Leistungsprobleme in einer Hyperscale-Datenbank?

Für die meisten Leistungsprobleme (insbesondere für solche, die nicht durch die Speicherleistung entstehen) gelten allgemeine SQL-Diagnose- und -Problembehandlungsschritte. Informationen zur Hyperscale-spezifischen Speicherdiagnose finden Sie unter [Diagnose zur Problembehandlung bei SQL-Hyperscale-Leistungsproblemen](hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Fragen zur Skalierbarkeit

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Wie lange würde es dauern, um ein Computereplikat hoch- und herunterzuskalieren?

Das Hoch- oder Herunterskalieren von Compute dauert in der Regel bis zu 2 Minuten, unabhängig von der Datengröße.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Wird die Datenbank während des Vorgangs zum zentralen Hoch- bzw. Herunterskalieren offline geschaltet?

Nein. Das zentrale Hoch- und Herunterskalieren erfolgt online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Wird die Verbindung bei Skalierungsvorgängen getrennt?

Zentrales Hoch- oder Herunterskalieren führt dazu, dass bestehende Verbindungen getrennt werden, wenn am Ende des Skalierungsvorgangs ein Failover auftritt. Das Hinzufügen von sekundären Replikaten führt nicht zu Verbindungsausfällen.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Erfolgt das zentrale Hoch- und Herunterskalieren der Computereplikate automatisch, oder wird dieser Vorgang durch den Endbenutzer ausgelöst?

Durch den Endbenutzer. Dies ist kein automatischer Vorgang.  

### <a name="does-the-size-of-my-tempdb-database-and-rbpex-cache-also-grow-as-the-compute-is-scaled-up"></a>Werden beim zentralen Hochskalieren des Computevorgangs auch meine `tempdb`-Datenbank und mein RBPEX-Cache vergrößert?

Ja. Die Größe der `tempdb`-Datenbank und die Größe des [RBPEX-Cache](service-tier-hyperscale.md#distributed-functions-architecture) auf Computeknoten werden automatisch hochskaliert, wenn die Anzahl der Kerne erhöht wird.

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kann ich mehrere primäre Computereplikate, z.B. ein Multimastersystem, bereitstellen, bei dem mehrere primäre Computeheads zu einem höheren Maß an Parallelität führen können?

Nein. Nur das primäre Computereplikat akzeptiert Lese-/Schreibanforderungen. Sekundäre Computereplikate akzeptieren nur schreibgeschützte Anforderungen.

## <a name="read-scale-out-questions"></a>Fragen zur horizontalen Leseskalierung

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Wie viele sekundäre Computereplikate kann ich bereitstellen?

Wir erstellen standardmäßig ein sekundäres Replikat für Hyperscale-Datenbanken. Wenn Sie die Anzahl von Replikaten anpassen möchten, können Sie dies über das [Azure-Portal](https://portal.azure.com) oder die [REST-API](/rest/api/sql/databases/createorupdate) erledigen.

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Wie kann ich eine Verbindung mit diesen sekundären Computereplikaten herstellen?

Sie können eine Verbindung mit diesen zusätzlichen schreibgeschützten Computereplikaten herstellen, indem Sie das Argument `ApplicationIntent` in Ihrer Verbindungszeichenfolge auf `ReadOnly` festlegen. Verbindungen, die mit `ReadOnly` markiert sind, werden automatisch an eines der zusätzlichen schreibgeschützten Computereplikate weitergeleitet. Ausführliche Informationen finden Sie unter [Verwenden von schreibgeschützten Replikaten zum Lesen schreibgeschützter Abfrageworkloads](read-scale-out.md).

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Wie kann ich überprüfen, ob ich eine Verbindung mit dem sekundären Computereplikat mithilfe von SSMS oder anderen Clienttools erfolgreich hergestellt habe?

Sie können die folgende T-SQL-Abfrage ausführen: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
Das Ergebnis lautet `READ_ONLY`, wenn Sie mit einem schreibgeschützten sekundären Replikat verbunden sind, und `READ_WRITE` bei einer Verbindung mit dem primären Replikat. Beachten Sie, dass der Datenbankkontext auf den Namen der Hyperscale-Datenbank (und nicht auf die `master`-Datenbank) festgelegt werden muss.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Kann ich einen dedizierten Endpunkt für ein Replikat mit horizontaler Leseskalierung erstellen?

Nein. Sie können eine Verbindung mit Replikaten mit horizontaler Leseskalierung nur herstellen, indem Sie `ApplicationIntent=ReadOnly` angeben.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Führt das System einen intelligenten Lastenausgleich für die Leseworkload durch?

Nein. Eine neue Verbindung mit schreibgeschütztem Inhalt wird an ein beliebiges Replikat mit horizontaler Leseskalierung umgeleitet.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Kann ich sekundäre Computereplikate unabhängig vom primären Replikat hoch- oder herunterskalieren?

Nein. Weil die sekundären Computereplikate auch als Failoverziele mit Hochverfügbarkeit verwendet werden, müssen sie dieselbe Konfiguration wie das primäre Replikat haben, um die erwartete Leistung nach einem Failover bereitzustellen.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Erhalte ich für meine primären Computereplikate und zusätzlichen sekundären Computereplikate unterschiedliche `tempdb`-Größen?

Nein. Ihre `tempdb`-Datenbank wird basierend auf der bereitgestellten Computegröße konfiguriert. Die sekundären Computereplikate haben dieselbe Größe wie die primären Computereplikate.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Kann ich Indizes und Ansichten in meinen sekundären Computereplikaten hinzufügen?

Nein. Hyperscale-Datenbanken verfügen über einen gemeinsamen Speicher. Dies bedeutet, dass alle Computereplikate dieselben Tabellen, Indizes und Ansichten anzeigen. Wenn Sie zusätzliche für Lesevorgänge optimierte Indizes im sekundären Replikat benötigen, müssen Sie sie im primären Replikat hinzufügen.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Wie lange dauert die Verzögerung zwischen dem primären und dem sekundären Computereplikat?

Die Datenlatenz ab dem Zeitpunkt, zu dem eine Transaktion auf dem primären Replikat committet wird, bis zu dem Zeitpunkt, zu dem sie auf einem sekundären Replikat lesbar ist, hängt von den aktuellen Werten für Protokollgenerierungsrate, Transaktionsgröße und Last auf dem Replikat sowie anderen Faktoren ab. Die typische Datenlatenz bei kleinen Transaktionen liegt im Bereich einiger Zehntel Millisekunden, es gibt jedoch keine obere Grenze für die Datenlatenz. Daten auf einem bestimmten sekundären Replikat sind immer transaktionsweise konsistent. Zu einem bestimmten Zeitpunkt kann die Datenlatenz für verschiedene sekundäre Replikate jedoch unterschiedlich sein. Workloads, die committete Daten sofort lesen müssen, sollten auf dem primären Replikat ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Dienstebene „Hyperscale“ finden Sie unter [Dienstebene „Hyperscale“ (Vorschau) für bis zu 100 TB](service-tier-hyperscale.md).