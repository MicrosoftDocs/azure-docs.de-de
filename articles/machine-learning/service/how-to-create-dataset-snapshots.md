---
title: Vergleichen und Reproduzieren von Daten mit Datasetmomentaufnahmen
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Daten in einem Zeitraum vergleichen und die Reproduzierbarkeit mit Datasetmomentaufnahmen sicherstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
ms.date: 05/23/2019
ms.openlocfilehash: 525660be0f38c9458590e52cfcd575acb4cf5444
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162058"
---
# <a name="compare-data-and-ensure-reproducibility-with-snapshots-preview"></a>Vergleichen von Daten und Sicherstellen der Reproduzierbarkeit mit Momentaufnahmen (Vorschau)

In diesem Artikel erfahren Sie, wie Sie Momentaufnahmen Ihrer [Azure Machine Learning-Datasets](how-to-create-register-datasets.md) erstellen und verwalten, damit Sie Daten in einem Zeitraum erfassen oder vergleichen können. Datasets erleichtern den Zugriff auf Ihre Daten und das Arbeiten mit ihnen in der Cloud in verschiedenen Szenarien.

**Datasetmomentaufnahmen** speichern ein Profil (Zusammenfassungsstatistiken) der Daten zum Zeitpunkt der Erstellung. Sie können auch wählen, eine Kopie der Daten für die Reproduzierbarkeit in der Momentaufnahme zu speichern.

>[!Important]
> Momentaufnahmen ziehen Speicherkosten nach sich. Das Speichern einer Kopie der Daten in der Momentaufnahme erfordert sogar noch mehr Speicher. Verwenden Sie [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-), wenn sie nicht mehr benötigt werden.

## <a name="when-to-use-snapshots"></a>Wann sollten Momentaufnahmen verwendet werden?

Es gibt drei hauptsächliche Verwendungen für Momentaufnahmen:

+ **Modellüberprüfung**: Vergleichen Sie die Datenprofile verschiedener Momentaufnahmen zwischen Trainingsläufen oder mit Produktionsdaten.

+ **Modellreproduzierbarkeit**: Reproduzieren Sie Ihre Ergebnisse durch Aufrufen einer Momentaufnahme, die Daten während des Trainings enthält.

+ **Nachverfolgen von Daten in einem Zeitraum**: Sehen Sie durch [Vergleichen von Profilen](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#compare-profiles-rhs-dataset-snapshot--include-columns-none--exclude-columns-none--histogram-compare-method--histogramcomparemethod-wasserstein--0--), wie das Dataset sich entwickelt hat.
  
## <a name="prerequisites"></a>Voraussetzungen

Um Datasetmomentaufnahmen zu erstellen, benötigen Sie ein registriertes Azure Machine Learning-Dataset. Wenn Sie kein Dataset besitzen, informieren Sie sich unter [Erstellen und Registrieren von Datasets](how-to-create-register-datasets.md).

## <a name="create-dataset-snapshots"></a>Erstellen von Datasetmomentaufnahmen

Um eine Momentaufnahme eines Datasets zu erstellen, verwenden Sie [`dataset.create_snapshot()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?#create-snapshot-snapshot-name--compute-target-none--create-data-snapshot-false--target-datastore-none-) aus dem „azureml-datasets“-Paket des Azure Machine Learning SDK.

Standardmäßig speichert die Momentaufnahme das Profil (Zusammenfassungsstatistiken) der Daten mit Anwendung der neuesten [Datasetdefinition](how-to-manage-dataset-definitions.md). Eine Datasetdefinition enthält einen Datensatz der Transformationsschritte, die für die Daten definiert sind. Es ist eine hervorragende Möglichkeit, Ihre Datenvorbereitungen reproduzierbar zu machen.

Optional können Sie auch durch Hinzufügen von `create_data_snapshot = True` eine Kopie der Daten in die Momentaufnahme einbeziehen.  Diese Daten können für die Reproduzierbarkeit nützlich sein.

In diesem Beispiel werden [Beispieldaten zu Straftaten](https://dprepdata.blob.core.windows.net/dataset-sample-files/crime.csv) und ein Dataset namens `dataset_crime` verwendet, das mithilfe des Artikels [Erstellen und Registrieren von Datasets](how-to-create-register-datasets.md) erstellt wird.

```Python
from azureml.core.workspace import Workspace
from azureml.core.dataset import Dataset
from azureml.data.dataset_snapshot import DatasetSnapshot
import datetime

# get existing workspace
workspace = Workspace.from_config()

# get existing, named dataset:
dataset = workspace.datasets['dataset_crime']

# assign name to snapshot
snapshot_name = 'snapshot_' + datetime.datetime.today().strftime('%Y%m%d%H%M%S')

# create snapshot to store profile from a remote environment
# and include copy of data
snapshot = dataset.create_snapshot(snapshot_name = snapshot_name,
                                   compute_target = remote_compute_target,
                                   create_data_snapshot = True)
```

Da Momentaufnahmen asynchron erstellt werden, verwenden Sie die [ `wait_for_completion()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_snapshot.datasetsnapshot?view=azure-ml-py#wait-for-completion-show-output-true--status-update-frequency-10-)-Methode, um den Prozess zu überwachen.

```Python
# monitor process every 10 seconds
snapshot.wait_for_completion(show_output=True, status_update_frequency=10)

# return snapshot just created
dataset.get_snapshot(snapshot_name)
```

Ausgabe:

```
Action status: Running
Action status: Running
Action status: Completed


DatasetSnapshot(Name: snapshot_20190411165420,
Dataset Id: 66cd0830-2f72-41e5-b677-d2d953f54821,
Workspace: "your_workspace_name",
Dataset definition version: 1,
Snapshot created date: 2019-05-11 17:24:00+00:00)
```

Verwenden Sie [ `dataset.delete_snapshot()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#delete-snapshot-snapshot-name-), wenn sie nicht mehr benötigt werden.

## <a name="find-snapshots"></a>Suchen von Momentaufnahmen

Verwenden Sie [`get_snapshot()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-snapshot-snapshot-name-) zum Abrufen einer vorhandenen Momentaufnahme.

Verwenden Sie [`get_all_snapshots()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-all-snapshots--) zum Abrufen einer Liste Ihrer gespeicherten Momentaufnahmen eines bestimmten Datasets.

```Python
# Get named snapshot for this dataset
dataset.get_snapshot(snapshot_name)

# Get all snapshots for this dataset
dataset.get_all_snapshots()
```

## <a name="get-data-and-profiles-from-snapshots"></a>Abrufen von Daten und Profilen aus Momentaufnahmen

Jede Momentaufnahme generiert ein Profil des Datasets, das Statistiken über Zusammenfassungen enthält und Ihnen die Möglichkeit bietet, eine Kopie Ihrer Daten zu speichern.

### <a name="get-the-data-profile"></a>Abrufen des Datenprofils

Greifen Sie mit [ `get_profile()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#get-profile-arguments-none--generate-if-not-exist-true--workspace-none--compute-target-none-) auf das Profil der Daten zu.  Sie können dieses Profil z.B. verwenden, um Trainings- und Produktionsdaten zu vergleichen.

```Python
snapshot.get_profile()
```

||Type|Min|max|Count|Fehlt (Anzahl)|Fehlt nicht (Anzahl)|Fehlt (Prozent)|Fehler (Anzahl)|Leer (Anzahl)|0,1 % Quantil|1 % Quantil|5 % Quantil|25 % Quantil|50 % Quantil|75 % Quantil|95 % Quantil|99 % Quantil|99,9 % Quantil|Mittelwert|Standardabweichung|Variance|Schiefe|Kurtosis
-|----|---|---|-----|-------------|-----------------|---------------|-----------|-----------|-------------|-----------|-----------|------------|------------|------------|------------|------------|--------------|----|------------------|--------|--------|--------
ID|FieldType.INTEGER|1.04986e+07|1.05351e+07|10,0|0.0|10,0|0.0|0.0|0.0|1.04986e+07|1.04992e+07|1.04986e+07|1.05166e+07|1.05209e+07|1.05259e+07|1.05351e+07|1.05351e+07|1.05351e+07|1.05195e+07|12302.7|1.51358e+08|-0.495701|-1.02814
Fallnummer|FieldType.STRING|HZ239907|HZ278872|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Date|FieldType.DATE|2016-04-04 23:56:00+00:00|2016-04-15 17:00:00+00:00|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Block|FieldType.STRING|004XX S KILBOURN AVE|113XX S PRAIRIE AVE|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
IUCR|FieldType.INTEGER|810|1154|10,0|0.0|10,0|0.0|0.0|0.0|810|850|810|890|1.136|1153|1154|1154|1154|1058.5|137.285|18847.2|-0.785501|-1.3543
Primärer Typ|FieldType.STRING|DECEPTIVE PRACTICE|THEFT|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
BESCHREIBUNG|FieldType.STRING|BOGUS CHECK|OVER $500|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Standortbeschreibung|FieldType.STRING||SCHOOL, PUBLIC, BUILDING|10,0|0.0|10,0|0.0|0.0|1.0||||||||||||||
Verhaftung|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Inland|FieldType.BOOLEAN|False|False|10,0|0.0|10,0|0.0|0.0|0.0||||||||||||||
Beat|FieldType.INTEGER|531|2433|10,0|0.0|10,0|0.0|0.0|0.0|531|531|531|614|1318.5|1911|2433|2433|2433|1371.1|692.094|478994|0.105418|-1.60684
District|FieldType.INTEGER|5|24|10,0|0.0|10,0|0.0|0.0|0.0|5|5|5|6|13|19|24|24|24|13,5|6.94822|48.2778|0.0930109|-1.62325
Bezirk|FieldType.INTEGER|1|48|10,0|0.0|10,0|0.0|0.0|0.0|1|5|1|9|22,5|40|48|48|48|24,5|16.2635|264.5|0.173723|-1.51271
Gemeindebezirk|FieldType.INTEGER|4|77|10,0|0.0|10,0|0.0|0.0|0.0|4|8.5|4|24|37,5|71|77|77|77|41.2|26.6366|709.511|0.112157|-1.73379

### <a name="get-the-data-from-the-snapshot"></a>Abrufen der Daten aus der Momentaufnahme

Um eine Kopie der in einer Datasetmomentaufnahme gespeicherten Daten abzurufen, generieren Sie einen Pandas-Datenrahmen mit der [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#to-pandas-dataframe--)-Methode.

Bei dieser Methode tritt ein Fehler auf, wenn während der Erstellung der Momentaufnahme keine Kopie der Daten angefordert wurde.

```Python
snapshot.to_pandas_dataframe().head(3)
```

||ID|Fallnummer|Date|Block|IUCR|Primärer Typ|BESCHREIBUNG|Standortbeschreibung|Verhaftung|Inland|...|Bezirk|Gemeindebezirk|FBI-Code|x-Koordinate|y-Koordinate|Jahr|Aktualisiert am|Breitengrad|Längengrad|Location
-|--|-----------|----|-----|----|------------|-----------|--------------------|------|--------|---|----|--------------|--------|------------|------------|----|----------|--------|---------|--------
0|10498554|HZ239907|2016-04-04 23:56:00|007XX E 111TH ST|1153|DECEPTIVE PRACTICE|FINANCIAL IDENTITY THEFT OVER $ 300|OTHER|False|False|...|9|50|11|1183356.0|1831503.0|2016|2016-05-11 15:48:00|41.692834|-87.604319|(41.692833841, -87.60431945)
1|10516598|HZ258664|2016-04-15 17:00:00|082XX S MARSHFIELD AVE|890|THEFT|FROM BUILDING|RESIDENCE|False|False|...|21|71|6|1166776.0|1850053.0|2016|2016-05-12 15:48:00|41.744107|-87.664494|(41.744106973, -87.664494285)
2|10519196|HZ261252|2016-04-15 10:00:00|104XX S SACRAMENTO AVE|1154|DECEPTIVE PRACTICE|FINANCIAL IDENTITY THEFT $300 AND UNDER|RESIDENCE|False|False|...|19|74|11|NaN|NaN|2016|2016-05-12 15:50:00|NaN|NaN|

## <a name="next-steps"></a>Nächste Schritte

* Entwurfsmuster und Beispiele zur Verwendung finden Sie in der [Übersicht](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) zum SDK.

* Ein Beispiel für die Verwendung von Datasetmomentaufnahmen finden Sie in den [Beispielnotebooks](https://aka.ms/dataset-tutorial).
