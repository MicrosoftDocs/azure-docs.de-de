---
title: Senden von Suchanforderungen an die Bing-Entitätssuche-API
titleSuffix: Azure cognitive Services
description: Die Bing-Entitätssuche-API sendet eine Suchabfrage an Bing und erhält Ergebnisse, die Entitäten und Orte umfassen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: ad6d89fec9f2c94129e19c09ee3e1e76d5bb6e44
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353271"
---
# <a name="sending-search-requests-to-the-bing-entity-search-api"></a>Senden von Suchanforderungen an die Bing-Entitätssuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-Entitätssuche-API sendet eine Suchabfrage an Bing und erhält Ergebnisse, die Entitäten und Orte umfassen. Zu Orten gehören Restaurants, Hotels oder andere lokale Geschäfte. Für Orte kann die Abfrage den Namen des lokalen Unternehmens angeben oder nach einer Liste fragen (z.B. Restaurants in meiner Nähe). Entitätsergebnisse enthalten Personen, Orte oder Gegenstände. Orte sind in diesem Zusammenhang touristische Attraktionen, Staaten, Länder/Regionen usw.

## <a name="the-endpoint"></a>Der Endpunkt

Um Suchergebnisse für Entitäten und Orte zu erhalten, senden Sie eine GET-Anforderung an den folgenden Endpunkt:  

```
https://api.cognitive.microsoft.com/bing/v7.0/entities
```

Anforderungen müssen das HTTPS-Protokoll verwenden.

Alle Anforderungen sollten von einem Server stammen. Das Verteilen eines Schlüssels als Teil einer Clientanwendung bietet mehr Gelegenheiten, dass eine böswillige dritte Partei darauf zugreift. Aufrufe von einem Server stellen zudem einen einzelnen Upgradepunkt für zukünftige Versionen der API dar.

## <a name="specifying-query-parameters-and-headers"></a>Angeben von Abfrageparametern und Header

Die Anforderung muss den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) angeben, der den Suchbegriff des Benutzers enthält. Die Anforderung muss auch den [mkt](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mkt)-Abfrageparameter angeben, wodurch der Markt identifiziert wird, von dem die Ergebnisse stammen sollen. Unter [Abfrageparameter](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters) finden Sie eine Liste optionaler Abfrageparameter. Alle Abfrageparameter müssen URL-codiert sein.  
  
Die Anforderung muss den [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#subscriptionkey)-Header angeben. Auch wenn dies optional ist, sollten Sie die folgenden Header ebenfalls angeben:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientid)  
-   [X-MSEdge-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#location)  

Die Client-IP- und Adressheader sind für die Rückgabe von standortorientiertem Inhalt wichtig.  

Eine Liste aller Anforderungs- und Antwortheader finden Sie unter [Header](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers).

## <a name="the-request"></a>Die Anforderung.

Im Folgenden ist eine Entitätsanforderung dargestellt, die alle vorgeschlagenen Abfrageparameter und -header enthält. 

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/entities?q=mount+rainier&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat.

## <a name="the-response"></a>Die Antwort

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Im Beispiel sind auch die Bing-spezifischen Antwortheader aufgeführt. Informationen über das Antwortobjekt finden Sie unter [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse).

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "mount rainier"
    },
    "entities" : {
        "queryScenario" : "DominantEntity",
        "value" : [{
            "contractualRules" : [{
                "_type" : "ContractualRules\/LicenseAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "license" : {
                    "name" : "CC-BY-SA",
                    "url" : "http:\/\/creativecommons.org\/licenses\/by-sa\/3.0\/"
                },
                "licenseNotice" : "Text under CC-BY-SA license"
            },
            {
                "_type" : "ContractualRules\/LinkAttribution",
                "targetPropertyName" : "description",
                "mustBeCloseToContent" : true,
                "text" : "en.wikipedia.org",
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            },
            {
                "_type" : "ContractualRules\/MediaAttribution",
                "targetPropertyName" : "image",
                "mustBeCloseToContent" : true,
                "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
            }],
            "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Mount%20Rainier...",
            "name" : "Mount Rainier",
            "image" : {
                "name" : "Mount Rainier",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A21890c0e1f...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Mount_Rainier"
                }],
                "hostPageUrl" : "http:\/\/upload.wikimedia.org\/wikipedia...",
                "width" : 110,
                "height" : 110
            },
            "description" : "Mount Rainier, Mount Tacoma, or Mount Tahoma is the highest...",
            "entityPresentationInfo" : {
                "entityScenario" : "DominantEntity",
                "entityTypeHints" : ["Attraction"],
                "entityTypeDisplayHint" : "Mountain"
            },
            "bingId" : "9ae3e6ca-81ea-6fa1-ffa0-42e1d78906"
        }]
    }
}
```


## <a name="next-steps"></a>Nächste Schritte

* [Suche nach Entitäten mit der Bing-Entitäts-API](search-for-entities.md)
* [Verwendung der Bing-API und Anzeigen von Anforderungen](../../bing-web-search/use-display-requirements.md)