---
title: Debuggen Ihres Modells
titleSuffix: Azure Machine Learning Studio
description: Debuggen von Fehlern, die von den Modulen „Train Model“ und „Score Model“ in Azure Machine Learning Studio generiert wurden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 9c505262030e5b5aa13b8d221cf1e39c4a9c7833
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60751120"
---
# <a name="debug-your-model-in-azure-machine-learning-studio"></a>Debuggen Ihres Modells in Azure Machine Learning Studio

Wenn Sie ein Modell ausführen, können die folgenden Fehler auftreten:

* Für das Modul [Train Model][train-model] tritt ein Fehler auf. 
* Das Modul [Score Model][score-model] liefert falsche Ergebnisse. 

Dieser Artikel beschreibt mögliche Ursachen für diese Fehler.


## <a name="train-model-module-produces-an-error"></a>Für das Modul „Train Model“ tritt ein Fehler auf

![Bild1](./media/debug-models/train_model-1.png)

Für das Modul [Train Model][train-model] werden zwei Eingaben erwartet:

1. Der Typ des Modells für maschinelles Lernen aus der in Azure Machine Learning Studio bereitgestellten Modellsammlung.
2. Die Trainingsdaten mit einer angegebenen Spalte „Label“, die die vorherzusagende Variable angibt. (Bei den anderen Spalten wird davon ausgegangen, dass es sich um Features handelt).

Für dieses Modul kann in den folgenden Fällen ein Fehler auftreten:

1. Die Spalte „Label“ wurde falsch angegeben. Dies kann vorkommen, wenn entweder als Bezeichner (Label) mehrere Spalten ausgewählt sind oder ein falscher Spaltenindex ausgewählt ist. Der zweite Fall gilt beispielsweise, wenn der Spaltenindex „30“ für ein Eingabedataset verwendet wird, das nur 25 Spalten umfasst.

2. Das Dataset enthält keine Spalten mit Funktionen. Wenn das Eingabedataset beispielsweise nur eine Spalte umfasst, die als Spalte „Label“ gekennzeichnet ist, sind keine Funktionen vorhanden, mit denen das Modell erstellt werden kann. In diesem Fall löst das Modul [Train Model][train-model] einen Fehler aus.

3. Das Eingabedataset (Funktionen oder Bezeichner) enthält „Infinity“ als Wert.

## <a name="score-model-module-produces-incorrect-results"></a>Das Modul „Score Model“ liefert falsche Ergebnisse.

![Bild2](./media/debug-models/train_test-2.png)

In einem typischen Trainings- und Testexperiment für das beaufsichtigte Lernen teilt das Modul [Split Data][split] das ursprüngliche Dataset in zwei Teile auf: Ein Teil wird zum Trainieren des Modells verwendet, mit dem anderen Teil wird die Leistung des trainierten Modells bewertet. Mit dem trainierten Modell werden dann die Testdaten bewertet und anschließend die Ergebnisse evaluiert, um die Genauigkeit des Modells zu bestimmen.

Für das Modul [Score Model][score-model] sind zwei Eingaben erforderlich:

1. Die Ausgabe eines trainierten Modells aus dem Modul [Train Model][train-model]
2. Ein Dataset für die Bewertung, das sich von dem Dataset unterscheidet, mit dem das Modell trainiert wurde

Es ist möglich, dass das Modul [Score Model][score-model] auch dann falsche Ergebnisse liefert, wenn das Experiment erfolgreich ausgeführt wird. Dieses Problem kann durch verschiedene Szenarios verursacht werden:

1. Wenn der angegebene Bezeichner kategorisch ist und ein Regressionsmodell für die Daten trainiert wird, gibt das [Score Model][score-model]-Modul eine falsche Ausgabe aus. Dies ist darauf zurückzuführen, dass für die Regression eine stetige Antwortvariable erforderlich ist. In diesem Fall empfiehlt sich die Verwendung eines Klassifizierungsmodells. 

2. In ähnlicher Weise liefert ein Klassifizierungsmodell möglicherweise unerwünschte Ergebnisse, wenn es für ein Dataset mit Gleitkommazahlen in der Spalte "Label" trainiert wird. Dies liegt daran, dass für die Klassifizierung eine diskrete Antwortvariable erforderlich ist, die nur Werte zulässt, die innerhalb einer begrenzten und kleinen Gruppe von Klassen liegen.

3. Wenn das Dataset für die Bewertung nicht alle Funktionen enthält, mit denen das Modell trainiert wird, gibt das [Score Model][score-model]-Modul einen Fehler aus.

4. Wenn eine Zeile im Dataset für die Bewertung einen fehlenden Wert oder einen unendlichen Wert für die zugehörigen Funktionen aufweist, gibt das [Score Model][score-model]-Modul keine Ergebnisse für diese Zeile aus.

5. Das [Score Model][score-model]-Modul generiert unter Umständen identische Ausgaben für alle Zeilen im Dataset für die Bewertung. Dies ist beispielsweise möglich, wenn bei der Klassifizierung mithilfe von Decision Forests die ausgewählte minimale Anzahl der Stichproben pro Leaf-Knoten höher als die Anzahl der verfügbaren Trainingsbeispiele ist.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

