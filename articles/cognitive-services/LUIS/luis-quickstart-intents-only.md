---
title: Vorhersagen von Absichten
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial erstellen Sie eine benutzerdefinierte App, die die Absicht eines Benutzers vorhersagt. Diese App stellt den einfachsten Typ von LUIS-App dar, da sie keine verschiedenen Datenelemente, wie E-Mail-Adressen oder Datumsangaben, aus dem Äußerungstext extrahiert.
services: cognitive-services
author: lingliw
manager: digimobile
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/19/19
ms.author: v-lingwu
ms.openlocfilehash: 067829a1d9425ede1320242e364eca7c30bb7053
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66123934"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Tutorial: Erstellen einer LUIS-App zum Bestimmen von Benutzerabsichten

In diesem Tutorial erstellen Sie eine benutzerdefinierte Personalwesen-App, die die Absicht eines Benutzers auf der Grundlage seiner Äußerung (Text) vorhersagt. 

**In diesem Tutorial lernen Sie Folgendes:**

> [!div class="checklist"]
> * Erstellen einer neuen App 
> * Erstellen von Absichten
> * Hinzufügen von Beispieläußerungen
> * Trainieren der App
> * App veröffentlichen
> * Absicht beim Endpunkt abrufen


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Benutzerintentionen als Absichten

Der Zweck der App besteht darin, die Intention von dialogorientiertem, natürlichsprachigem Text zu erkennen: 

`Are there any new positions in the Seattle office?`

Diese Intentionen sind in **Absichten** kategorisiert. 

Diese App verfügt über einige Absichten. 

|Absicht|Zweck|
|--|--|
|ApplyForJob|Bestimmt, ob der Benutzer sich auf eine Stelle bewirbt.|
|GetJobInformation|Bestimmt, ob der Benutzer nach Informationen über Stellen im Allgemeinen oder über eine bestimmte Stelle sucht.|
|Keine|Bestimmt, ob der Benutzer etwas fragt, was von der App nicht beantwortet werden soll. Diese Absicht steht im Rahmen der App-Erstellung zur Verfügung und kann nicht gelöscht werden. |

## <a name="create-a-new-app"></a>Erstellen einer neuen App

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Absicht für Stelleninformationen erstellen

1. Wählen Sie **Create new intent** (Neue Absicht erstellen) aus. Geben Sie den Namen für die neue Absicht ein: `GetJobInformation`. Diese Absicht wird vorhergesagt, wenn ein Benutzer Informationen über offene Stellen im Unternehmen möchte. 

    ![Screenshot des Dialogfelds „Neus Absicht“ in Language Understanding (LUIS)](media/luis-quickstart-intents-only/create-intent.png "Screenshot des Dialogfelds „Neus Absicht“ in Language Understanding (LUIS)")

1. Wählen Sie **Fertig**aus.

2. Fügen Sie dieser Absicht mehrere voraussichtliche Beispieläußerungen von Benutzern hinzu:

    | Beispiele für Äußerungen|
    |--|
    |Wurden heute neue Stellen ausgeschrieben?|
    |Gibt es neue Stellen in Seattle?|
    |Gibt es offene Stellen für Ingenieure als Remote Worker oder als Telearbeiter?|
    |Gibt es offene Stellen im Datenbankbereich?|
    |Ich suche nach einer Co-Working-Möglichkeit in der Niederlassung in Tampa.|
    |Gibt es in der Niederlassung in San Francisco eine Praktikumsstelle?|
    |Gibt es Teilzeitjobs für Studenten?|
    |Ich möchte mich beruflich neu orientieren und suche nach Aufgaben in der Buchhaltung.|
    |Ich suche nach einer Stelle in New York City für zweisprachige Mitarbeiter.|
    |Ich suche nach einer neuen Anstellung in leitender Position im Bereich Buchhaltung.|
    |Neue Stellen?|
    |Zeige mit alle Stellen für Ingenieure, die wir in den letzten zwei Tagen neu hinzugefügt haben.|
    |Stellenausschreibungen von heute?|
    |Welche Stellen für Buchhalter sind in der Niederlassung London frei?|
    |Welche Positionen sind für Oberingenieure verfügbar?|
    |Wo finde ich die Stellenangebote?|

    [![Screenshot der Eingabe neuer Äußerungen für die Absicht „MyStore“](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Screenshot der Eingabe neuer Äußerungen für die Absicht „MyStore“")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Durch die Bereitstellung von _Beispieläußerungen_ trainieren Sie LUIS, welche Art von Äußerungen für diese Absicht vorhergesagt werden sollten. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Hinzufügen von Beispieläußerungen zur Absicht „None“ 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Trainieren der App vor dem Testen oder Veröffentlichen

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Veröffentlichen Sie die App, um sie vom Endpunkt aus abzufragen

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Abrufen der Absichtsvorhersage vom Endpunkt

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Geben Sie in der Adressleiste am Ende der URL `I'm looking for a job with Natural Language Processing` ein. Der letzte Parameter der Abfragezeichenfolge lautet `q` (für die Abfrage (**query**) der Äußerung). Diese Äußerung stimmt nicht mit einer der Beispieläußerungen überein. Sie stellt einen guten Test dar und sollte die Absicht `GetJobInformation` als Absicht mit der höchsten Bewertung zurückgeben. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Der Abfragezeichenfolgen-Parameter `verbose=true` bedeutet, dass **alle Absichten** in die Abfrageergebnisse der App aufgenommen werden sollen. Das Entitätenarray ist leer, weil diese App aktuell keine Entitäten aufweist. 

    Das JSON-Ergebnis identifiziert die am höchsten bewertete Absicht als **`topScoringIntent`**-Eigenschaft. Alle Bewertungen liegen zwischen 1 und 0. Die bessere Bewertung liegt näher bei 1. 

## <a name="create-intent-for-job-applications"></a>Absicht für Bewerbungen auf Stellen erstellen

Kehren Sie zum LUIS-Portal zurück, und erstellen Sie eine neue Absicht, um zu bestimmen, ob sich die Benutzeräußerung um eine Stellenbewerbung dreht.

1. Klicken Sie im Menü oben rechts auf **Build** (Erstellen), um zur App-Erstellung zurückzukehren.

1. Wählen Sie im linken Menü **Absichten** aus, um die Liste der Absichten anzuzeigen.

1. Klicken Sie auf **Create new intent** (Neue Absicht erstellen), und geben Sie den Namen `ApplyForJob` ein. 

    ![LUIS-Dialogfeld zum Erstellen einer neuen Absicht](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Fügen Sie dieser Absicht mehrere voraussichtliche Äußerungen von Benutzern hinzu, z.B.:

    | Beispiele für Äußerungen|
    |--|
    |Bewerbungsbogen für die Stelle 123456 ausfüllen|
    |Hier ist mein Lebenslauf für die Position 654234|
    |Hier ist mein Lebenslauf für die Teilzeitstelle am Empfang.|
    |Ich bewerbe mich mit diesen Unterlagen auf die Stelle als Grafiker.|
    |Ich bewerbe mich für das Sommerpraktikum für Studenten in der Forschungs- und Entwicklungsabteilung in San Diego|
    |Ich bitte, meinen Lebenslauf für die befristete Stelle in der Cafeteria weiterzuleiten.|
    |Ich reiche meinen Lebenslauf zur Bewerbung beim neuen Autocar-Team in Columbus, Ohio, ein.|
    |Ich möchte mich für die neue Stelle in der Buchhaltung bewerben|
    |Die Bewerbungsunterlagen für Stelle 456789, Buchhaltungspraktikum, sind hier|
    |Stelle 567890 und meine Unterlagen|
    |Meine Unterlagen für das Buchhaltungspraktikum in Tulsa sind beigefügt.|
    |Meine Papiere für die Ferienposition in der Auslieferung|
    |Senden Sie meinen Lebenslauf für die neue Buchhalterstelle in Seattle|
    |Lebenslauf für die Stelle als Techniker übermitteln|
    |This is my c.v. für Stelle 234123 in Tampa.|

<!--

    [![Screenshot of entering new utterances for ApplyForJob intent](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot of entering new utterances for ApplyForJob intent")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    The labeled intent is outlined in red because LUIS is currently uncertain the intent is correct. Training the app tells LUIS the utterances are on the correct intent. 

-->

## <a name="train-again"></a>Erneutes Trainieren

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Erneutes Veröffentlichen

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Absichtsvorhersage erneut abrufen

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Geben Sie im neuen Browserfenster `Can I submit my resume for job 235986` am Ende der URL ein. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    In den Ergebnissen ist die neue Absicht **ApplyForJob** ebenso enthalten wie die beiden vorhandenen Absichten. 

## <a name="client-application-next-steps"></a>Nächste Schritte in der Clientanwendung

Nachdem LUIS die JSON-Antwort zurückgegeben hat, hat LUIS diese Anforderung erledigt. LUIS gibt keine Antworten auf Benutzeräußerungen, es identifiziert lediglich, nach welcher Art Information in natürlicher Sprache gefragt wird. Der Dialoganschluss wird von der Clientanwendung, etwa einem Azure Bot, bereitgestellt. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* [Typen von Entitäten](luis-concept-entity-types.md)
* [Gewusst wie: trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Gewusst wie: Testen im LUIS-Portal](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden die Personalwesen-App und zwei Absichten erstellt, Beispieläußerungen für jede Absicht hinzugefügt, Beispieläußerungen zur Absicht „None“ (Keine) hinzugefügt, die App trainiert, veröffentlicht und am Endpunkt getestet. Dies sind die grundlegenden Schritte bei der Erstellung eines LUIS-Modells. 

Fahren Sie mit dieser App fort, und [fügen Sie eine einfache Entität und eine Ausdrucksliste hinzu](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Add prebuilt intents and entities to this app (Hinzufügen von vordefinierten Absichten und Entitäten)](luis-tutorial-prebuilt-intents-entities.md)




