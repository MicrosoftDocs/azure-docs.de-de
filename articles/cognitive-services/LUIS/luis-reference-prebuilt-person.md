---
title: Vordefinierte PersonName-Entität
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erhalten Sie Informationen zur vordefinierten personName-Entität in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3b12c69b7c6710e774d50e631d2423fd72ce828a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072175"
---
# <a name="personname-prebuilt-entity-for-a-luis-app"></a>Vordefinierte PersonName-Entität für eine LUIS-App
Die vordefinierte personName-Entität erkennt die Namen von Personen. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit personName hinzufügen. Die personName-Entität wird in den [Kulturen](luis-reference-prebuilt-entities.md) Englisch und Chinesisch unterstützt.

## <a name="resolution-for-personname-entity"></a>Auflösung für die personName-Entität

### <a name="api-version-2x"></a>API-Version 2.x

Im folgenden Beispiel wird die Auflösung der Entität **builtin.personName** veranschaulicht.

```json
{
  "query": "Is Jill Jones in Cairo?",
  "topScoringIntent": {
    "intent": "WhereIsEmployee",
    "score": 0.762141049
  },
  "entities": [
    {
      "entity": "Jill Jones",
      "type": "builtin.personName",
      "startIndex": 3,
      "endIndex": 12
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
{
    "query": "Is Jill Jones in Cairo?",
    "prediction": {
        "normalizedQuery": "is jill jones in cairo?",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.6544678
            }
        },
        "entities": {
            "personName": [
                "Jill Jones"
            ],
            "$instance": {
                "personName": [
                    {
                        "type": "builtin.personName",
                        "text": "Jill Jones",
                        "startIndex": 3,
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

Erfahren Sie mehr über die Entitäten [email](luis-reference-prebuilt-email.md), [number](luis-reference-prebuilt-number.md) und [ordinal](luis-reference-prebuilt-ordinal.md). 
