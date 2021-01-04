---
title: Bereitstellen von ML-Modellen in Kubernetes Service
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre Azure Machine Learning-Modelle mithilfe von Azure Kubernetes Service als Webdienst bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, deploy, devx-track-azurecli
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: d7540066ccc0d3a62dbd4012eee100d8e8aea98f
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505085"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Bereitstellen eines Modells in einem Azure Kubernetes Service-Cluster

Erfahren Sie, wie Sie ein Modell mit Azure Machine Learning als Webdienst in Azure Kubernetes Service (AKS) bereitstellen. Azure Kubernetes Service ist gut für umfangreiche Produktionsbereitstellungen geeignet. Verwenden Sie Azure Kubernetes Service, wenn Sie eine oder mehrere der folgenden Funktionen benötigen:

- __Schnelle Antwortzeiten__
- __Autoskalierung__ des bereitgestellten Diensts
- __Logging__
- __Modelldatensammlung__
- __Authentifizierung__
- __TLS-Terminierung__
- Optionen für die __Hardwarebeschleunigung__, beispielsweise GPU und FPGA (Field-Programmable Gate Arrays)

Bei der Bereitstellung in Azure Kubernetes Service führen Sie die Bereitstellung in einem AKS-Cluster durch, der __mit Ihrem Arbeitsbereich verbunden ist__. Informationen zum Verbinden eines AKS-Clusters mit Ihrem Arbeitsbereich finden Sie unter [Erstellen und Anfügen eines Azure Kubernetes Service-Clusters](how-to-create-attach-kubernetes.md).

> [!IMPORTANT]
> Es wird empfohlen, Debugvorgänge vor der Bereitstellung im Webdienst lokal durchzuführen. Weitere Informationen finden Sie unter [Lokal debuggen](./how-to-troubleshoot-deployment-local.md).
>
> Weitere Informationen finden Sie auch unter Azure Machine Learning – [Bereitstellung auf lokalem Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

- Ein Machine Learning-Modell, das in Ihrem Arbeitsbereich registriert ist. Wenn Sie über kein registriertes Modell verfügen, finden Sie hier weitere Informationen: [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

- Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](tutorial-setup-vscode-extension.md).

- Bei den in diesem Artikel verwendeten __Python__-Codeausschnitten wird davon ausgegangen, dass die folgenden Variablen festgelegt sind:

    * `ws`: Legen Sie diese Variable auf Ihren Arbeitsbereich fest.
    * `model`: Legen Sie diese Variable auf Ihr registriertes Modell fest.
    * `inference_config`: Legen Sie diese Variable auf die Rückschlusskonfiguration für das Modell fest.

    Weitere Informationen zum Festlegen dieser Variablen finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

- Bei den in diesem Artikel verwendeten __CLI__-Ausschnitten wird davon ausgegangen, dass Sie ein `inferenceconfig.json`-Dokument erstellt haben. Weitere Informationen zum Erstellen dieses Dokuments finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

- Ein Azure Kubernetes Service-Cluster, der mit Ihrem Arbeitsbereich verbunden ist. Weitere Informationen finden Sie unter [Erstellen und Anfügen eines Azure Kubernetes Service-Clusters](how-to-create-attach-kubernetes.md).

    - Wenn Sie Modelle auf GPU-Knoten oder FPGA-Knoten (oder einer bestimmten SKU) bereitstellen möchten, müssen Sie einen Cluster mit der jeweiligen SKU erstellen. Das Erstellen eines sekundären Knotenpools in einem vorhandenen Cluster und Bereitstellen von Modellen im sekundären Knotenpool wird nicht unterstützt.

## <a name="understand-the-deployment-processes"></a>Grundlegendes zu Bereitstellungsvorgängen

Das Wort „Bereitstellung“ wird sowohl in Kubernetes als auch bei Azure Machine Learning verwendet. „Bereitstellung“ hat in diesen beiden Kontexten jedoch unterschiedliche Bedeutungen. In Kubernetes ist eine `Deployment` eine konkrete Entität, die mit einer deklarativen YAML-Datei angegeben wird. Eine Kubernetes-`Deployment` verfügt über einen definierten Lebenszyklus und konkrete Beziehungen zu anderen Kubernetes-Entitäten, z. B. `Pods` und `ReplicaSets`. Informationen zu Kubernetes in Form von Dokumenten und Videos finden Sie unter [Was ist Kubernetes?](https://aka.ms/k8slearning).

In Azure Machine Learning wird „Bereitstellung“ allgemeiner für das Verfügbarmachen und Bereinigen Ihrer Projektressourcen verwendet. Folgende Schritte werden in Azure Machine Learning als Teil der Bereitstellung betrachtet:

1. Zippen der Dateien in Ihrem Projektordner, wobei die in „.amlignore“ oder „.gitignore“ angegebenen Dateien ignoriert werden
1. Zentrales Hochskalieren Ihres Computeclusters (bezieht sich auf Kubernetes)
1. Erstellen oder Herunterladen des Dockerfiles auf den Serverknoten (bezieht sich auf Kubernetes)
    1. Das System berechnet einen Hashwert aus: 
        - Dem Basisimage 
        - Benutzerdefinierten Docker-Schritten (siehe [Bereitstellen eines Modells mithilfe eines benutzerdefinierten Docker-Basisimages](./how-to-deploy-custom-docker-image.md))
        - Der Conda-Definitions-YAML-Datei (siehe [Erstellen und Verwenden von Softwareumgebungen in Azure Machine Learning](./how-to-use-environments.md))
    1. Das System verwendet diesen Hash als Schlüssel in einer Suche nach der Azure Container Registry (ACR) für den Arbeitsbereich.
    1. Wenn er nicht gefunden wird, wird nach einer Übereinstimmung in der globalen ACR gesucht.
    1. Wenn keine gefunden wird, erstellt das System ein neues Image (das zwischengespeichert und an die ACR des Arbeitsbereichs übermittelt wird).
1. Herunterladen der gezippten Projektdatei in den temporären Speicher auf dem Serverknoten
1. Entzippen der Projektdatei
1. Ausführen von `python <entry script> <arguments>` auf dem Serverknoten
1. Speichern von Protokollen, Modelldateien und anderen Dateien, die in dem Speicherkonto, das dem Arbeitsbereich zugeordnet ist, in `./outputs` geschrieben werden
1. Zentrales Herunterskalieren von Compute, einschließlich Entfernen des temporären Speichers (bezieht sich auf Kubernetes)

### <a name="azure-ml-router"></a>Azure ML-Router

Die Front-End-Komponente (azureml-fe), die eingehende Rückschlussanforderungen an bereitgestellte Dienste weiterleitet, wird automatisch nach Bedarf skaliert. Die Skalierung von azureml-fe basiert dem Zweck und der Größe (Anzahl der Knoten) des AKS-Clusters. Clusterzweck und -knoten werden konfiguriert, wenn Sie einen [AKS-Cluster erstellen oder anfügen](how-to-create-attach-kubernetes.md). Pro Cluster gibt es einen azureml-fe-Dienst, der möglicherweise auf mehreren Pods ausgeführt wird.

> [!IMPORTANT]
> Wenn Sie einen Cluster verwenden, der als __Dev/Test__ konfiguriert ist, wird der Self-Scaler **deaktiviert**.

Azureml-fe wird sowohl (vertikal) hochskaliert, um mehr Kerne zu verwenden, als auch (horizontal) aufskaliert, um mehr Pods zu verwenden. Wenn Sie die Entscheidung zum zentralen Hochskalieren treffen, wird die Zeit herangezogen, die zum Weiterleiten eingehender Rückschlussanforderungen benötigt wird. Wenn diese Zeit den Schwellenwert überschreitet, erfolgt eine Hochskalierung. Wenn die Zeit zum Weiterleiten eingehender Anforderungen weiterhin den Schwellenwert überschreitet, erfolgt eine Aufskalierung.

Beim Herunter- und Abskalieren wird die CPU-Auslastung verwendet. Wenn der Schwellenwert für die CPU-Auslastung erreicht ist, wird das Front-End zuerst herunterskaliert. Wenn die CPU-Auslastung auf den Schwellenwert für die Abskalierung sinkt, erfolgt eine Abskalierung. Ein Hoch- und Aufskalieren erfolgt nur, wenn genügend Clusterressourcen verfügbar sind.

## <a name="deploy-to-aks"></a>Bereitstellen für AKS

Um ein Modell für Azure Kubernetes Service bereitzustellen, erstellen Sie eine __Bereitstellungskonfiguration__, in der die benötigten Computeressourcen beschrieben werden. Dies sind beispielsweise die Anzahl von Kernen und die Arbeitsspeichergröße. Außerdem benötigen Sie eine __Rückschlusskonfiguration__, in der die zum Hosten des Modells und des Webdiensts erforderliche Umgebung beschrieben wird. Weitere Informationen zum Erstellen der Rückschlusskonfiguration finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

> [!NOTE]
> Die Anzahl der bereitzustellenden Modelle ist auf 1.000 Modelle pro Bereitstellung (pro Container) beschränkt.

<a id="using-the-cli"></a>

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen, Methoden und Parametern finden Sie in den folgenden Referenzdokumenten:

* [AksCompute](/python/api/azureml-core/azureml.core.compute.aks.akscompute?preserve-view=true&view=azure-ml-py)
* [AksWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?preserve-view=true&view=azure-ml-py)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie für die Bereitstellung mit der CLI den folgenden Befehl. Ersetzen Sie `myaks` durch den Namen des AKS-Computeziels. Ersetzen Sie `mymodel:1` durch den Namen und die Version des registrierten Modells. Ersetzen Sie `myservice` durch den Namen, den dieser Dienst erhalten soll:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Weitere Informationen finden Sie in der [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)-Referenz.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Informationen zur Verwendung von VS Code finden Sie im Artikel zum [Bereitstellen von AKS über die VS Code-Erweiterung](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Für die Bereitstellung über VS Code muss der AKS-Cluster im Vorfeld erstellt oder an Ihren Arbeitsbereich angefügt werden.

---

### <a name="autoscaling"></a>Automatische Skalierung

Die Komponente, die die automatische Skalierung für Implementierungen von Azure ML-Modellen behandelt, ist azureml-fe, einem intelligenten Anforderungsrouter. Da er von allen Rückschlussanforderungen durchlaufen wird, verfügt er über die zum automatischen Skalieren der bereitgestellten Modelle erforderlichen Daten.

> [!IMPORTANT]
> * **Aktivieren Sie die horizontale automatische Kubernetes-Podskalierung (HPA) nicht für Modellbereitstellungen**. Dies würde dazu führen, dass die beiden Komponenten für die automatische Skalierung miteinander konkurrieren würden. Azureml-fe ist für die automatische Skalierung von Modellen konzipiert, die von Azure ML bereitgestellt wurden, wobei HPA die Modellauslastung anhand einer generischen Metrik wie der CPU-Auslastung oder einer benutzerdefinierten Metrikkonfiguration erraten oder näherungsweise ermitteln müsste.
> 
> * **Azureml-fe skaliert die Anzahl der Knoten in einem AKS-Cluster nicht**, da dies zu unerwarteten Kostensteigerungen führen könnte. Stattdessen erfolgt eine **Skalierung der Anzahl der Replikate für das Modell** innerhalb der physischen Clustergrenzen. Wenn Sie die Anzahl der Knoten im Cluster skalieren müssen, können Sie den Cluster manuell skalieren oder die [Autoskalierung von AKS-Clustern konfigurieren](../aks/cluster-autoscaler.md).

Die automatische Skalierung kann durch Festlegen von `autoscale_target_utilization`, `autoscale_min_replicas` und `autoscale_max_replicas` für den AKS-Webdienst gesteuert werden. Die Aktivierung der automatischen Skalierung wird im folgenden Beispiel veranschaulicht:

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True, 
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

Skalierungsentscheidungen werden auf der Grundlage der Auslastung der aktuellen Containerreplikate getroffen. Zur Ermittlung der aktuellen Auslastung wird die Anzahl ausgelasteter Replikate (Replikate, die eine Anforderung verarbeiten) durch die Gesamtanzahl aktueller Replikate geteilt. Übersteigt dieser Wert `autoscale_target_utilization`, werden weitere Replikate erstellt. Ist der Wert niedriger, wird die Replikatanzahl verringert. Die Zielauslastung ist standardmäßig auf 70 Prozent festgelegt.

Entscheidungen zum Hinzufügen von Replikaten sind eifrig und schnell (ungefähr 1 Sekunde). Die Entscheidung, Replikate zu entfernen, erfolgt zurückhaltend (etwa 1 Minute).

Die erforderlichen Replikate können mithilfe des folgenden Codes berechnet werden:

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

Weitere Informationen zum Festlegen von `autoscale_target_utilization`, `autoscale_max_replicas` und `autoscale_min_replicas` finden Sie in der Modulreferenz zu [AksWebservice](/python/api/azureml-core/azureml.core.webservice.akswebservice?preserve-view=true&view=azure-ml-py).

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Bereitstellen von Modellen in AKS mithilfe eines kontrollierten Rollouts (Vorschau)

Sie können Modellversionen mithilfe von Endpunkten kontrolliert analysieren und höher stufen. Sie können bis zu sechs Versionen hinter einem einzelnen Endpunkt bereitstellen. Endpunkte bieten die folgenden Funktionen:

* Konfigurieren Sie den __Prozentsatz der Bewertung des Datenverkehrs, der an jeden Endpunkt gesendet wird__. Leiten Sie z. B. 20 % des Datenverkehrs zum Endpunkt „Test“ und 80 % zum Endpunkt „Produktion“ weiter.

    > [!NOTE]
    > Wenn Sie nicht 100 % des Datenverkehrs erfassen, wird jeder verbleibende Prozentsatz an die __Standardversion__ des Endpunkts weitergeleitet. Wenn Sie z. B. die Endpunktversion „Test“ so konfigurieren, dass 10 % des Datenverkehrs erfasst werden, und Sie die „Produktion“ so konfigurieren, dass 30 %, erfasst werden, dann werden die restlichen 60 % an die Standardversion des Endpunkts gesendet.
    >
    > Die erste erstellte Endpunktversion wird automatisch als Standard konfiguriert. Sie können dies ändern, indem Sie beim Erstellen oder Aktualisieren einer Endpunktversion `is_default=True` festlegen.
     
* Markieren Sie eine Endpunktversion entweder als __Kontrolle__ oder als __Behandlung__. Die aktuelle Produktionsendpunktversion könnte z. B. die Kontrolle darstellen, während potenzielle neue Modelle als Behandlungsversionen bereitgestellt werden. Nach der Bewertung der Leistung der Behandlungsversionen, wenn eine die aktuelle Kontrollversion übertrifft, könnte sie zur neuen Produktion/Kontrolle höher gestuft werden.

    > [!NOTE]
    > Sie können nur über __eine__ Kontrollversion verfügen. Sie können über mehrere Behandlungen verfügen.

Sie können App-Erkenntnisse aktivieren, um Metriken zum Betrieb von Endpunkten und bereitgestellten Versionen anzuzeigen.

### <a name="create-an-endpoint"></a>Erstellen eines Endpunkts
Wenn Sie bereit sind, Ihre Modelle bereitzustellen, erstellen Sie einen Bewertungsendpunkt, und stellen Sie Ihre erste Version bereit. Im folgenden Beispiel wird gezeigt, wie der Endpunkt mithilfe des SDK bereitgestellt und erstellt wird. Die erste Bereitstellung wird als Standardversion definiert, was bedeutet, dass ein nicht spezifiziertes Perzentil des Datenverkehrs in allen Versionen in die Standardversion eingehen.  

> [!TIP]
> Im folgenden Beispiel legt die Konfiguration die anfängliche Endpunktversion so fest, dass 20 % des Datenverkehrs verarbeitet werden. Da dies der erste Endpunkt ist, ist dies auch die Standardversion. Und da wir für die anderen 80 % des Datenverkehrs über keine anderen Versionen verfügen, wird er ebenfalls an die Standardversion weitergeleitet. Bis andere Versionen, die einen bestimmten Prozentsatz des Datenverkehrs übernehmen, bereitgestellt werden, erhält diese Version effektiv 100 % des Datenverkehrs.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')

# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Aktualisieren und Hinzufügen von Versionen zu einem Endpunkt

Fügen Sie Ihrem Endpunkt eine weitere Version hinzu, und konfigurieren Sie das bewertende Perzentil des Datenverkehrs, das in die Version eingeht. Es gibt zwei Arten von Versionen, eine Kontroll- und eine Behandlungsversion. Es kann mehrere Behandlungsversionen geben, um den Vergleich mit einer einzelnen Kontrollversion zu erleichtern.

> [!TIP]
> Die zweite Version, die durch den folgenden Codeausschnitt erstellt wurde, akzeptiert 10 % des Datenverkehrs. Die erste Version ist für 20 % konfiguriert, sodass nur 30 % des Datenverkehrs für bestimmte Versionen konfiguriert sind. Die verbleibenden 70 % werden an die erste Endpunktversion gesendet, da diese auch die Standardversion ist.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
endpoint.wait_for_deployment(True)
```

Aktualisieren Sie vorhandene Versionen, oder löschen Sie sie in einem Endpunkt. Sie können den Standardtyp, den Kontrolltyp und das Perzentil des Datenverkehrs der Version ändern. Im folgenden Beispiel erhöht die zweite Version ihren Datenverkehr auf 40 % und ist nun die Standardversion.

> [!TIP]
> Nach dem folgenden Codeausschnitt ist nun die zweite Version die Standardversion. Sie ist jetzt für 40 % konfiguriert, während die ursprüngliche Version noch für 20 % konfiguriert ist. Dies bedeutet, dass 40 % des Datenverkehrs nicht auf Versionskonfigurationen entfallen. Der verbleibende Datenverkehr wird auf die zweite Version umgeleitet, da sie jetzt als Standardversion gilt. Sie empfängt effektiv 80 % des Datenverkehrs.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```

## <a name="web-service-authentication"></a>Webdienstauthentifizierung

Bei der Bereitstellung in Azure Kubernetes Service ist die __schlüsselbasierte__ Authentifizierung standardmäßig aktiviert. Sie können auch die __tokenbasierte__ Authentifizierung aktivieren. Die tokenbasierte Authentifizierung erfordert, dass Clients ein Azure Active Directory-Konto verwenden, um ein Authentifizierungstoken anzufordern, mit dem Anforderungen an den bereitgestellten Dienst gesendet werden.

Um die Authentifizierung zu __deaktivieren__, legen Sie den Parameter `auth_enabled=False` beim Erstellen der Bereitstellungskonfiguration fest. Im folgenden Beispiel wird die Authentifizierung mithilfe des SDKs deaktiviert:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Informationen zum Authentifizieren aus einer Clientanwendung finden Sie unter [Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Authentifizierung mit Schlüsseln

Bei aktivierter Schlüsselauthentifizierung können Sie mithilfe der Methode `get_keys` einen primären und einen sekundären Authentifizierungsschlüssel abrufen:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Wenn Sie einen Schlüssel erneut generieren müssen, verwenden Sie [`service.regen_key`](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py).

### <a name="authentication-with-tokens"></a>Authentifizierung mit Tokens

Um Tokenauthentifizierung zu aktivieren, legen Sie beim Erstellen oder Aktualisieren einer Bereitstellung den Parameter `token_auth_enabled=True` fest. Im folgenden Beispiel wird die Authentifizierung mithilfe des SDKs aktiviert:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Bei aktivierter Tokenauthentifizierung können Sie mithilfe der Methode `get_token` ein JWT-Token und dessen Ablaufzeit abrufen:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Nach Ablauf der für `refresh_by` festgelegten Zeit müssen Sie ein neues Token anfordern.
>
> Microsoft empfiehlt dringend, den Azure Machine Learning-Arbeitsbereich in der gleichen Region zu erstellen wie den Azure Kubernetes Service-Cluster. Im Zuge der Tokenauthentifizierung richtet der Webdienst einen Aufruf an die Region, in der Ihr Azure Machine Learning-Arbeitsbereich erstellt wird. Ist die Region Ihres Arbeitsbereichs nicht verfügbar, können Sie kein Token für Ihren Webdienst abrufen (auch dann nicht, wenn sich Ihr Cluster in einer anderen Region befindet als Ihr Arbeitsbereich). Die tokenbasierte Authentifizierung ist dann erst wieder verfügbar, wenn die Region Ihres Arbeitsbereichs wieder verfügbar wird. Außerdem wirkt sich die Entfernung zwischen der Region Ihres Clusters und der Region Ihres Arbeitsbereichs direkt auf die Tokenabrufdauer aus.
>
> Zum Abrufen eines Tokens müssen Sie das Azure Machine Learning SDK oder den Befehl [az ml service get-access-token](/cli/azure/ext/azure-cli-ml/ml/service?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) verwenden.


### <a name="vulnerability-scanning"></a>Überprüfung auf Sicherheitsrisiken

Azure Security Center bietet einheitliche Funktionen für die Sicherheitsverwaltung und den erweiterten Schutz vor Bedrohungen für Hybrid Cloud-Workloads. Sie sollten zulassen, dass Azure Security Center Ihre Ressourcen überprüft und die Empfehlungen befolgt. Weitere Informationen finden Sie unter [Einführung in Azure Defender für Kubernetes](../security-center/defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Azure RBAC für die Kubernetes-Autorisierung](../aks/manage-azure-rbac.md)
* [Schützen von Rückschlussumgebungen mit Azure Virtual Network](how-to-secure-inferencing-vnet.md)
* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](how-to-deploy-custom-docker-image.md)
* [Problembehandlung von Bereitstellungen von Azure Machine Learning Service mit AKS und ACI](how-to-troubleshoot-deployment.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Consume a ML Model deployed as a web service (Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells)](how-to-consume-web-service.md).
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
