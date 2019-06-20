---
title: 'Regression mit neuronalen Netzwerken: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie das Modul für die Regression mit neuronalen Netzwerken in Azure Machine Learning Service verwendet wird, um mithilfe eines anpassbaren neuronalen Netzwerkalgorithmus ein Regressionsmodell zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027780"
---
# <a name="neural-network-regression-module"></a>Modul „Regression mit neuronalen Netzwerken“

*Erstellt ein Regressionsmodell mithilfe eines neuronalen Netzwerkalgorithmus*  
  
 Kategorie: Machine Learning / Modell initialisieren / Regression
  
## <a name="module-overview"></a>Modulübersicht  

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um mithilfe eines anpassbaren neuronalen Netzwerkalgorithmus ein Regressionsmodell zu erstellen.
  
 Neuronale Netzwerke sind zwar weithin für ihre Verwendung in Deep Learning und der Modellierung komplexer Probleme wie Bilderkennung bekannt, sie lassen sich aber einfach für Regressionsprobleme anpassen. Jede Klasse statistischer Modelle kann als neuronales Netz bezeichnet werden, sofern sie adaptive Gewichtungen verwenden und eine Annäherung an nicht lineare Funktionen in ihren Eingaben darstellen können. Daher ist Regression mit neuronalen Netzwerken für Probleme geeignet, für die traditionellere Regressionsmodelle keine passende Lösung bieten.
  
 Regression mit neuronalen Netzen ist eine überwachte Lernmethode und erfordert daher ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte enthält. Da ein Regressionsmodell einen numerischen Wert vorhersagt, muss die Bezeichnungsspalte ein numerischer Datentyp sein.  
  
 Sie können das Modell trainieren, indem Sie das Modell und das mit Tags versehene Dataset als Eingabe für [Train Model](./train-model.md) (Modell trainieren) bereitstellen. Das trainierte Modell kann anschließend verwendet werden, um Werte für neue Eingabebeispiele vorherzusagen.  
  
## <a name="configure-neural-network-regression"></a>Konfigurieren der Regression mit neuronalen Netzwerken 

Neuronale Netzwerke können umfassend angepasst werden. In diesem Abschnitt werden zwei Methoden zum Erstellen eines Modells beschrieben:
  
+ [Erstellen eines neuronalen Netzwerkmodells mithilfe der Standardarchitektur](#bkmk_DefaultArchitecture)  
  
    Wenn Sie die Standardarchitektur von neuronalen Netzen akzeptieren, verwenden Sie den Bereich **Eigenschaften** zum Festlegen von Parametern, die das Verhalten des neuronalen Netzwerks steuern, z.B. die Anzahl der Knoten in der verborgenen Ebene, die Lernrate und die Normalisierung.

    Beginnen Sie hier, wenn Sie noch nicht mit neuronalen Netzwerken vertraut sind. Das Modul unterstützt ohne tiefgreifende Kenntnis neuronaler Netze viele Anpassungen sowie die Optimierung von Modellen. 

+ Definieren einer benutzerdefinierten Architektur für ein neuronales Netzwerk 

    Verwenden Sie diese Option, wenn Sie zusätzliche verborgene Ebenen hinzufügen oder die Netzwerkarchitektur, ihre Verbindungen und die Aktivierungsfunktionen in vollem Umfang anpassen möchten.
    
    Diese Option eignet sich besonders, wenn Sie bereits etwas mit neuronalen Netzwerken vertraut sind. Zum Definieren der Netzwerkarchitektur verwenden Sie die Sprache Net#.  

##  <a name="bkmk_DefaultArchitecture"></a>Erstellen eines neuronalen Netzwerkmodells mithilfe der Standardarchitektur
  
1.  Fügen Sie auf der Benutzeroberfläche Ihrem Experiment das Modul **Regression mit neuronalen Netzwerken** hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Regression**. 
  
2. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie bereits wissen, wie Sie das Modell konfigurieren möchten.  

3.  Wählen Sie in **Hidden layer specification** (Spezifikation der verborgenen Ebenen) **Fully connected case** (Fall mit vollständigen Verbindungen) aus. Diese Option erstellt ein Modell mit der Standardarchitektur für neuronale Netze, die im Fall eines Regressionsmodells mit neuronalen Netzen drei Attribute aufweist:  
  
    + Das Netzwerk verfügt über genau eine verborgene Ebene.
    + Die Ausgabeebene ist vollständig mit der verborgenen Ebene verbunden, und die verborgene Ebene ist vollständig mit der Eingabeebene verbunden.
    + Die Anzahl der Knoten in der verborgenen Ebene kann vom Benutzer festgelegt werden (der Standardwert beträgt 100).  
  
    Da die Anzahl der Knoten auf der Eingabeebene durch die Anzahl der Merkmale in den Trainingsdaten bestimmt wird, kann es in einem Regressionsmodell nur einen Knoten auf der Ausgabeebene geben.  
  
4. Geben Sie in **Anzahl der verborgenen Knoten** die Anzahl der verborgenen Knoten ein. Der Standardwert ist eine verborgene Ebene mit 100 Knoten. (Diese Option ist nicht verfügbar, wenn Sie mithilfe von Net# eine benutzerdefinierte Architektur definieren.)
  
5.  Geben Sie für **Learning rate** (Lernrate) einen Wert ein, der die Größe des Schritts definiert, der bei jeder Iteration vor der Korrektur erfolgt. Ein höherer Wert für die Lernrate kann dazu führen, dass das Modell schneller konvergiert, wobei es jedoch lokale Mindestwerte überschreiten kann.

6.  Geben Sie für **Number of learning iterations** (Anzahl der Lerniterationen) die maximale Häufigkeit an, mit der der Algorithmus die Trainingsfälle verarbeitet.

7.  Geben Sie für „**The initial learning weights diameter“ (Anfangsdurchmesser der Lerngewichtungen) einen Wert ein, der die Knotengewichtungen am Anfang des Lernprozesses bestimmt.

8.  Geben Sie für **The momentum** (Dynamik) einen Wert ein, der beim Lernen als Gewichtung auf Knoten aus früheren Iterationen angewendet werden soll.

10. Wählen Sie die Option **Shuffle examples** (Zufallsreihenfolge der Beispiele) aus, um die Reihenfolge der Fälle zwischen den Iterationen zu ändern. Wenn Sie diese Option deaktivieren, werden die Fälle bei jeder Ausführung des Experiments in genau der gleichen Reihenfolge verarbeitet.
  
11. Für **Random number seed** (Zufällig gewählter Startwert) können Sie optional einen Wert eingeben, der als Startwert verwendet werden soll. Die Angabe eines Startwerts ist nützlich, wenn Sie die Wiederholbarkeit für alle Ausführungen desselben Experiments sicherstellen möchten.
  
13. Stellen Sie eine Verbindung eines Trainingsdatasets mit einem der [Trainingsmodule](module-reference.md) her: 
  
    -   Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie [Train Model](./train-model.md) verwenden.  
  
   
14. Führen Sie das Experiment aus.  

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Zusammenfassung der Parameter des Modells sowie der aus dem Training gewonnenen Merkmalsgewichtungen und anderer Parameter des neuronalen Netzes anzuzeigen, klicken Sie mit der rechten Maustaste auf die Ausgabe von [Train Model](./train-model.md) (Modell trainieren), und wählen Sie **Visualize** (Visualisieren) aus.  

+ Um eine Momentaufnahme des trainierten Modells zu speichern, klicken Sie mit der rechten Maustaste auf die Ausgabe **Trained model** (Trainiertes Modell), und wählen Sie **Save As Trained Model** (Als trainiertes Modell speichern) aus. Dieses Modell wird bei nachfolgenden Ausführungen desselben Experiments nicht aktualisiert.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 