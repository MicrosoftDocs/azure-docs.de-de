---
title: Modellbereitstellung mit hoher Leistung mit Triton (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihr Modell mit NVIDIA Triton Inference Server in Azure Machine Learning bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, devx-track-azurecli
ms.openlocfilehash: b47b8f9d9429f38fb8b5da6d8f9083736f230fcb
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458269"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Bereitstellung mit hoher Leistung mit Triton Inference Server (Vorschau) 

Erfahren Sie, wie Sie mit [NVIDIA Triton Inference Server](https://developer.nvidia.com/nvidia-triton-inference-server) die Leistung des für Modellrückschlüsse verwendeten Webdiensts verbessern können.

Eine der Möglichkeiten, ein Modell für Rückschlüsse bereitzustellen, ist als Webdienst. Beispielsweise eine Bereitstellung für Azure Kubernetes Service oder Azure Container Instances. Standardmäßig verwendet Azure Machine Learning für die Bereitstellung von Webdiensten ein *universelles* Webframework mit einem einzelnen Thread.

Triton ist ein Framework, das für *Rückschlüsse* optimiert ist. Es ermöglicht eine bessere Auslastung der GPUs und kostengünstigere Rückschlüsse. Auf der Serverseite fasst es eingehende Anforderungen in Batches zusammen und übermittelt diese Batches für Rückschlüsse. Die Batchverarbeitung lastet die GPU-Ressourcen besser aus und ist ein wesentlicher Bestandteil der Leistung von Triton.

> [!IMPORTANT]
> Die Verwendung von Triton für die Bereitstellung von Azure Machine Learning befindet sich derzeit in der __Vorschau__. Vorschaufunktionen werden möglicherweise nicht vom Kundensupport behandelt. Weitere Informationen finden Sie in den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!TIP]
> Die Codeausschnitte in diesem Dokument dienen der Veranschaulichung und zeigen möglicherweise keine vollständige Lösung. Funktionierenden Beispielcode finden Sie in den [End-to-End-Beispielen von Triton in Azure Machine Learning](https://github.com/Azure/azureml-examples/tree/main/tutorials).

## <a name="prerequisites"></a>Voraussetzungen

* Ein **Azure-Abonnement**. Wenn Sie keins besitzen, probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) aus.
* Vertrautheit mit der Vorgehensweise, [wie und wo ein Modell mit Azure Machine Learning bereitgestellt werden soll](how-to-deploy-and-where.md).
* Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/?view=azure-ml-py) **oder** die [Azure CLI](/cli/azure/?view=azure-cli-latest) und die [Machine Learning-Erweiterung](reference-azure-machine-learning-cli.md).
* Eine funktionierende Installation von Docker für lokale Tests. Weitere Informationen zum Installieren und Überprüfen von Docker finden Sie unter [Orientation and setup](https://docs.docker.com/get-started/) (Ausrichtung und Setup) in der Docker-Dokumentation.

## <a name="architectural-overview"></a>Übersicht über die Architektur

Bevor Sie versuchen, Triton für Ihr eigenes Modell zu verwenden, ist es wichtig zu verstehen, wie es mit Azure Machine Learning funktioniert und wie es im Vergleich zu einer Standardbereitstellung aussieht.

**Standardbereitstellung ohne Triton**

* Es werden mehrere [Gunicorn](https://gunicorn.org/)-Worker gestartet, um eingehende Anforderungen parallel zu bearbeiten.
* Diese Worker übernehmen die Vorverarbeitung, den Aufruf des Modells und die Nachbearbeitung. 
* Rückschlussanforderungen verwenden den __Bewertungs-URI__. Beispiel: `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagramm zur normalen Bereitstellungsarchitektur ohne Triton":::

### <a name="setting-the-number-of-workers"></a>Festlegen der Anzahl der Worker

Legen Sie die Umgebungsvariable `WORKER_COUNT` fest, um die Anzahl der Worker in Ihrer Bereitstellung zu bestimmen. Wenn Sie beispielsweise über ein [Environment](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py)-Objekt mit dem Namen `env` verfügen, können Sie die folgenden Schritte ausführen:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Dadurch wird Azure ML die von Ihnen angegebene Anzahl der Worker mitgeteilt, die gestartet werden sollen.


**Rückschlusskonfigurationsbereitstellung mit Triton**

* Es werden mehrere [Gunicorn](https://gunicorn.org/)-Worker gestartet, um eingehende Anforderungen parallel zu bearbeiten.
* Die Anforderungen werden an den **Triton-Server** weitergeleitet. 
* Triton verarbeitet Anforderungen in Batches, um die GPU-Auslastung zu maximieren.
* Der Client verwendet den __Bewertungs-URI__, um Anforderungen zu stellen. Beispiel: `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Rückschlusskonfigurationsbereitstellung mit Triton":::

Der Workflow für die Verwendung von Triton für Ihre Modellimplementierung lautet:

1. Überprüfen Sie, ob Triton Ihr Modell versorgen kann.
1. Vergewissern Sie sich, dass Sie Anforderungen an Ihr von Triton bereitgestelltes Modell senden können.
1. Integrieren Sie Ihren Triton-spezifischen Code in Ihre AML-Bereitstellung.

## <a name="verify-that-triton-can-serve-your-model"></a>Bestätigen, dass Triton das Modell verarbeiten kann

Führen Sie zunächst die folgenden Schritte aus, um zu überprüfen, ob der Triton Inference Server Ihr Modell verarbeiten kann.

### <a name="optional-define-a-model-config-file"></a>(Optional) Definieren einer Modellkonfigurationsdatei

Die Modellkonfigurationsdatei teilt Triton mit, wie viele Eingaben zu erwarten sind und in welchen Dimensionen diese Eingaben erfolgen sollen. Weitere Informationen zum Erstellen der Konfigurationsdatei finden Sie unter [Model configuration](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) (Modellkonfiguration) in der NVIDIA-Dokumentation.

> [!TIP]
> Wir verwenden die Option `--strict-model-config=false` beim Start von Triton Inference Server, was bedeutet, dass Sie für ONNX- oder TensorFlow-Modelle keine `config.pbtxt`-Datei bereitstellen müssen.
> 
> Weitere Informationen zu dieser Option finden Sie unter [Generated model configuration](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) (Generierte Modellkonfiguration) in der NVIDIA-Dokumentation.

### <a name="use-the-correct-directory-structure"></a>Verwenden der richtigen Verzeichnisstruktur

Wenn Sie ein Modell mit Azure Machine Learning registrieren, können Sie entweder einzelne Dateien oder eine Verzeichnisstruktur registrieren. Um Triton zu verwenden, muss die Modellregistrierung für eine Verzeichnisstruktur erfolgen, die ein Verzeichnis namens `triton` enthält. Die allgemeine Struktur dieses Verzeichnisses lautet wie folgt:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Diese Verzeichnisstruktur ist ein Triton-Modellrepository und ist für Ihre Modelle für die Arbeit mit Triton erforderlich. Weitere Informationen finden Sie unter [Triton Model Repositories](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) (Triton-Modellrepositorys) in der NVIDIA-Dokumentation.

### <a name="test-with-triton-and-docker"></a>Testen mit Triton und Docker

Sie können Docker zum Testen Ihres Modells verwenden, um sicherzustellen, dass es mit Triton ausgeführt werden kann. Die folgenden Befehle pullen den Triton-Container auf Ihren lokalen Computer und starten dann Triton Server:

1. Verwenden Sie den folgenden Befehl, um das Image für Triton Server auf Ihren lokalen Computer zu pullen:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Verwenden Sie den folgenden Befehl, um Triton Server zu starten. Ersetzen Sie `<path-to-models/triton>` durch den Pfad zum Triton-Modellrepository, das Ihre Modelle enthält:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Wenn Sie Windows verwenden, werden Sie möglicherweise aufgefordert, Netzwerkverbindungen für diesen Prozess zuzulassen, wenn Sie den Befehl zum ersten Mal ausführen. Wenn dies der Fall ist, wählen Sie das Zulassen des Zugriffs aus.

    Nach dem Start werden Informationen ähnlich dem folgenden Text in der Befehlszeile protokolliert:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    Die erste Zeile gibt die Adresse des Webdiensts an. In diesem Fall `0.0.0.0:8000`, was dasselbe ist wie `localhost:8000`.

1. Verwenden Sie ein Hilfsprogramm wie curl, um auf den Integritätsendpunkt zuzugreifen.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Die Ausgabe dieses Befehls sieht etwa wie folgt aus. Beachten Sie das `200 OK`. Dieser Status bedeutet, dass der Webserver aktiv ist.

    ```bash
    *   Trying 127.0.0.1:8000...
    * Connected to localhost (127.0.0.1) port 8000 (#0)
    > GET /v2/health/ready HTTP/1.1
    > Host: localhost:8000
    > User-Agent: curl/7.71.1
    > Accept: */*
    >
    * Mark bundle as not supporting multiuse
    < HTTP/1.1 200 OK
    HTTP/1.1 200 OK
    ```

Neben einer grundlegenden Integritätsprüfung können Sie einen Client erstellen, um Daten für Rückschlüsse an Triton zu senden. Weitere Informationen zum Erstellen eines Clients finden Sie in den [Clientbeispielen](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) in der NVIDIA-Dokumentation. Es gibt auch [Python-Beispiele im Triton GitHub](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples).

Weitere Informationen zum Ausführen von Triton mithilfe von Docker finden Sie unter [Ausführen von Triton auf einem System mit GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) und [Ausführen von Triton auf einem System ohne GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

### <a name="register-your-model"></a>Registrieren Ihres Modells

Nachdem Sie sich vergewissert haben, dass das Modell mit Triton funktioniert, registrieren Sie es mit Azure Machine Learning. Die Modellregistrierung speichert Ihre Modelldateien im Arbeitsbereich von Azure Machine Learning. Diese werden bei der Bereitstellung mit dem Python SDK und der Azure CLI verwendet.

Die folgenden Beispiele veranschaulichen das Registrieren der Modelle.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="verify-you-can-call-into-your-model"></a>Bestätigen, dass das Modell aufgerufen werden kann

Nachdem Sie überprüft haben, dass Triton das Modell verarbeiten kann, können Sie Code für die Vorverarbeitung und Nachbearbeitung hinzufügen, indem Sie ein _Einstiegsskript_ definieren. Diese Datei hat den Namen `score.py`. Weitere Informationen zu Einstiegsskripts finden Sie unter [Definieren eines Einstiegsskripts](how-to-deploy-and-where.md#define-an-entry-script).

Die beiden Hauptschritte sind die Initialisierung eines Triton-HTTP-Clients in Ihrer `init()`-Methode und der Aufruf dieses Clients in Ihrer `run()`-Funktion.

### <a name="initialize-the-triton-client"></a>Initialisieren des Triton-Clients

Beziehen Sie Code wie das folgende Beispiel in Ihre `score.py`-Datei ein. Triton in Azure Machine Learning erwartet, dass es über „Localhost“, Port 8000, adressiert wird. In diesem Fall befindet sich Localhost innerhalb des Docker-Images für diese Bereitstellung, d. h. es ist kein Port auf Ihrem lokalen Computer:

> [!TIP]
> Das pip-Paket `tritonhttpclient` ist in der kuratierten `AzureML-Triton`-Umgebung enthalten, sodass es nicht als pip-Abhängigkeit angegeben werden muss.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Ändern des Bewertungsskripts zum Aufrufen von Triton

Das folgende Beispiel zeigt, wie die Metadaten für das Modell dynamisch angefordert werden können:

> [!TIP]
> Sie können die Metadaten von Modellen, die mit Triton geladen wurden, dynamisch anfordern, indem Sie die `.get_model_metadata`-Methode des Triton-Clients verwenden. Ein Beispiel für ihre Verwendung finden Sie im [Beispiel-Notebook](https://aka.ms/triton-aml-sample).

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

## <a name="redeploy-with-an-inference-configuration"></a>Erneutes Bereitstellen mit einer Rückschlusskonfiguration

Eine Rückschlusskonfiguration gestattet Ihnen die Verwendung eines Einstiegsskripts sowie die Bereitstellung von Azure Machine Learning mithilfe des Python SDK oder der Azure CLI.

> [!IMPORTANT]
> Sie müssen die kuratierte `AzureML-Triton`-[Umgebung](./resource-curated-environments.md) angeben.
>
> Das Python-Codebeispiel klont `AzureML-Triton` in eine andere Umgebung namens `My-Triton`. Der Azure CLI-Code verwendet ebenfalls diese Umgebung. Weitere Informationen zum Klonen einer Umgebung finden Sie in der [Environment.Clone()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-)-Referenz.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

> [!TIP]
> Weitere Informationen zum Erstellen einer Rückschlusskonfiguration finden Sie unter dem [Rückschlusskonfigurationsschema](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Nachdem die Bereitstellung abgeschlossen ist, wird der Bewertungs-URI angezeigt. Für diese lokale Bereitstellung ist dies `http://localhost:6789/score`. Bei der Bereitstellung in der Cloud können Sie den CLI-Befehl [az ml service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) zum Abrufen des Bewertungs-URIs verwenden.

Informationen zum Erstellen eines Clients, der Rückschlussanforderungen an den Bewertungs-URI sendet, finden Sie unter [Nutzen eines als Webdienst bereitgestellten Modells](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie planen, den Azure Machine Learning-Arbeitsbereich weiterhin zu nutzen, aber den bereitgestellten Dienst entfernen möchten, verwenden Sie eine der folgenden Optionen:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen der End-to-End-Beispiele von Triton in Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Testen der [Triton-Clientbeispiele](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Lesen der [Triton Inference Server-Dokumentation](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [Problembehandlung bei einer fehlerhaften Bereitstellung](how-to-troubleshoot-deployment.md)
* [Bereitstellen für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)