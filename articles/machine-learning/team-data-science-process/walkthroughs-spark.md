---
title: 'Analysen für HDInsight Spark mit PySpark, Scala: Team Data Science-Prozess'
description: Hier finden Sie Beispiele des Team Data Science-Prozesses mit exemplarischer Vorgehensweise der Nutzung von PySpark und Scala in Azure HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320135"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Exemplarische Data Science-Vorgehensweisen mit HDInsight Spark unter Verwendung von PySpark und Scala in Azure

In diesen exemplarischen Vorgehensweisen werden PySpark und Scala in einem Azure Spark-Cluster für Predictive Analytics verwendet. Diese folgen den im Team Data Science-Prozess ausgeführten Schritten. Eine Übersicht über den Team Data Science-Prozess finden Sie unter [Data Science-Prozess](overview.md). Einen Überblick über Spark in HDInsight finden Sie unter [Einführung in Spark in HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Weitere exemplarische Vorgehensweisen zu Data Science, in denen der Team Data Science-Prozess befolgt wird, sind nach der jeweils verwendeten **Plattform** gruppiert. Eine Aufschlüsselung dieser Beispiele finden Sie unter [Exemplarische Vorgehensweisen zum Ausführen des Team Data Science-Prozesses](walkthroughs.md).

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Vorhersagen von Taxitrinkgeldern mithilfe von PySpark in Azure Spark

Die exemplarische Vorgehensweise [Verwenden von Spark in Azure HDInsight](spark-overview.md) sagt mithilfe von Daten von New Yorker Taxis voraus, ob ein Trinkgeld (und in welcher Höhe) gezahlt wird. Dieses Beispiel verwendet den Team Data Science-Prozess in einem Szenario mit einem [Azure HDInsight Spark-Cluster](https://azure.microsoft.com/services/hdinsight/), um Daten aus dem öffentlich zugänglichen Dataset mit Taxifahrten und Fahrpreisen für New York zu speichern, zu untersuchen und einem Feature Engineering zu unterziehen. Dieses Übersichtsthema verwendet einen HDInsight Spark-Cluster und Jupyter-PySpark-Notebooks. Diese Notebooks zeigen Ihnen, wie Sie Ihre Daten durchsuchen sowie Modelle erstellen und nutzen. Das Notebook zum erweiterten Durchsuchen von Daten und Modellieren zeigt, wie Sie die Kreuzvalidierung, das Hyper-Parameter-Sweeping und die Auswertung von Modellen einbeziehen können.

### <a name="data-exploration-and-modeling-with-spark"></a>Datenuntersuchung und -modellierung mit Spark 
Untersuchen Sie das Dataset, und erstellen und bewerten Sie die Machine Learning-Modelle. Arbeiten Sie dazu das Thema [Erstellen von binären Klassifizierungs- und Regressionsmodellen für Daten mit dem Spark MLib-Toolkit](spark-data-exploration-modeling.md) durch.

### <a name="model-consumption"></a>Nutzung von Modellen
Informationen zum Bewerten der in diesem Thema erstellten Klassifizierungs- und Regressionsmodelle finden Sie unter [Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kreuzvalidierung und Hyperparameter-Sweeping
Unter [Erweiterte Datendurchsuchung und Modellierung mit Spark](spark-advanced-data-exploration-modeling.md) erfahren Sie, wie Modelle mit Kreuzvalidierung und Hyperparameter-Sweeping trainiert werden können.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Vorhersagen von Taxitrinkgeldern mithilfe von Scala in Azure Spark

Die exemplarische Vorgehensweise [Data Science unter Verwendung von Scala und Spark in Azure](scala-walkthrough.md) sagt voraus, ob ein Trinkgeld (und in welcher Höhe) gezahlt wird. Gezeigt wird die Verwendung von Scala für überwachte Machine Learning-Aufgaben mit der Machine Learning-Bibliothek (MLlib) von Spark und SparkML-Paketen in einem Azure HDInsight Spark-Cluster. Sie führt Sie durch die Aufgaben, aus denen der [Data Science-Prozess](./index.yml)besteht: Erfassen und Durchsuchen von Daten, Visualisierung, Featureentwicklung, Modellierung und Modellnutzung. Die erstellten Modelle umfassen logistische und lineare Regression, Random Forests und Gradient-Boosted-Strukturen.


## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über den Team Data Science-Prozess finden Sie unter [Was ist der Team Data Science-Prozess (TDSP)?](overview.md).

Informationen zum Lebenszyklus des Team Data Science-Prozesses finden Sie unter [Lebenszyklus des Team Data Science-Prozesses](lifecycle.md). In diesem Lebenszyklus sind die gesamten Schritte aufgeführt, die bei Projekten bei der Ausführung normalerweise durchlaufen werden.