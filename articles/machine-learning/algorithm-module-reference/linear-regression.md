---
title: 'Linear Regression: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Linear Regression in Azure Machine Learning ein Modell für lineare Regression für den Einsatz in einer Pipeline erstellen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: 809b1be4f9f12e1963ff5caeaacd109c84db154f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323656"
---
# <a name="linear-regression-module"></a>Modul „Linear Regression“
In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie dieses Modul, um ein lineares Regressionsmodell für den Einsatz in einer Pipeline zu erstellen.  Bei der linearen Regression wird versucht, eine lineare Beziehung zwischen einer oder mehreren unabhängigen Variablen und einem numerischen Ergebnis oder einer abhängigen Variablen herzustellen. 

Mit diesem Modul können Sie eine lineare Regressionsmethode definieren und anschließend ein Modell mit einem bezeichneten Dataset trainieren. Das trainierte Modell kann danach verwendet werden, um Vorhersagen zu treffen.

## <a name="about-linear-regression"></a>Informationen zur linearen Regression

Lineare Regression ist eine gängige statistische Methode, die beim maschinellen Lernen eingesetzt und um viele neue Methoden zum Anpassen der Linie und Messen von Fehlern erweitert wurde. Einfach ausgedrückt ist die Regression die Vorhersage eines numerischen Zielwerts. Lineare Regression ist immer noch eine gute Wahl, wenn Sie ein einfaches Modell für eine einfache prädiktive Aufgabe benötigen. Lineare Regression funktioniert auch gut bei hochdimensionalen, spärlichen Datasets mit wenig Komplexität.

Neben der linearen Regression unterstützt Azure Machine Learning eine Vielzahl von Regressionsmodellen. Allerdings kann der Begriff „Regression“ lose interpretiert werden, und einige Arten von Regression, die in anderen Tools geboten werden, werden nicht unterstützt.

+ Das klassische Regressionsproblem umfasst eine einzelne unabhängige Variable und eine abhängige Variable. Es wird auch als *einfaches Regressionsmodell* bezeichnet.  Dieses Modul unterstützt einfache Regression.

+ *Multiple lineare Regression* umfasst zwei oder mehr unabhängige Variablen, die zu einer einzelnen abhängigen Variablen beitragen. Probleme, bei denen mehrere Eingaben zur Vorhersage eines einzelnen numerischen Ergebnisses verwendet werden, werden auch als *multivariate lineare Regression* bezeichnet.

    Das Modul **Linear Regression** kann diese Probleme ebenso wie die meisten anderen Regressionsmodule lösen.

+ *Regression mit mehreren Bezeichnungen* ist die Aufgabe der Vorhersage mehrerer abhängiger Variablen in einem einzelnen Modell. Beispielsweise kann bei der logistischen Regression mit mehreren Bezeichnungen eine Stichprobe mehreren verschiedenen Bezeichnungen zugeordnet werden. (Dies unterscheidet sich von der Aufgabe, mehrere Ebenen innerhalb einer einzelnen Klassenvariablen vorherzusagen.)

    Diese Art von Regression wird in Azure Machine Learning nicht unterstützt. Um mehrere Variablen vorherzusagen, erstellen Sie für jede Ausgabe, die Sie vorhersagen möchten, einen separaten Lernenden.

Seit Jahren entwickeln Statistiker immer ausgereiftere Methoden für Regression. Dies gilt auch für lineare Regression. Dieses Modul unterstützt zwei Methoden zur Fehlermessung und Anpassung der Regressionslinie: die Methode der kleinsten Quadrate und den Gradientenabstieg.

- **Gradientenabstieg** ist eine Methode, die die Fehlerhäufigkeit bei jedem Schritt des Modelltrainingsprozesses minimiert. Es gibt zahlreiche Variationen des Gradientenabstiegs, dessen Optimierung für verschiedene Lernprobleme umfassend untersucht wurde. Wenn Sie diese Option für **Solution method** (Lösungsmethode) wählen, können Sie eine Vielzahl von Parametern festlegen, um die Schrittgröße, Lernrate usw. zu steuern. Diese Option unterstützt auch die Verwendung eines integrierten Parameter-Sweeps.

- **Kleinste Quadrate** ist bei linearer Regression eine der am häufigsten verwendeten Methoden. Kleinste Quadrate ist beispielsweise die Methode, die im Microsoft Excel-Tool „Analyse-Funktionen“ verwendet wird.

    „Kleinste Quadrate“ bezieht sich auf die Verlustfunktion, die den Fehler als Summe des Entfernungsquadrats vom Istwert bis zur vorhergesagten Linie berechnet und das Modell durch Minimierung des quadrierten Fehlers anpasst. Diese Methode geht von einer engen linearen Beziehung zwischen den Eingaben und der abhängigen Variablen aus.

## <a name="configure-linear-regression"></a>Konfigurieren linearer Regression

Dieses Modul unterstützt zwei Methoden zur Anpassung eines Regressionsmodells mit unterschiedlichen Optionen:

+ [Anpassen eines Regressionsmodells mithilfe der Methode der kleinsten Quadrate](#create-a-regression-model-using-ordinary-least-squares)

    Für kleine Datasets empfiehlt sich die Methode der kleinsten Quadrate. Diese sollte ähnliche Ergebnisse wie Excel liefern.
    
+ [Erstellen eines Regressionsmodells mithilfe des Onlinegradientenabstiegs](#create-a-regression-model-using-online-gradient-descent)

    Der Gradientenabstieg ist eine bessere Verlustfunktion für Modelle, die komplexer sind oder angesichts der Anzahl von Variablen zu wenig Trainingsdaten haben.

### <a name="create-a-regression-model-using-ordinary-least-squares"></a>Erstellen eines Regressionsmodells mithilfe der Methode der kleinsten Quadrate

1. Fügen Sie im Designer Ihrer Pipeline das Modul **Linear Regression Model** hinzu.

    Sie finden dieses Modul in der Kategorie **Machine Learning**. Erweitern Sie **Modell initialisieren** , dann **Regression** , und ziehen Sie das Modul **Linear Regression Model** in Ihre Pipeline.

2. Wählen Sie im Bereich **Properties** (Eigenschaften) in der Dropdownliste **Solution method** (Lösungsmethode) **Ordinary Least Squares** (Methode der kleinsten Quadrate) aus. Diese Option gibt die Berechnungsmethode an, mit der die Regressionslinie ermittelt wird.

3. Geben Sie unter **L2 regularization weight** (L2-Regularisierungsgewichtung) den Wert ein, der zur Gewichtung der L2-Regularisierung verwendet werden soll. Wir empfehlen, einen Wert ungleich 0 zu verwenden, um eine Überpassung zu vermeiden.

     Wenn Sie mehr darüber erfahren möchten, wie sich die Regularisierung auf die Modellanpassung auswirkt, lesen Sie diesen Artikel: [L1- und L2-Regularisierung für Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)

4. Aktivieren Sie die Option **Include intercept term** (Term des Schnittpunkts einbeziehen), wenn Sie den Term für den Schnittpunkt anzeigen möchten.

    Deaktivieren Sie diese Option, wenn Sie die Regressionsformel nicht überprüfen müssen.

5. Für **Random number seed** (zufälliger Startwert) können Sie optional einen Wert eingeben, um für den vom Modell verwendeten Zufallszahlengenerator einen Startwert festzulegen.

    Die Verwendung eines Startwerts ist nützlich, wenn Sie die gleichen Ergebnisse bei verschiedenen Ausführungen derselben Pipeline beibehalten möchten. Andernfalls wird standardmäßig ein von der Systemuhr stammender Wert verwendet.


7. Fügen Sie das Modul [Train Model](./train-model.md) Ihrer Pipeline hinzu, und stellen Sie eine Verbindung mit einem bezeichneten Dataset her.

8. Übermitteln Sie die Pipeline.

### <a name="results-for-ordinary-least-squares-model"></a>Ergebnisse für das Modell der kleinsten Quadrate

Nach Abschluss des Trainings:


+ Um Vorhersagen zu treffen, verbinden Sie das trainierte Modell mit dem Modul [Score Model](./score-model.md) (Bewerten des Modells) sowie mit einem Dataset mit neuen Werten. 


### <a name="create-a-regression-model-using-online-gradient-descent"></a>Erstellen eines Regressionsmodells mithilfe des Onlinegradientenabstiegs

1. Fügen Sie im Designer Ihrer Pipeline das Modul **Linear Regression Model** hinzu.

    Sie finden dieses Modul in der Kategorie **Machine Learning**. Erweitern Sie **Modell initialisieren** , dann **Regression** , und ziehen Sie das Modul **Linear Regression Model** in Ihre Pipeline.

2. Wählen Sie im Bereich **Properties** (Eigenschaften) in der Dropdown-Liste **Solution method** (Lösungsmethode) **Online Gradient Descent** (Onlinegradientenabstieg) als Berechnungsmethode zum Auffinden der Regressionslinie.

3. Geben Sie für **Create trainer mode** (Trainermodus erstellen) an, ob Sie das Modell mit einem vordefinierten Parametersatz trainieren möchten oder ob Sie es mithilfe eines Parameter-Sweeps optimieren möchten.

    + **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Netzwerk der linearen Regression konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.
    
    + **Parameter Range** (Parameterbereich): Wählen Sie diese Option, wenn Sie nicht sicher sind, welche Parameter am besten geeignet sind, und einen Parametersweep ausführen möchten. Wählen Sie einen Wertebereich aus, über den iteriert werden soll. Anschließend iteriert das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) über alle möglichen Kombinationen der von Ihnen angegebenen Einstellungen, um die Hyperparameter zur Erzielung der optimalen Ergebnisse zu bestimmen.  

   
4. Geben Sie für **Learning rate** (Lernrate) die Anfangslernrate für die stochastische Gradientenabstiegsoptimierung an.

5. Geben Sie für **Number of training epochs** (Anzahl der Trainingsepochen) einen Wert ein, der angibt, wie oft der Algorithmus durch Beispiele iterieren soll. Bei Datasets mit einer kleinen Anzahl von Beispielen sollte dieser Wert groß sein, um Konvergenz zu erreichen.

6. **Normalize features** (Features normalisieren): Wenn Sie die zum Trainieren des Modells verwendeten numerischen Daten bereits normalisiert haben, können Sie diese Option deaktivieren. Standardmäßig normalisiert das Modul alle numerischen Eingaben zu einem Bereich von 0 bis 1.

    > [!NOTE]
    > 
    > Denken Sie daran, die gleiche Normalisierungsmethode auf neue Daten anzuwenden, die für die Bewertung verwendet werden.

7. Geben Sie unter **L2 regularization weight** (L2-Regularisierungsgewichtung) den Wert ein, der zur Gewichtung der L2-Regularisierung verwendet werden soll. Wir empfehlen, einen Wert ungleich 0 zu verwenden, um eine Überpassung zu vermeiden.

    Wenn Sie mehr darüber erfahren möchten, wie sich die Regularisierung auf die Modellanpassung auswirkt, lesen Sie diesen Artikel: [L1- und L2-Regularisierung für Machine Learning](/archive/msdn-magazine/2015/february/test-run-l1-and-l2-regularization-for-machine-learning)


9. Aktivieren Sie die Option **Decrease learning rate** (Lernrate verringern), wenn Sie möchten, dass die Lernrate im weiteren Verlauf der Iterationen abnimmt.  

10. Für **Random number seed** (zufälliger Startwert) können Sie optional einen Wert eingeben, um für den vom Modell verwendeten Zufallszahlengenerator einen Startwert festzulegen. Die Verwendung eines Startwerts ist nützlich, wenn Sie die gleichen Ergebnisse bei verschiedenen Ausführungen derselben Pipeline beibehalten möchten.


12. Trainieren des Modells:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie ein mit Tags versehenes Dataset und das Modul [Train Model](train-model.md) (Modell trainieren) verbinden.  
  
    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Parameter Range** (Parameterbereich) festlegen, verbinden Sie ein markiertes Dataset, und trainieren Sie das Modell mithilfe des Moduls [Tune Model Hyperparameters](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Wenn Sie einen Parameterbereich an das Modul [Train Model](train-model.md) übergeben, wird nur der Standardwert in der Liste der Einzelparameter verwendet.  
    > 
    > Wenn Sie einen einzelnen Satz von Parameterwerten an das Modul [Tune Model Hyperparameters](tune-model-hyperparameters.md) übergeben und ein Bereich von Einstellungen für jeden Parameter erwartet wird, werden die Werte ignoriert und stattdessen die Standardwerte für den Learner verwendet.  
    > 
    > Wenn Sie die Option **Parameter Range** (Parameterbereich) auswählen und einen einzelnen Wert für einen beliebigen Parameter eingeben, wird dieser angegebene einzelne Wert während des gesamten Löschvorgangs verwendet, auch wenn andere Parameter in einem Wertebereich geändert werden.

13. Übermitteln Sie die Pipeline.

### <a name="results-for-online-gradient-descent"></a>Ergebnisse für den Onlinegradientenabstieg

Nach Abschluss des Trainings:

+ Um Vorhersagen zu treffen, verbinden Sie das trainierte Modell mit dem Modul [Score Model](./score-model.md) (Bewerten des Modells) sowie mit neuen Eingabedaten.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an.