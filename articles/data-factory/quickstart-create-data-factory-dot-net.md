---
title: Erstellen einer Azure Data Factory-Instanz mithilfe des .NET SDK
description: Erstellen Sie eine Azure Data Factory-Instanz und Pipeline mithilfe des .NET SDK, um Daten von einem Speicherort in Azure Blob Storage an einen anderen Speicherort zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/24/2019
ms.author: jingwang
ms.openlocfilehash: d564b96807574dd7a275d6959aea085ad16e9e2e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013410"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Schnellstart: Erstellen einer Data Factory und Pipeline mit dem .NET SDK

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuelle Version](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Diese Schnellstartanleitung beschreibt, wie Sie mithilfe des .NET SDK eine Azure Data Factory-Instanz erstellen. Die in dieser Data Factory erstellte Pipeline **kopiert** Daten aus einem Ordner in einen anderen Ordner in Azure Blob Storage. Ein Tutorial zum **Transformieren** von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Daten mit Spark transformieren](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Dieser Artikel enthält keine ausführliche Einführung in den Data Factory-Dienst. Eine Einführung in den Azure Data Factory-Dienst finden Sie unter [Einführung in Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

In der exemplarischen Vorgehensweise in diesem Artikel wird Visual Studio 2019 verwendet. Die Verfahren für Visual Studio 2013, 2015 oder 2017 unterscheiden sich geringfügig.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Laden Sie das [Azure .NET SDK](https://azure.microsoft.com/downloads/) auf Ihren Computer herunter, und installieren Sie es.

## <a name="create-an-application-in-azure-active-directory"></a>Erstellen einer Anwendung in Azure Active Directory

Befolgen Sie aus den Abschnitten in *Vorgehensweise: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal* die Anweisungen zum Ausführen dieser Aufgaben:

1. Erstellen Sie unter [Erstellen einer Azure Active Directory-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) eine Anwendung, die die .NET-Anwendung darstellt, die Sie in diesem Tutorial erstellen. Als Anmelde-URL können Sie wie in diesem Artikel gezeigt eine Platzhalter-URL (`https://contoso.org/exampleapp`) angeben.
2. Rufen Sie unter [Abrufen von Werten für die Anmeldung](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) die **Anwendungs-ID** und die **Mandanten-ID** ab, und notieren Sie sich diese Werte zur späteren Verwendung in diesem Tutorial. 
3. Rufen Sie unter [Zertifikate und Geheimnisse](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) den **Authentifizierungsschlüssel** ab, und notieren Sie sich diesen Wert zur späteren Verwendung in diesem Tutorial.
4. Weisen Sie unter [Zuweisen der Anwendung zu einer Rolle](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) die Anwendung der Rolle **Mitwirkender** auf Abonnementebene zu, damit die Anwendung Data Factorys im Abonnement erstellen kann.

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

Erstellen Sie im nächsten Schritt in Visual Studio eine C# .NET-Konsolenanwendung:

1. Starten Sie **Visual Studio**.
2. Wählen Sie im Fenster „Start“ die Option **Neues Projekt erstellen** > **Konsolen-App (.NET Framework)** aus. .NET-Version 4.5.2 oder höher ist erforderlich.
3. Geben Sie unter **Projektname** den Namen **ADFv2QuickStart** ein.
4. Wählen Sie **Create** (Erstellen), um das Projekt zu erstellen.

## <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

1. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
2. Führen Sie im Bereich **Paket-Manager-Konsole** die folgenden Befehle zum Installieren von Paketen aus. Weitere Informationen finden Sie unter [Microsoft.Azure.Management.DataFactory-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Erstellen eines Data Factory-Clients

1. Öffnen Sie **Program.cs**, und fügen Sie die folgenden Anweisungen ein, um Verweise auf Namespaces hinzuzufügen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der die Variablen festlegt. Ersetzen Sie die Platzhalter durch Ihre eigenen Werte. Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight und andere) können sich in anderen Regionen befinden.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine Instanz der **DataFactoryManagementClient**-Klasse erstellt. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Datasets und eine Pipeline zu erstellen. Sie verwenden dieses Objekt ebenfalls zum Überwachen der Ausführungsdetails der Pipeline.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine **Data Factory** erstellt. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Erstellen eines verknüpften Diensts

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der einen **verknüpften Azure Storage-Dienst** erstellt.

Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In dieser Schnellstartanleitung müssen Sie nur einen verknüpften Azure Storage-Dienst für die Quell- und Senkendatenspeicher für den Kopiervorgang erstellen. In diesem Beispiel wird er als „AzureStorageLinkedService“ bezeichnet.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Erstellen eines Datasets

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der ein **Azure blob dataset (Azure-Blobdataset)** erstellt.

Sie definieren ein Dataset, das die Daten repräsentiert, die aus einer Quelle in eine Senke kopiert werden sollen. In diesem Beispiel verweist dieses Blobdataset auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt erstellt haben. Das Dataset akzeptiert einen Parameter, dessen Wert in einer Aktivität festgelegt ist, die das Dataset verwendet. Der Parameter wird verwendet, um den „folderPath“ zu erstellen, der auf den Speicherort der Dateien zeigt.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine **Pipeline mit einer Kopieraktivität** erstellt.

In diesem Beispiel enthält die Pipeline eine Aktivität und nimmt zwei Parameter an: den Eingabeblobpfad und den Ausgabeblobpfad. Die Werte für diese Parameter werden festgelegt, wenn die Pipeline ausgelöst bzw. ausgeführt wird. Die Kopieraktivität verweist für die Eingabe und Ausgabe auf das gleiche Blobdataset, das im vorherigen Schritt erstellt wurde. Wenn das Dataset als Eingabedataset verwendet wird, wird der Eingabepfad angegeben. Wenn das Dataset als Ausgabedataset verwendet wird, wird der Ausgabepfad angegeben. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der **eine Pipelineausführung auslöst**.

Dieser Code legt auch die in der Pipeline angegeben Parameter **inputPath** und **outputPath** auf die tatsächlichen Werte der Quell- und Senkenblobpfade fest.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Überwachen einer Pipelineausführung

1. Fügen Sie der **Main**-Methode den folgenden Code hinzu, um den Status kontinuierlich zu überwachen, bis die Pipeline das Kopieren der Daten beendet hat.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der Ausführungsdetails zur Kopieraktivität abruft, z.B. die Größe der Daten, die gelesen oder geschrieben werden.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>Ausführen des Codes

Erstellen und starten Sie die Anwendung, und überprüfen Sie dann die Pipelineausführung.

Die Konsole druckt den Status der Erstellung der Data Factory, des verknüpften Diensts, der Datasets, der Pipeline und der Pipelineausführung aus. Danach wird der Status der Pipelineausführung überprüft. Warten Sie, bis Sie die Ausführungsdetails der Kopieraktivität mit der Größe der gelesen/geschriebenen Daten sehen. Überprüfen Sie anschließend mit Tools wie z. B. [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), ob die Blobs – wie von Ihnen in den Variablen angegeben – aus „inputBlobPath“ in „outputBlobPath“ kopiert werden.

### <a name="sample-output"></a>Beispielausgabe

```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
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
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
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
        "name": "CopyFromBlobToBlob"
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
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Überprüfen der Ausgabe

Die Pipeline erstellt den Ausgabeordner automatisch im Blobcontainer **adftutorial**. Anschließend wird die Datei **emp.txt** aus dem Eingabe- in den Ausgabeordner kopiert. 

1. Wählen Sie im Azure-Portal auf der Containerseite **adftutorial**, bei der Sie im Abschnitt [Hinzufügen eines Eingabeordners und einer Datei für den Blobcontainer](#add-an-input-folder-and-file-for-the-blob-container) Ihre Arbeit unterbrochen haben, **Aktualisieren** aus, um den Ausgabeordner anzuzeigen. 
2. Wählen Sie in der Ordnerliste **output** aus.
3. Überprüfen Sie, ob die Datei **emp.txt** in den Ausgabeordner kopiert wurde. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Data Factory programmgesteuert zu löschen, fügen Sie dem Programm die folgenden Codezeilen hinzu: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Nächste Schritte

Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Arbeiten Sie die [Tutorials](tutorial-copy-data-dot-net.md) durch, um zu erfahren, wie Sie Data Factory in anderen Szenarien verwenden können. 
