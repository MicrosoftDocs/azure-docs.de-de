---
title: 'Two-Class Neural Network: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Modul „Two-Class Neural Network“ (Neuronales Netz mit zwei Klassen) in Azure Machine Learning Service ein neuronales Netzmodell erstellen, mit dem Sie ein Ziel vorhersagen können, das nur zwei Werte hat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027768"
---
# <a name="two-class-neural-network-module"></a>Modul „Two-Class Neural Network“ (Neuronales Netz mit zwei Klassen)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Erstellen Sie mit diesem Modul ein neuronales Netzmodell, mit dem Sie ein Ziel mit nur zwei Werten vorhersagen können.

Die Klassifizierung mittels neuronaler Netze ist eine überwachte Lernmethode und erfordert daher ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält. Sie können dieses neuronale Netzmodell beispielsweise verwenden, um binäre Ergebnisse vorherzusagen, z.B. ob ein Patient eine bestimmte Krankheit hat oder nicht oder ob eine Maschine innerhalb eines bestimmten Zeitfensters ausfallen wird.  

Nachdem Sie das Modell definiert haben, trainieren Sie es, indem Sie ein mit Tags versehenes Dataset und das Modell als Eingabe in [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingaben vorherzusagen.

### <a name="more-about-neural-networks"></a>Weitere Informationen zu neuronalen Netzen

Ein neuronales Netz ist ein Komplex miteinander verbundener Schichten. Die Eingaben bilden die erste Schicht und sind mit einer Ausgabeschicht durch ein azyklisches Diagramm verbunden, das aus gewichteten Edges und Knoten besteht.

Zwischen Ein- und Ausgabeschicht können Sie mehrere ausgeblendete Schichten einfügen. Die meisten Vorhersageaufgaben können mithilfe nur einer oder einigen wenigen ausgeblendeten Schichten einfach durchgeführt werden. Jüngste Forschungen haben jedoch gezeigt, dass Deep Neural Networks (DNN) mit vielen Schichten bei komplexen Aufgaben wie Bild- oder Spracherkennung effektiv sein können. Die aufeinanderfolgenden Schichten werden verwendet, um zunehmende semantische Tiefe zu modellieren.

Die Beziehung zwischen Ein- und Ausgaben wird durch das Training des neuronalen Netzes mit den Eingabedaten erlernt. Die Richtung des Graphen verläuft von den Eingaben über die ausgeblendete Schicht bis zur Ausgabeschicht. Alle Knoten einer Schicht sind durch die gewichteten Edges mit den Knoten der nächsten Schicht verbunden.

Um die Ausgabe des Netzes für eine bestimmte Eingabe zu berechnen, wird bei jedem Knoten in den ausgeblendeten Schichten und in der Ausgabeschicht ein Wert berechnet. Der Wert wird festgelegt, indem die gewichtete Summe der Werte der Knoten der vorherigen Schicht berechnet wird. Auf diese gewichtete Summe wird dann eine Aktivierungsfunktion angewendet.
  
## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

1.  Fügen Sie das Modul **Two-Class Neural Network** Ihrem Experiment hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Classification** (Klassifizierung).  
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) festlegen.  
  
    -   **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie bereits wissen, wie Sie das Modell konfigurieren möchten.  

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

10. Wählen Sie die Option **Shuffle examples** (Beispiele mischen), um Fälle zwischen den Iterationen zu mischen. Wenn Sie diese Option deaktivieren, werden die Fälle bei jeder Ausführung des Experiments in genau der gleichen Reihenfolge verarbeitet.
  
11. Geben Sie für **Random number seed** (zufälliger Startwert) den als Startwert zu verwendenden Wert ein.
  
     Die Angabe eines Startwerts ist nützlich, wenn Sie die Wiederholbarkeit für alle Ausführungen desselben Experiments sicherstellen möchten.  Andernfalls wird ein von der Systemuhr bereitgestellter Wert als Startwert verwendet, was bei jeder Ausführung des Experiments zu geringfügig unterschiedlichen Ergebnissen führen kann.
  
13. Fügen Sie dem Experiment ein mit Tags versehenes Dataset hinzu, und stellen Sie eine Verbindung mit einem der [Trainingsmodule](module-reference.md) her.  
  
    -   Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](train-model.md) (Trainieren des Modells) verwenden.  
  
14. Führen Sie das Experiment aus.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Zusammenfassung der Parameter des Modells sowie der aus dem Training gewonnenen Merkmalsgewichtungen und anderer Parameter des neuronalen Netzes anzuzeigen, klicken Sie mit der rechten Maustaste auf die Ausgabe von [Train Model](./train-model.md) (Modell trainieren), und wählen Sie **Visualize** (Visualisieren) aus.  

+ Um eine Momentaufnahme des trainierten Modells zu speichern, klicken Sie mit der rechten Maustaste auf die Ausgabe **Trained model** (Trainiertes Modell), und wählen Sie **Save As Trained Model** (Als trainiertes Modell speichern) aus. Dieses Modell wird bei nachfolgenden Ausführungen desselben Experiments nicht aktualisiert.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 
