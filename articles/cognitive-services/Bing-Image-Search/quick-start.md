---
title: Schnellstart für die Bildersuche-API | Microsoft-Dokumentation
description: In diesem Artikel lernen Sie die ersten Schritte mit der Bing-Bildersuche-API kennen.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: BE2B2F8C-20B5-4E0B-AEC8-EAD505BA4C85
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 03/04/2019
ms.author: scottwhi
ms.openlocfilehash: 006b611bd2dcc94b1b651de7789e165a5c45a17b
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383771"
---
# <a name="your-first-images-search-query"></a>Ihre erste Bildersuchabfrage

Bevor Sie Ihren ersten Aufruf vornehmen können, benötigen Sie einen Cognitive Services-Abonnementschlüssel für die Bing-Suche. Unter [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api) erfahren Sie, wie Sie einen Schlüssel erhalten.  Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

Um nur Bildersuchergebnisse zu erhalten, senden Sie eine GET-Anforderung an den folgenden Endpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search
```
  
Die Anforderung muss das HTTPS-Protokoll verwenden.

Alle Anforderungen sollten von einem Server stammen. Das Verteilen eines Schlüssels als Teil einer Clientanwendung birgt ein größeres Risiko, dass eine böswillige dritte Partei darauf zugreift. Aufrufe von einem Server stellen zudem einen einzelnen Upgradepunkt für zukünftige Versionen der API dar.

Die Anforderung muss den Abfrageparameter [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) angeben, der den Suchbegriff des Benutzers enthält. Obwohl er optional ist, sollte die Anforderung auch den Abfrageparameter [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#mkt) angeben, wodurch der Markt identifiziert wird, von dem die Ergebnisse stammen sollen. Unter [Abfrageparameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters) finden Sie eine Liste optionaler Abfrageparameter wie `freshness` und `size`. Alle Abfrageparameterwerte müssen URL-codiert sein.  
  
Die Anforderung muss den [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#subscriptionkey)-Header angeben. Auch wenn dies optional ist, sollten Sie die folgenden Header ebenfalls angeben:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#location)  

Die Client-IP- und Adressheader sind für die Rückgabe von standortorientiertem Inhalt wichtig.  

Eine Liste aller Anforderungs- und Antwortheader finden Sie unter [Header](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers).

## <a name="the-request"></a>Die Anforderung

Im Folgenden ist eine Suchanforderung dargestellt, die alle vorgeschlagenen Abfrageparameter und -header enthält. Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung.

```json
{
    "_type" : "Images",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "totalEstimatedMatches" : 838,
    "value" : [
        {
            "name" : "File:Rich Passage Minto Sailing Dinghy.jpg - Wikipedia",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
            "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
            "datePublished" : "2011-10-29T11:26:00",
            "contentUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
            "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
            "contentSize" : "79239 B",
            "encodingFormat" : "jpeg",
            "hostPageDisplayUrl" : "en.wikipedia.org\/wiki\/File:Rich_Passage...",
            "width" : 526,
            "height" : 688,
            "thumbnail" : {
                "width" : 229,
                "height" : 300
            },
            "imageInsightsToken" : "ccid_GNarK7ma*mid_CCF85447ADA6...",
            "insightsSourcesSummary" : {
                "shoppingSourcesCount" : 0,
                "recipeSourcesCount" : 0
            },
            "imageId" : "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
            "accentColor" : "376094"
        },
        . . .
    ],
    "queryExpansions" : [
        {
            "text" : "Small Sailing Dinghies",
            "displayText" : "Small",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118C8B4...",
            "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail" : {
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
            }
        },
        . . .
    ],
    "nextOffset" : 10,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Tender",
                    "displayText" : "Tender",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=73118...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com\/api...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?q=..."
                    }
                },
                . . .
            ]
        }
    ],
    "displayShoppingSourcesBadges" : false,
    "displayRecipeSourcesBadges" : true
}
```

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die API. Navigieren Sie zur [Testkonsole für die Bildersuche-API](https://dev.cognitive.microsoft.com/docs/services/8336afba49a84475ba401758c0dbf749/operations/571fab09dbe2d933e891028f). 

Ausführliche Details zur Nutzung der Antwortobjekte finden Sie auf der Seite [Internetsuchen](./search-the-web.md).

Einzelheiten zum Abrufen von Informationen über ein Bild (z.B. Webseiten, die das Bild enthalten, oder Personen, die auf dem Bild erkannt wurden), finden Sie unter [Abrufen von Informationen über Bilder](./image-insights.md).  
  
Weitere Informationen über Bilder, die in sozialen Medien beliebt sind, finden Sie unter [Beliebte Bilder](./trending-images.md).  
