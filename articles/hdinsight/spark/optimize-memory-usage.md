---
title: Optimieren der Speicherauslastung in Apache Spark – Azure HDInsight
description: Erfahren Sie, wie Sie die Speicherauslastung in Apache Spark in Azure HDInsight optimieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: ed10648fb115ae0dc12300f3e48db165fcb4c215
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028154"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Optimierung der Speicherauslastung für Apache Spark

In diesem Artikel wird erläutert, wie Sie die Speicherverwaltung Ihres Apache Spark-Clusters optimieren, um die optimale Leistung für Azure HDInsight zu erzielen.

## <a name="overview"></a>Übersicht

Spark platziert Daten im Arbeitsspeicher. Aus diesem Grund ist die Verwaltung von Arbeitsspeicherressourcen ein sehr wichtiger Aspekt beim Optimieren der Ausführung von Spark-Aufträgen.  Es gibt mehrere Verfahren, die Sie anwenden können, um den Arbeitsspeicher Ihres Clusters effizient zu nutzen.

* Legen Sie kleinere Datenpartitionen an, und berücksichtigen Sie in Ihrer Partitionierungsstrategie die Größe, den Typ und die Verteilung Ihrer Daten.
* Erwägen Sie die Verwendung der neueren, effizienteren [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) anstelle der standardmäßigen Java-Serialisierung.
* Verwenden Sie vorzugsweise YARN, da dies `spark-submit` nach Batches aufteilt.
* Überwachen und optimieren Sie die Spark-Konfigurationseinstellungen.

Die folgende Abbildung zeigt die Spark-Arbeitsspeicherstruktur und einige wichtige Arbeitsspeicherparameter für den Executor.

## <a name="spark-memory-considerations"></a>Überlegungen zum Spark-Arbeitsspeicher

Bei Verwendung von Apache Hadoop YARN steuert YARN den von allen Containern auf jedem Spark-Knoten verwendeten Gesamtarbeitsspeicher.  Das folgende Diagramm zeigt die wichtigsten Objekte und ihre Beziehungen.

![Übersicht über die YARN-Spark-Arbeitsspeicherverwaltung](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Um Meldungen zu unzureichendem Arbeitsspeicher zu beheben, versuchen Sie Folgendes:

* Überprüfen Sie die Shufflevorgänge in der DAG-Verwaltung. Verringern Sie die Datenmenge durch zuordnungsseitige Reduktion, partitionieren Sie Quelldaten vorab (oder legen Sie sie in Buckets ab), maximieren Sie einzelne Shufflevorgänge, und verringern Sie die Menge an gesendeten Daten.
* Verwenden Sie `ReduceByKey` mit festem Arbeitsspeicherlimit anstelle von `GroupByKey`, das Aggregationen, Fenstervorgänge und weitere Funktionen bietet, aber ein ungebundenes Arbeitsspeicherlimit aufweist.
* Verwenden Sie `TreeReduce`, welches einen größeren Teil der Verarbeitung in den Executors oder Partitionen ausführt, anstelle von `Reduce`, das die gesamte Arbeit im Treiber erledigt.
* Nutzen Sie eher DataFrames als die Low-Level-RDD-Objekte.
* Erstellen Sie ComplexTypes, die Aktionen kapseln, wie z.B. „Top N“, verschiedene Aggregationen oder Fenstervorgänge.

Informationen zu weiteren Schritten für die Problembehandlung finden Sie unter [OutOfMemoryError-Ausnahmen für Apache Spark in Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Nächste Schritte

* [Optimieren der Datenverarbeitung für Apache Spark](optimize-cluster-configuration.md)
* [Optimieren der Datenspeicherung für Apache Spark](optimize-data-storage.md)
* [Optimieren der Clusterkonfiguration für Apache Spark](optimize-cluster-configuration.md)
