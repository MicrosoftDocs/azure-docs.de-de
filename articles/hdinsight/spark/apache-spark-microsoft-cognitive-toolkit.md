---
title: Microsoft Cognitive Toolkit mit Azure HDInsight Spark für intensives Lernen
description: Erfahren Sie, wie ein trainiertes Microsoft Cognitive Toolkit-Modell für intensives Lernen mithilfe der Spark Python-API in einem Azure HDInsight Spark-Cluster auf ein Dataset angewendet werden kann.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: hrasheed
ms.openlocfilehash: 3462255311eaa6e418f97de5da598eb985b2a935
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695057"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Verwenden des Microsoft Cognitive Toolkit-Modells für intensives Lernen mit einem Azure HDInsight Spark-Cluster

In diesem Artikel führen Sie die folgenden Schritte aus.

1. Führen Sie ein benutzerdefiniertes Skript aus, um [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/) in einem Azure HDInsight Spark-Cluster zu installieren.

2. Laden Sie ein [Jupyter Notebook](https://jupyter.org/) in den [Apache Spark](https://spark.apache.org/)-Cluster hoch, um zu erfahren, wie ein trainiertes Deep Learning-Modell von Microsoft Cognitive Toolkit mit der [Spark Python-API (PySpark)](https://spark.apache.org/docs/0.9.0/python-programming-guide.html) auf Dateien in einem Azure Blob Storage-Konto angewendet wird.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Für dieses Tutorial wird ein Azure-Abonnement benötigt. Weitere Informationen finden Sie unter [Erstellen Sie noch heute Ihr kostenloses Azure-Konto](https://azure.microsoft.com/free).

* **Azure HDInsight Spark-Cluster**. Erstellen Sie für diesen Artikel einen Spark 2.0-Cluster. Eine Anleitung finden Sie unter [Erste Schritte: Erstellen eines Apache Spark-Clusters für Azure HDInsight und Ausführen von interaktiven Abfragen per Spark-SQL](apache-spark-jupyter-spark-sql.md).

## <a name="how-does-this-solution-flow"></a>Wir läuft diese Lösung ab?

Diese Lösung ist auf diesen Artikel und ein Jupyter Notebook aufgeteilt, das Sie als Teil dieses Tutorials hochladen können. In diesem Artikel führen Sie die folgenden Schritte aus:

* Ausführen einer Skriptaktion in einem HDInsight Spark-Cluster zum Installieren von Microsoft Cognitive Toolkit und Python-Paketen.
* Hochladen des Jupyter Notebooks, das die Lösung im HDInsight Spark-Cluster ausführt.

Die folgenden übrigen Schritte werden vom Jupyter Notebook abgedeckt.

- Laden von Beispielimages in ein Spark Resiliant Distributed Dataset oder RDD
   - Laden von Modulen und Definieren von Voreinstellungen
   - Lokales Herunterladen des Datasets in den Spark-Cluster
   - Konvertieren des Datasets in ein RDD
- Bewerten der Images mithilfe eines trainierten Cognitive Toolkit-Modells
   - Herunterladen des trainierten Cognitive Toolkit-Modells in den Spark-Cluster
   - Definieren von Funktionen, die von Workerknoten verwendet werden sollen
   - Bewerten der Images auf Workerknoten
   - Auswerten der Modellgenauigkeit


## <a name="install-microsoft-cognitive-toolkit"></a>Installieren von Microsoft Cognitive Toolkit

Sie können Microsoft Cognitive Toolkit mithilfe einer Skriptaktion in einem Spark-Cluster installieren. Skriptaktionen verwenden benutzerdefinierte Skripts zum Installieren von Komponenten im Cluster, die nicht standardmäßig verfügbar sind. Sie können das benutzerdefinierte Skript aus dem Azure-Portal verwenden. Verwenden Sie dazu das HDInsight .NET SDK oder Azure PowerShell. Sie können das Skript auch verwenden, um das Toolkit entweder als Teil der Clustererstellung zu installieren, oder sobald der Cluster ausgeführt wird. 

In diesem Artikel verwenden wir das Portal, um das Toolkit installieren, nachdem der Cluster erstellt wurde. Andere Möglichkeiten zum Ausführen des benutzerdefinierten Skripts finden Sie unter [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Eine Anleitung zur Verwendung des Azure-Portals zum Ausführen der Skriptaktion finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](../hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation). Stellen Sie sicher, dass Sie die folgenden Eingaben zum Installieren von Microsoft Cognitive Toolkit bereitstellen.

* Geben Sie einen Wert für den Skriptaktionsnamen ein.

* Geben Sie für **Bash-Skript-URI** `https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh` ein.

* Sie dürfen das Skript nur auf dem Haupt- und den Workerknoten ausführen. Deaktivieren Sie alle anderen Kontrollkästchen.

* Klicken Sie auf **Create**.

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Hochladen des Jupyter Notebooks in den Azure HDInsight Spark-Cluster

Um das Microsoft Cognitive Toolkit mit dem Azure HDInsight Spark-Cluster zu verwenden, müssen Sie das Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough.ipynb** in den Azure HDInsight Spark-Cluster hochladen. Dieses Notebook ist unter [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) auf GitHub verfügbar.

1. Klonen Sie das GitHub-Repository [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration). Anleitungen zum Klonen finden Sie unter [Cloning a repository](https://help.github.com/articles/cloning-a-repository/) (Klonen eines Repositorys).

2. Öffnen Sie im Azure-Portal das Blatt des Spark-Clusters, den Sie bereits bereitgestellt haben, und klicken Sie auf **Cluster-Dashboard** und dann auf **Jupyter Notebook**.

    Sie können das Jupyter Notebook auch starten, indem Sie zu der URL `https://<clustername>.azurehdinsight.net/jupyter/` navigieren. Ersetzen Sie \<clustername> durch den Namen Ihres HDInsight-Clusters.

3. Klicken Sie im Jupyter Notebook in der oberen rechten Ecke auf **Hochladen**, und navigieren Sie zu dem Speicherort, in dem Sie das GitHub-Repository geklont haben.

    ![Hochladen des Jupyter Notebooks in den Azure HDInsight Spark-Cluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Hochladen des Jupyter Notebooks in den Azure HDInsight Spark-Cluster")

4. Klicken Sie erneut auf **Hochladen**.

5. Sobald das Notebook hochgeladen ist, klicken Sie auf den Namen des Notebooks, folgen Sie den Anweisungen im Notebook zum Laden des Datasets, und führen Sie das Tutorial aus.

## <a name="see-also"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

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

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md
