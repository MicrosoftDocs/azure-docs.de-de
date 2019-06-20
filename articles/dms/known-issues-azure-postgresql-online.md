---
title: Artikel zu bekannten Problemen/Migrationseinschränkungen bei Onlinemigrationen zu Azure Database for MySQL | Microsoft-Dokumentation
description: Informationen zu bekannten Problemen/Migrationseinschränkungen bei Onlinemigrationen zu Azure Database for MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/23/2019
ms.openlocfilehash: 2c8a3f36e04fbedfdd127939d55fab376e3e6b30
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "64691948"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen zu Azure DB for PostgreSQL

In den folgenden Abschnitten werden bekannte Probleme und Einschränkungen in Bezug auf Onlinemigrationen von PostgreSQL zu Azure Database for PostgreSQL beschrieben. 

## <a name="online-migration-configuration"></a>Konfiguration der Onlinemigration
- Der PostgreSQL-Quellserver muss Version 9.5.11, 9.6.7, 10.3 oder höher ausführen. Weitere Informationen finden Sie im Artikel [Unterstützte PostgreSQL-Datenbankversionen](../postgresql/concepts-supported-versions.md).
- Es werden nur Migrationen innerhalb einer Version unterstützt. Das Migrieren von PostgreSQL 9.5.11 zu Azure Database for PostgreSQL 9.6.7 wird z.B. nicht unterstützt.

    > [!NOTE]
    > Für PostgreSQL Version 10 unterstützt DMS zurzeit nur die Migration von Version 10.3 zu Azure Database for PostgreSQL. Wir planen, neuere Versionen von PostgreSQL schon bald zu unterstützen.

- Um die logische Replikation in der PostgreSQL-Quelldatei **postgresql.config** zu aktivieren, legen Sie die folgenden Parameter fest:
    - **wal_level** = logical
    - **max_replication_slots** = [maximale Anzahl von Datenbanken für die Migration]. Wenn Sie 4 Datenbanken migrieren möchten, legen Sie den Wert auf 4 fest.
    - **max_wal_senders** = [Anzahl der gleichzeitig ausgeführten Datenbanken]. Der empfohlene Wert ist 10.
- Hinzufügen der IP-Adresse des DMS-Agents zur PostgreSQL-Quelldatei „pg_hba.conf“
    1. Notieren Sie sich die DMS-IP-Adresse, nachdem Sie die Bereitstellung einer Instanz von DMS abgeschlossen haben.
    2. Fügen Sie die IP-Adresse der Datei „pg_hba.conf“ wie gezeigt hinzu:

        host    all     172.16.136.18/10    md5  host    replication postgres    172.16.136.18/10    md5

- Der Benutzer muss über Administratorberechtigung auf dem Server verfügen, der die Datenbank hostet.
- Abgesehen davon, dass ENUM im Quelldatenbankschema enthalten ist, müssen das Quell- und das Zieldatenbankschema übereinstimmen.
- Das Schema in der Azure for PostgreSQL-Zieldatenbank darf keine Fremdschlüssel enthalten. Verwenden Sie die folgende Abfrage, um Fremdschlüssel zu löschen:

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

- Das Schema in der Azure for PostgreSQL-Zieldatenbank darf keine Trigger enthalten. Führen Sie zum Deaktivieren von Triggern in der Zieldatenbank Folgendes aus:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Einschränkungen bei Datentypen

- **Einschränkung**: Wenn die PostgreSQL-Quelldatenbank einen ENUM-Datentyp enthält, treten bei der Migration während der fortlaufenden Synchronisierung Fehler auf.

    **Problemumgehung**: Ändern Sie den ENUM-Datentyp in Azure Database for PostgreSQL in „character varying“.

- **Einschränkung**: Wenn in Tabellen kein Primärschlüssel vorhanden ist, tritt bei der fortlaufenden Synchronisierung ein Fehler auf.

    **Problemumgehung**: Legen Sie vorübergehend einen Primärschlüssel für die Tabelle fest, damit die Migration fortgesetzt wird. Sie können den Primärschlüssel nach Abschluss der Datenmigration entfernen.

## <a name="lob-limitations"></a>LOB-Einschränkungen
LOB-Spalten (Large Object) sind Spalten, die groß werden können. Für PostgreSQL sind XML, JSON, IMAGE, TEXT usw. Beispiele für LOB-Datentypen.

- **Einschränkung**: Wenn LOB-Datentypen als Primärschlüssel verwendet werden, tritt bei der Migration ein Fehler auf.

    **Problemumgehung**: Ersetzen Sie den Primärschlüssel durch andere Datentypen oder Spalten, die keine LOB-Datentypen oder LOB-Spalten sind.

- **Einschränkung**: Wenn die Länge der LOB-Spalte (Large Object) 32 KB überschreitet, werden die Daten möglicherweise am Ziel abgeschnitten. Mithilfe der folgenden Abfrage können Sie die Länge der LOB-Spalte überprüfen:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Problemumgehung**: Wenden Sie sich bei einem LOB-Objekt, das größer als 32 KB ist, unter [Fragen zur Azure-Datenbankmigration](mailto:AskAzureDatabaseMigrations@service.microsoft.com) an das Entwicklerteam.

- **Einschränkung**: Wenn LOB-Spalten in der Tabelle vorhanden sind und kein Primärschlüssel für die Tabelle festgelegt wurde, werden möglicherweise keine Daten für diese Tabelle migriert.

    **Problemumgehung**: Legen Sie vorübergehend einen Primärschlüssel für die Tabelle fest, damit die Migration fortgesetzt wird. Sie können den Primärschlüssel nach Abschluss der Datenmigration entfernen.

## <a name="postgresql10-workaround"></a>PostgreSQL10-Problemumgehung
PostgreSQL 10.x nimmt verschiedene Änderungen an pg_xlog-Ordnernamen vor und bewirkt damit, dass die Migration nicht wie erwartet ausgeführt wird. Wenn Sie von PostgreSQL 10.x zu Azure Database for PostgreSQL 10.3 migrieren, führen Sie das folgende Skript für die PostgreSQL-Quelldatenbank aus, um eine Wrapper-Funktion um pg_xlog-Funktionen zu erstellen.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>Weitere Einschränkungen
- Der Name der Datenbank darf kein Semikolon (;) enthalten.
- Eine Kennwortzeichenfolge mit öffnenden und schließenden geschweiften Klammern { } wird nicht unterstützt. Diese Einschränkung gilt für Verbindungen mit der PostgreSQL-Quelldatenbank sowie der Azure Database for PostgreSQL-Zielinstanz.
- Eine erfasste Tabelle muss über einen Primärschlüssel verfügen. Wenn eine Tabelle keinen Primärschlüssel besitzt, ist das Ergebnis der Vorgänge DELETE- und UPDATE für Datensätze unvorhersehbar.
- Die Aktualisierung eines Primärschlüsselsegments wird ignoriert. In solchen Fällen wird das Anwenden einer solchen Aktualisierung vom Ziel als eine Aktualisierung identifiziert, die keine Zeilen aktualisiert hat und zu einem Datensatz führt, der in die Ausnahmetabelle geschrieben wird.
- Die Migration mehrerer Tabellen mit gleichem Namen, aber unterschiedlicher Groß- und Kleinschreibung (z.B. table1, TABLE1 und Table1) kann zu unvorhersehbarem Verhalten führen und wird daher nicht unterstützt.
- Die Änderungsverarbeitung von [CREATE | ALTER | DROP] für Tabellen-DDLs wird unterstützt, es sei denn, sie sind in einem inneren Funktions-/Prozedurentextblock oder in anderen geschachtelten Konstrukten enthalten. Beispielsweise wird die folgende Änderung nicht erfasst:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Die Änderungsverarbeitung (fortlaufende Synchronisierung) von TRUNCATE-Vorgängen wird nicht unterstützt. Die Migration partitionierter Tabellen wird nicht unterstützt. Wenn eine partitionierte Tabelle erkannt wird, werden die folgenden Aktionen ausgeführt:
    - Die Datenbank meldet eine Liste von übergeordneten und untergeordneten Tabellen.
    - Die Tabelle wird im Ziel als reguläre Tabelle mit den gleichen Eigenschaften wie die ausgewählten Tabellen erstellt.
    - Wenn die übergeordnete Tabelle in der Quelldatenbank den gleichen Primärschlüsselwert wie die untergeordneten Tabellen aufweist, wird ein Fehler des Typs „doppelt vorhandener Schlüssel“ generiert.
- In DMS ist die Anzahl der in einer einzelnen Migrationsaktivität zu migrierenden Datenbanken auf vier Datenbanken beschränkt.