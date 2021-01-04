---
title: 'Entscheidungswald mit zwei Klassen: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe des Moduls „Entscheidungswald mit zwei Klassen“ in Azure Machine Learning ein Modell für maschinelles Lernen erstellen, das auf dem Entscheidungswald-Algorithmus basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/03/2020
ms.openlocfilehash: c4c303a7640454ba0cb6622b21fd161354266068
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375926"
---
# <a name="two-class-decision-forest-module"></a>Modul „Entscheidungswald mit zwei Klassen“

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um ein Modell für maschinelles Lernen zu erstellen, das auf dem Entscheidungswald-Algorithmus basiert.  

Entscheidungswälder sind schnelle, beaufsichtigte Ensemblemodelle. Dieses Modul ist eine gute Wahl, wenn Sie ein Ziel mit maximal zwei Ergebnissen vorhersagen möchten. 

## <a name="understanding-decision-forests"></a>Grundlegendes zu Entscheidungswäldern

Der Entscheidungswald-Algorithmus ist eine Ensemble-Lernmethode, die für Klassifizierungsaufgaben vorgesehen ist. Ensemblemethoden basieren auf dem allgemeinen Prinzip statt auf einem einzelnen Modell, Sie können bessere Ergebnisse und ein stärker generalisiertes Modell erhalten, indem Sie mehrere verwandte Modelle erstellen und sie in geeigneter Weise kombinieren. Im Allgemeinen bieten Ensemblemodelle eine bessere Abdeckung und Genauigkeit als einzelne Entscheidungsbäume. 

Es gibt viele Möglichkeiten, einzelne Modelle zu erstellen und sie in einem Ensemble zu kombinieren. Diese spezifische Implementierung eines Entscheidungswalds funktioniert dergestalt, dass mehrere Entscheidungsbäume erstellt werden und dann über die beliebteste Ausgabeklasse **abgestimmt** wird. Abstimmung ist eine der bekannteren Methoden zum Generieren von Ergebnissen in einem Ensemblemodell. 

+ Unter Verwendung des gesamten Datasets werden viele einzelne Klassifizierungsbäume erstellt, jedoch mit verschiedenen (normalerweise randomisierten) Ausgangspunkten. Dies unterscheidet sich vom Random Forest-Ansatz, bei dem die einzelnen Entscheidungsbäume nur einen randomisierten Anteil der Daten oder Merkmale verwenden können.
+ Jeder Baum im Entscheidungswald gibt ein nicht normalisiertes Häufigkeitshistogramm der Bezeichnungen aus. 
+ Der Aggregationsprozess summiert diese Histogramme und normalisiert das Ergebnis, um die „Wahrscheinlichkeiten“ für jede Bezeichnung abzurufen. 
+ Die Bäume mit hoher Vorhersagesicherheit haben bei der endgültigen Entscheidung des Ensembles eine größere Gewichtung.

Entscheidungsbäume haben ganz allgemein viele Vorteile bei Klassifizierungsaufgaben:
  
- Sie können nicht lineare Entscheidungsgrenzen erfassen.
- Für Training und Vorhersage können große Datenmengen verwendet werden, denn sie sind in Berechnung und Arbeitsspeicherauslastung effizient.
- Merkmalsauswahl ist in die Trainings- und Klassifizierungsprozesse integriert.  
- Bäume können verrauschte Daten und viele Merkmale bewältigen.  
- Sie stellen nichtparametrische Modelle dar, was bedeutet, dass sie mit Daten mit unterschiedlichen Verteilungen umgehen können. 

Allerdings kann es bei einfachen Entscheidungsbäumen zu Überanpassung an Daten kommen, und sie sind weniger generalisierbar als Baumensembles.

Weitere Informationen finden Sie unter [Entscheidungswälder](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration
  
1.  Fügen Sie Ihrer Pipeline in Azure Machine Learning das Modul **Two-Class Decision Forest** hinzu, und öffnen Sie den Bereich **Eigenschaften** des Moduls. 

    Sie finden das Modul unter **Machine Learning**. Erweitern Sie **Initialize** (Initialisieren) und dann **Classification** (Klassifizierung).  
  
2.  Wählen Sie für **Resampling method** (Methode für Wiederholungsprobennahme) die Methode aus, mit der die einzelnen Bäume erstellt wurden.  Sie können zwischen **Bagging** und **Replikation** wählen.  
  
    -   **Bagging**: Bagging ist die Abkürzung von *Bootstrap aggregating*. Bei dieser Methode basiert jeder Baum auf einer neuen Stichprobe, die mithilfe von Zufallsstichproben des ursprünglichen Datasets mit Austausch erstellt wird, bis Sie ein Dataset mit der Größe des Originals erhalten.  
  
         Die Ausgaben der Modelle werden durch *Abstimmung* kombiniert, was eine Form von Aggregation ist. Jeder Baum in einem Entscheidungswald gibt ein nicht normalisiertes Histogramm der Häufigkeit von Bezeichnungen aus. Die Aggregationsprozess führt dann die Summierung und Normalisierung dieser Histogramme durch, um die „Wahrscheinlichkeiten“ für jede Bezeichnung abzurufen. Auf diese Weise haben die Bäume mit hoher Vorhersagesicherheit bei der endgültigen Entscheidung des Ensembles eine größere Gewichtung.  
  
         Weitere Informationen finden Sie im Wikipedia-Eintrag zu „Bootstrap aggregating“.  
  
    -   **Replikation**: Bei der Replikation wird jede Struktur mit genau denselben Eingabedaten trainiert. Die Bestimmung, welches Aufteilungsprädikat für jeden Baumknoten verwendet wird, bleibt zufällig, und die entstehenden Bäume unterscheiden sich.   
  
3.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.

    -   **Parameter Range** (Parameterbereich): Wenn Sie sich hinsichtlich der besten Parameter nicht sicher sind, können Sie optimale Parameter mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md) finden. Sie geben einen Wertebereich an, woraufhin das Training über mehrere Einstellungskombinationen iteriert, um die Wertekombination zu bestimmen, die das beste Ergebnis liefert.
  
4.  Geben Sie in **Number of decision trees** die maximale Anzahl von Entscheidungsbäumen ein, die im Ensemble erstellt werden können. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie u. U. eine bessere Abdeckung, allerdings verlängert sich dadurch auch die Trainingsdauer.  
  
    > [!NOTE]
    >  Sie können den Wert auf 1 festlegen. Allerdings wird dann möglicherweise auch nur ein einzelner Baum (die Struktur mit dem anfänglichen Parametersatz) generiert, und es finden keine weiteren Iterationen statt.
  
5.  Geben Sie in **Maximum depth of the decision trees** (Maximale Tiefe der Entscheidungsbäume) einen Wert ein, um die maximale Tiefe der Entscheidungsbäume zu begrenzen. Eine größere Tiefe des Baums kann die Genauigkeit erhöhen, wobei das Risiko einer Überanpassung und einer längeren Trainingsdauer besteht.
  
  
7.  Geben Sie unter **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) die Mindestanzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.
  
     Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.  
  
8.  Wählen Sie die Option **Allow unknown values for categorical features** (Unbekannte Werte für Kategoriemerkmale zulassen) aus, um eine Gruppe für unbekannte Werte in den Trainings- oder Validierungsmengen zu erstellen. Das Modell ist bei bekannten Werten u. U. weniger genau, liefert dafür jedoch bessere Vorhersagen für neue (unbekannte) Werte. 

     Wenn Sie diese Option deaktivieren, akzeptiert das Modell nur Werte, die in den Trainingsdaten enthalten sind.
  
9. Fügen Sie ein bezeichnetes Dataset an, und trainieren Sie das Modell:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.  
    
## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben** im rechten Bereich des Moduls **Train model** aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbares Modul zu speichern.

+ Um das Modell zur Bewertung zu verwenden, fügen Sie einer Pipeline das Modul **Score Model** hinzu.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 