---
title: Was ist verteiltes Training?
titleSuffix: Azure Machine Learning
description: Erfahren Sie, welche Art von verteiltem Training Azure Machine Learning unterstützt und welche Integrationen von Open-Source-Frameworks für das verteilte Training verfügbar sind.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 4e1972e0530656bf6cc0e6319191ab4ebbfbfddb
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358574"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Verteiltes Training mit Azure Machine Learning

Dieser Artikel informiert Sie über verteiltes Training und die Unterstützung von Deep Learning-Modellen durch Azure Machine Learning. 

Beim verteilten Training wird die Arbeitsauslastung zum Trainieren eines Modells auf mehrere Miniprozessoren, die als Workerknoten bezeichnet werden, verteilt. Diese Workerknoten werden parallel ausgeführt, um das Modelltraining zu beschleunigen. Verteiltes Training kann für herkömmliche ML-Modelle verwendet werden, eignet sich jedoch besser für rechen- und zeitintensive Aufgaben, z. B. [Deep Learning](concept-deep-learning-vs-machine-learning.md) zum Trainieren von Deep Neural Networks. 

## <a name="deep-learning-and-distributed-training"></a>Deep Learning und verteiltes Training 

Es gibt zwei Haupttypen von verteiltem Training: [Datenparallelität](#data-parallelism) und [Modellparallelität](#model-parallelism). Das [Azure Machine Learning-SDK in Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) unterstützt für verteiltes Training in Deep Learning-Modellen Integrationen in die beliebten Frameworks PyTorch und TensorFlow. Beide Frameworks nutzen Datenparallelität für verteiltes Training und können mithilfe von [Horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) die Rechengeschwindigkeit optimieren. 

* [Verteiltes Training](how-to-train-pytorch.md#distributed-training) (PyTorch)

* [Verteiltes Training](how-to-train-tensorflow.md#distributed-training) (TensorFlow)

Für ML-Modelle, die kein verteiltes Training erfordern, finden Sie unter [Trainieren von Modellen mit Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) Informationen über die verschiedenen Möglichkeiten zum Trainieren von Modellen mithilfe des Python-SDK.

## <a name="data-parallelism"></a>Datenparallelität

Datenparallelität lässt sich einfacher als der andere Ansatz für verteiltes Training implementieren und ist für die meisten Anwendungsfälle ausreichend.

Hierbei werden die Daten in Partitionen unterteilt, wobei die Anzahl der Partitionen gleich der Gesamtzahl der verfügbaren Knoten im Computecluster ist. Das Modell wird in jeden dieser Workerknoten kopiert, und jeder Worker arbeitet mit einer eigenen Teilmenge der Daten. Beachten Sie, dass jeder Knoten das Modell unterstützen können muss, das trainiert wird. Das heißt, das Modell muss vollständig auf jeden Knoten passen. Im folgenden Diagramm ist dieser Ansatz dargestellt.

![Konzeptdiagramm Datenparallelität](./media/concept-distributed-training/distributed-training.svg)

Jeder Knoten berechnet eigenständig die Fehler (Abweichungen) zwischen den Vorhersagen für die Trainingsbeispiele und den gekennzeichneten Ausgaben. Jeder Knoten wiederum aktualisiert sein Modell basierend auf den Fehlern. Er muss alle seine Änderungen an die anderen Knoten übermitteln, damit diese ihre entsprechenden Modelle aktualisieren. Dies bedeutet, dass die Workerknoten die Modellparameter (oder Gradienten) am Ende der Batchberechnung synchronisieren müssen, um sicherzustellen, dass sie ein konsistentes Modell trainieren. 

## <a name="model-parallelism"></a>Modellparallelität

Bei der Modellparallelität, auch als Netzwerkparallelität bezeichnet, wird das Modell in verschiedene Teile segmentiert, die gleichzeitig auf verschiedenen Knoten ausgeführt werden können. Jeder Knoten wird für dieselben Daten ausgeführt. Die Skalierbarkeit dieser Methode hängt vom Grad der Aufgabenparallelisierung des Algorithmus ab, und sie ist schwieriger zu implementieren als Datenparallelität. 

Bei Modellparallelität müssen die Workerknoten nur die freigegebenen Parameter synchronisieren, in der Regel einmal für jeden Vorwärtspropagierungs- oder Rückpropagierungsschritt. Außerdem sind größere Modelle ohne Belang, da jeder Knoten in einem Unterabschnitt des Modells mit denselben Trainingsdaten arbeitet.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Computeziele für das Modelltraining](how-to-set-up-training-targets.md) mit dem Python SDK verwenden.
* Ein technisches Beispiel finden Sie im [Referenzarchitekturszenario](/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Trainieren von ML-Modellen mit TensorFlow](how-to-train-tensorflow.md)
* [Trainieren von ML-Modellen mit PyTorch](how-to-train-pytorch.md)