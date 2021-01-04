---
title: 'Tutorial: Onlinemigration von RDS MySQL zu Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von RDS MySQL zu Azure Database for MySQL durchführen.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 06/09/2020
ms.openlocfilehash: b8d5c763b68a9f69add14ab8430c117e5705a515
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955088"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Ausführen einer Onlinemigration von RDS MySQL zu Azure Database for MySQL mithilfe von DMS

Sie können Azure Database Migration Service verwenden, um Datenbanken von einer RDS MySQL-Instanz zu [Azure Database for MySQL](../mysql/index.yml) zu migrieren, während die Quelldatenbank während der Migration online bleibt. Somit kommt es bei der Migration nur zu einer geringen Ausfallzeit für die Anwendung. In diesem Tutorial migrieren Sie die Beispieldatenbank **Employees** von einer lokalen Instanz von RDS MySQL zu Azure Database for MySQL. Zu diesem Zweck verwenden Sie eine Onlinemigrationsaktivität in Azure Database Migration Service.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Migrieren des Beispielschemas mithilfe der Hilfsprogramme mysqldump und mysql.
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs. Weitere Informationen finden Sie auf der Seite [Azure Database Migration Service – Preise](https://azure.microsoft.com/pricing/details/database-migration/).

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Dieser Artikel beschreibt, wie Sie eine Onlinemigration von einer Instanz von RDS MySQL zu Azure Database for MySQL durchführen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Stellen Sie sicher, dass der MySQL-Quellserver eine unterstützte MySQL Community Edition ausführt. Um die Version Ihrer MySQL-Instanz zu ermitteln, führen Sie im mysql-Hilfsprogramm oder in MySQL Workbench den folgenden Befehl aus:

    ```
    SELECT @@version;
    ```

    Weitere Informationen finden Sie im Artikel [Unterstützte Versionen von Azure Database for MySQL](../mysql/concepts-supported-versions.md).

* Laden Sie die [MySQL-Beispieldatenbank **Employees**](https://dev.mysql.com/doc/employee/en/employees-installation.html) herunter, und installieren Sie sie.
* Erstellen Sie eine Instanz von [Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md).
* Erstellen Sie ein virtuelles Microsoft Azure-Netzwerk für Azure Database Migration Service mithilfe des Azure Resource Manager-Bereitstellungsmodells, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.
* Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des virtuellen Netzwerks nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445 und 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurieren Sie Ihre [Windows-Firewall](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (oder Ihre Linux-Firewall) für Datenbank-Engine-Zugriff. Verwenden Sie bei MySQL Server Port 3306 für die Verbindung.

> [!NOTE]
> Azure Database for MySQL unterstützt nur InnoDB-Tabellen. Informationen zum Konvertieren von MyISAM-Tabellen in InnoDB finden Sie im Artikel [Konvertieren von Tabellen von MyISAM zu InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html).

### <a name="set-up-aws-rds-mysql-for-replication"></a>Einrichten von AWS RDS MySQL für die Replikation

1. Um eine neue Parametergruppe zu erstellen, befolgen Sie die von AWS im Artikel [MySQL-Datenbankprotokolldateien](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html) im Abschnitt **Binäres Protokollformat** bereitgestellten Anweisungen.
2. Erstellen Sie eine neue Parametergruppe mit der folgenden Konfiguration:
    * log_bin = ON
    * binlog_format = row
    * binlog_checksum = NONE
3. Speichern Sie die neue Parametergruppe.
4. Ordnen Sie die neue Parametergruppe der RDS MySQL-Instanz zu. Möglicherweise ist ein Neustart erforderlich.
5. Sobald die Parametergruppe implementiert wurde, stellen Sie eine Verbindung zur MySQL-Instanz her, und [legen Sie die binlog-Aufbewahrungsdauer](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/mysql_rds_set_configuration.html#mysql_rds_set_configuration-usage-notes.binlog-retention-hours) auf mindestens fünf Tage fest.
```
call mysql.rds_set_configuration('binlog retention hours', 120);
```

## <a name="migrate-the-schema"></a>Migrieren des Schemas

1. Extrahieren Sie das Schema aus der Quelldatenbank, und wenden Sie es auf die Zieldatenbank an, um die Migration aller Datenbankobjekte wie Tabellenschemas, Indizes und gespeicherten Prozeduren abzuschließen.

    Die einfachste Möglichkeit zum Migrieren nur des Schemas ist die Verwendung von mysqldump mit dem Parameter „--no-data“. Der folgende Befehl wird zum Migrieren des Schemas verwendet:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Zum Sichern einer Schemadatei für die Datenbank **Employees** verwenden Sie z.B. den folgenden Befehl:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importieren Sie das Schema in den Zieldienst Azure Database for MySQL. Um die Schemasicherungsdatei wiederherzustellen, führen Sie den folgenden Befehl aus:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Zum Importieren des Schemas für die Datenbank **Employees** geben Sie z.B. Folgendes ein:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Enthält das Schema Fremdschlüssel, tritt beim ersten Ladevorgang und bei der fortlaufenden Synchronisierung der Migration ein Fehler auf. Führen Sie das folgende Skript in MySQL Workbench aus, um das Skript zum Löschen von Fremdschlüsseln zu extrahieren, und fügen Sie das Fremdschlüsselskript im Ziel hinzu (Azure Database for MySQL).

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Führen Sie „drop foreign key“ (zweite Spalte) im Abfrageergebnis aus, um den Fremdschlüssel zu löschen.

> [!NOTE]
> Azure DMS unterstützt die referenzielle CASCADE-Aktion nicht, die verwendet werden kann, um eine übereinstimmende Zeile in der untergeordneten Tabelle automatisch zu löschen oder zu aktualisieren, wenn eine Zeile in der übergeordneten Tabelle gelöscht oder aktualisiert wird. Weitere Informationen finden Sie in der MySQL-Dokumentation im Abschnitt „Referential Actions“ (referenzielle Aktionen) des Artikels [FOREIGN KEY Constraints](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> Azure DMS erfordert, dass Sie Fremdschlüsseleinschränkungen auf dem Zieldatenbankserver während des anfänglichen Ladens von Daten löschen. Zudem können Sie keine referenziellen Aktionen verwenden. Wenn Ihre Workload von der Aktualisierung einer zugehörigen untergeordneten Tabelle über diese referenzielle Aktion abhängt, empfiehlt es sich, stattdessen eine [Sicherung und Wiederherstellung](../mysql/concepts-migrate-dump-restore.md) durchzuführen. 

5. Enthalten die Daten einen Trigger (INSERT- oder UPDATE-Trigger), wird die Datenintegrität im Ziel vor dem Replizieren von Daten aus der Quelle erzwungen. Es wird empfohlen, während der Migration die Trigger in allen Tabellen *im Ziel* zu deaktivieren und die Trigger nach Abschluss der Migration erneut zu aktivieren.

    So deaktivieren Sie Trigger in der Zieldatenbank:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Wenn es in einer Tabelle Instanzen des ENUM-Datentyps vorhanden sind, empfehlen wir, vorübergehend auf den Datentyp „character varying“ in der Zieltabelle zu aktualisieren. Wenn die Datenreplikation abgeschlossen ist, setzen Sie den Datentyp auf „ENUM“ zurück.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Erstellen einer Instanz von Azure Database Migration Service

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten.

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk bietet Azure Database Migration Service Zugriff auf die MySQL-Quellinstanz und die Azure Database for MySQL-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie einen Tarif aus. Für diese Onlinemigration müssen Sie Premium auswählen: 4vCores-Tarif.

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Suchen Sie im Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

     ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Wählen Sie **+ Neues Migrationsprojekt** aus.
4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **MySQL**, und wählen Sie dann im Textfeld **Typ des Zielservers** die Option **AzureDbForMySQL**.
5. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

    > [!IMPORTANT]
    > Wählen Sie unbedingt **Onlinedatenmigration** aus; Offlinemigrationen werden für dieses Szenario nicht unterstützt.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

6. Wählen Sie **Speichern** aus.

7. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

    > [!NOTE]
    > Notieren Sie sich die Voraussetzungen für die Einrichtung der Onlinemigration auf dem Projekterstellungsblatt.

## <a name="specify-source-details"></a>Angeben von Quelldetails

* Geben Sie auf dem Bildschirm **Migrationsquelldetail** die Verbindungsdetails für die MySQL-Quellinstanz an.

   ![Quellendetails](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Wählen Sie **Speichern** aus, und geben Sie dann auf dem Bildschirm **Zieldetails** die Verbindungsdetails für den Azure Database for MySQL-Zielserver an, der vorab bereitgestellt wird und das Schema **Employees** über MySQLDump implementiert.

    ![Auswählen des Ziels](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Initialisierung**.

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Ausgeführt** lautet.

    ![Aktivitätsstatus: Wird ausgeführt](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. Wählen Sie unter **DATENBANKNAME** eine bestimmte Datenbank aus, um den Migrationsstatus für die Vorgänge **Vollständiger Datenladevorgang** und **Inkrementelle Datensynchronisierung** abzurufen.

    Unter **Vollständiger Datenladevorgang** wird der Migrationsstatus des ersten Ladevorgangs und unter **Inkrementelle Datensynchronisierung** der CDC-Status (Change Data Capture) angezeigt.

    ![Inventarbildschirm: Vollständiger Datenladevorgang](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Inventarbildschirm: Inkrementelle Datensynchronisierung](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

    ![Starten der Übernahme](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Achten Sie darauf, alle eingehenden Transaktionen für die Quelldatenbank zu beenden. Warten Sie, bis der Zähler **Ausstehende Änderungen** das Ergebnis **0** zeigt.
3. Aktivieren Sie **Bestätigen**, und klicken Sie dann auf **Anwenden**.
4. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Azure Database for MySQL-Zieldatenbank her.

Die Onlinemigration von einer lokalen Instanz von MySQL zu Azure Database for MySQL ist jetzt abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
* Informationen zu Azure Database for MySQL finden Sie im Artikel [Was ist Azure-Datenbank für MySQL?](../mysql/overview.md).
* Wenn Sie weitere Fragen haben, senden Sie eine E-Mail an den Alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).