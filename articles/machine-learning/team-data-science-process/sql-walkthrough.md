---
title: Erstellen und Bereitstellen eines Modells auf einem virtuellen SQL Server-Computer – Team Data Science-Prozess
description: Erstellen Sie ein Machine Learning-Modell unter Verwendung von SQL Server, und stellen Sie es auf einem virtuellen Azure-Computer mit einem öffentlich verfügbaren Dataset bereit.
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
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96007408"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Der Team Data Science-Prozess in Aktion: Verwenden von SQL Server
In diesem Tutorial werden Sie durch die Erstellung und Bereitstellung eines Machine Learning-Modells geleitet. Hierfür werden SQL Server und das öffentlich zugängliche Dataset [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) verwendet. Die Prozedur entspricht dem standardmäßigen Data Science-Workflow: Erfassen und Durchsuchen der Daten, Entwickeln von Features zur Vereinfachung des Lernens und anschließendes Erstellen und Bereitstellen eines Modells.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Beschreibung des NYC Taxi Trips-DataSets
Die NYC Taxi Trips-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit mehr als 173 Millionen einzelnen Fahrten mit den zugehörigen Preisen. Jeder Fahrtendatensatz enthält den Start- und Zielort mit der Uhrzeit, die anonymisierte Lizenznummer des Fahrers (Hack) und die eindeutige ID des Taxis (Medallion). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden Datasets bereitgestellt:

1. Die CSV-Datei "trip_data" enthält Fahrtendetails wie die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Die CSV-Datei "trip_fare" enthält Details für jede bezahlte Gebühr, wie Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den bezahlten Gesamtbetrag für den Fahrpreis. Es folgen einige Beispieleinträge:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Der eindeutige Schlüssel für die Zusammenführung von „trip\_data“ und „trip\_fare“ besteht aus den Feldern: „medallion“, „hack\_licence“ und „pickup\_datetime“.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Beispiele für Vorhersageaufgaben
Wir werden drei Vorhersageprobleme formulieren, die auf *tip\_amount* basieren, nämlich:

* Binäre Klassifizierung: Vorhersagen, ob Trinkgeld für eine Fahrt bezahlt wurde. Das bedeutet: *tip\_amount* größer 0 ist ein Positivbeispiel, *tip\_amount* gleich 0 ist ein Negativbeispiel.
* Multiklassenklassifizierung: Vorhersage des Trinkgeldbereichs für die Fahrt. Wir teilen *tip\_amount* in fünf Fächer oder Klassen auf:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Regressionsaufgabe: Vorhersage des Trinkgeldbetrags für eine Fahrt.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Einrichten der Azure Data Science-Umgebung für die erweiterte Analyse
Wie Sie unter [Planen Ihrer Umgebung](plan-your-environment.md) sehen können, gibt es mehrere Möglichkeiten für die Arbeit mit dem DataSet "NYC Taxi Trips" in Azure:

* Verarbeiten der Daten in Azure-Blobs und anschließendes Modellieren in Azure Machine Learning
* Laden der Daten in eine SQL Server-Datenbank und anschließendes Modellieren in Azure Machine Learning

In diesem Tutorial wird der parallele Massenimport von Daten in SQL Server gezeigt sowie das Durchsuchen von Daten, die Verarbeitung von Funktion und das Downsampling mithilfe von SQL Server Management Studio und IPython Notebook. Die [Beispielskripts](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) und [IPython Notebooks](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) sind auf GitHub verfügbar. Ein Beispiel-IPython Notebook zum Arbeiten mit den Daten in Azure-Blobs ist am gleichen Speicherort verfügbar.

So richten Sie Ihre Azure Data Science-Umgebung ein:

1. [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md)
2. [Erstellen eines Azure Machine Learning-Arbeitsbereichs](../classic/create-workspace.md)
3. [Stellen Sie einen virtuellen Computer für Data Science bereit](../data-science-virtual-machine/overview.md), der als Server für SQL Server und ein IPython Notebook fungiert.
   
   > [!NOTE]
   > Die Beispielskripts und IPython Notebooks werden während der Einrichtung auf den virtuellen Computer für Data Science heruntergeladen. Nach Abschluss der VM-Nachinstallationsskripts finden Sie die Beispiele in der Dokumentbibliothek auf Ihrem virtuellen Computer:  
   > 
   > * Beispielskripts: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Beispiele für IPython Notebooks: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   where `<user_name>` Ihr Windows-Anmeldename für Ihren virtuellen Computers ist. Wir bezeichnen die Beispielordner mit **Sample Scripts** und **Sample IPython Notebooks**.
   > 
   > 

Basierend auf der Größe des Datasets, dem Speicherort der Datenquelle und der ausgewählten Azure-Zielumgebung ähnelt dieses Szenario dem [Szenario \#5: Großes Dataset in lokalen Dateien, Ziel-SQL Server in Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Abrufen der Daten aus der öffentlichen Quelle
Sie können zum Abrufen des Datasets [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) von seinem öffentlichen Speicherort eine der Methoden, die unter [Verschieben von Daten in und aus Azure Blob Storage](move-azure-blob.md) beschrieben werden, zum Kopieren der Daten auf den neuen virtuellen Computer verwenden.

So kopieren Sie die Daten mit AzCopy:

1. Melden Sie sich an dem virtuellen Computer an.
2. Erstellen Sie ein neues Verzeichnis auf dem VM-Datenträger. (Hinweis: Verwenden Sie nicht den temporären Datenträger, der als Datenträger auf dem virtuellen Computer enthalten ist.)
3. Führen Sie in einem Eingabeaufforderungsfenster die folgende AzCopy-Befehlszeile aus, und ersetzen Sie „<path_to_data_folder>“ durch den in (2) erstellten Datenordner:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Nach Abschluss von AzCopy sollten insgesamt 24 komprimierte CSV-Dateien (12 für „trip\_data“ und 12 für „trip\_fare“) im Datenordner enthalten sein.
4. Extrahieren Sie die heruntergeladenen Dateien. Notieren Sie sich den Ordner mit den extrahierten Dateien. Sie verweisen auf diesen Ordner mit <path\_to\_data\_files\>.

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Massenimport von Daten in eine SQL Server-Datenbank
Die Leistung beim Laden/Übertragen großer Datenmengen in eine SQL-Datenbank und den nachfolgenden Abfragen kann mithilfe von *partitionierten Tabellen und Sichten* verbessert werden. In diesem Abschnitt führen wir die Anweisungen unter [Paralleler Massenimport mithilfe von partitionierten SQL-Tabellen](parallel-load-sql-partitioned-tables.md) durch, um eine neue Datenbank zu erstellen und die Daten parallel in partitionierte Tabellen zu laden.

1. Starten Sie, während sie am virtuellen Computer angemeldet sind, **SQL Server Management Studio**.
2. Stellen Sie mithilfe der Windows-Authentifizierung eine Verbindung her.
   
    ![SSMS-Verbindung][12]
3. Wenn Sie noch nicht den SQL Server-Authentifizierungsmodus geändert und einen neuen SQL-Benutzer erstellt haben, öffnen Sie die Skriptdatei **change\_auth.sql** im Ordner **Sample Scripts**. Ändern Sie den Standardbenutzernamen und das Kennwort. Klicken Sie auf der Symbolleiste auf **Ausführen**, um das Skript auszuführen.
   
    ![Skript ausführen][13]
4. Überprüfen und/oder ändern Sie die Standardordner für die SQL Server-Datenbank und die Protokolldateien, um sicherzustellen, dass die neu erstellten Datenbanken auf einem Datenträger gespeichert werden. Das für das Laden von Data Warehousing optimierte SQL Server-VM-Image wurde mit den Datenträgern für Daten- und Protokolldateien vorkonfiguriert. Wenn Ihr virtueller Computer keinen Datenträger enthält und Sie während der Einrichtung des virtuellen Computers neue virtuelle Festplatten hinzugefügt haben, ändern Sie die Standardordner wie folgt:
   
   * Klicken Sie im linken Bereich mit der rechten Maustaste auf den SQL Server-Namen, und klicken Sie dann auf **Eigenschaften**.
     
       ![SQL-Servereigenschaften][14]
   * Wählen Sie aus der Liste **Seite auswählen** auf der linken Seite die Option **Datenbankeinstellungen** aus.
   * Überprüfen und/oder ändern Sie die **Standardspeicherorte für Datenbank** in die Speicherorte Ihrer **Datenträger**. An diesem Speicherort werden die neuen Datenbanken gespeichert, wenn sie mit den Standardeinstellungen erstellt wurden.
     
       ![SQL-Datenbankstandards][15]  
5. Um eine neue Datenbank und einen Satz von Dateigruppen zum Speichern der partitionierten Tabellen zu erstellen, öffnen Sie das Beispielskript **create\_db\_default.sql**. Das Skript erstellt eine neue Datenbank namens **TaxiNYC** und 12 Dateigruppen am Standardspeicherort für Daten. Jede Dateigruppe enthält die Daten aus „trip\_data“ und „trip\_fare data“ eines Monats. Ändern Sie bei Bedarf den Datenbanknamen. Klicken Sie auf **Ausführen**, um das Skript auszuführen.
6. Erstellen Sie als Nächstes zwei Partitionstabellen für „trip\_data“ und „trip\_fare“. Öffnen Sie das Beispielskript **create\_partitioned\_table.sql**. Dadurch wird Folgendes ausgeführt:
   
   * Erstellen einer Partitionsfunktion zum Aufteilen der Daten nach Monaten
   * Erstellen eines Partitionsschemas für die Zuordnung der Daten der einzelnen Monate zu einer anderen Dateigruppe
   * Erstellen Sie zwei partitionierte Tabellen, die dem Partitionsschema zugeordnet sind: **nyctaxi\_trip** enthält „trip\_data“ und **nyctaxi\_fare** enthält „trip\_fare data“.
     
     Klicken Sie auf **Ausführen**, um das Skript auszuführen und die partitionierten Tabellen zu erstellen.
7. Im Ordner **Sample Scripts** befinden sich zwei PowerShell-Beispielskripts zur Veranschaulichung des parallelen Massenimports von Daten in SQL Server-Tabellen.
   
   * **bcp\_parallel\_generic.ps1** ist ein allgemeines Skript für den parallelen Massenimport von Daten in eine Tabelle. Ändern Sie dieses Skript, um die Eingabe- und Zielvariablen wie in den Kommentarzeilen im Skript angegeben festzulegen.
   * **bcp\_parallel\_nyctaxi.ps1** ist eine vorkonfigurierte Version des generischen Skripts, das verwendet werden kann, um beide Tabellen für die „NYC Taxi Trips“-Daten zu laden.  
8. Klicken Sie mit der rechten Maustaste auf den Namen des Skripts **bcp\_parallel\_nyctaxi.ps1**, und klicken Sie dann auf **Bearbeiten**, um es in PowerShell zu öffnen. Überprüfen und ändern Sie die vordefinierten Variablen gemäß den von Ihnen gewählten Namen für Datenbank, Eingabedatenordner, Zielprotokollordner sowie den Pfaden zu den Beispielformatdateien **nyctaxi_trip.xml** und **nyctaxi\_fare.xml** (im Ordner **Sample Scripts**).
   
    ![Massenimportdaten][16]
   
    Sie können auch den Authentifizierungsmodus auswählen, in der Voreinstellung wird die Windows-Authentifizierung verwendet. Klicken Sie für die Ausführung auf den grünen Pfeil auf der Symbolleiste. Das Skript startet 24 Massenimportvorgänge parallel – 12 für jede partitionierte Tabelle. Sie können den Fortschritt des Datenimports überwachen, indem Sie den oben festgelegten Standardordner mit den SQL Server-Daten öffnen.
9. Das PowerShell-Skript meldet die Start- und Endzeiten. Wenn alle Massenimportvorgänge abgeschlossen wurden, wird die Endzeit gemeldet. Überprüfen Sie den Zielprotokollordner, um zu überprüfen, ob die Massenimportvorgänge erfolgreich ausgeführt wurden, d. h., ob keine Fehler im Zielprotokollordner gemeldet wurden.
10. Die Datenbank kann jetzt zum Durchsuchen, zur Funktionsverarbeitung und für andere Vorgänge verwendet werden. Da die Tabellen nach dem Feld **pickup\_datetime** partitioniert sind, profitieren Abfragen mit **pickup\_datetime**-Bedingungen in der **WHERE**-Klausel vom Partitionsschema.
11. Untersuchen Sie in **SQL Server Management Studio** das bereitgestellte Beispielskript **sample\_queries.sql**. Um eine der Beispielabfragen auszuführen, markieren Sie die Abfragezeilen, und klicken Sie auf der Symbolleiste auf **Ausführen**.
12. Die "NYC Taxi Trips"-Daten werden in zwei separate Tabellen geladen. Zur Verbesserung der Join-Vorgänge empfiehlt es sich dringend, die Tabellen zu indizieren. Das Beispielskript **create\_partitioned\_index.sql** erstellt partitionierte Indizes für den zusammengesetzten Verknüpfungsschlüssel **medallion, hack\_license und pickup\_datetime**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Durchsuchen von Daten und Verarbeiten von Funktionen in SQL Server
In diesem Abschnitt durchsuchen wir Daten und generieren Funktionen durch Ausführen von SQL-Abfragen direkt in **SQL Server Management Studio**. Wir verwenden dazu die zuvor erstellte SQL Server-Datenbank. Das Beispielskript **sample\_queries.sql** ist im Ordner **Sample Scripts** enthalten. Ändern Sie das Skript, um den Datenbanknamen zu ändern, falls er nicht dem Standardwert entspricht: **TaxiNYC**.

In dieser Übung führen Sie die folgenden Aktionen durch:

* Herstellen einer Verbindung mit **SQL Server Management Studio** mithilfe der Windows-Authentifizierung oder der SQL-Authentifizierung mit SQL-Anmeldenamen und Kennwort
* Untersuchen der Datenverteilungen einiger Felder in unterschiedlichen Zeitfenstern
* Überprüfen der Datenqualität der Felder "longitude" und "latitude"
* Generieren von binären und Multi-Klassen-Klassifizierungsbezeichnern auf der Grundlage von **tip\_amount**.
* Generieren von Funktionen und Berechnen/Vergleichen der Fahrtentfernungen
* Zusammenführen der beiden Tabellen und Extrahieren einer zufälligen Stichprobe zum Erstellen von Modellen

Wenn Sie bereit sind, mit Azure Machine Learning fortzufahren, können Sie:  

1. die fertige SQL-Abfrage speichern, mit der die Daten extrahiert und zusammengestellt werden, und die Abfrage per Kopieren und Einfügen direkt in ein [Import Data][import-data]-Modul in Azure Machine Learning einfügen oder
2. die zusammengestellten und verarbeiteten Daten, die Sie zur Modellerstellung verwenden möchten, in einer neuen Datenbanktabelle speichern und dann die neue Tabelle im [Import Data][import-data]-Modul in Azure Machine Learning verwenden.

In diesem Abschnitt speichern wir die endgültige Abfrage zum Extrahieren der Daten und zum Erstellen von Proben. Die zweite Methode wird im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in IPython Notebook](#ipnb) beschrieben.

Für eine schnelle Überprüfung der Anzahl von Zeilen und Spalten in den Tabellen, die zuvor mithilfe des parallelen Massenimports aufgefüllt wurden, verwenden Sie folgenden Code:

- Melden der Anzahl der Zeilen in der Tabelle „nyctaxi_trip“ ohne Tabellenscan: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Melden der Anzahl von Zeilen und Spalten in der Tabelle „nyctaxi_trip“: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“
In diesem Beispiel wird die Taxinummer ("medallion") mit mehr als 100 Fahrten innerhalb eines bestimmten Zeitraums ermittelt. Die Abfrage profitiert vom Zugriff auf die partitionierte Tabelle, da sie vom Partitionsschema **pickup\_datetime** abhängig ist. Abfragen an das vollständige DataSet nutzen ebenfalls die partitionierte Tabelle und/oder den Indexscan.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“ und „hack_license“

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Bewertung der Datenqualität: Überprüfen der Einträge auf falsche Werte für „longitude“ und/oder „latitude“
In diesem Beispiel wird untersucht, ob die Felder "longitude" und/oder "latitude" entweder einen ungültigen Wert enthalten (die Gradzahl sollte zwischen –90 und 90 liegen) oder als Koordinaten (0,0) aufweisen.

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Durchsuchen: Vergleich der Verteilung von Fahrten mit und ohne Trinkgeld
Dieses Beispiel ermittelt die Anzahl von Fahrten mit und ohne Trinkgeld in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird). Diese Verteilung spiegelt die binäre Bezeichnerverteilung wider, die später für die Modellierung der binären Klassifizierung verwendet wird.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Durchsuchen: Verteilung von Trinkgeld nach Klasse/Bereich
In diesem Beispiel wird die Verteilung von Trinkgeldbereichen in einem bestimmten Zeitraum (oder im vollständigen DataSet, wenn das ganze Jahr verwendet wird) berechnet. Diese Verteilung der Bezeichnerklassen wird später für die Modellierung der Multiklassenklassifizierung verwendet.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Durchsuchen: Berechnen und Vergleichen der Fahrtentfernungen
In diesem Beispiel werden die Werte von „longitude“ und „latitude“ für Start- und Zielort in SQL-Geografiepunkte konvertiert. Anschließend werden anhand dieser SQL-Geografiepunkte die Fahrtentfernung berechnet und eine zufällige Stichprobe der Ergebnisse für den Vergleich ausgegeben. Im Beispiel werden die Ergebnisse anhand der zuvor durchgeführten Bewertung der Datenqualität auf gültige Koordinaten begrenzt.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Verarbeiten von Funktionen in SQL-Abfragen
Die Suchabfragen zur Generierung von Bezeichnern und zum Konvertieren der Geografiepunkte können auch zum Erstellen von Bezeichnern/Funktionen verwendet werden, indem der Zählaspekt entfernt wird. Weitere SQL-Beispiele zum Verarbeiten von Funktionen finden Sie im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in IPython Notebook](#ipnb). Es ist effizienter, die Abfragen zum Generieren von Funktionen für das komplette Dataset oder einen großen Teil davon auszuführen und dazu SQL-Abfragen zu verwenden, die direkt auf der SQL Server-Datenbankinstanz ausgeführt werden. Die Abfragen können in **SQL Server Management Studio**, IPython Notebook oder einem Entwicklungstool bzw. einer Entwicklungsumgebung ausgeführt werden, sofern lokaler oder Remotezugriff auf die Datenbank besteht.

#### <a name="preparing-data-for-model-building"></a>Vorbereiten von Daten für die Modellerstellung
Die folgende Abfrage führt die Tabellen **nyctaxi\_trip** und **nyctaxi\_fare** zusammen, generiert den binären Klassifikationsbezeichner **tipped** und den Bezeichner **tip\_class** für die Multi-Klassen-Klassifizierung und extrahiert eine zufällige 1-%-Stichprobe aus dem vollständig verbundenen Dataset. Diese Abfrage kann kopiert und dann direkt in das [Import Data][import-data]-Modul von [Azure Machine Learning Studio](https://studio.azureml.net) eingefügt werden, um eine direkte Datenerfassung aus der SQL Server-Datenbankinstanz in Azure zu erzielen. Die Abfrage schließt DataSets mit falschen Koordinaten (0, 0) aus.

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Durchsuchen von Daten und Verarbeiten von Funktionen in IPython Notebook
In diesem Abschnitt werden wir Daten durchsuchen und Funktionen generieren, und zwar sowohl mit Python als auch mit SQL-Abfragen in der zuvor erstellten SQL Server-Datenbank. Ein Beispiel-IPython Notebook namens **machine-Learning-data-science-process-sql-story.ipynb** ist im Ordner **Sample IPython Notebooks** enthalten. Dieses Notebook ist auch auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)verfügbar.

Wenn Sie mit Big Data arbeiten, folgen Sie dieser empfohlenen Reihenfolge:

* Einlesen eines kleinen Teils der Daten in ein DataFrame im Speicher
* Durchführen von Visualisierungen und Suchvorgängen mit den Beispieldaten
* Experimentieren mit der Featureentwicklung anhand der Beispieldaten
* Bei größeren DataSet-Suchvorgängen, Datenbearbeitungsschritten und Funktionsverarbeitungen sollten Sie mithilfe von Python SQL-Abfragen direkt in der SQL Server-Datenbank in der Azure-VM ausführen.
* Treffen von Entscheidungen zur Größe der Stichproben für die Modellerstellung in Azure Machine Learning

Wenn Sie bereit für die Weiterarbeit in Azure Machine Learning sind, können Sie Folgendes durchführen:  

1. Die fertige SQL-Abfrage speichern, mit der die Daten extrahiert und zusammengestellt werden, und die Abfrage per Kopieren und Einfügen direkt in ein [Import Data][import-data]-Modul in Azure Machine Learning einfügen. Dieses Verfahren wird im Abschnitt [Erstellen von Modellen in Azure Machine Learning](#mlmodel) veranschaulicht.    
2. die zusammengestellten und verarbeiteten Daten, die Sie zur Modellerstellung verwenden möchten, in einer neuen Datenbanktabelle speichern und dann die neue Tabelle im [Import Data][import-data]-Modul verwenden.

Es folgen einige Beispiele für das Durchsuchen von Daten, die Datenvisualisierung und das Verarbeiten von Funktionen. Weitere Beispiele finden Sie im Beispiel-SQL-IPython Notebook im Ordner **Sample IPython Notebooks** .

#### <a name="initialize-database-credentials"></a>Initialisieren der Datenbank-Anmeldeinformationen
Initialisieren Sie die Datenbank-Verbindungseinstellungen in den folgenden Variablen:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Erstellen der Datenbankverbindung

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Melden der Anzahl von Zeilen und Spalten in der Tabelle "nyctaxi_trip"

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Gesamtanzahl von Zeilen = 173.179.759  
* Gesamtanzahl von Spalten = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Einlesen einer kleinen Datenprobe aus der SQL Server-Datenbank

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

Zeit für das Einlesen der Beispieltabelle = 6,492000 Sekunden  
Anzahl der abgerufenen Zeilen und Spalten = (84.952, 21)

#### <a name="descriptive-statistics"></a>Deskriptive Statistik
Jetzt können die erfassten Daten durchsucht werden. Wir beginnen mit einem Blick auf die deskriptive Statistik für das Feld **trip\_distance** (oder andere Felder):

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Visualisierung: Boxplotbeispiel
Als Nächstes zeigen wir das Boxplot-Diagramm für die Fahrtentfernungen an, um die Quantile zu visualisieren.

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Grafik 1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisierung: Verteilungsdiagrammbeispiel

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Grafik 2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisierung: Balken- und Liniendiagramme
In diesem Beispiel teilen wir die Fahrtentfernungen in fünf Klassifizierungen auf und visualisieren die Klassifizierungsergebnisse.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Die oben genannte Klassifizierungsverteilung können wir wie unten gezeigt in einem Balken- oder Liniendiagramm darstellen.

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Grafik 3][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![Grafik 4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisierung: Punktdiagrammbeispiel
Wir zeigen ein Punktdiagramm zwischen **trip\_time\_in\_secs** und **trip\_distance**, um zu ermitteln, ob es Korrelationen gibt.

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Grafik 6][6]

Auf ähnliche Weise können wir die Beziehung zwischen **rate\_code** und **trip\_distance** überprüfen.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Grafik 8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Generieren von Stichproben aus den Daten in SQL
Bei der Vorbereitung von Daten für die Modellerstellung in [Azure Machine Learning Studio](https://studio.azureml.net) können Sie sich entweder für eine **direkte Verwendung von SQL-Abfragen im „Import Data“-Modul** oder für das Beibehalten der verarbeiteten und zusammengestellten Daten in einer neuen Tabelle entscheiden, die Sie im [Import Data][import-data]-Modul mit einer einfachen SELECT-Abfrage wie **SELECT * FROM <Name\_Ihrer\_neuen\_Tabelle>** verwenden können.

In diesem Abschnitt erstellen Sie eine neue Tabelle zum Speichern der erfassten und verarbeiteten Daten. Ein Beispiel für eine direkte SQL-Abfrage für die Modellerstellung finden Sie im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in SQL Server](#dbexplore).

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Erstellen einer Beispieltabelle und Auffüllen dieser mit 1 % der verknüpften Tabellen. Löschen Sie zunächst die Tabelle, falls sie bereits vorhanden ist.
In diesem Abschnitt führen wir die Tabellen **nyctaxi\_trip** und **nyctaxi\_fare** zusammen, extrahieren 1% zufälliger Stichproben und speichern die erfassten Daten in der neuen Tabelle **nyctaxi\_one\_percent**:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Durchsuchen von Daten und Verwenden von SQL-Abfragen in IPython Notebook
In diesem Abschnitt untersuchen wir die Datenverteilungen anhand der 1 % Stichprobendaten in der zuvor neu erstellten Tabelle. Ähnliche Suchvorgänge können anhand der ursprünglichen Tabellen ausgeführt werden, wobei die Suchvorgänge optional mit **TABLESAMPLE** auf Stichproben begrenzt werden können, oder dass die Ergebnisse durch die Begrenzung mithilfe von **pickup\_datetime**-Partitionen auf einen bestimmten Zeitraum eingeschränkt werden können. Dies wird im Abschnitt [Durchsuchen von Daten und Verarbeiten von Funktionen in SQL Server](#dbexplore) beschrieben.

#### <a name="exploration-daily-distribution-of-trips"></a>Durchsuchen: Tägliche Verteilung der Fahrten

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Durchsuchen: Verteilung der Fahrten nach „medallion“

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Generieren von Funktionen mithilfe von SQL-Abfragen in IPython Notebook
In diesem Abschnitt erzeugen Sie neue Bezeichner und Funktionen direkt mithilfe von SQL-Abfragen für die Tabelle mit den 1 % Stichproben, die Sie im vorherigen Abschnitt erstellt haben.

#### <a name="label-generation-generate-class-labels"></a>Bezeichnergenerierung: Generieren von Klassenbezeichnern
Im folgenden Beispiel erstellen Sie zwei Sätze von Bezeichnern für die Modellierung:

1. Binäre Klassenbezeichner **tipped** (Vorhersagen, ob ein Trinkgeld gegeben wird)
2. Multi-Klassenbezeichner **tip\_class** (Vorhersage des Trinkgeldbereichs)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funktionsverarbeitung: Zählen von Funktionen für kategorische Spalten
In diesem Beispiel wird ein kategorisches Feld in ein numerisches Feld umgewandelt, indem jede Kategorie durch die Anzahl ihrer Vorkommen in den Daten ersetzt wird.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funktionsverarbeitung: Einteilen von Funktionen für numerische Spalten
In diesem Beispiel wird ein kontinuierliches numerisches Feld in vordefinierte Kategoriebereiche umgewandelt, d. h., das numerische Feld wird in ein kategorisches Feld konvertiert.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funktionsverarbeitung: Extrahieren von Ortsfunktionen aus den Dezimalwerten für „latitude“ und „longitude“
In diesem Beispiel wird die dezimale Darstellung eines Felds "latitude" und/oder "longitude" in mehrere Regionsfelder unterschiedlicher Granularität aufgeteilt, wie z.B. Land, Stadt/Region, Stadtteil, Straße usw. Die neuen Geocode-Felder sind keinen tatsächlichen Positionen zugeordnet. Informationen über die Zuordnung von Geocode-Positionen finden Sie in den [REST-Diensten für Bing Maps](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Überprüfen der endgültigen Form der Funktionstabelle

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Wir können nun mit der Modellerstellung und -bereitstellung in [Azure Machine Learning](https://studio.azureml.net)fortfahren. Die Daten sind für die oben beschriebenen Vorhersageprobleme vorbereitet:

1. Binäre Klassifizierung: Vorhersage, ob Trinkgeld für eine Fahrt bezahlt wurde.
2. Multiklassenklassifizierung: Vorhersage des Trinkgeldbereichs gemäß den zuvor definierten Klassen.
3. Regressionsaufgabe: Vorhersage des Trinkgeldbetrags für eine Fahrt.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Erstellen von Modellen in Azure Machine Learning
Melden Sie sich zum Starten der Modellierungsübung im Azure Machine Learning-Arbeitsbereich an. Wenn Sie noch keinen Machine Learning-Arbeitsbereich erstellt haben, siehe [Erstellen eines Azure Machine Learning-Arbeitsbereichs](../classic/create-workspace.md).

1. Informationen zu den ersten Schritten in Azure Machine Learning finden Sie unter [Was ist Azure Machine Learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Melden Sie sich in [Azure Machine Learning Studio](https://studio.azureml.net)an.
3. Die Startseite von Studio enthält eine Vielzahl an Informationen, Videos, Tutorials, Links zu Modulreferenzen und andere Ressourcen. Weitere Informationen zu Azure Machine Learning finden Sie im [Azure Machine Learning-Dokumentationscenter](https://azure.microsoft.com/documentation/services/machine-learning/).

Ein typisches Trainingsexperiment umfasst die folgenden Schritte:

1. Erstellen eines **+NEW** -Experiments
2. Übertragen Sie die Daten in Azure Machine Learning.
3. Vorverarbeiten, Transformieren und Ändern der Daten nach Bedarf
4. Generieren von Funktionen nach Bedarf
5. Aufteilen der Daten in DataSets für Training/Überprüfung/Tests (oder Verwenden verschiedener DataSets für alles)
6. Auswählen eines oder mehrerer Algorithmen für das maschinelle Lernen in Abhängigkeit vom zu lösenden Lernproblem Beispiel: Binäre Klassifizierung, Multiklassenklassifizierung, Regression
7. Trainieren eines oder mehrerer Modelle mit dem Trainings-DataSet
8. Bewerten des Validierungs-DataSets mithilfe der trainierten Modelle
9. Evaluieren der Modelle zur Berechnung der relevanten Kennzahlen für das Lernproblem
10. Optimieren der Modelle und Auswählen des geeignetsten Modells für die Bereitstellung

Sie haben in dieser Übung bereits die Daten in SQL Server untersucht und bearbeitet und sich für eine Stichprobengröße für die Erfassung in Azure Machine Learning entschieden. Für das Erstellen einer oder mehrerer Vorhersagemodelle gehen Sie folgendermaßen vor:

1. Erfassen Sie die Daten in Azure Machine Learning mithilfe des [Import Data][import-data]-Moduls, das im Abschnitt **Data Input and Output** (Datenein- und -ausgabe) verfügbar ist. Weitere Informationen finden Sie auf der Referenzseite zum [Import Data][import-data]-Modul.
   
    ![Azure Machine Learning: Importieren von Daten][17]
2. Wählen Sie **Azure SQL-Datenbank** als **Datenquelle** im **Eigenschaften** bereich aus.
3. Geben Sie den DNS-Namen für die Datenbank im Feld **Datenbankservername** ein. Format: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Geben Sie den **Datenbanknamen** in das entsprechende Feld ein.
5. Geben Sie den **SQL-Benutzernamen** unter **Server user account name** und das **Kennwort** unter **Server user account password** ein.
7. Fügen Sie im Textbereich für die **Datenbankabfrage** die Abfrage ein, die die erforderlichen Datenbankfelder (einschließlich berechneter Felder wie die Bezeichner) extrahiert und die Daten auf die gewünschte Stichprobengröße reduziert.

Ein Beispiel für ein binäres Klassifizierungsexperiment zum Lesen von Daten direkt aus der SQL Server-Datenbank finden Sie in der folgenden Abbildung. Ähnliche Experimente können für Multiklassenklassifizierungen und Regressionsprobleme erstellt werden.

![Azure Machine Learning: Training][10]

> [!IMPORTANT]
> In den Modellierungsbeispielen für Datenextraktion und Stichprobengenerierung in den vorherigen Abschnitten sind **alle Bezeichner für die drei Modellierungsübungen in der Abfrage enthalten**. Ein wichtiger (erforderlicher) Schritt in den einzelnen Modellierungsübungen ist das **Ausschließen** unnötiger Bezeichner für die anderen beiden Probleme und alle anderen **Zielverluste**. Wenn Sie z.B. eine binäre Klassifizierung nutzen, verwenden Sie den Bezeichner **tipped** und schließen die Felder **tip\_class**, **tip\_amount** und **total\_amount** aus. Letztere sind Zielverluste, da sie das bezahlte Trinkgeld beinhalten.
> 
> Um nicht benötigte Spalten und/oder Zielverluste auszuschließen, können Sie das [Select Columns in Dataset][select-columns]- (Spalten im Dataset auswählen) oder [Edit Metadata][edit-metadata]-Modul (Bearbeiten von Metadaten) verwenden. Weitere Informationen finden Sie auf den Referenzseiten zu [Select Columns in Dataset][select-columns] und [Edit Metadata][edit-metadata].
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Bereitstellen von Modellen in Azure Machine Learning
Wenn das Modell fertig ist, können Sie es problemlos als Webdienst direkt aus dem Experiment heraus bereitstellen. Weitere Informationen zum Bereitstellen von Azure Machine Learning-Webdiensten finden Sie unter [Bereitstellen von Azure Machine Learning-Webdiensten](../classic/deploy-a-machine-learning-web-service.md).

So stellen Sie einen neuen Webdienst bereit:

1. Erstellen Sie ein Bewertungsexperiment.
2. Stellen Sie den Webdienst bereit.

Zum Erstellen eines Bewertungsexperiments aus einem **beendeten** Trainingsexperiment klicken Sie auf der unteren Aktionsleiste auf **CREATE SCORING EXPERIMENT**.

![Azure-Bewertung][18]

Azure Machine Learning versucht, ein Bewertungsexperiment basierend auf den Komponenten des Trainingsexperiments zu erstellen. Insbesondere werden dabei folgende Schritte durchgeführt:

1. Speichern des trainierten Modells und Entfernen der Modelltrainings-Module
2. Ermitteln eines logischen **Eingabeports** für das erwartete Eingabedatenschema
3. Ermitteln eines logischen **Ausgabeport** s für das erwartete Ausgabeschema für den Webdienst

Wenn das Bewertungsexperiment erstellt wurde, überprüfen Sie es und passen es bei Bedarf an. Eine typische Anpassung besteht darin, das Eingabedataset und/oder die Abfrage durch ausgeschlossene Bezeichnerfelder zu ersetzen, da diese Bezeichnungen im Schema nicht verfügbar sein werden, wenn der Dienst aufgerufen wird. Es empfiehlt sich möglicherweise auch, die Größe des Eingabedatasets und/oder der Abfrage auf so wenige Datasets zu reduzieren, dass das Eingabeschema ermittelt werden kann. Für den Ausgabeport ist es üblich, alle Eingabefelder auszuschließen und nur die **Scored Labels** und die **Scored Probabilities** mit dem Modul [Select Columns in Dataset][select-columns] in die Ausgabe einzuschließen.

In der folgenden Abbildung finden Sie ein Beispiel für ein Bewertungsexperiment. Wenn Sie die Bereitstellung fertig vorbereitet haben, klicken Sie auf der unteren Aktionsleiste auf die Schaltfläche **PUBLISH WEB SERVICE** .

![Azure-Machine Learning: Veröffentlichen][11]

Zusammenfassend haben Sie in diesem Tutorial eine Azure Data Science-Umgebung erstellt und mit einem großen öffentlichen DataSet gearbeitet und dabei alle Schritte von der Datenerfassung bis zum Modelltraining und zur Bereitstellung eines Azure Machine Learning-Webdiensts durchlaufen.

### <a name="license-information"></a>Lizenzinformationen
Diese exemplarische Vorgehensweise und die zugehörigen Skripts und IPython Notebook(s) werden von Microsoft unter MIT-Lizenz bereitgestellt. Weitere Informationen finden Sie in der Datei „LICENSE.txt“ im Verzeichnis mit dem Beispielcode auf GitHub.

### <a name="references"></a>References
•    [Andrés Monroy NYC Taxi Trips – Downloadseite](https://www.andresmh.com/nyctaxitrips/)  
•    [FOILing NYC’s Taxi Trip Data von Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [NYC Taxi and Limousine Commission Research and Statistics](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data