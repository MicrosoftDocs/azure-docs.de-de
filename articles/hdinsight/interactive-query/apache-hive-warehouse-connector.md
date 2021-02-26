---
title: Apache Spark und Hive – Hive Warehouse Connector – Azure HDInsight
description: Es wird beschrieben, wie Sie Apache Spark und Apache Hive unter Azure HDInsight per Hive Warehouse Connector integrieren.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 6611f5ca7ddae243c4bc314be73a9030311cec89
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594433"
---
# <a name="integrate-apache-spark-and-apache-hive-with-hive-warehouse-connector-in-azure-hdinsight"></a>Integrieren von Apache Spark und Apache Hive unter Azure HDInsight per Hive Warehouse Connector

Der Apache Hive Warehouse Connector (HWC) ist eine Bibliothek, die Ihnen ein einfacheres Arbeiten mit Apache Spark und Apache Hive ermöglicht. Er unterstützt Aufgaben wie das Verschieben von Daten zwischen Spark-Datenrahmen und Hive-Tabellen. Darüber hinaus wird das Weiterleiten von Spark-Streamingdaten an Hive-Tabellen unterstützt. Hive Warehouse Connector fungiert quasi als Brücke zwischen Spark und Hive. Außerdem werden Scala, Java und Python als Programmiersprachen für die Entwicklung unterstützt.

Mit Hive Warehouse Connector können Sie die einzigartigen Features von Hive und Spark nutzen, um leistungsfähige Big Data-Anwendungen zu entwickeln.

Apache Hive verfügt über Unterstützung für Datenbanktransaktionen, die atomisch, konsistent, isoliert und dauerhaft sind (Atomic, Consistent, Isolated and Durable, ACID). Weitere Informationen zu ACID und Transaktionen in Hive finden Sie im Artikel zu [Hive-Transaktionen](https://cwiki.apache.org/confluence/display/Hive/Hive+Transactions). Darüber hinaus verfügt Hive über detaillierte Sicherheitssteuerelemente über Apache Ranger und die analytische Verarbeitung mit geringer Latenz (Low Latency Analytical Processing, LLAP), die in Apache Spark nicht verfügbar sind.

Apache Spark enthält eine API für strukturierte Streams, mit der Streamingfunktionen ermöglicht werden, die in Apache Hive nicht verfügbar sind. Ab HDInsight 4.0 gibt es in Apache Spark 2.3.1 und Apache Hive 3.1.0 separate Metastores. Die separaten Metastores können die Interoperabilität erschweren. Der Hive Warehouse-Connector vereinfacht die gemeinsame Nutzung von Spark und Hive. Die HWC-Bibliothek lädt Daten parallel aus LLAP-Daemons in Spark-Executors. Dieser Prozess führt zu besserer Effizienz und Anpassbarkeit als bei einer standardmäßigen JDBC-Verbindung von Spark zu Hive.

![Hive Warehouse Connector-Architektur](./media/apache-hive-warehouse-connector/hive-warehouse-connector-architecture.png)

Einige Vorgänge, die von Hive Warehouse Connector unterstützt werden, sind:

* Beschreiben einer Tabelle
* Erstellen einer Tabelle für Daten im ORC-Format
* Auswählen von Hive-Daten und Abrufen eines Datenrahmens
* Schreiben eines Datenrahmens in Hive als Batchvorgang
* Ausführen einer Hive-Aktualisierungsanweisung
* Lesen von Tabellendaten aus Hive, Durchführen der Transformation für Spark und Schreiben der Daten in eine neue Hive-Tabelle
* Schreiben eines Datenrahmens oder Spark-Streams in Hive per HiveStreaming

## <a name="hive-warehouse-connector-setup"></a>Hive Warehouse Connector-Setup

> [!IMPORTANT]
> - Die Verwendung der auf den Enterprise-Sicherheitspaket-Clustern von Spark 2.4 installierten HiveServer2 Interactive-Instanz mit dem Hive Warehouse Connector wird nicht unterstützt. Stattdessen müssen Sie einen eigenständigen HiveServer2 Interactive-Cluster konfigurieren, um Ihre HiveServer2 Interactive-Workloads zu hosten. Eine Hive Warehouse Connector-Konfiguration, die einen einzelnen Spark 2.4-Cluster verwendet, wird nicht unterstützt.
> - Die HWC-Bibliothek (Hive Warehouse Connector) wird nicht für die Verwendung mit Interactive Query-Clustern unterstützt, in denen die Funktion zur Workloadverwaltung (Workload Management, WLM) aktiviert ist. <br>
In einem Szenario, in dem Sie nur über Spark-Workloads verfügen und die HWC-Bibliothek verwenden möchten, müssen Sie sicherstellen, dass für den Interactive Query-Cluster nicht das Feature zur Workloadverwaltung aktiviert ist (die Konfiguration `hive.server2.tez.interactive.queue` ist in den Hive-Konfigurationen nicht festgelegt). <br>
In einem Szenario, das sowohl Spark-Workloads (HWC) als auch native LLAP-Workloads umfasst, müssen Sie zwei separate Interactive Query-Cluster mit einer freigegebenen Metastore-Datenbank erstellen: einen Cluster für native LLAP-Workloads, bei denen die Funktion zur Workloadverwaltung bedarfsabhängig aktiviert werden kann, und einen weiteren Cluster für HWC-Workloads, bei denen die Funktion zur Workloadverwaltung nicht konfiguriert wird.
Beachten Sie, dass Sie die Ressourcenpläne für die Workloadverwaltung aus beiden Clustern anzeigen können, auch wenn die Funktion nur in einem Cluster aktiviert ist. Nehmen Sie keine Änderungen an den Ressourcenplänen in dem Cluster vor, in dem die Funktion zur Workloadverwaltung deaktiviert ist, da sich dies auf die Funktionalität der Workloadverwaltung im anderen Clustern auswirken kann.

Für Hive Warehouse Connector werden separate Cluster für Spark- und Interactive Query-Workloads benötigt. Führen Sie diese Schritte aus, um diese Cluster in Azure HDInsight einzurichten.

### <a name="create-clusters"></a>Erstellen von Clustern

1. Erstellen Sie einen HDInsight Spark **4.0**-Cluster mit einem Speicherkonto und einem benutzerdefinierten virtuellen Azure-Netzwerk. Informationen zur Erstellung eines Clusters in einem virtuellen Azure-Netzwerk finden Sie unter [Hinzufügen von HDInsight zu einem vorhandenen virtuellen Netzwerk](../../hdinsight/hdinsight-plan-virtual-network-deployment.md#existingvnet).

1. Erstellen Sie einen Cluster des Typs „HDInsight Interactive Query (LLAP) **4.0**“, indem Sie dasselbe Speicherkonto und virtuelle Azure-Netzwerk wie für den Spark-Cluster verwenden.

### <a name="configure-hwc-settings"></a>Konfigurieren von HWC-Einstellungen

#### <a name="gather-preliminary-information"></a>Sammeln vorläufiger Informationen

1. Navigieren Sie in einem Webbrowser zu `https://LLAPCLUSTERNAME.azurehdinsight.net/#/main/services/HIVE`. Hierbei ist „LLAPCLUSTERNAME“ der Name Ihres Interactive Query-Clusters.

1. Navigieren Sie zu **Summary** > **HiveServer2 Interactive JDBC URL**, und notieren Sie sich den Wert. Der Wert kann wie folgt lauten: `jdbc:hive2://zk0-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2ce.bx.internal.cloudapp.net:2181/;serviceDiscoveryMode=zooKeeper;zooKeeperNamespace=hiveserver2-interactive`.

1. Navigieren Sie zu **Configs** > **Advanced** > **Advanced hive-site** > **hive.zookeeper.quorum**, und notieren Sie sich den Wert. Der Wert kann wie folgt lauten: `zk0-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181,zk4-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:2181`.

1. Navigieren Sie zu **Configs** > **Advanced** > **General** > **hive.metastore.uris**, und notieren Sie sich den Wert. Der Wert kann wie folgt lauten: `thrift://iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083,thrift://hn1-iqgiro.rekufuk2y2cezcbowjkbwfnyvd.bx.internal.cloudapp.net:9083`.

1. Navigieren Sie zu **Configs** > **Advanced** > **Advanced hive-interactive-site** > **hive.llap.daemon.service.hosts**, und notieren Sie sich den Wert. Der Wert kann wie folgt lauten: `@llap0`.

#### <a name="configure-spark-cluster-settings"></a>Konfigurieren von Einstellungen des Spark-Clusters

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`. Hierbei ist „CLUSTERNAME“ der Name Ihres Apache Spark-Clusters.

1. Erweitern Sie **Custom spark2-defaults**.

    ![Apache Ambari Spark2-Konfiguration](./media/apache-hive-warehouse-connector/hive-warehouse-connector-spark2-ambari.png)

1. Wählen Sie **Add Property...** , um die folgenden Konfigurationen hinzuzufügen:

    | Konfiguration | Wert |
    |----|----|
    |`spark.datasource.hive.warehouse.load.staging.dir`|`wasbs://STORAGE_CONTAINER_NAME@STORAGE_ACCOUNT_NAME.blob.core.windows.net/tmp`. <br> Legen Sie diese Einstellung auf ein geeignetes mit HDFS kompatibles Stagingverzeichnis fest. Wenn Sie zwei unterschiedliche Cluster verwenden, sollte das Stagingverzeichnis ein Ordner im Stagingverzeichnis Ihres Speicherkontos im LLAP-Cluster sein, damit HiveServer2 Zugriff darauf hat.  Ersetzen Sie `STORAGE_ACCOUNT_NAME` durch den Namen des vom Cluster verwendeten Speicherkontos und `STORAGE_CONTAINER_NAME` durch den Namen des Speichercontainers. |
    |`spark.sql.hive.hiveserver2.jdbc.url`| Der Wert, den Sie zuvor unter **HiveServer2 Interactive JDBC URL** notiert haben. |
    |`spark.datasource.hive.warehouse.metastoreUri`| Der Wert, den Sie zuvor aus **hive.metastore.uris** abgerufen haben. |
    |`spark.security.credentials.hiveserver2.enabled`|`true` für YARN-Clustermodus und `false` für YARN-Clientmodus. |
    |`spark.hadoop.hive.zookeeper.quorum`| Der Wert, den Sie zuvor aus **hive.zookeeper.quorum** abgerufen haben. |
    |`spark.hadoop.hive.llap.daemon.service.hosts`| Der Wert, den Sie zuvor aus **hive.llap.daemon.service.hosts** abgerufen haben. |

1. Speichern Sie die Änderungen, und starten Sie alle betroffenen Komponenten neu.

### <a name="configure-hwc-for-enterprise-security-package-esp-clusters"></a>Konfigurieren von HWC für ESP-Cluster (Enterprise-Sicherheitspaket)

Das Enterprise-Sicherheitspaket (ESP) umfasst Unternehmensfunktionen wie Active Directory-basierte Authentifizierung, Mehrbenutzerunterstützung und rollenbasierte Zugriffssteuerung für Apache Hadoop-Cluster in Azure HDInsight. Weitere Informationen zu Enterprise-Sicherheitspaketen finden Sie unter [Verwendung des Enterprise-Sicherheitspakets in HDInsight](../domain-joined/apache-domain-joined-architecture.md).

Fügen Sie zusätzlich zu den im vorherigen Abschnitt erwähnten Konfigurationen die folgende Konfiguration hinzu, um HWC in den ESP-Clustern zu verwenden.

1. Navigieren Sie auf der Ambari-Webbenutzeroberfläche des Spark-Clusters zu **Spark2** > **CONFIGS** > **Custom spark2-defaults**.

1. Aktualisieren Sie die folgende Eigenschaft.

    | Konfiguration | Wert |
    |----|----|
    | `spark.sql.hive.hiveserver2.jdbc.url.principal`    | `hive/<llap-headnode>@<AAD-Domain>` |
    
    * Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`. Hierbei ist „CLUSTERNAME“ der Name Ihres Interactive Query-Clusters. Klicken Sie auf **HiveServer2 Interactive**. Sie sehen den vollqualifizierten Domänennamen (FQDN) des Hauptknotens, auf dem LLAP ausgeführt wird, wie im Screenshot gezeigt. Ersetzen Sie `<llap-headnode>` durch diesen Wert.

        ![Hauptknoten des Hive Warehouse Connectors](./media/apache-hive-warehouse-connector/head-node-hive-server-interactive.png)

    * Verwenden Sie den [Befehl „ssh“](../hdinsight-hadoop-linux-use-ssh-unix.md), um eine Verbindung mit Ihrem Interactive Query-Cluster herzustellen. Suchen Sie in der Datei `/etc/krb5.conf` nach dem Parameter `default_realm`. Ersetzen Sie `<AAD-DOMAIN>` durch diesen Wert als Zeichenfolge in Großbuchstaben, da die Anmeldeinformationen andernfalls nicht gefunden werden.

        ![Hive Warehouse Connector Azure AD-Domäne](./media/apache-hive-warehouse-connector/aad-domain.png)

    * Beispielsweise `hive/hn0-ng36ll.mjry42ikpruuxgs2qy2kpg4q5e.cx.internal.cloudapp.net@PKRSRVUQVMAE6J85.D2.INTERNAL.CLOUDAPP.NET`.
    
1. Speichern Sie Änderungen, und starten Sie Komponenten nach Bedarf neu.

## <a name="hive-warehouse-connector-usage"></a>Hive Warehouse Connector-Nutzung

Sie können zwischen einigen unterschiedlichen Methoden wählen, um eine Verbindung mit Ihrem Interactive Query-Cluster herzustellen und Abfragen auszuführen, indem Sie Hive Warehouse Connector verwenden. Beispielsweise werden die folgenden Tools unterstützt:

* [Spark-Shell/PySpark](../spark/apache-spark-shell.md)
* [Spark-submit](#spark-submit)
* [Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)


Hier sind einige Beispiele für die Verbindungsherstellung mit HWC aus Spark angegeben.

### <a name="spark-shell"></a>Spark-Shell

1. Verwenden Sie den [Befehl „ssh“](../hdinsight-hadoop-linux-use-ssh-unix.md), um eine Verbindung mit Ihrem Apache Spark-Cluster herzustellen. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Führen Sie in Ihrer SSH-Sitzung den folgenden Befehl aus, und notieren Sie sich die Version von `hive-warehouse-connector-assembly`:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Bearbeiten Sie den unten angegebenen Code, indem Sie die oben ermittelte Version für `hive-warehouse-connector-assembly` verwenden. Führen Sie anschließend den Befehl aus, um die Spark-Shell zu starten:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. Nachdem die Spark-Shell gestartet wurde, kann mit den folgenden Befehlen eine Hive Warehouse Connector-Instanz gestartet werden:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

### <a name="spark-submit"></a>Spark-submit

Nachdem Sie den Scala/Java-Code zusammen mit den Abhängigkeiten als Assembly-JAR-Datei erstellt haben, können Sie den unten angegebenen Befehl verwenden, um eine Spark-Anwendung zu starten. Ersetzen Sie `<VERSION>` und `<APP_JAR_PATH>` durch die tatsächlichen Werte.

* YARN-Clientmodus
    
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode client \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

* YARN-Clustermodus
    ```scala
    spark-submit \
    --class myHwcApp \
    --master yarn \
    --deploy-mode cluster \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=true
    /<APP_JAR_PATH>/myHwcAppProject.jar
    ```

Fügen Sie für Python auch die folgende Konfiguration hinzu. 

```python
--py-files /usr/hdp/current/hive_warehouse_connector/pyspark_hwc-<VERSION>.zip
```
    
## <a name="run-queries-on-enterprise-security-package-esp-clusters"></a>Ausführen von Abfragen in ESP-Clustern (Enterprise-Sicherheitspaket)

Verwenden Sie `kinit`, bevor Sie „spark-shell“ oder „spark-submit“ starten. Ersetzen Sie „USERNAME“ durch den Namen eines Domänenkontos mit Berechtigungen zum Zugreifen auf den Cluster, und führen Sie anschließend den folgenden Befehl aus:

```bash
kinit USERNAME
```

### <a name="securing-data-on-spark-esp-clusters"></a>Schützen von Daten in Spark-ESP-Clustern

1. Erstellen Sie die Tabelle `demo` mit einigen Beispieldaten, indem Sie die folgenden Befehle eingeben:

    ```scala
    create table demo (name string);
    INSERT INTO demo VALUES ('HDinsight');
    INSERT INTO demo VALUES ('Microsoft');
    INSERT INTO demo VALUES ('InteractiveQuery');
    ```

1. Zeigen Sie den Inhalt der Tabelle mit dem folgenden Befehl an. Vor dem Anwenden der Richtlinie wird in der Tabelle `demo` die gesamte Spalte angezeigt.

    ```scala
    hive.executeQuery("SELECT * FROM demo").show()
    ```

    ![Tabelle „demo“ vor dem Anwenden der Ranger-Richtlinie](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-before-ranger-policy.png)

1. Wenden Sie eine Richtlinie für die Spaltenmaskierung an, bei der nur die letzten vier Zeichen der Spalte angezeigt werden.  
    1. Navigieren Sie zur Ranger-Administratoroberfläche unter `https://LLAPCLUSTERNAME.azurehdinsight.net/ranger/`.
    1. Klicken Sie unter **Hive** auf den Hive-Dienst für Ihren Cluster.
        ![Ranger Service Manager](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-service-manager.png)
    1. Klicken Sie auf die Registerkarte für die **Maskierung** und dann auf **Neue Richtlinie hinzufügen**.

        ![Hive Warehouse Connector – Ranger Hive-Richtlinienliste](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-hive-policy-list.png)

    1. Geben Sie den gewünschten Namen für die Richtlinie ein. Wählen Sie Folgendes aus. Datenbank: **Standard**, Hive-Tabelle: **demo**, Hive-Spalte: **name**, Benutzer: **rsadmin2**, Zugriffstypen: **Auswählen** und **Teilmaske: Letzte 4 anzeigen** (Menü **Maskierungsfunktion auswählen**). Klicken Sie auf **Hinzufügen**.
                ![Richtlinie erstellen](./media/apache-hive-warehouse-connector/hive-warehouse-connector-ranger-create-policy.png)
1. Zeigen Sie den Inhalt der Tabelle erneut an. Nachdem die Ranger-Richtlinie angewendet wurde, werden nur die letzten vier Zeichen der Spalte angezeigt.

    ![Tabelle „demo“ nach dem Anwenden der Ranger-Richtlinie](./media/apache-hive-warehouse-connector/hive-warehouse-connector-table-after-ranger-policy.png)

## <a name="next-steps"></a>Nächste Schritte

* [HWC und Apache Spark-Vorgänge](./apache-hive-warehouse-connector-operations.md)
* [Verwenden von Interactive Query mit HDInsight](./apache-interactive-query-get-started.md)
* [HWC-Integration mit Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
* [Beispiele für die Interaktion mit Hive Warehouse Connector per Zeppelin, Livy, spark-submit und pyspark](https://community.hortonworks.com/articles/223626/integrating-apache-hive-with-apache-spark-hive-war.html)
