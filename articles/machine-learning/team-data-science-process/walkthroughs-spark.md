---
title: 'Analysen für HDInsight Spark mit PySpark, Scala: Team Data Science-Prozess'
description: Beispiele des Team Data Science-Prozesses mit exemplarischer Vorgehensweise der Nutzung von PySpark und Scala in Azure HDInsight Spark für Predictive Analytics.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ce57c37791f9fcf353d08e177eb243f4939b52a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60805530"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Exemplarische Data Science-Vorgehensweisen mit HDInsight Spark unter Verwendung von PySpark und Scala in Azure

In diesen exemplarischen Vorgehensweisen werden PySpark und Scala in einem Azure Spark-Cluster für Predictive Analytics verwendet. Diese folgen den im Team Data Science-Prozess ausgeführten Schritten. Eine Übersicht über den Team Data Science-Prozess finden Sie unter [Data Science-Prozess](overview.md). Einen Überblick über Spark in HDInsight finden Sie unter [Einführung in Spark in HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Weitere exemplarische Vorgehensweisen zu Data Science, in denen der Team Data Science-Prozess befolgt wird, sind nach der jeweils verwendeten **Plattform** gruppiert. Eine Aufschlüsselung dieser Beispiele finden Sie unter [Exemplarische Vorgehensweisen zum Ausführen des Team Data Science-Prozesses](walkthroughs.md).

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Vorhersagen von Taxitrinkgeldern mithilfe von PySpark in Azure Spark

Die exemplarische Vorgehensweise [Verwenden von Spark in Azure HDInsight](spark-overview.md) arbeitet mit Daten von New Yorker Taxis, um vorherzusagen, ob ein Trinkgeld (und in welcher Höhe) gezahlt wird. Es wird der Team Data Science-Prozess in einem Szenario mit einem [Azure HDInsight Spark-Cluster](https://azure.microsoft.com/services/hdinsight/) verwendet, um Daten aus dem öffentlich zugänglichen Dataset mit Taxifahrten und Fahrpreisen für New York zu speichern, zu untersuchen und einem Feature Engineering zu unterziehen. In diesem Übersichtsthema richten Sie einen HDInsight Spark-Cluster und die Jupyter PySpark Notebooks ein, die im weiteren Verlauf der exemplarischen Vorgehensweise verwendet werden. Diese Notebooks zeigen Ihnen, wie Sie Ihre Daten durchsuchen sowie Modelle erstellen und nutzen. Das Notebook zum erweiterten Durchsuchen von Daten und Modellieren zeigt, wie Sie die Kreuzvalidierung, das Hyper-Parameter-Sweeping und die Auswertung von Modellen einbeziehen können.

### <a name="data-exploration-and-modeling-with-spark"></a>Datenuntersuchung und -modellierung mit Spark 
Untersuchen Sie das Dataset, und erstellen und bewerten Sie die Machine Learning-Modelle. Arbeiten Sie dazu das Thema [Erstellen von binären Klassifizierungs- und Regressionsmodellen für Daten mit dem Spark MLib-Toolkit](spark-data-exploration-modeling.md) durch.

### <a name="model-consumption"></a>Nutzung von Modellen
Informationen zum Bewerten der in diesem Thema erstellten Klassifizierungs- und Regressionsmodelle finden Sie unter [Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kreuzvalidierung und Hyperparameter-Sweeping
Unter [Erweiterte Datendurchsuchung und Modellierung mit Spark](spark-advanced-data-exploration-modeling.md) erfahren Sie, wie Modelle mit Kreuzvalidierung und Hyperparameter-Sweeping trainiert werden können.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Vorhersagen von Taxitrinkgeldern mithilfe von Scala in Azure Spark

Die exemplarische Vorgehensweise [Verwenden von Scala in Azure](scala-walkthrough.md) arbeitet mit Daten zu New Yorker Taxis, um vorherzusagen, ob ein Trinkgeld (und in welcher Höhe) gezahlt wird. Gezeigt wird die Verwendung von Scala für überwachte Machine Learning-Aufgaben mit der Machine Learning-Bibliothek (MLlib) von Spark und SparkML-Paketen in einem Azure HDInsight Spark-Cluster. Sie führt Sie durch die Aufgaben, aus denen der [Data Science-Prozess](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)besteht: Erfassen und Durchsuchen von Daten, Visualisierung, Featureentwicklung, Modellierung und Modellnutzung. Die erstellten Modelle umfassen logistische und lineare Regression, Random Forests und Gradient-Boosted-Strukturen.


## <a name="next-steps"></a>Nächste Schritte

Eine Erläuterung der Hauptkomponenten, die den Team Data Science-Prozess bilden, finden Sie unter [Übersicht über den Team Data Science-Prozess](overview.md).

Eine Erläuterung des Team Data Science-Prozesslebenszyklus, mit dem Sie Ihre Data Science-Projekte strukturieren können, finden Sie unter [Team Data Science-Prozesslebenszyklus](lifecycle.md). Im Lebenszyklus sind die gesamten Schritte aufgeführt, die bei Projekten bei der Ausführung normalerweise durchlaufen werden. 

