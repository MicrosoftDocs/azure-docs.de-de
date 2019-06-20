---
title: Erstellen einer Apache Spark-Machine Learning-Pipeline – Azure HDInsight
description: Verwenden Sie die Apache Spark-Machine Learning-Bibliothek zum Erstellen von Datenpipelines.
ms.service: hdinsight
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: c539460177a0a85938b886d161803e1fdf0e9e68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730195"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Erstellen einer Apache Spark-Machine Learning-Pipeline

Die skalierbare Machine Learning-Bibliothek von Apache Spark (MLlib) bringt Modellierungsfunktionen in eine verteilte Umgebung ein. Das Spark-Paket [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) umfasst eine Gruppe von auf Dataframes aufbauenden APIs auf höherer Ebene. Mit diesen APIs können Sie praktische Machine Learning-Pipelines erstellen und optimieren.  *Spark-Machine Learning* bezieht sich auf diese Dataframe-basierte MLlib-API und nicht auf die ältere RDD-basierte Pipeline-API.

Bei einer Machine Learning-Pipeline (ML-Pipeline) handelt es sich um einen vollständigen Workflow, der mehrere Algorithmen für maschinelles Lernen miteinander kombiniert. Zum Verarbeiten und Lernen von Daten können viele Schritte und eine Folge von Algorithmen erforderlich sein. Pipelines definieren die Phasen und die Reihenfolge eines Prozesses für maschinelles Lernen. In MLlib werden Phasen einer Pipeline durch eine bestimmte Abfolge von Pipelinephasen dargestellt, wobei ein Transformator und ein Estimator jeweils Aufgaben ausführen.

Ein Transformator ist ein Algorithmus, der unter Verwendung der `transform()`-Methode einen Dataframe in einen anderen Dataframe transformiert. Beispielsweise kann ein Featuretransformator eine Spalte eines Dataframes lesen, diese einer anderen Spalte zuordnen und einen neuen Dataframe mit der angehängten zugeordneten Spalte ausgeben.

Ein Estimator ist eine Abstraktion von Lernalgorithmen und dient der Anpassung oder dem Training für ein Dataset zum Generieren eines Transformators. Ein Estimator implementiert eine Methode mit dem Namen `fit()`, die einen Dataframe akzeptiert und einen Dataframe generiert, der ein Transformator ist.

Jeder zustandslosen Instanz eines Transformators oder Estimators ist ein eigener eindeutiger Bezeichner zugewiesen, der beim Angeben von Parametern verwendet wird. Beide verwenden eine einheitliche API zur Angabe dieser Parameter.

## <a name="pipeline-example"></a>Pipelinebeispiel

Zur Veranschaulichung der praktischen Verwendung einer ML-Pipeline verwendet dieses Beispiel die Datendatei `HVAC.csv`, die bereits in den Standardspeicher (Azure Storage oder Data Lake Storage) für Ihren HDInsight-Cluster geladen wurde. Zum Anzeigen des Inhalts der Datei navigieren Sie zum Verzeichnis `/HdiSamples/HdiSamples/SensorSampleData/hvac`. `HVAC.csv` enthält einen Satz von Zeiten mit den Zieltemperaturen und den tatsächlichen Temperaturen für HVAC-Systeme (*Heating, Ventilation, Air Conditioning*) in verschiedenen Gebäuden. Ziel ist das Trainieren des Modells für die Daten und das Generieren einer Temperaturprognose für ein bestimmtes Gebäude.

Der folgende Code:

1. Definiert ein `LabeledDocument`, in dem die `BuildingID`, die `SystemInfo` (Bezeichner eines Systems und Alter) und ein `label` (1.0, wenn das Gebäude überhitzt ist, andernfalls 0.0) gespeichert sind.
2. Erstellt die benutzerdefinierte Parserfunktion `parseDocument`, die eine Datenzeile abruft und ermittelt, ob das Gebäude warm („hot“) ist, indem die Zieltemperatur mit der tatsächlichen Temperatur verglichen wird.
3. Wendet den Parser beim Extrahieren der Quelldaten an.
4. Erstellt Trainingsdaten.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument
def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0        

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)

# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Diese Beispielpipeline umfasst drei Phasen: `Tokenizer` und `HashingTF` (beide Transformatoren) und `Logistic Regression` (Estimator).  Die extrahierten und analysierten Daten im `training`-Dataframe durchlaufen die Pipeline, wenn `pipeline.fit(training)` aufgerufen wird.

1. In der ersten Phase `Tokenizer` wird die Eingabespalte `SystemInfo` (bestehend aus dem Systembezeichner und den Alterswerten) in die Ausgabespalte `words` geteilt. Diese neue Spalte `words` wird dem Dataframe hinzugefügt. 
2. In der zweiten Phase `HashingTF` wird die neue Spalte `words` in Funktionsvektoren umgewandelt. Diese neue Spalte `features` wird dem Dataframe hinzugefügt. Bei diesen ersten beiden Phasen handelt es sich um Transformatoren. 
3. Bei der dritten Phase `LogisticRegression` handelt es sich um einen Estimator, daher ruft die Pipeline die `LogisticRegression.fit()`-Methode auf, um ein `LogisticRegressionModel` zu generieren. 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Um die neuen von den Transformatoren `Tokenizer` und `HashingTF` hinzugefügten Spalten `words` und `features` sowie ein Beispiel für den Estimator `LogisticRegression` anzuzeigen, führen Sie eine `PipelineModel.transform()`-Methode für den ursprünglichen Dataframe aus. Im Produktionscode ist der nächste Schritt die Übergabe in einen Testdataframe, um das Training zu überprüfen.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

Das `model`-Objekt kann nun für Vorhersagen verwendet werden. Das vollständige Beispiel dieser Anwendung für maschinelles Lernen sowie Anweisungen in einzelnen Schritten zu der Ausführung finden Sie unter [Erstellen von Apache Spark-Machine Learning-Anwendungen für Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Weitere Informationen

* [Data Science unter Verwendung von Scala und Apache Spark in Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
