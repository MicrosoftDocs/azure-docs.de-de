---
title: 'Two-Class Neural Network: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Two-Class Neural Network in Azure Machine Learning einen binären Klassifizierer erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 9131a2439facef00cae818bffef38e536a40a2fd
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421157"
---
# <a name="two-class-neural-network-module"></a>Modul „Two-Class Neural Network“ (Neuronales Netz mit zwei Klassen)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Erstellen Sie mit diesem Modul ein neuronales Netzmodell, mit dem Sie ein Ziel mit nur zwei Werten vorhersagen können.

Die Klassifizierung mittels neuronaler Netze ist eine überwachte Lernmethode und erfordert daher ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält. Sie können dieses neuronale Netzmodell beispielsweise verwenden, um binäre Ergebnisse vorherzusagen, z.B. ob ein Patient eine bestimmte Krankheit hat oder nicht oder ob eine Maschine innerhalb eines bestimmten Zeitfensters ausfallen wird.  

Nachdem Sie das Modell definiert haben, trainieren Sie es, indem Sie ein mit Tags versehenes Dataset und das Modell als Eingabe in [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingaben vorherzusagen.

### <a name="more-about-neural-networks"></a>Weitere Informationen zu neuronalen Netzen

Ein neuronales Netz ist ein Komplex miteinander verbundener Schichten. Die Eingaben bilden die erste Schicht und sind mit einer Ausgabeschicht durch ein azyklisches Diagramm verbunden, das aus gewichteten Edges und Knoten besteht.

Zwischen Ein- und Ausgabeschicht können Sie mehrere ausgeblendete Schichten einfügen. Die meisten Vorhersageaufgaben können mithilfe nur einer oder einigen wenigen ausgeblendeten Schichten einfach durchgeführt werden. Jüngste Forschungen haben jedoch gezeigt, dass Deep Neural Networks (DNN) mit vielen Schichten bei komplexen Aufgaben wie Bild- oder Spracherkennung effektiv sein können. Die aufeinanderfolgenden Schichten werden verwendet, um zunehmende semantische Tiefe zu modellieren.

Die Beziehung zwischen Ein- und Ausgaben wird durch das Training des neuronalen Netzes mit den Eingabedaten erlernt. Die Richtung des Graphen verläuft von den Eingaben über die ausgeblendete Schicht bis zur Ausgabeschicht. Alle Knoten einer Schicht sind durch die gewichteten Edges mit den Knoten der nächsten Schicht verbunden.

Um die Ausgabe des Netzes für eine bestimmte Eingabe zu berechnen, wird bei jedem Knoten in den ausgeblendeten Schichten und in der Ausgabeschicht ein Wert berechnet. Der Wert wird festgelegt, indem die gewichtete Summe der Werte der Knoten der vorherigen Schicht berechnet wird. Auf diese gewichtete Summe wird dann eine Aktivierungsfunktion angewendet.
  
## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

1.  Fügen Sie das Modul **Zweiklassiges neuronales Netzwerk** Ihrer Pipeline hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Classification** (Klassifizierung).  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie bereits wissen, wie Sie das Modell konfigurieren möchten.

    -   **Parameter Range** (Parameterbereich): Wenn Sie sich hinsichtlich der besten Parameter nicht sicher sind, können Sie optimale Parameter mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md) finden. Sie geben einen Wertebereich an, woraufhin das Training über mehrere Einstellungskombinationen iteriert, um die Wertekombination zu bestimmen, die das beste Ergebnis liefert.  

3.  Wählen Sie für **Hidden layer specification** (Angabe ausgeblendeter Schichten) den Typ der zu erstellenden Netzarchitektur aus.  
  
    -   **Fully connected case** (Vollständig verbundener Fall): Verwendet die standardmäßige neuronale Netzarchitektur, die für neuronale Netze mit zwei Klassen wie folgt definiert ist:
  
        -   Hat eine ausgeblendete Schicht.
  
        -   Die Ausgabeschicht ist vollständig mit der ausgeblendeten Schicht verbunden, und die ausgeblendete Schicht ist vollständig mit der Eingabeschicht verbunden.
  
        -   Die Anzahl der Knoten in der Eingabeschicht entspricht der Anzahl der Features in den Trainingsdaten.
  
        -   Die Anzahl der Knoten in der ausgeblendeten Schicht wird vom Benutzer festgelegt. Der Standardwert ist 100.
  
        -   Die Anzahl der Knoten entspricht der Anzahl der Klassen. Für ein neuronales Netz mit zwei Klassen bedeutet dies, dass alle Eingaben einem von zwei Knoten in der Ausgabeschicht zugeordnet werden müssen.

5.  Definieren Sie für **Learning rate** (Lernrate) die Größe des Schritts, der bei jeder Iteration vor der Korrektur erfolgt. Ein höherer Wert für die Lernrate kann dazu führen, dass das Modell schneller konvergiert, wobei es jedoch lokale Mindestwerte überschreiten kann.

6.  Geben Sie für **Number of learning iterations** (Anzahl der Lerniterationen) die maximale Häufigkeit an, mit der der Algorithmus die Trainingsfälle verarbeiten soll.

7.  Geben Sie für **The initial learning weights diameter** (Anfangsdurchmesser der Lerngewichtungen) die Knotengewichtungen am Anfang des Lernprozesses an.

8.  Geben Sie für **The momentum** (Dynamik) eine Gewichtung an, die beim Lernen auf Knoten aus früheren Iterationen angewendet werden soll.  

10. Wählen Sie die Option **Shuffle examples** (Beispiele mischen), um Fälle zwischen den Iterationen zu mischen. Wenn Sie diese Option deaktivieren, werden die Fälle bei jeder Ausführung der Pipeline in exakt der gleichen Reihenfolge verarbeitet.
  
11. Geben Sie für **Random number seed** (zufälliger Startwert) den als Startwert zu verwendenden Wert ein.
  
     Die Angabe eines Startwerts ist nützlich, wenn Sie die Wiederholbarkeit für alle Ausführungen derselben Pipeline sicherstellen möchten.  Andernfalls wird ein von der Systemuhr bereitgestellter Wert als Startwert verwendet, was bei jeder Ausführung der Pipeline zu leicht unterschiedlichen Ergebnissen führen kann.
  
13. Fügen Sie ein gekennzeichnetes Dataset zur Pipeline hinzu, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.  
  
14. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich des Moduls **Train model** aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbares Modul zu speichern.

+ Um das Modell zur Bewertung zu verwenden, fügen Sie einer Pipeline das Modul **Score Model** hinzu.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
