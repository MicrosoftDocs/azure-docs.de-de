---
title: Neukonfigurieren der Azure-SSIS-Integration Runtime | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Azure SSIS Integration Runtime in Azure Data Factory neu konfigurieren, nachdem Sie sie bereits bereitgestellt haben.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66152355"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Neukonfigurieren der Azure-SSIS-Integration Runtime
In diesem Artikel erfahren Sie, wie Sie eine Instanz von Azure-SSIS Integration Runtime neu konfigurieren. Informationen zum Erstellen einer Azure-SSIS-Integration Runtime (IR) in Azure Data Factory finden Sie im Artikel [Erstellen einer Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche 
Sie können die Data Factory-Benutzeroberfläche verwenden, um eine Azure-SSIS IR zu beenden, zu bearbeiten bzw. neu zu konfigurieren oder zu löschen. 

1. Wechseln Sie auf der **Data Factory-Benutzeroberfläche** zur Registerkarte **Bearbeiten**. Klicken Sie zum Starten der Data Factory-Benutzeroberfläche auf der Startseite Ihrer Data Factory auf **Erstellen und überwachen**.
2. Klicken Sie im linken Bereich auf **Verbindungen**.
3. Wechseln Sie im rechten Bereich zu **Integration Runtimes**. 
4. Sie können die Schaltflächen in der Spalte „Aktionen“ verwenden, um die Integration Runtime zu **beenden**, zu **bearbeiten** oder zu **löschen**. Mit der Schaltfläche **Code** in der Spalte **Aktionen** können Sie die JSON-Definition anzeigen, die der Integration Runtime zugeordnet ist.  
    
    ![Aktionen für Azure-SSIS IR](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Neukonfigurieren einer Azure-SSIS IR
1. Beenden Sie die Integration Runtime, indem Sie in der Spalte **Aktionen** auf **Beenden** klicken. Klicken Sie zum Aktualisieren der Listenansicht in der Symbolleiste auf **Aktualisieren**. Nachdem die IR beendet wurde, sehen Sie, dass Sie die IR mit der ersten Aktion starten können. 

    ![Aktionen für Azure-SSIS IR – Nach dem Beenden](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Sie können die IR bearbeiten bzw. neu konfigurieren, indem Sie in der Spalte **Aktionen** auf die Schaltfläche **Bearbeiten** klicken. Ändern Sie im Fenster **Integration Runtime Setup** (Integration Runtime-Setup) die Einstellungen (z.B. Größe des Knotens, Anzahl von Knoten oder maximale parallele Ausführungen pro Knoten). 
3. Klicken Sie zum Neustarten der IR in der Spalte **Aktionen** auf die Schaltfläche **Start**.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nach dem Bereitstellen und Starten einer Instanz der Azure-SSIS-Integrationslaufzeit können Sie sie durch Ausführen einer Reihe von `Stop` - `Set` - `Start`-PowerShell-Cmdlets neu konfigurieren. Mit dem folgenden PowerShell-Skript wird beispielsweise die Anzahl von Knoten, die für die Instanz der Azure-SSIS-Integrationslaufzeit zugewiesen wurden, in „5“ geändert.

### <a name="reconfigure-an-azure-ssis-ir"></a>Neukonfigurieren einer Azure-SSIS-Integrationslaufzeit (IR)

1. Beenden Sie zuerst die Azure SSIS Integration Runtime mit dem Cmdlet [Stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime). Dieser Befehl gibt alle Knoten frei und beendet die Abrechnung.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. Konfigurieren Sie als Nächstes die Azure-SSIS-Integrationslaufzeit mit dem Cmdlet [Set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) neu. Der folgende Beispielbefehl skaliert eine Azure SSIS Integration Runtime horizontal auf fünf Knoten.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. Starten Sie dann die Azure SSIS Integration Runtime mit dem Cmdlet [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime). Durch diesen Befehl werden alle Knoten für die Ausführung von SSIS-Paketen zugeordnet.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Löschen einer Azure-SSIS-Integrationslaufzeit
1. Listen Sie zuerst alle vorhandenen Azure-SSIS-Integrationslaufzeit-Instanzen unter Ihrer Data Factory auf.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. Beenden Sie als Nächstes alle vorhandenen Azure-SSIS-Integrationslaufzeit-Instanzen in Ihrer Data Factory.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. Entfernen Sie anschließend nacheinander einzeln alle vorhandenen Azure-SSIS-Integrationslaufzeit-Instanzen in Ihrer Data Factory.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Entfernen Sie abschließend Ihre Data Factory.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Entfernen Sie die Ressourcengruppe, falls Sie eine neue Ressourcengruppe erstellt haben.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Azure-SSIS-Laufzeit finden Sie in den folgenden Themen: 

- [Azure-SSIS-Integrationslaufzeit](concepts-integration-runtime.md#azure-ssis-integration-runtime): Dieser Artikel enthält konzeptionelle Informationen zu Integrationslaufzeiten, die im Allgemeinen die Azure-SSIS-Integrationslaufzeit einschließen. 
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](tutorial-create-azure-ssis-runtime-portal.md): Dieser Artikel enthält schrittweise Anweisungen zum Erstellen einer Azure-SSIS-Integrationslaufzeit und verwendet eine Azure SQL-Datenbank zum Hosten des SSIS-Katalogs. 
- [Erstellen der Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md): In diesem Artikel wird das Tutorial vertieft, und er enthält Anweisungen zur Verwendung einer verwalteten Azure SQL-Datenbank-Instanz und zum Verknüpfen der IR mit einem virtuellen Netzwerk. 
- [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk:](join-azure-ssis-integration-runtime-virtual-network.md). Dieser Artikel enthält grundlegende Informationen zum Verknüpfen einer Azure-SSIS-IR mit einem virtuellen Azure-Netzwerk. Darüber hinaus enthält er Schritte zur Verwendung des Azure-Portals zum Konfigurieren des virtuellen Netzwerks, damit die Azure-SSIS-IR dem virtuellen Netzwerk beitreten kann. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel wird das Abrufen von Informationen zu einer Azure-SSIS-Integrationslaufzeit veranschaulicht, und er enthält Beschreibungen der Status in den zurückgegebenen Informationen. 
 
