---
title: Verwenden gespeicherter Prozeduren in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Tipps zum Implementieren von gespeicherten Prozeduren in Azure SQL Data Warehouse für die Entwicklung von Lösungen
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 200433d95d62edf2e878e58e5089a6baff290775
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65850589"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Verwenden gespeicherter Prozeduren in Azure SQL Data Warehouse
Tipps zum Implementieren von gespeicherten Prozeduren in Azure SQL Data Warehouse für die Entwicklung von Lösungen

## <a name="what-to-expect"></a>Ausblick

SQL Data Warehouse unterstützt viele der T-SQL-Features, die in SQL Server verwendet werden. Darüber hinaus sind bestimmte Features für das horizontale Hochskalieren vorhanden, mit denen Sie die Leistung Ihrer Lösung verbessern können.

In Bezug auf die Verwaltung der Skalierung und Leistung von SQL Data Warehouse sind auch einige Features und Funktionen vorhanden, die Unterschiede beim Verhalten aufweisen. Es gibt auch Features, die nicht unterstützt werden.


## <a name="introducing-stored-procedures"></a>Einführung in gespeicherte Prozeduren
Gespeicherte Prozeduren sind eine hervorragende Möglichkeit zum Einschließen (Kapseln) von SQL-Code. Hierbei wird Code im Data Warehouse in der Nähe Ihrer Daten gespeichert. Gespeicherte Prozeduren unterstützen Entwickler beim Modularisieren ihrer Lösungen, indem Code in besser verwaltbare Einheiten eingeschlossen wird. Dies ermöglicht eine bessere Wiederverwendbarkeit des Codes. Für jede gespeicherte Prozedur können außerdem Parameter verwendet werden, um sie flexibler zu machen.

SQL Data Warehouse bietet eine vereinfachte und optimierte Implementierung von gespeicherten Prozeduren. Der größte Unterschied im Vergleich zu SQL Server ist, dass die gespeicherte Prozedur kein vorab kompilierter Code ist. In Data Warehouses ist die Kompilierzeit im Vergleich zu der benötigten Zeit, um Abfragen für große Datenmengen auszuführen, relativ gering. Es ist wichtiger sicherzustellen, dass der Code der gespeicherten Prozedur für große Abfragen ordnungsgemäß optimiert ist. Das Ziel besteht darin, Stunden, Minuten und Sekunden zu sparen, keine Millisekunden. Es ist daher hilfreicher, gespeicherte Prozeduren als Container für SQL-Logik zu betrachten.     

Wenn SQL Data Warehouse Ihre gespeicherte Prozedur ausführt, werden die SQL-Anweisungen zur Laufzeit analysiert, übersetzt und optimiert. Während dieses Vorgangs wird jede Anweisung in verteilte Abfragen konvertiert. Der SQL-Code, der für die Daten ausgeführt wird, unterscheidet sich von der übermittelten Abfrage.

## <a name="nesting-stored-procedures"></a>Schachteln von gespeicherten Prozeduren
Wenn gespeicherte Prozeduren andere gespeicherte Prozeduren aufrufen oder dynamischen SQL-Code ausführen, wird die innere gespeicherte Prozedur bzw. der Codeaufruf als „geschachtelt“ bezeichnet.

SQL Data Warehouse unterstützt maximal acht Schachtelungsebenen. Dies ist ein Unterschied zu SQL Server. In SQL Server sind 32 Schachtelungsebenen zulässig.

Der Aufruf der obersten gespeicherten Prozedur entspricht Schachtelungsebene 1.

```sql
EXEC prc_nesting
```
Wenn die gespeicherte Prozedur auch einen weiteren EXEC-Aufruf durchführt, wird die Schachtelungsebene auf 2 erhöht.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Wenn die zweite Prozedur dann dynamischen SQL-Code ausführt, wird die Schachtelungsebene auf 3 erhöht.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Beachten Sie, dass [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql) von SQL Data Warehouse derzeit nicht unterstützt wird. Sie müssen die Schachtelungsebene nachverfolgen. Es ist unwahrscheinlich, dass Sie die Grenze von acht Schachtelungsebenen überschreiten. Sollte dies dennoch der Fall sein, müssen Sie Ihren Code so überarbeiten, damit dieser innerhalb des Grenzwerts bleibt.

## <a name="insertexecute"></a>INSERT..EXECUTE
SQL Data Warehouse lässt nicht zu, dass Sie das Resultset einer gespeicherten Prozedur mit einer INSERT-Anweisung verwenden. Es gibt aber eine andere Möglichkeit. Ein Beispiel finden Sie im Artikel unter [Temporäre Tabellen](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Einschränkungen
Es gibt einige Aspekte von gespeicherten Transact-SQL-Prozeduren, die in SQL Data Warehouse nicht implementiert sind.

Sie lauten wie folgt:

* Temporäre gespeicherte Prozeduren
* Nummerierte gespeicherte Prozeduren
* Erweiterte gespeicherte Prozeduren
* Gespeicherte CLR-Prozeduren
* Verschlüsselungsoption
* Replikationsoption
* Tabellenwertparameter
* Schreibgeschützte Parameter
* Standardparameter
* Ausführungskontexte
* return-Anweisung

## <a name="next-steps"></a>Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht](sql-data-warehouse-overview-develop.md).

