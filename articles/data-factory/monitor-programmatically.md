---
title: Programmgesteuertes Überwachen einer Azure Data Factory
description: Erfahren Sie, wie Sie mithilfe verschiedener Software Development Kits (SDKs) eine Pipeline in einer Data Factory überwachen können.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: dcstwh
ms.author: weetok
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: e7b435080fda48e1df0bb31d16dafed30ac3d3db
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497859"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programmgesteuertes Überwachen einer Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird beschrieben, wie Sie mithilfe verschiedener Software Development Kits (SDKs) eine Pipeline in einer Data Factory überwachen können. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Datenbereich

Data Factory speichert nur Pipelineausführungsdaten 45 Tage lang. Wenn Sie Daten zu Data Factory-Pipelineausführungen programmgesteuert abfragen (z.B. mit dem PowerShell-Befehl `Get-AzDataFactoryV2PipelineRun`), gibt es keine maximalen Datumsangaben für die optionalen Parameter `LastUpdatedAfter` und `LastUpdatedBefore`. Wenn Sie jedoch beispielsweise Daten zum vergangenen Jahr abfragen, gibt die Abfrage keinen Fehler zurück, sondern nur die Pipelineausführungsdaten der letzten 45 Tage.

Wenn Sie Pipelineausführungsdaten länger als 45 Tage beibehalten möchten, richten Sie mit [Azure Monitor](monitor-using-azure-monitor.md) eine eigene Diagnoseprotokollierung ein.

## <a name="net"></a>.NET
Eine vollständige exemplarische Vorgehensweise zum Erstellen und Überwachen einer Pipeline mit dem .NET SDK finden Sie unter [Erstellen einer Data Factory und Pipeline mit dem .NET SDK](quickstart-create-data-factory-dot-net.md).

1. Fügen Sie den folgenden Code hinzu, um kontinuierlich den Status der Pipelineausführung zu überwachen, bis die Pipeline das Kopieren der Daten beendet hat.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Fügen Sie den folgenden Code hinzu, der Ausführungsdetails zur Kopieraktivität abruft, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Eine vollständige Dokumentation zum .NET SDK finden Sie in der [.NET SDK-Referenz für Data Factory](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Eine vollständige exemplarische Vorgehensweise zum Erstellen und Überwachen einer Pipeline mit dem Python-SDK finden Sie unter [Erstellen einer Data Factory und Pipeline mithilfe von Python](quickstart-create-data-factory-python.md).

Fügen Sie den folgenden Code hinzu, um die Pipelineausführung zu überwachen:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Eine vollständige Dokumentation zum Python-SDK finden Sie in der [Python-SDK-Referenz für Data Factory](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST-API
Eine vollständige exemplarische Vorgehensweise zum Erstellen und Überwachen einer Pipeline mit der REST-API finden Sie unter [Erstellen einer Azure Data Factory und einer Pipeline mithilfe der REST-API](quickstart-create-data-factory-rest-api.md).
 
1. Führen Sie das folgende Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Führen Sie das folgende Skript aus, um Ausführungsdetails zur Kopieraktivität abzurufen, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

In der [REST-API-Referenz für Data Factory](/rest/api/datafactory/) finden Sie eine vollständige Dokumentation zur REST-API.

## <a name="powershell"></a>PowerShell
Eine vollständige exemplarische Vorgehensweise zum Erstellen und Überwachen einer Pipeline mit PowerShell finden Sie unter [Erstellen einer Data Factory und Pipeline mithilfe von PowerShell](quickstart-create-data-factory-powershell.md).

1. Führen Sie das folgende Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Führen Sie das folgende Skript aus, um Ausführungsdetails zur Kopieraktivität abzurufen, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Die vollständige Dokumentation zu PowerShell-Cmdlets finden Sie in der [PowerShell-Cmdlet-Referenz für Data Factory](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Nächste Schritte
Im Artikel [Überwachen von Pipelines mit Azure Monitor](monitor-using-azure-monitor.md) finden Sie Informationen zum Überwachen von Data Factory-Pipelines mit Azure Monitor. 

