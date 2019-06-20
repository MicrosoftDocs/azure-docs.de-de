---
title: Leitfaden für die Optimierung der Leistung von Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Leitfaden für die Optimierung der Leistung von Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 6f831dd0cde4641eb48f3c23e010f8c5e8aa3fa2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939356"
---
# <a name="tuning-azure-data-lake-storage-gen2-for-performance"></a>Optimieren der Leistung von Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 unterstützt hohe Durchsätze für E/A-intensive Analysen und Datenverschiebungen.  In Data Lake Storage Gen2 ist es wichtig, den gesamten verfügbaren Durchsatz zu nutzen, um eine optimale Leistung zu erzielen. Der verfügbare Durchsatz bezieht sich hierbei auf die Menge an Daten, die pro Sekunde gelesen oder geschrieben werden können.  Dies wird erreicht, indem möglichst viele Lese- und Schreibvorgänge parallel ausgeführt werden.

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/throughput.png)

Data Lake Storage Gen2 kann skaliert werden, um den erforderlichen Durchsatz für sämtliche Analyseszenarien bereitzustellen. Standardmäßig bietet ein Data Lake Storage Gen2-Konto automatisch genügend Durchsatz, um die Anforderungen eines breiten Spektrums an Anwendungsfällen zu erfüllen. Sollten Kunden das Standardlimit erreichen, kann das Data Lake Storage Gen2-Konto für die Bereitstellung eines höheren Durchsatzes konfiguriert werden. Wenden Sie sich hierzu an den [Azure-Support](https://azure.microsoft.com/support/faq/).

## <a name="data-ingestion"></a>Datenerfassung

Bei der Erfassung von Daten aus einem Quellsystem in Data Lake Storage Gen2 ist zu berücksichtigen, dass es bei der Quellhardware, bei der Quellnetzwerkhardware und bei der Netzwerkkonnektivität mit Data Lake Storage Gen2 zu Engpässen kommen kann.  

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/bottleneck.png)

Es ist unbedingt sicherzustellen, dass die Datenverschiebung durch diese Faktoren nicht beeinträchtigt wird.

### <a name="source-hardware"></a>Quellhardware

Sie sollten die entsprechende Hardware sorgfältig auswählen, unabhängig davon, ob Sie mit lokalen Computern oder VMs in Azure arbeiten. Bei Quelldatenträgerhardware wird empfohlen, SSDs gegenüber HDDs vorzuziehen und Datenträgerhardware mit schnelleren Spindeln zu wählen. Verwenden Sie für Quellnetzwerkhardware die schnellstmöglichen Netzwerkkarten.  Für Azure werden Azure D14-VMs mit entsprechend leistungsstarker Datenträger- und Netzwerkhardware empfohlen.

### <a name="network-connectivity-to-data-lake-storage-gen2"></a>Netzwerkkonnektivität mit Data Lake Storage Gen2

Bei der Netzwerkkonnektivität zwischen Ihren Quelldaten und Data Lake Storage Gen2 kann es gelegentlich zu Engpässen kommen. Wenn Ihre Quelldaten lokal gespeichert sind, ziehen Sie die Verwendung einer dedizierten Verknüpfung mit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) in Erwägung. Sind Ihre Quelldaten in Azure enthalten, wird eine optimale Leistung erzielt, wenn sich die Daten in der Azure-Region befinden wie das Data Lake Storage Gen2-Konto.

### <a name="configure-data-ingestion-tools-for-maximum-parallelization"></a>Konfigurieren von Datenerfassungstools für maximale Parallelisierung

Nachdem Sie die oben genannten Engpässe bei der Quellhardware und der Netzwerkkonnektivität behoben haben, können Sie nun Ihre Erfassungstools konfigurieren. In der folgenden Tabelle werden wichtige Einstellungen für verschiedene gängige Erfassungstools zusammengefasst und ausführliche Artikel zu ihrer Leistungsoptimierung angegeben.  Weitere Informationen darüber, welches Tool für Ihr Szenario geeignet ist, finden Sie in diesem [Artikel](data-lake-storage-data-scenarios.md).

| Tool               | Einstellungen     | Weitere Informationen                                                                 |
|--------------------|------------------------------------------------------|------------------------------|
| DistCp            | -m (Mapper)   | [Link](data-lake-storage-use-distcp.md#performance-considerations-while-using-distcp)                             |
| Azure Data Factory| parallelCopies    | [Link](../../data-factory/copy-activity-performance.md)                          |
| Sqoop           | fs.azure.block.size, -m (Mapper)    |   [Link](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)        |

## <a name="structure-your-data-set"></a>Strukturieren Ihres Datasets

Beim Speichern von Daten in Data Lake Storage Gen2 sind für die Leistung die Dateigröße, die Anzahl der Dateien und die Ordnerstruktur ausschlaggebend.  Im folgenden Abschnitt werden bewährte Methoden für diese Bereiche beschrieben.  

### <a name="file-size"></a>Dateigröße

In der Regel erzeugen Analyse-Engines wie HDInsight und Azure Data Lake Analytics Overhead pro Datei. Wenn Sie Ihre Daten in zahlreichen kleinen Dateien speichern, kann sich dies negativ auf die Leistung auswirken. Zur Verbesserung der Leistung empfiehlt es sich grundsätzlich, Daten in größeren Dateien (256 MB bis 100 GB) zu organisieren. Dateien mit einer Größe von mehr als 100 GB können von einigen Engines und Anwendungen unter Umständen nicht effizient verarbeitet werden.

Mitunter können Datenpipelines Rohdaten mit vielen kleinen Dateien nur begrenzt nutzen. Es wird empfohlen, bei der Erstellung größere Dateien für die Verwendung bei Downstreamanwendungen zu generieren.

### <a name="organizing-time-series-data-in-folders"></a>Organisieren von Zeitreihendaten in Ordnern

Bei Hive-Workloads kann durch Partition Pruning von Zeitreihendaten bewirkt werden, dass einige Abfragen nur eine Teilmenge der Daten lesen. Hierdurch wird die Leistung verbessert.    

Diese Pipelines, die Zeitreihendaten erfassen, versehen ihre Dateien oftmals mit einer äußerst strukturierten Datei- und Ordnerbenennung. Nachfolgend wird ein sehr gängiges Beispiel für Daten vorgestellt, die nach Datum strukturiert sind:

    \DataSet\YYYY\MM\DD\datafile_YYYY_MM_DD.tsv

Beachten Sie, dass Informationen zu Datum/Uhrzeit sowohl im Ordnernamen als auch im Dateinamen angegeben werden.

Im Folgenden wird ein allgemeines Muster für Datums- und Uhrzeitangaben vorgestellt:

    \DataSet\YYYY\MM\DD\HH\mm\datafile_YYYY_MM_DD_HH_mm.tsv

Auch hier sollte die Wahl, die Sie bei der Ordner- und Dateiorganisation treffen, für größere Dateien und eine angemessene Anzahl von Dateien in den einzelnen Ordnern optimiert sein.

## <a name="optimizing-io-intensive-jobs-on-hadoop-and-spark-workloads-on-hdinsight"></a>Optimieren von E/A-intensiven Aufträgen bei Hadoop- und Spark-Workloads für HDInsight

Aufträge lassen sich in einer der folgenden drei Kategorien unterteilen:

* **CPU-intensive Aufträge**:  Diese Aufträge weisen lange Computezeiten mit minimalen E/A-Zeiten auf.  Hierzu zählen beispielsweise Machine Learning-Aufträge und Aufträge für die Verarbeitung natürlicher Sprache.  
* **Speicherintensive Aufträge**:  Solche Aufträge belegen viel Speicher,  z.B. PageRank- und Echtzeitanalyseaufträge.  
* **E/A-intensive Aufträge**:  Bei diesen Aufträgen wird der Großteil der Zeit für E/A-Vorgänge beansprucht.  Ein gängiges Beispiel ist ein Kopierauftrag, bei dem nicht nur Lesevorgänge, sondern auch Schreibvorgänge durchgeführt werden.  Ein weiteres Beispiel sind Datenvorbereitungsaufträge, die eine große Menge an Daten lesen, Datentransformationen durchführen und die Daten dann wieder in den Speicher schreiben.  

Die folgende Anleitung gilt nur für E/A-intensive Aufträge.

## <a name="general-considerations"></a>Allgemeine Hinweise

Sie können zwar Aufträge verwenden, die bis zu 100 MB in einem einzelnen Vorgang lesen oder schreiben, ein Puffer dieser Größe beeinträchtigt jedoch unter Umständen die Leistung.
Verwenden Sie daher nach Möglichkeit E/A-Vorgänge mit einer Größe zwischen 4 MB und 16 MB, um die Leistung zu optimieren.

### <a name="general-considerations-for-an-hdinsight-cluster"></a>Allgemeine Überlegungen zu HDInsight-Clustern

* **HDInsight-Versionen**: Um eine optimale Leistung zu erzielen, verwenden Sie die neueste Version von HDInsight.
* **Regionen**: Platzieren Sie das Data Lake Storage Gen2-Konto in der gleichen Region wie den HDInsight-Cluster.  

Ein HDInsight-Cluster besteht aus zwei Hauptknoten und einigen Workerknoten. Jeder Workerknoten stellt eine bestimmte Anzahl von Kernen und eine bestimmte Menge an Speicher bereit, die durch den VM-Typ festgelegt wird.  Bei der Ausführung eines Auftrags ist YARN der Verhandlungspartner für Ressourcen, der den verfügbaren Speicher und die Kerne zur Erstellung von Containern zuordnet.  Jeder Container führt die für den Auftrag erforderlichen Aufgaben durch.  Zur schnellen Verarbeitung von Aufgaben werden Container parallel ausgeführt. Aus diesem Grund wird eine bessere Leistung erzielt, indem Sie so viele Container wie möglich parallel ausführen.

Es gibt drei Ebenen in einem HDInsight-Cluster, die sich optimieren lassen, um die Anzahl von Containern zu erhöhen und den gesamten verfügbaren Durchsatz zu nutzen.  

* **Physische Ebene**
* **YARN-Ebene**
* **Workloadebene**

### <a name="physical-layer"></a>Physische Ebene

**Führen Sie Cluster mit einer größeren Anzahl von Knoten und/oder größeren VMs aus.**  Bei einem größeren Cluster können Sie mehr YARN-Container ausführen, wie in der folgenden Abbildung gezeigt wird.

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/VM.png)

**Verwenden Sie VMs mit mehr Netzwerkbandbreite.**  Ist die Netzwerkbandbreite kleiner als der Data Lake Storage Gen2-Durchsatz, kann dies zu einem Engpass führen.  Die Menge der Netzwerkbandbreite variiert je nach VM.  Wählen Sie einen VM-Typ, der die größtmögliche Netzwerkbandbreite aufweist.

### <a name="yarn-layer"></a>YARN-Ebene

**Verwenden Sie kleinere YARN-Container.**  Reduzieren Sie die Größe der einzelnen YARN-Container, um mit derselben Menge an Ressourcen mehr Container zu erstellen.

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/small-containers.png)

Abhängig von Ihrer Workload ist stets eine Mindestgröße für YARN-Container erforderlich. Wenn Sie einen zu kleinen Container auswählen, treten bei Ihren Aufträgen Probleme aufgrund von unzureichendem Speicherplatz auf. In der Regel dürfen YARN-Container nicht kleiner als 1 GB sein. Üblich sind YARN-Container mit 3 GB. Für einige Workloads benötigen Sie möglicherweise größere YARN-Container.  

**Erhöhen Sie die Anzahl der Kerne pro YARN-Container.**  Erhöhen Sie die Anzahl der Kerne pro Container, um die Anzahl der Aufgaben, die in den einzelnen Containern parallel ausgeführt werden, zu erhöhen.  Dies gilt für Anwendungen wie Spark, die mehrere Aufgaben pro Container ausführt.  Bei Anwendungen wie Hive, die einen einzelnen Thread in jedem Container ausführt, empfiehlt es sich, die Anzahl der Container statt der Anzahl der Kerne pro Container zu erhöhen.

### <a name="workload-layer"></a>Workloadebene

**Verwenden Sie alle verfügbaren Container.**  Legen Sie die Anzahl von Aufgaben auf dieselbe oder eine höhere Anzahl der verfügbaren Containern fest, damit alle Ressourcen ausgelastet sind.

![Leistung von Data Lake Storage Gen2](./media/data-lake-storage-performance-tuning-guidance/use-containers.png)

**Aufgaben, die mit Fehlern beendet werden, sind kostspielig.** Sind bei jeder Aufgabe große Mengen an Daten zu verarbeiten, führen fehlerhafte Aufgaben zu einer kostenintensiven Wiederholung.  Aus diesem Grund empfiehlt es sich, mehr Aufgaben zu erstellen, bei denen jeweils eine kleine Menge von Daten verarbeitet wird.

Neben den oben genannten allgemeinen Richtlinien stehen in jeder Anwendung verschiedene Parameter zur Verfügung, die sich für die jeweilige Anwendung optimieren lassen. In der folgenden Tabelle werden einige dieser Parameter und Links aufgeführt, um sich mit der Leistungsoptimierung der einzelnen Anwendungen vertraut zu machen.

| Workload | Parameter zum Festlegen von Aufgaben |
|----------|------------------------|
| [Spark in HDInsight](data-lake-storage-performance-tuning-spark.md) | <ul><li>Num-executors</li><li>Executor-memory</li><li>Executor-cores</li></ul> |
| [Hive in HDInsight](data-lake-storage-performance-tuning-hive.md) | <ul><li>hive.tez.container.size</li></ul> |
| [MapReduce in HDInsight](data-lake-storage-performance-tuning-mapreduce.md) | <ul><li>Mapreduce.map.memory</li><li>Mapreduce.job.maps</li><li>Mapreduce.reduce.memory</li><li>Mapreduce.job.reduces</li></ul> |
| [Storm in HDInsight](data-lake-storage-performance-tuning-storm.md)| <ul><li>Anzahl von Workerprozessen</li><li>Anzahl von Spout Executor-Instanzen</li><li>Anzahl von Bolt Executor-Instanzen </li><li>Anzahl von Spout-Aufgaben</li><li>Anzahl von Bolt-Aufgaben</li></ul>|

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
