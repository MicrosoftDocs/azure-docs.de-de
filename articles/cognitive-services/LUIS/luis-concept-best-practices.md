---
title: Bewährte Methoden
titleSuffix: Language Understanding - Azure Cognitive Services
description: Erfahren Sie, wie Sie mit bewährten Methoden bei LUIS die besten Ergebnisse für das Modell Ihrer LUIS-App erzielen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: diberry
ms.openlocfilehash: 9a6f9d54c52f36b8f709eacaf25d3fea31dbe516
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812913"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Bewährte Methoden zum Erstellen einer Sprachverständnis-App mit Cognitive Services
Verwenden Sie den App-Erstellungsprozess zur Erstellung der LUIS-App. 

* Erstellen des Sprachmodells
* Hinzufügen einiger Beispieläußerungen für das Training (10–15 pro Absicht)
* Veröffentlichen 
* Testen am Endpunkt 
* Hinzufügen von Features

Nachdem die App [veröffentlicht](luis-how-to-publish-app.md) wurde, können Sie über den Erstellungszyklus Features hinzufügen, die Veröffentlichung durchführen und Tests am Endpunkt durchführen. Beginnen Sie den nächsten Erstellungszyklus nicht durch das Hinzufügen weiterer Beispieläußerungen. Damit lernt LUIS Ihr Modell nicht durch reale Benutzeräußerungen kennen. 

Damit LUIS effizient lernen kann, erweitern Sie die Äußerungen erst, wenn der aktuelle Satz von Beispiel- und Endpunktäußerungen angemessene Zuverlässigkeits- und Vorhersagebewertungen aufweist. Verbessern Sie die Bewertungen durch [aktives Lernen](luis-concept-review-endpoint-utterances.md), [Muster](luis-concept-patterns.md) und [Ausdruckslisten](luis-concept-feature.md). 

## <a name="do-and-dont"></a>Empfehlungen
Die folgende Liste enthält bewährte Methoden für LUIS-Apps:

|Empfohlene Vorgehensweise|Nicht empfohlene Vorgehensweise|
|--|--|
|[Definieren unterschiedlicher Absichten](#do-define-distinct-intents) |[Hinzufügen vieler Beispieläußerungen zu Absichten](#dont-add-many-example-utterances-to-intents) |
|[Anstreben einer Balance zwischen zu allgemeinen und zu spezifischen Absichten](#do-find-sweet-spot-for-intents)|[Verwenden von LUIS als Trainingsplattform](#dont-use-luis-as-a-training-platform)|
|[Iteratives Erstellen der App](#do-build-the-app-iteratively)|[Hinzufügen sehr vieler Beispieläußerungen im gleichen Format und Ignorieren anderer Formate](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Hinzufügen von Ausdruckslisten und Mustern in späteren Iterationen](#do-add-phrase-lists-and-patterns-in-later-iterations)|[Kombinieren der Definition von Absichten und Entitäten](#dont-mix-the-definition-of-intents-and-entities)|
|[Verwenden Sie für alle Absichten die gleiche Menge an Äußerungen](#balance-your-utterances-across-all-intents), mit Ausnahme der Absicht „None“.<br>[Hinzufügen von Beispieläußerungen zur Absicht „None“](#do-add-example-utterances-to-none-intent)|[Erstellen von Ausdruckslisten mit allen möglichen Werten](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Verwenden des Vorschlagfeatures für das aktive Lernen](#do-leverage-the-suggest-feature-for-active-learning)|[Hinzufügen von zu vielen Mustern](#dont-add-many-patterns)|
|[Überwachen der Leistung Ihrer App](#do-monitor-the-performance-of-your-app)|[Trainieren und Veröffentlichen jeder einzelnen hinzugefügten Beispieläußerung](#dont-train-and-publish-with-every-single-example-utterance)|
|[Verwenden von Versionen für jede App-Iteration](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>Definieren Sie unterschiedliche Absichten
Stellen Sie sicher, dass sich das Vokabular der einzelnen Absichten nicht mit dem anderer Absichten überschneidet. Wenn Sie z.B. eine App planen, die Reisebuchungen wie Flüge oder Hotels verarbeiten soll, können Sie diese Themenbereiche als separate Absichten oder als dieselbe Absicht mit separaten Entitäten für bestimmte Daten innerhalb der Äußerung planen.

Wenn das Vokabular bei zwei Absichten gleich ist, fassen Sie die Absichten zusammen, und verwenden Sie Entitäten. 

Erwägen Sie die folgenden Beispieläußerungen:

|Beispiele für Äußerungen|
|--|
|Book a flight|
|Book a hotel|

„Book a flight“ und „Book a hotel“ verwenden das gleiche Vokabular „Book a “. Das Format ist das gleiche. Aus diesem Grund sollte nur eine Absicht verwendet werden. Extrahieren Sie die unterschiedlichen Wörter „einen Flug“ und „ein Hotelzimmer“ in Entitäten. 

Weitere Informationen finden Sie unter:
* Konzept: [Konzepte zu Absichten in Ihrer LUIS-App](luis-concept-intent.md)
* Tutorial: [Erstellen einer LUIS-App zum Bestimmen von Benutzerabsichten](luis-quickstart-intents-only.md)
* Gewusst wie: [Hinzufügen von Absichten, um die Absicht von Benutzeräußerungen zu bestimmen](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>Streben Sie eine Balance zwischen Absichten an
Verwenden Sie die Vorhersagedaten von LUIS, um zu bestimmen, ob die Absichten überlappen. Überlappende Absichten verwirren LUIS. Im Ergebnis ist die Absicht mit der höchsten Bewertung zu nah an einer anderen Absicht. Da LUIS nicht bei jedem Training genau denselben Pfad durch die Daten anwendet, kann eine überlappende Absicht beim Training den ersten oder zweiten Platz erreichen. Damit eine solche Überlappung nicht eintritt, sollten Sie das Ergebnis der Äußerung für jede Absicht weiter voneinander trennen. Bei einer guten Unterscheidung der Absichten sollte das Ergebnis jedes Mal die erwartete bestbewertete Absicht sein. 
 
## <a name="do-build-the-app-iteratively"></a>Erstellen Sie die App iterativ
Behalten Sie einen separaten Satz von Äußerungen bei, der nicht für [Beispieläußerungen](luis-concept-utterance.md) oder Endpunktäußerungen verwendet wird. Optimieren Sie die App immer weiter mit Ihrem Testsatz. Passen Sie den Testsatz an die tatsächlichen Benutzeräußerungen an. Verwenden Sie diesen Testsatz, um die einzelnen Iterationen oder Versionen der App zu bewerten. 

Entwickler sollten über drei Datensätze verfügen. Der erste enthält die Beispieläußerungen zum Erstellen des Modells. Der zweite dient zum Testen des Modells am Endpunkt. Der dritte enthält die blinden Testdaten für das [Testen in Batches](luis-how-to-batch-test.md). Der letzte Datensatz wird nicht zum Trainieren der Anwendung verwendet und auch nicht an den Endpunkt gesendet.  

Weitere Informationen finden Sie unter:
* Konzept: [Erstellungszyklus für Ihre LUIS-App](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>Fügen Sie Ausdruckslisten und Muster in späteren Iterationen hinzu

Eine bewährte Methode besteht darin, diese Methoden nicht anzuwenden, bevor Ihre App getestet wurde. Sie sollten verstehen, wie sich die App verhält, bevor Sie Phrasenlisten und -muster hinzufügen, da diese Funktionen stärker gewichtet werden als Beispieläußerungen und das Vertrauen verzerren. 

Nachdem Sie das Verhalten Ihrer App ohne diese kennen, fügen Sie Ihrer App nach Bedarf diese Funktionen hinzu. Sie müssen diese Funktionen nicht bei jedem [Durchlauf](luis-concept-app-iteration.md) hinzufügen oder bei jeder Version ändern. 

Es ist nicht schädlich, sie am Anfang der Modellentwicklung hinzuzufügen, aber es ist einfacher, festzustellen, welchen Einfluss jede Funktion auf die Ergebnisse hat, nachdem das Modell mit Äußerungen getestet wurde. 

Eine bewährte Methode ist das Testen über den [Endpunkt](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance), damit Sie vom zusätzlichen Vorteil des [aktiven Lernens](luis-concept-review-endpoint-utterances.md) profitieren. Der [interaktive Testbereich](luis-interactive-test.md) ist auch eine gültige Testmethode. 
 

### <a name="phrase-lists"></a>Begriffslisten

Mithilfe von [Ausdruckslisten](luis-concept-feature.md) können Sie Wörterbücher definieren, die Wörter im Zusammenhang mit Ihrer App-Domäne enthalten. Beginnen Sie die Ausdrucksliste mit wenigen Wörtern, und nutzen Sie dann das Vorschlagfeature, damit LUIS weitere für Ihre App spezifische Wörter im Vokabular kennt. Eine Begriffsliste verbessert die Absichtserkennung und die Entitätsklassifizierung durch eine stärkere Berücksichtigung des Signals, das Wörtern oder Ausdrücken zugeordnet ist, die für Ihre App von Bedeutung sind. 

Fügen Sie dem Vokabular nicht jedes Wort hinzu, da die Ausdrucksliste keine genaue Übereinstimmung darstellen soll. 

Weitere Informationen finden Sie unter:
* Konzept: [Features für Begriffslisten in Ihrer LUIS-App](luis-concept-feature.md)
* Gewusst wie: [Verwenden von Begriffslisten, um das Signal von Wortlisten zu verstärken](luis-how-to-add-features.md)

### <a name="patterns"></a>Muster

Äußerungen echter Benutzer des Endpunkts, die sich sehr stark ähneln, können auf Muster bei der Wortwahl und -anordnung hinweisen. Das [Musterfeature](luis-concept-patterns.md) übernimmt diese Wortwahl und -anordnung zusammen mit regulären Ausdrücken, um die Vorhersagegenauigkeit zu verbessern. Ein regulärer Ausdruck im Muster ermöglicht es Ihnen, Wörter und Satzzeichen zu ignorieren, während das Muster weiterhin übereinstimmt. 

Verwenden Sie die [optionale Syntax](luis-concept-patterns.md) des Musters für Satzzeichen, sodass diese ignoriert werden können. Verwenden Sie die [explizite Liste](luis-concept-patterns.md#explicit-lists) um Syntaxfehler vom Typ „pattern.any“ zu kompensieren. 

Weitere Informationen finden Sie unter:
* Konzept: [Verbessern der Vorhersagegenauigkeit mit Mustern](luis-concept-patterns.md)
* Gewusst wie: [Hinzufügen von Mustern zum Verbessern der Vorhersagegenauigkeit](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>Für alle Absichten die gleiche Menge an Äußerungen verwenden

Damit LUIS-Vorhersagen präzise sind, muss die Menge an Beispieläußerungen in jeder Absicht (mit Ausnahme der Absicht „None“) relativ gleich sein. 

Wenn Sie eine Absicht mit 100 Beispieläußerungen und eine Absicht mit 20 Beispieläußerungen haben, ist die Vorhersagequote der Absicht mit 100 Äußerungen höher.  

## <a name="do-add-example-utterances-to-none-intent"></a>Fügen Sie der Absicht „None“ Beispieläußerungen hinzu

Diese Absicht ist die Fallbackabsicht, die alles außerhalb der Anwendung angibt. Fügen Sie der Absicht „None“ eine Beispieläußerung für jeweils 10 Beispieläußerungen im Rest der LUIS-App hinzu.

Weitere Informationen finden Sie unter:
* Konzept: [Grundlegendes zu geeigneten Äußerungen für Ihre LUIS-App](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Verwenden Sie das Vorschlagfeature für das aktive Lernen

Verwenden Sie beim [aktiven Lernen](luis-how-to-review-endpoint-utterances.md) regelmäßig das **Überprüfen von Endpunktäußerungen**, anstatt Absichten zusätzliche Beispieläußerungen hinzuzufügen. Da die App ständig Endpunktäußerungen empfängt, nimmt diese Liste fortlaufend zu und ändert sich.

Weitere Informationen finden Sie unter:
* Konzept: [Konzepte für das Aktivieren des aktiven Lernens durch Überprüfen von Endpunktäußerungen](luis-concept-review-endpoint-utterances.md)
* Tutorial: [Tutorial: Beheben unsicherer Vorhersagen durch Überprüfung von Endpunktäußerungen](luis-tutorial-review-endpoint-utterances.md)
* Gewusst wie: [Überprüfen von Endpunktäußerungen im LUIS-Portal](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>Überwachen Sie die Leistung Ihrer App.

Überwachen Sie die Vorhersagegenauigkeit mithilfe eines [Batchtestsatzes](luis-concept-batch-test.md). 

## <a name="dont-add-many-example-utterances-to-intents"></a>Fügen Sie Absichten nur wenige Beispieläußerungen hinzu.

Nachdem die Anwendung veröffentlicht wurde, fügen Sie in nachfolgenden Iterationen nur Äußerungen aus dem aktiven Lernen hinzu. Wenn sich die Äußerungen zu ähnlich sind, fügen Sie ein Muster hinzu. 

## <a name="dont-use-luis-as-a-training-platform"></a>Verwenden Sie LUIS nicht als Trainingsplattform

LUIS ist für Sprachmodelle vorgesehen. Er ist nicht als allgemeine Trainingsplattform für natürliche Sprache gedacht. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Fügen Sie nur wenige Beispieläußerungen im gleichen Format hinzu, und vernachlässigen Sie andere Format nicht

LUIS erwartet Variationen in den Äußerungen zu einer Absicht. Die Äußerungen können variieren, sollten aber eine einheitliche Bedeutung haben. Variationen können durch die Länge der Äußerung, die Wortwahl und die Wortanordnung entstehen. 

|Verwenden Sie nicht dasselbe Format.|Verwenden Sie unterschiedliche Formate.|
|--|--|
|Buy a ticket to Seattle<br>Kaufe ein Ticket nach Paris<br>Kaufe ein Ticket nach Orlando|Kaufe 1 Ticket nach Seattle<br>Reserviere zwei Plätze im Nachtflug nach Paris nächsten Montag<br>Ich möchte 3 Tickets nach Orlando in den Frühjahrsferien buchen|

In der zweiten Spalte werden verschiedene Verben (buy, reserve, book), verschiedene Mengenangaben (1, two, 3) und unterschiedliche Wortanordnungen verwendet, alle enthalten jedoch dieselbe Absicht: Flugtickets für eine Reise zu erwerben. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Trennen Sie die Definitionen von Absichten und Entitäten

Erstellen Sie eine Absicht für jede Aktion, die Ihr Bot akzeptieren soll. Verwenden Sie Entitäten als Parameter, die diese Aktionen möglich machen. 

Erstellen Sie für einen Chatbot, der Flüge buchen soll, die Absicht **BookFlight**. Sie sollten nicht für jede Fluggesellschaft oder jedes Ziel eine Absicht erstellen. Verwenden Sie diese Datenelemente als [Entitäten](luis-concept-entity-types.md), und kennzeichnen Sie sie in den Beispieläußerungen. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Erstellen Sie keine Ausdruckslisten mit allen möglichen Werten

Stellen Sie einige wenige Beispiele in den [Ausdruckslisten](luis-concept-feature.md) bereit, aber fügen Sie nicht jedes Wort hinzu. LUIS generalisiert und berücksichtigt dabei den Kontext. 

## <a name="dont-add-many-patterns"></a>Fügen Sie nicht viele Muster hinzu.

Fügen Sie nicht zu viele [Muster](luis-concept-patterns.md) hinzu. LUIS ist dafür vorgesehen, mit wenigen Beispielen schnell zu lernen. Überlasten Sie das System nicht unnötigerweise.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Führen Sie nicht für jede einzelne hinzugefügte Beispieläußerung Training und Veröffentlichung durch

Fügen Sie vor dem Training und der Veröffentlichung 10–15 Äußerungen hinzu. Damit können Sie die Auswirkungen auf die Vorhersagegenauigkeit überprüfen. Das Hinzufügen einer einzelnen Äußerung hat möglicherweise keine sichtbaren Auswirkung auf das Ergebnis. 

## <a name="do-use-versions-for-each-app-iteration"></a>Verwenden von Versionen für jede App-Iteration

Jeder Erstellungszyklus muss innerhalb einer neuen [Version](luis-concept-version.md) erfolgen und aus einer vorhandenen Version geklont sein. In LUIS gilt keine Beschränkung für Versionen. Ein Versionsname wird als Teil der API-Route verwendet. Daher ist es wichtig, Zeichen auswählen, die in einer URL zulässig sind, und die Anzahl von 10 Zeichen für eine Version einzuhalten. Entwickeln Sie eine Strategie für Versionsnamen, um Ihre Versionen organisiert zu halten. 

Weitere Informationen finden Sie unter:
* Konzept: [Grundlegendes zur Verwendung einer LUIS-Version](luis-concept-version.md)
* Gewusst wie: [Verwenden von Versionen, um Staging- und Produktions-Apps nicht durch Bearbeitungsschritte oder Tests zu beeinträchtigen](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie in der LUIS-App [Ihre App planen](luis-how-plan-your-app.md).
