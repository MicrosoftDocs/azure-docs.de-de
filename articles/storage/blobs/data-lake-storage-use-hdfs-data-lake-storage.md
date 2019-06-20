---
title: Verwenden der HDFS-CLI mit Azure Data Lake Storage Gen2
description: Einführung in die HDFS-CLI für Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: 24123278ff353860ff2af59f4fd77645dfc189e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938859"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>Verwenden der HDFS-CLI mit Data Lake Storage Gen2

Um auf die Daten in Ihrem Speicherkonto zuzugreifen und sie zu verwalten, können Sie wie beim [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) eine Befehlszeilenschnittstelle verwenden. Dieser Artikel enthält einige Beispiele, die Ihnen den Einstieg erleichtern.

HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Sie können auf dieses Dateisystem mithilfe der Shell zugreifen, die direkt mit dem HDFS interagiert, sowie den anderen Dateisystemen, die von Hadoop unterstützt werden.

Weitere Informationen zur HDFS-CLI finden Sie in der [offiziellen Dokumentation](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) und im [Handbuch zu HDFS-Berechtigungen](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

>[!NOTE]
>Wenn Sie Azure Databricks anstelle von HDInsight verwenden und über eine Befehlszeilenschnittstelle mit Ihren Daten interagieren möchten, können Sie die Databricks-CLI für die Interaktion mit dem Databricks-Dateisystem verwenden. Siehe [Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Databricks-Befehlszeilenschnittstelle).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>Verwenden der Hadoop Distributed File System-Befehlszeilenschnittstelle mit einem HDInsight Hadoop-Cluster unter Linux

Richten Sie zuerst den [Remotezugriff auf Dienste](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-information#remote-access-to-services) ein. Wenn Sie [SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) auswählen, sieht der PowerShell-Beispielcode folgendermaßen aus:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
Die Verbindungszeichenfolge ist im Abschnitt „SSH + Clusteranmeldung“ auf dem Blatt für den HDInsight-Cluster im Azure-Portal zu finden. SSH-Anmeldeinformationen wurden zum Zeitpunkt der Clustererstellung angegeben.

>[!IMPORTANT]
>Die Abrechnung für einen HDInsight-Cluster beginnt, nachdem der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Gebühren werden anteilig nach Minuten erhoben. Daher sollten Sie Ihren Cluster immer löschen, wenn Sie ihn nicht mehr verwenden. Informationen zum Löschen eines Clusters finden Sie in unserem [Artikel zum Thema](../../hdinsight/hdinsight-delete-cluster.md). In einem Speicherkonto mit aktiviertem Data Lake Storage Gen2 gespeicherte Daten bleiben jedoch auch nach dem Löschen eines HDInsight-Clusters erhalten.

## <a name="create-a-file-system"></a>Erstellen eines Dateisystems

    hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<file-system-name>@<storage-account-name>.dfs.core.windows.net/

* Ersetzen Sie den Platzhalter `<file-system-name>` durch den Namen, den Sie für Ihr Dateisystem verwenden möchten.

* Ersetzen Sie den Platzhalter `<storage-account-name>` durch den Namen Ihres Speicherkontos.

## <a name="get-a-list-of-files-or-directories"></a>Abrufen einer Liste mit Dateien oder Verzeichnissen

    hdfs dfs -ls <path>

Ersetzen Sie den Platzhalter `<path>` durch den URI des Dateisystems oder des Dateisystemordners.

Beispiel: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

    hdfs dfs -mkdir [-p] <path>

Ersetzen Sie den Platzhalter `<path>` durch den Namen des Stammdateisystems oder einen Ordner in Ihrem Dateisystem.

Beispiel: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Löschen einer Datei oder eines Verzeichnisses

    hdfs dfs -rm <path>

Ersetzen Sie den Platzhalter `<path>` durch den URI der Datei oder des Ordners, die bzw. den Sie löschen möchten.

Beispiel: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Anzeigen der Zugriffssteuerungslisten (Access Control Lists, ACLs) von Dateien und Verzeichnissen

    hdfs dfs -getfacl [-R] <path>

Beispiel:

`hdfs dfs -getfacl -R /dir`

Siehe [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl).

## <a name="set-acls-of-files-and-directories"></a>Festlegen der Zugriffssteuerungslisten von Dateien und Verzeichnissen

    hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]

Beispiel:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Siehe [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl).

## <a name="change-the-owner-of-files"></a>Ändern des Besitzers von Dateien

    hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>

Siehe [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown).

## <a name="change-group-association-of-files"></a>Ändern der Gruppenzuordnung von Dateien

    hdfs dfs -chgrp [-R] <group> <URI>

Siehe [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp).

## <a name="change-the-permissions-of-files"></a>Ändern der Berechtigungen von Dateien

    hdfs dfs -chmod [-R] <mode> <URI>

Siehe [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod).

Sie können die vollständige Liste mit Befehlen auf der Website [Apache Hadoop 2.4.1 File System Shell Guide](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) (Leitfaden zur Apache Hadoop 2.4.1-Dateisystemshell) anzeigen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden eines Azure Data Lake Storage Gen2-Kontos in Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)

* [Zugriffssteuerung in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
