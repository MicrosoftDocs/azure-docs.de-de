---
title: Optimieren der Datenverarbeitung für Apache Spark – Azure HDInsight
description: Erfahren Sie, wie Sie mit Azure HDInsight die effizientesten Vorgänge zur Verarbeitung Ihrer Daten unter Apache Spark auswählen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 3b85f6238bec4ef85d724a2fc48ea5988c3fceb2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031724"
---
# <a name="data-processing-optimization-for-apache-spark"></a>Optimierung der Datenverarbeitung für Apache Spark

In diesem Artikel wird erläutert, wie Sie die Konfiguration Ihres Apache Spark-Clusters optimieren, um die optimale Leistung für Azure HDInsight zu erzielen.

## <a name="overview"></a>Übersicht

Wenn Aufträge in einem Join oder Shuffle langsam sind, ist die Ursache dafür wahrscheinlich eine *Datenschiefe*. Hierbei handelt es sich um eine Asymmetrie in Ihren Auftragsdaten. Angenommen, ein Zuordnungsauftrag dauert 20 Sekunden. Die Ausführung eines Auftrags mit Datenjoins und -shuffles dauert hingegen mehrere Stunden. Zum Beheben der Datenschiefe sollten Sie Saltvorgänge für den gesamten Schlüssel anwenden oder einen *isolierten Salt* nur für eine Teilmenge der Schlüssel verwenden. Wenn Sie einen isolierten Salt verwenden, sollten Sie eine weitere Filterung anwenden, um die Teilmenge der Schlüssel mit Salts in Zuordnungsjoins zu isolieren. Eine andere Option besteht darin, eine Bucketspalte einzuführen und zuerst vorab eine Aggregation in den Buckets durchzuführen.

Ein weiterer Faktor, der Joins verlangsamen kann, ist möglicherweise der Jointyp. Standardmäßig verwendet Spark den Jointyp `SortMerge`. Diese Art des Joins eignet sich am besten für große Datasets, ist aber teuer in der Berechnung, weil hierbei vor dem Zusammenführen zuerst die linke und rechte Seite der Daten sortiert werden muss.

Ein `Broadcast`-Join eignet sich am besten für kleinere Datasets oder für Fälle, in denen die eine Seite des Joins wesentlich kleiner ist als die andere Seite. Dieser Jointyp übermittelt eine Seite per Broadcast an alle Executors und erfordert daher im Allgemeinen mehr Arbeitsspeicher für Broadcasts.

Sie können den Jointyp in Ihrer Konfiguration durch Einrichten von `spark.sql.autoBroadcastJoinThreshold` ändern oder mithilfe der Data Frame-APIs (`dataframe.join(broadcast(df2))`) einen Joinhinweis festlegen.

```scala
// Option 1
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 1*1024*1024*1024)

// Option 2
val df1 = spark.table("FactTableA")
val df2 = spark.table("dimMP")
df1.join(broadcast(df2), Seq("PK")).
    createOrReplaceTempView("V_JOIN")

sql("SELECT col1, col2 FROM V_JOIN")
```

Wenn Sie Tabellen mit Buckets verwenden, steht Ihnen ein dritter Jointyp zur Verfügung: der `Merge`-Join. Ein ordnungsgemäß vorab partitioniertes und vorab sortiertes Dataset überspringt die teure Sortierungsphase eines `SortMerge`-Joins.

Die Reihenfolge der Joins spielt eine Rolle, insbesondere in komplexeren Abfragen. Beginnen Sie mit den selektivsten Joins. Verschieben Sie darüber hinaus nach Möglichkeit Joins, die die Zeilenanzahl erhöhen, hinter die Aggregation.

Zum Verwalten der Parallelität von kartesischen Joins können Sie geschachtelte Strukturen oder Fenstervorgänge hinzufügen und möglicherweise einen oder mehrere Schritte in Ihrem Spark-Auftrag überspringen.

## <a name="optimize-job-execution"></a>Optimieren der Auftragsausführung

* Setzen Sie den Cache nach Bedarf ein. Wenn Sie Daten z.B. zweimal verwenden, speichern Sie sie zwischen.
* Übertragen Sie Variablen per Broadcast an alle Executors. Die Variablen werden nur einmal serialisiert, wodurch Lookupvorgänge beschleunigt werden.
* Verwenden Sie den Threadpool im Treiber, wodurch die Vorgänge für viele Tasks beschleunigt werden.

Überwachen Sie Ihre ausgeführten Aufträge regelmäßig auf Leistungsprobleme. Wenn Sie bei bestimmten Problemen genauere Einblicke benötigen, ziehen Sie eins der folgenden Tools für die Leistungsprofilerstellung in Betracht:

* [Intel PAL Tool](https://github.com/intel-hadoop/PAT) überwacht die Auslastung von CPU, Speicher und Netzwerkbandbreite.
* [Oracle Java 8 Mission Control](https://www.oracle.com/technetwork/java/javaseproducts/mission-control/java-mission-control-1998576.html) erstellt Profile für Spark- und Executorcode.

Entscheidend für die Abfrageleistung von Spark 2.x ist die Tungsten-Engine, die von der Codegenerierung für die gesamte Phase abhängig ist. In einigen Fällen ist die Codegenerierung für die gesamte Phase möglicherweise deaktiviert. Wenn Sie z.B. einen nicht veränderbaren Typ (`string`) im Aggregationsausdruck verwenden, wird `SortAggregate` anstelle von `HashAggregate` angezeigt. Um die Leistung zu verbessern, können Sie Folgendes versuchen und dann die Codegenerierung wieder aktivieren:

```sql
MAX(AMOUNT) -> MAX(cast(AMOUNT as DOUBLE))
```

## <a name="next-steps"></a>Nächste Schritte

* [Optimieren der Datenspeicherung für Apache Spark](optimize-data-storage.md)
* [Optimieren der Speicherauslastung für Apache Spark](optimize-memory-usage.md)
* [Optimieren der Clusterkonfiguration für Apache Spark](optimize-cluster-configuration.md)
