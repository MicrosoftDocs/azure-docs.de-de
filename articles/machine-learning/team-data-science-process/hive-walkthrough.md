---
title: 'Untersuchen von Daten in einem Hadoop-Cluster: Team Data Science-Prozess'
description: Verwenden des Team Data Science-Prozesses für ein End-to-End-Szenario mit einem HDInsight Hadoop-Cluster zum Erstellen und Bereitstellen eines Modells.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 53f50e98bcec4b8ace342808f0bcfd96770834b0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002220"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Der Team Data Science-Prozess in Aktion: Verwenden von Azure HDInsight Hadoop-Clustern
In dieser exemplarischen Vorgehensweise wird der [Team Data Science-Prozess (TDSP)](overview.md) in einem durchgängigen Szenario verwendet. Es wird ein [Azure HDInsight Hadoop-Cluster](https://azure.microsoft.com/services/hdinsight/) verwendet, um Daten aus dem öffentlich zugänglichen [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/)-Dataset zu speichern, zu untersuchen und merkmalbezogen zu analysieren sowie ein Downsampling auf die Daten anzuwenden. Um eine binäre Klassifizierung und eine Multiklassenklassifizierung sowie Regressionsvorhersageaufgaben durchzuführen, werden Modelle der Daten mit Azure Machine Learning entwickelt. 

Eine exemplarische Vorgehensweise zur Handhabung eines größeren Datasets finden Sie unter [Team Data Science-Prozess: Verwenden von Azure HDInsight Hadoop-Clustern für ein Dataset mit 1 TB](hive-criteo-walkthrough.md).

Die in dieser exemplarischen Vorgehensweise mit dem 1-TB-Dataset vorgestellten Aufgaben können Sie auch mit einem IPython-Notebook umsetzen. Weitere Informationen finden Sie unter [Criteo-Vorgehensweise bei Verwendung einer Hive ODBC-Verbindung](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Beschreibung des „NYC Taxi Trips“-Datasets
Die „NYC Taxi Trips“-Daten bestehen aus ca. 20 GB an komprimierten CSV-Dateien (~48 GB unkomprimiert). Sie umfassen mehr als 173 Millionen einzelne Fahrten mit den für jede Fahrt gezahlten Fahrtkosten. Jeder Fahrtendatensatz enthält den Start- und Zielort mit der Uhrzeit, die anonymisierte Lizenznummer des Fahrers („Hack“) und die eindeutige ID des Taxis („Medallion“). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden Datasets bereitgestellt:

- Die CSV-Dateien des Typs „trip_data“ enthalten Fahrtendetails: die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- Die CSV-Dateien des Typs „trip_fare“ enthalten Details zu den Kosten für jede Fahrt: Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den entrichteten Gesamtbetrag. Es folgen einige Beispieleinträge:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Der eindeutige Schlüssel für die Zusammenführung von „trip\_data“ und „trip\_fare“ besteht aus den Feldern: „medallion“, „hack\_licence“ und „pickup\_datetime“. Um alle relevanten Details zu einer bestimmten Fahrt abzurufen, reicht eine Zusammenführung mit diesen drei Schlüsseln.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Beispiele für Vorhersageaufgaben
Legen Sie die Art der Vorhersagen fest, die Sie anhand von Datenanalysen vornehmen möchten, um die erforderlichen Aufgaben des Verfahrens klarzustellen. Hier sehen Sie drei Beispiele für Vorhersageprobleme, die in dieser exemplarischen Vorgehensweise behandelt werden. Alle basieren auf *tip\_amount*:

- **Binäre Klassifizierung**: Vorhersage, ob ein Trinkgeld für eine Fahrt bezahlt wurde. Das heißt, ein *tip\_amount* größer als 0 $ ist ein positives Beispiel, während ein *tip\_amount* von 0 $ ein negatives Beispiel ist.

  - Class 0: tip_amount = $0
  - Class 1: tip_amount > $0

- **Multiklassenklassifizierung**: Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip\_amount* in fünf Klassen auf:

  - Class 0: tip_amount = $0
  - Class 1: tip_amount > $0 and tip_amount <= $5
  - Class 2: tip_amount > $5 and tip_amount <= $10
  - Class 3: tip_amount > $10 and tip_amount <= $20
  - Class 4: tip_amount > $20

- **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für die Fahrt.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Einrichten eines HDInsight Hadoop-Clusters für die erweiterte Analyse
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Administrator ausgeführt.
> 
> 

Sie können in drei Schritten eine Azure-Umgebung für die erweiterte Analyse einrichten, in der ein HDInsight-Cluster verwendet wird:

1. [Erstellen Sie ein Speicherkonto](../../storage/common/storage-account-create.md): Mit diesem Speicherkonto werden Daten in Azure Blob Storage gespeichert. Die in HDInsight-Clustern verwendeten Daten werden ebenfalls hier gespeichert.
2. [Anpassen von Azure HDInsight Hadoop-Clustern für erweiterte Analyseprozesse und -technologien](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md) In diesem Schritt erstellen Sie einen HDInsight Hadoop-Cluster, bei dem auf allen Knoten Anaconda Python 2.7 (64 Bit) installiert ist. Bei der Anpassung Ihres HDInsight-Clusters müssen Sie an zwei wichtige Schritte denken.
   
   * Verknüpfen Sie das in Schritt 1 erstellte Speicherkonto mit dem HDInsight-Cluster, wenn Sie diesen erstellen. Dieses Speicherkonto greift auf Daten zu, die innerhalb des Clusters verarbeitet werden.
   * Sie müssen nach dem Erstellen den Remotezugriff auf den Hauptknoten des Clusters aktivieren. Navigieren Sie zur Registerkarte **Konfiguration**, und wählen Sie **Remote aktivieren** aus. Mit diesem Schritt werden die Benutzeranmeldeinformationen für die Remoteanmeldung angegeben.
3. [Erstellen eines Azure Machine Learning-Arbeitsbereichs](../classic/create-workspace.md): Sie verwenden diesen Arbeitsbereich zum Erstellen von Machine Learning-Modellen. Diese Aufgabe wird ausgeführt, nachdem Sie mit dem HDInsight-Cluster eine anfängliche Datenuntersuchung und ein Downsampling durchgeführt haben.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Abrufen der Daten aus der öffentlichen Quelle
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Administrator ausgeführt.
> 
> 

Wenn Sie das [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/)-Dataset aus seinem öffentlichen Speicherort auf Ihren Computer kopieren möchten, verwenden Sie eine der in [Verschieben von Daten in und aus Azure Blob Storage](move-azure-blob.md) beschriebenen Methoden.

Nachfolgend wird erläutert, wie Sie AzCopy zum Übertragen der Dateien verwenden, die Daten enthalten. Folgen Sie den Anweisungen unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy](../../storage/common/storage-use-azcopy-v10.md), um AzCopy herunterzuladen und zu installieren.

1. Führen Sie über ein Eingabeaufforderungsfenster die folgenden AzCopy-Befehle aus, und ersetzen Sie *\<path_to_data_folder>* durch den gewünschten Speicherort:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. Nach Abschluss des Kopiervorgangs sehen Sie insgesamt 24 gezippte Dateien im ausgewählten Datenordner. Entzippen Sie die heruntergeladenen Dateien in dasselbe Verzeichnis auf Ihrem lokalen Computer. Notieren Sie sich den Ordner mit den extrahierten Dateien. Auf diesen Ordner wird nachfolgend als *\<path\_to\_unzipped_data\_files\>* verwiesen.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Hochladen der Daten in den Standardcontainer des HDInsight Hadoop-Clusters
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Administrator ausgeführt.
> 
> 

Ersetzen Sie in den folgenden AzCopy-Befehlen die folgenden Parameter durch die tatsächlichen Werte, die Sie beim Erstellen des Hadoop-Clusters und beim Entzippen der Dateien angegeben haben.

* * **\<path_to_data_folder>** _: Das Verzeichnis (einschließlich des Pfads) auf Ihrem Computer, das die extrahierten Datendateien enthält.  
_ * **\<storage account name of Hadoop cluster>** _: Das mit Ihrem HDInsight-Cluster verknüpfte Speicherkonto.
_ * **\<default container of Hadoop cluster>** _: Der von Ihrem Cluster verwendete Standardcontainer. Der Name des Standardcontainers stimmt üblicherweise mit dem des Clusters überein. Wenn der Cluster beispielsweise den Namen "abc123.azurehdinsight.net" verwendet, heißt der Standardcontainer "abc123".
_ * **\<storage account key>** _: Der Schlüssel für das von Ihrem Cluster verwendete Speicherkonto.

Führen Sie über eine Eingabeaufforderung oder ein Windows PowerShell-Fenster die folgenden beiden AzCopy-Befehle aus.

Dieser Befehl lädt die Fahrtdaten in das Verzeichnis _*_nyctaxitripraw_*_ im Standardcontainer des Hadoop-Clusters hoch.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data__.csv
```

Dieser Befehl lädt die Fahrpreisdaten in das Verzeichnis **nyctaxifareraw** _ im Standardcontainer des Hadoop-Clusters hoch.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare__.csv
```

Die Daten sollten jetzt im Blobspeicher vorliegen und zur Verarbeitung im HDInsight-Cluster genutzt werden können.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Anmelden beim Hauptknoten des Hadoop-Clusters und Vorbereiten einer explorativen Datenanalyse
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Administrator ausgeführt.
> 
> 

Um für eine explorative Datenanalyse und ein Downsampling der Daten auf den Hauptknoten des Clusters zuzugreifen, folgen Sie den Anweisungen unter [Zugreifen auf den Hauptknoten des Hadoop-Clusters](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

In dieser exemplarischen Vorgehensweise werden vorwiegend in [Hive](https://hive.apache.org/) geschriebene Abfragen verwendet. Hive ist eine SQL-ähnliche Abfragesprache zur Durchführung von Vorabdatenuntersuchungen. Die Hive-Abfragen werden in HQL-Dateien gespeichert. Anschließend komprimieren wir diese Daten, um sie für die Modellentwicklung in Machine Learning zu nutzen.

Um den Cluster auf die explorative Datenanalyse vorzubereiten, laden Sie die HQL-Dateien mit den relevanten Hive-Skripts von [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) in ein lokales Verzeichnis („C:\temp“) auf dem Hauptknoten herunter. Öffnen Sie auf dem Hauptknoten im Cluster die Eingabeaufforderung, und führen Sie die folgenden beiden Befehle aus:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

Diese beiden Befehlen laden alle für diese exemplarische Vorgehensweise benötigten HQL-Dateien in das lokale Verzeichnis ***C:\temp&#92;** _ auf dem Hauptknoten herunter.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Erstellen von Hive-Datenbank und Tabellen partitioniert nach Monat
> [!NOTE]
> Diese Aufgabe wird normalerweise durch einen Administrator ausgeführt.
> 
> 

Sie können jetzt Hive-Tabellen für das „NYC Taxi“-Dataset erstellen.
Öffnen Sie im Hauptknoten des Hadoop-Clusters die Hadoop-Befehlszeile auf dem Desktop des Hauptknotens. Geben Sie das Hive-Verzeichnis an, indem Sie den folgenden Befehl ausführen:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Führen Sie alle Hive-Befehle dieser exemplarischen Vorgehensweise über die Eingabeaufforderung im Hive-Verzeichnis „bin/“ aus. So werden alle Pfadprobleme automatisch behoben. Die hier verwendeten Begriffe „Hive-Eingabeaufforderung“, „Hive-Eingabeaufforderung im bin-Verzeichnis“ und „Hadoop-Befehlszeile“ sind austauschbar.
> 
> 

Führen Sie auf dem Hauptknoten an der Hive-Eingabeaufforderung den folgenden Befehl in der Hadoop-Befehlszeile des Hauptknotens aus, der die Hive-Datenbank und -Tabellen erstellt:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Dies ist der Inhalt der Datei _ *C:\temp\sample\_hive\_create\_db\_and\_tables.hql**, die die Hive-Datenbank **nyctaxidb** und die Tabellen **trip** und **fare** erstellt.

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

Dieses Hive-Skript erstellt zwei Tabellen:

* Die Tabelle **trip** enthält Details zu jeder Fahrt (Fahrerinformationen, Abholzeit, Fahrtstrecke und Zeiten).
* Die Tabelle **fare** enthält Details zu den Fahrtkosten (Fahrpreis, Trinkgeld, Mautgebühren und Zuschläge).

Wenn Sie weitere Hilfe bei diesen Verfahren benötigen oder alternative Verfahren untersuchen möchten, lesen Sie den Abschnitt [Übermitteln der Hive-Abfragen direkt an der Hadoop-Befehlszeile](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Laden von Daten in Hive-Tabellen nach Partitionen
> [!NOTE]
> Diese Aufgabe wird normalerweise durch einen Administrator ausgeführt.
> 
> 

Das NYC Taxi-Dataset ist bereits nach Monat partitioniert, was wir für eine schnellere Verarbeitung und Abfrage nutzen. Die folgenden PowerShell-Befehle (ausgegeben über das Hive-Verzeichnis mithilfe der Hadoop-Befehlszeile) laden Daten in die nach Monat partitionierten Hive-Tabellen „trip“ und „fare“.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

Die Datei **sample\_hive\_load\_data\_by\_partitions.hql** enthält die folgenden **LOAD**-Befehle:

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

Für einige der in der Untersuchung verwendeten Hive-Abfragen müssen nur eine oder zwei Partitionen betrachtet werden. Diese Abfragen können jedoch auch über das gesamte Dataset ausgeführt werden.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Anzeigen von Datenbanken im HDInsight Hadoop-Cluster
Um die in HDInsight Hadoop-Clustern erstellten Datenbanken im Hadoop-Befehlszeilenfenster anzuzeigen, führen Sie den folgenden Befehl über die Hadoop-Befehlszeile aus:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Anzeigen der Hive-Tabellen in der Datenbank **nyctaxidb**
Führen Sie zum Anzeigen der Tabellen in der Datenbank **nyctaxidb** folgenden Befehl über die Hadoop-Befehlszeile aus:

```console
hive -e "show tables in nyctaxidb;"
```

Mithilfe des folgenden Befehls lässt sich bestätigen, dass die Tabellen partitioniert sind:

```console
hive -e "show partitions nyctaxidb.trip;"
```

So sieht die erwartete Ausgabe aus:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

Auf ähnliche Weise können Sie über den folgenden Befehl sicherstellen, dass die Fahrpreistabelle partitioniert ist:

```console
hive -e "show partitions nyctaxidb.fare;"
```

So sieht die erwartete Ausgabe aus:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Untersuchen von Daten und Entwickeln von Features in Hive
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Mithilfe von Hive-Abfragen können Sie Datenuntersuchungen durchführen und Features für die in die Hive-Tabellen geladenen Daten entwickeln. Hier finden Sie einige Anwendungsbeispiele:

* Anzeigen der ersten 10 Datensätze in beiden Tabellen
* Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
* Überprüfen der Datenqualität der Felder "longitude" und "latitude"
* Generieren von Bezeichnungen für binäre und Multiklassenklassifizierungen auf der Grundlage des Trinkgelds
* Generieren von Funktionen durch Berechnung der direkten Fahrtentfernungen

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Durchsuchen: Anzeigen der ersten 10 Datensätze in der Tabelle „trip“
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Um einen Eindruck der Daten zu erhalten, untersuchen Sie die ersten 10 Datensätze jeder Tabelle. Führen Sie zum Überprüfen der Datensätze die folgenden zwei Abfragen einzeln von der Hive-Eingabeaufforderung in der Hadoop-Befehlszeilenkonsole aus.

So rufen Sie die ersten 10 Datensätze in der Tabelle „trip“ für den ersten Monat ab:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

So rufen Sie die ersten 10 Datensätze in der Tabelle „fare“ für den ersten Monat ab:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

Sie können die Datensätze in einer Datei speichern und dann mit einer kleinen Änderung an der vorhergehenden Abfrage bequem anzeigen:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Durchsuchen: Anzeigen der Anzahl von Datensätzen in jeder der 12 Partitionen
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Interessant ist, wie die Anzahl von Fahrten im Laufe des Kalenderjahrs variiert. Die Gruppierung nach Monat veranschaulicht die Verteilung der Fahrten.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

Dieser Befehl erstellt die folgende Ausgabe:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

Hier steht die erste Spalte für den Monat, und die zweite Spalte gibt die Anzahl von Fahrten für diesen Monat an.

Zudem lässt sich die Gesamtzahl der Datensätze im „trip“-Dataset berechnen, indem Sie den folgenden Befehl über die Hive-Eingabeaufforderung ausführen:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

Dieser Befehl führt zu folgendem Ergebnis:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Mithilfe ähnlicher Befehle, wie denen für das trip-Dataset, können wir Hive-Abfragen über die Eingabeaufforderung im Hive-Verzeichnis ausführen, um die Anzahl von Datensätzen im fare-Dataset zu ermitteln.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

Dieser Befehl erstellt diese Ausgabe:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

Die genaue Anzahl von Fahrten pro Monat wird für beide Datasets zurückgegeben. Dies ist die erste Bestätigung, dass die Daten ordnungsgemäß geladen wurden.

Sie können die Gesamtzahl der Datensätze im „fare“-Dataset mit folgendem Befehl über die Hive-Eingabeaufforderung ermitteln:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

Dieser Befehl führt zu folgendem Ergebnis:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

Die Gesamtanzahl der Datensätze in beiden Tabellen ist ebenfalls identisch, sodass eine zweite Bestätigung zur Verfügung steht, dass die Daten ordnungsgemäß geladen wurden.

### <a name="exploration-trip-distribution-by-medallion"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“
> [!NOTE]
> Diese Analyse wird normalerweise von einem Data Scientist ausgeführt.
> 
> 

In diesem Beispiel werden die Taxinummern („medallions“) mit mehr als 100 Fahrten innerhalb eines bestimmten Zeitraums ermittelt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie von der Partitionsvariablen **month** abhängig ist. Die Abfrageergebnisse werden in eine lokale Datei namens **queryoutput.tsv** im Verzeichnis `C:\temp` auf dem Hauptknoten geschrieben.

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Dies ist der Inhalt der Datei **sample\_hive\_trip\_count\_by\_medallion.hql**, der untersucht wird.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Über „medallion“ wird im „NYC taxi“-Dataset ein Taxi eindeutig identifiziert. Sie können ermitteln, welche Taxis vergleichsweise häufig zum Einsatz kommen, indem Sie abfragen, welche Taxis innerhalb einer bestimmten Zeit mehr als eine bestimmte Anzahl von Fahrten durchgeführt haben. Im folgenden Beispiel werden Taxis ermittelt, die in den ersten drei Monaten mehr als 100 Fahrten durchgeführt haben. Die Ergebnisse der Abfrage werden in einer lokalen Datei namens **C:\temp\queryoutput.tsv** gespeichert.

Dies ist der Inhalt der Datei **sample\_hive\_trip\_count\_by\_medallion.hql**, der untersucht wird.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Führen Sie über die Hive-Eingabeaufforderung den folgenden Befehl aus:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“ und „hack_license“
> [!NOTE]
> Diese Aufgabe wird normalerweise von einem Data Scientist ausgeführt.
> 
> 

Beim Untersuchen eines Datasets wird häufig die Verteilung von Wertgruppen untersucht. Dieser Abschnitt zeigt anhand eines Beispiels, wie diese Analyse für Taxis und Fahrer durchgeführt werden kann.

In der Datei **sample\_hive\_trip\_count\_by\_medallion\_license.hql** wird das Dataset „fare“ nach **medallion** und **hack_license** gruppiert, und es wird die Anzahl jeder Kombination zurückgegeben. Dies ist der Inhalt der Datei:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

Diese Abfrage gibt Kombinationen aus Taxi und Fahrer zurück, geordnet nach der Anzahl von Fahrten (in absteigender Reihenfolge).

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

Die Abfrageergebnisse werden in eine lokale Datei namens **C:\temp\queryoutput.tsv** geschrieben.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Durchsuchen: Bewerten der Datenqualität durch Prüfen auf Datensätze mit ungültigem Längen- oder Breitengrad
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Eine allgemeines Ziel bei der explorativen Datenanalyse besteht darin, ungültige oder falsche Datensätze auszusortieren. Im Beispiel in diesem Abschnitt wird ermittelt, ob Werte in den Feldern für Längen- oder Breitengrad außerhalb des Bereichs von New York liegen. Da es wahrscheinlich ist, dass solche Datensätze einen falschen Wert für den Längen-/Breitengrad enthalten, sollen diese Daten bei der Modellierung ausgeschlossen werden.

Dies ist der Inhalt der Datei **sample\_hive\_quality\_assessment.hql**, der untersucht wird.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

Das *-S* -Argument in diesem Befehl unterdrückt die Ausgabe der Hive Map/Reduce-Aufträge an den Statusbildschirm. Dieser Befehl ist nützlich, da so die Bildschirmausgabe der Hive-Abfrage besser lesbar ist.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Durchsuchen: Binäre Klassenverteilung von Trinkgeldern für Fahrten
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Für das im Abschnitt [Beispiele für Vorhersageaufgaben](hive-walkthrough.md#mltasks) beschriebene Problem der binären Klassifizierung ist es nützlich zu wissen, ob ein Trinkgeld gegeben wurde oder nicht. Die Verteilung der Trinkgelder ist binär:

* Trinkgeld erhalten (Class 1, tip\_amount > $0)  
* Kein Trinkgeld (Class 0, tip\_amount = $0)

Die Datei **sample\_hive\_tipped\_frequencies.hql** zeigt, welcher Befehl ausgeführt werden soll:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Durchsuchen: Klassenverteilungen in der Multiklasseneinstellung
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Für das im Abschnitt [Beispiele für Vorhersageaufgaben](hive-walkthrough.md#mltasks) beschriebene Problem der Multiklassenklassifizierung eignet sich dieses Dataset für eine natürliche Klassifizierung, um den Betrag der Trinkgelder vorherzusagen. In der Abfrage können Trinkgeldbereiche mithilfe von Containern definiert werden. Zum Abrufen der Klassenverteilungen für die verschiedenen Trinkgeldbereiche verwenden Sie die Datei **sample\_hive\_tip\_range\_frequencies.hql**. Dies ist der Inhalt der Datei.

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Führen Sie den folgenden Befehl über die Hadoop-Befehlszeile aus:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Durchsuchen: Berechnen der direkten Entfernung zwischen zwei Längengrad- oder Breitengradpositionen
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Vielleicht möchten Sie wissen, ob es einen Unterschied zwischen der direkten Entfernung zwischen zwei Orten und der tatsächlichen Fahrtstrecke des Taxis gibt. Ein Kunde ist wahrscheinlich weniger geneigt, ein Trinkgeld zu geben, wenn er bemerkt, dass der Fahrer absichtlich eine längere Route genommen hat.

Um den Unterschied zwischen der tatsächlichen Fahrtstrecke und der Entfernung nach der [Semiversus-Formel](https://en.wikipedia.org/wiki/Haversine_formula) zwischen zwei Längengrad- bzw. Breitengradpositionen (die Großkreisentfernung) zu ermitteln, können Sie die in Hive verfügbaren trigonometrischen Funktionen verwenden:

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

In der vorherigen Abfrage steht R für den Erdradius in Meilen, und Pi wird in das Bogenmaß konvertiert. Die Längen- und Breitengradpositionen werden gefiltert, um vom Bereich New York City zu weiter entfernten Werte zu entfernen.

In diesem Fall werden die Ergebnisse in ein Verzeichnis namens **queryoutputdir** geschrieben. Die nachfolgend gezeigte Befehlssequenz erstellt zunächst das Ausgabeverzeichnis, anschließend wird der Hive-Befehl ausgeführt.

Führen Sie an der Hive-Eingabeaufforderung den folgenden Befehl aus:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

Die Abfrageergebnisse werden in neun Azure-Blobs (von **queryoutputdir/000000\_0** bis **queryoutputdir/000008\_0**) im Standardcontainer des Hadoop-Clusters geschrieben.

Um die Größe der einzelnen Blobs anzuzeigen, führen Sie den folgenden Befehl über die Hive-Eingabeaufforderung aus:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Um die Inhalte einer bestimmten Datei anzuzeigen, beispielsweise **000000\_0**, verwenden Sie den Hadoop-Befehl `copyToLocal`.

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> `copyToLocal` kann für große Dateien sehr langsam sein. Deshalb wird eine Verwendung für große Dateien nicht empfohlen.  
> 
> 

Das Speichern dieser Daten in einem Azure-Blob hat den Vorteil, dass die Daten in Machine Learning mit dem [Import Data][import-data]-Modul untersucht werden können.

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Downsampling von Daten und Entwickeln von Modellen in Machine Learning
> [!NOTE]
> Diese Aufgabe wird typischerweise von einem Data Scientist ausgeführt.
> 
> 

Im Anschluss an die explorative Datenanalyse können Sie ein Downsampling der Daten durchführen, um in Machine Learning Modelle zu entwickeln. In diesem Abschnitt wird gezeigt, wie Sie eine Hive-Abfrage zum Komprimieren der Daten verwenden. Machine Learning greift dann aus dem [Import Data][import-data]-Modul darauf zu.

### <a name="down-sampling-the-data"></a>Downsampling der Daten
Dieses Verfahren umfasst zwei Schritte. Zunächst werden die Tabellen **nyctaxidb.trip** und **nyctaxidb.fare** mithilfe von drei Schlüsseln zusammengeführt, die in allen Datensätzen vorhanden sind: **medallion**, **hack\_license** und **pickup\_datetime**. Anschließend wird die Bezeichnung **tipped** für die binäre Klassifizierung und die Bezeichnung **tip\_class** für die Multiklassenklassifizierung generiert.

Um die Daten nach dem Downsampling direkt aus dem [Import Data][import-data]-Modul in Machine Learning verwenden zu können, sollten Sie die Ergebnisse der vorherigen Abfrage in einer internen Hive-Tabelle speichern. Im Folgenden erstellen wir eine interne Hive-Tabelle und füllen diese mit den zusammengeführten und komprimierten Daten.

Die Abfrage wendet Hive-Standardfunktionen direkt an, um die folgenden Zeitparameter aus dem Feld **pickup\_datetime** zu generieren:
- Stunde des Tages
- Woche des Jahres
- Wochentag („1“ steht für Montag und „7“ für Sonntag)

Die Abfrage generiert auch den direkten Abstand zwischen den Start- und Zielorten. Eine vollständige Liste solcher Funktionen finden Sie unter [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) (in englischer Sprache).

Anschließend führt die Abfrage ein Downsampling der Daten durch, damit die Ergebnisse in Azure Machine Learning Studio verwendet werden können. Es wird nur etwa 1 Prozent des ursprünglichen Datasets in Studio importiert.

Hier sind die Inhalte der Datei **sample\_hive\_prepare\_for\_aml\_full.hql** zu sehen, mit der Daten für die Modellentwicklung in Machine Learning vorbereitet werden:

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

Führen Sie diese Abfrage über die Hive-Eingabeaufforderung aus:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

Sie verfügen nun über eine interne Tabelle namens **nyctaxidb.nyctaxi_downsampled_dataset**, auf die über das [Import Data][import-data]-Modul in Machine Learning zugegriffen werden kann. Darüber hinaus können Sie dieses Dataset zum Entwickeln von Machine Learning-Modellen nutzen.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Verwenden des „Import Data“-Moduls in Machine Learning für den Zugriff auf Daten nach dem Downsampling
Zur Ausführung von Hive-Abfragen im [Import Data][import-data]-Modul von Machine Learning benötigen Sie Zugriff auf einen Machine Learning-Arbeitsbereich. Außerdem benötigen Sie Zugriff auf die Anmeldeinformationen des Clusters und dessen zugehöriges Speicherkonto.

Hier finden Sie einige Details zum [Import Data][import-data]-Modul und zu den Parametern für die Eingabe:

**HCatalog-Server-URI**: Wenn der Clustername **abc123** lautet, verwenden Sie: https:\//abc123.azurehdinsight.net.

**Hadoop user account name**: Der für den Cluster ausgewählte Benutzername (nicht der Benutzername für den Remotezugriff).

**Hadoop user account password**: Das für den Cluster ausgewählte Kennwort (nicht das Kennwort für den Remotezugriff).

**Location of output data**: Als Azure ausgewählt.

**Azure Storage-Kontoname:** Der Name des dem Cluster zugeordneten Standardspeicherkontos.

**Azure container name**: Der standardmäßige Containername für den Cluster, normalerweise der Clustername. Wenn der Cluster **abc123** heißt, lautet der Name „abc123“.

> [!IMPORTANT]
> Jede Tabelle, die mit dem [Import Data][import-data]-Modul in Machine Learning abgefragt werden soll, muss eine interne Tabelle sein.
> 
> 

Sie können ermitteln, ob es sich bei einer Tabelle **T** in einer Datenbank **D.db** um eine interne Tabelle handelt. Führen Sie über die Hive-Eingabeaufforderung den folgenden Befehl aus:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Wenn es sich um eine interne Tabelle handelt und diese mit Daten gefüllt ist, müssen die Inhalte angezeigt werden.

Alternativ können Sie auch mit Azure Storage-Explorer prüfen, ob es sich bei einer Tabelle um eine interne Tabelle handelt. Wechseln Sie im Azure Storage-Explorer zum standardmäßigen Containernamen des Clusters, und filtern Sie anschließend nach dem Tabellennamen. Wenn die Tabelle vorhanden ist und ihre Inhalte angezeigt werden, handelt es sich um eine interne Tabelle.

Hier sehen Sie einen Screenshot der Hive-Abfrage und des[Import Data][import-data]-Moduls:

![Screenshot der Hive-Abfrage für das „Import Data“-Modul](./media/hive-walkthrough/1eTYf52.png)

Da sich die Daten, für die ein Downsampling durchgeführt wurde, in dem Standardcontainer befinden, ist die resultierende Hive-Abfrage in Machine Learning einfach. Geben Sie Folgendes ein: **SELECT * FROM nyctaxidb.nyctaxi\_downsampled\_data**.

Das Dataset kann jetzt als Startpunkt für die Entwicklung von Machine Learning-Modellen verwendet werden.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Entwickeln von Modellen in Machine Learning
Sie können nun mit der Modellentwicklung und -bereitstellung in [Machine Learning](https://studio.azureml.net) fortfahren. Die Daten sind jetzt für die zuvor festgelegten Vorhersageprobleme vorbereitet:

- **Binäre Klassifizierung**: Vorhersage, ob Trinkgeld für eine Fahrt bezahlt wurde.

  **Verwendeter Lernansatz:** Logistische Regression mit zwei Klassen

  a. Für dieses Problem lautet die Zielbezeichnung (oder die Zielklasse) **tipped**. Das ursprüngliche komprimierte Dataset weist einige Spalten auf, die Ziellecks für dieses Klassifizierungsexperiment darstellen. Dies sind insbesondere: **tip\_class**, **tip\_amount** und **total\_amount**. Diese Spalten enthalten Informationen zur Zielbezeichnung, die zum Testzeitpunkt nicht zur Verfügung stehen. Mithilfe des [Select Columns in Dataset][select-columns]-Moduls werden diese Spalten von der Berücksichtigung ausgeschlossen.

  Das folgende Diagramm zeigt unser Experiment zur Vorhersage, ob für eine bestimmte Fahrt ein Trinkgeld gezahlt wurde oder nicht:

  ![Diagramm zum Experiment zur Vorhersage, ob Trinkgeld gezahlt wurde](./media/hive-walkthrough/QGxRz5A.png)

  b. Bei diesem Experiment ergibt sich für die Zielbezeichner eine Verteilung von etwa 1:1.

   Das folgende Diagramm zeigt die Verteilung der „tip_class“-Bezeichnungen für das Problem der binären Klassifizierung:

  ![Diagramm zur Verteilung der „tip_class“-Bezeichnungen](./media/hive-walkthrough/9mM4jlD.png)

    Als Ergebnis erhalten Sie einen AUC-Wert (Area Under Curve) von 0,987, wie in der folgenden Abbildung gezeigt:

  ![Diagramm für AUC-Werte](./media/hive-walkthrough/8JDT0F8.png)

- **Multiklassenklassifizierung**: Zur Vorhersage des Trinkgeldbereichs für die Fahrt gemäß den zuvor definierten Klassen

  **Verwendeter Lernansatz:** Logistische Regression mit mehreren Klassen

  a. Für dieses Problem lautet unsere Zielbezeichnung (oder die Zielklasse) **tip\_class** und kann einen von fünf Werten annehmen (0,1,2,3,4). Wie bei der binären Klassifizierung sind Spalten vorhanden, die Datenlecks für dieses Experiment darstellen. Dies sind insbesondere: **tipped**, **tip\_amount** und **total\_amount**. Diese Spalten enthalten Informationen zur Zielbezeichnung, die zum Testzeitpunkt nicht zur Verfügung stehen. Entfernen Sie diese Spalten mithilfe des [Select Columns in Dataset][select-columns]-Moduls.

  Das folgende Diagramm zeigt das Experiment zur Vorhersage des Trinkgeldbetrags. Es gibt folgende Möglichkeiten: Class 0: tip = $0, Class 1: tip > $0 und tip <= $5, Class 2: tip > $5 und tip <= $10, Class 3: tip > $10 und tip <= $20 sowie Class 4: tip > $20.

  ![Diagramm zum Experiment zur Vorhersage der Klasse für das Trinkgeld](./media/hive-walkthrough/5ztv0n0.png)

  Nachstehend wird gezeigt, wie die tatsächliche Testklassenverteilung aussieht. Während Klasse 0 und Klasse 1 sehr häufig vorkommen, sind die weiteren Klassen eher selten.

  ![Diagramm der Testklassenverteilung](./media/hive-walkthrough/Vy1FUKa.png)

  b. Für dieses Experiment verwenden wir eine Konfusionsmatrix, um die Vorhersagegenauigkeit zu untersuchen, wie hier gezeigt:

  ![Konfusionsmatrix](./media/hive-walkthrough/cxFmErM.png)

  Während die Klassengenauigkeit bei den häufig auftretenden Klassen gut ist, eignet sich das Modell für das „Lernen“ seltenerer Klassen nicht so gut.

- **Regressionsaufgabe**: Vorhersage des Trinkgeldbetrags für eine Fahrt.

  **Verwendeter Lernansatz:** Boosted Decision Tree (Verstärkter Entscheidungsbaum)

  a. Für dieses Problem lautet die Zielbezeichnung (oder die Zielklasse) **tip\_amount**. Die Ziellecks lauten in diesem Fall: **tipped**, **tip\_class** und **total\_amount**. Sämtliche dieser Variablen enthalten Informationen zum Trinkgeldbetrag, die zum Testzeitpunkt normalerweise nicht zur Verfügung stehen. Entfernen Sie diese Spalten mithilfe des [Select Columns in Dataset][select-columns]-Moduls.

  Das folgende Diagramm zeigt das Experiment zur Vorhersage des Trinkgeldbetrags:

  ![Diagramm zum Experiment zur Vorhersage der Höhe des Trinkgelds](./media/hive-walkthrough/11TZWgV.png)

  b. Bei Regressionsproblemen messen wir die Genauigkeit der Vorhersage, indem wir die quadratische Abweichung in den Vorhersagen und den Bestimmungskoeffizienten betrachten:

  ![Screenshot der Vorhersagestatistik](./media/hive-walkthrough/Jat9mrz.png)

  Hier lautet der Bestimmungskoeffizient 0,709, was impliziert, dass etwa 71 Prozent der Varianz durch die Modellkoeffizienten erklärt werden.

> [!IMPORTANT]
> Weitere Informationen zu Machine Learning und dazu, wie Sie darauf zugreifen und es verwenden, finden Sie unter [Was ist Machine Learning?](../classic/index.yml). Zudem umfasst der [Azure KI-Katalog](https://gallery.cortanaintelligence.com/) eine große Palette an Experimenten und bietet eine umfassende Einführung in die Möglichkeiten von Machine Learning.
> 
> 

## <a name="license-information"></a>Lizenzinformationen
Diese exemplarische Vorgehensweise und die zugehörigen Skripts werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei **LICENSE.txt** im Verzeichnis mit dem Beispielcode auf GitHub.

## <a name="references"></a>References
•    [Andrés Monroy NYC Taxi Trips – Downloadseite](https://www.andresmh.com/nyctaxitrips/)  
•    [FOILing NYC’s Taxi Trip Data von Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [NYC Taxi and Limousine Commission Research and Statistics](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data