---
title: Kopieren von Daten zwischen Data Lake Storage Gen1 und Azure SQL-Datenbank mithilfe von Sqoop | Microsoft-Dokumentation
description: Verwenden von Sqoop zum Kopieren von Daten zwischen Azure SQL-Datenbank und Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878765"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Kopieren von Daten zwischen Data Lake Storage Gen1 und Azure SQL-Datenbank mithilfe von Sqoop
Erfahren Sie mehr über das Verwenden von Apache Sqoop zum Importieren und Exportieren von Daten zwischen Azure SQL-Datenbank und Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Was ist Sqoop?
Big Data-Anwendungen sind eine gute Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z. B. Protokolle und Dateien. Allerdings müssen gelegentlich strukturierte Daten verarbeitet werden, die in relationalen Datenbanken gespeichert sind.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) ist ein Tool zum Übertragen von Daten zwischen relationalen Datenbanken und einem Big Data-Repository wie Data Lake Storage Gen1. Mit diesem Tool können Sie Daten aus einem relationalen Datenbank-Managementsystem (RDBMS) wie z.B. Azure SQL-Datenbank in Data Lake Storage Gen1 importieren. Sie können die Daten dann mithilfe von Big Data-Workloads transformieren und analysieren und anschließend wieder in ein RDBMS zurückexportieren. In diesem Tutorial verwenden Sie eine Azure SQL-Datenbank als relationale Datenbank für den Import/Export.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Ein Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen1-Konto. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). In diesem Artikel wird davon ausgegangen, dass Sie einen HDInsight-Linux-Cluster mit Data Lake Storage Gen1-Zugriff verwenden.
* **Azure SQL-Datenbank**. Anleitungen zum Erstellen einer Datenbank finden Sie unter [Erstellen einer Azure SQL-Datenbank](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Lernen Sie schnell mithilfe von Videos?
[Sehen Sie sich dieses Video](https://mix.office.com/watch/1butcdjxmu114) zum Kopieren von Daten zwischen Azure Storage-Blobs und Data Lake Storage Gen1 mithilfe von DistCp an.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Erstellen von Beispieltabellen in der Azure SQL-Datenbank
1. Erstellen Sie zuerst zwei Beispieltabellen in der Azure SQL-Datenbank. Stellen Sie mit [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) oder Visual Studio eine Verbindung mit der Azure SQL-Datenbank her, und führen Sie die folgenden Abfragen aus:

    **Erstellen von Table1**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Erstellen von Table2**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. Fügen Sie in **Table1**einige Beispieldaten hinzu. Lassen Sie **Table2** leer. Wir importieren Daten aus **Table1** in Data Lake Storage Gen1. Anschließend exportieren wir Daten aus Data Lake Storage Gen1 in **Table2**. Führen Sie den folgenden Codeausschnitt aus:

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Verwenden von Sqoop in einem HDInsight-Cluster mit Zugriff auf Data Lake Storage Gen1
In einem HDInsight-Cluster sind die Sqoop-Pakete bereits verfügbar. Wenn Sie den HDInsight-Cluster so konfiguriert haben, dass Data Lake Storage Gen1 als zusätzlicher Speicher verwendet wird, können Sie Sqoop (ohne Konfigurationsänderungen) zum Importieren/Exportieren von Daten zwischen einer relationalen Datenbank (in diesem Beispiel die Azure SQL-Datenbank) und einem Data Lake Storage Gen1-Konto verwenden.

1. In diesem Tutorial wird davon ausgegangen, dass Sie einen Linux-Cluster erstellt haben. Daher sollten Sie SSH für das Herstellen einer Verbindung mit dem Cluster verwenden. Informationen hierzu finden Sie unter [Verbinden mit einem Linux-basierten HDInsight-Cluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Überprüfen Sie, ob Sie vom Cluster auf das Data Lake Storage Gen1-Konto zugreifen können. Geben Sie an der SSH-Eingabeaufforderung Folgendes ein:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    Dadurch sollte eine Liste der Dateien und Ordner im Data Lake Storage Gen1-Konto bereitgestellt werden.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importieren von Daten aus Azure SQL-Datenbank in Data Lake Storage Gen1
1. Navigieren Sie zu dem Verzeichnis, in dem Sqoop-Pakete verfügbar sind. In der Regel befinden sie sich unter `/usr/hdp/<version>/sqoop/bin`.
2. Importieren Sie die Daten aus **Table1** in das Data Lake Storage Gen1-Konto. Verwenden Sie die folgende Syntax:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Beachten Sie, dass der Platzhalter **sql-database-server-name** für den Namen des Servers steht, auf dem die Azure SQL-Datenbank ausgeführt wird. **sql-database-name** steht für den eigentlichen Datenbanknamen.

    Beispiel:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Stellen Sie sicher, dass die Daten in das Data Lake Storage Gen1-Konto übertragen wurden. Führen Sie den folgenden Befehl aus:

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    Die folgende Ausgabe wird angezeigt.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Jede **part-m***-Datei entspricht einer Zeile in der Quelltabelle **Table1**. Sie können den Inhalt der part-m-*-Dateien anzeigen und überprüfen.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Exportieren von Daten aus Data Lake Storage Gen1 in Azure SQL-Datenbank
1. Exportieren Sie die Daten aus dem Data Lake Storage Gen1-Konto in die leere Tabelle **Table2** in Azure SQL-Datenbank. Verwenden Sie die folgende Syntax.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Beispiel:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Stellen Sie sicher, dass die Daten in die SQL-Datenbanktabelle hochgeladen wurden. Stellen Sie mit [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) oder Visual Studio eine Verbindung mit Azure SQL-Datenbank her, und führen Sie dann die folgende Abfrage aus:

        SELECT * FROM TABLE2

    Dies führt zu folgender Ausgabe.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Überlegungen zur Leistung bei der Verwendung von Sqoop

Informationen zur Leistungsoptimierung Ihres Sqoop-Auftrags zum Kopieren von Daten in Data Lake Storage Gen1 finden Sie im [Dokument zur Sqoop-Leistung](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Weitere Informationen
* [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Verwenden von Azure HDInsight mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
