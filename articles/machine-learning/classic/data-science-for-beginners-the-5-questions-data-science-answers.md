---
title: 'ML Studio (Classic): Data Science für Einsteiger – Azure'
description: Data Science für Anfänger erläutert die grundlegenden Konzepte in fünf kurzen Videos, beginnend mit den fünf Fragen, die Data Science beantwortet. Aus Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: ae4eca82df498f1aa8f9ef84691cecdf719d0d76
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307488"
---
# <a name="data-science-for-beginners-video-1-the-5-questions-data-science-answers"></a>Data Science für Einsteiger – Video 1: Die 5 Fragen, die Data Science beantwortet

In den fünf kurzen Videos der Reihe *Data Science für Einsteiger* von einem Data Science-Experten erhalten Sie eine kurze Einführung in Data Science. In diesen Videos werden zwar nur die Grundlagen behandelt, sie sind aber hilfreich, wenn Sie sich selbst mit Data Science beschäftigen möchten oder mit Data Scientists arbeiten.

In diesem ersten Video geht es um die Arten von Fragen, die Data Science beantworten kann. Die Reihe bietet den größten Nutzen, wenn Sie sich alle Videos ansehen. [Zur Liste der Videos wechseln](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/8/player]
>
>

## <a name="other-videos-in-this-series"></a>Andere Videos in dieser Reihe
In *Data Science für Einsteiger* erhalten Sie eine ca. 25 Minuten dauernde Schnelleinführung in Data Science. Sehen Sie sich alle fünf Videos an:

* Video 1: Die 5 Fragen, die Data Science beantwortet
* Video 2: [Sind Ihre Daten für Data Science bereit?](data-science-for-beginners-is-your-data-ready-for-data-science.md) *(4:56 Min.)*
* Video 3: [Stellen einer Frage, die Sie mit Daten beantworten können](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4:17 Min.)*
* Video 4: [Vorhersagen einer Antwort mit einem einfachen Modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7:42 Min.)*
* Video 5: [Kopieren der Arbeit anderer für Ihre Data Science](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3:18 Min.)*

## <a name="transcript-the-5-questions-data-science-answers"></a>Transkript: Die 5 Fragen, die Data Science beantwortet
Hallo! Willkommen bei der Videoreihe *Data Science für Einsteiger*.

Data Science kann einschüchternd sein, weshalb ich hier die Grundlagen ohne jegliche Gleichungen oder Fachausdrücke von Programmierern vorstelle.

In diesem ersten Video befassen wir uns mit den „5 Fragen, die Data Science beantwortet“.

Data Science verwendet Zahlen und Namen (auch „Kategorien“ oder „Bezeichnungen“ genannt), um Antworten auf Fragen vorherzusagen.

Es mag Sie vielleicht überraschen, doch *es gibt nur fünf Fragen, die Data Science beantwortet* :

* Ist dies A oder B?
* Ist dies merkwürdig?
* Wie viel – oder – wie viele?
* Wie ist dies organisiert?
* Was soll ich als Nächstes tun?

Jede dieser Fragen wird mithilfe einer eigenen Gruppe von Machine Learning-Methoden beantwortet, die Algorithmen genannt werden.

Es ist hilfreich, sich einen Algorithmus als Rezept und Ihre Daten als die Zutaten vorzustellen. Ein Algorithmus gibt vor, wie die Daten zu kombinieren und zu mischen sind, um eine Antwort zu erhalten. Computer fungieren dabei als ein Mixer. Sie übernehmen einen Großteil der harten Arbeit des Algorithmus für Sie, und zwar ziemlich schnell.

## <a name="question-1-is-this-a-or-b-uses-classification-algorithms"></a>Frage 1: Für „Ist dies A oder B?“ werden Klassifizierungsalgorithmen verwendet.
Wir beginnen mit der Frage: Ist dies A oder B?

![Klassifizierungsalgorithmen: Ist dies A oder B?](./media/data-science-for-beginners-the-5-questions-data-science-answers/classification-algorithms.png)

Diese Gruppe von Algorithmen wird als Klassifizierung mit zwei Klassen bezeichnet.

Sie empfiehlt sich für Fragen, die nur zwei mögliche Antworten haben.

Beispiel:

* Übersteht dieser Reifen die nächsten 1.600 km: Ja oder nein?
* Was bringt mehr Kunden: ein 5-€-Gutschein oder ein Rabatt von 25 %?

Diese Frage kann auch so umformuliert werden, dass es mehr als zwei Optionen gibt: Ist dies A oder B oder C oder D usw.?  Dies wird als Multiklassenklassifizierung bezeichnet und ist nützlich, wenn es mehrere (oder mehrere tausend) mögliche Antworten gibt. Die Multiklassenklassifizierung wählt die wahrscheinlichste Antwort.

## <a name="question-2-is-this-weird-uses-anomaly-detection-algorithms"></a>Frage 2: Für „Ist dies merkwürdig?“ werden Algorithmen zur Erkennung von Anomalien verwendet.
Die nächste Frage, die Data Science beantworten kann, lautet: Ist dies merkwürdig? Diese Frage wird mithilfe einer Gruppe von Algorithmen zur Erkennung von Anomalien beantwortet.

![Algorithmen für die Anomalieerkennung: Ist dies merkwürdig?](./media/data-science-for-beginners-the-5-questions-data-science-answers/anomaly-detection-algorithms.png)

Wenn Sie eine Kreditkarte besitzen, haben Sie bereits von der Anomalieerkennung profitiert. Ihr Kreditkartenunternehmen analysiert Ihre Einkaufsmuster, damit es Sie bei einem möglichen Betrug warnen kann. „Merkwürdige“ Abrechnungspositionen sind z.B. ein Kauf in einem Geschäft, in dem Sie normalerweise nicht einkaufen, oder der Kauf eines ungewöhnlich teuren Artikels.

Diese Frage kann auf viele Weisen nützlich sein. Beispiel:

* Wenn Sie über einen PKW mit Reifendruckkontrollsystem verfügen, möchten Sie ggf. Folgendes wissen: Stimmt der angezeigte Wert des Reifendruckkontrollsystems?
* Bei der Internetüberwachung möchten Sie ggf. Folgendes wissen: Ist dies eine typische Nachricht aus dem Internet?

Die Erkennung von Anomalien dient zum Kennzeichnen unerwarteter oder ungewöhnlicher Ereignisse oder Verhaltensweisen. Sie gibt Hinweise, wo nach Problemen zu suchen ist.

## <a name="question-3-how-much-or-how-many-uses-regression-algorithms"></a>Frage 3: Für „Wie viel bzw. wie viele?“ werden Regressionsalgorithmen verwendet.
Machine Learning kann auch die Antwort auf die Fragen „Wie viel?“ oder „Wie viele?“ vorhersagen. Die Gruppe von Algorithmen, die diese Frage beantwortet, wird als Regression bezeichnet.

![Regressionsalgorithmen: Wie viel bzw. wie viele?](./media/data-science-for-beginners-the-5-questions-data-science-answers/regression-algorithms.png)

Regressionsalgorithmen treffen numerische Vorhersagen, wie z.B.:

* Wie wird die Temperatur nächsten Dienstag sein?  
* Wie hoch wird mein Umsatz im vierten Quartal sein?

Diese Algorithmen helfen beim Beantworten von Fragen nach einer Zahl.

## <a name="question-4-how-is-this-organized-uses-clustering-algorithms"></a>Frage 4: Für „Wie ist dies organisiert?“ werden Clusteringalgorithmen verwendet.
Die letzten beiden Fragen sind allerdings etwas komplexer.

Mitunter möchten Sie die Struktur eines Datasets verstehen, d.h. wie es organisiert ist. Bei dieser Frage gibt es keine Beispiele, für die Sie bereits Ergebnisse kennen.

Es gibt viele Möglichkeiten, die Struktur von Daten zu ermitteln. Ein Ansatz ist das Clustering. Es dient zum Trennen von Daten in natürliche „Haufen“ zur Vereinfachung der Interpretation. Beim Clustering gibt es nicht die eine richtige Antwort.

![Clusteringalgorithmen: Wie ist dies organisiert?](./media/data-science-for-beginners-the-5-questions-data-science-answers/clustering-algorithms.png)

Gängige Beispiele für Clusteringfragen sind u.a.:

* Welche Besucher mögen dieselben Arten von Filmen?
* Welche Druckermodelle fallen auf dieselbe Weise aus?

Indem Sie verstehen, wie Daten organisiert sind, können Sie Verhalten und Ereignisse besser verstehen und vorhersagen.  

## <a name="question-5-what-should-i-do-now-uses-reinforcement-learning-algorithms"></a>Frage 5: Für „Was soll ich jetzt tun?“ werden Algorithmen für vertiefendes Lernen verwendet.
Für die letzte Frage „Was soll ich jetzt tun?“ wird eine Gruppe von Algorithmen mit der Bezeichnung „Vertiefendes Lernen“ verwendet.

Vertiefendes Lernen wurde dadurch inspiriert, wie die Gehirne von Ratten und Menschen auf Bestrafung und Belohnungen reagieren. Diese Algorithmen lernen von Ergebnissen und entscheiden sich für die nächste Aktion.

Vertiefendes Lernen ist in der Regel gut für automatisierte Systeme geeignet, die viele kleine Entscheidungen ohne menschliche Anleitung treffen müssen.

![Algorithmen für vertiefendes Lernen: Was soll ich als Nächstes tun?](./media/data-science-for-beginners-the-5-questions-data-science-answers/reinforcement-learning-algorithms.png)

Fragen, die beantwortet werden, drehen sich stets darum, welche Aktion ausgeführt werden sollte, meist von einer Maschine oder einem Roboter. Beispiele:

* Bei einem Temperaturregelsystem für ein Haus: Soll die Temperatur angepasst oder unverändert gelassen werden?  
* Bei einem selbstfahrenden PKW: Soll bei einer gelben Ampel gebremst oder beschleunigt werden?  
* Für einen Staubsauger-Roboter: Weiter saugen oder zur Ladestation zurückkehren?

Algorithmen für vertiefendes Lernen sammeln Daten im laufenden Betrieb und lernen durch systematisches Ausprobieren.

Das sind sie also: Die 5 Fragen, die Data Science beantworten kann.

## <a name="next-steps"></a>Nächste Schritte
* [Durchführen eines ersten Data Science-Experiments mit Machine Learning Studio (klassisch)](create-experiment.md)
* [Einführung in Machine Learning in Microsoft Azure](../overview-what-is-azure-ml.md)