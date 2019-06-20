---
title: Durchblättern von Suchergebnissen – Bing-Websuche-API
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie Suchergebnisse der Bing-Websuche-API durchblättern.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: a038dc2706c7cb128751630f8997851409886290
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384805"
---
# <a name="how-to-page-through-results-from-the-bing-web-search-api"></a>Durchblättern der Suchergebnisse der Bing-Websuche-API

Wenn Sie die API für die Websuche aufrufen, gibt Bing eine Liste mit Ergebnissen zurück. Bei der Liste handelt es sich um eine Teilmenge der gesamten Ergebnisse, die für die Abfrage relevant sein könnten. Um die geschätzte Gesamtzahl der verfügbaren Ergebnisse aufzurufen, greifen Sie auf das Feld [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) des Antwortobjekts zu.  

Das folgende Beispiel zeigt das `totalEstimatedMatches`-Feld mit einer Webantwort.  

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

Wenn Sie die verfügbaren Webseiten durchblättern möchten, verwenden Sie die Abfrageparameter [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#count) und [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#offset).  

Der `count`-Parameter gibt die Anzahl der Ergebnisse an, die in der Antwort zurückgegeben werden sollen. Die maximale Anzahl an Ergebnissen, die Sie in der Antwort anfordern können, ist 50. Der Standardwert ist 10. Die tatsächlich gelieferte Anzahl kann geringer sein als angefordert.

Der `offset`-Parameter gibt die Anzahl der zu überspringenden Ergebnisse an. Der `offset`-Parameter ist nullbasiert und sollte kleiner sein als (`totalEstimatedMatches` - `count`).  

Wenn Sie pro Seite 15 Webseiten anzeigen möchten, setzen Sie `count` auf 15 und `offset` auf 0, um die erste Ergebnisseite anzuzeigen. Für jede nachfolgende Seite erhöhen Sie `offset` um 15 (z.B. 15, 30).  

Im Beispiel unten werden 15 Webseiten ab Offset 45 angefordert.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Wenn der `count`-Standardwert für Ihre Implementierung das gewünschte Ergebnis erzielt, müssen Sie nur den `offset`-Abfrageparameter angeben.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Die Websuche-API gibt Ergebnisse mit Webseiten und ggf. auch Bildern, Videos und Nachrichten zurück. Wenn Sie die Suchergebnisse durchblättern, blättern Sie die [WebAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer)-Antwort und nicht die anderen Antworten wie Bilder oder Nachrichten durch. Wenn Sie `count` z.B. auf 50 festlegen, erhalten Sie als Ergebnis 50 Webseiten, die Antwort kann aber auch Ergebnisse für die anderen Antworten enthalten. Die Antwort kann beispielsweise auch 15 Bilder und 4 Nachrichtenartikeln enthalten. Es ist auch möglich, dass die Ergebnisse Nachrichten auf der ersten Seite enthalten, aber nicht auf der zweiten Seite oder umgekehrt.   

Wenn Sie den `responseFilter`-Abfrageparameter angeben, aber Webseiten nicht in die Filterliste aufnehmen, verwenden Sie nicht die Parameter `count` und `offset`. 

> [!NOTE]
> Das Feld `TotalEstimatedAnswers` gibt eine Schätzung der Gesamtzahl der Suchergebnisse an, die Sie für die aktuelle Abfrage abrufen können.  Wenn Sie die Parameter `count` und `offset` festlegen, kann sich der Wert von `TotalEstimatedAnswers` ändern. 

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Bing-Websuche-API](overview.md)?
