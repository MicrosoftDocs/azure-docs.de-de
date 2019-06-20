---
title: Vordefinierte URL-Entitäten
titleSuffix: Azure
description: In diesem Artikel erhalten Sie Informationen zur vordefinierten Entität „url“ in LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d59020eb45f7dcced5ea8da04b3b908def34fb70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072246"
---
# <a name="url-prebuilt-entity-for-a-luis-app"></a>Vordefinierte URL-Entität für eine LUIS-App
Die Entität „url“ extrahiert URLs mit Domänennamen oder IP-Adressen. Da diese Entität bereits trainiert wurde, müssen Sie der Anwendung keine Beispieläußerungen mit URLs hinzufügen. Die Entität „url“ wird nur in der Kultur `en-us` unterstützt. 

## <a name="types-of-urls"></a>url-Typen
Die Entität „url“ wird über das GitHub-Repository [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-URL.yaml) verwaltet.

## <a name="resolution-for-prebuilt-url-entity"></a>Auflösung der url-Entität

### <a name="api-version-2x"></a>API-Version 2.x

Im folgenden Beispiel wird die Auflösung der Entität **builtin.url** veranschaulicht.

```json
{
  "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.781975448
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.781975448
    }
  ],
  "entities": [
    {
      "entity": "https://www.luis.ai",
      "type": "builtin.url",
      "startIndex": 0,
      "endIndex": 17
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
{
    "query": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
    "prediction": {
        "normalizedQuery": "https://www.luis.ai is a great cognitive services example of artificial intelligence",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.421936184
            }
        },
        "entities": {
            "url": [
                "https://www.luis.ai"
            ],
            "$instance": {
                "url": [
                    {
                        "type": "builtin.url",
                        "text": "https://www.luis.ai",
                        "startIndex": 0,
                        "length": 19,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Entitäten [ordinal](luis-reference-prebuilt-ordinal.md), [number](luis-reference-prebuilt-number.md) und [temperature](luis-reference-prebuilt-temperature.md).
