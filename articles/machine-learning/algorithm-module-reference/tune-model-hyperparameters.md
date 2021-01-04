---
title: Tune Model Hyperparameters
titleSuffix: Azure Machine Learning
description: Verwenden Sie das Tune Model Hyperparameters-Modul im Designer, um eine Parameterbereinigung zur Optimierung von Hyperparametern durchzuführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420630"
---
# <a name="tune-model-hyperparameters"></a>Tune Model Hyperparameters

In diesem Artikel wird die Verwendung des Moduls „Tune Model Hyperparameters“ im Azure Machine Learning-Designer beschrieben. Das Ziel besteht darin, die optimalen Hyperparameter für ein Machine Learning-Modell zu ermitteln. Vom Modul werden mehrere Modelle erstellt und getestet, indem verschiedene Kombinationen von Einstellungen verwendet werden. Die Metriken werden über alle Modelle hinweg verglichen, um die Kombinationen der Einstellungen zu erhalten. 

Die Begriffe *Parameter* und *Hyperparameter* können verwirrend sein. Die *Parameter* des Modells sind die Optionen, die Sie im rechten Bereich des Moduls festgelegt haben. Im Grunde genommen führt dieses Modul eine *Parameterbereinigung* für die angegebenen Parametereinstellungen durch. Es wird ein optimaler Satz mit _Hyperparametern_ erlernt. Diese können sich für jede spezifische Entscheidungsstruktur, jedes Dataset oder jede Regressionsmethode unterscheiden. Der Prozess zum Finden der optimalen Konfiguration wird manchmal als *Optimierung* bezeichnet. 

Das Modul unterstützt die folgende Methode zum Ermitteln der optimalen Einstellungen für ein Modell: *Integriertes Trainieren und Optimieren*. Bei dieser Methode konfigurieren Sie einen Satz mit Parametern zur späteren Verwendung. Anschließend können Sie für das Modul den Durchlauf mit mehreren Kombinationen durchführen. Das Modul misst die Genauigkeit, bis das „beste“ Modell ermittelt wurde. Bei den meisten Lernmodulen können Sie wählen, welche Parameter während des Trainingsprozesses geändert werden und welche unverändert bleiben sollen.

Abhängig davon, wie lange der Optimierungsprozess ausgeführt werden soll, treffen Sie unter Umständen die Entscheidung, alle Kombinationen eingehend zu testen. Sie können den Prozess auch verkürzen, indem Sie ein Raster mit Parameterkombinationen einrichten und eine zufällig ausgewählte Teilmenge des Parameterrasters testen.

Diese Methode generiert ein trainiertes Modell, das Sie zur Wiederverwendung speichern können.  

> [!TIP] 
> Sie können eine entsprechende Aufgabe ausführen. Vor Beginn einer Optimierung sollten Sie eine Auswahl von Merkmalen (Featureauswahl) anwenden, um die Spalten oder Variablen zu bestimmen, die den größten Informationswert haben.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Konfigurieren von „Tune Model Hyperparameters“  

Zum Erlernen der optimalen Hyperparameter für ein Machine Learning-Modell ist eine Pipelinenutzung in erheblichem Maße erforderlich.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Trainieren eines Modells mit einer Parameterbereinigung  

In diesem Abschnitt wird beschrieben, wie eine einfache Parameterbereinigung ausgeführt wird, bei der ein Modell mit dem Modul „Tune Model Hyperparameters“ trainiert wird.

1.  Fügen Sie das Modul „Tune Model Hyperparameters“ Ihrer Pipeline im Designer hinzu.

2.  Verbinden Sie ein untrainiertes Modell mit der am weitesten links befindlichen Eingabe. 

    > [!NOTE] 
    > **Tune Model Hyperparameters** kann nur mit integrierten Algorithmusmodulen des maschinellen Lernens verbunden werden und unterstützt keine angepassten Modelle, die in **Create Python Model** (Python-Modell erstellen) erstellt wurden.


3.  Fügen Sie das Dataset hinzu, das Sie für das Training verwenden möchten, und verbinden Sie es mit der mittleren Eingabe von „Tune Model Hyperparameters“.  

    Wenn Sie ein mit Tags versehenes Dataset haben, können Sie es optional mit dem am weitesten rechts befindlichen Eingabeport (**Optional validation dataset**, „Optionales Validierungsdataset“) verbinden. Dies ermöglicht es Ihnen, die Genauigkeit beim Training und bei der Optimierung zu messen.

4.  Wählen Sie im rechten Bereich von „Tune Model Hyperparameters“ einen Wert für **Parameter sweeping mode** (Parameterbereinigungsmodus) aus. Mit dieser Option wird gesteuert, wie die Parameter ausgewählt werden.

    - **Entire grid** (Gesamtes Raster): Wenn Sie diese Option auswählen, durchläuft das Modul ein vom System vordefiniertes Raster in einer Schleife, um unterschiedliche Kombinationen auszuprobieren und das beste Lernmodul zu ermitteln. Diese Option ist nützlich, falls Sie die besten Parametereinstellungen nicht kennen und alle möglichen Kombinationen von Werten ausprobieren möchten.

    - **Random sweep** (Zufällige Bereinigung): Wenn Sie diese Option auswählen, wählt das Modul nach dem Zufallsprinzip Parameterwerte für einen vom System definierten Bereich aus. Sie müssen die maximale Anzahl von Ausführungen angeben, die das Modul ausführen soll. Diese Option ist nützlich, wenn Sie die Modellleistung mit den von Ihnen gewünschten Metriken steigern, aber weiterhin Computeressourcen sparen möchten.    

5.  Öffnen Sie für **Label column** (Bezeichnungsspalte) die Spaltenauswahl, um eine einzelne Bezeichnungsspalte auszuwählen.

6.  Auswählen der Anzahl von Ausführungen:

    - **Maximum number of runs on random sweep**: Wenn Sie eine zufällige Bereinigung auswählen, können Sie angeben, wie oft das Modell mit einer zufälligen Kombination von Parameterwerten trainiert werden soll.

7.  Wählen Sie unter **Rang** eine Metrik aus, die zum Zuweisen eines Rangs für die Modelle verwendet werden soll.

    Wenn Sie eine Parameterbereinigung ausführen, werden vom Modul alle zutreffenden Metriken für den Modelltyp berechnet und im Bericht **Sweep results** (Bereinigungsergebnisse) zurückgegeben. Das Modul nutzt separate Metriken für Regressions- und Klassifizierungsmodelle.

    Die von Ihnen gewählte Metrik bestimmt aber, wie die Modelle eingestuft werden. Nur das oberste Modell, eingestuft entsprechend der gewählten Metrik, wird als trainiertes Modell ausgegeben, das für die Bewertung verwendet werden soll.

8.  Geben Sie für **Random seed** (Zufälliger Ausgangswert) eine Zahl ein, die zum Starten der Parameterbereinigung verwendet werden soll. 

9. Übermitteln der Pipeline

## <a name="results-of-hyperparameter-tuning"></a>Ergebnisse einer Hyperparameteroptimierung

Nach Abschluss des Trainings:

+ Um die Bereinigungsergebnisse anzuzeigen, könnten Sie entweder mit der rechten Maustaste auf das Modul klicken und dann **Visualisieren** auswählen oder mit der rechten Maustaste auf den linken Ausgangsport des Moduls klicken, um ihn zu visualisieren.

    Die **Bereinigungsergebnisse** enthält die gesamte Parameterbereingung und alle Genauigkeitsmetriken, die für den Modelltyp zutreffen. Und anhand der Metrik, die Sie für die Einstufung ausgewählt haben, wird bestimmt, welches Modell als das „beste“ angesehen wird.

+ Um eine Momentaufnahme des trainierten Modells zu speichern, wählen Sie die Registerkarte **Ausgaben und Protokolle** im rechten Bereich des Moduls **Train model** (Modell trainieren) aus. Wählen Sie das Symbol **Register dataset** (Dataset registrieren) aus, um das Modell als wiederverwendbares Modul zu speichern.


## <a name="technical-notes"></a>Technische Hinweise

Dieser Abschnitt enthält Details und Tipps zur Implementierung.

### <a name="how-a-parameter-sweep-works"></a>Funktionsweise einer Parameterbereingung

Beim Einrichten einer Parameterbereinigung definieren Sie den Bereich Ihrer Suche. Bei der Suche kann eine endliche Anzahl von zufällig ausgewählten Parametern verwendet werden. Es kann auch eine umfassende Suche in einem von Ihnen definierten Parameterbereich durchgeführt werden.

+ **Random sweep** (Zufällige Bereinigung): Mit dieser Option wird ein Modell mit einer festgelegten Anzahl von Iterationen trainiert. 

  Sie geben einen Wertebereich an, der durchlaufen werden soll, und im Modul wird eine zufällig gewählte Teilmenge dieser Werte verwendet. Werte werden mit Ersetzung ausgewählt, d. h., dass zuvor zufällig ausgewählte Zahlen nicht aus dem Pool der verfügbaren Zahlen entfernt werden. Daher bleibt die Wahrscheinlichkeit für die Auswahl eines Werts in allen Durchläufen gleich.  

+ **Entire grid** (Gesamtes Raster): Die Option zum Verwenden des gesamten Rasters bedeutet, dass jede mögliche Kombination getestet wird. Diese Option ist die gründlichste, benötigt aber auch die meiste Zeit. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Steuern der Länge und Komplexität eines Trainings

Ein Iterieren über viele Kombinationen von Einstellungen kann zeitaufwändig sein, daher bietet das Modul mehrere Möglichkeiten, den Prozess zu beschränken:

+ Begrenzen der Anzahl von Iterationen, die zum Testen eines Modells verwendet werden
+ Begrenzen des Parameterbereichs
+ Begrenzen der Anzahl von Iterationen und des Parameterbereichs

Es empfiehlt sich, mit den Einstellungen zu experimentieren, um die effizienteste Methode des Trainings für ein bestimmtes Dataset und Modell zu ermitteln.

### <a name="choosing-an-evaluation-metric"></a>Auswählen einer Auswertungsmetrik

Am Ende des Testvorgangs wird vom Modell ein Bericht angezeigt, in dem die Genauigkeit für jedes Modell angegeben ist, damit Sie die Metrikergebnisse überprüfen können:

- Für alle Modelle für die Binärklassifizierung wird ein einheitlicher Metriksatz verwendet.
- Die Genauigkeit wird für alle Klassifizierungsmodelle mit mehreren Klassen verwendet.
- Für Regressionsmodelle wird ein anderer Metriksatz genutzt. 

Während eines Trainings müssen Sie jedoch eine *einzige* Metrik auswählen, die für die Einstufung der Modelle verwendet werden soll, die während des Optimierungsprozesses generiert werden. Unter Umständen stellen Sie fest, dass die beste Metrik je nach Ihrem Geschäftsproblem und dem Aufwand für falsch positive und falsche negative Ergebnisse variiert.

#### <a name="metrics-used-for-binary-classification"></a>Metriken, die für Binärklassifizierung verwendet werden

-   **Accuracy** (Treffergenauigkeit): Anteil von richtigen Ergebnissen an den Gesamtfällen  

-   **Precision** (Genauigkeit): Anteil von richtigen Ergebnisse an allen positiven Ergebnissen  

-   **Recall** (Trefferquote): Anteil aller richtigen Ergebnisse an allen Ergebnissen  

-   **F-score** (F-Maß): Maß, das Genauigkeit und Trefferquote abgleicht  

-   **AUC**: Wert, der den Bereich unter der Kurve darstellt, wenn falsch positive Ergebnisse auf der x-Achse geplottet und richtig positive Ergebnisse auf der y-Achse geplottet werden  

-   **Average Log Loss** (Logarithmische Durchschnittsdämpfung): Differenz zwischen zwei Wahrscheinlichkeitsverteilungen – der tatsächlichen und der im Modell  

#### <a name="metrics-used-for-regression"></a>Metriken, die für Regression verwendet werden

-   **Mean absolute error** (Mittlerer absoluter Fehler): Mittelt den gesamten Fehler im Modell, wobei mit *Fehler* der Abstand des vorhergesagten Werts zum wahren Wert gemeint ist. Wird häufig als *MAE* abgekürzt.  

-   **Root mean squared error** (Standardfehler der Regression) misst den Durchschnitt der Quadrate der Fehler und berechnet dann die Quadratwurzel dieses Werts. Wird häufig als *RMSE* abgekürzt.  

-   **Relative absolute error** (Relativer absoluter Fehler) entspricht dem Fehler als Prozentsatz des richtigen Werts.  

-   **Relative squared error** (Relativer quadratischer Fehler): Normalisiert den gesamten quadratischen Fehler, indem dieser durch den gesamten quadratischen Fehler der vorhergesagten Werte dividiert wird.  

-   **Coefficient of determination** (Bestimmtheitsmaß): Einzelne Zahl, die angibt, wie gut Daten zu einem Modell passen. Der Wert „1“ bedeutet, dass das Modell genau mit den Daten übereinstimmt. Der Wert „0“ bedeutet, dass die Daten zufälliger Art sind oder aus anderen Gründen nicht an das Modell angepasst werden können. Häufig lautet die Bezeichnung hierfür *r <sup>2</sup>* , *R <sup>2</sup>* oder *r-squared* (R zum Quadrat).  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Module, die keine Parameterbereinigung unterstützen

Fast alle Lernmodule in Azure Machine Learning unterstützen Kreuzvalidierung mit einer integrierten Parameterbereinigung, mit der Sie die Parameter auswählen können, mit denen experimentiert werden soll. Ist es nicht möglich, für das Lernmodul einen Wertebereich festzulegen, können Sie es trotzdem in Kreuzvalidierungen verwenden. In diesem Fall wird ein Bereich mit zulässigen Werten für die Bereinigung ausgewählt. 


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 

