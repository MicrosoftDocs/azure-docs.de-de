---
title: Gute Beispieläußerungen – LUIS
description: Äußerungen sind Eingaben vom Benutzer, die Ihre App interpretieren muss. Sammeln Sie Ausdrücke, die Benutzer möglicherweise eingeben. Fügen Sie Äußerungen ein, die dieselbe Bedeutung haben, aber andere Wortlängen oder einen anderen Satzbau aufweisen.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 96b9754908f437ccf81e002e9e9dd17af0bab4e3
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019074"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Grundlegendes zu geeigneten Äußerungen für Ihre LUIS-App

**Äußerungen** sind Eingaben vom Benutzer, die Ihre App interpretieren muss. Es ist wichtig, eine Vielzahl verschiedener Beispieläußerungen für jede Absicht zu erfassen, um LUIS für die Extrahierung von Absichten und Entitäten zu trainieren. Aktives Lernen bzw. der Prozess des kontinuierlichen Trainierens neuer Äußerungen ist ein grundlegender Bestandteil der Machine Learning-Intelligenz, die LUIS bereitstellt.

Sammeln Sie Äußerungen, die Benutzer möglicherweise eingeben. Schließen Sie Äußerungen mit ein, die zwar die gleiche Bedeutung haben, aber unterschiedlich konstruiert sind:

* Äußerungslänge (kurz, mittel und lang für Ihre Clientanwendung)
* Wort- und Ausdruckslänge
* Wortposition (Entität am Anfang, in der Mitte und am Ende der Äußerung)
* Grammatik
* Pluralisierung
* Wortstammerkennung
* Nomen und Verb
* [Interpunktion](luis-reference-application-settings.md#punctuation-normalization) (Varianten mit korrekter und falscher Grammatik sowie ohne Grammatik)

## <a name="how-to-choose-varied-utterances"></a>Auswählen verschiedener Äußerungen

Wenn Sie mit Ihrem LUIS-Modell einsteigen, indem Sie [Beispieläußerungen hinzufügen](./luis-how-to-add-entities.md), finden Sie im Folgenden einige wichtige Prinzipien.

### <a name="utterances-arent-always-well-formed"></a>Äußerungen sind nicht immer ordnungsgemäß formatiert

Dabei kann es sich um einen Satz (z.B. „Ein Ticket nach Paris für mich buchen“) oder um einen Teil eines Satzes (z.B. „Buchen“ oder „Flug nach Paris“) handeln.  Benutzer machen oft Rechtschreibfehler. Überlegen Sie sich daher bei der Planung Ihrer App, ob Sie Benutzereingaben mithilfe der [Bing-Rechtschreibprüfung](luis-tutorial-bing-spellcheck.md) korrigieren möchten, bevor Sie sie an LUIS übergeben.

Wenn Sie keine Rechtschreibprüfung auf die Äußerungen Ihrer Benutzer anwenden, sollten Sie LUIS auf Äußerungen trainieren, die Tipp- und Schreibfehler enthalten.

### <a name="use-the-representative-language-of-the-user"></a>Verwenden Sie Sprache, die den Benutzern entspricht

Beachten Sie bei der Wahl der Äußerungen, dass das, was Sie als allgemeinen Begriff oder Ausdruck kennen, unter Umständen für typische Benutzer Ihrer Clientanwendung nicht geeignet ist. Möglicherweise fehlt den Benutzern das Fachwissen. Seien Sie daher vorsichtig mit Begriffen oder Ausdrücken, die ein Benutzer nur verwenden würde, wenn er bestens mit der Thematik vertraut ist.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Auswählen variierender Terminologie und Ausdrücke

Auch wenn Sie sich bemühen, variierende Satzstrukturen zu verwenden, werden Sie feststellen, dass Sie einiges an Vokabular wiederholen müssen.

Sehen Sie sich diese Beispieläußerungen an:

|Beispiele für Äußerungen|
|--|
|Wie bekomme ich einen Computer?|
|Wo bekomme ich einen Computer?|
|Ich möchte einen Computer bekommen, wie gehe ich dazu vor?|
|Wann kann ich einen Computer bekommen?|

Für den zentralen Begriff („Computer“) sind keine Varianten vorhanden. Verwenden Sie Alternativen wie „Desktopcomputer“, „Laptop“, „Arbeitsstation“ oder sogar nur „Maschine“. LUIS kann diese Synonyme auf intelligente Weise vom Kontext ableiten. Wenn Sie jedoch Äußerungen zum Trainieren erstellen, ist es immer besser, diese zu variieren.

## <a name="example-utterances-in-each-intent"></a>Beispieläußerungen mit jeder Absicht

Jede Absicht benötigt mindestens 15 Beispieläußerungen. Wenn Sie jedoch über eine Absicht verfügen, die keine Beispieläußerungen aufweist, können Sie LUIS nicht trainieren. Wenn Sie über eine Absicht mit nur einer oder sehr wenigen Beispieläußerungen verfügen, sagt LUIS die Absicht möglicherweise nicht genau vorher.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Hinzufügen kleiner Gruppen mit 15 Äußerungen pro Erstellungsiteration

Fügen Sie nicht jeder Iteration des Modells große Mengen von Äußerungen hinzu. Fügen Sie Äußerungen in 15er Gruppen hinzu. [Trainieren](luis-how-to-train.md), [veröffentlichen](luis-how-to-publish-app.md) und [testen](luis-interactive-test.md) Sie anschließend erneut.

LUIS erstellt effektive Modelle mit sorgfältig vom LUIS-Modellersteller ausgewählten Äußerungen. Das Hinzufügen zu vieler Äußerungen ist nicht nützlich, da es zu Verwechslungen führen kann.

Es ist besser, mit nur wenigen Äußerungen anzufangen und anschließend die [Endpunktäußerungen zu überprüfen](luis-how-to-review-endpoint-utterances.md), um die richtige Vorhersage der Absicht und Extraktion der Entität zu gewährleisten.

## <a name="utterance-normalization"></a>Äußerungsnormalisierung

Bei der Äußerungsnormalisierung wird die Wirkung von Textelementen wie Interpunktion und diakritischen Zeichen beim Trainieren und bei Vorhersagen ignoriert.

Standardmäßig sind die Äußerungsnormalisierungseinstellungen deaktiviert. Dies umfasst Folgendes:

* Wortformen
* Diakritische Zeichen
* Interpunktion

Wenn Sie eine Normalisierungseinstellung aktivieren, ändern sich Bewertungen im Bereich **Test** sowie Batchtests und Endpunktabfragen für alle Äußerungen, für die diese Normalisierungseinstellung gilt.

Wenn Sie eine Version im LUIS-Portal klonen, werden die Versionseinstellungen in der neuen geklonten Version übernommen.

Die Versionseinstellungen können über das LUIS-Portal, im Abschnitt **Verwalten**, auf der Seite **Anwendungseinstellungen** oder über die [API zum Aktualisieren der Versionseinstellungen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) festgelegt werden. Weitere Informationen zu diesen Normalisierungsänderungen finden Sie in der [Referenz](luis-reference-application-settings.md).

### <a name="word-forms"></a>Wortformen

Bei der Normalisierung von **Wortformen** werden über den Wortstamm hinausgehende Unterschiede ignoriert.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Diakritische Zeichen

Diakritische Zeichen sind Markierungen oder Kennzeichnungen innerhalb des Texts, z. B.:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Interpunktion
**Interpunktion** zu normalisieren bedeutet, dass die Interpunktionszeichen aus den Äußerungen entfernt werden, bevor Ihre Modelle trainiert und bevor Ihre Endpunktabfragen vorhergesagt werden.

Interpunktion ist ein separates Token in LUIS. Eine Äußerung mit einem Punkt am Ende und eine Äußerung, in der dies nicht der Fall ist, sind zwei separate Äußerungen und erhalten möglicherweise zwei unterschiedliche Vorhersagen.

Wenn die Interpunktion nicht normalisiert wird, ignoriert LUIS Satzzeichen standardmäßig nicht, da diese für einige Clientanwendungen unter Umständen wichtig sind. Stellen Sie sicher, dass Sie Beispieläußerungen mit und ohne Satzzeichen verwenden, damit beide Formate die gleichen relativen Ergebnisse zurückgeben.

Stellen Sie sicher, dass das Modell die Interpunktion entweder in den Beispieläußerungen (mit und ohne Interpunktion) oder in den [Mustern](luis-concept-patterns.md) behandelt, wo es mit der speziellen Syntax einfacher ist, die Interpunktion zu ignorieren: `I am applying for the {Job} position[.]`

Wenn die Interpunktion in Ihrer Clientanwendung keine besondere Bedeutung hat, sollten Sie erwägen, [Satzzeichen zu ignorieren](#utterance-normalization), indem Sie die Interpunktion normalisieren.

### <a name="ignoring-words-and-punctuation"></a>Ignorieren von Wörtern und Interpunktion

Wenn Sie bestimme Wörter oder Satzzeichen ignorieren möchten, können Sie [Muster](luis-concept-patterns.md#pattern-syntax) verwenden. Verwenden Sie in diesen zum _Ignorieren_ von Wörtern und Satzzeichen eine Syntax mit eckigen Klammern (`[]`).

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Trainieren mit allen Äußerungen

Das Training ist im Allgemeinen nicht deterministisch: Die Vorhersage von Äußerungen kann über verschiedene Versionen oder Apps hinweg variieren.
Sie können nicht deterministisches Training entfernen, indem Sie die API für die [Versionseinstellungen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) mit dem `UseAllTrainingData`-Name-Wert-Paar aktualisieren, um alle Trainingsdaten zu verwenden.

## <a name="testing-utterances"></a>Testen von Äußerungen

Entwickler sollten damit beginnen, ihre LUIS-Anwendung mit echtem Datenverkehr zu testen, indem sie Äußerungen an die URL des [Vorhersageendpunkts](luis-how-to-azure-subscription.md) senden. Diese Äußerungen werden verwendet, um die Leistung der Absichten und Entitäten mit [Überprüfungsäußerungen](luis-how-to-review-endpoint-utterances.md) zu verbessern. Tests, die über den Testbereich der LUIS-Website eingesendet werden, werden nicht über den Endpunkt gesendet und tragen daher nicht zum aktiven Lernen bei.

## <a name="review-utterances"></a>Überprüfen von Äußerungen

Nachdem Ihr Modell trainiert sowie veröffentlicht ist und [Endpunktabfragen](luis-glossary.md#endpoint) empfängt, [überprüfen Sie die Äußerungen](luis-how-to-review-endpoint-utterances.md), die LUIS vorschlägt. LUIS wählt Endpunktäußerungen aus, die niedrige Bewertungen für die Absicht oder Entität aufweisen.

## <a name="best-practices"></a>Bewährte Methoden

Machen Sie sich mit den [bewährten Methoden](luis-concept-best-practices.md) vertraut, und integrieren Sie sie in Ihren Erstellungszyklus.

## <a name="label-for-word-meaning"></a>Bezeichnungen für Wortbedeutungen

Wenn die Wortauswahl oder die Anordnung der Wörter identisch ist, sie aber nicht dieselbe Bedeutung haben, sollten Sie sie nicht mit der Entität bezeichnen.

In den folgenden Äußerungen ist das Wort `fair` ein Homograph. Die Schreibweise ist identisch, aber die Bedeutung ist eine andere:

|Äußerung|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

Wenn Sie mit einer Veranstaltungsentität alle Veranstaltungsdaten suchen möchten, bezeichnen Sie das Wort `fair` in der ersten Äußerung, aber nicht in der zweiten.


## <a name="next-steps"></a>Nächste Schritte
Informationen zum Trainieren einer LUIS-App, damit sie Benutzeräußerungen versteht, finden Sie unter [Hinzufügen von Beispieläußerungen und -beschriftungen bei Entitäten](./luis-how-to-add-entities.md).