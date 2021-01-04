---
title: Datenszenarien mit Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zu den verschiedenen Szenarien und Tools, in bzw. mit denen Daten in Data Lake Storage Gen1 (ehemals Azure Data Lake Store) erfasst, verarbeitet, heruntergeladen und visualisiert werden können.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 702a59f768bfe978e1a7708d91c6b6b1bb9c92fe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452242"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Verwenden von Azure Data Lake Storage Gen1 für Big Data-Anforderungen

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Es gibt vier wichtige Phasen in der Big Data-Verarbeitung:

* Erfassung von großen Datenmengen in einem Datenspeicher, in Echtzeit oder in Batches
* Verarbeiten der Daten
* Herunterladen der Daten
* Visualisieren der Daten

In diesem Artikel betrachten wir diese Phasen in Bezug auf Azure Data Lake Storage Gen1, um die Optionen und Tools kennenzulernen, die zur Erfüllung Ihrer Big Data-Anforderungen verfügbar sind.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Erfassen von Daten in Data Lake Storage Gen1
In diesem Abschnitt werden die unterschiedlichen Quellen von Daten sowie die verschiedenen Methoden hervorgehoben, mit denen Daten in einem Data Lake Storage Gen1-Konto erfasst werden können.

![Erfassen von Daten in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Erfassen von Daten in Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Ad-hoc-Daten:
Dies steht für kleinere Datasets, die zum Erstellen von Prototypen einer Big Data-Anwendung verwendet werden. Es gibt, abhängig von der Quelle der Daten, verschiedene Möglichkeiten zum Erfassen von Ad-hoc-Daten.

| Data source | Erfassen Sie mit |
| --- | --- |
| Lokalem Computer |<ul> <li>[Azure portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)</li> <li>[Verwenden von Data Lake-Tools für Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage-Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy-Tool](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Ausführung von DistCp auf einem HDInsight-Cluster](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Streamingdaten
Diese Daten können von verschiedenen Quellen wie Anwendungen, Geräten, Sensoren etc. generiert werden. Diese Daten können in Data Lake Storage Gen1 von verschiedenen Tools erfasst werden. Diese Tools erfassen und verarbeiten die Daten in der Regel Ereignis für Ereignis in Echtzeit und schreiben dann die Ereignisse stapelweise in Data Lake Storage Gen1, damit sie weiterverarbeitet werden können.

Folgende Tools können Sie verwenden:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md): In Event Hubs erfasste Ereignisse können mithilfe einer Azure Data Lake Storage Gen1-Ausgabe in Azure Data Lake Storage Gen1 geschrieben werden.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md): Daten aus dem Storm-Cluster können direkt in Data Lake Storage Gen1 geschrieben werden.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md): Sie können Ereignisse von Event Hubs empfangen und dann mit dem [Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md) in Data Lake Storage Gen1 schreiben.

### <a name="relational-data"></a>Relationale Daten
Sie können auch Daten aus relationalen Datenbanken erfassen. Relationale Datenbanken sammeln über einen Zeitraum hinweg umfangreiche Datenmengen, die bei Verarbeitung durch eine Big Data-Pipeline wichtige Einblicke bieten können. Mit den folgenden Tools können Sie solche Daten in Data Lake Storage Gen1 verschieben.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webserverprotokolldaten (mit benutzerdefinierten Anwendungen hochladen)
Auf diesen Datasettyp wird besonders hingewiesen, da die Analyse von Webserverprotokolldaten eine gängige Praxis für Big Data-Anwendungen ist und voraussetzt, dass zahlreiche Protokolldateien in Data Lake Storage Gen1 hochgeladen werden. Sie können jedes der folgenden Tools nutzen, um eigene Skripts oder Anwendungen zum Hochladen solcher Daten zu schreiben.

* [Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Für das Hochladen von Webserverprotokolldaten und auch anderer Arten von Daten (z. B. soziale Stellungnahmen) ist es ein guter Ansatz, wenn Sie Ihre eigenen benutzerdefinierten Skripts/Anwendungen schreiben, denn dies gibt Ihnen die Flexibilität, Ihre Datenhochladekomponente als Teil in Ihre Big Data-Anwendung einzubeziehen. In einigen Fällen kann dieser Code die Form eines Skripts oder eines einfachen Befehlszeilenprogramms annehmen. In anderen Fällen kann der Code verwendet werden, die Big Data-Verarbeitung in eine Geschäftsanwendung oder -lösung zu integrieren.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Mit Azure HDInsight-Clustern verknüpfte Daten
Die meisten HDInsight-Clustertypen (Hadoop, HBase, Storm) unterstützen Data Lake Storage Gen1 als Datenspeicherrepository. HDInsight-Cluster greifen aus Azure Storage-Blobs (WASB) auf Daten zu. Zur Verbesserung der Leistung können Sie die Daten aus WASB in ein mit dem Cluster verknüpftes Data Lake Storage Gen1-Konto kopieren. Mit den folgenden Tools können Sie die Daten kopieren.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy-Dienst](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>In lokalen oder IaaS Hadoop-Clustern gespeicherte Daten
Große Datenmengen können in vorhandenen Hadoop-Clustern oder lokal auf Computern gespeichert werden, die HDFS verwenden. Die Hadoop-Cluster können sich in einer lokalen Bereitstellung oder innerhalb eines IaaS-Clusters in Azure befinden. Möglicherweise bestehen Anforderungen für das einmalige oder regelmäßige Kopieren solcher Daten in Azure Data Lake Storage Gen1. Hierfür stehen verschiedene Möglichkeiten zur Verfügung. Im Folgenden finden Sie eine Liste der Alternativen und die jeweiligen Vor- und Nachteile.

| Vorgehensweise | Details | Vorteile | Überlegungen |
| --- | --- | --- | --- |
| Verwenden Sie Azure Data Factory (ADF), um Daten direkt aus Hadoop-Clustern in Azure Data Lake Storage Gen1 zu kopieren. |[ADF unterstützt HDFS als Datenquelle.](../data-factory/connector-hdfs.md) |ADF bietet sofortige Unterstützung für HDFS und erstklassige End-to-End-Verwaltung und -Überwachung. |Ein Datenverwaltungsgateway muss lokal oder im IaaS-Cluster bereitgestellt werden. |
| Exportieren Sie Daten in Form von Dateien aus Hadoop. Kopieren Sie die Dateien dann mit einer geeigneten Methode in Azure Data Lake Storage Gen1. |Sie können Dateien mithilfe der folgenden Komponenten in Azure Data Lake Storage Gen1 kopieren: <ul><li>[Azure PowerShell für Windows-Betriebssystem](data-lake-store-get-started-powershell.md)</li><li>[Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)</li><li>Benutzerdefinierte App mit einem Data Lake Storage Gen1-SDK</li></ul> |Lässt sich schnell einrichten. Benutzerdefinierte Uploads sind möglich. |Der Prozess erfordert mehrere Schritte und verschiedene Technologien. Da die Tools benutzerdefiniert sind, werden Verwaltung und Überwachung im Lauf der Zeit schwierig. |
| Verwenden Sie Distcp, um Daten von Hadoop in Azure Storage zu kopieren. Kopieren Sie die Daten dann mit einer geeigneten Methode aus Azure Storage in Data Lake Storage Gen1. |Sie können Daten mithilfe der folgenden Komponenten aus Azure Storage in Data Lake Storage Gen1 kopieren: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy-Tool](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Ausführung von Apache DistCp auf HDInsight-Clustern](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Sie können Open Source-Tools verwenden. |Der Prozess erfordert mehrere Schritte und verschiedene Technologien. |

### <a name="really-large-datasets"></a>Sehr große Datasets
Das Hochladen von Datasets im Bereich mehrerer Terabyte kann mithilfe der oben beschriebenen Methoden manchmal langsam und kostspielig sein. In solchen Fällen können Sie die folgenden Optionen verwenden:

* **Verwenden von Azure ExpressRoute**. Azure ExpressRoute ermöglicht Ihnen, private Verbindungen zwischen Azure-Rechenzentren und Ihrer lokalen Infrastruktur zu erstellen. Dies ist eine zuverlässige Option zur Übertragung großer Datenmengen. Weitere Informationen finden Sie in der [Dokumentation zu Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Offline-Datenupload**. Wenn Azure ExpressRoute aus irgendeinem Grund nicht verwendet werden kann, können Sie über den [Azure Import/Export-Dienst](../storage/common/storage-import-export-service.md) Festplattenlaufwerke mit Ihren Daten an ein Azure-Rechenzentrum senden. Ihre Daten werden zunächst in Azure Storage-Blobs hochgeladen. Anschließend können Sie mit [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) oder dem [AdlCopy-Tool](data-lake-store-copy-data-azure-storage-blob.md) Daten aus Azure Storage-Blobs in Data Lake Storage Gen1 kopieren.

  > [!NOTE]
  > Wenn Sie den Import/Export-Dienst verwenden, sollte die Größe der Dateien auf den Datenträgern, die Sie an das Azure-Rechenzentrum senden, nicht größer als 195 GB sein.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Verarbeiten von in Data Lake Storage Gen1 gespeicherten Daten
Sobald die Daten in Data Lake Storage Gen1 verfügbar sind, können Sie mit den unterstützten Big Data-Anwendungen Analysen für diese Daten ausführen. Derzeit können Sie mit Azure HDInsight und Azure Data Lake Analytics Datenanalyseaufträge für die in Data Lake Storage Gen1 gespeicherten Daten ausführen.

![Analysieren von Daten in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analysieren von Daten in Data Lake Storage Gen1")

Betrachten Sie die folgenden Beispiele.

* [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 als Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Herunterladen von Daten aus Data Lake Storage Gen1
Vielleicht möchten Sie auch für Szenarien wie die folgenden Daten aus Azure Data Lake Storage Gen1 herunterladen oder verschieben:

* Verschieben von Daten aus anderen Repositorys zur Verbindung mit Ihren vorhandenen Datenverarbeitungspipelines. Sie möchten z. B. Daten aus Data Lake Storage Gen1 in Azure SQL-Datenbank oder SQL Server verschieben.
* Herunterladen von Daten auf Ihren lokalen Computer für die Verarbeitung in IDE-Umgebungen beim Erstellen von Anwendungsprototypen.

![Ausgehende Daten aus Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Ausgehende Daten aus Data Lake Storage Gen1")

In solchen Fällen können Sie eine der folgenden Optionen verwenden:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

Sie können auch mit den folgenden Methoden Ihr eigenes Skript/Ihre eigene Anwendung zum Herunterladen von Daten aus Data Lake Storage Gen1 schreiben.

* [Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Visualisieren von Daten in Data Lake Storage Gen1
Sie können eine Kombination aus Diensten verwenden, um visuelle Darstellungen von in Data Lake Storage Gen1 gespeicherten Daten zu erstellen.

![Visualisieren von Daten in Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Visualisieren von Daten in Data Lake Storage Gen1")

* Sie können beginnen, indem Sie [Daten mithilfe von Azure Data Factory aus Data Lake Storage Gen1 nach Azure Synapse Analytics verschieben](../data-factory/copy-activity-overview.md)
* Danach können Sie [Power BI in Azure Synapse Analytics integrieren](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect), um eine visuelle Darstellung der Daten zu erstellen.