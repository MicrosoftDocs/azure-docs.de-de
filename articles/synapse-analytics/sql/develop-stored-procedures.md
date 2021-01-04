---
title: Verwenden von gespeicherten Prozeduren
description: Tipps zum Implementieren gespeicherter Prozeduren mithilfe von Synapse SQL in Azure Synapse Analytics zur Lösungsentwicklung.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3940d762dbc249e0303ddf905acbeeed7f96aa4f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315554"
---
# <a name="stored-procedures-using-synapse-sql-in-azure-synapse-analytics"></a>Implementieren von gespeicherten Prozeduren mithilfe von Synapse SQL in Azure Synapse Analytics

In Synapse SQL bereitgestellte und serverlose Pools ermöglichen es Ihnen, komplexe Datenverarbeitungslogik in gespeicherte SQL-Prozeduren einzufügen. Gespeicherte Prozeduren sind eine hervorragende Möglichkeit, um SQL-Code einzuschließen (kapseln) und im Data Warehouse in der Nähe Ihrer Daten zu speichern. Gespeicherte Prozeduren unterstützen Entwickler beim Modularisieren ihrer Lösungen, indem Code in besser verwaltbare Einheiten eingeschlossen wird. So lässt sich der Code besser wiederverwenden. Für jede gespeicherte Prozedur können außerdem Parameter verwendet werden, um sie flexibler zu machen.
In diesem Artikel finden Sie einige Tipps zum Implementieren gespeicherter Prozeduren in einem Synapse SQL-Pool zum Entwickeln von Lösungen.

## <a name="what-to-expect"></a>Ausblick

Synapse SQL unterstützt viele der T-SQL-Features, die in SQL Server verwendet werden. Darüber hinaus sind bestimmte Features für das horizontale Hochskalieren vorhanden, mit denen Sie die Leistung Ihrer Lösung verbessern können. In diesem Artikel erfahren Sie etwas über die Features, die Sie in gespeicherte Prozeduren einfügen können.

> [!NOTE]
> Im Text der Prozedur können Sie nur die Features verwenden, die auf der Synapse SQL-Oberfläche unterstützt werden. Lesen Sie [diesen Artikel](overview-features.md), um Objekte und Anweisungen zu identifizieren, die in gespeicherten Prozeduren verwendet werden können. In den Beispielen in diesen Artikeln werden allgemeine Features verwendet, die sowohl auf der serverlosen als auch auf der dedizierten Oberfläche verfügbar sind. Am Ende dieses Artikels finden Sie weitere [Einschränkungen in bereitgestellten und serverlosen Synapse SQL-Pools](#limitations).

In Bezug auf die Verwaltung der Skalierung und Leistung des SQL-Pools sind auch einige Features und Funktionen vorhanden, die Unterschiede beim Verhalten aufweisen. Es gibt auch Features, die nicht unterstützt werden.

## <a name="stored-procedures-in-synapse-sql"></a>Gespeicherte Prozeduren in Synapse SQL

Im folgenden Beispiel werden die Prozeduren zum Löschen externer Objekte gezeigt, wenn Sie in der-Datenbank vorhanden sind:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Diese Prozeduren können mit der `EXEC`-Anweisung ausgeführt werden, in der Sie den Prozedurnamen und -parameter angeben können:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL bietet eine vereinfachte und optimierte Implementierung von gespeicherten Prozeduren. Der größte Unterschied im Vergleich zu SQL Server ist, dass die gespeicherte Prozedur kein vorab kompilierter Code ist. In Data Warehouses ist die Kompilierzeit im Vergleich zu der benötigten Zeit, um Abfragen für große Datenmengen auszuführen, relativ gering. Es ist wichtiger sicherzustellen, dass der Code der gespeicherten Prozedur für große Abfragen ordnungsgemäß optimiert ist. Das Ziel besteht darin, Stunden, Minuten und Sekunden zu sparen, keine Millisekunden. Es ist daher hilfreicher, gespeicherte Prozeduren als Container für SQL-Logik zu betrachten.

Wenn Synapse SQL Ihre gespeicherte Prozedur ausführt, werden die SQL-Anweisungen zur Laufzeit analysiert, übersetzt und optimiert. Während dieses Vorgangs wird jede Anweisung in verteilte Abfragen konvertiert. Der SQL-Code, der für die Daten ausgeführt wird, unterscheidet sich von der übermittelten Abfrage.

## <a name="encapsulate-validation-rules"></a>Kapseln von Validierungsregeln

Gespeicherte Prozeduren ermöglichen Ihnen die Suche nach Validierungslogik in einem einzelnen in der SQL-Datenbank gespeicherten Modul. Im folgenden Beispiel sehen Sie, wie die Werte von Parametern validiert und deren Standardwerte geändert werden.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

Die Logik in der SQL-Prozedur validiert die Eingabeparameter, wenn die Prozedur aufgerufen wird.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Schachteln von gespeicherten Prozeduren

Wenn gespeicherte Prozeduren andere gespeicherte Prozeduren aufrufen oder dynamischen SQL-Code ausführen, wird die innere gespeicherte Prozedur bzw. der Codeaufruf als „geschachtelt“ bezeichnet.
Im folgenden Code wird ein Beispiel für eine geschachtelte Prozedur gezeigt:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Diese Prozedur akzeptiert einen Parameter, der einen bestimmten Namen darstellt, und ruft dann andere Prozeduren auf, um die Objekte mit diesem Namen zu abzulegen.
Ein Synapse SQL-Pool unterstützt maximal acht Schachtelungsebenen. Diese Funktion unterscheidet sich geringfügig von SQL Server. In SQL Server sind 32 Schachtelungsebenen zulässig.

Der Aufruf der obersten gespeicherten Prozedur entspricht Schachtelungsebene 1.

```sql
EXEC clean_up 'mytest'
```

Wenn die gespeicherte Prozedur auch einen weiteren EXEC-Aufruf durchführt, wird die Schachtelungsebene auf 2 erhöht.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Wenn die zweite Prozedur dann dynamischen SQL-Code ausführt, wird die Schachtelungsebene auf 3 erhöht.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL unterstützt derzeit kein [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Sie müssen die Schachtelungsebene nachverfolgen. Es ist unwahrscheinlich, dass Sie die Grenze von acht Schachtelungsebenen überschreiten. Sollte dies dennoch der Fall sein, müssen Sie Ihren Code so überarbeiten, damit dieser innerhalb des Grenzwerts bleibt.

## <a name="insertexecute"></a>INSERT..EXECUTE

Der bereitgestellte Synapse SQL-Pool lässt nicht zu, dass Sie das Resultset einer gespeicherten Prozedur bei einer INSERT-Anweisung verwenden. Es gibt jedoch eine andere Möglichkeit. Ein Beispiel finden Sie im Artikel unter [Temporäre Tabellen](develop-tables-temporary.md) für einen bereitgestellten Synapse SQL-Pool.

## <a name="limitations"></a>Einschränkungen

Es gibt einige Aspekte von gespeicherten Transact-SQL-Prozeduren, die nicht in Synapse SQL implementiert sind, z. B.:

| Feature/Option | Bereitgestellt | Serverlos |
| --- | --- |
| Temporäre gespeicherte Prozeduren | Nein  | Ja |
| numbered_stored_procedures | Nein  | Nein  |
| Erweiterte gespeicherte Prozeduren | Nein  | Nein  |
| Gespeicherte CLR-Prozeduren | Nein  | Nein  |
| Verschlüsselungsoption | Nein  | Ja |
| Replikationsoption | Nein  | Nein  |
| Tabellenwertparameter | Nein  | Nein  |
| Schreibgeschützte Parameter | Nein  | Nein  |
| Standardparameter | Nein  | Ja |
| Ausführungskontexte | Nein  | Nein  |
| Return-Anweisung | Nein  | Ja |
| INSERT INTO ... EXEC | Nein  | Ja |

## <a name="next-steps"></a>Nächste Schritte

Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](develop-overview.md).
