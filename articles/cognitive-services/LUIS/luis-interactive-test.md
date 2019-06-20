---
title: Testen von Apps im LUIS-Portal
titleSuffix: Language Understanding - Azure Cognitive Services
description: Verwenden Sie LUIS (Language Understanding Intelligent Service), um Ihre Anwendung fortlaufend zu optimieren und ihr Sprachverständnis zu verbessern.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 51c6a58567b35c9b8486d8634b0bed1af7218994
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60199070"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testen Ihrer LUIS-App im LUIS-Portal
Das <a name="train-your-app"></a>
[Testen](luis-concept-test.md) einer App ist ein iterativer Vorgang. Testen Sie Ihre LUIS-App nach dem Trainieren mit Beispieläußerungen, um festzustellen, ob die Absichten und Entitäten ordnungsgemäß erkannt werden. Ist das nicht der Fall, aktualisieren Sie die LUIS-App, und trainieren und testen Sie sie erneut. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Testen einer Äußerung

1. Greifen Sie auf Ihre App zu, indem Sie ihren Namen auf der Seite **Meine Apps** auswählen. 

2. Um auf den Einblendbereich **Test** zuzugreifen, wählen Sie im oberen Bereich Ihrer Anwendung **Test** aus.

    ![Seite „Train & Test App“ (App trainieren und testen)](./media/luis-how-to-interactive-test/test.png)

3. Geben Sie eine Äußerung in das Textfeld ein, und drücken Sie die EINGABETASTE. Sie können im **Test** beliebig viele Äußerungen eingeben, aber immer nur einzeln.

4. Die Äußerung, die am höchsten bewertete Absicht und ihre Bewertung werden der Liste der Äußerungen unter dem Textfeld hinzugefügt.

    ![Erkennen einer falschen Absicht in interaktiven Tests](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Überprüfen der Bewertung

Sie überprüfen die Details des Testergebnisses im Bereich **Überprüfen**. 
 
1. Wählen Sie bei geöffnetem Einblendbereich **Test** die Option **Überprüfen** für eine Äußerung aus, die verglichen werden soll. 

    ![Auswählen der Schaltfläche „Überprüfen“, um weitere Details zu den Testergebnissen anzuzeigen](./media/luis-how-to-interactive-test/inspect.png)

2. Der Bereich **Überprüfung** wird angezeigt. Dieser Bereich enthält die Absicht mit der höchsten Bewertung sowie alle identifizierten Entitäten. Das Ergebnis der ausgewählten Äußerung wird im Bereich angezeigt.

    ![Dieser Bereich enthält die Absicht mit der höchsten Bewertung sowie alle identifizierten Entitäten. Das Ergebnis der ausgewählten Äußerung wird im Bereich angezeigt.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Korrigieren der Absicht mit der höchsten Bewertung

1. Wenn die Absicht mit der höchsten Bewertung falsch ist, wählen Sie die Schaltfläche **Bearbeiten** aus.

2.  Wählen Sie in der Dropdownliste die richtige Absicht für die Äußerung aus.

    ![Auswählen der richtigen Absicht](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Anzeigen von Standpunktergebnissen

Wenn die **Standpunktanalyse** auf der Seite **[Publish](luis-how-to-publish-app.md#enable-sentiment-analysis)** (Veröffentlichen) konfiguriert wird, schließen die Testergebnisse den in der Äußerung gefundenen Standpunkt ein. 

![Abbildung des Testbereichs mit Standpunktanalyse](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Korrigieren der Absicht des übereinstimmenden Musters

Wenn Sie [Muster](luis-concept-patterns.md) verwenden und die Äußerung mit einem Muster übereinstimmte, jedoch die falsche Absicht vorhergesagt wurde, wählen Sie den Link **Bearbeiten** für das Muster und dann die richtige Absicht aus.

## <a name="compare-with-published-version"></a>Vergleichen mit der veröffentlichten Version

Sie können die aktive Version Ihrer App mit der veröffentlichten [Endpunktversion](luis-glossary.md#endpoint) testen. Wählen Sie im Bereich **Überprüfen** die Option **Compare with published** (Mit veröffentlichtem Element vergleichen) aus. Tests mit dem veröffentlichten Modell werden mit dem Kontingent Ihres Azure-Abonnements verrechnet. 

![Compare with published (Mit veröffentlichtem Element vergleichen)](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Anzeigen des Endpunkt-JSON-Codes im Testbereich
Sie können den für den Vergleich zurückgegebenen JSON-Code des Endpunkts anzeigen, indem Sie die Ansicht **JSON anzeigen** auswählen.

![Veröffentlichte JSON-Antwort](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Zusätzliche Einstellungen im Testbereich

### <a name="luis-endpoint"></a>LUIS-Endpunkt

Wenn Sie mehrere LUIS-Endpunkte haben, können Sie den für Tests verwendeten Endpunkt über den Link **Zusätzliche Einstellungen** im Bereich „Veröffentlicht“ des Tests ändern. Wenn Sie nicht sicher sind, welchen Endpunkt Sie verwenden sollten, wählen Sie den Standard **Starter_Key** aus. 

![Testbereich mit hervorgehobenem Link „Zusätzliche Einstellungen“](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Anzeigen von Korrekturen der Bing-Rechtschreibprüfung im Testbereich

Anforderungen zum Anzeigen der Rechtschreibkorrekturen: 

* Veröffentlichte App
* [Dienstschlüssel](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) für die Bing-Rechtschreibprüfung. Der Dienstschlüssel wird nicht gespeichert und muss für jede Browsersitzung zurückgesetzt werden. 

Verwenden Sie das folgende Verfahren, um den Dienst [Bing-Rechtschreibprüfung v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) in die Ergebnisse im Testbereich einzuschließen. 

1. Geben Sie im Bereich **Test** eine Äußerung ein. Wenn die Äußerung vorhergesagt wird, wählen Sie unter der eingegebenen Äußerung **[Überprüfen](#inspect-score)** aus. 

2. Wenn der Bereich **Überprüfen** geöffnet wird, wählen Sie **[Compare with Published](#compare-with-published-version)** (Mit veröffentlichtem Element vergleichen) aus. 

3. Wenn der Bereich **Veröffentlicht** geöffnet wird, wählen Sie **[Zusätzliche Einstellungen](#additional-settings-in-test-panel)** aus.

4. Geben Sie im Popupdialogfeld Ihren Dienstschlüssel für die **Bing-Rechtschreibprüfung** ein. 
    ![Eingeben des Dienstschlüssels für die Bing-Rechtschreibprüfung](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)

5. Geben Sie eine Abfrage in falscher Schreibweise ein, z.B. `book flite to seattle`, und drücken Sie die EINGABETASTE. Die falsche Schreibweise des Worts `flite` wird in der an LUIS gesendeten Abfrage ersetzt, und der resultierende JSON-Code enthält sowohl die ursprüngliche Abfrage als `query` als auch die korrigierte Schreibweise in der Abfrage als `alteredQuery`.

    ![JSON-Code mit korrigierter Rechtschreibung](./media/luis-how-to-interactive-test/interactive-with-spell-check-results.png)

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="batch-testing"></a>Testen in Batches
Weitere Informationen finden Sie in den [Konzepten](luis-concept-batch-test.md) zu Batchtests und im Artikel zum [Testen eines Batches von Äußerungen](luis-how-to-batch-test.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn sich in Tests herausstellt, dass Ihre LUIS-App nicht die richtigen Absichten und Entitäten erkennt, können Sie die Genauigkeit Ihrer LUIS-App verbessern, indem Sie aktiv weitere Äußerungen bezeichnen oder Features hinzufügen. 

* [Bezeichnen von vorgeschlagenen Äußerungen mit LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Verwenden von Features zum Verbessern der Leistung Ihrer LUIS-App](luis-how-to-add-features.md) 
