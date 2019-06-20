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
ms.openlocfilehash: 43bca65810d09c87f7f473b3bbac71ca6a7f9bc2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389011"
---
# <a name="bing-entity-search-api-endpoint"></a>API-Endpunkt für die Bing-Entitätssuche


Die API für die Bing-Entitätssuche verfügt über einen Endpunkt, der basierend auf einer Abfrage Entitäten aus dem Internet zurückgibt. Diese Suchergebnisse werden im JSON-Format zurückgegeben.

## <a name="get-entity-results-from-the-endpoint"></a>Abrufen von Entitätsergebnissen vom Endpunkt

Um mithilfe der **Bing-API** Entitätsergebnisse zu erhalten, senden Sie eine `GET`-Anforderung an den folgenden Endpunkt. Verwenden Sie [Header](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#headers) und [Abfrageparameter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query-parameters), um Ihre Suchanforderungen anzupassen. Suchanforderungen können mithilfe des Parameters `?q=` gesendet werden.

```cURL
 GET https://api.cognitive.microsoft.com/bing/v7.0/entities
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was ist die Bing-Entitätssuche-API?](overview.md)

## <a name="see-also"></a>Weitere Informationen 

Weitere Informationen zu Headern, Parametern, Marktcodes, Antwortobjekten, Fehlern usw. finden Sie im Referenzartikel zur [Bing-Entitätssuche-API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference).
