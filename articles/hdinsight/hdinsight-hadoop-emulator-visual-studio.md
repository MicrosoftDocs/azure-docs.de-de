---
title: Data Lake Tools für Visual Studio mit Hortonworks Sandbox – Azure HDInsight
description: Es wird beschrieben, wie Sie die Azure Data Lake-Tools für Visual Studio mit der Hortonworks Sandbox verwenden (Ausführung auf einer lokalen VM). Mit diesen Tools können Sie Hive- und Pig-Aufträge in der Sandbox erstellen und ausführen und die Auftragsausgabe und den Verlauf anzeigen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: hrasheed
ms.openlocfilehash: 3286ca3b9c85236ff322eb19324bc5ac7a904e22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65605454"
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>Verwenden der Azure Data Lake-Tools für Visual Studio mit der Hortonworks Sandbox

Azure Data Lake umfasst Tools für die Verwendung von generischen Apache Hadoop-Clustern. Dieses Dokument enthält die erforderlichen Schritte zum Verwenden der Data Lake-Tools mit der Hortonworks Sandbox auf einem lokalen virtuellen Computer.

Mit der Hortonworks Sandbox können Sie Hadoop lokal in Ihrer Entwicklungsumgebung verwenden. Nachdem Sie eine Lösung entwickelt haben und diese bedarfsorientiert bereitstellen möchten, können Sie dann die Umstellung auf einen HDInsight-Cluster durchführen.

## <a name="prerequisites"></a>Voraussetzungen

* Hortonworks Sandbox, die auf einem virtuellen Computer in Ihrer Entwicklungsumgebung ausgeführt wird. Dieses Dokument wurde mit Sandbox in Oracle VirtualBox geschrieben und getestet. Das Dokument [Erste Schritte mit Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md) enthält Informationen zum Einrichten von Sandbox.

* Visual Studio.

* [Azure SDK für .NET](https://azure.microsoft.com/downloads/) 2.7.1 oder höher

* Die [Azure Data Lake-Tools für Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)

## <a name="configure-passwords-for-the-sandbox"></a>Konfigurieren von Kennwörtern für die Sandbox

Stellen Sie sicher, dass Hortonworks Sandbox ausgeführt wird. Führen Sie dann die Schritte im Dokument [Erste Schritte mit Hortonworks Sandbox](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords) aus. Mit diesen Schritten konfigurieren Sie das Kennwort für das SSH-`root`-Konto und das Apache Ambari-`admin`-Konto. Diese Kennwörter werden verwendet, wenn Sie aus Visual Studio eine Verbindung mit der Sandbox herstellen.

## <a name="connect-the-tools-to-the-sandbox"></a>Verbinden der Tools mit der Sandbox

1. Öffnen Sie Visual Studio, klicken Sie auf **Ansicht**, und dann auf **Server-Explorer**.

2. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Eintrag **HDInsight**, und wählen Sie dann die Option **Verbinden mit HDInsight Emulator**.

    ![Screenshot des Server-Explorer mit hervorgehobenem „Mit HDInsight-Emulator verbinden“](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. Geben Sie im Dialogfeld **Mit HDInsight-Emulator verbinden** das Kennwort ein, das Sie für Ambari konfiguriert haben.

    ![Screenshot des Dialogfelds mit hervorgehobenen Kennworttextfeld](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    Klicken Sie auf **Weiter**, um fortzufahren.

4. Verwenden Sie das Feld **Kennwort**, um das Kennwort einzugeben, das Sie für das `root`-Konto konfiguriert haben. Behalten Sie für die anderen Felder den Standardwert bei.

    ![Screenshot des Dialogfelds mit hervorgehobenen Kennworttextfeld](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    Klicken Sie auf **Weiter**, um fortzufahren.

5. Warten Sie, bis die Überprüfung der Dienste abgeschlossen ist. Es kann vorkommen, dass die Überprüfung nicht erfolgreich ist und Sie eine Aufforderung zum Aktualisieren der Konfiguration erhalten. Wenn die Überprüfung nicht erfolgreich ist, klicken Sie auf **Aktualisieren**, und warten Sie, bis die Konfiguration und Verifizierung für den Dienst abgeschlossen ist.

    ![Screenshot des Dialogfelds mit der hervorgehobenen Schaltfläche „Aktualisieren“](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]  
    > Während des Aktualisierungsprozess wird Ambari verwendet, um die Hortonworks Sandbox-Konfiguration so zu ändern, dass die Erwartungen der Data Lake-Tools für Visual Studio erfüllt werden.

6. Wählen Sie nach Abschluss der Überprüfung die Option **Fertig stellen**, um die Konfiguration abzuschließen.
    ![Screenshot des Dialogfelds mit der hervorgehobenen Schaltfläche „Fertig stellen“](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]  
     > Je nach Geschwindigkeit Ihrer Entwicklungsumgebung und der Arbeitsspeichermenge, die dem virtuellen Computer zugewiesen wurde, kann das Konfigurieren und Überprüfen der Dienste mehrere Minuten dauern.

Nach dem Ausführen dieser Schritte enthält der Server-Explorer im Abschnitt **HDInsight** jetzt den Eintrag **HDInsight local cluster** (Lokaler HDInsight-Cluster).

## <a name="write-an-apache-hive-query"></a>Schreiben einer Apache Hive-Abfrage

Hive stellt eine SQL-ähnliche Abfragesprache (HiveQL) für die Arbeit mit strukturierten Daten bereit. Führen Sie die folgenden Schritte aus, um zu erfahren, wie Sie bedarfsgesteuerte Abfragen für den lokalen Cluster ausführen.

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Eintrag für den lokalen Cluster, den Sie zuvor hinzugefügt haben, und klicken Sie dann auf die Option **Hive-Abfrage schreiben**.

    ![Screenshot des Server-Explorers mit hervorgehobenem „Hive-Abfrage stellen“](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    Ein neues Abfragefenster wird angezeigt. Hier können Sie schnell eine Abfrage schreiben und an den lokalen Cluster senden.

2. Geben Sie im neuen Abfragefenster den folgenden Befehl ein:

        select count(*) from sample_08;

    Wählen Sie zum Ausführen der Abfrage oben im Fenster die Option **Übermitteln** aus. Behalten Sie für die anderen Werte (**Batch** und Servername) die Standardwerte bei.

    ![Screenshot des Abfragefenster mit der hervorgehobenen Schaltfläche „Absenden“](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    Sie können auch das Dropdownmenü neben **Senden** verwenden, um **Erweitert** auszuwählen. Mit den erweiterten Optionen können Sie beim Senden des Auftrags weitere Optionen angeben.

    ![Screenshot des Dialogfelds „Skriptaktion übermitteln“](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. Nachdem Sie die Abfrage übermittelt haben, wird der Auftragsstatus angezeigt. Der Auftragsstatus zeigt Informationen zum Auftrag an, während er von Hadoop verarbeitet wird. Der **Auftragsstatus** zeigt den Status des Auftrags an. Der Status wird in regelmäßigen Abständen aktualisiert, oder Sie können das Aktualisierungssymbol verwenden, um den Status manuell zu aktualisieren.

    ![Screenshot des Dialogfelds „Auftragsansicht“ mit hervorgehobenem „Auftragsstatus“](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    Nachdem der **Auftragsstatus** in **Beendet** geändert wurde, wird ein gerichteter azyklischer Graph (Directed Acyclic Graph, DAG) angezeigt. Dieses Diagramm beschreibt den Ausführungspfad, der von Tez beim Verarbeiten der Hive-Abfrage bestimmt wurde. Tez ist die Standard-Ausführungs-Engine für Hive auf dem lokalen Cluster.

    > [!NOTE]  
    > Apache Tez ist auch die Standardeinstellung, wenn Sie Linux-basierte HDInsight-Cluster verwenden. Es ist nicht die Standardeinstellung auf Windows-basiertem HDInsight. Um es zu verwenden, müssen Sie die Zeile `set hive.execution.engine = tez;` auf Anfang der Hive-Abfrage einfügen.

    Verwenden Sie den Link **Auftragsausgabe**, um die Ausgabe anzuzeigen. In diesem Fall ist dies 823, also die Anzahl von Zeilen in der Tabelle „sample_08“. Sie können Diagnoseinformationen zum Auftrag anzeigen, indem Sie die Links **Auftragsprotokoll** und **YARN-Protokoll herunterladen** verwenden.

4. Sie können Hive-Aufträge auch interaktiv ausführen, indem Sie das Feld **Batch** in **Interaktiv** ändern. Klicken Sie auf **Ausführen**.

    ![Screenshot der hervorgehobenen Schaltflächen „Interaktiv“ und „Ausführen“](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    Das Ausgabeprotokoll, das während der Verarbeitung generiert wurde, wird von einer interaktiven Abfrage an das Fenster **HiveServer2-Ausgabe** gestreamt.

    > [!NOTE]  
    > Dies sind die gleichen Informationen, die über den Link **Auftragsprotokoll** zur Verfügung stehen, nachdem ein Auftrag abgeschlossen wurde.

    ![Screenshot des Ausgabeprotokolls](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>Erstellen eines Hive-Projekts

Sie können auch ein Projekt erstellen, das mehrere Hive-Skripts enthält. Verwenden Sie ein Projekt, wenn Sie über verwandte Skripts verfügen oder Skripts in einem Versionskontrollsystem verwalten möchten.

1. Wählen Sie in Visual Studio die Option **Datei**, **Neu** und dann **Projekt**.

2. Erweitern Sie in der Liste mit den Projekten die Option **Vorlagen**, erweitern Sie **Azure Data Lake**, und wählen Sie dann **HIVE (HDInsight)** . Wählen Sie in der Liste mit den Vorlagen die Option **Hive-Beispiel** aus. Geben Sie einen Namen und einen Speicherort ein, und klicken Sie anschließend auf **OK**.

    ![Screenshot des Fensters „Neues Projekt“ mit hervorgehobenem Azure Data Lake, HIVE, Hive-Beispiel und OK](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

Das Projekt **Hive-Beispiel** enthält zwei Skripts: **WebLogAnalysis.hql** und **SensorDataAnalysis.hql**. Sie können diese Skripts senden, indem Sie oben im Fenster jeweils die Schaltfläche **Übermitteln** verwenden.

## <a name="create-an-apache-pig-project"></a>Erstellen eines Apache Pig-Projekts

Während Hive eine SQL-ähnliche Sprache zum Arbeiten mit strukturierten Daten bereitstellt, funktioniert Pig, indem Datentransformationen durchgeführt werden. Pig stellt eine Sprache bereit (Pig Latin), mit der Sie eine Pipeline von Transformationen entwickeln können. Um Pig mit dem lokalen Clustern zu verwenden, gehen Sie folgendermaßen vor:

1. Öffnen Sie Visual Studio, und wählen Sie **Datei**, **Neu** und dann **Projekt** aus. Erweitern Sie in der Liste mit den Projekten die Option **Vorlagen**, erweitern Sie **Azure Data Lake**, und wählen Sie **Pig (HDInsight)** aus. Wählen Sie in der Liste der Vorlagen die Option **Pig-Anwendung** aus. Geben Sie einen Namen und einen Speicherort ein, und klicken Sie auf **OK**.

    ![Screenshot des neuen Projekt angezeigt, mit hervorgehobenem Azure Data Lake, Pig, Pig-Anwendung und OK](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. Geben Sie folgenden Text als Inhalt der Datei **script.pig** ein, die mit diesem Projekt erstellt wurde.

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    Für Pig wird zwar eine andere Sprache als für Hive verwendet, aber die Ausführung über die Schaltfläche **Übermitteln** ist für beide Sprachen gleich. Wenn Sie die Dropdownliste neben **Übermitteln** auswählen, wird ein erweitertes Sendedialogfeld für Pig angezeigt.

    ![Screenshot des Dialogfelds „Skriptaktion übermitteln“](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. Der Auftragsstatus und die Ausgabe werden genauso wie für eine Hive-Abfrage angezeigt.

    ![Screenshot eines abgeschlossenen Pig-Auftrags](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>Anzeigen von Aufträgen

Mit Data Lake-Tools können Sie leicht Informationen zu Aufträgen anzeigen, die mit Hadoop ausgeführt wurden. Verwenden Sie die folgenden Schritte zum Anzeigen der Aufträge, die im lokalen Cluster ausgeführt wurden.

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den lokalen Cluster, und wählen Sie **Aufträge anzeigen**. Eine Liste mit den Aufträgen wird angezeigt, die an den Cluster übermittelt wurden.

    ![Screenshot des Server-Explorer mit hervorgehobenem „Anzeigen von Aufträgen“](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. Wählen Sie in der Liste mit den Aufträgen einen Auftrag aus, um die Auftragsdetails anzuzeigen.

    ![Screenshot des Auftragsbrowsers mit einem hervorgehobenen Auftrag](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    Die angezeigten Informationen ähneln der Anzeige nach dem Ausführen einer Hive- oder Pig-Abfrage und enthalten Links zum Anzeigen der Ausgabe und der Protokollinformationen.

3. Hier können Sie den Auftrag auch ändern und erneut senden.

## <a name="view-hive-databases"></a>Anzeigen von Hive-Datenbanken

1. Erweitern Sie im **Server-Explorer** den Eintrag **Lokaler HDInsight-Cluster**, und erweitern Sie dann die Option **Hive-Datenbanken**. Die Datenbanken **Standard** und **xademo** werden im lokalen Cluster sichtbar. Wenn Sie eine Datenbank erweitern, werden die in der Datenbank enthaltenen Tabellen angezeigt.

    ![Screenshot des Server-Explorer mit erweiterten Datenbanken](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. Beim Erweitern einer Tabelle werden die Spalten der Tabelle angezeigt. Sie können mit der rechten Maustaste auf eine Tabelle klicken und die Option **Top 100 Zeilen anzeigen** wählen, um die Daten schnell anzuzeigen.

    ![Screenshot des Server-Explorer mit einer erweiterten Tabelle und ausgewähltem „Top 100 Zeilen anzeigen“](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>Datenbank- und Tabelleneigenschaften

Sie können die Eigenschaften einer Datenbank oder Tabelle anzeigen. Die Auswahl von **Eigenschaften** zeigt Details für das ausgewählte Element im Fenster „Eigenschaften“ an. Sehen Sie sich beispielsweise die Informationen im folgenden Screenshot an:

![Screenshot des Eigenschaftenfensters](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>Erstellen einer Tabelle

Klicken Sie zum Erstellen einer Tabelle mit der rechten Maustaste auf eine Datenbank, und klicken Sie dann auf **Tabelle erstellen**.

![Screenshot des Server-Explorer mit hervorgehobenem „Tabelle erstellen“](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

Sie können die Tabelle dann mit einem Formular erstellen. Am unteren Rand des folgenden Screenshots können Sie unformatierten HiveQL-Code sehen, der zum Erstellen der Tabelle verwendet wird.

![Screenshot des Formulars, das zum Erstellen einer Tabelle verwendet wird](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Hortonworks Sandbox](https://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Apache Hadoop-Tutorial – Erste Schritte mit HDP](https://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
