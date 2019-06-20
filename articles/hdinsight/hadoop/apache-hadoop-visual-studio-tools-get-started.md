---
title: Herstellen einer Verbindung mit Azure Hadoop mithilfe von Data Lake Tools für Visual Studio – Azure HDInsight
description: Hier erfahren Sie, wie Sie Data Lake Tools für Visual Studio installieren und verwenden, um eine Verbindung mit Apache Hadoop-Clustern in Azure HDInsight herzustellen, und anschließend Hive-Abfragen ausführen.
keywords: Hadoop-Tools, Hive-Abfrage, Visual Studio, Visual Studio Hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 42ef03d604caacf5ba18773b88e892237b5f0eae
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688529"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>Herstellen einer Verbindung mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake Tools für Visual Studio

Hier erfahren Sie, wie Sie mit [Microsoft Azure Data Lake- und Stream Analytics-Tools für Visual Studio](../hdinsight-hadoop-introduction.md) (auch als Data Lake Tools bezeichnet) eine Verbindung mit Apache Hadoop-Clustern in [Azure HDInsight](https://www.microsoft.com/download/details.aspx?id=49504) herstellen und Hive-Abfragen übermitteln können.  

Weitere Informationen zur Verwendung von HDInsight finden Sie unter [Einführung in HDInsight](../hdinsight-hadoop-introduction.md) und [Erste Schritte mit HDInsight](apache-hadoop-linux-tutorial-get-started.md).  

Weitere Informationen zum Herstellen einer Verbindung mit einem Apache Storm-Cluster finden Sie im Artikel zum [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](../storm/apache-storm-develop-csharp-visual-studio-topology.md).

Sie können Data Lake Tools für Visual Studio verwenden, um auf Data Lake Analytics und HDInsight zuzugreifen. Weitere Informationen zu Data Lake Tools finden Sie unter [Entwickeln von U-SQL-Skripts mit Data Lake Tools für Visual Studio](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md).

## <a name="prerequisites"></a>Voraussetzungen

Um dieses Tutorial durchzuführen und Data Lake Tools für Visual Studio zu verwenden, benötigen Sie Folgendes:

* Ein Azure HDInsight-Cluster. Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erste Schritte bei der Verwendung von Apache Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md). Zum Ausführen interaktiver Apache Hive-Abfragen benötigen Sie einen [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-Cluster.  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) (2013 und höher).  Die [Visual Studio Community-Edition](https://visualstudio.microsoft.com/vs/community/) ist kostenlos erhältlich.  Weitere Informationen finden Sie unter [Installieren von Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) und [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). Visual Studio 2019 enthält leichte Abweichungen bei Schnittstellen.

  > [!IMPORTANT]  
  > Data Lake Tools wird nicht mehr für Visual Studio 2013 unterstützt.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installieren von Data Lake-Tools für Visual Studio  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 oder Visual Studio 2019  
  Achten Sie bei der Installation darauf, dass Sie mindestens die Workload **Azure development** (Azure-Entwicklung) oder **Datenspeicherung und -verarbeitung** einbeziehen.  

  Navigieren Sie für vorhandene Installationen über die Menüleiste zu **Extras** > **Tools und Features abrufen...** , um den Visual Studio-Installer zu öffnen.  Wählen Sie dann mindestens die Workload **Azure development** (Azure-Entwicklung) oder **Datenspeicherung und -verarbeitung** aus.

  ![Screenshot vom Visual Studio-Installer](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 und 2015  
  [Data Lake Tools herunterladen](https://www.microsoft.com/download/details.aspx?id=49504). Wählen Sie die passende Version von Data Lake Tools für Ihre Visual Studio-Version aus.  

> [!NOTE]  
> Derzeit ist nur die englische Version von Data Lake Tools für Visual Studio verfügbar.

## <a name="update-data-lake-tools-for-visual-studio"></a>Aktualisieren von Data Lake Tools für Visual Studio  

1. Öffnen Sie Visual Studio.

2. Navigieren Sie über die Menüleiste zu **Extras** > **Extensions und Updates...** .

3. Erweitern Sie im Fenster **Extensions und Updates** die Option **Updates** auf der linken Seite.

4. Wenn ein Update verfügbar ist, wird **Azure Data Lake and Stream Analytic Tools** (Azure Data Lake- und Stream Analytics-Tools) im Hauptfenster angezeigt.  Wählen Sie **Update** aus.

> [!NOTE]  
> Sie können Data Lake Tools Version 2.3.0.0 oder höher nur verwenden, um eine Verbindung mit Interactive Query-Clustern herzustellen und interaktive Hive-Abfragen auszuführen.

## <a name="connect-to-azure-subscriptions"></a>Herstellen einer Verbindung mit Azure-Abonnements
Mithilfe von Data Lake Tools für Visual Studio können Sie eine Verbindung mit Ihren HDInsight-Clustern herstellen, einige einfache Verwaltungsvorgänge durchführen und Hive-Abfragen ausführen.

> [!NOTE]  
> Informationen zum Herstellen einer Verbindung mit einem generischen Hadoop-Cluster finden Sie unter [Write and submit Hive queries by using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx) (Schreiben und Übermitteln von Hive-Abfragen mithilfe von Visual Studio).

So stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her:

1. Öffnen Sie Visual Studio.

2. Navigieren Sie über die Menüleiste zu **Ansicht** > **Server-Explorer**.

3. Klicken Sie im Server-Explorer mit der rechten Maustaste auf den Knoten **Azure**, klicken Sie auf **Verbindung mit Microsoft Azure-Abonnement herstellen...** , und melden Sie sich an.

4. Im Server-Explorer wird eine Liste der vorhandenen HDInsight-Cluster angezeigt. Wenn Sie noch keine Cluster haben, können Sie über das Azure-Portal, Azure PowerShell oder das HDInsight SDK Cluster erstellen. Weitere Informationen finden Sie unter [Erstellen von HDInsight-Clustern](../hdinsight-hadoop-provision-linux-clusters.md).

   ![Screenshot der Clusterliste von Data Lake Tools für Visual Studio im Server-Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "Clusterliste von Data Lake Tools für Visual Studio im Server-Explorer")

5. Erweitern Sie einen HDInsight-Cluster. **Hive-Datenbanken**, das Standardspeicherkonto, verknüpfte Speicherkonten und das **Hadoop-Dienstprotokoll** werden angezeigt. Sie können die einzelnen Elemente weiter erweitern.

Nach dem Herstellen der Verbindung mit Ihrem Azure-Abonnement können Sie die folgenden Aufgaben durchführen.

So stellen Sie in Visual Studio eine Verbindung mit dem Azure-Portal her:

1. Navigieren Sie im Server-Explorer zu **Azure** > **HDInsight**, und wählen Sie Ihren Cluster aus.

2. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und wählen Sie **Cluster in Azure-Portal verwalten** aus.

So können Sie in Visual Studio Fragen stellen und/oder Feedback geben:

1. Navigieren Sie im Server-Explorer zu **Azure** > **HDInsight**.

2. Klicken Sie mit der rechten Maustaste auf **HDInsight**, und wählen Sie entweder **MSDN-Forum** aus, um Fragen zu stellen, oder **Feedback abgeben**, um Feedback einzureichen.

## <a name="link-a-cluster"></a>Verknüpfen eines Clusters
Sie können einen Cluster verknüpfen, indem Sie mit der rechten Maustaste auf **HDInsight** klicken und dann **HDInsight-Cluster verknüpfen** auswählen. Geben Sie **Verbindungs-URL**, **Benutzername** und **Kennwort** ein, klicken Sie auf **Weiter** und dann auf **Fertig stellen**. Der Cluster sollte erfolgreich unter „HDInsight-Knoten“ aufgelistet werden.

![Screenshot von Data Lake Tools für das Dialogfeld von Visual Studio zum Verknüpfen von Clustern](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

Klicken Sie mit der rechten Maustaste auf den verknüpften Cluster, und wählen Sie **Bearbeiten** aus. Der Benutzer kann die Clusterinformationen aktualisieren. Die Option zum Hinzufügen von HDInsight-Clustern unterstützt derzeit nur Hive.

![Screenshot von Data Lake Tools für das Dialogfeld von Visual Studio zum Verknüpfen von Clustern – Update](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>Erkunden der verknüpften Ressourcen
Im Server-Explorer werden das Standardspeicherkonto und verknüpfte Speicherkonten angezeigt. Wenn Sie das Standardspeicherkonto erweitern, werden die Container im Speicherkonto angezeigt. Das Standardspeicherkonto und der Standardcontainer sind markiert. Klicken Sie mit der rechten Maustaste auf einen der Container, um seinen Inhalt anzuzeigen.

![Screenshot der Liste der verknüpften Ressourcen von Data Lake Tools für Visual Studio im Server-Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "Liste der verknüpften Ressourcen")

Nach dem Öffnen eines Containers können Sie mithilfe der folgenden Schaltflächen Blobs hochladen, löschen und herunterladen:

![Screenshot der Blobvorgänge von Data Lake Tools für Visual Studio im Server-Explorer](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "Hochladen, Löschen und Herunterladen von Blobs im Server-Explorer")

## <a name="run-interactive-apache-hive-queries"></a>Ausführen interaktiver Apache Hive-Abfragen
[Apache Hive](https://hive.apache.org) ist eine Data Warehouse-Infrastruktur, die auf Hadoop basiert. Hive wird für die Datenzusammenfassung, Abfragen und die Analyse verwendet. Sie können Data Lake Tools für Visual Studio verwenden, um Hive-Abfragen in Visual Studio auszuführen. Weitere Informationen zu Hive finden Sie unter [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md).

[Interactive Query](../interactive-query/apache-interactive-query-get-started.md) verwendet [Hive unter LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) in Apache Hive 2.1. Interactive Query bietet Interaktivität für komplexe Data Warehouse-Abfragen von umfangreichen, gespeicherten Datasets. Die Ausführung von Hive-Abfragen mit Interactive Query ist im Vergleich zu herkömmlichen Hive-Batchaufträgen deutlich schneller. 

> [!NOTE]  
> Das Ausführen von interaktiven Hive-Abfragen ist nur möglich, wenn Sie eine Verbindung mit einem [HDInsight Interactive Query](../interactive-query/apache-interactive-query-get-started.md)-Cluster herstellen.

Sie können Data Lake Tools für Visual Studio auch verwenden, um den Inhalt eines Hive-Auftrags anzuzeigen. Data Lake Tools für Visual Studio sammelt die YARN-Protokolle bestimmter Hive-Aufträge und zeigt sie an.

Navigieren Sie im Server-Explorer zu **Azure** > **HDInsight**, und wählen Sie Ihren Cluster aus.  Das ist der Ausgangspunkt im Server-Explorer für die folgenden Abschnitte.

### <a name="view-hivesampletable"></a>Anzeigen der Beispieltabelle „hivesampletable“
Alle HDInsight-Cluster verfügen über eine Hive-Beispieltabelle mit dem Namen `hivesampletable`.  

Navigieren Sie von Ihrem Cluster zu **Hive-Datenbanken** > **Standard** > **hivesampletable**.

* So zeigen Sie das `hivesampletable`-Schema an:  
Erweitern Sie **hivesampletable**.

* So zeigen Sie `hivesampletable`-Daten an:  
Klicken Sie mit der rechten Maustaste auf **hivesampletable** und dann auf **Erste 100 Zeilen anzeigen**.  Dies entspricht dem Ausführen der folgenden Hive-Abfrage mithilfe des Hive-ODBC-Treibers:

   `SELECT * FROM hivesampletable LIMIT 100`

  Sie können die Anzahl der Zeilen anpassen.

  ![Screenshot einer HDInsight Hive Visual Studio-Schemaabfrage](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive-Abfrageergebnisse")

### <a name="create-hive-tables"></a>Erstellen von Hive-Tabellen
Zum Erstellen einer Hive-Tabelle können Sie die grafische Benutzeroberfläche oder Hive-Abfragen verwenden. Informationen zum Verwenden von Hive-Abfragen finden Sie unter [Ausführen von Apache Hive-Abfragen](#run.queries).

1. Navigieren Sie von Ihrem Cluster zu **Hive-Datenbanken** > **Standard**.

2. Klicken Sie mit der rechten Maustaste auf **Standard**, und wählen Sie dann **Tabelle erstellen** aus.

3. Konfigurieren Sie die Tabelle wie gewünscht.  

4. Klicken Sie auf **Tabelle erstellen**, um den Auftrag zum Erstellen der neuen Hive-Tabelle zu übermitteln.

    ![Screenshot des Fensters „Tabelle erstellen“ der HDInsight Visual Studio-Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "Erstellen einer Hive-Tabelle")

### <a name="run.queries"></a>Erstellen und Ausführen von Hive-Abfragen
Zum Erstellen und Ausführen von Hive-Abfragen stehen Ihnen zwei Möglichkeiten zur Auswahl:

* Erstellen von Ad-hoc-Abfragen
* Erstellen einer Hive-Anwendung

So können Sie Ad-hoc-Abfragen erstellen und ausführen

1. Klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und wählen Sie dann **Hive-Abfrage schreiben** aus.  

2. Geben Sie die folgende Hive-Abfrage ein:

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Der Hive-Editor unterstützt IntelliSense. Data Lake Tools für Visual Studio unterstützt das Laden von Remotemetadaten, wenn Sie Ihr Hive-Skript bearbeiten. Wenn Sie beispielsweise `SELECT * FROM` eingeben, listet IntelliSense alle vorgeschlagenen Tabellennamen auf. Wird ein Tabellenname angegeben, listet IntelliSense die Spaltennamen auf. Die Tools unterstützen die meisten Hive-DML-Anweisungen, Unterabfragen und integrierte UDFs.

    ![Screenshot von Beispiel 1 für HDInsight Visual Studio-Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![Screenshot von Beispiel 2 für HDInsight Visual Studio-Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense schlägt nur die Metadaten des Clusters vor, der auf der HDInsight-Symbolleiste ausgewählt ist.

3. Wählen Sie den Ausführungsmodus aus:

    * **Interactive**  

      Achten Sie darauf, dass **Interactive** (Interaktiv) ausgewählt ist, und klicken Sie dann auf **Senden**.

      ![Screenshot: Abfrage und Ausführen](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **Batch**  

      Achten Sie darauf, dass **Batch** ausgewählt ist, und klicken Sie dann auf **Senden**.  Konfigurieren Sie den **Auftragsnamen**, **Argumente**, **zusätzliche Konfigurationen** und das **Statusverzeichnis** für das Skript, wenn Sie die Option für erweitertes Senden auswählen:

      ![Screenshot: Abfrage und Batch](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![Screenshot einer HDInsight Hadoop Hive-Abfrage](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "Übermitteln von Abfragen")

      > [!NOTE]  
      > Sie können keine Batches an Interactive Query-Cluster senden.  Dazu müssen Sie den interaktiven Modus verwenden.

So erstellen Sie eine Hive-Lösung und führen Sie sie aus:

1. Navigieren Sie über die Menüleiste zu **Datei** > **Neu** > **Projekt...** .

2. Navigieren Sie im linken Bereich zu **Installiert** > **Azure Data Lake** > **HIVE (HDInsight)** .  

3. Wählen Sie im mittleren Bereich **Hive Application** (Hive-Anwendung) aus. Geben Sie die Eigenschaften ein, und klicken Sie dann auf **OK**.

    ![Screenshot eines neuen Hive-Projekts in den HDInsight Visual Studio-Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "Erstellen von Hive-Anwendungen in Visual Studio")

4. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.hql**, um das Skript zu öffnen.

### <a name="view-job-summary-and-output"></a>Anzeigen der Auftragszusammenfassung und -ausgabe

Die Auftragszusammenfassung unterscheidet sich zwischen den Modi **Batch** und **Interactive** (Interaktiv).

![Auftragszusammenfassung](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive-Auftragszusammenfassung")

Klicken Sie auf die Schaltfläche **Aktualisieren**, um den Status zu aktualisieren, bis sich der Auftragsstatus in **Abgeschlossen** ändert.  

* Wenn Sie die Auftragsdetails im **Batch**-Modus anzeigen möchten, klicken Sie unten auf die Links, um die **Auftragsabfrage**, die **Auftragsausgabe**, das **Auftragsprotokoll** oder das **YARN-Protokoll** anzuzeigen.

* Wenn Sie die Auftragsdetails im **interaktiven** Modus anzeigen möchten, klicken Sie auf die Registerkarten **Ausgabe** und **HiveServer2-Ausgabe**.

  ![Auftragsdetails](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive-Auftragsdetails")

### <a name="view-job-graph"></a>Anzeigen von Auftragsdiagrammen

Aktuell werden Auftragsdiagramme nur für Hive-Aufträge angezeigt, die Tez als Ausführungs-Engine verwenden.  Informationen zum Aktivieren von Tez finden Sie unter [Verwenden von Apache Hive in HDInsight](hdinsight-use-hive.md).  Weitere Informationen finden Sie im Abschnitt „Verwenden von Apache Tez anstelle von MapReduce“ unter [Optimieren von Apache Hive-Abfragen in Azure HDInsight](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce).  

Um alle Operatoren am Scheitelpunkt anzuzeigen, doppelklicken Sie auf die Scheitelpunkte des Auftragsdiagramms. Sie können auch auf einen bestimmten Operator zeigen, um weitere Details anzuzeigen.

Das Auftragsdiagramm wird möglicherweise auch dann nicht angezeigt, wenn Tez als Ausführung-Engine beim Start der Tez-Anwendung verwendet wird.  Dies kann daran liegen, dass der Auftrag keine DML-Anweisungen enthält, oder dass die DML-Anweisungen etwas zurückgegeben können, ohne dass eine Tez-Anwendung gestartet werden muss. Durch `SELECT * FROM table1` wird die Tez-Anwendung z.B. nicht gestartet.

![Auftragsdiagramm](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Hive-Auftragszusammenfassung")


### <a name="task-execution-detail"></a>Details zur Taskausführung

In der Auftragsdiagramm können Sie **Details zur Taskausführung** auswählen, um strukturierte und visualisierte Informationen zu Hive-Aufträgen anzeigen. Sie können auch weitere Auftragsinformationen abrufen. Im Fall von Leistungsproblemen können Sie in der Ansicht weitere Details zum Problem anzeigen. Beispielsweise können Sie Informationen zur Funktion jeder Aufgabe und ausführliche Informationen zu einzelnen Aufgaben (Lese-/Schreibzugriff auf Daten, Zeitplan, Start- und Endzeit usw.) anzeigen. Mithilfe der visualisierten Informationen können Sie Auftragskonfigurationen oder die Systemarchitektur optimieren.

![Screenshot der Aufgabenausführungsansicht von Data Lake Tools für Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "Aufgabenausführungsansicht")


### <a name="view-hive-jobs"></a>Anzeigen von Hive-Aufträgen
Sie können Auftragsabfragen, die Auftragsausgabe, Auftragsprotokolle und YARN-Protokolle für Hive-Aufträge anzeigen.

In der neuesten Version der Tools können Sie den Inhalt Ihrer Hive-Aufträge anzeigen, indem Sie YARN-Protokolle erfassen und anzeigen. Ein YARN-Protokoll kann Ihnen bei der Untersuchung von Leistungsproblemen helfen. Weitere Informationen zur Erfassung von YARN-Protokollen durch HDInsight finden Sie unter [Zugriff auf YARN-Anwendungsprotokolle unter Windows-basiertem HDInsight](../hdinsight-hadoop-access-yarn-app-logs.md).

So zeigen Sie Hive-Aufträge an:

1. Klicken Sie mit der rechten Maustaste auf einen HDInsight-Cluster, und klicken Sie dann auf **Aufträge anzeigen**. Eine Liste der für den Cluster ausgeführten Hive-Aufträge wird angezeigt.  

2. Wählen Sie einen Auftrag aus. Wählen Sie im Fenster **Hive Job Summary** (Hive-Auftragszusammenfassung) eine der folgenden Optionen aus:
    - **Job Query** (Auftragsabfrage)
    - **Auftragsausgabe**
    - **Job Log** (Auftragsprotokoll)  
    - **Yarn log** (YARN-Protokoll)

    ![Screenshot des Fensters „View Hive Jobs“ (Hive-Aufträge anzeigen) der HDInsight Visual Studio-Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "Anzeigen von Hive-Aufträgen")


## <a name="run-apache-pig-scripts"></a>Ausführen von Apache Pig-Skripts

1. Navigieren Sie über die Menüleiste zu **Datei** > **Neu** > **Projekt...** .

2. Navigieren Sie im linken Bereich zu **Installiert** > **Azure Data Lake** > **Pig (HDInsight)** .  

3. Wählen Sie im mittleren Bereich **Pig-Anwendung** aus. Geben Sie die Eigenschaften ein, und klicken Sie dann auf **OK**.

4. Doppelklicken Sie im **Projektmappen-Explorer** auf **Script.pig**, um das Skript zu öffnen.

## <a name="feedback-and-known-issues"></a>Feedback und bekannte Probleme
* Ein Problem, aufgrund dessen mit NULL-Werten gestartete Ergebnisse nicht angezeigt werden, wurde behoben. Wenden Sie sich an das Supportteam, falls die Ausführung bei Ihnen durch dieses Problem blockiert wird.
* Das von Visual Studio erstellte HQL-Skript wird abhängig von der lokalen Regionseinstellung des Benutzers codiert. Das Skript wird nicht korrekt ausgeführt, wenn Sie es als Binärdatei in einen Cluster hochladen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie das Data Lake Tools für Visual Studio-Paket verwenden, um in Visual Studio eine Verbindung mit HDInsight-Clustern herzustellen. Zudem haben Sie gelernt, wie Sie eine Hive-Abfrage ausführen. Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

* [Ausführen von Apache Hive-Abfragen mit Data Lake Tools für Visual Studio](apache-hadoop-use-hive-visual-studio.md)
* [Verwenden von Hadoop Hive in HDInsight](hdinsight-use-hive.md)
* [Erste Schritte mit Apache Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Übermitteln von Apache Hadoop-Aufträgen in HDInsight](submit-apache-hadoop-jobs-programmatically.md)
* [Analysieren von Twitter-Daten mit Apache Hadoop in HDInsight](../hdinsight-analyze-twitter-data.md)

