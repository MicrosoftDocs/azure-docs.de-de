---
title: Übersicht über Machine Learning – Azure HDInsight
description: In diesem Artikel werden die Optionen für das maschinelle Lernen in HDInsight beschrieben.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: hrasheed
ms.openlocfilehash: ca69424ecbddc068fdaf8bb5839a647701ee27fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686587"
---
# <a name="machine-learning-on-hdinsight"></a>Machine Learning in HDInsight

HDInsight ermöglicht Machine Learning mit Big Data und bietet so die Möglichkeit, wertvolle Erkenntnisse aus großen Mengen (Petabytes, ja sogar Exabytes) strukturierter, unstrukturierter und sich schnell verändernder Daten zu gewinnen. In HDInsight gibt es mehrere Optionen für maschinelles Lernen:  SparkML und Apache Spark MLlib, R, Apache Hive und das Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML und MLlib

[HDInsight Spark](spark/apache-spark-overview.md) ist ein in Azure gehostetes Angebot von [Apache Spark](https://spark.apache.org/), einem einheitlichen Open-Source-Framework für die Parallelverarbeitung von Daten, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Big Data-Analysen zu steigern. Die Spark-Verarbeitungs-Engine ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner verteilten In-Memory-Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. Es gibt zwei skalierbare Machine Learning-Bibliotheken, die algorithmische Modellierungsfunktionen in dieser verteilten Umgebung bereitstellen: MLlib und SparkML. MLlib enthält die Original-API, die auf Grundlage von RDDs erstellt wurde. SparkML ist ein neueres Paket, das eine API auf höherer Ebene bereitstellt, die auf Dataframes basiert, um ML-Pipelines zu erstellen. SparkML unterstützt noch nicht alle Funktionen von MLlib, ersetzt jedoch MLlib als Machine Learning-Standardbibliothek in Spark.

Die Microsoft Machine Learning-Bibliothek für Apache Spark ist [MMLSpark](https://github.com/Azure/mmlspark). Diese Bibliothek ist darauf ausgelegt, die Produktivität von Datenanalysten mit Spark zu steigern, die Experimentierrate zu erhöhen und innovative Verfahren für das maschinelle Lernen, einschließlich Deep Learning, in sehr großen Datasets zu nutzen. MMLSpark bietet zusätzlich zu den SparkML-APIs auf niedriger Ebene eine weitere Ebene beim Erstellen skalierbarer ML-Modelle, z.B. zum Indizieren von Zeichenfolgen, Umwandeln von Daten in ein von Algorithmen für maschinelles Lernen erwartetes Layout und Zusammenstellen von Featurevektoren. Die MMLSpark-Bibliothek vereinfacht diese und andere gängige Aufgaben zum Erstellen von Modellen in PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) ist derzeit die weltweit am häufigsten verwendete Programmiersprache für statistische Berechnungen. Es handelt sich um ein Open-Source-Datenvisualisierungstool mit einer Community von mehr als 2,5 Millionen Benutzern, die stetig wächst. Mit der stark wachsenden Benutzerbasis und mehr als 8.000 verteilten Paketen ist R die wahrscheinlichste Wahl für viele Unternehmen, die maschinelles Lernen nutzen möchten. Sie können einen HDInsight-Cluster mit ML Services erstellen, der bereit für den Einsatz mit sehr großen Datasets und Modellen ist. Diese Funktion bietet Datenanalysten und Statistikern eine vertraute R-Schnittstelle, die bei Bedarf durch HDInsight skaliert werden kann – ohne den zusätzlichen Aufwand der Einrichtung und Wartung eines Clusters.

![Training für die Vorhersage mit R Server](./media/hdinsight-machine-learning-overview/r-training.png)

Der Edgeknoten eines Clusters ist ein praktischer Ort für die Verbindungsherstellung mit dem Cluster und die Ausführung Ihrer R-Skripts.  Außerdem können Sie R-Skripts über die Knoten des Clusters hinweg ausführen, indem Sie Hadoop MapReduce von ScaleR oder Spark-Computekontexte verwenden.

Mit ML Services in HDInsight mit Spark können Sie das Training auf den Knoten eines Clusters durch Verwenden eines Spark-Computekontexts parallel ausführen. Sie können R-Skripts direkt auf dem Edgeknoten ausführen und bei Bedarf alle verfügbaren Kerne parallel verwenden. Alternativ können Sie den Code auch über den Edgeknoten ausführen, um die Verarbeitung zu starten, die auf alle Knoten im Cluster verteilt wird. ML Services in HDInsight mit Spark ermöglicht zudem bei Bedarf das Parallelisieren der Funktionen von Open-Source-R-Paketen.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning und Apache Hive

Azure Machine Learning verfügt über Tools zur Modellierung von Predictive Analytics-Lösungen und einen vollständig verwalteten Dienst, über den Sie Ihre Vorhersagemodelle als sofort nutzbare Webdienste bereitstellen können. Azure Machine Learning ist eine vollständige Predictive Analytics-Lösung in der Cloud, die Sie zum Erstellen, Testen, Operationalisieren und Verwalten von Vorhersagemodelle verwenden können. Wählen Sie aus einer großen Algorithmusbibliothek aus, verwenden Sie ein webbasiertes Studio zum Erstellen von Modellen, und stellen Sie Ihr Modell ganz einfach als Webdienst bereit.

![Verfügbarkeit erweiterter Analysen für Hadoop mit Microsoft Azure Machine Learning](./media/hdinsight-machine-learning-overview/hadoop-azure-ml.png)

Sie können Features für Daten in einem HDInsight Hadoop-Cluster mit [Hive-Abfragen](../machine-learning/team-data-science-process/create-features-hive.md) erstellen. Bei der *Featureentwicklung* wird versucht, die Vorhersageleistung der Lernalgorithmen durch Erstellen von Merkmalen aus Rohdaten zu verbessern und mit diesen den Lernprozess zu vereinfachen. Mithilfe des [Import Data-Moduls](../machine-learning/studio/import-data.md) können Sie HiveQL-Abfragen von Azure Machine Learning Studio ausführen und auf in Hive verarbeitete und in Blob Storage gespeicherte Daten zugreifen.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Deep Learning](https://www.microsoft.com/en-us/research/group/dltc/) ist ein Nebenzweig des maschinellen Lernens, der neuronale Netze verwendet, die an die biologischen Prozesse des menschlichen Gehirns angelehnt sind. Viele Forscher sehen Deep Learning als vielversprechenden Ansatz zur Weiterentwicklung der künstlichen Intelligenz. Beispiele für Deep Learning sind Übersetzungssysteme für gesprochene Sprache, Bilderkennungssysteme und Machine Reasoning (maschinelles logisches Schlussfolgern).

Um seine eigenen Bestrebungen im Deep Learning voranzutreiben, hat Microsoft das kostenlose und bedienfreundliche Open-Source-[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) entwickelt. Dieses Toolkit wird von einer Vielzahl verschiedener Microsoft-Produkte sowie von Firmen weltweit verwendet, die Deep Learning skaliert bereitstellen müssen, sowie von Studenten, die an den neuesten Algorithmen und Methoden interessiert sind.

## <a name="see-also"></a>Weitere Informationen

### <a name="scenarios"></a>Szenarien

* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Generieren von Filmempfehlungen mithilfe von Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive und Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive und Azure Machine Learning End-to-End](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine Learning mit Apache Spark in HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Deep Learning-Ressourcen

* [Verwenden des Microsoft Cognitive Toolkit-Modells für intensives Lernen mit einem Azure HDInsight Spark-Cluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Verwenden von Caffe in Azure HDInsight Spark für verteiltes Deep Learning](spark/apache-spark-deep-learning-caffe.md)
* [Deep Learning- und KI-Frameworks für die Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-deep-learning-ai-frameworks.md)
