---
title: Laden von Daten aus einer CSV-Datei in eine Datenbank (BCP)
description: Für kleinere Datenmengen wird BCP zum Importieren von Daten in Azure SQL-Datenbank verwendet.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d2d501206dcb1fc6b9bb545f9dcf4d5c61af994e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92777876"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Laden von Daten aus einer CSV-Datei in Azure SQL-Datenbank oder Azure SQL Managed Instance (Flatfiles)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Sie können das Befehlszeilenprogramm BCP verwenden, um Daten aus einer CSV-Datei in Azure SQL-Datenbank oder Azure SQL Managed Instance zu importieren.

## <a name="before-you-begin"></a>Voraussetzungen

### <a name="prerequisites"></a>Voraussetzungen

Zur Durchführung der in diesem Artikel aufgeführten Schritte ist Folgendes erforderlich:

* Eine Datenbank in Azure SQL-Datenbank
* Eine Installation des Befehlszeilenprogramms bcp
* Eine Installation des Befehlszeilenprogramms sqlcmd

Sie können die Dienstprogramme bcp und sqlcmd im [Microsoft Download Center][Microsoft Download Center]herunterladen.

### <a name="data-in-ascii-or-utf-16-format"></a>Daten im ASCII- oder UTF-16-Format

Wenn Sie in diesem Tutorial Ihre eigenen Daten verwenden möchten, müssen die Daten in ASCII- oder UTF-16-Codierung vorlegen, da UTF-8 von bcp nicht unterstützt wird.

## <a name="1-create-a-destination-table"></a>1. Erstellen einer Zieltabelle

Definieren Sie eine Tabelle in SQL-Datenbank als Zieltabelle. Die Spalten der Tabelle müssen auf die Daten in den Zeilen der Datendatei abgestimmt sein.

Führen Sie zum Erstellen einer Tabelle an einer Eingabeaufforderung den Befehl „sqlcmd.exe“ aus:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. Erstellen einer Quelldatendatei

Öffnen Sie Editor, kopieren Sie die folgenden Datenzeilen in eine neue Textdatei, und speichern Sie diese Datei im lokalen temporären Verzeichnis C:\Temp\DimDate2.txt. Diese Daten liegen im ASCII-Format vor.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Optional) Wenn Sie Ihre eigenen Daten aus einer SQL Server-Datenbank exportieren möchten, führen Sie an einer Eingabeaufforderung den folgenden Befehl aus. Ersetzen Sie „TableName“, „ServerName“, „DatabaseName“, „Username“ und „Password“ durch Ihre eigenen Informationen.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. Laden der Daten

Führen Sie zum Laden der Daten an einer Eingabeaufforderung den folgenden Befehl aus, und ersetzen Sie dabei die Werte für Servername, Datenbankname, Benutzername und Kennwort durch Ihre eigenen Informationen:

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Vergewissern Sie sich mithilfe des folgenden Befehls, dass die Daten ordnungsgemäß geladen wurden:

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Das Ergebnis sollte in etwa wie folgt aussehen:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Migrieren einer SQL Server-Datenbank finden Sie im Artikel zur [SQL Server-Datenbankmigration](database/migrate-to-database-from-sql-server.md).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433