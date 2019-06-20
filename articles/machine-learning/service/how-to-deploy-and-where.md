---
title: Wie und wo Modelle bereitgestellt werden
titleSuffix: Azure Machine Learning service
description: 'Erfahren Sie, wie und wo Sie Ihre Azure Machine Learning Service-Modelle bereitstellen, wie z. B.: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge und Field Programmable Gate Arrays.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/31/2019
ms.custom: seoapril2019
ms.openlocfilehash: 328da909449eb71df3139e06be2254eb302232a4
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692909"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst

Erfahren Sie, wie Sie Ihr Machine Learning-Modell als Webdienst in der Azure-Cloud oder auf IoT Edge-Geräten bereitstellen. 

Der Workflow ähnelt sich unabhängig vom [Bereitstellungsort](#target) Ihres Modells:

1. Registrieren des Modells.
1. Vorbereiten der Bereitstellung (Ressourcen, Nutzung, Computeziel angeben).
1. Bereitstellen des Modells auf dem Computeziel.
1. Testen des bereitgestellten Modells (auch als „Webdienst“ bezeichnet).

Weitere Informationen zu den am Bereitstellungsworkflow beteiligten Konzepten finden Sie unter [Verwalten, Bereitstellen und Überwachen von Modellen mit dem Azure Machine Learning-Dienst](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Modell. Wenn Sie über kein trainiertes Modell verfügen, können Sie die Modell- und Abhängigkeitsdateien verwenden, die in [diesem Tutorial](https://aka.ms/azml-deploy-cloud) bereitgestellt werden.

- Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](how-to-vscode-tools.md).

## <a id="registermodel"></a> Registrieren Ihres Modells

Registrieren Sie Ihre Machine Learning-Modelle in Ihrem Azure Machine Learning-Arbeitsbereich. Das Modell kann aus Azure Machine Learning oder aus einer anderen Quelle stammen. Die folgenden Beispiele veranschaulichen das Registrieren eines Modells aus einer Datei.

### <a name="register-a-model-from-an-experiment-run"></a>Registrieren eines Modells aus einer Experimentausführung

+ **Scikit-Learn-Beispiel mit Verwendung des SDK**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```
+ **Verwenden der CLI**
  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```


+ **Verwenden von VS Code**

  Registrieren Sie Modelle, indem Sie beliebige Modelldateien oder -ordner mit der [VS Code](how-to-vscode-tools.md#deploy-and-manage-models)-Erweiterung verwenden.

### <a name="register-an-externally-created-model"></a>Registrieren eines extern erstellten Modells

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

Sie können ein extern erstelltes Modell registrieren, indem Sie einen **lokalen Pfad** für das Modell bereitstellen. Sie können einen Ordner oder eine einzelne Datei angeben.

+ **ONNX-Beispiel mit dem Python SDK:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

+ **Verwenden der CLI**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

**Geschätzter Zeitaufwand**: Ungefähr 10 Sekunden.

Weitere Informationen finden Sie in der Referenzdokumentation zur [Modellklasse](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py).

<a name="target"></a>

## <a name="choose-a-compute-target"></a>Auswählen eines Computeziels

Die folgenden Computeziele bzw. Computeressourcen können verwendet werden, um Ihre Webdienstbereitstellung zu hosten. 

| Computeziel | Verwendung | BESCHREIBUNG |
| ----- | ----- | ----- |
| [Lokaler Webdienst](#local) | Testen/Debuggen | Geeignet für eingeschränkte Tests und Problembehandlung.
| [Azure Kubernetes Service (AKS)](#aks) | Echtzeitrückschluss | Geeignet für hochgradig skalierbare Produktionsbereitstellungen. Bietet automatische Skalierung und schnelle Reaktionszeiten. |
| [Azure Container Instances (ACI)](#aci) | Testen | Ideal für CPU-basierte Workloads mit geringer Skalierung. |
| [Azure Machine Learning Compute](how-to-run-batch-predictions.md) | Batchrückschluss | Führen Sie Batchrückschluss für serverloses Computing aus. Unterstützt virtuelle Computer mit normaler und niedriger Priorität. |
| [Azure IoT Edge](#iotedge) | (Vorschauversion) IoT-Modul | Bereitstellen und Verarbeiten von ML-Modellen auf IoT-Geräten. |


## <a name="prepare-to-deploy"></a>Vorbereiten der Bereitstellung

Um die Bereitstellung als Webdienst durchzuführen, müssen Sie eine Rückschlusskonfiguration (`InferenceConfig`) und eine Bereitstellungskonfiguration erstellen. Rückschlüsse oder Modellbewertungen stellen die Phase dar, in der das bereitgestellte Modell für die Vorhersage verwendet wird (meist für Produktionsdaten). In der Rückschlusskonfiguration geben Sie die Skripts und Abhängigkeiten an, die für die Verarbeitung Ihres Modells erforderlich sind. In der Bereitstellungskonfiguration geben Sie Details zur Verarbeitung des Modells auf dem Computeziel an.


### <a id="script"></a> 1. Definieren des Eingangsskripts und der Abhängigkeiten

Das Eingangsskript empfängt an einen bereitgestellten Webdienst übermittelte Daten und übergibt sie an das Modell. Anschließend nimmt es die vom Modell zurückgegebene Antwort entgegen und gibt diese an den Client zurück. **Das Skript ist modellspezifisch.** Es muss die vom Modell erwarteten und zurückgegebenen Daten verstehen.

Das Skript enthält zwei Funktionen zum Laden und Ausführen des Modells:

* `init()`: Diese Funktion lädt üblicherweise das Modell in ein globales Objekt. Diese Funktion wird nur ein Mal ausgeführt, wenn der Docker-Container für Ihren Webdienst gestartet wird.

* `run(input_data)`: Diese Funktion verwendet das Modell zur Vorhersage eines Werts, der auf den Eingabedaten basiert. Ein- und Ausgaben an die/von der Ausführung verwenden in der Regel JSON für die Serialisierung und Deserialisierung. Sie können auch mit binären Rohdaten arbeiten. Sie können die Daten vor dem Senden an das Modell oder vor der Rückgabe an den Client transformieren.

#### <a name="optional-automatic-swagger-schema-generation"></a>(Optional) Automatische Generierung des Swagger-Schemas

Um automatisch ein Schema für Ihren Webdienst zu generieren, stellen Sie ein Beispiel der Eingabe und/oder Ausgabe im Konstruktor für eines der definierten Typobjekte bereit, und der Typ und das Beispiel werden zur automatischen Erstellung des Schemas verwendet. Azure Machine Learning Service erstellt dann während der Bereitstellung eine [OpenAPI](https://swagger.io/docs/specification/about/)-Spezifikation (Swagger) für den Webdienst.

Folgende Typen werden derzeit unterstützt:

* `pandas`
* `numpy`
* `pyspark`
* Python-Standardobjekt

Um Schemagenerierung zu verwenden, schließen Sie das `inference-schema`-Paket in Ihre Conda-Umgebungsdatei ein. Das folgende Beispiel verwendet `[numpy-support]`, da das Eingangsskript einen NumPy-Parametertyp verwendet: 

#### <a name="example-dependencies-file"></a>Beispieldatei für Abhängigkeiten
Das folgende YAML-Beispiel zeigt eine Conda-Abhängigkeitsdatei für den Rückschluss.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

Wenn Sie automatische Schemagenerierung verwenden möchten, **muss** Ihr Eingangsskript die `inference-schema`-Pakete importieren. 

Definieren Sie in den Variablen `input_sample` und `output_sample` das Format für das Ein- und Ausgabebeispiel. (Die Variablen stellen das Anforderungs- und das Antwortformat für den Webdienst dar.) Verwenden Sie diese Beispiele in den Decorator-Elementen der Ein- und Ausgabefunktion für die Funktion `run()`. Das Scikit-learn-Beispiel unten verwendet Schemagenerierung.

> [!TIP]
> Verwenden Sie nach der Bereitstellung des Diensts die Eigenschaft `swagger_uri`, um das JSON-Schemadokument abzurufen.

#### <a name="example-entry-script"></a>Beispiel für ein Eingangsskript

Das folgende Beispiel zeigt, wie JSON-Daten akzeptiert und zurückgegeben werden:

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>Beispielskript mit Wörterbucheingabe (Unterstützung der Nutzung von Daten aus Power BI)

Im folgende Beispiel wird veranschaulicht, wie Eingabedaten unter Verwendung von Datenrahmen als Wörterbuch des Typs <Schlüssel: Wert> definiert werden. Diese Methode wird für die Nutzung des von Power BI bereitgestellten Webdiensts unterstützt ([erfahren Sie mehr über den Webdienst von Power BI](https://docs.microsoft.com/en-us/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType

def init():
    global model
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
Weitere Beispielskripts finden Sie in den folgenden Beispielen:

* PyTorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* Bewertung anhand von Binärdaten: [Nutzen eines Webdiensts](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. Definieren der Rückschlusskonfiguration

Die Rückschlusskonfiguration beschreibt, wie das Modell zum Treffen von Vorhersagen konfiguriert wird. Im folgenden Beispiel wird veranschaulicht, wie eine Rückschlusskonfiguration erstellt wird:

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

In diesem Beispiel enthält die Konfiguration die folgenden Elemente:

* Ein Verzeichnis, das Ressourcen enthält, die zum Ziehen von Rückschlüssen erforderlich sind
* Die Angabe, dass für dieses Modell Python erforderlich ist.
* Das [Eingangsskript](#script), das zum Verarbeiten von an den bereitgestellten Dienst gesendeten Webanforderungen verwendet wird.
* Die Conda-Datei zur Beschreibung der Python-Pakete, die zum Ziehen von Rückschlüssen erforderlich sind.

Weitere Informationen zu InferenceConfig-Funktionen finden Sie im Abschnitt [Erweiterte Konfiguration](#advanced-config).

### <a name="3-define-your-deployment-configuration"></a>3. Definieren der Bereitstellungskonfiguration

Bevor Sie die Bereitstellung ausführen, müssen Sie die Bereitstellungskonfiguration definieren. Die Bereitstellungskonfiguration ist spezifisch für das Computeziel, das den Webdienst hosten wird. Beispielsweise müssen Sie bei einer lokalen Bereitstellung den Port angeben, an dem der Dienst Anforderungen akzeptiert.

Möglicherweise müssen Sie die Computeressourcen auch erstellen. Dies ist beispielsweise der Fall, wenn Sie noch keinen Azure Kubernetes Service für Ihren Arbeitsbereich eingerichtet haben.

Die folgende Tabelle enthält ein Beispiel für das Erstellen einer Bereitstellungskonfiguration für jedes Computeziel:

| Computeziel | Beispiel für eine Bereitstellungskonfiguration |
| ----- | ----- |
| Lokal | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

Die folgenden Abschnitte zeigen, wie Sie die Bereitstellungskonfiguration erstellen und sie dann zum Bereitstellen des Webdiensts verwenden.

## <a name="deploy-to-target"></a>Bereitstellen auf dem Ziel

### <a id="local"></a> Lokale Bereitstellung

Um eine lokale Bereitstellung durchzuführen, müssen Sie **Docker** auf Ihrem lokalen Computer installiert haben.

+ **Verwenden des SDK**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Verwenden der CLI**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

### <a id="aci"></a> Azure Container Instances (DEVTEST)

Verwenden Sie Azure Container Instances für die Bereitstellung Ihrer Modelle als Webdienst, wenn mindestens eine der folgenden Bedingungen zutrifft:
- Sie müssen Ihr Modell schnell bereitstellen und überprüfen.
- Sie testen ein Modell in der Entwicklungsphase. 

Um Kontingente und die Regionsverfügbarkeit für ACI anzuzeigen, lesen Sie den Artikel [Kontingente und Regionsverfügbarkeit für Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

+ **Verwenden des SDK**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **Verwenden der CLI**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```


+ **Verwenden von VS Code**

  Zum [Bereitstellen Ihrer Modelle mit VS Code](how-to-vscode-tools.md#deploy-and-manage-models) müssen Sie für Tests nicht vorab einen ACI-Container erstellen, da ACI-Container dynamisch erstellt werden.

Weitere Informationen finden Sie in der Referenzdokumentation für die Klassen [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) und [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py).

### <a id="aks"></a>Azure Kubernetes Service (ENTWICKLUNG/TESTS und PRODUKTION)

Sie können einen vorhandenen AKS-Cluster verwenden oder mithilfe des Azure Machine Learning-SDK, der CLI oder des Azure-Portals einen neuen erstellen.

<a id="deploy-aks"></a>

Falls Sie bereits einen AKS-Cluster angefügt haben, können Sie dafür die Bereitstellung durchführen. Führen Sie die Schritte zum <a href="#create-attach-aks">Erstellen eines neuen AKS-Clusters</a> aus, wenn Sie noch keinen AKS-Cluster erstellt oder angefügt haben.

+ **Verwenden des SDK**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **Verwenden der CLI**

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **Verwenden von VS Code**

  Sie können auch die [Bereitstellung für AKS über die VS Code-Erweiterung](how-to-vscode-tools.md#deploy-and-manage-models) wählen, aber in diesem Fall müssen Sie vorab AKS-Cluster konfigurieren.

Weitere Informationen zur AKS-Bereitstellung und zur automatischen Skalierung finden Sie im Referenzartikel zu [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice).

#### Erstellen eines neuen AKS-Clusters<a id="create-attach-aks"></a>
**Geschätzter Zeitaufwand:** Ca. fünf Minuten.

Das Erstellen oder Anfügen eines AKS-Clusters ist ein für Ihren Arbeitsbereich einmaliger Vorgang. Sie können diesen Cluster für mehrere Bereitstellungen wiederverwenden. Wenn Sie den Cluster oder die Ressourcengruppe löschen, die ihn enthält, müssen Sie bei der nächsten Bereitstellung einen neuen Cluster erstellen. Sie können an Ihren Arbeitsbereich mehrere AKS-Cluster anfügen.

Falls Sie einen AKS-Cluster für Entwicklung, Validierung und Tests erstellen möchten, legen Sie `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` fest, wenn Sie [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) verwenden. Ein mit dieser Einstellung erstellter Cluster hat nur einen Knoten.

> [!IMPORTANT]
> Bei Festlegen von `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` wird ein AKS-Cluster erstellt, der nicht für die Verarbeitung von Produktionsdatenverkehr geeignet ist. Rückschlusszeiten können länger sein als bei einem Cluster, der für die Produktion angelegt wurde. Für Entwicklungs- und Testcluster wird zudem keine Fehlertoleranz garantiert.
>
> Wir empfehlen, dass Cluster, die für Entwicklung und Tests erstellt werden, mindestens zwei virtuelle CPUs aufweisen.

Das folgende Beispiel zeigt, wie ein neuer Azure Kubernetes Service-Cluster erstellt wird:

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Weitere Informationen zum Erstellen eines AKS-Clusters außerhalb des Azure Machine Learning SDK finden Sie in den folgenden Artikeln:
* [az aks create](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Schnellstart: Bereitstellen eines AKS-Clusters (Azure Kubernetes Service) über das Azure-Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Weitere Informationen zum Parameter `cluster_purpose` finden Sie in der Referenz zu [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py).

> [!IMPORTANT]
> Wenn Sie für [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) benutzerdefinierte Werte für agent_count und vm_size auswählen, müssen Sie sicherstellen, dass agent_count multipliziert mit vm_size mindestens 12 virtuellen CPUs entspricht. Wenn Sie beispielsweise für vm_size „Standard_D3_v2“ verwenden, das 4 virtuelle CPUs hat, müssen Sie für agent_count einen Wert von mindestens 3 wählen.

**Geschätzter Zeitaufwand**: Ca. 20 Minuten.

#### <a name="attach-an-existing-aks-cluster"></a>Anfügen eines vorhandenen AKS-Clusters

Wenn Sie in Ihrem Azure-Abonnement bereits über einen AKS-Cluster verfügen und dieser die Version 1.12.* hat, können Sie diesen für die Bereitstellung Ihres Image verwenden.

> [!WARNING]
> Wenn Sie einen AKS-Cluster an einen Arbeitsbereich anfügen, können Sie durch Festlegen des Parameters `cluster_purpose` bestimmen, wie Sie den Cluster verwenden möchten.
>
> Wenn Sie den Parameter `cluster_purpose` nicht bzw. `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` festlegen, muss der Cluster über mindestens 12 virtuelle CPUs verfügen.
>
> Wenn Sie `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` festlegen, muss der Cluster nicht über 12 virtuelle CPUs verfügen. Ein Cluster, der für Entwicklung/Tests konfiguriert ist, eignet sich jedoch nicht für Produktionsdatenverkehr und kann Rückschlusszeiten erhöhen.

Der folgende Code veranschaulicht das Anfügen eines vorhandenen AKS-Clusters der Version 1.12.## an Ihren Arbeitsbereich:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

Weitere Informationen zu `attack_configuration()` finden Sie in der Referenz zu [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-).

Weitere Informationen zum Parameter `cluster_purpose` finden Sie in der Referenz zu [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py).

## <a name="consume-web-services"></a>Nutzen von Webdiensten

Jeder bereitgestellte Webdienst stellt eine REST-API zur Verfügung, sodass Sie Clientanwendungen in einer Reihe verschiedener Programmiersprachen erstellen können. Wenn Sie die Authentifizierung für Ihren Dienst aktiviert haben, müssen Sie einen Dienstschlüssel als Token in Ihrem Anforderungsheader angeben.

### <a name="request-response-consumption"></a>Nutzung von Anforderung/Antwort

Dies ist ein Beispiel, wie Sie Ihren Dienst in Python aufrufen können:
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Weitere Informationen finden Sie unter [Erstellen von Clientanwendungen zur Nutzung von Webdiensten](how-to-consume-web-service.md).


### <a id="azuremlcompute"></a> Batchrückschluss
Azure Machine Learning Compute-Ziele werden von Azure Machine Learning Service erstellt und verwaltet. Sie können für Batchvorhersagen über Azure Machine Learning-Pipelines verwendet werden.

Eine exemplarische Vorgehensweise zu Batchrückschlüssen mit Azure Machine Learning Compute finden Sie im Artikel [Ausführen von Batchvorhersagen](how-to-run-batch-predictions.md).

### <a id="iotedge"></a> IoT Edge-Rückschluss
Unterstützung für die Bereitstellung auf Edge-Geräten befindet sich in der Vorschauphase. Weitere Informationen finden Sie im Artikel [Bereitstellen von Azure Machine Learning als IoT Edge-Modul](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).


## <a id="update"></a> Aktualisieren von Webdiensten

Wenn Sie ein neues Modell erstellen, müssen Sie jeden Dienst, der das neue Modell verwenden soll, manuell aktualisieren. Verwenden Sie die Methode `update`, um den Webdienst zu aktualisieren. Der folgende Code veranschaulicht, wie Sie den Webdienst so aktualisieren, dass er ein neues Modell verwendet:

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

<a id="advanced-config"></a>

## <a name="advanced-settings"></a>Erweiterte Einstellungen 

**<a id="customimage"></a> Verwenden eines benutzerdefinierten Basisimages**

Intern erstellt InferenceConfig ein Docker-Image, das das Modell und andere für den Dienst benötigte Ressourcen enthält. Wenn keine Angabe erfolgt, wird ein Basisstandardimage verwendet.

Wenn Sie ein Image erstellen, das mit Ihrer Rückschlusskonfiguration verwendet werden soll, muss das Image die folgenden Anforderungen erfüllen:

* Ubuntu 16.04 oder höher.
* Conda 4.5.# oder höher.
* Python 3.5# oder 3.6#.

Legen Sie bei Verwendung eines benutzerdefinierten Images die Eigenschaft `base_image` für die Rückschlusskonfiguration auf die Adresse des Images fest. Im folgenden Beispiel wird veranschaulicht, wie Sie ein Image aus einer öffentlichen und einer privaten Azure Container Registry verwenden:

```python
# use an image available in public Container Registry without authentication
inference_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
inference_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
inference_config.base_image_registry.address = "myregistry.azurecr.io"
inference_config.base_image_registry.username = "username"
inference_config.base_image_registry.password = "password"
```

Die folgenden Image-URIs beziehen sich auf von Microsoft bereitgestellte Images und können ohne Angabe eines Benutzernamens oder Kennwortwerts verwendet werden:

* `mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-cuda10.0-cudnn7`
* `mcr.microsoft.com/azureml/onnxruntime:v0.4.0-tensorrt19.03`

Um diese Images zu verwenden, legen Sie das `base_image` auf den URI aus der Liste oben fest. Setzen Sie `base_image_registry.address` auf `mcr.microsoft.com`.

> [!IMPORTANT]
> Microsoft-Images, die CUDA oder TensorRT verwenden, dürfen nur für Microsoft Azure-Dienste verwendet werden.

Weitere Informationen zum Hochladen eines eigenen Images in eine Azure Container Registry finden Sie unter [Pushen des ersten Images in eine private Docker-Containerregistrierung](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli).

Wenn Ihr Modell in Azure Machine Learning Compute mit __Version 1.0.22 oder höher__ des Azure Machine Learning-SDK trainiert wird, wird während des Trainings ein Image erstellt. Die Verwendung dieses Images wird im folgenden Beispiel veranschaulicht:

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Verwenden Sie zum Löschen eines bereitgestellten Webdiensts `service.delete()`.
Verwenden Sie zum Löschen eines registrierten Modells `model.delete()`.

Weitere Informationen finden Sie in der Referenzdokumentation zu [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) und [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--).

## <a name="next-steps"></a>Nächste Schritte
* [Problembehandlung von Bereitstellungen von Azure Machine Learning Service mit AKS und ACI](how-to-troubleshoot-deployment.md)
* [Secure Azure Machine Learning web services with SSL (Sichere Azure Machine Learning-Webdienste mit SSL)](how-to-secure-web-service.md)
* [Consume a ML Model deployed as a web service (Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells)](how-to-consume-web-service.md).
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)

