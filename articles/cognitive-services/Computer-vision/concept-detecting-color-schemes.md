---
title: Erkennen von Farbschemas – Maschinelles Sehen
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
ms.openlocfilehash: ff7af2204f9e05a1ba4ef800c63c3ad462242350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368566"
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
|![Eine weiße Blume vor einem grünen Hintergrund](./Images/flower.png)| Vordergrund: Schwarz<br/>Hintergrund: Weiß<br/>Farben: Schwarz, Weiß, Grün|
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

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Konzepte zum [Erkennen von Bildtypen](concept-detecting-image-types.md).
