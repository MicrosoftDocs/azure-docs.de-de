---
title: Tutorial zu zusammengesetzten Entitäten
titleSuffix: Azure Cognitive Services
description: Hinzufügen einer zusammengesetzten Entität, um extrahierte Daten verschiedenen Typs in einer einzelnen enthaltenden Entität zu bündeln. Durch Bündeln der Daten kann die Clientanwendung aufeinander bezogene Daten verschiedener Datentypen leicht extrahieren.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: a5cedec4937da276a4848498ad29a723f6e41ba3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073704"
---
# <a name="tutorial-group-and-extract-related-data"></a>Tutorial: Gruppieren und Extrahieren zugehöriger Daten
In diesem Tutorial fügen Sie eine zusammengesetzte Entität hinzu, um extrahierte Daten verschiedenen Typs in einer einzelnen enthaltenden Entität zu bündeln. Durch Bündeln der Daten kann die Clientanwendung aufeinander bezogene Daten verschiedener Datentypen leicht extrahieren.

Zusammengesetzte Entitäten dienen dazu, verknüpfte Entitäten in einer Entität der übergeordneten Kategorie zu gruppieren. Die Informationen sind vor dem Erstellen einer zusammengesetzten Entität jeweils separate Entitäten. 

Die zusammengesetzte Entität ist eine gute Wahl für diese Art von Daten, da für die Daten Folgendes gilt:

* Sind aufeinander bezogen. 
* Verwenden eine Vielzahl von Entitätstypen.
* Müssen gruppiert und von einer Client-App als eine Informationseinheit verarbeitet werden.

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importieren der Beispiel-App
> * Erstellen einer Absicht
> * Zusammengesetzte Entität hinzufügen 
> * Trainieren
> * Veröffentlichen
> * Abrufen von Absichten und Entitäten vom Endpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importieren der Beispiel-App

1.  Laden Sie die [JSON-Datei der App](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) aus dem Tutorial zum Entitätstyp „Liste“ herunter, und speichern Sie sie.

2. Importieren Sie den JSON-Code in eine neue App.

3. Klonen Sie die Version von der Registerkarte **Versionen** aus dem Abschnitt **Verwalten**, und geben Sie ihr den Namen `composite`. Durch Klonen können Sie ohne Auswirkungen auf die ursprüngliche Version mit verschiedenen Features von LUIS experimentieren. Da der Versionsname als Teil der URL-Route verwendet wird, darf er keine Zeichen enthalten, die in einer URL ungültig sind.

## <a name="composite-entity"></a>Entität vom Typ „Composite“

In dieser App ist der Abteilungsname in der Listenentität **Department** (Abteilung) definiert (einschließlich Synonymen). 

Die Absicht **TransferEmployeeToDepartment** verfügt über Beispieläußerungen, um die Versetzung eines Mitarbeiters in eine neue Abteilung anzufordern. 

Im Anschluss finden Sie einige Beispieläußerungen für diese Absicht:

|Beispiele für Äußerungen|
|--|
|„move John W. Smith to the accounting department“ (John W. Smith in die Buchhaltungsabteilung versetzen)|
|„transfer Jill Jones to R&D“ (Jill Jones in die Abteilung für Forschung und Entwicklung versetzen)|
 
Die Versetzungsanforderung muss den Namen der Abteilung und den Namen des Mitarbeiters enthalten. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Hinzufügen der vordefinierten Entität „PersonName“, um die Extraktion allgemeiner Datentypen zu vereinfachen

LUIS enthält mehrere vordefinierte Entitäten für das Extrahieren allgemeiner Daten. 

1. Wählen Sie im oberen Navigationsbereich die Option **Erstellen** und anschließend im linken Navigationsmenü die Option **Entitäten** aus.

1. Wählen Sie die Schaltfläche **Manage prebuilt entity** (Vordefinierte Entität verwalten) aus.

1. Wählen Sie in der Liste mit den vordefinierten Entitäten die Option **[PersonName](luis-reference-prebuilt-person.md)** und dann **Fertig** aus.

    ![Screenshot: Auswahl von „number“ im Dialogfeld mit den vordefinierten Entitäten](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Diese Entität hilft Ihnen dabei, Ihre Clientanwendung mit einer Namenserkennung auszustatten.

## <a name="create-composite-entity-from-example-utterances"></a>Erstellen einer zusammengesetzten Entität auf der Grundlage von Beispieläußerungen

1. Wählen Sie im linken Navigationsbereich **Absichten** aus.

1. Wählen Sie in der Liste mit den Absichten die Option **TransferEmployeeToDepartment** aus.

1. Wählen Sie in der Äußerung `place John Jackson in engineering` die personName-Entität `John Jackson` aus. Wählen Sie anschließend im Popupmenü für die folgende Äußerung die Option **In zusammengesetzter Entität umschließen** aus. 

    ![Screenshot: Auswahl des Umschließens in der zusammengesetzten Entität im Dropdowndialogfeld](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Wählen Sie dann die letzte Entität `engineering` in der Äußerung aus. Die markierten Wörter werden grün unterstrichen, was auf eine zusammengesetzte Entität hinweist. Geben Sie im Popupmenü den zusammengesetzten Namen `TransferEmployeeInfo` ein, und drücken Sie die EINGABETASTE. 

    ![Screenshot: Eingabe des zusammengesetzten Namens im Dropdowndialogfeld](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. Unter **Welche Arten von Entitäten möchten Sie erstellen?** sind alle Pflichtfelder in der Liste enthalten: `personName` und `Department`. Wählen Sie **Fertig**aus. Beachten Sie, dass der zusammengesetzten Entität die vordefinierte Entität „personName“ hinzugefügt wurde. Wenn eine vordefinierte Entität zwischen dem Anfangs- und dem Endtoken einer zusammengesetzten Entität auftreten kann, muss die zusammengesetzte Entität diese vordefinierten Entitäten enthalten. Wenn die vordefinierten Entitäten nicht enthalten sind, wird die zusammengesetzte Entität nicht ordnungsgemäß vorhergesagt, wohl aber die Einzelelemente.

    ![Screenshot: Eingabe des zusammengesetzten Namens im Dropdowndialogfeld](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Bezeichnen von Beispieläußerungen mit zusammengesetzter Entität

1. Wählen Sie in jeder Beispieläußerung die Entität ganz links aus, die sich in der zusammengesetzten Entität befinden sollte. Klicken Sie dann auf **Zusammengesetzte Entität umschließen**.

1. Wählen Sie das letzte Wort in der zusammengesetzten Entität und anschließend im Popupmenü die Option **TransferEmployeeInfo** aus. 

1. Überprüfen Sie, ob alle Äußerungen in der Absicht mit der zusammengesetzten Entität bezeichnet sind. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trainieren der App, um die Absichtsänderungen testen zu können 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Veröffentlichen der App, damit das trainierte Modell über den Endpunkt abgefragt werden kann

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Abrufen von Absicht und Entitätsvorhersage vom Endpunkt 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Geben Sie in der Adressleiste am Ende der URL `Move Jill Jones to DevOps` ein. Der letzte Parameter der Abfragezeichenfolge lautet `q` (für die Abfrage (query) der Äußerung). 

    Da mit diesem Test überprüft werden soll, ob die zusammengesetzte Entität korrekt extrahiert wurde, kann ein Test entweder eine vorhandene Beispieläußerung oder eine neue Äußerung enthalten. Ein guter Test beinhaltet alle untergeordneten Entitäten in der zusammengesetzten Entität.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Diese Äußerung gibt ein Array von zusammengesetzten Entitäten zurück. Jede Entität ist mit einem Typ und einem Wert versehen. Um die Genauigkeit für jede untergeordnete Entität zu erhöhen, verwenden Sie die Kombination aus Typ und Wert des Arrays von zusammengesetzten Elementen, um nach dem entsprechenden Element im Entitätenarray zu suchen.  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* [Tutorial zum Entitätstyp „Liste“](luis-quickstart-intents-only.md)
* [Informationen zum Konzept der zusammengesetzten Entität](luis-concept-entity-types.md)
* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde eine zusammengesetzte Entität zur Verkapselung vorhandener Entitäten verwendet. Dadurch kann die Clientanwendung eine Gruppe auf einander bezogener Daten in verschiedenen Datentypen finden, um die Unterhaltung fortzusetzen. Eine Clientanwendung für diese Personalwesen-App könnte fragen, an welchem Tag und um welche Uhrzeit die Verlegung beginnen und enden muss. Sie kann auch nach anderen Logistikdetails fragen (etwa nach einem physischen Telefon). 

> [!div class="nextstepaction"] 
> [Erfahren Sie, wie eine einfache Entität mit einer Ausdrucksliste hinzugefügt wird.](luis-quickstart-primary-and-secondary-data.md)  
