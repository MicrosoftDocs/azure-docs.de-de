---
title: Vorschlagen von Suchbegriffen mit der Bing-Vorschlagssuche-API
titlesuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie4 Sie die Bing-Vorschlagssuche-API verwenden.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 7cd81a937a3fe24e16e3f77e38686378aa28e10a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382676"
---
# <a name="suggesting-query-terms"></a>Vorschlagen von Abfrageausdrücken

In der Regel rufen Sie die Bing-Vorschlagssuche-API jedes Mal auf, wenn ein Benutzer ein neues Zeichen in das Suchfeld Ihrer Anwendung eingibt. Die Vollständigkeit der Abfragezeichenfolge wirkt sich auf die Relevanz der vorgeschlagenen Abfragebegriffe aus, die von der API zurückgegeben werden. Wenn die Abfragezeichenfolge vollständig ist, steigt auch die Relevanz der vorgeschlagenen Abfragebegriffe in der Liste. Beispielsweise sind die Vorschläge der API für die Eingabe `s` vermutlich weniger relevant als die Abfragen, die bei der Eingabe `sailing dinghies` zurückgegeben werden.

## <a name="example-request"></a>Beispielanforderung

Im folgenden Beispiel wird eine Anforderung gezeigt, die die vorgeschlagene Abfragezeichenfolgen für *sail* zurückgibt. Denken Sie daran, für den Teilabfragebegriff des Benutzers eine URL-Codierung durchzuführen, wenn Sie den Abfrageparameter [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) festlegen. Wenn der Benutzer also beispielsweise *sailing les* eingibt, legen Sie `q` auf `sailing+les` oder `sailing%20les` fest.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Die folgende Antwort enthält eine Liste mit [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction)-Objekten, in denen die vorgeschlagenen Abfragebegriffe enthalten sind.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Verwenden von vorgeschlagenen Abfrageausdrücken

Jeder Vorschlag enthält die Felder `displayText`, `query` und `url`. Das Feld `displayText` enthält die vorgeschlagene Abfrage, die Sie zum Auffüllen der Dropdownliste des Suchfelds verwenden. Sie müssen alle in der Antwort enthaltenen Vorschläge in der angegebenen Reihenfolge anzeigen.

Das folgende Beispiel zeigt ein Dropdownsuchfeld mit vorgeschlagenen Suchbegriffen der Bing-Vorschlagssuche-API.

![Dropdownliste des Suchfelds der Vorschlagssuche](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Wenn der Benutzer eine vorgeschlagene Abfrage aus der Dropdownliste auswählt, können Sie den Abfragebegriff im `query`-Feld verwenden, um die [Bing-Websuche-API](../../bing-web-search/search-the-web.md) aufzurufen und die Ergebnisse selbst anzuzeigen. Alternativ könnten Sie die URL im `url`-Feld verwenden, um den Benutzer zur Seite mit den Bing-Suchergebnissen weiterzuleiten.

## <a name="next-steps"></a>Nächste Schritte

* [Worum handelt es sich bei der Bing-Vorschlagssuche?](../get-suggested-search-terms.md)