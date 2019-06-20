---
title: Eine experimentelle Voreinstellung für die inhaltsbezogene Codierung – Azure | Microsoft-Dokumentation
description: In diesem Artikel wird die inhaltsbezogene Codierung in Azure Media Services erläutert.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: ddb7bfd2437af806c8db75068c50545e69867ea0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65151020"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimentelle Voreinstellung für die inhaltsbezogene Codierung

Um Inhalte für die Übermittlung durch [Adaptive Bitrate Streaming](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) vorzubereiten, muss Video mit mehreren Bitraten (hoch bis niedrig) codiert werden. Um eine gleichmäßige Qualitätsminderung zu gewährleisten, wird mit abnehmender Bitrate auch die Auflösung des Videos verringert. Dies führt zu einer sogenannten Codierungsleiter, also einer Tabelle mit Auflösungen und Bitraten. Informationen hierzu finden Sie in den [integrierten Codierungsvoreinstellung](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) von Media Services.

## <a name="overview"></a>Übersicht

Das Interesse, über einen Ansatz mit einer Voreinstellung für alle Videos hinauszugehen, nahm zu, nachdem Netflix im Dezember 2015 seinen [Blog](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) veröffentlicht hatte. Seitdem wurden mehrere Lösungen für inhaltsbezogene Codierung auf den Markt gebracht. In diesem [Artikel](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) finden Sie eine Übersicht. Die Idee ist, sich auf den Inhalt zu beziehen und die Codierungsleiter an die Komplexität des jeweiligen Videos anzupassen oder damit abzustimmen. Bei jeder Auflösung gibt es eine Bitrate, bei der eine Qualitätssteigerung nicht mehr wahrnehmbar ist. Der Encoder arbeitet dann mit diesem optimalen Bitratenwert. Die nächste Optimierungsstufe ist die Wahl der Auflösungen basierend auf dem Inhalt. Beispielsweise profitiert ein Video einer PowerPoint-Präsentation nicht von einem Wert unter 720p. Darüber hinaus kann der Encoder aufgefordert werden, die Einstellungen für jede Einstellung innerhalb des Videos zu optimieren. Netflix hat [einen solchen Ansatz](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) 2018 beschrieben.

Anfang 2017 hat Microsoft die [Adaptive Streaming](autogen-bitrate-ladder.md)-Voreinstellung veröffentlicht, um das Problem der Variabilität hinsichtlich Qualität und Auflösung von Quellvideos zu lösen. Unsere Kunden hatten eine unterschiedliche Zusammenstellung von Inhalten, einige mit 1080p, andere mit 720p sowie weitere mit SD- und niedrigeren Auflösungen. Darüber hinaus zeichnen sich nicht alle Quellinhalte durch Mezzanine mit hoher Qualität von Film- oder Fernsehstudios aus. Die Adaptive Streaming-Voreinstellung löst diese Probleme, indem sichergestellt wird, dass die Bitratenleiter nicht die Auflösung oder die durchschnittliche Bitrate der Eingangs-Mezzanine überschreitet.

Die experimentelle inhaltsbezogene Codierungsvoreinstellung erweitert diesen Mechanismus. Es wird benutzerdefinierte Logik integriert, die es dem Encoder ermöglicht, den optimalen Bitratenwert für eine bestimmte Auflösung zu suchen, ohne dass eine umfangreiche Berechnungsanalyse erforderlich ist. Das Endergebnis ist, dass diese neue Voreinstellung eine Ausgabe liefert, die eine niedrigere Bitrate als die Adaptive Streaming-Voreinstellung aufweist, aber mit einer höheren Qualität. Die folgenden Beispielgrafiken zeigen den Vergleich mit Qualitätsmetriken wie [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) und [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Die Quelle wurde durch das Verketten kurzer Clips mit hochkomplexen Einstellungen aus Filmen und Fernsehsendungen erstellt, die den Encoder auf eine harte Probe stellen sollten. Per Definition liefert diese Voreinstellung Ergebnisse, die je nach Inhalt variieren. Das bedeutet auch, dass es bei einigen Inhalten möglicherweise keine signifikante Reduzierung der Bitrate oder Verbesserung der Qualität gibt.

![Rate-Distortion-Kurve (RD) unter Verwendung von PSNR](media/cae-experimental/msrv1.png)

**Abbildung 1: Rate-Distortion-Kurve (RD) unter Verwendung der Metrik PSNR für Quelle mit hoher Komplexität**

![Rate-Distortion-Kurve (RD) unter Verwendung von VMAF](media/cae-experimental/msrv2.png)

**Abbildung 2: Rate-Distortion-Kurve (RD) unter Verwendung der Metrik VMAF für Quelle mit hoher Komplexität**

Die Voreinstellung ist derzeit auf hochkomplexe, qualitativ hochwertige Quellvideos (Filme, Fernsehsendungen) ausgelegt. Derzeit wird an der Anpassung an Inhalte mit geringer Komplexität (z.B. PowerPoint-Präsentationen) sowie an Videos mit schlechterer Qualität gearbeitet. Diese Voreinstellung verwendet auch die gleichen Auflösungen wie die Adaptive Streaming-Voreinstellung. Microsoft arbeitet an Methoden, um den minimalen Satz von Auflösungen basierend auf dem Inhalt auszuwählen. Es folgen Ergebnisse für eine weitere Kategorie von Quellinhalten, bei denen der Encoder feststellen konnte, dass die Eingabe von schlechter Qualität war (viele Kompressionsartefakte aufgrund der niedrigen Bitrate). Beachten Sie, dass der Encoder bei der experimentellen Voreinstellung sich dafür entschieden hat, nur eine Ausgabeschicht zu erzeugen, und zwar bei einer ausreichend niedrigen Bitrate, sodass die meisten Clients den Stream ohne Verzögerung wiedergeben können.

![RD-Kurve mit PSNR](media/cae-experimental/msrv3.png)

**Abbildung 3: RD-Kurve mit PSNR für Eingabe mit niedriger Qualität (bei 1080p)**

![RD-Kurve mit VMAF](media/cae-experimental/msrv4.png)

**Abbildung 4: RD-Kurve mit VMAF für Eingabe mit niedriger Qualität (bei 1080p)**

## <a name="use-the-experimental-preset"></a>Verwenden der experimentellen Voreinstellung

Sie können Transformationen erstellen, die diese Voreinstellung wie folgt verwenden. Wenn Sie ein Tutorial [wie dieses](stream-files-tutorial-with-api.md) nutzen, können Sie den Code wie folgt aktualisieren:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Das Präfix „experimental“ wird hier verwendet, um zu signalisieren, dass sich die zugrunde liegenden Algorithmen noch in der Entwicklung befinden. Es kann und wird im Laufe der Zeit Änderungen an der Logik zur Generierung von Bitratenleitern geben. Ziel ist es, zu einem zuverlässigen Algorithmus zu gelangen, der sich an eine Vielzahl von Eingabebedingungen anpasst. Codierungsaufträge, die diese Voreinstellung verwenden, werden weiterhin nach Ausgabeminuten abgerechnet. Die Ausgabedaten können von unseren Streamingendpunkten in Protokollen wie DASH und HLS übermittelt werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun diese neue Option zur Optimierung Ihrer Videos kennengelernt haben, laden wir Sie ein, sie auszuprobieren. Sie können uns über die Links am Ende dieses Artikels Feedback zukommen lassen oder sich unter <amsved@microsoft.com> direkt an uns wenden.
