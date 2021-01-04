---
title: 'Boosted Decision Tree Regression (Regression bei verstärktem Entscheidungsbaum): Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie das Modul Boosted Decision Tree Regression in Azure Machine Learning verwenden, um mithilfe von Verstärkung ein Ensemble von Regressionsbäumen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/24/2020
ms.openlocfilehash: 664943fc5535883b3df77b2795383e5c0586a71c
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375328"
---
# <a name="boosted-decision-tree-regression-module"></a>Modul „Boosted Decision Tree Regression“ (Regression bei verstärktem Entscheidungsbaum)

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um mithilfe von Verstärkung ein Ensemble von Regressionsbäumen zu erstellen. *Verstärkung* bedeutet, dass jeder Baum von früheren Bäumen abhängig ist. Der Algorithmus lernt durch Anpassung der übrigen, früher trainierten Bäume. Daher führt die Verstärkung in einem Ensemble von Entscheidungsbäumen meist zu höherer Genauigkeit, allerdings mit dem minimalen Risiko einer geringeren Abdeckung.  

Dieses Modul basiert auf dem LightGBM-Algorithmus.
  
Diese Regressionsmethode entspricht einer überwachten Lernmethode, die folglich ein *bezeichnetes Dataset* erfordert. Die Bezeichnungsspalte muss numerische Werte enthalten.  

> [!NOTE]
> Verwenden Sie dieses Modul nur mit Datasets, die numerische Variablen enthalten.  

Nachdem Sie das Modell definiert haben, trainieren Sie es mit [Train Model](./train-model.md) (Modell trainieren).

  
## <a name="more-about-boosted-regression-trees"></a>Weitere Informationen zu verstärkten Regressionsbäumen  

Verstärkung zählt neben Bagging, zufälligen Gesamtstrukturen usw. zu den klassischen Methoden für die Erstellung von Ensemblemodellen.  In Azure Machine Learning verwenden verstärkte Entscheidungsbäume eine effiziente Implementierung des Gradient Boosting-Algorithmus mit dem Namen MART. Gradient Boosting ist ein Machine Learning-Verfahren für Regressionsprobleme. Die einzelnen Regressionsbäume werden schrittweise erstellt, wobei eine vordefinierte Verlustfunktion die Fehler in den einzelnen Schritten misst und diese im nächsten Schritt korrigiert. Daher ist das Vorhersagemodell tatsächlich ein Ensemble schwächerer Vorhersagemodelle.  
  
Bei Regressionsproblemen dient die Verstärkung dazu, eine Reihe von Bäumen schrittweise zu erstellen. Anschließend wird der optimale Baum mithilfe einer beliebig differenzierbaren Verlustfunktion ausgewählt.  
  
Weitere Informationen finden Sie in diesen Artikeln:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Dieser Wikipedia-Artikel zum Thema Gradient Boosting bietet Hintergrundinformationen zu verstärkten Bäumen. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: From RankNet to LambdaRank to LambdaMART: An Overview. Von J.C. Burges.

Die Gradient Boosting-Methode kann auch für Klassifizierungsprobleme verwendet werden, um diese mit einer geeigneten Verlustfunktion auf ein Regressionsproblem herunterzustufen. Weitere Informationen zur Implementierung verstärkter Entscheidungsbäume bei Klassifizierungsaufgaben finden Sie unter [Verstärkter Entscheidungsbaum mit zwei Klassen](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Gewusst wie: Konfigurieren von „Boosted Decision Tree Regression“

1.  Fügen Sie das Modul **Boosted Decision Tree** Ihrer Pipeline hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Regression**. 
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie wissen, wie Sie das Modell konfigurieren möchten, und geben Sie einen bestimmten Satz von Werten als Argumente an. 
     
    -   **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.    
   
  
3. **Maximum number of leaves per tree** (Maximale Anzahl der Blätter pro Baum): Geben Sie die maximale Anzahl von Endknoten (Blättern) an, die in einem Baum erstellt werden können.  

    Eine Erhöhung dieses Werts führt zu einem potenziell größeren Baum und zu einer höheren Genauigkeit, kann aber auch eine Überanpassung und eine längere Trainingsdauer zur Folge haben.  

4. **Minimum number of samples per leaf node** (Minimale Anzahl der Stichproben pro Blattknoten): Geben Sie die minimale Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatts) in einem Baum erforderlich sind.

    Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.

5. **Learning rate** (Lernrate): Geben Sie eine Zahl zwischen 0 und 1 ein, um die Schrittgröße beim Lernen zu definieren. Die Lernrate bestimmt, wie schnell bzw. langsam sich das Lernmodell der optimalen Lösung annähert. Ist die Schrittgröße zu groß, wird die optimale Lösung u. U. verfehlt. Ist die Schrittgröße zu klein, dauert die Annäherung an die beste Lösung länger.

6. **Number of trees constructed**: (Anzahl der erstellten Bäume) Geben Sie die Gesamtzahl von Entscheidungsbäumen an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie u. U. eine bessere Abdeckung, allerdings verlängert sich dadurch auch die Trainingsdauer.

    Wenn Sie den Wert auf 1 festlegen, wird allerdings nur eine einzelne Struktur (die Struktur mit dem anfänglichen Parametersatz) generiert, und es finden keine weiteren Iterationen statt.

7. **Random number seed** (Zufälliger Startwert): Geben Sie eine optionale, nicht negative ganze Zahl als zufälligen Startwert an. Die Angabe eines Startwerts gewährleistet Reproduzierbarkeit in verschiedenen Ausführungen, die auf den gleichen Daten und Parametern basieren.

    Der zufällige Startwert ist standardmäßig auf „0“ festgelegt, was bedeutet, dass der ursprüngliche Startwert von der Systemuhr abgerufen wird.
  

9. Trainieren des Modells:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.
    

10. Übermitteln Sie die Pipeline.  
  
## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Wenn Sie das Modell zur Bewertung verwenden möchten, verbinden Sie [Train Model](train-model.md) (Modell trainieren) mit [Score Model](./score-model.md) (Modell bewerten), um Werte für neue Eingabebeispiele vorherzusagen.

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich des Moduls **Trained model** (Trainiertes Modell) aus, und klicken Sie dann auf das Symbol für **Dataset registrieren**. Die Kopie des trainierten Modells wird als Modul in der Modulstruktur gespeichert und wird bei aufeinanderfolgenden Durchläufen der Pipeline nicht aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 
