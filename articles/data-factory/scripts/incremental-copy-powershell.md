---
title: 'PowerShell-Skript: Inkrementelles Laden von Daten mithilfe von Azure Data Factory | Microsoft-Dokumentation'
description: Dieses PowerShell-Skript veranschaulicht, wie Sie Azure Data Factory zum inkrementellen Kopieren von Daten aus einer Azure SQL-Datenbank in eine Azure Blob Storage-Instanz verwenden.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5ae6d6ed06aa5734dc601e6e72ba55ec8ddf7bcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160639"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell-Skript: Inkrementelles Laden von Daten mithilfe von Azure Data Factory
Dieses PowerShell-Beispielskript lädt nach der vollständigen Erstkopie von Daten aus der Quelle in die Senke nur neue oder aktualisierte Datensätze aus einem Quelldatenspeicher in einen Senkendatenspeicher.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Die Voraussetzungen zum Ausführen dieses Beispiels finden Sie unter [Inkrementelles Laden von Daten aus Azure SQL-Datenbank in Azure Blob Storage](../tutorial-incremental-copy-powershell.md#prerequisites). 

## <a name="sample-script"></a>Beispielskript

> [!IMPORTANT]
> Dieses Skript erstellt JSON-Dateien, die Data Factory-Entitäten (verknüpften Dienst, Dataset und Pipeline) auf der Festplatte im Ordner „c:\“ definieren.

```powershell
# Set variables with your own values
$resourceGroupName = "<azure resource group name>" # group will be created if it does not exist already
$dataFactoryName = "<data factory name>" # must be globally unique
$dataFactoryRegion = "East US" 
$storageAccountName = "<Az.Storage account name>"
$storageAccountKey = "<Az.Storage account key>"
$azureSqlServer = "<azure sql server name>"
$azureSqlDatabase = "<azure sql database name>"
$azureSqlUser = "<azure sql server - user name>"
$azureSqlPassword = "<azure sql server - user password>"
$outputBlobFolderPath = "<azure blob container>/<folder>" # output folder where the records are copied. 

$azureStorageLinkedServiceName = "AzureStorageLinkedService"
$azureSqlDatabaseLinkedServiceName = "AzureSQLDatabaseLinkedService"
$sourceDatasetName = "SourceDataset"
$sinkDatasetName = "SinkDataset"
$sourceSqlTableName = "datasource"
$watermarkDatasetName = "WatermarkDataset"
$pipelineName = "IncrementalCopyPipeline"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion


# Create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryRegion -Name $dataFactoryName 

# Create an Az.Storage linked service in the data factory

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "$azureStorageLinkedServiceName",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=$storageAccountName;AccountKey=$storageAccountKey",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\$azureStorageLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureStorageLinkedServiceName" -File c:\$azureStorageLinkedServiceName.json

## JSON definition of the linked service. 
$azureSQLDatabaseLinkedServiceDefinition = @"
{
    "name": "$azureSqlDatabaseLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "value": "Server = tcp:$azureSqlServer.database.windows.net,1433;Initial Catalog=$azureSqlDatabase; Persist Security Info=False; User ID=$azureSqlUser; Password=$azureSqlPassword; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                "type": "SecureString"
            }
        }
    }
}
"@

## IMPORTANT: stores the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$azureSQLDatabaseLinkedServiceDefinition | Out-File c:\$azureSqlDatabaseLinkedServiceName.json

## Creates a linked service in the data factory
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$azureSqlDatabaseLinkedServiceName" -File c:\$azureSqlDatabaseLinkedServiceName.json

# Create an Azure SQL dataset in the data factory

## JSON definition of the dataset
$sourceDataset = @"
{
    "name": "$sourceDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "$sourceSqlTableName"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sourceDataset | Out-File c:\$sourceDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sourceDatasetName" -File "c:\$sourceDatasetName.json"


# Create an Azure Blob dataset in the data factory

## JSON definition of the dataset
$sinkDataset = @"
{
    "name": "$sinkDatasetName",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "$outputBlobFolderPath",
            "format": {
                "type": "TextFormat"
            }
        },
        "linkedServiceName": {
            "referenceName": "$azureStorageLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$sinkDataset | Out-File c:\$sinkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$sinkDatasetName" -File "c:\$sinkDatasetName.json"

## JSON definition of the dataset
$watermarkDataset = @"
{
    "name": "$watermarkDatasetName",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties": {
            "tableName": "watermarktable"
        },
        "linkedServiceName": {
            "referenceName": "$azureSqlDatabaseLinkedServiceName",
            "type": "LinkedServiceReference"
        }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryDataset command. 
$watermarkDataset | Out-File c:\$watermarkDatasetName.json

## Create a dataset in the data factory
Set-AzDataFactoryDataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$watermarkDatasetName" -File "c:\$watermarkDatasetName.json"

# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
    "name": "$pipelineName",
    "properties": {
        "activities": [
            {
                "name": "LookupWaterMarkActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                    "type": "SqlSource",
                    "sqlReaderQuery": "select * from watermarktable"
                    },

                    "dataset": {
                    "referenceName": "$watermarkDatasetName",
                    "type": "DatasetReference"
                    }
                }
            },
            {
                "name": "LookupMaxValuefromSourceActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "SELECT MAX(LastModifytime) as NewWatermarkvalue FROM dbo.datasource"
                    },

                    "dataset": {
                    "referenceName": "$sourceDatasetName",
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
                        "sqlReaderQuery": "select * from dbo.datasource where LastModifytime > '@{activity('LookupWaterMarkActivity').output.WatermarkValue}' and LastModifytime <= '@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}'"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "dependsOn": [
                    {
                        "activity": "LookupMaxValuefromSourceActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    },
                    {
                        "activity": "LookupWaterMarkActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],

                "inputs": [
                    {
                        "referenceName": "$sourceDatasetName",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "$sinkDatasetName",
                        "type": "DatasetReference"
                    }
                ]
            },

            {
                "name": "StoredProceduretoWriteWatermarkActivity",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {

                    "storedProcedureName": "sp_write_watermark",
                    "storedProcedureParameters": {
                        "LastModifiedtime": {"value": "@{activity('LookupMaxValuefromSourceActivity').output.NewWatermarkvalue}", "type": "datetime" },
                        "TableName":  { "value":"@{activity('LookupWaterMarkActivity').output.TableName}", "type":"String"}
                    }
                },

                "linkedServiceName": {
                    "referenceName": "$azureSqlDatabaseLinkedServiceName",
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
        ],

    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command. 
$pipelineDefinition | Out-File c:\$pipelineName.json

## Create a pipeline in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "$pipelineName" -File "c:\$pipelineName.json"


$RunId = Invoke-AzDataFactoryPipeline -PipelineName "$pipelineName" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName

# Check the pipeline run status until it finishes the copy operation
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 30
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}


$result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
    -PipelineRunId $runId `
    -RunStartedAfter (Get-Date).AddMinutes(-10) `
    -RunStartedBefore (Get-Date).AddMinutes(10) `
    -ErrorAction Stop

$result

if ($result.Status -eq "Succeeded") {`
    $result.Output -join "`r`n"`
}`
else {`
    $result.Error -join "`r`n"`
}

# INSERT INTO datasource
# VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

# INSERT INTO datasource
# VALUES (7, 'newdata','9/7/2017 9:01:00 AM')

# Inovke the pipeline and see that only new records are copied to the destination. 


# To remove the data factory from the resource gorup
# Remove-AzDataFactory -Name $dataFactoryName -ResourceGroupName $resourceGroupName
# 
# To remove the whole resource group
# Remove-AzResourceGroup  -Name $resourceGroupName
```

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können Sie den folgenden Befehl ausführen, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Führen Sie den folgenden Befehl aus, um die Data Factory aus der Ressourcengruppe zu entfernen: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle: 

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Erstellen einer Data Factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Erstellt einen verknüpften Dienst in der Data Factory. Ein verknüpfter Dienst verbindet einen Datenspeicher oder ein Compute mit einer Data Factory. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Erstellt ein Dataset in der Data Factory. Ein Dataset stellt die Eingabe/Ausgabe für eine Aktivität in einer Pipeline dar. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Erstellt eine Pipeline in der Data Factory. Eine Pipeline enthält eine oder mehrere Aktivitäten zur Ausführung eines bestimmten Vorgangs. In dieser Pipeline kopiert eine Kopieraktivität in einem Azure Blob Storage Daten von einem Speicherort an einen anderen. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Erstellt eine Ausführung für die Pipeline. Soll heißen, führt die Pipeline aus. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ruft Details zur Ausführung der Aktivität (Aktivitätsausführung) in der Pipeline ab. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).

Zusätzliche PowerShell-Skriptbeispiele für Azure Data Factory finden Sie unter [PowerShell-Beispiele für Azure Data Factory](../samples-powershell.md).
