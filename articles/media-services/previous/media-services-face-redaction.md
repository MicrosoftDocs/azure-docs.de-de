---
title: Bearbeiten von Gesichtern mit Azure Media Analytics | Microsoft Docs
description: In diesem Thema wird veranschaulicht, wie Sie Gesichter mit Azure Media Analytics bearbeiten können.
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
ms.date: 03/18/2019
ms.author: juliako;
ms.openlocfilehash: 1fe003ae13bc5f195932f4f140e17c4dc2791959
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61247349"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Bearbeiten von Gesichtern mit Azure Media Analytics 
## <a name="overview"></a>Übersicht
**Azure Media Redactor** ist ein [Azure Media Analytics](media-services-analytics-overview.md)-Medienprozessor (MP), der eine skalierbare Gesichtsbearbeitung in der Cloud ermöglicht. Mit der Gesichtsbearbeitung können Sie Ihr Video ändern, um Gesichter von ausgewählten Personen unscharf anzuzeigen und so unkenntlich zu machen. Es kann beispielsweise sein, dass Sie den Gesichtsbearbeitungsdienst nutzen möchten, wenn es um die öffentliche Sicherheit oder Medienarbeit geht. Die Bearbeitung von Material mit einer Länge von einigen Minuten, das mehrere Gesichter enthält, kann bei manueller Vorgehensweise Stunden dauern. Mit diesem Dienst sind für den Prozess der Gesichtsbearbeitung aber nur einige einfache Schritte erforderlich. Weitere Informationen finden Sie in [diesem](https://azure.microsoft.com/blog/azure-media-redactor/) Blog.

Dieser Artikel enthält Details zu **Azure Media Redactor** und veranschaulicht die Verwendung zusammen mit dem Media Services SDK für .NET.

## <a name="face-redaction-modes"></a>Modi der Gesichtsbearbeitung
Bei der Gesichtsbearbeitung werden Gesichter in jedem Bild eines Videos erkannt, und gleichzeitig wird das Gesichtsobjekt in Vorwärts- und Rückwärtsrichtung verfolgt, damit eine Person auch aus anderen Winkeln unkenntlich gemacht werden kann. Der automatisierte Prozess der Gesichtsbearbeitung ist komplex und führt nicht immer exakt zum gewünschten Ergebnis. Aus diesem Grund werden unter Media Analytics einige Optionen zum Ändern der Endausgabe bereitgestellt.

Zusätzlich zu einem vollautomatischen Modus ist ein zweistufiger Workflow vorhanden, der die Auswahl bzw. Abwahl von gefundenen Gesichtern anhand einer Liste mit IDs ermöglicht. Für beliebige Anpassungen pro Bild nutzt der Medienprozessor außerdem eine Metadatendatei im JSON-Format. Dieser Workflow ist in die Modi **Analyze** (Analysieren) und **Redact** (Bearbeiten) unterteilt. Sie können die beiden Modi auch zusammen in einem Durchlauf anwenden, bei dem beide Aufgaben als Teil eines Einzelvorgangs ausgeführt werden. Dieser Modus hat die Bezeichnung **Combined** (Kombiniert).

### <a name="combined-mode"></a>Kombinierter Modus
Hierbei wird eine bearbeitete MP4-Datei ohne manuelle Eingabe automatisch erstellt.

| Phase | Dateiname | Notizen |
| --- | --- | --- |
| Eingabeasset |foo.bar |Video im WMV-, MOV- oder MP4-Format |
| Eingabekonfiguration |Vorgangskonfiguration (Voreinstellung) |{'version':'1.0', 'options': {'mode':'combined'}} |
| Ausgabeasset |foo_redacted.mp4 |Video mit angewendeter Unschärfe |

#### <a name="input-example"></a>Eingabebeispiel:
[Video anzeigen](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Ausgabebeispiel:
[Video anzeigen](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Analysemodus
Im Durchlauf **Analyze** des zweistufigen Workflows wird eine Videoeingabe verwendet, und es werden eine JSON-Datei mit Gesichtspositionen und JPG-Bilder für jedes erkannte Gesicht erstellt.

| Phase | Dateiname | Notizen |
| --- | --- | --- |
| Eingabeasset |foo.bar |Video im WMV-, MPV- oder MP4-Format |
| Eingabekonfiguration |Vorgangskonfiguration (Voreinstellung) |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Ausgabeasset |foo_annotations.json |Anmerkungsdaten von Gesichtspositionen im JSON-Format. Können vom Benutzer bearbeitet werden, um die Begrenzungsrahmen für die unscharfen Bereiche zu ändern. Siehe Beispiel unten. |
| Ausgabeasset |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Zugeschnittenes JPG-Bild jedes erkannten Gesichts mit Nummer für die „labelId“ des Gesichts |

#### <a name="output-example"></a>Ausgabebeispiel:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Bearbeitungsmodus
Im zweiten Durchlauf des Workflows wird eine größere Anzahl von Eingaben verwendet, die zu einem einzelnen Asset zusammengefasst werden müssen.

Hierzu gehören eine Liste mit den IDs für die Anwendung der Unschärfe, das ursprüngliche Video und die JSON-Datei mit den Anmerkungen. In diesem Modus werden die Anmerkungen verwendet, um die Unschärfe auf das Eingabevideo anzuwenden.

In der Ausgabe des Analysedurchlaufs ist das Originalvideo nicht enthalten. Das Video muss in das Eingabeasset für die Aufgabe im Bearbeitungsmodus hochgeladen und als primäre Datei ausgewählt werden.

| Phase | Dateiname | Notizen |
| --- | --- | --- |
| Eingabeasset |foo.bar |Video im WMV-, MPV- oder MP4-Format. Dasselbe Video wie in Schritt 1. |
| Eingabeasset |foo_annotations.json |Metadatendatei mit Anmerkungen aus Phase 1 mit optionalen Änderungen |
| Eingabeasset |foo_IDList.txt (optional) |Optionale neue Liste mit zu bearbeitenden Gesicht-IDs mit Zeilentrennung. Wenn die Datei leer ist, werden alle Gesichter verschwommen angezeigt. |
| Eingabekonfiguration |Vorgangskonfiguration (Voreinstellung) |{'version':'1.0', 'options': {'mode':'redact'}} |
| Ausgabeasset |foo_redacted.mp4 |Video mit angewendeter Unschärfe, basierend auf Anmerkungen |

#### <a name="example-output"></a>Beispielausgabe
Dies ist die Ausgabe von IDList mit einer ausgewählten ID.

[Video anzeigen](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Beispiel: „foo_IDList.txt“
 
     1
     2
     3

## <a name="blur-types"></a>Typen von Weichzeichnern

In den Modi **Kombiniert** und **Bearbeiten** gibt es fünf verschiedene Weichzeichnermodi, zwischen denen Sie über die JSON-Eingabekonfiguration wählen können: **Low** (Niedrig), **Med** (Medium), **High** (Hoch), **Box** (Feld) und **Black** (Schwarz). Standardmäßig wird **Medium** verwendet.

Untenstehend finden Sie Beispiele für Weichzeichnertypen.

### <a name="example-json"></a>JSON-Beispiel:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Niedrig

![Niedrig](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Medium

![Medium](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Hoch

![Hoch](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Schwarz

![Schwarz](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>Elemente der JSON-Ausgabedatei

Der Medienprozessor zur Gesichtsbearbeitung ermöglicht eine Gesichtspositionserkennung und -nachverfolgung mit hoher Genauigkeit, sodass bis zu 64 menschliche Gesichter in einem Videobild erkannt werden können. Frontalansichten von Gesichtern bieten die besten Ergebnisse, während Seitenansichten und kleine Gesichter (maximal 24 x 24 Pixel) unter Umständen nicht so genau erkannt werden.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-Beispielcode

Das folgende Programm zeigt Ihnen, wie Sie folgendes ausführen:

1. Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
2. Erstellen eines Auftrags mit einer Gesichtsbearbeitungsaufgabe auf Basis einer Konfigurationsdatei, die die folgende JSON-Voreinstellung enthält: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Verwandte Links
[Azure Media Services Analytics – Übersicht](media-services-analytics-overview.md)

[Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

