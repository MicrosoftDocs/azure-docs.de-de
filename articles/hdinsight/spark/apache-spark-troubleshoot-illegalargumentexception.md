---
title: Fehler „IllegalArgumentException“ für Apache Spark – Azure HDInsight
description: IllegalArgumentException für Apache Spark-Aktivität in Azure HDInsight für Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 61190a932389b429040d6b643db2dd2732b2c41d
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287902"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Szenario: IllegalArgumentException für Apache Spark-Aktivität in Azure HDInsight

In diesem Artikel werden Schritte zur Problembehandlung und mögliche Lösungen für Probleme bei der Verwendung von Apache Spark-Komponenten in Azure HDInsight-Clustern beschrieben.

## <a name="issue"></a>Problem

Sie erhalten die folgende Ausnahme, wenn Sie versuchen, eine Spark-Aktivität in einer Azure Data Factory-Pipeline auszuführen:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Ursache

Ein Spark-Auftrag kann nicht ausgeführt werden, wenn sich die JAR-Datei der Anwendung nicht im standardmäßigen oder primären Speicher des Spark-Clusters befindet.

Dies ist ein bekanntes Problem beim Open-Source-Framework von Spark, das in diesem Fehler nachverfolgt wird: [Fehler beim Spark-Auftrag, wenn fs.defaultFS und die JAR-Datei der Anwendung unterschiedliche URLs aufweisen](https://issues.apache.org/jira/browse/SPARK-22587).

Das Problem wurde in Spark 2.3.0 gelöst.

## <a name="resolution"></a>Lösung

Stellen Sie sicher, dass die JAR-Datei der Anwendung im standardmäßigen/primären Speicher für den HDInsight-Cluster gespeichert wird. Stellen Sie bei Azure Data Factory sicher, dass der über ADF verknüpfte Dienst auf den HDInsight-Standardcontainer verweist, nicht auf einen sekundären Container.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]