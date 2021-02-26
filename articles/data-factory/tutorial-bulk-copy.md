---
title: Massenkopieren von Daten mithilfe von PowerShell
description: Verwenden Sie Azure Data Factory mit der Copy-Aktivität zum Kopieren von Daten per Massenvorgang aus einem Quelldatenspeicher in einen Zieldatenspeicher.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 01/22/2018
ms.openlocfilehash: bbd0a98ad14aeef31232aa2557860989e39273aa
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375118"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-using-powershell"></a>Massenkopieren mehrerer Tabellen mithilfe von Azure Data Factory und PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial wird das **Kopieren von mehreren Tabellen aus einer Azure SQL-Datenbank in Azure Synapse Analytics** veranschaulicht. Sie können dieses Muster auch in anderen Kopierszenarios anwenden. So können Sie z.B. Tabellen aus SQL Server/Oracle in Azure SQL-Datenbank/Data Warehouse/Azure Blob kopieren oder verschiedene Pfade aus Blob in Azure SQL-Datenbanktabellen.

Das Tutorial umfasst die folgenden Schritte:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen verknüpfter Azure SQL-Datenbank-, Azure Synapse Analytics- und Azure Storage-Dienste
> * Erstellen von Azure SQL-Datenbank- und Azure Synapse Analytics-Datasets
> * Erstellen einer Pipeline zum Abrufen der zu kopierenden Tabellen und einer weiteren Pipeline zur Durchführung des eigentlichen Kopiervorgangs. 
> * Starten einer Pipelineausführung
> * Überwachen der Pipeline- und Aktivitätsausführungen.

In diesem Tutorial wird Azure PowerShell verwendet. Informationen zur Verwendung von anderen Tools/SDKs zum Erstellen einer Data Factory finden Sie unter [Schnellstarts](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletter Workflow
In diesem Szenario sollen mehrere Tabellen aus Azure SQL-Datenbank in Azure Synapse Analytics kopiert werden. Nachfolgend ist der logische Ablauf eines Workflows dargestellt, der in Pipelines ausgeführt wird:

![Workflow](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Die erste Pipeline ruft die Liste mit den Tabellen ab, die in die Senkendatenspeicher kopiert werden sollen.  Sie können stattdessen auch eine Metadatentabelle mit den Tabellen verwalten, die in die Senkendatenspeicher kopiert werden sollen. Die Pipeline löst anschließend eine weitere Pipeline aus, die wiederum jede Tabelle in der Datenbank durchläuft und den Datenkopiervorgang ausführt.
* Die zweite Pipeline führt den eigentlichen Kopiervorgang aus. Dazu wird die Liste mit den Tabellen als Parameter verwendet. Kopieren Sie für jede Tabelle in der Liste die jeweilige Tabelle aus Azure SQL-Datenbank in die entsprechende Tabelle in Azure Synapse Analytics. Verwenden Sie für eine optimale Leistung das [gestaffelte Kopieren über Blob Storage und PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics). In diesem Beispiel wird die Liste mit den Tabellen von der ersten Pipeline als Wert für den Parameter übergeben. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).
* **Azure Storage-Konto**. Das Azure Storage-Konto wird im Massenkopiervorgang als Staging-Blobspeicher verwendet. 
* **Azure SQL-Datenbank**. Diese Datenbank enthält die Quelldaten. 
* **Azure Synapse Analytics**: Dieses Data Warehouse enthält die Daten, die aus der SQL-Datenbank kopiert werden. 

### <a name="prepare-sql-database-and-azure-synapse-analytics"></a>Vorbereiten von SQL-Datenbank und Azure Synapse Analytics

**Vorbereiten der Azure SQL-Quelldatenbank**:

Erstellen Sie eine Datenbank mit den AdventureWorks LT-Beispieldaten in SQL-Datenbank anhand der Informationen aus dem Artikel [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). In diesem Tutorial werden alle Tabellen aus der Beispieldatenbank in Azure Synapse Analytics kopiert.

**Vorbereiten der Azure Synapse Analytics-Senke:**

1. Wenn Sie über keinen Azure Synapse Analytics-Arbeitsbereich verfügen, führen Sie die Schritte im Artikel [Erste Schritte mit Azure Synapse Analytics](..\synapse-analytics\get-started.md) aus, um einen Arbeitsbereich zu erstellen.

2. Erstellen Sie entsprechende Tabellenschemas in Azure Synapse Analytics. In einem späteren Schritt können Sie Daten mit Azure Data Factory migrieren/kopieren.

## <a name="azure-services-to-access-sql-server"></a>Azure-Dienste für den Zugriff auf SQL-Server

Gewähren Sie den Azure-Diensten sowohl für SQL-Datenbank als auch für Azure Synapse Analytics den Zugriff auf SQL Server. Stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihren Server auf **EIN** festgelegt ist. Mit dieser Einstellung wird dem Data Factory-Dienst erlaubt, Daten aus Ihrer Azure SQL-Datenbank-Instanz zu lesen und in Azure Synapse Analytics zu schreiben. Führen Sie folgende Schritte aus, um diese Einstellung zu überprüfen und zu aktivieren:

1. Klicken Sie links auf **Alle Dienste** und anschließend auf **SQL Server**.
2. Wählen Sie Ihren Server aus, und klicken Sie unter **EINSTELLUNGEN** auf **Firewall**.
3. Klicken Sie auf der Seite **Firewalleinstellungen** für **Zugriff auf Azure-Dienste erlauben** auf **EIN**.

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie **PowerShell**. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.

    Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:
        
    ```powershell
    Connect-AzAccount
    ```
    Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```
    Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **SubscriptionId** durch die ID Ihres Azure-Abonnements:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Führen Sie zum Erstellen einer Data Factory das Cmdlet **Set-AzDataFactoryV2** aus. Ersetzen Sie vor dem Ausführen des Befehls die Platzhalter durch Ihre eigenen Werte. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Beachten Sie folgende Punkte:

    * Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * Um Data Factory-Instanzen zu erstellen, müssen Sie Mitwirkender oder Administrator des Azure-Abonnements sein.
    * Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

In diesem Tutorial erstellen Sie drei verknüpfte Dienste für Quell-, Senken- und Stagingblob, die Verbindungen mit Ihren Datenspeichern enthalten:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Erstellen des verknüpften Quelldiensts Azure SQL-Datenbank

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **AzureSqlDatabaseLinkedService.json** und dem folgenden Inhalt: (Erstellen Sie den Ordner „ADFv2TutorialBulkCopy“, falls dieser noch nicht vorhanden ist.)

    > [!IMPORTANT]
    > Ersetzen Sie &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; und &lt;password&gt; durch Werte Ihrer Azure SQL-Datenbank. Speichern Sie anschließend die Datei.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Wechseln Sie in **Azure PowerShell** zum Ordner **ADFv2TutorialBulkCopy**.

3. Führen Sie das Cmdlet **Set-AzDataFactoryV2LinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Erstellen des verknüpften Diensts für die Azure Synapse Analytics-Senke

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **AzureSqlDWLinkedService.json** und dem folgenden Inhalt:

    > [!IMPORTANT]
    > Ersetzen Sie &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; und &lt;password&gt; durch Werte Ihrer Azure SQL-Datenbank. Speichern Sie anschließend die Datei.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des verknüpften Diensts **AzureSqlDWLinkedService** das Cmdlet **Set-AzDataFactoryV2LinkedService** aus.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Erstellen des verknüpften Stagingdiensts Azure Storage

In diesem Tutorial wird Azure Blob Storage als vorläufiger Stagingbereich zur Aktivierung von PolyBase verwendet, um eine bessere Leistung zu erzielen.

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **AzureStorageLinkedService.json** und dem folgenden Inhalt:

    > [!IMPORTANT]
    > Ersetzen Sie &lt;accountname&gt; und &lt;accountkey&gt; durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos, bevor Sie die Datei speichern.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des verknüpften Diensts **AzureStorageLinkedService** das Cmdlet **Set-AzDataFactoryV2LinkedService** aus.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-datasets"></a>Erstellen von Datasets

In diesem Tutorial werden Quell- und Senkendatasets erstellt, die den Speicherort der Daten angeben:

### <a name="create-a-dataset-for-source-sql-database"></a>Erstellen eines Datasets für die SQL-Quelldatenbank

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **AzureSqlDatabaseDataset.json** und dem folgenden Inhalt. „tableName“ stellt einen Dummy dar. Später verwenden Sie in der Kopieraktivität die SQL-Abfrage zum Abrufen von Daten.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des Datasets **AzureSqlDatabaseDataset** das Cmdlet **Set-AzDataFactoryV2Dataset** aus.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Erstellen eines Datasets für die Azure Synapse Analytics-Senke

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **AzureSqlDWDataset.json** und dem folgenden Inhalt: „tableName“ ist als Parameter festgelegt. Später übergibt die Kopieraktivität, die auf dieses Dataset verweist, den tatsächlichen Wert in das Dataset.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des Datasets **AzureSqlDWDataset** das Cmdlet **Set-AzDataFactoryV2Dataset** aus.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Erstellen von Pipelines

In diesem Tutorial werden zwei Pipelines erstellt:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Erstellen der Pipeline „IterateAndCopySQLTables“

Diese Pipeline verwendet die Liste mit den Tabellen als Parameter. Für jede Tabelle in der Liste werden Daten aus der Tabelle in Azure SQL-Datenbank nach Azure Synapse Analytics kopiert. Dazu wird das gestaffelte Kopieren und PolyBase verwendet.

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **IterateAndCopySQLTables.json** und dem folgenden Inhalt:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from Azure SQL Database to Azure Synapse Analytics",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Führen Sie zum Erstellen der Pipeline **IterateAndCopySQLTables** das Cmdlet **Set-AzDataFactoryV2Pipeline** aus.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Erstellen der Pipeline „GetTableListAndTriggerCopyData“

Mit dieser Pipeline werden zwei Schritte ausgeführt:

* Abrufen der Systemtabelle für die Azure SQL-Datenbank, um die Liste mit den Tabellen abzurufen, die kopiert werden sollen.
* Auslösen der Pipeline „IterateAndCopySQLTables“, um den eigentlichen Kopiervorgang der Daten auszuführen.

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **GetTableListAndTriggerCopyData.json** und dem folgenden Inhalt:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Führen Sie zum Erstellen der Pipeline **GetTableListAndTriggerCopyData** das Cmdlet **Set-AzDataFactoryV2Pipeline** aus.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Starten und Überwachen der Pipelineausführung

1. Starten Sie eine Pipelineausführung für die Hauptpipeline „GetTableListAndTriggerCopyData“, und erfassen Sie die ID der Pipelineausführung für die zukünftige Überwachung. Darunter wird die Ausführung der Pipeline „IterateAndCopySQLTables“ wie in der Aktivität „ExecutePipeline“ angegeben ausgelöst.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Führen Sie das folgende Skript aus, um den Status der Pipeline **GetTableListAndTriggerCopyData** zu überprüfen. Drucken Sie das Ergebnis der endgültigen Pipeline- und Aktivitätsausführung aus.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Hier ist die Ausgabe der Beispielausführung:

    ```console
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Rufen Sie die Ausführungs-ID der Pipeline **IterateAndCopySQLTables** ab, und überprüfen Sie das detaillierte Ergebnis der Aktivitätsausführung wie folgt:

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Hier ist die Ausgabe der Beispielausführung:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Stellen Sie eine Verbindung mit der Azure Synapse Analytics-Senke her, und überprüfen Sie, ob die Daten aus Azure SQL-Datenbank ordnungsgemäß kopiert wurden.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen verknüpfter Azure SQL-Datenbank-, Azure Synapse Analytics- und Azure Storage-Dienste
> * Erstellen von Azure SQL-Datenbank- und Azure Synapse Analytics-Datasets
> * Erstellen einer Pipeline zum Abrufen der zu kopierenden Tabellen und einer weiteren Pipeline zur Durchführung des eigentlichen Kopiervorgangs. 
> * Starten einer Pipelineausführung
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Fahren Sie nun mit dem folgenden Tutorial fort, um mehr über das inkrementelle Kopieren von Daten aus einer Quelle in ein Ziel zu erfahren:
> [!div class="nextstepaction"]
>[Inkrementelles Kopieren von Daten](tutorial-incremental-copy-powershell.md)
