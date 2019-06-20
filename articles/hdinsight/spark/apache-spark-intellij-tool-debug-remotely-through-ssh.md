---
title: 'Azure-Toolkit für IntelliJ: Remotedebuggen von Spark-Anwendungen per SSH '
description: Hier finden Sie eine ausführliche Anleitung zur Verwendung der HDInsight Tools im Azure-Toolkit für IntelliJ zum Remotedebuggen von Anwendungen in HDInsight-Clustern per SSH.
keywords: Remotedebuggen von IntelliJ, Remotedebugging IntelliJ, SSH IntelliJ, HDInsight, Debuggen von Intellij, Debuggen
ms.service: hdinsight
author: hrasheed
ms.author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/25/2017
ms.openlocfilehash: 7554f0739626520153196472a790ab672a932331
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066216"
---
# <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Lokales oder Remotedebuggen von Apache Spark-Anwendungen in einem HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ per SSH

Dieser Artikel enthält eine ausführliche Anleitung zur Verwendung der HDInsight-Tools im [Azure-Toolkit für IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) zum Remotedebuggen von Anwendungen in einem HDInsight-Cluster. Informationen zum Debuggen Ihres Projekts finden Sie auch im Video [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Debuggen von HDInsight Spark-Anwendungen mit dem Azure-Toolkit für IntelliJ).

**Voraussetzungen**
* **HDInsight-Tools im Azure-Toolkit für IntelliJ.** Dieses Tool ist Teil des Azure-Toolkits für IntelliJ. Weitere Informationen finden Sie unter [Installieren des Azure-Toolkits für IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation). Und **Azure-Toolkit für IntelliJ**. Verwenden Sie dieses Toolkit zum Erstellen von Apache Spark-Anwendungen für einen HDInsight-Cluster. Weitere Informationen finden Sie in den Anweisungen unter [Erstellen von Apache Spark-Anwendungen für einen HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin).

* **HDInsight SSH-Dienst mit Benutzernamen- und Kennwortverwaltung**. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) und [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, JobHistory, NameNode, Apache Oozie und andere Webbenutzeroberflächen](https://docs.microsoft.com/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel). 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>Informationen zum lokalen Ausführen und Debuggen
### <a name="scenario-1-create-a-spark-scala-application"></a>Szenario 1: Erstellen einer Spark Scala-Anwendung 

1. Starten Sie IntelliJ IDEA, und erstellen Sie ein Projekt. Führen Sie im Dialogfeld **Neues Projekt** folgende Schritte aus:

   a. Wählen Sie **Azure Spark/HDInsight** aus. 

   b. Wählen Sie entsprechend Ihren Anforderungen eine Java- oder Scala-Vorlage aus. Folgende Optionen stehen zur Auswahl:

   - **Spark-Projekt (Java)**

   - **Spark-Projekt (Scala)**

   - **Spark-Projekt mit Beispielen (Scala)**

   - **Spark-Projekt mit Beispielen zum Debuggen von Fehleraufgaben (Vorschau) (Scala)**

     In diesem Beispiel wird eine Vorlage vom Typ **Spark-Projekt mit Beispielen (Scala)** verwendet.

   c. Wählen Sie in der Liste **Build-Tool** je nach Ihren Anforderungen einen der folgenden Einträge aus:

   - **Maven** für die Unterstützung des Scala-Projekterstellungs-Assistenten

   - **SBT** zum Verwalten von Abhängigkeiten und Erstellen für das Scala-Projekt 

     ![Erstellen eines Debugprojekts](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d. Klicken Sie auf **Weiter**.     
 
1. Führen Sie im nächsten Fenster **New Project** (Neues Projekt) folgende Schritte aus:

   ![Auswählen des Spark-SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. Geben Sie einen Projektnamen und -speicherort an.

   b. Wählen Sie in der Dropdownliste **Project SDK** (Projekt-SDK) die Option **Java 1.8** für **Spark 2.x**-Cluster oder die Option **Java 1.7** für **Spark 1.x**-Cluster aus.

   c. In der Dropdownliste **Spark Version** (Spark-Version) fügt der Scala-Projekterstellungs-Assistent die richtige Version für das Spark-SDK und das Scala-SDK ein. Wenn die Spark-Clusterversion niedriger als 2.0 ist, wählen Sie **Spark 1.x** aus. Wählen Sie andernfalls **Spark 2.x** aus. In diesem Beispiel wird **Spark 2.0.2 (Scala 2.11.8)** verwendet.

   d. Wählen Sie **Fertig stellen** aus.

1. Wählen Sie **src** > **main** > **scala** aus, um Ihren Code im Projekt zu öffnen. In diesem Beispiel wird das Skript **SparkCore_wasbloTest** verwendet.

### <a name="prerequisite-for-windows"></a>Erforderliche Komponente für Windows
Beim Ausführen der lokalen Spark Scala-Anwendung auf einem Windows-Computer wird unter Umständen eine Ausnahme wie unter [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) beschrieben ausgelöst. Diese Ausnahme tritt auf, weil die Datei „WinUtils.exe“ in Windows fehlt. 

Um diesen Fehler zu beheben, müssen Sie [die ausführbare Datei herunterladen](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) und an einem Speicherort wie **C:\WinUtils\bin** speichern. Fügen Sie anschließend die Umgebungsvariable **HADOOP_HOME** hinzu, und legen Sie den Wert der Variable auf **C:\WinUtils** fest.

### <a name="scenario-2-perform-local-run"></a>Szenario 2: Durchführen einer lokalen Ausführung
1. Öffnen Sie das Skript **SparkCore_wasbloTest**, klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann die Option **Run „[Spark Job]XXX“** („[Spark-Auftrag]XXX“ ausführen), um eine lokale Ausführung zu starten.
1. Nach Abschluss der lokalen Ausführung sehen Sie, dass die Ausgabedatei in Ihrem aktuellen Projekt-Explorer im Verzeichnis **data** >  **__default__** gespeichert wurde.

    ![Ergebnis der lokalen Ausführung](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
1. Unsere Tools legen die Standardkonfiguration für die lokale Ausführung automatisch fest, wenn Sie die lokale Ausführung und lokales Debuggen durchführen. Öffnen Sie die Konfiguration **[Spark on HDInsight] XXX** ([Spark auf HDInsight] XXX). In der oberen rechten Ecke sehen Sie, dass **[Spark auf HDInsight] XXX** bereits unter **Apache Spark on HDInsight** (Apache Spark auf HDInsight) erstellt wurde. Wechseln Sie zur Registerkarte **Lokal ausführen**.

    ![Konfiguration der lokalen Ausführung](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [Environment variables](#prerequisite-for-windows) (Umgebungsvariablen): Wenn Sie die Systemumgebungsvariable **HADOOP_HOME** bereits auf **C:\WinUtils** festgelegt haben, kann automatisch festgestellt werden, dass eine manuelle Hinzufügung nicht erforderlich ist.
    - [WinUtils.exe Location](#prerequisite-for-windows) (Speicherort von „WinUtils.exe“): Wenn Sie die Systemumgebungsvariable nicht festgelegt haben, können Sie den Speicherort durch Klicken auf die zugehörige Schaltfläche finden.
    - Wählen Sie eine der beiden Optionen aus. Unter macOS und Linux sind diese nicht erforderlich.
1. Sie können die Konfiguration auch manuell vor der lokalen Ausführung und dem lokalen Debuggen festlegen. Wählen Sie im obigen Screenshot das Pluszeichen ( **+** ). Wählen Sie dann die Option **Apache Spark auf HDInsight** aus. Geben Sie Informationen für **Name** und **Main class name** (Name der Hauptklasse) für den Speichervorgang ein, und klicken Sie dann auf die Schaltfläche für die lokale Ausführung.

### <a name="scenario-3-perform-local-debugging"></a>Szenario 3: Durchführen eines lokalen Debuggens
1. Öffnen Sie das Skript **SparkCore_wasbloTest**, und legen Sie Breakpoints fest.
1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann die Option **Debug „[Spark on HDInsight]XXX“** („[Spark auf HDInsight]XXX“ debuggen), um das lokale Debuggen zu starten.   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>Informationen zum Remoteausführen und -debuggen
### <a name="scenario-1-perform-remote-run"></a>Szenario 1: Remoteausführung

1. Um das Menü **Edit Configurations** (Konfigurationen bearbeiten) zu öffnen, klicken Sie auf das Symbol in der rechten oberen Ecke. Über dieses Menü können Sie die Konfigurationen für das Remotedebuggen erstellen und bearbeiten.

   ![Konfigurationen bearbeiten](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

1. Klicken Sie im Dialogfeld **Run/Debug Configurations** (Konfigurationen ausführen/debuggen) auf das Plussymbol ( **+** ). Wählen Sie dann die Option **Apache Spark auf HDInsight** aus.

   ![Hinzufügen einer neuen Konfiguration](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
1. Wechseln Sie zur Registerkarte **Remotely Run in Cluster** (Im Cluster remote ausführen). Geben Sie Informationen für **Name**, **Spark cluster** und **Main class name** (Name der main-Klasse) ein. Klicken Sie dann auf **Erweiterte Konfiguration (Remotedebuggen)** . Unseren Tools unterstützen das Debuggen mit **Executors**. Die **numExecutors**, der Standardwert ist 5. Sie sollten ihn nicht auf einen größeren Wert als 3 festlegen.

   ![Debugkonfigurationen ausführen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Wählen Sie im Teil **Erweiterte Konfiguration (Remotedebuggen)** **Remotedebuggen für Spark aktivieren** aus. Geben Sie den SSH-Benutzernamen und anschließend ein Kennwort ein, oder verwenden Sie eine private Schlüsseldatei. Wenn Sie das Remotedebuggen durchführen möchten, müssen Sie diesen Wert festlegen. Dies ist nicht notwendig, wenn Sie nur die Remoteausführung verwenden möchten.

   ![Remotedebuggen für Spark aktivieren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Die Konfiguration wird jetzt unter dem von Ihnen angegebenen Namen gespeichert. Um die Konfigurationsdetails anzuzeigen, wählen Sie den Konfigurationsnamen aus. Um Änderungen vorzunehmen, wählen Sie **Edit Configurations** (Konfigurationen bearbeiten) aus. 

1. Nachdem Sie die Konfigurationseinstellungen abgeschlossen haben, können Sie das Projekt für den Remotecluster ausführen oder das Remotedebuggen durchführen.
   
   ![Schaltfläche „Remote ausführen“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

1. Klicken Sie auf die Schaltfläche **Trennen**, damit Die Übermittlungsprotokolle nicht im linken Bereich angezeigt werden. Die Ausführung wird jedoch auf dem Back-End fortgesetzt.

   ![Schaltfläche „Remote ausführen“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>Szenario 2: Remotedebuggen
1. Richten Sie Breakpoints ein, und klicken Sie dann auf das Symbol **Remotedebuggen**. Der Unterschied bei der Remoteübermittlung besteht darin, dass der SSH-Benutzername und das Kennwort konfiguriert werden müssen.

   ![Klicken auf das Symbol „Debug“ (Debuggen)](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Wenn die Programmausführung den Breakpoint erreicht, werden eine Registerkarte **Driver** (Treiber) und zwei Registerkarten **Executor** im Bereich **Debugger** angezeigt. Klicken Sie auf das Symbol **Resume Program** (Programm fortsetzen), um die Ausführung des Codes fortzusetzen. Die Ausführung erreicht dann den nächsten Breakpoint. Sie müssen zur richtigen **Executor**-Registerkarte wechseln, um den Zielexecutor für das Debuggen zu finden. Sie können die Ausführungsprotokolle auf der entsprechenden Registerkarte **Console** (Konsole) anzeigen.

   ![Registerkarte „Debugging“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>Szenario 3: Remotedebuggen und Beheben von Fehlern
1. Richten Sie zwei Breakpoints ein, und klicken Sie dann auf das Symbol **Debug** (Debuggen), um das Remotedebuggen zu starten.

1. Der Code wird am ersten Breakpoint angehalten, und die Informationen zu Parametern und Variablen werden im Bereich **Variables** (Variablen) angezeigt. 

1. Klicken Sie auf das Symbol **Resume Program** (Programm fortsetzen), um die Ausführung fortzusetzen. Der Code wird am zweiten Punkt beendet. Die Ausnahme wird wie erwartet abgefangen.

   ![Fehler auslösen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

1. Klicken Sie erneut auf das Symbol **Resume Program** (Programm fortsetzen). Im Fenster **HDInsight Spark Submission** (HDInsight Spark-Übermittlung) wird ein Fehler bei der Auftragsausführung angezeigt.

   ![Fehler beim Übermitteln](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

1. Um den Wert der Variablen mithilfe der Debugfunktion von IntelliJ dynamisch zu aktualisieren, wählen Sie erneut **Debug** (Debuggen) aus. Der Bereich **Variables** (Variablen) wird wieder angezeigt. 

1. Klicken Sie mit der Maustaste auf die Registerkarte **Debug**, und wählen Sie dann **Set Value** (Wert festlegen) aus. Als Nächstes geben Sie einen neuen Wert für die Variable ein. Wählen Sie dann **Enter** (Eingabe) aus, um den Wert zu speichern. 

   ![Wert festlegen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

1. Klicken Sie auf das Symbol **Resume Program** (Programm fortsetzen), um die Programmausführung fortzusetzen. Dieses Mal wird keine Ausnahme abgefangen. Sie können sehen, dass das Projekt erfolgreich und ohne Ausnahmen ausgeführt wird.

   ![Debuggen ohne Ausnahme](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>Nächste Schritte
* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo
* Erstellen von Scala-Projekten (Video): [Erstellen von Spark Scala-Anwendungen](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Remotedebuggen (Video): [Remotedebuggen von Apache Spark-Anwendungen in einem HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Szenarien
* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen
* [Erstellen einer eigenständigen Anwendung mit Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen
* [Erstellen von Apache Spark-Anwendungen für einen HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen über VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Apache Spark-Anwendungen](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter Notebooks in einem Apache Spark-Cluster für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Verwalten von Ressourcen
* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)
