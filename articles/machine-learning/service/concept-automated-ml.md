---
title: Automatisierte ML-Algorithmusauswahl und -optimierung
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie der Azure Machine Learning-Dienst automatisch einen Algorithmus für Sie auswählen und ein Modell daraus generieren kann, um Ihnen Zeit zu sparen, indem er die von Ihnen angegebenen Parameter und Kriterien verwendet, um den besten Algorithmus für Ihr Modell auszuwählen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 05/21/2019
ms.custom: seodec18
ms.openlocfilehash: 88e4e305e0f66c61ab4d73bcfef21319b4d02946
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989815"
---
# <a name="what-is-automated-machine-learning"></a>Was ist automatisiertes maschinelles Lernen?

Automatisiertes maschinelles Lernen, auch als automatisiertes ML bezeichnet, ist der Prozess des Automatisierens der zeitaufwändigen, iterativen Aufgaben der Entwicklung eines Machine Learning-Modells. Es versetzt Data Scientists, Analysten und Entwickler in die Lage, ML-Modelle mit hoher Skalierbarkeit, Effizienz und Produktivität zu erstellen und gleichzeitig die Modellqualität zu erhalten.

Die Entwicklung traditioneller Machine Learning-Modelle ist ressourcenintensiv und erfordert viel Fachwissen und Zeit, um Dutzende von Modellen zu erstellen und zu vergleichen. Sie arbeiten mit automatisiertem ML, wenn Sie möchten, dass Azure Machine Learning mit der von Ihnen angegebenen Zielmetrik ein Modell für Sie trainiert und optimiert. Der Dienst durchläuft die ML-Algorithmen dann iterativ im Zusammenspiel mit Featureauswahlen, wobei für jede Iteration ein Modell mit einer Trainingsbewertung erzeugt wird. Je höher die Bewertung ist, desto besser wird das Modell als „passend“ für Ihre Daten angesehen.

Mit automatisiertem maschinellem Lernen verkürzen Sie die Zeit, die benötigt wird, um produktionsbereite ML-Modelle mit großer Leichtigkeit und Effizienz zu erhalten.

## <a name="when-to-use-automated-ml"></a>Nutzung von automatisiertem Machine Learning

Automatisiertes ML demokratisiert den Entwicklungsprozess eines Machine Learning-Modells und befähigt seine Benutzer, unabhängig von deren Data Science-Kenntnissen, eine durchgängige Machine Learning-Pipeline für jedes Problem zu bestimmen.

Data Scientists, Analysten und Entwickler aus den verschiedensten Branchen können automatisiertes ML für Folgendes verwenden:

+ Implementieren von Machine Learning-Lösungen ohne umfangreiche Programmierkenntnisse
+ Sparen von Zeit und Ressourcen
+ Nutzen von bewährten Methoden aus der Data Science
+ Bereitstellen flexibler Problemlösungen

## <a name="how-automated-ml-works"></a>Funktionsweise von automatisiertem ML

Mithilfe von **Azure Machine Learning Service** können Sie automatisierte ML-Trainingsexperimente mit den folgenden Schritten entwerfen und ausführen:

1. **Identifizieren des ML-Problems**, das gelöst werden soll: Klassifizierung, Vorhersage oder Regression.

1. **Angeben der Quelle und des Formats der bezeichneten Trainingsdaten**: NumPy-Arrays oder Pandas-Datenrahmen.

1. **Konfigurieren des Computeziels für das Modelltraining**, z.B. [lokaler Computer, Azure Machine Learning Computes, Remote-VMs oder Azure Databricks](how-to-set-up-training-targets.md).  Weitere Informationen zu automatisiertem Training [für eine Remoteressource](how-to-auto-train-remote.md).

1. **Konfigurieren der automatisierten Machine Learning-Parameter**, die die Anzahl der Iterationen über verschiedene Modelle, die Hyperparametereinstellungen, erweiterte Vorverarbeitung/Featurebereitstellung und die Metriken bestimmen, die bei der Ermittlung des besten Modells zu berücksichtigen sind.  Sie können die Einstellungen für ein automatisches Trainingsexperiment [im Azure-Portal](how-to-create-portal-experiments.md) oder [mit dem SDK](how-to-configure-auto-train.md) konfigurieren.

1. **Übermitteln der Trainingsausführung.**

  ![Automatisiertes maschinelles Lernen](./media/how-to-automated-ml/automl-concept-diagram.png)

Während des Trainings erstellt der Azure Machine Learning Service eine Reihe von parallelen Pipelines, die unterschiedliche Algorithmen und Parametern ausprobieren. Die Ausführung wird beendet, sobald die im Experiment definierten Beendigungskriterien erreicht werden.

Sie können die protokollierten Ausführungsinformationen auch untersuchen. Sie enthalten die während der Ausführung erfassten Metriken. Bei der Trainingsausführung wird ein serialisiertes Python-Objekt (`.pkl`-Datei) generiert, das die Vorabverarbeitung des Modells und der Daten enthält.

Obwohl die Modellerstellung automatisiert ist, können Sie auch [ermitteln, wie wichtig oder relevant Features für die generierten Modelle sind](how-to-configure-auto-train.md#explain).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Vorverarbeitung

In jedem automatisierten Machine Learning-Experiment werden Ihre Daten mit den Standardmethoden und optional durch eine erweiterte Vorverarbeitung vorverarbeitet.

### <a name="automatic-preprocessing-standard"></a>Automatische Vorverarbeitung (Standard)

In jedem automatisierten Machine Learning-Experiment werden Ihre Daten automatisch skaliert oder normalisiert, damit die Algorithmen gut funktionieren.  Während des Modelltrainings wird eine der folgenden Skalierungs- oder Normalisierungstechniken auf jedes Modell angewendet.

|Skalierung&nbsp;&&nbsp;Normalisierung| BESCHREIBUNG |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardisieren von Features durch Entfernen des Mittelwerts und Skalierung auf Einheitenvarianz.  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Transformieren von Features durch Skalieren jedes Features anhand des Mindest- und Maximalwerts dieser Spalte.  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Skalieren jedes Features anhand seines maximalen Absolutwerts. |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Diese Skalarfunktionen skaliert Features nach ihren Quantilbereich. |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Lineare Dimensionalitätsreduzierung unter Verwendung von Singulärwertzerlegung der Daten, um sie auf einen niedrigeren Dimensionsbereich zu projizieren. |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Diese Transformation führt Dimensionalitätsreduzierung durch abgeschnittene Singulärwertzerlegung (SVD) durch. Im Gegensatz zu PCA zentriert diese Schätzfunktion die Daten nicht vor der Berechnung der Singulärwertzerlegung. Dies bedeutet, dass sie mit scipy.sparse-Matrizen effizient arbeiten kann. |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Jede Stichprobe (d.h. jede Zeile der Datenmatrix) mit mindestens einer Komponente ungleich Null wird unabhängig von anderen Stichproben neu skaliert, sodass ihre Norm (l1 oder l2) gleich eins ist. |

### <a name="advanced-preprocessing-optional-featurization"></a>Erweiterte Vorverarbeitung: optionale Featurebereitstellung

Zusätzliche erweiterte Vorverarbeitung und Featurebereitstellung sind ebenfalls verfügbar, z.B. Imputation fehlender Werte, Codierung und Transformationen. [Weitere Informationen zur enthaltenen Featurebereitstellung](how-to-create-portal-experiments.md#preprocess). Diese Einstellung kann aktiviert werden über:

+ Azure-Portal: Aktivieren Sie das Kontrollkästchen **Vorverarbeitung** in den **Erweiterten Einstellungen** [mit diesen Schritten](how-to-create-portal-experiments.md).

+ Python SDK: Angeben von `"preprocess": True` für die [`AutoMLConfig`-Klasse](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig?view=azure-ml-py).

## <a name="ensemble-models"></a>Ensemblemodelle

Sie können Ensemblemodelle mit automatisiertem Machine Learning mit dem [Caruana-Algorithmus für die Ensembleauswahl mit sortierter Ensembleinitialisierung](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) trainieren. Das Lernen mit Ensembles verbessert die Ergebnisse des maschinellen Lernens und die Vorhersageleistung, da nicht einzelne Modelle verwendet, sondern zahlreiche Modelle kombiniert werden. Die Ensembleiteration erscheint als letzte Iteration Ihrer Ausführung.

## <a name="use-with-onnx-in-c-apps"></a>Verwenden von ONNX in C#-Apps

Mit Azure Machine Learning können Sie automatisiertes ML verwenden, um ein Python-Modell zu erstellen und in das ONNX-Format zu konvertieren. Die ONNX-Runtime unterstützt C#, sodass Sie das erstellte Modell automatisch in Ihren C#-Apps verwenden können, ohne dass Sie es neu codieren oder die Netzwerklatenzen in Kauf nehmen müssen, die REST-Endpunkte mit sich bringen. Testen Sie ein Beispiel für diese Vorgehensweise [in diesem Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-with-onnx/auto-ml-classification-with-onnx.ipynb).

## <a name="automated-ml-across-microsoft"></a>Automatisiertes ML bei Microsoft

Automatisiertes ML ist auch in anderen Lösungen von Microsoft verfügbar:

+ In .NET-Apps mit Visual Studio und Visual Studio Code mit [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview).
+ [HDInsight](../../hdinsight/spark/apache-spark-run-machine-learning-automl.md): Die automatisierten ML-Trainingsaufträge werden in Spark in HDInsight-Clustern parallel horizontal hochskaliert.
+ [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Modelle mithilfe des automatisierten maschinellen Lernens erstellt werden, und sehen Sie sich Beispiele dazu an:

+ [Tutorial: Automatisches Trainieren eines Klassifizierungsmodells mithilfe von Azure Automated Machine Learning](tutorial-auto-train-models.md)

+ Konfigurieren Sie die Einstellungen für ein automatisches Trainingsexperiment:
  + Verwenden Sie in der Benutzeroberfläche des Azure-Portals [die folgenden Schritte](how-to-create-portal-experiments.md).
  + Mit dem Python SDK [verwenden Sie diese Schritte](how-to-configure-auto-train.md).
  
+ Erfahren Sie, wie automatisches Training mithilfe von Zeitreihendaten funktioniert, indem Sie [diese Schritte verwenden](how-to-auto-train-forecast.md).

+ Testen Sie [Jupyter Notebook-Beispiele](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).
