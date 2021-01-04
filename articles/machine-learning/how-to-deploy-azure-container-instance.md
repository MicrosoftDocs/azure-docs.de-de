---
title: Bereitstellen von Modellen in Azure Container Instances
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre Azure Machine Learning-Modelle mithilfe von Azure Container Instances als Webdienst bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: a0e9401842284cad29b297be5ce572fa53cfe774
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188087"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Bereitstellen eines Modells in Azure Container Instances

Erfahren Sie, wie Sie ein Modell mit Azure Machine Learning als Webdienst in Azure Container Instances (ACI) bereitstellen. Verwenden Sie Azure Container Instances, wenn eine der folgenden Bedingungen zutrifft:

- Sie müssen Ihr Modell schnell bereitstellen und überprüfen. Sie müssen vorab keine ACI-Container erstellen. Sie werden im Rahmen des Bereitstellungsprozesses erstellt.
- Sie testen ein Modell in der Entwicklungsphase. 

Informationen zu den für ACI geltenden Kontingenten und zur Verfügbarkeit in den Regionen finden Sie im Artikel [Kontingente und Limits für Azure Container Instances](../container-instances/container-instances-quotas.md).

> [!IMPORTANT]
> Es wird dringend empfohlen, vor der Bereitstellung im Webdienst lokal zu debuggen. Weitere Informationen finden Sie unter [Lokales Debuggen](./how-to-troubleshoot-deployment-local.md).
>
> Weitere Informationen finden Sie auch unter Azure Machine Learning – [Bereitstellung auf lokalem Notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local).

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

## <a name="limitations"></a>Einschränkungen

* Wenn Sie Azure Container Instances in einem virtuellen Netzwerk verwenden, muss sich das virtuelle Netzwerk in derselben Ressourcengruppe wie der Azure Machine Learning-Arbeitsbereich befinden.
* Wenn Sie Azure Container Instances innerhalb des virtuellen Netzwerks verwenden, darf sich die Azure Container Registry (ACR) für Ihren Arbeitsbereich nicht ebenfalls im virtuellen Netzwerk befinden.

Weitere Informationen finden Sie unter [Schützen von Rückschlüssen mit virtuellen Netzwerken](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Bereitstellen für ACI

Um ein Modell für Azure Container Instances bereitzustellen, erstellen Sie eine __Bereitstellungskonfiguration__, in der die benötigten Computeressourcen beschrieben werden. Dies sind beispielsweise die Anzahl von Kernen und die Arbeitsspeichergröße. Außerdem benötigen Sie eine __Rückschlusskonfiguration__, in der die zum Hosten des Modells und des Webdiensts erforderliche Umgebung beschrieben wird. Weitere Informationen zum Erstellen der Rückschlusskonfiguration finden Sie unter [Wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI eignet sich nur für kleine Modelle, die weniger als 1 GB groß sind. 
> * Wir empfehlen die Verwendung von AKS mit einem einzelnen Knoten, um größere Modelle zu entwickeln.
> * Die Anzahl der bereitzustellenden Modelle ist auf 1.000 Modelle pro Bereitstellung (pro Container) beschränkt. 

### <a name="using-the-sdk"></a>Verwenden des SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen, Methoden und Parametern finden Sie in den folgenden Referenzdokumenten:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Model.deploy](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truewait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Verwenden der CLI

Verwenden Sie für die Bereitstellung mit der CLI den folgenden Befehl. Ersetzen Sie `mymodel:1` durch den Namen und die Version des registrierten Modells. Ersetzen Sie `myservice` durch den Namen, den dieser Dienst erhalten soll:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Weitere Informationen finden Sie in der [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)-Referenz. 

## <a name="using-vs-code"></a>Verwenden von VS Code

Lesen Sie die unter [Erstellen und Verwalten von Modellen](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model) aufgeführten Informationen zum Bereitstellen von Modellen mit Visual Studio Code.

> [!IMPORTANT]
> Sie müssen vorab keinen ACI-Container zu Testzwecken erstellen. ACI-Container werden bei Bedarf erstellt.

> [!IMPORTANT]
> Wir fügen eine Arbeitsbereichs-ID mit Hash allen zugrunde liegenden ACI-Ressourcen hinzu, die erstellt werden. Alle ACI-Namen im selben Arbeitsbereich haben dasselbe Suffix. Der Azure Machine Learning Service-Name wäre immer noch derselbe vom Kunden vorgegebene „Dienstname“, und für alle Benutzer von Azure Machine Learning SDK-APIs ist keine Änderung erforderlich. Wir geben keine Garantien für die Namen der zugrunde liegenden Ressourcen, die erstellt werden.

## <a name="next-steps"></a>Nächste Schritte

* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](how-to-deploy-custom-docker-image.md)
* [Problembehandlung von Bereitstellungen von Azure Machine Learning Service mit AKS und ACI](how-to-troubleshoot-deployment.md)
* [Aktualisieren des Webdiensts](how-to-deploy-update-web-service.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Consume a ML Model deployed as a web service (Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells)](how-to-consume-web-service.md).
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)