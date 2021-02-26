---
title: Kopieren von Daten aus Azure Blob Storage nach Azure SQL-Datenbank mithilfe von Azure Data Factory
description: Dieses Tutorial bietet Schrittanleitungen zum Kopieren von Daten von Azure Blob Storage nach Azure SQL-Datenbank.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.date: 11/08/2019
ms.author: jingwang
ms.openlocfilehash: b2ec4a65f1001d6d1c93a23964d59972419f651e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380881"
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Kopieren von Daten aus Azure Blob Storage nach Azure SQL-Datenbank mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Data Factory-Pipeline, die Daten aus Azure Blob Storage nach Azure SQL-Datenbank kopiert. Das Konfigurationsmuster in diesem Tutorial gilt für Kopiervorgänge aus einem dateibasierten Datenspeicher in einen relationalen Datenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen verknüpfter Azure Storage- und Azure SQL-Datenbank-Dienste.
> * Erstellen von Azure Blob- und Azure SQL-Datenbank-Datasets.
> * Erstellen einer Pipeline mit einer Kopieraktivität.
> * Starten einer Pipelineausführung
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Dieses Tutorial verwendet .NET SDK. Sie können andere Mechanismen zur Interaktion mit Azure Data Factory verwenden. Beispiele finden Sie unter **Schnellstarts**.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* *Azure Storage-Konto*. Sie verwenden den Blob Storage als *Quelldatenspeicher*. Falls Sie noch nicht über ein Azure-Speicherkonto verfügen, lesen Sie die Informationen unter [Erstellen eines Azure Storage-Kontos](../storage/common/storage-account-create.md).
* *Azure SQL-Datenbank*. Sie verwenden die Datenbank als *Senkendatenspeicher*. Wenn Sie in Azure SQL-Datenbank keine Datenbank haben, lesen Sie [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md).
* *Visual Studio*. In der exemplarischen Vorgehensweise in diesem Artikel wird Visual Studio 2019 verwendet.
* *[Azure SDK für .NET](/dotnet/azure/dotnet-tools)*
* *Azure Active Directory-Anwendung*. Falls Sie noch nicht über eine Azure Active Directory-Anwendung verfügen, lesen Sie den Abschnitt [Erstellen einer Azure Active Directory-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) des Artikels [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](../active-directory/develop/howto-create-service-principal-portal.md). Kopieren Sie die folgenden Werte zur Verwendung in späteren Schritten: **Anwendungs-ID (Client)** , **Authentifizierungsschlüssel** und **Verzeichnis-ID (Mandant)** . Weisen Sie die Anwendung gemäß der Anleitung im gleichen Artikel der Rolle **Mitwirkender** zu.

### <a name="create-a-blob-and-a-sql-table"></a>Erstellen eines Blobs und einer SQL-Tabelle

Bereiten Sie als Nächstes Ihr Azure-Blob und Ihre Azure SQL-Datenbank für das Tutorial vor, indem Sie ein Quellblob und eine SQL-Senkentabelle erstellen.

#### <a name="create-a-source-blob"></a>Erstellen eines Quellblobs

Erstellen Sie zunächst ein Quellblob, indem Sie einen Container erstellen und eine Eingabetextdatei in den Container hochladen:

1. Öffnen Sie Notepad. Kopieren Sie den folgenden Text, und speichern Sie ihn lokal in einer Datei namens *inputEmp.txt*.

    ```inputEmp.txt
    John|Doe
    Jane|Doe
    ```

2. Verwenden Sie ein Tool wie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), um den Container *adfv2tutorial* zu erstellen und die Datei *inputEmp.txt* in den Container hochzuladen.

#### <a name="create-a-sink-sql-table"></a>Erstellen einer SQL-Senkentabelle

Erstellen Sie als Nächstes eine SQL-Senkentabelle:

1. Verwenden Sie das folgende SQL-Skript, um die Tabelle *dbo.emp* in Ihrer Azure SQL-Datenbank zu erstellen.

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

2. Gewähren Sie Azure-Diensten den Zugriff auf SQL-Datenbank. Lassen Sie für Ihren Server den Zugriff auf Azure-Dienste zu, damit der Data Factory-Dienst Daten in SQL-Datenbank schreiben kann. Führen Sie folgende Schritte aus, um diese Einstellung zu überprüfen und zu aktivieren:

    1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um Ihre SQL Server-Instanz zu verwalten. Suchen Sie nach **SQL-Server**, und wählen Sie die entsprechende Option aus.

    2. Wählen Sie Ihren Server aus.

    3. Wählen Sie unter der Überschrift **Sicherheit** des SQL Server-Menüs die Option **Firewalls und virtuelle Netzwerke** aus.

    4. Wählen Sie auf der Seite **Firewall und virtuelle Netzwerke** unter **Anderen Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten** die Option **EIN** aus.

## <a name="create-a-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts

Erstellen Sie mithilfe von Visual Studio eine C# .NET-Konsolenanwendung.

1. Öffnen Sie Visual Studio.
2. Wählen Sie im Fenster **Start** die Option **Neues Projekt erstellen** aus.
3. Wählen Sie im Fenster **Neues Projekt erstellen** in der Liste mit den Projekttypen die C#-Version von **Konsolen-App (.NET Framework)** aus. Wählen Sie **Weiter** aus.
4. Geben Sie im Fenster **Neues Projekt konfigurieren** unter **Projektname** die Zeichenfolge *ADFv2Tutorial* ein. Navigieren Sie unter **Speicherort** zu dem Verzeichnis, in dem das Projekt gespeichert werden soll, oder erstellen Sie es. Klicken Sie anschließend auf **Erstellen**. Das neue Projekt wird in der Visual Studio-IDE angezeigt.

## <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

Installieren Sie als Nächstes die erforderlichen Bibliothekspakete mithilfe des NuGet-Paket-Managers.

1. Wählen Sie auf der Menüleiste **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.
2. Führen Sie im Bereich **Paket-Manager-Konsole** die folgenden Befehle zum Installieren von Paketen aus. Weitere Informationen zum Azure Data Factory-NuGet-Paket finden Sie unter [Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```package manager console
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -PreRelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Erstellen eines Data Factory-Clients

Führen Sie die folgenden Schritte aus, um einen Data Factory-Client zu erstellen:

1. Öffnen Sie *Program.cs*, und überschreiben Sie die vorhandenen `using`-Anweisungen durch den folgenden Code, um Verweise auf Namespaces hinzuzufügen:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Fügen Sie der `Main`-Methode den folgenden Code hinzu, um Variablen festzulegen. Ersetzen Sie die 14 Platzhalter durch Ihre eigenen Werte.

    Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Wählen Sie in der Dropdownliste **Produkte** Folgendes aus: **Durchsuchen** > **Analytics** > **Data Factory**. Wählen Sie anschließend in der Dropdownliste **Regionen** die Regionen aus, die für Sie von Interesse sind. Daraufhin wird ein Raster mit dem Verfügbarkeitsstatus von Data Factory-Produkten für die ausgewählten Regionen angezeigt.

    > [!NOTE]
    > Von Data Factory verwendete Datenspeicher (etwa Azure Storage und Azure SQL-Datenbank) und Computedienste (etwa HDInsight) müssen sich nicht unbedingt in der für Data Factory ausgewählten Region befinden.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    string region = "<location to create the data factory in, such as East US>";
    string dataFactoryName = "<name of data factory to create (must be globally unique)>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString =
        "Server=tcp:<your server name>.database.windows.net,1433;" +
        "Database=<your database name>;" +
        "User ID=<your username>@<your server name>;" +
        "Password=<your password>;" +
        "Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Fügen Sie der `Main`-Methode den folgenden Code hinzu, um eine Instanz der Klasse `DataFactoryManagementClient` zu erstellen. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Datasets und eine Pipeline zu erstellen. Sie verwenden dieses Objekt ebenfalls zum Überwachen der Ausführungsdetails der Pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync(
        "https://management.azure.com/", cc
    ).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um eine *Data Factory* zu erstellen.

```csharp
// Create a data factory
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};

client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings)
);

while (
    client.Factories.Get(
        resourceGroup, dataFactoryName
    ).ProvisioningState == "PendingCreation"
)
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten

In diesem Tutorial werden zwei verknüpfte Dienste erstellt: einer für die Quelle und einer für die Senke.

### <a name="create-an-azure-storage-linked-service"></a>Erstellen eines verknüpften Azure Storage-Diensts

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um einen *verknüpften Azure Storage-Dienst* zu erstellen. Informationen zu unterstützten Eigenschaften und Details finden Sie unter [Eigenschaften des verknüpften Diensts](connector-azure-blob-storage.md#linked-service-properties).

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey
        )
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings)
);
```

### <a name="create-an-azure-sql-database-linked-service"></a>Erstellen eines verknüpften Azure SQL-Datenbank-Diensts

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um einen *verknüpften Azure SQL-Datenbank-Dienst* zu erstellen. Informationen zu unterstützten Eigenschaften und Details finden Sie unter [Eigenschaften des verknüpften Diensts](connector-azure-sql-database.md#linked-service-properties).

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);

client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings)
);
```

## <a name="create-datasets"></a>Erstellen von Datasets

In diesem Abschnitt erstellen Sie zwei Datasets: eins für die Quelle und eins für die Senke.

### <a name="create-a-dataset-for-source-azure-blob"></a>Erstellen eines Datasets für das Azure-Quellblob

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um ein *Azure-Blobdataset* zu erstellen. Informationen zu unterstützten Eigenschaften und Details finden Sie unter [Dataset-Eigenschaften](connector-azure-blob-storage.md#dataset-properties).

Sie definieren ein Dataset, das die Quelldaten im Azure-Blob darstellt. Dieses Blobdataset verweist auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt erstellen und beschreibt:

- Den Speicherort des Blobs, aus dem kopiert werden soll: `FolderPath` und `FileName`
- Das Blobformat, das angibt, wie der Inhalt analysiert werden soll: `TextFormat` und die zugehörigen Einstellungen (beispielsweise das Spaltentrennzeichen)
- Die Datenstruktur, einschließlich Spaltennamen und Datentypen (entspricht in diesem Beispiel der SQL-Senkentabelle)

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement { Name = "FirstName", Type = "String" },
            new DatasetDataElement { Name = "LastName", Type = "String" }
        }
    }
);

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings)
);
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Erstellen eines Datasets für eine Azure SQL-Senkendatenbank

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um ein *Azure SQL-Datenbank-Dataset* zu erstellen. Informationen zu unterstützten Eigenschaften und Details finden Sie unter [Dataset-Eigenschaften](connector-azure-sql-database.md#dataset-properties).

Sie definieren ein Dataset, das die Senkendaten in Azure SQL-Datenbank darstellt. Dieses Dataset verweist auf den verknüpften Azure SQL-Datenbank-Dienst, den Sie im vorherigen Schritt erstellt haben. Es gibt auch die SQL-Tabelle an, die die kopierten Daten enthält.

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

client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset
);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings)
);
```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um eine *Pipeline mit einer Kopieraktivität* zu erstellen. In diesem Tutorial enthält diese Pipeline eine einzelne Aktivität (`CopyActivity`), die das Blobdataset als Quelle und das SQL-Dataset als Senke verwendet. Ausführliche Informationen zur Kopieraktivität finden Sie unter [Kopieraktivität in Azure Data Factory](copy-activity-overview.md).

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
                new DatasetReference() { ReferenceName = blobDatasetName }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference { ReferenceName = sqlDatasetName }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings)
);
```

## <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung

Fügen Sie der `Main`-Methode den folgenden Code hinzu, um eine *Pipelineausführung auszulösen*.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Überwachen einer Pipelineausführung

Fügen Sie als Nächstes Code ein, um Pipelineausführungszustände zu überprüfen und Details zur Ausführung der Kopieraktivität abzurufen.

1. Fügen Sie der `Main`-Methode den folgenden Code hinzu, um kontinuierlich den Status der Pipelineausführung zu überprüfen, bis das Kopieren der Daten abgeschlossen ist.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(
            resourceGroup, dataFactoryName, runResponse.RunId
        );
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Fügen Sie der `Main`-Methode den folgenden Code hinzu, um Ausführungsdetails zur Kopieraktivität abzurufen (etwa die Größe der gelesenen oder geschriebenen Daten).

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    RunFilterParameters filterParams = new RunFilterParameters(
        DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)
    );

    ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
        resourceGroup, dataFactoryName, runResponse.RunId, filterParams
    );

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(queryResponse.Value.First().Output);
    }
    else
        Console.WriteLine(queryResponse.Value.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Ausführen des Codes

Wählen Sie **Erstellen** > **Projektmappe erstellen** aus, um die Anwendung zu erstellen. Starten Sie dann die Anwendung, indem Sie **Debuggen** > **Debuggen starten** auswählen, und überprüfen Sie die Pipelineausführung.

Die Konsole gibt den Status der Erstellung der Data Factory, des verknüpften Diensts, der Datasets, der Pipeline und der Pipelineausführung aus. Danach wird der Status der Pipelineausführung überprüft. Warten Sie, bis die Ausführungsdetails der Kopieraktivität mit der Größe der gelesenen/geschriebenen Daten angezeigt werden. Anschließend können Sie mithilfe von Tools wie SQL Server Management Studio (SSMS) oder Visual Studio eine Verbindung mit Ihrer Azure SQL-Zieldatenbank herstellen und überprüfen, ob die von Ihnen angegebene Tabelle die kopierten Daten enthält.

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

Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Data Factory.
> * Erstellen verknüpfter Azure Storage- und Azure SQL-Datenbank-Dienste.
> * Erstellen von Azure Blob- und Azure SQL-Datenbank-Datasets.
> * Erstellen einer Pipeline mit einer Kopieraktivität
> * Starten einer Pipelineausführung
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Fahren Sie mit dem folgenden Tutorial fort, um zu erfahren, wie Sie Daten von einem lokalen Speicherort in die Cloud kopieren:

> [!div class="nextstepaction"]
>[Kopieren von Daten aus lokalen Quellen in die Cloud](tutorial-hybrid-copy-powershell.md)
