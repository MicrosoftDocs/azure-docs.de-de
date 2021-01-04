---
title: 'Multiclass Neural Network: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Multiclass Neural Network-Modul im Azure Machine Learning-Designer verwenden, um ein Ziel vorherzusagen, das mehrklassige Werte hat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: a4f7f8a7793f31ffbf2099cbfb314fc5097319f5
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421259"
---
# <a name="multiclass-neural-network-module"></a>Modul „Multiclass Neural Network“ (Neuronales Netz mit mehreren Klassen)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Erstellen Sie mit diesem Modul ein neuronales Netzmodell, mit dem Sie ein Ziel mit mehreren Werten vorhersagen können. 

Neuronale Netzwerke dieser Art können z.B. in komplexen Aufgaben des maschinellen Sehens wie Ziffern- oder Buchstabenerkennung, Klassifizierung von Dokumenten und Mustererkennung verwendet werden.

Die Klassifizierung mittels neuronaler Netze ist eine überwachte Lernmethode und erfordert daher ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält.

Sie können das Modell trainieren, indem Sie das Modell und das mit Tags versehene Dataset als Eingabe für [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabebeispiele vorherzusagen.  

## <a name="about-neural-networks"></a>Informationen zu neuronalen Netzen

Ein neuronales Netz ist ein Komplex miteinander verbundener Schichten. Die Eingaben bilden die erste Schicht und sind mit einer Ausgabeschicht durch ein azyklisches Diagramm verbunden, das aus gewichteten Edges und Knoten besteht.

Zwischen Ein- und Ausgabeschicht können Sie mehrere ausgeblendete Schichten einfügen. Die meisten Vorhersageaufgaben können mithilfe nur einer oder einigen wenigen ausgeblendeten Schichten einfach durchgeführt werden. Jüngste Forschungen haben jedoch gezeigt, dass Deep Neural Networks (DNN) mit vielen Schichten bei komplexen Aufgaben wie Bild- oder Spracherkennung effektiv sein können. Die aufeinanderfolgenden Schichten werden verwendet, um zunehmende semantische Tiefe zu modellieren.

Die Beziehung zwischen Ein- und Ausgaben wird durch das Training des neuronalen Netzes mit den Eingabedaten erlernt. Die Richtung des Graphen verläuft von den Eingaben über die ausgeblendete Schicht bis zur Ausgabeschicht. Alle Knoten einer Schicht sind durch die gewichteten Edges mit den Knoten der nächsten Schicht verbunden.

Um die Ausgabe des Netzes für eine bestimmte Eingabe zu berechnen, wird bei jedem Knoten in den ausgeblendeten Schichten und in der Ausgabeschicht ein Wert berechnet. Der Wert wird festgelegt, indem die gewichtete Summe der Werte der Knoten der vorherigen Schicht berechnet wird. Auf diese gewichtete Summe wird dann eine Aktivierungsfunktion angewendet.

## <a name="configure-multiclass-neural-network"></a>Konfigurieren des Multiclass Neural Network

1. Fügen Sie das Modul **Multiclass Neural Network** im Designer zu Ihrer Pipeline hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Classification** (Klassifizierung).

2. **Create trainer mode** (Trainermodus erstellen): Verwenden Sie diese Option, um anzugeben, wie das Modell trainiert werden soll:

    - **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie bereits wissen, wie Sie das Modell konfigurieren möchten.

    - **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  

3. **Hidden layer specification** (Ausgeblendete Schichtspezifikation): Wählen Sie den Typ der zu erstellenden Netzwerkarchitektur aus.

    - **Fully connected case** (Vollständig verbundener Fall): Wählen Sie diese Option, um ein Modell mit standardmäßiger der neuronaler Netzarchitektur zu erstellen. Für mehrklassige neuronale Netzwerkmodelle lauten die Standardwerte:

        - Eine ausgeblendete Schicht
        - Die Ausgabeschicht ist vollständig mit der ausgeblendeten Schicht verbunden.
        - Die ausgeblendete Schicht ist vollständig mit der Eingabeschicht verbunden.
        - Die Anzahl der Knoten in der Eingabeschicht wird durch die Anzahl der Features in den Trainingsdaten bestimmt.
        - Die Anzahl der Knoten in der ausgeblendeten Schicht kann vom Benutzer festgelegt werden. Der Standardwert ist 100.
        - Die Anzahl der Knoten in der Ausgabeschicht hängt von der Anzahl der Klassen ab.
  
   

5. **Number of hidden nodes** (Anzahl der ausgeblendeten Knoten): Mit dieser Option können Sie die Anzahl der ausgeblendeten Knoten in der Standardarchitektur anpassen. Geben Sie die Anzahl der ausgeblendeten Knoten ein. Der Standardwert ist eine verborgene Ebene mit 100 Knoten.

6. **Die Lernrate**: Definieren Sie die Größe des bei jeder Iteration vor der Korrektur ausgeführten Schritts. Ein höherer Wert für die Lernrate kann dazu führen, dass das Modell schneller konvergiert, es kann jedoch auch lokale Mindestwerte unterschreiten.

7. **Number of learning iterations** (Anzahl der Lerniterationen): Geben Sie die maximale Häufigkeit an, mit der der Algorithmus die Trainingsfälle verarbeiten soll.

8. **The initial learning weights diameter** (Anfangsdurchmesser der Lerngewichtungen): Geben Sie die Knotengewichtungen am Anfang des Lernprozesses an.

9. **The momentum** (Dynamik): Geben Sie eine Gewichtung an, die beim Lernen auf Knoten aus früheren Iterationen angewendet werden soll.
  
11. **Shuffle examples** (Beispiele mischen): Wählen Sie diese Option, um Fälle zwischen den Iterationen zu mischen.

    Wenn Sie diese Option deaktivieren, werden die Fälle bei jeder Ausführung der Pipeline in exakt der gleichen Reihenfolge verarbeitet.

12. **Random number seed** (Zufälliger Startwert): Geben Sie einen als Startwert zu verwendenden Wert ein, wenn Sie die Wiederholbarkeit für alle Ausführungen derselben Pipeline sicherstellen möchten.

14. Trainieren des Modells:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein mit Tags versehenes Dataset, und trainieren Sie das Modell mithilfe von [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie eine einzelne Reihe bestimmter Parameterwerte an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.  
  

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

- Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich des Moduls **Train model** aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbares Modul zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 