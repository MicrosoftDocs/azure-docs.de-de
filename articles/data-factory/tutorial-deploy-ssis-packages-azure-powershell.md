---
title: Bereitstellen der Azure-SSIS-Integration Runtime mit PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie zur Bereitstellung und Ausführung von SSIS-Paketen in Azure die Azure-SSIS-Integration Runtime mit PowerShell in Azure Data Factory bereitstellen.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 10/28/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: cba8af369beac935834da8d2073510e0f997648b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66123456"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Bereitstellen der Azure-SSIS Integration Runtime in Azure Data Factory mit PowerShell
In diesem Tutorial werden die Schritte für die Bereitstellung einer Azure-SSIS Integration Runtime (Integration Runtime, IR) in Azure Data Factory beschrieben. Anschließend können Sie SQL Server Data Tools (SSDT) oder SQL Server Management Studio (SSMS) zum Bereitstellen und Ausführen von SQL Server Integration Services-Paketen (SSIS) in dieser Runtime in Azure verwenden. In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!NOTE]
> In diesem Artikel wird Azure PowerShell zum Bereitstellen einer Azure SSIS IR-Instanz verwendet. Informationen zum Bereitstellen einer Azure-SSIS IR über die Data Factory-Benutzeroberfläche finden Sie unter [ Bereitstellen der Azure-SSIS Integration Runtime in Azure Data Factory](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen einer Azure SSIS Integration Runtime
> * Starten der Azure SSIS Integration Runtime
> * Bereitstellen von SSIS-Paketen
> * Überprüfen des kompletten Skripts

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-Abonnement**. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Konzeptionelle Informationen zur Azure-SSIS-IR finden Sie unter [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). 
- **Azure SQL-Datenbank-Server**. Wenn Sie noch nicht über einen Datenbankserver verfügen, erstellen Sie einen im Azure-Portal, bevor Sie beginnen. Dieser Server hostet die SSIS-Katalog-Datenbank (SSISDB). Es empfiehlt sich, den Datenbankserver in derselben Azure-Region zu erstellen, in der sich auch die Integration Runtime befindet. Diese Konfiguration ermöglicht es der Integration Runtime, Ausführungsprotokolle in SSISDB zu schreiben, ohne Azure-Regionen zu überschreiten. 
    - Basierend auf dem ausgewählten Datenbankserver kann SSISDB in Ihrem Auftrag als einzelne Datenbank, als Teil eines Pools für elastische Datenbanken oder in einer verwalteten Instanz erstellt werden und in einem öffentlichen Netzwerk oder durch das Hinzufügen zu einem virtuellen Netzwerk zugänglich sein. Einen Leitfaden zum Auswählen des Datenbankservertyps zum Hosten von SSISDB finden Sie unter [Vergleich zwischen Azure SQL-Datenbank-Einzeldatenbanken/Pools für elastische Datenbanken und einer verwalteten Instanz](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Wenn Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer verwalteten Instanz zum Hosten von SSISDB verwenden oder Zugriff auf lokale Daten benötigen, müssen Sie Azure-SSIS IR einem virtuellen Netzwerk hinzufügen. Weitere Informationen finden Sie unter [Erstellen von Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Vergewissern Sie sich, dass die Einstellung **Zugriff auf Azure-Dienste zulassen** für den Datenbankserver **aktiviert** ist. Diese Einstellung gilt nicht, wenn Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder mit einer verwalteten Instanz zum Hosten von SSISDB verwenden. Weitere Informationen finden Sie unter [Schützen der Azure SQL-Datenbank](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Informationen zum Aktivieren dieser Einstellung mithilfe von PowerShell finden Sie unter [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule). 
    - Fügen Sie der Client-IP-Adressenliste in den Firewalleinstellungen für den Datenbankserver die IP-Adresse des Clientcomputers oder einen IP-Adressbereich hinzu, der die IP-Adresse des Clientcomputers enthält. Weitere Informationen finden Sie unter [Firewallregeln auf Serverebene und Datenbankebene für Azure SQL-Datenbank](../sql-database/sql-database-firewall-configure.md). 
    - Sie können eine Verbindung mit dem Datenbankserver herstellen, indem Sie die SQL-Authentifizierung mit Ihren Serveradministrator-Anmeldeinformationen oder die Azure Active Directory-Authentifizierung (AAD) mit der verwalteten Identität für Ihre Azure Data Factory-Instanz verwenden.  Für Letztere müssen Sie die verwaltete Identität für Ihre Azure Data Factory-Instanz einer AAD-Gruppe mit Zugriffsberechtigungen für den Datenbankserver hinzufügen. Weitere Informationen finden Sie unter [Erstellen einer Azure-SSIS Integration Runtime in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
    - Vergewissern Sie sich, dass Ihr Azure SQL-Datenbank-Server keinen SSIS-Katalog (SSISDB) aufweist. Für die Bereitstellung der Azure-SSIS IR wird die Verwendung eines vorhandenen SSIS-Katalogs nicht unterstützt. 
- **Azure PowerShell**. Führen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets) aus. Sie verwenden PowerShell zum Ausführen eines Skripts, um eine Azure-SSIS Integration Runtime bereitzustellen, die SSIS-Pakete in der Cloud ausführt. 

> [!NOTE]
> - Eine Liste mit den Azure-Regionen, in denen Data Factory und Azure-SSIS Integration Runtime zurzeit verfügbar sind, finden Sie unter [ADF- und SSIS IR-Verfügbarkeit nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="launch-windows-powershell-ise"></a>Starten von Windows PowerShell ISE
Starten Sie **Windows PowerShell ISE** mit Administratorrechten. 

## <a name="create-variables"></a>Erstellen von Variablen
Kopieren Sie das folgende Skript, und fügen Sie es ein. Geben Sie Werte für die Variablen an. Eine Liste mit den unterstützten **Tarifen** für Azure SQL-Datenbank finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](../sql-database/sql-database-resource-limits.md).

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported, but for other nodes, 1-8 are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"
```

## <a name="validate-the-connection-to-database"></a>Überprüfen der Verbindung mit der Datenbank
Fügen Sie das folgende Skript hinzu, um das Netzwerk des Azure SQL-Datenbank-Servers (`<servername>.database.windows.net`) zu überprüfen. 

```powershell
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
```

Das folgende Beispiel veranschaulicht die Erstellung einer Azure SQL-Datenbank-Instanz im Rahmen des Skripts: 

Legen Sie Werte für die noch nicht definierten Variablen fest. Beispiel:  SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="log-in-and-select-subscription"></a>Anmelden und Auswählen des Abonnements
Fügen Sie dem Skript zum Anmelden bei Ihrem Azure-Abonnement und Auswählen des Abonnements den folgenden Code hinzu: 

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und als Gruppe verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `myResourceGroup` am Standort `westeurope` erstellt.

Wenn die Ressourcengruppe bereits vorhanden ist, müssen Sie diesen Code nicht in das Skript kopieren. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Führen Sie den folgenden Befehl aus, um eine Data Factory zu erstellen:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Erstellen einer Integration Runtime
Führen Sie den folgenden Befehl aus, um eine Azure SSIS Integration Runtime zu erstellen, die SSIS-Pakete in Azure ausführt: 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
  
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
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Starten der Integration Runtime
Führen Sie den folgenden Befehl aus, um die Azure SSIS Integration Runtime zu starten: 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
Die Ausführung dieses Befehls dauert ca. **20 bis 30 Minuten**. 

## <a name="deploy-ssis-packages"></a>Bereitstellen von SSIS-Paketen
Verwenden Sie jetzt SQL Server Data Tools (SSDT) oder SQL Server Management Studio (SSMS), um Ihre SSIS-Pakete in Azure bereitzustellen. Stellen Sie eine Verbindung mit der Azure SQL Server-Instanz her, die den SSIS-Katalog (SSISDB) hostet. Der Name des Azure SQL-Datenbank-Servers hat das folgende Format: `<servername>.database.windows.net`. 

Informationen finden Sie in den folgenden Artikeln der SSIS-Dokumentation: 

- [Deploy, run, and monitor an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) (Bereitstellen, Ausführen und Überwachen eines SSIS-Pakets in Azure)   
- [Connect to the SSISDB Catalog database on Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) (Herstellen einer Verbindung mit der SSISDB-Katalogdatenbank in Azure)
- [Schedule the execution of an SSIS package on Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) (Planen der Ausführung eines SSIS-Pakets in Azure)
- [Connect to on-premises data sources with Windows Authentication](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) (Herstellen einer Verbindung mit lokalen Datenquellen mit Windows-Authentifizierung) 

## <a name="full-script"></a>Vollständiges Skript
Das PowerShell-Skript in diesem Abschnitt konfiguriert eine Instanz der Azure SSIS Integration Runtime in der Cloud, die SSIS-Pakete ausführt. Nachdem Sie dieses Skript erfolgreich ausgeführt haben, können Sie SSIS-Pakete in der Microsoft Azure-Cloud bereitstellen und ausführen, wobei SSISDB in Azure SQL-Datenbank gehostet wird.

1. Starten Sie die Windows PowerShell Integrated Scripting Environment (ISE).
2. Führen Sie in der ISE den folgenden Befehl an der Eingabeaufforderung aus.    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. Kopieren Sie das PowerShell-Skript in diesem Abschnitt, und fügen Sie es in die ISE ein.
4. Geben Sie am Anfang des Skripts geeignete Werte für alle Parameter an.
5. Führen Sie das Skript aus. Die Ausführung des Befehls `Start-AzDataFactoryV2IntegrationRuntime` am Ende des Skripts dauert **20 bis 30 Minuten**.

> [!NOTE]
> - Das Skript stellt die Verbindung mit Ihrem Azure SQL-Datenbank-Server her, um die SSIS-Katalogdatenbank (SSISDB) vorzubereiten.
> 
> - Wenn Sie eine Azure SSIS IR-Instanz bereitstellen, werden auch das Azure Feature Pack für SSIS und die Access Redistributable-Komponente installiert. Diese Komponenten ermöglichen nicht nur die Konnektivität mit Excel- und Access-Dateien und verschiedenen Azure-Datenquellen, sondern auch mit den Datenquellen, die von den integrierten Komponenten unterstützt werden. Sie können auch weitere Komponenten installieren. Weitere Informationen finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

Eine Liste mit den unterstützten **Tarifen** für Azure SQL-Datenbank finden Sie unter [Ressourceneinschränkungen für Azure SQL-Datenbank](../sql-database/sql-database-resource-limits.md). 

Eine Liste mit den Azure-Regionen, in denen Data Factory und Azure-SSIS Integration Runtime zurzeit verfügbar sind, finden Sie unter [ADF- und SSIS IR-Verfügbarkeit nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

```powershell
# Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[Azure subscription name]"
$ResourceGroupName = "[Azure resource group name]"
# Data factory name. Must be globally unique
$DataFactoryName = "[Data factory name]"
$DataFactoryLocation = "EastUS"

# Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[Specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[Specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported, but for other nodes, 1-8 are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>)]"

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

Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
    
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
    
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
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogAdminCredential $serverCreds `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-virtual-network"></a>Verknüpfen der Azure-SSIS-IR mit einem virtuellen Netzwerk
Wenn Sie Azure SQL-Datenbank mit VNET-Dienstendpunkten oder einer verwalteten Instanz verwenden, die zum Hosten von SSISDB einem virtuellen Netzwerk hinzugefügt wird, müssen Sie diesem virtuellen Netzwerk auch Azure-SSIS Integration Runtime hinzufügen. Mit Azure Data Factory können Sie Ihre Azure-SSIS-Integration Runtime zu einem virtuellen Netzwerk hinzufügen. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).

Ein vollständiges Skript zum Erstellen einer Azure-SSIS-Integration Runtime, die mit einem virtuellen Netzwerk verknüpft wird, finden Sie unter [Erstellen einer Azure-SSIS-Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).

## <a name="monitor-and-manage-azure-ssis-ir"></a>Überwachen und Verwalten einer Azure-SSIS-IR
In den folgenden Artikeln finden Sie ausführliche Informationen zum Überwachen und Verwalten einer Azure-SSIS-IR. 

- [Überwachen einer Integrationslaufzeit in Azure Data Factory](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory
> * Erstellen einer Azure SSIS Integration Runtime
> * Starten der Azure SSIS Integration Runtime
> * Bereitstellen von SSIS-Paketen
> * Überprüfen des kompletten Skripts

Informationen zum Anpassen Ihrer Azure-SSIS Integration Runtime finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
>[Anpassen des Setups für Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)
