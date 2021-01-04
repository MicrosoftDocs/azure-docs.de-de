---
title: 'Gemitteltes Perzeptron mit zwei Klassen: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Two-Class Averaged Perceptron-Modul im Designer verwenden, um einen binären Klassifizierer zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: f1161dc99b84026bcc830c08b63b95d0bf3b0994
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421140"
---
# <a name="two-class-averaged-perceptron-module"></a>„Two-Class Averaged Perceptron“-Modul

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um ein Modell für maschinelles Lernen zu erstellen, das auf dem „Gemitteltes Perzeptron“-Algorithmus basiert.  
  
Dieser Klassifizierungsalgorithmus ist eine überwachte Lernmethode und erfordert ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält. Sie können das Modell trainieren, indem Sie das Modell und das mit Tags versehene Dataset als Eingabe für [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabebeispiele vorherzusagen.  

### <a name="about-averaged-perceptron-models"></a>Informationen zu „Gemitteltes Perzeptron“-Modellen

Die *„Gemitteltes Perzeptron“-Methode* ist eine frühe und einfache Version eines neuronalen Netzwerks. Bei diesem Ansatz werden Eingaben basierend auf einer linearen Funktion in mehrere mögliche Ausgaben klassifiziert und dann mit einem Satz Gewichtungen kombiniert, die aus dem Featurevektor abgeleitet werden – daher der Name „Perzeptron“.

Die einfacheren Perzeptronmodelle eignen sich zum Lernen linear trennbarer Muster, während neuronale Netze (insbesondere tiefe neuronale Netze) komplexere Klassengrenzen modellieren können. Allerdings sind Perzeptrone schneller, und weil sie Fälle seriell verarbeiten, können Perzeptrone kontinuierlich trainiert verwendet.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Gewusst wie: Konfigurieren eines gemittelten Perzeptrons mit zwei Klassen

1.  Fügen Sie das Modul **Two-Class Averaged Perceptron** Ihrer Pipeline hinzu.  

2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, geben Sie eine bestimmte Menge von Werten als Argumente an.

    -   **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  
  
3.  Geben Sie für **Learning rate** (Lernrate) einen Wert für die *Lernrate* an. Die Lernratenwerte bestimmen die Größe des Schritts, der jedes Mal, wenn das Modell getestet und korrigiert wird, beim stochastischen Gradientenverfahren verwendet wird.
  
     Indem Sie die Rate verkleinern, testen Sie das Modell häufiger, mit dem Risiko, dass Sie auf einem lokalen Plateau hängen bleiben. Mit größeren Schritten können Sie schneller konvergieren, mit dem Risiko, den echten Mindestwert zu unterschreiten.
  
4.  Geben Sie für **Maximum number of iterations** (Maximale Anzahl von Iterationen) die Häufigkeit ein, mit der der Algorithmus die Trainingsdaten überprüfen soll.  
  
     Ein frühes Stoppen bietet oft eine bessere Generalisierung. Ein Erhöhen der Anzahl von Iterationen verbessert die Anpassung, mit dem Risiko der Überanpassung.
  
5.  Geben Sie für **Random number seed** (Zufällig gewählter Startwert) optional einen ganzzahligen Wert ein, der als Startwert verwendet wird. Es empfiehlt sich, einen Startwert zu verwenden, wenn Sie die ausführungsübergreifende Reproduzierbarkeit der Pipeline sicherstellen möchten.  
  
1.  Verbinden Sie ein Trainingsdataset, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.




## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 