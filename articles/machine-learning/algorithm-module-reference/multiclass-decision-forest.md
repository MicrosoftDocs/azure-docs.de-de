---
title: 'Multiclass Decision Forest: Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie mithilfe des Moduls „Multiclass Decision Forest“ (Entscheidungswald mit mehreren Klassen) in Azure Machine Learning Service ein Modell für maschinelles Lernen erstellen, das auf dem *Entscheidungswald*-Algorithmus basiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 10364d014431a500e7c38a02d47f432cd464feb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411490"
---
# <a name="multiclass-decision-forest-module"></a>Modul „Multiclass Decision Forest“ (Entscheidungswald mit mehreren Klassen)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Verwenden Sie dieses Modul, um ein Modell für maschinelles Lernen zu erstellen, das auf dem *Entscheidungswald*-Algorithmus basiert. Ein Entscheidungswald ist ein Ensemblemodell, das in kurzer Zeit eine Reihe von Entscheidungsbäumen erstellt und dabei aus mit Tags versehenen Daten lernt.

## <a name="more-about-decision-forests"></a>Weitere Informationen zu Entscheidungswäldern

Der Entscheidungswald-Algorithmus ist eine Ensemble-Lernmethode für die Klassifizierung. Der Algorithmus funktioniert dergestalt, dass mehrere Entscheidungsbäume erstellt werden und dann für die beliebteste Ausgabeklasse *abstimmt* wird. Die Abstimmung ist eine Form der Aggregation, bei der jeder Baum in einem Entscheidungswald zur Klassifizierung ein nicht normalisiertes Histogramm der Häufigkeit von Bezeichnungen ausgibt. Der Aggregationsprozess summiert diese Histogramme und normalisiert das Ergebnis, um die „Wahrscheinlichkeiten“ für jede Bezeichnung abzurufen. Die Bäume mit hoher Vorhersagesicherheit haben bei der endgültigen Entscheidung des Ensembles eine größere Gewichtung.

Entscheidungsbäume sind im Allgemeinen nichtparametrische Modelle, die demnach Daten mit unterschiedlichen Verteilungen unterstützen. In jedem Baum erfolgt für jede Klasse eine Abfolge einfacher Tests, wobei die Ebenen einer Baumstruktur erhöht werden, bis ein Blattknoten (die Entscheidung) erreicht ist.

Entscheidungsbäume haben zahlreiche Vorteile:

+ Sie können nicht lineare Entscheidungsgrenzen darstellen.
+ Sie sind während des Trainings und der Vorhersage effizient bei der Berechnung und der Arbeitsspeicherauslastung.
+ Sie führen eine integrierte Featureauswahl und -klassifizierung durch.
+ Sie sind resilient gegen störende Features.

Der Entscheidungswaldklassifizierer in Azure Machine Learning besteht aus einem Ensemble von Entscheidungsbäumen. Im Allgemeinen bieten Ensemblemodelle eine bessere Abdeckung und Genauigkeit als einzelne Entscheidungsbäume. Weitere Informationen finden Sie unter [Entscheidungsbäume](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Konfigurieren des Moduls „Multiclass Decision Forest“



1. Fügen Sie auf der Benutzeroberfläche das Modul **Multiclass Decision Forest** Ihrem Experiment hinzu. Sie finden dieses Modul unter **Machine Learning**, **Initialize** (Initialisieren), **Classification** (Klassifizierung).

2. Doppelklicken Sie auf das Modul, um den Bereich **Properties** (Eigenschaften) zu öffnen.

3. Wählen Sie für **Resampling method** (Methode für Wiederholungsprobennahme) die Methode, mit der die einzelnen Bäume erstellt wurden.  Sie können zwischen Bagging oder Replikation wählen.

    + **Bagging**: Bagging ist die Abkürzung von *Bootstrap aggregating*. Bei dieser Methode basiert jeder Baum auf einer neuen Stichprobe, die mithilfe von Zufallsstichproben des ursprünglichen Datasets mit Austausch erstellt wird, bis Sie ein Dataset mit der Größe des Originals erhalten. Die Ausgaben der Modelle werden durch *Abstimmung* kombiniert, was eine Form von Aggregation ist. Weitere Informationen finden Sie im Wikipedia-Eintrag zu „Bootstrap aggregating“.

    + **Replikation**: Bei der Replikation wird jede Struktur mit genau denselben Eingabedaten trainiert. Die Bestimmung, welches Aufteilungsprädikat für jeden Baumknoten verwendet wird, bleibt zufällig, wodurch verschiedene Bäume entstehen.

   

4. Geben Sie an, wie das Modell trainiert werden soll, indem Sie die Option **Create trainer mode** (Trainermodus erstellen) festlegen.

    + **Single Parameter** (Einzelner Parameter): Wählen Sie diese Option, wenn Sie wissen, wie Sie das Modell konfigurieren möchten, und geben Sie eine bestimmte Menge von Werten als Argumente an.


5. **Number of decision trees** (Anzahl von Entscheidungsbäumen): Geben Sie die maximale Anzahl von Entscheidungsbäumen ein, die im Ensemble erstellt werden können. Mit einer höheren Anzahl von Entscheidungsbäumen erzielen Sie möglicherweise eine bessere Abdeckung, allerdings verlängert sich dadurch ggf. die Trainingsdauer.

    Dieser Wert steuert auch die Anzahl von Bäumen in den Ergebnissen, wenn das trainierte Modell visualisiert wird. Um einen einzelnen Baum anzuzeigen oder auszugeben, können Sie den Wert auf 1 festlegen. Das bedeutet jedoch, dass nur ein Baum erzeugt werden kann (der Baum mit der anfänglichen Menge von Parametern) und keine weiteren Iterationen erfolgen.

6. **Maximum depth of the decision trees** (Maximale Tiefe der Entscheidungsbäume): Geben Sie einen Wert ein, um die maximale Tiefe der Entscheidungsbäume zu begrenzen. Eine größere Tiefe des Baums kann die Genauigkeit erhöhen, wobei das Risiko einer Überanpassung und einer längeren Trainingsdauer besteht.

7. **Number of random splits per node** (Anzahl zufälliger Aufteilungen pro Knoten): Geben Sie die Anzahl der Aufteilungen ein, die beim Erstellen der einzelnen Knoten des Baums verwendet werden sollen. Eine *Aufteilung* bedeutet, dass Features auf jeder Ebene des Baums (Knotens) zufällig aufgeteilt werden.

8. **Minimum number of samples per leaf node** (Minimale Anzahl der Stichproben pro Blattknoten): Geben Sie unter „Minimum number of samples per leaf node“ die Anzahl von Fällen an, die zum Erstellen eines Endknotens (Blatts) in einem Baum erforderlich sind. Wenn Sie diesen Wert erhöhen, erhöht sich der Schwellenwert für die Erstellung neuer Regeln.

    Bei Verwendung des Standardwerts „1“ reicht für die Erstellung einer neuen Regel beispielsweise bereits ein einzelner Fall aus. Wenn Sie den Wert auf „5“ erhöhen, müssen die Trainingsdaten mindestens fünf Fälle enthalten, die die gleichen Bedingungen erfüllen.



10. Stellen Sie eine Verbindung mit einem bezeichneten Dataset und einem der Trainingsmodule her:

    + Wenn Sie **Create trainer mode** (Trainermodus erstellen) auf **Single Parameter** (Einzelner Parameter) festlegen, müssen Sie das Modul [Train Model](./train-model.md) (Trainieren des Modells) verwenden.

11. Führen Sie das Experiment aus.

## <a name="results"></a>Ergebnisse

Nach Abschluss des Trainings:

+ Um den Baum anzuzeigen, der bei jeder Iteration erstellt wurde, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls [Train Model](./train-model.md), und wählen Sie **Visualize** aus.
+ Um die Regeln für jeden Knoten anzuzeigen, klicken Sie auf jeden Baum, um die Aufteilungen detailliert zu analysieren.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 