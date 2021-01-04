---
title: Analysieren von Videoinhalten auf anstößiges Material in C# – Content Moderator
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Videoinhalte unter Verwendung des Content Moderator SDK für .NET auf verschiedenes anstößiges Material analysieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f98eac4305333ec7225c90da2777b7e02f050a0
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853531"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Analysieren von Videoinhalten auf anstößiges Material in C#

Dieser Artikel enthält Informationen und Codebeispiele, die Ihnen den Einstieg in die Verwendung des [Content Moderator SDK für .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) erleichtern, um Videos auf jugendgefährdende oder anzügliche Inhalte zu überprüfen.

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services/), bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen
- Eine beliebige Edition von [Visual Studio 2015 oder 2017](https://www.visualstudio.com/downloads/)

## <a name="set-up-azure-resources"></a>Einrichten von Azure-Ressourcen

Die Videomoderationsfunktion von Content Moderator ist als **Medienprozessor** in der kostenlosen öffentlichen Vorschauversion in Azure Media Services (AMS) verfügbar. Azure Media Services ist ein spezieller Azure-Dienst für das Speichern und Streamen von Videoinhalten. 

### <a name="create-an-azure-media-services-account"></a>Erstellen eines Azure Media Services-Kontos

Befolgen Sie die Anweisungen in [Erstellen eines Azure Media Services-Kontos](../../media-services/previous/media-services-portal-create-account.md), um AMS zu abonnieren und ein zugeordnetes Azure Storage-Konto zu erstellen. Erstellen Sie in diesem Speicherkonto einen neuen Blob Storage-Container.

### <a name="create-an-azure-active-directory-application"></a>Erstellen einer Azure Active Directory-Anwendung

Navigieren Sie zu Ihrem neuen AMS-Abonnement im Azure-Portal, und wählen Sie **API-Zugriff** aus im seitlichen Menü. Wählen Sie **Verbindung mit Azure Media Services über Dienstprinzipal herstellen** aus. Notieren Sie den Wert im Feld **REST-API-Endpunkt**. Diesen benötigen Sie später.

Wählen Sie im Abschnitt **Azure AD-App** die Option **Neue erstellen** und benennen Sie Ihre neue Azure AD-Anwendungsregistrierung (z.B. „VideoModADApp“). Klicken Sie auf **Speichern**, und warten Sie einige Minuten, während die Anwendung konfiguriert wird. Ihre neue App-Registrierung sollte dann im Abschnitt **Azure AD-App** der Seite angezeigt werden.

Wählen Sie Ihre App-Registrierung, und klicken Sie darunter auf **Anwendung verwalten**. Notieren Sie den Wert im Feld **Anwendungs-ID**. Diesen benötigen Sie später. Wählen Sie **Einstellungen** > **Schlüssel** aus, und geben Sie eine Beschreibung für einen neuen Schlüssel ein (z.B. „VideoModKey“). Klicken Sie auf **Speichern**, und notieren Sie den neuen Schlüsselwert. Kopieren Sie diese Zeichenfolge, und speichern Sie sie an einem sicheren Ort.

Eine ausführlichere Erläuterung für das oben beschriebene Verfahren finden Sie unter [Erste Schritte mit Azure AD-Authentifizierung](../../media-services/previous/media-services-portal-get-started-with-aad.md).

Wenn Sie fertig sind, können Sie die Videomoderation des Medienprozessors auf zwei unterschiedliche Arten verwenden.

## <a name="use-azure-media-services-explorer"></a>Verwenden des Azure Media Services-Explorers

Der Azure Media Services Explorer ist ein benutzerfreundliches Front-End für AMS. Verwenden Sie ihn, um Ihr AMS-Konto zu durchsuchen, Videos hochzuladen und Scans von Inhalten mit dem Content Moderator-Medienprozessor durchzuführen. Laden Sie [GitHub](https://github.com/Azure/Azure-Media-Services-Explorer/releases) herunter und installieren Sie es, oder lesen Sie den Blogbeitrag [Azure Media Services Explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/).

![Azure Media Services-Explorer mit Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>Erstellen des Visual Studio-Projekts

1. Erstellen Sie in Visual Studio ein neues Projekt vom Typ **Konsolen-App (.NET Framework)** , und nennen Sie es **VideoModeration**. 
1. Sollte Ihre Projektmappe noch andere Projekte enthalten, wählen Sie dieses Projekt als das alleinige Startprojekt aus.
1. Rufen Sie die erforderlichen NuGet-Pakete ab. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, wählen Sie **NuGet-Pakete verwalten** aus, suchen Sie nach den folgenden Paketen, und installieren Sie sie:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Hinzufügen von Videomoderationscode

Kopieren Sie als nächstes den Code aus dieser Anleitung, und fügen Sie ihn in Ihr Projekt ein, um ein einfaches Inhaltsmoderationsszenario zu implementieren.

### <a name="update-the-programs-using-statements"></a>Aktualisieren der using-Anweisungen des Programms

Fügen Sie am Anfang der Datei _Program.cs_ die folgenden `using`-Anweisungen hinzu:

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Einrichten von Ressourcenverweisen

Fügen Sie der Klasse **Program** in _Program.cs_ die folgenden statischen Felder hinzu. Diese Felder enthalten die Informationen, die für die Verbindung zu Ihrem AMS-Abonnement erforderlich sind. Geben Sie hier die Werte ein, die Sie in den vorherigen Schritten erhalten haben. Beachten Sie, dass `CLIENT_ID` der Wert für **Anwendungs-ID** für Ihre Azure AD-App und `CLIENT_SECRET` der Wert von „VideoModKey“ ist, den Sie für diese App erstellt haben.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Wenn Sie eine lokale Videodatei verwenden möchten (einfachster Fall), fügen Sie sie dem Projekt hinzu und geben Sie ihren Pfad als Wert `INPUT_FILE` ein (relative Pfade sind relativ zum Ausführungsverzeichnis).

Sie müssen auch die Datei _preset.json_ im aktuellen Verzeichnis erstellen und damit eine Versionsnummer angeben. Beispiel:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>Laden des/der Eingabevideos

Die **Main**-Methode der **Program**-Klasse erstellt zunächst einen Azure-Medienkontext und anschließend einen Azure-Speicherkontext, falls Ihre Videos sich in Blob Storage befinden. Der restliche Code überprüft ein Video aus einem lokalen Ordner, einem Blob oder mehreren Blogs innerhalb eines Azure-Speichercontainers. Sie können alle Optionen testen, indem Sie die anderen Codezeilen auskommentieren.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Erstellen eines Azure-Medienkontexts

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. Dabei werden Ihre AMS-Anmeldeinformationen zum Zulassen der Kommunikation mit AMS verwendet.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Code zum Erstellen eines Azure-Speicherkontexts

Fügen Sie der **Program**-Klasse die folgende Methode hinzu. Sie verwenden den Speicherkontext, der mithilfe Ihrer Storage-Anmeldeinformationen erstellt wurden, um auf Blob Storage zuzugreifen.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Code zum Erstellen von Azure-Medienobjekten aus lokalen Dateien und Blobs

Der Content Moderator-Medienprozessor führt Aufträge für **Objekte** innerhalb der Azure Media Services-Plattform aus.
Diese Methoden erstellen die Objekte aus einer lokalen Datei oder einem zugeordneten Blob.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Code zum Überprüfen mehrerer Videos (als Blobs) innerhalb eines Containers

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Methode zum Hinzufügen des Content Moderator-Auftrags

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Hinzufügen von Hilfsfunktionen

Diese Methoden laden die Content Moderator-Ausgabedatei (JSON) von der Azure Media Services-Ressource herunter, und verfolgen den Status des Moderationsauftrags, damit das Programm einen Ausführungsstatus in der Konsole protokollieren kann.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>Ausführen des Programms und Überprüfen der Ausgabe

Nachdem der Content Moderator-Auftrag abgeschlossen ist, analysieren Sie die JSON-Antwort. Diese besteht aus folgenden Elementen:

- Zusammenfassung der Videoinformationen
- **Screenshots** als "**fragments**"
- **Keyframes** als "**events**" mit dem Flag **reviewRecommended" (= true or false)"** , das auf **adultScore** und **racyScore** basiert.
- **start**, **duration**, **totalDuration** und **timestamp** werden in „Takten“ angegeben. Teilen Sie diese Werte durch **timescale**, um die Anzahl in Sekunden zu erhalten.
 
> [!NOTE]
> - `adultScore` gibt das mögliche Vorhandensein und die Vorhersage von Inhalten an, die in bestimmten Situationen als explizit sexuell oder nicht jugendfrei betrachtet werden können.
> - `racyScore` gibt das mögliche Vorhandensein und die Vorhersage von Inhalten an, die in bestimmten Situationen als zweideutig/freizügig oder als für Erwachsene bestimmt betrachtet werden können.
> - `adultScore` und `racyScore` liegen zwischen 0 und 1. Je höher der Wert, desto höher die vom Modell ermittelte Wahrscheinlichkeit, dass die Kategorie zutreffend ist. Diese Vorschauversion basiert nicht auf manuell programmierten Ergebnissen, sondern auf einem statistischen Modell. Es wird empfohlen, anhand Ihrer eigenen Inhalte zu testen, wie die jeweiligen Kategorien zu Ihren Anforderungen passen.
> - `reviewRecommended` ist entweder „true“ oder „false“ (abhängig von den internen Ergebnisschwellenwerten). Kunden müssen entscheiden, ob sie diesen Wert verwenden oder auf der Grundlage ihrer Inhaltsrichtlinien eigene Schwellenwerte festlegen möchten.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Videoüberprüfungen](video-reviews-quickstart-dotnet.md) aus Ihrer Moderationsausgabe generieren.

Laden Sie die [Visual Studio-Projektmappe](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) für diesen und andere Content Moderator-Schnellstarts für .NET herunter.