---
title: 'Optimieren der Leistung: Hive, HDInsight und Azure Data Lake Storage Gen2 | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zu den Richtlinien zur Optimierung von E/A-intensive Abfragen unter Verwendung von Hive, HDInsight und Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912942"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Optimieren der Leistung: Hive, HDInsight und Azure Data Lake Storage Gen2

Die Standardeinstellungen bieten eine gute Leistung für viele verschiedene Anwendungsfälle.  Für E/A-intensive Abfragen kann Hive optimiert werden, um eine bessere Leistung mit Azure Data Lake Storage Gen2 zu erzielen.  

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Data Lake Storage Gen2-Konto**. Anweisungen zum Erstellen eines solchen Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](../common/storage-account-create.md)
* Einen **Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen2-Konto. Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
* **Ausführung von Hive in HDInsight**.  Weitere Informationen zum Ausführen von Hive-Aufträgen in HDInsight finden Sie unter [Verwenden von Hive in HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md).
* **Leitlinien für die Leistungsoptimierung von Data Lake Storage Gen2**.  Allgemeine Leistungskonzepte finden Sie unter [Leitfaden für die Leistungsoptimierung von Data Lake Storage Gen2](data-lake-storage-performance-tuning-guidance.md).

## <a name="parameters"></a>Parameter

Im Folgenden finden Sie die wichtigsten Einstellungen für die Optimierung der Data Lake Storage Gen2-Leistung:

* **hive.tez.container.size** – die von jedem Task verwendete Arbeitsspeichermenge

* **tez.grouping.min-size** – die Mindestgröße jedes Mappers

* **tez.grouping.max-size** – die maximale Größe jedes Mappers

* **hive.exec.reducer.bytes.per.reducer** – die Größe jedes Reducers

**hive.tez.container.size**: Die Containergröße bestimmt die Menge an Arbeitsspeicher, die für jeden Task verfügbar ist.  Dies ist die wichtigste Information für die Steuerung der Parallelität in Hive.  

**tez.grouping.min-size**: Dieser Parameter ermöglicht es Ihnen, die Mindestgröße jedes Mappers festzulegen.  Wenn die Anzahl von Mappern, die Tez auswählt, kleiner ist als der Wert dieses Parameters, verwendet Tez den hier festgelegten Wert.

**tez.grouping.max-size**: Dieser Parameter ermöglicht es Ihnen, die maximale Größe jedes Mappers festzulegen.  Wenn die Anzahl von Mappern, die Tez auswählt, größer ist als der Wert dieses Parameters, verwendet Tez den hier festgelegten Wert.

**hive.exec.reducer.bytes.per.reducer**: Dieser Parameter legt die Größe jedes Reducers fest.  Standardmäßig ist jeder Reducer 256 MB groß.  

## <a name="guidance"></a>Anleitungen

**Set hive.exec.reducer.bytes.per.reducer**: Der Standardwert funktioniert gut, wenn die Daten nicht komprimiert sind.  Bei komprimierten Daten sollten Sie den Reducer verkleinern.  

**Set hive.tez.container.size**: Der Arbeitsspeicher wird in jedem Knoten von „yarn.nodemanager.resource.memory-mb“ angegeben und sollte im HDI-Cluster standardmäßig richtig festgelegt sein.  Weitere Informationen zum Festlegen des geeigneten Speichers in YARN finden Sie in diesem [Beitrag](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

E/A-intensive Workloads können von einem höheren Maß an Parallelität profitieren, indem Sie die Größe der Tez-Container reduzieren. So können Benutzer mehr Container verwenden, wodurch die Parallelität verbessert wird.  Einige Hive-Abfragen erfordern sehr viel Arbeitsspeicher (z.B. MapJoin).  Wenn der Task nicht über genügend Arbeitsspeicher verfügt, wird zur Laufzeit eine Ausnahme wegen unzureichenden Arbeitsspeichers angezeigt.  Wenn Sie eine solche Ausnahme erhalten, sollten Sie den Arbeitsspeicher vergrößern.   

Die Anzahl von gleichzeitigen Tasks bzw. die Parallelität wird durch die Gesamtmenge an YARN-Arbeitsspeicher begrenzt.  Die Anzahl von YARN-Containern bestimmt, wie viele gleichzeitige Tasks ausgeführt werden können.  Die Menge an YARN-Arbeitsspeicher pro Knoten finden Sie bei Ambari.  Navigieren Sie zu YARN, und zeigen Sie die Registerkarte für die Konfiguration an.  Die Größe des YARN-Arbeitsspeichers wird in diesem Fenster angezeigt.  

- YARN-Arbeitsspeicher gesamt = Knoten * YARN-Arbeitsspeicher pro Knoten
- \# der YARN-Container = YARN-Arbeitsspeicher gesamt / Tez-Containergröße

Entscheidend für die Verbesserung der Leistung mit Data Lake Storage Gen2 ist es, die Parallelität so weit wie möglich zu erhöhen.  Tez berechnet automatisch die Anzahl von Tasks, die erstellt werden müssen – darum müssen Sie sich nicht kümmern.   

## <a name="example-calculation"></a>Beispielberechnung

Angenommen, Sie haben einen D14-Cluster mit 8 Knoten.  

- YARN-Arbeitsspeicher gesamt = Knoten * YARN-Arbeitsspeicher pro Knoten
- YARN-Arbeitsspeicher gesamt = 8 Knoten * 96 GB = 768 GB
- \# der YARN-Container = 768 GB / 3072 MB = 256

## <a name="further-information-on-hive-tuning"></a>Weitere Informationen zur Hive-Optimierung

Diese Blogs können Sie bei der Optimierung Ihrer Hive-Abfragen unterstützen:
* [Optimize Hive queries for Hadoop in HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md) (Optimieren von Hive-Abfragen für Hadoop in HDInsight)
* [Optimieren von Apache Hive-Abfragen in Azure HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Ignite talk on optimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25) (Diskussion über die Optimierung von Hive in HDInsight)