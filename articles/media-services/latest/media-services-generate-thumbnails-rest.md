---
title: Generieren von Miniaturansichten mithilfe von Media Encoder Standard mit REST
description: In diesem Artikel sehen Sie, wie Sie mithilfe von REST ein Objekt codieren und gleichzeitig Vorschauminiaturen mithilfe des Media Encoder Standards generieren können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f4456a65f422c23da73f36dd74680fbb598db186
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955833"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>Generieren von Vorschauminiaturen mithilfe des Media Encoder Standards mit REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Sie können Media Encoder Standard verwenden, um eine oder mehrere Miniaturansichten aus den Eingangsvideodaten in den Bilddateiformaten [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) oder [BMP](https://en.wikipedia.org/wiki/BMP_file_format) zu generieren.

## <a name="recommended-reading-and-practice"></a>Empfohlene Literatur und Praxis

Es wird empfohlen, dass Sie sich mit benutzerdefinierten Transformationen vertraut machen, indem Sie den Artikel [Codieren mit einer benutzerdefinierten Transformation: REST](custom-preset-rest-howto.md) lesen.

## <a name="thumbnail-parameters"></a>Vorschauminiaturparameter

Sie sollten die folgenden Parameter festlegen:

- **start:** Die Stelle im Eingabevideo, ab der Vorschauminiaturen erstellt werden sollen. Der Wert kann das ISO 8601-Format aufweisen, z. B. PT05S, damit ab fünf Sekunden gestartet wird, oder die Nummer eines Frames, z. B. 10, damit beim zehnten Frame gestartet wird, oder einen relativen Wert zur Dauer des Streams, z. B. 10 %, damit bei zehn Prozent der Gesamtdauer des Streams gestartet wird. Außerdem wird ein Makro namens {Best} unterstützt, das den Encoder anweist, die beste Vorschauminiatur aus den ersten Sekunden des Videos auszuwählen. Dabei wird unabhängig von anderen Einstellungen für „step“ und „range“ nur eine Vorschauminiatur erstellt. Der Standardwert des Makros lautet {Best}.
- **step:** Das Intervall, mit dem Vorschauminiaturen erstellt werden. Der Wert kann das ISO 8601-Format aufweisen, z. B. PT05S, damit ein Bild alle fünf Sekunden erstellt wird, oder die Nummer eines Frames, z. B. 30 für ein Bild alle 30 Frames, oder einen relativen Wert für die Gesamtdauer des Streams, z. B. 10 %, damit ein Bild zu den Zeitpunkten erstellt wird, die jeweils immer zehn Prozent der Gesamtdauer des Streams entsprechen. Der step-Wert wirkt sich auf die erste Vorschauminiatur aus, die erstellt wird. Dabei handelt es sich möglicherweise jedoch nicht um die, die zur vordefinierten Startzeit der Transformation angegeben wurde. Das liegt am Encoder, der versucht, die beste Vorschauminiatur zwischen Startzeit und Schrittposition (bei der Startzeit beginnend) als erste Ausgabe auszuwählen. Der Standardwert beträgt 10 %, d. h., die erste erstellte Vorschauminiatur kann sich weit von dem zur Startzeit angegebenen entfernt befindet, wenn der Stream eine lange Dauer aufweist. Wählen Sie einen sinnvollen step-Wert aus, wenn zu erwarten ist, dass sich die erste Vorschauminiatur in der Nähe der Startzeit befindet, oder legen Sie den range-Wert auf 1 fest, wenn zur Startzeit nur eine Vorschauminiatur erforderlich ist.
- **range:** Die relative Position zur Transformation der vordefinierten Startzeit im Eingabevideo, bei der die Erzeugung von Vorschauminiaturen beendet werden soll. Der Wert kann das ISO 8601-Format aufweisen, z. B. PT5M30S, damit ab fünf Minuten und 30 Sekunden nach Startzeit angehalten wird, oder die Nummer eines Frames, z. B. 300, damit beim dreihundertsten Frame ab dem Frame zur Startzeit angehalten wird. Wenn dieser Wert 1 ist, bedeutet dies, dass nur eine Vorschauminiatur zur Startzeit erzeugt wird, oder ein relativer Wert zur Streamdauer, z. B. 50 %, um bei der Hälfte der Streamdauer ab Startzeit anzuhalten. Der Standardwert ist 100 %. Dies bedeutet, dass am Ende des Streams angehalten werden soll.
- **layers:** Eine Sammlung von Ausgabebildebenen, die vom Encoder erzeugt werden sollen.

## <a name="example-of-a-single-png-file-preset"></a>Beispiel für die Voreinstellung „Einzelne PNG-Datei“

Die folgende JSON-Voreinstellung kann verwendet werden, um eine einzelne PNG-Ausgabedatei von den ersten Sekunden der Eingangsvideodaten zu erzeugen, indem der Encoder einen Versuch zum Suchen eines „interessanten“ Frames macht. Beachten Sie, dass die Ausgabebilddimensionen auf 100 % festgelegt wurden, d. h. sie entsprechen den Dimensionen der Eingangsvideodaten. Beachten Sie außerdem, dass die Einstellung „Format“ unter „Ausgaben“ erforderlich ist, damit die Verwendung von „PngLayers“ im Abschnitt „Codecs“ übereinstimmt.  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Beispiel für die Voreinstellung „Eine Reihe von JPEG-Bildern“

Die folgende JSON-Voreinstellung kann verwendet werden, um zehn Bilder an den Zeitstempeln 5 %, 15 %,..., 95 % der Eingabezeitachse zu erstellen, wobei die Bildgröße auf ein Viertel der Eingangsvideodaten festgelegt ist.

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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Beispiel für die Voreinstellung „Ein Bild zu einem bestimmten Zeitstempel“

Die folgende JSON-Voreinstellung kann verwendet werden, um ein einzelnes JPEG-Bild an der 30-Sekunden-Marke der Eingangsvideodaten zu erzeugen. Diese Voreinstellung geht von einer Eingangsvideodatenlänge von mehr als 30 Sekunden aus (andernfalls tritt beim Auftrag ein Fehler auf).

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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Beispiel für das Generieren einer Miniaturansicht bei der Codierung

In allen oben genannten Beispielen wurde erläutert, dass Sie eine Codierungsaufgabe, die nur Bilder erzeugt, übermitteln können. Jedoch ist die Video- bzw. Audiocodierung auch beim Generieren von Miniaturansichten möglich. Die folgende JSON-Voreinstellung weist Media Encoder Standard an, bei der Codierung eine Vorschauminiatur zu generieren.

### <a name="json-preset"></a>JSON-Voreinstellung

Informationen zum Schema finden Sie in [diesem](../previous/media-services-mes-schema.md) Artikel.

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

## <a name="next-steps"></a>Nächste Schritte
[Generieren von Vorschauminiaturen mithilfe von Media Encoder Standard mit .NET](media-services-generate-thumbnails-dotnet.md)