---
title: 'Entscheidungswald mit zwei Klassen: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mithilfe des Moduls „Entscheidungswald mit zwei Klassen“ in Azure Machine Learning Service ein Modell für maschinelles Lernen erstellen, das auf dem Entscheidungswald-Algorithmus basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 73b7822c56e2b07eeefdedce1bce6d410d110ebc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411471"
---
# <a name="two-class-decision-forest-module"></a>Modul „Entscheidungswald mit zwei Klassen“

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für den Azure Machine Learning Service beschrieben.

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
  
1.  Fügen Sie Ihrem Experiment in Azure Machine Learning das Modul **Entscheidungswald mit zwei Klassen** hinzu, und öffnen Sie den Bereich **Eigenschaften** des Moduls. 

    Sie finden das Modul unter **Machine Learning**. Erweitern Sie **Initialize** (Initialisieren) und dann **Classification** (Klassifizierung).  
  
2.  Wählen Sie für **Resampling method** (Methode für Wiederholungsprobennahme) die Methode aus, mit der die einzelnen Bäume erstellt wurden.  Sie können zwischen **Bagging** und **Replikation** wählen.  
  
    -   **Bagging**: Bagging ist die Abkürzung von *Bootstrap aggregating*. Bei dieser Methode basiert jeder Baum auf einer neuen Stichprobe, die mithilfe von Zufallsstichproben des ursprünglichen Datasets mit Austausch erstellt wird, bis Sie ein Dataset mit der Größe des Originals erhalten.  
  
         Die Ausgaben der Modelle werden durch *Abstimmung* kombiniert, was eine Form von Aggregation ist. Jeder Baum in einem Entscheidungswald gibt ein nicht normalisiertes Histogramm der Häufigkeit von Bezeichnungen aus. Die Aggregationsprozess führt dann die Summierung und Normalisierung dieser Histogramme durch, um die „Wahrscheinlichkeiten“ für jede Bezeichnung abzurufen. Auf diese Weise haben die Bäume mit hoher Vorhersagesicherheit bei der endgültigen Entscheidung des Ensembles eine größere Gewichtung.  
  
         Weitere Informationen finden Sie im Wikipedia-Eintrag zu „Bootstrap aggregating“.  
  
    -   **Replikation**: Bei der Replikation wird jede Struktur mit genau denselben Eingabedaten trainiert. Die Bestimmung, welches Aufteilungsprädikat für jeden Baumknoten verwendet wird, bleibt zufällig, und die entstehenden Bäume unterscheiden sich.   
  
3.  Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) aktivieren.  
  
    -   **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Modell konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.
  
4.  Geben Sie in **Number of decision trees** die maximale Anzahl von Entscheidungsbäumen ein, die im Ensemble erstellt werden können. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie u. U. eine bessere Abdeckung, allerdings verlängert sich dadurch auch die Trainingsdauer.  
  
    > [!NOTE]
    >  Dieser Wert steuert auch die Anzahl von Bäumen, die bei der Visualisierung des trainierten Modells angezeigt werden. Wenn Sie eine einzelne Struktur anzeigen oder ausgeben möchten, können Sie den Wert auf „1“ festlegen. Allerdings wird dann möglicherweise auch nur ein einzelner Baum (die Struktur mit dem anfänglichen Parametersatz) generiert, und es finden keine weiteren Iterationen statt.
  
5.  Geben Sie in **Maximum depth of the decision trees** (Maximale Tiefe der Entscheidungsbäume) einen Wert ein, um die maximale Tiefe der Entscheidungsbäume zu begrenzen. Eine größere Tiefe des Baums kann die Genauigkeit erhöhen, wobei das Risiko einer Überanpassung und einer längeren Trainingsdauer besteht.
  
6.  Geben Sie in **Number of random splits per node** (Anzahl der zufälligen Aufteilungen pro Knoten) die Anzahl der Aufteilungen ein, die beim Erstellen der einzelnen Knoten des Baums verwendet werden sollen. Eine *Aufteilung* bedeutet, dass Features auf jeder Ebene des Baums (Knotens) zufällig aufgeteilt werden.
  
7.  Geben Sie unter **Minimum number of samples per leaf node** (Minimale Anzahl von Stichproben pro Blattknoten) die Mindestanzahl von Fällen an, die zum Erstellen eines Endknotens (Blatt) in einer Struktur erforderlich sind.
  
     Wenn Sie diesen Wert heraufsetzen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln. Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.  
  
8.  Wählen Sie die Option **Allow unknown values for categorical features** (Unbekannte Werte für Kategoriemerkmale zulassen) aus, um eine Gruppe für unbekannte Werte in den Trainings- oder Validierungsmengen zu erstellen. Das Modell ist bei bekannten Werten u. U. weniger genau, liefert dafür jedoch bessere Vorhersagen für neue (unbekannte) Werte. 

     Wenn Sie diese Option deaktivieren, akzeptiert das Modell nur Werte, die in den Trainingsdaten enthalten sind.
  
9. Fügen Sie ein bezeichnetes Dataset und eines der [Trainingsmodule hinzu](module-reference.md):  
  
    -   Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](./train-model.md) (Trainieren des Modells) verwenden.  
  
    
## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um den Baum anzuzeigen, der bei jeder Iteration erstellt wurde, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls [Train Model](./train-model.md), und wählen Sie **Visualize** aus.
  
    Klicken Sie auf den Baum, um Detailinformationen zu den Teilen sowie Regeln für die einzelnen Knoten anzuzeigen.

+ Um eine Momentaufnahme des Modells zu speichern, klicken Sie mit der rechten Maustaste auf die Ausgabe **Trained model** (Trainiertes Modell), und wählen Sie **Save Model** (Modell speichern) aus. Das gespeicherte Modell wird bei nachfolgenden Ausführungen des Experiments nicht aktualisiert.

+ Um das Modell zur Bewertung zu verwenden, fügen Sie einem Experiment das Modul **Score Model** (Modell bewerten) hinzu.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 