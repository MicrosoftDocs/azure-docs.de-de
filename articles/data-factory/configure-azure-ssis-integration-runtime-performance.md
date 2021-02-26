---
title: Konfigurieren der Leistung für Azure-SSIS Integration Runtime
description: Hier erfahren Sie, wie Sie die Eigenschaften von Azure-SSIS Integration Runtime für hohe Leistung konfigurieren.
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 5d275100124660b901504b7e7f71cf93518fd077
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364391"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurieren von Azure-SSIS Integration Runtime für hohe Leistung

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


In diesem Artikel erfahren Sie, wie Sie eine Instanz von Azure-SSIS Integration Runtime (IR) für hohe Leistung konfigurieren. Mit Azure-SSIS IR können Sie SSIS-Pakete (SQL Server Integration Services) in Azure bereitstellen und ausführen. Weitere Informationen zu Azure-SSIS IR finden Sie im Artikel zu [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Informationen zum Bereitstellen und Ausführen von SSIS-Paketen in Azure finden Sie unter [Lift and shift SQL Server Integration Services workloads to the cloud](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) (Migrieren von SQL Server Integration Services-Workloads in die Cloud per Lift & Shift).

> [!IMPORTANT]
> Dieser Artikel enthält Leistungsergebnisse und Beobachtungen aus internen Tests, die von Mitgliedern des SSIS-Entwicklerteams durchgeführt wurden. Ihre Ergebnisse können davon abweichen. Führen Sie daher eigene Tests durch, bevor Sie Ihre Konfigurationseinstellungen endgültig festlegen. Diese Einstellungen können sich sowohl auf die Kosten als auch auf die Leistung auswirken.

## <a name="properties-to-configure"></a>Zu konfigurierende Eigenschaften

Der folgende Auszug aus einem Konfigurationsskript enthält die Eigenschaften, die Sie beim Erstellen einer Instanz von Azure-SSIS Integration Runtime konfigurieren können. Das vollständige PowerShell-Skript und die dazugehörige Beschreibung finden Sie unter [Bereitstellen von SQL Server Integration Services-Paketen in Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
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
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** ist der Ort, an dem sich der Integration Runtime-Workerknoten befindet. Der Workerknoten gewährleistet eine konstante Verbindung mit der SSIS-Katalogdatenbank (SSIS Catalog Database, SSISDB) in Azure SQL-Datenbank. Legen Sie **AzureSSISLocation** auf denselben Ort fest, an dem sich auch der [logische SQL-Server](../azure-sql/database/logical-servers.md) zum Hosten der SSISDB befindet, um die bestmögliche Integration Runtime-Effizienz zu erzielen.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, das auch die Azure SSIS-IR enthält, unterstützt folgende Optionen:
-   Standard\_A4\_v2
-   Standard\_A8\_v2
-   Standard\_D1\_v2
-   Standard\_D2\_v2
-   Standard\_D3\_v2
-   Standard\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Standard\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

Gemäß den inoffiziellen internen Tests des SSIS-Entwicklerteams scheint die D-Serie besser für die SSIS-Paketausführung geeignet zu sein als die A-Serie.

-   Das Leistung/Preis-Verhältnis der D-Serie ist höher als das der A-Serie und das Leistung/Preis-Verhältnis der v3-Serie höher als das der v2-Serie.
-   Der Durchsatz der D-Reihe ist höher als der der A-Serie zum selben Preis, und der Durchsatz der v3-Serie ist höher als der der v2-Serie zum selben Preis.
-   Knoten der v2-Serie der Azure-SSIS IR eignen sich nicht für ein benutzerdefiniertes Setup, daher sollten Sie stattdessen Knoten der v3-Serie verwenden. Wenn Sie bereits Knoten der v2-Serie verwenden, wechseln Sie so bald wie möglich zu Knoten der v3-Serie.
-   Die E-Serie bietet arbeitsspeicheroptimierte VM-Größen, die ein höheres Verhältnis von Speicher zu CPU als andere Computer bieten. Wenn Ihr Paket viel Arbeitsspeicher benötigt, können Sie VMs der E-Serie in Betracht ziehen.

### <a name="configure-for-execution-speed"></a>Konfigurieren für Ausführungsgeschwindigkeit
Wenn Sie nicht viele Pakete ausführen müssen und die Pakete schnell ausgeführt werden sollen, wählen Sie anhand der Informationen im folgenden Diagramm einen geeigneten VM-Typ für Ihr Szenario aus.

Diese Daten stellen eine einzelne Paketausführung auf einem einzelnen Workerknoten dar. Das Paket lädt 3 Millionen Datensätze mit Vor- und Nachnamespalten aus Azure Blob Storage herunter, erstellt eine Spalte für den vollständigen Namen und schreibt Datensätze, bei denen der vollständige Name mehr als 20 Zeichen umfasst, in Azure Blob Storage.

Die Y-Achse zeigt die Anzahl von Paketen, deren Ausführung innerhalb einer Stunde abgeschlossen wurde. Beachten Sie, dass dies nur ein Testergebnis eines Pakets ist, das Arbeitsspeicher verbraucht. Wenn Sie den Durchsatz Ihres Pakets ermitteln möchten, sollten Sie den Test selbst ausführen.

![SSIS Integration Runtime: Paketausführungsgeschwindigkeit](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurieren für allgemeinen Durchsatz

Wenn Sie viele Pakete ausführen müssen und für Sie der allgemeine Durchsatz im Vordergrund steht, wählen Sie anhand der Informationen im folgenden Diagramm einen geeigneten VM-Typ für Ihr Szenario aus.

Die Y-Achse zeigt die Anzahl von Paketen, deren Ausführung innerhalb einer Stunde abgeschlossen wurde. Beachten Sie, dass dies nur ein Testergebnis eines Pakets ist, das Arbeitsspeicher verbraucht. Wenn Sie den Durchsatz Ihres Pakets ermitteln möchten, sollten Sie den Test selbst ausführen.

![SSIS Integration Runtime: Maximaler allgemeiner Durchsatz](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** passt die Integration Runtime-Skalierbarkeit an. Der Integration Runtime-Durchsatz ist proportional zu **AzureSSISNodeNumber**. Legen Sie **AzureSSISNodeNumber** zunächst auf einen niedrigen Wert fest, überwachen Sie den Integration Runtime-Durchsatz, und passen Sie den Wert anschließend für Ihr Szenario an. Informationen zum Ändern der Konfiguration der Workerknotenanzahl finden Sie unter [Verwalten einer Azure-SSIS-Integrationslaufzeit](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Wenn Sie bereits einen leistungsstarken Workerknoten für die Paketausführung verwenden, lässt sich der allgemeine Integration Runtime-Durchsatz ggf. durch Erhöhen von **AzureSSISMaxParallelExecutionsPerNode** steigern. Wenn Sie den maximalen Wert erhöhen möchten, müssen Sie Azure PowerShell verwenden, um **AzureSSISMaxParallelExecutionsPerNode** zu aktualisieren. Einen geeigneten ungefähren Wert können Sie auf der Grundlage der Kosten für Ihr Paket und der folgenden Konfigurationen für Workerknoten ermitteln. Weitere Informationen finden Sie unter [Universelle VM-Größen](../virtual-machines/sizes-general.md).

| Size             | vCPU | Memory: GiB | Temporärer Speicher (SSD): GiB | Maximaler Durchsatz (temporärer Speicher): IOPS/MBit/s Lesen/MBps Schreiben | Max. Datenträger/Durchsatz: IOPS | Maximale Anzahl NICs/Erwartete Netzwerkbandbreite (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standard\_D1\_v2 | 1    | 3,5         | 50                     | 3000/46/23                                             | 2/2 x 500                         | 2/750                                        |
| Standard\_D2\_v2 | 2    | 7           | 100                    | 6000/93/46                                             | 4/4 x 500                         | 2/1500                                       |
| Standard\_D3\_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 8/8 x 500                         | 4/3000                                       |
| Standard\_D4\_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 16/16 x 500                       | 8/6000                                       |
| Standard\_A4\_v2 | 4    | 8           | 40                     | 4000/80/40                                             | 8/8 x 500                         | 4/1000                                       |
| Standard\_A8\_v2 | 8    | 16          | 80                     | 8000/160/80                                            | 16/16 x 500                       | 8/2000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000/46/23                                             | 4/6 x 500                         | 2/1000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000/93/46                                             | 8/12 x 500                        | 2/2000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000/187/93                                           | 16/24 x 500                       | 4/4000                                       |
| Standard\_D16\_v3| 16   | 64          | 400                    | 24000/375/187                                          | 32/48 x 500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000/750/375                                          | 32/96 x 500                       | 8/16000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192 x 500                      | 8 / 30000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000/46/23                                             | 4/6 x 500                         | 2/1000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000/93/46                                             | 8/12 x 500                        | 2/2000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000/187/93                                           | 16/24 x 500                       | 4/4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000/375/187                                          | 32/48 x 500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000/750/375                                          | 32/96 x 500                       | 8/16000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192 x 500                      | 8 / 30000                                      |

Richtlinien für das Festlegen des passenden Werts für die Eigenschaft **AzureSSISMaxParallelExecutionsPerNode**: 

1. Verwenden Sie zunächst einen niedrigen Wert.
2. Erhöhen Sie ihn geringfügig, um zu überprüfen, ob sich der Durchsatz insgesamt verbessert.
3. Erhöhen Sie den Wert nicht weiter, wenn der allgemeine Durchsatz den Maximalwert erreicht.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** ist der Tarif für die SSIS-Katalogdatenbank (SSIS Catalog Database, SSISDB) in Azure SQL-Datenbank. Diese Einstellung beeinflusst die maximale Anzahl von Workern in der IR-Instanz, die Geschwindigkeit beim Hinzufügen einer Paketausführung zur Warteschlange und die Geschwindigkeit beim Laden des Ausführungsprotokolls.

-   Wenn die Geschwindigkeit beim Hinzufügen einer Paketausführung zur Warteschlange und beim Laden des Ausführungsprotokolls für Sie nicht relevant ist, können Sie den niedrigsten Datenbanktarif verwenden. Azure SQL-Datenbank mit Basic-Tarif unterstützt acht Worker in einer Integration Runtime-Instanz.

-   Wählen Sie bei Verwendung von mehr als acht Workern oder mehr als 50 Kernen eine leistungsfähigere Datenbank als Basic. Andernfalls wird die Datenbank zum Engpass der Integration Runtime-Instanz und beeinträchtigt die allgemeine Leistung.

-   Wählen Sie eine leistungsfähigere Datenbank wie z.B. s3 aus, wenn der ausführliche Protokolliergrad festgelegt ist. Entsprechend unseren inoffiziellen internen Tests kann der Tarif s3 eine SSIS-Paketausführung mit 2 Knoten, 128 parallel und ausführlichem Protokolliergrad unterstützen.

Der Datenbanktarif kann auch auf der Grundlage der im Azure-Portal verfügbaren Informationen zur Nutzung von [Datenbanktransaktionseinheiten](../azure-sql/database/service-tiers-dtu.md) (Database Transaction Units, DTUs) angepasst werden.

## <a name="design-for-high-performance"></a>Entwerfen für hohe Leistung
Das Entwerfen eines SSIS-Pakets für die Ausführung in Azure unterscheidet sich vom Entwerfen eines Pakets für die lokale Ausführung. Unabhängige Aufgaben werden hier nicht im gleichen Paket zusammengefasst, sondern auf mehrere Pakete aufgeteilt, um eine effizientere Ausführung in Azure-SSIS IR zu erreichen. Erstellen Sie für jedes Paket eine Paketausführung, damit nicht auf den Abschluss der jeweils anderen Paketausführungen gewartet werden muss. Dieser Ansatz macht sich die Skalierbarkeit von Azure-SSIS Integration Runtime zunutze und verbessert den allgemeinen Durchsatz.

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich ausführlicher über Azure-SSIS Integration Runtime: [Azure-SSIS-Integrationslaufzeit](concepts-integration-runtime.md#azure-ssis-integration-runtime)