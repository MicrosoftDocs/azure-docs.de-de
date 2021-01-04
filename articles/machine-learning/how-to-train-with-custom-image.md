---
title: Trainieren eines Modells mithilfe eines benutzerdefinierten Docker-Images
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie eigene oder von Microsoft kuratierte Docker-Images verwenden, um Modelle in Azure Machine Learning zu trainieren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.date: 10/20/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: a441d1d22f938e1d1e05aea547929fa3b315d406
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95012897"
---
# <a name="train-a-model-by-using-a-custom-docker-image"></a>Trainieren eines Modells mithilfe eines benutzerdefinierten Docker-Images

In diesem Artikel erfahren Sie, wie Sie ein benutzerdefiniertes Docker-Image zum Trainieren von Modellen mit Azure Machine Learning verwenden. Mit den Beispielskripts in diesem Artikel klassifizieren Sie Bilder von Haustieren durch Erstellen eines Convolutional Neural Network. 

Azure Machine Learning bietet ein standardmäßig verwendetes Docker-Basisimage. Sie können jedoch auch Azure Machine Learning-Umgebungen verwenden, um ein anderes Basisimage anzugeben, z. B. eines der verwalteten [Azure Machine Learning-Basisimages](https://github.com/Azure/AzureML-Containers) oder Ihr eigenes [benutzerdefiniertes Image](how-to-deploy-custom-docker-image.md#create-a-custom-base-image). Mit benutzerdefinierten Basisimages können Sie Abhängigkeiten umfassend verwalten und die Komponentenversionen beim Ausführen von Trainingsaufträgen genauer steuern.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie den Code in einer der folgenden Umgebungen aus:

* Azure Machine Learning-Compute-Instanz (weder Download noch Installation erforderlich):
  * Führen Sie die Schritte im Tutorial zum [Einrichten der Umgebung und des Arbeitsbereichs](tutorial-1st-experiment-sdk-setup.md) aus, um einen dedizierten Notebookserver zu erstellen, der bereits über das SDK und das Beispielrepository verfügt.
  * Navigieren Sie im [Azure Machine Learning-Beispielrepository](https://github.com/Azure/azureml-examples) zum Verzeichnis **notebooks** > **fastai** > **train-pets-resnet34.ipynb**, um dort nach einem fertigen Notebook zu suchen. 
* Auf Ihrem eigenen Jupyter Notebook-Server:
  * Erstellen Sie eine [Konfigurationsdatei für den Arbeitsbereich](how-to-configure-environment.md#workspace).
  * Installieren Sie das [Azure Machine Learning SDK.](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) 
  * Erstellen Sie eine [Azure Container Registry](../container-registry/index.yml)-Instanz oder eine andere Docker-Registrierung, auf die über das Internet zugegriffen werden kann.

## <a name="set-up-a-training-experiment"></a>Einrichten eines Trainingsexperiments

In diesem Abschnitt richten Sie Ihr Trainingsexperiment ein, indem Sie einen Arbeitsbereich initialisieren, Ihre Umgebung definieren und ein Computeziel konfigurieren.

### <a name="initialize-a-workspace"></a>Initialisieren eines Arbeitsbereichs

Der [Azure Machine Learning-Arbeitsbereich](concept-workspace.md) ist die Ressource der obersten Ebene für den Dienst. Er stellt einen zentralen Ort für die Arbeit mit allen von Ihnen erstellten Artefakten dar. Im Python SDK können Sie auf die Arbeitsbereichsartefakte zugreifen, indem Sie ein [`Workspace`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py)-Objekt erstellen.

Erstellen Sie aus der Datei „config.json“, die Sie im Abschnitt [Voraussetzungen](#prerequisites) erstellt haben, ein `Workspace`-Objekt.

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```

### <a name="define-your-environment"></a>Definieren der Umgebung

Erstellen Sie ein `Environment`-Objekt, und aktivieren Sie Docker.

```python
from azureml.core import Environment

fastai_env = Environment("fastai2")
fastai_env.docker.enabled = True
```

Das Basisimage im folgenden Code unterstützt die Bibliothek „fast.ai“, die Funktionen für verteiltes Deep Learning ermöglicht. Weitere Informationen finden Sie im [Docker Hub-Repository für „fast.ai“](https://hub.docker.com/u/fastdotai). 

Wenn Sie Ihr benutzerdefiniertes Docker-Image verwenden, ist Ihre Python-Umgebung möglicherweise bereits ordnungsgemäß eingerichtet. Legen Sie in diesem Fall das Flag `user_managed_dependencies` auf `True` fest, um die integrierte Python-Umgebung Ihres benutzerdefinierten Images zu verwenden. Standardmäßig erstellt Azure Machine Learning eine Conda-Umgebung mit den von Ihnen angegebenen Abhängigkeiten. Der Dienst führt das Skript in dieser Umgebung aus, statt die im Basisimage ggf. installierten Python-Bibliotheken zu verwenden.

```python
fastai_env.docker.base_image = "fastdotai/fastai2:latest"
fastai_env.python.user_managed_dependencies = True
```

#### <a name="use-a-private-container-registry-optional"></a>Verwenden einer privaten Containerregistrierung (optional)

Wenn Sie ein Image aus einer privaten Containerregistrierung verwenden möchten, die sich nicht in Ihrem Arbeitsbereich befindet, müssen Sie mit `docker.base_image_registry` die Adresse des Repositorys sowie einen Benutzernamen und ein Kennwort angeben:

```python
# Set the container registry information.
fastai_env.docker.base_image_registry.address = "myregistry.azurecr.io"
fastai_env.docker.base_image_registry.username = "username"
fastai_env.docker.base_image_registry.password = "password"
```

#### <a name="use-a-custom-dockerfile-optional"></a>Verwenden eines benutzerdefinierten Dockerfiles (optional)

Sie können auch ein benutzerdefiniertes Dockerfile verwenden. Verwenden Sie diesen Ansatz, wenn Sie Nicht-Python-Pakete als Abhängigkeiten installieren müssen. Denken Sie daran, das Basisimage auf `None` festzulegen.

```python 
# Specify Docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04
RUN echo "Hello from custom container!"
"""

# Set the base image to None, because the image is defined by Dockerfile.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
fastai_env.docker.base_image = None
fastai_env.docker.base_dockerfile = "./Dockerfile"
```

Weitere Informationen zum Erstellen und Verwalten von Azure Machine Learning-Umgebungen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md). 

### <a name="create-or-attach-a-compute-target"></a>Erstellen oder Anfügen eines Computeziels

Sie müssen zum Trainieren des Modells ein [Computeziel](concept-azure-machine-learning-architecture.md#compute-targets) erstellen. In diesem Tutorial erstellen Sie `AmlCompute` als Trainingscomputeressource.

Das Erstellen von `AmlCompute` dauert einige Minuten. Wenn sich die `AmlCompute`-Ressource bereits in Ihrem Arbeitsbereich befindet, überspringt der Code den Erstellungsprozess.

Wie bei anderen Azure-Diensten gelten für bestimmte Ressourcen (z. B. `AmlCompute`) in Verbindung mit Azure Machine Learning Service Grenzwerte. Weitere Informationen finden Sie unter [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md).

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your cluster.
cluster_name = "gpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target.')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_NC6',
                                                           max_nodes=4)

    # Create the cluster.
    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True)

# Use get_status() to get a detailed status for the current AmlCompute.
print(compute_target.get_status().serialize())
```

## <a name="configure-your-training-job"></a>Konfigurieren Ihres Trainingsauftrags

Verwenden Sie für dieses Tutorial das auf [GitHub](https://github.com/Azure/azureml-examples/blob/main/workflows/train/fastai/pets/src/train.py) verfügbare Trainingsskript *train.py*. In der Praxis können Sie jedes benutzerdefinierte Trainingsskript verwenden und so, wie es ist, mit Azure Machine Learning ausführen.

Erstellen Sie eine `ScriptRunConfig`-Ressource, um den Auftrag für die Ausführung auf dem gewünschten [Computeziel](how-to-set-up-training-targets.md) zu konfigurieren.

```python
from azureml.core import ScriptRunConfig

src = ScriptRunConfig(source_directory='fastai-example',
                      script='train.py',
                      compute_target=compute_target,
                      environment=fastai_env)
```

## <a name="submit-your-training-job"></a>Übermitteln Ihres Trainingsauftrags

Wenn Sie eine Trainingsausführung mithilfe eines `ScriptRunConfig`-Objekts übermitteln, gibt die `submit`-Methode ein Objekt vom Typ `ScriptRun` zurück. Das zurückgegebene `ScriptRun`-Objekt ermöglicht den programmgesteuerten Zugriff auf Informationen zur Trainingsausführung. 

```python
from azureml.core import Experiment

run = Experiment(ws,'fastai-custom-image').submit(src)
run.wait_for_completion(show_output=True)
```

> [!WARNING]
> Zum Ausführen von Trainingsskripts wird von Azure Machine Learning das gesamte Quellverzeichnis kopiert. Sind vertrauliche Daten vorhanden, die nicht hochgeladen werden sollen, verwenden Sie für diese eine [IGNORE-Datei](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots), oder legen Sie sie nicht im Quellverzeichnis ab. Greifen Sie stattdessen über einen [Datenspeicher](/python/api/azureml-core/azureml.data?preserve-view=true&view=azure-ml-py) auf Ihre Daten zu.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie ein Modell mit einem benutzerdefinierten Docker-Image trainiert. Weitere Informationen zu Azure Machine Learning finden Sie in den folgenden Artikeln:
* [Erfassen von Ausführungsmetriken](how-to-track-experiments.md) während des Trainings
* [Bereitstellen eines Modells](how-to-deploy-custom-docker-image.md) mithilfe eines benutzerdefinierten Docker-Images
