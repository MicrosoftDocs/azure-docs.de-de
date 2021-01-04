---
title: 'Verwenden von Apache Beeline mit Apache Hive: Azure HDInsight'
description: Erfahren Sie, wie Sie den Beeline-Client verwenden, um Hive-Abfragen mit Hadoop in HDInsight auszuführen. Beeline ist ein Dienstprogramm zum Arbeiten mit HiveServer2 über JDBC.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: 70fbbdfc5d8f1bac5fa27175ea25be1503a77594
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031894"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Verwenden des Apache Beeline-Clients mit Apache Hive

In diesem Artikel wird beschrieben, wie Sie den [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell)-Client an der Befehlszeile verwenden, um Apache Hive-Abfragen über eine SSH-Verbindung zu erstellen und auszuführen.

## <a name="background"></a>Hintergrund

Beeline ist ein Hive-Client, der auf den Hauptknoten des HDInsight-Clusters enthalten ist. Informationen zum Herstellen einer Verbindung mit dem auf Ihrem HDInsight-Cluster installierten Beeline-Client oder zum lokalen Installieren von Beeline finden Sie unter [Herstellen einer Verbindung mit oder Installieren von Apache Beeline](connect-install-beeline.md) Beeline verwendet JDBC, um eine Verbindung mit HiveServer2 herzustellen, einem Dienst, der in Ihrem HDInsight-Cluster gehostet wird. Mit Beeline können Sie auch remote über das Internet auf Hive unter HDInsight zugreifen. Die folgenden Beispiele zeigen die am häufigsten verwendeten Verbindungszeichenfolgen zum Herstellen einer Verbindung mit HDInsight aus Beeline.

## <a name="prerequisites-for-examples"></a>Erforderliche Komponenten für Beispiele

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Beachten Sie das URI-Schema für den primären Speicher Ihres Clusters. Beispiele: `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage aktiviert ist, lautet der URI `wasbs://`. Weitere Informationen finden Sie unter [Sichere Übertragung](../../storage/common/storage-require-secure-transfer.md).

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). In den meisten Schritten in diesem Dokument wird davon ausgegangen, dass Sie Beeline aus einer SSH-Sitzung für den Cluster verwenden. Sie können auch einen lokalen Beeline-Client verwenden. Die zugehörigen Schritte werden in diesem Artikel jedoch nicht behandelt.

## <a name="run-a-hive-query"></a>Ausführen einer Hive-Abfrage

Dieses Beispiel basiert auf der Verwendung des Beeline-Clients über eine SSH-Verbindung.

1. Öffnen Sie über den nachstehenden Code eine SSH-Verbindung mit dem Cluster. Ersetzen Sie `sshuser` durch den SSH-Benutzer für Ihren Cluster und `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie bei entsprechender Aufforderung das Kennwort für das SSH-Benutzerkonto ein.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Stellen Sie mithilfe des folgenden Befehls mit Ihrem Beeline-Client eine Verbindung mit HiveServer2 aus Ihrer Open SSH-Sitzung her:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-Befehle beginnen mit dem Zeichen `!`, z.B. `!help` zum Anzeigen der Hilfe. Jedoch kann `!` bei einigen Befehlen ausgelassen werden. `help` funktioniert beispielsweise auch.

    Es gibt ein `!sql`, das zum Ausführen von HiveQL-Anweisungen verwendet wird. HiveQL wird aber so häufig genutzt, dass Sie das vorangestellte `!sql` weglassen können. Die folgenden beiden Anweisungen sind gleichwertig:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    In einem neuen Cluster wird nur eine Tabelle aufgelistet: **hivesampletable**.

4. Verwenden Sie folgenden Befehl, um das Schema für „hivesampletable“ anzuzeigen:

    ```hiveql
    describe hivesampletable;
    ```

    Dieser Befehl gibt folgende Information zurück:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Diese Information beschreibt die Spalten in der Tabelle.

5. Geben Sie die folgenden Anweisungen ein, um eine Tabelle mit dem Namen **log4jLogs** zu erstellen, indem Sie die über das HDInsight-Cluster bereitgestellten Beispieldaten verwenden: (Überarbeiten Sie dies je nach Bedarf basierend auf Ihrem URI-Schema.)

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

    |-Anweisung. |BESCHREIBUNG |
    |---|---|
    |DROP TABLE|Wenn die Tabelle vorhanden ist, wird sie gelöscht.|
    |CREATE EXTERNAL TABLE|Erstellt eine **externe** Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.|
    |ROW FORMAT|Gibt an, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.|
    |STORED AS TEXTFILE LOCATION|Gibt an, wo und in welchem Dateiformat die Daten gespeichert werden.|
    |SELECT|Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Diese Abfrage gibt den Wert **3** zurück, da dieser Wert in drei Zeilen enthalten ist.|
    |INPUT__FILE__NAME LIKE '%.log'|Hive versucht, das Schema auf alle Dateien im Verzeichnis anzuwenden. In diesem Fall enthält das Verzeichnis Dateien, die dem Schema nicht entsprechen. Um überflüssige Daten in den Ergebnissen zu vermeiden, weist diese Anweisung Hive an, nur Daten aus Dateien zurückzugeben, die auf „.log“ enden.|

   > [!NOTE]  
   > Externe Tabellen sollten Sie verwenden, wenn Sie erwarten, dass die zugrunde liegenden Daten aus einer externen Quelle aktualisiert werden. Das könnte z.B. ein automatisierter Datenupload oder ein MapReduce-Vorgang sein.
   >
   > Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

    Die Ausgabe dieses Befehls ähnelt dem folgenden Text:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Beenden Sie Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Ausführen einer HiveQL-Datei

Dieses Beispiel ist eine Fortsetzung des vorherigen Beispiels. Verwenden Sie die folgenden Schritte, um eine Datei zu erstellen und sie dann mit Beeline auszuführen.

1. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen **query.hql** zu erstellen:

    ```bash
    nano query.hql
    ```

1. Verwenden Sie als Inhalt der Datei den folgenden Text. Diese Abfrage erstellt eine neue „interne“ Tabelle mit dem Namen **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

    |-Anweisung. |BESCHREIBUNG |
    |---|---|
    |CREATE TABLE IF NOT EXISTS|Wenn die Tabelle noch nicht vorhanden ist, wird sie erstellt. Da das **EXTERNAL**-Schlüsselwort nicht verwendet wird, erstellt diese Anweisung eine interne Tabelle. Interne Tabellen werden im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet.|
    |STORED AS ORC|Speichert die Daten im ORC-Format (Optimized Row Columnar). ORC ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.|
    |INSERT OVERWRITE ... SELECT|Wählt die Zeilen aus der Tabelle **log4jLogs** aus, die den Wert **[ERROR]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.|

    > [!NOTE]  
    > Anders als bei externen Tabellen werden beim Löschen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

1. Verwenden Sie **STRG**+**X**, um die Datei zu speichern. Geben Sie dann **Y** ein, und drücken Sie die **EINGABETASTE**.

1. Verwenden Sie Folgendes, um die Datei mit Beeline auszuführen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Der `-i`-Parameter startet Beeline und führt die Anweisungen in der Datei `query.hql` aus. Nach Abschluss der Abfrage wird die Eingabeaufforderung `jdbc:hive2://headnodehost:10001/>` angezeigt. Sie können eine Datei auch mit dem `-f`-Parameter ausführen, der Beeline beendet, nachdem die Abfrage abgeschlossen ist.

1. Um zu überprüfen, ob die Tabelle **errorLogs** erstellt wurde, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte „t4“ den Wert **[FEHLER]** enthalten:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Nächste Schritte

* Allgemeinere Informationen zu Hive in HDInsight finden Sie unter [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md).

* Weitere Informationen zu anderen Methoden zur Verwendung von Hadoop in HDInsight finden Sie unter [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md).
