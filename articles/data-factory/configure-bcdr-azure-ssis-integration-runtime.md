---
title: Konfigurieren von Azure-SSIS Integration Runtime für Failover der SQL-Datenbank | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie Azure-SSIS Integration Runtime mit Georeplikation für die Azure SQL-Datenbank und Failover für die SSISDB-Datenbank konfiguriert wird.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f0612a688bb1e0fd79325b9a1f9b43731a210d10
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399243"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurieren von Azure-SSIS Integration Runtime mit Georeplikation der Azure SQL-Datenbank und Failover

In diesem Artikel wird beschrieben, wie Azure-SSIS Integration Runtime mit Georeplikation für die Azure SQL-Datenbank und die SSISDB-Datenbank konfiguriert wird. Wenn ein Failover auftritt, können Sie sicherstellen, dass Azure-SSIS IR mit der sekundären Datenbank weiterhin funktioniert.

Weitere Informationen zu Georeplikation und Failover für die SQL-Datenbank finden Sie unter [Übersicht: Aktive Georeplikation und Gruppen für automatisches Failover](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Szenario 1: Azure-SSIS IR verweist auf einen Listenerendpunkt mit Lese-/Schreibberechtigung

### <a name="conditions"></a>Bedingungen

Dieser Abschnitt gilt, wenn die folgenden Bedingungen erfüllt sind:

- Azure-SSIS IR verweist auf den Listenerendpunkt mit Lese-/Schreibberechtigung der Failovergruppe.

  AND

- Der SQL-Datenbank-Server ist *nicht* mit der Regel des VNET-Dienstendpunkts konfiguriert.

### <a name="solution"></a>Lösung

Wenn ein Failover auftritt, ist dies transparent für Azure-SSIS IR. Azure-SSIS IR stellt automatisch eine Verbindung mit dem neuen primären Endpunkt der Failovergruppe her.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Szenario 2: Azure-SSIS IR verweist auf den primären Serverendpunkt

### <a name="conditions"></a>Bedingungen

Dieser Abschnitt gilt, wenn eine der folgenden Bedingungen erfüllt ist:

- Azure-SSIS IR verweist auf den primären Serverendpunkt der Failovergruppe. Dieser Endpunkt ändert sich beim Auftreten eines Failovers.

  OR

- Der Azure SQL-Datenbank-Server ist mit einer Regel des VNET-Dienstendpunkts konfiguriert.

  OR

- Der Datenbankserver ist eine verwaltete SQL-Datenbankinstanz, die mit einem virtuellen Netzwerk konfiguriert ist.

### <a name="solution"></a>Lösung

Bei einem Failover müssen Sie die folgenden Schritte ausführen:

1. Beenden Sie die Azure SSIS IR.

2. Konfigurieren Sie die IR so, dass sie auf den neuen primären Endpunkt und auf ein virtuelles Netzwerk in der neuen Region verweist.

3. Starten Sie die IR neu.

In den folgenden Abschnitten werden diese Schritte ausführlicher beschrieben.

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass die Notfallwiederherstellung für den Azure SQL-Datenbank-Server aktiviert ist, falls beim Server zum gleichen Zeitpunkt ein Ausfall auftritt. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität mit Azure SQL-Datenbank](../sql-database/sql-database-business-continuity.md).

- Wenn Sie ein virtuelles Netzwerk in der aktuellen Region verwenden, müssen Sie ein anderes virtuelles Netzwerk in der neuen Region verwenden, um eine Verbindung mit der Azure-SSIS-Integration Runtime herzustellen. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).

- Bei Verwendung eines benutzerdefinierten Setups müssen Sie unter Umständen einen weiteren SAS-URI für den Blobcontainer vorbereiten, in dem das benutzerdefinierte Setupskript und die zugehörigen Dateien gespeichert sind, damit er auch während eines Ausfalls zugänglich ist. Weitere Informationen finden Sie unter [Anpassen des Setups für Azure-SSIS-Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Schritte

Führen Sie die folgenden Schritte aus, um die Azure-SSIS-IR anzuhalten, auf eine neue Region umzustellen und wieder zu starten.

1. Beenden Sie die IR in der ursprünglichen Region.

2. Führen Sie den folgenden Befehl in PowerShell aus, um die IR mit den neuen Einstellungen zu aktualisieren.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen der Azure-SSIS-Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).

3. Starten Sie IR erneut.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Szenario 3: Anfügen einer vorhandenen SSISDB (SSIS-Katalog) an eine neue Azure-SSIS IR

Wenn ein ADF- oder Azure-SSIS IR-Notfall im aktuellen Bereich auftritt, können Sie den Betrieb Ihrer SSISDB mit einer neuen Azure-SSIS IR in einer neuen Region aufrechterhalten.

### <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie ein virtuelles Netzwerk in der aktuellen Region verwenden, müssen Sie ein anderes virtuelles Netzwerk in der neuen Region verwenden, um eine Verbindung mit der Azure-SSIS-Integration Runtime herzustellen. Weitere Informationen finden Sie unter [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk](join-azure-ssis-integration-runtime-virtual-network.md).

- Bei Verwendung eines benutzerdefinierten Setups müssen Sie unter Umständen einen weiteren SAS-URI für den Blobcontainer vorbereiten, in dem das benutzerdefinierte Setupskript und die zugehörigen Dateien gespeichert sind, damit er auch während eines Ausfalls zugänglich ist. Weitere Informationen finden Sie unter [Anpassen des Setups für Azure-SSIS-Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Schritte

Führen Sie die folgenden Schritte aus, um die Azure-SSIS-IR anzuhalten, auf eine neue Region umzustellen und wieder zu starten.

1. Führen Sie die gespeicherte Prozedur aus, um die SSISDB an **\<new_data_factory_name\>** oder **\<new_integration_runtime_name\>** anzufügen.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Erstellen Sie eine neue Data Factory mit dem Namen **\<new_data_factory_name\>** in der neuen Region. Weitere Informationen finden Sie unter „Erstellen einer Data Factory“.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen einer Azure Data Factory mithilfe von PowerShell](quickstart-create-data-factory-powershell.md).

3. Erstellen Sie eine neue Azure-SSIS IR mit dem Namen **\<new_integration_runtime_name\>** in der neuen Region mit Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Weitere Informationen zu diesem PowerShell-Befehl finden Sie unter [Erstellen der Azure-SSIS-Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md).

4. Starten Sie IR erneut.

## <a name="next-steps"></a>Nächste Schritte

Ziehen Sie diese weiteren Konfigurationsoptionen für die Azure-SSIS-IR in Betracht:

- [Konfigurieren der Azure-SSIS-Integration Runtime für hohe Leistung](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassen des Setups für Azure-SSIS-Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Bereitstellen der Enterprise-Edition für die Azure-SSIS-Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
