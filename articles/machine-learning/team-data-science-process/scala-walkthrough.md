---
title: Data Science unter Verwendung von Scala und Spark in Azure – Team Data Science-Prozess
description: Die Verwendung von Scala für überwachte Machine Learning-Aufgaben mit der skalierbaren Machine Learning-Bibliothek (MLlib) von Spark und Spark ML-Paketen auf einem Azure HDInsight Spark-Cluster.
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
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321370"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Data Science unter Verwendung von Scala und Spark in Azure
Dieser Artikel zeigt Ihnen die Verwendung von Scala für überwachte Machine Learning-Aufgaben mit der skalierbaren Machine Learning-Bibliothek (MLlib) von Spark und Spark ML-Paketen auf einem Azure HDInsight Spark-Cluster. Sie werden durch die Aufgaben geführt, aus denen der [Data Science-Prozess](./index.yml)besteht: Erfassen und Durchsuchen von Daten, Visualisierung, Featureentwicklung, Modellierung und Modellnutzung. Die im Artikel behandelten Modelle beinhalten logistische und lineare Regression, zufällige Gesamtstrukturen und Gradient-Boosted-Strukturen (Gradient-boosted Trees, GBTs) neben zwei häufig überwachten Machine Learning-Aufgaben:

* Regressionsproblem: Vorhersage des Trinkgeldbetrags ($) für eine Taxifahrt
* Binäre Klassifizierung: Vorhersage für eine Taxifahrt, ob Trinkgeld gegeben wird oder nicht (1/0)

Die Modellierung erfordert Training und Auswertung von Testdatasets und relevanten Genauigkeitsmetriken. In diesem Artikel erfahren Sie, wie diese Modelle in Azure Blob Storage gespeichert werden, und wie ihre Vorhersageleistung bewertet und ausgewertet wird. Dieser Artikel behandelt auch die fortgeschritteneren Themen, in denen es darum geht, Modelle mittels Kreuzvalidierung und Hyperparameter-Sweeping zu optimieren. Die verwendeten Daten sind eine Stichprobe des bei GitHub verfügbaren Datasets für Taxifahrten und Fahrpreise in New York aus dem Jahr 2013.

[Scala](https://www.scala-lang.org/), eine Sprache auf der Basis von Java Virtual Machine, integriert objektorientierte und funktionale Sprachkonzepte. Es ist eine skalierbare Sprache, die sich gut für die verteilte Verarbeitung in der Cloud eignet und auf Azure Spark-Clustern ausgeführt wird.

[Spark](https://spark.apache.org/) ist ein Open-Source-Framework für die Parallelverarbeitung, das die In-Memory-Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern. Die Spark-Verarbeitungs-Engine ist auf Geschwindigkeit, einfache Nutzung und anspruchsvolle Analysen ausgelegt. Dank seiner verteilten In-Memory-Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen bei Machine Learning und für Graphberechnungen. Das [spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) -Paket bietet eine einheitliche Gruppe von auf Dataframes aufgesetzten High-Level-APIs, mit deren Hilfe Sie praktische Machine Learning-Pipelines erstellen und optimieren können. [MLlib](https://spark.apache.org/mllib/) ist die skalierbare Machine Learning-Bibliothek von Spark, die Modellierungsfunktionen in diese verteilte Umgebung einbringt.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) ist das in Azure gehostete Angebot für Open-Source-Spark. HDInsight Spark unterstützt darüber hinaus Jupyter Scala-Notebooks im Spark-Cluster und kann interaktive Spark-SQL-Abfragen zum Transformieren, Filtern und Visualisieren von Daten ausführen, die in Azure Blob Storage gespeichert sind. Die Scala-Codeausschnitte in diesem Artikel, die die Lösungen bereitstellen und die relevanten Plots zum Visualisieren der Daten zeigen, werden in Jupyter-Notebooks ausgeführt, die in den Spark-Clustern installiert sind. Die Modellierungsschritte in diesen Themen enthalten auch Code zum Trainieren, Evaluieren, Speichern und Nutzen jedes Modelltyps.

Installationsschritte und Code in diesem Artikel sind für Azure HDInsight 3.4 Spark 1.6 vorgesehen. Der in diesem Artikel und im [Jupyter Scala-Notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) vorhandene Code ist jedoch generisch und sollte auf jedem Spark-Cluster funktionieren. Wenn Sie nicht HDInsight Spark verwenden, weichen Clustereinrichtung und Verwaltungsschritte möglicherweise geringfügig von dem ab, was in diesem Artikel gezeigt wird.

> [!NOTE]
> Ein Thema, das die Verwendung von Python statt Scala zur Ausführung von Aufgaben für einen End-to-End-Data Science-Process veranschaulicht, ist [Übersicht über Data Science mit Spark in Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
* Sie benötigen ein Azure-Abonnement. Wenn Sie noch keins besitzen, [beschaffen Sie sich eine kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Sie benötigen einen Azure HDInsight 3.4 Spark 1.6-Cluster, um die folgenden Schritte auszuführen. Anweisungen zum Erstellen eines Clusters finden Sie unter [Erste Schritte: Erstellen von Apache Spark in Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Geben Sie Clustertyp und Version im Menü **Clustertyp auswählen** an.

![Konfiguration des HDInsight-Clustertyps](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

Eine Beschreibung der NYC-Taxidaten und Anweisungen zum Ausführen von Code aus einem Jupyter-Notebook im Spark-Cluster finden Sie in den entsprechenden Abschnitten von [Übersicht zu Data Science unter Verwendung von Spark unter Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Ausführen von Scala-Code über ein Jupyter-Notebook auf dem Spark-Cluster
Sie können ein Jupyter-Notebook über das Azure-Portal starten. Suchen Sie den Spark-Cluster auf Ihrem Dashboard, und klicken Sie darauf, um zur Verwaltungsseite für Ihren Cluster zu gelangen. Klicken Sie dann auf **Cluster-Dashboards** und **Jupyter-Notebook** , um das dem Spark-Cluster zugeordnete Notebook zu öffnen.

![Cluster-Dashboard und Jupyter-Notebooks](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Sie können auch unter https://&lt;clustername&gt;.azurehdinsight.net/jupyter. auf Jupyter Notebooks zuzugreifen. Ersetzen Sie *clustername* durch den Namen Ihres Clusters. Sie benötigen das Kennwort für Ihr Administratorkonto, um auf die Jupyter-Notebooks zuzugreifen.

![Wechseln zu Jupyter-Notebooks mit dem Namen des Clusters](./media/scala-walkthrough/spark-jupyter-notebook.png)

Wählen Sie **Scala** , um ein Verzeichnis mit ein paar Beispielen für vorkonfigurierte Notebooks anzuzeigen, die die PySpark-API verwenden. Durchsuchen, Modellierung und Bewertung mit dem Scala.ipynb-Notebook, das die Codebeispiele für diese Suite von Spark-Themen enthält, ist auf [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala)verfügbar.

Sie können das Notebook direkt von GitHub auf den Jupyter-Notebook-Server in Ihrem Spark-Cluster hochladen. Klicken Sie auf der Jupyter-Startseite auf die **Hochladen** -Schaltfläche. Fügen Sie im Datei-Explorer die GitHub-URL (Rohdateninhalt) des Scala-Notebooks ein, und klicken Sie auf **Öffnen**. Das Scala-Notebook ist unter folgender URL verfügbar:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Setup: Voreinstellung von Spark- und Hive-Kontext, Spark-Magic-Befehle und Spark-Bibliotheken
### <a name="preset-spark-and-hive-contexts"></a>Voreinstellung von Spark- und Hive-Kontext

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

Die Kontexte der mit Jupyter-Notebooks bereitgestellten Spark-Kernel sind voreingestellt. Sie müssen nicht ausdrücklich den Spark- oder Hive-Kontext festlegen, um mit der Anwendung, die Sie entwickeln, arbeiten zu können. Die voreingestellten Kontexte sind:

* `sc` für Spark-Kontext
* `sqlContext` für Hive-Kontext

### <a name="spark-magics"></a>Spark-Magics
Der Spark-Kernel bietet einige so genannte „Magic-Befehle“, die vordefiniert sind. Dies sind spezielle Befehle, die Sie mit `%%` aufrufen können. Zwei dieser Befehle werden in den folgenden Codebeispielen verwendet.

* `%%local` gibt an, dass der Code in den nachfolgenden Zeilen lokal ausgeführt wird. Der Code muss gültiger Scala-Code sein.
* `%%sql -o <variable name>` führt eine Hive-Abfrage für `sqlContext` aus. Wenn der Parameter `-o` übergeben wird, wird das Ergebnis der Abfrage im `%%local`-Scala-Kontext als Spark-Dataframe beibehalten.

Weitere Informationen zu den Kernels für Jupyter-Notebooks und den zugehörigen vordefinierten „Magics“, die Sie mit `%%` aufrufen (z.B. `%%local`), finden Sie unter [Verfügbare Kernels für Jupyter-Notebooks mit HDInsight Spark-Linux-Clustern in HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importieren von Bibliotheken
Importieren Sie die Spark-, MLlib- und anderen erforderlichen Bibliotheken mit folgendem Code.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Datenerfassung
Der erste Schritt im Data Science-Prozess ist die Erfassung der Daten, die Sie analysieren möchten. Sie bringen die Daten aus externen Quellen oder Systemen in Ihre Umgebung zum Durchsuchen und Modellieren von Daten ein. In diesem Artikel handelt es sich bei den von Ihnen erfassten Daten um ein verknüpftes 0,1%-Beispiel der Datei für Taxifahrten- und Fahrpreise (gespeichert als TSV-Datei). Die Umgebung zum Durchsuchen und Modellieren der Daten ist Spark. Dieser Abschnitt enthält den Code zur Ausführung der folgenden Reihe von Aufgaben:

1. Legen Sie die Verzeichnispfade für Daten- und Modellspeicherung fest.
2. Lesen Sie das Eingabedataset ein (gespeichert als TSV-Datei).
3. Definieren Sie ein Schema für die Daten, und bereinigen Sie die Daten.
4. Erstellen Sie einen bereinigten Dataframe, und speichern Sie ihn im Arbeitsspeicher zwischen.
5. Registrieren Sie die Daten als temporäre Tabelle in SQLContext.
6. Fragen Sie die Tabelle ab, und importieren Sie die Ergebnisse in einen Dataframe.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Festlegen von Verzeichnispfaden für Speicherorte in Azure Blob Storage
Spark kann aus Azure Blob Storage lesen und darin schreiben. Sie können mit Spark beliebige Ihrer vorhandenen Daten verarbeiten und dann die Ergebnisse wieder im Blobspeicher speichern.

Um Modelle oder Dateien im Blobspeicher zu speichern, müssen Sie den Pfad ordnungsgemäß angeben. Verweisen Sie mit einem Pfad, der mit `wasb:///`beginnt, auf den an den Spark-Cluster angefügten Standardcontainer. Verweisen Sie auf andere Speicherorte mit `wasb://`.

Das folgende Codebeispiel gibt den Speicherort der zu lesenden Eingabedaten und den Pfad zum Blobspeicher an, der dem Spark-Cluster zugeordnet ist, in dem das Modell gespeichert wird.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importieren von Daten, Erstellen von RDDs (Resilient Distributed Datasets) und Definieren eines Dataframes gemäß Schema

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Ausgabe:**

Zeit zum Ausführen der Zelle: 8 Sekunden.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Abfragen der Tabelle und Importieren der Ergebnisse in einen Dataframe
Fragen Sie als Nächstes die Tabelle nach Fahrpreis-, Fahrgast- und Trinkgelddaten ab; filtern Sie beschädigte und außerhalb liegende Daten aus, und drucken Sie mehrere Zeilen.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Ausgabe:**

| fare_amount | passenger_count | tip_amount | tipped |
| --- | --- | --- | --- |
|        13,5 |1.0 |2,9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10,5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Durchsuchen und Visualisieren von Daten
Nachdem Sie die Daten in Spark eingebracht haben, besteht der nächste Schritt im Data Science-Prozess darin, durch Durchsuchen und Visualisieren eine tiefer gehende Einsicht in die Daten zu erhalten. In diesem Abschnitt untersuchen Sie die Taxi-Daten mithilfe von SQL-Abfragen. Importieren Sie dann die Ergebnisse in einen Dataframe, um die Zielvariablen und künftigen Features für die Sichtprüfung mithilfe des Jupyter-Features zur automatischen Visualisierung zu zeichnen.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Verwenden von lokalen und SQL-Magic-Befehlen, um Daten zu zeichnen
Standardmäßig ist die Ausgabe von allen Codeausschnitten, die Sie über ein Jupyter-Notebook ausführen, innerhalb des Kontexts der Sitzung verfügbar, die auf Workerknoten beibehalten wird. Wenn Sie eine Fahrt für jede Berechnung auf den Workerknoten speichern möchten, und wenn alle Daten, die Sie zur Berechnung benötigen, lokal auf dem Jupyter-Serverknoten (dem Hauptknoten) verfügbar sind, können Sie den Codeausschnitt mit dem Magic-Befehl `%%local` auf dem Jupyter-Server ausführen.

* **SQL Magic** (`%%sql`). Der HDInsight Spark-Kernel unterstützt einfache HiveQL-Inlineabfragen für den SQLContext. Mit dem Argument (`-o VARIABLE_NAME`) wird die Ausgabe der SQL-Abfrage als Pandas-Dataframe auf dem Jupyter-Server beibehalten. Mit dieser Einstellung wird die Ausgabe im lokalen Modus verfügbar gemacht.
* `%%local` **magic** : Der Magic-Befehl `%%local` führt den Code lokal auf dem Jupyter-Server aus – dem Hauptknoten des HDInsight-Clusters. Normalerweise verwenden Sie den Magic-Befehl `%%local` zusammen mit dem Magic-Befehl `%%sql` mit dem Parameter `-o`. Mit dem Parameter `-o` wird die Ausgabe der SQL-Abfrage lokal beibehalten. Anschließend löst der Magic-Befehl `%%local` die nächste Gruppe von Codeausschnitten aus, damit diese lokal für die Ausgabe der lokal gespeicherten SQL-Abfragen ausgeführt werden können.

### <a name="query-the-data-by-using-sql"></a>Abfragen der Daten mit SQL
Diese Abfrage ruft die Taxifahrten nach Höhe des Fahrpreises, Anzahl der Fahrgäste und Trinkgeldbetrag ab.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

Im folgenden Code erstellt der Magic-Befehl `%%local` einen lokalen Dataframe, sqlResults. Sie können sqlResults verwenden, um mit Matplotlib zu zeichnen.

> [!TIP]
> Der Local-Magic-Befehl wird in diesem Artikel mehrfach genutzt. Verwenden Sie bei einem großen Dataset Stichproben, um einen Dataframe zu erstellen, der nicht zu groß für den lokalen Arbeitsspeicher ist.
> 
> 

### <a name="plot-the-data"></a>Zeichnen der Daten
Sie können mit Python-Code zeichnen, sobald der Dataframe im lokalen Kontext als Pandas-Dataframe vorhanden ist.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 Der Spark-Kernel visualisiert automatisch die Ausgabe der SQL-Abfragen (HiveQL), nachdem Sie den Code ausgeführt haben. Sie können zwischen verschiedenen Visualisierungstypen wählen:

* Tabelle
* Kreisdiagramm
* Zeile
* Bereich
* Balkendiagramm

Hier ist der Code zum Zeichnen der Daten:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Ausgabe:**

![Trinkgeldbetrag-Histogramm](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Trinkgeldbetrag nach Anzahl der Fahrgäste](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Trinkgeldbetrag nach Höhe des Fahrpreises](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Erstellen und Transformieren von Features und anschließendes Vorbereiten von Daten für die Eingabe in Modellierungsfunktionen
Für strukturbasierte Modellierungsfunktionen von Spark ML und MLlib müssen Sie Ziel und Features mithilfe einer Vielzahl geeigneter Techniken wie Diskretisierung, Indizierung, One-Hot-Codierung und Vektorisierung vorbereiten. Diese Verfahren durchlaufen wir in diesem Abschnitt:

1. Erstellen eines neuen Features durch **Diskretisieren** von Stunden in Verkehrszeitbuckets.
2. Anwenden von **Indizieren und One-Hot-Codieren** auf kategorische Features.
3. **Stichprobenerfassung in Dataset und Teilen** in Trainings- und Testteile.
4. **Festlegen von Trainingsvariablen und Features** und dann Erstellen indizierter oder One-Hot-codierter RDDs (Resilient Distributed Datasets) mit bezeichnetem Punkt oder Dataframes für Training und Testeingabe.
5. Automatisches **Kategorisieren und Vektorisieren von Features und Zielen** für die Verwendung als Eingaben für ML-Modelle.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Erstellen eines neuen Features durch Diskretisieren von Stunden in Verkehrszeitbuckets
Dieser Code zeigt, wie Sie ein neues Feature durch Diskretisieren von Stunden in Verkehrszeitbuckets erstellen und den resultierenden Dataframe im Arbeitsspeicher zwischenspeichern. Wo RDDs und Dataframes wiederholt verwendet werden, führt das Zwischenspeichern zu verbesserten Ausführungszeiten. Folglich werden Sie RDDs und Dataframes in den folgenden Verfahren in mehreren Phasen zwischenspeichern.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indizieren und One-Hot-Codieren von kategorischen Features
Die Modellierungs- und Vorhersagefunktionen von MLlib erfordern, dass Features mit kategorischen Eingabedaten vor der Verwendung indiziert oder codiert werden. Dieser Abschnitt zeigt Ihnen das Indizieren oder Codieren von kategorischen Features für die Eingabe in die Modellierungsfunktionen.

Indizieren oder Codieren kann von Modell zu Modell unterschiedlich sein. Beispielsweise erfordern logistische und lineare Regressionsmodelle One-hot-Codierung. Beispielsweise kann ein Feature mit drei Kategorien auf drei Featurespalten ausgedehnt werden. Jede Spalte enthält abhängig von der Kategorie einer Beobachtung 0 oder 1. MLlib bietet die Funktion [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) zur One-Hot-Codierung. Dieser Encoder ordnet eine Spalte mit Bezeichnungsindizes einer Spalte mit binären Vektoren mit höchstens einem einzigen Wert zu. Mit dieser Codierung können Algorithmen, die Features mit numerischen Werten erwarten, z.B. logistische Regression, auf kategorische Features angewandt werden.

Hier wandeln Sie nur vier Variablen um, um Beispiele zu zeigen, die Zeichenfolgen sind. Sie können auch andere Variablen, z.B. den Wochentag, die durch numerische Werte dargestellt werden, als kategorische Variablen indizieren.

Verwenden Sie für die Indizierung `StringIndexer()`- und für die One-Hot-Codierung `OneHotEncoder()`-Funktionen von MLlib. Hier ist der Code zum Indizieren und Codieren von kategorischen Features:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Ausgabe:**

Zeit zum Ausführen der Zelle: 4 Sekunden.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Stichprobenerfassung in Dataset und Teilen in Trainings- und Testteile
Dieser Code erstellt eine zufällige Stichprobe der Daten (in diesem Beispiel 25%). Obwohl es aufgrund der Größe des Datasets in diesem Beispiel nicht erforderlich ist, zeigt Ihnen der Artikel, wie Sie Stichproben erfassen können, damit Sie dieses Verfahren bei Bedarf zur Lösung eigener Probleme verwenden können. Bei großen Stichproben können Sie so beim Trainieren von Modellen deutlich Zeit sparen. Teilen Sie die Stichprobe als Nächstes zur Klassifizierung und Regressionsmodellierung in einen Trainingsteil (in diesem Beispiel 75%) und einen Testteil (in diesem Beispiel 25%) ein.

Fügen Sie eine Zufallszahl (zwischen 0 und 1) in jede Zeile (in einer „rand“-Spalte) ein, die zur Auswahl der Kreuzvalidierungsteilmengen während des Trainings verwendet werden kann.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Ausgabe:**

Zeit zum Ausführen der Zelle: 2 Sekunden.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Festlegen von Trainingsvariablen und Features und dann Erstellen indizierter oder One-Hot-codierter RDDs mit bezeichnetem Punkt oder Dataframes für Training und Testeingabe
Dieser Abschnitt enthält Code, der zeigt, wie Sie kategorische Textdaten als bezeichneten Punktdatentyp indizieren und so codieren, dass Sie sie zum Trainieren und Testen logistischer Regression gemäß MLlib und anderer Klassifizierungsmodelle verwenden können. Bezeichnete Punktobjekte sind RDDs, die in einer Weise formatiert sind, die von den meisten Machine Learning-Algorithmen in MLlib als Eingabedaten benötigt wird. Ein [bezeichneter Punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) ist ein lokaler Vektor, entweder dicht oder platzsparend, der mit einer Bezeichnung/Antwort verknüpft ist.

In diesem Code geben Sie die Zielvariable (zielabhängige Variable) und die Features ein, die zum Trainieren der Modelle verwendet werden. Dann erstellen Sie indizierte oder One-Hot-codierte RDDs mit bezeichnetem Punkt oder Dataframes für Training und Testeingabe.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Ausgabe:**

Zeit zum Ausführen der Zelle: 4 Sekunden.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Automatisches Kategorisieren und Vektorisieren von Features und Zielen für die Verwendung als Eingaben für Machine Learning-Modelle
Kategorisieren Sie Ziel und Features für die Verwendung in strukturbasierten Modellierungsfunktionen mit Spark ML. Der Code führt zwei Aufgaben aus:

* Erstellen eines binären Ziels für die Klassifizierung durch Zuweisung eines Werts von 0 oder 1 zu jedem Datenpunkt zwischen 0 und 1 unter Verwendung eines Schwellenwerts von 0,5.
* Automatisches Kategorisieren von Features. Wenn die Anzahl unterschiedlicher numerischer Werte für ein beliebiges Feature unter 32 liegt, dann ist dieses Feature kategorisiert.

Hier ist der Code für diese beiden Aufgaben.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binäres Klassifizierungsmodell: Vorhersage, ob ein Trinkgeld gezahlt wird
In diesem Abschnitt erstellen Sie drei Arten von Modellen der binären Klassifizierung, um vorherzusagen, ob ein Trinkgeld gezahlt wird oder nicht:

* Ein **logistisches Regressionsmodell** mithilfe der `LogisticRegression()`-Funktion von Spark ML
* Ein **zufälliges Gesamtstrukturmodell** mithilfe der `RandomForestClassifier()`-Funktion von Spark ML
* Ein **Gradient-Boosted-Struktur-Klassifizierungsmodell** mithilfe der `GradientBoostedTrees()`-Funktion von MLlib

### <a name="create-a-logistic-regression-model"></a>Erstellen eines logistischen Regressionsmodells
Erstellen Sie als Nächstes ein logistisches Regressionsmodell mithilfe der `LogisticRegression()` -Funktion von Spark ML. Sie erstellen den Modellerstellungscode in einer Reihe von Schritten:

1. **Trainieren der Modelldaten** mit einem Parametersatz.
2. **Modellevaluierung** anhand eines Testdatasets mit Metriken.
3. **Speichern des Modells** im Blobspeicher für die zukünftige Nutzung.
4. **Bewerten des Modells** anhand von Testdaten.
5. **Zeichnen der Ergebnisse** mit Grenzwertoptimierungskurven (Receiver Operating Characteristic, ROC).

Hier ist der Code für diese beiden Verfahren:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Laden, bewerten und speichern Sie die Ergebnisse.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Ausgabe:**

ROC für Testdaten = 0,9827381497557599

Verwenden Sie Python auf lokalen Pandas-Dataframes, um die ROC-Kurve zu zeichnen.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
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

**Ausgabe:**

![„Trinkgeld-oder-nicht“-ROC-Kurve](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Erstellen eines zufälligen Gesamtstrukturmodells
Erstellen Sie als Nächstes ein zufälliges Gesamtstrukturmodell mit der `RandomForestClassifier()` -Funktion von Spark ML, und werten Sie das Modell anhand von Testdaten aus.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Ausgabe:**

ROC für Testdaten = 0,9847103571552683

### <a name="create-a-gbt-classification-model"></a>Erstellen eines GBT-Klassifizierungsmodells
Erstellen Sie als Nächstes ein GBT-Klassifizierungsmodell mit der `GradientBoostedTrees()` -Funktion von MLlib, und werten Sie das Modell anhand von Testdaten aus.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Ausgabe:**

Bereich unter ROC-Kurve = 0,9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressionsmodell: Vorhersage des Trinkgeldbetrags
In diesem Abschnitt erstellen Sie zwei Arten von Regressionsmodellen, um den Trinkgeldbetrag vorherzusagen:

* Ein **normalisiertes lineares Regressionsmodell** mithilfe der `LinearRegression()`-Funktion von Spark ML. Sie speichern das Modell und evaluieren das Modell anhand von Testdaten.
* Ein **Gradient-Boosted-Struktur-Regressionsmodell** mit der `GBTRegressor()`-Funktion von Spark ML.

### <a name="create-a-regularized-linear-regression-model"></a>Erstellen eines normalisierten linearen Regressionsmodells

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Ausgabe:**

Zeit zum Ausführen der Zelle: 13 Sekunden.

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Ausgabe:**

R-sqr. für Testdaten = 0,5960320470835743

Als Nächstes fragen Sie die Testergebnisse als Dataframe ab, und stellen Sie sie mit AutoVizWidget und Matplotlib visuell dar.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Der Code erstellt aus der Ausgabe der Abfrage einen lokalen Dataframe und zeichnet die Daten. Der Magic-Befehl `%%local` erstellt einen lokalen Dataframe, `sqlResults`, den Sie für das Plotten mit Matplotlib verwenden können.

> [!NOTE]
> Dieser Spark-Magic-Befehl wird in diesem Artikel mehrfach genutzt. Bei einer großen Datenmenge sollten Sie Stichproben verwenden, um einen Dataframe zu erstellen, der nicht zu groß für den lokalen Arbeitsspeicher ist.
> 
> 

Erstellen Sie Plots mit Matplotlib von Python.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Ausgabe:**

![Trinkgeldbetrag: „Tatsächlich“ verglichen mit „Vorhergesagt“](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Erstellen eines GBT-Regressionsmodells
Erstellen Sie als Nächstes ein GBT-Regressionsmodell mit der `GBTRegressor()` -Funktion von Spark ML, und werten Sie das Modell anhand von Testdaten aus.

[Gradient-Boosted-Strukturen](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) sind Gruppen von Entscheidungsstrukturen. GBTS trainieren Entscheidungsstrukturen iterativ, um eine Verlustfunktion zu minimieren. Sie können GBTS für die Regression und die Klassifizierung verwenden. Sie können kategorische Features behandeln, erfordern keine Featureskalierung und können Nichtlinearitäten und Featureinteraktionen erfassen. Sie können sie auch in einer Mehrklassenklassifizierung verwendet werden.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Ausgabe:**

Test-R-sqr. ist: 0,7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Erweiterte Modellierungshilfsprogramme zur Optimierung
In diesem Abschnitt verwenden Sie Machine Learning-Hilfsprogramme, die Entwickler häufig zur Modelloptimierung einsetzen. Insbesondere können Sie Machine Learning-Modelle auf drei verschiedene Arten mit Parameter-Sweeping und Kreuzvalidierung optimieren:

* Aufteilen der Daten in Trainings- und Validierungssets, Optimieren des Modells mit Hyperparameter-Sweeping eines Trainingssets und Auswertung eines Validierungssets (lineare Regression)
* Optimieren des Modells mit Kreuzvalidierung und Hyperparameter-Sweeping mithilfe der CrossValidator-Funktion von Spark ML (binäre Klassifizierung)
* Optimieren des Modells mit benutzerdefinierter Kreuzvalidierung und Parameter-Sweepingcode zur Nutzung beliebiger Machine Learning-Funktionen und Parametersätze (lineare Regression)

**Kreuzvalidierung** ist eine Technik, mit der bewertet wird, wie gut ein Modell, das mit einem bekannten Dataset trainiert wurde, verallgemeinern kann, um die Features von Datasets, mit denen es nicht trainiert wurde, vorherzusagen. Der Grundgedanke hinter dieser Technik ist, dass ein Modell mit bekannten Daten trainiert und die Genauigkeit seiner Vorhersagen dann mit einem unabhängigen Dataset geprüft wird. Eine gängige Implementierung besteht darin, den Dataset *k* -fach aufzuteilen und das Modell daraufhin im Roundrobin-Verfahren mit allen Teilmengen außer einer zu trainieren.

Die **Hyperparameteroptimierung** besteht in dem Problem, einen Hyperparametersatz für einen Lernalgorithmus auszuwählen, in der Regel um eine Leistungskennzahl des Algorithmus in Bezug auf einen unabhängigen Dataset zu optimieren. Ein Hyperparameter ist ein Wert, den Sie außerhalb des Modelltrainingsverfahrens angeben müssen. Annahmen über Hyperparameterwerte können sich auf die Flexibilität und Genauigkeit des Modells auswirken. Entscheidungsstrukturen umfassen Hyperparameter, wie z.B. die gewünschte Tiefe und Anzahl der Blätter in der Struktur. Sie müssen Bedingungen für Abzüge bei Fehlklassifikationen für eine Support Vector Machine (SVM) festlegen.

Eine hier häufig verwendete Methode für die Hyperparameteroptimierung ist die auch als **Parameter-Sweeping** bezeichnete Rastersuche. Bei einer Rastersuche werden die Werte einer bestimmten Teilmenge des Hyperparameterraums für einen Lernalgorithmus umfassend durchsucht. Die Kreuzvalidierung kann eine Leistungsmetrik liefern, um die besten Ergebnisse des Rastersuchalgorithmus auszusortieren. Wenn Sie Kreuzvalidierungs-Hyperparameter-Sweeping verwenden, können Sie Probleme wie Überanpassung eines Modell an Trainingsdaten vermindern. So ist das Modell weiterhin für die allgemeine Datenmenge gültig, aus der die Trainingsdaten extrahiert wurden.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimieren eines linearen Regressionsmodells mit Hyperparameter-Sweeping
Teilen Sie die Daten als Nächstes in Trainings- und Validierungssets ein, wenden Sie Hyperparameter-Sweeping auf ein Trainingsset an, um das Modell zu optimieren, und werten Sie ein Validierungsset aus (lineare Regression).

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Ausgabe:**

Test-R-sqr. ist: 0,6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimieren des binären Klassifizierungsmodells mit Kreuzvalidierung und Hyperparameter-Sweeping
Dieser Abschnitt zeigt Ihnen das Optimieren des binären Klassifizierungsmodells mit Kreuzvalidierung und Hyperparameter-Sweeping. Hierzu wird die `CrossValidator` -Funktion von Spark ML verwendet.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Ausgabe:**

Zeit zum Ausführen der Zelle: 33 Sekunden.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimieren des linearen Regressionsmodells mithilfe benutzerdefinierter Kreuzvalidierung und Parameter-Sweepingcode
Optimieren Sie als Nächstes das Modell mit benutzerdefiniertem Code, und identifizieren Sie die besten Modellparameter mit dem Kriterium der höchste Genauigkeit. Erstellen Sie dann das finale Modell, werten Sie es anhand von Testdaten aus, und speichern Sie es im Blobspeicher. Laden Sie schließlich das Modell, bewerten Sie die Testdaten, und werten Sie die Genauigkeit aus.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Ausgabe:**

Zeit zum Ausführen der Zelle: 61 Sekunden.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Automatische Nutzung mit Spark erstellter Machine Learning-Modelle mit Scala
Eine Übersicht zu Themen, die Sie durch die Aufgaben führen, die den Data Science-Prozess in Azure umfassen, finden Sie unter [Team Data Science-Prozess (TDSP)](./index.yml).

[Exemplarische Vorgehensweisen für den Team Data Science-Prozess](walkthroughs.md) beschreibt andere exemplarische End-to-End-Vorgehensweisen, in denen die Schritte im Team Data Science-Prozess für bestimmte Szenarien veranschaulicht werden. Die exemplarischen Vorgehensweisen zeigen auch, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden.

[Bewerten von Machine Learning-Modellen, die mit Spark erstellt wurden](spark-model-consumption.md) zeigt Ihnen, wie Sie Scala-Code verwenden, um mit in Spark erstellten und in Azure Blob Storage gespeicherten Machine Learning-Modellen automatisch neue Datasets zu laden und zu bewerten. Sie können die dortigen Anweisungen befolgen und für eine automatisierte Nutzung einfach den Python-Code durch den Scala-Code in diesem Artikel ersetzen.