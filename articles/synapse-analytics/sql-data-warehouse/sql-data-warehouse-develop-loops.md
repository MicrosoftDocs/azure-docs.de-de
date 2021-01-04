---
title: Verwenden von T-SQL-Schleifen
description: Tipps für die Lösungsentwicklung mithilfe von T-SQL-Schleifen und das Ersetzen von Cursors für dedizierte SQL-Pools in Azure Synapse Analytics.
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
ms.openlocfilehash: 3477b3095414248afa9fbc7417ab707c94f35546
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96462736"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Verwenden von T-SQL-Schleifen für dedizierte SQL-Pools in Azure Synapse Analytics

Dieser Artikel enthält Tipps zur Entwicklung von Lösungen für dedizierte SQL-Pools mithilfe von T-SQL-Schleifen und durch Ersetzen von Cursors.

## <a name="purpose-of-while-loops"></a>Zweck der WHILE-Schleifen

Dedizierte SQL-Pools in Azure Synapse unterstützen die [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)-Schleife für die wiederholte Ausführung von Anweisungsblöcken. Die WHILE-Schleife wird so lange ausgeführt, wie die angegebenen Bedingungen wahr sind oder bis die Schleife im Code mit dem Schlüsselwort BREAK gezielt beendet wird.

Schleifen sind nützlich, um im SQL-Code definierte Cursor zu ersetzen. Glücklicherweise sind fast alle Cursor, die per SQL-Code geschrieben werden, schreibgeschützte Cursor für den schnellen Vorlauf. Daher sind WHILE-Schleifen eine gute Alternative zum Ersetzen von Cursorn.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>Ersetzen von Cursors im dedizierten SQL-Pool

Bevor Sie lange überlegen, sollte Sie sich zuerst folgende Frage stellen: „Könnte dieser Cursor so umgeschrieben werden, dass er setbasierte Vorgänge verwendet?“

In vielen Fällen können Sie die Frage bejahen, daher ist dies häufig der beste Ansatz. Ein satzbasierter Vorgang wird oft schneller als ein iterativer Durchlauf Zeile für Zeile durchgeführt.

Nur-Lese-Cursor für den schnellen Vorlauf können leicht durch ein Schleifenkonstrukt ersetzt werden. Das folgende Beispiel ist einf. In diesem Codebeispiel wird die Statistik für jede Tabelle der Datenbank aktualisiert. Indem die Tabellen mit der Schleife durchlaufen werden, wird jeder Befehl der Reihe nach ausgeführt.

Erstellen Sie zuerst eine temporäre Tabelle mit einer eindeutigen Zeilenzahl zum Identifizieren der einzelnen Anweisungen:

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Initialisieren Sie als Nächstes die Variablen, die zum Durchführen des Schleifenvorgangs erforderlich sind:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Jetzt können Sie die Anweisungen per Schleife durchlaufen und einzeln ausführen:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Abschließend können Sie die temporäre Tabelle verwerfen, die Sie im ersten Schritt erstellt haben.

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).
