---
title: Problembehandlung für Spark in Azure HDInsight
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Arbeiten mit Apache Spark und Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: a4dc7293c00097c7a5752e29bf7c9a203cbb31a5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721153"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Problembehandlung bei Apache Spark mit Azure HDInsight

Lernen Sie die wichtigsten Probleme und ihre Lösungen bei der Arbeit mit [Apache Spark](https://spark.apache.org/)-Nutzlasten in [Apache Ambari](https://ambari.apache.org/) kennen.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über Apache Ambari in Clustern?

### <a name="resolution-steps"></a>Lösungsschritte

Spark-Konfigurationswerte können optimiert werden, um eine OutofMemoryError-Ausnahme der Apache Spark-Anwendung zu vermeiden. Die folgenden Schritte zeigen die Standardwerte der Spark-Konfiguration in Azure HDInsight: 

1. Wählen Sie in der Liste der Cluster **Spark2** aus.

    ![Cluster aus der Liste wählen](./media/apache-troubleshoot-spark/update-config-1.png)

2. Wählen Sie die Registerkarte **Configs** aus.

    ![Registerkarte „Configs“ auswählen](./media/apache-troubleshoot-spark/update-config-2.png)

3. Wählen Sie in der Liste der Konfigurationen **Custom-spark2-defaults** aus.

    ![„custom-spark-defaults“ auswählen](./media/apache-troubleshoot-spark/update-config-3.png)

4. Suchen Sie nach der Werteinstellung, die Sie anpassen müssen, z.B. **spark.executor.memory**. In diesem Fall ist der Wert **4608m** hoch.

    ![Feld „spark.executor.memory“ wählen](./media/apache-troubleshoot-spark/update-config-4.png)

5. Legen Sie den Wert auf die empfohlene Einstellung fest. Für diese Einstellung wird der Wert **2048m** empfohlen.

    ![Wert in „2048m“ ändern](./media/apache-troubleshoot-spark/update-config-5.png)

6. Speichern Sie den Wert und anschließend die Konfiguration. Wählen Sie auf der Symbolleiste **Save** (Speichern) aus.

    ![Einstellung und Konfiguration speichern](./media/apache-troubleshoot-spark/update-config-6a.png)

    Sie werden benachrichtigt, wenn eine Konfiguration Ihre Aufmerksamkeit erfordert. Notieren Sie sich die Elemente, und wählen Sie anschließend **Proceed Anyway** (Dennoch fortfahren) aus. 

    ![„Proceed Anyway“ (Dennoch fortfahren) auswählen](./media/apache-troubleshoot-spark/update-config-6b.png)

    Schreiben Sie eine Notiz zu den Konfigurationsänderungen, und wählen Sie **Save** (Speichern) aus.

    ![Notiz zu den von Ihnen vorgenommenen Änderungen eingeben](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Wenn eine Konfiguration gespeichert ist, werden Sie aufgefordert, den Dienst neu starten. Wählen Sie **Neu starten** aus.

    ![„Neu starten“ auswählen](./media/apache-troubleshoot-spark/update-config-7a.png)

    Bestätigen Sie den Neustart.

    ![„Confirm Restart All“ (Bestätigen: Alle neu starten) auswählen](./media/apache-troubleshoot-spark/update-config-7b.png)

    Sie können die ausgeführten Prozesse überprüfen.

    ![Ausgeführte Prozesse überprüfen](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Sie können Konfigurationen hinzufügen. Wählen Sie in der Liste der Konfigurationen **Custom-spark2-defaults** aus, und wählen Sie dann **Add Property** (Eigenschaft hinzufügen) aus.

    ![„Add Property“ (Eigenschaft hinzufügen) auswählen](./media/apache-troubleshoot-spark/update-config-8.png)

9. Definieren Sie eine neue Eigenschaft. Sie können eine einzelne Eigenschaft mithilfe eines Dialogfelds für bestimmte Einstellungen definieren (z.B. den Datentyp). Sie können auch mehrere Eigenschaften definieren, indem Sie eine Eigenschaft pro Zeile angeben. 

    In diesem Beispiel wird die Eigenschaft **spark.driver.memory** mit dem Wert **4g** definiert.

    ![Neue Eigenschaft definieren](./media/apache-troubleshoot-spark/update-config-9.png)

10. Speichern Sie die Konfiguration, und starten Sie den Dienst wie in Schritt 6 und 7 beschrieben neu.

Diese Änderungen betreffen den gesamten Cluster, sie können jedoch beim Senden des Spark-Auftrags überschrieben werden.

### <a name="additional-reading"></a>Zusätzliche Lektüre

[Übermitteln von Apache Spark-Aufträgen in HDInsight-Clustern](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über ein Jupyter Notebook in Clustern?

### <a name="resolution-steps"></a>Lösungsschritte

1. Wenn Sie ermitteln möchten, welche Spark-Konfigurationen auf welche Werte festgelegt werden müssen, lesen Sie den Artikel „Wodurch wird eine OutOfMemoryError-Ausnahme in einer Apache Spark-Anwendung verursacht?“

2. Geben Sie in der ersten Zelle im Jupyter-Notebook nach der Anweisung **%%configure** die Spark-Konfigurationen in einem gültigen JSON-Format ein. Ändern Sie die tatsächlichen Werte entsprechend den jeweiligen Anforderungen:

    ![Konfiguration hinzufügen](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Zusätzliche Lektüre

[Übermitteln von Apache Spark-Aufträgen in HDInsight-Clustern](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über Apache Livy in Clustern?

### <a name="resolution-steps"></a>Lösungsschritte

1. Wenn Sie ermitteln möchten, welche Spark-Konfigurationen auf welche Werte festgelegt werden müssen, lesen Sie den Artikel „Wodurch wird eine OutOfMemoryError-Ausnahme in einer Apache Spark-Anwendung verursacht?“ 

2. Senden Sie die Spark-Anwendung mit einem REST-Client wie cURL an Livy. Führen Sie einen Befehl aus, der dem folgenden ähnelt: Ändern Sie die tatsächlichen Werte entsprechend den jeweiligen Anforderungen:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Zusätzliche Lektüre

[Übermitteln von Apache Spark-Aufträgen in HDInsight-Clustern](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über spark-submit in Clustern?

### <a name="resolution-steps"></a>Lösungsschritte

1. Wenn Sie ermitteln möchten, welche Spark-Konfigurationen auf welche Werte festgelegt werden müssen, lesen Sie den Artikel „Wodurch wird eine OutOfMemoryError-Ausnahme in einer Apache Spark-Anwendung verursacht?“

2. Starten Sie spark-shell mit einem Befehl, der dem folgenden ähnelt. Ändern Sie den tatsächlichen Wert der Konfigurationen entsprechend den jeweiligen Anforderungen: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Zusätzliche Lektüre

[Übermitteln von Apache Spark-Aufträgen in HDInsight-Clustern](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>Wodurch wird eine OutOfMemoryError-Ausnahme in einer Apache Spark-Anwendung verursacht?

### <a name="detailed-description"></a>Detaillierte Beschreibung

Bei der Spark-Anwendung sind die folgenden Typen nicht abgefangener Ausnahmefehler aufgetreten:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Wahrscheinliche Ursache

Die wahrscheinlichste Ursache dieser Ausnahme besteht darin, dass den Java Virtual Machines (JVMs) kein ausreichender Heapspeicher zugeordnet ist. Diese JVMs werden als Executors oder Treiber im Rahmen der Spark-Anwendung gestartet. 

### <a name="resolution-steps"></a>Lösungsschritte

1. Ermitteln Sie die maximale Größe der Daten, die von der Spark-Anwendung verarbeitet werden. Basierend auf der maximalen Größe der Eingabedaten, der bei der Transformation der Eingabedaten erzeugten Zwischendateien und der Ausgabedaten, die bei der weiteren Transformation der Zwischendateien durch die Anwendung erzeugt werden, kann eine Schätzung vorgenommen werden. Diese kann iterativ erfolgen, wenn Sie keine formale Ausgangsschätzung vornehmen können. 

2. Stellen Sie sicher, dass der verwendete HDInsight-Cluster über ausreichende Ressourcen im Hinblick auf Arbeitsspeicher und Kerne verfügt, um die Spark-Anwendung auszuführen. Dies können Sie feststellen, indem Sie den Abschnitt mit den Clustermetriken der YARN-Benutzeroberfläche für die Werte **Verwendeter Arbeitsspeicher**, **Arbeitsspeicher gesamt** sowie **VCores Used** (Verwendete VCores) und **VCores Total** (VCores insgesamt) betrachten.

3. Legen Sie die folgenden Spark-Konfigurationen auf die entsprechenden Werte fest, die 90 % der verfügbaren Ressourcen an Arbeitsspeicher und Kernen nicht überschreiten sollten. Die Werte sollten die Arbeitsspeicheranforderungen der Spark-Anwendung erfüllen: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    So berechnen Sie den gesamten von allen Executors verwendeten Arbeitsspeicher: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   So berechnen Sie den gesamten vom Treiber verwendeten Arbeitsspeicher:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Übersicht über die Apache Spark-Speicherverwaltung](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Debuggen einer Apache Spark-Anwendung in einem HDInsight-Cluster](https://web.archive.org/web/20190112152909/ https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Siehe auch
[Beheben von Problemen mit Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
