---
title: 'Schreiben: Datenvorbereitung mit dem Python SDK'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie mehr über das Schreiben von Daten mit dem Azure Machine Learning Data Prep SDK. Sie können Daten an jeder beliebigen Stelle in einem Datenfluss und in Dateien in unseren unterstützten Speicherorten (lokales Dateisystem, Azure Blob Storage und Azure Data Lake Storage) schreiben.
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
ms.openlocfilehash: 6206ad1a7356221bf94134e5d293c27d778cc187
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752868"
---
# <a name="write-and-configure-data--with-the-azure-machine-learning-data-prep-sdk"></a>Schreiben und Konfigurieren von Daten mit dem Azure Machine Learning Data Prep SDK

In diesem Artikel lernen Sie verschiedene Methoden zum Schreiben von Daten mithilfe des [Azure Machine Learning-Data Prep SDK für Python](https://aka.ms/data-prep-sdk) und Konfigurieren dieser Daten für Experimente mit dem [Azure Machine Learning SDK für Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) kennen.  Ausgabedaten können an jeder Stelle in einen Datenfluss geschrieben werden. Schreibvorgänge werden dem Ergebnisdatenfluss als Schritte hinzugefügt, und diese Schritte werden bei jedem Ausführen des Datenflusses ausgeführt. Daten werden in mehrere Partitionsdateien geschrieben, um parallele Schreibvorgänge zuzulassen.

> [!Important]
> Wenn Sie eine neue Lösung erstellen, probieren Sie die [Azure Machine Learning Datasets](how-to-explore-prepare-data.md) (Vorschau) aus, um Ihre Daten zu transformieren, Momentaufnahmen von Daten zu erstellen und mit einer Versionsangabe versehene Datasetdefinitionen zu speichern. Datasets ist die nächste Version des Datenaufbereitungs-SDK und bietet erweiterte Funktionen zum Verwalten von Datasets in KI-Lösungen.
> Wenn Sie das `azureml-dataprep`-Paket verwenden, um einen Datenfluss mit Ihren Transformationen zu erstellen, anstatt das `azureml-datasets`-Paket zum Erstellen eines Datensatzes zu verwenden, können Sie später keine Snapshots oder Datensätze mit Versionsangabe verwenden.

Da es keine Einschränkungen für die Anzahl von Schreibschritten in einer Pipeline gibt, können Sie problemlos zusätzliche Schreibschritte hinzufügen, um Zwischenergebnisse bei der Problembehandlung oder für andere Pipelines abzurufen.

Jedes Mal, wenn Sie einen Schreibschritt ausführen, wird ein vollständiger Pull für die Daten im Datenfluss ausgeführt. So wird beispielsweise ein Datenfluss mit drei Schreibschritten jeden Datensatz im Dataset dreimal lesen und verarbeiten.

## <a name="supported-data-types-and-location"></a>Unterstützte Datentypen und Speicherorte

Die folgenden Dateiformate werden unterstützt:
-   Durch Trennzeichen getrennte Dateien (CSV, TSV usw.)
-   Parquet-Dateien

Mit dem Azure Machine Learning-Data Prep SDK können Sie Daten in diese Speicherorte schreiben:
+ Ein lokales Dateisystem
+ Azure Blob Storage
+ Azure Data Lake Store

## <a name="spark-considerations"></a>Überlegungen zu Spark

Beim Ausführen eines Datenflusses in Spark müssen Sie in einen leeren Ordner schreiben. Der Versuch, in einen vorhandenen Ordner zu schreiben, führt zu einem Fehler. Stellen Sie sicher, dass Ihr Zielordner leer ist, oder verwenden Sie für jede Ausführung einen anderen Zielort, da der Schreibvorgang sonst fehlschlägt.

## <a name="monitoring-write-operations"></a>Überwachen von Schreibvorgängen

Der Einfachheit halber wird nach Abschluss des Schreibvorgangs eine Sentineldatei mit dem Namen „SUCCESS“ erzeugt. Sie hilft Ihnen zu erkennen, wann ein Zwischenschreibvorgang abgeschlossen ist, ohne dass Sie warten müssen, bis die gesamte Pipeline abgeschlossen ist.

## <a name="example-write-code"></a>Beispielhafter Schreibcode

Laden Sie in diesem Beispiel zuerst Daten mit `auto_read_file()` in einen Datenfluss. Diese Daten werden mit unterschiedlichen Formaten wiederverwendet.

```python
import azureml.dataprep as dprep
t = dprep.auto_read_file('./data/fixed_width_file.txt')
t = t.to_number('Column3')
t.head(5)
```

Beispielausgabe:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | Keine | NO | NO | ENRS | NaN | NaN | NaN |   
|1| 10003,0 | 99999,0 | Keine | NO | NO | ENSO | NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | Keine | NO | JN | ENJA | 70933,0 | –8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | Keine | NO | NO |      | NaN | NaN | NaN |
|4| 10014,0 | 99999,0 | Keine | NO | NO | ENSO | 59783,0 | 5350,0 |  500,0|

### <a name="delimited-file-example"></a>Beispiel für eine durch Trennzeichen getrennte Datei

Der folgende Code verwendet die [`write_to_csv()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-csv-directory-path--datadestination--separator--str--------na--str----na---error--str----error------azureml-dataprep-api-dataflow-dataflow)-Funktion zum Schreiben von Daten in eine durch Trennzeichen getrennte Datei.

```python
# Create a new data flow using `write_to_csv` 
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'))

# Run the data flow to begin the write operation.
write_t.run_local()

written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Beispielausgabe:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | ERROR | NO | NO | ENRS | NaN    | NaN | NaN |   
|1| 10003,0 | 99999,0 | ERROR | NO | NO | ENSO |    NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | ERROR | NO | JN | ENJA |    70933,0 | –8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | ERROR | NO | NO |     | NaN | NaN | NaN |
|4| 10014,0 | 99999,0 | ERROR | NO | NO | ENSO |    59783,0 | 5350,0 |  500,0|

In der vorhergehenden Ausgabe treten mehrere Fehler in den numerischen Spalten auf, weil Zahlen nicht korrekt analysiert wurden. Beim Schreiben in eine CSV-Datei werden Nullwerte standardmäßig durch die Zeichenfolge „ERROR“ ersetzt.

Fügen Sie Parameter als Teil Ihres Schreibaufrufs hinzu, und geben Sie eine Zeichenfolge an, die zur Darstellung von Nullwerten verwendet werden soll.

```python
write_t = t.write_to_csv(directory_path=dprep.LocalFileOutput('./test_out/'), 
                         error='BadData',
                         na='NA')
write_t.run_local()
written_files = dprep.read_csv('./test_out/part-*')
written_files.head(5)
```

Der vorhergehende Code gibt folgende Ausgabe zurück:

| | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |
|0| 10000,0 | 99999,0 | BadData | NO | NO | ENRS | NaN  | NaN | NaN |   
|1| 10003,0 | 99999,0 | BadData | NO | NO | ENSO |  NaN | NaN | NaN |   
|2| 10010,0 | 99999,0 | BadData | NO | JN | ENJA |  70933,0 | –8667,0 | 90,0 |
|3| 10013,0 | 99999,0 | BadData | NO | NO |   | NaN | NaN | NaN |
|4| 10014,0 | 99999,0 | BadData | NO | NO | ENSO |  59783,0 | 5350,0 |  500,0|

### <a name="parquet-file-example"></a>Beispiel für eine Parquet-Datei

Ähnlich wie `write_to_csv()` gibt die [`write_to_parquet()`](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.dataflow#write-to-parquet-file-path--typing-union--datadestination--nonetype----none--directory-path--typing-union--datadestination--nonetype----none--single-file--bool---false--error--str----error---row-groups--int---0-----azureml-dataprep-api-dataflow-dataflow)-Funktion einen neuen Datenfluss zurück, der einen Schritt mit einem Schreibvorgang in eine Parquet-Datei enthält, der beim Ausführen des Datenflusses ausgeführt wird.

```python
write_parquet_t = t.write_to_parquet(directory_path=dprep.LocalFileOutput('./test_parquet_out/'),
error='MiscreantData')
```

Führen Sie den Datenfluss aus, um den Schreibvorgang zu starten.

```python
write_parquet_t.run_local()

written_parquet_files = dprep.read_parquet_file('./test_parquet_out/part-*')
written_parquet_files.head(5)
```

Der vorhergehende Code gibt folgende Ausgabe zurück:

|   | Column1 | Column2 | Column3 | Column4 | Column5 | Column6 | Column7 | Column8 | Column9 |
| -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- | -------- |-------- |
|0| 10000,0 | 99999,0 | MiscreantData | NO | NO | ENRS | MiscreantData | MiscreantData | MiscreantData |
|1| 10003,0 | 99999,0 | MiscreantData | NO | NO | ENSO | MiscreantData | MiscreantData | MiscreantData |   
|2| 10010,0 | 99999,0 | MiscreantData | NO| JN| ENJA|   70933,0|    –8667,0 |90,0|
|3| 10013,0 | 99999,0 | MiscreantData | NO| NO| |   MiscreantData|    MiscreantData|    MiscreantData|
|4| 10014,0 | 99999,0 | MiscreantData | NO| NO| ENSO|   59783,0|    5350,0| 500,0|

## <a name="configure-data-for-automated-machine-learning-training"></a>Konfigurieren von Daten für automatisiertes Training mit maschinellem Lernen

Übergeben Sie Ihre neu geschriebene Datendatei in ein [`AutoMLConfig`](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#automlconfig)-Objekt zur Vorbereitung für das automatisierte Training mit maschinellem Lernen. 

Im folgenden Codebeispiel wird veranschaulicht, wie Sie Ihren Datenfluss in einen Pandas-Datenrahmen konvertieren und anschließend in Trainings- und Testdatasets für automatisiertes Training mit maschinellem Lernen aufteilen.

```Python
from azureml.train.automl import AutoMLConfig
from sklearn.model_selection import train_test_split

dflow = dprep.auto_read_file(path="")
X_dflow = dflow.keep_columns([feature_1,feature_2, feature_3])
y_dflow = dflow.keep_columns("target")

X_df = X_dflow.to_pandas_dataframe()
y_df = y_dflow.to_pandas_dataframe()

X_train, X_test, y_train, y_test = train_test_split(X_df, y_df, test_size=0.2, random_state=223)

# flatten y_train to 1d array
y_train.values.flatten()

#configure 
automated_ml_config = AutoMLConfig(task = 'regression',
                               X = X_train.values,  
                   y = y_train.values.flatten(),
                   iterations = 30,
                       Primary_metric = "AUC_weighted",
                       n_cross_validation = 5
                       )

```

Wenn Sie nicht wie im vorherigen Beispiel Zwischenschritte zur Datenvorbereitung benötigen, können Sie Ihren Datenfluss direkt in `AutoMLConfig` übergeben.

```Python
automated_ml_config = AutoMLConfig(task = 'regression', 
                   X = X_dflow,   
                   y = y_dflow, 
                   iterations = 30, 
                   Primary_metric = "AUC_weighted",
                   n_cross_validation = 5
                   )
```

## <a name="next-steps"></a>Nächste Schritte
* Entwurfsmuster und Beispiele zur Verwendung finden Sie in der [Übersicht](https://aka.ms/data-prep-sdk) zum SDK. 
* Ein Beispiel für ein Regressionsmodell finden Sie im [Tutorial](tutorial-auto-train-models.md) zu automatisiertem maschinellem Lernen.
