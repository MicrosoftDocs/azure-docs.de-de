---
title: 'Tutorial: Onlinemigration von SQL Server zu einer einzelnen SQL-Datenbank'
titleSuffix: Azure Database Migration Service
description: Es wird beschrieben, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer SQL Server-Instanz zu Azure SQL-Datenbank durchführen.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/21/2020
ms.openlocfilehash: 29a154e33d8025f53b929bf0bb7602b2d0a64b13
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955020"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Tutorial: Onlinemigration von SQL Server zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank mit DMS

Mit Azure Database Migration Service können Sie die Datenbanken mit minimaler Ausfallzeit von einer SQL Server-Instanz zu [Azure SQL-Datenbank](/azure/sql-database/) migrieren. In diesem Tutorial migrieren Sie die Datenbank **Adventureworks2012**, die in einer lokalen Instanz von SQL Server 2016 (oder höher) wiederhergestellt wurde, mithilfe von Azure Database Migration Service zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> - Bewerten Ihrer lokalen Datenbank mithilfe des Data Migration Assistant
> - Migrieren des Beispielschemas über den Data Migration Assistant
> - Erstellen einer Instanz von Azure Database Migration Service
> - Erstellen Sie ein Migrationsprojekt mithilfe von Azure Database Migration Service.
> - Ausführen der Migration
> - Überwachen der Migration
> - Laden Sie einen Migrationsbericht herunter.

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs. Weitere Informationen finden Sie auf der Seite [Azure Database Migration Service – Preise](https://azure.microsoft.com/pricing/details/database-migration/).

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird eine Onlinemigration von SQL Server zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank beschrieben. Informationen zur Offlinemigration finden Sie unter [Offlinemigration von SQL Server zu Azure SQL-Datenbank mit DMS](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Laden Sie [SQL Server 2012 oder höher](https://www.microsoft.com/sql-server/sql-server-downloads) herunter, und installieren Sie es.
- Aktivieren Sie das TCP/IP-Protokoll (dieses wird während der SQL Server Express-Installation standardmäßig deaktiviert), indem Sie die Anweisungen im Artikel [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) befolgen.
- Erstellen Sie eine Einzeldatenbank (oder eine in einem Pool zusammengefasste Datenbank) in Azure SQL-Datenbank gemäß der Anleitung im Artikel [Schnellstart: Erstellen einer Einzeldatenbank in Azure SQL-Datenbank über das Azure-Portal](../azure-sql/database/single-database-create-quickstart.md).

    > [!NOTE]
    > Falls Sie SQL Server Integration Services (SSIS) verwenden und die Katalogdatenbank für Ihre SSIS-Projekte/-Pakete (SSISDB) von SQL Server zu Azure SQL-Datenbank migrieren möchten, wird die Ziel-SSISDB automatisch für Sie erstellt und verwaltet, wenn Sie SSIS in Azure Data Factory (ADF) bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

- Laden Sie mindestens Version 3.3 des [Datenmigrations-Assistenten](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) herunter, und installieren Sie sie.
- Erstellen Sie ein Microsoft Azure Virtual Network für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver entweder über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    > - Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > - Speicherendpunkt
    > - Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, da Azure Database Migration Service über keine Internetverbindung verfügt.

- Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des virtuellen Netzwerks nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem Azure Virtual Network finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433). Wenn die Standardinstanz an einem anderen Port lauscht, fügen Sie diesen Port der Firewall hinzu.
- Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
- Wenn Sie eine Firewall-Appliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, damit Azure Database Migration Service auf die Quelldatenbanken für die Migration zugreifen kann.
- Erstellen Sie für Azure SQL-Datenbank eine [Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um für Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
- Stellen Sie sicher, dass die für die Verbindung mit der SQL Server-Quellinstanz verwendeten Anmeldeinformationen über [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-Berechtigungen verfügen.
- Stellen Sie sicher, dass die für die Verbindung mit der Azure SQL-Zieldatenbankinstanz verwendeten Anmeldeinformationen die Berechtigung CONTROL DATABASE für die Azure SQL-Zieldatenbankinstanzen besitzen.
- Die Quellversion von SQL Server muss SQL Server 2005 oder höher sein. Informationen zum Ermitteln der Version der ausgeführten SQL Server-Instanz finden Sie im Artikel [So ermitteln Sie die Version, Edition und Updateebene von SQL Server und seinen Komponenten](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- Datenbanken müssen entweder im massenprotokollierten oder im vollständigen Wiederherstellungsmodus vorliegen. Wenn Sie das für Ihre SQL Server-Instanz konfigurierte Wiederherstellungsmodell ermitteln möchten, lesen Sie die Informationen im Artikel [Anzeigen oder Ändern des Wiederherstellungsmodells einer Datenbank (SQL Server)](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Stellen Sie sicher, dass vollständige Datenbanksicherungen für die Datenbanken erstellt werden. Informationen zum Erstellen einer vollständigen Datenbanksicherung finden Sie im Artikel [Vorgehensweise: Erstellen einer vollständigen Datenbanksicherung (Transact-SQL)](/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)).
- Wenn eine der Tabellen keinen Primärschlüssel enthält, aktivieren Sie Change Data Capture (CDC) für die Datenbank und bestimmte Tabellen.
    > [!NOTE]
    > Mit dem folgenden Skript können Sie Tabellen suchen, die keinen Primärschlüssel enthalten.
    
    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
    ```

    Wenn in den Ergebnissen für mindestens eine Tabelle „0“ für „is_tracked_by_cdc“ angezeigt wird, aktivieren Sie die Änderungserfassung für die Datenbank und die jeweiligen Tabellen. Gehen Sie dazu wie im Artikel [Aktivieren und Deaktivieren von Change Data Capture (SQL Server)](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017) beschrieben vor.

- Konfigurieren Sie die Verteilerrolle für die SQL Server-Quellinstanz.

    >[!NOTE]
    > Anhand der folgenden Abfrage können Sie überprüfen, ob Replikationskomponenten installiert sind.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Wenn im Ergebnis eine Fehlermeldung zurückgegeben wird, in der die Installation von Replikationskomponenten vorgeschlagen wird, installieren Sie die SQL Server-Replikationskomponenten über das Verfahren im folgenden Artikel: [Installieren der SQL Server-Replikation](/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Wenn die Replikation bereits installiert ist, überprüfen Sie anhand des folgenden T-SQL-Befehls, ob die Verteilungsrolle auf dem SQL Server-Quellserver konfiguriert ist.

    ```sql
    EXEC sp_get_distributor;
    ```

    Ist die Verteilung nicht eingerichtet (für den Verteilungsserver wird in der obigen Befehlsausgabe NULL angezeigt), konfigurieren Sie die Verteilung anhand der Anleitung im Artikel [Konfigurieren der Veröffentlichung und der Verteilung](/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Deaktivieren Sie die Datenbanktrigger in der Azure SQL-Zieldatenbank.
    >[!NOTE]
    > Sie können die Datenbanktrigger in der Azure SQL-Zieldatenbank mit der folgenden Abfrage ermitteln:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Weitere Informationen finden Sie im Artikel [DISABLE TRIGGER (Transact-SQL)](/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Bewerten Ihrer lokalen Datenbank

Damit Sie Daten von einer SQL Server-Instanz zu Azure SQL-Datenbank migrieren können, müssen Sie die SQL Server-Datenbank auf Probleme untersuchen, die eine Migration ggf. verhindern können. Führen Sie im Datenmigrations-Assistenten, Version 3.3 oder höher, die im Artikel [Durchführen einer SQL Server-Migrationsbewertung](/sql/dma/dma-assesssqlonprem) beschriebenen Schritte aus, um die Bewertung der lokalen Datenbank abzuschließen.

Führen Sie zum Bewerten einer lokalen Datenbank die folgenden Schritte aus:

1. Klicken Sie im DMA auf das Symbol „Neu (+)“, und wählen Sie dann den Projekttyp **Bewertung** aus.
2. Geben Sie einen Projektnamen im Textfeld **Source server type** (Quellservertyp) an, wählen Sie **SQL Server**, wählen Sie dann im Textfeld **Target server type** (Zielservertyp) die Option **Azure SQL Database** (Azure SQL-Datenbank) und anschließend **Create** (Erstellen), um das Projekt zu erstellen.

   Bei der Bewertung der SQL Server-Quelldatenbank für die Migration zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank können Sie einen oder beide der folgenden Bewertungsberichtstypen auswählen:

   - Check database compatibility (Datenbankkompatibilität prüfen)
   - Check feature parity (Featureparität prüfen)

   Beide Berichtsarten sind standardmäßig ausgewählt.

3. Klicken Sie im DMA auf dem Bildschirm **Optionen** auf **Weiter**.
4. Geben Sie im Bildschirm **Select sources** (Quellen auswählen) im Dialogfeld **Connect to a server** (Verbindung mit einem Server herstellen) die Verbindungsdetails für SQL Server an, und wählen Sie dann **Connect** (Verbinden).
5. Wählen Sie im Dialogfeld **Quellen hinzufügen** den Eintrag **AdventureWorks2012** und dann nacheinander **Hinzufügen** und **Bewertung starten** aus.

    > [!NOTE]
    > Bei Verwendung von SSIS wird die Bewertung der Quell-SSISDB derzeit von DMA nicht unterstützt. SSIS-Projekte/-Pakete werden jedoch bewertet/überprüft, wenn sie in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitgestellt werden. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

    Nach Abschluss der Bewertung werden die Ergebnisse wie in der folgenden Abbildung angezeigt:

    ![Bewerten der Datenmigration](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Für Einzel- oder Pooldatenbanken in Azure SQL-Datenbank ermitteln die Bewertungen sowohl Probleme mit der Featureparität als auch Probleme, die eine Migration verhindern können, wenn eine Bereitstellung in Einzel- oder Pooldatenbanken vorgenommen wird.

    - Die Kategorie **SQL Server-Featureparität** bietet eine umfassende Reihe von Empfehlungen, in Azure verfügbaren alternativen Herangehensweisen sowie Schritte zur Risikominimierung, sodass Sie den Aufwand bei Ihren Migrationsprojekten einplanen können.
    - In der Kategorie **Kompatibilitätsprobleme** sind teilweise oder nicht unterstützte Features angegeben, die zu Kompatibilitätsproblemen führen können, durch die eine Migration der SQL Server-Datenbank(en) zu Azure SQL-Datenbank ggf. verhindert wird. Zudem werden Empfehlungen bereitgestellt, mit deren Hilfe Sie diese Probleme behandeln können.

6. Prüfen Sie die Bewertungsergebnisse auf die oben genannten Probleme, indem Sie die jeweiligen Optionen auswählen.

## <a name="migrate-the-sample-schema"></a>Migrieren des Beispielschemas

Nachdem Sie sich mit der Bewertung vertraut gemacht und sich davon überzeugt haben, dass die ausgewählte Datenbank ein guter Kandidat für die Migration zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank ist, verwenden Sie DMA zum Migrieren des Schemas zu Azure SQL-Datenbank.

> [!NOTE]
> Bevor Sie ein Migrationsprojekt im DMA erstellen, vergewissern Sie sich, dass Sie bereits eine SQL-Datenbank in Azure bereitgestellt haben, wie in den Voraussetzungen aufgeführt. Zum Zweck dieses Tutorials lautet der Name der Azure SQL-Datenbank **AdventureWorksAzure**. Sie können sie jedoch nach Bedarf umbenennen.

> [!IMPORTANT]
> Bei Verwendung von SSIS wird die Migration der Quell-SSISDB derzeit von DMA nicht unterstützt. Sie können jedoch Ihre SSIS-Projekte/-Pakete in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

Führen Sie zur Migration des **AdventureWorks2012**-Schemas zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank die folgenden Schritte aus:

1. Wählen Sie im Data Migration Assistant das Symbol „Neu (+)“ aus, und wählen Sie dann unter **Projekttyp** die Option **Migration** aus.
2. Geben Sie einen Projektnamen im Textfeld **Source server type** (Quellservertyp) an, wählen Sie **SQL Server**, und wählen Sie dann im Textfeld **Target server type** (Zielservertyp) die Option **Azure SQL Database** (Azure SQL-Datenbank).
3. Wählen Sie unter **Migration Scope** (Migrationsumfang) die Option **Schema only** (Nur Schema).

    Nachdem Sie die vorherigen Schritte ausgeführt haben, wird die DMA-Oberfläche wie in der folgenden Abbildung angezeigt:

    ![Erstellen eines Data Migration Assistant-Projekts](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.
5. Geben Sie im DMA die Quellverbindungsdetails für Ihre SQL Server-Instanz an, wählen Sie **Verbinden**, und wählen Sie dann die Datenbank **AdventureWorks2012** aus.

    ![Data Migration Assistant-Quellverbindungsdetails](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Wählen Sie **Weiter** aus. Geben Sie unter **Verbindung mit Zielserver herstellen** die Zielverbindungsdetails für die Azure SQL-Datenbank an, wählen Sie **Verbinden**, und wählen Sie dann die Datenbank **AdventureWorksAzure** aus, die Sie zuvor in Azure SQL-Datenbank bereitgestellt hatten.

    ![Data Migration Assistant-Zielverbindungsdetails](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Wählen Sie **Weiter**, um zum Bildschirm **Select objects** (Objekte auswählen) zu gelangen. Hier können Sie die Schemaobjekte in der Datenbank **AdventureWorks2012** angeben, die in Azure SQL-Datenbank bereitgestellt werden sollen.

    Standardmäßig sind alle Objekte ausgewählt.

    ![Generieren von SQL-Skripts](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Wählen Sie **Generate SQL script** (SQL-Skript generieren) aus, um die SQL-Skripts zu erstellen, und prüfen Sie die Skripts dann auf Fehler.

    ![Schemaskript](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Wählen Sie **Deploy schema** (Schema bereitstellen) aus, um das Schema für Azure SQL-Datenbank bereitzustellen, und prüfen Sie den Zielserver anschließend auf Anomalien.

    ![Bereitstellen des Schemas](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

   ![Abonnements im Portal anzeigen](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus der Dropdownliste aus.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten. 

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die Azure SQL-Datenbank-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

    ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Suchen Sie im Bildschirm **Azure Database Migration Services** nach dem Namen der von Ihnen erstellten Azure Database Migration Service-Instanz, und wählen Sie die Instanz aus.

    ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Wählen Sie **+ Neues Migrationsprojekt** aus.
4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen im Textfeld **Typ des Quellservers** an, wählen Sie **SQL Server**, und wählen Sie dann im Textfeld **Typ des Zielservers** die Option **Azure SQL-Datenbank**.
5. Wählen Sie im Abschnitt **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > Alternativ können Sie **Nur Projekt erstellen** auswählen, um das Migrationsprojekt jetzt zu erstellen und die Migration später durchzuführen.

6. Wählen Sie **Speichern** aus.

7. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

    ![Erstellen und Ausführen einer Database Migration Service-Aktivität](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Migrationsquelldetail** die Verbindungsdetails für die SQL Server-Quellinstanz an.

    Stellen Sie sicher, dass Sie einen vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den SQL Server-Quellinstanznamen verwenden. Sie können auch die IP-Adresse für Situationen verwenden, in denen DNS-Namensauflösung nicht möglich ist.

2. Wenn Sie auf Ihrem Quellserver kein vertrauenswürdiges Zertifikat installiert haben, aktivieren Sie das Kontrollkästchen **Serverzertifikat vertrauen**.

    Wenn kein vertrauenswürdiges Zertifikat installiert ist, generiert SQL Server beim Starten der Instanz ein selbstsigniertes Zertifikat. Dieses Zertifikat wird zum Verschlüsseln der Anmeldeinformationen für Clientverbindungen verwendet.

    > [!CAUTION]
    > TLS-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind anfällig für Man-in-the-Middle-Angriffe. In einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, sollten Sie sich nicht auf TLS mit Verwendung selbstsignierter Zertifikate verlassen.

   ![Quellendetails](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Bei Verwendung von SSIS wird die Migration der Quell-SSISDB derzeit von DMS nicht unterstützt. Sie können jedoch Ihre SSIS-Projekte/-Pakete in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Wählen Sie **Speichern** aus, und geben Sie dann auf dem Bildschirm **Zieldetails für die Migration** die Verbindungsdetails für die Azure SQL-Datenbank-Zielinstanz an. Hierbei handelt es sich um die zuvor bereitgestellte Azure SQL-Datenbank-Instanz, für die das **AdventureWorks2012**-Schema über den DMA bereitgestellt wurde.

    ![Auswählen des Ziels](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Wählen Sie **Save** (Speichern) im Bildschirm **Select tables** (Tabellen auswählen) aus, erweitern Sie die Tabellenauflistung, und prüfen Sie die Liste betroffener Felder.

    Azure Database Migration Service wählt automatisch alle leeren Quelltabellen aus, die in der Zielinstanz von Azure SQL-Datenbank vorhanden sind. Wenn Sie Tabellen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Tabellen auf diesem Blatt explizit auswählen.

    ![Tabellen auswählen](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Klicken Sie auf **Speichern**. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität ein, und überprüfen Sie anschließend die Zusammenfassung, um sicherzustellen, dass die Ziel- und Quelldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Ausführen der Migration

- Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Initialisierung**.

    ![Aktivitätsstatus: Initialisierung](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Ausgeführt** lautet.

2. Klicken Sie auf eine bestimmte Datenbank, um den Migrationsstatus für die Vorgänge **Full data load** (Vollständiger Datenladevorgang) und **Inkrementelle Datensynchronisierung** abzurufen.

    ![Aktivitätsstatus: In Bearbeitung](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Durchführen der Migrationsübernahme

Wenn der erste vollständige Ladevorgang abgeschlossen ist, werden die Datenbanken als **Zur Übernahme bereit** markiert.

1. Wenn Sie die Datenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

    ![Starten der Übernahme](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Achten Sie darauf, alle eingehenden Transaktionen für die Quelldatenbank zu beenden. Warten Sie, bis der Zähler **Ausstehende Änderungen** das Ergebnis **0** zeigt.
3. Aktivieren Sie **Bestätigen**, und klicken Sie dann auf **Anwenden**.
4. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Azure SQL-Zieldatenbank her.

    ![Aktivitätsstatus: Abgeschlossen](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu bekannten Problemen und Einschränkungen beim Ausführen der Onlinemigration zu Azure SQL-Datenbank finden Sie im Artikel [Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank](known-issues-azure-sql-online.md).
- Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
- Informationen zu Azure SQL-Datenbank finden Sie im Artikel [Der Azure SQL-Datenbank-Dienst](../azure-sql/database/sql-database-paas-overview.md).