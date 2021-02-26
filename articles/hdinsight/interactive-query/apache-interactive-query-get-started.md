---
title: Was ist Interactive Query in Azure HDInsight?
description: Eine Einführung in Interactive Query (auch als „Apache Hive LLAP“ bezeichnet) in Azure HDInsight
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 2813554700e015c0ac34e47d632d16d97c948c4e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941081"
---
# <a name="what-is-interactive-query-in-azure-hdinsight"></a>Interactive Query in Azure HDInsight

Interactive Query (auch Apache Hive LLAP oder [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP) genannt) ist ein Azure HDInsight-[Clustertyp](../hdinsight-hadoop-provision-linux-clusters.md#cluster-type). Interactive Query unterstützt speicherinternes Zwischenspeichern, wodurch Apache Hive-Abfragen schneller und viel interaktiver werden. Kunden verwenden Interactive Query, um Daten, die in Azure Storage und Azure Data Lake Storage gespeichert sind, äußerst schnell abzufragen. Interactive Query erleichtert Entwicklern und Data Scientists die Arbeit mit Big Data mit den BI-Tools, die sie am meisten lieben. HDInsight Interactive Query unterstützt verschiedene Tools für den mühelosen Zugriff auf Big Data.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Ein Interactive Query-Cluster unterscheidet sich von einem Apache Hadoop-Cluster. Er enthält nur den Hive-Dienst.

Sie können im Interactive Query-Cluster nur über die Apache Ambari-Hive-Ansicht, Beeline und den Microsoft Hive Open Database Connectivity-Treiber (Hive ODBC) auf den Hive-Dienst zugreifen. Sie können nicht über die Hive-Konsole, Templeton, die klassische Azure-Befehlszeilenschnittstelle oder Azure PowerShell darauf zugreifen.

## <a name="create-an-interactive-query-cluster"></a>Erstellen eines Interactive Query-Clusters

Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw](../hdinsight-hadoop-provision-linux-clusters.md). Wählen Sie den Clustertyp „Interactive Query“ aus.

> [!IMPORTANT]
> Die minimale Hauptknotengröße für interaktive Abfragecluster ist Standard_D13_v2. Weitere Informationen finden Sie im [Größentabelle für virtuelle Azure-Computer](../../cloud-services/cloud-services-sizes-specs.md#dv2-series).

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Ausführen von Apache Hive-Abfragen über Interactive Query

Um Hive-Abfragen auszuführen, können Sie folgende Optionen nutzen:

|Methode |BESCHREIBUNG |
|---|---|
|Microsoft Power BI|Weitere Informationen finden Sie unter [Visualisieren von Interactive Query-Hive-Daten mit Power BI mithilfe direkter Abfragen in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) sowie unter [Visualisieren von Apache Hive-Daten mit Microsoft Power BI mithilfe von ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).|
|Visual Studio|Siehe [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)|
|Visual Studio Code|Weitere Informationen finden Sie unter [Verwenden von Visual Studio Code für Apache Hive, LLAP oder pySpark](../hdinsight-for-vscode.md).|
|Apache Ambari-Hive-Ansicht|Siehe [Verwenden der Apache Ambari-Hive-Ansicht mit Apache Hadoop in HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md) Die Hive-Ansicht ist für HDInsight 4.0 nicht verfügbar.|
|Apache Beeline|Informationen finden Sie unter [Verwenden des Apache Beeline-Clients mit Apache Hive](../hadoop/apache-hadoop-use-hive-beeline.md). Sie können Beeline entweder über den Hauptknoten oder von einem leeren Edgeknoten aus verwenden. Am besten verwenden Sie Beeline von einem leeren Edgeknoten aus. Informationen zum Erstellen eines HDInsight-Clusters mit einem leeren Edgeknoten finden Sie unter [Verwenden leerer Edgeknoten in Hadoop-Clustern in HDInsight](../hdinsight-apps-use-edge-node.md).|
|Hive ODBC|Informationen finden Sie unter [Verbinden von Excel mit Apache Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).|

So suchen Sie die Verbindungszeichenfolge für die Java Database Connectivity (JDBC):

1. Navigieren Sie in einem Webbrowser zu `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.
1. Wählen Sie das Symbol für die Zwischenablage zum Kopieren der URL aus:

   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Interactive Query-Cluster in HDInsight erstellen](../hdinsight-hadoop-provision-linux-clusters.md).
* Erfahren Sie, wie Sie [Big Data mit Power BI in Azure HDInsight visualisieren](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Erfahren Sie, wie Sie [Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight verwenden](../interactive-query/hdinsight-connect-hive-zeppelin.md).
