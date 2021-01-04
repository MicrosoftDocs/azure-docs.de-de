---
title: Bereitstellen von Azure-SSIS Integration Runtime
description: Hier erfahren Sie, wie Sie zur Bereitstellung und Ausführung von SSIS-Paketen in Azure die Azure-SSIS-Integration Runtime in Azure Data Factory bereitstellen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 10/13/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 33f3f599eaf95852b52b5bd3301e44316d18cce5
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637019"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Bereitstellen der Azure-SSIS Integration Runtime in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial werden die Schritte für die Verwendung des Azure-Portals zum Bereitstellen einer Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) in Azure Data Factory (ADF) beschrieben. Die Azure-SSIS IR unterstützt Folgendes:

- Ausführen von Paketen, die im SSIS-Katalog (SSISDB) bereitgestellt werden, wobei zum Hosten ein Azure SQL-Datenbank-Server/eine verwaltete Instanz verwendet wird (Projektbereitstellungsmodell)
- Ausführen von Paketen, die im Dateisystem, in Azure Files oder SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten eine verwaltete Azure SQL-Instanz verwendet wird (Paketbereitstellungsmodell)

Nach der Bereitstellung einer Azure-SSIS IR können Sie die vertrauten Tools nutzen, um Ihre Pakete in Azure bereitzustellen und auszuführen. Diese Tools sind bereits Azure-fähig und enthalten SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) sowie Befehlszeilen-Hilfsprogramme wie [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) und [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Konzeptionelle Informationen zu Azure-SSIS IRs finden Sie unter [Azure-SSIS-Integrationslaufzeit](concepts-integration-runtime.md#azure-ssis-integration-runtime).

In diesem Tutorial führen Sie folgende Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Bereitstellen einer Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement** . Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- **Azure SQL-Datenbank-Server (optional)** . Wenn Sie noch nicht über einen Datenbankserver verfügen, erstellen Sie einen im Azure-Portal, bevor Sie beginnen. Mit Data Factory wird wiederum eine SSISDB-Instanz auf diesem Datenbankserver erstellt. 

  Es empfiehlt sich, den Datenbankserver in derselben Azure-Region zu erstellen, in der sich auch die Integration Runtime befindet. Diese Konfiguration ermöglicht es der Integration Runtime, Ausführungsprotokolle in SSISDB zu schreiben, ohne Azure-Regionen zu überschreiten.

  Beachten Sie Folgendes:

  - Basierend auf dem ausgewählten Datenbankserver kann die SSISDB-Instanz in Ihrem Namen als Einzeldatenbank, als Teil eines Pools für elastische Datenbanken oder auf einer verwalteten Instanz erstellt werden. Der Zugriff kann über ein öffentliches Netzwerk oder durch den Beitritt zu einem virtuellen Netzwerk erfolgen. Einen Leitfaden zum Auswählen des Datenbankservertyps zum Hosten von SSISDB finden Sie unter [Vergleich zwischen SQL-Datenbank und einer verwalteten SQL-Instanz](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen, müssen Sie Ihre Azure-SSIS IR-Instanz mit einem virtuellen Netzwerk verknüpfen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory](./create-azure-ssis-integration-runtime.md).

  - Vergewissern Sie sich, dass die Einstellung **Zugriff auf Azure-Dienste zulassen** für den Datenbankserver aktiviert ist. Diese Einstellung gilt nicht, wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden. Weitere Informationen finden Sie unter [Schützen der Azure SQL-Datenbank](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Informationen zum Aktivieren dieser Einstellung mithilfe von PowerShell finden Sie unter [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Fügen Sie der Client-IP-Adressenliste in den Firewalleinstellungen für den Datenbankserver die IP-Adresse des Clientcomputers oder einen IP-Adressbereich hinzu, der die IP-Adresse des Clientcomputers enthält. Weitere Informationen finden Sie unter [Firewallregeln auf Serverebene und Datenbankebene für Azure SQL-Datenbank](../azure-sql/database/firewall-configure.md).

  - Sie können eine Verbindung mit dem Datenbankserver herstellen, indem Sie die SQL-Authentifizierung mit Ihren Serveradministrator-Anmeldeinformationen oder die Azure AD-Authentifizierung mit der verwalteten Identität für Ihre Data Factory-Instanz verwenden. Im letzteren Fall müssen Sie die verwaltete Identität für Ihre Data Factory-Instanz einer Azure AD-Gruppe mit Zugriffsberechtigungen für den Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory](./create-azure-ssis-integration-runtime.md).

  - Vergewissern Sie sich, dass Ihr Datenbankserver noch nicht über eine SSISDB-Instanz verfügt. Für die Bereitstellung einer Azure-SSIS IR wird die Verwendung einer vorhandenen SSISDB-Instanz nicht unterstützt.

> [!NOTE]
> Eine Liste mit den Azure-Regionen, in denen Data Factory und eine Azure-SSIS Integration Runtime derzeit verfügbar sind, finden Sie unter [Data Factory und SSIS IR: Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Befolgen Sie die Schritt-für-Schritt-Anleitung unter [Erstellen einer Data Factory](./quickstart-create-data-factory-portal.md#create-a-data-factory), um Ihre Data Factory-Instanz über das Azure-Portal zu erstellen. Wählen Sie hierbei die Option **An Dashboard anheften** aus, um nach der Erstellung den schnellen Zugriff zu ermöglichen. 

Öffnen Sie nach der Erstellung Ihrer Data Factory-Instanz im Azure-Portal die zugehörige Übersichtsseite. Wählen Sie die Kachel **Erstellen und überwachen** aus, um die Seite **Erste Schritte** auf einer separaten Registerkarte zu öffnen. Sie können darauf dann mit der Erstellung Ihrer Azure-SSIS IR fortfahren.

## <a name="create-an-azure-ssis-integration-runtime"></a>Erstellen einer Azure SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Über die Seite mit der Übersicht über Data Factory

1. Wählen Sie auf der Seite **Let's get started** (Erste Schritte) die Kachel **Configure SSIS Integration Runtime** (SSIS Integration Runtime konfigurieren). 

   ![Kachel „Configure SSIS Integration Runtime“ (SSIS Integration Runtime konfigurieren)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Die übrigen Schritte zum Einrichten einer Azure-SSIS IR finden Sie im Abschnitt [Bereitstellen einer Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Über die Benutzeroberfläche für die Erstellung

1. Wechseln Sie auf der Azure Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten** , und wählen Sie **Verbindungen** aus. Wechseln Sie anschließend zur Registerkarte **Integration Runtimes** , um die vorhandenen Integration Runtimes Ihrer Data Factory-Instanz anzuzeigen. 

   ![Auswahl zum Anzeigen von vorhandenen IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Wählen Sie **Neu** aus, um eine Azure-SSIS IR zu erstellen, und öffnen Sie den Bereich **Integration Runtime-Setup** . 

   ![Integration Runtime über das Menü](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Wählen Sie im Bereich **Integration Runtime-Setup** die Kachel **Migrieren vorhandener SSIS-Pakete per Lift & Shift für die Ausführung in Azure** und anschließend **Weiter** aus.

   ![Angeben des Integration Runtime-Typs](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Die übrigen Schritte zum Einrichten einer Azure-SSIS IR finden Sie im Abschnitt [Bereitstellen einer Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Bereitstellen einer Azure-SSIS Integration Runtime

Der Bereich **Integration Runtime-Setup** enthält drei Seiten, auf denen Sie nacheinander allgemeine Einstellungen, Bereitstellungseinstellungen und erweiterte Einstellungen konfigurieren.

### <a name="general-settings-page"></a>Seite „Allgemeine Einstellungen“

Führen Sie auf der Seite **Allgemeine Einstellungen** des Bereichs **Integration Runtime-Setup** die folgenden Schritte aus. 

   ![Allgemeine Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Geben Sie unter **Name** den Namen Ihrer Integration Runtime ein. 

   1. Geben Sie unter **Beschreibung** die Beschreibung Ihrer Integration Runtime ein. 

   1. Wählen Sie unter **Standort** den Standort für Ihre Integration Runtime aus. Es werden nur unterstützte Standorte angezeigt. Sie sollten den gleichen Standort wie für den Datenbankserver zum Hosten von SSISDB auswählen. 

   1. Wählen Sie unter **Knotengröße** die Größe des Knotens in Ihrem Integration Runtime-Cluster aus. Nur unterstützte Knotengrößen werden angezeigt. Wählen Sie eine große Knotengröße (Hochskalieren) aus, wenn Sie zahlreiche compute- oder arbeitsspeicherintensive Pakete ausführen möchten. 

   1. Wählen Sie unter **Knotenzahl** die Anzahl von Knoten in Ihrer Integration Runtime aus. Nur unterstützte Knotenzahlen werden angezeigt. Wählen Sie einen großen Cluster mit zahlreichen Knoten (Aufskalieren) aus, wenn Sie viele Pakete parallel ausführen möchten. 

   1. Wählen Sie unter **Edition/Lizenz** die SQL Server-Edition für Ihre Integration Runtime aus: Standard oder Enterprise. Wählen Sie Enterprise aus, wenn Sie erweiterte Funktionen für Ihre Integration Runtime verwenden möchten. 

   1. Wählen Sie unter **Sparen Sie Geld** die Option „Azure-Hybridvorteil“ für Ihre Integration Runtime aus: **Ja** oder **Nein** . Wählen Sie **Ja** , wenn Sie eine eigene SQL Server-Lizenz mit Software Assurance verwenden möchten, um bei der Hybridnutzung von Kostenersparnissen zu profitieren. 

   1. Wählen Sie **Weiter** aus. 

### <a name="deployment-settings-page"></a>Seite „Bereitstellungseinstellungen“

Auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) können Sie SSISDB- und/oder Azure-SSIS IR-Paketspeicher erstellen.

#### <a name="creating-ssisdb"></a>Erstellen von SSISDB

Wenn Sie Ihre Pakete in SSISDB bereitstellen möchten (Projektbereitstellungsmodell), aktivieren Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) das Kontrollkästchen **Vom Azure SQL-Datenbank-Server/von der verwalteten Instanz gehosteten SSIS-Katalog (SSISDB) zum Speichern Ihrer Projekte/Pakete/Umgebungen/Ausführungsprotokolle erstellen** . Wenn Sie Ihre Pakete im Dateisystem, in Azure Files oder SQL Server-Datenbank (MSDB) bereitstellen möchten, das bzw. die von Azure SQL Managed Instance gehostet wird (Paketbereitstellungsmodell), müssen Sie weder eine SSISDB erstellen noch das Kontrollkästchen aktivieren.

Aktivieren Sie dieses Kontrollkästchen unabhängig von Ihrem Bereitstellungsmodell, wenn Sie den von Azure SQL Managed Instance gehosteten SQL Server-Agent zum Orchestrieren/Planen Ihrer Paketausführungen verwenden möchten, da dies durch SSISDB aktiviert wird. Weitere Informationen finden Sie unter [Planen der Ausführung von SSIS-Paketen mit einem Agent für die verwaltete Azure SQL-Instanz](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Wenn Sie das Kontrollkästchen aktivieren, führen Sie die folgenden Schritte aus, um Ihren eigenen Datenbankserver zum Hosten der SSISDB bereitzustellen, die wir in Ihrem Namen erstellen und verwalten.

   ![Bereitstellungseinstellungen für SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Geben Sie unter **Abonnement** das Azure-Abonnement mit dem Azure-Datenbankserver zum Hosten von SSISDB an. 

   1. Wählen Sie als **Standort** den Standort des Datenbankservers zum Hosten von SSISDB aus. Sie sollten denselben Standort wie den für die Integration Runtime auswählen.

   1. Wählen Sie unter **Katalogdatenbank-Serverendpunkt** den Endpunkt Ihres Datenbankservers zum Hosten von SSISDB aus. 
   
      Basierend auf dem ausgewählten Datenbankserver kann die SSISDB-Instanz in Ihrem Namen als Einzeldatenbank, als Teil eines Pools für elastische Datenbanken oder auf einer verwalteten Instanz erstellt werden. Der Zugriff kann über ein öffentliches Netzwerk oder durch den Beitritt zu einem virtuellen Netzwerk erfolgen. Einen Leitfaden zum Auswählen des Datenbankservertyps zum Hosten von SSISDB finden Sie unter [Vergleich zwischen SQL-Datenbank und einer verwalteten SQL-Instanz](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Wenn Sie einen Azure SQL-Datenbankserver mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB auswählen oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen, müssen Sie Ihre Azure-SSIS IR-Instanz einem virtuellen Netzwerk hinzufügen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory](./create-azure-ssis-integration-runtime.md).

   1. Aktivieren Sie das Kontrollkästchen **Use Azure AD authentication with the managed identity for your ADF** (Azure AD-Authentifizierung mit der verwalteten Identität für Ihre ADF verwenden), um die Authentifizierungsmethode für Ihren Datenbankserver zum Hosten der SSISDB auszuwählen. Sie wählen entweder die SQL-Authentifizierung oder die Azure AD-Authentifizierung mit der verwalteten Identität für Ihre Data Factory-Instanz aus.

      Wenn Sie das Kontrollkästchen aktivieren, müssen Sie die verwaltete Identität für Ihre Data Factory-Instanz einer Azure AD-Gruppe mit Zugriffsberechtigungen für Ihren Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory](./create-azure-ssis-integration-runtime.md).
   
   1. Geben Sie unter **Administratorbenutzername** den Benutzernamen für die SQL-Authentifizierung für den Datenbankserver zum Hosten der SSISDB an. 

   1. Geben Sie unter **Administratorbenutzerkennwort** das Kennwort für die SQL-Authentifizierung für den Datenbankserver zum Hosten der SSISDB an. 

   1. Wählen Sie unter **Katalogdatenbank-Dienstebene** die Dienstebene für Ihren Datenbankserver zum Hosten der SSISDB aus. Wählen Sie den Tarif „Basic“, „Standard“ oder „Premium“ oder den Namen eines Pools für elastische Datenbanken aus.

Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Weiter** aus.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Erstellen von Azure-SSIS IR-Paketspeichern

Aktivieren Sie auf der Seite **Bereitstellungseinstellungen** des Bereichs **Integration runtime setup** (Integration Runtime-Setup) das Kontrollkästchen **Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance** (Paketspeicher zum Verwalten Ihrer Pakete erstellen, die im Dateisystem/in Azure Files/der SQL Server-Datenbank (MSDB) bereitgestellt werden, wobei zum Hosten Azure SQL Managed Instance verwendet wird), wenn Sie Ihre in MSDB, im Dateisystem oder in Azure Files (Paketbereitstellungsmodell) bereitgestellten Pakete mit Azure-SSIS IR-Paketspeichern verwalten möchten.
   
Ein Azure-SSIS IR-Paketspeicher ermöglicht Ihnen das Importieren/Exportieren/Löschen/Ausführen von Paketen und das Überwachen/Beenden der Ausführung von Paketen über SSMS, ähnlich wie im [Legacy-SSIS-Paketspeicher](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017). Weitere Informationen finden Sie unter [Verwalten von SSIS-Paketen mit Azure-SSIS IR-Paketspeichern](./azure-ssis-integration-runtime-package-store.md).
   
Wenn Sie dieses Kontrollkästchen aktivieren, können Sie Ihrer Azure-SSIS IR mehrere Paketspeicher hinzufügen, indem Sie **Neu** auswählen. Umgekehrt kann ein einziger Paketspeicher von mehreren Azure-SSIS IRs gemeinsam genutzt werden.

![Bereitstellungseinstellungen für MSDB/Dateisystem/Azure Files](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

Führen Sie im Bereich **Paketspeicher hinzufügen** die folgenden Schritte aus.
   
   1. Geben Sie unter **Name des Paketspeichers** den Namen Ihres Paketspeichers ein. 

   1. Wählen Sie unter **Mit Paketspeicher verknüpfter Dienst** Ihren vorhandenen verknüpften Dienst zum Speichern der Zugriffsinformationen für das Dateisystem/Azure Files/die verwaltete Azure SQL-Instanz aus, in dem bzw. der Ihre Pakete bereitgestellt werden, oder erstellen Sie einen neuen Dienst, indem Sie **Neu** auswählen. Führen Sie im Bereich **Neuer verknüpfter Dienst** die folgenden Schritte aus. 

      > [!NOTE]
      > Für den Zugriff auf Azure Files können Sie die verknüpften Dienste **Azure File Storage** oder **Dateisystem** verwenden. Wenn Sie den verknüpften Dienst **Azure File Storage** verwenden, unterstützt der Azure-SSIS IR-Paketspeicher derzeit nur die **Standardauthentifizierungsmethode** . ( **Kontoschlüssel** und **SAS-URI** werden nicht unterstützt.) Wenn Sie die **Standardauthentifizierung** für den verknüpften Dienst **Azure File Storage** nutzen möchten, können Sie `?feature.upgradeAzureFileStorage=false` an die ADF-Portal-URL in Ihrem Browser anfügen. Alternativ können Sie den verknüpften Dienst **Dateisystem** verwenden, um stattdessen auf Azure Files zuzugreifen. 

      ![Bereitstellungseinstellungen für verknüpfte Dienste](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Geben Sie unter **Name** den Namen Ihres verknüpften Diensts ein. 
         
      1. Geben Sie unter **Beschreibung** die Beschreibung Ihres verknüpften Diensts ein. 
         
      1. Wählen Sie unter **Typ** einen der Typen **Azure File Storage** , **Verwaltete Azure SQL-Instanz** oder **Dateisystem** aus.

      1. Sie können **Verbinden über Integration Runtime** ignorieren, da wir immer Ihre Azure-SSIS IR zum Abrufen der Zugriffsinformationen für Paketspeicher verwenden.

      1. Wenn Sie **Azure File Storage** auswählen, führen Sie die folgenden Schritte aus. 

         1. Wählen Sie unter **Kontoauswahlmethode** eine der Methoden **Aus Azure-Abonnement** oder **Manuell eingeben** aus.
         
         1. Wenn Sie **Aus Azure-Abonnement** auswählen, wählen Sie die entsprechenden Werte für **Azure-Abonnement** , **Speicherkontoname** und **Dateifreigabe** aus.
            
         1. Wenn Sie **Manuell eingeben** auswählen, geben Sie unter **Host** `\\<storage account name>.file.core.windows.net\<file share name>`, unter **Benutzername** `Azure\<storage account name>` und unter **Kennwort** `<storage account key>` ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wenn Sie **Verwaltete Azure SQL-Instanz** auswählen, führen Sie die folgenden Schritte aus. 

         1. Wählen Sie **Verbindungszeichenfolge** aus, um sie manuell einzugeben, oder Ihren **Azure Key Vault** aus, in dem sie als Geheimnis gespeichert ist.
         
         1. Wenn Sie **Verbindungszeichenfolge** auswählen, führen Sie die folgenden Schritte aus. 

            1. Geben Sie unter **Vollqualifizierter Domänenname** `<server name>.<dns prefix>.database.windows.net` oder `<server name>.public.<dns prefix>.database.windows.net,3342` als privaten bzw. öffentlichen Endpunkt Ihrer verwalteten Azure SQL-Instanz ein. Wenn Sie den privaten Endpunkt eingeben, kann **Verbindung testen** nicht angewendet werden, da dieser Endpunkt über die ADF-Benutzeroberfläche nicht erreicht werden kann.

            1. Geben Sie unter **Datenbankname** `msdb` ein.
               
            1. Wählen Sie unter **Authentifizierungstyp** einen der Typen **SQL-Authentifizierung** , **Verwaltete Identität** oder **Dienstprinzipal** aus.

            1. Wenn Sie **SQL-Authentifizierung** auswählen, geben Sie die entsprechenden Werte für **Benutzername** und **Kennwort** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

            1. Wenn Sie **Verwaltete Identität** auswählen, gewähren Sie Ihrer verwalteten ADF-Identität Zugriff auf Ihre verwaltete Azure SQL-Instanz.

            1. Wenn Sie **Dienstprinzipal** auswählen, geben Sie die entsprechenden Werte für **Dienstprinzipal-ID** und **Dienstprinzipalschlüssel** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wenn Sie **Dateisystem** auswählen, geben Sie den UNC-Pfad des Ordners, in dem Ihre Pakete für **Host** bereitgestellt werden, sowie die entsprechenden Werte für **Benutzername** und **Kennwort** ein, oder wählen Sie Ihren **Azure Key Vault** aus, in dem diese Werte als Geheimnis gespeichert sind.

      1. Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Erstellen** aus.

   1. Ihre hinzugefügten Paketspeicher werden auf der Seite **Bereitstellungseinstellungen** angezeigt. Wenn Sie sie entfernen möchten, können Sie die entsprechenden Kontrollkästchen aktivieren und dann **Löschen** auswählen.

Wählen Sie ggf. **Verbindung testen** und – bei erfolgreichem Test – **Weiter** aus.

### <a name="advanced-settings-page"></a>Seite „Erweiterte Einstellungen“

Führen Sie auf der Seite **Erweiterte Einstellungen** des Bereichs **Integration Runtime-Setup** die folgenden Schritte aus. 

   ![Erweiterte Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Legen Sie unter **Maximale Anzahl von parallelen Ausführungen pro Knoten** die maximale Anzahl von Paketen fest, die in Ihrem Integration Runtime-Cluster pro Knoten gleichzeitig ausgeführt werden sollen. Nur unterstützte Paketzahlen werden angezeigt. Wählen Sie eine niedrigere Anzahl aus, wenn Sie mehrere Kerne verwenden möchten, um ein einzelnes großes Paket auszuführen, das compute- oder arbeitsspeicherintensiv ist. Wählen Sie eine hohe Anzahl aus, wenn Sie mindestens ein kleines Paket in einem einzelnen Kern ausführen möchten. 

   1. Aktivieren Sie das Kontrollkästchen **Azure-SSIS IR mit zusätzlichen Systemkonfigurationen/Komponenteninstallationen anpassen** , um festzulegen, ob Sie benutzerdefinierte Standard-/Express-Setups für Ihre Azure-SSIS IR-Instanz hinzufügen möchten. Weitere Informationen finden Sie unter [Anpassen des Setups für Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. Aktivieren Sie das Kontrollkästchen **Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses** (VNET für die Einbindung Ihrer Azure-SSIS Integration Runtime-Instanz auswählen, Erstellung bestimmter Netzwerkressourcen für ADF ermöglichen und optional eigene statische öffentliche IP-Adressen verwenden), um auszuwählen, ob Sie Ihre Azure-SSIS IR-Instanz in ein virtuelles Netzwerk einbinden möchten.

      Aktivieren Sie dieses Kontrollkästchen, wenn Sie einen Azure SQL-Datenbank-Server mit IP-Firewallregeln/VNET-Dienstendpunkten oder eine verwaltete Instanz mit privatem Endpunkt zum Hosten der SSISDB verwenden oder Zugriff auf lokale Daten ohne Konfiguration einer selbstgehosteten IR benötigen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory](./create-azure-ssis-integration-runtime.md). 
   
   1. Aktivieren Sie das Kontrollkästchen **Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime** (Selbstgehostete Integration Runtime als Proxy für Ihre Azure-SSIS Integration Runtime-Instanz einrichten), um auszuwählen, ob Sie eine selbstgehostete IR als Proxy für Ihre Azure-SSIS IR-Instanz konfigurieren möchten. Weitere Informationen finden Sie unter [Konfigurieren einer selbstgehosteten IR als Proxy für Azure-SSIS IR in ADF](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. Wählen Sie **Weiter** . 

Überprüfen Sie im Bereich **Integration Runtime-Setup**  auf der Seite **Zusammenfassung** alle Bereitstellungseinstellungen, fügen Sie Textmarken für die empfohlenen Dokumentationslinks hinzu, und wählen Sie **Fertig stellen** aus, um die Erstellung Ihrer Integration Runtime zu starten. 

   > [!NOTE]
   > Abgesehen von einer benutzerdefinierten Einrichtungszeit sollte dieser Prozess innerhalb von fünf Minuten abgeschlossen sein.
   >
   > Bei Verwendung der SSISDB stellt der Data Factory-Dienst eine Verbindung mit Ihrem Datenbankserver her, um die SSISDB vorzubereiten. 
   > 
   > Wenn Sie Azure-SSIS IR bereitstellen, werden auch die Access Redistributable-Komponente und das Azure Feature Pack für SSIS installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-Dateien, Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten bereits unterstützt werden. Weitere Informationen zu integrierten/vorinstallierten Komponenten finden Sie unter [Integrierte/vorinstallierte Komponenten für Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Weitere Informationen zu zusätzlichen Komponenten, die Sie installieren können, finden Sie unter [Custom setups for Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md) (Benutzerdefinierte Setups for Azure-SSIS IR).

### <a name="connections-pane"></a>Bereich „Verbindungen“

Wechseln Sie im Bereich **Verbindungen** von **Verwaltungshub** zur Seite **Integration Runtimes** , und wählen Sie **Aktualisieren** aus. 

   ![Bereich „Verbindungen“](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Sie können Ihre Azure-SSIS IR bearbeiten/neu konfigurieren, indem Sie deren Namen auswählen. Sie können auch die entsprechenden Schaltflächen zum Überwachen/Starten/Beenden/Löschen Ihrer Azure-SSIS IR, zum automatischen Generieren einer ADF-Pipeline mit der Aktivität „SSIS-Paket ausführen“ zur Ausführung in Ihrer Azure-SSIS IR und zum Anzeigen des JSON-Codes bzw. der Nutzlast Ihrer Azure-SSIS IR auswählen.  Ihre Azure-SSIS IR kann nur bearbeitet/gelöscht werden, wenn sie angehalten wurde.

## <a name="deploy-ssis-packages"></a>Bereitstellen von SSIS-Paketen

Wenn Sie SSISDB verwenden, können Sie Ihre Pakete darin bereitstellen und sie mithilfe der Azure-fähigen SSDT- oder SSMS-Tools in Ihrer Azure-SSIS IR ausführen. Mit diesen Tools wird über den zugehörigen Serverendpunkt eine Verbindung mit Ihrem Datenbankserver hergestellt: 

- Bei einem Azure SQL-Datenbankserver weist der Serverendpunkt das Format `<server name>.database.windows.net` auf.
- Bei einer verwalteten Instanz mit privatem Endpunkt weist der Serverendpunkt das Format `<server name>.<dns prefix>.database.windows.net` auf.
- Bei einer verwalteten Instanz mit öffentlichem Endpunkt weist der Serverendpunkt das Format `<server name>.public.<dns prefix>.database.windows.net,3342` auf. 

Wenn Sie SSISDB nicht verwenden, können Sie Ihre Pakete im Dateisystem, in Azure Files oder der MSDB bereitstellen, das bzw. die von Ihrer Azure SQL Managed Instance gehostet wird, und sie mit den Befehlszeilen-Hilfsprogrammen [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) und [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) in Ihrer Azure-SSIS IR ausführen. 

Weitere Informationen finden Sie unter [Bereitstellen von SQL Server Integration Services-Projekten und -Paketen (SSIS)](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages?view=sql-server-ver15).

In beiden Fällen können Sie Ihre bereitgestellten Pakete auch in der Azure-SSIS IR-Instanz ausführen, indem Sie die Aktivität „SSIS-Paket ausführen“ in Data Factory-Pipelines verwenden. Weitere Informationen finden Sie unter [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

Sehen Sie sich auch die folgende SSIS-Dokumentation an: 

- [Bereitstellen und Ausführen eines SSIS-Pakets in Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Herstellen einer Verbindung mit dem SSIS-Katalog (SSISDB) in Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Planen der Ausführung von in Azure bereitgestellten SSIS-Paketen](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung) 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anpassen Ihrer Azure-SSIS Integration Runtime finden Sie im folgenden Artikel: 

> [!div class="nextstepaction"]
> [Anpassen des Setups für Azure-SSIS Integration Runtime](./how-to-configure-azure-ssis-ir-custom-setup.md)