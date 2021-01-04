---
title: 'Tutorial: Extrahieren strukturierter Daten mit einer Machine Learning-Entität – LUIS'
description: Extrahieren strukturierter Daten aus einer Äußerung mithilfe einer Machine Learning-Entität. Fügen Sie untergeordnete Entitäten mit Features hinzu, um die Genauigkeit der Extraktion zu erhöhen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: aa2783abe86b90a907510dec8d7eb40162820ad5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025258"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Tutorial: Extrahieren strukturierter Daten aus Benutzeräußerungen mithilfe von Machine Learning-Entitäten in Language Understanding (LUIS)

In diesem Tutorial extrahieren Sie strukturierte Daten aus einer Äußerung mithilfe einer Machine Learning-Entität.

Die Machine Learning-Entität unterstützt das [Modellaufschlüsselungskonzept](luis-concept-model.md#v3-authoring-model-decomposition), indem sie untergeordnete Entitäten von Entitäten mit [Features](luis-concept-feature.md) bereitstellt.

**In diesem Tutorial lernen Sie Folgendes:**

> [!div class="checklist"]
> * Importieren der Beispiel-App
> * Hinzufügen einer Machine Learning-Entität
> * Hinzufügen von untergeordneten Entitäten und Features
> * Trainieren, Testen und Veröffentlichen einer App
> * Abrufen der Entitätsvorhersage vom Endpunkt

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Gründe für die Verwendung einer Machine Learning-Entität

In diesem Tutorial wird eine Machine Learning-Entität hinzugefügt, um Daten aus der Äußerung eines Benutzers zu extrahieren.

Die Entität definiert die Daten, die aus der Äußerung extrahiert werden sollen. Dies umfasst die Angabe eines Namens, eines Typs (falls möglich), einer beliebigen Auflösung der Daten bei Mehrdeutigkeit und des genauen Textes, aus dem die Daten bestehen.

Gehen Sie wie folgt vor, um die Daten zu definieren:
* Erstellen der Entität
* Bezeichnen Sie den Text in den Beispieläußerungen, die die Entität darstellen. Diese beschrifteten Beispiele vermitteln LUIS, worum es sich bei der Entität handelt und wo sie sich in einer Äußerung befindet.

## <a name="entity-decomposability-is-important"></a>Die Möglichkeit zur Aufschlüsselung von Entitäten ist wichtig.

Die Möglichkeit zur Aufschlüsselung von Entitäten ist sowohl für die Absichtsvorhersage als auch für die Datenextraktion mit der Entität wichtig.

Beginnen Sie mit einer Machine Learning-Entität, die den Anfang und die Entität der höchsten Ebene für die Datenextraktion darstellt. Unterteilen Sie die Entität anschließend in untergeordnete Entitäten.

Zu Beginn der Erstellung Ihrer App wissen Sie unter Umständen noch nicht, wie detailliert Ihre Entität sein soll. Es ist aber eine bewährte Methode, mit einer Machine Learning-Entität zu beginnen und dann mit der Unterteilung in untergeordnete Entitäten fortzufahren, während die Entwicklung Ihrer App voranschreitet.

In diesem Tutorial wird eine Machine Learning-Entität erstellt, um eine Bestellung für eine Pizza-App darzustellen. Die Entität extrahiert zunächst bestellungsbezogenen Text, z. B. zur Größe und Menge.

Bei einer Äußerung wie `Please deliver one large cheese pizza to me` sollte `one large cheese pizza` als Bestellung und dann `1` als Menge und `large` als Größe extrahiert werden.

## <a name="download-json-file-for-app"></a>Herunterladen der JSON-Datei für die App

Laden Sie die [App-JSON-Datei](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json) herunter, und speichern Sie sie.

## <a name="import-json-file-for-app"></a>Importieren der JSON-Datei für die App

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Erstellen einer Machine Learning-Entität

Um Details einer Pizzabestellung zu extrahieren, erstellen Sie eine Machine Learning-Entität `Order` auf höchster Ebene.

1. Wählen Sie auf der Seite **Absichten** die Absicht **OrderPizza** (Pizza bestellen) aus.

1. Wählen Sie in der Liste der Beispieläußerungen die folgende Äußerung aus.

    |Beispieläußerung zu einer Bestellung|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Klicken Sie, und ziehen Sie den Mauszeiger über den Text der ersten Beispielabsicht. Geben Sie im angezeigten Menü `Order` als Namen der Entität ein. Wählen Sie anschließend aus der Liste `Order Create new entity` aus.

    ![Bezeichnen von Anfang und Ende des Textes für die vollständige Bestellung](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Eine Entität ist nicht immer die gesamte Äußerung. In diesem speziellen Fall gibt `pickup` an, wie die Bestellung empfangen werden soll. Aus konzeptioneller Sicht sollte `pickup` Teil der bezeichneten Entität für die Bestellung sein.

1. Wählen Sie im Feld **Entitätstyp auswählen** die Option **Struktur hinzufügen** und dann **Weiter** aus. Die Struktur ist erforderlich, um untergeordnete Entitäten wie die Größe und Menge hinzuzufügen.

    ![Der Screenshot zeigt das Fenster „Entitätstyp auswählen“ mit aktivierter Option „Struktur hinzufügen“.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Wählen Sie im Feld **Add subentities (optional)** (Untergeordnete Entitäten hinzufügen (optional)) **+** in der Zeile `Order` aus, fügen Sie `Size` und `Quantity` als untergeordnete Entitäten hinzu, und wählen Sie dann **Erstellen** aus.

    > [!div class="mx-imgBorder"]
    > ![Der Screenshot zeigt das Fenster „Untergeordnete Entitäten hinzufügen (optional)“ mit hervorgehobenem untergeordneten Entitäten.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Bearbeiten von untergeordneten Entitäten zum Verbessern der Extraktion

In den vorherigen Schritten wurden die Entität und die untergeordnete Entität erstellt. Um die Extraktion zu verbessern, fügen Sie den untergeordneten Entitäten Features hinzu.

### <a name="improve-size-extraction-with-phrase-list"></a>Verbessern der Größenextraktion mit einer Ausdrucksliste

1. Wählen Sie im linken Menü **Entitäten** und dann die Entität **Order** (Bestellung) aus.

1. Wählen Sie auf der Registerkarte **Schema and features** (Schema und Features) die untergeordnete Entität **Size** (Größe) aus, und wählen Sie dann **+ Add feature** (+ Feature hinzufügen) aus.

1. Wählen Sie im Dropdownmenü **Create new phrase list** (Neue Ausdruckliste erstellen) aus.

1. Geben Sie im Feld **Neue Liste mit Ausdrücken erstellen** den Namen `SizePhraselist` und dann die Werte für `small`, `medium` und `large` ein. Wenn das Feld **Vorschläge** ausgefüllt ist, wählen Sie `extra large` und `xl` aus. Wählen Sie **Erstellen** aus, um die neue Ausdrucksliste zu erstellen.

    Dieses Ausdruckslistenfeature hilft der untergeordneten Entität `Size` dabei, Wörter mit Bezug zur Größe zu finden, indem Beispielwörter bereitgestellt werden. Diese Ausdrucksliste muss nicht jedes auf die Größe bezogene Wort enthalten, sondern sollte Wörter einbeziehen, die voraussichtlich die Größe angeben.

### <a name="add-sizelist-entity"></a>SizeList-Entität hinzufügen

Das Hinzufügen einer Liste bekannter Größen, die von der Clientanwendung erkannt werden, unterstützt auch die Extraktion.

1. Wählen Sie im linken Menü **Entitäten** und dann **+ Create** (+ Erstellen) aus.

1. Legen Sie den Entitätsnamen auf `SizeListentity` und den Typ auf **Liste** fest, sodass er im Vergleich mit `SizePhraselist` aus dem vorherigen Abschnitt leicht zu erkennen ist.

1. Fügen Sie die Größen hinzu, die die Client Anwendung erwartet – `Small`, `Medium`, `Large` und `XLarge` –, und fügen Sie dann für jede Synonyme hinzu. Die Synonyme sollten die Begriffe sein, die ein Benutzer im Chatbot eingibt. Die Entität wird mithilfe einer Listenentität extrahiert, wenn sie exakt mit dem normalisierten Wert oder den Synonymen übereinstimmt.

    |Normalisierter Wert|Synonyme|
    |--|--|
    |Klein|winzig, kleinste|
    |Medium|normal, mittel, Durchschnitt, mittelgroß|
    |Groß|größte|
    |Extra groß|XL, größte, riesig, max|


    > [!div class="mx-imgBorder"]
    > ![Der Screenshot zeigt das SizeList-Fenster und Listenelemente mit ausgewählter Option „XLarge“ an.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Hinzufügen eines Features aus der SizeList-Entität

1. Wählen Sie im linken Menü **Entitäten** aus, um zur Liste der Entitäten zurückzukehren.

1. Wählen Sie in der Liste der Entitäten **Bestellung** aus.

1. Wählen Sie auf der Registerkarte **Schema and features** (Schema und Features) die Entität **Size** (Größe) aus, und wählen Sie dann **+ Add feature** (+ Feature hinzufügen) aus.

1. Wählen Sie in der Dropdownliste den Eintrag **@ SizeListentity** aus.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Hinzufügen einer vordefinierten Zahlenentität

Das Hinzufügen einer vordefinierten Zahlenentität unterstützt auch die Extraktion.

1. Wählen Sie **Entitäten** aus dem linken Menü und dann **Vordefinierte Entität hinzufügen** aus.

1. Wählen Sie in der Liste **Zahl** und dann **Fertig** aus.

1. Wählen Sie im linken Menü **Entitäten** aus, um zur Liste der Entitäten zurückzukehren.

### <a name="add-feature-of-prebuilt-number-entity"></a>Hinzufügen eines Features aus der vordefinierten Zahlenentität

1. Wählen Sie in der Liste der Entitäten **Bestellung** aus.

1. Wählen Sie auf der Registerkarte **Schema and features** (Schema und Features) die Entität **Quantity** (Menge) aus, und wählen Sie dann **+ Add feature** (+ Feature hinzufügen) aus.

1. Wählen Sie in der Dropdownliste **@ number** aus.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Konfigurieren der erforderlichen Features

Wählen Sie auf der Seite mit Entitätsdetails der Entität **Order** (Bestellung) das Sternsymbol `*` für beide Features aus, **@ SizeList** und **@ number**. Das Sternsymbol wird in der gleichen Bezeichnung wie der Name des Features angezeigt.

> [!div class="mx-imgBorder"]
> ![Der Screenshot zeigt das @SizeList-Feature mit dem Sternsymbol und einer Erforderlich-Warnung.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Bezeichnen von Beispieläußerungen

Die Machine Learning-Entität wird erstellt, und die untergeordneten Entitäten weisen Features auf. Um die Verbesserung der Extraktion abzuschließen, müssen die Beispieläußerungen mit den untergeordneten Entitäten bezeichnet werden.

1. Wählen Sie im linken Navigationsbereich **Absichten** und dann die Absicht **OrderPizza** aus.

1. Wählen Sie zum Öffnen der **Entitätspalette** das Symbol **@** in der kontextbezogenen Symbolleiste aus.

1. Wählen Sie jede Entitätszeile in der Palette aus, und verwenden Sie anschließend den Palettencursor, um die Entität in jeder Beispieläußerung auszuwählen. Wenn Sie damit fertig sind, sollte Ihre Entitätenliste wie in der folgenden Abbildung aussehen.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mit Teilansicht der Konfiguration eines erforderlichen Features](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Trainieren der App

Wählen Sie zum Trainieren der App **Trainieren** aus. Das Training wendet die Änderungen, z. B. die neuen Entitäten und die bezeichneten Äußerungen, auf das aktive Modell an.

## <a name="add-a-new-example-utterance"></a>Hinzufügen einer neuen Beispieläußerung

1. Nach dem Training fügen Sie der `OrderPizza`-Absicht eine neue Beispieläußerung hinzu, um zu erfahren, wie gut LUIS die Machine Learning-Entität versteht.

    |Beispieläußerung zu einer Bestellung|
    |--|
    |`I need a large pepperoni pizza`|

    Die oberste Entität `Order` ist bezeichnet, und die untergeordnete Entität `Size` ist ebenfalls mit gepunkteten Linien gekennzeichnet.

    > [!div class="mx-imgBorder"]
    > ![Screenshot eines Teils einer neuen Beispieläußerung, die mithilfe einer Entität vorhergesagt wurde](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    Die gepunktete Linie zeigt die Vorhersage basierend auf der aktuellen trainierten App an.

1. Um aus der Vorhersage eine bezeichnete Entität zu machen, aktivieren Sie das Markierungshäkchen in der gleichen Zeile.

    > [!div class="mx-imgBorder"]
    > ![Der Screenshot zeigt eine Beispieläußerung mit hervorgehobenem Markierungshäkchen.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    An diesem Punkt funktioniert die Machine Learning-Entität, da sie die Entität innerhalb einer neuen Beispieläußerung finden kann. Wenn die Entität beim Hinzufügen von Beispieläußerungen nicht korrekt vorhergesagt wird, sollten Sie die Entität und die untergeordneten Entitäten bezeichnen. Wenn die Entität richtig vorhergesagt wird, stellen Sie sicher, dass Sie die Vorhersagen bestätigen.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Trainieren der App zum Anwenden der Entitätsänderungen auf die App

Wählen Sie **Trainieren** aus, um die App mit dieser neuen Äußerung zu trainieren.

An dieser Stelle weist die Bestellung einige Details auf, die extrahiert werden können (Größe, Menge und gesamter Text der Bestellung). Es gibt eine weitere Verfeinerung der `Order`-Entität, z. B. Pizzabeläge, Krustentyp und Nebenbestellungen. Jede dieser Komponenten sollte als untergeordnete Entität der Entität `Order` erstellt werden.

## <a name="test-the-app-to-validate-the-changes"></a>Testen der App zum Überprüfen der Änderungen

Testen Sie die App über den interaktiven **Testbereich**. Mit diesem Prozess können Sie eine neue Äußerung eingeben und dann die Vorhersageergebnisse anzeigen, um zu überprüfen, wie gut die aktive trainierte App funktioniert. Die Absichtsvorhersage sollte ziemlich zuverlässig sein (über 60 %) und die Entitätsextraktion sollte mindestens die `Order`-Entität aufnehmen. Die Details der Entität „Bestellung“ fehlen möglicherweise, da diese wenigen Äußerungen nicht ausreichen, um jeden Fall zu bearbeiten.

1. Klicken Sie auf der oberen Navigationsleiste auf **Test** (Testen).
1. Geben Sie die Äußerung `2 small cheese pizzas for pickup` ein, und wählen Sie die EINGABETASTE aus. Das aktive Modell hat die richtige Absicht mit einer Zuverlässigkeit von über 60 % vorhergesagt.


1. Wählen Sie **Überprüfen** aus, um die Entitätsvorhersagen anzuzeigen.

    > [!div class="mx-imgBorder"]
    > ![Screenshot eines Teils der Darstellung von Entitätsvorhersagen im interaktiven Testbereich.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Veröffentlichen der App für den Zugriff über den HTTP-Endpunkt

Damit Sie eine LUIS-Vorhersage in einem Chatbot oder einer anderen Clientanwendung empfangen können, muss die App im Endpunkt veröffentlicht werden.

1. Wählen Sie im Navigationsbereich rechts oben die Option **Veröffentlichen** aus.

    ![Screenshot: LUIS-Schaltfläche zum Veröffentlichen am Endpunkt (im Menü rechts oben)](./media/howto-publish/publish-button.png)

1. Wählen Sie den Slot **Produktion**, dann **Einstellungen ändern**, dann **Standpunktanalyse** und schließlich **Fertig** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: LUIS-Veröffentlichung am Endpunkt](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Wählen sie in der Benachrichtigung den Link **Access your endpoint URLs** (Auf Endpunkt-URLs zugreifen) aus, um die Seite **Azure-Ressourcen** aufzurufen. Die Endpunkt-URLs sind als **Beispielabfrage** aufgeführt.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Abrufen von Absicht und Entitätsvorhersage vom HTTP-Endpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Ersetzen Sie in der Adressleiste am Ende der URL die Angabe _YOUR_QUERY_HERE_ durch die Abfrage, die Sie im interaktiven Testbereich eingegeben haben.

    `2 small cheese pizzas for pickup`

    Der letzte Parameter der Abfragezeichenfolge lautet `query` (für die Abfrage (**query**) der Äußerung).

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* [Tutorial: Absichten](./tutorial-intents-only.md)
* [Konzept: Entitäten](luis-concept-entity-types.md) – Informationen zum Konzept
* [Konzept: Features](luis-concept-feature.md) – Informationen zum Konzept
* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial verwendet die App eine Machine Learning-Entität, um die Absicht der Äußerung eines Benutzers zu finden und Details aus dieser Äußerung zu extrahieren. Die Verwendung der Machine Learning-Entität ermöglicht es Ihnen, die Details der Entität aufzuschlüsseln.

> [!div class="nextstepaction"]
> [Hinzufügen einer vorgefertigten keyPhrase-Entität](./luis-reference-prebuilt-keyphrase.md)