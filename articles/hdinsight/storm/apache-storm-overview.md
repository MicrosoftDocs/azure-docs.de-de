---
title: Informationen zu Apache Storm – Azure HDInsight
description: Mit Apache Storm können Sie Datenströme in Echtzeit verarbeiten. Mit Azure HDInsight können Sie auf einfache Weise Storm-Cluster in der Azure-Cloud erstellen. Mit Visual Studio können Sie Storm-Lösungen mithilfe von C# erstellen und dann in Ihren HDInsight Storm-Clustern bereitstellen.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: a2bca34f683df27c97b131d57c6c3278f7cb175e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928905"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Was ist Apache Storm in Azure HDInsight?

[Apache Storm](https://storm.apache.org/) ist ein verteiltes, fehlertolerantes Open Source-Berechnungssystem. Mithilfe von Storm können Sie Datenströme in Echtzeit mit [Apache Hadoop](../hadoop/apache-hadoop-introduction.md) verarbeiten. Storm-Lösungen sind außerdem in der Lage, die Verarbeitung von Daten zu garantieren und Daten erneut wiederzugeben, die beim ersten Versuch nicht erfolgreich verarbeitet wurden.

## <a name="why-use-apache-storm-on-hdinsight"></a>Vorteile von Apache Storm in HDInsight

Storm in HDInsight umfasst die folgenden Features:

* __Vereinbarung zum Servicelevel (SLA) mit 99% Verfügbarkeit von Storm__: Storm in HDInsight bietet durchgehend umfassenden Support. Außerdem gilt für Storm in HDInsight eine SLA von 99,9 Prozent. Für einen Storm-Cluster wird von Microsoft also eine externe Konnektivität für mindestens 99,9 Prozent der Zeit garantiert. Weitere Informationen finden Sie unter [Azure-Support](https://azure.microsoft.com/support/options/). Weitere Informationen finden Sie im Dokument [SLA für HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Einfache Anpassung durch Ausführung von Skripts für einen Storm-Cluster während oder nach der Erstellung. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](../hdinsight-hadoop-customize-cluster-linux.md).

* **Erstellen von Lösungen in mehreren Sprachen**: Sie können die Storm-Komponenten in der Sprache Ihrer Wahl schreiben (etwa in Java, C# oder Python).

    * Zusammenarbeit von Visual Studio und HDInsight für die Entwicklung, Verwaltung und Überwachung von C#-Topologien. Weitere Informationen finden Sie unter [Entwickeln von C#-Storm-Topologien mit HDInsight-Tools für Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

    * Unterstützung der Java-Schnittstelle „Trident“. Sie können Storm-Topologien erstellen, die eine exakt einmalige Verarbeitung von Nachrichten, transaktionale Datenspeicherpersistenz sowie eine Reihe allgemeiner Stream Analytics-Vorgänge unterstützen.

* **Dynamische Skalierung**: Sie können Workerknoten ohne Auswirkungen auf aktive Storm-Topologien hinzufügen oder entfernen. Sie müssen ausgeführte Topologien deaktivieren und neu aktivieren, um neue, durch Skalierungsvorgänge hinzugefügte Knoten nutzen zu können.

* **Erstellen von Streamingpipelines mithilfe verschiedener Azure-Dienste**: Storm in HDInsight kann in andere Azure-Dienste integriert werden, etwa in Event Hubs, SQL-Datenbank, Azure Storage und Azure Data Lake Storage. Eine Beispiellösung, die in Azure-Dienste integriert wird, finden Sie unter [Verarbeiten von Ereignissen aus Event Hubs mit Apache Storm in HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

Eine Liste der Unternehmen, die Apache Storm für ihre Echtzeitanalyselösungen verwenden, finden Sie unter [Unternehmen, die Apache Storm verwenden](https://storm.apache.org/Powered-By.html).

Informationen zu den ersten Schritten mit Storm finden Sie unter [Erstellen und Überwachen einer Apache Storm-Topologie in Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Funktionsweise von Apache Storm

Anstelle der [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)-Aufträge, die Sie möglicherweise kennen, führt Storm Topologien aus. Storm-Topologien setzen sich aus mehreren Komponenten zusammen, die in einem gerichteten azyklischen Graph (DAG) angeordnet sind. Daten werden zwischen den Komponenten im Diagramm übertragen. Jede Komponente nutzt einen oder mehrere Datenströme und kann optional einen oder mehrere Datenströme ausgeben kann. Das folgende Diagramm veranschaulicht den Datenfluss zwischen Komponenten in einer einfachen Wortzählungstopologie:

![Beispielanordnung von Komponenten in einer Storm-Topologie](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* Spout-Komponenten bringen Daten in eine Topologie ein. Sie geben mindestens einen Datenstrom in die Topologie aus.

* Bolt-Komponenten nutzen Datenströme, die von Spouts oder von anderen Bolts ausgegeben werden. Bolts können optional auch Datenströme in die Topologie ausgeben. Außerdem sind Bolts für das Schreiben von Daten in externe Dienste oder externen Speicher (wie etwa HDFS, Kafka oder HBase) zuständig.

## <a name="reliability"></a>Zuverlässigkeit

Mit Apache Storm ist garantiert, dass jede eingehende Nachricht auch dann vollständig verarbeitet wird, wenn die Datenanalyse über Hunderte von Knoten verteilt ist.

Der Nimbus-Knoten bietet ähnliche Funktionen wie Apache Hadoop JobTracker. Nimbus weist anderen Knoten im Cluster über Apache ZooKeeper Aufgaben zu. Zookeeper-Knoten kümmern sich um die Koordination eines Clusters und unterstützen die Kommunikation zwischen Nimbus und dem Supervisor-Prozess in den Workerknoten. Wenn ein Verarbeitungsknoten ausfällt, wird der Nimbus-Knoten darüber informiert, und die Aufgabe sowie zugehörige Daten werden einem anderen Knoten zugewiesen.

In der Standardkonfiguration verfügen Apache Storm-Cluster über einen einzelnen Nimbus-Knoten. Storm in HDInsight bietet zwei Nimbus-Knoten. Wenn der primäre Knoten ausfällt, wechselt der Storm-Cluster auf den sekundären Knoten, während der primäre Knoten wiederhergestellt wird. Das folgende Diagramm veranschaulicht die Konfiguration des Aufgabenablaufs für Storm in HDInsight:

![Diagramm mit Nimbus, Zookeeper und Supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>Einfache Bedienung

|Zweck |BESCHREIBUNG |
|---|---|
|SSH-Konnektivität (Secure Shell)|Sie können per SSH über das Internet auf die Hauptknoten Ihres Storm-Clusters zugreifen. Mit SSH können Sie Befehle direkt in Ihrem Cluster ausführen. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Webkonnektivität|Für alle HDInsight-Cluster wird die Ambari-Webbenutzeroberfläche bereitgestellt. Über die Ambari-Webbenutzeroberfläche können Sie Dienste in Ihrem Cluster komfortabel überwachen, konfigurieren und verwalten. Storm-Cluster bieten auch die Storm-Benutzeroberfläche. Mit der Storm-Benutzeroberfläche können Sie die Ausführung von Storm-Topologien über Ihren Browser überwachen und verwalten. Weitere Informationen finden Sie in den Dokumenten [Verwalten von HDInsight mithilfe der Apache Ambari-Webbenutzeroberfläche](../hdinsight-hadoop-manage-ambari.md) und [Überwachen und Verwalten mit der Apache Storm-Benutzeroberfläche](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui).|
|Azure PowerShell und Azure-Befehlszeilenschnittstelle|PowerShell und die Azure-Befehlszeilenschnittstelle bieten Befehlszeilenprogramme, die Sie auf Ihrem Clientsystem für die Arbeit mit HDInsight und anderen Azure-Diensten verwenden können.|
|Visual Studio-Integration|Azure Data Lake Tools für Visual Studio enthält Projektvorlagen für die Erstellung von C#-Storm-Topologien unter Verwendung des SCP.NET-Frameworks. Data Lake Tools bietet zudem Tools zum Bereitstellen, Überwachen und Verwalten von Lösungen mit Storm in HDInsight. Weitere Informationen finden Sie unter [Entwickeln von C#-Storm-Topologien mit HDInsight-Tools für Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Integration in andere Azure-Dienste

* __Azure Data Lake Storage__: Informationen finden Sie unter [Tutorial: Schreiben in Apache Hadoop HDFS aus Apache Storm auf Azure HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Ein Beispiel für die Verwendung von Event Hubs in einem Storm-Cluster finden Sie unter den folgenden Beispielen:

    * [Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Verarbeiten von Ereignissen aus Azure Event Hubs mit Apache Storm in HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL-Datenbank__, __Cosmos DB__, __Event Hubs__ und __HBase__: Beispielvorlagen sind in Data Lake Tools für Visual Studio enthalten. Weitere Informationen finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Anwendungsfälle für Apache Storm

Im Folgenden finden Sie eine Liste mit typischen Verwendungsszenarien für Storm in HDInsight:

* Internet der Dinge (IoT, Internet of Things)
* Betrugserkennung
* Soziale Analysen
* Extrahieren, Transformieren und Laden (ETL)
* Netzwerküberwachung
* Suchen,
* Mobile Engagement

Weitere Informationen zu Szenarien in der Praxis finden Sie im Dokument über die [Nutzung von Apache Storm durch Unternehmen](https://storm.apache.org/Powered-By.html).

## <a name="development"></a>Entwicklung

Mit Data Lake Tools für Visual Studio können .NET-Entwickler Topologien in C# entwerfen und implementieren. Sie können auch Hybridtopologien erstellen, die Java- und C#-Komponenten verwenden. Weitere Informationen finden Sie unter [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Sie können auch Java-Lösungen entwickeln und dabei Ihre bevorzugte IDE verwenden. Weitere Informationen finden Sie unter [Entwickeln von Java-basierten Topologien für Apache Storm in HDInsight](apache-storm-develop-java-topology.md).

Storm-Komponenten können auch unter Verwendung von Python entwickelt werden. Weitere Informationen finden Sie unter [Entwickeln von Apache Storm-Topologien mit Python in HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Gängige Entwicklungsmuster

### <a name="guaranteed-message-processing"></a>Garantierte Nachrichtenverarbeitung

Mit Apache Storm lassen sich verschiedene Stufen der garantierten Nachrichtenverarbeitung implementieren. Eine einfache Storm-Anwendung garantiert beispielsweise die Verarbeitung nach dem Prinzip „At-Least-Once“, während Trident Nachrichten nach dem Prinzip „Exactly-Once“ garantiert. Informationen finden Sie auf apache.org unter [Garantien für die Datenverarbeitung](https://storm.apache.org/about/guarantees-data-processing.html).

### <a name="ibasicbolt"></a>IBasicBolt

Eine übliche Methode besteht darin, ein Eingangstupel zu lesen, null oder mehr Tupel auszugeben und das Eingangstupel am Ende der execute-Methode dann sofort zu bestätigen. Storm stellt die [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html)-Schnittstelle für die Automatisierung dieses Musters bereit.

### <a name="joins"></a>Joins

Die Vorgehensweise beim Verknüpfen von Datenströmen variiert zwischen Anwendungen. So können Sie beispielsweise alle Tupel aus mehreren Datenströmen in einem neuen Datenstrom zusammenführen oder nur Tupel-Batches für ein bestimmtes Intervall zusammenführen. In allen Fällen kann das Zusammenführen mithilfe von [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) erreicht werden. Die Feldgruppierung ist eine Möglichkeit, um zu definieren, wie Tupel an Bolts weitergeleitet werden.

Das folgende Java-Beispiel verwendet „fieldsGrouping“, um Tupel aus den Komponenten „1“, „2“ und „3“ an den MyJoiner-Bolt weiterzuleiten:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Batches

Apache Storm bietet einen internen Timermechanismus, der als „Tick-Tupel“ bezeichnet wird. Sie können festlegen, wie oft ein Tick-Tupel in der Topologie ausgegeben wird.

Ein Beispiel für die Verwendung eines Tick-Tupels für eine C#-Komponente finden Sie unter [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Caches

Speicherinterne Zwischenspeicherung wird oft verwendet, um die Verarbeitung zu beschleunigen, da häufig verwendete Elemente im Speicher erhalten bleiben. Da eine Topologie auf mehrere Knoten und mehrere Prozesse auf jedem Knoten verteilt ist, sollten Sie erwägen, [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) zu verwenden. Verwenden Sie `fieldsGrouping`, um sicherzustellen, dass Tupel mit den Feldern, die für die Cachesuche verwendet werden, immer an den gleichen Prozess weitergeleitet werden. Mit dieser Gruppierungsfunktion wird die prozessübergreifende Duplizierung von Cacheeinträgen verhindert.

### <a name="stream-top-n"></a>Datenstrom „Top N“

Wenn Ihre Topologie auf die Berechnung eines Top N-Werts angewiesen ist, berechnen Sie diesen Wert parallel. Führen Sie anschließend die Ausgabe der entsprechenden Berechnungen zu einem globalen Wert zusammen. Hierzu können Sie [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) verwenden, um Daten für die parallele Verarbeitung nach Feldern weiterzuleiten. Anschließend können Sie Daten an einen Bolt weiterleiten, der global den Top N-Wert bestimmt.

Ein Beispiel für die Berechnung eines Top N-Werts finden Sie im Beispiel [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Protokollierung

Storm verwendet Apache Log4j 2 zum Protokollieren von Informationen. Standardmäßig wird eine große Datenmenge protokolliert, was die Durchsicht der Informationen erschweren kann. Sie können eine Protokollierungskonfigurationsdatei als Teil Ihrer Storm-Topologie hinzufügen, um das Protokollierungsverhalten zu steuern.

Eine Beispieltopologie, die das Konfigurieren der Protokollierung veranschaulicht, finden Sie im [Java-basierten WordCount](apache-storm-develop-java-topology.md) -Beispiel für Storm in HDInsight.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Lösungen für Echtzeitanalysen mit Apache Storm in HDInsight:

* [Erstellen und Überwachen einer Apache Storm-Topologie in Azure HDInsight](apache-storm-quickstart.md)
* [Beispieltopologien für Apache Storm in HDInsight](apache-storm-example-topology.md)
