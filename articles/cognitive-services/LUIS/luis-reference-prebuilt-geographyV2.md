---
title: Vordefinierte GeographyV2-Entität
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erhalten Sie Informationen zur vorgefertigten geographyV2-Entität in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5b9ace8a2402d96c4bb54864eb59a56b46d04067
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65071965"
---
# <a name="geographyv2-prebuilt-entity-for-a-luis-app"></a>Vordefinierte GeographyV2-Entität für eine LUIS-App
Die vordefinierte geographyV2-Entität erkennt Orte. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit geographyV2 hinzufügen. Die geographyV2-Entität wird in der [Kultur](luis-reference-prebuilt-entities.md) Englisch unterstützt.

## <a name="subtypes"></a>Untertypen
Die geografischen Standorte weisen Untertypen auf:

|Subtype|Zweck|
|--|--|
|`poi`|Point of Interest|
|`city`|Name der Stadt|
|`countryRegion`|Name des Lands oder der Region|
|`continent`|Name des Kontinents|
|`state`|Name des Bundeslands oder Kantons|


## <a name="resolution-for-geographyv2-entity"></a>Auflösung für die geographyV2-Entität

### <a name="api-version-2x"></a>API-Version 2.x

Im folgenden Beispiel wird die Auflösung der Entität **builtin.geographyV2** veranschaulicht.

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "topScoringIntent": {
        "intent": "None",
        "score": 0.8008023
    },
    "intents": [
        {
            "intent": "None",
            "score": 0.8008023
        }
    ],
    "entities": [
        {
            "entity": "the sphinx",
            "type": "builtin.geographyV2.poi",
            "startIndex": 18,
            "endIndex": 27
        },
        {
            "entity": "gizah",
            "type": "builtin.geographyV2.city",
            "startIndex": 32,
            "endIndex": 36
        },
        {
            "entity": "egypt",
            "type": "builtin.geographyV2.countryRegion",
            "startIndex": 38,
            "endIndex": 42
        },
        {
            "entity": "africa",
            "type": "builtin.geographyV2.continent",
            "startIndex": 47,
            "endIndex": 52
        },
        {
            "entity": "texas",
            "type": "builtin.geographyV2.state",
            "startIndex": 72,
            "endIndex": 76
        },
        {
            "entity": "carol",
            "type": "builtin.personName",
            "startIndex": 0,
            "endIndex": 4
        }
    ]
} 
```

### <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
{
    "query": "Carol is visiting the sphinx in gizah egypt in africa before heading to texas",
    "prediction": {
        "normalizedQuery": "carol is visiting the sphinx in gizah egypt in africa before heading to texas",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.5115521
            }
        },
        "entities": {
            "geographyV2": [
                "the sphinx",
                "gizah",
                "egypt",
                "africa",
                "texas"
            ],
            "$instance": {
                "geographyV2": [
                    {
                        "type": "builtin.geographyV2",
                        "text": "the sphinx",
                        "startIndex": 18,
                        "length": 10,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "gizah",
                        "startIndex": 32,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "egypt",
                        "startIndex": 38,
                        "length": 5,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "africa",
                        "startIndex": 47,
                        "length": 6,
                        "modelTypeId": 2,
                        "modelType": "Prebuilt Entity Extractor"
                    },
                    {
                        "type": "builtin.geographyV2",
                        "text": "texas",
                        "startIndex": 72,
                        "length": 5,
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

Erfahren Sie mehr über die Entitäten [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) und [ordinal](luis-reference-prebuilt-ordinal.md). 
