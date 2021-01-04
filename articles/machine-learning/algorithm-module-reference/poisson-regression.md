---
title: 'Poisson-Regression: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Poisson Regression-Modul im Azure Machine Learning-Designer ein Poisson-Regressionsmodell erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/13/2020
ms.openlocfilehash: 2dfd8b3d919f9eeb3e183135ef543f417c878977
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420698"
---
# <a name="poisson-regression"></a>Poisson-Regression

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Erstellen Sie mit diesem Modul ein lineares Poisson-Regressionsmodell in einer Pipeline. Die Poisson-Regression ist für die Vorhersage numerischer Werte, in der Regel Anzahlen, gedacht. Daher sollten Sie dieses Modul nur dann verwenden, um das Regressionsmodell zu erstellen, wenn die Werte, die Sie vorhersagen möchten, folgende Bedingungen erfüllen:

- Die Antwortvariable hat eine [Poisson-Verteilung](https://en.wikipedia.org/wiki/Poisson_distribution).  

- Die Anzahlen dürfen nicht negativ sein. Bei der Methode tritt direkt ein Fehler auf, wenn Sie versuchen, sie mit negativen Bezeichnungen zu verwenden.

- Eine Poisson-Verteilung ist eine gleichmäßige Verteilung, darum ist es nicht sinnvoll, diese Methode mit Zahlen zu verwenden, die keine Ganzzahlen sind.

> [!TIP]
> Wenn der Zielwert keine Anzahl ist, ist die Poisson-Regression wahrscheinlich keine geeignete Methode. Probieren Sie [andere Regressionsmodule im Designer](./module-reference.md#machine-learning-algorithms) aus. 

Nachdem Sie die Regressionsmethode eingerichtet haben, müssen Sie das Modell mithilfe eines Datasets trainieren, das Beispiele für den Wert enthält, den Sie vorhersagen möchten. Das trainierte Modell kann danach verwendet werden, um Vorhersagen zu treffen.

## <a name="more-about-poisson-regression"></a>Weitere Informationen zur Poisson-Regression

Eine Poisson-Regression ist ein spezieller Typ von Regressionsanalyse, der üblicherweise dazu verwendet wird, Anzahlen zu modellieren. Beispielsweise bietet sich eine Poisson-Regression für die folgenden Szenarien an:

- Modellieren der Anzahl von Erkältungen, die mit Flugreisen in Zusammenhang stehen

- Schätzen der Anzahl von Notrufen während eines Ereignisses

- Projizieren der Anzahl von Kundenanfragen nach einer Werbekampagne

- Erstellen von Kontingenztabellen

Da die Antwortvariable eine Poisson-Verteilung aufweist, trifft das Modell andere Annahmen über die Daten und ihre Wahrscheinlichkeitsverteilung als beispielsweise die Regression mit der Methode der kleinsten Quadrate. Daher sollten Poisson-Modelle anders als andere Regressionsmodelle interpretiert werden.

## <a name="how-to-configure-poisson-regression"></a>So konfigurieren Sie eine Poisson-Regression

1. Fügen Sie in Designer das Modul **Poisson Regression** Ihrer Pipeline hinzu. Sie finden dieses Modul unter den **Machine Learning-Algorithmen** in der Kategorie **Regression**.

2. Fügen Sie ein Dataset hinzu, das Trainingsdaten des richtigen Typs enthält. 

    Sie sollten [Normalize Data](normalize-data.md) (Daten normalisieren) verwenden, um das Eingabedataset zu normalisieren, bevor Sie damit den Regressor trainieren.

3. Geben Sie im rechten Bereich des Moduls **Poisson Regression** an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) festlegen.  
  
    - **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, geben Sie eine bestimmte Menge von Werten als Argumente an.
  
    - **Parameter Range** (Parameterbereich): Wenn Sie sich der besten Parameter nicht sicher sind, führen Sie eine Parametersuche mit dem Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Abstimmen der Hyperparameter des Modells) durch. Der Trainer durchläuft mehrere von Ihnen angegebene Werte, um die optimale Konfiguration zu finden.
  
4. **Optimization Tolerance** (Optimierungstoleranz): Geben Sie einen Wert ein, der das Intervall für die Toleranz bei der Optimierung definiert. Je kleiner der Wert ist, desto langsamer und genauer ist die Anpassung.

5. **L1 regularization weight** (L1-Regularisierungsgewichtung) und **L2 regularization weight** (L2-Regularisierungsgewichtung): Geben Sie Werte für L1- und L2-Regularisierung ein. Die *Regularisierung* fügt dem Algorithmus Beschränkungen bezüglich der Aspekte des Modells hinzu, die von den Trainingsdaten unabhängig sind. Regularisierung wird häufig verwendet, um Überanpassung zu vermeiden. 

    - Die L1-Regularisierung ist nützlich, wenn ein Modell mit möglichst geringer Dichte gewünscht ist.

        Eine L1-Regularisierung wird ausgeführt, indem die L1-Gewichtung des Gewichtungsvektors von dem Verlustausdruck subtrahiert wird, den das Lernmodul minimieren möchte. Die L1-Norm ist eine gute Näherung an die L0-Norm, die die Anzahl von Koordinaten ungleich 0 angibt.

    - L2-Regularisierung verhindert, dass jeder einzelnen Koordinate im Gewichtungsvektor zu viel Bedeutung zugewiesen wird. L2-Regularisierung ist nützlich, wenn ein Modell mit kleinen Gesamtgewichtungen gewünscht ist.

    In diesem Modul können Sie eine Kombination aus L1- und L2-Regularisierungen anwenden. Durch Kombinieren von L1- und L2-Regularisierung können Sie eine Strafe für die Größenordnung der Parameterwerte festlegen. Das Lernmodul versucht, die Strafe im Kompromiss mit dem Minimieren des Verlusts zu minimieren.

    Eine gute Erörterung der L1-und L2-Regularisierung finden Sie unter [Testlauf – L1-und L2-Regularisierung für Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning).

6. **Memory Size for L-BFGS** (Arbeitsspeichergröße für L-BFGS): Geben Sie an, wieviel Arbeitsspeicher für Anpassung und Optimierung des Modells reserviert werden soll.

     L-BFGS ist eine bestimmte Optimierungsmethode, die auf dem „Broyden–Fletcher–Goldfarb–Shanno“-Algorithmus (BFGS) basiert. Die Methode verwendet eine begrenzte Menge an Arbeitsspeicher (L), um den nächsten Schritt berechnen.

     Durch Ändern dieses Parameters können Sie die Anzahl von vorherigen Positionen und Gradienten beeinflussen, die für die Berechnung des nächsten Schritts gespeichert werden.

7. Stellen Sie die Verbindung des Trainingsdatasets und des untrainierten Modells mit einem der Trainingsmodule her: 

    - Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](train-model.md) (Trainieren des Modells) verwenden.

    - Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verwenden Sie das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) (Abstimmen der Hyperparameter des Modells).

    > [!WARNING]
    > 
    > - Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der erste Wert in der Parameterbereichsliste verwendet.
    > 
    > - Wenn Sie eine einzelne Reihe bestimmter Parameterwerte an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.
    > 
    > - Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

8.  Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie das Trainingsmodul aus, und wechseln Sie dann im rechten Bereich zur Registerkarte **Outputs+logs** (Ausgaben und Protokolle). Klicken Sie auf das Symbol für **Dataset registrieren**.  Sie finden das gespeicherte Modell als Modul in der Modulstruktur. 

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.