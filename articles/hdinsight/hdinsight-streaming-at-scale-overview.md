---
title: Streaming im großen Maßstab in Azure HDInsight
description: Hier erfahren Sie, wie Sie das Datenstreaming mit skalierbaren Apache-Clustern in Azure HDInsight verwenden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: a04ce77c7e81a3a73b87eaf5790b383dece35d86
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022716"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Bedarfsorientiertes Streaming in HDInsight

Big Data-Echtzeitlösungen verarbeiten Daten während der Übertragung. In der Regel sind diese Daten zum Zeitpunkt des Eingangs am nützlichsten. Wenn der eingehende Datenstrom aufgrund seiner Größe nicht mehr verarbeitet werden kann, müssen die Ressourcen gedrosselt werden. Alternativ kann ein HDInsight-Cluster hochskaliert werden, um die Anforderungen Ihrer Lösung durch bedarfsgerechtes Hinzufügen von Knoten zu erfüllen.

Von den Datenquellen einer Streaminganwendung können pro Sekunde Millionen von Ereignissen generiert werden, die schnell und ohne den Verlust nützlicher Informationen erfasst werden müssen. Die eingehenden Ereignisse werden durch *Streampufferung* (auch *Ereignisqueuing* genannt) von einem Dienst wie [Apache Kafka](kafka/apache-kafka-introduction.md) oder [Event Hubs](https://azure.microsoft.com/services/event-hubs/) verarbeitet. Nachdem die Ereignisse erfasst wurden, können Sie die Daten innerhalb der *Streamverarbeitungsebene* mithilfe eines Echtzeitanalysesystems wie [Apache Storm](storm/apache-storm-overview.md) oder [Apache Spark Streaming](spark/apache-spark-streaming-overview.md) analysieren. Die verarbeiteten Daten können langfristig in entsprechenden Systemen wie [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/) gespeichert und in Echtzeit in einem Business Intelligence-Dashboard wie [Power BI](https://powerbi.microsoft.com) oder Tableau oder auf einer benutzerdefinierten Webseite angezeigt werden.

![Azure HDInsight-Streamingmuster](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka bietet einen Message Queuing-Dienst mit hohem Durchsatz und geringer Wartezeit und ist nun Teil der Apache-OOS-Suite (Open Source Software). Kafka verwendet ein auf Veröffentlichung und Abonnements basierendes Messagingmodell und speichert Streams von partitionierten Daten sicher in einem verteilten, replizierten Cluster. Bei zunehmendem Durchsatz wird Kafka linear skaliert.

Weitere Informationen finden Sie unter [Einführung in Apache Kafka in HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm ist ein verteiltes, fehlertolerantes Open Source-Berechnungssystem, das für die Echtzeitverarbeitung von Datenströmen mit Hadoop optimiert ist. Die grundlegende Dateneinheit für ein eingehendes Ereignis ist ein Tupel. Hierbei handelt es sich um einen unveränderlichen Satz von Schlüssel-Wert-Paaren. Eine ungebundene Sequenz dieser Tupel bildet einen Datenstrom, der von einem Spout stammt. Der Spout umschließt eine Streamingdatenquelle (beispielsweise Kafka) und gibt Tupel aus. Bei einer Storm-Topologie handelt es sich um eine Sequenz von Transformationen für diese Datenströme.

Weitere Informationen finden Sie unter [Was ist Apache Storm in Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming ist eine Erweiterung für Spark, mit der Sie den gleichen Code wie für die Batchverarbeitung verwenden können. Batchabfragen und interaktive Abfragen können in einer einzelnen Anwendung kombiniert werden. Im Gegensatz zu Storm steht in Spark Streaming eine statusbehaftete Semantik vom Typ „Exactly Once“ zur Verfügung. In Kombination mit der [Kafka Direct-API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), die sicherstellt, dass alle Kafka-Daten genau einmal von Spark Streaming empfangen werden, können End-to-End-Garantien vom Typ „Exactly Once“ erreicht werden. Zu den Stärken von Spark Streaming zählen seine fehlertoleranten Funktionen und die damit verbundene schnelle Wiederherstellung fehlerhafter Knoten bei Verwendung mehrerer Knoten im Cluster.

Weitere Informationen finden Sie in der [Übersicht über Apache Spark-Streaming](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skalieren eines Clusters

Die Anzahl von Knoten im Cluster kann zwar bei der Erstellung angegeben werden, manchmal muss der Cluster jedoch abhängig von der Workload vergrößert oder verkleinert werden. Bei allen HDInsight-Clustern können Sie [die Anzahl von Knoten im Cluster ändern](hdinsight-administer-use-portal-linux.md#scale-clusters). Spark-Cluster können ohne Datenverlust verworfen werden, da alle Daten in Azure Storage oder Data Lake Storage gespeichert werden.

Die Entkopplung von Technologien hat gewisse Vorteile. Bei Kafka handelt es sich beispielsweise um eine Technologie zum Puffern von Ereignissen. Sie ist sehr E/A-lastig und benötigt nur wenig Rechenleistung. Datenstromverarbeitungslösungen wie Spark Streaming sind hingegen rechenintensive Lösungen, die virtuelle Computer mit mehr Leistung benötigen. Dank der Entkopplung dieser Technologien mittels unterschiedlicher Cluster können Sie sie unabhängig voneinander skalieren und die virtuellen Computer optimal nutzen.

### <a name="scale-the-stream-buffering-layer"></a>Skalieren der Ebene für die Streampufferung

Die Streampufferungstechnologien Event Hubs und Kafka verwenden jeweils Partitionen, die von Consumern ausgelesen werden. Zum Skalieren des Eingabedurchsatzes muss die Anzahl von Partitionen erhöht werden, wodurch sich wiederum die Parallelität erhöht. In Event Hubs kann die Anzahl von Partitionen nach der Bereitstellung nicht mehr geändert werden. Daher ist es wichtig, sich schon zu Beginn Gedanken über die Zielgröße zu machen. Bei Kafka können Sie [Partitionen hinzufügen](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion) – und das sogar während Kafka Daten verarbeitet. Kafka verfügt über ein Tool zum Neuzuweisen von Partitionen: `kafka-reassign-partitions.sh`. HDInsight verfügt über ein [Tool zum Ausgleichen von Partitionsreplikaten](https://github.com/hdinsight/hdinsight-kafka-tools): `rebalance_rackaware.py`. Dieses Ausgleichstool ruft das Tool `kafka-reassign-partitions.sh` so auf, dass sich jedes Replikat in einer separaten Fehler- und Updatedomäne befindet. Kafka wird dadurch rackfähig und fehlertoleranter.

### <a name="scale-the-stream-processing-layer"></a>Skalieren der Ebene für die Streamverarbeitung

Apache Storm und Spark Streaming unterstützen beide das Hinzufügen von Workerknoten zu ihren Clustern – und das sogar während der Datenverarbeitung.

Für die Nutzung neuer Knoten, die durch die Skalierung von Storm hinzugefügt wurden, müssen Sie sämtliche Storm-Topologien ausgleichen, die vor dem Vergrößern des Clusters gestartet wurden. Dieser Ausgleich kann über die Webbenutzeroberfläche oder über die Befehlszeilenschnittstelle von Storm durchgeführt werden. Weitere Informationen finden Sie in der [Apache Storm-Dokumentation](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark verwendet für die Konfiguration der Umgebung je nach Anwendungsanforderungen drei zentrale Parameter: `spark.executor.instances`, `spark.executor.cores` und `spark.executor.memory`. Ein *Executor* ist ein Prozess, der für eine Spark-Anwendung gestartet wird. Er wird auf dem Workerknoten ausgeführt und ist für die Ausführung der Anwendungsaufgaben zuständig. Die Standardanzahl von Executors und die Executorgrößen für jeden Cluster werden basierend auf der Anzahl von Workerknoten und der Größe der Workerknoten berechnet. Diese Werte werden auf den einzelnen Clusterhauptknoten in der Datei `spark-defaults.conf` gespeichert.

Die drei Parameter können auf der Clusterebene (für alle Anwendungen, die im Cluster ausgeführt werden) konfiguriert oder für jede einzelne Anwendung angegeben werden. Weitere Informationen finden Sie unter [Verwalten von Ressourcen für den Apache Spark-Cluster unter Azure HDInsight](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Überwachen einer Apache Storm-Topologie in Azure HDInsight](storm/apache-storm-quickstart.md)
* [Beispieltopologien für Apache Storm in HDInsight](storm/apache-storm-example-topology.md)
* [Einführung in Apache Spark in HDInsight](spark/apache-spark-overview.md)
* [Einstieg in Apache Kafka in HDInsight](kafka/apache-kafka-get-started.md)