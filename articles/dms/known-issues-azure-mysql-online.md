---
title: 'Bekannte Probleme: Onlinemigrationen zu Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie mehr zu bekannten Problemen und Migrationseinschränkungen bei Onlinemigrationen zu Azure Database for MySQL, wenn Sie Azure Database Migration Service verwenden.
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
ms.openlocfilehash: a9ac4830d11aa3360a272ac1feb167eb20c26c9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009805"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Onlinemigrationsprobleme und Einschränkungen bei Azure DB for MySQL mit Azure Database Migration Service

In den folgenden Abschnitten werden bekannte Probleme und Einschränkungen in Bezug auf Onlinemigrationen von MySQL zu Azure Database for MySQL beschrieben.

## <a name="online-migration-configuration"></a>Konfiguration der Onlinemigration


- Die MySQL-Version des Quellservers muss 5.6.35, 5.7.18 oder höher sein.
- Azure Database for MySQL unterstützt Folgendes:
  - MySQL Community-Edition
  - InnoDB-Engine
- Migration zu derselben Version. Die Migration von MySQL 5.6 zu Azure Database for MySQL 5.7 wird nicht unterstützt. Eine Migration zu oder von MySQL 8.0 wird nicht unterstützt.
- Aktivieren Sie die binäre Protokollierung in „my.ini“ (Windows) oder „my.cnf“ (Unix).
  - Legen Sie „Server_id“ auf eine beliebige Zahl größer oder gleich 1 fest, z.B. Server_id=1 (nur für MySQL 5.6).
  - Legen Sie „log-bin = \<path>“ fest (nur für MySQL 5.6).
  - Legen Sie „binlog_format = row“ fest.
  - „Expire_logs_days = 5“ (empfohlen – nur für MySQL 5.6)
- Der Benutzer muss die Rolle „ReplicationAdmin“ besitzen.
- Die für die MySQL-Quelldatenbank definierten Sortierungen sind identisch mit den in der Azure Database for MySQL-Zielinstanz definierten Sortierungen.
- Das Schema muss zwischen der MySQL-Quelldatenbank und der Zieldatenbank in Azure Database for MySQL übereinstimmen.
- Das Schema in der Azure Database for MySQL-Zielinstanz darf keine Fremdschlüssel aufweisen. Verwenden Sie die folgende Abfrage, um Fremdschlüssel zu löschen:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Führen Sie „drop foreign key“ (zweite Spalte) im Abfrageergebnis aus.
- Das Schema in der Azure Database for MySQL-Zielinstanz darf keine Trigger aufweisen. So löschen Sie Trigger in der Zieldatenbank
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Einschränkungen bei Datentypen

- **Einschränkung**: Wenn die MySQL-Quelldatenbank einen JSON-Datentyp enthält, tritt bei der Migration während der fortlaufenden Synchronisierung ein Fehler auf.

    **Problemumgehung**: Ändern Sie den JSON-Datentyp in der MySQL-Quelldatenbank in „mediumtext“ oder „longtext“.

- **Einschränkung**: Wenn in Tabellen kein Primärschlüssel vorhanden ist, tritt bei der fortlaufenden Synchronisierung ein Fehler auf.

    **Problemumgehung**: Legen Sie vorübergehend einen Primärschlüssel für die Tabelle fest, damit die Migration fortgesetzt wird. Sie können den Primärschlüssel nach Abschluss der Datenmigration entfernen.

## <a name="lob-limitations"></a>LOB-Einschränkungen

LOB-Spalten (Large Object) sind Spalten, die groß werden können. Bei MySQL sind „Mediumtext“, „Longtext“, „Blob“, „Mediumblob“, „Longblob“ usw. einige der LOB-Datentypen.

- **Einschränkung**: Wenn LOB-Datentypen als Primärschlüssel verwendet werden, tritt bei der Migration ein Fehler auf.

    **Problemumgehung**: Ersetzen Sie den Primärschlüssel durch andere Datentypen oder Spalten, die keine LOB-Datentypen oder LOB-Spalten sind.

- **Einschränkung**: Wenn die Länge der LOB-Spalte (Large Object) den Parameter „LOB-Größe beschränken“ überschreitet (sollte nicht größer als 64 KB sein), werden die Daten möglicherweise am Ziel abgeschnitten. Mithilfe der folgenden Abfrage können Sie die Länge der LOB-Spalte überprüfen:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Problemumgehung**: Wenn Sie über ein LOB-Objekt verfügen, das größer als 64 KB ist, verwenden Sie den Parameter „Unbegrenzte LOB-Größe zulassen“. Beachten Sie, dass Migrationen mit dem Parameter „Unbegrenzte LOB-Größe zulassen“ langsamer als Migrationen mit dem Parameter „LOB-Größe beschränken“ sind.

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Einschränkungen bei der Onlinemigration von AWS RDS MySQL

Beim Versuch der Onlinemigration von AWS RDS MySQL zu Azure Database for MySQL können die folgenden Fehler auftreten.

- **Fehler:** Datenbank „{0}“ verfügt auf dem Ziel über Fremdschlüssel. Beheben Sie den Fehler für das Ziel, und starten Sie eine neue Aktivität für die Datenmigration. Führen Sie das unten angegebene Skript auf dem Ziel aus, um die Fremdschlüssel aufzulisten.

  **Einschränkung**: Enthält das Schema Fremdschlüssel, tritt beim ersten Ladevorgang und bei der fortlaufenden Synchronisierung der Migration ein Fehler auf.
  **Problemumgehung**: Führen Sie das folgende Skript in MySQL Workbench aus, um das Skript zum Löschen des Fremdschlüssels und das Skript zum Hinzufügen des Fremdschlüssels zu extrahieren:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Fehler:** Die Datenbank „{0}“ ist auf dem Server nicht vorhanden. Für den angegebenen MySQL-Quellserver wird die Groß-/Kleinschreibung beachtet. Überprüfen Sie den Datenbanknamen.

  **Einschränkung**: Für Benutzer tritt dieser Fehler ggf. auf, wenn sie eine MySQL-Datenbank über die Befehlszeilenschnittstelle (CLI) zu Azure migrieren. Der Dienst konnte die Datenbank auf dem Quellserver nicht finden. Möglicherweise wurde ein falscher Datenbankname angegeben, oder die Datenbank ist auf dem angegebenen Server nicht vorhanden. Beachten Sie, dass bei Datenbanknamen die Groß-/Kleinschreibung berücksichtigt wird.

  **Problemumgehung**: Geben Sie den genauen Datenbanknamen an, und wiederholen Sie den Vorgang.

- **Fehler:** Die Datenbank „{database}“ enthält Tabellen mit demselben Namen. Azure Database for MySQL unterscheidet bei Tabellennamen keine Groß-/Kleinschreibung.

  **Einschränkung**: Dieser Fehler tritt auf, wenn in der Quelldatenbank zwei Tabellen den gleichen Namen haben. Azure Database for MySQL unterscheidet bei Tabellennamen keine Groß-/Kleinschreibung.

  **Problemumgehung**: Aktualisieren Sie die Tabellennamen, sodass sie eindeutig sind, und wiederholen Sie dann den Vorgang.

- **Fehler:** Die Zieldatenbank „{database}“ ist leer. Migrieren Sie das Schema.

  **Einschränkung**: Dieser Fehler tritt auf, wenn die Azure Database for MySQL-Zieldatenbank nicht über das erforderliche Schema verfügt. Die Schemamigration ist erforderlich, um Daten zu Ihrem Ziel migrieren zu können.

  **Problemumgehung**: [Migrieren Sie das Schema](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema) von Ihrer Quell- zur Zieldatenbank.

## <a name="other-limitations"></a>Weitere Einschränkungen

- Kennwortzeichenfolgen mit öffnender und schließender geschweifter Klammer {  } am Anfang und Ende der Kennwortzeichenfolge werden nicht unterstützt. Diese Einschränkung gilt für Verbindungen mit der MySQL-Quelldatenbank sowie der Azure Database for MySQL-Zielinstanz.
- Die folgenden DDLs werden nicht unterstützt:
  - Alle DDLs von Partitionen
  - Löschen einer Tabelle
  - Umbenennen von Tabellen
- Die Verwendung der Anweisung *alter table <Tabellenname> add column <Spaltenname>* zum Hinzufügen von Spalten am Anfang oder in der Mitte einer Tabelle wird nicht unterstützt. Mit der Anweisung *alter table <Tabellenname> add column <Spaltenname>* wird eine Spalte am Ende der Tabelle hinzugefügt.
- Indizes, die nur für einen Teil der Spaltendaten erstellt werden, werden nicht unterstützt. Mit der folgenden Beispielanweisung wird ein Index mit nur einem Teil der Spaltendaten erstellt:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- In Azure Database Migration Service ist die Anzahl der in einer einzelnen Migrationsaktivität zu migrierenden Datenbanken auf vier Datenbanken beschränkt.

- Azure DMS unterstützt die referenzielle CASCADE-Aktion nicht, die verwendet werden kann, um eine übereinstimmende Zeile in der untergeordneten Tabelle automatisch zu löschen oder zu aktualisieren, wenn eine Zeile in der übergeordneten Tabelle gelöscht oder aktualisiert wird. Weitere Informationen finden Sie in der MySQL-Dokumentation im Abschnitt „Referential Actions“ (referenzielle Aktionen) des Artikels [FOREIGN KEY Constraints](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html). Azure DMS erfordert, dass Sie Fremdschlüsseleinschränkungen auf dem Zieldatenbankserver während des anfänglichen Ladens von Daten löschen. Zudem können Sie keine referenziellen Aktionen verwenden. Wenn Ihre Workload von der Aktualisierung einer zugehörigen untergeordneten Tabelle über diese referenzielle Aktion abhängt, empfiehlt es sich, stattdessen eine [Sicherung und Wiederherstellung](../mysql/concepts-migrate-dump-restore.md) durchzuführen. 

- **Fehler:** Zeile zu groß (> 8126). Das Ändern einiger Spalten in TEXT oder BLOB kann helfen. Beim aktuellen Zeilenformat wird das BLOB-Präfix von 0 Byte inline gespeichert.

  **Einschränkung**: Dieser Fehler tritt auf, wenn Sie mithilfe der InnoDB-Speicher-Engine zu Azure Database for MySQL migrieren und eine Tabellenzeilen zu groß ist (> 8126 Bytes).

  **Problemumgehung**: Aktualisieren Sie das Schema der Tabelle, die eine Zeilengröße von mehr als 8126 Bytes aufweist. Es wird nicht empfohlen, den Strict-Modus zu ändern, da die Daten abgeschnitten werden. Das Ändern von „page_size“ wird nicht unterstützt.