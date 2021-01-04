---
title: 'Tutorial: Offlinemigration von SQL Server zu einer einzelnen SQL-Datenbank'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Offlinemigration von SQL Server zu Azure SQL-Datenbank durchführen.
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
ms.openlocfilehash: 7710cac7920ef62dfdd638bb8dc177bc765becb6
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955003"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Tutorial: Offlinemigration von SQL Server zu Azure SQL-Datenbank mit DMS

Mit Azure Database Migration Service können Sie die Datenbanken von einer SQL Server-Instanz zu [Azure SQL-Datenbank](/azure/sql-database/) migrieren. In diesem Tutorial migrieren Sie die Datenbank **Adventureworks2012**, die in einer lokalen Instanz von SQL Server 2016 (oder höher) wiederhergestellt wurde, mithilfe von Azure Database Migration Service zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> - Bewerten Ihrer lokalen Datenbank mithilfe des Data Migration Assistant
> - Migrieren des Beispielschemas über den Data Migration Assistant
> - Erstellen einer Instanz von Azure Database Migration Service
> - Erstellen eines Migrationsprojekts mithilfe von Azure Database Migration Service
> - Ausführen der Migration
> - Überwachen der Migration
> - Laden Sie einen Migrationsbericht herunter.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird eine Offlinemigration von SQL Server zu einer Datenbank in Azure SQL-Datenbank beschrieben. Informationen zur Onlinemigration finden Sie unter [Onlinemigration von SQL Server zu Azure SQL-Datenbank mit DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Laden Sie [SQL Server 2016 oder höher](https://www.microsoft.com/sql-server/sql-server-downloads) herunter, und installieren Sie es.
- Aktivieren Sie das TCP/IP-Protokoll (dieses wird während der SQL Server Express-Installation standardmäßig deaktiviert), indem Sie die Anweisungen im Artikel [Aktivieren oder Deaktivieren eines Servernetzwerkprotokolls](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) befolgen.
- Erstellen Sie gemäß der Anleitung im Artikel [Schnellstart: Erstellen einer Azure SQL-Einzeldatenbank](../azure-sql/database/single-database-create-quickstart.md) eine Datenbank in Azure SQL-Datenbank.

    > [!NOTE]
    > Falls Sie SQL Server Integration Services (SSIS) verwenden und die Katalogdatenbank für Ihre SSIS-Projekte/-Pakete (SSISDB) von SQL Server zu Azure SQL-Datenbank migrieren möchten, wird die Ziel-SSISDB automatisch für Sie erstellt und verwaltet, wenn Sie SSIS in Azure Data Factory (ADF) bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).
  
- Laden Sie den [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595), Version 3.3 oder höher, herunter, und installieren Sie ihn.
- Erstellen Sie ein virtuelles Microsoft Azure-Netzwerk für Azure Database Migration Service mithilfe des Azure Resource Manager-Bereitstellungsmodells, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > - Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > - Speicherendpunkt
    > - Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.
    >
    >Wenn Sie keine Site-to-Site-Konnektivität zwischen dem lokalen Netzwerk und Azure haben oder wenn die Bandbreite der Site-to-Site-Konnektivität begrenzt ist, sollten Sie die Verwendung von Azure Database Migration Service im Hybridmodus (Vorschauversion) in Betracht ziehen. Der Hybridmodus nutzt einen lokalen Migrationsworker zusammen mit einer Instanz von Azure Database Migration Service, die in der Cloud ausgeführt wird. Informationen zum Erstellen einer Instanz von Azure Database Migration Service im Hybridmodus finden Sie im Artikel [Erstellen einer Instanz von Azure Database Migration Service im Hybridmodus mit dem Azure-Portal](./quickstart-create-data-migration-service-portal.md).

- Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des virtuellen Netzwerks nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung für virtuelle Azure-Netzwerke finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Konfigurieren Sie Ihre [Windows-Firewall für Datenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433). Wenn die Standardinstanz an einem anderen Port lauscht, fügen Sie diesen der Firewall hinzu.
- Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
- Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, um Azure Database Migration Service den Zugriff auf die Quelldatenbanken für die Migration zu ermöglichen.
- Erstellen Sie für Azure SQL-Datenbank eine IP-[Firewallregel](../azure-sql/database/firewall-configure.md) auf Serverebene, um Azure Database Migration Service den Zugriff auf die Zieldatenbanken zu ermöglichen. Geben Sie den Subnetzbereich des für Azure Database Migration Service verwendeten virtuellen Netzwerks an.
- Stellen Sie sicher, dass die für die Verbindung mit der SQL Server-Quellinstanz verwendeten Anmeldeinformationen über [CONTROL SERVER](/sql/t-sql/statements/grant-server-permissions-transact-sql)-Berechtigungen verfügen.
- Stellen Sie sicher, dass die für die Verbindungsherstellung mit der Azure SQL-Datenbank-Zielinstanz verwendeten Anmeldeinformationen über die Berechtigung „CONTROL DATABASE“ für die Zieldatenbanken verfügen.

## <a name="assess-your-on-premises-database"></a>Bewerten Ihrer lokalen Datenbank

Damit Sie Daten von einer SQL Server-Instanz zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank migrieren können, müssen Sie die SQL Server-Datenbank auf Probleme untersuchen, die eine Migration verhindern könnten. Führen Sie im Datenmigrations-Assistenten, Version 3.3 oder höher, die im Artikel [Durchführen einer SQL Server-Migrationsbewertung](/sql/dma/dma-assesssqlonprem) beschriebenen Schritte aus, um die Bewertung der lokalen Datenbank abzuschließen. Hier sehen Sie eine Zusammenfassung der erforderlichen Schritte:

1. Wählen Sie im Data Migration Assistant das Symbol „New (+)“ (Neu), und wählen Sie dann den Projekttyp **Assessment** (Bewertung) aus.
2. Geben Sie einen Projektnamen im Textfeld **Source server type** (Quellservertyp) an, wählen Sie **SQL Server**, wählen Sie dann im Textfeld **Target server type** (Zielservertyp) die Option **Azure SQL Database** (Azure SQL-Datenbank) und anschließend **Create** (Erstellen), um das Projekt zu erstellen.

    Bei der Bewertung der SQL Server-Quelldatenbank für die Migration zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank können Sie einen oder beide der folgenden Bewertungsberichtstypen auswählen:

   - Check database compatibility (Datenbankkompatibilität prüfen)
   - Check feature parity (Featureparität prüfen)

    Beide Berichtsarten sind standardmäßig ausgewählt.

3. Wählen Sie im Data Migration Assistant im Bildschirm **Options** (Optionen) die Schaltfläche **Weiter**.
4. Geben Sie im Bildschirm **Select sources** (Quellen auswählen) im Dialogfeld **Connect to a server** (Verbindung mit einem Server herstellen) die Verbindungsdetails für SQL Server an, und wählen Sie dann **Connect** (Verbinden).
5. Wählen Sie im Dialogfeld **Quellen hinzufügen** den Eintrag **AdventureWorks2012** und dann nacheinander **Hinzufügen** und **Bewertung starten** aus.

    > [!NOTE]
    > Bei Verwendung von SSIS wird die Bewertung der Quell-SSISDB derzeit von DMA nicht unterstützt. SSIS-Projekte/-Pakete werden jedoch bewertet/überprüft, wenn sie in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitgestellt werden. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

    Nach Abschluss der Bewertung werden die Ergebnisse wie in der folgenden Abbildung angezeigt:

    ![Bewerten der Datenmigration](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Für Datenbanken in Azure SQL-Datenbank ermitteln die Bewertungen sowohl Probleme mit der Featureparität als auch Probleme, die eine Migration verhindern können, wenn eine Bereitstellung in einer Einzel- oder Pooldatenbank vorgenommen wird.

    - Die Kategorie **SQL Server-Featureparität** bietet eine umfassende Reihe von Empfehlungen, in Azure verfügbaren alternativen Herangehensweisen sowie Schritte zur Risikominimierung, sodass Sie den Aufwand bei Ihren Migrationsprojekten einplanen können.
    - In der Kategorie **Kompatibilitätsprobleme** sind teilweise oder nicht unterstützte Features angegeben, die zu Kompatibilitätsproblemen führen können, durch die eine Migration der SQL Server-Datenbank(en) zu Azure SQL-Datenbank ggf. verhindert wird. Zudem werden Empfehlungen bereitgestellt, mit deren Hilfe Sie diese Probleme behandeln können.

6. Prüfen Sie die Bewertungsergebnisse auf die oben genannten Probleme, indem Sie die jeweiligen Optionen auswählen.

## <a name="migrate-the-sample-schema"></a>Migrieren des Beispielschemas

Nachdem Sie sich mit der Bewertung vertraut gemacht und sich davon überzeugt haben, dass die ausgewählte Datenbank ein guter Kandidat für die Migration zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank ist, verwenden Sie DMA zum Migrieren des Schemas zu Azure SQL-Datenbank.

> [!NOTE]
> Bevor Sie ein Migrationsprojekt im Data Migration Assistant erstellen, vergewissern Sie sich, dass Sie bereits eine Datenbank in Azure bereitgestellt haben, wie in den Voraussetzungen erwähnt. Zum Zweck dieses Tutorials lautet der Name der Azure SQL-Datenbank **AdventureWorksAzure**. Sie können sie jedoch nach Bedarf umbenennen.

> [!IMPORTANT]
> Bei Verwendung von SSIS wird die Migration der Quell-SSISDB derzeit von DMA nicht unterstützt. Sie können jedoch Ihre SSIS-Projekte/-Pakete in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

Führen Sie zur Migration des **AdventureWorks2012**-Schemas zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank die folgenden Schritte aus:

1. Wählen Sie im Data Migration Assistant das Symbol „Neu (+)“ aus, und wählen Sie dann unter **Projekttyp** die Option **Migration** aus.
2. Geben Sie einen Projektnamen im Textfeld **Source server type** (Quellservertyp) an, wählen Sie **SQL Server**, und wählen Sie dann im Textfeld **Target server type** (Zielservertyp) die Option **Azure SQL Database** (Azure SQL-Datenbank).
3. Wählen Sie unter **Migration Scope** (Migrationsumfang) die Option **Schema only** (Nur Schema).

    Nachdem Sie die vorherigen Schritte durchgeführt haben, wird die Data Migration Assistant-Oberfläche wie in der folgenden Abbildung angezeigt:

    ![Erstellen eines Data Migration Assistant-Projekts](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.
5. Geben Sie im Data Migration Assistant die Quellverbindungsdetails für Ihre SQL Server-Instanz an, wählen Sie **Connect** (Verbinden), und wählen Sie dann die Datenbank **AdventureWorks2012** aus.

    ![Data Migration Assistant-Quellverbindungsdetails](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Wählen Sie **Weiter** aus. Geben Sie unter **Verbindung mit Zielserver herstellen** die Zielverbindungsdetails für die Azure SQL-Datenbank an, wählen Sie **Verbinden**, und wählen Sie dann die Datenbank **AdventureWorksAzure** aus, die Sie zuvor in Azure SQL-Datenbank bereitgestellt hatten.

    ![Data Migration Assistant-Zielverbindungsdetails](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Wählen Sie **Weiter**, um zum Bildschirm **Select objects** (Objekte auswählen) zu gelangen. Hier können Sie die Schemaobjekte in der Datenbank **AdventureWorks2012** angeben, die in Azure SQL-Datenbank bereitgestellt werden sollen.

    Standardmäßig sind alle Objekte ausgewählt.

    ![Generieren von SQL-Skripts](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Wählen Sie **Generate SQL script** (SQL-Skript generieren) aus, um die SQL-Skripts zu erstellen, und prüfen Sie die Skripts dann auf Fehler.

    ![Schemaskript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Wählen Sie **Deploy schema** (Schema bereitstellen) aus, um das Schema für Azure SQL-Datenbank bereitzustellen, und prüfen Sie den Zielserver anschließend auf Anomalien.

    ![Bereitstellen des Schemas](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an. Suchen Sie nach **Abonnements**, und wählen Sie diese Option aus.

   ![Abonnements im Portal anzeigen](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie dann **Registrieren** für **Microsoft.DataMigration** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Erstellen einer Instanz

1. Wählen Sie im Menü des Azure-Portals oder auf der **Startseite** die Option **Ressource erstellen** aus. Suchen Sie **Azure Database Migration Service**, und wählen Sie ihn aus.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Standort aus, an dem Sie die Azure Database Migration Service-Instanz erstellen möchten.

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie ein neues.

    Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die Azure SQL-Datenbank-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

6. Wählen Sie einen Tarif.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Konfigurieren der Einstellungen einer Azure Database Migration Service-Instanz](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem der Dienst erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Menü des Azure-Portals **Alle Dienste** aus. Suchen Sie **Azure Database Migration Services**, und wählen Sie sie aus.

     ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Services** die von Ihnen erstellte Azure Database Migration Service-Instanz aus.

3. Wählen Sie **Neues Migrationsprojekt** aus.

     ![Suchen Ihrer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Geben Sie im Bildschirm **Neues Migrationsprojekt** einen Projektnamen ein, und wählen Sie im Textfeld **Quellservertyp** die Option **SQL Server** und im Textfeld **Zielservertyp** die Option **Azure SQL-Datenbank-Instanz** aus. Wählen Sie außerdem für **Aktivitätstyp auswählen** die Option **Offlinedatenmigration** aus.

    ![Erstellen eines Database Migration Service-Projekts](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen und die Migrationsaktivität auszuführen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Migrationsquelldetail** die Verbindungsdetails für die SQL Server-Quellinstanz an.

    Stellen Sie sicher, dass Sie einen vollständig qualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) für den SQL Server-Quellinstanznamen verwenden. Sie können auch die IP-Adresse für Situationen verwenden, in denen DNS-Namensauflösung nicht möglich ist.

2. Wenn Sie auf Ihrem Quellserver kein vertrauenswürdiges Zertifikat installiert haben, aktivieren Sie das Kontrollkästchen **Serverzertifikat vertrauen**.

    Wenn ein vertrauenswürdiges Zertifikat nicht installiert ist, erzeugt SQL Server beim Starten der Instanz ein selbstsigniertes Zertifikat. Dieses Zertifikat wird zum Verschlüsseln der Anmeldeinformationen für Clientverbindungen verwendet.

    > [!CAUTION]
    > TLS-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind anfällig für Man-in-the-Middle-Angriffe. In einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, sollten Sie sich nicht auf TLS mit Verwendung selbstsignierter Zertifikate verlassen.

   ![Quellendetails](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Bei Verwendung von SSIS wird die Migration der Quell-SSISDB derzeit von DMS nicht unterstützt. Sie können jedoch Ihre SSIS-Projekte/-Pakete in der von Azure SQL-Datenbank gehosteten Ziel-SSISDB neu bereitstellen. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Klicken Sie auf **Speichern**, und geben Sie dann auf dem Bildschirm **Zieldetails für die Migration** die Verbindungsdetails für die gewünschte Azure SQL-Datenbank an. Dabei handelt es sich um die zuvor bereitgestellte Azure SQL-Datenbank, für die das **AdventureWorks2012**-Schema über den Datenmigrations-Assistenten bereitgestellt wurde.

    ![Auswählen des Ziels](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Klicken Sie auf **Speichern**, und ordnen Sie dann auf dem Bildschirm **Den Zieldatenbanken zuordnen** die Quell- und die Zieldatenbank für die Migration zu.

    Wenn die Zieldatenbank denselben Datenbanknamen wie die Quelldatenbank enthält, wählt Azure Database Migration Service die Zieldatenbank standardmäßig aus.

    ![Zuordnen zu Zieldatenbanken](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Wählen Sie **Save** (Speichern) im Bildschirm **Select tables** (Tabellen auswählen) aus, erweitern Sie die Tabellenauflistung, und prüfen Sie die Liste betroffener Felder.

    Azure Database Migration Service wählt automatisch alle leeren Quelltabellen aus, die in der Zielinstanz von Azure SQL-Datenbank vorhanden sind. Wenn Sie Tabellen, die bereits Daten enthalten, erneut migrieren möchten, müssen Sie die Tabellen auf diesem Blatt explizit auswählen.

    ![Tabellen auswählen](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Wählen Sie **Save** (Speichern) im Bildschirm **Migration summary** (Migrationszusammenfassung) aus, und geben Sie im Textfeld **Activity name** (Aktivitätsname) einen Namen für die Migrationsaktivität an.

5. Erweitern Sie den Abschnitt **Überprüfungsoption**, um den Bildschirm **Überprüfungsoption auswählen** anzuzeigen. Geben Sie an, ob für die migrierten Datenbanken ein **Schemavergleich** sowie eine Überprüfung auf **Datenkonsistenz** und **Abfragerichtigkeit** durchgeführt werden sollen.

    ![Auswählen der Validierungsoption](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Wählen Sie **Save** (Speichern), und prüfen Sie die Zusammenfassung, um sicherzustellen, dass die Quell- und die Zieldetails Ihren vorherigen Angaben entsprechen.

    ![Migrationszusammenfassung](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Ausführen der Migration

- Wählen Sie **Migration ausführen** aus.

    Das Fenster „Migrationsaktivität“ wird angezeigt, und der **Status** der Aktivität lautet **Ausstehend**.

    ![Aktivitätsstatus](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Überwachen der Migration

1. Klicken Sie auf dem Bildschirm „Migrationsaktivität“ auf **Aktualisieren**, um die Anzeige zu aktualisieren, bis der **Status** der Migration **Abgeschlossen** lautet.

    ![Aktivitätsstatus „Abgeschlossen“](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Wählen Sie nach Abschluss der Migration die Option **Bericht herunterladen** aus, um einen Bericht abzurufen, in dem die Details zum Migrationsprozess aufgeführt werden.

3. Überprüfen Sie die Zieldatenbank(en) in der Azure SQL-Zieldatenbank.

### <a name="additional-resources"></a>Zusätzliche Ressourcen

- [SQL-Migration mit Azure Data Migration Service](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587) – praktische Übungseinheit.
- Informationen zu bekannten Problemen und Einschränkungen beim Ausführen der Onlinemigration zu Azure SQL-Datenbank finden Sie im Artikel [Bekannte Probleme/Migrationseinschränkungen bei Onlinemigrationen zu Azure SQL-Datenbank](known-issues-azure-sql-online.md).
- Informationen zu Azure Database Migration Service finden Sie im Artikel [Was ist Azure Database Migration Service?](./dms-overview.md).
- Informationen zu Azure SQL-Datenbank finden Sie im Artikel [Der Azure SQL-Datenbank-Dienst](../azure-sql/database/sql-database-paas-overview.md).