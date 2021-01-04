---
title: Vorschlagen von Suchbegriffen mit der Bing-Vorschlagssuche-API
titleSuffix: Azure Cognitive Services
description: In diesem Artikel wird das Konzept des Vorschlagens von Suchbegriffen mit der Bing-Vorschlagssuche-API und der Einfluss der Abfragelänge auf die Relevanz diskutiert.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: be7686c4d8a676d2a1d85516d2e4aa6abe3f3bfd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353407"
---
# <a name="suggesting-query-terms"></a>Vorschlagen von Abfrageausdrücken

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

In der Regel rufen Sie die Bing-Vorschlagssuche-API jedes Mal auf, wenn ein Benutzer ein neues Zeichen in das Suchfeld Ihrer Anwendung eingibt. Die Vollständigkeit der Abfragezeichenfolge wirkt sich auf die Relevanz der vorgeschlagenen Abfragebegriffe aus, die von der API zurückgegeben werden. Wenn die Abfragezeichenfolge vollständig ist, steigt auch die Relevanz der vorgeschlagenen Abfragebegriffe in der Liste. Beispielsweise sind die Vorschläge der API für die Eingabe `s` vermutlich weniger relevant als die Abfragen, die bei der Eingabe `sailing dinghies` zurückgegeben werden.

## <a name="example-request"></a>Beispielanforderung

Im folgenden Beispiel wird eine Anforderung gezeigt, die die vorgeschlagene Abfragezeichenfolgen für *sail* zurückgibt. Denken Sie daran, für den Teilabfragebegriff des Benutzers eine URL-Codierung durchzuführen, wenn Sie den Abfrageparameter [q](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#query) festlegen. Wenn der Benutzer also beispielsweise *sailing les* eingibt, legen Sie `q` auf `sailing+les` oder `sailing%20les` fest.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Die folgende Antwort enthält eine Liste mit [SearchAction](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#searchaction)-Objekten, in denen die vorgeschlagenen Abfragebegriffe enthalten sind.

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

Wenn der Benutzer eine vorgeschlagene Abfrage aus der Dropdownliste auswählt, können Sie den Abfragebegriff im `query`-Feld verwenden, um die [Bing-Websuche-API](../../bing-web-search/overview.md) aufzurufen und die Ergebnisse selbst anzuzeigen. Alternativ könnten Sie die URL im `url`-Feld verwenden, um den Benutzer zur Seite mit den Bing-Suchergebnissen weiterzuleiten.

## <a name="next-steps"></a>Nächste Schritte

* [Worum handelt es sich bei der Bing-Vorschlagssuche?](../get-suggested-search-terms.md)