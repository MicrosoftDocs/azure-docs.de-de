---
title: 'Tutorial: Onlinemigration von RDS SQL Server zu Microsoft Azure SQL-Datenbank'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer RDS SQL Server-Instanz zu einer einzelnen oder einer verwalteten Azure SQL-Datenbank-Instanz durchführen.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 249667dfa8c0491027f0244d4aa5e49d19399ab0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955037"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Tutorial: Migrieren von RDS SQL Server zu Azure SQL-Datenbank oder zu einer verwalteten Azure SQL-Instanz mithilfe von DMS (online)

Mit Azure Database Migration Service können Sie die Datenbanken aus einer RDS SQL Server-Instanz mit minimaler Ausfallzeit zu [Azure SQL-Datenbank](/azure/sql-database/) oder einer [verwalteten Azure SQL-Instanz](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) migrieren. In diesem Tutorial migrieren Sie die Datenbank **Adventureworks2012**, die in einer RDS SQL Server-Instanz von SQL Server 2012 (oder höher) wiederhergestellt wurde, mithilfe von Azure Database Migration Service zu SQL-Datenbank oder einer verwalteten SQL-Instanz.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen einer Datenbank in Azure SQL-Datenbank oder einer SQL Managed Instance 
> * Migrieren des Beispielschemas über den Data Migration Assistant
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen Sie ein Migrationsprojekt mithilfe von Azure Database Migration Service.
> * Ausführen der Migration
> * Überwachen der Migration
> * Laden Sie einen Migrationsbericht herunter.

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs. Weitere Informationen finden Sie auf der Seite [Azure Database Migration Service – Preise](https://azure.microsoft.com/pricing/details/database-migration/).

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird eine Onlinemigration von RDS SQL Server zu SQL-Datenbank oder einer verwalteten SQL-Instanz beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Erstellen Sie eine [RDS SQL Server-Datenbank](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Erstellen Sie eine Datenbank in Azure SQL-Datenbank im Azure-Portal](../azure-sql/database/single-database-create-quickstart.md), oder [erstellen Sie eine Datenbank in SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md), und erstellen Sie dann eine leere Datenbank mit dem Namen **AdventureWorks2012**. 
* Laden Sie mindestens Version 3.3 des [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) herunter, und installieren Sie sie.
* Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden. Stellen Sie beim Migrieren zu einer verwalteten SQL-Instanz sicher, dass Sie die DMS-Instanz in demselben virtuellen Netzwerk erstellen, das auch für die verwaltete SQL-Instanz verwendet wird. Verwenden Sie hierbei aber ein anderes Subnetz.  Falls Sie ein anderes virtuelles Netzwerk für DMS nutzen, müssen Sie ein Peering virtueller Netzwerke zwischen diesen beiden virtuellen Netzwerken erstellen. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, da Azure Database Migration Service über keine Internetverbindung verfügt. 

* Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des virtuellen Netzwerks nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433).
* Erstellen Sie für SQL-Datenbank eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um für Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
* Achten Sie darauf, dass die Anmeldeinformationen zum Herstellen der Verbindung mit der RDS SQL Server-Quellinstanz einem Konto zugeordnet sind, das Mitglied der Serverrolle „Processadmin“ und der Datenbankrolle „db_owner“ in allen zu migrierenden Datenbanken ist.
* Stellen Sie sicher, dass die Anmeldeinformationen zum Herstellen der Verbindung mit der Zieldatenbank über die Berechtigung CONTROL DATABASE für die Zieldatenbank in SQL-Datenbank verfügen und Mitglied der Rolle „sysadmin“ sind, wenn eine Migration zu einer Datenbank in SQL Managed Instance durchgeführt wird.
* Die Quellversion von RDS SQL Server muss SQL Server 2012 oder höher sein. Informationen zum Ermitteln der Version der ausgeführten SQL Server-Instanz finden Sie im Artikel [So ermitteln Sie die Version, Edition und Updateebene von SQL Server und seinen Komponenten](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Aktivieren Sie Change Data Capture (CDC) in der RDS SQL Server-Datenbank und für alle Benutzertabellen, die für die Migration ausgewählt wurden.
    > [!NOTE]
    > Sie können das unten angegebene Skript verwenden, um CDC in einer RDS SQL Server-Datenbank zu aktivieren.
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Sie können das unten angegebene Skript verwenden, um CDC in allen Tabellen zu aktivieren.
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Deaktivieren Sie die Datenbanktrigger in der Zieldatenbank.
    > [!NOTE]
    > Sie können die Datenbanktrigger in der Zieldatenbank mit der folgenden Abfrage ermitteln:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Weitere Informationen finden Sie im Artikel [DISABLE TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Migrieren des Beispielschemas
Migrieren Sie das Schema mithilfe von DMA.

> [!NOTE]
> Bevor Sie ein Migrationsprojekt im DMA erstellen, vergewissern Sie sich, dass Sie bereits eine Datenbank in SQL-Datenbank oder SQL Managed Instance bereitgestellt haben, wie in den Voraussetzungen aufgeführt. In diesem Tutorial lautet der Name der Datenbank **AdventureWorks2012**. Sie können jedoch einen beliebigen Namen angeben.

Führen Sie zur Migration des **AdventureWorks2012**-Schemas die folgenden Schritte aus:

1. Wählen Sie im Data Migration Assistant das Symbol „Neu (+)“ aus, und wählen Sie dann unter **Projekttyp** die Option **Migration** aus.
2. Geben Sie einen Projektnamen im Textfeld **Source server type** (Quellservertyp) an, wählen Sie **SQL Server**, und wählen Sie dann im Textfeld **Target server type** (Zielservertyp) die Option **Azure SQL Database** (Azure SQL-Datenbank).

    > [!NOTE]
    > Wählen Sie sowohl für die Migration zu Azure SQL-Datenbank als auch zu SQL Managed Instance **Azure SQL-Datenbank** als Zielservertyp aus.

3. Wählen Sie unter **Migration Scope** (Migrationsumfang) die Option **Schema only** (Nur Schema).

    Nachdem Sie die vorherigen Schritte ausgeführt haben, wird die DMA-Oberfläche wie in der folgenden Abbildung angezeigt:

    ![Erstellen eines Data Migration Assistant-Projekts](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.
5. Geben Sie im DMA die Quellverbindungsdetails für Ihre SQL Server-Instanz an, wählen Sie **Verbinden**, und wählen Sie dann die Datenbank **AdventureWorks2012** aus.

    ![Data Migration Assistant-Quellverbindungsdetails](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Wählen Sie **Weiter** aus. Geben Sie unter **Verbindung mit Zielserver herstellen** die Zielverbindungsdetails für die Datenbank in SQL-Datenbank oder SQL Managed Instance an, wählen Sie **Verbinden** aus, und wählen Sie dann die Datenbank **AdventureWorksAzure** aus, die Sie vorab bereitgestellt haben.

    ![Data Migration Assistant-Zielverbindungsdetails](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Wählen Sie **Weiter** aus, um zum Bildschirm **Select objects** (Objekte auswählen) zu gelangen. Hier können Sie die Schemaobjekte in der Datenbank **AdventureWorks2012** angeben, die bereitgestellt werden müssen.

    Standardmäßig sind alle Objekte ausgewählt.

    ![Generieren von SQL-Skripts](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Wählen Sie **Generate SQL script** (SQL-Skript generieren) aus, um die SQL-Skripts zu erstellen, und prüfen Sie die Skripts dann auf Fehler.

    ![Schemaskript](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Wählen Sie **Deploy schema** (Schema bereitstellen) aus, um das Schema bereitzustellen, und prüfen Sie den Zielserver nach der Schemabereitstellung auf Anomalien.

    ![Bereitstellen des Schemas](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten. 

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die SQL-Datenbank-Zielinstanz oder SQL Managed Instance-Instanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie einen Tarif aus. Für diese Onlinemigration ist der Tarif „Premium“ die richtige Wahl.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

     ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

      ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Suchen Sie im Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

     ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Wählen Sie **+ Neues Migrationsprojekt** aus.
4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **AWS RDS for SQL Server** (AWS RDS für SQL Server), und wählen Sie dann im Textfeld **Typ des Zielservers** die Option **Azure SQL-Datenbank**.

    > [!NOTE]
    > Wählen Sie sowohl für die Migration zu SQL-Datenbank als auch zu SQL Managed Instance **Azure SQL-Datenbank** als Zielservertyp aus.

5. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

    > [!IMPORTANT]
    > Wählen Sie unbedingt **Onlinedatenmigration** aus; Offlinemigrationen werden für dieses Szenario nicht unterstützt.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

6. Wählen Sie **Speichern** aus.

7. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

    ![Erstellen und Ausführen einer Database Migration Service-Aktivität](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Migrationsquelldetail** die Verbindungsdetails für die SQL Server-Quellinstanz an.

    Stellen Sie sicher, dass Sie einen vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den SQL Server-Quellinstanznamen verwenden.

2. Wenn Sie auf Ihrem Quellserver kein vertrauenswürdiges Zertifikat installiert haben, aktivieren Sie das Kontrollkästchen **Serverzertifikat vertrauen**.

    Wenn kein vertrauenswürdiges Zertifikat installiert ist, generiert SQL Server beim Starten der Instanz ein selbstsigniertes Zertifikat. Dieses Zertifikat wird zum Verschlüsseln der Anmeldeinformationen für Clientverbindungen verwendet.

    > [!CAUTION]
    > TLS-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind anfällig für Man-in-the-Middle-Angriffe. In einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, sollten Sie sich nicht auf TLS mit Verwendung selbstsignierter Zertifikate verlassen.

   ![Quellendetails](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Wählen Sie **Speichern** aus, und geben Sie dann im Bildschirm **Migrationszieldetail** die Verbindungsdetails für die Zieldatenbank in Azure an.

    ![Auswählen des Ziels](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Wählen Sie **Save** (Speichern) im Bildschirm **Select tables** (Tabellen auswählen) aus, erweitern Sie die Tabellenauflistung, und prüfen Sie die Liste betroffener Felder.

    Azure Database Migration Service wählt automatisch alle leeren Quelltabellen aus, die in der Zieldatenbank vorhanden sind. Wenn Sie Tabellen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Tabellen in diesem Fenster explizit auswählen.

    ![Tabellen auswählen](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Wählen Sie **Speichern**, nachdem Sie die folgenden **erweiterten Einstellungen für die Onlinemigration** vorgenommen haben.

    | Einstellung | BESCHREIBUNG |
    | ------------- | ------------- |
    | **Maximale Anzahl von Tabellen, die parallel geladen werden** | Gibt die Anzahl von Tabellen an, die von DMS während der Migration parallel ausgeführt werden. Der Standardwert ist 5. Dieser Wert kann aber auf einen optimalen Wert festgelegt werden, um bestimmte Migrationsanforderungen zu erfüllen, die auf POC-Migrationen basieren. |
    | **Wenn die Quelltabelle abgeschnitten ist** | Gibt an, ob DMS die Zieltabelle während der Migration abschneidet. Diese Einstellung ist hilfreich, wenn im Rahmen des Migrationsprozesses eine oder mehrere Tabellen abgeschnitten werden. |
    | **Einstellungen für LOB-Daten (Large Object) konfigurieren** | Gibt an, ob DMS unbegrenzt LOB-Daten migriert oder die migrierten LOB-Daten auf eine bestimmte Größe beschränkt.  Wenn für die migrierten LOB-Daten eine Beschränkung gilt, werden alle LOB-Daten abgeschnitten, die diese Beschränkung überschreiten. Für Produktionsmigrationen wird empfohlen, die Option **Unbegrenzte LOB-Größe zulassen** zu aktivieren, um Datenverlust zu verhindern. Aktivieren Sie beim Zulassen einer unbegrenzten LOB-Größe das Kontrollkästchen **Migrate LOB data in a single block when the LOB size is less than (KB) specified** (LOB-Daten als einzelnen Block migrieren, wenn als LOB-Größe weniger als (KB) angegeben sind), um die Leistung zu erhöhen. |

    ![Festlegen von erweiterten Einstellungen für die Onlinemigration](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

* Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Initialisierung**.

    ![Aktivitätsstatus: Initialisierung](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Ausgeführt** lautet.

2. Klicken Sie auf eine bestimmte Datenbank, um den Migrationsstatus für die Vorgänge **Full data load** (Vollständiger Datenladevorgang) und **Inkrementelle Datensynchronisierung** abzurufen.

    ![Aktivitätsstatus: In Bearbeitung](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

    ![Starten der Übernahme](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Achten Sie darauf, alle eingehenden Transaktionen für die Quelldatenbank zu beenden. Warten Sie, bis der Zähler **Ausstehende Änderungen** das Ergebnis **0** zeigt.
3. Aktivieren Sie **Bestätigen**, und klicken Sie dann auf **Anwenden**.
4. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Zieldatenbank her.

    ![Aktivitätsstatus: Abgeschlossen](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu bekannten Problemen und Einschränkungen beim Ausführen der Onlinemigration zu finden Sie im Artikel [Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen](known-issues-azure-sql-online.md).
* Informationen zu Database Migration Service finden Sie im Artikel [Was ist Database Migration Service?](./dms-overview.md).
* Informationen zu SQL-Datenbank finden Sie im Artikel [Der SQL-Datenbank-Dienst](../azure-sql/database/sql-database-paas-overview.md).
* Informationen zu SQL Managed Instance finden Sie im Artikel [Was ist SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)