---
title: Datenszenarien mit Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zu den verschiedenen Szenarien und Tools, in bzw. mit denen Daten in Data Lake Storage Gen2 (ehemals Azure Data Lake Store) erfasst, verarbeitet, heruntergeladen und visualisiert werden können.
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: normesta
ms.openlocfilehash: c5b6287757f6b71cfd60687f463673f142db04d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939294"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Verwenden von Azure Data Lake Storage Gen2 für Big Data-Anforderungen

Es gibt vier wichtige Phasen in der Big Data-Verarbeitung:

> [!div class="checklist"]
> * Erfassung von großen Datenmengen in einem Datenspeicher, in Echtzeit oder in Batches
> * Verarbeiten der Daten
> * Herunterladen der Daten
> * Visualisieren der Daten

Erstellen Sie zunächst ein Speicherkonto und ein Dateisystem. Gewähren Sie dann Zugriff auf die Daten. Die ersten Abschnitte in diesem Artikel unterstützen Sie bei der Ausführung dieser Aufgaben. In den verbleibenden Abschnitten werden wir die Optionen und Tools für jede Verarbeitungsphase hervorheben.

## <a name="create-a-data-lake-storage-gen2-account"></a>Erstellen eines Data Lake Storage Gen2-Kontos

Ein Data Lake Storage Gen2-Konto ist ein Speicherkonto mit einem hierarchischen Namespace. 

Informationen zur Erstellung finden Sie unter [Schnellstart: Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Erstellen eines Dateisystems

Ein *Dateisystem* ist ein Container für Ordner und Dateien. Sie benötigen mindestens eines davon, um mit dem Erfassen von Daten in Ihrem Speicherkonto zu beginnen.  Hier ist eine Liste der Tools, die Sie zur Erstellung verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure Storage-Explorer | [Erstellen eines Dateisystems mithilfe von Storage-Explorer](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-filesystem) |
|AzCopy | [Erstellen eines Blobcontainers oder einer Dateifreigabe mithilfe von AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-blob-container-or-file-share)|
|Befehlszeilenschnittstelle (CLI) des Hadoop Distributed File System mit HDInsight |[Erstellen eines Dateisystems mithilfe von HDFS mit HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Code in einem Azure Databricks-Notebook|[Erstellen des Dateisystems für das Speicherkonto (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Erstellen und Einbinden eines Dateisystems (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

Dateisysteme lassen sich am einfachsten mit Storage-Explorer oder AzCopy erstellen. Die Erstellung von Dateisystemen mit HDInsight und Databricks erfordert etwas mehr Arbeit. Wenn Sie jedoch planen, HDInsight- oder Databricks-Cluster zur Verarbeitung Ihrer Daten zu verwenden, dann können Sie zuerst Ihre Cluster erstellen und die HDFS-CLI für Ihre Dateisysteme verwenden.  

## <a name="grant-access-to-the-data"></a>Gewähren von Zugriff auf die Daten

Richten Sie die entsprechenden Zugriffsberechtigungen für Ihr Konto und die Daten in Ihrem Konto ein, bevor Sie mit der Datenerfassung beginnen.

Es gibt drei Möglichkeiten, um Zugriff zu gewähren:

* Weisen Sie eine dieser Rollen einem Benutzer, einer Gruppe, einer vom Benutzer verwalteten Identität oder einem Dienstprinzipal zu:

  [Leser von Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader-preview)

  [Mitwirkender an Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor-preview)

  [Besitzer von Speicherblobdaten](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner-preview)

* Verwenden Sie ein SAS-Token (Shared Access Signature).

* Verwenden Sie einen Speicherkontoschlüssel.

Diese Tabelle zeigt, wie Sie den Zugriff für jeden Azure-Dienst oder jedes Tool gewähren können.

|Tool | Gewähren von Zugriff | Anleitungen |
|---|--|---|
|Storage-Explorer| Zuweisen einer Rolle zu Benutzern und Gruppen | [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Zuweisen einer Rolle zu Benutzern und Gruppen <br>**or**<br> Verwenden eines SAS-Token| [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Einfaches Erstellen einer SAS zum Herunterladen einer Datei aus Azure Storage – mit dem Azure Storage-Explorer](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität | [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität<br>**or**<br> Zuweisen einer Rolle zu einem Dienstprinzipal<br>**or**<br> Verwenden eines Speicherkontoschlüssels | [Eigenschaften des verknüpften Diensts](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Zuweisen einer Rolle zu einer vom Benutzer zugewiesenen verwalteten Identität | [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Zuweisen einer Rolle zu einem Dienstprinzipal | [Gewusst wie: Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

In diesen Artikeln finden Sie Informationen zum Gewähren von Zugriff auf eine spezifische Datei oder Ordner.

* [Festlegen von Berechtigungen auf Datei- und Verzeichnisebene mithilfe des Azure Storage-Explorers mit Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Zugriffssteuerungslisten für Dateien und Verzeichnisse](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Informationen zum Einrichten von anderen Aspekte der Sicherheit finden Sie unter [Sicherheitshandbuch für Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Erfassen der Daten

In diesem Abschnitt werden die unterschiedlichen Quellen von Daten sowie die verschiedenen Methoden hervorgehoben, mit denen Daten in einem Data Lake Storage Gen2-Konto erfasst werden können.

![Erfassen von Daten in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Erfassen von Daten in Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Ad-hoc-Daten:

Dies steht für kleinere Datasets, die zum Erstellen von Prototypen einer Big Data-Anwendung verwendet werden. Es gibt, abhängig von der Quelle der Daten, verschiedene Möglichkeiten zum Erfassen von Ad-hoc-Daten. 

Hier ist eine Liste der Tools, die Sie zum Erfassen von Ad-hoc-Daten verwenden können.

| Data source | Erfassen Sie mit |
| --- | --- |
| Lokalem Computer |[Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Das Tool AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage-Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Das Tool AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[Ausführung von DistCp auf einem HDInsight-Cluster](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Streamingdaten

Diese Daten können von verschiedenen Quellen wie Anwendungen, Geräten, Sensoren etc. generiert werden. Diese Daten können in Data Lake Storage Gen2 von verschiedenen Tools erfasst werden. Diese Tools erfassen und verarbeiten die Daten in der Regel ereignisbezogen in Echtzeit und schreiben dann die Ereignisse batchweise in Data Lake Storage Gen2, damit sie weiterverarbeitet werden können.

Hier ist eine Liste der Tools, die Sie zum Erfassen von gestreamten Daten verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure HDInsight Storm | [Schreiben in Apache Hadoop HDFS aus Apache Storm auf HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Relationale Daten

Sie können auch Daten aus relationalen Datenbanken erfassen. Relationale Datenbanken sammeln über einen Zeitraum hinweg umfangreiche Datenmengen, die bei Verarbeitung durch eine Big Data-Pipeline wichtige Einblicke bieten können. Mit den folgenden Tools können Sie solche Daten in Data Lake Storage Gen2 verschieben.

Hier ist eine Liste der Tools, die Sie zum Erfassen von relationalen Daten verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure Data Factory | [Kopieraktivität in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserverprotokolldaten (mit benutzerdefinierten Anwendungen hochladen)

Auf diesen Typ von Dataset wird besonders hingewiesen, da die Analyse von Webserverprotokolldaten eine gängige Praxis für Big Data-Anwendungen ist und voraussetzt, dass zahlreiche Protokolldateien in Data Lake Storage Gen2 hochgeladen werden. Sie können jedes der folgenden Tools nutzen, um eigene Skripts oder Anwendungen zum Hochladen solcher Daten zu schreiben.

Hier ist eine Liste der Tools, die Sie zum Erfassen von Webserverprotokolldaten verwenden können.

|Tool | Anleitungen |
|---|--|
|Azure Data Factory | [Kopieraktivität in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Für das Hochladen von Webserverprotokolldaten und auch anderer Arten von Daten (z. B. soziale Stellungnahmen) ist es ein guter Ansatz, wenn Sie Ihre eigenen benutzerdefinierten Skripts/Anwendungen schreiben, denn dies gibt Ihnen die Flexibilität, Ihre Datenhochladekomponente als Teil in Ihre Big Data-Anwendung einzubeziehen. In einigen Fällen kann dieser Code die Form eines Skripts oder eines einfachen Befehlszeilenprogramms annehmen. In anderen Fällen kann der Code verwendet werden, die Big Data-Verarbeitung in eine Geschäftsanwendung oder -lösung zu integrieren.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Mit Azure HDInsight-Clustern verknüpfte Daten

Die meisten HDInsight-Clustertypen (Hadoop, HBase, Storm) unterstützen Data Lake Storage Gen2 als Datenspeicherrepository. HDInsight-Cluster greifen aus Azure Storage-Blobs (WASB) auf Daten zu. Zur Verbesserung der Leistung können Sie die Daten aus WASB in ein mit dem Cluster verknüpftes Data Lake Storage Gen2-Konto kopieren. Mit den folgenden Tools können Sie die Daten kopieren.

Hier ist eine Liste von Tools, die Sie zum Erfassen von Daten im Zusammenhang mit HDInsight-Clustern verwenden können.

|Tool | Anleitungen |
|---|--|
|Apache DistCp | [Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen2 mit DistCp](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Das Tool AzCopy | [Übertragen von Daten mit AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

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
|Azure HDInsight | [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Schnellstart: Analysieren von Daten in Azure Data Lake Storage Gen2 mit Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualisieren der Daten

Sie können eine Kombination aus Diensten verwenden, um visuelle Darstellungen von in Data Lake Storage Gen2 gespeicherten Daten zu erstellen.

![Visualisieren von Daten in Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualisieren von Daten in Data Lake Storage Gen2")

* Sie können beginnen, indem Sie [Daten mithilfe von Azure Data Factory aus Data Lake Storage Gen2 nach Azure SQL Data Warehouse verschieben](../../data-factory/copy-activity-overview.md)
* Danach können Sie [Power BI in Azure SQL Data Warehouse integrieren](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) , um eine visuelle Darstellung der Daten zu erstellen.

## <a name="download-the-data"></a>Herunterladen der Daten

Ggf. möchten Sie auch für Szenarien wie die folgenden Daten aus Azure Data Lake Storage Gen2 herunterladen oder verschieben:

* Verschieben von Daten aus anderen Repositorys zur Verbindung mit Ihren vorhandenen Datenverarbeitungspipelines. Sie möchten z. B. Daten aus Data Lake Storage Gen2 in die Azure SQL-Datenbank oder auf eine lokale SQL Server-Instanz verschieben.

* Herunterladen von Daten auf Ihren lokalen Computer für die Verarbeitung in IDE-Umgebungen beim Erstellen von Anwendungsprototypen.

![Ausgeben von Daten aus Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Ausgeben von Daten aus Data Lake Storage Gen2")

Hier ist eine Liste von Tools, mit denen Sie Daten von Data Lake Storage Gen2 herunterladen können.

|Tool | Anleitungen |
|---|--|
|Azure Data Factory | [Kopieraktivität in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCop | [Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen2 mit DistCp](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
