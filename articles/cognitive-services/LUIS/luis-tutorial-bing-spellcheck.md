---
title: Korrigieren falsch geschriebener Wörter
titleSuffix: Azure
description: Korrigieren Sie falsch geschriebene Wörter in Äußerungen, indem Sie LUIS-Endpunktabfragen die Bing-Rechtschreibprüfungs-API V7 hinzufügen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 1e5536b08b3b78f35426207369f444e6eb21c87d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494999"
---
# <a name="correct-misspelled-words-with-bing-spell-check"></a>Korrigieren falsch geschriebener Wörter mit der Bing-Rechtschreibprüfung

Sie können die [Bing-Rechtschreibprüfungs-API V7](https://azure.microsoft.com/services/cognitive-services/spell-check/) in Ihrer LUIS-App integrieren, um falsch geschriebene Wörter in Äußerungen zu korrigieren, bevor LUIS diese bewertet und die Entitäten der Äußerung vorhersagt. 

## <a name="create-first-key-for-bing-spell-check-v7"></a>Erstellen des ersten Schlüssels für die Bing-Rechtschreibprüfungs-API V7
Ihr [erster Schlüssel für die Bing-Rechtschreibprüfungs-API v7](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) ist kostenlos. 

![Erstellen eines kostenlosen Schlüssels](./media/luis-tutorial-bing-spellcheck/free-key.png)

<a name"create-subscription-key"></a>
## <a name="create-endpoint-key"></a>Erstellen eines Endpunktschlüssels
Wenn Ihr kostenloser Schlüssel abgelaufen ist, erstellen Sie einen Endpunktschlüssel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an. 

2. Wählen Sie in der linken oberen Ecke **Ressource erstellen** aus.

3. Geben Sie im Suchfeld `Bing Spell Check API V7`ein.

    ![Suchen der Bing-Rechtschreibprüfungs-API V7](./media/luis-tutorial-bing-spellcheck/portal-search.png)

4. Wählen Sie den Dienst aus. 

5. Auf der rechten Seite wird ein Informationsbereich angezeigt, der Informationen sowie die rechtlichen Hinweise enthält. Wählen Sie **Erstellen** aus, um mit der Abonnementerstellung zu beginnen. 

6. Geben Sie im nächsten Bereich Ihre Diensteinstellungen an. Warten Sie, bis die Diensterstellung abgeschlossen wurde.

    ![Eingeben von Diensteinstellungen](./media/luis-tutorial-bing-spellcheck/subscription-settings.png)

7. Wählen Sie auf der linken Navigationsleiste **Alle Ressourcen** unter der Überschrift **Favoriten** aus.

8. Wählen Sie den neuen Dienst aus. Der Typ ist **Cognitive Services**, und der Standort ist **global**. 

9. Wählen Sie im Hauptbereich **Schlüssel** aus, um Ihre neuen Schlüssel anzuzeigen.

    ![Abrufen von Schlüsseln](./media/luis-tutorial-bing-spellcheck/grab-keys.png)

10. Kopieren Sie den ersten Schlüssel. Sie benötigen nur einen der beiden Schlüssel. 

## <a name="using-the-key-in-luis-test-panel"></a>Verwenden des Schlüssels im LUIS-Testbereich
Es gibt zwei Bereiche in LUIS, in denen Sie den Schlüssel verwenden. Die erste ist der [Testbereich](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). Der Schlüssel wird nicht in LUIS gespeichert, sondern ist stattdessen eine Sitzungsvariable. Sie müssen den Schlüssel jedes Mal festlegen, wenn Sie im Testbereich den Dienst Bing-Rechtschreibprüfungs-API V7 auf eine Äußerung anwenden möchten. [Anweisungen](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) zum Festlegen des Schlüssels finden Sie im Testbereich.

## <a name="adding-the-key-to-the-endpoint-url"></a>Hinzufügen des Schlüssels zur Endpunkt-URL
Sie müssen der Endpunktabfrage den Schlüssel in den Parametern der Abfragezeichenfolgen für jede Abfrage übergeben, auf die Sie die Rechtschreibprüfung anwenden möchten. Ihr Chatbot könnte beispielsweise LUIS aufrufen, oder Sie rufen die LUIS-Endpunkt-API direkt auf. Unabhängig davon, wie der Endpunkt aufgerufen wird, muss jeder Aufruf die erforderlichen Informationen enthalten, damit die Rechtschreibkorrektur ordnungsgemäßen funktioniert.

Die Endpunkt-URL enthält mehrere Werte, die ordnungsgemäß übergeben werden müssen. Der Schlüssel für die Bing-Rechtschreibprüfungs-API v7 ist nur einer davon. Sie müssen den **spellCheck**-Parameter auf TRUE und den Wert von **bing-spell-check-subscription-key** auf den Schlüsselwert festlegen:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appID}?subscription-key={luisKey}&spellCheck=**true**&bing-spell-check-subscription-key=**{bingKey}**&verbose=true&timezoneOffset=0&q={utterance}`

## <a name="send-misspelled-utterance-to-luis"></a>Übermitteln falsch geschriebener Äußerungen an LUIS
1. Kopieren Sie in einem Webbrowser die vorhergehende Zeichenfolge, und ersetzen Sie `region`, `appId`, `luisKey` und `bingKey` durch Ihre eigenen Werte. Sie müssen unbedingt die Endpunktregion verwenden, wenn diese sich von der [Veröffentlichungsregion](luis-reference-regions.md) unterscheidet.

2. Fügen Sie eine falsch geschriebene Äußerung wie z.B. „How far is the mountainn?“ hinzu. Im Englischen ist `mountain` mit einem `n` die richtige Schreibweise. 

3. Drücken Sie die EINGABETASTE, um die Abfrage an LUIS zu senden.

4. LUIS antwortet mit einem JSON-Ergebnis für `How far is the mountain?`. Wenn die Bing-Rechtschreibprüfungs-API v7 einen Rechtschreibfehler erkennt, enthält das Feld `query` in der JSON-Antwort der LUIS-App die ursprüngliche Abfrage und das Feld `alteredQuery` die korrigierte, an LUIS gesendete Abfrage.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Ignorieren von Rechtschreibfehlern
Wenn Sie den Dienst Bing-Rechtschreibprüfungs-API V7 nicht verwenden möchten, können Sie Äußerungen mit Rechtschreibfehlern kennzeichnen, damit LUIS die richtige Schreibweise sowie Tippfehler erlernen kann. Diese Option erfordert einen höheren Aufwand für die Bezeichnung als die Verwendung einer Rechtschreibprüfung.

## <a name="publishing-page"></a>Seite „Veröffentlichen“
Die Seite [Veröffentlichen](luis-how-to-publish-app.md) enthält das Kontrollkästchen **Enable Bing spell checker** (Bing-Rechtschreibprüfung aktivieren). Dieses vereinfacht das Erstellen des Schlüssels und hilft zu verstehen, wie sich die Endpunkt-URL ändert. Sie müssen trotzdem die richtigen Endpunktparameter verwenden, damit die Rechtschreibung für jede Äußerung korrigiert wird. 

> [!div class="nextstepaction"]
> [Weitere Informationen zu Beispieläußerungen](luis-how-to-add-example-utterances.md)
