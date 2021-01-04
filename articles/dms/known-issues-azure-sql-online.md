---
title: 'Bekannte Probleme: Onlinemigrationen zu SQL-Datenbank'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie mehr zu bekannten Problemen und Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank bei der Verwendung von Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 748c3f8e38b98b2cbdcfecdf7d755827230fdb3d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962466"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank

Im Folgenden werden bekannte Probleme und Einschränkungen in Bezug auf Onlinemigrationen von SQL Server zu Azure SQL-Datenbank beschrieben.

> [!IMPORTANT]
> Bei Onlinemigrationen von SQL Server zu Azure SQL-Datenbank wird die Migration von SQL_variant-Datentypen nicht unterstützt.

### <a name="migration-of-temporal-tables-not-supported"></a>Migration von temporalen Tabellen wird nicht unterstützt

**Symptom**

Wenn die Quelldatenbank eine oder mehrere temporale Tabellen umfasst, treten bei der Datenbankmigration während des Vorgangs „Full data load“ (Vollständiger Datenladevorgang) Fehler auf, und möglicherweise wird die folgende Meldung angezeigt:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Beispiel für Fehler bei temporalen Tabellen](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Problemumgehung**

Führen Sie die folgenden Schritte aus:

1. Suchen Sie mithilfe der folgenden Abfrage die temporalen Tabellen im Quellschema.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Schließen Sie diese Tabellen auf dem Blatt **Migrationseinstellungen konfigurieren**, auf dem Sie die Tabellen für die Migration angeben, aus.

3. Führen Sie die Migrationsaktivität erneut aus.

**Ressourcen**

Weitere Informationen finden Sie im Artikel [Temporale Tabellen](/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migration von Tabellen enthält eine oder mehrere Spalten mit dem Datentyp „hierarchyid“

**Symptom**

Möglicherweise wird während des Vorgangs „Full data load“ (Vollständiger Datenladevorgang) die SQL-Ausnahme „ntext ist mit hierarchyid nicht kompatibel.“ angezeigt:

![Beispiel für Fehler bei „hierarchyid“](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Problemumgehung**

Führen Sie die folgenden Schritte aus:

1. Suchen Sie mithilfe der folgenden Abfrage die Benutzertabellen, die Spalten mit dem Datentyp „hierarchyid“ enthalten.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Schließen Sie diese Tabellen auf dem Blatt **Migrationseinstellungen konfigurieren**, auf dem Sie die Tabellen für die Migration angeben, aus.

3. Führen Sie die Migrationsaktivität erneut aus.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Migrationsfehler mit verschiedenen Integritätsverletzungen bei aktiven Triggern im Schema während „Full data load“ (Vollständiger Datenladevorgang) oder „Inkrementelle Datensynchronisierung“

**Problemumgehung**

Führen Sie die folgenden Schritte aus:

1. Suchen Sie mithilfe der folgenden Abfrage die Trigger, die derzeit in der Quelldatenbank aktiv sind:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Deaktivieren Sie mit den im Artikel [DISABLE TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) angegebenen Schritten die Trigger in der Quelldatenbank.

3. Führen Sie die Migrationsaktivität erneut aus.

### <a name="support-for-lob-data-types"></a>Unterstützung für LOB-Datentypen

**Symptom**

Wenn die Länge der LOB-Spalte (Large Object) 32 KB überschreitet, werden die Daten möglicherweise am Ziel abgeschnitten. Mithilfe der folgenden Abfrage können Sie die Länge der LOB-Spalte überprüfen:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Problemumgehung**

Wenn Sie über eine LOB-Spalte verfügen, die größer als 32 KB ist, wenden Sie sich unter [Fragen zur Azure-Datenbankmigration](mailto:AskAzureDatabaseMigrations@service.microsoft.com) an das technische Team.

### <a name="issues-with-timestamp-columns"></a>Probleme bei Zeitstempelspalten

**Symptom**

Azure Database Migration Service migriert nicht den Zeitstempelwert der Quelle, sondern generiert einen neuen Zeitstempelwert in der Zieltabelle.

**Problemumgehung**

Wenn Azure Database Migration Service den genauen in der Quelltabelle gespeicherten Zeitstempelwert migrieren soll, wenden Sie sich unter [Fragen zur Azure-Datenbankmigration](mailto:AskAzureDatabaseMigrations@service.microsoft.com) an das Entwicklerteam.

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Für Fehler bei der Datenmigration werden keine zusätzlichen Details auf dem Blatt mit dem detaillierten Status der Datenbank angegeben

**Symptom**

Wenn die Migrationsfehler in der Ansicht mit dem detaillierten Status der Datenbank angegeben werden, werden durch Auswählen des Links **Datenmigrationsfehler** im oberen Menüband möglicherweise keine spezifischen zusätzlichen Details zu den Migrationsfehlern angezeigt.

![Beispiel zu Datenmigrationsfehlern ohne angezeigte Details](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Problemumgehung**

Führen Sie folgende Schritte aus, um spezifische Fehlerdetails abzurufen.

1. Schließen Sie das Blatt mit dem detaillierten Status der Datenbank, um den Bildschirm für die Migrationsaktivität anzuzeigen.

     ![Bildschirm für die Migrationsaktivität](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Wählen Sie **Fehlerdetails anzeigen** aus, um spezifische Fehlermeldungen anzuzeigen, anhand derer Sie die Migrationsfehler beheben können.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Keine Unterstützung für den Datentyp „Geography“ (Geografie) in der SQLDB-Onlinemigration

**Symptom**

Bei der Migration wird folgende Fehlermeldung angezeigt:

```output
"** encountered a fatal error", "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode.
```

**Problemumgehung**

Azure Database Migration Service unterstützt den Datentyp „Geography“ (Geografie) für Offlinemigrationen zu Azure SQL-Datenbank, jedoch nicht für Onlinemigrationen. Probieren Sie alternative Methoden zum Ändern des Datentyps der Quelle in einen unterstützten Typ aus, bevor Sie versuchen, für diese Datenbank eine Onlinemigration mithilfe von Azure Database Migration Service durchzuführen.

### <a name="supported-editions"></a>Unterstützte Editionen

**Symptom**

Bei der Migration wird folgende Fehlermeldung angezeigt:

```output
Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].
```

**Problemumgehung**

Die Unterstützung für Onlinemigrationen zu Azure SQL-Datenbank mithilfe von Azure Database Migration Service gilt nur für die Editionen Enterprise, Standard und Developer. Vergewissern Sie sich vor Beginn des Migrationsvorgangs, dass Sie eine unterstützte Edition verwenden.