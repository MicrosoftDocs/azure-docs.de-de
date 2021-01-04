---
title: 'ML Studio (Classic) – Tutorial 2: Trainieren von Kreditrisikomodellen – Azure'
description: Ein ausführliches Tutorial zum Erstellen einer Predictive Analytics-Lösung für die Kreditrisikobewertung in Azure Machine Learning Studio (klassisch). Dieses Tutorial ist der zweite Teil einer dreiteiligen Reihe. Es zeigt, wie Sie Modelle trainieren und auswerten.
keywords: Kreditrisiko, Predictive Analytics-Lösung, Risikobewertung
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 557c624b6ed683f701b6a8d38854cd8604e05b31
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96325263"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Tutorial 2: Trainieren von Kreditrisikomodellen – Azure Machine Learning Studio (klassisch)

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic).](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Dieses Tutorial befasst sich eingehend mit der Entwicklung einer Predictive Analytics-Lösung. Hierzu wird in Machine Learning Studio (klassisch) ein einfaches Modell entwickelt.  Anschließend wird das Modell als Azure Machine Learning-Webdienst bereitgestellt.  Dieses bereitgestellte Modell kann auf der Grundlage neuer Daten Vorhersagen generieren. Dieses Tutorial ist der **zweite Teil einer dreiteiligen Reihe**.

Stellen Sie sich vor, Sie müssen das Kreditrisiko von Personen anhand der Daten auf einem Kreditantrag vorhersagen.  

Die Bewertung des Kreditrisikos ist allerdings ein komplexes Problem und wurde daher in diesem Tutorial etwas vereinfacht. Diese Aufgabenstellung dient als Beispiel dafür, wie Sie eine Predictive Analytics-Lösung mit Microsoft Azure Machine Learning Studio (klassisch) erstellen können. Für diese Lösung werden Azure Machine Learning Studio (klassisch) und ein Machine Learning-Webdienst verwendet.  

In diesem dreiteiligen Tutorial werden zunächst öffentlich verfügbare Kreditrisikodaten verwendet.  Als Nächstes entwickeln und trainieren Sie ein Vorhersagemodell.  Abschließend stellen Sie das Modell als Webdienst bereit.

Im [ersten Teil des Tutorials](tutorial-part1-credit-risk.md) haben Sie einen Arbeitsbereich in Machine Learning Studio (klassisch) erstellt, Daten hochgeladen und ein Experiment erstellt.

In diesem Teil des Tutorials führen Sie die folgenden Schritte aus:
 
> [!div class="checklist"]
> * Trainieren mehrerer Modelle
> * Bewerten und Auswerten der Modelle


Im [dritten Teil des Tutorials](tutorial-part3-credit-risk-deploy.md) stellen Sie das Modell als Webdienst bereit.

## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie den [ersten Teil des Tutorials](tutorial-part1-credit-risk.md) ab.

## <a name="train-multiple-models"></a><a name="train"></a>Trainieren mehrerer Modelle

Einer der Vorteile von Azure Machine Learning Studio (klassisch) zum Erstellen von Machine Learning-Modellen ist die Möglichkeit, mehr als einen Typ von Modell gleichzeitig in einem einzelnen Experiment zu testen und die Ergebnisse zu vergleichen. Dieser Typ von Experiment hilft Ihnen, die beste Lösung für Ihr Problem zu finden.

In dem Experiment, das wir in diesem Tutorial entwickeln, werden zwei verschiedene Modelltypen erstellt und anschließend deren Bewertungsergebnisse verglichen, um zu entscheiden, welcher Algorithmus im endgültigen Experiment verwendet werden soll.  

Es stehen verschiedene Modelle zur Auswahl. Um die verfügbaren Modelle anzuzeigen, erweitern Sie den Knoten **Machine Learning** in der Modulpalette, und erweitern Sie dann **Initialize Model** und die darunter liegenden Knoten. Für dieses Experiment werden die Module [Two-Class Support Vector Machine][two-class-support-vector-machine] (SVM) und [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] verwendet.

> [!TIP]
> Hilfe für die Entscheidung, welcher Machine Learning-Algorithmus für das Lösen des jeweiligen Problems am besten geeignet ist, finden Sie unter [Auswählen von Algorithmen für Microsoft Azure Machine Learning Studio (klassisch)](../how-to-select-algorithms.md).
> 
> 

Diesem Experiment werden sowohl das Modul [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] als auch das Modul [Two-Class Support Vector Machine][two-class-support-vector-machine] hinzugefügt.

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree

Zuerst wird das Boosted Decision Tree-Modell eingerichtet.

1. Suchen Sie das Modul [Two-Class Boosted Decision Trees][two-class-boosted-decision-tree] in der Modulpalette, und ziehen Sie es in den Experimentbereich.

1. Suchen Sie das Modul [Train Model][train-model], ziehen Sie es in den Bereich, und verbinden Sie die Ausgabe des Moduls [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] mit dem linken Eingabeport des Moduls [Train Model][train-model].
   
   Das Modul [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] initialisiert das allgemeine Modell. Das Modul [Train Model][train-model] nutzt Trainingsdaten zum Trainieren des Modells. 

1. Verbinden Sie die linke Ausgabe des linken Moduls [Execute R Script][execute-r-script] mit dem rechten Eingabeport des Moduls [Train Model][train-model]. (In diesem Tutorial haben Sie die [Daten von der linken Seite](#train) des Moduls „Split Data“ für das Training verwendet.)
   
   > [!TIP]
   > Zwei der Eingaben und eine der Ausgaben des Moduls [Execute R Script][execute-r-script] werden für dieses Experiment nicht benötigt und daher nicht angefügt. 
   > 
   > 

Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:  

![Trainieren eines Modells](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Nun müssen Sie dem Modul [Train Model][train-model] mitteilen, dass das Modell den Wert des Kreditrisikos vorhersagen soll.

1. Wählen Sie das Modul [Train Model][train-model] (Modell trainieren) aus. Klicken Sie im Bereich **Properties** auf **Launch column selector**.

1. Geben Sie im Dialogfeld **Select a single column** in das Suchfeld unter **Available Columns** „Credit risk“ ein. Wählen Sie unten „Credit risk“ aus, und klicken Sie auf den nach rechts zeigenden Pfeil ( **>** ), um „Credit risk“ in **Selected Columns** zu verschieben. 

    ![Wählen Sie die Spalte „Credit Risk“ des Moduls „Train Model“ aus](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Klicken Sie auf das Häkchen **OK**.

### <a name="two-class-support-vector-machine"></a>Two-Class Support Vector Machine

Als Nächstes wird das SVM-Modell eingerichtet.  

Zunächst soll SVM ein wenig erläutert werden. Boosted Decision Trees funktionieren hervorragend mit allen Arten von Merkmalen. Da das SVM-Modul jedoch einen linearen Klassifikator generiert, hat das entsprechende generierte Modell den besten Testfehler, wenn alle numerischen Merkmale dieselbe Skala verwenden. Um alle numerischen Features zur gleichen Skala zu konvertieren, verwenden Sie das Modul [Normalize Data][normalize-data] mit einer Tanh-Transformation. Diese transformiert unsere Zahlen in den Bereich [0,1]. Zeichenfolgenfeatures werden vom SVM-Modul in kategorische Features und anschließend in binäre 0/1-Features konvertiert. Daher müssen Zeichenfolgenfeatures nicht manuell transformiert werden. Zudem darf die Spalte „Credit Risk“ (Spalte 21) nicht transformiert werden. Diese ist zwar numerisch, da es sich dabei aber um den Wert handelt, für dessen Vorhersage das Modell trainiert wird, darf er nicht verändert werden.  

Um das SVM-Modell einzurichten, führen Sie folgende Schritte aus:

1. Suchen Sie das Modul [Two-Class Support Vector Machine][two-class-support-vector-machine] in der Modulpalette, und ziehen Sie es in den Experimentbereich.

1. Klicken Sie mit der rechten Maustaste auf das Modul [Train Model][train-model], und wählen Sie **Copy** (Kopieren) aus. Klicken Sie anschließend mit der rechten Maustaste auf die Canvas, und wählen Sie **Paste** (Einfügen) aus. Beachten Sie, dass die Kopie des Moduls [Train Model][train-model] die gleiche Spaltenauswahl wie das Original hat.

1. Verbinden Sie die Ausgabe des Moduls [Two-Class Support Vector Machine][two-class-support-vector-machine] mit dem linken Eingabeport des zweiten Moduls [Train Model][train-model].

1. Suchen Sie das Modul [Normalize Data][normalize-data], und ziehen Sie es in den Experimentbereich.

1. Verbinden Sie die Eingabe dieses Moduls mit der linken Ausgabe des linken Moduls [Execute R Script][execute-r-script] (beachten Sie, dass der Ausgabeport eines Moduls mit mehr als einem anderen Modul verbunden sein kann).

1. Verbinden Sie den linken Ausgabeport des Moduls [Normalize Data][normalize-data] mit dem rechten Eingabeport des Moduls [Train Model][train-model].

Dieser Teil des Experiments sieht jetzt in etwa wie folgt aus:  

![Trainieren des zweiten Modells](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Konfigurieren Sie jetzt das Modul [Normalize Data][normalize-data]:

1. Klicken Sie, um das Modul [Normalize Data][normalize-data] auszuwählen. Wählen Sie im Bereich **Properties** den Wert **Tanh** für den Parameter **Transformation method** aus.

1. Klicken Sie auf **Launch column selector**, wählen Sie für **Begin With** „No columns“, und wählen Sie in der ersten Dropdownliste **Include**, in der zweiten Dropdownliste **column type** und in der dritten Dropdownliste **Numeric** aus. Damit wird festgelegt, dass alle numerischen Spalten (und nur die numerischen Spalten) transformiert werden.

1. Klicken Sie auf das Pluszeichen (+) rechts neben dieser Zeile. Dadurch wird eine neue Zeile mit Dropdownlisten erstellt. Wählen Sie in der ersten Dropdownliste **Exclude** aus. Wählen Sie in der zweiten Dropdownliste **column names** aus, und geben Sie „Credit risk“ in das Textfeld ein. Dadurch wird angegeben, dass die Spalte „Credit Risk“ ignoriert werden soll. (Dies ist erforderlich, da diese Spalte numerisch ist und transformiert wird, wenn Sie sie nicht ausschließen.)

1. Klicken Sie auf das Häkchen **OK**.  

    ![Wählen von Spalten für das Modul „Normalize Data“](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Das Modul [Normalize Data][normalize-data] ist jetzt für eine Tanh-Transformation aller numerischen Spalten mit Ausnahme der Spalte „Credit Risk“ (Kreditrisiko) eingerichtet.  

## <a name="score-and-evaluate-the-models"></a>Bewerten und Auswerten der Modelle

Sie verwenden die Testdaten, die durch das Modul [Split Data][split] getrennt wurden, um die trainierten Modelle zu bewerten. Danach können die Ergebnisse der beiden Modelle verglichen werden, um zu ermitteln, welches Modell die besseren Ergebnisse liefert.  

### <a name="add-the-score-model-modules"></a>Hinzufügen der „Score Model“-Module

1. Suchen Sie das Modul [Score Model][score-model], und ziehen Sie es in den Bereich.

1. Verbinden Sie das Modul [Train Model][train-model], das mit dem Modul [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] verbunden ist, mit dem linken Eingabeport des Moduls [Score Model][score-model].

1. Verbinden Sie das rechte Modul [Execute R Script][execute-r-script] (unsere Testdaten) mit dem rechten Eingabeport des Moduls [Score Model][score-model].

    ![Hergestellte Verbindung mit dem Modul „Score Model“](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Das Modul [Score Model][score-model] kann nun die Kreditinformationen aus den Testdaten entnehmen, sie durch das Modell laufen lassen und vom Modell generierte Vorhersagen mit der Spalte mit dem tatsächlichen Kreditrisiko in den Testdaten vergleichen.

1. Kopieren Sie das Modul [Score Model][score-model], und fügen Sie es ein, um eine zweite Kopie zu erstellen.

1. Verbinden Sie die Ausgabe des SVM-Modells (d. h. den Ausgabeport des Moduls [Train Model][train-model], der mit dem Modul [Two-Class Support Vector Machine][two-class-support-vector-machine] verbunden ist) mit dem Eingabeport des zweiten Moduls [Score Model][score-model].

1. Für das SVM-Modell muss die gleiche Transformation für die Testdaten durchgeführt werden wie für die Trainingsdaten. Kopieren Sie also das Modul [Normalize Data][normalize-data], und fügen Sie es ein, um eine zweite Kopie zu erstellen, und verbinden Sie es mit dem rechten Modul [Execute R Script][execute-r-script].

1. Verbinden Sie die linke Ausgabe des zweiten Moduls [Normalize Data][normalize-data] mit dem rechten Eingabeport des zweiten Moduls [Score Model][score-model].

    ![Hergestellte Verbindung mit beiden „Score Model“-Modulen](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Hinzufügen des Moduls „Evaluate Model“

Zum Evaluieren und Vergleichen der beiden Bewertungsergebnisse wird das Modul [Evaluate Model][evaluate-model] verwendet.  

1. Suchen Sie das Modul [Evaluate Model][evaluate-model], und ziehen Sie es in den Bereich.

1. Verbinden Sie den Ausgabeport des Moduls [Score Model][score-model], der dem Modell „Boosted Decision Tree“ zugeordnet ist, mit dem linken Eingabeport des Moduls [Evaluate Model][evaluate-model].

1. Verbinden Sie das andere Modul [Score Model][score-model] mit dem rechten Eingabeport.  

    ![Hergestellte Verbindung mit dem Modul „Evaluate Model“](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Ausführen des Experiments und Überprüfen der Ergebnisse

Klicken Sie auf die Schaltfläche **RUN** unter dem Bereich, um das Experiment auszuführen. Dies kann einige Minuten dauern. Für jedes Modul wird ein Drehzeiger angezeigt, um anzugeben, dass es ausgeführt wird. Wenn das Modul abgeschlossen ist, wird ein grünes Häkchen angezeigt. Wenn alle Module ein Häkchen aufweisen, ist die Ausführung des Experiments beendet.

Das Experiment sollte in etwa wie folgt aussehen:  

![Evaluieren beider Modelle](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Um die Ergebnisse zu prüfen, klicken Sie auf den Ausgabeport des Moduls [Evaluate Model][evaluate-model], und wählen Sie **Visualize** aus.  

Das Modul [Evaluate Model][evaluate-model] erzeugt ein Paar aus Kurven und Metriken, mit denen die Ergebnisse der beiden bewerteten Modelle verglichen werden können. Sie können die Ergebnisse als ROC-Kurven (Receiver Operator Characteristic), Genauigkeits-/Trefferkurven oder Prognosegütekurven anzeigen. Zu den zusätzlich angezeigten Daten zählen eine Wahrheitsmatrix, kumulative Werte für den Bereich unter der Kurve (AUC) sowie weitere Metriken. Sie können den Schwellwert ändern, indem Sie den Schieberegler nach links oder rechts bewegen und beobachten, wie sich dies auf den Metriksatz auswirkt.  

Klicken Sie rechts neben dem Graph auf **Scored dataset** oder auf **Scored dataset to compare**, um die zugeordnete Kurve zu markieren und die zugeordneten Metriken darunter anzuzeigen. In der Legende für die Kurven entspricht „Bewertetes Dataset“ dem linken Eingabeport des Moduls [Evaluate Model][evaluate-model] – in unserem Fall ist dies das Boosted Decision Tree-Modell. "Scored dataset to compare" entspricht dem rechten Eingabeport – in unserem Fall dem SVM-Modell. Wenn Sie auf eine dieser Beschriftungen klicken, werden die Kurve für das betreffende Modell markiert und die entsprechenden Metriken darunter angezeigt (siehe die folgende Abbildung).  

![ROC-Kurven für Modelle](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Wenn Sie diese Werte prüfen, können Sie entscheiden, welches Modell am ehesten den gewünschten Ergebnissen entspricht. Sie können zurückgehen und das Experiment erneut ausführen, indem Sie Parameterwerte in den verschiedenen Modellen ändern. 

Die wissenschaftliche Vorgehensweise und Kunst der Interpretation dieser Ergebnisse und das Optimieren der Modellleistung werden in diesem Tutorial nicht behandelt. Weitere Hilfe erhalten Sie in den folgenden Artikeln:
- [Auswerten der Modellleistung in Azure Machine Learning Studio (klassisch)](evaluate-model-performance.md)
- [Auswählen von Parametern zur Optimierung von Algorithmen in Azure Machine Learning Studio (klassisch)](algorithm-parameters-optimize.md)
- [Interpretieren von Modellergebnissen in Azure Machine Learning Studio (klassisch)](interpret-model-results.md)

> [!TIP]
> Jedes Mal, wenn Sie das Experiment ausführen, wird im Ausführungsverlauf ein Dataset für diese Iteration aufbewahrt. Sie können die Iterationen anzeigen und zu jeder von ihnen zurückkehren, indem Sie unter dem Bereich auf **VIEW RUN HISTORY** klicken. Sie können auch im Fenster **Properties** auf **Prior Run** klicken, um zur Iteration unmittelbar vor der geöffneten Iteration zurückzukehren.
> 
> Sie können eine Kopie jeder Iteration des Experiments anfertigen, indem Sie unter dem Bereich auf **SAVE AS** klicken. 
> Verwenden Sie die Eigenschaften **Summary** und **Description** des Experiments, um nachzuhalten, was Sie in den Iterationen Ihres Experiments versucht haben.
> 
> Weitere Informationen finden Sie unter [Verwalten von Experimentiterationen in Azure Machine Learning-Studio (klassisch)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 
 
> [!div class="checklist"]
> * Erstellen eines Experiments
> * Trainieren mehrerer Modelle
> * Bewerten und Auswerten der Modelle

Als Nächstes können Sie Modelle für diese Daten bereitstellen.

> [!div class="nextstepaction"]
> [Tutorial 3 – Bereitstellen von Modellen](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/