---
title: Maschinelles Lernen mit Apache Spark
description: Dieser Artikel bietet eine konzeptionelle Übersicht über die durch Apache Spark on Azure Synapse Analytics verfügbaren Machine Learning- und Data Science-Funktionen.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: ced78955c71f37a5a6f5231f61e5327428834f00
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919030"
---
# <a name="machine-learning-with-apache-spark"></a>Maschinelles Lernen mit Apache Spark

Apache Spark in Azure Synapse Analytics ermöglicht maschinelles Lernen mit Big Data und bietet so die Möglichkeit, wertvolle Erkenntnisse aus großen Mengen strukturierter, unstrukturierter und sich schnell verändernder Daten zu gewinnen. 

Dieser Abschnitt enthält eine Übersicht und Tutorials für Machine Learning-Workflows, einschließlich explorativer Datenanalyse, Feature Engineering, Modelltraining, Modellbewertung und Bereitstellung.  

## <a name="synapse-runtime"></a>Synapse-Runtime 
Die Synapse-Runtime ist eine zusammengestellte Umgebung, die für Data Science und maschinelles Lernen optimiert ist. Die Synapse-Runtime bietet eine Reihe von beliebten Open-Source-Bibliotheken und erstellt standardmäßig das Azure Machine Learning SDK. Die Synapse-Runtime enthält auch viele externe Bibliotheken, einschließlich PyTorch, Scikit-Learn, XGBoost und weiteren.

Weitere Informationen zu den verfügbaren Bibliotheken und zugehörigen Versionen finden Sie in der veröffentlichten [Azure Synapse Analytics-Runtime](../spark/apache-spark-version-support.md).

## <a name="exploratory-data-analysis"></a>Explorative Datenanalyse
Bei der Verwendung von Apache Spark in Azure Synapse Analytics gibt es verschiedene integrierte Optionen, die Ihnen bei der Visualisierung Ihrer Daten helfen, einschließlich Diagrammoptionen für Synapse-Notebooks, Zugriff auf beliebte Open-Source-Bibliotheken wie Seaborn und Matplotlib sowie die Integration mit Synapse SQL und Power BI.

Weitere Informationen zu den Optionen für die Datenvisualisierung und Datenanalyse finden Sie im Artikel zum [Visualisieren von Daten mithilfe von Azure Synapse-Notebooks](../spark/apache-spark-data-visualization.md).

## <a name="feature-engineering"></a>Featureentwicklung
Standardmäßig enthält die Synapse-Runtime eine Reihe von Bibliotheken, die häufig für das Feature Engineering verwendet werden. Bei großen Datasets können Spark SQL, MLlib und Koalas für das Feature Engineering verwendet werden. Bei kleineren Datasets bieten Bibliotheken von Drittanbietern wie Numpy, Pandas und Scikit-learn ebenfalls nützliche Methoden für diese Szenarien an.

## <a name="train-models"></a>Trainieren von Modellen
Beim Trainieren von Machine Learning-Modellen mithilfe von Azure Spark in Azure Synapse Analytics gibt es mehrere Möglichkeiten: Apache Spark MLlib, Azure Machine Learning und verschiedene andere Open-Source-Bibliotheken. 

Weitere Informationen zu den Möglichkeiten des maschinellen Lernens finden Sie im Artikel zum [Trainieren von Modellen in Azure Synapse Analytics](../spark/apache-spark-machine-learning-training.md).

### <a name="sparkml-and-mllib"></a>SparkML und MLlib
Dank seiner verteilten In-Memory-Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. ```spark.ml``` bietet einen einheitlichen Satz von allgemeinen APIs, die Benutzer beim Erstellen und Optimieren von Machine Learning-Pipelines unterstützen. Weitere Informationen zu ```spark.ml``` finden Sie im [Apache Spark ML-Programmierleitfaden](https://spark.apache.org/docs/1.2.2/ml-guide.html).

### <a name="azure-machine-learning-automl"></a>Azure Machine Learning AutoML
[Azure Machine Learning AutoML](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) (automatisiertes maschinelles Lernen) hilft dabei, den Prozess der Entwicklung von Machine Learning-Modellen zu automatisieren. Es versetzt Data Scientists, Analysten und Entwickler in die Lage, ML-Modelle mit hoher Skalierbarkeit, Effizienz und Produktivität zu erstellen und gleichzeitig die Modellqualität zu erhalten. Die Komponenten zur Ausführung des Azure Machine Learning AutoML SDKs sind direkt in die Synapse-Runtime integriert.

### <a name="open-source-libraries"></a>Open-Source-Bibliotheken
Jeder Apache Spark-Pool in Azure Synapse Analytics wird mit einem Satz vorab geladener und beliebter Machine Learning-Bibliotheken bereitgestellt.  Zu den relevanten Machine Learning-Bibliotheken, die standardmäßig enthalten sind, gehören die folgenden:

- [Scikit-learn](https://scikit-learn.org/stable/index.html) ist eine der beliebtesten Machine Learning-Bibliotheken für einen Knoten für klassische ML-Algorithmen. Scikit-learn unterstützt die meisten beaufsichtigten und unbeaufsichtigten Lernalgorithmen und kann ebenfalls für Data Mining und Datenanalyse verwendet werden.
  
- [XGBoost](https://xgboost.readthedocs.io/en/latest/) ist eine beliebte Machine Learning-Bibliothek, die optimierte Algorithmen zum Trainieren von Entscheidungsstrukturen und zufälligen Gesamtstrukturen enthält. 
  
- [PyTorch](https://pytorch.org/) & [Tensorflow](https://www.tensorflow.org/) sind leistungsfähige Deep Learning-Bibliotheken von Python. Innerhalb eines Apache Spark-Pools in Azure Synapse Analytics können Sie diese Bibliotheken verwenden, um Modelle mit einzelnen Computern zu erstellen, indem Sie die Anzahl von Executors in Ihrem Pool auf Null festlegen. Auch wenn Apache Spark in dieser Konfiguration nicht funktionsfähig ist, stellt es eine einfache und kostengünstige Möglichkeit dar, Modelle mit einzelnen Computern zu erstellen.

## <a name="track-model-development"></a>Nachverfolgen der Modellentwicklung
[MLFlow](https://www.mlflow.org/) ist eine Open-Source-Bibliothek zum Verwalten des Lebenszyklus Ihrer Machine Learning-Experimente. MLflow Tracking ist eine Komponente von MLflow, die Metriken zu Trainingsausführungen und Modellartefakte protokolliert und nachverfolgt. Weitere Informationen zur Verwendung von MLFlow Tracking über Azure Synapse Analytics und Azure Machine Learning finden Sie in diesem Tutorial zum [Verwenden von MLFlow](https://docs.microsoft.com/azure/machine-learning/how-to-use-mlflow).

## <a name="model-scoring"></a>Modellbewertung
Die Modellbewertung (oder Rückschließen) ist die Phase, in der ein Modell zum Erstellen von Vorhersagen verwendet wird. Für die Modellbewertung mit SparkML oder MLLib können Sie die nativen Spark-Methoden nutzen, um Rückschlüsse direkt über einen Spark DataFrame zu ziehen. Sie könne für andere Open-Source-Bibliotheken und Modelltypen auch eine Spark-UDF erstellen, um den Rückschluss für große Datasets aufzuskalieren. Für kleinere Datasets können Sie auch die von der Bibliothek bereitgestellten nativen Modellrückschlussmethoden verwenden.

## <a name="register-and-serve-models"></a>Registrieren und Bereitstellen von Modellen
Die Registrierung eines Modells ermöglicht es Ihnen, Metadaten zu Modellen in Ihrem Arbeitsbereich zu speichern, zu versionieren und zu verfolgen. Nachdem Sie das Training Ihres Modells abgeschlossen haben, können Sie Ihr Modell in der [Azure Machine Learning-Modellregistrierung](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment#register-package-and-deploy-models-from-anywhere) registrieren. Nach der Registrierung können ONNX-Modelle auch zum [Erweitern der Daten](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) verwendet werden, die in dedizierten SQL-Pools gespeichert werden.

## <a name="next-steps"></a>Nächste Schritte
Für die ersten Schritte mit dem maschinellen Lernen in Azure Synapse Analytics sollten Sie sich unbedingt die folgenden Tutorials ansehen:
- [Analysieren von Daten mit Azure Synapse-Notebooks](../spark/apache-spark-data-visualization-tutorial.md)

- [Trainieren eines Machine Learning-Modells mit AutoML](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Trainieren eines Machine Learning-Modells mit Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
  