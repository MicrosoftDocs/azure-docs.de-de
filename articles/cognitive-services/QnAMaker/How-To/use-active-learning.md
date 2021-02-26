---
title: 'Verwenden des aktiven Lernens mit Wissensdatenbank: QnA Maker'
description: Erfahren Sie, wie Sie durch aktives Lernen die Qualität Ihrer Wissensdatenbank verbessern können. Sie können Einträge überprüfen, annehmen oder ablehnen und hinzufügen, ohne vorhandene Fragen zu entfernen oder zu ändern.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: ffc1a0a401de634c1932b9653f231b377c4f154e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591921"
---
# <a name="active-learning"></a>Aktives Lernen

Durch _Vorschläge für aktives Lernen_ lässt sich die Qualität Ihrer Wissensdatenbank verbessern, indem auf der Grundlage von Benutzerbeiträgen alternative Fragen zu Ihrem Frage-Antwort-Paar vorgeschlagen werden. Sie überprüfen diese Vorschläge, indem Sie sie entweder zu vorhandenen Fragen hinzufügen oder ablehnen.

Ihre Wissensdatenbank wird nicht automatisch geändert. Damit Änderungen wirksam werden, müssen Sie die Vorschläge akzeptieren. Mit diesen Vorschlägen werden Fragen hinzugefügt, vorhandene Fragen werden aber weder geändert noch entfernt.

## <a name="what-is-active-learning"></a>Was ist aktives Lernen?

QnA Maker erlernt neue Fragevarianten über implizites und explizites Feedback.

* [Implizites Feedback:](#how-qna-makers-implicit-feedback-works) Die Bewertung erkennt, wenn für eine Benutzerfrage mehrere Antworten vorliegen, deren Bewertungen sehr nah beieinander liegen, und betrachtet dies als Feedback. Hierzu müssen keinerlei Aktionen ausgeführt werden.
* [Explizites Feedback:](#how-you-give-explicit-feedback-with-the-train-api) Wenn von der Wissensdatenbank mehrere Antworten zurückgegeben werden, deren Bewertungen sich kaum unterscheiden, fordert die Clientanwendung den Benutzer auf, die richtige Frage auszuwählen. Das explizite Feedback des Benutzers wird mit der [Trainings-API](../How-To/improve-knowledge-base.md#train-api) an QnA Maker gesendet.

Mit beiden Methoden werden ähnliche (gruppierte) Abfragen für die Bewertung bereitgestellt.

## <a name="how-active-learning-works"></a>Aktives Lernen: Funktionsweise

Aktives Lernen wird basierend auf den Bewertungen der relevantesten Antworten ausgelöst, die von QnA Maker zurückgegeben werden. Liegt der Scoreunterschied zwischen Frage-Antwort-Paaren, die der Abfrage entsprechen, innerhalb eines geringen Bereichs, wird die Abfrage als möglicher Vorschlag (alternative Frage) für jedes der möglichen Frage-Antwort-Paare betrachtet. Nachdem Sie die vorgeschlagene Frage für ein bestimmtes QnA-Paar akzeptiert haben, wird sie für die anderen Paare abgelehnt. Denken Sie daran, nach dem Akzeptieren von Vorschlägen zu speichern und ein Training auszuführen.

Das Feature für aktives Lernen liefert die bestmöglichen Vorschläge in Fällen, in denen die Endpunkte eine angemessene Anzahl und Vielfalt von Nutzungsabfragen erhalten. Wenn mindestens 5 ähnliche Abfragen gruppiert werden, sendet QnA Maker alle 30 Minuten die benutzerbasierten Fragen als Vorschläge an den Entwickler der Wissensdatenbank, um diese zu akzeptieren oder abzulehnen. Alle Vorschläge werden nach Ähnlichkeit gruppiert, und die besten Vorschläge für alternative Fragen werden basierend auf der Häufigkeit der jeweiligen Abfragen von Endbenutzern angezeigt.

Sobald Fragen im QnA Maker-Portal vorgeschlagen werden, müssen Sie diese überprüfen und akzeptieren oder ablehnen. Es gibt keine API zum Verwalten von Vorschlägen.

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

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Upgraden der Laufzeitversion, um aktives Lernen zu nutzen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Das Feature für aktives Lernen wird in der Runtimeversion 4.4.0 und höher unterstützt. Wenn Ihre Wissensdatenbank mit einer Vorgängerversion erstellt wurde, [führen Sie ein Upgrade Ihrer Runtime durch](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates), um dieses Feature zu nutzen.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Da bei QnA Maker verwaltet (Vorschau) die Runtime vom QnA Maker-Dienst selbst gehostet wird, ist es nicht erforderlich, ein Upgrade der Runtime manuell durchzuführen.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Aktivieren des aktiven Lernens für alternative Fragen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Das Feature für aktives Lernen ist standardmäßig deaktiviert. Aktivieren Sie das Feature, um die vorgeschlagenen Fragen anzuzeigen. Nachdem Sie aktives Lernen aktiviert haben, müssen Sie Informationen aus der Client-App an QnA Maker senden. Weitere Informationen finden Sie unter [Architekturfluss für die Verwendung der GenerateAnswer- und der Trainings-API über einen Bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wählen Sie **Veröffentlichen** aus, um die Wissensdatenbank zu veröffentlichen. Aktive Lernabfragen werden nur vom Vorhersageendpunkt der GenerateAnswer-API erfasst. Die Abfragen im Bereich „Test“ im QnA Maker-Portal besitzen keine Auswirkungen auf das aktive Lernen.

1. Wenn Sie aktives Lernen im QnA Maker-Portal aktivieren möchten, wählen Sie in der rechten oberen Ecke Ihren **Namen** aus, und navigieren Sie zu [**Diensteinstellungen**](https://www.qnamaker.ai/UserSettings).

    ![Aktivieren Sie auf der Seite „Diensteinstellungen“ Alternativen für die vom Feature für aktives Lernen vorgeschlagenen Fragen. Wählen Sie im Menü rechts oben Ihren Benutzernamen und anschließend „Diensteinstellungen“ aus.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Suchen Sie nach dem QnA Maker-Dienst, und aktivieren Sie die Option **Active Learning**.

    > [!div class="mx-imgBorder"]
    > [![Aktivieren Sie auf der Seite „Diensteinstellungen“ das Feature für aktives Lernen. Wenn Sie das Feature nicht aktivieren können, müssen Sie möglicherweise ein Upgrade des Diensts ausführen.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Die genaue Version auf der vorherigen Abbildung ist nur als Beispiel dargestellt. Ihre Version kann abweichen.

    Nachdem Sie **Aktives Lernen** aktiviert haben, schlägt die Wissensdatenbank basierend auf den von Benutzern übermittelten Fragen in regelmäßigen Abständen neue Fragen vor. Sie können **Active Learning** deaktivieren, indem Sie die Einstellung wieder umschalten.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Das Feature für aktives Lernen ist in QnA Maker verwaltet (Vorschau) standardmäßig **aktiviert**. Verwenden Sie zum Anzeigen der vorgeschlagenen alternativen Fragen die [Anzeigeoptionen](../How-To/improve-knowledge-base.md#view-suggested-questions) auf der Seite „Bearbeiten“.

---

## <a name="review-suggested-alternate-questions"></a>Überprüfen von Vorschlägen für alternative Fragen

Auf der Seite **Bearbeiten** jeder Wissensdatenbank können Sie die [Vorschläge für alternative Fragen überprüfen](improve-knowledge-base.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Wissensdatenbank](./manage-knowledge-bases.md)
