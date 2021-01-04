---
title: 'Abfragen der Wissensdatenbank: QnA Maker'
description: Eine Wissensdatenbank muss veröffentlicht werden. Nach der Veröffentlichung wird die Wissensdatenbank mithilfe der generateAnswer-API am Vorhersageendpunkt der Runtime abgefragt.
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: d8f986299edee46bf5cace7a9f4c805c29b3ce0c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346204"
---
# <a name="query-the-knowledge-base-for-answers"></a>Abfragen der Wissensdatenbank nach Antworten

Eine Wissensdatenbank muss veröffentlicht werden. Nach der Veröffentlichung wird die Wissensdatenbank mithilfe der generateAnswer-API am Vorhersageendpunkt der Runtime abgefragt. Die Abfrage enthält den Fragetext und andere Einstellungen, mit denen QnA Maker die bestmögliche Entsprechung für eine Antwort auswählen kann.

## <a name="how-qna-maker-processes-a-user-query-to-select-the-best-answer"></a>Verarbeitung einer Benutzerabfrage mit QnA Maker, um die beste Antwort auszuwählen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Die trainierte und [veröffentlichte](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker-Wissensdatenbank empfängt eine Benutzerabfrage von einem Bot oder einer anderen Clientanwendung über die [GenerateAnswer-API](../how-to/metadata-generateanswer-usage.md). Im folgenden Diagramm ist der Prozess nach dem Empfang der Benutzerabfrage dargestellt.

![Einstufungsmodellprozess für eine Benutzerabfrage](../media/qnamaker-concepts-knowledgebase/ranker-v1.png)

### <a name="ranker-process"></a>Einstufungsprozess

Der Prozess wird in der folgenden Tabelle erläutert:

|Schritt|Zweck|
|--|--|
|1|Die Clientanwendung sendet die Benutzerabfrage an die [GenerateAnswer-API](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker führt die Vorverarbeitung der Benutzerabfrage mit Spracherkennung, Rechtschreibprüfung und Worttrennung durch.|
|3|Diese Vorverarbeitung hat das Ziel, die Benutzerabfrage so anzupassen, dass die besten Suchergebnisse erzielt werden.|
|4|Die geänderte Abfrage wird an den Azure Cognitive Search-Index gesendet, der die mit `top` festgelegte Anzahl von Ergebnissen empfängt. Falls diese Ergebnisse die richtige Antwort nicht enthalten, sollten Sie den Wert von `top` leicht erhöhen. Im Allgemeinen eignet sich der Wert „10“ für `top` bei 90 % aller Abfragen.|
|5|QnA Maker verwendet eine auf Syntax und Semantik basierende Featurebereitstellung, um die Ähnlichkeit zwischen der Benutzerabfrage und den abgerufenen QnA-Ergebnissen zu ermitteln.|
|6|Das maschinell erlernte Bewertermodell verwendet die verschiedenen Features aus Schritt 5, um die Zuverlässigkeitsbewertungen und die neue Rangfolge zu ermitteln.|
|7|Die neuen Ergebnisse werden in der Rangfolge an die Clientanwendung zurückgegeben.|
|||

Zu den verwendeten Funktionen gehören u. a. Semantik auf Wortebene, Wichtigkeit auf Begriffsebene in einem Korpus und Deep Learning-Semantikmodelle, um die Ähnlichkeit und Relevanz zwischen zwei Textzeichenfolgen zu ermitteln.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Die trainierte und [veröffentlichte](../quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) QnA Maker-Wissensdatenbank empfängt eine Benutzerabfrage von einem Bot oder einer anderen Clientanwendung über die [GenerateAnswer-API](../how-to/metadata-generateanswer-usage.md). Im folgenden Diagramm ist der Prozess nach dem Empfang der Benutzerabfrage dargestellt.

![Einstufungsmodellprozess für eine Benutzerabfrage (Vorschau)](../media/qnamaker-concepts-knowledgebase/ranker-v2.png)

### <a name="ranker-process"></a>Einstufungsprozess

Der Prozess wird in der folgenden Tabelle erläutert:

|Schritt|Zweck|
|--|--|
|1|Die Clientanwendung sendet die Benutzerabfrage an die [GenerateAnswer-API](../how-to/metadata-generateanswer-usage.md).|
|2|QnA Maker führt die Vorverarbeitung der Benutzerabfrage mit Spracherkennung, Rechtschreibprüfung und Worttrennung durch.|
|3|Diese Vorverarbeitung hat das Ziel, die Benutzerabfrage so anzupassen, dass die besten Suchergebnisse erzielt werden.|
|4|Die geänderte Abfrage wird an den Azure Cognitive Search-Index gesendet, der die mit `top` festgelegte Anzahl von Ergebnissen empfängt. Falls diese Ergebnisse die richtige Antwort nicht enthalten, sollten Sie den Wert von `top` leicht erhöhen. Im Allgemeinen eignet sich der Wert „10“ für `top` bei 90 % aller Abfragen.|
|5|QnA Maker verwendet ein modernes transformatorbasiertes Modell, um die Ähnlichkeit zwischen der Benutzerabfrage und den aus Azure Cognitive Search abgerufenen QnA-Kandidatenergebnissen zu ermitteln. Das transformatorbasierte Modell ist ein mehrsprachiges Deep Learning-Modell, das für alle Sprachen horizontal arbeitet, um die Konfidenzscores sowie die neue Rangfolge zu bestimmen.|
|6|Die neuen Ergebnisse werden in der Rangfolge an die Clientanwendung zurückgegeben.|
|||

Der Bewerter bearbeitet alle alternativen Fragen und Antworten, um die am besten übereinstimmenden QnA-Paare für die Benutzerabfrage zu finden. Benutzer haben die Flexibilität, den Bewerter als reinen Fragenbewerter zu konfigurieren. 

---

## <a name="http-request-and-response-with-endpoint"></a>HTTP-Anforderung und -Antwort mit Endpunkt
Beim Veröffentlichen Ihrer Wissensdatenbank erstellt der Dienst einen REST-basierten HTTP-Endpunkt, den Sie in Ihre Anwendung integrieren können, etwa in Form eines Chatbots.

### <a name="the-user-query-request-to-generate-an-answer"></a>Benutzerabfrageanforderung zum Generieren einer Antwort

Eine Benutzerabfrage ist die Frage, die der Benutzer an die Wissensdatenbank richtet, z. B. `How do I add a collaborator to my app?`. Die Abfrage wird häufig in einem natürlichen Sprachformat oder in Form von Schlüsselwörtern formuliert, die die Frage darstellen, z. B. `help with collaborators`. Die Abfrage wird von einer HTTP-Anforderung in der Clientanwendung an Ihre Wissensdatenbank gesendet.

```json
{
    "question": "How do I add a collaborator to my app?",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "QuestionType",
        "value": "Support"
    }],
    "userId": "sd53lsY="
}
```

Sie steuern die Antwort, indem Sie Eigenschaften wie [scoreThreshold](./confidence-score.md#choose-a-score-threshold), [top](../how-to/improve-knowledge-base.md#use-the-top-property-in-the-generateanswer-request-to-get-several-matching-answers) und [strictFilters](../how-to/metadata-generateanswer-usage.md#filter-results-with-strictfilters-for-metadata-tags) festlegen.

Verwenden Sie [Unterhaltungskontext](../how-to/metadata-generateanswer-usage.md#use-question-and-answer-results-to-keep-conversation-context) mit [Mehrfachdurchlauffunktion](../how-to/multiturn-conversation.md), um die Konversation aufrechtzuerhalten und die Fragen und Antworten so zu verfeinern, dass die richtige und abschließende Antwort gefunden wird.

### <a name="the-response-from-a-call-to-generate-an-answer"></a>Reaktion auf einen Aufruf zum Generieren einer Antwort

Die HTTP-Antwort ist die Antwort, die aus der Wissensdatenbank basierend auf der besten Übereinstimmung für eine bestimmte Benutzerabfrage abgerufen wird. Darin enthalten ist die eigentliche Antwort und das Vorhersageergebnis. Wenn Sie mit der Eigenschaft `top` mehr als eine Top-Antwort angefordert haben, erhalten Sie mehrere Top-Antworten jeweils mit einer Bewertung.

```json
{
    "answers": [
        {
            "questions": [
                "How do I add a collaborator to my app?",
                "What access control is provided for the app?",
                "How do I find user management and security?"
            ],
            "answer": "Use the Azure portal to add a collaborator using Access Control (IAM)",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "QuestionType",
                    "value": "Support"
                },
                {
                    "name": "ToolDependency",
                    "value": "Azure Portal"
                }
            ]
        }
    ]
}
```


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Zuverlässigkeitsbewertung](./confidence-score.md)