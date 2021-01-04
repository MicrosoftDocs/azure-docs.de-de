---
title: 'Aktivieren und Deaktivieren von Datenaufbewahrungsrichtlinien: Azure SQL Edge'
description: Erfahren Sie, wie Sie Datenaufbewahrungsrichtlinien in Azure SQL Edge aktivieren und deaktivieren.
keywords: SQL Edge, Datenaufbewahrung
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 5b8dd911952a63ba8775f27a6128ff61e849e823
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861424"
---
# <a name="enable-and-disable-data-retention-policies"></a>Aktivieren und Deaktivieren von Datenaufbewahrungsrichtlinien

In diesem Thema ist beschrieben, wie Datenaufbewahrungsrichtlinien für eine Datenbank und eine Tabelle aktiviert und deaktiviert werden. 

## <a name="enable-data-retention-for-a-database"></a>Aktivieren von Datenaufbewahrung für eine Datenbank

Im folgenden Beispiel wird gezeigt, wie Datenaufbewahrung über [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options) aktiviert werden kann.

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Überprüfen, ob Datenaufbewahrung für eine Datenbank aktiviert ist

Der folgende Befehl kann verwendet werden, um zu überprüfen, ob Datenaufbewahrung für eine Datenbank aktiviert ist.
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Aktivieren von Datenaufbewahrung für eine Tabelle

Datenaufbewahrung muss für jede Tabelle aktiviert werden, für die Daten automatisch bereinigt werden sollen. Wenn Datenaufbewahrung für die Datenbank und die Tabelle aktiviert ist, wird die Tabelle regelmäßig von einer Hintergrundsystemaufgabe überprüft, um veraltete Zeilen zu erkennen und zu löschen. Datenaufbewahrung kann für eine Tabelle während der Tabellenerstellung mit [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) oder später mit [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) aktiviert werden.

Im folgenden Beispiel wird gezeigt, wie Datenaufbewahrung für eine Tabelle über [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) aktiviert werden kann. 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

Datenaufbewahrung wird für die Tabelle im `WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )`-Teil des CREATE TABLE-Befehls festgelegt. Für den Befehl werden die folgenden erforderlichen Parameter verwendet: 

- DATA_DELETION: kennzeichnet, ob Datenaufbewahrung aktiviert (ON) oder deaktiviert (OFF) ist.
- FILTER_COLUMN: der Name der Spalte in der Tabelle, die verwendet wird, um zu ermitteln, ob die Zeilen veraltet sind oder nicht. Die Filterspalte kann nur eine Spalte sein, die einen der folgenden Datentypen hat: 
    - Datum
    - SmallDateTime
    - Datetime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD: ein ganzzahliger Wert, gefolgt von einem Einheitendeskriptor. Die zulässigen Einheiten sind DAY, DAYS, WEEK, WEEKS, MONTH, MONTHS, YEAR und YEARS.

Im folgenden Beispiel wird gezeigt, wie Datenaufbewahrung für eine Tabelle über [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) aktiviert werden kann.  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Überprüfen, ob Datenaufbewahrung für eine Tabelle aktiviert ist

Der folgende Befehl kann verwendet werden, um die Tabellen zu überprüfen, für die Datenaufbewahrung aktiviert ist.

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Durch „data_retention_period =-1“ und „data_retention_period_unit = INFINITE“ ist angegeben, dass keine Datenaufbewahrung für die Tabelle festgelegt ist.

Die folgende Abfrage kann verwendet werden, um die Spalte zu ermitteln, die als FILTER_COLUMN für die Datenaufbewahrung verwendet wird. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="correlating-db-and-table-data-retention-settings"></a>Korrelieren von Aufbewahrungseinstellungen für Datenbank- und Tabellendaten

Die Datenaufbewahrungseinstellungen für die Datenbank und die Tabellen werden zusammen verwendet, um zu bestimmen, ob die automatische Bereinigung für veraltete Zeilen für die Tabellen ausgeführt wird oder nicht. 

|Datenbankoption | Tabellenoption | Verhalten |
|----------------|--------------|----------|
| OFF | OFF | Die Datenaufbewahrungsrichtlinie ist deaktiviert, und sowohl die automatische als auch die manuelle Bereinigung veralteter Datensätze sind deaktiviert.|
| OFF | EIN  | Die Datenaufbewahrungsrichtlinie ist für die Tabelle aktiviert. Die automatische Bereinigung veralteter Datensätze ist deaktiviert, es kann jedoch eine manuelle Bereinigung ausgeführt werden, um veraltete Datensätze zu bereinigen. |
| EIN | OFF | Die Datenaufbewahrungsrichtlinie ist auf Datenbankebene aktiviert. Da die Option auf Tabellenebene jedoch deaktiviert ist, gibt es keine aufbewahrungsbasierte Bereinigung veralteter Zeilen.|
| EIN | EIN | Die Datenaufbewahrungsrichtlinie ist sowohl für die Datenbank als auch die Tabellen aktiviert. Die automatische Bereinigung veralteter Datensätze ist aktiviert. |

## <a name="disable-data-retention-on-a-table"></a>Deaktivieren der Datenaufbewahrung für eine Tabelle 

Datenaufbewahrung kann für eine Tabelle über [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) deaktiviert werden. Der folgende Befehl kann verwendet werden, um die Datenaufbewahrung für eine Tabelle zu deaktivieren.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Deaktivieren der Datenaufbewahrung für eine Datenbank

Datenaufbewahrung kann für eine Datenbank über [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options) deaktiviert werden. Der folgende Befehl kann verwendet werden, um die Datenaufbewahrung für eine Datenbank zu deaktivieren.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über Datenaufbewahrungsrichtlinie](data-retention-overview.md)
- [Verwalten von historischen Daten mit einer Aufbewahrungsrichtlinie](data-retention-cleanup.md)
