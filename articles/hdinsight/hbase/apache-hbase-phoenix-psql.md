---
title: Massenladen in Apache Phoenix mit psql – Azure HDInsight
description: Es wird beschrieben, wie Sie das Tool psql verwenden, um das Massenladen von Daten in Phoenix-Tabellen durchzuführen.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: fe6705dc3dedd521357f924dd433c7eacf88ba84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64718632"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Massenladen von Daten in Apache Phoenix mithilfe von psql

[Apache Phoenix](https://phoenix.apache.org/) ist eine relationale Open-Source-Datenbank mit hochgradig parallelisierter Verarbeitung, die auf [Apache HBase](../hbase/apache-hbase-overview.md) basiert. Bei Phoenix werden SQL-ähnliche Abfragen über HBase bereitgestellt. Phoenix nutzt JDBC-Treiber, um Benutzern das Erstellen, Löschen und Ändern von SQL-Tabellen, -Indizes, -Sichten und -Sequenzen sowie das Durchführen des Upsert-Vorgangs für Zeilen einzeln oder als Massenvorgang zu ermöglichen. Für Phoenix wird anstelle von MapReduce die native NoSQL-Kompilierung verwendet, um Abfragen zu kompilieren und so basierend auf HBase Anwendungen mit geringer Wartezeit zu erstellen. Phoenix fügt Coprozessoren hinzu, um das Ausführen von per Client bereitgestelltem Code im Adressraum des Servers zu unterstützen und den Code auszuführen, der den Daten beigefügt ist. Auf diese Weise kann die Datenübertragung zwischen Client und Server reduziert werden.  Erstellen Sie zuerst Tabellen, und laden Sie anschließend Daten in diese Tabellen, um Phoenix in HDInsight zu verwenden.

## <a name="bulk-loading-with-apache-phoenix"></a>Massenladen mit Apache Phoenix

Es gibt mehrere Möglichkeiten, wie Daten in HBase gelangen, z.B. die Nutzung von Client-APIs, ein MapReduce-Auftrag mit TableOutputFormat oder das manuelle Eingeben der Daten per HBase-Shell. In Phoenix gibt es zwei Methoden zum Laden von CSV-Daten in Phoenix-Tabellen: ein Clientladetool mit dem Namen `psql` und ein MapReduce-basiertes Tool für das Massenladen.

Das Tool `psql` ist ein Singlethread-Tool und am besten zum Laden von Daten im MB- oder GB-Bereich geeignet. Alle CSV-Dateien, die geladen werden sollen, müssen über die Dateierweiterung „.csv“ verfügen.  Sie können in der Befehlszeile von `psql` auch SQL-Skriptdateien mit der Dateierweiterung „.sql“ angeben.

Das Massenladen mit MapReduce wird für deutlich größere Datenvolumen verwendet (normalerweise in der Produktion), da bei MapReduce mehrere Threads genutzt werden.

Stellen Sie vor Beginn des Datenladevorgangs sicher, dass Phoenix aktiviert ist und die Einstellungen für den Abfragetimeout wie erwartet festgelegt wurden.  Greifen Sie auf das [Apache Ambari](https://ambari.apache.org/)-Dashboard Ihres HDInsight-Clusters zu, und wählen Sie „HBase“ und dann die Konfigurationsregisterkarte aus.  Scrollen Sie nach unten, um zu überprüfen, ob Apache Phoenix wie hier dargestellt auf `enabled` festgelegt ist:

![Apache Phoenix – Einstellungen für HDInsight-Cluster](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Verwenden von `psql` für das Massenladen von Tabellen

1. Erstellen Sie eine neue Tabelle, und speichern Sie Ihre Abfrage unter dem Dateinamen `createCustomersTable.sql`.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. Kopieren Sie die CSV-Datei (Beispielinhalt dargestellt) als `customers.csv` in ein `/tmp/`-Verzeichnis für das Laden in Ihre neu erstellte Tabelle.  Verwenden Sie den Befehl `hdfs`, um die CSV-Datei an den gewünschten Quellspeicherort zu kopieren.

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. Erstellen Sie eine SQL-SELECT-Abfrage, um sicherzustellen, dass die Eingabedaten richtig geladen wurden, und speichern Sie Ihre Abfrage dann unter dem Dateinamen `listCustomers.sql`. Sie können eine beliebige SQL-Abfrage verwenden.
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. Führen Sie das Massenladen der Daten durch, indem Sie ein *neues* Hadoop-Befehlsfenster öffnen. Ändern Sie zuerst mit dem Befehl `cd` den Speicherort des Ausführungsverzeichnisses, und verwenden Sie anschließend das Tool `psql` (Python-Befehl `psql.py`). 

    Im folgenden Beispiel wird vorausgesetzt, dass Sie die Datei `customers.csv` aus einem Speicherkonto in Ihr lokales temporäres Verzeichnis kopiert haben, indem Sie wie oben in Schritt 2 `hdfs` verwendet haben.

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE]   
    > Suchen Sie zum Ermitteln des `ZookeeperQuorum`-Namens in der Datei `/etc/hbase/conf/hbase-site.xml` anhand des Eigenschaftsnamens `hbase.zookeeper.quorum` nach der [Apache ZooKeeper](https://zookeeper.apache.org/)-Quorumzeichenfolge.

5. Nachdem der `psql`-Vorgang abgeschlossen wurde, sollte in Ihrem Befehlsfenster die folgende Meldung angezeigt werden:

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Verwenden von MapReduce für das Massenladen von Tabellen

Verwenden Sie das MapReduce-Ladetool, um für die Ladevorgänge im Cluster einen höheren Durchsatz zu erzielen. Mit diesem Ladeprogramm werden zunächst alle Daten in HFiles konvertiert, und anschließend werden die erstellten HFiles für HBase bereitgestellt.

1. Starten Sie das MapReduce-CSV-Ladeprogramm, indem Sie den Befehl `hadoop` mit dem JAR-Archiv des Phoenix-Clients verwenden:

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. Erstellen Sie wie mit `CreateCustomersTable.sql` im obigen Schritt 1 eine neue Tabelle mit einer SQL-Anweisung.

3. Führen Sie `!describe inputTable` aus, um das Schema Ihrer Tabelle zu überprüfen.

4. Ermitteln Sie den Speicherpfad zu Ihren Eingabedaten, z.B. zur Beispieldatei `customers.csv`. Die Eingabedateien können sich in Ihrem WASB/ADLS-Speicherkonto befinden. In diesem Beispielszenario sind die Eingabedateien im Verzeichnis `<storage account parent>/inputFolderBulkLoad` enthalten.

5. Wechseln Sie in das Ausführungsverzeichnis für den MapReduce-Befehl für das Massenladen:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. Ermitteln Sie Ihren `ZookeeperQuorum`-Wert in `/etc/hbase/conf/hbase-site.xml` mit dem Eigenschaftsnamen `hbase.zookeeper.quorum`.

7. Richten Sie den Klassenpfad ein, und führen Sie den Toolbefehl `CsvBulkLoadTool` aus:

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. Um MapReduce mit Azure Data Lake Storage zu verwenden, suchen Sie das Stammverzeichnis von Data Lake Storage. Es handelt sich dabei um den `hbase.rootdir`-Wert in `hbase-site.xml`. Im folgenden Befehl lautet das Data Lake Storage-Stammverzeichnis `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`. Geben Sie in diesem Befehl die Data Lake Storage-Eingabe- und -Ausgabeordner als Parameter an:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>Empfehlungen

* Verwenden Sie für die Eingabe- und Ausgabeordner das gleiche Speichermedium, entweder Azure Storage (WASB) oder Azure Data Lake Storage (ADL). Um Daten aus Azure Storage an Data Lake Storage zu übertragen, können Sie den Befehl `distcp` verwenden:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Verwenden Sie größere Workerknoten. Bei den Zuordnungsprozessen des MapReduce-Vorgangs für das Massenkopieren werden große Mengen von temporären Ausgaben erzeugt, die den verfügbaren Speicherplatz (nicht DFS) belegen. Verwenden Sie beim Massenladen von großen Datenmengen eine höhere Zahl von Workerknoten und außerdem größere Workerknoten. Die Anzahl von Workerknoten, die Sie für Ihren Cluster zuordnen, hat eine direkte Auswirkung auf die Verarbeitungsgeschwindigkeit.

* Unterteilen Sie Eingabedateien in Blöcke mit einer Größe von ca. 10 GB. Da das Massenladen ein speicherintensiver Vorgang ist, führt das Aufteilen der Eingabedateien in mehrere Blöcke zu einer besseren Leistung.

* Vermeiden Sie Regionsserver-Hotspots. Falls sich Ihr Zeilenschlüssel monoton erhöht, kommt es durch sequenzielle HBase-Schreibvorgänge ggf. zu Regionsserver-Hotspotting. Durch das so genannte *Salting* des Zeilenschlüssels wird die Anzahl von sequenziellen Schreibvorgängen reduziert. In Phoenix besteht die Möglichkeit, den Zeilenschlüssel auf transparente Weise mit einem Salting-Byte für eine bestimmte Tabelle zu versehen, wie unten angegeben.

## <a name="next-steps"></a>Nächste Schritte

* [Bulk Data Loading with Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html) (Massenladen von Daten mit Apache Phoenix)
* [Verwenden von Apache Phoenix mit Linux-basierten Apache HBase-Clustern in HDInsight](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [Salted Tables](https://phoenix.apache.org/salted.html) (Tabellen mit Salting)
* [Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html)
