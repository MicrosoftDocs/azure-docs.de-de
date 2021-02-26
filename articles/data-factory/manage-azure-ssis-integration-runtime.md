---
title: Neukonfigurieren der Azure-SSIS-Integration Runtime
description: Erfahren Sie, wie Sie eine Azure SSIS Integration Runtime in Azure Data Factory neu konfigurieren, nachdem Sie sie bereits bereitgestellt haben.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370681"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Neukonfigurieren der Azure-SSIS-Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel erfahren Sie, wie Sie eine Instanz von Azure-SSIS Integration Runtime neu konfigurieren. Informationen zum Erstellen einer Azure-SSIS-Integration Runtime (IR) in Azure Data Factory finden Sie im Artikel [Erstellen einer Azure SSIS Integration Runtime](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory-Benutzeroberfläche 
Sie können die Data Factory-Benutzeroberfläche verwenden, um eine Azure-SSIS IR zu beenden, zu bearbeiten bzw. neu zu konfigurieren oder zu löschen. 

1. Öffnen Sie die Data Factory-Benutzeroberfläche, indem Sie auf der Startseite Ihrer Data Factory auf die Kachel **Author & Monitor** (Erstellen und überwachen) klicken.
2. Klicken Sie unter den Hubs **Start**, **Bearbeiten** und **Überwachen** auf den Hub **Verwalten**, um den Bereich **Verbindungen** anzuzeigen.

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Neukonfigurieren einer Azure-SSIS IR
Wechseln Sie im Bereich **Verbindungen** von **Verwaltungshub** zur Seite **Integration Runtimes**, und wählen Sie **Aktualisieren** aus. 

   ![Bereich „Verbindungen“](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Sie können Ihre Azure-SSIS IR bearbeiten/neu konfigurieren, indem Sie deren Namen auswählen. Sie können auch die entsprechenden Schaltflächen zum Überwachen/Starten/Beenden/Löschen Ihrer Azure-SSIS IR, zum automatischen Generieren einer ADF-Pipeline mit der Aktivität „SSIS-Paket ausführen“ zur Ausführung in Ihrer Azure-SSIS IR und zum Anzeigen des JSON-Codes bzw. der Nutzlast Ihrer Azure-SSIS IR auswählen.  Ihre Azure-SSIS IR kann nur bearbeitet/gelöscht werden, wenn sie angehalten wurde.

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
- [Tutorial: Bereitstellen von SSIS-Paketen in Azure](./tutorial-deploy-ssis-packages-azure.md): Dieser Artikel enthält schrittweise Anleitungen zum Erstellen einer Azure-SSIS IR, und darin wird eine Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. 
- [Vorgehensweise: Azure-SSIS Integration Runtime in Azure Data Factory](create-azure-ssis-integration-runtime.md): In diesem Artikel wird das Tutorial vertieft, und er enthält Anleitungen zur Verwendung einer verwalteten Azure SQL-Instanz und zum Verknüpfen der IR mit einem virtuellen Netzwerk. 
- [Verknüpfen einer Azure-SSIS-Integration Runtime mit einem virtuellen Netzwerk:](join-azure-ssis-integration-runtime-virtual-network.md). Dieser Artikel enthält grundlegende Informationen zum Verknüpfen einer Azure-SSIS-IR mit einem virtuellen Azure-Netzwerk. Darüber hinaus enthält er Schritte zur Verwendung des Azure-Portals zum Konfigurieren des virtuellen Netzwerks, damit die Azure-SSIS-IR dem virtuellen Netzwerk beitreten kann. 
- [Überwachen einer Azure-SSIS-Integrationslaufzeit](monitor-integration-runtime.md#azure-ssis-integration-runtime): In diesem Artikel wird das Abrufen von Informationen zu einer Azure-SSIS-Integrationslaufzeit veranschaulicht, und er enthält Beschreibungen der Status in den zurückgegebenen Informationen.