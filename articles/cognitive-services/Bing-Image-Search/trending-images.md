---
title: Abrufen von beliebten Bildern über die Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Suchen Sie nach zurzeit beliebten Bildern im Web mit der Bing-Bildersuche-API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: EAB92D35-5C0B-4A0A-8F49-02DF7FAD44B4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: cf7d1baf895d44730eb913b658ee4c7fe7eb7b11
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341613"
---
# <a name="get-trending-images-from-the-web"></a>Abrufen von beliebten Bildern aus dem Web

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Um die heutigen beliebten Bilder zu erhalten, senden Sie die folgenden GET-Anforderung:  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/trending?mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Die API für beliebte Bilder unterstützt zurzeit nur die folgenden Märkte:  

- en-US (Englisch, USA)  
- en-CA (Englisch, Kanada)  
- en-AU (Englisch, Australien)  
- zh-CN (Chinesisch, China)

Die Antwort enthält einen [TrendingImages](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#trendingimages) -Objekt, das Bilder nach Kategorie aufgelistet. Verwenden Sie den `title`-Parameter der Kategorie, um die Bilder in Ihrer Benutzeroberfläche zu gruppieren. Die Kategorien können sich täglich ändern.  

```json
{
    "_type" : "TrendingImages",  
    "categories" : [{  
        "title" : "Popular people searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Smith",  
                "displayText" : "Mr. Smith",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=smith&FORM=..."
            },  
            "image" : {  
                "id" : "C3C60AE779A054D5CD80D3CACF0F3",  
                "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OIP.M2532...",  
                "contentUrl" : "http:\/\/www.contoso.com.au\/assets\/Uploads\/smith-SH01.jpg",  
                "thumbnail" : {  
                    "width" : 288,  
                    "height" : 300  
                }  
            }  
        },  
        . . .  
        ]  
    },  
    . . .  
    {  
        "title" : "Popular Halloween searches",  
        "tiles" : [{  
            "query" : {  
                "text" : "Halloween costumes for adults",  
                "displayText" : "Halloween costumes for adults",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Halloween+costumes..."
            },  
            "image" : {  
                "id" : "0F3395F2983003F89DCEE711B55D7FA53E4",  
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OIP.Me429c...",  
                "contentUrl" : "http:\/\/images.domain.com\/products\/8179\/1-1\/adult-squirrel...",  
                "thumbnail" : {  
                    "width" : 336,  
                    "height" : 480  
                }  
            }  
        }]  
    }]  
}  
```  

Jede Kachel enthält ein Bild und die Optionen zum Abrufen verwandter Bilder. Um die verwandten Bilder zu erhalten, können Sie die Abfrage `text` verwenden, um die [Bildersuche-API](./overview.md) aufzurufen und die verwandten Bilder selbst anzuzeigen. Alternativ können Sie die URL im `webSearchUrl` verwenden, um den Benutzer zur Seite der Bildersuchergebnisse von Bing zu leiten, auf der die verwandten Bilder zu finden sind.

Wenn Sie die Bildersuche-API zum Abrufen der verwandten Bilder aufrufen, legen Sie für den [Id](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#id)-Abfrageparameter die ID im `id`-Feld fest. Durch die Angabe der ID wird sichergestellt, dass die Antwort das Bild (es ist das erste Bild in der Antwort) und die verwandten Bilder enthält. Legen Sie außerdem für den [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) -Abfrageparameter den Text im `text`-Feld des `query`-Objekts fest.

Das folgende Beispiel zeigt, wie die Bild-ID verwendet wird, um verwandte Bilder von Mr. Smith in der vorhergehenden Antwort der API für beliebte Bilder zu erhalten.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=Smith&id=77FDE4A1C6529A23C7CF0EC073FAA64843E828F2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```