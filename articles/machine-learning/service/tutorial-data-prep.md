---
title: 'Regressionsmodelltutorial: Vorbereiten von Daten'
titleSuffix: Azure Machine Learning service
description: Im ersten Teil dieses Tutorials erfahren Sie, wie Sie Daten in Python für die Regressionsmodellierung mit dem Azure Machine Learning SDK vorbereiten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sihhu
ms.author: MayMSFT
ms.reviewer: trbye
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: 8b7bf5a0ece3927c7dbafdec9716b7c6f8dfbc0e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474710"
---
# <a name="tutorial-prepare-data-for-regression-modeling"></a>Tutorial: Vorbereiten von Daten für die Regressionsmodellierung

In diesem Tutorial erfahren Sie, wie Sie Daten mit dem [Datenvorbereitungspaket](https://aka.ms/data-prep-sdk) aus dem [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) für die Regressionsmodellierung vorbereiten. Sie führen verschiedene Transformationen durch, um zwei Datasets für New Yorker Taxis zu filtern und miteinander zu kombinieren.

Dieses Tutorial ist der **erste Teil einer zweiteiligen Reihe**. Nach Abschluss der Tutorialreihe können Sie die Kosten für eine Taxifahrt prognostizieren, indem Sie ein Modell mit Datenfeatures trainieren. Zu den Features zählen der Abholzeitpunkt (Tag und Uhrzeit), die Anzahl der Fahrgäste und der Abholort.

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Einrichten einer Python-Umgebung und Importieren von Paketen
> * Laden von zwei Datasets mit unterschiedlichen Feldnamen
> * Bereinigen der Daten, um Anomalien zu entfernen
> * Transformieren von Daten mithilfe intelligenter Transformationen, um neue Features zu erstellen
> * Speichern Ihres Dataflowobjekts für die Verwendung in einem Regressionsmodell

## <a name="prerequisites"></a>Voraussetzungen

Fahren Sie mit dem Abschnitt [Einrichten Ihrer Entwicklungsumgebung](#start) fort, um mehr zu den Schritten mit Notebooks zu erfahren, oder rufen Sie anhand der nachfolgenden Anweisungen das Notebook ab, und führen Sie es unter Azure Notebooks oder auf Ihrem eigenen Notebook-Server aus. Für die Ausführung des Notebooks ist Folgendes erforderlich:

* Ein Python 3.6-Notebook-Server mit folgenden Komponenten:
    * Paket `azureml-dataprep` aus dem Azure Machine Learning SDK
* Das Tutorial-Notebook

* Verwenden eines [cloudbasierten Notebook-Servers in Ihrem Arbeitsbereich](#azure) 
* Verwenden [Ihres eigenen Notebook-Servers](#server)

### <a name="azure"></a>Verwenden eines cloudbasierten Notebook-Servers in Ihrem Arbeitsbereich

Die ersten Schritte mit Ihrem eigenen cloudbasierten Notebook-Server sind ganz einfach. Das Azure Machine Learning SDK für Python wird für Sie installiert und konfiguriert, wenn Sie diese Cloudressource erstellen.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* Führen Sie nach dem Starten der Notebook-Webseite das Notebook **tutorials/regression-part1-data-prep.ipynb** aus.

### <a name="server"></a>Verwenden Ihres eigenen Jupyter Notebook-Servers

Führen Sie diese Schritte aus, um auf Ihrem Computer einen lokalen Jupyter Notebook-Server zu erstellen.  Führen Sie nach den Schritten das Notebook **tutorials/regression-part1-data-prep.ipynb** aus.

1. Führen Sie die Installationsschritte unter [Schnellstart: Verwenden von Python für die ersten Schritte mit Azure Machine Learning](setup-create-workspace.md#sdk) aus, um eine Miniconda-Umgebung zu erstellen und das SDK zu installieren.  Den Abschnitt **Erstellen eines Arbeitsbereichs** können Sie überspringen, wenn Sie möchten, Sie benötigen ihn jedoch für [Teil 2](tutorial-auto-train-models.md) dieser Tutorialreihe.
1. Das Paket `azureml-dataprep` wird beim Installieren des SDK automatisch installiert.
1. Klonen Sie das [GitHub-Repository](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Starten Sie den Notebook-Server aus Ihrem geklonten Verzeichnis.

    ```shell
    jupyter notebook
    ```

## <a name="start"></a>Einrichten Ihrer Entwicklungsumgebung

Das gesamte Setup für Ihre Entwicklungsarbeit kann in einem Python Notebook erfolgen. Die Einrichtung umfasst die folgenden Aktionen:

* Installieren des SDKs
* Importieren von Python-Paketen

### <a name="install-and-import-packages"></a>Installieren und Importieren von Paketen

Installieren Sie wie folgt die erforderlichen Pakete, sofern sie noch nicht vorhanden sind:

```shell
pip install "azureml-dataprep[pandas]>=1.1.0,<1.2.0"
```

Importieren Sie das Paket.

```python
import azureml.dataprep as dprep
```

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie die aktuelle Paketversion „azureml.dataprep“ installieren. Dieses Tutorial funktioniert nicht mit Versionen unter 1.1.0.

## <a name="load-data"></a>Laden von Daten

Laden Sie zwei verschiedene Datasets für New Yorker Taxis in Dataflowobjekte herunter. Die Felder dieser Datasets unterscheiden sich geringfügig. Die Methode `auto_read_file()` erkennt automatisch die Art der Eingabedatei.

```python
from IPython.display import display
dataset_root = "https://dprepdata.blob.core.windows.net/demo"

green_path = "/".join([dataset_root, "green-small/*"])
yellow_path = "/".join([dataset_root, "yellow-small/*"])

green_df_raw = dprep.read_csv(path=green_path, header=dprep.PromoteHeadersMode.GROUPED)
# auto_read_file automatically identifies and parses the file type, which is useful when you don't know the file type.
yellow_df_raw = dprep.auto_read_file(path=yellow_path)

display(green_df_raw.head(5))
display(yellow_df_raw.head(5))
```

Ein `Dataflow`-Objekt ähnelt einem Datenrahmen und stellt eine Reihe von träge ausgewerteten, unveränderlichen Vorgängen für Daten dar. Vorgänge können durch Aufrufen der verschiedenen verfügbaren Transformations- und Filtermethoden hinzugefügt werden. Wenn einem `Dataflow` ein Vorgang hinzugefügt wird, entsteht immer ein neues `Dataflow`-Objekt.

## <a name="cleanse-data"></a>Bereinigen der Daten

Füllen Sie als Nächstes einige Variablen mit Verknüpfungstransformationen auf, die für alle Dataflows gelten. Die Variable `drop_if_all_null` dient zum Löschen von Datensätzen, bei denen alle Felder Null sind. Die Variable `useful_columns` enthält ein Array von Spaltenbeschreibungen, die in allen Dataflows beibehalten werden.

```python
all_columns = dprep.ColumnSelector(term=".*", use_regex=True)
drop_if_all_null = [all_columns, dprep.ColumnRelationship(dprep.ColumnRelationship.ALL)]
useful_columns = [
    "cost", "distance", "dropoff_datetime", "dropoff_latitude", "dropoff_longitude",
    "passengers", "pickup_datetime", "pickup_latitude", "pickup_longitude", "store_forward", "vendor"
]
```

Sie arbeiten zunächst mit den grünen Taxidaten und bringen diese in eine Form, die mit den gelben Taxidaten kombiniert werden kann. Rufen Sie die Funktionen `replace_na()`, `drop_nulls()` und `keep_columns()` unter Verwendung der zuvor erstellten Variablen für die Verknüpfungstransformation auf. Benennen Sie außerdem alle Spalten im Datenrahmen um, sodass sie den Namen in der Variablen `useful_columns` entsprechen.


```python
green_df = (green_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "VendorID": "vendor",
        "lpep_pickup_datetime": "pickup_datetime",
        "Lpep_dropoff_datetime": "dropoff_datetime",
        "lpep_dropoff_datetime": "dropoff_datetime",
        "Store_and_fwd_flag": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Pickup_longitude": "pickup_longitude",
        "Pickup_latitude": "pickup_latitude",
        "Dropoff_longitude": "dropoff_longitude",
        "Dropoff_latitude": "dropoff_latitude",
        "Passenger_count": "passengers",
        "Fare_amount": "cost",
        "Trip_distance": "distance"
     })
    .keep_columns(columns=useful_columns))
green_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>dropoff_datetime</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 08:14:37</td>
      <td>2013-08-01 09:09:06</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>21.25</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 09:13:00</td>
      <td>2013-08-01 11:38:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>2</td>
      <td>.00</td>
      <td>74.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-01 09:48:00</td>
      <td>2013-08-01 09:49:00</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-01 10:38:35</td>
      <td>2013-08-01 10:38:51</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>3.25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-01 11:51:45</td>
      <td>2013-08-01 12:03:52</td>
      <td>N</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>0</td>
      <td>1</td>
      <td>.00</td>
      <td>8.5</td>
    </tr>
  </tbody>
</table>
</div>

Führen Sie die gleichen Transformationsschritte für die gelben Taxidaten aus. Diese Funktionen sorgen dafür, dass NULL-Daten aus dem Dataset entfernt werden, was zur Verbesserung der Genauigkeit des Machine Learning-Modells beiträgt.

```python
yellow_df = (yellow_df_raw
    .replace_na(columns=all_columns)
    .drop_nulls(*drop_if_all_null)
    .rename_columns(column_pairs={
        "vendor_name": "vendor",
        "VendorID": "vendor",
        "vendor_id": "vendor",
        "Trip_Pickup_DateTime": "pickup_datetime",
        "tpep_pickup_datetime": "pickup_datetime",
        "Trip_Dropoff_DateTime": "dropoff_datetime",
        "tpep_dropoff_datetime": "dropoff_datetime",
        "store_and_forward": "store_forward",
        "store_and_fwd_flag": "store_forward",
        "Start_Lon": "pickup_longitude",
        "Start_Lat": "pickup_latitude",
        "End_Lon": "dropoff_longitude",
        "End_Lat": "dropoff_latitude",
        "Passenger_Count": "passengers",
        "passenger_count": "passengers",
        "Fare_Amt": "cost",
        "fare_amount": "cost",
        "Trip_Distance": "distance",
        "trip_distance": "distance"
    })
    .keep_columns(columns=useful_columns))
yellow_df.head(5)
```

Rufen Sie die Funktion `append_rows()` für die grünen Taxidaten auf, um die gelben Taxidaten anzufügen. Ein neuer kombinierter Datenrahmen wird erstellt.

```python
combined_df = green_df.append_rows([yellow_df])
```

### <a name="convert-types-and-filter"></a>Konvertieren von Typen und Filtern

Überprüfen Sie die Zusammenfassungsstatistik der Start- und Zielkoordinaten, um sich mit der Datenverteilung vertraut zu machen. Definieren Sie zunächst ein Objekt vom Typ `TypeConverter`, um die Felder für Längen- und Breitengrad in einen dezimalen Datentyp zu ändern. Rufen Sie als Nächstes die Funktion `keep_columns()` auf, um die Ausgabe auf die Längen- und Breitengradfelder zu beschränken, und rufen Sie anschließend die Funktion `get_profile()` auf. Diese Funktionsaufrufe erstellen eine komprimierte Ansicht des Datenflusses, die lediglich die Felder für Längen- und Breitengrad enthält. Dadurch lassen sich fehlende Koordinaten sowie Koordinaten, die außerhalb des zulässigen Bereichs liegen, leichter bewerten.


```python
decimal_type = dprep.TypeConverter(data_type=dprep.FieldType.DECIMAL)
combined_df = combined_df.set_column_types(type_conversions={
    "pickup_longitude": decimal_type,
    "pickup_latitude": decimal_type,
    "dropoff_longitude": decimal_type,
    "dropoff_latitude": decimal_type
})
combined_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Fehlt (Anzahl)</th>
      <th>Fehlt nicht (Anzahl)</th>
      <th>Fehlt (Prozent)</th>
      <th>Fehler (Anzahl)</th>
      <th>Leer (Anzahl)</th>
      <th>0,1 % Quantil</th>
      <th>1 % Quantil</th>
      <th>5 % Quantil</th>
      <th>25 % Quantil</th>
      <th>50 % Quantil</th>
      <th>75 % Quantil</th>
      <th>95 % Quantil</th>
      <th>99 % Quantil</th>
      <th>99,9 % Quantil</th>
      <th>Standardabweichung</th>
      <th>Mittelwert</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-88.114046</td>
      <td>-73.961840</td>
      <td>-73.961964</td>
      <td>-73.947693</td>
      <td>-73.922097</td>
      <td>-73.846670</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.792672</td>
      <td>-68.833579</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>40.919121</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.682889</td>
      <td>40.675541</td>
      <td>40.721075</td>
      <td>40.756159</td>
      <td>40.803909</td>
      <td>40.849406</td>
      <td>40.870681</td>
      <td>40.891244</td>
      <td>10.345967</td>
      <td>37.936742</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-115.179337</td>
      <td>0.000000</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-87.699611</td>
      <td>-73.984734</td>
      <td>-73.985777</td>
      <td>-73.956250</td>
      <td>-73.928948</td>
      <td>-73.866208</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>18.696526</td>
      <td>-68.896978</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>0.000000</td>
      <td>41.008934</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>7722.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>40.662763</td>
      <td>40.654851</td>
      <td>40.717821</td>
      <td>40.756534</td>
      <td>40.784688</td>
      <td>40.852437</td>
      <td>40.879289</td>
      <td>40.937291</td>
      <td>10.290780</td>
      <td>37.963774</td>
    </tr>
  </tbody>
</table>



In der Ausgabe der Zusammenfassungsstatistik sehen Sie, dass einerseits Koordinaten fehlen und andererseits Koordinaten vorhanden sind, die sich nicht im Stadtgebiet von New York befinden (was mittels subjektiver Analyse ermittelt wird). Filtern Sie Koordinaten für Orte heraus, die sich außerhalb der Stadtgrenzen befinden. Verketten Sie die Spaltenfilterbefehle innerhalb der Funktion `filter()`, und definieren Sie die Unter- und Obergrenze für die einzelnen Felder. Rufen Sie anschließend erneut die Funktion `get_profile()` auf, um die Transformation zu überprüfen.


```python
latlong_filtered_df = (combined_df
    .drop_nulls(
        columns=["pickup_longitude", "pickup_latitude", "dropoff_longitude", "dropoff_latitude"],
        column_relationship=dprep.ColumnRelationship(dprep.ColumnRelationship.ANY)
    )
    .filter(dprep.f_and(
        dprep.col("pickup_longitude") <= -73.72,
        dprep.col("pickup_longitude") >= -74.09,
        dprep.col("pickup_latitude") <= 40.88,
        dprep.col("pickup_latitude") >= 40.53,
        dprep.col("dropoff_longitude") <= -73.72,
        dprep.col("dropoff_longitude") >= -74.09,
        dprep.col("dropoff_latitude") <= 40.88,
        dprep.col("dropoff_latitude") >= 40.53
    )))
latlong_filtered_df.keep_columns(columns=[
    "pickup_longitude", "pickup_latitude",
    "dropoff_longitude", "dropoff_latitude"
]).get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Fehlt (Anzahl)</th>
      <th>Fehlt nicht (Anzahl)</th>
      <th>Fehlt (Prozent)</th>
      <th>Fehler (Anzahl)</th>
      <th>Leer (Anzahl)</th>
      <th>0,1 % Quantil</th>
      <th>1 % Quantil</th>
      <th>5 % Quantil</th>
      <th>25 % Quantil</th>
      <th>50 % Quantil</th>
      <th>75 % Quantil</th>
      <th>95 % Quantil</th>
      <th>99 % Quantil</th>
      <th>99,9 % Quantil</th>
      <th>Standardabweichung</th>
      <th>Mittelwert</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.078156</td>
      <td>-73.736481</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.076314</td>
      <td>-73.962542</td>
      <td>-73.962893</td>
      <td>-73.948975</td>
      <td>-73.927856</td>
      <td>-73.866662</td>
      <td>-73.830438</td>
      <td>-73.823160</td>
      <td>-73.769750</td>
      <td>0.048711</td>
      <td>-73.913865</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.575485</td>
      <td>40.879852</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.632884</td>
      <td>40.713105</td>
      <td>40.711600</td>
      <td>40.721403</td>
      <td>40.758142</td>
      <td>40.805145</td>
      <td>40.848855</td>
      <td>40.867567</td>
      <td>40.877690</td>
      <td>0.048348</td>
      <td>40.765226</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74.085747</td>
      <td>-73.720871</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-74.078828</td>
      <td>-73.985650</td>
      <td>-73.985813</td>
      <td>-73.959041</td>
      <td>-73.936681</td>
      <td>-73.884846</td>
      <td>-73.815507</td>
      <td>-73.776697</td>
      <td>-73.733471</td>
      <td>0.055961</td>
      <td>-73.920718</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40.583530</td>
      <td>40.879734</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>7059.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>40.597741</td>
      <td>40.695376</td>
      <td>40.695115</td>
      <td>40.727549</td>
      <td>40.758160</td>
      <td>40.788378</td>
      <td>40.850372</td>
      <td>40.867968</td>
      <td>40.878586</td>
      <td>0.050462</td>
      <td>40.759487</td>
    </tr>
  </tbody>
</table>

### <a name="split-and-rename-columns"></a>Aufteilen und Umbenennen von Spalten

Sehen Sie sich das Datenprofil für die Spalte `store_forward` an. Bei diesem Feld handelt es sich um ein boolesches Flag. Es ist `Y`, wenn das Taxi nach der Fahrt nicht mit dem Server verbunden war. In diesem Fall müssen die Fahrtdaten im Arbeitsspeicher gespeichert und später an den Server weitergeleitet werden, wenn wieder eine Verbindung besteht.


```python
latlong_filtered_df.keep_columns(columns='store_forward').get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Min</th>
      <th>max</th>
      <th>Count</th>
      <th>Fehlt (Anzahl)</th>
      <th>Fehlt nicht (Anzahl)</th>
      <th>Fehlt (Prozent)</th>
      <th>Fehler (Anzahl)</th>
      <th>Leer (Anzahl)</th>
      <th>0,1 % Quantil</th>
      <th>1 % Quantil</th>
      <th>5 % Quantil</th>
      <th>25 % Quantil</th>
      <th>50 % Quantil</th>
      <th>75 % Quantil</th>
      <th>95 % Quantil</th>
      <th>99 % Quantil</th>
      <th>99,9 % Quantil</th>
      <th>Standardabweichung</th>
      <th>Mittelwert</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>N</td>
      <td>J</td>
      <td>7059.0</td>
      <td>99.0</td>
      <td>6960.0</td>
      <td>0.014025</td>
      <td>0.0</td>
      <td>0.0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>



Die Datenprofilausgabe in der Spalte `store_forward` zeigt, dass die Daten inkonsistent sind und dass Werte fehlen oder Nullwerte vorhanden sind. Ersetzen Sie diese Werte mithilfe der Funktionen `replace()` und `fill_nulls()` durch „N“:


```python
replaced_stfor_vals_df = latlong_filtered_df.replace(columns="store_forward", find="0", replace_with="N").fill_nulls("store_forward", "N")
```

Führen Sie die Funktion `replace` für das Feld `distance` aus. Diese Funktion ändert das Format der Entfernungswerte, die fälschlicherweise als `.00` gekennzeichnet sind, und füllt alle NULL-Werte mit Nullen (0) auf. Konvertieren Sie das `distance`-Feld in ein numerisches Format. Diese falschen Datenpunkte sind wahrscheinlich Anomalien im Datenerfassungssystem der Taxis.


```python
replaced_distance_vals_df = replaced_stfor_vals_df.replace(columns="distance", find=".00", replace_with=0).fill_nulls("distance", 0)
replaced_distance_vals_df = replaced_distance_vals_df.to_number(["distance"])
```

Teilen Sie die Datums-/Uhrzeitwerte für Start und Ziel in entsprechende Datums- und Uhrzeitspalten auf. Verwenden Sie zum Aufteilen die Funktion `split_column_by_example()`. In diesem Fall wird der optionale Parameter `example` der Funktion `split_column_by_example()` weggelassen. Dadurch bestimmt die Funktion anhand der Daten automatisch, wo die Aufteilung erfolgen soll.


```python
time_split_df = (replaced_distance_vals_df
    .split_column_by_example(source_column="pickup_datetime")
    .split_column_by_example(source_column="dropoff_datetime"))
time_split_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_datetime_1</th>
      <th>pickup_datetime_2</th>
      <th>dropoff_datetime</th>
      <th>dropoff_datetime_1</th>
      <th>dropoff_datetime_2</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>2013-08-01 17:22:00</td>
      <td>2013-08-01</td>
      <td>17:22:00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>2013-08-01</td>
      <td>17:24:00</td>
      <td>2013-08-01 17:25:00</td>
      <td>2013-08-01</td>
      <td>17:25:00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>2013-08-06</td>
      <td>06:51:19</td>
      <td>2013-08-06 06:51:36</td>
      <td>2013-08-06</td>
      <td>06:51:36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>2013-08-06</td>
      <td>13:26:34</td>
      <td>2013-08-06 13:26:57</td>
      <td>2013-08-06</td>
      <td>13:26:57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>2013-08-06</td>
      <td>13:27:53</td>
      <td>2013-08-06 13:28:08</td>
      <td>2013-08-06</td>
      <td>13:28:08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Versehen Sie die von der Funktion `split_column_by_example()` generierten Spalten mit aussagekräftigen Namen.

```python
renamed_col_df = (time_split_df
    .rename_columns(column_pairs={
        "pickup_datetime_1": "pickup_date",
        "pickup_datetime_2": "pickup_time",
        "dropoff_datetime_1": "dropoff_date",
        "dropoff_datetime_2": "dropoff_time"
    }))
renamed_col_df.head(5)
```

Rufen Sie die Funktion `get_profile()` auf, um die vollständige Zusammenfassungsstatistik nach allen Bereinigungsschritten anzuzeigen.

```python
renamed_col_df.get_profile()
```

## <a name="transform-data"></a>Transformieren von Daten

Teilen Sie das Start- und Zieldatum weiter nach Wochentag, Monatstag und Monat auf. Verwenden Sie zum Abrufen des Wochentagswerts die Funktion `derive_column_by_example()`. Die Funktion akzeptiert einen Arrayparameter mit Beispielobjekten, die die Eingabedaten definieren, sowie die gewünschte Ausgabe. Die Funktion bestimmt automatisch Ihre bevorzugte Transformation. Teilen Sie die Uhrzeit der Start- und Zielzeitspalten in Stunde, Minute und Sekunde auf. Verwenden Sie dazu die Funktion `split_column_by_example()` ohne Beispielparameter.

Verwenden Sie nach dem Generieren der neuen Features die Funktion `drop_columns()`, um die ursprünglichen Felder zu löschen, da die neu generierten Features bevorzugt werden. Versehen Sie die restlichen Felder mit aussagekräftigen Beschreibungen.

Transformieren Sie die Daten auf diese Weise, um neue zeitbasierte Features zu erstellen, die zur Verbesserung der Genauigkeit des Machine Learning-Modells beitragen. Durch die Erstellung eines neuen Features für den Wochentag lässt sich beispielsweise eine Beziehung zwischen dem Wochentag und dem Fahrpreis herstellen, der häufig nachfragebedingt an bestimmten Tagen höher ist.


```python
transformed_features_df = (renamed_col_df
    .derive_column_by_example(
        source_columns="pickup_date",
        new_column_name="pickup_weekday",
        example_data=[("2009-01-04", "Sunday"), ("2013-08-22", "Thursday")]
    )
    .derive_column_by_example(
        source_columns="dropoff_date",
        new_column_name="dropoff_weekday",
        example_data=[("2013-08-22", "Thursday"), ("2013-11-03", "Sunday")]
    )

    .split_column_by_example(source_column="pickup_time")
    .split_column_by_example(source_column="dropoff_time")
    # The following two calls to split_column_by_example reference the column names generated from the previous two calls.
    .split_column_by_example(source_column="pickup_time_1")
    .split_column_by_example(source_column="dropoff_time_1")
    .drop_columns(columns=[
        "pickup_date", "pickup_time", "dropoff_date", "dropoff_time",
        "pickup_date_1", "dropoff_date_1", "pickup_time_1", "dropoff_time_1"
    ])

    .rename_columns(column_pairs={
        "pickup_date_2": "pickup_month",
        "pickup_date_3": "pickup_monthday",
        "pickup_time_1_1": "pickup_hour",
        "pickup_time_1_2": "pickup_minute",
        "pickup_time_2": "pickup_second",
        "dropoff_date_2": "dropoff_month",
        "dropoff_date_3": "dropoff_monthday",
        "dropoff_time_1_1": "dropoff_hour",
        "dropoff_time_1_2": "dropoff_minute",
        "dropoff_time_2": "dropoff_second"
    }))

transformed_features_df.head(5)
```

<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>vendor</th>
      <th>pickup_datetime</th>
      <th>pickup_weekday</th>
      <th>pickup_hour</th>
      <th>pickup_minute</th>
      <th>pickup_second</th>
      <th>dropoff_datetime</th>
      <th>dropoff_weekday</th>
      <th>dropoff_hour</th>
      <th>dropoff_minute</th>
      <th>dropoff_second</th>
      <th>store_forward</th>
      <th>pickup_longitude</th>
      <th>pickup_latitude</th>
      <th>dropoff_longitude</th>
      <th>dropoff_latitude</th>
      <th>passengers</th>
      <th>distance</th>
      <th>cost</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>2013-08-01 17:22:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>22</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>-73.937767</td>
      <td>40.758480</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2013-08-01 17:24:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>24</td>
      <td>00</td>
      <td>2013-08-01 17:25:00</td>
      <td>Thursday</td>
      <td>17</td>
      <td>25</td>
      <td>00</td>
      <td>N</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>-73.937927</td>
      <td>40.757843</td>
      <td>1</td>
      <td>0.0</td>
      <td>2.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>2013-08-06 06:51:19</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>19</td>
      <td>2013-08-06 06:51:36</td>
      <td>Tuesday</td>
      <td>06</td>
      <td>51</td>
      <td>36</td>
      <td>N</td>
      <td>-73.937721</td>
      <td>40.758404</td>
      <td>-73.937721</td>
      <td>40.758369</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2</td>
      <td>2013-08-06 13:26:34</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>34</td>
      <td>2013-08-06 13:26:57</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>26</td>
      <td>57</td>
      <td>N</td>
      <td>-73.937691</td>
      <td>40.758419</td>
      <td>-73.937790</td>
      <td>40.758358</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2</td>
      <td>2013-08-06 13:27:53</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>27</td>
      <td>53</td>
      <td>2013-08-06 13:28:08</td>
      <td>Tuesday</td>
      <td>13</td>
      <td>28</td>
      <td>08</td>
      <td>N</td>
      <td>-73.937805</td>
      <td>40.758396</td>
      <td>-73.937775</td>
      <td>40.758450</td>
      <td>1</td>
      <td>0.0</td>
      <td>3.3</td>
    </tr>
  </tbody>
</table>
</div>

Anhand der Daten sehen Sie, dass die auf der Grundlage der abgeleiteten Transformationen erzeugten Datums- und Uhrzeitkomponenten für Start und Ziel korrekt sind. Löschen Sie die Spalten `pickup_datetime` und `dropoff_datetime`. Sie werden nicht mehr benötigt. (Granulare Zeitfeatures wie Stunde, Minute und Sekunde sind für das Modelltraining sinnvoller.)


```python
processed_df = transformed_features_df.drop_columns(columns=["pickup_datetime", "dropoff_datetime"])
```

Verwenden Sie die Typrückschlussfunktion zur automatischen Prüfung des Datentyps der einzelnen Felder, und zeigen Sie die Rückschlussergebnisse an.


```python
type_infer = processed_df.builders.set_column_types()
type_infer.learn()
type_infer
```

Hier sehen Sie die Ausgabe von `type_infer`:

    Column types conversion candidates:
    'pickup_weekday': [FieldType.STRING],
    'pickup_hour': [FieldType.DECIMAL],
    'pickup_minute': [FieldType.DECIMAL],
    'pickup_second': [FieldType.DECIMAL],
    'dropoff_hour': [FieldType.DECIMAL],
    'dropoff_minute': [FieldType.DECIMAL],
    'dropoff_second': [FieldType.DECIMAL],
    'store_forward': [FieldType.STRING],
    'pickup_longitude': [FieldType.DECIMAL],
    'dropoff_longitude': [FieldType.DECIMAL],
    'passengers': [FieldType.DECIMAL],
    'distance': [FieldType.DECIMAL],
    'vendor': [FieldType.STRING],
    'dropoff_weekday': [FieldType.STRING],
    'pickup_latitude': [FieldType.DECIMAL],
    'dropoff_latitude': [FieldType.DECIMAL],
    'cost': [FieldType.DECIMAL]

Die Rückschlussergebnisse sehen korrekt aus. Wenden Sie nun die Typkonvertierungen auf den Dataflow an.


```python
type_converted_df = type_infer.to_dataflow()
type_converted_df.get_profile()
```

Führen Sie vor dem Verpacken des Dataflows zwei abschließende Filter für das Dataset aus. Filtern Sie den Dataflow nach Datensätzen, in denen die Variablenwerte `cost` und `distance` größer Null sind, um falsch erfasste Datenpunkte zu löschen. Dieser Schritt trägt erheblich zur Verbesserung der Genauigkeit des Machine Learning-Modells bei, da Datenpunkte ohne Kosten oder Entfernung große Ausreißer darstellen, die die Vorhersagegenauigkeit beeinträchtigen.

```python
final_df = type_converted_df.filter(dprep.col("distance") > 0)
final_df = final_df.filter(dprep.col("cost") > 0)
```

Sie verfügen nun über ein vollständig transformiertes und vorbereitetes Dataflowobjekt für die Verwendung in einem Machine Learning-Modell. Das SDK enthält eine Objektserialisierungsfunktion. Diese wird wie folgt verwendet:

```python
import os

file_path = os.path.join(os.getcwd(), "dflows.dprep")
final_df.save(file_path)
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Für den zweiten Teil des Tutorials benötigen Sie die Datei **dflows.dprep** im aktuellen Verzeichnis.

Falls Sie nicht mit dem zweiten Teil fortfahren möchten, löschen Sie die Datei **dflows.dprep** in Ihrem aktuellen Verzeichnis. Löschen Sie die Datei unabhängig davon, ob Sie für die Ausführung Ihre lokale Umgebung oder [Azure Notebooks](https://notebooks.azure.com/) verwenden.

## <a name="next-steps"></a>Nächste Schritte

Im ersten Teil einer Tutorialreihe haben Sie folgende Schritte ausgeführt:

> [!div class="checklist"]
> * Einrichten Ihrer Entwicklungsumgebung.
> * Laden und Bereinigen von Datasets
> * Prognostizieren Ihrer Logik anhand eines Beispiels unter Verwendung intelligenter Transformationen
> * Zusammenführen und Verpacken von Datasets zum Trainieren von maschinellem Lernen

Die Trainingsdaten sind nun für die Verwendung im zweiten Teil des Tutorials bereit:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen Ihres Regressionsmodells mit automatisiertem Machine Learning](tutorial-auto-train-models.md)
