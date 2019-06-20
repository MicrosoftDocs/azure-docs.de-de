---
title: 'Verwenden von Apache Beeline mit Apache Hive: Azure HDInsight'
description: Erfahren Sie, wie Sie den Beeline-Client verwenden, um Hive-Abfragen mit Hadoop in HDInsight auszuführen. Beeline ist ein Dienstprogramm zum Arbeiten mit HiveServer2 über JDBC.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: hrasheed
ms.openlocfilehash: dcfcd4b55f848e1725e286e6ef2a87a2c36e5a71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64684930"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Verwenden des Apache Beeline-Clients mit Apache Hive

Hier erfahren Sie, wie Sie [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) verwenden, um Hive-Abfragen unter HDInsight auszuführen.

Beeline ist ein Hive-Client, der auf den Hauptknoten des HDInsight-Clusters enthalten ist. Beeline verwendet JDBC, um eine Verbindung mit HiveServer2 herzustellen, einem Dienst, der in Ihrem HDInsight-Cluster gehostet wird. Mit Beeline können Sie auch remote über das Internet auf Hive unter HDInsight zugreifen. Die folgenden Beispiele zeigen die am häufigsten verwendeten Verbindungszeichenfolgen zum Herstellen einer Verbindung mit HDInsight aus Beeline:

## <a name="types-of-connections"></a>Arten von Verbindungen

### <a name="from-an-ssh-session"></a>Über eine SSH-Sitzung

Beim Herstellen einer Verbindung aus einer SSH-Sitzung mit einem Clusterhauptknoten können Sie anschließend eine Verbindung mit der `headnodehost`-Adresse an Port `10001` herstellen:

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

---

### <a name="over-an-azure-virtual-network"></a>Über ein virtuelles Azure-Netzwerk

Beim Herstellen einer Verbindung von einem Client zu HDInsight über ein virtuelles Azure-Netzwerk müssen Sie den vollqualifizierten Domänennamen (FQDN) eines Clusterhauptknotens angeben. Da diese Verbindung direkt mit den Clusterknoten hergestellt wird, wird für die Verbindung Port `10001` verwendet:

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Ersetzen Sie `<headnode-FQDN>` durch den vollqualifizierten Domänennamen eines Clusterhauptknotens. Verwenden Sie die Informationen im Dokument [Verwalten von HDInsight mithilfe der Apache Ambari-REST-API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes), um den vollqualifizierten Domänennamen eines Hauptknotens zu ermitteln.

---

### <a name="to-hdinsight-enterprise-security-package-esp-cluster"></a>An den Cluster des HDInsight-Enterprise-Sicherheitspakets (ESP)

Beim Herstellen einer Verbindung vom Client zu einem mit Azure Active Directory (AAD) verknüpften ESP-Cluster (Enterprise-Sicherheitspaket) müssen Sie auch den Domänennamen `<AAD-Domain>` und den Namen eines Domänenbenutzerkontos mit der Berechtigung für den Zugriff auf den Cluster `<username>` angeben:

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

Ersetzen Sie `<username>` durch den Namen eines Kontos in der Domäne mit der Berechtigung für den Zugriff auf den Cluster. Ersetzen Sie `<AAD-DOMAIN>` durch den Namen der Azure Active Directory-Instanz (AAD), der der Cluster angehört. Verwenden Sie eine Zeichenfolge aus Großbuchstaben für den Wert `<AAD-DOMAIN>`, da die Anmeldeinformationen sonst nicht gefunden werden. Überprüfen Sie `/etc/krb5.conf` bei Bedarf auf die Bereichsnamen.

---

### <a name="over-public-internet"></a>Über das öffentliche Internet

Wenn Sie eine Verbindung über das öffentliche Internet herstellen möchten, müssen Sie den Kontonamen für die Clusteranmeldung (Standard ist `admin`) und das Kennwort angeben. Beispiel: Verwenden von Beeline von einem Clientsystem zum Herstellen einer Verbindung mit der `<clustername>.azurehdinsight.net`-Adresse. Diese Verbindung erfolgt über Port `443` und wird mithilfe von SSL verschlüsselt:

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p password
```

Ersetzen Sie `clustername` durch den Namen Ihres HDInsight-Clusters. Ersetzen Sie `admin` durch das Anmeldekonto für Ihren Cluster. Ersetzen Sie `password` durch das Kennwort des Anmeldekontos für den Cluster.

---

### <a id="sparksql"></a>Verwenden von Beeline mit Apache Spark

Apache Spark stellt eine eigene Implementierung von HiveServer2 bereit, die manchmal als Spark Thrift-Server bezeichnet wird. Bei diesem Dienst wird Spark SQL anstelle von Hive zum Auflösen von Abfragen verwendet und ermöglicht je nach Abfrage ggf. eine bessere Leistung.

#### <a name="over-public-internet-with-apache-spark"></a>Über das öffentliche Internet mit Apache Spark

Die Verbindungszeichenfolge, die beim Herstellen einer Verbindung über das Internet verwendet wird, weicht geringfügig ab. Sie enthält `httpPath/sparkhive2` anstelle von `httpPath=/hive2`:

```bash 
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p password
```

---

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>Vom Clusterhauptknoten oder in Azure Virtual Network mit Apache Spark

Wenn Sie direkt vom Clusterhauptknoten oder von einer Ressource, die sich in der gleichen Azure Virtual Network-Instanz wie der HDInsight-Cluster befindet, eine Verbindung herstellen, muss für den Spark Thrift-Server Port `10002` anstelle von Port `10001` verwendet werden. Das folgende Beispiel zeigt, wie eine direkte Verbindung mit dem Hauptknoten hergestellt wird:

```bash
beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

---

## <a id="prereq"></a>Voraussetzungen

* Ein Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Beachten Sie das [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) für den primären Speicher Ihres Clusters. Beispiele: `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder `adl://` für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage oder Data Lake Storage Gen2 aktiviert ist, lautet der URI `wasbs://` bzw. `abfss://`. Weitere Informationen finden Sie unter [Sichere Übertragung](../../storage/common/storage-require-secure-transfer.md).


* Option 1: Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). In den meisten Schritten in diesem Dokument wird davon ausgegangen, dass Sie Beeline aus einer SSH-Sitzung für den Cluster verwenden.

* Option 2:  Ein lokaler Beeline-Client.


## <a id="beeline"></a>Ausführen einer Hive-Abfrage

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

    Diese Information beschreibt die Spalten in der Tabelle.

5. Geben Sie die folgenden Anweisungen ein, um eine Tabelle mit dem Namen **log4jLogs** zu erstellen, indem Sie die über das HDInsight-Cluster bereitgestellten Beispieldaten verwenden: (Überarbeiten Sie dies je nach Bedarf basierend auf Ihrem [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme).)

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

    * `DROP TABLE`: Wenn die Tabelle vorhanden ist, wird sie gelöscht.

    * `CREATE EXTERNAL TABLE`: Erstellt eine **externe** Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.

    * `ROW FORMAT`: Gibt an, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.

    * `STORED AS TEXTFILE LOCATION`: Wo die Daten gespeichert werden und in welchem Dateiformat.

    * `SELECT`: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Diese Abfrage gibt den Wert **3** zurück, da dieser Wert in drei Zeilen enthalten ist.

    * `INPUT__FILE__NAME LIKE '%.log'`: Hive versucht, das Schema auf alle Dateien im Verzeichnis anzuwenden. In diesem Fall enthält das Verzeichnis Dateien, die dem Schema nicht entsprechen. Um überflüssige Daten in den Ergebnissen zu vermeiden, weist diese Anweisung Hive an, nur Daten aus Dateien zurückzugeben, die auf „.log“ enden.

   > [!NOTE]  
   > Externe Tabellen sollten Sie verwenden, wenn Sie erwarten, dass die zugrunde liegenden Daten aus einer externen Quelle aktualisiert werden. Das könnte z.B. ein automatisierter Datenupload oder ein MapReduce-Vorgang sein.
   >
   > Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

    Die Ausgabe dieses Befehls ähnelt dem folgenden Text:

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

6. Verwenden Sie `!exit`, um Beeline zu beenden.

## <a id="file"></a>Ausführen einer HiveQL-Datei

Dies ist eine Fortsetzung des vorherigen Beispiels. Verwenden Sie die folgenden Schritte, um eine Datei zu erstellen und sie dann mit Beeline auszuführen.

1. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen **query.hql**zu erstellen:

    ```bash
    nano query.hql
    ```

2. Verwenden Sie als Inhalt der Datei den folgenden Text. Diese Abfrage erstellt eine neue „interne“ Tabelle mit dem Namen **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Diese Anweisungen führen die folgenden Aktionen aus:

   * **CREATE TABLE IF NOT EXISTS**: Wenn die Tabelle noch nicht vorhanden ist, wird sie erstellt. Da das **EXTERNAL**-Schlüsselwort nicht verwendet wird, erstellt diese Anweisung eine interne Tabelle. Interne Tabellen werden im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet.
   * **ALS ORC GESPEICHERT** – Speichert die Daten im ORC-Format (Optimized Row Columnar). ORC ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
   * **ÜBERSCHREIBEN EINFÜGEN ... SELECT**: Wählt die Zeilen aus der Tabelle **log4jLogs** aus, die den Wert **[ERROR]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.

    > [!NOTE]  
    > Anders als bei externen Tabellen werden beim Löschen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

3. Verwenden Sie **STRG**+ **_X**, um die Datei zu speichern. Geben Sie dann **Y** ein, und drücken Sie die **EINGABETASTE**.

4. Verwenden Sie Folgendes, um die Datei mit Beeline auszuführen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > Der `-i`-Parameter startet Beeline und führt die Anweisungen in der Datei `query.hql` aus. Nach Abschluss der Abfrage wird die Eingabeaufforderung `jdbc:hive2://headnodehost:10001/>` angezeigt. Sie können eine Datei auch mit dem `-f`-Parameter ausführen, der Beeline beendet, nachdem die Abfrage abgeschlossen ist.

5. Um zu überprüfen, ob die Tabelle **errorLogs** erstellt wurde, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Es sollten drei Datenzeilen zurückgegeben werden, die alle in Spalte „t4“ den Wert **[FEHLER]** enthalten:

        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
        | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
        +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
        3 rows selected (1.538 seconds)




## <a id="summary"></a><a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight finden Sie im folgenden Artikel:

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)

Weitere Informationen zu anderen Methoden zur Verwendung von Hadoop in HDInsight finden Sie in den folgenden Artikeln:

* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/download.html


[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx
