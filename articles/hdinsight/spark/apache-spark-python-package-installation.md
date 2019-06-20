---
title: 'Skriptaktion: Installieren von Python-Paketen mit Jupyter in Azure HDInsight'
description: Eine Schritt-für-Schritt-Anleitung zum Verwenden einer Skriptaktion für die Konfiguration von Jupyter Notebooks die mit Spark-Clustern in HDInsight verfügbar sind, sodass sie externe Python-Pakete verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2019
ms.openlocfilehash: c07326cc3a4334f1873eef2dc23da05156a93577
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574659"
---
# <a name="use-script-action-to-install-external-python-packages-for-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Installieren externer Python-Pakete für Jupyter Notebooks in Apache Spark-Clustern unter HDInsight mithilfe von Skriptaktionen
> [!div class="op_single_selector"]
> * [Verwenden von Cell Magic](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Verwenden von Skriptaktionen](apache-spark-python-package-installation.md)

Hier erfahren Sie, wie Sie mithilfe von Skriptaktionen einen [Apache Spark](https://spark.apache.org/)-Cluster unter HDInsight konfigurieren, um externe, von der Community bereitgestellte **Python**-Pakete zu verwenden, die nicht standardmäßig im Cluster enthalten sind.

> [!NOTE]  
> Sie können ein Jupyter Notebook auch mit `%%configure`-Magic zur Verwendung externer Pakete konfigurieren. Anweisungen finden Sie unter [Verwenden externer Pakete mit Jupyter Notebooks in Apache Spark-Clustern unter HDInsight](apache-spark-jupyter-notebook-use-external-packages.md).

Sie können den [Paketindex](https://pypi.python.org/pypi) nach einer vollständigen Liste mit verfügbaren Paketen durchsuchen. Sie können die Liste der verfügbaren Pakete auch aus anderen Quellen abrufen. So können Sie beispielsweise Pakete installieren, die über [conda-forge](https://conda-forge.org/feedstocks/) verfügbar gemacht wurden.

In diesem Artikel erfahren Sie anhand eines Beispiels, wie Sie das [TensorFlow](https://www.tensorflow.org/)-Paket per Skriptaktion in Ihrem Cluster installieren und über das Jupyter Notebook verwenden.

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen Folgendes:

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

   > [!NOTE]  
   > Wenn Sie noch nicht über einen Spark-Cluster unter HDInsight (Linux) verfügen, können Sie während der Clustererstellung Skriptaktionen ausführen. Informationen zum Verwenden benutzerdefinierter Skriptaktionen finden Sie in der [Dokumentation](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).
   
## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Unterstützung für Open-Source-Software in HDInsight-Clustern

Der Microsoft Azure HDInsight-Dienst verwendet eine Reihe von Open-Source-Technologien für Apache Hadoop. Microsoft Azure bietet lediglich allgemeinen Support für Open-Source-Technologien. Weitere Informationen finden Sie im Abschnitt **Supportumfang** auf der Website [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/). Der HDInsight-Dienst bietet zusätzliche Unterstützung für integrierte Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

* **Integrierte Komponenten** – Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Apache Hadoop YARN Resource Manager, die Apache Hive-Abfragesprache (HiveQL) und die Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Apache Hadoop-Clusterversionen](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).
* **Benutzerdefinierte Komponenten** – Als Benutzer des Clusters können Sie in Ihrem Workload eine beliebige in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

> [!IMPORTANT]   
> Mit dem HDInsight-Cluster bereitgestellte Komponenten werden vollständig unterstützt. Der Microsoft-Support unterstützt Sie beim Isolieren und Beheben von Problemen im Zusammenhang mit diesen Komponenten.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Der Microsoft-Support kann das Problem möglicherweise beheben, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight), [https://stackoverflow.com](https://stackoverflow.com). Für Apache-Projekte gibt es auch Projektwebsites auf [https://apache.org](https://apache.org). Beispiel: [Hadoop](https://hadoop.apache.org/).


## <a name="use-external-packages-with-jupyter-notebooks"></a>Verwenden von externen Paketen mit Jupyter Notebooks

1. Navigieren Sie vom [Azur-Portal](https://portal.azure.com/) zu Ihrem Cluster.  

2. Wenn Ihr Cluster im linken Bereich unter **Einstellungen** ausgewählt ist, wählen Sie **Skriptaktionen** aus.

3. Klicken Sie auf **+Neue übermitteln**.

4. Geben Sie die folgenden Werte für das Fenster **Skriptaktion übermitteln** ein:  


    |Parameter | Wert |
    |---|---|
    |Skripttyp | Wählen Sie in der Dropdownliste **– Benutzerdefiniert** aus.|
    |NAME |Geben Sie `tensorflow` in das Textfeld ein.|
    |Bash-Skript-URI |Geben Sie `https://hdiconfigactions.blob.core.windows.net/linuxtensorflow/tensorflowinstall.sh` in das Textfeld ein. |
    |Knotentyp(en) | Aktivieren Sie die Kontrollkästchen **Hauptknoten** und **Worker**. |

    `tensorflowinstall.sh` enthält die folgenden Befehle:

    ```bash
    #!/usr/bin/env bash
    /usr/bin/anaconda/bin/conda install --yes tensorflow
    ```

5. Klicken Sie auf **Erstellen**.  Informationen zum Verwenden benutzerdefinierter Skriptaktionen finden Sie in der [Dokumentation](../hdinsight-hadoop-customize-cluster-linux.md).

6. Warten Sie, bis das Skript abgeschlossen ist.  Im Bereich **Skriptaktionen** wird **Neue Skriptaktionen können übermittelt werden, nachdem der aktuelle Clustervorgang abgeschlossen wurde** angezeigt, während das Skript ausgeführt wird.  Eine Statusanzeige wird im Ambari UI-Fenster **Hintergrundvorgänge** angezeigt.

7. Öffnen Sie ein PySpark Jupyter-Notebook.  Informationen zu den entsprechenden Schritten finden Sie unter [Erstellen eines Jupyter-Notebooks unter Spark HDInsight](./apache-spark-jupyter-notebook-kernels.md#create-a-jupyter-notebook-on-spark-hdinsight).

    ![Erstellen eines neuen Jupyter Notebooks](./media/apache-spark-python-package-installation/hdinsight-spark-create-notebook.png "Erstellen eines neuen Jupyter Notebooks")

8. In diesem Schritt importieren Sie TensorFlow (`import tensorflow`) und führen ein Hello World-Beispiel aus. Geben Sie den folgenden Code ein:

    ```
    import tensorflow as tf
    hello = tf.constant('Hello, TensorFlow!')
    sess = tf.Session()
    print(sess.run(hello))
    ```

    Das Ergebnis sieht wie folgt aus:
    
    ![Ausführung des TensorFlow-Codes](./media/apache-spark-python-package-installation/execution.png "Ausführung des TensorFlow-Codes")

> [!NOTE]  
> Im Cluster gibt es zwei Python-Installationen. Spark verwendet die Anaconda-Python-Installation, die sich bei `/usr/bin/anaconda/bin` befindet, und verwendet standardmäßig die Python 2.7-Umgebung. Um Python 3.x zu verwenden und Pakete im PySpark3-Kernel zu installieren, verwenden Sie den Pfad zur ausführbaren Datei `conda` für diese Umgebung, und verwenden Sie den Parameter `-n` zur Angabe der Umgebung. Der Befehl `/usr/bin/anaconda/envs/py35/bin/conda install -c conda-forge ggplot -n py35` installiert z. B. das Paket `ggplot` in der Python 3.5-Umgebung über den Kanal `conda-forge`.

## <a name="seealso"></a>Weitere Informationen
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Szenarien
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen
* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Verwenden externer Pakete mit Jupyter Notebooks in Apache Spark-Clustern unter HDInsight](apache-spark-jupyter-notebook-use-external-packages.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
