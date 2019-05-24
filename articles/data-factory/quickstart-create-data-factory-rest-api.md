---
title: Erstellen einer Azure Data Factory mithilfe der REST-API | Microsoft-Dokumentation
description: Erstellen Sie eine Azure Data Factory-Instanz, um Daten von einem Speicherort in einem Azure Blob-Speicher an einen anderen Speicherort zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: rest-api
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: d5255e8cd8c662295a714931a3e292b20ab10079
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156935"
---
# <a name="quickstart-create-an-azure-data-factory-and-pipeline-by-using-the-rest-api"></a>Schnellstart: Erstellen einer Azure Data Factory und einer Pipeline mithilfe der REST-API

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](quickstart-create-data-factory-rest-api.md)

Azure Data Factory ist ein cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows in der Cloud erstellen können, um Datenverschiebungen und Datentransformationen zu orchestrieren und zu automatisieren. Mit Azure Data Factory können Sie datengesteuerte Workflows (sogenannte Pipelines) erstellen und planen, die Daten aus unterschiedlichen Datenspeichern erfassen, diese Daten mithilfe von Compute Services wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics und Azure Machine Learning verarbeiten/transformieren und die Ausgabedaten für Datenspeicher wie Azure SQL Data Warehouse veröffentlichen, damit diese von Business Intelligence (BI)-Anwendungen genutzt werden können.

Diese Schnellstartanleitung beschreibt, wie Sie die REST-API verwenden, um eine Azure Data Factory zu erstellen. Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure-Abonnement**. Falls Sie nicht über ein Abonnement verfügen, können Sie ein [kostenloses Testkonto](https://azure.microsoft.com/pricing/free-trial/) erstellen.
* **Azure Storage-Konto**. Sie verwenden den Blob Storage als **Quelldatenspeicher** und als **Senkendatenspeicher**. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-quickstart-create-account.md) Schritte zum Erstellen eines solchen Kontos.
* Erstellen Sie einen **Blobcontainer** in Blob Storage, erstellen Sie einen **Eingabeordner** im Container, und laden Sie einige Dateien in den Ordner. Sie können Tools wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwenden, um eine Verbindung mit Azure Blob Storage herzustellen, einen Blobcontainer zu erstellen, eine Eingabedatei hochzuladen und die Ausgabedatei zu überprüfen.
* Installieren Sie **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets). Diese Schnellstartanleitung verwendet PowerShell, um REST-API-Aufrufe aufzurufen.
* **Erstellen Sie eine Anwendung in Azure Active Directory**, indem Sie [diese Anweisungen](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) befolgen. Notieren Sie sich die folgenden Werte, die Sie in späteren Schritten benötigen: **Anwendungs-ID**, **Authentifizierungsschlüssel** und **Mandanten-ID**. Weisen Sie die Anwendung der Rolle **Mitwirkender** zu.

## <a name="set-global-variables"></a>Festlegen von globalen Variablen

1. Starten Sie **PowerShell**. Lassen Sie Azure PowerShell bis zum Ende dieser Schnellstartanleitung geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.

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
2. Nachdem Sie die Platzhalter durch Ihre eigenen Werte ersetzt haben, führen Sie die folgenden Befehle aus, um globale Variablen festzulegen, die in späteren Schritten verwendet werden.

    ```powershell
    $tenantID = "<your tenant ID>"
    $appId = "<your application ID>"
    $authKey = "<your authentication key for the application>"
    $subsId = "<your subscription ID to create the factory>"
    $resourceGroup = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $apiVersion = "2018-06-01"
    ```

## <a name="authenticate-with-azure-ad"></a>Authentifizierung über Azure AD

Führen Sie die folgenden Befehle aus, um die Authentifizierung bei Azure Active Directory (AAD) durchzuführen:

```powershell
$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]"https://login.microsoftonline.com/${tenantId}"
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($appId, $authKey)
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Führen Sie die folgenden Befehle aus, um eine Data Factory zu erstellen:

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}?api-version=${apiVersion}"
$body = @"
{
    "name": "$dataFactoryName",
    "location": "East US",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Wenn die folgende Fehlermeldung angezeigt wird, ändern Sie den Namen, und wiederholen Sie den Vorgang.

    ```
    Data factory name "ADFv2QuickStartDataFactory" is not available.
    ```
* Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

Hier ist die Beispielantwort:

```json
{
    "name": "<dataFactoryName>",
    "tags": {
    },
    "properties":  {
        "provisioningState":  "Succeeded",
        "loggingStorageAccountKey":  "**********",
        "createTime":  "2017-09-14T06:22:59.9106216Z",
        "version":  "2018-06-01"
    },
    "identity":  {
        "type":  "SystemAssigned",
        "principalId":  "<service principal ID>",
        "tenantId":  "<tenant ID>"
    },
    "id":  "dataFactoryName",
    "type":  "Microsoft.DataFactory/factories",
    "location":  "East US"
}
```

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In dieser Schnellstartanleitung müssen Sie nur einen verknüpften Azure Storage-Dienst für die Quell- und Senkendatenspeicher für den Kopiervorgang erstellen – in diesem Beispiel als „AzureStorageLinkedService“ bezeichnet.

Führen Sie die folgenden Befehle aus, um einen verknüpften Dienst mit dem Namen **AzureStorageLinkedService** zu erstellen:

Ersetzen Sie &lt;accountName&gt; und &lt;accountKey&gt; durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos, bevor Sie die Befehle ausführen.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/linkedservices/AzureStorageLinkedService?api-version=${apiVersion}"
$body = @"
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                "type": "SecureString"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier ist die Beispielausgabe:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/linkedservices/AzureStorageLinkedService",
    "name":  "AzureStorageLinkedService",
    "properties":  {
        "type":  "AzureStorage",
        "typeProperties":  {
            "connectionString":  "@{value=**********; type=SecureString}"
        }
    },
    "etag":  "0000c552-0000-0000-0000-59b1459c0000"
}
```

## <a name="create-datasets"></a>Erstellen von Datasets

Sie definieren ein Dataset, das die Daten repräsentiert, die aus einer Quelle in eine Senke kopiert werden sollen. In diesem Beispiel verweist dieses Blobdataset auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt erstellt haben. Das Dataset akzeptiert einen Parameter, dessen Wert in einer Aktivität festgelegt ist, die das Dataset verwendet. Der Parameter wird verwendet, um den „folderPath“ zu erstellen, der auf den Speicherort der Dateien zeigt.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/datasets/BlobDataset?api-version=${apiVersion}"
$body = @"
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier ist die Beispielausgabe:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/datasets/BlobDataset",
    "name":  "BlobDataset",
    "properties":  {
        "type":  "AzureBlob",
        "typeProperties":  {
            "folderPath":  "@{value=@{dataset().path}; type=Expression}"
        },
        "linkedServiceName":  {
            "referenceName":  "AzureStorageLinkedService",
            "type":  "LinkedServiceReference"
        },
        "parameters":  {
            "path":  "@{type=String}"
        }
    },
    "etag":  "0000c752-0000-0000-0000-59b1459d0000"
}
```

## <a name="create-pipeline"></a>Erstellen der Pipeline

In diesem Beispiel enthält die Pipeline eine Aktivität und akzeptiert zwei Parameter: Eingabeblobpfad und Ausgabeblobpfad. Die Werte für diese Parameter werden festgelegt, wenn die Pipeline ausgelöst bzw. ausgeführt wird. Die Kopieraktivität verweist für die Eingabe und Ausgabe auf das gleiche Blobdataset, das im vorherigen Schritt erstellt wurde. Wenn das Dataset als Eingabedataset verwendet wird, wird der Eingabepfad angegeben. Wenn das Dataset als Ausgabedataset verwendet wird, wird der Ausgabepfad angegeben.

```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline?api-version=${apiVersion}"
$body = @"
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                    "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
"@
$response = Invoke-RestMethod -Method PUT -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
```

Hier ist die Beispielausgabe:

```json
{
    "id":  "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/pipelines/Adfv2QuickStartPipeline",
    "name":  "Adfv2QuickStartPipeline",
    "properties":  {
        "activities":  [
            "@{name=CopyFromBlobToBlob; type=Copy; inputs=System.Object[]; outputs=System.Object[]; typeProperties=}"
        ],
        "parameters":  {
            "inputPath":  "@{type=String}",
            "outputPath":  "@{type=String}"
        }
    },
    "etag":  "0000c852-0000-0000-0000-59b1459e0000"
}
```

## <a name="create-pipeline-run"></a>Erstellen einer Pipelineausführung

In diesem Schritt legen Sie die in der Pipeline angegebenen Parameter **inputPath** und **outputPath** auf die tatsächlichen Werte der Quell- und Senkenblobpfade fest, und lösen eine Pipelineausführung aus. Die im Antworttext zurückgegebene ID der Pipelineausführung wird später in der Überwachungs-API verwendet.

Ersetzen Sie die Werte für **inputPath** und **outputPath** durch den Pfad Ihres Quell- bzw. Senkenblobs, um Daten aus der Quelle in die Senke zu kopieren. Speichern Sie dann die Datei.


```powershell
$request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelines/Adfv2QuickStartPipeline/createRun?api-version=${apiVersion}"
$body = @"
{
    "inputPath": "<the path to existing blob(s) to copy data from, e.g. containername/path>",
    "outputPath": "<the blob path to copy data to, e.g. containername/path>"
}
"@
$response = Invoke-RestMethod -Method POST -Uri $request -Header $authHeader -Body $body
$response | ConvertTo-Json
$runId = $response.runId
```

Hier ist die Beispielausgabe:

```json
{
    "runId":  "2f26be35-c112-43fa-9eaa-8ba93ea57881"
}
```

## <a name="monitor-pipeline"></a>Überwachen der Pipeline

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

    Hier ist die Beispielausgabe:

    ```json
    {
        "key":  "000000000-0000-0000-0000-00000000000",
        "timestamp":  "2017-09-07T13:12:39.5561795Z",
        "runId":  "000000000-0000-0000-0000-000000000000",
        "dataFactoryName":  "<dataFactoryName>",
        "pipelineName":  "Adfv2QuickStartPipeline",
        "parameters":  [
            "inputPath: <inputBlobPath>",
            "outputPath: <outputBlobPath>"
        ],
        "parametersCount":  2,
        "parameterNames":  [
            "inputPath",
            "outputPath"
        ],
        "parameterNamesCount":  2,
        "parameterValues":  [
            "<inputBlobPath>",
            "<outputBlobPath>"
        ],
        "parameterValuesCount":  2,
        "runStart":  "2017-09-07T13:12:00.3710792Z",
        "runEnd":  "2017-09-07T13:12:39.5561795Z",
        "durationInMs":  39185,
        "status":  "Succeeded",
        "message":  ""
    }
    ```

2. Führen Sie das folgende Skript aus, um Ausführungsdetails zur Kopieraktivität abzurufen, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

    Hier ist die Beispielausgabe:

    ```json
    {
        "value":  [
            {
                "id":  "000000000-0000-0000-0000-00000000000",
                "timestamp":  "2017-09-07T13:12:38.4780542Z",
                "pipelineRunId":  "000000000-0000-00000-0000-0000000000000",
                "pipelineName":  "Adfv2QuickStartPipeline",
                "status":  "Succeeded",
                "failureType":  "",
                "linkedServiceName":  "",
                "activityName":  "CopyFromBlobToBlob",
                "activityType":  "Copy",
                "activityStart":  "2017-09-07T13:12:02.3299261Z",
                "activityEnd":  "2017-09-07T13:12:38.4780542Z",
                "duration":  36148,
                "input":  "@{source=; sink=}",
                "output":  "@{dataRead=331452208; dataWritten=331452208; copyDuration=22; throughput=14712.9; errors=System.Object[]; effectiveIntegrationRuntime=DefaultIntegrationRuntime (West US); usedDataIntegrationUnits=2; billedDuration=22}",
                "error":  "@{errorCode=; message=; failureType=; target=CopyFromBlobToBlob}"
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Überprüfen der Ausgabe

Verwenden Sie dann Tools wie z.B. Azure Storage-Explorer, um zu überprüfen, ob die Blobs wie von Ihnen beim Erstellen der Pipelineausführung angegeben von „inputBlobPath“ nach „outputBlobPath“ kopiert werden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Die im Rahmen dieser Schnellstartanleitung erstellten Ressourcen können auf zwei Arten bereinigt werden. Sie können die [Azure-Ressourcengruppe](../azure-resource-manager/resource-group-overview.md) einschließlich aller darin enthaltenen Ressourcen löschen. Falls die anderen Ressourcen erhalten bleiben sollen, löschen Sie nur die Data Factory, die Sie in diesem Tutorial erstellt haben.

Führen Sie den folgenden Befehl aus, um die gesamte Ressourcengruppe zu löschen:
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Führen Sie den folgenden Befehl aus, um nur die Data Factory zu löschen:

```powershell
Remove-AzDataFactoryV2 -Name "<NameOfYourDataFactory>" -ResourceGroupName "<NameOfResourceGroup>"
```

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Arbeiten Sie die [Tutorials](tutorial-copy-data-dot-net.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können.
