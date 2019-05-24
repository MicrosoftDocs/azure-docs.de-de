---
title: Vordefinierte Age-Entität
titleSuffix: Azure
description: In diesem Artikel erhalten Sie Informationen zur vorgefertigten Altersentität in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b8239688000f0ce32ca2c2be054b1443bbb698b5
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65069138"
---
# <a name="age-prebuilt-entity-for-a-luis-app"></a>Vordefinierte Age-Entität für eine LUIS-App
Die vorgefertigte Altersentität erfasst den Alterswert sowohl numerisch als auch bezüglich Tagen, Wochen, Monaten und Jahren. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit Altersangaben hinzufügen. Die Altersentität wird in [vielen Kulturen](luis-reference-prebuilt-entities.md) unterstützt. 

## <a name="types-of-age"></a>Alterstypen
Die Entität „age“ wird über das GitHub-Repository [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml#L3) verwaltet.

## <a name="resolution-for-prebuilt-age-entity"></a>Auflösung der vorgefertigten Altersentität

### <a name="api-version-2x"></a>API-Version 2.x

Im folgenden Beispiel wird die Auflösung der Entität **builtin.age** veranschaulicht.

```json
{
  "query": "A 90 day old utilities bill is quite late.",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8236133
  },
  "entities": [
    {
      "entity": "90 day old",
      "type": "builtin.age",
      "startIndex": 2,
      "endIndex": 11,
      "resolution": {
        "unit": "Day",
        "value": "90"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
{
    "query": "A 90 day old utilities bill is quite late.",
    "prediction": {
        "normalizedQuery": "a 90 day old utilities bill is quite late.",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.558252
            }
        },
        "entities": {
            "age": [
                {
                    "number": 90,
                    "unit": "Day"
                }
            ],
            "$instance": {
                "age": [
                    {
                        "type": "builtin.age",
                        "text": "90 day old",
                        "startIndex": 2,
                        "length": 10,
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

Erfahren Sie mehr zu den [Währungs](luis-reference-prebuilt-currency.md)-, [datetimeV2](luis-reference-prebuilt-datetimev2.md)- und [Dimensionsentitäten](luis-reference-prebuilt-dimension.md). 
