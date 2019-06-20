---
title: Problembehandlung bei Storm mit Azure HDInsight
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum Arbeiten mit Apache Storm und Azure HDInsight.
keywords: Azure HDInsight, Storm, FAQ, Problembehandlungshandbuch, allgemeine Probleme
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 3866e25cc3c87f569e84b2d5639d25aa9386cc78
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713757"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Problembehandlung bei Apache Storm mit Azure HDInsight

Hier finden Sie Informationen zu den gängigsten Problemen bei der Verwendung von [Apache Storm](https://storm.apache.org/)-Nutzlasten in [Apache Ambari](https://ambari.apache.org/) sowie entsprechende Lösungen.

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Wie greife ich auf die Storm-Benutzeroberfläche in einem Cluster zu?
Sie haben zwei Optionen für den Zugriff auf die Storm-Benutzeroberfläche in einem Browser:

### <a name="ambari-ui"></a>Ambari-Benutzeroberfläche
1. Wechseln Sie zum Ambari-Dashboard.
2. Wählen Sie in der Liste mit den Diensten **Storm** aus.
3. Wählen Sie im Menü **Quick Links** (Direktlinks) **Storm UI** (Storm-Benutzeroberfläche) aus.

### <a name="direct-link"></a>Direkte Verknüpfung
Sie können an der folgenden URL auf die Storm-Benutzeroberfläche zugreifen:

https://\<DNS-Name des Clusters\>/stormui

Beispiel:

 https://stormcluster.azurehdinsight.net/stormui

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Wie übertrage ich Storm-EventHub-Spout-Prüfpunktinformationen von einer Topologie zu einer anderen?

Beim Entwickeln von Topologien, die Daten aus Azure Event Hubs mithilfe der JAR-Datei des EventHub-Spouts von HDInsight Storm lesen, müssen Sie eine Topologie bereitstellen, die in einem neuen Cluster den gleichen Namen hat. Sie müssen jedoch die Prüfpunktdaten beibehalten, die im alten Cluster in [Apache ZooKeeper](https://zookeeper.apache.org/) committet wurden.

### <a name="where-checkpoint-data-is-stored"></a>Wo werden Prüfpunktdaten gespeichert?
Prüfpunktdaten für Offsets werden vom EventHub-Spout in Zookeeper unter zwei Stammpfaden gespeichert:
- Nicht transaktionale Spout-Prüfpunkte werden unter „/eventhubspout“ gespeichert.
- Transaktionale Spout-Prüfpunktdaten werden unter „/transactional“ gespeichert.

### <a name="how-to-restore"></a>Vorgehensweise zum Wiederherstellen
Skripts und Bibliotheken, die Sie verwenden, um Daten aus ZooKeeper zu exportieren und mit einem neuen Namen in ZooKeeper zurück zu importieren, finden Sie in den [Beispielen zu HDInsight Storm](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Der Ordner „lib“ enthält JAR-Dateien, die die Implementierung für den Import-/Exportvorgang enthalten. Der Ordner „bash“ enthält ein Beispielskript zum Veranschaulichen des Exports von Daten vom ZooKeeper-Server im alten Cluster und Zurückimportieren in den ZooKeeper-Server im neuen Cluster.

Führen das Skript [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) auf den ZooKeeper-Knoten aus, um Daten zu exportieren und dann zu importieren. Aktualisieren Sie das Skript auf die richtige Version von Hortonworks Data Platform (HDP). (Wir arbeiten daran, diese Skripts in HDInsight generisch zu gestalten. Generische Skripts können auf einem beliebigen Knoten im Cluster ohne Änderungen durch den Benutzer ausgeführt werden.)

Der Exportbefehl schreibt die Metadaten in einem Pfad im Apache Hadoop Distributed File System (HDFS) (in Azure Blob Storage oder Azure Data Lake Storage) an einem Speicherort, den Sie festlegen.

### <a name="examples"></a>Beispiele

#### <a name="export-offset-metadata"></a>Exportieren von Offsetmetadaten
1. Stellen Sie per SSH eine Verbindung mit dem ZooKeeper-Cluster im Cluster her, von dem aus der Prüfpunktoffset exportiert werden muss.
2. Führen Sie (nach der Aktualisierung der HDP-Versionszeichenfolge) den folgenden Befehl aus, um ZooKeeper-Offsetdaten in den HDFS-Pfad „/stormmetadta/zkdata“ zu exportieren:

    ```apache   
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importieren von Offsetmetadaten
1. Stellen Sie per SSH eine Verbindung mit dem ZooKeeper-Cluster im Cluster her, von dem aus der Prüfpunktoffset importiert werden muss.
2. Führen Sie (nach der Aktualisierung der HDP-Versionszeichenfolge) den folgenden Befehl aus, um ZooKeeper-Offsetdaten aus dem HDFS-Pfad „/stormmetadta/zkdata“ in den Zookeeper-Server im Zielcluster zu importieren:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```
   
#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Löschen von Offsetplatten, damit Topologien entweder vom Anfang oder einem vom Benutzer gewählten Zeitstempel an mit der Verarbeitung von Daten beginnen können
1. Stellen Sie per SSH eine Verbindung mit dem ZooKeeper-Cluster im Cluster her, von dem aus der Prüfpunktoffset gelöscht werden muss.
2. Führen Sie (nach der Aktualisierung der HDP-Versionszeichenfolge) den folgenden Befehl aus, um alle Zookeeper-Offsetdaten im aktuellen Cluster zu löschen:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Wie finde ich Storm-Binärdateien auf einem Cluster?
Storm-Binärdateien für den aktuellen HDP-Stapel befinden sich unter „/usr/hdp/current/storm-client“. Der Speicherort ist sowohl für Haupt- als auch für Workerknoten identisch.
 
In „/usr/hdp“ kann es für bestimmte HDP-Versionen mehrere Binärdateien geben (Beispiel: /usr/hdp/2.5.0.1233/storm). Der Ordner „/usr/hdp/current/storm-client“ ist über einen SYM-Link mit der neuesten Version verknüpft, die im Cluster ausgeführt wird.

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einem HDInsight-Cluster mit SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) und [Apache Storm](https://storm.apache.org/).
 
## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Wie bestimme ich die Bereitstellungstopologie eines Storm-Clusters?
Identifizieren Sie zunächst alle Komponenten, die mit HDInsight Storm installiert sind. Ein Storm-Cluster besteht aus vier Kategorien von Knoten:

* Gatewayknoten
* Hauptknoten
* ZooKeeper-Knoten
* Workerknoten
 
### <a name="gateway-nodes"></a>Gatewayknoten
Ein Gatewayknoten ist ein Gateway- und Reverseproxydienst, der öffentlichen Zugriff auf den aktiven Ambari-Verwaltungsdienst ermöglicht. Dieser Knoten übernimmt auch die Wahl des Ambari Leaders.
 
### <a name="head-nodes"></a>Hauptknoten
Storm-Hauptknoten führen die folgenden Dienste aus:
* Nimbus
* Ambari-Server
* Ambari Metrics-Server
* Ambari Metrics Collector
 
### <a name="zookeeper-nodes"></a>ZooKeeper-Knoten
Im Funktionsumfang von HDInsight ist ein ZooKeeper-Quorum mit drei Knoten enthalten. Die Größe des Quorums ist unveränderlich und nicht neu konfigurierbar.
 
Storm-Dienste im Cluster werden für die automatische Verwendung des ZooKeeper-Quorums konfiguriert.
 
### <a name="worker-nodes"></a>Workerknoten
Storm-Workerknoten führen die folgenden Dienste aus:
* Supervisor
* Worker Java-VMs (JVMs), für die Ausführung von Topologien
* Ambari-Agent
 
## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Wie finde ich Storm-EventHub-Spout-Binärdateien für die Entwicklung?
 
Weitere Informationen zur Verwendung von JAR-Dateien von Storm-EventHub-Spout mit Ihrer Topologie finden Sie in den folgenden Ressourcen.
 
### <a name="java-based-topology"></a>Java-basierte Topologie
[Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (Java)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-java-event-hub-topology)
 
### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-basierte Topologie (Mono in HDI 3.4+-Linux-Storm-Clustern)
[Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (C#)](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-develop-csharp-event-hub-topology)
 
### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Neueste Apache Storm-EventHub-Spout-Binärdateien für HDInsight 3.5+-Linux-Storm-Cluster
Informationen zur Verwendung des neuesten Storm-EventHub-Spouts, der mit HDInsight 3.5+-Linux-Storm-Clustern arbeitet, finden Sie in der [Infodatei](https://github.com/hdinsight/mvn-repo/blob/master/README.md) zu „mvn-repo“.
 
### <a name="source-code-examples"></a>Quellcodebeispiele
Unter [Beispiele](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) erfahren Sie, wie Sie Daten aus Azure Event Hub mithilfe einer (in Java geschriebenen) Apache Storm-Topologie in einem Azure HDInsight-Cluster lesen und schreiben.
 
## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Wie finde ich Storm-Log4J 2-Konfigurationsdateien in Clustern?
 
Gehen Sie wie folgt vor, um [Apache Log4j 2](https://logging.apache.org/log4j/2.x/)-Konfigurationsdateien für Storm-Dienste zu identifizieren:
 
### <a name="on-head-nodes"></a>Auf Hauptknoten
Die Nimbus Log4J-Konfiguration wird aus „usr/hdp/\<HDP-Version\>/storm/log4j2/cluster.xml“ gelesen.
 
### <a name="on-worker-nodes"></a>Auf Workerknoten
Die Supervisor Log4J-Konfiguration wird aus „usr/hdp/\<HDP-Version\>/storm/log4j2/cluster.xml“ gelesen.
 
Die Worker Log4J-Konfiguration wird aus „usr/hdp/\<HDP-Version\>/storm/log4j2/worker.xml“ gelesen.
 
Beispiele: /usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml, /usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml

### <a name="see-also"></a>Siehe auch
[Beheben von Problemen mit Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
