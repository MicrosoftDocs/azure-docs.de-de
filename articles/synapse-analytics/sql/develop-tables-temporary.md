---
title: Verwenden von temporären Tabellen in Synapse SQL
description: Grundlegender Leitfaden für die Verwendung temporärer Tabellen in Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 06faa1da71331c299245a93af96166880e7732de
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451786"
---
# <a name="temporary-tables-in-synapse-sql"></a>Temporäre Tabellen in Synapse SQL

Dieser Artikel enthält wichtige Anleitungen zur Verwendung von temporären Tabellen. Zudem werden die Grundsätze von temporären Tabellen auf Sitzungsebene in Synapse SQL behandelt. 

Die Ressourcen sowohl des dedizierten SQL-Pools als auch des serverlosen SQL-Pools können temporäre Tabellen verwenden. Beim serverlosen SQL-Pool gibt es Einschränkungen, die am Ende dieses Artikels erläutert werden. 

## <a name="temporary-tables"></a>Temporäre Tabellen

Temporäre Tabellen sind nützlich bei der Verarbeitung von Daten – vor allem bei Transformationen, bei denen die Zwischenergebnisse vorübergehend sind. Bei Synapse SQL befinden sich temporäre Tabellen auf Sitzungsebene.  Sie sind nur für die Sitzung sichtbar, in der sie erstellt wurden. Daher werden sie automatisch gelöscht, wenn diese Sitzung abgemeldet wird. 

## <a name="temporary-tables-in-dedicated-sql-pool"></a>Temporäre Tabellen im dedizierten SQL-Pool

In der Ressource des dedizierten SQL-Pools bieten temporäre Tabellen einen Leistungsvorteil, weil deren Ergebnisse nicht in den Remotespeicher, sondern in den lokalen Speicher geschrieben werden.

### <a name="create-a-temporary-table"></a>Erstellen einer temporären Tabelle

Temporäre Tabellen werden erstellt, indem dem Tabellennamen `#` als Präfix vorangestellt wird.  Beispiel:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Temporäre Tabellen können auch mit `CTAS` auf genau die gleiche Weise erstellt werden:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS` ist ein leistungsfähiger Befehl und bietet den zusätzlichen Vorteil, dass er den Speicherplatz für das Transaktionsprotokoll effizient verwendet. 
> 
> 

### <a name="drop-temporary-tables"></a>Löschen temporärer Tabellen

Beim Erstellen einer neuen Sitzung sollten keine temporären Tabellen vorhanden sein.  Wenn Sie jedoch dieselbe gespeicherte Prozedur aufrufen, die eine temporäre Tabelle mit dem gleichen Namen erstellt, können Sie mit einer einfachen Überprüfung auf das Vorhandensein per `DROP` sicherstellen, dass Ihre `CREATE TABLE`-Anweisungen erfolgreich sind: 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

In Bezug auf die Codekonsistenz ist es eine bewährte Methode, dieses Muster sowohl für Tabellen als auch für temporäre Tabellen zu verwenden.  Es empfiehlt sich auch, mit `DROP TABLE` temporäre Tabellen zu entfernen, wenn diese nicht mehr benötigt werden.  

Bei der Entwicklung gespeicherter Prozeduren ist es üblich, die Befehle zum Löschen am Ende einer Prozedur zu bündeln, um sicherzustellen, dass diese Objekte bereinigt werden.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularize-code"></a>Modularisieren von Code

Temporäre Tabellen können an beliebiger Stelle in einer Benutzersitzung verwendet werden. Diese Funktion kann dann genutzt werden, um Ihnen bei der Modularisierung Ihres Anwendungscodes zu helfen.  Die folgende gespeicherte Prozedur generiert zum Veranschaulichen z. B. DDL-Code, um alle Statistiken in der Datenbank nach Statistiknamen zu aktualisieren:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

In dieser Phase ist die einzige Aktion, die durchgeführt wurde, die Erstellung einer gespeicherten Prozedur, die die temporäre Tabelle „#stats_ddl“ erzeugt.  Die gespeicherte Prozedur löscht „#stats_ddl“, wenn sie bereits vorhanden ist. Dieser Vorgang stellt sicher, dass kein Fehler auftritt, wenn er mehr als einmal innerhalb einer Sitzung ausgeführt wird.  

Da am Ende der gespeicherten Prozedur keine `DROP TABLE`-Anweisung vorhanden ist, wird die erstellte Tabelle nach Abschluss der gespeicherten Prozedur beibehalten, und sie kann außerhalb der gespeicherten Prozedur gelesen werden.  

Im Gegensatz zu anderen SQL Server-Datenbanken können Sie mit Synapse SQL die temporäre Tabelle außerhalb der Prozedur verwenden, die sie erstellt hat.  Die temporären Tabellen, die über den dedizierten SQL-Pool erstellt wurden, können **überall** innerhalb der Sitzung verwendet werden. Daher verfügen Sie über mehr modularen und verwaltbaren Code, wie im folgenden Beispiel gezeigt:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Einschränkungen der temporären Tabelle

Beim dedizierten SQL-Pool gibt es einige Implementierungseinschränkungen für temporäre Tabellen:

- Nur temporäre Tabellen werden für den Sitzungsbereich unterstützt.  Globale temporäre Tabellen werden nicht unterstützt.
- Sichten können nicht in temporären Tabellen erstellt werden.
- Temporäre Tabellen können nur mit Hash- oder Roundrobin-Verteilung erstellt werden.  Die Verteilung replizierter temporärer Tabellen wird nicht unterstützt. 

## <a name="temporary-tables-in-serverless-sql-pool"></a>Temporäre Tabellen im serverlosen SQL-Pool

Temporäre Tabellen im serverlosen SQL-Pool werden zwar unterstützt, ihre Verwendung ist aber begrenzt. Sie können nicht in Abfragen verwendet werden, die auf Dateien ausgerichtet sind. 

Sie können z. B. eine temporäre Tabelle nicht mit Daten aus Dateien im Speicher verbinden. Die Anzahl der temporären Tabellen ist auf 100 begrenzt, und ihre Gesamtgröße ist auf 100 MB beschränkt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwickeln von Tabellen finden Sie im Artikel [Entwerfen von Tabellen mithilfe von Synapse SQL-Ressourcen](develop-tables-overview.md).

