---
title: 'Two-Class Logistic Regression: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Two-Class Logistic Regression in Azure Machine Learning einen binären Klassifizierer erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 2e29a666f4d478e11986f834cff94d9743223f22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012645"
---
# <a name="two-class-logistic-regression-module"></a>Modul „Two-Class Logistic Regression“ (Logistische Regression mit zwei Klassen)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um ein logistisches Regressionsmodell zu erstellen, mit dem Sie zwei (und nur zwei) Ergebnisse vorhersagen können. 

Die logistische Regression ist eine bekannte statistische Methode, die zur Modellierung vieler Arten von Problemen verwendet wird. Dieser Algorithmus ist eine *überwachte Lernmethode*, weshalb Sie ein Dataset bereitstellen müssen, das bereits die Ergebnisse zum Trainieren des Modells enthält.  

### <a name="about-logistic-regression"></a>Informationen zur logistischen Regression  

Die logistische Regression ist eine bekannte Statistikmethode, die zur Vorhersage der Wahrscheinlichkeit eines Ergebnisses verwendet wird und besonders für Klassifizierungsaufgaben geeignet ist. Der Algorithmus prognostiziert die Wahrscheinlichkeit des Vorkommens eines Ereignisses, indem er Daten an eine logistische Funktion anpasst.
  
Bei diesem Modul wird der Klassifizierungsalgorithmus für dichotome oder binäre Variablen optimiert. Wenn Sie mehrere Ergebnisse klassifizieren müssen, verwenden Sie das Modul [Multiclass Logistic Regression](./multiclass-logistic-regression.md) (Logisches Regression mit mehreren Klassen).

##  <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration  

Zum Trainieren dieses Modells müssen Sie ein Dataset bereitstellen, das eine Bezeichnungs- oder Klassenspalte enthält. Da dieses Modul für Probleme mit zwei Klassen vorgesehen ist, muss die Bezeichnungs- oder Klassenspalte genau zwei Werte enthalten. 

Beispielsweise kann die Bezeichnungsspalte [Abgestimmt] mit den möglichen Werten „Ja“ oder „Nein“ sein. Oder sie kann [Kreditrisiko] mit den möglichen Werten „Hoch“ oder „Niedrig“ lauten. 
  
1.  Fügen Sie das Modul **Two-Class Logistic Regression** Ihrer Pipeline hinzu.  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.  

    -   **Parameter Range** (Parameterbereich): Wenn Sie sich hinsichtlich der besten Parameter nicht sicher sind, können Sie optimale Parameter mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md) finden. Sie geben einen Wertebereich an, woraufhin das Training über mehrere Einstellungskombinationen iteriert, um die Wertekombination zu bestimmen, die das beste Ergebnis liefert.
  
3.  Geben Sie für **Optimization tolerance** (Optimierungstoleranz) einen Schwellenwert an, der bei der Optimierung des Modells verwendet werden soll. Wenn die Verbesserung zwischen Iterationen unter den angegebenen Schwellenwert fällt, gilt der Algorithmus als zu einer Lösung konvergiert, woraufhin das Training beendet wird.  
  
4.  Geben Sie für **L1 regularization weight** (Gewichtung von L1-Regularisierung) und **L2 regularization weight** (Gewichtung von L2-Regularisierung) einen Wert für die Regularisierungsparameter L1 und L2 ein. Für beide wird ein Wert ungleich 0 (null) empfohlen.  
     *Regularisierung* ist eine Methode zur Vermeidung von Überpassung durch Zuordnung von Straftermen zu Modellen mit extremen Koeffizientenwerten. Regularisierung funktioniert, indem die Strafterme, die mit Koeffizientenwerten verbunden sind, zum Fehler der Hypothese hinzufügt werden. So würde ein genaues Modell mit extremen Koeffizientenwerten stärker mit Straftermen belegt, während ein weniger genaues Modell mit konservativeren Werten weniger mit Straftermen belegt würde.  
  
     Die L1- und L2-Regularisierung haben unterschiedliche Auswirkungen und Zwecke.  
  
    -   L1 kann auf spärliche Modelle angewendet werden, was bei der Arbeit mit Daten mit hoher Dimensionalität nützlich ist.  
  
    -   Im Gegensatz dazu ist die L2-Regularisierung für Daten vorzuziehen, die nicht spärlich sind.  
  
     Dieser Algorithmus unterstützt eine lineare Kombination von L1- und L2-Regularisierungswerten, d.h. wenn <code>x = L1</code> und <code>y = L2</code>, dann definiert <code>ax + by = c</code> die lineare Spanne der Regalisierungsbegriffe.  
  
    > [!NOTE]
    >  Möchten Sie mehr zur L1- und L2-Regularisierung erfahren? Der folgende Artikel bietet eine Diskussion darüber, inwieweit sich die L1- und L2-Regularisierung unterscheiden und wie sie sich auf die Modellanpassung auswirken, sowie Codebeispiele für logistische Regressions- und neuronale Netzmodelle:  [L1- und L2-Regularisierung für Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)  
    >
    > Für logistische Regressionsmodelle wurden verschiedene lineare Kombinationen von L1- und L2-Begriffen entwickelt: zum Beispiel [Regularisierung mit elastischem Netz](https://wikipedia.org/wiki/Elastic_net_regularization). Wir empfehlen, dass Sie sich auf diese Kombinationen beziehen, um eine lineare Kombination zu definieren, die in Ihrem Modell wirksam ist.
      
5.  Geben Sie für **Memory size for L-BFGS** (Speichergröße für L-BFGS) die Speichergröße an, die für die *L-BFGS*-Optimierung verwendet werden soll.  
  
     L-BFGS steht für „limited memory Broyden-Fletcher-Goldfarb-Shanno“. Es handelt sich um einen Optimierungsalgorithmus, der häufig zur Parameterschätzung verwendet wird. Dieser Parameter gibt die Anzahl der bisherigen Positionen und Gradienten an, die für die Berechnung des nächsten Schritts gespeichert werden sollen.  
  
     Dieser Optimierungsparameter begrenzt die Menge an Speicher, die zur Berechnung des nächsten Schritts und der nächsten Richtung verwendet wird. Wenn Sie weniger Speicher angeben, ist das Training zwar schneller, aber ungenauer.  
  
6.  Geben Sie für **Random number seed** (Zufälliger Startwert) eine ganze Zahl ein. Die Definition eines Startwerts ist wichtig, wenn die Ergebnisse bei mehreren Ausführungen derselben Pipeline reproduzierbar sein sollen.  
  
  
8. Fügen Sie ein gekennzeichnetes Dataset zur Pipeline hinzu, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie eine einzelne Gruppe von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.  
  
9. Übermitteln Sie die Pipeline.  
  
## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:
 
  
+ Um Vorhersagen zu neuen Daten zu treffen, verwenden Sie das trainierte Modell und neue Daten als Eingabe für das Modul [Score Model](./score-model.md) (Bewerten des Modells). 


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.