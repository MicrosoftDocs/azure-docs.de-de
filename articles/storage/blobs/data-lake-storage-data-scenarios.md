---
title: Datenszenarien mit Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zu den verschiedenen Szenarien und Tools, in bzw. mit denen Daten in Data Lake Storage Gen2 (ehemals Azure Data Lake Store) erfasst, verarbeitet, heruntergeladen und visualisiert werden können.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1d22a992ac4ae69c8541b6efbaee58340f48caa4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517874"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Verwenden von Azure Data Lake Storage Gen2 für Big Data-Anforderungen

Es gibt vier wichtige Phasen in der Big Data-Verarbeitung:

> [!div class="checklist"]
> * Erfassung von großen Datenmengen in einem Datenspeicher, in Echtzeit oder in Batches
> * Verarbeiten der Daten
> * Herunterladen der Daten
> * Visualisieren der Daten

In diesem Artikel werden die Optionen und Tools für die einzelnen Verarbeitungsphasen behandelt.

Eine umfassende Liste der Azure-Dienste, die Sie mit Azure Data Lake Storage Gen2 verwenden können, finden Sie unter [Integrieren von Azure Data Lake Storage mit Azure-Diensten](./data-lake-storage-supported-azure-services.md).

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Erfassen der Daten in Data Lake Storage Gen2

In diesem Abschnitt werden die unterschiedlichen Quellen von Daten sowie die verschiedenen Methoden hervorgehoben, mit denen Daten in einem Data Lake Storage Gen2-Konto erfasst werden können.

![Erfassen von Daten in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Erfassen von Daten in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad-hoc-Daten:

Dies steht für kleinere Datasets, die zum Erstellen von Prototypen einer Big Data-Anwendung verwendet werden. Es gibt, abhängig von der Quelle der Daten, verschiedene Möglichkeiten zum Erfassen von Ad-hoc-Daten. 

Hier ist eine Liste der Tools, die Sie zum Erfassen von Ad-hoc-Daten verwenden können.

| Data source | Erfassen Sie mit |
| --- | --- |
| Lokalem Computer |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Das Tool AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage-Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Das Tool AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[Ausführung von DistCp auf einem HDInsight-Cluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamingdaten

Diese Daten können von verschiedenen Quellen wie Anwendungen, Geräten, Sensoren etc. generiert werden. Diese Daten können in Data Lake Storage Gen2 von verschiedenen Tools erfasst werden. Diese Tools erfassen und verarbeiten die Daten in der Regel ereignisbezogen in Echtzeit und schreiben dann die Ereignisse batchweise in Data Lake Storage Gen2, damit sie weiterverarbeitet werden können.

Hier ist eine Liste der Tools, die Sie zum Erfassen von gestreamten Daten verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure Stream Analytics|[Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Blobspeicher und Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [Schreiben in Apache Hadoop HDFS aus Apache Storm auf HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Relationale Daten

Sie können auch Daten aus relationalen Datenbanken erfassen. Relationale Datenbanken sammeln über einen Zeitraum hinweg umfangreiche Datenmengen, die bei Verarbeitung durch eine Big Data-Pipeline wichtige Einblicke bieten können. Mit den folgenden Tools können Sie solche Daten in Data Lake Storage Gen2 verschieben.

Hier ist eine Liste der Tools, die Sie zum Erfassen von relationalen Daten verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure Data Factory | [Kopieraktivität in Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserverprotokolldaten (mit benutzerdefinierten Anwendungen hochladen)

Auf diesen Typ von Dataset wird besonders hingewiesen, da die Analyse von Webserverprotokolldaten eine gängige Praxis für Big Data-Anwendungen ist und voraussetzt, dass zahlreiche Protokolldateien in Data Lake Storage Gen2 hochgeladen werden. Sie können jedes der folgenden Tools nutzen, um eigene Skripts oder Anwendungen zum Hochladen solcher Daten zu schreiben.

Hier ist eine Liste der Tools, die Sie zum Erfassen von Webserverprotokolldaten verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure Data Factory | [Kopieraktivität in Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure-Befehlszeilenschnittstelle](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Für das Hochladen von Webserverprotokolldaten und auch anderer Arten von Daten (z. B. soziale Stellungnahmen) ist es ein guter Ansatz, wenn Sie Ihre eigenen benutzerdefinierten Skripts/Anwendungen schreiben, denn dies gibt Ihnen die Flexibilität, Ihre Datenhochladekomponente als Teil in Ihre Big Data-Anwendung einzubeziehen. In einigen Fällen kann dieser Code die Form eines Skripts oder eines einfachen Befehlszeilenprogramms annehmen. In anderen Fällen kann der Code verwendet werden, die Big Data-Verarbeitung in eine Geschäftsanwendung oder -lösung zu integrieren.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Mit Azure HDInsight-Clustern verknüpfte Daten

Die meisten HDInsight-Clustertypen (Hadoop, HBase, Storm) unterstützen Data Lake Storage Gen2 als Datenspeicherrepository. HDInsight-Cluster greifen aus Azure Storage-Blobs (WASB) auf Daten zu. Zur Verbesserung der Leistung können Sie die Daten aus WASB in ein mit dem Cluster verknüpftes Data Lake Storage Gen2-Konto kopieren. Mit den folgenden Tools können Sie die Daten kopieren.

Hier ist eine Liste von Tools, die Sie zum Erfassen von Daten im Zusammenhang mit HDInsight-Clustern verwenden können.

|Tool | Anleitungen |
|---|--|
|Apache DistCp | [Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen2 mit DistCp](./data-lake-storage-use-distcp.md) |
|Das Tool AzCopy | [Übertragen von Daten mit AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>In lokalen oder IaaS Hadoop-Clustern gespeicherte Daten

Große Datenmengen können in vorhandenen Hadoop-Clustern oder lokal auf Computern gespeichert werden, die HDFS verwenden. Die Hadoop-Cluster können sich in einer lokalen Bereitstellung oder innerhalb eines IaaS-Clusters in Azure befinden. Möglicherweise bestehen Anforderungen für das einmalige oder regelmäßige Kopieren solcher Daten in Azure Data Lake Storage Gen2. Hierfür stehen verschiedene Möglichkeiten zur Verfügung. Im Folgenden finden Sie eine Liste der Alternativen und die jeweiligen Vor- und Nachteile.

| Vorgehensweise | Details | Vorteile | Überlegungen |
| --- | --- | --- | --- |
| Verwenden von Azure Data Factory (ADF), um Daten direkt aus Hadoop-Clustern in Azure Data Lake Storage Gen2 zu kopieren |[ADF unterstützt HDFS als Datenquelle.](../../data-factory/connector-hdfs.md) |ADF bietet sofortige Unterstützung für HDFS und erstklassige End-to-End-Verwaltung und -Überwachung. |Ein Datenverwaltungsgateway muss lokal oder im IaaS-Cluster bereitgestellt werden. |
| Verwenden Sie Distcp, um Daten von Hadoop in Azure Storage zu kopieren. Kopieren Sie die Daten dann mit einer geeigneten Methode aus Azure Storage in Data Lake Storage Gen2. |Sie können Daten mithilfe der folgenden Komponenten aus Azure Storage in Data Lake Storage Gen2 kopieren: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Das Tool AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Ausführung von Apache DistCp auf HDInsight-Clustern](data-lake-storage-use-distcp.md)</li></ul> |Sie können Open Source-Tools verwenden. |Der Prozess erfordert mehrere Schritte und verschiedene Technologien. |

### <a name="really-large-datasets"></a>Sehr große Datasets

Das Hochladen von Datasets im Bereich mehrerer Terabyte kann mithilfe der oben beschriebenen Methoden manchmal langsam und kostspielig sein. In solchen Fällen können Sie Azure ExpressRoute verwenden.  

Azure ExpressRoute ermöglicht Ihnen, private Verbindungen zwischen Azure-Rechenzentren und Ihrer lokalen Infrastruktur zu erstellen. Dies ist eine zuverlässige Option zur Übertragung großer Datenmengen. Weitere Informationen finden Sie in der [Dokumentation zu Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Verarbeiten der Daten

Sobald die Daten in Data Lake Storage Gen2 verfügbar sind, können Sie mit den unterstützten Big Data-Anwendungen Analysen für diese Daten ausführen. 

![Analysieren von Daten in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analysieren von Daten in Data Lake Storage Gen2")

Hier ist eine Liste von Tools, mit denen Sie Datenanalyseaufträge für Daten ausführen können, die in Data Lake Storage Gen2 gespeichert sind.

|Tool | Anleitungen |
|---|--|
|Azure HDInsight | [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[Schnellstart: Analysieren von Daten in Azure Data Lake Storage Gen2 mit Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualisieren der Daten

Verwenden Sie den Power BI-Connector, um visuelle Darstellungen von in Data Lake Storage Gen2 gespeicherten Daten zu erstellen. Informationen finden Sie unter [Analysieren von Daten in Azure Data Lake Storage Gen2 mit Power BI](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Herunterladen der Daten

Ggf. möchten Sie auch für Szenarien wie die folgenden Daten aus Azure Data Lake Storage Gen2 herunterladen oder verschieben:

* Verschieben von Daten aus anderen Repositorys zur Verbindung mit Ihren vorhandenen Datenverarbeitungspipelines. Sie möchten z. B. Daten aus Data Lake Storage Gen2 in die Azure SQL-Datenbank oder eine SQL Server-Instanz verschieben.

* Herunterladen von Daten auf Ihren lokalen Computer für die Verarbeitung in IDE-Umgebungen beim Erstellen von Anwendungsprototypen.

![Ausgang von Daten aus Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Ausgang von Daten aus Data Lake Storage Gen2")

Hier ist eine Liste von Tools, mit denen Sie Daten von Data Lake Storage Gen2 herunterladen können.

|Tool | Anleitungen |
|---|--|
|Azure Data Factory | [Kopieraktivität in Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen2 mit DistCp](./data-lake-storage-use-distcp.md) |
|Azure Storage-Explorer|[Verwenden von Azure Storage-Explorer zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](data-lake-storage-explorer.md)|
|Das Tool AzCopy|[Übertragen von Daten mit AzCopy und Blobspeicher](../common/storage-use-azcopy-v10.md#transfer-data)|
