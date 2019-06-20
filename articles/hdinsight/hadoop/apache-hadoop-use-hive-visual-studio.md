---
title: Tools für Apache Hive mit Data Lake (Apache Hadoop) für Visual Studio – Azure HDInsight
description: Erfahren Sie, wie Sie die Data Lake Tools für Visual Studio verwenden, um Apache Hive-Abfragen mit Apache Hadoop in Azure HDInsight auszuführen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: hrasheed
ms.openlocfilehash: 7480dafe435e555bfba81ebd9242bb5724c0bf3f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65861597"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Ausführen von Apache Hive-Abfragen mit Data Lake-Tools für Visual Studio

Erfahren Sie, wie Sie die Data Lake Tools für Visual Studio für Abfragen in Apache Hive verwenden. Mithilfe der Data Lake Tools können Sie Hive-Abfragen an Apache Hadoop in Azure HDInsight ganz einfach erstellen, übermitteln und überwachen.

## <a id="prereq"></a>Voraussetzungen

* Ein Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Visual Studio (eine der folgenden Versionen):

    * Visual Studio 2015, 2017 (beliebige Edition)
    * Visual Studio 2013 Community/Professional/Premium/Ultimate mit Update 4

* HDInsight-Tools für Visual Studio oder Azure Data Lake-Tools für Visual Studio. Informationen zum Installieren und Konfigurieren der Tools finden Sie unter [Erste Schritte bei der Verwendung von Hadoop-Tools für Visual Studio für HDInsight](apache-hadoop-visual-studio-tools-get-started.md) .

## <a id="run"></a> Ausführen von Apache Hive-Abfragen mit Visual Studio

Zum Erstellen und Ausführen von Hive-Abfragen stehen Ihnen zwei Möglichkeiten zur Auswahl:

* Erstellen von Ad-hoc-Abfragen
* Erstellen einer Hive-Anwendung

### <a name="ad-hoc"></a>Ad-hoc

Ad-hoc-Abfragen können entweder im Modus **Batch** oder **Interaktiv** ausgeführt werden.

1. Öffnen Sie **Visual Studio**.

2. Navigieren Sie im **Server-Explorer** zu **Azure** > **HDInsight**.

3. Erweitern Sie **HDInsight**, und klicken Sie mit der rechten Maustaste auf den Cluster, in dem Sie die Abfrage ausführen möchten, und wählen Sie dann **Write a Hive Query** (Hive-Abfrage schreiben) aus.

4. Geben Sie die folgende Hive-Abfrage ein:

    ```hql
    SELECT * FROM hivesampletable;
    ```

5. Wählen Sie **Execute**(Ausführen). Beachten Sie, dass der Ausführungsmodus standardmäßig auf **Interaktiv** festgelegt ist.

    ![Screenshot: Ausführen interaktiver Hive-Abfragen](./media/apache-hadoop-use-hive-visual-studio/vs-execute-hive-query.png)

6. Um dieselbe Abfrage im Modus **Batch** auszuführen, wechseln Sie in der Dropdownliste von **Interaktiv** zu **Batch**. Beachten Sie, dass die Schaltfläche für die Ausführung von **Execute** (Ausführen) zu **Submit** (Übermitteln) wechselt.

    ![Screenshot: Übermitteln einer Hive-Abfrage](./media/apache-hadoop-use-hive-visual-studio/vs-batch-query.png)

    Der Hive-Editor unterstützt IntelliSense. Data Lake Tools für Visual Studio unterstützt das Laden von Remotemetadaten, wenn Sie Ihr Hive-Skript bearbeiten. Wenn Sie beispielsweise `SELECT * FROM` eingeben, listet IntelliSense alle vorgeschlagenen Tabellennamen auf. Wird ein Tabellenname angegeben, listet IntelliSense die Spaltennamen auf. Die Tools unterstützen die meisten Hive-DML-Anweisungen, Unterabfragen und integrierte UDFs. IntelliSense schlägt nur die Metadaten des Clusters vor, der auf der HDInsight-Symbolleiste ausgewählt ist.

    ![Screenshot von Beispiel 1 für HDInsight Visual Studio-Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-table-name.png "U-SQL IntelliSense")
   
    ![Screenshot von Beispiel 2 für HDInsight Visual Studio-Tools](./media/apache-hadoop-use-hive-visual-studio/vs-intellisense-column-name.png "U-SQL IntelliSense")

7. Klicken Sie auf **Submit** (Senden) oder **Submit (Advanced)** (Erweitertes Senden).

   Wenn Sie die Option für erweitertes Senden auswählen, konfigurieren Sie den **Auftragsnamen**, **Argumente**, **zusätzliche Konfigurationen** und das **Statusverzeichnis** für das Skript:

    ![Screenshot einer HDInsight Hadoop Hive-Abfrage](./media/apache-hadoop-use-hive-visual-studio/hdinsight.visual.studio.tools.submit.jobs.advanced.png "Übermitteln von Abfragen")

### <a name="hive-application"></a>Hive-Anwendung

1. Öffnen Sie **Visual Studio**.

2. Navigieren Sie über die Menüleiste zu **Datei** > **Neu** > **Projekt**.

3. Navigieren Sie im Fenster **Neues Projekt** zu **Vorlagen** > **Azure Data Lake** > **HIVE (HDInsight)**  > **Hive-Anwendung**. 

4. Geben Sie einen Namen für dieses Projekt an, und wählen Sie dann **OK** aus.

5. Öffnen Sie die Datei **Script.hql** , die mit diesem Projekt erstellt wird, und fügen Sie dann die folgenden HiveQL-Anweisungen ein:

    ```hiveql
    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

   * `DROP TABLE`: Wenn die Tabelle vorhanden ist, wird sie von dieser Anweisung gelöscht.

   * `CREATE EXTERNAL TABLE`: Erstellt eine neue „externe“ Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort.

     > [!NOTE]  
     > Externe Tabellen sollten Sie verwenden, wenn Sie erwarten, dass die zugrunde liegenden Daten aus einer externen Quelle aktualisiert werden. Beispielsweise einen MapReduce-Auftrag oder Azure-Dienst.
     >
     > Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

   * `ROW FORMAT`: Teilt Hive mit, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.

   * `STORED AS TEXTFILE LOCATION`: Teilt Hive mit, dass die Daten im Verzeichnis „example/data“ als Text gespeichert sind.

   * `SELECT`: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte `t4` den Wert `[ERROR]` enthält. Mit dieser Anweisung sollte der Wert `3` zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.

   * `INPUT__FILE__NAME LIKE '%.log'`: Teilt Hive mit, dass nur Daten aus Dateien mit der Erweiterung „.log“ zurückgeben werden sollen. Diese Klausel schränkt die Suche auf die Datei „sample.log“ ein, die die Daten enthält.

6. Wählen Sie auf der Symbolleiste den **HDInsight-Cluster**, den Sie für diese Abfrage verwenden möchten. Wählen Sie **Übermitteln**, um die Anweisungen als Hive-Auftrag auszuführen.

   ![Leiste „Übermitteln“](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

7. Die **Hive-Auftragszusammenfassung** wird mit Informationen zum aktiven Auftrag angezeigt. Verwenden Sie den Link **Aktualisieren**, um die Auftragsinformationen zu aktualisieren, bis der **Auftragsstatus** zu **Abgeschlossen** wechselt.

   ![Auftragszusammenfassung, die einen abgeschlossenen Auftrag anzeigt](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

8. Verwenden Sie den Link **Auftragsausgabe** , um die Ausgabe dieses Auftrags anzuzeigen. `[ERROR] 3` wird angezeigt – dies ist der Wert, der von dieser Abfrage zurückgegeben wird.

### <a name="additional-example"></a>Zusätzliches Beispiel

Dieses Beispiel basiert auf der im vorherigen Schritt erstellten Tabelle `log4jLogs`.

1. Klicken Sie in **Server-Explorer** mit der rechten Maustaste auf Ihren Cluster, und wählen Sie **Hive-Abfrage schreiben** aus.

2. Geben Sie die folgende Hive-Abfrage ein:

    ```hql
    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

    * `CREATE TABLE IF NOT EXISTS`: Erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das `EXTERNAL`-Schlüsselwort nicht verwendet wird, erstellt diese Anweisung eine interne Tabelle. Interne Tabellen werden im Hive-Data Warehouse gespeichert und von Hive verwaltet.
    
    > [!NOTE]  
    > Anders als bei `EXTERNAL` Tabellen werden beim Löschen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

    * `STORED AS ORC`: Speichert die Daten im ORC-Format (Optimized Row Columnar). ORC ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
    
    * `INSERT OVERWRITE ... SELECT`: Wählt die Zeilen aus der `log4jLogs` Tabelle, die `[ERROR]` enthalten, und fügt dann die Daten in die `errorLogs` Tabelle ein.

3. Führen Sie die Abfrage im **Batch**-Modus aus.

4. Verwenden Sie den **Server-Explorer**, und erweitern Sie **Azure** > **HDInsight** > Ihren HDInsight-Cluster > **Hive-Datenbanken** > **Standard**, um zu überprüfen, ob der Auftrag die Tabelle erstellt hat. Die Tabellen **errorLogs** und **log4jLogs** werden aufgelistet.

## <a id="nextsteps"></a>Nächste Schritte

Wie Sie sehen, können Sie mit den HDInsight-Tools für Visual Studio mühelos mit Hive-Abfragen in HDInsight arbeiten.

Allgemeine Informationen zu Hive in HDInsight:

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](hdinsight-use-pig.md)

* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Weitere Informationen zu den HDInsight Tools für Visual Studio:

* [Erste Schritte mit HDInsight Tools für Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)