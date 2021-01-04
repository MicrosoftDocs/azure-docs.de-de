---
title: Stellen von Fragen, die durch Daten beantwortet werden können – ML Studio (Classic) – Azure
description: Erfahren Sie im 3. Video von „Data Science für Einsteiger“, wie Sie eine gezielte Data Science-Frage formulieren. Enthält einen Vergleich von Klassifizierungs- und Regressionsfragen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.date: 03/22/2019
ms.openlocfilehash: dbac7c8818300ffb09959969e6b79f949177f457
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307673"
---
# <a name="ask-a-question-you-can-answer-with-data"></a>Stellen einer Frage, die Sie mit Daten beantworten können
## <a name="video-3-data-science-for-beginners-series"></a>Video 3: Reihe „Data Science für Einsteiger“
Erfahren Sie im 3. Video von „Data Science für Einsteiger“, wie Sie ein Data Science-Problem in einer Frage formulieren. Dieses Video enthält einen Vergleich von Fragen zu Klassifizierungs- und Regressionsalgorithmen.

Die Reihe bietet den größten Nutzen, wenn Sie sich alle Videos ansehen. [Zur Liste der Videos wechseln](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Data-science-for-beginners-Ask-a-question-you-can-answer-with-data/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere Videos in dieser Reihe
*Data Science für Einsteiger* erhalten Sie eine Schnelleinführung in Data Science.

* Video 1: [Die 5 Fragen, die Data Science beantwortet](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5:14 Min.)*
* Video 2: [Sind Ihre Daten für Data Science bereit?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4:56 Min.)*
* Video 3: Stellen einer Frage, die Sie mit Daten beantworten können
* Video 4: [Vorhersagen einer Antwort mit einem einfachen Modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7:42 Min.)*
* Video 5: [Kopieren der Arbeit anderer für Ihre Data Science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3:18 Min.)*

## <a name="transcript-ask-a-question-you-can-answer-with-data"></a>Transkript: Stellen einer Frage, die Sie mit Daten beantworten können
Willkommen beim dritten Video der Reihe „Data Science für Einsteiger“.  

In diesem Artikel erhalten Sie einige Tipps zum Formulieren einer Frage, die Sie mit Daten beantworten können.

Sie lernen mehr bei diesem Video, wenn Sie sich zuerst die vorherigen beiden Videos der Reihe ansehen: „Die 5 Fragen, die Data Science beantwortet“ und „Sind Ihre Daten für Data Science bereit?“

## <a name="ask-a-sharp-question"></a>Stellen einer genauen Frage
Wir haben darüber gesprochen, dass es sich bei Data Science um den Prozess der Verwendung von Namen (bzw. von Kategorien oder Bezeichnungen) und Zahlen zum Vorhersagen einer Antwort auf eine Frage handelt. Dabei darf es sich nicht um eine beliebige Frage handeln, sondern es muss eine *genaue Frage* sein.

Eine vage Frage muss nicht mit einem Namen oder einer Zahl beantwortet werden. Eine genaue Frage dagegen schon.

Angenommen, Sie haben eine Wunderlampe mit einem Geist gefunden, der Ihnen jede Frage, die Sie ihm stellen, wahrheitsgemäß beantwortet. Doch es handelt sich um einen schelmischen Geist, der seine Antwort so vage und verwirrend wie gerade eben möglich formuliert. Sie möchten ihn deshalb mit einer Frage festnageln, die so hieb- und stichfest ist, dass er nicht anders kann, als Ihnen zu sagen, was Sie wissen wollen.

Wenn Sie eine vage Frage stellen, wie z.B. „Was passiert mit meinen Aktien?“, antwortet der Geist womöglich: „Der Preis wird sich ändern“. Das ist eine wahrheitsgemäße Antwort, die aber nicht sehr hilfreich ist.

Wenn Sie jedoch eine genaue Frage stellen, wie z.B. „Wie ist der Verkaufspreis meiner Aktien in der nächsten Woche?“, kann der Geist nicht anders, als Ihnen eine präzise Antwort mit einer Vorhersage eines Verkaufspreises zu geben.

## <a name="examples-of-your-answer-target-data"></a>Beispiele für Ihre Antwort: Zieldaten
Sobald Sie Ihre Fragen formuliert haben, prüfen Sie, ob es Beispiele für die Antwort in Ihren Daten gibt.

Ist unsere Frage „Wie hoch ist der Verkaufspreis meiner Aktien nächste Woche?“, dann müssen wir sicherstellen, dass unsere Daten den Verlauf des Aktienkurses enthalten.

Ist unsere Frage „Welches Auto in meinem Fuhrpark fällt zuerst aus?“, dann müssen wir sicherstellen, dass unsere Daten Informationen zu früheren Mängeln enthalten.

![Zieldaten: Beispiele für Ihre Antwort Formulieren einer Data Science-Frage](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/target-data.png)

Diese Beispiele von Antworten werden als „Ziel“ bezeichnet. Ein Ziel ist, was wir zu künftigen Datenpunkten vorhersagen möchten, sei es eine Kategorie oder eine Zahl.

Wenn Sie keine Zieldaten haben, müssen Sie sich welche verschaffen. Denn ohne diese ist keine Antwort auf Ihre Frage möglich.

## <a name="reformulate-your-question"></a>Umformulieren Ihrer Frage
Mitunter können Sie Ihre Frage umformulieren, um eine nützlichere Antwort zu erhalten.

Die Frage „Ist dies Datenpunkt A oder B?“ sagt die Kategorie (bzw. den Namen oder die Bezeichnung) von etwas vorher. Um sie zu beantworten, verwenden wir einen *Klassifikationsalgorithmus*.

Die Frage „Wie viel?“ oder „Wie viele?“ sagt eine Menge vorher. Um sie zu beantworten, verwenden wir einen *Regressionsalgorithmus*.

Um zu prüfen, wie wir diese transformieren können, lassen Sie uns einen Blick auf die folgende Frage werfen: „Welcher Zeitungsartikel ist für diesen Leser am interessantesten?“ Gefragt wird nach einer Vorhersage einer einzelnen Auswahl aus vielen Möglichkeiten, also „Ist dies A oder B oder C oder D?“, weshalb ein Klassifizierungsalgorithmus zum Einsatz kommt.

Doch diese Frage lässt sich ggf. einfacher beantworten, wenn Sie sie so umformulieren: „Wie interessant ist jeder Artikel in dieser Liste für diesen Leser?“ Nun können Sie jedem Artikel einen numerischen Wert zuordnen, wodurch es einfach ist, den Artikel mit dem höchsten Wert zu bestimmen. Dies ist das Umformulieren der Klassifizierungsfrage in eine Regressionsfrage (bzw. „Wie viel?“).

![Umformulieren Ihrer Frage Klassifizierungsfrage im Vergleich mit Regressionsfrage](./media/data-science-for-beginners-ask-a-question-you-can-answer-with-data/classification-question-vs-regression-question.png)

Wie Sie eine Frage stellen, bestimmt, welcher Algorithmus Ihnen eine Antwort liefern kann.

Sie werden feststellen, dass bestimmte Gruppen von Algorithmen, wie z.B. diejenigen in unserem Beispiel mit den Artikeln, eng miteinander verknüpft sind. Sie können Ihre Frage so umformulieren, dass der Algorithmus verwendet wird, der Ihnen die nützlichste Antwort liefert.

Doch am wichtigsten ist, dass Sie Ihre Frage so genau wie möglich stellen, damit sie mit Daten beantwortet werden kann. Und stellen Sie sicher, dass Sie über die richtigen Daten dafür verfügen.

Wir haben uns mit einigen Grundprinzipien zum Stellen einer Frage beschäftigt, die Sie mit Daten beantworten können.

Sehen Sie sich unbedingt auch die anderen Videos in der Reihe „Data Science für Einsteiger“ von Microsoft Azure Machine Learning Studio (klassisch) an.

## <a name="next-steps"></a>Nächste Schritte
* [Durchführen eines ersten Data Science-Experiments mit Machine Learning Studio (klassisch)](create-experiment.md)
* [Einführung in Machine Learning in Microsoft Azure](../overview-what-is-azure-ml.md)