---
title: Trainieren und Bereitstellen eines TensorFlow-Modells
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning es Ihnen ermöglicht, einen TensorFlow-Trainingsauftrag mithilfe elastischer Cloudcomputeressourcen aufzuskalieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: minxia
author: mx-iao
ms.date: 09/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 9b8d48139e6cbabfbc5bf63f85d2d03c64d7efd9
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542285"
---
# <a name="train-tensorflow-models-at-scale-with-azure-machine-learning"></a>Bedarfsorientiertes Trainieren von TensorFlow-Modellen mit Azure Machine Learning

In diesem Artikel erfahren Sie, wie Sie Ihre [TensorFlow](https://www.tensorflow.org/overview)-Trainingsskripts mithilfe von Azure Machine Learning bedarfsorientiert ausführen.

In diesem Beispiel wird ein TensorFlow-Modell trainiert und registriert, um handschriftliche Ziffern mithilfe eines Deep Neural Network (DNN) zu klassifizieren.

Unabhängig davon, ob Sie ein TensorFlow-Modell von Grund auf entwickeln oder ob Sie ein [vorhandenes Modell](how-to-deploy-existing-model.md) in die Cloud bringen, können Sie Azure Machine Learning zum Aufskalieren von Open-Source-Trainingsaufträgen verwenden, um für die Produktion geeignete Modelle zu erstellen, bereitzustellen und zu überwachen sowie ihre Versionen zu verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie diesen Code in einer dieser Umgebungen aus:

 - Azure Machine Learning-Compute-Instanz: keine Downloads oder Installationen erforderlich

     - Absolvieren Sie [Tutorial: Einrichten von Umgebung und Arbeitsbereich](tutorial-1st-experiment-sdk-setup.md), um einen dedizierten Notebookserver zu erstellen, auf dem das SDK und Beispielrepository vorinstalliert sind.
    - Suchen Sie im Deep Learning-Beispielordner auf dem Notebook-Server ein fertiges und erweitertes Notebook. Dazu navigieren Sie zum folgenden Verzeichnis: **how-to-use-azureml > ml-frameworks > tensorflow > train-hyperparameter-tune-deploy-with-tensorflow**. 
 
 - Ihr eigener Jupyter Notebook-Server

    - [Installieren des Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (1.15.0 oder höher).
    - [Erstellen Sie eine Konfigurationsdatei für den Arbeitsbereich.](how-to-configure-environment.md#workspace)
    - [Laden Sie die Beispielskriptdateien](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow) `tf_mnist.py` und `utils.py` herunter.
     
    Auf der GitHub-Seite mit Beispielen finden Sie außerdem eine fertige [Jupyter Notebook-Version](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-hyperparameter-tune-deploy-with-tensorflow/train-hyperparameter-tune-deploy-with-tensorflow.ipynb) dieser Anleitung. Das Notebook umfasst erweiterte Abschnitte, in denen die intelligente Hyperparameteroptimierung, die Modellimplementierung und Notebook-Widgets behandelt werden.

## <a name="set-up-the-experiment"></a>Einrichten des Experiments

In diesem Abschnitt wird das Trainingsexperiment eingerichtet, indem die erforderlichen Python-Pakete geladen, ein Arbeitsbereich initialisiert, das Computeziel erstellt und die Trainingsumgebung definiert wird.

### <a name="import-packages"></a>Importieren von Paketen

Importieren Sie zunächst die erforderlichen Python-Bibliotheken.

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run
from azureml.core import Environment

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

Verwenden Sie die `register()`-Methode, um das Dataset in Ihrem Arbeitsbereich zu registrieren, damit es für andere Benutzer freigegeben und für unterschiedliche Experimente wiederverwendet werden kann, und damit in Ihrem Trainingsskript über den Namen auf es verwiesen werden kann.

```python
dataset = dataset.register(workspace=ws,
                           name='mnist-dataset',
                           description='training and test dataset',
                           create_new_version=True)

# list the files referenced by dataset
dataset.to_path()
```

### <a name="create-a-compute-target"></a>Erstellen eines Computeziels

Erstellen Sie ein Computeziel, auf dem der TensorFlow-Auftrag ausgeführt werden soll. In diesem Beispiel erstellen Sie einen GPU-fähigen Azure Machine Learning-Computercluster.

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

Um die Azure ML-[Umgebung](concept-environments.md) zu definieren, die die Abhängigkeiten Ihres Trainingsskripts kapselt, können Sie entweder eine benutzerdefinierte Umgebung definieren oder eine von Azure ML zusammengestellte Umgebung verwenden.

#### <a name="use-a-curated-environment"></a>Verwenden einer zusammengestellten Umgebung
Azure ML bietet vorgefertigte, zusammengestellte Umgebungen, falls Sie keine eigene Umgebung definieren möchten. Azure ML verfügt über mehrere unterschiedliche CPU- und GPU-Umgebungen für TensorFlow, die verschiedenen Versionen von TensorFlow entsprechen. Weitere Informationen finden Sie [hier](resource-curated-environments.md).

Wenn Sie eine zusammengestellte Umgebung verwenden möchten, können Sie stattdessen den folgenden Befehl ausführen:

```python
curated_env_name = 'AzureML-TensorFlow-2.2-GPU'
tf_env = Environment.get(workspace=ws, name=curated_env_name)
```

Um die Pakete anzuzeigen, die in der zusammengestellten Umgebung enthalten sind, können Sie die Conda-Abhängigkeiten auf den Datenträger schreiben:
```python
tf_env.save_to_directory(path=curated_env_name)
```

Stellen Sie sicher, dass die zusammengestellte Umgebung alle Abhängigkeiten enthält, die von Ihrem Trainingsskript benötigt werden. Wenn dies nicht der Fall ist, müssen Sie die Umgebung so ändern, dass die fehlenden Abhängigkeiten eingeschlossen werden. Beachten Sie Folgendes: Wenn die Umgebung geändert wird, müssen Sie ihr einen neuen Namen geben, da das Präfix „AzureML“ für zusammengestellte Umgebungen reserviert ist. Wenn Sie die YAML-Datei für Conda-Abhängigkeiten geändert haben, können Sie daraus eine neue Umgebung mit einem neuen Namen erstellen, z. B.:
```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')
```

Wenn Sie das Objekt der zusammengestellten Umgebung stattdessen direkt geändert haben, können Sie diese Umgebung mit einem neuen Namen klonen:
```python
tf_env = tf_env.clone(new_name='tensorflow-2.2-gpu')
```

#### <a name="create-a-custom-environment"></a>Erstellen einer benutzerdefinierten Umgebung

Sie können auch eine eigene Azure ML-Umgebung erstellen, die die Abhängigkeiten Ihres Trainingsskripts kapselt.

Definieren Sie zunächst Ihre Conda-Abhängigkeiten in einer YAML-Datei. In diesem Beispiel trägt die Datei den Namen `conda_dependencies.yml`.

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
```

Erstellen Sie anhand dieser Spezifikation der Conda-Umgebung eine Azure ML-Umgebung. Die Umgebung wird zur Laufzeit in einen Docker-Container gepackt.

Wenn kein Basisimage angegeben wird, verwendet Azure ML standardmäßig ein CPU-Image `azureml.core.environment.DEFAULT_CPU_IMAGE` als Basisimage. Da das Training in diesem Beispiel auf einem GPU-Cluster ausgeführt wird, müssen Sie ein GPU-Basisimage mit den erforderlichen GPU-Treibern und -Abhängigkeiten angeben. Azure ML verwaltet eine Reihe von Basisimages, die für Microsoft Container Registry (MCR) veröffentlicht werden, die Sie verwenden können. Weitere Informationen finden Sie im GitHub-Repository [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers).

```python
tf_env = Environment.from_conda_specification(name='tensorflow-2.2-gpu', file_path='./conda_dependencies.yml')

# Specify a GPU base image
tf_env.docker.enabled = True
tf_env.docker.base_image = 'mcr.microsoft.com/azureml/openmpi3.1.2-cuda10.1-cudnn7-ubuntu18.04'
```

> [!TIP]
> Optional können Sie einfach alle ihre Abhängigkeiten direkt in einem benutzerdefinierten Docker-Image oder in einer Dockerfile-Datei erfassen und Ihre Umgebung daraus erstellen. Weitere Informationen finden Sie unter [Trainieren mit einem benutzerdefinierten Image](how-to-train-with-custom-image.md).

Weitere Informationen zum Erstellen und Verwenden von Umgebungen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md).

## <a name="configure-and-submit-your-training-run"></a>Konfigurieren und Übermitteln Ihrer Trainingsausführung

### <a name="create-a-scriptrunconfig"></a>Erstellen eines ScriptRunConfig-Elements

Erstellen Sie ein [ScriptRunConfig-](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) Objekt, um die Konfigurationsdetails Ihres Trainingsauftrags anzugeben, einschließlich Ihres Trainingsskripts, der zu verwendenden Umgebung und des Computeziels für die Ausführung. Alle Argumente des Trainingsskripts werden über die Befehlszeile übergeben, wenn sie im `arguments` Parameter angegeben sind.

```python
from azureml.core import ScriptRunConfig

args = ['--data-folder', dataset.as_mount(),
        '--batch-size', 64,
        '--first-layer-neurons', 256,
        '--second-layer-neurons', 128,
        '--learning-rate', 0.01]

src = ScriptRunConfig(source_directory=script_folder,
                      script='tf_mnist.py',
                      arguments=args,
                      compute_target=compute_target,
                      environment=tf_env)
```

> [!WARNING]
> Zum Ausführen von Trainingsskripts wird von Azure Machine Learning das gesamte Quellverzeichnis kopiert. Sind vertrauliche Daten vorhanden, die nicht hochgeladen werden sollen, verwenden Sie die [IGNORE-Datei](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots), oder platzieren Sie sie nicht im Quellverzeichnis. Greifen Sie stattdessen mit einem Azure ML-[Dataset](how-to-train-with-datasets.md) auf Ihre Daten zu.

Weitere Informationen zum Konfigurieren von Aufträgen mit ScriptRunConfig finden Sie unter [Konfigurieren und Übermitteln von Trainingsausführungen](how-to-set-up-training-targets.md).

> [!WARNING]
> Wenn Sie zuvor den TensorFlow-Schätzer zum Konfigurieren ihrer TensorFlow-Trainingsaufträge verwendet haben, beachten Sie, dass Schätzer in einer zukünftigen Version des Azure ML SDK als veraltet eingestuft werden. Mit dem Azure ML SDK 1.15.0 oder höher ist ScriptRunConfig die empfohlene Vorgehensweise zum Konfigurieren von Trainingsaufträgen, einschließlich derjenigen, die DL-Frameworks verwenden.

### <a name="submit-a-run"></a>Initiieren einer Ausführung

Das [Run-Objekt](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) bildet die Schnittstelle zum Ausführungsverlauf, während der Auftrag ausgeführt wird und nachdem er abgeschlossen wurde.

```Python
run = Experiment(workspace=ws, name='tf-mnist').submit(src)
run.wait_for_completion(show_output=True)
```
### <a name="what-happens-during-run-execution"></a>Was passiert während der Ausführung
Die Ausführung durchläuft die folgenden Phasen:

- **Vorbereitung**: Ein Docker-Image wird entsprechend der definierten Umgebung erstellt. Das Image wird in die Containerregistrierung des Arbeitsbereichs hochgeladen und für spätere Ausführungen zwischengespeichert. Darüber hinaus werden Protokolle in den Ausführungsverlauf gestreamt, mit deren Hilfe der Status überwacht werden kann. Wenn stattdessen eine zusammengestellte Umgebung angegeben wird, wird das zwischengespeicherte Image verwendet, das diese zusammengestellte Umgebung unterstützt.

- **Skalierung**: Der Cluster versucht ein Hochskalieren, wenn der Batch KI-Cluster mehr Knoten zur Ausführung benötigt, als derzeit verfügbar sind.

- **Wird ausgeführt:** Alle Skripts im Skriptordner werden auf das Computeziel hochgeladen, Datenspeicher werden bereitgestellt oder kopiert, und das `script` wird ausgeführt. Ausgaben aus „stdout“ und dem Ordner **./logs** werden in den Ausführungsverlauf gestreamt und können zur Überwachung der Ausführung verwendet werden.

- **Nachbearbeitung**: Der Ordner **./outputs** der Ausführung wird in den Ausführungsverlauf kopiert.

## <a name="register-or-download-a-model"></a>Registrieren oder Herunterladen eines Modells

Sobald Sie das Modell trainiert haben, können Sie es in Ihrem Arbeitsbereich registrieren. Die Modellregistrierung bietet die Möglichkeit, Ihre Modelle in Ihrem Arbeitsbereich zu speichern und zu versionieren, um die [Modellverwaltung und -bereitstellung](concept-model-management-and-deployment.md) zu vereinfachen. Optional: Durch die Angabe der Parameter `model_framework`, `model_framework_version` und `resource_configuration` wird die Modellimplementierung ohne Code verfügbar. Auf diese Weise können Sie Ihr Modell direkt als Webdienst aus dem registrierten Modell bereitstellen, und das Objekt `ResourceConfiguration` definiert die Computeressource für den Webdienst.

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='tf-mnist', 
                           model_path='outputs/model',
                           model_framework=Model.Framework.TENSORFLOW,
                           model_framework_version='2.0',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

Mit dem Run-Objekt können Sie auch eine lokale Kopie des Modells herunterladen. Im Trainingsskript `tf_mnist.py` wird das Modell durch ein saver-Objekt von TensorFlow persistent in einem lokalen Ordner (lokal für das Computeziel) gespeichert. Sie können das Run-Objekt verwenden, um eine Kopie herunterzuladen.

```Python
# Create a model folder in the current directory
os.makedirs('./model', exist_ok=True)
run.download_files(prefix='outputs/model', output_directory='./model', append_prefix=False)
```

## <a name="distributed-training"></a>Verteiltes Training

Azure Machine Learning unterstützt auch verteilte TensorFlow-Aufträge auf mehreren Knoten, sodass Sie Ihre Trainingworkloads skalieren können. Sie können ganz einfach verteilte TensorFlow-Aufträge ausführen. Azure ML verwaltet die Orchestrierung für Sie.

Azure ML unterstützt die Ausführung verteilter TensorFlow-Aufträge sowohl mit Horovod als auch mit der integrierten API für verteiltes Training von TensorFlow.

### <a name="horovod"></a>Horovod
[Horovod](https://github.com/uber/horovod) ist ein Open-Source-Allreduce-Framework, das von Uber für verteiltes Training entwickelt wurde. Es bietet einen einfachen Weg zum Schreiben von verteiltem TensorFlow-Code für das Training.

Ihr Trainingscode muss mit Horovod für verteiltes Training instrumentiert werden. Weitere Informationen zur Verwendung von Horovod mit TensorFlow finden Sie in der Horovod-Dokumentation:

Weitere Informationen zur Verwendung von Horovod mit TensorFlow finden Sie in der Horovod-Dokumentation:

* [Horovod mit TensorFlow](https://github.com/horovod/horovod/blob/master/docs/tensorflow.rst)
* [Horovod mit der Keras-API von TensorFlow](https://github.com/horovod/horovod/blob/master/docs/keras.rst)

Stellen Sie außerdem sicher, dass Ihre Trainingsumgebung das **horovod**-Paket enthält. Wenn Sie eine zusammengestellte TensorFlow-Umgebung verwenden, ist Horovod bereits als eine der Abhängigkeiten enthalten. Wenn Sie Ihre eigene Umgebung verwenden, stellen Sie sicher, dass die Horovod-Abhängigkeit enthalten ist, z. B.:

```yaml
channels:
- conda-forge
dependencies:
- python=3.6.2
- pip:
  - azureml-defaults
  - tensorflow-gpu==2.2.0
  - horovod==0.19.5
```

Um einen verteilten Auftrag mit MPI/Horovod in Azure ML auszuführen, müssen Sie eine [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py) für den `distributed_job_config`-Parameter des ScriptRunConfig-Konstruktors angeben. Der folgende Code konfiguriert einen auf 2 Knoten verteilten Auftrag, der einen Prozess pro Knoten ausführt. Wenn Sie auch mehrere Prozesse pro Knoten ausführen möchten (wenn die Cluster-SKU also über mehrere GPUs verfügt), geben Sie zusätzlich den `process_count_per_node`-Parameter in MpiConfiguration an (der Standardwert ist `1`).

```python
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory=project_folder,
                      script='tf_horovod_word2vec.py',
                      arguments=['--input_data', dataset.as_mount()],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=MpiConfiguration(node_count=2))
```

Ein vollständiges Tutorial zum Ausführen von verteiltem TensorFlow mit Horovod in Azure ML finden Sie unter [Verteiltes TensorFlow mit Horovod](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/distributed-tensorflow-with-horovod).

### <a name="tfdistribute"></a>tf.distribute

Wenn Sie [natives verteiltes TensorFlow](https://www.tensorflow.org/guide/distributed_training) in Ihrem Trainingscode verwenden, z. B. die `tf.distribute.Strategy`-API von TensorFlow 2.x, können Sie den verteilten Auftrag auch über Azure ML starten. 

Geben Sie hierzu eine [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) für den `distributed_job_config`-Parameter des ScriptRunConfig-Konstruktors an. Wenn Sie `tf.distribute.experimental.MultiWorkerMirroredStrategy` verwenden, geben Sie den `worker_count`-Wert in der TensorflowConfiguration an, der der Anzahl der Knoten für den Trainingsauftrag entspricht.

```python
import os
from azureml.core import ScriptRunConfig
from azureml.core.runconfig import TensorflowConfiguration

distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=0)

model_path = os.path.join("./outputs", "keras-model")

src = ScriptRunConfig(source_directory=source_dir,
                      script='train.py',
                      arguments=["--epochs", 30, "--model-dir", model_path],
                      compute_target=compute_target,
                      environment=tf_env,
                      distributed_job_config=distr_config)
```

In TensorFlow ist die `TF_CONFIG`-Umgebungsvariable für das Training auf mehreren Computern erforderlich. Azure ML konfiguriert und legt die `TF_CONFIG`-Variable für jeden Worker entsprechend fest, bevor das Trainingsskript ausgeführt wird. Sie können auf `TF_CONFIG` aus Ihrem Trainingsskript über `os.environ['TF_CONFIG']` zugreifen, falls dies erforderlich ist.

Beispielstruktur von `TF_CONFIG`, die auf einem Chief-Workerknoten festgelegt ist:
```JSON
TF_CONFIG='{
    "cluster": {
        "worker": ["host0:2222", "host1:2222"]
    },
    "task": {"type": "worker", "index": 0},
    "environment": "cloud"
}'
```

Wenn Ihr Trainingsskript die Parameterserverstrategie für verteiltes Training verwendet, d. h. für Legacy-TensorFlow 1.x, müssen Sie auch die Anzahl der Parameterserver angeben, die im Auftrag verwendet werden sollen, z. B. `distr_config = TensorflowConfiguration(worker_count=2, parameter_server_count=1)`.

## <a name="deploy-a-tensorflow-model"></a>Bereitstellen eines TensorFlow-Modells

Die Schrittanleitung zur Bereitstellung enthält einen Abschnitt zur Registrierung von Modellen, aber Sie können direkt zu [Erstellen eines Computeziels](how-to-deploy-and-where.md#choose-a-compute-target) für die Bereitstellung springen, da Sie bereits über ein registriertes Modell verfügen.

### <a name="preview-no-code-model-deployment"></a>(Vorschau) Modellimplementierung ohne Code

Anstelle der herkömmlichen Bereitstellungsroute können Sie auch die Bereitstellung ohne Code (Vorschau) für TensorFlow verwenden. Indem Sie Ihr Modell wie oben gezeigt mit den Parametern `model_framework`, `model_framework_version` und `resource_configuration` registrieren, können Sie einfach die statische Funktion `deploy()` verwenden, um Ihr Modell bereitzustellen.

```python
service = Model.deploy(ws, "tensorflow-web-service", [model])
```

Die vollständige [Schrittanleitung](how-to-deploy-and-where.md) behandelt die Bereitstellung in Azure Machine Learning eingehender.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie ein TensorFlow-Modell trainiert und registriert und sich über Bereitstellungsoptionen informiert. Weitere Informationen zu Azure Machine Learning finden Sie in den folgenden Artikeln.

* [Erfassen einer Ausführungsmetrik während des Trainings](how-to-track-experiments.md)
* [Optimieren von Hyperparametern](how-to-tune-hyperparameters.md)
* [Verteiltes Trainieren von Deep Learning-Modellen in Azure](/azure/architecture/reference-architectures/ai/training-deep-learning)
