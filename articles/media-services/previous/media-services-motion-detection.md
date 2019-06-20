---
title: Erkennen von Bewegungen mit Azure Media Analytics | Microsoft Docs
description: Mit dem Medienprozessor (MP) Azure Media Motion Detector können Sie effizient Ihrem Interesse entsprechende Abschnitte innerhalb eines ansonsten langen und ereignislosen Videos identifizieren.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: milanga;juliako;
ms.openlocfilehash: e0b083cba575f4d1c0eb19afb76fca29431ae75e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61463530"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Erkennen von Bewegungen mit Azure Media Analytics
## <a name="overview"></a>Übersicht
Mit dem Medienprozessor (MP) **Azure Media Motion Detector** können Sie effizient Ihrem Interesse entsprechende Abschnitte innerhalb eines ansonsten langen und ereignislosen Videos identifizieren. Mit Bewegungserkennung können in Aufnahmen statischer Kameras Abschnitte des Videos identifiziert werden, wo Bewegung auftritt. Es wird eine JSON-Datei generiert, die Metadaten mit Zeitstempeln und der Umgebungsregion des Ereignisses enthält.

Diese für Sicherheitsvideoaufnahmen konzipierte Technologie kann Bewegung in relevante Ereignisse und falsch-positive Ergebnisse – z. B. Schatten und Beleuchtungsänderungen – kategorisieren. So können Sie Sicherheitswarnungen aus Kameraaufnahmen generieren, ohne unzählige irrelevante Ereignisse auswerten zu müssen, und sind in der Lage, Momente von Interesse aus langen Überwachungsvideos zu extrahieren.

Der MP **Azure Media Motion Detector** befindet sich derzeit in der Vorschauphase.

Dieser Artikel enthält Details zu **Azure Media Motion Detector** und zeigt die Verwendung mit dem Media Services SDK für .NET.

## <a name="motion-detector-input-files"></a>Motion Detector-Eingabedateien
Videodateien. Die folgenden Formate werden derzeit unterstützt: MP4, MOV und WMV.

## <a name="task-configuration-preset"></a>Taskkonfiguration (Voreinstellung)
Wenn Sie eine Aufgabe mit **Azure Media Motion Detector**erstellen, müssen Sie eine Konfigurationsvoreinstellung angeben. 

### <a name="parameters"></a>Parameter
Sie können die folgenden Parameter verwenden:

| NAME | Optionen | BESCHREIBUNG | Standard |
| --- | --- | --- | --- |
| sensitivityLevel |Zeichenfolge: „low“, „medium“, „high“ |Legt die Vertraulichkeitsstufe fest, mit der Bewegungen gemeldet werden. Passen Sie diese Einstellung an, um die Anzahl falsch positiver Ergebnisse zu steuern. |„medium“ |
| frameSamplingValue |Positive ganze Zahl |Legt fest, wie häufig der Algorithmus ausgeführt wird. 1 steht für jedes Bild, 2 steht für jedes zweite Bild usw. |1 |
| detectLightChange |Boolescher Wert: „true“, „false“ |Legt fest, ob kleinere Änderungen in den Ergebnissen gemeldet werden. |„false“ |
| mergeTimeThreshold |Xs-time: hh:mm:ss<br/>Beispiel: 00:00:03 |Gibt das Zeitfenster zwischen Bewegungsereignissen an, wenn zwei Ereignisse kombiniert und als ein Ereignis gemeldet werden. |00:00:00 |
| detectionZones |Ein Array mit Erkennungszonen:<br/>- Eine Erkennungszone ist ein Array aus mindestens drei Punkten.<br/>– Ein Punkt ist eine x- und y-Koordinate zwischen 0 und 1. |Beschreibt die Liste mit den zu verwendenden polygonalen Erkennungszonen.<br/>Die Ergebnisse werden mit den Zonen als ID gemeldet, und die erste ID lautet 'id':0. |Einzelne Zone, die den gesamten Rahmen abdeckt. |

### <a name="json-example"></a>JSON-Beispiel

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Motion Detector-Ausgabedateien
Ein Bewegungserkennungsauftrag gibt eine JSON-Datei in das Ausgabeasset zurück, die die Bewegungswarnungen und ihre Kategorien im Video beschreibt. Die Datei enthält Informationen über Zeit und Dauer der erkannten Bewegung im Video.

Die Motion Detector-API stellt Indikatoren bereit, sobald bewegte Objekte in einem Video mit unbewegtem Hintergrund (z. B. einem Überwachungsvideo) auftreten. Der Motion Detector wird trainiert, falsche Alarme, die z. B. auf Änderungen von Lichtverhältnissen und Schatten beruhen, zu reduzieren. Die aktuellen Einschränkungen der Algorithmen beziehen sich auf Nachtsichtvideos, halbtransparente und kleine Objekte.

### <a id="output_elements"></a>Elemente der JSON-Ausgabedatei
> [!NOTE]
> In der neuesten Version hat sich das JSON-Ausgabeformat geändert und stellt für einige Kunden unter Umständen eine größere Umstellung dar.
> 
> 

In der folgenden Tabelle werden die Elemente der Ausgabe-JSON-Datei beschrieben.

| Element | BESCHREIBUNG |
| --- | --- |
| Version |Dies bezieht sich auf die Version der Video-API. Die aktuelle Version ist 2. |
| Timescale |„Ticks“ pro Sekunde des Videos. |
| Offset |Dies ist der Zeitoffset für Zeitstempel in „Ticks“. In Version 1.0 von Video-APIs wird dies immer 0 sein. In zukünftigen Szenarien, die wir unterstützen, kann sich dieser Wert ändern. |
| Framerate |Frames des Videos pro Sekunde. |
| Width, Height |Bezieht sich auf die Breite und Höhe des Videos in Pixeln. |
| Start |Der Zeitstempel für den Start in „Ticks“. |
| Duration |Die Länge des Ereignisses in „Ticks“. |
| Intervall |Das Intervall für jeden Eintrag im Ereignis in „Ticks“. |
| Events |Jedes Ereignisfragment enthält die innerhalb dieser Zeitspanne erkannte Bewegung. |
| Type |Dies ist in der aktuellen Version immer ‘2’ für generische Bewegung. Diese Bezeichnung gibt Video-APIs die Flexibilität zum Kategorisieren der Bewegung in zukünftigen Versionen. |
| RegionID |Wie bereits dargelegt, wird dies in dieser Version immer 0 sein. Diese Bezeichnung gibt Video-APIs die Flexibilität, in zukünftigen Versionen Bewegung in verschiedenen Regionen zu suchen. |
| Regions |Bezieht sich auf den Bereich in Ihrem Video, in dem Bewegung Sie interessiert. <br/><br/>\- „id“ repräsentiert den Regionsbereich. In dieser Version ist nur ein Bereich vorhanden, ID 0. <br/>\- „type“ repräsentiert die Form der Region, in der die Bewegung von Interesse ist. Derzeit werden „rectangle“ (Rechteck) und „polygon“ (Polygon) unterstützt.<br/> Wenn Sie „rectangle“ angegeben haben, verfügt die Region über die Dimensionen „X“, „Y“, „Width“ (Breite) und „Height“ (Höhe). Die X- und Y-Koordinaten stellen die linken oberen XY-Koordinaten der Region in einem normalisierten Maßstab von 0,0 bis 1,0 dar. Die Breite und Höhe stellen die Größe der Region in einem normalisierten Maßstab von 0,0 bis 1,0 dar. In der aktuellen Version sind X, Y, Breite und Höhe stets auf „0, 0“ und „1, 1“ festgelegt. <br/>Wenn Sie „polygon“ angegeben haben, sind die Dimensionen der Region Punkte. <br/> |
| Fragments |Die Metadaten werden in verschiedene, als Fragmente bezeichnete Segmente aufgeteilt. Jedes Fragment enthält Startzeitpunkt, Dauer, Intervallnummer und Ereignis(se). Ein Fragment ohne Ereignisse bedeutet, dass zu diesem Startzeitpunkt und während dieser Dauer keine Bewegung erkannt wurde. |
| Eckige Klammern ( [] ) |Jede Klammer stellt ein Intervall im Ereignis dar. Leere Klammern für ein Intervall bedeuten, dass keine Bewegung erkannt wurde. |
| locations |Mit diesem neuen Eintrag wird unter den Ereignissen die Position aufgeführt, an der die Bewegung stattgefunden hat. Dies ist eine genauere Angabe als bei Erkennungszonen. |

Das folgende JSON-Beispiel zeigt die Ausgabe:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Einschränkungen
* Zu den unterstützten Eingabevideoformaten zählen MP4, MOV und WMV.
* Die Bewegungserkennung ist für Videos mit unbewegtem Hintergrund optimiert. Der Algorithmus konzentriert sich auf die Reduzierung falscher Alarme, wie Änderungen von Licht und Schatten.
* Manche Bewegung wird möglicherweise aufgrund technischer Probleme nicht erkannt; z. B. Nachtsichtvideos, halbtransparente und kleine Objekte.

## <a name="net-sample-code"></a>.NET-Beispielcode

Das folgende Programm zeigt Ihnen, wie Sie folgendes ausführen:

1. Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
2. Erstellen eines Auftrags mit einer Bewegungserkennungsaufgabe auf Basis einer Konfigurationsdatei, die die folgende JSON-Voreinstellung enthält: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Azure Media Services Motion Detector-Blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics – Übersicht](media-services-analytics-overview.md)

[Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

