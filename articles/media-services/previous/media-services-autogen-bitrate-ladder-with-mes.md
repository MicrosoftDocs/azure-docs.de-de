---
title: Verwenden von Azure Media Encoder Standard zum automatischen Generieren einer Reihe von Bitraten/Auflösungs-Paaren| Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie Sie mit Media Encoder Standard (MES) automatisch eine Reihe von Bitraten/Auflösungs-Paaren auf Basis der eingegebenen Auflösung und Bitrate generieren können. Eingegebene Auflösung und Bitrate werden nie überschritten. Ist die Eingabe beispielsweise 720p bei 3MBit/s, bleibt die Ausgabe bestenfalls bei 720p, und es beginnt mit niedrigeren Raten als 3MBit/s.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: bbaf4d490fcebb4cd741a9b83ffc5d7e85699755
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61224343"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Verwenden von Azure Media Encoder Standard zum automatischen Generieren einer Reihe von Bitraten  

## <a name="overview"></a>Übersicht

In diesem Artikel wird gezeigt, wie Sie mit Media Encoder Standard (MES) automatisch eine Reihe von Bitraten/Auflösungs-Paaren auf Basis der eingegebenen Auflösung und Bitrate generieren können. Die automatisch generierte Voreinstellung wird nie die eingegebene Auflösung und Bitrate übersteigen. Ist die Eingabe beispielsweise 720p bei 3 MBit/s, bleibt die Ausgabe bestenfalls bei 720p, und es beginnt mit niedrigeren Raten als 3 MBit/s.

### <a name="encoding-for-streaming-only"></a>Codierung nur für Streaming

Wenn Sie Ihr Quellvideo nur für das Streamen codieren möchten, verwenden Sie die Voreinstellung „Adaptives Streaming“, wenn Sie eine Codierungsaufgabe erstellen. Bei Verwendung der Voreinstellung **Adaptives Streaming** begrenzt der MES-Encoder eine Reihe von Bitraten/Auflösungs-Paaren intelligent. Allerdings können Sie die Codierungskosten nicht steuern, da der Dienst bestimmt, wie viele Ebenen bei welcher Auflösung verwendet werden. Am Ende dieses Artikels finden Sie Beispiele von Ausgabeebenen, die von MES als Ergebnis der Codierung mit der Voreinstellung **Adaptives Streaming** erzeugt wurden. Das Ausgabeasset enthält MP4-Dateien, wo Audio- und Videodaten sich nicht überlappen.

### <a name="encoding-for-streaming-and-progressive-download"></a>Codieren für das Streamen und den progressiven Download

Wenn Sie Ihr Quellvideo für das Streamen sowie für das Erstellen von MP4-Dateien für den progressiven Download codieren möchten, sollten Sie die Voreinstellung „Content Adaptive Multiple Bitrate MP4“ (Für den Inhalt angepasste Content Adaptive multiple Bitrate MP4) verwenden, wenn Sie eine Codierungsaufgabe erstellen. Wenn Sie die Voreinstellung **Content Adaptive Multiple Bitrate MP4** verwenden, wendet der MES-Encoder die gleiche Codierungslogik wie oben an. Nun enthält aber das Ausgabeasset MP4-Dateien, wobei sich Audio und Video überlappen. Sie können eine dieser MP4-Dateien als progressive Download-Datei verwenden (z.B. die Version mit der höchsten Bitrate).

## <a id="encoding_with_dotnet"></a>Codierung mit dem Media Services .NET SDK

Im folgenden Codebeispiel wird das Media Services-.NET-SDK verwendet, um die folgenden Aufgaben auszuführen:

- Erstellen eines Codierungsauftrags.
- Abrufen eines Verweises auf den Media Encoder Standard-Encoder
- Fügen Sie dem Auftrag eine Codierungsaufgabe hinzu, und geben Sie die Verwendung der Voreinstellung **Adaptives Streaming** an. 
- Erstellen Sie ein Ausgabemedienobjekt, das das codierte Medienobjekt enthält.
- Fügen Sie einen Ereignishandler hinzu, um den Auftragsstatus zu überprüfen.
- Übermitteln des Auftrags.

#### <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 

#### <a name="example"></a>Beispiel

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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
                    break;
                default:
                    break;
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
    }
}
```

## <a id="output"></a>Ausgabe

Dieser Abschnitt zeigt drei Beispiele von Ausgabeebenen, die von MES als Ergebnis der Codierung mit der Voreinstellung **Adaptives Streaming** erzeugt wurden. 

### <a name="example-1"></a>Beispiel 1
Die Quelle mit der Höhe „1080“ und der Bildrate „29,970“ produziert 6 Videoebenen:

|Ebene|Höhe|Breite|Bitrate(KBit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>Beispiel 2
Die Quelle mit der Höhe „720“ und der Bildrate „23,970“ produziert 5 Videoebenen:

|Ebene|Höhe|Breite|Bitrate(KBit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>Beispiel 3
Die Quelle mit der Höhe „360“ und der Bildrate „29,970“ produziert 3 Videoebenen:

|Ebene|Höhe|Breite|Bitrate(KBit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Siehe auch
[Media Services-Codierung (Übersicht)](media-services-encode-asset.md)

