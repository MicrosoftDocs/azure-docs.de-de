---
title: 'Anpassen von Bildersuchabfragen und Nutzen von Vorschlägen: Bing-Bildersuche-API'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Suchabfragen an die Bing-Bildersuche-API anpassen.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: 2566b2cf950df915f8ea843c34ea1fb6f8e7ea21
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342004"
---
# <a name="customize-and-suggest-image-search-queries"></a>Anpassen von Bildersuchabfragen und Nutzen von Vorschlägen

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Artikel wird beschrieben, wie Sie Abfragen anpassen und Suchbegriffe vorschlagen, die an die Bing-Bildersuche-API gesendet werden.

## <a name="suggest-search-terms"></a>Vorschlagen von Suchbegriffen

Wenn Ihre App über ein Suchfeld verfügt, in das Suchbegriffe eingegeben werden, können Sie die [Bing-Vorschlagssuche-API](../../bing-autosuggest/get-suggested-search-terms.md) zum Verbessern der Benutzerfreundlichkeit verwenden. Die API kann vorgeschlagene Suchbegriffe in Echtzeit anzeigen. Die API gibt vorgeschlagene Abfragezeichenfolgen zurück, die auf Teilen von Suchbegriffen und Cognitive Services basieren.

## <a name="pivot-the-query"></a>Pivotieren der Abfrage

Wenn Bing die ursprüngliche Suchabfrage segmentieren kann, enthält das Objekt [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) den Wert `pivotSuggestions`. Pivotiertvorschläge können dem Benutzer als optionale Suchbegriffe angezeigt werden. Lautet die ursprüngliche Abfrage also beispielsweise *Microsoft Surface*, kann Bing die Abfrage in *Microsoft* und *Surface* segmentieren und dafür jeweils Pivotvorschläge anzeigen. Diese Vorschläge können dem Benutzer als optionale Abfragebegriffe angezeigt werden.

Das folgende Beispiel veranschaulicht die Pivotvorschläge für *Microsoft Surface*:  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Das Feld `pivotSuggestions` enthält die Liste mit den Segmenten (Pivotwerte), in die die ursprüngliche Abfrage aufgeteilt wurde. Die Antwort enthält für jeden Pivotwert eine Liste mit Objekten vom Typ [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj), die wiederum vorgeschlagene Abfragen enthalten. Das Feld `text` enthält die vorgeschlagene Abfrage. Das Feld `displayText` enthält den Ausdruck, der den Pivotwert in der ursprünglichen Abfrage ersetzt. Beispiel: Veröffentlichungsdatum von Surface.

Wenn der Benutzer nach der Pivotabfragezeichenfolge sucht, verwenden Sie die Felder `text` und `thumbnail`, um die Pivotabfragezeichenfolgen anzuzeigen. Verwenden Sie die URL `webSearchUrl` oder `searchLink`, damit das Miniaturbild bzw. der Text angeklickt werden können. Verwenden Sie `webSearchUrl`, um den Benutzer an die Bing-Suchergebnisse zu senden. Wenn Sie Ihre eigene Ergebnisseite angeben, verwenden Sie `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Erweitern der Abfrage

Wenn Bing die Abfrage erweitern kann, um die ursprüngliche Suche einzugrenzen, enthält das Objekt [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) das Feld `queryExpansions`. Wenn die Abfrage z. B. *Microsoft Surface* lautete, können die erweiterten Abfragen wie folgt aussehen:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

Das folgende Beispiel zeigt die erweiterten Abfragen für *Microsoft Surface*:

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Das Feld `queryExpansions` enthält eine Liste mit Objekten vom Typ [Query](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj). Das Feld `text` enthält die erweiterte Abfrage. Das Feld `displayText` enthält den Erweiterungsbegriff. Wenn der Benutzer nach der erweiterten Abfragezeichenfolge sucht, verwenden Sie die Felder `text` und `thumbnail`, um die erweiterte Abfragezeichenfolgen anzuzeigen. Verwenden Sie die URL `webSearchUrl` oder `searchLink`, damit das Miniaturbild bzw. der Text angeklickt werden können. Verwenden Sie `webSearchUrl`, um den Benutzer an die Bing-Suchergebnisse zu senden. Wenn Sie Ihre eigene Ergebnisseite angeben, verwenden Sie `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Schnellstartanleitung](../quickstarts/csharp.md), falls Sie die Bing-Bildersuche-API noch nicht ausprobiert haben. Falls Sie nach einem komplexeren Ansatz suchen, können Sie das Tutorial zur Erstellung einer [Einzelseiten-Web-App](../tutorial-bing-image-search-single-page-app.md) ausprobieren.