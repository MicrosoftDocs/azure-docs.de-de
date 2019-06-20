---
title: Language Understanding-Dashboard
titleSuffix: Azure Cognitive Services
description: Verbessern Sie Absichten mit dem Analysedashboard, einem grafischen Berichterstellungstool.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 055d113a2bc77f8de1b4b881718007c869470532
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236942"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Verwenden des Dashboards zum Verbessern Ihrer App

Finden und beheben Sie Probleme mit Absichten Ihrer trainierten App beim Verwenden von Beispieläußerungen. Auf dem Dashboard werden allgemeine App-Informationen angezeigt und die wichtigsten Absichten aufgeführt, die verbessert werden sollten. 

Das Überprüfen von Dashboardanalysen ist ein iterativer Prozess, der immer dann erforderlich ist, wenn Sie Ihr Modell ändern und verbessern.

Die auf dieser Seite beschriebenen Analysen sind nicht für Apps relevant, deren Absichten keine Beispieläußerungen enthalten. Diese Apps werden auch als _ausschließlich musterbasiert_ bezeichnet. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Welche Probleme lassen sich über das Dashboard beheben?

Mit dem Dashboard können Sie die folgenden drei Probleme behandeln:

|Problem|Diagrammfarbe|Erklärung|
|--|--|--|
|Unausgeglichene Daten|-|Dieses Problem tritt auf, wenn die Menge der Beispieläußerungen signifikant variiert. Alle Absichten mit Ausnahme von „None“ (Keine) müssen _ungefähr_ die gleiche Anzahl von Beispieläußerungen aufweisen. Nur 10 bis 15 % der Gesamtzahl der Äußerungen sollten in der App verwendet werden.<br><br> Wenn die Daten unausgeglichen sind, die Genauigkeit der Absicht aber über einem bestimmten Schwellenwert liegt, wird eine solche Unausgeglichenheit nicht als Problem gemeldet.<br><br>**Behandeln Sie zuerst dieses Problem, da es möglicherweise die Grundursache für andere Probleme ist.**|
|Unklare Vorhersagen|Orange|Dieses Problem tritt auf, wenn sich die Absicht mit der höchsten Bewertung und die darauffolgende Absicht in ihrem Wert nur minimal unterscheiden, sodass sich die Rangfolge beim nächsten Training ändert. Dies kann auf eine [negative Stichprobenentnahme](luis-how-to-train.md#train-with-all-data) (Negative Sampling) oder auf weitere Beispieläußerungen zurückzuführen sein, die der Absicht hinzugefügt wurden. |
|Falsche Vorhersagen|Rot|Dieses Problem tritt auf, wenn eine Beispieläußerung nicht für die Absicht mit der Bezeichnung (also für die Absicht, in der die Äußerung enthalten ist) vorhergesagt wird.|

Richtige Vorhersagen werden blau dargestellt.

Auf dem Dashboard werden diese Probleme aufgeführt. Dort wird auch angezeigt, welche Absichten betroffen sind, und es werden Vorschläge zur Verbesserung der App gemacht. 

## <a name="before-app-is-trained"></a>Status vor dem Trainieren der App 

Das Dashboard enthält vor dem Trainieren der App keine Verbesserungsvorschläge. Diese werden erst nach dem Training angezeigt.  

## <a name="check-your-publishing-status"></a>Überprüfen des Veröffentlichungsstatus

Die Karte **Publishing status** (Veröffentlichungsstatus) enthält Informationen zur letzten Veröffentlichung der aktiven Version. 

Überprüfen Sie, ob die aktive Version diejenige ist, die Sie verbessern möchten. 

![Auf dem Dashboard werden die externen Dienste und veröffentlichten Regionen sowie die aggregierte Endpunkttrefferanzahl der App angezeigt.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Hier werden außerdem die externen Dienste und veröffentlichten Regionen sowie die aggregierte Endpunkttrefferanzahl angezeigt. 

## <a name="review-training-evaluation"></a>Überprüfen der Trainingsauswertung

Die Karte **Training evaluation** (Trainingsauswertung) enthält die aggregierte Zusammenfassung für die Gesamtgenauigkeit Ihrer App und schlüsselt diese nach Bereichen auf. Die Bewertung gibt Aufschluss über die Qualität der Absicht. 

![Die Karte „Training evaluation“ (Trainingsauswertung) enthält den ersten Informationsbereich für die Gesamtgenauigkeit Ihrer App.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Im Diagramm werden die richtig vorhergesagten Absichten und die Problembereiche mit unterschiedlichen Farben dargestellt. Wenn Sie die App mithilfe der Vorschläge verbessern, steigt die Bewertung. 

Die wichtigsten Verbesserungsvorschläge für Ihre App sind getrennt nach Problemtyp aufgeführt. Wenn Sie stattdessen Probleme mit einzelnen Absichten behandeln möchten, können Sie dazu die Karte **[Intents with errors](#intents-with-errors)** (Fehlerhafte Absichten) unten auf der Seite verwenden. 

Jeder Problembereich enthält Absichten, die verbessert werden müssen. Wenn Sie den Namen der Absicht auswählen, wird die Seite **Intent** (Absicht) mit einem Filter für die Äußerungen geöffnet. Mithilfe dieses Filters können Sie sich auf die Absichten konzentrieren, die das Problem verursachen.

### <a name="compare-changes-across-versions"></a>Vergleichen von Änderungen zwischen Versionen

Erstellen Sie eine neue Version, bevor Sie Änderungen an der App vornehmen. Nehmen Sie in der neuen Version die vorgeschlagenen Änderungen an den Beispieläußerungen der Absicht vor, und trainieren Sie die App dann erneut. Verwenden Sie auf der Karte **Training evaluation** (Trainingsauswertung) der Dashboardseite die Option **Show change from trained version** (Änderungen gegenüber Trainingsversion anzeigen), um die Änderungen zu vergleichen. 

![Vergleichen von Änderungen zwischen Versionen](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Verbessern einer Version durch Hinzufügen oder Bearbeiten von Beispieläußerungen und durch erneutes Trainieren

Sie verbessern Ihre App hauptsächlich dadurch, dass Sie Beispieläußerungen hinzufügen oder bearbeiten und anschließend die App erneut trainieren. Die neuen oder geänderten Äußerungen müssen den Richtlinien für [verschiedenartige Äußerungen](luis-concept-utterance.md) entsprechen.

Beispieläußerungen sollten von einer Person hinzugefügt werden, die folgende Voraussetzungen erfüllt:

* Sie muss genau verstehen, welche Äußerungen in den unterschiedlichen Absichten verwendet werden.
* Sie muss wissen, unter welchen Umständen Äußerungen in einer Absicht fälschlicherweise einer anderen Absicht zugeordnet werden.
* Sie kann entscheiden, ob zwei Absichten, die häufig miteinander verwechselt werden, in einer einzelnen zusammengeführt werden sollten, und kann festlegen, welche unterschiedlichen Daten mit Entitäten extrahiert werden.

### <a name="patterns-and-phrase-lists"></a>Muster und Begriffslisten

Auf der Analyseseite wird nicht angegeben, wann [Muster](luis-concept-patterns.md) oder [Begriffslisten](luis-concept-feature.md) verwendet werden sollten. Wenn Sie diese hinzufügen, können zwar falsche oder unklare Vorhersagen korrigiert werden, doch das Problem unausgeglichener Daten wird auf diese Weise nicht behoben. 

### <a name="review-data-imbalance"></a>Überprüfen unausgeglichener Daten

Behandeln Sie zuerst dieses Problem, da es möglicherweise die Grundursache für andere Probleme ist.

Die Liste **data imbalance** (Unausgeglichene Daten) enthält Absichten, für die weitere Äußerungen erforderlich sind, damit das Ungleichgewicht korrigiert werden kann. 

**So beheben Sie dieses Problem:**

* Fügen Sie der Absicht mehr Äußerungen hinzu, und führen Sie den Trainingsvorgang erneut aus. 

Falls nicht anders auf dem Dashboard angegeben, sollten Sie der Absicht „None“ (Keine) keine Äußerungen hinzufügen.

> [!Tip]
> Auf der Seite finden Sie im dritten Bereich **Utterances per intent** (Äußerungen pro Absicht) die Einstellung **Utterances (number)** (Äußerungen (Anzahl)). Hier erhalten Sie einen Überblick darüber, für welche Absichten weitere Äußerungen erforderlich sind.  
    ![Verwenden Sie „Utterances (number)“ (Äußerungen (Anzahl)), um Absichten zu finden, die unausgeglichene Daten enthalten.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Überprüfen falscher Vorhersagen

Die Liste **incorrect prediction** (Falsche Vorhersage) enthält Absichten mit Äußerungen, die zwar als Beispiele für eine bestimmte Absicht verwendet, aber für andere Absichten vorhergesagt werden. 

**So beheben Sie dieses Problem:**

* Bearbeiten Sie die Äußerungen so, dass diese besser auf die Absicht abgestimmt sind, und führen Sie den Trainingsvorgang anschließend erneut aus.
* Kombinieren Sie Absichten, wenn sich Äußerungen zu ähnlich sind, und führen Sie den Trainingsvorgang anschließend erneut aus.

### <a name="review-unclear-predictions"></a>Überprüfen von unklaren Vorhersagen

Die Liste **unclear prediction** (Unklare Vorhersage) enthält Absichten mit Äußerungen, deren Vorhersagebewertungen sich nicht ausreichend von denen der nachfolgenden Absichten unterscheiden. Die Absicht mit der höchsten Bewertung für die Äußerung kann sich daher beim nächsten Training aufgrund [negativer Stichprobenentnahmen](luis-how-to-train.md#train-with-all-data) ändern.

**So beheben Sie dieses Problem:**

* Bearbeiten Sie die Äußerungen so, dass diese besser auf die Absicht abgestimmt sind, und führen Sie den Trainingsvorgang anschließend erneut aus.
* Kombinieren Sie Absichten, wenn sich Äußerungen zu ähnlich sind, und führen Sie den Trainingsvorgang anschließend erneut aus.

## <a name="utterances-per-intent"></a>Utterances per intent (Äußerungen pro Absicht)

Auf dieser Karte wird der allgemeine Integritätszustand der App für die jeweiligen Absichten angezeigt. Behalten Sie diese Karte auch nach dem Verbessern von Absichten und dem erneuten Training weiterhin im Blick, um Probleme zu ermitteln.

Im folgenden Diagramm ist eine ausgeglichene App zu sehen, für die fast keine zu behebenden Probleme vorliegen.

![Im folgenden Diagramm ist eine ausgeglichene App zu sehen, für die fast keine zu behebenden Probleme vorliegen.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Im folgenden Diagramm ist eine unzureichend ausgeglichene App zu sehen, für die viele zu behebende Probleme vorliegen.

![Im folgenden Diagramm ist eine ausgeglichene App zu sehen, für die fast keine zu behebenden Probleme vorliegen.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Zeigen Sie auf eine Säule, um sich Informationen zur zugehörigen Absicht anzusehen. 

![Im folgenden Diagramm ist eine ausgeglichene App zu sehen, für die fast keine zu behebenden Probleme vorliegen.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Verwenden Sie das Feature **Sortieren nach**, um die Absichten nach Problemtyp anzuordnen. So können Sie sich auf die relevantesten Absichten mit genau diesem Problem konzentrieren. 

## <a name="intents-with-errors"></a>Fehlerhafte Absichten

Auf dieser Karte können Sie Probleme mit einer bestimmten Absicht überprüfen. In der Standardansicht werden die problematischsten Absichten aufgeführt, auf die Sie sich konzentrieren sollten.

![Auf der Karte mit fehlerhaften Absichten können Sie Probleme mit einer bestimmten Absicht überprüfen. Auf der Karte ist standardmäßig eine gefilterte Ansicht zu sehen, sodass nur die problematischsten Absichten angezeigt werden. Auf diese sollten Sie sich konzentrieren.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Im Ringdiagramm ganz oben sind die Probleme mit einer Absicht für drei Problemtypen aufgeführt. Wenn ein Problem eines bestimmten Typs auftritt, werden für diesen weiter unten ein Diagramm und sehr ähnliche Absichten angezeigt. 

### <a name="filter-intents-by-issue-and-percentage"></a>Filtern von Absichten nach Problem und Prozentwert

In diesem Abschnitt der Karte finden Sie Beispieläußerungen, deren Werte unter oder über dem Fehlerschwellenwert liegen. Im Idealfall sollten richtige Vorhersagen signifikant sein. Der Prozentsatz hierfür ergibt sich aus Geschäfts- und Kundeninteressen. 

Sie können die Schwellenwerte in Prozent festlegen, die für Ihr Unternehmen geeignet sind. 

Mit dem Filter können Sie Absichten mit einem bestimmten Problem suchen:

|Filter|Vorgeschlagener Prozentsatz|Zweck|
|--|--|--|
|Most problematic intents (Problematischste Absichten)|-|**Verbessern Sie zuerst die Äußerungen in dieser Absicht**. Dieser Vorgang hat auf die Verbesserung der App größere Auswirkungen als andere Fehlerbehebungen.|
|Correct predictions below (Falsche Vorhersagen unter)|60 %|Dies ist der Prozentsatz der Äußerungen in der ausgewählten Absicht, die zwar richtig sind, aber deren Konfidenzwert unter dem Schwellenwert liegt. |
|Unclear predictions above (Unklare Vorhersagen über)|15 %|Dies ist der Prozentsatz der Äußerungen in der ausgewählten Absicht, die fälschlicherweise der ähnlichsten Absicht zugeordnet werden.|
|Incorrect predictions above (Falsche Vorhersagen über)|15 %|Dies ist der Prozentsatz der Äußerungen in der ausgewählten Absicht, die falsch vorhergesagt werden. |

### <a name="correct-prediction-threshold"></a>Schwellenwert für richtige Vorhersagen

Sie selbst entscheiden, welcher Konfidenzwert für Vorhersagen akzeptabel sind. Zu Beginn der App-Entwicklung liegt dieser Wert möglicherweise bei 60 %. Verwenden Sie **Correct predictions below** (Falsche Vorhersagen unter) mit einem Prozentsatz von 60 %, um Äußerungen in der ausgewählten Absicht zu finden, die verbessert werden müssen.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Schwellenwert für unklare oder falsche Vorhersagen

Mit diesen beiden Filtern können Sie Äußerungen in der ausgewählten Absicht finden, deren Werte über dem Schwellenwert liegen. Diese beiden Prozentsätze können als Fehlerquoten betrachtet werden. Wenn eine Fehlerhäufigkeit von 10 bis 15 % für Vorhersagen akzeptabel ist, legen Sie den Schwellenwert des Filters auf 15 % fest, um alle Äußerungen über diesem Wert zu finden. 

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Azure-Ressourcen](luis-how-to-azure-subscription.md)
