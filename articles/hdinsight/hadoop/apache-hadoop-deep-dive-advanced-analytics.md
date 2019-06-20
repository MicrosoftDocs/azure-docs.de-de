---
title: Deep Dive – Advanced Analytics – Azure HDInsight
description: Erfahren Sie, wie Advanced Analytics Algorithmen zur Verarbeitung von Big Data verwenden.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: ac0edf2de4337154b665b8f3898134a7c2fd1f4c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712396"
---
# <a name="deep-dive---advanced-analytics"></a>Tief eintauchen – Advanced Analytics

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Was sind Advanced Analytics für HDInsight?

HDInsight bietet die Möglichkeit, wertvolle Erkenntnisse aus großen Mengen strukturierter, unstrukturierter und sich schnell verändernder Daten zu gewinnen. Advanced Analytics beinhalten die Verwendung hoch skalierbarer Architekturen, statistischer und Machine Learning-Modelle sowie intelligenter Dashboards zur Bereitstellung aussagekräftiger Einblicke für Sie. Machine Learning, oder *Predictive Analytics*, verwendet Algorithmen, die Beziehungen in Ihren Daten identifizieren und aus diesen lernen, um Vorhersagen zu treffen und Ihre Entscheidungen zu unterstützen.

## <a name="advanced-analytics-process"></a>Advanced Analytics-Prozesse

![Prozess](./media/apache-hadoop-deep-dive-advanced-analytics/process.png)

Nachdem Sie das geschäftliche Problem identifiziert und die Sammlung und Verarbeitung Ihrer Daten gestartet haben, müssen Sie ein Modell erstellen, das die Frage darstellt, für die Sie eine Vorhersage treffen möchten. Ihr Modell verwendet mindestens einen Machine Learning-Algorithmus, um die Art von Vorhersage zu treffen, die Ihren Geschäftsanforderungen am besten entspricht.  Die Mehrheit Ihrer Daten sollte zum Trainieren Ihres Modells verwendet werden, wobei der Rest zum Testen oder Bewerten des Modells verwendet werden sollte. 

Nach dem Erstellen, Laden, Testen und Bewerten Ihres Modells besteht der nächste Schritt darin, Ihr Modell bereitzustellen, damit es beginnt, Antworten auf Ihre Fragen zu liefern. Der letzte Schritt ist das Überwachen der Leistung Ihres Modells und nach Bedarf seine Optimierung. 

## <a name="common-types-of-algorithms"></a>Gängige Typen von Algorithmen

Advaced Analytics-Lösungen bieten eine Gruppe von Machine Learning-Algorithmen. Hier finden Sie eine Zusammenfassung der Kategorien von Algorithmen und der ihnen zugeordneten gängigen geschäftlichen Anwendungsfälle.

![Anwendungsfälle für Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/ml-use-cases.png)

Zusammen mit der Auswahl der am besten geeigneten Algorithmen müssen Sie berücksichtigen, ob Sie Daten für das Training bereitstellen müssen oder nicht. Machine Learning-Algorithmen werden wie folgt kategorisiert:

* Beaufsichtigt – Der Algorithmus muss mit einem Satz bezeichneter Daten trainiert werden, bevor er Ergebnisse liefern kann.
* Teilweise beaufsichtigt – Der Algorithmus kann durch zusätzliche Ziele, die während der ersten Trainingsphase nicht verfügbar waren, mittels interaktiver Abfragen durch einen Trainer verbessert werden.
* Unbeaufsichtigt – Der Algorithmus benötigt keine Trainingsdaten. 
* Vertiefendes Lernen – Der Algorithmus verwendet Software-Agents, um das Idealverhalten innerhalb eines bestimmten Kontexts zu bestimmen (wird häufig in der Robotik eingesetzt).


| Algorithmuskategorie| Zweck | Lerntyp | Algorithmen |
| --- | --- | --- | -- |
| Classification | Klassifizieren von Personen oder Dingen in Gruppen | Beaufsichtigt | Entscheidungsstrukturen, logistische Regression und neuronale Netzwerke |
| Clustering | Unterteilen einer Gruppe von Beispielen in homogene Gruppen | Unbeaufsichtigt | K-Means-Clustering |
| Mustererkennung | Identifizieren häufiger Zuordnungen in den Daten | Unbeaufsichtigt | Zuordnungsregeln |
| Regression | Vorhersage numerischer Ergebnisse | Beaufsichtigt | Lineare Regression, neuronale Netzwerke |
| Vertiefendes Lernen | Bestimmen des optimalen Verhaltens von Robotern | Vertiefendes Lernen | Monte Carlo-Simulationen, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine Learning in HDInsight

HDInsight verfügt über mehrere Machine Learning-Optionen für einen Advanced Analytics-Workflow:

* Machine Learning und Apache Spark
* R und ML Services
* Azure Machine Learning und Apache Hive
* Apache Spark und Deep Learning

### <a name="machine-learning-and-apache-spark"></a>Machine Learning und Apache Spark


[HDInsight Spark](../spark/apache-spark-overview.md) ist ein gehostetes Angebot von [Apache Spark](https://spark.apache.org/), einem einheitlichen Open-Source-Framework für die Parallelverarbeitung von Daten, das die arbeitsspeicherinterne Verarbeitung verwendet, um die Leistung von Big Data-Analysen zu steigern. Die Spark-Verarbeitungs-Engine ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner verteilten In-Memory-Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. 


Es gibt drei skalierbare Machine Learning-Bibliotheken, die algorithmische Modellierungsfunktionen in dieser verteilten Umgebung bereitstellen:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) – MLlib enthält die Original-API, die auf Grundlage von Spark RDDs erstellt wurde.
* [**SparkML** ](https://spark.apache.org/docs/1.2.2/ml-guide.html) – SparkML ist ein neueres Paket, das höher entwickelte API bereitstellt, die auf Spark DataFrames basiert, um ML-Pipelines zu erzeugen.
* [**MMLSpark** ](https://github.com/Azure/mmlspark) – Die Microsoft Machine Learning-Bibliothek für Apache Spark (MMLSpark) ist darauf ausgelegt, die Produktivität von Datenanalysten mit Spark zu steigern, die Experimentierrate zu erhöhen und innovative Machine Learning-Verfahren, einschließlich Deep Learning, in sehr großen Datasets zu nutzen. Die MMLSpark-Bibliothek vereinfacht gängige Modellieraufgaben zum Erstellen von Modellen in PySpark. 

### <a name="r-and-ml-services"></a>R und ML Services

Als Teil von HDInsight können Sie einen HDInsight-Cluster mit [ML Services](../r-server/r-server-overview.md) erstellen, der bereit für den Einsatz mit sehr großen Datasets und Modellen ist. Diese neue Funktion bietet Datenanalysten und Statistikern eine vertraute R-Schnittstelle, die bei Bedarf durch HDInsight skaliert werden kann, ohne den zusätzlichen Aufwand der Einrichtung und Wartung eines Clusters.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning und Apache Hive

[Azure Machine Learning Studio](https://studio.azureml.net/) verfügt über Tools zur Modellierung von Predictive Analytics-Lösungen und einen vollständig verwalteten Dienst, über den Sie Ihre Vorhersagemodelle als sofort nutzbare Webdienste bereitstellen können. Azure Machine Learning bietet Tools zum Erstellen vollständiger Predictive Analytics-Lösungen in der Cloud, um Vorhersagemodelle schnell und mühelos zu erstellen, testen, operationalisieren und verwalten. Wählen Sie aus einer großen Algorithmusbibliothek aus, verwenden Sie ein webbasiertes Studio zum Erstellen von Modellen, und stellen Sie Ihr Modell ganz einfach als Webdienst bereit.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark und Deep Learning

[Deep Learning](https://www.microsoft.com/research/group/dltc/) ist ein Nebenzweig des Machine Learning, der *Deep Neural Networks* (DNNs) verwendet, die von den biologischen Prozessen des menschliche Gehirns inspiriert sind. Viele Forscher sehen Deep Learning als vielversprechenden Ansatz für die Künstliche Intelligenz. Einige Beispiele für Deep Learning sind Übersetzungssysteme für gesprochene Sprache, Bilderkennungssysteme und Machine Reasoning (maschinelles logisches Schlussfolgern). Um seine eigenen Bestrebungen im Deep Learning voranzutreiben, hat Microsoft das kostenlose und bedienfreundliche Open-Source-[Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) entwickelt. Das Toolkit wird intensiv von einer Vielzahl verschiedener Microsoft-Produkte sowie von Firmen weltweit verwendet, die Deep Learning skaliert bereitstellen müssen, sowie von Studenten, die an den neuesten Algorithmen und Methoden interessiert sind. 

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Szenario – Bewerten von Bildern zur Identifizierung von Mustern bei der Stadtentwicklung

Betrachten wir ein Beispiel für eine Advanced Analytics-Machine Learning-Pipeline, die HDInsight verwendet.

In diesem Szenario werden Sie sehen, wie DNNs, die in einem Deep Learning-Framework, dem Cognitive Toolkit von Microsoft (CNTK), erzeugt wurden, für die Bewertung großer Bildersammlungen eingesetzt werden können, die in einem Azure Blob Storage-Konto mithilfe von PySpark in einem HDInsight Spark-Cluster gespeichert sind. Dieser Ansatz wird auf einen gängigen DNN-Anwendungsfall angewendet, die Klassifizierung von Luftbildern, und kann zur Identifizierung jüngster Muster bei der Stadtentwicklung verwendet werden.  Sie verwenden hierbei ein vortrainiertes Klassifizierungsmodell. Das Modell wurde mit dem [CIFAR 10 Dataset](https://www.cs.toronto.edu/~kriz/cifar.html) vortrainiert und auf 10.000 nicht enthaltene Bilder angewendet.

Es gibt drei wesentliche Aufgaben in diesem Szenario für Advanced Analytics:

1. Erstellen eines Azure HDInsight Hadoop-Clusters mit einer Apache Spark 2.1.0-Distribution. 
2. Ausführen eines benutzerdefinierten Skripts zum Installieren des Microsoft Cognitive Toolkits auf allen Knoten eines Azure HDInsight Spark-Clusters. 
3. Laden Sie eine vorbereitete Jupyter Notebook-Instanz in Ihren HDInsight Spark-Cluster hoch, um ein trainiertes Microsoft Cognitive Toolkit Deep Learning-Modell mithilfe der Spark Python-API (PySpark) auf Dateien in einem Azure Blob Storage-Konto anzuwenden. 

Dieses Beispiel verwendet das CIFAR-10-Bilderset, zusammengestellt und verteilt von Alex Krizhevsky, Vinod Nair und Geoffrey Hinton. Das CIFAR-10-Dataset enthält 60.000 32x32-Farbbilder, die zu 10 sich gegenseitig ausschließenden Klassen gehören:

![Bilder](./media/apache-hadoop-deep-dive-advanced-analytics/ml-images.png)

Weitere Details zu dem Dataset finden Sie in [Learning Multiple Layers of Features from Tiny Images](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf) (Lernen mehrerer Ebenen von Merkmalen aus kleinen Bildern)von Alex Krizhevsky.

Das Dataset wurde in ein Trainingsset von 50.000 Bildern und ein Testset von 10.000 Bildern partitioniert. Das erste Set wurde verwendet, um ein zwanzig Ebenen tiefes ResNet-Modell (Convolutional Residual Network) mithilfe des Microsoft Cognitive Toolkits zu trainieren, indem [dieses Lernprogramm](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) aus dem Cognitive Toolkit GitHub-Repository befolgt wurde. Die verbleibenden 10.000 Bilder wurden zum Testen der Genauigkeit des Modells verwendet. An dieser Stelle kommt Distributed Computing ins Spiel: die Aufgabe der Vorverarbeitung und Bewertung der Bilder ist in hohem Maße parallelisierbar. Mit dem gespeicherten, trainierten Modell haben wir Folgendes verwendet:

* PySpark zum Verteilen der Bilder und des trainierten Modells auf die Workerknoten des Clusters.
* Python zum Vorverarbeiten der Bilder auf den einzelnen Knoten des HDInsight Spark-Clusters.
* Cognitive Toolkit zum Laden des Modells und zum Bewerten der vorverarbeiteten Bilder auf den einzelnen Knoten.
* Jupyter Notebooks zum Ausführen des PySpark-Skripts und zum Aggregieren der Ergebnisse sowie [Matplotlib](https://matplotlib.org/) zum Visualisieren der Modellleistung.

Die gesamte Vorverarbeitung/Bewertung der 10.000 Bilder dauert auf einem Cluster mit 4 Workerknoten weniger als eine Minute. Das Modell sagt die Bezeichnungen von ~ 9.100 (91 %) Bildern exakt vorher. Eine Konfusionsmatrix veranschaulicht die häufigsten Klassifizierungsfehler. Beispielsweise zeigt die Matrix, dass die Fehlbezeichnung von Hunden als Katzen und umgekehrt häufiger vorkommt als bei anderen Bezeichnungspaaren.

![Ergebnisse](./media/apache-hadoop-deep-dive-advanced-analytics/ml-results.png)

### <a name="try-it-out"></a>Probieren Sie sie aus.

Befolgen Sie [dieses Lernprogramm](../spark/apache-spark-microsoft-cognitive-toolkit.md), um diese Lösung End-to-End zu implementieren: richten Sie einen HDInsight Spark-Cluster ein, installieren Sie das Cognitive Toolkit, und führen Sie das Jupyter Notebook aus, das 10.000 CIFAR-Bilder bewertet.

## <a name="next-steps"></a>Nächste Schritte

Apache Hive und Azure Machine Learning

* [Apache Hive und Azure Machine Learning End-to-End](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Verwenden eines Azure HDInsight Hadoop-Clusters mit einem 1-TB-Dataset](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark und MLLib

* [Machine Learning mit Apache Spark in HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Apache Spark in HDInsight und HVAC-Daten](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Apache Spark in HDInsight](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Cognitive Toolkit und anderes

* [Data Science Azure Virtual Machine](../../machine-learning/data-science-virtual-machine/overview.md)
* [Einführung von H2O.ai in Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
