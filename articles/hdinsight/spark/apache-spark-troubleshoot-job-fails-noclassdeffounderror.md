---
title: NoClassDefFoundError – Apache Spark mit Apache Kafka-Daten in Azure HDInsight
description: Ein Apache Spark-Streamingauftrag, der Daten aus einem Apache Kafka-Cluster liest, führt zu dem Fehler NoClassDefFoundError in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c523dbc4612ebfcebf3923900b31f3a2a7b0c5c6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287774"
---
# <a name="apache-spark-streaming-job-that-reads-apache-kafka-data-fails-with-noclassdeffounderror-in-hdinsight"></a>Apache Spark-Streamingauftrag, der Apache Kafka-Daten liest, führt zu dem Fehler NoClassDefFoundError in HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Verwendung von Apache Spark-Komponenten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Der Apache Spark-Cluster führt einen Spark-Streamingauftrag aus, der Daten aus einem Apache Kafka-Cluster liest. Der Spark-Streamingauftrag führt zu einem Fehler, wenn die Kafka-Streamkomprimierung aktiviert ist. In diesem Fall führt die Yarn-App application_1525986016285_0193 für das Spark-Streaming zu dem Fehler:

```
18/05/17 20:01:33 WARN YarnAllocator: Container marked as failed: container_e25_1525986016285_0193_01_000032 on host: wn87-Scaled.2ajnsmlgqdsutaqydyzfzii3le.cx.internal.cloudapp.net. Exit status: 50. Diagnostics: Exception from container-launch.
Container id: container_e25_1525986016285_0193_01_000032
Exit code: 50
Stack trace: ExitCodeException exitCode=50: 
 at org.apache.hadoop.util.Shell.runCommand(Shell.java:944)
```

## <a name="cause"></a>Ursache

Dieser Fehler kann durch Angabe einer Version der JAR-Datei `spark-streaming-kafka` verursacht werden, die sich von der Version des ausgeführten Kafka-Clusters unterscheidet.

Wenn Sie z. B. die Kafka-Clusterversion 0.10.1 ausführen, führt der folgende Befehl zu einem Fehler:

```
spark-submit \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0
--conf spark.executor.instances=16 \
...
~/Kafka_Spark_SQL.py <bootstrap server details>
```

## <a name="resolution"></a>Lösung

Verwenden Sie den Befehl „spark-submit“ mit der Option `–packages`, und stellen Sie sicher, dass die Version der JAR-Datei „spark-streaming-kafka“ mit der Version des ausgeführten Kafka-Clusters identisch ist.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]