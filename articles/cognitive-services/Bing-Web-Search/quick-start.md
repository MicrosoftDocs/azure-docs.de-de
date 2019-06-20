---
title: 'Schnellstart: Websuche-API'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel lernen Sie die ersten Schritte mit der Bing-Websuche-API kennen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 27B4B51A-D017-44C8-8E4E-9684DC553886
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/17/2019
ms.author: scottwhi
ms.openlocfilehash: 7f165ca6f2fa0fed550d454277081958cd276633
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390665"
---
# <a name="your-first-bing-search-query"></a>Ihre erste Bing-Suchabfrage

Bevor Sie Ihren ersten Aufruf vornehmen können, benötigen Sie einen Cognitive Services-Abonnementschlüssel. Unter [Cognitive Services ausprobieren](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) erfahren Sie, wie Sie einen Schlüssel erhalten. Siehe auch [Cognitive Services-Preise – Bing-Suche-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)

Um nur Websuchergebnisse zu erhalten, senden Sie eine GET-Anforderung an den folgenden Endpunkt:  

```
https://api.cognitive.microsoft.com/bing/v7.0/search
```  

Die Anforderung muss das HTTPS-Protokoll verwenden.

Alle Anforderungen sollten von einem Server stammen. Das Verteilen eines Schlüssels als Teil einer Clientanwendung birgt ein größeres Risiko, dass eine böswillige dritte Partei darauf zugreift. Aufrufe von einem Server stellen zudem einen einzelnen Upgradepunkt für zukünftige Versionen der API dar.  

Die Anforderung muss den Abfrageparameter [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) angeben, der den Suchbegriff des Benutzers enthält. Obwohl er optional ist, sollte die Anforderung auch den Abfrageparameter [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#mkt) angeben, wodurch der Markt identifiziert wird, von dem die Ergebnisse stammen sollen. Unter [Abfrageparameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query-parameters) finden Sie eine Liste optionaler Abfrageparameter wie `responseFilter` und `textDecorations`. Alle Abfrageparameterwerte müssen URL-codiert sein.  

Die Anforderung muss den [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#subscriptionkey)-Header angeben. Auch wenn dies optional ist, sollten Sie die folgenden Header ebenfalls angeben:  

-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#location)  

Die Client-IP- und Adressheader sind für die Rückgabe von standortbezogenem Inhalt wichtig. Bei der Benutzerabfrage *sailing+lessons* beispielsweise werden aller Wahrscheinlichkeit nach Kurse gesucht, die in der Nähe gelegen sind. Wenn die Ergebnisse Kurse enthalten sollen, die in der Nähe des Benutzerstandorts verfügbar sind, müssen Sie den Standortheader und optional den Client-IP-Header einschließen. Hierbei ist weniger entscheidend, ob im Abfrageausdruck explizit ein Standort (z.B. „sailing+lessons+florida+keys“) erwähnt wird.

Eine Liste aller Anforderungs- und Antwortheader finden Sie unter [Header](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers).

## <a name="the-request"></a>Die Anforderung

Im Folgenden ist eine Suchanforderung dargestellt, die alle vorgeschlagenen Abfrageparameter und -header enthält. Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+lessons+seattle&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Das folgende Beispiel zeigt die Antwort auf die vorherige Anforderung. Im Beispiel sind auch die Bing-spezifischen Antwortheader aufgeführt.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Seattle Sailing Club | Lessons, Membership & More",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/seattlesailing.com",
            "snippet" : "Sailing is fun! Seattle Sailing Club makes it accessible and affordable...",
            "deepLinks" : [{
                "name" : "Learn to Sail",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD..."
            },
            {
                "name" : "Fleet",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
                "snippet" : "The Seattle Sailing Club is nothing without our fleet of excellent..."
            },
            {
                "name" : "Sailing Lessons",
                "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
                "snippet" : "Sailing Lessons. Taking the first step into the sailing community..."
            },
            {
                "name" : "Membership",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD3...",
                "snippet" : "Seattle sailboat rental for less than the cost of moorage!"
            },
            {
                "name" : "Hours – Directions",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874...",
                "snippet" : "Contact the Seattle Sailing Club by phone or email..."
            },
            {
                "name" : "Overnight Cruising",
                "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD...",
                "snippet" : "Overnight Cruising. Setting sail for a multi-day adventure..."
            }],
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1",
            "name" : "Best Sailing lessons in Seattle, WA - yelp.com",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38AA...",
            "displayUrl" : "https:\/\/www.yelp.com\/search?find_desc=sailing+lessons&find_loc...",
            "snippet" : "Reviews on Sailing lessons in Seattle, WA - Seattle Sailing...",
            "dateLastCrawled" : "2017-04-01T10:49:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2",
            "name" : "Seattle Sailing with Windworks Sailing Club - Sailing ...",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD3...",
            "displayUrl" : "https:\/\/www.windworkssailing.com",
            "snippet" : "Seattle sailing club for sailing lessons, sailboat charters...",
            "dateLastCrawled" : "2017-04-09T03:33:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3",
            "name" : "Sailing Lessons in Downtown Seattle - SheSails Seattle",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDAD38...",
            "displayUrl" : "shesailsseattle.com",
            "snippet" : "SheSails Seattle is sailing lessons and sailboat charters in a fun...",
            "dateLastCrawled" : "2017-04-05T07:15:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4",
            "name" : "Seattle Sailing Club - 19 Photos & 27 Reviews - Boating ...",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.yelp.com\/biz\/seattle-sailing-club-seattle",
            "snippet" : "27 reviews of Seattle Sailing Club \"Love the Seattle Sailing...",
            "dateLastCrawled" : "2017-04-08T14:40:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5",
            "name" : "Puget Sound Sailing - Seattle and Tacoma - PUGET SOUND ...",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FEFDA...",
            "displayUrl" : "www.pugetsoundsailing.com",
            "snippet" : "Puget Sound Sailing Institute is an American Sailing Association...",
            "dateLastCrawled" : "2017-04-09T01:39:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.6",
            "name" : "Seattle Yacht Club - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "www.seattleyachtclub.org",
            "snippet" : "Seattle Yacht Club Awarded Joe Prosser Trophy for Excellence...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        }],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        },
        {
            "text" : "sailing schools seattle",
            "displayText" : "sailing schools seattle",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4..."
        },
        {
            "text" : "seattle boating lessons",
            "displayText" : "seattle boating lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4..."
        },
        {
            "text" : "adult sailing lessons seattle",
            "displayText" : "adult sailing lessons seattle",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED..."
        },
        {
            "text" : "seattle sailing club",
            "displayText" : "seattle sailing club",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED459..."
        },
        {
            "text" : "seattle sailing club lessons",
            "displayText" : "seattle sailing club lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289..."
        },
        {
            "text" : "sailing in seattle area",
            "displayText" : "sailing in seattle area",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED45948..."
        },
        {
            "text" : "seattle sailing lessons lake union",
            "displayText" : "seattle sailing lessons lake union",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED459..."
        }]
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
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 6,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.6"
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

## <a name="next-steps"></a>Nächste Schritte

Testen Sie die API. Besuchen Sie die [Testkonsole für die Bing-Websuche-API](https://dev.cognitive.microsoft.com/docs/services/56b43eeccf5ff8098cef3807/operations/56b4447dcf5ff8098cef380d).

Ausführliche Details zur Nutzung der Antwortobjekte finden Sie auf der Seite [Internetsuchen](./search-the-web.md).
