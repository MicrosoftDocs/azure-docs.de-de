---
title: Verwenden von Azure Media Video Thumbnails zum Erstellen einer Videozusammenfassung | Microsoft Docs
description: Die Videozusammenfassung kann Ihnen dabei helfen, Zusammenfassungen von langen Videos durch das automatische Auswählen von interessanten Ausschnitten aus dem Quellvideo zu erstellen. Dies ist hilfreich, wenn Sie einen schnellen Überblick darüber bieten wollen, was man in einem langen Video zu sehen bekommt.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 0fcacf68f4b41ed8945a6a40d7da125aef499947
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60825531"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Verwenden von Azure Media Video Thumbnails zum Erstellen einer Videozusammenfassung  
## <a name="overview"></a>Übersicht
Mit dem **Azure Media-Videovorschau**-Medienprozessor können Sie eine Zusammenfassung eines Videos erstellen. Das ist hilfreich für Kunden, die nur eine zusammenfassende Vorschau eines langen Videos ansehen möchten. Kunden möchten zum Beispiel nur eine kurze „Videozusammenfassung“ sehen, wenn sie mit der Maus auf eine Miniaturansicht zeigen. Durch Anpassen der Parameter von **Azure Media-Videovorschau** per Konfigurationsvoreinstellung können Sie die leistungsstarken Technologien des Medienprozessors für das Erkennen und Zusammenfügen von Szenen verwenden, um algorithmisch einen beschreibenden Subclip zu erstellen.  

Der **Azure Media Video Thumbnails** -Medienprozessor befindet sich derzeit in der Vorschauphase.

Dieser Artikel enthält Details zur **Azure Media-Videovorschau** und zeigt, wie diese mit dem Media Services SDK für .NET verwendet wird.

## <a name="limitations"></a>Einschränkungen

In einigen Fällen, wenn Ihr Video nicht aus unterschiedlichen Szenen besteht, ist die Ausgabe nur eine einzelne Aufnahme.

## <a name="video-summary-example"></a>Beispiel einer Videozusammenfassung
Hier sind einige Beispiele für die Fähigkeiten des Azure Media Video Thumbnails-Medienprozessors :

### <a name="original-video"></a>Original-Video
[Original-Video](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Ergebnis der Video-Miniaturansicht
[Ergebnis der Video-Miniaturansicht](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Taskkonfiguration (Voreinstellung)
Beim Erstellen eines Video-Miniaturansichten-Tasks mit **Azure Media Video Thumbnails**müssen Sie eine Voreinstellungskonfiguration angeben. Das obige Miniaturansicht-Beispiel wurde mit der folgenden JSON-Basiskonfiguration erstellt:

```json
    {
        "version":"1.0"
    }
```

Derzeit können Sie die folgenden Parameter ändern:

| Param | BESCHREIBUNG |
| --- | --- |
| outputAudio |Gibt an, ob das resultierende Video Audiodaten enthält oder nicht. <br/>Zulässige Werte sind: „True“ oder „False“. Der Standardwert ist TRUE. |
| fadeInFadeOut |Gibt an, ob Übergänge zwischen den separaten bewegten Miniaturansichten verwendet werden oder nicht.  <br/>Zulässige Werte sind: „True“ oder „False“.  Der Standardwert ist TRUE. |
| maxMotionThumbnailDurationInSecs |Ganze Zahl, die angibt, wie lange das gesamte fertige Video sein soll.  Der Standardwert hängt von der ursprünglichen Videodauer ab. |

Die folgende Tabelle beschreibt die Standarddauer, wenn **maxMotionThumbnailInSecs** nicht verwendet wird.

|  |  |  |
| --- | --- | --- |
| Videodauer |d < 3 Minuten |3 Minuten < d < 15 Minuten |
| Dauer der Miniaturansicht |15 Sekunden (2 bis 3 Szenen) |30 Sekunden (3 bis 5 Szenen) |

Die folgende JSON legt verfügbare Parameter fest.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET-Beispielcode

Das folgende Programm zeigt Ihnen, wie Sie folgendes ausführen:

1. Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
2. Erstellt einen Auftrag mit einer Video-Miniaturansicht anhand einer Konfigurationsdatei, die die folgende JSON-Voreinstellung enthält: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. Sie die Ausgabedateien herunterladen. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Ausgabe der Video-Miniaturansicht
[Ausgabe der Video-Miniaturansicht](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwandte Links
[Azure Media Services Analytics – Übersicht](media-services-analytics-overview.md)

[Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

