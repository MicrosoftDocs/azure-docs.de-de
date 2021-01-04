---
title: Verwenden von automatisiertem ML in ML-Pipelines
titleSuffix: Azure Machine Learning
description: AutoMLStep ermöglicht Ihnen die Verwendung von automatisiertem maschinellen Lernen (ML) in Ihren Pipelines.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
manager: cgronlun
ms.date: 08/26/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl
ms.openlocfilehash: 4cbe43f224ddf349db6b182feb3a717bb2bfd32e
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358829"
---
# <a name="use-automated-ml-in-an-azure-machine-learning-pipeline-in-python"></a>Verwenden von automatisiertem ML in einer Azure Machine Learning-Pipeline in Python


Mit den Azure Machine Learning-Funktionen für automatisiertes ML können Sie hochleistungsfähige Modelle ermitteln, ohne jeden möglichen Ansatz neu implementieren zu müssen. In Kombination mit Azure Machine Learning-Pipelines können Sie bereitstellbare Workflows erstellen, die im Handumdrehen den besten Algorithmus für Ihre Daten ermitteln. In diesem Artikel erfahren Sie, wie Sie einen Datenaufbereitungsschritt effizient mit einem Schritt für automatisiertes ML verknüpfen. Mithilfe von automatisiertem ML können Sie in kürzester Zeit den Algorithmus ermitteln, der für Ihre Daten am besten geeignet ist. Gleichzeitig schaffen Sie die Grundlage für MLOps und die Operationalisierung des Modelllebenszyklus mit Pipelines.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).  

* Kenntnisse der Azure-Funktionen für [automatisiertes maschinelles Lernen](concept-automated-ml.md) und [ML-Pipelines](concept-ml-pipelines.md) sowie des SDK.

## <a name="review-automated-mls-central-classes"></a>Überblick über die wichtigsten Klassen für automatisiertes ML

Automatisiertes ML in einer Pipeline wird durch ein `AutoMLStep`-Objekt dargestellt. Die `AutoMLStep`-Klasse ist eine Unterklasse von `PipelineStep`. Ein Diagramm mit `PipelineStep`-Objekten definiert eine `Pipeline`.

`PipelineStep` verfügt über mehrere Unterklassen. Neben `AutoMLStep` wird in diesem Artikel ein `PythonScriptStep` zur Datenaufbereitung sowie ein weiterer Schritt zum Registrieren des Modells erläutert.

Die bevorzugte Methode für das anfängliche Verschieben von Daten _in_ eine ML-Pipeline ist die Verwendung von `Dataset`-Objekten. Um Daten _zwischen_ Schritten zu verschieben, ist die Verwendung von `PipelineData`-Objekten die bevorzugte Vorgehensweise. Für die Verwendung mit `AutoMLStep` muss das `PipelineData`-Objekt in ein `PipelineOutputTabularDataset`-Objekt transformiert werden. Weitere Informationen finden Sie unter [Verschieben von Daten in ML-Pipelineschritte und zwischen ML-Pipelineschritten (Python)](how-to-move-data-in-out-of-pipelines.md).


> [!TIP]
> Eine verbesserte Oberfläche zum Übergeben von temporären Daten zwischen Pipelineschritten steht in den Public Preview-Klassen, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) und [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py), zur Verfügung.  Diese Klassen sind [experimentelle](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) Previewfunktionen und können jederzeit geändert werden.

`AutoMLStep` wird über ein `AutoMLConfig`-Objekt konfiguriert. `AutoMLConfig` ist eine flexible Klasse, wie unter [Konfigurieren automatisierter ML-Experimente in Python](./how-to-configure-auto-train.md#configure-your-experiment-settings) erläutert. 

Eine `Pipeline` wird in einem `Experiment` ausgeführt. Die Pipelineausführung (`Run`) verfügt über ein untergeordnetes Ausführungselement für jeden Schritt (`StepRun`). Der `StepRun` für automatisiertes ML gibt die Trainingsmetriken und das Modell mit der höchsten Leistung aus.

Zur Veranschaulichung wird in diesem Artikel eine einfache Pipeline für eine Klassifizierungsaufgabe erstellt. Mit dieser Aufgabe werden die Überlebenszahlen für die Titanic vorhergesagt. Die Daten oder die Aufgabe werden jedoch nicht näher erläutert.

## <a name="get-started"></a>Erste Schritte

### <a name="retrieve-initial-dataset"></a>Abrufen des anfänglichen Datasets

Ein ML-Workflow beginnt häufig mit bereits vorhandenen Baselinedaten. Dieses Szenario eignet sich gut für ein registriertes Dataset. Datasets sind innerhalb des gesamten Arbeitsbereichs sichtbar, unterstützen die Versionsverwaltung und können interaktiv untersucht werden. Datasets lassen sich über eine Vielzahl von Methoden erstellen und auffüllen, wie unter [Erstellen von Azure Machine Learning-Datasets](how-to-create-register-datasets.md) beschrieben. Da wir unsere Pipeline mit dem Python SDK erstellen, sollten Sie Baselinedaten mit dem SDK herunterladen und mit dem Namen „titanic_ds“ registrieren.

```python
from azureml.core import Workspace, Dataset

ws = Workspace.from_config()
if not 'titanic_ds' in ws.datasets.keys() :
    # create a TabularDataset from Titanic training data
    web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
                 'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
    titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

    titanic_ds.register(workspace = ws,
                                     name = 'titanic_ds',
                                     description = 'Titanic baseline data',
                                     create_new_version = True)

titanic_ds = Dataset.get_by_name(ws, 'titanic_ds')
```

Über den Code wird zunächst die Anmeldung beim Azure Machine Learning-Arbeitsbereich durchgeführt, der in **config.json** definiert ist (eine Erläuterung finden Sie unter [Erstellen einer Konfigurationsdatei für den Arbeitsbereich](how-to-configure-environment.md#workspace)). Wenn noch kein Dataset mit dem Namen `'titanic_ds'` registriert wurde, wird dieses Dataset erstellt. Der Code lädt CSV-Daten aus dem Internet herunter, verwendet diese Daten zum Instanziieren eines `TabularDataset` und registriert das Dataset dann beim Arbeitsbereich. Anschließend wird das `Dataset` über die Funktion `Dataset.get_by_name()` zu `titanic_ds` zugewiesen. 

### <a name="configure-your-storage-and-compute-target"></a>Konfigurieren Ihres Speicher- und Computeziels

Die Pipeline benötigt als zusätzliche Ressourcen Speicher und üblicherweise Azure Machine Learning-Computeressourcen. 

```python
from azureml.core import Datastore
from azureml.core.compute import AmlCompute, ComputeTarget

datastore = ws.get_default_datastore()

compute_name = 'cpu-cluster'
if not compute_name in ws.compute_targets :
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                min_nodes=0,
                                                                max_nodes=1)
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)

    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # Show the result
    print(compute_target.get_status().serialize())

compute_target = ws.compute_targets[compute_name]
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Die Zwischendaten zwischen den Schritten für Datenaufbereitung und automatisiertes ML können im standardmäßigen Datenspeicher des Arbeitsbereichs gespeichert werden, sodass lediglich `get_default_datastore()` für das `Workspace`-Objekt aufgerufen werden muss. 

Anschließend überprüft der Code, ob das AML-Computeziel `'cpu-cluster'` bereits vorhanden ist. Falls nicht, geben wir an, dass ein kleines CPU-basiertes Computeziel verwendet werden soll. Wenn Sie beabsichtigen, die Deep Learning-Funktionen von automatisiertem ML zu verwenden (z. B. die Textfeaturisierung mit DNN-Unterstützung), sollten Sie ein Computeziel mit umfassender GPU-Unterstützung auswählen. Dies wird unter [Für GPU optimierte VM-Größen](../virtual-machines/sizes-gpu.md) näher beschrieben. 

Der Code wird erst dann weiter ausgeführt, wenn das Ziel bereitgestellt wurde. Dann werden Details des soeben erstellten Computeziels geschrieben. Schließlich wird das benannte Computeziel aus dem Arbeitsbereich abgerufen und `compute_target` zugewiesen. 

### <a name="configure-the-training-run"></a>Konfigurieren der Trainingsausführung

Im nächsten Schritt wird sichergestellt, dass die Remotetrainingsausführung über alle Abhängigkeiten verfügt, die für die Trainingsschritte erforderlich sind. Abhängigkeiten und der Laufzeitkontext werden festgelegt, indem ein `RunConfiguration`-Objekt erstellt und konfiguriert wird. 

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import Environment 

aml_run_config = RunConfiguration()
# Use just-specified compute target ("cpu-cluster")
aml_run_config.target = compute_target

USE_CURATED_ENV = True
if USE_CURATED_ENV :
    curated_environment = Environment.get(workspace=ws, name="AzureML-Tutorial")
    aml_run_config.environment = curated_environment
else:
    aml_run_config.environment.python.user_managed_dependencies = False
    
    # Add some packages relied on by data prep step
    aml_run_config.environment.python.conda_dependencies = CondaDependencies.create(
        conda_packages=['pandas','scikit-learn'], 
        pip_packages=['azureml-sdk[automl]', 'azureml-dataprep[fuse,pandas]'], 
        pin_sdk_version=False)
```

Der obige Code zeigt zwei Optionen für die Behandlung von Abhängigkeiten. In dieser Darstellung basiert die Konfiguration bei `USE_CURATED_ENV = True` auf einer zusammengestellten Umgebung. Zusammengestellte Umgebungen sind bereits vorkonfiguriert und weisen gemeinsame voneinander abhängige Bibliotheken auf. Somit können sie erheblich schneller online gestellt werden. Für zusammengestellte Umgebungen sind in [Microsoft Container Registry](https://hub.docker.com/publishers/microsoftowner) vorab erstellte Docker-Images vorhanden. Wenn Sie `USE_CURATED_ENV` in `False` ändern, wird ein Pfad durchlaufen, der das Muster zum expliziten Festlegen Ihrer Abhängigkeiten zeigt. In diesem Szenario wird ein neues benutzerdefiniertes Docker-Image erstellt und bei einer Azure Container Registry-Instanz in Ihrer Ressourcengruppe registriert (siehe [Einführung in private Docker-Containerregistrierungen in Azure](../container-registry/container-registry-intro.md)). Das Erstellen und Registrieren dieses Images kann längere Zeit dauern. 

## <a name="prepare-data-for-automated-machine-learning"></a>Vorbereiten von Daten für automatisiertes maschinelles Lernen

### <a name="write-the-data-preparation-code"></a>Schreiben des Datenaufbereitungscodes

Das Titanic-Baselinedataset besteht aus einer Kombination aus numerischen Daten und Textdaten, wobei einige Werte fehlen. Um dieses Dataset für automatisiertes ML vorzubereiten, werden im Pipelineschritt zur Datenaufbereitung folgende Aufgaben ausgeführt:

- Auffüllen von fehlenden Daten mit Zufallsdaten oder einer Kategorie, die „Unknown“ entspricht
- Transformieren von kategorischen Daten in ganze Zahlen
- Entfernen von Spalten, die nicht verwendet werden sollen
- Unterteilen der Daten in Trainings- und Testsätze
- Schreiben der transformierten Daten in einen der beiden
    - `PipelineData`-Ausgabepfade

```python
%%writefile dataprep.py
from azureml.core import Run

import pandas as pd 
import numpy as np 
import pyarrow as pa
import pyarrow.parquet as pq
import argparse

RANDOM_SEED=42

def prepare_age(df):
    # Fill in missing Age values from distribution of present Age values 
    mean = df["Age"].mean()
    std = df["Age"].std()
    is_null = df["Age"].isnull().sum()
    # compute enough (== is_null().sum()) random numbers between the mean, std
    rand_age = np.random.randint(mean - std, mean + std, size = is_null)
    # fill NaN values in Age column with random values generated
    age_slice = df["Age"].copy()
    age_slice[np.isnan(age_slice)] = rand_age
    df["Age"] = age_slice
    df["Age"] = df["Age"].astype(int)
    
    # Quantize age into 5 classes
    df['Age_Group'] = pd.qcut(df['Age'],5, labels=False)
    df.drop(['Age'], axis=1, inplace=True)
    return df

def prepare_fare(df):
    df['Fare'].fillna(0, inplace=True)
    df['Fare_Group'] = pd.qcut(df['Fare'],5,labels=False)
    df.drop(['Fare'], axis=1, inplace=True)
    return df 

def prepare_genders(df):
    genders = {"male": 0, "female": 1, "unknown": 2}
    df['Sex'] = df['Sex'].map(genders)
    df['Sex'].fillna(2, inplace=True)
    df['Sex'] = df['Sex'].astype(int)
    return df

def prepare_embarked(df):
    df['Embarked'].replace('', 'U', inplace=True)
    df['Embarked'].fillna('U', inplace=True)
    ports = {"S": 0, "C": 1, "Q": 2, "U": 3}
    df['Embarked'] = df['Embarked'].map(ports)
    return df
    
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
    
titanic_ds = Run.get_context().input_datasets['titanic_ds']
df = titanic_ds.to_pandas_dataframe().drop(['PassengerId', 'Name', 'Ticket', 'Cabin'], axis=1)
df = prepare_embarked(prepare_genders(prepare_fare(prepare_age(df))))

os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
pq.write_table(pa.Table.from_pandas(df), args.output_path)

print(f"Wrote test to {args.output_path} and train to {args.output_path}")
```

Der oben gezeigte Codeausschnitt ist ein vollständiges, jedoch minimales Beispiel der Datenaufbereitung für die Titanic-Daten. Zunächst wird ein Jupyter-Magic-Befehl verwendet, um den Code in einer Datei auszugeben. Wenn Sie kein Jupyter Notebook verwenden, entfernen Sie diese Zeile, und erstellen Sie die Datei manuell.

Die verschiedenen `prepare_`-Funktionen im obigen Codeausschnitt ändern die relevante Spalte im Eingabedataset. Mit diesen Funktionen werden die Daten bearbeitet, sobald sie in ein Pandas-`DataFrame`-Objekt geändert wurden. In jedem Fall werden fehlende Daten entweder mit repräsentativen Zufallsdaten oder mit kategorischen Daten gefüllt, die "Unknown" entsprechen. Textbasierte kategorische Daten werden ganzen Zahlen zugeordnet. Spalten, die nicht mehr benötigt werden, werden überschrieben oder entfernt. 

Nachdem die Datenaufbereitungsfunktionen über den Code definiert wurden, analysiert der Code das Eingabeargument (den Pfad zum Schreiben unserer Daten). (Diese Werte werden durch `PipelineData`-Objekte bestimmt, die im nächsten Schritt erläutert werden.) Der Code ruft das registrierte `Dataset` `'titanic_cs'` ab, wandelt es in ein Pandas-`DataFrame`-Element um, und ruft die verschiedenen Datenaufbereitungsfunktionen auf. 

Da `output_path` vollqualifiziert ist, wird die Funktion `os.makedirs()` zum Vorbereiten der Verzeichnisstruktur verwendet. An dieser Stelle könnten Sie `DataFrame.to_csv()` zum Schreiben der Ausgabedaten verwenden, Parquet-Dateien sind jedoch effizienter. Bei einem kleinen Dataset wie diesem ist diese Effizienz vermutlich nicht relevant, bei Verwendung der Funktionen `from_pandas()` und `write_table()` aus dem **PyArrow** -Paket müssen jedoch nur einige wenige Tasten mehr gedrückt werden als bei `to_csv()`.

Parquet-Dateien werden vom unten beschriebenen Schritt für automatisiertes ML nativ unterstützt, sodass keine speziellen Verarbeitungsschritte erforderlich sind, um diese Dateien zu nutzen. 

### <a name="write-the-data-preparation-pipeline-step-pythonscriptstep"></a>Schreiben des Pipelineschritts zur Datenaufbereitung (`PythonScriptStep`)

Für die Verwendung mit einer Pipeline muss der oben beschriebene Datenaufbereitungscode mit einem `PythonScripStep`-Objekt verknüpft werden. Der Pfad, in den die Parquet-Datenaufbereitungsausgabe geschrieben wird, wird über ein `PipelineData`-Objekt generiert. Die zuvor vorbereiteten Ressourcen (z. B. `ComputeTarget`, `RunConfig` und `'titanic_ds' Dataset`) werden verwendet, um die Spezifikation abzuschließen.

PipelineData-Benutzer
```python
from azureml.pipeline.core import PipelineData

from azureml.pipeline.steps import PythonScriptStep
prepped_data_path = PipelineData("titanic_train", datastore).as_dataset()

dataprep_step = PythonScriptStep(
    name="dataprep", 
    script_name="dataprep.py", 
    compute_target=compute_target, 
    runconfig=aml_run_config,
    arguments=["--output_path", prepped_data_path],
    inputs=[titanic_ds.as_named_input("titanic_ds")],
    outputs=[prepped_data_path],
    allow_reuse=True
)
```
Das `prepped_data_path`-Objekt ist vom Typ `PipelineOutputFileDataset`. Beachten Sie, dass es in den Argumenten `arguments` und `outputs` angegeben ist. Wenn Sie noch einmal zum vorherigen Schritt zurückkehren, sehen Sie, dass der Wert des Arguments `'--output_path'` im Datenaufbereitungcode der Dateipfad ist, in den die Parquet-Datei geschrieben wurde. 

> [!TIP]
> Eine verbesserte Oberfläche zum Übergeben von Zwischendaten zwischen Pipelineschritten steht in der Public Preview-Klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) zur Verfügung. Ein Codebeispiel der Verwendung der `OutputFileDatasetConfig`-Klasse finden Sie in der Schrittanleitung [Erstellen einer ML Pipeline mit zwei Schritten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="train-with-automlstep"></a>Trainieren mit AutoMLStep

Ein Pipelineschritt für automatisiertes ML wird mit der Klasse `AutoMLConfig` konfiguriert. Diese flexible Klasse ist unter [Konfigurieren automatisierter ML-Experimente in Python](./how-to-configure-auto-train.md) beschrieben. Bei der Konfiguration einer ML-Pipeline sind die Dateneingabe und -ausgabe die einzigen Aspekte, die besondere Aufmerksamkeit benötigen. Auf die Ein- und Ausgabe für `AutoMLConfig` in Pipelines wird nachfolgend näher eingegangen. Neben Daten ist die Möglichkeit, unterschiedliche Computeziele für verschiedene Schritte zu verwenden, ein weiterer Vorteil von ML-Pipelines. Sie können beispielsweise nur für den Prozess für automatisiertes ML ein leistungsfähigeres `ComputeTarget` verwenden. Dazu muss dem Parameter `run_configuration` des `AutoMLConfig`-Objekts lediglich eine leistungsfähigere `RunConfiguration` zugewiesen werden.

### <a name="send-data-to-automlstep"></a>Senden von Daten an `AutoMLStep`

In einer ML-Pipeline muss ein `Dataset`-Objekt für die Eingabedaten verwendet werden. Für eine optimale Leistung werden die Eingabedaten in Form von `PipelineOutputTabularDataset`-Objekten angegeben. Sie erstellen ein Objekt dieses Typs mit `parse_parquet_files()` oder `parse_delimited_files()` für ein `PipelineOutputFileDataset` (z. B. das `prepped_data_path`-Objekt).

```python
# type(prepped_data_path) == PipelineOutputFileDataset
# type(prepped_data) == PipelineOutputTabularDataset
prepped_data = prepped_data_path.parse_parquet_files(file_extension=None)
```

Über den oben gezeigten Codeausschnitt wird ein hochleistungsfähiges `PipelineOutputTabularDataset` anhand der `PipelineOutputFileDataset`-Ausgabe des Datenaufbereitungsschritts erstellt.

> [!TIP]
> Die Public Preview-Klasse, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py), enthält die Methode [read_delimited_files()](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py#&preserve-view=trueread-delimited-files-include-path-false--separator------header--promoteheadersbehavior-all-files-have-same-headers--3---partition-format-none--path-glob-none--set-column-types-none-), die ein `OutputFileDatasetConfig` in ein [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py) für die Verwendung in AutoML-Ausführungen konvertiert.

Eine weitere Option ist die Verwendung von `Dataset`-Objekten, die im Arbeitsbereich registriert sind:

```python
prepped_data = Dataset.get_by_name(ws, 'Data_prepared')
```

Vergleich der beiden Verfahren:

| Verfahren | Vor- und Nachteile | 
|-|-|
|`PipelineOutputTabularDataset`| Höhere Leistung | 
|| Natürliche Route von `PipelineData` | 
|| Daten werden nach der Pipelineausführung nicht persistent gespeichert |
|| [Notebook zur Veranschaulichung des `PipelineOutputTabularDataset`-Verfahrens](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) |
| Registriertes `Dataset` | Geringere Leistung |
| | Kann über verschiedene Methoden generiert werden | 
| | Daten werden persistent gespeichert und sind innerhalb des gesamten Arbeitsbereichs sichtbar |
| | [Notebook zur Veranschaulichung des Verfahrens mit registriertem `Dataset`](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/continuous-retraining/auto-ml-continuous-retraining.ipynb)


### <a name="specify-automated-ml-outputs"></a>Festlegen der Ausgabe von automatisiertem ML

Als Ausgabe von `AutoMLStep` werden die finalen Metrikergebnisse des Modells mit der höheren Leistung sowie dieses Modell selbst ausgegeben. Um diese Ausgabe in weiteren Pipelineschritten zu verwenden, bereiten Sie `PipelineData`-Objekte vor, um die Ausgabedaten zu empfangen.

```python

from azureml.pipeline.core import TrainingOutput

metrics_data = PipelineData(name='metrics_data',
                            datastore=datastore,
                            pipeline_output_name='metrics_output',
                            training_output=TrainingOutput(type='Metrics'))

model_data = PipelineData(name='best_model_data',
                          datastore=datastore,
                          pipeline_output_name='model_output',
                          training_output=TrainingOutput(type='Model'))
```

Mit dem oben gezeigten Codeausschnitt werden die beiden `PipelineData`-Objekte für die Ausgabe der Metriken und des Modells erstellt. Für jedes dieser Objekte wird ein Name festgelegt, und das Objekt wird dem zuvor abgerufenen Standarddatenspeicher zugewiesen. Außerdem wird es dem jeweiligen `type` von `TrainingOutput` aus `AutoMLStep` zugeordnet. Da wir `pipeline_output_name` für diese `PipelineData`-Objekte zuweisen, sind ihre Werte nicht nur über den einzelnen Pipelineschritt, sondern über die gesamte Pipeline verfügbar. Dies wird weiter unten im Abschnitt zum Untersuchen der Pipelineergebnisse näher beschrieben. 

### <a name="configure-and-create-the-automated-ml-pipeline-step"></a>Konfigurieren und Erstellen des Pipelineschritts für automatisiertes ML

Nachdem die Ein- und Ausgaben definiert wurden, werden nun `AutoMLConfig` und `AutoMLStep` erstellt. Die Details der Konfiguration hängen von Ihrer Aufgabe ab, wie unter [Konfigurieren automatisierter ML-Experimente in Python](./how-to-configure-auto-train.md) beschrieben. Der folgende Ausschnitt zeigt eine einfache Konfiguration für die Klassifizierungsaufgabe zu den Überlebenden auf der Titanic.

```python
from azureml.train.automl import AutoMLConfig
from azureml.pipeline.steps import AutoMLStep

# Change iterations to a reasonable number (50) to get better accuracy
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 2,
    "experiment_timeout_hours" : 0.25,
    "primary_metric" : 'AUC_weighted'
}

automl_config = AutoMLConfig(task = 'classification',
                             path = '.',
                             debug_log = 'automated_ml_errors.log',
                             compute_target = compute_target,
                             run_configuration = aml_run_config,
                             featurization = 'auto',
                             training_data = prepped_data,
                             label_column_name = 'Survived',
                             **automl_settings)

train_step = AutoMLStep(name='AutoML_Classification',
    automl_config=automl_config,
    outputs=[metrics_data,model_data],
    enable_default_model_output=False,
    enable_default_metrics_output=False,
    allow_reuse=True)
```
Dieser Codeausschnitt zeigt einen häufig mit `AutoMLConfig` verwendeten Ausdruck. Veränderliche Argumente (Hyperparameter) werden in einem separaten Verzeichnis angegeben. Werte, bei denen eine Änderung weniger wahrscheinlich ist, werden direkt im `AutoMLConfig`-Konstruktor angegeben. In diesem Fall wird über `automl_settings` eine kurze Ausführung festgelegt: Die Ausführung wird nach zwei Iterationen bzw. 15 Minuten beendet (je nachdem, welcher Fall zuerst eintritt).

Das `automl_settings`-Verzeichnis wird als kwargs an den `AutoMLConfig`-Konstruktor übergeben. Die übrigen Parameter sind nicht sehr komplex:

- `task` wird für dieses Beispiel auf `classification` festgelegt. Andere gültige Werte sind `regression` und `forecasting`
- `path` und `debug_log` beschreiben den Pfad zum Projekt und zu einer lokalen Datei, in die Debuginformationen geschrieben werden 
- `compute_target` ist das zuvor definierte `compute_target`, bei dem es sich in diesem Beispiel um einen kostengünstigen CPU-basierten Computer handelt. Wenn Sie die Deep Learning-Optionen von AutoML nutzen, sollten Sie das Computeziel in ein GPU-basiertes Ziel ändern
- `featurization` ist auf `auto` festgelegt. Weitere Einzelheiten finden Sie im Abschnitt [Merkmalerstellung für Daten](./how-to-configure-auto-train.md#data-featurization) des Dokuments zur Konfiguration von automatisiertem ML 
- `label_column_name` gibt an, für welche Spalte eine Vorhersage getroffen werden soll 
- `training_data` ist auf die `PipelineOutputTabularDataset`-Objekte festgelegt, die anhand der Ausgabe des Datenaufbereitungsschritts erstellt wurden 

`AutoMLStep` selbst verwendet `AutoMLConfig` und gibt die `PipelineData`-Objekte aus, die für die Metriken und Modelldaten erstellt wurden. 

>[!Important]
> Sie müssen `enable_default_model_output` und `enable_default_metrics_output` nur dann auf `True` festlegen, wenn Sie `AutoMLStepRun` verwenden.

In diesem Beispiel führt der Prozess für automatisiertes ML eine Kreuzvalidierung für die `training_data` durch. Die Anzahl von Kreuzvalidierungen kann über das Argument `n_cross_validations` gesteuert werden. Wenn Sie Ihre Trainingsdaten im Rahmen Ihrer Datenaufbereitungsschritte bereits unterteilt haben, können Sie für `validation_data` ein eigenes `Dataset` festlegen.

Gelegentlich wird `X` für Datenfeatures und `y` für Datenbeschriftungen verwendet. Diese Vorgehensweise ist jedoch veraltet. Verwenden Sie für die Eingabe stattdessen `training_data`. 

## <a name="register-the-model-generated-by-automated-ml"></a>Registrieren des durch automatisiertes ML generierten Modells 

Im letzten Schritt einer einfachen ML-Pipeline wird das erstellte Modell registriert. Wenn das Modell zur Modellregistrierung des Arbeitsbereichs hinzugefügt wird, ist es im Portal verfügbar und kann mit einer Versionsangabe versehen werden. Zum Registrieren des Modells schreiben Sie einen weiteren `PythonScriptStep`, der die `model_data`-Ausgabe von `AutoMLStep` verwendet.

### <a name="write-the-code-to-register-the-model"></a>Schreiben des Codes zum Registrieren des Modells

Ein Modell wird in einem `Workspace` registriert. Wahrscheinlich sind Sie mit der Verwendung von `Workspace.from_config()` zum Anmelden bei Ihrem Arbeitsbereich auf Ihrem lokalen Computer vertraut. Sie können jedoch auch über eine andere Methode aus einer aktiven ML-Pipeline auf den Arbeitsbereich zugreifen. `Run.get_context()` ruft die aktive Ausführung (`Run`) ab. Dieses `run`-Objekt ermöglicht den Zugriff auf eine Vielzahl wichtiger Objekte, u. a. auf den hier verwendeten `Workspace`.

```python
%%writefile register_model.py
from azureml.core.model import Model, Dataset
from azureml.core.run import Run, _OfflineRun
from azureml.core import Workspace
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--model_name", required=True)
parser.add_argument("--model_path", required=True)
args = parser.parse_args()

print(f"model_name : {args.model_name}")
print(f"model_path: {args.model_path}")

run = Run.get_context()
ws = Workspace.from_config() if type(run) == _OfflineRun else run.experiment.workspace

model = Model.register(workspace=ws,
                       model_path=args.model_path,
                       model_name=args.model_name)

print("Registered version {0} of model {1}".format(model.version, model.name))
```

### <a name="write-the-pythonscriptstep-code"></a>Schreiben des PythonScriptStep-Codes

Der zur Modellregistrierung verwendete `PythonScriptStep` verwendet einen `PipelineParameter` für eins seiner Argumente. Pipelineparameter sind Argumente für Pipelines, die problemlos bei der Übermittlung der Ausführung festgelegt werden können. Nachdem sie deklariert wurden, werden sie als normale Argumente übermittelt. 

```python

from azureml.pipeline.core.graph import PipelineParameter

# The model name with which to register the trained model in the workspace.
model_name = PipelineParameter("model_name", default_value="TitanicSurvivalInitial")

register_step = PythonScriptStep(script_name="register_model.py",
                                       name="register_model",
                                       allow_reuse=False,
                                       arguments=["--model_name", model_name, "--model_path", model_data],
                                       inputs=[model_data],
                                       compute_target=compute_target,
                                       runconfig=aml_run_config)
```

## <a name="create-and-run-your-automated-ml-pipeline"></a>Erstellen und Ausführen Ihrer Pipeline für automatisiertes ML

Zum Erstellen und Ausführen einer Pipeline mit einem `AutoMLStep` werden dieselben Schritte ausgeführt wie bei einer normalen Pipeline. 

```python
from azureml.pipeline.core import Pipeline
from azureml.core import Experiment

pipeline = Pipeline(ws, [dataprep_step, train_step, register_step])

experiment = Experiment(workspace=ws, name='titanic_automl')

run = experiment.submit(pipeline, show_output=True)
run.wait_for_completion()
```

Mit dem obigen Code werden die Schritte zur Datenaufbereitung, für automatisiertes ML und zur Modellregistrierung in einem `Pipeline`-Objekt zusammengefasst. Anschließend wird ein `Experiment`-Objekt erstellt. Der `Experiment`-Konstruktor ruft das benannte Experiment ab, sofern es vorhanden ist, oder erstellt es gegebenenfalls neu. Er übergibt die `Pipeline` an das `Experiment`. Dabei wird ein `Run`-Objekt erstellt, das die Pipeline asynchron ausführt. Die `wait_for_completion()`-Funktion wird blockiert, bis die Ausführung abgeschlossen ist.

### <a name="examine-pipeline-results"></a>Untersuchen der Pipelineergebnisse 

Sobald die Ausführung (`run`) abgeschlossen ist, können Sie `PipelineData`-Objekte abrufen, denen ein `pipeline_output_name` zugewiesen wurde. Sie können die Ergebnisse herunterladen und zur weiteren Verarbeitung laden.  

```python
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

Heruntergeladene Dateien werden in das Unterverzeichnis `azureml/{run.id}/` geschrieben. Die Metrikdatei weist das JSON-Format auf und kann zur Untersuchung in einen Pandas-Datenrahmen konvertiert werden.

Für eine lokale Verarbeitung müssen möglicherweise die relevanten Pakete (Pandas, Pickle, das AzureML-SDK usw.) installiert werden. Bei diesem Beispiel ist es wahrscheinlich, dass das beste Modell, das durch automatisiertes ML ermittelt wurde, von XGBoost abhängt.

```bash
!pip install xgboost==0.90
```

```python
import pandas as pd
import json

metrics_filename = metrics_output._path_on_datastore
# metrics_filename = path to downloaded file
with open(metrics_filename) as f:
   metrics_output_result = f.read()
   
deserialized_metrics_output = json.loads(metrics_output_result)
df = pd.DataFrame(deserialized_metrics_output)
df
```

Im obigen Codeausschnitt wird die Metrikdatei aus ihrem Speicherort im Azure-Datenspeicher geladen. Sie können die Daten auch aus der heruntergeladenen Datei laden, wie im Kommentar gezeigt. Nach der Deserialisierung und Konvertierung in einen Pandas-Datenrahmen, können Sie ausführliche Metriken für jede Iteration des Schritts für automatisiertes ML anzeigen.

Die Modelldatei kann in ein `Model`-Objekt deserialisiert werden, das sie für Rückschlüsse, eine weitere Analyse der Metriken usw. nutzen können. 

```python
import pickle

model_filename = model_output._path_on_datastore
# model_filename = path to downloaded file

with open(model_filename, "rb" ) as f:
    best_model = pickle.load(f)

# ... inferencing code not shown ...
```

Weitere Informationen dazu, wie Sie vorhandene Modelle laden und mit diesen Modellen arbeiten, finden Sie unter [Verwenden eines vorhandenen Modells mit Azure Machine Learning](how-to-deploy-existing-model.md).

### <a name="download-the-results-of-an-automated-ml-run"></a>Herunterladen der Ergebnisse einer automatisierten ML-Ausführung

Wenn Sie die Schritte in diesem Artikel ausgeführt haben, verfügen Sie nun über ein instanziiertes `run`-Objekt. Sie können jedoch über ein `Experiment`-Objekt auch abgeschlossene `Run`-Objekte aus dem `Workspace` abrufen.

Der Arbeitsbereich enthält einen vollständigen Datensatz mit all Ihren Experimenten und Ausführungen. Sie können die Ausgabe von Experimenten entweder über das Portal suchen und herunterladen oder diese Schritte mithilfe von Code ausführen. Um auf die Datensätze einer früheren Ausführung zuzugreifen, suchen Sie über Azure Machine Learning nach der ID der Ausführung, für die Sie sich interessieren. Mit dieser ID können Sie über den `Workspace` und das `Experiment` die jeweilige Ausführung (`run`) auswählen.

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['titanic_automl']
run = next(run for run in ex.get_runs() if run.id == run_id)
```

Dabei müssen Sie die Zeichenfolgen im obigen Code in die Details Ihrer vergangenen Ausführung ändern. Beim oben stehenden Codeausschnitt wird davon ausgegangen, dass Sie `ws` dem relevanten `Workspace` mit dem normalen `from_config()`-Element zugewiesen haben. Das gewünschte Experiment wird direkt abgerufen. Anschließend ermittelt der Code das `Run`-Objekt durch einen Abgleich für den `run.id`-Wert.

Sobald ein `Run`-Objekt ermittelt wurde, können Sie die Metriken und das Modell herunterladen. 

```python
automl_run = next(r for r in run.get_children() if r.name == 'AutoML_Classification')
outputs = automl_run.get_outputs()
metrics = outputs['default_metrics_AutoML_Classification']
model = outputs['default_model_AutoML_Classification']

metrics.get_port_data_reference().download('.')
model.get_port_data_reference().download('.')
```

Jedes `Run`-Objekt umfasst `StepRun`-Objekte, die Informationen zur Ausführung des jeweiligen Pipelineschritts enthalten. Im `run`-Objekt wird nach dem `StepRun`-Objekt für `AutoMLStep` gesucht. Die Metriken und das Modell werden anhand ihrer Standardnamen abgerufen. Diese sind selbst dann verfügbar, wenn Sie keine `PipelineData`-Objekte an den `outputs`-Parameter von `AutoMLStep` übergeben. 

Zum Schluss werden die tatsächlichen Metriken und das Modell auf Ihren lokalen Computer heruntergeladen, wie im Abschnitt „Untersuchen der Pipelineergebnisse“ oben erläutert.

## <a name="next-steps"></a>Nächste Schritte

- Führen Sie dieses Jupyter Notebook aus, das ein [vollständiges Beispiel für automatisiertes ML in einer Pipeline](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/nyc-taxi-data-regression-model-building/nyc-taxi-data-regression-model-building.ipynb) zeigt, bei dem Taxitarife mithilfe von Regression vorhergesagt werden
- [Erstellen, Überprüfen und Bereitstellen von automatisierten Machine Learning-Modellen mit Azure Machine Learning](how-to-use-automated-ml-for-ml-models.md)
- Untersuchen Sie eine Vielzahl von [Jupyter Notebooks zur Veranschaulichung von automatisiertem ML](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
- Informieren Sie sich über die Integration Ihrer Pipeline in [End-to-End-MLOps](./concept-model-management-and-deployment.md#automate-the-ml-lifecycle), oder arbeiten Sie mit dem [MLOps-GitHub-Repository](https://github.com/Microsoft/MLOpspython)