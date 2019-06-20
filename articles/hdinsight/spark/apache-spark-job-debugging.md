---
title: Debuggen von Apache Spark-Aufträgen, die in HDInsight ausgeführt werden
description: Verwenden Sie die YARN-Benutzeroberfläche, die Spark-Benutzeroberfläche und den Spark-Verlaufsserver, um auf einem Spark-Cluster in Azure HDInsight ausgeführte Aufträge nachzuverfolgen und zu debuggen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/05/2018
ms.author: hrasheed
ms.openlocfilehash: 5e384520c1b8d6cf5e3b182bbddf41a5f4f7f8f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707830"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Debuggen von Apache Spark-Aufträgen, die in HDInsight ausgeführt werden

In diesem Artikel erfahren Sie, wie Sie [Apache Spark](https://spark.apache.org/)-Aufträge, die in HDInsight-Clustern ausgeführt werden, mit der [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-Benutzeroberfläche, der Spark-Benutzeroberfläche und dem Spark-Verlaufsserver nachverfolgen und debuggen. Sie starten einen Spark-Auftrag über das im Spark-Cluster verfügbare Notebook **Machine Learning: Predictive analysis on food inspection data using MLLib** (Maschinelles Lernen: Vorhersageanalyse von Lebensmittelkontrolldaten mithilfe von MLLib). Sie können mithilfe der folgenden Schritte auch eine Anwendung nachverfolgen, die Sie mit einer anderen Methode wie z.B. **spark-submit** übermittelt haben.

## <a name="prerequisites"></a>Voraussetzungen
Sie benötigen Folgendes:

* Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Ein Apache Spark-Cluster unter HDInsight. Eine Anleitung finden Sie unter [Erstellen von Apache Spark-Clustern in Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Sie sollten mit der Ausführung des Notebooks **[Machine Learning: Predictive analysis on food inspection data using MLLib](apache-spark-machine-learning-mllib-ipython.md)** (Maschinelles Lernen: Vorhersageanalyse von Lebensmittelkontrolldaten mithilfe von MLLib) begonnen haben. Für eine Anleitung zum Ausführen dieses Notebooks folgen Sie dem Link.  

## <a name="track-an-application-in-the-yarn-ui"></a>Nachverfolgen einer Anwendung auf der YARN-Benutzeroberfläche
1. Starten Sie die YARN-Benutzeroberfläche. Klicken Sie unter **Cluster-Dashboards** auf **Yarn**.
   
    ![YARN-Benutzeroberfläche starten](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]  
   > Alternativ können Sie die YARN-Benutzeroberfläche auch über die Ambari-Benutzeroberfläche starten. Klicken Sie zum Starten der Ambari-Benutzeroberfläche unter **Cluster-Dashboards** auf **Ambari Home**. Klicken Sie auf der Ambari-Benutzeroberfläche auf **YARN**, auf **QuickLinks**, auf den aktiven Ressourcen-Manager und dann auf **Resource Manager UI** (Ressourcen-Manager-UI). 

2. Da Sie den Spark-Auftrag mit Jupyter-Notebooks gestartet haben, hat die Anwendung den Namen **remotesparkmagics** (dies ist der Name für alle Anwendungen, die über die Notebooks gestartet werden). Klicken Sie auf die Anwendungs-ID für den Anwendungsnamen, um weitere Informationen zum Auftrag abzurufen. Dadurch wird die Anwendungsansicht geöffnet.
   
    ![Suchen der Spark-Anwendungs-ID](./media/apache-spark-job-debugging/find-application-id.png)
   
    Für Anwendungen, die über die Jupyter-Notebooks gestartet werden, ist der Status immer **WIRD AUSGEFÜHRT** , bis Sie das Notebook beenden.
3. In der Anwendungsansicht können Sie weitere Details anzeigen, um die der Anwendung zugeordneten Container und die Protokolle (stdout/stderr) zu finden. Sie können die Spark-Benutzeroberfläche auch starten, indem Sie auf die Verknüpfung für die **Nachverfolgungs-URL**klicken, wie unten dargestellt. 
   
    ![Herunterladen von Containerprotokollen](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Nachverfolgen einer Anwendung auf der Spark-Benutzeroberfläche
Auf der Spark-Benutzeroberfläche können Sie Details der Spark-Aufträge anzeigen, die von der Anwendung erzeugt werden, die Sie zuvor gestartet haben.

1. Um die Spark-Benutzeroberfläche zu starten, klicken Sie in der Anwendungsansicht auf den Link für die **Nachverfolgungs-URL**, wie im Screenshot oben dargestellt. Es werden alle Spark-Aufträge angezeigt, die von der Anwendung, die im Jupyter-Notebook ausgeführt wird, gestartet werden.
   
    ![Anzeigen von Spark-Aufträgen](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Klicken Sie auf die Registerkarte **Executors** , um Informationen zur Verarbeitung und Speicherung für jeden Executor anzuzeigen. Sie können auch die Aufrufliste abrufen, indem Sie auf den Link **Thread Dump** klicken.
   
    ![Anzeigen von Spark-Executors](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Klicken Sie auf die Registerkarte **Stages** , um die der Anwendung zugeordneten Phasen anzuzeigen.
   
    ![Anzeigen von Spark-Phasen](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Jede Phase kann mehrere Aufgaben aufweisen, für die Sie Ausführungsstatistiken anzeigen können, wie unten dargestellt.
   
    ![Anzeigen von Spark-Phasen](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Auf der Seiten mit Phasendetails können Sie die DAG-Visualisierung starten. Erweitern Sie den Link **DAG Visualization** oben auf der Seite, wie unten dargestellt.
   
    ![Anzeigen der DAG-Visualisierung von Spark-Phasen](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    Mit DAG (Direct Aclyic Graph, gerichteter azyklischer Graph) werden die verschiedenen Phasen in der Anwendung dargestellt. Jedes blaue Feld im Diagramm stellt einen Spark-Vorgang dar, der von der Anwendung aufgerufen wurde.
5. Auf der Seite mit den Phasendetails können Sie auch die Zeitachsenansicht für die Anwendung starten. Erweitern Sie den Link **Event Timeline** oben auf der Seite, wie unten dargestellt.
   
    ![Anzeigen der Ereigniszeitachse von Spark-Phasen](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Dadurch werden die Spark-Ereignisse in Form einer Zeitachse angezeigt. Die Zeitachsenansicht ist auf drei Ebenen verfügbar: für Aufträge, innerhalb eines Auftrags und innerhalb einer Phase. In der Abbildung oben ist die Zeitachsenansicht für eine bestimmte Phase dargestellt.
   
   > [!TIP]  
   > Wenn Sie das Kontrollkästchen **Enable zooming** aktivieren, können Sie in der Zeitachsenansicht einen Bildlauf nach links und rechts ausführen.

6. Andere Registerkarten der Spark-Benutzeroberfläche enthalten ebenfalls nützliche Informationen über die Spark-Instanz.
   
   * Registerkarte „Storage“: Wenn Ihre Anwendung RDDs erstellt, finden Sie auf der Registerkarte „Storage“ Informationen dazu.
   * Registerkarte „Environment“: Diese Registerkarte enthält viele nützliche Informationen zu Ihrer Spark-Instanz, z.B. die folgenden 
     * Scala-Version
     * Ereignisprotokollverzeichnis, das dem Cluster zugeordnet ist
     * Anzahl der Executorkerne für die Anwendung
     * und weitere

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Anzeigen von Informationen zu abgeschlossenen Aufträgen mithilfe des Spark-Verlaufsservers
Wenn ein Auftrag abgeschlossen ist, werden die Informationen zum Auftrag auf dem Spark-Verlaufsserver beibehalten.

1. Klicken Sie zum Starten des Spark-Verlaufsservers auf dem Clusterblatt auf **Cluster-Dashboard** und dann auf **Spark-Verlaufsserver**.
   
    ![Starten des Spark-Verlaufsservers](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]  
   > Alternativ können Sie die Benutzeroberfläche des Spark-Verlaufsservers auch über die Ambari-Benutzeroberfläche starten. Klicken Sie zum Starten der Ambari-Benutzeroberfläche über das Blatt „Übersicht“ unter **Cluster-Dashboards** auf **Ambari Home**. Klicken Sie auf der Ambari-Benutzeroberfläche auf **Spark**, klicken Sie auf **Quick Links**, und klicken Sie dann auf **Spark History Server UI** (UI des Spark-Verlaufsservers).

2. Eine Liste der abgeschlossenen Anwendungen wird angezeigt. Klicken Sie auf eine Anwendungs-ID, um weitere Informationen zur Anwendung anzuzeigen.
   
    ![Starten des Spark-Verlaufsservers](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Weitere Informationen
*  [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
*  [Debuggen von Apache Spark-Aufträgen mit dem erweiterten Spark-Verlaufsserver](apache-azure-spark-history-server.md)

### <a name="for-data-analysts"></a>Für Datenanalysten

* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analysieren von Application Insights-Telemetriedaten mit Apache Spark in HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Verwenden von Caffe in Azure HDInsight Spark für verteiltes Deep Learning](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Für Spark-Entwickler

* [Erstellen einer eigenständigen Anwendung mit Scala](apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Erstellen und Übermitteln von Spark Scala-Anwendungen](apache-spark-intellij-tool-plugin.md)
* [Verwenden des HDInsight-Tools-Plug-Ins für IntelliJ IDEA zum Remotedebuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernel für Jupyter Notebook in Apache Spark-Clustern für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)
