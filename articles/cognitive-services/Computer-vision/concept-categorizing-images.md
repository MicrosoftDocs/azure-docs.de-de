---
title: Bildkategorisierung – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Machen Sie sich mit Konzepten des Bildkategorisierungsfeatures der Maschinelles Sehen-API vertraut.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 9721ffa807c9adbeb50839113bc64fd23d8eb13f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533722"
---
# <a name="categorize-images-by-subject-matter"></a>Kategorisieren von Bildern nach Schlüsselinhalt

Zusätzlich zu Tags und Beschreibungen gibt maschinelles Sehen auf der Taxonomie basierende Kategorien zurück, die in einem Bild erkannt wurden. Im Gegensatz zu Tags sind Kategorien in einer Vererbungshierarchie mit über- und untergeordneten Elementen organisiert. Außerdem gibt es weniger davon (86, im Gegensatz zu Tausenden von Tags). Alle Kategorienamen sind in Englisch. Die Kategorisierung kann eigenständig oder parallel zum neueren Tagmodell erfolgen.

## <a name="the-86-category-concept"></a>Das 86-Kategorien-Konzept

Maschinelles Sehen kann ein Bild mithilfe der Liste mit 86 Kategorien in der folgenden Abbildung allgemein oder spezifisch kategorisieren. Die vollständige Taxonomie im Textformat finden Sie unter [Kategorietaxonomie](category-taxonomy.md).

![Gruppierte Listen aller Kategorien in der Kategorietaxonomie](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Beispiele für die Kategorisierung von Bildern

Die folgende JSON-Antwort veranschaulicht, was das maschinelle Sehen bei der Kategorisierung des Beispielbilds anhand seiner visuellen Merkmale zurückgibt.

![Eine Frau auf dem Dach eines Wohnblocks](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

Die folgende Tabelle zeigt einen typischen Satz von Bildern und die vom maschinellen Sehen zurückgegebene Kategorie für jedes Bild.

| Image | Category |
|-------|----------|
| ![Vier Personen, die zusammen als Familie posieren](./Images/family_photo.png) | people_group |
| ![Ein Welpe, der in einem Feld sitzt](./Images/cute_dog.png) | animal_dog |
| ![Eine Person, die bei Sonnenuntergang auf einem Gebirgsfelsen steht](./Images/mountain_vista.png) | outdoor_mountain |
| ![Mehrere Brötchen auf einem Tisch](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Verwenden der API

Das Kategorisierungsfeature ist Teil der [Bildanalyse-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b). Sie können diese API über ein natives SDK oder REST-Aufrufe aufrufen. Beziehen Sie `Categories` in den Abfrageparameter **visualFeatures** ein. Nachdem Sie die vollständige JSON-Antwort erhalten haben, analysieren Sie einfach die Zeichenfolge auf den Inhalt im Abschnitt `"categories"`.

* [Schnellstart: Verwenden der Clientbibliothek für maschinelles Sehen](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den verwandten Konzepten [Taggen von Bildern](concept-tagging-images.md) und [Beschreiben von Bildern](concept-describing-images.md) vertraut.
