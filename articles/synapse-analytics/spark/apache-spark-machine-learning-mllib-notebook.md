---
title: 'Tutorial: Erstellen einer Machine-Learning-App mit Apache Spark MLlib'
description: Ein Tutorial, wie Sie Apache Spark MLlib verwenden, um eine Machine Learning-App zu erstellen, die ein Dataset analysiert, indem eine Klassifizierung mittels logistischer Regression vorgenommen wird.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 50429696c4cbe10c4723f6d4bb9c9499d9b775c5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450426"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Tutorial: Erstellen einer Machine Learning-App mit Apache Spark MLlib und Azure Synapse Analytics

In diesem Artikel erfahren Sie, wie Sie mithilfe von Apache Spark [MLlib](https://spark.apache.org/mllib/) eine Machine-Learning-Anwendung erstellt, die eine einfache Vorhersageanalyse für ein Azure Open Dataset ausführt. Spark bietet integrierte Machine Learning-Bibliotheken. In diesem Beispiel wird eine *Klassifizierung* mittels logistischer Regression verwendet.

SparkML und MLLib sind Spark-Kernbibliotheken, die viele Hilfsprogramme enthalten, die nützlich für Aufgaben aus dem Bereich des Machine Learning sind. Darunter befinden sich auch Hilfsprogramme für folgende Aufgaben:

- Klassifizierung
- Regression
- Clustering
- Themenmodellierung
- Singulärwertzerlegung (Singular Value Decomposition, SVD) und Hauptkomponentenanalyse (Principal Component Analysis, PCA)
- Testen von Hypothesen und Berechnen von Beispielstatistiken

## <a name="understand-classification-and-logistic-regression"></a>Grundlegendes zu Klassifizierung und logistischer Regression

*Klassifizierung*, eine Aufgabe im Bereich des Machine Learning, ist der Prozess, bei dem Eingabedaten in Kategorien sortiert werden. Der Klassifizierungsalgorithmus hat die Aufgabe, herauszufinden, wie *Labels* den von Ihnen bereitgestellten Eingabedaten zugeordnet werden. So kann ein Machine-Learning-Algorithmus beispielsweise Börsendaten als Eingabe akzeptieren und die Daten in zwei Kategorien einteilen: Aktien, die Sie verkaufen sollten, und solche, die Sie behalten sollten.

*Logistische Regression* ist ein Algorithmus, den Sie für die Klassifizierung verwenden können. Die API für die logistische Regression von Spark ist nützlich für eine *binäre Klassifizierung* oder für die Klassifizierung der Eingabedaten in einer von zwei Gruppen. Weitere Informationen zur logistischen Regression finden Sie in [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Zusammenfassend gesagt, erzeugt der Prozess der logistischen Regression eine *logistische Funktion* , die verwendet werden kann, um die Wahrscheinlichkeit vorherzusagen, dass ein Eingabevektor zu einer Gruppe oder der anderen gehört.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Beispiel für eine Vorhersageanalyse mit NYC-Taxidaten

In diesem Beispiel verwenden Sie Spark, um ein paar Vorhersageanalysen zu Trinkgelddaten von Taxifahrten in New York durchzuführen. Die Daten sind über [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) verfügbar. Diese Teilmenge des Datasets enthält Informationen über Taxifahrten von Yellow Cabs, einschließlich Informationen zu jeder einzelnen Fahrt, den Start- und Endzeiten/-orten, den Kosten und andere interessante Attribute.

> [!IMPORTANT]
> Es können zusätzliche Gebühren für das Abrufen dieser Daten von ihrem Speicherort anfallen.

In den folgenden Schritten entwickeln Sie ein Modell, um vorherzusagen, ob eine bestimmte Fahrt ein Trinkgeld enthält oder nicht.

## <a name="create-an-apache-spark--machine-learning-model"></a>Erstellen eines Apache Spark-Machine Learning-Modells

1. Erstellen Sie ein Notebook unter Verwendung des PySpark-Kernels. Eine entsprechende Anleitung finden Sie unter [Erstellen eines Notebooks](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importieren Sie die Typen, die für diese Anwendung benötigt werden. Kopieren Sie den folgenden Code, fügen Sie ihn in eine leere Zelle ein, und drücken Sie dann **UMSCHALT+EINGABE**, oder führen Sie die Zelle aus, indem Sie das blaue Wiedergabesymbol links neben dem Code verwenden.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    Durch den PySpark-Kernel müssen Sie keine Kontexte explizit erstellen. Der Spark-Kontext wird automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen.

## <a name="construct-the-input-dataframe"></a>Erstellen des Eingabedatenrahmens

Da die Rohdaten im Parquet-Format vorliegen, können Sie den Spark-Kontext verwenden, um die Datei als Dataframe direkt in den Arbeitsspeicher zu lesen. Während der unten stehende Code die Standardoptionen verwendet, ist es möglich, bei Bedarf die Zuordnung von Datentypen und anderen Schemaattributen zu erzwingen.

1. Führen Sie die folgenden Zeilen aus, um einen Spark-Dataframe zu erstellen, indem Sie den Code in eine neue Zelle einfügen. Dadurch werden die Daten über die Open Datasets-API abgerufen. Das Abrufen aller dieser Daten generiert ungefähr 1,5 Milliarden Zeilen. Je nach Größe Ihres serverlosen Apache Spark-Pools können die Rohdaten zu umfangreich sein oder zu viel Verarbeitungszeit in Anspruch nehmen. Sie können diese Daten zu einer kleineren Menge filtern. Das folgende Codebeispiel verwendet „start_date“ und „end_date“, um einen Filter anzuwenden, der Daten für einen einzelnen Monat zurückgibt.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Der Nachteil eines einfachen Filtervorgangs besteht darin, dass aus statistischer Sicht dadurch ein Trend in die Daten eingeführt werden kann. Ein anderer Ansatz besteht darin, die in Spark integrierte Stichprobenentnahme zu verwenden. Der folgende Code verringert wird das Dataset bis auf ungefähr 2000 Zeilen, wenn er nach dem obigen Code angewendet wird. Dieser Stichprobenentnahmeschritt kann anstelle des einfachen Filters oder in Verbindung mit dem einfachen Filter verwendet werden.

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Es ist jetzt möglich, sich die Daten anzusehen, um herauszufinden, was gelesen wurde. Normalerweise ist es besser, Daten anhand einer Teilmenge anstelle der vollständigen Menge zu überprüfen, abhängig von der Größe des Datasets. Der folgende Code bietet zwei Möglichkeiten zum Anzeigen der Daten: die erste ist einfach, während die zweite eine wesentlich funktionsreichere Rastererfahrung bietet sowie die Funktion zum grafischen Visualisieren der Daten.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Abhängig von der Größe des generierten Datasets und ihrem Bedürfnis zum Experimentieren oder zum mehrfachen Ausführen des Notebooks, kann es ratsam sein, das Dataset lokal im Arbeitsbereich zwischenzuspeichern. Es gibt drei Methoden, um ein explizites Zwischenspeichern durchzuführen:

   - Lokales Speichern des Dataframes als Datei
   - Speichern des Dataframes als temporäre Tabelle oder Ansicht
   - Speichern des Dataframes als permanente Tabelle

Die ersten beiden Ansätze sind in den folgenden Codebeispielen enthalten.

Das Erstellen einer temporären Tabelle oder Ansicht bietet unterschiedliche Zugriffspfade auf die Daten, besteht aber nur für die Dauer der Spark-Instanzsitzung.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Vorbereiten der Daten

Die Daten im Rohformat sind häufig nicht für die direkte Übergabe an ein Modell geeignet. Eine Reihe von Aktionen muss an den Daten ausgeführt werden, um sie in einen Zustand zu versetzen, in dem das Modell sie verwenden kann.

Im folgenden Code werden vier Klassen von Vorgängen durchgeführt:

- Das Entfernen von Ausreißern/falschen Werten durch Filtern.
- Das Entfernen von Spalten, die nicht benötigt werden.
- Das Erstellen neuer Spalten, die von den Rohdaten abgeleitet werden, damit das Modell effektiver funktioniert, manchmal auch als „Merkmalserstellung“ bezeichnet.
- Bezeichnen („Labeln“) – Da Sie eine binäre Klassifizierung vornehmen (gibt es bei einer bestimmte Fahrt ein Trinkgeld oder nicht), muss der Trinkgeldbetrag in einen Wert von 0 oder 1 konvertiert werden.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Danach wird ein zweiter Durchlauf mit den Daten gemacht, um die endgültigen Merkmale hinzuzufügen.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Erstellen eines logistischen Regressionsmodells

Die letzte Aufgabe besteht darin, die Daten mit Label in ein Format zu konvertieren, das mit der logistische Regression analysiert werden kann. Die Eingabe für einen logistischen Regressionsalgorithmus muss ein Satz von *Bezeichnung-Merkmals-Vektorpaaren* (Label-Feature) sein, wobei der *Merkmalsvektor* aus Zahlen besteht, die den Eingabepunkt darstellen. Somit müssen wir die Kategoriespalten in Zahlen konvertieren. Die Spalten `trafficTimeBins` und `weekdayString` müssen in ganzzahlige Darstellungen konvertiert werden. Es gibt mehrere Ansätze zum Durchführen der Konvertierung, der in diesem Beispiel verwendete Ansatz ist jedoch *OneHotEncoding*, ein gängiger Ansatz.

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Diese Aktion führt zu einem neuen Dataframe mit allen Spalten im richtigen Format, um ein Modell zu trainieren.

## <a name="train-a-logistic-regression-model"></a>Trainieren eines logistischen Regressionsmodells

Die erste Aufgabe besteht darin, das Dataset in ein Trainingsdataset und ein Test- oder Validierungsdataset aufzuteilen. Die Aufteilung erfolgt hierbei beliebig, und Sie sollten mit unterschiedlichen Aufteilungseinstellungen experimentieren, um festzustellen, ob sie sich auf das Modell auswirken.

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nachdem nun zwei DataFrames vorhanden sind, besteht die nächste Aufgabe darin, die Modellformel zu erstellen, sie mit dem Trainings-DataFrame auszuführen und dann anhand des Test-DataFrames zu validieren. Sie sollten mit verschiedenen Versionen der Modellformel experimentieren, um die Auswirkungen verschiedener Kombinationen herauszufinden.

> [!Note]
> Sie benötigen in Azure die Azure-Rolle „Mitwirkender an Storage-Blobdaten“, um das Modell speichern zu können. Navigieren Sie unter Ihrem Speicherkonto zu „Zugriffssteuerung (IAM)“, und klicken Sie auf **Rollenzuweisung hinzufügen**. Weisen Sie Ihrem SQL-Datenbank-Server die Azure-Rolle „Mitwirkender an Storage-Blobdaten“ zu. Nur Mitglieder mit der Berechtigung „Besitzer“ können diesen Schritt ausführen. Verschiedene integrierte Azure-Rollen finden Sie in [diesem Leitfaden](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Die Ausgabe dieser Zelle ist:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Erstellen einer visuellen Darstellung der Vorhersage

Nun können Sie eine endgültige Visualisierung erstellen, um sich mit den Ergebnissen dieses Tests auseinanderzusetzen. Eine [ROC-Kurve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) ist eine Möglichkeit, um das Ergebnis zu überprüfen.

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![ROC-Kurve für logistisches Regressionsmodell für Trinkgelder](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Herunterfahren der Spark-Instanz

Nachdem Sie die Ausführung der Anwendung abgeschlossen haben, fahren Sie das Notebook herunter, um die Ressourcen freizugeben, indem Sie die Registerkarte schließen, oder wählen Sie unten im Notebook im Statusbereich **Sitzung beenden** aus.

## <a name="see-also"></a>Weitere Informationen

- [Übersicht: Apache Spark in Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Nächste Schritte

- [Dokumentation zu .NET für Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Offizielle Apache Spark-Dokumentation](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> In der offiziellen Apache Spark-Dokumentation wird teilweise die Spark-Konsole verwendet, diese ist in Azure Synapse Spark jedoch nicht verfügbar. Verwenden Sie stattdessen das [Notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) oder [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
