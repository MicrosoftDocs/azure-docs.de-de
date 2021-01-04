---
title: Verwenden von GROUP BY-Optionen in Synapse SQL
description: Synapse SQL ermöglicht die Entwicklung von Lösungen durch Implementierung verschiedener GROUP BY-Optionen.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: d160040620fcaa60a8adc955efcc54ac3ca2609e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321440"
---
# <a name="group-by-options-in-synapse-sql"></a>GROUP BY-Optionen in Synapse SQL

Synapse SQL ermöglicht die Entwicklung von Lösungen durch Implementierung verschiedener GROUP BY-Optionen. 

## <a name="what-group-by-does"></a>Funktionsweise von GROUP BY

Die T-SQL-Klausel [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) fasst Daten zu einer Gruppe von Zeilen zusammen.

Der serverlose SQL-Pool unterstützt die gesamte Palette von GROUP BY-Optionen. Der dedizierte SQL-Pool unterstützt eine begrenzte Anzahl von GROUP BY-Optionen.

## <a name="group-by-options-supported-in-dedicated-sql-pool"></a>Im dedizierten SQL-Pool unterstützte GROUP BY-Optionen

GROUP BY bietet einige Optionen, die der dedizierte SQL-Pool nicht unterstützt. Für diese Optionen gibt es jedoch Problemumgehungen, die wie folgt lauten:

* GROUP BY mit ROLLUP
* GROUPING SETS
* GROUP BY mit CUBE

### <a name="rollup-and-grouping-sets-options"></a>Rollup- und Grouping Set-Optionen

Die einfachste Lösung ist, anstelle der expliziten Syntax UNION ALL für die Ausführung des Rollups zu verwenden. Das Ergebnis ist identisch.

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

### <a name="cube-options"></a>Cube-Optionen

Es ist möglich, mit dem UNION ALL-Ansatz eine GROUP BY WITH CUBE-Klausel zu erstellen. Das Problem ist, dass der Code schnell unübersichtlich und schwerfällig werden kann. Dies lässt sich vermeiden, indem Sie diesen erweiterten Ansatz verwenden.

Der erste Schritt ist das Definieren des „Cubes“, mit dem alle Aggregationsebenen festgelegt werden, die erstellt werden sollen. Beachten Sie den CROSS JOIN der beiden abgeleiteten Tabellen, da dadurch alle Ebenen generiert werden. Der übrige Code dient nur der Formatierung.

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

Die folgende Abbildung zeigt die Ergebnisse von [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true):

![GROUP BY mit CUBE](./media/develop-group-by-options/develop-group-by-cube.png)

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

Der dritte Schritt ist das Durchführen einer Schleife für den Cube mit den Spalten, um die Aggregation durchzuführen. Die Abfrage wird für jede Zeile in der temporären Tabelle „#Cube“ einmal ausgeführt. Die Ergebnisse werden in der temporären Tabelle „#Results“ gespeichert:

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

Abschließend können Sie die Ergebnisse zurückgeben, indem Sie sie aus der temporären Tabelle „#Results“ auslesen:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Indem der Code in Abschnitte unterteilt und ein Schleifenkonstrukt generiert wird, lässt sich der Code leichter verwalten und instand halten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](develop-overview.md).
