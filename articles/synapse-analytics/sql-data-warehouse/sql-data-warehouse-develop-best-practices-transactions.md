---
title: Optimieren von Transaktionen
description: Hier erfahren Sie, wie Sie die Leistung Ihres Transaktionscodes im dedizierten SQL-Pool optimieren und gleichzeitig das Risiko für lange Rollbacks minimieren können.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 46a165ea7fa21c02e859c16027086695f1f378c3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462790"
---
# <a name="optimizing-transactions-in-dedicated-sql-pool-in-azure-synapse-analytics"></a>Optimieren von Transaktionen bei einem dedizierten SQL-Pool in Azure Synapse Analytics

Hier erfahren Sie, wie Sie die Leistung Ihres Transaktionscodes im dedizierten SQL-Pool optimieren und gleichzeitig das Risiko für lange Rollbacks minimieren können.

## <a name="transactions-and-logging"></a>Transaktionen und Protokollierung

Transaktionen sind eine wichtige Komponente einer relationalen SQL-Pool-Engine. Transaktionen werden während der Datenänderung verwendet. Diese Transaktionen können expliziter oder impliziter Art sein. Einzelne INSERT-, UPDATE- und DELETE-Anweisungen sind Beispiele für implizite Transaktionen. Für explizite Transaktionen wird BEGIN TRAN, COMMIT TRAN oder ROLLBACK TRAN verwendet. Explizite Transaktionen werden normalerweise verwendet, wenn mehrere Änderungsanweisungen zu einer einzelnen atomischen Einheit zusammengefasst werden müssen.

Änderungen am SQL-Pool werden mithilfe von Transaktionsprotokollen nachverfolgt. Jede Verteilung verfügt über ein eigenes Transaktionsprotokoll. Schreibvorgänge für Transaktionsprotokolle werden automatisch durchgeführt. Es ist keine Konfiguration erforderlich. Mit diesem Prozess wird der Schreibvorgang zwar sichergestellt, aber er bedeutet auch Mehraufwand für das System. Sie können diese negative Auswirkung reduzieren, indem Sie Code schreiben, der in Bezug auf Transaktionen effektiv ist. Code dieser Art lässt sich grob in zwei Kategorien einteilen.

* Verwenden von Konstrukten mit minimaler Protokollierung, wann immer möglich
* Verarbeiten von Daten mit bereichsbezogenen Batches, um einzelne Transaktionen mit langer Ausführungsdauer zu vermeiden
* Nutzen eines Partitionswechselmusters für große Änderungen einer bestimmten Partition

## <a name="minimal-vs-full-logging"></a>Vergleich von minimaler und vollständiger Protokollierung

Im Gegensatz zu vollständig protokollierten Vorgängen, bei denen das Transaktionsprotokoll zum Nachverfolgen aller Zeilenänderungen verwendet wird, werden bei Vorgängen mit minimaler Protokollierung nur Umfangszuordnungen und Metadatenänderungen nachverfolgt. Die minimale Protokollierung umfasst daher nur die Informationen, die erforderlich sind, um für die Transaktion nach einem Ausfall oder bei einer expliziten Anforderung (ROLLBACK TRAN) ein Rollback auszuführen. Da im Transaktionsprotokoll deutlich weniger Informationen nachverfolgt werden, weist ein Vorgang mit minimaler Protokollierung eine höhere Leistung als ein Vorgang mit ähnlicher Größe und vollständiger Protokollierung auf. Da außerdem weniger Schreibvorgänge für das Transaktionsprotokoll anfallen, wird eine viel kleinere Menge von Protokolldaten generiert, und die E/A-Effizienz steigt.

Die Sicherheitslimits für Transaktionen gelten nur für Vorgänge mit vollständiger Protokollierung.

> [!NOTE]
> Vorgänge mit minimaler Protokollierung können Teil von expliziten Transaktionen sein. Da alle Änderungen in Zuordnungsstrukturen nachverfolgt werden, ist es möglich, für Vorgänge mit minimaler Protokollierung ein Rollback durchzuführen.

## <a name="minimally-logged-operations"></a>Vorgänge mit minimaler Protokollierung

Eine minimale Protokollierung ist für folgende Vorgänge möglich:

* CREATE TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT..SELECT
* CREATE INDEX
* ALTER INDEX REBUILD
* DROP INDEX
* TRUNCATE TABLE
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Interne Datenverschiebungsvorgänge (z.B. BROADCAST und SHUFFLE) sind vom Sicherheitslimit für Transaktionen nicht betroffen.

## <a name="minimal-logging-with-bulk-load"></a>Minimale Protokollierung mit Massenladen

CTAS und INSERT...SELECT sind jeweils Massenladevorgänge. Beide werden aber durch die Zieltabellendefinition beeinflusst und sind vom Ladeszenario abhängig. In der folgenden Tabelle wird erläutert, wann Massenvorgänge vollständig oder nur minimal protokolliert werden:  

| Primärer Index | Ladeszenario | Protokollierungsmodus |
| --- | --- | --- |
| Heap |Any |**Minimal** |
| Gruppierter Index |Leere Zieltabelle |**Minimal** |
| Gruppierter Index |Geladene Zeilen überlappen sich nicht mit vorhandenen Seiten im Ziel |**Minimal** |
| Gruppierter Index |Geladene Zeilen überlappen sich mit vorhandenen Seiten im Ziel |Vollständig |
| Gruppierter Columnstore-Index |Batchgröße >= 102.400 pro nach Partition ausgerichteter Verteilung |**Minimal** |
| Gruppierter Columnstore-Index |Batchgröße < 102.400 pro nach Partition ausgerichteter Verteilung |Vollständig |

Beachten Sie, dass es sich bei allen Schreibvorgängen zum Aktualisieren von sekundären oder nicht gruppierten Indizes immer um Vorgänge mit vollständiger Protokollierung handelt.

> [!IMPORTANT]
> Bei einem dedizierten SQL-Pool gibt es 60 Verteilungen. Wenn davon ausgegangen wird, dass alle Zeilen gleichmäßig verteilt und in einer einzelnen Partition angeordnet sind, muss Ihr Batch also mindestens 6.144.000 Zeilen enthalten, damit beim Schreiben in einen gruppierten Columnstore-Index die minimale Protokollierung verwendet wird. Falls die Tabelle partitioniert ist und die eingefügten Zeilen über Partitionsgrenzen hinweg reichen, benötigen Sie bei gleichmäßiger Datenverteilung 6.144.000 Zeilen pro Partitionsgrenze. Für jede Partition in jeder Verteilung muss unabhängig voneinander der Schwellenwert in Höhe von 102.400 Zeilen überschritten werden, damit für den Einfügevorgang in die Verteilung die minimale Protokollierung angewendet wird.

Das Laden von Daten in eine nicht leere Tabelle mit einem gruppierten Index kann häufig eine Mischung aus vollständig protokollierten und minimal protokollierten Zeilen umfassen. Bei einem gruppierten Index handelt es sich um eine ausbalancierte Struktur (B-Struktur) von Seiten. Falls die Seite, auf die geschrieben wird, bereits Zeilen aus einer anderen Transaktion enthält, werden diese Schreibvorgänge vollständig protokolliert. Aber wenn die Seite leer ist, wird für das Schreiben auf die Seite nur die minimale Protokollierung genutzt.

## <a name="optimizing-deletes"></a>Optimieren der Löschvorgänge

DELETE ist ein Vorgang mit vollständiger Protokollierung.  Wenn Sie eine große Datenmenge in einer Tabelle oder Partition löschen müssen, ist es häufiger sinnvoller, stattdessen mit `SELECT` die Daten auszuwählen, die Sie behalten möchten. Dieser Vorgang kann mit minimaler Protokollierung ausgeführt werden.  Um die Daten auszuwählen, erstellen Sie mit [CTAS](sql-data-warehouse-develop-ctas.md) eine neue Tabelle.  Verwenden Sie nach der Erstellung [RENAME](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), um die alte Tabelle gegen die neu erstellte Tabelle auszutauschen.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Optimieren von Aktualisierungen

UPDATE ist ein Vorgang mit vollständiger Protokollierung.  Wenn Sie eine große Zahl von Zeilen in einer Tabelle oder einer Partition aktualisieren müssen, ist es häufig viel effizienter, einen Vorgang mit minimaler Protokollierung zu verwenden, z.B. [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Im Beispiel unten wurde eine vollständige Tabellenaktualisierung in einen CTAS-Vorgang konvertiert, damit die minimale Protokollierung möglich ist.

In diesem Fall fügen wir dem Umsatz in der Tabelle nachträglich einen Rabattbetrag hinzu:

```sql
--Step 01. Create a new table containing the "Update".
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Bei der Neuerstellung großer Tabellen können die Features zur Workloadverwaltung des dedizierten SQL-Pools vorteilhaft sein. Weitere Informationen finden Sie unter [Ressourcenklassen für die Workloadverwaltung](resource-classes-for-workload-management.md).

## <a name="optimizing-with-partition-switching"></a>Optimieren mit Partitionswechsel

Bei umfangreichen Änderungen innerhalb einer [Tabellenpartition](sql-data-warehouse-tables-partition.md) ist ein Muster für Partitionswechsel sinnvoll. Wenn der Datenänderungsaufwand groß ist und mehrere Partitionen umfasst, lässt sich mit dem Durchlaufen der Partitionen das gleiche Ergebnis erzielen.

Schritte zum Durchführen eines Partitionswechsels:

1. Erstellen einer leeren Bereinigungspartition
2. Durchführen der Aktualisierung als CTAS-Vorgang
3. Verschieben der vorhandenen Daten in die Tabelle „out“
4. Einfügen der neuen Daten
5. Bereinigen der Daten

Um jedoch die zu wechselnden Partitionen zu identifizieren, erstellen Sie das folgende Hilfsprogramm.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id]
                                                AND i.[index_id]        = p.[index_id]
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id]
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Mit diesem Verfahren wird die Wiederverwendung von Code erhöht, und das Partitionswechselbeispiel ist übersichtlicher.

Im folgenden Code sind die oben erwähnten Schritte enthalten, mit denen eine vollständige Partitionswechselroutine erzielt werden kann.

```sql
--Create a partitioned aligned empty table to switch out the data
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT
    [ProductKey]  
,    [OrderDateKey]
,    [DueDateKey]  
,    [ShipDateKey]
,    [CustomerKey]
,    [PromotionKey]
,    [CurrencyKey]
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber]
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Minimieren der Protokollierung mit kleinen Batches

Bei großen Datenänderungsvorgängen kann es sinnvoll sein, den Vorgang in Teile oder Batches aufzuteilen, um kleinere Einheiten zu erhalten.

Der folgende Code ist ein funktionierendes Beispiel. Für die Batchgröße wurde ein Beispielwert gewählt, um die Vorgehensweise zu verdeutlichen. In Wirklichkeit wäre der Wert für die Batchgröße deutlich höher.

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Anleitung zum Anhalten und Skalieren

Sie können einen dedizierten SQL-Pool bei Bedarf [anhalten, fortsetzen und skalieren](sql-data-warehouse-manage-compute-overview.md). Wenn Sie Ihren dedizierten SQL-Pool anhalten oder skalieren, ist es wichtig zu verstehen, dass alle laufenden Transaktionen sofort beendet werden. Dies führt dazu, dass für alle geöffneten Transaktionen ein Rollback durchgeführt wird. Wenn für Ihre Workload vor dem Anhalte- oder Skaliervorgang eine längere und unvollständige Datenänderung ausgegeben wurde, müssen diese Schritte rückgängig gemacht werden. Dieser Vorgang wirkt sich möglicherweise auf den Zeitraum aus, der zum Anhalten oder Skalieren Ihres dedizierten SQL-Pools benötigt wird.

> [!IMPORTANT]
> Sowohl `UPDATE` als auch `DELETE` sind Vorgänge mit vollständiger Protokollierung. Diese Vorgänge zum Rückgängigmachen und Wiederholen können also deutlich länger dauern als vergleichbare Vorgänge mit minimaler Protokollierung.

Die beste Vorgehensweise ist, auf den Abschluss aktiver Datenänderungstransaktionen zu warten, bevor ein dedizierter SQL-Pool angehalten oder skaliert wird. Allerdings ist dieses Szenario unter Umständen nicht immer praktikabel. Sie können folgende Optionen verwenden, um das Risiko eines langen Rollbackvorgangs zu verringern:

* Umschreiben von Vorgängen mit langer Ausführungsdauer mithilfe von [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
* Unterteilen des Vorgangs in Blöcke, ausgeführt für jeweils eine Teilmenge der Zeilen

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Isolationsstufen und Transaktionsgrenzen finden Sie unter [Transaktionen im dedizierten SQL-Pool](sql-data-warehouse-develop-transactions.md).  Eine Übersicht über andere bewährte Methoden finden Sie unter [Bewährte Methoden für den dedizierten SQL-Pool](sql-data-warehouse-best-practices.md).
