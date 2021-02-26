---
title: 'ML Studio (Classic): Auswertung und Cross-Validate Models – Azure'
description: Hier erhalten Sie weitere Informationen zu den Metriken, die Sie verwenden können, um die Modellleistung in Azure Machine Learning Studio (Classic) zu überwachen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: b2ca78d30659fce6e4246c81216cae94b404955e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520016"
---
# <a name="evaluate-model-performance-in-azure-machine-learning-studio-classic"></a>Auswerten der Modellleistung in Azure Machine Learning Studio (Classic)

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Hier erhalten Sie weitere Informationen zu den Metriken, die Sie verwenden können, um die Modellleistung in Azure Machine Learning Studio (Classic) zu überwachen.  Die Auswertung der Leistung eines Modells ist eine der wesentlichen Phasen im Datenanalyseprozess. Sie gibt an, wie erfolgreich die Bewertung (die Vorhersagen) eines Datasets eines trainierten Modells war. In Azure Machine Learning Studio (klassisch) wird die Auswertung von Modellen durch zwei der wichtigsten Machine Learning-Module unterstützt: 
+ [Auswertungsmodell][evaluate-model] 
+ [Cross-Validate Model][cross-validate-model]

Mit diesen Modulen können Sie die Leistung Ihres Modells im Hinblick auf verschiedene Kennzahlen anzeigen, die beim maschinellen Lernen und in der Statistik häufig verwendet werden.

Beim Auswerten von Modellen sollte außerdem Folgendes berücksichtigt werden:
+ [Auswählen von Parametern zur Optimierung von Algorithmen in Azure Machine Learning Studio (klassisch)](algorithm-parameters-optimize.md)
+ [Interpretieren von Modellergebnissen in Azure Machine Learning Studio (klassisch)](interpret-model-results.md)

Es werden drei häufig eingesetzte Szenarios des beaufsichtigten Lernens vorgestellt: 
* Regression
* Binäre Klassifizierung 
* Multiklassenklassifizierung


## <a name="evaluation-vs-cross-validation"></a>Auswertung und Kreuzvalidierung im Vergleich
Die Auswertung und die Kreuzvalidierung sind Standardmethoden zum Messen der Leistung von Modellen. Bei beiden Methoden werden Auswertungskennzahlen generiert, die mit denen anderer Modelle geprüft oder verglichen werden können.

Bei [Evaluate Model][evaluate-model] wird ein bewertetes Dataset als Eingabe erwartet (oder zwei Datasets, wenn Sie die Leistung zweier verschiedener Modelle vergleichen möchten). Daher müssen Sie Ihr Modell mit dem Modul [Train Model][train-model] trainieren und mit dem Modul [Score Model][score-model] Vorhersagen für ein Dataset treffen, bevor Sie die Ergebnisse auswerten können. Die Auswertung basiert auf den bewerteten Bezeichnern/Wahrscheinlichkeiten sowie auf den tatsächlichen Bezeichnern, die alle vom Modul [Score Model][score-model] ausgegeben werden.

Alternativ können Sie mithilfe der Kreuzvalidierung automatisch mehrere Trainings-/Bewertungs-/Auswertungsvorgänge (Aufteilung in 10 Teildatensätze) für verschiedene Teilmengen der Eingabedaten durchführen. Die Eingabedaten werden in 10 Teilmengen aufgeteilt, wobei eine Teilmenge zum Testen und die anderen 9 zum Trainieren vorgesehen sind. Dieser Vorgang wird 10-mal wiederholt, und die Auswertungskennzahlen werden gemittelt. Damit lässt sich feststellen, wie gut ein Modell neue Datasets verallgemeinert. Das Modul [Cross Validate Model][cross-validate-model] akzeptiert ein untrainiertes Modell und ein Dataset mit Bezeichnern und gibt die Auswertungsergebnisse aller zehn Aufteilungen sowie die gemittelten Ergebnisse aus.

In den folgenden Abschnitten werden einfache Regressions- und Klassifizierungsmodelle erstellt, und deren Leistung wird evaluiert. Dazu werden die beiden Module [Evaluate Model][evaluate-model] und [Cross-Validate Model][cross-validate-model] verwendet.

## <a name="evaluating-a-regression-model"></a>Auswerten eines Regressionsmodells
Angenommen, wir möchten den Preis eines Fahrzeugs anhand von Eigenschaften wie Abmessungen, PS, Motorisierung usw. vorhersagen. Dies ist ein typischer Regressionsfall, bei dem die Zielvariable (*price*) ein beständiger Zahlenwert ist. Dazu kann ein lineares Regressionsmodell angepasst werden, das mit den Eigenschaftenwerten eines bestimmten Fahrzeugs den Preis des Fahrzeugs vorhersagen kann. Mit diesem Regressionsmodell kann das gleiche Dataset bewertet werden, das für das Training verwendet wurde. Wenn die vorhergesagten Fahrzeugpreise vorliegen, kann die Modellleistung ausgewertet werden, indem wir uns ansehen, inwieweit die Vorhersagen im Durchschnitt von den tatsächlichen Preisen abweichen. Zur Veranschaulichung verwenden wir das im Abschnitt **Saved Datasets** (gespeicherte Datasets) in Azure Machine Learning Studio (klassisch) bereitgestellte Dataset *Automobile price data (Raw)* (Fahrzeugpreisdaten (Rohdaten)).

### <a name="creating-the-experiment"></a>Erstellen des Experiments
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio (klassisch) die folgenden Module hinzu:

* Automobile price data (Raw)
* [Lineare Regression][linear-regression]
* [Train Model][train-model] (Modell trainieren)
* [Score Model][score-model] (Modell bewerten)
* [Auswertungsmodell][evaluate-model]

Verbinden Sie die Ports wie in Abbildung 1 dargestellt, und legen Sie für die Spalte „Label“ des Moduls [Train Model][train-model] den Wert *price* fest.

![Auswerten eines Regressionsmodells](./media/evaluate-model-performance/1.png)

Abbildung 1. Auswerten eines Regressionsmodells.

### <a name="inspecting-the-evaluation-results"></a>Überprüfen der Auswertungsergebnisse
Nach dem Ausführen des Experiments können Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model] klicken und *Visualize* auswählen, um die Auswertungsergebnisse anzuzeigen. Für Regressionsmodelle stehen folgende Auswertungskennzahlen zur Verfügung: *Mean Absolute Error*, *Root Mean Absolute Error*, *Relative Absolute Error*, *Relative Squared Error* und *Coefficient of Determination*.

Der Begriff "Fehler" gibt hier die Differenz zwischen dem vorhergesagten und dem tatsächlichen Wert an. Normalerweise wird der absolute Wert oder das Quadrat dieser Differenz berechnet, um die gesamte Fehlerabweichung in allen Fällen zu erfassen, da die Differenz zwischen dem vorhergesagten und dem tatsächlichen Wert in einigen Fällen auch negativ sein kann. Die Fehlerkennzahlen messen die Vorhersageleistung eines Regressionsmodells im Hinblick auf die mittlere Abweichung seiner Vorhersagen von den tatsächlichen Werten. Kleinere Fehlerwerte bedeuten, dass das Modell bei Vorhersagen genauer ist. Die Fehlerkennzahl null bedeutet, dass das Modell perfekt mit den Daten übereinstimmt.

Das Bestimmtheitsmaß, das auch als "R-Quadrat" notiert wird, ist ebenfalls eine Standardmethode für die Berechnung, wie gut das Vorhersagemodell mit den Daten übereinstimmt. Es kann als der Anteil der Abweichung interpretiert werden, der durch das Modell beschrieben wird. In diesem Fall ist ein höherer Anteil besser, wobei "1" eine perfekte Übereinstimmung angibt.

![Auswertungskennzahlen bei der linearen Regression.](./media/evaluate-model-performance/2.png)

Abbildung 2. Auswertungskennzahlen bei der linearen Regression.

### <a name="using-cross-validation"></a>Verwenden der Kreuzvalidierung
Wie bereits erwähnt, können Sie mit dem Modul [Cross-Validate Model][cross-validate-model] automatisch wiederholte Trainings-, Bewertungs- und Auswertungsvorgänge durchführen. In diesem Fall benötigen Sie lediglich ein Dataset, ein untrainiertes Modell und das Modul [Cross-Validate Model][cross-validate-model] (siehe Abbildung unten). Sie müssen in den Eigenschaften des Moduls [Cross-Validate Model][cross-validate-model] für die Spalte „label“ den Wert *price* festlegen.

![Kreuzvalidierung eines Regressionsmodells.](./media/evaluate-model-performance/3.png)

Abbildung 3. Kreuzvalidierung eines Regressionsmodells.

Nach dem Ausführen des Experiments können Sie die Auswertungsergebnisse prüfen, indem Sie auf den rechten Ausgabeport des Moduls [Cross-Validate Model][cross-validate-model] klicken. Dadurch werden eine Detailansicht der Kennzahlen für jede Iteration (Aufteilung) und die gemittelten Ergebnisse aller Kennzahlen angezeigt (Abbildung 4).

![Ergebnisse der Kreuzvalidierung eines Regressionsmodells.](./media/evaluate-model-performance/4.png)

Abbildung 4. Ergebnisse der Kreuzvalidierung eines Regressionsmodells.

## <a name="evaluating-a-binary-classification-model"></a>Auswerten eines binären Klassifizierungsmodells
Bei der binären Klassifizierung hat die Zielvariable nur zwei mögliche Ergebnisse, z. B.: {0, 1} oder {falsch, wahrt}, {negativ, positiv}. Angenommen, Sie erhalten ein Dataset mit Mitarbeiterdaten, das verschiedene Variablen zu Demografie und Beschäftigung enthält, und Sie werden gebeten, das Einkommensniveau (eine binäre Variable mit den Werten {„<=50 K“, „>50 K“}) vorherzusagen. Anders gesagt: Die negative Klasse gibt die Mitarbeiter an, deren Einkommen pro Jahr kleiner oder gleich 50 K (50.000) ist, und die positive Klasse alle anderen Mitarbeiter. Wie beim Regressionsszenario werden ein Modell trainiert, einige Daten bewertet und die Ergebnisse ausgewertet. Der Hauptunterschied besteht hier in der Auswahl der Metriken, die in Azure Machine Learning Studio (klassisch) berechnet und ausgegeben werden. Zur Veranschaulichung des Vorhersageszenarios für das Einkommensniveau wird mit dem Dataset [Adult](https://archive.ics.uci.edu/ml/datasets/Adult) ein (klassisches) Studio-Experiment erstellt und anschließend die Leistung eines logistischen Zwei-Klassen-Regressionsmodells ausgewertet, einem häufig eingesetzten binärer Klassifikator.

### <a name="creating-the-experiment"></a>Erstellen des Experiments
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio (klassisch) die folgenden Module hinzu:

* Dataset "Adult Census Income Binary Classification"
* [Logistische Regression mit zwei Klassen][two-class-logistic-regression]
* [Train Model][train-model] (Modell trainieren)
* [Score Model][score-model] (Modell bewerten)
* [Auswertungsmodell][evaluate-model]

Verbinden Sie die Ports wie in Abbildung 5 dargestellt, und legen Sie für die Spalte „Label“ des Moduls [Train Model][train-model] den Wert *income* fest.

![Auswerten eines binären Klassifizierungsmodells](./media/evaluate-model-performance/5.png)

Abbildung 5. Auswerten eines binären Klassifizierungsmodells.

### <a name="inspecting-the-evaluation-results"></a>Überprüfen der Auswertungsergebnisse
Nach dem Ausführen des Experiments können Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model] klicken und *Visualize* auswählen, um die Auswertungsergebnisse anzuzeigen (Abbildung 7). Für binäre Klassifikationsmodelle stehen folgende Auswertungskennzahlen zur Verfügung: *Accuracy*, *Precision*, *Recall*, *F1 Score* und *AUC*. Darüber hinaus gibt das Modul eine Wahrheitsmatrix mit der Anzahl der Fälle „richtig positiv“, „falsch negativ“, „falsch positiv“ und „richtig negativ“ sowie den Kurven *ROC*, *Precision/Recall* und *Lift* aus.

Genauigkeit (Accuracy) ist dabei der Anteil der richtig klassifizierten Fälle. Sie ist normalerweise die erste Kennzahl, die Sie sich bei der Auswertung eines Klassifikators ansehen. Wenn die Testdaten jedoch unausgeglichen sind (also die meisten Fälle einer einzelnen Klasse angehören) oder Sie sich überwiegend für eine einzelne Klasse interessieren, wird mit der Genauigkeit die Effektivität eines Klassifikators nicht wirklich erfasst. Angenommen, beim Klassifizierungsszenario für das Einkommensniveau testen Sie Daten, bei denen 99 % der Fälle Mitarbeiter angeben, deren Einkommen pro Jahr kleiner oder gleich 50K ist. Bei der Vorhersage der Klasse „<=50.000“ für alle Fälle kann eine Genauigkeit von 0,99 erreicht werden. Der Klassifikator scheint in diesem Fall eine durchweg solide Leistung zu liefern, in Wirklichkeit werden aber alle Personen mit höherem Einkommen (die 1 %) nicht richtig klassifiziert.

Aus diesem Grund ist es nützlich, weitere Kennzahlen zu berechnen, die die spezifischeren Aspekte der Auswertung erfassen. Bevor die Einzelheiten dieser Kennzahlen besprochen werden, ist es wichtig, die Wahrheitsmatrix der Auswertung einer binären Klassifizierung zu verstehen. Die Klassen in den Trainingsdaten können nur zwei mögliche Werte annehmen, die normalerweise positiv oder negativ ausfallen. Die positiven und negativen Fälle, die ein Klassifikator richtig vorhersagt, werden als "richtig positiv" (RP) bzw. "richtig negativ" (RN) bezeichnet. Dementsprechend werden die falsch klassifizierten Fälle als "falsch positiv" (FP) und "falsch negativ" (FN) bezeichnet. Bei der Wahrheitsmatrix handelt es sich um eine tabellarische Aufstellung der Anzahl der Fälle, die sich in eine dieser vier Kategorien einordnen lassen. In Azure Machine Learning Studio (klassisch) wird automatisch festgelegt, welche der beiden Klassen im Dataset als positive Klasse eingeordnet wird. Wenn es sich bei den Klassenwerten um boolesche Werte oder ganze Zahlen handelt, werden die mit „Richtig“ oder „1“ kategorisierten Fälle der positiven Klasse zugewiesen. Wenn die Bezeichner wie beim Dataset zur Einkommenserhebung Zeichenfolgen sind, werden sie alphabetisch sortiert. Die erste Ebene wird als negative Klasse eingestuft und die zweite Ebene als positive Klasse.

![Wahrheitsmatrix der binären Klassifizierung.](./media/evaluate-model-performance/6a.png)

Abbildung 6. Wahrheitsmatrix der binären Klassifizierung.

Im Hinblick auf die Klassifizierung des Einkommens in unserem Beispiel sollen mehrere Auswertungsfragen gestellt werden, mit denen die Leistung des verwendeten Klassifikators bewertet werden kann. Eine nahe liegende Frage ist: „Wie viele der Personen, für die im Modell vorhergesagt wurde, dass ihr Einkommen mehr als 50.000 beträgt (RP+FP), wurden richtig klassifiziert (RP)?“ Diese Frage kann durch Prüfen der Genauigkeit (**Precision**) des Modells beantwortet werden, die den Anteil der positiven Fälle angibt, die richtig klassifiziert wurden: RP/(RP+FP). Eine weitere Frage lautet: „Wie viele aller besserverdienenden Mitarbeiter mit einem Einkommen von mehr als 50.000 (RP+FN) wurden mit dem Klassifikator richtig klassifiziert (RP)?“ Dies ist die Sensitivität (**Recall**) oder die Richtig-Positiv-Rate des Klassifikators: RP/(RP+FN). Sie können feststellen, dass ein offensichtlicher Zusammenhang zwischen Präzision und Sensitivität besteht. Ein relativ ausgeglichenes Dataset vorausgesetzt, weist ein Klassifikator, der überwiegend positive Fälle vorhersagt, eine hohe Sensitivität, jedoch eine eher geringe Präzision auf, da viele der negativen Fälle falsch klassifiziert werden, was zu einer hohen Anzahl falsch-negativer Fälle führt. Um grafisch darzustellen, wie diese beiden Metriken variieren, können Sie in der Ausgabeseite der Auswertungsergebnisse auf die Kurve **PRECISION/RECALL** klicken (links oben in Abbildung 7).

![Auswertungsergebnisse der binären Klassifizierung.](./media/evaluate-model-performance/7.png)

Abbildung 7. Auswertungsergebnisse der binären Klassifizierung.

Eine weitere zugehörige, häufig verwendete Kennzahl ist das sogenannte F-Maß (**F1 Score**), das sowohl Genauigkeit als auch Sensitivität berücksichtigt. Es handelt sich um das harmonische Mittel dieser beiden Metriken, das wie folgt berechnet wird: F1 = 2 (Genauigkeit x Sensitivität) / (Genauigkeit + Sensitivität). Der F1-Score ist eine gute Methode, um die Auswertung in einer einzelnen Zahl zusammenzufassen. Es empfiehlt sich jedoch auch immer, die Präzision und Sensitivität zusammen zu betrachten, um das Verhalten eines Klassifikators besser einschätzen zu können.

Darüber hinaus können Sie mit der Grenzwertoptimierungskurve (**Receiver Operating Characteristic, ROC**) und dem entsprechenden Wert für die Fläche unter der Kurve (**Area Under the Curve, AUC**) die Richtig-Positiv-Rate im Vergleich zur Falsch-Positiv-Rate überprüfen. Je stärker sich diese Kurve der linken oberen Ecke annähert, desto besser ist die Leistung des Klassifikators (d. h., die Richtig-Positiv-Rate wird maximiert, während die Falsch-Positiv-Rate minimiert wird). Kurven, die sich der Diagonale in der Darstellung nähern, sind auf Klassifikatoren zurückzuführen, deren Vorhersagen auf bloßes Raten hinauslaufen.

### <a name="using-cross-validation"></a>Verwenden der Kreuzvalidierung
Wie im Regressionsbeispiel kann die Kreuzvalidierung durchgeführt werden, um verschiedene Teilmengen der Daten automatisch mehrmals zu trainieren, zu bewerten und auszuwerten. In ähnlicher Weise können das Modul [Cross-Validate Model][cross-validate-model], ein untrainiertes logistisches Regressionsmodell und ein Dataset verwendet werden. Für die Beschriftungsspalte muss in den Eigenschaften des Moduls [Cross-Validate Model][cross-validate-model] der Wert *income* festgelegt werden. Nach Ausführen des Experiments und Klicken auf den rechten Ausgabeport des Moduls [Cross-Validate Model][cross-validate-model] werden die Metrikwerte der binären Klassifizierung sowie der Mittelwert und die Standardabweichung für jede Aufteilung angezeigt. 

![Kreuzvalidierung eines binären Klassifizierungsmodells.](./media/evaluate-model-performance/8.png)

Abbildung 8. Kreuzvalidierung eines binären Klassifizierungsmodells.

![Ergebnisse der Kreuzvalidierung eines binären Klassifikators.](./media/evaluate-model-performance/9.png)

Abbildung 9. Ergebnisse der Kreuzvalidierung eines binären Klassifikators.

## <a name="evaluating-a-multiclass-classification-model"></a>Auswerten eines Modells für die Multiklassenklassifizierung
In diesem Experiment wird das beliebte Dataset [Iris](https://archive.ics.uci.edu/ml/datasets/Iris "Iris") verwendet, das Fälle der drei verschiedenen Typen (Klassen) der Irispflanze enthält. Für jeden Fall sind vier Funktionswerte (Länge/Breite des Kelchblatts und Länge/Breite des Blütenblatts) definiert. In den vorhergehenden Experimenten wurden die Modelle mit den gleichen Datasets trainiert und getestet. Hier werden nun mithilfe des Moduls [Split Data][split] zwei Teilmengen der Daten erstellt. Das Modell wird mit der ersten Teilmenge trainiert und anschließend mit der zweiten Teilmenge bewertet und ausgewertet. Das Iris-Dataset wird im [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/index.html) öffentlich zur Verfügung gestellt und kann mit einem [Import Data][import-data]-Modul heruntergeladen werden.

### <a name="creating-the-experiment"></a>Erstellen des Experiments
Fügen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio (klassisch) die folgenden Module hinzu:

* [Daten importieren][import-data]
* [Entscheidungswald mit mehreren Klassen][multiclass-decision-forest]
* [Aufteilen von Daten][split]
* [Train Model][train-model] (Modell trainieren)
* [Score Model][score-model] (Modell bewerten)
* [Auswertungsmodell][evaluate-model]

Verbinden Sie die Ports wie unten in Abbildung 10 dargestellt.

Legen Sie den Index der Spalte „Label“ des Moduls [Train Model][train-model] auf „5“ fest. Das Dataset verfügt über keinen Header, wir wissen jedoch, dass sich die Klassenbezeichner in der fünften Spalte befinden.

Klicken Sie auf das Modul [Import Data][import-data], und legen Sie die Eigenschaft *Data Source* auf *Web URL via HTTP* und die *URL* auf http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data fest.

Legen Sie im Modul [Split Data][split] den Anteil der Fälle fest, die für das Training verwendet werden sollen (beispielsweise 0,7).

![Auswerten eines Multiklassenklassifikators](./media/evaluate-model-performance/10.png)

Abbildung 10. Auswerten eines Multiklassenklassifikators

### <a name="inspecting-the-evaluation-results"></a>Überprüfen der Auswertungsergebnisse
Führen Sie das Experiment aus, und klicken Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model]. Die Auswertungsergebnisse werden in diesem Beispiel in Form einer Wahrheitsmatrix angezeigt. In der Matrix sind die tatsächlichen und vorhergesagten Fälle für alle drei Klassen aufgeführt.

![Auswertungsergebnisse der Multiklassenklassifizierung.](./media/evaluate-model-performance/11.png)

Abbildung 11. Auswertungsergebnisse der Multiklassenklassifizierung.

### <a name="using-cross-validation"></a>Verwenden der Kreuzvalidierung
Wie bereits erwähnt, können Sie mit dem Modul [Cross-Validate Model][cross-validate-model] automatisch wiederholte Trainings-, Bewertungs- und Auswertungsvorgänge durchführen. Sie benötigen ein Dataset, ein untrainiertes Modell und das Modul [Cross-Validate Model][cross-validate-model] (siehe Abbildung unten). Auch in diesem Szenario muss wieder die Spalte „Label“ des Moduls [Cross-Validate Model][cross-validate-model] festgelegt werden (Spaltenindex „5“ in diesem Fall). Nach Ausführen des Experiments und Klicken auf den rechten Ausgabeport des Moduls [Cross-Validate Model][cross-validate-model] können Sie die Metrikwerte sowie den Mittelwert und die Standardabweichung für die einzelnen Aufteilungen überprüfen. Die hier aufgeführten Kennzahlen sind denen sehr ähnlich, die zuvor im Beispiel für die binäre Klassifizierung erörtert wurden. Bei der Multiklassenklassifizierung erfolgt die Berechnung der Fälle "Richtig positiv/negativ" und "Falsch positiv/negativ" jedoch klassenspezifisch, da es keine allgemeine positive oder negative Klasse gibt. Wenn beispielsweise die Präzision oder die Sensitivität der Klasse „Iris-setosa“ berechnet wird, wird davon ausgegangen, dass es sich dabei um die positive Klasse handelt und dass alle anderen Klassen negativ sind.

![Kreuzvalidierung eines Modells für die Multiklassenklassifizierung.](./media/evaluate-model-performance/12.png)

Abbildung 12: Kreuzvalidierung eines Modells für die Multiklassenklassifizierung

![Ergebnisse der Kreuzvalidierung eines Modells für die Multiklassenklassifizierung.](./media/evaluate-model-performance/13.png)

Abbildung 13: Ergebnisse der Kreuzvalidierung eines Modells für die Multiklassenklassifizierung.

<!-- Module References -->
[cross-validate-model]: /azure/machine-learning/studio-module-reference/cross-validate-model
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[multiclass-decision-forest]: /azure/machine-learning/studio-module-reference/multiclass-decision-forest
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-logistic-regression]: /azure/machine-learning/studio-module-reference/two-class-logistic-regression