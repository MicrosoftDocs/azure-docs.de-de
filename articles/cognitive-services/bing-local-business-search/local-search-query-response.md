---
title: 'Senden und Verwenden von API-Abfragen und -Antworten: Bing-Suche für ortsansässige Unternehmen'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie Suchabfragen mit der API für die Bing-Suche nach ortsansässigen Unternehmen senden und verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: rosh
ms.openlocfilehash: 70a33774ac82312660d887fb86f7e2a482c30a0c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487166"
---
# <a name="sending-and-using-bing-local-business-search-api-queries-and-responses"></a>Senden und Verwenden von Abfragen und Antworten der API für die Bing-Suche nach ortsansässigen Unternehmen

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Sie können mit der API für die Bing-Suche nach ortsansässigen Unternehmen lokale Ergebnisse abrufen, indem Sie eine Suchabfrage an ihren Endpunkt senden und den erforderlichen Header `Ocp-Apim-Subscription-Key` angeben. Die Suche lässt sich anhand der verfügbaren [Header](local-search-reference.md#headers) und [Parameter](local-search-reference.md#query-parameters) sowie durch Angeben von [geografischen Begrenzungen](specify-geographic-search.md) des zu durchsuchenden Bereichs und [Kategorien](local-search-query-response.md) der zurückgegebenen Orte anpassen.

## <a name="creating-a-request"></a>Erstellen einer Anforderung

Um eine Anforderung an die API für die Bing-Suche nach ortsansässigen Unternehmen zu senden, fügen Sie einen Suchbegriff an den Parameter `q=` an, bevor Sie ihn dem API-Endpunkt hinzufügen, einschließlich des Headers `Ocp-Apim-Subscription-Key`. Beispiel:

`https://api.cognitive.microsoft.com/bing/localbusinesses/v7.0/search?q=restaurant+in+Bellevue`

Die vollständige Anforderungs-URL-Syntax ist unten dargestellt. Weitere Informationen zum Senden von Anforderungen finden Sie in den [Schnellstarts](quickstarts/local-quickstart.md) für die API für die Bing-Suche nach ortsansässigen Unternehmen sowie in den Referenzinhalten für [Header](local-search-reference.md#headers) und [Parameter](local-search-reference.md#query-parameters). 

Weitere Informationen zu lokalen Suchkategorien finden Sie unter [Suchkategorien für die API für die Bing-Suche nach ortsansässigen Unternehmen](local-categories.md).

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search[?q][&localCategories][&cc][&mkt][&safesearch][&setlang][&count][&first][&localCircularView][&localMapView]
```

## <a name="using-responses"></a>Verwenden von Antworten

JSON-Antworten aus der API für die Bing-Suche nach ortsansässigen Unternehmen enthalten ein `SearchResponse`-Objekt. Die API gibt relevante Suchergebnisse im Feld `places` zurück. Wenn keine Ergebnisse gefunden werden, wird das Feld `places` nicht in die Antwort aufgenommen.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

```
{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": "restaurant in Bellevue"
   },
   "places": {
      "totalEstimatedMatches": 10,
. . . 
```

### <a name="search-result-attributes"></a>Suchergebnisattribute

Die von der API zurückgegebenen JSON-Ergebnisse beinhalten die folgenden Attribute:

* _type
* address
* entityPresentationInfo
* Geografischer Raum
* id
* name
* routeablePoint
* telephone
* url

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie in der Referenz für die [API für die Bing-Suche nach ortsansässigen Unternehmen v7](local-search-reference.md).

> [!NOTE]
> Weder Sie noch Dritte in Ihrem Namen dürfen Daten aus der API für die Bing-Suche nach ortsansässigen Unternehmen für Tests, Entwicklung, Schulung, Verteilung oder Bereitstellung eines Nicht-Microsoft-Diensts oder einer Nicht-Microsoft-Funktion verwenden, beibehalten, speichern, zwischenspeichern, freigeben oder verteilen. 


## <a name="example-json-response"></a>JSON-Beispielantwort

Die folgende JSON-Antwort enthält Suchergebnisse, die durch die Abfrage `?q=restaurant+in+Bellevue` angegeben wurden.

```json
Vary: Accept-Encoding
BingAPIs-TraceId: 5376FFEB65294E24BB9F91AD70545826
BingAPIs-SessionId: 06ED7CEC80F746AA892EDAAC97CB0CB4
X-MSEdge-ClientID: 112C391E72C0624204153594738C63DE
X-MSAPI-UserState: aeab
BingAPIs-Market: en-US
X-Search-ResponseInfo: InternalResponseTime=659,MSDatacenter=CO4
X-MSEdge-Ref: Ref A: 5376FFEB65294E24BB9F91AD70545826 Ref B: BY3EDGE0306 Ref C: 2018-10-16T16:26:15Z
apim-request-id: fe54f585-7c54-4bf5-8b92-b9bede2b710a
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
Cache-Control: max-age=0, private
Date: Tue, 16 Oct 2018 16:26:15 GMT
P3P: CP="NON UNI COM NAV STA LOC CURa DEVa PSAa PSDa OUR IND"
Content-Length: 978
Content-Type: application/json; charset=utf-8
Expires: Tue, 16 Oct 2018 16:25:15 GMT

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "restaurant Bellevue"
  },
  "places": {
    "totalEstimatedMatches": 50,
    "value": [{
      "_type": "LocalBusiness",
      "id": "https:\/\/cognitivegblppe.azure-api.net\/api\/v7\/#Places.0",
      "name": "Facing East Taiwanese Restaurant",
      "url": "http:\/\/litadesign.wix.com\/facingeastrestaurant",
      "entityPresentationInfo": {
        "entityScenario": "ListItem",
        "entityTypeHints": ["Place", "LocalBusiness", "Restaurant"]
      },
      "geo": {
        "latitude": 47.6199188232422,
        "longitude": -122.202796936035
      },
      "routablePoint": {
        "latitude": 47.6199188232422,
        "longitude": -122.201713562012
      },
      "address": {
        "streetAddress": "1075 Bellevue Way NE Ste B2",
        "addressLocality": "Bellevue",
        "addressRegion": "WA",
        "postalCode": "98004",
        "addressCountry": "US",
        "neighborhood": "Bellevue",
        "text": "1075 Bellevue Way NE Ste B2, Bellevue, WA 98004"
      },
      "telephone": "(425) 688-2986"
    }],
    "searchAction": {
      "location": [{
        "name": "Bellevue, Washington"
      }],
      "query": "restaurant"
    }
  }
}
 
```

## <a name="throttling-requests"></a>Drosselungsanforderungen

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="next-steps"></a>Nächste Schritte
- [Schnellstart: Suche nach ortsansässigen Unternehmen](quickstarts/local-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Java](quickstarts/local-search-java-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Node](quickstarts/local-search-node-quickstart.md)
- [Schnellstart: Suche nach ortsansässigen Unternehmen mit Python](quickstarts/local-search-python-quickstart.md)