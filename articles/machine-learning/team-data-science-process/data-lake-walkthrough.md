---
title: 'Skalierbare Data Science mit Azure Data Lake: Team Data Science-Prozess'
description: Informationen zum Verwenden von Azure Data Lake zum Anwenden von Datenuntersuchungs- und binären Klassifizierungsaufgaben auf ein Dataset.
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
ms.openlocfilehash: e6eb0be4d9946907dc5bb2f22b27530a27a37aec
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021451"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalierbare Data Science mit Azure Data Lake: lückenlose exemplarische Vorgehensweise
Diese exemplarische Vorgehensweise veranschaulicht die Nutzung von Azure Data Lake zum Anwenden von Datenuntersuchungs- und binären Klassifizierungsaufgaben auf das Dataset „NYC Taxi Trip and Fare“, um vorherzusagen, ob neben dem Fahrpreis ein Trinkgeld gezahlt wird. Sie werden lückenlos durch die Schritte des [Data Science-Prozesses](./index.yml)begleitet – von der Datenerfassung über das Training des Modells bis zur Bereitstellung eines Webdiensts zum Veröffentlichen des Modells.

## <a name="technologies"></a>Technologien

In dieser exemplarischen Vorgehensweise werden die folgenden Technologien verwendet:
* Azure Data Lake Analytics
* U-SQL und Visual Studio
* Python
* Azure Machine Learning
* Skripts


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) bietet alle Funktionen, die erforderlich sind, um Data Scientists das Speichern von Daten unabhängig von Größe, Form und Tempo zu ermöglichen. Außerdem ermöglicht diese Lösung kostengünstig eine Datenverarbeitung, erweiterte Analyse und Machine Learning-Modellierung mit hoher Skalierbarkeit.   Sie zahlen auftragsbezogen und nur, wenn Daten tatsächlich verarbeitet werden. Zu Data Lake Analytics gehört U-SQL, eine Sprache, bei der die deklarative Art von SQL mit den leistungsstarken Ausdrücken von C# kombiniert wird, um eine skalierbare verteilte Abfragefunktion zu bieten. Sie können unstrukturierte Daten verarbeiten, indem Sie Schemas auf Lesevorgänge anwenden, sowie benutzerdefinierte Logik und benutzerdefinierte Funktionen (UDFs) einfügen. Funktionen zur Erweiterbarkeit sind vorhanden, um eine differenzierte Kontrolle der Ausführung nach Maß zu ermöglichen. Weitere Informationen zur Entwurfsphilosophie von U-SQL finden Sie in diesem [Visual Studio-Blogbeitrag](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Darüber hinaus ist Data Lake Analytics ein wichtiger Bestandteil von Cortana Analytics Suite und kann mit Azure Synapse Analytics, Power BI und Data Factory eingesetzt werden. Mit dieser Kombination erhalten Sie eine umfassende cloudbasierte Big Data-Lösung und erweiterte Analyseplattform.

Am Anfang dieser exemplarischen Vorgehensweise wird das Installieren der Komponenten und Ressourcen beschrieben, die zum Ausführen der Aufgaben für den Data Science-Prozess benötigt werden. Anschließend werden die Datenverarbeitungsschritte beschrieben, die U-SQL verwenden, und zum Abschluss wird die Verwendung von Python und Hive mit Azure Machine Learning Studio (klassisch) zum Erstellen und Bereitstellen der Vorhersagemodelle demonstriert.

### <a name="u-sql-and-visual-studio"></a>U-SQL und Visual Studio
In dieser exemplarischen Vorgehensweise wird die Verwendung von Visual Studio zum Bearbeiten von U-SQL-Skripts und Verarbeiten des Datasets empfohlen. Die U-SQL-Skripts werden hier beschrieben und in einer separaten Datei bereitgestellt. Der Prozess umfasst das Erfassen, Untersuchen und Ziehen von Stichproben der Daten. Ferner wird gezeigt, wie Sie einen skriptgestützten U-SQL-Auftrag im Azure-Portal ausführen. In einem zugeordneten HDInsight-Cluster werden Hive-Tabellen für die Daten generiert, um die Erstellung und Bereitstellung eines binären Klassifizierungsmodells in Azure Machine Learning Studio zu vereinfachen.

### <a name="python"></a>Python
Diese exemplarische Vorgehensweise enthält auch einen Abschnitt, der das Erstellen und Bereitstellen von Vorhersagemodellen mit Python mit Azure Machine Learning Studio veranschaulicht. Sie stellt ein Jupyter Notebook mit den Python-Skripts für die Schritte in diesem Prozess bereit. Das Notebook enthält Code für einige zusätzliche Feature-Engineering-Schritte und modelliert Konstruktionen, wie z. B. mehrklassige Klassifizierungs- und Regressionsmodellierung, zusätzlich zum hier vorgestellten binären Klassifizierungsmodell. Die Regressionsaufgabe besteht darin, die Höhe des Trinkgelds basierend auf anderen Trinkgeldmerkmalen vorherzusagen.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (Classic) wird auf zwei Arten zum Erstellen und Bereitstellen der Vorhersagemodelle verwendet: mit Python-Skripts und mit Hive-Tabellen in einem HDInsight-Cluster (Hadoop).

### <a name="scripts"></a>Skripts
In dieser exemplarischen Vorgehensweise werden nur die wichtigsten Schritte beschrieben. Sie können das vollständige **U-SQL-Skript** und **Jupyter Notebook** von [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) herunterladen.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesen Themen beginnen können, benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie noch keins besitzen, lesen Sie den Artikel [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)(Gewusst wie: Erhalten einer Azure-Testversion zum Testen von Hadoop in HDInsight).
* [Empfohlen] Visual Studio 2013 oder höher. Falls nicht bereits eine dieser Versionen installiert ist, können Sie von der [Visual Studio Community](https://www.visualstudio.com/vs/community/) eine kostenlose Community-Version herunterladen.

> [!NOTE]
> Statt Visual Studio können Sie auch das Azure-Portal nutzen, um Azure Data Lake-Abfragen zu übermitteln. Im Abschnitt **Verarbeiten von Daten mit U-SQL** finden Sie entsprechende Anweisungen für Visual Studio und das Portal.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Vorbereiten der Data Science-Umgebung für Azure Data Lake
Um die Data Science-Umgebung für diese exemplarische Vorgehensweise vorzubereiten, erstellen Sie die folgenden Ressourcen:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob Storage-Konto
* Azure Machine Learning Studio-Konto (klassisch)
* Azure Data Lake-Tools für Visual Studio (empfohlen)

Dieser Abschnitt enthält Anweisungen zum Erstellen dieser Ressourcen. Wenn Sie zum Erstellen eines Modells Hive-Tabellen mit Azure Machine Learning anstelle von Python wählen, müssen Sie auch einen HDInsight-Cluster (Hadoop) bereitstellen. Das alternative Verfahren wird im Abschnitt „Option 2“ beschrieben.


> [!NOTE]
> Der **Azure Data Lake Store** kann entweder getrennt oder bei der Erstellung von **Azure Data Lake Analytics** als Standardspeicher erstellt werden. Sie finden hier Anweisungen zum getrennten Erstellen dieser Ressourcen, doch das Data Lake-Speicherkonto muss nicht getrennt erstellt werden.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Erstellen einer Azure Data Lake Storage-Instanz


Erstellen Sie im [Azure-Portal](https://portal.azure.com) einen Azure Data Lake-Speicher. Einzelheiten dazu finden Sie unter [Erstellen von HDInsight-Clustern mit Azure Data Lake Store mithilfe des Azure-Portals](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Richten Sie die AAD-Identität des Clusters auf dem hier beschriebenen Blatt **Datenquelle** des Blatts **Optionale Konfiguration** ein.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Erstellen eines Azure Data Lake Analytics-Kontos
Erstellen Sie im [Azure-Portal](https://portal.azure.com) ein Azure Data Lake Analytics-Konto. Einzelheiten finden Sie im [Tutorial: Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Erstellen eines Azure Blob Storage-Kontos
Erstellen Sie im [Azure-Portal](https://portal.azure.com) ein Azure Blob Storage-Konto. Ausführliche Informationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-create.md) im Abschnitt „Erstellen eines Speicherkontos“.

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Einrichten eines Azure Machine Learning Studio-Kontos (klassisch)
Registrieren Sie sich über die Seite [Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning/) für Azure Machine Learning Studio (klassisch), bzw. melden Sie sich an. Klicken Sie auf die Schaltfläche **Get started now**, und wählen Sie „Free Workspace“ oder „Standard Workspace“. Jetzt können Sie in Azure Machine Learning Studio Experimente erstellen.

### <a name="install-azure-data-lake-tools-recommended"></a>Installieren von Azure Data Lake-Tools [Empfohlen]
Installieren Sie Azure Data Lake-Tools für Ihre Version von Visual Studio entsprechend den Angaben unter [Azure Data Lake-Tools für Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

Öffnen Sie nach Abschluss der Installation Visual Studio. Die Registerkarte „Data Lake“ sollte auf der oberen Menüleiste angezeigt werden. Nachdem Sie sich bei Ihrem Azure-Konto angemeldet haben, sollten Ihre Azure-Ressourcen im linken Bereich angezeigt werden.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>Das Dataset „NYC Taxi Trips“
Das hier verwendete Dataset ist ein öffentlich verfügbares Dataset mit dem Namen [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/). Die „NYC Taxi Trips“-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (~48 GB unkomprimiert) mit Aufzeichnungen von mehr als 173 Millionen einzelner Fahrten mit den zugehörigen Preisen. Jeder Fahrtendatensatz enthält den Start- und Zielort, jeweils mit Uhrzeit, die anonymisierte Lizenznummer des Fahrers („Hack“) und die eindeutige ID des Taxis („Medallion“). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden Datasets bereitgestellt:

Die CSV-Datei "trip_data" enthält Fahrtendetails wie die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

Die CSV-Datei "trip_fare" enthält Details für jede bezahlte Gebühr, wie Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den bezahlten Gesamtbetrag für den Fahrpreis. Es folgen einige Beispieleinträge:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Der eindeutige Schlüssel für die Zusammenführung von „trip\_data“ und „trip\_fare“ besteht aus den folgenden drei Feldern: „medallion“, „hack\_license“ und „pickup\_datetime“. Der Zugriff auf die unformatierten CSV-Dateien kann über ein Azure Storage-Blob erfolgen. Das U-SQL-Skript für diese Verknüpfung befindet sich im Abschnitt [Verknüpfen der Tabellen mit Fahrten und Trinkgeldern](#join) .

## <a name="process-data-with-u-sql"></a>Verarbeiten von Daten mit U-SQL
Die in diesem Abschnitt beschriebenen Datenverarbeitungsaufgaben umfassen das Erfassen der Daten, das Prüfen ihrer Qualität, ihre Untersuchung und das Ziehen von Stichproben. Es wird außerdem gezeigt, wie Fahrten- und Trinkgeldtabellen verknüpft werden. Im letzten Abschnitt wird gezeigt, wie Sie einen skriptgestützten U-SQL-Auftrag im Azure-Portal ausführen. Es folgen Links zu den einzelnen Unterabschnitten:

* [Datenerfassung: Einlesen von Daten aus öffentlichem Blob](#ingest)
* [Prüfungen der Datenqualität](#quality)
* [Datenuntersuchung](#explore)
* [Verknüpfen der Tabellen mit Fahrten und Trinkgeldern](#join)
* [Ziehen von Datenstichproben](#sample)
* [Ausführen von U-SQL-Aufträgen](#run)

Die U-SQL-Skripts werden hier beschrieben und in einer separaten Datei bereitgestellt. Sie können die vollständigen **U-SQL-Skripts** von [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough)herunterladen.

Zum Ausführen von U-SQL öffnen Sie Visual Studio, klicken auf **Datei--> Neu--> Projekt**, wählen **U-SQL-Projekt** aus, benennen das Projekt und speichern es in einem Ordner.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> U-SQL kann im Azure-Portal statt in Visual Studio ausgeführt werden. Sie können zur Azure Data Lake Analytics-Ressource im Portal navigieren und Abfragen direkt übermitteln, wie in der folgenden Abbildung dargestellt:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Datenerfassung: Einlesen von Daten aus einem öffentlichen Blob

Der Speicherort der Daten im Azure-Blob wird als **wasb://container\_name\@blob\_storage\_account\_name.blob.core.windows.net/blob_name** referenziert und kann mithilfe von **Extractors.Csv()** extrahiert werden. Geben Sie in den folgenden Skripts für „container\_name\@blob\_storage\_account\_name“ in der „wasb“-Adresse Ihren eigenen Container- und Speicherkontonamen ein. Da die Dateinamen das gleiche Format haben, kann **trip\_data\_\{\*\}.csv** verwendet werden, um alle 12 Fahrtendateien einzulesen.

```sql
///Read in Trip data
@trip0 =
    EXTRACT
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string
// This is reading 12 trip data from blob
FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
USING Extractors.Csv();
```

Da die erste Zeile Überschriften enthält, müssen Sie die Überschriften entfernen und Spaltentypen entsprechend anpassen. Sie können die verarbeiteten Daten entweder mithilfe von **swebhdfs://data_lake_storage_name.azuredatalakestorage.net/folder_name/file_name** in Azure Data Lake Storage oder **wasb://container_name\@blob_storage_account_name.blob.core.windows.net/blob_name** in einem Azure Blob Storage-Konto speichern.

```sql
// change data types
@trip =
    SELECT
    medallion,
    hack_license,
    vendor_id,
    rate_code,
    store_and_fwd_flag,
    DateTime.Parse(pickup_datetime) AS pickup_datetime,
    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
    Int32.Parse(passenger_count) AS passenger_count,
    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
    Double.Parse(trip_distance) AS trip_distance,
    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
FROM @trip0
WHERE medallion != "medallion";

////output data to ADL
OUTPUT @trip
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
USING Outputters.Csv();

////Output data to blob
OUTPUT @trip
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
USING Outputters.Csv();
```

Auf ähnliche Weise können Sie die Datasets mit den Fahrpreisen einlesen. Klicken Sie mit der rechten Maustaste auf „Azure Data Lake Storage“. Sie können auswählen, ob Sie die Daten über **Azure-Portal > Daten-Explorer** oder im **Datei-Explorer** in Visual Studio untersuchen möchten.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Prüfungen der Datenqualität
Nachdem die Tabellen mit Fahrten und Trinkgeldern eingelesen wurden, können Prüfungen der Datenqualität auf folgende Weise erfolgen. Die resultierenden CSV-Dateien können in Azure Blob Storage oder Azure Data Lake Storage ausgegeben werden.

Suchen Sie die Anzahl der Taxinummern („Medallions“) und eindeutige Anzahl von Taxinummern:

```sql
///check the number of medallions and unique number of medallions
@trip2 =
    SELECT
    medallion,
    vendor_id,
    pickup_datetime.Month AS pickup_month
    FROM @trip;

@ex_1 =
    SELECT
    pickup_month,
    COUNT(medallion) AS cnt_medallion,
    COUNT(DISTINCT(medallion)) AS unique_medallion
    FROM @trip2
    GROUP BY pickup_month;
    OUTPUT @ex_1
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
USING Outputters.Csv();
```

Suchen Sie die Taxinummern mit mehr als 100 Fahrten:

```sql
///find those medallions that had more than 100 trips
@ex_2 =
    SELECT medallion,
           COUNT(medallion) AS cnt_medallion
    FROM @trip2
    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
    GROUP BY medallion
    HAVING COUNT(medallion) > 100;
    OUTPUT @ex_2
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
USING Outputters.Csv();
```

Suchen Sie ungültige Datensätze mit Blick auf „pickup_longitude“:

```sql
///find those invalid records in terms of pickup_longitude
@ex_3 =
    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
    FROM @trip
    WHERE
    pickup_longitude <- 90 OR pickup_longitude > 90;
    OUTPUT @ex_3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
USING Outputters.Csv();
```

Suchen Sie fehlende Werte für einige Variablen:

```sql
//check missing values
@res =
    SELECT *,
           (medallion == null? 1 : 0) AS missing_medallion
    FROM @trip;

@trip_summary6 =
    SELECT
        vendor_id,
    SUM(missing_medallion) AS medallion_empty,
    COUNT(medallion) AS medallion_total,
    COUNT(DISTINCT(medallion)) AS medallion_total_unique
    FROM @res
    GROUP BY vendor_id;
OUTPUT @trip_summary6
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
USING Outputters.Csv();
```

### <a name="data-exploration"></a><a name="explore"></a>Datenuntersuchung
Untersuchen Sie mithilfe der folgenden Skripts die Daten, um sie besser zu verstehen.

Suchen Sie die Verteilung von Fahrten mit und ohne Trinkgeld:

```sql
///tipped vs. not tipped distribution
@tip_or_not =
    SELECT *,
           (tip_amount > 0 ? 1: 0) AS tipped
    FROM @fare;

@ex_4 =
    SELECT tipped,
           COUNT(*) AS tip_freq
    FROM @tip_or_not
    GROUP BY tipped;
    OUTPUT @ex_4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
USING Outputters.Csv();
```

Suchen Sie die Verteilung des Trinkgeldbetrags mithilfe der Bezugswerte: 0, 5, 10 und 20 USD.

```sql
//tip class/range distribution
@tip_class =
    SELECT *,
           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @fare;
@ex_5 =
    SELECT tip_class,
           COUNT(*) AS tip_freq
    FROM @tip_class
    GROUP BY tip_class;
    OUTPUT @ex_5
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
USING Outputters.Csv();
```

Suchen Sie grundlegende Statistiken zur Fahrtstrecke:

```sql
// find basic statistics for trip_distance
@trip_summary4 =
    SELECT
        vendor_id,
        COUNT(*) AS cnt_row,
        MIN(trip_distance) AS min_trip_distance,
        MAX(trip_distance) AS max_trip_distance,
        AVG(trip_distance) AS avg_trip_distance
    FROM @trip
    GROUP BY vendor_id;
OUTPUT @trip_summary4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
USING Outputters.Csv();
```

Suchen Sie die Quantile für die Fahrtstrecke:

```sql
// find percentiles of trip_distance
@trip_summary3 =
    SELECT DISTINCT vendor_id AS vendor,
                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
    FROM @trip;
   // group by vendor_id;
OUTPUT @trip_summary3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
USING Outputters.Csv();
```

### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Verknüpfen der Tabellen mit Fahrten und Trinkgeldern
Die Tabellen „trip“ und „fare“ können anhand von „medallion“, „hack_license“ und „pickup_time“ verbunden werden.

```sql
//join trip and fare table

@model_data_full =
SELECT t.*,
f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
(f.tip_amount > 0 ? 1: 0) AS tipped,
(f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
FROM @trip AS t JOIN  @fare AS f
ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

//// output to blob
OUTPUT @model_data_full
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
USING Outputters.Csv();

////output data to ADL
OUTPUT @model_data_full
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
USING Outputters.Csv();
```

Berechnen Sie für jede Ebene der Anzahl der Fahrgäste die Anzahl der Datensätze, das durchschnittliche Trinkgeld, die Varianz des Trinkgeldbetrags und den Prozentsatz von Fahrten mit Trinkgeld.

```sql
// contingency table
@trip_summary8 =
    SELECT passenger_count,
           COUNT(*) AS cnt,
           AVG(tip_amount) AS avg_tip_amount,
           VAR(tip_amount) AS var_tip_amount,
           SUM(tipped) AS cnt_tipped,
           (float)SUM(tipped)/COUNT(*) AS pct_tipped
    FROM @model_data_full
    GROUP BY passenger_count;
    OUTPUT @trip_summary8
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
USING Outputters.Csv();
```

### <a name="data-sampling"></a><a name="sample"></a>Ziehen von Datenstichproben
Wählen Sie zunächst nach dem Zufallsprinzip 0,1 % der Daten aus der verknüpften Tabelle aus:

```sql
//random select 1/1000 data for modeling purpose
@addrownumberres_randomsample =
SELECT *,
        ROW_NUMBER() OVER() AS rownum
FROM @model_data_full;

@model_data_random_sample_1_1000 =
SELECT *
FROM @addrownumberres_randomsample
WHERE rownum % 1000 == 0;

OUTPUT @model_data_random_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
USING Outputters.Csv();
```

Erstellen Sie dann anhand der binären Variablen „tip_class“ geschichtete Stichproben:

```sql
//stratified random select 1/1000 data for modeling purpose
@addrownumberres_stratifiedsample =
SELECT *,
        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
FROM @model_data_full;

@model_data_stratified_sample_1_1000 =
SELECT *
FROM @addrownumberres_stratifiedsample
WHERE rownum % 1000 == 0;
//// output to blob
OUTPUT @model_data_stratified_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
////output data to ADL
OUTPUT @model_data_stratified_sample_1_1000
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
```

### <a name="run-u-sql-jobs"></a><a name="run"></a>Ausführen von U-SQL-Aufträgen
Wenn Sie mit der Bearbeitung von U-SQL-Skripts fertig sind, können Sie sie mithilfe Ihres Azure Data Lake Analytics-Kontos an den Server übermitteln. Klicken Sie auf **Data Lake**, **Auftrag übermitteln**, wählen Sie Ihr **Analytics-Konto** und dann **Parallelität** aus, und klicken Sie auf die Schaltfläche **Übermitteln**.

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

Nachdem der Auftrag erfolgreich kompiliert wurde, wird der Status des Auftrags zur Überwachung in Visual Studio angezeigt. Nach Abschluss des Auftrags können Sie den Auftragsausführungsprozess wiederholen, um Schritte mit Engpässen zu bestimmen und die Effizienz des Auftrags zu steigern. Sie können auch im Azure-Portal den Status Ihrer U-SQL-Aufträge überprüfen.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Sie können nun die Ausgabedateien in Azure Blob Storage oder im Azure-Portal überprüfen. Verwenden Sie für die Modellierung im nächsten Schritt die geschichteten Stichprobendaten.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Erstellen und Bereitstellen von Modellen in Azure Machine Learning
Ihnen stehen zwei Optionen zur Verfügung, um Daten per Pull zum Erstellen und Bereitstellen von Modellen in Azure Machine Learning zu übertragen.

* Sie können zum einen die Stichprobendaten verwenden, die (im obigen Schritt **Ziehen von Datenstichproben** ) in ein Azure-Blob geschrieben wurden, und anschließend mithilfe von Python in Azure Machine Learning Modelle erstellen und bereitstellen.
* Zum anderen können Sie die Daten in Azure Data Lake direkt mithilfe einer Hive-Abfrage abfragen. Hierzu müssen Sie einen neuen HDInsight-Cluster erstellen oder einen vorhandenen HDInsight-Cluster verwenden, in dem die Hive-Tabellen auf die New Yorker Taxidaten im Azure Data Lake-Speicher verweisen.  Beide Optionen werden in den folgenden Abschnitten erläutert.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Option 1: Verwenden von Python zum Erstellen und Bereitstellen von Machine Learning-Modellen
Erstellen Sie zum Erstellen und Bereitstellen der Machine Learning-Modelle mit Python ein Jupyter Notebook auf Ihrem lokalen Computer oder in Azure Machine Learning Studio. Die Jupyter Notebook-Instanz enthält den auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) bereitgestellten vollständigen Code zum Untersuchen und Visualisieren von Daten sowie für Feature-Engineering, Modellierung und Bereitstellung. In diesem Artikel werden lediglich die Modellierung und die Bereitstellung behandelt.

### <a name="import-python-libraries"></a>Importieren von Python-Bibliotheken
Zum Ausführen des Jupyter Notebook-Beispiels oder der Python-Skriptdatei sind die folgenden Python-Pakete erforderlich. Wenn Sie den Azure Machine Learning-Notebookdienst verwenden, wurden diese Pakete bereits vorinstalliert.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
import sklearn
from sklearn.linear_model import LogisticRegression
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from __future__ import division
from sklearn import linear_model
from azureml import services
```

### <a name="read-in-the-data-from-blob"></a>Einlesen der Daten aus dem Blob
* Verbindungszeichenfolge

  ```text
  CONTAINERNAME = 'test1'
  STORAGEACCOUNTNAME = 'XXXXXXXXX'
  STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
  BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
  blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
  ```

* Einlesen als Text

  ```text
  t1 = time.time()
  data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
  t2 = time.time()
  print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  ```

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)

* Hinzufügen von Spaltennamen und Trennen von Spalten

  ```text
  colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
  'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
  df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
  ```

* Ändern des Datentyps einiger Spalten in „Numerisch“

  ```text
  cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
  ,'tipped','tip_class','rownum']
  for col in cols_2_float:
      df1[col] = df1[col].astype(float)
  ```

### <a name="build-machine-learning-models"></a>Erstellen von Machine Learning-Modellen
Sie erstellen nun ein binäres Klassifizierungsmodell zum Vorhersagen, ob für eine Fahrt ein Trinkgeld gezahlt wird oder nicht. Im Jupyter Notebook finden Sie zwei andere Modelle: ein Multiklassenklassifizierungs- und ein Regressionsmodell.

* Zuerst müssen Sie Pseudovariablen erstellen, die in „scikit-learn“-Modellen verwendet werden können.

  ```python
  df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
  df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
  ```

* Erstellen des Datenrahmens für die Modellierung

  ```python
  cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
  data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

  X = data.iloc[:,1:]
  Y = data.tipped
    ```

* Trainieren und Testen der 60-40-Aufteilung

  ```python
  X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
  ```

* Logistische Regression im Trainingssatz

  ```python
  model = LogisticRegression()
  logit_fit = model.fit(X_train, Y_train)
  print ('Coefficients: \n', logit_fit.coef_)
  Y_train_pred = logit_fit.predict(X_train)
  ```

    ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)

* Auswerten des Testdatasets

  ```python
  Y_test_pred = logit_fit.predict(X_test)
  ```

* Berechnen von Auswertungsmetriken

  ```python
  fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
  print fpr_train, tpr_train, thresholds_train

  fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
  print fpr_test, tpr_test, thresholds_test

  #AUC
  print metrics.auc(fpr_train,tpr_train)
  print metrics.auc(fpr_test,tpr_test)

  #Confusion Matrix
  print metrics.confusion_matrix(Y_train,Y_train_pred)
  print metrics.confusion_matrix(Y_test,Y_test_pred)
  ```

    ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Erstellen der Webdienst-API zur anschließenden Verwendung in Python
Nachdem das Machine Learning-Modell erstellt wurde, sollten Sie es in Betrieb nehmen. Hier wird das binäre logistische Modell als Beispiel verwendet. Vergewissern Sie sich, dass auf Ihrem lokalen Computer die Scikit-learn-Version 0.15.1 installiert ist. (Azure Machine Learning Studio hat bereits mindestens diese Version.)

* Suchen Sie in den Einstellungen von Azure Machine Learning Studio (klassisch) die Anmeldeinformationen für Ihren Arbeitsbereich. Klicken Sie in Azure Machine Learning Studio auf **Settings** --> **Name** --> **Authorization Tokens**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

  ```output
  workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  ```

* Erstellen eines Webdiensts

  ```python
  @services.publish(workspaceid, auth_token)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(int) #0, or 1
  def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
      return logit_fit.predict(inputArray)
  ```

* Abrufen von Anmeldeinformationen für den Webdienst

  ```python
  url = predictNYCTAXI.service.url
  api_key =  predictNYCTAXI.service.api_key

  print url
  print api_key

  @services.service(url, api_key)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(float)
  def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      pass
  ```

* Rufen Sie die Webdienst-API auf. Warten Sie nach dem vorherigen Schritt fünf bis zehn Sekunden.

  ```python
  NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  ```

    ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Option 2: Direktes Erstellen und Bereitstellen von Modellen in Azure Machine Learning
Azure Machine Learning Studio (Classic) kann Daten direkt aus Azure Data Lake Storage einlesen und dann zum Erstellen und Bereitstellen von Modellen verwendet werden. Bei diesem Ansatz verweist eine Hive-Tabelle auf Azure Data Lake Storage. Für die Hive-Tabelle muss ein separater Azure HDInsight-Cluster bereitgestellt werden. 

### <a name="create-an-hdinsight-linux-cluster"></a>Erstellen eines HDInsight Linux-Clusters
Erstellen Sie im [Azure-Portal](https://portal.azure.com) einen HDInsight-Cluster (Linux). Ausführliche Informationen finden Sie unter [Erstellen von HDInsight-Clustern mit Azure Data Lake Storage Gen1 mithilfe des Azure-Portals](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) im Abschnitt **Erstellen eines HDInsight-Clusters mit Zugriff auf Azure Data Lake Storage**.

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Erstellen einer Hive-Tabelle in HDInsight
Nun erstellen Sie mithilfe der Daten, die im vorherigen Schritt in Azure Data Lake Storage gespeichert wurden, Hive-Tabellen, die in Azure Machine Learning Studio (Classic) im HDInsight-Cluster verwendet werden sollen. Wechseln Sie zu dem erstellten HDInsight-Cluster. Klicken Sie auf **Einstellungen** --> **Eigenschaften** --> **Azure Active Directory-Identität für den Cluster** --> **ADLS-Zugriff**. Vergewissern Sie sich, dass das Azure Data Lake Storage-Konto der Liste mit Lese-, Schreib- und Ausführungsrechten hinzugefügt wurde.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Klicken Sie dann neben der Schaltfläche **Einstellungen** auf **Dashboard**, woraufhin ein Fenster geöffnet wird. Klicken Sie rechts oben auf der Seite auf **Hive-Ansicht**. Daraufhin sollte der **Abfrage-Editor** angezeigt werden.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Fügen Sie zum Erstellen einer Tabelle die folgenden Hive-Skripts ein. Auf den Speicherort der Datenquelle in Azure Data Lake Storage wird wie folgt verwiesen: **adl://data_lake_store_name.azuredatalakestore.net:443/folder_name/file_name**.

```hiveql
CREATE EXTERNAL TABLE nyc_stratified_sample
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string,
  payment_type string,
  fare_amount string,
  surcharge string,
  mta_tax string,
  tolls_amount string,
  total_amount string,
  tip_amount string,
  tipped string,
  tip_class string,
  rownum string
  )
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';
```

Nachdem die Abfrage ausgeführt wurde, sollten die Ergebnisse wie folgt angezeigt werden:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Erstellen und Bereitstellen von Modellen in Azure Machine Learning Studio
Sie können jetzt mit Azure Machine Learning ein Modell erstellen und bereitstellen, das vorhersagt, ob Trinkgeld gezahlt wird oder nicht. Die geschichteten Beispieldaten können für dieses binäre Klassifizierungsproblem (Trinkgeld oder nicht) verwendet werden. Die Vorhersagemodelle mit mehrklassiger Klassifizierung (tip_class) und Regression (tip_amount) können auch mit Azure Machine Learning Studio erstellt und bereitgestellt werden. Hier wird jedoch nur gezeigt, wie der Fall mithilfe des Modells für binäre Klassifizierung behandelt wird.

1. Übertragen Sie die Daten mithilfe des Moduls **Import Data** im Abschnitt **Data Input and Output** (Dateneingabe und -ausgabe) in Azure Machine Learning Studio (klassisch). Weitere Informationen finden Sie auf der Referenzseite zum [Import Data](/azure/machine-learning/studio-module-reference/import-data) -Modul.
2. Wählen Sie **Hive-Abfrage** als **Datenquelle** im Bereich **Eigenschaften** aus.
3. Fügen Sie das folgende Hive-Skript in den Editor **Hive-Datenbankabfrage** ein.

    ```hiveql
    select * from nyc_stratified_sample;
    ```

4. Geben Sie den URI des HDInsight-Clusters (den Sie im Azure-Portal finden), Hadoop-Anmeldeinformationen, den Speicherort der Ausgabedaten sowie den Namen/Schlüssel/Container des Azure Storage-Kontos ein.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Ein Beispiel eines binären Klassifizierungsexperiments zum Einlesen von Daten aus einer Hive-Tabelle finden Sie in der folgenden Abbildung:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

Nachdem das Experiment erstellt wurde, klicken Sie auf **Set Up Web Service** --> **Predictive Web Service**.

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Führen Sie das automatisch erstellte Bewertungsexperiment aus, sobald es abgeschlossen wurde, und klicken Sie auf **Deploy Web Service**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Das Dashboard des Webdiensts wird kurz darauf angezeigt:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Zusammenfassung
Mit Abschluss dieser exemplarischen Vorgehensweise haben Sie eine Data Science-Umgebung zum Erstellen skalierbarer End-to-End-Lösungen in Azure Data Lake eingerichtet. Diese Umgebung wurde verwendet, um ein großes öffentliches Dataset in den ordnungsgemäßen Schritten im Data Science-Prozess zu analysieren, von der Datenerfassung über das Training des Modells bis zur Bereitstellung des Modells als Webdienst. U-SQL wurde zum Verarbeiten, Untersuchen und Abfragen der Daten verwendet. Python und Hive wurden mit Azure Machine Learning Studio (klassisch) verwendet, um Vorhersagemodelle zu erstellen und bereitzustellen.

## <a name="whats-next"></a>Wie geht es weiter?
Der Lernpfad für den [Team Data Science-Prozess (TDSP)](./index.yml) enthält Links zu Themen, in denen jeder Schritt im erweiterten Analyseprozess beschrieben wird. Auf der Seite [Team Data Science-Prozess: Exemplarische Vorgehensweisen](walkthroughs.md) finden Sie eine Reihe exemplarischer Vorgehensweisen, die veranschaulichen, wie Ressourcen und Dienste in den verschiedenen Predictive Analytics-Szenarien genutzt werden:

* [Der Team Data Science-Prozess in Aktion: Verwenden von Azure Synapse Analytics](sqldw-walkthrough.md)
* [Der Team Data Science-Prozess in Aktion: Verwenden von HDInsight Hadoop-Clustern](hive-walkthrough.md)
* [Der Team Data Science-Prozess: Verwenden von SQL Server](sql-walkthrough.md)
* [Übersicht über Data Science mit Spark in Azure HDInsight](spark-overview.md)