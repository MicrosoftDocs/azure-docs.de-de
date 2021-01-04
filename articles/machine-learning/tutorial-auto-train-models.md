---
title: 'Tutorial zur Regression: Automatisiertes maschinelles Lernen'
titleSuffix: Azure Machine Learning
description: Erstellen Sie ein Experiment mit automatisiertem maschinellem Lernen, das basierend auf den von Ihnen bereitgestellten Trainingsdaten und Konfigurationseinstellungen ein Regressionsmodell für Sie generiert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python, automl
ms.openlocfilehash: e1a5370501fe73fb783db9a039d9f060acdb0a35
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511031"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>Tutorial: Vorhersagen von Preisen für Taxifahrten mit automatisiertem maschinellem Lernen


In diesem Tutorial verwenden Sie automatisiertes maschinelles Lernen in Azure Machine Learning, um ein Regressionsmodell für die Vorhersage der Preise für Taxifahrten in New York zu erstellen. Dieser Prozess akzeptiert Trainingsdaten und Konfigurationseinstellungen und durchläuft automatisch Kombinationen der verschiedenen Methoden, Modelle und Hyperparametereinstellungen zur Featurenormalisierung/-standardisierung, um zum besten Modell zu gelangen.

![Flussdiagramm](./media/tutorial-auto-train-models/flow2.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Herunterladen, Transformieren und Bereinigen von Daten unter Verwendung von Azure Open Datasets
> * Trainieren eines Regressionsmodells mit automatisiertem maschinellem Lernen
> * Berechnen der Modellgenauigkeit

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie das [Einrichtungstutorial](tutorial-1st-experiment-sdk-setup.md), falls Sie noch nicht über einen Azure Machine Learning-Arbeitsbereich oder über einen virtuellen Notebook-Computer verfügen.
* Öffnen Sie nach Abschluss des Einrichtungstutorials das Notebook *tutorials/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb* unter Verwendung des gleichen Notebook-Servers.

Dieses Tutorial ist auch auf [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) verfügbar, falls Sie es in Ihrer eigenen [lokalen Umgebung](how-to-configure-environment.md#local) ausführen möchten. So erhalten Sie die erforderlichen Pakete: 
* [Installieren Sie den vollständigen `automl`-Client.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/README.md#setup-using-a-local-conda-environment)
* Führen Sie `pip install azureml-opendatasets azureml-widgets` aus, um die erforderlichen Pakete abzurufen.

## <a name="download-and-prepare-data"></a>Herunterladen und Vorbereiten von Daten

Importieren Sie die erforderlichen Pakete. Das Paket mit öffentlichen Datasets enthält eine Klasse für jede Datenquelle (z. B. `NycTlcGreen`), damit Sie vor dem Herunterladen Datumsparameter ganz einfach filtern können.

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

Beginnen Sie, indem Sie einen Datenrahmen für die Taxidaten erstellen. Bei der Arbeit in einer Spark-fremden Umgebung ermöglichen öffentliche Datasets nur jeweils das Herunterladen der Daten eines Monats mit bestimmten Klassen, um `MemoryError` bei großen Datasets zu vermeiden.

Rufen Sie zum Herunterladen der Taxidaten iterativ jeweils einen Monat ab, und nehmen Sie nach dem Zufallsprinzip eine Stichprobe von 2,000 Einträgen aus jedem Monat, bevor Sie die Daten an `green_taxi_df` anfügen. So verhindern Sie eine Überfrachtung des Datenrahmens. Zeigen Sie dann eine Vorschau der Daten an.


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType |fareAmount |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|    tripType|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4.84|Keine|Keine|-73.88|40.84|-73.94|...|2|15.00|0,50|0,50|0,3|0.00|0.00|nan|16.30|1.00
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0.69|Keine|Keine|-73.96|40.81|-73.96|...|2|4.50|1.00|0,50|0,3|0.00|0.00|nan|6.30|1.00
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0.45|Keine|Keine|-73.92|40.76|-73.91|...|2|4.00|0.00|0,50|0,3|0.00|0.00|nan|4.80|1.00
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0.00|Keine|Keine|-73.81|40.70|-73.82|...|2|12.50|0,50|0,50|0,3|0.00|0.00|nan|13.80|1.00
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Keine|Keine|-73.92|40.76|-73.92|...|2|4.00|0,50|0,50|0|0.00|0.00|nan|5.00|1.00
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Keine|Keine|-73.96|40.72|-73.95|...|2|6.50|0,50|0,50|0,3|0.00|0.00|nan|7.80|1.00
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0.90|Keine|Keine|-73.88|40.76|-73.87|...|2|6.00|0.00|0,50|0,3|0.00|0.00|nan|6.80|1.00
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3.30|Keine|Keine|-73.96|40.72|-73.91|...|2|12.50|0,50|0,50|0,3|0.00|0.00|nan|13.80|1.00
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1.19|Keine|Keine|-73.94|40.71|-73.95|...|1|7.00|0.00|0,50|0,3|1,75|0.00|nan|9,55|1.00
|432136|2|2015-01-22 23:16:33   2015-01-22 23:20:13 1   0,65|Keine|Keine|-73.94|40.71|-73.94|...|2|5.00|0,50|0,50|0,3|0.00|0.00|nan|6.30|1.00

Nachdem die ersten Daten geladen wurden, definieren Sie eine Funktion zum Erstellen verschiedener zeitbasierter Features aus dem Feld für Startdatum und -uhrzeit. Dadurch werden neue Felder für die Monatsnummer, den Tag des Monats, den Wochentag und die Stunde des Tages erstellt. So kann das Modell zeitbasiert saisonale Besonderheiten berücksichtigen. Verwenden Sie die `apply()`-Funktion im Datenrahmen, um die `build_time_features()`-Funktion iterativ auf jede Zeile in den Taxidaten anzuwenden.

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType|fareAmount  |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|tripType|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|----
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4.84|Keine|Keine|-73.88|40.84|-73.94|...|2|15.00|0,50|0,50|0,3|0.00|0.00|nan|16.30|1.00|1|11|6|5
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0.69|Keine|Keine|-73.96|40.81|-73.96|...|2|4.50|1.00|0,50|0,3|0.00|0.00|nan|6.30|1.00|1|20|1|16
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0.45|Keine|Keine|-73.92|40.76|-73.91|...|2|4.00|0.00|0,50|0,3|0.00|0.00|nan|4.80|1.00|1|1|3|5
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0.00|Keine|Keine|-73.81|40.70|-73.82|...|2|12.50|0,50|0,50|0,3|0.00|0.00|nan|13.80|1.00|1|17|5|2
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0,50|Keine|Keine|-73.92|40.76|-73.92|...|2|4.00|0,50|0,50|0|0.00|0.00|nan|5.00|1.00|1|1|3|5
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|Keine|Keine|-73.96|40.72|-73.95|...|2|6.50|0,50|0,50|0,3|0.00|0.00|nan|7.80|1.00|1|4|6|19
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0.90|Keine|Keine|-73.88|40.76|-73.87|...|2|6.00|0.00|0,50|0,3|0.00|0.00|nan|6.80|1.00|1|3|5|12
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3.30|Keine|Keine|-73.96|40.72|-73.91|...|2|12.50|0,50|0,50|0,3|0.00|0.00|nan|13.80|1.00|1|9|4|23
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1.19|Keine|Keine|-73.94|40.71|-73.95|...|1|7.00|0.00|0,50|0,3|1,75|0.00|nan|9,55|1.00|1|11|6|17
|432136|2|2015-01-22 23:16:33   2015-01-22 23:20:13 1   0,65|Keine|Keine|-73.94|40.71|-73.94|...|2|5.00|0,50|0,50|0,3|0.00|0.00|nan|6.30|1.00|1|22|3|23

Entfernen Sie einige der Spalten, die Sie für das Training und die Erstellung zusätzlicher Features nicht benötigen.

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>Bereinigen der Daten

Führen Sie die `describe()`-Funktion für das neue Dataframe aus, um zusammenfassende Statistiken für jedes Feld anzuzeigen.

```python
green_taxi_df.describe()
```

|vendorID|passengerCount|tripDistance|pickupLongitude|pickupLatitude|dropoffLongitude|dropoffLatitude|  totalAmount|month_num   day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|---
|count|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00
|mean|1,78|1.37|2,87|-73.83|40.69|-73.84|40.70|14.75|6.50|15.13|3,27|13.52
|std|0,41|1,04|2,93|2.76|1.52|2.61|1.44|12.08|3.45|8.45|1,95|6.83
|Min|1.00|0.00|0.00|-74.66|0.00|-74.66|0.00|-300.00|1.00|1.00|0.00|0.00
|25 %|2.00|1.00|1.06|-73.96|40.70|-73.97|40.70|7.80|3,75|8.00|2.00|9.00
|50%|2.00|1.00|1.90|-73.94|40.75|-73.94|40.75|11.30|6.50|15.00|3.00|15.00
|75 %|2.00|1.00|3.60|-73.92|40.80|-73.91|40.79|17.80|9.25|22.00|5.00|19.00
|max|2.00|9.00|97.57|0.00|41.93|0.00|41.94|450.00|12.00|30,00|6.00|23.00


In den zusammenfassenden Statistiken sehen Sie, dass mehrere Felder Ausreißer oder Werte aufweisen, die die Modellgenauigkeit verringern. Filtern Sie zuerst die Felder für Längen-/Breitengrad, sodass diese das Gebiet von Manhattan umfassen. Dadurch werden längere Taxifahrten sowie Fahrten herausgefiltert, bei denen es sich hinsichtlich der Beziehung zu anderen Features um Ausreißer handelt.

Filtern Sie außerdem `tripDistance` das Feld so, dass es größer Null, aber kleiner als 31 Meilen (Semiversus-Abstand zwischen zwei Längen-/Breitengrad-Paaren) ist. Dadurch werden längere Fahrten mit inkonsistenten Fahrtkosten ausgeschlossen.

Darüber hinaus enthält das Feld `totalAmount` negative Werte für Fahrtkosten, was im Kontext unseres Modells keinen Sinn ergibt, und das Feld `passengerCount` enthält ungültige Daten, bei denen die minimalen Werte Null sind.

Filtern Sie diese Anomalien mithilfe von Abfragefunktionen heraus, und entfernen Sie dann die letzten Spalten, die für das Training nicht erforderlich sind.


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

Rufen Sie `describe()` erneut für die Daten auf, um sicherzustellen, dass die Bereinigung erwartungsgemäß verlaufen ist. Sie haben jetzt einen vorbereiteten und bereinigten Satz von Taxi-, Feiertags- und Wetterdaten, mit dem Sie das Machine Learning-Modell trainieren können.

```python
final_df.describe()
```

## <a name="configure-workspace"></a>Konfigurieren des Arbeitsbereichs

Erstellen Sie ein Arbeitsbereichsobjekt aus dem vorhandenen Arbeitsbereich. Ein [Arbeitsbereich](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py) ist eine Klasse, die Informationen zu Ihrem Azure-Abonnement und Ihren Azure-Ressourcen akzeptiert. Außerdem erstellt der Arbeitsbereich eine Cloudressource zur Überwachung und Nachverfolgung Ihrer Modellausführungen. `Workspace.from_config()` liest die Datei **config.json** und lädt die Authentifizierungsdetails in ein Objekt namens `ws`. `ws` wird im restlichen Code in diesem Tutorial verwendet.

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>Aufteilen der Daten in Trainings- und Testsätze

Teilen Sie die Daten mithilfe der Funktion `train_test_split` in der Bibliothek `scikit-learn` in Trainings- und Testsätze auf. Diese Funktion unterteilt die Daten in das x-Dataset (**Features**) zum Trainieren des Modells und in das y-Dataset (**zu prognostizierende Werte**) zum Testen.

Der Parameter `test_size` bestimmt den Prozentsatz der Daten, die zu Testzwecken verwendet werden sollen. Der Parameter `random_state` legt einen Seed für den Zufallsgenerator fest, damit die Aufteilung zwischen Trainings- und Testdaten deterministisch ist.

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

Der Zweck dieses Schritts besteht darin, Datenpunkte zum Testen des fertigen Modells zu erhalten, mit denen das Modell nicht trainiert wurde. Anhand dieser Datenpunkte kann dann die tatsächliche Genauigkeit beurteilt werden.

Anders ausgedrückt: Ein gut trainiertes Modell muss zutreffende Vorhersagen auf der Grundlage von Daten generieren können, die dem Modell noch nicht bekannt waren. Sie verfügen nun über Daten, die für das automatische Trainieren eines Machine Learning-Modells vorbereitet sind.

## <a name="automatically-train-a-model"></a>Automatisches Trainieren eines Modells

Führen Sie die folgenden Schritte aus, um ein Modell automatisch zu trainieren:
1. Definieren Sie Einstellungen für die Experimentausführung. Fügen Sie Ihre Trainingsdaten an die Konfiguration an, und ändern Sie Einstellungen, die den Trainingsprozess steuern.
1. Übermitteln Sie das Experiment zur Modelloptimierung. Nach Übermittlung des Experiments durchläuft der Prozess verschiedene Machine Learning-Algorithmen und Hyperparametereinstellungen unter Berücksichtigung der von Ihnen definierten Einschränkungen. Er optimiert eine Genauigkeitsmetrik, um das am besten geeignete Modell auszuwählen.

### <a name="define-training-settings"></a>Definieren von Trainingseinstellungen

Definieren Sie die Experimentparameter und Modelleinstellungen für das Training. Die vollständige Liste mit den Einstellungen finden Sie [hier](how-to-configure-auto-train.md). Mit diesen Standardeinstellungen dauert die Übermittlung des Experiments etwa fünf bis 20 Minuten. Durch Verringern des Parameters `experiment_timeout_hours` können Sie die Laufzeit allerdings verkürzen.

|Eigenschaft| Wert in diesem Tutorial |BESCHREIBUNG|
|----|----|---|
|**iteration_timeout_minutes**|10|Zeitlimit in Minuten für jede Iteration. Erhöhen Sie diesen Wert für größere Datasets, die mehr Zeit für jede Iteration benötigen.|
|**experiment_timeout_hours**|0,3|Maximal zulässige Dauer für alle Iterationen (in Stunden). Danach wird das Experiment beendet.|
|**enable_early_stopping**|True|Flag zum Aktivieren der vorzeitigen Beendigung, wenn sich der Score auf kurze Sicht nicht verbessert.|
|**primary_metric**| spearman_correlation | Metrik, die Sie optimieren möchten. Das am besten geeignete Modell wird basierend auf dieser Metrik ausgewählt.|
|**featurization**| auto | Bei Verwendung von **auto** kann das Experiment die Eingabedaten vorverarbeiten und beispielsweise fehlende Daten behandeln, Text in numerische Daten umwandeln und Ähnliches.|
|**verbosity**| logging.INFO | Steuert den Protokollierungsgrad.|
|**n_cross_validations**|5|Anzahl von Kreuzvalidierungsaufteilungen, die ausgeführt werden sollen, wenn keine Überprüfungsdaten angegeben sind.|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 10,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

Verwenden Sie Ihre definierten Trainingseinstellungen als Parameter `**kwargs` für ein Objekt vom Typ `AutoMLConfig`. Geben Sie außerdem Ihre Trainingsdaten und die Art des Modells (in diesem Fall: `regression`) an.

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> Die Schritte zur Vorverarbeitung bei automatisiertem maschinellen Lernen (Featurenormalisierung, Behandlung fehlender Daten, Umwandlung von Text in numerische Daten usw.) werden Teil des zugrunde liegenden Modells. Bei Verwendung des Modells für Vorhersagen werden die während des Trainings angewendeten Vorverarbeitungsschritte automatisch auf Ihre Eingabedaten angewendet.

### <a name="train-the-automatic-regression-model"></a>Trainieren des automatischen Regressionsmodells

Erstellen Sie ein Experimentobjekt in Ihrem Arbeitsbereich. Ein Experiment fungiert als Container für die einzelnen Ausführungen. Übergeben Sie das definierte Objekt vom Typ `automl_config` an das Experiment, und legen Sie die Ausgabe auf `True` fest, um den Fortschritt während der Ausführung anzuzeigen.

Nach Beginn des Experiments werden Aktualisierungen live in der Ausgabe angezeigt. Für die einzelnen Iterationen werden jeweils die Art des Modells, die Ausführungsdauer und die Trainingsgenauigkeit angezeigt. Im Feld `BEST` wird die beste Trainingsbewertung auf der Grundlage Ihres Metriktyps nachverfolgt.

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>Untersuchen der Ergebnisse

Untersuchen Sie die Ergebnisse des automatischen Trainings mit einem [Jupyter-Widget](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py). Mit dem Widget können Sie ein Diagramm und eine Tabelle aller individuellen Ausführungsiterationen sowie Metriken zur Trainingsgenauigkeit und Metadaten anzeigen. Darüber hinaus können Sie über das Dropdownmenü nach anderen Genauigkeitsmetriken filtern.

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Ausführungsdetails des Jupyter-Widgets](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter-Widget-Plot](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>Abrufen des besten Modells

Wählen Sie aus Ihren Iterationen das beste Modell aus. Die Funktion `get_output` gibt die beste Ausführung und das angepasste Modell für den letzten passenden Aufruf zurück. Mithilfe der Überladungen für `get_output` können Sie die beste Ausführung und das angepasste Modell für eine beliebige protokollierte Metrik oder für eine bestimmte Iteration abrufen.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>Testen der Genauigkeit des besten Modells

Verwenden Sie das beste Modell, um Vorhersagen für das Testdataset auszuführen und Preise für Taxifahrten vorauszusagen. Die Funktion `predict` verwendet das beste Modell und prognostiziert die Werte von y (**Fahrtkosten**) anhand des Datasets `x_test`. Geben Sie die ersten zehn prognostizierten Kostenwerte von `y_predict` aus.

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

Berechnen Sie den `root mean squared error` der Ergebnisse. Konvertieren Sie den Datenrahmen `y_test` in eine Liste, um ihn mit den vorhergesagten Werten zu vergleichen. Die Funktion `mean_squared_error` akzeptiert zwei Wertarrays und berechnet den durchschnittlichen quadratischen Fehler zwischen den Arrays. Die Quadratwurzel des Ergebnisses ergibt einen Fehler in der gleichen Maßeinheit wie die y-Variable (**Kosten**). Dieser Fehler gibt Aufschluss darüber, wie weit die Vorhersagen des Preises für Taxifahrten ungefähr vom tatsächlichen Preis entfernt sind.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Führen Sie den folgenden Code aus, um den mittleren absoluten Fehler in Prozent (Mean Absolute Percent Error, MAPE) anhand der vollständigen Datasets `y_actual` und `y_predict` zu berechnen. Diese Metrik berechnet eine absolute Differenz zwischen jedem vorhergesagten und tatsächlichen Wert und addiert alle Differenzen. Die Summe wird dann als Prozentsatz der gesamten tatsächlichen Werte ausgedrückt.

```python
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

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


Anhand der endgültigen Metriken für die Vorhersagegenauigkeit sehen Sie, dass das Modell die Preise von Taxifahrten auf der Grundlage der Features des Datasets ziemlich gut vorhersagen kann (in der Regel mit einer Genauigkeit von +/-4,00 USD und einem Fehler von etwa 15 Prozent).

Der traditionelle Modellentwicklungsprozess für das maschinelle Lernen ist sehr ressourcenintensiv und erfordert erhebliches Fachwissen und großen Zeitaufwand, um die Ergebnisse von Dutzenden von Modellen auszuführen und zu vergleichen. Mit automatisiertem maschinellem Lernen können Sie schnell zahlreiche verschiedene Modelle für Ihr Szenario testen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Überspringen Sie diesen Abschnitt, wenn Sie weitere Azure Machine Learning-Tutorials absolvieren möchten.

### <a name="stop-the-compute-instance"></a>Beenden der Compute-Instanz

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Alles löschen

Wenn Sie die erstellten Ressourcen nicht mehr benötigen, löschen Sie sie, damit Ihnen keine Kosten entstehen.

1. Wählen Sie ganz links im Azure-Portal **Ressourcengruppen** aus.
1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie erstellt haben.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie den Ressourcengruppennamen ein. Wählen Sie anschließend die Option **Löschen**.

Sie können die Ressourcengruppe auch behalten und einen einzelnen Arbeitsbereich löschen. Zeigen Sie die Eigenschaften des Arbeitsbereichs an, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial zum automatisierten maschinellen Lernen haben Sie folgende Aufgaben ausgeführt:

> [!div class="checklist"]
> * Konfigurieren eines Arbeitsbereichs und Vorbereiten der Daten für ein Experiment
> * Lokales Trainieren mit einem automatisierten Regressionsmodell und benutzerdefinierten Parametern
> * Untersuchen und Überprüfen der Trainingsergebnisse

Als Nächstes können Sie [Ihr Modell mit Azure Machine Learning bereitstellen](tutorial-deploy-models-with-aml.md).
