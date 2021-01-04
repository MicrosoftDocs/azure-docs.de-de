---
title: Maschinelles Lernen und KI mit ONNX in Azure SQL Edge
description: Beim maschinellen Lernen in Azure SQL Edge werden Modelle im ONNX-Format (Open Neural Network Exchange) unterstützt. ONNX ist ein offenes Format, das Sie zum Austauschen von Modellen zwischen verschiedenen Frameworks und Tools für maschinelles Lernen verwenden können.
keywords: SQL Edge bereitstellen
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 5dc3d44ac4396897fd43831d51ee628bb06048cb
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392060"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge"></a>Machine Learning und KI mit ONNX in SQL Edge

Beim maschinellen Lernen in Azure SQL Edge werden Modelle im [ONNX-Format (Open Neural Network Exchange)](https://onnx.ai/) unterstützt. ONNX ist ein offenes Format, das Sie zum Austauschen von Modellen zwischen verschiedenen [Frameworks und Tools für maschinelles Lernen](https://onnx.ai/supported-tools) verwenden können.

## <a name="overview"></a>Übersicht

Zum Ableiten von Machine Learning-Modellen in Azure SQL Edge benötigen Sie zunächst ein Modell. Hierbei kann es sich um ein vorab trainiertes Modell oder ein benutzerdefiniertes Modell handeln, das mit Ihrem bevorzugten Framework trainiert wurde. Azure SQL Edge unterstützt das ONNX-Format. Sie müssen das Modell in dieses Format konvertieren. Dies sollte keinerlei Auswirkungen auf die Modellgenauigkeit haben. Nachdem Sie über das ONNX-Modell verfügen, können Sie es in Azure SQL Edge bereitstellen und die [native Bewertung mit der T-SQL-Funktion „PREDICT“](/sql/advanced-analytics/sql-native-scoring/) verwenden.

## <a name="get-onnx-models"></a>Abrufen von ONNX-Modellen

So rufen Sie ein Modell im ONNX-Format ab:

- **Modellerstellungsdienste** : Dienste wie das [automatisierte Machine Learning-Feature in Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) und der [Custom Vision-Dienst von Azure](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) unterstützen den direkten Export des trainierten Modells in das ONNX-Format.

- [**Konvertieren und/oder Exportieren vorhandener Modelle**](https://github.com/onnx/tutorials#converting-to-onnx-format): Mehrere Trainingsframeworks, z. B. [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer und Caffe2, unterstützen native Funktionen für den Export in das ONNX-Format, sodass Sie Ihr trainiertes Modell in einer bestimmten Version des ONNX-Formats speichern können. Für Frameworks, die keinen nativen Export unterstützen, gibt es eigenständige installierbare ONNX-Konverterpakete, mit denen Sie Modelle, die anhand verschiedener Machine Learning-Frameworks trainiert wurden, in das ONNX-Format konvertieren können.

     **Unterstützte Frameworks**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [TensorFlow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [Core ML](https://github.com/onnx/onnxmltools)
    
    Die vollständige Liste mit den unterstützten Frameworks und Beispielen finden Sie unter [Konvertieren in das ONNX-Format](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Einschränkungen

Derzeit werden nicht alle ONNX-Modelle von Azure SQL Edge unterstützt. Die Unterstützung ist auf Modelle mit **numerischen Datentypen** beschränkt:

- [int und bigint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [real und float](/sql/t-sql/data-types/float-and-real-transact-sql).
  
Andere numerische Typen können mithilfe von [CAST und CONVERT](/sql/t-sql/functions/cast-and-convert-transact-sql) in unterstützte Typen konvertiert werden.

Die Modelleingaben sollten so strukturiert werden, dass jede Eingabe in das Modell einer einzelnen Spalte in einer Tabelle entspricht. Wenn Sie z. B. einen Pandas-Datenrahmen zum Trainieren eines Modells verwenden, sollte jede Eingabe eine separate Spalte für das Modell sein.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von SQL Edge über das Azure-Portal](deploy-portal.md)
- [Bereitstellen eines ONNX-Modells in Azure SQL Edge](deploy-onnx.md)