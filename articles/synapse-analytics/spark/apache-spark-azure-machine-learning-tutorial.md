---
title: 'Tutorial: Ausführen von Experimenten mit automatisiertem Azure ML'
description: Tutorial zum Ausführen von Machine Learning-Experimenten mithilfe von Apache Spark und automatisiertem Azure ML
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: b2fbc74304cdb71d9cb3e1ea476af8c92eb99b7e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458827"
---
# <a name="tutorial-run-experiments-using-azure-automated-ml-and-apache-spark"></a>Tutorial: Ausführen von Experimenten mit automatisiertem Azure ML und Apache Spark

Azure Machine Learning ist eine cloudbasierte Umgebung, die Ihnen das Trainieren, Bereitstellen, Automatisieren, Verwalten und Nachverfolgen von Machine Learning-Modellen ermöglicht. 

In diesem Tutorial verwenden Sie [automatisiertes maschinelles Lernen](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) in Azure Machine Learning, um ein Regressionsmodell für die Vorhersage der Preise für Taxifahrten in New York zu erstellen. Dieser Prozess akzeptiert Trainingsdaten und Konfigurationseinstellungen und durchläuft automatisch Kombinationen der verschiedenen Methoden, Modelle und Hyperparametereinstellungen zur Merkmalsnormalisierung/-standardisierung, um zum besten Modell zu gelangen.

In diesem Tutorial lernen Sie Folgendes:
- Herunterladen der Daten unter Verwendung von Apache Spark und Azure Open Datasets
- Transformieren und Bereinigen von Daten unter Verwendung von Azure Spark-Dataframes
- Trainieren eines Regressionsmodells mit automatisiertem maschinellem Lernen
- Berechnen der Modellgenauigkeit

### <a name="before-you-begin"></a>Voraussetzungen

- Erstellen Sie anhand der Schnellstartanleitung zum [Erstellen eines serverlosen Apache Spark-Pools](../quickstart-create-apache-spark-pool-studio.md) einen serverlosen Apache Spark-Pool.
- Absolvieren Sie das [Tutorial für die Einrichtung des Azure Machine Learning-Arbeitsbereichs](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup), falls Sie noch nicht über einen vorhandenen Azure Machine Learning-Arbeitsbereich verfügen. 

### <a name="understand-regression-models"></a>Grundlegendes zu logistischen Regressionsmodellen

*Regressionsmodelle* sagen numerische Ausgabewerte auf Grundlage unabhängiger Prädiktoren voraus. Bei der Regression besteht das Ziel darin, die Beziehung zwischen diesen unabhängigen Vorhersagevariablen herzustellen, indem geschätzt wird, wie eine Variable die anderen beeinflusst.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Beispiel für eine Regressionsanalyse mit den NYC-Taxidaten

In diesem Beispiel verwenden Sie Spark, um ein paar Analysen zu Trinkgelddaten von Taxifahrten in New York durchzuführen. Die Daten sind über [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) verfügbar. Diese Teilmenge des Datasets enthält Informationen über Taxifahrten von Yellow Cabs, einschließlich Informationen zu jeder einzelnen Fahrt, den Start- und Endzeiten/-orten, den Kosten und andere interessante Attribute.

> [!IMPORTANT]
> 
> Es können zusätzliche Gebühren für das Abrufen dieser Daten von ihrem Speicherort anfallen. In den folgenden Schritten entwickeln Sie ein Modell zur Vorhersage von NYC Taxi fare-Preisen. 
> 

##  <a name="download-and-prepare-the-data"></a>Herunterladen und Vorbereiten der Daten

1. Erstellen Sie ein Notebook unter Verwendung des PySpark-Kernels. Eine entsprechende Anleitung finden Sie unter [Erstellen eines Notebooks](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.).
   
> [!Note]
> 
> Durch den PySpark-Kernel müssen Sie keine Kontexte explizit erstellen. Der Spark-Kontext wird automatisch für Sie erstellt, wenn Sie die erste Codezelle ausführen.
>

2. Da die Rohdaten im Parquet-Format vorliegen, können Sie den Spark-Kontext verwenden, um die Datei als Dataframe direkt in den Arbeitsspeicher zu lesen. Erstellen Sie ein Spark-Dataframe, indem Sie die Daten über die Open Datasets-API abrufen. Hier verwenden wir die Spark-Dataframe *Schema beim Lese*-Eigenschaften, um die Datentypen und das Schema abzuleiten. 
   
```python
blob_account_name = "azureopendatastorage"
blob_container_name = "nyctlc"
blob_relative_path = "yellow"
blob_sas_token = r""

# Allow Spark to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

# Spark read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)

```

3. Je nach Größe Ihres Spark-Pools können die Rohdaten zu umfangreich sein oder zu viel Verarbeitungszeit in Anspruch nehmen. Sie können diese Daten zu einer kleineren Menge filtern, indem Sie die Filter ```start_date``` und ```end_date``` verwenden. Hierdurch wird ein Filter angewendet, der Daten eines Monats zurückgibt. Sobald wir das gefilterte Dataframe haben, führen wir auch die ```describe()```-Funktion für das neue Dataframe aus, um zusammenfassende Statistiken für jedes Feld anzuzeigen. 

   Basierend auf den Zusammenfassungsstatistiken können wir erkennen, dass es einige Unregelmäßigkeiten und Ausreißer in den Daten gibt. Beispielsweise zeigt die Statistik, dass die minimale Fahrtstrecke kleiner als 0 ist. Diese unregelmäßigen Datenpunkte müssen herausgefiltert werden.
   
```python
# Create an ingestion filter
start_date = '2015-01-01 00:00:00'
end_date = '2015-12-31 00:00:00'

filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

filtered_df.describe().show()
```

4. Nun generieren wir Merkmale aus dem Dataset, indem wir eine Gruppe von Spalten auswählen und verschiedene zeitbasierte Merkmale aus dem DateTime-Feld für die Abholung erstellen. Außerdem filtern wir Ausreißer heraus, die in dem vorherigen Schritt identifiziert wurden, und entfernen dann die letzten Spalten, die für das Training unnötig sind.
   
```python
from datetime import datetime
from pyspark.sql.functions import *

# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                , column('puMonth').alias('month_num') \
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                & (sampled_taxi_df.rateCodeId <= 5)\
                                & (sampled_taxi_df.paymentType.isin({"1", "2"})))
taxi_df.show(10)
```
   
   Wie Sie sehen können, wird dadurch ein neuer Dataframe mit zusätzlichen Spalten für den Tag des Monats, die Stunde der Abholung, den Wochentag und die Gesamtfahrtzeit erstellt. 


![Abbildung des Taxi-Dataframes.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Generieren von Datasets für Test und Validierung

Nachdem wir unser endgültiges Dataset haben, können wir die Daten mithilfe der Spark-Funktion ```random_ split ``` in Trainings- und Testsätze aufteilen. Unter Verwendung der bereitgestellten Gewichtungen teilt diese Funktion die Daten in das Trainingsdataset für das Modelltraining und das Validierungsdataset zum Testen auf.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Dieser Schritt stellt sicher, dass die Datenpunkte zum Testen des fertigen Modells nicht zum Training des Modells verwendet wurden. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Herstellen einer Verbindung mit einem Azure Machine Learning-Arbeitsbereich
In Azure Machine Learning ist ein **Arbeitsbereich** eine Klasse, die Informationen zu Ihrem Azure-Abonnement und Ihren Azure-Ressourcen akzeptiert. Außerdem erstellt der Arbeitsbereich eine Cloudressource zur Überwachung und Nachverfolgung Ihrer Modellausführungen. In diesem Schritt erstellen wir ein Arbeitsbereichsobjekt auf der Grundlage des vorhandenen Azure Machine Learning-Arbeitsbereichs.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Konvertieren eines Dataframes in ein Azure Machine Learning-Dataset
Um ein Remoteexperiment zu übermitteln, müssen wir unser Dataset in ein Azure Machine Learning ```TabularDatset``` konvertieren. Ein [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py&preserve-view=true) stellt Daten in einem tabellarischen Format dar, indem die bereitgestellten Dateien analysiert werden.

Mit dem folgenden Code werden der vorhandene Arbeitsbereich und der Azure Machine Learning-Standarddatenspeicher abgerufen. Anschließend werden der Datenspeicher und die Dateispeicherorte an den Pfadparameter übergeben, um ein neues ```TabularDataset``` zu erstellen. 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Abbildung des hochgeladenen Datasets.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automl-experiment"></a>Senden eines AutoML-Experiments.

#### <a name="define-training-settings"></a>Definieren von Trainingseinstellungen
1. Um ein Experiment zu übermitteln, müssen wir die Experimentparameter und Modelleinstellungen für das Training definieren. Die vollständige Liste der Einstellungen können Sie [hier](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train) anzeigen.

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_minutes": 30,
    "enable_early_stopping": True,
    "primary_metric": 'r2_score',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 2}
```

2. Nun werden die definierten Trainingseinstellungen als ein **kwargs-Parameter an ein AutoMLConfig-Objekt übergeben. Da wir in Spark trainieren, müssen wir auch den Spark-Kontext übergeben, auf den automatisch über die Variable ```sc``` zugegriffen werden kann. Außerdem geben wir die Trainingsdaten und den Typ des Modells an, der in diesem Fall „Regression“ ist.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data = dataset_training,
                             spark_context = sc,
                             model_explainability = False, 
                             label_column_name ="fareAmount",**automl_settings)
```

> [!NOTE]
>Die Schritte zur Vorverarbeitung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Vorverarbeitungsschritte automatisch auf Ihre Eingabedaten angewendet.

#### <a name="train-the-automatic-regression-model"></a>Trainieren des automatischen Regressionsmodells 
Nun erstellen wir ein Experimentobjekt in Ihrem Azure Machine Learning-Arbeitsbereich. Ein Experiment fungiert als Container für die einzelnen Ausführungen. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
Sobald das Experiment abgeschlossen ist, gibt die Ausgabe Details zu den abgeschlossenen Iterationen zurück. Für die einzelnen Iterationen werden jeweils die Art des Modells, die Ausführungsdauer und die Trainingsgenauigkeit angezeigt. Im Feld BEST wird die beste laufende Trainingsbewertung auf der Grundlage Ihres Metriktyps nachverfolgt.

![Screenshot der Modellausgabe.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> Sobald das AutoML-Experiment übermittelt ist, führt es verschiedene Iterationen und Modelltypen aus. Diese Ausführung dauert in der Regel 1 bis 1,5 Stunden. 

#### <a name="retrieve-the-best-model"></a>Abrufen des besten Modells
Um das beste Modell aus ihren Iterationen auszuwählen, verwenden wir die ```get_output```-Funktion, um das am besten ausgeführte und angepasste Modell zurückzugeben. Mit dem folgenden Code wird das am besten ausgeführte und angepasste Modell für eine beliebige protokollierte Metrik oder für eine bestimmte Iteration abgerufen.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Testen der Modellgenauigkeit
1. Um die Modellgenauigkeit zu testen, verwenden wir das beste Modell, um Vorhersagen für die Preise von Taxifahrten mit dem Testdataset auszuführen. Die Funktion ```predict``` verwendet das beste Modell und sagt die Werte von „y“ (Fahrtkosten) aus dem Validierungsdataset voraus. 

```python
# Test best model accuracy
validation_data_pd = validation_data.toPandas()
y_test = validation_data_pd.pop("fareAmount").to_frame()
y_predict = fitted_model.predict(validation_data_pd)
```

2. Die mittlere quadratische Gesamtabweichung (Root-Mean-Square-Error, RMSE) ist ein häufig verwendetes Maß für die Unterschiede zwischen den von einem Modell vorhergesagten Stichprobenwerten und den beobachteten Werten. Wir berechnen die mittlere quadratische Gesamtabweichung der Ergebnisse, indem wir den y_test-Dataframe mit den vom Modell vorhergesagten Werten vergleichen. 

   Die Funktion ```mean_squared_error``` akzeptiert zwei Arrays und berechnet den durchschnittlichen quadratischen Fehler zwischen den Arrays. Danach ziehen wir die Quadratwurzel aus dem Ergebnis. Diese Metrik gibt Aufschluss darüber, wie weit die Vorhersagen des Preises für Taxifahrten ungefähr von den tatsächlichen Werten der Preise entfernt sind.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

# Calculate Root Mean Square Error
y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))

print("Root Mean Square Error:")
print(rmse)
```

```Output
Root Mean Square Error:
2.309997102577151
```
Die mittlere quadratische Gesamtabweichung ist ein gutes Maß für die Genauigkeit der Vorhersage der Antwort durch das Modell. Anhand der Ergebnisse sehen Sie, dass das Modell die Preise von Taxifahrten auf der Grundlage der Features des Datasets ziemlich gut vorhersagen kann (in der Regel mit einer Genauigkeit von +/-2,00 USD).

3. Führen Sie den folgenden Code aus, um den mittleren absoluten Fehler in Prozent (Mean Absolute Percent Error, MAPE) zu berechnen. Diese Metrik drückt die Genauigkeit als Prozentsatz des Fehlers aus. Hierzu wird eine absolute Differenz zwischen jedem vorhergesagten und tatsächlichen Wert berechnet, und anschließend werden alle Differenzen summiert. Die Summe wird dann als Prozentsatz der gesamten tatsächlichen Werte ausgedrückt.

```python
# Calculate MAPE and Model Accuracy 
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals

print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```Output
Model MAPE:
0.03655071038487368

Model Accuracy:
0.9634492896151263
```
Anhand der zwei Metriken für die Vorhersagegenauigkeit sehen Sie, dass das Modell die Preise von Taxifahrten auf der Grundlage der Features des Datasets ziemlich gut vorhersagen kann. 

4. Nachdem wir ein lineares Regressionsmodell angepasst haben, müssen wir nun bestimmen, wie gut das Modell auf die Daten passt. Zu diesem Zweck werden die tatsächlichen Fahrpreiswerte gegen die vorhergesagte Ausgabe gezeichnet. Zusätzlich berechnen wir noch das Bestimmtheitsmaß (R²), um zu verstehen, wie nah die Daten an der angepassten Regressionslinie liegen.

```python
import matplotlib.pyplot as plt
import numpy as np
from sklearn.metrics import mean_squared_error, r2_score

# Calculate the R2 score using the predicted and actual fare prices
y_test_actual = y_test["fareAmount"]
r2 = r2_score(y_test_actual, y_predict)

# Plot the Actual vs Predicted Fare Amount Values
plt.style.use('ggplot')
plt.figure(figsize=(10, 7))
plt.scatter(y_test_actual,y_predict)
plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
plt.xlabel("Actual Fare Amount")
plt.ylabel("Predicted Fare Amount")
plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
plt.show()

```
![Screenshot des Regressionsplots.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Anhand der Ergebnisse können wir sehen, dass das Bestimmtheitsmaß 95 % unserer Varianz abdeckt. Dies wird auch durch den Plot der tatsächlichen gegen die beobachteten Werte bestätigt. Je mehr der Varianz durch das Regressionsmodell abgedeckt wird, desto näher werden die Datenpunkte an der angepassten Regressionslinie liegen.  

## <a name="register-model-to-azure-machine-learning"></a>Registrieren des Modells in Azure Machine Learning
Nachdem wir unser bestes Modell validiert haben, können wir das Modell bei Azure Machine Learning registrieren. Nachdem Sie das Modell registriert haben, können Sie das registrierte Modell herunterladen oder bereitstellen und alle Dateien empfangen, die Sie registriert haben.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Anzeigen von Ergebnissen in Azure Machine Learning
Als Letztes können Sie auch auf die Ergebnisse der Iterationen zugreifen, indem Sie in Ihrem Azure Machine Learning-Arbeitsbereich zu dem Experiment navigieren. Hier können Sie zusätzliche Details zum Status ihrer Ausführung, zu ausprobierten Modellen und anderen Modellmetriken untersuchen. 

![Screenshot des AML-Arbeitsbereichs.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Nächste Schritte
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark MLlib-Tutorial](./apache-spark-machine-learning-mllib-notebook.md)