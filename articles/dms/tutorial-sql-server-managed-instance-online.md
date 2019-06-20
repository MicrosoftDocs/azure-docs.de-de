---
title: 'Tutorial: Durchführen einer Onlinemigration von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von Azure Database Migration Service | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie mit Azure Database Migration Service eine Onlinemigration von einer lokalen SQL Server-Instanz zu einer verwalteten Azure SQL-Datenbank-Instanz durchführen.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/22/2019
ms.openlocfilehash: 1229ff3221deb49601dec3cd40b556ea367fc4c9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240702"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Tutorial: Migrieren von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von DMS (online)

Mit Azure Database Migration Service können Sie die Datenbanken aus einer lokalen SQL Server-Instanz mit minimaler Ausfallzeit zu einer [verwalteten Azure SQL-Datenbank-Instanz](../sql-database/sql-database-managed-instance.md) migrieren. Informationen zu weiteren Methoden, für die etwas manueller Aufwand erforderlich ist, finden Sie im Artikel [Migration einer SQL Server-Instanz zu einer verwalteten Azure SQL-Datenbank-Instanz](../sql-database/sql-database-managed-instance-migrate.md).

In diesem Tutorial migrieren Sie mithilfe von Azure Database Migration Service die Datenbank **Adventureworks2012** mit minimaler Ausfallzeit von einer lokalen SQL Server-Instanz zu einer verwalteten Azure SQL-Datenbank-Instanz.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
>
> * Erstellen einer Instanz von Azure Database Migration Service
> * Erstellen eines Migrationsprojekts und Starten der Onlinemigration mithilfe von Azure Database Migration Service
> * Überwachen der Migration
> * Übernehmen der Migration, wenn Sie dazu bereit sind

> [!IMPORTANT]
> Für die Onlinemigration von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von Azure Database Migration Service müssen Sie die vollständige Datenbanksicherung und nachfolgende Protokollsicherungen auf der SMB-Netzwerkfreigabe bereitstellen, die der Dienst zum Migrieren Ihrer Datenbanken verwenden kann. Azure Database Migration Service initiiert keine Sicherungen, sondern verwendet für die Migration vorhandene Sicherungen, die Sie im Rahmen Ihres Notfallwiederherstellungsplans möglicherweise bereits besitzen.
> Achten Sie darauf, [Sicherungen mit der Option WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017) zu erstellen. Stellen Sie außerdem sicher, dass einem einzelnen Sicherungsmedium nicht mehrere Sicherungen (d. h. vollständige Sicherungen und Protokollfragmentsicherungen) hinzugefügt werden. Speichern Sie jede Sicherung in einer separaten Sicherungsdatei.

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs.

> [!IMPORTANT]
> Für eine optimale Migration empfiehlt Microsoft die Erstellung einer Azure Database Migration Service-Instanz in derselben Azure-Region, in der sich auch die Zieldatenbank befindet. Die Verschiebung von Daten zwischen Regionen oder Geografien kann den Migrationsvorgang verlangsamen und Fehler verursachen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In diesem Artikel wird eine Onlinemigration von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz beschrieben. Informationen zur Offlinemigration finden Sie unter [Tutorial: Migrieren von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz mithilfe von DMS (offline)](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Erstellen Sie ein virtuelles Azure-Netzwerk (VNET) für Azure Database Migration Service, indem Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, das entweder über [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) oder über [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) Site-to-Site-Konnektivität für Ihre lokalen Quellserver bereitstellt. [Lernen Sie die Netzwerktopologien für Migrationen von verwalteten Azure SQL-Datenbank-Instanzen mithilfe von Azure Database Migration Service kennen.](https://aka.ms/dmsnetworkformi) Weitere Informationen zum Erstellen eines VNET finden Sie in der [Dokumentation zu Virtual Network](https://docs.microsoft.com/azure/virtual-network/) und insbesondere in den Schnellstartartikeln mit Schritt-für-Schritt-Anleitungen.

    > [!NOTE]
    > Fügen Sie bei Verwendung von ExpressRoute mit Netzwerkpeering zu Microsoft während des VNET-Setups die folgenden [Dienstendpunkte](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) zu dem Subnetz hinzu, in dem der Dienst bereitgestellt werden soll:
    >
    > * Zieldatenbankendpunkt (z. B. SQL-Endpunkt, Cosmos DB-Endpunkt usw.)
    > * Speicherendpunkt
    > * Service Bus-Endpunkt
    >
    > Diese Konfiguration ist erforderlich, da Azure Database Migration Service über keine Internetverbindung verfügt.

* Stellen Sie sicher, dass die Netzwerksicherheitsgruppen-Regeln des VNET nicht die folgenden Ports für eingehende Kommunikation in Azure Database Migration Service blockieren: 443, 53, 9354, 445, 12000. Ausführlichere Informationen zur NSG-Datenverkehrsfilterung in einem Azure-VNET finden Sie im Artikel [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Konfigurieren Sie Ihre [Windows-Firewall für Quelldatenbank-Engine-Zugriff](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öffnen Sie Ihre Windows-Firewall, damit Azure Database Migration Service auf die SQL Server-Quellinstanz zugreifen kann (standardmäßig TCP-Port 1433).
* Bei der Ausführung mehrerer benannter SQL Server-Instanzen mit dynamischen Ports empfiehlt es sich, den SQL-Browser-Dienst zu aktivieren und den Zugriff auf den UDP-Port 1434 durch Ihre Firewalls zuzulassen, sodass Azure Database Migration Service eine Verbindung mit einer benannten Instanz auf Ihrem Quellserver herstellen kann.
* Wenn Sie eine Firewallappliance vor Ihren Quelldatenbanken verwenden, müssen Sie möglicherweise Firewallregeln hinzufügen, damit Azure Database Migration Service auf die Quelldatenbanken für die Migration sowie auf Dateien über SMB-Port 445 zugreifen kann.
* Erstellen Sie eine verwaltete Azure SQL-Datenbank-Instanz, indem Sie die Anweisungen im Artikel [Erstellen einer verwalteten Azure SQL-Datenbank-Instanz im Azure-Portal](https://aka.ms/sqldbmi) befolgen.
* Stellen Sie sicher, dass die zum Herstellen einer Verbindung zwischen der SQL Server-Quellinstanz und der verwalteten Zielinstanz verwendeten Anmeldungen Mitglieder der Serverrolle „sysadmin“ sind.
* Stellen Sie eine SMB-Netzwerkfreigabe bereit, die alle vollständigen Datenbanksicherungsdateien Ihrer Datenbank sowie nachfolgende Sicherungsdateien des Transaktionsprotokolls enthält, die Azure Database Migration Service für die Datenbankmigration verwenden kann.
* Stellen Sie sicher, dass das Dienstkonto, unter dem die SQL Server-Quellinstanz ausgeführt wird, über Schreibberechtigungen für die Netzwerkfreigabe verfügt, die Sie erstellt haben, und dass das Computerkonto für den Quellserver über Lese-/Schreibzugriff auf dieselbe Freigabe verfügt.
* Notieren Sie sich einen Windows-Benutzer (mit Kennwort), der Vollzugriff auf die von Ihnen zuvor erstellte Netzwerkfreigabe hat. Azure Database Migration Service verwendet die Benutzeranmeldeinformationen, um die Sicherungsdateien für Wiederherstellungsvorgänge in den Azure Storage-Container hochzuladen.
* Erstellen Sie eine Azure Active Directory-Anwendungs-ID, die den Anwendungs-ID-Schlüssel generiert, den der DMS-Dienst für die Verbindungsherstellung mit der verwalteten Azure-Datenbank-Zielinstanz und mit dem Azure Storage-Container verwenden kann. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).
* Erstellen Sie oder notieren Sie sich die **Standardleistungsstufe** (Azure Storage-Konto), die es dem DMS-Dienst ermöglicht, die Datenbanksicherungsdateien hochzuladen und für die Datenbankmigration zu verwenden.  Achten Sie darauf, das Azure Storage-Konto in der gleichen Region zu erstellen, in der sich auch der erstellte DMS-Dienst befindet.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrieren des Ressourcenanbieters „Microsoft.DataMigration“

1. Melden Sie sich beim Azure-Portal an, und klicken Sie auf **Alle Dienste** und anschließend auf **Abonnements**.

    ![Abonnements im Portal anzeigen](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Wählen Sie das Abonnement aus, in dem Sie die Azure Database Migration Service-Instanz erstellen möchten, und klicken Sie auf **Ressourcenanbieter**.

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

5. Wählen Sie ein vorhandenes VNET aus, oder erstellen Sie ein VNET.

    Das VNET erteilt Azure Database Migration Service Zugriff auf die SQL Server-Quellinstanz und die verwaltete Azure SQL-Datenbank-Zielinstanz.

    Weitere Informationen zum Erstellen eines VNET im Azure-Portal finden Sie im Artikel [Erstellen eines virtuellen Netzwerks im Azure Portal](https://aka.ms/DMSVnet).

    Weitere Einzelheiten finden Sie im Artikel [Netzwerktopologien für Migrationen von verwalteten Azure SQL-Datenbank-Instanzen mithilfe von Azure Database Migration Service](https://aka.ms/dmsnetworkformi).

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

4. Geben Sie auf dem Bildschirm **Neues Migrationsprojekt** einen Projektnamen ein, und wählen Sie im Textfeld **Quellservertyp** die Option **SQL Server** und im Textfeld **Zielservertyp** die Option **Verwaltete Azure SQL-Datenbank-Instanz** aus. Wählen Sie außerdem für **Aktivitätstyp auswählen** die Option **Onlinedatenmigration** aus.

   ![Erstellen eines DMS-Projekts](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Klicken Sie auf **Aktivität erstellen und ausführen**, um das Projekt zu erstellen.

## <a name="specify-source-details"></a>Angeben von Quelldetails

1. Geben Sie im Bildschirm **Migrationsquelldetail** die Verbindungsdetails für die SQL Server-Quellinstanz an.

2. Falls Sie auf Ihrem Server kein vertrauenswürdiges Zertifikat installiert haben, aktivieren Sie das Kontrollkästchen **Serverzertifikat vertrauen**.

    Wenn kein vertrauenswürdiges Zertifikat installiert ist, generiert SQL Server beim Starten der Instanz ein selbstsigniertes Zertifikat. Dieses Zertifikat wird zum Verschlüsseln der Anmeldeinformationen für Clientverbindungen verwendet.

    > [!CAUTION]
    > SSL-Verbindungen, die mit einem selbstsignierten Zertifikat verschlüsselt sind, bieten keine hohe Sicherheit. Sie sind anfällig für Man-in-the-Middle-Angriffe. Verlassen Sie sich in einer Produktionsumgebung oder auf Servern, die mit dem Internet verbunden sind, nicht auf SSL-Verbindungen, die selbstsignierte Zertifikate verwenden.

   ![Quellendetails](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Wählen Sie **Speichern** aus.

4. Wählen Sie auf dem Bildschirm **Quelldatenbanken auswählen** die Datenbank **Adventureworks2012** für die Migration aus.

   ![Auswählen von Quelldatenbanken](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Wenn Sie SQL Server Integration Services (SSIS) verwenden, unterstützt DMS die Migration der Katalogdatenbank für Ihre SSIS-Projekte/-Pakete (SSISDB) von SQL Server zu einer verwalteten Azure SQL-Datenbank-Instanz derzeit nicht. Sie können SSIS jedoch in Azure Data Factory (ADF) bereitstellen und Ihre SSIS-Projekte/-Pakete in der Ziel-SSISDB erneut bereitstellen, die von der verwalteten Azure SQL-Datenbank-Instanz gehostet wird. Weitere Informationen zur Migration von SSIS-Paketen finden Sie im Artikel [Migrieren von SQL Server Integration Services-Paketen in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Wählen Sie **Speichern** aus.

## <a name="specify-target-details"></a>Angeben von Zieldetails

1. Geben Sie auf dem Bildschirm **Zieldetails für die Migration** die **Anwendungs-ID** und den **Schlüssel** an, die bzw. den die DMS-Instanz für die Verbindungsherstellung mit der verwalteten Azure SQL-Datenbank-Zielinstanz und dem Azure Storage-Konto verwenden kann.

    Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Wählen Sie das **Abonnement** aus, das die verwaltete Azure SQL-Datenbank-Zielinstanz enthält, und wählen Sie dann die Zielinstanz aus.

    Falls Sie die verwaltete Azure SQL-Datenbank-Instanz noch nicht bereitgestellt haben, klicken Sie auf den [Link](https://aka.ms/SQLDBMI), um Unterstützung bei der Bereitstellung der Instanz zu erhalten. Wenn die verwaltete Azure SQL-Datenbank-Instanz bereit ist, kehren Sie zu diesem spezifischen Projekt zurück, um die Migration auszuführen.

3. Geben Sie den **SQL-Benutzer** und das **Kennwort** für die Verbindungsherstellung mit der verwalteten Azure SQL-Datenbank-Instanz an.

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
    |**SMB-Netzwerkadressfreigabe** | Die lokale SMB-Netzwerkfreigabe, die alle vollständigen Datenbanksicherungsdateien sowie Sicherungsdateien des Transaktionsprotokolls enthält, die Azure Database Migration Service für die Migration verwenden kann. Das Dienstkonto, unter dem die SQL Server-Quellinstanz ausgeführt wird, muss über Lese-/Schreibberechtigungen für diese Netzwerkfreigabe verfügen. Geben Sie in der Netzwerkfreigabe einen FQDN oder IP-Adressen des Servers an, wie z.B. „\\\servername.domainname.com\backupfolder\\“ oder „\IP address\backupfolder“.|
    |**Benutzername** | Achten Sie darauf, dass der Windows-Benutzer eine Berechtigung für Vollzugriff auf die von Ihnen angegebene Netzwerkfreigabe besitzt. Azure Database Migration Service nimmt die Anmeldeinformationen des Benutzers an, um die Sicherungsdateien für Wiederherstellungsvorgänge in den Azure Storage-Container hochzuladen. |
    |**Kennwort** | Kennwort für den Benutzer |
    |**Abonnement des Azure Storage-Kontos** | Wählen Sie das Abonnement aus, das das Azure Storage-Konto enthält. |
    |**Azure Storage-Konto** | Wählen Sie das Azure Storage-Konto aus, in das DMS die Sicherungsdateien aus der SMB-Netzwerkfreigabe hochladen und das von DMS für die Datenbankmigration verwendet werden kann.  Es empfiehlt sich, das Speicherkonto in der gleichen Region auszuwählen, in der sich auch der DMS-Dienst befindet, um beim Hochladen der Dateien eine optimale Leistung zu erzielen. |

    ![Konfigurieren von Migrationseinstellungen](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

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

   ![Migrationsaktivität in Bearbeitung](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Durchführen der Migrationsübernahme

Nachdem die vollständige Datenbanksicherung in der verwalteten Azure SQL-Datenbank-Zielinstanz wiederhergestellt wurde, steht die Datenbank für die Durchführung einer Migrationsübernahme zur Verfügung.

1. Wenn Sie die Onlinedatenmigration abschließen möchten, klicken Sie auf **Übernahme starten**.

2. Beenden Sie den gesamten eingehenden Datenverkehr für Quelldatenbanken.

3. Verwenden Sie die [Sicherung des Protokollfragments], machen Sie die Sicherungsdatei auf der SMB-Netzwerkfreigabe verfügbar, und warten Sie, bis diese letzte Transaktionsprotokollsicherung wiederhergestellt wurde.

    Für **Ausstehende Änderungen** wird nun „0“ angezeigt.

4. Aktivieren Sie das Kontrollkästchen **Bestätigen**, und klicken Sie anschließend auf **Anwenden**.

    ![Vorbereiten des Abschlusses der Übernahme](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Wenn als Status der Datenmigration **Abgeschlossen** angezeigt wird, stellen Sie eine Verbindung zwischen Ihren Anwendungen und der neuen verwalteten Azure SQL-Datenbank-Zielinstanz her.

    ![Übernahme abgeschlossen](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Nächste Schritte

- Ein Tutorial, in dem gezeigt wird, wie Sie eine Datenbank mit dem T-SQL-Befehl RESTORE zu einer verwalteten Instanz migrieren können, finden Sie unter [Schnellstart: Wiederherstellen einer Datenbank in einer verwalteten Instanz](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Informationen zur verwalteten Instanz finden Sie unter [Verwenden der Advanced Data Security einer Azure SQL-Datenbank mit virtuellen Netzwerken und nahezu 100iger % Kompatibilität](../sql-database/sql-database-managed-instance.md).
- Informationen zum Verbinden von Apps mit einer verwalteten Instanz finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](../sql-database/sql-database-managed-instance-connect-app.md).
