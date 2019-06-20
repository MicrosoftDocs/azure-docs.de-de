---
title: Versionsverwaltung und Verwaltung von Dataset-Definitionen mit „azureml-datasets“
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Dataset-Definitionen aktualisieren und den Lebenszyklus von Definitionen verwalten können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: 4476bdd902f054683fa544dc44b548689f3a1881
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241879"
---
# <a name="version-and-manage-your-dataset-definitions"></a>Versionsverwaltung und Verwaltung von Datasetdefinitionen

Erfahren Sie, wie Sie Dataset-Definitionen mit Azure Machine Learning Datasets (Vorschau) aktualisieren und verwalten können.

Dataset-Definitionen sind die Rezepte, die zur Vorbereitung Ihrer Datasets verwendet werden und die sowohl den Verweis auf Ihre Quelldaten als auch die durchgeführten Transformationsschritte enthalten. Ein Dataset kann über viele Definitionen verfügen, und jede Definition besitzt einen eigenen Lebenszyklus.

Das folgende Szenario ist ein Beispiel für die Verwendung von Dataset-Definitionen:

1. Sie erstellen eine Machine Learning-Pipeline, die die aktuelle Definition Ihres Datasets nutzt.
1. Die zugrunde liegenden Daten ändern sich, neue Attribute wurden hinzugefügt.
1. Sie erstellen eine neue Version Ihrer Definition, die zusätzliche Transformationen hinzufügt, um die neuen Attribute zu verarbeiten.

In diesem Beispiel nutzt die vorhandene Pipeline weiterhin die ursprüngliche Definitionsversion. Die neue Definitionsversion kann für neue Szenarien (etwa das Trainieren eines Modells oder das Erstellen einer Pipeline) verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement und einen Arbeitsbereich, um Ihr Dataset zu registrieren, um den Lebenszyklus von Dataset-Definitionen zu verwalten.

Die in den Beispielen in diesem Dokument verwendete Beispieldatei ist unter [https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) verfügbar.

## <a name="create-and-update-definitions"></a>Erstellen und Aktualisieren von Definitionen

Zuerst erstellen und registrieren wir ein Dataset in Ihrem Arbeitsbereich.

```python
from azureml.core import Workspace, Datastore, Dataset

# change the configuration for workspace and Datastore
subscription_id = 'your subscription id'
resource_group = 'your resource group name'
workspace_name = 'your workspace name'
datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace(subscription_id, resource_group, workspace_name)

# get a Datastore that has already been created in the workspace
dstore = Datastore.get(workspace, datastore_name)

# create an in-memory Dataset from Datastore
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# register the Dataset with the workspace. if a Dataset with the same name already exists, retrieve it by turning `exist_ok = True`
dataset_name = 'crime dataset'
dataset = dataset.register(workspace = workspace, 
                 name = dataset_name, 
                 description = 'crime dataset for demo', 
                 tags = {'year':'2019', 'month':'Apr'},
                 exist_ok = True)
```

Die erste Dataset-Definition (`version_id` = 1) wird erstellt, wenn das Dataset erstellt wird. Jedes Mal, wenn Sie Ihre Dataset-Definition aktualisieren, wird dann der neuesten Definition eine neue version_id zugewiesen.

```python
# get the Dataset from the workspace by name
dataset = workspace.datasets['dataset_name']

# get the latest Dataset definition
ds_def = dataset.get_definition()

# update the Dataset definition to select only the columns I'm interested in
ds_def = ds_def.keep_columns(['ID', 'Arrest', 'Latitude', 'Longitude'])

# with this update, the latest definition for dataset now has `version_id = 2`
dataset = dataset.update_definition(ds_def, 'select useful column')

dataset = workspace.datasets['dataset_name']
dataset.head(5)
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
      <th>ID</th>
      <th>Verhaftung</th>
      <th>Breitengrad</th>
      <th>Längengrad</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>FALSE</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>FALSE</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>FALSE</td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Dieses Definitionsupdate löscht keine vorherigen Dataset-Definitionen. Sie können noch immer auf die vorherigen Definitionen zugreifen und diese nutzen.

```python
# specify `version_id` to get a previous definition 
ds_def_old = dataset.get_definition(version_id = 1)
ds_def_old.head(5)
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
      <th>ID</th>
      <th>Fallnummer</th>
      <th>Date</th>
      <th>Block</th>
      <th>IUCR</th>
      <th>Primärer Typ</th>
      <th>BESCHREIBUNG</th>
      <th>Standortbeschreibung</th>
      <th>Verhaftung</th>
      <th>Inland</th>
      <th>...</th>
      <th>Bezirk</th>
      <th>Gemeindebezirk</th>
      <th>FBI-Code</th>
      <th>x-Koordinate</th>
      <th>y-Koordinate</th>
      <th>Jahr</th>
      <th>Aktualisiert am</th>
      <th>Breitengrad</th>
      <th>Längengrad</th>
      <th>Location</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10498554</td>
      <td>HZ239907</td>
      <td>4/4/2016 23:56</td>
      <td>007XX E 111TH ST</td>
      <td>1153</td>
      <td>DECEPTIVE PRACTICE</td>
      <td>FINANCIAL IDENTITY THEFT OVER $ 300</td>
      <td>OTHER</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>50</td>
      <td>11</td>
      <td>1183356</td>
      <td>1831503</td>
      <td>2016</td>
      <td>5/11/2016 15:48</td>
      <td>41.69283384</td>
      <td>-87.60431945</td>
      <td>(41.692833841, -87.60431945)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>10516598</td>
      <td>HZ258664</td>
      <td>4/15/2016 17:00</td>
      <td>082XX S MARSHFIELD AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>FROM BUILDING</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>21</td>
      <td>71</td>
      <td>6</td>
      <td>1166776</td>
      <td>1850053</td>
      <td>2016</td>
      <td>5/12/2016 15:48</td>
      <td>41.74410697</td>
      <td>-87.66449429</td>
      <td>(41.744106973, -87.664494285)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>10519196</td>
      <td>HZ261252</td>
      <td>4/15/2016 10:00</td>
      <td>104XX S SACRAMENTO AVE</td>
      <td>1154</td>
      <td>DECEPTIVE PRACTICE</td>
      <td>FINANCIAL IDENTITY THEFT $300 AND UNDER</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>19</td>
      <td>74</td>
      <td>11</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/12/2016 15:50</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>3</th>
      <td>10519591</td>
      <td>HZ261534</td>
      <td>4/15/2016 9:00</td>
      <td>113XX S PRAIRIE AVE</td>
      <td>1120</td>
      <td>DECEPTIVE PRACTICE</td>
      <td>FORGERY</td>
      <td>RESIDENCE</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>9</td>
      <td>49</td>
      <td>10</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/13/2016 15:51</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>4</th>
      <td>10534446</td>
      <td>HZ277630</td>
      <td>4/15/2016 10:00</td>
      <td>055XX N KEDZIE AVE</td>
      <td>890</td>
      <td>THEFT</td>
      <td>FROM BUILDING</td>
      <td>SCHOOL, PUBLIC, BUILDING</td>
      <td>FALSE</td>
      <td>FALSE</td>
      <td>...</td>
      <td>40</td>
      <td>13</td>
      <td>6</td>
      <td></td>
      <td></td>
      <td>2016</td>
      <td>5/25/2016 15:59</td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
  </tbody>
</table>
</div>

Wie wir am Ergebnis sehen können, behält die erste Version der Dataset-Definition immer noch alle Spalten aus den Kriminalitätsdaten bei, unabhängig von einer späteren Aktualisierung. Wenn Sie beispielsweise ein Machine Learning-Modell verwenden, das derzeit die erste Version des Datasets nutzt und die Spalte `Date` aus dem Dataset als eines der Features verwendet, werden Sie keinen Fehler beim letzten Definitionsupdate feststellen, das nur die Spalten `ID`, `Arrest`, `Latitude`, `Longitude` aus den Kriminalitätsdaten enthält.

## <a name="how-to-access-dataset-definitions"></a>Vorgehensweise beim Zugreifen auf Dataset-Definitionen

Verwenden Sie zum Abrufen einer Liste aller Definitionen `get_definitions()`. Verwenden Sie zum Abrufen einer bestimmten Version einer Definition `get_definition()`. Das folgende Beispiel zeigt das Abrufen einer Liste aller Definitionen und das anschließende Abrufen von Version 1:

```python
# list all definitions for the dataset
dataset.get_definitions()
# get version ID 1
dataset.get_definition(version_id=1)
```

Die Ausgabe von `get_definitions()` sieht in etwa wie das folgende Beispiel aus:

```text
{'2': VersionID: 2, State: active, Created: 2019-04-19 16:43:52.439890+00:00, Modified: 2019-04-19 16:43:52.439890+00:00, Notes: select useful column,
 '1': VersionID: 1, State: active, Created: 2019-04-19 16:39:14.112046+00:00, Modified: 2019-04-19 16:44:12.912663+00:00, Notes: None}
```

Sobald Sie eine Definition abgerufen haben, können Sie sie mit Ihren Machine Learning Modellen oder in einer Machine Learning-Pipeline verwenden.

## <a name="manage-lifecycle-of-dataset-definitions"></a>Verwalten des Lebenszyklus von Dataset-Definitionen

Sie können den Lebenszyklus der Dataset-Definitionen unabhängig voneinander verwalten. Es gibt drei Phasen im Lebenszyklus: aktiv, veraltet oder archiviert.

### <a name="active"></a>Aktiv

Wenn die Definition eines neues Datasets erstellt wird, ist sie standardmäßig aktiv. 

### <a name="deprecate"></a>Als veraltet kennzeichnen

Dataset-Definitionen können als veraltet gekennzeichnet werden, wenn ihre Verwendung nicht mehr empfehlenswert und Ersatz verfügbar ist. Wenn eine als veraltet gekennzeichnete Dataset-Definition in Machine Learning-Pipelines verwendet wird, wird eine Warnmeldung zurückgegeben, die Ausführung wird jedoch nicht blockiert.

Wenn eine Dataset-Definition als veraltet gekennzeichnet wird, geben Sie die Dataset-ID und die Definitionsversions-ID des Datasets für die Ersatzdefinition an. Diese Informationen werden gespeichert und in der Warnmeldung verwendet, wenn Sie versuchen, eine als veraltet gekennzeichnete Dataset-Definition zu nutzen.

```python
# get the definition that you want to deprecate
ds_def = dataset.get_definition(version_id = 1)

# deprecate it by providing the information for the replacement definition, which is recommended to be used in machine learning scenarios
ds_def.deprecate(deprecate_by_dataset_id=dataset.id, deprecated_by_definition_version=2)
```

### <a name="archive"></a>Archivieren

Dataset-Definitionen können archiviert werden, wenn Definitionen aus irgendeinem Grund nicht verwendet werden sollen (z.B. nicht mehr verfügbare zugrunde liegende Daten). Wenn eine archivierte Dataset-Definition in Machine Learning-Pipelines verwendet wird, wird die Ausführung mit einem Fehler blockiert.

```python
# archive the definition with `version_id = 1`
ds_def = dataset.get_definition(version_id = 1)
ds_def.archive()
```

### <a name="reactivate"></a>Erneut aktivieren

Sie können eine als veraltet gekennzeichnete oder archivierte Dataset-Definition einfach erneut aktivieren.

```python
# reactivate Dataset definition with `version_id =1` which was archived in the previous step
ds_def = dataset.get_definition(version_id = 1)
ds_def.reactivate()
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Arbeiten mit Datasets finden Sie unter [Erstellen und Registrieren von Azure Machine Learning Datasets](how-to-create-register-datasets.md).

Ein Beispiel für die Verwendung von Datasets finden Sie in den [Beispielnotebooks](https://aka.ms/dataset-tutorial).
