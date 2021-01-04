---
title: Verschieben von Daten in ML-Pipelines
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning-Pipelines Daten erfassen und wie Sie Daten verwalten und zwischen Pipelineschritten verschieben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 8a5663df590e0f617f8049f0201e6e508351c755
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027576"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Verschieben von Daten in ML-Pipelineschritte und zwischen ML-Pipelineschritten (Python)



Dieser Artikel enthält Code zum Importieren, Transformieren und Verschieben von Daten zwischen Schritten in einer Azure Machine Learning-Pipeline. Eine Übersicht über die Funktionsweise von Daten in Azure Machine Learning finden Sie unter [Zugreifen auf Daten in Azure Storage-Diensten](how-to-access-data.md). Informationen zu den Vorteilen und zur Struktur von Azure Machine Learning-Pipelines finden Sie unter [Beschreibung von Azure Machine Learning-Pipelines](concept-ml-pipelines.md).

In diesem Artikel wird Folgendes gezeigt:

- Verwenden von `Dataset`-Objekten für bereits vorhandene Daten
- Zugreifen auf Daten in Ihren Schritten
- Aufteilen von `Dataset`-Daten in Teilmengen, z. B. Teilmengen für Training und Validierung
- Erstellen von `PipelineData`-Objekten zum Übertragen von Daten in den nächsten Pipelineschritt
- Verwenden von `PipelineData`-Objekten als Eingabe für Pipelineschritte
- Erstellen neuer `Dataset`-Objekte aus `PipelineData`, die Sie beibehalten möchten

> [!TIP]
> Eine verbesserte Oberfläche zum Übergeben von temporären Daten zwischen Pipelineschritten und der Persistenz Ihrer Daten nach Pipelineausführungen steht in den Public Preview-Klassen, [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) und [`OutputTabularDatasetConfig`](/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?preserve-view=true&view=azure-ml-py), zur Verfügung.  Diese Klassen sind [experimentelle](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) Previewfunktionen und können jederzeit geändert werden.


## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes:

- Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.

- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), oder greifen Sie auf [Azure Machine Learning-Studio](https://ml.azure.com/) zu.

- Ein Azure Machine Learning-Arbeitsbereich.
  
  Sie können wahlweise [einen Azure Machine Learning-Arbeitsbereich erstellen](how-to-manage-workspace.md) oder mithilfe des Python SDKs einen vorhandenen verwenden. Importieren Sie die Klasse `Workspace` und `Datastore`, und laden Sie Ihre Abonnementinformationen mit der Funktion `from_config()` aus der Datei `config.json`. Diese Funktion durchsucht standardmäßig das aktuelle Verzeichnis nach der JSON-Datei. Sie können jedoch auch einen Pfadparameter angeben, um mit `from_config(path="your/file/path")` auf die Datei zu verweisen.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Einige bereits vorhandene Daten. In diesem Artikel wird kurz die Verwendung eines [Azure-Blobcontainers](../storage/blobs/storage-blobs-overview.md) erläutert.

- Optional: Eine vorhandene Pipeline für maschinelles Lernen, wie die in [Erstellen und Ausführen von Machine Learning-Pipelines mit dem Azure Machine Learning SDK](how-to-create-your-first-pipeline.md) beschriebene Pipeline.

## <a name="use-dataset-objects-for-pre-existing-data"></a>Verwenden von `Dataset`-Objekten für bereits vorhandene Daten 

Die bevorzugte Methode zur Erfassung von Daten in einer Pipeline ist die Verwendung eines [Dataset](/python/api/azureml-core/azureml.core.dataset%28class%29?preserve-view=true&view=azure-ml-py)-Objekts. `Dataset`-Objekte stellen persistente Daten dar, die im gesamten Arbeitsbereich verfügbar sind.

Es gibt viele Möglichkeiten, `Dataset`-Objekte zu erstellen und zu registrieren. Tabellendatasets sind für durch Trennzeichen getrennte Daten verfügbar, die in einer oder mehreren Dateien enthalten sind. Dateidatasets sind für Binärdaten (z. B. Images) oder für zu analysierende Daten verfügbar. Die einfachsten programmgesteuerten Möglichkeiten zum Erstellen von `Dataset`-Objekte sind die Verwendung vorhandener Blobs im Arbeitsbereichsspeicher oder öffentlicher URLs:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Weitere Optionen zum Erstellen von Datasets mit verschiedenen Optionen und aus unterschiedlichen Quellen, zum Registrieren und Überprüfen der Daten in der Azure Machine Learning-Benutzeroberfläche, zum Verständnis, wie die Datengröße mit der Computekapazität interagiert, und zu deren Versionsverwaltung finden Sie unter [Erstellen von Azure Machine Learning-Datasets](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Übergeben von Datasets an Ihr Skript

Verwenden Sie die `as_named_input()`-Methode des `Dataset`-Objekts, um den Pfad des Datasets an Ihr Skript zu übergeben. Sie können entweder das resultierende `DatasetConsumptionConfig`-Objekt als Argument an Ihr Skript übergeben oder durch Verwendung des `inputs`-Arguments an Ihr Pipelineskript das Dataset mit `Run.get_context().input_datasets[]` abrufen.

Nachdem Sie eine benannte Eingabe erstellt haben, können Sie ihren Zugriffsmodus auswählen: `as_mount()` oder `as_download()`. Wenn Ihr Skript alle Dateien in Ihrem Dataset verarbeitet und der Datenträger auf Ihrer Compute-Ressource ausreichend groß für das Dataset ist, ist der Zugriffsmodus „Herunterladen“ die bessere Wahl. Der Zugriffsmodus „Herunterladen“ vermeidet den Mehraufwand für das Streaming der Daten zur Laufzeit. Wenn Ihr Skript auf eine Teilmenge des Datasets zugreift oder es zu groß für Ihre Berechnung ist, verwenden Sie den Zugriffsmodus „Einbinden“. Weitere Informationen finden Sie unter [Einbinden im Vergleich zum Herunterladen](./how-to-train-with-datasets.md#mount-vs-download)

So übergeben Sie ein Dataset an den Pipelineschritt:

1. Verwenden Sie `TabularDataset.as_named_input()` oder `FileDataset.as_named_input()` (kein „s“ am Ende) zum Erstellen eines `DatasetConsumptionConfig`-Objekts.
1. Verwenden Sie `as_mount()` oder `as_download()`, um den Zugriffsmodus festzulegen.
1. Übergeben Sie die Datasets mithilfe des Arguments `arguments` oder `inputs` an ihre Pipelineschritte.

Der folgende Codeausschnitt zeigt das allgemeine Muster der Kombination dieser Schritte innerhalb des Konstruktors `PythonScriptStep`: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Sie müssen die Werte aller dieser Argumente (d. h. `"train_data"`, `"train.py"`, `cluster` und `iris_dataset`) durch eigene Daten ersetzen. Der obige Ausschnitt zeigt nur die Form des Aufrufs und ist nicht Teil eines Microsoft-Beispiels. 

Sie können auch Methoden wie `random_split()` und `take_sample()` verwenden, um mehrere Eingaben zu erstellen oder die Menge der an Ihren Pipelineschritt übergebenen Daten zu reduzieren:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Zugreifen auf Datasets in Ihrem Skript

Benannte Eingaben für Ihr Pipelineschrittskript sind als Wörterbuch innerhalb des `Run`-Objekts verfügbar. Rufen Sie das aktive `Run`-Objekt mit `Run.get_context()` und anschließend das Wörterbuch der benannten Eingaben mit `input_datasets` ab. Wenn Sie das `DatasetConsumptionConfig`-Objekt mit dem `arguments`-Argument statt mit dem `inputs`-Argument übergeben haben, greifen Sie auf die Daten mit `ArgParser`-Code zu. Beide Verfahren werden im folgenden Codeausschnitt veranschaulicht.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Der übergebene Wert ist der Pfad zu der/den Datasetdatei(en).

Es ist auch möglich, direkt auf ein registriertes `Dataset` zuzugreifen. Da registrierte Datasets persistent sind und in einem Arbeitsbereich freigegeben werden, können Sie sie direkt abrufen:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Die vorherigen Ausschnitte zeigen nur die Form der Aufrufe und sind nicht Teil eines Microsoft-Beispiels. Sie müssen die verschiedenen Argumente durch Werte aus Ihrem eigenen Projekt ersetzen.

## <a name="use-pipelinedata-for-intermediate-data"></a>Verwenden von `PipelineData` für Zwischendaten

Während `Dataset`-Objekte persistente Daten darstellen, werden [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py)-Objekte für temporäre Daten verwendet, die von Pipelineschritten ausgegeben werden. Da die Lebensdauer eines `PipelineData`-Objekts länger als ein einzelner Pipelineschritt ist, definieren Sie sie im Skript für die Pipelinedefinition. Wenn Sie ein `PipelineData`-Objekt erstellen, müssen Sie einen Namen und einen Datenspeicher angeben, in dem sich die Daten befinden werden. Übergeben Sie Ihr(e) `PipelineData`-Objekt(e) an Ihr `PythonScriptStep` unter Verwendung von _beiden_ Argumenten, `arguments` und `outputs`:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

Sie können Ihr `PipelineData`-Objekt mit einem Zugriffsmodus erstellen, der ein sofortiges Hochladen ermöglicht. Wenn Sie in diesem Fall Ihr `PipelineData` erstellen, legen Sie `upload_mode` auf `"upload"` fest, und verwenden Sie das Argument `output_path_on_compute`, um den Pfad anzugeben, in den Sie die Daten schreiben werden:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!NOTE]
> Die vorherigen Ausschnitte zeigen nur die Form der Aufrufe und sind nicht Teil eines Microsoft-Beispiels. Sie müssen die verschiedenen Argumente durch Werte aus Ihrem eigenen Projekt ersetzen.

> [!TIP]
> Eine verbesserte Oberfläche zum Übergeben von Zwischendaten zwischen Pipelineschritten steht in der Public Preview-Klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) zur Verfügung. Ein Codebeispiel der Verwendung von `OutputFileDatasetConfig` finden Sie in der Schrittanleitung [Erstellen einer ML Pipeline mit zwei Schritten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Verwenden von `PipelineData` als Ausgaben eines Trainingsschritts
Innerhalb der `PythonScriptStep` Ihrer Pipeline können Sie die verfügbaren Ausgabepfade mit den Argumenten des Programms abrufen. Wenn dieser Schritt der erste ist und die Ausgabedaten initialisieren soll, müssen Sie das Verzeichnis unter dem angegebenen Pfad erstellen. Sie können dann die Dateien schreiben, die Sie in `PipelineData` aufnehmen möchten.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Wenn Sie Ihr `PipelineData` mit dem `is_directory`-Argument auf `True` festgelegt haben, würde es ausreichen, einfach den `os.makedirs()`-Aufruf auszuführen, und Sie könnten dann beliebige Dateien in den Pfad schreiben. Weitere Informationen finden Sie in der [PipelineData](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?preserve-view=true&view=azure-ml-py)-Referenzdokumentation.


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Lesen von `PipelineData` als Eingaben für nachfolgende Schritte

Nachdem der anfängliche Pipelineschritt einige Daten in den `PipelineData`-Pfad geschrieben hat und sie als Ausgabe dieses anfänglichen Schritts dienen, können die Daten als Eingabe für einen späteren Schritt verwendet werden:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Der Wert einer `PipelineData`-Eingabe ist der Pfad zur vorherigen Ausgabe. 

> [!NOTE]
> Die vorherigen Ausschnitte zeigen nur die Form der Aufrufe und sind nicht Teil eines Microsoft-Beispiels. Sie müssen die verschiedenen Argumente durch Werte aus Ihrem eigenen Projekt ersetzen.

> [!TIP]
> Eine verbesserte Oberfläche zum Übergeben von Zwischendaten zwischen Pipelineschritten steht in der Public Preview-Klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) zur Verfügung. Ein Codebeispiel der Verwendung von `OutputFileDatasetConfig` finden Sie in der Schrittanleitung [Erstellen einer ML Pipeline mit zwei Schritten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

Wenn, wie zuvor gezeigt, im ersten Schritt eine einzelne Datei geschrieben wurde, könnte ihre Verwendung wie folgt aussehen: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Konvertieren von `PipelineData`-Objekten in `Dataset`s

Wenn Sie Ihre `PipelineData` länger als die Dauer einer Ausführung zur Verfügung stellen möchten, verwenden Sie dessen `as_dataset()`-Funktion, um es in ein `Dataset` zu konvertieren. Sie können das `Dataset` dann registrieren, was es zu einem wichtigen Aspekt in Ihrem Arbeitsbereich macht. Da Ihr `PipelineData`-Objekt jedes Mal, wenn die Pipeline ausgeführt wird, einen anderen Pfad hat, wird dringend empfohlen, dass Sie `create_new_version` auf `True` festlegen, wenn Sie ein aus einem `PipelineData`-Objekt erstelltes `Dataset` registrieren.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Mit der Public Preview-Klasse [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) ist eine verbesserte Oberfläche für das Beibehalten Ihrer Zwischendaten außerhalb Ihrer Pipelineausführungen verfügbar. Ein Codebeispiel der Verwendung von `OutputFileDatasetConfig` finden Sie in der Schrittanleitung [Erstellen einer ML Pipeline mit zwei Schritten](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Azure Machine Learning-Datasets](how-to-create-register-datasets.md)
* [Erstellen und Ausführen von Machine Learning-Pipelines mit dem Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)