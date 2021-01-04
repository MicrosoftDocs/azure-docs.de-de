---
title: 'Tutorial: ETL-Vorgänge (Extrahieren, Transformieren und Laden) mit Interactive Query: Azure HDInsight'
description: 'Tutorial: Erfahren Sie, wie Sie Daten aus einem unformatierten CSV-Dataset extrahieren. Transformieren Sie sie mit einer interaktiven Abfrage in HDInsight. Laden Sie die transformierten Daten anschließend mit Apache Sqoop in Azure SQL-Datenbank.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 07/02/2019
ms.openlocfilehash: 730a6bfa627eafcab799fc811db4e20a1d4cec48
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534582"
---
# <a name="tutorial-extract-transform-and-load-data-using-interactive-query-in-azure-hdinsight"></a>Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Interactive Query in Azure HDInsight

In diesem Tutorial laden Sie eine Rohdaten-CSV-Datendatei mit öffentlich verfügbaren Flugdaten herunter. Importieren Sie sie in den HDInsight-Clusterspeicher, und transformieren Sie die Daten dann mithilfe der interaktiven Abfrage in Azure HDInsight. Anschließend laden Sie die transformierten Daten mit [Apache Sqoop](https://sqoop.apache.org/) in eine Datenbank in Azure SQL-Datenbank.

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Herunterladen der Beispielflugdaten
> * Hochladen von Daten in einen HDInsight-Cluster
> * Transformieren der Daten mit Interactive Query
> * Erstellen einer Tabelle in einer Datenbank in Azure SQL-Datenbank
> * Exportieren von Daten in eine Datenbank in Azure SQL-Datenbank mit Sqoop

## <a name="prerequisites"></a>Voraussetzungen

* Ein Interactive Query-Cluster in HDInsight. Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md), und wählen Sie **Interactive Query** für **Clustertyp** aus.

* Sie benötigen eine Datenbank in Azure SQL-Datenbank. Sie verwenden die Datenbank als Zieldatenspeicher. Wenn Sie noch nicht über eine Datenbank in Azure SQL-Datenbank verfügen, helfen Ihnen die Informationen unter [Erstellen einer Datenbank in Azure SQL-Datenbank im Azure-Portal](../../azure-sql/database/single-database-create-quickstart.md) weiter.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Herunterladen der Flugdaten

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA) auf.

2. Löschen Sie auf der Seite die Inhalte aller Felder, und wählen Sie dann die folgenden Werte aus:

   | Name | Wert |
   | --- | --- |
   | Filter Year |2019 |
   | Filter Period |January |
   | Felder |`Year, FlightDate, Reporting_Airline, DOT_ID_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay`. |

3. Wählen Sie **Herunterladen** aus. Sie erhalten eine ZIP-Datei mit den ausgewählten Datenfeldern.

## <a name="upload-data-to-an-hdinsight-cluster"></a>Hochladen von Daten in einen HDInsight-Cluster

Daten können auf unterschiedliche Weise in den zugeordneten Speicher eines HDInsight-Clusters hochgeladen werden. In diesem Abschnitt verwenden Sie `scp` zum Hochladen der Daten. Informationen zu anderen Uploadmöglichkeiten für Daten finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](../hdinsight-upload-data.md).

1. Laden Sie die ZIP-Datei auf den Hauptknoten des HDInsight-Clusters hoch. Bearbeiten Sie den folgenden Befehl, indem Sie `FILENAME` durch den Namen der ZIP-Datei und `CLUSTERNAME` durch den Namen des HDInsight-Clusters ersetzen. Öffnen Sie dann eine Eingabeaufforderung, legen Sie Ihr Arbeitsverzeichnis auf den Speicherort der Datei fest, und geben Sie schließlich den Befehl ein.

    ```cmd
    scp FILENAME.zip sshuser@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.zip
    ```

    Wenn Sie dazu aufgefordert werden, geben Sie „Ja“ oder „Nein“ ein. Der Text wird während der Eingabe nicht im Fenster angezeigt.

2. Stellen Sie nach Abschluss des Uploadvorgangs eine SSH-Verbindung mit dem Cluster her. Bearbeiten Sie den folgenden Befehl, indem Sie `CLUSTERNAME` durch den Namen des HDInsight-Clusters ersetzen. Geben Sie dann den folgenden Befehl ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Richten Sie die Umgebungsvariable ein, sobald eine SSH-Verbindung hergestellt wurde. Ersetzen Sie `FILE_NAME`, `SQL_SERVERNAME`, `SQL_DATABASE`, `SQL_USER` und `SQL_PASWORD` durch die entsprechenden Werte. Geben Sie anschließend den Befehl ein:

    ```bash
    export FILENAME=FILE_NAME
    export SQLSERVERNAME=SQL_SERVERNAME
    export DATABASE=SQL_DATABASE
    export SQLUSER=SQL_USER
    export SQLPASWORD='SQL_PASWORD'
    ```

4. Entzippen Sie die ZIP-Datei, indem Sie folgenden Befehl eingeben:

    ```bash
    unzip $FILENAME.zip
    ```

5. Erstellen Sie ein Verzeichnis im HDInsight-Speicher, und kopieren Sie dann die CSV-Datei in das Verzeichnis, indem Sie den folgenden Befehl eingeben:

    ```bash
    hdfs dfs -mkdir -p /tutorials/flightdelays/data
    hdfs dfs -put $FILENAME.csv /tutorials/flightdelays/data/
    ```

## <a name="transform-data-using-a-hive-query"></a>Transformieren von Daten mithilfe einer Hive-Abfrage

Ein Hive-Auftrag kann auf verschiedenste Arten in einem HDInsight-Cluster ausgeführt werden. In diesem Abschnitt verwenden Sie [Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell), um einen Hive-Auftrag auszuführen. Informationen zu anderen Ausführungsmethoden für Hive-Aufträge finden Sie unter [Was sind Apache Hive und HiveQL in Azure HDInsight?](../hadoop/hdinsight-use-hive.md).

Im Rahmen des Hive-Auftrags importieren Sie die Daten aus der CSV-Datei in eine Hive-Tabelle namens **Delays** .

1. Verwenden Sie an der SSH-Eingabeaufforderung, die bereits für den HDInsight-Cluster geöffnet ist, den folgenden Befehl, um eine neue Datei namens **flightdelays.hql** zu erstellen und zu bearbeiten:

    ```bash
    nano flightdelays.hql
    ```

2. Verwenden Sie als Inhalt der Datei den folgenden Text:

    ```hiveql
    DROP TABLE delays_raw;
    -- Creates an external table over the csv file
    CREATE EXTERNAL TABLE delays_raw (
        YEAR string,
        FL_DATE string,
        UNIQUE_CARRIER string,
        CARRIER string,
        FL_NUM string,
        ORIGIN_AIRPORT_ID string,
        ORIGIN string,
        ORIGIN_CITY_NAME string,
        ORIGIN_CITY_NAME_TEMP string,
        ORIGIN_STATE_ABR string,
        DEST_AIRPORT_ID string,
        DEST string,
        DEST_CITY_NAME string,
        DEST_CITY_NAME_TEMP string,
        DEST_STATE_ABR string,
        DEP_DELAY_NEW float,
        ARR_DELAY_NEW float,
        CARRIER_DELAY float,
        WEATHER_DELAY float,
        NAS_DELAY float,
        SECURITY_DELAY float,
        LATE_AIRCRAFT_DELAY float)
    -- The following lines describe the format and location of the file
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE
    LOCATION '/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays AS
    SELECT YEAR AS year,
        FL_DATE AS flight_date,
        substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier,
        substring(CARRIER, 2, length(CARRIER) -1) AS carrier,
        substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num,
        ORIGIN_AIRPORT_ID AS origin_airport_id,
        substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code,
        substring(ORIGIN_CITY_NAME, 2) AS origin_city_name,
        substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr,
        DEST_AIRPORT_ID AS dest_airport_id,
        substring(DEST, 2, length(DEST) -1) AS dest_airport_code,
        substring(DEST_CITY_NAME,2) AS dest_city_name,
        substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr,
        DEP_DELAY_NEW AS dep_delay_new,
        ARR_DELAY_NEW AS arr_delay_new,
        CARRIER_DELAY AS carrier_delay,
        WEATHER_DELAY AS weather_delay,
        NAS_DELAY AS nas_delay,
        SECURITY_DELAY AS security_delay,
        LATE_AIRCRAFT_DELAY AS late_aircraft_delay
    FROM delays_raw;
    ```

3. Um die Datei zu speichern, drücken Sie **STRG+X** , dann **Y** und schließlich die EINGABETASTE.

4. Starten Sie Hive mit dem folgenden Befehl, und führen Sie die Datei **flightdelays.hql** aus:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
    ```

5. Öffnen Sie nach der Ausführung des Skripts **flightdelays.hql** mithilfe des folgenden Befehls eine interaktive Beeline-Sitzung:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

6. Wenn Sie die `jdbc:hive2://localhost:10001/>`-Eingabeaufforderung erhalten, rufen Sie die Daten mit der folgenden Abfrage aus den importierten Flugverspätungsdaten ab:

    ```hiveql
    INSERT OVERWRITE DIRECTORY '/tutorials/flightdelays/output'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    SELECT regexp_replace(origin_city_name, '''', ''),
        avg(weather_delay)
    FROM delays
    WHERE weather_delay IS NOT NULL
    GROUP BY origin_city_name;
    ```

    Mit dieser Abfrage werden eine Liste von Orten, in denen Verspätungen infolge des Wetters auftraten, sowie die durchschnittliche Verspätung abgerufen. Die Liste wird anschließend in `/tutorials/flightdelays/output` gespeichert. Sqoop liest später die Daten an diesem Speicherort und exportiert sie in die Azure SQL-Datenbank.

7. Geben Sie zum Beenden von Beeline `!quit` an der Eingabeaufforderung ein.

## <a name="create-a-sql-database-table"></a>Erstellen einer SQL-Datenbanktabelle

Es gibt viele Möglichkeiten, eine Verbindung mit der SQL-Datenbank herzustellen und eine Tabelle zu erstellen. Die folgenden Schritte verwenden [FreeTDS](http://www.freetds.org/) aus dem HDInsight-Cluster.

1. Verwenden Sie zum Installieren von FreeTDS den folgenden Befehl über die offene SSH-Verbindung mit dem Cluster:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Nachdem die Installation abgeschlossen ist, verwenden Sie den folgenden Befehl, um eine Verbindung mit SQL-Datenbank herzustellen.

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Eine Ausgabe ähnlich folgendem Text wird angezeigt:

    ```
    locale is "en_US.UTF-8"
    locale charset is "UTF-8"
    using default charset "UTF-8"
    Default database being set to <yourdatabase>
    1>
    ```

3. Geben Sie bei der Eingabeaufforderung `1>` folgende Zeilen ein:

    ```hiveql
    CREATE TABLE [dbo].[delays](
    [origin_city_name] [nvarchar](50) NOT NULL,
    [weather_delay] float,
    CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
    ([origin_city_name] ASC))
    GO
    ```

    Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet. Diese Anweisung erstellt eine Tabelle namens **delays** mit einem gruppierten Index.

    Stellen Sie mit der folgenden Abfrage sicher, dass die Tabelle erstellt wurde:

    ```hiveql
    SELECT * FROM information_schema.tables
    GO
    ```

    Die Ausgabe sieht in etwa wie folgender Text aus:

    ```
    TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
    databaseName       dbo             delays        BASE TABLE
    ```

4. EINGABE `exit` at the `1>` ein.

## <a name="export-data-to-sql-database-using-apache-sqoop"></a>Exportieren von Daten in SQL-Datenbank mithilfe von Apache Sqoop

In den vorherigen Abschnitten haben Sie die transformierten Daten unter `/tutorials/flightdelays/output` kopiert. In diesem Abschnitt verwenden Sie Sqoop, um die Daten aus `/tutorials/flightdelays/output` in die Tabelle zu exportieren, die Sie in Azure SQL-Datenbank erstellt haben.

1. Überprüfen Sie durch Eingeben des folgenden Befehls, ob Sqoop Ihre SQL-Datenbank erreichen kann:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433 --username $SQLUSER --password $SQLPASWORD
    ```

    Dieser Befehl gibt eine Liste mit Datenbanken zurück, in der auch die Datenbank enthalten ist, in der Sie bereits die Tabelle `delays` erstellt haben.

2. Exportieren Sie Daten aus `/tutorials/flightdelays/output` in die `delays`-Tabelle, indem Sie den folgenden Befehl eingeben:

    ```bash
    sqoop export --connect "jdbc:sqlserver://$SQLSERVERNAME.database.windows.net:1433;database=$DATABASE" --username $SQLUSER --password $SQLPASWORD --table 'delays' --export-dir '/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
    ```

    Sqoop stellt eine Verbindung mit der Datenbank her, die die `delays`-Tabelle enthält, und exportiert Daten aus dem Verzeichnis `/tutorials/flightdelays/output` in die `delays`-Tabelle.

3. Nach Abschluss des Sqoop-Befehls stellen Sie über das Hilfsprogramm „tsql“ eine Verbindung mit der Datenbank her, indem Sie den folgenden Befehl eingeben:

    ```bash
    TDSVER=8.0 tsql -H $SQLSERVERNAME.database.windows.net -U $SQLUSER -p 1433 -D $DATABASE -P $SQLPASWORD
    ```

    Überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle „delays“ exportiert wurden:

    ```sql
    SELECT * FROM delays
    GO
    ```

    Es sollte eine Liste der Tabellendaten angezeigt werden. Die Tabelle enthält den Namen der Stadt und die durchschnittliche Flugverspätung für diese Stadt.

    Geben Sie `exit` ein, um das Dienstprogramm tsql zu beenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Tutorials kann es ratsam sein, den Cluster zu löschen. Mit HDInsight werden Ihre Daten in Azure Storage gespeichert, sodass Sie einen Cluster problemlos löschen können, wenn er nicht verwendet wird. Für einen HDInsight-Cluster fallen auch dann Gebühren an, wenn er nicht verwendet wird. Da die Gebühren für den Cluster erheblich höher sind als die Kosten für den Speicher, ist es sinnvoll, nicht verwendete Cluster zu löschen.

Informationen zum Löschen eines Clusters finden Sie unter [Löschen eines HDInsight-Clusters mit Ihrem Browser, PowerShell oder der Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine unformatierte CSV-Datendatei in einen HDInsight-Clusterspeicher importiert und die Daten anschließend mithilfe von Interactive Query in Azure HDInsight transformiert.  Fahren Sie mit dem nächsten Tutorial fort, um mehr über den Apache Hive Warehouse Connector zu erfahren.

> [!div class="nextstepaction"]
> [Integrieren von Apache Spark und Apache Hive per Hive Warehouse Connector](./apache-hive-warehouse-connector.md)