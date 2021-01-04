---
title: Datasetversionierung
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre Datasets optimal versionieren und wie das Versionieren mit Machine Learning-Pipelines funktioniert.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.openlocfilehash: 8c9beaca0fb3ee7881559ffcc955f171bc2ddd7b
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886446"
---
# <a name="version-and-track-datasets-in-experiments"></a>Versionieren und Nachverfolgen von Datasets in Experimenten

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-Datasets für die Reproduzierbarkeit versionieren und nachverfolgen. Eine Datasetversionierung ermöglicht es, den Zustand der Daten zu markieren, sodass Sie eine bestimmte Version des Datasets für zukünftige Experimente verwenden können.

Typische Versionierungsszenarios:

* Wenn neue Daten für ein erneutes Training verfügbar sind
* Wenn Sie verschiedene Ansätze für Datenvorbereitung oder Merkmalsentwicklung anwenden

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Installiertes Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). Dieses SDK enthält das Paket [azureml-datasets](/python/api/azureml-core/azureml.core.dataset?preserve-view=true&view=azure-ml-py).
    
- Ein [Azure Machine Learning-Arbeitsbereich](concept-workspace.md). Rufen Sie einen vorhandenen Arbeitsbereich ab, indem Sie den folgenden Code ausführen, oder [erstellen Sie einen neuen Arbeitsbereich](how-to-manage-workspace.md).

    ```Python
    import azureml.core
    from azureml.core import Workspace
    
    ws = Workspace.from_config()
    ```
- Ein [Azure Machine Learning-Dataset](how-to-create-register-datasets.md).

<a name="register"></a>

## <a name="register-and-retrieve-dataset-versions"></a>Registrieren und Abrufen von Datasetversionen

Wenn Sie ein Dataset registrieren, können Sie es versionieren, wiederverwenden und in Experimenten und mit Kollegen teilen. Sie können mehrere Datasets unter dem gleichen Namen registrieren und eine bestimmte Version anhand des Namens und der Versionsnummer abrufen.

### <a name="register-a-dataset-version"></a>Registrieren einer Datasetversion

Der folgende Code registriert eine neue Version des `titanic_ds`-Datasets, indem der `create_new_version`-Parameter auf `True` festgelegt wird. Wenn kein vorhandenes `titanic_ds`-Dataset im Arbeitsbereich registriert ist, erstellt der Code ein neues Dataset mit dem Namen `titanic_ds` und legt dessen Version auf 1 fest.

```Python
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'titanic training data',
                                 create_new_version = True)
```

### <a name="retrieve-a-dataset-by-name"></a>Abrufen eines Datasets anhand des Namens

Die Methode [get_by_name()](/python/api/azureml-core/azureml.core.dataset.dataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-by-name-workspace--name--version--latest--) für die Klasse `Dataset` gibt standardmäßig die neueste Version des im Arbeitsbereich registrierten Datasets zurück. 

Mit dem folgenden Code wird Version 1 des `titanic_ds`-Datasets abgerufen.

```Python
from azureml.core import Dataset
# Get a dataset by name and version number
titanic_ds = Dataset.get_by_name(workspace = workspace,
                                 name = 'titanic_ds', 
                                 version = 1)
```

<a name="best-practice"></a>

## <a name="versioning-best-practice"></a>Best Practices für die Versionierung

Wenn Sie eine Datasetversion erstellen, erstellen Sie *keine* zusätzliche Kopie der Daten im Arbeitsbereich. Da Datasets auf die Daten in Ihrem Speicherdienst verweisen, verfügen Sie über eine einzelne zuverlässige Datenquelle, die von Ihrem Speicherdienst verwaltet wird.

>[!IMPORTANT]
> Wenn die Daten, auf die vom Dataset verwiesen wird, überschrieben oder gelöscht werden, wird die Änderung beim Aufrufen einer bestimmten Version des Datasets *nicht* rückgängig gemacht.

Wenn Sie Daten aus einem Dataset laden, wird immer der aktuelle Dateninhalt geladen, auf den das Dataset verweist. Wenn Sie sicherstellen möchten, dass jede Datasetversion reproduzierbar ist, wird empfohlen, den Dateninhalt, auf den die Datasetversion verweist, nicht zu ändern. Wenn neue Daten eingehen, speichern Sie neue Datendateien in einem separaten Datenordner, und erstellen Sie dann eine neue Datasetversion, um Daten aus diesem neuen Ordner einzubinden.

Die folgende Abbildung und der Beispielcode zeigen die empfohlene Vorgehensweise zum Strukturieren Ihrer Datenordner und zum Erstellen von Datasetversionen, die auf diese Ordner verweisen:

![Ordnerstruktur](./media/how-to-version-track-datasets/folder-image.png)

```Python
from azureml.core import Dataset

# get the default datastore of the workspace
datastore = workspace.get_default_datastore()

# create & register weather_ds version 1 pointing to all files in the folder of week 27
datastore_path1 = [(datastore, 'Weather/week 27')]
dataset1 = Dataset.File.from_files(path=datastore_path1)
dataset1.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27',
                  create_new_version = True)

# create & register weather_ds version 2 pointing to all files in the folder of week 27 and 28
datastore_path2 = [(datastore, 'Weather/week 27'), (datastore, 'Weather/week 28')]
dataset2 = Dataset.File.from_files(path = datastore_path2)
dataset2.register(workspace = workspace,
                  name = 'weather_ds',
                  description = 'weather data in week 27, 28',
                  create_new_version = True)

```

<a name="pipeline"></a>

## <a name="version-an-ml-pipeline-output-dataset"></a>Version eines ML-Pipelineausgabe-Datasets

Sie können ein Dataset als Eingabe und Ausgabe für jeden Schritt der [ML-Pipeline](concept-ml-pipelines.md) verwenden. Wenn Sie Pipelines erneut ausführen, wird die Ausgabe der einzelnen Pipelineschritte als neue Datasetversion registriert.

ML-Pipelines füllen immer dann, wenn die Pipelines neu ausgeführt werden, die Ausgabe jedes Schritts in einen neuen Ordner. Dieses Verhalten ermöglicht, dass die versionsspezifischen Ausgabedatasets reproduzierbar sind. Erfahren Sie mehr über [Datasets in Pipelines](how-to-create-your-first-pipeline.md#steps).

```Python
from azureml.core import Dataset
from azureml.pipeline.steps import PythonScriptStep
from azureml.pipeline.core import Pipeline, PipelineData
from azureml.core. runconfig import CondaDependencies, RunConfiguration

# get input dataset 
input_ds = Dataset.get_by_name(workspace, 'weather_ds')

# register pipeline output as dataset
output_ds = PipelineData('prepared_weather_ds', datastore=datastore).as_dataset()
output_ds = output_ds.register(name='prepared_weather_ds', create_new_version=True)

conda = CondaDependencies.create(
    pip_packages=['azureml-defaults', 'azureml-dataprep[fuse,pandas]'], 
    pin_sdk_version=False)

run_config = RunConfiguration()
run_config.environment.docker.enabled = True
run_config.environment.python.conda_dependencies = conda

# configure pipeline step to use dataset as the input and output
prep_step = PythonScriptStep(script_name="prepare.py",
                             inputs=[input_ds.as_named_input('weather_ds')],
                             outputs=[output_ds],
                             runconfig=run_config,
                             compute_target=compute_target,
                             source_directory=project_folder)
```

<a name="track"></a>

## <a name="track-data-in-your-experiments"></a>Nachverfolgen von Daten in Ihren Experimenten

Azure Machine Learning verfolgt Ihre Daten im gesamten Experiment als Eingabe- und Ausgabedatasets nach.  

Im Folgenden finden Sie Szenarios, in denen Ihre Daten als **Eingabedataset** nachverfolgt werden. 

* Beim Übermitteln der Experimentausführung entweder über den `inputs`- oder `arguments`-Parameter des `ScriptRunConfig`-Objekts als `DatasetConsumptionConfig`-Objekt. 

* Wenn Methoden wie get_by_name() oder get_by_id() im Skript aufgerufen werden. In diesem Szenario wird der Name angezeigt, der dem Dataset bei der Registrierung im Arbeitsbereich zugewiesen wurde. 

Im Folgenden finden Sie Szenarios, in denen Ihre Daten als **Ausgabedataset** nachverfolgt werden.  

* Übergeben Sie ein `OutputFileDatasetConfig`-Objekt entweder über den `outputs`- oder `arguments`-Parameter, wenn eine Experimentausführung gesendet wird. `OutputFileDatasetConfig`-Objekte können auch verwendet werden, um Daten zwischen Pipelineschritten beizubehalten. Weitere Informationen finden Sie unter [Verschieben von Daten in ML-Pipelineschritte und zwischen ML-Pipelineschritten (Python)](how-to-move-data-in-out-of-pipelines.md).
    > [!TIP]
    > [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig?preserve-view=true&view=azure-ml-py) ist eine Klasse in der öffentlichen Vorschau mit [experimentellen](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=truestable-vs-experimental) Vorschaufeatures, die sich jederzeit ändern können.

* Registrieren Sie ein Dataset in Ihrem Skript. In diesem Szenario wird der Name angezeigt, der dem Dataset bei der Registrierung im Arbeitsbereich zugewiesen wurde. Im folgenden Beispiel ist `training_ds` der Name, der angezeigt würde.

    ```Python
   training_ds = unregistered_ds.register(workspace = workspace,
                                     name = 'training_ds',
                                     description = 'training data'
                                     )
    ```

* Übermitteln Sie die untergeordnete Ausführung mit einem nicht registrierten Dataset im Skript. Dies führt zu einem anonymen gespeicherten Dataset.

### <a name="trace-datasets-in-experiment-runs"></a>Ablaufverfolgung von Datasets in Experimentausführungen

Sie können die als Eingabe verwendeten Datasets für jedes Machine Learning-Experiment einfach mit dem `Run`-Objekt des Experiments nachverfolgen.

Im folgenden Code wird die [`get_details()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--)-Methode verwendet, um nachzuverfolgen, welche Eingabedatasets bei der Ausführung des Experiments verwendet wurden:

```Python
# get input datasets
inputs = run.get_details()['inputDatasets']
input_dataset = inputs[0]['dataset']

# list the files referenced by input_dataset
input_dataset.to_path()
```

Die `input_datasets` aus den Experimenten können Sie auch mit [Azure Machine Learning Studio]() suchen. 

Die folgende Abbildung zeigt, wo Sie das Eingabedataset eines Experiments im Azure Machine Learning Studio finden. Wechseln Sie in diesem Beispiel zum Bereich **Experimente**, und öffnen Sie die Registerkarte **Eigenschaften** für eine bestimmte Ausführung Ihres Experiments, `keras-mnist`.

![Eingabedatasets](./media/how-to-version-track-datasets/input-datasets.png)

Verwenden Sie den folgenden Code, um Modelle mit Datasets zu registrieren:

```Python
model = run.register_model(model_name='keras-mlp-mnist',
                           model_path=model_path,
                           datasets =[('training data',train_dataset)])
```

Nach der Registrierung können Sie die Liste der mit dem Dataset registrierten Modelle mit Python oder im [Studio](https://ml.azure.com/) einsehen.

Die folgende Ansicht stammt aus dem Bereich **Datasets** unter **Objekte**. Wählen Sie das Dataset und dann die Registerkarte **Modelle** aus, um eine Liste der Modelle anzuzeigen, die mit dem Dataset registriert sind. 

![Modelle mit dem Eingabedataset](./media/how-to-version-track-datasets/dataset-models.png)

## <a name="next-steps"></a>Nächste Schritte

* [Trainieren mit Datasets](how-to-train-with-datasets.md)
* [Weitere Notebooks für Beispieldatasets](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/)