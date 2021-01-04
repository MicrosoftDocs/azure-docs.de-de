---
title: Erstellen und Aktualisieren von Statistiken mit Azure Synapse SQL-Ressourcen
description: Empfehlungen und Beispiele zum Erstellen und Aktualisieren von Abfrageoptimierungsstatistiken in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/19/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 52e3ea3e07a81495f64f70f72686154a02a654af
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451792"
---
# <a name="statistics-in-synapse-sql"></a>Statistiken in Synapse SQL

In diesem Artikel finden Sie Empfehlungen und Beispiele für die Erstellung und Aktualisierung von Abfrageoptimierungsstatistiken mithilfe folgender Synapse SQL-Ressourcen: dedizierter SQL-Pool und serverloser SQL-Pool.

## <a name="statistics-in-dedicated-sql-pool"></a>Statistiken im dedizierten SQL-Pool

### <a name="why-use-statistics"></a>Gründe für die Verwendung von Statistiken

Je mehr Informationen zu Ihren Daten der dedizierte SQL-Pool hat, desto schneller kann er Abfragen ausführen. Nach dem Laden der Daten in einen dedizierten SQL-Pool ist das Erfassen von Statistiken zu Ihren Daten eine der wichtigsten Maßnahmen, die Sie zur Abfrageoptimierung treffen können.  

Der Abfrageoptimierer im dedizierten SQL-Pool arbeitet kostenorientiert. Die Kosten der verschiedenen Abfragepläne werden verglichen, und dann wird der Plan mit den geringsten Kosten gewählt. In den meisten Fällen wird der Plan gewählt, der am schnellsten ausgeführt wird.

Wenn der Abfrageoptimierer beispielsweise schätzt, dass das Datum, nach dem die Abfrage gefiltert wird, eine Zeile zurückgibt, wählt er einen Plan aus. Wenn er schätzt, dass für das ausgewählte Datum 1 Million Zeilen zurückgegeben werden, wird er einen anderen Plan zurückgegeben.

### <a name="automatic-creation-of-statistics"></a>Automatische Erstellung von Statistiken

Die dedizierte SQL-Pool-Engine analysiert eingehende Benutzerabfragen auf fehlende Statistiken hin, wenn die Datenbankoption „AUTO_CREATE_STATISTICS“ auf `ON` festgelegt ist.  Wenn Statistiken fehlen, erstellt der Abfrageoptimierer Statistiken für einzelne Spalten im Abfrageprädikat oder der Verknüpfungsbedingung. 

Diese Funktion wird verwendet, um Kardinalitätsschätzungen für den Abfrageplan zu verbessern.

> [!IMPORTANT]
> Die automatische Erstellung von Statistiken ist zurzeit standardmäßig aktiviert.

Sie können überprüfen, ob AUTO_CREATE_STATISTICS bei Ihrem Data Warehouse konfiguriert ist, indem Sie den folgenden Befehl ausführen:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Wenn AUTO_CREATE_STATISTICS nicht für Ihr Data Warehouse aktiviert ist, sollten Sie diese Eigenschaft durch Ausführen des folgenden Befehls aktivieren:

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
> Die automatische Erstellung von Statistiken wird nicht bei temporären oder externen Tabellen generiert.

Die automatische Erstellung von Statistiken erfolgt synchron. Daher kann möglicherweise eine geringfügige Verlangsamung bei der Abfrageleistung auftreten, sofern in Ihren Spalten keine Statistiken enthalten sind. Die Zeit zum Erstellen von Statistiken für eine einzelne Spalte hängt von der Größe der Tabelle ab.

Um messbare Leistungseinbußen zu verhindern, sollten Sie zuerst durch Ausführen der Benchmarkworkload sicherstellen, dass Statistiken erstellt wurden, bevor Sie Profile für das System erstellen.

> [!NOTE]
> Die Erstellung von Statistiken wird in [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) in einem anderen Benutzerkontext protokolliert.

Wenn automatische Statistiken erstellt werden, sehen Sie wie folgt aus: _WA_Sys_<8 digit column id in Hex>_<8 digit table id in Hex>. Sie können bereits erstellte Statistiken anzeigen, indem Sie den Befehl [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) ausführen:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

„Table_name“ ist der Name der Tabelle, die die anzuzeigende Statistik enthält, bei der es sich nicht um eine externe Tabelle handeln kann. „Target“ ist der Name des Zielindex, der Statistik oder der Spalte, für den bzw. für die Statistikinformationen angezeigt werden sollen.

### <a name="update-statistics"></a>Statistikaktualisierung

Eine bewährte Methode ist es, die Statistiken für Datenspalten im Zuge des Hinzufügens neuer Daten täglich zu aktualisieren. Bei jedem Laden von neuen Zeilen in das Data Warehouse werden neue Datumsangaben für Lade- oder Transaktionsvorgänge hinzugefügt. Durch diese Ergänzungen wird die Datenverteilung geändert, und die Statistiken sind nicht mehr aktuell.

Statistiken zu einer Länder-/Regionenspalte in einer Kundentabelle müssen unter Umständen nie aktualisiert werden, da sich die Verteilung der Werte in der Regel nicht ändert. Wenn davon auszugehen ist, dass die Verteilung zwischen Kunden konstant ist, bewirkt das Hinzufügen neuer Zeilen zur Tabellenvariante keine Änderung der Datenverteilung.

Wenn Ihr Data Warehouse allerdings nur ein Land oder eine Region enthält und Sie Daten eines neuen Lands oder einer neuen Region hinzufügen, dann müssen Sie die Statistiken in der Länder- oder Regionenspalte aktualisieren.

Im Folgenden finden Sie Empfehlungen für Updates für Statistiken:

|||
|-|-|
| **Aktualisierungshäufigkeit für Statistiken**  | Konservativ: Täglich </br> Nach dem Laden oder Transformieren von Daten |
| **Stichproben** |  Bei weniger als 1 Milliarde Zeilen Standard-Stichprobenentnahme verwenden (20 Prozent). </br> Bei mehr als 1 Milliarde Zeilen Stichprobenentnahme von zwei Prozent verwenden. |

### <a name="determine-last-statistics-update"></a>Letzte Statistikupdates ermitteln

Eine der ersten Fragen bei der Problembehandlung für eine Abfrage sollte lauten: **„Sind die Statistiken auf dem aktuellen Stand?“**

Diese Frage kann nicht anhand des Alters der Daten beantwortet werden. Ein Statistikobjekt auf dem aktuellen Stand ist ggf. alt, falls sich die zugrunde liegenden Daten nicht wesentlich geändert haben. Wenn sich die Anzahl von Zeilen deutlich geändert hat oder eine wesentliche Änderung bei der Verteilung der Werte für eine Spalte aufgetreten ist, *ist der Zeitpunkt gekommen*, die Statistiken zu aktualisieren.

Es gibt keine dynamische Verwaltungssicht, mit der Sie feststellen können, ob sich die Daten innerhalb der Tabelle seit der letzten Aktualisierung der Statistik geändert haben. Das Alter Ihrer Statistiken kann Ihnen teilweise als Hinweis auf die Aktualität der Daten dienen. 

Sie können die folgende Abfrage verwenden, um den Zeitpunkt zu ermitteln, zu dem die Statistiken für jede Tabelle zuletzt aktualisiert wurden.

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

Beispielsweise benötigen **Datumsspalten** in einem Data Warehouse normalerweise häufige Statistikaktualisierungen. Bei jedem Laden von neuen Zeilen in das Data Warehouse werden neue Datumsangaben für Lade- oder Transaktionsvorgänge hinzugefügt. Durch diese Ergänzungen wird die Datenverteilung geändert, und die Statistiken sind nicht mehr aktuell.

Statistiken für die Spalte „Geschlecht“ in einer Kundentabelle müssen unter Umständen nie aktualisiert werden. Wenn davon auszugehen ist, dass die Verteilung zwischen Kunden konstant ist, bewirkt das Hinzufügen neuer Zeilen zur Tabellenvariante keine Änderung der Datenverteilung.

Wenn Ihr Data Warehouse aber nur ein Geschlecht enthält und eine neue Anforderung zu mehr als einem Geschlecht führt, müssen Sie die Statistiken für die Spalte „Geschlecht“ aktualisieren. 

Weitere Informationen finden Sie im Artikel [Statistiken](/sql/relational-databases/statistics/statistics).

### <a name="implement-statistics-management"></a>Implementieren der Statistikverwaltung

Häufig ist es ratsam, den Datenladeprozess zu erweitern, um sicherzustellen, dass die Statistiken am Ende des Ladevorgangs aktualisiert werden. Das Laden von Daten ist der Zeitpunkt, zu dem Tabellen am häufigsten ihre Größe, die Verteilung der Werte oder beides ändern. Daher ist der Ladevorgang ein logischer Ansatzpunkt zum Implementieren einiger Verwaltungsprozesse.

Hier sind einige Richtlinien zur Aktualisierung von Statistiken während des Ladeprozesses angegeben:

- Stellen Sie sicher, dass jede geladene Tabelle mindestens über ein aktualisiertes Statistikobjekt verfügt. Im Rahmen der Statistikaktualisierung werden dann die Informationen zur Tabellengröße (Zeilen- und Seitenanzahl) aktualisiert.
- Konzentrieren Sie sich auf Spalten mit JOIN-, GROUP BY-, ORDER BY- und DISTINCT-Klauseln.
- Erwägen Sie, Spalten vom Typ „aufsteigender Schlüssel“, z. B. Transaktionsdaten, häufiger zu aktualisieren, da diese Werte nicht in das Statistikhistogramm einbezogen werden.
- Erwägen Sie, Spalten mit statischer Verteilung weniger häufig zu aktualisieren.
- Bedenken Sie, dass jedes statistische Objekt der Reihe nach aktualisiert wird. Es ist nicht ideal, einfach `UPDATE STATISTICS <TABLE_NAME>` zu implementieren. Dies gilt besonders für breite Tabellen mit vielen Statistikobjekten.

Weitere Informationen finden Sie unter [Kardinalitätsschätzung (SQL Server)](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics"></a>Beispiele: Erstellen von Statistiken

In diesen Beispielen wird veranschaulicht, wie Sie verschiedene Optionen zum Erstellen von Statistiken verwenden. Die Optionen, die Sie für die einzelnen Spalten verwenden, richten Sie nach den Merkmalen Ihrer Daten und nach der Verwendung der Spalten in Abfragen.

#### <a name="create-single-column-statistics-with-default-options"></a>Erstellen von Einspaltenstatistiken mit Standardoptionen

Zum Erstellen von Statistiken für eine Spalte geben Sie einen Namen für das Statistikobjekt und den Namen der Spalte an.
Bei dieser Syntax werden alle Standardoptionen verwendet. Standardmäßig wird in dedizierten SQL-Pools beim Erstellen von Statistiken eine Stichprobenrate von **20 %** der Tabelle verwendet.

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name]);
```

Beispiel:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1);
```

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Erstellen von Einspaltenstatistiken per Untersuchung jeder Zeile

Die standardmäßige Stichprobenrate von 20 % ist in den meisten Fällen ausreichend. Sie können die Stichprobenrate aber auch anpassen. Verwenden Sie die folgende Syntax, um die gesamte Tabelle zu verwenden:

```sql
CREATE STATISTICS [statistics_name]
    ON [schema_name].[table_name]([column_name])
    WITH FULLSCAN;
```

Beispiel:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH FULLSCAN;
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Erstellen von Einspaltenstatistiken durch Angeben der Stichprobengröße

Eine weitere Option besteht darin, die Stichprobengröße als Prozentsatz anzugeben:

```sql
CREATE STATISTICS col1_stats
    ON dbo.table1 (col1)
    WITH SAMPLE = 50 PERCENT;
```

#### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Erstellen von Einspaltenstatistiken für einen Teil der Zeilen

Sie können auch Statistiken für einen Teil der Zeilen einer Tabelle erstellen, was als gefilterte Statistik bezeichnet wird.

Sie können gefilterte Statistiken beispielsweise verwenden, wenn Sie planen, eine bestimmte Partition einer großen partitionierten Tabelle abzufragen. Indem Sie nur für die Partitionswerte Statistiken erstellen, wird die Genauigkeit der Statistiken erhöht. Außerdem wird die Abfrageleistung verbessert.

In diesem Beispiel werden Statistiken für einen Bereich von Werten erstellt. Die Werte können leicht so definiert werden, dass sie den Werten in einer Partition entsprechen.

```sql
CREATE STATISTICS stats_col1
    ON table1(col1)
    WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Damit der Abfrageoptimierer beim Auswählen des Plans für die verteilte Abfrage die Verwendung von gefilterten Statistiken berücksichtigt, muss die Abfrage in die Definition des Statistikobjekts passen. Im vorherigen Beispiel müssen für die WHERE-Klausel der Abfrage col1-Werte zwischen 2000101 und 20001231 angegeben werden.

#### <a name="create-single-column-statistics-with-all-the-options"></a>Erstellen von Einspaltenstatistiken mit allen Optionen

Sie können die Optionen auch kombinieren. Im folgenden Beispiel wird ein Objekt vom Typ „gefilterte Statistik“ mit einer benutzerdefinierten Stichprobengröße erstellt:

```sql
CREATE STATISTICS stats_col1
    ON table1 (col1)
    WHERE col1 > '2000101' AND col1 < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Die gesamte Referenz finden Sie unter [CREATE STATISTICS (Transact-SQL)](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

#### <a name="create-multi-column-statistics"></a>Erstellen von Mehrspaltenstatistiken

Verwenden Sie zum Erstellen eines mehrspaltigen Statistikobjekts die vorherigen Beispiele, aber geben Sie mehr Spalten an.

> [!NOTE]
> Das Histogramm, das zum Schätzen der Zeilenanzahl im Abfrageergebnis verwendet wird, ist nur für die erste Spalte verfügbar, die in der Definition des Statistikobjekts aufgelistet ist.

In diesem Beispiel basiert das Histogramm auf *product\_category*. Spaltenübergreifende Statistiken werden für *product\_category* und *product\_sub_category* berechnet:

```sql
CREATE STATISTICS stats_2cols
    ON table1 (product_category, product_sub_category)
    WHERE product_category > '2000101' AND product_category < '20001231'
    WITH SAMPLE = 50 PERCENT;
```

Da zwischen *product\_category* und *product\_sub\_category* eine Korrelation besteht, kann ein Mehrspaltenstatistik-Objekt nützlich sein, wenn gleichzeitig auf diese Spalten zugegriffen wird.

#### <a name="create-statistics-on-all-columns-in-a-table"></a>Erstellen von Statistiken für alle Spalten einer Tabelle

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

#### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Verwenden einer gespeicherten Prozedur zum Erstellen von Statistiken für alle Spalten einer Datenbank

Der SQL-Pool verfügt nicht über eine gespeicherte Systemprozedur, die „sp_create_stats“ in SQL Server entspricht. Mit dieser gespeicherten Prozedur wird ein Einzelspaltenstatistik-Objekt für jede Spalte der Datenbank erstellt, die nicht bereits über eine Statistik verfügt.

Das folgende Beispiel ist eine nützliche Einstiegshilfe für den Datenbankentwurf. Sie können diesen Vorgang an Ihre Anforderungen anpassen:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default, 2 Fullscan, 3 Sample
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

Rufen Sie diese Prozedur auf, um damit Statistiken für alle Spalten in der Tabelle mithilfe der Option „Fullscan“ zu erstellen:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Geben Sie „3“ und den Beispielprozentsatz ein, um erfasste Statistiken für alle Spalten in der Tabelle zu erstellen. Die nachfolgende Prozedur verwendet eine Stichprobenrate von 20 %.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

### <a name="examples-update-statistics"></a>Beispiele: Statistikaktualisierung

Sie können wie folgt vorgehen, um Statistiken zu aktualisieren:

- Aktualisieren Sie ein Statistikobjekt. Geben Sie den Namen des Statistikobjekts an, das Sie aktualisieren möchten.
- Aktualisieren Sie alle Statistikobjekte einer Tabelle. Geben Sie anstelle eines bestimmten Statistikobjekts den Namen der Tabelle an.

#### <a name="update-one-specific-statistics-object"></a>Aktualisieren eines bestimmten Statistikobjekts

Verwenden Sie die folgende Syntax, um ein bestimmtes Statistikobjekt zu aktualisieren:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Beispiel:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Indem Sie bestimmte Statistikobjekte aktualisieren, können Sie den Zeit- und Ressourcenaufwand reduzieren, der zum Verwalten von Statistiken erforderlich ist. Für diese Aktion ist es erforderlich, genaue Überlegungen anzustellen, damit die besten Statistikobjekte für die Aktualisierung ausgewählt werden können.

#### <a name="update-all-statistics-on-a-table"></a>Update aller Statistiken für eine Tabelle

Hier ist eine einfache Methode zum Aktualisieren aller Statistikobjekte einer Tabelle:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Beispiel:

```sql
UPDATE STATISTICS dbo.table1;
```

Die UPDATE STATISTICS-Anweisung ist einfach zu verwenden. Bedenken Sie, dass hiermit *alle* Statistiken der Tabelle aktualisiert werden und damit mehr Arbeit als nötig anfällt. 

Wenn die Leistung kein Problem darstellt, ist diese Methode die einfachste und umfassendste Möglichkeit sicherzustellen, dass die Statistiken aktuell sind.

> [!NOTE]
> Beim Aktualisieren aller Statistiken einer Tabelle führt der dedizierte SQL-Pool einen Scan durch, um für jedes Statistikobjekt Stichproben der Tabelle zu nehmen. Wenn die Tabelle groß ist und viele Spalten und Statistiken enthält, kann es effizienter sein, je nach Bedarf einzelne Spalten zu aktualisieren.

Informationen zur Implementierung einer `UPDATE STATISTICS`-Prozedur finden Sie unter [Temporäre Tabellen](develop-tables-temporary.md). Die Implementierungsmethode unterscheidet sich etwas von der obigen `CREATE STATISTICS`-Prozedur, aber das Ergebnis ist identisch.
Die vollständige Syntax finden Sie unter [Aktualisieren der Statistik](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="statistics-metadata"></a>Statistikmetadaten

Es gibt mehrere Systemsichten und -funktionen, die Sie zum Suchen nach Informationen zu Statistiken verwenden können. Beispielsweise können Sie sehen, ob ein Statistikobjekt veraltet ist, indem Sie die STATS_DATE()-Funktion verwenden. Mit der Funktion STATS_DATE() können Sie prüfen, wann Statistiken zuletzt erstellt oder aktualisiert wurden.

#### <a name="catalog-views-for-statistics"></a>Katalogsichten für Statistiken

Diese Systemsichten enthalten Informationen zu Statistiken:

| Katalogsicht | BESCHREIBUNG |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jede Spalte. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jedes Objekt in der Datenbank. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jedes Schema in der Datenbank. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jedes Statistikobjekt. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jede Spalte im Statistikobjekt. Eine Verknüpfung zurück zu sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jede Tabelle (enthält externe Tabellen). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Eine Zeile für jeden Datentyp. |

#### <a name="system-functions-for-statistics"></a>Systemfunktionen für Statistiken

Diese Systemfunktionen sind nützlich für die Arbeit mit Statistiken:

| Systemfunktion | BESCHREIBUNG |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Datum, an dem das Statistikobjekt zuletzt aktualisiert wurde. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |Zusammenfassungsebene und ausführliche Informationen zur Verteilung der Werte gemäß Statistikobjekt. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinieren von Statistikspalten und -funktionen zu einer Sicht

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

### <a name="dbcc-show_statistics-examples"></a>DBCC SHOW_STATISTICS()-Beispiele

Mit DBCC SHOW_STATISTICS() werden die Daten angezeigt, die in einem Statistikobjekt enthalten sind. Diese Daten bestehen aus drei Teilen:

- Header
- Dichtevektor
- Histogramm

Der Header stellt die Metadaten zur Statistik dar. Im Histogramm wird die Verteilung der Werte in der ersten Schlüsselspalte des Statistikobjekts angezeigt. 

Der Dichtevektor misst die spaltenübergreifende Korrelation. Der dedizierte SQL-Pool berechnet Kardinalitätsschätzungen anhand der Daten im Statistikobjekt.

#### <a name="show-header-density-and-histogram"></a>Anzeigen von Header, Dichte und Histogramm

In diesem einfachen Beispiel werden alle drei Teile eines Statistikobjekts angezeigt:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Beispiel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

#### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>Anzeigen eines oder mehrerer Teile von DBCC SHOW_STATISTICS()

Wenn Sie nur bestimmte Teile anzeigen möchten, verwenden Sie die `WITH`-Klausel und geben an, welche Teile dies sein sollen:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
    WITH stat_header, histogram, density_vector
```

Beispiel:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1)
    WITH histogram, density_vector
```

### <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS()-Unterschiede

`DBCC SHOW_STATISTICS()` ist im Vergleich zu SQL Server im dedizierten SQL-Pool strenger implementiert:

- Nicht dokumentierte Features werden nicht unterstützt.
- Verwendung von Stats_stream nicht möglich.
- Ergebnisse für bestimmte Teilmengen von Statistikdaten können nicht verknüpft werden. Beispiel: (STAT_HEADER JOIN DENSITY_VECTOR).
- NO_INFOMSGS kann für die Meldungsunterdrückung nicht festgelegt werden.
- Eckige Klammern um Namen von Statistiken können nicht verwendet werden.
- Spaltennamen können nicht zum Identifizieren von Statistikobjekten verwendet werden.
- Benutzerdefinierter Fehler 2767 wird nicht unterstützt.


## <a name="statistics-in-serverless-sql-pool"></a>Statistiken im serverlosen SQL-Pool

Statistiken werden für ein bestimmtes Dataset (Speicherpfad) pro bestimmter Spalte erstellt.

> [!NOTE]
> Für LOB-Spalten können keine Statistiken erstellt werden.

### <a name="why-use-statistics"></a>Gründe für die Verwendung von Statistiken

Je mehr Informationen zu Ihren Daten der serverlose SQL-Pool hat, desto schneller kann er Abfragen dafür ausführen. Das Erfassen von Statistiken über Ihre Daten ist eine der wichtigsten Maßnahmen, die Sie zur Optimierung von Abfragen ergreifen können. 

Der Abfrageoptimierer im serverlosen SQL-Pool arbeitet kostenorientiert. Die Kosten der verschiedenen Abfragepläne werden verglichen, und dann wird der Plan mit den geringsten Kosten gewählt. In den meisten Fällen wird der Plan gewählt, der am schnellsten ausgeführt wird. 

Wenn der Abfrageoptimierer z.B. schätzt, dass das Datum, nach dem die Abfrage gefiltert wird, eine Zeile zurückgibt, wird er einen Plan auswählen. Wenn er schätzt, dass für das ausgewählte Datum 1 Million Zeilen zurückgegeben werden, wird er einen anderen Plan zurückgegeben.

### <a name="automatic-creation-of-statistics"></a>Automatische Erstellung von Statistiken

Der serverlose SQL-Pool analysiert eingehende Benutzerabfragen auf fehlende Statistiken hin. Wenn Statistiken fehlen, erstellt der Abfrageoptimierer Statistiken für einzelne Spalten im Abfrageprädikat oder der Verknüpfungsbedingung, um Kardinalitätsschätzungen für den Abfrageplan zu verbessern.

Die SELECT-Anweisung löst die automatische Erstellung von Statistiken aus.

> [!NOTE]
> Die automatische Erstellung von Statistiken ist für Parquet-Dateien aktiviert. Für CSV-Dateien müssen Sie Statistiken manuell erstellen, bis die automatische Erstellung von Statistiken für CSV-Dateien unterstützt wird.

Die automatische Erstellung von Statistiken erfolgt synchron, sodass möglicherweise eine geringfügige Verlangsamung bei der Abfrageleistung auftreten kann, sofern in Ihren Spalten keine Statistiken enthalten sind. Die Zeit zum Erstellen von Statistiken für eine einzelne Spalte hängt von der Größe der Zieldateien ab.

### <a name="manual-creation-of-statistics"></a>Manuelle Erstellung von Statistiken

Der serverlose SQL-Pool ermöglicht Ihnen das manuelle Erstellen von Statistiken. Für CSV-Dateien müssen Sie Statistiken manuell erstellen, da die automatische Erstellung von Statistiken für CSV-Dateien nicht aktiviert ist. 

In den folgenden Beispielen finden Sie Anweisungen zum manuellen Erstellen von Statistiken.

### <a name="update-statistics"></a>Statistikaktualisierung

Änderungen an Daten in Dateien sowie das Löschen und Hinzufügen von Dateien führen zu Änderungen der Datenverteilung und bewirken, dass die Statistiken nicht mehr aktuell sind. In diesem Fall müssen die Statistiken aktualisiert werden.

Der serverlose SQL-Pool erstellt Statistiken automatisch neu, wenn Daten erheblich geändert wurden. Jedes Mal, wenn Statistiken automatisch erstellt werden, wird auch der aktuelle Zustand des Datasets gespeichert: Dateipfade, Größen, Datum der letzten Änderung.

Wenn die Statistiken veraltet sind, werden neue erstellt. Der Algorithmus durchläuft die Daten und vergleicht sie mit dem aktuellen Zustand des Datasets. Wenn der Umfang der Änderungen den angegebenen Schwellenwert überschreitet, werden alte Statistiken gelöscht und für das neue Dataset neu erstellt.

Manuelle Statistiken werden nie als veraltet deklariert.

> [!NOTE]
> Die automatische Neuerstellung von Statistiken ist für Parquet-Dateien aktiviert. Für CSV-Dateien müssen Sie Statistiken löschen und manuell erstellen, bis die automatische Erstellung von Statistiken für CSV-Dateien unterstützt wird. In den folgenden Beispielen finden Sie Informationen zum Löschen und Erstellen von Statistiken.

Eine der ersten Fragen bei der Problembehandlung für eine Abfrage sollte lauten: **„Sind die Statistiken auf dem aktuellen Stand?“**

Wenn sich die Anzahl von Zeilen deutlich geändert hat oder es eine wesentliche Änderung bei der Verteilung der Werte für eine Spalte gibt, *ist der Zeitpunkt gekommen*, die Statistiken zu aktualisieren.

> [!NOTE]
> Immer wenn sich die Verteilung der Werte einer Spalte wesentlich ändert, sollten Sie die Statistiken – unabhängig vom Zeitpunkt des letzten Updates – aktualisieren.

### <a name="implement-statistics-management"></a>Implementieren der Statistikverwaltung

Sie sollten Ihre Datenpipeline erweitern, um sicherzustellen, dass die Statistiken aktualisiert werden, wenn Daten durch Hinzufügen, Löschen oder Ändern von Dateien erheblich verändert werden.

Im Folgenden finden Sie einige Richtlinien zur Aktualisierung von Statistiken:

- Stellen Sie sicher, dass das Dataset mindestens über ein aktualisiertes Statistikobjekt verfügt. Im Rahmen der Statistikaktualisierung werden dann die Informationen zur Größe (Zeilen- und Seitenanzahl) aktualisiert.
- Konzentrieren Sie sich auf Spalten mit WHERE-, JOIN-, GROUP BY-, ORDER BY- und DISTINCT-Klauseln.
- Aktualisieren Sie Spalten vom Typ „aufsteigender Schlüssel“, z. B. Transaktionsdaten, häufiger, da diese Werte nicht in das Statistikhistogramm einbezogen werden.
- Aktualisieren Sie Spalten mit statischer Verteilung weniger häufig.

Weitere Informationen finden Sie unter [Kardinalitätsschätzung (SQL Server)](/sql/relational-databases/performance/cardinality-estimation-sql-server).

### <a name="examples-create-statistics-for-column-in-openrowset-path"></a>Beispiele: Erstellen von Statistiken für eine Spalte im OPENROWSET-Pfad

In den folgenden Beispielen wird veranschaulicht, wie verschiedene Optionen zum Erstellen von Statistiken verwendet werden. Die Optionen, die Sie für die einzelnen Spalten verwenden, richten Sie nach den Merkmalen Ihrer Daten und nach der Verwendung der Spalten in Abfragen.

> [!NOTE]
> Im Moment können Sie nur einspaltige Statistiken erstellen.
>
> Zum Ausführen von „sp_create_openrowset_statistics“ und „sp_drop_openrowset_statistics“ sind folgende Berechtigungen erforderlich: ADMINISTER BULK OPERATIONS- oder ADMINISTER DATABASE BULK OPERATIONS.

Die folgende gespeicherte Prozedur wird zur Erstellung von Statistiken verwendet:

```sql
sys.sp_create_openrowset_statistics [ @stmt = ] N'statement_text'
```

Argumente: [ @stmt = ] N'statement_text' – Gibt eine Transact-SQL-Anweisung an, die Spaltenwerte zurückgibt, die für Statistiken verwendet werden sollen. Mit TABLESAMPLE können Sie Stichproben von zu verwendenden Daten angeben. Wenn TABLESAMPLE nicht angegeben wird, wird FULLSCAN verwendet.

```syntaxsql
<tablesample_clause> ::= TABLESAMPLE ( sample_number PERCENT )
```

> [!NOTE]
> Die CSV-Stichprobenentnahme funktioniert derzeit nicht. Für CSV wird nur FULLSCAN unterstützt.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Erstellen von Einspaltenstatistiken per Untersuchung jeder Zeile

Stellen Sie zum Erstellen von Statistiken über eine Spalte eine Abfrage bereit, die die Spalte zurückgibt, für die Sie Statistiken benötigen.

Wenn Sie nichts anderes angeben, verwendet der serverlose SQL-Pool bei der Erstellung von Statistiken standardmäßig 100 % der im Dataset bereitgestellten Daten.

Um z. B. Statistiken mit Standardoptionen (FULLSCAN) für eine Jahresspalte des Datasets auf der Grundlage der Datei „population.csv“ zu erstellen:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT year
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/csv/population/population.csv'',
        FORMAT = ''CSV'',
        FIELDTERMINATOR ='','',
        ROWTERMINATOR = ''\n''
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
) AS [r]
'
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Erstellen von Einspaltenstatistiken durch Angeben der Stichprobengröße

Sie können die Stichprobengröße als Prozentwert angeben:

```sql
/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-update-statistics"></a>Beispiele: Statistikaktualisierung

Zum Aktualisieren von Statistiken müssen Sie Statistiken löschen und erstellen. Die folgende gespeicherte Prozedur wird zum Löschen von Statistiken verwendet:

```sql
sys.sp_drop_openrowset_statistics [ @stmt = ] N'statement_text'
```

> [!NOTE]
> Zum Ausführen von „sp_create_openrowset_statistics“ und „sp_drop_openrowset_statistics“ sind folgende Berechtigungen erforderlich: ADMINISTER BULK OPERATIONS- oder ADMINISTER DATABASE BULK OPERATIONS.

Argumente: [ @stmt = ] N'statement_text' – Gibt dieselbe Transact-SQL-Anweisung an, die bei der Erstellung der Statistiken verwendet wurde.

Um die Statistiken für die Jahresspalte im Dataset, das auf der Datei „population.csv“ basiert, zu aktualisieren, müssen Sie die Statistiken löschen und erstellen:

```sql
EXEC sys.sp_drop_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/

EXEC sys.sp_create_openrowset_statistics N'SELECT payment_type
FROM OPENROWSET(
        BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/year=2018/month=6/*.parquet'',
         FORMAT = ''PARQUET''
    ) AS [nyc]
    TABLESAMPLE(5 PERCENT)
'
```

### <a name="examples-create-statistics-for-external-table-column"></a>Beispiele: Erstellen von Statistiken für externe Tabellenspalten

In den folgenden Beispielen wird veranschaulicht, wie verschiedene Optionen zum Erstellen von Statistiken verwendet werden. Die Optionen, die Sie für die einzelnen Spalten verwenden, richten Sie nach den Merkmalen Ihrer Daten und nach der Verwendung der Spalten in Abfragen.

> [!NOTE]
> Im Moment können Sie nur einspaltige Statistiken erstellen.

Zum Erstellen von Statistiken für eine Spalte geben Sie einen Namen für das Statistikobjekt und den Namen der Spalte an.

```sql
CREATE STATISTICS statistics_name
ON { external_table } ( column )
    WITH
        { FULLSCAN
          | [ SAMPLE number PERCENT ] }
        , { NORECOMPUTE }
```

Argumente: external_table – Gibt die externe Tabelle an, für die Statistiken erstellt werden sollen.

FULLSCAN berechnet die Statistiken, indem alle Zeilen überprüft werden. FULLSCAN und SAMPLE 100 PERCENT führen zu gleichen Ergebnissen. FULLSCAN kann nicht in Verbindung mit der Option SAMPLE verwendet werden.

SAMPLE Zahl PERCENT – Gibt den ungefähren Prozentsatz oder die ungefähre Anzahl von Zeilen in der Tabelle oder indizierten Sicht an, die vom Abfrageoptimierer beim Erstellen von Statistiken verwendet werden sollen. Die Zahl kann zwischen 0 und 100 liegen.

SAMPLE kann nicht in Verbindung mit der Option FULLSCAN verwendet werden.

> [!NOTE]
> Die CSV-Stichprobenentnahme funktioniert derzeit nicht. Für CSV wird nur FULLSCAN unterstützt.

#### <a name="create-single-column-statistics-by-examining-every-row"></a>Erstellen von Einspaltenstatistiken per Untersuchung jeder Zeile

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

#### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Erstellen von Einspaltenstatistiken durch Angeben der Stichprobengröße

```sql
-- following sample creates statistics with sampling 20%
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH SAMPLE 5 percent, NORECOMPUTE
```

### <a name="examples-update-statistics"></a>Beispiele: Statistikaktualisierung

Zum Aktualisieren von Statistiken müssen Sie Statistiken löschen und erstellen. Löschen Sie zunächst die Statistik:

```sql
DROP STATISTICS census_external_table.sState
```

Und erstellen Sie eine Statistik:

```sql
CREATE STATISTICS sState
    on census_external_table (STATENAME)
    WITH FULLSCAN, NORECOMPUTE
```

### <a name="statistics-metadata"></a>Statistikmetadaten

Es gibt mehrere Systemsichten und -funktionen, die Sie zum Suchen nach Informationen zu Statistiken verwenden können. Beispielsweise können Sie sehen, ob ein Statistikobjekt veraltet ist, indem Sie die STATS_DATE()-Funktion verwenden. Mit der Funktion STATS_DATE() können Sie prüfen, wann Statistiken zuletzt erstellt oder aktualisiert wurden.

> [!NOTE]
> Statistikmetadaten sind nur für Spalten externer Tabellen verfügbar. Statistikmetadaten sind nicht für OPENROWSET-Spalten verfügbar.

#### <a name="catalog-views-for-statistics"></a>Katalogsichten für Statistiken

Diese Systemsichten enthalten Informationen zu Statistiken:

| Katalogsicht                                                 | BESCHREIBUNG                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jede Spalte.                                     |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jedes Objekt in der Datenbank.                     |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jedes Schema in der Datenbank.                     |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jedes Statistikobjekt.                          |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jede Spalte im Statistikobjekt. Eine Verknüpfung zurück zu sys.columns. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jede Tabelle (enthält externe Tabellen).           |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Eine Zeile für jeden Datentyp.                                  |

#### <a name="system-functions-for-statistics"></a>Systemfunktionen für Statistiken

Diese Systemfunktionen sind nützlich für die Arbeit mit Statistiken:

| Systemfunktion                                              | BESCHREIBUNG                                  |
| :----------------------------------------------------------- | :------------------------------------------- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Datum, an dem das Statistikobjekt zuletzt aktualisiert wurde. |

#### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Kombinieren von Statistikspalten und -funktionen zu einer Sicht

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
JOIN    sys.tables          AS tb ON    co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm ON    tb.[schema_id]      = sm.[schema_id]
WHERE   st.[user_created] = 1
;
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur weiteren Verbesserung der Abfrageleistung bei einem dedizierten SQL-Pool finden Sie unter [Überwachen Ihrer Workload](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) und [Bewährte Methoden für den dedizierten SQL-Pool](best-practices-sql-pool.md#maintain-statistics).

Informationen zur weiteren Verbesserung der Abfrageleistung bei einem serverlosen SQL-Pool finden Sie unter [Bewährte Methoden für den serverlosen SQL-Pool](best-practices-sql-on-demand.md)
