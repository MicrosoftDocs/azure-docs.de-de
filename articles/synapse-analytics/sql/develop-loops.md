---
title: Verwenden von T-SQL-Schleifen
description: Tipps zur Verwendung von T-SQL-Schleifen, zum Ersetzen von Cursorn und zur Entwicklung verwandter Lösungen mit Synapse SQL in Azure Synapse Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 99ee41de7ffd66191ff712a5ffbda65f3233196f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324451"
---
# <a name="use-t-sql-loops-with-synapse-sql-in-azure-synapse-analytics"></a>Verwenden von T-SQL-Schleifen mit Synapse SQL in Azure Synapse Analytics

In diesem Artikel erhalten Sie wichtige Tipps zur Verwendung von T-SQL-Schleifen, zum Ersetzen von Cursorn und zur Entwicklung verwandter Lösungen mit Synapse SQL.

## <a name="purpose-of-while-loops"></a>Zweck der WHILE-Schleifen

Synapse SQL unterstützt die [WHILE](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15&preserve-view=true)-Schleife für die wiederholte Ausführung von Anweisungsblöcken. Die WHILE-Schleife wird so lange ausgeführt, wie die angegebenen Bedingungen wahr sind oder bis die Schleife im Code mit dem Schlüsselwort BREAK gezielt beendet wird. 

Schleifen in Synapse SQL sind nützlich, um im SQL-Code definierte Cursor zu ersetzen. Glücklicherweise sind fast alle Cursor, die per SQL-Code geschrieben werden, schreibgeschützte Cursor für den schnellen Vorlauf. Daher sind WHILE-Schleifen eine gute Alternative zum Ersetzen von Cursorn.

## <a name="replace-cursors-in-synapse-sql"></a>Ersetzen von Cursorn in Synapse SQL

Vor dem Einstieg sollte die folgende Frage bedacht werden: „Könnte dieser Cursor so umgeschrieben werden, dass er setbasierte Vorgänge verwendet?“ In vielen Fällen können Sie die Frage bejahen, daher ist dies häufig der beste Ansatz. Ein satzbasierter Vorgang wird oft schneller als ein iterativer Durchlauf Zeile für Zeile ausgeführt.

Nur-Lese-Cursor für den schnellen Vorlauf können leicht durch ein Schleifenkonstrukt ersetzt werden. Im Folgenden sehen Sie ein einfaches Beispiel. In diesem Codebeispiel wird die Statistik für jede Tabelle der Datenbank aktualisiert. Indem die Tabellen mit der Schleife durchlaufen werden, wird jeder Befehl der Reihe nach ausgeführt.

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

Initialisieren Sie als Nächstes die Variablen, die zum Ausführen des Schleifenvorgangs erforderlich sind:

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

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](develop-overview.md).
