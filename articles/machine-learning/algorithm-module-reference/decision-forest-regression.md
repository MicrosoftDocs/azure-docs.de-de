---
title: 'Entscheidungswaldregression: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Modul „Entscheidungswaldregression“ in Azure Machine Learning verwenden, um auf der Grundlage eines Ensembles von Entscheidungsstrukturen ein Regressionsmodell zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 76fd09b5e2c2540cbc1608558800e7897a6cf839
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375357"
---
# <a name="decision-forest-regression-module"></a>Modul „Entscheidungswaldregression“

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um auf der Grundlage eines Ensembles von Entscheidungsstrukturen ein Regressionsmodell zu erstellen.

Nachdem Sie das Modell konfiguriert haben, müssen Sie es mit einem bezeichneten Dataset und dem Modul [Modell trainieren](./train-model.md) trainieren. Das trainierte Modell kann danach verwendet werden, um Vorhersagen zu treffen. 

## <a name="how-it-works"></a>Funktionsweise

Entscheidungsstrukturen sind nicht parametrische Modelle, die für jede Instanz eine Reihe einfacher Tests durchführen und dabei eine Binärbaum-Datenstruktur durchlaufen, bis ein Blattknoten (Entscheidung) erreicht wird.

Entscheidungsbäume haben diese Vorteile:

- Sie sind während des Trainings und der Vorhersage effizient sowohl bei der Berechnung als auch bei der Arbeitsspeicherauslastung.

- Sie können nicht lineare Entscheidungsgrenzen darstellen.

- Sie führen integrierte Merkmalsauswahl und Klassifizierung durch und sind in Gegenwart verrauschter Merkmale robust.

Dieses Regressionsmodell besteht aus einem Ensemble von Entscheidungsbäumen. Jeder Baum in einem Regressionsentscheidungswald gibt eine Gauß-Verteilung als Vorhersage aus. Es erfolgt eine Aggregation über das Baumensemble, um eine Gauß-Verteilung zu finden, die der kombinierten Verteilung für alle Bäume im Modell am nächsten kommt.

Weitere Informationen zum theoretischen Gerüst für diesen Algorithmus und seine Implementierung finden Sie in diesem Artikel: [Decision Forests: A Unified Framework for Classification, Regression, Density Estimation, Manifold Learning and Semi-Supervised Learning](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#) (Entscheidungswälder: Ein einheitliches Framework für Klassifikation, Regression, Dichteabschätzung, vielfältiges Lernen und teilüberwachtes Lernen)

## <a name="how-to-configure-decision-forest-regression-model"></a>Vorgehensweise: Konfigurieren des Entscheidungswald-Regressionsmodells

1. Fügen Sie der Pipeline das Modul **Entscheidungswaldregression** hinzu. Sie finden dieses Modul im Designer unter **Machine Learning**, **Initialize** (Initialisieren), **Regression**.

2. Öffnen Sie die Moduleigenschaften, und wählen Sie für **Resampling method** (Methode für Wiederholungsprobennahme) die Methode aus, mit der die einzelnen Bäume erstellt wurden.  Sie können zwischen **Bagging** und **Replikation** wählen.

    - **Bagging**: Bagging ist die Abkürzung von *Bootstrap aggregating*. Jeder Baum in einem Regressionsentscheidungswald gibt eine Gauß-Verteilung als Vorhersage aus. Aufgabe der Aggregation ist es, eine Gauß-Verteilung zu finden, deren erste zwei Momente mit den Momenten der Mischung der Gauß-Verteilungen übereinstimmen, die sich durch Kombination aller von den einzelnen Bäumen zurückgegebenen Verteilungen ergeben.

         Weitere Informationen finden Sie im Wikipedia-Eintrag zu [Bootstrap aggregating](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikation**: Bei der Replikation wird jede Struktur mit genau denselben Eingabedaten trainiert. Die Bestimmung, welches Aufteilungsprädikat für jeden Baumknoten verwendet wird, bleibt zufällig, und die entstehenden Bäume unterscheiden sich.

         Weitere Informationen zum Trainingsprozess mit der Option **Replikation** finden Sie in [Decision Forests for Computer Vision and Medical Image Analysis. Criminisi und J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/) (Entscheidungsbäume für maschinelles Sehen und medizinische Bildanalyse).

3. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.

    - **Single Parameter** (Einzelner Parameter):

      Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben. Möglicherweise haben Sie diese Werte durch Experimentieren ermittelt oder sie als Anleitung erhalten.

    - **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen. 



4. Geben Sie unter **Number of decision trees** (Anzahl der Entscheidungsbäume) die Gesamtzahl der Entscheidungsbäume an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie unter Umständen eine bessere Abdeckung, allerdings verlängert sich dadurch die Trainingsdauer.

    > [!TIP]
    > Wenn Sie den Wert auf 1 festlegen, wird allerdings nur eine einzelne Struktur (die Struktur mit dem anfänglichen Parametersatz) generiert, und es finden keine weiteren Iterationen statt.

5. Geben Sie in **Maximum depth of the decision trees** (Maximale Tiefe der Entscheidungsbäume) einen Wert ein, um die maximale Tiefe der Entscheidungsbäume zu begrenzen. Eine größere Tiefe des Baums kann die Genauigkeit erhöhen, wobei das Risiko einer Überanpassung und einer längeren Trainingsdauer besteht.

6. Geben Sie in **Number of random splits per node** (Anzahl der zufälligen Aufteilungen pro Knoten) die Anzahl der Aufteilungen ein, die beim Erstellen der einzelnen Knoten des Baums verwendet werden sollen. Eine *Aufteilung* bedeutet, dass Features auf jeder Ebene des Baums (Knotens) zufällig aufgeteilt werden.

7. Geben Sie unter **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) die Mindestanzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.

     Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.


9. Trainieren des Modells:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

   

10. Übermitteln Sie die Pipeline.

### <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie das Trainingsmodul aus, und wechseln Sie dann im rechten Bereich zur Registerkarte **Ausgaben**. Klicken Sie auf das Symbol für **Modell registrieren**.  Sie finden das gespeicherte Modell als Modul in der Modulstruktur. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 