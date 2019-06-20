---
title: Entität vom Typ „Regulärer Ausdruck“
titleSuffix: Azure Cognitive Services
description: Extrahieren Sie mithilfe der Entität vom Typ „Regulärer Ausdruck“ einheitlich formatierte Daten aus einer Äußerung.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 5e79de8dc8b4e81f427925b6e3d662bd4931804d
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497025"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Tutorial: Abrufen von gut formatierten Daten aus der Äußerung
In diesem Tutorial erstellen Sie eine App zum Extrahieren einheitlich formatierter Daten aus einer Äußerung mithilfe der Entität **Regulärer Ausdruck**.

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Erstellen einer neuen App 
> * Hinzufügen einer Absicht
> * Hinzufügen einer Entität vom Typ „Regulärer Ausdruck“ 
> * Trainieren
> * Veröffentlichen
> * Abrufen von Absichten und Entitäten vom Endpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>RegEx-Entitäten

In dieser App wird die Entität vom Typ „Regulärer Ausdruck“ verwendet, um gut formatierte Personalformularnummern aus einer Äußerung zu extrahieren. Zwar wird die Absicht der Äußerung immer durch Machine Learning bestimmt, doch wird bei diesem spezifischen Entitätstyp kein Machine Learning verwendet. 

**Hier sehen Sie einige einfache Beispieläußerungen:**

|Beispiele für Äußerungen|
|--|
|Wo ist HRF-123456?|
|Wer hat HRF-123234 erstellt?|
|Ist HRF-456098 in Französisch veröffentlicht?|
|HRF-456098|
|Welches Datum hat HRF-456098?|
 
Ein regulärer Ausdruck ist eine gute Wahl für diese Art von Daten, wenn Folgendes gilt:

* Die Daten sind korrekt formatiert.


## <a name="create-a-new-app"></a>Erstellen einer neuen App

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Erstellen einer Absicht zum Finden eines Formulars

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wählen Sie **Create new intent** (Neue Absicht erstellen) aus. 

1. Geben Sie im Popupdialogfeld die Zeichenfolge `FindForm` ein, und klicken Sie anschließend auf **Fertig**. 

    ![Screenshot: Dialogfeld zum Erstellen einer neuen Absicht mit eingegebenem Suchbegriff](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Fügen Sie der Absicht Beispieläußerungen hinzu.

    |Beispieläußerungen|
    |--|
    |What is the URL for hrf-123456? (Wie lautet die URL für hrf-123456?)|
    |Where is hrf-345678? (Wo ist hrf-345678?)|
    |When was hrf-456098 updated? (Wann wurde hrf-456098 aktualisiert?)|
    |Did John Smith update hrf-234639 last week? (Hat John Smith hrf-234639 letzte Woche aktualisiert?)|
    |How many version of hrf-345123 are there? (Wie viele-Versionen von hrf-345123 gibt es?)|
    |Who needs to authorize form hrf-123456? (Wer muss das Formular hrf-123456 autorisieren?)|
    |How many people need to sign off on hrf-345678? (Wie viele Personen müssen hrf-345678 genehmigen?)|
    |hrf-234123 date? (hrf-234123 Datum?)|
    |author of hrf-546234? (Ersteller von hrf-546234?)|
    |title of hrf-456234? (Titel von hrf-456234?)|

    [![Screenshot: Seite „Intents“ (Absichten) mit hervorgehobenen neuen Äußerungen](./media/luis-quickstart-intents-regex-entity/findform-intent.png)](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Verwenden der Entität „Regulärer Ausdruck“ für gut formatierte Daten
Die Entität vom Typ „Regulärer Ausdruck“ für den Abgleich der Formularnummer lautet `hrf-[0-9]{6}`. Dieser reguläre Ausdruck gleicht die Literalzeichen `hrf-` ab, ignoriert aber die Groß-/Kleinschreibung sowie kulturelle Varianten. Außerdem werden genau sechs Ziffern (jeweils von 0 bis 9) abgeglichen.

HRF steht für `human resources form`.

LUIS tokenisiert die Äußerung, wenn diese einer Absicht hinzugefügt wird. Bei der Tokenisierung für diese Äußerungen werden vor und nach dem Bindestrich Leerzeichen eingefügt: `Where is HRF - 123456?`. Der reguläre Ausdruck wird auf die Rohform der Äußerung (vor der Tokenisierung) angewendet. Aufgrund der Anwendung auf die _Rohform_ muss der reguläre Ausdruck keine Wortgrenzen berücksichtigen. 

In den folgenden Schritten erstellen Sie eine Entität vom Typ „Regulärer Ausdruck“, um LUIS mit dem Format von „HRF-number“ vertraut zu machen:

1. Klicken Sie im linken Bereich auf **Entitäten**.

1. Klicken Sie auf der Seite „Entitäten“ auf die Schaltfläche **Create new entity** (Neue Entität erstellen). 

1. Geben Sie im daraufhin angezeigten Popupdialogfeld `HRF-number` als Name der neuen Entität, **RegEx** als Entitätstyp und `hrf-[0-9]{6}` als Wert für **RegEx** ein, und klicken Sie anschließend auf **Fertig**.

    ![Screenshot: Popupdialogfeld zum Festlegen der Eigenschaften für die neue Entität](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Wählen Sie im linken Menü die Option **Intents** (Absichten) und dann die Absicht **FindForm** aus, um den gekennzeichneten regulären Ausdruck in den Äußerungen zu sehen. 

    [![Screenshot: Gekennzeichnete Äußerungen mit vorhandener Entität und RegEx-Muster](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Da die Entität keine ML-Entität ist, wird die Entität sofort nach der Erstellung auf die Äußerungen angewendet und auf der LUIS-Website angezeigt.

## <a name="add-example-utterances-to-the-none-intent"></a>Hinzufügen von Beispieläußerungen zur Absicht „None“ 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Trainieren der App vor dem Testen oder Veröffentlichen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Veröffentlichen der App, um sie vom Endpunkt aus abzufragen

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Abrufen von Absicht und Entitätsvorhersage vom Endpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Geben Sie in der Adressleiste am Ende der URL die folgende Äußerung ein:

    `When were HRF-123456 and hrf-234567 published in the last year?`

    Der letzte Parameter der Abfragezeichenfolge lautet `q` (für die Abfrage (**query**) der Äußerung). Diese Äußerung entspricht keiner der gekennzeichneten Äußerungen. Sie ist daher ein guter Test und sollte die Absicht `FindForm` mit den beiden Formularnummern `HRF-123456` und `hrf-234567` zurückgeben.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Durch die Verwendung einer Entität vom Typ „Regulärer Ausdruck“ extrahiert LUIS benannte Daten. Dies ist programmatisch hilfreicher für die Clientanwendung, die die JSON-Antwort erhält.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* Konzepte zur Entität [Regulärer Ausdruck](luis-concept-entity-types.md#regular-expression-entity)
* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurden eine neue Absicht erstellt, Beispieläußerungen hinzugefügt und dann eine Entität vom Typ „Regulärer Ausdruck“ erstellt, um korrekt formatierte Daten aus den Äußerungen zu extrahieren. Nach dem Trainieren und Veröffentlichen der App hat eine Abfrage des Endpunkts die Absicht identifiziert und die extrahierten Daten zurückgegeben.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Listenentität](luis-quickstart-intent-and-list-entity.md)

