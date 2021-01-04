---
title: 'Untersuchen und Modellieren von Daten mit Spark: Team Data Science-Prozess'
description: Veranschaulicht die Funktionen zum Durchsuchen und Modellieren von Daten des Spark MLlib-Toolkits in HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 1ef6c678110037a4aed24ef40631b73a04ca6a9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028086"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Durchsuchen von Daten und Modellierung mit Spark

Hier erfahren Sie, wie Sie HDInsight Spark zum Trainieren von Machine Learning-Modellen für die Vorhersage von Taxifahrtkosten unter Verwendung von Spark MLlib verwenden.

Dieses Beispiel veranschaulicht die verschiedenen Schritte im [Team Data Science-Prozess](./index.yml). Zum Laden, Untersuchen und Vorbereiten von Daten wird eine Teilmenge des Datasets der NYC-Taxifahrten und Fahrpreise von 2013 verwendet. Anschließend werden mithilfe von Spark MLlib binäre Klassifizierungs- und Regressionsmodelle trainiert, um vorherzusagen, ob ein Trinkgeld für die Fahrt bezahlt wird, und um den Trinkgeldbetrag zu schätzen.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Konto und einen Spark 1.6- (oder Spark 2.0-) HDInsight-Cluster zum Durchführen dieser exemplarischen Vorgehensweise. Anweisungen zum Erfüllen dieser Anforderungen finden Sie im Thema [Übersicht über Data Science mit Spark in Azure HDInsight](spark-overview.md). Dieses Thema enthält auch eine Beschreibung der hier verwendeten NYC 2013 Taxi-Daten und eine Anleitung zum Ausführen von Code aus einem Jupyter Notebook im Spark-Cluster. 

### <a name="spark-clusters-and-notebooks"></a>Spark-Cluster und Notebooks

Die Einrichtungsschritte und der Code in dieser exemplarischen Vorgehensweise beziehen sich auf HDInsight Spark 1.6. Jupyter-Notebooks werden jedoch für HDInsight Spark 1.6- und Spark 2.0-Cluster bereitgestellt. Eine Beschreibung der Notebooks und Links zu diesen finden Sie in der Datei [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) zu dem GitHub-Repository, das sie enthält. Der hier und in den verknüpften Notebooks zu findende Code ist darüber hinaus generisch und sollte in allen Spark-Clustern funktionieren. Wenn Sie HDInsight Spark nicht verwenden, weichen Clustereinrichtung und Verwaltungsschritte möglicherweise geringfügig von dem ab, was hier gezeigt wird. Der Einfachheit halber finden Sie hier die Links zu den Jupyter-Notebooks für Spark 1.6 (auszuführen im PySpark-Kernel des Jupyter-Notebookservers) und Spark 2.0 (auszuführen im PySpark3-Kernel des Jupyter-Notebookservers):

- [Spark 1.6-Notebooks:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/) Bietet Informationen zum Durchführen von Datenuntersuchungen, Modellieren und Bewerten mit mehreren verschiedenen Algorithmen.
- [Spark 2.0-Notebooks:](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/) Enthält Informationen zum Durchführen von Regressions- und Klassifizierungsaufgaben. Datasets können variieren, die Schritte und Konzepte sind jedoch auf verschiedene Datasets anwendbar.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> Die Beschreibungen unten beziehen sich auf die Verwendung von Spark 1.6. Verwenden Sie für Spark 2.0-Versionen die oben beschriebenen und verlinkten Notebooks. 

## <a name="setup"></a>Einrichten

Spark kann aus dem Azure Storage-Blob (auch bekannt als WASB) lesen und darin schreiben. Es können also alle Ihre vorhandenen Daten mithilfe von Spark verarbeitet und die Ergebnisse wieder im WASB gespeichert werden.

Um Modelle oder Dateien im WASB zu speichern, muss der Pfad korrekt angegeben werden. Auf den an den Spark-Cluster angefügten Standardcontainer können Sie mit einem Pfad verweisen, der mit „wasb:///“ beginnt. Auf andere Speicherorte wird mit „wasb://“ verwiesen.

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Festlegen von Verzeichnispfaden für Speicherorte im WASB

Das folgende Codebeispiel gibt den Speicherort der zu lesenden Daten und den Pfad für das Modellspeicherverzeichnis an, in dem die Ausgabe des Modells gespeichert wird:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Importieren von Bibliotheken

Zum Setup gehört auch das Importieren erforderlicher Bibliotheken. Legen Sie den Spark-Kontext fest, und importieren Sie die erforderlichen Bibliotheken mit dem folgenden Code.

```python
# IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Vorab festgelegter Spark-Kontext und PySpark-Magics

Der Kontext der mit Jupyter-Notebooks bereitgestellten PySpark-Kernel ist voreingestellt. Sie müssen nicht ausdrücklich den Spark- oder Hive-Kontext festlegen, um mit der Anwendung, die Sie entwickeln, arbeiten zu können. Diese Kontexte sind standardmäßig verfügbar. Diese Kontexte sind:

* sc – für Spark 
* sqlContext – für Hive

Der PySpark-Kernel bietet einige so genannte „Magic-Befehle“, die vordefiniert sind. Dies sind spezielle Befehle, die Sie mit %% aufrufen können. Es gibt zwei Befehle dieser Art, die in den Codebeispielen verwendet werden.

* **%%local** gibt an, dass der Code in den nachfolgenden Zeilen lokal ausgeführt wird. Der Code muss gültiger Python-Code sein.
* **%%sql -o \<variable name>** wendet eine Hive-Abfrage auf „sqlContext“ an. Wenn der Parameter „-o“ übergeben wird, wird das Ergebnis der Abfrage im Python-Kontext „%%local“ als Pandas-Dataframe beibehalten.

Weitere Informationen zu Jupyter Notebook-Kernels und den vordefinierten „Magics“ finden Sie unter [Kernel für Jupyter Notebook in Apache Spark-Clustern in Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="load-the-data"></a>Laden der Daten

Der erste Schritt im Data Science-Prozess ist das Erfassen der zu analysierenden Daten aus Quellen in der Umgebung, die Sie zum Durchsuchen und Modellieren von Daten nutzen. In dieser exemplarischen Vorgehensweise ist Spark diese Umgebung. Dieser Abschnitt enthält den Code zur Ausführung einer Reihe von Aufgaben:

* Erfassen des zu modellierenden Datenbeispiels
* Einlesen des Eingabedatasets (gespeichert als TSV-Datei)
* Formatieren und Bereinigen der Daten
* Erstellen und Zwischenspeichern von Objekten (RDDs oder Datenrahmen) im Arbeitsspeicher
* deren Registrierung als temporäre Tabelle im SQL-Kontext.

Hier ist der Code für die Datenerfassung.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 51,72 Sekunden

## <a name="explore-the-data"></a>Untersuchen der Daten

Nachdem die Daten in Spark eingegeben wurden, besteht der nächste Schritt im Data Science-Prozess darin, durch Durchsuchen und Visualisieren eine tiefer gehende Einsicht in die Daten zu erhalten. In diesem Abschnitt untersuchen wir die Taxidaten mit SQL-Abfragen und zeichnen Diagramme der Zielvariablen und künftigen Merkmale zur Sichtprüfung. Insbesondere stellen wir die Häufigkeit der Fahrgastzahlen bei Taxifahrten, die Häufigkeit der Trinkgeldbeträge und die Variation der Trinkgelder nach Zahlungsbetrag und -art im Diagramm dar.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Zeichnen eines Histogramms der Häufigkeit der Fahrgastzahlen im Taxifahrtenbeispiel

In diesem Code und den nachfolgenden Codeausschnitten werden SQL-Magic-Befehle verwendet, um das Beispiel abzufragen, und lokale Magic-Befehle, um die Daten in einem Diagramm darzustellen.

* **SQL magic (`%%sql`)** Der HDInsight PySpark-Kernel unterstützt einfache HiveQL-Inlineabfragen für „sqlContext“. Mit dem Argument (-o VARIABLE_NAME) wird die Ausgabe der SQL-Abfrage als Pandas-Dataframe auf dem Jupyter-Server beibehalten. Mit dieser Einstellung wird die Ausgabe im lokalen Modus verfügbar gemacht.
* Der Befehl **`%%local` magic** wird genutzt, um Code lokal auf dem Jupyter-Server auszuführen. Dies ist der Hauptknoten des HDInsight-Clusters. Normalerweise verwenden Sie den Magic-Befehl `%%local` zusammen mit dem Magic-Befehl `%%sql` mit dem Parameter -o. Mit dem Parameter -o wird die Ausgabe der SQL-Abfrage lokal beibehalten. Anschließend löst der Magic-Befehl %%local die nächste Gruppe von Codeausschnitten aus, damit diese lokal für die Ausgabe der lokal gespeicherten SQL-Abfragen ausgeführt werden können.

Die Ausgabe wird automatisch visualisiert, nachdem Sie den Code ausgeführt haben.

Mit dieser Abfrage werden die Fahrten nach der Fahrgastanzahl abgerufen. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Mit dem Code wird aus der Ausgabe der Abfrage ein lokaler Dataframe erstellt, und die Daten werden in einem Diagramm dargestellt („geplottet“). Der Magic-Befehl `%%local` erstellt einen lokalen Dataframe `sqlResults`, der für das Plotten mit matplotlib verwendet werden kann. 

> [!NOTE]
> Dieser PySpark-Magic-Befehl wird in dieser exemplarischen Vorgehensweise mehrfach genutzt. Bei einer großen Datenmenge sollten Sie Stichproben verwenden, um einen Dataframe zu erstellen, der nicht zu groß für den lokalen Arbeitsspeicher ist.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Hier ist der Code zum Plotten der Fahrten nach der Fahrgastanzahl angegeben:

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**AUSGABE:**

![Fahrtenhäufigkeit nach Anzahl der Fahrgäste](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Sie können zwischen verschiedenen Arten von Visualisierungen auswählen (Tabelle, Kreis, Linie, Fläche oder Balken), indem Sie im Notebook die Menüschaltflächen unter **Typ** verwenden. Hier ist das Balkendiagramm dargestellt.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Zeichnen eines Histogramms des Trinkgeldbetrags und der Variation des Trinkgeldbetrags nach Anzahl der Fahrgäste und Höhe des Fahrpreises

Verwenden Sie eine SQL-Abfrage, um Beispieldaten zu erstellen.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

In dieser Codezelle wird die SQL-Abfrage verwendet, um drei Diagramme der Daten zu erstellen.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**AUSGABE:** 

![Trinkgeldbetragverteilung](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Trinkgeldbetrag nach Anzahl der Fahrgäste](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Trinkgeldbetrag nach Höhe des Fahrpreises](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>Vorbereiten der Daten

Dieser Abschnitt enthält den Code für die Prozeduren zum Vorbereiten von Daten für die Verwendung in der ML-Modellierung und deren Beschreibung. Hier erfahren Sie, wie Sie die folgenden Aufgaben ausführen:

* Erstellen eines neuen Features durch Diskretisieren von Stunden in Verkehrszeitbuckets
* Indizieren und Codieren von kategorischen Features
* Erstellen von bezeichneten Punktobjekten für die Eingabe in ML-Funktionen
* Erstellen einer Zufallsunterauswahl der Daten und Aufteilen in Trainings- und Testsätze
* Featureskalierung
* Zwischenspeichern von Objekten im Arbeitsspeicher

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Erstellen eines neuen Features durch Diskretisieren von Stunden in Verkehrszeitbuckets

Dieser Code zeigt, wie Sie ein neues Feature durch Diskretisieren von Stunden in Verkehrszeitbuckets erstellen und dann den resultierenden Datenrahmen im Arbeitsspeicher zwischenspeichern. Wo robuste verteilte Datasets (Resilient Distributed Datasets, RDDs) und Datenrahmen wiederholt verwendet werden, führt das Zwischenspeichern zu verbesserten Ausführungszeiten. Folglich werden wir RDDs und Datenrahmen in mehreren Phasen der exemplarischen Vorgehensweise zwischenspeichern. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**AUSGABE:**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indizieren und Codieren von kategorischen Features für die Eingabe in Modellierungsfunktionen

Dieser Abschnitt beschreibt das Indizieren oder Codieren von kategorischen Features für die Eingabe in die Modellierungsfunktionen. Die Modellierungs- und Vorhersagefunktionen von MLlib erfordern, dass Features mit kategorischen Eingabedaten vor der Verwendung indiziert oder codiert werden. Je nach Modell müssen Sie auf unterschiedliche Weise indizieren oder codieren:  

* **Strukturbasierte Modellierung** erfordert, dass Kategorien als numerische Werte codiert werden (z.B. kann ein Feature mit drei Kategorien möglicherweise mit 0, 1 und 2 codiert werden). Dieser Algorithmus wird durch die MLlib-Funktion [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) bereitgestellt. Diese Funktion codiert eine Zeichenfolgenspalte von Bezeichnungen in eine Spalte mit der Bezeichnungsindizes, die nach der Häufigkeit der Bezeichnungen angeordnet sind. Obwohl sie mit numerischen Werten für die Verarbeitung von Eingabe und Daten indiziert sind, können strukturbasierte Algorithmen so angegeben werden, dass sie entsprechend als Kategorien behandelt werden. 
* **Logistische und lineare Regressionsmodelle** erfordern One-Hot-Codierung, wobei z.B. ein Feature mit drei Kategorien auf drei Featurespalten erweitert werden kann, wobei jede abhängig von der Kategorie einer Beobachtung 0 oder 1 enthalten kann. MLlib bietet die Funktion [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) zur One-Hot-Codierung. Dieser Encoder ordnet eine Spalte mit Bezeichnungsindizes einer Spalte mit binären Vektoren mit höchstens einem einzigen Wert zu. Diese Codierung ermöglicht die Anwendung von Algorithmen, die Nummernwertfeatures erwarten, z.B. logistische Regression, auf kategorische Features.

Hier ist der Code zum Indizieren und Codieren von kategorischen Features:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 1,28 Sekunden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Erstellen von bezeichneten Punktobjekten für die Eingabe in ML-Funktionen

Dieser Abschnitt enthält Code, der zeigt, wie Sie kategorische Textdaten als bezeichneten Punktdatentyp und so codieren, dass sie zum Trainieren und Testen logistischer Regression gemäß MLlib und anderer Klassifizierungsmodelle verwendet werden können. Bezeichnete Punktobjekte sind robuste verteilte Datasets (RDD), die in einer Weise formatiert sind, die von den meisten ML-Algorithmen in MLlib als Eingabedaten benötigt wird. Ein [bezeichneter Punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) ist ein lokaler Vektor, entweder dicht oder platzsparend, der mit einer Bezeichnung/Antwort verknüpft ist.  

Dieser Abschnitt enthält Code, der zeigt, wie Sie kategorische Textdaten als [bezeichneten Punktdatentyp](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) und so codieren, dass sie zum Trainieren und Testen logistischer Regression gemäß MLlib und anderer Klassifizierungsmodelle verwendet werden können. Bezeichnete Punktobjekte sind robuste verteilte Datasets (RDD), die aus Bezeichnung (Ziel/Antwortvariable) und Featurevektor bestehen. Dieses Format wird von vielen ML-Algorithmen in MLlib als Eingabe benötigt.

Hier ist der Code angegeben, mit dem Textfeatures für die binäre Klassifizierung indiziert und codiert werden.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Hier ist der Code angegeben, mit dem kategorische Textfeatures für die lineare Regressionsanalyse codiert und indiziert werden.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Erstellen einer Zufallsunterauswahl der Daten und Aufteilen in Trainings- und Testsätze

Dieser Code erstellt eine zufällige Stichprobe der Daten (25 % werden hier verwendet). Obwohl es aufgrund der Größe des Datasets in diesem Beispiel nicht erforderlich ist, zeigen wir Ihnen, wie Sie hier Stichproben erfassen können, damit Sie dieses Verfahren bei Bedarf zur Lösung eigener Probleme verwenden können. Bei großen Stichproben kann die Stichprobenentnahme das Trainieren von Modellen erheblich beschleunigen. Als Nächstes teilen wir die Stichprobe in einen Trainingsteil (hier 75 %) und einen Testteil (hier 25 %) zur Klassifizierung und Regressionsmodellierung.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 0,24 Sekunden

### <a name="feature-scaling"></a>Featureskalierung

Featureskalierung, auch bekannt als Datennormalisierung, stellt sicher, dass Features mit weit verteilten Werten keine übermäßige Gewichtung in der Zielfunktion erhalten. Der Code für die Featureskalierung verwendet [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) zum Skalieren der Features auf Einheitenvarianz. Er wird von MLlib für die Verwendung bei der linearen Regression mit dem stochastischen Gradientenverfahren (SGD), einem beliebten Algorithmus für das Training einer Vielzahl anderer Machine Learning-Modelle, z. B. normalisierter Regressionen oder Support Vector Machines (SVM), bereitgestellt.

> [!NOTE]
> Wir haben festgestellt, dass der LinearRegressionWithSGD-Algorithmus für die Featureskalierung zu empfindlich ist.

Im Folgenden finden Sie den Code zum Skalieren von Variablen für die Verwendung mit dem normalisierten linearen SGD-Algorithmus.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 13,17 Sekunden

### <a name="cache-objects-in-memory"></a>Zwischenspeichern von Objekten im Arbeitsspeicher

Die Zeit zum Trainieren und Testen von ML-Algorithmen kann durch Zwischenspeichern der Eingabedatenrahmen-Objekte für Klassifizierung, Regression und skalierte Features verwendet werden.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:** 

Für die Ausführung der obigen Zelle benötigte Zeit: 0,15 Sekunden

## <a name="train-a-binary-classification-model"></a>Trainieren eines binären Klassifikationsmodells

Dieser Abschnitt beschreibt die Verwendung von drei Modellen für die Aufgabe der binären Klassifizierung der Vorhersage, ob für eine Taxifahrt ein Trinkgeld bezahlt wird. Die präsentierten Modelle sind:

* Normalisierte logistische Regression 
* Random Forest-Modell
* Gradient-Boosted-Strukturen

Jeder Codeabschnitt zur Modellerstellung ist in Schritte unterteilt: 

1. **Modelltrainingsdaten** mit einem Parametersatz
2. **Modellevaluierung** an einem Testdataset mit Metriken
3. **Speichern des Modells** im Blob für die zukünftige Nutzung

### <a name="classification-using-logistic-regression"></a>Klassifizierung mittels logistischer Regression

Der Code in diesem Abschnitt zeigt, wie ein logistisches Regressionsmodell, das im Dataset der Taxifahrten und Fahrpreise in NYC vorhersagt, ob ein Trinkgeld für eine Fahrt gezahlt wird, mit [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) trainiert, evaluiert und gespeichert werden kann.

**Trainieren des logistischen Regressionsmodells per Kreuzvalidierung und Hyperparameter-Sweeping**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:** 

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept: -0.0111216486893

Für die Ausführung der obigen Zelle benötigte Zeit: 14,43 Sekunden

**Evaluieren des binären Klassifizierungsmodells mit Standardmetriken**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**AUSGABE:** 

Area under PR = 0.985297691373

Area under ROC = 0.983714670256

Summary Stats

Precision = 0.984304060189

Recall = 0.984304060189

F1 Score = 0.984304060189

Für die Ausführung der obigen Zelle benötigte Zeit: 57,61 Sekunden

**Plotten Sie die ROC-Kurve.**

*predictionAndLabelsDF* wird als Tabelle *tmp_results* in der vorherigen Zelle registriert. *tmp_results* dient zum Ausführen von Abfragen und Ausgeben von Ergebnissen im Datenrahmen „sqlResults“ zum Zeichnen. Hier ist der Code angegeben.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Hier ist der Code zum Treffen von Vorhersagen und Plotten der ROC-Kurve angegeben.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**AUSGABE:**

![Logistic regression ROC curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Klassifizierung der Random Forest

Der Code in diesem Abschnitt zeigt, wie ein Random Forest-Modell, das vorhersagt, ob ein Trinkgeld für eine Fahrt im NYC-Taxifahrten- und Fahrpreisedataset gezahlt wird, trainiert, evaluiert und gespeichert werden kann.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

Area under ROC = 0.985297691373

Für die Ausführung der obigen Zelle benötigte Zeit: 31,09 Sekunden

### <a name="gradient-boosting-trees-classification"></a>Klassifizierung von Gradient-Boosted-Strukturen

Der Code in diesem Abschnitt zeigt, wie ein Gradient-Boosted-Strukturmodell, das vorhersagt, ob ein Trinkgeld für eine Fahrt im NYC-Taxifahrten- und Fahrpreisedataset gezahlt wird, trainiert, evaluiert und gespeichert werden kann.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

Area under ROC = 0.985297691373

Für die Ausführung der obigen Zelle benötigte Zeit: 19,76 Sekunden

## <a name="train-a-regression-model"></a>Trainieren eines Regressionsmodells

Dieser Abschnitt zeigt Ihnen die Verwendung von drei Modellen für die Regressionsaufgabe der Vorhersage des Trinkgeldbetrags, der für eine Taxifahrt gezahlt wird, auf der Basis anderer Trinkgeldfeatures. Die präsentierten Modelle sind:

* Normalisierte lineare Regression
* Random Forest
* Gradient-Boosted-Strukturen

Diese Modelle wurden in der Einführung beschrieben. Jeder Codeabschnitt zur Modellerstellung ist in Schritte unterteilt: 

1. **Modelltrainingsdaten** mit einem Parametersatz
2. **Modellevaluierung** an einem Testdataset mit Metriken
3. **Speichern des Modells** im Blob für die zukünftige Nutzung

### <a name="linear-regression-with-sgd"></a>Lineare Regression mit SGD

Der Code in diesem Abschnitt zeigt, wie skalierte Features zum Trainieren einer linearen Regression verwendet werden, die das stochastische Gradientenverfahren (SGD) für die Optimierung verwendet, und wie das Modell bewertet, evaluiert und in Azure Blob Storage (WASB) gespeichert wird.

> [!TIP]
> Nach unserer Erfahrung können Probleme mit der Konvergenz von LinearRegressionWithSGD-Modellen auftreten, und die Parameter müssen sorgfältig geändert/optimiert werden, um ein gültiges Modell zu erhalten. Das Skalieren von Variablen ist bei der Konvergenz hilfreich.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

Coefficients: [0.00457675809917, -0.0226314167349, -0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981, -0.000987181489428, -0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995, -0.00990211159703, -0.00637410344522, 0.545083566179, -0.536756072402, 0.0105762393099, -0.0130117577055, 0.0129304737772, -0.00171065945959]

Intercept: 0.853872718283

RMSE = 1.24190115863

R-sqr = 0.608017146081

Für die Ausführung der obigen Zelle benötigte Zeit: 58,42 Sekunden

### <a name="random-forest-regression"></a>Regression der Random Forest

Der Code in diesem Abschnitt zeigt, wie eine Random Forest-Regression, die den Trinkgeldbetrag für die NYC-Taxifahrtendaten vorhersagt, trainiert, evaluiert und gespeichert werden kann.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

RMSE = 0.891209218139

R-sqr = 0.759661334921

Für die Ausführung der obigen Zelle benötigte Zeit: 49,21 Sekunden

### <a name="gradient-boosting-trees-regression"></a>Gradient-Boosted-Strukturenregression

Der Code in diesem Abschnitt zeigt, wie ein Gradient-Boosted-Strukturmodell, das den Trinkgeldbetrag für die NYC-Taxifahrtendaten vorhersagt, trainiert, evaluiert und gespeichert werden kann.

**Trainieren und bewerten**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**AUSGABE:**

RMSE = 0.908473148639

R-sqr = 0.753835096681

Für die Ausführung der obigen Zelle benötigte Zeit: 34,52 Sekunden

**Diagrammausgabe**

*tmp_results* ist als Hive-Tabelle in der vorherigen Zelle registriert. Ergebnisse aus der Tabelle werden zum Plotten in den Dataframe *sqlResults* ausgegeben. Hier ist der Code angegeben.

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Hier ist der Code zum Plotten der Daten mit dem Jupyter-Server angegeben.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**AUSGABE:**

![Actual-vs-predicted-tip-amounts](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Bereinigung von Objekten aus dem Arbeitsspeicher

Löschen Sie mit `unpersist()` zwischengespeicherte Objekte aus dem Arbeitsspeicher.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>Speichern der Modelle

Zur Nutzung und Bewertung eines unabhängigen Datasets, das im Thema [Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md) beschrieben wird, müssen Sie diese Namen der Dateien, die die hier erstellten gespeicherten Modelle enthalten, kopieren und in das Jupyter-Notebook „Consumption“ einfügen. Hier ist der Code zum Drucken der Pfade zu Modelldateien, die Sie benötigen.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0317_06_51.737282"

## <a name="whats-next"></a>Wie geht es weiter?

Da Sie nun Regressions- und Klassifizierungsmodelle mit der Spark MlLib erstellt haben, können Sie jetzt lernen, wie diese Modelle bewertet und evaluiert werden. Das Notebook zum erweiterten Durchsuchen von Daten und Modellieren dringt tiefer in Kreuzvalidierung, Hyper-Parameter-Sweeping und Auswertung von Modellen ein. 

**Nutzung von Modellen:** Informationen zum Bewerten der in diesem Thema erstellten Klassifizierungs- und Regressionsmodelle finden Sie unter [Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md).

**Kreuzvalidierung und Hyperparameter-Sweeping**: Unter [Erweiterte Datenuntersuchung und Modellierung mit Spark](spark-advanced-data-exploration-modeling.md) erfahren Sie, wie Modelle mit Kreuzvalidierung und Hyperparameter-Sweeping trainiert werden können.