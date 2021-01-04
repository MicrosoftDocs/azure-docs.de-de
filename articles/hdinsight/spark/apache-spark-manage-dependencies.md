---
title: Verwalten der Abhängigkeiten von Spark-Anwendungen unter Azure HDInsight
description: Dieser Artikel enthält eine Einführung in die Verwaltung von Spark-Abhängigkeiten in HDInsight Spark-Clustern bei PySpark- und Scala-Anwendungen.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b2cd50b1b35b87b1a11301ddc36ac355bef20dc4
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780615"
---
# <a name="manage-spark-application-dependencies"></a>Verwalten der Abhängigkeiten von Spark-Anwendungen

In diesem Artikel erfahren Sie, wie Sie Abhängigkeiten für Spark-Anwendungen verwalten, die unter HDInsight ausgeführt werden. Es werden sowohl Scala- als auch PySpark-Spark im Bereich der Spark-Anwendungen und im Clusterbereich behandelt.

Mithilfe der Direktlinks gelangen Sie jeweils zu dem für Ihren Anwendungsfall passenden Abschnitt:
* [Einrichten von JAR-Abhängigkeiten bei Spark-Aufträgen mithilfe von Jupyter Notebook](#use-jupyter-notebook)
* [Einrichten von JAR-Abhängigkeiten bei Spark-Aufträgen mit dem Azure-Toolkit für IntelliJ](#use-azure-toolkit-for-intellij)
* [Konfigurieren von JAR-Abhängigkeiten für Spark-Cluster](#jar-libs-for-cluster)
* [Sicheres Verwalten von JAR-Abhängigkeiten](#safely-manage-jar-dependencies)
* [Einrichten von Python-Paketen für Spark-Aufträge mithilfe von Jupyter Notebook](#use-jupyter-notebook-1)
* [Sicheres Verwalten von Python-Paketen für Spark-Cluster](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>JAR-Bibliotheken für einen Spark-Auftrag
### <a name="use-jupyter-notebook"></a>Verwenden von Jupyter Notebook
Wenn eine Spark-Sitzung in Jupyter Notebook im Spark-Kernel für Scala gestartet wird, können Pakete über folgende Optionen konfiguriert werden:

* [Maven Repository](https://search.maven.org/) oder von der Community bereitgestellte Pakete unter [Spark Packages](https://spark-packages.org/) (Spark-Pakete).
* JAR-Dateien, die im primären Speicher Ihres Clusters gespeichert sind.

Verwenden Sie den Magic-Befehl `%%configure`, um das Notebook so zu konfigurieren, dass es ein externes Paket verwendet. Stellen Sie sicher, dass Notebooks, die externe Pakete verwenden, die `%%configure` -Magic in der ersten Codezelle aufrufen. Dadurch wird sichergestellt, dass der Kernel für die Verwendung des Pakets konfiguriert ist, bevor die Sitzung gestartet wird.

>
>[!IMPORTANT]  
>Wenn Sie vergessen, den Kernel in der ersten Zelle zu konfigurieren, können Sie `%%configure` mit dem Parameter `-f` verwenden. Allerdings wird die Sitzung dadurch neu gestartet, und der bisherige Fortschritt geht verloren.

**Beispiele für Pakete von Maven Repository oder Spark Packages**

Nachdem Sie das Paket von Maven Repository geladen haben, sammeln Sie die Werte für **GroupId**, **ArtifactId** und **Version**. Verketten Sie die drei Werte, getrennt durch einen Doppelpunkt ( **:** ).

   ![Schema für die Verkettung von Paketen](./media/apache-spark-manage-dependencies/spark-package-schema.png "Schema für die Verkettung von Paketen")

Vergewissern Sie sich, dass die erfassten Werte Ihrem Cluster entsprechen. In diesem Fall wird das Spark Cosmos DB-Connectorpaket für Scala 2.11 und Spark 2.3 für das Spark-Cluster in HDInsight 3.6 verwendet. Wenn Sie nicht sicher sind, führen Sie `scala.util.Properties.versionString` in einer Codezelle im Spark-Kernel aus, um die Scala-Clusterversion abzurufen. Führen Sie `sc.version` aus, um die Spark-Clusterversion abzurufen.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Beispiel für im primären Speicher gespeicherte JAR-Dateien**

Verwenden Sie das [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) für JAR-Dateien in Ihren primären Clusterspeicher. Dies ist `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage oder Data Lake Storage Gen2 aktiviert ist, lautet der URI `wasbs://` bzw. `abfss://`. Informationen hierzu finden Sie unter [Sichere Übertragung](../../storage/common/storage-require-secure-transfer.md).

Verwenden Sie bei mehreren JAR-Dateien eine durch Trennzeichen getrennte Liste mit JAR-Pfaden. Globs sind zulässig. Die JAR-Dateien sind in den Treiber- und Executor-Klassenpfaden enthalten.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Nach dem Konfigurieren von externen Paketen können Sie in der Codezelle „import“ ausführen, um zu prüfen, ob die Pakete ordnungsgemäß eingefügt wurden.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Verwenden von Azure-Toolkit für IntelliJ
Das Plug-In [Azure-Toolkit für IntelliJ](./apache-spark-intellij-tool-plugin.md) stellt eine Benutzeroberfläche zum Übermitteln von Spark Scala-Anwendungen an einen HDInsight-Cluster bereit. Es enthält die Eigenschaften `Referenced Jars` und `Referenced Files`, mit denen Sie beim Übermitteln einer Spark-Anwendung JAR-Bibliothekspfade konfigurieren können. Weitere Informationen finden Sie unter [Verwenden von Azure-Toolkit für IntelliJ für HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

![Dialogfeld für die Spark-Übermittlung](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>JAR-Bibliotheken für Cluster
Gelegentlich sollten Sie die JAR-Abhängigkeiten auf Clusterebene konfigurieren, sodass jede Anwendung standardmäßig mit denselben Abhängigkeiten eingerichtet wird. In diesem Fall fügen Sie die JAR-Pfade einem Treiber- oder Executor-Klassenpfad von Spark hinzu.

1. Führen Sie die folgenden Beispielskriptaktionen aus, um JAR-Dateien vom primären Speicher `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` in das lokale Dateisystem `/usr/libs/sparklibs` des Clusters zu kopieren. Dieser Schritt ist erforderlich, da Linux zum Trennen der Klassenpfadliste `:` verwendet, HDInsight jedoch nur Speicherpfade mit einem Schema wie `wasb://` unterstützt. Der Remotespeicherpfad funktioniert nicht ordnungsgemäß, wenn Sie ihn dem Klassenpfad direkt hinzufügen.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Ändern Sie die Spark-Dienstkonfiguration von Ambari, um den Klassenpfad zu aktualisieren. Wechseln Sie zu **Ambari > Spark > Configs > Custom Spark2-defaults**. **Fügen Sie Eigenschaften wie folgt hinzu**. Wenn Sie mehrere Pfade hinzufügen möchten, verwenden Sie zum Trennen der Pfade `:`. Globs sind zulässig.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Ändern der Standardkonfiguration von Spark](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Ändern der Standardkonfiguration von Spark")

3. Speichern Sie die geänderten Konfigurationen, und starten Sie die betreffenden Dienste neu.

   ![Neustart der betreffenden Dienste](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Neustart der betreffenden Dienste")

Mithilfe von [Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md) können Sie diese Schritte automatisieren. Die Skriptaktion zum [Hinzufügen von benutzerdefinierten Hive-Bibliotheken](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) ist eine gute Quelle. Verwenden Sie beim Ändern der Konfiguration von Spark-Diensten Ambari-APIs, statt die Konfigurationsdateien direkt zu ändern. 

## <a name="safely-manage-jar-dependencies"></a>Sicheres Verwalten von JAR-Abhängigkeiten
Der HDInsight-Cluster enthält integrierte JAR-Abhängigkeiten. Für diese JAR-Versionen werden von Zeit zu Zeit Updates ausgeführt. Um Versionskonflikte zwischen integrierten JAR-Dateien und den JAR-Dateien zu vermeiden, die Sie als Referenz nutzen, sollten Sie [für die Abhängigkeiten Ihrer Anwendung Shading verwenden](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Python-Pakete für einen Spark-Auftrag
### <a name="use-jupyter-notebook"></a>Verwenden von Jupyter Notebook
Der PySpark-Kernel von HDInsight Jupyter Notebook unterstützt die direkte Installation von Python-Paketen über das PyPi- oder Anaconda-Paketrepository nicht. Gehen Sie wie folgt vor, wenn Sie für eine Spark-Sitzung auf `.zip`-, `.egg`- oder `.py`-Abhängigkeiten verweisen möchten:

1. Führen Sie die folgenden Beispielskriptaktionen aus, um `.zip`-, `.egg`- oder `.py`-Dateien vom primären Speicher `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` in das lokale Dateisystem `/usr/libs/pylibs` des Clusters zu kopieren. Dieser Schritt ist erforderlich, da Linux zum Trennen der Suchpfadliste `:` verwendet, HDInsight jedoch nur Speicherpfade mit einem Schema wie `wasb://` unterstützt. Der Remotespeicherpfad funktioniert nicht ordnungsgemäß, wenn Sie `sys.path.insert` verwenden.

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. Führen Sie in Ihrem Notebook den folgenden Code in einer Codezelle mit PySpark-Kernel aus:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Führen Sie `import` aus, um zu prüfen, ob Ihre Pakete erfolgreich hinzugefügt wurden.  

## <a name="python-packages-for-cluster"></a>Python-Pakete für Cluster
Python-Pakete können über Anaconda mithilfe von Conda-Befehlen über Skriptaktionen im Cluster installiert werden. Die Pakete werden auf Clusterebene installiert und gelten für alle Anwendungen. 

HDInsight Spark-Cluster verfügt mit Anaconda Python 2.7 und Anaconda Python 3.5 über zwei integrierte Python-Installationen. Weitere Informationen zu den Python-Standardeinstellungen für Dienste und zur sicheren Installation externer Python-Pakete ohne Beschädigung des Clusters finden Sie unter [Sicheres Verwalten von Python-Paketen für Ihr Cluster](./apache-spark-python-package-installation.md).
