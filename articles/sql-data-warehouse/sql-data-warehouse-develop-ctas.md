---
title: CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Erläuterungen und Beispiele zur Anweisung CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse zum Entwickeln von Lösungen.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019
ms.openlocfilehash: 91de474cc0610099b4264cc6d0dfbd26e8df0618
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851444"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse

In diesem Artikel wird die T-SQL-Anweisung CREATE TABLE AS SELECT (CTAS) in Azure SQL Data Warehouse zum Entwickeln von Lösungen beschrieben. Der Artikel enthält auch Codebeispiele.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

Die Anweisung [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) ist eines der wichtigsten verfügbaren T-SQL-Features. CTAS ist ein paralleler Vorgang, bei dem eine neue Tabelle anhand der Ausgabe einer SELECT-Anweisung erstellt wird. CTAS ist die einfachste und schnellste Methode zum Erstellen und Einfügen von Daten in eine Tabelle mit einem einzigen Befehl.

## <a name="selectinto-vs-ctas"></a>SELECT...INTO im Vergleich zu CTAS

CTAS ist eine stärker anpassbare Version der [SELECT...INTO](/sql/t-sql/queries/select-into-clause-transact-sql)-Anweisung.

Nachstehend sehen Sie ein Beispiel für eine einfache SELECT...INTO-Anweisung:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

SELECT...INTO lässt jedoch nicht zu, dass Sie im Rahmen des Vorgangs die Verteilungsmethode oder den Indextyp ändern. Sie erstellen `[dbo].[FactInternetSales_new]` mithilfe des Standardverteilungstyps als ROUND_ROBIN und die Standardtabellenstruktur als GRUPPIERTER COLUMNSTORE-INDEX.

Mit CTAS können Sie andererseits sowohl die Verteilung der Tabellendaten als auch den Tabellenstrukturtyp angeben. So konvertieren Sie das vorherige Beispiel in CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Wenn Sie in Ihrem CTAS-Vorgang nur den Index ändern möchten und es sich bei der Quelltabelle um eine verteilte Hashtabelle handelt, behalten Sie dieselbe Verteilungsspalte und denselben Datentyp bei. So wird effizienterweise eine domänenübergreifende Datenverschiebung während des Vorgangs vermieden.

## <a name="use-ctas-to-copy-a-table"></a>Kopieren einer Tabelle mithilfe von CTAS

Einer der häufigsten Verwendungszwecke für CTAS ist vermutlich das Kopieren einer Tabelle, um den DDL-Code ändern zu können. Angenommen, Sie haben die Tabelle ursprünglich als `ROUND_ROBIN` erstellt und möchten sie nun in eine Tabelle ändern, die über eine Spalte verteilt ist. In diesem Fall könnten Sie mit CTAS die Verteilungsspalte ändern. Außerdem könnten Sie CTAS zum Ändern der Partitionierung, der Indizierung oder von Spaltentypen verwenden.

Nehmen wir an, dass Sie diese Tabelle mit dem Standardverteilungstyp `ROUND_ROBIN` erstellt und in `CREATE TABLE` keine Verteilungsspalte angegeben haben.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Nun möchten Sie eine neue Kopie dieser Tabelle mit `Clustered Columnstore Index` erstellen, sodass Sie die Leistung von gruppierten Columnstore-Tabellen nutzen können. Außerdem soll diese Tabelle nach `ProductKey` verteilt werden, da Sie Verknüpfungen für diese Spalte erwarten und Datenverschiebungen während der Verknüpfung nach `ProductKey` vermeiden möchten. Schließlich möchten Sie auch eine Partitionierung für `OrderDateKey` hinzufügen, sodass Sie schnell alte Daten löschen können, indem Sie alte Partitionen verwerfen. Im Folgenden finden Sie die CTAS-Anweisung, mit der Sie die alte Tabelle in eine neue Tabelle kopieren.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Schließlich können Sie die Tabellen umbenennen, um zur neuen Tabelle zu wechseln und die alte Tabelle zu verwerfen.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>Umgehen von nicht unterstützten Funktionen mit CTAS

Mit CTAS können auch einige nicht unterstützte Funktionen umgangen werden, die nachfolgend aufgeführt sind. Diese Methode ist häufig sehr hilfreich, da Ihr Code nicht nur konform ist, sondern unter SQL Data Warehouse oft auch schneller ausgeführt wird. Diese Leistung ist auf das vollständig parallelisierte Design zurückzuführen. Mögliche Szenarien:

* ANSI JOINS bei UPDATEs
* ANSI JOINs bei DELETEs
* MERGE-Anweisung

> [!TIP]
> Denken Sie zuerst an CTAS. Ein Problem mit CTAS zu lösen, ist in der Regel ein guter Ansatz, auch wenn Sie dabei mehr Daten schreiben müssen.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI-Verknüpfungsersatz für update-Anweisungen

Angenommen, Sie stehen vor einem komplexen Update. Bei dem Update werden mehr als zwei Tabellen verknüpft, indem ANSI-Verknüpfungssyntax zum Durchführen des UPDATE- oder DELETE-Vorgangs genutzt wird.

Stellen Sie sich vor, Sie müssen diese Tabelle aktualisieren:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Die ursprüngliche Abfrage könnte etwa wie in diesem Beispiel ausgesehen haben:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

SQL Data Warehouse unterstützt keine ANSI-Joins in der `FROM`-Klausel einer `UPDATE`-Anweisung, also können Sie das obige Beispiel nicht ohne Änderung verwenden.

Sie können eine Kombination aus CTAS und einer impliziten Verknüpfung verwenden, um das obige Beispiel zu ersetzen:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI-Verknüpfungsersatz für delete-Anweisungen

Der beste Ansatz zum Löschen von Daten ist in einigen Fällen die Verwendung von CTAS, insbesondere für `DELETE`-Anweisungen, die ANSI-Verknüpfungssyntax verwenden. Der Grund ist, dass SQL Data Warehouse in der `FROM`-Klausel einer `DELETE`-Anweisung keine ANSI-Joins unterstützt. Anstatt die Daten zu löschen, wählen Sie die Daten aus, die Sie behalten möchten.

Es folgt ein Beispiel für eine konvertierte `DELETE`-Anweisung:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Ersetzen von MERGE-Anweisungen

Sie können MERGE-Anweisungen mit CTAS zumindest teilweise ersetzen. Sie können `INSERT` und `UPDATE` in einer einzelnen Anweisung kombinieren. Alle gelöschten Datensätze sollten aus der `SELECT`-Anweisung ausgeschlossen werden, damit sie in den Ergebnissen nicht enthalten sind.

Dies ist ein Beispiel für `UPSERT`:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explizites Angeben des Datentyps und der NULL-Zulässigkeit der Ausgabe

Beim Migrieren von Code stoßen Sie unter Umständen auf diese Art von Codiermuster:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Sie denken vielleicht, dass Sie diesen Code zu CTAS migrieren sollten, und damit hätten Sie auch recht. Hierbei liegt jedoch ein verstecktes Problem vor.

Der folgende Code führt nicht zum gleichen Ergebnis:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Beachten Sie, dass für die Spalte „result“ der Datentyp und die NULL-Zulässigkeitswerte des Ausdrucks übernommen werden. Das Übertragen des Datentyps kann zu geringfügigen Abweichungen bei den Werten führen, wenn nicht darauf geachtet wird.

Testen Sie dieses Beispiel:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Der für „result“ gespeicherte Wert ist anders. Da der in der Spalte „result“ beibehaltene Wert in anderen Ausdrücken verwendet wird, wird der Fehler noch signifikanter.

![Screenshot der CTAS-Ergebnisse](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Dies ist für Datenmigrationen wichtig. Auch wenn die zweite Abfrage natürlich genauer ist, liegt ein Problem vor. Die Daten würden sich gegenüber dem Quellsystem unterscheiden, und dies stellt die Integrität der Migration infrage. Dies ist einer der seltenen Fälle, in denen die „falsche“ Antwort eigentlich die richtige Antwort ist!

Der Grund für diese Ungleichheit zwischen den beiden Ergebnissen ist die implizite Typumwandlung. Im ersten Beispiel definiert die Tabelle die Spaltendefinition. Beim Einfügen der Zeile tritt eine implizite Typumwandlung auf. Im zweiten Beispiel ist keine implizite Typumwandlung vorhanden, da der Ausdruck den Datentyp der Spalte definiert.

Beachten Sie auch, dass die Spalte im zweiten Beispiel so konfiguriert wurde, dass NULL-Werte zulässig sind. Im ersten Beispiel ist dies nicht der Fall. Als die Tabelle im ersten Beispiel erstellt wurde, wurde die NULL-Zulässigkeit der Spalte explizit definiert. Im zweiten Beispiel wurde dies dem Ausdruck überlassen, was standardmäßig zu einer NULL-Definition führen würde.

Zum Lösen dieser Probleme müssen Sie die Typumwandlung und NULL-Zulässigkeit im SELECT-Teil der CTAS-Anweisung explizit festlegen. Sie können diese Eigenschaften nicht in CREATE TABLE festlegen.
Das Korrigieren des Codes wird im folgenden Beispiel veranschaulicht:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Beachten Sie Folgendes:

* Sie können CAST oder CONVERT verwenden.
* Verwenden Sie zum Erzwingen der NULL-Zulässigkeit ISNULL, nicht COALESCE. Beachten Sie den folgenden Hinweis.
* ISNULL ist die äußerste Funktion.
* Der zweite Teil von ISNULL ist die Konstante 0.

> [!NOTE]
> Damit die NULL-Zulässigkeit richtig festgelegt wird, muss unbedingt ISNULL verwendet werden, und nicht COALESCE. COALESCE ist keine deterministische Funktion, sodass für das Ergebnis des Ausdrucks immer NULL-Werte zulässig wären. Dies ist bei ISNULL anders. Die Funktion ist deterministisch. Wenn der zweite Teil der ISNULL-Funktion eine Konstante oder ein Literal ist, ist der sich ergebende Wert daher NICHT NULL.

Das Sicherstellen der Integrität Ihrer Berechnungen ist auch für den Tabellenpartitionswechsel wichtig. Stellen Sie sich vor, Sie haben die folgende Tabelle als Faktentabelle definiert:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Das amount-Feld ist aber ein berechneter Ausdruck. Es ist nicht Teil der Quelldaten.

Zum Erstellen des partitionierten Datasets können Sie beispielsweise folgenden Code verwenden:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Die Abfrage würde problemlos ausgeführt werden. Das Problem entsteht, wenn Sie versuchen, den Partitionswechsel durchzuführen. Die Tabellendefinitionen stimmen nicht überein. Damit die Tabellendefinitionen übereinstimmen, ändern Sie die CTAS-Anweisung, und fügen Sie eine `ISNULL`-Funktion hinzu, damit das NULL-Zulässigkeitsattribut der Spalte beibehalten wird.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Sie sehen also, dass die Typkonsistenz und die Pflege der Eigenschaften für die NULL-Zulässigkeit für CTAS eine bewährte Methode für das Engineering sind. Es ist hilfreich, die Integrität in Ihren Berechnungen zu wahren. Außerdem wird so sichergestellt, dass der Partitionswechsel möglich ist.

CTAS ist eine der wichtigsten Anweisungen in SQL Data Warehouse. Machen Sie sich damit eingehend vertraut. Weitere Informationen finden Sie in der [CTAS-Dokumentation](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

