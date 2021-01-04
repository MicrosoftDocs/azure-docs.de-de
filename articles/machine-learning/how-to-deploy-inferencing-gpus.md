---
title: Bereitstellen eines Modells für das Ziehen von Rückschlüssen mit einer GPU
titleSuffix: Azure Machine Learning
description: Dieser Artikel zeigt Ihnen, wie Sie Azure Machine Learning verwenden können, um ein GPU-fähiges TensorFlow-Deep Learning-Modell als Webdienst bereitzustellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy
ms.openlocfilehash: 8480a0b8722fbfff0f1d8a8fafc1a64f38d21d6e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307204"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Bereitstellen eines Deep Learning-Modells für das Ziehen von Rückschlüssen mit einer GPU


Dieser Artikel zeigt Ihnen, wie Sie Azure Machine Learning verwenden können, um ein GPU-fähiges Modell als Webservice bereitzustellen. Die Informationen in diesem Artikel basieren auf der Bereitstellung eines Modells in Azure Kubernetes Service (AKS). Der AKS-Cluster stellt eine GPU-Ressource bereit, die vom Modell für das Ziehen von Rückschlüssen verwendet wird.

Das Ziehen von Rückschlüssen oder Modellbewertung ist die Phase, in der das bereitgestellte Modell verwendet wird, um Vorhersagen zu treffen. Die Verwendung von GPUs anstelle von CPUs bietet Leistungsvorteile bei hochparallelisierbaren Berechnungen.

> [!IMPORTANT]
> Für Webdienstbereitstellungen wird GPU-Rückschluss nur in Azure Kubernetes Service unterstützt. Für den Rückschluss mithilfe einer __Machine Learning-Pipeline__ werden GPUs nur in Azure Machine Learning Compute unterstützt. Weitere Informationen zur Verwendung von ML-Pipelines finden Sie im [Tutorial: Erstellen einer Azure Machine Learning-Pipeline für die Batchbewertung](tutorial-pipeline-batch-scoring-classification.md). 

> [!TIP]
> Obwohl bei den Codeausschnitten in diesem Artikel ein TensorFlow-Modell verwendet wird, können Sie die Informationen auf jedes Machine Learning-Framework anwenden, das GPUs unterstützt.

> [!NOTE]
> Die Informationen in diesem Artikel bauen auf den Informationen im Artikel [Bereitstellen in Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) auf. Während darin die Bereitstellung in AKS im Allgemeinen behandelt wird, geht es in diesem Artikel um die GPU-spezifische Bereitstellung.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Eine Python-Entwicklungsumgebung, in der das Azure Machine Learning SDK installiert ist. Weitere Informationen finden Sie unter [Install the Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (Installieren des Azure Machine Learning SDK für Python).  

* Ein registriertes Modell, das eine GPU verwendet.

    * Informationen zum Registrieren von Modellen finden Sie unter [Bereitstellen von Modellen](how-to-deploy-and-where.md#registermodel).

    * Informationen zum Erstellen und Registrieren des TensorFlow-Modells, das zum Erstellen dieses Dokuments verwendet wird, finden Sie unter [Trainieren eines TensorFlow-Modells](how-to-train-tensorflow.md).

* Ein allgemeines Verständnis dafür, [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Herstellen einer Verbindung mit Ihrem Arbeitsbereich

Verwenden Sie den folgenden Code, um eine Verbindung mit einem vorhandenen Arbeitsbereich herzustellen:

> [!IMPORTANT]
> In diesem Codeausschnitt wird davon ausgegangen, dass die Arbeitsbereichskonfiguration im aktuellen Verzeichnis oder in dessen übergeordnetem Verzeichnis gespeichert wird. Weitere Informationen zum Erstellen eines Arbeitsbereichs finden Sie unter [Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen](how-to-manage-workspace.md).   Weitere Informationen zum Speichern der Konfiguration in einer Datei finden Sie unter [Konfigurieren einer Entwicklungsumgebung für Azure Machine Learning](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Erstellen eines Kubernetes-Clusters mit GPUs

Der Azure Kubernetes Service bietet viele verschiedene GPU-Optionen. Diese können Sie alle für das Ziehen von Modellrückschlüssen verwenden. In der [Liste mit den VMs der N-Serie](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) finden Sie eine vollständige Aufschlüsselung der Funktionen und Kosten.

Der folgende Code veranschaulicht, wie Sie einen neuen AKS-Cluster für Ihren Arbeitsbereich erstellen:

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure berechnet Ihnen Gebühren, solange der AKS-Cluster vorhanden ist. Stellen Sie sicher, dass Sie Ihren AKS-Cluster löschen, wenn Sie ihn nicht mehr benötigen.

Weitere Informationen zum Verwenden von AKS mit Azure Machine Learning finden Sie unter [Bereitstellen in Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Schreiben des Eingangsskripts

Das Eingabeskript empfängt Daten, die an den Webdienst übermittelt wurden, übergibt sie an das Modell und gibt die Bewertungsergebnisse zurück. Das folgende Skript lädt das TensorFlow-Modell beim Start und verwendet es dann zum Bewerten von Daten.

> [!TIP]
> Das Eingabeskript ist für Ihr Modell spezifisch. Das Skript muss beispielsweise das Framework kennen, das bei Ihrem Modell, den Datenformaten usw. verwendet werden soll.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

Diese Datei hat den Namen `score.py`. Weitere Informationen zu Eingabeskripts finden Sie unter [Bereitstellung: wo und wie](how-to-deploy-and-where.md).

## <a name="define-the-conda-environment"></a>Definieren der Conda-Umgebung

Die Conda-Umgebungsdatei gibt die Abhängigkeiten für den Dienst an. Sie enthält Abhängigkeiten, die sowohl für das Modell als auch das Eingabeskript erforderlich sind. Beachten Sie, dass Sie azureml-defaults mit Version >= 1.0.45 als PIP-Abhängigkeit angeben müssen, da sie die Funktionalität enthält, die zum Hosten des Modells als Webdienst erforderlich ist. Der folgende YAML-Code definiert die Umgebung für ein TensorFlow-Modell. Er gibt den Wert `tensorflow-gpu` an, der die in dieser Bereitstellung verwendete GPU nutzen wird:

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

Bei diesem Beispiel wird die Datei als `myenv.yml` gespeichert.

## <a name="define-the-deployment-configuration"></a>Definieren der Bereitstellungskonfiguration

> [!IMPORTANT]
> AKS erlaubt es nicht, dass Pods GPUs gemeinsam nutzen. Sie können nur so viele Replikate eines GPU-fähigen Webdiensts verwenden, wie es GPUs im Cluster gibt.

Die Bereitstellungskonfiguration definiert die Azure Kubernetes Service-Umgebung, die zum Ausführen des Webdiensts verwendet wird:

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

Weitere Informationen finden Sie in der Referenzdokumentation zu [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definieren der Rückschlusskonfiguration

Die Rückschlusskonfiguration verweist auf das Eingabeskript und ein Umgebungsobjekt, das ein Docker-Image mit GPU-Unterstützung verwendet. Bitte beachten Sie, dass die für die Umgebungsdefinition verwendete YAML-Datei azureml-defaults mit Version >= 1.0.45 als PIP-Abhängigkeit auflisten muss, da sie die Funktionalität enthält, die zum Hosten des Modells als Webdienst erforderlich ist.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

Weitere Informationen zu Umgebungen finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md).
Weitere Informationen finden Sie in der Referenzdokumentation zu [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py).

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Stellen Sie dieses Modell in Ihrem AKS-Cluster bereit, und warten Sie, bis es den Dienst erstellt.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

Weitere Informationen finden Sie in der Referenzdokumentation zu [Model](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Ausstellen einer Beispielabfrage für Ihren Dienst

Senden Sie eine Testabfrage an das bereitgestellte Modell. Wenn Sie ein JPEG-Bild an das Modell senden, wird es bewertet. Im folgenden Codebeispiel werden Testdaten heruntergeladen. Anschließend wird ein zufälliges Testbild ausgewählt, das an den Dienst gesendet werden soll.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

Weitere Informationen zum Erstellen einer Clientanwendung finden Sie unter [Erstellen eines Clients zur Nutzung des bereitgestellten Webdiensts](how-to-consume-web-service.md).

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Wenn Sie den AKS-Cluster speziell für dieses Beispiel erstellt haben, löschen Sie Ihre Ressourcen nach Abschluss des Vorgangs.

> [!IMPORTANT]
> Azure berechnet Ihnen Gebühren basierend darauf, wie lange der AKS-Cluster bereitgestellt wird. Achten Sie darauf, ihn zu bereinigen, wenn Sie ihn nicht mehr nutzen.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines Modells auf einem FPGA](how-to-deploy-fpga-web-service.md)
* [Bereitstellen eines Modells mit ONNX](concept-onnx.md#deploy-onnx-models-in-azure)
* [Trainieren von TensorFlow-DNN-Modellen](how-to-train-tensorflow.md)