---
title: Vordefinierte Währungsentität
titleSuffix: Azure
description: In diesem Artikel erhalten Sie Informationen zur vordefinierten Entität „currency“ in LUIS (Language Understanding Intelligent Service).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 7e882a66ae5a090e1fd3a0850ff35281dc4e692d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072026"
---
# <a name="currency-prebuilt-entity-for-a-luis-app"></a>Vordefinierte Währungsentität für eine LUIS-App
Die vordefinierte currency-Entität erkennt Währungen in vielen Bezeichnungen und Ländern/Regionen, unabhängig von der LUIS-App-Kultur. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit Währungen hinzufügen. Die currency-Entität wird in [vielen Kulturen](luis-reference-prebuilt-entities.md) unterstützt. 

## <a name="types-of-currency"></a>Arten von Währung
Die Währung wird über das GitHub-Repository [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L26) verwaltet.

## <a name="resolution-for-currency-entity"></a>Auflösung der currency-Entität

### <a name="api-version-2x"></a>API-Version 2.x

Im folgenden Beispiel wird die Auflösung der Entität **builtin.currency** veranschaulicht.

```json
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```



### <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
{
    "query": "search for items under $10.99",
    "prediction": {
        "normalizedQuery": "search for items under $10.99",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.605889857
            }
        },
        "entities": {
            "money": [
                {
                    "number": 10.99,
                    "unit": "Dollar"
                }
            ],
            "$instance": {
                "money": [
                    {
                        "type": "builtin.currency",
                        "text": "$10.99",
                        "startIndex": 23,
                        "length": 6,
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

Erfahren Sie mehr über die Entitäten [datetimeV2](luis-reference-prebuilt-datetimev2.md), [dimension](luis-reference-prebuilt-dimension.md) und [email](luis-reference-prebuilt-email.md). 
