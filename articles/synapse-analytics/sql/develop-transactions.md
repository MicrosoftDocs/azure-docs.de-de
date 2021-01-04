---
title: Verwenden von Transaktionen
description: Tipps zum Implementieren von Transaktionen mit einem dedizierten SQL-Pool in Azure Synapse Analytics zum Entwickeln von Lösungen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: c4fe512ff6db24498148ffa724c3144a2f61823f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451715"
---
# <a name="use-transactions-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>Verwenden von Transaktionen mit dem dedizierten SQL-Pool in Azure Synapse Analytics

Tipps zum Implementieren von Transaktionen mit einem dedizierten SQL-Pool in Azure Synapse Analytics zum Entwickeln von Lösungen.

## <a name="what-to-expect"></a>Ausblick

Wie zu erwarten, unterstützt ein dedizierter SQL-Pool Transaktionen als Teil der Data Warehouse-Workload. Um allerdings eine angemessene Leistung des dedizierten SQL-Pools sicherzustellen, wurden einige Features im Vergleich zu SQL Server eingeschränkt. In diesem Artikel werden die Unterschiede hervorgehoben und die anderen Features aufgelistet.

## <a name="transaction-isolation-levels"></a>Transaktionsisolationsstufen

Dedizierte SQL-Pools implementieren ACID-Transaktionen. Die Isolationsstufe der Transaktionsunterstützung ist standardmäßig auf READ UNCOMMITTED (Lesen ohne Commit) festgelegt.  Sie können diese in READ COMMITTED SNAPSHOT ISOLATION (Read Committed-Momentaufnahmeisolation) ändern, indem Sie die Datenbankoption READ_COMMITTED_SNAPSHOT für eine Benutzerdatenbank aktivieren, wenn Sie mit der Masterdatenbank verbunden sind.  

Nach der Aktivierung werden alle Transaktionen in dieser Datenbank unter READ COMMITTED SNAPSHOT ISOLATION ausgeführt, und die Einstellung READ UNCOMMITTED auf Sitzungsebene wird nicht berücksichtigt. Ausführliche Informationen finden Sie unter [ALTER DATABASE SET-Optionen (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest&preserve-view=true).

## <a name="transaction-size"></a>Transaktionsgröße
Eine einzelne Transaktion zur Datenänderung ist in Bezug auf die Größe beschränkt. Der Grenzwert wird pro Verteilung angewendet. Die Gesamtzuordnung kann also ermittelt werden, indem der Grenzwert mit der Verteilungsanzahl multipliziert wird. 

Um eine Annäherung für die maximale Zeilenanzahl in der Transaktion zu erhalten, teilen Sie die Verteilungsobergrenze durch die Gesamtgröße jeder Zeile. Bei Spalten mit variabler Länge können Sie erwägen, anstelle der maximalen Größe eine durchschnittliche Spaltenlänge zu verwenden.

Für die Tabelle unten gelten die folgenden Annahmen:

* Gleichmäßige Verteilung der Daten
* Durchschnittliche Zeilenlänge beträgt 250 Byte

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Obergrenze pro Verteilung (GB) | Anzahl der Verteilungen | Max. Transaktionsgröße (GB) | Anzahl der Zeilen pro Verteilung | Max. Zeilenzahl pro Transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200c |1.5 |60 |90 |6\.000.000 |360.000.000 |
| DW300c |2,25 |60 |135 |9\.000.000 |540.000.000 |
| DW400c |3 |60 |180 |12.000.000 |720.000.000 |
| DW500c |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW1000c |7,5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1500c |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000c |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW2500c |18,75 |60 |1125 |75.000.000 |4\.500.000.000 |
| DW3000c |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW5000c |37,5 |60 |2\.250 |150.000.000 |9\.000.000.000 |
| DW6000c |45 |60 |2\.700 |180.000.000 |10.800.000.000 |
| DW7500c |56,25 |60 |3\.375 |225.000.000 |13.500.000.000 |
| DW10000c |75 |60 |4\.500 |300.000.000 |18.000.000.000 |
| DW15000c |112,5 |60 |6\.750 |450.000.000 |27.000.000.000 |
| DW30000c |225 |60 |13.500 |900.000.000 |54.000.000.000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Obergrenze pro Verteilung (GB) | Anzahl der Verteilungen | Max. Transaktionsgröße (GB) | Anzahl der Zeilen pro Verteilung | Max. Zeilenzahl pro Transaktion |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4\.000.000 |240.000.000 |
| DW200 |1.5 |60 |90 |6\.000.000 |360.000.000 |
| DW300 |2,25 |60 |135 |9\.000.000 |540.000.000 |
| DW400 |3 |60 |180 |12.000.000 |720.000.000 |
| DW500 |3,75 |60 |225 |15.000.000 |900.000.000 |
| DW600 |4,5 |60 |270 |18.000.000 |1\.080.000.000 |
| DW1000 |7,5 |60 |450 |30.000.000 |1\.800.000.000 |
| DW1200 |9 |60 |540 |36.000.000 |2\.160.000.000 |
| DW1500 |11,25 |60 |675 |45.000.000 |2\.700.000.000 |
| DW2000 |15 |60 |900 |60.000.000 |3\.600.000.000 |
| DW3000 |22,5 |60 |1\.350 |90.000.000 |5\.400.000.000 |
| DW6000 |45 |60 |2\.700 |180.000.000 |10.800.000.000 |

Die Obergrenze für die Transaktionsgröße wird pro Transaktion oder Vorgang angewendet. Sie wird nicht übergreifend für alle gleichzeitigen Transaktionen angewendet. Daher ist es für jede Transaktion zulässig, diese Menge an Daten in das Protokoll zu schreiben.

Informationen zum Optimieren und Reduzieren der Datenmenge, die in das Protokoll geschrieben wird, finden Sie im Artikel [Optimieren von Transaktionen in Synapse SQL](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

> [!WARNING]
> Die maximale Transaktionsgröße kann nur für Tabellen mit HASH- oder ROUND_ROBIN-Verteilung erreicht werden, bei denen die Daten gleichmäßig verteilt werden. Wenn bei der Transaktion Daten auf verzerrte Weise in die Verteilungen geschrieben werden, wird die Obergrenze wahrscheinlich vor der maximalen Transaktionsgröße erreicht.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Transaktionsstatus

Dedizierte SQL-Pools verwenden die XACT_STATE()-Funktion, um einen Transaktionsfehler mit dem Wert „-2“ zu melden. Dieser Wert bedeutet, dass die Transaktion fehlerhaft und nur für den Rollback markiert ist.

> [!NOTE]
> Die Verwendung von "-2" in der XACT_STATE-Funktion zum Kennzeichnen einer fehlgeschlagenen Transaktion stellt für SQL Server unterschiedliche Verhalten dar. SQL Server verwendet den Wert "-1" für eine Transaktion, für die kein Commit durchgeführt werden kann. SQL Server kann einige Fehler innerhalb einer Transaktion tolerieren, ohne als nicht commitfähig gekennzeichnet zu werden. `SELECT 1/0` würde beispielsweise einen Fehler verursachen, aber keinen Transaktionszustand erzwingen, der keinen Commit zulässt. SQL Server lässt auch Lesevorgänge in der nicht commitfähigen Transaktion zu. Ein dedizierter SQL-Pool lässt dies dagegen nicht zu. Wenn in einer Transaktion mit einem dedizierten SQL-Pool ein Fehler auftritt, wird die Transaktion sofort in den Zustand „– 2“ versetzt. Sie können erst dann weitere SELECT-Anweisungen ausführen, wenn ein Rollback für die ursprüngliche Anweisung erfolgt ist. Es ist daher wichtig, zu überprüfen, ob in Ihrem Anwendungscode XACT_STATE() verwendet wird, da Sie den Code möglicherweise ändern müssen.

In SQL Server kann z.B. eine Transaktion wie die folgende angezeigt werden:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Der vorangehende Code gibt die folgende Fehlermeldung aus:

Meldung 111233, Ebene 16, Status 1, Zeile 1 111233; Die aktuelle Transaktion wurde abgebrochen, und alle ausstehenden Änderungen wurden zurückgesetzt. Ursache: Für eine Transaktion in einem Nur-Rollback-Status wurde vor einer DDL-, DML- oder SELECT-Anweisung kein explizites Rollback ausgeführt.

Sie erhalten auch keine Ausgabe der ERROR_*-Funktionen.

Bei einem dedizierten SQL-Pool muss der Code geringfügig geändert werden:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Jetzt werden Sie das erwartete Verhalten feststellen. Der Fehler in der Transaktion wird verwaltet, und die ERROR_*-Funktionen geben wie erwartet Werte zurück.

Die einzige Änderung besteht darin, dass die ROLLBACK-Ausführung für die Transaktion vor dem Lesen der Fehlerinformationen im CATCH-Block erfolgen muss.

## <a name="error_line-function"></a>Error_Line()-Funktion

Es ist auch erwähnenswert, dass die ERROR_LINE()-Funktion im dedizierten SQL-Pool nicht implementiert oder unterstützt wird. Wenn diese Funktion in Ihrem Code enthalten ist, müssen Sie sie entfernen, um Kompatibilität mit dem dedizierten SQL-Pool zu gewährleisten. Verwenden Sie stattdessen Abfragebezeichnungen in Ihrem Code, um entsprechende Funktionalität zu implementieren. Weitere Informationen finden Sie im Artikel [Verwenden von Abfragebezeichnungen in Synapse SQL](develop-label.md).

## <a name="use-of-throw-and-raiserror"></a>Verwenden von THROW und RAISERROR

THROW ist die modernere Implementierung zum Auslösen von Ausnahmen im dedizierten SQL-Pool, RAISERROR wird jedoch ebenfalls unterstützt. Es gibt aber einige erwähnenswerte Unterschiede.

* Benutzerdefinierte Fehlermeldungsnummern können für THROW nicht im Bereich 100.000 bis 150.000 liegen.
* RAISERROR-Fehlermeldungen sind bei 50.000 festgelegt.
* Die Verwendung von "sys.messages" wird nicht unterstützt.

## <a name="limitations"></a>Einschränkungen

Dedizierte SQL-Pools verfügen über einige weitere Einschränkungen in Bezug auf Transaktionen. Dies sind:

* Keine verteilten Transaktionen
* Keine geschachtelten Transaktionen zulässig
* Keine Speicherpunkte zulässig
* Keine benannten Transaktionen
* Keine markierten Transaktionen
* Keine Unterstützung für DDL wie z.B. CREATE TABLE innerhalb von benutzerdefinierten Transaktionen

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Optimieren von Transaktionen finden Sie unter [Bewährte Methoden für Transaktionen](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). Weitere Leitfäden zu bewährten Methoden gibt es auch für [dedizierte SQL-Pools](best-practices-sql-pool.md) und [serverlose SQL-Pools](best-practices-sql-on-demand.md).
