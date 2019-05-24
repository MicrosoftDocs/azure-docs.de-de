---
title: Migrieren von SQL-Code nach SQL Data Warehouse | Microsoft Docs
description: Tipps für die Migration des SQL-Codes in Azure SQL Data Warehouse zum Entwickeln von Lösungen.
services: sql-data-warehouse
author: TwoUnder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: mausher
ms.reviewer: igorstan
ms.openlocfilehash: 61821af2c1c4732488d1a691939a21df284fe3c8
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873507"
---
# <a name="migrate-your-sql-code-to-sql-data-warehouse"></a>Migrieren von SQL-Code nach SQL Data Warehouse

Dieser Artikel beschreibt Codeänderungen, die Sie sicherlich benötigen, wenn Sie Ihren Code von einer anderen Datenbank zu SQL Data Warehouse migrieren. Mit einigen SQL Data Warehouse-Funktionen kann die Leistung erheblich verbessert werden, da sie speziell für eine verteilte Ausführung entwickelt wurden. Um jedoch eine gleichbleibende Leistung und Skalierung zu gewährleisten, sind einige Funktionen auch nicht verfügbar.

## <a name="common-t-sql-limitations"></a>Allgemeine T-SQL-Einschränkungen

Die folgende Liste enthält die wichtigsten Funktionen, die von SQL Data Warehouse nicht unterstützt werden. Über die Links gelangen Sie zu Problemumgehungen für die nicht unterstützten Funktionen:

* [ANSI-Joins bei Aktualisierungen][ANSI joins on updates]
* [ANSI-Joins bei Löschvorgängen][ANSI joins on deletes]
* [MERGE-Anweisung][merge statement]
* Datenbankübergreifende Verknüpfungen
* [Cursor][cursors]
* [INSERT..EXEC][INSERT..EXEC]
* OUTPUT-Klausel
* Benutzerdefinierte Inlinefunktionen
* Funktionen mit mehreren Anweisungen
* Allgemeine Tabellenausdrücke
* [Rekursive allgemeine Tabellenausdrücke (CTE)] (#Recursive-common-table-expressions-(CTE)
* CLR-Funktionen und -Prozeduren
* $partition-Funktion
* Tabellenvariablen
* Parameter für Tabellenwerte
* Verteilte Transaktionen
* Commit- oder Rollback-Vorgänge
* SAVE TRANSACTION
* Ausführungskontexte (EXECUTE AS)
* [GROUP BY-Klausel mit ROLLUP-, CUBE- oder GROUPING SETS-Option][group by clause with rollup / cube / grouping sets options]
* [Über 8 Schachtelungsebenen][nesting levels beyond 8]
* [Aktualisieren über Sichten][updating through views]
* [Kein MAX-Datentyp für dynamische SQL-Zeichenfolgen][no MAX data type for dynamic SQL strings]

Glücklicherweise können die meisten dieser Einschränkungen umgangen werden. Die entsprechenden Erläuterungen finden Sie in den jeweiligen oben referenzierten Artikeln.

## <a name="supported-cte-features"></a>Unterstützte CTE-Funktionen

Allgemeine Tabellenausdrücke (CTEs) werden in SQL Data Warehouse teilweise unterstützt.  Die folgenden CTE-Funktionen werden derzeit unterstützt:

* Ein allgemeiner Tabellenausdruck kann in einer SELECT-Anweisung angegeben werden.
* Ein allgemeiner Tabellenausdruck kann in einer CREATE VIEW-Anweisung angegeben werden.
* Ein allgemeiner Tabellenausdruck kann in einer CREATE TABLE AS SELECT (CTAS)-Anweisung angegeben werden.
* Ein allgemeiner Tabellenausdruck kann in einer CREATE REMOTE TABLE AS SELECT (CRTAS)-Anweisung angegeben werden.
* Ein allgemeiner Tabellenausdruck kann in einer CREATE EXTERNAL TABLE AS SELECT (CETAS)-Anweisung angegeben werden.
* Eine Remotetabelle kann über einen allgemeinen Tabellenausdruck referenziert werden.
* Eine externe Tabelle kann über einen allgemeinen Tabellenausdruck referenziert werden.
* In einem allgemeinen Tabellenausdruck können mehrere Abfragedefinitionen definiert werden.

## <a name="cte-limitations"></a>Einschränkungen bei allgemeinen Tabellenausdrücken (CTE)

Allgemeine Tabellenausdrücke weisen einige der folgenden Einschränkungen in SQL Data Warehouse auf:

* Auf einen allgemeinen Tabellenausdruck muss eine einzelne SELECT-Anweisung folgen. INSERT-, UPDATE-, DELETE- und MERGE-Anweisungen werden nicht unterstützt.
* Ein allgemeiner Tabellenausdruck, der Verweise auf sich selbst (einen rekursiven allgemeinen Tabellenausdrucks) enthält, wird nicht unterstützt (siehe nachfolgenden Abschnitt).
* Es ist maximal eine WITH-Klausel in einem allgemeinen Tabellenausdruck zulässig. Beispiel: Wenn eine Abfragedefinition für einen allgemeinen Tabellenausdruck eine Unterabfrage enthält, kann nicht diese Unterabfrage keine geschachtelte WITH-Klausel enthalten, die einen anderen allgemeinen Tabellenausdruck definiert.
* Eine ORDER BY-Klausel kann in der Abfragedefinition für einen allgemeinen Tabellenausdruck nur verwendet werden, wenn eine TOP-Klausel angegeben wurde.
* Wenn ein allgemeiner Tabellenausdruck in einer Anweisung verwendet wird, die Teil einer Batchinstanz ist, muss der Anweisung davor ein Semikolon folgen.
* Bei Verwendung in Anweisungen, die mit sp_prepare vorbereitet wurden, verhalten sich allgemeine Tabellenausdrücke genauso, wie andere SELECT-Anweisungen in PDW. Wenn CTEs jedoch als Teil von CETAS verwendet werden, die mit „sp_prepare“ vorbereitet werden, kann das Verhalten von SQL Server- und anderen PDW-Anweisungen aufgrund der Art, wie die Bindung für „sp_prepare“ implementiert wird, abweichen. Falls die das CTE referenzierende SELECT-Anweisung eine falsche Spalte verwendet, die im CTE nicht vorhanden ist, wird „sp_prepare“ übergeben, ohne dass der Fehler erkannt wird. Der Fehler wird dann aber während „sp_execute“ ausgegeben.

## <a name="recursive-ctes"></a>Rekursive CTEs

Rekursive CTEs werden in SQL Data Warehouse nicht unterstützt.  Die Migration rekursiver CTEs kann mehr oder weniger komplex sein, und die beste Vorgehensweise besteht darin, diese in mehrere Schritte aufzuteilen. In der Regel können Sie eine Schleife verwenden und eine temporäre Tabelle auffüllen, während Sie die rekursiven Zwischenabfragen durchlaufen. Sobald die temporäre Tabelle aufgefüllt ist, können Sie die Daten als ein einzelnes Resultset zurückgeben. Ein ähnlicher Ansatz wurde als Lösung für `GROUP BY WITH CUBE` im Artikel zur [GROUP BY-Klausel mit ROLLUP-, CUBE- oder GROUPING SETS-Option][group by clause with rollup / cube / grouping sets options] verwendet.

## <a name="unsupported-system-functions"></a>Nicht unterstützte Systemfunktionen

Es werden auch einige Systemfunktionen nicht unterstützt. Zu den wichtigsten Funktionen, die normalerweise in Data Warehousing verwendet, gehören u. a.:

* NEWSEQUENTIALID()
* @@NESTLEVEL()
* @@IDENTITY()
* @@ROWCOUNT()
* ROWCOUNT_BIG
* ERROR_LINE()

Einige dieser Probleme können umgangen werden.

## <a name="rowcount-workaround"></a>@@ROWCOUNT – Problemumgehung

Um die fehlende Unterstützung für @@ROWCOUNT zu umgehen, erstellen Sie eine gespeicherte Prozedur, die die letzte Zeilenanzahl aus „sys.dm_pdw_request_steps“ abruft, und führen Sie dann `EXEC LastRowCount` nach einer DML-Anweisung aus.

```sql
CREATE PROCEDURE LastRowCount AS
WITH LastRequest as
(   SELECT TOP 1    request_id
    FROM            sys.dm_pdw_exec_requests
    WHERE           session_id = SESSION_ID()
    AND             resource_class IS NOT NULL
    ORDER BY end_time DESC
),
LastRequestRowCounts as
(
    SELECT  step_index, row_count
    FROM    sys.dm_pdw_request_steps
    WHERE   row_count >= 0
    AND     request_id IN (SELECT request_id from LastRequest)
)
SELECT TOP 1 row_count FROM LastRequestRowCounts ORDER BY step_index DESC
;
```

## <a name="next-steps"></a>Nächste Schritte

Eine vollständige Liste aller unterstützten T-SQL-Anweisungen finden Sie in den [Themen zu Transact-SQL][Transact-SQL topics].

<!--Image references-->

<!--Article references-->
[ANSI joins on updates]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[ANSI joins on deletes]: ./sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[merge statement]: ./sql-data-warehouse-develop-ctas.md#replace-merge-statements
[INSERT..EXEC]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[Transact-SQL topics]: ./sql-data-warehouse-reference-tsql-statements.md

[cursors]: ./sql-data-warehouse-develop-loops.md
[group by clause with rollup / cube / grouping sets options]: ./sql-data-warehouse-develop-group-by-options.md
[nesting levels beyond 8]: ./sql-data-warehouse-develop-transactions.md
[updating through views]: ./sql-data-warehouse-develop-views.md
[use of select for variable assignment]: ./sql-data-warehouse-develop-variable-assignment.md
[no MAX data type for dynamic SQL strings]: ./sql-data-warehouse-develop-dynamic-sql.md

<!--MSDN references-->

<!--Other Web references-->
