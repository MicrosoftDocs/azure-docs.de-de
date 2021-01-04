---
title: Erstellen und Aktualisieren von Statistiken für Tabellen
description: Empfehlungen und Beispiele zum Erstellen und Aktualisieren von Abfrageoptimierungsstatistiken für Tabellen im dedizierten SQL-Pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e7fc89dcc0e7938ea2958d5c804abe82e20f186d
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96447936"
---
# <a name="table-statistics-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Tabellenstatistiken für den dedizierten SQL-Pool in Azure Synapse Analytics

In diesem Artikel finden Sie Empfehlungen und Beispiele zum Erstellen und Aktualisieren von Abfrageoptimierungsstatistiken für Tabellen im dedizierten SQL-Pool.

## <a name="why-use-statistics"></a>Gründe für die Verwendung von Statistiken

Je mehr Informationen zu Ihren Daten der dedizierte SQL-Pool hat, desto schneller können Abfragen dafür ausgeführt werden. Nach dem Laden der Daten in den dedizierten SQL-Pool ist das Erfassen von Statistiken zu Ihren Daten eine der wichtigsten Maßnahmen, die Sie zur Optimierung Ihrer Abfragen treffen können.

Der Abfrageoptimierer im dedizierten SQL-Pool arbeitet kostenorientiert. Die Kosten der verschiedenen Abfragepläne werden verglichen, und dann wird der Plan mit den geringsten Kosten gewählt. In den meisten Fällen wird der Plan gewählt, der am schnellsten ausgeführt wird.

Wenn der Abfrageoptimierer z.B. schätzt, dass das Datum, nach dem die Abfrage gefiltert wird, eine Zeile zurückgibt, wird er einen Plan auswählen. Wenn er schätzt, dass für das ausgewählte Datum 1 Million Zeilen zurückgegeben werden, wird er einen anderen Plan zurückgegeben.

## <a name="automatic-creation-of-statistic"></a>Automatische Erstellung von Statistiken

Wenn die Datenbankoption „AUTO_CREATE_STATISTICS“ aktiviert ist, werden im dedizierten SQL-Pool eingehende Benutzerabfragen auf fehlende Statistiken hin analysiert.

Wenn Statistiken fehlen, erstellt der Abfrageoptimierer Statistiken für einzelne Spalten im Abfrageprädikat oder der Verknüpfungsbedingung, um Kardinalitätsschätzungen für den Abfrageplan zu verbessern.

> [!NOTE]
> Die automatische Erstellung von Statistiken ist zurzeit standardmäßig aktiviert.

Sie können überprüfen, ob „AUTO_CREATE_STATISTICS“ bei Ihrem dedizierten SQL-Pool konfiguriert wurde, indem Sie den folgenden Befehl ausführen:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Wenn „AUTO_CREATE_STATISTICS“ für Ihren dedizierten SQL-Pool nicht konfiguriert wurde, sollten Sie diese Eigenschaft durch Ausführen des folgenden Befehls aktivieren:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Durch diese Anweisungen wird die automatische Erstellung von Statistiken ausgelöst:

- SELECT
- INSERT-SELECT
- CTAS
- UPDATE
- Delete
- EXPLAIN, wenn eine Verknüpfung enthalten ist oder das Vorhandensein eines Prädikats erkannt wird.

> [!NOTE]
> Die automatische Erstellung von Statistiken ist nicht bei temporären oder externen Tabellen möglich.

Die automatische Erstellung von Statistiken erfolgt synchron, sodass möglicherweise eine geringfügige Verlangsamung bei der Abfrageleistung auftreten kann, sofern in Ihren Spalten keine Statistiken enthalten sind. Die Zeit zum Erstellen von Statistiken für eine einzelne Spalte hängt von der Größe der Tabelle ab.

Um messbare Leistungseinbußen zu verhindern, sollten Sie zuerst durch Ausführen der Benchmarkworkload sicherstellen, dass Statistiken erstellt wurden, bevor Sie Profile für das System erstellen.

> [!NOTE]
> Die Erstellung von Statistiken wird in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) in einem anderen Benutzerkontext protokolliert.

Wenn automatische Statistiken erstellt werden, sehen Sie wie folgt aus: _WA_Sys_<8 digit column id in Hex>_<8 digit table id in Hex>. Sie können Statistiken anzeigen, die bereits erstellt wurden, indem Sie den Befehl [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ausführen:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

„Table_name“ ist der Name der Tabelle, die die anzuzeigende Statistik enthält. Diese Tabelle darf keine externe Tabelle sein. „Target“ ist der Name des Zielindex, der Statistik oder der Spalte, für den bzw. für die Statistikinformationen angezeigt werden sollen.

## <a name="update-statistics"></a>Statistikaktualisierung

Eine bewährte Methode ist es, die Statistiken für Datenspalten im Zuge des Hinzufügens neuer Daten täglich zu aktualisieren. Bei jedem Laden von neuen Zeilen in den dedizierten SQL-Pool werden neue Daten für Lade- oder Transaktionsvorgänge hinzugefügt. Durch diese Ergänzungen wird die Datenverteilung geändert, und die Statistiken sind nicht mehr aktuell.

Statistiken zu einer Länder-/Regionenspalte in einer Kundentabelle müssen unter Umständen nie aktualisiert werden, da sich die Verteilung der Werte in der Regel nicht ändert. Wenn davon auszugehen ist, dass die Verteilung zwischen Kunden konstant ist, bewirkt das Hinzufügen neuer Zeilen zur Tabellenvariante keine Änderung der Datenverteilung.

Wenn Ihr dedizierter SQL-Pool allerdings nur ein Land/eine Region enthält und Sie Daten eines neuen Lands/einer neuen Region hinzufügen, führt dies dazu, dass Daten aus mehreren Ländern/Regionen gespeichert werden. In diesem Fall müssen Sie die Statistiken in der Länder-/Regionenspalte aktualisieren.

Im Folgenden finden Sie Empfehlungen für Updates für Statistiken:

|||
|-|-|
| **Aktualisierungshäufigkeit für Statistiken**  | Konservativ: Täglich </br> Nach dem Laden oder Transformieren von Daten |
| **Stichproben** |  Bei weniger als 1 Milliarde Zeilen Standard-Stichprobenentnahme verwenden (20 Prozent). </br> Bei mehr als 1 Milliarde Zeilen Stichprobenentnahme von zwei Prozent verwenden. |

Eine der ersten Fragen bei der Problembehandlung für eine Abfrage sollte lauten: **„Sind die Statistiken auf dem aktuellen Stand?“**

Diese Frage kann nicht anhand des Alters der Daten beantwortet werden. Ein Statistikobjekt auf dem aktuellen Stand ist ggf. alt, falls sich die zugrunde liegenden Daten nicht wesentlich geändert haben. Wenn sich die Anzahl von Zeilen deutlich geändert hat oder es eine wesentliche Änderung bei der Verteilung der Werte für eine Spalte gibt, *ist der Zeitpunkt gekommen*, die Statistiken zu aktualisieren. 

Es gibt keine dynamische Verwaltungssicht, mit der Sie feststellen können, ob sich die Daten innerhalb der Tabelle seit der letzten Aktualisierung der Statistik geändert haben.  Die folgenden zwei Abfragen können Ihnen helfen, zu bestimmen, ob Ihre Statistiken veraltet sind.

**Abfrage 1:**  Ermitteln der Differenz zwischen der Zeilenanzahl aus den Statistiken (**stats_row_count**) und der tatsächlichen Zeilenanzahl (**actual_row_count**). 

```sql
select 
objIdsWithStats.[object_id], 
actualRowCounts.[schema], 
actualRowCounts.logical_table_name, 
statsRowCounts.stats_row_count, 
actualRowCounts.actual_row_count,
row_count_difference = CASE
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN actualRowCounts.actual_row_count - statsRowCounts.stats_row_count
    ELSE statsRowCounts.stats_row_count - actualRowCounts.actual_row_count
END,
percent_deviation_from_actual = CASE
    WHEN actualRowCounts.actual_row_count = 0 THEN statsRowCounts.stats_row_count
    WHEN statsRowCounts.stats_row_count = 0 THEN actualRowCounts.actual_row_count
    WHEN actualRowCounts.actual_row_count >= statsRowCounts.stats_row_count THEN CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (actualRowCounts.actual_row_count - statsRowCounts.stats_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
    ELSE CONVERT(NUMERIC(18, 0), CONVERT(NUMERIC(18, 2), (statsRowCounts.stats_row_count - actualRowCounts.actual_row_count)) / CONVERT(NUMERIC(18, 2), actualRowCounts.actual_row_count) * 100)
END
from
(
    select distinct object_id from sys.stats where stats_id > 1
) objIdsWithStats
left join
(
    select object_id, sum(rows) as stats_row_count from sys.partitions group by object_id
) statsRowCounts
on objIdsWithStats.object_id = statsRowCounts.object_id 
left join
(
    SELECT sm.name [schema] ,
    tb.name logical_table_name ,
    tb.object_id object_id ,
    SUM(rg.row_count) actual_row_count
    FROM sys.schemas sm
    INNER JOIN sys.tables tb ON sm.schema_id = tb.schema_id
    INNER JOIN sys.pdw_table_mappings mp ON tb.object_id = mp.object_id
    INNER JOIN sys.pdw_nodes_tables nt ON nt.name = mp.physical_name
    INNER JOIN sys.dm_pdw_nodes_db_partition_stats rg
    ON rg.object_id = nt.object_id
    AND rg.pdw_node_id = nt.pdw_node_id
    AND rg.distribution_id = nt.distribution_id
    WHERE 1 = 1
    GROUP BY sm.name, tb.name, tb.object_id
) actualRowCounts
on objIdsWithStats.object_id = actualRowCounts.object_id

```

**Abfrage 2:** Ermitteln des Alters Ihrer Statistiken, indem Sie den Zeitpunkt überprüfen, zu dem die Statistiken für jede Tabelle zuletzt aktualisiert wurden. 

> [!NOTE]
> Immer wenn sich die Verteilung der Werte einer Spalte wesentlich ändert, sollten Sie die Statistiken – unabhängig vom Zeitpunkt des letzten Updates – aktualisieren.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Beispielsweise sind für **Datumsspalten** in einem dedizierten SQL-Pool normalerweise häufige Statistikaktualisierungen erforderlich. Bei jedem Laden von neuen Zeilen in den dedizierten SQL-Pool werden neue Daten für Lade- oder Transaktionsvorgänge hinzugefügt. Durch diese Ergänzungen wird die Datenverteilung geändert, und die Statistiken sind nicht mehr aktuell.

Im Gegensatz dazu müssen die Statistiken für die Spalte „Geschlecht“ in einer Kundentabelle unter Umständen nie aktualisiert werden. Wenn davon auszugehen ist, dass die Verteilung zwischen Kunden konstant ist, bewirkt das Hinzufügen neuer Zeilen zur Tabellenvariante keine Änderung der Datenverteilung.

Wenn Ihr dedizierter SQL-Pool nur ein Geschlecht enthält und eine neue Anforderung zu mehr als einem Geschlecht führt, müssen Sie die Statistiken für die Spalte „Geschlecht“ aktualisieren.

Weitere Informationen finden Sie im allgemeinen Leitfaden zu [Statistik](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="implementing-statistics-management"></a>Implementieren der Statistikverwaltung

Häufig ist es ratsam, den Datenladeprozess zu erweitern, um sicherzustellen, dass die Statistiken am Ende des Ladevorgangs aktualisiert werden. Dadurch vermeiden/minimieren Sie Sperren oder Ressourcenkonflikte zwischen gleichzeitigen Abfragen.  

Das Laden von Daten ist der Zeitpunkt, zu dem Tabellen am häufigsten ihre Größe oder die Verteilung der Werte ändern. Das Laden von Daten ist ein logischer Ansatzpunkt zum Implementieren einiger Verwaltungsprozesse.

Im Folgenden finden Sie einige Richtlinien zur Aktualisierung von Statistiken:

- Stellen Sie sicher, dass jede geladene Tabelle mindestens über ein aktualisiertes Statistikobjekt verfügt. Im Rahmen der Statistikaktualisierung werden dann die Informationen zur Tabellengröße (Zeilen- und Seitenanzahl) aktualisiert.
- Konzentrieren Sie sich auf Spalten mit JOIN-, GROUP BY-, ORDER BY- und DISTINCT-Klauseln.
- Erwägen Sie, Spalten vom Typ „aufsteigender Schlüssel“, z.B. Transaktionsdaten, häufiger zu aktualisieren, da diese Werte nicht in das Statistikhistogramm einbezogen werden.
- Erwägen Sie, Spalten mit statischer Verteilung weniger häufig zu aktualisieren.
- Bedenken Sie, dass jedes statistische Objekt der Reihe nach aktualisiert wird. Es ist nicht ideal, einfach `UPDATE STATISTICS <TABLE_NAME>` zu implementieren. Dies gilt besonders für breite Tabellen mit vielen Statistikobjekten.

Weitere Informationen finden Sie unter [Kardinalitätsschätzung (SQL Server)](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="examples-create-statistics"></a>Beispiele: Erstellen von Statistiken

In diesen Beispielen wird veranschaulicht, wie Sie verschiedene Optionen zum Erstellen von Statistiken verwenden. Die Optionen, die Sie für die einzelnen Spalten verwenden, richten Sie nach den Merkmalen Ihrer Daten und nach der Verwendung der Spalten in Abfragen.

### <a name="create-single-column-statistics-with-default-options"></a>Erstellen von Einspaltenstatistiken mit Standardoptionen

Zum Erstellen von Statistiken für eine Spalte geben Sie einen Namen für das Statistikobjekt und den Namen der Spalte an.

Bei dieser Syntax werden alle Standardoptionen verwendet. Beim Erstellen von Statistiken werden standardmäßig von **20 Prozent** der Tabelle Stichproben genommen.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Beispiel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Erstellen von Einspaltenstatistiken per Untersuchung jeder Zeile

Die standardmäßige Stichprobenrate von 20 % ist in den meisten Fällen ausreichend. Sie können die Stichprobenrate aber auch anpassen.

Verwenden Sie die folgende Syntax, um die gesamte Tabelle zu verwenden:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Beispiel:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Erstellen von Einspaltenstatistiken durch Angeben der Stichprobengröße

Alternativ dazu können Sie die Stichprobengröße als Prozentwert angeben:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Erstellen von Einspaltenstatistiken für einen Teil der Zeilen

Sie können auch Statistiken für einen Teil der Zeilen einer Tabelle erstellen. Dies wird als gefilterte Statistik bezeichnet.

Sie können gefilterte Statistiken beispielsweise verwenden, wenn Sie planen, eine bestimmte Partition einer großen partitionierten Tabelle abzufragen. Indem Sie nur für die Partitionswerte Statistiken erstellen, wird die Genauigkeit der Statistiken erhöht und die Abfrageleistung verbessert.

In diesem Beispiel werden Statistiken für einen Bereich von Werten erstellt. Die Werte können leicht so definiert werden, dass sie den Werten in einer Partition entsprechen.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Damit der Abfrageoptimierer beim Auswählen des Plans für die verteilte Abfrage die Verwendung von gefilterten Statistiken berücksichtigt, muss die Abfrage in die Definition des Statistikobjekts passen. Im vorherigen Beispiel müssen für die WHERE-Klausel der Abfrage col1-Werte zwischen 2000101 und 20001231 angegeben werden.

### <a name="create-single-column-statistics-with-all-the-options"></a>Erstellen von Einspaltenstatistiken mit allen Optionen

Sie können die Optionen auch kombinieren. Im folgenden Beispiel wird ein Objekt vom Typ „gefilterte Statistik“ mit einer benutzerdefinierten Stichprobengröße erstellt:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Die gesamte Referenz finden Sie unter [CREATE STATISTICS (Transact-SQL)](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="create-multi-column-statistics"></a>Erstellen von Mehrspaltenstatistiken

Verwenden Sie zum Erstellen eines mehrspaltigen Statistikobjekts die vorherigen Beispiele, aber geben Sie mehr Spalten an.

> [!NOTE]
> Das Histogramm, das zum Schätzen der Zeilenanzahl im Abfrageergebnis verwendet wird, ist nur für die erste Spalte verfügbar, die in der Definition des Statistikobjekts aufgelistet ist.

In diesem Beispiel basiert das Histogramm auf *product\_category*. Spaltenübergreifende Statistiken werden für *product\_category* und *product\_sub_category* berechnet:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Da zwischen *product\_category* und *product\_sub\_category* eine Korrelation besteht, kann ein Mehrspaltenstatistik-Objekt nützlich sein, wenn gleichzeitig auf diese Spalten zugegriffen wird.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Erstellen von Statistiken für alle Spalten einer Tabelle

Eine Möglichkeit zum Erstellen von Statistiken ist das Ausführen von CREATE STATISTICS-Befehlen nach dem Erstellen der Tabelle:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-sql-pool"></a>Verwenden einer gespeicherten Prozedur zum Erstellen von Statistiken für alle Spalten in einem SQL-Pool

Der dedizierte SQL-Pool verfügt nicht über eine gespeicherte Systemprozedur, die „sp_create_stats“ in SQL Server entspricht. Mit dieser gespeicherten Prozedur wird ein Einzelspaltenstatistik-Objekt für jede Spalte in einem SQL-Pool erstellt, die nicht bereits über eine Statistik verfügt.

Das folgende Beispiel ist eine nützliche Einstiegshilfe für Ihren SQL-Pool-Entwurf. Sie können diesen Vorgang an Ihre Anforderungen anpassen.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Führen Sie die gespeicherte Prozedur aus, um damit Statistiken für alle Spalten in der Tabelle mit den Standardwerten zu erstellen.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Rufen Sie diese Prozedur auf, um damit Statistiken für alle Spalten in der Tabelle mithilfe eines vollständigen Scans zu erstellen.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Geben Sie „3“ und den Beispielprozentsatz ein, um erfasste Statistiken für alle Spalten in der Tabelle zu erstellen. Diese Prozedur verwendet eine Stichprobenrate von 20 %.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Beispiele: Statistikaktualisierung

Sie können wie folgt vorgehen, um Statistiken zu aktualisieren:

- Aktualisieren Sie ein Statistikobjekt. Geben Sie den Namen des Statistikobjekts an, das Sie aktualisieren möchten.
- Aktualisieren Sie alle Statistikobjekte einer Tabelle. Geben Sie anstelle eines bestimmten Statistikobjekts den Namen der Tabelle an.

### <a name="update-one-specific-statistics-object"></a>Aktualisieren eines bestimmten Statistikobjekts

Verwenden Sie die folgende Syntax, um ein bestimmtes Statistikobjekt zu aktualisieren:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Beispiel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Indem Sie bestimmte Statistikobjekte aktualisieren, können Sie den Zeit- und Ressourcenaufwand reduzieren, der zum Verwalten von Statistiken erforderlich ist. Hierbei ist es erforderlich, genaue Überlegungen anzustellen, damit die besten Statistikobjekte für die Aktualisierung ausgewählt werden können.

### <a name="update-all-statistics-on-a-table"></a>Update aller Statistiken für eine Tabelle

Hier ist eine einfache Methode zum Aktualisieren aller Statistikobjekte einer Tabelle:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Beispiel:

```sql
UPDATE STATISTICS dbo.table1;
```

Die UPDATE STATISTICS-Anweisung ist einfach zu verwenden. Bedenken Sie, dass hiermit *alle* Statistiken der Tabelle aktualisiert werden, sodass unter Umständen mehr Arbeit als erforderlich erledigt wird. Wenn die Leistung kein Problem darstellt, ist dies die einfachste und umfassendste Möglichkeit sicherzustellen, dass die Statistiken aktuell sind.

> [!NOTE]
> Beim Aktualisieren aller Statistiken einer Tabelle führt der dedizierte SQL-Pool einen Scan durch, um für jedes Statistikobjekt Stichproben der Tabelle zu nehmen. Wenn die Tabelle groß ist und viele Spalten und Statistiken enthält, kann es effizienter sein, je nach Bedarf einzelne Spalten zu aktualisieren.

Informationen zur Implementierung einer `UPDATE STATISTICS`-Prozedur finden Sie unter [Temporäre Tabellen in SQL Data Warehouse](sql-data-warehouse-tables-temporary.md). Die Implementierungsmethode unterscheidet sich etwas von der obigen `CREATE STATISTICS`-Prozedur, aber das Ergebnis ist identisch.

Die vollständige Syntax finden Sie unter [UPDATE STATISTICS (Transact-SQL)](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="statistics-metadata"></a>Statistikmetadaten

Es gibt mehrere Systemsichten und -funktionen, die Sie zum Suchen nach Informationen zu Statistiken verwenden können. Beispielsweise können Sie sehen, ob ein Statistikobjekt veraltet ist, indem Sie die stats-date-Funktion verwenden. Damit können Sie anzeigen, wann Statistiken zuletzt erstellt oder aktualisiert wurden.

### <a name="catalog-views-for-statistics"></a>Katalogsichten für Statistiken

Diese Systemsichten enthalten Informationen zu Statistiken:

| Katalogsicht | BESCHREIBUNG |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jede Spalte. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jedes Objekt in der Datenbank. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jedes Schema in der Datenbank. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jedes Statistikobjekt. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jede Spalte im Statistikobjekt. Eine Verknüpfung zurück zu sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jede Tabelle (enthält externe Tabellen). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Eine Zeile für jeden Datentyp. |

### <a name="system-functions-for-statistics"></a>Systemfunktionen für Statistiken

Diese Systemfunktionen sind nützlich für die Arbeit mit Statistiken:

| Systemfunktion | BESCHREIBUNG |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Datum, an dem das Statistikobjekt zuletzt aktualisiert wurde. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Zusammenfassungsebene und ausführliche Informationen zur Verteilung der Werte gemäß Statistikobjekt. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinieren von Statistikspalten und -funktionen zu einer Sicht

In dieser Sicht werden Spalten, die sich auf Statistiken beziehen, und Ergebnisse aus der STATS_DATE()-Funktion zusammengefasst.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS()-Beispiele

Mit DBCC SHOW_STATISTICS() werden die Daten angezeigt, die in einem Statistikobjekt enthalten sind. Diese Daten bestehen aus drei Teilen:

- Header
- Dichtevektor
- Histogramm

Dies sind die Headermetadaten zur Statistik. Im Histogramm wird die Verteilung der Werte in der ersten Schlüsselspalte des Statistikobjekts angezeigt. Der Dichtevektor misst die spaltenübergreifende Korrelation.

> [!NOTE]
> Der dedizierte SQL-Pool berechnet Kardinalitätsschätzungen anhand der Daten im Statistikobjekt.

### <a name="show-header-density-and-histogram"></a>Anzeigen von Header, Dichte und Histogramm

In diesem einfachen Beispiel werden alle drei Teile eines Statistikobjekts angezeigt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Beispiel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Anzeigen eines oder mehrerer Teile von DBCC SHOW_STATISTICS()

Wenn Sie nur bestimmte Teile anzeigen möchten, verwenden Sie die `WITH`-Klausel und geben an, welche Teile dies sein sollen:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Beispiel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS()-Unterschiede

DBCC SHOW_STATISTICS() ist im Vergleich zu SQL Server im dedizierten SQL-Pool strenger implementiert:

- Nicht dokumentierte Funktionen werden nicht unterstützt.
- Verwendung von Stats_stream nicht möglich.
- Ergebnisse für bestimmte Teilmengen von Statistikdaten können nicht verknüpft werden. Beispiel: (STAT_HEADER JOIN DENSITY_VECTOR).
- NO_INFOMSGS kann für die Meldungsunterdrückung nicht festgelegt werden.
- Eckige Klammern um Namen von Statistiken können nicht verwendet werden.
- Spaltennamen können nicht zum Identifizieren von Statistikobjekten verwendet werden.
- Benutzerdefinierter Fehler 2767 wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Wie Sie die Abfrageleistung weiter verbessern, erfahren Sie unter [Überwachen Ihrer Workload mit dynamischen Verwaltungssichten](sql-data-warehouse-manage-monitor.md).
