---
title: API-Endpunkt für die Bing-Entitätssuche
titlesuffix: Azure Cognitive Services
description: Erfahren Sie mehr über den API-Endpunkt für die Bing-Entitätssuche und das Senden von Anforderungen an diesen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: b29e568d6b3b1382b5be434500014f10740b58f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65788546"
---
# <a name="bing-entity-search-api-endpoint"></a>API-Endpunkt für die Bing-Entitätssuche


Die API für die Bing-Entitätssuche verfügt über einen Endpunkt, der basierend auf einer Abfrage Entitäten aus dem Internet zurückgibt. Diese Suchergebnisse werden im JSON-Format zurückgegeben.

## <a name="get-entity-results-from-the-endpoint"></a>Abrufen von Entitätsergebnissen vom Endpunkt

Um mithilfe der **Bing-API** Entitätsergebnisse zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt. Verwenden Sie [Header](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#headers) und [Abfrageparameter](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#query-parameters), um Ihre Suchanforderungen anzupassen. Suchanforderungen können mithilfe des Parameters `?q=` gesendet werden.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist die Bing-Entitätssuche-API?](overview.md)

## <a name="see-also"></a>Weitere Informationen 

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie im Referenzartikel zur [Bing-Entitätssuche-API v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference).
