---
title: Apache Sqoop mit Apache Hadoop – Azure HDInsight
description: Erfahren Sie mehr darüber, wie Sie mithilfe von Apache Sqoop Daten zwischen Apache Hadoop unter HDInsight und einer Azure SQL-Datenbank importieren und exportieren.
keywords: Hadoop Sqoop, Sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721640"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Importieren und Exportieren von Daten zwischen Apache Hadoop unter HDInsight und einer SQL-Datenbank mithilfe von Apache Sqoop

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sie Apache Sqoop zum Importieren und Exportieren von Daten zwischen einem Apache Hadoop-Cluster unter Azure HDInsight und einer Azure SQL- oder Microsoft SQL Server-Datenbank verwenden. Die Schritte in diesem Artikel verwenden den Befehl `sqoop` direkt vom Hauptknoten des Hadoop-Clusters aus. Sie können SSH verwenden, um die Verbindung zum Hauptknoten herzustellen und die Befehle in diesem Artikel auszuführen. Dieser Artikel ist eine Fortsetzung von [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Voraussetzungen

* Die unter [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md) beschriebene [Einrichtung der Testumgebung](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) muss abgeschlossen sein.

* Ein Client zum Abfragen der Azure SQL-Datenbank. Erwägen Sie die Verwendung von [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) oder [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Sqoop-Export

Aus Hive in SQL Server:

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters, und geben Sie dann den folgenden Befehl ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Ersetzen Sie `MYSQLSERVER` durch den Namen Ihrer SQL Server-Instanz. Geben Sie den folgenden Befehl in der geöffneten SSH-Verbindung ein, um sicherzustellen, dass Sqoop Ihre SQL-Datenbank sehen kann. Geben Sie das Kennwort für die SQL Server-Anmeldung ein, wenn Sie dazu aufgefordert werden. Dieser Befehl gibt eine Liste von Datenbanken zurück.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Ersetzen Sie `MYSQLSERVER` durch den Namen Ihrer SQL Server-Instanz und `MYDATABASE` durch den Namen Ihrer SQL-Datenbank. Um Daten aus der Hive-Tabelle `hivesampletable` in die Tabelle `mobiledata` in SQL-Datenbank zu exportieren, geben Sie den folgenden Befehl in der geöffneten SSH-Verbindung ein. Geben Sie das Kennwort für die SQL Server-Anmeldung ein, wenn Sie dazu aufgefordert werden.

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Um zu überprüfen, ob Daten exportiert wurden, führen Sie auf Ihrem SQL-Client die folgenden Abfragen zum Anzeigen der exportierten Daten aus:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Sqoop-Import

Aus SQL Server in Azure Storage:

1. Ersetzen Sie `MYSQLSERVER` durch den Namen Ihrer SQL Server-Instanz und `MYDATABASE` durch den Namen Ihrer SQL-Datenbank. Geben Sie den folgenden Befehl in der geöffneten SSH-Verbindung ein, um Daten aus der Tabelle `mobiledata` in SQL-Datenbank in das Verzeichnis `wasb:///tutorials/usesqoop/importeddata` in HDInsight zu importieren. Geben Sie das Kennwort für die SQL Server-Anmeldung ein, wenn Sie dazu aufgefordert werden. In den Daten sind die Felder durch ein Tabstoppzeichen getrennt und die Zeilen durch ein Zeilenumbruchzeichen abgeschlossen.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Nachdem der Import abgeschlossen ist, geben Sie in der geöffneten SSH-Verbindung den folgenden Befehl ein, um die Daten im neuen Verzeichnis aufzulisten:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Einschränkungen

* Massenexport: Bei Linux-basiertem HDInsight unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector keine Masseneinfügung.

* Batchverarbeitung: Wenn Sie in Linux-basiertem HDInsight zum Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="important-considerations"></a>Wichtige Hinweise

* Sowohl HDInsight als auch SQL Server müssen sich im selben virtuellen Azure-Netzwerk befinden.

    Ein Beispiel finden Sie im Dokument [Connect HDInsight to your on-premises network](./../connect-on-premises-network.md) (Herstellen einer Verbindung von HDInsight mit Ihrem lokalen Netzwerk).

    Weitere Informationen zur Verwendung von HDInsight mit einem virtuellen Azure-Netzwerk finden Sie im Dokument [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md). Weitere Informationen zu Azure Virtual Network erhalten Sie im Dokument [Übersicht über virtuelle Netzwerke](../../virtual-network/virtual-networks-overview.md).

* SQL Server muss so konfiguriert sein, dass SQL-Authentifizierung erlaubt ist. Weitere Informationen finden Sie im Artikel [Auswählen eines Authentifizierungsmodus](https://msdn.microsoft.com/ms144284.aspx).

* Sie müssen SQL Server möglicherweise für Remoteverbindungen konfigurieren. Weitere Informationen finden Sie im Artikel [Beheben von Fehlern bei der Verbindung mit der SQL Server-Datenbank-Engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Nächste Schritte

Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Oozie mit HDInsight](../hdinsight-use-oozie-linux-mac.md): Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
* [Analysieren von Daten zu Flugverspätungen mit HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): Verwenden von Apache Hive zum Analysieren von Daten zu Flugverspätungen und Verwenden von Sqoop zum Exportieren von Daten in Azure SQL-Datenbank.
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md): Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob Storage.
