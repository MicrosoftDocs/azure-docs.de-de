---
title: Erkennung von Farbschemas – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Konzepte zur Erkennung des Farbschemas in Bildern mithilfe der Maschinelles Sehen-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e7774d2cd100931f92ff80066ebea4463c6f65c
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532651"
---
# <a name="detect-color-schemes-in-images"></a>Erkennen von Farbschemas auf Bildern

Maschinelles Sehen analysiert die Farben eines Bilds hinsichtlich dreier Attribute: die vorherrschende Vordergrundfarbe, die vorherrschende Hintergrundfarbe und den Satz vorherrschender Farben für das gesamte Bild. Die zurückgegeben Farben gehören zum folgenden Satz: Schwarz, Blau, Braun, Grau, Grün, Orange, Rosa, Violett, Rot, Aquamarin, Weiß und Gelb. 

Maschinelles Sehen extrahiert auch eine Akzentfarbe, die die leuchtendste Farbe im Bild darstellt und auf einer Kombination aus dominanten Farben und Sättigung basiert. Die Akzentfarbe wird als hexadezimaler HTML-Farbcode zurückgegeben. 

Maschinelles Sehen gibt auch einen booleschen Wert zurück, der angibt, ob ein Bild schwarzweiß ist.

## <a name="color-scheme-detection-examples"></a>Beispiele für die Farbschemaerkennung

Das folgende Beispiel veranschaulicht die vom maschinellen Sehen zurückgegebene JSON-Antwort bei der Erkennung des Farbschemas im Beispielbild. In diesem Fall ist das Beispielbild nicht schwarzweiß, aber die vorherrschenden Vorder- und Hintergrundfarben sind schwarz, und die vorherrschenden Farben für das gesamte Bild sind schwarzweiß.

![Berg bei Sonnenuntergang mit Silhouette einer Person](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Beispiele für die vorherrschende Farbe

Die folgende Tabelle enthält die zurückgegebenen Vorder-, Hintergrund- und Bildfarben für jedes Beispielbild.

| Image | Vorherrschende Farben |
|-------|-----------------|
|![Eine weiße Blume vor einem grünen Hintergrund](./Images/flower.png)| Vordergrund: Schwarz<br/>Hintergrund: White<br/>Farben: Schwarz, Weiß, Grün|
![Ein Zug, der durch einen Bahnhof fährt](./Images/train_station.png) | Vordergrund: Schwarz<br/>Hintergrund: Schwarz<br/>Farben: Schwarz |

### <a name="accent-color-examples"></a>Beispiele für Akzentfarben

 Die folgende Tabelle enthält die zurückgegebene Akzentfarbe als hexadezimalen HTML-Farbwert für jedes Beispielbild.

| Image | Akzentfarbe |
|-------|--------------|
|![Eine Person, die bei Sonnenuntergang auf einem Gebirgsfelsen steht](./Images/mountain_vista.png) | #BB6D10 |
|![Eine weiße Blume vor einem grünen Hintergrund](./Images/flower.png) | #C6A205 |
|![Ein Zug, der durch einen Bahnhof fährt](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Beispiele für die Schwarzweiß-Erkennung

Die folgende Tabelle enthält die Schwarzweiß-Auswertung von maschinellem Sehen in den Beispielbildern.

| Image | Schwarzweiß? |
|-------|----------------|
|![Ein Schwarzweißbild von Gebäuden in Manhattan](./Images/bw_buildings.png) | true |
|![Ein blaues Haus mit Vorgarten](./Images/house_yard.png) | false |

## <a name="use-the-api"></a>Verwenden der API

Die Funktion zur Erkennung von Farbschemas ist Teil der [Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-API. Sie können diese API über ein natives SDK oder REST-Aufrufe aufrufen. Beziehen Sie `Color` in den Abfrageparameter **visualFeatures** ein. Nachdem Sie die vollständige JSON-Antwort erhalten haben, analysieren Sie einfach die Zeichenfolge auf den Inhalt im Abschnitt `"color"`.

* [Schnellstart: Verwenden der Clientbibliothek für maschinelles Sehen](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
