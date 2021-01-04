---
title: Verwenden von Rangfolgen zur Anzeige von Suchergebnissen – Bing-Websuche-API
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mithilfe von Rangfolgen Suchergebnisse aus der Bing-Websuche-API anzeigen können.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 20501d0993cc4566a79d6e916d801911606bea35
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380448"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>Verwenden von Rangfolgen zur Anzeige von Ergebnissen der Bing-Websuche-API  

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Jede Suchantwort enthält eine [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)-Antwort, die angibt, wie die Suchergebnisse angezeigt werden müssen. Anhand der Rangfolgeantwort werden die Ergebnisse für eine herkömmliche Suchergebnisseite nach Hauptbereichsinhalt und Randleisteninhalt gruppiert. Wenn Sie die Ergebnisse nicht in einem herkömmlichen Hauptbereichs- und Randleistenformat anzeigen, müssen Sie dem Hauptbereichsinhalt höhere Sichtbarkeit als dem Randleisteninhalt zuweisen.  

Innerhalb jeder Gruppe (Hauptbereich oder Randleiste) gibt das [Items](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items)-Array die Reihenfolge an, in der die Inhalte angezeigt werden müssen. Jedes Element bietet die beiden folgenden Möglichkeiten zur Angabe des Ergebnisses in einer Antwort.  

-   `answerType` und `resultIndex`: Das `answerType`-Feld bezeichnet die Antwort (z.B. Webseite oder News) und `resultIndex` bezeichnet ein Ergebnis in der Antwort (z.B. einen News-Artikel). Der Index ist nullbasiert.  

-   `value`: Das `value`-Feld enthält eine ID, die entweder der ID einer Antwort oder eines Ergebnisses in der Antwort entspricht. Die ID ist entweder in der Antwort oder den Ergebnissen enthalten, jedoch nicht in beidem.  

Die Verwendung der ID ist einfacher, da nur die Rangfolgen-ID mit der ID einer Antwort oder eines ihrer Ergebnisse übereinstimmen muss. Wenn ein Antwortobjekt ein `id`-Feld enthält, werden alle Ergebnisse der Antwort zusammen angezeigt. Enthält beispielsweise das `News`-Objekt das `id`-Feld, werden alle News-Artikel zusammen angezeigt. Wenn das `News`-Objekt das `id`-Feld nicht enthält, weist jeder News-Artikel ein `id`-Feld auf, und in der Rangfolgeantwort sind die News-Artikel mit den Ergebnissen aus anderen Antworten gemischt.  

Die Verwendung von `answerType` und `resultIndex` ist etwas komplizierter. Sie verwenden `answerType`, um die Antwort anzugeben, in der die anzuzeigenden Ergebnisse enthalten sind. Dann verwenden Sie `resultIndex` zum Indizieren über die Ergebnisse der Antwort, um das anzuzeigende Ergebnis abzurufen. (Der Wert für `answerType` ist der Name des Felds im [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse)-Objekt.) Wenn alle Ergebnisse der Antwort zusammen angezeigt werden sollen, weist das Element der Rangfolgeantwort kein `resultIndex`-Feld auf.  

## <a name="ranking-response-example"></a>Beispiel einer Rangfolgeantwort

Nachfolgend sehen Sie ein Beispiel für [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse). Da die Webantwort kein `id`-Feld enthält, werden alle Webseiten einzeln basierend auf der Rangfolge angezeigt (jede Webseite enthält ein `id`-Feld). Da die Bilder, Videos und Antworten verwandter Suchvorgänge das `id`-Feld enthalten, werden die Ergebnisse jeder dieser Antworten zusammen basierend auf der Rangfolge angezeigt.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

Basierend auf dieser Rangfolgeantwort werden im Hauptbereich die folgenden Suchergebnisse angezeigt:  

-   Das Ergebnis der ersten Webseite
-   Alle Bilder  
-   Die Ergebnisse der zweiten und dritten Webseite  
-   Alle Videos  
-   Die Ergebnisse der vierten, fünften und sechsten Webseite  

In der Randleiste werden die folgenden Suchergebnisse angezeigt:  

-   Alle verwandten Suchvorgänge  


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Höherstufen von Ergebnissen ohne Rangfolge finden Sie unter [Höherstufen von Antworten, die keine Rangfolge besitzen](./filter-answers.md#promoting-answers-that-are-not-ranked).

Informationen zum Einschränken der Anzahl von Ergebnissen mit Rangfolge in der Antwort finden Sie unter [Einschränken der Anzahl von Ergebnissen in der Antwort](./filter-answers.md#limiting-the-number-of-answers-in-the-response).

Ein C#-Beispiel, bei dem die Rangfolge zum Anzeigen von Ergebnissen verwendet wird, finden Sie unter [C#-Tutorial zur Rangfolge](./csharp-ranking-tutorial.md).