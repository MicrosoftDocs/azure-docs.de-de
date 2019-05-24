---
title: Temporäre Tabellen in SQL Data Warehouse | Microsoft Docs
description: Wichtige Anleitungen zur Verwendung von temporären Tabellen und Vorstellen der Grundsätze von temporären Tabellen auf Sitzungsebene.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56c15a9505b3f0e4344c9164268082da1ff7cc22
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851569"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Temporäre Tabellen in SQL Data Warehouse
Dieser Artikel enthält wichtige Anleitungen zur Verwendung von temporären Tabellen. Zudem werden die Grundsätze von temporären Tabellen auf Sitzungsebene behandelt. Mit den Informationen in diesem Artikel können Sie Ihren Code modularisieren und sowohl die Wiederverwendbarkeit als auch die Einfachheit der Verwaltung für den Code verbessern.

## <a name="what-are-temporary-tables"></a>Was sind temporäre Tabellen?
Temporäre Tabellen sind nützlich bei der Verarbeitung von Daten – vor allem bei Transformationen, bei denen die Zwischenergebnisse vorübergehend sind. In SQL Data Warehouse befinden sich temporäre Tabellen auf Sitzungsebene.  Sie sind nur für die Sitzung sichtbar, in der sie erstellt wurden, und werden automatisch verworfen, wenn die Sitzung abgemeldet wird.  Temporäre Tabellen verfügen über einen Leistungsvorteil, da ihre Ergebnisse nicht in den Remotespeicher geschrieben werden, sondern in den lokalen Speicher.

## <a name="create-a-temporary-table"></a>Erstellen einer temporären Tabelle
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
``` 

> [!NOTE]
> `CTAS` ist ein leistungsfähiger Befehl und bietet den zusätzlichen Vorteil, dass er den Speicherplatz für das Transaktionsprotokoll effizient verwendet. 
> 
> 

## <a name="dropping-temporary-tables"></a>Löschen von temporären Tabellen
Beim Erstellen einer neuen Sitzung sollten keine temporären Tabellen vorhanden sein.  Wenn Sie aber dieselbe gespeicherte Prozedur aufrufen, die eine temporäre Tabelle mit dem gleichen Namen erstellt, können Sie mit einer einfachen Überprüfung auf das Vorhandensein per `DROP` sicherstellen, dass Ihre `CREATE TABLE`-Anweisungen erfolgreich sind. Dies wird im folgenden Beispiel veranschaulicht:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

In Bezug auf die Codekonsistenz ist es eine bewährte Methode, dieses Muster sowohl für Tabellen als auch für temporäre Tabellen zu verwenden.  Es ist auch eine gute Idee, mit `DROP TABLE` temporäre Tabellen zu entfernen, wenn Sie sie in Ihrem Code nicht mehr benötigen.  Bei der Entwicklung gespeicherter Prozeduren ist es üblich, die Befehle zum Löschen am Ende einer Prozedur zu bündeln, um sicherzustellen, dass diese Objekte bereinigt werden.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularisieren von Code
Da temporäre Tabellen in einer Benutzersitzung an einer beliebigen Stelle angezeigt werden können, kann dies zur Modularisierung des Anwendungscodes genutzt werden.  Die folgende gespeicherte Prozedur generiert z. B. DDL, um alle Statistiken in der Datenbank nach Statistiknamen zu aktualisieren.

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

In dieser Phase ist die einzige Aktion, die durchgeführt wurde, die Erstellung einer gespeicherten Prozedur. Dabei wird die temporäre Tabelle „#stats_ddl“ mit DDL-Anweisungen generiert.  Diese gespeicherte Prozedur verwirft „#stats_ddl“, wenn sie bereits vorhanden ist, um sicherzustellen, dass kein Fehler auftritt, wenn sie innerhalb einer Sitzung mehr als einmal ausgeführt wird.  Da am Ende der gespeicherten Prozedur keine `DROP TABLE`-Anweisung vorhanden ist, wird die erstellte Tabelle nach Abschluss der gespeicherten Prozedur beibehalten, damit sie außerhalb der gespeicherten Prozedur gelesen werden kann.  In SQL Data Warehouse ist es im Gegensatz zu anderen SQL Server-Datenbanken möglich, diese temporäre Tabelle außerhalb der Prozedur zu verwenden, mit der sie erstellt wurde.  Temporäre SQL Data Warehouse-Tabellen können innerhalb der Sitzung **überall** verwendet werden. Dies kann zu modularerem und besser verwaltbarem Code führen, wie im folgenden Beispiel dargestellt:

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

## <a name="temporary-table-limitations"></a>Einschränkungen der temporären Tabelle
SQL Data Warehouse weist eine Reihe von Einschränkungen bei der Implementierung von temporären Tabellen auf.  Derzeit werden nur temporäre Tabellen für den Sitzungsbereich unterstützt.  Globale temporäre Tabellen werden nicht unterstützt.  Außerdem können Sichten nicht in temporären Tabellen erstellt werden.  Temporäre Tabellen können nur mit Hash- oder Roundrobin-Verteilung erstellt werden.  Die Verteilung replizierter temporärer Tabellen wird nicht unterstützt. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Entwickeln von Tabellen finden Sie in der [Tabellenübersicht](sql-data-warehouse-tables-overview.md).

