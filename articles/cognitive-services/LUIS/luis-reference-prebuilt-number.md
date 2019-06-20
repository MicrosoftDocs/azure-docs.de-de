---
title: Vordefinierte Number-Entität
titleSuffix: Azure
description: In diesem Artikel erhalten Sie Informationen zur vorgefertigten Nummernentität in Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: d4f707d4bf9bac5e2208eadb94983af368b9f521
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072259"
---
# <a name="number-prebuilt-entity-for-a-luis-app"></a>Vordefinierte Number-Entität für eine LUIS-App
Es gibt verschiedene Möglichkeiten, numerische Werte zu verwenden, um Informationen zu quantifizieren, auszudrücken und zu beschreiben. In diesem Artikel werden nur einige Beispiele aufgeführt. LUIS interpretiert die Variationen verschiedener Benutzeräußerungen und gibt einheitliche numerische Werte zurück. Da diese Entität bereits trainiert wurde, müssen Sie den Anwendungsabsichten keine Beispieläußerungen mit Nummern hinzufügen. 

## <a name="types-of-number"></a>Nummerntypen
Die Entität „number“ wird über das GitHub-Repository [Recognizers-text](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml) verwaltet.

## <a name="examples-of-number-resolution"></a>Beispiele für Nummernauflösungen

| Äußerung        | Entität   | Lösung |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |


LUIS fügt den anerkannten Wert einer **`builtin.number`** -Entität in das `resolution`-Feld der JSON-Antwort ein, die zurückgegeben wird.

## <a name="resolution-for-prebuilt-number"></a>Auflösung der vorgefertigten Nummer


### <a name="api-version-2x"></a>API-Version 2.x

Im folgenden Beispiel wird eine JSON-Antwort von LUIS gezeigt, die die Auflösung des Werts „24“ für die Äußerung „two dozen“ enthält.

```json
{
  "query": "order two dozen eggs",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.105443209
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.105443209
    },
    {
      "intent": "OrderFood",
      "score": 0.9468431361
    },
    {
      "intent": "Help",
      "score": 0.000399122015
    },
  ],
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "subtype": "integer",
        "value": "24"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Vorschau-API-Version 3.x

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `false` festgelegt:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ]
        }
    }
}
```

Beim folgenden JSON-Code wurde der `verbose`-Parameter auf `true` festgelegt:

```json
{
    "query": "order two dozen eggs",
    "prediction": {
        "normalizedQuery": "order two dozen eggs",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "number": [
                24
            ],
            "$instance": {
                "number": [
                    {
                        "type": "builtin.number",
                        "text": "two dozen",
                        "startIndex": 6,
                        "length": 9,
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

Erfahren Sie mehr zu den [Währungs](luis-reference-prebuilt-currency.md)-, [Ordinal](luis-reference-prebuilt-ordinal.md)- und [Prozentsatzentitäten](luis-reference-prebuilt-percentage.md). 
