---
title: Bereitstellen von Machine Learning-Modellen
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie und wo Sie Machine Learning-Modelle bereitstellen können. Die Bereitstellung ist für Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge und FPGA möglich.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 01/13/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
adobe-target: true
ms.openlocfilehash: da47967b719b5ce601d8049f54597c207ea732c8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372033"
---
# <a name="deploy-machine-learning-models-to-azure"></a>Bereitstellen von Machine Learning-Modellen für Azure

Es wird beschrieben, wie Sie Ihr Machine Learning- oder Deep Learning-Modell als Webdienst in der Azure-Cloud bereitstellen. Darüber hinaus ist auch die Bereitstellung auf Azure IoT Edge-Geräten möglich.

Der Workflow ist unabhängig vom Bereitstellungsort Ihres Modells sehr ähnlich:

1. Registrieren des Modells (optional, siehe unten)
1. Vorbereiten einer Rückschlusskonfiguration (außer bei [Bereitstellung ohne Code](./how-to-deploy-no-code-deployment.md))
1. Vorbereiten eines Eingabeskripts (außer bei [Bereitstellung ohne Code](./how-to-deploy-no-code-deployment.md))
1. Auswählen eines Computeziels
1. Stellen Sie das Modell auf dem Computeziel bereit.
1. Testen des resultierenden Webdiensts

Weitere Informationen zu den Konzepten, die für den Workflow zur Bereitstellung von Machine Learning-Modellen gelten, finden Sie unter [Verwalten, Bereitstellen und Überwachen von Modellen mit Azure Machine Learning](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Ein Modell. Wenn Sie über kein trainiertes Modell verfügen, können Sie die Modell- und Abhängigkeitsdateien verwenden, die in [diesem Tutorial](https://aka.ms/azml-deploy-cloud) bereitgestellt werden.
- Die [Azure CLI-Erweiterung für den Machine Learning Service](reference-azure-machine-learning-cli.md).

# <a name="python"></a>[Python](#tab/python)

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Ein Modell. Wenn Sie über kein trainiertes Modell verfügen, können Sie die Modell- und Abhängigkeitsdateien verwenden, die in [diesem Tutorial](https://aka.ms/azml-deploy-cloud) bereitgestellt werden.
- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>Herstellen einer Verbindung mit Ihrem Arbeitsbereich

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Befolgen Sie die Anweisungen in der Azure CLI-Dokumentation zum [Festlegen Ihres Abonnementkontexts](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Führen Sie dann Folgendes aus:

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

um die Arbeitsbereiche anzuzeigen, auf die Sie Zugriff haben.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

Weitere Informationen zur Verwendung des SDKs, um eine Verbindung mit einem Arbeitsbereich herzustellen, finden Sie in der Dokumentation zum [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true#workspace).


---


## <a name="register-your-model-optional"></a><a id="registermodel"></a> Registrieren Ihres Modells (optional)

Ein registriertes Modell ist ein logischer Container für eine oder mehrere Dateien, aus denen Ihr Modell besteht. Wenn Sie beispielsweise ein Modell verwenden, das in mehreren Dateien gespeichert ist, können Sie diese als einzelnes Modell im Arbeitsbereich registrieren. Nachdem Sie die Dateien registriert haben, können Sie das registrierte Modell herunterladen oder bereitstellen und alle Dateien empfangen, die Sie registriert haben.

> [!TIP] 
> Das Registrieren eines Modells für die Versionsverfolgung wird empfohlen, ist jedoch nicht erforderlich. Wenn Sie den Vorgang fortsetzen möchten, ohne ein Modell zu registrieren, müssen Sie in [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py) oder [inferenceconfig.json](./reference-azure-machine-learning-cli.md#inference-configuration-schema) ein Quellverzeichnis angeben und sicherstellen, dass sich Ihr Modell innerhalb dieses Quellverzeichnisses befindet.

> [!TIP]
> Wenn Sie ein Modell registrieren, geben Sie den Pfad eines Cloudspeicherorts (aus einem Trainingslauf) oder eines lokalen Verzeichnisses an. Dieser Pfad wird lediglich dazu verwendet, die Dateien während des Registrierungsprozesses für das Hochladen zu finden. Er muss nicht mit dem Pfad identisch sein, der im Eingabeskript verwendet wird. Weitere Informationen finden Sie unter [Suchen nach Modelldateien im Eingabeskript](./how-to-deploy-advanced-entry-script.md#load-registered-models).

> [!IMPORTANT]
> Bei Verwendung der Option „Filtern nach `Tags`“ auf der Seite „Modelle“ von Azure Machine Learning Studio müssen Kunden `TagName=TagValue` (ohne Leerzeichen) anstelle von `TagName : TagValue` verwenden.

Die folgenden Beispiele veranschaulichen das Registrieren eines Modells.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrieren eines Modells aus einer Azure ML-Trainingsausführung

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

Der `--asset-path`-Parameter verweist auf den Cloudspeicherort des Modells. In diesem Beispiel wird der Pfad einer einzelnen Datei verwendet. Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `--asset-path` auf den Pfad eines Ordners fest, der die Dateien enthält.

### <a name="register-a-model-from-a-local-file"></a>Registrieren eines Modells aus einer lokalen Datei

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `-p` auf den Pfad eines Ordners fest, der die Dateien enthält.

Weitere Informationen zu `az ml model register` finden Sie in der [Referenzdokumentation](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Registrieren eines Modells aus einer Azure ML-Trainingsausführung

  Wenn Sie das SDK verwenden, um ein Modell zu trainieren, können Sie entweder ein [Run](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py)-Objekt oder ein [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun)-Objekt empfangen. Dies hängt davon ab, wie Sie das Modell trainiert haben. Jedes Objekt kann verwendet werden, um ein Modell zu registrieren, das von einer Experimentausführung erstellt wurde.

  + Registrieren Sie ein Modell über ein `azureml.core.Run`-Objekt:
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    Der `model_path`-Parameter verweist auf den Cloudspeicherort des Modells. In diesem Beispiel wird der Pfad einer einzelnen Datei verwendet. Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `model_path` auf den Pfad eines Ordners fest, der die Dateien enthält. Weitere Informationen finden Sie in der Dokumentation zum [Run.register-Modell](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-).

  + Registrieren Sie ein Modell über ein `azureml.train.automl.run.AutoMLRun`-Objekt:

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    In diesem Beispiel sind die Parameter `metric` und `iteration` nicht angegeben. Daher wird die Iteration mit der besten primären Metrik registriert. Der von der Ausführung zurückgegebene `model_id`-Wert wird anstelle eines Modellnamens verwendet.

    Weitere Informationen finden Sie in der Dokumentation zu [AutoMLRun.register-Modell](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-).

### <a name="register-a-model-from-a-local-file"></a>Registrieren eines Modells aus einer lokalen Datei

Sie können ein Modell registrieren, indem Sie den lokalen Pfad des Modells bereitstellen. Sie können den Pfad eines Ordners oder einer einzelnen Datei angeben. Mit dieser Methode können Sie Modelle registrieren, die mit dem Azure Machine Learning trainiert und dann heruntergeladen wurden. Mit dieser Methode können Sie auch Modelle registrieren, die außerhalb von Azure Machine Learning trainiert wurden.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **Verwenden des SDK und von ONNX**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  Um mehrere Dateien in die Modellregistrierung aufzunehmen, legen Sie `model_path` auf den Pfad eines Ordners fest, der die Dateien enthält.

Weitere Informationen finden Sie in der Dokumentation zur [Model-Klasse](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py).

Weitere Informationen zur Arbeit mit Modellen, die außerhalb des Azure Machine Learning trainiert wurden, finden Sie unter [Verwenden eines vorhandenen Modells mit Azure Machine Learning](how-to-deploy-existing-model.md).

---

## <a name="define-an-entry-script"></a>Definieren eines Einstiegsskripts

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>Definieren einer Rückschlusskonfiguration


Eine Rückschlusskonfiguration beschreibt, wie der Webdienst, der Ihr Modell enthält, eingerichtet werden kann. Sie wird später verwendet, wenn Sie das Modell bereitstellen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Eine Konfiguration für minimalen Rückschluss kann wie folgt geschrieben werden:

```json
{
    "entryScript": "score.py",
    "sourceDirectory": "./working_dir"
}
```

Hiermit wird angegeben, dass bei der Machine Learning-Bereitstellung die Datei `score.py` im Verzeichnis `./working_dir` verwendet wird, um eingehende Anforderungen zu verarbeiten.

[In diesem Artikel](./reference-azure-machine-learning-cli.md#inference-configuration-schema) finden Sie eine ausführliche Erörterung von Rückschlusskonfigurationen. 

# <a name="python"></a>[Python](#tab/python)

Dies wird im folgenden Beispiel veranschaulicht:

1. Laden einer [zusammengestellten Umgebung](resource-curated-environments.md) aus Ihrem Arbeitsbereich
1. Klonen der Umgebung
1. Festlegen von `scikit-learn` als Abhängigkeit
1. Verwenden der Umgebung zum Erstellen von eines InferenceConfig-Elements

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


env = Environment.get(workspace, "AzureML-Minimal").clone(env_name)

for pip_package in ["scikit-learn"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=env)
```

Weitere Informationen zu Umgebungen finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md).

Weitere Informationen zur Rückschlusskonfiguration finden Sie in der Dokumentation der [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py)-Klasse.

---

> [!TIP] 
> Informationen zur Verwendung eines benutzerdefinierten Docker-Images mit einer Rückschlusskonfiguration finden Sie unter [Bereitstellen eines Modells mithilfe eines benutzerdefinierten Docker-Basisimages](how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Auswählen eines Computeziels

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Definieren einer Bereitstellungskonfiguration

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Die für eine Bereitstellungskonfiguration verfügbaren Optionen unterscheiden sich je nach dem von Ihnen ausgewählten Computeziel.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

Weitere Informationen finden Sie [dieser Referenz](./reference-azure-machine-learning-cli.md#deployment-configuration-schema).

# <a name="python"></a>[Python](#tab/python)

Bevor Sie Ihr Modell bereitstellen, müssen Sie die Bereitstellungskonfiguration definieren. *Die Bereitstellungskonfiguration ist spezifisch für das Computeziel, das den Webdienst hosten wird.* Beispielsweise müssen Sie, wenn Sie ein Modell lokal bereitstellen, den Port angeben, an dem der Dienst Anforderungen akzeptiert. Die Bereitstellungskonfiguration ist kein Bestandteil Ihres Eingabeskripts. Sie wird verwendet, um die Merkmale des Computeziels zu definieren, von dem das Modell und das Eingabeskript gehostet werden.

Möglicherweise müssen Sie auch die Computeressource erstellen. Dies ist beispielsweise der Fall, wenn Sie noch keine AKS-Instanz (Azure Kubernetes Service) haben, die mit Ihrem Arbeitsbereich verknüpft ist.

Die folgende Tabelle enthält ein Beispiel für das Erstellen einer Bereitstellungskonfiguration für jedes Computeziel:

| Computeziel | Beispiel für eine Bereitstellungskonfiguration |
| ----- | ----- |
| Lokal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Die Klassen für lokale, Azure Container Instances- und AKS-Webdienste können aus `azureml.core.webservice` importiert werden:

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---

## <a name="deploy-your-machine-learning-model"></a>Bereitstellen Ihres Machine Learning-Modells

Sie können Ihr Modell jetzt bereitstellen. 

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

### <a name="using-a-registered-model"></a>Verwenden eines registrierten Modells

Wenn Sie Ihr Modell im Azure Machine Learning-Arbeitsbereich registriert haben, ersetzen Sie „mymodel:1“ durch den Namen Ihres Modells und seine Versionsnummer.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Verwenden eines lokalen Modells

Wenn Sie Ihr Modell lieber nicht registrieren möchten, können Sie den Parameter „sourceDirectory“ in Ihrer Datei „inferenceconfig.json“ übergeben, um ein lokales Verzeichnis anzugeben, aus dem Ihr Modell bereitgestellt werden soll.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

Im folgenden Beispiel wird eine lokale Bereitstellung veranschaulicht. Die Syntax variiert in Abhängigkeit von dem Computeziel, das Sie im vorherigen Schritt ausgewählt haben.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Weitere Informationen finden Sie in der Dokumentation zu [LocalWebservice](/python/api/azureml-core/azureml.core.webservice.local.localwebservice?preserve-view=true&view=azure-ml-py), [Model.deploy()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) und [Webservice](/python/api/azureml-core/azureml.core.webservice.webservice?preserve-view=true&view=azure-ml-py).

---

### <a name="understanding-service-state"></a>Grundlegendes zum Dienstzustand

Während der Modellimplementierung kann es vorkommen, dass sich der Dienstzustand während der vollständigen Bereitstellung ändert.

In der folgenden Tabelle werden die verschiedenen Dienstzustände beschrieben:

| Webservice-Zustand | BESCHREIBUNG | Endgültiger Zustand?
| ----- | ----- | ----- |
| Im Übergang | Der Dienst wird gerade bereitgestellt. | Nein |
| Fehlerhaft | Der Dienst wurde bereitgestellt, ist aber zurzeit nicht erreichbar.  | Nein |
| Nicht planbar | Der Dienst kann derzeit aufgrund fehlender Ressourcen nicht bereitgestellt werden. | Nein |
| Fehler | Der Dienst konnte aufgrund eines Fehlers oder Absturzes nicht bereitgestellt werden. | Ja |
| Healthy | Der Dienst ist fehlerfrei und der Endpunkt ist verfügbar. | Ja |

> [!TIP]
> Bei der Bereitstellung werden Docker-Images für Computeziele aus Azure Container Registry (ACR) erstellt und geladen. Standardmäßig erstellt Azure Machine Learning eine ACR-Instanz mit der Dienstebene *Basic*. Wenn Sie die ACR-Instanz für Ihren Arbeitsbereich auf einen der Tarife „Standard“ oder „Premium“ umstellen, kann dies die Zeit zum Erstellen und Bereitstellen von Images auf Ihren Computezielen verringern. Weitere Informationen finden Sie unter [Azure Container Registry-Tarife](../container-registry/container-registry-skus.md).

### <a name="batch-inference"></a><a id="azuremlcompute"></a> Batchrückschluss
Azure Machine Learning Compute-Ziele werden von Azure Machine Learning erstellt und verwaltet. Sie können für Batchvorhersagen über Azure Machine Learning-Pipelines verwendet werden.

Eine exemplarische Vorgehensweise zu Batchrückschlüssen mit Azure Machine Learning Compute finden Sie unter [Verwenden von Azure Machine Learning-Pipelines für Batchbewertung](tutorial-pipeline-batch-scoring-classification.md).

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge-Rückschluss
Unterstützung für die Bereitstellung auf Edge-Geräten befindet sich in der Vorschauphase. Weitere Informationen finden Sie unter [Bereitstellen von Azure Machine Learning als IoT Edge-Modul](../iot-edge/tutorial-deploy-machine-learning.md).

## <a name="delete-resources"></a>Löschen von Ressourcen

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)

Um einen bereitgestellten Webdienst zu löschen, verwenden Sie `az ml service <name of webservice>`.

Um ein registriertes Modell aus Ihrem Arbeitsbereich zu löschen, verwenden Sie `az ml model delete <model id>`.

Erfahren Sie mehr über das [Löschen eines Webdiensts](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) und das [Löschen eines Modells](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete).

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie zum Löschen eines bereitgestellten Webdiensts `service.delete()`.
Verwenden Sie zum Löschen eines registrierten Modells `model.delete()`.

Weitere Informationen finden Sie in der Dokumentation zu [WebService.delete()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--) und [Model.delete()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

---

## <a name="next-steps"></a>Nächste Schritte

* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Erstellen von Clientanwendungen zum Nutzen von Webdiensten](how-to-consume-web-service.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](how-to-deploy-custom-docker-image.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
* [Erstellen von Ereigniswarnungen und Triggern für Modellbereitstellungen](how-to-use-event-grid.md)