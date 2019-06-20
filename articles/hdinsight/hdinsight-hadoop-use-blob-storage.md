---
title: 'Abfragen von Daten aus HDFS-kompatiblem Azure-Speicher: Azure HDInsight'
description: Es wird beschrieben, wie Sie Daten in Azure-Speicher und Azure Data Lake Storage abfragen, um die Ergebnisse Ihrer Analyse zu speichern.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 6e0192029decef95dcaecc0c60dce5fd5b6f99ff
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479906"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Verwenden von Azure Storage mit Azure HDInsight-Clustern

Zum Analysieren von Daten im HDInsight-Cluster können Sie die Daten in [Azure Storage](../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) oder einer Kombination dieser beiden speichern. Diese Speichervarianten ermöglichen das sichere Löschen von HDInsight-Clustern, die für Berechnungen verwendet werden, ohne dass Benutzerdaten verloren gehen.

Apache Hadoop unterstützt eine Variante des Standarddateisystems. Das Standarddateisystem gibt ein Standardschema und eine Standardautorität vor. Es kann auch zur Auflösung relativer Pfade verwendet werden. Bei der Erstellung des HDInsight-Clusters können Sie einen Blobcontainer in Azure Storage als Standarddateisystem angeben. Mit HDInsight 3.6 können Sie Azure Storage oder Azure Data Lake Storage Gen 1/Azure Data Lake Storage Gen2 als Standarddateisystem auswählen (mit einigen Ausnahmen). Informationen zur Unterstützung von Data Lake Storage Gen1 als Standardspeicher und als verknüpfter Speicher finden Sie unter [Verfügbarkeit für HDInsight-Cluster](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters).

In diesem Artikel erfahren Sie, wie Azure Storage mit HDInsight-Clustern funktioniert. Informationen zur Funktionsweise von Data Lake Storage Gen1 mit HDInsight-Clustern finden Sie unter [Verwenden von Azure Data Lake Storage mit Azure HDInsight-Clustern](hdinsight-hadoop-use-data-lake-store.md). Weitere Informationen zum Erstellen eines HDInsight-Clusters finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

Azure Storage stellt eine robuste, universelle Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. In HDInsight kann ein Blobcontainer in Azure Storage als Standarddateisystem für den Cluster verwendet werden. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit strukturierten oder unstrukturierten Daten arbeiten, die als Blobs gespeichert sind.

> [!WARNING]  
> Speicherkonten vom Typ **BlobStorage** können nur als sekundärer Speicher für HDInsight-Cluster verwendet werden.

| Art des Speicherkontos | Unterstützte Dienste | Unterstützte Leistungsstufen | Unterstützte Zugriffsebenen |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (universell v2)  | Blob     | Standard                    | Heiß, Kalt, Archiv\*   |
| Speicher (general-purpose v1)   | Blob     | Standard                    | –                    |
| BlobStorage                    | Blob     | Standard                    | Heiß, Kalt, Archiv\*   |

Die Verwendung des Standard-Blobcontainers zum Speichern von Geschäftsdaten wird nicht empfohlen. Stattdessen empfiehlt es sich, den Standard-Blobcontainer nach jeder Verwendung zu löschen, um die Speicherkosten zu verringern. Der Standardcontainer enthält Anwendungs- und Systemprotokolle. Stellen Sie sicher, dass Sie die Protokolle abrufen, bevor Sie den Container löschen.

Das Freigeben eines Blobcontainers als Standarddateisystem für mehrere Cluster wird nicht unterstützt.

> [!NOTE]  
> Die Zugriffsebene „Archiv“ ist eine Offlineebene, die eine Abrufwartezeit von mehreren Stunden aufweist, und wird für die Verwendung mit HDInsight nicht empfohlen. Weitere Informationen finden Sie unter [Zugriffsebene „Archiv“](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

Wenn Sie sich dazu entscheiden, Ihr Speicherkonto mit den **Firewalls und virtuelle Netzwerke**-Einschränkungen auf **Ausgewählte Netzwerke** zu schützen, denken Sie daran, die Ausnahme **Vertrauenswürdige Microsoft-Dienste zulassen…** zu aktivieren, damit HDInsight auf Ihr Speicherkonto zugreifen kann.

## <a name="hdinsight-storage-architecture"></a>HDInsight-Speicherarchitektur
Das folgende Diagramm bietet einen zusammenfassenden Überblick über die HDInsight-Speicherarchitektur bei der Verwendung von Azure Storage:

![Hadoop-Cluster verwenden die HDFS-API, um auf strukturierte und unstrukturierte Daten im Blobspeicher zuzugreifen und diese zu speichern.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight Storage-Architektur")

HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Auf dieses Dateisystem kann über den vollständig qualifizierten URI zugegriffen werden. Zum Beispiel:

    hdfs://<namenodehost>/<path>

Zusätzlich bietet HDInsight die Möglichkeit, auf die in Azure Storage gespeicherten Daten zuzugreifen. Die Syntax ist:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Hier sind einige Aspekte beim Verwenden eines Azure Storage-Kontos mit HDInsight-Clustern.

* **Container in Speicherkonten, die mit einem Cluster verbunden sind:** Da Kontoname und Schlüssel dem Cluster bei der Erstellung zugeordnet werden, haben Sie vollständigen Zugriff auf die Blobs in diesen Containern.

* **Öffentliche Container oder öffentliche Blobs in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie verfügen über Leserechte für die Blobs in den Containern.
  
> [!NOTE]  
> Öffentliche Container erlauben das Abrufen einer Liste aller im Container verfügbaren Blobs und der Container-Metadaten. Auf öffentliche Blobs haben Sie nur Zugriff, wenn Sie die exakte URL kennen. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Container und Blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Private Container in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie können nicht auf die Blobs in den Containern zugreifen, es sei denn, Sie definieren beim Senden der WebHCat-Aufträge das Speicherkonto. Dies wird weiter unten im Artikel erläutert.

Die bei der Erstellung definierten Speicherkonten und ihre Schlüssel werden in `%HADOOP_HOME%/conf/core-site.xml` auf den Clusterknoten gespeichert. Standardmäßig verwendet HDInsight die in der Datei core-site.xml definierten Speicherkonten. Sie können diese Einstellung mit [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) ändern.

Verschiedene WebHCat-Aufträge, darunter Apache Hive, MapReduce, Apache Hadoop Streaming und Apache Pig, können eine Beschreibung von Speicherkonten und Metadaten enthalten. (Dies funktioniert momentan nur für Pig und Speicherkonten, nicht für Metadaten.) Weitere Informationen finden Sie unter [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobs können für strukturierte und unstrukturierte Daten verwendet werden. In Blobcontainern werden Daten als Schlüssel-Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen der Schrägstrich (/) verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heißen. Das Verzeichnis *input* existiert zwar nicht, wegen des Schrägstrichs (/) im Schlüsselnamen sieht es jedoch so aus, als gäbe es einen Dateipfad.

## <a id="benefits"></a>Vorteile von Azure Storage
Der Leistungsaufwand, der damit verbunden ist, dass die Computecluster und Speicherressourcen nicht an demselben Ort vorliegen, wird dadurch verringert, dass die Computecluster nahe an den Speicherkontoressourcen in der Azure-Region erstellt werden. Hier macht das Hochgeschwindigkeitsnetzwerk den Zugriff auf die Daten im Azure-Speicher für die Serverknoten effizient.

Die Speicherung von Daten im Azure-Speicher anstatt im HDFS hat mehrere Vorteile:

* **Datenwiederverwendung und -freigabe:** Die Daten im HDFS befinden sich innerhalb des Computeclusters. Nur die Anwendungen, die Zugriff auf den Rechencluster haben, können die Daten über die HDFS-API verwenden. Auf die Daten im Azure-Speicher kann entweder über die HDFS-APIs oder über die [Blob Storage-REST-APIs](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) zugegriffen werden. Somit kann eine größere Menge von Anwendungen (darunter andere HDInsight-Cluster) und Tools verwendet werden, um die Daten zu produzieren und abzurufen.

* **Datenarchivierung:** Die Speicherung von Daten im Azure-Speicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.

* **Datenspeicherkosten:** Die langfristige Datenspeicherung in DFS ist kostspieliger als die Datenspeicherung im Azure-Speicher, da die Kosten eines Computeclusters höher als die Kosten für Azure-Speicher sind. Da die Daten nicht für jede Erzeugung eines neues Rechenclusters neu geladen werden, sparen Sie auch Kosten für das Laden von Daten.

* **Elastische horizontale Skalierung:** Auch wenn HDFS ein horizontal skaliertes Dateisystem bietet, wird die Skalierung durch die Anzahl der Knoten bestimmt, die Sie für Ihren Cluster erstellen. Eine Änderung der Skalierung kann weitaus schwieriger werden, als auf die flexiblen Speicherkapazitäten zu vertrauen, die Ihnen der Azure-Speicher automatisch bietet.

* **Georeplikation:** Für den Azure-Speicher kann eine Georeplikation durchgeführt werden. Obwohl Sie dadurch von geographischer Wiederherstellung und Datenredundanz profitieren, wirkt sich ein Ausfall des georeplizierten Standorts schwer auf Ihre Leistung aus und kann zusätzliche Kosten nach sich ziehen. Deshalb empfehlen wir, die Georeplikation mit Bedacht auszuwählen und nur dann anzuwenden, wenn der Wert der Daten die zusätzlichen Kosten rechtfertigt.

Bestimmte MapReduce-Aufträge und -Pakete können zu Zwischenergebnissen führen, die Sie eher nicht im Azure-Speicher speichern möchten. In diesem Fall können Sie die Dateien auch im lokalen HDFS speichern. Tatsächlich verwendet HDInsight DFS für einige dieser Zwischenergebnisse in Hive-Jobs und anderen Prozessen.

> [!NOTE]  
> Die meisten HDFS-Befehle (z.B. `ls`, `copyFromLocal` und `mkdir`) funktionieren weiterhin wie erwartet. Nur die für die native (als DFS bezeichnete) HDFS-Implementierung spezifischen Befehle wie `fschk` und `dfsadmin` zeigen in Azure Storage ein anderes Verhalten.

## <a name="address-files-in-azure-storage"></a>Zugreifen auf Dateien in Azure-Speicher
Das URI-Schema für den Zugriff auf Dateien in Azure-Speicher aus HDInsight lautet:

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

Das URI-Schema bietet unverschlüsselten Zugriff (mit dem Präfix *wasb:* ) wie auch SSL-verschlüsselten Zugriff (mit *wasbs*). Wir empfehlen die Verwendung von *wasbs* , und zwar auch für den Zugriff auf Daten, die sich in Azure in derselben Region befinden.

`<BlobStorageContainerName>` ist der Name des Blobcontainers im Azure-Speicher.
`<StorageAccountName>` ist der Name des Azure Storage-Kontos. Ein vollqualifizierter Domänenname (FQDN) ist erforderlich.

Wenn weder `<BlobStorageContainerName>` noch `<StorageAccountName>` angegeben wurde, wird das Standarddateisystem verwendet. Für die Dateien im Standarddateisystem können Sie relative oder absolute Pfade verwenden. Auf die Datei *hadoop-mapreduce-examples.jar*, die sich in HDInsight-Clustern befindet, kann z. B. mit einem der folgenden Befehle verwiesen werden:

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> In HDInsight-Clustern der Version 2.1 und 1.6 lautet der Dateiname `hadoop-examples.jar`.

Der Pfad ist der Pfadname des Hadoop Distributed File System (HDFS) für die Datei oder das Verzeichnis. Da es sich bei Containern im Azure-Speicher um Schlüssel-Wert-Paare handelt, wird kein echtes hierarchisches Dateisystem verwendet. Ein Schrägstrich (/) in einem Blobschlüssel wird als Verzeichnistrennzeichen interpretiert. Der Blob-Name für *hadoop-mapreduce-examples.jar* lautet z. B.:

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Wenn Blobs außerhalb von HDInsight verwendet werden, wird das WASB-Format von den meisten Dienstprogrammen nicht erkannt. Diese erwartet vielmehr ein grundlegendes Pfadformat wie `example/jars/hadoop-mapreduce-examples.jar`.

##  <a name="blob-containers"></a>Blobcontainer
Um Blobs zu verwenden, erstellen Sie zuerst ein Azure-[Speicherkonto](../storage/common/storage-create-storage-account.md). Hierzu liegen Sie eine Azure-Region fest, in der das Speicherkonto erstellt wird. Cluster und Speicherkonto müssen sich in der gleichen Region befinden. Die SQL Server-Datenbanken für den Hive- und Apache Oozie-Metastore müssen sich ebenfalls in der gleichen Region befinden.

Ein Blob gehört unabhängig davon, wo es sich befindet, stets zu einem Container Ihres Azure-Speicherkontos. Dieser Container kann ein außerhalb von HDInsight erstelltes Blob sein, oder es handelt sich um einen Container, der für einen HDInsight-Cluster erstellt wird.

Der standardmäßige Blobcontainer speichert clusterspezifische Informationen wie etwa Auftragsverlauf und Protokolle. Geben Sie einen Standard-Blob-Container nicht für mehrere HDInsight-Cluster frei. Dies kann zu einer Beschädigung des Auftragsverlaufs führen. Es wird empfohlen, unterschiedliche Container für die einzelnen Cluster zu verwenden und freigegebene Daten nicht im Standardspeicherkonto, sondern in einem verknüpften Speicherkonto abzulegen, das in der Bereitstellung aller relevanten Cluster angegeben wird. Weitere Informationen zum Konfigurieren verknüpfter Speicherkonten finden Sie unter [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md). Einen Standardspeichercontainer können Sie jedoch auch wiederverwenden, wenn der ursprüngliche HDInsight-Cluster gelöscht wurde. Bei HBase-Clustern können Sie das HBase-Tabellenschema sowie die darin enthaltenen Daten sogar beibehalten, indem Sie einen neuen HBase-Cluster mit dem Standardblobcontainer erstellen, der von einem gelöschten HBase-Cluster verwendet wurde.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Interaktion mit Azure-Speicher

Microsoft bietet die folgenden Tools für die Arbeit mit Azure Storage:

| Tool | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure-Befehlszeilenschnittstelle](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>Verwenden zusätzlicher Speicherkonten

Beim Erstellen eines HDInsight-Clusters geben Sie das Azure Storage-Konto an, dem Sie es zuordnen möchten. Zusätzlich zu diesem Speicherkonto können Sie während des Erstellungsprozesses oder nach der Erstellung eines Clusters weitere Speicherkonten aus dem gleichen oder einem anderen Azure-Abonnement hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Erstellen von HDInsight-Clustern](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> Die Verwendung eines zusätzlichen Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie HDFS-kompatiblen Azure Storage mit HDInsight verwenden. Dadurch können Sie skalierbare Datenerfassungslösungen mit langfristiger Archivierung aufbauen und HDInsight verwenden, um die Informationen innerhalb der gespeicherten strukturierten und unstrukturierten Daten zu entsperren.

Weitere Informationen finden Sie unter

* [Erste Schritte mit Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Erste Schritte mit Azure Data Lake Storage](../data-lake-store/data-lake-store-get-started-portal.md)
* [Hochladen von Daten in HDInsight](hdinsight-upload-data.md)
* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](hadoop/hdinsight-use-pig.md)
* [Verwenden von Azure Storage Shared Access Signatures zum Einschränken des Zugriffs auf Daten mit HDInsight](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](hdinsight-hadoop-use-data-lake-storage-gen2.md)