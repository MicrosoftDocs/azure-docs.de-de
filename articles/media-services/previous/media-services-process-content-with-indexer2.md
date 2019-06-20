---
title: Indizieren von Mediendateien mit der Vorschauversion von Azure Media Indexer 2 | Microsoft Docs
description: Mit dem Azure Media Indexer können Sie die Inhalte Ihrer Mediendateien durchsuchbar machen und eine Volltext-Aufzeichnung für Untertitel und Schlüsselwörter generieren. In diesem Thema wird die Verwendung von Media Indexer 2 Preview erläutert.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 304ecda320e1fdd9573bc961fde74efe03400aa3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712953"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Indizieren von Mediendateien mit Azure Media Indexer 2 Preview
## <a name="overview"></a>Übersicht
Mit dem Medienprozessor (MP) **Azure Media Indexer 2 Preview** können Sie Mediendateien und Inhalte durchsuchbar machen sowie Untertitelspuren und Schlüsselwörter generieren. Verglichen mit der vorherigen Version des [Azure Media Indexer](media-services-index-content.md)führt **Azure Media Indexer 2 Preview** eine schnellere Indizierung durch und bietet eine breitere Sprachunterstützung. Zu den unterstützten Sprachen zählen Englisch, Spanisch, Französisch, Deutsch, Italienisch, Chinesisch (Mandarin, vereinfacht), Portugiesisch, Arabisch, Russisch und Japanisch.

Der Medienprozessor von **Azure Media Indexer 2 Preview** befindet sich derzeit in der Vorschauphase.

In diesem Artikel wird dargestellt, wie Sie Indizierungsaufträge mit **Azure Media Indexer 2 Preview** erstellen.

> [!NOTE]
> Es gelten die folgenden Bedingungen:
> 
> Indexer 2 wird in Azure China und Azure Government nicht unterstützt.
> 
> Stellen Sie beim Indizieren von Inhalten sicher, dass Mediendateien verwendet werden, die sehr klare Sprache enthalten (ohne Hintergrundmusik, Lärm, Effekte oder Mikrofonrauschen). Die folgenden Beispiele sind geeignete Inhalte: aufgezeichnete Besprechungen, Vorträge oder Präsentationen. Folgende Inhalte sind für die Indizierung ggf. nicht geeignet: Filme, Fernsehsendungen, Material mit gemischten Audio- und Soundeffekten, schlecht aufgezeichnete Inhalte mit Hintergrundgeräuschen (Rauschen).
> 
> 

Dieser Artikel enthält Details zu **Azure Media Indexer 2 Preview** und zeigt, wie diese Version mit dem Media Services SDK für .NET verwendet wird.

## <a name="input-and-output-files"></a>Ein- und Ausgabedateien
### <a name="input-files"></a>Eingabedateien
Audio- oder Videodateien

### <a name="output-files"></a>Ausgabedateien
Ein Indizierungsauftrag kann Untertiteldateien in den folgenden Formaten generieren:  

* **SAMI**
* **TTML**
* **WebVTT**

Untertiteldateien in diesen Formaten können verwendet werden, um Hörgeschädigten Audio- und Videodateien zugänglich zu machen.

## <a name="task-configuration-preset"></a>Taskkonfiguration (Voreinstellung)
Wenn Sie eine Indizierungsaufgabe mit **Azure Media Indexer 2 Preview**erstellen, müssen Sie eine Konfigurationsvoreinstellung angeben.

Die folgende JSON legt verfügbare Parameter fest.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Unterstützte Sprachen
Azure Media Indexer 2 Preview unterstützt Sprache in Text für die folgenden Sprachen (wenn Sie den Namen der Sprache in der Taskkonfiguration angeben, verwenden Sie einen 4-Zeichen-Code in Klammern, wie unten gezeigt):

* Englisch [EnUs]
* Spanisch [EsEs]
* Chinesisch (Mandarin, vereinfacht) [ZhCn]
* Französisch [FrFr]
* Deutsch [DeDe]
* Italienisch [ItIt]
* Portugiesisch [PtBr]
* Arabisch (ägyptisch) [ArEg]
* Japanisch [JaJp]
* Russisch [RuRu]
* Englisch (Britisch) [EnGb]
* Spanisch (Mexiko) [EsMx] 

## <a name="supported-file-types"></a>Unterstützte Dateitypen

Informationen zu den unterstützten Dateitypen finden Sie im Abschnitt zu den [unterstützten Codecs/Formaten](media-services-media-encoder-standard-formats.md#input-containerfile-formats).

## <a name="net-sample-code"></a>.NET-Beispielcode

Das folgende Programm zeigt Ihnen, wie Sie folgendes ausführen:

1. Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
2. Erstellen eines Auftrags mit einer Indizierungsaufgabe anhand einer Konfigurationsdatei, die die folgende JSON-Voreinstellung enthält:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Laden Sie die Ausgabedatei herunter. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 

#### <a name="example"></a>Beispiel

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
{
    class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

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
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:
                    // Cast sender as a job.
                    IJob job = (IJob)sender;
                    // Display or log error details as needed.
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwandte Links
[Azure Media Services Analytics – Übersicht](media-services-analytics-overview.md)

[Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

