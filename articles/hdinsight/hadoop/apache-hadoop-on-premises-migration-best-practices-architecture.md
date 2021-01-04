---
title: 'Architektur: Lokales Apache Hadoop zu Azure HDInsight'
description: Erfahren Sie mehr über bewährte Methoden für die Architektur bei der Migration von lokalen Hadoop-Clustern zu Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: af9b1f42140c5656c5f55a98c2d635d59e130db5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533732"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---architecture-best-practices"></a>Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight – bewährte Methoden für die Architektur

Dieser Artikel enthält Empfehlungen für die Architektur von Azure HDInsight-Systemen. Er ist Teil einer Reihe von Artikeln, die bewährte Methoden für die Migration von lokalen Apache Hadoop-Systemen zu Azure HDInsight enthalten.

## <a name="use-multiple-workload-optimized-clusters"></a>Verwenden mehrerer für Workloads optimierter Cluster

Viele lokale Bereitstellungen von Apache Hadoop bestehen aus einem einzelnen großen Cluster, der viele Workloads unterstützt. Dieser einzelne Cluster kann komplex sein und macht möglicherweise Kompromisse bei einzelnen Diensten erforderlich, damit alles zusammen funktioniert. Die Migration von lokalen Hadoop-Clustern zu Azure HDInsight erfordert einen anderen Ansatz.

Azure HDInsight-Cluster sind für einen bestimmten Typ von Computenutzung ausgelegt. Da der Speicher von mehreren Clustern gemeinsam genutzt werden kann, ist es möglich, mehrere für Workloads optimierte Computecluster zu erstellen, um die Anforderungen verschiedener Aufträge zu erfüllen. Jeder Clustertyp weist die optimale Konfiguration für die jeweilige Workload auf. In der folgenden Tabelle sind die unterstützten Clustertypen in HDInsight und die jeweiligen Workloads aufgeführt.

|Workload|HDInsight-Clustertyp|
|---|---|
|Batchverarbeitung (ETL/ELT)|Hadoop, Spark|
|Data Warehousing|Hadoop, Spark, Interactive Query|
|IoT/Streaming|Kafka, Storm, Spark|
|NoSQL-Transaktionsverarbeitung|hbase|
|Interaktive und schnellere Abfragen mit speicherinternem Caching|Interactive Query|
|Data Science|ML Services, Spark|

In der folgenden Tabelle sind die verschiedenen Methoden angegeben, die zum Erstellen eines HDInsight-Clusters verwendet werden können.

|Tool|Browserbasiert|Befehlszeile|REST-API|SDK|
|---|---|---|---|---|
|[Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md)|X||||
|[Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md)|X|X|X|X|
|[Azure CLI (Version 1.0)](../hdinsight-hadoop-create-linux-clusters-azure-cli.md)||X|||
|[Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md)||X|||
|[cURL](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)||X|X||
|[.NET SDK](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true)||||X|
|[Python SDK](/python/api/overview/azure/hdinsight)||||X|
|[Java SDK](/java/api/overview/azure/hdinsight)||||X|
|[Azure-Ressourcen-Manager-Vorlagen](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)||X|||

Weitere Informationen finden Sie im Artikel [Clustertypen in HDInsight](../hadoop/apache-hadoop-introduction.md).

## <a name="use-transient-on-demand-clusters"></a>Verwenden kurzlebiger bedarfsgesteuerter Cluster

HDInsight-Cluster werden möglicherweise über lange Zeiträume nicht verwendet. Um Ressourcenkosten zu sparen, unterstützt HDInsight bedarfsgesteuerte kurzlebige Cluster, die gelöscht werden können, sobald die Workload erfolgreich abgeschlossen wurde.

Wenn Sie einen Cluster löschen, werden das zugeordnete Speicherkonto und externe Metadaten nicht entfernt. Der Cluster kann später mit den gleichen Speicherkonten und Metastores neu erstellt werden.

Mit Azure Data Factory kann die Erstellung bedarfsgesteuerter HDInsight-Cluster geplant werden. Weitere Informationen finden Sie im Artikel [Erstellen bedarfsgesteuerter Apache Hadoop-Cluster in HDInsight mit Azure Data Factory](../hdinsight-hadoop-create-linux-clusters-adf.md).

## <a name="decouple-storage-from-compute"></a>Entkoppeln von Speicher und Compute

Übliche lokale Hadoop-Bereitstellungen verwenden die gleiche Gruppe von Computern für die Datenspeicherung und die Datenverarbeitung. Da sie am gleichen Ort vorhanden sind, müssen Compute und Speicher gemeinsam skaliert werden.

Bei HDInsight-Clustern muss der Speicher nicht am gleichen Ort vorhanden sein wie Compute und kann sich in Azure Storage, in Azure Data Lake Storage oder in beidem befinden. Das Entkoppeln von Speicher und Compute bietet folgende Vorteile:

- Datenfreigabe zwischen Clustern
- Verwendung kurzlebiger Cluster, da die Daten nicht vom Cluster abhängig sind
- Reduzierte Speicherkosten
- Separates Skalieren von Speicher und Compute
- Regionsübergreifende Datenreplikation

Computecluster werden in der Nähe von Speicherkontoressourcen in einer Azure-Region erstellt, um den Leistungsaufwand bei einer Trennung von Computer und Speicher zu verringern. Durch Hochgeschwindigkeitsnetzwerke können Computeknoten effizient auf die Daten im Azure-Speicher zugreifen.

## <a name="use-external-metadata-stores"></a>Verwenden von externen Metadatenspeichern

Es gibt zwei wichtige Metastores, die mit HDInsight-Clustern verwendet werden können: [Apache Hive](https://hive.apache.org/) und [Apache Oozie](https://oozie.apache.org/). Der Hive-Metastore ist das zentrale Schemarepository, das von Datenverarbeitungsmodulen verwendet werden kann. Dazu gehören Hadoop, Spark, LLAP, Presto und Apache Pig. Im Oozie-Metastore werden Details zur Zeitplanung sowie der Status von laufenden und abgeschlossenen Hadoop-Aufträgen gespeichert.

HDInsight verwendet Azure SQL-Datenbank für Hive- und Oozie-Metastores. Ein Metastore kann in HDInsight-Clustern auf zwei Arten eingerichtet werden:

1. Standardmetastore

    - Keine zusätzlichen Kosten.
    - Metastore wird gelöscht, wenn der Cluster gelöscht wird
    - Metastore kann nicht für verschiedene Cluster freigegeben werden
    - Verwendet die Azure SQL-Basisdatenbank, die auf fünf DTUs begrenzt ist

1. Benutzerdefinierter externer Metastore

    - Angeben einer externen Azure SQL-Datenbank als Metastore
    - Cluster können erstellt und gelöscht werden, ohne dass Metadaten verloren gehen, einschließlich Informationen zum Hive-Schema oder Oozie-Auftrag
    - Einzelne Metastore-Datenbank kann für verschiedene Clustertypen freigegeben werden
    - Metastore kann nach Bedarf zentral hochskaliert werden
    - Weitere Informationen finden Sie unter [Verwenden von externen Metadatenspeichern in Azure HDInsight](../hdinsight-use-external-metadata-stores.md).

## <a name="best-practices-for-hive-metastore"></a>Bewährte Methoden für den Hive-Metastore

Es folgen einige bewährte Methoden für den Hive-Metastore in HDInsight:

- Verwenden Sie einen benutzerdefinierten externen Metastore, um Computeressourcen und Metadaten zu trennen.
- Beginnen Sie mit einer Azure SQL-Instanz im S2-Tarif, die 50 DTUs und 250 GB Speicher bietet. Wenn Sie einen Engpass feststellen, können Sie die Datenbank zentral hochskalieren.
- Geben Sie den Metastore, der für eine HDInsight-Clusterversion erstellt wurde, nicht für Cluster einer anderen Version frei. Verschiedene Hive-Versionen verwenden unterschiedliche Schemas. Ein Metastore kann beispielsweise nicht für Cluster der Version Hive 1.2 und Hive 2.1 freigegeben werden.
- Sichern Sie den benutzerdefinierten Metastore regelmäßig.
- Belassen Sie den Metastore und den HDInsight-Cluster in derselben Region.
- Überwachen Sie den Metastore in Hinblick auf Leistung und Verfügbarkeit. Verwenden Sie dazu Überwachungstools für die Azure SQL-Datenbank, z.B. das Azure-Portal oder Azure Monitor-Protokolle.
- Führen Sie bei Bedarf den Befehl `ANALYZE TABLE` aus, um Statistiken für Tabellen und Spalten zu generieren. Beispiel: `ANALYZE TABLE [table_name] COMPUTE STATISTICS`.

## <a name="best-practices-for-different-workloads"></a>Bewährte Methoden für verschiedene Workloads

- Verwenden Sie ggf. einen LLAP-Cluster für interaktive Hive-Abfragen mit verbesserter Antwortzeit. [LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) ist ein neues Feature in Hive 2.0, das ein speicherinternes Caching von Abfragen ermöglicht. LLAP macht Hive-Abfragen deutlich schneller – bis zu [in einigen Fällen 26-mal schneller als Hive 1.x](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).
- Ziehen Sie die Verwendung von Spark-Aufträgen anstelle von Hive-Aufträgen in Betracht.
- Ersetzen Sie ggf. Impala-basierte Abfragen durch LLAP-Abfragen.
- Ersetzen Sie ggf. MapReduce-Aufträge durch Spark-Aufträge.
- Ersetzen Sie ggf. Spark-Batchaufträge mit niedriger Latenz mithilfe von Spark Structured Streaming-Aufträgen.
- Ziehen Sie die Verwendung von Azure Data Factory (ADF) 2.0 für die Datenorchestrierung in Betracht.
- Ziehen Sie Ambari für die Clusterverwaltung in Betracht.
- Ändern Sie den Datenspeicher von lokalem HDFS in WASB oder ADLS oder ADFS für die Verarbeitung von Skripts.
- Ziehen Sie die Verwendung von Ranger RBAC für Hive-Tabellen und Überwachung in Betracht.
- Verwenden Sie ggf. COSMOSDB anstelle von MongoDB oder Cassandra.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den nächsten Artikel in dieser Reihe:

- [Bewährte Methoden für die Infrastruktur bei der Migration von lokalen Hadoop-Clustern zu Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-infrastructure.md)