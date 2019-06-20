---
title: Problembehandlung mit Apache Spark-Clustern in Azure HDInsight
description: Erfahren Sie mehr zu Problemen mit Apache Spark-Clustern in Azure HDInsight und wie Sie diese umgehen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: hrasheed
ms.openlocfilehash: 51a0ee6f2d928d79e60ca9976d7651c70867a41f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717592"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Bekannte Probleme bei Apache Spark-Clustern unter HDInsight

In diesem Dokument werden sämtliche bekannte Probleme für die öffentliche Vorschauversion von HDInsight Spark erfasst.  

## <a name="apache-livy-leaks-interactive-session"></a>Verlust einer interaktiven Sitzung durch Apache Livy
Wenn [Apache Livy](https://livy.incubator.apache.org/) neu gestartet wird (von [Apache Ambari](https://ambari.apache.org/) oder aufgrund des Neustarts eines virtuellen Computers mit Hauptknoten 0), solange eine interaktive Sitzung aktiv ist, geht eine interaktive Auftragssitzung verloren. Daher können neue Aufträge im Status „Akzeptiert“ hängen bleiben.

**Lösung:**

Gehen Sie wie folgt vor, um das Problem zu umgehen:

1. Greifen Sie per SSH auf den Stammknoten zu. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Führen Sie den folgenden Befehl aus, um die Anwendungs-IDs der interaktiven Aufträge zu ermitteln, die über Livy gestartet wurden. 
   
        yarn application –list
   
    Die Standardauftragsnamen entsprechen Livy, wenn die Aufträge mit einer interaktiven Livy-Sitzung ohne ausdrücklich angegebene Namen gestartet wurden. Bei den von einem [Jupyter Notebook](https://jupyter.org/) gestarteten Livy-Sitzungen beginnt der Name des Auftrags mit „remotesparkmagics_*“. 
3. Führen Sie den folgenden Befehl aus, um die Beendigung dieser Aufträge zu erzwingen. 
   
        yarn application –kill <Application ID>

Neue Aufträge werden gestartet. 

## <a name="spark-history-server-not-started"></a>Spark-Verlaufsserver startet nicht
Der Spark-Verlaufsserver wird nach der Clustererstellung nicht automatisch gestartet.  

**Lösung:** 

Starten Sie den Verlaufsserver in Ambari manuell.

## <a name="permission-issue-in-spark-log-directory"></a>Berechtigungsproblem im Spark-Protokollverzeichnis
„hdiuser“ erhält beim Senden eines Auftrags mit „spark-submit“ den folgenden Fehler:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
Und es wird kein Treiberprotokoll geschrieben. 

**Lösung:**

1. Fügen Sie „hdiuser“ der Hadoop-Gruppe hinzu. 
2. Erteilen Sie nach der Clustererstellung 777-Berechtigungen für „/var/log/spark“. 
3. Aktualisieren Sie den Spark-Protokollspeicherort mit Ambari auf ein Verzeichnis mit 777-Berechtigungen.  
4. Führen Sie „spark-submit“ als sudo aus.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Keine Unterstützung für den Spark-Phoenix-Connector

HDInsight Spark-Cluster unterstützen den Spark-Phoenix-Connector nicht.

**Lösung:**

Sie müssen stattdessen den Spark-HBase-Connector verwenden. Anweisungen finden Sie unter [How to use Spark-HBase connector](https://web.archive.org/web/20190112153146/ https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/) (Verwenden des Spark-HBase-Connectors).

## <a name="issues-related-to-jupyter-notebooks"></a>Probleme im Zusammenhang mit Jupyter Notebooks
Im Folgenden sind einige Probleme im Zusammenhang mit Jupyter Notebooks genannt.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Notebooks mit Nicht-ASCII-Zeichen in Dateinamen
Verwenden Sie in Jupyter Notebook-Dateinamen keine ASCII-fremden Zeichen. Wenn Sie versuchen, eine Datei über die Jupyter-Benutzeroberfläche hochzuladen, deren Dateiname ASCII-fremde Zeichen enthält, schlägt der Upload ohne Fehlermeldung fehl. Jupyter erlaubt den Upload der Datei nicht, gibt aber auch keinen sichtbaren Fehler aus.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Fehler beim Laden von größeren Notebooks
Möglicherweise wird beim Laden von größeren Notebooks der Fehler **`Error loading notebook`** angezeigt.  

**Lösung:**

Wenn Sie diesen Fehler erhalten, bedeutet dies nicht, dass Ihre Daten beschädigt oder verloren sind.  Ihre Notebooks befinden sich weiterhin auf der Festplatte in `/var/lib/jupyter`, und Sie können über SSH eine Verbindung mit dem Cluster herstellen, um darauf zuzugreifen. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).

Sobald Sie mithilfe von SSH eine Verbindung mit dem Cluster hergestellt haben, können Sie Ihre Notebooks zur Sicherung aus Ihrem Cluster auf den lokalen Computer kopieren (mit SCP oder WinSCP), um den Verlust wichtiger Daten im Notebook zu vermeiden. Anschließend können Sie über einen SSH-Tunnel an Port 8001 eine Verbindung mit Ihrem Hauptknoten herstellen, um ohne Umweg über das Gateway auf Jupyter zuzugreifen.  Dort können Sie die Ausgabe Ihres Notebooks löschen und es erneut speichern, um die Größe des Notebooks zu minimieren.

Um zu verhindern, dass dieser Fehler in Zukunft auftritt, müssen Sie einige bewährten Methoden befolgen:

* Es ist wichtig, die Größe von Notebooks niedrig zu halten. Alle Ausgaben Ihrer Spark-Aufträge, die an Jupyter zurückgesendet werden, werden beständig im Notebook gespeichert.  Für Jupyter wird allgemein empfohlen, das Ausführen von `.collect()` auf großen RDDs (Resilient Distributed Datasets) oder Datenrahmen zu vermeiden. Wenn Sie einen Blick auf den Inhalt eines RDD werfen möchten, führen Sie stattdessen `.take()` oder `.sample()` aus, damit die Ausgabe nicht zu groß wird.
* Löschen Sie außerdem beim Speichern eines Notebooks alle Ausgabezellen, um die Größe zu verringern.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Erster Notebook-Start dauert länger als erwartet
Die Verarbeitung der ersten Codeanweisung in Jupyter Notebook mit Spark Magic kann über eine Minute dauern.  

**Erklärung:**

Dies geschieht, wenn die erste Codezelle ausgeführt wird. Im Hintergrund werden dadurch die Sitzungskonfiguration initiiert und Spark-, SQL- sowie Hive-Kontexte festgelegt. Nachdem diese Kontexte festgelegt wurden, wird die erste Anweisung ausgeführt, die den Eindruck entstehen lässt, dass sie lange Zeit in Anspruch nimmt.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook-Timeout bei der Sitzungserstellung
Wenn dem Spark-Cluster nicht genügend Ressourcen zur Verfügung stehen, tritt bei der Sitzungserstellung für die Spark- und PySpark-Kernel im Jupyter Notebook ein Timeout auf. 

**Lösung:** 

1. Führen Sie folgende Schritte aus, um Ressourcen in Ihrem Spark-Cluster freizugeben:
   
   * Beenden Sie andere Spark-Notebooks über das entsprechende Menü, oder klicken Sie im Notebook-Explorer auf „Herunterfahren“.
   * Beenden Sie andere Spark-Anwendungen über YARN.
2. Starten Sie das Notebook, das Sie starten wollten, neu. Nun sollten genügend Ressourcen für die Sitzungserstellung verfügbar sein.

## <a name="see-also"></a>Weitere Informationen
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
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)

