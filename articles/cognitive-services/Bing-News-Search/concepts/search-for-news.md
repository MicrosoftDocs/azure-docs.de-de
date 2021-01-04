---
title: Suchen nach Nachrichten mit der Bing-News-Suche-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Suchabfragen für allgemeine Nachrichten, beliebte Themen und Schlagzeilen senden.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: ecf01720126a9bf5da4aabb08653e62b42265d36
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351349"
---
# <a name="search-for-news-with-the-bing-news-search-api"></a>Suchen nach Nachrichten mit der Bing-News-Suche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Die Bing-Bildersuche-API vereinfacht das Integrieren der Funktionen der kognitiven Bing-News-Suche in Anwendungen.

Die Bing-News-Suche-API dient zwar in erster Linie zum Suchen und Zurückgeben von relevanten Nachrichtenartikeln, sie bietet jedoch auch verschiedene Funktionen für den intelligenten und gezielten Abruf von Nachrichten im Web.

## <a name="suggest-and-use-search-terms"></a>Vorschlagen und Verwenden von Suchbegriffen

Wenn Sie ein Suchfeld bereitstellen, in das Benutzer ihre Suchbegriffe eingeben, verwenden Sie die [Bing-Vorschlagssuche-API](../../bing-autosuggest/get-suggested-search-terms.md), um die Benutzerfreundlichkeit zu verbessern. Die API gibt vorgeschlagene Abfragezeichenfolgen zurück, während der Benutzer einen Suchbegriff eingibt.

Codieren Sie den vom Benutzer eingegebenen Suchbegriff als URL, bevor Sie den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#query) festlegen. Wenn der Benutzer also beispielsweise *sailing dinghies* eingibt, legen Sie `q` auf `sailing+dinghies` oder `sailing%20dinghies` fest.

## <a name="get-general-news"></a>Abrufen allgemeiner Nachrichten

Senden Sie die folgende GET-Anforderung, um allgemeine Nachrichtenartikel aus dem Web abzurufen, die mit dem Suchbegriff des Benutzers zusammenhängen:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Wenn Sie zuvor noch keine der Bing-APIs aufgerufen haben, lassen Sie den Client-ID-Header weg. Schließen Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben und Bing eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben hat.

Wenn Sie News aus einer bestimmten Domäne abrufen möchten, verwenden Sie den Abfrageoperator [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Das folgende JSON-Beispiel zeigt die Antwort auf die vorherige Abfrage. Gemäß den [Anforderungen für die Verwendung und Anzeige](../../bing-web-search/use-display-requirements.md) für die Bing-Suche-APIs müssen Sie alle Nachrichtenartikel in der in der Antwort angegebenen Reihenfolge anzeigen. Wenn der Artikel gruppierte Artikel enthält, sollten Sie angeben, dass verwandte Artikel existieren, und diese bei entsprechender Aufforderung anzeigen.

```json
{
    "_type" : "News",
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v5\/news\/search?q=sailing+dinghies",
    "totalEstimatedMatches" : 88400,
    "value" : [{
        "name" : "Sailing Vies for Four Trophies",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891FE99A72...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.9C23AA5...",
                "width" : 650,
                "height" : 341
            }
        },
        "description" : "College Rankings, presented by Zim...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "contoso.com"
        }],
        "datePublished" : "2017-04-14T15:28:00"
    },

    ...

    {
        "name" : "Fabrikam Sailing Club to host Mirror Dinghy...",
        "url" : "http:\/\/www.bing.com\/cr?IG=CCE2F06CA750455891F...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.36...",
                "width" : 448,
                "height" : 300
            }
        },
        "description" : "The sailing club that trained Olympian Ben...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-04T11:02:00",
        "category" : "Sports"
    }]
}
```

Die Antwort vom Typ [news](/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#news) enthält eine Liste mit Nachrichtenartikeln, die nach Einschätzung von Bing für die Abfrage relevant sind. Das Feld `totalEstimatedMatches` enthält die geschätzte Anzahl von Artikeln, die angezeigt werden können. Informationen zum Durchblättern der Artikel finden Sie unter [Durchblättern von Nachrichten](../../bing-web-search/paging-search-results.md).

Jeder [Nachrichtenartikel](/rest/api/cognitiveservices-bingsearch/bing-news-api-v5-reference#newsarticle) in der Liste beinhaltet den Namen des Artikels, eine Beschreibung und die URL zu dem Artikel auf der Website des Hosts. Wenn der Artikel ein Bild enthält, umfasst das Objekt eine Miniaturansicht des Bilds. Verwenden Sie `name` und `url`, um einen Hyperlink zu erstellen, über den der Benutzer zu dem Nachrichtenartikel auf der Website des Hosts geleitet wird. Wenn der Artikel ein Bild enthält, machen Sie das Bild mithilfe von `url` auch klickbar. Vergessen Sie nicht, mit `provider` die Quelle anzugeben.

Wenn Bing die Kategorie des Nachrichtenartikels bestimmen kann, enthält der Artikel das Feld `category`.

## <a name="get-todays-top-news"></a>Abrufen der Top-Nachrichten von heute

Um die Top-Nachrichtenartikel von heute zu erhalten, können Sie die gleiche allgemeine Nachrichtenanfrage wie bisher senden, wobei Sie den Parameter `q` nicht setzen.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Die Antwort für das Abrufen der wichtigsten Nachrichten ist fast die gleiche wie bei allgemeinen Nachrichten. Allerdings enthält die `news`-Antwort nicht das Feld `totalEstimatedMatches`, da eine festgelegte Anzahl von Ergebnissen vorhanden ist. Die Anzahl der wichtigsten Nachrichtenartikel kann je nach Nachrichtenzyklus variieren. Vergessen Sie nicht, mit dem `provider`-Feld die Quelle anzugeben.

## <a name="get-news-by-category"></a>Abrufen von Nachrichten nach Kategorie

Um Nachrichtenartikel nach Kategorien zu erhalten, wie z. B. die Top-Artikel aus Sport oder Unterhaltung, senden Sie die folgende GET-Anforderung an Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?category=sports&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Verwenden Sie den Abfrageparameter [category](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category), um die Kategorie der gewünschten Artikel anzugeben. Eine Liste der möglichen Nachrichtenkategorien, die Sie angeben können, finden Sie unter [Nachrichtenkategorien nach Markt](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-categories-by-market).

Die Antwort für das Abrufen von News nach Kategorie ist fast die gleiche wie bei allgemeinen Nachrichten. Allerdings stammen alle Artikel aus der angegebenen Kategorie.

## <a name="get-headline-news"></a>Abrufen von Schlagzeilen

Um Schlagzeilen-Nachrichtenartikel anzufordern und Artikel aus allen Nachrichtenkategorien zu erhalten, senden Sie die folgende GET-Anforderung an Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Schließen Sie den Abfrageparameter [category](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) nicht ein.

Die Antwort für das Abrufen der wichtigsten Schlagzeilen ist fast die gleiche wie bei allgemeinen Nachrichten. Wenn es sich bei dem Artikel um einen Schlagzeilenartikel handelt, wird sein `headline`-Feld auf **true** gesetzt.

Standardmäßig umfasst die Antwort bis zu 12 Schlagzeilenartikel. Um die Anzahl der zurückzugebenden Schlagzeilenartikel zu ändern, geben Sie den Abfrageparameter [headlineCount](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#headlinecount) an. Die Antwort enthält außerdem pro Nachrichtenkategorie bis zu vier Artikel, die keine Schlagzeilenartikel sind.

Die Antwort zählt Cluster als einen Artikel. Da ein Cluster aus mehreren Artikel bestehen kann, kann die Antwort mehr als 12 Schlagzeilenartikel und mehr als vier Nicht-Schlagzeilenartikel pro Kategorie umfassen.

## <a name="get-trending-news"></a>Abrufen von beliebten Nachrichten

Um Nachrichtenthemen zu erhalten, die in sozialen Netzwerken im Trend liegen, senden Sie die folgende GET-Anforderung an Bing:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/news/trendingtopics?mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)
X-Search-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
X-MSAPI-UserState: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!NOTE]
> „Populäre Themen“ ist nur in den Märkten en-US und zh-CN verfügbar.

Das folgende JSON zeigt die Antwort auf die vorherige Anforderung. Jeder populäre Nachrichtenartikel enthält ein zugehöriges Bild, eine Breaking News-Kennzeichnung und eine URL zu den Bing-Suchergebnissen für den Artikel. Verwenden Sie die URL im `webSearchUrl`-Feld, um den Benutzer zur Seite mit den Bing-Suchergebnissen weiterzuleiten. Oder verwenden Sie den Abfragetext, um die [Websuche-API](../../bing-web-search/overview.md) abzurufen und die Ergebnisse selbst anzuzeigen.

```json
{
    "_type" : "TrendingTopics",
    "value" : [{
        "name" : "Canada pot measure",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_hHD...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso Images"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Canada marijuana"
        }
    },
    {
        "name" : "Down on Vegas move",
        "image" : {
            "url" : "https:\/\/www.bing.com\/th?id=OPN.RTNews_Bfbmg8h...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }]
        },
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=070292D8CEDD...",
        "isBreakingNews" : false,
        "query" : {
            "text" : "Marcus Appel Las Vegas"
        }
    },

    ...

    ]
}
```

## <a name="getting-related-news"></a>Abrufen verwandter Nachrichten

Wenn es andere Artikel gibt, die sich auf einen Nachrichtenartikel beziehen, kann der Nachrichtenartikel das Feld [clusteredArticles](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle-clusteredarticles) beinhalten. Das folgende Beispiel zeigt einen Artikel mit gruppierten Artikeln.

```json
    {
        "name" : "Playoffs 2017: Betting lines, point spreads...",
        "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D115...",
        "image" : {
            "thumbnail" : {
                "contentUrl" : "https:\/\/www.bing.com\/th?id=ON.D7B1...",
                "width" : 700,
                "height" : 393
            }
        },
        "description" : "April 14, 2017 3:37pm EDT April 14, 2017 3:34pm...",
        "provider" : [{
            "_type" : "Organization",
            "name" : "Contoso"
        }],
        "datePublished" : "2017-04-14T19:43:00",
        "category" : "Sports",
        "clusteredArticles" : [{
            "name" : "Playoffs 2017: Betting odds, favorites to win...",
            "url" : "http:\/\/www.bing.com\/cr?IG=4B7056CEC271408997D1159E...",
            "description" : "April 14, 2017 3:30pm EDT April 14, 2017 3:27pm...",
            "provider" : [{
                "_type" : "Organization",
                "name" : "Contoso"
            }],
            "datePublished" : "2017-04-14T19:37:00",
            "category" : "Sports"
        }]
    },
```

## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Durchblättern der Ergebnisse der Bing-News-Suche-API](../../bing-web-search/paging-search-results.md)