---
title: 'Multiclass Neural Network: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Modul „Multiclass Neural Network“ (Neuronales Netz mit mehreren Klassen) in Azure Machine Learning Service ein neuronales Netzmodell erstellen, mit dem Sie ein Ziel vorhersagen können, das mehrere Werte hat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027790"
---
# <a name="multiclass-neural-network-module"></a>Modul „Multiclass Neural Network“ (Neuronales Netz mit mehreren Klassen)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

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

1. Fügen Sie auf der Benutzeroberfläche das Modul **Multiclass Neural Network** Ihrem Experiment hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Classification** (Klassifizierung).

2. **Create trainer mode** (Trainermodus erstellen): Verwenden Sie diese Option, um anzugeben, wie das Modell trainiert werden soll:

    - **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie bereits wissen, wie Sie das Modell konfigurieren möchten.

    

3. **Hidden layer specification** (Ausgeblendete Schichtspezifikation): Wählen Sie den Typ der zu erstellenden Netzwerkarchitektur aus.

    - **Fully connected case** (Vollständig verbundener Fall): Wählen Sie diese Option, um ein Modell mit standardmäßiger der neuronaler Netzarchitektur zu erstellen. Für mehrklassige neuronale Netzwerkmodelle lauten die Standardwerte:

        - Eine ausgeblendete Schicht
        - Die Ausgabeschicht ist vollständig mit der ausgeblendeten Schicht verbunden.
        - Die ausgeblendete Schicht ist vollständig mit der Eingabeschicht verbunden.
        - Die Anzahl der Knoten in der Eingabeschicht wird durch die Anzahl der Features in den Trainingsdaten bestimmt.
        - Die Anzahl der Knoten in der ausgeblendeten Schicht kann vom Benutzer festgelegt werden. Der Standardwert ist 100.
        - Die Anzahl der Knoten in der Ausgabeschicht hängt von der Anzahl der Klassen ab.
  
   

5. **Number of hidden nodes** (Anzahl der ausgeblendeten Knoten): Mit dieser Option können Sie die Anzahl der ausgeblendeten Knoten in der Standardarchitektur anpassen. Geben Sie die Anzahl der ausgeblendeten Knoten ein. Der Standardwert ist eine ausgeblendete Schicht mit 100 Knoten.

6. **Die Lernrate**: Definieren Sie die Größe des bei jeder Iteration vor der Korrektur ausgeführten Schritts. Ein höherer Wert für die Lernrate kann dazu führen, dass das Modell schneller konvergiert, es kann jedoch auch lokale Mindestwerte unterschreiten.

7. **Number of learning iterations** (Anzahl der Lerniterationen): Geben Sie die maximale Häufigkeit an, mit der der Algorithmus die Trainingsfälle verarbeiten soll.

8. **The initial learning weights diameter** (Anfangsdurchmesser der Lerngewichtungen): Geben Sie die Knotengewichtungen am Anfang des Lernprozesses an.

9. **The momentum** (Dynamik): Geben Sie eine Gewichtung an, die beim Lernen auf Knoten aus früheren Iterationen angewendet werden soll.
  
11. **Shuffle examples** (Beispiele mischen): Wählen Sie diese Option, um Fälle zwischen den Iterationen zu mischen.

    Wenn Sie diese Option deaktivieren, werden Fälle in genau der Reihenfolge jedes Mal verarbeitet, die Sie das Experiment ausführen.

12. **Random number seed** (Startwert der Zufallszahlen): Geben Sie einen als Startwert zu verwendenden Wert ein, wenn Sie die Wiederholbarkeit für alle Ausführungen desselben Experiments sicherstellen möchten.

14. Stellen Sie eine Verbindung eines Trainingsdatasets mit einem der [Trainingsmodule](module-reference.md) her: 

    - Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie [Train Model](train-model.md) verwenden.  
  

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

- Um eine Zusammenfassung der Parameter des Modells sowie der aus dem Training gewonnenen Featuregewichtungen und anderer Parameter des neuronalen Netzes zu sehen, klicken Sie mit der rechten Maustaste auf die Ausgabe von [Train Model](./train-model.md), und wählen Sie **Visualize** (Visualisieren) aus.  

- Um eine Momentaufnahme des trainierten Modells zu speichern, klicken Sie mit der rechten Maustaste auf die Ausgabe **Trained model** (Trainiertes Modell), und wählen Sie **Save As Trained Model** (Als trainiertes Modell speichern) aus. Dieses Modell wird bei nachfolgenden Ausführungen desselben Experiments nicht aktualisiert.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 