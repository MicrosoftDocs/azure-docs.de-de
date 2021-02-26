---
title: Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie zur Bereitstellung und Ausführung von SSIS-Paketen in Azure die Azure-SSIS-Integration Runtime in Azure Data Factory erstellen.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 4e3137b08c558c8e9dfadda07f0b8bb66433ee83
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389415"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel werden die Schritte zur Bereitstellung einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory beschrieben. Die Azure-SSIS IR unterstützt Folgendes:

- Ausführen von Paketen, die im SSIS-Katalog (SSISDB) bereitgestellt werden, wobei zum Hosten ein Azure SQL-Datenbank-Server/eine verwaltete Instanz verwendet wird (Projektbereitstellungsmodell)
- Ausführen von Paketen, die im Dateisystem, in Azure Files oder SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten eine verwaltete Azure SQL-Instanz verwendet wird (Paketbereitstellungsmodell)

Nach der Bereitstellung einer Azure-SSIS IR können Sie die vertrauten Tools nutzen, um Ihre Pakete in Azure bereitzustellen und auszuführen. Diese Tools sind bereits Azure-fähig und enthalten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) sowie Befehlszeilen-Hilfsprogramme wie [dtutil](/sql/integration-services/dtutil-utility) und [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Im Tutorial zum [Bereitstellen der Azure-SSIS Integration Runtime](./tutorial-deploy-ssis-packages-azure.md) wird veranschaulicht, wie Sie eine Azure-SSIS IR mit dem Azure-Portal oder der Data Factory-App erstellen. Außerdem wird im Tutorial veranschaulicht, wie Sie optional einen Azure SQL-Datenbank-Server oder eine verwaltete Instanz zum Hosten der SSISDB verwenden. Dieser Artikel baut auf dem Tutorial auf, und es wird beschrieben, wie Sie die folgenden optionalen Aufgaben durchführen:

- Verwenden Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit einem privaten Endpunkt zum Hosten der SSISDB. Als Voraussetzung müssen Sie Berechtigungen und Einstellungen für das virtuelle Netzwerk konfigurieren, damit Ihre Azure-SSIS IR einem virtuellen Netzwerk beitreten kann.

- Verwenden Sie die Azure AD-Authentifizierung (Azure Active Directory) mit der verwalteten Identität für Ihre Data Factory, um eine Verbindung mit dem Azure SQL-Datenbank-Server oder der verwalteten Instanz herzustellen. Als Voraussetzung müssen Sie die verwaltete Identität für Ihre Data Factory als Datenbankbenutzer hinzufügen, der eine SSISDB-Instanz erstellen kann.

- Verknüpfen Sie Ihre Azure-SSIS IR mit einem virtuellen Netzwerk, oder konfigurieren Sie eine selbstgehostete Integration Runtime als Proxy für Ihre Azure-SSIS IR, um auf lokale Daten zuzugreifen.

In diesem Artikel wird veranschaulicht, wie Sie eine Azure-SSIS IR mit dem Azure-Portal, Azure PowerShell und einer Azure Resource Manager-Vorlage bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement**. Falls Sie noch nicht über ein Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

- **Azure SQL-Datenbank-Server oder SQL Managed Instance (optional)** . Wenn Sie noch nicht über einen Datenbankserver oder eine verwaltete Instanz verfügen, erstellen Sie einen bzw. eine im Azure-Portal, bevor Sie beginnen. Mit Data Factory wird wiederum eine SSISDB-Instanz auf diesem Datenbankserver erstellt. 

  Wir empfehlen, dass Sie den Datenbankserver oder die verwaltete Instanz in derselben Azure-Region wie die Integration Runtime erstellen. Diese Konfiguration ermöglicht es der Integration Runtime, Ausführungsprotokolle in SSISDB zu schreiben, ohne Azure-Regionen zu überschreiten.

  Beachten Sie Folgendes:

  - Die SSISDB-Instanz kann in Ihrem Namen als Einzeldatenbank, als Teil eines Pools für elastische Datenbanken oder in einer verwalteten Instanz erstellt werden. Der Zugriff kann über ein öffentliches Netzwerk oder durch den Beitritt zu einem virtuellen Netzwerk erfolgen. Einen Leitfaden zum Wählen zwischen SQL-Datenbank und SQL Managed Instance zum Hosten von SSISDB finden Sie in diesem Artikel im Abschnitt [Vergleich zwischen SQL-Datenbank und SQL Managed Instance](#comparison-of-sql-database-and-sql-managed-instance). 
  
    Wenn Sie einen Azure SQL-Datenbank-Server mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete SQL-Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen, müssen Sie Ihre Azure-SSIS IR-Instanz mit einem virtuellen Netzwerk verknüpfen. Weitere Informationen finden Sie im Artikel zum [Beitritt einer Azure-SSIS Integration Runtime zu einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Vergewissern Sie sich, dass die Einstellung **Zugriff auf Azure-Dienste zulassen** für den Datenbankserver aktiviert ist. Diese Einstellung gilt nicht, wenn Sie einen Azure SQL-Datenbank-Server mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete SQL-Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden. Weitere Informationen finden Sie unter [Schützen der Azure SQL-Datenbank](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Informationen zum Aktivieren dieser Einstellung mithilfe von PowerShell finden Sie unter [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Fügen Sie der Client-IP-Adressenliste in den Firewalleinstellungen für den Datenbankserver die IP-Adresse des Clientcomputers oder einen IP-Adressbereich hinzu, der die IP-Adresse des Clientcomputers enthält. Weitere Informationen finden Sie unter [Firewallregeln auf Serverebene und Datenbankebene für Azure SQL-Datenbank](../azure-sql/database/firewall-configure.md).

  - Sie können eine Verbindung mit dem Datenbankserver herstellen, indem Sie die SQL-Authentifizierung mit Ihren Serveradministrator-Anmeldeinformationen oder die Azure AD-Authentifizierung mit der verwalteten Identität für Ihre Data Factory-Instanz verwenden. Im letzteren Fall müssen Sie die verwaltete Identität für Ihre Data Factory-Instanz einer Azure AD-Gruppe mit Zugriffsberechtigungen für den Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md).

  - Vergewissern Sie sich, dass Ihr Datenbankserver noch nicht über eine SSISDB-Instanz verfügt. Für die Bereitstellung einer Azure-SSIS IR wird die Verwendung einer vorhandenen SSISDB-Instanz nicht unterstützt.

- **Virtuelles Azure Resource Manager-Netzwerk (optional)** . Sie benötigen ein virtuelles Azure Resource Manager-Netzwerk, wenn mindestens eine der folgenden Bedingungen zutrifft:

  - Sie hosten die SSISDB auf einem Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder einer verwalteten Instanz mit einem privaten Endpunkt.

  - Sie möchten über SSIS-Pakete, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung mit lokalen Datenspeichern herstellen, ohne eine selbstgehostete IR zu konfigurieren.

- **Azure PowerShell (optional)** . Befolgen Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps), wenn Sie ein PowerShell-Skript zur Bereitstellung von Azure-SSIS IR ausführen möchten.

### <a name="regional-support"></a>Regionsunterstützung

Eine Liste mit den Azure-Regionen, in denen Data Factory und eine Azure-SSIS Integration Runtime verfügbar sind, finden Sie unter [Data Factory und SSIS IR: Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Vergleich zwischen SQL-Datenbank und einer verwalteten SQL-Instanz

In der folgenden Tabelle werden bestimmte Features eines Azure SQL-Datenbank-Servers und der verwalteten SQL-Instanz in Bezug auf die Azure-SSIS IR-Instanz verglichen:

| Funktion | SQL-Datenbank| Verwaltete SQL-Instanz |
|---------|--------------|------------------|
| **Zeitplanung** | Der SQL Server-Agent ist nicht verfügbar.<br/><br/>Weitere Informationen finden Sie unter [Planen eines Pakets als Teil einer Azure Data Factory-Pipeline](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| Der Agent für die verwaltete Instanz ist verfügbar. |
| **Authentifizierung** | Sie können eine SSISDB-Instanz mit einem Benutzer für eine eigenständige Datenbank erstellen, der eine beliebige Azure AD-Gruppe mit der verwalteten Identität Ihrer Data Factory als Member in der Rolle **db_owner** darstellt.<br/><br/>Informationen finden Sie unter [Aktivieren der Azure AD-Authentifizierung zum Erstellen einer SSISDB auf einem Azure SQL-Datenbank-Server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Sie können eine SSISDB-Instanz mit einem Benutzer für eine eigenständige Datenbank erstellen, der die verwaltete Identität Ihrer Data Factory darstellt. <br/><br/>Informationen finden Sie unter [Aktivieren der Azure AD-Authentifizierung zum Erstellen einer SSISDB in einer verwalteten Azure SQL-Datenbank-Instanz](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Dienstebene** | Wenn Sie eine Azure-SSIS IR mit Ihrem Azure SQL-Datenbank-Server erstellen, können Sie die Dienstebene für die SSISDB auswählen. Es gibt mehrere Dienstebenen. | Wenn Sie eine Azure-SSIS IR mit Ihrer verwalteten Instanz erstellen, können Sie die Dienstebene für die SSISDB nicht auswählen. Alle Datenbanken in Ihrer verwalteten Instanz nutzen dieselbe Ressource, die dieser Instanz zugeordnet ist, gemeinsam. |
| **Virtuelles Netzwerk** | Ihre Azure-SSIS IR kann zu einem virtuellen Azure Resource Manager-Netzwerk hinzugefügt werden, wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten verwenden. | Ihre Azure-SSIS IR kann zu einem virtuellen Azure Resource Manager-Netzwerk hinzugefügt werden, wenn Sie eine verwaltete Instanz mit einem privaten Endpunkt verwenden. Das virtuelle Netzwerk ist erforderlich, wenn Sie keinen öffentlichen Endpunkt für Ihre verwaltete Instanz aktivieren.<br/><br/>Wenn Sie Ihre Azure-SSIS IR in dasselbe virtuelle Netzwerk wie Ihre verwaltete Instanz einbinden, sollten Sie sicherstellen, dass sich Ihre Azure-SSIS IR in einem anderen Subnetz als Ihre verwaltete Instanz befindet. Wenn Sie Ihre Azure-SSIS IR in ein anderes virtuelles Netzwerk als Ihre verwaltete Instanz einbinden, empfehlen wir Ihnen, entweder ein Peering virtueller Netzwerke oder eine Verbindung zwischen den virtuellen Netzwerken zu verwenden. Informationen hierzu finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/connect-application-instance.md). |
| **Verteilte Transaktionen** | Dieses Feature wird über elastische Transaktionen unterstützt. MS DTC-Transaktionen (Microsoft Distributed Transaction Coordinator) werden nicht unterstützt. Wenn Ihre SSIS-Pakete MSDTC verwenden, um verteilte Transaktionen zu koordinieren, sollten Sie eine Migration zu elastischen Transaktionen für Azure SQL-Datenbank erwägen. Weitere Informationen finden Sie unter [Verteilte Transaktionen über Clouddatenbanken](../azure-sql/database/elastic-transactions-overview.md). | Wird nicht unterstützt. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Verwenden des Azure-Portals zum Erstellen einer Integration Runtime

In diesem Abschnitt erstellen Sie mit dem Azure-Portal über die Data Factory-Benutzeroberfläche (UI) oder die App eine Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Befolgen Sie die Schritt-für-Schritt-Anleitung unter [Erstellen einer Data Factory](./quickstart-create-data-factory-portal.md#create-a-data-factory), um Ihre Data Factory-Instanz über das Azure-Portal zu erstellen. Wählen Sie hierbei die Option **An Dashboard anheften** aus, um nach der Erstellung den schnellen Zugriff zu ermöglichen. 

Öffnen Sie nach der Erstellung Ihrer Data Factory-Instanz im Azure-Portal die zugehörige Übersichtsseite. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die zugehörige Seite **Erste Schritte** auf einer separaten Registerkarte zu öffnen. Sie können darauf dann mit der Erstellung Ihrer Azure-SSIS IR fortfahren.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Bereitstellen einer Azure-SSIS Integration Runtime

Wählen Sie auf der Seite **Los geht‘s** die Kachel **SSIS Integration Runtime konfigurieren** aus, um den Bereich **Integration Runtime-Setup** zu öffnen.

   ![Kachel „Configure SSIS Integration Runtime“ (SSIS Integration Runtime konfigurieren)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   Der Bereich **Integration Runtime-Setup** enthält drei Seiten, auf denen Sie nacheinander allgemeine Einstellungen, Bereitstellungseinstellungen und erweiterte Einstellungen konfigurieren.

#### <a name="general-settings-page"></a>Seite „Allgemeine Einstellungen“

Führen Sie auf der Seite **Allgemeine Einstellungen** des Bereichs **Integration Runtime-Setup** die folgenden Schritte aus.

   ![Allgemeine Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Geben Sie unter **Name** den Namen Ihrer Integration Runtime ein.

   2. Geben Sie unter **Beschreibung** die Beschreibung Ihrer Integration Runtime ein.

   3. Wählen Sie unter **Standort** den Standort für Ihre Integration Runtime aus. Es werden nur unterstützte Standorte angezeigt. Sie sollten den gleichen Standort wie für den Datenbankserver zum Hosten von SSISDB auswählen.

   4. Wählen Sie unter **Knotengröße** die Größe des Knotens in Ihrem Integration Runtime-Cluster aus. Nur unterstützte Knotengrößen werden angezeigt. Wählen Sie eine große Knotengröße (Hochskalieren) aus, wenn Sie zahlreiche compute- oder arbeitsspeicherintensive Pakete ausführen möchten.
   > [!NOTE]
   > Wenn Sie [Computeisolation](../azure-government/azure-secure-isolation-guidance.md#compute-isolation) benötigen, wählen Sie die Knotengröße **Standard_E64i_v3** aus. Diese Knotengröße stellt isolierte virtuelle Computer dar, die ihren gesamten physischen Host nutzen. Sie bietet damit die erforderliche Isolationsstufe, die für bestimmte Workloads erforderlich ist, wie z. B. für Workloads gemäß IL5 (Impact Level 5) des US-Verteidigungsministeriums.
   
   5. Wählen Sie unter **Knotenzahl** die Anzahl von Knoten in Ihrer Integration Runtime aus. Nur unterstützte Knotenzahlen werden angezeigt. Wählen Sie einen großen Cluster mit zahlreichen Knoten (Aufskalieren) aus, wenn Sie viele Pakete parallel ausführen möchten.

   6. Wählen Sie unter **Edition/Lizenz** die SQL Server-Edition für Ihre Integration Runtime aus: Standard oder Enterprise. Wählen Sie Enterprise aus, wenn Sie erweiterte Funktionen für Ihre Integration Runtime verwenden möchten.

   7. Wählen Sie unter **Sparen Sie Geld** die Option „Azure-Hybridvorteil“ für Ihre Integration Runtime aus: **Ja** oder **Nein**. Wählen Sie **Ja**, wenn Sie eine eigene SQL Server-Lizenz mit Software Assurance verwenden möchten, um bei der Hybridnutzung von Kostenersparnissen zu profitieren.

   8. Wählen Sie **Weiter** aus.

#### <a name="deployment-settings-page"></a>Seite „Bereitstellungseinstellungen“

Auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) können Sie SSISDB- und/oder Azure-SSIS IR-Paketspeicher erstellen.

##### <a name="creating-ssisdb"></a>Erstellen von SSISDB

Wenn Sie Ihre Pakete in SSISDB bereitstellen möchten (Projektbereitstellungsmodell), aktivieren Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) das Kontrollkästchen **Vom Azure SQL-Datenbank-Server/von der verwalteten Instanz gehosteten SSIS-Katalog (SSISDB) zum Speichern Ihrer Projekte/Pakete/Umgebungen/Ausführungsprotokolle erstellen**. Wenn Sie Ihre Pakete im Dateisystem, in Azure Files oder SQL Server-Datenbank (MSDB) bereitstellen möchten, das bzw. die von Azure SQL Managed Instance gehostet wird (Paketbereitstellungsmodell), müssen Sie weder eine SSISDB erstellen noch das Kontrollkästchen aktivieren.

Aktivieren Sie dieses Kontrollkästchen unabhängig von Ihrem Bereitstellungsmodell, wenn Sie den von Azure SQL Managed Instance gehosteten SQL Server-Agent zum Orchestrieren/Planen Ihrer Paketausführungen verwenden möchten, da dies durch SSISDB aktiviert wird. Weitere Informationen finden Sie unter [Planen der Ausführung von SSIS-Paketen mit einem Agent für die verwaltete Azure SQL-Instanz](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Wenn Sie das Kontrollkästchen aktivieren, führen Sie die folgenden Schritte aus, um Ihren eigenen Datenbankserver zum Hosten der SSISDB bereitzustellen, die wir in Ihrem Namen erstellen und verwalten.

   ![Bereitstellungseinstellungen für SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Geben Sie unter **Abonnement** das Azure-Abonnement mit dem Azure-Datenbankserver zum Hosten von SSISDB an. 

   1. Wählen Sie als **Standort** den Standort des Datenbankservers zum Hosten von SSISDB aus. Sie sollten denselben Standort wie den für die Integration Runtime auswählen.

   1. Wählen Sie unter **Katalogdatenbank-Serverendpunkt** den Endpunkt Ihres Datenbankservers zum Hosten von SSISDB aus. 
   
      Basierend auf dem ausgewählten Datenbankserver kann die SSISDB-Instanz in Ihrem Namen als Einzeldatenbank, als Teil eines Pools für elastische Datenbanken oder auf einer verwalteten Instanz erstellt werden. Der Zugriff kann über ein öffentliches Netzwerk oder durch den Beitritt zu einem virtuellen Netzwerk erfolgen. Einen Leitfaden zum Auswählen des Datenbankservertyps zum Hosten von SSISDB finden Sie unter [Vergleich zwischen SQL-Datenbank und einer verwalteten SQL-Instanz](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB auswählen oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen, müssen Sie Ihre Azure-SSIS IR-Instanz einem virtuellen Netzwerk hinzufügen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory]().

   1. Aktivieren Sie das Kontrollkästchen **Use Azure AD authentication with the managed identity for your ADF** (Azure AD-Authentifizierung mit der verwalteten Identität für Ihre ADF verwenden), um die Authentifizierungsmethode für Ihren Datenbankserver zum Hosten der SSISDB auszuwählen. Sie wählen entweder die SQL-Authentifizierung oder die Azure AD-Authentifizierung mit der verwalteten Identität für Ihre Data Factory-Instanz aus.

      Wenn Sie das Kontrollkästchen aktivieren, müssen Sie die verwaltete Identität für Ihre Data Factory-Instanz einer Azure AD-Gruppe mit Zugriffsberechtigungen für Ihren Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory]().
   
   1. Geben Sie unter **Administratorbenutzername** den Benutzernamen für die SQL-Authentifizierung für den Datenbankserver zum Hosten der SSISDB an. 

   1. Geben Sie unter **Administratorbenutzerkennwort** das Kennwort für die SQL-Authentifizierung für den Datenbankserver zum Hosten der SSISDB an. 

   1. Wählen Sie unter **Katalogdatenbank-Dienstebene** die Dienstebene für Ihren Datenbankserver zum Hosten der SSISDB aus. Wählen Sie den Tarif „Basic“, „Standard“ oder „Premium“ oder den Namen eines Pools für elastische Datenbanken aus.

Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Weiter** aus.

> [!NOTE]
   > Wenn Sie den Azure SQL-Datenbankserver zum Hosten von SSISDB verwenden, werden Ihre Daten standardmäßig im georedundanten Speicher für Sicherungen gespeichert. Wenn Sie nicht möchten, dass Ihre Daten in anderen Regionen repliziert werden, befolgen Sie die Anweisungen zum [Konfigurieren der Redundanz für Sicherungsspeicher mithilfe von PowerShell](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Erstellen von Azure-SSIS IR-Paketspeichern

Aktivieren Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) das Kontrollkästchen **Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance** (Paketspeicher zum Verwalten Ihrer Pakete erstellen, die im Dateisystem/in Azure Files/der SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten Azure SQL Managed Instance verwendet wird), wenn Sie Ihre in MSDB, im Dateisystem oder in Azure Files (Paketbereitstellungsmodell) bereitgestellten Pakete mit Azure-SSIS IR-Paketspeichern verwalten möchten.
   
Ein Azure-SSIS IR-Paketspeicher ermöglicht Ihnen das Importieren/Exportieren/Löschen/Ausführen von Paketen und das Überwachen/Beenden der Ausführung von Paketen über SSMS, ähnlich wie im [Legacy-SSIS-Paketspeicher](/sql/integration-services/service/package-management-ssis-service). Weitere Informationen finden Sie unter [Verwalten von SSIS-Paketen mit Azure-SSIS IR-Paketspeichern](./azure-ssis-integration-runtime-package-store.md).
   
Wenn Sie dieses Kontrollkästchen aktivieren, können Sie Ihrer Azure-SSIS IR mehrere Paketspeicher hinzufügen, indem Sie **Neu** auswählen. Umgekehrt kann ein einziger Paketspeicher von mehreren Azure-SSIS IRs gemeinsam genutzt werden.

![Bereitstellungseinstellungen für MSDB/Dateisystem/Azure Files](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

Führen Sie im Bereich **Paketspeicher hinzufügen** die folgenden Schritte aus.
   
   1. Geben Sie unter **Name des Paketspeichers** den Namen Ihres Paketspeichers ein. 

   1. Wählen Sie unter **Mit Paketspeicher verknüpfter Dienst** Ihren vorhandenen verknüpften Dienst zum Speichern der Zugriffsinformationen für das Dateisystem/Azure Files/die verwaltete Azure SQL-Instanz aus, in dem bzw. der Ihre Pakete bereitgestellt werden, oder erstellen Sie einen neuen Dienst, indem Sie **Neu** auswählen. Führen Sie im Bereich **Neuer verknüpfter Dienst** die folgenden Schritte aus.
   
      > [!NOTE]
      > Für den Zugriff auf Azure Files können Sie die verknüpften Dienste **Azure File Storage** oder **Dateisystem** verwenden. Wenn Sie den verknüpften Dienst **Azure File Storage** verwenden, unterstützt der Azure-SSIS IR-Paketspeicher derzeit nur die **Standardauthentifizierungsmethode**. (**Kontoschlüssel** und **SAS-URI** werden nicht unterstützt.) Wenn Sie die **Standardauthentifizierung** für den verknüpften Dienst **Azure File Storage** nutzen möchten, können Sie `?feature.upgradeAzureFileStorage=false` an die ADF-Portal-URL in Ihrem Browser anfügen. Alternativ können Sie den verknüpften Dienst **Dateisystem** verwenden, um stattdessen auf Azure Files zuzugreifen. 

      ![Bereitstellungseinstellungen für verknüpfte Dienste](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Geben Sie unter **Name** den Namen Ihres verknüpften Diensts ein. 
         
      1. Geben Sie unter **Beschreibung** die Beschreibung Ihres verknüpften Diensts ein. 
         
      1. Wählen Sie unter **Typ** einen der Typen **Azure File Storage**, **Verwaltete Azure SQL-Instanz** oder **Dateisystem** aus.

      1. Sie können **Verbinden über Integration Runtime** ignorieren, da wir immer Ihre Azure-SSIS IR zum Abrufen der Zugriffsinformationen für Paketspeicher verwenden.

      1. Wenn Sie **Azure File Storage** auswählen, führen Sie die folgenden Schritte aus. 

         1. Wählen Sie unter **Kontoauswahlmethode** eine der Methoden **Aus Azure-Abonnement** oder **Manuell eingeben** aus.
         
         1. Wenn Sie **Aus Azure-Abonnement** auswählen, wählen Sie die entsprechenden Werte für **Azure-Abonnement**, **Speicherkontoname** und **Dateifreigabe** aus.
            
         1. Wenn Sie **Manuell eingeben** auswählen, geben Sie unter **Host** `\\<storage account name>.file.core.windows.net\<file share name>`, unter **Benutzername** `Azure\<storage account name>` und unter **Kennwort** `<storage account key>` ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wenn Sie **Verwaltete Azure SQL-Instanz** auswählen, führen Sie die folgenden Schritte aus. 

         1. Wählen Sie **Verbindungszeichenfolge** aus, um sie manuell einzugeben, oder Ihren **Azure Key Vault** aus, in dem sie als Geheimnis gespeichert ist.
         
         1. Wenn Sie **Verbindungszeichenfolge** auswählen, führen Sie die folgenden Schritte aus. 

            1. Geben Sie unter **Vollqualifizierter Domänenname** `<server name>.<dns prefix>.database.windows.net` oder `<server name>.public.<dns prefix>.database.windows.net,3342` als privaten bzw. öffentlichen Endpunkt Ihrer verwalteten Azure SQL-Instanz ein. Wenn Sie den privaten Endpunkt eingeben, kann **Verbindung testen** nicht angewendet werden, da dieser Endpunkt über die ADF-Benutzeroberfläche nicht erreicht werden kann.

            1. Geben Sie unter **Datenbankname** `msdb` ein.
               
            1. Wählen Sie unter **Authentifizierungstyp** einen der Typen **SQL-Authentifizierung**, **Verwaltete Identität** oder **Dienstprinzipal** aus.

            1. Wenn Sie **SQL-Authentifizierung** auswählen, geben Sie die entsprechenden Werte für **Benutzername** und **Kennwort** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

            1. Wenn Sie **Verwaltete Identität** auswählen, gewähren Sie Ihrer verwalteten ADF-Identität Zugriff auf Ihre verwaltete Azure SQL-Instanz.

            1. Wenn Sie **Dienstprinzipal** auswählen, geben Sie die entsprechenden Werte für **Dienstprinzipal-ID** und **Dienstprinzipalschlüssel** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wenn Sie **Dateisystem** auswählen, geben Sie den UNC-Pfad des Ordners, in dem Ihre Pakete für **Host** bereitgestellt werden, sowie die entsprechenden Werte für **Benutzername** und **Kennwort** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Erstellen** aus.

   1. Ihre hinzugefügten Paketspeicher werden auf der Seite **Bereitstellungseinstellungen** angezeigt. Wenn Sie sie entfernen möchten, können Sie die entsprechenden Kontrollkästchen aktivieren und dann **Löschen** auswählen.

Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Weiter** aus.

#### <a name="advanced-settings-page"></a>Seite „Erweiterte Einstellungen“

Führen Sie auf der Seite **Erweiterte Einstellungen** des Bereichs **Integration Runtime-Setup** die folgenden Schritte aus.

   ![Erweiterte Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Legen Sie unter **Maximale Anzahl von parallelen Ausführungen pro Knoten** die maximale Anzahl von Paketen fest, die in Ihrem Integration Runtime-Cluster pro Knoten gleichzeitig ausgeführt werden sollen. Nur unterstützte Paketzahlen werden angezeigt. Wählen Sie eine niedrigere Anzahl aus, wenn Sie mehrere Kerne verwenden möchten, um ein einzelnes großes Paket auszuführen, das compute- oder arbeitsspeicherintensiv ist. Wählen Sie eine hohe Anzahl aus, wenn Sie mindestens ein kleines Paket in einem einzelnen Kern ausführen möchten.

   1. Aktivieren Sie das Kontrollkästchen **Azure-SSIS IR mit zusätzlichen Systemkonfigurationen/Komponenteninstallationen anpassen**, um festzulegen, ob Sie benutzerdefinierte Standard-/Express-Setups für Ihre Azure-SSIS IR-Instanz hinzufügen möchten. Weitere Informationen finden Sie unter [Anpassen des Setups für Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      ![Erweiterte Einstellungen bei benutzerdefinierten Setups](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Geben Sie für **SAS-URI des Containers für benutzerdefinierte Setups** den SAS-URI des Containers ein, in dem Sie Skripts und zugehörige Dateien für benutzerdefinierte Standard-Setups speichern.

      1. Wählen Sie für **benutzerdefinierte Express-Setups** die Option **Neu** aus, um den Bereich **Benutzerdefiniertes Express-Setup hinzufügen** zu öffnen, und wählen Sie dann im Dropdown Menü **Typ des benutzerdefinierten Express-Setups** einen Typ aus, z. B. **Befehl „cmdkey“ ausführen**, **Umgebungsvariable hinzufügen**, **Lizenzierte Komponente installieren** usw.

         Bei Auswahl des Typs **Lizenzierte Komponente installieren** können Sie im Dropdownmenü **Komponentenname** alle integrierten Komponenten unserer ISV-Partner auswählen und bei Bedarf im Feld **Lizenzschlüssel**/**Lizenzdatei** den erworbenen Produktlizenzschlüssel eingeben/die erworbene Produktlizenzdatei hochladen.
  
         Ihre hinzugefügten benutzerdefinierten Express-Setups werden auf der Seite **Erweiterte Einstellungen** angezeigt. Um diese zu entfernen, können Sie die entsprechenden Kontrollkästchen aktivieren und dann **Löschen** auswählen.

   1. Aktivieren Sie das Kontrollkästchen **VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden**, um festzulegen, ob Sie Ihre Integration Runtime mit einem virtuellen Netzwerk verknüpfen möchten. 

      Aktivieren Sie dieses Kontrollkästchen, wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden oder Zugriff auf lokale Daten benötigen (d. h., dass lokale Datenquellen oder Ziele in Ihren SSIS-Paketen vorhanden sind), ohne eine selbstgehostete IR zu konfigurieren. Weitere Informationen finden Sie im Artikel zum [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      ![Erweiterte Einstellungen mit einem virtuellen Netzwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

      1. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

      1. Wählen Sie für **Typ** den Typ des virtuellen Netzwerks aus: „Klassisch“ oder „Azure Resource Manager“. Sie sollten ein virtuelles Azure Resource Manager-Netzwerk auswählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

      1. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Er sollte mit dem übereinstimmen, der für Ihren Azure SQL-Datenbankserver mit VNET-Dienstendpunkten oder die verwaltete Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Oder er sollte mit dem Namen des Netzwerks übereinstimmen, das mit Ihrem lokalen Netzwerk verbunden ist. Andernfalls kann es sich um ein beliebiges virtuelles Netzwerk handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

      1. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Es sollte mit dem übereinstimmen, der für Ihren Azure SQL-Datenbankserver mit VNET-Dienstendpunkten zum Hosten der SSISDB verwendet wird. Oder es sollte ein anderes Subnetz sein als das, das für Ihre verwaltete Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwendet wird. Andernfalls kann es sich um ein beliebiges Subnetz handeln, damit Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwendet werden.

      1. Aktivieren Sie das Kontrollkästchen **Statische öffentliche IP-Adressen für die Azure-SSIS Integration Runtime einbringen**, um festzulegen, ob Sie Ihre eigenen statischen öffentlichen IP-Adressen für Azure-SSIS IR verwenden möchten, damit Sie sie in der Firewall für Ihre Datenquellen zulassen können.

         Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

         1. Wählen Sie für **Erste statische öffentliche IP-Adresse** die erste statische öffentliche IP-Adresse aus, die den Anforderungen für Ihre Azure-SSIS IR entspricht. Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.
      
         1. Wählen Sie für **Zweite statische öffentliche IP-Adresse** die zweite statische öffentliche IP-Adresse aus, die den Anforderungen für Ihre Azure-SSIS IR entspricht. Klicken Sie auf den Link **Neue erstellen**, wenn Sie über keine statischen öffentlichen IP-Adressen verfügen, um diese im Azure-Portal zu erstellen. Klicken Sie dann hier auf die Schaltfläche „Aktualisieren“, um sie auszuwählen.

   1. Aktivieren Sie das Kontrollkästchen **Selbstgehostete Integration Runtime als Proxy für Ihre Azure-SSIS Integration Runtime-Instanz einrichten**, um festzulegen, ob Sie eine selbstgehostete IR als Proxy für Ihre Azure-SSIS IR-Instanz konfigurieren möchten. Weitere Informationen finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Führen Sie die unten angegebenen Schritte aus, falls Sie das Kontrollkästchen aktivieren.

      ![Erweiterte Einstellungen bei einer selbstgehosteten IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Wählen Sie für die **selbstgehostete Integration Runtime** Ihre vorhandene selbstgehostete IR als Proxy für Azure-SSIS IR aus.

      1. Wählen Sie für den **verknüpften Dienst des Stagingspeichers** den verknüpften Dienst für Ihren vorhandenen Azure-Blobspeicher aus oder erstellen Sie einen neuen Dienst für den Stagingprozess.

      1. Geben Sie für den **Stagingpfad** einen Blobcontainer in Ihrem ausgewählten Azure-Blobspeicherkonto an oder lassen Sie das Feld leer, um einen Standardpfad für den Stagingprozess zu verwenden.

   1. Wählen Sie **VNET-Überprüfung** > **Weiter** aus. 

Überprüfen Sie im Abschnitt **Zusammenfassung** alle Bereitstellungseinstellungen, erstellen Sie Lesezeichen für die empfohlenen Dokumentationslinks, und wählen Sie **Fertig stellen** aus, um die Erstellung Ihrer Integration Runtime-Instanz zu starten.

   > [!NOTE]
   > Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von fünf Minuten abgeschlossen sein. Es kann aber 20 bis 30 Minuten dauern, bis der Betritt einer Azure-SSIS IR zu einem virtuellen Netzwerk abgeschlossen ist.
   >
   > Bei Verwendung der SSISDB stellt der Data Factory-Dienst eine Verbindung mit Ihrem Datenbankserver her, um die SSISDB vorzubereiten. Außerdem konfiguriert er Berechtigungen und Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die Azure-SSIS IR mit dem virtuellen Netzwerk.
   > 
   > Wenn Sie Azure-SSIS IR bereitstellen, werden auch die Access Redistributable-Komponente und das Azure Feature Pack für SSIS installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-Dateien, Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten bereits unterstützt werden. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Weitere Informationen zu zusätzlichen Komponenten, die Sie installieren können, finden Sie unter [Custom setups for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Setups for Azure-SSIS IR).

#### <a name="connections-pane"></a>Bereich „Verbindungen“

Wechseln Sie im Bereich **Verbindungen** von **Verwaltungshub** zur Seite **Integration Runtimes**, und wählen Sie **Aktualisieren** aus. 

   ![Bereich „Verbindungen“](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Sie können Ihre Azure-SSIS IR bearbeiten/neu konfigurieren, indem Sie deren Namen auswählen. Sie können auch die entsprechenden Schaltflächen zum Überwachen/Starten/Beenden/Löschen Ihrer Azure-SSIS IR, zum automatischen Generieren einer ADF-Pipeline mit der Aktivität „SSIS-Paket ausführen“ zur Ausführung in Ihrer Azure-SSIS IR und zum Anzeigen des JSON-Codes bzw. der Nutzlast Ihrer Azure-SSIS IR auswählen.  Ihre Azure-SSIS IR kann nur bearbeitet/gelöscht werden, wenn sie angehalten wurde.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS Integration Runtimes im Portal

1. Wechseln Sie auf der Azure Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, und wählen Sie **Verbindungen** aus. Wechseln Sie anschließend zur Registerkarte **Integration Runtimes**, um die vorhandenen Integration Runtimes Ihrer Data Factory-Instanz anzuzeigen.

   ![Anzeigen vorhandener IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wählen Sie **Neu** aus, um eine neue Azure-SSIS IR zu erstellen, und öffnen Sie den Bereich **Integration Runtime-Setup**.

   ![Integration Runtime über das Menü](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Wählen Sie im Bereich **Integration Runtime-Setup** die Kachel **Migrieren vorhandener SSIS-Pakete per Lift & Shift für die Ausführung in Azure** und anschließend **Weiter** aus.

   ![Angeben des Integration Runtime-Typs](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Die übrigen Schritte zum Einrichten einer Azure-SSIS IR finden Sie im Abschnitt [Bereitstellen einer Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime).

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Verwenden von Azure PowerShell zum Erstellen einer Integration Runtime

In diesem Abschnitt verwenden Sie Azure PowerShell zum Erstellen einer Azure-SSIS IR.

### <a name="create-variables"></a>Erstellen von Variablen

Kopieren und fügen Sie das folgende Skript ein. Geben Sie Werte für die Variablen an. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Anmelden und Auswählen eines Abonnements

Fügen Sie das folgende Skript hinzu, um sich anzumelden und Ihr Azure-Abonnement auszuwählen.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Überprüfen der Verbindung mit dem Datenbankserver

Fügen Sie das folgende Skript hinzu, um den Azure SQL-Datenbank-Server oder die verwaltete Instanz zu überprüfen.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Konfigurieren des virtuellen Netzwerks

Fügen Sie das folgende Skript hinzu, um Berechtigungen und Einstellungen für das virtuelle Netzwerk für die Azure-SSIS Integration Runtime, die Sie einbinden möchten, automatisch zu konfigurieren.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden.

Wenn die Ressourcengruppe bereits vorhanden ist, müssen Sie diesen Code nicht in das Skript kopieren. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Führen Sie den folgenden Befehl aus, um eine Data Factory zu erstellen:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Erstellen einer Integration Runtime

Führen Sie die folgenden Befehle aus, um eine Azure-SSIS-Integration Runtime zu erstellen, die SSIS-Pakete in Azure ausführt.

Wenn Sie die SSISDB nicht nutzen, können Sie die Parameter `CatalogServerEndpoint`, `CatalogPricingTier` und `CatalogAdminCredential` weglassen.

Falls Sie keinen Azure SQL-Datenbank-Server mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit einem privaten Endpunkt zum Hosten der SSISDB verwenden oder wenn Sie Zugriff auf lokale Daten benötigen, können Sie die Parameter `VNetId` und `Subnet` weglassen oder dafür leere Werte übergeben. Sie können sie auch weglassen, wenn Sie eine selbstgehostete IR als Proxy für Ihre Azure-SSIS IR für den Zugriff auf lokale Daten konfigurieren. Andernfalls können Sie sie nicht weglassen und müssen gültige Werte aus Ihrer Konfiguration des virtuellen Netzwerks übergeben. Weitere Informationen finden Sie im Artikel zum [Beitritt einer Azure-SSIS Integration Runtime zu einem virtuellen Netzwerk](./join-azure-ssis-integration-runtime-virtual-network.md).

Wenn Sie eine verwaltete Instanz zum Hosten der SSISDB verwenden, können Sie den Parameter `CatalogPricingTier` weglassen oder dafür einen leeren Wert übergeben. Andernfalls können Sie ihn nicht weggelassen und müssen einen gültigen Wert aus der Liste mit den für Azure SQL-Datenbank unterstützten Tarifen übergeben. Weitere Informationen finden Sie unter [Ressourceneinschränkungen für SQL-Datenbank](../azure-sql/database/resource-limits-logical-server.md).

Wenn Sie die Azure AD-Authentifizierung mit der verwalteten Identität für Ihre Data Factory verwenden, um eine Verbindung mit dem Datenbankserver herzustellen, können Sie den Parameter `CatalogAdminCredential` weglassen. Sie müssen aber die verwaltete Identität für Ihre Data Factory-Instanz einer Azure AD-Gruppe mit Zugriffsberechtigungen für den Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung für Azure-SSIS Integration Runtime](./enable-aad-authentication-azure-ssis-ir.md). Andernfalls können Sie ihn nicht weglassen, sondern müssen ein gültiges Objekt – gebildet aus Ihrem Administratorbenutzernamen und dem zugehörigen Kennwort für den Server –für die SQL-Authentifizierung übergeben.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Starten der Integration Runtime

Führen Sie die folgenden Befehle aus, um Azure-SSIS Integration Runtime zu starten.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von fünf Minuten abgeschlossen sein. Es kann aber 20 bis 30 Minuten dauern, bis der Betritt einer Azure-SSIS IR zu einem virtuellen Netzwerk abgeschlossen ist.
>
> Bei Verwendung der SSISDB stellt der Data Factory-Dienst eine Verbindung mit Ihrem Datenbankserver her, um die SSISDB vorzubereiten. Außerdem konfiguriert er Berechtigungen und Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die Azure-SSIS IR mit dem virtuellen Netzwerk.
> 
> Wenn Sie Azure-SSIS IR bereitstellen, werden auch die Access Redistributable-Komponente und das Azure Feature Pack für SSIS installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-Dateien, Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten bereits unterstützt werden. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Weitere Informationen zu zusätzlichen Komponenten, die Sie installieren können, finden Sie unter [Custom setups for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Setups for Azure-SSIS IR).

### <a name="full-script"></a>Vollständiges Skript

Hier ist das vollständige Skript angegeben, mit dem eine Azure-SSIS Integration Runtime erstellt wird.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Verwenden einer Azure Resource Manager-Vorlage zum Erstellen einer Integration Runtime

In diesem Abschnitt verwenden Sie eine Azure Resource Manager-Vorlage, um die Azure-SSIS Integration Runtime zu erstellen. Hier ist eine exemplarische Vorgehensweise angegeben:

1. Erstellen Sie eine JSON-Datei mit der folgenden Azure Resource Manager-Vorlage. Ersetzen Sie die Werte in spitzen Klammern (Platzhalter) durch Ihre eigenen Werte.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Führen Sie den Befehl `New-AzResourceGroupDeployment` wie im folgenden Beispiel aus, um die Azure Resource Manager-Vorlage bereitzustellen. Im Beispiel ist `ADFTutorialResourceGroup` der Name Ihrer Ressourcengruppe. `ADFTutorialARM.json` ist die Datei, die die JSON-Definition für Ihre Data Factory und die Azure-SSIS IR enthält.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Mit diesem Befehl wird die Data Factory und darin eine Azure-SSIS IR erstellt, aber er dient nicht zum Starten der IR.

3. Führen Sie den Befehl `Start-AzDataFactoryV2IntegrationRuntime` aus, um Ihre Azure-SSIS IR zu starten:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von fünf Minuten abgeschlossen sein. Es kann aber 20 bis 30 Minuten dauern, bis der Betritt einer Azure-SSIS IR zu einem virtuellen Netzwerk abgeschlossen ist.
>
> Bei Verwendung der SSISDB stellt der Data Factory-Dienst eine Verbindung mit Ihrem Datenbankserver her, um die SSISDB vorzubereiten. Außerdem konfiguriert er Berechtigungen und Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die Azure-SSIS IR mit dem virtuellen Netzwerk.
> 
> Wenn Sie Azure-SSIS IR bereitstellen, werden auch die Access Redistributable-Komponente und das Azure Feature Pack für SSIS installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-Dateien, Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten bereits unterstützt werden. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Weitere Informationen zu zusätzlichen Komponenten, die Sie installieren können, finden Sie unter [Custom setups for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Setups for Azure-SSIS IR).

## <a name="deploy-ssis-packages"></a>Bereitstellen von SSIS-Paketen

Wenn Sie SSISDB verwenden, können Sie Ihre Pakete darin bereitstellen und sie mithilfe der Azure-fähigen SSDT- oder SSMS-Tools in Ihrer Azure-SSIS IR ausführen. Mit diesen Tools wird über den zugehörigen Serverendpunkt eine Verbindung mit Ihrem Datenbankserver hergestellt: 

- Bei einem Azure SQL-Datenbankserver weist der Serverendpunkt das Format `<server name>.database.windows.net` auf.
- Bei einer verwalteten Instanz mit privatem Endpunkt weist der Serverendpunkt das Format `<server name>.<dns prefix>.database.windows.net` auf.
- Bei einer verwalteten Instanz mit öffentlichem Endpunkt weist der Serverendpunkt das Format `<server name>.public.<dns prefix>.database.windows.net,3342` auf. 

Wenn Sie SSISDB nicht verwenden, können Sie Ihre Pakete im Dateisystem, in Azure Files oder der MSDB bereitstellen, das bzw. die von Ihrer Azure SQL Managed Instance gehostet wird, und sie mit den Befehlszeilen-Hilfsprogrammen [dtutil](/sql/integration-services/dtutil-utility) und [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) in Ihrer Azure-SSIS IR ausführen. 

Weitere Informationen finden Sie unter [Bereitstellen von SQL Server Integration Services-Projekten und -Paketen (SSIS)](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

In beiden Fällen können Sie Ihre bereitgestellten Pakete auch in der Azure-SSIS IR-Instanz ausführen, indem Sie die Aktivität „SSIS-Paket ausführen“ in Data Factory-Pipelines verwenden. Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich auch andere Themen zur Azure-SSIS IR in dieser Dokumentation an:

- [Azure SSIS-Integration Runtime:](concepts-integration-runtime.md#azure-ssis-integration-runtime) Dieser Artikel enthält allgemeine Informationen zu Integration Runtimes, einschließlich der Azure-SSIS IR.
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel erfahren Sie, wie Sie Informationen zur Azure-SSIS IR abrufen und verstehen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie die Azure-SSIS IR beenden, starten oder löschen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS Integration Runtime aufskalieren, indem Sie weitere Knoten hinzufügen.
- [Bereitstellen und Ausführen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung) 
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)