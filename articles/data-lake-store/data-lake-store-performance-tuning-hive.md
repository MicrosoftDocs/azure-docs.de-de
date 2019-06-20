---
title: Leitlinien für die Optimierung der Leistung von Azure Data Lake Storage Gen1 Hive | Microsoft Docs
description: Leitlinien für die Optimierung der Leistung von Azure Data Lake Storage Gen1 Hive
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61437275"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Anleitung für die Leistungsoptimierung für Hive in HDInsight und Azure Data Lake Storage Gen1

Die Standardeinstellungen bieten eine gute Leistung für viele verschiedene Anwendungsfälle.  Für E/A-intensive Abfragen kann Hive optimiert werden, um eine bessere Leistung mit Azure Data Lake Storage Gen1 zu bieten.  

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Data Lake Storage Gen1-Konto.** Eine Anleitung zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Ein Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen1-Konto. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.
* **Ausführung von Hive in HDInsight**.  Weitere Informationen zum Ausführen von Hive-Aufträgen in HDInsight finden Sie unter [Verwenden von Hive in HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive).
* **Richtlinien für die Leistungsoptimierung von Data Lake Storage Gen1**.  Allgemeine Leistungskonzepte finden Sie unter [Anleitung für die Leistungsoptimierung von Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).

## <a name="parameters"></a>Parameter

Im Folgenden finden Sie die wichtigsten Einstellungen für die Optimierung der Data Lake Storage Gen1-Leistung:

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

**Set hive.tez.container.size**: Der Arbeitsspeicher wird in jedem Knoten von „yarn.nodemanager.resource.memory-mb“ angegeben und sollte im HDI-Cluster standardmäßig richtig festgelegt sein.  Weitere Informationen zum Festlegen des geeigneten Speichers in YARN finden Sie in diesem [Beitrag](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

E/A-intensive Workloads können von einem höheren Maß an Parallelität profitieren, indem Sie die Größe der Tez-Container reduzieren. So können Benutzer mehr Container verwenden, wodurch die Parallelität verbessert wird.  Einige Hive-Abfragen erfordern sehr viel Arbeitsspeicher (z.B. MapJoin).  Wenn der Task nicht über genügend Arbeitsspeicher verfügt, wird zur Laufzeit eine Ausnahme wegen unzureichenden Arbeitsspeichers angezeigt.  Wenn Sie eine solche Ausnahme erhalten, sollten Sie den Arbeitsspeicher vergrößern.   

Die Anzahl von gleichzeitigen Tasks bzw. die Parallelität wird durch die Gesamtmenge an YARN-Arbeitsspeicher begrenzt.  Die Anzahl von YARN-Containern bestimmt, wie viele gleichzeitige Tasks ausgeführt werden können.  Die Menge an YARN-Arbeitsspeicher pro Knoten finden Sie bei Ambari.  Navigieren Sie zu YARN, und zeigen Sie die Registerkarte für die Konfiguration an.  Die Größe des YARN-Arbeitsspeichers wird in diesem Fenster angezeigt.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Entscheidend für die Verbesserung der Leistung bei Data Lake Storage Gen1 ist es, die Parallelität so weit wie möglich zu erhöhen.  Tez berechnet automatisch die Anzahl von Tasks, die erstellt werden müssen – darum müssen Sie sich nicht kümmern.   

## <a name="example-calculation"></a>Beispielberechnung

Angenommen, Sie haben einen D14-Cluster mit 8 Knoten.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Einschränkungen

**Data Lake Storage Gen1-Einschränkung** 

Wenn Sie die Data Lake Storage Gen1-Grenzwerte für die Bandbreite erreichen, treten Aufgabenfehler auf. Dies lässt sich durch Beobachten der Drosselungsfehler in den Taskprotokollen ermitteln.  Sie können die Parallelität verringern, indem Sie die Tez-Containergröße erhöhen.  Wenn Sie für einen Auftrag mehr Parallelität benötigen, wenden Sie sich an uns.

Um zu prüfen, ob eine Drosselung vorliegt, müssen Sie die Debugprotokollierung auf Clientseite aktivieren. Gehen Sie hierzu wie folgt vor:

1. Fügen Sie folgende Eigenschaft in die log4j-Eigenschaften in der Hive-Konfigurationsdatei ein. Dies kann über die Ambari-Ansicht erfolgen: log4j.logger.com.microsoft.azure.datalake.store=DEBUG. Starten Sie alle Knoten und Dienste neu, damit die Konfiguration wirksam wird.

2. Wenn eine Drosselung vorliegt, wird in der Hive-Protokolldatei ein HTTP 429-Fehlercode angezeigt. Die Hive-Protokolldatei befindet sich hier: /tmp/&lt;Benutzer&gt;/hive.log.

## <a name="further-information-on-hive-tuning"></a>Weitere Informationen zur Hive-Optimierung

Diese Blogs können Sie bei der Optimierung Ihrer Hive-Abfragen unterstützen:
* [Optimize Hive queries for Hadoop in HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/) (Optimieren von Hive-Abfragen für Hadoop in HDInsight)
* [Troubleshooting Hive query performance](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/) (Behandlung von Problemen mit der Hive-Abfrageleistung)
* [Ignite talk on optimize Hive on HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25) (Diskussion über die Optimierung von Hive in HDInsight)
