---
title: Verwenden der Rangfolge zum Anzeigen von Antworten – Bing-Entitätssuche
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die Rangfolge zum Anzeigen der von der Bing-Entitätssuche-API zurückgegebenen Antworten verwenden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b63c2e53f34ac86f8ddf5ad300c2465ee2d9f032
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365634"
---
# <a name="using-ranking-to-display-entity-search-results"></a>Verwenden der Rangfolge zum Anzeigen von Ergebnissen der Entitätssuche  

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Jedes Ergebnis einer Entitätssuche enthält eine [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)-Antwort, die angibt, wie die von der Bing-Entitätssuche-API zurückgegebenen Suchergebnisse angezeigt werden müssen. Anhand der Rangfolgeantwort werden die Ergebnisse in Spitzenpositions-, Hauptbereichs- und Randleisteninhalte gruppiert. Das Spitzenpositionsergebnis ist das wichtigste oder hervorstechendste Ergebnis und sollte zuerst angezeigt werden. Wenn Sie die übrigen Ergebnisse nicht in einem herkömmlichen Hauptbereichs- und Randleistenformat anzeigen, müssen Sie dem Hauptbereichsinhalt höhere Sichtbarkeit als dem Randleisteninhalt zuweisen. 
  
Innerhalb jeder Gruppe gibt das [Items](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankinggroup-items)-Array die Reihenfolge an, in der die Inhalte angezeigt werden müssen. Jedes Element bietet zwei Möglichkeiten zur Angabe des Ergebnisses in einer Antwort.  
 

|Feld | Beschreibung  |
|---------|---------|
|`answerType` und `resultIndex` | `answerType` bezeichnet die Antwort (entweder eine Entität oder ein Ort) und `resultIndex` bezeichnet ein Ergebnis in dieser Antwort (z.B. eine Entität). Der Index beginnt bei 0.|
|`value`    | `value` enthält eine ID, die entweder der ID einer Antwort oder eines Ergebnisses in der Antwort entspricht. Die ID ist entweder in der Antwort oder den Ergebnissen enthalten, jedoch nicht in beidem. |
  
Die Verwendung von `answerType` und `resultIndex` ist ein zweistufiger Prozess. Verwenden Sie zuerst `answerType`, um die Antwort anzugeben, in der die anzuzeigenden Ergebnisse enthalten sind. Verwenden Sie dann `resultIndex` zum Indizieren in die Ergebnisse dieser Antwort, um das anzuzeigende Ergebnis abzurufen. (Der Wert für `answerType` ist der Name des Felds im [SearchResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#searchresponse)-Objekt.) Wenn alle Ergebnisse der Antwort zusammen angezeigt werden sollen, weist das Element der Rangfolgeantwort kein `resultIndex`-Feld auf.

Bei Verwendung der ID muss die Rangfolgen-ID mit der ID einer Antwort oder eines ihrer Ergebnisse übereinstimmen. Wenn ein Antwortobjekt ein `id`-Feld enthält, werden alle Ergebnisse der Antwort zusammen angezeigt. Enthält beispielsweise das `Entities`-Objekt das `id`-Feld, werden alle Entitätsartikel zusammen angezeigt. Wenn das `Entities`-Objekt das `id`-Feld nicht enthält, weist jede Entität ein `id`-Feld auf, und in der Rangfolgeantwort sind die Entitäten mit den Ergebnissen für Orte gemischt.  
  
## <a name="ranking-response-example"></a>Beispiel einer Rangfolgeantwort

Nachfolgend sehen Sie ein Beispiel für [RankingResponse](/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse).
  
```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "Jimi Hendrix"
  },
  "entities": { ... },
  "rankingResponse": {
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        },
        {
          "answerType": "Entities",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.1"
          }
        }
      ]
    }
  }
}
```

Auf Grundlage dieser Rangfolgeantwort werden in der Randleiste die beiden Entitätsergebnisse im Zusammenhang mit Jimi Hendrix angezeigt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](tutorial-bing-entities-search-single-page-app.md)