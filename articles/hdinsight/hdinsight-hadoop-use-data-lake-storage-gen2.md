---
title: Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern
description: Erfahren Sie, wie Sie Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern verwenden können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 04/24/2020
ms.openlocfilehash: cc17cd23ae197db25fed440eb249f2cf069d4859
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744587"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) ist ein Cloudspeicherdienst für Big-Data-Analysen, der auf [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) basiert. Data Lake Storage Gen2 vereint die Funktionen von Azure Blob Storage und Azure Data Lake Storage Gen1. Der daraus resultierende Dienst bietet Azure Data Lake Storage Gen1-Features wie beispielsweise Dateisystemsemantik, Sicherheit auf Verzeichnis- und Dateiebene sowie Anpassbarkeit. Sie werden ergänzt durch die geringen Kosten, den mehrstufigen Speicher, die Hochverfügbarkeit und die Notfallwiederherstellung von Azure Blob Storage.

Einen vollständigen Vergleich der Optionen für die Clustererstellung mit Azure Data Lake Storage Gen2 finden Sie unter [Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Verfügbarkeit von Data Lake Storage Gen2

Data Lake Storage Gen2 ist als Speicheroption für fast alle Typen von Azure HDInsight-Clustern sowohl als Standard- als auch zusätzliches Speicherkonto verfügbar. HBase kann jedoch nur ein Konto mit Data Lake Storage Gen2 verwenden.

> [!Note]  
> Nachdem Sie Data Lake Storage Gen2 als **primären Speichertyp** ausgewählt haben, können Sie keine Data Lake Storage Gen1-Instanz als zusätzlichen Speicher auswählen.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Erstellen von HDInsight-Clustern mithilfe von Data Lake Storage Gen2

Unter den folgenden Links finden Sie eine ausführliche Anleitung, wie Sie HDInsight-Cluster mit Zugriff auf Data Lake Storage Gen2 erstellen.

* [Verwenden des Portals](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Verwenden der Azure-Befehlszeilenschnittstelle](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell wird zum Erstellen eines HDInsight-Clusters mit Azure Data Lake Storage Gen2 derzeit nicht unterstützt.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Zugriffssteuerung für Data Lake Storage Gen2 in HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Welche Arten von Berechtigungen unterstützt Data Lake Storage Gen2?

Data Lake Storage Gen2 verwendet ein Zugriffssteuerungsmodell, das sowohl die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) in Azure als auch POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs) unterstützt. Data Lake Storage Gen1 unterstützt Zugriffssteuerungslisten nur zum Steuern des Zugriffs auf Daten.

Für Azure RBAC werden Rollenzuweisungen verwendet, um Benutzern, Gruppen und Dienstprinzipalen Berechtigungen für Azure-Ressourcen effektiv zuzuweisen. In der Regel sind diese Azure-Ressourcen auf Ressourcen auf oberster Ebene beschränkt (z. B. Azure Blob Storage-Konten). Für Azure Blob Storage und damit auch Data Lake Storage Gen2 wurde dieser Mechanismus auf die Dateisystemressource ausgeweitet.

Weitere Informationen zu Dateiberechtigungen mit Azure RBAC finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

Weitere Informationen zu Dateiberechtigungen mit ACLs finden Sie im Abschnitt „Zugriffssteuerungslisten für Dateien und Verzeichnisse“ unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Wie steuere ich in Data Lake Storage Gen2 den Zugriff auf meine Daten?

In Data Lake Storage Gen2 steuern verwaltete Identitäten den Zugriff auf Dateien durch Ihren HDInsight-Cluster. Eine verwaltete Identität ist eine in Azure Active Directory (Azure AD) registrierte Identität, deren Anmeldeinformationen von Azure verwaltet werden. Bei Verwendung von verwalteten Identitäten müssen Sie in Azure AD keine Dienstprinzipale registrieren. Sie müssen auch keine Anmeldeinformationen, z. B. Zertifikate, verwalten.

Es gibt zwei Arten verwalteter Identitäten für Azure-Dienste: durch das System zugewiesene und durch den Benutzer zugewiesene Identitäten. HDInsight verwendet durch den Benutzer zugewiesene verwaltete Identitäten, um auf Data Lake Storage Gen2 zuzugreifen. Benutzerseitig zugewiesene verwaltete Identitäten werden als eigenständige Azure-Ressourcen erstellt. Azure erstellt eine Identität in dem Azure AD-Mandanten, der vom verwendeten Abonnement als vertrauenswürdig eingestuft wird. Nachdem die Identität erstellt wurde, kann sie einer oder mehreren Azure-Dienstinstanzen zugewiesen werden.

Der Lebenszyklus einer vom Benutzer zugewiesenen Identität wird getrennt vom Lebenszyklus der Azure-Dienstinstanzen verwaltet, denen sie zugewiesen ist. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Wie richte ich Berechtigungen für Azure AD-Benutzer zum Abfragen von Daten in Data Lake Storage Gen2 mit Hive oder anderen Diensten ein?

Verwenden Sie Azure AD-Sicherheitsgruppen als zugewiesenen Prinzipal in ACLs, um Benutzern Berechtigungen zum Abfragen von Daten zu erteilen. Weisen Sie einzelnen Benutzern oder Dienstprinzipalen nicht direkt Dateizugriffsberechtigungen zu. Wenn Sie Azure AD-Sicherheitsgruppen zum Steuern des Flusses von Berechtigungen verwenden, können Sie Benutzer oder Dienstprinzipale hinzufügen und entfernen, ohne erneut ACLs auf eine gesamte Verzeichnisstruktur anzuwenden. Sie müssen die Benutzer einfach der entsprechenden Azure AD-Sicherheitsgruppe hinzufügen oder sie aus ihr entfernen. ACLs werden nicht vererbt, und die erneute Anwendung von ACLs erfordert daher eine Aktualisierung der ACL für jede Datei und jedes Unterverzeichnis.

## <a name="access-files-from-the-cluster"></a>Zugreifen auf Dateien aus dem Cluster

Es gibt mehrere Möglichkeiten, wie Sie auf die Dateien in Data Lake Storage Gen2 über einen HDInsight-Cluster zugreifen können.

* **Verwenden des vollqualifizierten Namens** Bei diesem Ansatz geben Sie den vollständigen Pfad zu der Datei an, auf die Sie zugreifen möchten.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Verwenden des verkürzten Pfadformats** Bei diesem Ansatz ersetzen Sie den Pfad bis zum Clusterstamm durch:

    ```
    abfs:///<file.path>/
    ```

* **Verwenden des relativen Pfads** Bei diesem Ansatz geben Sie nur den relativen Pfad zu der Datei an, auf die Sie zugreifen möchten.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Beispiele für Datenzugriff

Die Beispiele basieren auf einer [ssh-Verbindung](./hdinsight-hadoop-linux-use-ssh-unix.md) mit dem Hauptknoten des Clusters. In den Beispielen werden alle drei URI-Schemas verwendet. Ersetzen Sie `CONTAINERNAME` und `STORAGEACCOUNT` durch die entsprechenden Werte.

#### <a name="a-few-hdfs-commands"></a>Ein paar HDFS-Befehle

1. Erstellen von Dateien im lokalen Speicher.

    ```bash
    touch testFile.txt
    ```

1. Erstellen von Verzeichnissen im Clusterspeicher.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopieren von Daten aus dem lokalen Speicher in den Clusterspeicher.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Auflisten von Verzeichnisinhalten im Clusterspeicher.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Erstellen einer Hive-Tabelle

Zu Zwecken der Veranschaulichung werden drei Speicherorte gezeigt. Verwenden Sie für die tatsächliche Ausführung nur einen der `LOCATION`-Einträge.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Nächste Schritte

* [Azure HDInsight integration with Data Lake Storage Gen2 preview – ACL and security update (Integration von Azure HDInsight in Data Lake Storage Gen2 (Vorschau) – ACL und Sicherheitsupdate)](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Interactive Query in Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
