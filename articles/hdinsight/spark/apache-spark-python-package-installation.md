---
title: Skriptaktion für Python-Pakete mit Jupyter in Azure HDInsight
description: Eine Schritt-für-Schritt-Anleitung zum Verwenden einer Skriptaktion für die Konfiguration von Jupyter Notebooks die mit Spark-Clustern in HDInsight verfügbar sind, sodass sie externe Python-Pakete verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/29/2020
ms.openlocfilehash: 5a0f9f9f972ec42987d6152c16e4377e399cdba5
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896411"
---
# <a name="safely-manage-python-environment-on-azure-hdinsight-using-script-action"></a>Sicheres Verwalten der Python-Umgebung in Azure HDInsight mithilfe einer Skriptaktion

Für den Spark-Cluster verfügt HDInsight mit Anaconda Python 2.7 und Python 3.5 über zwei integrierte Python-Installationen. Kunden müssen die Python-Umgebung ggf. anpassen, um z. B. externe Python-Pakete zu installieren. Hier zeigen wir die bewährte Methode zur sicheren Verwaltung von Python-Umgebungen für Apache Spark-Cluster in HDInsight.

## <a name="prerequisites"></a>Voraussetzungen

Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md). Wenn Sie noch nicht über einen Spark-Cluster in HDInsight verfügen, können Sie während der Clustererstellung Skriptaktionen ausführen. Informationen zum Verwenden benutzerdefinierter Skriptaktionen finden Sie in der [Dokumentation](../hdinsight-hadoop-customize-cluster-linux.md).

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Unterstützung für Open-Source-Software in HDInsight-Clustern

Der Dienst Microsoft Azure HDInsight verwendet eine Umgebung von Open-Source-Technologien für Apache Hadoop. Microsoft Azure bietet lediglich allgemeinen Support für Open-Source-Technologien. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zum Azure-Support](https://azure.microsoft.com/support/faq/). Der HDInsight-Dienst bietet zusätzliche Unterstützung für integrierte Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

|Komponente |BESCHREIBUNG |
|---|---|
|Integriert|Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Apache Hadoop YARN Resource Manager, die Apache Hive-Abfragesprache (HiveQL) und die Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Apache Hadoop-Clusterversionen](../hdinsight-component-versioning.md).|
|Benutzerdefiniert|Als Benutzer des Clusters können Sie in Ihrer Workload eine beliebige, in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.|

> [!IMPORTANT]
> Mit dem HDInsight-Cluster bereitgestellte Komponenten werden vollständig unterstützt. Der Microsoft-Support unterstützt Sie beim Isolieren und Beheben von Problemen im Zusammenhang mit diesen Komponenten.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Der Microsoft-Support kann das Problem möglicherweise beheben, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: [Frageseite von Microsoft Q&A (Fragen und Antworten) für HDInsight](/answers/topics/azure-hdinsight.html), `https://stackoverflow.com`. Für Apache-Projekte gibt es auch Projektwebsites bei `https://apache.org`.

## <a name="understand-default-python-installation"></a>Grundlegendes zur Python-Standardinstallation

Der Spark-Cluster in HDInsight wird mit der Anaconda-Installation erstellt. Im Cluster gibt es zwei Python-Installationen: Anaconda Python 2.7 and Python 3.5. In der folgenden Tabelle sind die Python-Standardeinstellungen für Spark, Livy und Jupyter aufgeführt.

|Einstellung |Python 2,7|Python 3.5|
|----|----|----|
|`Path`|/usr/bin/anaconda/bin|/usr/bin/anaconda/envs/py35/bin|
|Spark-Version|Standard ist 2.7|Die Konfiguration kann in „3,5“ geändert werden.|
|Livy-Version|Standard ist 2.7|Die Konfiguration kann in „3,5“ geändert werden.|
|Jupyter|PySpark-Kernel|PySpark3-Kernel|

## <a name="safely-install-external-python-packages"></a>Sicheres Installieren externer Python-Pakete

Der HDInsight-Cluster hängt von der integrierten Python-Umgebung (Python 2.7 oder 3.5) ab. Die direkte Installation von benutzerdefinierten Paketen in diesen standardmäßig integrierten Umgebungen kann zu unerwarteten Änderungen der Bibliotheksversionen führen und eine weitere Beschädigung des Clusters verursachen. Um benutzerdefinierte externe Python-Pakete für Ihre Spark-Anwendungen sicher zu installieren, führen Sie die folgenden Schritte aus.

1. Erstellen Sie mithilfe von Conda eine virtuelle Python-Umgebung. Eine virtuelle Umgebung bietet einen isolierten Raum für Ihre Projekte, ohne andere zu beeinträchtigen. Beim Erstellen der virtuellen Python-Umgebung können Sie die Python-Version angeben, die Sie verwenden möchten. Sie müssen immer noch eine virtuelle Umgebung erstellen, auch wenn Sie Python 2.7 und 3.5 verwenden möchten. Durch diese Anforderung wird sichergestellt, dass die Standardumgebung des Clusters nicht beschädigt wird. Führen Sie Skriptaktionen für alle Knoten in Ihrem Cluster mit dem folgenden Skript aus, um eine virtuelle Python-Umgebung zu erstellen.

    -   `--prefix` gibt einen Pfad zu einer virtuellen Conda-Umgebung an. Es gibt mehrere Konfigurationen, für die basierend auf dem hier angegebenen Pfad weitere Änderungen vorgenommen werden müssen. In diesem Beispiel wird py35new verwendet, da der Cluster bereits über eine bestehende virtuelle Umgebung namens py35 verfügt.
    -   `python=` gibt die Python-Version für die virtuelle Umgebung an. In diesem Beispiel wird die Version 3.5 verwendet, die gleiche Version wie die bereits im Cluster integrierte. Sie können auch andere Python-Versionen verwenden, um die virtuelle Umgebung zu erstellen.
    -   `anaconda` legt package_spec als Anaconda fest, um Anaconda-Pakete in der virtuellen Umgebung zu installieren.
    
    ```bash
    sudo /usr/bin/anaconda/bin/conda create --prefix /usr/bin/anaconda/envs/py35new python=3.5 anaconda --yes
    ```

2. Installieren Sie bei Bedarf in der erstellten virtuellen Umgebung externe Python-Pakete. Führen Sie Skriptaktionen für alle Knoten in Ihrem Cluster mit dem folgenden Skript aus, um externe Python-Pakete zu installieren. Sie müssen über sudo-Berechtigungen verfügen, um Dateien in den Ordner der virtuellen Umgebung zu schreiben.

    Durchsuchen Sie den [Paketindex](https://pypi.python.org/pypi) nach einer vollständigen Liste der verfügbaren Pakete. Sie können die Liste der verfügbaren Pakete auch aus anderen Quellen abrufen. So können Sie beispielsweise Pakete installieren, die über [conda-forge](https://conda-forge.org/feedstocks/) verfügbar gemacht wurden.

    Verwenden Sie den folgenden Befehl, wenn Sie eine Bibliothek mit der aktuellen Version installieren möchten:

    - Verwenden Sie den Conda-Kanal:

        -   `seaborn` ist der Name des Pakets, das Sie installieren möchten.
        -   `-n py35new` gibt den Namen der virtuellen Umgebung an, die gerade erstellt wird. Stellen Sie sicher, dass Sie den Namen entsprechend der von Ihnen erstellten virtuellen Umgebung ändern.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install seaborn -n py35new --yes
        ```

    - Oder verwenden Sie das PyPi-Repository, und ändern Sie `seaborn` und `py35new` entsprechend:
        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install seaborn
        ```

    Verwenden Sie den folgenden Befehl, wenn Sie eine Bibliothek mit einer spezifischen Version installieren möchten:

    - Verwenden Sie den Conda-Kanal:

        -   `numpy=1.16.1` ist der Paketname und die Version, die Sie installieren möchten.
        -   `-n py35new` gibt den Namen der virtuellen Umgebung an, die gerade erstellt wird. Stellen Sie sicher, dass Sie den Namen entsprechend der von Ihnen erstellten virtuellen Umgebung ändern.

        ```bash
        sudo /usr/bin/anaconda/bin/conda install numpy=1.16.1 -n py35new --yes
        ```

    - Oder verwenden Sie das PyPi-Repository, und ändern Sie `numpy==1.16.1` und `py35new` entsprechend:

        ```bash
        sudo /usr/bin/anaconda/envs/py35new/bin/pip install numpy==1.16.1
        ```

    Wenn Sie den Namen der virtuellen Umgebung nicht kennen, können Sie SSH an den Hauptknoten des Clusters senden und `/usr/bin/anaconda/bin/conda info -e` ausführen, um alle virtuellen Umgebungen anzuzeigen.

3. Ändern Sie die Spark- und Livy-Konfigurationen, und zeigen Sie auf die erstellte virtuelle Umgebung.

    1. Öffnen Sie die Ambari-Benutzeroberfläche, und wechseln Sie zur Spark2-Seite auf die Registerkarte „Konfigurationen“.

        ![Ändern der Spark- und Livy-Konfiguration über Ambari](./media/apache-spark-python-package-installation/ambari-spark-and-livy-config.png)

    2. Erweitern Sie „livy2-env“, und fügen Sie die unten aufgeführten Anweisungen unten hinzu. Wenn Sie die virtuelle Umgebung mit einem anderen Präfix installiert haben, ändern Sie den Pfad entsprechend.

        ```bash
        export PYSPARK_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        export PYSPARK_DRIVER_PYTHON=/usr/bin/anaconda/envs/py35new/bin/python
        ```

        ![Ändern der Livy-Konfiguration über Ambari](./media/apache-spark-python-package-installation/ambari-livy-config.png)

    3. Erweitern Sie „Advanced spark2-env“, und ersetzen Sie unten die vorhandene PYSPARK_PYTHON-Anweisung für den Export. Wenn Sie die virtuelle Umgebung mit einem anderen Präfix installiert haben, ändern Sie den Pfad entsprechend.

        ```bash
        export PYSPARK_PYTHON=${PYSPARK_PYTHON:-/usr/bin/anaconda/envs/py35new/bin/python}
        ```

        ![Ändern der Spark-Konfiguration über Ambari](./media/apache-spark-python-package-installation/ambari-spark-config.png)

    4. Speichern Sie die Änderungen, und starten Sie die betreffenden Dienste neu. Damit diese Änderungen übernommen werden, müssen Sie den Spark2-Dienst neu starten. Über die Ambari-Benutzeroberfläche werden Sie zu einem Neustart aufgefordert. Klicken Sie daher auf „Neu starten“, um alle betreffenden Dienste neu zu starten.

        ![Neustarten von Diensten](./media/apache-spark-python-package-installation/ambari-restart-services.png)

    5. Legen Sie zwei Eigenschaften für Ihre Spark-Sitzung fest, um sicherzustellen, dass der Auftrag auf die aktualisierte Spark-Konfiguration verweist: `spark.yarn.appMasterEnv.PYSPARK_PYTHON` und `spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON`. 

        Verwenden Sie die Funktion `spark.conf.set` mithilfe des Terminals oder einer Notebooks.

        ```spark
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        spark.conf.set("spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON", "/usr/bin/anaconda/envs/py35/bin/python")
        ```

        Fügen Sie die folgenden Eigenschaften zum Anforderungstext hinzu, wenn Sie Livy verwenden:

        ```
        “conf” : {
        “spark.yarn.appMasterEnv.PYSPARK_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”,
        “spark.yarn.appMasterEnv.PYSPARK_DRIVER_PYTHON”:”/usr/bin/anaconda/envs/py35/bin/python”
        }
        ```

4. Wenn Sie die neu erstellte virtuelle Umgebung unter Jupyter verwenden möchten, ändern Sie die Jupyter-Konfigurationen, und starten Sie Jupyter neu. Führen Sie Skriptaktionen für alle Headerknoten mit der folgenden Anweisung aus, um Jupyter auf die neu erstellte virtuelle Umgebung zu verweisen. Stellen Sie sicher, dass Sie den Pfad zu dem Präfix ändern, das Sie für Ihre virtuelle Umgebung angegeben haben. Nachdem Sie diese Skriptaktion ausgeführt haben, starten Sie den Jupyter-Dienst über die Ambari-Benutzeroberfläche neu, um diese Änderung zu übernehmen.

    ```bash
    sudo sed -i '/python3_executable_path/c\ \"python3_executable_path\" : \"/usr/bin/anaconda/envs/py35new/bin/python3\"' /home/spark/.sparkmagic/config.json
    ```

    Sie können die Python-Umgebung in Jupyter Notebook doppelt bestätigen, indem Sie den folgenden Code ausführen:

    ![Überprüfen der Python-Version in Jupyter Notebook](./media/apache-spark-python-package-installation/check-python-version-in-jupyter.png)

## <a name="known-issue"></a>Bekanntes Problem

Es gibt einen bekannten Fehler in den Versionen `4.7.11`, `4.7.12` und `4.8.0` von Anaconda. Wenn Ihre Skriptaktionen bei `"Collecting package metadata (repodata.json): ...working..."` nicht mehr reagieren und der Fehler `"Python script has been killed due to timeout after waiting 3600 secs"` auftritt, können Sie [dieses Skript](https://gregorysfixes.blob.core.windows.net/public/fix-conda.sh) herunterladen und damit Skriptaktionen für alle Knoten ausführen, um das Problem zu beheben.

Wenn Sie wissen möchten, welche Anaconda-Version Sie verwenden, können Sie SSH auf dem Clusterheaderknoten anwenden und `/usr/bin/anaconda/bin/conda --v` ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)
* [Externe Pakete mit Jupyter Notebooks in Apache Spark](apache-spark-jupyter-notebook-use-external-packages.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
