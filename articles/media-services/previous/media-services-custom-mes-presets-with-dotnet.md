---
title: Anpassen von Media Encoder Standard-Voreinstellungen | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie die erweiterte Codierung durch Anpassen der Aufgabenvoreinstellungen von Media Encoder Standard ausführen. In diesem Thema wird die Verwendung des Media Services .NET SDK zum Erstellen einer Aufgabe und eines Auftrags für die Codierung erläutert. Darüber hinaus wird veranschaulicht, wie Sie benutzerdefinierte Voreinstellungen für den Codierungsauftrag angeben.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 39a1dd5c3d26eeb6545a96aa35f9457bd9859c21
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61247242"
---
# <a name="customizing-media-encoder-standard-presets"></a>Anpassen von Media Encoder Standard-Voreinstellungen  

## <a name="overview"></a>Übersicht

In diesem Artikel wird erläutert, wie Sie die erweiterten Codierungsaufgaben mithilfe von Media Encoder-Standard (MES) mit einer benutzerdefinierten Voreinstellung ausführen. In diesem Artikel wird .NET verwendet, um eine Codierungsaufgabe und einen Auftrag zu erstellen, der diese Aufgabe ausführt.  

In diesem Artikel erfahren Sie, wie Sie eine Voreinstellung anpassen, indem Sie die Anzahl der Ebenen in der Voreinstellung [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) reduzieren. Der Artikel [Erweiterte Codierung mit Media Encoder Standard-Voreinstellungen](media-services-advanced-encoding-with-mes.md) zeigt benutzerdefinierte Voreinstellungen, die verwendet werden können, um erweiterte Codierungsaufgaben auszuführen.

> [!NOTE]
> Die in diesem Artikel beschriebenen benutzerdefinierten Voreinstellungen können nicht in [Media Services V3](https://docs.microsoft.com/azure/media-services/latest/)-Transformationen oder in den Befehlen der Befehlszeilenschnittstelle verwendet werden. Weitere Informationen finden Sie unter [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

## <a id="customizing_presets"></a> Anpassen einer MES-Voreinstellung

### <a name="original-preset"></a>Ursprüngliche Voreinstellung

Speichern Sie das im Artikel [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) definierte JSON-Objekt in einer Datei mit der Erweiterung „.json“. Beispiel: **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Benutzerdefinierte Voreinstellung

Öffnen Sie die Datei **CustomPreset_JSON.json**, und entfernen Sie die ersten drei Ebenen von **H264Layers**, sodass die Datei wie folgt aussieht.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a id="encoding_with_dotnet"></a>Codierung mit dem Media Services .NET SDK

Im folgenden Codebeispiel wird das Media Services-.NET-SDK verwendet, um die folgenden Aufgaben auszuführen:

- Erstellen eines Codierungsauftrags.
- Abrufen eines Verweises auf den Media Encoder Standard-Encoder
- Laden Sie die benutzerdefinierte JSON-Voreinstellung, die Sie im vorherigen Abschnitt erstellt haben. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- Hinzufügen einer Codierungsaufgabe zum Auftrag 
- Geben Sie das zu codierende Medienobjekt an.
- Erstellen Sie ein Ausgabemedienobjekt, das das codierte Medienobjekt enthält.
- Fügen Sie einen Ereignishandler hinzu, um den Auftragsstatus zu überprüfen.
- Übermitteln des Auftrags.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

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

            // Encode and generate the output using custom presets.
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

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
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

## <a name="see-also"></a>Weitere Informationen

- [Codieren einer benutzerdefinierten Transformation mithilfe der Befehlszeilenschnittstelle](../latest/custom-preset-cli-howto.md)
- [Codierung mit Media Services (v3)](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
