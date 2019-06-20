---
title: 'Transformation: Datenvorbereitung mit dem Python SDK'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie mehr über das Transformieren und Bereinigen von Daten mit dem Azure Machine Learning Data Prep SDK. Verwenden Sie Transformationsmethoden, um Spalten hinzuzufügen, nicht benötigte Zeilen oder Spalten herauszufiltern und fehlende Werte zu imputieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: db23c8af7eaa4a86691ccb0bb831ce2cc28d635c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65471834"
---
# <a name="transform-data-with-the-azure-machine-learning-data-prep-sdk"></a>Transformieren von Daten mit dem Azure Machine Learning Data Prep SDK

In diesem Artikel lernen Sie verschiedene Methoden zum Transformieren von Daten mit dem `azureml-dataprep`-Paket kennen. Das Paket enthält Funktionen zum einfachen Hinzufügen von Spalten, Herausfiltern von nicht benötigten Zeilen oder Spalten und Imputieren von fehlenden Werten. Beachten Sie die vollständige Referenzdokumentation für das [Paket „azureml-dataprep“](https://aka.ms/data-prep-sdk).

> [!Important]
> Wenn Sie eine neue Lösung erstellen, probieren Sie die [Azure Machine Learning Datasets](how-to-explore-prepare-data.md) (Vorschau) aus, um Ihre Daten zu transformieren, Momentaufnahmen von Daten zu erstellen und mit einer Versionsangabe versehene Datasetdefinitionen zu speichern. Datasets ist die nächste Version des Datenaufbereitungs-SDK und bietet erweiterte Funktionen zum Verwalten von Datasets in KI-Lösungen. Wenn Sie das `azureml-dataprep`-Paket verwenden, um einen Datenfluss mit Ihren Transformationen zu erstellen, anstatt das `azureml-datasets`-Paket zum Erstellen eines Datensatzes zu verwenden, können Sie später keine Snapshots oder Datensätze mit Versionsangabe verwenden.

In dieser Vorgehensweise finden Sie Beispiele für die folgenden Aufgaben:

- Hinzufügen einer Spalte mithilfe eines Ausdrucks
- [Imputieren von fehlenden Werten](#impute-missing-values)
- [Ableiten einer Spalte nach Beispiel](#derive-column-by-example)
- [Filterung](#filtering)
- [Benutzerdefinierte Python-Transformationen](#custom-python-transforms)

## <a name="add-column-using-an-expression"></a>Hinzufügen einer Spalte mithilfe eines Ausdrucks

Das Azure Machine Learning Data Prep SDK enthält `substring`-Ausdrücke, mit denen Sie einen Wert aus vorhandenen Spalten berechnen und anschließend in eine neue Spalte einfügen können. In diesem Beispiel werden Daten geladen, und es wird versucht, diesen Eingabedaten Spalten hinzuzufügen.

```Python
import azureml.dataprep as dprep

# loading data
dflow = dprep.read_csv(path=r'data\crime0-10.csv')
dflow.head(3)
```

||ID|Fallnummer|Date|Block|IUCR|Primärer Typ|BESCHREIBUNG|Standortbeschreibung|Verhaftung|Inland|...|Bezirk|Gemeindebezirk|FBI-Code|x-Koordinate|y-Koordinate|Jahr|Aktualisiert am|Breitengrad|Längengrad|Location|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
|0|10140490|HY329907|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


Verwenden Sie den Ausdruck `substring(start, length)`, um das Präfix aus der Spalte „Fallnummer“ zu extrahieren, und fügen Sie diese Zeichenfolge in eine neue Spalte `Case Category` ein. Durch Übergeben der Variablen `substring_expression` an den Parameter `expression` wird eine neue berechnete Spalte erstellt, die den Ausdruck für jeden Datensatz ausführt.

```Python
substring_expression = dprep.col('Case Number').substring(0, 2)
case_category = dflow.add_column(new_column_name='Case Category',
                                    prior_column='Case Number',
                                    expression=substring_expression)
case_category.head(3)
```

||ID|Fallnummer|Fallkategorie|Date|Block|IUCR|Primärer Typ|BESCHREIBUNG|Standortbeschreibung|Verhaftung|Inland|...|Bezirk|Gemeindebezirk|FBI-Code|x-Koordinate|y-Koordinate|Jahr|Aktualisiert am|Breitengrad|Längengrad|Location|
|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|------|
|0|10140490|HY329907|HY|07/05/2015 11:50:00 PM|050XX N NEWLAND AVE|0820|THEFT|$500 AND UNDER|STREET|false|false|...|41|10|06|1129230|1933315|2015|07/12/2015 12:42:46 PM|41.973309466|-87.800174996|(41.973309466, -87.800174996)|
|1|10139776|HY329265|HY|07/05/2015 11:30:00 PM|011XX W MORSE AVE|0460|BATTERY|SIMPLE|STREET|false|true|...|49|1|08B|1167370|1946271|2015|07/12/2015 12:42:46 PM|42.008124017|-87.65955018|(42.008124017, -87.65955018)|
|2|10140270|HY329253|HY|07/05/2015 11:20:00 PM|121XX S FRONT AVE|0486|BATTERY|DOMESTIC BATTERY SIMPLE|STREET|false|true|...|9|53|08B|||2015|07/12/2015 12:42:46 PM|


Verwenden Sie den Ausdruck `substring(start)`, um nur die Zahl aus der Spalte „Fallnummer“ zu extrahieren und eine neue Spalte zu erstellen. Konvertieren Sie sie mithilfe der Funktion `to_number()` in einen numerischen Datentyp, und übergeben Sie den Zeichenfolgenspaltennamen als Parameter.

```Python
substring_expression2 = dprep.col('Case Number').substring(2)
case_id = dflow.add_column(new_column_name='Case Id',
                              prior_column='Case Number',
                              expression=substring_expression2)
case_id = case_id.to_number('Case Id')
```

## <a name="impute-missing-values"></a>Imputieren von fehlenden Werten

Das SDK kann fehlende Werte in den angegebenen Spalten imputieren. In diesem Beispiel laden Sie Werte für Breiten- und Längengrade und imputieren dann fehlende Werte in den Eingabedaten.

```python
import azureml.dataprep as dprep

# loading input data
dflow = dprep.read_csv(r'data\crime0-10.csv')
dflow = dflow.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])
dflow = dflow.to_number(['Latitude', 'Longitude'])
dflow.head(3)
```

||ID|Verhaftung|Breitengrad|Längengrad|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|NaN|NaN|

Im dritten Datensatz fehlen Werte für Breiten- und Längengrade. Zum Imputieren dieser fehlenden Werte verwenden Sie [`ImputeMissingValuesBuilder`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.api.builders.imputemissingvaluesbuilder?view=azure-dataprep-py), um einen festen Ausdruck zu lernen. Sie können die Spalten entweder mit einem berechneten `MIN`-, `MAX`- oder `MEAN`-Wert oder einem `CUSTOM`-Wert imputieren. Wenn `group_by_columns` angegeben ist, werden fehlende Werte nach Gruppe imputiert, wobei `MIN`, `MAX`, und `MEAN` pro Gruppe berechnet werden.

Überprüfen Sie den Wert `MEAN` der Spalte für den Breitengrad mithilfe der [`summarize()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#summarize-summary-columns--typing-union-typing-list-azureml-dataprep-api-dataflow-summarycolumnsvalue---nonetype----none--group-by-columns--typing-union-typing-list-str---nonetype----none--join-back--bool---false--join-back-columns-prefix--typing-union-str--nonetype----none-----azureml-dataprep-api-dataflow-dataflow)-Funktion. Diese Funktion akzeptiert ein Array von Spalten im Parameter `group_by_columns` zur Festlegung der Aggregationsebene. Der Parameter `summary_columns` akzeptiert einen `SummaryColumnsValue`-Aufruf. Mit diesem Funktionsaufruf wird der aktuelle Spaltenname, der Name des neuen berechneten Felds sowie die auszuführende `SummaryFunction` festlegt.

```python
dflow_mean = dflow.summarize(group_by_columns=['Arrest'],
                       summary_columns=[dprep.SummaryColumnsValue(column_id='Latitude',
                                                                 summary_column_name='Latitude_MEAN',
                                                                 summary_function=dprep.SummaryFunction.MEAN)])
dflow_mean = dflow_mean.filter(dprep.col('Arrest') == 'false')
dflow_mean.head(1)
```

||Verhaftung|Latitude_MEAN|
|-----|-----|----|
|0|false|41.878961|

Der `MEAN`-Wert der Breitengrade sieht korrekt aus, verwenden Sie die Funktion `ImputeColumnArguments`, um ihn zu imputieren. Diese Funktion akzeptiert eine `column_id`-Zeichenfolge und eine `ReplaceValueFunction` zum Festlegen des Impute-Typs. Der fehlende Längengradwert wird basierend auf externen Informationen mit 42 imputiert.

Impute-Schritte können mit der Generatorfunktion `impute_missing_values()` zu einem `ImputeMissingValuesBuilder`-Objekt zusammengefügt werden. Der Parameter `impute_columns` akzeptiert ein Array von `ImputeColumnArguments`-Objekten. Rufen Sie die Funktion `learn()` auf, um die Impute-Schritte zu speichern und dann mit `to_dataflow()` auf ein Dataflow-Objekt anzuwenden.

```python
# impute with MEAN
impute_mean = dprep.ImputeColumnArguments(column_id='Latitude',
                                          impute_function=dprep.ReplaceValueFunction.MEAN)
# impute with custom value 42
impute_custom = dprep.ImputeColumnArguments(column_id='Longitude',
                                            custom_impute_value=42)
# get instance of ImputeMissingValuesBuilder
impute_builder = dflow.builders.impute_missing_values(impute_columns=[impute_mean, impute_custom],
                                                   group_by_columns=['Arrest'])

impute_builder.learn()
dflow_imputed = impute_builder.to_dataflow()
dflow_imputed.head(3)
```

||ID|Verhaftung|Breitengrad|Längengrad|
|-----|------|-----|------|-----|
|0|10140490|false|41.973309|-87.800175|
|1|10139776|false|42.008124|-87.659550|
|2|10140270|false|41.878961|42.000000|

Wie im oben genannten Ergebnis gezeigt wird, wurde der fehlende Breitengrad mit dem Wert `MEAN` der Gruppe `Arrest=='false'` imputiert. Der fehlende Längengrad wurde mit 42 imputiert.

```python
imputed_longitude = dflow_imputed.to_pandas_dataframe()['Longitude'][2]
assert imputed_longitude == 42
```

## <a name="derive-column-by-example"></a>Ableiten von Spalten anhand eines Beispiels

Zu den erweiterten Tools im Azure Machine Learning Data Prep SDK zählt die Möglichkeit, Spalten anhand von Beispielen für die gewünschten Ergebnisse abzuleiten. Auf diese Weise können Sie dem SDK ein Beispiel hinzufügen, damit es zur Erzielung der gewünschten Transformation Code generieren kann.

```python
import azureml.dataprep as dprep
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/BostonWeather.csv')
dflow.head(4)
```

||DATE|REPORTTPYE|HOURLYDRYBULBTEMPF|HOURLYRelativeHumidity|HOURLYWindSpeed|
|----|----|----|----|----|----|
|0|1/1/2015 0:54|FM-15|22|50|10|
|1|1/1/2015 1:00|FM-12|22|50|10|
|2|1/1/2015 1:54|FM-15|22|50|10|
|3|1/1/2015 2:54|FM-15|22|50|11|

Es wird davon ausgegangen, dass Sie diese Datei mit einem Dataset verknüpfen müssen, in dem Datum und Uhrzeit im Format „Mar 10, 2018 | 2AM-4AM“ angegeben sind.

```python
builder = dflow.builders.derive_column_by_example(source_columns=['DATE'], new_column_name='date_timerange')
builder.add_example(source_data=dflow.iloc[1], example_value='Jan 1, 2015 12AM-2AM')
builder.preview(count=5) 
```

||DATE|date_timerange|
|----|----|----|
|0|1/1/2015 0:54|Jan 1, 2015 12AM-2AM|
|1|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|
|2|1/1/2015 1:54|Jan 1, 2015 12AM-2AM|
|3|1/1/2015 2:54|Jan 1, 2015 2AM-4AM|
|4|1/1/2015 3:54|Jan 1, 2015 2AM-4AM|

Der obige Code erstellt zunächst einen Generator für die abgeleitete Spalte. Sie geben ein Array der zu berücksichtigenden Quellspalten (`DATE`) und einen Namen für die neue Spalte an, die hinzugefügt werden soll. Im ersten Beispiel übergeben Sie die zweite Zeile (Index 1) und einen erwarteten Wert für die abgeleitete Spalte an.

Zu guter Letzt rufen Sie `builder.preview(skip=30, count=5)` auf und können neben der Quellspalte die abgeleitete Spalte sehen. Das Format wirkt auf den ersten Blick richtig, Sie sehen jedoch nur Werte für das gleiche Datum „Jan 1, 2015“.

Übergeben Sie nun die Anzahl der Zeilen, die Sie vom Anfang aus überspringen möchten (`skip`), um die Zeilen weiter unten zu sehen.

> [!NOTE]
> Die preview()-Funktion überspringt Zeilen, nummeriert jedoch den Ausgabeindex nicht neu. Im nachstehenden Beispiel entspricht der Index 0 in der Tabelle dem Index 30 im Datenfluss.

```python
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Feb 1, 2015 12AM-2AM|
|4|1/2/2015 1:00|Feb 1, 2015 12AM-2AM|

Hier können Sie ein Problem mit dem generierten Programm sehen: Das abgeleitete Programm, das ausschließlich auf dem oben angegebenen Beispiel basiert, hat das Datum im Format „Tag/Monat/Jahr“ analysiert, was in diesem Fall jedoch nicht gewünscht ist. Um dieses Problem zu beheben, legen Sie einen bestimmten Datensatzindex als Ziel fest, und geben Sie ein weiteres Beispiel unter Verwendung der `add_example()`-Funktion mit der Variable `builder` an.

```python
builder.add_example(source_data=dflow.iloc[3], example_value='Jan 2, 2015 12AM-2AM')
builder.preview(skip=30, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/1/2015 22:54|Jan 1, 2015 10PM-12AM|
|1|1/1/2015 23:54|Jan 1, 2015 10PM-12AM|
|2|1/1/2015 23:59|Jan 1, 2015 10PM-12AM|
|3|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|
|4|1/2/2015 1:00|Jan 2, 2015 12AM-2AM|

Nun verarbeiten die Zeilen „1/2/2015“ ordnungsgemäß als „Jan 2, 2015“. Wenn Sie sich jedoch den Bereich nach Index 76 der abgeleiteten Spalte ansehen, sehen Sie, dass am Ende der abgeleiteten Spalte keine Werte angezeigt werden.

```python
builder.preview(skip=75, count=5)
```


||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-8AM|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-8AM|
|2|1/29/2015 6:54|Keine|
|3|1/29/2015 7:00|Keine|
|4|1/29/2015 7:54|Keine|

```python
builder.add_example(source_data=dflow.iloc[77], example_value='Jan 29, 2015 6AM-8AM')
builder.preview(skip=75, count=5)
```

||DATE|date_timerange|
|-----|-----|-----|
|0|1/3/2015 7:00|Jan 3, 2015 6AM-8AM|
|1|1/3/2015 7:54|Jan 3, 2015 6AM-8AM|
|2|1/29/2015 6:54|Jan 29, 2015 6AM-8AM|
|3|1/29/2015 7:00|Jan 29, 2015 6AM-8AM|
|4|1/29/2015 7:54|Jan 29, 2015 6AM-8AM|

 Rufen Sie `list_examples()` für das Generatorobjekt auf, um eine Liste der aktuellen Beispielableitungen anzuzeigen.

```python
examples = builder.list_examples()
```

| |DATE|Beispiel|example_id|
| -------- | -------- | -------- | -------- |
|0|1/1/2015 1:00|Jan 1, 2015 12AM-2AM|-1|
|1|1/2/2015 0:54|Jan 2, 2015 12AM-2AM|-2|
|2|1/29/2015 20:54|Jan 29, 2015 8PM-10PM|-3|


Wenn Sie fehlerhafte Beispiele löschen möchten, können Sie in bestimmten Fällen `example_row` aus dem Pandas-DataFrame oder den Wert `example_id` übergeben. Wenn Sie beispielsweise `builder.delete_example(example_id=-1)` ausführen, wird das erste Transformationsbeispiel gelöscht.


Rufen Sie `to_dataflow()` im Generator auf. Damit wird ein Datenfluss zurückgegeben, dem die gewünschten abgeleiteten Spalten hinzugefügt wurden.

```python
dflow = builder.to_dataflow()
df = dflow.to_pandas_dataframe()
```

## <a name="filtering"></a>Filterung

Das SDK enthält die Methoden [`drop_columns()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#drop-columns-columns--multicolumnselection-----azureml-dataprep-api-dataflow-dataflow) und [`filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py), mit denen Sie Spalten oder Zeilen herausfiltern können.

### <a name="initial-setup"></a>Anfangssetup

```python
import azureml.dataprep as dprep
from datetime import datetime
dflow = dprep.read_csv(path='https://dprepdata.blob.core.windows.net/demo/green-small/*')
dflow.head(5)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Store_and_fwd_flag|RateCodeID|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|N|1|0|0|0|0|1|.00|0|0|21.25|
|2|2013-08-01 09:13:00|2013-08-01 11:38:00|N|1|0|0|0|0|2|.00|0|0|75|
|3|2013-08-01 09:48:00|2013-08-01 09:49:00|N|5|0|0|0|0|1|.00|0|1|2.1|
|4|2013-08-01 10:38:35|2013-08-01 10:38:51|N|1|0|0|0|0|1|.00|0|0|3.25|

### <a name="filtering-columns"></a>Filtern von Spalten

Um Spalten zu filtern, verwenden Sie `drop_columns()`. Diese Methode akzeptiert eine Liste von zu löschenden Spalten oder ein komplexeres Argument mit der Bezeichnung [`ColumnSelector`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.columnselector?view=azure-dataprep-py).

#### <a name="filtering-columns-with-list-of-strings"></a>Filtern von Spalten mit einer Liste von Zeichenfolgen

In diesem Beispiel akzeptiert `drop_columns()` eine Liste von Zeichenfolgen. Jede Zeichenfolge sollte exakt der zu löschenden Spalte entsprechen.

```python
dflow = dflow.drop_columns(['Store_and_fwd_flag', 'RateCodeID'])
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Pickup_longitude|Pickup_latitude|Dropoff_longitude|Dropoff_latitude|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|Keine|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|0|0|0|0|1|.00|0|0|21.25|

#### <a name="filtering-columns-with-regex"></a>Filtern von Spalten mit RegEx

Alternativ können Sie den Ausdruck `ColumnSelector` verwenden, um Spalten zu löschen, die einem RegEx-Ausdruck entsprechen. In diesem Beispiel löschen Sie alle Spalten, die dem Ausdruck `Column*|.*longitude|.*latitude` entsprechen.

```python
dflow = dflow.drop_columns(dprep.ColumnSelector('Column*|.*longitud|.*latitude', True, True))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|Keine|Keine|Keine|Keine|Keine|Keine|Keine|
|1|2013-08-01 08:14:37|2013-08-01 09:09:06|1|.00|0|0|21.25|

## <a name="filtering-rows"></a>Filtern von Zeilen

Verwenden Sie zum Filtern von Zeilen `filter()`. Bei dieser Methode wird ein Azure Machine Learning Data Prep SDK-Ausdruck als Argument verwendet und ein neuer Datenfluss mit den Zeilen zurückgegeben, die der Ausdruck als „True“ auswertet. Ausdrücke werden mithilfe von Ausdrucks-Generatoren (`col`, `f_not`, `f_and`, `f_or`) und regulären Operatoren (>, <, >=, <=, ==, !=) erstellt.

### <a name="filtering-rows-with-simple-expressions"></a>Filtern von Zeilen mit einfachen Ausdrücken

Verwenden Sie den Ausdrucksgenerator `col`, und geben Sie den Namen der Spalte als Zeichenfolgenargument `col('column_name')` an. Verwenden Sie diesen Ausdruck in Verbindung mit einer der folgenden Standardoperatoren (>, <, >=, <=, ==, !=), um einen Ausdruck wie z. B. `col('Tip_amount') > 0` zu erstellen. Übergeben Sie abschließend den erstellten Ausdruck an die Funktion `filter()`.

In diesem Beispiel gibt `dflow.filter(col('Tip_amount') > 0)` einen neuen Datenfluss mit den Zeilen zurück, bei denen der Wert von `Tip_amount` größer als 0 ist.

> [!NOTE] 
> `Tip_amount` wird zuerst in einen numerischen Wert konvertiert, damit wir einen Ausdruck zum Vergleich mit anderen numerischen Werten erstellen können.

```python
dflow = dflow.to_number(['Tip_amount'])
dflow = dflow.filter(dprep.col('Tip_amount') > 0)
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-01 19:33:28|2013-08-01 19:35:21|5|.00|0.08|0|4.58|
|1|2013-08-05 13:16:38|2013-08-05 13:18:24|1|.00|0,30|0|3.8|

### <a name="filtering-rows-with-complex-expressions"></a>Filtern von Zeilen mit komplexen Ausdrücken

Um anhand von komplexen Ausdrücken zu filtern, kombinieren Sie mindestens einen einfachen Ausdruck mit den Ausdrucks-Generatoren `f_not`, `f_and` oder `f_or`.

In diesem Beispiel gibt `dflow.filter()` einen neuen Datenfluss mit den Zeilen zurück, bei denen `'Passenger_count'` kleiner als 5 und `'Tolls_amount'` größer als 0 ist.

```python
dflow = dflow.to_number(['Passenger_count', 'Tolls_amount'])
dflow = dflow.filter(dprep.f_and(dprep.col('Passenger_count') < 5, dprep.col('Tolls_amount') > 0))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-08 12:16:00|2013-08-08 12:16:00|1.0|.00|2,25|5.00|19.75|
|1|2013-08-12 14:43:53|2013-08-12 15:04:50|1.0|5.28|6.46|5.33|32.29|

Es ist auch möglich, Zeilen mit mehr als einem Ausdrucks-Generator zu filtern, um einen geschachtelten Ausdruck zu erstellen.

> [!NOTE]
> `lpep_pickup_datetime` und `Lpep_dropoff_datetime` werden zuerst in einen datetime-Wert konvertiert, damit wir einen Ausdruck zum Vergleich mit anderen datetime-Werten erstellen können.

```python
dflow = dflow.to_datetime(['lpep_pickup_datetime', 'Lpep_dropoff_datetime'], ['%Y-%m-%d %H:%M:%S'])
dflow = dflow.to_number(['Total_amount', 'Trip_distance'])
mid_2013 = datetime(2013,7,1)
dflow = dflow.filter(
    dprep.f_and(
        dprep.f_or(
            dprep.col('lpep_pickup_datetime') > mid_2013,
            dprep.col('Lpep_dropoff_datetime') > mid_2013),
        dprep.f_and(
            dprep.col('Total_amount') > 40,
            dprep.col('Trip_distance') < 10)))
dflow.head(2)
```

||lpep_pickup_datetime|Lpep_dropoff_datetime|Passenger_count|Trip_distance|Tip_amount|Tolls_amount|Total_amount|
|-----|-----|-----|-----|-----|-----|-----|-----|
|0|2013-08-13 06:11:06+00:00|2013-08-13 06:30:28+00:00|1.0|9.57|7.47|5.33|44.80|
|1|2013-08-23 12:28:20+00:00|2013-08-23 12:50:28+00:00|2.0|8,22|8.08|5.33|40.41|

## <a name="custom-python-transforms"></a>Benutzerdefinierte Python-Transformationen

Es wird immer Szenarien geben, in denen die einfachste Möglichkeit, eine Transformation durchzuführen, das Schreiben eines eigenen Skripts ist. Das SDK bietet drei Erweiterungspunkte, die Sie für benutzerdefinierte Python-Scripts verwenden können.

- Neue Skriptspalte
- Neuer Skriptfilter
- Transformieren der Partition

Jede der Erweiterungen wird in der Laufzeit zum zentralen Hochskalieren und horizontalen Skalieren unterstützt. Ein wichtiger Vorteil bei der Verwendung dieser Erweiterungspunkte ist, dass Sie nicht alle Daten abrufen müssen, um einen Datenframe zu erstellen. Ihr benutzerdefinierter Python-Code wird wie bei anderen Transformationen im großen Maßstab, partitionsweise und in der Regel parallel ausgeführt.

### <a name="initial-data-preparation"></a>Anfängliche Datenvorbereitung

Laden Sie zunächst einige Daten aus Azure Blob Storage.

```python
import azureml.dataprep as dprep
col = dprep.col

dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv', skip_rows=1)
dflow.head(2)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|ALABAMA|1|101710|Hale County|10171002158| |
|1|ALABAMA|1|101710|Hale County|10171002162| |

Kürzen Sie das Dataset, und führen Sie einige grundlegende Transformationen durch, einschließlich Entfernen von Spalten, Ersetzen von Werten und Konvertieren von Typen.

```python
dflow = dflow.keep_columns(['stnam', 'leanm10', 'ncessch', 'MAM_MTH00numvalid_1011'])
dflow = dflow.replace_na(columns=['leanm10', 'MAM_MTH00numvalid_1011'], custom_na_list='.')
dflow = dflow.to_number(['ncessch', 'MAM_MTH00numvalid_1011'])
dflow.head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1.017100e+10|Keine|
|1|ALABAMA|Hale County|1.017100e+10|Keine|

Suchen Sie mithilfe des folgenden Filters nach NULL-Werten.

```python
dflow.filter(col('MAM_MTH00numvalid_1011').is_null()).head(2)
```

| |stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1.017100e+10|Keine|
|1|ALABAMA|Hale County|1.017100e+10|Keine|

### <a name="transform-partition"></a>Transformieren der Partition

Verwenden Sie [`transform_partition()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#transform-partition-script--str-----azureml-dataprep-api-dataflow-dataflow), um alle NULL-Werte durch eine 0 zu ersetzen. Dieser Code wird nicht für das gesamte Dataset gleichzeitig ausgeführt, sondern partitionsweise. Das heißt, dass dieser Code bei einem großen Dataset parallel ausgeführt werden kann, während die Laufzeit die Daten partitionsweise verarbeitet.

Das Python-Skript muss eine Funktion namens `transform()` definieren, die zwei Argumente akzeptiert, `df` und `index`. Das Argument `df` ist ein Pandas-Datenrahmen, der die Daten für die Partition enthält, und das Argument `index` ist ein eindeutiger Bezeichner der Partition. Die Transform-Funktion kann die im Datenrahmen übergebenen Daten vollständig bearbeiten, muss aber einen Datenrahmen zurückgeben. Alle Bibliotheken, die das Python-Skript importiert, müssen in der Umgebung vorhanden sein, in der der Dataflow ausgeführt wird.

```python
df = df.transform_partition("""
def transform(df, index):
    df['MAM_MTH00numvalid_1011'].fillna(0,inplace=True)
    return df
""")
df.head(2)
```

||stnam|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|
|0|ALABAMA|Hale County|1.017100e+10|0.0|
|1|ALABAMA|Hale County|1.017100e+10|0.0|

### <a name="new-script-column"></a>Neue Skriptspalte

Sie können mit einen Python-Skript eine neue Spalte erstellen, die den Namen des Countys und den des Bundesstaats enthält und den Namen des Bundesstaats mit einem großen Anfangsbuchstaben schreibt. Verwenden Sie hierfür die [`new_script_column()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-column-new-column-name--str--insert-after--str--script--str-----azureml-dataprep-api-dataflow-dataflow)-Methode für den Datenfluss.

Das Python-Skript muss eine Funktion namens `newvalue()` definieren, die ein einzelnes `row`-Argument akzeptiert. Das Argument `row` ist ein Wörterbuch (`key`:Spaltenname, `val`: aktueller Wert) und wird für jede Zeile im Dataset an diese Funktion übergeben. Diese Funktion muss einen Wert zurückgeben, der in der neuen Spalte verwendet wird. Alle Bibliotheken, die das Python-Skript importiert, müssen in der Umgebung vorhanden sein, in der der Dataflow ausgeführt wird.

```python
dflow = dflow.new_script_column(new_column_name='county_state', insert_after='leanm10', script="""
def newvalue(row):
    return row['leanm10'] + ', ' + row['stnam'].title()
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|
|1|ALABAMA|Hale County|Hale County, Alabama|1.017100e+10|0.0|

### <a name="new-script-filter"></a>Neuer Skriptfilter

Erstellen Sie einen Python-Ausdruck mit [`new_script_filter()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow?view=azure-dataprep-py#new-script-filter-script--str-----azureml-dataprep-api-dataflow-dataflow), um das Dataset nur auf die Zeilen zu filtern, in denen „Hale“ nicht in der neuen Spalte `county_state` enthalten ist. Der Ausdruck gibt `True` zurück, wenn die Zeile beibehalten werden soll, und `False`, um die Zeile zu löschen.

```python
dflow = dflow.new_script_filter("""
def includerow(row):
    val = row['county_state']
    return 'Hale' not in val
""")
dflow.head(2)
```

||stnam|leanm10|county_state|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|
|0|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|1.0|
|1|ALABAMA|Jefferson County|Jefferson County, Alabama|1.019200e+10|0.0|

## <a name="next-steps"></a>Nächste Schritte

* Ein Beispiel zum Lösen eines spezifischen Szenarios finden Sie im [Tutorial](tutorial-data-prep.md) zum Azure Machine Learning-Data Prep SDK.
