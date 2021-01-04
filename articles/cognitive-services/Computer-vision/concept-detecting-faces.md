---
title: Gesichtserkennung – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Machen Sie sich mit Konzepten des Gesichtserkennungsfeatures der API für maschinelles Sehen vertraut.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3aa8f9057c3c9e3a141824a9d23e9be8d0bbe4b2
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532634"
---
# <a name="face-detection-with-computer-vision"></a>Gesichtserkennung mit maschinellem Sehen

Maschinelles Sehen erkennt menschliche Gesichter auf einem Bild und generiert für jedes erkannte Gesicht das Alter, Geschlecht und Rechteck. 

> [!NOTE]
> Dieses Feature wird auch vom Azure-Dienst [Gesichtserkennung](../face/index.yml) geboten. Diese Alternative bietet eine detailliertere Gesichtsanalyse, einschließlich Gesichtsidentifikation und Posenerkennung. 

## <a name="face-detection-examples"></a>Beispiele für die Gesichtserkennung

Das folgende Beispiel zeigt die JSON-Antwort, die von Maschinelles Sehen für ein Bild mit einem einzelnen menschlichen Gesicht zurückgegeben wird.

![Bildanalyse: Woman Roof Face](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Das nächste Beispiel zeigt die JSON-Antwort, die für ein Bild mit mehreren menschlichen Gesichtern zurückgegeben wird.

![Bildanalyse: Family Photo Face](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Verwenden der API

Die Funktion zur Gesichtserkennung ist Teil der [Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)-API. Sie können diese API über ein natives SDK oder REST-Aufrufe aufrufen. Beziehen Sie `Faces` in den Abfrageparameter **visualFeatures** ein. Nachdem Sie die vollständige JSON-Antwort erhalten haben, analysieren Sie einfach die Zeichenfolge auf den Inhalt im Abschnitt `"faces"`.

* [Schnellstart: Verwenden der Clientbibliothek für maschinelles Sehen](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
