---
title: 'Apache Hadoop: Data Lake Tools für Visual Studio – Azure HDInsight'
description: Hier erfahren Sie, wie Sie Data Lake Tools für Visual Studio installieren. Verwenden Sie das Tool, um in Azure HDInsight eine Verbindung mit Apache Hadoop-Clustern herzustellen, und führen Sie dann Hive-Abfragen durch.
keywords: Hadoop-Tools, Hive-Abfrage, Visual Studio, Visual Studio Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: 56154f7e5ebd71e1b6580bec55ca57520eb126ff
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548012"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Herstellen einer Verbindung mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake Tools für Visual Studio

Erfahren Sie, wie Sie Microsoft Azure Data Lake und Stream Analytics-Tools für Visual Studio (Data Lake-Tools) verwenden. Verwenden Sie das Tool, um eine Verbindung mit [Apache Hadoop-Clustern in Azure HDInsight](apache-hadoop-introduction.md) herzustellen, und führen Sie dann Hive-Abfragen durch.  

Weitere Informationen zum Verwenden von HDInsight finden Sie unter [Erste Schritte mit HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Weitere Informationen zum Herstellen einer Verbindung mit Apache Storm finden Sie unter [Entwickeln von C#-Topologien für Apache Storm mithilfe von Data Lake-Tools für Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Sie können Data Lake Tools für Visual Studio verwenden, um auf Data Lake Analytics und HDInsight zuzugreifen. Weitere Informationen zu Data Lake Tools finden Sie unter [Entwickeln von U-SQL-Skripts mit Data Lake Tools für Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Voraussetzungen

Um diesen Artikel durchzuarbeiten und Data Lake Tools für Visual Studio zu verwenden, benötigen Sie Folgendes:

* Ein Azure HDInsight-Cluster. Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erste Schritte bei der Verwendung von Apache Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md). Zum Ausführen interaktiver Apache Hive-Abfragen benötigen Sie einen [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-Cluster.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/). Die [Visual Studio Community-Edition](https://visualstudio.microsoft.com/vs/community/) ist kostenlos erhältlich. Die hier enthaltenen Anweisungen gelten für [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

## <a name="install-data-lake-tools-for-visual-studio"></a>Installieren von Data Lake-Tools für Visual Studio  

Befolgen Sie die entsprechenden Anweisungen, um Data Lake Tools für Ihre Version von Visual Studio zu installieren:

* Visual Studio 2017 oder Visual Studio 2019:

    Stellen Sie bei der Installation von Visual Studio sicher, dass Sie die Workload **Azure-Entwicklung** oder **Datenspeicherung und -verarbeitung** einfügen.  

    Navigieren Sie bei vorhandenen Visual Studio-Installationen zur IDE-Menüleiste, und wählen Sie **Extras** > **Tools und Features abrufen** aus, um Visual Studio-Installer zu öffnen. Wählen Sie auf der Registerkarte **Workloads** mindestens die Workload **Azure-Entwicklung** (unter **Web und Cloud** ) aus. Wählen Sie alternativ die Workload **Datenspeicherung und -verarbeitung** (unter **Andere Toolsets** ) aus.

  ![Workloadauswahl, Visual Studio-Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

* Visual Studio 2015:

    [Data Lake Tools herunterladen](https://www.microsoft.com/download/details.aspx?id=49504). Wählen Sie die passende Version von Data Lake Tools für Ihre Visual Studio-Version aus.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualisieren von Data Lake Tools für Visual Studio  

Aktualisieren Sie als Nächstes Data Lake Tools auf die neueste Version.

1. Öffnen Sie Visual Studio.

2. Wählen Sie im Fenster **Start** die Option **Ohne Code fortfahren** aus.

3. Wählen Sie in der Visual Studio-IDE-Menüleiste die Optionen **Erweiterungen** > **Erweiterungen verwalten** aus.

4. Erweitern Sie im Dialogfeld **Erweiterungen verwalten** den Knoten **Updates** .

5. Wenn in der Liste der verfügbaren Updates der Eintrag für die **Azure Data Lake- und Stream Analytic-Tools** aufgeführt ist, wählen Sie ihn aus. Wählen Sie dann die zugehörige Schaltfläche **Aktualisieren** aus. Nachdem das Dialogfeld **Herunterladen und installieren** eingeblendet und dann wieder ausgeblendet wurde, wird die Erweiterung mit den **Azure Data Lake- und Stream Analytics-Tools** in Visual Studio dem Zeitplan für Updates hinzugefügt.

6. Schließen Sie alle Visual Studio-Fenster. Das Dialogfeld **VSIX Installer** wird angezeigt.

7. Wählen Sie **Lizenz** aus, um die Lizenzbedingungen zu lesen, und dann **Schließen** , um zum Dialogfeld **VSIX Installer** zurückzukehren.

8. Wählen Sie **Ändern** aus. Die Installation des Erweiterungsupdates wird gestartet. Kurze Zeit später wird im Dialogfeld angezeigt, dass die Änderungen vorgenommen wurden. Wählen Sie **Schließen** aus, und starten Sie dann Visual Studio neu, um die Installation abzuschließen.

> [!NOTE]  
> Sie können Data Lake Tools Version 2.3.0.0 oder höher nur verwenden, um eine Verbindung mit Interactive Query-Clustern herzustellen und interaktive Hive-Abfragen auszuführen.

## <a name="connect-to-azure-subscriptions"></a>Herstellen einer Verbindung mit Azure-Abonnements

Mit Data Lake Tools für Visual Studio können Sie eine Verbindung mit Ihren HDInsight-Clustern herstellen, einige einfache Verwaltungsvorgänge durchführen und Hive-Abfragen ausführen.

> [!NOTE]  
> Informationen zum Herstellen einer Verbindung mit einem generischen Hadoop-Cluster finden Sie unter [How to write and submit Hive queries using Visual Studio](/archive/blogs/xiaoyong/how-to-write-and-submit-hive-queries-using-visual-studio) (Schreiben und Übermitteln von Hive-Abfragen in Visual Studio).

### <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

So stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her:

1. Öffnen Sie Visual Studio.

2. Wählen Sie im Fenster **Start** die Option **Ohne Code fortfahren** aus.

3. Wählen Sie auf der Menüleiste der IDE die Option **Ansicht** > **Server-Explorer** aus.

4. Klicken Sie in **Server-Explorer** mit der rechten Maustaste auf den Knoten **Azure** , wählen Sie **Verbindung mit Microsoft Azure-Abonnement herstellen** aus, und schließen Sie den Authentifizierungsvorgang ab. Erweitern Sie in **Server-Explorer** den Knoten **Azure** > **HDInsight** , um eine Liste der vorhandenen HDInsight-Cluster anzuzeigen.

5. Wenn Sie noch keine Cluster festgelegt haben, können Sie sie über das Azure-Portal, Azure PowerShell oder das HDInsight SDK erstellen. Weitere Informationen finden Sie unter [Einrichten von Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Liste der HDInsight-Cluster, Server-Explorer, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. Erweitern Sie einen HDInsight-Cluster. Der Cluster enthält Knoten für **Hive-Datenbanken** . Außerdem ein Standardspeicherkonto, ggf. weitere verknüpfte Speicherkonten und das **Hadoop-Dienstprotokoll** . Sie können die einzelnen Elemente weiter erweitern.

Nach dem Herstellen der Verbindung mit Ihrem Azure-Abonnement können Sie die folgenden Aufgaben durchführen.

### <a name="connect-to-azure-from-visual-studio"></a>Herstellen einer Verbindung mit Azure in Visual Studio

So stellen Sie in Visual Studio eine Verbindung mit dem Azure-Portal her:

1. Erweitern Sie in **Server-Explorer** die Knoten **Azure** > **HDInsight** , und wählen Sie Ihren Cluster aus.

2. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und wählen Sie dann **Cluster in Azure-Portal verwalten** aus.

### <a name="offer-questions-and-feedback-from-visual-studio"></a>Fragen und Feedback in Visual Studio

So können Sie in Visual Studio Fragen stellen und/oder Feedback geben:

1. Wählen Sie in Server-Explorer die Knoten **Azure** > **HDInsight** aus.

2. Klicken Sie mit der rechten Maustaste auf **HDInsight** , und wählen Sie entweder **MSDN-Forum** aus, um Fragen zu stellen, oder **Feedback abgeben** , um Feedback einzureichen.

## <a name="link-to-or-edit-a-cluster"></a>Verknüpfen oder Bearbeiten eines Clusters

> [!NOTE]
> Derzeit können nur HDInsight-Cluster des Typs „Hive“ verknüpft werden.

So verknüpfen Sie einen HDInsight-Cluster

1. Klicken Sie mit der rechten Maustaste auf **HDInsight** , und wählen Sie dann **HDInsight-Cluster verknüpfen** aus, um das Dialogfeld **HDInsight-Cluster verknüpfen** anzuzeigen.

2. Geben Sie im Formular `https://CLUSTERNAME.azurehdinsight.net` eine **Verbindungs-URL** ein. Der **Clustername** wird automatisch mit dem Clusternamenteil der URL gefüllt, wenn Sie zu einem anderen Feld wechseln. Geben Sie dann einen **Benutzernamen** und ein **Kennwort** ein, und wählen Sie **Weiter** aus.

    ![Verknüpfen eines Clusters, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. Wählen Sie **Fertig stellen** aus. Wenn der Cluster erfolgreich verknüpft wurde, wird er unter dem Knoten **HDInsight** aufgeführt.

Um einen verknüpften Cluster zu aktualisieren, klicken Sie mit der rechten Maustaste auf den Cluster, und wählen Sie **Bearbeiten** aus. Dann können Sie die Clusterinformationen aktualisieren.

![Bearbeiten eines verknüpften Clusters, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Erkunden der verknüpften Ressourcen

Im Server-Explorer werden das Standardspeicherkonto und verknüpfte Speicherkonten angezeigt. Wenn Sie das Standardspeicherkonto erweitern, werden die Container im Speicherkonto angezeigt. Das Standardspeicherkonto und der Standardcontainer sind markiert.

![Verknüpfte Ressourcen von Data Lake Tools für Visual Studio in Server-Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

Klicken Sie mit der rechten Maustaste auf einen Container, und wählen Sie **Container anzeigen** aus, um den Inhalt des Containers anzuzeigen. Nach dem Öffnen eines Containers können Sie über die verschiedenen Symbolleistenschaltflächen die Inhaltsliste **aktualisieren** , ein **Blob hochladen** , **ausgewählte Blobs löschen** , ein **Blob öffnen** und ausgewählte Blobs herunterladen ( **Speichern unter** ).

![Containerliste und Vorgänge für Blobs, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>Ausführen interaktiver Apache Hive-Abfragen

[Apache Hive](https://hive.apache.org) ist eine Data Warehouse-Infrastruktur, die auf Hadoop basiert. Hive wird für die Datenzusammenfassung, Abfragen und die Analyse verwendet. Sie können Data Lake Tools für Visual Studio verwenden, um Hive-Abfragen in Visual Studio auszuführen. Weitere Informationen zu Hive finden Sie unter [Was sind Apache Hive und HiveQL in Azure HDInsight?](hdinsight-use-hive.md)

[Interactive Query in Azure HDInsight](../interactive-query/apache-interactive-query-get-started.md) verwendet [Hive unter LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query bietet Interaktivität für komplexe Data Warehouse-Abfragen von umfangreichen, gespeicherten Datasets. Die Ausführung von Hive-Abfragen mit Interactive Query ist im Vergleich zu herkömmlichen Hive-Batchaufträgen deutlich schneller. 

> [!NOTE]  
> Das Ausführen von interaktiven Hive-Abfragen ist nur möglich, wenn Sie eine Verbindung mit einem [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-Cluster herstellen.

Sie können Data Lake Tools für Visual Studio auch verwenden, um den Inhalt eines Hive-Auftrags anzuzeigen. Data Lake Tools für Visual Studio sammelt die YARN-Protokolle bestimmter Hive-Aufträge und zeigt sie an.

Wählen Sie in **Server-Explorer** die Knoten **Azure** > **HDInsight** und dann Ihren Cluster aus.  Dieser Knoten ist der Ausgangspunkt in **Server-Explorer** für die folgenden Abschnitte.

### <a name="view-hivesampletable"></a>Anzeigen der Beispieltabelle „hivesampletable“

Alle HDInsight-Cluster verfügen über eine Hive-Beispieltabelle mit dem Namen `hivesampletable`.  

Wählen Sie in Ihrem Cluster die Knoten **Hive-Datenbanken** > **Standard** > **hivesampletable** aus.

* So zeigen Sie das `hivesampletable`-Schema an

    Erweitern Sie **hivesampletable** . Die Namen und Datentypen der `hivesampletable`-Spalten werden angezeigt.

* So zeigen Sie die `hivesampletable`-Daten an

    Klicken Sie mit der rechten Maustaste auf **hivesampletable** und dann auf **Erste 100 Zeilen anzeigen** . Eine Liste mit 100 Ergebnissen wird im Fenster **Hive-Tabelle: hivesampletable** angezeigt. Diese Aktion entspricht dem Ausführen der folgenden Hive-Abfrage mithilfe des Hive-ODBC-Treibers:

    `SELECT * FROM hivesampletable LIMIT 100`

    Sie können die Zeilenanzahl anpassen, indem Sie **Anzahl der Zeilen** ändern: In der Dropdownliste können Sie 50, 100, 200 oder 1.000 Zeilen auswählen.

### <a name="create-hive-tables"></a>Erstellen von Hive-Tabellen

Zum Erstellen einer Hive-Tabelle können Sie die grafische Benutzeroberfläche oder Hive-Abfragen verwenden. Informationen zum Verwenden von Hive-Abfragen finden Sie unter [Erstellen und Ausführen von Hive-Abfragen](#create-and-run-hive-queries).

1. Wählen Sie in Ihrem Cluster die Knoten **Hive-Datenbanken** > **Standard** aus.

2. Klicken Sie mit der rechten Maustaste auf **Standard** , und wählen Sie dann **Tabelle erstellen** aus.

3. Konfigurieren Sie die Tabelle.

4. Wählen Sie die Schaltfläche **Tabelle erstellen** aus, um den Auftrag zu übermitteln, mit dem die neue Hive-Tabelle erstellt wird.

    ![Fenster „Tabelle erstellen“, Hive, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>Erstellen und Ausführen von Hive-Abfragen

Zum Erstellen und Ausführen von Hive-Abfragen stehen Ihnen zwei Möglichkeiten zur Auswahl:

* Erstellen von Ad-hoc-Abfragen
* Erstellen einer Hive-Anwendung

#### <a name="create-an-ad-hoc-query"></a>Erstellen einer Ad-hoc-Abfrage

So können Sie eine Ad-hoc-Abfrage erstellen und ausführen

1. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und wählen Sie dann **Hive-Abfrage schreiben** aus.  

2. Geben Sie eine Hive-Abfrage ein.

    Der Hive-Editor unterstützt IntelliSense. Data Lake Tools für Visual Studio unterstützt das Laden von Remotemetadaten, wenn Sie Ihr Hive-Skript bearbeiten. Wenn Sie beispielsweise `SELECT * FROM` eingeben, listet IntelliSense alle vorgeschlagenen Tabellennamen auf. Wird ein Tabellenname angegeben, listet IntelliSense die Spaltennamen auf. Die Tools unterstützen die meisten Hive-DML-Anweisungen, Unterabfragen und integrierte UDFs.

    ![Beispiel 1 für IntelliSense, Hive-Ad-hoc-Abfrage, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![Beispiel 2 für IntelliSense, Hive-Ad-hoc-Abfrage, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense schlägt nur die Metadaten des Clusters vor, der auf der HDInsight-Symbolleiste ausgewählt ist.

    Sie können folgende Beispielabfrage verwenden:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. Wählen Sie den Ausführungsmodus aus:

    * **Interactive**  

        Wählen Sie in der ersten Dropdownliste die Option **Interaktiv** und dann **Ausführen** aus.

        ![Interaktiver Modus, Hive-Ad-hoc-Abfrage, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **Batch**  

        Wählen Sie in der ersten Dropdownliste die Option **Batch** und dann **Senden** aus. Alternativ können Sie das Dropdownsymbol neben **Senden** und dann **Erweitert** auswählen.

        ![Batchmodus, Hive-Ad-hoc-Abfrage, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        Wenn Sie unter „Übermitteln“ die Option „Erweitert“ auswählen, wird das Dialogfeld **Skript übermitteln** angezeigt. Konfigurieren Sie **Auftragsname** , **Argumente** , **Zusätzliche Konfigurationen** und **Statusverzeichnis** für das Skript.

        ![Dialogfeld „Skript übermitteln“, Hive-Ad-hoc-Abfrage, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > Sie können keine Batches an Interactive Query-Cluster senden.  Dazu müssen Sie den interaktiven Modus verwenden.

#### <a name="create-a-hive-application"></a>Erstellen einer Hive-Anwendung

So erstellen Sie eine Hive-Lösung und führen Sie sie aus:

1. Wählen Sie auf der Menüleiste die Option **Datei** > **Neu** > **Projekt** aus.

2. Wählen Sie im Fenster **Neues Projekt erstellen** das Suchfeld aus, und geben Sie **Hive** ein. Wählen Sie dann **Hive Application** (Hive-Anwendung) und anschließend **Weiter** aus.

3. Geben Sie im Fenster **Neues Projekt konfigurieren** einen **Projektnamen** ein, wählen Sie den **Speicherort** des Projekts aus, oder erstellen Sie ihn, und wählen Sie dann **Erstellen** aus.

    ![Neue Hive-Anwendung, Fenster „Neues Projekt konfigurieren“, HDInsight, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.hql** , um das Skript zu öffnen.

### <a name="view-job-summary-and-output"></a>Anzeigen der Auftragszusammenfassung und -ausgabe

Die Auftragszusammenfassung unterscheidet sich zwischen den Modi **Batch** und **Interactive** (Interaktiv).

![Fenster mit Hive-Auftragszusammenfassung, interaktiver und Batchmodus, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

Verwenden Sie das Symbol **Aktualisieren** , um den Status zu aktualisieren, bis sich der Auftragsstatus in **Abgeschlossen** ändert.  

* Wenn Sie die Auftragsdetails im Modus **Batch** anzeigen möchten, wählen Sie unten die entsprechenden Links aus, um die **Auftragsabfrage** , die **Auftragsausgabe** , das **Auftragsprotokoll** oder **YARN-Protokolle** anzuzeigen.

* Die Auftragsdetails im Modus **Interaktiv** werden in den Bereichen **Ausgabe** und **HiveServer2-Ausgabe** angezeigt.

    ![Interaktive Hive-Auftragsausgabe, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>Anzeigen von Auftragsdiagrammen

Aktuell werden Auftragsdiagramme nur für Hive-Aufträge angezeigt, die Tez als Ausführungs-Engine verwenden.  Informationen zum Aktivieren von Tez finden Sie unter [Was sind Apache Hive und HiveQL in Azure HDInsight?](hdinsight-use-hive.md)  Weitere Informationen finden Sie im Abschnitt „Verwenden von Apache Tez anstelle von MapReduce“ unter [Optimieren von Apache Hive-Abfragen in Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Um alle Operatoren am Scheitelpunkt anzuzeigen, doppelklicken Sie auf die Scheitelpunkte des Auftragsgraphs. Sie können auch auf einen bestimmten Operator zeigen, um weitere Details anzuzeigen.

Auch wenn Tez als Ausführungs-Engine angegeben ist, wird der Auftragsgraph möglicherweise nicht angezeigt, wenn keine Tez-Anwendung gestartet wird.  Diese Situation könnte auftreten, wenn der Auftrag keine DML-Anweisungen enthält. Ein anderer Grund wäre, dass die DML-Anweisungen eine Rückgabe liefern können, ohne eine Tez-Anwendung zu starten. Durch `SELECT * FROM table1` wird die Tez-Anwendung z. B. nicht gestartet.

![Apache Hive-Auftragsgraph, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>Anzeigen von Details zur Taskausführung

In der Auftragsdiagramm können Sie **Details zur Taskausführung** auswählen, um strukturierte und visualisierte Informationen zu Hive-Aufträgen anzeigen. Sie können auch weitere Auftragsinformationen abrufen. Im Fall von Leistungsproblemen können Sie in der Ansicht weitere Details zum Problem anzeigen. Beispielsweise können Sie Informationen zur Funktionsweise jeder Aufgabe und ausführliche Informationen zu den einzelnen Aufgaben (Lesen und Schreiben von Daten, Zeitplan, Start- und Endzeit usw.) anzeigen. Mithilfe der visualisierten Informationen können Sie Auftragskonfigurationen oder die Systemarchitektur optimieren.

![Fenster „Taskausführungsansicht“, Data Lake Tools für Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>Anzeigen von Hive-Aufträgen

Sie können Auftragsabfragen, die Auftragsausgabe, Auftragsprotokolle und YARN-Protokolle für Hive-Aufträge anzeigen.

In der neuesten Version der Tools können Sie den Inhalt Ihrer Hive-Aufträge anzeigen, indem Sie YARN-Protokolle erfassen und anzeigen. Ein YARN-Protokoll kann Ihnen bei der Untersuchung von Leistungsproblemen helfen. Weitere Informationen zur Erfassung von YARN-Protokollen in HDInsight finden Sie unter [Zugreifen auf Apache Hadoop YARN-Anwendungsprotokolle](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

So zeigen Sie Hive-Aufträge an:

1. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Aufträge anzeigen** .

    ![Anzeigen von Aufträgen, Apache Hive, HDInsight-Cluster, Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    Eine Liste der für den Cluster ausgeführten Hive-Aufträge wird angezeigt.  

2. Wählen Sie einen Auftrag aus. Wählen Sie im Fenster **Hive Job Summary** (Hive-Auftragszusammenfassung) einen der folgenden Links aus:
    - **Job Query** (Auftragsabfrage)
    - **Auftragsausgabe**
    - **Job Log** (Auftragsprotokoll)  
    - **Yarn-Protokoll**

## <a name="run-apache-pig-scripts"></a>Ausführen von Apache Pig-Skripts

1. Wählen Sie auf der Menüleiste die Option **Datei** > **Neu** > **Projekt** aus.

2. Wählen Sie im Fenster **Start** das Suchfeld aus, und geben Sie **Pig** ein. Wählen Sie dann die **Pig-Anwendung** und anschließend **Weiter** aus.

3. Geben Sie im Fenster **Neues Projekt konfigurieren** einen **Projektnamen** ein, und wählen Sie einen **Speicherort** für das Projekt aus, oder erstellen Sie einen. Klicken Sie anschließend auf **Erstellen** .

4. Doppelklicken Sie im IDE-Bereich **Projektmappen-Explorer** auf **cript.pig** , um das Skript zu öffnen.

## <a name="feedback-and-known-issues"></a>Feedback und bekannte Probleme

* Ein Problem, aufgrund dessen mit NULL-Werten gestartete Ergebnisse nicht angezeigt werden, wurde behoben. Wenden Sie sich an das Supportteam, falls die Ausführung bei Ihnen durch dieses Problem blockiert wird.

* Das von Visual Studio erstellte HQL-Skript wird abhängig von der lokalen Regionseinstellung des Benutzers codiert. Das Skript wird nicht korrekt ausgeführt, wenn Sie es als Binärdatei in einen Cluster hochladen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie das Data Lake Tools für Visual Studio-Paket verwenden, um in Visual Studio eine Verbindung mit HDInsight-Clustern herzustellen. Zudem haben Sie gelernt, wie Sie eine Hive-Abfrage ausführen. 

* [Ausführen von Apache Hive-Abfragen mit Data Lake Tools für Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Was sind Apache Hive und HiveQL in Azure HDInsight?](hdinsight-use-hive.md)
* [Erstellen eines Apache Hadoop-Clusters: Vorlage](apache-hadoop-linux-tutorial-get-started.md)
* [Übermitteln von Apache Hadoop-Aufträgen in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysieren von Twitter-Daten mit Apache Hive und Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data-linux.md)