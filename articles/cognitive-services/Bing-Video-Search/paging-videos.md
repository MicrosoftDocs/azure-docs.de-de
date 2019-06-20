---
title: Durchblättern verfügbarer Videos – Bing-Videosuche
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie alle von der Bing-Videosuche-API zurückgegebenen Videos durchblättern können.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 12549bb53a21dd657f51a4a02460ddc82c47bef8
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386384"
---
# <a name="paging-through-video-search-results"></a>Durchblättern in den Suchergebnissen der Videosuche

Die Bing-Videosuche-API gibt eine Teilmenge aller Suchergebnisse zurück, die für Ihre Abfrage gefunden wurden. Wenn Sie diese Ergebnisse mit nachfolgenden Aufrufen der API durchblättern, können Sie sie in Ihrer Anwendung abrufen und anzeigen.

> [!NOTE]
> Das Durchblättern gilt nur für die Videosuche (/videos/search) und nicht Videoeinblicke (/videos/details) oder beliebte Videos (/videos/trending).

## <a name="total-estimated-matches"></a>Geschätzte Gesamtanzahl der Übereinstimmungen

Um die geschätzte Anzahl der gefundenen Suchergebnisse abzurufen, verwenden Sie das Feld [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-totalestimatedmatches) in der JSON-Antwort.   
  
```json  
{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
    "totalEstimatedMatches" : 1000,
    "value" : [...]
}  
```  
  
## <a name="paging-through-videos"></a>Durchblättern von Videos

Wenn Sie die verfügbaren Videos durchblättern möchten, verwenden Sie beim Senden Ihrer Anforderung die Abfrageparameter [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) und [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset).  
  

|Parameter  |BESCHREIBUNG  |
|---------|---------|
|`count`     | Gibt die Anzahl der Ergebnisse an, die in der Antwort zurückgegeben werden sollen. Die maximale Anzahl an Ergebnissen, die Sie in der Antwort anfordern können, ist 100. Der Standardwert ist 10. Die tatsächlich gelieferte Anzahl kann geringer sein als angefordert.        |
|`offset`     | Gibt die Anzahl der zu überspringenden Ergebnisse an. Der `offset`-Parameter ist nullbasiert und sollte kleiner sein als (`totalEstimatedMatches` - `count`).          |

Wenn Sie pro Seite z.B. 20 Artikel anzeigen möchten, setzen Sie `count` auf 20 und `offset` auf 0, um die erste Ergebnisseite anzuzeigen. Für jede nachfolgende Seite erhöhen Sie `offset` um 20 (z.B. 20, 40).  
  
Im Beispiel unten werden 20 Videos ab Offset 40 angefordert.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Wenn Sie den Standardwert für den Abfrageparameter [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#count) verwenden, müssen Sie nur den Abfrageparameter `offset` angeben, wie im folgenden Beispiel.  
  
```cURL  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

Wenn Sie 35 Videos gleichzeitig durchblättern, würden Sie den Abfrageparameter `offset` bei Ihrer ersten Anforderung auf 0 setzen und `offset` dann bei jeder weiteren Anfragen um 35 erhöhen. Einige Ergebnisse in der nächsten Antwort können jedoch doppelte Videoergebnisse aus der vorherigen Antwort enthalten. Beispielsweise können die ersten beiden Videos in einer Antwort die gleichen sein wie die letzten beiden Videos der vorherigen Antwort.

Um doppelte Ergebnisse zu eliminieren, verwenden Sie das [nextOffset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos-nextoffset)-Feld des `Videos`-Objekts.

Wenn Sie beispielsweise 30 Videos gleichzeitig durchblättern möchten, können Sie `count` in Ihrer ersten Anforderung auf 30 und `offset` auf 0 festlegen. In der nächsten Anforderung würden Sie den `offset`-Abfrageparameter auf `nextOffset` festlegen.

> [!NOTE]
> Das Feld `TotalEstimatedAnswers` gibt eine Schätzung der Gesamtzahl der Suchergebnisse an, die Sie für die aktuelle Abfrage abrufen können.  Wenn Sie die Parameter `count` und `offset` festlegen, kann sich der Wert von `TotalEstimatedAnswers` ändern. 
  
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Gewinnen von Erkenntnissen aus Videos](video-insights.md)
