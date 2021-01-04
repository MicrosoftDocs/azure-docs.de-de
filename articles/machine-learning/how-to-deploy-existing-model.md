---
title: Verwenden und Bereitstellen vorhandener Modelle
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie mit Azure Machine Learning Ihre außerhalb von Azure trainierten ML-Modelle in die Azure-Cloud bringen können. Danach stellen Sie das Modell als Webdienst oder IoT-Modul bereit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 6721357464c2a49331d9c02982841d36aa207cc6
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324979"
---
# <a name="deploy-your-existing-model-with-azure-machine-learning"></a>Bereitstellen Ihres vorhandenen Modells mit Azure Machine Learning


In diesem Artikel erfahren Sie, wie Sie ein Machine Learning-Modell, das Sie außerhalb von Azure Machine Learning trainiert haben, registrieren und bereitstellen können. Sie können als Webdienst oder auf einem IoT Edge-Gerät bereitgestellt werden.  Nach der Bereitstellung können Sie Ihr Modell überwachen und Datendrift in Azure Machine Learning erkennen. 

Weitere Informationen zu den in diesem Artikel verwendeten Konzepten und Begriffen finden Sie unter [Verwalten, Bereitstellen und Überwachen von Modellen für maschinelles Lernen](concept-model-management-and-deployment.md).

## <a name="prerequisites"></a>Voraussetzungen

* [Ein Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md)
  + Python-Beispiele gehen davon aus, dass die Variable `ws` auf Ihren Azure Machine Learning-Arbeitsbereich festgelegt ist. Weitere Informationen, wie Sie eine Verbindung mit einem Arbeitsbereich herstellen, finden Sie in der [Dokumentation zum Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#&preserve-view=trueworkspace).
  
  + CLI-Beispiele verwenden Platzhalter von `myworkspace` und `myresourcegroup`, die Sie durch den Namen Ihres Arbeitsbereichs und der Ressourcengruppe, die ihn enthält, ersetzen sollten.

* Das [Python-SDK für Azure Machine Learning](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py)  

* Die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) und die [Machine Learning-CLI-Erweiterung](reference-azure-machine-learning-cli.md).

* Ein trainiertes Modell. Das Modell muss dauerhaft in mindestens eine Datei in Ihrer Entwicklungsumgebung gespeichert werden. <br><br>Um die Registrierung eines trainierten Modells zu veranschaulichen, verwendet der Beispielcode in diesem Artikel die Modelle aus dem [Twitter-Stimmungsanalyseprojekt von Paolo Ripamonti](https://www.kaggle.com/paoloripamonti/twitter-sentiment-analysis).

## <a name="register-the-models"></a>Registrieren der Modelle

Die Registrierung eines Modells ermöglicht es Ihnen, Metadaten zu Modellen in Ihrem Arbeitsbereich zu speichern, zu versionieren und zu verfolgen. In den folgenden Python- und CLI-Beispielen enthält das Verzeichnis `models` die Dateien `model.h5`, `model.w2v`, `encoder.pkl` und `tokenizer.pkl`. In diesem Beispiel werden die im Verzeichnis `models` enthaltenen Dateien als neue Modellregistrierung mit dem Namen `sentiment` hochgeladen:

```python
from azureml.core.model import Model
# Tip: When model_path is set to a directory, you can use the child_paths parameter to include
#      only some of the files from the directory
model = Model.register(model_path = "./models",
                       model_name = "sentiment",
                       description = "Sentiment analysis model trained outside Azure Machine Learning",
                       workspace = ws)
```

Weitere Informationen finden Sie in der [Model.register()](/python/api/azureml-core/azureml.core.model%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueregister-workspace--model-path--model-name--tags-none--properties-none--description-none--datasets-none--model-framework-none--model-framework-version-none--child-paths-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none-)-Referenz.

```azurecli
az ml model register -p ./models -n sentiment -w myworkspace -g myresourcegroup
```

> [!TIP]
> Sie können auch `tags`- und `properties`-Wörterbuchobjekte zum registrierten Modell hinzufügen. Diese Werte können später verwendet werden, um ein bestimmtes Modell zu identifizieren. Beispiel: das verwendete Framework, Trainingsparameter usw.

Weitere Informationen finden Sie in der [az ml model register](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)-Referenz.


Weitere Informationen zur Modellregistrierung im Allgemeinen finden Sie unter [Verwalten, Bereitstellen und Überwachen von Modellen für maschinelles Lernen](concept-model-management-and-deployment.md).

## <a name="define-inference-configuration"></a>Definieren der Rückschlusskonfiguration

Die Rückschlusskonfiguration definiert die Umgebung, in der das bereitgestellte Modell ausgeführt wird. Die Rückschlusskonfiguration verweist auf die folgenden Entitäten, die zum Ausführen des Modells verwendet werden, wenn es bereitgestellt wird:

* Ein Einstiegsskript namens `score.py` lädt das Modell, wenn der bereitgestellte Dienst startet. Dieses Skript ist auch dafür verantwortlich, Daten zu empfangen, sie an das Modell weiterzugeben und dann eine Antwort zurückzugeben.
* Eine Azure Machine Learning-[Umgebung](how-to-use-environments.md). Mit einer Umgebung werden die Softwareabhängigkeiten definiert, die zum Ausführen des Modells und Eingabeskripts benötigt werden.

Im folgenden Beispiel wird veranschaulicht, wie das SDK genutzt wird, um eine Umgebung zu erstellen und dann mit einer Rückschlusskonfiguration zu verwenden:

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

# Create the environment
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Define the packages needed by the model and scripts
conda_dep.add_conda_package("tensorflow")
conda_dep.add_conda_package("numpy")
conda_dep.add_conda_package("scikit-learn")
# You must list azureml-defaults as a pip dependency
conda_dep.add_pip_package("azureml-defaults")
conda_dep.add_pip_package("keras")
conda_dep.add_pip_package("gensim")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep

inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
```

Weitere Informationen finden Sie in den folgenden Artikeln:

+ [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md)
+ [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py)-Referenz


Die CLI lädt die Rückschlusskonfiguration aus einer YAML-Datei:

```yaml
{
   "entryScript": "score.py",
   "runtime": "python",
   "condaFile": "myenv.yml"
}
```

Mit der CLI wird die Conda-Umgebung in der Datei `myenv.yml` definiert, auf die in der Rückschlusskonfiguration verwiesen wird. Der folgende YAML-Code gibt den Inhalt dieser Datei wieder:

```yaml
name: inference_environment
dependencies:
- python=3.6.2
- tensorflow
- numpy
- scikit-learn
- pip:
    - azureml-defaults
    - keras
    - gensim
```

Weitere Informationen zur Rückschlusskonfiguration finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

### <a name="entry-script-scorepy"></a>Einstiegsskript (score.py)

Das Eingabeskript weist nur zwei erforderliche Funktionen auf, `init()` und `run(data)`. Diese Funktionen werden verwendet, um den Dienst beim Start zu initialisieren und das Modell mit den von einem Client übergebenen Anforderungsdaten auszuführen. Der Rest des Skripts übernimmt das Laden und Ausführen der Modelle.

> [!IMPORTANT]
> Es gibt kein generisches Eingabeskript, das für alle Modelle funktioniert. Sie ist immer spezifisch für das verwendete Modell. Es muss verstehen, wie das Modell geladen wird, welches Datenformat das Modell erwartet und wie Daten mit dem Modell bewertet werden.

Der folgende Python-Code ist ein Beispiel für ein Eingabeskript (`score.py`):

```python
import os
import pickle
import json
import time
from keras.models import load_model
from keras.preprocessing.sequence import pad_sequences
from gensim.models.word2vec import Word2Vec

# SENTIMENT
POSITIVE = "POSITIVE"
NEGATIVE = "NEGATIVE"
NEUTRAL = "NEUTRAL"
SENTIMENT_THRESHOLDS = (0.4, 0.7)
SEQUENCE_LENGTH = 300

# Called when the deployed service starts
def init():
    global model
    global tokenizer
    global encoder
    global w2v_model

    # Get the path where the deployed model can be found.
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), './models')
    # load models
    model = load_model(model_path + '/model.h5')
    w2v_model = Word2Vec.load(model_path + '/model.w2v')

    with open(model_path + '/tokenizer.pkl','rb') as handle:
        tokenizer = pickle.load(handle)

    with open(model_path + '/encoder.pkl','rb') as handle:
        encoder = pickle.load(handle)

# Handle requests to the service
def run(data):
    try:
        # Pick out the text property of the JSON request.
        # This expects a request in the form of {"text": "some text to score for sentiment"}
        data = json.loads(data)
        prediction = predict(data['text'])
        #Return prediction
        return prediction
    except Exception as e:
        error = str(e)
        return error

# Determine sentiment from score
def decode_sentiment(score, include_neutral=True):
    if include_neutral:
        label = NEUTRAL
        if score <= SENTIMENT_THRESHOLDS[0]:
            label = NEGATIVE
        elif score >= SENTIMENT_THRESHOLDS[1]:
            label = POSITIVE
        return label
    else:
        return NEGATIVE if score < 0.5 else POSITIVE

# Predict sentiment using the model
def predict(text, include_neutral=True):
    start_at = time.time()
    # Tokenize text
    x_test = pad_sequences(tokenizer.texts_to_sequences([text]), maxlen=SEQUENCE_LENGTH)
    # Predict
    score = model.predict([x_test])[0]
    # Decode sentiment
    label = decode_sentiment(score, include_neutral=include_neutral)

    return {"label": label, "score": float(score),
       "elapsed_time": time.time()-start_at}  
```

Weitere Informationen zu Eingabeskripts finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="define-deployment"></a>Definieren der Bereitstellung

Das [Webdienst](/python/api/azureml-core/azureml.core.webservice?preserve-view=true&view=azure-ml-py)-Paket enthält die für die Bereitstellung verwendeten Klassen. Die von Ihnen verwendete Klasse bestimmt, wo das Modell bereitgestellt wird. Verwenden Sie z. B. zur Bereitstellung als Webdienst für Azure Kubernetes Service [AksWebService.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-), um die Bereitstellungskonfiguration zu erstellen.

Der folgende Python-Code definiert eine Bereitstellungskonfiguration für eine lokale Bereitstellung. Diese Konfiguration stellt das Modell als Webdienst auf Ihrem lokalen Computer bereit.

> [!IMPORTANT]
> Eine lokale Bereitstellung erfordert eine funktionierende Installation von [Docker](https://www.docker.com/) auf Ihrem lokalen Computer:

```python
from azureml.core.webservice import LocalWebservice

deployment_config = LocalWebservice.deploy_configuration()
```

Weitere Informationen finden Sie in der [LocalWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.localwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-port-none-)-Referenz.

Die CLI lädt die Bereitstellungskonfiguration aus einer YAML-Datei:

```YAML
{
    "computeType": "LOCAL"
}
```

Die Bereitstellung auf einem anderen Computeziel, z. B. Azure Kubernetes Service in der Azure Cloud, ist so einfach wie die Änderung der Bereitstellungskonfiguration. Weitere Informationen finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Das folgende Beispiel lädt Informationen über das registrierte Modell namens `sentiment` und stellt es dann als Dienst namens `sentiment` bereit. Während der Bereitstellung werden die Rückschluss- und die Bereitstellungskonfiguration verwendet, um die Dienstumgebung zu erstellen und zu konfigurieren:

```python
from azureml.core.model import Model

model = Model(ws, name='sentiment')
service = Model.deploy(ws, 'myservice', [model], inference_config, deployment_config)

service.wait_for_deployment(True)
print(service.state)
print("scoring URI: " + service.scoring_uri)
```

Weitere Informationen finden Sie in der [Model.deploy()](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)-Referenz.

Verwenden Sie den folgenden Befehl, um das Modell über die CLI bereitzustellen. Dieser Befehl stellt die Version 1 des registrierten Modells (`sentiment:1`) unter Verwendung der in den Dateien `inferenceConfig.json` und `deploymentConfig.json` gespeicherten Rückschluss- und Bereitstellungskonfiguration bereit:

```azurecli
az ml model deploy -n myservice -m sentiment:1 --ic inferenceConfig.json --dc deploymentConfig.json
```

Weitere Informationen finden Sie in der [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)-Referenz.

Weitere Informationen zur Bereitstellung finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

## <a name="request-response-consumption"></a>Nutzung von Anforderung/Antwort

Nach der Bereitstellung wird der Bewertungs-URI angezeigt. Dieser URI kann von Clients verwendet werden, um Anfragen an den Dienst zu senden. Das folgende Beispiel ist ein einfacher Python-Client, der Daten an den Dienst übermittelt und die Antwort anzeigt:

```python
import requests
import json

scoring_uri = 'scoring uri for your service'
headers = {'Content-Type':'application/json'}

test_data = json.dumps({'text': 'Today is a great day!'})

response = requests.post(scoring_uri, data=test_data, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

Weitere Informationen zur Verwendung des bereitgestellten Diensts finden Sie unter [Erstellen eines Clients](how-to-consume-web-service.md).

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
* [Erstellen eines Clients für ein bereitgestelltes Modell](how-to-consume-web-service.md)