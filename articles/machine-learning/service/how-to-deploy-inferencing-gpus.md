---
title: Bereitstellen eines Modells für das Ziehen von Rückschlüssen mit einer GPU
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie ein Deep Learning-Modell als Webdienst bereitstellen, für den eine GPU zum Ziehen von Rückschlüssen verwendet wird. In diesem Artikel wird ein Tensorflow-Modell in einem Azure Kubernetes Service-Cluster bereitgestellt. Der Cluster verwendet einen GPU-fähigen virtuellen Computer, um den Webdienst zu hosten und Rückschlussanforderungen zu bewerten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 05/02/2019
ms.openlocfilehash: ec71165553a1d65ff133d605bf94255100f74e6e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388924"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>Bereitstellen eines Deep Learning-Modells für das Ziehen von Rückschlüssen mit einer GPU

Es wird beschrieben, wie Sie GPU-Rückschlüsse für ein als Webdienst bereitgestelltes Machine Learning-Modell verwenden. Rückschlüsse oder Modellbewertungen stellen die Phase dar, in der das bereitgestellte Modell für die Vorhersage verwendet wird (meist für Produktionsdaten).

In diesem Artikel erfahren Sie, wie Sie den Azure Machine Learning Service nutzen, um ein Beispiel für ein TensorFlow-Deep Learning-Modell in einem AKS-Cluster (Azure Kubernetes Service) auf einem GPU-fähigen virtuellen Computer (VM) bereitzustellen. Wenn Anforderungen an den Dienst gesendet werden, verwendet das Modell die GPU zur Ausführung der Rückschlussworkloads.

GPUs bieten Leistungsvorteile gegenüber CPUs bei hochparallelisierbarer Berechnung. Sehr gut geeignete Anwendungsfälle für GPU-fähige VMs sind Deep Learning-Modelltraining und Rückschlüsse. Dies gilt vor allem für große Batches mit Anforderungen.

Dieses Beispiel veranschaulicht, wie Sie ein gespeichertes TensorFlow-Modell in Azure Machine Learning bereitstellen. Führen Sie die folgenden Schritte aus:

* Erstellen eines GPU-fähigen AKS-Clusters
* Bereitstellen eines TensorFlow-GPU-Modells

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning Service-Arbeitsbereich
* Eine Python-Distribution
* Ein registriertes gespeichertes TensorFlow-Modell. Informationen zum Registrieren von Modellen finden Sie unter [Bereitstellen von Modellen](../service/how-to-deploy-and-where.md#registermodel).

Dieser Artikel basiert auf dem Jupyter-Notebook [Deploying TensorFlow Models to AKS](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/production-deploy-to-aks-gpu/production-deploy-to-aks-gpu.ipynb) (Bereitstellen von TensorFlow-Modellen für AKS). Für das Jupyter-Notebook werden gespeicherte TensorFlow-Modelle verwendet und in einem AKS-Cluster bereitgestellt. Sie können das Notebook auch unter allen Machine Learning-Frameworks anwenden, die GPUs unterstützen, indem Sie geringfügige Änderungen an der Bewertungsdatei und der Umgebungsdatei vornehmen.  

## <a name="provision-an-aks-cluster-with-gpus"></a>Bereitstellen eines AKS-Clusters mit GPUs

Azure verfügt über viele verschiedene GPU-Optionen. Diese können Sie alle für das Ziehen von Rückschlüssen verwenden. In der [Liste mit den VMs der N-Serie](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) finden Sie eine vollständige Aufschlüsselung der Funktionen und Kosten.

Weitere Informationen zur Verwendung von AKS mit Azure Machine Learning Service finden Sie unter [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](../service/how-to-deploy-and-where.md#deploy-aks).

```python
# Provision AKS cluster with GPU machine
prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

# Create the cluster
aks_target = ComputeTarget.create(
    workspace=ws, name=aks_name, provisioning_configuration=prov_config
)

aks_target.wait_for_deployment()
```

> [!IMPORTANT]
> Azure berechnet Ihnen Gebühren, solange der AKS-Cluster bereitgestellt ist. Stellen Sie sicher, dass Sie Ihren AKS-Cluster löschen, wenn Sie ihn nicht mehr benötigen.

## <a name="write-the-entry-script"></a>Schreiben des Eingangsskripts

Speichern Sie den folgenden Code als `score.py` in Ihrem Arbeitsverzeichnis. Mit dieser Datei werden Bilder bewertet, wenn sie an Ihren Dienst gesendet werden. Sie lädt das gespeicherte TensorFlow-Modell und übergibt bei jeder POST-Anforderung dann das Eingabebild an die TensorFlow-Sitzung und gibt die sich ergebenden Bewertungen zurück. Für andere Frameworks zum Ziehen von Rückschlüssen sind andere Bewertungsdateien erforderlich.

```python
import tensorflow as tf
import numpy as np
import ujson
from azureml.core.model import Model
from azureml.contrib.services.aml_request import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    global session
    global input_name
    global output_name
    
    session = tf.Session()

    model_path = Model.get_model_path('resnet50')
    model = tf.saved_model.loader.load(session, ['serve'], model_path)
    if len(model.signature_def['serving_default'].inputs) > 1:
        raise ValueError("This score.py only supports one input")
    input_name = [tensor.name for tensor in model.signature_def['serving_default'].inputs.values()][0]
    output_name = [tensor.name for tensor in model.signature_def['serving_default'].outputs.values()]
    

@rawhttp
def run(request):
    if request.method == 'POST':
        reqBody = request.get_data(False)
        resp = score(reqBody)
        return AMLResponse(resp, 200)
    if request.method == 'GET':
        respBody = str.encode("GET is not supported")
        return AMLResponse(respBody, 405)
    return AMLResponse("bad request", 500)

def score(data):
    result = session.run(output_name, {input_name: [data]})
    return ujson.dumps(result[1])

if __name__ == "__main__":
    init()
    with open("lynx.jpg", 'rb') as f: #load file for testing locally
        content = f.read()
        print(score(content))

```

## <a name="define-the-conda-environment"></a>Definieren der Conda-Umgebung

Erstellen Sie eine Conda-Umgebungsdatei namens `myenv.yml`, um die Abhängigkeiten für Ihren Dienst anzugeben. Es ist wichtig anzugeben, dass Sie `tensorflow-gpu` verwenden, um eine beschleunigte Leistung zu erzielen.

```yaml
name: aml-accel-perf
channels:
  - defaults
dependencies:
  - tensorflow-gpu = 1.12
  - numpy
  - ujson
  - pip:
    - azureml-core
    - azureml-contrib-services
```

## <a name="define-the-gpu-inferenceconfig-class"></a>Definieren der InferenceConfig-GPU-Klasse

Erstellen Sie ein `InferenceConfig`-Objekt, um die GPUs zu aktivieren und sicherzustellen, dass CUDA mit Ihrem Docker-Image installiert wird.

```python
from azureml.core.model import Model
from azureml.core.model import InferenceConfig

aks_service_name ='gpu-rn'
gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled = False, 
                                                    num_replicas = 3, 
                                                    cpu_cores=2, 
                                                    memory_gb=4)
model = Model(ws,"resnet50")

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score.py",
                                   conda_file="myenv.yml", 
                                   gpu_enabled=True)
```

Weitere Informationen finden Sie unter

- [InferenceConfig-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)
- [AksServiceDeploymentConfiguration-Klasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Stellen Sie dieses Modell in Ihrem AKS-Cluster bereit, und warten Sie, bis es den Dienst erstellt.

```python
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config, 
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!NOTE]
> Der Azure Machine Learning Service stellt kein Modell mit einem `InferenceConfig`-Objekt bereit, für das erwartet wird, dass die GPU für einen Cluster ohne GPU aktiviert ist.

Weitere Informationen finden Sie unter [Model class](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py) (Model-Klasse).

## <a name="issue-a-sample-query-to-your-deployed-model"></a>Ausführen einer Beispielabfrage für Ihr bereitgestelltes Modell

Senden Sie eine Testabfrage an das bereitgestellte Modell. Wenn Sie ein JPEG-Bild an das Modell senden, wird es bewertet.

```python
scoring_url = aks_service.scoring_uri
api_key = aks_service.get_key()(0)
IMAGEURL = "https://upload.wikimedia.org/wikipedia/commons/thumb/6/68/Lynx_lynx_poing.jpg/220px-Lynx_lynx_poing.jpg"

headers = {'Authorization':('Bearer '+ api_key)}
img_data = read_image_from(IMAGEURL).read()
r = requests.post(scoring_url, data = img_data, headers=headers)
```

> [!IMPORTANT]
> Stellen Sie sicher, dass sich Ihr Client in derselben Azure-Region wie der Endpunkt befindet, um die Latenz zu verringern und den Durchsatz zu optimieren. In diesem Beispiel werden die APIs in der Azure-Region „USA, Osten“ erstellt.

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Löschen Sie Ihre Ressourcen, nachdem Sie dieses Beispiel abgeschlossen haben.

> [!IMPORTANT]
> Azure berechnet Ihnen Gebühren basierend darauf, wie lange der AKS-Cluster bereitgestellt wird. Achten Sie darauf, ihn zu bereinigen, wenn Sie ihn nicht mehr nutzen.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen eines Modells auf einem FPGA](../service/how-to-deploy-fpga-web-service.md)
* [Bereitstellen eines Modells mit ONNX](../service/concept-onnx.md#deploy-onnx-models-in-azure)
* [Trainieren von TensorFlow-DNN-Modellen](../service/how-to-train-tensorflow.md)
