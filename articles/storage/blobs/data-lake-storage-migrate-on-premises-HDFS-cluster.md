---
title: Verwenden von Azure Data Box zum Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage
description: Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 1eac7ecce88dc817b9bd7bd5330d10b019cc7dd2
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939265"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Verwenden von Azure Data Box zum Migrieren von Daten aus einem lokalen Hadoop Distributed File System-Speicher zu Azure Storage

Sie können Daten aus einem lokalen HDFS-Speicher (Hadoop Distributed File System) Ihres Hadoop-Clusters zu Azure Storage (Blobspeicher oder Data Lake Storage Gen2) migrieren, indem Sie ein Data Box-Gerät verwenden.

Dieser Artikel enthält Informationen zur Durchführung dieser Aufgaben:

:heavy_check_mark: Kopieren Ihrer Daten auf ein Data Box-Gerät.

:heavy_check_mark: Senden des Data Box-Geräts an Microsoft.

:heavy_check_mark: Verschieben der Daten in Ihr Data Lake Storage Gen2-Speicherkonto.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um die Migration durchführen zu können:

* Ein Azure Storage-Konto, für das hierarchische Namespaces **nicht** aktiviert sind.

* Wenn Sie ein Azure Data Lake Storage Gen2-Konto (Speicherkonto, für das hierarchische Namespaces **aktiviert sind**) verwenden möchten, kann es ratsam sein, es jetzt zu erstellen.

* Einen lokalen Hadoop-Cluster, in dem Ihre Quelldaten enthalten sind.

* Ein [Azure Data Box-Gerät](https://azure.microsoft.com/services/storage/databox/). 

    - [Bestellen Sie Ihre Data Box-Datenträger.](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) Beachten Sie beim Bestellen Ihrer Box, dass Sie ein Speicherkonto wählen, für das hierarchische Namespaces **nicht** aktiviert sind. Der Grund ist, dass für Data Box die direkte Erfassung in Azure Data Lake Storage Gen2 noch nicht unterstützt wird. Sie müssen den Kopiervorgang in ein Speicherkonto und dann einen zweiten Kopiervorgang in das ADLS Gen2-Konto durchführen. Die unten angegebenen Schritte enthalten hierzu eine Anleitung.
    - Führen Sie das [Verkabeln und Herstellen einer Verbindung mit der Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) für ein lokales Netzwerk durch.

Wenn Sie bereit sind, können wir beginnen.

## <a name="copy-your-data-to-a-data-box-device"></a>Kopieren Ihrer Daten auf ein Data Box-Gerät

Zum Kopieren der Daten aus Ihrem lokalen HDFS-Speicher auf ein Data Box-Gerät richten Sie einige Dinge ein und verwenden dann das Tool [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html).

Wenn die Menge der Daten, die Sie kopieren, die Kapazität eines einzelnen Data Box-Geräts übersteigt, müssen Sie Ihr Dataset in Komponenten mit einer Größe unterteilen, die für Ihr Data Box-Gerät geeignet ist.

Führen Sie diese Schritte aus, um Daten über die REST-APIs des Blob-/Objektspeichers auf Ihr Data Box-Gerät zu kopieren. Über die REST-API-Schnittstelle wird das Data Box-Gerät als HDFS-Speicher für Ihren Cluster angezeigt. 


1. Bevor Sie die Daten per REST kopieren, sollten Sie die Sicherheits- und Verbindungsprimitive zum Herstellen einer Verbindung mit der REST-Schnittstelle auf dem Data Box-Gerät identifizieren. Melden Sie sich an der lokalen Webbenutzeroberfläche von Data Box an, und navigieren Sie zur Seite **Verbindung herstellen und Daten kopieren**. Suchen Sie für das Azure-Speicherkonto für Ihr Data Box-Gerät unter **Zugriffseinstellungen** nach **REST (Vorschau)** , und wählen Sie die Option aus.

    ![Seite „Verbindung herstellen und Daten kopieren“](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Kopieren Sie im Dialogfeld „Auf Speicherkonto zugreifen und Daten hochladen“ den **Blob-Dienstendpunkt** und den **Speicherkontoschlüssel**. Lassen Sie beim Blob-Dienstendpunkt den Teil `https://` und den nachgestellten Schrägstrich weg.

    Der Endpunkt ist in diesem Fall: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. Der Hostteil des verwendeten URI ist: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Ein Beispiel hierfür finden Sie in der Beschreibung der [Verbindungsherstellung mit REST per http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Dialogfeld „Auf Speicherkonto zugreifen und Daten hochladen“](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Fügen Sie `/etc/hosts` auf jedem Knoten den Endpunkt und die Data Box-IP-Adresse hinzu.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Falls Sie für DNS einen anderen Mechanismus verwenden, sollten Sie sicherstellen, dass der Data Box-Endpunkt aufgelöst werden kann.
    
4. Legen Sie die Shellvariable `azjars` so fest, dass sie auf die JAR-Dateien `hadoop-azure` und `microsoft-windowsazure-storage-sdk` verweist. Diese Dateien befinden sich unter dem Hadoop-Installationsverzeichnis. (Sie können das Vorhandensein dieser Dateien überprüfen, indem Sie den Befehl `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` verwenden, wobei `<hadoop_install_dir>` das Verzeichnis ist, in dem Sie Hadoop installiert haben.) Verwenden Sie die vollständigen Pfade. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Erstellen Sie den Speichercontainer, den Sie beim Kopieren von Daten verwenden möchten. Sie sollten auch einen Zielordner als Teil des Befehls angeben. Dies könnte zu diesem Zeitpunkt ein Dummyzielordner sein.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Führen Sie einen „List“-Befehl aus, um sicherzustellen, dass Ihr Container und Ordner erstellt wurden.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Kopieren Sie Daten aus dem Hadoop-HDFS zum Data Box-Blobspeicher in den zuvor erstellten Container. Wenn der Ordner, in den Sie kopieren möchten, nicht gefunden wurde, wird er vom Befehl automatisch erstellt.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   Die Option `-libjars` wird verwendet, um die Datei `hadoop-azure*.jar` und die abhängige Datei `azure-storage*.jar` für `distcp` verfügbar zu machen. Dies kann für einige Cluster bereits erfolgt sein.
   
   Im folgenden Beispiel wird veranschaulicht, wie der Befehl `distcp` zum Kopieren von Daten verwendet wird.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Erhöhen Sie die Kopiergeschwindigkeit wie folgt:
- Versuchen Sie, die Anzahl von Zuordnungen zu ändern. (Im obigen Beispiel werden „`m` = 4“ Zuordnungen verwendet.)
- Versuchen Sie, mehrere `distcp`-Elemente parallel auszuführen.
- Beachten Sie hierbei, dass für große Dateien eine bessere Leistung als für kleine Dateien erzielt wird.       
    
## <a name="ship-the-data-box-to-microsoft"></a>Senden des Data Box-Geräts an Microsoft

Führen Sie diese Schritte aus, um das Data Box-Gerät vorzubereiten und an Microsoft zu senden.

1. Wenn das Kopieren der Daten abgeschlossen ist, können Sie auf Ihrem Data Box-Gerät das [Vorbereiten auf den Versand](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest) durchführen. Laden Sie die BOM-Dateien herunter, nachdem die Vorbereitung des Geräts abgeschlossen ist. Sie verwenden diese BOM- bzw. Manifestdateien später, um sich zu vergewissern, dass die Daten in Azure hochgeladen wurden. Fahren Sie das Gerät herunter, und entfernen Sie die Kabel. 
2.  Vereinbaren Sie einen Termin für die Abholung durch UPS, um [Ihr Data Box-Gerät zurück an Azure zu senden](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up). 
3.  Nachdem Ihr Gerät von Microsoft empfangen wurde, wird es mit dem Netzwerkdatencenter verbunden, und die Daten werden in das Speicherkonto hochgeladen (mit deaktivierten hierarchischen Namespaces), das Sie bei der Bestellung des Data Box-Geräts angegeben haben. Überprüfen Sie anhand der BOM-Dateien, ob Ihre gesamten Daten in Azure hochgeladen wurden. Sie können diese Daten jetzt in ein Data Lake Storage Gen2-Speicherkonto verschieben.

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Verschieben der Daten in Ihr Data Lake Storage Gen2-Speicherkonto

Dieser Schritt ist erforderlich, wenn Sie Azure Data Lake Storage Gen2 als Datenspeicher verwenden. Falls Sie nur ein Blobspeicherkonto ohne hierarchischen Namespace als Datenspeicher nutzen, müssen Sie diesen Schritt nicht ausführen.

Sie haben hierbei zwei Möglichkeiten. 

- Verwenden Sie [Azure Data Factory zum Verschieben von Daten in ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Sie müssen **Azure Blob Storage** als Quelle angeben.

- Verwenden Sie Ihren Azure-basierten Hadoop-Cluster. Sie können diesen DistCp-Befehl ausführen:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Mit diesem Befehl werden sowohl Daten als auch Metadaten aus Ihrem Speicherkonto in Ihr Data Lake Storage Gen2-Speicherkonto kopiert.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Data Lake Storage Gen2 mit HDInsight-Clustern funktioniert. Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
