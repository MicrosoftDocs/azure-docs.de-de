---
title: Trainieren mit „azureml-datasets“
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihre Daten für das ML-Modelltraining mit Azure Machine Learning-Datasets lokal oder remote verfügbar machen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: b6ec9d7035194efc471fc06befad9822c8684a5d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685578"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Trainieren mit Datasets in Azure Machine Learning


In diesem Artikel erfahren Sie, wie Sie [Azure Machine Learning-Datasets](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py) in Ihren Trainingsexperimenten einsetzen.  Sie können Datasets in Ihrem Lokal- oder Remotecomputeziel verwenden, ohne sich Gedanken über Verbindungszeichenfolgen oder Datenpfade machen zu müssten.

Azure Machine Learning-Datasets bieten eine nahtlose Integration in Azure Machine Learning-Trainingsfunktionen wie [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py), [HyperDrive](/python/api/azureml-train-core/azureml.train.hyperdrive?preserve-view=true&view=azure-ml-py) und [Azure Machine Learning-Pipelines](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Datasets zu erstellen und für das Training zu nutzen:

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

* Eine [Installation des Azure Machine Learning-SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (mindestens 1.13.0), in dem das Paket „azureml-datasets“ enthalten ist

> [!Note]
> Einige Datasetklassen sind vom Paket [azureml-dataprep](/python/api/azureml-dataprep/?preserve-view=true&view=azure-ml-py) abhängig. Für Linux-Benutzer werden diese Klassen nur unter den folgenden Distributionen unterstützt:  Red Hat Enterprise Linux, Ubuntu, Fedora und CentOS.

## <a name="use-datasets-directly-in-training-scripts"></a>Direktes Verwenden von Datasets in Trainingsskripts

Wenn Sie strukturierte Daten besitzen, die noch nicht als Dataset registriert wurden, können Sie eine TabularDataset-Klasse erstellen und diese direkt im Trainingsskript für das Lokal- oder Remoteexperiment verwenden.

In diesem Beispiel erstellen Sie ein nicht registriertes [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py)-Element und geben es als Skriptargument im ScriptRunConfig-Objekt für das Training an. Wenn Sie diese TabularDataset-Klasse für andere Experimente in Ihrem Arbeitsbereich wiederverwenden möchten, finden Sie weitere Informationen unter [Registrieren von Datasets](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Erstellen eines TabularDataset-Elements

Der folgende Code erstellt ein nicht registriertes TabularDataset-Element aus einer Web-URL.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

TabularDataset-Objekte bieten die Möglichkeit, die TabularDataset-Daten in einen Pandas- oder Spark-Datenrahmen zu laden, damit Sie mit vertrauten Datenaufbereitungs- und Trainingsbibliotheken arbeiten können, ohne das Notebook zu verlassen.

### <a name="access-dataset-in-training-script"></a>Zugreifen auf das Dataset im Trainingsskript

Mit dem folgenden Code wird das Skriptargument `--input-data` konfiguriert, das Sie beim Konfigurieren der Trainingsausführung angeben (siehe nächster Abschnitt). Wenn das Tabellendataset als Argumentwert übergeben wird, wird dieser von Azure ML in die ID des Datasets aufgelöst. Sie können diese ID dann für den Zugriff auf das Dataset im Trainingsskript verwenden (ohne den Namen oder die ID des Datasets in Ihrem Skript hartcodieren zu müssen). Anschließend wird die [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--)-Methode verwendet, um das Dataset in einen Pandas-Datenrahmen zu laden und die Datenuntersuchung und -vorbereitung vor dem Training zu vertiefen.

> [!Note]
> Wenn die ursprüngliche Datenquelle einen nicht numerischen Wert, leere Zeichenfolgen oder leere Werte enthält, werden diese Werte bei Verwendung von `to_pandas_dataframe()` durch *NULL*-Werte ersetzt.

Wenn Sie die vorbereiteten Daten aus einem In-Memory-Pandas-Datenrahmen in ein neues Dataset laden müssen, schreiben Sie die Daten in eine lokale Datei, z. B. eine Parquet-Datei, und erstellen das neue Dataset aus dieser Datei. Sie können Datasets auch aus lokalen Dateien oder Pfaden in Datenspeichern erstellen. Erfahren Sie mehr über das [Erstellen von Datasets](how-to-create-register-datasets.md).

```Python
%%writefile $script_folder/train_titanic.py

import argparse
from azureml.core import Dataset, Run

parser = argparse.ArgumentParser()
parser.add_argument("--input-data", type=str)
args = parser.parse_args()

run = Run.get_context()
ws = run.experiment.workspace

# get the input dataset by ID
dataset = Dataset.get_by_id(ws, id=args.input_data)

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-training-run"></a>Konfigurieren der Trainingsausführung
Zum Konfigurieren und Übermitteln der Trainingsausführung wird ein Objekt vom Typ [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrun?preserve-view=true&view=azure-ml-py) verwendet.

Dieser Code erstellt ein ScriptRunConfig-Objekt (`src`), das Folgendes festlegt:

* Ein Skriptverzeichnis für Ihre Skripts. Alle Dateien in dieses Verzeichnis werden zur Ausführung in die Clusterknoten hochgeladen.
* Das Trainingsskript *train_titanic.py*
* Das Eingabedataset für das Training (`titanic_ds`) als Skriptargument. Azure ML löst dies in die entsprechende ID des Datasets auf, wenn es an Ihr Skript übergeben wird.
* Das Computeziel für die Ausführung
* Die Umgebung für die Ausführung

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_titanic.py',
                      # pass dataset as an input with friendly name 'titanic'
                      arguments=['--input-data', titanic_ds.as_named_input('titanic')],
                      compute_target=compute_target,
                      environment=myenv)
                             
# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)                             
```

## <a name="mount-files-to-remote-compute-targets"></a>Einbinden von Dateien in Remotecomputezielen

Wenn Sie unstrukturierte Daten besitzen, sollten Sie eine [FileDataset](/python/api/azureml-core/azureml.data.filedataset?preserve-view=true&view=azure-ml-py)-Klasse erstellen und Ihre Datendateien einbinden oder herunterladen, damit diese vom Remotecomputeziel für das Training genutzt werden können. Lesen Sie nach, wann Sie Daten [einbinden oder herunterladen](#mount-vs-download) sollten, wenn Sie Remotetrainingsexperimente durchführen. 

Im folgenden Beispiel wird eine FileDataset-Klasse erstellt, und das Dataset wird in das Computeziel eingebunden, indem es als Argument an das Trainingsskript übergeben wird. 

> [!Note]
> Wenn Sie ein benutzerdefiniertes Docker-Basisimage verwenden, müssen Sie fuse über `apt-get install -y fuse` als Abhängigkeit für die Durchführung der Datasetinstallation installieren. Erfahren Sie, wie Sie ein [benutzerdefiniertes Buildimage erstellen](how-to-deploy-custom-docker-image.md#build-a-custom-base-image).

### <a name="create-a-filedataset"></a>Erstellen eines FileDataset-Elements

Im folgenden Beispiel wird ein nicht registriertes FileDataset-Element aus Web-URLs erstellt. Erfahren Sie mehr über das [Erstellen von Datasets](how-to-create-register-datasets.md) aus anderen Quellen.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-training-run"></a>Konfigurieren der Trainingsausführung
Es wird empfohlen, das Dataset bei der Einbindung als Argument über den Parameter `arguments` des Konstruktors `ScriptRunConfig` zu übergeben. Auf diese Weise erhalten Sie den Datenpfad (Bereitstellungspunkt) im Trainingsskript über Argumente. So können Sie dasselbe Trainingsskript für lokales Debugging und Remotetraining auf jeder Cloudplattform verwenden.

Im folgenden Beispiel wird ein ScriptRunConfig-Element erstellt, das das FileDataset-Element über `arguments` übergibt. Nach dem Übermitteln der Ausführung werden Datendateien, auf die das `mnist_ds`-Dataset verweist, auf dem Computeziel eingebunden.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory=script_folder,
                      script='train_mnist.py',
                      # the dataset will be mounted on the remote compute and the mounted path passed as an argument to the script
                      arguments=['--data-folder', mnist_ds.as_mount(), '--regularization', 0.5],
                      compute_target=compute_target,
                      environment=myenv)

# Submit the run configuration for your training run
run = experiment.submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Abrufen der Daten im Trainingsskript

Der folgende Code zeigt, wie Sie die Daten in Ihrem Skript abrufen können.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through `arguments` in the ScriptRunConfig
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="mount-vs-download"></a>Vergleich: Einbinden oder Herunterladen

Das Einbinden oder Herunterladen von Dateien beliebiger Formate aus Azure Blob Storage, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL-Datenbank und Azure Database for PostgreSQL wird für Datasets beliebiger Formate unterstützt. 

Wenn Sie ein Dataset **einbinden**, fügen Sie die Datei, auf die das Dataset verweist, an ein Verzeichnis (Bereitstellungspunkt) an und stellen sie auf dem Computeziel zur Verfügung. Einbinden wird für Linux-basierte Computeressourcen unterstützt, einschließlich Azure Machine Learning Compute, virtuelle Computer und HDInsight. 

Beim **Herunterladen** eines Datasets werden alle Dateien, auf die das Dataset verweist, auf das Computeziel heruntergeladen. Herunterladen wird für alle Computetypen unterstützt. 

Wenn Ihr Skript alle Dateien verarbeitet, auf die das Dataset verweist, und Ihr Computedatenträger über Platz für das vollständige Dataset verfügt, wird Herunterladen empfohlen, um den Mehraufwand das Streamen von Daten von Speicherdiensten zu vermeiden. Wenn der Umfang Ihrer Daten die Größe des Computedatenträgers übersteigt, ist das Herunterladen nicht möglich. Für dieses Szenario empfehlen wir das Einbinden, da nur die von Ihrem Skript verwendeten Datendateien zum Zeitpunkt der Verarbeitung geladen werden.

Der folgende Code bindet `dataset` in das temporäre Verzeichnis unter `mounted_path` ein.

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

## <a name="directly-access-datasets-in-your-script"></a>Direktes Zugreifen auf Datasets in Ihrem Skript

Auf registrierte Datasets kann sowohl lokal als auch remote in Computeclustern wie Azure Machine Learning Compute zugegriffen werden. Verwenden Sie für den experimentübergreifenden Zugriff auf Ihr registriertes Dataset den folgenden Code, um anhand des Namens auf Ihren Arbeitsbereich und das registrierte Dataset zuzugreifen. Die Methode [`get_by_name()`](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) für die Klasse `Dataset` gibt standardmäßig die neueste Version des im Arbeitsbereich registrierten Datasets zurück.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Zugreifen auf den Quellcode während des Trainings

Azure Blob Storage verfügt über eine höhere Durchsatzgeschwindigkeit als die Azure-Dateifreigabe und wird auf eine hohe Anzahl parallel gestarteter Aufträge skaliert. Aus diesem Grund empfiehlt es sich, Ausführungen so zu konfigurieren, dass für die Übertragung von Quellcodedateien Blobspeicher verwendet wird.

Im folgenden Codebeispiel wird in der Laufzeitkonfiguration angegeben, welcher Blobdatenspeicher für Quellcodeübertragungen verwendet werden soll.

```python 
# workspaceblobstore is the default blob storage
src.run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Notebook-Beispiele

+ Die [Dataset-Notebooks](https://aka.ms/dataset-tutorial) veranschaulichen und erläutern die in diesem Artikel enthaltenen Konzepte.
+ Informationen zum Parametrisieren von Datasets in ML-Pipelines finden Sie [hier](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb).

## <a name="next-steps"></a>Nächste Schritte

* [Automatisches Trainieren von Machine Learning-Modellen](how-to-auto-train-remote.md) mit TabularDatasets

* [Trainieren von Bildklassifizierungsmodellen](https://aka.ms/filedataset-samplenotebook) mit FileDatasets

* [Training mit Datasets mithilfe von Pipelines](how-to-create-your-first-pipeline.md)
