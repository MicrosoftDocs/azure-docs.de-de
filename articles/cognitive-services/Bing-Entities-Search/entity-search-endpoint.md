---
title: API-Endpunkt für die Bing-Entitätssuche
titleSuffix: Azure Cognitive Services
description: Die API für die Bing-Entitätssuche verfügt über einen Endpunkt, der basierend auf einer Abfrage Entitäten aus dem Internet zurückgibt. Diese Suchergebnisse werden im JSON-Format zurückgegeben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 7fc65b27c3c02d6102210ae277690795d763d91a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338256"
---
# <a name="bing-entity-search-api-endpoint"></a>API-Endpunkt für die Bing-Entitätssuche

> [!WARNING]
> Die APIs der Bing-Suche werden von Cognitive Services auf Bing-Suchdienste umgestellt. Ab dem **30. Oktober 2020** müssen alle neuen Instanzen der Bing-Suche mit dem [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) dokumentierten Prozess bereitgestellt werden.
> APIs der Bing-Suche, die mit Cognitive Services bereitgestellt wurden, werden noch drei Jahre lang bzw. bis zum Ablauf Ihres Enterprise Agreement unterstützt (je nachdem, was zuerst geschieht).
> Eine Anleitung zur Migration finden Sie unter [Bing-Suchdienste](/bing/search-apis/bing-web-search/create-bing-search-service-resource).


Die API für die Bing-Entitätssuche verfügt über einen Endpunkt, der basierend auf einer Abfrage Entitäten aus dem Internet zurückgibt. Diese Suchergebnisse werden im JSON-Format zurückgegeben.

## <a name="get-entity-results-from-the-endpoint"></a>Abrufen von Entitätsergebnissen vom Endpunkt

Um mithilfe der **Bing-API** Entitätsergebnisse zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt. Verwenden Sie [Header](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) und [Abfrageparameter](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters), um Ihre Suchanforderungen anzupassen. Suchanforderungen können mithilfe des Parameters `?q=` gesendet werden.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist die Bing-Entitätssuche-API?](overview.md)

## <a name="see-also"></a>Weitere Informationen 

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie im Referenzartikel zur [Bing-Entitätssuche-API v7](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).