---
title: Debuggen von Spark-Aufträgen mit dem Azure-Toolkit für IntelliJ (Vorschau) – HDInsight
description: Leitfaden unter Verwendung der HDInsight-Tools im Azure-Toolkit für IntelliJ zum Debuggen von Anwendungen
keywords: Remotedebuggen von IntelliJ, Remotedebugging IntelliJ, SSH IntelliJ, HDInsight, Debuggen von Intellij, Debuggen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: ebed7d87ba538b2f886155527bb89c1ffd2bcf58
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545700"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Debuggen von fehlgeschlagenen Spark-Aufträgen mit dem Azure-Toolkit für IntelliJ (Vorschau)

Dieser Artikel enthält eine ausführliche Anleitung zur Verwendung der HDInsight-Tools im [Azure-Toolkit für IntelliJ](/azure/developer/java/toolkit-for-intellij) zum Ausführen von Anwendungen für das **Debuggen von fehlgeschlagenen Spark-Aufträgen** .

## <a name="prerequisites"></a>Voraussetzungen

* [Oracle Java Development Kit.](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) In diesem Tutorial wird die Java-Version 8.0.202 verwendet.
  
* IntelliJ IDEA. In diesem Artikel wird [IntelliJ IDEA Community 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure-Toolkit für IntelliJ. Weitere Informationen finden Sie unter [Installieren des Azure-Toolkits für IntelliJ](/azure/developer/java/toolkit-for-intellij/installation).

* Herstellen einer Verbindung mit Ihrem HDInsight-Cluster. Siehe [Herstellen einer Verbindung mit Ihrem HDInsight-Cluster](apache-spark-intellij-tool-plugin.md).

* Microsoft Azure Storage-Explorer. Siehe [Azure Storage-Explorer herunterladen](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Erstellen eines Projekts mit Debugvorlage

Erstellen Sie ein Spark 2.3.2-Projekt, um das Debuggen fortzusetzen. Verwenden Sie die Beispieldatei für das Debuggen von Aufgaben in diesem Dokument.

1. Öffnen Sie IntelliJ IDEA. Öffnen Sie das Fenster **Neues Projekt** .

   a. Wählen Sie im linken Bereich **Azure Spark/HDInsight** aus.

   b. Wählen Sie im Hauptfenster **Spark-Projekt mit Beispielen zum Debuggen von Fehleraufgaben (Vorschau) (Scala)** aus.

     ![Intellij – Erstellen eines Debugprojekts](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Wählen Sie **Weiter** aus.

2. Führen Sie im Fenster **New Project** (Neues Projekt) die folgenden Schritte aus:

   ![Intellij – Neues Projekt – Spark-Version auswählen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Geben Sie einen Projektnamen und -speicherort an.

   b. Wählen Sie in der Dropdownliste **Project SDK** (Projekt-SDK) **Java 1.8** für den **Spark 2.3.2** -Cluster aus.

   c. Wählen Sie in der Dropdown-Liste **Spark-Version** den Eintrag **Spark 2.3.2 (Scala 2.11.8)** aus.

   d. Wählen Sie **Fertig stellen** aus.

3. Wählen Sie **src** > **main** > **scala** aus, um Ihren Code im Projekt zu öffnen. In diesem Beispiel wird das Skript **AgeMean_Div()** verwendet.

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Ausführen einer Spark-Scala/Java-Anwendung in einem HDInsight-Cluster

Erstellen Sie eine Spark-Scala/Java-Anwendung, und führen Sie dann die Anwendung in einem Spark-Cluster aus, indem Sie die folgenden Schritte ausführen:

1. Klicken Sie auf **Konfiguration hinzufügen** , um das Fenster **Run/Debug Configurations** (Konfigurationen ausführen/debuggen) zu öffnen.

   ![HDI Intellij – Konfiguration hinzufügen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. Klicken Sie im Dialogfeld **Run/Debug Configurations** (Konfigurationen ausführen/debuggen) auf das Plussymbol ( **+** ). Wählen Sie dann die Option **Apache Spark auf HDInsight** aus.

   ![Intellij – Neue Konfiguration hinzufügen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Wechseln Sie zur Registerkarte **Remotely Run in Cluster** (Im Cluster remote ausführen). Geben Sie Informationen für **Name** , **Spark cluster** und **Main class name** (Name der main-Klasse) ein. Unseren Tools unterstützen das Debuggen mit **Executors** . Der Standardwert von **numExectors** ist 5. Es empfiehlt sich, hierfür keinen höheren Wert als 3 festzulegen. Wenn Sie die Laufzeit verkürzen möchten, können Sie **spark.yarn.maxAppAttempts** zu **Job Configurations** (Auftragskonfigurationen) hinzufügen und den Wert auf 1 festlegen. Klicken Sie auf **OK** , um die Konfiguration zu speichern.

   ![Intellij – Neue Konfigurationen debuggen/ausführen](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. Die Konfiguration wird jetzt unter dem von Ihnen angegebenen Namen gespeichert. Um die Konfigurationsdetails anzuzeigen, wählen Sie den Konfigurationsnamen aus. Um Änderungen vorzunehmen, wählen Sie **Edit Configurations** (Konfigurationen bearbeiten) aus.

5. Nachdem Sie die Konfigurationseinstellungen abgeschlossen haben, können Sie das Projekt für den Remotecluster ausführen.

   ![Intellij – Spark-Auftrag remotedebuggen – Schaltfläche „Remote ausführen“](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Sie können die Anwendungs-ID im Ausgabefenster überprüfen.

   ![Intellij – Spark-Auftrag remotedebuggen – Ergebnis von „Remote ausführen“](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>Herunterladen des Profils des fehlgeschlagenen Auftrags

Wenn die Auftragsübermittlung fehlschlägt, können Sie das Profil des fehlgeschlagenen Auftrags zum weiteren Debuggen auf den lokalen Computer herunterladen.

1. Öffnen Sie den **Microsoft Azure Storage-Explorer** , suchen Sie das HDInsight-Konto des Clusters für den fehlerhaften Auftrag, und laden Sie die Ressourcen des fehlerhaften Auftrags vom Speicherort **\hdp\spark2-events\\.spark-failures\\\<application ID>** in einen lokalen Ordner herunter. Im Fenster **Aktivitäten** wird der Fortschritt des Downloads angezeigt.

   ![Azure Storage-Explorer – Download fehlerhaft](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure Storage-Explorer – Download erfolgreich](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Konfigurieren der lokalen Debugumgebung und Debuggen eines Fehlers

1. Öffnen Sie das ursprüngliche Projekt, oder erstellen Sie ein neues Projekt, und ordnen Sie es dem ursprünglichen Quellcode zu. Derzeit wird das Debuggen fehlgeschlagener Aufträge nur in Spark, Version 2.3.2, unterstützt.

1. Erstellen Sie in IntelliJ IDEA unter **Spark Failure Debug** (Debuggen von Spark-Fehlern) eine Konfigurationsdatei, und wählen Sie für das Feld **Spark Job Failure Context location** (Speicherort des Fehlerkontexts für den Spark-Auftrag) die FTD-Datei für die zuvor heruntergeladenen Ressourcen des fehlgeschlagenen Auftrags aus.

   ![Erstellen Fehler Konfiguration](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. Klicken Sie auf der Symbolleiste auf die Schaltfläche für lokales Ausführen, und der Fehler wird im Ausführungsfenster angezeigt.

   ![run-failure-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![run-failure-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. Legen Sie den im Protokoll angegebenen Haltepunkt fest, und klicken Sie dann auf die Schaltfläche für das lokale Debuggen, um das lokale Debuggen wie in normalen Scala-/Java-Projekten in IntelliJ auszuführen.

1. Wenn das Projekt nach dem Debuggen erfolgreich abgeschlossen wurde, können Sie den fehlgeschlagenen Auftrag erneut an den Cluster in Spark für HDInsight übermitteln.

## <a name="next-steps"></a><a name="seealso"></a>Nächste Schritte

* [Übersicht: Debuggen von Apache Spark-Anwendungen](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

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
* [Verwenden der HDInsight-Tools für IntelliJ mit Hortonworks Sandbox](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
* [Verwenden der HDInsight-Tools im Azure-Toolkit für Eclipse zum Erstellen von Apache Spark-Anwendungen](./apache-spark-eclipse-tool-plugin.md)
* [Verwenden von Apache Zeppelin Notebooks mit einem Apache Spark-Cluster unter HDInsight](apache-spark-zeppelin-notebook.md)
* [Verfügbare Kernels für Jupyter Notebooks in einem Apache Spark-Cluster für HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Verwenden von externen Paketen mit Jupyter Notebooks](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installieren von Jupyter Notebook auf Ihrem Computer und Herstellen einer Verbindung zum Apache Spark-Cluster in Azure HDInsight (Vorschau)](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Ressourcen verwalten

* [Verwalten von Ressourcen für den Apache Spark-Cluster in Azure HDInsight](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Nachverfolgen und Debuggen von Aufträgen in einem Apache Spark-Cluster unter HDInsight)](apache-spark-job-debugging.md)