---
title: Verwenden von Group By-Optionen
description: Tipps zum Implementieren von GROUP BY-Optionen für dedizierte SQL-Pools in Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: cdac3b1e502a203c272db75eb4345446b0f7c0c8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462764"
---
# <a name="group-by-options-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>GROUP BY-Optionen für dedizierte SQL-Pools in Azure Synapse Analytics

In diesem Artikel finden Sie Tipps für das Implementieren von GROUP BY-Optionen in dedizierten SQL-Pools.

## <a name="what-does-group-by-do"></a>Wozu dient GROUP BY?

Die T-SQL-Klausel [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) fasst Daten zu einer Gruppe von Zeilen zusammen. GROUP BY bietet einige Optionen, die der dedizierte SQL-Pool nicht unterstützt. Für diese Optionen gibt es jedoch Problemumgehungen, die wie folgt lauten:

* GROUP BY mit ROLLUP
* GROUPING SETS
* GROUP BY mit CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup- und Grouping Set-Optionen

Die einfachste Lösung ist, anstelle der expliziten Syntax UNION ALL für die Durchführung des Rollups zu verwenden. Das Ergebnis ist in beiden Fällen identisch.

Im folgenden Beispiel wird die GROUP BY-Anweisung mit der ROLLUP-Option verwendet:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Mithilfe von ROLLUP werden im obigen Beispiel die folgenden Aggregationen angefordert:

* Land und Region
* Country
* Grand Total

Um ROLLUP zu ersetzen und die gleichen Ergebnisse zurückzugeben, können Sie UNION ALL verwenden und explizit die erforderlichen Aggregationen angeben:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Für den Ersatz von GROUPING SETS gelten die Beispielprinzipien. Sie müssen nur die UNION ALL-Abschnitte für die Aggregationsebenen erstellen, die angezeigt werden sollen.

## <a name="cube-options"></a>Cube-Optionen

Es ist möglich, mit dem UNION ALL-Ansatz eine GROUP BY WITH CUBE-Klausel zu erstellen. Das Problem ist, dass der Code schnell unübersichtlich und schwerfällig werden kann. Dies lässt sich vermeiden, indem Sie diesen erweiterten Ansatz verwenden.

Dem vorherigen Beispiel entsprechend besteht der erste Schritt im Definieren des „Cubes“, der alle Aggregationsebenen definiert, die erstellt werden sollen.

Beachten Sie die CROSS JOIN-Klausel der beiden abgeleiteten Tabellen, da diese alle Ebenen automatisch generiert. Der übrige Code dient nur der Formatierung:

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

In der folgenden Abbildung werden die Ergebnisse von CTAS dargestellt:

![GROUP BY mit CUBE](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

Der zweite Schritt ist das Angeben einer Zieltabelle zum Speichern von Zwischenergebnissen:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

Der dritte Schritt ist das Durchführen einer Schleife für den Cube mit den Spalten, um die Aggregation durchzuführen. Die Abfrage wird für jede Zeile in der temporären Tabelle „#Cube“ einmal ausgeführt. Die Ergebnisse werden in der temporären Tabelle „#Results“ gespeichert.

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Abschließend können Sie die Ergebnisse zurückgeben, indem Sie sie aus der temporären Tabelle „#Results“ auslesen.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Indem der Code in Abschnitte unterteilt und ein Schleifenkonstrukt generiert wird, lässt sich der Code leichter verwalten und instand halten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).
