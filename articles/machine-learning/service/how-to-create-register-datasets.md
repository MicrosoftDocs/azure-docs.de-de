---
title: Erstellen von Datasets zum Zugreifen auf Daten mit „azureml-datasets“
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie Datasets aus verschiedenen Quellen erstellen und Datasets in Ihrem Arbeitsbereich registrieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 05/21/2019
ms.openlocfilehash: b4c22caae86e20b8379db2b7feffb1ca82001239
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66753156"
---
# <a name="create-and-access-datasets-preview-in-azure-machine-learning"></a>Erstellen von und Zugreifen auf Datasets (Vorschauversion) in Azure Machine Learning

In diesem Artikel wird beschrieben, wie Sie Azure Machine Learning-Datasets (Vorschauversion) erstellen und auf die Daten von lokal und remote durchgeführten Experimenten zugreifen.

Mit verwalteten Datasets haben Sie folgende Möglichkeiten: 
* **Einfaches Zugreifen auf Daten während des Modelltrainings**, ohne eine erneute Verbindung mit zugrunde liegenden Speichern herstellen zu müssen

* **Sicherstellen der Datenkonsistenz und -reproduzierbarkeit** durch die übergreifende Nutzung des gleichen Zeigers für Experimente: Notebooks, automatisiertes maschinelles Lernen, Pipelines, grafische Benutzeroberfläche

* **Freigeben von Daten und Zusammenarbeiten** mit anderen Benutzern

* **Untersuchen von Daten** und Verwalten des Lebenszyklus von Datenmomentaufnahmen und -versionen

* **Vergleichen von Daten** im Training mit der Produktion


## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Datasets zu erstellen und zu nutzen:

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie noch heute die [kostenlose oder kostenpflichtige Version von Azure Machine Learning Service](https://aka.ms/AMLFree) aus.

* Ein [Azure Machine Learning Service-Arbeitsbereich](https://docs.microsoft.com/azure/machine-learning/service/setup-create-workspace)

* Eine [Installation des Azure Machine Learning-SDK für Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), in dem das Paket „azureml-datasets“ enthalten ist.

> [!Note]
> Einige Dataset-Klassen (Vorschauversion) verfügen über Abhängigkeiten vom [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py)-Paket (GA). Für Linux-Benutzer werden diese Klassen nur unter den folgenden Distributionen unterstützt:  Red Hat Enterprise Linux, Ubuntu, Fedora und CentOS.

## <a name="data-formats"></a>Datenformate

Sie können aus den folgenden Daten ein Azure Machine Learning-Dataset erstellen:
+ [delimited](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset#from-delimited-files-path--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---encoding--fileencoding-utf8--0---quoting-false--infer-column-types-true--skip-rows-0--skip-mode--skiplinesbehavior-no-rows--0---comment-none--include-path-false--archive-options-none-)
+ [binary](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-binary-files-path-)
+ [json](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-json-files-path--encoding--fileencoding-utf8--0---flatten-nested-arrays-false--include-path-false-)
+ [Excel](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-excel-files-path--sheet-name-none--use-column-headers-false--skip-rows-0--include-path-false--infer-column-types-true-)
+ [Parquet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-parquet-files-path--include-path-false-)
+ [Azure SQL-Datenbank](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)
+ [Azure Data Lake Gen1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#from-sql-query-data-source--query-)

## <a name="create-datasets"></a>Erstellen von Datasets 

Sie können mit Ihren Datasets mit dem azureml-datasets-Paket im [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) und speziell mit [der `Dataset`-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py) interagieren.

### <a name="create-from-local-files"></a>Erstellen aus lokalen Dateien

Um Dateien von Ihrem lokalen Computer zu laden, geben Sie den Pfad von Datei oder Ordner mit der [`auto_read_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset(class)?view=azure-ml-py#auto-read-files-path--include-path-false-)-Methode aus der `Dataset`-Klasse an.  Diese Methode führt die folgenden Schritte aus, ohne dass Sie den Dateityp angeben oder Argumente analysieren müssen:

* Ableiten und Festlegen von Trennzeichen.
* Überspringen leerer Datensätze am Anfang der Datei.
* Ableiten und Festlegen der Kopfzeile.
* Ableiten und Konvertieren von Spaltendatentypen.

```Python
from azureml.core.dataset import Dataset

dataset = Dataset.auto_read_files('./data/crime.csv')
```

Verwenden Sie alternativ die dateispezifischen Funktionen, um explizit die Analyse der Datei zu steuern. 


### <a name="create-from-azure-datastores"></a>Erstellen aus Azure-Datenspeichern

Erstellen Sie wie folgt Datasets aus einem Azure-Datenspeicher:

* Stellen Sie sicher, dass Sie über Zugriff vom Typ `contributor` oder `owner` auf den registrierten Azure-Datenspeicher verfügen.

* Importieren Sie die [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)-, [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py#definition)- und `Dataset`-Pakete aus dem SDK.

```Python
from azureml.core.workspace import Workspace
from azureml.core.datastore import Datastore
from azureml.core.dataset import Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
```

 Die `get()`-Methode ruft einen vorhandenen Datenspeicher im Arbeitsbereich ab.

```
dstore = Datastore.get(workspace, datastore_name)
```

Verwenden Sie die `from_delimited_files()`-Methode, um in durch Trennzeichen getrennten Dateien zu lesen und ein nicht registriertes Dataset zu erstellen.

```Python
# create an in-memory Dataset on your local machine
datapath = dstore.path('data/src/crime.csv')
dataset = Dataset.from_delimited_files(datapath)

# returns the first 5 rows of the Dataset as a pandas Dataframe.
dataset.head(5)
```

## <a name="register-datasets"></a>Registrieren von Datasets

Registrieren Sie Ihre Datasets beim Arbeitsbereich, um den Erstellungsprozess abzuschließen:

Verwenden Sie die [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--visible-true--exist-ok-false--update-if-exist-false-)-Methode zum Registrieren von Datasets in Ihrem Arbeitsbereich, damit diese für andere Benutzer freigegeben und für unterschiedliche Experimente wiederverwendet werden können.

```Python
dataset = dataset.register(workspace = workspace,
                           name = 'dataset_crime',
                           description = 'Training data',
                           exist_ok = False
                           )
```

>[!NOTE]
> Es tritt ein Fehler auf, wenn `exist_ok = False` (Standardeinstellung) gilt und Sie versuchen, ein Dataset unter dem gleichen Namen wie für ein anderes Dataset zu registrieren. Legen Sie diese Option auf `True` fest, um das Überschreiben von vorhandenen Datasets zuzulassen.

## <a name="access-data-in-datasets"></a>Zugreifen auf Daten in Datasets

Registrierte Datasets sind lokal, remote und auf Computeclustern wie der Azure Machine Learning-Computeressource verfügbar und nutzbar. Um Ihre registrierten Datasets Experimente und Computeumgebungen übergreifend wiederzuverwenden, rufen Sie mit folgendem Code Ihren Arbeitsbereich und das registrierte Dataset mit Namen ab.

```Python
workspace = Workspace.from_config()

# See list of datasets registered in workspace.
Dataset.list(workspace)

# Get dataset by name
dataset = workspace.datasets['dataset_crime']
```

## <a name="next-steps"></a>Nächste Schritte

* [Untersuchen und Aufbereiten von Datasets](how-to-explore-prepare-data.md).
* [Verwalten des Lebenszyklus von Dataset-Definitionen](how-to-manage-dataset-definitions.md).
* Ein Beispiel für die Verwendung von Datasets finden Sie in den [Beispielnotebooks](https://aka.ms/dataset-tutorial).
