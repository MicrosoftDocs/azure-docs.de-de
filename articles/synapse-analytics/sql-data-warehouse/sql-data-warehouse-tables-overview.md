---
title: Entwerfen von Tabellen
description: Einführung in das Entwerfen von Tabellen mithilfe eines dedizierten SQL-Pools in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/15/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ec62724b7aedbad4111a4882dd89f86d116b2a96
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448053"
---
# <a name="design-tables-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Entwerfen von Tabellen mithilfe eines dedizierten SQL-Pools in Azure Synapse Analytics

Dieser Artikel enthält wichtige grundlegende Konzepte für das Entwerfen von Tabellen in einem dedizierten SQL-Pool.

## <a name="determine-table-category"></a>Bestimmen der Tabellenkategorie

In einem [Sternschema](https://en.wikipedia.org/wiki/Star_schema) werden Daten in Fakten-und Dimensionstabellen organisiert. Einige Tabellen werden für die Integration oder das Staging von Daten verwendet, bevor sie in eine Fakten- oder Dimensionstabelle verschoben werden. Wenn Sie eine Tabelle entwerfen, entscheiden Sie, ob die Tabellendaten in einer Faktentabelle, eine Dimensionstabelle oder eine Integrationstabelle gehören. Diese Entscheidung bestimmt die Tabellenstruktur und die Verteilung.

- **Faktentabellen** enthalten quantitative Daten, die häufig in einem transaktionalen System generiert und dann in den dedizierten SQL-Pool geladen werden. Beispielsweise generiert ein Einzelhandelsunternehmen täglich Verkaufstransaktionen und lädt dann die Daten zur Analyse in eine Faktentabelle in einem dedizierten SQL-Pool.

- **Dimensionstabellen** enthalten Attributdaten, die sich ändern können, es in der Regel jedoch nur selten tun. Beispielsweise werden der Name und die Adresse eines Kunden in einer Dimensionstabelle gespeichert und nur aktualisiert, wenn sich das Profil des Kunden ändert. Damit eine Faktentabelle nicht zu groß wird, kann darauf verzichtet werden, den Namen und die Adresse des Kunden in jede Zeile der Faktentabelle aufzunehmen. Stattdessen kann eine Kunden-ID von der Faktentabelle und der Dimensionstabelle gemeinsam genutzt werden. Die beiden Tabellen können mithilfe einer Abfrage verknüpft werden, um das Profil eines Kunden den Transaktionen zuzuordnen.

- **Integrationstabellen** werden für das Integrieren oder Staging von Daten verwendet. Sie können eine Integrationstabelle als normale Tabelle, externe Tabelle oder temporäre Tabelle erstellen. Sie können z. B. Daten in eine Stagingtabelle laden, Transformationen für die Daten in der Stagingumgebung durchführen und die Daten dann in eine Produktionstabelle einfügen.

## <a name="schema-and-table-names"></a>Schema und Tabellennamen

Schemas eignen sich gut für das Gruppieren von Tabellen, die auf ähnliche Weise verwendet werden.  Wenn Sie mehrere Datenbanken aus einer lokalen Lösung zu einem dedizierten SQL-Pool migrieren, migrieren Sie am besten alle Fakten-, Dimensions- und Integrationstabellen in ein Schema in einem dedizierten SQL-Pool.

Sie können z. B. alle Tabellen im dedizierten SQL-Poolbeispiel [WideWorldImportersDW](/sql/sample/world-wide-importers/database-catalog-wwi-olap?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) in einem Schema namens „wwi“ speichern. Mit dem folgenden Code wird ein [benutzerdefiniertes Schema](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) mit dem Namen „wwi“ erstellt.

```sql
CREATE SCHEMA wwi;
```

Um die Organisation der Tabellen im dedizierten SQL-Pool anzuzeigen, können Sie „fact“, „dim“ und „int“ als Präfixe für die Tabellennamen verwenden. Die folgende Tabelle zeigt einige der Schema- und Tabellennamen für „WideWorldImportersDW“.  

| WideWorldImportersDW table  | Tabellentyp | Dedizierter SQL-Pool |
|:-----|:-----|:------|:-----|
| City | Dimension | wwi.DimCity |
| Order | Fakt | wwi.FactOrder |

## <a name="table-persistence"></a>Tabellenpersistenz

Tabellen speichern Daten dauerhaft in Azure Storage oder vorübergehend in Azure Storage oder in einem Datenspeicher außerhalb des dedizierten SQL-Pools.

### <a name="regular-table"></a>Normale Tabelle

In einer normalen Tabelle werden Daten in Azure Storage als Teil des dedizierten SQL-Pools gespeichert. Die Tabelle und die Daten werden unabhängig davon beibehalten, ob eine Sitzung geöffnet ist.  Im folgenden Beispiel wird eine normale Tabelle mit zwei Spalten erstellt.

```sql
CREATE TABLE MyTable (col1 int, col2 int );  
```

### <a name="temporary-table"></a>Temporäre Tabelle

Eine temporäre Tabelle ist nur für die Dauer der Sitzung vorhanden. Sie können eine temporäre Tabelle verwenden, um zu verhindern, dass andere Benutzer temporäre Ergebnisse sehen, und um die Notwendigkeit von Bereinigungen zu reduzieren.  

Temporäre Tabellen nutzen lokalen Speicher, um eine höhere Leistung bereitzustellen.  Weitere Informationen finden Sie unter [Temporäre Tabellen](sql-data-warehouse-tables-temporary.md).

### <a name="external-table"></a>Externe Tabelle

Eine externe Tabelle verweist auf Daten in Azure Storage Blob oder Azure Data Lake Store. Bei Verwendung in Verbindung mit der CREATE TABLE AS SELECT-Anweisung werden aus einer externen Tabelle ausgewählte Daten in den dedizierten SQL-Pool importiert.

Externe Tabellen eignen sich daher zum Laden von Daten. Ein Tutorial zum Ladevorgang finden Sie unter [Verwenden von PolyBase zum Laden von Daten aus Azure Blob Storage](load-data-from-azure-blob-storage-using-polybase.md).

## <a name="data-types"></a>Datentypen

Dedizierte SQL-Pools unterstützen die am häufigsten verwendeten Datentypen. Eine Liste der unterstützten Datentypen finden Sie in der CREATE TABLE-Anweisung im [CREATE TABLE-Verweis im Abschnitt „Datentypen“](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes). Eine Anleitung zur Verwendung der Datentypen finden Sie unter [Datentypen](sql-data-warehouse-tables-data-types.md).

## <a name="distributed-tables"></a>Verteilte Tabellen

Ein grundlegendes Feature von dedizierten SQL-Pools ist die Art und Weise, wie Tabellen über [Verteilungen](massively-parallel-processing-mpp-architecture.md#distributions) hinweg gespeichert und ausgeführt werden können.  Dedizierte SQL-Pools unterstützen drei Verfahren für die Verteilung der Daten: Roundrobin (Standard), Hash und repliziert.

### <a name="hash-distributed-tables"></a>Tabellen mit Hashverteilung

Bei einer Hashverteilung werden die Tabellenzeilen auf Basis des Werts in der Verteilungsspalte verteilt. Eine Tabelle mit Hashverteilung ist für eine hohe Leistung bei Abfragen an große Tabellen ausgelegt. Bei der Auswahl der Verteilungsspalte müssen mehrere Faktoren berücksichtigt werden.

Weitere Informationen finden Sie unter [Verteilen von Tabellen in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md).

### <a name="replicated-tables"></a>Replizierte Tabellen

Eine replizierte Tabelle ist eine vollständige Kopie der Tabelle, die auf jedem Serverknoten zur Verfügung gestellt wird. Abfragen werden für replizierte Tabellen schnell ausgeführt, da bei Verknüpfungen replizierter Tabellen keine Datenverschiebung erforderlich ist. Für die Replikation wird zusätzlicher Speicherplatz benötigt, und sie ist für große Tabellen nicht geeignet.

Weitere Informationen finden Sie unter [Entwurfsleitfaden für replizierte Tabellen](design-guidance-for-replicated-tables.md).

### <a name="round-robin-tables"></a>Roundrobintabellen

Bei einer Roundrobintabelle werden die Tabellenzeilen gleichmäßig auf alle Verteilungen aufgeteilt. Die Zeilen werden nach dem Zufallsprinzip verteilt. Das Laden von Daten in eine Roundrobintabelle geht schnell vonstatten.  Beachten Sie aber, dass bei Abfragen eine größere Anzahl von Datenverschiebungen erforderlich sein kann als bei anderen Verteilungsmethoden.

Weitere Informationen finden Sie unter [Verteilen von Tabellen in SQL Data Warehouse](sql-data-warehouse-tables-distribute.md).

### <a name="common-distribution-methods-for-tables"></a>Allgemeine Verteilungsmethoden für Tabellen

Die Tabellenkategorie bestimmt oftmals, welche Option für das Verteilen der Tabelle ausgewählt wird.

| Tabellenkategorie | Empfohlene Verteilungsoption |
|:---------------|:--------------------|
| Fakt           | Verwenden Sie die Hashverteilung mit gruppiertem Columnstore-Index. Die Leistung wird verbessert, wenn zwei Hashtabellen über die gleiche Verteilungsspalte verknüpft sind. |
| Dimension      | Verwenden Sie bei kleineren Tabellen die Replikation. Wenn Tabellen zu groß sind, um sie auf jedem Serverknoten zu speichern, verwenden Sie die Hashverteilung. |
| Staging        | Verwenden Sie für die Stagingtabelle das Roundrobinprinzip. Das Laden lässt sich mit CTAS beschleunigen. Wenn sich die Daten in der Stagingtabelle befinden, verwenden Sie INSERT...SELECT, um die Daten in Produktionstabellen zu verschieben. |

## <a name="table-partitions"></a>Tabellenpartitionen

In einer partitionierten Tabelle werden die Tabellenzeilen nach Datenbereichen gespeichert und Vorgänge entsprechend ausgeführt. Beispielsweise könnte eine Tabelle nach Tag, Monat oder Jahr partitioniert werden. Sie können die Abfrageleistung durch eine Partitionsbeseitigung verbessern, wobei ein Abfragescan auf Daten in einer Partition begrenzt wird. Auch können Sie die Daten durch Partitionswechsel verwalten. Da die Daten im SQL-Pool bereits verteilt sind, können zu viele Partitionen die Abfrageleistung beeinträchtigen. Weitere Informationen finden Sie unter [Partitionieren von Tabellen in SQL Data Warehouse](sql-data-warehouse-tables-partition.md).  Beim Wechseln der Partitionen zu Tabellenpartitionen, die nicht leer sind, sollten Sie die Option TRUNCATE_TARGET in Ihrer [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)-Anweisung in Erwägung ziehen, wenn die vorhandenen Daten abgeschnitten werden sollen. Der folgende Code fügt die transformierten Tagesdaten in SalesFact ein und überschreibt alle vorhandenen Daten.

```sql
ALTER TABLE SalesFact_DailyFinalLoad SWITCH PARTITION 256 TO SalesFact PARTITION 256 WITH (TRUNCATE_TARGET = ON);  
```

## <a name="columnstore-indexes"></a>Columnstore-Indizes

Standardmäßig speichert der dedizierte SQL-Pool eine Tabelle als gruppierten Columnstore-Index. Mit dieser Form der Datenspeicherung wird eine hohe Datenkomprimierung und Abfrageleistung für große Tabellen erreicht.  

Der gruppierte Columnstore-Index ist in der Regel die beste Wahl, aber in einigen Fällen ist ein gruppierter Index oder ein Heap die geeignete Speicherstruktur.  

> [!TIP]
> Eine Heaptabelle kann besonders für das Laden flüchtiger Daten hilfreich sind, z. B. für eine Stagingtabelle, die in eine endgültige Tabelle transformiert wird.

Eine Liste der Columnstore-Funktionen finden Sie unter [Columnstore-Indizes – Neuigkeiten](/sql/relational-databases/indexes/columnstore-indexes-what-s-new?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Informationen zum Verbessern der Leistung von Columnstore-Indizes finden Sie unter [Maximieren der Zeilengruppenqualität für Columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

## <a name="statistics"></a>Statistik

Der Abfrageoptimierer verwendet beim Erstellen des Plans für die Ausführung einer Abfrage Statistiken auf Spaltenebene.

Um die Abfrageleistung zu verbessern, ist es wichtig, über Statistiken für einzelne Spalten zu verfügen, insbesondere für in Abfrageverknüpfungen verwendete Spalten. Das [Erstellen von Statistiken](sql-data-warehouse-tables-statistics.md#automatic-creation-of-statistic) erfolgt automatisch.  

Die Aktualisierung der Statistiken erfolgt nicht automatisch. Führen Sie die Statistikaktualisierung durch, wenn eine erhebliche Anzahl von Zeilen hinzugefügt oder geändert wurde. Aktualisieren Sie Statistiken z. B. nach einem Ladevorgang. Weitere Informationen finden Sie unter [Verwalten von Statistiken für Tabellen in SQL Data Warehouse](sql-data-warehouse-tables-statistics.md).

## <a name="primary-key-and-unique-key"></a>Primärschlüssel und eindeutiger Schlüssel

PRIMARY KEY wird nur unterstützt, wenn sowohl NONCLUSTERED als auch NOT ENFORCED verwendet werden.  Der UNIQUE-Constraint wird nur unterstützt, wenn NOT ENFORCED verwendet wird.  Weitere Informationen finden Sie unter [Tabelleneinschränkungen bei dedizierten SQL-Pools](sql-data-warehouse-table-constraints.md).

## <a name="commands-for-creating-tables"></a>Befehle zum Erstellen von Tabellen

Sie können eine Tabelle als neue leere Tabelle erstellen. Alternativ können Sie eine Tabelle erstellen und mit den Ergebnissen einer SELECT-Anweisung füllen. Es folgen die T-SQL-Befehle zum Erstellen einer Tabelle.

| T-SQL-Anweisung | BESCHREIBUNG |
|:----------------|:------------|
| [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Erstellt eine leere Tabelle durch die Definition der Tabellenspalten und Optionen. |
| [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Erstellt eine externe Tabelle. Die Definition der Tabelle wird in einem dedizierten SQL-Pool gespeichert. Die Tabellendaten werden in Azure Blob Storage oder Azure Data Lake Store abgelegt. |
| [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Füllt eine neue Tabelle mit den Ergebnissen einer SELECT-Anweisung. Die Spalten und Datentypen der Tabelle basieren auf den Ergebnissen der SELECT-Anweisung. Mit dieser Anweisung können Daten zum Importieren aus einer externen Tabelle ausgewählt werden. |
| [CREATE EXTERNAL TABLE AS SELECT](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) | Erstellt eine neue externe Tabelle durch den Export der Ergebnisse einer SELECT-Anweisung an einen externen Speicherort.  Dieser Speicherort ist entweder Azure Blob Storage oder Azure Data Lake Store. |

## <a name="aligning-source-data-with-dedicated-sql-pool"></a>Ausrichten von Quelldaten mit dedizierten SQL-Pools

Tabellen in dedizierten SQL-Pools werden durch das Laden von Daten aus einer anderen Datenquelle gefüllt. Um einen Ladevorgang erfolgreich ausführen zu können, müssen die Anzahl und die Datentypen der Spalten in den Quelldaten an der Tabellendefinition im dedizierten SQL-Pool ausgerichtet werden. Diese Ausrichtung der Daten zu bewältigen, ist möglicherweise der schwierigste Aspekt beim Entwerfen von Tabellen.

Wenn Daten aus mehreren Datenspeichern stammen, können Sie die Daten in den dedizierten SQL-Pool laden und in einer Integrationstabelle speichern. Sobald sich die Daten in der Integrationstabelle befinden, können Sie die Leistungsfähigkeit des dedizierten SQL-Pools nutzen, um Transformationsvorgänge auszuführen. Sobald die Daten vorbereitet sind, können Sie sie in Produktionstabellen einfügen.

## <a name="unsupported-table-features"></a>Nicht unterstützte Tabellenfunktionen

Dedizierte SQL-Pools unterstützen viele, aber nicht alle Tabellenfunktionen, die in anderen Datenbanken zu finden sind.  Die folgende Liste enthält einige der Tabellenfunktionen, die in dedizierten SQL-Pools nicht unterstützt werden:

- Fremdschlüssel, überprüfen Sie die [Tabellenconstraints](/sql/t-sql/statements/alter-table-table-constraint-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Berechnete Spalten](/sql/t-sql/statements/alter-table-computed-column-definition-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Indizierte Sichten](/sql/relational-databases/views/create-indexed-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Sequenz](/sql/t-sql/statements/create-sequence-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Spalten mit geringer Dichte](/sql/relational-databases/tables/use-sparse-columns?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- Ersatzschlüssel. Implementierung mit [Identity](sql-data-warehouse-tables-identity.md).
- [Synonyme](/sql/t-sql/statements/create-synonym-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Trigger](/sql/t-sql/statements/create-trigger-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Eindeutige Indizes](/sql/t-sql/statements/create-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Benutzerdefinierte Typen](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="table-size-queries"></a>Abfragen für die Tabellengröße

Eine einfache Möglichkeit, den Speicherplatz- und Zeilenverbrauch einer Tabelle in jeder der 60 Verteilungen zu ermitteln, ist die Verwendung von [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Die Verwendung von DBCC-Befehlen kann aber auch mit größeren Einschränkungen verbunden sein.  Dynamische Verwaltungssichten (DMVs) zeigen mehr Details als DBCC-Befehle. Erstellen Sie zunächst diese Sicht:

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count]
    + nps.[row_overflow_used_page_count]
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count]
 - (nps.[reserved_page_count] - nps.[used_page_count])
 - ([in_row_data_page_count]
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
WHERE pn.[type] = 'COMPUTE'
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT *
FROM size
;
```

### <a name="table-space-summary"></a>Tabellenspeicherplatz – Zusammenfassung

Diese Abfrage gibt die Zeilen und den Speicherplatz nach Tabelle zurück.  Damit können Sie anzeigen, welche Tabellen am größten sind und ob es sich um Roundrobintabellen, replizierte Tabellen oder Tabellen mit Hashverteilung handelt.  Für Tabellen mit Hashverteilung wird zudem die Verteilungsspalte angezeigt.  

```sql
SELECT
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM
    dbo.vTableSizes
GROUP BY
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,      distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Tabellenspeicherplatz nach Verteilungstyp

```sql
SELECT
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Tabellenspeicherplatz nach Indextyp

```sql
SELECT
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Verteilungsspeicherplatz – Zusammenfassung

```sql
SELECT
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Nächste Schritte

Nach dem Erstellen der Tabellen für den dedizierten SQL-Pool werden im nächsten Schritt Daten in die Tabelle geladen.  Ein Tutorial zum Laden von Daten finden Sie unter [Laden von Daten in einen dedizierten SQL-Pool](load-data-wideworldimportersdw.md).
