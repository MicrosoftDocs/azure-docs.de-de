---
title: 'Data Science mit Spark in Azure HDInsight: Team Data Science-Prozess'
description: Das Spark MLlib-Toolkit bringt wesentliche Machine Learning-Modellierungsfunktionen in die verteilte HDInsight-Umgebung ein.
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
ms.openlocfilehash: 1dd82fb00c55e3676929999f204eae8755671038
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314744"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Übersicht über Data Science mit Spark in Azure HDInsight

Diese Sammlung von Themen zeigt, wie mit HDInsight Spark allgemeine Data Science-Aufgaben wie Datenerfassung, Featureentwicklung, Modellierung und Modellauswertung durchgeführt werden können. Die verwendeten Daten sind eine Stichprobe des Datasets für Taxifahrten und Fahrpreise in New York aus dem Jahr 2013. Die erstellten Modelle umfassen logistische und lineare Regression, Random Forests und Gradient-Boosted-Strukturen. In den Themen wird auch gezeigt, wie diese Modelle in Azure Blob Storage (WASB) gespeichert werden und wie ihre Vorhersageleistung bewertet und ausgewertet wird. Fortgeschrittenere Themen behandeln das Trainieren von Modellen mithilfe von Kreuzvalidierung und Hyper-Parameter-Sweeping. Dieses Übersichtsthema enthält außerdem Themen, in denen beschrieben wird, wie Sie den Spark-Cluster einrichten, den Sie zum Ausführen der Schritte in den bereitgestellten exemplarischen Vorgehensweisen benötigen.

## <a name="spark-and-mllib"></a>Spark und MLlib
[Spark](https://spark.apache.org/) ist ein Open-Source-Framework für die Parallelverarbeitung, das die In-Memory-Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern. Die Spark-Verarbeitungs-Engine ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner verteilten In-Memory-Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen. [MLlib](https://spark.apache.org/mllib/) ist die skalierbare Bibliothek für maschinelles Lernen von Spark, die algorithmische Modellierungsfunktionen in diese verteilte Umgebung bereitstellt.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) ist das in Azure gehostete Angebot für Open-Source-Spark. Darüber hinaus unterstützt der Spark-Cluster **Jupyter-PySpark-Notebooks**. Außerdem kann er interaktive Spark-SQL-Abfragen zum Transformieren, Filtern und Visualisieren von in Azure-Blobs (WASB) gespeicherten Daten ausführen. PySpark ist die Python-API für Spark. Die Codeausschnitte, die die Lösungen bereitstellen und die relevanten Plots zum Visualisieren der Daten zeigen, werden in Jupyter-Notebooks in den Spark-Clustern ausgeführt. Die Modellierungsschritte in diesen Themen enthalten auch Code zum Trainieren, Evaluieren, Speichern und Nutzen jedes Modelltyps.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Einrichtung: Spark-Cluster und Jupyter-Notebooks
Die Einrichtungsschritte und der Code in dieser exemplarischen Vorgehensweise beziehen sich auf HDInsight Spark 1.6. Jupyter-Notebooks werden jedoch für HDInsight Spark 1.6- und Spark 2.0-Cluster bereitgestellt. Eine Beschreibung der Notebooks und Links zu diesen finden Sie in der Datei [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) zu dem GitHub-Repository, das sie enthält. Der hier und in den verknüpften Notebooks zu findende Code ist darüber hinaus generisch und sollte in allen Spark-Clustern funktionieren. Wenn Sie HDInsight Spark nicht verwenden, weichen Clustereinrichtung und Verwaltungsschritte möglicherweise geringfügig von dem ab, was hier gezeigt wird. Der Einfachheit halber finden Sie hier die Links zu den Jupyter-Notebooks für Spark 1.6 (auszuführen im PySpark-Kernel des Jupyter-Notebookservers) und Spark 2.0 (auszuführen im PySpark3-Kernel des Jupyter-Notebookservers):

### <a name="spark-16-notebooks"></a>Spark 1.6-Notebooks
Diese Notebooks werden im PySpark-Kernel des Jupyter-Notebookservers ausgeführt.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Bietet Informationen zum Durchführen von Datenuntersuchungen, Modellieren und Bewerten mit mehreren verschiedenen Algorithmen.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Enthält Themen im Notebook 1 und Modellentwicklung mit Hyperparameteroptimierung und Kreuzvalidierung.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Zeigt, wie ein gespeichertes Modell mithilfe von Python in HDInsight-Clustern operationalisiert wird.

### <a name="spark-20-notebooks"></a>Spark 2.0-Notebooks
Diese Notebooks werden im PySpark3-Kernel des Jupyter-Notebookservers ausgeführt.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Diese Datei enthält Informationen zum Durchsuchen, Modellieren und Bewerten von Daten in Spark 2.0-Clustern anhand des [hier](#the-nyc-2013-taxi-data) beschriebenen „NYC Taxi Trips“ und des Fahrpreisdatasets. Dieses Notebook ist möglicherweise ein guter Ausgangspunkt zum schnellen Untersuchen des Codes, den wir für Spark 2.0 bereitgestellt haben. Ein ausführlicheres Notebook zur Analyse der NYC-Taxidaten finden Sie im nächsten Notebook in dieser Liste. Informationen finden Sie in den Hinweisen im Anschluss an diese Liste, in der diese Notebooks verglichen werden.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Diese Datei zeigt, wie Datenanalysen (Spark SQL und Dataframevorgänge), Suchvorgänge, Modellierungen und Bewertungen mithilfe des [hier](#the-nyc-2013-taxi-data) beschriebenen Datasets zu Taxifahrten und Fahrpreisen in New York durchgeführt werden.
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Diese Datei zeigt, wie Datenanalysen (Spark SQL und Dataframevorgänge), Suchvorgänge, Modellierungen und Bewertungen mithilfe des bekannten Fluglinien-Datasets zu pünktlichen Abflügen aus dem Jahr 2011 und 2012 durchgeführt werden. Wir kombinieren das Fluglinien-Dataset vor der Modellierung mit den Flughafen-Wetterdaten (z. B. Windgeschwindigkeit, Temperatur, Höhe usw.), damit diese Wetterdaten in das Modell aufgenommen werden können.

<!-- -->

> [!NOTE]
> Das Fluglinien-Dataset wurde den Spark 2.0-Notebooks hinzugefügt, um die Verwendung von Klassifizierungsalgorithmen besser zu veranschaulichen. Unter den folgenden Links finden Sie Informationen zum Dataset zur Pünktlichkeit von Flugreisestarts und zum Wetterdataset:
> 
> - Daten zur Pünktlichkeit von Fluggesellschaften: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Flughafen-Wetterdaten: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Die Ausführung der Spark 2.0-Notebooks zu den Datasets mit den NYC-Taxidaten und den Flugverspätungen bei Fluggesellschaften können 10 Minuten oder länger dauern (je nach Größe Ihres HDI-Clusters). Das erste Notebook in der Liste oben zeigt viele Aspekte der Datenuntersuchung, Visualisierung und des ML-Modelltrainings in einem Notebook, das weniger Zeit für die Ausführung durch einen heruntergerechneten NYC-Datensatz benötigt, in dem die Taxi- und Fahrpreisdateien bereits zusammengeführt wurden: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Dieses Notebook benötigt weitaus weniger Zeit bis zum Abschluss (2-3 Minuten) ist möglicherweise ein guter Ausgangspunkt zum schnellen Untersuchen des Codes, den wir für Spark 2.0 bereitgestellt haben.

<!-- -->

Anleitungen zur Operationalisierung eines Spark 2.0-Modells und der Modellnutzung für die Bewertung finden Sie in der [Spark 1.6-Dokumentation zum Verbrauch](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb). Diese enthält ein Beispiel, in dem die erforderlichen Schritte aufgeführt werden. Um dieses Beispiel in Spark 2.0 verwenden zu können, ersetzen Sie die Python-Codedatei durch [diese Datei](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Voraussetzungen

Die folgenden Vorgehensweisen beziehen sich auf Spark 1.6. Verwenden Sie für die Version 2.0 von Spark die zuvor beschriebenen und verlinkten Notebooks.

1. Sie benötigen ein Azure-Abonnement. Wenn Sie noch keins besitzen, lesen Sie den Artikel [How to get Azure Free trial for testing Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)(Gewusst wie: Erhalten einer Azure-Testversion zum Testen von Hadoop in HDInsight).

2. Sie benötigen zum Durchführen dieser exemplarischen Vorgehensweise einen Spark 1.6-Cluster. Anweisungen zum Erstellen eines solchen Clusters finden Sie unter [Erste Schritte: Erstellen von Apache Spark in Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Clustertyp und Version werden im Menü **Clustertyp auswählen** angegeben.

![Konfigurieren des Clusters](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Ein Thema, das die Verwendung von Scala statt Python zur Ausführung von Aufgaben für einen End-to-End-Data Science-Process veranschaulicht, ist [Data Science unter Verwendung von Scala und Spark in Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Die NYC-2013-Taxidaten
Die NYC Taxi Trips-Daten umfassen ca. 20 GB komprimierter CSV-Dateien (ca. 48 GB unkomprimiert) mit mehr als 173 Mio. einzelnen Fahrten mit den zugehörigen Preisen. Jeder Fahrtendatensatz enthält den Start- und Zielort mit der Uhrzeit, die anonymisierte Lizenznummer des Fahrers (Hack) und die eindeutige ID des Taxis (Medallion). Die Daten umfassen alle Fahrten im Jahr 2013. Sie werden für jeden Monat in den folgenden beiden Datasets bereitgestellt:

1. Die CSV-Dateien des Typs „trip_data“ enthalten Fahrtendetails, z. B. die Anzahl der Fahrgäste, Start- und Zielort, Fahrtdauer und Fahrtlänge. Es folgen einige Beispieleinträge:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Die CSV-Dateien "trip_fare" enthalten Details zu den Kosten für jede Fahrt, beispielsweise Zahlungsart, Fahrpreis, Zuschläge und Steuern, Trinkgelder und Mautgebühren sowie den entrichteten Gesamtbetrag. Es folgen einige Beispieleinträge:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Wir haben eine Stichprobe von 0,1 % dieser Dateien entnommen und diese CSV-Dateien der Typen „trip\_data“ und „trip\_fare“ in einem einzelnen Dataset verknüpft, das für diese exemplarische Vorgehensweise als Eingabedataset verwendet wird. Der eindeutige Schlüssel für die Zusammenführung von „trip\_data“ und „trip\_fare“ besteht aus den Feldern: „medallion“, „hack\_licence“ und „pickup\_datetime“. Jeder Datensatz des Datasets enthält die folgenden Attribute, die eine NYC-Taxifahrt darstellen:

| Feld | Kurzbeschreibung |
| --- | --- |
| medallion |Anonymisierte Taxi-Medallion (eindeutige Taxi-ID) |
| hack_license |Anonymisierte Hackney Carriage-Lizenznummer |
| vendor_id |Taxiunternehmer-ID |
| rate_code |NYC-Taxitarif |
| store_and_fwd_flag |Speicherungs- und Weiterleitungsflag |
| pickup_datetime |Datum und Uhrzeit der Aufnahme |
| dropoff_datetime |Datum und Uhrzeit des Fahrtgastabsetzens |
| pickup_hour |Stunde der Aufnahme |
| pickup_week |Kalenderwoche der Aufnahme |
| weekday |Wochentag (Bereich 1 bis 7) |
| passenger_count |Anzahl der Fahrgäste bei einer Taxifahrt |
| trip_time_in_secs |Fahrtzeit in Sekunden |
| trip_distance |Zurückgelegte Fahrstrecke in Meilen |
| pickup_longitude |Längengrad der Aufnahme |
| pickup_latitude |Breitengrad der Aufnahme |
| dropoff_longitude |Längengrad des Absetzens |
| dropoff_latitude |Breitengrad des Absetzens |
| direct_distance |Direkte Entfernung zwischen Start- und Zielort |
| payment_type |Zahlungsart (bar, Kreditkarte usw.) |
| fare_amount |Fahrpreis in |
| surcharge |Zuschlag |
| mta_tax |MTA Metro-Transportsteuer |
| tip_amount |Trinkgeldbetrag |
| tolls_amount |Mautgebührenbetrag |
| total_amount |Gesamtbetrag |
| tipped |Trinkgeld gezahlt (0/1 für „Nein“ oder „Ja“) |
| tip_class |Trinkgeldklasse (0: 0 $, 1: 0-5 $, 2: 6 bis 10 $, 3: 11 bis 20 $, 4: > 20$) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Ausführen von Code über ein Jupyter-Notebook auf dem Spark-Cluster
Sie können das Jupyter-Notebook über das Azure-Portal starten. Suchen Sie Ihren Spark-Cluster auf dem Dashboard, und klicken Sie darauf, um zur Verwaltungsseite für Ihren Cluster zu gelangen. Klicken Sie dann auf **Cluster-Dashboards** -> **Jupyter-Notebook** , um das dem Spark-Cluster zugeordnete Notebook zu öffnen.

![Cluster-Dashboards](./media/spark-overview/spark-jupyter-on-portal.png)

Für den Zugriff auf die Jupyter-Notebooks können Sie auch zu * *_`https://CLUSTERNAME.azurehdinsight.net/jupyter`_* _ navigieren. Ersetzen Sie das Element CLUSTERNAME in dieser URL durch den Namen Ihres eigenen Clusters. Sie benötigen das Kennwort für Ihr Administratorkonto, um auf die Notebooks zuzugreifen.

![Durchsuchen von Jupyter-Notebooks](./media/spark-overview/spark-jupyter-notebook.png)

Wählen Sie „PySpark“ aus, um ein Verzeichnis mit ein paar Beispielen für vorkonfigurierte Notebooks anzuzeigen, die die PySpark-API verwenden. Die Notebooks, die die Codebeispiele für diese Sammlung von Spark-Themen enthalten, sind auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) verfügbar.

Sie können die Notebooks direkt von [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) auf den Jupyter-Notebookserver in Ihrem Spark-Cluster hochladen. Klicken Sie auf der Startseite Ihres Jupyter im rechten Teil des Bildschirms auf die Schaltfläche _ *Hochladen* *. Ein Datei-Explorer wird geöffnet. Hier können Sie die GitHub-URL (Rohdateninhalt) des Notebooks einfügen und auf **Öffnen** klicken.

Sie sehen den Dateinamen in der Jupyter-Dateiliste, wieder mit einer Schaltfläche **Hochladen**. Klicken Sie auf diese Schaltfläche **Hochladen** . Sie haben nun das Notebook importiert. Wiederholen Sie diese Schritte, um die anderen Notebooks dieser exemplarischen Vorgehensweise hochzuladen.

> [!TIP]
> Sie können in Ihrem Browser mit der rechten Maustaste auf die Links klicken und **Link kopieren** auswählen, um die GitHub-URL für unformatierten Inhalt abzurufen. Sie können diese URL in das Dialogfeld „Hochladen“ im Datei-Explorer von Jupyter einfügen.
> 
> 

Sie können jetzt:

* Den Code anzeigen, indem Sie auf das Notebook klicken.
* Jede Zelle durch Drücken von **UMSCHALT+EINGABETASTE** ausführen.
* Das gesamte Notebook ausführen, indem Sie auf **Cell** -> **Run** klicken.
* Die automatische Visualisierung von Abfragen verwenden.

> [!TIP]
> Der PySpark-Kernel visualisiert automatisch die Ausgabe der SQL-Abfragen (HiveQL). Sie haben die Möglichkeit, verschiedene Arten von Visualisierungen auszuwählen (Tabelle, Kreis, Linie, Fläche oder Balken), indem Sie im Notebook die Menüschaltflächen unter **Typ** verwenden:
>
>

![ROC-Kurve der logistischen Regression für die allgemeine Methode](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Wie geht es weiter?
Nachdem Sie einen HDInsight Spark-Cluster eingerichtet und die Jupyter-Notebooks hochgeladen haben, können Sie nun die Themen für diese drei PySpark-Notebooks durcharbeiten. Darin erfahren Sie, wie Sie Ihre Daten durchsuchen sowie Modelle erstellen und nutzen. Das Notebook zum erweiterten Durchsuchen von Daten und Modellieren zeigt, wie Sie die Kreuzvalidierung, das Hyper-Parameter-Sweeping und die Auswertung von Modellen einbeziehen können.

**Datenuntersuchung und -modellierung mit Spark:** Untersuchen Sie das Dataset, und erstellen und bewerten Sie die Machine Learning-Modelle. Arbeiten Sie dazu das Thema [Erstellen von binären Klassifizierungs- und Regressionsmodellen für Daten mit dem Spark MLib-Toolkit](spark-data-exploration-modeling.md) durch.

**Nutzung von Modellen:** Informationen zum Bewerten der in diesem Thema erstellten Klassifizierungs- und Regressionsmodelle finden Sie unter [Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md).

**Kreuzvalidierung und Hyperparameter-Sweeping** : Unter [Erweiterte Datenuntersuchung und Modellierung mit Spark](spark-advanced-data-exploration-modeling.md) erfahren Sie, wie Modelle mit Kreuzvalidierung und Hyperparameter-Sweeping trainiert werden können.