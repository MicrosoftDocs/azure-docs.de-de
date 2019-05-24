---
title: Kopieren von Daten aus Azure Blob Storage nach SQL-Datenbank | Microsoft-Dokumentation
description: Dieses Tutorial bietet Schrittanleitungen zum Kopieren von Daten von Azure Blob Storage nach Azure SQL-Datenbank.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jingwang
ms.openlocfilehash: dbf45853f5f7a440578f3a9005831a4ef63d85e7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65778864"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopieren von Daten aus Azure Blob Storage nach Azure SQL-Datenbank mithilfe von Azure Data Factory

In diesem Tutorial erstellen Sie eine Data Factory-Pipeline, die Daten aus Azure Blob Storage nach Azure SQL-Datenbank kopiert. Das Konfigurationsmuster in diesem Tutorial gilt für Kopiervorgänge aus einem dateibasierten Datenspeicher in einen relationalen Datenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen verknüpfter Azure Storage- und Azure SQL-Datenbank-Dienste.
> * Erstellen von Azure Blob- und Azure SQL-Datenbank-Datasets.
> * Erstellen einer Pipeline mit einer Kopieraktivität.
> * Starten einer Pipelineausführung.
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Dieses Tutorial verwendet .NET SDK. Sie können andere Mechanismen zur Interaktion mit Azure Data Factory verwenden. Beispiele finden Sie unter „Schnellstarts“.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* **Azure Storage-Konto**. Sie verwenden den Blob Storage als **Quelldatenspeicher**. Wenn Sie kein Azure Storage-Konto besitzen, finden Sie im Artikel [Erstellen eines Speicherkontos](../storage/common/storage-quickstart-create-account.md) Schritte zum Erstellen eines solchen Kontos.
* **Azure SQL-Datenbank**. Sie verwenden die Datenbank als **Senkendatenspeicher**. Wenn Sie noch nicht über eine Azure SQL-Datenbank verfügen, finden Sie im Artikel [Erstellen einer Azure SQL-Datenbank](../sql-database/sql-database-get-started-portal.md) die Schritte zum Erstellen einer solchen Datenbank.
* **Visual Studio** 2015 oder 2017. In der exemplarischen Vorgehensweise in diesem Artikel wird Visual Studio 2017 verwendet.
* **Laden Sie das [Azure .NET SDK](https://azure.microsoft.com/downloads/) herunter, und installieren Sie es**.
* **Erstellen Sie eine Anwendung in Azure Active Directory**, indem Sie [diese Anweisungen](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) befolgen. Notieren Sie sich die folgenden Werte, die Sie in späteren Schritten benötigen: **Anwendungs-ID**, **Authentifizierungsschlüssel** und **Mandanten-ID**. Weisen Sie die Anwendung der Rolle **Mitwirkender** zu, indem Sie die Anweisungen im gleichen Artikel befolgen.

### <a name="create-a-blob-and-a-sql-table"></a>Erstellen eines Blobs und einer SQL-Tabelle

Bereiten Sie jetzt Ihr Azure-Blob und Ihre Azure SQL-Datenbank für das Tutorial vor, indem Sie die folgenden Schritte ausführen:

#### <a name="create-a-source-blob"></a>Erstellen eines Quellblobs

1. Starten Sie den Editor. Kopieren Sie den folgenden Text, und speichern Sie ihn als **inputEmp.txt**-Datei auf einem Datenträger.

    ```
    John|Doe
    Jane|Doe
    ```

2. Verwenden Sie Tools wie [Azure Storage-Explorer](https://storageexplorer.com/), um den Container **adfv2tutorial** zu erstellen und die Datei **inputEmp.txt** in den Container hochzuladen.

#### <a name="create-a-sink-sql-table"></a>Erstellen einer SQL-Senkentabelle

1. Verwenden Sie das folgende SQL-Skript, um die Tabelle **dbo.emp** in Ihrer Azure SQL-Datenbank zu erstellen.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Gewähren Sie Azure-Diensten den Zugriff auf SQL Server. Stellen Sie sicher, dass die Einstellung **Zugriff auf Azure-Dienste erlauben** für Ihre Azure SQL Server-Instanz **aktiviert** ist, damit der Data Factory-Dienst auf diese Instanz zugreifen kann. Führen Sie folgende Schritte aus, um diese Einstellung zu überprüfen und zu aktivieren:

    1. Klicken Sie links auf den Hub **Weitere Dienste** und dann auf **SQL Server**.
    2. Wählen Sie Ihren Server aus, und klicken Sie unter **EINSTELLUNGEN** auf **Firewall**.
    3. Klicken Sie auf der Seite **Firewalleinstellungen** für **Zugriff auf Azure-Dienste erlauben** auf **EIN**.


## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

Erstellen Sie mithilfe von Visual Studio 2015/2017 eine C# .NET-Konsolenanwendung.

1. Starten Sie **Visual Studio**.
2. Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**.
3. Wählen Sie in der Liste der Projekttypen auf der rechten Seite **Visual C#** -> **Konsolen-App (.NET Framework)** aus. .NET-Version 4.5.2 oder höher ist erforderlich.
4. Geben Sie **ADFv2Tutorial** als Namen ein.
5. Klicken Sie auf **OK**, um das Projekt zu erstellen.

## <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

1. Klicken Sie auf **Tools** -> **NuGet-Paket-Manager** -> **Paket-Manager-Konsole**.
2. Führen Sie in der **Paket-Manager-Konsole** die folgenden Befehle zum Installieren von Paketen aus. Ausführliche Informationen finden Sie auf der Seite zum [Microsoft.Azure.Management.DataFactory-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager
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

    
2. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der Variablen festlegt. Ersetzen Sie Platzhalter durch Ihre eigenen Werte. Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine Instanz der **DataFactoryManagementClient**-Klasse erstellt. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Datasets und eine Pipeline zu erstellen. Sie verwenden dieses Objekt ebenfalls zum Überwachen der Ausführungsdetails der Pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine **Data Factory** erstellt.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()

};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

In diesem Tutorial erstellen Sie zwei verknüpfte Dienste, jeweils einen für die Quelle und einen für die Senke:

### <a name="create-an-azure-storage-linked-service"></a>Erstellen eines verknüpften Azure Storage-Diensts

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der einen **verknüpften Azure Storage-Dienst** erstellt. Unter [Eigenschaften von verknüpften Azure Blobdiensten](connector-azure-blob-storage.md#linked-service-properties) erfahren Sie mehr über unterstützte Eigenschaften und Details.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

### <a name="create-an-azure-sql-database-linked-service"></a>Erstellen eines verknüpften Azure SQL-Datenbank-Diensts

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der einen **verknüpften Azure SQL-Datenbank-Dienst** erstellt. Unter [Eigenschaften von verknüpften Azure SQL-Datenbank-Diensten](connector-azure-sql-database.md#linked-service-properties) erfahren Sie mehr über unterstützte Eigenschaften und Details.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Erstellen von Datasets

In diesem Abschnitt erstellen Sie zwei Datasets: eines für die Quelle und das andere für die Senke. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Erstellen eines Datasets für das Azure-Quellblob

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der ein **Azure blob dataset (Azure-Blobdataset)** erstellt. Erfahren Sie mehr in [Azure Blob dataset properties(Azure Blobdataset-Eigenschaften)](connector-azure-blob-storage.md#dataset-properties) zum Thema unterstützte Eigenschaften und Details.

Sie definieren ein Dataset, das die Quelldaten im Azure-Blob darstellt. Dieses Blobdataset verweist auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt erstellen und beschreibt:

- Den Speicherort des Blobs, aus dem kopiert werden soll: **FolderPath** (Ordnerpfad) und **FileName** (Dateiname).
- Das Blobformat, das angibt, wie der Inhalt analysiert werden soll: **TextFormat** und die entsprechenden Einstellungen (beispielsweise Spaltentrennzeichen)
- Die Datenstruktur, einschließlich Spaltennamen und Datentypen – entspricht in diesem Fall der SQL-Senkentabelle

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
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Erstellen eines Datasets für eine Azure SQL-Senkendatenbank

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der ein **Azure SQL-Datenbank-Dataset** erstellt. Unter [Eigenschaften von Azure SQL-Datenbank-Datasets](connector-azure-sql-database.md#dataset-properties) erfahren Sie mehr über unterstützte Eigenschaften und Details.

Sie definieren ein Dataset, das die Senkendaten in Azure SQL-Datenbank darstellt. Dieses Dataset verweist auf den verknüpften Azure SQL-Datenbank-Dienst, den Sie im vorherigen Schritt erstellt haben. Es gibt auch die SQL-Tabelle an, die die kopierten Daten enthält. 

```csharp
// Create an Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der eine **Pipeline mit einer Kopieraktivität** erstellt. In diesem Tutorial enthält diese Pipeline eine einzige Aktivität: die Kopieraktivität, die das Blobdataset als Quelle und das SQL-Dataset als Senke verwendet. Unter [Kopieraktivität – Übersicht](copy-activity-overview.md) erhalten Sie weitere Informationen zur Kopieraktivität.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung

Fügen Sie der **Main**-Methode den folgenden Code hinzu, der **eine Pipelineausführung auslöst**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Überwachen einer Pipelineausführung

1. Fügen Sie der **Main**-Methode den folgenden Code hinzu, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis die Pipeline das Kopieren der Daten beendet hat.

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

2. Fügen Sie der **Main**-Methode den folgenden Code hinzu, der Ausführungsdetails zur Kopieraktivität abruft, z.B. die Größe der gelesenen/geschriebenen Daten.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);
    
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Ausführen des Codes

Erstellen und starten Sie die Anwendung, und überprüfen Sie dann die Pipelineausführung.

Die Konsole gibt den Status der Erstellung der Data Factory, des verknüpften Diensts, der Datasets, der Pipeline und der Pipelineausführung aus. Danach wird der Status der Pipelineausführung überprüft. Warten Sie, bis Sie die Ausführungsdetails der Kopieraktivität mit der Größe der gelesenen/geschriebenen Daten sehen. Verwenden Sie dann Tools wie z. B. SSMS (SQL Server Management Studio) oder Visual Studio, um eine Verbindung mit Ihrer Azure SQL-Zieldatenbank herzustellen und zu überprüfen, ob die Daten in die von Ihnen angegebene Tabelle kopiert werden.

### <a name="sample-output"></a>Beispielausgabe

```json
Creating a data factory AdfV2Tutorial...
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
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
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
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
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
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedDataIntegrationUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Nächste Schritte

Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Es wurde Folgendes vermittelt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen verknüpfter Azure Storage- und Azure SQL-Datenbank-Dienste.
> * Erstellen von Azure Blob- und Azure SQL-Datenbank-Datasets.
> * Erstellen einer Pipeline mit einer Kopieraktivität.
> * Starten einer Pipelineausführung.
> * Überwachen der Pipeline- und Aktivitätsausführungen.


Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Daten von einem lokalen Speicherort in die Cloud kopieren: 

> [!div class="nextstepaction"]
>[Kopieren von Daten aus lokalen Quellen in die Cloud](tutorial-hybrid-copy-powershell.md)
