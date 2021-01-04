---
title: Senden von Suchanforderungen an die Bing-Videosuche-API
titleSuffix: Azure Cognitive Services
description: In diesem Artikel werden die Parameter und Attribute von Anforderungen beschrieben, die an die Bing-Videosuche-API gesendet werden. Außerdem finden Sie hier Informationen zum zurückgegebenen JSON-Antwortobjekt.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: aahi
ms.openlocfilehash: 78a3c3c2936bfcaf58ea9e46bd3d6a610cdfe436
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341494"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Senden von Suchanforderungen an die Bing-Videosuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In diesem Artikel werden die Parameter und Attribute von Anforderungen beschrieben, die an die Bing-Videosuche-API gesendet werden. Außerdem finden Sie hier Informationen zum zurückgegebenen JSON-Antwortobjekt. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Vorschlagen von Suchbegriffen mit der Bing-Vorschlagssuche-API

Wenn Sie ein Suchfeld bereitstellen, in das Benutzer ihre Suchbegriffe eingeben, verwenden Sie die [Bing-Vorschlagssuche-API](../../bing-autosuggest/get-suggested-search-terms.md), um die Benutzerfreundlichkeit zu verbessern. Die API gibt vorgeschlagene Abfragezeichenfolgen zurück, während der Benutzer einen Suchbegriff eingibt.

Codieren Sie den vom Benutzer eingegebenen Suchbegriff als URL, bevor Sie den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) festlegen. Wenn der Benutzer also beispielsweise *sailing dinghies* eingibt, legen Sie `q` auf `sailing+dinghies` oder `sailing%20dinghies` fest.

## <a name="sending-a-request"></a>Senden einer Anforderung

Um Videosuchergebnisse zu erhalten, senden Sie eine GET-Anforderung an den folgenden Endpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
Die Anforderung muss das HTTPS-Protokoll verwenden.

Alle Anforderungen sollten von einem Server stammen. Das Verteilen eines Schlüssels als Teil einer Clientanwendung bietet mehr Gelegenheiten, dass eine böswillige dritte Partei darauf zugreift. Aufrufe von einem Server stellen zudem einen einzelnen Upgradepunkt für zukünftige Versionen der API dar.

  
Die Anforderung muss den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query) angeben, der den Suchbegriff des Benutzers enthält. Obwohl er optional ist, sollte die Anforderung auch den Abfrageparameter [mkt](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt) angeben, wodurch der Markt identifiziert wird, von dem die Ergebnisse stammen sollen. Unter [Abfrageparameter](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters) finden Sie eine Liste optionaler Abfrageparameter wie `pricing`. Alle Abfrageparameterwerte müssen URL-codiert sein.  
  
Die Anforderung muss den [Ocp-Apim-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey)-Header angeben. Auch wenn dies optional ist, sollten Sie die folgenden Header ebenfalls angeben:  
  
-   [User-Agent](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

Die Client-IP- und Adressheader sind für die Rückgabe von standortorientiertem Inhalt wichtig.  

Eine Liste aller Anforderungs- und Antwortheader finden Sie unter [Header](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers).

## <a name="example-search-request"></a>Beispiel für eine Suchanforderung

Im Folgenden ist eine Suchanforderung dargestellt, die alle vorgeschlagenen Abfrageparameter und -header enthält. Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>JSON-Beispielantwort

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Im Beispiel sind auch die Bing-spezifischen Antwortheader aufgeführt.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die API. Besuchen Sie die [Testkonsole für die Videosuche-API](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Ausführliche Informationen zur Nutzung der Antwortobjekte finden Sie unter [Suchen nach Videos im Web](../overview.md).

Details zum Abrufen von Auswertungen zu einem Video, z.B. verwandte Suchvorgänge, finden Sie unter [Videoauswertungen](../video-insights.md).  
  
Weitere Informationen über Videos, die in sozialen Medien beliebt sind, finden Sie unter [Beliebte Videos](../trending-videos.md).