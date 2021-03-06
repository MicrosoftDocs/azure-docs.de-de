---
title: Entwickeln von Azure Functions mit Media Services v3
description: In diesem Thema wird gezeigt, wie Sie im Azure-Portal mit dem Entwickeln von Azure Functions mit Media Services v3 beginnen.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 389ad34bb856675dfabd761507ed07cc722c032a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281256"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Entwickeln von Azure Functions mit Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel wird veranschaulicht, wie Sie mit der Erstellung von Azure Functions beginnen, für die Media Services verwendet werden. Die in diesem Artikel definierte Azure Function überwacht einen Speicherkontocontainer mit dem Namen **input** für neue MP4-Dateien. Sobald eine Datei in den Speichercontainer abgelegt wird, führt der Blobtrigger die Funktion aus. Informationen zu Azure Functions finden Sie unter [Übersicht](../../azure-functions/functions-overview.md) und anderen Themen im Abschnitt **Azure Functions**.

Wenn Sie vorhandene Azure Functions-Instanzen, die Azure Media Services verwenden, erkunden und bereitstellen möchten, sehen Sie sich [Media Services mit Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Dieses Repository enthält Beispiele, in denen Media Services verwendet wird. Hiermit werden Workflows veranschaulicht, bei denen es um das Erfassen von Inhalten direkt aus Blob Storage, die Codierung und das Rückschreiben von Inhalt in Blob Storage geht.

## <a name="prerequisites"></a>Voraussetzungen

- Bevor Sie Ihre erste Funktion erstellen können, müssen Sie über ein aktives Azure-Konto verfügen. Wenn Sie noch kein Azure-Konto haben, [erstellen Sie ein kostenloses Konto](https://azure.microsoft.com/free/).
- Es ist ratsam, wie [hier](account-create-how-to.md) beschrieben ein AMS-Konto zu erstellen, wenn Sie Azure Functions erstellen möchten, mit denen Aktionen in Ihrem AMS-Konto (Azure Media Services) durchgeführt werden oder auf von Media Services gesendete Ereignisse gelauscht wird.

## <a name="create-a-function-app"></a>Erstellen einer Funktionen-App

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.
2. Erstellen Sie wie [hier](../../azure-functions/functions-create-function-app-portal.md) beschrieben eine Funktionen-App.

>[!NOTE]
> Ein von Ihnen angegebenes Speicherkonto sollte sich in derselben Region wie die App befinden.

## <a name="configure-function-app-settings"></a>Konfigurieren von Einstellungen der Funktionen-App

Beim Entwickeln von Media Services-Funktionen ist es nützlich, Umgebungsvariablen hinzuzufügen, die in Ihren Funktionen verwendet werden. Klicken Sie zum Konfigurieren von App-Einstellungen auf den Link „App-Einstellungen konfigurieren“. Weitere Informationen finden Sie unter [Konfigurieren der Einstellungen der Azure Function-App](../../azure-functions/functions-how-to-use-azure-function-app-settings.md).

## <a name="create-a-function"></a>Erstellen einer Funktion

Nachdem die Funktionen-App bereitgestellt wurde, wird sie unter den Azure Functions von **App Services** aufgeführt.

1. Wählen Sie Ihre Funktionen-App aus, und klicken Sie auf **Neue Funktion**.
1. Wählen Sie die Sprache **C#** und das Szenario **Datenverarbeitung** aus.
1. Wählen Sie die Vorlage **BlobTrigger** aus. Diese Funktion wird ausgelöst, wenn ein Blob in den **input**-Container hochgeladen wird. Der Name **input** wird unter **Path** im nächsten Schritt festgelegt.
1. Nachdem Sie **BlobTrigger** ausgewählt haben, werden auf der Seite einige weitere Steuerelemente angezeigt.
1. Klicken Sie auf **Erstellen**.

## <a name="files"></a>Dateien

Ihre Azure-Funktion ist Codedateien und anderen Dateien zugeordnet, die in diesem Abschnitt beschrieben werden. Bei Verwendung des Azure-Portals zum Erstellen einer Funktion werden **function.json** und **run.csx** für Sie erstellt. Sie müssen die Datei **project.json** hinzufügen oder hochladen. Der restliche Teil dieses Abschnitts enthält eine kurze Erläuterung der einzelnen Dateien und zeigt ihre Definitionen.

### <a name="functionjson"></a>function.json

Die Datei „function.json“ definiert die Funktionsbindungen und weitere Konfigurationseinstellungen. Die Laufzeit verwendet diese Datei, um zu ermitteln, welche Ereignisse überwacht werden sollen und wie Daten in die Funktionsausführung übergeben und aus dieser zurückgegeben werden. Weitere Informationen finden Sie unter [HTTP- und Webhookbindungen in Azure Functions](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Legen Sie die **disabled**-Eigenschaft auf **true** fest, um die Ausführung der Funktion zu verhindern.

Ersetzen Sie den Inhalt der vorhandenen Datei „function.json“ durch den folgenden Code:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.json

Die Datei „project.json“ enthält die Abhängigkeiten. Hier sehen Sie ein Beispiel für die Datei **project.json**, das die erforderlichen .NET Azure Media Services-Pakete aus NuGet enthält. Beachten Sie, dass sich die Versionsnummern mit den aktuellen Updates für die Pakete ändert, daher sollten Sie überprüfen, ob die aktuellste Version vorliegt.

Fügen Sie die folgende Definition der Datei „project.json“ hinzu.

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}

```

### <a name="runcsx"></a>run.csx

Dies ist der C#-Code für Ihre Funktion.  Die unten definierte Funktion überwacht einen Speicherkontocontainer mit dem Namen **input** (so wie im Pfad angegeben) für neue MP4-Dateien. Sobald eine Datei in den Speichercontainer abgelegt wird, führt der Blobtrigger die Funktion aus.

Das in diesem Abschnitt definierte Beispiel veranschaulicht Folgendes:

1. Erfassen eines Medienobjekts in einem Media Services-Konto (durch Kopieren eines Blobs in ein AMS-Medienobjekt)
2. Übermitteln eines Codierungsauftrags, der die Voreinstellung „Adaptives Streaming“ von Media Encoder Standard verwendet

Ersetzen Sie den Inhalt der vorhandenen Datei „run.csx“ durch folgenden Code: Wenn Sie mit der Definition Ihrer Funktion fertig sind, klicken Sie auf **Speichern und ausführen**.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>Testen der Funktion

Um die Funktion zu testen, müssen Sie eine MP4-Datei in den **input**-Container des Speicherkontos hochladen, das Sie in der Verbindungszeichenfolge angegeben haben.  

1. Wählen Sie das von Ihnen angegebene Speicherkonto aus.
2. Klicken Sie auf **Blobs**.
3. Klicken Sie auf **+ Container**. Geben Sie dem Container den Namen **input**.
4. Wählen Sie **Hochladen** aus, und navigieren Sie zu einer MP4-Datei, die Sie hochladen möchten.

>[!NOTE]
> Bei Verwendung eines Blobtriggers in einem Verbrauchsplan kann es bis zu 10 Minuten dauern, bis neue Blobs verarbeitet werden, nachdem eine Funktions-App in den Leerlauf gewechselt ist. Sobald die Funktions-App ausgeführt wird, werden die Blobs sofort verarbeitet. Weitere Informationen finden Sie unter [Blob Storage-Trigger und -Bindungen](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Nächste Schritte

Nun können Sie mit der Entwicklung einer Mediendienste-Anwendung beginnen.

Weitere Details und umfassende Beispiele/Lösungen zur Verwendung von Azure Functions und Logic Apps mit Azure Media Services zum Erstellen von benutzerdefinierten Inhaltserstellungsworkflows finden Sie unter [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration).
