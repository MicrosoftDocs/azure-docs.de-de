---
title: 'Entscheidungswaldregression: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mithilfe des Moduls „Two-Class Averaged Perceptron“ (Gemitteltes Perzeptron mit zwei Klassen) in Azure Machine Learning Service ein Modell für maschinelles Lernen erstellen, das auf dem „Gemitteltes Perzeptron“-Algorithmus basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027770"
---
# <a name="two-class-averaged-perceptron-module"></a>„Two-Class Averaged Perceptron“-Modul

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Modell für maschinelles Lernen zu erstellen, das auf dem „Gemitteltes Perzeptron“-Algorithmus basiert.  
  
Dieser Klassifizierungsalgorithmus ist eine überwachte Lernmethode und erfordert ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält. Sie können das Modell trainieren, indem Sie das Modell und das mit Tags versehene Dataset als Eingabe für [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabebeispiele vorherzusagen.  

### <a name="about-averaged-perceptron-models"></a>Informationen zu „Gemitteltes Perzeptron“-Modellen

Die *„Gemitteltes Perzeptron“-Methode* ist eine frühe und einfache Version eines neuronalen Netzwerks. Bei diesem Ansatz werden Eingaben basierend auf einer linearen Funktion in mehrere mögliche Ausgaben klassifiziert und dann mit einem Satz Gewichtungen kombiniert, die aus dem Featurevektor abgeleitet werden – daher der Name „Perzeptron“.

Die einfacheren Perzeptronmodelle eignen sich zum Lernen linear trennbarer Muster, während neuronale Netze (insbesondere tiefe neuronale Netze) komplexere Klassengrenzen modellieren können. Allerdings sind Perzeptrone schneller, und weil sie Fälle seriell verarbeiten, können Perzeptrone kontinuierlich trainiert verwendet.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Gewusst wie: Konfigurieren eines gemittelten Perzeptrons mit zwei Klassen

1.  Fügen Sie das Modul **Two-Class Averaged Perceptron** Ihrem Experiment hinzu.  

2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, geben Sie eine bestimmte Menge von Werten als Argumente an.
  
3.  Geben Sie für **Learning rate** (Lernrate) einen Wert für die *Lernrate* an. Die Lernratenwerte bestimmen die Größe des Schritts, der jedes Mal, wenn das Modell getestet und korrigiert wird, beim stochastischen Gradientenverfahren verwendet wird.
  
     Indem Sie die Rate verkleinern, testen Sie das Modell häufiger, mit dem Risiko, dass Sie auf einem lokalen Plateau hängen bleiben. Mit größeren Schritten können Sie schneller konvergieren, mit dem Risiko, den echten Mindestwert zu unterschreiten.
  
4.  Geben Sie für **Maximum number of iterations** (Maximale Anzahl von Iterationen) die Häufigkeit ein, mit der der Algorithmus die Trainingsdaten überprüfen soll.  
  
     Ein frühes Stoppen bietet oft eine bessere Generalisierung. Ein Erhöhen der Anzahl von Iterationen verbessert die Anpassung, mit dem Risiko der Überanpassung.
  
5.  Geben Sie für **Random number seed** (Zufällig gewählter Startwert) optional einen ganzzahligen Wert ein, der als Startwert verwendet wird. Sie sollten einen Startwert verwenden, wenn Sie die Ausführungen übergreifende Reproduzierbarkeit des Experiments sicherstellen möchten.  
  
1.  Stellen Sie eine Verbindung eines Trainingsdatasets mit einem der Trainingsmodule her:
  
    -   Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](train-model.md) (Trainieren des Modells) verwenden.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Zusammenfassung der Parameter des Modells sowie der aus dem Training gewonnenen Featuregewichtungen anzuzeigen, klicken Sie mit der rechten Maustaste auf die Ausgabe von [Train Model](./train-model.md).


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 