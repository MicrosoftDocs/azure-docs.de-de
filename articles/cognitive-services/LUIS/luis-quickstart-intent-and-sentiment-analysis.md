---
title: Stimmungsanalyse
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial erstellen Sie eine App, die veranschaulicht, wie Sie positive, negative und neutrale Emotionen aus Äußerungen abrufen. Die Stimmung wird anhand der gesamten Äußerung bestimmt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 1408e29793fdac77b89e3f0cc0a7be525f7fa1d2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479771"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Tutorial:  Abrufen der Stimmung einer Äußerung

In diesem Tutorial erstellen Sie eine App, die veranschaulicht, wie Sie positive, negative und neutrale Emotionen in Äußerungen bestimmen. Die Stimmung wird anhand der gesamten Äußerung bestimmt.

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Erstellen einer neuen App
> * Hinzufügen der Standpunktanalyse als Veröffentlichungseinstellung
> * Trainieren der App
> * App veröffentlichen
> * Abrufen der Stimmung einer Äußerung von einem Endpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>Standpunktanalyse ist eine Veröffentlichungseinstellung

Die folgenden Äußerungen sind Beispiele für die Stimmung:

|Stimmung|Punkte|Äußerung|
|:--|:--|:--|
|Positiv|0,91 |John W. Smith did a great job on the presentation in Paris. (John W. Smith hat bei der Präsentation in Paris gute Arbeit geleistet.)|
|Positiv|0,84 |Die Techniker aus Seattle haben bei der Parker-Verkaufspräsentation erstklassige Arbeit abgeliefert.|

Die Standpunktanalyse ist eine Veröffentlichungseinstellung, die auf jede Äußerung angewendet wird. Sie müssen die Wörter, die auf die Stimmung in einer Äußerung hinweisen, nicht suchen und markieren. 

Da es sich um eine Veröffentlichungseinstellung handelt, wird diese nicht auf den Seiten mit Absichten oder Entitäten angezeigt. Sie wird aber im Bereich für den [interaktiven Test](luis-interactive-test.md#view-sentiment-results) oder beim Testen an der Endpunkt-URL angezeigt. 


## <a name="create-a-new-app"></a>Erstellen einer neuen App

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Hinzufügen der vordefinierten PersonName-Entität 

1. Wählen Sie im Navigationsmenü **Build** aus.

1. Wählen Sie im linken Navigationsmenü die Option **Entities** (Entitäten) aus.

1. Wählen Sie die Schaltfläche **Add prebuilt entity** (Vordefinierte Entität hinzufügen).

1. Wählen Sie in der Liste der vordefinierten Entitäten die folgenden Entitäten und dann **Fertig** aus:

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Screenshot: Auswahl von „number“ im Dialogfeld mit den vordefinierten Entitäten](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Erstellen einer Absicht zum Bestimmen des Mitarbeiterfeedbacks

Fügen Sie eine neue Absicht für die Erfassung von internem Mitarbeiterfeedback hinzu. 

1. Klicken Sie im linken Bereich auf **Intents** (Absichten).

1. Wählen Sie **Create new intent** (Neue Absicht erstellen) aus.

1. Nennen Sie die neue Absicht `EmployeeFeedback`.

    ![Dialogfeld zum Erstellen einer neuen Absicht namens „EmployeeFeedback“](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Fügen Sie mehrere Äußerungen hinzu, in denen ein Mitarbeiter für etwas gelobt wird oder die einen Bereich mit Verbesserungsbedarf angeben:

    |Äußerungen|
    |--|
    |John Smith hat den Wiedereinstieg einer Kollegin nach dem Mutterschaftsurlaub sehr erleichtert|
    |Jill Jones hat sich mitfühlend um einen trauernden Kollegen gekümmert.|
    |Bob Barnes hatte nicht alle erforderlichen Rechnungen für die Unterlagen beisammen.|
    |Todd Thomas hat die erforderlichen Formulare einen Monat zu spät und ohne Unterschrift eingereicht.|
    |Katherine Kelly war bei der wichtigen externen Marketingbesprechung nicht anwesend.|
    |Denise Dillard war bei der Besprechung für die Juni-Prüfung nicht dabei.|
    |Mark Mathews hat die Verkaufspräsentation für Harvard exzellent gemeistert.|
    |Walter Williams hat bei der Präsentation für Stanford gute Arbeit geleistet.|

    Wählen Sie **Ansichtsoptionen** und dann **Entitätswerte anzeigen** aus, um die Namen anzuzeigen.

    [![Screenshot: LUIS-App mit Beispieläußerungen in der Absicht „EmployeeFeedback“](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Hinzufügen von Beispieläußerungen zur Absicht „None“ 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trainieren der App, um die Absichtsänderungen testen zu können 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Konfigurieren der App zur Aufnahme der Stimmungsanalyse

1. Klicken Sie im Navigationsbereich oben rechts auf **Verwalten**, und wählen Sie dann im linken Menü die Option **Veröffentlichungseinstellungen** aus.

1. Wählen Sie **Use sentiment analysis to determine if a user's utterance is positive, negative, or neutral** (Mithilfe der Stimmungsanalyse ermitteln, ob die Äußerung eines Benutzers positiv, negativ oder neutral ist) aus, um diese Einstellung zu aktivieren. 

    ![Aktivieren der Standpunktanalyse als Veröffentlichungseinstellung](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Veröffentlichen der App, damit das trainierte Modell über den Endpunkt abgefragt werden kann

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Abrufen der Stimmung einer Äußerung vom Endpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Geben Sie in der Adressleiste am Ende der URL die folgende Äußerung ein:

    `Jill Jones work with the media team on the public portal was amazing` 

    Der letzte Parameter der Abfragezeichenfolge lautet `q` (für die Abfrage (**query**) der Äußerung). Diese Äußerung entspricht keiner der bezeichneten Äußerungen. Sie ist daher ein guter Test und sollte die Absicht `EmployeeFeedback` mit der extrahierten Stimmungsanalyse zurückgeben.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    Die Standpunktanalyse ist positiv (Wert: 86 %). 

    Probieren Sie eine andere Äußerung aus, indem Sie den Wert für `q` in der Adressleiste des Browsers entfernen: `William Jones did a terrible job presenting his ideas.` Der Stimmungswert gibt durch Rückgabe einer niedrigen Bewertung (`0.18597582`) eine negative Stimmung an.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* Die Stimmungsanalyse wird von der Cognitive Service-[Textanalyse](../Text-Analytics/index.yml) bereitgestellt. Das Feature ist auf die von der Textanalyse [unterstützten Sprachen](luis-language-support.md##languages-supported) beschränkt.
* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wird die Standpunktanalyse als Veröffentlichungseinstellung zum Extrahieren von Stimmungswerten aus der gesamten Äußerung hinzugefügt.

> [!div class="nextstepaction"] 
> [Überprüfen von Endpunktäußerungen in der Personal-App](luis-tutorial-review-endpoint-utterances.md) 

