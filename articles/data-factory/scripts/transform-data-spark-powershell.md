---
title: 'PowerShell-Skript: Transformieren von Daten in der Cloud mithilfe der Data Factory | Microsoft-Dokumentation'
description: Dieses PowerShell-Skript transformiert Daten in der Cloud durch Ausführen eines Spark-Programms in einem Azure HDInsight Spark-Cluster.
services: data-factory
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: bfec4ffa4d8a9f41b9c9c55ab0d84f4133bd2445
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160653"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell-Skript: Transformieren von Daten in der Cloud mithilfe von Azure Data Factory

Dieses PowerShell-Beispielskript erstellt eine Pipeline, die Daten in der Cloud durch Ausführen eines Spark-Programms in einem Azure HDInsight Spark-Cluster transformiert. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Azure Storage-Konto**. Erstellen Sie ein Python-Skript und eine Eingabedatei, und laden Sie diese in Azure Storage hoch. Die Ausgabe des Spark-Programms wird in diesem Storage-Konto gespeichert. Der bedarfsgesteuerte Spark-Cluster verwendet dieses Storage-Konto als primären Speicher.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Hochladen eines Python-Skripts in Ihr Blob Storage-Konto
1. Erstellen Sie eine Python-Datei mit dem Namen **WordCount_Spark.py** und dem folgenden Inhalt: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Ersetzen Sie **&lt;storageAccountName&gt;** durch den Namen Ihres Azure Storage-Kontos. Speichern Sie dann die Datei. 
3. Erstellen Sie in Azure Blob Storage einen Container mit dem Namen **adftutorial**, falls dieser noch nicht vorhanden ist. 
4. Erstellen Sie einen Ordner mit dem Namen **spark**.
5. Erstellen Sie unterhalb des Ordners **spark** einen Unterordner mit dem Namen **script**. 
6. Laden Sie die Datei **WordCount_Spark.py** in den Unterordner **script** hoch. 


### <a name="upload-the-input-file"></a>Hochladen der Eingabedatei
1. Erstellen Sie eine Datei mit dem Namen **minecraftstory.txt** und etwas Text darin. Das Spark-Programm zählt die Wörter in diesem Text. 
2. Erstellen Sie im Blobcontainer im Ordner `spark` einen Unterordner mit dem Namen `inputfiles`. 
3. Laden Sie `minecraftstory.txt` in den Unterordner `inputfiles` hoch. 

## <a name="sample-script"></a>Beispielskript
> [!IMPORTANT]
> Dieses Skript erstellt JSON-Dateien, die Data Factory-Entitäten (verknüpften Dienst, Dataset und Pipeline) auf der Festplatte im Ordner „c:\“ definieren.

```powershell
powershell Set-ExecutionPolicy Unrestricted -Scope CurrentUser

# Set variables with your own values
$resourceGroupName = "<Azure resource group name>"
$dataFactoryName = "<Data factory name. Must be globally unique.>"
$dataFactoryRegion = "China East" 
$storageAccountName = "<Az.Storage account name> "
$storageAccountKey = "<Az.Storage account key>"
$subscriptionID = "<Azure subscription ID>"
$tenantID = "<tenant ID>"
$servicePrincipalID = "<Active directory service principal ID>"
$servicePrincipalKey = "<Active directory service principal key>"

$pipelineName = "SparkTransformPipeline"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $dataFactoryRegion

# Create a data factory
$df = Set-AzDataFactory -ResourceGroupName $resourceGroupName -Location $dataFactoryRegion -Name $dataFactoryName

# Create an Az.Storage linked service in the data factory

## JSON definition of the linked service. 
$storageLinkedServiceDefinition = @"
{
    "name": "AzureStorageLinkedService",
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

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$storageLinkedServiceDefinition | Out-File c:\AzureStorageLinkedService.json

## Creates an Az.Storage linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File c:\AzureStorageLinkedService.json

# Create on-demand Spark linked service in the data factory

## JSON definition of the linked service. 
$sparkLinkedServiceDefinition = @"
{
    "name": "OnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "$subscriptionID",
        "servicePrincipalId": "$servicePrincipalID",
        "servicePrincipalKey": {
          "value": "$servicePrincipalKey",
          "type": "SecureString"
        },
        "tenant": "$tenantID",
        "clusterResourceGroup": "$resourceGroupName",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "AzureStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryLinkedService command. 
$sparkLinkedServiceDefinition | Out-File c:\OnDemandSparkLinkedService.json

# Creates an on-demand Spark linked service
Set-AzDataFactoryLinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "OnDemandSparkLinkedService" -File "C:\OnDemandSparkLinkedService.json"

# Create a pipeline in the data factory

## JSON definition of the pipeline
$pipelineDefinition = @"
{
  "name": "SparkTransformPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "OnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Set-AzDataFactoryPipeline command.
$pipelineDefinition | Out-File c:\SparkTransformPipeline.json

## Create a pipeline with Spark Activity in the data factory
Set-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SparkTransformPipeline" -File "c:\SparkTransformPipeline.json"

# Create a pipeline run 

## JSON definition for dummy pipeline parameters
$pipelineParameters = @"
{
    "dummy":  "b"
}
"@

## IMPORTANT: store the JSON definition in a file that will be used by the Invoke-AzDataFactoryPipeline command. 
$pipelineParameters | Out-File c:\PipelineParameters.json

# Create a pipeline run by using parameters
$runId = Invoke-AzDataFactoryPipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName -ParameterFile c:\PipelineParameters.json

# Check the pipeline run status until it finishes
Start-Sleep -Seconds 30
while ($True) {
    $result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        Start-Sleep -Seconds 300
    }
    else {
        Write-Host "Pipeline $pipelineName run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
}

# Get the activity run details 
$result = Get-AzDataFactoryActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName `
    -PipelineRunId $runId `
    -RunStartedAfter (Get-Date).AddMinutes(-30) `
    -RunStartedBefore (Get-Date).AddMinutes(30) `
    -ErrorAction Stop

$result

if ($result.Status -eq "Succeeded") {`
    $result.Output -join "`r`n"`
}`
else {`
    $result.Error -join "`r`n"`
}

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Erstellt einen verknüpften Dienst in der Data Factory. Ein verknüpfter Dienst verbindet einen Datenspeicher oder ein Compute mit einer Data Factory. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Erstellt eine Pipeline in der Data Factory. Eine Pipeline enthält eine oder mehrere Aktivitäten zur Ausführung eines bestimmten Vorgangs. In dieser Pipeline transformiert eine Spark-Aktivität Daten durch Ausführen eines Programms in einem Azure HDInsight Spark-Cluster. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Erstellt eine Ausführung für die Pipeline. Soll heißen, führt die Pipeline aus. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ruft Details zur Ausführung der Aktivität (Aktivitätsausführung) in der Pipeline ab. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).

Zusätzliche PowerShell-Skriptbeispiele für Azure Data Factory finden Sie unter [Azure PowerShell-Beispiele für Azure Data Factory](../samples-powershell.md).
