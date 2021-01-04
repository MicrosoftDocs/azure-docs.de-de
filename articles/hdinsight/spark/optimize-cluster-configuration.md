---
title: Optimieren der Apache Spark-Clusterkonfiguration – Azure HDInsight
description: Erfahren Sie, wie Sie Ihren Apache Spark-Cluster konfigurieren, um den Durchsatz für Azure HDInsight zu maximieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 80abc052bc8fa731a1cea0d3ecb523aedb46cf05
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031780"
---
# <a name="cluster-configuration-optimization-for-apache-spark"></a>Optimierung der Clusterkonfiguration für Apache Spark

In diesem Artikel wird erläutert, wie Sie die Konfiguration Ihres Apache Spark-Clusters optimieren, um die optimale Leistung für Azure HDInsight zu erzielen.

## <a name="overview"></a>Übersicht

Je nach Größe Ihrer Spark-Clusterworkload stellen Sie möglicherweise fest, dass Sie mit einer nicht standardmäßigen Spark-Konfiguration eine Optimierung der Spark-Auftragsausführung erzielen könnten.  Führen Sie Benchmarktests mit Beispielworkloads durch, um verschiedene nicht standardmäßige Clusterkonfigurationen zu überprüfen.

Hier finden Sie einige allgemeine Parameter, die Sie anpassen können:

|Parameter |BESCHREIBUNG |
|---|---|
|--num-executors|Legt die angemessene Anzahl von Executors fest.|
|--executor-cores|Legt die Anzahl von Kernen für jeden Executor fest. In der Regel sollten Sie Executors mittlerer Größe einsetzen, da andere Prozesse einen Teil des verfügbaren Arbeitsspeicherplatzes nutzen.|
|--executor-memory|Legt die Arbeitsspeichergröße für jeden Executor fest, wodurch die Heapgröße in YARN gesteuert wird. Reservieren Sie eine bestimmte Menge an Arbeitsspeicher für den Ausführungsoverhead.|

## <a name="select-the-correct-executor-size"></a>Auswählen der richtigen Executorgröße

Wenn Sie über die Executorkonfiguration entscheiden, ziehen Sie den Overhead für die Java-Garbage Collection (GC) in Betracht.

* Folgende Faktoren reduzieren die Größe eines Executors:
    1. Reduzieren Sie die Heapgröße auf unter 32 GB, um den GC-Overhead unter 10 % zu halten.
    2. Reduzieren Sie die Anzahl von Kernen, um den GC-Overhead unter 10 % zu halten.

* Folgende Faktoren erhöhen die Größe eines Executors:
    1. Reduzieren Sie die Kommunikation zwischen Executors.
    2. Reduzieren Sie die Anzahl offener Verbindungen zwischen Executors (N2) in größeren Clustern (über 100 Executors).
    3. Erhöhen Sie die Heapgröße, um genügend Platz für arbeitsspeicherintensive Tasks zu haben.
    4. Optional: Reduzieren Sie den Arbeitsspeicheroverhead pro Executor.
    5. Optional: Erhöhen Sie die Auslastung und Parallelität durch Überabonnierung der CPU.

Bei der Auswahl der Executorgröße gelten folgende allgemeine Regeln:

1. Beginnen Sie mit 30 GB pro Executor, und verteilen Sie verfügbare Computerkerne.
2. Erhöhen Sie die Anzahl von Executorkernen für größere Cluster (über 100 Executors).
3. Ändern Sie die Größe anhand von Testausführungen und den oben genannten Faktoren wie z.B. GC-Overhead.

Berücksichtigen Sie beim Ausführen paralleler Abfragen folgende Aspekte:

1. Beginnen Sie mit 30 GB pro Executor und allen Computerkernen.
2. Erstellen Sie durch Überabonnierung der CPU mehrere parallele Spark-Anwendungen (etwa 30 % verbesserte Latenz).
3. Verteilen Sie Abfragen auf parallele Anwendungen.
4. Ändern Sie die Größe anhand von Testausführungen und den oben genannten Faktoren wie z.B. GC-Overhead.

Weitere Informationen zur Verwendung von Ambari zum Konfigurieren von Executors finden Sie unter [Apache Spark-Einstellungen – Spark-Executors](apache-spark-settings.md#configuring-spark-executors).

Überwachen Sie die Abfrageleistung auf Ausreißer oder andere Leistungsprobleme, indem Sie die Zeitachsenansicht untersuchen. Betrachten Sie auch das SQL-Diagramm, die Auftragsstatistiken usw. Informationen zum Debuggen von Spark-Aufträgen mithilfe von YARN und dem Spark-Verlaufsserver finden Sie unter [Debuggen von Apache Spark-Aufträgen, die in HDInsight ausgeführt werden](apache-spark-job-debugging.md). Tipps zur Verwendung von YARN Timeline Server finden Sie unter [Zugreifen auf Apache Hadoop YARN-Anwendungsprotokolle](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Zuweilen sind einige Executors langsamer als die anderen, und die Ausführung der Tasks dauert wesentlich länger. Dies kommt häufig in größeren Clustern vor (über 30 Knoten). Teilen Sie in diesem Fall die Arbeit auf eine größere Anzahl von Tasks auf, sodass der Taskplaner langsame Tasks kompensieren kann. In der Anwendung sollten z.B. mindestens doppelt so viel Tasks wie Executorkerne vorhanden sein. Sie können auch mit `conf: spark.speculation = true` die spekulative Ausführung von Tasks aktivieren.

## <a name="next-steps"></a>Nächste Schritte

* [Optimieren der Datenverarbeitung für Apache Spark](optimize-cluster-configuration.md)
* [Optimieren der Datenspeicherung für Apache Spark](optimize-data-storage.md)
* [Optimieren der Speicherauslastung für Apache Spark](optimize-memory-usage.md)
