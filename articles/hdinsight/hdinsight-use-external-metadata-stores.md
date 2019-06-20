---
title: Verwenden von externen Metadatenspeichern – Azure HDInsight
description: Verwenden von externen Metadatenspeichern für HDInsight-Cluster
author: hrasheed-msft
ms.reviewer: jasonh
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 705ced82ad4edad0bb4adc057414f6b20b80d8d3
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298872"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Verwenden von externen Metadatenspeichern in Azure HDInsight

Der Apache Hive-Metastore in HDInsight ist ein wesentlicher Bestandteil der Apache Hadoop-Architektur. Ein Metastore ist das zentrale Schemarepository, das von anderen Tools für den Zugriff auf Big Data wie Apache Spark, Interactive Query (LLAP), Presto oder Apache Pig verwendet werden kann. HDInsight verwendet eine Azure SQL-Datenbank als Hive-Metastore.

![Architektur des Hive-Metadatenspeichers in HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Sie können einen Metastore für Ihre HDInsight-Cluster auf zwei Arten einrichten:

* [Standardmetastore](#default-metastore)
* [Benutzerdefinierter Metastore](#custom-metastore)

## <a name="default-metastore"></a>Standardmetastore

HDInsight erstellt standardmäßig einen Metastore für jeden Clustertyp. Sie können stattdessen einen benutzerdefinierten Metastore angeben. Beim Standardmetastore sind folgende Aspekte zu berücksichtigen:
- Keine zusätzlichen Kosten. HDInsight erstellt für jeden Clustertyp einen Metastore ohne zusätzliche Kosten.
- Jeder Standardmetastore ist Teil des Clusterlebenszyklus. Wenn Sie einen Cluster löschen, werden der entsprechende Metastore und die jeweiligen Metadaten ebenfalls gelöscht.
- Der Standardmetastore kann nicht für andere Cluster freigegeben werden.
- Der Standardmetastore verwendet die Azure SQL-Basisdatenbank, die auf fünf DTUs (Datenbankübertragungseinheiten) begrenzt ist.
Dieser Standardmetastore wird normalerweise für relativ einfache Arbeitslasten verwendet, für die weder mehrere Cluster noch eine Beibehaltung von Metadaten über den Lebenszyklus des Clusters hinaus erforderlich sind.


## <a name="custom-metastore"></a>Benutzerdefinierter Metastore

HDInsight unterstützt auch benutzerdefinierte Metastores, der für Produktionscluster empfohlen werden:
- Sie geben Ihre eigene Azure SQL-Datenbank als Metastore an.
- Der Lebenszyklus des Metastore ist nicht an den Lebenszyklus eines Clusters gebunden, sodass Sie Cluster ohne Verlust von Metadaten erstellen und löschen können. Metadaten, wie z. B. Ihre Hive-Schemas, bleiben auch nach dem Löschen und erneuten Erstellen des HDInsight-Clusters erhalten.
- Ein benutzerdefinierter Metastore ermöglicht das Anfügen mehrerer Cluster und Clustertypen an diesen Metastore. Beispielsweise kann ein einzelner Metastore für Interactive Query-, Hive- und Spark-Cluster in HDInsight freigegeben werden.
- Die Kosten für einen Metastore (Azure SQL-Datenbank) richten sich nach der von Ihnen ausgewählten Leistungsstufe.
- Sie können den Metastore nach Bedarf zentral hochskalieren.

![Anwendungsfall für den Hive-Metadatenspeicher in HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)


### <a name="select-a-custom-metastore-during-cluster-creation"></a>Auswählen eines benutzerdefinierten Metastore während der Clustererstellung

Sie können den Cluster während der Erstellung auf eine zuvor erstellte Azure SQL-Datenbank verweisen, oder Sie können die SQL-Datenbank nach der Erstellung des Clusters konfigurieren. Diese Option wird unter **Speicher > Metastore-Einstellungen** beim Erstellen eines neuen Hadoop-, Spark- oder Interactive Hive-Clusters über das Azure-Portal angegeben.

![Azure-Portal für Hive-Metadatenspeicher in HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-azure-portal.png)

Sie können über das Azure-Portal oder die Ambari-Konfigurationen („Hive“ > „Erweitert“) auch zusätzliche Cluster zu einem benutzerdefinierten Metastore hinzufügen.

![Ambari für Hive-Metadatenspeicher in HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-ambari.png)

## <a name="hive-metastore-best-practices"></a>Bewährte Methoden für den Hive-Metastore

Es folgen einige allgemeine bewährte Methoden für den Hive-Metastore in HDInsight:

- Verwenden Sie wann immer möglich einen benutzerdefinierten Metastore, um das Trennen von Computeressourcen (Ihre ausgeführten Cluster) und Metadaten (im Metastore gespeichert) zu erleichtern.
- Beginnen Sie mit einem S2-Tarif, der 50 DTUs und 250 GB Speicher bietet. Wenn Sie einen Engpass feststellen, können Sie die Datenbank zentral hochskalieren.
- Wenn Sie mehreren HDInsight-Clustern Zugriff auf separate Daten gewähren möchten, verwenden Sie für den Metastore auf jedem Cluster eine eigene Datenbank. Wenn Sie einen Metastore für mehrere HDInsight-Cluster freigeben, bedeutet dies, dass die Cluster dieselben Metadaten und zugrunde liegenden Benutzerdatendateien verwenden.
- Sichern Sie Ihren benutzerdefinierten Metastore regelmäßig. Die Azure SQL-Datenbank generiert Sicherungen automatisch, der Aufbewahrungszeitraum der Sicherungen variiert jedoch. Weitere Informationen finden Sie unter [Informationen zu automatischen Sicherungen von SQL-Datenbank](../sql-database/sql-database-automated-backups.md).
- Um eine bessere Leistung und möglichst geringe Kosten für ausgehenden Netzwerkdatenverkehr zu erzielen, sollten sich der Metastore und der HDInsight-Cluster in derselben Region befinden.
- Überwachen Sie den Metastore in Hinblick auf Leistung und Verfügbarkeit. Verwenden Sie dazu Überwachungstools für die Azure SQL-Datenbank, wie z. B. das Azure-Portal oder Azure Monitor-Protokolle.
- Wenn eine neue, höhere Version von Azure HDInsight anhand einer vorhandenen benutzerdefinierten Metastoredatenbank erstellt wird, aktualisiert das System das Metastoreschema unwiderruflich, ohne die Datenbank aus der Sicherung wiederherzustellen.
- Falls Sie einen Metastore für mehrere Cluster freigeben, achten Sie darauf, dass alle Cluster die gleiche HDInsight-Version haben. Verschiedene Hive-Versionen verwenden unterschiedliche Schemas der Metastoredatenbank. So können Sie beispielsweise einen Metastore nicht für Hive 1.2- und Hive 2.1-Cluster freigeben. 

##  <a name="apache-oozie-metastore"></a>Apache Oozie Metastore

Apache Oozie ist ein Koordinationssystem für Workflows zur Verwaltung von Hadoop-Aufträgen.  Oozie unterstützt Hadoop-Aufträge für Apache MapReduce, Pig, Hive und andere.  Oozie verwendet einen Metastore zum Speichern von Details zu aktuellen und abgeschlossenen Workflows. Zur Leistungssteigerung bei Verwendung von Oozie können Sie eine Azure SQL-Datenbank als benutzerdefinierten Metastore verwenden. Der Metastore kann auch Zugriff auf Oozie-Auftragsdaten bieten, nachdem Sie Ihren Cluster gelöscht haben.

Anleitungen zum Erstellen eines Oozie-Metastore mit einer Azure SQL-Datenbank finden Sie unter [Verwenden von Apache Oozie für Workflows](hdinsight-use-oozie-linux-mac.md).

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.](./hdinsight-hadoop-provision-linux-clusters.md)
