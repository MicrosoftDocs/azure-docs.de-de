---
title: Massenkopieren von Daten mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: In diesem Artikel wird erklärt, wie Sie Azure Data Factory und Kopieraktivität zum Kopieren von Daten per Massenvorgang aus einem Quelldatenspeicher in einen Zieldatenspeicher verwenden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 88fd480f87c3a33af1d16e57d6687d739dc1ec7d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66123418"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>Massenkopieren von mehreren Tabellen mithilfe von Azure Data Factory
In diesem Tutorial wird das **Kopieren von mehreren Tabellen aus einer Azure SQL-Datenbank in Azure SQL Data Warehouse** veranschaulicht. Sie können dieses Muster auch in anderen Kopierszenarios anwenden. So können Sie z.B. Tabellen aus SQL Server/Oracle in Azure SQL-Datenbank/Data Warehouse/Azure Blob kopieren oder verschiedene Pfade aus Blob in Azure SQL-Datenbanktabellen.

Das Tutorial umfasst die folgenden Schritte:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen von Azure SQL-Datenbank, Azure SQL Data Warehouse und mit Azure Storage verknüpften Diensten.
> * Erstellen von Datasets für Azure SQL-Datenbank und Azure SQL Data Warehouse.
> * Erstellen einer Pipeline zum Abrufen der zu kopierenden Tabellen und einer weiteren Pipeline zur Durchführung des eigentlichen Kopiervorgangs. 
> * Starten einer Pipelineausführung
> * Überwachen der Pipeline- und Aktivitätsausführungen.

In diesem Tutorial wird Azure PowerShell verwendet. Informationen zur Verwendung von anderen Tools/SDKs zum Erstellen einer Data Factory finden Sie unter [Schnellstarts](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Kompletter Workflow
In diesem Szenario sollen mehrere Tabellen aus der Azure SQL-Datenbank in SQL Data Warehouse kopiert werden. Nachfolgend ist der logische Ablauf eines Workflows dargestellt, der in Pipelines ausgeführt wird:

![Workflow](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Die erste Pipeline ruft die Liste mit den Tabellen ab, die in die Senkendatenspeicher kopiert werden sollen.  Sie können stattdessen auch eine Metadatentabelle mit den Tabellen verwalten, die in die Senkendatenspeicher kopiert werden sollen. Die Pipeline löst anschließend eine weitere Pipeline aus, die wiederum jede Tabelle in der Datenbank durchläuft und den Datenkopiervorgang ausführt.
* Die zweite Pipeline führt den eigentlichen Kopiervorgang aus. Dazu wird die Liste mit den Tabellen als Parameter verwendet. Kopieren Sie für jede Tabelle in der Liste die jeweilige Tabelle aus der Azure SQL-Datenbank in die entsprechende Tabelle in SQL Data Warehouse. Verwenden Sie für eine optimale Leistung das [gestaffelte Kopieren über Blob Storage und PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). In diesem Beispiel wird die Liste mit den Tabellen von der ersten Pipeline als Wert für den Parameter übergeben. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).
* **Azure Storage-Konto**. Das Azure Storage-Konto wird im Massenkopiervorgang als Staging-Blobspeicher verwendet. 
* **Azure SQL-Datenbank**. Diese Datenbank enthält die Quelldaten. 
* **Azure SQL Data Warehouse**. Dieses Data Warehouse enthält die Daten, die aus der SQL-Datenbank kopiert werden. 

### <a name="prepare-sql-database-and-sql-data-warehouse"></a>Vorbereiten von SQL-Datenbank und SQL Data Warehouse

**Vorbereiten der Azure SQL-Quelldatenbank**:

Erstellen Sie eine Azure SQL-Datenbank mit Adventure Works LT-Beispieldaten, indem Sie den Anweisungen im Artikel [Erstellen einer Azure SQL-Datenbank](../sql-database/sql-database-get-started-portal.md) folgen. In diesem Tutorial werden alle Tabellen aus der Beispieldatenbank in SQL Data Warehouse kopiert.

**Vorbereiten des Senkenwarehouses Azure SQL Data Warehouse**:

1. Wenn Sie noch kein Azure SQL Data Warehouse erstellt haben, finden Sie die Anleitung dazu im Artikel [Erstellen eines SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).

2. Erstellen Sie in SQL Data Warehouse übereinstimmende Tabellenschemas. Verwenden Sie das [Hilfsprogramm für die Migration](https://www.microsoft.com/download/details.aspx?id=49100) zum **Migrieren von Schemas** aus einer Azure SQL-Datenbank zu Azure SQL Data Warehouse. In einem späteren Schritt können Sie Daten mit Azure Data Factory migrieren/kopieren.

## <a name="azure-services-to-access-sql-server"></a>Azure-Dienste für den Zugriff auf SQL-Server

Erlauben Sie Azure-Diensten den Zugriff auf SQL-Server. Das gilt sowohl für die SQL-Datenbank als auch für SQL Data Warehouse. Stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihren Azure SQL-Server auf **EIN** festgelegt ist. Mit dieser Einstellung wird dem Data Factory-Dienst erlaubt, Daten aus Ihrer Azure SQL-Datenbank zu lesen und in Ihr Azure SQL Data Warehouse zu schreiben. Führen Sie folgende Schritte aus, um diese Einstellung zu überprüfen und zu aktivieren:

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
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
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

    ```json
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-sql-data-warehouse-linked-service"></a>Erstellen des verknüpften Senkendiensts Azure SQL Data Warehouse

1. Erstellen Sie im Ordner **C:\ADFv2TutorialBulkCopy** eine JSON-Datei mit dem Namen **AzureSqlDWLinkedService.json** und dem folgenden Inhalt:

    > [!IMPORTANT]
    > Ersetzen Sie &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; und &lt;password&gt; durch Werte Ihrer Azure SQL-Datenbank. Speichern Sie anschließend die Datei.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des verknüpften Diensts **AzureSqlDWLinkedService** das Cmdlet **Set-AzDataFactoryV2LinkedService** aus.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```json
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
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
                }
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des verknüpften Diensts **AzureStorageLinkedService** das Cmdlet **Set-AzDataFactoryV2LinkedService** aus.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```json
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

    ```json
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-sql-data-warehouse"></a>Erstellen eines Datasets für das Senkenwarehouse SQL Data Warehouse

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

    ```json
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Erstellen von Pipelines

In diesem Tutorial werden zwei Pipelines erstellt:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Erstellen der Pipeline „IterateAndCopySQLTables“

Diese Pipeline verwendet die Liste mit den Tabellen als Parameter. Für jede Tabelle in der Liste werden Daten aus der Tabelle in der Azure SQL-Datenbank in Azure SQL Data Warehouse kopiert. Dazu wird das gestaffelte Kopieren und PolyBase verwendet.

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
                                "description": "Copy data from SQL database to SQL DW",
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

    ```json
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

    ```json
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

    ```json
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

3. Verbinden Sie sich mit Ihrem Senkenwarehouse Azure SQL Data Warehouse, und bestätigen Sie, dass die Daten aus der Azure SQL-Datenbank ordnungsgemäß kopiert wurden.

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen von Azure SQL-Datenbank, Azure SQL Data Warehouse und mit Azure Storage verknüpften Diensten.
> * Erstellen von Datasets für Azure SQL-Datenbank und Azure SQL Data Warehouse.
> * Erstellen einer Pipeline zum Abrufen der zu kopierenden Tabellen und einer weiteren Pipeline zur Durchführung des eigentlichen Kopiervorgangs. 
> * Starten einer Pipelineausführung
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Fahren Sie nun mit dem folgenden Tutorial fort, um mehr über das inkrementelle Kopieren von Daten aus einer Quelle in ein Ziel zu erfahren:
> [!div class="nextstepaction"]
>[Inkrementelles Kopieren von Daten](tutorial-incremental-copy-powershell.md)
