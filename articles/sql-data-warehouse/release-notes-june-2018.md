---
title: Azure SQL Data Warehouse – Versionshinweise Juni 2018 | Microsoft-Dokumentation
description: Versionshinweise zu Azure SQL Data Warehouse
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 95c59d3e5504058e27cdb4eda311c3917d6c834a
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65912227"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Neuerungen in Azure SQL Data Warehouse Juni 2018
Azure SQL Data Warehouse wird fortlaufend verbessert. In diesem Artikel informieren wir Sie über neue Features und Änderungen, die im Juni 2018 eingeführt wurden. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Benutzerdefinierte Wiederherstellungspunkte
SQL Data Warehouse erstellt automatisch alle acht Stunden Momentaufnahmen Ihres Data Warehouse und garantiert somit ein RPO (Recovery Point Objective) von acht Stunden. Auch wenn diese automatisierten Momentaufnahmen den Verwaltungsaufwand Ihres Data Warehouse vereinfachen, besteht die Notwendigkeit, zu kritischen Zeiten basierend auf Ihren geschäftlichen Anforderungen Momentaufnahmen zu erstellen. Beispiele: Erstellen einer Momentaufnahme direkt vor einer wichtigen Datenauslastung oder Bereitstellung neuer Skripts im Data Warehouse zum Ermöglichen eines Wiederherstellungspunkts direkt vor dem Vorgang. 

SQL Data Warehouse unterstützt jetzt auch [benutzerdefinierte Wiederherstellungspunkte](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) über das Cmdlet [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint).

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Sicherheit auf Spaltenebene
Die Verwaltung des Datenzugriffs und der Sicherheit in Ihrem Data Warehouse ist wichtig für den Aufbau von Vertrauen bei Ihren Kunden und Partnern. SQL Data Warehouse [unterstützt jetzt Sicherheit auf Spaltenebene](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/), mit der Sie Berechtigungen zum Anzeigen vertraulicher Daten durch Einschränken des Benutzerzugriffs auf bestimmte Spalten in Ihren Tabellen ohne Umgestalten Ihres Data Warehouse anpassen können.

Mit der Sicherheit auf Spaltenebene können Sie den Zugriff auf Datentabellenspalten basierend auf dem Ausführungskontext oder der Gruppenmitgliedschaft des Benutzers mit der Standard-T-SQL-Anweisung [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) steuern. Die Zugriffsbeschränkungslogik befindet sich auf der Datenbankebene und nicht auf einer anderen, von den Daten getrennten Anwendungsebene und vereinfacht dadurch die allgemeine Sicherheitsimplementierung.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
Die [OBJECT_SCHEMA_NAME()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql)-Funktion gibt den Namen der Datenbankschemas für Objekte mit Schemabereich zurück. Diese Funktion ist in ETL-Tools für die Objektschemavalidierung gebräuchlich geworden. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Beispielergebnisse**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>Unterstützung für die Ansicht sys.time_zone_info
Die Ansicht [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) gibt Informationen zu den unterstützten Zeitzonen in Azure SQL Data Warehouse zurück.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Beispielergebnisse**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Anzeige von Vorgängen für automatische Statistiken in sys.dm_pdw_exec_requests (Behavior Change)

Mit der Einführung von [Statistiken automatisch erstellen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics) generiert Azure SQL Data Warehouse Statistiken, um die Ausführung von Abfragen zu optimieren. Die Release von Juni 2018 bietet die Möglichkeit, zu überwachen, wann Statistiken automatisch generiert werden. Dazu wird in der Ansicht [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) ein Datensatz hinzugefügt, wenn ein [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql)-Vorgang ausgeführt wird.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Beispielergebnisse**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich mit den Grundlagen von SQL Data Warehouse vertraut gemacht haben, können Sie sich nun darüber informieren, wie Sie schnell [eine SQL Data Warehouse-Instanz erstellen][create a SQL Data Warehouse]. Falls Sie mit Azure noch nicht vertraut sind und auf neue Terminologie stoßen, ist das [Azure-Glossar][Azure glossary] sehr nützlich. Weitere SQL Data Warehouse-Ressourcen:  

* [Kundenerfolgsgeschichten]
* [Blogs]
* [Funktionsanfragen]
* [Videos]
* [Customer Advisory Team-Blogs]
* [Stack Overflow-Forum]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Customer Advisory Team-Blogs]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Kundenerfolgsgeschichten]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funktionsanfragen]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-Forum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
