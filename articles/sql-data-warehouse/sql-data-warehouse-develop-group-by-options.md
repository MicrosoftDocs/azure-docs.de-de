---
title: Verwendung von Group By-Optionen in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Tipps zum Implementieren von Group By-Optionen in Azure SQL Data Warehouse für die Entwicklung von Lösungen
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b158048929d3db8672d76027666331448a91a0a8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861807"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Group By-Optionen in SQL Data Warehouse
Tipps zum Implementieren von Group By-Optionen in Azure SQL Data Warehouse für die Entwicklung von Lösungen

## <a name="what-does-group-by-do"></a>Wozu dient GROUP BY?

Die T-SQL-Klausel [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) fasst Daten zu einer Gruppe von Zeilen zusammen. GROUP BY weist einige Optionen auf, die SQL Data Warehouse nicht unterstützt. Für diese Optionen stehen Problemumgehungen zur Verfügung.

Die Optionen sind:

* GROUP BY mit ROLLUP
* GROUPING SETS
* GROUP BY mit CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup- und Grouping Set-Optionen
Die einfachste Möglichkeit ist hierbei die alternative Verwendung von UNION ALL, um das Rollup durchzuführen, anstatt sich auf die explizite Syntax zu verlassen. Das Ergebnis ist identisch.

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
* Gesamtergebnis

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
Es ist möglich, mit dem UNION ALL-Ansatz ein GROUP BY WITH CUBE-Element zu erstellen. Das Problem ist, dass der Code schnell unübersichtlich und schwerfällig werden kann. Um dies zu vermeiden, können Sie diesen erweiterten Ansatz verwenden.

Wir verwenden hierfür das obige Beispiel.

Der erste Schritt ist das Definieren des „Cubes“, mit dem alle Aggregationsebenen festgelegt werden, die erstellt werden sollen. Es ist wichtig, das CROSS JOIN-Element der beiden abgeleiteten Tabellen zu beachten. Hiermit werden alle Ebenen für uns erzeugt. Der Rest des Codes dient eigentlich nur der Formatierung.

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

Im Folgenden werden die Ergebnisse der CTAS dargestellt:

![GROUP BY mit CUBE](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

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

Der dritte Schritt ist das Durchführen einer Schleife für den Cube mit den Spalten, um die Aggregation durchzuführen. Die Abfrage wird einmal für jede Zeile in der temporären #Cube-Tabelle ausgeführt, und die Ergebnisse werden in der temporären #Results-Tabelle gespeichert.

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

Abschließend können Sie die Ergebnisse zurückgeben, indem Sie sie einfach aus der temporären #Results-Tabelle auslesen.

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Indem der Code in Abschnitte unterteilt und ein Schleifenkonstrukt generiert wird, wird die Verwaltung und Pflege der Codeerstellung verbessert.

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

