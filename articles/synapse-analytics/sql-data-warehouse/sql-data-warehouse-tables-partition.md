---
title: Partitionieren von Tabellen
description: Empfehlungen und Beispiele für die Verwendung von Tabellenpartitionen in einem dedizierten SQL-Pool
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f65c1d6fda09d7762a59fb5a932a72ad706a767a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448024"
---
# <a name="partitioning-tables-in-dedicated-sql-pool"></a>Partitionierungstabellen im dedizierten SQL-Pool

Empfehlungen und Beispiele für die Verwendung von Tabellenpartitionen in einem dedizierten SQL-Pool

## <a name="what-are-table-partitions"></a>Was sind Tabellenpartitionen?

Durch Tabellenpartitionen können Sie Ihre Daten in kleinere Gruppen von Daten unterteilen. In den meisten Fällen werden Tabellenpartitionen in einer Datumsspalte erstellt. Die Partitionierung wird für alle Tabellentypen in einem dedizierten SQL-Pool unterstützt, einschließlich der Typen gruppierter Columnstore, gruppierter Index und Heap. Außerdem wird die Partitionierung für alle Verteilungstypen unterstützt, z.B. Hash- oder Roundrobin-Verteilung.  

Durch die Partitionierung können sich Vorteile für die Wartung und die Abfrageleistung ergeben. Ob sich Vorteile für beide oder nur einen dieser Punkte ergeben, hängt davon ab, wie Daten geladen werden und ob dieselbe Spalte für beide Zwecke genutzt werden kann. Die Partitionierung kann nämlich nur für eine Spalte durchgeführt werden.

### <a name="benefits-to-loads"></a>Vorteile für Lasten

Der Hauptvorteil der Partitionierung in einem dedizierten SQL-Pool ist die Verbesserung der Effizienz und Leistung beim Laden von Daten, indem Partitionen gelöscht, gewechselt und zusammengeführt werden. In den meisten Fällen werden Daten nach einer Datumsspalte partitioniert, die eng an die Reihenfolge gebunden ist, mit der die Daten in den SQL-Pool geladen werden. Einer der größten Vorteile bei der Verwendung von Partitionen zum Verwalten von Daten ist die Vermeidung der Transaktionsprotokollierung. Das schlichte Einfügen, Aktualisieren oder Löschen von Daten kann zwar der einfachste Ansatz sein, aber wenn Sie etwas Planung und Arbeit investieren, kann die Leistung durch die Verwendung der Partitionierung während des Ladevorgangs erheblich verbessert werden.

Sie können den Partitionswechsel einsetzen, um einen Abschnitt einer Tabelle schnell zu entfernen oder auszutauschen.  So kann beispielsweise eine Umsatzfaktentabelle erstellt werden, die nur Daten für die letzten 36 Monate enthält. Am Monatsende wird jeweils der älteste Verkaufsdatenmonat aus der Tabelle gelöscht.  Sie können eine delete-Anweisung verwenden, um jeweils die Daten für den ältesten Monat zu löschen. 

Das Löschen einer großen Datenmenge Zeile für Zeile mit einer delete-Anweisung kann aber zu lange dauern und das Risiko für große Transaktionen erhöhen, deren Rollback sehr viel Zeit in Anspruch nimmt, wenn es zu Fehlern kommt. Ein wesentlich besserer Ansatz besteht darin, die älteste Partition der Daten zu löschen. Während das Löschen der einzelnen Zeilen Stunden dauern kann, werden für das Löschen einer gesamten Partition meist nur wenige Sekunden benötigt.

### <a name="benefits-to-queries"></a>Vorteile für Abfragen

Die Partitionierung kann auch verwendet werden, um die Abfrageleistung zu verbessern. Eine Abfrage, die einen Filter auf partitionierte Daten anwendet, kann die Prüfung auf die qualifizierten Partitionen beschränken. Diese Methode der Filterung kann eine vollständige Überprüfung der Tabelle vermeiden und nur eine kleinere Teilmenge von Daten überprüfen. Aufgrund der Einführung von gruppierten Columnstore-Indizes sind die Leistungsvorteile bei der Prädikatbeseitigung nicht so groß, aber in einigen Fällen können sich trotzdem Vorteile für Abfragen ergeben. 

Beispiel: Wenn eine Umsatzfaktentabelle auf Grundlage des Verkaufsdatumsfelds in 36 Monate partitioniert wird, kann bei Abfragen, die das Verkaufsdatum als Filter verwenden, das Durchsuchen von Partitionen übersprungen werden, die nicht dem Filter entsprechen.

## <a name="sizing-partitions"></a>Anpassen der Größe von Partitionen

Die Partitionierung kann zwar verwendet werden, um die Leistung in einigen Szenarien zu verbessern, aber das Erstellen einer Tabelle mit **zu vielen** Partitionen kann die Leistung unter Umständen beeinträchtigen.  Dies gilt besonders für gruppierte Columnstore-Tabellen. 

Es muss klar sein, wann sich der Einsatz der Partitionierung anbietet und wie viele Partitionen erstellt werden sollten, damit die Partitionierung hilfreich ist. Es gibt keine genaue Vorgabe, welche Anzahl von Partitionen zu hoch ist. Dies hängt von Ihren Daten und außerdem davon ab, wie viele Partitionen gleichzeitig geladen werden. Ein erfolgreiches Partitionierungsschema hat normalerweise Dutzende bis Hunderte von Partitionen, nicht Tausende.

Beim Erstellen von Partitionierungen für **gruppierte Columnstore**-Tabellen ist es wichtig zu beachten, wie viele Zeilen zu jeder Partition gehören werden. Für eine optimale Komprimierung und Leistung von gruppierten Columnstore-Tabellen sind mindestens 1 Million Zeilen pro Verteilung und Partition erforderlich. Bereits vor der Erstellung von Partitionen teilt ein dedizierter SQL-Pool jede Tabelle auf 60 verteilte Datenbanken auf. 

Jegliche Partitionierungen, die einer Tabelle hinzugefügt werden, werden zusätzlich zu den im Hintergrund erstellten Verteilungen durchgeführt. Für dieses Beispiel bedeutet das Folgendes: Wenn die Umsatzfaktentabelle 36 Monatspartitionen enthält und ein dedizierter SQL-Pool 60 Verteilungen umfasst, muss die Umsatzfaktentabelle mindestens 60 Millionen Zeilen pro Monat umfassen (oder 2,1 Milliarden Zeilen, wenn alle Monate aufgefüllt sind). Wenn eine Tabelle weniger Zeilen als das empfohlene Minimum an Zeilen pro Partition enthält, sollten Sie die Verwendung von weniger Partitionen erwägen, um die Anzahl von Zeilen pro Partition zu erhöhen. 

Weitere Informationen finden Sie im Artikel zur [Indizierung](sql-data-warehouse-tables-index.md), in dem Abfragen erläutert werden, die die Qualität von gruppierten Columnstore-Indizes bewerten können.

## <a name="syntax-differences-from-sql-server"></a>Syntaxunterschiede zu SQL Server

Ein dedizierter SQL-Pool bietet eine einfachere Möglichkeit zum Definieren von Partitionen als SQL Server. Partitionierungsfunktionen und -schemas werden in einem dedizierten SQL-Pool nicht so wie in SQL Server verwendet. Stattdessen müssen Sie lediglich die partitionierte Spalte und die Grenzpunkte identifizieren. 

Die Syntax der Partitionierung kann gegenüber SQL Server leicht variieren, aber die grundlegenden Konzepte sind identisch. SQL Server und ein dedizierter SQL-Pool unterstützen eine Partitionsspalte pro Tabelle, bei der es sich um eine Bereichspartition handeln kann. Weitere Informationen zur Partitionierung finden Sie unter [Partitionierte Tabellen und Indizes](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Im folgenden Beispiel wird mit der [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)-Anweisung die Tabelle „FactInternetSales“ nach der Spalte „OrderDateKey“ partitioniert:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrieren der Partitionierung von SQL Server

Gehen Sie wie folgt vor, um SQL Server-Partitionsdefinitionen zu einem dedizierten SQL-Pool zu migrieren:

- Entfernen Sie das SQL Server-[Partitionsschema](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Fügen Sie die Definition der [Partitionsfunktion](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) zu CREATE TABLE hinzu.

Wenn Sie eine partitionierte Tabelle von einer SQL Server-Instanz migrieren, können Sie mit dem unten angegebenen SQL-Code die Anzahl von Zeilen in jeder Partition ermitteln. Beachten Sie Folgendes: Wenn für einen dedizierten SQL-Pool die gleiche Partitionierungsgranularität verwendet wird, verringert sich die Anzahl von Zeilen pro Partition um den Faktor 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partitionswechsel

Ein dedizierter SQL-Pool unterstützt das Aufteilen, Zusammenführen und Wechseln von Partitionen. Jede dieser Funktionen wird mithilfe der [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)-Anweisung ausgeführt.

Für den Wechsel zweier Partitionen zwischen zwei Tabellen müssen Sie sicherstellen, dass die Partitionen an ihren jeweiligen Grenzen ausgerichtet sind und die Tabellendefinitionen übereinstimmen. Da keine Überprüfungseinschränkungen verfügbar sind, um den Bereich der Werte in einer Tabelle zu erzwingen, muss die Quelltabelle die gleichen Partitionsgrenzen enthalten wie die Zieltabelle. Ist dies nicht der Fall, tritt ein Fehler beim Partitionswechsel auf, da die Partitionsmetadaten nicht synchronisiert werden.

### <a name="how-to-split-a-partition-that-contains-data"></a>Aufteilen einer Partition mit Daten

Die effizienteste Methode, um eine Partition zu teilen, die bereits Daten enthält, ist die Verwendung einer `CTAS` -Anweisung. Wenn es sich bei der partitionierten Tabelle um einen gruppierten Columnstore handelt, muss die Tabellenpartition leer sein, bevor sie aufgeteilt werden kann.

Im folgenden Beispiel wird eine partitionierte Columnstore-Tabelle erstellt. In jeder Partition wird eine Zeile eingefügt:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

Die folgende Abfrage sucht die Anzahl der Zeilen über die Katalogsicht `sys.partitions`:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Der folgende Split-Befehl erhält eine Fehlermeldung:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Level 15, State 1, Line 44 SPLIT clause of ALTER PARTITION statement failed because the partition is not empty. Wenn ein columnstore-Index für die Tabelle vorhanden ist, können nur leere Partitionen aufgeteilt werden. Deaktivieren Sie ggf. den Columnstore-Index vor Ausgabe der ALTER PARTITION-Anweisung, und erstellen Sie dann nach Abschluss von ALTER PARTITION den Columnstore-Index neu.

Sie können aber auch `CTAS` zum Erstellen einer neuen Datentabelle verwenden.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Ein Wechsel ist zulässig, da die Partitionsgrenzen ausgerichtet sind. Dadurch verbleibt die Quelltabelle mit einer leeren Partition, die Sie später aufteilen können.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Sie müssen nun lediglich die Daten mit `CTAS` an die neuen Partitionsgrenzen anpassen und dann wieder in die Haupttabelle zurückführen.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Nach dem Verschieben der Daten ist es sinnvoll, die Statistiken für die Zieltabelle zu aktualisieren. Durch das Aktualisieren der Statistiken wird sichergestellt, dass diese die neue Aufteilung der Daten in ihren jeweiligen Partitionen genau widerspiegeln.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Laden neuer Daten in Partitionen, die Daten enthalten, in einem Schritt

Das Laden von Daten in Partitionen mit Partitionswechsel ist eine praktische Möglichkeit, neue Daten in einer Tabelle zu platzieren, die für Benutzer nicht sichtbar ist.  Es kann für stark ausgelastete Systeme eine Herausforderung sein, den mit dem Partitionswechsel verbundenen Sperrkonflikt zu verarbeiten.  

Um die vorhandenen Daten in einer Partition zu löschen, wurde früher ein `ALTER TABLE`-Befehl benötigt, um die Daten auszutauschen.  Anschließend wurde ein weiterer `ALTER TABLE`-Befehl benötigt, um die neuen Daten einzulesen.  

In einem dedizierten SQL-Pool wird die Option `TRUNCATE_TARGET` im Befehl `ALTER TABLE` unterstützt.  Mit `TRUNCATE_TARGET` überschreibt der `ALTER TABLE`-Befehl vorhandene Daten in der Partition mit neuen Daten.  Nachfolgend sehen Sie ein Beispiel, das mit `CTAS` eine neue Tabelle mit den vorhandenen Daten erstellt, neue Daten einfügt, dann alle Daten wieder in die Zieltabelle umschaltet und die vorhandenen Daten überschreibt.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Datenquellen-Steuerelement für die Tabellenpartitionierung

Um ein **Rosten** der Tabellendefinition in Ihrem Quellcodeverwaltungssystem zu vermeiden, sollten Sie die folgende Vorgehensweise befolgen:

1. Erstellen der Tabelle als partitionierte Tabelle ohne Partitionswerte

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT` für die Tabelle als Teil des Bereitstellungsprozesses durch:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Bei diesem Ansatz bleibt der Code in der Quellcodeverwaltung statisch, und die Partitionierungsgrenzwerte können sich im Laufe der Zeit mit dem SQL-Pool dynamisch entwickeln.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwickeln von Tabellen finden Sie in den Artikeln zur [Tabellenübersicht](sql-data-warehouse-tables-overview.md).
