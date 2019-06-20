---
title: Ports für Hadoop-Dienste in HDInsight – Azure
description: Enthält eine Liste mit Ports, die von in HDInsight ausgeführten Hadoop-Diensten verwendet werden.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 16041d2b7a971c9ba479c133261930b38d130792
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66252776"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Ports für Apache Hadoop-Dienste in HDInsight

Dieses Dokument enthält eine Liste mit den Ports für Apache Hadoop-Dienste, die in Linux-basierten HDInsight-Clustern ausgeführt werden. Außerdem enthält es Informationen zu Ports, die zum Herstellen einer Verbindung mit dem Cluster per SSH verwendet werden.

## <a name="public-ports-vs-non-public-ports"></a>Öffentliche Ports im Vergleich mit nicht öffentlichen Ports

Für Linux-basierte HDInsight-Cluster werden nur drei Ports öffentlich im Internet verfügbar gemacht: 22, 23 und 443. Diese Ports werden zum sicheren Zugreifen auf den Cluster per SSH und auf Dienste verwendet, die über das sichere HTTPS-Protokoll bereitgestellt werden.

Intern wird HDInsight von mehreren Azure Virtual Machines (Knoten im Cluster) implementiert, die in einem Azure Virtual Network ausgeführt werden. Aus dem virtuellen Netzwerk können Sie auf Ports zugreifen, die nicht über das Internet verfügbar gemacht werden. Wenn Sie beispielsweise eine Verbindung mit einem der Hauptknoten per SSH herstellen, können Sie vom Hauptknoten dann direkt auf Dienste zugreifen, die auf den Clusterknoten ausgeführt werden.

> [!IMPORTANT]  
> Wenn Sie kein virtuelles Azure-Netzwerk als eine Konfigurationsoption für HDInsight angeben, wird automatisch eines erstellt. Sie können diesem virtuellen Netzwerk jedoch nicht mit anderen Computern (z.B. anderen virtuellen Azure-Computern oder Ihrem Client-Entwicklungscomputer) beitreten.

Um für weitere Computer den Beitritt zum virtuellen Netzwerk durchzuführen, müssen Sie zuerst das virtuelle Netzwerk erstellen und beim Erstellen des HDInsight-Clusters dann angeben. Weitere Informationen finden Sie unter [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Öffentliche Ports

Alle Knoten eines HDInsight-Clusters befinden sich in einem Azure Virtual Network, und darauf kann aus dem Internet nicht direkt zugegriffen werden. Ein öffentliches Gateway ermöglicht den Internetzugriff auf die folgenden Ports, die für alle HDInsight-Clustertypen gleich sind:

| Dienst | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- |
| sshd |22 |SSH |Stellt eine Verbindung zwischen Clients und SSHD auf dem primären Hauptknoten her. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Verbindet Clients mit sshd auf dem Edgeknoten. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Stellt eine Verbindung zwischen Clients und SSHD auf dem sekundären Hauptknoten her. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Ambari-Webbenutzeroberfläche. Siehe [Verwalten von HDInsight mithilfe der Apache Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |Ambari-REST-API. Siehe [Verwalten von HDInsight mithilfe der Apache Ambari-REST-API](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |HCatalog-REST-API. Siehe [Verwenden von Apache Hive mit Curl](hadoop/apache-hadoop-use-pig-curl.md), [Verwenden von Apache Pig mit Curl](hadoop/apache-hadoop-use-pig-curl.md), [Verwenden von MapReduce mit Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Stellt per ODBC ein Verbindung mit Hive her. Siehe [Verbinden von Excel mit HDInsight mithilfe des Microsoft ODBC-Treibers](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Stellt per JDBC ein Verbindung mit Apache Hive her. Siehe [Herstellen einer Verbindung mit Apache Hive unter HDInsight per Hive-JDBC-Treiber](hadoop/apache-hadoop-connect-hive-jdbc-driver.md) |

Für spezielle Clustertypen ist Folgendes verfügbar:

| Dienst | Port | Protocol | Clustertyp | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |hbase |HBase-REST-API. Siehe [Erste Schritte mit Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md) |
| Livy |443 |HTTPS |Spark |Spark-REST-API. Siehe [Remoteübermittlung von Apache Spark-Aufträgen mit Apache Livy](spark/apache-spark-livy-rest-interface.md) |
| Spark Thrift-Server |443 |HTTPS |Spark |Zum Übermitteln von Hive-Abfragen verwendeter Spark Thrift-Server. Siehe [Verwenden von Beeline mit Apache Hive in HDInsight](hadoop/apache-hadoop-use-hive-beeline.md) |
| Storm |443 |HTTPS |Storm |Storm-Webbenutzeroberfläche. Siehe [Bereitstellen und Verwalten von Apache Storm-Topologien in HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md) |

### <a name="authentication"></a>Authentication

Alle Dienste, die im Internet öffentlich verfügbar gemacht werden, müssen authentifiziert werden:

| Port | Anmeldeinformationen |
| --- | --- |
| 22 oder 23 |SSH-Benutzeranmeldeinformationen, die während der Clustererstellung angegeben werden |
| 443 |Anmeldename (Standard: admin) und Kennwort, die während der Clustererstellung angegeben werden |

## <a name="non-public-ports"></a>Nicht öffentliche Ports

> [!NOTE]  
> Einige Dienste stehen nur bei bestimmten Clustertypen zur Verfügung. HBase beispielsweise ist nur bei HBase-Clustertypen verfügbar.

> [!IMPORTANT]  
> Einige Dienste werden nur auf einem Hauptknoten gleichzeitig ausgeführt. Wenn Sie versuchen, auf dem primären Hauptknoten eine Verbindung mit dem Dienst herzustellen und einen Fehler erhalten, versuchen Sie es mit dem sekundären Hauptknoten erneut.

### <a name="ambari"></a>Ambari

| Dienst | Nodes | Port | URL-Pfad | Protocol | 
| --- | --- | --- | --- | --- |
| Ambari-Webbenutzeroberfläche | Hauptknoten | 8080 | / | HTTP |
| Ambari-REST-API | Hauptknoten | 8080 | /api/v1 | HTTP |

Beispiele:

* Ambari-REST-API: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>HDFS-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| NameNode-Webbenutzeroberfläche |Hauptknoten |30070 |HTTPS |Webbenutzeroberfläche zum Anzeigen des Status |
| NameNode-Metadatendienst |Hauptknoten |8020 |IPC |Dateisystem-Metadaten |
| DataNode |Alle Workerknoten |30075 |HTTPS |Webbenutzeroberfläche zum Anzeigen von Status, Protokollen usw. |
| DataNode |Alle Workerknoten |30010 |&nbsp; |Datenübertragung |
| DataNode |Alle Workerknoten |30020 |IPC |Metadatenvorgänge |
| Sekundärer NameNode |Hauptknoten |50090 |HTTP |Prüfpunkt für NameNode-Metadaten |

### <a name="yarn-ports"></a>YARN-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| Resource Manager-Webbenutzeroberfläche |Hauptknoten |8088 |HTTP |Webbenutzeroberfläche für Resource Manager |
| Resource Manager-Webbenutzeroberfläche |Hauptknoten |8090 |HTTPS |Webbenutzeroberfläche für Resource Manager |
| Resource Manager-Administratoroberfläche |Hauptknoten |8141 |IPC |Für Anwendungsübermittlungen (Hive, Hive-Server, Pig usw.) |
| Resource Manager-Planer |Hauptknoten |8030 |HTTP |Administratoroberfläche |
| Resource Manager-Anwendungsschnittstelle |Hauptknoten |8050 |HTTP |Adresse der Anwendungs-Manager-Schnittstelle |
| NodeManager |Alle Workerknoten |30050 |&nbsp; |Adresse des Container-Managers |
| NodeManager-Webbenutzeroberfläche |Alle Workerknoten |30060 |HTTP |Resource Manager-Schnittstelle |
| Zeitachsenadresse |Hauptknoten |10200 |RPC |RPC-Dienst des Zeitachsendiensts |
| Zeitachsen-Webbenutzeroberfläche |Hauptknoten |8181 |HTTP |Webbenutzeroberfläche des Zeitachsendiensts |

### <a name="hive-ports"></a>Hive-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| HiveServer2 |Hauptknoten |10001 |Thrift |Dienst für die Verbindung mit Hive (Thrift/JDBC) |
| Hive-Metastore |Hauptknoten |9083 |Thrift |Dienst für die Verbindung mit Hive-Metadaten (Thrift/JDBC) |

### <a name="webhcat-ports"></a>WebHCat-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| WebHCat-Server |Hauptknoten |30111 |HTTP |Web-API zusätzlich zu HCatalog und anderen Hadoop-Diensten |

### <a name="mapreduce-ports"></a>MapReduce-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| JobHistory |Hauptknoten |19888 |HTTP |MapReduce JobHistory-Webbenutzeroberfläche |
| JobHistory |Hauptknoten |10020 |&nbsp; |MapReduce JobHistory-Server |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Überträgt Map-Zwischenausgaben an anfordernde Reducer |

### <a name="oozie"></a>Oozie

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| Oozie-Server |Hauptknoten |11000 |HTTP |URL für Oozie-Dienst |
| Oozie-Server |Hauptknoten |11001 |HTTP |Port für Oozie-Verwaltung |

### <a name="ambari-metrics"></a>Ambari-Metriken

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| TimeLine (Anwendungsverlauf) |Hauptknoten |6188 |HTTP |Webbenutzeroberfläche des Zeitachsendiensts |
| TimeLine (Anwendungsverlauf) |Hauptknoten |30200 |RPC |Webbenutzeroberfläche des Zeitachsendiensts |

### <a name="hbase-ports"></a>HBase-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| HMaster |Hauptknoten |16000 |&nbsp; |&nbsp; |
| HMaster-Info-Webbenutzeroberfläche |Hauptknoten |16010 |HTTP |Port für die HBase-Master-Webbenutzeroberfläche |
| Regionsserver |Alle Workerknoten |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |Port, der von Clients für die Verbindungsherstellung mit ZooKeeper verwendet wird |

### <a name="kafka-ports"></a>Kafka-Ports

| Dienst | Nodes | Port | Protocol | BESCHREIBUNG |
| --- | --- | --- | --- | --- |
| Broker |Workerknoten |9092 |[Kafka Wire Protocol](https://kafka.apache.org/protocol.html) |Für die Clientkommunikation |
| &nbsp; |Zookeeper-Knoten |2181 |&nbsp; |Port, der von Clients für die Verbindungsherstellung mit ZooKeeper verwendet wird |

### <a name="spark-ports"></a>Spark-Ports

| Dienst | Nodes | Port | Protocol | URL-Pfad | BESCHREIBUNG |
| --- | --- | --- | --- | --- | --- |
| Spark Thrift-Server |Hauptknoten |10002 |Thrift | &nbsp; | Dienst für die Verbindung mit Spark SQL (Thrift/JDBC) |
| Livy-Server | Hauptknoten | 8998 | HTTP | &nbsp; | Dienst für die Ausführung von Anweisungen, Aufträgen und Anwendungen |
| Jupyter Notebook | Hauptknoten | 8001 | HTTP | &nbsp; | Jupyter Notebook-Website |

Beispiele:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. In diesem Beispiel ist `10.0.0.11` die IP-Adresse des Hauptknotens, der den Livy-Dienst hostet.
