---
title: Bereitstellen eines Modells für die Verwendung mit Cognitive Search
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Azure Machine Learning zum Bereitstellen eines Modells für die Nutzung mit Cognitive Search verwenden. Das Modell wird als benutzerdefinierte Skill verwendet, um die Suchoberfläche zu erweitern.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.reviewer: larryfr
ms.date: 06/11/2020
ms.custom: deploy
ms.openlocfilehash: e1eebf88b72c87ce9db02760c5c44a0aa25c57cc
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305923"
---
# <a name="deploy-a-model-for-use-with-cognitive-search"></a>Bereitstellen eines Modells für die Verwendung mit Cognitive Search


In diesem Artikel wird die Verwendung von Azure Machine Learning zum Bereitstellen eines Modells für die Nutzung mit [Azure Cognitive Search](../search/search-what-is-azure-search.md) erläutert.

Cognitive Search führt die Inhaltsverarbeitung heterogener Inhalte durch, damit diese von Personen oder Anwendungen abgefragt werden können. Dieser Prozess kann mithilfe eines Modells erweitert werden, das von Azure Machine Learning bereitgestellt wird.

Azure Machine Learning kann ein trainiertes Modell als Webdienst bereitstellen. Der Webdienst wird dann in einen _Cognitive Search-Skill_ eingebettet, der Teil der Verarbeitungspipeline wird.

> [!IMPORTANT]
> Die Informationen in diesem Artikel gelten für die Bereitstellung des Modells. Der Artikel enthält Informationen zu den unterstützten Bereitstellungskonfigurationen, durch die das Modell von Cognitive Search verwendet werden kann.
>
> Informationen zum Konfigurieren von Cognitive Search für die Verwendung des bereitgestellten Modells finden Sie im Tutorial [Erstellen und Bereitstellen eines benutzerdefinierten Skills mit Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md).
>
> Das Beispiel, auf dem das Tutorial basiert, finden Sie unter [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).

Wenn Sie ein Modell für die Verwendung mit Azure Cognitive Search bereitstellen, muss die Bereitstellung die folgenden Anforderungen erfüllen:

* Verwenden Sie Azure Kubernetes Service, um das Modell für Rückschlüsse zu hosten.
* Aktivieren Sie TLS (Transport Layer Security) für Azure Kubernetes Service. TLS wird zum Sichern der HTTPS-Kommunikation zwischen Cognitive Search und dem bereitgestellten Modell verwendet.
* Das Einstiegsskript muss das `inference_schema`-Paket verwenden, um ein OpenAPI-Schema (Swagger) für den Dienst zu generieren.
* Das Einstiegsskript muss auch JSON-Daten als Eingabe akzeptieren und JSON-Daten als Ausgabe generieren.


## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Eine Python-Entwicklungsumgebung, in der das Azure Machine Learning SDK installiert ist. Weitere Informationen finden Sie unter [Install the Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py) (Installieren des Azure Machine Learning SDK für Python).  

* Ein registriertes Modell. Wenn Sie über kein Modell verfügen, verwenden Sie das Beispielnotebook unter [https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).

* Ein allgemeines Verständnis dafür, [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

## <a name="connect-to-your-workspace"></a>Herstellen einer Verbindung mit Ihrem Arbeitsbereich

Ein Azure Machine Learning-Arbeitsbereich ist ein zentraler Ort für die Arbeit mit allen Artefakten, die Sie bei der Verwendung von Azure Machine Learning erstellen. Im Arbeitsbereich wird der Verlauf aller Trainingsläufe gespeichert, einschließlich Protokollen, Metriken, Ausgabe und einer Momentaufnahme Ihrer Skripts.

Verwenden Sie den folgenden Code, um eine Verbindung mit einem vorhandenen Arbeitsbereich herzustellen:

> [!IMPORTANT]
> In diesem Codeausschnitt wird davon ausgegangen, dass die Arbeitsbereichskonfiguration im aktuellen Verzeichnis oder in dessen übergeordnetem Verzeichnis gespeichert wird. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Azure Machine Learning-Arbeitsbereichen im Azure-Portal](how-to-manage-workspace.md). Weitere Informationen zum Speichern der Konfiguration in einer Datei finden Sie unter [Konfigurieren einer Entwicklungsumgebung für Azure Machine Learning](how-to-configure-environment.md#workspace).

```python
from azureml.core import Workspace

try:
    # Load the workspace configuration from local cached inffo
    ws = Workspace.from_config()
    print(ws.name, ws.location, ws.resource_group, ws.location, sep='\t')
    print('Library configuration succeeded')
except:
    print('Workspace not found')
```

## <a name="create-a-kubernetes-cluster"></a>Erstellen eines Kubernetes-Clusters

**Geschätzter Zeitaufwand** : Ca. 20 Minuten.

Bei einem Kubernetes-Cluster handelt es sich um eine Reihe von VM-Instanzen (werden Knoten genannt), die zum Ausführen von Containeranwendungen verwendet werden.

Wenn Sie ein Modell aus Azure Machine Learning für Azure Kubernetes Service bereitstellen, werden das Modell und alle Ressourcen, die zum Hosten als Webdienst erforderlich sind, in einen Docker-Container gepackt. Dieser Container wird dann auf dem Cluster bereitgestellt.

Der folgende Code veranschaulicht, wie Sie einen neuen AKS-Cluster (Azure Kubernetes Service) für Ihren Arbeitsbereich erstellen:

> [!TIP]
> Sie können einen vorhandenen Azure Kubernetes Service-Cluster auch an Ihren Azure Machine Learning-Arbeitsbereich anfügen. Weitere Informationen finden Sie unter [Bereitstellen eines Modells in einem Azure Kubernetes Service-Cluster](how-to-deploy-azure-kubernetes-service.md).

> [!IMPORTANT]
> Beachten Sie, dass der Code die `enable_ssl()`-Methode verwendet, um TLS (Transport Layer Security) für den Cluster zu aktivieren. Dies ist erforderlich, wenn Sie das bereitgestellte Modell aus Cognitive Services verwenden möchten.

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Create or attach to an AKS inferencing cluster

# Create the provisioning configuration with defaults
prov_config = AksCompute.provisioning_configuration()

# Enable TLS (sometimes called SSL) communications
# Leaf domain label generates a name using the formula
#  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
#  where "######" is a random series of characters
prov_config.enable_ssl(leaf_domain_label = "contoso")

cluster_name = 'amlskills'
# Try to use an existing compute target by that name.
# If one doesn't exist, create one.
try:
    
    aks_target = ComputeTarget(ws, cluster_name)
    print("Attaching to existing cluster")
except Exception as e:
    print("Creating new cluster")
    aks_target = ComputeTarget.create(workspace = ws, 
                                  name = cluster_name, 
                                  provisioning_configuration = prov_config)
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Azure berechnet Ihnen Gebühren, solange der AKS-Cluster vorhanden ist. Stellen Sie sicher, dass Sie Ihren AKS-Cluster löschen, wenn Sie ihn nicht mehr benötigen.

Weitere Informationen zum Verwenden von AKS mit Azure Machine Learning finden Sie unter [Bereitstellen in Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md).

## <a name="write-the-entry-script"></a>Schreiben des Eingangsskripts

Das Eingabeskript empfängt Daten, die an den Webdienst übermittelt wurden, übergibt sie an das Modell und gibt die Bewertungsergebnisse zurück. Das folgende Skript lädt das Modell beim Start und verwendet es dann zum Bewerten von Daten. Diese Datei wird manchmal als `score.py` bezeichnet.

> [!TIP]
> Das Eingabeskript ist für Ihr Modell spezifisch. Das Skript muss beispielsweise das Framework kennen, das bei Ihrem Modell, den Datenformaten usw. verwendet werden soll.

> [!IMPORTANT]
> Wenn Sie planen, das bereitgestellte Modell aus Azure Cognitive Services zu verwenden, müssen Sie das `inference_schema`-Paket verwenden, um die Schemagenerierung für die Bereitstellung zu aktivieren. Dieses Paket stellt Decorator-Elemente bereit, mit denen Sie das Eingabe- und Ausgabedatenformat für den Webdienst definieren können, der den Rückschluss mithilfe des Modells ausführt.

```python
from azureml.core.model import Model
from nlp_architect.models.absa.inference.inference import SentimentInference
from spacy.cli.download import download as spacy_download
import traceback
import json
# Inference schema for schema discovery
from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.standard_py_parameter_type import StandardPythonParameterType

def init():
    """
    Set up the ABSA model for Inference  
    """
    global SentInference
    spacy_download('en')
    aspect_lex = Model.get_model_path('hotel_aspect_lex')
    opinion_lex = Model.get_model_path('hotel_opinion_lex') 
    SentInference = SentimentInference(aspect_lex, opinion_lex)

# Use inference schema decorators and sample input/output to
# build the OpenAPI (Swagger) schema for the deployment
standard_sample_input = {'text': 'a sample input record containing some text' }
standard_sample_output = {"sentiment": {"sentence": "This place makes false booking prices, when you get there, they say they do not have the reservation for that day.", 
                                        "terms": [{"text": "hotels", "type": "AS", "polarity": "POS", "score": 1.0, "start": 300, "len": 6}, 
                                                  {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 295, "len": 4}]}}
@input_schema('raw_data', StandardPythonParameterType(standard_sample_input))
@output_schema(StandardPythonParameterType(standard_sample_output))    
def run(raw_data):
    try:
        # Get the value of the 'text' field from the JSON input and perform inference
        input_txt = raw_data["text"]
        doc = SentInference.run(doc=input_txt)
        if doc is None:
            return None
        sentences = doc._sentences
        result = {"sentence": doc._doc_text}
        terms = []
        for sentence in sentences:
            for event in sentence._events:
                for x in event:
                    term = {"text": x._text, "type":x._type.value, "polarity": x._polarity.value, "score": x._score,"start": x._start,"len": x._len }
                    terms.append(term)
        result["terms"] = terms
        print("Success!")
        # Return the results to the client as a JSON document
        return {"sentiment": result}
    except Exception as e:
        result = str(e)
        # return error message back to the client
        print("Failure!")
        print(traceback.format_exc())
        return json.dumps({"error": result, "tb": traceback.format_exc()})
```

Weitere Informationen zu Eingabeskripts finden Sie unter [Bereitstellung: wo und wie](how-to-deploy-and-where.md).

## <a name="define-the-software-environment"></a>Definieren der Softwareumgebung

Die Umgebungsklasse wird verwendet, um die Python-Abhängigkeiten für den Dienst zu definieren. Sie enthält Abhängigkeiten, die sowohl für das Modell als auch das Eingabeskript erforderlich sind. In diesem Beispiel werden Pakete aus dem regulären pypi-Index und aus einem GitHub-Repository installiert. 

```python
from azureml.core.conda_dependencies import CondaDependencies 
from azureml.core import Environment

conda = None
pip = ["azureml-defaults", "azureml-monitoring", 
       "git+https://github.com/NervanaSystems/nlp-architect.git@absa", 'nlp-architect', 'inference-schema',
       "spacy==2.0.18"]

conda_deps = CondaDependencies.create(conda_packages=None, pip_packages=pip)

myenv = Environment(name='myenv')
myenv.python.conda_dependencies = conda_deps
```

Weitere Informationen zu Umgebungen finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md).

## <a name="define-the-deployment-configuration"></a>Definieren der Bereitstellungskonfiguration

Die Bereitstellungskonfiguration definiert die Azure Kubernetes Service-Hostumgebung, die zum Ausführen des Webdiensts verwendet wird.

> [!TIP]
> Wenn Sie sich nicht sicher sind, welche Anforderungen es an den Arbeitsspeicher, die CPU oder die GPU im Rahmen Ihrer Bereitstellung gibt, können Sie diese mithilfe der Profilerstellung herausfinden. Weitere Informationen finden Sie unter [Vorbereiten der Bereitstellung](how-to-deploy-and-where.md).

```python
from azureml.core.model import Model
from azureml.core.webservice import Webservice
from azureml.core.image import ContainerImage
from azureml.core.webservice import AksWebservice, Webservice

# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.

aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                       autoscale_min_replicas=1, 
                                                       autoscale_max_replicas=3, 
                                                       autoscale_refresh_seconds=10, 
                                                       autoscale_target_utilization=70,
                                                       auth_enabled=True, 
                                                       cpu_cores=1, memory_gb=2, 
                                                       scoring_timeout_ms=5000, 
                                                       replica_max_concurrent_requests=2, 
                                                       max_request_wait_time=5000)
```

Weitere Informationen finden Sie in der Referenzdokumentation zu [AksService.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-).

## <a name="define-the-inference-configuration"></a>Definieren der Rückschlusskonfiguration

Die Rückschlusskonfiguration verweist auf das Einstiegsskript und das Umgebungsobjekt:

```python
from azureml.core.model import InferenceConfig
inf_config = InferenceConfig(entry_script='score.py', environment=myenv)
```

Weitere Informationen finden Sie in der Referenzdokumentation zu [InferenceConfig](/python/api/azureml-core/azureml.core.model.inferenceconfig?preserve-view=true&view=azure-ml-py).

## <a name="deploy-the-model"></a>Bereitstellen des Modells

Stellen Sie dieses Modell in Ihrem AKS-Cluster bereit, und warten Sie, bis es den Dienst erstellt. In diesem Beispiel werden zwei registrierte Modelle aus der Registrierung geladen und in AKS bereitgestellt. Nach der Bereitstellung lädt die `score.py`-Datei in der Bereitstellung diese Modelle und verwendet sie, um Rückschlussvorgänge durchzuführen.

```python
from azureml.core.webservice import AksWebservice, Webservice

c_aspect_lex = Model(ws, 'hotel_aspect_lex')
c_opinion_lex = Model(ws, 'hotel_opinion_lex') 
service_name = "hotel-absa-v2"

aks_service = Model.deploy(workspace=ws,
                           name=service_name,
                           models=[c_aspect_lex, c_opinion_lex],
                           inference_config=inf_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target,
                           overwrite=True)

aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

Weitere Informationen finden Sie in der Referenzdokumentation zu [Model](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py).

## <a name="issue-a-sample-query-to-your-service"></a>Ausstellen einer Beispielabfrage für Ihren Dienst

Im folgenden Beispiel werden die Bereitstellungsinformationen verwendet, die im vorherigen Codeabschnitt in der Variablen `aks_service` gespeichert sind. Diese Variable wird verwendet, um die Bewertungs-URL und das Authentifizierungstoken abzurufen, die für die Kommunikation mit dem Dienst erforderlich sind:

```python
import requests
import json

primary, secondary = aks_service.get_keys()

# Test data
input_data = '{"raw_data": {"text": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend."}}'

# Since authentication was enabled for the deployment, set the authorization header.
headers = {'Content-Type':'application/json',  'Authorization':('Bearer '+ primary)} 

# Send the request and display the results
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)
print(resp.text)
```

Das vom Dienst zurückgegebene Ergebnis ähnelt dem folgenden JSON-Code:

```json
{"sentiment": {"sentence": "This is a nice place for a relaxing evening out with friends. The owners seem pretty nice, too. I have been there a few times including last night. Recommend.", "terms": [{"text": "place", "type": "AS", "polarity": "POS", "score": 1.0, "start": 15, "len": 5}, {"text": "nice", "type": "OP", "polarity": "POS", "score": 1.0, "start": 10, "len": 4}]}}
```

## <a name="connect-to-cognitive-search"></a>Herstellen einer Verbindung mit Cognitive Search

Informationen zum Verwenden dieses Modells aus Cognitive Search finden Sie im Tutorial [Erstellen und Bereitstellen eines benutzerdefinierten Skills mit Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md).

## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Wenn Sie den AKS-Cluster speziell für dieses Beispiel erstellt haben, löschen Sie Ihre Ressourcen nach Abschluss des Testvorgangs mit Cognitive Search.

> [!IMPORTANT]
> Azure berechnet Ihnen Gebühren basierend darauf, wie lange der AKS-Cluster bereitgestellt wird. Achten Sie darauf, ihn zu bereinigen, wenn Sie ihn nicht mehr nutzen.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Erstellen und Bereitstellen eines benutzerdefinierten Skills mit Azure Machine Learning](../search/cognitive-search-tutorial-aml-custom-skill.md)