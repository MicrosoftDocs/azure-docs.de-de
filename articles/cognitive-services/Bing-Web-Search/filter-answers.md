---
title: 'Gewusst wie: Filtern von Suchergebnissen – Bing-Websuche-API'
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Suchergebnisse der Bing-Websuche-API filtern und anzeigen können.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: 8d8fd03d9c3d912788e9893377bbab3efac86f8a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383835"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtern der Ergebnisse, die die Suchantwort enthält  

Wenn Sie das Web abfragen, gibt Bing alle Inhalte zurück, die für die Suche relevant sind. Wenn die Suchabfrage z.B. „sailing+dinghies“ lautet, kann die Antwort die folgenden Ergebnisse enthalten:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```
Sie können die empfangenen Inhalte mit dem Abfrageparameter [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) nach Typ filtern (z.B. Bilder, Videos und News). Wenn Bing relevante Inhalte für die angegebenen Antworten findet, werden diese zurückgegeben. Der Antwortfilter ist eine durch Trennzeichen getrennte Liste mit Antworten. 

Um bestimmte Inhaltstypen wie z.B. Bilder aus der Antwort auszuschließen, können Sie das Zeichen `-` am Anfang des `responseFilter`-Werts hinzufügen. Sie trennen ausgeschlossene Inhaltstypen durch ein Komma (`,`) voneinander ab. Beispiel:

```
&responseFilter=-images,-videos
```


Im folgenden wird gezeigt, wie `responseFilter` zum Anfordern von Bildern, Videos und News zu Segeljollen verwendet wird. Wenn Sie die Abfragezeichenfolge codieren, ändern sich die Trennzeichen in %2C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Das folgende Beispiel zeigt die Antwort auf die vorherige Abfrage. Da Bing keine relevanten Video- und Newsergebnisse gefunden hat, enthält die Antwort diese auch nicht.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Obwohl Bing in der vorherigen Antwort keine Video- und News-Ergebnisse zurückgegeben hat, bedeutet dies nicht, dass es keine Video- und News-Inhalte gibt. Es bedeutet lediglich, dass sie nicht auf der Seite enthalten waren. Wenn Sie jedoch weitere [Seiten](./paging-webpages.md) mit Ergebnissen anzeigen, werden die nachfolgenden Seiten solche Ergebnisse wahrscheinlich enthalten. Auch wenn Sie die [Videosuche-API](../bing-video-search/search-the-web.md)- und [News-Suche-API](../bing-news-search/search-the-web.md)-Endpunkte direkt aufrufen, enthält die Antwort wahrscheinlich Ergebnisse.

Es wird davon abgeraten, `responseFilter` zu verwenden, um Ergebnisse von einer einzigen API zu erhalten. Wenn Sie Inhalte von einer einzelnen Bing-API wünschen, rufen Sie diese API direkt auf. Um beispielsweise nur Bilder zu erhalten, senden Sie eine Anforderung an den Bildersuche-API-Endpunkt, an `https://api.cognitive.microsoft.com/bing/v7.0/images/search` oder einen der anderen [Bilder](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints)-Endpunkte. Der Aufruf der einzelnen API ist nicht nur aus Leistungsgründen wichtig, sondern auch, weil die inhaltsspezifischen APIs reichhaltigere Ergebnisse liefern. Sie können z.B. Filter zum Filtern der Ergebnisse verwenden, die für die Web-API nicht verfügbar sind.  

Um Suchergebnisse von einer bestimmten Domäne zu erhalten, schließen Sie den Abfrageoperator `site:` in die Abfragezeichenfolge ein.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Bei Verwendung des Abfrageoperators `site:` kann es je nach Abfrage vorkommen, dass die Antwort ggf nicht jugendfreie Inhalte enthält (unabhängig von der Einstellung [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch)). Verwenden Sie `site:` nur, wenn Sie wissen, welche Inhalte die Website enthält, und wenn in Ihrem Szenario ggf. auch nicht jugendfreie Inhalte zulässig sind.

## <a name="limiting-the-number-of-answers-in-the-response"></a>Einschränken der Anzahl von Ergebnissen in der Antwort

Bing schließt Ergebnisse in die Antwort basierend auf der Rangfolge ein. Wenn Sie z.B. *sailing+dinghies* abfragen, gibt Bing `webpages`, `images`, `videos` und `relatedSearches` zurück.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Um die Anzahl der Antworten einzuschränken, die Bing an die beiden wichtigsten Ergebnisse (Webseiten und Bilder) zurückgibt, legen Sie den Abfrageparameter [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) auf 2 fest.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Die Antwort enthält nur `webPages` und `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Wenn Sie den Abfrageparameter `responseFilter` zur vorherigen Abfrage hinzufügen und ihn auf Webseiten und News festlegen, enthält die Antwort nur Webseiten, da News keine Rangfolge haben.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Höherstufen von Antworten, die keine Rangfolge besitzen

Wenn die am besten bewerteten Antworten, die Bing für eine Abfrage zurückgibt, Webseiten, Bilder, Videos und relatedSearches sind, würde die Antwort diese Ergebnisse enthalten. Wenn Sie [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) auf 2 festlegen, gibt Bing die beiden Ergebnisse mit der höchsten Rangfolge zurück: Webseiten und Bilder. Wenn Bing Bilder und Videos in die Antwort einbeziehen soll, geben Sie den Abfrageparameter [promote](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) an und legen ihn auf Bilder und Videos fest.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Das folgende Beispiel zeigt die Antwort auf die Anforderung oben. Bing gibt die am besten bewerteten zwei Antworten (Webseiten und Bilder) zurück und stuft Videos höher, sodass diese in die Antwort aufgenommen werden.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Wenn Sie `promote` auf News festlegen, enthält die Antwort kein News-Ergebnis, da dies keine Antwort mit Rangfolge ist &mdash; Sie können nur Antworten mit Rangfolge höher stufen.

Die Antworten, die Sie höher stufen möchten, zählen nicht für den `answerCount`-Grenzwert. Wenn die Antworten mit Rangfolge z.B. News, Bilder und Videos sind, und legen Sie `answerCount` auf 1 und `promote` auf News fest, enthält die Antwort News und Bilder. Wenn die Antworten mit Rangfolge Videos, Bilder und News sind, enthält die Antwort Videos und News.

Sie können `promote` nur verwenden, wenn Sie den Abfrageparameter `answerCount` angeben.
