---
title: Schema von Eingabemetadaten für Azure Media Services v3
description: Dieser Artikel bietet eine Übersicht über das Schema von Eingabemetadaten für Azure Media Services v3.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 1ca526b7ecbe20a54ec115521cdfbc93c713e0da
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360053"
---
# <a name="input-metadata"></a>Eingabemetadaten

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ein Codierauftrag ist einem Eingabeobjekt (oder mehreren) zugeordnet, für das Sie einige Codieraufgaben durchführen möchten.  Nach Abschluss einer Aufgabe wird ein Ausgabeobjekt erzeugt. Das Ausgabeobjekt enthält Videodaten, Audiodaten, Miniaturansichten, das Manifest und andere Dateien. 

Das Ausgabeobjekt enthält auch eine Datei mit Metadaten zum Eingabeobjekt. Der Name der JSON-Datei mit Metadaten hat eine Zufalls-ID. Verwenden Sie diese nicht, um das Eingabeobjekt zu identifizieren, zu dem das Ausgabeobjekt gehört. Um das Eingabeobjekt zu bestimmen, zu dem es gehört, verwenden Sie das Feld `Uri` (weitere Informationen finden Sie unter [Andere untergeordnete Elemente](#other-child-elements)).  

Media Services scannt Eingabeobjekte nicht im Vorhinein, um Metadaten zu generieren. Eingabemetadaten werden nur dann als Artefakt generiert, wenn ein Eingabeobjekt in einem Auftrag verarbeitet wird. Dieses Artefakt wird dann in das Ausgabeobjekt geschrieben. Zum Generieren von Metadaten für Eingabe- und Ausgabeobjekte werden verschiedene Tools eingesetzt. Deshalb weicht das Schema der Eingabemetadaten leicht von dem der Ausgabemetadaten ab.

In diesem Artikel werden die Elemente und Typen des JSON-Schemas beschrieben, auf denen die Eingabemetadaten (&lt;asset_id&gt;_metadata.json) basieren. Weitere Informationen zur Datei, in der die Metadaten zum Ausgabeobjekt enthalten sind, finden Sie unter [Ausgabemetadaten](output-metadata-schema.md).  

Sie finden das Beispiel eines JSON-Schemas am Ende dieses Artikels.  

## <a name="assetfile"></a>AssetFile  

Enthält eine Sammlung der AssetFile-Elemente für den Codierungsauftrag.  

> [!NOTE]
> Die folgenden vier untergeordneten Elemente müssen als Sequenz angezeigt werden.  
> 
> 

| Name  | BESCHREIBUNG |
| --- | --- | 
| **VideoTracks**|Jede physische Medienobjektdatei kann null oder mehr Videospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Weitere Informationen finden Sie unter [VideoTracks](#videotracks). |
| **AudioTracks**|Jede physische Assetdatei kann null oder mehr Audiospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Weitere Informationen finden Sie unter [AudioTracks](#audiotracks). |
| **Metadaten**  |Darstellung der Metadaten einer Assetdatei als Schlüssel-Wert-Zeichenfolge. <br />Beispiel: `<Metadata key="language" value="eng" />` |

### <a name="other-child-elements"></a>Andere untergeordnete Elemente

| Name | BESCHREIBUNG |
| --- | --- |
| **Name**<br />Erforderlich |Name der Objektdatei <br /><br />Beispiel: `"Name": "Ignite-short.mp4"` |
| **URI**<br />Erforderlich |Die URL, unter der sich das Eingabeobjekt befindet. Um das Eingabeobjekt zu bestimmen, zu dem das Ausgabeobjekt gehört, verwenden Sie das Feld `Uri` anstelle der ID.|
| **Größe**<br />Erforderlich |Größe der Objektdatei in Byte  <br /><br />Beispiel: `"Size": 75739259`|
| **Duration**<br />Erforderlich |Dauer der Inhaltswiedergabe. <br /><br />Beispiel: `"Duration": "PT1M10.304S"`. |
| **NumberOfStreams**<br />Erforderlich |Anzahl von Datenströmen in der Assetdatei  <br /><br />Beispiel: `"NumberOfStreams": 2`|
| **FormatNames**<br />Erforderlich |Formatierung von Namen  <br /><br />Beispiel: `"FormatNames": "mov,mp4,m4a,3gp,3g2,mj2"`|
| **FormatVerboseName**<br /> Erforderlich |Formatieren von ausführlichen Namen <br /><br />Beispiel: `"FormatVerboseName": "QuickTime / MOV"` |
| **StartTime** |Startzeit des Inhalts.  <br /><br />Beispiel: `"StartTime": "PT0S"` |
| **OverallBitRate** |Durchschnittliche Bitrate der Objektdatei in Bits pro Sekunde.  <br /><br />Beispiel: `"OverallBitRate": 8618539`|

## <a name="videotracks"></a>VideoTracks

| Name | BESCHREIBUNG |
| --- | --- |
| **FourCC**<br />Erforderlich |FourCC-Code des Videocodecs, der von ffmpeg gemeldet wird.<br /><br />Beispiel: `"FourCC": "avc1"` |
| **Profil** |Profil der Videospur <br /><br />Beispiel: `"Profile": "Main"`|
| **Level** |Ebene der Videospur <br /><br />Beispiel: `"Level": "3.2"`|
| **PixelFormat** |Pixelformat der Videospur <br /><br />Beispiel: `"PixelFormat": "yuv420p"`|
| **Width**<br />Erforderlich |Breite des codierten Videos in Pixel <br /><br />Beispiel: `"Width": "1280"`|
| **Height**<br />Erforderlich |Höhe des codierten Videos in Pixel<br /><br />Beispiel: `"Height": "720"` |
| **DisplayAspectRatioNumerator**<br />Erforderlich |Seitenverhältnis-Zähler der Videoanzeige<br /><br />Beispiel: `"DisplayAspectRatioNumerator": 16.0` |
| **DisplayAspectRatioDenominator**<br />Erforderlich |Seitenverhältnis-Nenner der Videoanzeige <br /><br />Beispiel: `"DisplayAspectRatioDenominator": 9.0`|
| **SampleAspectRatioNumerator** |Seitenverhältnis-Zähler des Videosamples <br /><br />Beispiel: `"SampleAspectRatioNumerator": 1.0`|
| **SampleAspectRatioDenominator**|Beispiel: `"SampleAspectRatioDenominator": 1.0`|
| **FrameRate**<br />Erforderlich |Gemessene Videobildfrequenz im Format „.3f“ <br /><br />Beispiel: `"FrameRate": 29.970`|
| **Bitrate** |Durchschnittliche Videobitrate in Bits pro Sekunde gemäß Berechnung anhand der Objektdatei. Nur die elementare Datenstrom-Nutzlast wird gezählt. Der Aufwand für das Verpacken wird nicht einbezogen. <br /><br />Beispiel: `"Bitrate": 8421583`|
| **HasBFrames** |Videospuranzahl von B-Bildern <br /><br />Beispiel: `"HasBFrames": 2`|
| **Metadaten** |Generische Schlüssel-Wert-Zeichenfolgen, die für verschiedene Informationen verwendet werden können. <br />Das vollständige Beispiel finden Sie am Ende des Artikels. |
| **Id**<br />Erforderlich |Nullbasierter Index dieser Audio- oder Videospur.<br /><br /> Diese **ID** ist nicht unbedingt die „TrackID“, die in einer MP4-Datei verwendet wird. <br /><br />Beispiel: `"Id": 2`|
| **Codec** |Codeczeichenfolge der Videospur <br /><br />Beispiel: `"Codec": "h264"`|
| **CodecLongName** |Langer Name des Audio- oder Videospurcodecs <br /><br />Beispiel: `"CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"`|
| **Codec** |Codeczeichenfolge der Videospur <br /><br />Beispiel: `"Codec": "h264"`|
| **TimeBase**<br />Erforderlich |Gibt die Zeitbasis an.<br /><br />Beispiel: `"TimeBase": "1/30000"`|
| **NumberOfFrames** |Anzahl von Bildern (für Videospuren) <br /><br />Beispiel: `"NumberOfFrames": 2107`|
| **StartTime** |Gibt die Startzeit des Titels an.<br /><br />Beispiel: `"StartTime": "PT0.033S"` |
| **Duration** |Gibt die Dauer des Titels an. <br /><br />Beispiel: `"Duration": "PT1M10.304S"`|

## <a name="audiotracks"></a>AudioTracks

| Name  | BESCHREIBUNG |
| --- | --- | 
| **SampleFormat** |Beispielformat <br /><br />Beispiel: `"SampleFormat": "fltp"`|
| **ChannelLayout** |Kanallayout <br /><br />Beispiel: `"ChannelLayout": "stereo"`|
| **Channels**<br />Erforderlich |Anzahl von Audiokanälen (0 oder mehr) <br /><br />Beispiel: `"Channels": 2`|
| **SamplingRate**<br />Erforderlich |Audiosamplingrate in Stichproben/Sekunde oder Hz <br /><br />Beispiel: `"SamplingRate": 48000`|
| **Bitrate** |Die durchschnittliche Audiobitrate in Bit/s gemäß Berechnung anhand der Assetdatei. Nur die elementare Datenstrom-Nutzlast wird gezählt. Der Aufwand für das Verpacken wird in diesen Wert nicht einbezogen. <br /><br />Beispiel: `"Bitrate": 192080`|
| **Metadaten** |Generische Schlüssel-Wert-Zeichenfolgen, die für verschiedene Informationen verwendet werden können.  <br />Das vollständige Beispiel finden Sie am Ende des Artikels. |
| **Id**<br />Erforderlich |Nullbasierter Index dieser Audio- oder Videospur.<br /><br /> Dies bedeutet nicht unbedingt, dass die TrackID in einer MP4-Datei verwendet wird. <br /><br />Beispiel: `"Id": 1`|
| **Codec** |Codeczeichenfolge der Videospur <br /><br />Beispiel: `"Codec": "aac"`|
| **CodecLongName** |Langer Name des Audio- oder Videospurcodecs <br /><br />Beispiel: `"CodecLongName": "AAC (Advanced Audio Coding)"`|
| **TimeBase**<br />Erforderlich |Gibt die Zeitbasis an.<br /><br />Beispiel: `"TimeBase": "1/48000"` |
| **NumberOfFrames** |Anzahl von Bildern (für Videospuren) <br /><br />Beispiel: `"NumberOfFrames": 3294`|
| **StartTime** |Gibt die Startzeit des Titels an. Weitere Informationen finden Sie unter [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html). <br /><br />Beispiel: `"StartTime": "PT0S"` |
| **Duration** |Gibt die Dauer des Titels an. <br /><br />Beispiel: `"Duration": "PT1M10.272S"` |

## <a name="metadata"></a>Metadaten

| Name | BESCHREIBUNG |
| --- | --- |
| **key**<br />Erforderlich |Schlüssel des Schlüssel-Wert-Paars |
| **value**<br /> Erforderlich |Wert des Schlüssel-Wert-Paars |

## <a name="schema-example"></a>Beispiel für das Schema

```json
{
  "AssetFile": [
    {
      "VideoTracks": [
        {
          "FourCC": "avc1",
          "Profile": "Main",
          "Level": "3.2",
          "PixelFormat": "yuv420p",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "SampleAspectRatioNumerator": 1.0,
          "SampleAspectRatioNumeratorSpecified": true,
          "SampleAspectRatioDenominator": 1.0,
          "SampleAspectRatioDenominatorSpecified": true,
          "FrameRate": 29.970,
          "Bitrate": 8421583,
          "BitrateSpecified": true,
          "HasBFrames": 2,
          "HasBFramesSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Video Media Handler"
            },
            {
              "key": "encoder",
              "value": "AVC Coding"
            }
          ],
          "Id": 2,
          "Codec": "h264",
          "CodecLongName": "H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10",
          "TimeBase": "1/30000",
          "NumberOfFrames": 2107,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0.033S",
          "Duration": "PT1M10.304S"
        }
      ],
      "AudioTracks": [
        {
          "SampleFormat": "fltp",
          "ChannelLayout": "stereo",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192080,
          "BitrateSpecified": true,
          "BitsPerSampleSpecified": true,
          "Disposition": {
            "Default": 1
          },
          "Metadata": [
            {
              "key": "creation_time",
              "value": "2018-02-21T21:42:08.000000Z"
            },
            {
              "key": "language",
              "value": "eng"
            },
            {
              "key": "handler_name",
              "value": "Sound Media Handler"
            }
          ],
          "Id": 1,
          "Codec": "aac",
          "CodecLongName": "AAC (Advanced Audio Coding)",
          "TimeBase": "1/48000",
          "NumberOfFrames": 3294,
          "NumberOfFramesSpecified": true,
          "StartTime": "PT0S",
          "Duration": "PT1M10.272S"
        }
      ],
      "Metadata": [
        {
          "key": "major_brand",
          "value": "mp42"
        },
        {
          "key": "minor_version",
          "value": "19529854"
        },
        {
          "key": "compatible_brands",
          "value": "mp42isom"
        },
        {
          "key": "creation_time",
          "value": "2018-02-21T21:42:08.000000Z"
        }
      ],
      "Name": "Ignite-short.mp4",
      "Uri": "https://amsstorageacct.blob.core.windows.net/asset-00000000-0000-0000-000000000000/ignite.mp4",
      "Size": 75739259,
      "Duration": "PT1M10.304S",
      "NumberOfStreams": 2,
      "FormatNames": "mov,mp4,m4a,3gp,3g2,mj2",
      "FormatVerboseName": "QuickTime / MOV",
      "StartTime": "PT0S",
      "OverallBitRate": 8618539,
      "OverallBitRateSpecified": true
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

[Ausgeben von Metadaten](output-metadata-schema.md)
