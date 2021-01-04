---
title: 'Worum handelt es sich bei Apache Hadoop und MapReduce: Azure HDInsight'
description: Eine Einführung in HDInsight, den Apache Hadoop-Technologiestapel und Apache Hadoop-Komponenten
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: ef1914499765beff9913f9735cf55736135f9d96
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92899621"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Worum handelt es sich bei Apache Hadoop in Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) war ursprünglich ein Open Source-Framework für die verteilte Verarbeitung und Analyse umfangreicher Datasets in Clustern. Das Hadoop-Ökosystem umfasst verwandte Software und Hilfsprogramme, einschließlich Apache Hive, Apache HBase, Spark, Kafka und viele andere.

Azure HDInsight ist ein umfassender, vollständig verwalteter Open-Source-Analysedienst in der Cloud für Unternehmen. Der Apache Hadoop-Clustertyp in Azure HDInsight ermöglicht Ihnen die Verwendung von [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), der [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)-Ressourcenverwaltung und eines einfachen [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)-Programmiermodells zum parallelen Verarbeiten und Analysieren von Batchdaten.  Hadoop-Cluster in HDInsight sind mit [Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md) oder [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) kompatibel.

Informationen zu verfügbare Komponenten des Hadoop-Technologiestapels für HDInsight finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](../hdinsight-component-versioning.md). Weitere Informationen zu Hadoop in HDInsight finden Sie auf der Seite mit [Azure-Features für HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Was ist MapReduce?

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) ist ein Softwareframework zum Schreiben von Aufträgen, die sehr große Datenmengen verarbeiten. Eingabedaten werden in unabhängigen Blöcke aufgeteilt. Jeder Block wird in den Knoten im Cluster parallel verarbeitet. Ein MapReduce-Auftrag besteht aus zwei Funktionen:

* **Mapper** : Verarbeitet Eingabedaten, analysiert sie (in der Regel mit Filter- und Sortiervorgängen) und gibt Tupel (Schlüssel-Wert-Paare) aus.

* **Reducer** : Verarbeitet Tupel, die vom Mapper ausgegeben wurden, und führt einen Zusammenfassungsvorgang aus, der kleinere, kombinierte Ergebnisse aus den Mapper-Daten erstellt.

Ein Beispiel für einen grundlegenden MapReduce-Auftrag zum Zählen von Wörtern wird im folgenden Diagramm veranschaulicht:

 ![HDI.Wortzähldiagramm](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Dieses Auftrag gibt die Anzahl der Vorkommen jedes Worts im Text aus.

* Der Mapper verwendet die einzelnen Zeilen des Eingabetexts als Eingabe und unterteilt diese in Wörter. Er gibt bei jedem Vorkommen eines Worts ein Schlüssel-Wert-Paar des Worts gefolgt von 1 aus. Die Ausgabe wird sortiert, bevor sie an den Reducer gesendet wird.
* Der Reducer addiert die einzelnen Zählwerte jedes Worts und gibt ein einziges Schlüssel-Wert-Paar aus, das aus dem Wort gefolgt von der Summe seiner Vorkommen besteht.

MapReduce kann in verschiedenen Sprachen implementiert werden. Java ist die am häufigsten verwendete Implementierung und wird in diesem Dokument zu Demonstrationszwecken verwendet.

## <a name="development-languages"></a>Entwicklungssprachen

Sprachen oder Frameworks auf der Grundlage von Java und Java Virtual Machine können direkt als [MapReduce-Auftrag](..//hadoop/submit-apache-hadoop-jobs-programmatically.md) ausgeführt werden. Das in diesem Artikel verwendete Beispiel ist eine Java-MapReduce-Anwendung. Nicht Java-basierte Sprachen (etwa C# oder Python) bzw. eigenständige ausführbare Dateien müssen **Hadoop-Datenströme** verwenden.

Hadoop-Datenströme kommunizieren über STDIN und STDOUT mit Mapper und Reducer. Mapper und Reducer lesen zeilenweise Daten aus STDIN, und schreiben die Ausgabe in STDOUT. Jede vom Mapper und vom Reducer gelesene oder ausgegebene Zeile muss als Schlüssel-Wert-Paar mit Tabstopptrennzeichen formatiert sein:

`[key]\t[value]`

Weitere Informationen finden Sie unter [Hadoop Streaming](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html)(in englischer Sprache).

Beispiele für die Verwendung von Hadoop-Datenströmen mit HDInsight finden Sie in dem folgenden Artikel:

* [Entwickeln von C#-MapReduce-Aufträgen](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Erste Schritte

* [Schnellstart: Erstellen eines Apache Hadoop-Clusters in Azure HDInsight im Azure-Portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Tutorial: Übermitteln von Apache Hadoop-Aufträgen in HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Entwickeln von Java MapReduce-Programmen für Apache Hadoop in HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Verwenden von Apache Hive als Tool zum Extrahieren, Transformieren und Laden (ETL)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Bedarfsorientiertes Extrahieren, Transformieren und Laden (ETL)](../hadoop/apache-hadoop-etl-at-scale.md)
* [Operationalisieren einer Datenanalysepipeline](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Apache Hadoop-Clusters in HDInsight mithilfe des Portals](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Erstellen eines Apache Hadoop-Clusters in HDInsight mithilfe einer ARM-Vorlage](../hadoop/apache-hadoop-linux-tutorial-get-started.md)