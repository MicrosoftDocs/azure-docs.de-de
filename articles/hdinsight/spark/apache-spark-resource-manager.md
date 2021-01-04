---
title: Verwalten von Ressourcen für den Apache Spark-Cluster unter Azure HDInsight
description: Hier erfahren Sie, wie Sie Ressourcen für Spark-Cluster unter Azure HDInsight verwalten, um die Leistung zu optimieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 5427077a4b07917c8852d0a63c815195e776b9de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017031"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>Verwalten von Ressourcen für den Apache Spark-Cluster unter Azure HDInsight

Erfahren Sie, wie Sie auf die mit Ihrem [Apache Spark](https://spark.apache.org/)-Cluster verknüpften Benutzeroberflächen wie die [Apache Ambari](https://ambari.apache.org/)-Benutzeroberfläche, die [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-Benutzeroberfläche und den [Spark-Verlaufsserver](./apache-azure-spark-history-server.md) zugreifen, und wie Sie den Cluster für optimale Leistung konfigurieren.

## <a name="open-the-spark-history-server"></a>Öffnen des Spark-Verlaufsservers

Der Spark-Verlaufsserver ist die Webbenutzeroberfläche für abgeschlossene und ausgeführte Spark-Anwendungen. Er stellt eine Erweiterung der Webbenutzeroberfläche von Spark dar. Ausführliche Informationen finden Sie unter [Spark-Verlaufsserver](./apache-azure-spark-history-server.md).

## <a name="open-the-yarn-ui"></a>Öffnen der YARN-Benutzeroberfläche

Auf der YARN-Benutzeroberfläche können Anwendungen überwachen, die derzeit auf dem Spark-Cluster ausgeführt werden.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) den Spark-Cluster. Weitere Informationen finden Sie unter [Auflisten und Anzeigen von Clustern](../hdinsight-administer-use-portal-linux.md#showClusters).

2. Wählen Sie unter **Cluster-Dashboards** die Option **YARN** aus. Geben Sie bei Aufforderung die Anmeldeinformationen für den Spark-Cluster ein.

    ![YARN-Benutzeroberfläche starten](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > Alternativ können Sie die YARN-Benutzeroberfläche auch über die Ambari-Benutzeroberfläche starten. Navigieren Sie auf der Ambari-Benutzeroberfläche zu **YARN** > **Quicklinks** > **Aktiv** > **Resource Manager UI** (Benutzeroberfläche des Ressourcen-Managers).

## <a name="optimize-clusters-for-spark-applications"></a>Optimieren von Clustern für Spark-Anwendungen

Die drei wichtigsten Parameter, die je nach Anwendungsanforderungen für die Spark-Konfiguration verwendet werden können, sind `spark.executor.instances`, `spark.executor.cores` und `spark.executor.memory`. Ein Executor ist ein Prozess, der für eine Spark-Anwendung gestartet wird. Er wird auf dem Workerknoten ausgeführt und ist für die Ausführung der Aufgaben für die Anwendung zuständig. Die Standardanzahl von Executors und die Executorgrößen für jeden Cluster werden basierend auf der Anzahl von Workerknoten und der Größe der Workerknoten berechnet. Diese Informationen werden in `spark-defaults.conf` in den Clusterhauptknoten gespeichert.

Die drei Konfigurationsparameter können auf Clusterebene (für alle Anwendungen, die im Cluster ausgeführt werden) konfiguriert werden oder für jede einzelne Anwendung angegeben werden.

### <a name="change-the-parameters-using-ambari-ui"></a>Ändern der Parameter mit der Ambari-Benutzeroberfläche

1. Navigieren Sie auf der Ambari-Benutzeroberfläche zu **Spark2** > **Configs** (Konfigurationen) > **Custom spark2-defaults**.

    ![Festlegen von Parametern mit Ambari: Benutzerdefiniert](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "Festlegen von Parametern mit Ambari: Benutzerdefiniert")

1. Die Standardwerte sind gut geeignet, um vier Spark-Anwendungen gleichzeitig im Cluster auszuführen. Sie können diese Werte über die Benutzeroberfläche ändern, wie im folgenden Screenshot dargestellt wird:

    ![Festlegen von Parametern mit Ambari](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "Festlegen von Parametern mit Ambari")

1. Wählen Sie **Speichern** aus, um die Änderungen an der Konfiguration zu speichern. Oben auf der Seite werden Sie aufgefordert, alle betroffenen Dienste neu zu starten. Wählen Sie **Neu starten** aus.

    ![Neustarten von Diensten](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändern der Parameter für eine Anwendung, die im Jupyter-Notebook ausgeführt wird

Für Anwendungen, die im Jupyter-Notebook ausgeführt werden, können Sie die `%%configure` -Magic für Konfigurationsänderungen verwenden. Im Idealfall nehmen Sie diese Änderungen am Anfang der Anwendung vor, bevor Sie die erste Codezelle ausführen. Dadurch wird sichergestellt, dass die Konfiguration auf die Livy-Sitzung angewendet wird, wenn sie erstellt wird. Wenn Sie die Konfiguration zu einem späteren Zeitpunkt in der Anwendung ändern möchten, müssen Sie den Parameter `-f` verwenden. Allerdings geht dadurch der gesamte Status in der Anwendung verloren.

Der folgende Ausschnitt zeigt, wie die Konfiguration für eine in Jupyter ausgeführte Anwendung geändert wird.

```scala
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

Konfigurationsparameter müssen als JSON-Zeichenfolge übergeben werden und in der nächsten Zeile nach dem Magic-Befehl stehen, wie in der Beispielspalte gezeigt.

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>Ändern der Parameter für eine Anwendung, die mit „spark-submit“ übermittelt wird

Der folgende Befehl ist ein Beispiel dafür, wie die Konfigurationsparameter für eine Batchanwendung geändert werden, die mithilfe von `spark-submit`übermittelt wird.

```scala
spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>
```

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>Ändern der Parameter für eine Anwendung, die mit „cURL“ übermittelt wird

Der folgende Befehl ist ein Beispiel dafür, wie die Konfigurationsparameter für eine Batchanwendung geändert werden, die mithilfe von „cURL“ übermittelt wird.

```bash
curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches
```

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>Ändern dieser Parameter auf einem Spark Thrift-Server

Der Spark Thrift-Server bietet JDBC/ODBC-Zugriff auf einen Spark-Cluster und wird verwendet, um Spark-SQL-Abfragen zu verarbeiten. Tools wie Power BI, Tableau usw. verwenden das ODBC-Protokoll zur Kommunikation mit dem Spark Thrift-Server, um Spark-SQL-Abfragen als Spark-Anwendung auszuführen. Wenn ein Spark-Cluster erstellt wird, werden zwei Instanzen des Spark Thrift-Servers gestartet, eine auf jedem Hauptknoten. Jeder Spark Thrift-Server wird als Spark-Anwendung auf der YARN-Benutzeroberfläche angezeigt.

Der Spark Thrift-Server nutzt die dynamische Executorzuordnung in Spark, und somit wird `spark.executor.instances` nicht verwendet. Der Spark Thrift-Server verwendet stattdessen `spark.dynamicAllocation.maxExecutors` und `spark.dynamicAllocation.minExecutors`, um die Executoranzahl anzugeben. Die Konfigurationsparameter `spark.executor.cores` und `spark.executor.memory` werden verwendet, um die Executorgröße zu ändern. Sie können diese Parameter ändern, wie in den folgenden Schritten dargestellt:

* Erweitern Sie die Kategorie **Advanced spark2-thrift-sparkconf**, um die Parameter `spark.dynamicAllocation.maxExecutors` und `spark.dynamicAllocation.minExecutors` zu aktualisieren.

    ![Konfigurieren des Spark Thrift-Servers](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "Konfigurieren des Spark Thrift-Servers")

* Erweitern Sie die Kategorie **Custom spark2-thrift-sparkconf**, um die Parameter `spark.executor.cores` und `spark.executor.memory` zu aktualisieren.

    ![Konfigurieren des Spark Thrift-Servers: Parameter](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "Konfigurieren des Spark Thrift-Servers: Parameter")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>Ändern des Treiberspeichers des Spark Thrift-Servers

Der Treiberspeicher des Spark Thrift-Servers ist so konfiguriert, dass er 25 % der RAM-Größe des Hauptknotens umfasst, vorausgesetzt, dass die RAM-Gesamtgröße des Hauptknotens über 14 GB liegt. Sie können mit der Ambari-Benutzeroberfläche die Treiberspeicherkonfiguration ändern, wie im folgenden Screenshot dargestellt wird:

Navigieren Sie auf der Ambari-Benutzeroberfläche zu **Spark2** > **Configs** (Konfigurationen) > **Advanced spark2-env**. Geben Sie dann den Wert für **spark_thrift_cmd_opts** an.

## <a name="reclaim-spark-cluster-resources"></a>Freigeben von Spark-Clusterressourcen

Da die dynamische Zuteilung von Spark verwendet wird, werden nur die Ressourcen für die beiden Anwendungsmaster vom Thrift-Server genutzt. Um diese Ressourcen freizugeben, müssen Sie die im Cluster ausgeführten Dienste des Thrift-Servers beenden.

1. Wählen Sie auf der Ambari-Benutzeroberfläche im linken Bereich **Spark2** aus.

2. Klicken Sie auf der nächsten Seite auf **Spark2 Thrift-Server**.

    ![Neustarten des Thrift-Servers: 1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "Neustarten des Thrift-Servers: 1")

3. Sie sollten zwei Hauptknoten sehen, auf denen der Spark2 Thrift-Server ausgeführt wird. Wählen Sie einen der Hauptknoten aus.

    ![Neustarten des Thrift-Servers: 2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "Neustarten des Thrift-Servers: 2")

4. Auf der nächsten Seite werden alle auf diesem Hauptknoten ausgeführten Dienste aufgeführt. Wählen Sie in der Liste neben „Spark2 Thrift-Server“ die Dropdownschaltfläche und dann **Stop** aus.

    ![Neustarten des Thrift-Servers: 3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "Neustarten des Thrift-Servers: 3")
5. Wiederholen Sie diese Schritte auch auf dem anderen Hauptknoten.

## <a name="restart-the-jupyter-service"></a>Neustarten des Jupyter-Diensts

Starten Sie die Ambari-Webbenutzeroberfläche, wie am Anfang des Artikels beschrieben wird. Wählen Sie im linken Navigationsbereich **Jupyter**, **Dienstaktionen** und dann **Alle neu starten** aus. Dadurch wird der Jupyter-Dienst auf allen Hauptknoten gestartet.

![Neustarten von Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "Neustarten von Jupyter")

## <a name="monitor-resources"></a>Überwachen von Ressourcen

Starten Sie die YARN-Benutzeroberfläche, wie am Anfang des Artikels beschrieben wird. Überprüfen Sie im oberen Bildschirmbereich in der Tabelle mit den Clustermetriken die Werte in den Spalten **Memory Used** (Verwendeter Arbeitsspeicher) und **Memory Total** (Arbeitsspeicher gesamt). Wenn die beiden Werte dicht beieinander liegen, sind unter Umständen nicht genügend Ressourcen für den Start der nächsten Anwendung vorhanden. Gleiches gilt für die Spalten **VCores Used** (Verwendete virtuelle Kerne) und **VCores Total** (Virtuelle Kerne gesamt). Falls in der Hauptansicht eine Anwendung mit dem Zustand **AKZEPTIERT** nicht in den Zustand **RUNNING** (AUSGEFÜHRT) oder **FEHLER** übergeht, kann dies ebenfalls ein Hinweis darauf sein, dass nicht genügend Ressourcen zum Starten der Anwendung zur Verfügung stehen.

![Ressourcenlimit](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "Ressourcenlimit")

## <a name="kill-running-applications"></a>Beenden der Anwendungsausführung

1. Wählen Sie im linken Bereich der Benutzeroberfläche von YARN **Running** (Ausgeführt) aus. Suchen Sie in der Liste mit den ausgeführten Anwendungen nach der Anwendung, die Sie beenden möchten, und wählen Sie die **ID** aus.

    ![Beenden der App: 1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "Beenden der App: 1")

2. Wählen Sie rechts oben **Kill Application** (Anwendung beenden) und anschließend **OK** aus.

    ![Beenden der App: 2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "Beenden der App: 2")

## <a name="see-also"></a>Weitere Informationen

* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>Für Datenanalysten

* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analysieren von Application Insights-Telemetriedaten mit Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>Für Apache Spark-Entwickler

* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)
