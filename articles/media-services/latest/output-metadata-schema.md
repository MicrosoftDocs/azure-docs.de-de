---
title: Schema von Ausgabemetadaten für Azure Media Services
description: Dieser Artikel enthält eine Übersicht über das Metadatenschema der Azure Media Services v3-Ausgabe.
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
ms.openlocfilehash: 1a27db81866760bff56c900a908162f356c01a66
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895202"
---
# <a name="output-metadata"></a>Ausgeben von Metadaten

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ein Codierauftrag ist einem Eingabeasset (oder mehreren) zugeordnet, für das Sie einige Codieraufgaben durchführen möchten. Beispiele: Codieren einer MP4-Datei in H.264 MP4 Adaptive Bitrate-Sätze, Erstellen einer Miniaturansicht, Erstellen von Überlagerungen. Nach Abschluss einer Aufgabe wird ein Ausgabeasset erzeugt.  Das Ausgabeasset enthält Video-, Audio-, Miniaturansichts- und weitere Dateien. Das Ausgabeasset enthält auch eine Datei mit Metadaten zum Ausgabeasset. Der Name der JSON-Metadatendatei weist das folgende Format auf: `<source_file_name>_manifest.json` (Beispiel: `BigBuckBunny_manifest.json`). Sie sollten nach „*_metadata.json“ suchen und die enthaltene Dateipfad-Zeichenfolge abfragen, um den Quelldateinamen (ohne Kürzung) zu suchen.

Media Services scannt Eingabeobjekte nicht im Vorhinein, um Metadaten zu generieren. Eingabemetadaten werden nur dann als Artefakt generiert, wenn ein Eingabeasset in einem Auftrag verarbeitet wird. Dieses Artefakt wird dann in das Ausgabeasset geschrieben. Zum Generieren von Metadaten für Eingabe- und Ausgabeobjekte werden verschiedene Tools eingesetzt. Deshalb weicht das Schema der Eingabemetadaten leicht von dem der Ausgabemetadaten ab.

In diesem Artikel werden die Elemente und Typen des JSON-Schemas beschrieben, auf denen die Ausgabemetadaten (&lt;Quelldateiname&gt;_manifest.json) basieren. <!--For information about the file that contains metadata about the input asset, see [Input metadata](input-metadata-schema.md).  -->

Sie finden den vollständigen Schemacode und das JSON-Beispiel am Ende des Artikels.  

## <a name="assetfile"></a>AssetFile

Sammlung von „AssetFile“-Einträgen für den Codierauftrag.  

| Name | BESCHREIBUNG |
| --- | --- |
| **Sources** |Sammlung von Eingabe-/Quellmediendateien, die verarbeitet wurde, um diese Medienobjektdatei zu erzeugen.<br />Beispiel: `"Sources": [{"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"}]`|
| **VideoTracks**|Jede physische Medienobjektdatei kann null oder mehr Videospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. <br />Siehe dazu [VideoTracks](#videotracks). |
| **AudioTracks**|Jede physische Medienobjektdatei kann null oder mehr Audiospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Dies ist die Sammlung aller dieser Audiospuren.<br /> Weitere Informationen finden Sie unter [AudioTracks](#audiotracks). |
| **Name**<br />Erforderlich |Der Name der Medienobjektdatei. <br /><br />Beispiel: `"Name": "Ignite-short_1280x720_AACAudio_3551.mp4"`|
| **Größe**<br />Erforderlich |Größe der Objektdatei in Byte <br /><br />Beispiel: `"Size": 32414631`|
| **Duration**<br />Erforderlich |Dauer der Inhaltswiedergabe. Weitere Informationen finden Sie in der Dokumentation zum [ISO8601](https://www.iso.org/iso-8601-date-and-time-format.html)-Format. <br /><br />Beispiel: `"Duration": "PT1M10.315S"`|

## <a name="videotracks"></a>VideoTracks 

Jede physische Medienobjektdatei kann null oder mehr Videospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Das **VideoTracks**-Element stellt eine Sammlung aller Videotitel dar.  

| Name | BESCHREIBUNG |
| --- | --- |
| **Id**<br /> Erforderlich |Nullbasierter Index dieser Videospur. **Hinweis:**  Diese **ID** ist nicht unbedingt die „TrackID“, die in einer MP4-Datei verwendet wird. <br /><br />Beispiel: `"Id": 1`|
| **FourCC**<br />Erforderlich | FourCC-Code des Videocodecs, der von ffmpeg gemeldet wird.  <br /><br />Beispiel: `"FourCC": "avc1"`|
| **Profil** |H264-Profil (gilt nur für den H264-Codec).  <br /><br />Beispiel: `"Profile": "High"` |
| **Level** |H264-Pegel (gilt nur für den H264-Codec).  <br /><br />Beispiel: `"Level": "3.2"`|
| **Width**<br />Erforderlich |Breite des codierten Videos in Pixel  <br /><br />Beispiel: `"Width": "1280"`|
| **Height**<br />Erforderlich |Höhe des codierten Videos in Pixel  <br /><br />Beispiel: `"Height": "720"`|
| **DisplayAspectRatioNumerator**<br />Erforderlich|Seitenverhältnis-Zähler der Videoanzeige  <br /><br />Beispiel: `"DisplayAspectRatioNumerator": 16.0`|
| **DisplayAspectRatioDenominator**<br />Erforderlich |Seitenverhältnis-Nenner der Videoanzeige  <br /><br />Beispiel: `"DisplayAspectRatioDenominator": 9.0`|
| **Framerate**<br />Erforderlich |Gemessene Videobildfrequenz im Format „.3f“  <br /><br />Beispiel: `"Framerate": 29.970`|
| **Bitrate**<br />Erforderlich |Durchschnittliche Videobitrate in Bits pro Sekunde gemäß Berechnung anhand der Objektdatei. Zählt nur die elementare Datenstrom-Nutzlast, ohne den Aufwand für das Packen einzubeziehen.  <br /><br />Beispiel: `"Bitrate": 3551567`|
| **TargetBitrate**<br />Erforderlich |Durchschnittliche Bitrate dieser Videospur entsprechend der Anforderung der Codierungsvoreinstellung in Bit/s. <br /><br />Beispiel: `"TargetBitrate": 3520000` |

## <a name="audiotracks"></a>AudioTracks 

Jede physische Medienobjektdatei kann null oder mehr Audiospuren enthalten, die zu einem entsprechenden Containerformat zusammengeführt werden. Das **AudioTracks**-Element stellt eine Sammlung aller Audiotitel dar.  

| Name  | BESCHREIBUNG |
| --- | --- |
| **Id**<br />Erforderlich  |Nullbasierter Index dieser Audiospur. **Hinweis:**  Dies ist nicht unbedingt die „TrackID“, die in einer MP4-Datei verwendet wird.  <br /><br />Beispiel: `"Id": 2`|
| **Codec**  |Codec-Zeichenfolge der Audiospur.  <br /><br />Beispiel: `"Codec": "aac"`|
| **Sprache**|Beispiel: `"Language": "eng"`|
| **Channels**<br />Erforderlich|Anzahl der Audiokanäle.  <br /><br />Beispiel: `"Channels": 2`|
| **SamplingRate**<br />Erforderlich |Audiosamplingrate in Stichproben/Sekunde oder Hz  <br /><br />Beispiel: `"SamplingRate": 48000`|
| **Bitrate**<br />Erforderlich |Die durchschnittliche Audiobitrate in Bit/s gemäß Berechnung anhand der Medienobjektdatei. Zählt nur die elementare Datenstrom-Nutzlast, ohne den Aufwand für das Packen einzubeziehen.  <br /><br />Beispiel: `"Bitrate": 128041`|

## <a name="json-schema-example"></a>Beispiel für das JSON-Schema

```json
{
  "AssetFile": [
    {
      "Sources": [
        {
          "Name": "Ignite-short_1280x720_AACAudio_3551.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.2",
          "Width": "1280",
          "Height": "720",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 3551567,
          "TargetBitrate": 3520000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_1280x720_AACAudio_3551.mp4",
      "Size": 32414631,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_960x540_AACAudio_2216.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.1",
          "Width": "960",
          "Height": "540",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 2216326,
          "TargetBitrate": 2210000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_960x540_AACAudio_2216.mp4",
      "Size": 20680897,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_640x360_AACAudio_1150.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "3.0",
          "Width": "640",
          "Height": "360",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 1150440,
          "TargetBitrate": 1150000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_640x360_AACAudio_1150.mp4",
      "Size": 11313920,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_480x270_AACAudio_722.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "2.1",
          "Width": "480",
          "Height": "270",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 722682,
          "TargetBitrate": 720000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }
      ],
      "Name": "Ignite-short_480x270_AACAudio_722.mp4",
      "Size": 7554708,
      "Duration": "PT1M10.315S"
    },
    {
      "Sources": [
        {
          "Name": "Ignite-short_320x180_AACAudio_380.mp4"
        }
      ],
      "VideoTracks": [
        {
          "Id": 1,
          "FourCC": "avc1",
          "Profile": "High",
          "Level": "1.3",
          "Width": "320",
          "Height": "180",
          "DisplayAspectRatioNumerator": 16.0,
          "DisplayAspectRatioDenominator": 9.0,
          "Framerate": 29.970,
          "Bitrate": 380655,
          "TargetBitrate": 380000
        }
      ],
      "AudioTracks": [
        {
          "Id": 2,
          "Codec": "aac",
          "Language": "eng",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128041
        }`
      ],
      "Name": "Ignite-short_320x180_AACAudio_380.mp4",
      "Size": 4548932,
      "Duration": "PT1M10.315S"
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Auftragseingabe aus einer HTTPS-URL](job-input-from-http-how-to.md)
