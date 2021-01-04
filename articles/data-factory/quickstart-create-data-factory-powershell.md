---
title: Kopieren von Daten in Blob Storage mithilfe von Azure Data Factory
description: Erstellen Sie eine Azure Data Factory-Instanz mithilfe von PowerShell, um Daten von einem Speicherort in Azure Blob Storage an einen anderen Speicherort zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: quickstart
ms.date: 04/10/2020
ms.author: jingwang
ms.openlocfilehash: a7fcb4be47e0e1e62c190a9b089243a178df8e7a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013354"
---
# <a name="quickstart-create-an-azure-data-factory-using-powershell"></a>Schnellstart: Erstellen einer Azure Data Factory-Instanz mithilfe von PowerShell

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](quickstart-create-data-factory-powershell.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Diese Schnellstartanleitung beschreibt, wie Sie mithilfe von PowerShell eine Azure Data Factory-Instanz erstellen. Die in dieser Data Factory erstellte Pipeline **kopiert** Daten aus einem Ordner in einen anderen Ordner in Azure Blob Storage. Ein Tutorial zum **Transformieren** von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Daten mit Spark transformieren](transform-data-using-spark.md).

> [!NOTE]
> Dieser Artikel enthält keine ausführliche Einführung in den Data Factory-Dienst. Eine Einführung in den Azure Data Factory-Dienst finden Sie unter [Einführung in Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)]

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installieren Sie die aktuellen Azure PowerShell-Module, indem Sie die Anweisungen unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) befolgen.

#### <a name="log-in-to-powershell"></a>Anmelden an PowerShell

1. Starten Sie **PowerShell** auf Ihrem Computer. Lassen Sie PowerShell bis zum Ende dieser Schnellstartanleitung geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.

2. Führen Sie den folgenden Befehl aus, und geben Sie den gleichen Azure-Benutzernamen und das gleiche Kennwort ein, die Sie bei der Anmeldung beim Azure-Portal verwendet haben:

    ```powershell
    Connect-AzAccount
    ```

3. Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```

4. Sollten Ihrem Konto mehrere Azure-Abonnements zugeordnet sein, führen Sie den folgenden Befehl aus, um das zu verwendende Abonnement auszuwählen. Ersetzen Sie **SubscriptionId** durch die ID Ihres Azure-Abonnements:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Definieren Sie eine Variable für den Ressourcengruppennamen zur späteren Verwendung in PowerShell-Befehlen. Kopieren Sie den folgenden Befehlstext nach PowerShell, geben Sie einen Namen für die [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md) in doppelten Anführungszeichen an, und führen Sie dann den Befehl aus. Beispiel: `"ADFQuickStartRG"`.

     ```powershell
    $resourceGroupName = "ADFQuickStartRG";
    ```

    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$ResourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.

2. Führen Sie den folgenden Befehl aus, um die Azure-Ressourcengruppe zu erstellen:

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'East US'
    ```

    Beachten Sie, dass die Ressourcengruppe ggf. nicht überschrieben werden soll, falls sie bereits vorhanden ist. Weisen Sie der Variablen `$ResourceGroupName` einen anderen Wert zu, und führen Sie den Befehl erneut aus.

3. Definieren Sie eine Variable für den Namen der Data Factory. 

    > [!IMPORTANT]
    >  Aktualisieren Sie den Data Factory-Namen, damit er global eindeutig ist. Beispiel: ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFQuickStartFactory";
    ```

4. Führen Sie zum Erstellen der Data Factory das folgende Cmdlet vom Typ **Set-AzDataFactoryV2** aus. Verwenden Sie dabei den Standort und die Eigenschaft „ResourceGroupName“ aus der Variablen „$ResGrp“:

    ```powershell
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
        -Location $ResGrp.Location -Name $dataFactoryName
    ```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Damit Sie Data Factory-Instanzen erstellen können, muss das Benutzerkonto, mit dem Sie sich bei Azure anmelden, ein Mitglied der Rolle **Mitwirkender** oder **Besitzer** oder ein **Administrator** des Azure-Abonnements sein.

* Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.


## <a name="create-a-linked-service"></a>Erstellen eines verknüpften Diensts

Erstellen Sie verknüpfte Dienste in einer Data Factory, um Ihre Datenspeicher und Computedienste mit der Data Factory zu verknüpfen. In dieser Schnellstartanleitung erstellen Sie einen mit Azure Storage verknüpften Dienst, der sowohl als Quellspeicher als auch als Senkenspeicher verwendet wird. Der verknüpfte Dienste enthält die Verbindungsinformationen, die der Data Factory-Dienst zur Laufzeit zur Verbindungsherstellung verwendet.

>[!TIP]
>In dieser Schnellstartanleitung verwenden Sie *Kontoschlüssel* als Authentifizierungstyp für Ihren Datenspeicher, können bei Bedarf aber auch andere unterstützte Authentifizierungsmethoden (*SAS-URI*, *Dienstprinzipal* und *Verwaltete Identität*) auswählen. Ausführlichere Informationen finden Sie in den entsprechenden Abschnitten [dieses Artikels](./connector-azure-blob-storage.md#linked-service-properties).
>Zum sicheren Speichern von Geheimnissen für Datenspeicher empfiehlt sich darüber hinaus die Verwendung einer Azure Key Vault-Instanz. Eine ausführliche Erläuterung finden Sie in [diesem Artikel](./store-credentials-in-key-vault.md).

1. Erstellen Sie im Ordner **C:\ADFv2QuickStartPSH** eine JSON-Datei mit dem Namen **AzureStorageLinkedService.jso** und dem unten angegebenen Inhalt: (Erstellen Sie den Ordner „ADFv2QuickStartPSH“, falls er nicht bereits vorhanden ist.)

    > [!IMPORTANT]
    > Ersetzen Sie &lt;accountname&gt; und &lt;accountkey&gt; durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos, bevor Sie die Datei speichern.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
    ```

    Wählen Sie bei Verwendung des Editors im Dialogfeld **Speichern unter** für **Dateityp** die Option **Alle Dateien**. Andernfalls wird der Datei ggf. die Erweiterung `.txt` hinzugefügt. Beispiel: `AzureStorageLinkedService.json.txt`. Wenn Sie die Datei vor dem Öffnen im Editor im Datei-Explorer erstellen, wird die Erweiterung `.txt` ggf. nicht angezeigt, da die Option **Erweiterungen bei bekannten Dateitypen ausblenden** standardmäßig aktiviert ist. Entfernen Sie die Erweiterung `.txt`, bevor Sie mit dem nächsten Schritt fortfahren.

2. Wechseln Sie in **PowerShell** zum Ordner **ADFv2QuickStartPSH**.

    ```powershell
    Set-Location 'C:\ADFv2QuickStartPSH'
    ```

3. Führen Sie das Cmdlet **Set-AzDataFactoryV2LinkedService** aus, um den verknüpften Dienst zu erstellen: **AzureStorageLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureStorageLinkedService" `
        -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

## <a name="create-datasets"></a>Erstellen von Datasets

In diesem Verfahren erstellen Sie zwei Datasets: **InputDataset** und **OutputDataset**. Diese Datasets sind vom Typ **Binär**. Sie verweisen auf den mit Azure Storage verknüpften Dienst, den Sie im vorherigen Abschnitt erstellt haben.
Das Eingabedataset stellt die Quelldaten im Eingabeordner dar. In der Definition des Eingabedatasets geben Sie den Blobcontainer (**adftutorial**), den Ordner (**input**) und die Datei (**emp.txt**) mit den Quelldaten an.
Das Ausgabedataset stellt die Daten dar, die zum Ziel kopiert werden. In der Definition des Ausgabedatasets geben Sie den Blobcontainer (**adftutorial**), den Ordner (**output**) und die Datei an, in die die Daten kopiert werden. 
1. Erstellen Sie im Ordner **C:\ADFv2QuickStartPSH** eine JSON-Datei mit dem Namen **InputDataset.json** und dem folgenden Inhalt:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "fileName": "emp.txt",
                    "folderPath": "input",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

2. Führen Sie zum Erstellen des Datasets **InputDataset** das Cmdlet **Set-AzDataFactoryV2Dataset** aus.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "InputDataset" `
        -DefinitionFile ".\InputDataset.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    DatasetName       : InputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```

3. Wiederholen Sie die Schritte zum Erstellen des Ausgabedatasets. Erstellen Sie im Ordner **C:\ADFv2QuickStartPSH** eine JSON-Datei mit dem Namen **OutputDataset.json** und dem folgenden Inhalt:

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            },
            "annotations": [],
            "type": "Binary",
            "typeProperties": {
                "location": {
                    "type": "AzureBlobStorageLocation",
                    "folderPath": "output",
                    "container": "adftutorial"
                }
            }
        }
    }
    ```

4. Führen Sie das Cmdlet **Set-AzDataFactoryV2Dataset** aus, um **OutDataset** zu erstellen:

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName -Name "OutputDataset" `
        -DefinitionFile ".\OutputDataset.json"
    ```

    Hier ist die Beispielausgabe:

    ```console
    DatasetName       : OutputDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.BinaryDataset
    ```
## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

In diesem Schritt erstellen Sie eine Pipeline mit einer Copy-Aktivität, die das Eingabe- und Ausgabedataset verwendet. Die Copy-Aktivität kopiert Daten aus der in den Einstellungen des Eingabedatasets angegebenen Datei in die Datei, die in den Einstellungen des Ausgabedatasets angegeben ist.  

1. Erstellen Sie im Ordner **C:\ADFv2QuickStartPSH** eine JSON-Datei mit dem Namen **Adfv2QuickStartPipeline.json** und dem folgenden Inhalt:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
                    "dependsOn": [],
                    "policy": {
                        "timeout": "7.00:00:00",
                        "retry": 0,
                        "retryIntervalInSeconds": 30,
                        "secureOutput": false,
                        "secureInput": false
                    },
                    "userProperties": [],
                    "typeProperties": {
                        "source": {
                            "type": "BinarySource",
                            "storeSettings": {
                                "type": "AzureBlobStorageReadSettings",
                                "recursive": true
                            }
                        },
                        "sink": {
                            "type": "BinarySink",
                            "storeSettings": {
                                "type": "AzureBlobStorageWriteSettings"
                            }
                        },
                        "enableStaging": false
                    },
                    "inputs": [
                        {
                            "referenceName": "InputDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "OutputDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ],
            "annotations": []
        }
    }
    ```

2. Führen Sie zum Erstellen der Pipeline **Adfv2QuickStartPipeline** das Cmdlet **Set-AzDataFactoryV2Pipeline** aus.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline `
        -DataFactoryName $DataFactory.DataFactoryName `
        -ResourceGroupName $ResGrp.ResourceGroupName `
        -Name "Adfv2QuickStartPipeline" `
        -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

## <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung

In diesem Schritt erstellen Sie eine Pipelineausführung.

Führen Sie das Cmdlet **Invoke-AzDataFactoryV2Pipeline** aus, um eine Pipelineausführung zu erstellen. Das Cmdlet gibt die ID der Pipelineausführung für die zukünftige Überwachung zurück.

  ```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline `
    -DataFactoryName $DataFactory.DataFactoryName `
    -ResourceGroupName $ResGrp.ResourceGroupName `
    -PipelineName $DFPipeLine.Name 
```

## <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

1. Führen Sie das folgende PowerShell-Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist. Kopieren Sie das folgende Skript, fügen Sie es in das PowerShell-Fenster ein, und drücken Sie die EINGABETASTE.

    ```powershell
    while ($True) {
        $Run = Get-AzDataFactoryV2PipelineRun `
            -ResourceGroupName $ResGrp.ResourceGroupName `
            -DataFactoryName $DataFactory.DataFactoryName `
            -PipelineRunId $RunId

        if ($Run) {
            if ($run.Status -ne 'InProgress') {
                Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
                $Run
                break
            }
            Write-Output "Pipeline is running...status: InProgress"
        }

        Start-Sleep -Seconds 10
    }
    ```

    Hier ist die Beispielausgabe der Pipelineausführung:

    ```console
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    RunId             : 00000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 8/27/2019 7:23:07 AM
    Parameters        : {}
    RunStart          : 8/27/2019 7:22:56 AM
    RunEnd            : 8/27/2019 7:23:07 AM
    DurationInMs      : 11324
    Status            : Succeeded
    Message           : 
    ```
2. Führen Sie das folgende Skript aus, um Ausführungsdetails zur Kopieraktivität abzurufen, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```powershell
    Write-Output "Activity run details:"
    $Result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineRunId $RunId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $Result

    Write-Output "Activity 'Output' section:"
    $Result.Output -join "`r`n"

    Write-Output "Activity 'Error' section:"
    $Result.Error -join "`r`n"
    ```
3. Vergewissern Sie sich, dass die Ausgabe ähnlich aussieht wie die folgende Beispielausgabe des Ergebnisses einer Aktivitätsausführung:

    ```console
    ResourceGroupName : ADFQuickStartRG
    DataFactoryName   : ADFQuickStartFactory
    ActivityRunId     : 00000000-0000-0000-0000-000000000000
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink, enableStaging}
    Output            : {dataRead, dataWritten, filesRead, filesWritten...}
    LinkedServiceName :
    ActivityRunStart  : 8/27/2019 7:22:58 AM
    ActivityRunEnd    : 8/27/2019 7:23:05 AM
    DurationInMs      : 6828
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 20
    "dataWritten": 20
    "filesRead": 1
    "filesWritten": 1
    "sourcePeakConnections": 1
    "sinkPeakConnections": 1
    "copyDuration": 4
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (Central US)"
    "usedDataIntegrationUnits": 4
    "usedParallelCopies": 1
    "executionDetails": [
      {
        "source": {
          "type": "AzureBlobStorage"
        },
        "sink": {
          "type": "AzureBlobStorage"
        },
        "status": "Succeeded",
        "start": "2019-08-27T07:22:59.1045645Z",
        "duration": 4,
        "usedDataIntegrationUnits": 4,
        "usedParallelCopies": 1,
        "detailedDurations": {
          "queuingDuration": 3,
          "transferDuration": 1
        }
      }
    ]
    
    Activity 'Error' section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "CopyFromBlobToBlob"
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Arbeiten Sie die [Tutorials](tutorial-copy-data-dot-net.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können.
