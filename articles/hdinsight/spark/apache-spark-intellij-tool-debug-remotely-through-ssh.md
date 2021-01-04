---
title: 'Azure-Toolkit für IntelliJ: Debuggen von Spark-Apps mit SSH – HDInsight'
description: Hier finden Sie eine ausführliche Anleitung zur Verwendung der HDInsight Tools im Azure-Toolkit für IntelliJ zum Remotedebuggen von Anwendungen in HDInsight-Clustern per SSH.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: 421993ac4aaba551b6fcbd002783d44559ce377d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995334"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>Debuggen von Apache Spark-Anwendungen in einem HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ per SSH

Dieser Artikel enthält eine ausführliche Anleitung zur Verwendung der HDInsight-Tools im [Azure-Toolkit für IntelliJ](/azure/developer/java/toolkit-for-intellij) zum Remotedebuggen von Anwendungen in einem HDInsight-Cluster. Informationen zum Debuggen Ihres Projekts finden Sie auch im Video [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ) (Debuggen von HDInsight Spark-Anwendungen mit dem Azure-Toolkit für IntelliJ).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Spark-Cluster unter HDInsight. Weitere Informationen finden Sie unter [Erstellen eines Apache Spark-Clusters](../spark/apache-spark-jupyter-spark-sql-use-portal.md).

* Für Windows-Benutzer: Beim Ausführen der lokalen Spark Scala-Anwendung auf einem Windows-Computer wird unter Umständen eine Ausnahme wie unter [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) beschrieben ausgelöst. Diese Ausnahme tritt auf, weil die Datei „WinUtils.exe“ in Windows fehlt.

    Um diesen Fehler zu beheben, müssen Sie [Winutils.exe](https://github.com/steveloughran/winutils) herunterladen und an einem Speicherort wie **C:\WinUtils\bin** ablegen. Fügen Sie anschließend die Umgebungsvariable **HADOOP_HOME** hinzu, und legen Sie den Wert der Variable auf **C:\WinUtils** fest.

* [IntelliJ IDEA](https://www.jetbrains.com/idea/download/#section=windows) (Die Community Edition ist kostenlos.).

* [Azure-Toolkit für IntelliJ.](/azure/developer/java/toolkit-for-intellij/installation)

* [Scala-Plug-In für IntelliJ](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea).

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-spark-scala-application"></a>Erstellen einer Spark Scala-Anwendung

1. Starten Sie IntelliJ IDEA, und wählen Sie **Create New Project** (Neues Projekt erstellen) aus, um das Fenster **New Project** (Neues Projekt) zu öffnen.

1. Wählen Sie im linken Bereich **Apache Spark/HDInsight** aus.

1. Wählen Sie im Hauptfenster **Spark Project with Samples (Scala)** (Spark-Projekt mit Beispielen (Scala)) aus.

1. Wählen Sie in der Dropdownliste **Build tool** (Buildtool) eine der folgenden Optionen aus:

    * **Maven** für die Unterstützung des Scala-Projekterstellungs-Assistenten
    * **SBT** zum Verwalten von Abhängigkeiten und Erstellen für das Scala-Projekt

     ![Intellij – Neues Projekt erstellen – Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. Wählen Sie **Weiter** aus.

1. Geben Sie im nächsten Fenster **New Project** (Neues Projekt) die folgenden Informationen an:

    |Eigenschaft |BESCHREIBUNG |
    |---|---|
    |Projektname|Geben Sie einen Namen ein. In dieser exemplarischen Vorgehensweise wird `myApp` verwendet.|
    |Projektspeicherort|Geben Sie den gewünschten Speicherort für Ihr Projekt ein.|
    |Project SDK (Projekt-SDK)|Wenn leer, wählen Sie **New...** (Neu...) aus, und navigieren Sie zu Ihrem JDK.|
    |Spark-Version|Der Erstellungs-Assistent integriert die passende Version für das Spark-SDK und das Scala-SDK. Wenn Sie eine ältere Spark-Clusterversion als 2.0 verwenden, wählen Sie **Spark 1.x** aus. Wählen Sie andernfalls **Spark 2.x** aus. In diesem Beispiel wird **Spark 2.3.0 (Scala 2.11.8)** verwendet.|

   ![Intellij – Neues Projekt – Spark-Version auswählen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. Wählen Sie **Fertig stellen** aus. Es kann einige Minuten dauern, bis das Projekt verfügbar ist. Verfolgen Sie den Fortschritt in der unteren rechten Ecke.

1. Erweitern Sie das Projekt, und navigieren Sie zu **src** > **main** > **scala** > **sample** (Beispiel). Doppelklicken Sie auf **SparkCore_WasbIOTest**.

## <a name="perform-local-run"></a>Durchführen einer lokalen Ausführung

1. Klicken Sie im Skript **SparkCore_WasblOTest** mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann die Option **Run 'SparkCore_WasbIOTest'** („SparkCore_WasbIOTest“ ausführen) aus, um eine lokale Ausführung zu starten.

1. Nach Abschluss der lokalen Ausführung sehen Sie, dass die Ausgabedatei in Ihrem aktuellen Projekt-Explorer im Verzeichnis **data** >  **__default__** gespeichert wurde.

    ![Intellij – Ergebnis der lokalen Ausführung des Projekts](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. Unsere Tools legen die Standardkonfiguration für die lokale Ausführung automatisch fest, wenn Sie die lokale Ausführung und lokales Debuggen durchführen. Öffnen Sie die Konfiguration **[Spark on HDInsight] XXX** ([Spark auf HDInsight] XXX). In der oberen rechten Ecke sehen Sie, dass **[Spark auf HDInsight] XXX** bereits unter **Apache Spark on HDInsight** (Apache Spark auf HDInsight) erstellt wurde. Wechseln Sie zur Registerkarte **Lokal ausführen**.

    ![Intellij – Konfigurationen debuggen/ausführen – lokale Ausführung](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [Environment variables](#prerequisites) (Umgebungsvariablen): Wenn Sie die Systemumgebungsvariable **HADOOP_HOME** bereits auf **C:\WinUtils** festgelegt haben, kann automatisch festgestellt werden, dass eine manuelle Hinzufügung nicht erforderlich ist.
    - [WinUtils.exe Location](#prerequisites) (Speicherort von „WinUtils.exe“): Wenn Sie die Systemumgebungsvariable nicht festgelegt haben, können Sie den Speicherort durch Klicken auf die zugehörige Schaltfläche finden.
    - Wählen Sie eine der beiden Optionen aus. Unter macOS und Linux sind diese nicht erforderlich.

1. Sie können die Konfiguration auch manuell vor der lokalen Ausführung und dem lokalen Debuggen festlegen. Wählen Sie im obigen Screenshot das Pluszeichen ( **+** ). Wählen Sie dann die Option **Apache Spark auf HDInsight** aus. Geben Sie Informationen für **Name** und **Main class name** (Name der Hauptklasse) für den Speichervorgang ein, und klicken Sie dann auf die Schaltfläche für die lokale Ausführung.

## <a name="perform-local-debugging"></a>Durchführen eines lokalen Debuggens

1. Öffnen Sie das Skript **SparkCore_wasbloTest**, und legen Sie Breakpoints fest.

1. Klicken Sie mit der rechten Maustaste auf den Skript-Editor, und wählen Sie dann die Option **Debug „[Spark on HDInsight]XXX“** („[Spark auf HDInsight]XXX“ debuggen), um das lokale Debuggen zu starten.

## <a name="perform-remote-run"></a>Remoteausführung

1. Navigieren Sie zu **Ausführen** > **Konfigurationen bearbeiten**. Über dieses Menü können Sie die Konfigurationen für das Remotedebuggen erstellen und bearbeiten.

1. Klicken Sie im Dialogfeld **Run/Debug Configurations** (Konfigurationen ausführen/debuggen) auf das Plussymbol ( **+** ). Wählen Sie dann die Option **Apache Spark auf HDInsight** aus.

   ![Intellij – Neue Konfiguration hinzufügen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. Wechseln Sie zur Registerkarte **Remotely Run in Cluster** (Im Cluster remote ausführen). Geben Sie Informationen für **Name**, **Spark cluster** und **Main class name** (Name der main-Klasse) ein. Klicken Sie dann auf **Erweiterte Konfiguration (Remotedebuggen)** . Unseren Tools unterstützen das Debuggen mit **Executors**. Die **numExecutors**, der Standardwert ist 5. Sie sollten ihn nicht auf einen größeren Wert als 3 festlegen.

   ![Intellij – Konfigurationen debuggen/ausführen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. Wählen Sie im Teil **Erweiterte Konfiguration (Remotedebuggen)** **Remotedebuggen für Spark aktivieren** aus. Geben Sie den SSH-Benutzernamen und anschließend ein Kennwort ein, oder verwenden Sie eine private Schlüsseldatei. Wenn Sie das Remotedebuggen durchführen möchten, müssen Sie diesen Wert festlegen. Dies ist nicht notwendig, wenn Sie nur die Remoteausführung verwenden möchten.

   ![Intellij – Erweiterte Konfiguration – Remotedebuggen für Spark aktivieren](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. Die Konfiguration wird jetzt unter dem von Ihnen angegebenen Namen gespeichert. Um die Konfigurationsdetails anzuzeigen, wählen Sie den Konfigurationsnamen aus. Um Änderungen vorzunehmen, wählen Sie **Edit Configurations** (Konfigurationen bearbeiten) aus.

1. Nachdem Sie die Konfigurationseinstellungen abgeschlossen haben, können Sie das Projekt für den Remotecluster ausführen oder das Remotedebuggen durchführen.

   ![Intellij – Spark-Auftrag remotedebuggen – Schaltfläche „Remote ausführen“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. Klicken Sie auf die Schaltfläche **Trennen**, damit Die Übermittlungsprotokolle nicht im linken Bereich angezeigt werden. Die Ausführung wird jedoch auf dem Back-End fortgesetzt.

   ![Intellij – Spark-Auftrag remotedebuggen – Ergebnis von „Remote ausführen“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>Remotedebuggen

1. Richten Sie Breakpoints ein, und klicken Sie dann auf das Symbol **Remotedebuggen**. Der Unterschied bei der Remoteübermittlung besteht darin, dass der SSH-Benutzername und das Kennwort konfiguriert werden müssen.

   ![Intellij – Spark-Auftrag Remotedebuggen – Symbol „Debuggen“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. Wenn die Programmausführung den Breakpoint erreicht, werden eine Registerkarte **Driver** (Treiber) und zwei Registerkarten **Executor** im Bereich **Debugger** angezeigt. Klicken Sie auf das Symbol **Resume Program** (Programm fortsetzen), um die Ausführung des Codes fortzusetzen. Die Ausführung erreicht dann den nächsten Breakpoint. Sie müssen zur richtigen **Executor**-Registerkarte wechseln, um den Zielexecutor für das Debuggen zu finden. Sie können die Ausführungsprotokolle auf der entsprechenden Registerkarte **Console** (Konsole) anzeigen.

   ![Intellij – Spark-Auftrag Remotedebuggen – Registerkarte „Debuggen“](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>Remotedebuggen und Beheben von Fehlern

1. Richten Sie zwei Breakpoints ein, und klicken Sie dann auf das Symbol **Debug** (Debuggen), um das Remotedebuggen zu starten.

1. Der Code wird am ersten Breakpoint angehalten, und die Informationen zu Parametern und Variablen werden im Bereich **Variables** (Variablen) angezeigt.

1. Klicken Sie auf das Symbol **Resume Program** (Programm fortsetzen), um die Ausführung fortzusetzen. Der Code wird am zweiten Punkt beendet. Die Ausnahme wird wie erwartet abgefangen.

   ![Intellij – Spark-Auftrag Remotedebuggen – Auslösen von Fehler](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. Klicken Sie erneut auf das Symbol **Resume Program** (Programm fortsetzen). Im Fenster **HDInsight Spark Submission** (HDInsight Spark-Übermittlung) wird ein Fehler bei der Auftragsausführung angezeigt.

   ![Intellij – Spark-Auftrag Remotedebuggen – Fehler bei Übermittlung](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. Um den Wert der Variablen mithilfe der Debugfunktion von IntelliJ dynamisch zu aktualisieren, wählen Sie erneut **Debug** (Debuggen) aus. Der Bereich **Variables** (Variablen) wird wieder angezeigt.

1. Klicken Sie mit der Maustaste auf die Registerkarte **Debug**, und wählen Sie dann **Set Value** (Wert festlegen) aus. Als Nächstes geben Sie einen neuen Wert für die Variable ein. Wählen Sie dann **Enter** (Eingabe) aus, um den Wert zu speichern.

   ![Intellij – Spark-Auftrag Remotedebuggen – Wert festlegen](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. Klicken Sie auf das Symbol **Resume Program** (Programm fortsetzen), um die Programmausführung fortzusetzen. Dieses Mal wird keine Ausnahme abgefangen. Sie können sehen, dass das Projekt erfolgreich und ohne Ausnahmen ausgeführt wird.

   ![Intellij – Spark-Auftrag Remotedebuggen – ohne Ausnahme](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht: Apache Spark in Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Demo

* Erstellen von Scala-Projekten (Video): [Erstellen von Spark Scala-Anwendungen](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Remotedebuggen (Video): [Remotedebuggen von Apache Spark-Anwendungen in einem HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Szenarien

* [Apache Spark mit BI: Durchführen interaktiver Datenanalysen mithilfe von Spark in HDInsight mit BI-Tools](apache-spark-use-bi-tools.md)
* [Apache Spark mit Machine Learning: Analysieren von Gebäudetemperaturen mithilfe von Spark in HDInsight und HVAC-Daten](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark mit Machine Learning: Vorhersage von Lebensmittelkontrollergebnissen mithilfe von Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Websiteprotokollanalyse mithilfe von Apache Spark in HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Erstellen und Ausführen von Anwendungen

* [Erstellen einer eigenständigen Anwendung mit Scala](./apache-spark-create-standalone-application.md)
* [Ausführen von Remoteaufträgen in einem Apache Spark-Cluster mithilfe von Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Tools und Erweiterungen

* [Erstellen von Apache Spark-Anwendungen für einen HDInsight-Cluster mit dem Azure-Toolkit für IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Verwenden des Azure-Toolkits für IntelliJ zum Remotedebuggen von Apache Spark-Anwendungen über VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Apache Spark-Anwendungen](./apache-spark-eclipse-tool-plugin.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter Notebooks in einem Apache Spark-Cluster für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Ressourcen verwalten

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)