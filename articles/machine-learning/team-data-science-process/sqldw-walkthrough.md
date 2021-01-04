---
title: 'Erstellen und Bereitstellen eines Modells mit Azure Synapse Analytics: Team Data Science-Prozess'
description: Erstellen Sie ein Machine Learning-Modell unter Verwendung von Azure Synapse Analytics, und stellen Sie es mit einem öffentlich verfügbaren Dataset bereit.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, devx-track-python, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b638cb2b33f24220e7ceb852402862c707cc7bc6
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93316003"
---
# <a name="the-team-data-science-process-in-action-using-azure-synapse-analytics"></a>Der Team Data Science-Prozess in Aktion: Verwenden von Azure Synapse Analytics
In diesem Tutorial führen wir Sie durch die Erstellung und Bereitstellung eines Machine Learning-Modells mit Azure Synapse Analytics für ein öffentlich zugängliches Dataset: das Dataset [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/). Das erstellte binäre Klassifizierungsmodell sagt vorher, ob ein Trinkgeld für eine Fahrt bezahlt wird.  Die Modelle umfassen die Multiklassenklassifizierung (unabhängig davon, ob es ein Trinkgeld gibt oder nicht) und die Regression (die Verteilung der gezahlten Trinkgeldbeträge).

Das Verfahren folgt dem Workflow des [Team Data Science-Prozesses (TDSP)](./index.yml) . Wir zeigen das Einrichten einer Data Science-Umgebung, das Laden der Daten in Azure Synapse Analytics und wie entweder Azure Synapse Analytics oder ein IPython Notebook zum Untersuchen der Daten und Entwickeln von Modellierungsfeatures verwendet werden. Anschließend zeigen wir das Erstellen und Bereitstellen eines Modells mit Azure Machine Learning.

## <a name="the-nyc-taxi-trips-dataset"></a><a name="dataset"></a>Das Dataset „NYC Taxi Trips“
Die „NYC Taxi Trips“-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit Aufzeichnungen von mehr als 173 Millionen einzelner Fahrten mit den zugehörigen Preisen. Jeder Fahrtendatensatz enthält den Start- und den Zielort, jeweils mit Uhrzeit, die anonymisierte Lizenznummer des Fahrers („Hack“) und die eindeutige ID des Taxis („Medallion“). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden Datasets bereitgestellt:

1. Die Datei **trip_data.csv** enthält Fahrtendetails wie die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Die Datei **trip_fare.csv** enthält Details für jede gezahlte Gebühr wie Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den gezahlten Gesamtbetrag für den Fahrpreis. Es folgen einige Beispieleinträge:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Der **eindeutige Schlüssel** für die Zusammenführung von „trip\_data“ und „trip\_fare“ besteht aus den folgenden drei Feldern:

* „medallion“,
* „hack\_license“ und
* „pickup\_datetime“.

## <a name="address-three-types-of-prediction-tasks"></a><a name="mltasks"></a>Drei Typen von Vorhersageaufgaben
Wir formulieren drei Vorhersageprobleme basierend auf *tip\_amount* , um drei Arten von Modellierungsaufgaben zu veranschaulichen:

1. **Binäre Klassifizierung** : Vorhersagen, ob Trinkgeld für eine Fahrt bezahlt wurde. Das bedeutet: *tip\_amount* größer 0 ist ein Positivbeispiel, *tip\_amount* gleich 0 ist ein Negativbeispiel.
2. **Multiklassenklassifizierung** : Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip\_amount* in fünf Fächer oder Klassen auf:

`Class 0 : tip_amount = $0`

`Class 1 : tip_amount > $0 and tip_amount <= $5`

`Class 2 : tip_amount > $5 and tip_amount <= $10`

`Class 3 : tip_amount > $10 and tip_amount <= $20`

`Class 4 : tip_amount > $20`

3. **Regressionsaufgabe** : Vorhersage des Trinkgeldbetrags für eine Fahrt.

## <a name="set-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Einrichten der Azure Data Science-Umgebung für die erweiterte Analyse
Zum Einrichten Ihrer Azure Data Science-Umgebung führen Sie die folgenden Schritte durch:

**Erstellen Ihres eigenen Azure Blob Storage-Kontos**

* Wenn Sie eine eigeneAzure Blob Storage-Instanz bereitstellen, wählen Sie einen geografischen Standort für Azure Blob Storage aus, der so nah wie möglich bei **USA, Süden-Mitte** liegt, wo die NYC Taxi-Daten gespeichert sind. Die Daten werden mit AzCopy aus dem öffentlichen Blobspeichercontainer in einen Container in Ihrem eigenen Speicherkonto kopiert. Je näher Ihre Azure Blob Storage-Instanz an „USA, Süden-Mitte“ liegt, desto schneller wird diese Aufgabe (Schritt 4) abgeschlossen.
* Um Ihr eigenes Azure Storage-Konto zu erstellen, befolgen Sie die in [Informationen zu Azure Storage-Konten](../../storage/common/storage-account-create.md)beschriebenen Schritte. Notieren Sie sich unbedingt die Werte für die folgenden Anmeldeinformationen für das Speicherkonto, da sie später in der exemplarischen Vorgehensweise benötigt werden.

  * **Speicherkontoname**
  * **Speicherkontoschlüssel**
  * **Containername** (in dem die Daten in Azure Blob Storage gespeichert werden sollen)

**Stellen Sie Ihre Azure Synapse Analytics-Instanz bereit.**
Folgen Sie der Dokumentation unter [Erstellen und Abfragen von Azure Synapse Analytics im Azure-Portal](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md), um eine Azure Synapse Analytics-Instanz bereitzustellen. Vergewissern Sie sich, dass Sie sich die folgenden Anmeldeinformationen für Azure Synapse Analytics notieren, die in späteren Schritten verwendet werden.

* **Servername** : \<server Name>.database.windows.net
* **SQLDW-Name (Datenbank)**
* **Benutzername**
* **Kennwort**

**Installieren Sie Visual Studio und SQL Server Data Tools.** Anweisungen finden Sie unter [Erste Schritte mit Visual Studio 2019 für Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Stellen Sie mit Visual Studio eine Verbindung mit Azure Synapse Analytics her.** Anweisungen finden Sie in den Schritten 1 und 2 unter [Herstellen einer Verbindung mit SQL-Analyse in Azure Synapse Analytics](../../synapse-analytics/sql/connect-overview.md).

> [!NOTE]
> Führen Sie die folgende SQL-Abfrage für die Datenbank aus, die Sie in Azure Synapse Analytics erstellt haben (anstelle der Abfrage, die in Schritt 3 des Verbindungsthemas bereitgestellt wird), um **einen Hauptschlüssel zu erstellen**.
>
>

```sql
BEGIN TRY
       --Try to create the master key
    CREATE MASTER KEY
END TRY
BEGIN CATCH
       --If the master key exists, do nothing
END CATCH;
```

**Erstellen Sie einen Machine Learning-Arbeitsbereich unter Ihrem Azure-Abonnement.** Eine Anleitung hierzu finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](../classic/create-workspace.md)beschriebenen Schritte.

## <a name="load-the-data-into-azure-synapse-analytics"></a><a name="getdata"></a>Laden der Daten Azure Synapse Analytics
Öffnen Sie eine Windows PowerShell-Befehlskonsole. Führen Sie die folgenden PowerShell-Befehle zum Herunterladen der Beispiel-SQL-Skriptdateien aus, die wir für Sie auf GitHub bereitstellen. Speichern Sie sie in einem lokalen Verzeichnis, das Sie mit dem Parameter *-DestDir* angeben. Sie können den Wert des Parameters *-DestDir* in ein beliebiges lokales Verzeichnis ändern. Wenn *-DestDir* nicht vorhanden ist, wird es vom PowerShell-Skript erstellt.

> [!NOTE]
> Möglicherweise müssen Sie beim Ausführen des folgenden PowerShell-Skripts die Option **Als Administrator ausführen** verwenden, wenn Sie Administratorrechte benötigen, um Ihr *DestDir* zu erstellen oder darin zu schreiben.
>
>

```azurepowershell
$source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
$ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
$wc = New-Object System.Net.WebClient
$wc.DownloadFile($source, $ps1_dest)
.\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'
```

Nach erfolgreicher Ausführung ändert sich Ihr aktuelles Arbeitsverzeichnis in *-DestDir*. Ihnen sollte in etwa der folgende Bildschirm angezeigt werden:

![Das aktuelle Arbeitsverzeichnis ändert sich.][19]

Führen Sie im *-DestDir* das folgende PowerShell-Skript im Administratormodus aus:

```azurepowershell
./SQLDW_Data_Import.ps1
```

Bei der ersten Ausführung des PowerShell-Skripts werden Sie aufgefordert, die Informationen aus Ihrem Azure Synapse Analytics und Ihrem Azure Blob Storage-Konto einzugeben. Wenn das PowerShell-Skripts erstmals vollständig ausgeführt wurde, wurden die von Ihnen eingegebenen Anmeldeinformationen in eine Konfigurationsdatei „SQLDW.conf“ im aktuellen Arbeitsverzeichnis geschrieben. Bei der zukünftigen Ausführung dieser PowerShell-Skriptdatei können alle benötigten Parameter aus dieser Konfigurationsdatei gelesen werden. Wenn Sie einige Parameter ändern müssen, können Sie entweder die Parameter nach Aufforderung am Bildschirm eingeben, indem Sie diese Konfigurationsdatei löschen und die Parameterwerte wie abgefragt eingeben, oder die Parameterwerte ändern, indem Sie die Datei „SQLDW.conf“ in Ihrem *-DestDir* -Verzeichnis bearbeiten.

> [!NOTE]
> Um beim direkten Lesen von Parametern aus der Datei „SQLDW.conf“ Namenskonflikte mit den Schemas zu vermeiden, die bereits in Ihrem Azure Synapse Analytics vorhanden sind, wird dem Schemanamen aus der Datei „SQLDW.conf“ eine dreistellige Zufallszahl als Standardschemaname für jede Ausführung hinzugefügt. Das PowerShell-Skript könnte Sie auffordern, einen Schemanamen einzugeben: Der Name kann nach Ermessen des Benutzers angegeben werden.
>
>

Diese **PowerShell-Skriptdatei** führt folgende Aufgaben aus:

* **Herunterladen und Installieren von AzCopy** , falls AzCopy noch nicht installiert ist

  ```azurepowershell
  $AzCopy_path = SearchAzCopy
  if ($AzCopy_path -eq $null){
         Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
      InstallAzCopy
      $AzCopy_path = SearchAzCopy
  }
      $env_path = $env:Path
      for ($i=0; $i -lt $AzCopy_path.count; $i++){
          if ($AzCopy_path.count -eq 1){
              $AzCopy_path_i = $AzCopy_path
          } else {
              $AzCopy_path_i = $AzCopy_path[$i]
          }
          if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
              Write-Host $AzCopy_path_i 'not in system path, add it...'
              [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
              $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
              $env_path = $env:Path
          }
  ```

* **Kopieren von Daten in Ihr privates Blobspeicherkonto** aus dem öffentlichen Blob mit AzCopy

  ```azurepowershell
  Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
  $start_time = Get-Date
  AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
  $end_time = Get-Date
  $time_span = $end_time - $start_time
  $total_seconds = [math]::Round($time_span.TotalSeconds,2)
  Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
  Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"
  ```

* **Laden von Daten mithilfe von Polybase (durch Ausführen von LoadDataToSQLDW.sql) in Ihr Azure Synapse Analytics** aus Ihrem privaten Blob-Speicherkonto mit den folgenden Befehlen.

  * Erstellen eines Schemas

    ```sql
    EXEC (''CREATE SCHEMA {schemaname};'');
    ```

  * Erstellen der datenbankbezogenen Anmeldeinformationen

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
    WITH IDENTITY = ''asbkey'' ,
    Secret = ''{StorageAccountKey}''
    ```

  * Erstellen einer externen Datenquelle für einen Azure Storage-Blob

    ```sql
    CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;

    CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
    WITH
    (
        TYPE = HADOOP,
        LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
        CREDENTIAL = {KeyAlias}
    )
    ;
    ```

  * Erstellen eines externen Dateiformats für eine CSV-Datei. Die Daten werden nicht komprimiert, und die Felder werden durch den senkrechten Strich getrennt.

    ```sql
    CREATE EXTERNAL FILE FORMAT {csv_file_format}
    WITH
    (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS
        (
            FIELD_TERMINATOR ='','',
            USE_TYPE_DEFAULT = TRUE
        )
    )
    ;
    ```

  * Erstellen externer Fahrpreis- und Trinkgeldtabellen für das „NYC Taxi“-Dataset in Azure Blob Storage

    ```sql
    CREATE EXTERNAL TABLE {external_nyctaxi_fare}
    (
        medallion varchar(50) not null,
        hack_license varchar(50) not null,
        vendor_id char(3),
        pickup_datetime datetime not null,
        payment_type char(3),
        fare_amount float,
        surcharge float,
        mta_tax float,
        tip_amount float,
        tolls_amount float,
        total_amount float
    )
    with (
        LOCATION    = ''/nyctaxifare/'',
        DATA_SOURCE = {nyctaxi_fare_storage},
        FILE_FORMAT = {csv_file_format},
        REJECT_TYPE = VALUE,
        REJECT_VALUE = 12
    )

      CREATE EXTERNAL TABLE {external_nyctaxi_trip}
      (
             medallion varchar(50) not null,
             hack_license varchar(50)  not null,
             vendor_id char(3),
             rate_code char(3),
             store_and_fwd_flag char(3),
             pickup_datetime datetime  not null,
             dropoff_datetime datetime,
             passenger_count int,
             trip_time_in_secs bigint,
             trip_distance float,
             pickup_longitude varchar(30),
             pickup_latitude varchar(30),
             dropoff_longitude varchar(30),
             dropoff_latitude varchar(30)
      )
      with (
          LOCATION    = ''/nyctaxitrip/'',
          DATA_SOURCE = {nyctaxi_trip_storage},
          FILE_FORMAT = {csv_file_format},
          REJECT_TYPE = VALUE,
          REJECT_VALUE = 12
      )
    ```

    - Laden von Daten aus externen Tabellen in Azure Blob Storage in Azure Synapse Analytics

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_fare}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_fare}
      ;

      CREATE TABLE {schemaname}.{nyctaxi_trip}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      SELECT *
      FROM   {external_nyctaxi_trip}
      ;
      ```

    - Erstellen einer Beispieldatentabelle (NYCTaxi_Sample) und Einfügen von Daten durch Auswahl von SQL-Abfragen für die Tabellen „trip“ und „fare“. (Einige Schritte in dieser exemplarischen Vorgehensweise müssen diese Beispieltabelle verwenden.)

      ```sql
      CREATE TABLE {schemaname}.{nyctaxi_sample}
      WITH
      (
          CLUSTERED COLUMNSTORE INDEX,
          DISTRIBUTION = HASH(medallion)
      )
      AS
      (
          SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
          tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
          tip_class = CASE
                  WHEN (tip_amount = 0) THEN 0
                  WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                  WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                  WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                  ELSE 4
              END
          FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
          WHERE datepart("mi",t.pickup_datetime) = 1
          AND t.medallion = f.medallion
          AND   t.hack_license = f.hack_license
          AND   t.pickup_datetime = f.pickup_datetime
          AND   pickup_longitude <> ''0''
          AND   dropoff_longitude <> ''0''
      )
      ;
      ```

Der geografische Standort Ihrer Speicherkonten wirkt sich auf Ladezeiten aus.

> [!NOTE]
> Je nach dem geografischen Standort Ihres privaten Blob-Speicherkontos dauert der Kopiervorgang der Daten aus einem öffentlichen Blob in Ihr privates Speicherkonto ca. 15 Minuten oder auch länger, und der Vorgang zum Laden von Daten aus Ihrem Speicherkonto in Ihr Azure Synapse Analytics kann 20 Minuten oder länger dauern.
>
>

Sie müssen entscheiden, was erfolgen soll, wenn Sie über doppelte Quell- und Zieldateien verfügen.

> [!NOTE]
> Wenn die CSV-Dateien, die aus dem öffentlichen Blobspeicher in Ihr privates Blobspeicherkonto kopiert werden sollen, bereits in Ihrem privaten Blobspeicherkonto vorhanden sind, werden Sie von AzCopy gefragt, ob Sie sie überschreiben möchten. Wenn Sie sie nicht überschreiben möchten, geben Sie bei Aufforderung **n** ein. Wenn Sie **alle** Dateien überschreiben möchten, geben Sie bei Aufforderung **a** ein. Sie können auch **y** eingeben, um die CSV-Dateien einzeln zu überschreiben.
>
>

![Ausgabe von AzCopy][21]

Sie können Ihre eigenen Daten verwenden. Wenn Ihre Daten auf Ihrem lokalen Computer in einer realen Anwendung gespeichert sind, können Sie AzCopy dennoch zum Hochladen lokaler Daten in Ihren privaten Azure Blob Storage verwenden. Sie müssen im AzCopy-Befehl der PowerShell-Skriptdatei nur den Speicherort von **Source** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, in das lokale Verzeichnis ändern, das Ihre Daten enthält.

> [!TIP]
> Wenn Ihre Daten sich bereits in Ihrer privaten Azure Blob Storage-Instanz in einer realen Anwendung befinden, können Sie den AzCopy-Schritt im PowerShell-Skript überspringen und die Daten direkt in Azure Synapse Analytics hochladen. Dies erfordert zusätzliche Bearbeitung des Skripts, um es dem Format Ihrer Daten anzupassen.
>
>

Dieses PowerShell-Skript bindet auch die Azure Synapse Analytics-Informationen in die Beispieldateien zum Durchsuchen von Daten („SQLDW_Explorations.sql“, „SQLDW_Explorations.ipynb“ und „SQLDW_Explorations_Scripts.py“) ein, sodass diese drei Dateien sofort nach Abschluss des PowerShell-Skripts getestet werden können.

Nach erfolgreicher Ausführung wird folgender Bildschirm angezeigt:

![Ausgabe von einer erfolgreichen Skriptausführung][20]

## <a name="data-exploration-and-feature-engineering-in-azure-synapse-analytics"></a><a name="dbexplore"></a>Durchsuchen von Daten und Entwickeln von Features in Azure Synapse Analytics
In diesem Abschnitt durchsuchen wir Daten und generieren Features durch das direkte Ausführen von SQL-Abfragen für Azure Synapse Analytics mit **Visual Studio Data Tools**. Alle in diesem Abschnitt verwendeten SQL-Abfragen finden Sie im Beispielskript *SQLDW_Explorations.sql*. Diese Datei wurde bereits vom PowerShell-Skript in das lokale Verzeichnis heruntergeladen. Sie können sie auch aus [GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql) abrufen. In die Datei in GitHub sind jedoch keine Azure Synapse Analytics-Informationen eingebunden.

Verbinden Sie sich mithilfe von Visual Studio unter Verwendung des Anmeldenamens und Kennworts von Azure Synapse Analytics mit Ihrem Azure Synapse Analytics, und öffnen Sie den **SQL-Objekt-Explorer** , um zu überprüfen, ob Datenbank sowie Tabellen importiert wurden. Rufen Sie die Datei *SQLDW_Explorations.sql* ab.

> [!NOTE]
> Um einen Parallel Data Warehouse-Abfrage-Editor (PDW) zu öffnen, verwenden Sie den Befehl **Neue Abfrage** , während Ihr PDW im **SQL-Objekt-Explorer** ausgewählt ist. Der standardmäßige SQL-Abfrage-Editor wird von PDW nicht unterstützt.
>
>

Hier sind die Arten der in diesem Abschnitt durchgeführten Aufgaben zum Durchsuchen von Daten und Generieren von Features aufgeführt:

* Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
* Überprüfen der Datenqualität der Felder "longitude" und "latitude"
* Generieren von binären und Multi-Klassen-Klassifizierungsbezeichnern auf der Grundlage von **tip\_amount**.
* Generieren von Funktionen und Berechnen/Vergleichen der Fahrtentfernungen
* Zusammenführen der beiden Tabellen und Extrahieren einer zufälligen Stichprobe zum Erstellen von Modellen

### <a name="data-import-verification"></a>Überprüfung des Datenimports
Diese Abfragen ermöglichen eine schnelle Überprüfung der Anzahl der Zeilen und Spalten in den Tabellen, die zuvor mithilfe des parallelen Polybase-Massenimports aufgefüllt wurden,

-- Melden der Anzahl der Zeilen in der Tabelle „<nyctaxi_trip>“ ohne Tabellenscan

   ```sql
   SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
   ```

-- Melden der Anzahl von Zeilen und Spalten in der Tabelle „<nyctaxi_trip>“

   ```sql
   SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'
   ```

**Ausgabe:** Sie sollten 173.179.759 Zeilen und 14 Spalten erhalten.

### <a name="exploration-trip-distribution-by-medallion"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“
In diesem Beispiel werden die „medallions“ (Taxinummern) mit mehr als 100 abgeschlossenen Fahrten innerhalb eines bestimmten Zeitraums abgefragt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie vom Partitionsschema **pickup\_datetime** abhängig ist. Abfragen an das vollständige DataSet nutzen ebenfalls die partitionierte Tabelle und/oder den Indexscan.

```sql
SELECT medallion, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

**Ausgabe:** Die Abfrage sollte eine Tabelle mit Zeilen zurückgeben, die 13.369 Taxinummern und die Anzahl der mit diesen 2013 erfolgten Fahrten enthalten. Die letzte Spalte enthält die Anzahl erfolgter Fahrten.

### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“ und „hack_license“
In diesem Beispiel werden die Taxinummern („medallions“) und Fahrer („hack_license“) mit mehr als 100 abgeschlossenen Fahrten innerhalb eines bestimmten Zeitraums ermittelt.

```sql
SELECT medallion, hack_license, COUNT(*)
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

**Ausgabe:** Die Abfrage sollte eine Tabelle mit 13.369 Zeilen zurückgeben, die 13.369 Fahrzeug-/Fahrer-IDs enthalten, die 2013 mehr als 100 Fahrten absolviert haben. Die letzte Spalte enthält die Anzahl erfolgter Fahrten.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Bewertung der Datenqualität: Überprüfen der Einträge auf falsche Werte für „longitude“ und/oder „latitude“
In diesem Beispiel wird untersucht, ob die Felder "longitude" und/oder "latitude" entweder einen ungültigen Wert enthalten (die Gradzahl sollte zwischen –90 und 90 liegen) oder als Koordinaten (0,0) aufweisen.

```sql
SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

**Ausgabe:** Die Abfrage gibt 837.467 Fahrten mit ungültigen Feldern für „longitude“ und/oder „latitude“ zurück.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Durchsuchen: Vergleich der Verteilung von Fahrten mit bzw. ohne Trinkgeld
Dieses Beispiel ermittelt die Anzahl von Fahrten mit und ohne Trinkgeld in einem bestimmten Zeitraum (oder im vollständigen Dataset, wenn wie hier das ganze Jahr verwendet wird). Diese Verteilung spiegelt die binäre Bezeichnerverteilung wider, die später für die Modellierung der binären Klassifizierung verwendet wird.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM <schemaname>.<nyctaxi_fare>
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

**Ausgabe:** Die Abfrage sollte die folgenden Werte für Trinkgeldzahlungen für 2013 zurückgeben: 90.447.622 Fälle mit Trinkgeld und 82.264.709 Fälle ohne. 90.447.622 Fälle mit Trinkgeldzahlung und 82.264.709 ohne.

### <a name="exploration-tip-classrange-distribution"></a>Durchsuchen: Verteilung von Trinkgeld nach Klasse/Bereich
In diesem Beispiel wird die Verteilung von Trinkgeldbereichen in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird) berechnet. Diese Verteilung von Bezeichnerklassen wird später für die Modellierung der Multiklassenklassifizierung verwendet.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_fare>
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

**Ausgabe:**

| tip_class | tip_freq |
| --- | --- |
| 1 |82230915 |
| 2 |6198803 |
| 3 |1932223 |
| 0 |82264625 |
| 4 |85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Durchsuchen: Berechnen und Vergleichen der Fahrtlängen
In diesem Beispiel werden die Werte von „longitude“ und „latitude“ für Start- und Zielort in SQL-Geografiepunkte konvertiert. Anschließend werden anhand dieser SQL-Geografiepunkte die Fahrtentfernung berechnet und eine zufällige Stichprobe der Ergebnisse für den Vergleich ausgegeben. Im Beispiel werden die Ergebnisse anhand der zuvor durchgeführten Bewertung der Datenqualität auf gültige Koordinaten begrenzt.

```sql
/****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO

SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
```

### <a name="feature-engineering-using-sql-functions"></a>Featureentwicklung mit SQL-Funktionen
SQL-Funktionen können manchmal eine effiziente Option für die Featureentwicklung sein. In dieser exemplarischen Vorgehensweise haben wir eine SQL-Funktion definiert, um die direkte Entfernung zwischen dem Start- und dem Zielort zu berechnen. Sie können die folgenden SQL-Skripts in **Visual Studio Data Tools** ausführen.

Hier sehen Sie das SQL-Skript, in dem die Funktion „distance“ definiert wird.

```sql
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
  DROP FUNCTION fnCalculateDistance
GO

-- User-defined function calculate the direct distance between two geographical coordinates.
CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

RETURNS float
AS
BEGIN
      DECLARE @distance decimal(28, 10)
      -- Convert to radians
      SET @Lat1 = @Lat1 / 57.2958
      SET @Long1 = @Long1 / 57.2958
      SET @Lat2 = @Lat2 / 57.2958
      SET @Long2 = @Long2 / 57.2958
      -- Calculate distance
      SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
      --Convert to miles
      IF @distance <> 0
      BEGIN
        SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
      END
      RETURN @distance
END
GO
```

Es folgt ein Beispiel zum Aufrufen dieser Funktion, um Features in Ihrer SQL-Abfrage zu generieren:

-- Beispielabfrage zum Aufruf der Funktion zum Erstellen von Features

   ```sql
SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
FROM <schemaname>.<nyctaxi_trip>
WHERE datepart("mi",pickup_datetime)=1
AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND pickup_longitude != '0' AND dropoff_longitude != '0'
   ```

**Ausgabe:** Diese Abfrage generiert eine Tabelle (mit 2.803.538 Zeilen) mit Start- und Zielkoordinaten (in geografischen Breiten- und Längengraden) und entsprechenden direkten Entfernungen in Meilen. Dies sind die Ergebnisse für die ersten drei Zeilen:

| (Zeilennummer) | pickup_latitude | pickup_longitude | dropoff_latitude | dropoff_longitude | DirectDistance |
| --- | --- | --- | --- | --- | --- |
| 1 |40,731804 |-74,001083 |40,736622 |-73,988953 |0,7169601222 |
| 2 |40,715794 |-74.010635 |40,725338 |-74,00399 |0,7448343721 |
| 3 |40,761456 |-73,999886 |40,766544 |-73,988228 |0,7037227967 |

### <a name="prepare-data-for-model-building"></a>Vorbereiten von Daten für die Modellerstellung
Die folgende Abfrage führt die Tabellen **nyctaxi\_trip** und **nyctaxi\_fare** zusammen, generiert der binäre Klassifikationsbezeichner **tipped** , den Bezeichner **tip\_class** für die Multi-Klassen-Klassifizierung und extrahiert eine Stichprobe aus dem vollständig verbundenen Dataset. Die Stichprobennahme erfolgt durch das Abrufen einer Teilmenge der Fahrten basierend auf der Startzeit.  Diese Abfrage kann kopiert und dann direkt in das Modul [Import Data][import-data] in [Azure Machine Learning Studio (Classic)](https://studio.azureml.net) eingefügt werden, um eine direkte Datenerfassung aus der SQL-Datenbankinstanz in Azure zu erzielen. Die Abfrage schließt DataSets mit falschen Koordinaten (0, 0) aus.

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
WHERE datepart("mi",t.pickup_datetime) = 1
AND   t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

Wenn Sie bereit sind, mit Azure Machine Learning fortzufahren, können Sie:

1. die letzte SQL-Abfrage zum Extrahieren und Erstellen von Stichprobendaten speichern und per Kopieren und Einfügen direkt in ein [Import Data][import-data]-Modul in Azure Machine Learning einfügen oder
2. die extrahierten und verarbeiteten Daten, die Sie für Ihr Modell verwenden möchten, in einer neuen Azure Synapse Analytics-Tabelle speichern und dann die neue Tabelle im [Import Data][import-data]-Modul in Azure Machine Learning verwenden. Das PowerShell-Skript hat diese Aufgabe in einem früheren Schritt für Sie erledigt. Sie können Daten direkt aus dieser Tabelle in das „Import Data“-Modul einlesen.

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Durchsuchen von Daten und Entwickeln von Features in IPython Notebook
In diesem Abschnitt werden wir Daten durchsuchen und Features generieren, und zwar sowohl mit Python als auch mit SQL-Abfragen für das zuvor erstellte Azure Synapse Analytics. Ein IPython Notebook-Beispiel namens **SQLDW_Explorations.ipynb** und eine Python-Skriptdatei namens **SQLDW_Explorations_Scripts.py** wurden in Ihr lokales Verzeichnis heruntergeladen. Sie stehen auch auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW)zur Verfügung. Diese beiden Dateien sind in Python-Skripts identisch. Für den Fall, dass Sie keinen IPython Notebook-Server verwenden, wird die Python-Skriptdatei für Sie bereitgestellt. Diese beiden Python-Beispieldateien wurden unter **Python 2.7** entworfen.

Die erforderlichen Azure Synapse Analytics-Informationen im IPython Notebook-Beispiel und die auf Ihren lokalen Computer heruntergeladene Python-Skriptdatei wurden zuvor vom PowerShell-Skript eingebunden. Sie sind ohne Änderung ausführbar.

Wenn Sie bereits einen Azure Machine Learning-Arbeitsbereich eingerichtet haben, können Sie das IPython-Notebook-Beispiel direkt in den AzureML-IPython-Notebook-Dienst hochladen und ausführen. Im Folgenden werden die Schritte zum Hochladen in den AzureML-IPython-Notebook-Dienst aufgeführt:

1. Melden Sie sich bei Ihrem Azure Machine Learning-Arbeitsbereich an, klicken Sie oben auf **Studio** , und klicken Sie auf der linken Seite der Webseite auf **NOTEBOOKS**.

    ![Klicken auf „Studio“ und dann auf „NOTEBOOKS“][22]
2. Klicken Sie in der linken unteren Ecke der Webseite auf **NEW** (Neu), und wählen Sie **Python 2** aus. Geben Sie dann einen Namen für das Notebook ein, und klicken Sie auf das Häkchen, um das neue, leere IPython Notebook zu erstellen.

    ![Klicken auf „Neu“ und Auswählen von „Python 2“][23]
3. Klicken Sie auf das Symbol **Jupyter** in der linken oberen Ecke des neuen IPython-Notebooks.

    ![Klicken auf das Jupyter-Symbol][24]
4. Ziehen Sie das IPython Notebook-Beispiel auf die **Strukturseite** Ihres AzureML IPython Notebook-Diensts, und klicken Sie auf **Upload**. Das IPython Notebook-Beispiel wird dann in den AzureML IPython Notebook-Dienst hochgeladen.

    ![Klicken Sie auf „Hochladen“.][25]

Zum Ausführen des IPython Notebook-Beispiels oder der Python-Skriptdatei sind die folgenden Python-Pakete erforderlich. Wenn Sie den AzureML IPython Notebook-Dienst verwenden, wurden diese Pakete bereits vorinstalliert.

- pandas
- numpy
- matplotlib
- pyodbc
- PyTables

Die empfohlene Reihenfolge beim Erstellen fortgeschrittener Analyselösungen mit großen Datenmengen in Azure Machine Learning lautet wie folgt:

* Einlesen eines kleinen Teils der Daten in ein DataFrame im Speicher
* Durchführen von Visualisierungen und Suchvorgängen mit den Beispieldaten
* Experimentieren mit der Featureentwicklung anhand der Beispieldaten
* Verwenden von Python für das direkte Ausführen von SQL-Abfragen für das Azure Synapse Analytics bei umfangreicherer Datendurchsuchung, Datenbearbeitung und Featureentwicklung
* Treffen von Entscheidungen zum Umfang von Stichproben für die Modellerstellung in Azure Machine Learning

Es folgen einige Beispiele für das Durchsuchen von Daten, die Datenvisualisierung und die Featureentwicklung. Weitere Datensuchvorgänge finden Sie im IPython Notebook-Beispiel und in der Python-Beispielskriptdatei.

### <a name="initialize-database-credentials"></a>Initialisieren von Datenbank-Anmeldeinformationen
Initialisieren Sie die Datenbank-Verbindungseinstellungen in den folgenden Variablen:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database driver>
```

### <a name="create-database-connection"></a>Erstellen der Datenbankverbindung
Hier ist die Verbindungszeichenfolge aufgeführt, die die Verbindung mit der Datenbank erstellt.

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Melden der Anzahl von Zeilen und Spalten in der Tabelle <nyctaxi_trip>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Gesamtanzahl von Zeilen = 173.179.759
* Gesamtanzahl von Spalten = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_fare"></a>Melden der Anzahl von Zeilen und Spalten in der Tabelle <nyctaxi_fare>

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Gesamtanzahl von Zeilen = 173.179.759
* Gesamtanzahl von Spalten = 11

### <a name="read-in-a-small-data-sample-from-the-azure-synapse-analytics-database"></a>Einlesen einer kleinen Datenprobe aus der Azure Synapse Analytics-Datenbank

```sql
t0 = time.time()

query = '''
    SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

Zeit für das Einlesen der Beispieltabelle = 14,096495 Sekunden
Anzahl der abgerufenen Zeilen und Spalten = (1000, 21)

### <a name="descriptive-statistics"></a>Deskriptive Statistik
Jetzt können die erfassten Daten durchsucht werden. Wir beginnen mit einem Blick auf einige deskriptive Statistiken für das Feld **trip\_distance** (oder andere Felder, die Sie angeben möchten).

```sql
df1['trip_distance'].describe()
```

### <a name="visualization-box-plot-example"></a>Visualisierung: Boxplotbeispiel
Als Nächstes sehen wir uns das Boxplotdiagramm für die Fahrtentfernungen an, um die Quantile zu visualisieren.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Boxplotausgabe][1]

### <a name="visualization-distribution-plot-example"></a>Visualisierung: Verteilungsdiagrammbeispiel
Diagramme, die die Verteilung und ein Histogramm für die Stichproben der Fahrtentfernungen visualisieren.

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Verteilungsdiagrammausgabe][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisierung: Balken- und Liniendiagramme
In diesem Beispiel teilen wir die Fahrtentfernungen in fünf Klassifizierungen auf und visualisieren die Klassifizierungsergebnisse.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Die oben genannte Klassifizierungsverteilung können wir wie folgt in einem Balken- oder Liniendiagramm darstellen.

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Balkendiagrammausgabe][3]

and

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```

![Liniendiagrammausgabe][4]

### <a name="visualization-scatterplot-examples"></a>Visualisierung: Punktdiagrammbeispiele
Wir zeigen ein Punktdiagramm zwischen **trip\_time\_in\_secs** und **trip\_distance** , um zu ermitteln, ob es Korrelationen gibt.

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Punktdiagrammausgabe der Beziehung zwischen Zeit und Entfernung][6]

Auf ähnliche Weise können wir die Beziehung zwischen **rate\_code** und **trip\_distance** überprüfen.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Punktdiagrammausgabe der Beziehung zwischen Code und Entfernung][8]

### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Durchsuchen von Stichprobendaten mit SQL-Abfragen in IPython Notebook
In diesem Abschnitt untersuchen wir die Datenverteilungen anhand der Stichprobendaten in der zuvor neu erstellten Tabelle. Ähnliche Suchvorgänge können anhand der ursprünglichen Tabellen ausgeführt werden.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Durchsuchen: Melden der Anzahl von Zeilen und Spalten in der Stichprobentabelle

```sql
nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
print 'Number of rows in sample = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
print 'Number of columns in sample = %d' % ncols.iloc[0,0]
```

#### <a name="exploration-tippednot-tripped-distribution"></a>Durchsuchen: Verteilung von Fahrten mit/ohne Trinkgeld

```sql
query = '''
SELECT tipped, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tipped
    '''

    pd.read_sql(query, conn)
```

#### <a name="exploration-tip-class-distribution"></a>Durchsuchen: Trinkgeldklassenverteilung

```sql
query = '''
    SELECT tip_class, count(*) AS tip_freq
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY tip_class
'''

tip_class_dist = pd.read_sql(query, conn)
```

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Durchsuchen: Darstellen der Trinkgeldverteilung nach Klasse

```sql
tip_class_dist['tip_freq'].plot(kind='bar')
```

![Grafik 26][26]

#### <a name="exploration-daily-distribution-of-trips"></a>Durchsuchen: Tägliche Verteilung der Fahrten

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM <schemaname>.<nyctaxi_sample>
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“ und „hack_license“

```sql
query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-time-distribution"></a>Durchsuchen: Verteilung der Fahrtzeit

```sql
query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distance-distribution"></a>Durchsuchen: Verteilung der Fahrtentfernung

```sql
query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
pd.read_sql(query,conn)
```

#### <a name="exploration-payment-type-distribution"></a>Durchsuchen: Zahlungsartverteilung

```sql
query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
pd.read_sql(query,conn)
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Überprüfen der endgültigen Form der Funktionstabelle

```sql
query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
pd.read_sql(query,conn)
```

## <a name="build-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Entwickeln von Modellen in Azure Machine Learning
Wir können nun mit der Modellerstellung und -bereitstellung in [Azure Machine Learning](https://studio.azureml.net)fortfahren. Die Daten können jetzt für die oben beschriebenen Vorhersageprobleme verwendet werden:

1. **Binäre Klassifizierung** : Vorhersage, ob Trinkgeld für eine Fahrt bezahlt wurde.
2. **Multiklassenklassifizierung** : Vorhersage des Trinkgeldbereichs gemäß den zuvor definierten Klassen.
3. **Regressionsaufgabe** : Vorhersage des Trinkgeldbetrags für eine Fahrt.

Melden Sie sich zum Starten der Modellierungsübung bei Ihrem **(klassischen) Azure Machine Learning** -Arbeitsbereich an. Wenn Sie noch keinen Machine Learning-Arbeitsbereich erstellt haben, siehe [Erstellen eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs](../classic/create-workspace.md).

1. Informationen zu den ersten Schritten in Azure Machine Learning finden Sie unter [Was ist Azure Machine Learning Studio (klassisch)?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Melden Sie sich bei [Azure Machine Learning Studio (klassisch)](https://studio.azureml.net) an.
3. Die Startseite von Machine Learning Studio (klassisch) bietet eine Vielzahl von Informationen, Videos, Tutorials, Links zu Modulreferenzen und anderen Ressourcen. Weitere Informationen zu Azure Machine Learning finden Sie im [Azure Machine Learning-Dokumentationscenter](https://azure.microsoft.com/documentation/services/machine-learning/).

Ein typisches Trainingsexperiment umfasst die folgenden Schritte:

1. Erstellen eines **+NEW** -Experiments
2. Abrufen der Daten in Azure Machine Learning Studio (klassisch)
3. Vorverarbeiten, Transformieren und Ändern der Daten nach Bedarf
4. Generieren von Funktionen nach Bedarf
5. Aufteilen der Daten in DataSets für Training/Überprüfung/Tests (oder Verwenden verschiedener DataSets für alles)
6. Auswählen eines oder mehrerer Algorithmen für das maschinelle Lernen in Abhängigkeit vom zu lösenden Lernproblem Beispiel: Binäre Klassifizierung, Multiklassenklassifizierung, Regression
7. Trainieren eines oder mehrerer Modelle mit dem Trainings-DataSet
8. Bewerten des Validierungs-DataSets mithilfe der trainierten Modelle
9. Evaluieren der Modelle zur Berechnung der relevanten Kennzahlen für das Lernproblem
10. Optimieren der Modelle und Auswählen des geeignetsten Modells für die Bereitstellung

Sie haben in dieser Übung bereits die Daten in Azure Synapse Analytics untersucht und bearbeitet und sich für eine Stichprobengröße für die Erfassung in Azure Machine Learning Studio (Classic) entschieden. Im Folgenden ist das Verfahren zum Erstellen eines oder mehrerer der Vorhersagemodelle aufgeführt:

1. Übertragen Sie die Daten mithilfe des Moduls [Import Data][import-data] im Abschnitt **Data Input and Output** (Dateneingabe und -ausgabe) in Azure Machine Learning Studio (Classic). Weitere Informationen finden Sie auf der Referenzseite zum [Import Data][import-data]-Modul.

    ![Azure ML – Import Data][17]
2. Wählen Sie **Azure SQL-Datenbank** als **Datenquelle** im **Eigenschaften** bereich aus.
3. Geben Sie den DNS-Namen für die Datenbank im Feld **Datenbankservername** ein. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Geben Sie den **Datenbanknamen** in das entsprechende Feld ein.
5. Geben Sie den *SQL-Benutzernamen* unter **Server user account name** und das *Kennwort* unter **Server user account password** ein.
7. Fügen Sie im Textbereich für die **Datenbankabfrage** die Abfrage ein, die die erforderlichen Datenbankfelder (einschließlich berechneter Felder wie die Bezeichner) extrahiert und die Daten auf die gewünschte Stichprobengröße reduziert.

Ein Beispiel für ein binäres Klassifizierungsexperiment zum Lesen von Daten direkt aus der Azure Synapse Analytics-Datenbank wird in der folgenden Abbildung gezeigt. (Ersetzen Sie die Tabellennamen „nyctaxi_trip“ und „nyctaxi_fare“ durch den Schemanamen und die Tabellennamen, die Sie in Ihrer exemplarischen Vorgehensweise verwendet haben.) Ähnliche Experimente können für Multiklassenklassifizierungen und Regressionsprobleme erstellt werden.

![Azure ML-Schulung][10]

> [!IMPORTANT]
> In den Modellierungsbeispielen für Datenextraktion und Stichprobengenerierung in den vorherigen Abschnitten sind **alle Bezeichner für die drei Modellierungsübungen in der Abfrage enthalten**. Ein wichtiger (erforderlicher) Schritt in den einzelnen Modellierungsübungen ist das **Ausschließen** unnötiger Bezeichner für die anderen beiden Probleme und alle anderen **Zielverluste**. Wenn Sie z.B. eine binäre Klassifizierung anwenden, verwenden Sie den Bezeichner **tipped** und schließen die Felder **tip\_class** , **tip\_amount** und **total\_amount** aus. Letztere sind Zielverluste, da sie das bezahlte Trinkgeld beinhalten.
>
> Um nicht benötigte Spalten oder Zielverluste auszuschließen, können Sie eines der Module [Select Columns in Dataset][select-columns] oder [Edit Metadata][edit-metadata] verwenden. Weitere Informationen finden Sie auf den Referenzseiten zu [Select Columns in Dataset][select-columns] und [Edit Metadata][edit-metadata].
>
>

## <a name="deploy-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Bereitstellen von Modellen in Azure Machine Learning
Wenn das Modell fertig ist, können Sie es problemlos als Webdienst direkt aus dem Experiment heraus bereitstellen. Weitere Informationen zum Bereitstellen von Azure ML-Webdiensten finden Sie unter [Bereitstellen von Azure Machine Learning-Webdiensten](../classic/deploy-a-machine-learning-web-service.md).

So stellen Sie einen neuen Webdienst bereit:

1. Erstellen Sie ein Bewertungsexperiment.
2. Stellen Sie den Webdienst bereit.

Zum Erstellen eines Bewertungsexperiments aus einem **beendeten** Trainingsexperiment klicken Sie auf der unteren Aktionsleiste auf **CREATE SCORING EXPERIMENT**.

![Azure-Bewertung][18]

Azure Machine Learning versucht, ein Bewertungsexperiment basierend auf den Komponenten des Trainingsexperiments zu erstellen. Insbesondere werden dabei folgende Schritte durchgeführt:

1. Speichern des trainierten Modells und Entfernen der Modelltrainings-Module
2. Ermitteln eines logischen **Eingabeports** für das erwartete Eingabedatenschema
3. Ermitteln eines logischen **Ausgabeport** s für das erwartete Ausgabeschema für den Webdienst

Wenn das Bewertungsexperiment erstellt wurde, überprüfen Sie die Ergebnisse und passen es bei Bedarf an. Eine typische Anpassung besteht darin, das Eingabedataset oder die Abfrage durch ausgeschlossene Bezeichnerfelder zu ersetzen, da diese Bezeichnerfelder dem Schema nicht zugeordnet werden, wenn der Dienst aufgerufen wird. Es empfiehlt sich möglicherweise auch, die Größe des Eingabedatasets und/oder der Abfrage auf so wenige Datasets zu reduzieren, dass das Eingabeschema ermittelt werden kann. Für den Ausgabeport ist es üblich, alle Eingabefelder auszuschließen und nur die **Scored Labels** und die **Scored Probabilities** mit dem Modul [Select Columns in Dataset][select-columns] in die Ausgabe einzuschließen.

In der folgenden Abbildung finden Sie ein Beispiel für ein Bewertungsexperiment. Wenn Sie die Bereitstellung fertig vorbereitet haben, klicken Sie auf der unteren Aktionsleiste auf die Schaltfläche **PUBLISH WEB SERVICE** .

![Azure ML-Veröffentlichung][11]

## <a name="summary"></a>Zusammenfassung
Zusammenfassend haben Sie in diesem Tutorial eine Azure Data Science-Umgebung erstellt und mit einem großen öffentlichen Dataset gearbeitet und dabei alle Schritte des Team Data Science-Prozesses durchlaufen: von der Datenerfassung über das Modelltraining bis hin zur Bereitstellung eines Azure Machine Learning-Webdiensts.

### <a name="license-information"></a>Lizenzinformationen
Diese exemplarische Vorgehensweise und die zugehörigen Skripts und IPython Notebook(s) werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei „LICENSE.txt“ im Verzeichnis mit dem Beispielcode auf GitHub.

## <a name="references"></a>References
- [Andrés Monroy NYC Taxi Trips – Downloadseite](https://www.andresmh.com/nyctaxitrips/)
- [„FOILing NYC's Taxi Trip Data“ (Freigeben der Taxifahrtendaten für NYC mit FOIL) von Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)
- [NYC Taxi and Limousine Commission Research and Statistics (Forschung und Statistiken der NYC Taxi and Limousine Commission)](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sqldw-walkthrough/azuremltrain.png
[11]: ./media/sqldw-walkthrough/azuremlpublish.png
[12]: ./media/sqldw-walkthrough/ssmsconnect.png
[13]: ./media/sqldw-walkthrough/executescript.png
[14]: ./media/sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/sqldw-walkthrough/bulkimport.png
[17]: ./media/sqldw-walkthrough/amlreader.png
[18]: ./media/sqldw-walkthrough/amlscoring.png
[19]: ./media/sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/sqldw-walkthrough/ps_load_data.png
[21]: ./media/sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data