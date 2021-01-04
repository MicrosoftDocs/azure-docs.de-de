---
title: Erweitertes Untersuchen und Modellieren von Daten mit Spark –Team Data Science-Prozess
description: Verwenden Sie HDInsight Spark zum Untersuchen von Daten und Trainieren von Modellen für die binäre Klassifizierung und Regression mithilfe der Kreuzvalidierung und Hyperparameteroptimierung.
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
ms.openlocfilehash: 4f4a8fb82a42c5121105ddf7bb9d3d886b531350
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321335"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Erweitertes Untersuchen und Modellieren von Daten mit Spark

In dieser exemplarischen Vorgehensweise wird HDInsight Spark für das Durchsuchen von Daten und Trainieren von Modellen für die binäre Klassifizierung und Regression verwendet. Hierbei werden die Kreuzvalidierung und Hyperparameteroptimierung für eine Stichprobe des Datasets der NYC-Taxifahrten und Fahrpreise von 2013 genutzt. Sie werden schrittweise unter Verwendung eines HDInsight Spark-Clusters für die Verarbeitung und von Azure-Blobs zum Speichern der Daten und Modelle durch den gesamten [Data Science-Prozess](./index.yml)geführt. In diesem Prozess werden Daten aus einem Azure Storage-Blob untersucht sowie visualisiert und anschließend für das Erstellen von Vorhersagemodellen vorbereitet. Python wurde verwendet, um die Lösung zu codieren und die entsprechenden Diagramme anzuzeigen. Diese Modelle werden mithilfe des Spark MLlib-Toolkits erstellt, um Aufgaben zur binären Klassifizierung und Regressionsmodellierung ausführen. 

* Die Aufgabe zur **binären Klassifizierung** besteht darin, vorherzusagen, ob ein Trinkgeld für eine Fahrt bezahlt wird. 
* Die Aufgabe zur **Regression** besteht darin, die Höhe des Trinkgelds basierend auf anderen Trinkgeldfunktionen vorherzusagen. 

Die Modellierungsschritte enthalten auch Code zum Trainieren, Evaluieren und Speichern jedes Modelltyps. Das Thema behandelt einige der gleichen Aspekte wie im Thema [Durchsuchen von Daten und Modellierung mit Spark](spark-data-exploration-modeling.md). Es ist jedoch dahingehend „erweitert“, dass außerdem die Kreuzvalidierung mit Hyperparameter-Sweeping verwendet wird, um präzise Klassifizierungs- und Regressionsmodelle optimal zu trainieren. 

**Kreuzvalidierung** ist eine Technik, mit der bewertet wird, wie gut ein Modell, das mit einem bekannten Datensatz trainiert wurde, verallgemeinert, um die Features von Datensätzen vorherzusagen, mit denen es nicht trainiert wurde.  Eine gängige Implementierung besteht darin, den Datensatz k-fach aufzuteilen und das Modell daraufhin im Roundrobin-Verfahren mit allen Teilmengen außer einer zu trainieren. Die Fähigkeit des Modells zur genauen Vorhersage bei Tests mit dem unabhängigen Dataset in dieser Teilmenge, die nicht zum Trainieren des Modells verwendet wird, wird bewertet.

Die **Hyperparameteroptimierung** besteht in dem Problem, einen Hyperparametersatz für einen Lernalgorithmus auszuwählen, in der Regel um eine Leistungskennzahl des Algorithmus in Bezug auf einen unabhängigen Datensatz zu optimieren. **Hyperparameter** sind Werte, die außerhalb des Trainingsverfahrens des Modells festgelegt werden müssen. Annahmen über diese Werte können sich auf die Flexibilität und Genauigkeit der Modelle auswirken. Entscheidungsstrukturen umfassen Hyperparameter, wie z.B. die gewünschte Tiefe und Anzahl der Blätter in der Struktur. Support Vector Machines (SVMs) erfordern es, Bedingungen für Abzüge bei Fehlklassifikationen festzulegen. 

Eine hier häufig verwendete Methode für die Hyperparameteroptimierung besteht in der Rastersuche bzw. im **Parameter-Sweeping**. Diese Suche durchläuft eine Teilmenge des Hyperparameterraums für einen Lernalgorithmus. Die Kreuzvalidierung kann eine Leistungsmetrik liefern, um die besten Ergebnisse des Rastersuchalgorithmus auszusortieren. Zusammen mit dem Hyperparameter-Sweeping ermöglicht es die Kreuzvalidierung, Probleme wie eine Überanpassung eines Modells an die Trainingsdaten einzuschränken, damit das Modell weiterhin auf den allgemeinen Datensatz, aus dem die Trainingsdaten extrahiert wurden, angewandt werden kann.

Die Modelle, die wir verwenden, umfassen logistische und lineare Regression, Random Forests und Gradient-Boosted-Strukturen:

* [Lineare Regression mit SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) ist ein lineares Regressionsmodell, das eine SGD-Methode (Stochastic Gradient Descent, stochastisches Gradientenverfahren) zur Optimierung und Featureskalierung verwendet, um die gezahlten Trinkgeldbeträge vorherzusagen. 
* [Logistische Regression mit LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) oder „Logit“-Regression ist ein Regressionsmodell, das verwendet werden kann, wenn die abhängige Variable für Datenklassifizierung kategorisch ist. LBFGS ist ein quasi-Newtonscher Optimierungsalgorithmus, der sich dem Broyden-Fletcher-Goldfarb-Shanno-Algorithmus (BFGS) unter Verwendung einer begrenzten Menge an Arbeitsspeicher annähert und häufig im Machine Learning verwendet wird.
* [Random Forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sind Ensembles von Entscheidungsstrukturen.  In ihnen sind viele Entscheidungsstrukturen kombiniert, um das Risiko der Überanpassung zu verringern. Random Forests werden für Regression und Klassifizierung genutzt, können Kategoriefeatures verarbeiten und in eine Klassifizierungseinstellung mit mehreren Klassen erweitert werden. Sie erfordern keine Featureskalierung und können Nichtlinearitäten und Interaktionen von Features erfassen. Random Forest zählen zu den erfolgreichsten Machine Learning-Modelle für Klassifizierung und Regression.
* [Gradient-Boosted-Strukturen](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) sind Gruppen von Entscheidungsstrukturen. GBTS trainieren Entscheidungsstrukturen iterativ, um eine Verlustfunktion zu minimieren. GBTS werden für die Regression und Klassifizierung verwendet und können kategorische Features behandeln, erfordern keine Featureskalierung und können Nichtlinearitäten und Featureinteraktionen erfassen. Sie können auch in einer Mehrklassenklassifizierung verwendet werden.

Beispiele für die Modellierung mit Kreuzvalidierung und Hyperparameter-Sweeping werden für das binäre Klassifizierungsproblem gezeigt. Einfachere Beispiele (ohne Parameter-Sweeping) finden sich im Hauptthema zu Regressionsaufgaben. Im Anhang werden jedoch auch die Validierung mit Elastic Net für die lineare Regression und die Kreuzvalidierung mit Parameter-Sweeping für eine Regression der Random Forest veranschaulicht. **Elastic Net** ist eine regularisierte Regressionsmethode zum Anpassen linearer Regressionsmodelle, die die Metriken L1 und L2 als Abzüge für die Methoden [Lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) und [Ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) kombiniert.   

<!-- -->

> [!NOTE]
> Obwohl das Spark MLlib-Toolkit für die Arbeit mit großen Datasets entworfen wurde, wird der Einfachheit halber ein relativ kleines Beispiel (ca. 30 MB mit 170.000 Zeilen, etwa 0,1 % des ursprünglichen NYC-Datasets) verwendet. Diese Übung wird in einem HDInsight-Cluster mit zwei Workerknoten (in ungefähr 10 Minuten) effizient ausgeführt. Der gleiche Code kann mit geringfügigen Änderungen zur Verarbeitung größerer Datasets verwendet werden – mit entsprechenden Änderungen zum Zwischenspeichern von Daten im Arbeitsspeicher und Änderung der Clustergröße.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Einrichtung: Spark-Cluster und Notebooks
Die Einrichtungsschritte und der Code in dieser exemplarischen Vorgehensweise beziehen sich auf HDInsight Spark 1.6. Jupyter-Notebooks werden jedoch für HDInsight Spark 1.6- und Spark 2.0-Cluster bereitgestellt. Eine Beschreibung der Notebooks und Links zu diesen finden Sie in der Datei [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) zu dem GitHub-Repository, das sie enthält. Der hier und in den verknüpften Notebooks zu findende Code ist darüber hinaus generisch und sollte in allen Spark-Clustern funktionieren. Wenn Sie HDInsight Spark nicht verwenden, weichen Clustereinrichtung und Verwaltungsschritte möglicherweise geringfügig von dem ab, was hier gezeigt wird. Der Einfachheit halber finden Sie hier die Links zu den Jupyter-Notebooks für Spark 1.6 und 2.0, die im PySpark-Kernel der Jupyter-Notebookserver ausgeführt werden:

### <a name="spark-16-notebooks"></a>Spark 1.6-Notebooks

[pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Enthält Themen im Notebook 1 und Modellentwicklung mit Hyperparameteroptimierung und Kreuzvalidierung.

### <a name="spark-20-notebooks"></a>Spark 2.0-Notebooks

[Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb:](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) Diese Datei bietet Informationen zum Durchführen von Datenuntersuchungen, Modellieren und Bewerten in Spark 2.0-Clustern.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: Speicherorte, Bibliotheken und vorab festgelegter Spark-Kontext
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**OUTPUT**

datetime.datetime(2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importieren von Bibliotheken
Importieren Sie die erforderlichen Bibliotheken mit dem unten angegebenen Code.

```python
# LOAD PYSPARK LIBRARIES
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

Weitere Informationen zu den Kernels für Jupyter-Notebooks und den zugehörigen vordefinierten „Magics“ finden Sie unter [Verfügbare Kernels für Jupyter-Notebooks mit HDInsight Spark-Linux-Clustern in HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Datenerfassung über das öffentliche Blob:
Der erste Schritt im Data Science-Prozess ist das Erfassen der zu analysierenden Daten aus Quellen in der Umgebung, die Sie zum Durchsuchen und Modellieren von Daten nutzen. In dieser exemplarischen Vorgehensweise ist Spark diese Umgebung. Dieser Abschnitt enthält den Code zur Ausführung einer Reihe von Aufgaben:

* Erfassen des zu modellierenden Datenbeispiels
* Einlesen des Eingabedatasets (gespeichert als TSV-Datei)
* Formatieren und Bereinigen der Daten
* Erstellen und Zwischenspeichern von Objekten (RDDs oder Datenrahmen) im Arbeitsspeicher
* deren Registrierung als temporäre Tabelle im SQL-Kontext.

Hier ist der Code für die Datenerfassung.

```python
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

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 276,62 Sekunden

## <a name="data-exploration--visualization"></a>Durchsuchen und Visualisierung von Daten
Nachdem die Daten in Spark eingegeben wurden, besteht der nächste Schritt im Data Science-Prozess darin, durch Durchsuchen und Visualisieren eine tiefer gehende Einsicht in die Daten zu erhalten. In diesem Abschnitt untersuchen wir die Taxidaten mit SQL-Abfragen und zeichnen Diagramme der Zielvariablen und künftigen Merkmale zur Sichtprüfung. Insbesondere stellen wir die Häufigkeit der Fahrgastzahlen bei Taxifahrten, die Häufigkeit der Trinkgeldbeträge und die Variation der Trinkgelder nach Zahlungsbetrag und -art im Diagramm dar.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Zeichnen eines Histogramms der Häufigkeit der Fahrgastzahlen im Taxifahrtenbeispiel
In diesem Code und den nachfolgenden Codeausschnitten werden SQL-Magic-Befehle verwendet, um das Beispiel abzufragen, und lokale Magic-Befehle, um die Daten in einem Diagramm darzustellen.

* **SQL magic (`%%sql`)** Der HDInsight PySpark-Kernel unterstützt einfache HiveQL-Inlineabfragen für „sqlContext“. Mit dem Argument (-o VARIABLE_NAME) wird die Ausgabe der SQL-Abfrage als Pandas-Dataframe auf dem Jupyter-Server beibehalten. Das bedeutet, dass die Daten im lokalen Modus verfügbar sind.
* Der Befehl **`%%local` magic** wird genutzt, um Code lokal auf dem Jupyter-Server auszuführen. Dies ist der Hauptknoten des HDInsight-Clusters. In der Regel verwenden Sie die `%%local`-Magic, nachdem die `%%sql -o`-Magic zum Ausführen einer Abfrage verwendet wurde. Durch den Parameter „-o“ wird die Ausgabe der SQL-Abfrage lokal gespeichert. Die `%%local`-Magic löst anschließend den nächsten Satz von Codeausschnitten aus, die lokal auf die Ausgabe der SQL-Abfragen angewendet werden, die lokal gespeichert wurde. Die Ausgabe wird automatisch visualisiert, nachdem Sie den Code ausgeführt haben.

Mit dieser Abfrage werden die Fahrten nach der Fahrgastanzahl abgerufen. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Mit dem Code wird aus der Ausgabe der Abfrage ein lokaler Dataframe erstellt, und die Daten werden in einem Diagramm dargestellt („geplottet“). Der Magic-Befehl `%%local` erstellt einen lokalen Dataframe `sqlResults`, der für das Plotten mit matplotlib verwendet werden kann. 

<!-- -->

> [!NOTE]
> Dieser PySpark-Magic-Befehl wird in dieser exemplarischen Vorgehensweise mehrfach genutzt. Bei einer großen Datenmenge sollten Sie Stichproben verwenden, um einen Dataframe zu erstellen, der nicht zu groß für den lokalen Arbeitsspeicher ist.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Hier ist der Code zum Plotten der Fahrten nach der Fahrgastanzahl angegeben:

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**OUTPUT**

![Fahrtenhäufigkeit nach Anzahl der Fahrgäste](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Sie können zwischen verschiedenen Arten von Visualisierungen auswählen (Tabelle, Kreis, Linie, Fläche oder Balken), indem Sie im Notebook die Menüschaltflächen unter **Typ** verwenden. Hier ist das Balkendiagramm dargestellt.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Zeichnen eines Histogramms des Trinkgeldbetrags und der Variation des Trinkgeldbetrags nach Anzahl der Fahrgäste und Höhe des Fahrpreises
Verwenden Sie eine SQL-Abfrage, um Beispieldaten zu erstellen.

```sql
# SQL SQUERY
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
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**AUSGABE:** 

![Trinkgeldbetragverteilung](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Trinkgeldbetrag nach Anzahl der Fahrgäste](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Trinkgeldbetrag nach Höhe des Fahrpreises](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Feature Engineering, Transformation und Datenvorbereitung für die Modellierung
Dieser Abschnitt enthält den Code für die Prozeduren zum Vorbereiten von Daten für die Verwendung in der ML-Modellierung und deren Beschreibung. Hier erfahren Sie, wie Sie die folgenden Aufgaben ausführen:

* Erstellen eines neuen Features durch Aufteilen von Stunden in Verkehrszeitcontainer
* Indizieren und One-Hot-Codieren von kategorischen Features
* Erstellen von bezeichneten Punktobjekten für die Eingabe in ML-Funktionen
* Erstellen einer Zufallsunterauswahl der Daten und Aufteilen in Trainings- und Testsätze
* Featureskalierung
* Zwischenspeichern von Objekten im Arbeitsspeicher

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Erstellen eines neuen Features durch Aufteilen von Verkehrszeit in Container
Dieser Code zeigt, wie Sie ein neues Feature durch Aufteilen von Stunden in Verkehrszeitcontainer erstellen und dann den resultierenden Datenrahmen im Arbeitsspeicher zwischenspeichern. Das Zwischenspeichern führt zu verbesserten Ausführungszeiten, wenn robuste verteilte Datasets (Resilient Distributed Datasets, RDDs) und Datenrahmen wiederholt verwendet werden Folglich werden wir RDDs und Datenrahmen in mehreren Phasen der exemplarischen Vorgehensweise zwischenspeichern.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**OUTPUT**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Indizieren und One-Hot-Codieren von kategorischen Features
Dieser Abschnitt beschreibt das Indizieren oder Codieren von kategorischen Features für die Eingabe in die Modellierungsfunktionen. Die Modellierungs- und Vorhersagefunktionen von MLlib erfordern, dass Features mit kategorischen Eingabedaten vor der Verwendung indiziert oder codiert werden. 

Je nach Modell müssen Sie sie auf unterschiedliche Weise indizieren oder codieren. Logistische und lineare Regressionsmodelle erfordern beispielsweise One-Hot-Codierung, wobei z.B. ein Feature mit drei Kategorien auf drei Featurespalten erweitert werden kann, wobei jede abhängig von der Kategorie einer Beobachtung 0 oder 1 enthalten kann. MLlib bietet die Funktion [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) zur One-Hot-Codierung. Dieser Encoder ordnet eine Spalte mit Bezeichnungsindizes einer Spalte mit binären Vektoren mit höchstens einem einzigen Wert zu. Diese Codierung ermöglicht die Anwendung von Algorithmen, die Nummernwertfeatures erwarten, z.B. logistische Regression, auf kategorische Features.

Hier ist der Code zum Indizieren und Codieren von kategorischen Features:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 3,14 Sekunden

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Erstellen von bezeichneten Punktobjekten für die Eingabe in ML-Funktionen
Dieser Abschnitt enthält Code, der zeigt, wie kategorische Textdaten als bezeichneter Punktdatentyp indiziert und codiert werden. Diese Transformation bereitet Textdaten auf das Trainieren der logistischen MLlib-Regressions- und anderer Klassifizierungsmodelle vor. Bezeichnete Punktobjekte sind robuste verteilte Datasets (RDD), die in einer Weise formatiert sind, die von den meisten ML-Algorithmen in MLlib als Eingabedaten benötigt wird. Ein [bezeichneter Punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) ist ein lokaler Vektor, entweder dicht oder platzsparend, der mit einer Bezeichnung/Antwort verknüpft ist.

Hier ist der Code angegeben, mit dem Textfeatures für die binäre Klassifizierung indiziert und codiert werden.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Dieser Code erstellt eine zufällige Stichprobe der Daten (25 % werden hier verwendet). Obwohl es aufgrund der Größe des Datasets in diesem Beispiel nicht erforderlich ist, zeigen wir Ihnen, wie Sie hier Stichproben erfassen können. Sie können dann dieses Verfahren bei Bedarf zur Lösung eigener Probleme verwenden. Bei großen Stichproben kann die Stichprobenentnahme beim Trainieren von Modellen deutlich Zeit sparen. Als Nächstes teilen wir die Stichprobe in einen Trainingsteil (hier 75 %) und einen Testteil (hier 25 %) zur Klassifizierung und Regressionsmodellierung.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

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

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 0,31 Sekunden

### <a name="feature-scaling"></a>Featureskalierung
Featureskalierung, auch bekannt als Datennormalisierung, stellt sicher, dass Features mit weit verteilten Werten keine übermäßige Gewichtung in der Zielfunktion erhalten. Der Code für die Featureskalierung verwendet [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) zum Skalieren der Features auf Einheitenvarianz. Er wird von MLlib für die Verwendung bei der linearen Regression mit dem stochastischen Gradientenverfahren (Stochastic Gradient Descent, SGD) bereitgestellt. SGD ist ein beliebter Algorithmus für das Training einer Vielzahl anderer Machine Learning-Modelle, z.B. normalisierte Regressionen oder Support Vector Machines (SVM).   

> [!TIP]
> Wir haben festgestellt, dass der LinearRegressionWithSGD-Algorithmus für die Featureskalierung zu empfindlich ist.   
> 
> 

Im Folgenden finden Sie den Code zum Skalieren von Variablen für die Verwendung mit dem normalisierten linearen SGD-Algorithmus.

```python
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

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 11,67 Sekunden

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

**OUTPUT** 

Für die Ausführung der obigen Zelle benötigte Zeit: 0,13 Sekunden

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Vorhersage, ob ein Trinkgeld bezahlt wird, mit Modellen zur binären Klassifizierung
Dieser Abschnitt beschreibt die Verwendung von drei Modellen für die Aufgabe der binären Klassifizierung der Vorhersage, ob für eine Taxifahrt ein Trinkgeld bezahlt wird. Die präsentierten Modelle sind:

* Logistische Regression 
* Random Forest
* Gradient-Boosted-Strukturen

Jeder Codeabschnitt zur Modellerstellung ist in Schritte unterteilt: 

1. **Modelltrainingsdaten** mit einem Parametersatz
2. **Modellevaluierung** an einem Testdataset mit Metriken
3. **Speichern des Modells** im Blob für die zukünftige Nutzung

Die Kreuzvalidierung mit Parameter-Sweeping kann auf zweierlei Weise ausgeführt werden:

1. Mithilfe von **allgemeinem** benutzerdefiniertem Code, der auf jeden Algorithmus in MLlib und auf alle Parametersätze in einem Algorithmus angewendet werden kann. 
2. Mithilfe der **PySpark CrossValidator-Pipelinefunktion**. CrossValidator weist für Spark 1.5.0 einige Einschränkungen auf: 
   
   * Pipelinemodelle können für die zukünftige Verwendung nicht gespeichert oder beibehalten werden.
   * Kann nicht für jeden Parameter in einem Modell verwendet werden.
   * Kann nicht für jeden MLlib-Algorithmus verwendet werden.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Allgemeine Kreuzvalidierung und Hyperparameter-Sweeping mit dem logistischen Regressionsalgorithmus für die binäre Klassifizierung
Der Code in diesem Abschnitt zeigt, wie ein logistisches Regressionsmodell, das im Dataset der Taxifahrten und Fahrpreise in NYC vorhersagt, ob ein Trinkgeld für eine Fahrt gezahlt wird, mit [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) trainiert, evaluiert und gespeichert werden kann. Das Modell wird mithilfe der Kreuzvalidierung und des Hyperparameter-Sweeping trainiert. Dies wurde mit benutzerdefiniertem Code implementiert, der auf alle Lernalgorithmen in MLlib angewandt werden kann.   

<!-- -->

> [!NOTE]
> Die Ausführung dieser benutzerdefinierten Kreuzvalidierung kann einige Minuten in Anspruch nehmen.

<!-- -->

**Trainieren des logistischen Regressionsmodells per Kreuzvalidierung und Hyperparameter-Sweeping**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

Coefficients: [0.0082065285375, -0.0223675576104, -0.0183812028036, -3.48124578069e-05, -0.00247646947233, -0.00165897881503, 0.0675394837328, -0.111823113101, -0.324609912762, -0.204549780032, -1.36499216354, 0.591088507921, -0.664263411392, -1.00439726852, 3.46567827545, -3.51025855172, -0.0471341112232, -0.043521833294, 0.000243375810385, 0.054518719222]

Intercept: -0.0111216486893

Für die Ausführung der obigen Zelle benötigte Zeit: 14,43 Sekunden

**Evaluieren des binären Klassifizierungsmodells mit Standardmetriken**

Der Code in diesem Abschnitt zeigt, wie ein logistisches Regressionsmodell mit einem Testdataset evaluiert wird, einschließlich Ausgabe der ROC-Kurve.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

Area under PR = 0.985336538462

Area under ROC = 0.983383274312

Summary Stats

Precision = 0.984174341679

Recall = 0.984174341679

F1 Score = 0.984174341679

Für die Ausführung der obigen Zelle benötigte Zeit: 2,67 Sekunden

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

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

**OUTPUT**

![ROC-Kurve der logistischen Regression für die allgemeine Methode](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Beibehalten des Modells im Blob für die zukünftige Nutzung**

Der Code in diesem Abschnitt zeigt, wie das logistische Regressionsmodell für die Nutzung gespeichert wird.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 34,57 Sekunden

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Verwenden der CrossValidator-Pipeline-Funktion von MLlib mit dem logistischen Regressionsmodell (elastische Regression)
Der Code in diesem Abschnitt zeigt, wie ein logistisches Regressionsmodell, das im Dataset der Taxifahrten und Fahrpreise in NYC vorhersagt, ob ein Trinkgeld für eine Fahrt gezahlt wird, mit [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) trainiert, evaluiert und gespeichert werden kann. Das Modell wird mithilfe der Kreuzvalidierung und des Hyperparameter-Sweeping trainiert. Dies wurde mit der MLlib CrossValidator-Pipeline-Funktion für die Kreuzvalidierung mit Hyperparameter-Sweeping implementiert.   

<!-- -->

> [!NOTE]
> Die Ausführung dieses MLlib-Kreuzvalidierungscode kann einige Minuten in Anspruch nehmen.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 107,98 Sekunden

**Plotten Sie die ROC-Kurve.**

*predictionAndLabelsDF* wird als Tabelle *tmp_results* in der vorherigen Zelle registriert. *tmp_results* dient zum Ausführen von Abfragen und Ausgeben von Ergebnissen im Datenrahmen „sqlResults“ zum Zeichnen. Hier ist der Code angegeben.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Hier ist der Code zum Plotten der ROC-Kurve angegeben.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

**OUTPUT**

![ROC-Kurve der logistischen Regression mithilfe des CrossValidator von MLlib](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Klassifizierung der Random Forest
Der Code in diesem Abschnitt zeigt, wie eine Regression der Random Forest, die im NYC-Taxifahrten- und Fahrpreisedataset vorhersagt, ob ein Trinkgeld für eine Fahrt gezahlt wird, trainiert, evaluiert und gespeichert werden kann.

```python
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
## UN-COMMENT IF YOU WANT TO PRING TREES
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

**OUTPUT**

Area under ROC = 0.985336538462

Für die Ausführung der obigen Zelle benötigte Zeit: 26,72 Sekunden

### <a name="gradient-boosting-trees-classification"></a>Klassifizierung von Gradient-Boosted-Strukturen
Der Code in diesem Abschnitt zeigt, wie ein Gradient-Boosted-Strukturmodell, das vorhersagt, ob ein Trinkgeld für eine Fahrt im NYC-Taxifahrten- und Fahrpreisedataset gezahlt wird, trainiert, evaluiert und gespeichert werden kann.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
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

**OUTPUT**

Area under ROC = 0.985336538462

Für die Ausführung der obigen Zelle benötigte Zeit: 28,13 Sekunden

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Vorhersage von Trinkgeldbeträgen mit Regressionsmodellen (ohne Kreuzvalidierung)
Dieser Abschnitt zeigt Ihnen die Verwendung von drei Modellen für die Regressionsaufgabe: Vorhersage des Trinkgeldbetrags, der für eine Taxifahrt gezahlt wird, auf der Basis anderer Trinkgeldfeatures. Die präsentierten Modelle sind:

* Normalisierte lineare Regression
* Random Forest
* Gradient-Boosted-Strukturen

Diese Modelle wurden in der Einführung beschrieben. Jeder Codeabschnitt zur Modellerstellung ist in Schritte unterteilt: 

1. **Modelltrainingsdaten** mit einem Parametersatz
2. **Modellevaluierung** an einem Testdataset mit Metriken
3. **Speichern des Modells** im Blob für die zukünftige Nutzung   

<!-- -->

> [!NOTE] 
> Die Kreuzvalidierung wird für die drei Regressionsmodelle in diesem Abschnitt nicht verwendet, da dies für die logistischen Regressionsmodelle ausführlich gezeigt wurde. Ein Beispiel dazu, wie die Kreuzvalidierung mit Elastic Net für die lineare Regression verwendet wird, finden Sie im Anhang zu diesem Thema.

<!-- -->

<!-- -->

> [!NOTE] 
> Nach unserer Erfahrung können Probleme mit der Konvergenz von LinearRegressionWithSGD-Modellen auftreten, und die Parameter müssen sorgfältig geändert/optimiert werden, um ein gültiges Modell zu erhalten. Das Skalieren von Variablen ist bei der Konvergenz hilfreich. Die Elastic Net-Regression, die im Anhang dieses Themas veranschaulicht wird, kann auch anstelle von LinearRegressionWithSGD verwendet werden.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Lineare Regression mit SGD
Der Code in diesem Abschnitt zeigt, wie skalierte Features zum Trainieren einer linearen Regression verwendet werden, die das stochastische Gradientenverfahren (SGD) für die Optimierung verwendet, und wie das Modell bewertet, evaluiert und in Azure Blob Storage (WASB) gespeichert wird.

> [!TIP]
> Nach unserer Erfahrung können Probleme mit der Konvergenz von LinearRegressionWithSGD-Modellen auftreten, und die Parameter müssen sorgfältig geändert/optimiert werden, um ein gültiges Modell zu erhalten. Das Skalieren von Variablen ist bei der Konvergenz hilfreich.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

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

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

Coefficients: [0.0141707753435, -0.0252930927087, -0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092, -0.00456498588241, -0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632, -0.00289545676449, -0.00791124681938, 0.54396316518, -0.536293513569, 0.0119076553369, -0.0173039244582, 0.0119632796147, 0.00146764882502]

Intercept: 0.854507624459

RMSE = 1.23485131376

R-sqr = 0.597963951127

Für die Ausführung der obigen Zelle benötigte Zeit: 38,62 Sekunden

### <a name="random-forest-regression"></a>Regression der Random Forest
Der Code in diesem Abschnitt zeigt, wie ein Random Forest-Modell, das den Trinkgeldbetrag für die NYC-Taxifahrtendaten vorhersagt, trainiert, evaluiert und gespeichert werden kann.   

<!-- -->

> [!NOTE]
> Kreuzvalidierung mit Parameter-Sweeping mithilfe von benutzerdefinierten Code finden Sie im Anhang.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

**OUTPUT**

RMSE = 0.931981967875

R-sqr = 0.733445485802

Für die Ausführung der obigen Zelle benötigte Zeit: 25,98 Sekunden

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

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

RMSE = 0.928172197114

R-sqr = 0.732680354389

Für die Ausführung der obigen Zelle benötigte Zeit: 20,9 Sekunden

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
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Actual-vs-predicted-tip-amounts](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Anhang: Zusätzliche Regressionsaufgaben mithilfe der Kreuzvalidierung mit Parameter-Sweeping
Dieser Anhang enthält Code, der zeigt, wie die Kreuzvalidierung mithilfe von Elastic Net für die lineare Regression und mit Parameter-Sweeping mithilfe von benutzerdefiniertem Code für die Regression der Random Forest verwendet wird.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Kreuzvalidierung mithilfe von Elastic Net für die lineare Regression
Der Code in diesem Abschnitt zeigt wie die Kreuzvalidierung mithilfe von Elastic Net für die lineare Regression durchgeführt wird und wie das Modell in Bezug auf Testdaten evaluiert wird.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

Für die Ausführung der obigen Zelle benötigte Zeit: 161,21 Sekunden

**Auswerten mit R-SQR-Metrik**

*tmp_results* ist als Hive-Tabelle in der vorherigen Zelle registriert. Ergebnisse aus der Tabelle werden zum Plotten in den Dataframe *sqlResults* ausgegeben. Hier ist der Code angegeben.

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Hier ist der Code zum Berechnen von R-sqr angegeben.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**OUTPUT**

R-sqr = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kreuzvalidierung mit Parameter-Sweeping mithilfe von benutzerdefiniertem Code für die Regression der Random Forest
Der Code in diesem Abschnitt zeigt wie die Kreuzvalidierung mit Parameter-Sweeping mithilfe von benutzerdefiniertem Code für die Regression der Random Forest durchgeführt wird und wie das Modell in Bezug auf Testdaten evaluiert wird.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**OUTPUT**

RMSE = 0.906972198262

R-sqr = 0.740751197012

Für die Ausführung der obigen Zelle benötigte Zeit: 69,17 Sekunden

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Bereinigen von Objekten aus dem Arbeitsspeicher und Drucken von Modellspeicherorten
Löschen Sie mit `unpersist()` zwischengespeicherte Objekte aus dem Arbeitsspeicher.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

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

**OUTPUT**

PythonRDD[122] at RDD at PythonRDD.scala: 43

\*\*Geben Sie den Pfad zu den Modelldateien zur Verwendung im „Consumption Notebook“ aus. \*\*Um Nutzung und Bewertung für ein unabhängiges Dataset zu erhalten, müssen Sie diese Dateinamen kopieren und in das „Consumption Notebook“ einfügen.

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**OUTPUT**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05-0316_47_30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05-0316_51_28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05-0316_50_17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05-0316_51_57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05-0316_50_40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05-0316_52_18.827237"

## <a name="whats-next"></a>Wie geht es weiter?
Da Sie nun Regressions- und Klassifizierungsmodelle mit der Spark MlLib erstellt haben, können Sie jetzt lernen, wie diese Modelle bewertet und evaluiert werden.

**Nutzung von Modellen:** Informationen zum Bewerten der in diesem Thema erstellten Klassifizierungs- und Regressionsmodelle finden Sie unter [Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md).