---
title: 'Abfragen von Vorhersagen mithilfe eines Browsers: LUIS'
description: In diesem Artikel bestimmen Sie im Browser mithilfe einer verfügbaren öffentlichen LUIS-App aus Konversationstext die Absicht eines Benutzers.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: 6aa681c8ea60cb57ac3e6d143c60cc46bc057719
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436911"
---
# <a name="how-to-query-the-prediction-runtime-with-user-text"></a>Abfragen der Vorhersagelaufzeit mit Benutzertext

Um zu verstehen, was ein LUIS-Vorhersageendpunkt zurückgibt, zeigen Sie ein Vorhersageergebnis in einem Webbrowser an.

## <a name="prerequisites"></a>Voraussetzungen

Zum Abfragen einer öffentlichen App benötigen Sie Folgendes:

* Informationen zu Ihrer LUIS-Ressource (Language Understanding):
    * **Vorhersageschlüssel:** Kann über das [LUIS-Portal](https://www.luis.ai/) abgerufen werden. Wenn Sie noch kein Abonnement besitzen, um einen Schlüssel zu erstellen, können Sie sich für ein [kostenloses Konto](https://azure.microsoft.com/free/cognitive-services) registrieren.
    * **Unterdomäne des Vorhersageendpunkts:** Die Unterdomäne ist gleichzeitig der **Name** Ihrer LUIS-Ressource.
* Eine LUIS-App-ID: Verwenden Sie die öffentliche IoT-App-ID `df67dcdb-c37d-46af-88e1-8b97951ca1c2`. Die im Schnellstartcode verwendete Benutzerabfrage ist spezifisch für diese App.

## <a name="use-the-browser-to-see-predictions"></a>Verwenden des Browsers zum Anzeigen von Vorhersagen

1. Öffnen Sie einen Webbrowser.
1. Verwenden Sie die unten aufgeführten vollständigen URLs, und ersetzen Sie `YOUR-KEY` durch Ihren eigenen LUIS-Vorhersageschlüssel. Die Anforderungen sind GET-Anforderungen und enthalten die Autorisierung mit dem LUIS-Vorhersageschlüssel als Abfragezeichenfolgenparameter.

    #### <a name="v3-prediction-request"></a>[V3-Vorhersageanforderung](#tab/V3-1-1)


    Das Format der V3-URL für eine **GET**-Endpunktanforderung (nach Slots) ist wie folgt:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY
    `

    #### <a name="v2-prediction-request"></a>[V2-Vorhersageanforderung](#tab/V2-1-2)

    Das Format der V2-URL für eine **GET**-Endpunktanforderung ist wie folgt:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=YOUR-LUIS-PREDICTION-KEY&q=turn on all lights
    `

1. Fügen Sie die URL in ein Browserfenster ein, und drücken Sie die EINGABETASTE. Das im Browser angezeigte JSON-Ergebnis gibt an, dass LUIS die Absicht `HomeAutomation.TurnOn` als die am höchsten bewertete Absicht und die Entität `HomeAutomation.Operation` mit dem Wert `on` erkennt.

    #### <a name="v3-prediction-response"></a>[V3-Vorhersageantwort](#tab/V3-2-1)

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-response"></a>[V2-Vorhersageantwort](#tab/V2-2-2)

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

    * * *

1. Fügen Sie den entsprechenden Abfragezeichenfolgen-Parameter hinzu, um alle Absichten anzuzeigen.

    #### <a name="v3-prediction-endpoint"></a>[V3-Vorhersageendpunkt](#tab/V3-3-1)

    Fügen Sie `show-all-intents=true` am Ende der Abfragezeichenfolge hinzu, um **alle Absichten anzuzeigen**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/predict/v3.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?query=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&show-all-intents=true
    `

    ```JSON
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ]
            }
        }
    }
    ```

    #### <a name="v2-prediction-endpoint"></a>[V2-Vorhersageendpunkt](#tab/V2)

    Fügen Sie `verbose=true` am Ende der Abfragezeichenfolge hinzu, um **alle Absichten anzuzeigen**:

    `
    https://YOUR-LUIS-ENDPOINT-SUBDOMAIN.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?q=turn on all lights&subscription-key=YOUR-LUIS-PREDICTION-KEY&verbose=true
    `

    ```json
    {
        "query": "turn on all lights",
        "topScoringIntent": {
            "intent": "HomeAutomation.TurnOn",
            "score": 0.5375382
        },
        "intents": [
            {
                "intent": "HomeAutomation.TurnOn",
                "score": 0.5375382
            },
            {
                "intent": "None",
                "score": 0.08687421
            },
            {
                "intent": "HomeAutomation.TurnOff",
                "score": 0.0207554
            }
        ],
        "entities": [
            {
                "entity": "on",
                "type": "HomeAutomation.Operation",
                "startIndex": 5,
                "endIndex": 6,
                "score": 0.724984169
            }
        ]
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:
* [V3-Vorhersageendpunkt](luis-migration-api-v3.md)
* [Benutzerdefinierte Unterdomänen](../cognitive-services-custom-subdomains.md)

> [!div class="nextstepaction"]
> [Erstellen einer App im LUIS-Portal](get-started-portal-build-app.md)
