---
title: 'Tutorial: Onlinemigration von SQL Server zu SQL Managed Instance'
titleSuffix: Azure Database Migration Service
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von SQL Server zu einer Instanz von Azure SQL Managed Instance durchführen.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 08/04/2020
ms.openlocfilehash: 0041779abcd97901fb9315125a287a1d45cff133
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350108"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-online-using-dms"></a>Tutorial: Onlinemigration von SQL Server zu Azure SQL Managed Instance mithilfe von DMS

Mit Azure Database Migration Service können Sie die Datenbanken von einer SQL Server-Instanz mit minimaler Downtime zu [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) migrieren. Informationen zu weiteren Methoden, die einen gewissen manuellen Aufwand erfordern, finden Sie im Artikel [Migration einer SQL Server-Instanz zu Azure SQL Managed Instance](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

In diesem Tutorial migrieren Sie mithilfe von Azure Database Migration Service die Datenbank **Adventureworks2012** mit minimaler Downtime von einer lokalen SQL Server-Instanz zu einer Instanz von SQL Managed Instance.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts und Starten der Onlinemigration mithilfe von Azure Database Migration Service
> * Überwachen der Migration
> * Führen Sie die Migrationsübernahme durch, wenn Sie bereit sind.

> [!IMPORTANT]
> Für die Onlinemigration von SQL Server zu SQL Managed Instance mithilfe von Azure Database Migration Service müssen Sie die vollständige Datenbanksicherung und nachfolgende Protokollsicherungen auf der SMB-Netzwerkfreigabe bereitstellen, die der Dienst zum Migrieren Ihrer Datenbanken verwenden kann. Azure Database Migration Service initiiert keine Sicherungen, sondern verwendet für die Migration vorhandene Sicherungen, die Sie im Rahmen Ihres Notfallwiederherstellungsplans möglicherweise bereits besitzen.
> Achten Sie darauf, [Sicherungen mit der Option WITH CHECKSUM](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?preserve-view=true&view=sql-server-2017) zu erstellen. Stellen Sie außerdem sicher, dass einem einzelnen Sicherungsmedium nicht mehrere Sicherungen (d. h. vollständige Sicherungen und Protokollfragmentsicherungen) hinzugefügt werden. Speichern Sie jede Sicherung in einer separaten Sicherungsdatei. Des Weiteren können Sie komprimierte Sicherungen verwenden, um die Wahrscheinlichkeit von potenziellen Problemen bei der Migration großer Sicherungen zu verringern.

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

> [!IMPORTANT]
> Verkürzen Sie die Dauer des Onlinemigrationsvorgangs so stark wie möglich, um die Gefahr von Unterbrechungen zu minimieren, die durch die Neukonfiguration von Instanzen oder geplante Wartung bedingt sind. Im Fall eines solchen Ereignisses muss der Migrationsprozess von Anfang an neu beginnen. Bei einer geplanten Wartung gibt es eine Toleranzperiode von 36 Stunden, bevor der Migrationsprozess neu gestartet wird.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird die Onlinemigration von SQL Server zu einer Instanz von SQL Managed Instance beschrieben. Informationen zur Offlinemigration finden Sie unter [Tutorial: Migrieren von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von DMS (offline)](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Erstellen Sie ein virtuelles Microsoft Azure-Netzwerk für Azure Database Migration Service mithilfe des Azure Resource Manager-Bereitstellungsmodells, das Site-to-Site-Konnektivität für Ihre lokalen Quellserver über [ExpressRoute](../expressroute/expressroute-introduction.md) oder über [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) bereitstellt. [Erfahren Sie mehr über Netzwerktopologien für SQL Managed Instance-Migrationen mithilfe von Azure Database Migration Service](./resource-network-topologies.md). Weitere Informationen zum Erstellen eines virtuellen Netzwerks finden Sie in der [Dokumentation zu Virtual Network](../virtual-network/index.yml) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des Setups des virtuellen Netzwerks die folgenden [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, weil Azure Database Migration Service über keine Internetverbindung verfügt.
    >
    >Wenn Sie keine Site-to-Site-Konnektivität zwischen dem lokalen Netzwerk und Azure haben oder wenn die Bandbreite der Site-to-Site-Konnektivität begrenzt ist, sollten Sie die Verwendung von Azure Database Migration Service im Hybridmodus (Vorschauversion) in Betracht ziehen. Der Hybridmodus nutzt einen lokalen Migrationsworker zusammen mit einer Instanz von Azure Database Migration Service, die in der Cloud ausgeführt wird. Informationen zum Erstellen einer Instanz von Azure Database Migration Service im Hybridmodus finden Sie im Artikel [Erstellen einer Instanz von Azure Database Migration Service im Hybridmodus mit dem Azure-Portal](./quickstart-create-data-migration-service-portal.md).

    > [!IMPORTANT]
    > In Bezug auf das Speicherkonto, das im Rahmen der Migration verwendet wird, müssen Sie einen der folgenden Schritte ausführen:
    > * Festlegen, dass alle Netzwerke auf das Speicherkonto zugreifen können
    > * [Subnetzdelegierung](../virtual-network/manage-subnet-delegation.md) für MI-Subnetz aktivieren und die Firewallregeln für das Speicherkonto aktualisieren, um dieses Subnetz zuzulassen

* Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des virtuellen Netzwerks nicht die folgenden Ports für ausgehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem virtuellen Netzwerk finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Konfigurieren Sie Ihre [Windows-Firewall für Quelldatenbank-Engine-Zugriff](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433). Wenn die Standardinstanz an einem anderen Port lauscht, fügen Sie diesen der Firewall hinzu.
* Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, damit Azure Database Migration Service auf die Quelldatenbanken für die Migration sowie auf Dateien über SMB-Port 445 zugreifen kann.
* Eine Anleitung zum Erstellen einer verwalteten SQL-Instanz finden Sie im Artikel [Erstellen einer verwalteten SQL-Instanz im Azure-Portal](../azure-sql/managed-instance/instance-create-quickstart.md).
* Stellen Sie sicher, dass die zum Herstellen einer Verbindung zwischen der SQL Server-Quellinstanz und der SQL Managed Instance-Zielinstanz verwendeten Anmeldungen Mitglieder der sysadmin-Serverrolle sind.
* Stellen Sie eine SMB-Netzwerkfreigabe bereit, die alle vollständigen Datenbanksicherungsdateien Ihrer Datenbank sowie nachfolgende Sicherungsdateien des Transaktionsprotokolls enthält, die Azure Database Migration Service für die Datenbankmigration verwenden kann.
* Stellen Sie sicher, dass das Dienstkonto, unter dem die SQL Server-Quellinstanz ausgeführt wird, über Schreibberechtigungen für die Netzwerkfreigabe verfügt, die Sie erstellt haben, und dass das Computerkonto für den Quellserver über Lese-/Schreibzugriff auf dieselbe Freigabe verfügt.
* Notieren Sie sich einen Windows-Benutzer (mit Kennwort), der Vollzugriff auf die von Ihnen zuvor erstellte Netzwerkfreigabe hat. Azure Database Migration Service verwendet die Benutzeranmeldeinformationen, um die Sicherungsdateien für Wiederherstellungsvorgänge in den Azure Storage-Container hochzuladen.
* Erstellen Sie eine Azure Active Directory-Anwendungs-ID, die den Anwendungs-ID-Schlüssel generiert, den Azure Database Migration Service für die Verbindungsherstellung mit dem Ziel Azure Database Managed Instance und Azure Storage-Container verwenden kann. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md).

  > [!NOTE]
  > Azure Database Migration Service erfordert die Berechtigung „Mitwirkender“ für das Abonnement für die angegebene Anwendungs-ID. Alternativ können Sie auch benutzerdefinierte Rollen erstellen, die die speziellen Berechtigungen gewähren, die Azure Database Migration Service erfordert. Eine Schrittanleitung zur Verwendung benutzerdefinierter Rollen finden Sie im Artikel [Benutzerdefinierte Rollen für die Onlinemigration von SQL Server zu SQL Managed Instance](./resource-custom-roles-sql-db-managed-instance.md).

* Erstellen Sie oder notieren Sie sich die **Standardleistungsstufe** (Azure Storage-Konto), die es dem DMS-Dienst ermöglicht, die Datenbanksicherungsdateien hochzuladen und für die Datenbankmigration zu verwenden.  Achten Sie darauf, das Azure Storage-Konto in der gleichen Region zu erstellen, in der auch die Azure Database Migration Service-Instanz erstellt wird.

  > [!NOTE]
  > Beim Migrieren einer Datenbank, die durch [Transparent Data Encryption](../azure-sql/database/transparent-data-encryption-tde-overview.md) geschützt ist, zu einer verwalteten Instanz mithilfe der Onlinemigration muss das entsprechende Zertifikat von der lokalen oder der SQL Server-Instanz auf der Azure-VM vor der Datenwiederherstellung migriert werden. Eine ausführliche Anleitung finden Sie unter [Migrieren des Zertifikats einer durch TDE geschützten Datenbank zu einer verwalteten Azure SQL-Instanz](../azure-sql/database/transparent-data-encryption-tde-overview.md).

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

    ![Abonnements im Portal anzeigen](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie dann auf **Ressourcenanbieter**.

    ![Ressourcenanbieter anzeigen](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Suchen Sie nach „Migration“, und wählen Sie rechts neben **Microsoft.DataMigration** die Option **Registrieren** aus.

    ![Registrieren des Ressourcenanbieters](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Erstellen einer Instanz von Azure Database Migration Service

1. Wählen Sie im Azure-Portal die Option **+ Ressource erstellen**, suchen Sie nach **Azure Database Migration Service**, und wählen Sie anschließend **Azure Database Migration Service** aus der Dropdownliste aus.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Wählen Sie auf dem Bildschirm **Azure Database Migration Service** die Schaltfläche **Erstellen** aus.

    ![Erstellen einer Instanz von Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Geben Sie auf dem Bildschirm **Migrationsdienst erstellen** einen Namen für den Dienst, das Abonnement und eine neue oder vorhandene Ressourcengruppe an.

4. Wählen Sie den Speicherort aus, an dem Sie die DMS-Instanz erstellen möchten.

5. Wählen Sie ein vorhandenes virtuelles Netzwerk aus, oder erstellen Sie eins.

    Das virtuelle Netzwerk erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die SQL Managed Instance-Zielinstanz.

    Weitere Informationen zum Erstellen eines virtuellen Netzwerks im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](../virtual-network/quick-create-portal.md).

    Weitere Einzelheiten finden Sie im Artikel [Netzwerktopologien für SQL Managed Instance-Migrationen mithilfe von Azure Database Migration Service](./resource-network-topologies.md).

6. Wählen Sie eine SKU aus dem Premium-Tarif aus.

    > [!NOTE]
    > Onlinemigrationen werden nur bei Verwendung des Premium-Tarifs unterstützt.

    Weitere Informationen zu Kosten und Tarifen finden Sie in der [Preisübersicht](https://aka.ms/dms-pricing).

    ![Erstellen eines DMS-Diensts](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Wählen Sie **Erstellen**, um den Dienst zu erstellen.

## <a name="create-a-migration-project"></a>Erstellen eines Migrationsprojekts

Nachdem eine Instanz des Diensts erstellt wurde, suchen Sie diesen im Azure-Portal, öffnen Sie ihn, und erstellen Sie anschließend ein neues Migrationsprojekt.

1. Wählen Sie im Azure-Portal **Alle Dienste**, suchen Sie nach Azure Database Migration Service, und wählen Sie dann **Azure Database Migration Service** aus.

    ![Suchen aller Instanzen von Azure Database Migration Service](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Suchen Sie im Bildschirm **Azure Database Migration Service** nach dem Namen der von Ihnen erstellten Instanz, und wählen Sie anschließend die Instanz aus.

3. Wählen Sie **+ Neues Migrationsprojekt** aus.

4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen ein, und wählen Sie im Textfeld **Quellservertyp** die Option **SQL Server** und im Textfeld **Zielservertyp** die Option **Verwaltete Azure SQL-Instanz** aus. Wählen Sie außerdem für **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

   ![Erstellen eines Azure Database Migration Service-Projekts](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Migrationsquelldetail** die Verbindungsdetails für die SQL Server-Quellinstanz an.

2. Falls Sie auf Ihrem Server kein vertrauenswürdiges Zertifikat installiert haben, aktivieren Sie das Kontrollkästchen **Serverzertifikat vertrauen**.

    Wenn kein vertrauenswürdiges Zertifikat installiert ist, generiert SQL Server beim Starten der Instanz ein selbstsigniertes Zertifikat. Dieses Zertifikat wird zum Verschlüsseln der Anmeldeinformationen für Clientverbindungen verwendet.

    > [!CAUTION]
    > TLS-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind anfällig für Man-in-the-Middle-Angriffe. In einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, sollten Sie sich nicht auf TLS mit Verwendung selbstsignierter Zertifikate verlassen.

   ![Quellendetails](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Wählen Sie **Speichern** aus.

4. Wählen Sie auf dem Bildschirm **Quelldatenbanken auswählen** die Datenbank **Adventureworks2012** für die Migration aus.

   ![Auswählen von Quelldatenbanken](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Wenn Sie SQL Server Integration Services (SSIS) verwenden, bietet DMS aktuell keine Unterstützung für die Migration der Katalogdatenbank für Ihre SSIS-Projekte/-Pakete (SSISDB) von SQL Server zu SQL Managed Instance. Sie können SSIS jedoch in Azure Data Factory (ADF) bereitstellen und Ihre SSIS-Projekte/-Pakete in der Ziel-SSISDB neu bereitstellen, die von SQL Managed Instance gehostet wird. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](./how-to-migrate-ssis-packages.md).

5. Wählen Sie **Speichern** aus.

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie auf dem Bildschirm **Zieldetails für die Migration** die **Anwendungs-ID** und den **Schlüssel** an, die bzw. den die DMS-Instanz für die Verbindungsherstellung mit der Zielinstanz von SQL Managed Instance und dem Azure Storage-Konto verwenden kann.

    Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md).

2. Wählen Sie das **Abonnement** aus, das die Zielinstanz von SQL Managed Instance enthält, und wählen Sie dann die Zielinstanz aus.

    Falls Sie die verwaltete SQL-Instanz noch nicht bereitgestellt haben, wählen Sie den [Link](../azure-sql/managed-instance/instance-create-quickstart.md) aus, um Unterstützung bei der Bereitstellung der Instanz zu erhalten. Wenn die Instanz von SQL Managed Instance bereit ist, kehren Sie zu diesem spezifischen Projekt zurück, um die Migration durchzuführen.

3. Geben Sie den **SQL-Benutzer** und das **Kennwort** für die Verbindungsherstellung mit SQL Managed Instance an.

    ![Auswählen des Ziels](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Wählen Sie **Speichern** aus.

## <a name="select-source-databases"></a>Auswählen von Quelldatenbanken

1. Wählen Sie auf dem Bildschirm **Quelldatenbanken auswählen** die Quelldatenbank aus, die Sie migrieren möchten.

    ![Auswählen von Quelldatenbanken](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Wählen Sie **Speichern** aus.

## <a name="configure-migration-settings"></a>Konfigurieren von Migrationseinstellungen

1. Geben Sie auf dem Bildschirm **Migrationseinstellungen konfigurieren** die folgenden Details an:

    | | |
    |--------|---------|
    |**SMB-Netzwerkadressfreigabe** | Die lokale SMB-Netzwerkfreigabe oder Azure-Dateifreigabe, die alle vollständigen Datenbanksicherungsdateien sowie Sicherungsdateien des Transaktionsprotokolls enthält, die Azure Database Migration Service für die Migration verwenden kann. Das Dienstkonto, unter dem die SQL Server-Quellinstanz ausgeführt wird, muss über Lese-/Schreibberechtigungen für diese Netzwerkfreigabe verfügen. Geben Sie in der Netzwerkfreigabe einen FQDN oder IP-Adressen des Servers an, wie z.B. „\\\servername.domainname.com\backupfolder\\“ oder „\IP address\backupfolder“. Zur Verbesserung der Leistung wird empfohlen, für jede zu migrierende Datenbank einen eigenen Ordner zu verwenden. Sie können den Dateifreigabepfad auf Datenbankebene mithilfe der Option **Erweiterte Einstellungen** angeben. |
    |**Benutzername** | Achten Sie darauf, dass der Windows-Benutzer eine Berechtigung für Vollzugriff auf die von Ihnen angegebene Netzwerkfreigabe besitzt. Azure Database Migration Service nimmt die Anmeldeinformationen des Benutzers an, um die Sicherungsdateien für Wiederherstellungsvorgänge in den Azure Storage-Container hochzuladen. Verwenden Sie bei der Nutzung der Azure-Dateifreigabe als Benutzernamen den Speicherkontonamen, dem „AZURE\“ vorangestellt ist. |
    |**Kennwort** | Kennwort für den Benutzer Verwenden Sie bei Nutzung der Azure-Dateifreigabe einen Speicherkontoschlüssel als Kennwort. |
    |**Abonnement des Azure Storage-Kontos** | Wählen Sie das Abonnement aus, das das Azure Storage-Konto enthält. |
    |**Azure Storage-Konto** | Wählen Sie das Azure Storage-Konto aus, in das DMS die Sicherungsdateien aus der SMB-Netzwerkfreigabe hochladen und das von DMS für die Datenbankmigration verwendet werden kann.  Es empfiehlt sich, das Speicherkonto in der gleichen Region auszuwählen, in der sich auch der DMS-Dienst befindet, um beim Hochladen der Dateien eine optimale Leistung zu erzielen. |

    ![Konfigurieren von Migrationseinstellungen](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Wird in Azure Database Migration Service der Fehler „Systemfehler 53“ oder „Systemfehler 57“ anzeigt, rührt das unter Umständen daher, dass Azure Database Migration Service nicht auf die Azure-Dateifreigabe zugreifen kann. Sollte einer dieser Fehler auftreten, gewähren Sie Speicherkontozugriff über das virtuelle Netzwerk. Eine entsprechende Anleitung finden Sie [hier](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Wenn die Loopback-Funktionalität aktiviert ist und sich die SQL Server-Quellinstanz und die Dateifreigabe auf demselben Computer befinden, kann die Quelle nicht mithilfe des FQDN auf die Dateifreigabe zugreifen. Um dieses Problem zu beheben, deaktivieren Sie die Loopback-Funktionalität anhand [dieser Anweisungen](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Wählen Sie **Speichern** aus.

## <a name="review-the-migration-summary"></a>Überprüfen der Migrationszusammenfassung

1. Geben Sie auf dem Bildschirm **Migrationszusammenfassung** im Textfeld **Aktivitätsname** einen Namen für die Migrationsaktivität an.

2. Überprüfen und verifizieren Sie die dem Migrationsprojekt zugeordneten Details.

    ![Zusammenfassung des Migrationsprojekts](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Ausführen und Überwachen der Migration

1. Wählen Sie **Migration ausführen** aus.

2. Wählen Sie auf dem Bildschirm zur Migrationsaktivität die Option **Aktualisieren** aus, um die Anzeige zu aktualisieren.

   ![Migrationsaktivität in Bearbeitung](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    Sie können die Datenbanken und Anmeldekategorien erweitern, um den Migrationsstatus des jeweiligen Serverobjekts zu überwachen.

   ![Status der Migrationsaktivität](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Durchführen der Migrationsübernahme

Nachdem die vollständige Datenbanksicherung für die Zielinstanz von SQL Managed Instance wiederhergestellt wurde, steht die Datenbank für die Durchführung einer Migrationsübernahme zur Verfügung.

1. Wenn Sie die Onlinedatenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

2. Beenden Sie den gesamten eingehenden Datenverkehr für Quelldatenbanken.

3. Verwenden Sie die [Sicherung des Protokollfragments], machen Sie die Sicherungsdatei auf der SMB-Netzwerkfreigabe verfügbar, und warten Sie, bis diese letzte Transaktionsprotokollsicherung wiederhergestellt wurde.

    Für **Ausstehende Änderungen** wird nun „0“ angezeigt.

4. Aktivieren Sie das Kontrollkästchen **Bestätigen**, und klicken Sie anschließend auf **Anwenden**.

    ![Vorbereiten des Abschlusses der Übernahme](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

    > [!IMPORTANT]
    > Nach der Übernahme kann die Verfügbarkeit von SQL Managed Instance mit nur der Dienstebene „Unternehmenskritisch“ erheblich länger dauern als mit „Universell“, da das Seeding von drei sekundären Replikaten für eine Always On-Verfügbarkeitsgruppe mit Hochverfügbarkeit ausgeführt werden muss. Die Dauer dieses Vorgangs hängt von der Größe der Daten ab. Weitere Informationen finden Sie unter [Dauer von Verwaltungsvorgängen](../azure-sql/managed-instance/management-operations-overview.md#duration).

5. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen Instanz von SQL Managed Instance her.

    ![Übernahme abgeschlossen](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial, in dem gezeigt wird, wie Sie eine Datenbank mit dem T-SQL-Befehl RESTORE zu SQL Managed Instance migrieren können, finden Sie unter [Wiederherstellen einer Sicherung in SQL Managed Instance mit dem restore-Befehl](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
* Informationen zu SQL Managed Instance finden Sie unter [Was ist SQL Managed Instance?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
* Informationen zum Verbinden von Apps mit SQL Managed Instance finden Sie unter [Verbinden von Anwendungen](../azure-sql/managed-instance/connect-application-instance.md).