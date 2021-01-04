---
title: Erweiterte Codierung mit Media Encoder Standard-Voreinstellungen | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie die erweiterte Codierung durch Anpassen der Aufgabenvoreinstellungen von Media Encoder Standard ausführen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 4288e2e955f8205f3b6551c83a5c883eecf02501
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021145"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Erweiterte Codierung mit Media Encoder Standard-Voreinstellungen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Übersicht

In diesem Thema wird das Anpassen von Media Encoder Standard-Voreinstellungen gezeigt. Das Thema [Anpassen von Media Encoder Standard-Voreinstellungen](media-services-custom-mes-presets-with-dotnet.md) zeigt, wie Sie .NET verwenden können, um eine Codierungsaufgabe zu erstellen sowie einen Auftrag, der diese Aufgabe ausführt. Wenn Sie eine Voreinstellung anpassen, übergeben Sie die benutzerdefinierten Voreinstellungen der Codierungsaufgabe. 

Stellen Sie bei der Verwendung einer XML-Voreinstellung sicher, dass Sie die Reihenfolge der Elemente beibehalten, wie in den untenstehenden XML-Beispielen gezeigt wird (z.B. KeyFrameInterval sollte SceneChangeDetection voranstehen).

> [!NOTE] 
> Viele der erweiterten Features von Media Services v2 des Media Encoder Standard sind derzeit in v3 nicht verfügbar. Weitere Informationen finden Sie unter [Featurelücken](../latest/media-services-v2-vs-v3.md#feature-gaps-with-respect-to-v2-apis).

## <a name="support-for-relative-sizes"></a>Unterstützung relativer Größen

Beim Generieren von Miniaturansichten müssen Sie nicht immer die Breite und Höhe der Ausgabe in Pixel angeben. Sie können sie in Prozent im Bereich [1 %, ..., 100 %] angeben.

### <a name="json-preset"></a>JSON-Voreinstellung

```json
"Width": "100%",
"Height": "100%"
```

### <a name="xml-preset"></a>XML-Voreinstellung

```xml
<Width>100%</Width>
<Height>100%</Height>
```

## <a name="generate-thumbnails"></a>Generieren von Miniaturansichten

In diesem Abschnitt erfahren Sie, wie Sie eine Voreinstellung anpassen, die Miniaturansichten generiert. Die unten definierte Voreinstellung enthält Informationen zum Codieren Ihrer Datei sowie die erforderlichen Informationen zum Generieren von Miniaturansichten. Sie können alle in [diesem](media-services-mes-presets-overview.md) Abschnitt dokumentierten MES-Voreinstellungen verwenden und Code hinzufügen, mit dem Miniaturansichten generiert werden.  

> [!NOTE]
> Die Einstellung **SceneChangeDetection** in der folgenden Voreinstellung kann nur auf „true“ festgelegt werden, wenn eine Codierung als Single-Bitrate-Video erfolgt. Wenn eine Codierung als Video mit variabler Bitrate erfolgt und **SceneChangeDetection** auf TRUE festgelegt ist, gibt der Encoder einen Fehler zurück.  
>
>

Informationen zum Schema finden Sie in [diesem](media-services-mes-schema.md) Thema.

Lesen Sie unbedingt den Abschnitt [Überlegungen](#considerations) .

### <a name="json-preset"></a>JSON-Voreinstellung

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
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "PngLayers": [
        {
          "Type": "PngLayer",
          "Width": 640,
          "Height": 360,
        }
      ],
      "Start": "00:00:01",
      "Step": "00:00:10",
      "Range": "00:00:58",
      "Type": "PngImage"
    },
    {
      "BmpLayers": [
        {
          "Type": "BmpLayer",
          "Width": 640,
          "Height": 360
        }
      ],
      "Start": "10%",
      "Step": "10%",
      "Range": "90%",
      "Type": "BmpImage"
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
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "PngFormat"
      }
    },
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "BmpFormat"
      }
    },
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
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
          <Width>640</Width>
          <Height>360</Height>
          <Quality>90</Quality>
        </JpgLayer>
      </JpgLayers>
    </JpgImage>
    <BmpImage Start="10%" Step="10%" Range="90%">
      <BmpLayers>
        <BmpLayer>
          <Width>640</Width>
          <Height>360</Height>
        </BmpLayer>
      </BmpLayers>
    </BmpImage>
    <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
      <PngLayers>
        <PngLayer>
          <Width>640</Width>
          <Height>360</Height>
        </PngLayer>
      </PngLayers>
    </PngImage>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <JpgFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <BmpFormat />
    </Output>
    <Output FileName="{Basename}_{Index}{Extension}">
      <PngFormat />
    </Output>
  </Outputs>
</Preset>
```

### <a name="considerations"></a>Überlegungen

Es gelten die folgenden Bedingungen:

* Bei der Verwendung von expliziten Zeitstempeln für "Start"/"Step"/"Range" wird davon ausgegangen, dass die Dauer der Eingabequelle mindestens 1 Minute beträgt.
* Jpg-/Png-/BmpImage-Elemente weisen Start-, Step- und Range-Zeichenfolgenattribute auf, die folgendermaßen interpretiert werden können:

  * Framenummer, wenn es sich nicht um negative Integers handelt, z.B. "Start": "120",
  * Relativ zur Quelldauer bei Ausdrücken mit dem Suffix „%“, z.B. "Start": "15%", ODER
  * Zeitstempel bei Ausdrücken im HH:MM:SS...- Format, z.B. "Start" : "00:01:00"

    Sie können die Formate nach Belieben mischen.

    „Start“ unterstützt darüber hinaus auch das spezielle Makro {Best}, das versucht, den ersten „interessanten“ Frame des Inhalts zu ermitteln. (HINWEIS: „Step“ und „Range“ werden ignoriert, wenn „Start“ auf „{Best}“ festgelegt ist.)
  * Standardwerte: Start: {Best}
* Das Ausgabeformat muss für jedes Bildformat ausdrücklich bereitgestellt werden: „Jpg“/„Png“/„BmpFormat“. Falls vorhanden, ordnet MES „JpgVideo“ zu „JpgFormat“ usw. zu. "OutputFormat" führt ein neues Imagecodec-spezifisches Makro ein: "{Index}". Dieses Makro muss für Bildausgabeformate vorhanden sein (genau einmal).

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Kürzen eines Videos (Clipping)
Dieser Abschnitt befasst sich mit dem Ändern der Encoder-Voreinstellungen zum Beschneiden oder Kürzen des Eingabevideos, wenn es sich bei der Eingabe um eine sogenannte Zwischendatei (Mezzanine File) oder bedarfsgesteuerte Datei handelt. Der Encoder kann darüber hinaus zum Beschneiden oder Kürzen eines Medienobjekts verwendet werden, das aus einem Livedatenstrom erfasst oder archiviert wird. Ausführliche Informationen hierzu finden Sie in [diesem Blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Zum Kürzen Ihrer Videos können Sie alle in [diesem](media-services-mes-presets-overview.md) Abschnitt dokumentierten MES-Voreinstellungen verwenden und das **Sources**-Element (wie unten gezeigt) ändern. Der Wert von „StartTime“ muss mit den absoluten Zeitstempeln des Eingabevideos übereinstimmen. Wenn z. B. der erste Frame des Eingabevideos den Zeitstempel 12:00:10.000 trägt, sollte „StartTime“ mindestens 12:00:10.000 betragen. Im folgenden Beispiel wird davon ausgegangen, dass das Eingabevideo den Startzeitstempel 0 trägt. **Sources** muss am Beginn der Voreinstellung platziert werden.

### <a name="json-preset"></a><a id="json"></a>JSON-Voreinstellung

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "StartTime": "00:00:04",
      "Duration": "00:00:16"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "StretchMode": "AutoSize",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1500,
          "MaxBitrate": 1500,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 540,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
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

### <a name="xml-preset"></a>XML-Voreinstellung
Zum Kürzen Ihrer Videos können Sie alle [hier](media-services-mes-presets-overview.md) dokumentierten MES-Voreinstellungen verwenden und das **Sources** -Element (wie unten gezeigt) ändern.

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source StartTime="PT4S" Duration="PT14S"/>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>3400</Bitrate>
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
          <MaxBitrate>3400</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>2250</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2250</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1500</Bitrate>
          <Width>960</Width>
          <Height>540</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1500</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>1000</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1000</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>650</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>650</MaxBitrate>
        </H264Layer>
        <H264Layer>
          <Bitrate>400</Bitrate>
          <Width>320</Width>
          <Height>180</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>3</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cabac</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>400</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>128</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="create-an-overlay"></a><a id="overlay"></a>Erstellen einer Überlagerung

Media Encoder Standard ermöglicht die Überlagerung eines Bildes mit einem vorhandenen Video. Derzeit werden die folgenden Formate unterstützt: png, jpg, gif und bmp. Die unten definierte Voreinstellung ist ein einfaches Beispiel einer Videoüberlagerung.

Zusätzlich zur Definition einer Voreinstellungsdatei müssen Sie Media Services darüber informieren, welche Datei im Asset das Überlagerungsbild und welche Datei das Quellvideo darstellt, das mit dem Bild überlagert werden soll. Die Videodatei muss die **primäre** Datei sein.

Bei Verwendung von .NET fügen Sie die folgenden zwei Funktionen dem in [diesem](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) Thema definierten .NET-Beispiel hinzu. Die **UploadMediaFilesFromFolder**-Funktion lädt Dateien aus einem Ordner hoch (z.B. „BigBuckBunny.mp4“ und „Image001.png“) und legt die MP4-Datei als primäre Datei im Asset fest. Die **EncodeWithOverlay**-Funktion verwendet die übergebene benutzerdefinierte Voreinstellungsdatei (z.B. die darauf folgende Voreinstellung) zum Erstellen des Codierungstasks.

```csharp
static public IAsset UploadMediaFilesFromFolder(string folderPath)
{
    IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);

    foreach (var af in asset.AssetFiles)
    {
        // The following code assumes 
        // you have an input folder with one MP4 and one overlay image file.
        if (af.Name.Contains(".mp4"))
            af.IsPrimary = true;
        else
            af.IsPrimary = false;

        af.Update();
    }

    return asset;
}

static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
{
    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
    // Get a media processor reference, and pass to it the name of the 
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(customPresetFileName);

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input assets to be encoded.
    // This asset contains a source file and an overlay file.
    task.InputAssets.Add(assetSource);

    // Add an output asset to contain the results of the job. 
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

    return job.OutputMediaAssets[0];
}
```

> [!NOTE]
> Aktuelle Einschränkungen:
>
> Die Einstellung für die Deckkraft der Überlagerung wird nicht unterstützt.
>
> Ihre Quellvideodatei und die Überlagerungsbilddatei müssen sich im gleichen Medienobjekt befinden, und die Videodatei muss in diesem Medienobjekt als primäre Datei festgelegt sein.
>
>

### <a name="json-preset"></a>JSON-Voreinstellung

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "VideoOverlay": {
          "Position": {
            "X": 100,
            "Y": 100,
            "Width": 100,
            "Height": 50
          },
          "AudioGainLevel": 0.0,
          "MediaParams": [
            {
              "OverlayLoopCount": 1
            },
            {
              "IsOverlay": true,
              "OverlayLoopCount": 1
            }
          ],
          "Source": "Image001.png",
          "Clip": {
            "Duration": "00:00:05"
          },
          "FadeInDuration": {
            "Duration": "00:00:01"
          },
          "FadeOutDuration": {
            "StartTime": "00:00:03",
            "Duration": "00:00:04"
          }
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1045,
          "MaxBitrate": 1045,
          "BufferWindow": "00:00:05",
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Type": "CopyAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}{Extension}",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="xml-preset"></a>XML-Voreinstellung

```xml
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Sources>
    <Source>
      <Streams />
      <Filters>
        <VideoOverlay>
          <Source>Image001.png</Source>
          <Clip Duration="PT5S" />
          <FadeInDuration Duration="PT1S" />
          <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
          <Position X="100" Y="100" Width="100" Height="50" />
          <Opacity>0</Opacity>
          <AudioGainLevel>0</AudioGainLevel>
          <MediaParams>
            <MediaParam>
              <IsOverlay>false</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
            <MediaParam>
              <IsOverlay>true</IsOverlay>
              <OverlayLoopCount>1</OverlayLoopCount>
            </MediaParam>
          </MediaParams>
        </VideoOverlay>
      </Filters>
      <Pad>true</Pad>
    </Source>
  </Sources>
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:02</KeyFrameInterval>
      <H264Layers>
        <H264Layer>
          <Bitrate>1045</Bitrate>
          <Width>640</Width>
          <Height>360</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Auto</Profile>
          <Level>auto</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>true</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>1045</MaxBitrate>
        </H264Layer>
      </H264Layers>
    </H264Video>
    <CopyAudio />
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}{Extension}">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```

## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Einfügen einer stillen Audiospur bei einer Eingabe ohne Audio
Wenn Sie eine Eingabe an den Encoder senden, die keine Audiodaten, sondern nur Videodaten enthält, besteht das Ausgabemedienobjekt standardmäßig nur aus Dateien mit Videodaten. Einige Player können derartige Ausgabedatenströme möglicherweise nicht verarbeiten. Mit dieser Einstellung können Sie den Encoder zwingen, der Ausgabe in diesem Szenario eine stille Audiospur hinzuzufügen.

Um zu erzwingen, dass der Encoder ein Asset erstellt, das bei einer Eingabe ohne Audio eine stille Audiospur enthält, geben Sie den Wert „InsertSilenceIfNoAudio“ an.

Sie können alle in [diesem](media-services-mes-presets-overview.md) Abschnitt dokumentierten MES-Voreinstellungen verwenden und folgende Änderung vornehmen:

### <a name="json-preset"></a>JSON-Voreinstellung

```json
{
  "Channels": 2,
  "SamplingRate": 44100,
  "Bitrate": 96,
  "Type": "AACAudio",
  "Condition": "InsertSilenceIfNoAudio"
}
```

### <a name="xml-preset"></a>XML-Voreinstellung

```xml
<AACAudio Condition="InsertSilenceIfNoAudio">
  <Channels>2</Channels>
  <SamplingRate>44100</SamplingRate>
  <Bitrate>96</Bitrate>
</AACAudio>
```

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Deaktivieren des automatischen Deinterlacings
Kunden müssen nichts tun, wenn das Interlacing der Interlace-Inhalte automatisch aufgehoben werden soll. Bei Aktivierung des automatischen De-Interlacings (Standard) übernimmt MWS die automatische Erkennung von Frames mit Zeilensprung und hebt das Interlacing nur für Frames auf, die als Interlaced markiert sind.

Sie können das automatische De-Interlacing deaktivieren. Diese Option wird nicht empfohlen.

### <a name="json-preset"></a>JSON-Voreinstellung

```json
"Sources": [
  {
    "Filters": {
      "Deinterlace": {
        "Mode": "Off"
      }
    },
  }
]
```

### <a name="xml-preset"></a>XML-Voreinstellung

```xml
<Sources>
  <Source>
    <Filters>
      <Deinterlace>
        <Mode>Off</Mode>
      </Deinterlace>
    </Filters>
  </Source>
</Sources>
```

## <a name="audio-only-presets"></a><a id="audio_only"></a>Nur Audio-Voreinstellungen
In diesem Abschnitt werden zwei Nur-Audio-MES-Voreinstellungen demonstriert: AAC Audio und AAC Good Quality Audio.

### <a name="aac-audio"></a>AAC Audio

```json
{
  "Version": 1.0,
  "Codecs": [
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
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

### <a name="aac-good-quality-audio"></a>AAC Good Quality Audio

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Verketten von zwei oder mehr Videodateien

Das folgende Beispiel veranschaulicht, wie Sie eine Voreinstellung generieren, um zwei oder mehr Videodateien zu verketten. Das häufigste Szenario hierfür ist das Hinzufügen eines Vor- oder Nachspanns zum Hauptvideo. er Zweck ist, dass die Videodateien, die gemeinsam bearbeitet werden, über die gleichen Eigenschaften verfügen (Videoauflösung, Framerate, Anzahl von Audiospuren usw.). Sie sollten darauf achten, Videos mit verschiedenen Frameraten oder einer unterschiedlichen Anzahl von Audiospuren nicht gemeinsam zu verwenden.

>[!NOTE]
>Im aktuellen Design des Verkettungsfeatures wird davon ausgegangen, dass die Eingabevideoclips bezüglich Auflösung, Framerate usw. einheitlich sind. 

### <a name="requirements-and-considerations"></a>Anforderungen und Überlegungen

* Eingabevideos sollten nur eine Audiospur aufweisen.
* Alle Eingabevideos sollten die gleiche Framerate aufweisen.
* Sie müssen Ihre Videos in separate Medienobjekte hochladen und die Videos in jedem Medienobjekt als primäre Datei festlegen.
* Sie müssen die Länge der Videos kennen.
* Bei den unten gezeigten Voreinstellungsbeispielen wird davon ausgegangen, dass alle Eingabevideos mit dem Zeitstempel 0 starten. Wenn die Videos unterschiedliche Startzeitstempel aufweisen, was bei Live-Archiven häufig vorkommt, müssen Sie die StartTime-Werte ändern.
* Die JSON-Voreinstellung verweist explizit auf die AssetID-Werte der Eingabemedienobjekte.
* Im Beispielcode wird davon ausgegangen, dass die JSON-Voreinstellung in einer lokalen Datei gespeichert wurde, beispielsweise „C:\supportFiles\preset.json“. Es wird ebenfalls angenommen, dass durch Hochladen von zwei Videodateien zwei Medienobjekte erstellt wurden und dass Sie die resultierenden AssetID-Werte kennen.
* Der Codeausschnitt und die JSON-Voreinstellung zeigen ein Beispiel für das Verketten von zwei Videodateien. Sie können den Vorgang folgendermaßen auf mehr als zwei Videos erweitern:

  1. Wiederholtes Aufrufen von task.InputAssets.Add(), um weitere Videos in einer bestimmten Reihenfolge hinzuzufügen.
  2. Ändern des Sources-Elements in der JSON-Voreinstellung durch Hinzufügen weiterer Einträge in der gleichen Reihenfolge.

### <a name="net-code"></a>.NET code

```csharp
IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

// Declare a new job.
IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
// Get a media processor reference, and pass to it the name of the
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

// Load the XML (or JSON) from the local file.
string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

// Create a task
ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
    processor,
    configuration,
    TaskOptions.None);

// Specify the input videos to be concatenated (in order).
task.InputAssets.Add(asset1);
task.InputAssets.Add(asset2);
// Add an output asset to contain the results of the job.
// This output is specified as AssetCreationOptions.None, which
// means the output asset is not encrypted.
task.OutputAssets.AddNew("Output asset",
    AssetCreationOptions.None);

job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
job.Submit();
job.GetExecutionProgressTask(CancellationToken.None).Wait();
```

### <a name="json-preset"></a>JSON-Voreinstellung

Aktualisieren Sie Ihre benutzerdefinierte Voreinstellung mit den IDs der Medienobjekte, die Sie verketten möchten, und mit dem geeigneten Zeitsegment für jedes Video.

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
      "StartTime": "00:00:01",
      "Duration": "00:00:15"
    },
    {
      "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
      "StartTime": "00:00:02",
      "Duration": "00:00:05"
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Level": "auto",
          "Bitrate": 1800,
          "MaxBitrate": 1800,
          "BufferWindow": "00:00:05",
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "Width": "640",
          "Height": "360",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
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
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Zuschneiden von Videos mit Media Encoder Standard
Siehe das Thema [Zuschneiden von Videos mit Media Encoder Standard](media-services-crop-video.md) .

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Einfügen einer Videospur, wenn die Eingabe kein Video enthält

Wenn Sie eine Eingabe an den Encoder senden, die nur Audiodaten und keine Videodaten enthält, besteht das Ausgabemedienobjekt standardmäßig aus Dateien mit ausschließlich Audiodaten. Einige Player, einschließlich Azure Media Player (siehe [hier](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)), solche Streams möglicherweise nicht handhaben. Mit dieser Einstellung können Sie den Encoder zwingen, der Ausgabe in diesem Szenario eine monochrome Videospur hinzuzufügen.

> [!NOTE]
> Wenn der Encoder gezwungen wird, eine Ausgabevideospur einzufügen, erhöht sich die Größe des Ausgabemedienobjekts und dadurch die für die Codierungsaufgabe anfallenden Kosten. Führen Sie Tests aus, um sicherzustellen, dass diese resultierende Zunahme nur geringe Auswirkungen auf Ihre monatlichen Gebühren hat.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Einfügen von Videoinhalten mit ausschließlich der niedrigsten Bitrate

Angenommen, Sie verwenden eine Codierungsvoreinstellung mit mehreren Bitraten wie [H264 Multiple Bitrate 720p](media-services-mes-preset-h264-multiple-bitrate-720p.md) , um Ihren gesamten Eingabekatalog für das Streaming zu codieren, der eine Mischung aus Videodateien und reinen Audiodateien enthält. Wenn in diesem Szenario die Eingabe kein Video enthält, können Sie den Encoder zwingen, eine monochrome Videospur mit ausschließlich der niedrigsten Bitrate anstatt Video mit jeder Ausgabebitrate hinzuzufügen. Um dies zu erreichen, müssen Sie das Flag **InsertBlackIfNoVideoBottomLayerOnly** verwenden.

Sie können alle in [diesem](media-services-mes-presets-overview.md) Abschnitt dokumentierten MES-Voreinstellungen verwenden und folgende Änderung vornehmen:

#### <a name="json-preset"></a>JSON-Voreinstellung

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-Voreinstellung

Verwenden Sie bei der Verwendung von XML die Bedingung="InsertBlackIfNoVideoBottomLayerOnly" als Attribut zu dem Element **H264Video** und die Bedingung="InsertSilenceIfNoAudio" als Attribut zu **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Einfügen von Video mit allen Ausgabebitraten
Angenommen, Sie verwenden eine Codierungsvoreinstellung mit mehreren Bitraten wie [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) , um Ihren gesamten Eingabekatalog für das Streaming zu codieren, der eine Mischung aus Videodateien und reinen Audiodateien enthält. Wenn in diesem Szenario die Eingabe kein Video enthält, können Sie den Encoder zwingen, eine monochrome Videospur für alle Ausgabebitraten hinzuzufügen. Dadurch wird sichergestellt, dass alle Ausgabemedienobjekte in Bezug auf die Anzahl der Video- und Audiospuren homogen sind. Um dies zu erreichen, müssen Sie das Flag „InsertBlackIfNoVideo“ angeben.

Sie können alle in [diesem](media-services-mes-presets-overview.md) Abschnitt dokumentierten MES-Voreinstellungen verwenden und folgende Änderung vornehmen:

#### <a name="json-preset"></a>JSON-Voreinstellung

```json
{
  "KeyFrameInterval": "00:00:02",
  "StretchMode": "AutoSize",
  "Condition": "InsertBlackIfNoVideo",
  "H264Layers": [
    …
  ]
}
```

#### <a name="xml-preset"></a>XML-Voreinstellung

Verwenden Sie bei der Verwendung von XML die Bedingung="InsertBlackIfNoVideo" als Attribut zu dem Element **H264Video** und die Bedingung="InsertSilenceIfNoAudio" als Attribut zu **AACAudio**.

```xml
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Drehen eines Videos
Der [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) unterstützt Drehungen in den Winkeln 0/90/180/270 Grad. Das Standardverhalten ist „Auto“. Dabei wird versucht, die Rotationsmetadaten in der eingehenden Videodatei zu erkennen und auszugleichen. Schließen Sie das folgende **Sources**-Element in einer der in [diesem](media-services-mes-presets-overview.md) Abschnitt definierten JSON-Voreinstellungen ein:

### <a name="json-preset"></a>JSON-Voreinstellung

```json
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
  ],
  "Codecs": [

    ...
```

### <a name="xml-preset"></a>XML-Voreinstellung

```xml
<Sources>
  <Source>
    <Streams />
    <Filters>
      <Rotation>90</Rotation>
    </Filters>
  </Source>
</Sources>
```

In [diesem](media-services-mes-schema.md#PreserveResolutionAfterRotation) Thema erhalten Sie weitere Informationen dazu, wie der Encoder die Breiten- und Höheneinstellungen in der Voreinstellung interpretiert, wenn die Rotationskompensierung ausgelöst wird.

Mit dem Wert „0“ können Sie den Encoder anweisen, Rotationsmetadaten im Videoeingang zu ignorieren (falls vorhanden).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen
[Media Services-Codierung (Übersicht)](media-services-encode-asset.md)
