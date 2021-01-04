---
title: 'Bekannte Probleme: Onlinemigrationen von PostgreSQL zu Azure Database for PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie mehr zu bekannten Problemen und Migrationseinschränkungen bei Onlinemigrationen von PostgreSQL zu Azure Database for PostgreSQL, wenn Sie Azure Database Migration Service verwenden.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: db2f6e95153610e0a1b79cf5d30cea3c917333d3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962568"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen von PostgreSQL zu Azure DB for PostgreSQL

In den folgenden Abschnitten werden bekannte Probleme und Einschränkungen in Bezug auf Onlinemigrationen von PostgreSQL zu Azure Database for PostgreSQL beschrieben.

## <a name="online-migration-configuration"></a>Konfiguration der Onlinemigration

- Auf dem PostgreSQL-Quellserver muss Version 9.4, 9.5, 9.6, 10 oder 11 ausgeführt werden. Weitere Informationen finden Sie im Artikel [Unterstützte PostgreSQL-Datenbankversionen](../postgresql/concepts-supported-versions.md).
- Es werden nur Migrationen zur gleichen oder einer höheren Version unterstützt. Beispielsweise wird die Migration von PostgreSQL 9.5 zu Azure Database for PostgreSQL 9.6 oder 10 unterstützt, aber die Migration von PostgreSQL 11 zu PostgreSQL 9.6 nicht.
- Um die logische Replikation in der PostgreSQL-Quelldatei **postgresql.config** zu aktivieren, legen Sie die folgenden Parameter fest:
  - **wal_level** = logical
  - **max_replication_slots** = [mindestens maximale Anzahl von Datenbanken für die Migration]. Wenn Sie vier Datenbanken migrieren möchten, legen Sie den Wert auf mindestens 4 fest.
  - **max_wal_senders** = [Anzahl der gleichzeitig ausgeführten Datenbanken]. Der empfohlene Wert ist 10.
- Hinzufügen der IP-Adresse des DMS-Agents zur PostgreSQL-Quelldatei „pg_hba.conf“
  1. Notieren Sie sich die DMS-IP-Adresse, nachdem Sie die Bereitstellung einer Instanz von Azure Database Migration Service abgeschlossen haben.
  2. Fügen Sie die IP-Adresse der Datei „pg_hba.conf“ wie gezeigt hinzu:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- Der Benutzer muss auf dem Server, der die Datenbank hostet, über die Rolle REPLIKATION verfügen.
- Quell- und Zieldatenbankschema müssen übereinstimmen.
- Das Schema des betreffenden Azure Database for PostgreSQL-Einzelservers darf keine Fremdschlüssel enthalten. Verwenden Sie die folgende Abfrage, um Fremdschlüssel zu löschen:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Führen Sie „drop foreign key“ (zweite Spalte) im Abfrageergebnis aus.

- Das Schema des betreffenden Azure Database for PostgreSQL-Einzelservers darf keine Trigger enthalten. Führen Sie zum Deaktivieren von Triggern in der Zieldatenbank Folgendes aus:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="size-limitations"></a>Größenbeschränkungen
- Sie können mit einem einzigen DMS-Dienst bis zu 2 TB Daten von PostgreSQL zu Azure DB for PostgreSQL migrieren.
## <a name="datatype-limitations"></a>Einschränkungen bei Datentypen

  **Einschränkung**: Wenn kein Primärschlüssel für Tabellen vorhanden ist, werden Änderungen möglicherweise nicht mit der Zieldatenbank synchronisiert.

  **Problemumgehung**: Legen Sie vorübergehend einen Primärschlüssel für die Tabelle fest, damit die Migration fortgesetzt wird. Sie können den Primärschlüssel nach Abschluss der Datenmigration entfernen.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Einschränkungen bei der Onlinemigration von AWS RDS PostgreSQL

Beim Versuch der Onlinemigration von AWS RDS PostgreSQL zu Azure Database for PostgreSQL können die folgenden Fehler auftreten.

- **Fehler:** Der Standardwert der Spalte „{column}“ in der Tabelle „{table}“ der Datenbank „{database}“ unterscheidet sich für den Quell- und Zielserver. Auf der Quelle lautet er „{value on source}“, und auf dem Ziel „{value on target}“.

  **Einschränkung**: Dieser Fehler tritt auf, wenn der Standardwert für ein Spaltenschema in Quell- und Zieldatenbank unterschiedlich ist.
  **Problemumgehung**: Stellen Sie sicher, dass das Schema des Ziels mit dem Schema der Quelle übereinstimmt. Details zum Migrieren des Schemas finden Sie in der [Dokumentation zur Azure PostgreSQL-Onlinemigration](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Fehler:** Die Zieldatenbank „{database}“ enthält „{number of tables}“ Tabellen, während die Quelldatenbank „{database}“ „{number of tables}“ Tabellen enthält. Die Anzahl von Tabellen in der Quell- und Zieldatenbank sollte jeweils übereinstimmen.

  **Einschränkung**: Dieser Fehler tritt auf, wenn sich die Anzahl der Tabellen in Quell- und Zieldatenbank unterscheidet.

  **Problemumgehung**: Stellen Sie sicher, dass das Schema des Ziels mit dem Schema der Quelle übereinstimmt. Details zum Migrieren des Schemas finden Sie in der [Dokumentation zur Azure PostgreSQL-Onlinemigration](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

- **Fehler:** Die Quelldatenbank {database} ist leer.

  **Einschränkung**: Dieser Fehler tritt auf, wenn die Quelldatenbank leer ist. Meistens besteht die Ursache darin, dass Sie die falsche Datenbank als Quelle ausgewählt haben.

  **Problemumgehung**: Überprüfen Sie erneut die Quelldatenbank, die Sie für die Migration ausgewählt haben, und wiederholen Sie den Vorgang.

- **Fehler:** Die Zieldatenbank „{database}“ ist leer. Migrieren Sie das Schema.

  **Einschränkung**: Dieser Fehler tritt auf, wenn für die Zieldatenbank kein Schema vorhanden ist. Stellen Sie sicher, dass das Schema des Ziels mit dem Schema der Quelle übereinstimmt.
  **Problemumgehung**: Stellen Sie sicher, dass das Schema des Ziels mit dem Schema der Quelle übereinstimmt. Details zum Migrieren des Schemas finden Sie in der [Dokumentation zur Azure PostgreSQL-Onlinemigration](./tutorial-postgresql-azure-postgresql-online.md#migrate-the-sample-schema).

## <a name="other-limitations"></a>Weitere Einschränkungen

- Der Name der Datenbank darf kein Semikolon (;) enthalten.
- Eine erfasste Tabelle muss über einen Primärschlüssel verfügen. Wenn eine Tabelle keinen Primärschlüssel besitzt, ist das Ergebnis der Vorgänge DELETE- und UPDATE für Datensätze unvorhersehbar.
- Die Aktualisierung eines Primärschlüsselsegments wird ignoriert. In solchen Fällen wird das Anwenden einer solchen Aktualisierung vom Ziel als eine Aktualisierung identifiziert, die keine Zeilen aktualisiert hat und zu einem Datensatz führt, der in die Ausnahmetabelle geschrieben wird.
- Die Migration mehrerer Tabellen mit gleichem Namen, aber unterschiedlicher Groß- und Kleinschreibung (z.B. table1, TABLE1 und Table1) kann zu unvorhersehbarem Verhalten führen und wird daher nicht unterstützt.
- Änderungsverarbeitung von [CREATE | ALTER | DROP | TRUNCATE]-Tabellen-DDLs wird nicht unterstützt.
- In Azure Database Migration Service kann eine einzelne Migrationsaktivität nur bis zu vier Datenbanken aufnehmen.