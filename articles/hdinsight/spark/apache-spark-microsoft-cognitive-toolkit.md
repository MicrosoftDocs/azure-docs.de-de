---
title: Microsoft Cognitive Toolkit mit Azure Spark – Azure HDInsight
description: Erfahren Sie, wie ein trainiertes Microsoft Cognitive Toolkit-Modell für intensives Lernen mithilfe der Spark Python-API in einem Azure HDInsight Spark-Cluster auf ein Dataset angewendet werden kann.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: d94a4858b4c4fa99a09acc2bec88f94a64686c2d
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539138"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Verwenden des Microsoft Cognitive Toolkit-Modells für intensives Lernen mit einem Azure HDInsight Spark-Cluster

In diesem Artikel führen Sie die folgenden Schritte aus.

1. Führen Sie ein benutzerdefiniertes Skript aus, um [Microsoft Cognitive Toolkit](/cognitive-toolkit/) in einem Azure HDInsight Spark-Cluster zu installieren.

2. Laden Sie ein [Jupyter Notebook](https://jupyter.org/) in den [Apache Spark](https://spark.apache.org/)-Cluster hoch, um zu erfahren, wie ein trainiertes Deep Learning-Modell von Microsoft Cognitive Toolkit mit der [Spark Python-API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html) auf Dateien in einem Azure Blob Storage-Konto angewendet wird.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Spark-Cluster unter HDInsight. Weitere Informationen finden Sie unter [Erstellen eines Apache Spark-Clusters](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kenntnisse im Umgang mit Jupyter Notebooks mit Spark in HDInsight. Weitere Informationen finden Sie unter [Tutorial: Laden von Daten und Ausführen von Abfragen auf einem Apache Spark-Cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Wir läuft diese Lösung ab?

Diese Lösung ist auf diesen Artikel und ein Jupyter Notebook aufgeteilt, das Sie als Teil dieses Artikels hochladen können. In diesem Artikel führen Sie die folgenden Schritte aus:

* Ausführen einer Skriptaktion in einem HDInsight Spark-Cluster zum Installieren von Microsoft Cognitive Toolkit und Python-Paketen.
* Hochladen des Jupyter Notebooks, das die Lösung im HDInsight Spark-Cluster ausführt.

Die folgenden übrigen Schritte werden vom Jupyter Notebook abgedeckt.

* Laden von Beispielimages in ein Spark Resilient Distributed Dataset oder RDD
  * Laden von Modulen und Definieren von Voreinstellungen
  * Lokales Herunterladen des Datasets in den Spark-Cluster
  * Konvertieren des Datasets in ein RDD
* Bewerten der Images mithilfe eines trainierten Cognitive Toolkit-Modells
  * Herunterladen des trainierten Cognitive Toolkit-Modells in den Spark-Cluster
  * Definieren von Funktionen, die von Workerknoten verwendet werden sollen
  * Bewerten der Images auf Workerknoten
  * Auswerten der Modellgenauigkeit

## <a name="install-microsoft-cognitive-toolkit"></a>Installieren von Microsoft Cognitive Toolkit

Sie können Microsoft Cognitive Toolkit mithilfe einer Skriptaktion in einem Spark-Cluster installieren. Von Skriptaktionen werden benutzerdefinierte Skripts zum Installieren von Komponenten im Cluster verwendet, die nicht standardmäßig verfügbar sind. Sie können das benutzerdefinierte Skript aus dem Azure-Portal verwenden. Verwenden Sie dazu das HDInsight .NET SDK oder Azure PowerShell. Sie können das Skript auch verwenden, um das Toolkit entweder als Teil der Clustererstellung zu installieren, oder sobald der Cluster ausgeführt wird.

In diesem Artikel verwenden wir das Portal, um das Toolkit installieren, nachdem der Cluster erstellt wurde. Andere Möglichkeiten zum Ausführen des benutzerdefinierten Skripts finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Eine Anleitung zur Verwendung des Azure-Portals zum Ausführen der Skriptaktion finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Stellen Sie sicher, dass Sie die folgenden Eingaben zum Installieren von Microsoft Cognitive Toolkit bereitstellen. Verwenden Sie die folgenden Werte für die Skriptaktion:

|Eigenschaft |value |
|---|---|
|Skripttyp|--Benutzerdefiniert|
|Name| Installieren von MCT|
|Bash-Skript-URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Knotentyp(en):|Hauptknoten, Workerknoten|
|Parameter|Keine|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Hochladen des Jupyter Notebooks in den Azure HDInsight Spark-Cluster

Um das Microsoft Cognitive Toolkit mit dem Azure HDInsight Spark-Cluster zu verwenden, müssen Sie das Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** in den Azure HDInsight Spark-Cluster hochladen. Dieses Notebook ist unter [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) auf GitHub verfügbar.

1. Laden Sie [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) herunter, und entzippen Sie es.

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/jupyter`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.

1. Wählen Sie oben rechts im Jupyter Notebook die Option **Upload** (Hochladen) aus, navigieren Sie dann zum Download, und wählen Sie die Datei `CNTK_model_scoring_on_Spark_walkthrough.ipynb` aus.

    ![Hochladen von Jupyter Notebook in den Azure HDInsight Spark-Cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Hochladen von Jupyter Notebook in den Azure HDInsight Spark-Cluster")

1. Wählen Sie erneut **Upload** (Hochladen) aus.

1. Sobald das Notebook hochgeladen ist, klicken Sie auf den Namen des Notebooks, folgen Sie den Anweisungen im Notebook zum Laden des Datasets, und gehen Sie dem Artikel gemäß vor.

## <a name="see-also"></a>Weitere Informationen

* [Übersicht: Apache Spark für Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien

* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analysieren von Application Insights-Telemetriedaten mit Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen

* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen

* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Ressourcen verwalten

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)