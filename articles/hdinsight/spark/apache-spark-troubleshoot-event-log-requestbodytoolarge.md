---
title: Fehler „RequestBodyTooLarge“ aus der Apache Spark-App – Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge ist im Protokoll für die Apache Spark-Streaming-App in Azure HDInsight enthalten.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 38d6e5bfea1ae7ad4eead3a3f614007d31f0a7cb
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287921"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>„NativeAzureFileSystem...RequestBodyTooLarge“ wird im Apache Spark-Streaming-App-Protokoll in HDInsight angezeigt.

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Verwendung von Apache Spark-Komponenten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Der Fehler `NativeAzureFileSystem ... RequestBodyTooLarge` wird in das Treiberprotokoll für eine Apache Spark-Streaming-App geschrieben.

## <a name="cause"></a>Ursache

Die Größe Ihrer Spark-Ereignisprotokolldatei übersteigt vermutlich die Dateilänge für WASB.

In Spark 2.3 generiert jede Spark-App eine Spark-Ereignisprotokolldatei. Die Spark-Ereignisprotokolldatei für eine Spark-Streaming-App wird während der App-Ausführung immer größer. Heutzutage gilt für eine Datei in WASB ein Blocklimit von 50.000, und die Standardblockgröße beträgt 4 MB. Also beträgt die maximale Dateigröße in der Standardkonfiguration 195 GB. In Azure Storage wurde jedoch die maximale Blockgröße auf 100 MB angehoben und dadurch die Beschränkung für einzelne Dateien auf 4,75 TB erhöht. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Blob Storage](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Lösung

Für diesen Fehler gibt es drei Lösungen:

* Vergrößern Sie die Blockgröße auf bis zu 100 MB. Ändern Sie auf der Ambari-Benutzeroberfläche die HDFS-Konfigurationseigenschaft `fs.azure.write.request.size` (oder erstellen Sie sie im Abschnitt `Custom core-site`). Legen Sie die Eigenschaft auf einen größeren Wert fest, z. B.: 33554432. Speichern Sie die aktualisierte Konfiguration, und starten Sie die betroffenen Komponenten neu.

* Beenden Sie den Spark-Streamingauftrag regelmäßig, und übermitteln Sie ihn erneut.

* Verwenden Sie HDFS zum Speichern der Spark-Ereignisprotokolle. Die Verwendung von HDFS für die Speicherung kann zu einem Verlust von Spark-Ereignisdaten während der Clusterskalierung oder bei Azure-Upgrades führen.

    1. Nehmen Sie auf der Ambari-Benutzeroberfläche Änderungen an `spark.eventlog.dir` und `spark.history.fs.logDirectory` vor:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Erstellen Sie Verzeichnisse in HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Starten Sie alle betroffenen Dienste über die Ambari-Benutzeroberfläche neu.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]