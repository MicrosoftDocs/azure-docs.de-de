---
title: 'Linear Regression: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mit dem Modul „Linear Regression“ (Linear Regression) in Azure Machine Learning Service ein lineares Regressionsmodell für den Einsatz in einem Experiment erstellen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 040f726a703d34a95bae7d5b7cdd766655c62a4e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027834"
---
# <a name="linear-regression-module"></a>Modul „Linear Regression“
In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein lineares Regressionsmodell für den Einsatz in einem Experiment zu erstellen.  Bei der linearen Regression wird versucht, eine lineare Beziehung zwischen einer oder mehreren unabhängigen Variablen und einem numerischen Ergebnis oder einer abhängigen Variablen herzustellen. 

Mit diesem Modul können Sie eine lineare Regressionsmethode definieren und anschließend ein Modell mit einem bezeichneten Dataset trainieren. Das trainierte Modell kann danach verwendet werden, um Vorhersagen zu treffen.

## <a name="about-linear-regression"></a>Informationen zur linearen Regression

Lineare Regression ist eine gängige statistische Methode, die beim maschinellen Lernen eingesetzt und um viele neue Methoden zum Anpassen der Linie und Messen von Fehlern erweitert wurde. Im einfachsten Sinne ist Regression die Vorhersage eines numerischen Zielwerts. Lineare Regression ist immer noch eine gute Wahl, wenn Sie ein einfaches Modell für eine einfache prädiktive Aufgabe benötigen. Lineare Regression funktioniert auch gut bei hochdimensionalen, spärlichen Datasets mit wenig Komplexität.

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

+ [Erstellen eines Regressionsmodells mithilfe des Onlinegradientenabstiegs](#bkmk_GradientDescent)

    Der Gradientenabstieg ist eine bessere Verlustfunktion für Modelle, die komplexer sind oder angesichts der Anzahl von Variablen zu wenig Trainingsdaten haben.



+ [Anpassen eines Regressionsmodells mithilfe der Methode der kleinsten Quadrate](#bkmk_OrdinaryLeastSquares)

    Für kleine Datasets empfiehlt sich die Methode der kleinsten Quadrate. Diese sollte ähnliche Ergebnisse wie Excel liefern.

## <a name="bkmk_OrdinaryLeastSquares"></a> Erstellen eines Regressionsmodells mithilfe der Methode der kleinsten Quadrate

1. Fügen Sie auf der Benutzeroberfläche das Modul **Linear Regression Model** (Lineares Regressionsmodell) Ihrem Experiment hinzu.

    Sie finden dieses Modul in der Kategorie **Machine Learning**. Erweitern Sie **Initialize Model** (Modell initialisieren), dann **Regression**, und ziehen Sie das Modul **Linear Regression Model** (Lineares Regressionsmodell) in Ihr Experiment.

2. Wählen Sie im Bereich **Properties** (Eigenschaften) in der Dropdownliste **Solution method** (Lösungsmethode) **Ordinary Least Squares** (Methode der kleinsten Quadrate) aus. Diese Option gibt die Berechnungsmethode an, mit der die Regressionslinie ermittelt wird.

3. Geben Sie unter **L2 regularization weight** (L2-Regularisierungsgewichtung) den Wert ein, der zur Gewichtung der L2-Regularisierung verwendet werden soll. Wir empfehlen, einen Wert ungleich 0 zu verwenden, um eine Überpassung zu vermeiden.

     Wenn Sie mehr darüber erfahren möchten, wie sich die Regularisierung auf die Modellanpassung auswirkt, lesen Sie diesen Artikel: [L1- und L2-Regularisierung für Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)

4. Aktivieren Sie die Option **Include intercept term** (Term des Schnittpunkts einbeziehen), wenn Sie den Term für den Schnittpunkt anzeigen möchten.

    Deaktivieren Sie diese Option, wenn Sie die Regressionsformel nicht überprüfen müssen.

5. Für **Random number seed** (zufälliger Startwert) können Sie optional einen Wert eingeben, um für den vom Modell verwendeten Zufallszahlengenerator einen Startwert festzulegen.

    Die Verwendung eines Startwerts ist nützlich, wenn Sie die gleichen Ergebnisse bei verschiedenen Ausführungen desselben Experiments beibehalten möchten. Andernfalls wird standardmäßig ein von der Systemuhr stammender Wert verwendet.


7. Fügen Sie das Modul [Train Model](./train-model.md) (Trainieren des Modells) Ihrem Experiment hinzu, und stellen Sie eine Verbindung mit einem bezeichneten Dataset her.

8. Führen Sie das Experiment aus.

## <a name="results-for-ordinary-least-squares-model"></a>Ergebnisse für das Modell der kleinsten Quadrate

Nach Abschluss des Trainings:

+ Um die Modellparameter anzuzeigen, klicken Sie mit der rechten Maustaste auf die Trainingsausgabe, und wählen Sie **Visualize** (Visualisieren) aus.

+ Um Vorhersagen zu treffen, verbinden Sie das trainierte Modell mit dem Modul [Score Model](./score-model.md) (Bewerten des Modells) sowie mit einem Dataset mit neuen Werten. 


## <a name="bkmk_GradientDescent"></a> Erstellen eines Regressionsmodells mithilfe des Onlinegradientenabstiegs

1. Fügen Sie auf der Benutzeroberfläche das Modul **Linear Regression Model** (Lineares Regressionsmodell) Ihrem Experiment hinzu.

    Sie finden dieses Modul in der Kategorie **Machine Learning**. Erweitern Sie **Initialize Model** (Modell initialisieren), dann **Regression**, und ziehen Sie das Modul **Linear Regression Model** (Lineares Regressionsmodell) in Ihr Experiment.

2. Wählen Sie im Bereich **Properties** (Eigenschaften) in der Dropdown-Liste **Solution method** (Lösungsmethode) **Online Gradient Descent** (Onlinegradientenabstieg) als Berechnungsmethode zum Auffinden der Regressionslinie.

3. Geben Sie für **Create trainer mode** (Trainermodus erstellen) an, ob Sie das Modell mit einem vordefinierten Parametersatz trainieren möchten oder ob Sie es mithilfe eines Parameter-Sweeps optimieren möchten.

    + **Single Parameter** (Einzelner Parameter): Wenn Sie wissen, wie Sie das Netzwerk der linearen Regression konfigurieren möchten, können Sie einen bestimmten Satz von Werten als Argumente angeben.

   
4. Geben Sie für **Learning rate** (Lernrate) die Anfangslernrate für die stochastische Gradientenabstiegsoptimierung an.

5. Geben Sie für **Number of training epochs** (Anzahl der Trainingsepochen) einen Wert ein, der angibt, wie oft der Algorithmus durch Beispiele iterieren soll. Bei Datasets mit einer kleinen Anzahl von Beispielen sollte dieser Wert groß sein, um Konvergenz zu erreichen.

6. **Normalize features** (Features normalisieren): Wenn Sie die zum Trainieren des Modells verwendeten numerischen Daten bereits normalisiert haben, können Sie diese Option deaktivieren. Standardmäßig normalisiert das Modul alle numerischen Eingaben zu einem Bereich von 0 bis 1.

    > [!NOTE]
    > 
    > Denken Sie daran, die gleiche Normalisierungsmethode auf neue Daten anzuwenden, die für die Bewertung verwendet werden.

7. Geben Sie unter **L2 regularization weight** (L2-Regularisierungsgewichtung) den Wert ein, der zur Gewichtung der L2-Regularisierung verwendet werden soll. Wir empfehlen, einen Wert ungleich 0 zu verwenden, um eine Überpassung zu vermeiden.

    Wenn Sie mehr darüber erfahren möchten, wie sich die Regularisierung auf die Modellanpassung auswirkt, lesen Sie diesen Artikel: [L1- und L2-Regularisierung für Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)


9. Aktivieren Sie die Option **Decrease learning rate** (Lernrate verringern), wenn Sie möchten, dass die Lernrate im weiteren Verlauf der Iterationen abnimmt.  

10. Für **Random number seed** (zufälliger Startwert) können Sie optional einen Wert eingeben, um für den vom Modell verwendeten Zufallszahlengenerator einen Startwert festzulegen. Die Verwendung eines Startwerts ist nützlich, wenn Sie die gleichen Ergebnisse bei verschiedenen Ausführungen desselben Experiments beibehalten möchten.


12. Fügen Sie ein bezeichnetes Dataset und eines der Trainingsmodule hinzu.

    Wenn Sie keinen Parameter-Sweep einsetzen, verwenden Sie das Modul [Train Model](train-model.md) (Trainieren des Moduls).

13. Führen Sie das Experiment aus.

## <a name="results-for-online-gradient-descent"></a>Ergebnisse für den Onlinegradientenabstieg

Nach Abschluss des Trainings:

+ Um Vorhersagen zu treffen, verbinden Sie das trainierte Modell mit dem Modul [Score Model](./score-model.md) (Bewerten des Modells) sowie mit neuen Eingabedaten.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 