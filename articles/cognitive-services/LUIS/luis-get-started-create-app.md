---
title: 'Schnellstart: Erstellen einer App – LUIS'
description: In dieser Schnellstartanleitung lernen Sie, eine LUIS-App zu erstellen, die die vordefinierte Domäne `HomeAutomation` zum Ein- und Ausschalten von Beleuchtungen und Haushaltsgeräten verwendet. Diese vordefinierte Domäne bietet Absichten, Entitäten und Beispieläußerungen, die Sie nutzen können. Am Ende dieser Schnellstartanleitung verfügen Sie über einen LUIS-Endpunkt in der Cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/13/2020
ms.openlocfilehash: 2048079a9b1eb5234d03c6fe57073228bd892d53
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019159"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Schnellstart: Verwenden der vordefinierten Home Automation-App

In dieser Schnellstartanleitung erstellen Sie eine LUIS-App, die die vordefinierte Domäne `HomeAutomation` zum Ein- und Ausschalten von Beleuchtungen und Haushaltsgeräten verwendet. Diese vordefinierte Domäne bietet Absichten, Entitäten und Beispieläußerungen, die Sie nutzen können. Am Ende dieser Schnellstartanleitung verfügen Sie über einen LUIS-Endpunkt in der Cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Erstellen einer neuen App
Sie können Ihre Anwendungen auf der Seite **Meine Apps** erstellen und verwalten.

### <a name="create-an-application"></a>Erstellen einer Anwendung

Klicken Sie zum Erstellen einer Anwendung auf **+ Neue App**. 

Geben Sie im angezeigten Fenster die folgenden Informationen ein:

|Name  |Beschreibung  |
|---------|---------|
|AName     | Ein Name für Ihre App. Beispiel: „Home Automation“.        |
|culture     | Die Sprache, die Ihre App versteht und spricht.   |
|Beschreibung | Eine Beschreibung für Ihre App.
|Vorhersageressource | Die Vorhersageressource, die Abfragen empfängt. |

Wählen Sie **Fertig** aus.

>[!NOTE]
>Die Kultur kann nach dem Erstellen der Anwendung nicht mehr geändert werden.

## <a name="add-prebuilt-domain"></a>Hinzufügen einer vordefinierten Domäne

1. Wählen Sie im linken Navigationsbereich die Option **Prebuilt domains** (Vordefinierte Domänen) aus.
1. Suchen Sie nach **HomeAutomation**.
1. Wählen Sie auf der Karte „HomeAutomation“ die Option **Domäne hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Wählen Sie die Option „Vordefinierte Domänen“ aus, und suchen Sie nach „HomeAutomation“. Wählen Sie auf der Karte „HomeAutomation“ die Option „Domäne hinzufügen“ aus.](media/luis-quickstart-new-app/home-automation.png)

    Wenn die Domäne erfolgreich hinzugefügt wurde, enthält das Feld für die vordefinierte Domäne eine Schaltfläche **Remove domain** (Domäne entfernen).

## <a name="intents-and-entities"></a>Absichten und Entitäten

1. Wählen Sie im linken Navigationsmenü die Option **Absichten** aus, um die Absichten der Domäne „HomeAutomation“ anzuzeigen. Hierin sind Beispieläußerungen enthalten, z. B. `HomeAutomation.QueryState` und `HomeAutomation.SetDevice`.

    > [!NOTE]
    > **None** (Keine) ist eine Absicht, die von allen LUIS-Apps bereitgestellt wird. Diese Absicht verwenden Sie zum Behandeln von Äußerungen, die keiner Funktionalität Ihrer App entsprechen.

1. Wählen Sie die Absicht **HomeAutomation.TurnOff** aus. Die Absicht enthält eine Liste von Beispieläußerungen, die mit Entitäten beschriftet sind.

    > [!div class="mx-imgBorder"]
    > [![Screenshot: Absicht „HomeAutomation.TurnOff“](media/luis-quickstart-new-app/home-automation-turnoff.png "Screenshot: Absicht „HomeAutomation.TurnOff“")](media/luis-quickstart-new-app/home-automation-turnoff.png)

1. Wählen Sie **Entitäten** aus, wenn Sie Entitäten für die App anzeigen möchten. Wenn Sie auf eine der Entitäten klicken, z. B. **HomeAutomation.DeviceName**, wird eine Liste mit den zugeordneten Werten angezeigt. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Alternativer Bildtext" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Trainieren der LUIS-App

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testen Ihrer App
Nachdem Sie Ihre App trainiert haben, können Sie sie testen.

1. Wählen Sie im Navigationsbereich rechts oben die Option **Testen** aus.

1. Geben Sie eine Testäußerung wie `Turn off the lights` in den interaktiven Testbereich ein, und drücken Sie die EINGABETASTE. Beispiel: *Turn off the lights*.

    In diesem Beispiel wurde `Turn off the lights` korrekt als Absicht mit der höchsten Bewertung von **HomeAutomation.TurnOff** identifiziert.

    ![Screenshot des Testbereichs mit hervorgehobener Äußerung](media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png)

1. Wählen Sie **Überprüfen** aus, um weitere Informationen zur Vorhersage anzuzeigen.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Testbereich mit Überprüfungsinformationen](media/luis-quickstart-new-app/test.png)

1. Schließen Sie den Testbereich.

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Veröffentlichen der App zum Abrufen der Endpunkt-URL

Damit Sie eine LUIS-Vorhersage in einem Chatbot oder einer anderen Clientanwendung empfangen können, muss die App im Vorhersage-Endpunkt veröffentlicht werden.

1. Wählen Sie oben rechts im Fenster die Option **Veröffentlichen** aus.

1. Wählen Sie den Slot **Produktion** und anschließend **Fertig** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: LUIS-Veröffentlichung am Endpunkt](media/howto-publish/publish-app-popup.png)

1. Wählen sie in der Benachrichtigung den Link **Access your endpoint URLs** (Auf Endpunkt-URLs zugreifen) aus, um die Seite **Azure-Ressourcen** aufzurufen. Die Endpunkt-URLs sind als **Beispielabfrage** aufgeführt.

<!-- [!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)] -->

<a name="query-the-v2-api-prediction-endpoint"></a>

## <a name="query-the-v3-api-prediction-endpoint"></a>Abfragen des V3-API-Vorhersageendpunkts

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Stellen Sie in der Adressleiste des Browsers für die Abfragezeichenfolge sicher, dass die URL die folgenden Name-Wert-Leisten aufweist. Fügen Sie diese Angaben hinzu, falls sie in der Abfragezeichenfolge nicht enthalten sind:

    |Name-Wert-Paar|
    |--|
    |`verbose=true`|
    |`show-all-intents=true`|

3. Navigieren Sie in der Adressleiste des Browsers an das Ende der URL, und geben Sie `turn off the living room light` für den Wert _query_ ein. Drücken Sie anschließend die EINGABETASTE.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
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

    Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie können den Endpunkt im Code aufrufen:

> [!div class="nextstepaction"]
> [Aufrufen eines LUIS-Endpunkts mithilfe von Code](./luis-get-started-get-intent-from-rest.md)