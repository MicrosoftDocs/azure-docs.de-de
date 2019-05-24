---
title: Verwenden von T-SQL-Schleifen in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Tipps zum Verwenden von T-SQL-Schleifen und Ersetzen von Cursorn in Azure SQL Data Warehouse für die Entwicklung von Lösungen
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c321bc4e493799a50ada4dd91faf2d2ebdee8aba
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850454"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>Verwenden von T-SQL-Schleifen in SQL Data Warehouse
Tipps zum Verwenden von T-SQL-Schleifen und Ersetzen von Cursorn in Azure SQL Data Warehouse für die Entwicklung von Lösungen

## <a name="purpose-of-while-loops"></a>Zweck der WHILE-Schleifen

SQL Data Warehouse unterstützt die [WHILE](/sql/t-sql/language-elements/while-transact-sql)-Schleife für die wiederholte Ausführung von Anweisungsblöcken. Die WHILE-Schleife wird so lange ausgeführt, wie die angegebenen Bedingungen wahr sind oder bis die Schleife im Code mit dem Schlüsselwort BREAK gezielt beendet wird. Schleifen sind nützlich, um im SQL-Code definierte Cursor zu ersetzen. Glücklicherweise sind fast alle Cursor, die per SQL-Code geschrieben werden, schreibgeschützte Cursor für den schnellen Vorlauf. Aus diesem Grund sind [WHILE]-Schleifen eine gute Alternative zum Ersetzen von Cursorn.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Ersetzen von Cursorn in SQL Data Warehouse
Bevor Sie lange überlegen, sollte Sie sich zuerst folgende Frage stellen: "Könnte dieser Cursor so umgeschrieben werden, dass er setbasierte Vorgänge verwendet?" In vielen Fällen können Sie die Frage bejahen, daher ist dies häufig der beste Ansatz. Ein satzbasierter Vorgang wird oft schneller als ein iterativer Durchlauf Zeile für Zeile durchgeführt.

Nur-Lese-Cursor für den schnellen Vorlauf können leicht durch ein Schleifenkonstrukt ersetzt werden. Im Folgenden sehen Sie ein einfaches Beispiel. In diesem Codebeispiel wird die Statistik für jede Tabelle der Datenbank aktualisiert. Indem die Tabellen mit der Schleife durchlaufen werden, wird jeder Befehl der Reihe nach ausgeführt.

Erstellen Sie zuerst eine temporäre Tabelle mit einer eindeutigen Zeilenzahl zum Identifizieren der einzelnen Anweisungen:

```
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

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Jetzt können Sie die Anweisungen per Schleife durchlaufen und einzeln ausführen:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Abschließend können Sie die temporäre Tabelle verwerfen, die Sie im ersten Schritt erstellt haben.

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

