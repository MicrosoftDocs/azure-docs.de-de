---
title: Generieren von Miniaturansichten mithilfe von Media Encoder Standard mit .NET
description: In diesem Thema wird gezeigt, wie Sie .NET verwenden, um mithilfe von Media Encoder Standard gleichzeitig ein Medienobjekt zu codieren und Miniaturansichten zu generieren.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61244228"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Generieren von Miniaturansichten mithilfe von Media Encoder Standard mit .NET 

Sie können Media Encoder Standard verwenden, um eine oder mehrere Miniaturansichten aus den Eingangsvideodaten in den Bilddateiformaten [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) oder [BMP](https://en.wikipedia.org/wiki/BMP_file_format) zu generieren. Sie können Aufgaben einreichen, die nur Bilder erzeugen, oder Sie können das Erstellen von Miniaturansichten mit der Codierung kombinieren. Dieser Artikel enthält einige Beispiele für XML- und JSON-Miniaturansichtsvoreinstellungen für solche Szenarien. Am Ende des Artikels wird ein [Beispielcode](#code_sample) aufgeführt, der zeigt, wie das Media Services .NET SDK zum Ausführen der Codierungsaufgabe verwendet werden kann.

Weitere Informationen zu den Elementen, die in Beispielvoreinstellungen verwendet werden, finden Sie unter [Media Encoder Standard-Schema](media-services-mes-schema.md).

Lesen Sie unbedingt den Abschnitt [Überlegungen](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) .
    
## <a name="example-of-a-single-png-file-preset"></a>Beispiel für die Voreinstellung „Einzelne PNG-Datei“

Die folgende JSON- und XML-Voreinstellung kann verwendet werden, um eine einzelne PNG-Ausgabedatei von den ersten Sekunden der Eingangsvideodaten zu erzeugen, indem der Encoder einen Versuch zum Suchen eines „interessanten“ Frames macht. Beachten Sie, dass die Ausgabebilddimensionen auf 100 % festgelegt wurden, d. h. sie entsprechen den Dimensionen der Eingangsvideodaten. Beachten Sie außerdem, dass die Einstellung „Format“ unter „Ausgaben“ erforderlich ist, damit die Verwendung von „PngLayers“ im Abschnitt „Codecs“ übereinstimmt. 

### <a name="json-preset"></a>JSON-Voreinstellung

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML-Voreinstellung

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Beispiel für die Voreinstellung „Eine Reihe von JPEG-Bildern“

Die folgende JSON und XML-Voreinstellung kann verwendet werden, um einen Satz von 10 Bildern an den Zeitstempeln 5%, 15%,..., 95% der Eingabezeitachse zu erstellen, wobei die Bildgröße auf ein Viertel der Eingangsvideodaten festgelegt ist.

### <a name="json-preset"></a>JSON-Voreinstellung

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-Voreinstellung
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Beispiel für die Voreinstellung „Ein Bild zu einem bestimmten Zeitstempel“

Die folgende JSON und XML-Voreinstellung kann verwendet werden, um ein einzelnes JPEG-Bild an der 30-Sekunden-Marke der Eingangsvideodaten zu erzeugen. Diese Voreinstellung geht von einer Eingangsvideodatenlänge von mehr als 30 Sekunden aus (andernfalls tritt beim Auftrag ein Fehler auf).

### <a name="json-preset"></a>JSON-Voreinstellung

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-Voreinstellung
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Beispiel für die Voreinstellung „Miniaturansichten in unterschiedlichen Auflösungen“

Mithilfe der folgenden Voreinstellung können Miniaturansichten in unterschiedlichen Auflösungen in einer Aufgabe generiert werden. In dem Beispiel generiert der Encoder bei den Positionen 5 % bis 95 % (in 10 %-Schritten, z. B. 5 %, 15 % usw.) der Eingabezeitachse zwei Bilder – ein Bild mit 100 % der Auflösung des Eingangsvideos und ein weiteres Bild mit einer Auflösung von 50 %.

Beachten Sie die Verwendung des Makros {Resolution} im Element „FileName“. Dieses gibt an, dass der Encoder die Breite und Höhe verwendet, die Sie beim Generieren des Dateinamens der Ausgabebilder im Abschnitt „Codierung“ der Voreinstellung angegeben haben. Dies hilft Ihnen zudem dabei, mühelos die verschiedenen Bildern zu unterscheiden.

### <a name="json-preset"></a>JSON-Voreinstellung

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-Voreinstellung
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Beispiel für das Generieren einer Miniaturansicht bei der Codierung

In allen oben genannten Beispielen wurde erläutert, dass Sie eine Codierungsaufgabe, die nur Bilder erzeugt, übermitteln können. Jedoch ist die Video- bzw. Audiocodierung auch beim Generieren von Miniaturansichten möglich. Die folgende JSON- und XML-Voreinstellung weist **Media Encoder Standard** an, bei der Codierung eine Miniaturansicht zu generieren.

### <a id="json"></a>JSON-Voreinstellung
Informationen zum Schema finden Sie in [diesem](https://msdn.microsoft.com/library/mt269962.aspx) Artikel.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a id="xml"></a>XML-Voreinstellung
Informationen zum Schema finden Sie in [diesem](https://msdn.microsoft.com/library/mt269962.aspx) Artikel.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a id="code_sample"></a>Codieren eines Videos und Generieren einer Miniaturansicht mit .NET

Im folgenden Codebeispiel wird das Media Services-.NET-SDK verwendet, um die folgenden Aufgaben auszuführen:

* Erstellen eines Codierungsauftrags.
* Abrufen eines Verweises auf den Media Encoder Standard-Encoder
* Laden Sie die [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml)- oder [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)-Voreinstellung, die die Codierungsvoreinstellung sowie zum Generieren von Miniaturansichten erforderliche Informationen enthält. Sie können diese [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml)- oder [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json)-Voreinstellung in einer Datei speichern und die Datei mithilfe des folgenden Codes laden.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Fügen Sie eine einzelne Codierungsaufgabe zum Auftrag hinzu. 
* Geben Sie das zu codierende Asset an.
* Erstellen Sie ein Ausgabemedienobjekt, das das codierte Medienobjekt enthält.
* Fügen Sie einen Ereignishandler hinzu, um den Auftragsstatus zu überprüfen.
* Übermitteln des Auftrags.

Im Artikel [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md) erfahren Sie, wie Sie Ihre Entwicklungsumgebung einrichten.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>Überlegungen
Es gelten die folgenden Bedingungen:

* Bei der Verwendung von expliziten Zeitstempeln für "Start"/"Step"/"Range" wird davon ausgegangen, dass die Dauer der Eingabequelle mindestens 1 Minute beträgt.
* Jpg-/Png-/BmpImage-Elemente weisen Start-, Step- und Range-Zeichenfolgenattribute auf, die folgendermaßen interpretiert werden können:
  
  * Framenummer, wenn es sich nicht um negative Integers handelt, z.B. "Start": "120",
  * Relativ zur Quelldauer bei Ausdrücken mit dem Suffix „%“, z.B. "Start": "15%", ODER
  * Zeitstempel bei Ausdrücken im HH:MM:SS...- Format. Beispiel: "Start" : "00:01:00"
    
    Sie können die Formate nach Belieben mischen.
    
    „Start“ unterstützt darüber hinaus auch das spezielle Makro {Best}, das versucht, den ersten „interessanten“ Frame des Inhalts zu ermitteln. (HINWEIS: „Step“ und „Range“ werden ignoriert, wenn „Start“ auf „{Best}“ festgelegt ist.)
  * Standardwerte: Start: {Best}
* Das Ausgabeformat muss für jedes Bildformat ausdrücklich bereitgestellt werden: „Jpg“/„Png“/„BmpFormat“. Falls vorhanden, ordnet MES „JpgVideo“ zu „JpgFormat“ usw. zu. "OutputFormat" führt ein neues Imagecodec-spezifisches Makro ein: "{Index}". Dieses Makro muss für Bildausgabeformate vorhanden sein (genau einmal).

## <a name="next-steps"></a>Nächste Schritte

Sie können den [Status des Auftrags](media-services-check-job-progress.md) überprüfen, während der Codierungsauftrag aussteht.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Siehe auch
[Media Services-Codierung (Übersicht)](media-services-encode-asset.md)

