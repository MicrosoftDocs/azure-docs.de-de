---
title: Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight
description: Erfahren Sie, wie Sie Daten für Apache Hadoop-Aufträge in HDInsight hochladen und darauf zugreifen können. Verwenden Sie die klassische Azure-Befehlszeilenschnittstelle, Azure Storage-Explorer, Azure PowerShell, die Hadoop-Befehlszeile oder Sqoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 47262e0dc7247e1d514eeb64512c21ff1ae527ab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534820"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight

HDInsight stellt über Azure Storage und Azure Data Lake Storage ein Hadoop Distributed File System (HDFS) zur Verfügung. Dieser Speicher umfasst Gen1 und Gen2. Azure Storage und Data Lake Storage Gen1 und Gen2 sind als HDFS-Erweiterungen konzipiert. Der vollständige Satz von Komponenten in der Hadoop-Umgebung kann direkt für die damit verwalteten Daten verwendet werden. Azure Storage, Data Lake Storage Gen1 und Gen2 sind unterschiedliche Dateisysteme. Die Systeme sind für die Speicherung von Daten und für Berechnungen mit diesen Daten optimiert. Die Vorteile der Verwendung von Azure Storage werden unter [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md) beschrieben. Weitere Informationen finden Sie auch unter [Verwenden von Data Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md) und [Verwenden von Data Lake Storage Gen2 mit HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Voraussetzungen

Beachten Sie die folgenden Anforderungen, bevor Sie beginnen:

* Ein Azure HDInsight-Cluster. Eine Anleitung finden Sie unter [Schnellstart: Erstellen eines Apache Hadoop-Clusters in Azure HDInsight mit einer Resource Manager-Vorlage](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Kenntnis der folgenden Artikel:
    * [Verwenden von Azure Storage mit HDInsight](hdinsight-hadoop-use-blob-storage.md)
    * [Verwenden von Data Lake Storage Gen1 mit HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Verwenden von Data Lake Storage Gen2 mit HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Hochladen von Daten in Azure Storage

### <a name="utilities"></a>Versorgungsunternehmen

Microsoft bietet die folgenden Hilfsprogramme für die Arbeit mit Azure Storage:

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-Befehlszeilenschnittstelle](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop-Befehl](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> Der Hadoop-Befehl ist nur für den HDInsight-Cluster verfügbar. Mit dem Befehl können nur Daten aus dem lokalen Dateisystem in Azure Storage geladen werden.  

### <a name="hadoop-command-line"></a>Hadoop-Befehlszeile

Die Hadoop-Befehlszeile eignet sich nur dann zum Speichern von Daten in Azure Storage Blob, wenn die Daten bereits auf dem Hauptknoten des Clusters vorhanden sind.

Um den Hadoop-Befehl verwenden zu können, müssen Sie zunächst mithilfe von [SSH oder PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md) eine Verbindung mit dem Hauptknoten herstellen.

Nachdem die Verbindung hergestellt wurde, verwenden Sie die folgende Syntax, um eine Datei in den Speicher hochzuladen.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Zum Beispiel, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Da sich das Standarddateisystem für HDInsight in Azure Storage befindet, befindet sich die Datei „/example/data/data.txt“ auch tatsächlich in Azure Storage. Sie können auch folgendermaßen auf die Datei verweisen:

`wasbs:///example/data/data.txt`

oder

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

Eine Liste mit weiteren Hadoop-Befehlen, die für Dateien verwendet werden können, finden Sie unter [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html).

> [!WARNING]  
> In Apache HBase-Clustern beträgt die standardmäßige Blockgröße beim Schreiben von Daten 256 KB. Bei Verwendung von HBase-APIs oder REST-APIs funktioniert dies einwandfrei. Die Nutzung der Befehle `hadoop` oder `hdfs dfs` zum Schreiben von Daten mit einem Umfang von mehr als ca. 12 GB führt allerdings zu einem Fehler. Weitere Informationen finden Sie unter [Speicherausnahme beim Schreiben in ein Blob](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Clients mit grafischer Benutzeroberfläche

Es gibt auch einige Anwendungen, die eine grafische Benutzeroberfläche für die Arbeit mit Azure Storage bereitstellen. In der folgenden Tabelle sind einige dieser Anwendungen aufgelistet:

| Client | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio-Tools für HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage-Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [CloudBerry Explorer für Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Einbinden von Azure Storage als lokales Laufwerk

Siehe [Mount Azure Storage as Local Drive](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive) (Einbinden von Azure Storage als lokales Laufwerk).

## <a name="upload-using-services"></a>Hochladen mithilfe von Diensten

### <a name="azure-data-factory"></a>Azure Data Factory

Der Azure Data Factory-Dienst ist ein vollständig verwalteter Dienst für das Kombinieren von Daten: Speicherung, Verarbeitung und Verschiebung von Diensten in optimierte, anpassbare und zuverlässige Datenproduktionspipelines.

|Speichertyp|Dokumentation|
|----|----|
|Azure Blob Storage|[Kopieren von Daten nach oder aus Azure Blob Storage mit Azure Data Factory](../data-factory/connector-azure-blob-storage.md)|
|Azure Data Lake Storage Gen1|[Kopieren von Daten nach und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|Azure Data Lake Storage Gen2 |[Laden von Daten in Azure Data Lake Storage Gen2 mit Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

Sqoop ist ein Tool zum Übertragen von Daten zwischen Hadoop und relationalen Datenbanken. Verwenden Sie es, um Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle zu importieren. Importieren Sie sie dann in das Hadoop Distributed File System (HDFS). Transformieren Sie die Daten in Hadoop mit MapReduce oder Hive, und exportieren Sie die Daten dann wieder in ein RDBMS.

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight](hadoop/hdinsight-use-sqoop.md).

### <a name="development-sdks"></a>Entwicklungs-SDKs

Auf Azure Storage kann auch mithilfe eines Azure-SDK über die folgenden Programmiersprachen zugegriffen werden:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Weitere Informationen zum Installieren der Azure-SDKs finden Sie unter [Azure-Downloads](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Nächste Schritte

Jetzt wissen Sie, wie Sie Daten in HDInsight importieren. Lesen Sie in den folgenden Artikeln, um mehr über die Analyse zu erfahren:

* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Programmgesteuertes Übermitteln von Apache Hadoop-Aufträgen](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)