---
title: Integrieren von Data Lake Storage Gen1 in andere Azure-Dienste
description: Erfahren Sie, wie Sie Azure Data Lake Storage Gen1 in andere Azure-Dienste integrieren.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 929853f4dbedca7034c8e2a51e6231651a2dd08f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461651"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrieren von Azure Data Lake Storage Gen1 in andere Azure-Dienste
Azure Data Lake Storage Gen1 kann zusammen mit anderen Azure-Diensten verwendet werden, um eine breitere Palette von Szenarien zu ermöglichen. Im folgende Artikel werden die Dienste aufgeführt, in die Data Lake Storage Gen1 integriert werden kann.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Verwenden von Data Lake Storage Gen1 mit Azure HDInsight
Sie können einen [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/)-Cluster bereitstellen, der Data Lake Storage Gen1 als HDFS-kompatiblen Speicher verwendet. In dieser Version können Sie für Hadoop- und Storm-Cluster unter Windows und Linux Data Lake Storage Gen1 nur als Zusatzspeicher verwenden. Solche Cluster verwenden weiterhin Azure Storage (WASB) als Standardspeicher. Sie können jedoch für HBase-Cluster unter Windows und Linux Data Lake Storage Gen1 als Standardspeicher oder als Zusatzspeicher verwenden.

Anleitungen zum Bereitstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 finden Sie hier:

* [Bereitstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 mithilfe des Azure-Portals](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Bereitstellen von HDInsight-Clustern mit Data Lake Storage Gen1 als Standardspeicher mithilfe von PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Bereitstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 als Zusatzspeicher mithilfe von PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Verwenden von Data Lake Storage Gen1 mit Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) ermöglicht das Arbeiten mit Big Data in der Cloud. Die Anwendung stellt Ressourcen dynamisch bereit und ermöglicht Ihnen die Durchführung von Analysen von Terabytes oder sogar Exabytes an Daten, die in einer Reihe von unterstützten Datenquellen gespeichert sein können, auch in Data Lake Storage Gen1. Data Lake-Analytics ist speziell für die Arbeit mit Data Lake Storage Gen1 optimiert – so erzielen Sie Höchstwerte bei Leistung, Durchsatz und Parallelisierung bei Big Data-Workloads.

Anleitung zum Verwenden von Data Lake Analytics mit Data Lake Storage Gen1 finden Sie unter [Erste Schritte mit Data Lake Analytics unter Verwendung von Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Verwenden von Data Lake Storage Gen1 mit Azure Data Factory
Sie können [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) zum Erfassen von Daten aus Azure-Tabellen, Azure SQL-Datenbank, Azure SQL Data Warehouse, Azure Storage-Blobs und lokalen Datenbanken verwenden. Als wichtige Komponente im Azure-Ökosystem kann Azure Data Factory auch zum Orchestrieren der Erfassung von Daten aus diesen Quelle in Data Lake Storage Gen1 verwendet werden.

Anleitungen zum Verwenden von Azure Data Factory mit Data Lake Storage Gen1 finden Sie unter [Verschieben von Daten in und aus Data Lake Storage Gen1 mit Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1
Azure Data Lake Storage Gen1 bietet ein Befehlszeilentool namens AdlCopy, mit dem Sie Daten aus Azure Blob Storage in ein Data Lake Storage Gen1-Konto kopieren können. Weitere Informationen finden Sie unter [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopieren von Daten zwischen Azure SQL-Datenbank und Data Lake Storage Gen1
Sie können Apache Sqoop verwenden, um Daten zwischen Azure SQL-Datenbank und Data Lake Storage Gen1 zu importieren und zu exportieren. Weitere Informationen finden Sie unter [Kopieren von Daten zwischen Data Lake Storage Gen1 und Azure SQL-Datenbank mithilfe von Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Verwenden von Data Lake Storage Gen1 mit Stream Analytics
Sie können Data Lake Storage Gen1 als eine der Ausgaben zum Speichern von Daten verwenden, die mit Azure Stream Analytics gestreamt werden. Weitere Informationen finden Sie unter [Streamen von Daten aus Azure Storage-Blob in Data Lake Storage Gen1 mit Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Verwenden von Data Lake Storage Gen1 mit Power BI
Sie können Power BI zum Importieren von Daten aus einem Data Lake Storage Gen1-Konto verwenden, um die Daten zu analysieren und zu visualisieren. Weitere Informationen finden Sie unter [Analysieren von Daten in Data Lake Storage Gen1 mithilfe von Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Verwenden von Data Lake Storage Gen1 mit Data Catalog
Sie können Daten aus Data Lake Storage Gen1 in Azure Data Catalog registrieren, damit die Daten in der gesamten Organisation auffindbar sind. Weitere Informationen finden Sie unter [Registrieren von Daten aus Data Lake Storage Gen1 in Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Verwenden von Data Lake Storage Gen1 mit SQL Server Integration Services (SSIS)
Sie können den Data Lake Storage Gen1-Verbindungs-Manager in SSIS verwenden, um für ein SSIS-Paket eine Verbindung mit Data Lake Storage Gen1 herzustellen. Weitere Informationen finden Sie unter [Verwenden von Data Lake Storage Gen1 mit SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Verwenden von Data Lake Storage Gen1 mit Azure Synapse Analytics
Sie können PolyBase verwenden, um Daten aus Data Lake Storage Gen1 in Azure Synapse Analytics zu laden. Weitere Informationen finden Sie unter [Verwenden von Data Lake Storage Gen1 mit Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Verwenden von Data Lake Storage Gen1 mit Azure Event Hubs
Sie können Azure Data Lake Storage Gen1 zum Archivieren und Erfassen von Daten verwenden, die von Azure Event Hubs empfangen werden. Weitere Informationen finden Sie unter [Verwenden von Data Lake Storage Gen1 mit Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Erste Schritte mit Data Lake Storage Gen1 unter Verwendung des Portals](data-lake-store-get-started-portal.md)
* [Erste Schritte mit Data Lake Storage Gen1 unter Verwendung von PowerShell](data-lake-store-get-started-powershell.md)