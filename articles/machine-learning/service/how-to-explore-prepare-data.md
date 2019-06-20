---
title: Untersuchen und Transformieren von Daten (Dataset-Klasse)
titleSuffix: Azure Machine Learning service
description: Untersuchen Sie Daten mithilfe von zusammenfassenden Statistiken, und bereiten Sie Daten durch Datenbereinigung, Transformation und Feature Engineering auf.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/23/2019
ms.openlocfilehash: e29ef2616a43223ec582575ca6363f78b26e5f22
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753057"
---
# <a name="explore-and-prepare-data-with-the-dataset-class-preview"></a>Untersuchen und Aufbereiten von Daten mit der Dataset-Klasse (Vorschau)

Erfahren Sie, wie Sie Daten untersuchen und aufbereiten können, indem Sie das „azureml-datasets“-Paket aus dem [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) verwenden. Die [Dataset](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py)-Klasse (Vorschau) ermöglicht Ihnen das Untersuchen und Aufbereiten der Daten durch die Bereitstellung von Funktionen (z.B. Stichprobenentnahme, zusammenfassende Statistiken und intelligente Transformationen). Transformationsschritte werden in [Dataset-Definitionen](how-to-manage-dataset-definitions.md) mit der Möglichkeit gespeichert, mehrere große Dateien mit unterschiedlichen Schemas in einer hochgradig skalierbaren Weise zu verarbeiten.

> [!Important]
> Einige Dataset-Klassen (Vorschauversion) haben Abhängigkeiten zu dem [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)-Paket (GA). Während Transformationsfunktionen direkt mit den allgemein verfügbaren [Funktionen der Datenaufbereitung](how-to-transform-data.md) ausgeführt werden können, empfehlen wir die in diesem Artikel beschriebenen Dataset-Paketwrapper, wenn Sie eine neue Lösung erstellen. Azure Machine Learning Datasets (Vorschau) ermöglicht es Ihnen nicht nur, Ihre Daten zu transformieren, sondern auch [Momentaufnahmen von Daten](how-to-create-dataset-snapshots.md) zu erstellen und [mit einer Versionsangabe versehene Dataset-Definitionen](how-to-manage-dataset-definitions.md) zu speichern. Datasets ist die nächste Version des Datenaufbereitungs-SDK und bietet erweiterte Funktionen zum Verwalten von Datasets in KI-Lösungen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Untersuchen und Vorbereiten der Daten benötigen Sie Folgendes:

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

* Ein Azure Machine Learning-Dienstbereich. Siehe [Erstellen eines Azure Machine Learning Service-Arbeitsbereichs](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace).

* Das Azure Machine Learning-SDK für Python (Version 1.0.21 oder höher), in dem das Paket „azureml-datasets“ enthalten ist. Wie Sie die neueste Version des SDK installieren oder auf diese aktualisieren, erfahren Sie unter [Installieren des Azure Machine Learning SDK für Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Das Datenaufbereitungs-SDK von Azure Machine Learning. Unter [Installieren des Datenaufbereitungs-SDK](https://docs.microsoft.com/python/api/overview/azure/dataprep/intro?view=azure-dataprep-py#install) erfahren Sie, wie die aktuelle Version installiert oder ein Update darauf ausgeführt wird.

* Laden Sie die Beispieldateien herunter, um die Beispiele nachzuvollziehen: [crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) und [city.json](https://dprepdata.blob.core.windows.net/dataset-sample-files/city.json).

## <a name="sampling"></a>Stichproben

Nehmen Sie eine Stichprobe Ihrer Daten, um ein erstes Verständnis Ihrer Datenarchitektur und des Inhalts zu erhalten. Zu diesem Zeitpunkt unterstützt die [`sample()`](https://docs.microsoft.com//python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#sample-sample-strategy--arguments-)-Methode aus der Dataset-Klasse Top N-Abfragen, einfache Zufallsstichproben und geschichtete Stichprobenstrategien.

```Python
from azureml.core.dataset import Dataset
import random

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')

# create seed for Simple Random and Stratified sampling
seed = random.randint(0, 4294967295)
```

### <a name="top-n-sample"></a>Top N-Stichprobe

Für Top N-Stichproben sind die ersten n Datensätze Ihres Datasets Ihre Stichprobe. Dies ist hilfreich, wenn Sie nur versuchen, eine Vorstellung davon zu bekommen, wie Ihre Datensätze aussehen oder welche Felder in Ihren Daten enthalten sind.

```Python
top_n_sample_dataset = dataset.sample('top_n', {'n': 5})
top_n_sample_dataset.to_pandas_dataframe()
```

||ID|Fallnummer|Date|Block|IUCR|Primärer Typ|...|
-|--|-----------|----|-----|----|------------|---
0|10498554|HZ239907|4/4/2016 23:56|007XX E 111TH ST|1153|DECEPTIVE PRACTICE|...
1|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
2|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|...
3|10519591|HZ261534|4/15/2016 9:00|113XX S PRAIRIE AVE|1120|DECEPTIVE PRACTICE|...
4|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="simple-random-sample"></a>Einfache Zufallsstichprobe

Bei der einfachen Zufallsstichprobe hat jedes Element der Datenpopulation die gleiche Chance, als Teil der Stichprobe ausgewählt zu werden. In der `simple_random`-Stichprobenstrategie werden die Datensätze aus Ihrem Dataset basierend auf der angegebenen Wahrscheinlichkeit ausgewählt und geben ein modifiziertes Dataset zurück. Der Seedparameter ist optional.

```Python
simple_random_sample_dataset = dataset.sample('simple_random', {'probability':0.3, 'seed': seed})
simple_random_sample_dataset.to_pandas_dataframe()
```

||ID|Fallnummer|Date|Block|IUCR|Primärer Typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10519196|HZ261252|4/15/2016 10:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|...
2|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...

### <a name="stratified-sample"></a>Geschichtete Stichprobe

Geschichtete Stichproben stellen sicher, dass bestimmte Gruppen einer Population in der Stichprobe enthalten sind. In der `stratified`-Stichprobenstrategie wird die Population in Schichten oder Untergruppen unterteilt, die auf Ähnlichkeiten basieren, und die Datensätze werden nach dem Zufallsprinzip aus jeder Schicht gemäß den durch den Parameter `fractions` angegebenen Gewichtungen der Schichten ausgewählt.

Im folgenden Beispiel gruppieren wir jeden Datensatz nach den angegebenen Spalten und schließen den genannten Datensatz basierend auf den Gewichtungsinformationen der Schicht X in `fractions` ein. Wenn eine Schicht nicht angegeben wird oder der Datensatz nicht gruppiert werden kann, ist die Standardgewichtung der Stichprobe 0.

```Python
# take 50% of records with `Primary Type` as `THEFT` and 20% of records with `Primary Type` as `DECEPTIVE PRACTICE` into sample Dataset
fractions = {}
fractions[('THEFT',)] = 0.5
fractions[('DECEPTIVE PRACTICE',)] = 0.2

sample_dataset = dataset.sample('stratified', {'columns': ['Primary Type'], 'fractions': fractions, 'seed': seed})

sample_dataset.to_pandas_dataframe()
```

||ID|Fallnummer|Date|Block|IUCR|Primärer Typ|...|
-|--|-----------|----|-----|----|------------|---
0|10516598|HZ258664|4/15/2016 17:00|082XX S MARSHFIELD AVE|890|THEFT|...
1|10534446|HZ277630|4/15/2016 10:00|055XX N KEDZIE AVE|890|THEFT|...
2|10535059|HZ278872|4/15/2016 4:30|004XX S KILBOURN AVE|810|THEFT|...

## <a name="explore-with-summary-statistics"></a>Untersuchen mit Zusammenfassungsstatistik

 Erkennung Sie Anomalien, fehlende Werte oder die Fehleranzahl mit der [`get_profile()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-)-Methode. Diese Funktion ruft das Profil und die zusammenfassende Statistik Ihrer Daten ab, was wiederum hilft, die erforderlichen anzuwendenden Datenaufbereitungsvorgänge zu bestimmen.

```Python
dataset.get_profile()
```

||Type|Min|max|Count|Fehlt (Anzahl)|Fehlt nicht (Anzahl)|Fehlt (Prozent)|Fehler (Anzahl)|Leer (Anzahl)|0,1 % Quantil|1 % Quantil|5 % Quantil|25 % Quantil|50 % Quantil|75 % Quantil|95 % Quantil|99 % Quantil|99,9 % Quantil|Mittelwert|Standardabweichung|Variance|Schiefe|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Fallnummer|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Block|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1\.136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primärer Typ|FieldType.STRING|DECEPTIVE PRACTICE|THEFT|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
BESCHREIBUNG|FieldType.STRING|BOGUS CHECK|OVER $500|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Standortbeschreibung|FieldType.STRING||SCHOOL, PUBLIC, BUILDING|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Verhaftung|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Inland|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Bezirk|FieldType.INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Gemeindebezirk|FieldType.INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379
FBI-Code|FieldType.INTEGER|6|11|10,0|0.0|10,0|0.0|0.0|0.0|6|6|6|6|11|11|11|11|11|9.4|2.36643|5.6|-0.702685|-1.59582
x-Koordinate|FieldType.INTEGER|1.16309e+06|1.18336e+06|10,0|7.0|3.0|0.7|0.0|0.0|1.16309e+06|1.16309e+06|1.16309e+06|1.16401e+06|1.16678e+06|1.17921e+06|1.18336e+06|1.18336e+06|1.18336e+06|1.17108e+06|10793.5|1.165e+08|0.335126|-2.33333
y-Koordinate|FieldType.INTEGER|1.8315e+06|1.908e+06|10,0|7.0|3.0|0.7|0.0|0.0|1.8315e+06|1.8315e+06|1.8315e+06|1.83614e+06|1.85005e+06|1.89352e+06|1.908e+06|1.908e+06|1.908e+06|1.86319e+06|39905.2|1.59243e+09|0.293465|-2.33333
Jahr|FieldType.INTEGER|2016|2016|10,0|0.0|10,0|0.0|0.0|0.0|2016|2016|2016|2016|2016|2016|2016|2016|2016|2016|0|0|NaN|NaN
Aktualisiert am|FieldType.DATE|2016-05-11 15:48:00+00:00|2016-05-27 15:45:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Breitengrad|FieldType.DECIMAL|41.6928|41.9032|10,0|7.0|3.0|0.7|0.0|0.0|41.6928|41.6928|41.6928|41.7057|41.7441|41.8634|41.9032|41.9032|41.9032|41.78|0.109695|0.012033|0.292478|-2.33333
Längengrad|FieldType.DECIMAL|-87.6764|-87.6043|10,0|7.0|3.0|0.7|0.0|0.0|-87.6764|-87.6764|-87.6764|-87.6734|-87.6645|-87.6194|-87.6043|-87.6043|-87.6043|-87.6484|0.0386264|0.001492|0.344429|-2.33333
Location|FieldType.STRING||(41.903206037, -87.676361925)|10,0|0.0|10,0|0.0|0.0|7.0||||||||||||||

## <a name="impute-missing-values"></a>Imputieren von fehlenden Werten

In Datasets werden NULL-Werte, NaN-Werte und die Werte, die keinen Inhalt enthalten, als fehlende Werte betrachtet. Diese können sich auf die Leistung der Machine Learning-Modelle auswirken oder dazu führen, dass ungültige Schlüsse gefolgert werden. Imputation ist ein gängiger Ansatz, um fehlende Werte anzugehen, und eignet sich, wenn Sie einen hohen Prozentsatz an fehlenden Wertdatensätzen haben, die Sie nicht einfach löschen können.

Aus dem im vorhergehenden Abschnitt generierten Dataset-Profil können wir ersehen, dass die Spalten `Latitude` und `Longitude` einen hohen Prozentsatz an fehlenden Werten aufweisen. In diesem Beispiel berechnen wir den Mittelwert und imputieren fehlende Werte für diese beiden Spalten.

Rufen Sie zunächst die neueste Definition des Datasets mit [`get_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-definition-version-id-none-) ab, und verringern Sie die Daten mit [`keep_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#keep-columns-columns--multicolumnselection--validate-column-exists--bool---false-----azureml-dataprep-api-dataflow-dataflow), sodass wir nur die Spalten anzeigen, die wir behandeln möchten.

```Python
from azureml.core.dataset import Dataset
import azureml.dataprep as dprep

# get the latest definition of Dataset
ds_def = dataset.get_definition()

# keep useful columns for this example
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
ds_def.head(3)
```

||ID|Verhaftung| Breitengrad|Längengrad|
-|---------|-----|---------|----------|
|0|10498554|False|41.692834|-87.604319|
|1|10516598|False| 41.744107 |-87.664494|
|2|10519196|False| NaN|NaN|

Überprüfen Sie nun den Wert `MEAN` der Spalte für den Breitengrad mithilfe der [`summarize()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow)-Funktion. Diese Funktion akzeptiert ein Array von Spalten im Parameter `group_by_columns` zur Festlegung der Aggregationsebene. Der Parameter `summary_columns` nimmt die `SummaryColumnsValue`-Funktion an, die den aktuellen Spaltennamen, den Namen des neuen berechneten Felds sowie die auszuführende `SummaryFunction` festlegt.

```Python
lat_mean = ds_def.summarize(group_by_columns = ['Arrest'],
                            summary_columns = [dprep.SummaryColumnsValue(column_id = 'Latitude',
                                                                         summary_column_name = 'Latitude_MEAN',
                                                                         summary_function = dprep.SummaryFunction.MEAN)])
lat_mean = lat_mean.filter(lat_mean['Arrest'] == False)
lat_mean.head(1)
```

||Verhaftung|Latitude_MEAN|
--|-----|--------|
|0|False|41.780049|

Sobald wir die zu imputierenden Werte überprüft haben, verwenden Sie [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py), um einen festen Ausdruck zu erhalten, der die Spalten entweder mit einem berechneten `MIN`-, `MAX`-, `MEAN`-Wert oder einem `CUSTOM`-Wert imputiert. Wenn `group_by_columns` angegeben ist, werden fehlende Werte nach Gruppe imputiert, wobei `MIN`, `MAX`, und `MEAN` pro Gruppe berechnet werden.

[`ImputeColumnArguments`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputecolumnarguments?view=azure-dataprep-pyfunction) akzeptiert eine column_id-Zeichenfolge und eine `ReplaceValueFunction` zum Festlegen des Imputetyps. Der fehlende Längengradwert wird basierend auf externen Informationen mit -87 imputiert.

```Python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id = 'Latitude',
                                          impute_function = dprep.ReplaceValueFunction.MEAN)

# impute with custom value -87
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=-87)
```

Imputeschritte können mit der [`Builders`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py)-Klassenfunktion [`impute_missing_values()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.builders?view=azure-dataprep-py#impute-missing-values-impute-columns--typing-list-azureml-dataprep-api-builders-imputecolumnarguments----none--group-by-columns--typing-union-typing-list-str---nonetype----none-----azureml-dataprep-api-builders-imputemissingvaluesbuilder) zu einem `ImputeMissingValuesBuilder`-Objekt verkettet werden. Der Parameter `impute_columns` akzeptiert ein Array von `ImputeColumnArguments`-Objekten.

```Python
# get instance of ImputeMissingValuesBuilder
impute_builder = ds_def.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])
```

Rufen Sie die Funktion [`learn()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#learn------none) auf, um die Imputeschritte zu speichern und dann mit [`to_dataflow()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py#to-dataflow------azureml-dataprep-api-dataflow-dataflow) auf ein Dataflow-Objekt anzuwenden.

```Python
impute_builder.learn()
ds_def = impute_builder.to_dataflow()
ds_def.head(3)
```

Wie in der folgenden Ausgabetabelle dargestellt, wurde der fehlende Breitengrad mit dem `MEAN`-Wert der Gruppe `Arrest==False` und der fehlende Längengrad mit -87 imputiert.

||ID|Verhaftung|Breitengrad|Längengrad
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

Aktualisieren Sie die Definition des Datasets mit [`update_definition()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#update-definition-definition--definition-update-message-), um die ausgeführten Transformationsschritte beizubehalten.

```Python
dataset = dataset.update_definition(ds_def, 'Impute Missing')
dataset.head(3)
```

||ID|Verhaftung|Breitengrad|Längengrad
-|---------|-----|---------|----------
0|10498554|False|41.692834|-87.604319
1|10516598|False|41.744107|-87.664494
2|10519196|False|41.780049|-87.000000

## <a name="create-assertion-rules"></a>Erstellen von Assertionregeln

Häufig sind die Daten, mit denen wir bei der Bereinigung und Aufbereitung von Daten arbeiten, nur eine Teilmenge der gesamten Daten, die wir für die Produktion benötigen. Infolgedessen können sich einige der Annahmen, die wir im Rahmen unserer Bereinigung treffen, als falsch erweisen. In einem Dataset, das ständig aktualisiert wird, kann beispielsweise eine Spalte, die ursprünglich nur Zahlen innerhalb eines bestimmten Bereichs enthielt, in späteren Ausführungen einen größeren Wertebereich enthalten. Diese Fehler führen häufig zu fehlerhaften Pipelines oder ungültigen Daten.

Datasets unterstützen das Erstellen von Assertionen für Daten, die ausgewertet werden, während die Pipeline ausgeführt wird. Mit diesen Assertionen können wir überprüfen, ob unsere Annahmen zu den Daten weiterhin zutreffend sind. Wenn dies nicht der Fall ist, können wir Fehler entsprechend behandeln.

Wenn Sie beispielsweise die Werte `Latitude` und `Longitude` in Ihrem Dataset auf bestimmte numerische Bereiche beschränken möchten, stellt die Methode [`assert_value()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#assert-value-columns--multicolumnselection--expression--azureml-dataprep-api-expressions-expression--policy--azureml-dataprep-api-engineapi-typedefinitions-assertpolicy----assertpolicy-errorvalue--1---error-code--str----assertionfailed------azureml-dataprep-api-dataflow-dataflow) sicher, dass dies immer der Fall ist.

```Python
from azureml.dataprep import value
from azureml.core.dataset import Dataset

# get the latest definition of the Dataset
ds_def = dataset.get_definition()

# set assertion rules for `Latitude` and `Longitude` columns
ds_def = ds_def.assert_value('Latitude', (value <= 90) & (value >= -90), error_code='InvalidLatitude')
ds_def = ds_def.assert_value('Longitude', (value <= 180) & (value >= -87), error_code='InvalidLongitude')

ds_def.get_profile()
```

||Type|Min|max|Count|Fehlt (Anzahl)|Fehlt nicht (Anzahl)|Fehlt (Prozent)|Fehler (Anzahl)|Leer (Anzahl)|0,1 % Quantil|1 % Quantil|5 % Quantil|25 % Quantil|50 % Quantil|75 % Quantil|95 % Quantil|99 % Quantil|99,9 % Quantil|Mittelwert|Standardabweichung|Variance|Schiefe|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Verhaftung|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Breitengrad|FieldType.DECIMAL|41.6928|41.9032|10,0|0.0|10,0|0.0|0.0|0.0|41.6928|41.7185|41.6928|41.78|41.78|41.78|41.9032|41.9032|41.9032|41.78|0.0517107|0.002674|0.837593|1.05
Längengrad|FieldType.INTEGER|-87|-87|10,0|0.0|10,0|0.0|3.0|0.0|-87|-87|-87|-87|-87|-87|-87|-87|-87|-87|0|0|NaN|NaN

Im Profil erkennen Sie, dass `Error Count` für die `Longitude`-Spalte 3 ist. Der folgende Code filtert das Dataset, ruft den Fehler ab und untersucht, welcher Wert dazu führt, dass die Assertion fehlerhaft ist. Passen Sie von hier aus Ihren Code an, und bereinigen Sie Ihre Daten entsprechend.

```Python
from azureml.dataprep import col

ds_error = ds_def.filter(col('Longitude').is_error())
error = ds_error.head(10)['Longitude'][0]

print(error.originalValue)
```

    -87.60431945

## <a name="derive-columns-by-example"></a>Ableiten von Spalten anhand eines Beispiels

Eines der erweiterten Tools für Datasets ist die Möglichkeit, Spalten anhand von Beispielen für gewünschte Ergebnisse abzuleiten. Auf diese Weise können Sie dem SDK ein Beispiel hinzufügen, damit es zur Erzielung der gewünschten Transformationen Code generieren kann.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local file
dataset = Dataset.auto_read_files('./data/crime.csv')
dataset.head(3)
```

||ID|Fallnummer|Date|Block|...|
-|---------|-----|---------|----|---
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|...
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|...
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|...

Angenommen, Sie müssen das Format für Datum und Uhrzeit in „2016-04-04 10PM-12AM“ transformieren. Geben Sie im Argument [`derive_column_by_example()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#derive-column-by-example-source-columns--sourcecolumns--new-column-name--str--example-data--exampledata-----azureml-dataprep-api-dataflow-dataflow) Beispiele für Ihre gewünschte Ausgabe im Parameter `example_data` im folgenden Format an: *(Originalausgabe, gewünschte Ausgabe)* .

Der folgende Code enthält zwei Beispiele für die gewünschte Ausgabe: ("2016-04-04 23:56:00", "2016-04-04 10PM-12AM") und ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM").

```Python
ds_def = dataset.get_definition()
ds_def = ds_def.derive_column_by_example(
        source_columns = "Date",
        new_column_name = "Date_Time_Range",
        example_data = [("2016-04-04 23:56:00", "2016-04-04 10PM-12AM"), ("2016-04-15 17:00:00", "2016-04-15 4PM-6PM")]
    )
ds_def.keep_columns(['ID','Date','Date_Time_Range']).head(3)
```

Beachten Sie, dass in der folgenden Tabelle eine neue Spalte (Date_Time_Range) Datensätze im angegebenen Format enthält.

||ID|Date|Date_Time_Range
-|--------|-----|----
0|10498554|2016-04-04 23:56:00|2016-04-04 10PM-12AM
1|10516598|2016-04-15 17:00:00|2016-04-15 4PM-6PM
2|10519196|2016-04-15 10:00:00|2016-04-15 10AM-12PM

```Python
# update Dataset definition to keep the transformation steps performed.
dataset = dataset.update_definition(ds_def, 'Derive Date_Time_Range')
```

## <a name="fuzzy-groupings"></a>Fuzzygruppierungen

Wenn Sie Daten aus verschiedenen Quellen erfassen, kann es zu Abweichungen in der Schreibweise, Groß- und Kleinschreibung oder Abkürzungen für die gleichen Entitäten kommen. Standardisieren und stimmen Sie diese Varianten automatisch mit den Funktionen für Fuzzygruppierung oder Textclustering des SDK ab.

Die Spalte `inspections.business.city` enthält beispielsweise mehrere Formen des Städtenamens „San Francisco“.

```Python
from azureml.core.dataset import Dataset

# create an in-memory Dataset from a local json file
dataset = Dataset.auto_read_files('./data/city.json')
dataset.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|...|
-|-----|-------------------------|------------|--|---
0|16162|Quick-N-Ezee Indian Foods|3861 24th St|SF|...
1|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
2|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|...
3|68701|Grindz|832 Clement St|SF|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|...

Verwenden wir die Methode [`fuzzy_group_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#fuzzy-group-column-source-column--str--new-column-name--str--similarity-threshold--float---0-8--similarity-score-column-name--str---none-----azureml-dataprep-api-dataflow-dataflow), um eine Spalte mit der automatisch erkannten kanonischen Form „San Francisco“ hinzuzufügen. Die Argumente `source_column` und `new_column_name` sind erforderlich. Außerdem sind die folgenden Optionen verfügbar:

* Erstellen Sie eine neue Spalte (`similarity_score_column_name`), die die Ähnlichkeitsbewertung für jedes Paar von Original- und kanonischen Werten anzeigt.

* Stellen Sie einen `similarity_threshold` zur Verfügung, der die minimale Ähnlichkeitsbewertung für die zu gruppierenden Werte angibt.

```Python
# get the latest Dataset definition
ds_def = dataset.get_definition()
ds_def = ds_def.fuzzy_group_column(source_column='inspections.business.city',
                                       new_column_name='city_grouped',
                                       similarity_threshold=0.8,
                                       similarity_score_column_name='similarity_score')
ds_def.head(5)
```

||inspections.business.business_id|inspections.business_name|inspections.business.address|inspections.business.city|city_grouped|similarity_score|...|
-|-----|-------------------------|------------|--|---|---|---
0|16162|Quick-N-Ezee Indian Foods|3861 24th St|SF|San Francisco|0.814806|...
1|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
2|67565|King of Thai Noodles Cafe|1541 TARAVAL St|SAN FRANCISCO|San Francisco|1.000000|...
3|68701|Grindz|832 Clement St|SF|San Francisco|0.814806|...
4|69186|Premier Catering & Events, Inc.|1255 22nd St|S.F.|San Francisco|0.814806|...

In der daraus resultierenden Dataset-Definition wurden alle verschiedenen Varianten der Darstellung von „San Francisco“ in den Daten in die gleiche Zeichenfolge „San Francisco“ normalisiert.

Speichern Sie diesen Schritt der Fuzzygruppierung mit `update_definition()` in der aktuellsten Dataset-Definition.

```Python
dataset = dataset.update_definition(ds_def, 'fuzzy grouping')
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten des Lebenszyklus von Dataset-Definitionen](how-to-manage-dataset-definitions.md).

* Ein Beispiel für ein Regressionsmodell finden Sie im [Tutorial](tutorial-auto-train-models.md) zu automatisiertem Machine Learning.

* Entwurfsmuster und Beispiele zur Verwendung finden Sie in der [Übersicht](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) zum SDK.

* Ein Beispiel für die Verwendung von Datasets finden Sie in den [Beispielnotebooks](https://aka.ms/dataset-tutorial).
