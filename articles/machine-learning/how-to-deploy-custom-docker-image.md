---
title: Bereitstellen von Modellen mit einem benutzerdefinierten Docker-Image
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie bei der Bereitstellung Ihrer Azure Machine Learning-Modelle ein benutzerdefiniertes Docker-Basisimage verwenden können. Azure Machine Learning stellt ein Standardbasisimage für Sie bereit, Sie können aber auch Ihr eigenes Basisimage verwenden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sagopal
author: saachigopal
ms.reviewer: larryfr
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 1ff4d7693a7e493ccb736ab9363fd26c93017c79
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695349"
---
# <a name="deploy-a-model-using-a-custom-docker-base-image"></a>Bereitstellen eines Modells mithilfe eines benutzerdefinierten Docker-Basisimages

Erfahren Sie, wie Sie ein benutzerdefiniertes Docker-Basisimage verwenden können, wenn Sie trainierte Modelle mit Azure Machine Learning bereitstellen.

Azure Machine Learning verwendet ein standardmäßiges Docker-Basisimage, wenn kein Image angegeben wird. Sie können das spezifische Docker-Image ermitteln, das mit `azureml.core.runconfig.DEFAULT_CPU_IMAGE`verwendet wird. Sie können auch Azure Machine Learning-__Umgebungen__ verwenden, um ein bestimmtes Basisimage auszuwählen oder ein von Ihnen bereitgestelltes benutzerdefiniertes Image zu verwenden.

Ein Basisimage wird als Ausgangspunkt verwendet, wenn ein Image für eine Bereitstellung erstellt wird. Es stellt das zugrunde liegende Betriebssystem und die Komponenten zur Verfügung. Der Bereitstellungsprozess fügt dann dem Image zusätzliche Komponenten wie Ihr Modell, Ihre Conda-Umgebung und andere Objekte hinzu.

In der Regel erstellen Sie ein benutzerdefiniertes Basisimage, wenn Sie Docker dazu verwenden möchten, Ihre Abhängigkeiten zu verwalten, eine strengere Kontrolle über Komponentenversionen zu behalten oder während der Bereitstellung Zeit zu sparen. Möglicherweise möchten Sie auch die von Ihrem Modell benötigte Software installieren, bei der der Installationsprozess sehr lange dauert. Die Installation der Software beim Erstellen des Basisimages bedeutet, dass Sie diese nicht für jede Bereitstellung installieren müssen.

> [!IMPORTANT]
> Bei der Bereitstellung eines Modells können Sie Kernkomponenten wie den Webserver oder IoT Edge-Komponenten nicht überschreiben. Diese Komponenten bieten eine bekannte Arbeitsumgebung, die von Microsoft getestet und unterstützt wird.

> [!WARNING]
> Microsoft kann möglicherweise nicht helfen, Probleme zu beheben, die durch ein benutzerdefiniertes Image verursacht werden. Wenn Sie auf Probleme stoßen, werden Sie möglicherweise aufgefordert, das Standardimage oder eines der von Microsoft bereitgestellten Images zu verwenden, um zu sehen, ob das Problem von Ihrem Image verursacht wird.

Dieser Artikel ist in zwei Abschnitte unterteilt:

* Erstellen eines benutzerdefinierten Basisimages: Bietet Administratoren und DevOps Informationen zur Erstellung eines benutzerdefinierten Images und zur Konfiguration der Authentifizierung in einer Azure Container Registry unter Verwendung der Azure CLI und Machine Learning CLI.
* Bereitstellen eines Modells mithilfe eines benutzerdefinierten Basisimages: Bietet Data Scientists und DevOps/ML-Technikern Informationen zur Verwendung benutzerdefinierter Images, wenn ein trainiertes Modell aus dem Python SDK oder der ML-CLI bereitgestellt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie im Artikel [Erstellen eines Arbeitsbereichs](how-to-manage-workspace.md).
* Das [Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py). 
* Die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* Die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Ein [Azure Container Registry](../container-registry/index.yml) oder eine andere Docker-Registrierung, die über das Internet zugänglich ist.
* Die Schritte in diesem Dokument gehen davon aus, dass Sie mit dem Erstellen und Verwenden eines __Rückschlusskonfiguration__-Objekts im Rahmen der Modellimplementierung vertraut sind. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="create-a-custom-base-image"></a>Erstellen eines benutzerdefinierten Basisimages

Die Informationen in diesem Abschnitt gehen davon aus, dass Sie eine Azure Container Registry verwenden, um Docker-Images zu speichern. Verwenden Sie die folgende Checkliste, wenn Sie planen, benutzerdefinierte Images für Azure Machine Learning zu erstellen:

* Werden Sie die Azure-Containerregistrierung, die für den Azure Machine Learning-Arbeitsbereich erstellt wurde, oder eine eigenständige Azure-Containerregistrierung verwenden?

    Wenn Sie Images verwenden, die in der __Container-Registrierung für den Arbeitsbereich__ gespeichert sind, müssen Sie sich nicht bei der Registrierung authentifizieren. Authentifizierung erfolgt über den Arbeitsbereich.

    > [!WARNING]
    > Die Azure Container Registry für Ihren Arbeitsbereich wird __erstellt, wenn Sie ein Modell zum ersten Mal über den Arbeitsbereich trainieren oder bereitstellen__. Wenn Sie einen neuen Arbeitsbereich, aber kein Training oder Modell erstellt haben, existiert für den Arbeitsbereich keine Azure Container Registry.

    Wenn Sie Images verwenden, die in einer __eigenständigen Containerregistrierung__ gespeichert sind, müssen Sie einen Dienstprinzipal konfigurieren, der mindestens Leserechte hat. Sie geben dann die Dienstprinzipal-ID (Benutzername) und das Kennwort an jeden weiter, der Images aus der Registrierung verwendet. Die Ausnahme ist, wenn Sie die Containerregistrierung öffentlich zugänglich machen.

    Informationen zum Erstellen einer privaten Azure Container Registry finden Sie unter [Erstellen einer privaten Containerregistrierung](../container-registry/container-registry-get-started-azure-cli.md).

    Informationen zur Verwendung von Dienstprinzipalen mit der Azure Container Registry finden Sie unter [Authentifizierung der Azure Container Registry mit Dienstprinzipalen](../container-registry/container-registry-auth-service-principal.md).

* Azure Container Registry und Imageinformationen: Geben Sie den Imagenamen an jeden weiter, der ihn verwenden muss. Beispielsweise wird ein Image namens `myimage`, das in einer Registrierung namens `myregistry` gespeichert ist, als `myregistry.azurecr.io/myimage` referenziert, wenn das Image für die Modellimplementierung verwendet wird.

### <a name="image-requirements"></a>Bildanforderungen

Azure Machine Learning unterstützt nur Docker-Images, die die folgende Software enthalten:
* Ubuntu 16.04 oder höher.
* Conda 4.5.# oder höher.
* Python 3.5 oder höher.

Um Datasets zu verwenden, installieren Sie das libfuse-dev-Paket. Stellen Sie außerdem sicher, dass Sie ggf. erforderliche Benutzerspeicherpakete installieren.

Azure ML verwaltet eine Reihe von CPU- und GPU-Basisimages, die in Microsoft-Container Registry veröffentlicht werden, die Sie optional nutzen (oder darauf verweisen) können, anstatt Ihr eigenes benutzerdefiniertes Image zu erstellen. Die Dockerfile-Dateien für diese Images finden Sie im GitHub-Repository [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers).

Für GPU-Images bietet Azure ML derzeit sowohl cuda9- als auch cuda10-Basisimages. Die wichtigsten Abhängigkeiten, die in diesen Basisimages installiert werden, sind:

| Abhängigkeiten | IntelMPI-CPU | OpenMPI-CPU | IntelMPI-GPU | OpenMPI-GPU |
| --- | --- | --- | --- | --- |
| miniconda | ==4.5.11 | ==4.5.11 | ==4.5.11 | ==4.5.11 |
| mpi | intelmpi==2018.3.222 |openmpi==3.1.2 |intelmpi==2018.3.222| openmpi==3.1.2 |
| cuda | - | - | 9.0/10.0 | 9.0/10.0/10.1 |
| cudnn | - | - | 7.4/7.5 | 7.4/7.5 |
| nccl | - | - | 2.4 | 2.4 |
| Git | 2.7.4 | 2.7.4 | 2.7.4 | 2.7.4 |

Die CPU-Images werden aus Ubuntu 16.04 erstellt. Die GPU-Images für cuda9 werden aus nvidia/cuda:9.0-cudnn7-devel-ubuntu16.04 erstellt. Die GPU-Images für cuda10 werden aus nvidia/cuda:10.0-cudnn7-devel-ubuntu16.04 erstellt.
<a id="getname"></a>

> [!IMPORTANT]
> Bei Verwenden benutzerdefinierter Docker-Images empfiehlt es sich, Paketversionen anzuheften, um Reproduzierbarkeit besser zu gewährleisten.

### <a name="get-container-registry-information"></a>Abrufen von Informationen aus der Container Registry

In diesem Abschnitt erfahren Sie, wie Sie den Namen der Azure Container Registry für Ihren Azure Machine Learning-Arbeitsbereich erhalten.

> [!WARNING]
> Die Azure Container Registry für Ihren Arbeitsbereich wird __erstellt, wenn Sie ein Modell zum ersten Mal über den Arbeitsbereich trainieren oder bereitstellen__. Wenn Sie einen neuen Arbeitsbereich, aber kein Training oder Modell erstellt haben, existiert für den Arbeitsbereich keine Azure Container Registry.

Wenn Sie bereits Modelle mit Azure Machine Learning trainiert oder bereitgestellt haben, wurde eine Containerregistrierung für Ihren Arbeitsbereich erstellt. Um den Namen dieser Containerregistrierung zu finden, führen Sie die folgenden Schritte aus:

1. Öffnen Sie eine neue Shell oder Eingabeaufforderung, und verwenden Sie den folgenden Befehl, um sich bei Ihrem Azure-Abonnement zu authentifizieren:

    ```azurecli-interactive
    az login
    ```

    Folgen Sie den Anweisungen, um sich beim Abonnement zu authentifizieren.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

2. Verwenden Sie den folgenden Befehl, um die Containerregistrierung für den Arbeitsbereich aufzulisten. Ersetzen Sie `<myworkspace>` durch den Namen Ihres Azure Machine Learning-Arbeitsbereichs. Ersetzen Sie `<resourcegroup>` durch die Azure-Ressourcengruppe, die Ihren Arbeitsbereich enthält:

    ```azurecli-interactive
    az ml workspace show -w <myworkspace> -g <resourcegroup> --query containerRegistry
    ```

    [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

    Die zurückgegebenen Informationen ähneln dem folgenden Text:

    ```text
    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.ContainerRegistry/registries/<registry_name>
    ```

    Der `<registry_name>`-Wert ist der Name der Azure Container Registry für Ihren Arbeitsbereich.

### <a name="build-a-custom-base-image"></a>Erstellen eines benutzerdefinierten Basisimages

Die Schritte in diesem Abschnitt führen zur Erstellung eines benutzerdefinierten Docker-Images in Ihrer Azure Container Registry. Beispiele für Dockerfile-Dateien finden Sie im GitHub-Repository [Azure/AzureML-Containers](https://github.com/Azure/AzureML-Containers)).

1. Erstellen Sie eine neue Textdatei mit dem Namen `Dockerfile`, und verwenden Sie den folgenden Text als Inhalt:

    ```text
    FROM ubuntu:16.04

    ARG CONDA_VERSION=4.7.12
    ARG PYTHON_VERSION=3.7
    ARG AZUREML_SDK_VERSION=1.13.0
    ARG INFERENCE_SCHEMA_VERSION=1.1.0

    ENV LANG=C.UTF-8 LC_ALL=C.UTF-8
    ENV PATH /opt/miniconda/bin:$PATH
    ENV DEBIAN_FRONTEND=noninteractive

    RUN apt-get update --fix-missing && \
        apt-get install -y wget bzip2 && \
        apt-get install -y fuse && \
        apt-get clean -y && \
        rm -rf /var/lib/apt/lists/*

    RUN useradd --create-home dockeruser
    WORKDIR /home/dockeruser
    USER dockeruser

    RUN wget --quiet https://repo.anaconda.com/miniconda/Miniconda3-${CONDA_VERSION}-Linux-x86_64.sh -O ~/miniconda.sh && \
        /bin/bash ~/miniconda.sh -b -p ~/miniconda && \
        rm ~/miniconda.sh && \
        ~/miniconda/bin/conda clean -tipsy
    ENV PATH="/home/dockeruser/miniconda/bin/:${PATH}"

    RUN conda install -y conda=${CONDA_VERSION} python=${PYTHON_VERSION} && \
        pip install azureml-defaults==${AZUREML_SDK_VERSION} inference-schema==${INFERENCE_SCHEMA_VERSION} &&\
        conda clean -aqy && \
        rm -rf ~/miniconda/pkgs && \
        find ~/miniconda/ -type d -name __pycache__ -prune -exec rm -rf {} \;
    ```

2. Führen Sie von einer Shell oder Eingabeaufforderung aus die folgenden Schritte durch, um sich bei der Azure Container Registry zu authentifizieren. Ersetzen Sie das `<registry_name>` durch den Namen der Containerregistrierung, in der Sie das Image speichern möchten:

    ```azurecli-interactive
    az acr login --name <registry_name>
    ```

3. Um das Dockerfile hochzuladen und zu erstellen, verwenden Sie den folgenden Befehl. Ersetzen Sie `<registry_name>` durch den Namen der Containerregistrierung, in der Sie das Image speichern möchten:

    ```azurecli-interactive
    az acr build --image myimage:v1 --registry <registry_name> --file Dockerfile .
    ```

    > [!TIP]
    > In diesem Beispiel wird das Tag `:v1` auf das Image angewandt. Wenn kein Tag angegeben ist, wird das Tag `:latest` angewandt.

    Während des Erstellungsprozesses werden Informationen gestreamt, um sie an die Befehlszeile zurückzugeben. Wenn die Erstellung erfolgreich war, erhalten Sie eine Meldung ähnlich dem folgenden Text:

    ```text
    Run ID: cda was successful after 2m56s
    ```

Weitere Informationen zum Erstellen von Images mit einer Azure Container Registry finden Sie unter [Erstellen und Ausführen eines Containerimages mit Azure Container Registry Tasks](../container-registry/container-registry-quickstart-task-cli.md).

Weitere Informationen zum Hochladen vorhandener Images in eine Azure Container Registry finden Sie unter [Senden Sie ihr erstes Bild an eine private Docker-Containerregistrierung](../container-registry/container-registry-get-started-docker-cli.md).

## <a name="use-a-custom-base-image"></a>Verwenden eines benutzerdefinierten Basisimages

Um ein benutzerdefiniertes Image zu verwenden, benötigen Sie die folgenden Informationen:

* Der __Imagename__. Zum Beispiel ist `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest` der Pfad zu einem einfachen Docker-Image, das von Microsoft bereitgestellt wird.

    > [!IMPORTANT]
    > Stellen Sie für eigene erstellte Images sicher, dass Sie alle mit dem Image verwendeten Tags einschließen. Beispiel: Das Image wurde mit einem bestimmten Tag erstellt, z. B. `:v1`. Wenn Sie beim Erstellen des Images kein bestimmtes Tag verwendet haben, wurde das Tag `:latest` angewandt.

* Wenn sich das Image in einem __privaten Repository__ befindet, benötigen Sie die folgenden Informationen:

    * Die __Registrierungsadresse__. Beispiel: `myregistry.azureecr.io`.
    * Ein __Benutzername__ und ein __Kennwort__ für den Dienstprinzipal, der Lesezugriff auf die Registrierung hat.

    Wenn Sie diese Informationen nicht haben, wenden Sie sich an den Administrator der Azure Container Registry, die Ihr Image enthält.

### <a name="publicly-available-base-images"></a>Öffentlich verfügbare Basisimages

Microsoft stellt mehrere Docker-Images in einem öffentlich zugänglichen Repository zur Verfügung, die in den Schritten in diesem Abschnitt verwendet werden können:

| Image | BESCHREIBUNG |
| ----- | ----- |
| `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda` | Core-Image für Azure Machine Learning |
| `mcr.microsoft.com/azureml/onnxruntime:latest` | Enthält ONNX Runtime für CPU-Rückschlüsse |
| `mcr.microsoft.com/azureml/onnxruntime:latest-cuda` | Enthält ONNX Runtime und CUDA für GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-tensorrt` | Enthält ONNX Runtime und TensorRT für GPU |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-vadm` | Enthält ONNX Runtime und OpenVINO für Intel<sup></sup> Vision Accelerator Design auf Basis von Movidius<sup>TM</sup> MyriadX VPUs |
| `mcr.microsoft.com/azureml/onnxruntime:latest-openvino-myriad` | Enthält ONNX Runtime und OpenVINO für Intel<sup></sup> Movidius<sup>TM</sup> USB-Sticks |

Weitere Informationen zu ONNX Runtime-Basisimages finden Sie im Abschnitt zur [ONNX Runtime-Dockerfile](https://github.com/microsoft/onnxruntime/blob/master/dockerfiles/README.md) im GitHub-Repository.

> [!TIP]
> Da diese Images öffentlich zugänglich sind, müssen Sie bei der Verwendung keine Adresse, Benutzernamen oder Kennwort angeben.

Weitere Informationen finden Sie im GitHub-Repository [Azure Machine Learning service containers](https://github.com/Azure/AzureML-Containers).

### <a name="use-an-image-with-the-azure-machine-learning-sdk"></a>Verwenden eines Images mit dem Azure Machine Learning SDK

Legen Sie die folgenden [Environment](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)-Attribute fest, um ein Image zu verwenden, das in der **Azure Container Registry-Instanz für Ihren Arbeitsbereich gespeichert ist**, oder eine **Containerregistrierung, die öffentlich zugänglich ist**:

+ `docker.enabled=True`
+ `docker.base_image`: Legen Sie dies auf die Registrierung und den Pfad zum Image fest.

```python
from azureml.core.environment import Environment
# Create the environment
myenv = Environment(name="myenv")
# Enable Docker and reference an image
myenv.docker.enabled = True
myenv.docker.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest"
```

Um ein Image aus einer __privaten Containerregistrierung__ zu nutzen, die sich nicht in Ihrem Arbeitsbereich befindet, müssen Sie `docker.base_image_registry` verwenden, um die Adresse des Repositorys sowie einen Benutzernamen und ein Kennwort anzugeben:

```python
# Set the container registry information
myenv.docker.base_image_registry.address = "myregistry.azurecr.io"
myenv.docker.base_image_registry.username = "username"
myenv.docker.base_image_registry.password = "password"

myenv.inferencing_stack_version = "latest"  # This will install the inference specific apt packages.

# Define the packages needed by the model and scripts
from azureml.core.conda_dependencies import CondaDependencies
conda_dep = CondaDependencies()
# you must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
myenv.python.conda_dependencies=conda_dep
```

Sie müssen „azureml-defaults“ mit Version >= 1.0.45 als Pip-Abhängigkeit hinzufügen. Dieses Paket enthält die erforderlichen Funktionen zum Hosten des Modells als Webdienst. Sie müssen auch die „inferencing_stack_version“-Eigenschaft der Umgebung auf „latest“ (Neueste) festlegen. Dadurch werden bestimmte, vom Webdienst erforderliche apt-Pakete installiert. 

Verwenden Sie die Umgebung nach dem Definieren mit einem [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py)-Objekt, um die Rückschlussumgebung festzulegen, in der das Modell und der Webdienst ausgeführt werden.

```python
from azureml.core.model import InferenceConfig
# Use environment in InferenceConfig
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Sie können dann mit der Bereitstellung fortfahren. Mit dem folgenden Codeausschnitt wird beispielsweise ein Webdienst lokal bereitgestellt, indem die Rückschlusskonfiguration und das benutzerdefinierte Image verwendet werden:

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Weitere Informationen zur Bereitstellung finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

Weitere Informationen zur Anpassung Ihrer Python-Umgebung finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md). 

### <a name="use-an-image-with-the-machine-learning-cli"></a>Verwenden eines Images mit der Machine Learning-CLI

> [!IMPORTANT]
> Derzeit kann die Machine Learning-CLI Images aus der Azure Container Registry für Ihren Arbeitsbereich oder aus öffentlich zugänglichen Repositorys verwenden. Sie kann keine Images aus eigenständigen privaten Registrierungen verwenden.

Bevor Sie ein Modell mit der Machine Learning-CLI bereitstellen, erstellen Sie eine [Umgebung](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py), die das benutzerdefinierte Image verwendet. Erstellen Sie dann eine Rückschlusskonfigurationsdatei, die auf die Umgebung verweist. Sie können die Umgebung auch direkt in der Rückschlusskonfigurationsdatei definieren. Das folgende JSON-Dokument zeigt, wie auf ein Image in einer öffentlichen Containerregistrierung verwiesen wird. In diesem Beispiel wird die Umgebung inline definiert:

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda:latest",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

Diese Datei wird mit dem `az ml model deploy`-Befehl verwendet. Der `--ic`-Parameter wird verwendet, um die Rückschlusskonfigurationsdatei anzugeben.

```azurecli
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
```

Weitere Informationen zur Bereitstellung eines Modells mit der ML-CLI finden Sie im Abschnitt „Modellregistrierung, Profilerstellung und Bereitstellung“ des Artikels [Verwenden der CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md#model-registration-profiling-deployment).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr darüber, [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* Erfahren Sie, wie ein [Trainieren und Bereitstellen von Machine Learning-Modellen mit Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning?view=azure-devops&preserve-view=true) erfolgt.