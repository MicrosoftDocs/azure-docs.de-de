---
title: Artikel zu bekannten Problemen/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank | Microsoft-Dokumentation
description: Informationen zu bekannten Problemen/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/09/2019
ms.openlocfilehash: 00ed2f20884c3cd8f49307bd726f14f3007f884f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60534424"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank

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

1. Suchen Sie mithilfe der folgenden Abfrage die temporalen Tabellen im Quellschema.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Schließen Sie diese Tabellen auf dem Blatt **Migrationseinstellungen konfigurieren**, auf dem Sie die Tabellen für die Migration angeben, aus.

3. Führen Sie die Migrationsaktivität erneut aus.

**Ressourcen**

Weitere Informationen finden Sie im Artikel [Temporale Tabellen](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migration von Tabellen enthält eine oder mehrere Spalten mit dem Datentyp „hierarchyid“

**Symptom**

Möglicherweise wird während des Vorgangs „Full data load“ (Vollständiger Datenladevorgang) die SQL-Ausnahme „ntext ist mit hierarchyid nicht kompatibel.“ angezeigt:
     
![Beispiel für Fehler bei „hierarchyid“](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Problemumgehung**

1. Suchen Sie mithilfe der folgenden Abfrage die Benutzertabellen, die Spalten mit dem Datentyp „hierarchyid“ enthalten.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. Schließen Sie diese Tabellen auf dem Blatt **Migrationseinstellungen konfigurieren**, auf dem Sie die Tabellen für die Migration angeben, aus.

3. Führen Sie die Migrationsaktivität erneut aus.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Migrationsfehler mit verschiedenen Integritätsverletzungen bei aktiven Triggern im Schema während „Full data load“ (Vollständiger Datenladevorgang) oder „Inkrementelle Datensynchronisierung“

**Problemumgehung**

1. Suchen Sie mithilfe der folgenden Abfrage die Trigger, die derzeit in der Quelldatenbank aktiv sind:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Deaktivieren Sie mit den im Artikel [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017) angegebenen Schritten die Trigger in der Quelldatenbank.

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

In DMS wird der Zeitstempelwert der Quelle nicht migriert, stattdessen wird in der Zieltabelle ein neuer Zeitstempelwert generiert.

**Problemumgehung**

Wenn DMS den genauen in der Quelltabelle gespeicherten Zeitstempelwert migrieren soll, wenden Sie sich unter [Fragen zur Azure-Datenbankmigration](mailto:AskAzureDatabaseMigrations@service.microsoft.com) an das Entwicklerteam.

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Für Fehler bei der Datenmigration werden keine zusätzlichen Details auf dem Blatt mit dem detaillierten Status der Datenbank angegeben.

**Symptom**

Wenn die Migrationsfehler in der Ansicht mit dem detaillierten Status der Datenbank angegeben werden, werden durch Auswählen des Links **Datenmigrationsfehler** im oberen Menüband möglicherweise keine spezifischen zusätzlichen Details zu den Migrationsfehlern angezeigt.

![Beispiel zu Datenmigrationsfehlern ohne angezeigte Details](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Problemumgehung**

Führen Sie die folgenden Schritte aus, um spezifische Details zu den Fehlern zu erhalten.

1. Schließen Sie das Blatt mit dem detaillierten Status der Datenbank, um den Bildschirm für die Migrationsaktivität anzuzeigen.

     ![Bildschirm für die Migrationsaktivität](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Wählen Sie **Fehlerdetails anzeigen** aus, um spezifische Fehlermeldungen anzuzeigen, anhand derer Sie die Migrationsfehler beheben können.
