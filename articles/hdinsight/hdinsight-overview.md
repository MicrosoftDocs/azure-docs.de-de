---
title: Was ist Azure HDInsight?
description: Eine Einführung in HDInsight und den Apache Hadoop- und Apache Spark-Technologiestapel sowie die zugehörigen Komponenten wie Kafka, Hive, Storm und HBase für Big Data-Analysen.
ms.service: hdinsight
ms.topic: overview
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: d1c32bf749850ac40e23c1a9cb9c5cd7755d45c6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939438"
---
# <a name="what-is-azure-hdinsight"></a>Worum handelt es sich bei Azure HDInsight?

Azure HDInsight ist ein umfassender, verwalteter Open-Source-Analysedienst in der Cloud für Unternehmen. Sie können Open Source-Frameworks wie Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R und andere verwenden.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Was sind HDInsight und der Hadoop-Technologiestapel?

Azure HDInsight ist eine Clouddistribution von Hadoop-Komponenten. Azure HDInsight ermöglicht die einfache, schnelle und kostengünstige Verarbeitung umfangreicher Datenmengen. Sie können die beliebtesten Open-Source-Frameworks wie Hadoop, Spark, Hive, LLAP, Kafka, Storm, R usw. verwenden. Mit diesen Frameworks können Sie einen weiten Bereich von Szenarien ermöglichen, z.B. Extrahieren, Transformieren und Laden (ETL), Data Warehousing, Machine Learning und IoT.

Informationen zu verfügbare Komponenten des Hadoop-Technologiestapels für HDInsight finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](./hdinsight-component-versioning.md). Weitere Informationen zu Hadoop in HDInsight finden Sie auf der Seite mit [Azure-Features für HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Was versteht man unter "Big Data"?

Große Datenmengen, also „Big Data“, werden in immer größeren Mengen, mit immer höherer Geschwindigkeit und in immer mehr Formaten als jemals zuvor erfasst. Dabei kann es sich um Verlaufsdaten (also gespeicherte Daten) oder um Echtzeitdaten (von der Quelle gestreamt) handeln. Informationen zu den gängigsten Anwendungsfällen für Big Data finden Sie unter [Verwendungsszenarien für HDInsight](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-azure-hdinsight"></a>Argumente für die Verwendung von Azure HDInsight

In diesem Abschnitt werden die Funktionen von Azure HDInsight aufgeführt.

|Funktion  |BESCHREIBUNG  |
|---------|---------|
|Cloudbasiert     |     Mit Azure HDInsight können Sie optimierte Cluster für Hadoop, Spark, [Interactive Query (LLAP)](./interactive-query/apache-interactive-query-get-started.md), Kafka, Storm, HBase und ML Services in Azure erstellen. Darüber hinaus bietet HDInsight eine End-to-End-SLA für alle Ihre Produktionsworkloads.  |
|Kostengünstig und skalierbar     | Mit HDInsight können Sie Workloads hoch- und herunterskalieren. Durch das Erstellen bedarfsgesteuerter Cluster können Sie Ihre Kosten senken, indem Sie nur für das bezahlen, was Sie tatsächlich nutzen. Außerdem können Sie Datenpipelines für die Operationalisierung Ihrer Aufträge erstellen. Die Entkoppelung von Compute und Speicher sorgt für bessere Leistung und mehr Flexibilität. |
|Sicher und konform    | Mit HDInsight können Sie die Datenressourcen Ihres Unternehmens durch die Verwendung von Azure Virtual Network, Verschlüsselung und Integration von Azure Active Directory schützen. Darüber hinaus erfüllt HDInsight die gängigsten branchen- und behördenspezifischen Compliancestandards.        |
|Überwachung    | Dank der Integration mit Azure Monitor-Protokollen bietet Azure HDInsight eine zentrale Oberfläche für die Überwachung Ihrer gesamten Cluster.        |
|Globale Verfügbarkeit | HDInsight ist in mehr Regionen verfügbar als jede andere Big Data-Analyselösung. Zudem steht Azure HDInsight für Azure Government, China und Deutschland zur Verfügung, was die Erfüllung geschäftlicher Anforderungen in zentralen unabhängigen Bereichen ermöglicht. |  
|Produktivität     |  Mit Azure HDInsight können Sie umfangreiche Produktivitätstools für Hadoop und Spark in Ihrer bevorzugten Entwicklungsumgebung nutzen. Zu diesen Entwicklungsumgebungen gehören Visual Studio, VSCode, Eclipse und IntelliJ für die Unterstützung von Scala, Python, R, Java und .NET. Datenanalysten können zudem über gängige Notebooks wie Jupyter und Zeppelin zusammenarbeiten.    |
|Erweiterbarkeit     |  Sie können die HDInsight-Cluster um installierte Komponenten erweitern (z.B. Hue, Presto usw.), indem Sie Skriptaktionen verwenden, Edgeknoten hinzufügen oder andere, für Big Data zertifizierte Anwendungen integrieren. HDInsight lässt sich mittels One-Click-Bereitstellung nahtlos in die gängigsten Big Data-Lösungen integrieren.|

## <a name="scenarios-for-using-hdinsight"></a>Verwendungsszenarien für HDInsight

Azure HDInsight kann im Rahmen verschiedenster Szenarien für die Big Data-Verarbeitung verwendet werden. Dabei kann es sich um Verlaufsdaten (Daten, die bereits erfasst und gespeichert wurden) oder um Echtzeitdaten (Daten, die direkt von der Quelle gestreamt werden) handeln. Die Szenarien für die Verarbeitung dieser Daten lassen sich in folgende Kategorien unterteilen:

### <a name="batch-processing-etl"></a>Batchverarbeitung (ETL)

Extrahieren, Transformieren und Laden (ETL) ist ein Prozess, bei dem nicht strukturierte und strukturierte Daten aus heterogenen Datenquellen extrahiert werden. Anschließend werden sie in ein strukturiertes Format transformiert und in einen Datenspeicher geladen. Sie können die transformierten Daten für Data Science- oder Data Warehousing-Zwecke verwenden.

### <a name="data-warehousing"></a>Data Warehousing

Mit HDInsight können Sie interaktive Abfragen für Petabytes von strukturierten oder unstrukturierten Daten in einem beliebigen Format durchführen. Darüber hinaus können Sie Modelle für die Verknüpfung mit BI-Tools erstellen.

![HDInsight-Architektur: Data Warehousing](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "HDInsight: Data Warehousing-Architektur")

### <a name="internet-of-things-iot"></a>Internet der Dinge (IoT, Internet of Things)

Mit HDInsight können Sie Streamingdaten verarbeiten, die in Echtzeit von verschiedenen Arten von Geräten empfangen werden. Weitere Informationen finden Sie in [diesem Azure-Blog, in dem die öffentliche Vorschauversion von Apache Kafka unter HDInsight mit Azure Managed Disks angekündigt wird](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![HDInsight-Architektur: Internet der Dinge (IoT)](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "HDInsight: IoT-Architektur")

### <a name="data-science"></a>Data Science

Mit HDInsight können Sie Anwendungen erstellen, die wichtige Erkenntnisse aus Daten extrahieren. Zusätzlich können Sie mithilfe von Azure Machine Learning zukünftige Trends für Ihr Unternehmen prognostizieren. Weitere Informationen finden Sie in [diesem Kundenbericht](https://customers.microsoft.com/story/pros).

![HDInsight-Architektur: Data Science](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "HDInsight: Data Science-Architektur")

### <a name="hybrid"></a>Hybrid

Mit HDInsight können Sie Ihre vorhandene lokale Big Data-Infrastruktur auf Azure ausdehnen und von den erweiterten Analysefunktionen der Cloud profitieren.

![HDInsight-Architektur: Hybrid](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "HDInsight: Hybridarchitektur")

## <a name="cluster-types-in-hdinsight"></a>Clustertypen in HDInsight

HDInsight umfasst bestimmte Clustertypen und Clusteranpassungsfunktionen, z.B. die Möglichkeit zum Hinzufügen von Komponenten, Hilfsprogrammen und Sprachen. HDInsight bietet die folgenden Clustertypen:

|Clustertyp | BESCHREIBUNG |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|Ein Framework, das Hadoop Distributed File System, die YARN-Ressourcenverwaltung und ein einfaches MapReduce-Programmiermodell zum parallelen Verarbeiten und Analysieren von Batchdaten nutzt.|
|[Apache Spark](./spark/apache-spark-overview.md)|Ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern. Siehe [Was ist Apache Spark in HDInsight?](./spark/apache-spark-overview.md)|
|[Apache HBase](./hbase/apache-hbase-overview.md)|Eine auf Hadoop basierende NoSQL-Datenbank, die wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet – in einer potenziellen Dimension von Milliarden von Zeilen multipliziert mit Milliarden von Spalten. Siehe [Was ist HBase in HDInsight?](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|Ein Server zum Hosten und Verwalten von parallelen, verteilten R-Prozessen. Diese Funktion ermöglicht Datenanalysten, Statistikern und R-Programmierern bei Bedarf den Zugriff auf skalierbare, verteilte Analysemethoden in HDInsight. Siehe [Einführung in R Server und Open-Source-R-Funktionen in HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Ein verteiltes Echtzeitberechnungssystem für die schnelle Verarbeitung großer Datenströme. Storm wird als verwalteter Cluster in HDInsight angeboten. Siehe [Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop](./storm/apache-storm-overview.md).|
|[Interaktive Apache-Abfrage](./interactive-query/apache-interactive-query-get-started.md)|Arbeitsspeicherinternes Caching für interaktive und schnellere Hive-Abfragen. Siehe [Use Interactive Query in HDInsight](./interactive-query/apache-interactive-query-get-started.md) (Verwenden von Interactive Query in HDInsight).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Eine Open Source-Plattform zum Erstellen von Streamingdatenpipelines und -anwendungen. Kafka bietet auch eine Nachrichtenwarteschlangenfunktion, die Ihnen das Veröffentlichen und Abonnieren von Datenströmen ermöglicht. Siehe [Introduction to Apache Kafka on HDInsight](./kafka/apache-kafka-introduction.md) (Einführung in Apache Kafka in HDInsight).|

## <a name="open-source-components-in-hdinsight"></a>Open-Source-Komponenten in HDInsight

Azure HDInsight ermöglicht die Erstellung von Clustern mit Open Source-Frameworks wie Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase und R. Diese Cluster verfügen standardmäßig über weitere integrierte Open Source-Komponenten wie Apache Ambari5, Avro5, Apache Hive3, HCatalog2, Apache Mahout2, Apache Hadoop MapReduce3, Apache Hadoop YARN2, Apache Phoenix3, Apache Pig3, Apache Sqoop3, Apache Tez3, Apache Oozie2 und Apache ZooKeeper5.  

## <a name="programming-languages-in-hdinsight"></a>Programmiersprachen in HDInsight

HDInsight-Cluster, z.B. Spark, HBase, Kafka, Hadoop und andere, unterstützen viele Programmiersprachen. Einige Programmiersprachen werden nicht standardmäßig installiert. Verwenden Sie eine Skriptaktion, um Bibliotheken, Module oder Pakete zu installieren, die standardmäßig nicht installiert sind.

|Programmiersprache  |Information  |
|---------|---------|
|Standardmäßige Unterstützung für Programmiersprachen     | Standardmäßig unterstützen HDInsight-Cluster folgende Sprachen:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Go</li></ul>  |
|JVM-Sprachen (Java Virtual Machine)     | Auf einer Java Virtual Machine (JVM) können neben Java auch viele andere Sprachen ausgeführt werden. Bei der Ausführung von einigen dieser Sprachen müssen Sie im Cluster unter Umständen aber zusätzliche Komponenten installieren. Die folgenden JVM-basierten Sprachen werden in HDInsight-Clustern unterstützt: <ul><li>Clojure</li><li>Jython (Python für Java)</li><li>Scala</li></ul>     |
|Hadoop-spezifische Sprachen     | HDInsight-Cluster bieten Unterstützung für die folgenden Sprachen, die für den Hadoop-Technologiestapel spezifisch sind: <ul><li>Pig Latin für Pig-Aufträge</li><li>HiveQL für Hive-Aufträge und SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Entwicklungstools für HDInsight

Sie können HDInsight-Entwicklungstools wie IntelliJ, Eclipse, Visual Studio Code und Visual Studio verwenden, um HDInsight-Datenabfragen und -aufträge zu erstellen und zu übermitteln – mit nahtloser Integration in Azure.

* Azure-Toolkit für IntelliJ10
* Azure-Toolkit für Eclipse6
* Azure HDInsight-Tools für VS Code13
* Azure Data Lake-Tools für Visual Studio9

## <a name="business-intelligence-on-hdinsight"></a>Business Intelligence in HDInsight

Bekannte Business Intelligence-Tools (BI) rufen Daten, die in HDInsight integriert sind, entweder über das Power Query-Add-In oder den Microsoft Hive ODBC-Treiber ab, analysieren sie und erstellen Berichte:

* [Apache Spark BI mit Datenvisualisierungstools unter Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Visualize Interactive Query Hive data with Microsoft Power BI using direct query in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) (Visualisieren von Interactive Query-Hive-Daten mit Microsoft Power BI mittels direkter Abfrage in Azure HDInsight)

* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (setzt Windows voraus)

* [Verbinden von Excel mit Apache Hadoop über den Microsoft Hive ODBC-Treiber](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (setzt Windows voraus)


## <a name="in-region-data-residency"></a>Data Residency in der Region 

Spark, Hadoop, LLAP, Storm und MLService speichern keine Kundendaten, daher erfüllen diese Dienste automatisch die Anforderungen an die Data Residency in der Region, einschließlich der im [Trust Center](https://azuredatacentermap.azurewebsites.net/) angegebenen Anforderungen. 

Kafka und HBase speichern Kundendaten. Diese Daten werden von Kafka und HBase automatisch in einer einzigen Region gespeichert, sodass dieser Dienst die Anforderungen an die Data Residency in der Region erfüllt, einschließlich der im [Trust Center](https://azuredatacentermap.azurewebsites.net/) angegebenen Anforderungen. 


Bekannte Business Intelligence-Tools (BI) rufen Daten, die in HDInsight integriert sind, entweder über das Power Query-Add-In oder den Microsoft Hive ODBC-Treiber ab, analysieren sie und erstellen Berichte.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Apache Hadoop-Clusters in HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Erstellen eines Apache Spark-Clusters: Portal](./spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Unternehmenssicherheit in Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
