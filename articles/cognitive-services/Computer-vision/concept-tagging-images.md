---
title: Anwenden von Inhaltstags auf Bilder – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Machen Sie sich mit Konzepten des Bildmarkierungsfeatures der Maschinelles Sehen-API vertraut.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: aeb03566a650fe46286d77913e0d36dcbb19f436
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759672"
---
# <a name="applying-content-tags-to-images"></a>Anwenden von Inhaltstags auf Bilder

Maschinelles Sehen gibt Tags basierend auf Tausenden erkennbaren Objekten, Lebewesen, Landschaften und Aktionen zurück. Wenn Tags nicht eindeutig sind oder nicht zum Allgemeinwissen gehören, enthält die API-Antwort „Hinweise“, um die Bedeutung des Tags in einem bekannten Kontext zu erläutern. Tags sind nicht als eine Taxonomie organisiert, und es sind keine Vererbungshierarchien vorhanden. Eine Auflistung von Inhaltstags bildet die Grundlage für eine „Bildbeschreibung“, die in einer für Menschen lesbaren Sprache in vollständigen Sätzen angezeigt wird. Beachten Sie, dass derzeit nur Englisch als Sprache für Bildbeschreibungen unterstützt wird.

Nachdem Sie ein Bild hochgeladen oder eine Bild-URL angegeben haben, geben die Algorithmen für das maschinelle Sehen basierend auf den Objekten, Lebewesen und Aktionen aus, die im Bild erkannt werden. Das Tagging ist nicht auf den Hauptinhalt (z.B. eine Person im Vordergrund) beschränkt, sondern umfasst auch die Umgebung (Innen- oder Außenbereich), Möbel, Werkzeuge, Pflanzen, Tiere, Zubehör, Geräte usw.

## <a name="image-tagging-example"></a>Beispiel zum Taggen von Bildern

Die folgende JSON-Antwort veranschaulicht, was vom maschinellen Sehen beim Taggen von visuellen Merkmalen zurückgegeben wird, die im Beispielbild erkannt wurden.

![Ein blaues Haus mit Vorgarten](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Konzepte zum [Kategorisieren von Bildern](concept-categorizing-images.md) und [Beschreiben von Bildern](concept-describing-images.md).
