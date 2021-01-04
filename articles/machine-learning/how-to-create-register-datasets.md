---
title: Erstellen von Azure Machine Learning-Datasets für den Datenzugriff
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Azure Machine Learning-Datasets erstellen, um auf Ihre Daten für Machine Learning-Experimentausführungen zuzugreifen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 28e70a5d5a6ac4cd51f5ed3fc85afd47a5af68d8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033271"
---
# <a name="create-azure-machine-learning-datasets"></a>Erstellen von Azure Machine Learning-Datasets



In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Datasets erstellen, um auf Daten für Ihre lokalen oder remotebasierten Experimente mit dem Python-SDK für Azure Machine Learning zuzugreifen. Informationen dazu, welche Rolle Datasets im Workflow für den Datenzugriff in Azure Machine Learning spielen, finden Sie im Artikel [Datenzugriff in Azure Machine Learning](concept-data.md#data-workflow).

Durch Erstellen eines Datasets erstellen Sie einen Verweis auf den Speicherort der Datenquelle sowie eine Kopie der zugehörigen Metadaten. Da die Daten an ihrem bisherigen Speicherort verbleiben, entstehen Ihnen keine zusätzlichen Speicherkosten und Sie riskieren nicht die Integrität Ihrer Datenquellen. Auch Datasets werden verzögert ausgewertet, was zum Erhöhen der Workflowleistung beiträgt. Sie können Datasets aus Datenspeichern, öffentlichen URLs und [Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) erstellen.

Für eine Erfahrung mit sehr wenig Code [erstellen Sie mit dem Azure Machine Learning Studio entsprechende Azure Machine Learning-Datasets](how-to-connect-data-ui.md#create-datasets).

Azure Machine Learning-Datasets ermöglichen Folgendes:

* Aufbewahren einer einzelnen Datenkopie in Ihrem Speicher, auf die durch Datasets verwiesen wird

* Nahtloses Zugreifen auf Daten während des Modelltrainings, ohne sich Gedanken über Verbindungszeichenfolgen oder Datenpfade machen zu müssen. [Weitere Informationen zum Training mit Datasets](how-to-train-with-datasets.md).

* Freigeben von Daten und Zusammenarbeiten mit anderen Benutzern

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Datasets zu erstellen und zu nutzen:

* Ein Azure-Abonnement. Wenn Sie keines haben, erstellen Sie ein kostenloses Konto, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

* Eine [Installation des Azure Machine Learning-SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), in dem das Paket „azureml-datasets“ enthalten ist.

    * Erstellen Sie eine [Azure Machine Learning-Computeinstanz](how-to-create-manage-compute-instance.md), bei der es sich um eine vollständig konfigurierte und verwaltete Entwicklungsumgebung handelt, die integrierte Notebooks und das bereits installierte SDK beinhaltet.

    **OR**

    * Arbeiten Sie an Ihrem eigenen Jupyter-Notebook, und installieren Sie selbst das SDK mit [diesen Anweisungen](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

> [!NOTE]
> Einige Datasetklassen sind vom Paket [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) abhängig, das nur mit 64-Bit Python kompatibel ist. Für Linux-Benutzer werden diese Klassen nur unter den folgenden Distributionen unterstützt:  Red Hat Enterprise Linux (7, 8), Ubuntu (14.04, 16.04, 18.04), Fedora (27, 28), Debian (8, 9) und CentOS (7). Wenn Sie nicht unterstützte Distributionen verwenden, befolgen Sie [diese Anleitung](/dotnet/core/install/linux), um .NET Core 2.1 zu installieren und fortzufahren. 

## <a name="compute-size-guidance"></a>Leitfaden für die Computegröße

Wenn Sie ein Dataset erstellen, überprüfen Sie Ihre Computeverarbeitungsleistung und die Größe der Daten im Arbeitsspeicher. Die Größe der Daten im Speicher ist nicht mit der Größe der Daten in einem Datenrahmen identisch. Beispielsweise können Daten in CSV-Dateien sich in einem Datenrahmen um das10-fache ausdehnen, sodass aus einer 1 GB großen CSV-Datei 10 GB in einem Datenrahmen werden können. 

Wenn Ihre Daten komprimiert sind, können sie sich weiter ausdehnen. 20 GB Daten mit relativ geringer Dichte, die im komprimierten Parquet-Format gespeichert sind, können sich auf etwa 800 GB im Arbeitsspeicher ausdehnen. Da in Parquet-Dateien Daten in einem Spaltenformat gespeichert werden, müssen Sie, wenn Sie nur die Hälfte der Spalten benötigen, nur etwa 400 GB in den Arbeitsspeicher laden.

[Erfahren Sie mehr über die Optimierung der Datenverarbeitung in Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Datasettypen

Es gibt zwei Typen von Datasets, je nachdem, wie sie von den Benutzern im Training verwendet werden: FileDatasets und TabularDatasets. Beide Typen können in Trainingsworkflows von Azure Machine Learning mit Schätzern, AutoML, HyperDrive und Pipelines verwendet werden. 

### <a name="filedataset"></a>FileDataset

Ein [FileDataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset?preserve-view=true&view=azure-ml-py) verweist auf eine einzelne Datei oder auf mehrere Dateien in Ihren Datenspeichern oder öffentlichen URLs. Wenn Ihre Daten bereits bereinigt und für Trainingsexperimente bereit sind, können Sie die Dateien als FileDataset-Objekt für Ihre Computeinstanz [herunterladen oder einbinden](how-to-train-with-datasets.md#mount-vs-download). 

Wir empfehlen FileDatasets für Ihre Machine Learning-Workflows, da die Quelldateien in jedem beliebigen Format vorliegen können, was ein breiteres Spektrum von Machine Learning-Szenarien ermöglicht, einschließlich Deep Learning.

Erstellen eines FileDataset mit dem [Python SDK](#create-a-filedataset) oder [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).
### <a name="tabulardataset"></a>TabularDataset

Ein [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) stellt Daten in einem tabellarischen Format dar, indem die bereitgestellte Datei oder Liste von Dateien analysiert wird. Dadurch haben Sie die Möglichkeit, die Daten in einem Pandas- oder Spark-Datenrahmen zu materialisieren, sodass Sie mit vertrauten Datenaufbereitungs- und Trainingsbibliotheken arbeiten können, ohne das Notebook verlassen zu müssen. Ein Objekt vom Typ `TabularDataset` kann auf der Grundlage von CSV-, TSV-, Parquet- und JSONL-Dateien sowie auf der Grundlage von [SQL-Abfrageergebnissen](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) erstellt werden.

Mit TabularDataset-Objekten können Sie einen Zeitstempel auf der Grundlage einer Spalte in den Daten oder auf der Grundlage des Orts angeben, an dem die Pfadmusterdaten gespeichert sind, um ein Zeitreihenmerkmal zu aktivieren. Diese Angabe ermöglicht einfaches und effizientes Filtern nach Zeit. Ein Beispiel finden Sie unter [Demo einer tabellarischen, zeitreihenbezogenen API mit NOAA-Wetterdaten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Erstellen Sie ein TabularDataset mit dem [Python SDK](#create-a-tabulardataset) oder [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> AutoML-Workflows, die über das Azure Machine Learning Studio generiert werden, unterstützen derzeit nur TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Zugreifen auf Datasets in einem virtuellen Netzwerk

Wenn sich Ihr Arbeitsbereich in einem virtuellen Netzwerk befindet, müssen Sie das Dataset so konfigurieren, dass die Überprüfung übersprungen wird. Weitere Informationen zur Verwendung von Datenspeichern und Datasets in einem virtuellen Netzwerk finden Sie unter [Sichern eines Azure Machine Learning-Arbeitsbereichs mit virtuellen Netzwerken](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets"></a>Erstellen von Datasets

Damit Azure Machine Learning auf die Daten zugreifen kann, müssen Datasets auf der Grundlage von Pfaden in [Azure-Datenspeichern](how-to-access-data.md) oder auf der Grundlage öffentlicher Web-URLs erstellt werden. 

So erstellen Sie Datasets aus einem [Azure-Datenspeicher](how-to-access-data.md) mit dem Python SDK:

1. Vergewissern Sie sich, dass Sie für den registrierten Azure-Datenspeicher über Zugriff vom Typ `contributor` oder `owner` verfügen.

2. Erstellen Sie das Dataset, indem Sie auf Pfade im Datenspeicher verweisen. Ein Dataset kann aus mehreren Pfaden in mehreren Datenspeichern erstellt werden. Es gibt keine festen Grenzwerte für die Anzahl der Dateien oder die Datengröße, mit denen Sie ein Dataset erstellen können. 

> [!NOTE]
> Für jeden Datenpfad werden einige Anforderungen an den Speicherdienst gesendet, um zu prüfen, ob er auf eine Datei oder einen Ordner verweist. Dieser Aufwand kann zu einer Beeinträchtigung der Leistung oder zu einem Fehler führen. Ein Dataset, das auf einen Ordner mit 1000 Dateien verweist, bezieht sich auf einen Datenpfad. Es wird empfohlen, für eine optimale Leistung ein Dataset zu erstellen, das auf weniger als 100 Pfade in Datenspeichern verweist.

### <a name="create-a-filedataset"></a>Erstellen eines FileDataset-Elements

Verwenden Sie die Methode [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#&preserve-view=truefrom-files-path--validate-true-) für die Klasse `FileDatasetFactory`, um Dateien in einem beliebigen Format zu laden und ein nicht registriertes FileDataset-Objekt zu erstellen. 

Wenn sich Ihr Speicher hinter einem virtuellen Netzwerk oder einer Firewall befindet, legen Sie den Parameter `validate=False` in Ihrer `from_files()`-Methode fest. Dadurch wird der erste Überprüfungsschritt umgangen und sichergestellt, dass Sie Ihr Dataset aus diesen sicheren Dateien erstellen können. Informieren Sie sich über die [Verwendung von Datenspeichern und Datasets in einem virtuellen Netzwerk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
[Registrieren Sie Ihr Dataset](#register-datasets), um Datasets in Ihrem Arbeitsbereich experimentübergreifend wiederverwenden und freigeben zu können. 

> [!TIP] 
> Laden Sie Dateien aus einem lokalen Verzeichnis hoch, und erstellen Sie ein FileDataset-Element in einer einzelnen Methode mit der öffentlichen Vorschaumethode [upload_directory()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?preserve-view=true&view=azure-ml-py#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Diese Methode ist eine [experimentelle](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) Previewfunktion und kann jederzeit geändert werden. 
> 
>  Mit dieser Methode werden Daten in den zugrunde liegenden Speicher hochgeladen, was zu Speicherkosten führt. 
### <a name="create-a-tabulardataset"></a>Erstellen eines TabularDataset-Elements

Verwenden Sie die Methode [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) für die Klasse `TabularDatasetFactory`, um Dateien im CSV- oder TSV-Format zu lesen und ein nicht registriertes TabularDataset-Objekt zu erstellen. Wenn Sie Daten aus mehreren Dateien lesen, werden die Ergebnisse in einer Tabellendarstellung aggregiert. 

Wenn sich Ihr Speicher hinter einem virtuellen Netzwerk oder einer Firewall befindet, legen Sie den Parameter `validate=False` in Ihrer `from_delimited_files()`-Methode fest. Dadurch wird der erste Überprüfungsschritt umgangen und sichergestellt, dass Sie Ihr Dataset aus diesen sicheren Dateien erstellen können. Erfahren Sie mehr über die Verwendung von [Datenspeichern und Datasets in einem virtuellen Netzwerk](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

Mit dem folgenden Code werden der vorhandene Arbeitsbereich und der gewünschte Datenspeicher anhand des Namens abgerufen. Anschließend werden die Speicherorte von Datenspeicher und Datei dem `path`-Parameter übergeben, um ein neues TabularDataset `weather_ds` zu erstellen.

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Beim Erstellen eines TabularDataset-Objekts werden Spaltendatentypen standardmäßig automatisch abgeleitet. Sollten die abgeleiteten Typen nicht Ihren Erwartungen entsprechen, können Sie den folgenden Code verwenden, um Spaltentypen anzugeben. Der Parameter `infer_column_type` ist nur für Datasets anwendbar, die aus durch Trennzeichen getrennten Dateien erstellt wurden. [Weitere Informationen zu unterstützten Datentypen](/python/api/azureml-core/azureml.data.dataset_factory.datatype?preserve-view=true&view=azure-ml-py).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|(Index)|PassengerId|Survived|Pclass|Name|Geschlecht|Age|SibSp|Parch|Ticket|Fare|Cabin|Embarked
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|False|3|Braund, Mr. Owen Harris|male|22,0|1|0|A/5 21171|7.2500||E
1|2|True|1|Cumings, Mrs. John Bradley (Florence Briggs Th...|female|38,0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, Miss. Laina|female|26,0|0|0|STON/O2. 3101282|7.9250||E

[Registrieren Sie Ihr Dataset](#register-datasets), um Datasets in Ihrem Arbeitsbereich experimentübergreifend wiederverwenden und freigeben zu können.

## <a name="create-a-dataset-from-pandas-dataframe"></a>Erstellen eines Datasets aus Pandas-Datenrahmen

Um ein TabularDataset aus einem In-Memory-Pandas-Datenrahmen zu erstellen, schreiben Sie die Daten in eine lokale Datei, z. B. eine CSV-Datei, und erstellen Sie Ihr Dataset aus dieser Datei. Der folgende Code veranschaulicht diesen Workflow.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Erstellen und registrieren Sie ein TabularDataset-Element aus einem In-Memory-Spark- oder -Pandas-Datenrahmen mit einer einzelnen Methode mithilfe der öffentlichen Vorschaumethoden [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods) und [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?preserve-view=true&view=azure-ml-py#methods). Diese Registrierungsmethoden sind [experimentelle](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) Previewfunktionen und können jederzeit geändert werden. 
> 
>  Mit ihnen werden Daten in den zugrunde liegenden Speicher hochgeladen, was zu Speicherkosten führt. 

## <a name="register-datasets"></a>Registrieren von Datasets

Registrieren Sie Ihre Datasets bei einem Arbeitsbereich, um den Erstellungsprozess abzuschließen. Verwenden Sie die Methode [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-), um Datasets bei Ihrem Arbeitsbereich zu registrieren, damit sie für andere freigegeben und für Experimente in Ihrem Arbeitsbereich wiederverwendet werden können:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Erstellen von Datasets mithilfe des Azure Resource Managers

Es gibt eine Reihe von Vorlagen unter [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/), die zum Erstellen von Datasets verwendet werden können.

Informationen zur Verwendung dieser Vorlagen finden Sie unter [Verwenden einer Azure Resource Manager-Vorlage zum Erstellen eines Arbeitsbereichs für Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-with-azure-open-datasets"></a>Erstellen von Datasets mit Azure Open Datasets

[Öffentliche Azure-Datasets](https://azure.microsoft.com/services/open-datasets/) sind kuratierte öffentliche Datasets, mit denen Sie Lösungen mit maschinellem Lernen szenariospezifische Features hinzufügen können, um genauere Modelle zu erzielen. Die Datasets umfassen gemeinfreie Daten für Wetter, Volkszählungen, Feiertage, öffentliche Sicherheit und Orte, mit denen Sie Machine Learning-Modelle trainieren und Vorhersagelösungen anreichern können. Open Datasets befindet sich in der Cloud unter Microsoft Azure und ist sowohl im SDK als auch in Studio enthalten.

Erfahren Sie, wie Sie [Azure Machine Learning-Datasets aus Azure Open Datasets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md) erstellen. 

## <a name="train-with-datasets"></a>Trainieren mit Datasets

Verwenden Sie Ihre Datasets in Ihren Machine Learning-Experimenten zum Trainieren von ML-Modellen. [Erfahren Sie mehr über das Trainieren mit Datasets](how-to-train-with-datasets.md)

## <a name="version-datasets"></a>Versionsverwaltung von Datasets

Sie können ein neues Dataset unter demselben Namen registrieren, indem Sie eine neue Version erstellen. Eine Datasetversion ermöglicht es, den Zustand der Daten zu markieren, sodass Sie eine bestimmte Version des Datasets für Experimente oder zukünftige Reproduktion verwenden können. Weitere Informationen zu Datasetversionen finden Sie [hier](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie mit Datasets trainiert wird](how-to-train-with-datasets.md).
* Verwenden Sie automatisiertes Machine Learning, um [mit TabularDatasets zu trainieren](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Weitere Beispiele zum Trainieren von Datasets finden Sie in den [Beispielnotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
