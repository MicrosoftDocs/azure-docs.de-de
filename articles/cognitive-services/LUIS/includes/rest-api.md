---
title: REST-API-Includedatei
description: include file
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 11/30/2020
ms.topic: include
ms.openlocfilehash: ac5d4ce3080bad36b0f68289bee32910bdc58837
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941520"
---
Wenn Sie dieser Schnellstartanleitung folgen, führen Sie drei REST-Aufrufe nacheinander aus.

- Zuerst laden Sie mit dem Aufruf [REST Batch add labels](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09) einen Batch von Beispieläußerungen hoch, die zum Trainieren des Modells der Pizza-App verwendet werden.
- Anschließend beginnen Sie mit einer Trainingssitzung für die Pizza-App, für die Sie den Aufruf [REST Train application version](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) verwenden.
- Schließlich rufen Sie mit dem Aufruf [REST Get version training status](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c46) den Status der Trainingssitzung für die Pizza-App ab.

[Referenzdokumentation](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45)

## <a name="prerequisites"></a>Voraussetzungen

* Ein kostenloses [LUIS](https://www.luis.ai)-Konto.
* Ein Text-Editor, z. B. [Visual Studio Code](https://code.visualstudio.com/).
* Das Befehlszeilenprogramm cURL. Das cURL-Programm ist unter macOS, in den meisten Linux-Distributionen und in Windows 10, Build 1803 und höher, bereits installiert.

    Wenn Sie cURL installieren müssen, können Sie es von der [cURL-Downloadseite](https://curl.haxx.se/download.html) herunterladen.

## <a name="create-pizza-app"></a>Erstellen Sie die Pizza-App

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]


## <a name="create-a-json-file-to-train-the-pizza-app"></a>Erstellen einer JSON-Datei zum Trainieren der Pizza-App

Um eine JSON-Datei mit drei Beispieläußerungen zu erstellen, speichern Sie die folgenden JSON-Daten in einer Datei mit dem Namen `ExampleUtterances.JSON`:

```JSON
[
  {
    "text": "order a pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "order a large pepperoni pizza",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 6,
        "endCharIndex": 28
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 14,
        "endCharIndex": 28
      },
      {
        "entityName": "Size",
        "startCharIndex": 8,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "I want two large pepperoni pizzas on thin crust",
    "intentName": "ModifyOrder",
    "entityLabels": [
      {
        "entityName": "Order",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "FullPizzaWithModifiers",
        "startCharIndex": 7,
        "endCharIndex": 46
      },
      {
        "entityName": "PizzaType",
        "startCharIndex": 17,
        "endCharIndex": 32
      },
      {
        "entityName": "Size",
        "startCharIndex": 11,
        "endCharIndex": 15
      },
      {
        "entityName": "Quantity",
        "startCharIndex": 7,
        "endCharIndex": 9
      },
      {
        "entityName": "Crust",
        "startCharIndex": 37,
        "endCharIndex": 46
      }
    ]
  }
]`
```

Der JSON-Code mit den Beispieläußerungen folgt einem bestimmten Format.

Das Feld `text` enthält den Text der Beispieläußerung. Das Feld `intentName` muss dem Namen einer vorhandenen Absicht in der LUIS-App entsprechen. `entityLabels` ist ein Pflichtfeld. Wenn Sie keine Bezeichnungen für Entitäten angeben möchten, geben Sie ein leeres Array an.

Ist das entityLabels-Array nicht leer, müssen `startCharIndex` und `endCharIndex` die Entität markieren, auf die im Feld `entityName` verwiesen wird. Der Index ist nullbasiert. Wenn der Text der Bezeichnung mit einem Leerzeichen beginnt oder endet, schlagen die API-Aufrufe zum Hinzufügen der Äußerungen fehl.

## <a name="add-example-utterances"></a>Hinzufügen von Beispieläußerungen

1. Kopieren Sie diesen Befehl in Ihren Text-Editor, um den Batch mit Beispieläußerungen hochzuladen:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "***YOUR-AUTHORING-ENDPOINT**_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" ^
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/examples?verbose=true&show-all-intents=true" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_" \
          --data "@ExampleUtterances.JSON"
    ```

    _*_

1. Ersetzen Sie die Werte, die mit `_*_YOUR-` beginnen, durch Ihre eigenen Werte.

    |Information|Zweck|
    |--|--|
    |`_*_YOUR-AUTHORING-ENDPOINT_*_`| Ihr URL-Endpunkt für die Erstellung. Beispiel: „https://REPLACE-WITH-YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/“. Sie haben Ihren Ressourcennamen festgelegt, als Sie die Ressource erstellt haben.|
    |`_*_YOUR-APP-ID_*_`| Ihre LUIS-App-ID. |
    |`_*_YOUR-APP-VERSION_*_`| Ihre LUIS-App-Version. Die Versionsnummer der Pizza-App lautet „0.1“ (ohne Anführungszeichen).|
    |`_*_YOUR-AUTHORING-KEY_*_`|Ihr Erstellungsschlüssel mit 32 Zeichen.|

    Zugewiesene Schlüssel und Ressourcen werden im LUIS-Portal auf der Seite _ *Azure-Ressourcen** im Abschnitt „Verwalten“ angezeigt. Die App-ID wird auf der Seite **Anwendungseinstellungen** ebenfalls im Abschnitt „Verwalten“ angezeigt.

1. Starten Sie eine Eingabeaufforderung (Windows) oder ein Terminal (macOS und Linux), und wechseln Sie in das Verzeichnis, in dem Sie die `ExampleUtterances.JSON`-Datei gespeichert haben.

1. Kopieren Sie den cURL-Befehl aus dem Editor, und fügen Sie ihn an der Eingabeaufforderung (Windows) oder im Terminal (macOS und Linux) ein. Drücken Sie die EINGABETASTE, um den Befehl auszuführen.

    Daraufhin sollte die folgende Antwort angezeigt werden:

    ```json
    [{"value":{"ExampleId":1255129706,"UtteranceText":"order a pizza"},"hasError":false},{"value":{"ExampleId":1255129707,"UtteranceText":"order a large pepperoni pizza"},"hasError":false},{"value":{"ExampleId":1255129708,"UtteranceText":"i want two large pepperoni pizzas on thin crust"},"hasError":false}]
    ```

    Hier sehen Sie die zur besseren Lesbarkeit formatierte Ausgabe:

    ```json
    [
      {
        "value": {
          "ExampleId": 1255129706,
          "UtteranceText": "order a pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129707,
          "UtteranceText": "order a large pepperoni pizza"
        },
        "hasError": false
      },
      {
        "value": {
          "ExampleId": 1255129708,
          "UtteranceText": "i want two large pepperoni pizzas on thin crust"
        },
        "hasError": false
      }
    ]
    ```

## <a name="train-the-pizza-app-model"></a>Trainieren des Pizza-App-Modells

1. Um eine Trainingssitzung für die Pizza-App zu beginnen, kopieren Sie diesen Befehl in Ihren Text-Editor:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "**_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
          --data "" ^
          --request POST ^
          --header "Content-Type:application/json" ^
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
          --data "" \
          --request POST \
          --header "Content-Type:application/json" \
          --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. Ersetzen Sie wie schon zuvor die Werte, die mit `_*_YOUR-` beginnen, durch Ihre eigenen Werte.

1. Kopieren Sie den cURL-Befehl aus dem Editor, und fügen Sie ihn an der Eingabeaufforderung (Windows) oder im Terminal (macOS und Linux) ein. Drücken Sie die EINGABETASTE, um den Befehl auszuführen.

    Daraufhin sollte die folgende Antwort angezeigt werden:

    ```json
    {"statusId":2,"status":"UpToDate"}
    ```

    Hier sehen Sie die zur besseren Lesbarkeit formatierte Ausgabe:

    ```json
    {
      "statusId": 2,
      "status": "UpToDate"
    }
    ```

## <a name="get-the-status-of-training"></a>Abrufen des Trainingsstatus

1. Um den Trainingsstatus der Trainingssitzung abzurufen, kopieren Sie diesen Befehl in Ihren Text-Editor:

    #### <a name="windows"></a>[Windows](#tab/windows)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" ^
            --request GET ^
            --header "Content-Type:application/json" ^
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "_*_YOUR-AUTHORING-ENDPOINT_*_/luis/authoring/v3.0-preview/apps/_*_YOUR-APP-ID_*_/versions/_*_YOUR-APP-VERSION_*_/train?verbose=true&show-all-intents=true" \
            --request GET \
            --header "Content-Type:application/json" \
            --header "Ocp-Apim-Subscription-Key: _*_YOUR-AUTHORING-KEY_*_"
    ```

    _*_

1. Ersetzen Sie wie schon zuvor die Werte, die mit `_*_YOUR-` beginnen, durch Ihre eigenen Werte.

1. Kopieren Sie den cURL-Befehl aus dem Editor, und fügen Sie ihn an der Eingabeaufforderung (Windows) oder im Terminal (macOS und Linux) ein. Drücken Sie die EINGABETASTE, um den Befehl auszuführen.

    Daraufhin sollte die folgende Antwort angezeigt werden:

    ```json
    [{"modelId":"8eb7ad8f-5db5-4c28-819b-ca3905fffd80","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6f53bc92-ae54-44ce-bc4e-010d1f8cfda0","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6cb17888-ad6e-464c-82c0-d37fd1f2c4f8","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"a16fc4fd-1949-4e77-9de3-30369f16c3a5","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"6bacdb75-1889-4f93-8971-8c8995ff8960","details":{"statusId":2,"status":"UpToDate","exampleCount":171}},{"modelId":"be963f4c-4898-48d7-9eba-3c6af0867b9d","details":{"statusId":2,"status":"UpToDate","exampleCount":171}}]
    ```

    Hier sehen Sie die zur besseren Lesbarkeit formatierte Ausgabe:

    ```json
    [
      {
        "modelId": "8eb7ad8f-5db5-4c28-819b-ca3905fffd80",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6f53bc92-ae54-44ce-bc4e-010d1f8cfda0",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6cb17888-ad6e-464c-82c0-d37fd1f2c4f8",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "a16fc4fd-1949-4e77-9de3-30369f16c3a5",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "6bacdb75-1889-4f93-8971-8c8995ff8960",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      },
      {
        "modelId": "be963f4c-4898-48d7-9eba-3c6af0867b9d",
        "details": {
          "statusId": 2,
          "status": "UpToDate",
          "exampleCount": 171
        }
      }
    ]
    ```


## <a name="get-intent-from-the-prediction-endpoint"></a>Abrufen der Absicht vom Vorhersageendpunkt

Verwenden Sie cURL, um den [Vorhersageendpunkt](https://aka.ms/luis-apim-v3-prediction) abzurufen und ein Vorhersageergebnis zu erhalten.

> [!NOTE]
> Dieser Befehl verwendet Ihren Vorhersageendpunkt.

1. Kopieren Sie diesen Befehl in Ihren Text-Editor:

    #### <a name="windows"></a>[Windows](#tab/windows)
    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" ^
          --request GET ^
          --get ^
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" ^
          --data "verbose=true" ^
          --data "show-all-intents=true" ^
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="macos"></a>[macOS](#tab/unix)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    #### <a name="linux"></a>[Linux](#tab/linux)

    ```cURL
    curl "https://_*_YOUR-PREDICTION-ENDPOINT_*_/luis/prediction/v3.0/apps/_*_YOUR-APP-ID_*_/slots/production/predict" \
          --request GET \
          --get \
          --data "subscription-key=_*_YOUR-PREDICTION-KEY_*_" \
          --data "verbose=true" \
          --data "show-all-intents=true" \
          --data-urlencode "query=I want two large pepperoni pizzas on thin crust please"
    ```

    _*_

1. Ersetzen Sie die Werte, die mit `_*_YOUR-` beginnen, durch Ihre eigenen Werte.

    |Information|Zweck|
    |--|--|
    |`_*_YOUR-PREDICTION-ENDPOINT_*_`| Ihr URL-Endpunkt für die Vorhersage. Die Seite mit den Azure-Ressourcen für Ihre App im LUIS-Portal.<br>Beispiel: `https://westus.api.cognitive.microsoft.com/`.|
    |`_*_YOUR-APP-ID_*_`|Ihre App-ID. Die Seite mit den Anwendungseinstellungen für Ihre App im LUIS-Portal.
    |`_*_YOUR-PREDICTION-KEY_**`|Ihr 32-stelliger Vorhersageschlüssel. Die Seite mit den Azure-Ressourcen für Ihre App im LUIS-Portal.

1. Kopieren Sie den Text in ein Konsolenfenster, und drücken Sie die EINGABETASTE, um den Befehl auszuführen:

1. Überprüfen Sie die Vorhersageantwort, die im JSON-Format zurückgegeben wird:

    ```console
    {"query":"I want two large pepperoni pizzas on thin crust please","prediction":{"topIntent":"ModifyOrder","intents":{"ModifyOrder":{"score":1.0},"None":{"score":8.55E-09},"Greetings":{"score":1.82222226E-09},"CancelOrder":{"score":1.47272727E-09},"Confirmation":{"score":9.8125E-10}},"entities":{"Order":[{"FullPizzaWithModifiers":[{"PizzaType":["pepperoni pizzas"],"Size":[["Large"]],"Quantity":[2],"Crust":[["Thin"]],"$instance":{"PizzaType":[{"type":"PizzaType","text":"pepperoni pizzas","startIndex":17,"length":16,"score":0.9978157,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Size":[{"type":"SizeList","text":"large","startIndex":11,"length":5,"score":0.9984481,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Quantity":[{"type":"builtin.number","text":"two","startIndex":7,"length":3,"score":0.999770939,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"Crust":[{"type":"CrustList","text":"thin crust","startIndex":37,"length":10,"score":0.933985531,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"$instance":{"FullPizzaWithModifiers":[{"type":"FullPizzaWithModifiers","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.90681237,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}]}}],"ToppingList":[["Pepperoni"]],"$instance":{"Order":[{"type":"Order","text":"two large pepperoni pizzas on thin crust","startIndex":7,"length":40,"score":0.9047088,"modelTypeId":1,"modelType":"Entity Extractor","recognitionSources":["model"]}],"ToppingList":[{"type":"ToppingList","text":"pepperoni","startIndex":17,"length":9,"modelTypeId":5,"modelType":"List Entity Extractor","recognitionSources":["model"]}]}}}}
    ```

    Die zur besseren Lesbarkeit formatierte JSON-Antwort:

    ```JSON
    {
      "query": "I want two large pepperoni pizzas on thin crust please",
      "prediction": {
        "topIntent": "ModifyOrder",
        "intents": {
          "ModifyOrder": {
            "score": 1.0
          },
          "None": {
            "score": 8.55e-9
          },
          "Greetings": {
            "score": 1.82222226e-9
          },
          "CancelOrder": {
            "score": 1.47272727e-9
          },
          "Confirmation": {
            "score": 9.8125e-10
          }
        },
        "entities": {
          "Order": [
            {
              "FullPizzaWithModifiers": [
                {
                  "PizzaType": [
                    "pepperoni pizzas"
                  ],
                  "Size": [
                    [
                      "Large"
                    ]
                  ],
                  "Quantity": [
                    2
                  ],
                  "Crust": [
                    [
                      "Thin"
                    ]
                  ],
                  "$instance": {
                    "PizzaType": [
                      {
                        "type": "PizzaType",
                        "text": "pepperoni pizzas",
                        "startIndex": 17,
                        "length": 16,
                        "score": 0.9978157,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Size": [
                      {
                        "type": "SizeList",
                        "text": "large",
                        "startIndex": 11,
                        "length": 5,
                        "score": 0.9984481,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Quantity": [
                      {
                        "type": "builtin.number",
                        "text": "two",
                        "startIndex": 7,
                        "length": 3,
                        "score": 0.999770939,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ],
                    "Crust": [
                      {
                        "type": "CrustList",
                        "text": "thin crust",
                        "startIndex": 37,
                        "length": 10,
                        "score": 0.933985531,
                        "modelTypeId": 1,
                        "modelType": "Entity Extractor",
                        "recognitionSources": [
                          "model"
                        ]
                      }
                    ]
                  }
                }
              ],
              "$instance": {
                "FullPizzaWithModifiers": [
                  {
                    "type": "FullPizzaWithModifiers",
                    "text": "two large pepperoni pizzas on thin crust",
                    "startIndex": 7,
                    "length": 40,
                    "score": 0.90681237,
                    "modelTypeId": 1,
                    "modelType": "Entity Extractor",
                    "recognitionSources": [
                      "model"
                    ]
                  }
                ]
              }
            }
          ],
          "ToppingList": [
            [
              "Pepperoni"
            ]
          ],
          "$instance": {
            "Order": [
              {
                "type": "Order",
                "text": "two large pepperoni pizzas on thin crust",
                "startIndex": 7,
                "length": 40,
                "score": 0.9047088,
                "modelTypeId": 1,
                "modelType": "Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ],
            "ToppingList": [
              {
                "type": "ToppingList",
                "text": "pepperoni",
                "startIndex": 17,
                "length": 9,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                  "model"
                ]
              }
            ]
          }
        }
      }
    }
    ```