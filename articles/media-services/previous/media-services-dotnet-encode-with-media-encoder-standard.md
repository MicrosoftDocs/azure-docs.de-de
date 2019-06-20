---
title: Codieren eines Medienobjekts mit Media Encoder Standard mit .NET | Microsoft-Dokumentation
description: In diesem Thema wird veranschaulicht, wie Sie .NET verwenden, um ein Medienobjekt mithilfe von Media Encoder Standard zu codieren.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;anilmur
ms.openlocfilehash: d3eb2affe76374eb35ac724dff0204f43b567e09
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61225699"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Codieren eines Medienobjekts mit Media Encoder Standard mit .NET  

Die Codierung ist einer der häufigsten Verarbeitungsvorgänge in Media Services. Sie erstellen Codierungsaufträge, um Mediendateien von einer Codierung in eine andere zu konvertieren. Zum Codieren können Sie den in Media Services integrierten Media Encoder verwenden. Sie können auch einen Encoder von Media Services-Partnern verwenden. Encoder von Drittanbietern finden Sie im Azure Marketplace. 

In diesem Artikel wird veranschaulicht, wie Sie .NET verwenden, um Medienobjekte mithilfe von Media Encoder Standard (MES) zu codieren. Media Encoder Standard wird mithilfe von Encoder-Voreinstellungen konfiguriert, die [hier](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)beschrieben sind.

Es wird empfohlen, Quelldateien immer in einen MP4-Satz mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung](media-services-dynamic-packaging-overview.md)in das gewünschte Format zu konvertieren. 

Wenn Ihr Ausgabemedienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES erzeugt eine Ausgabedatei mit einem Namen, der die ersten 32 Zeichen des Namens der Eingabedatei enthält. Der Name basiert auf der Angabe in der Voreinstellungsdatei. Beispiel: "FileName": "{Basename}_{Index}{Extension}". {Basename} wird durch die ersten 32 Zeichen des Namens der Eingabedatei ersetzt.
> 
> 

### <a name="mes-formats"></a>MES-Formate
[Formate und Codecs](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-Voreinstellungen
Media Encoder Standard wird mithilfe von Encoder-Voreinstellungen konfiguriert, die [hier](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)beschrieben sind.

### <a name="input-and-output-metadata"></a>Eingabe- und Ausgabemetadaten
Wenn Sie Eingabemedienobjekte mithilfe von MES codieren, erhalten Sie bei erfolgreichem Abschluss dieser Codieraufgabe ein Ausgabemedienobjekt. Das Ausgabeasset enthält Video- und Audioinhalte, Miniaturansichten, das Manifest usw. Dies hängt von den verwendeten Codierungsvoreinstellungen ab.

Das Ausgabeasset enthält auch eine Datei mit Metadaten zum Eingabeasset. Der Name der XML-Metadatendatei weist das folgende Format auf: <Medienobjekt-ID>_metadata.xml (z.B. „41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml“), wobei <Medienobjekt-ID> der „AssetId“-Wert des Eingabemedienobjekts ist. Das Schema dieser XML-Eingabemetadaten ist [hier](media-services-input-metadata-schema.md) beschrieben.

Das Ausgabeasset enthält auch eine Datei mit Metadaten zum Ausgabeasset. Der Name der XML-Metadatendatei weist das folgende Format auf: <Quelldateiname>_manifest.xml (z.B. BigBuckBunny_manifest.xml). Das Schema dieser XML-Ausgabemetadaten ist [hier](media-services-output-metadata-schema.md) beschrieben.

Wenn Sie eine der beiden Metadatendateien untersuchen möchten, können Sie einen SAS-Locator erstellen und die Datei auf Ihren lokalen Computer herunterladen. Ein Beispiel zum Erstellen eines SAS-Locators mit der Möglichkeit zum Herunterladen der Datei finden Sie in den .NET SDK-Erweiterungen für Media Services.

## <a name="download-sample"></a>Beispiel herunterladen
Sie können [hier](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/) ein Beispiel abrufen und ausführen, das veranschaulicht, wie Sie mit MES codieren.

## <a name="net-sample-code"></a>.NET-Beispielcode

Im folgenden Codebeispiel wird das Media Services-.NET-SDK verwendet, um die folgenden Aufgaben auszuführen:

* Erstellen eines Codierungsauftrags.
* Abrufen eines Verweises auf den Media Encoder Standard-Encoder
* Angeben der Voreinstellung [Adaptives Streaming](media-services-autogen-bitrate-ladder-with-mes.md). 
* Fügen Sie eine einzelne Codierungsaufgabe zum Auftrag hinzu. 
* Geben Sie das zu codierende Asset an.
* Erstellen Sie ein Ausgabemedienobjekt, das das codierte Medienobjekt enthält.
* Fügen Sie einen Ereignishandler hinzu, um den Auftragsstatus zu überprüfen.
* Übermitteln des Auftrags.

#### <a name="create-and-configure-a-visual-studio-project"></a>Erstellen und Konfigurieren eines Visual Studio-Projekts

Richten Sie Ihre Entwicklungsumgebung ein, und füllen Sie die Datei „app.config“ mit Verbindungsinformationen, wie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) beschrieben. 

#### <a name="example"></a>Beispiel 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

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

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
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

## <a name="advanced-encoding-features-to-explore"></a>Erweiterte Codierungsfeatures
* [Generieren von Miniaturansichten](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generieren von Miniaturansichten während der Codierung](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Zuschneiden von Videos während der Codierung](media-services-crop-video.md)
* [Anpassen von Codierungsvoreinstellungen](media-services-custom-mes-presets-with-dotnet.md)
* [Überlagern eines Videos mit einem Bild oder Versehen eines Videos mit einem Wasserzeichen](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
[Erstellen von Miniaturansichten über Media Encoder Standard mit .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services-Codierung (Übersicht)](media-services-encode-asset.md)

