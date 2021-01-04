---
title: Problembehandlung für Apache Spark in Azure HDInsight
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Arbeiten mit Apache Spark und Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: 9e60903aaa61cae63d406d459937d33317eee394
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022250"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Problembehandlung bei Apache Spark mit Azure HDInsight

Lernen Sie die wichtigsten Probleme und ihre Lösungen bei der Arbeit mit Apache Spark-Nutzlasten in [Apache Ambari](https://ambari.apache.org/) kennen.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über Apache Ambari in Clustern?

Spark-Konfigurationswerte können optimiert werden, um eine `OutofMemoryError`-Ausnahme der Apache Spark-Anwendung zu vermeiden. Die folgenden Schritte zeigen die Standardwerte der Spark-Konfiguration in Azure HDInsight:

1. Melden Sie sich unter `https://CLUSTERNAME.azurehdidnsight.net` mit Ihren Clusteranmeldeinformationen bei Ambari an. Auf dem ersten Bildschirm wird ein Dashboard mit einer Übersicht angezeigt. Es gibt geringfügige kosmetische Unterschiede zwischen HDInsight 3.6 und 4.0.

1. Navigieren Sie zu **Spark2** > **Configs**.

    ![Registerkarte „Configs“ auswählen](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. Wählen Sie in der Liste der Konfigurationen den Eintrag **Custom-spark2-defaults** aus, und erweitern Sie diesen.

1. Suchen Sie nach der Werteinstellung, die Sie anpassen müssen, z.B. **spark.executor.memory**. In diesem Fall ist der Wert **9728m** zu hoch.

    ![„custom-spark-defaults“ auswählen](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Legen Sie den Wert auf die empfohlene Einstellung fest. Für diese Einstellung wird der Wert **2048m** empfohlen.

1. Speichern Sie den Wert und anschließend die Konfiguration. Wählen Sie **Speichern** aus.

    ![Wert in „2048m“ ändern](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Schreiben Sie eine Notiz zu den Konfigurationsänderungen, und wählen Sie **Save** (Speichern) aus.

    ![Notiz zu den von Ihnen vorgenommenen Änderungen eingeben](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Sie werden benachrichtigt, wenn eine Konfiguration Ihre Aufmerksamkeit erfordert. Notieren Sie sich die Elemente, und wählen Sie anschließend **Proceed Anyway** (Dennoch fortfahren) aus.

    ![„Proceed Anyway“ (Dennoch fortfahren) auswählen](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Wenn eine Konfiguration gespeichert ist, werden Sie aufgefordert, den Dienst neu starten. Wählen Sie **Neu starten** aus.

    ![„Neu starten“ auswählen](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Bestätigen Sie den Neustart.

    ![„Confirm Restart All“ (Bestätigen: Alle neu starten) auswählen](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Sie können die ausgeführten Prozesse überprüfen.

    ![Ausgeführte Prozesse überprüfen](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Sie können Konfigurationen hinzufügen. Wählen Sie in der Liste der Konfigurationen **Custom-spark2-defaults** aus, und wählen Sie dann **Add Property** (Eigenschaft hinzufügen) aus.

    ![„Add Property“ (Eigenschaft hinzufügen) auswählen](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Definieren Sie eine neue Eigenschaft. Sie können eine einzelne Eigenschaft mithilfe eines Dialogfelds für bestimmte Einstellungen definieren (z.B. den Datentyp). Sie können auch mehrere Eigenschaften definieren, indem Sie eine Eigenschaft pro Zeile angeben.

    In diesem Beispiel wird die Eigenschaft **spark.driver.memory** mit dem Wert **4g** definiert.

    ![Neue Eigenschaft definieren](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Speichern Sie die Konfiguration, und starten Sie den Dienst wie in Schritt 6 und 7 beschrieben neu.

Diese Änderungen betreffen den gesamten Cluster, sie können jedoch beim Senden des Spark-Auftrags überschrieben werden.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über ein Jupyter Notebook in Clustern?

Geben Sie in der ersten Zelle im Jupyter-Notebook nach der Anweisung **%%configure** die Spark-Konfigurationen in einem gültigen JSON-Format ein. Ändern Sie die tatsächlichen Werte entsprechend den jeweiligen Anforderungen:

![Konfiguration hinzufügen](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über Apache Livy in Clustern?

Senden Sie die Spark-Anwendung mit einem REST-Client wie cURL an Livy. Führen Sie einen Befehl aus, der dem folgenden ähnelt: Ändern Sie die tatsächlichen Werte entsprechend den jeweiligen Anforderungen:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Wie konfiguriere ich eine Apache Spark-Anwendung über spark-submit in Clustern?

Starten Sie spark-shell mit einem Befehl, der dem folgenden ähnelt. Ändern Sie den tatsächlichen Wert der Konfigurationen entsprechend den jeweiligen Anforderungen:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>Zusätzliche Lektüre

[Übermitteln von Apache Spark-Aufträgen in HDInsight-Clustern](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihr Problem nicht aufgeführt ist oder Sie es nicht lösen können, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* [Spark memory management overview](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview) (Übersicht über die Spark-Speicherverwaltung).

* [Debugging Spark application on HDInsight clusters](/archive/blogs/azuredatalake/spark-debugging-101) (Debuggen der Spark-Anwendung in HDInsight-Clustern).

* Nutzen Sie den [Azure-Communitysupport](https://azure.microsoft.com/support/community/), um Antworten von Azure-Experten zu erhalten.

* Herstellen einer Verbindung mit [@AzureSupport](https://twitter.com/azuresupport), dem offiziellen Microsoft Azure-Konto zum Verbessern der Kundenfreundlichkeit. Verbinden der Azure-Community mit den richtigen Ressourcen: Antworten, Support und Experten.

* Sollten Sie weitere Unterstützung benötigen, senden Sie eine Supportanfrage über das [Azure-Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wählen Sie dazu auf der Menüleiste die Option **Support** aus, oder öffnen Sie den Hub **Hilfe und Support**. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md). Zugang zu Abonnementverwaltung und Abrechnungssupport ist in Ihrem Microsoft Azure-Abonnement enthalten. Technischer Support wird über einen [Azure-Supportplan](https://azure.microsoft.com/support/plans/) bereitgestellt.