---
title: 'Tutorial: Extrahieren, Transformieren und Laden von Daten mit Azure HDInsight'
description: In diesem Tutorial erfahren Sie, wie Sie Daten aus einem unformatierten CSV-Dataset extrahieren, mithilfe von Apache Hive in Azure HDInsight transformieren und die transformierten Daten dann mit Sqoop in eine Azure SQL-Datenbank laden.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7640b7a6053532360da4c908089aecfe163bd3de
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912653"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-hdinsight"></a>Tutorial: Extrahieren, Transformieren und Laden von Daten mit Azure HDInsight

In diesem Tutorial führen Sie einen ETL-Vorgang durch: Daten extrahieren, transformieren und laden. Sie verwenden eine unformatierte CSV-Datendatei, importieren diese Datei in einen Azure HDInsight-Cluster, transformieren sie mit Apache Hive und laden sie mit Apache Sqoop in Azure SQL-Datenbank.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Extrahieren und Hochladen der Daten in einen HDInsight-Cluster
> * Transformieren der Daten mithilfe von Apache Hive
> * Laden der Daten in Azure SQL-Datenbank mithilfe von Sqoop

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure Data Lake Storage Gen2-Speicherkonto, das für HDInsight konfiguriert ist**

    Siehe [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).

* **Ein Linux-basierter Hadoop-Cluster in HDInsight**

    Weitere Informationen finden Sie unter [Schnellstart: Erste Schritte mit Apache Hadoop und Apache Hive in Azure HDInsight im Azure-Portal](../../hdinsight/hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md).

* **Azure SQL-Datenbank**: Sie verwenden Azure SQL-Datenbank als Zieldatenspeicher. Sollten Sie über keine Datenbank in SQL-Datenbank verfügen, helfen Ihnen die Informationen unter [Erstellen einer Datenbank in Azure SQL-Datenbank im Azure-Portal](../../azure-sql/database/single-database-create-quickstart.md) weiter.

* **Azure CLI**: Wenn Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) nicht installiert haben, finden Sie alle erforderlichen Informationen unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

* **Ein SSH-Client (Secure Shell):** Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Apache Hadoop) per SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="download-the-flight-data"></a>Herunterladen der Flugdaten

1. Rufen Sie die Website von [Research and Innovative Technology Administration, Bureau of Transportation Statistics](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time) (RITA) auf.

2. Wählen Sie auf der Website die folgenden Werte aus:

   | Name | Wert |
   | --- | --- |
   | Filter Year |2013 |
   | Filter Period |January |
   | Felder |Year, FlightDate, Reporting_Airline, IATA_CODE_Reporting_Airline, Flight_Number_Reporting_Airline, OriginAirportID, Origin, OriginCityName, OriginState, DestAirportID, Dest, DestCityName, DestState, DepDelayMinutes, ArrDelay, ArrDelayMinutes, CarrierDelay, WeatherDelay, NASDelay, SecurityDelay, LateAircraftDelay. |
   
   Entfernen Sie die Häkchen bei allen anderen Feldern.

3. Wählen Sie **Herunterladen** aus. Sie erhalten eine ZIP-Datei mit den ausgewählten Datenfeldern.

## <a name="extract-and-upload-the-data"></a>Extrahieren und Hochladen der Daten

In diesem Abschnitt laden Sie Daten in Ihren HDInsight-Cluster hoch und kopieren die Daten dann in Ihr Data Lake Storage Gen2-Konto.

1. Öffnen Sie eine Eingabeaufforderung, und verwenden Sie den folgenden SCP-Befehl (Secure Copy), um die ZIP-Datei in den Hauptknoten des HDInsight-Clusters hochzuladen:

   ```bash
   scp <file-name>.zip <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net:<file-name.zip>
   ```

   * Ersetzen Sie den Platzhalter `<file-name>` durch den Namen der ZIP-Datei.
   * Ersetzen Sie den Platzhalter `<ssh-user-name>` durch den SSH-Anmeldenamen für den HDInsight-Cluster.
   * Ersetzen Sie den Platzhalter `<cluster-name>` durch den Namen des HDInsight-Clusters.

   Wenn Sie für die Authentifizierung Ihrer SSH-Anmeldung ein Kennwort verwenden, werden Sie zur Eingabe dieses Kennworts aufgefordert.

   Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden und den Pfad zum passenden privaten Schlüssel angeben. Beispiel: `scp -i ~/.ssh/id_rsa <file_name>.zip <user-name>@<cluster-name>-ssh.azurehdinsight.net:`.

2. Stellen Sie nach Abschluss des Uploadvorgangs eine SSH-Verbindung mit dem Cluster her. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

   ```bash
   ssh <ssh-user-name>@<cluster-name>-ssh.azurehdinsight.net
   ```

3. Extrahieren Sie die ZIP-Datei mit folgendem Befehl:

   ```bash
   unzip <file-name>.zip
   ```

   Dieser Befehl extrahiert eine **CSV**-Datei.

4. Verwenden Sie den folgenden Befehl, um den Data Lake Storage Gen2-Container zu erstellen.

   ```bash
   hadoop fs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/
   ```

   Ersetzen Sie den Platzhalter `<container-name>` durch den Namen, den Sie für Ihren Container verwenden möchten.

   Ersetzen Sie den Platzhalter `<storage-account-name>` durch den Namen Ihres Speicherkontos.

5. Verwenden Sie den folgenden Befehl, um ein Verzeichnis zu erstellen.

   ```bash
   hdfs dfs -mkdir -p abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data
   ```

6. Kopieren Sie die *CSV*-Datei mit dem folgenden Befehl in das Verzeichnis:

   ```bash
   hdfs dfs -put "<file-name>.csv" abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data/
   ```

   Setzen Sie den Dateinamen in Anführungszeichen, wenn dieser Leerzeichen oder Sonderzeichen enthält.

## <a name="transform-the-data"></a>Transformieren der Daten

In diesem Abschnitt verwenden Sie Beeline, um einen Apache Hive-Auftrag auszuführen.

Im Rahmen des Apache Hive-Auftrags importieren Sie die Daten aus der CSV-Datei in eine Apache Hive-Tabelle namens **delays**.

1. Verwenden Sie an der SSH-Eingabeaufforderung, die bereits für den HDInsight-Cluster geöffnet ist, den folgenden Befehl, um eine neue Datei namens **flightdelays.hql** zu erstellen und zu bearbeiten:

   ```bash
   nano flightdelays.hql
   ```

2. Ändern Sie den folgenden Text, indem Sie die Platzhalter `<container-name>` und `<storage-account-name>` durch den Namen Ihres Containers bzw. Ihres Speicherkontos ersetzen. Kopieren Sie den Text anschließend, und fügen Sie ihn in die Nano-Konsole ein, indem Sie die UMSCHALTTASTE drücken und gleichzeitig mit der rechten Maustaste klicken.

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
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/data';

    -- Drop the delays table if it exists
    DROP TABLE delays;
    -- Create the delays table and populate it with data
    -- pulled in from the CSV file (via the external table defined previously)
    CREATE TABLE delays
    LOCATION 'abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/processed'
    AS
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

3. Speichern Sie die Datei mit STRG+X, und geben Sie dann `Y` ein, wenn Sie zur Eingabe aufgefordert werden.

4. Starten Sie Hive mit dem folgenden Befehl, und führen Sie die Datei **flightdelays.hql** aus:

   ```bash
   beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f flightdelays.hql
   ```

5. Öffnen Sie nach der Ausführung des Skripts __flightdelays.hql__ mithilfe des folgenden Befehls eine interaktive Beeline-Sitzung:

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

   Mit dieser Abfrage werden eine Liste von Orten, in denen Verspätungen infolge des Wetters auftraten, sowie die durchschnittliche Verspätung abgerufen. Die Liste wird anschließend in `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` gespeichert. Sqoop liest später die Daten an diesem Speicherort und exportiert sie in die Azure SQL-Datenbank.

7. Geben Sie zum Beenden von Beeline `!quit` an der Eingabeaufforderung ein.

## <a name="create-a-sql-database-table"></a>Erstellen einer SQL-Datenbanktabelle

Für diesen Vorgang benötigen Sie den Servernamen aus SQL-Datenbank. Führen Sie die folgenden Schritte aus, um den Servernamen festzustellen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie **SQL-Datenbanken** aus.

3. Filtern Sie nach dem Namen der Datenbank, die Sie verwenden möchten. Der Servername befindet sich in der Spalte **Servername**.

4. Filtern Sie nach dem Namen der Datenbank, die Sie verwenden möchten. Der Servername befindet sich in der Spalte **Servername**.

    ![Abrufen der Details zum SQL-Server](./media/data-lake-storage-tutorial-extract-transform-load-hive/get-azure-sql-server-details.png "Abrufen der Details zum SQL-Server")

    Es gibt viele Möglichkeiten, eine Verbindung mit der SQL-Datenbank herzustellen und eine Tabelle zu erstellen. Die folgenden Schritte verwenden [FreeTDS](https://www.freetds.org/) aus dem HDInsight-Cluster.

5. Verwenden Sie zum Installieren von FreeTDS den folgenden Befehl über eine SSH-Verbindung mit dem Cluster:

   ```bash
   sudo apt-get --assume-yes install freetds-dev freetds-bin
   ```

6. Nachdem die Installation abgeschlossen ist, verwenden Sie den folgenden Befehl, um eine Verbindung mit SQL-Datenbank herzustellen.

   ```bash
   TDSVER=8.0 tsql -H '<server-name>.database.windows.net' -U '<admin-login>' -p 1433 -D '<database-name>'
    ```
   * Ersetzen Sie den Platzhalter `<server-name>` durch den logischen SQL-Servernamen.

   * Ersetzen Sie den Platzhalter `<admin-login>` durch den Administratoranmeldenamen für SQL-Datenbank.

   * Ersetzen Sie den Platzhalter `<database-name>` durch den Namen der Datenbank.

   Geben Sie das Kennwort für die SQL-Datenbank-Administratoranmeldung ein, wenn Sie dazu aufgefordert werden.

   Eine Ausgabe ähnlich folgendem Text wird angezeigt:

   ```
   locale is "en_US.UTF-8"
   locale charset is "UTF-8"
   using default charset "UTF-8"
   Default database being set to sqooptest
   1>
   ```

7. Geben Sie an der Eingabeaufforderung `1>` die folgenden Anweisungen ein:

   ```hiveql
   CREATE TABLE [dbo].[delays](
   [OriginCityName] [nvarchar](50) NOT NULL,
   [WeatherDelay] float,
   CONSTRAINT [PK_delays] PRIMARY KEY CLUSTERED
   ([OriginCityName] ASC))
   GO
   ```

8. Nach Eingabe der Anweisung `GO` werden die vorherigen Anweisungen ausgewertet.

   Diese Abfrage erstellt eine Tabelle namens **delays**, die über einen gruppierten Index verfügt.

9. Stellen Sie mithilfe der folgenden Abfrage sicher, dass die Tabelle erstellt wurde:

   ```hiveql
   SELECT * FROM information_schema.tables
   GO
   ```

   Die Ausgabe sieht in etwa wie folgender Text aus:

   ```
   TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
   databaseName       dbo             delays        BASE TABLE
   ```

10. EINGABE `exit` at the `1>` ein.

## <a name="export-and-load-the-data"></a>Exportieren und Laden der Daten

In den vorherigen Abschnitten haben Sie die transformierten Daten unter `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` kopiert. In diesem Abschnitt verwenden Sie Sqoop, um die Daten aus `abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/tutorials/flightdelays/output` in die Tabelle zu exportieren, die Sie in Azure SQL-Datenbank erstellt haben.

1. Verwenden Sie den folgenden Befehl, um zu überprüfen, ob Sqoop Ihre SQL-Datenbank erreichen kann:

   ```bash
   sqoop list-databases --connect jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433 --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD>
   ```

   Der Befehl gibt eine Liste von Datenbanken zurück, in der auch die Datenbank enthalten ist, in der Sie die Tabelle **delays** erstellt haben.

2. Verwenden Sie den folgenden Befehl, um Daten aus der Tabelle **hivesampletable** in die Tabelle **delays** zu exportieren:

   ```bash
   sqoop export --connect 'jdbc:sqlserver://<SERVER_NAME>.database.windows.net:1433;database=<DATABASE_NAME>' --username <ADMIN_LOGIN> --password <ADMIN_PASSWORD> --table 'delays' --export-dir 'abfs://<container-name>@.dfs.core.windows.net/tutorials/flightdelays/output' --fields-terminated-by '\t' -m 1
   ```

   Sqoop stellt eine Verbindung mit der Datenbank her, die die Tabelle **delays** enthält, und exportiert Daten aus dem Verzeichnis `/tutorials/flightdelays/output` in die Tabelle **delays**.

3. Nach Abschluss des `sqoop`-Befehls stellen Sie über das Hilfsprogramm tsql eine Verbindung mit der Datenbank her:

   ```bash
   TDSVER=8.0 tsql -H <SERVER_NAME>.database.windows.net -U <ADMIN_LOGIN> -P <ADMIN_PASSWORD> -p 1433 -D <DATABASE_NAME>
   ```

4. Überprüfen Sie mithilfe der folgenden Anweisungen, ob die Daten in die Tabelle **delays** exportiert wurden:

   ```sql
   SELECT * FROM delays
   GO
   ```

   Es sollte eine Liste der Tabellendaten angezeigt werden. Die Tabelle enthält den Namen der Stadt und die durchschnittliche Flugverspätung für diese Stadt.

5. Geben Sie `exit` ein, um das Hilfsprogramm tsql zu beenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Tutorial wurden nur Ressourcen verwendet, die bereits vorhanden waren. Eine Bereinigung ist nicht erforderlich.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Daten in HDInsight finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)