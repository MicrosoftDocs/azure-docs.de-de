---
title: 'Vorschläge für aktives Lernen: QnA Maker'
description: Durch Vorschläge für aktives Lernen lässt sich die Qualität Ihrer Wissensdatenbank verbessern, indem auf der Grundlage von Benutzerbeiträgen alternative Fragen zu Ihrem Frage-Antwort-Paar vorgeschlagen werden.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: e1a8043912c984be46f85bd384a7049da27028b3
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353237"
---
# <a name="active-learning-suggestions"></a>Vorschläge für aktives Lernen

Durch _Vorschläge für aktives Lernen_ lässt sich die Qualität Ihrer Wissensdatenbank verbessern, indem auf der Grundlage von Benutzerbeiträgen alternative Fragen zu Ihrem Frage-Antwort-Paar vorgeschlagen werden. Sie überprüfen diese Vorschläge, indem Sie sie entweder zu vorhandenen Fragen hinzufügen oder ablehnen.

Ihre Wissensdatenbank wird nicht automatisch geändert. Damit Änderungen wirksam werden, müssen Sie die Vorschläge akzeptieren. Mit diesen Vorschlägen werden Fragen hinzugefügt, vorhandene Fragen werden aber weder geändert noch entfernt.

## <a name="what-is-active-learning"></a>Was ist aktives Lernen?

QnA Maker erlernt neue Fragevarianten über implizites und explizites Feedback.

* [Implizites Feedback:](#how-qna-makers-implicit-feedback-works) Die Bewertung erkennt, wenn für eine Benutzerfrage mehrere Antworten vorliegen, deren Bewertungen sehr nah beieinander liegen, und betrachtet dies als Feedback. Hierzu müssen keinerlei Aktionen ausgeführt werden.
* [Explizites Feedback:](#how-you-give-explicit-feedback-with-the-train-api) Wenn von der Wissensdatenbank mehrere Antworten zurückgegeben werden, deren Bewertungen sich kaum unterscheiden, fordert die Clientanwendung den Benutzer auf, die richtige Frage auszuwählen. Das explizite Feedback des Benutzers wird mit der [Trainings-API](../How-to/improve-knowledge-base.md#train-api) an QnA Maker gesendet.

Mit beiden Methoden werden ähnliche (gruppierte) Abfragen für die Bewertung bereitgestellt.

## <a name="how-active-learning-works"></a>Aktives Lernen: Funktionsweise

Aktives Lernen wird basierend auf den Bewertungen der relevantesten Antworten ausgelöst, die von QnA Maker zurückgegeben werden. Liegt der Scoreunterschied zwischen Frage-Antwort-Paaren, die der Abfrage entsprechen, innerhalb eines geringen Bereichs, wird die Abfrage als möglicher Vorschlag (alternative Frage) für jedes der möglichen Frage-Antwort-Paare betrachtet. Nachdem Sie die vorgeschlagene Frage für ein bestimmtes QnA-Paar akzeptiert haben, wird sie für die anderen Paare abgelehnt. Denken Sie daran, nach dem Akzeptieren von Vorschlägen zu speichern und ein Training auszuführen.

Das Feature für aktives Lernen liefert die bestmöglichen Vorschläge in Fällen, in denen die Endpunkte eine angemessene Anzahl und Vielfalt von Nutzungsabfragen erhalten. Wenn mindestens 5 ähnliche Abfragen gruppiert werden, sendet QnA Maker alle 30 Minuten die benutzerbasierten Fragen als Vorschläge an den Entwickler der Wissensdatenbank, um diese zu akzeptieren oder abzulehnen. Alle Vorschläge werden nach Ähnlichkeit gruppiert, und die besten Vorschläge für alternative Fragen werden basierend auf der Häufigkeit der jeweiligen Abfragen von Endbenutzern angezeigt.

Sobald Fragen im QnA Maker-Portal vorgeschlagen werden, müssen Sie diese überprüfen und akzeptieren oder ablehnen. Es gibt keine API zum Verwalten von Vorschlägen.

## <a name="turn-on-active-learning"></a>Aktivieren des Features für aktives Lernen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Das Feature für aktives Lernen ist standardmäßig **deaktiviert**.
So verwenden Sie aktives Lernen:
* Sie müssen [aktives Lernen aktivieren](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions), damit QnA Maker alternative Fragen für Ihre Wissensdatenbank sammelt.
* Verwenden Sie zum Anzeigen der vorgeschlagenen alternativen Fragen die [Anzeigeoptionen](../How-To/improve-knowledge-base.md#view-suggested-questions) auf der Seite „Bearbeiten“.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Das Feature für aktives Lernen ist in QnA Maker verwaltet (Vorschau) standardmäßig **aktiviert**. Verwenden Sie zum Anzeigen der vorgeschlagenen alternativen Fragen die [Anzeigeoptionen](../How-To/improve-knowledge-base.md#view-suggested-questions) auf der Seite „Bearbeiten“.

---

## <a name="how-qna-makers-implicit-feedback-works"></a>Funktionsweise des impliziten Feedbacks von QnA Maker

Beim impliziten Feedback von QnA Maker wird ein Algorithmus verwendet, um die Nähe von Scores zu ermitteln und anschließend Vorschläge für aktives Lernen bereitzustellen. Der Algorithmus zur Bestimmung der Nähe ist keine einfache Berechnung. Die Bereiche im folgenden Beispiel sind keine statischen Werte, sondern dienen lediglich als Orientierungshilfe, um die Auswirkungen des Algorithmus nachvollziehen zu können.

Wenn die Bewertung einer Frage eine hohe Zuverlässigkeit aufweist, wie z.B. 80%, ist die Bandbreite der für das aktive Lernen in Betracht gezogenen Bewertungen mit etwa 10% recht groß. Wenn der Zuverlässigkeitswert einer Bewertung sinkt, z.B. auf 40%, nimmt auch die Bandbreite der Bewertungen ab und liegt nur etwa innerhalb von 4%.

In der folgenden JSON-Antwort auf eine Abfrage von generateAnswer von QnA Maker sind die Scores für A, B und C sehr ähnlich und werden daher als Vorschläge angesehen.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker weiß nicht, welches die beste Antwort ist. Verwenden Sie die Liste der Vorschläge im QnA Maker-Portal, um die beste Antwort auszuwählen und das Training zu wiederholen.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Abgeben von explizitem Feedback mit der Trainings-API

QnA Maker benötigt explizites Feedback dazu, welche der Antworten die beste Antwort war. Wie die beste Antwort bestimmt wird, liegt bei Ihnen. Mögliche Optionen sind beispielsweise:

* Benutzerfeedback (Auswählen einer der Antworten)
* Geschäftslogik (etwa Bestimmung eines akzeptablen Bewertungsbereichs)
* Kombination aus Benutzerfeedback und Geschäftslogik

Verwenden Sie die [Trainings-API](/rest/api/cognitiveservices/qnamaker4.0/runtime/train), um die richtige Antwort an QnA Maker zu senden, nachdem sie vom Benutzer ausgewählt wurde.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Abfragen der Wissensdatenbank](query-knowledge-base.md)