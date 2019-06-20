---
title: 'Entscheidungswaldregression: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Entscheidungswaldregression“ in Azure Machine Learning Service verwenden, um auf der Grundlage eines Ensembles von Entscheidungsstrukturen ein Regressionsmodell zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65442364"
---
# <a name="decision-forest-regression-module"></a>Modul „Entscheidungswaldregression“

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um auf der Grundlage eines Ensembles von Entscheidungsstrukturen ein Regressionsmodell zu erstellen.

Nachdem Sie das Modell konfiguriert haben, müssen Sie es mit einem bezeichneten Dataset und dem Modul [Modell trainieren](./train-model.md) trainieren.  Das trainierte Modell kann danach verwendet werden, um Vorhersagen zu treffen. 

## <a name="how-it-works"></a>So funktioniert's

Entscheidungsstrukturen sind nicht parametrische Modelle, die für jede Instanz eine Reihe einfacher Tests durchführen und dabei eine Binärbaum-Datenstruktur durchlaufen, bis ein Blattknoten (Entscheidung) erreicht wird.

Entscheidungsbäume haben diese Vorteile:

- Sie sind während des Trainings und der Vorhersage effizient sowohl bei der Berechnung als auch bei der Arbeitsspeicherauslastung.

- Sie können nicht lineare Entscheidungsgrenzen darstellen.

- Sie führen integrierte Merkmalsauswahl und Klassifizierung durch und sind in Gegenwart verrauschter Merkmale robust.

Dieses Regressionsmodell besteht aus einem Ensemble von Entscheidungsbäumen. Jeder Baum in einem Regressionsentscheidungswald gibt eine Gauß-Verteilung als Vorhersage aus. Es erfolgt eine Aggregation über das Baumensemble, um eine Gauß-Verteilung zu finden, die der kombinierten Verteilung für alle Bäume im Modell am nächsten kommt.

Weitere Informationen zum theoretischen Gerüst für diesen Algorithmus und seine Implementierung finden Sie in diesem Artikel: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) (Entscheidungswälder: Ein einheitliches Framework für Klassifikation, Regression, Dichteabschätzung, vielfältiges Lernen und teilüberwachtes Lernen)

## <a name="how-to-configure-decision-forest-regression-model"></a>Vorgehensweise: Konfigurieren des Entscheidungswald-Regressionsmodells

1. Fügen Sie dem Experiment das Modul **Entscheidungswaldregression** hinzu. Sie finden dieses Modul auf der Oberfläche unter **Machine Learning**, **Initialize** (Initialisieren), **Regression**.

2. Öffnen Sie die Moduleigenschaften, und wählen Sie für **Resampling method** (Methode für Wiederholungsprobennahme) die Methode aus, mit der die einzelnen Bäume erstellt wurden.  Sie können zwischen **Bagging** und **Replikation** wählen.

    - **Bagging**: Bagging ist die Abkürzung von *Bootstrap aggregating*. Jeder Baum in einem Regressionsentscheidungswald gibt eine Gauß-Verteilung als Vorhersage aus. Aufgabe der Aggregation ist es, eine Gauß-Verteilung zu finden, deren erste zwei Momente mit den Momenten der Mischung der Gauß-Verteilungen übereinstimmen, die sich durch Kombination aller von den einzelnen Bäumen zurückgegebenen Gauß-Verteilungen ergeben.

         Weitere Informationen finden Sie im Wikipedia-Eintrag zu [Bootstrap aggregating](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikation**: Bei der Replikation wird jede Struktur mit genau denselben Eingabedaten trainiert. Die Bestimmung, welches Aufteilungsprädikat für jeden Baumknoten verwendet wird, bleibt zufällig, und die entstehenden Bäume unterscheiden sich.

         Weitere Informationen zum Trainingsprozess mit der Option **Replikation** finden Sie in [Decision Forests for Computer Vision and Medical Image Analysis. Criminisi und J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/) (Entscheidungsbäume für maschinelles Sehen und medizinische Bildanalyse).

3. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.

    - **Single Parameter** (Einzelner Parameter):

      Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben. Möglicherweise haben Sie diese Werte durch Experimentieren ermittelt oder sie als Anleitung erhalten.



4. Geben Sie unter **Number of decision trees** (Anzahl der Entscheidungsbäume) die Gesamtzahl der Entscheidungsbäume an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie unter Umständen eine bessere Abdeckung, allerdings verlängert sich dadurch die Trainingsdauer.

    > [!TIP]
    > Dieser Wert steuert auch die Anzahl von Bäumen, die bei der Visualisierung des trainierten Modells angezeigt werden. Wenn Sie einen einzelnen Baum anzeigen oder ausgeben möchten, können Sie den Wert auf 1 festlegen. Das bedeutet jedoch, dass nur ein Baum erzeugt wird (der Baum mit der anfänglichen Menge von Parametern) und keine weiteren Iterationen erfolgen.

5. Geben Sie in **Maximum depth of the decision trees** (Maximale Tiefe der Entscheidungsbäume) einen Wert ein, um die maximale Tiefe der Entscheidungsbäume zu begrenzen. Eine größere Tiefe des Baums kann die Genauigkeit erhöhen, wobei das Risiko einer Überanpassung und einer längeren Trainingsdauer besteht.

6. Geben Sie in **Number of random splits per node** (Anzahl der zufälligen Aufteilungen pro Knoten) die Anzahl der Aufteilungen ein, die beim Erstellen der einzelnen Knoten des Baums verwendet werden sollen. Eine *Aufteilung* bedeutet, dass Features auf jeder Ebene des Baums (Knotens) zufällig aufgeteilt werden.

7. Geben Sie unter **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) die Mindestanzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.

     Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.


9. Stellen Sie eine Verbindung mit einem bezeichneten Dataset her, wählen Sie eine einzelne Bezeichnungsspalte aus, die nicht mehr als zwei Ergebnisse enthält, und stellen Sie eine Verbindung mit [Train Model](./train-model.md) (Modell trainieren) her.

    - Wenn Sie die Option **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](./train-model.md) (Modell trainieren) zum Trainieren des Modells verwenden.

   

10. Führen Sie das Experiment aus.

### <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um den Baum anzuzeigen, der bei jeder Iteration erstellt wurde, klicken Sie mit der rechten Maustaste auf die Ausgabe des Trainingsmoduls, und wählen Sie **Visualize** (Visualisieren) aus.

+ Um die Regeln für jeden Knoten anzuzeigen, klicken Sie auf jeden Baum und führen Sie einen Drilldown in die Aufteilungen aus.

+ Um eine Momentaufnahme des trainierten Modells zu speichern, klicken Sie mit der rechten Maustaste auf die Ausgabe des Trainingsmoduls, und wählen Sie **Save As Trained Model** (Als trainiertes Modell speichern) aus. Diese Kopie des Modells wird bei nachfolgenden Ausführungen des Experiments nicht aktualisiert. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 