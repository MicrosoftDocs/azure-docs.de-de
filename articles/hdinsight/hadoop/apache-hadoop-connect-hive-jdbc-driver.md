---
title: Abfragen von Apache Hive über den JDBC-Treiber – Azure HDInsight
description: Erfahren Sie, wie Sie den JDBC-Treiber aus einer Java-Anwendung heraus verwenden, um Apache Hive-Abfragen an Hadoop in HDInsight zu übermitteln. Stellen Sie programmgesteuert vom SQuirrel SQL-Client eine Verbindung her.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: hrasheed
ms.openlocfilehash: 56a2b89277cbf8866c1992a6738bd80106ef3313
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66480010"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Abfragen von Apache Hive über den JDBC-Treiber in HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Erfahren Sie, wie Sie den JDBC-Treiber aus einer Java-Anwendung heraus verwenden, um Apache Hive-Abfragen an Apache Hadoop in Azure HDInsight zu übermitteln. Die Informationen in diesem Dokument veranschaulichen, wie eine Verbindung programmgesteuert und mithilfe des SQuirreL SQL-Clients hergestellt wird.

Weitere Informationen zur Hive-JDBC-Schnittstelle finden Sie unter [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Voraussetzungen

* einen HDInsight Hadoop-Cluster. Hinweise zum Erstellen finden Sie unter [Erste Schritte mit Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* [Java Developer Kit (JDK) Version 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) oder höher.
* [SQuirreL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL ist eine JDBC-Clientanwendung.

## <a name="jdbc-connection-string"></a>Verbindungszeichenfolge für JDBC

JDBC-Verbindungen mit einem HDInsight-Cluster unter Azure werden über Port 443 hergestellt, und der Datenverkehr wird per SSL geschützt. Das öffentliche Gateway, hinter dem sich die Cluster befinden, leitet den Datenverkehr an den Port um, auf dem von HiveServer2 gelauscht wird. Die folgende Verbindungszeichenfolge zeigt das für HDInsight zu verwendende Format:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres HDInsight-Clusters.

## <a name="authentication"></a>Authentication

Beim Herstellen der Verbindung müssen Sie den Administratornamen und das Kennwort für den HDInsight-Cluster verwenden, um sich beim Clustergateway zu authentifizieren. Beim Herstellen der Verbindung in JDBC-Clients wie SQuirreL SQL müssen Sie den Administratornamen und das Kennwort in den Clienteinstellungen eingeben.

In einer Java-Anwendung müssen Sie den Namen und das Kennwort beim Herstellen einer Verbindung eingeben. Beispielsweise wird mit dem folgenden Java-Code eine neue Verbindung geöffnet, indem die Verbindungszeichenfolge, der Administratorname und das Kennwort verwendet werden:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Herstellen einer Verbindung mit dem SQuirreL SQL-Client

SQuirreL SQL ist ein JDBC-Client, der für die Remoteausführung von Hive-Abfragen mit Ihrem HDInsight-Cluster verwendet werden kann. Bei den folgenden Schritten wird davon ausgegangen, dass Sie SQuirreL SQL bereits installiert haben.

1. Erstellen Sie ein Verzeichnis zur Aufnahme bestimmter Dateien, die aus Ihrem Cluster kopiert werden müssen.

2. Ersetzen Sie im folgenden Skript `sshuser` durch den Namen des SSH-Benutzerkontonamens für den Cluster.  Ersetzen Sie `CLUSTERNAME` durch den Namen des HDInsight-Clusters.  Ändern Sie an einer Befehlszeile Ihre Arbeitsverzeichnis in das, das Sie im vorherigen Schritt erstellt haben, und geben Sie dann den folgenden Befehl ein, um Dateien aus einem HDInsight-Cluster zu kopieren:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-1.2*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Starten Sie die SQuirreL SQL-Anwendung. Wählen Sie auf der linken Seite des Fensters **Drivers** aus.

    ![Registerkarte „Drivers“ auf der linken Seite des Fensters](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

4. Wählen Sie aus den Symbolen oben im Dialogfeld **Drivers** das Symbol **+** aus, um einen Treiber zu erstellen.

    ![Treibersymbole](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

5. Fügen Sie im Dialogfeld „Add Driver“ die folgenden Informationen hinzu:

    * **Name**: Hive
    * **Beispiel-URL**: `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra Class Path**: Fügen Sie mit der Schaltfläche **Add** alle JAR-Dateien hinzu, die Sie zuvor heruntergeladen haben.
    * **Class Name:** org.apache.hive.jdbc.HiveDriver

   ![Dialogfeld „Add Driver“](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Wählen Sie auf **OK** aus, um die Einstellungen zu speichern.

6. Wählen Sie auf der linken Seite des Fensters „SQuirreL SQL“ die Option **Aliases** aus. Wählen Sie dann das Symbol **+** aus, um einen Verbindungsalias zu erstellen.

    ![Hinzufügen eines neuen Alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

7. Verwenden Sie für das Dialogfeld **Add Alias** die folgenden Werte.

    * **Name**: Hive in HDInsight

    * **Driver**: Wählen Sie in der Dropdownliste den **Hive**-Treiber aus

    * **URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres HDInsight-Clusters.

    * **User Name**: Der Name des Clusteranmeldekontos für den HDInsight-Cluster. Der Standardwert lautet `admin`.

    * **Password**: Das Kennwort für das Clusteranmeldekonto.

   ![Dialogfeld „Add Alias“](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    > [!IMPORTANT] 
    > Überprüfen Sie mithilfe der Schaltfläche **Test**, ob die Verbindung funktioniert. Wenn das Dialogfeld **Connect to: Hive on HDInsight** angezeigt wird, wählen Sie **Connect** aus, um den Test durchzuführen. Wenn der Test erfolgreich ist, wird das Dialogfeld **Connection successful** (Verbindung erfolgreich) angezeigt. Wenn ein Fehler auftritt, finden Sie Informationen unter [Problembehandlung](#troubleshooting).

    Verwenden Sie die Schaltfläche **OK** unten im Dialogfeld **Add Alias** (Alias hinzufügen), um den Verbindungsalias zu speichern.

8. Wählen Sie oben in SQuirreL SQL in der Dropdownliste **Connect to** die Option **Hive on HDInsight** aus. Wenn Sie dazu aufgefordert werden, wählen Sie **Connect** aus.

    ![Verbindungsdialogfeld](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

9. Nachdem die Verbindung hergestellt wurde, geben Sie im Dialogfeld für die SQL-Abfrage die folgende Abfrage ein, und wählen Sie das Symbol **Run** (ein laufender Mensch) aus. Die Ergebnisse der Abfrage sollten im Ergebnisbereich angezeigt werden.

    ```hql
    select * from hivesampletable limit 10;
    ```

    ![Dialogfeld für die SQL-Abfrage einschließlich der Ergebnisse](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Herstellen einer Verbindung von einer Beispiel-Java-Anwendung aus

Ein Beispiel der Verwendung eines Java-Clients zur Hive-Abfrage in HDInsight finden Sie unter [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Befolgen Sie die Anleitung im Repository, um das Beispiel zu erstellen und auszuführen.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Unerwarteter Fehler beim Versuch, eine SQL-Verbindung zu öffnen

**Symptome**: Beim Herstellen einer Verbindung mit einem HDInsight-Cluster der Version 3.3 oder höher erhalten Sie möglicherweise die Meldung, dass ein unerwarteter Fehler aufgetreten ist. Die Stapelüberwachung für diesen Fehler beginnt mit folgenden Zeilen:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Ursache**: Dieser Fehler wird durch eine ältere Version der in SQuirreL enthaltenen commons-codec.jar-Datei verursacht.

**Lösung**: Führen Sie zum Beheben dieses Fehlers die folgenden Schritte aus:

1. Beenden Sie SQuirreL, und wechseln Sie zu dem Verzeichnis, in dem SQuirreL in Ihrem System installiert ist. Ersetzen Sie im SquirreL-Verzeichnis, unterhalb des `lib` -Verzeichnisses, die vorhandene commons-codec.jar-Datei durch die Datei, die Sie aus dem HDInsight-Cluster heruntergeladen haben.

2. Starten Sie SQuirreL neu. Dieser Fehler sollte jetzt nicht mehr auftreten, wenn Sie eine Verbindung mit Hive in HDInsight herstellen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie JDBC mit Hive verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Visualisieren von Apache Hive-Daten mit Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md)
* [Visualisieren von Interactive Query-Hive-Daten mit Power BI in Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Verwenden von Apache Zeppelin zum Ausführen von Apache Hive-Abfragen in Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Verbinden von Excel mit Hadoop in Azure HDInsight mithilfe des Microsoft Hive ODBC-Treibers](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Verbinden von Excel mit Apache Hadoop mithilfe von Power Query](apache-hadoop-connect-excel-power-query.md)
* [Verbinden mit Azure HDInsight und Ausführen von Apache Hive-Abfragen mithilfe von Data Lake-Tools für Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)
* [Verwenden von Azure HDInsight-Tools für Visual Studio Code](../hdinsight-for-vscode.md)
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md)
* [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)
