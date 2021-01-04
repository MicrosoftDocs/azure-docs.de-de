---
title: Abrufen von Videoinformationen mithilfe der Bing-Videosuche-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Bing-Videosuche-API verwenden, um weitere Informationen zu Videos abzurufen, z.B. verwandte Videos.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: cbbde354c1bc68a2b1017c3ccba61b846fa62916
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380758"
---
# <a name="get-insights-about-a-video"></a>Abrufen von Auswertungen zu einem Video

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Jedes Video von der Bing-Videosuche-API zurückgegebene Video enthält eine Video-ID, die Sie verwenden können, um weitere Informationen zum Video (z.B. verwandte Videos) abzurufen. Verwenden Sie zum Abrufen von Auswertungen zu einem Video dessen [videoId](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video-videoid)-Token in der API-Antwort. 

```json
    "value" : [
        {
            . . .
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear...",
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHBZ--g",
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63",
            . . .
        }
    ],
```

Senden Sie dann eine GET-Anforderung an den Videodetailsendpunkt mit der ID. Legen Sie den [id](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#id)-Abfrageparameter auf das `videoId`-Token fest. Um die Auswertungen anzugeben, die Sie abrufen möchten, legen Sie den [modules](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested)-Abfrageparameter fest. Um alle Auswertungen abzurufen, legen Sie `modules` auf „Alle“ fest. Die Antwort enthält alle Auswertungen, die Sie angefordert haben (wenn verfügbar).

```cURL
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
``` 

## <a name="getting-related-videos-insights"></a>Abrufen von Auswertungen zu verwandten Videos  

Um Videos abzurufen, die mit dem angegebenen Video verwandt sind, legen Sie den [modules](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested)-Abfrageparameter auf `RelatedVideos` fest.
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/details?q=sailiing+dinghies&id=6DB795E11A6E3CBAAD636DB795E11A6E3CBAAD63&modules=RelatedVideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Die Antwort auf diese Anforderung wird ein [VideoDetails](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)-Objekt auf oberster Ebene anstelle eines [Videos](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos)-Objekts enthalten.  
  
```json
{
    "_type" : "Api.VideoDetails.VideoDetails",
    "relatedVideos" : {
        "value" : [
            {
                "name" : "How to sail - Reefing a Sail",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OVP.zt...",
                "datePublished" : "2014-03-04T16:11:09",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=7284B07...",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?...",
                "duration" : "PT4M56S",
                "motionThumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OM...",
                "allowHttpsEmbed" : true,
                "viewCount" : 21756,
                "videoId" : "AC1A157A4DDB571D03D6AC157A4DDB571D03D6",
                "allowMobileEmbed" : false,
                "isSuperfresh" : false
            },
            . . .
        ]
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Suchen Sie nach beliebten Videos](trending-videos.md)