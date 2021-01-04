---
title: 'Tutorial: Onlinemigration von Azure DB for PostgreSQL zu Azure DB for PostgreSQL über das Azure-Portal'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service über das Azure-Portal eine Onlinemigration von einer Azure DB for PostgreSQL-Instanz zu einer anderen durchführen.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 07/21/2020
ms.openlocfilehash: 772fc9f21f36a1487e2e3a1acbe644f9fd12e0f4
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563193"
---
# <a name="tutorial-migrateupgrade-azure-db-for-postgresql---single-server-to-azure-db-for-postgresql---single-server--online-using-dms-via-the-azure-portal"></a>Tutorial: Onlinemigration/Onlineupgrade von einer Azure Database for PostgreSQL – Einzelserver-Instanz zu einer anderen Azure Database for PostgreSQL – Einzelserver-Instanz mithilfe von DMS über das Azure-Portal

Sie können Azure Database Migration Service nutzen, um die Datenbanken mit minimaler Downtime aus einer [Azure Database for PostgreSQL – Einzelserver](../postgresql/overview.md#azure-database-for-postgresql---single-server)-Instanz in dieselbe oder eine andere Version einer Azure Database for PostgreSQL – Einzelserver-Instanz oder Azure Database for PostgreSQL – Flexible Server-Instanz zu migrieren. In diesem Tutorial migrieren Sie die Beispieldatenbank **DVD Rental** von Azure Database for PostgreSQL v10 zu Azure Database for PostgreSQL – Single Server. Zu diesem Zweck verwenden Sie die Onlinemigrationsaktivität in Azure Database Migration Service.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Migrieren des Beispielschemas mit dem Hilfsprogramm „pg_dump“
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts in Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration
> * Durchführen der Migrationsübernahme

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs. Wir verschlüsseln den Datenträger, um Datendiebstahl während der Migration zu verhindern.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

> [!IMPORTANT]
> Die Migration von Azure Database for PostgreSQL wird für PostgreSQL Version 10 und höher unterstützt. Sie können mit diesem Tutorial auch von einer Azure Database for PostgreSQL-Instanz zu einer anderen Azure Database for PostgreSQL-Instanz oder einer Hyperscale-Instanz (Citus) migrieren.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Informationen zu unterstützten Migrations- und Versionskombinationen finden Sie unter [Status von Migrationsszenarien, die in Azure Database Migration Service unterstützt werden](./resource-scenario-status.md). 
* Eine vorhandene [Azure Database for PostgreSQL](../postgresql/index.yml)-Instanz der Version 10 und höher mit der **DVD Rental**-Datenbank. 

    Beachten Sie auch, dass die Azure Database for PostgreSQL-Zielversion höher als die lokale PostgreSQL-Version sein oder dieser entsprechen muss. Beispiel: PostgreSQL 10 kann zu Azure Database for PostgreSQL 10 oder 11 migriert werden, nicht aber zu Azure Database for PostgreSQL 9.6.

* [Erstellen Sie einen Azure Database for PostgreSQL-Server](../postgresql/quickstart-create-server-database-portal.md), oder [erstellen Sie einen Azure Database for PostgreSQL-Server – Hyperscale (Citus)](../postgresql/quickstart-create-hyperscale-portal.md) als Zieldatenbankserver, um Daten dorthin zu migrieren.
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

* Stellen Sie sicher, dass die Regeln für Netzwerksicherheitsgruppen für Ihr virtuelles Netzwerk nicht die folgenden Ports für die eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Erstellen Sie für die Azure Database for PostgreSQL-Quelle eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
* Erstellen Sie für das Azure Database for PostgreSQL-Ziel eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
* [Aktivieren Sie die logische Replikation](../postgresql/concepts-logical.md) in der Azure DB for PostgreSQL-Quelle. 
* Legen Sie die folgenden Serverparameter in der Azure Database for PostgreSQL-Instanz fest, die als Quelle verwendet wird:

  * max_replication_slots = [Anzahl der Slots], empfohlene Einstellung: **zehn Slots**
  * max_wal_senders = [Anzahl gleichzeitiger Aufgaben] – Der max_wal_senders-Parameter legt die Anzahl von Aufgaben fest, die gleichzeitig ausgeführt werden können; empfohlene Einstellung: **10 Aufgaben**

> [!NOTE]
> Die oben genannten Serverparameter sind statisch und erfordern einen Neustart Ihrer Azure Database for PostgreSQL-Instanz, damit sie wirksam werden. Weitere Informationen zum Umschalten von Serverparametern finden Sie unter [Konfigurieren von Serverparametern in Azure Database for PostgreSQL (Einzelserver) über das Azure-Portal](../postgresql/howto-configure-server-parameters-using-portal.md).

> [!IMPORTANT]
> Alle Tabellen in Ihrer vorhandenen Datenbank benötigen einen Primärschlüssel, um sicherzustellen, dass Änderungen mit der Zieldatenbank synchronisiert werden können.

## <a name="migrate-the-sample-schema"></a>Migrieren des Beispielschemas

Zum Fertigstellen aller Datenbankobjekte wie Tabellenschemas, Indizes und gespeicherter Prozeduren muss das Schema aus der Quelldatenbank extrahiert und auf die Datenbank angewendet werden.

1. Verwenden Sie den Befehl „pg_dump -s“, um eine Schemasicherungsdatei für eine Datenbank zu erstellen.

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Um beispielsweise eine Schemasicherungsdatei für die Datenbank **dvdrental** zu erstellen, gehen Sie folgendermaßen vor:

    ```
    pg_dump -o -h mypgserver-source.postgres.database.azure.com -U pguser@mypgserver-source -d dvdrental -s -O -x > dvdrentalSchema.sql
    ```

    Weitere Informationen zur Verwendung des Dienstprogramms „pg_dump“ finden Sie in den Beispielen des [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES)-Tutorials.

2. Erstellen Sie eine leere Datenbank in der Zielumgebung, Azure Database for PostgreSQL.

    Ausführliche Anleitungen zum Erstellen und Verbinden einer Datenbank finden Sie unter [Erstellen eines Azure Database for PostgreSQL-Servers im Azure-Portal](../postgresql/quickstart-create-server-database-portal.md) und unter [Erstellen eines Azure Database for PostgreSQL-Servers – Hyperscale (Citus) über das Azure-Portal](../postgresql/quickstart-create-hyperscale-portal.md).

    > [!NOTE]
    > Eine Instanz von Azure Database for PostgreSQL – Hyperscale (Citus) besitzt nur eine einzige Datenbank: **citus**.

3. Importieren Sie das Schema in die Zieldatenbank, die Sie erstellt haben, indem Sie die Schemasicherungsdatei wiederherstellen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql
    ```

    Beispiel:

    ```
    psql -h mypgserver-source.postgres.database.azure.com  -U pguser@mypgserver-source -d dvdrental citus < dvdrentalSchema.sql
    ```

4. Um das Skript zum Löschen von Fremdschlüsseln zu extrahieren und im Ziel (Azure Database for PostgreSQL) hinzuzufügen, führen Sie das folgende Skript in PgAdmin oder psql aus.

   > [!IMPORTANT]
   > Fremdschlüssel in Ihrem Schema verursachen Fehler beim ersten Ladevorgang und bei der fortlaufenden Synchronisierung der Migration.

    ```
    SELECT Q.table_name
        ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name ,'"', STRING_AGG(DISTINCT CONCAT(' DROP CONSTRAINT ','"', foreignkey,'"'), ','), ';') as DropQuery
            ,CONCAT('ALTER TABLE ','"', table_schema,'"', '.','"', table_name,'"', STRING_AGG(DISTINCT CONCAT(' ADD CONSTRAINT ','"', foreignkey,'"', ' FOREIGN KEY (','"', column_name,'"', ')', ' REFERENCES ','"', foreign_table_schema,'"', '.','"', foreign_table_name,'"', '(','"', foreign_column_name,'"', ')',' ON UPDATE ',update_rule,' ON DELETE ',delete_rule), ','), ';') as AddQuery
    FROM
        (SELECT
        S.table_schema,
        S.foreignkey,
        S.table_name,
        STRING_AGG(DISTINCT S.column_name, ',') AS column_name,
        S.foreign_table_schema,
        S.foreign_table_name,
        STRING_AGG(DISTINCT S.foreign_column_name, ',') AS foreign_column_name,
        S.update_rule,
        S.delete_rule
    FROM
        (SELECT DISTINCT
        tc.table_schema,
        tc.constraint_name AS foreignkey,
        tc.table_name,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name,
        rc.update_rule AS update_rule,
        rc.delete_rule AS delete_rule
        FROM information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu ON tc.constraint_name = kcu.constraint_name AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu ON ccu.constraint_name = tc.constraint_name AND ccu.table_schema = tc.table_schema
        JOIN information_schema.referential_constraints as rc ON rc.constraint_name = tc.constraint_name AND rc.constraint_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY'
        ) S
        GROUP BY S.table_schema, S.foreignkey, S.table_name, S.foreign_table_schema, S.foreign_table_name,S.update_rule,S.delete_rule
        ) Q
        GROUP BY Q.table_schema, Q.table_name;
    ```

5. Führen Sie „drop foreign key“ (zweite Spalte) im Abfrageergebnis aus.

6. Um Trigger in der Zieldatenbank zu deaktivieren, führen Sie das folgende Skript aus.

   > [!IMPORTANT]
   > Trigger („insert“ oder „update“) in den Daten erzwingen Datenintegrität am Ziel, bevor die Daten aus der Quelle repliziert werden. Daher empfiehlt es sich, Trigger während der Migration in allen Tabellen **im Ziel** zu deaktivieren und nach Abschluss der Migration wieder zu aktivieren.

    ```
    SELECT DISTINCT CONCAT('ALTER TABLE ', event_object_schema, '.', event_object_table, ' DISABLE TRIGGER ', trigger_name, ';')
    FROM information_schema.triggers
    ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-subscriptions.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Erstellen einer DMS-Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen, das Abonnement, eine neue oder vorhandene Ressourcengruppe und den Speicherort für den Dienst an.

4. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk bietet Azure Database Migration Service Zugriff auf den PostgreSQL-Quellserver und die Azure Database for PostgreSQL-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

5. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-settings4.png)

6. Klicken Sie zum Erstellen des Diensts auf **Überprüfen + erstellen**.

   Die Diensterstellung dauert etwa 10 bis 15 Minuten.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-search.png)

2. Suchen Sie auf dem Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, wählen Sie die Instanz aus, und klicken Sie dann auf **Neues Migrationsprojekt**.

3. Geben Sie im Bereich **Neues Migrationsprojekt** einen Namen für das Projekt an, wählen Sie im Textfeld **Typ des Quellservers** den Eintrag **PostgreSQL** aus, und wählen Sie im Textfeld **Typ des Zielservers** den Eintrag **Azure Database for PostgreSQL** aus.
    > [!NOTE]
    > Wählen Sie **PostgreSQL** in **Typ des Quellservers** aus, obwohl der Quellserver eine **Azure Database for PostgreSQL**-Instanz ist.  

4. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

    ![Erstellen eines Azure Database Migration Service-Projekts](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-create-project.png)

    > [!NOTE]
    > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

5. Wählen Sie **Speichern** aus, beachten Sie die Anforderungen für die erfolgreiche Verwendung von Azure Database Migration Service zum Migrieren von Daten, und wählen Sie dann **Aktivität erstellen und ausführen** aus.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie auf dem Bildschirm **Quelldetails hinzufügen** die Verbindungsinformationen für die PostgreSQL-Quellinstanz an.

    ![Bildschirm „Quelldetails hinzufügen“](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-source-details.png)

    > [!NOTE]
    > Details wie „Servername“, „ServerPort“, „Datenbankname“ usw. finden Sie im **Azure Database for PostgreSQL**-Portal.

2. Wählen Sie **Speichern** aus.

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie auf dem Bildschirm **Zieldetails** die Verbindungsinformationen für den Hyperscale (Citus)-Zielserver an. Dabei handelt es sich um die vorab bereitgestellte Instanz von Hyperscale (Citus), auf der das Schema **DVD Rentals** über „pg_dump“ bereitgestellt wurde.

    ![Bildschirm „Zieldetails“](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-add-target-details.png)

    > [!NOTE]
    > Sie können von einer Azure Database for PostgreSQL-Instanz zu einer anderen Azure Database for PostgreSQL-Einzelserverinstanz oder einem Hyperscale-Server (Citus) migrieren.

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Bildschirm „Den Zieldatenbanken zuordnen“](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-map-target-databases.png)

3. Klicken Sie auf **Speichern**, und übernehmen Sie auf dem Bildschirm **Migrationseinstellungen** die Standardwerte.

    ![Bildschirm „Migrationseinstellungen“](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-settings.png)

4. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Bildschirm „Migrationszusammenfassung“](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität sollte zu **Sicherung wird durchgeführt** aktualisiert werden. Beim Durchführen eines Upgrades von Azure DB for PostgreSQL 9.5 oder 9.6 tritt ggf. ein Fehler der folgenden Art auf:

**Für ein Szenario wurde ein unbekannter Fehler gemeldet. 28000: Kein „pg_hba.conf“-Eintrag für Replikationsverbindung vom Host „40.121.141.121“, Benutzer „sr“**

Der Grund hierfür ist, dass PostgreSQL nicht über die benötigten Berechtigungen für die Erstellung der erforderlichen Artefakte für die logische Replikation verfügt. Zum Aktivieren der erforderlichen Berechtigungen können Sie wie folgt vorgehen:

1. Öffnen Sie die Einstellungen unter „Verbindungssicherheit“ für den Azure DB for PostgreSQL-Quellserver, von dem aus Sie die Migration bzw. das Upgrade durchführen möchten.
2. Fügen Sie eine neue Firewallregel mit einem Namen hinzu, der auf „_replrule“ endet, und fügen Sie die IP-Adresse aus der Fehlermeldung in die Felder für die Start- und End-IP-Adresse ein. Für das obige Fehlerbeispiel:
> Name der Firewallregel = sr_replrule, Start-IP-Adresse = 40.121.141.121, End-IP-Adresse = 40.121.141.121

3. Klicken Sie auf „Speichern“, und warten Sie ab, bis der Änderungsvorgang abgeschlossen ist. 
4. Versuchen Sie erneut, die DMS-Aktivität durchzuführen. 

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Abgeschlossen** lautet.

     ![Überwachen des Migrationsprozesses](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-monitor-migration.png)

2. Wenn die Migration abgeschlossen ist, wählen Sie unter **Datenbankname** eine bestimmte Datenbank aus, um den Migrationsstatus für die Vorgänge **Vollständiger Datenladevorgang** und **Inkrementelle Datensynchronisierung** abzurufen.

   > [!NOTE]
   > Unter **Vollständiger Datenladevorgang** wird der Migrationsstatus des ersten Ladevorgangs und unter **Inkrementelle Datensynchronisierung** der CDC-Status (Change Data Capture) angezeigt.

     ![Details zum vollständigen Datenladevorgang](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-full-data-load-details.png)

     ![Details zur inkrementellen Datensynchronisierung](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-incremental-data-sync-details.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

2. Warten Sie, bis der Zähler **Ausstehende Änderungen** den Wert **0** anzeigt, um sicherzustellen, dass alle eingehenden Transaktionen für die Datenbank angehalten werden, aktivieren Sie das Kontrollkästchen **Bestätigen**, und klicken Sie auf **Anwenden**.

    ![Bildschirm „Umstellung abschließen“](media/tutorial-azure-postgresql-to-azure-postgresql-online-portal/dms-complete-cutover.png)

3. Wenn der Status der Datenbankmigration als **Abgeschlossen** angezeigt wird, [erstellen Sie Sequenzen neu](https://wiki.postgresql.org/wiki/Fixing_Sequences) (sofern erforderlich), und stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Azure Database for PostgreSQL-Zielinstanz her.
 
> [!NOTE]
> Azure Database Migration Service kann verwendet werden, um Upgrades der Hauptversion mit verringerter Downtime in Azure Database for PostgreSQL – Einzelserver-Instanzen durchzuführen. Zuerst konfigurieren Sie eine Zieldatenbank mit der gewünschten höheren PostgreSQL-Version sowie entsprechenden Netzwerkeinstellungen und Parametern. Dann initiieren Sie die Migration in die Zieldatenbanken mithilfe der oben erläuterten Prozedur. Nachdem Sie die Umstellung auf den Zieldatenbankserver durchgeführt haben, können Sie Ihre Anwendungsverbindungszeichenfolge so aktualisieren, das sie auf den Zieldatenbankserver verweist. 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu bekannten Problemen und Einschränkungen beim Ausführen der Onlinemigration zu Azure Database for PostgreSQL finden Sie im Artikel [Bekannte Probleme/Migrationseinschränkungen bei der Onlinemigration zu Azure Database for PostgreSQL](known-issues-azure-postgresql-online.md).
* Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
* Weitere Informationen zu Azure Database for PostgreSQL finden Sie im Artikel [Was ist Azure Database for PostgreSQL?](../postgresql/overview.md).
