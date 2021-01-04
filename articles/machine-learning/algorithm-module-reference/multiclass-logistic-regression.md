---
title: Logistische Regression mit mehreren Klassen Modulreferenz
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Multiclass Logistic Regression im Azure Machine Learning-Designer mehrere Werte vorhersagen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420732"
---
# <a name="multiclass-logistic-regression-module"></a>Modul „Multiclass Logistic Regression“

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um ein logistisches Regressionsmodell zu erstellen, mit dem Sie mehrere Werte vorhersagen können.

Die Klassifizierung mit logistischer Regression entspricht einer überwachten Lernmethode, die folglich ein bezeichnetes Dataset erfordert. Sie trainieren das Modell, indem Sie das Modell und das Dataset als Eingabe für ein Modul wie z.B. [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabenbeispiele vorherzusagen.

Azure Machine Learning bietet auch ein [Two-Class Logistic Regression](./two-class-logistic-regression.md)-Modul (logistische Regression mit zwei Klassen), das zur Klassifizierung von binären oder dichotomen Variablen geeignet ist.

## <a name="about-multiclass-logistic-regression"></a>Informationen zur logistischen Regression mit mehreren Klassen

Die logistische Regression ist eine bekannte Statistikmethode, die zur Vorhersage der Wahrscheinlichkeit eines Ergebnisses verwendet wird und für Klassifizierungsaufgaben geeignet ist. Der Algorithmus prognostiziert die Wahrscheinlichkeit des Vorkommens eines Ereignisses, indem er Daten an eine logistische Funktion anpasst. 

Bei der logistischen Regression mit mehreren Klassen kann der Klassifizierer zur Vorhersage mehrerer Ergebnisse verwendet werden.

## <a name="configure-a-multiclass-logistic-regression"></a>Konfigurieren einer logistischen Regression mit mehreren Klassen

1. Fügen Sie das Modul **Multiclass Logistic Regression** Ihrer Pipeline hinzu.

2. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.

    + **Single Parameter** (Einzelner Parameter): Verwenden Sie diese Option, wenn Sie wissen, wie Sie das Modell konfigurieren möchten, und geben Sie einen bestimmten Satz von Werten als Argumente an.

    + **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  

3. **Optimization tolerance** (Optimierungstoleranz), geben Sie den Schwellenwert für die Optimierungskonvergenz an. Wenn die Verbesserung zwischen Iterationen kleiner als der Schwellenwert ist, wird der Algorithmus beendet und das aktuelle Modell zurückgegeben.

4. **L1 regularization weight** (L1-Regularisierungsgewichtung), **L2 regularization weight** (L2-Regularisierungsgewichtung): Geben Sie einen Wert für die Regularisierungsparameter „L1“ und „L2“ an. Für beide wird ein Wert ungleich 0 (null) empfohlen.

    Regularisierung ist eine Methode zur Vermeidung von Überpassung durch Zuordnung von Straftermen zu Modellen mit extremen Koeffizientenwerten. Regularisierung funktioniert, indem die Strafterme, die mit Koeffizientenwerten verbunden sind, zum Fehler der Hypothese hinzufügt werden. Ein genaues Modell mit extremen Koeffizientenwerten würde stärker mit Straftermen belegt, während ein weniger genaues Modell mit konservativeren Werten weniger mit Straftermen belegt würde.

     Die L1- und L2-Regularisierung haben unterschiedliche Auswirkungen und Zwecke. L1 kann auf spärliche Modelle angewendet werden, was bei der Arbeit mit Daten mit hoher Dimensionalität nützlich ist. Im Gegensatz dazu ist die L2-Regularisierung für Daten vorzuziehen, die nicht spärlich sind.  Dieser Algorithmus unterstützt eine lineare Kombination von L1- und L2-Regularisierungswerten, d.h. wenn `x = L1` und `y = L2`, dann definiert `ax + by = c` die lineare Spanne der Regularisierungsbegriffe.

     Für logistische Regressionsmodelle wurden verschiedene lineare Kombinationen von L1- und L2-Begriffen entwickelt, z.B. [Regularisierung mit elastischem Netz](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Random number seed** (Zufälliger Startwert): Geben Sie einen ganzzahligen Wert als Ausgangswert für den Algorithmus ein, wenn die Ergebnisse in den Ausführungen wiederholbar sein sollen. Andernfalls wird ein Systemtaktwert als Startwert verwendet. Dies kann in den Ausführungen derselben Pipeline zu geringfügig unterschiedlichen Ergebnissen führen.

8. Verbinden Sie ein bezeichnetes Dataset, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

9. Übermitteln Sie die Pipeline.



## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 