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
ms.openlocfilehash: f169f969a1acf4cefc8cee27f74a99730491176a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389411"
---
# <a name="get-images-from-the-web-with-the-bing-image-search-api"></a>Abrufen von Bildern aus dem Web mit der Bing-Bildersuche-API

Wenn Sie mit der Bing-Bildersuche-REST-API Bilder aus dem Web abrufen, erhalten Sie Bilder, die sich auf Ihren Suchbegriff beziehen, indem Sie die folgende GET-Anforderung senden:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Alle Anforderungen müssen über einen Server erfolgen, nicht über einen Client.
> * Lassen Sie den Client-ID-Header weg, falls Sie zuvor noch keine APIs für die Bing-Suche aufgerufen haben. Binden Sie die Client-ID nur ein, wenn Sie bereits eine Bing-API aufgerufen haben, mit der eine Client-ID für die Kombination aus Benutzer und Gerät zurückgegeben wurde.
> * Bilder müssen in der Reihenfolge angezeigt werden, die von der Antwort vorgegeben wird.

## <a name="get-images-from-a-specific-web-domain"></a>Abrufen von Bildern aus einer bestimmten Webdomäne

Wenn Sie Bilder aus einer bestimmten Domäne abrufen möchten, verwenden Sie den Abfrageoperator [site:](https://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Antworten auf Abfragen, für die der Operator `site:` verwendet wird, können unter Umständen nicht jugendfreien Inhalt enthalten, und zwar unabhängig von der Einstellung [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch). Verwenden Sie `site:` nur, wenn Sie den Inhalt der Domäne kennen.

Das folgende Beispiel zeigt, wie Sie von ContosoSailing.com kleine Bilder abrufen, die Bing in der letzten Woche gefunden hat.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filter-images"></a>Filtern von Bildern

 Die Bildersuche-API gibt standardmäßig alle Bilder zurück, die für die Abfrage relevant sind. Wenn Sie die von Bing zurückgegebenen Bilder filtern möchten (um z. B. nur Bilder mit einem durchsichtigen Hintergrund oder einer bestimmten Größe zurückzugeben), verwenden Sie die folgenden Abfrageparameter:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#aspect): Filtert Bilder nach Seitenverhältnis (beispielsweise Standard oder Breitbild).
* [color](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#color): Filtert Bilder nach dominanter Farbe oder Schwarzweiß.
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#freshness): Filtert Bilder nach Alter (beispielsweise Bilder, die von Bing in der letzten Woche gefunden wurden).
* [height](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#width): Filtert Bilder nach Breite und Höhe.
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagecontent): Filtert Bilder nach Inhalt (beispielsweise Bilder, die nur das Gesicht einer Person zeigen).
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype): Filtert Bilder nach Typ (beispielsweise ClipArts, animierte GIFs oder transparente Hintergründe).
* [license](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license): Filtert Bilder nach Art der Websitelizenz.
* [size](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#size): Filtert Bilder nach Größe (beispielsweise kleine Bilder bis zu einer Größe von 200 x 200 Pixel).

Wenn Sie Bilder aus einer bestimmten Domäne abrufen möchten, verwenden Sie den Abfrageoperator [site:](https://msdn.microsoft.com/library/ff795613.aspx).

 > [!NOTE]
 > Antworten auf Abfragen, für die der Operator `site:` verwendet wird, können unter Umständen nicht jugendfreien Inhalt enthalten, und zwar unabhängig von der Einstellung [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch). Verwenden Sie `site:` nur, wenn Sie den Inhalt der Domäne kennen.

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

Die Antwortnachricht von Bing enthält eine [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images)-Antwort mit einer Liste mit Bildern, die von Cognitive Services als für die Abfrage relevant ermittelt wurden. Jedes [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image)-Objekt der Liste enthält die folgenden Informationen zum Bild: URL, Größe, Abmessungen, Codierungsformat, URL für eine Miniaturansicht des Bilds und die Abmessungen der Miniaturansicht.

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

Wenn Sie die Bing-Bildersuche-API aufrufen, gibt Bing eine Liste mit Ergebnissen zurück. Bei der Liste handelt es sich um eine Teilmenge der gesamten Ergebnisse, die für die Abfrage relevant sind. Das Feld `totalEstimatedMatches` der Antwort enthält die geschätzte Anzahl von Bildern, die angezeigt werden können. Ausführliche Informationen zum Blättern durch die restlichen Bilder finden Sie [hier](../paging-images.md).

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die [Schnellstartanleitung](../quickstarts/csharp.md), falls Sie die Bing-Bildersuche-API noch nicht ausprobiert haben. Falls Sie nach einem komplexeren Ansatz suchen, können Sie das Tutorial zur Erstellung einer [Einzelseiten-Web-App](../tutorial-bing-image-search-single-page-app.md) ausprobieren.
