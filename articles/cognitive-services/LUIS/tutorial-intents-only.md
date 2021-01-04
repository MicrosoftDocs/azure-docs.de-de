---
title: 'Tutorial: Vorhersagen von Absichten: LUIS'
description: In diesem Tutorial erstellen Sie eine benutzerdefinierte App, die die Absicht eines Benutzers auf der Grundlage seiner Äußerung (Text) vorhersagt.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 398324099f923a5141b172d61f9fd41a69dbf069
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025292"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: Erstellen einer LUIS-App zum Bestimmen von Benutzerabsichten

In diesem Tutorial erstellen Sie eine benutzerdefinierte App, die die Absicht eines Benutzers auf der Grundlage seiner Äußerung (Text) vorhersagt.

**In diesem Tutorial lernen Sie Folgendes:**

> [!div class="checklist"]
> * Erstellen einer neuen App
> * Erstellen von Absichten
> * Hinzufügen von Beispieläußerungen
> * Trainieren der App
> * App veröffentlichen
> * Abrufen der Absichtsvorhersage vom Endpunkt

## <a name="user-intentions-as-intents"></a>Benutzerintentionen als Absichten

Der Zweck der App besteht darin, die Intention von dialogorientiertem, natürlichsprachigem Text zu erkennen:

`I'd like to order a veggie pizza with a salad on the side.`

Diese Intentionen sind in **Absichten** kategorisiert.

|Intent|Zweck|
|--|--|
|`ModifyOrder`|Ermitteln Sie die Pizzabestellung des Benutzers.|
|`Greeting`|Beginnen Sie mit der Bot-Konversation.|
|`ConfirmOrder`|Bestätigen Sie die Pizzabestellung.|
|`None`|Ermitteln Sie, ob der Benutzer etwas fragt, das die LUIS-App nicht beantworten kann. Diese Absicht steht im Rahmen der App-Erstellung zur Verfügung und kann nicht gelöscht werden. |

## <a name="create-a-new-app"></a>Erstellen einer neuen App

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Erstellen einer neuen Absicht

Eine Absicht wird verwendet, um Benutzeräußerungen auf der Grundlage der anhand des Texts in natürlicher Sprache bestimmten Benutzerabsicht zu klassifizieren.

Um eine Äußerung klassifizieren zu können, werden Beispiele von Benutzeräußerungen benötigt, die mit dieser Absicht klassifiziert werden sollen.

1. Wählen Sie im Abschnitt **Erstellen** auf der Seite **Absichten** die Option **+ Erstellen** aus, um eine neue Absicht zu erstellen. Geben Sie den neuen Namen der Absicht ein (`OrderPizza`), und wählen Sie dann **Fertig** aus.

    Die Absicht `OrderPizza` wird vorhergesagt, wenn ein Benutzer eine Pizza bestellen möchte.

1. Fügen Sie dieser Absicht mehrere voraussichtliche Beispieläußerungen von Benutzern hinzu:

    |`OrderPizza`-Beispieläußerungen|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Hinzufügen von Beispieläußerungen im LUIS-Portal auf der Seite der Absicht](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Durch die Bereitstellung von _Beispieläußerungen_ bringen Sie LUIS bei, welche Art von Äußerungen für diese Absicht vorhergesagt werden sollen. Dies sind positive Beispiele. Die Äußerungen in allen anderen Absichten werden für diese Absicht als negative Beispiele behandelt.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Erstellen verbleibender Absichten

1. Erstellen Sie die `Greeting`-Absicht, und fügen Sie die folgenden Beispieläußerungen hinzu. Dies ist die Absicht zur Ermittlung, ob ein Benutzer eine neue Unterhaltung zur Pizzabestellung beginnt.

    |`Greeting`-Beispieläußerungen|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Erstellen Sie die `Confirm`-Absicht, und fügen Sie die folgenden Beispieläußerungen hinzu. Dies ist die Absicht zur Ermittlung, ob ein Benutzer die Bestellung abgeschlossen hat und die Details der Bestellung annimmt.

    |`Confirm`-Beispieläußerungen|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Beispieläußerungen für die Absicht „None“

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Trainieren der App

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Veröffentlichen der App

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Abrufen der Absichtsvorhersage

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Geben Sie in der Adressleiste am Ende der URL Folgendes ein:

    `get a medium vegetarian pizza for delivery`

  
    Der Abfragezeichenfolgenparameter ist `query`, und die Äußerung für die Abfrage (**query**) wird im URI übergeben. Diese Äußerung stimmt nicht mit einer der Beispieläußerungen überein. Dies ist ein guter Test, um zu überprüfen, ob LUIS lernt und die Absicht `OrderPizza` als Absicht mit der höchsten Bewertung vorhersagt.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    Das Entitätenarray ist leer, da diese App aktuell keine Entitäten (Dateneinheiten in der zu extrahierenden Äußerung) aufweist.

    Das JSON-Ergebnis identifiziert die am höchsten bewertete Absicht als **`prediction.topIntent`** -Eigenschaft. Alle Bewertungen liegen zwischen 1 und 0. Die bessere Bewertung liegt näher bei 1.

1. Ändern Sie den **query**-Parameter der URL, um ihn auf die Absicht **Grußformel** auszurichten:

    `Howdy`

    Dies ist nicht genau dasselbe wie eine Beispieläußerung, also ist es ein guter Test, um zu prüfen, ob LUIS lernen kann, was mit dieser Absicht vorhergesagt werden sollte.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Diese Vorhersage weist eine Zuverlässigkeitsbewertung von 44 % auf. Um die Zuverlässigkeitsbewertung zu erhöhen, fügen Sie zwischen 15 und 30 Beispieläußerungen hinzu.

## <a name="client-application-next-steps"></a>Nächste Schritte in der Clientanwendung

In diesem Tutorial wurden die LUIS-App und Absichten erstellt, Beispieläußerungen für jede Absicht hinzugefügt, Beispieläußerungen zur Absicht „None“ (Keine) hinzugefügt, die App trainiert, veröffentlicht und am Endpunkt getestet. Dies sind die grundlegenden Schritte bei der Erstellung eines LUIS-Modells.

Nachdem LUIS die JSON-Antwort zurückgegeben hat, hat LUIS diese Anforderung erledigt. LUIS gibt keine Antworten auf Benutzeräußerungen, es identifiziert lediglich, nach welcher Art Information in natürlicher Sprache gefragt wird. Der Dialoganschluss wird von der Clientanwendung, etwa einem Azure Bot, bereitgestellt.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* [Typen von Entitäten](luis-concept-entity-types.md)
* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)
* [Azure Bot](/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer zerlegbaren Entität zu dieser App](tutorial-machine-learned-entity.md)