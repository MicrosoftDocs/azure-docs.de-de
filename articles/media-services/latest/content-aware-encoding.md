---
title: Voreinstellung für die inhaltsbezogene Codierung
description: In diesem Artikel wird die inhaltsbezogene Codierung in Microsoft Azure Media Services v3 erläutert.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c63ada9621fc1485742a7683fd6742f0e896854
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954428"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Ermitteln des optimalen Bitratenwerts für eine bestimmte Auflösung unter Verwendung der Voreinstellung für die inhaltsbezogene Codierung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Um Inhalte für die Übermittlung durch [Adaptive Bitrate Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) vorzubereiten, muss Video mit mehreren Bitraten (hoch bis niedrig) codiert werden. Dadurch wird eine gleichmäßige Qualitätsminderung gewährleistet, da mit abnehmender Bitrate auch die Auflösung des Videos verringert wird. Bei einer solchen Codierung mit Mehrfachbitrate wird eine sogenannte Codierungsleiter (Tabelle mit Auflösungen und Bitraten) verwendet. Weitere Informationen hierzu finden Sie in den [integrierten Codierungsvoreinstellungen](/rest/api/media/transforms/createorupdate#encodernamedpreset) von Media Services.

Berücksichtigen Sie stets den Inhalt, den Sie verarbeiten, und passen Sie die Codierungsleiter an die Komplexität des jeweiligen Videos an, bzw. optimieren Sie sie entsprechend. Bei jeder Auflösung gibt es eine Bitrate, bei der eine Qualitätssteigerung nicht mehr wahrnehmbar ist. Der Encoder arbeitet dann mit diesem optimalen Bitratenwert. Die nächste Optimierungsstufe ist die Wahl der Auflösungen basierend auf dem Inhalt. Beispielsweise profitiert ein Video einer PowerPoint-Präsentation nicht von einem Wert unter 720p. Darüber hinaus kann der Encoder aufgefordert werden, die Einstellungen für jede Einstellung innerhalb des Videos zu optimieren. 

Die Voreinstellung [Adaptives Streaming](autogen-bitrate-ladder.md) von Microsoft löst das Problem der Variabilität bei der Qualität und Auflösung von Quellvideos zum Teil. Unsere Kunden verfügen über unterschiedlichste Inhalte – manche in 1080p, andere in 720p und einige wenige in SD und niedrigeren Auflösungen. Darüber hinaus handelt es sich nicht bei allen Quellinhalten um hochwertige Mezzanine von Film- oder Fernsehstudios. Die Adaptive Streaming-Voreinstellung löst diese Probleme, indem sichergestellt wird, dass die Bitratenleiter nicht die Auflösung oder die durchschnittliche Bitrate der Eingangs-Mezzanine überschreitet. Diese Voreinstellung untersucht jedoch nur die Quelleigenschaften „Auflösung“ und „Bitrate“.

## <a name="the-content-aware-encoding"></a>Die inhaltsbezogene Codierung 

Die Voreinstellung für die inhaltsbezogene Codierung erweitert den Adaptive Bitrate Streaming-Mechanismus. Hierzu wird benutzerdefinierte Logik integriert, die es dem Encoder ermöglicht, den optimalen Bitratenwert für eine bestimmte Auflösung ohne aufwendige Berechnungsanalyse zu ermitteln. Diese Voreinstellung generiert eine Reihe von MP4-Dateien mit GOP-Ausrichtung. Der Dienst führt eine einfache Erstanalyse für den Eingabeinhalt aus und ermittelt anhand der Ergebnisse die optimale Anzahl von Ebenen, die geeignete Bitrate und die Auflösungseinstellungen für die Bereitstellung durch adaptives Streaming. Diese Voreinstellung eignet sich insbesondere für Videos mit geringer bis mittlerer Komplexität, bei denen die Ausgabedateien zwar eine niedrigere Bitrate als die Adaptive Streaming-Voreinstellung, aber immer noch eine gute Qualität haben. Die Ausgabe enthält MP4-Dateien mit AVI (Audio Video Interleaved).

Die folgenden Beispieldiagramme enthalten eine Gegenüberstellung mit Qualitätsmetriken wie [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) und [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Die Quelle wurde durch das Verketten kurzer Clips mit hochkomplexen Einstellungen aus Filmen und Fernsehsendungen erstellt, die den Encoder auf eine harte Probe stellen sollten. Per Definition liefert diese Voreinstellung Ergebnisse, die je nach Inhalt variieren. Das bedeutet auch, dass es bei einigen Inhalten möglicherweise keine signifikante Reduzierung der Bitrate oder Verbesserung der Qualität gibt.

![Rate-Distortion-Kurve (RD) unter Verwendung von PSNR](media/content-aware-encoding/msrv1.png)

**Abbildung 1: Rate-Distortion-Kurve (RD) unter Verwendung der Metrik PSNR für Quelle mit hoher Komplexität**

![Rate-Distortion-Kurve (RD) unter Verwendung von VMAF](media/content-aware-encoding/msrv2.png)

**Abbildung 2: Rate-Distortion-Kurve (RD) unter Verwendung der Metrik VMAF für Quelle mit hoher Komplexität**

Im Anschluss finden Sie die Ergebnisse für eine weitere Kategorie von Quellinhalten, bei denen der Encoder feststellen konnte, dass die Eingabe von schlechter Qualität war (viele Kompressionsartefakte aufgrund der niedrigen Bitrate). Beachten Sie, dass sich der Encoder mit der inhaltsbezogenen Voreinstellung dazu entschieden hat, nur eine einzelne Ausgabeschicht mit einer ausreichend niedrigen Bitrate zu erzeugen, sodass die meisten Clients den Stream ohne Verzögerung wiedergeben können.

![RD-Kurve mit PSNR](media/content-aware-encoding/msrv3.png)

**Abbildung 3: RD-Kurve mit PSNR für Eingabe mit niedriger Qualität (bei 1080p)**

![RD-Kurve mit VMAF](media/content-aware-encoding/msrv4.png)

**Abbildung 4: RD-Kurve mit VMAF für Eingabe mit niedriger Qualität (bei 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Verwenden der Voreinstellung für die inhaltsbezogene Codierung 

Sie können Transformationen erstellen, die diese Voreinstellung wie folgt verwenden. 

Unter [Nächste Schritte](#next-steps) finden Sie Links zu Tutorials für die Ausgabetransformation. Das Ausgabemedienobjekt kann von Media Services-Streamingendpunkten in Protokollen wie MPEG-DASH und HLS bereitgestellt werden (wie in den Tutorials gezeigt).

> [!NOTE]
> Achten Sie darauf, die Einstellung **ContentAwareEncoding**, nicht „ContentAwareEncodingExperimental“ zu verwenden.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Codierungsaufträge mit der Voreinstellung `ContentAwareEncoding` werden auf der Grundlage der Ausgabeminuten abgerechnet. 
  
## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Hochladen, Codieren und Streamen von Videos mit Media Services v3](stream-files-tutorial-with-api.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über – REST](stream-files-tutorial-with-rest.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – CLI](stream-files-cli-quickstart.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – .NET](stream-files-dotnet-quickstart.md)
* [Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos – Node.js](stream-files-nodejs-quickstart.md)
