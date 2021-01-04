---
title: 'Ausführen eines SSIS-Pakets mit der Aktivität „Gespeicherte Prozedur“: Azure'
description: In diesem Artikel wird das Ausführen eines SSIS-Pakets (SQL Server Integration Services) in einer Azure Data Factory-Pipeline mithilfe der Aktivität einer gespeicherten Prozedur beschrieben.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6cb3bf53db14c6c01290abea7a5c48c332a07632
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634877"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Ausführen eines SSIS-Pakets mit der Aktivität einer gespeicherten Prozedur in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird das Ausführen eines SSIS-Pakets in einer Azure Data Factory-Pipeline mithilfe der Aktivität einer gespeicherten Prozedur beschrieben. 

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-sql-database"></a>Azure SQL-Datenbank 
In der in diesem Artikel ausgelegten exemplarischen Vorgehensweise wird Azure SQL-Datenbank zum Hosten des SSIS-Katalogs verwendet. Alternativ können Sie eine Azure SQL Managed Instance verwenden.

## <a name="create-an-azure-ssis-integration-runtime"></a>Erstellen einer Azure SSIS Integration Runtime
Erstellen Sie eine Azure-SSIS Integration Runtime, falls Sie noch keine besitzen. Befolgen Sie dazu die ausführliche Anleitung unter [Tutorial: Bereitstellen von SSIS-Paketen](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory-Benutzeroberfläche (Azure-Portal)
In diesem Abschnitt erstellen Sie mithilfe der Data Factory-Benutzeroberfläche eine Data Factory-Pipeline mit der Aktivität einer gespeicherten Prozedur, die ein SSIS-Paket aufruft.

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Der erste Schritt besteht darin, eine Data Factory mit dem Azure-Portal zu erstellen. 

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome** . Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
2. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). 
3. Klicken Sie im Menü auf der linken Seite nacheinander auf **Neu** , **Data + Analytics** und **Data Factory** . 
   
   ![Neu -> Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Geben Sie auf der Seite **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein. 
      
     ![Seite „Neue Data Factory“](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Der Name der Azure Data Factory muss **global eindeutig** sein. Sollte für das Feld „Name“ der folgende Fehler auftreten, ändern Sie den Namen der Data Factory (beispielsweise in „<IhrName>ADFTutorialDataFactory“). Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).
  
     ![Name nicht verfügbar – Fehler](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Wählen Sie Ihr **Azure-Abonnement** aus, in dem die Data Factory erstellt werden soll. 
4. Führen Sie für die **Ressourcengruppe** einen der folgenden Schritte aus:
     
   - Wählen Sie die Option **Use existing** (Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
   - Wählen Sie **Neu erstellen** , und geben Sie den Namen einer Ressourcengruppe ein.   
         
     Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
4. Wählen Sie **V2** als **Version** aus.
5. Wählen Sie den **Standort** für die Data Factory aus. In der Dropdownliste werden nur Standorte angezeigt, die von Data Factory unterstützt werden. Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich an anderen Standorten befinden.
6. Wählen Sie die Option **An Dashboard anheften** aus.     
7. Klicken Sie auf **Erstellen** .
8. Auf dem Dashboard sehen Sie die folgende Kachel mit dem Status: **Deploying data factory** (Data Factory wird bereitgestellt...). 

     ![Kachel „Die Data Factory wird bereitgestellt“](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Nach Abschluss der Erstellung wird die Seite **Data Factory** wie in der Abbildung angezeigt.
   
     ![Data Factory-Startseite](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klicken Sie auf die Kachel **Erstellen und überwachen** , um die Anwendung für die Azure Data Factory-Benutzeroberfläche (User Interface, UI) auf einer separaten Registerkarte zu starten. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Erstellen einer Pipeline mit einer Aktivität einer gespeicherten Prozedur
In diesem Schritt erstellen Sie über die Data Factory-Benutzeroberfläche eine Pipeline. Sie fügen eine Aktivität einer gespeicherten Prozedur zur Pipeline hinzu und konfigurieren diese mit der gespeicherten Prozedur „sp_executesql“ für die Ausführung des SSIS-Pakets. 

1. Klicken Sie auf der Seite „Erste Schritte“ auf **Pipeline erstellen** . 

    ![Seite für die ersten Schritte](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Erweitern Sie in der Toolbox **Aktivitäten** die Option **Allgemein** , ziehen Sie die Aktivität **Gespeicherte Prozedur** auf die Oberfläche des Pipeline-Designers, und legen Sie sie dort ab. 

    ![Drag & Drop-Aktivität „Gespeicherte Prozedur“](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Wechseln Sie im Eigenschaftenfenster der Aktivität „Gespeicherte Prozedur“ zur Registerkarte **SQL-Konto** , und klicken Sie auf **+ Neu** . Sie erstellen eine Verbindung mit der Datenbank in Azure SQL-Datenbank, die als Host für den SSIS-Katalog (SSIDB-Datenbank) fungiert. 
   
    ![Schaltfläche für neuen verknüpften Dienst](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus: 

    1. Wählen Sie **Azure SQL-Datenbank** als **Typ** aus.
    2. Wählen Sie die **standardmäßige** Azure-Integration Runtime, um sich mit der Azure SQL-Datenbank zu verbinden, die die `SSISDB`-Datenbank hostet.
    3. Wählen Sie für das Feld **Servername** die Azure SQL-Datenbank aus, die die SSISDB-Datenbank hostet.
    4. Wählen Sie **SSISDB** als **Datenbankname** aus.
    5. Geben Sie unter **Benutzername** den Namen des Benutzers ein, der Zugriff auf die Datenbank hat.
    6. Geben Sie unter **Kennwort** das Kennwort des Benutzers ein. 
    7. Testen Sie die Verbindung mit der Datenbank, indem Sie auf die Schaltfläche **Verbindung testen** klicken.
    8. Speichern Sie den verknüpften Dienst, indem Sie auf die Schaltfläche **Speichern** klicken. 

        ![Screenshot: Prozess zum Hinzufügen eines neuen verknüpften Diensts](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Wechseln Sie im Eigenschaftenfenster von der Registerkarte **SQL-Konto** zur Registerkarte **Gespeicherte Prozedur** , und führen Sie folgende Schritte durch: 

    1. Wählen Sie **Bearbeiten** aus. 
    2. Geben Sie `sp_executesql` in das Feld **Name der gespeicherten Prozedur** ein. 
    3. Klicken Sie im Abschnitt **Parameter der gespeicherten Prozedur** auf **+ Neu** . 
    4. Geben Sie unter **Name** des Parameters **stmt** ein. 
    5. Geben Sie unter **Typ** des Parameters **Zeichenfolge** ein. 
    6. Geben Sie unter **Wert** des Parameters die folgende SQL-Abfrage ein:

        Geben Sie in der SQL-Abfrage die entsprechenden Werte für die Parameter **folder_name** , **project_name** und **package_name** ein. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Mit Azure SQL-Datenbank verknüpfter Dienst](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Klicken Sie zum Überprüfen der Pipelinekonfiguration in der Symbolleiste auf **Überprüfen** . Klicken Sie zum Schließen des **Pipeline Validation Report** (Pipelineüberprüfungsbericht) auf **>>** .

    ![Überprüfen der Pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Veröffentlichen Sie die Pipeline in der Data Factory, indem Sie auf die Schaltfläche **Alle veröffentlichen** klicken. 

    ![Veröffentlichen](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Ausführen und Überwachen der Pipeline
In diesem Abschnitt lösen Sie eine Pipelineausführung aus, und überwachen diese. 

1. Klicken Sie auf der Symbolleiste auf **Trigger** und anschließend auf **Jetzt auslösen** , um eine Pipelineausführung auszulösen. 

    ![Manuelles Auslösen](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. Wählen Sie im Fenster **Pipelineausführung** die Option **Fertig stellen** aus. 
3. Wechseln Sie im linken Bereich zur Registerkarte **Überwachen** . Es werden die Pipelineausführung, der zugehörige Status sowie weitere Informationen (z.B. Startzeit der Ausführung) angezeigt. Klicken Sie zum Aktualisieren der Ansicht auf **Aktualisieren** .

    ![Pipelineausführungen](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klicken Sie in der Spalte **Aktionen** auf den Link **Aktivitätsausführungen anzeigen** . Es wird nur eine Aktivitätsausführung angezeigt, da die Pipeline nur eine Aktivität (Aktivität „Gespeicherte Prozedur“) enthält.

    ![Aktivitätsausführungen](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Sie können die folgende **Abfrage** für die SSISDB-Datenbank in SQL-Datenbank ausführen, um zu überprüfen, ob das Paket ausgeführt wurde. 

    ```sql
    select * from catalog.executions
    ```

    ![Überprüfen von Paketausführungen](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Sie können auch einen geplanten Trigger für die Pipeline erstellen, damit die Pipeline basierend auf einem Zeitplan (stündlich, täglich usw.) ausgeführt wird. Ein Beispiel finden Sie unter [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Abschnitt erstellen Sie mithilfe von Azure PowerShell eine Data Factory-Pipeline mit der Aktivität einer gespeicherten Prozedur, die ein SSIS-Paket aufruft. 

Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) befolgen. 

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Sie können wahlweise die gleiche Data Factory verwenden, die die Azure-SSIS IR aufweist, oder eine separate Data Factory erstellen. Das folgende Verfahren beschreibt die Schritte zum Erstellen einer Data Factory. In dieser Data Factory erstellen Sie eine Pipeline mit einer Aktivität einer gespeicherten Prozedur. Die Aktivität der gespeicherten Prozedur führt eine gespeicherte Prozedur in der SSISDB-Datenbank aus, um Ihr SSIS-Paket auszuführen. 

1. Definieren Sie eine Variable für den Ressourcengruppennamen zur späteren Verwendung in PowerShell-Befehlen. Kopieren Sie den folgenden Befehlstext nach PowerShell, geben Sie einen Namen für die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) in doppelten Anführungszeichen an, und führen Sie dann den Befehl aus. Beispiel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$ResourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.
2. Führen Sie den folgenden Befehl aus, um die Azure-Ressourcengruppe zu erstellen: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$ResourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus. 
3. Definieren Sie eine Variable für den Namen der Data Factory. 

    > [!IMPORTANT]
    >  Aktualisieren Sie den Data Factory-Namen, damit er global eindeutig ist. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Führen Sie zum Erstellen der Data Factory das folgende Cmdlet vom Typ **Set-AzDataFactoryV2** aus. Verwenden Sie dabei den Standort und die Eigenschaft „ResourceGroupName“ aus der Variablen „$ResGrp“: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle **Mitwirkender** oder **Besitzer** oder ein **Administrator** des Azure-Abonnements sein.
* Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

### <a name="create-an-azure-sql-database-linked-service"></a>Erstellen eines verknüpften Azure SQL-Datenbank-Diensts
In diesem Schritt erstellen Sie einen verknüpften Dienst, um Ihre Datenbank, die als Host für den SSIS-Katalog fungiert, mit Ihrer Data Factory zu verknüpfen. Die Data Factory verwendet Informationen in diesem verknüpften Dienst zum Herstellen der Verbindung mit der SSISDB-Datenbank und führt eine gespeicherte Prozedur zum Ausführen eines SSIS-Pakets aus. 

1. Erstellen Sie im Ordner **C:\ADF\RUNSSISPackage** eine JSON-Datei mit dem Namen **AzureSQLDatabaseLinkedService.json** und folgendem Inhalt: 

    > [!IMPORTANT]
    > Ersetzen Sie &lt;servername&gt;, &lt;username&gt; und &lt;password&gt; durch Werte Ihrer Azure SQL-Datenbank. Speichern Sie anschließend die Datei.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Wechseln Sie in **Azure PowerShell** zum Ordner **C:\ADF\RunSSISPackage** .

3. Führen Sie das Cmdlet **Set-AzDataFactoryV2LinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureSqlDatabaseLinkedService** . 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Erstellen einer Pipeline mit einer Aktivität einer gespeicherten Prozedur 
In diesem Schritt erstellen Sie eine Pipeline mit einer Aktivität einer gespeicherten Prozedur. Die Aktivität ruft die gespeicherte Prozedur „sp_executesql“ auf, um Ihr SSIS-Paket auszuführen. 

1. Erstellen Sie im Ordner **C:\ADF\RunSSISPackage** eine JSON-Datei mit dem Namen **RunSSISPackagePipeline.json** und folgendem Inhalt:

    > [!IMPORTANT]
    > Ersetzen Sie &lt;FOLDER NAME&gt;, &lt;PROJECT NAME&gt;, &lt;PACKAGE NAME&gt; durch die Namen von Ordner, Projekt und Paket im SSIS-Katalog, und speichern Sie dann die Datei. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Führen Sie zum Erstellen der Pipeline **RunSSISPackagePipeline** das Cmdlet **Set-AzDataFactoryV2Pipeline** aus.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Hier ist die Beispielausgabe:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung
Verwenden Sie das Cmdlet **Invoke-AzDataFactoryV2Pipeline** , um die Pipeline auszuführen. Das Cmdlet gibt die ID der Pipelineausführung für die zukünftige Überwachung zurück.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

Führen Sie das folgende PowerShell-Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist. Kopieren Sie das folgende Skript, fügen Sie es in das PowerShell-Fenster ein, und drücken Sie die EINGABETASTE. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Erstellen eines Triggers
Im vorherigen Schritt wurde die Pipeline auf Anforderung aufgerufen. Alternativ können Sie einen Zeitplantrigger erstellen, um die Pipeline nach Zeitplan (stündlich, täglich usw.) auszuführen.

1. Erstellen Sie im Ordner **C:\ADF\RunSSISPackage** eine JSON-Datei mit dem Namen **MyTrigger.json** und dem folgenden Inhalt: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. Wechseln Sie in **Azure PowerShell** zum Ordner **C:\ADF\RunSSISPackage** .
3. Führen Sie das Cmdlet **Set-AzDataFactoryV2Trigger** aus, um den Trigger zu erstellen. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Standardmäßig befindet sich der Trigger im beendeten Zustand. Starten Sie den Trigger durch Ausführen des Cmdlets **Start-AzDataFactoryV2Trigger** . 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Bestätigen Sie, dass der Trigger sich im gestarteten Zustand befindet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** ausführen. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Führen Sie nach der nächsten vollen Stunde den folgenden Befehl aus. Wenn die aktuelle Uhrzeit z.B. 15:25 UTC ist, führen Sie den Befehl um 16:00 UTC aus. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Sie können die folgende Abfrage für die SSISDB-Datenbank in SQL-Datenbank ausführen, um zu überprüfen, ob das Paket ausgeführt wurde. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Nächste Schritte
Sie können die Pipeline auch mithilfe des Azure-Portals überwachen. Schritt-für-Schritt-Anweisungen finden Sie unter [Überwachen der Pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).