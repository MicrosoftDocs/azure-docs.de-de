---
title: 'Two-Class Boosted Decision Tree: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Hier erfahren Sie, wie Sie mithilfe des Moduls „Two-Class Boosted Decision Tree“ in Azure Machine Learning Service ein Machine Learning-Modell erstellen, das auf dem Algorithmus für Boosted Decision Trees basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027622"
---
# <a name="two-class-boosted-decision-tree-module"></a>Modul „Two-Class Boosted Decision Tree“

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschauversion) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Machine Learning-Modell zu erstellen, das auf dem Algorithmus für Boosted Decision Trees basiert. 

Ein Boosted Decision Tree ist eine Ensemble-basierte Lernmethode. Die zweite Struktur korrigiert dabei die Fehler der ersten Struktur, die dritte Struktur korrigiert die Fehler der ersten und zweiten Struktur und so weiter.  Vorhersagen basieren auf dem Strukturensemble, das die Vorhersage generiert.
  
Mit ordnungsgemäß konfigurierten Boosted Decision Trees lässt sich für gewöhnlich am einfachsten eine optimale Leistung bei einem breiten Spektrum von Machine Learning-Aufgaben erzielen. Sie zählen allerdings auch zu den arbeitsspeicherintensiveren Lernmodellen, und bei der aktuellen Implementierung wird alles im Arbeitsspeicher gespeichert. Aus diesem Grund ist ein Boosted Decision Tree-Modell möglicherweise nicht in der Lage, die umfangreichen Datasets zu verarbeitet, die von einigen linearen Lernmodulen verarbeitet werden können.

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

Dieses Modul erstellt ein untrainiertes Klassifizierungsmodell. Da es sich bei der Klassifizierung um eine beaufsichtigte Lernmethode handelt, benötigen Sie zum Trainieren des Modells ein *mit Tags versehenes Dataset*, das eine Bezeichnungsspalte mit einem Wert für alle Zeilen enthält.

Diese Art von Modell kann mithilfe von [Train Model](././train-model.md) (Modell trainieren) trainiert werden. 

1.  Fügen Sie Ihrem Experiment in Azure Machine Learning das Modul **Boosted Decision Tree** hinzu.
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) festlegen.
  
    + **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.
  
  
3.  Geben Sie unter **Maximum number of leaves per tree** (Maximale Anzahl von Blättern pro Struktur) die maximale Anzahl von Endknoten (Blätter) an, die in einer Struktur erstellt werden können.
  
     Eine Erhöhung dieses Werts führt zu einer potenziell größeren Struktur und zu einer höheren Präzision, kann aber auch eine Überanpassung und eine längere Trainingsdauer zur Folge haben.
  
4.  Geben Sie unter **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) die Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.  
  
     Wenn Sie diesen Wert erhöhen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.
  
5.  Geben Sie unter **Learning rate** (Lernrate) eine Zahl zwischen 0 und 1 ein, um die Schrittgröße beim Lernen zu definieren.  
  
     Die Lernrate bestimmt, wie schnell bzw. langsam sich das Lernmodell der optimalen Lösung annähert. Ist die Schrittgröße zu groß, wird die optimale Lösung unter Umständen verfehlt. Ist die Schrittgröße zu klein, dauert die Annäherung an die beste Lösung länger.
  
6.  Geben Sie unter **Number of trees constructed** (Anzahl erstellter Strukturen) die Gesamtzahl von Entscheidungsstrukturen an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsstrukturen erzielen Sie unter Umständen eine bessere Abdeckung, allerdings verlängert sich dadurch die Trainingsdauer.
  
     Dieser Wert steuert auch die Anzahl von Strukturen, die bei der Visualisierung des trainierten Modells angezeigt werden. Wenn Sie eine einzelne Struktur anzeigen oder ausgeben möchten, legen Sie den Wert auf „1“ fest. In diesem Fall wird allerdings auch nur eine einzelne Struktur (die Struktur mit dem anfänglichen Parametersatz) generiert, und es finden keine weiteren Iterationen statt.
  
7.  Geben Sie unter **Random number seed** (Zufällig gewählter Startwert) eine nicht negative ganze Zahl ein, die als zufälliger Startwert verwendet werden soll. Die Angabe eines Startwerts sorgt für Reproduzierbarkeit in verschiedenen Ausführungen mit den gleichen Daten und Parametern.  
  
     Der zufällig gewählte Startwert ist standardmäßig auf „0“ festgelegt, was bedeutet, dass der ursprüngliche Startwert von der Systemuhr abgerufen wird.  Bei nachfolgenden Ausführungen wird ein zufällig gewählter Startwert verwendet, was zu abweichenden Ergebnissen führen kann.
  

9. Trainieren Sie das Modell.
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](./train-model.md) (Modell trainieren) verbinden.  
  
   
## <a name="results"></a>Ergebnisse

Klicken Sie nach Abschluss des Modelltrainings mit der rechten Maustaste auf die Ausgabe von [Train Model](./train-model.md) (Modell trainieren), um die Ergebnisse anzuzeigen:

+ Wenn Sie die Struktur anzeigen möchten, die in der jeweiligen Iteration erstellt wurde, wählen Sie **Visualisieren** aus. 
+ Wenn Sie Detailinformationen zu den Teilen und die Regeln für die einzelnen Knoten anzeigen möchten, klicken Sie auf die einzelnen Strukturen.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 