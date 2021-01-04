---
title: Azure Monitor-Protokolle für Apache Kafka – Azure HDInsight
description: Erfahren Sie, wie Sie mithilfe von Azure Monitor-Protokollen Protokolle aus Apache Kafka-Clustern in Azure HDInsight analysieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/17/2020
ms.openlocfilehash: 5128ac7608dfce08471f7a7f97ed28a7971e62b2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534446"
---
# <a name="analyze-logs-for-apache-kafka-on-hdinsight"></a>Analysieren von Protokollen für Apache Kafka in HDInsight

Erfahren Sie, wie Sie mithilfe von Azure Monitor-Protokollen in Apache Kafka generierte Protokolle in HDInsight analysieren.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="logs-location"></a>Protokollspeicherort

Apache Kafka-Protokolle im Cluster befinden sich unter `/var/log/kafka`. Kafka-Protokolle werden unabhängig davon, ob verwaltete Datenträger verwendet werden, nicht über Clusterlebenszyklen hinweg gespeichert oder beibehalten. Die verfügbaren Protokolle sind in der nachfolgenden Tabelle aufgeführt.

|Log |BESCHREIBUNG |
|---|---|
|kafka.out|„stdout“ und „stderr“ des Kafka-Prozesses. In dieser Datei finden Sie die Protokolle zum Starten und Herunterfahren von Kafka.|
|server.log|Das Hauptprotokoll des Kafka-Servers. Alle Kafka-Brokerprotokolle werden hierin aufgenommen.|
|controller.log|Controllerprotokolle, wenn der Broker als Controller fungiert.|
|statechange.log|Alle Statusänderungsereignisse für Broker werden in dieser Datei protokolliert.|
|kafka-gc.log|Statistiken zur Kafka-Garbage Collection.|

## <a name="enable-azure-monitor-logs-for-apache-kafka"></a>Azure Monitor-Protokolle für Apache Kafka aktivieren

Die Schritte zum Aktivieren von Azure Monitor-Protokollen für HDInsight sind für alle HDInsight-Cluster identisch. Verwenden Sie die folgenden Links, um zu verstehen, wie die erforderlichen Dienste erstellt und konfiguriert werden:

1. Erstellen Sie einen Log Analytics-Arbeitsbereich. Weitere Informationen finden Sie im Dokument [Protokolle in Azure Monitor](../../azure-monitor/platform/data-platform-logs.md).

2. Erstellen Sie ein Kafka-Cluster im HDInsight-Cluster. Weitere Informationen finden Sie unter [Einstieg in Apache Kafka in HDInsight](apache-kafka-get-started.md).

3. Konfigurieren Sie den Kafka-Cluster für die Verwendung von Azure Monitor-Protokollen. Weitere Informationen finden Sie im Dokument [Verwenden von Azure Monitor-Protokollen zum Überwachen von HDInsight](../hdinsight-hadoop-oms-log-analytics-tutorial.md).

> [!IMPORTANT]  
> Es dauert ca. 20 Minuten, bis die Daten für Azure Monitor-Protokolle zur Verfügung stehen.

## <a name="query-logs"></a>Abfrageprotokolle

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren Log Analytics-Arbeitsbereich aus.

2. Wählen Sie im linken Menü unter **Allgemein** die Option **Protokolle** aus. In dieser Ansicht können Sie die von Kafka gesammelten Daten suchen. Geben Sie eine Abfrage im Abfragefenster ein, und wählen Sie dann **Ausführen** aus. Nachfolgend sehen Sie einige Beispielsuchen:

* Datenträgerauslastung:

    ```kusto
    Perf
    | where ObjectName == "Logical Disk" and CounterName == "Free Megabytes" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* CPU-Auslastung:

    ```kusto
    Perf 
    | where CounterName == "% Processor Time" and InstanceName == "_Total" and ((Computer startswith_cs "hn" and Computer contains_cs "-") or (Computer startswith_cs "wn" and Computer contains_cs "-")) 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    ```

* Eingehende Nachrichten pro Sekunde: (Ersetzen Sie `your_kafka_cluster_name` durch den Namen Ihres Clusters.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-MessagesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_MessagesInPerSec_Count_value_d) by HostName_s, bin(TimeGenerated, 1h)
    ```

* Eingehende Bytes pro Sekunde: (Ersetzen Sie `wn0-kafka` durch den Hostnamen eines Workerknotens.)

    ```kusto
    metrics_kafka_CL 
    | where HostName_s == "wn0-kafka" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesInPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesInPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

* Ausgehende Bytes pro Sekunde: (Ersetzen Sie `your_kafka_cluster_name` durch den Namen Ihres Clusters.)

    ```kusto
    metrics_kafka_CL 
    | where ClusterName_s == "your_kafka_cluster_name" and InstanceName_s == "kafka-BrokerTopicMetrics-BytesOutPerSec-Count" 
    | summarize AggregatedValue = avg(kafka_BrokerTopicMetrics_BytesOutPerSec_Count_value_d) by bin(TimeGenerated, 1h)
    ```

    Sie können auch `*` eingeben, um alle protokollierte Typen zu suchen. Derzeit sind folgende Protokolle für Abfragen verfügbar:

    | Protokolltyp | BESCHREIBUNG |
    | ---- | ---- |
    | log\_kafkaserver\_CL | server.log des Kafka-Brokers |
    | log\_kafkacontroller\_CL | controller.log des Kafka-Brokers |
    | metrics\_kafka\_CL | Kafka-JMX-Metriken |

    ![Apache Kafka-Protokollanalyse – CPU-Auslastung](./media/apache-kafka-log-analytics-operations-management/apache-kafka-cpu-usage.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Monitor finden Sie unter [Azure Monitor – Übersicht](../../azure-monitor/overview.md) und [Abfragen von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern](../hdinsight-hadoop-oms-log-analytics-use-queries.md).

Weitere Informationen zum Arbeiten mit Apache Kafka finden Sie in den folgenden Dokumenten:

* [Spiegeln von Apache Kafka zwischen HDInsight-Clustern](apache-kafka-mirroring.md)
* [Konfigurieren von Speicher und Skalierbarkeit für Apache Kafka in HDInsight](apache-kafka-scalability.md)
* [Verwenden von Apache Spark-Streaming (DStream) mit Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [Verwenden von strukturiertem Apache Spark-Streaming mit Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)