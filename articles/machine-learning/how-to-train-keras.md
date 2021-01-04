---
title: Trainieren von Deep Learning-Keras-Modellen
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie ein in TensorFlow ausgeführtes Keras-Deep Neural Network-Klassifizierungsmodell mit Azure Machine Learning trainieren und registrieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.reviewer: peterlu
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a7d55c6e550000d2dd6c2930d95086ec433c246b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93361096"
---
# <a name="train-keras-models-at-scale-with-azure-machine-learning"></a>Bedarfsgerechtes Trainieren von Keras-Modellen mit Azure Machine Learning

In diesem Artikel erfahren Sie, wie Keras-Trainingsskripts mit Azure Machine Learning ausführen.

Der Beispielcode in diesem Artikel zeigt Ihnen, wie Sie ein Keras-Klassifizierungsmodell trainieren und registrieren, das mit dem TensorFlow-Back-End mit Azure Machine Learning erstellt wurde. Es verwendet das beliebte [MNIST-Dataset](http://yann.lecun.com/exdb/mnist/), um handschriftliche Ziffern mithilfe eines DNN (Deep Neural Network) zu klassifizieren, das mit der [Keras Python-Bibliothek](https://keras.io) erstellt wurde, die auf [TensorFlow](https://www.tensorflow.org/overview) ausgeführt wird.

Keras ist eine allgemeine API für neuronale Netzwerke, die zur Vereinfachung der Entwicklung auf anderen beliebten DNN-Frameworks ausgeführt werden kann. Azure Machine Learning bietet die Möglichkeit, Trainingsaufträge mithilfe elastischer Cloudcomputeressourcen schnell horizontal zu skalieren. Sie können auch Ihre Trainingsläufe, Versions- und Bereitstellungsmodelle und vieles mehr nachverfolgen.

Egal, ob Sie ein Keras-Modell von Grund auf entwickeln oder ein bestehendes Modell in die Cloud bringen, Azure Machine Learning kann Ihnen helfen, produktionsreife Modelle zu erstellen.

> [!NOTE]
> Wenn Sie die in TensorFlow integrierte Keras-API **tf.keras** und nicht das eigenständige Keras-Paket verwenden, lesen Sie stattdessen [Trainieren von TensorFlow-Modellen](how-to-train-tensorflow.md).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie diesen Code in einer dieser Umgebungen aus:

- Azure Machine Learning-Compute-Instanz: keine Downloads oder Installationen erforderlich

     - Absolvieren Sie [Tutorial: Einrichten von Umgebung und Arbeitsbereich](tutorial-1st-experiment-sdk-setup.md), um einen dedizierten Notebookserver zu erstellen, auf dem das SDK und Beispielrepository vorinstalliert sind.
    - Suchen Sie im Beispielordner auf dem Notebookserver ein fertiges und erweitertes Notebook. Dazu navigieren Sie zum folgenden Verzeichnis: **how-to-use-azureml > ml-frameworks > keras > train-hyperparameter-tune-deploy-with-keras**.

 - Ihr eigener Jupyter Notebook-Server

    - [Installieren des Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (1.15.0 oder höher).
    - [Erstellen Sie eine Konfigurationsdatei für den Arbeitsbereich.](how-to-configure-environment.md#workspace)
    - [Laden Sie die Beispielskriptdateien](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras) `keras_mnist.py` und `utils.py` herunter.

    Auf der GitHub-Seite mit Beispielen finden Sie außerdem eine fertige [Jupyter Notebook-Version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/keras/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-keras.ipynb) dieser Anleitung. Das Notebook umfasst erweiterte Abschnitte, in denen die intelligente Hyperparameteroptimierung, die Modellimplementierung und Notebook-Widgets behandelt werden.

## <a name="set-up-the-experiment"></a>Einrichten des Experiments

In diesem Abschnitt wird das Trainingsexperiment eingerichtet, indem die erforderlichen Python-Pakete geladen, ein Arbeitsbereich initialisiert, das FileDataset für die Eingabetrainingsdaten erstellt, das Computeziel erstellt und die Trainingsumgebung definiert wird.

### <a name="import-packages"></a>Importieren von Paketen

Importieren Sie zunächst die erforderlichen Python-Bibliotheken.

```Python
import os
import azureml
from azureml.core import Experiment
from azureml.core import Environment
from azureml.core import Workspace, Run
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>Initialisieren eines Arbeitsbereichs

Der [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) ist die Ressource der obersten Ebene für den Dienst. Er stellt den zentralen Ort für die Arbeit mit allen erstellten Artefakten dar. Im Python SDK können Sie auf die Arbeitsbereichsartefakte zugreifen, indem Sie ein [`workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)-Objekt erstellen.

Erstellen Sie ein Arbeitsbereichsobjekt aus der Datei `config.json`, die im [Abschnitt „Voraussetzungen“](#prerequisites) erstellt wurde.

```Python
ws = Workspace.from_config()
```

### <a name="create-a-file-dataset"></a>Erstellen eines Dateidatasets (FileDataset)

Ein `FileDataset`-Objekt verweist auf Dateien in Ihrem Arbeitsbereichsdatenspeicher oder unter öffentlichen URLs. Die Dateien können ein beliebiges Format haben, und die Klasse ermöglicht es Ihnen, die Dateien in Ihre Computeinstanz herunterzuladen oder einzubinden. Durch Erstellen eines `FileDataset`-Objekts erstellen Sie einen Verweis auf den Speicherort der Datenquelle. Wenn Sie Transformationen auf das Dataset angewendet haben, werden diese ebenfalls im Dataset gespeichert. Die Daten verbleiben an ihrem Speicherort, sodass keine zusätzlichen Speicherkosten anfallen. Weitere Informationen finden Sie in der [Beschreibung](./how-to-create-register-datasets.md) des `Dataset`-Pakets.

```python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
dataset = Dataset.File.from_files(path=web_paths)
```

Sie können die `register()`-Methode verwenden, um das Dataset in Ihrem Arbeitsbereich zu registrieren, damit es für andere Benutzer freigegeben und für unterschiedliche Experimente wiederverwendet werden kann, und damit in Ihrem Trainingsskript über den Namen auf es verwiesen werden kann.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)
```

### <a name="create-a-compute-target"></a>Erstellen eines Computeziels

Erstellen Sie ein Computeziel, auf dem der Trainingsauftrag ausgeführt werden soll. In diesem Beispiel erstellen Sie einen GPU-fähigen Azure Machine Learning-Computercluster.

```Python
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

Weitere Informationen zu Computezielen finden Sie im Artikel [Was ist ein Computeziel?](concept-compute-target.md).

### <a name="define-your-environment"></a>Definieren der Umgebung

Definieren Sie die Azure ML-[Umgebung](concept-environments.md), die die Abhängigkeiten Ihres Trainingsskripts kapselt.

Definieren Sie zunächst Ihre Conda-Abhängigkeiten in einer YAML-Datei. In diesem Beispiel trägt die Datei den Namen `conda_dependencies.yml`.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.0.0
  - keras<=2.3.1
  - matplotlib
```

Erstellen Sie anhand dieser Spezifikation der Conda-Umgebung eine Azure ML-Umgebung. Die Umgebung wird zur Laufzeit in einen Docker-Container gepackt.

Wenn kein Basisimage angegeben wird, verwendet Azure ML standardmäßig ein CPU-Image `azureml.core.environment.DEFAULT_CPU_IMAGE` als Basisimage. Da das Training in diesem Beispiel auf einem GPU-Cluster ausgeführt wird, müssen Sie ein GPU-Basisimage mit den erforderlichen GPU-Treibern und -Abhängigkeiten angeben. Azure ML verwaltet eine Reihe von Basisimages, die für Microsoft Container Registry (MCR) veröffentlicht werden, die Sie verwenden können. Weitere Informationen finden Sie im GitHub-Repository [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers).

```python
keras_env = Environment.from_conda_specification(name='keras-env', file_path='conda_dependencies.yml')

# Specify a GPU base image
keras_env.docker.enabled = True
keras_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.0-cudnn7-ubuntu18.04'
```

Weitere Informationen zum Erstellen und Verwenden von Umgebungen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurieren und Übermitteln Ihrer Trainingsausführung

### <a name="create-a-scriptrunconfig"></a>Erstellen eines ScriptRunConfig-Elements
Verwenden Sie zunächst die `Dataset`-Klasse, um die Daten aus dem Arbeitsbereichsdatenspeicher abzurufen.

```python
dataset = Dataset.get_by_name(ws, 'mnist-dataset')

# list the files referenced by mnist-dataset
dataset.to_path()
```

Erstellen Sie ein ScriptRunConfig- Objekt, um die Konfigurationsdetails Ihres Trainingsauftrags anzugeben, einschließlich Ihres Trainingsskripts, der zu verwendenden Umgebung und des Computeziels für die Ausführung.

Alle Argumente des Trainingsskripts werden über die Befehlszeile übergeben, wenn sie im `arguments` Parameter angegeben sind. DatasetConsumptionConfig für unser FileDataset wird als Argument an das Trainingsskript für das `--data-folder`-Argument übergeben. Azure ML löst diese DatasetConsumptionConfig in den Bereitstellungspunkt des unterstützenden Datenspeichers auf, auf den dann vom Trainingsskript aus zugegriffen werden kann.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 50,
        '--first-layer-neurons', 300,
        '--second-layer-neurons', 100,
        '--learning-rate', 0.001]

src = ScriptRunConfig(source_directory=script_folder,
                      script='keras_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=keras_env)
```

Weitere Informationen zum Konfigurieren von Aufträgen mit ScriptRunConfig finden Sie unter [Konfigurieren und Übermitteln von Trainingsausführungen](how-to-set-up-training-targets.md).

> [!WARNING]
> Wenn Sie zuvor den TensorFlow-Schätzer zum Konfigurieren ihrer Keras-Trainingsaufträge verwendet haben, beachten Sie, dass Schätzer in einer zukünftigen Version des Azure ML SDK als veraltet eingestuft werden. Mit dem Azure ML SDK 1.15.0 oder höher ist ScriptRunConfig die empfohlene Vorgehensweise zum Konfigurieren von Trainingsaufträgen, einschließlich derjenigen, die DL-Frameworks verwenden.

### <a name="submit-your-run"></a>Übermitteln Ihrer Ausführung

Das [Run-Objekt](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) bildet die Schnittstelle zum Ausführungsverlauf, während der Auftrag ausgeführt wird und nachdem er abgeschlossen wurde.

```Python
run = Experiment(workspace=ws, name='keras-mnist').submit(src)
run.wait_for_completion(show_output=True)
```

### <a name="what-happens-during-run-execution"></a>Was passiert während der Ausführung
Die Ausführung durchläuft die folgenden Phasen:

- **Vorbereitung** : Ein Docker-Image wird entsprechend der definierten Umgebung erstellt. Das Image wird in die Containerregistrierung des Arbeitsbereichs hochgeladen und für spätere Ausführungen zwischengespeichert. Darüber hinaus werden Protokolle in den Ausführungsverlauf gestreamt, mit deren Hilfe der Status überwacht werden kann. Wenn stattdessen eine zusammengestellte Umgebung angegeben wird, wird das zwischengespeicherte Image verwendet, das diese zusammengestellte Umgebung unterstützt.

- **Skalierung** : Der Cluster versucht ein Hochskalieren, wenn der Batch KI-Cluster mehr Knoten zur Ausführung benötigt, als derzeit verfügbar sind.

- **Wird ausgeführt:** Alle Skripts im Skriptordner werden auf das Computeziel hochgeladen, Datenspeicher werden bereitgestellt oder kopiert, und `script` wird ausgeführt. Ausgaben aus „stdout“ und dem Ordner **./logs** werden in den Ausführungsverlauf gestreamt und können zur Überwachung der Ausführung verwendet werden.

- **Nachbearbeitung** : Der Ordner **./outputs** der Ausführung wird in den Ausführungsverlauf kopiert.

## <a name="register-the-model"></a>Registrieren des Modells

Sobald Sie das Modell trainiert haben, können Sie es in Ihrem Arbeitsbereich registrieren. Die Modellregistrierung bietet die Möglichkeit, Ihre Modelle in Ihrem Arbeitsbereich zu speichern und zu versionieren, um die [Modellverwaltung und -bereitstellung](concept-model-management-and-deployment.md) zu vereinfachen.

```Python
model = run.register_model(model_name='keras-mnist', model_path='outputs/model')
```

> [!TIP]
> Die Schrittanleitung zur Bereitstellung enthält einen Abschnitt zur Registrierung von Modellen, aber Sie können direkt zu [Erstellen eines Computeziels](how-to-deploy-and-where.md#choose-a-compute-target) für die Bereitstellung springen, da Sie bereits über ein registriertes Modell verfügen.

Sie können auch eine lokale Kopie des Modells herunterladen. Dies kann nützlich sein, um zusätzliche Arbeiten zur Modellüberprüfung lokal durchzuführen. Im Trainingsskript `keras_mnist.py` wird das Modell durch ein saver-Objekt von TensorFlow persistent in einem lokalen Ordner (lokal für das Computeziel) gespeichert. Mit dem Run-Objekt können Sie eine Kopie aus dem Ausführungsverlauf herunterladen.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)

for f in run.get_file_names():
    if f.startswith('outputs/model'):
        output_file_path = os.path.join('./model', f.split('/')[-1])
        print('Downloading from {} to {} ...'.format(f, output_file_path))
        run.download_file(name=f, output_file_path=output_file_path)
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein Keras-Modell in Azure Machine Learning trainiert und registriert. Um zu erfahren, wie Sie ein Modell bereitstellen, fahren Sie mit unserem Artikel zur Modellbereitstellung fort.

* [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md)
* [Erfassen einer Ausführungsmetrik während des Trainings](how-to-track-experiments.md)
* [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
* [Bereitstellen eines trainierten Modells](how-to-deploy-and-where.md)
* [Verteiltes Trainieren von Deep Learning-Modellen in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
