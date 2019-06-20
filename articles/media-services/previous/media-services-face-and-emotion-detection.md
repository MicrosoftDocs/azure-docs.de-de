---
title: Gesichts- und Emotionenerkennung mit Azure Media Analytics | Microsoft-Dokumentation
description: Dieses Thema veranschaulicht, wie Azure Media Analytics Gesichter und Emotionen erkennt.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: milanga;juliako;
ms.openlocfilehash: 46e60583da79006c133c8d9fac63e27f28bd699f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217206"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Gesichts- und Emotionenerkennung mit Azure Media Analytics
## <a name="overview"></a>Übersicht
Mit dem Medienprozessor (MP) **Azure Media Face Detector** können Sie Bewegungen zählen und nachverfolgen und sogar Teilnahme sowie Reaktion der Zielgruppe anhand von Gesichtsausdrücken einschätzen. Dieser Dienst enthält zwei Funktionen: 

* **Gesichtserkennung**
  
    Die Gesichtserkennung sucht und verfolgt menschliche Gesichter in einem Video. Mehrere Gesichter können erkannt und anschließend nachverfolgt werden, während sie sich bewegen, wobei Metadaten zu Zeit und Ort in einer JSON-Datei zurückgegeben werden. Während der Nachverfolgung wird versucht, dem gleichen Gesicht eine konsistente ID zuzuweisen, während die Person sich auf dem Bildschirm bewegt, auch wenn sie verdeckt wird oder kurzzeitig nicht im Frame ist.
  
  > [!NOTE]
  > Dieser Dienst führt keine Gesichtserkennung durch. Eine Person, die zu lange nicht im Frame ist oder verdeckt wird, erhält bei ihrer Rückkehr eine neue ID.
  > 
  > 
* **Emotionenerkennung**
  
    Emotionenerkennung ist eine optionale Komponente des Gesichtserkennungs-Medienprozessors, die eine Analyse verschiedener emotionaler Attribute in den Gesichtern liefert, einschließlich Glück, Trauer, Angst, Wut und anderer Emotionen. 

Der MP **Azure Media Face Detector** befindet sich derzeit in der Vorschauphase.

Dieser Artikel enthält Details zu **Azure Media Face Detector** und zeigt die Verwendung mit dem Media Services SDK für .NET.

## <a name="face-detector-input-files"></a>Face Detector-Eingabedateien
Videodateien. Die folgenden Formate werden derzeit unterstützt: MP4, MOV und WMV.

## <a name="face-detector-output-files"></a>Face Detector-Ausgabedateien
Die API zur Gesichtserkennung und -nachverfolgung ermöglicht Gesichtspositionserkennung und -nachverfolgung mit hoher Genauigkeit, sodass bis zu 64 menschliche Gesichter in einem Video erkannt werden können. Frontalansichten von Gesichtern bieten die besten Ergebnisse, während Seitenansichten und kleine Gesichter (höchstens 24 x 24 Pixel) möglicherweise nicht so genau erkannt werden.

Die erkannten und nachverfolgten Gesichter werden mit den Koordinaten (links, oben, Breite und Höhe) zurückgegeben, die die Position der Gesichter im Bild in Pixeln sowie eine Gesichts-ID angeben, die die Nachverfolgung einer einzelnen Person kenntlich macht. Gesichts-IDs können zurückgesetzt werden, wenn die Frontalansicht des Gesichts verloren geht oder im Frame überlappt wird, sodass einigen Personen möglicherweise mehrere IDs zugewiesen werden.

## <a id="output_elements"></a>Elemente der JSON-Ausgabedatei

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Face Detector verwendet Techniken der Fragmentierung (wobei die Metadaten in zeitbasierte Blöcke unterteilt werden können, sodass Sie nichts herunterladen müssen, was Sie nicht benötigen) und Segmentierung (wobei die Ereignisse aufgeteilt werden, falls sie zu groß werden). Einige einfache Berechnungen können Ihnen helfen, die Daten zu transformieren. Wenn ein Ereignis beispielsweise mit 6.300 (Ticks), einer Zeitskala von 2.997 (Ticks/s) und Framerate von 29,97 (Bilder/s) begonnen hat, dann gilt:

* Start/Zeitskala = 2,1 Sekunden
* Sekunden × Framerate = 63 Frames

## <a name="face-detection-input-and-output-example"></a>Eingabe- und Ausgabebeispiel für Gesichtserkennung
### <a name="input-video"></a>Videoeingang
[Videoeingang](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Aufgabenkonfiguration (Voreinstellung)
Wenn Sie eine Aufgabe mit **Azure Media Face Detector**erstellen, müssen Sie eine Konfigurationsvoreinstellung angeben. Die folgende Konfigurationsvoreinstellung ist nur zur Gesichtserkennung bestimmt.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Beschreibungen der Attribute
| Attributname | BESCHREIBUNG |
| --- | --- |
| Mode |Schneller: höhere Verarbeitungsgeschwindigkeit, aber weniger Genauigkeit (Standard).|

### <a name="json-output"></a>JSON-Ausgabe
Das folgende Beispiel einer JSON-Ausgabe wurde abgeschnitten.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Eingabe- und Ausgabebeispiel für Emotionenerkennung
### <a name="input-video"></a>Videoeingang
[Videoeingang](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Aufgabenkonfiguration (Voreinstellung)
Wenn Sie eine Aufgabe mit **Azure Media Face Detector**erstellen, müssen Sie eine Konfigurationsvoreinstellung angeben. Die folgende Konfigurationsvoreinstellung gibt an, eine JSON-Datei auf Basis der Emotionenerkennung zu erstellen.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Beschreibungen der Attribute
| Attributname | BESCHREIBUNG |
| --- | --- |
| Mode |Gesichtserkennung: nur Gesichtserkennung.<br/>PerFaceEmotion: unabhängige Rückgabe der Emotionen für jede Gesichtserkennung.<br/>AggregateEmotion: Rückgabe der durchschnittlichen Emotionswerte für alle Gesichter im Frame. |
| AggregateEmotionWindowMs |Bei Auswahl des Modus „AggregateEmotion“ verwenden. Gibt die Länge des Videos, das jeweils zum Produzieren eines Aggregierergebnisses verwendet wird, in Millisekunden an. |
| AggregateEmotionIntervalMs |Bei Auswahl des Modus „AggregateEmotion“ verwenden. Gibt an, mit welcher Häufigkeit Aggregierergebnisse erzeugt werden. |

#### <a name="aggregate-defaults"></a>Aggregierstandardwerte
Die folgenden Werte werden für Aggregierfenster und Intervalleinstellungen empfohlen. AggregateEmotionWindowMs sollte länger als AggregateEmotionIntervalMs sein.

|| Standardwerte (s) | Maximum (s) | Minimum (s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>JSON-Ausgabe
JSON-Ausgabe für Emotionsaggregierung (abgeschnitten):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Einschränkungen
* Zu den unterstützten Eingabevideoformaten zählen MP4, MOV und WMV.
* Die erkennbare Gesichtsgröße reicht von 24 x 24 bis 2.048 x 2.048 Pixel. Außerhalb dieses Bereichs liegende Gesichter werden nicht erkannt.
* Für jedes Video werden maximal 64 Gesichter zurückgegeben.
* Einige Gesichter werden möglicherweise aufgrund technischer Probleme nicht erkannt: z. B. sehr große Gesichtswinkel (Kopfpose) und starke Verdeckung. Frontalansichten und nahezu der Frontalansicht entsprechende Ansichten von Gesichtern führen zu den besten Ergebnissen.

## <a name="net-sample-code"></a>.NET-Beispielcode

Das folgende Programm zeigt Ihnen, wie Sie folgendes ausführen:

1. Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
2. Erstellen eines Auftrags mit einer Gesichtserkennungsaufgabe auf Basis einer Konfigurationsdatei, die die folgende JSON-Voreinstellung enthält: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Herunterladen der JSON-Ausgabedateien. 

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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Azure Media Analytics-Demos](https://amslabs.azurewebsites.net/demos/Analytics.html)

