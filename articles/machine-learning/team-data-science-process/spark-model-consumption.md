---
title: Operationalisieren von Machine Learning-Modellen, die mit Spark erstellt wurden – Team Data Science-Prozess
description: Laden und Bewerten von in Azure Blob Storage (WASB) gespeicherten Learning-Modellen mit Python.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: dd0467479960df30b1d44aeaef7ed0ed0d6c2a87
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60253175"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operationalisieren von Machine Learning-Modellen, die mit Spark erstellt wurden

In diesem Thema wird gezeigt, wie ein gespeichertes Machine Learning-Modell (ML) mithilfe von Python in HDInsight Spark-Clustern operationalisiert wird. Es wird beschrieben, wie Machine Learning-Modelle geladen werden, die mit Spark MLlib erstellt und in Azure Blob Storage (WASB) gespeichert wurden, und wie diese Modelle mit ebenfalls in WASB gespeicherten Datasets bewertet werden. Es zeigt, wie eingegebene Daten vorab verarbeitet, Funktionen mithilfe der Indizierungs- und Codierungsfunktionen im MLlib-Toolkit transformiert werden und ein bezeichnetes Datenpunktobjekt erstellt wird, das als Eingabe für die Bewertung der ML-Modelle verwendet werden kann. Zu den für die Bewertung verwendeten Modellen zählen lineare Regression, logistische Regression, Random Forest-Modelle und Gradient-Boosted-Strukturmodelle.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-Cluster und Jupyter-Notebooks
Setupschritte und der Code zum Operationalisieren eines ML-Modells werden in dieser exemplarischen Vorgehensweise für die Verwendung sowohl eines HDInsight Spark 1.6-Clusters als auch eines Spark 2.0-Clusters bereitgestellt. Der Code für diese Verfahren wird außerdem in Jupyter-Notebooks bereitgestellt.

### <a name="notebook-for-spark-16"></a>Notebook für Spark 1.6
Das Jupyter-Notebook [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) zeigt, wie ein gespeichertes Modell mithilfe von Python in HDInsight-Clustern operationalisiert wird. 

### <a name="notebook-for-spark-20"></a>Notebook für Spark 2.0
Um das Jupyter-Notebook für Spark 1.6 für die Verwendung mit einem HDInsight Spark 2.0-Cluster zu ändern, ersetzen Sie die Python-Codedatei durch [diese Datei](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Dieser Code zeigt, wie die in Spark 2.0 erstellten Modelle genutzt werden.


## <a name="prerequisites"></a>Voraussetzungen

1. Sie benötigen ein Azure-Konto und einen Spark 1.6- (oder Spark 2.0-) HDInsight-Cluster zum Durchführen dieser exemplarischen Vorgehensweise. Anweisungen zum Erfüllen dieser Anforderungen finden Sie im Thema [Übersicht über Data Science mit Spark in Azure HDInsight](spark-overview.md). Dieses Thema enthält auch eine Beschreibung der hier verwendeten NYC 2013 Taxi-Daten und eine Anleitung zum Ausführen von Code aus einem Jupyter Notebook im Spark-Cluster. 
2. Außerdem müssen Sie die hier zu bewertenden Machine Learning-Modelle erstellen, indem Sie das Thema [Durchsuchen von Daten und Modellierung mit Spark](spark-data-exploration-modeling.md) für Spark 1.6-Cluster oder Spark 2.0-Notebooks durcharbeiten. 
3. Die Spark 2.0-Notebooks verwenden ein zusätzliches Dataset für den Klassifizierungstask, nämlich das bekannte Dataset zur Pünktlichkeit von Flugreisestarts aus den Jahren 2011 und 2012. Eine Beschreibung der Notebooks und Links zu diesen finden Sie in der Datei [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) zu dem GitHub-Repository, das sie enthält. Der hier und in den verknüpften Notebooks zu findende Code ist darüber hinaus generisch und sollte in allen Spark-Clustern funktionieren. Wenn Sie HDInsight Spark nicht verwenden, weichen Clustereinrichtung und Verwaltungsschritte möglicherweise geringfügig von dem ab, was hier gezeigt wird. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Setup: Speicherorte, Bibliotheken und vorab festgelegter Spark-Kontext
Spark kann aus einem Azure Storage-Blob (WASB) lesen und darin schreiben. Es können also alle Ihre vorhandenen Daten mithilfe von Spark verarbeitet und die Ergebnisse wieder im WASB gespeichert werden.

Um Modelle oder Dateien im WASB zu speichern, muss der Pfad korrekt angegeben werden. Auf den an den Spark-Cluster angefügten Standardcontainer können Sie mit einem Pfad verweisen, der mit *wasb///* beginnt. Das folgende Codebeispiel gibt den Speicherort der zu lesenden Daten und den Pfad für das Modellspeicherverzeichnis an, in dem die Ausgabe des Modells gespeichert wird. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Festlegen von Verzeichnispfaden für Speicherorte im WASB
Modelle werden gespeichert in: „wasb:///user/remoteuser/NYCTaxi/Models“. Wenn dieser Pfad nicht ordnungsgemäß festgelegt ist, werden Modelle nicht für die Bewertung geladen.

Die bewerteten Ergebnisse wurden gespeichert in: „wasb:///user/remoteuser/NYCTaxi/ScoredResults“. Wenn der Pfad zum Ordner falsch ist, werden die Ergebnisse nicht in diesem Ordner gespeichert.   

> [!NOTE]
> Die Dateipfade können aus der Ausgabe der letzten Zelle des **machine-learning-data-science-spark-data-exploration-modeling.ipynb** -Notebooks kopiert und in die Platzhalter in diesem Code eingefügt werden.   
> 
> 

Hier ist der Code zum Festlegen der Verzeichnispfade: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**AUSGABE:**

datetime.datetime(2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importieren von Bibliotheken
Legen Sie den Spark-Kontext fest, und importieren Sie die erforderlichen Bibliotheken mit dem folgenden Code.

    #IMPORT LIBRARIES
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


### <a name="preset-spark-context-and-pyspark-magics"></a>Vorab festgelegter Spark-Kontext und PySpark-Magics
Der Kontext der mit Jupyter-Notebooks bereitgestellten PySpark-Kernel ist voreingestellt. Sie müssen nicht ausdrücklich den Spark- oder Hive-Kontext festlegen, um mit der Anwendung, die Sie entwickeln, arbeiten zu können. Diese sind standardmäßig verfügbar. Diese Kontexte sind:

* sc – für Spark 
* sqlContext – für Hive

Der PySpark-Kernel bietet einige so genannte „Magic-Befehle“, die vordefiniert sind. Dies sind spezielle Befehle, die Sie mit %% aufrufen können. Es gibt zwei Befehle dieser Art, die in den Codebeispielen verwendet werden.

* **%%local** gibt an, dass der Code in den nachfolgenden Zeilen lokal ausgeführt wird. Der Code muss gültiger Python-Code sein.
* **%%sql -o \<Variablenname>** 
* Führt eine Hive-Abfrage für sqlContext aus. Wenn der Parameter -o übergeben wird, wird das Ergebnis der Abfrage im %%local-Python-Kontext als Pandas-Dataframe beibehalten.

Weitere Informationen zu den Kernels für Jupyter-Notebooks und den zugehörigen vordefinierten „Magics“ finden Sie unter [Verfügbare Kernels für Jupyter-Notebooks mit HDInsight Spark-Linux-Clustern in HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Erfassen von Daten und Erstellen eines bereinigten Datenrahmens
Dieser Abschnitt enthält den Code für eine Reihe erforderlicher Aufgaben zum Erfassen der Daten, die bewertet werden sollen. Lesen Sie eine verknüpfte 0,1 %-Stichprobe der Datei für Taxifahrten und Fahrpreise (gespeichert als TSV-Datei) ein, formatieren und bereinigen Sie die Daten, und erstellen Sie einen bereinigten Datenrahmen.

Die Taxifahrten und Fahrpreise wurden basierend auf dem Verfahren verknüpft, das in folgendem Thema beschrieben wird: [Der Team Data Science-Prozess in Aktion: Verwenden von HDInsight Hadoop-Clustern](hive-walkthrough.md).

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)

    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
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

    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()

    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 46,37 Sekunden

## <a name="prepare-data-for-scoring-in-spark"></a>Vorbereiten von Daten für die Bewertung in Spark
In diesem Abschnitt wird das Indizieren, Codieren und Skalieren kategorischer Features beschrieben, um sie für die Verwendung in MLlib-überwachten Lernalgorithmen für Klassifizierung und Regression vorzubereiten.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Featuretransformation: Indizieren und Codieren kategorischer Features für die Eingabe in Modelle für die Bewertung
In diesem Abschnitt wird gezeigt, wie kategorische Daten mithilfe von `StringIndexer` indiziert und Features mit `OneHotEncoder`-Eingabe in den Modellen codiert werden.

Der [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) codiert eine Zeichenfolgenspalte von Bezeichnungen in eine Spalte mit Bezeichnungsindizes. Die Indizes werden nach der Häufigkeit der Bezeichnungen sortiert. 

Der [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) ordnet eine Spalte mit Bezeichnungsindizes einer Spalte mit binären Vektoren mit höchstens einem einzigen Wert zu. Diese Codierung ermöglicht die Anwendung von Algorithmen, die kontinuierliche wertige Features erwarten, z. B. logistische Regression, auf kategorische Features.

    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()

    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
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

    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 5,37 Sekunden

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Erstellen von RDD-Objekten mit Featurearrays für die Eingabe in Modelle
Dieser Abschnitt enthält Code, der zeigt, wie Sie kategorische Textdaten als RDD-Objekt indizieren und one-hot-codieren, sodass sie zum Trainieren und Testen logistischer Regression gemäß MLlib und anderer strukturbasierter Modelle verwendet werden können. Die indizierten Daten sind in [RDD](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) -Objekten (robuste verteilte Datasets) gespeichert. Hierbei handelt es sich um die grundlegende Abstraktion in Spark. Ein RDD-Objekt repräsentiert eine unveränderliche, partitionierte Sammlung von Elementen, die parallel in Spark verarbeitet werden können.

Es enthält auch Code, der zeigt, wie Daten mit dem von MLlib bereitgestellten `StandardScalar` für die Verwendung bei der linearen Regression mit dem stochastischen Gradientenverfahren (SGD), einem verbreiteten Algorithmus für das Training einer Vielzahl von Machine Learning-Modellen, skaliert werden. Der [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) wird zum Skalieren der Features auf Einheitenvarianz verwendet. Featureskalierung, auch bekannt als Datennormalisierung, stellt sicher, dass Features mit weit verteilten Werten keine übermäßige Gewichtung in der Zielfunktion erhalten. 

    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 11,72 Sekunden

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Bewerten mit dem logistischen Regressionsmodell und Speichern der Ausgabe im Blob
Der Code in diesem Abschnitt veranschaulicht das Laden eines in Azure Blob Storage gespeicherten logistischen Regressionsmodells und seine Verwendung zu der Vorhersage, ob ein Trinkgeld für eine Taxifahrt gezahlt wird, seine Bewertung mit standardisierten Klassifizierungsmetriken und anschließendes Zeichnen der Ergebnisse und Speichern in Blob Storage. Die bewerteten Ergebnisse werden in RDD-Objekten gespeichert. 

    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel

    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 19,22 Sekunden

## <a name="score-a-linear-regression-model"></a>Bewerten eines linearen Regressionsmodells
Wir verwendeten [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) zum Trainieren eines linearen Regressionsmodells mit dem stochastischen Gradientenverfahren (SGD) für die Optimierung der Vorhersage der gezahlten Trinkgeldbeträge. 

Der Code in diesem Abschnitt zeigt, wie ein lineares Regressionsmodell aus Azure Blob Storage geladen und mittels skalierter Variablen bewertet wird und dann die Ergebnisse wieder im Blob gespeichert werden.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 16,63 Sekunden

## <a name="score-classification-and-regression-random-forest-models"></a>Bewerten der Random Forest-Modelle für Klassifizierung und Regression
Der Code in diesem Abschnitt zeigt, wie die in Azure Blob Storage gespeicherten Random Forest-Modelle für Klassifizierung und Regression geladen werden, ihre Leistung mit Standardklassifizierungs- und Regressionsmaßnahmen bewertet wird und dann die Ergebnis wieder in Blob Storage gespeichert werden.

[Random Forests](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) sind Gruppen von Entscheidungsstrukturen.  In ihnen sind viele Entscheidungsstrukturen kombiniert, um das Risiko der Überanpassung zu verringern. Random Forest können kategorische Features behandeln, auf die Mehrklassenklassifizierung ausgedehnt werden, erfordern keine Featureskalierung und können Nichtlinearitäten und Funktionsinteraktionen erfassen. Random Forest zählen zu den erfolgreichsten Machine Learning-Modelle für Klassifizierung und Regression.

[spark.mllib](https://spark.apache.org/mllib/) unterstützt Random Forest für binäre und Mehrklassenklassifizierung sowie für Regression mit kontinuierlichen und kategorischen Features. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES    
    from pyspark.mllib.tree import RandomForest, RandomForestModel


    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 31,07 Sekunden

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Bewerten der Gradient-Boosted-Strukturmodelle für Klassifizierung und Regression
Der Code in diesem Abschnitt zeigt, wie die in Azure Blob Storage gespeicherten Gradient-Boosted-Strukturmodelle für Klassifizierung und Regression geladen werden, ihre Leistung mit Standardklassifizierungs- und Regressionsmaßnahmen bewertet wird und dann die Ergebnis wieder in Blob Storage gespeichert werden. 

**spark.mllib** unterstützt GBTs für binäre Klassifizierung sowie Regression mit kontinuierlichen und kategorischen Features. 

[Gradient-Boosted-Strukturen](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) sind Gruppen von Entscheidungsstrukturen. GBTs trainieren Entscheidungsstrukturen iterativ, um einen Funktionsverlust zu minimieren. GBTs können kategorische Features behandeln, erfordern keine Featureskalierung und können Nichtlinearitäten und Funktionsinteraktionen erfassen. Sie können auch in einer Mehrklassenklassifizierung verwendet werden.

    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)


    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)

    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**AUSGABE:**

Für die Ausführung der obigen Zelle benötigte Zeit: 14,6 Sekunden

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Bereinigen von Objekten aus dem Arbeitsspeicher und Drucken bewerteter Dateispeicherorte
    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**AUSGABE:**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016-05-0317_22_58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Verwenden von Spark-Modellen über eine Weboberfläche
Spark stellt einen Mechanismus zur Remoteübermittlung von Batchaufträgen oder interaktiven Abfragen über eine REST-Schnittstelle mit einer Komponente namens Livy bereit. Livy ist standardmäßig auf dem HDInsight Spark-Cluster aktiviert. Weitere Informationen zu Livy finden Sie hier: [Remoteübermittlung von Spark-Aufträgen mithilfe von Livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Livy können Sie zur Remoteübermittlung eines Auftrags verwenden, der eine Datei im Batch bewertet, die in einem Azure-Blob gespeichert ist, und dann die Ergebnisse in einen anderen Blob schreibt. Laden Sie zu diesem Zweck das Python-Skript von  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) in das Blob des Spark-Clusters hoch. Sie können zum Kopieren des Skripts in das Clusterblob ein Tool wie **Microsoft Azure Storage-Explorer** oder **AzCopy** verwenden. In unserem Fall haben wir das Skript in ***wasb:///example/python/ConsumeGBNYCReg.py*** hochgeladen.   

> [!NOTE]
> Die benötigten Zugriffsschlüssel finden Sie im Portal für das Speicherkonto, das dem Spark-Cluster zugeordnet ist. 
> 
> 

Nach dem Hochladen an diesen Speicherort wird dieses Skript innerhalb des Spark-Clusters in einem verteilten Kontext ausgeführt. Es lädt das Modell und führt Vorhersagen für Eingabedateien auf Grundlage des Modells aus.  

Sie können dieses Skript durch eine einfache HTTPS/REST-Anforderung von Livy remote aufrufen.  Hier ist ein Curl-Befehl zum Erstellen der HTTP-Anforderung, um das Python-Skript remote aufzurufen. Ersetzen Sie CLUSTERLOGIN, CLUSTERPASSWORD, CLUSTERNAME mit den entsprechenden Werten für den Spark-Cluster.

    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Sie können mit einem einfachen HTTPS-Anruf mit Standardauthentifizierung jede Sprache auf dem Remotesystem verwenden, um den Spark-Auftrag über Livy aufzurufen.   

> [!NOTE]
> Es wäre hilfreich, die Python Requests-Bibliothek bei der Ausführung dieses HTTP-Aufrufs zu verwenden, aber sie ist zurzeit nicht standardmäßig in Azure Functions installiert. Stattdessen werden darum ältere HTTP-Bibliotheken verwendet.   
> 
> 

Hier ist der Python-Code für den HTTP-Aufruf:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
    import httplib, urllib, base64

    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'

    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Sie können diesen Python-Code auch [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) hinzufügen, um eine Spark-Auftragsübermittlung auszulösen, die einen Blob basierend auf verschiedenen Ereignissen wie einem Timer, Erstellung oder Aktualisieren eines Blobs bewertet. 

Wenn Sie eine codefreie Kundenerfahrung bevorzugen, verwenden Sie [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) zum Aufrufen der Spark-Batchbewertung durch Definieren einer HTTP-Aktion für den **Logik-Apps-Designer** und Festlegung seiner Parameter. 

* Erstellen Sie im Azure-Portal eine neue Logik-App durch Auswählen von **+Neu** -> **Web und mobil** -> **Logik-App**. 
* Geben Sie den Namen der Logik-App und den App Service-Plan ein, um den **Logik-Apps-Designer** aufzurufen.
* Wählen Sie eine HTTP-Aktion, und geben Sie die Parameter wie in der folgenden Abbildung dargestellt ein:

![Designer für Logik-Apps](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Wie geht es weiter?
**Kreuzvalidierung und Hyperparameter-Sweeping**: Unter [Erweiterte Datendurchsuchung und Modellierung mit Spark](spark-advanced-data-exploration-modeling.md) erfahren Sie, wie Modelle mit Kreuzvalidierung und Hyperparameter-Sweeping trainiert werden können.

