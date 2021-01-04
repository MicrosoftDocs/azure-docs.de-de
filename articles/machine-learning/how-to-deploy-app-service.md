---
title: Bereitstellen von ML-Modellen in Azure App Service (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mithilfe von Azure Machine Learning ein trainiertes ML-Modell in einer Web-App mit Azure App Service bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 5f3861d43715ed68116d25206efe4347ea96805c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452133"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Bereitstellen eines Machine Learning-Modells in Azure App Service (Vorschauversion)


Hier erfahren Sie, wie Sie ein Modell in Azure Machine Learning als Web-App in Azure App Service bereitstellen.

> [!IMPORTANT]
> Azure Machine Learning und Azure App Service sind zwar allgemein verfügbar, die Funktion zum Bereitstellen eines Modells über Machine Learning in App Service befindet sich jedoch in der Vorschauphase.

Mit Azure Machine Learning können Sie Docker-Images auf der Grundlage trainierter Machine Learning-Modelle erstellen. Dieses Image enthält einen Webdienst, der Daten empfängt und an das Modell übermittelt und anschließend die Antwort zurückgibt. Azure App Service kann zum Bereitstellen des Images verwendet werden und bietet die folgenden Features:

* Erweiterte [Authentifizierung](../app-service/configure-authentication-provider-aad.md) für höhere Sicherheit. Zu den Authentifizierungsmethoden zählen Azure Active Directory und mehrstufige Authentifizierung.
* [Autoskalierung](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) ohne erneute Bereitstellung
* [TLS-Unterstützung](../app-service/configure-ssl-certificate-in-code.md) für die sichere Kommunikation zwischen Clients und dem Dienst

Weitere Informationen zu den Features von Azure App Service finden Sie unter [App Service: Übersicht](../app-service/overview.md).

> [!IMPORTANT]
> Wenn die mit dem bereitgestellten Modell verwendeten Bewertungsdaten oder die Ergebnisse der Bewertung protokolliert werden sollen, sollten Sie die Bereitstellung stattdessen in Azure Kubernetes Service durchführen. Weitere Informationen finden Sie unter [Sammeln von Daten zu Ihren Produktionsmodellen](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie im Artikel [Erstellen eines Arbeitsbereichs](how-to-manage-workspace.md).
* Die [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* Ein in Ihrem Arbeitsbereich registriertes trainiertes Machine Learning-Modell. Falls Sie kein Modell besitzen, können Sie anhand der Informationen unter [Tutorial: Trainieren von Bildklassifikationsmodellen mit MNIST-Daten und Scikit-learn mithilfe von Azure Machine Learning](tutorial-train-models-with-aml.md) ein Modell trainieren und registrieren.

    > [!IMPORTANT]
    > Bei den in diesem Artikel verwendeten Codeausschnitten wird davon ausgegangen, dass Sie die folgenden Variablen festgelegt haben:
    >
    > * `ws`: Ihr Azure Machine Learning-Arbeitsbereich
    > * `model`: das registrierte Modell, das bereitgestellt wird
    > * `inference_config`: die Rückschlusskonfiguration für das Modell
    >
    > Weitere Informationen zum Festlegen dieser Variablen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung

Vor der Bereitstellung müssen Sie die Elemente definieren, die zum Ausführen des Modells als Webdienst erforderlich sind. Die folgende Liste beschreibt die wesentlichen für eine Bereitstellung erforderlichen Elemente:

* Ein __Eingabeskript__. Dieses Skript akzeptiert Anforderungen, bewertet die Anforderung mithilfe des Modells und gibt die Ergebnisse zurück.

    > [!IMPORTANT]
    > Das Eingabeskript ist spezifisch für Ihr Modell. Es muss das Format der Daten der eingehenden Anforderung, das Format der im Modell erwarteten Daten und das Format der an Clients zurückgegebenen Daten erkennen.
    >
    > Wenn die Anforderungsdaten in einem Format vorliegen, das in Ihrem Modell nicht verwendet werden kann, können sie im Skript in ein akzeptables Format umgewandelt werden. Außerdem kann die Antwort umgewandelt werden, bevor sie an den Client zurückgegeben wird.

    > [!IMPORTANT]
    > Das Azure Machine Learning SDK bietet keine Möglichkeit des Zugriffs auf Ihren Datenspeicher oder Ihre Datasets durch den Webdienst. Wenn das bereitgestellte Modell auf Daten zugreifen muss, die außerhalb der Bereitstellung gespeichert sind – wie z.B. in einem Azure Storage-Konto –, müssen Sie mit dem entsprechenden SDK eine benutzerdefinierte Codelösung entwickeln. Ein Beispiel hierfür ist das [Azure Storage SDK für Python](https://github.com/Azure/azure-storage-python).
    >
    > Eine andere Alternative, die in Ihrem Szenario funktionieren könnte, ist die [Batchvorhersage](./tutorial-pipeline-batch-scoring-classification.md), die beim Erstellen von Bewertungen Zugriff auf Datenspeicher bietet.

    Weitere Informationen zu Eingabeskripts finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

* **Abhängigkeiten**, z. B. Hilfsprogramme oder Python/Conda-Pakete, die zum Ausführen des Eingabeskripts oder Modells erforderlich sind

Diese Entitäten werden in einer __Rückschlusskonfiguration__ gekapselt. Die Rückschlusskonfiguration verweist auf das Eingabeskript und andere Abhängigkeiten.

> [!IMPORTANT]
> Beim Erstellen einer Rückschlusskonfiguration zur Verwendung mit Azure App Service müssen Sie ein [Environment](/python/api/azureml-core/azureml.core.environment(class)?preserve-view=true&view=azure-ml-py)-Objekt verwenden. Beachten Sie, dass Sie beim Definieren einer benutzerdefinierten Umgebung azureml-defaults mit Version > = 1.0.45 als Pip-Abhängigkeit hinzufügen müssen. Dieses Paket enthält die erforderlichen Funktionen zum Hosten des Modells als Webdienst. Im folgenden Beispiel wird veranschaulicht, wie ein Environment-Objekt erstellt und mit einer Rückschlusskonfiguration verwendet wird:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Weitere Informationen zu Umgebungen finden Sie unter [Erstellen und Verwalten von Umgebungen für Training und Bereitstellung](how-to-use-environments.md).

Weitere Informationen zur Rückschlusskonfiguration finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Bei der Bereitstellung in Azure App Service müssen Sie keine __Bereitstellungskonfiguration__ erstellen.

## <a name="create-the-image"></a>Erstellen des Images

Verwenden Sie [Model.package](/python/api/azureml-core/azureml.core.model.model?preserve-view=true&view=azure-ml-py), um das Docker-Image zu erstellen, das in Azure App Service bereitgestellt wird. Der folgende Codeausschnitt veranschaulicht, wie ein neues Image aus dem Modell und der Rückschlusskonfiguration erstellt wird:

> [!NOTE]
> Im Codeausschnitt wird davon ausgegangen, dass `model` ein registriertes Modell und `inference_config` die Konfiguration für die Rückschlussumgebung enthält. Weitere Informationen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Bei `show_output=True` wird die Ausgabe des Docker-Buildprozesses angezeigt. Nach Abschluss des Prozesses wurde das Image in der Azure Container Registry-Instanz für Ihren Arbeitsbereich erstellt. Nachdem das Image erstellt wurde, wird der Speicherort in Ihrer Instanz von Azure Container Registry angezeigt. Der zurückgegebene Speicherort weist das Format `<acrinstance>.azurecr.io/package@sha256:<imagename>` auf. Beispiel: `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Speichern Sie die Speicherortinformationen, da sie beim Bereitstellen des Images verwendet werden.

## <a name="deploy-image-as-a-web-app"></a>Bereitstellen eines Images als Web-App

1. Verwenden Sie den folgenden Befehl, um die Anmeldeinformationen für die Azure Container Registry-Instanz zu erhalten, die das Image enthält. Ersetzen Sie `<acrinstance>` durch den zuvor von `package.location` zurückgegebenen Wert:

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    Die Ausgabe dieses Befehls ähnelt dem folgenden JSON-Dokument:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Speichern Sie die Werte für __Benutzername__ und eines der __Kennwörter__.

1. Wenn Sie noch nicht über eine Ressourcengruppe oder einen App Service-Plan für die Bereitstellung des Diensts verfügen, veranschaulichen die folgenden Befehle das Erstellen dieser beiden Elemente:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    In diesem Beispiel wird der __Basic__-Tarif (`--sku B1`) verwendet.

    > [!IMPORTANT]
    > Die von Azure Machine Learning erstellten Images verwenden Linux, daher müssen Sie den Parameter `--is-linux` verwenden.

1. Verwenden Sie den folgenden Befehl, um die neue Web-App zu erstellen. Ersetzen Sie `<app-name>` durch den Namen, den Sie verwenden möchten. Ersetzen Sie `<acrinstance>` und `<imagename>` durch die zuvor von `package.location` zurückgegebenen Werte:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    Dieser Befehl gibt Informationen ähnlich dem folgenden JSON-Dokument zurück:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > An diesem Punkt wurde die Web-App erstellt. Da Sie keine Anmeldeinformationen für die Azure Container Registry-Instanz mit dem Image angegeben haben, ist die Web-App jedoch nicht aktiv. Im nächsten Schritt geben Sie die Authentifizierungsinformationen für die Containerregistrierung an.

1. Verwenden Sie den folgenden Befehl, um die für den Zugriff auf die Containerregistrierung erforderlichen Anmeldeinformationen für die Web-App bereitzustellen. Ersetzen Sie `<app-name>` durch den Namen, den Sie verwenden möchten. Ersetzen Sie `<acrinstance>` und `<imagename>` durch die zuvor von `package.location` zurückgegebenen Werte. Ersetzen Sie `<username>` und `<password>` durch die zuvor abgerufenen ACR-Anmeldeinformationen:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Dieser Befehl gibt Informationen ähnlich dem folgenden JSON-Dokument zurück:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

Zu diesem Zeitpunkt beginnt die Web-App mit dem Laden des Images.

> [!IMPORTANT]
> Es kann einige Minuten dauern, bis das Image geladen wurde. Sie können den Fortschritt mithilfe des folgenden Befehls überwachen:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Nachdem das Image geladen wurde und die Website aktiv ist, wird im Protokoll die Meldung `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` angezeigt.

Nachdem das Image bereitgestellt wurde, können Sie den Hostnamen mit dem folgenden Befehl ermitteln:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Dieser Befehl gibt Informationen ähnlich dem folgenden Hostnamen zurück: `<app-name>.azurewebsites.net`. Verwenden Sie diesen Wert als Teil der __Basis-URL__ für den Dienst.

## <a name="use-the-web-app"></a>Verwenden der Web-App

Der Webdienst, der Anforderungen an das Modell übergibt, befindet sich unter `{baseurl}/score`. Beispiel: `https://<app-name>.azurewebsites.net/score`. Der folgende Python-Code veranschaulicht, wie Sie Daten an die URL übermitteln und die Antwort anzeigen:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Nächste Schritte

* In der Dokumentation zu [App Service für Linux](/azure/app-service/containers/) erfahren Sie, wie Sie Ihre Web-App konfigurieren.
* Weitere Informationen zur Skalierung finden Sie unter [Erste Schritte mit der automatischen Skalierung in Azure](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json).
* [Verwenden eines TLS/SSL-Zertifikats in Ihrem Code in Azure App Service](../app-service/configure-ssl-certificate-in-code.md)
* [Konfigurieren Ihrer App Service-App zur Verwendung der Azure Active Directory-Anmeldung](../app-service/configure-authentication-provider-aad.md)
* [Consume a ML Model deployed as a web service (Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells)](how-to-consume-web-service.md).