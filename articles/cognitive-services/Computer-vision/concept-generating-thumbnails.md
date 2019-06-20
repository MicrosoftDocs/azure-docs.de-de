---
title: Generieren von Miniaturbildern – Maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Konzepte zum Generieren von Miniaturbildern für Bilder mithilfe der Maschinelles Sehen-API.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8bbc86f5c6fe0f30968a1ba5bd5fa28160ef6963
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60372859"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Generieren intelligent zugeschnittener Miniaturbilder mit maschinellem Sehen

Ein Miniaturbild ist eine verkleinerte Darstellung eines Bilds. Miniaturbilder werden verwendet, um Bilder und andere Daten auf wirtschaftliche und layoutfreundliche Weise darzustellen. Die Maschinelles Sehen-API verwendet intelligente Funktionen zum Zuschneiden und Ändern der Größe von Bildern, um intuitive Miniaturbilder für ein bestimmtes Bild zu erstellen.

Der Algorithmus zum Generieren der Miniaturbilder der Maschinelles Sehen-API funktioniert wie folgt:

1. Entfernen störender Elemente aus dem Bild und Identifizieren des _relevanten Bereichs_ &mdash; das ist der Bildbereich, in dem die wichtigsten Objekte enthalten sind.
1. Das Bild wird auf Grundlage des erkannten _relevanten Bereichs_ zugeschnitten.
1. Das Seitenverhältnis wird entsprechend den Abmessungen des Zielminiaturbilds geändert.

## <a name="area-of-interest"></a>Relevanter Bereich

Wenn Sie ein Bild hochladen, analysiert die Maschinelles Sehen-API dieses, um den *relevanten Bereich* zu ermitteln. Anhand dieser Region kann dann bestimmt werden, wie das Bild zugeschnitten werden muss. Das Zuschneiden wird jedoch immer gemäß dem gewünschten Seitenverhältnis durchgeführt, sofern dieses angegeben wurde.

Sie können die Koordinaten des unbearbeiteten umgebenden Felds dieses *relevanten Bereichs* stattdessen auch durch Aufrufen der **areaOfInterest**-API abrufen. Mithilfe dieser Informationen können Sie dann das ursprüngliche Bild ändern, wie Sie möchten.

## <a name="examples"></a>Beispiele

Das generierte Miniaturbild kann je nach den Angaben für Höhe, Breite und intelligenter Zuschneidefunktion stark variieren, wie im folgenden Bild dargestellt.

![Ein Bild eines Bergs neben verschiedenen Zuschnittkonfigurationen](./Images/thumbnail-demo.png)

In der folgenden Tabelle sind typische Miniaturbilder dargestellt, die vom maschinellen Sehen für die Beispielbilder generiert wurden. Die Miniaturbilder wurden für eine bestimmte Zielhöhe und -breite von 50 Pixel generiert, wobei die intelligente Zuschneidefunktion aktiviert ist.

| Image | Miniaturansicht |
|-------|-----------|
|![Berg bei Sonnenuntergang mit Silhouette einer Person](./Images/mountain_vista.png) | ![Miniaturansicht eines Bergs bei Sonnenuntergang mit Silhouette einer Person](./Images/mountain_vista_thumbnail.png) |
|![Eine weiße Blume vor einem grünen Hintergrund](./Images/flower.png) | ![Bildanalyse: Miniaturbild Flower](./Images/flower_thumbnail.png) |
|![Eine Frau auf dem Dach eines Wohnblocks](./Images/woman_roof.png) | ![Miniaturansicht einer Frau auf dem Dach eines Wohnhauses](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das [Taggen von Bildern](concept-tagging-images.md) und das [Kategorisieren von Bildern](concept-categorizing-images.md).
