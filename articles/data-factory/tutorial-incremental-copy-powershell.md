---
title: Inkrementelles Kopieren einer Tabelle mithilfe von PowerShell
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Pipeline, die Daten inkrementell aus Azure SQL-Datenbank in Azure Blob Storage kopiert.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: a3346f3f126b880a5a2983f9dd35aa84f6389a31
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367723"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-powershell"></a>Inkrementelles Laden von Daten aus Azure SQL-Datenbank in Azure Blob Storage mithilfe von PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie mithilfe von Azure Data Factory eine Pipeline, die Deltadaten aus einer Tabelle in Azure SQL-Datenbank in Azure Blob Storage lädt.

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Vorbereiten des Datenspeichers zum Speichern des Grenzwerts.
> * Erstellen einer Data Factory.
> * Erstellen Sie verknüpfte Dienste.
> * Erstellen des Quell-, Senken-, Grenzwertdatasets
> * Erstellen einer Pipeline.
> * Ausführen der Pipeline.
> * Überwachen der Pipelineausführung.

## <a name="overview"></a>Übersicht
Allgemeines Lösungsdiagramm:

![Lädt Daten inkrementell](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

Hier sind die wesentlichen Schritte beim Erstellen dieser Lösung aufgeführt:

1. **Select the watermark column (Wählen Sie die Grenzwert-Spalte aus)** .
    Wählen Sie eine Spalte im Quelldatenspeicher aus, die verwendet werden kann, um die neuen oder aktualisierten Datensätze für jede Ausführung in Segmente aufzuteilen. Normalerweise steigen die Daten in dieser ausgewählten Spalte (z.B. Last_modify_time oder ID), wenn Zeilen erstellt oder aktualisiert werden. Der maximale Wert in dieser Spalte wird als Grenzwert verwendet.

2. **Prepare a data store to store the watermark value (Vorbereiten eines Datenspeichers zum Speichern des Grenzwerts)** .   
    In diesem Tutorial speichern Sie den Grenzwert in einer SQL-Datenbank.

3. **Erstellen einer Pipeline mit dem folgenden Workflow:**

    Die Pipeline in dieser Lösung enthält die folgenden Aktivitäten:

    * Erstellen Sie zwei Lookup-Aktivitäten. Verwenden Sie die erste Lookup-Aktivität, um den letzten Grenzwert abzurufen. Verwenden Sie die zweite Lookup-Aktivität, um den neuen Grenzwert abzurufen. Diese Grenzwerte werden an die Copy-Aktivität übergeben.
    * Erstellen Sie eine Copy-Aktivität, die Zeilen aus dem Quelldatenspeicher kopiert, wobei der Wert der Grenzwertspalte größer als der alte Grenzwert und kleiner als der neue Grenzwert ist. Anschließend werden die Deltadaten aus dem Quelldatenspeicher als neue Datei in einen Blobspeicher kopiert.
    * Erstellen Sie eine StoredProcedure-Aktivität, die den Grenzwert für die Pipeline aktualisiert, die nächstes Mal ausgeführt wird.


Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als den Quelldatenspeicher. Wenn Sie in Azure SQL-Datenbank noch keine Datenbank haben, lesen Sie [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md). Dort finden Sie die erforderlichen Schritte zum Erstellen einer solchen Datenbank.
* **Azure Storage**. Sie verwenden den Blobspeicher als Senkendatenspeicher. Wenn Sie kein Speicherkonto besitzen, finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md) Schritte zum Erstellen eines solchen Kontos. Erstellen Sie einen Container mit dem Namen „adftutorial“. 
* **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).

### <a name="create-a-data-source-table-in-your-sql-database"></a>Erstellen einer Datenquelltabelle in Ihrer SQL-Datenbank
1. Öffnen Sie SQL Server Management Studio. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die Datenbank, und wählen Sie **Neue Abfrage**.

2. Führen Sie den folgenden SQL-Befehl für Ihre SQL-Datenbank aus, um eine Tabelle mit dem Namen `data_source_table` als Quelldatenspeicher zu erstellen.

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    In diesem Tutorial verwenden Sie „LastModifytime“ als die Grenzwertspalte. Die Daten im Quelldatenspeicher sind in der folgenden Tabelle dargestellt:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Erstellen einer anderen Tabelle in SQL-Datenbank zum Speichern des hohen Grenzwerts
1. Führen Sie den folgenden SQL-Befehl für Ihre SQL-Datenbank aus, um eine Tabelle mit dem Namen `watermarktable` zum Speichern des Grenzwerts zu erstellen:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Legen Sie den Standardwert für den hohen Grenzwert mit dem Tabellennamen des Quelldatenspeichers fest. In diesem Tutorial lautet der Tabellenname „data_source_table“.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Überprüfen Sie die Daten in der Tabelle `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Ausgabe:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Erstellen einer gespeicherten Prozedur in Ihrer SQL-Datenbank

Führen Sie den folgenden Befehl zum Erstellen einer gespeicherten Prozedur in Ihrer SQL-Datenbank aus.

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory
1. Definieren Sie eine Variable für den Ressourcengruppennamen zur späteren Verwendung in PowerShell-Befehlen. Kopieren Sie den folgenden Befehlstext nach PowerShell, geben Sie einen Namen für die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) in doppelten Anführungszeichen an, und führen Sie dann den Befehl aus. z. B. `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$resourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.

2. Definieren Sie eine Variable für den Speicherort der Data Factory.

    ```powershell
    $location = "East US"
    ```
3. Führen Sie den folgenden Befehl aus, um die Azure-Ressourcengruppe zu erstellen:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$resourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.

4. Definieren Sie eine Variable für den Namen der Data Factory.

    > [!IMPORTANT]
    >  Aktualisieren Sie den Data Factory-Namen, damit er global eindeutig ist. Ein Beispiel hierfür ist ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Führen Sie zum Erstellen der Data Factory das Cmdlet **Set-AzDataFactoryV2** wie folgt aus:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

Beachten Sie folgende Punkte:

* Der Name der Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle „Mitwirkender“ oder „Besitzer“ oder ein Administrator des Azure-Abonnements sein.
* Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die Datenspeicher (Storage, SQL-Datenbank, Azure SQL Managed Instance usw.) und Computeeinheiten (Azure HDInsight usw.), die von der Data Factory genutzt werden, können sich in anderen Regionen befinden.


## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Abschnitt erstellen Sie verknüpfte Dienste für Ihr Speicherkonto und SQL-Datenbank.

### <a name="create-a-storage-linked-service"></a>Erstellen eines verknüpften Speicherdiensts
1. Erstellen Sie im Ordner „C:\ADF“ eine JSON-Datei mit dem Namen „AzureStorageLinkedService.json“ und folgendem Inhalt. (Erstellen Sie den Ordner „ADF“, wenn er noch nicht vorhanden ist.) Ersetzen Sie `<accountName>` und `<accountKey>` durch den Namen und den Schlüssel Ihres Speicherkontos, bevor Sie die Datei speichern.

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
2. Wechseln Sie in PowerShell zum Ordner „ADF“.

3. Führen Sie das Cmdlet **Set-AzDataFactoryV2LinkedService** aus, um den verknüpften Dienst „AzureStorageLinkedService“ zu erstellen. Im folgenden Beispiel übergeben Sie Werte für die *ResourceGroupName*- und *DataFactoryName*-Parameter:

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

### <a name="create-a-sql-database-linked-service"></a>Erstellen eines mit SQL-Datenbank verknüpften Diensts
1. Erstellen Sie im Ordner „C:\ADF“ eine JSON-Datei mit dem Namen „AzureSQLDatabaseLinkedService.json“ und folgendem Inhalt. (Erstellen Sie den Ordner „ADF“, wenn er noch nicht vorhanden ist.) Ersetzen Sie „&lt;server&gt;“, „&lt;database&gt;“, „&lt;user id&gt;“ und „&lt;password&gt;“ durch den Namen Ihres Servers, die Datenbank, die Benutzer-ID und das Kennwort, bevor Sie die Datei speichern.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. Wechseln Sie in PowerShell zum Ordner „ADF“.

3. Führen Sie das **Set-AzDataFactoryV2LinkedService**-Cmdlet aus, um den verknüpften Dienst „AzureSQLDatabaseLinkedService“ zu erstellen.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets zur Darstellung von Quell- und Senkendaten.

### <a name="create-a-source-dataset"></a>Erstellen eines Quelldatasets

1. Erstellen Sie eine JSON-Datei mit dem Namen „SourceDataset.json“ im selben Ordner und dem folgenden Inhalt:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }

    ```
    In diesem Tutorial verwenden Sie den Tabellennamen „data_source_table“. Ersetzen Sie ihn, wenn Sie eine Tabelle mit einem anderen Namen verwenden.

2. Führen Sie das Cmdlet **Set-AzDataFactoryV2Dataset** aus, um das Dataset „SourceDataset“ zu erstellen.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Hier ist die Beispielausgabe des Cmdlets:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Erstellen Sie ein Senkendataset

1. Erstellen Sie eine JSON-Datei mit dem Namen „SinkDataset.json“ im selben Ordner und dem folgenden Inhalt:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > In diesem Codeausschnitt wird davon ausgegangen, dass Ihr Blobspeicher einen Blobcontainer mit dem Namen `adftutorial` enthält. Erstellen Sie den Container, wenn er noch nicht vorhanden ist, oder geben Sie den Namen eines bereits vorhandenen ein. Der Ausgabeordner `incrementalcopy` wird automatisch erstellt, wenn er nicht bereits im Container vorhanden ist. In diesem Tutorial wird der Dateiname dynamisch mit dem Ausdruck `@CONCAT('Incremental-', pipeline().RunId, '.txt')` generiert.

2. Führen Sie das Cmdlet **Set-AzDataFactoryV2Dataset** aus, um das Dataset „SinkDataset“ zu erstellen.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Hier ist die Beispielausgabe des Cmdlets:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Erstellen eines Datasets für einen Grenzwert
In diesem Schritt erstellen Sie ein Dataset zum Speichern eines hohen Grenzwerts.

1. Erstellen Sie eine JSON-Datei mit dem Namen „WatermarkDataset.json“ im selben Ordner und dem folgenden Inhalt:

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Führen Sie das Cmdlet **Set-AzDataFactoryV2Dataset** aus, um das Dataset „WatermarkDataset“ zu erstellen.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Hier ist die Beispielausgabe des Cmdlets:

    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline
In diesem Tutorial erstellen Sie eine Pipeline mit zwei Lookup-Aktivitäten, einer Kopieraktivität und einer StoredProcedure-Aktivität, die in einer Pipeline verkettet sind.


1. Erstellen Sie in demselben Ordner die JSON-Datei „IncrementalCopyPipeline.json“ mit folgendem Inhalt:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },

                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },

                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },

                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],

                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },

                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {

                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },

                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },

                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
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


2. Führen Sie das Cmdlet **Set-AzDataFactoryV2Pipeline** aus, um die Pipeline „IncrementalCopyPipeline“ zu erstellen.

   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Hier ist die Beispielausgabe:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```

## <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.

1. Führen Sie mithilfe des Cmdlets **Invoke-AzDataFactoryV2Pipeline** die Pipeline „IncrementalCopyPipeline“ aus. Ersetzen Sie Platzhalter mit Ihrem eigenen Ressourcengruppen- und Data Factory-Namen.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
2. Überprüfen Sie den Status der Pipeline durch Ausführen des **Get-AzDataFactoryV2ActivityRun**-Cmdlets, bis Sie sehen, dass alle Aktivitäten erfolgreich ausgeführt wurden. Ersetzen Sie Platzhalter durch Ihre eigene geeignete Zeit für die Parameter *RunStartedAfter* und *RunStartedBefore*. In diesem Tutorial verwenden Sie Folgendes: *-RunStartedAfter "2017/09/14"* und *-RunStartedBefore "2017/09/15"* .

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Hier ist die Beispielausgabe:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Überprüfen der Ergebnisse

1. Im Blobspeicher (Senkenspeicher) sehen Sie, dass die Daten in die im „SinkDataset“ definierte Datei kopiert wurden. Im aktuellen Tutorial ist der Dateiname `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Öffnen Sie die Datei, und Sie können Datensätze in der Datei sehen, die mit den Daten in der SQL-Datenbank identisch sind.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
2. Überprüfen Sie den aktuellen Wert aus `watermarktable`. Sie sehen, dass der Grenzwert aktualisiert wurde.

    ```sql
    Select * from watermarktable
    ```

    Hier ist die Beispielausgabe:

    TableName | Grenzwert
    --------- | --------------
    „Data_source_table“ | 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Einfügen von Daten in den Datenquellspeicher, um das Laden von Deltadaten zu überprüfen

1. Fügen Sie neue Daten in die SQL-Datenbank (Datenquellenspeicher) ein.

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ```

    Die aktualisierten Daten in der SQL-Datenbank lauten:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Führen Sie die Pipeline „IncrementalCopyPipeline“ erneut mithilfe des **Invoke-AzDataFactoryV2Pipeline**-Cmdlets aus. Ersetzen Sie Platzhalter mit Ihrem eigenen Ressourcengruppen- und Data Factory-Namen.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Überprüfen Sie den Status der Pipeline durch Ausführen des **Get-AzDataFactoryV2ActivityRun**-Cmdlets, bis Sie sehen, dass alle Aktivitäten erfolgreich ausgeführt wurden. Ersetzen Sie Platzhalter durch Ihre eigene geeignete Zeit für die Parameter *RunStartedAfter* und *RunStartedBefore*. In diesem Tutorial verwenden Sie Folgendes: *-RunStartedAfter "2017/09/14"* und *-RunStartedBefore "2017/09/15"* .

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Hier ist die Beispielausgabe:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. Im Blobspeicher sehen Sie, dass eine weitere Datei erstellt wurde. In diesem Tutorial ist der neue Dateiname Name `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. Wenn Sie diese Datei öffnen, sehen Sie zwei Zeilen mit Datensätzen.

5. Überprüfen Sie den aktuellen Wert aus `watermarktable`. Sie sehen, dass der Grenzwert erneut aktualisiert wurde.

    ```sql
    Select * from watermarktable
    ```
    Beispielausgabe:

    TableName | Grenzwert
    --------- | ---------------
    „Data_source_table“ | 2017-09-07 09:01:00.000


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt:

> [!div class="checklist"]
> * Vorbereiten des Datenspeichers zum Speichern des Grenzwerts.
> * Erstellen einer Data Factory.
> * Erstellen Sie verknüpfte Dienste.
> * Erstellen des Quell-, Senken-, Grenzwertdatasets
> * Erstellen einer Pipeline.
> * Ausführen der Pipeline.
> * Überwachen der Pipelineausführung.

In diesem Tutorial hat die Pipeline Daten aus einer einzelnen Tabelle in Azure SQL-Datenbank in einen Blobspeicher kopiert. Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Daten aus mehreren Tabellen einer SQL Server-Datenbank in SQL-Datenbank kopieren.

> [!div class="nextstepaction"]
>[Inkrementelles Laden von mehreren SQL Server-Tabellen in Azure SQL-Datenbank](tutorial-incremental-copy-multiple-tables-powershell.md)
