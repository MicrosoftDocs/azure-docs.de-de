---
title: Lokale Ausführung und Bereitstellung
titleSuffix: Azure Machine Learning
description: In diesem Artikel wird beschrieben, wie Sie Ihren lokalen Computer als Ziel für das Trainieren, Debuggen oder Bereitstellen von Modellen verwenden, die in Azure Machine Learning erstellt wurden.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/20/2020
ms.topic: conceptual
ms.custom: how-to, deploy
ms.openlocfilehash: 71f393897dff266f1b0922a19eefd70cffea133d
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600352"
---
# <a name="deploy-models-trained-with-azure-machine-learning-on-your-local-machines"></a>Bereitstellen von mit Azure Machine Learning trainierten Modellen auf lokalen Computern 

In diesem Artikel wird beschrieben, wie Sie Ihren lokalen Computer als Ziel für das Trainieren oder Bereitstellen von Modellen verwenden, die in Azure Machine Learning erstellt wurden. Die Flexibilität von Azure Machine Learning ermöglicht die Verwendung der meisten Python-Frameworks für maschinelles Lernen. Lösungen für maschinelles Lernen umfassen im Allgemeinen komplexe Abhängigkeiten, die schwer zu duplizieren sein können. In diesem Artikel wird gezeigt, wie Sie einen Kompromiss zwischen vollständiger Kontrolle und Benutzerfreundlichkeit finden.

Beispiele für Szenarien für die lokale Bereitstellung:

* Schnelles Durchlaufen von Daten, Skripts und Modellen zu einem frühen Zeitpunkt in einem Projekt
* Debuggen und Problembehandlung in späteren Phasen
* Endgültige Bereitstellung auf vom Benutzer verwalteter Hardware

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Ein Modell und eine Umgebung. Wenn Sie über kein trainiertes Modell verfügen, können Sie die Modell- und Abhängigkeitsdateien verwenden, die in [diesem Tutorial](tutorial-train-models-with-aml.md) bereitgestellt werden.
- Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).
- Ein conda-Manager wie Anaconda oder Miniconda, wenn Sie die Paketabhängigkeiten von Azure Machine Learning spiegeln möchten
- Docker, wenn Sie eine Containerversion der Azure Machine Learning-Umgebung verwenden möchten

## <a name="prepare-your-local-machine"></a>Vorbereiten Ihres lokalen Computers

Die zuverlässigste Möglichkeit, ein Azure Machine Learning-Modell lokal auszuführen, ist durch Verwendung eines Docker-Images. Ein Docker-Image bietet eine isolierte Containerumgebung, die die Azure-Ausführungsumgebung mit Ausnahme von Hardwareproblemen dupliziert. Weitere Informationen zum Installieren und Konfigurieren von Docker für Entwicklungsszenarien finden Sie unter [Übersicht über die Remoteentwicklung mit Docker unter Windows](/windows/dev-environment/docker/overview).

Es ist möglich, einen Debugger an einen Prozess anzufügen, der in Docker ausgeführt wird. (Weitere Informationen finden Sie unter [Anfügen an einen ausgeführten Container](https://code.visualstudio.com/docs/remote/attach-container).) Es empfiehlt sich jedoch, Python-Code ohne Docker zu debuggen und zu durchlaufen. In diesem Szenario ist es wichtig, dass auf dem lokalen Computer dieselben Bibliotheken verwendet werden, die beim Ausführen des Experiments in Azure Machine Learning verwendet werden. Zum Verwalten von Python-Abhängigkeiten wird in Azure [conda](https://docs.conda.io/) verwendet. Auch wenn Sie die Umgebung mithilfe anderer Paket-Manager neu erstellen können, ist die Installation und Konfiguration von conda auf dem lokalen Computer die einfachste Möglichkeit zur Synchronisierung. 

## <a name="prepare-your-entry-script"></a>Vorbereiten des Einstiegsskripts

Auch wenn Sie Docker zum Verwalten des Modells und der Abhängigkeiten verwenden, muss das Python-Bewertungsskript lokal sein. Das Skript muss zwei Methoden enthalten:

- Eine `init()`-Methode, die keine Argumente aufweist und keine Rückgabe erzeugt 
- Eine `run()`-Methode, die eine Zeichenfolge im JSON-Format unterstützt und ein JSON-serialisierbares Objekt zurückgibt

Das Argument für die `run()`-Methode weist folgendes Format auf: 

```json
{
    "data": <model-specific-data-structure>
}
```

Das Objekt, das von der `run()`-Methode zurückgegeben wird, muss `toJSON() -> string` implementieren.

Das folgende Beispiel veranschaulicht, wie ein registriertes scikit-learn-Modell geladen und mit NumPy-Daten bewertet wird. Dieses Beispiel basiert auf dem Modell und den Abhängigkeiten aus [diesem Tutorial](tutorial-train-models-with-aml.md).

```python
import json
import numpy as np
import os
import pickle
import joblib

def init():
    global model
    # AZUREML_MODEL_DIR is an environment variable created during deployment.
    # It's the path to the model folder (./azureml-models/$MODEL_NAME/$VERSION).
    # For multiple models, it points to the folder containing all deployed models (./azureml-models).
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # Make prediction.
    y_hat = model.predict(data)
    # You can return any data type as long as it's JSON-serializable.
    return y_hat.tolist()
```

Weiterführende Beispiele, einschließlich der automatischen Swagger-Schemagenerierung und der Bewertung von Binärdaten (z. B. Bilder), finden Sie unter [Erweiterte Einstiegsskripterstellung](how-to-deploy-advanced-entry-script.md). 

## <a name="deploy-as-a-local-web-service-by-using-docker"></a>Bereitstellung als lokaler Webdienst mithilfe von Docker

Die einfachste Möglichkeit, die in Azure Machine Learning verwendete Umgebung zu replizieren, ist die Bereitstellung eines Webdiensts mithilfe von Docker. Wenn Docker auf dem lokalen Computer ausgeführt wird, gehen Sie wie folgt vor:

1. Stellen Sie eine Verbindung mit dem Azure Machine Learning-Arbeitsbereich her, in dem Ihr Modell registriert ist.
1. Erstellen Sie ein `Model`-Objekt, das das Modell darstellt.
1. Erstellen Sie ein `Environment`-Objekt, das die Abhängigkeiten enthält und die Softwareumgebung definiert, in der Ihr Code ausgeführt wird.
1. Erstellen Sie ein `InferenceConfig`-Objekt, das das Einstiegsskript mit `Environment` verknüpft.
1. Erstellen Sie ein `DeploymentConfiguration`-Objekt der Unterklasse `LocalWebserviceDeploymentConfiguration`.
1. Verwenden Sie `Model.deploy()`, um ein `Webservice`-Objekt zu erstellen. Mit dieser Methode wird das Docker-Image heruntergeladen und `Model`, `InferenceConfig` und `DeploymentConfiguration` zugeordnet.
1. Aktivieren Sie den `Webservice` mit `Webservice.wait_for_deployment()`.

Diese Schritte sind im folgenden Code dargestellt:

```python
from azureml.core.webservice import Webservice
from azure.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core import Workspace
from azureml.core.model import Model

ws = Workspace.from_config()
model = Model(ws, 'sklearn_mnist')


myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

deployment_config = LocalWebservice.deploy_configuration(port=6789)

local_service = Model.deploy(workspace=ws, 
                       name='sklearn-mnist-local', 
                       models=[model], 
                       inference_config=inference_config, 
                       deployment_config = deployment_config)

local_service.wait_for_deployment(show_output=True)
print(f"Scoring URI is : {local_service.scoring_uri}")
```

Der Aufruf von `Model.deploy()` kann einige Minuten dauern. Nach der anfänglichen Bereitstellung des Webdiensts ist es effizienter, die `update()`-Methode zu verwenden, anstatt von Grund auf neu zu beginnen. Weitere Informationen finden Sie unter [Aktualisieren eines bereitgestellten Webdiensts](how-to-deploy-update-web-service.md).

### <a name="test-your-local-deployment"></a>Testen der lokalen Bereitstellung

Wenn Sie das vorherige Bereitstellungsskript ausführen, wird der URI ausgegeben, an den Sie Daten per POST zur Bewertung senden können (z. B. `http://localhost:6789/score`). Im folgenden Beispiel wird ein Skript gezeigt, mit dem Beispieldaten mit dem lokal bereitgestellten Modell `"sklearn-mnist-local"` bewertet werden. Wenn das Modell ordnungsgemäß trainiert ist, leitet es ab, dass `normalized_pixel_values` als „2“ interpretiert werden soll. 

```python
import requests

normalized_pixel_values = "[\
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.7, 1.0, 1.0, 0.6, 0.4, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.9, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 0.8, 0.6, 0.7, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.2, 1.0, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.8, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 0.8, 0.1, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.3, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.1, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.3, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.8, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.9, 0.2, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.7, 1.0, 1.0, 0.6, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.9, 1.0, 0.9, 0.1, \
0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.8, 1.0, 1.0, 0.6, \
0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.3, 1.0, 1.0, 0.7, \
0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.1, 0.8, 1.0, 1.0, \
1.0, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.5, 1.0, 1.0, \
1.0, 0.7, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, 1.0, \
1.0, 1.0, 0.1, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0, \
1.0, 1.0, 1.0, 0.2, 0.1, 0.1, 0.1, 0.1, 0.0, 0.0, 0.0, 0.1, 0.1, 0.1, 0.6, 0.6, 0.6, 0.6, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.6, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.5, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.7, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 1.0, 0.7, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.5, 0.5, 0.5, 0.5, 0.7, 1.0, 1.0, 1.0, 0.6, 0.5, 0.5, 0.2, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, \
0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0]"

input_data = "{\"data\": [" + normalized_pixel_values + "]}"

headers = {'Content-Type': 'application/json'}

scoring_uri = "http://localhost:6789/score"
resp = requests.post(scoring_uri, input_data, headers=headers)

print("Should be predicted as '2'")
print("prediction:", resp.text)
```

## <a name="download-and-run-your-model-directly"></a>Herunterladen und direktes Ausführen des Modells

Die Verwendung von Docker zum Bereitstellen des Modells als Webdienst ist die gängigste Option. Sie können den Code jedoch auch direkt mithilfe von lokalen Python-Skripts ausführen. Sie benötigen zwei wichtige Komponenten: 

- das Modell
- die Abhängigkeiten, auf die sich das Modell stützt 

Sie können das Modell herunterladen:  

- Im Portal durch Auswählen der Registerkarte **Modelle**, des gewünschten Modells und dann auf der Seite **Details** der Option **Herunterladen**.
- Über die Befehlszeile mit `az ml model download`. (Weitere Informationen finden Sie unter [Herunterladen des Modells](/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext_azure_cli_ml_az_ml_model_download&preserve-view=false).)
- Mithilfe der `Model.download()`-Methode aus dem Python SDK. (Weitere Informationen finden Sie unter [Modellklasse](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#download-target-dir------exist-ok-false--exists-ok-none-&preserve-view=false).)

Bei einem Azure-Modell handelt es sich um ein oder mehrere serialisierte Python-Objekte, die als Python-Pickle-Datei (Erweiterung .pkl) gepackt sind. Der Inhalt der Pickle-Datei hängt von der Bibliothek für maschinelles Lernen oder der Technik ab, die zum Trainieren des Modells verwendet wird. Beispielsweise können Sie bei dem Modell aus dem Tutorial das Modell wie folgt laden:

```python
import pickle

with open('sklearn_mnist_model.pkl', 'rb') as f : 
    logistic_model = pickle.load(f, encoding='latin1')
```

Abhängigkeiten sind immer knifflig, insbesondere beim maschinellen Lernen, wo es häufig verwirrende spezifische Versionsanforderungen geben kann. Sie können eine Azure Machine Learning-Umgebung auf Ihrem lokalen Computer entweder als vollständige conda-Umgebung oder als Docker-Image neu erstellen, indem Sie die `build_local()`-Methode der `Environment`-Klasse verwenden: 

```python
ws = Workspace.from_config()
myenv = Environment.get(workspace=ws, name="tutorial-env", version="1")
myenv.build_local(workspace=ws, useDocker=False) #Creates conda environment.
```

Wenn Sie das Argument `useDocker` von `build_local()` auf `True` festlegen, wird mit der Funktion ein Docker-Image und keine conda-Umgebung erstellt. Wenn Sie mehr Kontrolle benötigen, können Sie die `save_to_directory()`-Methode von `Environment` verwenden, mit der Definitionsdateien vom Typ „conda_dependencies.yml“ und „azureml_environment.json“ geschrieben werden, die Sie optimieren und als Erweiterungsgrundlage verwenden können. 

Die `Environment`-Klasse enthält eine Reihe weiterer Methoden für die Synchronisierung von Umgebungen in Ihrer Computehardware, Ihrem Azure-Arbeitsbereich und Ihren Docker-Images. Weitere Informationen finden Sie unter [Umgebungsklasse](/python/api/azureml-core/azureml.core.environment(class)).

Nachdem Sie das Modell heruntergeladen und seine Abhängigkeiten gelöst haben, gibt es keine von Azure definierten Einschränkungen hinsichtlich der Durchführung der Bewertung, der Optimierung des Modells, der Verwendung des Lerntransfers usw. 

## <a name="upload-a-retrained-model-to-azure-machine-learning"></a>Hochladen eines neu trainierten Modells in Azure Machine Learning

Ein lokal trainiertes oder neu trainiertes Modell können Sie bei Azure registrieren. Nach der Registrierung können Sie es mit Azure Compute optimieren oder mithilfe von Azure-Features wie [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) oder [Triton Inference Server (Vorschau)](how-to-deploy-with-triton.md) bereitstellen.

Zur Verwendung mit dem Python SDK von Azure Machine Learning muss ein Modell als serialisiertes Python-Objekt im Pickle-Format (PKL-Datei) gespeichert werden. Außerdem muss es eine `predict(data)`-Methode implementieren, die ein als JSON-Code serialisierbares Objekt zurückgibt. Beispielsweise können Sie ein lokal trainiertes scikit-learn-Diabetes-Modell wie folgt speichern: 

```python
import joblib

from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge

dataset_x, dataset_y = load_diabetes(return_X_y=True)

sk_model = Ridge().fit(dataset_x, dataset_y)

joblib.dump(sk_model, "sklearn_regression_model.pkl")
```

Um das Modell in Azure verfügbar zu machen, können Sie die `register()`-Methode der `Model`-Klasse verwenden:

```python
from azureml.core.model import Model

model = Model.register(model_path="sklearn_regression_model.pkl",
                       model_name="sklearn_regression_model",
                       tags={'area': "diabetes", 'type': "regression"},
                       description="Ridge regression model to predict diabetes",
                       workspace=ws)
```

Das neu registrierte Modell finden Sie dann in Azure Machine Learning auf der Registerkarte **Modell**:

:::image type="content" source="media/how-to-deploy-local/registered-model.png" alt-text="Screenshot der Registerkarte „Modell“ in Azure Machine Learning mit einem hochgeladenen Modell":::

Weitere Informationen zum Hochladen und Aktualisieren von Modellen und Umgebungen finden Sie unter [Register model and deploy locally with advanced usages](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local-advanced.ipynb) (Registrieren des Modells und lokales Bereitstellen mit erweiterten Verwendungsmöglichkeiten).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Verwalten von Umgebungen finden Sie unter [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](how-to-use-environments.md).
- Weitere Informationen zum Zugreifen auf Daten in Ihrem Datenspeicher finden Sie unter [Herstellen einer Verbindung mit Speicherdiensten in Azure](how-to-access-data.md).
