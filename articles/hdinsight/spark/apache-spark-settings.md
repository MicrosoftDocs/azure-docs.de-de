---
title: Konfigurieren von Spark-Einstellungen – Azure HDInsight
description: Es wird beschrieben, wie Sie Spark für einen Azure HDInsight-Cluster konfigurieren.
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: d5dca3f4517f3d24b765652f420a38c258ad9a2e
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190919"
---
# <a name="configure-apache-spark-settings"></a>Konfigurieren von Apache Spark-Einstellungen

Ein HDInsight Spark-Cluster enthält eine Installation der [Apache Spark](https://spark.apache.org/)-Bibliothek.  Jeder HDInsight-Cluster enthält Standardkonfigurationsparameter für alle installierten Dienste, einschließlich Spark.  Ein wesentlicher Aspekt bei der Verwaltung eines HDInsight Apache Hadoop-Clusters ist die Überwachung der Workload, einschließlich der Spark-Aufträge, um sicherzustellen, dass sie in einer vorhersagbaren Weise ausgeführt werden. Um Spark-Aufträge optimal auszuführen, sollte bei der Festlegung, wie die logische Konfiguration des Clusters optimiert werden kann, die physische Konfiguration des Clusters berücksichtigt werden.

Der HDInsight Apache Spark-Standardcluster umfasst die folgenden Knoten: drei [Apache ZooKeeper](https://zookeeper.apache.org/)-Knoten, zwei Hauptknoten und mindestens einen Workerknoten.

![Spark HDInsight-Architektur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Auch die Anzahl der virtuellen Computer und die VM-Größen für die Knoten in Ihrem HDInsight-Cluster können sich auf die Spark-Konfiguration auswirken. Nicht standardmäßige HDInsight-Konfigurationswerte machen häufig nicht standardmäßige Spark-Konfigurationswerte erforderlich. Wenn Sie einen HDInsight Spark-Cluster erstellen, werden für die einzelnen Komponenten jeweils empfohlene VM-Größen angezeigt. Derzeit sind die [arbeitsspeicheroptimierten Größen virtueller Linux-Computer](../../virtual-machines/linux/sizes-memory.md) für Azure D12 v2 oder höher.

## <a name="apache-spark-versions"></a>Apache Spark-Versionen

Verwenden Sie die am besten geeignete Spark-Version für Ihren Cluster.  Der HDInsight-Dienst umfasst mehrere Versionen von Spark sowie auch von HDInsight.  Jede Version von Spark umfasst einen Satz von Standardclustereinstellungen.  

Beim Erstellen eines neuen Clusters können Sie zwischen verschiedenen Spark-Versionen wählen. Die vollständige Liste finden Sie unter [HDInsight-Komponenten und -Versionen](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning).


> [!NOTE]  
> Die Standardversion von Apache Spark im HDInsight-Dienst kann ohne vorherige Ankündigung geändert werden. Wenn eine Versionsabhängigkeit vorliegt, empfiehlt Microsoft, die spezifische Version anzugeben, wenn Sie Cluster mit .NET SDK, Azure PowerShell und der klassischen Azure-Befehlszeilenschnittstelle erstellen.

Apache Spark verfügt über drei Speicherorte für die Systemkonfiguration:

* Spark-Eigenschaften steuern die meisten Anwendungsparameter und können durch Verwendung eines `SparkConf`-Objekts oder über Java-Systemeigenschaften festgelegt werden.
* Mithilfe von Umgebungsvariablen können über das Skript `conf/spark-env.sh` auf jedem Knoten Einstellungen pro Computer (z.B. die IP-Adresse) festgelegt werden.
* Die Protokollierung kann über `log4j.properties` konfiguriert werden.

Bei Auswahl einer bestimmten Version von Spark umfasst Ihr Cluster die entsprechenden Standardkonfigurationseinstellungen.  Sie können die Spark-Standardkonfigurationswerte durch Angabe einer benutzerdefinierten Spark-Konfigurationsdatei ändern.  Ein entsprechendes Beispiel ist nachfolgend dargestellt.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

In diesem Beispiel werden mehrere Standardwerte für fünf Spark-Konfigurationsparameter überschrieben.  Dabei handelt es sich um den Komprimierungscodec, die Mindestgröße für die Teilung von Apache Hadoop MapReduce und Parquet-Blockgrößen sowie die Standardwerte für die Größe der Spark SQL-Partition und von geöffneten Dateien.  Diese Konfigurationsänderungen werden ausgewählt, da die zugeordneten Daten und Aufträge (in diesem Beispiel Genomdaten) bestimmte Merkmale aufweisen, die mit diesen benutzerdefinierten Konfigurationseinstellungen optimaler ausgeführt werden.

---

## <a name="view-cluster-configuration-settings"></a>Anzeigen von Clusterkonfigurationseinstellungen

Überprüfen Sie die aktuellen HDInsight-Clusterkonfigurationseinstellungen, bevor Sie eine Leistungsoptimierung für den Cluster ausführen. Starten Sie das HDInsight-Dashboard im Azure-Portal durch Klicken auf den Link **Dashboard** im Bereich für Spark-Cluster. Melden Sie sich mit dem Benutzernamen und dem Kennwort des Clusteradministrators an.

Die Apache Ambari-Webbenutzeroberfläche wird mit einer Dashboardansicht der wichtigsten Ressourcenverwendungsmetriken des Clusters angezeigt.  Im Ambari-Dashboard werden die Apache Spark-Konfiguration und andere installierte Dienste angezeigt. Das Dashboard enthält die Registerkarte **Config History** (Konfigurationsverlauf), auf der Konfigurationsinformationen für alle installierten Dienste, darunter Spark, angezeigt werden können.

Zum Anzeigen der Konfigurationswerte für Apache Spark wählen Sie **Config History** (Konfigurationsverlauf) und dann **Spark2** aus.  Wählen Sie die Registerkarte **Configs** (Konfigurationen) und dann in der Liste der Dienste den Link `Spark` (oder `Spark2`, abhängig von der verwendeten Version) aus.  Eine Liste der Konfigurationswerte für Ihren Cluster wird angezeigt:

![Spark-Konfigurationen](./media/apache-spark-settings/spark-config.png)

Zum Anzeigen und Ändern einzelner Spark-Konfigurationswerte wählen Sie einen Link mit dem Wort „spark“ im Titel aus.  Konfigurationen für Spark umfassen sowohl benutzerdefinierte als auch erweiterte Konfigurationswerte in den folgenden Kategorien:

* Custom Spark2-defaults
* Custom Spark2-metrics-properties
* Advanced Spark2-defaults
* Advanced Spark2-env
* Advanced spark2-hive-site-override

Wenn Sie eine nicht standardmäßige Gruppe von Konfigurationswerten erstellen, können Sie auch den Verlauf der Konfigurationsaktualisierungen anzeigen.  Dieser Konfigurationsverlauf kann sich als nützlich erweisen, wenn Sie überprüfen möchten, welche nicht standardmäßige Konfiguration eine optimale Leistung liefert.

> [!NOTE]  
> Wenn Sie allgemeine Spark-Clusterkonfigurationseinstellungen anzeigen, jedoch nicht ändern möchten, wählen Sie die Registerkarte **Environment** (Umgebung) auf der Benutzeroberfläche der **Spark-Aufträge** der obersten Ebene aus.

## <a name="configuring-spark-executors"></a>Konfigurieren von Spark-Executors

In der folgenden Abbildung sind wichtige Spark-Objekte dargestellt: das Treiberprogramm und der zugeordnete Spark-Kontext sowie der Cluster-Manager und die zugehörigen *n* Workerknoten.  Jeder Workerknoten enthält einen Executor, einen Cache und *n* Aufgabeninstanzen.

![Clusterobjekte](./media/apache-spark-settings/spark-arch.png)

Spark-Aufträge verwenden Workerressourcen, insbesondere Arbeitsspeicher. Daher werden Spark-Konfigurationswerte für Executors von Workerknoten häufig angepasst.

Dabei werden die folgenden drei Schlüsselparameter zur Optimierung von Spark-Konfigurationen zur Verbesserung von Anwendungsanforderungen häufig angepasst: `spark.executor.instances`, `spark.executor.cores` und `spark.executor.memory`. Ein Executor ist ein Prozess, der für eine Spark-Anwendung gestartet wird. Ein Executor wird auf dem Workerknoten ausgeführt und ist für die Ausführung der Aufgaben für die Anwendung zuständig. Die Standardanzahl von Executors und die Executorgrößen für jeden Cluster werden basierend auf der Anzahl der Workerknoten und der Größe der Workerknoten berechnet. Diese werden in `spark-defaults.conf` im Clusterhauptknoten gespeichert.  Sie können diese Werte in einem ausgeführten Cluster durch Auswählen des Links **Custom spark-defaults** in der Ambari-Webbenutzeroberfläche bearbeiten.  Nachdem Sie Änderungen vorgenommen haben, werden Sie aufgefordert, alle betroffenen Dienste neu zu starten (**Restart**).

> [!NOTE]  
> Diese drei Konfigurationsparameter können auf Clusterebene (für alle Anwendungen, die im Cluster ausgeführt werden) konfiguriert und auch für jede einzelne Anwendung angegeben werden.

Die Spark-Anwendungsbenutzeroberfläche stellt eine weitere Informationsquelle zu den von den Spark-Executors verwendeten Ressourcen dar.  Wählen Sie in der Spark-Benutzeroberfläche die Registerkarte **Executors** aus, um die Zusammenfassungs- und Detailansicht der Konfiguration und der von den Executors verwendeten Ressourcen anzuzeigen.  In diesen Ansichten können Sie feststellen, ob Standardwerte für Spark-Executors für den gesamten Cluster oder einen bestimmten Satz von Auftragsausführungen geändert werden müssen.

![Spark-Executors](./media/apache-spark-settings/spark-executors.png)

Alternativ können Sie über die Ambari-REST-API die HDInsight- und Spark-Clusterkonfigurationseinstellungen programmgesteuert ändern.  Weitere Informationen stehen in der [Apache Ambari-API-Referenz auf GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) zur Verfügung.

Je nach Größe Ihrer Spark-Workload stellen Sie möglicherweise fest, dass Sie mit einer nicht standardmäßigen Spark-Konfiguration eine Optimierung der Spark-Auftragsausführung erzielen können.  Sie sollten Benchmarktests mit Beispielworkloads durchführen, um verschiedene nicht standardmäßige Clusterkonfigurationen zu überprüfen.  Folgende allgemeine Parameter können berücksichtigt werden:

* `--num-executors` legt die Anzahl von Executors fest.
* `--executor-cores` legt die Anzahl von Kernen für jeden Executor fest. Es empfiehlt sich die Verwendung von Executors mittlerer Größe, da andere Prozesse auch einen Teil des verfügbaren Arbeitsspeicherplatzes nutzen.
* `--executor-memory` steuert die Speichergröße (Heapgröße) der einzelnen Executors in [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Sie müssen eine bestimmte Menge an Arbeitsspeicher für den Ausführungsoverhead reservieren.

Beispiel für zwei Workerknoten mit unterschiedlichen Konfigurationswerten:

![Konfigurationen mit zwei Knoten](./media/apache-spark-settings/executor-config.png)

In der folgenden Liste sind wichtige Arbeitsspeicherparameter für Spark-Executors aufgeführt.

* `spark.executor.memory` definiert die für einen Executor verfügbare Gesamtspeichermenge.
* `spark.storage.memoryFraction` (standardmäßig ca. 60 %) definiert die Menge des verfügbaren Arbeitsspeichers zum Speichern von persistenten RDDs.
* `spark.shuffle.memoryFraction` (standardmäßig ca. 20 %) definiert die Menge des reservierten Shuffle-Arbeitsspeichers.
* `spark.storage.unrollFraction` und `spark.storage.safetyFraction` (insgesamt ca. 30 % des gesamten Arbeitsspeichers): Diese Werte werden in Spark intern verwendet und sollten nicht geändert werden.

YARN steuert den maximal von den Containern auf jedem Spark-Knoten verwendeten Gesamtarbeitsspeicher. In der folgenden Abbildung sind die Beziehungen pro Knoten zwischen YARN-Konfigurationsobjekten und Spark-Objekten dargestellt.

![Übersicht über die YARN-Spark-Arbeitsspeicherverwaltung](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändern der Parameter für eine Anwendung, die im Jupyter Notebook ausgeführt wird

Spark-Cluster in HDInsight enthalten standardmäßig eine Reihe von Komponenten. Jede dieser Komponenten umfasst Standardkonfigurationswerte, die nach Bedarf überschrieben werden können.

* Spark Core: Spark Core, Spark SQL, Spark Streaming-APIs, GraphX und Apache Spark MLlib.
* Anaconda: ein Python-Paket-Manager.
* [Apache Livy](https://livy.incubator.apache.org/): die Apache Spark-REST-API, die zum Übermitteln von Remoteaufträgen an einen HDInsight Spark-Cluster verwendet wird.
* [Jupyter](https://jupyter.org/) und [Apache Zeppelin](https://zeppelin.apache.org/) Notebooks: interaktive browserbasierte Benutzeroberfläche zur Interaktion mit dem Spark-Cluster.
* ODBC-Treiber: verbindet Spark-Cluster in HDInsight mit BI-Tools (Business Intelligence), z.B. Microsoft Power BI und Tableau.

Für Anwendungen, die im Jupyter Notebook ausgeführt werden, können Sie mit dem Befehl `%%configure` Konfigurationsänderungen im eigentlichen Notebook vornehmen. Die Konfigurationsänderungen werden auf die Spark-Aufträge angewendet, die über Ihre Notebook-Instanz ausgeführt werden. Sie sollten diese Änderungen am Anfang der Anwendung vornehmen, bevor Sie die erste Codezelle ausführen. Die geänderte Konfiguration wird auf die Livy-Sitzung angewendet, wenn sie erstellt wird.

> [!NOTE]  
> Zum Ändern der Konfiguration zu einem späteren Zeitpunkt in der Anwendung verwenden Sie den Parameter `-f` (force). Dadurch geht jedoch der gesamte Fortschritt in der Anwendung verloren.

Der folgende Code zeigt, wie die Konfiguration für eine in einem Jupyter Notebook ausgeführte Anwendung geändert wird.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Zusammenfassung

Sie müssen verschiedene wesentliche Konfigurationseinstellungen überwachen und anpassen, um sicherzustellen, dass die Spark-Aufträge vorhersagbar und effizient ausgeführt werden. Mit diesen Einstellungen kann die optimale Spark-Clusterkonfiguration für Ihre spezifischen Workloads festgelegt werden.  Zudem müssen Sie die Ausführung von Spark-Aufträgen mit langer Ausführungszeit und von ressourcenintensiven Spark-Aufträgen überwachen.  Die häufigsten Herausforderungen betreffen unzureichenden Arbeitsspeicher aufgrund von nicht ordnungsgemäßen Konfigurationen (insbesondere bei falsch dimensionierten Executors), Vorgänge mit langer Ausführungsdauer und Tasks, die zu kartesischen Operationen führen.

## <a name="next-steps"></a>Nächste Schritte

* [Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md)
* [Verwalten von Ressourcen für den Apache Spark-Cluster unter HDInsight](apache-spark-resource-manager.md)
* [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark Configuration](https://spark.apache.org/docs/latest/configuration.html) (Apache Spark-Konfiguration)
* [Ausführen von Apache Spark in Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html) (in englischer Sprache)
