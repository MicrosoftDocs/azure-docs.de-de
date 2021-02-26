---
title: Bereitstellen der Enterprise-Edition für die Azure-SSIS Integration Runtime
description: In diesem Artikel werden die Features der Enterprise-Edition für die Azure-SSIS-Integration Runtime beschrieben, und wie diese bereitgestellt wird.
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/09/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: f700729c2d059648b1c3a7e451526aefcb436818
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387545"
---
# <a name="provision-enterprise-edition-for-the-azure-ssis-integration-runtime"></a>Bereitstellen der Enterprise-Edition für die Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Mit der Enterprise Edition der Azure-SSIS-Integrationslaufzeit können Sie die folgenden erweiterten und Premium-Features verwenden:
-   CDC-Komponenten (Change Data Capture)
-   Oracle-, Teradata- und SAP BW-Connectors
-   SQL Server Analysis Services (SSAS) und Azure Analysis Services (AAS) – Connectors und Transformationen
-   Transformationen für Fuzzygruppierung und Fuzzysuche
-   Transformationen für Ausdrucksextrahierung und Ausdruckssuche

Für einige dieser Features müssen Sie zusätzliche Komponenten installieren, um die Azure-SSIS-Integrationslaufzeit anzupassen. Weitere Informationen zur Installation der zusätzlichen Komponenten finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="enterprise-features"></a>Enterprise-Features

| **Enterprise-Features** | **Beschreibungen** |
|---|---|
| CDC-Komponenten | Die CDC-Quelle, Kontrollaufgabe und Splitter-Transformation sind für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. Zum Herstellen einer Verbindung mit Oracle müssen Sie außerdem den CDC-Designer und -Dienst auf einem anderen Computer installieren. |
| Oracle-Connectors | Der Oracle-Verbindungs-Manager, die Quelle und das Ziel sind für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. Sie müssen auch den OCI-Treiber (Oracle Call Interface) installieren und, falls erforderlich, die Oracle Transport Network Substrate-Komponente (TNS) für die Azure-SSIS-Integrationslaufzeit konfigurieren. Weitere Informationen finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Teradata-Connectors | Sie müssen den Teradata-Verbindungs-Manager, die Quelle und das Ziel sowie die TPT-API (Teradata Parallel Transporter) und den Teradata-ODBC-Treiber für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit installieren. Weitere Informationen finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| SAP BW-Connectors | Der SAP BW-Verbindungs-Manager, die Quelle und das Ziel sind für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. Außerdem müssen Sie den SAP BW-Treiber für die Azure-SSIS-Integrationslaufzeit installieren. Diese Connectors unterstützen SAP BW 7.0 oder früher. Zum Herstellen einer Verbindung mit höheren Versionen von SAP BW oder anderen SAP-Produkten können Sie SAP-Connectors von Drittanbietern für die Azure-SSIS-Integrationslaufzeit erwerben und installieren. Weitere Informationen zur Installation der zusätzlichen Komponenten finden Sie unter [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md). |
| Analysis Services-Komponenten               | Das Data Mining-Modelltraining-Ziel, das Ziel für die Dimensionsverarbeitung und das Ziel für die Partitionsverarbeitung sowie die Data Mining-Abfragetransformation sind für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. Alle diese Komponenten unterstützen SQL Server Analysis Services (SSAS), aber nur das Ziel für die Partitionsverarbeitung unterstützt Azure Analysis Services (AAS). Zum Herstellen einer Verbindung mit SSAS müssen Sie außerdem [Anmeldeinformationen für die Windows-Authentifizierung in SSISDB konfigurieren](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth). Zusätzlich zu diesen Komponenten sind auch der Analysis Services-Task „DDL ausführen“, der Task für die Analysis Services-Verarbeitung und der Data Mining-Abfragetask für die Standard bzw. Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. |
| Transformationen für Fuzzygruppierung und Fuzzysuche  | Die Transformationen für die Fuzzygruppierung und Fuzzysuche sind für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. Diese Komponenten unterstützen für die Speicherung von Verweisdaten sowohl SQL Server als auch Azure SQL-Datenbank. |
| Transformationen für Ausdrucksextrahierung und Ausdruckssuche | Die Transformationen für die Ausdrucksextrahierung und Ausdruckssuche sind für die Enterprise Edition der Azure-SSIS-Integrationslaufzeit vorinstalliert. Diese Komponenten unterstützen für die Speicherung von Verweisdaten sowohl SQL Server als auch Azure SQL-Datenbank. |

## <a name="instructions"></a>Instructions

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1.  Laden Sie [Azure PowerShell](/powershell/azure/install-az-ps)herunter, und installieren Sie sie.

2.  Wenn Sie die Azure-SSIS-Integrationslaufzeit mit PowerShell bereitstellen oder neu konfigurieren, sollten Sie `Set-AzDataFactoryV2IntegrationRuntime` mit **Enterprise** als Wert für den Parameter **Edition** ausführen, bevor Sie die Azure-SSIS-Integrationslaufzeit starten. Hier ist ein Beispielskript angegeben:

    ```powershell
    $MyAzureSsisIrEdition = "Enterprise"

    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                               -Name $MyAzureSsisIrName
                                               -ResourceGroupName $MyResourceGroupName
                                               -Edition $MyAzureSsisIrEdition

    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName
                                                 -Name $MyAzureSsisIrName
                                                 -ResourceGroupName $MyResourceGroupName
    ```

## <a name="next-steps"></a>Nächste Schritte

-   [Benutzerdefiniertes Setup von Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Entwicklung von kostenpflichtigen oder lizenzierten benutzerdefinierten Komponenten für Azure-SSIS Integration Runtime](how-to-develop-azure-ssis-ir-licensed-components.md)