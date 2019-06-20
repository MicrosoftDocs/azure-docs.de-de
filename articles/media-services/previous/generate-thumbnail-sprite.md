---
title: Generieren eines Miniaturbild-Sprites mit Azure Media Services | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie Sie mit Azure Media Services ein Miniaturbild-Sprite erstellen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61229049"
---
# <a name="generate-a-thumbnail-sprite"></a>Generieren eines Miniaturbild-Sprites  

Sie können Media Encoder Standard verwenden, um ein Miniaturbild-Sprite zu generieren, das eine JPEG-Datei ist, die mehrere Miniaturbilder mit kleiner Auflösung enthält, die zusammen mit einer VTT-Datei zu einem einzigen (großen) Bild zusammengefügt werden. Diese VTT-Datei gibt den Zeitbereich im Eingabevideo an, den jedes Miniaturbild darstellt, zusammen mit der Größe und den Koordinaten dieses Miniaturbildes innerhalb der großen JPEG-Datei. Videoplayer verwenden die VTT-Datei und das Sprite-Bild, um eine „visuelle“ Suchleiste anzuzeigen, die dem Betrachter visuelles Feedback gibt, wenn er sich entlang der Videozeitleiste vor- und zurückbewegt.

Um mit dem Media Encoder Standard Miniaturbild-Sprites zu generieren, muss Folgendes für die Voreinstellung gelten:

1. Sie muss das JPG-Miniaturbildformat verwenden.
2. Sie muss Werte für Start/Schritt/Bereich als Zeitstempel oder %-Werte (und nicht als Anzahl der Frames) angeben. 
    
    1. Zeitstempel und %-Werte können kombiniert werden.

3. Sie muss über den SpriteColumn-Wert verfügen, der einer nicht negativen Zahl entspricht, die größer als oder gleich 1 ist.

    1. Wenn „SpriteColumn“ auf M >= 1 festgelegt ist, entspricht das Ausgabebild einem Rechteck mit M-Spalten. Wenn die Anzahl der Miniaturbilder, die über Schritt 2 generiert wurden, nicht genau einem Vielfachen von M entspricht, ist die letzte Zeile unvollständig und mit schwarzen Pixeln versehen.  

Beispiel:

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
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

## <a name="known-issues"></a>Bekannte Probleme

1.  Es ist nicht möglich, ein Sprite-Bild mit einer einzelnen Zeile von Bildern zu generieren (SpriteColumn = 1 ergibt ein Bild mit einer einzelnen Spalte).
2.  Die Blockerstellung der Sprite-Bilder zu mittelgroßen JPEG-Bildern wird noch nicht unterstützt. Daher müssen Sie darauf achten, die Anzahl der Miniaturbilder und deren Größe zu begrenzen, sodass das resultierende zusammengefügte Miniaturbild-Sprite etwa 8 M Pixel oder weniger beträgt.
3.  Azure Media Player unterstützt Sprites für die Browser Microsoft Edge, Chrome und Firefox. Die VTT-Analyse wird im IE11 nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

[Codieren von Inhalten](media-services-encode-asset.md)
