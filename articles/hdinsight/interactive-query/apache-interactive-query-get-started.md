---
title: Verwenden von Interactive Query mit Azure HDInsight
description: Erfahren Sie, wie Sie Interactive Query (Hive LLAP) mit HDInsight verwenden.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2019
ms.openlocfilehash: db36bbf6e20f9fab2f9a99f59be7a088e8f208e1
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65800910"
---
# <a name="use-interactive-query-with-hdinsight"></a>Verwenden von Interactive Query mit HDInsight
Interactive Query (auch Apache Hive LLAP oder [Low Latency Analytical Processing](https://cwiki.apache.org/confluence/display/Hive/LLAP) genannt) ist ein Azure HDInsight-[Clustertyp](../hdinsight-hadoop-provision-linux-clusters.md#cluster-types). Interactive Query unterstützt speicherinternes Zwischenspeichern, wodurch Apache Hive-Abfragen schneller und viel interaktiver werden.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)] 

Ein Interactive Query-Cluster unterscheidet sich von einem Apache Hadoop-Cluster. Er enthält nur den Hive-Dienst. 

> [!NOTE]  
> Sie können im Interactive Query-Cluster nur über die Apache Ambari-Hive-Ansicht, Beeline und den Microsoft Hive Open Database Connectivity-Treiber (Hive ODBC) auf den Hive-Dienst zugreifen. Sie können nicht über die Hive-Konsole, Templeton, die klassische Azure-Befehlszeilenschnittstelle oder Azure PowerShell darauf zugreifen. 

## <a name="create-an-interactive-query-cluster"></a>Erstellen eines Interactive Query-Clusters
Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw](../hdinsight-hadoop-provision-linux-clusters.md). Wählen Sie den Clustertyp „Interactive Query“ aus.

## <a name="execute-apache-hive-queries-from-interactive-query"></a>Ausführen von Apache Hive-Abfragen über Interactive Query
Um Hive-Abfragen auszuführen, können Sie folgende Optionen nutzen:

* Verwenden von Microsoft Power BI

    Siehe [Visualisieren von Interactive Query-Hive-Daten mit Power BI mithilfe direkter Abfragen in Azure HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md). Siehe [Visualisieren von Apache Hive-Daten mit Microsoft Power BI mithilfe von ODBC in Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md).

* Verwenden von Visual Studio

    Siehe [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries)

* Verwenden von Visual Studio Code

    Weitere Informationen finden Sie unter [Verwenden von Visual Studio Code für Apache Hive, LLAP oder pySpark](../hdinsight-for-vscode.md).
* Ausführen von Apache Hive mithilfe der Apache Ambari-Hive-Ansicht.
  
    Siehe [Verwenden der Apache Ambari-Hive-Ansicht mit Apache Hadoop in HDInsight](../hadoop/apache-hadoop-use-hive-ambari-view.md)

* Ausführen von Apache Hive mithilfe von Beeline
  
    Informationen finden Sie unter [Verwenden des Apache Beeline-Clients mit Apache Hive](../hadoop/apache-hadoop-use-hive-beeline.md).
  
    Sie können Beeline entweder über den Hauptknoten oder von einem leeren Edgeknoten aus verwenden. Am besten verwenden Sie Beeline von einem leeren Edgeknoten aus. Informationen zum Erstellen eines HDInsight-Clusters mit einem leeren Edgeknoten finden Sie unter [Verwenden leerer Edgeknoten in Hadoop-Clustern in HDInsight](../hdinsight-apps-use-edge-node.md).
* Ausführen von Apache Hive mithilfe von Hive ODBC
  
    Informationen finden Sie unter [Verbinden von Excel mit Apache Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

So suchen Sie die Verbindungszeichenfolge für die Java Database Connectivity (JDBC):

1. Melden Sie sich bei Apache Ambari mit der folgenden URL an: `https://<cluster name>.AzureHDInsight.net`.
2. Wählen Sie im Menü auf der linken Seite die Option **Hive** aus.
3. Wählen Sie das Symbol für die Zwischenablage zum Kopieren der URL aus:
   
   ![HDInsight Hadoop Interactive Query LLAP JDBC](./media/apache-interactive-query-get-started/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Interactive Query-Cluster in HDInsight erstellen](../hdinsight-hadoop-provision-linux-clusters.md).
* Erfahren Sie, wie Sie [Big Data mit Power BI in Azure HDInsight visualisieren](../hadoop/apache-hadoop-connect-hive-power-bi.md).
* Erfahren Sie, wie Sie [Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight verwenden](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* Erfahren Sie, wie Sie [Apache Hive-Abfragen mit Data Lake-Tools für Visual Studio ausführen](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#run-interactive-apache-hive-queries).
* Erfahren Sie, wie Sie [HDInsight-Tools für Visual Studio Code verwenden](../hdinsight-for-vscode.md).
* Erfahren Sie, wie Sie die [Apache Hive-Ansicht mit Apache Hadoop in HDInsight verwenden](../hadoop/apache-hadoop-use-hive-ambari-view.md).
* Erfahren Sie, wie Sie [Beeline zum Übermitteln von Apache Hive-Abfragen in HDInsight verwenden](../hadoop/apache-hadoop-use-hive-beeline.md).
* Erfahren Sie, wie Sie [Excel mithilfe des Microsoft Hive ODBC-Treibers mit Apache Hadoop verbinden](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).

