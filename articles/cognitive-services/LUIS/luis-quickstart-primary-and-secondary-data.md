---
title: Simple-Entität, Begriffsliste
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial werden maschinell gelernte Daten einer Stellenbezeichnung mithilfe der Simple-Entität aus einer Äußerung extrahiert. Um die Genauigkeit der Extraktion zu steigern, fügen Sie der Simple-Entität eine Begriffsliste mit spezifischen Ausdrücken hinzu.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: ea9a2df1f06ba6836ef88bc57dc3f95fd31e1ee9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66124228"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Tutorial: Extrahieren von Namen mit der Simple-Entität und einer Begriffsliste

In diesem Tutorial werden maschinell gelernte Daten einer Stellenbezeichnung mithilfe der **Simple**-Entität aus einer Äußerung extrahiert. Um die Genauigkeit der Extraktion zu steigern, fügen Sie der Simple-Entität eine Begriffsliste mit spezifischen Ausdrücken hinzu.

Die Simple-Entität erkennt ein einfaches Datenkonzept, das in Wörtern oder Formulierungen enthalten ist.

**In diesem Tutorial lernen Sie Folgendes:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importieren der Beispiel-App
> * Hinzufügen der Entität vom Typ „Simple“ 
> * Hinzufügen einer Begriffsliste, um Signalwörter zu verstärken
> * Trainieren 
> * Veröffentlichen 
> * Abrufen von Absichten und Entitäten vom Endpunkt

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Entität vom Typ „Simple“

In diesem Tutorial wird eine neue Simple-Entität hinzugefügt, um die Stellenbezeichnung zu extrahieren. Mithilfe der Entität vom Typ „Einfach“ für diese LUIS-App wird LUIS beigebracht, was ein Stellenname ist und wie er in einer Äußerung ermittelt werden kann. Der Teil der Äußerung, der die Stellenbezeichnung darstellt, kann sich basierend auf der Wortwahl und der Länge der Äußerung bei den einzelnen Äußerungen unterscheiden. LUIS benötigt Beispiele für Stellenbezeichnungen aus allen Absichten, in denen Stellenbezeichnungen verwendet werden.  

Die Simple-Entität eignet sich gut für diese Art von Daten, wenn folgende Bedingungen erfüllt sind:

* Die Daten stellen nur ein Konzept dar.
* Die Daten sind nicht wohlgeformt, wie etwa ein regulärer Ausdruck.
* Die Daten sind nicht gängig, etwa wie eine vorkonfigurierte Entität von Telefonnummer oder Daten.
* Die Daten stimmen nicht exakt mit einer Liste bekannter Wörter überein, wie etwa eine Listenentität.
* Die Daten enthalten keine anderen Datenelemente, z. B. eine zusammengesetzte Entität oder kontextbezogene Rollen.

Sehen Sie sich die folgenden Äußerungen eines Chatbots an:

|Äußerung|Extrahierbarer Stellenname|
|:--|:--|
|I want to apply for the new accounting job. (Ich möchte mich für die neue Stelle in der Buchhaltung bewerben.)|accounting (Buchhaltung)|
|Submit my resume for the engineering position. (Meinen Lebenslauf für die Technikerstelle einsenden.)|engineering (Techniker)|
|Fill out application for job 123456 (Bewerbungsbogen für die Stelle 123456 ausfüllen)|123456|

Der Stellenname ist nicht ganz einfach zu ermitteln, da es sich dabei um ein Substantiv, um ein Verb oder um einen Ausdruck mit mehreren Wörtern handeln kann. Beispiel: 

|Aufträge|
|--|
|mitzuteilen.|
|software engineer (Softwareentwickler)|
|senior software engineer (leitender Softwareentwickler)|
|engineering team lead (Leiter des Technikerteams) |
|air traffic controller (Fluglotse)|
|motor vehicle operator (Kraftfahrer)|
|ambulance driver (Krankenwagenfahrer)|
|tender (Pfleger)|
|extruder (Strangpresser)|
|millwright (Mühlenbauer)|

Diese LUIS-App verfügt über Stellennamen in verschiedenen Absichten. Durch die Kennzeichnung dieser Wörter in allen Äußerungen der Absichten lernt LUIS mehr darüber, was eine Stellenbezeichnung ist und wo sie sich in Äußerungen befindet.

Nachdem die Entitäten in den Beispieläußerungen markiert sind, ist es wichtig, eine Begriffsliste hinzuzufügen, um das Signal der Simple-Entität zu verstärken. Eine Begriffsliste wird **nicht** als genaue Übereinstimmung verwendet und braucht nicht jeden möglichen erwartbaren Wert zu enthalten. 

## <a name="import-example-app"></a>Importieren der Beispiel-App

1.  Laden Sie die [JSON-Datei der App](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) aus dem Absichts-Tutorial herunter, und speichern Sie sie.

2. Importieren Sie den JSON-Code in eine neue App.

3. Klonen Sie die Version von der Registerkarte **Versionen** aus dem Abschnitt **Verwalten**, und geben Sie ihr den Namen `simple`. Durch Klonen können Sie ohne Auswirkungen auf die ursprüngliche Version mit verschiedenen Features von LUIS experimentieren. Da der Versionsname als Teil der URL-Route verwendet wird, darf er keine Zeichen enthalten, die in einer URL ungültig sind.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Markieren von Entitäten in Beispieläußerungen einer Absicht

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Wählen Sie auf der Seite **Absichten** die Option **ApplyForJob** aus. 

1. Wählen Sie in der Äußerung `I want to apply for the new accounting job` das Wort `accounting` aus, geben Sie `Job` in das oberste Feld des Popupmenüs ein, und klicken Sie anschließend im Popupmenü auf **Create new entity** (Neue Entität erstellen). 

    [![Screenshot: LUIS mit Absicht „ApplyForJob“ und hervorgehobenen Schritten für die Entitätserstellung](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Screenshot: LUIS mit Absicht „ApplyForJob“ und hervorgehobenen Schritten für die Entitätserstellung")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Überprüfen Sie Entitätsname und -typ im Popupfenster, und klicken Sie anschließend auf **Fertig**.

    ![Modales Popupdialogfeld zum Erstellen einer einfachen Entität mit dem Namen „Job“ und dem Typ „Simple“](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Markieren Sie in den verbleibenden Äußerungen die auf Stellen bezogenen Wörter mit der **Job**-Entität, indem Sie das Wort oder den Begriff auswählen und dann im Popupmenü **Job** auswählen. 

    [![Screenshot: LUIS mit hervorgehobener Entitätskennzeichnung „Job“](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Screenshot: LUIS mit hervorgehobener Entitätskennzeichnung „Job“")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Hinzufügen weiterer Beispieläußerungen und Markierungsentitäten

Entitäten vom Typ „Simple“ erfordern viele Beispiele, um zu einer guten Konfidenz der Vorhersage zu gelangen. 
 
1. Fügen Sie weitere Äußerungen hinzu, und kennzeichnen Sie die Wörter und Ausdrücke für die Stelle jeweils mit der Entität **Job**. 

    |Äußerung|Entität „Job“|
    |:--|:--|
    |I'm applying for the Program Manager desk in R&D (Ich bewerbe mich für die Position als Programm-Manager in der Entwicklungsabteilung.)|Program Manager (Programm-Manager)|
    |Here is my line cook application. (Hier ist meine Bewerbung als Gardemanger.)|line cook (Gardemanger)|
    |My resume for camp counselor is attached. (Anbei mein Lebenslauf für die Stelle als Camp Counselor.)|camp counselor|
    |This is my c.v. for administrative assistant. (Hier ist mein CV für die Stelle als Verwaltungsassistent.)|administrative assistant (Verwaltungsassistent)|
    |I want to apply for the management job in sales. (Hiermit bewerbe ich mich für die Management-Stelle im Vertrieb.)|management, sales (Management, Vertrieb)|
    |This is my resume for the new accounting position. (Hiermit übersende ich Ihnen meinen Lebenslauf für die neue Position in der Buchhaltung.)|accounting (Buchhaltung)|
    |My application for barback is included. (Meine Bewerbung als Barassistent finden Sie anbei.)|barback (Barassistent)|
    |I'm submitting my application for roofer and framer. (Hiermit übersende ich Ihnen meine Bewerbung als Dachdecker und Zimmerer.)|roofer, framer (Dachdecker, Zimmerer)|
    |My c.v. for bus driver is here. (Mein Lebenslauf für die Stelle als Busfahrer ist beigefügt.)|bus driver (Busfahrer)|
    |I'm a registered nurse. Here is my resume. (Ich bin ausgebildete Krankenschwester. Hier ist mein Lebenslauf.)|registered nurse (ausgebildete Krankenschwester)|
    |I would like to submit my paperwork for the teaching position I saw in the paper. (Hiermit möchte ich Ihnen meine Unterlagen für die ausgeschriebene Stelle als Lehrkraft zukommen lassen.)|teaching (Lehrkraft)|
    |This is my c.v. for the stocker post in fruits and vegetables. (Hier ist mein Lebenslauf für die Stelle als Auffüller in der Obst- und Gemüseabteilung.)|stocker (Auffüller)|
    |Apply for tile work. (Bewerbung als Fliesenleger.)|Kachel|
    |Attached resume for landscape architect. (Anbei mein Lebenslauf für die Position als Landschaftsarchitekt.)|landscape architect (Landschaftsarchitekt)|
    |My curriculum vitae for professor of biology is enclosed. (Anbei finden Sie mein Curriculum Vitae für die Stelle als Biologieprofessor.)|professor of biology (Biologieprofessor)|
    |I would like to apply for the position in photography. (Ich möchte mich gerne als Fotograf bewerben.)|photography (Fotografie)|

## <a name="mark-job-entity-in-other-intents"></a>Markieren der Job-Entität in weiteren Absichten

1. Wählen Sie im linken Menü die Option **Intents** (Absichten) aus.

1. Wählen Sie in der Liste mit den Absichten die Option **GetJobInformation** aus. 

1. Bezeichnen Sie die Stellen in den Beispieläußerungen

    Wenn eine Absicht mehr Beispieläußerungen als eine andere enthält, wird diese Absicht mit größerer Wahrscheinlichkeit im Text als hoch vorhergesagt. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Trainieren der App, um die Absichtsänderungen testen zu können 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Veröffentlichen der App, damit das trainierte Modell über den Endpunkt abgefragt werden kann

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Abrufen von Absicht und Entitätsvorhersage vom Endpunkt 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Geben Sie in der Adressleiste am Ende der URL `Here is my c.v. for the engineering job` ein. Der letzte Parameter der Abfragezeichenfolge lautet `q` (die Äußerung **query** (Abfrage)). Diese Äußerung entspricht keiner der bezeichneten Äußerungen. Sie ist daher ein guter Test und sollte die Äußerungen vom Typ `ApplyForJob` zurückgeben.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS hat die richtige Absicht, **ApplyForJob**, gefunden und die richtige Entität extrahiert, **Stelle**, mit dem Wert `engineering`.


## <a name="names-are-tricky"></a>Das Problem mit Namen
Die LUIS-App hat die korrekte Absicht mit hoher Zuverlässigkeit ermittelt und den Stellennamen extrahiert, aber Namen sind kompliziert. Nehmen wir beispielsweise die Äußerung `This is the lead welder paperwork`.  

Im folgenden JSON-Code antwortet LUIS zwar mit der korrekten Absicht (`ApplyForJob`), extrahiert aber den Stellennamen `lead welder` nicht: 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Da ein Name eine beliebige Zeichenfolge sein kann, sagt LUIS Entitäten präziser voraus, wenn das Signal durch eine Begriffsliste mit Wörtern verstärkt wird.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Um das Signal der stellenbezogenen Wörter zu verstärken, fügen Sie eine Begriffsliste mit auf Stellen bezogenen Wörtern hinzu

Öffnen Sie die Datei [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) aus dem GitHub-Repository „Azure-Samples“. Die Liste enthält über 1.000 stellenbezogene Wörter und Begriffe. Suchen Sie in der Liste nach Stellenwörtern, die für Sie relevant sind. Sollten die gewünschten Wörter oder Begriffe nicht in der Liste enthalten sein, fügen Sie eigene Wörter/Begriffe hinzu.

1. Klicken Sie im Abschnitt **Build** der LUIS-App im Menü **Improve app performance** (App-Leistung verbessern) auf **Phrase lists** (Begriffslisten).

1. Klicken Sie auf **Create new phrase list** (Neue Begriffsliste erstellen). 

1. Nennen Sie die Begriffsliste `JobNames`, und kopieren Sie die Liste aus „jobs-phrase-list.csv“ in das Textfeld **Werte**. Drücken Sie die EINGABETASTE. 

    [![Screenshot: Popupdialogfeld zum Erstellen einer neuen Begriffsliste](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Screenshot: Popupdialogfeld zum Erstellen einer neuen Begriffsliste")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Wenn Sie der Begriffsliste weitere Wörter hinzufügen möchten, wählen Sie **Empfehlen**, überprüfen Sie dann die neuen **verwandte Werte** und fügen sie alle relevanten Werte hinzu. 

    Achten Sie darauf, dass die Option **Diese Werte sind austauschbar** aktiviert bleibt, da diese Werte alle als Synonyme für Aufträge behandelt werden sollen. Erfahren Sie mehr über die Konzepte von austauschbaren bzw. nicht austauschbaren [Begriffslisten](luis-concept-feature.md#how-to-use-phrase-lists).

1. Klicken Sie auf **Speichern**, um die Begriffsliste zu aktivieren.

    [![Screenshot des Popupdialogfelds zum Erstellen einer neuen Begriffsliste mit Wörtern im Feld für die Begriffslistenwerte](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Screenshot des Popupdialogfelds zum Erstellen einer neuen Begriffsliste mit Wörtern im Feld für die Begriffslistenwerte")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Trainieren und veröffentlichen Sie die App erneut, um die Begriffsliste zu verwenden.

1. Fragen Sie den Endpunkt erneut mit der gleichen Äußerung ab: `This is the lead welder paperwork.`

    Die JSON-Antwort enthält die extrahierte Entität:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Verwandte Informationen

* [Tutorial zu Absichten ohne Entitäten](luis-quickstart-intents-only.md)
* Informationen zum Konzept der [Simple-Entitäten](luis-concept-entity-types.md)
* Informationen zum Konzept der [Begriffsliste](luis-concept-feature.md)
* [Informationen zum Trainieren](luis-how-to-train.md)
* [Informationen zum Veröffentlichen](luis-how-to-publish-app.md)
* [Informationen zum Testen im LUIS-Portal](luis-interactive-test.md)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial verwendet die Personalwesen-App eine maschinell gelernte Simple-Entität, um Stellenbezeichnungen in Äußerungen zu finden. Da Stellenbezeichnungen eine so große Bandbreite an Wörtern und Formulierungen aufweisen können, benötigte die App eine Begriffsliste, um die Wörter zu verstärken. 

> [!div class="nextstepaction"]
> [Hinzufügen einer vorgefertigten keyPhrase-Entität](luis-quickstart-intent-and-key-phrase.md)
