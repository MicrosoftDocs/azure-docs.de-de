---
title: Apache Sqoop mit Apache Hadoop – Azure HDInsight
description: Erfahren Sie mehr darüber, wie Sie mithilfe von Apache Sqoop Daten zwischen Apache Hadoop unter HDInsight und Azure SQL-Datenbank importieren und exportieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 6cd522192c498d628490430c46c18c8ae08ad2e2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547009"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Importieren und Exportieren von Daten zwischen Apache Hadoop unter HDInsight und Azure SQL-Datenbank mithilfe von Apache Sqoop

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sie Apache Sqoop zum Importieren und Exportieren von Daten zwischen einem Apache Hadoop-Cluster unter Azure HDInsight und Azure SQL-Datenbank oder Microsoft SQL Server verwenden. Die Schritte in diesem Artikel verwenden den Befehl `sqoop` direkt vom Hauptknoten des Hadoop-Clusters aus. Sie können SSH verwenden, um die Verbindung zum Hauptknoten herzustellen und die Befehle in diesem Artikel auszuführen. Dieser Artikel ist eine Fortsetzung von [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Voraussetzungen

* Die unter [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md) beschriebene [Einrichtung der Testumgebung](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) muss abgeschlossen sein.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Vertrautheit mit Sqoop. Weitere Informationen finden Sie im [Sqoop-Benutzerhandbuch](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Einrichten

1. Verwenden Sie einen [ssh-Befehl](../hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Legen Sie zur Verbesserung der Benutzerfreundlichkeit Variablen fest. Ersetzen Sie `PASSWORD`, `MYSQLSERVER` und `MYDATABASE` durch die entsprechenden Werte, und geben Sie dann die folgenden Befehle ein:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop-Export

Aus Hive in SQL.

1. Geben Sie den folgenden Befehl in der geöffneten SSH-Verbindung ein, um sicherzustellen, dass Sqoop Ihre Datenbank sehen kann. Dieser Befehl gibt eine Liste von Datenbanken zurück.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. Geben Sie den folgenden Befehl ein, um eine Liste von Tabellen für die angegebene Datenbank anzuzeigen:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Geben Sie den folgenden Befehl in Ihre offene SSH-Verbindung ein, um Daten aus der Hive-Tabelle `hivesampletable` in die Tabelle `mobiledata` in Ihre Datenbank zu exportieren:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Verwenden Sie die folgenden Abfragen über Ihre SSH-Verbindung, um die exportierten Daten anzuzeigen und zu überprüfen, ob die Daten exportiert wurden:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop-Import

Aus SQL in Azure Storage.

1. Geben Sie den folgenden Befehl in der geöffneten SSH-Verbindung ein, um Daten aus der Tabelle `mobiledata` in SQL in das Verzeichnis `wasbs:///tutorials/usesqoop/importeddata` in HDInsight zu importieren. In den Daten sind die Felder durch ein Tabstoppzeichen getrennt und die Zeilen durch ein Zeilenumbruchzeichen abgeschlossen.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Alternativ können Sie auch eine Hive-Tabelle festlegen:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Nachdem der Import abgeschlossen ist, geben Sie in der geöffneten SSH-Verbindung den folgenden Befehl ein, um die Daten im neuen Verzeichnis aufzulisten:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. Verwenden Sie [Beeline](./apache-hadoop-use-hive-beeline.md), um zu überprüfen, ob die Tabelle in Hive erstellt wurde.

    1. Verbinden

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Führen Sie alle der folgenden Abfragen einzeln aus, und überprüfen Sie die Ausgabe:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Beenden Sie Beeline mit dem Befehl `!exit`.

## <a name="limitations"></a>Einschränkungen

* Massenexport: Bei Linux-basiertem HDInsight unterstützt der zum Exportieren von Daten nach SQL verwendete Sqoop-Connector keine Masseneinfügung.

* Batchverarbeitung: Wenn Sie in Linux-basiertem HDInsight zum Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="important-considerations"></a>Wichtige Hinweise

* Sowohl HDInsight als auch SQL Server müssen sich im selben virtuellen Azure-Netzwerk befinden.

    Ein Beispiel finden Sie im Dokument [Connect HDInsight to your on-premises network](./../connect-on-premises-network.md) (Herstellen einer Verbindung von HDInsight mit Ihrem lokalen Netzwerk).

    Weitere Informationen zur Verwendung von HDInsight mit einem virtuellen Azure-Netzwerk finden Sie im Dokument [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md). Weitere Informationen zu Azure Virtual Network erhalten Sie im Dokument [Übersicht über virtuelle Netzwerke](../../virtual-network/virtual-networks-overview.md).

* SQL Server muss so konfiguriert sein, dass SQL-Authentifizierung erlaubt ist. Weitere Informationen finden Sie im Artikel [Auswählen eines Authentifizierungsmodus](/sql/relational-databases/security/choose-an-authentication-mode).

* Sie müssen SQL Server möglicherweise für Remoteverbindungen konfigurieren. Weitere Informationen finden Sie im Artikel [Beheben von Fehlern bei der Verbindung mit der SQL Server-Datenbank-Engine](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun gelernt, wie Sie Sqoop verwenden. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Oozie mit HDInsight](../hdinsight-use-oozie-linux-mac.md): Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
* [Analysieren von Daten zu Flugverspätungen mit HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): Verwenden von Interactive Query zum Analysieren von Daten zu Flugverspätungen und Verwenden von Sqoop zum Exportieren von Daten in eine Datenbank in Azure.
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md): Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob Storage.