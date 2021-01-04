---
title: Auswerten der Ergebnisse von AutoML-Experimenten
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Diagramme und Metriken für jede Ausführung Ihrer Experimente des automatisierten maschinellen Lernens anzeigen und auswerten.
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2, automl
ms.openlocfilehash: fcbe0fc5049f6e892f80f048a885c75420bc636e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359084"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>Auswerten der Ergebnisse von Experimenten des automatisierten maschinellen Lernens

In diesem Artikel erfahren Sie, wie Sie die Ergebnisse Ihrer Experimente des automatisierten maschinellen Lernens (automatisiertes ML) anzeigen und auswerten. Diese Experimente bestehen aus mehreren Ausführungen. Bei jeder Ausführung wird ein Modell erstellt. Um Sie bei der Auswertung der Modelle zu unterstützen, generiert automatisiertes ML automatisch Leistungsmetriken und Diagramme für Ihren jeweiligen Experimenttyp. 

Automatisiertes ML stellt beispielsweise verschiedene Diagramme für Klassifizierungs- und Regressionsmodelle bereit. 

|Klassifizierung|Regression
|---|---|
|<li> [Konfusionsmatrix](#confusion-matrix) <li>[Genauigkeit-Trefferquote-Diagramm](#precision-recall-chart) <li> [ROC-Kurve (Receiver Operating Characteristics)](#roc) <li> [Prognosegütekurve](#lift-curve)<li> [Gewinnkurve](#gains-curve)<li> [Kalibrierungsdiagramm](#calibration-plot) | <li> [Vorhergesagt im Vergleich zu TRUE](#pvt) <li> [Residualhistogramm](#histo)|

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

* Erstellen Sie ein Experiment für Ihre Ausführung des automatisierten maschinellen Lernens, entweder mit dem SDK oder in Azure Machine Learning-Studio.

    * Verwenden Sie das SDK zum Erstellen eines [Klassifizierungsmodells](how-to-auto-train-remote.md) oder [Regressionsmodells](tutorial-auto-train-models.md)
    * Verwenden Sie [Azure Machine Learning-Studio](how-to-use-automated-ml-for-ml-models.md), um ein Klassifizierungs- oder Regressionsmodell zu erstellen, indem Sie die entsprechenden Daten hochladen.

## <a name="view-run-results"></a>Anzeigen von Ausführungsergebnissen

Nach dem Ausführen Ihres Experiments für automatisiertes maschinelles Lernen finden Sie über [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) einen Verlauf der Ausführungen in Ihrem Machine Learning-Arbeitsbereich. 

Bei SDK-Experimenten können Sie diese Ergebnisse auch während einer Ausführung anzeigen, wenn Sie das [Jupyter-Widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) `RunDetails` verwenden.

Die folgende Animation und die anschließenden Schritte zeigen, wie Sie den Ausführungsverlauf sowie die Leistungsmetriken und Diagramme eines bestimmten Modells im Studio anzeigen.

![Schritte zum Anzeigen des Ausführungsverlaufs und der Leistungsmetriken und Diagramme eines Modells](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

So zeigen Sie den Ausführungsverlauf und die Leistungsmetriken und Diagramme eines Modells im Studio an: 

1. [Melden Sie sich beim Studio an](https://ml.azure.com/), und navigieren Sie zu Ihrem Arbeitsbereich.
1. Wählen Sie links im Arbeitsbereich die Option **Ausführungen** aus.
1. Wählen Sie in der Liste mit den Experimenten den gewünschten Eintrag aus.
1. Wählen Sie in der unteren Tabelle die Option **Ausführen** aus.
1. Wählen Sie auf der Registerkarte **Modelle** den **Algorithmusnamen** für das Modell aus, das Sie untersuchen möchten.
1. Wählen Sie auf der Registerkarte **Metriken** die Metriken und Diagramme aus, die Sie für dieses Modell auswerten möchten. 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>Leistungsmetriken für Klassifizierungen

In der folgenden Tabelle sind die Modellleistungsmetriken zusammengefasst, die automatisiertes ML für jedes Klassifizierungsmodell berechnet, das für Ihr Experiment generiert wird. 

Metrik|BESCHREIBUNG|Berechnung|Zusätzliche Parameter
--|--|--|--
AUC_macro| AUC ist die Fläche unter der ROC-Kurve (Receiver Operating Characteristic Curve). Macro ist das arithmetische Mittel der AUC für jede Klasse.  | [Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_micro| AUC ist die Fläche unter der ROC-Kurve (Receiver Operating Characteristic Curve). „Micro“ wird global durch die Kombination der echt positiven und der falsch positiven Ergebnisse aus jeder Klasse berechnet.| [Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_weighted  | AUC ist die Fläche unter der ROC-Kurve (Receiver Operating Characteristic Curve). Der gewichtete Wert ist das arithmetische Mittel des Ergebnisses für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse.| [Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
accuracy|Die Genauigkeit ist der Prozentsatz der prognostizierten Bezeichnungen, die den TRUE-Bezeichnungen genau entsprechen. |[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |Keine|
average_precision_score_macro|Die durchschnittliche Genauigkeit fasst eine Precision-Recall-Kurve als gewichteten Mittelwert der bei jedem Schwellenwert erzielten Genauigkeiten zusammen, wobei die Zunahme beim Recall aus dem vorherigen Schwellenwert als Gewichtung verwendet wird. „Macro“ ist das arithmetische Mittel des durchschnittlichen Genauigkeitswerts jeder Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Die durchschnittliche Genauigkeit fasst eine Precision-Recall-Kurve als gewichteten Mittelwert der bei jedem Schwellenwert erzielten Genauigkeiten zusammen, wobei die Zunahme beim Recall aus dem vorherigen Schwellenwert als Gewichtung verwendet wird. „Micro“ wird global durch Kombinieren der echt positiven und der falsch positiven Ergebnisse bei jedem Cutoff berechnet.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Die durchschnittliche Genauigkeit fasst eine Precision-Recall-Kurve als gewichteten Mittelwert der bei jedem Schwellenwert erzielten Genauigkeiten zusammen, wobei die Zunahme beim Recall aus dem vorherigen Schwellenwert als Gewichtung verwendet wird. Der gewichtete Wert ist das arithmetische Mittel der durchschnittlichen Genauigkeit für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|„Balanced accuracy“ ist das arithmetische Mittel des Recalls für jede Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|„F1 score“ ist das harmonische Mittel aus Genauigkeit und Recall. „Macro“ ist das arithmetische Mittel des F1-Ergebnisses für jede Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|„F1 score“ ist das harmonische Mittel aus Genauigkeit und Recall. „Micro“ wird global durch Zählen der insgesamt echt positiven, falsch negativen und falsch positiven Ergebnisse berechnet.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|„F1 score“ ist das harmonische Mittel aus Genauigkeit und Recall. Gewichteter Mittelwert nach Klassenhäufigkeit des F1-Ergebnisses für jede Klasse|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|Dies ist die Verlustfunktion, die bei der (multinomialen) logistischen Regression und deren Erweiterungen wie z. B. neuronalen Netzen verwendet wird. Sie ist definiert als die negative Log-Wahrscheinlichkeit der True-Bezeichnungen bei den Vorhersagen eines probabilistischen Klassifizierers. Für eine einzelne Stichprobe mit der TRUE-Bezeichnung „yt“ in {0,1} und der geschätzten Wahrscheinlichkeit yp, dass yt = 1 lautet der logarithmische Verlust -log P(yt&#124;yp) = -(yt log(yp) + (1 - yt) log(1 - yp)).|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|Keine|
norm_macro_recall|Der normalisierte Makro-Recall wird normalisiert, damit die zufällige Leistung ein Ergebnis von 0 und die ideale Leistung einen Wert von 1 liefert. Dies kann erzielt werden durch norm_macro_recall := (recall_score_macro - R)/(1 - R), wobei R der erwartete Wert von recall_score_macro für zufällige Vorhersagen ist (d. h. R=0,5 für die binäre Klassifizierung und R=(1/C) für C-Klassen-Klassifizierungsprobleme).|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "macro" |
precision_score_macro|Die Genauigkeit ist der Prozentsatz der positiv vorhergesagten Elemente, die richtig bezeichnet sind. „Macro“ ist das arithmetische Mittel der Genauigkeit für jede Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|Die Genauigkeit ist der Prozentsatz der positiv vorhergesagten Elemente, die richtig bezeichnet sind. „Micro“ wird global durch Zählen der insgesamt echt positiven und falsch positiven Ergebnisse berechnet.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|Die Genauigkeit ist der Prozentsatz der positiv vorhergesagten Elemente, die richtig bezeichnet sind. Der gewichtete Wert ist das arithmetische Mittel der Genauigkeit für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|Der Abruf ist der prozentuale Anteil der richtig bezeichneten Elemente in einer bestimmten Klasse. „Macro“ ist das arithmetische Mittel des Recalls für jede Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|Der Abruf ist der prozentuale Anteil der richtig bezeichneten Elemente in einer bestimmten Klasse. „Micro“ wird global durch Zählen der insgesamt echt positiven, falsch negativen und falsch positiven Ergebnisse berechnet.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|Der Abruf ist der prozentuale Anteil der richtig bezeichneten Elemente in einer bestimmten Klasse. Der gewichtete Wert ist das arithmetische Mittel des Recalls für jede Klasse, gewichtet gemäß der Anzahl der TRUE-Instanzen in jeder Klasse.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|Die gewichtete Genauigkeit ist die Genauigkeit, bei der die Gewichtung, die jedem Beispiel zugewiesen wird, gleich dem Anteil der TRUE-Instanzen in der TRUE-Klasse dieses Beispiels ist.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight ist ein Vektor gleich dem Anteil der betreffenden Klasse für jedes Element im Ziel.|

### <a name="binary-vs-multiclass-metrics"></a>Gegenüberstellung von Binär- und Multiklassenmetriken

Automatisiertes ML unterscheidet nicht zwischen Binär- und Multiklassenmetriken. Für Datasets mit zwei Klassen werden die gleichen Validierungsmetriken gemeldet wie für Datasets mit mehr als zwei Klassen. Einige Metriken sind jedoch für die Klassifizierung mit mehreren Klassen vorgesehen. Wenn eine solche Metrik auf ein binäres Dataset angewendet wird, wird keine Klasse als `true` behandelt. Metriken, die eindeutig für mehrere Klassen vorgesehen sind, sind mit dem Suffix `micro`, `macro`oder `weighted` gekennzeichnet. Beispiele hierfür sind etwa `average_precision_score`, `f1_score`, `precision_score`, `recall_score` und `AUC`.

Anstatt beispielsweise den Abruf als `tp / (tp + fn)` zu berechnen, wird beim Durchschnittsabruf mit mehreren Klassen (`micro`, `macro` oder `weighted`) für beide Klassen eines Datasets mit Binärklassifizierung ein Durchschnitt gebildet. Dies entspricht der separaten Berechnung des Abrufs für die Klassen `true` und `false` und der anschließenden Bildung des Durchschnitts der beiden Klassen.

## <a name="confusion-matrix"></a>Konfusionsmatrix

Eine Konfusionsmatrix beschreibt die Leistung eines Klassifizierungsmodells. Jede Zeile zeigt die Instanzen der wahren bzw. tatsächlichen Klasse in Ihrem Dataset an, und jede Spalte stellt die Instanzen der Klasse dar, die vom Modell vorhergesagt wurde. 

Das automatisierte maschinelle Lernen zeigt für jede Konfusionsmatrix die Häufigkeit der einzelnen vorhergesagten Bezeichnungen (Spalte) im Vergleich mit der tatsächlichen Bezeichnung (Zeile) an. Je dunkler die Farbe, desto höher die Anzahl in diesem bestimmten Teil der Matrix. 

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?

Eine Konfusionsmatrix vergleicht den tatsächlichen Wert des Datasets mit den vorhergesagten Werten aus dem Modell. Aus diesem Grund haben Machine Learning-Modelle eine höhere Genauigkeit, wenn die meisten Werte des Modells entlang der Diagonalen liegen, was bedeutet, dass das Modell den richtigen Wert vorhergesagt hat. Wenn ein Modell ein Klassenungleichgewicht aufweist, hilft die Konfusionsmatrix dabei, ein verzerrtes Modell zu erkennen.

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>Beispiel 1: Ein Klassifizierungsmodell mit schlechter Genauigkeit.
![Ein Klassifizierungsmodell mit schlechter Genauigkeit.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>Beispiel 2: Ein Klassifizierungsmodell mit hoher Genauigkeit. 
![Ein Klassifizierungsmodell mit hoher Genauigkeit.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>Beispiel 3: Ein Klassifizierungsmodell mit hoher Genauigkeit und hoher Verzerrung bei Modellvorhersagen.
![Ein Klassifizierungsmodell mit hoher Genauigkeit und hoher Verzerrung bei Modellvorhersagen.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>Genauigkeit-Trefferquote-Diagramm

Die Genauigkeit-Trefferquote-Kurve zeigt die Beziehung zwischen Genauigkeit und Trefferquote eines Modells. Der Begriff „Genauigkeit“ steht für die Fähigkeit eines Modells, alle Instanzen richtig zu bezeichnen. „Trefferquote“ stellt die Möglichkeit für einen Klassifizierer dar, alle Instanzen einer bestimmten Bezeichnung zu finden.

Mit diesem Diagramm können Sie die Genauigkeit-Trefferquote-Kurven für jedes Modell vergleichen, um festzustellen, welches Modell eine akzeptable Beziehung zwischen Genauigkeit und Trefferquote für Ihr spezielles Geschäftsproblem aufweist. Dieses Diagramm zeigt die durchschnittliche Makro-Genauigkeit-Trefferquote, die durchschnittliche Mikro-Genauigkeit-Trefferquote und die Genauigkeit-Trefferquote, die allen Klassen für ein Modell zugeordnet ist. 

Der **Makrodurchschnitt** berechnet die Metrik unabhängig von allen Klassen und gibt dann den Durchschnitt aus, sodass alle Klassen gleichbehandelt werden. Der **Mikrodurchschnitt** aggregiert jedoch die Beiträge aller Klassen, um den Durchschnitt zu berechnen. Der Mikrodurchschnitt ist vorzuziehen, wenn im Dataset ein Klassenungleichgewicht vorhanden ist.

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?
Abhängig vom Ziel des Geschäftsproblems kann sich die ideale Genauigkeit-Trefferquote-Kurve unterscheiden. 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>Beispiel 1: Ein Klassifizierungsmodell mit niedriger Genauigkeit und niedriger Trefferquote.
![Ein Klassifizierungsmodell mit niedriger Genauigkeit und niedriger Trefferquote.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>Beispiel 2: Ein Klassifizierungsmodell mit ~100 % Genauigkeit und ~100 % Trefferquote. 
![Ein Klassifizierungsmodell mit hoher Genauigkeit und hoher Trefferquote.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>ROC-Diagramm

ROC (Receiver Operating Characteristic) ist ein Diagramm der korrekt klassifizierten Bezeichnungen im Vergleich zu den falsch klassifizierten Bezeichnungen für ein bestimmtes Modell. Die ROC-Kurve kann weniger aussagekräftig sein, wenn Modelle auf der Grundlage von Datasets mit stark unausgeglichenen Klassen trainiert werden, da der Beitrag von Minderheitsklassen ggf. durch die Mehrheitsklasse verdrängt wird.

Der Bereich unter der ROC-Kurve kann als Anteil korrekt klassifizierter Stichproben visualisiert werden. Ein erfahrener Betrachter des ROC-Diagramms kann ggf. über den Bereich unter der Kurve hinausblicken und ein Gefühl für die True Positive- und False Positive-Raten als Funktion des Klassifizierungsschwellenwerts oder der Entscheidungsgrenze bekommen.

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?
Eine ROC-Kurve, die sich der linken oberen Ecke mit einer True Positive-Rate von 100 Prozent und einer False Positive-Rate von 0 Prozent annähert, ist das beste Modell. Ein Zufallsmodell wird als gerade Linie dargestellt, die von links unten nach rechts oben verläuft. Schlechter als zufällig wäre, wenn die Linie unter die y=x-Linie fällt.

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>Beispiel 1: Ein Klassifizierungsmodell mit niedrigen wahren Bezeichnungen und hohen falschen Bezeichnungen.
![Ein Klassifizierungsmodell mit niedrigen wahren Bezeichnungen und hohen falschen Bezeichnungen.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>Beispiel 2: Ein Klassifizierungsmodell mit hohen wahren Bezeichnungen und niedrigen falschen Bezeichnungen.

![Ein Klassifizierungsmodell mit hohen wahren Bezeichnungen und niedrigen falschen Bezeichnungen.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>Prognosegütediagramm

Prognosegütediagramme werten die Leistung von Klassifizierungsmodellen aus. Ein Prognosegütediagramm zeigt, wie viel besser ein Modell im Vergleich zu einem Zufallsmodell abschneidet. Dadurch erhalten Sie eine relative Leistungsangabe, bei der die Tatsache berücksichtigt wird, dass die Klassifizierung mit zunehmender Klassenanzahl schwieriger wird. Ein Zufallsmodell sagt bei einem Dataset mit zehn Klassen einen höheren Anteil von Stichproben falsch vorher als bei einem Dataset mit zwei Klassen.

Sie können die Prognosegüte des automatisch mit Azure Machine Learning erstellten Modells mit der Baseline (Zufallsmodell) vergleichen, um den Wertzuwachs dieses bestimmten Modells anzuzeigen.

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?

Ein Modell mit besserer Leistung weist im Graph eine höhere Prognosegütekurve auf, die weiter von der Grundlinie entfernt ist. 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>Beispiel 1: Ein Klassifizierungsmodell, das schlechter abschneidet als ein Zufallsauswahlmodell.
![Ein Klassifizierungsmodell, das schlechter abschneidet als ein Zufallsauswahlmodell.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>Beispiel 2: Ein Klassifizierungsmodell, das besser abschneidet als ein Zufallsauswahlmodell.
![Ein Klassifizierungsmodell, das besser abschneidet.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>Kumuliertes Gewinndiagramm

Ein kumuliertes Gewinndiagramm wertet die Leistung eines Klassifizierungsmodells anhand jedes Teils der Daten aus. Das Diagramm zeigt für jedes Perzentil des Datasets, wie viele weitere Stichproben im Vergleich zu einem Modell, das immer falsch ist, korrekt klassifiziert wurden. Diese Informationen bieten eine weitere Möglichkeit, die Ergebnisse im zugehörigen Prognosegütediagramm zu untersuchen.

Das Diagramm der kumulierten Gewinne erleichtert die Auswahl der Klassifizierungsgrenze anhand eines Prozentsatzes, der einem gewünschten Gewinn aus dem Modell entspricht. Sie können das Diagramm der kumulierten Gewinne mit der Baseline (Modell mit falschen Ergebnissen) vergleichen, um zu sehen, wie viel Prozent der Stichprobe in jedem Konfidenzperzentil korrekt klassifiziert wurden.

#### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?

Ähnlich wie bei einem Prognosegütediagramm gilt: Je höher die Kurve der kumulativen Gewinne über der Baseline liegt, desto besser funktioniert Ihr Modell. Darüber hinaus gilt: Je mehr sich die Kurve der kumulativen Gewinne der linken oberen Ecke des Diagramms nähert, desto mehr Gewinn erzielt Ihr Modell im Vergleich zur Baseline. 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>Beispiel 1: Ein Klassifizierungsmodell mit minimalem Gewinn.
![Ein Klassifizierungsmodell mit minimalem Gewinn.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>Beispiel 2: Ein Klassifizierungsmodell mit signifikantem Gewinn.
![Ein Klassifizierungsmodell mit signifikantem Gewinn.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>Kalibrierungsdiagramm

Ein Kalibrierungsdiagramm zeigt die Konfidenz eines Vorhersagemodells an. Dies geschieht, indem es die Beziehung zwischen der vorhergesagten Wahrscheinlichkeit und der tatsächlichen Wahrscheinlichkeit zeigt, wobei die „Wahrscheinlichkeit“ die Wahrscheinlichkeit darstellt, dass eine bestimmte Instanz zu einer bestimmten Bezeichnung gehört.

Für alle Klassifizierungsprobleme können Sie die Kalibrierungskurve auf Mikrodurchschnitt, Makrodurchschnitt und jede Klasse in einem bestimmten Vorhersagemodell überprüfen.

Der **Makrodurchschnitt** berechnet die Metrik unabhängig von allen Klassen und gibt dann den Durchschnitt aus, sodass alle Klassen gleichbehandelt werden. Der **Mikrodurchschnitt** aggregiert jedoch die Beiträge aller Klassen, um den Durchschnitt zu berechnen. 

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?
Ein gut kalibriertes Modell ist an der y=x-Linie ausgerichtet, wo es die Wahrscheinlichkeit der Zugehörigkeit von Stichproben zu den einzelnen Klassen korrekt vorhersagt. Bei einem übermäßig zuverlässigen Modell werden Wahrscheinlichkeiten übermäßig nah bei Null bzw. Eins vorhergesagt, sodass hinsichtlich der Klasse der jeweiligen Stichproben selten eine Unsicherheit besteht.

#### <a name="example-1-a-well-calibrated-model"></a>Beispiel 1: Ein gut kalibriertes Modell.
![ Ein besser kalibriertes Modell.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>Beispiel 2: Ein übermäßig zuverlässiges Modell.
![Ein übermäßig zuverlässiges Modell.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>Leistungsmetriken für Regression

In der folgenden Tabelle sind die Modellleistungsmetriken zusammengefasst, die automatisiertes ML für jedes Regressions- oder Vorhersagemodell berechnet, das für Ihr Experiment generiert wird. 

|Metrik|BESCHREIBUNG|Berechnung|Zusätzliche Parameter
--|--|--|--|
explained_variance|Die erläuterte Varianz ist der Anteil, mit dem ein mathematisches Modell für die Variation eines bestimmten Datasets verantwortlich ist. Hierbei handelt es sich um den prozentualen Rückgang der Varianz der ursprünglichen Daten im Vergleich zur Varianz der Fehler. Wenn der Mittelwert der Fehler 0 beträgt, entspricht er dem Ermittlungskoeffizienten (siehe r2_score weiter unten).|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|Keine|
r2_score|R^2 ist der Ermittlungskoeffizient bzw. die prozentuale Reduzierung der quadratischen Fehler im Vergleich zu einem Baselinemodell, das den Mittelwert ausgibt. |[Berechnung](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|Keine|
spearman_correlation|Die Spearman-Korrelation ist ein nicht parametrisches Maß für die Monotonie der Beziehung zwischen zwei Datasets. Im Gegensatz zur Pearson-Korrelation geht die Spearman-Korrelation nicht davon aus, dass beide Datasets normal verteilt sind. Wie bei anderen Korrelationskoeffizienten variiert dieser Wert zwischen -1 und +1, wobei 0 für keine Korrelation steht. Korrelationen von -1 oder +1 implizieren eine exakt monotone Beziehung. Positive Korrelationen implizieren, dass sich x ebenso wie y erhöht. Negative Korrelationen implizieren, dass sich x erhöht und y niedriger wird.|[Berechnung](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|Keine|
mean_absolute_error|Der mittlere absolute Fehler ist der erwartete Wert des absoluten Differenzwerts zwischen dem Ziel und der Vorhersage.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Keine|
normalized_mean_absolute_error|Der normalisierte mittlere absolute Fehler ist der mittlere absolute Fehler, dividiert durch den Datenbereich.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|Division durch den Datenbereich|
median_absolute_error|Der mittlere absolute Fehler ist der Median aller absoluten Differenzen zwischen dem Ziel und der Vorhersage. Dieser Verlust ist stabil in Bezug auf Ausreißer.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Keine|
normalized_median_absolute_error|Der normalisierte mittlere absolute Fehler ist der mittlere absolute Fehler, dividiert durch den Datenbereich.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|Division durch den Datenbereich|
root_mean_squared_error|Die Wurzel aus dem mittleren quadratischen Fehler ist die Quadratwurzel der erwarteten quadratischen Differenz zwischen dem Ziel und der Vorhersage.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Keine|
normalized_root_mean_squared_error|Die normalisierte Wurzel aus dem mittleren quadratischen Fehler ist die Wurzel aus dem mittleren quadratischen Fehler, dividiert durch den Datenbereich.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|Division durch den Datenbereich|
root_mean_squared_log_error|Die Wurzel aus dem mittleren quadratischen logarithmischen Fehler ist die Quadratwurzel des erwarteten quadratischen logarithmischen Fehlers.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Keine|
normalized_root_mean_squared_log_error|Die normalisierte Wurzel aus dem mittleren quadratischen logarithmischen Fehler ist die Wurzel aus dem mittleren quadratischen logarithmischen Fehler dividiert durch den Datenbereich.|[Berechnung](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|Division durch den Datenbereich|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a> Vorhergesagt im Vergleich zu Wahr-Diagramm

Vorhergesagt im Vergleich zu TRUE zeigt die Beziehung zwischen einem vorhergesagten Wert und seinem korrelierenden wahren Wert für ein Regressionsproblem. 

Nach jeder Ausführung wird für jedes Regressionsmodell ein Diagramm mit den vorhergesagten im Vergleich zu den wahren Werten angezeigt. Zum Schutz der Privatsphäre werden die Werte in Behältern zusammengeführt, und die Größe jedes Behälters wird als Balkendiagramm im unteren Teil des Diagrammbereichs angezeigt. Sie können das Vorhersagemodell (mit dem helleren Farbbereich, der die Fehlergrenzen anzeigt) mit dem Idealwert des Modells vergleichen.

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?
Mit diesem Graphen kann die Leistung eines Modells gemessen werden, da Folgendes gilt: Je näher die vorhergesagten Werte an der y=x-Linie liegen, desto besser ist die Leistung eines Vorhersagemodells.

#### <a name="example-1-a-regression-model-with-low-performance"></a>Beispiel 1: Ein Regressionsmodell mit niedriger Leistung
![Ein Regressionsmodell mit niedriger Genauigkeit bei Vorhersagen.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-performance"></a>Beispiel 2: Ein Regressionsmodell mit hoher Leistung
![Ein Regressionsmodell mit hoher Genauigkeit bei seinen Vorhersagen.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a> Histogramm der Residualwerte

Das automatisierte maschinelle Lernen stellt automatisch ein Residualwertediagramm bereit, um die Fehlerverteilung in den Vorhersagen eines Regressionsmodells zu zeigen. Ein Residualwert ist die Differenz zwischen der Vorhersage und dem tatsächlichen Wert (`y_pred - y_true`). 

### <a name="what-does-a-good-model-look-like"></a>Wie sieht ein gutes Modell aus?
Um eine Fehlerspanne mit geringer Verzerrung darzustellen, sollte das Histogramm der Residualwerte als Glockenkurve geformt sein, die um 0 zentriert ist.

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>Beispiel 1: Ein Regressionsmodell mit Verzerrung bei seinen Fehlern.
![Ein Regressionsmodell mit Verzerrung bei seinen Fehlern.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-a-more-even-distribution-of-errors"></a>Beispiel 2: Ein Regressionsmodell mit gleichmäßigerer Verteilung von Fehlern
![Ein Regressionsmodell mit gleichmäßigerer Verteilung von Fehlern.](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a> Interpretierbarkeit von Modellen und Featurepriorität
Automatisiertes ML bietet ein Dashboard für die Machine Learning-Interpretierbarkeit für Ihre Ausführungen.

Weitere Informationen zum Aktivieren von Interpretierbarkeitsfeatures finden Sie unter [Interpretierbarkeit: Modellerklärungen beim automatisierten maschinellen Lernen](how-to-machine-learning-interpretability-automl.md).

> [!NOTE]
> Das ForecastTCN-Modell wird aktuell vom Erklärungsclient nicht unterstützt. Dieses Modell gibt kein Erklärungsdashboard zurück, wenn es als bestes Modell zurückgegeben wird, und es unterstützt keine Erklärungsausführungen auf Anforderung.

## <a name="next-steps"></a>Nächste Schritte

+ Erfahren Sie mehr über [automatisiertes Machine Learning](concept-automated-ml.md) in Azure Machine Learning.
+ Probieren Sie die Beispielnotebooks für die [Modellerklärung zum automatisierten Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) aus.