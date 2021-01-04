---
title: Bewährte Methoden für das Entwickeln von LUIS-Apps
description: Erfahren Sie, wie Sie mit bewährten Methoden die besten Ergebnisse für das Modell Ihrer LUIS-App erzielen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: fc4475eb64744688e901d2b37e30632d044b0115
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019312"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Bewährte Methoden zum Erstellen von LUIS-Apps (Language Understanding)
Verwenden Sie den App-Erstellungsprozess zur Erstellung der LUIS-App:

* Erstellen Sie Sprachmodelle (Absichten und Entitäten)
* Fügen Sie einige Beispieläußerungen zum Trainieren hinzu (15–30 pro Absicht)
* Veröffentlichen Sie am Endpunkt
* Testen am Endpunkt

Nachdem die App [veröffentlicht](luis-how-to-publish-app.md) wurde, folgen Sie dem Entwicklungslebenszyklus, um Features hinzuzufügen, zu veröffentlichen und am Endpunkt zu testen. Beginnen Sie den nächsten Erstellungszyklus nicht, indem Sie weitere Beispieläußerungen hinzufügen, weil LUIS bei diesem Vorgehen Ihr Modell nicht an echten Benutzeräußerungen lernen lässt.

Erweitern Sie die Äußerungen erst, wenn der aktuelle Satz von Beispiel- und Endpunktäußerungen angemessene Zuverlässigkeits- und Vorhersagebewertungen ergibt. Verbessern Sie die Punktestände mithilfe von [aktivem Lernen](luis-concept-review-endpoint-utterances.md).




## <a name="do-and-dont"></a>Empfehlungen
Die folgende Liste enthält bewährte Methoden für LUIS-Apps:

|Sie sollten|Sie sollten auf keinen Fall|
|--|--|
|[Planen Ihres Schemas](#do-plan-your-schema)|[Erstellen und Veröffentlichen ohne vorherige Planung](#dont-publish-too-quickly)|
|[Definieren unterschiedlicher Absichten](#do-define-distinct-intents)<br>[Hinzufügen von Features zu Absichten](#do-add-features-to-intents)<br>
[Verwenden von durch maschinelles Lernen erworbenen Entitäten](#do-use-machine-learned-entities) |[Hinzufügen vieler Beispieläußerungen zu Absichten](#dont-add-many-example-utterances-to-intents)<br>[Verwenden weniger oder einfacher Entitäten](#dont-use-few-or-simple-entities) |
|[Anstreben einer Balance zwischen zu allgemeinen und zu spezifischen Absichten](#do-find-sweet-spot-for-intents)|[Verwenden von LUIS als Trainingsplattform](#dont-use-luis-as-a-training-platform)|
|[Iteratives Aufbauen der App mit Versionen](#do-build-your-app-iteratively-with-versions)<br>[Erstellen von Entitäten zur Modellaufschlüsselung](#do-build-for-model-decomposition)|[Hinzufügen sehr vieler Beispieläußerungen im gleichen Format und Ignorieren anderer Formate](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Hinzufügen von Mustern in späteren Iterationen](#do-add-patterns-in-later-iterations)|[Kombinieren der Definition von Absichten und Entitäten](#dont-mix-the-definition-of-intents-and-entities)|
|[Verwenden Sie für alle Absichten die gleiche Menge an Äußerungen](#balance-your-utterances-across-all-intents), mit Ausnahme der Absicht „None“.<br>[Hinzufügen von Beispieläußerungen zur Absicht „None“](#do-add-example-utterances-to-none-intent)|[Erstellen von Ausdruckslisten mit allen möglichen Werten](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Verwenden des Vorschlagfeatures für das aktive Lernen](#do-leverage-the-suggest-feature-for-active-learning)|[Hinzufügen von zu vielen Mustern](#dont-add-many-patterns)|
|[Überwachen der Leistung Ihrer App mit Batchtests](#do-monitor-the-performance-of-your-app)|[Trainieren und Veröffentlichen jeder einzelnen hinzugefügten Beispieläußerung](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Planen Sie Ihr Schema.

Machen Sie sich zunächst Gedanken dazu, was und wo Sie die App verwenden möchten, bevor Sie mit der Erstellung Ihres App-Schemas beginnen. Je gründlicher und spezifischer Ihre Planung, desto besser Ihre App.

* Untersuchen der Zielbenutzer
* Definieren von End-to-End-Personas für Ihre App: Stimme, Avatar, Problembehandlung (proaktiv, reaktiv)
* Identifizieren von Benutzerinteraktionen (Text, Sprache) und der Kanäle; Übergabe an vorhandene Lösungen oder Erstellung einer neuen Lösung für die App
* End-to-End-User Journey
    * Was soll die App tun und was nicht? * Welche Priorität haben die gewünschten Funktionen der App?
    * Was sind die hauptsächlichen Anwendungsfälle?
* Erfassen von Daten: [Informationen zum Erfassen und Aufbereiten von Daten](data-collection.md)

## <a name="do-define-distinct-intents"></a>Definieren Sie unterschiedliche Absichten
Stellen Sie sicher, dass sich das Vokabular der einzelnen Absichten nicht mit dem anderer Absichten überschneidet. Wenn Sie z.B. eine App planen, die Reisebuchungen wie Flüge oder Hotels verarbeiten soll, können Sie diese Themenbereiche als separate Absichten oder als dieselbe Absicht mit separaten Entitäten für bestimmte Daten innerhalb der Äußerung planen.

Wenn das Vokabular bei zwei Absichten gleich ist, fassen Sie die Absichten zusammen, und verwenden Sie Entitäten.

Erwägen Sie die folgenden Beispieläußerungen:

|Beispiele für Äußerungen|
|--|
|Book a flight|
|Book a hotel|

`Book a flight` und `Book a hotel` verwenden dasselbe Vokabular in Form von `book a `. Das Format ist das gleiche. Aus diesem Grund sollte nur eine Absicht verwendet werden. Extrahieren Sie die unterschiedlichen Wörter `flight` und `hotel` in Entitäten.

## <a name="do-add-features-to-intents"></a>Hinzufügen von Features zu Absichten

Features dienen zum Beschreiben von Konzepten für eine Absicht. Bei einem Feature kann es sich um eine Ausdrucksliste mit Wörtern handeln, die für die betreffende Absicht wichtig sind, oder um eine für die Absicht wichtige Entität.

## <a name="do-find-sweet-spot-for-intents"></a>Streben Sie eine Balance zwischen Absichten an
Verwenden Sie die Vorhersagedaten von LUIS, um zu bestimmen, ob die Absichten überlappen. Überlappende Absichten verwirren LUIS. Im Ergebnis ist die Absicht mit der höchsten Bewertung zu nah an einer anderen Absicht. Da LUIS nicht bei jedem Training genau denselben Pfad durch die Daten anwendet, kann eine überlappende Absicht beim Training den ersten oder zweiten Platz erreichen. Damit eine solche Überlappung nicht eintritt, sollten Sie das Ergebnis der Äußerung für jede Absicht weiter voneinander trennen. Bei einer guten Unterscheidung der Absichten sollte das Ergebnis jedes Mal die erwartete bestbewertete Absicht sein.

## <a name="do-use-machine-learned-entities"></a>Verwenden Sie durch maschinelles Lernen erworbene Entitäten.

Durch maschinelles Lernen erworbene Entitäten sind auf Ihre App zugeschnitten und erfordern eine erfolgreiche Bezeichnung. Wenn Sie keine furch maschinelles Lernen erworbenen Entitäten verwenden, verwenden Sie möglicherweise das falsche Tool.

Durch maschinelles Lernen erworbene Entitäten können andere Entitäten als Features verwenden. Diese anderen Entitäten können benutzerdefinierte Entitäten sein (beispielsweise RegEx-Entitäten oder Listenentitäten). Sie können aber auch vorgefertigte Entitäten als Features verwenden.

Informationen zu effektiven, durch maschinelles Lernen erworbenen Entitäten finden Sie [hier](luis-concept-entity-types.md#effective-machine-learned-entities).

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Bauen Sie Ihre App iterativ mit Versionen auf

Jeder Erstellungszyklus muss innerhalb einer neuen [Version](./luis-concept-app-iteration.md) erfolgen und aus einer vorhandenen Version geklont sein.

## <a name="do-build-for-model-decomposition"></a>Erstellen Sie mit dem Ziel der Modellaufschlüsselung

Die Modellaufschlüsselung folgt dem typischen Prozess von:

* Erstellen einer **Absicht** basierend auf den Benutzerabsichten der Client-App
* Hinzufügen von 15–30 Beispieläußerungen, die auf echten Benutzereingaben beruhen
* Bezeichnen des Datenkonzepts der oberen Ebene in Beispieläußerungen
* Aufschlüsseln des Datenkonzepts in untergeordnete Entitäten
* Hinzufügen von Features zu untergeordneten Entitäten
* Hinzufügen von Features zu Absichten

Nachdem Sie die Absicht erstellt und Beispieläußerungen hinzugefügt haben, wird im folgenden Beispiel die Entitätsaufschlüsselung beschrieben.

Beginnen Sie mit der Identifizierung der vollständigen Datenkonzepte, die Sie in einer Äußerung extrahieren möchten. Dies ist Ihre Machine Learning-Entität. Schlüsseln Sie anschließend den Ausdruck in seine Teile auf. Dies schließt die Identifizierung von untergeordneten Entitäten und Features ein.

Wenn Sie beispielsweise eine Adresse extrahieren möchten, könnte die oberste Machine Learning-Entität `Address` heißen. Identifizieren Sie beim Erstellen der Adresse einige der untergeordneten Entitäten wie etwa die Anschrift, die Stadt, das Bundesland und die Postleitzahl.

Schlüsseln Sie diese Elemente wie folgt weiter auf:
* Fügen Sie ein erforderliches Feature der Postleitzahl als Entität vom Typ „regulärer Ausdruck“ hinzu.
* Schlüsseln Sie die Anschrift in folgende Teile auf:
    * **Hausnummer** mit einem erforderlichen Feature in Form einer vordefinierten Entität (Zahl)
    * **Straßenname**
    * **Straßentyp** mit einem erforderlichen Feature in Form einer Listenentität mit Wörtern wie „Allee“, „Weg“, „Straße“ und „Platz“

Die V3-Erstellungs-API ermöglicht die Aufschlüsselung von Modellen.

## <a name="do-add-patterns-in-later-iterations"></a>Fügen Sie in späteren Iterationen Muster hinzu

Sie sollten verstehen, wie sich die App verhält, bevor Sie [Muster](luis-concept-patterns.md) hinzufügen, da Muster stärker gewichtet werden als Beispieläußerungen und die Zuverlässigkeit verzerren.

Sobald Sie verstehen, wie sich Ihre App verhält, fügen Sie Muster hinzu, sofern sie für Ihre App relevant sind. Sie müssen sie nicht bei jeder [Iteration](luis-concept-app-iteration.md) hinzufügen.

Es ist nicht schädlich, sie am Anfang der Modellentwicklung hinzuzufügen, aber es ist einfacher, festzustellen, welchen Einfluss jedes Muster auf das Modell hat, nachdem das Modell mit Äußerungen getestet wurde.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Gleichen Sie Ihre Äußerungen über alle Absichten hinweg aus

Damit LUIS-Vorhersagen präzise sind, muss die Menge an Beispieläußerungen in jeder Absicht (mit Ausnahme der Absicht „None“) relativ gleich sein.

Wenn Sie eine Absicht mit 100 Beispieläußerungen und eine Absicht mit 20 Beispieläußerungen haben, ist die Vorhersagequote der Absicht mit 100 Äußerungen höher.

## <a name="do-add-example-utterances-to-none-intent"></a>Fügen Sie der Absicht „None“ Beispieläußerungen hinzu

Diese Absicht ist die Fallbackabsicht, die alles außerhalb der Anwendung angibt. Fügen Sie der Absicht „None“ eine Beispieläußerung für jeweils 10 Beispieläußerungen im Rest der LUIS-App hinzu.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Verwenden Sie das Vorschlagfeature für das aktive Lernen

Verwenden Sie beim [aktiven Lernen](luis-how-to-review-endpoint-utterances.md) regelmäßig das **Überprüfen von Endpunktäußerungen**, anstatt Absichten zusätzliche Beispieläußerungen hinzuzufügen. Da die App ständig Endpunktäußerungen empfängt, nimmt diese Liste fortlaufend zu und ändert sich.

## <a name="do-monitor-the-performance-of-your-app"></a>Überwachen Sie die Leistung Ihrer App.

Überwachen Sie die Vorhersagegenauigkeit mithilfe eines [Batchtestsatzes](luis-concept-batch-test.md).

Reservieren Sie eine separate Menge von Äußerungen, die nicht als [Beispieläußerungen](luis-concept-utterance.md) oder Endpunktäußerungen verwendet werden. Optimieren Sie die App immer weiter mit Ihrem Testsatz. Passen Sie den Testsatz an die tatsächlichen Benutzeräußerungen an. Verwenden Sie diesen Testsatz, um die einzelnen Iterationen oder Versionen der App zu bewerten.

## <a name="dont-publish-too-quickly"></a>Überstürzen Sie die Veröffentlichung nicht.

Wenn Sie Ihre App zu schnell (sprich: ohne [ordnungsgemäße Planung](#do-plan-your-schema)) veröffentlichen, kann dies zu diversen Problemen führen. Im Anschluss folgen einige Beispiele:

* Die Leistung Ihrer App lässt in der Praxis zu wünschen übrig.
* Das Schema (Absichten und Entitäten) ist nicht angemessen, und falls Sie auf der Grundlage dieses Schemas Client-App-Logik entwickelt haben, muss diese möglicherweise komplett neu geschrieben werden. Dies führt zu unerwarteten Verzögerungen und zusätzlichen Kosten für das Projekt.
* Äußerungen, die Sie dem Modell hinzufügen, führen möglicherweise zu einer Bevorzugung des Beispieläußerungssatzes, was schwer zu debuggen und zu identifizieren ist. Dies erschwert außerdem die Entfernung von Mehrdeutigkeiten, nachdem Sie sich auf ein bestimmtes Schema festgelegt haben.

## <a name="dont-add-many-example-utterances-to-intents"></a>Fügen Sie Absichten nur wenige Beispieläußerungen hinzu.

Nachdem die Anwendung veröffentlicht wurde, fügen Sie im Entwicklungslebenszyklus-Prozess nur Äußerungen aus dem aktiven Lernen hinzu. Wenn sich die Äußerungen zu ähnlich sind, fügen Sie ein Muster hinzu.

## <a name="dont-use-few-or-simple-entities"></a>Verwenden Sie nicht wenige oder einfache Entitäten

Entitäten werden zum Zweck der Datenextrahierung und -vorhersage erstellt. Es ist entscheidend, dass jede Absicht über Machine Learning-Entitäten verfügt, die die in der Absicht enthaltenen Daten beschreiben. Dies hilft LUIS beim Vorhersagen der Absicht, selbst wenn Ihre Clientanwendung die extrahierte Entität nicht verwenden muss.

## <a name="dont-use-luis-as-a-training-platform"></a>Verwenden Sie LUIS nicht als Trainingsplattform

LUIS ist für Sprachmodelle vorgesehen. Er ist nicht als allgemeine Trainingsplattform für natürliche Sprache gedacht.

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Fügen Sie nur wenige Beispieläußerungen im gleichen Format hinzu, und vernachlässigen Sie andere Format nicht

LUIS erwartet Variationen in den Äußerungen zu einer Absicht. Die Äußerungen können variieren, sollten aber eine einheitliche Bedeutung haben. Variationen können durch die Länge der Äußerung, die Wortwahl und die Wortanordnung entstehen.

|Verwenden Sie nicht dasselbe Format.|Verwenden Sie unterschiedliche Formate.|
|--|--|
|Kaufe ein Ticket nach Seattle<br>Kaufe ein Ticket nach Paris<br>Kaufe ein Ticket nach Orlando|Kaufe 1 Ticket nach Seattle<br>Reserviere zwei Plätze im Nachtflug nach Paris nächsten Montag<br>Ich möchte 3 Tickets nach Orlando in den Frühjahrsferien buchen|

In der zweiten Spalte werden verschiedene Verben (kaufen, reservieren, buchen), verschiedene Mengenangaben (1, zwei, 3) und unterschiedliche Wortanordnungen verwendet, alle enthalten jedoch dieselbe Absicht: Flugtickets für eine Reise zu erwerben.

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Trennen Sie die Definitionen von Absichten und Entitäten

Erstellen Sie eine Absicht für jede Aktion, die Ihr Bot akzeptieren soll. Verwenden Sie Entitäten als Parameter, die diese Aktionen möglich machen.

Erstellen Sie für einen Bot, der Flüge buchen soll, die Absicht **BookFlight**. Sie sollten nicht für jede Fluggesellschaft oder jedes Ziel eine Absicht erstellen. Verwenden Sie diese Datenelemente als [Entitäten](luis-concept-entity-types.md), und kennzeichnen Sie sie in den Beispieläußerungen.

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Erstellen Sie keine Ausdruckslisten mit allen möglichen Werten

Geben Sie in den [Ausdruckslisten](luis-concept-feature.md) einige Beispiele an, aber nicht jedes Wort bzw. jeden Ausdruck. LUIS generalisiert und berücksichtigt dabei den Kontext.

## <a name="dont-add-many-patterns"></a>Fügen Sie nicht viele Muster hinzu.

Fügen Sie nicht zu viele [Muster](luis-concept-patterns.md) hinzu. LUIS ist dafür vorgesehen, mit wenigen Beispielen schnell zu lernen. Überlasten Sie das System nicht unnötigerweise.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Führen Sie nicht für jede einzelne hinzugefügte Beispieläußerung Training und Veröffentlichung durch

Fügen Sie vor dem Training und der Veröffentlichung 10–15 Äußerungen hinzu. Damit können Sie die Auswirkungen auf die Vorhersagegenauigkeit überprüfen. Das Hinzufügen einer einzelnen Äußerung hat möglicherweise keine sichtbaren Auswirkung auf das Ergebnis.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie in der LUIS-App [Ihre App planen](luis-how-plan-your-app.md).