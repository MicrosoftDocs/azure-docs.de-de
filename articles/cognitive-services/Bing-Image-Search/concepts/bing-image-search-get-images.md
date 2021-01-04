---
title: Abrufen von Bildern aus dem Web – Bing-Bildersuche-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Bing-Bildersuche-API, um nach Bildern zu suchen und diese aus dem Web abzurufen.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 988a1332d03bf2c9563ab0576f7a20ee6b0615aa
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342055"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Abrufen von Bildern aus dem Web mit der Bing-Bildersuche-API

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Wenn Sie mit der Bing-Bildersuche-REST-API Bilder aus dem Web abrufen, erhalten Sie Bilder, die sich auf Ihren Suchbegriff beziehen, indem Sie die folgende GET-Anforderung senden:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Verwenden Sie den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) für Ihren URL-codierten Suchbegriff. Wenn Sie z. B. *sailing dinghies* eingeben, legen Sie `q` auf `sailing+dinghies` oder `sailing%20dinghies` fest.

> [!IMPORTANT]
> * Alle Anforderungen müssen über einen Server erfolgen, nicht über einen Client.
> * Lassen Sie den Client-ID-Header weg, falls Sie zuvor noch keine APIs für die Bing-Suche aufgerufen haben. Binden Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben, mit der eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben wurde.

## <a name="get-images-from-a-specific-web-domain"></a>Abrufen von Bildern aus einer bestimmten Webdomäne

Wenn Sie Bilder aus einer bestimmten Domäne abrufen möchten, verwenden Sie den Abfrageoperator [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Antworten auf Abfragen, für die der Operator `site:` verwendet wird, können unter Umständen nicht jugendfreien Inhalt enthalten, und zwar unabhängig von der Einstellung [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch). Verwenden Sie `site:` nur, wenn Sie den Inhalt der Domäne kennen.

## <a name="filter-images"></a>Filtern von Bildern

 Die Bildersuche-API gibt standardmäßig alle Bilder zurück, die für die Abfrage relevant sind. Wenn Sie die von Bing zurückgegebenen Bilder filtern möchten (um z. B. nur Bilder mit einem durchsichtigen Hintergrund oder einer bestimmten Größe zurückzugeben), verwenden Sie die folgenden Abfrageparameter:

* [aspect](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect): Filtert Bilder nach Seitenverhältnis (beispielsweise Standard oder Breitbild).
* [color](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color): Filtert Bilder nach dominanter Farbe oder Schwarzweiß.
* [freshness](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness): Filtert Bilder nach Alter (beispielsweise Bilder, die von Bing in der letzten Woche gefunden wurden).
* [height](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [width](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width): Filtert Bilder nach Breite und Höhe.
* [imageContent](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent): Filtert Bilder nach Inhalt (beispielsweise Bilder, die nur das Gesicht einer Person zeigen).
* [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype): Filtert Bilder nach Typ (beispielsweise ClipArts, animierte GIFs oder transparente Hintergründe).
* [license](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license): Filtert Bilder nach Art der Websitelizenz.
* [size](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size): Filtert Bilder nach Größe (beispielsweise kleine Bilder bis zu einer Größe von 200 x 200 Pixel).

Wenn Sie Bilder aus einer bestimmten Domäne abrufen möchten, verwenden Sie den Abfrageoperator [site:](/previous-versions/bing/search/ff795613(v=msdn.10)).

Das folgende Beispiel zeigt, wie Sie von ContosoSailing.com kleine Bilder abrufen, die Bing in der letzten Woche gefunden hat.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Format von Antworten der Bing-Bildersuche

Die Antwortnachricht von Bing enthält eine [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)-Antwort mit einer Liste mit Bildern, die von Cognitive Services als für die Abfrage relevant ermittelt wurden. Jedes [Image](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image)-Objekt der Liste enthält die folgenden Informationen zum Bild: URL, Größe, Abmessungen, Codierungsformat, URL für eine Miniaturansicht des Bilds und die Abmessungen der Miniaturansicht.

> [!NOTE]
> * Bilder müssen in der Reihenfolge angezeigt werden, die von der Antwort vorgegeben wird.
> * Da sich URL-Formate und -Parameter ohne Vorankündigung ändern können, sollten Sie alle URLs in der vorliegenden Form verwenden. Es sollten keine Abhängigkeiten von URL-Formaten oder -Parametern bestehen, außer wenn dies angegeben ist.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

Wenn Sie die Bing-Bildersuche-API aufrufen, gibt Bing eine Liste mit Ergebnissen zurück. Bei der Liste handelt es sich um eine Teilmenge der gesamten Ergebnisse, die für die Abfrage relevant sind. Das Feld `totalEstimatedMatches` der Antwort enthält die geschätzte Anzahl von Bildern, die angezeigt werden können. Ausführliche Informationen zum Blättern durch die restlichen Bilder finden Sie [hier](../../bing-web-search/paging-search-results.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Schnellstartanleitung](../quickstarts/csharp.md), falls Sie die Bing-Bildersuche-API noch nicht ausprobiert haben. Falls Sie nach einem komplexeren Ansatz suchen, können Sie das Tutorial zur Erstellung einer [Einzelseiten-Web-App](../tutorial-bing-image-search-single-page-app.md) ausprobieren.