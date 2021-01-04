---
title: Verzweigungen in Azure Data Factory-Pipeline
description: Erfahren Sie, wie Sie den Datenfluss in Azure Data Factory, durch die Verkettung und Verzweigung von Aktivitäten steuern.
services: data-factory
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 9/27/2019
ms.openlocfilehash: ab7d17ee61d733483b6d3573e9bd69b1628c7940
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496941"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Verzweigen und Verketten von Aktivitäten in einer Data Factory-Pipeline

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial erstellen Sie eine Data Factory-Pipeline, die einige Ablaufsteuerungsfunktionen vorstellt. Diese Pipeline führt eine Kopieraktivität aus einem Container in Azure Blob Storage in einen anderen Container im selben Speicherkonto durch. Wenn die Kopieraktivität erfolgreich ist, versendet die Pipeline eine E-Mail mit Details zum Kopiervorgang. Dies umfasst beispielsweise die Menge der geschriebenen Daten. Wenn die Kopieraktivität nicht erfolgreich ist, versendet die Pipeline eine E-Mail mit Fehlerdetails, etwa der Fehlermeldung. In diesem Tutorial erfahren Sie, wie Sie Parameter übergeben.

Diese Grafik bietet einen Überblick über das Szenario:

![Diagramm: Azure Blob Storage als Ziel für einen Kopiervorgang. Bei erfolgreichem Vorgang wird eine E-Mail mit Details gesendet, bei nicht erfolgreichem Vorgang wird eine E-Mail mit Fehlerdetails gesendet.](media/tutorial-control-flow/overview.png)

Dieses Tutorial beschreibt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen einer Data Factory
> * Erstellen eines verknüpften Azure Storage-Diensts
> * Erstellen eines Azure-Blobdatasets.
> * Erstellen einer Pipeline, die eine Kopieraktivität und eine Webaktivität enthält.
> * Senden von Aktivitätsausgaben an nachfolgende Aktivitäten.
> * Verwenden von Parameterübergabe und Systemvariablen
> * Starten einer Pipelineausführung.
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Dieses Tutorial verwendet .NET SDK. Sie können andere Mechanismen zur Interaktion mit Azure Data Factory verwenden. Data Factory-Schnellstarts finden Sie unter [Schnellstarts](./quickstart-create-data-factory-portal.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure Storage-Konto. Sie verwenden Blob Storage als Quelldatenspeicher. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).
* Azure Storage-Explorer. Informationen zum Installieren des Tools finden Sie unter [Azure Storage-Explorer](https://storageexplorer.com/).
* Azure SQL-Datenbank. Sie verwenden die Datenbank als Senkendatenspeicher. Wenn Sie in Azure SQL-Datenbank keine Datenbank haben, lesen Sie [Erstellen einer Datenbank in Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md).
* Visual Studio. In diesem Artikel wird Visual Studio 2019 verwendet.
* Azure .NET SDK. Laden Sie das [Azure .NET SDK](https://azure.microsoft.com/downloads/) herunter, und installieren Sie es.

Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die Datenspeicher und Computeressourcen befinden sich unter Umständen in anderen Regionen. Die Speicher umfassen Azure Storage und Azure SQL-Datenbank. Die Computeressourcen umfassen HDInsight (von Data Factory verwendet).

Erstellen Sie eine Anwendung, wie unter [Erstellen einer Azure Active Directory-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) beschrieben. Weisen Sie die Anwendung der Rolle **Mitwirkender** zu, indem Sie die Anweisungen im gleichen Artikel befolgen. Später in diesem Tutorial benötigen Sie verschiedene Werte, wie z. B. **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** .

### <a name="create-a-blob-table"></a>Erstellen einer Blobtabelle

1. Öffnen Sie einen Text-Editor. Kopieren Sie den folgenden Text, und speichern Sie ihn lokal unter dem Namen *input.txt*.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Öffnen Sie den Azure Storage-Explorer. Erweitern Sie Ihr Speicherkonto. Klicken Sie mit der rechten Maustaste auf **Blobcontainer**, und wählen Sie **Blobcontainer erstellen** aus.
1. Geben Sie dem neuen Container den Namen *adfv2branch*, und wählen Sie **Hochladen** aus, um die Datei *input.txt* zum Container hinzuzufügen.

## <a name="create-visual-studio-project"></a>Erstellen eines Visual Studio-Projekts<a name="create-visual-studio-project"></a>

Erstellen Sie eine C# .NET-Konsolenanwendung:

1. Starten Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.
1. Wählen Sie unter **Neues Projekt erstellen** die Option **Konsolen-App (.NET Framework)** für C# und dann **Weiter** aus.
1. Nennen Sie das Projekt *ADFv2BranchTutorial*.
1. Wählen Sie **.NET Version 4.5.2** oder höher aus, und wählen Sie dann **Erstellen** aus.

### <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

1. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
1. Führen Sie in der **Paket-Manager-Konsole** die folgenden Befehle zum Installieren von Paketen aus. Ausführliche Informationen finden Sie auf der Seite zum [Microsoft.Azure.Management.DataFactory-NuGet-Paket](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Erstellen eines Data Factory-Clients

1. Öffnen Sie *Program.cs*, und fügen Sie die folgenden Anweisungen hinzu:

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

1. Fügen Sie diese statischen Variablen zur Klasse `Program` hinzu. Ersetzen Sie Platzhalter durch Ihre eigenen Werte.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container.
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Fügen Sie der `Main` -Methode den folgenden Code hinzu. Dieser Code erstellt eine Instanz der Klasse `DataFactoryManagementClient`. Sie verwenden dieses Objekt anschließend, um eine Data Factory, einen verknüpften Dienst, Datasets und eine Pipeline zu erstellen. Sie können dieses Objekt auch zum Überwachen der Ausführungsdetails der Pipeline verwenden.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Fügen Sie der Datei *Program.cs* eine `CreateOrUpdateDataFactory`-Methode hinzu:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Fügen Sie der Methode `Main` die folgenden Codezeile hinzu, die eine Data Factory erstellt:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Erstellen eines verknüpften Azure Storage-Diensts

1. Fügen Sie der Datei *Program.cs* eine `StorageLinkedServiceDefinition`-Methode hinzu:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Fügen Sie der Methode `Main` die folgende Codezeile hinzu, die einen verknüpften Azure Storage-Dienst erstellt:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Weitere Informationen zu unterstützten Eigenschaften und Details finden Sie unter [Eigenschaften des verknüpften Diensts](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Erstellen von Datasets

In diesem Abschnitt erstellen Sie zwei Datasets: eines für die Quelle und eines für die Senke.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Erstellen eines Datasets für ein Azure-Quellblob

Fügen Sie eine Methode hinzu, die ein *Azure-Blobdataset* erstellt. Weitere Informationen zu unterstützten Eigenschaften und Details finden Sie unter [Dataset-Eigenschaften](connector-azure-blob-storage.md#dataset-properties).

Fügen Sie der Datei *Program.cs* eine `SourceBlobDatasetDefinition`-Methode hinzu:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Sie definieren ein Dataset, das die Quelldaten im Azure-Blob darstellt. Dieses Blobdataset verweist auf den verknüpften Azure Storage-Dienst, den Sie im vorherigen Schritt unterstützt haben. Das Blobdataset beschreibt den Speicherort des Blobs, aus dem kopiert werden soll: *FolderPath* (Ordnerpfad) und *FileName* (Dateiname).

Beachten Sie die Verwendung von Parametern für *FolderPath*. `sourceBlobContainer` ist der Name des Parameters, und der Ausdruck wird durch die Werte ersetzt, die während der Pipelineausführung übergeben werden. Die Syntax zum Definieren von Parametern ist `@pipeline().parameters.<parameterName>`.

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Erstellen eines Datasets für ein Azure-Senkenblob

1. Fügen Sie der Datei *Program.cs* eine `SourceBlobDatasetDefinition`-Methode hinzu:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Fügen Sie der Methode `Main` den folgenden Code hinzu, der sowohl Azure Blob-Quelldatasets als auch -Senkendatasets erstellt.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>Erstellen einer C#-Klasse: EmailRequest

Erstellen Sie in Ihrem C#-Projekt eine Klasse mit dem Namen `EmailRequest`. Diese Klasse definiert, welche Eigenschaften die Pipeline in der Textanforderung beim Versenden einer E-Mail sendet. In diesem Tutorial sendet die Pipeline vier Eigenschaften von der Pipeline an die E-Mail:

* Message (Nachricht): Der Text der E-Mail. Bei einer erfolgreichen Kopie enthält diese Eigenschaft die Menge der geschriebenen Daten. Bei einer fehlerhaften Kopie enthält diese Eigenschaft die Fehlerdetails.
* Data Factory-Name. Der Name der Data Factory
* Pipelinename. Name der Pipeline.
* Empfänger. Der Parameter, der übergeben wird. Diese Eigenschaft gibt den Empfänger der E-Mail an.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>Erstellen von E-Mail-Workflow-Endpunkten

Um das Senden einer E-Mail auszulösen, verwenden Sie [Logic Apps](../logic-apps/logic-apps-overview.md) zum Definieren des Workflows. Details zum Erstellen eines Logic Apps-Workflows finden Sie unter [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Erfolgs-E-Mail-Workflow

Erstellen Sie im [Azure-Portal](https://portal.azure.com) einen Logic Apps-Workflow mit dem Namen *CopySuccessEmail*. Definieren Sie den Workflowtrigger als `When an HTTP request is received`. Geben Sie für Ihren Anforderungstrigger das `Request Body JSON Schema` mit dem folgenden JSON ein:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Ihr Workflow sollte in etwa wie im folgenden Beispiel aussehen:

![Erfolgs-E-Mail-Workflow](media/tutorial-control-flow/success-email-workflow-trigger.png)

Dieser JSON-Inhalt entspricht der Klasse `EmailRequest`, die Sie im vorherigen Abschnitt erstellt haben.

Fügen Sie eine `Office 365 Outlook – Send an email`-Aktion hinzu. Passen Sie für die Aktion **E-Mail senden** die Formatierungseinstellungen der E-Mail an Ihre Bedürfnisse an, indem Sie die Eigenschaften nutzen, die im **Text** des JSON-Anforderungsschemas übergeben wurden. Hier sehen Sie ein Beispiel:

![Logik-App-Designer: Aktion zum Senden einer E-Mail](media/tutorial-control-flow/customize-send-email-action.png)

Nachdem Sie den Workflow gespeichert haben, kopieren Sie den Wert **HTTP-POST-URL** aus dem Trigger, und speichern Sie ihn.

## <a name="fail-email-workflow"></a>Fehler-E-Mail-Workflow

Klonen Sie **CopySuccessEmail**, und erstellen Sie einen weiteren Logic Apps-Workflow mit dem Namen *CopyFailEmail*. Im Anforderungs-Trigger ist `Request Body JSON schema` gleich. Ändern Sie das Format Ihrer E-Mail (beispielsweise `Subject`), um eine E-Mail für einen nicht erfolgreichen Vorgang zu erhalten. Beispiel:

![Logik-App-Designer: Workflow für fehlerhafte E-Mail](media/tutorial-control-flow/fail-email-workflow.png)

Nachdem Sie den Workflow gespeichert haben, kopieren Sie den Wert **HTTP-POST-URL** aus dem Trigger, und speichern Sie ihn.

Sie verfügen nun über zwei Workflow-URLs, wie in den folgenden Beispielen gezeigt:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

Kehren Sie zu Ihrem Projekt in Visual Studio zurück. Wir fügen nun den Code hinzu, mit dem eine Pipeline mit einer Kopieraktivität und der Eigenschaft `DependsOn` erstellt wird. In diesem Tutorial enthält die Pipeline eine einzige Aktivität: die Kopieraktivität, die das Blobdataset als Quelle und ein anderes Blobdataset als Senke umfasst. Je nachdem, ob die Kopieraktivität erfolgreich ist oder ein Fehler auftritt, ruft sie unterschiedliche E-Mail-Aufgaben ab.

In dieser Pipeline verwenden Sie die folgenden Funktionen:

* Parameter
* Webaktivität
* Aktivitätsabhängigkeit
* Verwenden der Ausgabe aus einer Aktivität als eine Eingabe für eine andere Aktivität

1. Fügen Sie diese Methode zu Ihrem Projekt hinzu. In den folgenden Abschnitten finden Sie weitere Informationen.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Fügen Sie der Methode `Main` die folgenden Codezeile hinzu, die die Pipeline erstellt:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parameter

Der erste Abschnitt unseres Pipelinecodes definiert Parameter.

* `sourceBlobContainer`. Das Quell-Blobdataset verwendet diesen Parameter in der Pipeline.
* `sinkBlobContainer`. Das Senken-Blobdataset verwendet diesen Parameter in der Pipeline.
* `receiver`. Die beiden Webaktivitäten in der Pipeline, die Erfolgs- oder Fehlermeldungen an den Empfänger senden, verwenden diesen Parameter.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Webaktivität

Die Webaktivität ermöglicht einen Aufruf an jeden beliebigen REST-Endpunkt. Weitere Informationen zur Aktivität finden Sie unter [Webaktivität in Azure Data Factory](control-flow-web-activity.md). Die Pipeline verwendet eine Webaktivität, um den E-Mail-Workflow von Logic Apps aufzurufen. Sie erstellen zwei Webaktivitäten: eine, die Aufrufe an den Workflow `CopySuccessEmail` sendet, und eine, die `CopyFailWorkFlow` aufruft.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

Fügen Sie in der `Url`-Eigenschaft die **HTTP-POST-URL**-Endpunkte aus Ihrem Logic Apps-Workflow ein. Erstellen Sie in der Eigenschaft `Body` eine Instanz der Klasse `EmailRequest`. Die E-Mail-Anforderung enthält die folgenden Eigenschaften:

* Message (Nachricht): Übergibt den Wert von `@{activity('CopyBlobtoBlob').output.dataWritten`. Greift auf eine Eigenschaft der vorherigen Kopieraktivität zu, und übergibt den Wert von `dataWritten`. Für den Fehlerfall, übergeben Sie die Fehlerausgabe anstelle von `@{activity('CopyBlobtoBlob').error.message`.
* Data Factory-Name. Übergibt den Wert von `@{pipeline().DataFactory}`. Diese Systemvariable ermöglicht Ihnen den Zugriff auf den Namen der entsprechenden Data Factory. Eine Liste der Systemvariablen finden Sie unter [Systemvariablen](control-flow-system-variables.md).
* Pipelinename. Übergibt den Wert von `@{pipeline().Pipeline}`. Diese Systemvariable ermöglicht Ihnen den Zugriff auf den entsprechenden Pipelinenamen.
* Empfänger. Übergibt den Wert von `"@pipeline().parameters.receiver"`. Greift auf die Pipelineparameter zu.

Dieser Code erstellt eine neue Aktivitätsabhängigkeit, die von der vorherigen Kopieraktivität abhängt.

## <a name="create-a-pipeline-run"></a>Erstellen einer Pipelineausführung

Fügen Sie der Methode `Main` den folgenden Code hinzu, der eine Pipelineausführung auslöst.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main-Klasse

Die endgültige Methode `Main` sollte wie folgt aussehen.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Erstellen Sie Ihr Programm und führen Sie es aus, um die Ausführung einer Pipeline auszulösen!

## <a name="monitor-a-pipeline-run"></a>Überwachen einer Pipelineausführung

1. Fügen Sie den folgenden Code in die Methode `Main` ein:

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

    Dieser Code überprüft kontinuierlich den Status der Ausführung, bis das Kopieren der Daten beendet ist.

1. Fügen Sie der Methode `Main` den folgenden Code hinzu, der Ausführungsdetails zur Kopieraktivität abruft, wie z. B. die Größe der gelesenen/geschriebenen Daten:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList();

    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Ausführen des Codes

Erstellen und starten Sie die Anwendung, und überprüfen Sie dann die Pipelineausführung.

Die Anwendung zeigt den Fortschritt beim Erstellen der Data Factory, des verknüpften Diensts, der Datasets, der Pipeline und der Pipelineausführung an. Danach wird der Status der Pipelineausführung überprüft. Warten Sie, bis Sie die Ausführungsdetails der Kopieraktivität mit der Größe der gelesenen/geschriebenen Daten sehen. Verwenden Sie anschließend Tools wie z. B. Azure Storage-Explorer, um zu überprüfen, ob das Blob wie von Ihnen in den Variablen angegeben von *inputBlobPath* nach *outputBlobPath* kopiert wurde.

Die Ausgabe sollte wie im folgenden Beispiel aussehen:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=**_"
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
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
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> Erstellen einer Data Factory
> * Erstellen eines verknüpften Azure Storage-Diensts
> * Erstellen eines Azure-Blobdatasets.
> * Erstellen einer Pipeline, die eine Kopieraktivität und eine Webaktivität enthält.
> * Senden von Aktivitätsausgaben an nachfolgende Aktivitäten.
> * Verwenden von Parameterübergabe und Systemvariablen
> * Starten einer Pipelineausführung.
> * Überwachen der Pipeline- und Aktivitätsausführungen.

Sie können jetzt mit dem Abschnitt „Konzepte“ fortfahren, um weitere Informationen zu Azure Data Factory zu erhalten.
> [!div class="nextstepaction"]
>[Pipelines und Aktivitäten](concepts-pipelines-activities.md)