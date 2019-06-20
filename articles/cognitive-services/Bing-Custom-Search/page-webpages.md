---
title: Durchblättern verfügbarer Webseiten | Benutzerdefinierte Bing-Suche
titlesuffix: Azure Cognitive Services
description: Zeigt, wie Sie alle Webseiten durchblättern, die die benutzerdefinierte Bing-Suche zurückgeben kann.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 3c1bf9c6f2c1b38b9cf9729b769c9198da56147a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388584"
---
# <a name="paging-webpages"></a>Durchsuchen von Webseiten 

Wenn Sie die API für die benutzerdefinierte Suche aufrufen, gibt Bing eine Liste mit Ergebnissen zurück. Bei der Liste handelt es sich um eine Teilmenge der gesamten Ergebnisse, die für die Abfrage relevant sein könnten. Um die geschätzte Gesamtzahl der verfügbaren Ergebnisse aufzurufen, greifen Sie auf das Feld [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#totalestimatedmatches) des Antwortobjekts zu.  
  
Das folgende Beispiel zeigt das `totalEstimatedMatches`-Feld mit einer Webantwort.  
  
```  
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```  
  
Wenn Sie die verfügbaren Webseiten durchblättern möchten, verwenden Sie die Abfrageparameter [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#count) und [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#offset).  
  
Der `count`-Parameter gibt die Anzahl der Ergebnisse an, die in der Antwort zurückgegeben werden sollen. Die maximale Anzahl an Ergebnissen, die Sie in der Antwort anfordern können, ist 50. Der Standardwert ist 10. Die tatsächlich gelieferte Anzahl kann geringer sein als angefordert.

Der `offset`-Parameter gibt die Anzahl der zu überspringenden Ergebnisse an. Der `offset`-Parameter ist nullbasiert und sollte kleiner sein als (`totalEstimatedMatches` - `count`).  
  
Wenn Sie pro Seite 15 Webseiten anzeigen möchten, setzen Sie `count` auf 15 und `offset` auf 0, um die erste Ergebnisseite anzuzeigen. Für jede nachfolgende Seite erhöhen Sie `offset` um 15 (z.B. 15, 30).  
  
Unten sehen Sie ein Beispiel, bei dem 15 Webseiten ab Offset 45 angefordert werden.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>
Host: api.cognitive.microsoft.com  
```  

Wenn der `count`-Standardwert für Ihre Implementierung das gewünschte Ergebnis erzielt, müssen Sie nur den `offset`-Abfrageparameter angeben.  
  
```  
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us&customConfig=123456 HTTP/1.1  
Ocp-Apim-Subscription-Key: <subscription ID>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Das Feld `TotalEstimatedAnswers` gibt eine Schätzung der Gesamtzahl der Suchergebnisse an, die Sie für die aktuelle Abfrage abrufen können.  Wenn Sie die Parameter `count` und `offset` festlegen, kann sich der Wert von `TotalEstimatedAnswers` ändern. 

