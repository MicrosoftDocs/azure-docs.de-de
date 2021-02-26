---
title: Aufrufen des SSIS-Pakets mithilfe von Azure Data Factory – Aktivität der gespeicherten Prozedur
description: In diesem Artikel wird das Aufrufen eines SSIS-Pakets (SQL Server Integration Services) aus einer Azure Data Factory-Pipeline mithilfe einer Aktivität einer gespeicherten Prozedur beschrieben.
author: linda33wj
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 6c831fa1c6351840693f2b10a22f59c5cc424d0f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392883"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Aufrufen eines SSIS-Pakets mithilfe einer Aktivität einer gespeicherten Prozedur in Azure Data Factory
In diesem Artikel wird das Aufrufen eines SSIS-Pakets aus einer Azure Data Factory-Pipeline mithilfe einer Aktivität einer gespeicherten Prozedur beschrieben. 

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Aufrufen von SSIS-Paketen mithilfe der Aktivität für gespeicherte Prozeduren](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Voraussetzungen

### <a name="azure-sql-database"></a>Azure SQL-Datenbank 
In der exemplarischen Vorgehensweise in diesem Artikel wird Azure SQL-Datenbank verwendet. Alternativ können Sie eine verwaltete Azure SQL-Instanz verwenden.

### <a name="create-an-azure-ssis-integration-runtime"></a>Erstellen einer Azure SSIS Integration Runtime
Erstellen Sie eine Azure-SSIS Integration Runtime, falls Sie noch keine besitzen. Befolgen Sie dazu die ausführliche Anleitung unter [Tutorial: Bereitstellen von SSIS-Paketen](../tutorial-deploy-ssis-packages-azure.md). Sie können nicht Data Factory, Version 1 zum Erstellen einer Azure SSIS-Integration Runtime verwenden. 

## <a name="azure-powershell"></a>Azure PowerShell
In diesem Abschnitt erstellen Sie mithilfe von Azure PowerShell eine Data Factory-Pipeline mit einer Aktivität einer gespeicherten Prozedur, die ein SSIS-Paket aufruft.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-az-ps) befolgen.

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory
Das folgende Verfahren beschreibt die Schritte zum Erstellen einer Data Factory. In dieser Data Factory erstellen Sie eine Pipeline mit einer Aktivität einer gespeicherten Prozedur. Die Aktivität der gespeicherten Prozedur führt eine gespeicherte Prozedur in der SSISDB-Datenbank aus, um Ihr SSIS-Paket auszuführen.

1. Definieren Sie eine Variable für den Ressourcengruppennamen zur späteren Verwendung in PowerShell-Befehlen. Kopieren Sie den folgenden Befehlstext nach PowerShell, geben Sie einen Namen für die [Azure-Ressourcengruppe](../../azure-resource-manager/management/overview.md) in doppelten Anführungszeichen an, und führen Sie dann den Befehl aus. Beispiel: `"adfrg"`. 
   
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

5. Führen Sie zum Erstellen der Data Factory das folgende Cmdlet **New-AzDataFactory** aus. Verwenden Sie dabei die Eigenschaften „Location“ und „ResourceGroupName“ aus der Variablen „$ResGrp“: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle **Mitwirkender** oder **Besitzer** oder ein **Administrator** des Azure-Abonnements sein.

### <a name="create-an-azure-sql-database-linked-service"></a>Erstellen eines verknüpften Azure SQL-Datenbank-Diensts
In diesem Schritt erstellen Sie einen verknüpften Dienst, um Ihre Datenbanken in Azure SQL-Datenbank-Instanz, in der der SSIS-Katalog gehostet ist, mit der Data Factory zu verbinden. Die Data Factory verwendet Informationen in diesem verknüpften Dienst zum Herstellen der Verbindung mit der SSISDB-Datenbank und führt eine gespeicherte Prozedur zum Ausführen eines SSIS-Pakets aus. 

1. Erstellen Sie im Ordner **C:\ADF\RUNSSISPackage** eine JSON-Datei mit dem Namen **AzureSQLDatabaseLinkedService.json** und folgendem Inhalt: 

    > [!IMPORTANT]
    > Ersetzen Sie &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; und &lt;password&gt; durch Werte Ihrer Azure SQL-Datenbank. Speichern Sie anschließend die Datei.

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
2. Wechseln Sie in **Azure PowerShell** zum Ordner **C:\ADF\RunSSISPackage**.
3. Führen Sie das Cmdlet **New-AzDataFactoryLinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Erstellen eines Ausgabedatasets
Dieses Ausgabedataset ist ein Dummy-Dataset, das die Grundlage für den Zeitplan der Pipeline bildet. Beachten Sie, dass „Frequency“ auf „Hour“ und „interval“ auf „1“ festgelegt ist. Daher wird die Pipeline einmal pro Stunde innerhalb der Anfangs- und Endzeiten der Pipeline ausgeführt. 

1. Erstellen Sie eine OutputDataset.json-Datei mit dem folgenden Inhalt: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Führen Sie das Cmdlet **New-AzDataFactoryDataset** aus, um ein Dataset zu erstellen. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Erstellen einer Pipeline mit einer Aktivität einer gespeicherten Prozedur 
In diesem Schritt erstellen Sie eine Pipeline mit einer Aktivität einer gespeicherten Prozedur. Die Aktivität ruft die gespeicherte Prozedur „sp_executesql“ auf, um Ihr SSIS-Paket auszuführen. 

1. Erstellen Sie im Ordner **C:\ADF\RunSSISPackage** eine JSON-Datei mit dem Namen **MyPipeline.json** und folgendem Inhalt:

    > [!IMPORTANT]
    > Ersetzen Sie &lt;folder name&gt;, &lt;project name&gt;, &lt;package name&gt; durch die Namen von Ordner, Projekt und Paket im SSIS-Katalog, und speichern Sie dann die Datei.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Führen Sie zum Erstellen der Pipeline **RunSSISPackagePipeline** das Cmdlet **New-AzDataFactoryPipeline** aus.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

1. Führen Sie **Get-AzDataFactorySlice** aus, um Details zu allen Slices des Ausgabedatasets** zu erhalten. Dies ist die Ausgabetabelle der Pipeline.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Beachten Sie, dass der "StartDateTime"-Wert, den Sie hier angeben, der im JSON-Code der Pipeline angegebenen Startzeit entspricht. 
1. Führen Sie **Get-AzDataFactoryRun** aus, um die Details der Aktivitätsausführungen für einen bestimmten Slice abzurufen.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Sie können dieses Cmdlet weiter ausführen, bis der Slice den Status **Bereit** oder **Fehler** hat. 

    Sie können auf Ihrem Server die folgende Abfrage für die SSISDB-Datenbank ausführen, um zu überprüfen, ob das Paket ausgeführt wurde. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nächste Schritte
Detailinformationen zur Aktivität der gespeicherten Prozedur finden Sie im Artikel [Stored Procedure activity](data-factory-stored-proc-activity.md) (Aktivität einer gespeicherten Prozedur).