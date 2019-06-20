---
title: Durchblättern der Ergebnisse der Bing-News-Suche
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie durch die Nachrichtenartikel blättern, die von der Bing-News-Suche-API zurückgegeben werden.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: 1eab92dcc9c1890e82f9999e26e54378a3687c6d
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390495"
---
# <a name="how-to-page-through-news-search-results"></a>Durchblättern der Ergebnisse der News-Suche

Wenn Sie die News-Suche-API aufrufen, gibt Bing eine Liste mit Ergebnissen zurück, die für Ihre Abfrage relevant sind. Um die geschätzte Gesamtzahl der verfügbaren Ergebnisse aufzurufen, greifen Sie auf das Feld [totalEstimatedMatches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news-totalmatches) des Antwortobjekts zu.  
  
Das folgende Beispiel zeigt das `totalEstimatedMatches`-Feld mit einer News-Antwort.  

```json
{  
    "_type" : "News",  
    "readLink" : "https:\/\/api.cognitive.microsoft.com\/bing\/v7\/news\/search?q=sailing+dinghies",  
    "totalEstimatedMatches" : 88400,  
    "value" : [...]  
}  
```  
  
Wenn Sie die verfügbaren Artikel durchblättern möchten, verwenden Sie die Abfrageparameter [count](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#count) und [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#offset).  
 

|Parameter  |BESCHREIBUNG  |
|---------|---------|
|`count`     | Gibt die Anzahl der Ergebnisse an, die in der Antwort zurückgegeben werden sollen. Die maximale Anzahl an Ergebnissen, die Sie in der Antwort anfordern können, ist 100. Der Standardwert ist 10. Die tatsächlich gelieferte Anzahl kann geringer sein als angefordert.        |
|`offset`     | Gibt die Anzahl der zu überspringenden Ergebnisse an. Der `offset`-Parameter ist nullbasiert und sollte kleiner sein als (`totalEstimatedMatches` - `count`).          |

Wenn Sie pro Seite z.B. 20 Artikel anzeigen möchten, setzen Sie `count` auf 20 und `offset` auf 0, um die erste Ergebnisseite anzuzeigen. Für jede nachfolgende Seite erhöhen Sie `offset` um 20 (z.B. 20, 40).  
  
Im Beispiel unten werden 20 Nachrichtenartikel ab Offset 40 angefordert.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&count=20&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  
  
Wenn der `count`-Standardwert für Ihre Implementierung das gewünschte Ergebnis erzielt, geben Sie nur den `offset`-Abfrageparameter an, wie im folgenden Beispiel dargestellt:  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/news/search?q=sailing+dinghies&offset=40&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Das Durchblättern kann nur für die Nachrichtensuche (/news/search), und nicht für Trendthemen (/news/trendingtopics) oder News-Kategorien (/news) angewendet werden.

> [!NOTE]
> Das Feld `TotalEstimatedAnswers` gibt eine Schätzung der Gesamtzahl der Suchergebnisse an, die Sie für die aktuelle Abfrage abrufen können.  Wenn Sie die Parameter `count` und `offset` festlegen, kann sich der Wert von `TotalEstimatedAnswers` ändern. 
