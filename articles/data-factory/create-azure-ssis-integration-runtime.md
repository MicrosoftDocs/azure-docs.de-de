---
title: Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie zur Bereitstellung und Ausführung von SSIS-Paketen in Azure eine Azure-SSIS Integration Runtime in Azure Data Factory erstellen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d30ec0765627ec173f0027e49f44cb77f6b26ac6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66155195"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory

In diesem Artikel werden die Schritte für die Bereitstellung von Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF) beschrieben. Anschließend können Sie SQL Server Data Tools (SSDT) oder SQL Server Management Studio (SSMS) zum Bereitstellen und Ausführen von SQL Server Integration Services-Paketen (SSIS) in dieser Integration Runtime in Azure verwenden.

Im [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-create-azure-ssis-runtime-portal.md) erfahren Sie, wie Sie Azure-SSIS IR mithilfe eines Azure SQL-Datenbank-Servers zum Hosten der SSIS-Katalogdatenbank (SSISDB) erstellen. In diesem Artikel wird das Tutorial vertieft und erläutert, wie Sie die folgenden Aktionen ausführen:

- Verwenden Sie optional einen Azure SQL-Datenbank-Server mit VNET-Dienstendpunkten oder einer verwalteten Instanz zum Hosten von SSISDB. Einen Leitfaden zum Auswählen des Datenbankservertyps zum Hosten von SSISDB finden Sie unter [Vergleich zwischen Azure SQL-Datenbank-Server mit Einzeldatenbank/Pool für elastische Datenbanken und verwalteter SQL-Datenbank-Instanz](create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Als Voraussetzung müssen Sie Ihre Azure-SSIS IR mit einem virtuellen Netzwerk verknüpfen und Einstellungen/Berechtigungen für virtuelle Netzwerke wie erforderlich konfigurieren. Weitere Informationen finden Sie unter [Verknüpfen der Azure SSIS-IR mit einem virtuellen Netzwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

- Verwenden Sie optional Azure Active Directory-Authentifizierung (AAD) mit der verwalteten Identität für Ihre ADF, um eine Verbindung mit dem Datenbankserver herzustellen. Als Voraussetzung hierfür müssen Sie die verwaltete Identität für Ihre ADF als einen Benutzer für eigenständige Datenbank hinzufügen, der SSISDB auf Ihrem Azure SQL-Datenbank-Server bzw. in Ihrer verwalteten Instanz erstellen kann. Informationen dazu finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung für die Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

## <a name="overview"></a>Übersicht

Dieser Artikel zeigt verschiedene Methoden für die Bereitstellung einer Azure-SSIS IR auf:

- [Azure-Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure Resource Manager-Vorlage](#azure-resource-manager-template)

Wenn Sie Azure-SSIS IR erstellen, stellt der ADF-Dienst zur Vorbereitung von SSISDB eine Verbindung mit Ihrem Azure SQL-Datenbank-Server bzw. Ihrer verwalteten Instanz her. Außerdem konfiguriert er Berechtigungen/Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die Azure-SSIS IR mit dem virtuellen Netzwerk.

Wenn Sie Azure-SSIS IR bereitstellen, werden auch das Azure Feature Pack für SSIS und die Access Redistributable-Komponente installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel-/Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von integrierten Komponenten unterstützt werden. Sie können auch weitere Komponenten installieren. Weitere Informationen finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement**. Falls Sie noch nicht über ein Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

- **Azure SQL-Datenbank-Server oder verwaltete Instanz**. Wenn Sie noch nicht über einen Datenbankserver verfügen, können Sie einen im Azure-Portal erstellen, bevor Sie beginnen. Dieser Server hostet dann SSISDB. Es empfiehlt sich, den Datenbankserver in derselben Azure-Region zu erstellen, in der sich auch die Integration Runtime befindet. Diese Konfiguration ermöglicht es der Integration Runtime, Ausführungsprotokolle in SSISDB zu schreiben, ohne Azure-Regionen zu überschreiten. Basierend auf dem ausgewählten Datenbankserver kann SSISDB in Ihrem Auftrag als einzelne Datenbank, als Teil eines Pools für elastische Datenbanken oder in Ihrer verwalteten Instanz erstellt werden und in einem öffentlichen Netzwerk oder durch das Hinzufügen zu einem virtuellen Netzwerk zugänglich sein. Eine Liste mit den unterstützten Tarifen für Azure SQL-Datenbank finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](../sql-database/sql-database-resource-limits.md).

    Vergewissern Sie sich, dass der Azure SQL-Datenbank-Server/die verwaltete Instanz nicht bereits über eine SSIDB verfügt. Für die Bereitstellung der Azure-SSIS IR wird die Verwendung einer vorhandenen SSISDB nicht unterstützt.

- **Virtuelles Azure Resource Manager-Netzwerk (optional)**. Sie benötigen ein virtuelles Azure Resource Manager-Netzwerk, wenn mindestens eine der folgenden Bedingungen zutrifft:

  - Sie hosten SSISDB auf einem Azure SQL-Datenbank-Server mit VNET-Dienstendpunkten oder in einer verwalteten Instanz, die sich in einem virtuellen Netzwerk befindet.
  - Sie möchten aus SSIS-Paketen, die in der Azure-SSIS IR ausgeführt werden, eine Verbindung mit lokalen Datenspeichern herstellen.

- **Azure PowerShell**. Folgen Sie den Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps), wenn Sie ein PowerShell-Skript zur Bereitstellung von Azure-SSIS IR ausführen möchten.

### <a name="region-support"></a>Unterstützung für Regionen

Eine Liste mit den Azure-Regionen, in denen ADF und Azure-SSIS IR zurzeit verfügbar sind, finden Sie unter [ADF- und SSIS IR-Verfügbarkeit nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Vergleich zwischen SQL-Datenbank mit Einzeldatenbank/Pool für elastische Datenbanken und verwalteter SQL-Datenbank-Instanz

In der folgenden Tabelle werden bestimmte Features des Azure SQL-Datenbank-Servers und der verwalteten Instanz in Bezug auf Azure-SSIR IR verglichen:

| Feature | Einzeldatenbank/Pool für elastische Datenbanken| Verwaltete Instanz |
|---------|--------------|------------------|
| **Zeitplanung** | SQL Server-Agent ist nicht verfügbar.<br/><br/>Informationen finden Sie unter [Planen der Ausführung von Paketen in ADF-Pipeline](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent für die verwaltete Instanz ist verfügbar. |
| **Authentifizierung** | Sie können SSISDB mit einem Benutzer für eigenständige Datenbank erstellen, der eine beliebige AAD-Gruppe mit der verwalteten Identität Ihrer ADF als ein Mitglied in der Rolle **Db_owner** darstellt.<br/><br/>Informationen finden Sie unter [Aktivieren der Azure AD-Authentifizierung zum Erstellen von SSISDB auf einem Azure SQL-Datenbank-Server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Sie können SSISDB mit einem Benutzer für eigenständige Datenbank erstellen, der die verwaltete Identität Ihrer ADF darstellt. <br/><br/>Informationen finden Sie unter [Aktivieren der Azure AD-Authentifizierung zum Erstellen von SSISDB in einer verwalteten Azure SQL-Datenbank-Instanz](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Dienstebene** | Wenn Sie Azure-SSIS IR mit Ihrem Azure SQL-Datenbank-Server erstellen, können Sie die Dienstebene für SSISDB auswählen. Es gibt mehrere Dienstebenen. | Wenn Sie Azure-SSIS IR mit Ihrer verwalteten Instanz erstellen, können Sie die Dienstebene für SSISDB nicht auswählen. Alle Datenbanken in Ihrer verwalteten Instanz nutzen dieselbe Ressource, die dieser Instanz zugeordnet ist, gemeinsam. |
| **Virtuelles Netzwerk** | Unterstützt nur virtuelle Azure Resource Manager-Netzwerke, denen Ihre Azure-SSIS IR beitreten kann, wenn Sie den Azure SQL-Datenbank-Server mit VNET-Dienstendpunkten verwenden oder Zugriff auf lokale Datenspeicher benötigen. | Unterstützt nur virtuelle Azure Resource Manager-Netzwerke, denen Ihre Azure-SSIS IR beitreten kann. Das virtuelle Netzwerk ist immer erforderlich.<br/><br/>Wenn Sie Ihre Azure-SSIS IR mit demselben virtuellen Netzwerk verknüpfen wie Ihre verwaltete Instanz, stellen Sie sicher, dass sich Ihre Azure-SSIS IR in einem anderen Subnetz als Ihre verwaltete Instanz befindet. Wenn Sie Ihre Azure-SSIS IR mit einem anderen virtuellen Netzwerk verknüpfen als Ihre verwaltete Instanz, empfiehlt sich ein Peering virtueller Netzwerke oder eine Verbindung zwischen den virtuellen Netzwerken. Weitere Informationen finden Sie unter [Herstellen einer Verbindung zwischen einer Anwendung und einer verwalteten Azure SQL-Datenbank-Instanz](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Verteilte Transaktionen** | Unterstützt über elastische Transaktionen. MS DTC-Transaktionen (Microsoft Distributed Transaction Coordinator) werden nicht unterstützt. Wenn Ihre SSIS-Pakete MSDTC verwenden, um verteilte Transaktionen zu koordinieren, sollten Sie eine Migration zu elastischen Transaktionen für Azure SQL-Datenbank erwägen. Weitere Informationen finden Sie unter [Verteilte Transaktionen über Clouddatenbanken](../sql-database/sql-database-elastic-transactions-overview.md). | Nicht unterstützt. |
| | | |

## <a name="azure-portal"></a>Azure-Portal

In diesem Abschnitt erstellen Sie mithilfe des Azure-Portals (insbesondere der ADF-Benutzeroberfläche/App) eine Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Klicken Sie im Menü auf der linken Seite nacheinander auf **Neu**, **Data + Analytics** und **Data Factory**.

   ![Neu -> Data Factory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

1. Geben Sie auf der Seite **Neue Data Factory** unter **Name** die Zeichenfolge **MyAzureSsisDataFactory** ein.

   ![Seite „Neue Data Factory“](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Der Name der Azure Data Factory muss **global eindeutig**sein. Sollte der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>MyAzureSsisDataFactory“), und wiederholen Sie den Vorgang. Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).

   `Data factory name “MyAzureSsisDataFactory” is not available`

1. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll.
1. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:

   - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe.
   - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.

   Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/resource-group-overview.md).

1. Wählen Sie **V2** als **Version** aus.
1. Wählen Sie den **Standort** für die Data Factory aus. Die Liste enthält nur Standorte, die für die Erstellung von Data Factorys geeignet sind.
1. Wählen Sie die Option **An Dashboard anheften** aus.
1. Klicken Sie auf **Create**.
1. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying data factory** (Data Factory wird bereitgestellt...).

    ![Kachel „Die Data Factory wird bereitgestellt“](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

1. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.

    ![Data Factory-Startseite](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

1. Klicken Sie auf **Erstellen und überwachen**, um die Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten.

### <a name="provision-an-azure-ssis-integration-runtime"></a>Bereitstellen einer Azure SSIS Integration Runtime

1. Klicken Sie auf der Seite „Erste Schritte“ auf die Kachel **Configure SSIS Integration Runtime** (SSIS Integration Runtime konfigurieren).

   ![Kachel „Configure SSIS Integration Runtime“ (SSIS Integration Runtime konfigurieren)](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Führen Sie auf der Seite **Allgemeine Einstellungen** von **Integration Runtime Setup** (Integration Runtime-Setup) die folgenden Schritte aus:

   ![Allgemeine Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Geben Sie unter **Name** den Namen Ihrer Integration Runtime ein.

    b. Geben Sie unter **Beschreibung** die Beschreibung Ihrer Integration Runtime ein.

    c. Wählen Sie unter **Standort** den Standort für Ihre Integration Runtime aus. Es werden nur unterstützte Standorte angezeigt. Sie sollten den gleichen Standort wie für den Datenbankserver zum Hosten von SSISDB auswählen.

    d. Wählen Sie unter **Knotengröße** die Größe des Knotens in Ihrem Integration Runtime-Cluster aus. Nur unterstützte Knotengrößen werden angezeigt. Wählen Sie eine große Knotengröße (zentrales Hochskalieren) aus, wenn Sie zahlreiche compute-/arbeitsspeicherintensive Pakete ausführen möchten.

    e. Wählen Sie unter **Knotenzahl** die Anzahl von Knoten in Ihrer Integration Runtime aus. Nur unterstützte Knotenzahlen werden angezeigt. Wählen Sie einen großen Cluster mit zahlreichen Knoten (horizontales Hochskalieren) aus, wenn Sie viele Pakete parallel ausführen möchten.

    f. Wählen Sie unter **Edition/Lizenz** die SQL Server-Edition/-Lizenz für Ihre Integration Runtime aus: Standard oder Enterprise. Wählen Sie Enterprise aus, wenn Sie erweiterte Funktionen oder Premium-Funktionen für Ihre Integration Runtime verwenden möchten.

    g. Wählen Sie unter **Sparen Sie Geld** die Option „Azure-Hybridvorteil“ (Azure Hybrid Benefit, AHB) für Ihre Integration Runtime aus: „Ja“ oder „Nein“. Wählen Sie „Ja“, wenn Sie eine eigene SQL Server-Lizenz mit Software Assurance verwenden möchten, um bei der Hybridnutzung von Kostenersparnissen zu profitieren.

    h. Klicken Sie auf **Weiter**.

3. Führen Sie auf der Seite **SQL-Einstellungen** die folgenden Schritte aus:

   ![SQL-Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. Geben Sie unter **Abonnement** das Azure-Abonnement mit dem Azure-Datenbankserver zum Hosten von SSISDB an.

    b. Wählen Sie als **Standort** den Standort des Datenbankservers zum Hosten von SSISDB aus. Sie sollten denselben Standort wie den für die Integration Runtime auswählen.

    c. Wählen Sie unter **Katalogdatenbank-Serverendpunkt** den Endpunkt Ihres Datenbankservers zum Hosten von SSISDB aus. Basierend auf dem ausgewählten Datenbankserver kann SSISDB in Ihrem Auftrag als einzelne Datenbank, als Teil eines Pools für elastische Datenbanken oder in einer verwalteten Instanz erstellt werden und in einem öffentlichen Netzwerk oder durch das Hinzufügen zu einem virtuellen Netzwerk zugänglich sein.

    d. Wählen Sie mit dem Kontrollkästchen **AAD-Authentifizierung verwenden...** die Authentifizierungsmethode für Ihren Datenbankserver zum Hosten von SSISDB aus: SQL oder Azure Active Directory (AAD) mit der verwalteten Identität für Ihre Azure Data Factory. Wenn Sie dieses Kontrollkästchen aktivieren, müssen Sie die verwaltete Identität für die ADF einer AAD-Gruppe mit Zugriffsberechtigungen für den Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Aktivieren der Azure Active Directory-Authentifizierung für die Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

    e. Geben Sie unter **Administratorbenutzername** den Benutzernamen für die SQL-Authentifizierung für den Datenbankserver zum Hosten von SSISDB an.

    f. Geben Sie unter **Administratorbenutzerkennwort** das Kennwort für die SQL-Authentifizierung für den Datenbankserver zum Hosten von SSISDB an.

    g. Wählen Sie unter **Katalogdatenbank-Dienstebene** die Dienstebene für Ihren Datenbankserver zum Hosten von SSISDB aus: Basic/Standard/Premium oder Name des Pools für elastische Datenbanken.

    h. Klicken Sie auf **Verbindung testen** und bei erfolgreichem Test auf **Weiter**.

4. Führen Sie auf der Seite **Erweiterte Einstellungen** die folgenden Schritte aus:

    ![Erweiterte Einstellungen](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Legen Sie unter **Maximale Anzahl von parallelen Ausführungen pro Knoten** die maximale Anzahl von Paketen fest, die in Ihrem Integration Runtime-Cluster pro Knoten gleichzeitig ausgeführt werden sollen. Nur unterstützte Paketzahlen werden angezeigt. Wählen Sie eine niedrigere Anzahl aus, wenn Sie mehrere Kerne verwenden möchten, um ein einzelnes großes/umfangreiches Paket auszuführen, das compute-/arbeitsspeicherintensiv ist. Wählen Sie eine hohe Anzahl aus, wenn Sie mindestens ein kleines Paket in einem einzelnen Kern ausführen möchten.

    b. Geben Sie unter **Container-SAS-URI des benutzerdefinierten Setups** optional den SAS-URI (Shared Access Signature, Uniform Resource Identifier) Ihres Azure Storage Blob-Containers an, in dem Ihr Einrichtungsskript und die zugehörigen Dateien gespeichert sind. Weitere Informationen finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS-Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Geben Sie mithilfe des Kontrollkästchens **Virtuelles Netzwerk auswählen...** an, ob Ihre Integration Runtime einem virtuellen Netzwerk hinzugefügt werden soll. Aktivieren Sie es, wenn Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten/mit verwalteter Instanz zum Hosten von SSISDB verwenden oder Zugriff auf lokale Daten benötigen, d. h. in Ihren SSIS-Paketen über lokale Datenquellen/-ziele verfügen. Weitere Informationen finden Sie unter [Verknüpfen von Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Falls Sie es nicht aktivieren, führen Sie die folgenden Schritte aus:

   ![Erweiterte Einstellungen mit virtuellem Netzwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Ihr virtuelles Netzwerk enthält.

    b. Für **Standort** ist derselbe Standort wie für Ihre Integration Runtime ausgewählt.

    c. Wählen Sie für **Typ** den Typ des virtuellen Netzwerks aus: „Klassisch“ oder „Azure Resource Manager“. Sie sollten das virtuelle Azure Resource Manager-Netzwerk auswählen, da das klassische virtuelle Netzwerk bald veraltet sein wird.

    d. Wählen Sie unter **VNET-Name** den Namen des virtuellen Netzwerks aus. Dieses virtuelle Netzwerk muss dasselbe virtuelle Netzwerk, das für Azure SQL-Datenbank mit VNET-Dienstendpunkten/mit verwalteter Instanz zum Hosten von SSISDB verwendet wird, und/oder das mit dem lokalen Netzwerk verbundene sein.

    e. Wählen Sie unter **Subnetzname** den Namen des Subnetzes für Ihr virtuelles Netzwerk aus. Dies muss ein anderes Subnetz als das für die verwaltete Instanz zum Hosten von SSISDB verwendete sein.

6. Klicken Sie auf **VNET-Überprüfung**, und falls diese erfolgreich verläuft, auf **Fertig stellen**, um die Erstellung Ihrer Azure-SSIS-Integration Runtime zu starten.

    > [!IMPORTANT]
    > - Dieser Prozess dauert etwa 20 bis 30 Minuten.
    > - Der Data Factory-Dienst stellt die Verbindung mit Ihrer Azure SQL-Datenbank her, um die SSIS-Katalogdatenbank (SSISDB) vorzubereiten. Außerdem konfiguriert er Berechtigungen und Einstellungen für das virtuelle Netzwerk (sofern angegeben) und verknüpft die neue Instanz der Azure-SSIS Integration Runtime mit dem virtuellen Netzwerk.

7. Wechseln Sie im Fenster **Verbindungen** ggf. zu **Integration Runtimes**. Klicken Sie zum Aktualisieren des Status auf **Aktualisieren**.

   ![Status der Erstellung](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Verwenden Sie die Links in der Spalte **Aktionen**, um die Integration Runtime zu überwachen, zu beenden und zu starten, zu bearbeiten oder zu löschen. Über den letzten Link können Sie den JSON-Code für die Integration Runtime anzeigen. Die Schaltflächen zum Bearbeiten und Löschen sind nur verfügbar, wenn die IR beendet wird.

   ![Azure SSIS IR – Aktionen](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS Integration Runtimes im Portal

1. Wechseln Sie auf der Azure Data Factory-Benutzeroberfläche zur Registerkarte **Bearbeiten**, klicken Sie auf **Verbindungen**, und wechseln Sie dann zur Registerkarte **Integration Runtimes**, um vorhandene Integration Runtimes in Ihrer Data Factory anzuzeigen.

   ![Anzeigen vorhandener IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Klicken Sie auf **Neu**, um eine neue Azure SSIS IR zu erstellen.

   ![Integration Runtime über das Menü](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Klicken Sie zum Erstellen einer Azure SSIS Integration Runtime auf **Neu** wie in der Abbildung gezeigt.
4. Klicken Sie im Fenster für die Integration Runtime-Einrichtung auf **Lift-and-shift existing SSIS packages to execute in Azure** (Migrieren vorhandener SSIS-Pakete per Lift & Shift für die Ausführung in Azure), und klicken Sie auf **Weiter**.

   ![Angeben des Integration Runtime-Typs](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Die übrigen Schritte zum Einrichten einer Azure SSIS IR finden Sie im Abschnitt [Bereitstellen einer Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime).

## <a name="azure-powershell"></a>Azure PowerShell

In diesem Abschnitt verwenden Sie Azure PowerShell zum Erstellen einer Azure-SSIS IR.

### <a name="create-variables"></a>Erstellen von Variablen

Definieren Sie Variablen zur Verwendung im Skript dieses Tutorials:

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Anmelden und Auswählen des Abonnements

Fügen Sie dem Skript zum Anmelden bei Ihrem Azure-Abonnement und Auswählen des Abonnements den folgenden Code hinzu:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>Überprüfen der Verbindung mit der Datenbank

Fügen Sie das folgende Skript hinzu, um den Azure SQL-Datenbank-Serverendpunkt zu überprüfen.

```powershell
# Validate only when you do not use VNet nor AAD authentication
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
```

### <a name="configure-virtual-network"></a>Konfigurieren eines virtuellen Netzwerks

Fügen Sie das folgende Skript hinzu, um Berechtigungen/Einstellungen für das virtuelle Netzwerk für die Azure SSIS Integration Runtime automatisch zu konfigurieren, die Sie einbinden möchten.

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

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden.

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

Wenn Sie weder Azure SQL-Datenbank mit VNET-Dienstendpunkten/mit verwalteter Instanz zum Hosten von SSISDB verwenden noch Zugriff auf lokale Daten benötigen, können Sie die Parameter „VNetId“ und „Subnetz“ weglassen oder dafür leere Werte übergeben. Andernfalls können Sie sie nicht weglassen und müssen gültige Werte aus der Konfiguration des virtuellen Netzwerks übergeben, wie unter [Verknüpfen einer Azure-SSIS Integration Runtime mit einem virtuellen Netzwerk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) beschrieben.

Wenn Sie eine verwaltete Instanz zum Hosten von SSISDB verwenden, können Sie den Parameter CatalogPricingTier weglassen oder dafür einen leeren Wert übergeben. Andernfalls können Sie ihn nicht weggelassen und müssen einen gültigen Wert aus der Liste der für Azure SQL-Datenbank unterstützten Tarife übergeben, wie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](../sql-database/sql-database-resource-limits.md) beschrieben.

Wenn Sie die Azure Active Directory-Authentifizierung (AAD) mit der verwalteten Identität für Ihre Azure Data Factory verwenden, um eine Verbindung mit dem Datenbankserver herzustellen, können Sie den Parameter „CatalogAdminCredential“ auslassen, aber Sie müssen die verwaltete Identität für Ihre ADF einer AAD-Gruppe mit Zugriffsberechtigungen für den Datenbankserver hinzufügen, wie unter [Aktivieren der Azure Active Directory-Authentifizierung für die Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir) beschrieben. Andernfalls können Sie ihn nicht auslassen, sondern müssen ein gültiges Objekt, gebildet aus Ihrem Administratorbenutzername und dem zugehörigen Kennwort für den Server, für die SQL-Authentifizierung übergeben.

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
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>Starten der Integration Runtime

Führen Sie den folgenden Befehl aus, um die Azure SSIS Integration Runtime zu starten:

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

Die Ausführung dieses Befehls dauert ca. **20 bis 30 Minuten**.

### <a name="full-script"></a>Vollständiges Skript

Hier ist das vollständige Skript, das eine Azure-SSIS-Integration Runtime erstellt.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"

### Log in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
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

### Configure virtual network
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
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

# Add CatalogAdminCredential parameter when you do not use AAD authentication
if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

In diesem Abschnitt verwenden Sie eine Azure Resource Manager-Vorlage, um eine Azure-SSIS-Integration Runtime zu erstellen. Die folgende exemplarische Vorgehensweise kann als Beispiel dienen:

1. Erstellen Sie eine JSON-Datei mit der folgenden Azure Resource Manager-Vorlage. Ersetzen Sie die Werte in den eckigen Klammern (Platzhalter) durch Ihre eigenen Werte.

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

2. Führen Sie zum Bereitstellen der Azure Resource Manager-Vorlage den Befehl New-AzResourceGroupDeployment wie im folgenden Beispiel gezeigt aus, wobei ADFTutorialResourceGroup der Name der Ressourcengruppe und „ADFTutorialARM.json“ die Datei ist, die die JSON-Definition für Ihre Data Factory und Azure-SSIS-IR enthält.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Dieser Befehl erstellt die Data Factory und darin eine Azure-SSIS-IR. Er startet die IR aber nicht.

3. Führen Sie zum Starten der Azure-SSIS-IR den Befehl Start-AzDataFactoryV2IntegrationRuntime aus:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

## <a name="deploy-ssis-packages"></a>Bereitstellen von SSIS-Paketen

Verwenden Sie jetzt SQL Server Data Tools (SSDT) oder SQL Server Management Studio (SSMS), um Ihre SSIS-Pakete in Azure bereitzustellen. Stellen Sie eine Verbindung mit Ihrem Datenbankserver her, der den SSIS-Katalog (SSISDB) hostet. Der Name des Datenbankservers hat folgendes Format: „&lt;Name des Azure SQL-Datenbank-Servers&gt;.database.windows.net“ oder „&lt;Name der verwalteten Instanz.DNS-Präfix&gt;.database.windows.net“. Anweisungen finden Sie im Artikel [Bereitstellen von Paketen](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server).

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich auch die anderen Themen zur Azure-SSIS-IR in dieser Dokumentation an:

- [Azure-SSIS-Integrationslaufzeit](concepts-integration-runtime.md#azure-ssis-integration-runtime): Dieser Artikel enthält konzeptionelle Informationen zu Integrationslaufzeiten, die im Allgemeinen die Azure-SSIS-Integrationslaufzeit einschließen.
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-create-azure-ssis-runtime-portal.md): Dieser Artikel enthält schrittweise Anweisungen zum Erstellen einer Azure-SSIS-Integrationslaufzeit und verwendet eine Azure SQL-Datenbank zum Hosten des SSIS-Katalogs.
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel wird das Abrufen von Informationen zu einer Azure-SSIS-Integrationslaufzeit veranschaulicht, und er enthält Beschreibungen der Status in den zurückgegebenen Informationen.
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md): In diesem Artikel wird beschrieben, wie Sie eine Azure-SSIS-Integrationslaufzeit beenden, starten oder entfernen. Es wird zudem gezeigt, wie Sie Ihre Azure-SSIS-Integrationslaufzeit horizontal hochskalieren, indem Sie der Integrationslaufzeit weitere Knoten hinzufügen.
- [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk:](join-azure-ssis-integration-runtime-virtual-network.md). Dieser Artikel enthält grundlegende Informationen zum Verknüpfen Ihrer Azure-SSIS-IR mit einem virtuellen Azure-Netzwerk. Darüber hinaus enthält er Schritte zur Verwendung des Azure-Portals zum Konfigurieren des virtuellen Netzwerks, damit die Azure-SSIS-IR dem virtuellen Netzwerk beitreten kann.
