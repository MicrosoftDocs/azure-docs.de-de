---
title: Bedarfsorientiertes Extrahieren, Transformieren und Laden (ETL) – Azure HDInsight
description: Erfahren Sie, wie ETL in HDInsight mit Apache Hadoop verwendet wird.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: ashishth
ms.openlocfilehash: d3ce2a19e195174cd302b25005b3b063d028b6cf
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137168"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Bedarfsorientiertes Extrahieren, Transformieren und Laden (ETL)

Extrahieren, Transformieren und Laden (ETL) ist der Prozess, mit dem Daten aus verschiedenen Quellen abgerufen, an einem Standardspeicherort gesammelt, bereinigt und verarbeitet, und letztlich in einen Datenspeicher geladen werden, aus dem sie abgefragt werden können. Legacy-ETL-Prozesse importieren Daten, bereinigen Sie am Ursprungsort und speichern sie dann in einer relationalen Daten-Engine. Bei HDInsight unterstützt eine große Vielzahl von Apache Hadoop-Ökosystemkomponenten die Durchführung des bedarfsorientierten ETL. 

Die Verwendung von HDInsight im ETL-Prozess lässt sich in dieser folgenden Pipeline zusammenfassen:

![HDInsight-ETL-Übersicht](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

In den folgenden Abschnitten wird jede der ETL-Phasen mit den jeweils zugehörigen Komponenten eingehender untersucht.

## <a name="orchestration"></a>Orchestrierung

Orchestrierung erstreckt sich über alle Phasen der ETL-Pipeline. ETL-Aufträge in HDInsight umfassen häufig mehrere verschiedene Produkte, die zusammenarbeiten.  Sie können Hive verwenden, um einen Teil der Daten zu bereinigen, während Pig einen anderen Teil bereinigt.  Sie könnten Azure Data Factory verwenden, um Daten aus Azure Data Lake Store in Azure SQL-Datenbank zu laden.

Orchestrierung ist erforderlich, um den geeigneten Auftrag zum richtigen Zeitpunkt auszuführen.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie ist ein Koordinationssystem für Workflows zur Verwaltung von Hadoop-Aufträgen. Oozie wird innerhalb eines HDInsight-Clusters ausgeführt und ist in den Hadoop-Stapel integriert. Oozie unterstützt Hadoop-Aufträge für Apache Hadoop MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Oozie kann auch dazu verwendet werden, systemspezifische Aufträge zu planen, beispielsweise Java-Programme oder Shellskripts.

Weitere Informationen finden Sie unter [Verwenden von Apache Oozie mit Apache Hadoop zum Definieren und Ausführen eines Workflows in HDInsight](../hdinsight-use-oozie-linux-mac.md). Ausführliche Einblicke in die Verwendung von Oozie zum Nutzen einer End-to-End-Pipeline finden Sie unter [Operationalisieren einer Datenanalysepipeline](../hdinsight-operationalize-data-pipeline.md). 

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory bietet Orchestrierungsfunktionen als Platform-as-a-Service. Sie ist ein cloudbasierter Datenintegrationsdienst, mit dem Sie datengesteuerte Workflows in der Cloud erstellen können, um Datenverschiebungen und -transformationen zu orchestrieren und zu automatisieren. 

Mithilfe von Azure Data Factory können Sie:

1. Erstellen und Planen von datengesteuerten Workflows (sogenannte Pipelines), die Daten aus unterschiedlichen Datenspeichern erfassen.
2. Verarbeiten und Transformieren der Daten mithilfe von Computediensten wie Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics, Azure Batch und Azure Machine Learning.
3. Veröffentlichen von Ausgabedaten für Datenspeicher wie Azure SQL Data Warehouse für die Nutzung durch BI-Anwendungen (Business Intelligence)

Weitere Informationen zu Azure Data Factory finden Sie in der [Dokumentation](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Erfassungsdatei- und Ergebnisspeicher

Quelldatendateien werden in der Regel an einen Speicherort in Azure Storage oder Azure Data Lake Storage geladen. Dateien können in einem beliebigen Format sein, sind aber normalerweise Flatfiles wie CSV-Dateien. 

### <a name="azure-storage"></a>Azure Storage 

[Azure Storage](https://azure.microsoft.com/services/storage/blobs/) hat [spezifische Saklierbarkeitsziele](../../storage/common/storage-scalability-targets.md).  Für die meisten analytische Knoten lässt sich Azure Storage am besten skalieren, wenn viele kleinere Dateien verarbeitet werden.  Azure Storage garantiert dieselbe Leistung, unabhängig von der Anzahl und Größe der Dateien (sofern Sie sich innerhalb Ihrer Grenzwerte bewegen).  Dies bedeutet, dass Sie Terabytes von Daten speichern können und immer noch eine konsistente Leistung erhalten, egal ob Sie eine Teilmenge der Daten oder alle Daten verwenden.

Azure Storage verfügt über verschiedene Arten von Blobs.  Ein *Anfügeblob* ist eine großartige Möglichkeit zum Speichern von Webprotokollen oder Sensordaten.  

Mehrere Blobs können über mehrere Server verteilt werden, um den Zugriff darauf horizontal hochzuskalieren, aber ein einzelnes Blob kann nur von einem einzelnen Server bedient werden. Blobs können zwar logisch in Blob-Containern zusammengefasst werden, allerdings wirkt sich dies nicht auf die Partitionierung einer solchen Gruppierung aus.

Azure Storage verfügt außerdem über eine WebHDFS-API-Ebene für den Blob-Speicher.  Alle Dienste in HDInsight können auf Dateien in Azure Blob Storage zugreifen, um Daten zu bereinigen und zu verarbeiten, auf ähnliche Weise, wie diese Dienste HDFS-Dateien (Hadoop Distributed Files System) verwenden würden.

Daten werden in der Regel mithilfe der PowerShell, des Azure Storage SDK oder mit AZCopy in Azure Storage erfasst.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Store

Azure Data Lake Storage (ADLS) ist ein verwaltetes Repository mit Hyperskalierung für Analysedaten, das mit HDFS kompatibel ist.  ADLS verwendet ein Entwurfsparadigma, das HDFS ähnelt, und bietet unbegrenzte Skalierbarkeit hinsichtlich der Gesamtkapazität und der Größe einzelner Dateien. ADLS eignet sich besonders gut für die Arbeit mit großen Dateien, da eine große Datei auf mehreren Knoten gespeichert werden kann.  Das Partitionieren von Daten in ADLS erfolgt im Hintergrund.  Sie erhalten einen enormen Durchsatz, um Analyseaufträge mit Tausenden gleichzeitiger Executors durchführen können, die Hunderte Terabytes von Daten effizient lesen und schreiben.

Daten werden in ADLS in der Regel mithilfe von Azure Data Factory, ADLS SDKs, AdlCopy-Dienst, Apache DistCp oder Apache Sqoop erfasst.  Welchen dieser Dienste Sie verwenden sollten, hängt zum größten Teil davon ab, wo sich die Daten befinden.  Wenn die Daten aktuell in einem vorhandenen Hadoop-Cluster vorliegen, können Sie Apache DistCp, AdlCopy-Dienst oder Azure Data Factory verwenden.  Wenn sie sich in Azure Blob Storage befinden, können Sie das Azure Data Lake Storage .NET SDK, Azure PowerShell oder Azure Data Factory verwenden.

ADLS ist außerdem für die Ereigniserfassung mithilfe von Azure Event Hub oder Apache Storm optimiert.

#### <a name="considerations-for-both-storage-options"></a>Überlegungen zu beiden Speicheroptionen

Um Datasets in der Größenordnung von Terabytes hochzuladen, kann die Netzwerkwartezeit ein wesentliches Problem darstellen, insbesondere wenn die Daten von einem lokalen Speicherort stammen.  In solchen Fällen können Sie die folgenden Optionen verwenden:

* Azure ExpressRoute:  Mit Azure ExpressRoute können Sie private Verbindungen zwischen Azure-Datencentern und Ihrer lokalen Infrastruktur erstellen. Diese Verbindungen bieten eine zuverlässige Möglichkeit zur Übertragung großer Datenmengen. Weitere Informationen finden Sie in der [Dokumentation zu Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* Offline-Datenupload. Sie können über den [Azure Import/Export-Dienst](../../storage/common/storage-import-export-service.md) Festplattenlaufwerke mit Ihren Daten an ein Azure-Rechenzentrum senden. Ihre Daten werden zunächst in Azure Storage-Blobs hochgeladen. Anschließend können Sie mit [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) oder dem Tool [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md) Daten aus Azure Storage-Blobs in Data Lake Storage kopieren.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Azure SQL DW ist eine hervorragende Wahl, um bereinigte und vorbereitete Ergebnisse für zukünftige Analysen zu speichern.  Azure HDInsight kann verwendet werden, um diese Dienste für Azure SQL DW durchzuführen.

Azure SQL Data Warehouse (SQL DW) ist ein für analytische Workloads optimierter relationaler Datenbankspeicher.  Azure SQL DW lässt sich am besten bei partitionierten Tabellen skalieren.  Tabellen können über mehrere Knoten hinweg partitioniert werden.  Azure SQL DW-Knoten werden zum Zeitpunkt der Erstellung ausgewählt.  Sie können gemäß dem Fakt skaliert werden, aber dies ist ein aktiver Prozess, der möglicherweise das Verschieben von Daten erfordert. Weitere Informationen finden Sie unter [SQL Data Warehouse – Compute](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase ist ein in Azure HDInsight verfügbarer Schlüssel-Wert-Speicher.  Apache HBase ist eine Open-Source-NoSQL-Datenbank, die auf Hadoop basiert und nach dem Vorbild von Google BigTable erstellt wurde. HBase bietet leistungsfähigen, wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten in einer schemalosen Datenbank, die nach Spaltenfamilien gegliedert ist.

Daten werden in den Zeilen einer Tabelle gespeichert und die Daten in einer Zeile zu einer Spaltenfamilie zusammengefasst. HBase ist eine schemalose Datenbank in dem Sinne, dass weder die Spalten noch der Typ der darin gespeicherten Daten vor der Verwendung definiert werden müssen. Der Open-Source-Code lässt sich linear skalieren, sodass Petabytes von Daten auf Tausenden von Knoten verarbeitet werden können. HBase nutzt Datenredundanz, Stapelverarbeitung und andere Funktionen, die von verteilten Anwendungen im Hadoop-Ökosystem zur Verfügung gestellt werden.   

HBase ist ein ausgezeichnetes Ziel für Sensor- und Protokolldaten zur zukünftigen Analyse.

Die Skalierbarkeit von HBase hängt von der Anzahl der Knoten im HDInsight-Cluster ab.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL-Datenbank und Azure-Datenbank

Azure bietet drei verschiedene relationale Datenbanken als Platform-as-a-Service (PAAS).

* [Azure SQL-Datenbank](../../sql-database/sql-database-technical-overview.md) ist eine Implementierung von Microsoft SQL Server. Weitere Informationen zur Leistung finden Sie unter [Optimieren der Leistung bei Azure SQL-Datenbank](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) ist eine Implementierung von Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) ist eine Implementierung von PostgreSQL.

Diese Produkte skalieren zentral hoch, was bedeutet, dass sie durch Hinzufügen weiterer CPUs und von mehr Arbeitsspeicher skaliert werden.  Sie können sich auch entschließen, Premium-Datenträger mit den Produkten zu verwenden, um eine bessere E/A-Leistung zu erzielen.

## <a name="azure-analysis-services"></a>Azure Analysis Services 

Azure Analysis Services (AAS) ist eine analytische Daten-Engine, die zur Entscheidungsunterstützung und bei Geschäftsanalysen verwendet wird und die analytischen Daten für Geschäftsberichte und Clientanwendungen wie Power BI, Excel, Reporting Services-Berichte und andere Datenvisualisierungstools bereitstellt.

Analyse-Cubes lassen sich durch Ändern der Ebenen für jeden einzelnen Cube skalieren.  Weitere Informationen finden Sie unter [Azure Analysis Services – Preise](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Extrahieren und Laden

Sobald die Daten in Azure vorhanden sind, können Sie viele Dienste verwenden, um sie zu extrahieren und in andere Produkte zu laden.  HDInsight unterstützt Sqoop und Flume. 

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop ist ein Tool für die effiziente Datenübertragung zwischen strukturierten, halb strukturierten und unstrukturierten Datenquellen. 

Sqoop verwendet MapReduce zum Importieren und Exportieren der Daten, um Parallelbetrieb und Fehlertoleranz bereitzustellen.

### <a name="apache-flume"></a>Apache Flume

Apache Flume ist ein verteilter, zuverlässiger und verfügbarer Dienst für das effiziente Sammeln, Aggregieren und Verschieben großer Mengen von Protokolldaten. Flume hat eine einfache und flexible Architektur, die auf Streamingdatenflüssen basiert. Flume ist robust und fehlertolerant und besitzt einstellbare Zuverlässigkeitsmechanismen sowie zahlreiche Failover- und Wiederherstellungsmechanismen. Flume verwendet ein einfaches, erweiterbares Datenmodell, das analytische Onlineanwendungen zulässt.

Apache Flume kann nicht mit Azure HDInsight verwendet werden.  Eine lokale Hadoop-Installation kann Flume verwenden, um Daten an Azure Storage Blobs oder an Azure Data Lake Storage zu senden.  Weitere Informationen finden Sie unter [Verwenden von Apache Flume mit HDInsight](https://web.archive.org/web/20190217104751/ https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Transformieren

Sobald Daten am ausgewählten Speicherort vorhanden sind, müssen Sie sie bereinigen, kombinieren oder für ein bestimmtes Verwendungsmuster vorbereiten.  Hive, Pig und Spark SQL sind alle eine gute Wahl für diese Art von Arbeit.  Sie werden alle unter HDInsight unterstützt. 

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](hdinsight-use-pig.md)
* [Verwenden von Apache Hive als ETL-Tool](apache-hadoop-using-apache-hive-as-an-etl-tool.md) 
* [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
