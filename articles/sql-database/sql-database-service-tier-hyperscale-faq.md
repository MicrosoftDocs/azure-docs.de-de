---
title: Azure SQL-Datenbank Hyperscale – FAQs | Microsoft-Dokumentation
description: In diesem Artikel werden häufig gestellte Fragen beantwortet, die Kunden zu Azure SQL-Datenbank in der Dienstebene „Hyperscale“ haben (allgemein als Hyperscale-Datenbank bezeichnet).
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 05/06/2019
ms.openlocfilehash: 38d9ad007b67756bdca0c6f98267aa16ba38ee9d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65791427"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>FAQs zu Azure SQL-Datenbank Hyperscale

Dieser Artikel enthält Antworten auf häufig gestellte Fragen für Kunden, die in Erwägung ziehen, eine Datenbank in Azure SQL-Datenbank mit der Dienstebene „Hyperscale“ zu verwenden. Diese wird häufig als Hyperscale-Datenbank bezeichnet. In diesem Artikel werden die Szenarien beschrieben, die Hyperscale unterstützt. Die Dienste mit den verschiedenen Features sind in der Regel mit SQL-Datenbank im Tarif „Hyperscale“ kompatibel.

- Diese FAQs richten sich an Leser, die einen allgemeinen Überblick über die Dienstebene „Hyperscale“ besitzen und auf der Suche nach Antworten auf ihre Fragen und Probleme sind.
- Diese FAQs sind nicht als Handbuch zu verstehen oder beantworten Fragen zur Verwendung von SQL-Datenbank Hyperscale. Für diese Zwecke wird stattdessen die Dokumentation [Hyperscale-Diensttarif (Vorschau) für bis zu 100 TB](sql-database-service-tier-hyperscale.md) empfohlen.

## <a name="general-questions"></a>Allgemeine Fragen

### <a name="what-is-a-hyperscale-database"></a>Was ist eine Hyperscale-Datenbank?

Eine Hyperscale-Datenbank ist eine Azure SQL-Datenbank mit der Dienstebene „Hyperscale“, die durch die Hyperscaletechnologie zur horizontalen Skalierung unterstützt wird. Eine Hyperscale-Datenbank unterstützt bis zu 100 TB Daten, zeichnet sich durch einen hohen Durchsatz sowie eine hohe Leistung aus und bietet eine schnelle Skalierung, um sich rasch an die Workloadanforderungen anzupassen. Eine Skalierung ist für die Anwendung transparent, d.h., die Konnektivität, die Abfrageverarbeitung etc. funktionieren wie bei jeder anderen SQL-­Datenbank-Instanz.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Welche Ressourcentypen und Kaufmodelle unterstützt Hyperscale?

Die Dienstebene „Hyperscale“ ist nur für Einzeldatenbanken verfügbar, bei denen das vCore-basierte Kaufmodell in Azure SQL-Datenbank zum Einsatz kommt.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Inwiefern unterscheidet sich die Dienstebene „Hyperscale“ von den Dienstebenen „Universell“ und „Unternehmenskritisch“?

Die auf virtuellen Kernen basierenden Dienstebenen unterscheiden sich in erster Linie im Hinblick auf Verfügbarkeit, Speichertyp und IOPS.

- Die Dienstebene „Universell“ eignet sich für die meisten Geschäftsworkloads und bietet eine ausgewogene Kombination aus Compute- und Speicheroptionen, wobei Latenzen bei E/A-Vorgängen oder Failoverzeiten eine untergeordnete Rolle spielen.
- Die Dienstebene „Hyperscale“ ist für sehr große Datenbankworkloads optimiert.
- Die Dienstebene „Unternehmenskritisch“ eignet sich für Geschäftsworkloads, bei denen Latenzen bei E/A-Vorgängen im Vordergrund stehen.

| | Ressourcentyp | Allgemeiner Zweck |  Hyperscale | Unternehmenskritisch |
|:---|:---:|:---:|:---:|:---:|:---:|
| **Am besten geeignet für** |Alle|  Die meisten geschäftlichen Workloads. Bietet budgetorientierte ausgewogene Compute- und Speicheroptionen. | Datenanwendungen mit hohem Datenkapazitätsbedarf und der Möglichkeit, Speicher automatisch zu skalieren und Computeressourcen nahtlos zu skalieren. | OLTP-Anwendungen mit hoher Transaktionsrate und den geringsten Latenzen bei E/A-Vorgängen. Bietet höchste Resilienz gegenüber Ausfällen durch mehrere isolierte Replikate.|
|  **Ressourcentyp** ||Einzeldatenbank/Pool für elastische Datenbanken/verwaltete Instanz | Einzeldatenbank | Einzeldatenbank/Pool für elastische Datenbanken/verwaltete Instanz |
| **Computegröße**|Einzeldatenbank/Pool für elastische Datenbanken* | 1 bis 80 virtuelle Kerne | 1 bis 80 virtuelle Kerne* | 1 bis 80 virtuelle Kerne |
| |Verwaltete Instanz | 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne | – | 8, 16, 24, 32, 40, 64, 80 virtuelle Kerne |
| **Speichertyp** | Alle |Storage Premium (remote, pro Instanz) | Entkoppelter Speicher mit lokalem SSD-Cache (pro Instanz) | Äußerst schneller lokaler SSD-Speicher (pro Instanz) |
| **Speichergröße** | Einzeldatenbank/Pool für elastische Datenbanken | 5 GB – 4 TB | Bis zu 100 TB | 5 GB – 4 TB |
| | Verwaltete Instanz  | 32 GB – 8 TB | – | 32 GB – 4 TB |
| **E/A-Durchsatz** | Einzeldatenbank** | 500 IOPS pro V-Kern mit maximal 7.000 IOPS | Noch unbekannt | 5\.000 IOPS mit maximal 200.000 IOPS|
| | Verwaltete Instanz | Hängt von der Größe der Datei ab | – | Verwaltete Instanz: Hängt von der Größe der Datei ab|
|**Verfügbarkeit**|Alle|1 Replikat, keine Replikate mit Leseskalierung, kein lokaler Cache | Mehrere Replikate, bis zu 15 Replikate mit Leseskalierung, teilweise lokaler Cache | 3 Replikate, 1 Replikat mit Leseskalierung, zonenredundante Hochverfügbarkeit, vollständiger lokaler Cache |
|**Sicherungen**|Alle|RA-GRS, 7 - 35 Tage (standardmäßig 7 Tage)| RA-GRS, 7-35 Tage (Standard: 7 Tage), konstante Zeitpunktwiederherstellung (Point-in-Time Recovery, PITR) | RA-GRS, 7 - 35 Tage (standardmäßig 7 Tage) |

\* Pools für elastische Datenbanken werden in der Dienstebene „Hyperscale“ nicht unterstützt.

### <a name="who-should-use-the-hyperscale-service-tier"></a>Wer sollte die Dienstebene „Hyperscale“ verwenden?

Die Dienstebene „Hyperscale“ ist hauptsächlich für Kunden konzipiert, die große lokale SQL Server-Datenbanken verwenden und ihre Anwendungen durch eine Migration in die Cloud modernisieren möchten, oder für Kunden, die bereits Azure SQL-Datenbank verwenden und das Potenzial zum Datenbankwachstum im umfassenden Maß ausschöpfen möchten. Außerdem eignet sich Hyperscale für Kunden, die eine hohe Leistung und Skalierbarkeit wünschen. Mit Hyperscale erhalten Sie Folgendes:

- Unterstützung für eine Datenbankgröße von bis zu 100 TB
- Schnelle Datenbanksicherungen unabhängig von der Datenbankgröße (Sicherungen basieren auf Dateimomentaufnahmen)
- Schnelle Datenbankwiederherstellungen unabhängig von der Datenbankgröße (Wiederherstellungen basierend auf Dateimomentaufnahmen)
- Höhere Protokolldurchsatzergebnisse in kurzer Transaktionscommitzeit unabhängig von der Datenbankgröße
- Horizontale Leseskalierung auf mindestens einen schreibgeschützten Knoten zur Auslagerung von Leseworkloads und für unmittelbar betriebsbereite Standbyserver
- Schnelles zentrales Hochskalieren der Computeleistung in konstanter Zeit, um eine höhere Leistung bei der Verarbeitung umfangreicher Workloads bereitzustellen, und anschließend zentrales Herunterskalieren in konstanter Zeit. Dies ist mit dem zentralen Hoch- oder Herunterskalieren zwischen P6 und P11 beispielsweise vergleichbar, jedoch ist diese Art von Skalierung schneller, da dieser Vorgang nicht von der Datengröße abhängig ist.

### <a name="what-regions-currently-support-hyperscale"></a>Welche Regionen unterstützen derzeit Hyperscale?

Die Dienstebene „Hyperscale“ für Azure SQL-Datenbank ist derzeit in den unter [Hyperscale für Azure SQL-Datenbank – Übersicht](sql-database-service-tier-hyperscale.md#regions) aufgeführten Regionen verfügbar.

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Können mehrere Hyperscale-Datenbanken pro logischen Server erstellt werden?

Ja. Weitere Informationen und Beschränkungen hinsichtlich der Anzahl von Hyperscale-Datenbanken pro logischem Server finden Sie unter [Ressourceneinschränkungen in SQL-Datenbank für Einzeldatenbanken und Pooldatenbanken auf einem logischen Server](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristic-of-a-hyperscale-database"></a>Welche Leistungsmerkmale weist eine Hyperscale-Datenbank auf?

Die Hyperscale-Architektur von SQL-Datenbank bietet eine hohe Leistung und einen hohen Durchsatz und unterstützt außerdem große Datenbankgrößen. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Was beinhaltet die Skalierbarkeit einer Hyperscale-Datenbank?

SQL-Datenbank Hyperscale bietet schnelle Skalierbarkeit basierend auf Ihrem Workloadbedarf.

- **Zentrales Hoch-/Herunterskalieren**

  Bei Hyperscale können Sie die primäre Computegröße im Hinblick auf Ressourcen wie CPU und Arbeitsspeicher in konstanter Zeit zentral hochskalieren und anschließend zentral herunterskalieren. Da der Speicher gemeinsam genutzt wird, ist das zentrale Hoch- und Herunterskalieren kein Vorgang, der von der Datengröße abhängig ist.  
- **Horizontal herunter-/hochskalieren**

  Mit Hyperscale erhalten Sie auch die Möglichkeit, mindestens einen zusätzlichen Computeknoten bereitzustellen, mit dem Sie Ihre Leseanforderungen verarbeiten können. Dies bedeutet, dass Sie diese zusätzlichen Computeknoten als schreibgeschützte Knoten verwenden können, um Ihre Leseworkload vom primären Computeknoten auszulagern. Diese Knoten sind nicht nur schreibgeschützt, sondern dienen außerdem als unmittelbar betriebsbereite Standbyserver im Falle eines Failovers vom primären Knoten.

  Die Bereitstellung der einzelnen zusätzlichen Computeknoten kann in konstanter Zeit erfolgen und stellt einen Onlinevorgang dar. Sie können eine Verbindung mit diesen zusätzlichen schreibgeschützten Computeknoten herstellen, indem Sie das `ApplicationIntent`-Argument in Ihrer Verbindungszeichenfolge auf `read_only` festlegen. Verbindungen, die mit `read-only` markiert sind, werden automatisch an einen der zusätzlichen schreibgeschützten Computeknoten weitergeleitet.

## <a name="deep-dive-questions"></a>Vertiefende Fragen

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Können Hyperscale- und Einzeldatenbanken in einem einzelnen logischen Server kombiniert werden?

Ja, das ist möglich.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Müssen für Hyperscale Änderungen am Anwendungsprogrammiermodell vorgenommen werden?

Nein, Ihr Anwendungsprogrammiermodell muss nicht geändert werden. Sie verwenden Ihre Verbindungszeichenfolge wie gewohnt und die anderen regulären Modi, um mit Azure SQL-Datenbank zu interagieren.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Welche Transaktionsisolationsstufen sind standardmäßig in SQL-Datenbank Hyperscale vorhanden?

Für den primären Knoten gilt die RCSI-Transaktionsisolationsstufe (Read Committed Snapshot Isolation). Für den sekundären Knoten mit Leseskalierung gilt die Momentaufnahme-Isolationsstufe.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Können lokale oder IaaS-basierte SQL Server-Lizenzen zu SQL-Datenbank Hyperscale migriert werden?

Ja, der [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) ist für Hyperscale verfügbar. Jede SQL Server Standard-Kern kann 1 virtuellen Hyperscale-Kern zugeordnet werden. Jede SQL Server Enterprise-Kern kann 4 virtuellen Hyperscale-Kernen zugeordnet werden. Sie benötigen keine SQL-Lizenz für sekundäre Replikate. Der Preis für den Azure-Hybridvorteil wird automatisch auf (sekundäre) Replikate mit Leseskalierung angewendet.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Für welche Art von Workloads ist SQL-Datenbank Hyperscale konzipiert?

SQL-Datenbank Hyperscale unterstützt alle SQL Server-Workloads, wurde aber in erster Linie für OLTP optimiert. Zudem können Sie Hybrid- (HTAP-) und Analyseworkloads (Data Mart) migrieren.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Welche Kriterien sprechen für die Verwendung von Azure SQL Data Warehouse und SQL-Datenbank Hyperscale?

Wenn Sie zurzeit interaktive Analyseabfragen mit SQL Server als Data Warehouse ausführen, stellt SQL-Datenbank Hyperscale eine hervorragende Option dar, da Sie relativ kleine Data Warehouses (z.B. mit TB im einstelligen bis hin zum zweistelligen Bereich) zu geringeren Kosten hosten und Ihre Data Warehouse-Workload zu SQL-Datenbank Hyperscale migrieren können, ohne Änderungen am T-SQL-Code vornehmen zu müssen.

Wenn Sie Datenanalysen im großen Umfang mit komplexen Abfragen ausführen und Parallel Data Warehouse (PDW), Teradata oder andere Data Warehouses mit MPP-Design (Massively Parallel Processing) einsetzen, ist SQL Data Warehouse möglicherweise die beste Wahl.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Fragen zur Computeleistung von SQL-Datenbank Hyperscale

### <a name="can-i-pause-my-compute-at-any-time"></a>Kann ich die Computebereitstellung jederzeit anhalten?

Derzeit leider nicht. Sie können Ihre Computeleistung und die Anzahl von Replikaten jedoch herunterskalieren, um außerhalb von Spitzenzeiten Kosten zu senken.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Kann ich Computeressourcen mit zusätzlichem RAM für speicherintensive Workloads bereitstellen?

Nein. Um mehr RAM zu erhalten, müssen Sie ein Upgrade auf eine höhere Computegröße durchführen. Weitere Informationen finden Sie unter [Dienstebene „Hyperscale“ (Vorschau)](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier).

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Kann ich mehrere Computeknoten unterschiedlicher Größe bereitstellen?

Nein.

### <a name="how-many-read-scale-replicas-are-supported"></a>Wie viele Replikate mit Leseskalierung werden unterstützt?

Die Hyperscale-Datenbanken werden standardmäßig mit einem Replikat mit Leseskalierung (insgesamt zwei Replikaten) erstellt. Sie können die Anzahl der schreibgeschützten Replikate über das [Azure-Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) oder die [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) auf einen Wert zwischen 0 und 4 anpassen.

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Müssen zur Erzielung von Hochverfügbarkeit zusätzliche Computeknoten bereitgestellt werden?

Bei Hyperscale-Datenbanken wird Resilienz auf Speicherebene bereitgestellt. Sie benötigen nur ein Replikat, um Resilienz bereitzustellen. Wenn das Computereplikat ausfällt, wird automatisch und ohne Datenverluste ein neues Replikat erstellt.

Ist allerdings nur ein Replikat vorhanden, kann das Erstellen des lokalen Caches im neuen Replikat nach einem Failover einige Zeit in Anspruch nehmen. Während der Cacheneuerstellung ruft die Datenbank Daten direkt von den Seitenservern ab, was zu Einbußen hinsichtlich der IOPS-Anzahl und der Abfrageleistung führt.

Bei unternehmenskritischen Apps, die Hochverfügbarkeit erfordern, sollten Sie mindestens 2 Computeknoten bereitstellen, einschließlich des primären Computeknotens (Standard). So steht im Falle eines Failovers ein unmittelbar betriebsbereiter Standbyserver zur Verfügung.

## <a name="data-size-and-storage-questions"></a>Fragen zur Datengröße und Speicherkapazität

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Welche maximale Datenbankgröße wird in SQL-Datenbank Hyperscale unterstützt?

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Wie groß ist das Transaktionsprotokoll bei Hyperscale?

Das Transaktionsprotokoll bei Hyperscale ist praktisch unendlich. Sie müssen sich keine Gedanken darum machen, dass der Protokollspeicherplatz bei einem System mit hohem Protokolldurchsatz ausgeschöpft sein könnte. Allerdings wird bei kontinuierlichen intensiven Workloads möglicherweise die Protokollgenerierungsrate gedrosselt. Die maximale dauerhafte Protokollgenerierungsrate liegt bei etwa 100 MB pro Sekunde.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Kann die temp-Datenbank mit zunehmendem Wachstum skaliert werden?

Ihre `tempdb`-Datenbank befindet sich im lokalen SSD-Speicher und ist basierend auf der Computegröße, die Sie bereitstellen, konfiguriert. Die `tempdb`-Datenbank ist für die Bereitstellung von maximaler Leistung optimiert. Die Größe von `tempdb` ist nicht konfigurierbar und wird vom Speichersubsystem für Sie verwaltet.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Wird die Datenbankgröße automatisch skaliert oder muss die Größe der Datendateien selbst verwaltet werden?

Die Datenbankgröße nimmt automatisch zu, je mehr Daten Sie einfügen bzw. erfassen.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Was ist die kleinste Datenbankgröße, die SQL-Datenbank Hyperscale unterstützt?

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>In welchen Schritten wird die Datenbankgröße erhöht?

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Verfügt SQL-Datenbank Hyperscale über einen lokalen oder Remotespeicher?

In Hyperscale werden Datendateien im Azure-Standardspeicher gespeichert. Daten werden häufig im lokalen SSD-Speicher auf Computern in der Nähe der Computeknoten zwischengespeichert. Darüber hinaus verfügen Computeknoten über einen Cache auf dem lokalen SSD und In-Memory (Pufferpool etc.), um die Häufigkeit des Datenabrufs über Remoteknoten zu reduzieren.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Können bei Hyperscale Dateien oder Dateigruppen verwaltet oder definiert werden?

Nein
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kann für eine Datenbank eine feste Obergrenze für das Datenwachstum festgelegt werden?

Nein

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Wie sind Datendateien bei SQL-Datenbank Hyperscale angeordnet?

Die Datendateien werden von Seitenservern gesteuert. Wenn die Datengröße zunimmt, werden Datendateien und zugehörige Seitenserverknoten hinzugefügt.

### <a name="is-database-shrink-supported"></a>Wird die Verkleinerung von Datenbanken unterstützt?

Nein

### <a name="is-database-compression-supported"></a>Wird Datenbankkomprimierung unterstützt?

Ja

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Werden Daten in einer großen Tabelle auf mehrere Datendateien verteilt?

Ja. Die Datenseiten einer bestimmten Tabelle können auf mehrere Datendateien, die Teil der gleichen Dateigruppe sind, verteilt werden. Bei SQL Server kommt eine [proportionale Füllstrategie](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) zum Einsatz, um Daten auf Datendateien zu verteilen.

## <a name="data-migration-questions"></a>Fragen zur Datenmigration

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Können vorhandene Azure SQL-Datenbanken zur Dienstebene „Hyperscale“ migriert werden?

Ja. Sie können Ihre vorhandenen Azure SQL-Datenbank-Instanzen zu Hyperscale migrieren. Dies erfolgt im Rahmen einer unidirektionalen Migration. Datenbanken können nicht von Hyperscale zu einer anderen Dienstebene migriert werden. Es wird empfohlen, eine Kopie Ihrer Produktionsdatenbanken zu erstellen und für Proof of Concepts (POCs) eine Migration zu Hyperscale durchzuführen.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Können Hyperscale-Datenbanken zu anderen Versionen migriert werden?

Nein. Eine Hyperscale-Datenbank kann derzeit nicht zu einer anderen Dienstebene migriert werden.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Müssen bei der Migration zur Dienstebene „Hyperscale“ Einbußen hinsichtlich des Funktionsumfangs in Kauf genommen werden?

Ja. Einige der Features von Azure SQL-Datenbank werden in Hyperscale bisher nicht unterstützt, wie u. a. Sicherungen zur Langzeitaufbewahrung. Nach der Migration Ihrer Datenbanken zu Hyperscale funktionieren diese Features nicht mehr.  Wir gehen davon aus, dass diese Einschränkungen nur temporär bestehen.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Können lokale SQL Server-Datenbanken oder virtuelle Computer mit SQL Server-Datenbank zu Hyperscale migriert werden?

Ja. Sie können für die Migration zu Hyperscale alle vorhandenen Migrationstechnologien verwenden, einschließlich BACPAC, Transaktionsreplikation, logisches Laden von Daten. Weitere Informationen finden Sie auch unter [Was ist Azure Database Migration Service?](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Wie lange ist die Ausfallzeit bei der Migration von einer lokalen Umgebung oder Umgebung eines virtuellen Computers zu Hyperscale, und wie kann diese minimiert werden?

Die Ausfallzeit dauert genauso lange wie bei der Migration Ihrer Datenbanken zu einer Einzeldatenbank in Azure SQL-Datenbank. Mithilfe von [Transaktionsreplikationen](replication-to-sql-database.md#data-migration-scenario
) können Sie die Ausfallzeit bei der Migration von Datenbanken mit einer Größe von mehreren TB minimieren. Bei sehr großen Datenbanken (über 10 TB) empfiehlt es sich eventuell, Daten mit ADF, Spark oder anderen Datenverschiebungstechnologien zu migrieren.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Wie viel Zeit würde es brauchen, um eine bestimmte Datenmenge zu SQL-Datenbank Hyperscale zu migrieren?

Hyperscale kann pro Sekunde 100 MB neue/geänderte Daten verarbeiten.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Können Daten aus Blob Storage gelesen und schnell geladen werden (wie PolyBase und SQL Data Warehouse)?

Sie können Daten aus Azure Storage lesen und in eine Hyperscale-Datenbank laden (genau wie bei einer gewöhnlichen Einzeldatenbank). PolyBase wird in Azure SQL-Datenbank derzeit nicht unterstützt. Mit [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) oder der Ausführung eines Spark-Auftrags in [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) mit dem [Spark-Connector für SQL](sql-database-spark-connector.md) können Sie PolyBase-Abfragen ausführen. Der Spark-Connector für SQL unterstützt Importe mit BULK INSERT.

Einfache Wiederherstellungen oder Massenprotokollierungsmodelle werden in Hyperscale nicht unterstützt. Für die Bereitstellung von Hochverfügbarkeit ist das vollständige Wiederherstellungsmodell erforderlich. Hyperscale bietet aufgrund der neuen Protokollarchitektur jedoch eine bessere Datenerfassungsrate im Vergleich zu einer einzelnen Azure SQL-Datenbank.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Ermöglicht SQL-Datenbank Hyperscale die Bereitstellung mehrerer Knoten für die Erfassung von großen Datenmengen?

Nein. Bei SQL-Datenbank Hyperscale handelt es sich um eine SMP-Architektur, nicht um asymmetrisches Multiprocessing oder eine Multimasterarchitektur. Sie können nur mehrere Replikate erstellen, um schreibgeschützte Workloads horizontal hochzuskalieren.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Von welcher ältesten SQL Server-Version aus unterstützt SQL-Datenbank Hyperscale Migrationen?

SQL Server 2005. Weitere Informationen finden Sie unter [Migrieren zu einer Einzeldatenbank oder Pooldatenbank](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Informationen zu Kompatibilitätsproblemen finden Sie unter [Beheben von Kompatibilitätsproblemen mit der Datenbankmigration](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Unterstützt SQL-Datenbank Hyperscale die Migration aus anderen Datenquellen wie Aurora, MySQL, Oracle, DB2 und anderen Datenbankplattformen?

Ja. Für andere Datenquellen als SQL Server ist eine logische Migration erforderlich. Hierfür können Sie [Azure Database Migration Service](../dms/dms-overview.md) verwenden.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Fragen zur Geschäftskontinuität und Notfallwiederherstellung

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Welche SLAs werden für eine Hyperscale-Datenbank bereitgestellt?

Mit dem standardmäßig verwendeten primären und einem lesbaren sekundären Replikat gilt laut SLA eine Verfügbarkeit von 99,95 %.  Mit weiteren Replikaten steigt dieser Wert auf 99,99 %.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Werden Datenbanksicherungen vom Azure SQL-Datenbank-Dienst verwaltet?

Ja

### <a name="how-often-are-the-database-backups-taken"></a>Wie oft werden Datenbanksicherungen durchgeführt?

Bei SQL-Datenbank Hyperscale gibt es keine konventionellen vollständigen differenziellen Sicherungen oder Protokollsicherungen. Stattdessen werden regelmäßig Momentaufnahmen von den Datendateien und Protokollen generiert und einfach für die konfigurierte Beibehaltungsdauer beibehalten bzw. für Sie zur Verfügung gestellt.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>Unterstützt SQL-Datenbank Hyperscale Point-in-Time-Wiederherstellungen?

Ja

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Was beinhalten die Recovery Point Objective (RPO) und Recovery Time Objective (RTO) mit Sicherung und Wiederherstellung in SQL-Datenbank Hyperscale?

Die RPO beträgt 0 Minuten. Das RTO-Ziel beträgt unabhängig von der Größe der Datenbank weniger als 10 Minuten. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Wirken sich Sicherungen von großen Datenbanken auf die Computeleistung auf meinem primären Knoten aus?

Nein. Sicherungen werden vom Speichersubsystem verwaltet und nutzen Dateimomentaufnahmen. Sie wirken sich nicht auf Benutzerworkloads auf dem primären Knoten aus.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Kann bei einer SQL-Datenbank-Instanz mit Hyperscale eine Geowiederherstellung durchgeführt werden?

Ja.  Die Geowiederherstellung wird vollständig unterstützt.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Kann die Georeplikation bei SQL-Datenbank Hyperscale eingerichtet werden?

Derzeit leider nicht.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Werden meinen sekundären Computeknoten mit SQL-Datenbank Hyperscale georepliziert?

Derzeit leider nicht.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Können Datenbanksicherungen mit SQL-Datenbank Hyperscale durchgeführt und diese auf einem lokalen Server oder auf SQL Server auf einer VM wiederhergestellt werden?

Nein. Das Speicherformat für Hyperscale-Datenbanken unterscheidet sich vom konventionellen SQL Server. Dabei steuern Sie keine Sicherungen und haben keinen Zugriff auf diese. Um Ihre Daten aus SQL-Datenbank Hyperscale zu extrahieren, verwenden Sie entweder den Exportdienst oder Skripts mit BCP.

## <a name="cross-feature-questions"></a>Fragen zu verschiedenen Features

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Müssen bei der Migration zur Dienstebene „Hyperscale“ Einbußen hinsichtlich des Funktionsumfangs in Kauf genommen werden?

Ja. Einige der Features von Azure SQL-Datenbank werden in Hyperscale nicht unterstützt, wie u. a. Sicherungen zur Langzeitaufbewahrung. Nach der Migration Ihrer Datenbanken zu Hyperscale funktionieren diese Features nicht mehr.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Funktioniert PolyBase mit SQL-Datenbank Hyperscale?

Nein. PolyBase wird in Azure SQL-Datenbank nicht unterstützt.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Unterstützen die Computeknoten R und Python?

Nein. R und Python werden in Azure SQL-Datenbank nicht unterstützt.

### <a name="are-the-compute-nodes-containerized"></a>Sind die Computeknoten in Containern gepackt?

Nein. Die Datenbank befindet sich auf einer Compute-VM und nicht in einem Container.

## <a name="performance-questions"></a>Fragen zur Leistung

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Wie viel Durchsatz kann ich mithilfe von Push auf die größte SQL-Datenbank Hyperscale-Instanz übertragen?

Es wurden konsistent pro Sekunde 100 MB geänderte Daten übertragen (Transaktionsprotokoll zur Datengenerierung)

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Wie viel IOPS kann ich auf die größte SQL-Datenbank Hyperscale-Instanz übertragen?

Die IOPS- bzw. IO-Wartezeit variiert abhängig von den Workloadmustern.  Wenn die Daten, auf die zugegriffen werden muss, lokal beim Computecache vorliegen, werden die gleichen IO-Muster wie für das lokale SSD verwendet.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Beeinträchtigen Sicherungen meinen Durchsatz?

Nein. Die Computeknoten sind von der Speicherschicht entkoppelt, um eine Beeinträchtigung der Computeknoten zu verhindern.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Beeinträchtigt die Bereitstellung zusätzlicher Computeknoten den Durchsatz?

Da der Speicher gemeinsam genutzt wird und es keine direkte physische Replikation zwischen primären und sekundären Computeknoten gibt, wird der Durchsatz auf dem primären Knoten technisch gesehen durch das Hinzufügen von Knoten mit Leseskalierung nicht beeinträchtigt. Allerdings können kontinuierliche intensive Workloads gedrosselt werden, damit Protokolle auf sekundäre Knoten angewendet werden und Seitenserver mit der Verarbeitung aufholen können und um eine schlechte Leseleistung auf sekundären Knoten zu verhindern.

## <a name="scalability-questions"></a>Fragen zur Skalierbarkeit

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Wie lange würde es dauern, um einen Computeknoten zentral hoch- und herunterzuskalieren?

Das Hoch- bzw. Herunterskalieren der Computeressourcen sollte unabhängig von der Datengröße 5-10 Minuten dauern.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Wird die Datenbank während des Vorgangs zum zentralen Hoch- bzw. Herunterskalieren offline geschaltet?

Nein. Das zentrale Hoch- und Herunterskalieren erfolgt online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Wird die Verbindung bei Skalierungsvorgängen getrennt?

Das zentrale Hoch- und Herunterskalieren führt dazu, dass bestehende Verbindungen getrennt werden, sobald ein Failover zum Computeknoten mit der Zielgröße erfolgt. Das Hinzufügen von Lesereplikaten führt nicht zu Verbindungsausfällen.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Erfolgt das zentrale Hoch- und Herunterskalieren der Computeknoten automatisch oder wird dieser Vorgang durch den Endbenutzer ausgelöst?

Durch den Endbenutzer. Dies ist kein automatischer Vorgang.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Wird meine `tempb`-Datenbank auch beim zentralen Hochskalieren der Computeressourcen skaliert?

Ja. Die temp-Datenbank wird automatisch zentral hochskaliert, je größer der Computebedarf wird.  

### <a name="can-i-provision-multiple-primary-computes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Können mehrere primäre Computeknoten wie ein Multimastersystem bereitgestellt werden, bei dem mehrere primäre Computeheads zu einem höheren Maß an Parallelität führen können?

Nein. Nur der primäre Computeknoten akzeptiert Lese-/Schreibanforderungen. Sekundäre Computeknoten akzeptieren nur schreibgeschützte Anforderungen.

## <a name="read-scale-questions"></a>Fragen zur Leseskalierung

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Wie viele sekundäre Computeknoten können bereitgestellt werden?

Es werden standardmäßig zwei Replikate für Hyperscale-Datenbanken erstellt. Sie können die Anzahl der Replikate über das [Azure-Portal](https://portal.azure.com) anpassen.

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Wie wird eine Verbindung mit diesen sekundären Computeknoten hergestellt?

Sie können eine Verbindung mit diesen zusätzlichen schreibgeschützten Computeknoten herstellen, indem Sie das `ApplicationIntent`-Argument in Ihrer Verbindungszeichenfolge auf `read_only` festlegen. Verbindungen, die mit `read-only` markiert sind, werden automatisch an einen der zusätzlichen schreibgeschützten Computeknoten weitergeleitet.  

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Kann ein dedizierter Endpunkt für das Replikat mit Leseskalierung erstellt werden?

Nein. Sie können eine Verbindung mit Replikaten mit Leseskalierung nur herstellen, indem Sie `ApplicationIntent=ReadOnly` angeben.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Führt das System einen intelligenten Lastenausgleich für die Leseworkload durch?

Nein. Die schreibgeschützte Workload wird an ein zufälliges Replikat mit Leseskalierung umgeleitet.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Können sekundäre Computeknoten unabhängig vom primären Computeknoten zentral hoch- oder herunterskaliert werden?

Nein. Die sekundären Computeknoten werden auch für die Hochverfügbarkeit verwendet. Daher müssen diese für den Fall eines Failovers die gleiche Konfiguration wie die primären Computeknoten aufweisen.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Gelten für primäre Computeknoten und zusätzliche sekundäre Computeknoten unterschiedliche temp-Datenbankgrößen?

Nein. Ihre `tempdb`-Datenbank ist basierend auf der bereitgestellten Computegröße konfiguriert. Die sekundären Computeknoten weisen die gleiche Größe wie die primären Computeknoten auf.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Können Indizes und Ansichten für die sekundären Computeknoten hinzugefügt werden?

Nein. Hyperscale-Datenbanken verfügen über einen gemeinsame Speicher, was bedeutet, dass alle Computeknoten die gleichen Tabellen, Indizes und Ansichten anzeigen. Wenn Sie zusätzliche für Lesevorgänge optimierte Indizes auf sekundären Knoten wünschen, müssen Sie sie zuerst auf dem primären Knoten hinzufügen.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Wie lange dauert die Verzögerung zwischen dem primären und dem sekundären Computeknoten?

Ab dem Zeitpunkt, an dem ein Commit für eine Transaktion auf dem primären Knoten ausgeführt wurde, kann diese abhängig von der Protokollgenerierungsrate sofort oder im unteren Millisekundenbereich ausgeführt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Dienstebene „Hyperscale“ finden Sie unter [Dienstebene „Hyperscale“ (Vorschau) für bis zu 100 TB](sql-database-service-tier-hyperscale.md).
