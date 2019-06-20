---
title: 'Boosted Decision Tree Regression (Regression bei verstärktem Entscheidungsbaum): Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Boosted Decision Tree Regression“ (Regression bei verstärktem Entscheidungsbaum) in Azure Machine Learning Service verwenden, um mithilfe von Verstärkung ein Ensemble von Regressionsbäumen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67e54f10074ee566ce974dbd27485904bfe0a653
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411547"
---
# <a name="boosted-decision-tree-regression-module"></a>Modul „Boosted Decision Tree Regression“ (Regression bei verstärktem Entscheidungsbaum)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um mithilfe von Verstärkung ein Ensemble von Regressionsbäumen zu erstellen. *Verstärkung* bedeutet, dass jeder Baum von früheren Bäumen abhängig ist. Der Algorithmus lernt durch Anpassung der übrigen, früher trainierten Bäume. Daher führt die Verstärkung in einem Ensemble von Entscheidungsbäumen meist zu höherer Genauigkeit, allerdings mit dem minimalen Risiko einer geringeren Abdeckung.  
  
Diese Regressionsmethode entspricht einer überwachten Lernmethode, die folglich ein *bezeichnetes Dataset* erfordert. Die Bezeichnungsspalte muss numerische Werte enthalten.  

> [!NOTE]
> Verwenden Sie dieses Modul nur mit Datasets, die numerische Variablen enthalten.  

Nachdem Sie das Modell definiert haben, trainieren Sie es mit [Train Model](./train-model.md) (Modell trainieren).

> [!TIP]
> Möchten Sie mehr über die erstellten Bäume erfahren? Nachdem das Modell trainiert wurde, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls [Train Model](./train-model.md), und wählen Sie **Visualize** (Visualisieren) aus, um den bei jeder Iteration erstellten Baum anzuzeigen. Sie können Detailinformationen zu den Teilen jedes Baums sowie die Regeln für die einzelnen Knoten anzeigen.  
  
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

1.  Fügen Sie das Modul **Boosted Decision Tree** (Verstärkter Entscheidungsbaum) Ihrem Experiment hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren) in der Kategorie **Regression**. 
  
2.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) festlegen.  
  
    -   **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie wissen, wie Sie das Modell konfigurieren möchten, und geben Sie einen bestimmten Satz von Werten als Argumente an.  
   
  
3. **Maximum number of leaves per tree** (Maximale Anzahl der Blätter pro Baum): Geben Sie die maximale Anzahl von Endknoten (Blättern) an, die in einem Baum erstellt werden können.  

    Eine Erhöhung dieses Werts führt zu einem potenziell größeren Baum und zu einer höheren Genauigkeit, kann aber auch eine Überanpassung und eine längere Trainingsdauer zur Folge haben.  

4. **Minimum number of samples per leaf node** (Minimale Anzahl der Stichproben pro Blattknoten): Geben Sie die minimale Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatts) in einem Baum erforderlich sind.

    Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.

5. **Learning rate** (Lernrate): Geben Sie eine Zahl zwischen 0 und 1 ein, um die Schrittgröße beim Lernen zu definieren. Die Lernrate bestimmt, wie schnell bzw. langsam sich das Lernmodell der optimalen Lösung annähert. Ist die Schrittgröße zu groß, wird die optimale Lösung u. U. verfehlt. Ist die Schrittgröße zu klein, dauert die Annäherung an die beste Lösung länger.

6. **Number of trees constructed**: (Anzahl der erstellten Bäume) Geben Sie die Gesamtzahl von Entscheidungsbäumen an, die im Ensemble erstellt werden sollen. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie u. U. eine bessere Abdeckung, allerdings verlängert sich dadurch auch die Trainingsdauer.

    Dieser Wert steuert auch die Anzahl von Bäumen, die bei der Visualisierung des trainierten Modells angezeigt werden. Um einen einzelnen Baum anzuzeigen oder auszugeben, können Sie den Wert auf 1 festlegen. Das bedeutet jedoch, dass nur ein Baum erzeugt wird (der Baum mit dem anfänglichen Parametersatz) und keine weiteren Iterationen erfolgen.

7. **Random number seed** (Zufälliger Startwert): Geben Sie eine optionale, nicht negative ganze Zahl als zufälligen Startwert an. Die Angabe eines Startwerts gewährleistet Reproduzierbarkeit in verschiedenen Ausführungen, die auf den gleichen Daten und Parametern basieren.

    Der zufällige Startwert ist standardmäßig auf „0“ festgelegt, was bedeutet, dass der ursprüngliche Startwert von der Systemuhr abgerufen wird.
  
8. **Allow unknown categorical levels** (Unbekannte Kategorieebenen zulassen): Wählen Sie diese Option, um eine Gruppe für unbekannte Werte in den Trainings- und Validierungssätzen zu erstellen. Wenn Sie diese Option deaktivieren, akzeptiert das Modell nur Werte, die in den Trainingsdaten enthalten sind. Das Modell ist bei bekannten Werten u. U. weniger genau, liefert dafür jedoch bessere Vorhersagen für neue (unbekannte) Werte.

9. Fügen Sie ein Trainingsdataset und eines der Trainingsmodule hinzu:

    - Wenn Sie die Option **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](train-model.md) (Modell trainieren) verwenden.  
  
    

10. Führen Sie das Experiment aus.  
  
### <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um den Baum anzuzeigen, der bei jeder Iteration erstellt wurde, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls [Train Model](train-model.md), und wählen Sie **Visualize** aus.
  
     Klicken Sie auf den Baum, um Detailinformationen zu den Teilen sowie Regeln für die einzelnen Knoten anzuzeigen.  

+ Wenn Sie das Modell zur Bewertung verwenden möchten, verbinden Sie es mit [Score Model](./score-model.md) (Modell bewerten), um Werte für neue Eingabebeispiele vorherzusagen.

+ Um eine Momentaufnahme des trainierten Modells zu speichern, klicken Sie mit der rechten Maustaste auf die Ausgabe **Trained model** (Trainiertes Modell) des Trainingsmoduls, und wählen Sie **Save As** (Speichern unter) aus. Die Kopie des trainierten Modells, das Sie speichern, wird bei nachfolgenden Durchläufen des Experiments nicht aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 