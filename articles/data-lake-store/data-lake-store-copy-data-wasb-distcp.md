---
title: Kopieren von Daten in und aus WASB in Azure Data Lake Storage Gen1 mit DistCp | Microsoft-Dokumentation
description: Verwenden des Tools DistCp zum Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fbefe233ce0d2477982faf0a9f38a73062e0c7a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878799"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen1 mit DistCp
> [!div class="op_single_selector"]
> * [Verwenden von DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Verwenden von AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Bei einem HDInsight-Cluster mit Zugriff auf Azure Data Lake Storage Gen1 können Sie Tools aus dem Hadoop-Ökosystem wie DistCp verwenden, um Daten aus einem HDInsight-Clusterspeicher (WASB) in ein Data Lake Storage Gen1-fähiges Konto **zu kopieren (und umgekehrt)** . Dieser Artikel enthält Anweisungen zur Verwendung des Distcp-Tools.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Ein Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen1-Konto. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Verwenden von Distcp eines HDInsight-Linux-Clusters

Ein HDInsight-Cluster enthält das Distcp-Dienstprogramm, das zum Kopieren von Daten aus verschiedenen Quellen in einen HDInsight-Cluster verwendet werden kann. Wenn Sie den HDInsight-Cluster so konfiguriert haben, dass er Data Lake Storage Gen1 als zusätzlichen Speicher verwendet, kann das Hilfsprogramm DistCp ebenfalls direkt zum Kopieren von Daten in ein bzw. aus einem Data Lake Storage Gen1-Konto genutzt werden. In diesem Abschnitt wird die Verwendung des Distcp-Dienstprogramms erläutert.

1. Verwenden Sie SSH auf Ihrem Desktop, um eine Verbindung mit dem Cluster herzustellen. Informationen hierzu finden Sie unter [Verbinden mit einem Linux-basierten HDInsight-Cluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Führen Sie die Befehle von der SSH-Eingabeaufforderung aus.

2. Überprüfen Sie, ob Sie auf die Azure Storage-Blobs (WASB) zugreifen können. Führen Sie den folgenden Befehl aus:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Die Ausgabe sollte eine Liste der Inhalte im Speicherblob enthalten.

3. Überprüfen Sie zudem, ob Sie vom Cluster auf das Data Lake Storage Gen1-Konto zugreifen können. Führen Sie den folgenden Befehl aus:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/

    Die Ausgabe sollte eine Liste der Dateien bzw. Ordner im Data Lake Storage Gen1-Konto bereitstellen.

4. Verwenden Sie DistCp zum Kopieren von Daten aus WASB in ein Data Lake Storage Gen1-Konto.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder

    Der Befehl kopiert den Inhalt des Ordners **/example/data/gutenberg/** in WASB in den Ordner **/myfolder** im Data Lake Storage Gen1-Konto.

5. Verwenden Sie DistCp ebenso zum Kopieren von Daten aus einem Data Lake Storage Gen1-Speicherkonto nach WASB.

        hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Der Befehl kopiert den Inhalt des Ordners **/myfolder** im Data Lake Storage Gen1-Konto in den Ordner **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Überlegungen zur Leistung bei der Verwendung von DistCp

Da die kleinste Granularitätseinheit von DistCp eine einzelne Datei ist, stellt die maximale Anzahl gleichzeitiger Kopien den wichtigsten Parameter für die Data Lake Storage Gen1-Optimierung dar. Um die Anzahl gleichzeitiger Kopien zu steuern, wird der Parameter für die Anzahl von Zuordnungen (m) über die Befehlszeile festgelegt. Dieser Parameter gibt die maximale Anzahl von Zuordnungen an, die zum Kopieren von Daten verwendet werden. Der Standardwert ist 20.

**Beispiel**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Wie bestimme ich die Anzahl der zu verwendenden Zuordnungen?

Hier sind einige hilfreiche Informationen zur Vorgehensweise angegeben.

* **Schritt 1: Bestimmen des YARN-Gesamtarbeitsspeichers:** Zunächst muss der YARN-Arbeitsspeicher bestimmt werden, der für den Cluster zur Verfügung steht, in dem Sie den DistCp-Auftrag ausführen. Diese Information finden Sie im dem Cluster zugeordneten Ambari-Portal. Navigieren Sie zu YARN, und zeigen Sie die Konfigurationsregisterkarte an, um den YARN-Arbeitsspeicher zu ermitteln. Um den YARN-Gesamtarbeitsspeicher zu erhalten, multiplizieren Sie den YARN-Arbeitsspeicher pro Knoten mit der Anzahl von Knoten in Ihrem Cluster.

* **Schritt 2: Berechnen der Anzahl von Zuordnungen**: Der Wert von **m** entspricht dem Quotienten des YARN-Gesamtarbeitsspeichers dividiert durch die YARN-Containergröße. Die YARN-Containergröße finden Sie ebenfalls im Ambari-Portal. Navigieren Sie zu YARN, zeigen Sie die Konfigurationsregisterkarte an. Die YARN-Containergröße wird in diesem Fenster angezeigt. Die Formel zur Berechnung der Anzahl von Zuordnungen (**m**) sieht wie folgt aus:

        m = (number of nodes * YARN memory for each node) / YARN container size

**Beispiel**

Angenommen, Sie verfügen über einen Cluster mit vier D14v2-Knoten und möchten 10 TB an Daten aus zehn unterschiedlichen Ordnern übertragen. Jeder Ordner enthält unterschiedliche Datenmengen, und auch die Dateigrößen in den einzelnen Ordnern sind unterschiedlich.

* YARN-Gesamtarbeitsspeicher: Über das Ambari-Portal ermitteln Sie, dass der YARN-Arbeitsspeicher für einen D14-Knoten 96 GB beträgt. Für einen Cluster mit vier Knoten beträgt der YARN-Gesamtarbeitsspeicher demnach: 

        YARN memory = 4 * 96GB = 384GB

* Anzahl von Zuordnungen: Über das Ambari-Portal ermitteln Sie, dass die YARN-Containergröße für einen D14-Clusterknoten 3072 beträgt. Die Anzahl von Zuordnungen beträgt somit:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Wenn andere Anwendungen Arbeitsspeicher verwenden, können Sie festlegen, dass für DistCp nur ein Teil des YARN-Arbeitsspeichers Ihres Clusters verwendet werden soll.

### <a name="copying-large-datasets"></a>Kopieren umfangreicher Datasets

Wenn das zu verschiebende Dataset sehr groß ist (beispielsweise > 1 TB) oder Sie über zahlreiche unterschiedliche Ordner verfügen, empfiehlt sich unter Umständen die Verwendung mehrerer DistCp-Aufträge. Dadurch ergibt sich zwar wahrscheinlich kein Leistungsgewinn, die Aufträge werden jedoch verteilt, sodass im Falle eines Fehlers bei einem Auftrag nicht das gesamte Projekt, sondern lediglich der betroffene Auftrag neu gestartet werden muss.

### <a name="limitations"></a>Einschränkungen

* Zur Optimierung der Leistung versucht DistCp, Zuordnungen mit ähnlicher Größe zu erstellen. Eine höhere Anzahl von Zuordnungen führt nicht unbedingt zu mehr Leistung.

* DistCp ist auf eine einzelne Zuordnung pro Datei beschränkt. Aus diesem Grund sollte die Anzahl von Zuordnungen die Anzahl von Dateien nicht überschreiten. Der Umstand, dass DistCp einer Datei nur eine einzelne Zuordnung zuweisen kann, beschränkt den Umfang der Parallelität, die beim Kopieren großer Dateien verwendet werden kann.

* Wenn Sie über eine geringe Anzahl großer Dateien verfügen, sollten Sie diese in Dateiblöcke mit jeweils 256 MB aufteilen, um das Parallelitätspotenzial zu erhöhen. 
 
* Beim Kopieren aus einem Azure Blob Storage-Konto wird Ihr Kopierauftrag unter Umständen auf der Blob Storage-Seite gedrosselt. Dies beeinträchtigt die Leistung Ihres Kopierauftrags. Weitere Informationen zu den Einschränkungen von Azure Blob Storage finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md) unter „Speichergrenzwerte“.

## <a name="see-also"></a>Weitere Informationen
* [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Verwenden von Azure HDInsight mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
