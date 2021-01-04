---
title: 'Tutorial: Onlinemigration von MySQL zu Azure Database for MySQL'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer lokalen MySQL-Instanz zu Azure Database for MySQL durchführen.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 41c0bd23bbd2d69506a979c5a36ac40f73258f2c
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605512"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-online-using-dms"></a>Tutorial: Ausführen einer Onlinemigration von MySQL zu Azure Database for MySQL mithilfe von DMS

Mit Azure Database Migration Service können Sie die Datenbanken mit minimaler Ausfallzeit von einer lokalen MySQL-Instanz zu [Azure Database for MySQL](../mysql/index.yml) migrieren. Somit kommt es bei der Migration nur zu einer geringen Ausfallzeit für die Anwendung. In diesem Tutorial migrieren Sie die Beispieldatenbank **Employees** von einer lokalen Instanz von MySQL 5.7 zu Azure Database for MySQL. Zu diesem Zweck verwenden Sie eine Onlinemigrationsaktivität in Azure Database Migration Service.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Migrieren des Beispielschemas mit dem Hilfsprogramm „mysqldump“
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> * Ausführen der Migration
> * Überwachen der Migration

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

> [!NOTE]
> Vorurteilsfreie Kommunikation
>
> Microsoft setzt sich für Diversität und Inklusion ein. In diesem Artikel wird das Wort _Slave_ verwendet. Laut dem [Microsoft-Styleguide für vorurteilsfreie Kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) sollte dieses Wort jedoch vermieden werden. In diesem Artikel wird es aus Konsistenzgründen gebraucht, da das Wort derzeit noch in der Software vorkommt. Wenn die Software aktualisiert und um dieses Wort bereinigt wird, wird auch der Artikel entsprechend aktualisiert.
>


## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Laden Sie [MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.6 oder 5.7 herunter, und installieren Sie die Edition. Die lokale MySQL-Version muss mit der Version von Azure Database for MySQL übereinstimmen. Beispiel: MySQL 5.6 kann nur zu Azure Database for MySQL 5.6 migriert, aber nicht auf 5.7 aktualisiert werden. Eine Migration zu oder von MySQL 8.0 wird nicht unterstützt. Eine Migration zu oder von MySQL 8.0 wird nicht unterstützt.
* [Erstellen einer Instanz in Azure Database for MySQL](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md) Im Artikel [Azure-Datenbank für MySQL: Verwenden von MySQL Workbench zum Verbinden und Abfragen von Daten](../mysql/connect-workbench.md) finden Sie ausführliche Informationen zum Erstellen und Verbinden einer Datenbank über das Azure-Portal.  
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.

* Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des virtuellen Netzwerks nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf den MySQL-Quellserver zugreifen kann (standardmäßig TCP-Port 3306).
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.
* Erstellen Sie für Azure Database for MySQL eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um den Zugriff auf die Zieldatenbanken durch Azure Database Migration Service zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
* Die MySQL-Quellinstanz muss unter einer unterstützten Version von MySQL Community Edition ausgeführt werden. Führen Sie zum Ermitteln der Version der MySQL-Instanz im MySQL-Hilfsprogramm oder in MySQL Workbench den folgenden Befehl aus:

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL unterstützt nur InnoDB-Tabellen. Informationen zum Konvertieren von MyISAM-Tabellen in InnoDB finden Sie im Artikel [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Konvertieren von Tabellen von MyISAM zu InnoDB).

* Aktivieren Sie mithilfe der folgenden Konfiguration die binäre Protokollierung in der Datei „my.ini“ (Windows) oder „my.cnf“ (Unix) in der Quelldatenbank.

  * **server_id** = 1 oder höher (nur für MySQL 5.6 relevant)
  * **log-bin** =\<path> (nur für MySQL 5.6 relevant)    Beispiel: log-bin = E:\MySQL_logs\BinLog
  * **binlog_format** = row
  * **Expire_logs_days** = 5 (es wird empfohlen, nicht null zu verwenden; nur für MySQL 5.6 relevant)
  * **Binlog_row_image** = full (nur für MySQL 5.6 relevant)
  * **log_slave_updates** = 1

* Für den Benutzer muss die Rolle „ReplicationAdmin“ mit den folgenden Berechtigungen festgelegt sein:

  * **REPLICATION CLIENT**: Nur für Aufgaben zur Änderungsverarbeitung erforderlich. Für Aufgaben zum vollständigen Laden wird diese Berechtigung daher nicht benötigt.
  * **REPLICATION CLIENT**: Nur für Aufgaben zur Änderungsverarbeitung erforderlich. Für Aufgaben zum vollständigen Laden wird diese Berechtigung daher nicht benötigt.
  * **SUPER**: Nur in älteren Versionen als MySQL 5.6.6 erforderlich

## <a name="migrate-the-sample-schema"></a>Migrieren des Beispielschemas

Zum Fertigstellen aller Datenbankobjekte wie Tabellenschemas, Indizes und gespeicherter Prozeduren muss das Schema aus der Quelldatenbank extrahiert und auf die Datenbank angewendet werden. Zum Extrahieren des Schemas können Sie „mysqldump“ mit dem Parameter `--no-data` verwenden.

Wenn sich die MySQL-Beispieldatenbank **Employees** im lokalen System befindet, lautet der Befehl für die Schemamigration mithilfe von mysqldump wie folgt:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Beispiel:

```
mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
```

Führen Sie den folgenden Befehl aus, um das Schema in die Azure Database for MySQL-Zielinstanz zu migrieren:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Beispiel:

```
mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
 ```

Enthält das Schema Fremdschlüssel, tritt beim ersten Ladevorgang und bei der fortlaufenden Synchronisierung der Migration ein Fehler auf.  Führen Sie das folgende Skript in MySQL Workbench aus, um das Skript zum Löschen des Fremdschlüssels und das Skript zum Hinzufügen des Fremdschlüssels zu extrahieren.

```sql
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

Führen Sie „drop foreign key“ (zweite Spalte) im Abfrageergebnis aus, um den Fremdschlüssel zu löschen.

> [!NOTE]
> Azure DMS unterstützt die referenzielle CASCADE-Aktion nicht, die verwendet werden kann, um eine übereinstimmende Zeile in der untergeordneten Tabelle automatisch zu löschen oder zu aktualisieren, wenn eine Zeile in der übergeordneten Tabelle gelöscht oder aktualisiert wird. Weitere Informationen finden Sie in der MySQL-Dokumentation im Abschnitt „Referential Actions“ (referenzielle Aktionen) des Artikels [FOREIGN KEY Constraints](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html).
> Azure DMS erfordert, dass Sie Fremdschlüsseleinschränkungen auf dem Zieldatenbankserver während des anfänglichen Ladens von Daten löschen. Zudem können Sie keine referenziellen Aktionen verwenden. Wenn Ihre Workload von der Aktualisierung einer zugehörigen untergeordneten Tabelle über diese referenzielle Aktion abhängt, empfiehlt es sich, stattdessen eine [Sicherung und Wiederherstellung](../mysql/concepts-migrate-dump-restore.md) durchzuführen. 


> [!IMPORTANT]
> Entfernen Sie beim Importieren von Daten mithilfe einer Sicherung die CREATE DEFINER-Befehle manuell oder mithilfe des Befehls „--skip-definer“, wenn Sie einen mysqldump ausführen. DEFINER erfordert Administratorberechtigungen zum Erstellen und ist in Azure Database for MySQL eingeschränkt.

Enthalten die Daten einen Trigger (INSERT- oder UPDATE-Trigger), wird die Datenintegrität am Ziel vor den replizierten Daten aus der Quelle erzwungen. Es wird empfohlen, während der Migration die Trigger in allen Tabellen der Zieldatenbank zu deaktivieren und die Trigger nach der Migration wieder zu aktivieren.

Verwenden Sie zum Deaktivieren von Triggern in der Zieldatenbank den folgenden Befehl:

```
SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-mysql-to-azure-mysql-online/portal-select-subscriptions.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-mysql-to-azure-mysql-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-mysql-to-azure-mysql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Erstellen einer DMS-Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die Azure SQL-Datenbank-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

5. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-mysql-to-azure-mysql-online/dms-settings3.png)

6. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-search.png)

2. Suchen Sie auf dem Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

     ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-mysql-to-azure-mysql-online/dms-instance-search.png)

3. Wählen Sie **+ Neues Migrationsprojekt** aus.
4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **MySQL**, und wählen Sie dann im Textfeld **Typ des Zielservers** die Option **AzureDbForMySQL**.
5. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-mysql-to-azure-mysql-online/dms-create-project4.png)

    > [!NOTE]
    > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

6. Klicken Sie auf **Speichern**, sehen Sie sich die Anforderungen zum Migrieren von Daten mithilfe von DMS an, und klicken Sie dann auf **Aktivität erstellen und ausführen**.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie auf dem Bildschirm **Quelldetails hinzufügen** die Verbindungsdetails für die MySQL-Quellinstanz an.

    ![Bildschirm „Quelldetails hinzufügen“](media/tutorial-mysql-to-azure-mysql-online/dms-add-source-details.png)

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Wählen Sie **Speichern**, und geben Sie dann auf dem Bildschirm **Zieldetails** die Verbindungsdetails für den Azure Database for MySQL-Zielserver an. Dabei handelt es sich um die zuvor bereitgestellte Instanz von Azure Database for MySQL, für die das **Employees**-Schema mithilfe von „mysqldump“ bereitgestellt wurde.

    ![Bildschirm „Zieldetails“](media/tutorial-mysql-to-azure-mysql-online/dms-add-target-details.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-mysql-to-azure-mysql-online/dms-map-target-details.png)
   > [!NOTE] 
   > Sie können in diesem Schritt mehrere Datenbanken auswählen. Beachten Sie jedoch, dass jede Instanz von Azure Database Migration Service bis zu vier Datenbanken für die gleichzeitige Migration unterstützt. Außerdem besteht eine Beschränkung von zwei Instanzen von Azure Database Migration Service pro Region in einem Abonnement. Wenn Sie z. B. 40 Datenbanken migrieren müssen, können Sie jeweils nur acht Datenbanken gleichzeitig migrieren, aber auch nur, wenn Sie zwei Instanzen von Azure Database Migration Service erstellt haben.

3. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-mysql-to-azure-mysql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Initialisierung**.

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Abgeschlossen** lautet.

     ![Aktivitätsstatus: Abgeschlossen](media/tutorial-mysql-to-azure-mysql-online/dms-activity-completed.png)

2. Wählen Sie unter **Datenbankname** eine bestimmte Datenbank aus, um den Migrationsstatus für die Vorgänge **Full data load** (Vollständiger Datenladevorgang) und **Inkrementelle Datensynchronisierung** abzurufen.

    Unter „Full data load“ (Vollständiger Datenladevorgang) wird der Migrationsstatus des ersten Ladevorgangs und unter „Inkrementelle Datensynchronisierung“ der CDC-Status (Change Data Capture) angezeigt.

     ![Aktivitätsstatus: Vollständiger Ladevorgang abgeschlossen](media/tutorial-mysql-to-azure-mysql-online/dms-activity-full-load-completed.png)

     ![Aktivitätsstatus: Inkrementelle Datensynchronisierung](media/tutorial-mysql-to-azure-mysql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

    ![Starten der Übernahme](media/tutorial-mysql-to-azure-mysql-online/dms-start-cutover.png)

2. Achten Sie darauf, alle eingehenden Transaktionen für die Quelldatenbank zu beenden. Warten Sie, bis der Zähler **Ausstehende Änderungen** das Ergebnis **0** zeigt.
3. Aktivieren Sie **Bestätigen**, und klicken Sie dann auf **Anwenden**.
4. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Azure SQL-Zieldatenbank her.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu bekannten Problemen und Einschränkungen beim Ausführen der Onlinemigration zu Azure Database for MySQL finden Sie im Artikel [Known issues/migration limitations with online migrations to Azure DB for MySQL](known-issues-azure-mysql-online.md) (Bekannte Probleme/Migrationseinschränkungen bei der Onlinemigration zu Azure Database for MySQL).
* Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
* Informationen zu Azure Database for MySQL finden Sie im Artikel [Was ist Azure-Datenbank für MySQL?](../mysql/overview.md).