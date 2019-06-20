---
title: Kopieren von Daten in Azure Data Lake Storage Gen2 mithilfe von DistCp | Microsoft-Dokumentation
description: Verwenden des Tools DistCp zum Kopieren von Daten in und aus Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: seguler
ms.openlocfilehash: 0e85d2b2c7e9a3022e7fea2063ffa0aa915abb53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939055"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen2 mit DistCp

Sie können [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) verwenden, um Daten zwischen einem Speicherkonto des Typs „Universell V2“ und einem Speicherkonto des Typs „Universell V2“ mit aktiviertem hierarchischen Namespace zu kopieren. Dieser Artikel enthält Anweisungen zur Verwendung des Tools DistCp.

DistCp bietet eine Vielzahl von Befehlszeilenparametern. Wir empfehlen Ihnen dringend, diesen Artikel zu lesen, damit Sie diese optimal nutzen können. Dieser Artikel veranschaulicht die Grundfunktionen und konzentriert sich auf deren Verwendung zum Kopieren von Daten in ein Konto mit aktiviertem hierarchischen Namespace.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein vorhandenes Azure Storage-Konto ohne aktivierte Data Lake Storage Gen2-Funktionen (hierarchischer Namespace)** .
* **Ein Azure Storage-Konto mit aktiviertem Feature Azure Data Lake Storage Gen2**. Anweisungen zum Erstellen eines solchen Speicherkontos finden Sie unter [Erstellen eines Azure Data Lake Storage Gen2-Speicherkontos](data-lake-storage-quickstart-create-account.md).
* **Ein Dateisystem**, das im Speicherkonto mit aktiviertem hierarchischen Namespace erstellt wird.
* **Azure HDInsight-Cluster** mit Zugriff auf ein Speicherkonto mit aktiviertem Data Lake Storage Gen2. Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Verwenden von DistCp in einem HDInsight-Linux-Cluster

Ein HDInsight-Cluster bietet das Hilfsprogramm DistCp, das zum Kopieren von Daten aus verschiedenen Quellen in einen HDInsight-Cluster verwendet werden kann. Wenn Sie den HDInsight-Cluster so konfiguriert haben, dass er Azure Blob Storage und Azure Data Lake Storage zusammen verwendet, kann das Hilfsprogramm DistCp ebenfalls direkt zum Kopieren von Daten zwischen diesen genutzt werden. In diesem Abschnitt wird die Verwendung des Hilfsprogramms DistCp erläutert.

1. Richten Sie eine SSH-Sitzung mit Ihrem HDI-Cluster ein. Informationen hierzu finden Sie unter [Verbinden mit einem Linux-basierten HDInsight-Cluster](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Überprüfen Sie, ob Sie auf Ihr vorhandenes Konto des Typs „Universell V2“ (ohne aktivierten hierarchischen Namespace) zugreifen können.

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    Die Ausgabe sollte eine Liste der Inhalte im Container zeigen.

3. Überprüfen Sie zudem, ob Sie vom Cluster aus auf das Speicherkonto mit aktiviertem hierarchischen Namespace zugreifen können. Führen Sie den folgenden Befehl aus:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    Die Ausgabe sollte eine Liste der Dateien bzw. Ordner im Data Lake Storage-Konto bereitstellen.

4. Verwenden Sie DistCp zum Kopieren von Daten aus WASB in ein Data Lake Storage-Konto.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    Der Befehl kopiert den Inhalt des Ordners **/example/data/gutenberg/** im Blob-Speicher in den Ordner **/myfolder** im Data Lake Storage-Konto.

5. Verwenden Sie DistCp ebenso zum Kopieren von Daten aus einem Data Lake Storage-Konto in Blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    Der Befehl kopiert den Inhalt des Ordners **/myfolder** im Data Lake Store-Konto in den Ordner **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Überlegungen zur Leistung bei der Verwendung von DistCp

Da die kleinste Granularitätseinheit von DistCp eine einzelne Datei ist, ist die maximale Anzahl gleichzeitiger Kopien der wichtigste Parameter für die Data Lake Storage-Optimierung. Die Anzahl gleichzeitiger Kopien entspricht dem Parameter für die Anzahl von Zuordnungen (**m**) in der Befehlszeile. Dieser Parameter gibt die maximale Anzahl von Zuordnungen an, die zum Kopieren von Daten verwendet werden. Der Standardwert ist 20.

**Beispiel**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Wie bestimme ich die Anzahl der zu verwendenden Zuordnungen?

Hier sind einige hilfreiche Informationen zur Vorgehensweise angegeben.

* **Schritt 1: Bestimmen des Gesamtarbeitsspeichers, der für die „standardmäßige“ YARN-App-Warteschlange verfügbar ist**: Der erste Schritt besteht darin, den für die „standardmäßige“ YARN-App-Warteschlange verfügbaren Arbeitsspeicher zu bestimmen. Diese Information finden Sie im dem Cluster zugeordneten Ambari-Portal. Navigieren Sie zu YARN, und zeigen Sie die Konfigurationsregisterkarte an, um den YARN-Arbeitsspeicher zu ermitteln, der für die „standardmäßige“ YARN-App-Warteschlange verfügbar ist. Dies ist der gesamte verfügbare Arbeitsspeicher für den DistCp-Auftrag (der tatsächlich ein MapReduce-Auftrag ist).

* **Schritt 2: Berechnen der Anzahl von Zuordnungen**: Der Wert von **m** entspricht dem Quotienten des YARN-Gesamtarbeitsspeichers dividiert durch die YARN-Containergröße. Die YARN-Containergröße finden Sie ebenfalls im Ambari-Portal. Navigieren Sie zu YARN, zeigen Sie die Konfigurationsregisterkarte an. Die YARN-Containergröße wird in diesem Fenster angezeigt. Die Formel zur Berechnung der Anzahl von Zuordnungen (**m**) sieht wie folgt aus:

        m = (number of nodes * YARN memory for each node) / YARN container size

**Beispiel**

Angenommen, Sie verfügen über einen Cluster mit vier D14v2s-Knoten und möchten 10 TB Daten aus zehn Ordnern übertragen. Jeder Ordner enthält unterschiedliche Datenmengen, und auch die Dateigrößen in den einzelnen Ordnern sind unterschiedlich.

* **Gesamter YARN-Arbeitsspeicher**: Im Ambari-Portal ermitteln Sie, dass der YARN-Arbeitsspeicher für einen D14-Knoten 96 GB beträgt. Für einen Cluster mit vier Knoten beträgt der YARN-Gesamtarbeitsspeicher demnach: 

        YARN memory = 4 * 96GB = 384GB

* **Anzahl von Zuordnungen**: Im Ambari-Portal ermitteln Sie, dass die YARN-Containergröße für einen D14-Clusterknoten 3,072 MB beträgt. Die Anzahl von Zuordnungen beträgt somit:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Wenn andere Anwendungen Arbeitsspeicher verwenden, können Sie festlegen, dass für DistCp nur ein Teil des YARN-Arbeitsspeichers Ihres Clusters verwendet werden soll.

### <a name="copying-large-datasets"></a>Kopieren umfangreicher Datasets

Wenn das zu verschiebende Dataset sehr groß ist (beispielsweise > 1 TB) oder Sie über zahlreiche unterschiedliche Ordner verfügen, empfiehlt sich unter Umständen die Verwendung mehrerer DistCp-Aufträge. Dadurch ergibt sich zwar wahrscheinlich kein Leistungsgewinn, die Aufträge werden jedoch verteilt, sodass im Falle eines Fehlers bei einem Auftrag nicht das gesamte Projekt, sondern lediglich der betroffene Auftrag neu gestartet werden muss.

### <a name="limitations"></a>Einschränkungen

* Zur Optimierung der Leistung versucht DistCp, Zuordnungen mit ähnlicher Größe zu erstellen. Eine höhere Anzahl von Zuordnungen führt nicht unbedingt zu mehr Leistung.

* DistCp ist auf eine einzelne Zuordnung pro Datei beschränkt. Aus diesem Grund sollte die Anzahl von Zuordnungen die Anzahl von Dateien nicht überschreiten. Der Umstand, dass DistCp einer Datei nur eine einzelne Zuordnung zuweisen kann, beschränkt den Umfang der Parallelität, die beim Kopieren großer Dateien verwendet werden kann.

* Wenn Sie über eine geringe Anzahl großer Dateien verfügen, sollten Sie diese in Dateiblöcke mit jeweils 256 MB aufteilen, um das Parallelitätspotenzial zu erhöhen.
