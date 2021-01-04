---
title: Auswählen eines Algorithmus für maschinelles Lernen
titleSuffix: Azure Machine Learning
description: Auswahl eines Azure Machine Learning-Algorithmus für überwachtes und unbeaufsichtigtes Lernen bei Clustering-, Klassifizierungs- oder Regressionsexperimenten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 27b18fdc2dda40f8361483e6ecce28d0ccbd0310
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308240"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>Auswählen von Algorithmen für Azure Machine Learning

Eine häufig gestellte Frage lautet: „Welchen Algorithmus für maschinelles Lernen sollte ich verwenden?“ Der von Ihnen gewählte Algorithmus hängt in erster Linie von zwei verschiedenen Aspekten Ihres Data Science-Szenarios ab:

 - **Was möchten Sie mit Ihren Daten machen?** Wie genau lautet die geschäftliche Frage, die Sie beantworten möchten, indem Sie aus Ihren bisherigen Daten lernen?

 - **Welche Anforderungen gelten für Ihr Data Science-Szenario?** Welche Genauigkeit, Trainingszeit, Linearität, Anzahl der Parameter und Anzahl der Features unterstützt Ihre Lösung im Besonderen?

 ![Überlegungen zur Auswahl von Algorithmen: Was möchten Sie wissen? Wie lauten die Szenarioanforderungen?](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>Geschäftsszenarios und der Spickzettel für Machine Learning-Algorithmen

Der [Spickzettel für Azure Machine Learning-Algorithmen](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri) hilft Ihnen bei der ersten Überlegung: **Was möchten Sie mit Ihren Daten machen** ? Suchen Sie auf dem Spickzettel für den Machine Learning-Algorithmus nach der Aufgabe, die Sie erledigen möchten, und suchen Sie dann einen [Azure Machine Learning-Designeralgorithmus](./concept-designer.md?WT.mc_id=docs-article-lazzeri) für die Predictive Analytics-Lösung. 

Der Machine Learning-Designer bietet ein umfassendes Portfolio an Algorithmen, z. B. [Entscheidungswald mit mehreren Klassen](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri), [Empfehlungssysteme](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri), [Regression mit neuronalen Netzwerken](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri), [Mehrklassiges neuronales Netzwerk](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) und [k-Means-Algorithmus](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri). Jeder Algorithmus ist speziell auf eine andere Art von Machine Learning-Problem ausgelegt. Eine vollständige Liste sowie eine Dokumentation über die Funktionsweise der einzelnen Algorithmen und die Einstellung der Parameter zur Optimierung des Algorithmus finden Sie in der [Machine Learning-Designeralgorithmus- und Modulreferenz](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri).

> [!NOTE]
> Um den Spickzettel für den Machine Learning-Algorithmus herunterzuladen, wechseln Sie zu [Spickzettel für den Azure Machine Learning-Algorithmus](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri).
> 
> 

Berücksichtigen Sie neben der Anleitung auf dem Spickzettel für den Azure Machine Learning-Algorithmus auch andere Anforderungen, wenn Sie einen Algorithmus für maschinelles Lernen für Ihre Lösung auswählen. Im Folgenden sind zusätzliche Faktoren zu berücksichtigen, z. B. die Genauigkeit, die Trainingszeit, die Linearität, die Anzahl der Parameter und die Anzahl der Features.

## <a name="comparison-of-machine-learning-algorithms"></a>Vergleich von Algorithmen für maschinelles Lernen

Einige Lernalgorithmen treffen bestimmte Annahmen über die Struktur der Daten oder der gewünschten Ergebnisse. Wenn Sie einen finden, der Ihren Bedürfnissen entspricht, kann Ihnen dieser genauere Vorhersagen oder kürzere Trainingsdauern liefern.

In der folgenden Tabelle werden einige der wichtigsten Merkmale von Algorithmen aus den Familien Klassifizierung, Regression und Clustering zusammengefasst:

| **Algorithmus** | **Genauigkeit** | **Trainingsdauer** | **Linearität** | **Parameter** | **Hinweise** |
| --- |:---:|:---:|:---:|:---:| --- |
| **Familie Klassifizierung** | | | | | |
| [Logistische Regression mit zwei Klassen](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Gut  |Schnell |Ja |4 | |
| [Entscheidungswald mit zwei Klassen](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Hervorragend |Moderat |Nein |5 |Bei diesem Algorithmus kommt es zu längeren Bewertungszeiten. Es wird empfohlen, nicht mit One-vs-All-Klassifizierung mit mehreren Klassen zu arbeiten, da das Sperren von Threads beim Akkumulieren von Entscheidungsstrukturvorhersagen zu längeren Bewertungszeiten führt. |
| [Verstärkte Entscheidungsstruktur mit zwei Klassen](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Hervorragend |Moderat |Nein |6 |Hoher Speicherbedarf |
| [Neuronales Netz mit zwei Klassen](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |Gut |Moderat |Nein |8 | |
| [Gemitteltes Perzeptron mit zwei Klassen](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |Gut |Moderat |Ja |4 | |
| [Support Vector Machine mit zwei Klassen](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |Gut |Schnell |Ja |5 |Für große Merkmalssätze geeignet |
| [Logistische Regression mit mehreren Klassen](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |Gut |Schnell |Ja |4 | |
| [Entscheidungswald mit mehreren Klassen](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |Hervorragend |Moderat |Nein |5 |Bei diesem Algorithmus kommt es zu längeren Bewertungszeiten. |
| [Verstärkte Entscheidungsstruktur mit mehreren Klassen](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |Hervorragend |Moderat |Nein |6 | Verbessert tendenziell die Genauigkeit, allerdings mit einem geringen Risiko einer geringeren Abdeckung |
| [Neuronales Netz mit mehreren Klassen](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |Gut |Moderat |Nein |8 | |
| [One-vs-All-Klassifizierung mit mehreren Klassen](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |Siehe die Eigenschaften der ausgewählten zweiklassigen Methode |
| **Familie Regression** | | | | | |
| [Lineare Regression](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |Gut |Schnell |Ja |4 | |
| [Regression mit Entscheidungswald](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|Hervorragend |Moderat |Nein |5 | |
| [Regression mit verstärkter Entscheidungsstruktur](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |Hervorragend |Moderat |Nein |6 |Hoher Speicherbedarf |
| [Regression mit neuronalem Netz](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |Gut |Moderat |Nein |8 | |
| **Familie Clustering** | | | | | |
| [k-Means-Clustering](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |Hervorragend |Moderat |Ja |8 |Clusteringalgorithmus |

## <a name="requirements-for-a-data-science-scenario"></a>Anforderungen für ein Data Science-Szenario

Sobald Sie wissen, wie Sie mit Ihren Daten verfahren möchten, müssen Sie zusätzliche Anforderungen für Ihre Lösung festlegen. 

Treffen Sie Entscheidungen und möglicherweise Kompromisse für die folgenden Anforderungen:

- Genauigkeit
- Trainingsdauer
- Linearität
- Anzahl von Parametern
- Anzahl von Merkmalen

## <a name="accuracy"></a>Genauigkeit

Die Genauigkeit beim maschinellen Lernen misst die Wirksamkeit eines Modells als Anteil der tatsächlichen Ergebnisse an der Gesamtzahl der Fälle. Im Machine Learning-Designer berechnet das Modul [Evaluate Model](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) (Bewerten eines Modells) eine Reihe von branchenüblichen Auswertungsmetriken. Mithilfe dieses Moduls können Sie die Genauigkeit eines trainierten Modells messen.

Eine so genaue Antwort wie möglich zu erhalten, ist nicht immer erforderlich. Manchmal ist ein Näherungswert ausreichend, je nachdem, wofür Sie ihn verwenden möchten. Wenn dies der Fall ist, können Sie die Verarbeitungszeit möglicherweise erheblich verringern, indem Sie stärker Näherungsmethoden verwenden. Näherungsmethoden neigen natürlich auch dazu, eine Überanpassung zu vermeiden.

Es gibt drei Möglichkeiten zum Verwenden des Moduls „Evaluate Model“ (Bewerten des Modells):

- Generieren Sie Ergebnisse zu Ihren Trainingsdaten, um das Modell auszuwerten.
- Generieren Sie Ergebnisse für das Modell, vergleichen Sie diese aber mit Ergebnissen für ein reserviertes Testdataset.
- Vergleichen Sie die Ergebnisse für zwei verschiedene, aber zusammengehörige Modelle unter Verwendung desselben Datasets.

Eine vollständige Liste von Metriken und Ansätzen, die Sie zur Auswertung der Genauigkeit von Machine Learning-Modellen verwenden können, finden Sie unter dem Modul [Evaluate Model](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) (Bewerten des Modells).

## <a name="training-time"></a>Trainingsdauer

Beim beaufsichtigten Lernen bedeutet Training die Verwendung historischer Daten zur Erstellung eines Machine Learning-Modells, das Fehler minimiert. Die Anzahl der Minuten oder Stunden, die zum Trainieren eines Modells notwendig sind, variieren sehr stark von Algorithmus zu Algorithmus. Die Trainingsdauer ist oft eng an die Genauigkeit gebunden. Das eine geht in der Regel mit dem anderen einher. 

Darüber hinaus reagieren einige Algorithmen empfindlicher auf die Anzahl der Datenpunkte als andere. Aufgrund der vorliegenden zeitlichen Begrenzung könnten Sie einen bestimmten Algorithmus auswählen, insbesondere wenn das Dataset groß ist.

Im Machine Learning-Designer werden Machine Learning-Modelle in der Regel in einem dreistufigen Prozess erstellt und verwendet:

1.  Konfigurieren eines Modells, indem Sie einen bestimmten Algorithmustyp auswählen und dann seine Parameter oder Hyperparameter definieren. 

2.  Bereitstellen eines bezeichneten Datasets mit Daten, die mit dem Algorithmus kompatibel sind. Verbinden der Daten und des Modells mit dem Modul [Train Model](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri) (Trainieren des Modells).

3.  Nach Abschluss des Trainings verwenden Sie das trainierte Modell mit einem der [Bewertungsmodule](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri), um Vorhersagen für neue Daten zu treffen.

## <a name="linearity"></a>Linearität

Linearität in der Statistik und beim maschinellen Lernen bedeutet, dass es eine lineare Beziehung zwischen einer Variablen und einer Konstanten in Ihrem Dataset gibt. Lineare Klassifizierungsalgorithmen gehen z. B. davon aus, dass sich Klassen durch einer gerade Linie (oder deren höher dimensionale Entsprechung) trennen lassen.

Viele Algorithmen für maschinelles Lernen nutzen Linearität. Im Azure Machine Learning-Designer umfassen sie Folgendes: 

- [Logistische Regression mit mehreren Klassen](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Logistische Regression mit zwei Klassen](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [Support Vector Machines](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

Linear Regressionsalgorithmen gehen davon aus, dass Datentrends einer geraden Linie folgen. Diese Annahme ist für einige Probleme durchaus angemessen, aber für andere verringert sie die Genauigkeit. Trotz ihrer Nachteile sind lineare Algorithmen als erste Strategie beliebt. Sie sind tendenziell algorithmisch einfach und lassen sich schnell trainieren.

![Nicht lineare Klassengrenze](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

**_Nicht lineare Klassengrenze_* _: _Die Verwendung eines linearen Klassifizierungsalgorithmus würde zu einer verringerten Genauigkeit führen.*

![Daten mit einem nicht linearen Trend](./media/how-to-select-algorithms/nonlinear-trend.png)

***Daten mit einem nicht linearen Trend** _: _Die Verwendung einer linearen Regressionsmethode würde wesentlich höhere Fehler erzeugen als nötig.*

## <a name="number-of-parameters"></a>Anzahl von Parametern

Parameter sind die Knöpfe, an denen ein Datenanalyst drehen kann, wenn er einen Algorithmus einrichtet. Es handelt sich dabei um Zahlen, die das Verhalten des Algorithmus beeinflussen, z. B. Fehlertoleranz oder Anzahl von Iterationen oder Optionen zwischen Varianten der Verhaltensweisen des Algorithmus. Die Trainingsdauer und die Genauigkeit des Algorithmus können beim Finden der richtigen Einstellungen manchmal empfindlich reagieren. Normalerweise erfordern Algorithmen mit vielen Parametern die meisten Testversuche, um eine gute Kombination zu finden.

Alternativ gibt es das Modul [Tune Model Hyperparameters](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) (Abstimmen der Hyperparameter des Modells) im Machine Learning-Designer: Das Ziel dieses Moduls besteht darin, die optimalen Hyperparameter für ein Machine Learning-Modell zu ermitteln. Vom Modul werden mehrere Modelle erstellt und getestet, indem verschiedene Kombinationen von Einstellungen verwendet werden. Die Metriken werden über alle Modelle hinweg verglichen, um die Kombinationen der Einstellungen zu erhalten. 

Zwar stellt dies eine hervorragende Möglichkeit dar, um sicherzustellen, dass Sie den gesamten Parameterbereich abgedeckt haben, doch die Zeit, die zum Trainieren eines Modells erforderlich ist, steigt exponentiell mit der Anzahl der Parameter. Der Vorteil ist, dass das Vorhandensein vieler Parameter normalerweise darauf hindeutet, dass ein Algorithmus über größere Flexibilität verfügt. Er erreicht oft eine hervorragende Genauigkeit, vorausgesetzt, dass Sie die richtige Kombination von Parametereinstellungen finden.

## <a name="number-of-features"></a>Anzahl von Merkmalen

Beim maschinellen Lernen ist ein Merkmal eine quantifizierbare Variable des Phänomens, das Sie zu analysieren versuchen. Bei bestimmten Datentypen kann die Anzahl der Merkmale im Vergleich zur Anzahl der Datenpunkte sehr groß sein. Dies ist häufig der Fall bei Genetik- oder Textdaten. 

Eine große Anzahl von Merkmalen kann bei einigen Lernalgorithmen dazu führen, dass sie sich verzetteln, was impraktikabel lange Trainingsdauern nach sich zieht. [Support Vector Machines](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) eignen sich besonders gut für Szenarien mit einer hohen Anzahl von Merkmalen. Aus diesem Grund wurden sie in vielen Anwendungen (von der Informationsbeschaffung bis zur Text- und Bildklassifizierung) eingesetzt. Support Vector Machines können für sowohl Klassifizierungs-als auch Regressionsaufgaben verwendet werden.

„Feature Selection“ (Auswahl von Merkmalen) bezieht sich auf die Anwendung statistischer Tests auf Eingaben, wenn eine Ausgabe angegeben ist. Das Ziel besteht in der Bestimmung der Spalten, die für die Ausgabe aussagekräftiger sind. Das Modul [Filter Based Feature Selection](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) (Filterbasierte Merkmalauswahl) im Machine Learning-Designer stellt mehrere Algorithmen für die Merkmalauswahl bereit. Es beinhaltet Korrelationsmethoden wie Pearson-Korrelation und Chi-Quadrat-Werte.

Sie können auch das Modul [Permutation Feature Importance](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) (Permutationsmerkmalsgewichtung) verwenden, um einen Satz von Bewertungen der Merkmalsrelevanz für Ihr Dataset zu berechnen. Sie können dann diese Bewertungen nutzen, um die besten Merkmale zur Verwendung in einem Modell zu bestimmen.

## <a name="next-steps"></a>Nächste Schritte

 - [Weitere Informationen zum Azure Machine Learning-Designer](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - Beschreibungen aller im Azure Machine Learning-Designer verfügbaren Algorithmen für maschinelles Lernen finden Sie unter [Machine Learning-Designeralgorithmus- und Modulreferenz](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri)
 - Informationen zur Beziehung zwischen Deep Learning, maschinellem Lernen und KI finden Sie unter [Deep Learning im Vergleich zu maschinellem Lernen](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)