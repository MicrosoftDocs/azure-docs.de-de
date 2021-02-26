---
title: Bereitstellen von Modellen in Computeinstanzen
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihre Azure Machine Learning-Modelle mithilfe von Compute-Instanzen als Webdienst bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: d8b1c2a5384e479e39d169d368554f16c300a33e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954545"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Bereitstellen eines Modells auf einer Azure Machine Learning Studio-Compute-Instanz



Erfahren Sie, wie Sie mit Azure Machine Learning ein Modell als Webdienst auf Ihrer Azure Machine Learning-Compute-Instanz bereitstellen können. Verwenden Sie Compute-Instanzen, wenn eine der folgenden Bedingungen zutrifft:

- Sie müssen Ihr Modell schnell bereitstellen und überprüfen.
- Sie testen ein Modell in der Entwicklungsphase.

> [!TIP]
> Bei der Bereitstellung eines Modells aus einer Jupyter Notebook-Instanz auf einer Compute-Instanz in einem Webdienst auf der gleichen VM handelt es sich um eine _lokale Bereitstellung_. In diesem Fall ist der „lokale“ Computer die Compute-Instanz. Weitere Informationen zu Bereitstellungen finden Sie unter [Bereitstellen von Modellen mit Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Machine Learning-Arbeitsbereich mit einer ausgeführten Compute-Instanz. Weitere Informationen finden Sie unter [Tutorial: Einrichten der Umgebung und des Arbeitsbereichs](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Bereitstellung auf Compute-Instanzen

Ein Beispielnotebook mit einer Veranschaulichung lokaler Bereitstellungen ist auf Ihrer Compute-Instanz enthalten. Führen Sie die folgenden Schritte aus, um das Notebook zu laden und das Modell als Webdienst auf dem virtuellen Computer bereitzustellen:

1. Wählen Sie in [Azure Machine Learning Studio](https://ml.azure.com) die Option „Notebooks“ und dann unter „Beispiel-Notebooks“ die Datei „how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb“ aus. Klonen Sie dieses Notebook in Ihren Benutzerordner.

1. Suchen Sie das in Schritt 1 geklonte Notebook, wählen oder erstellen Sie eine Compute-Instanz, um das Notebook auszuführen.

    ![Screenshot des ausgeführten lokalen Diensts auf dem Notebook](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. Im Notebook werden die URL und der Port für den ausgeführten Dienst angezeigt. Beispiel: `https://localhost:6789`. Sie können zum Anzeigen des Ports auch die Zelle ausführen, die `print('Local service port: {}'.format(local_service.port))` enthält.

    ![Screenshot des Ports des ausgeführten lokalen Diensts](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. Verwenden Sie die URL `https://localhost:<local_service.port>`, um den Dienst auf der Compute-Instanz zu testen. Rufen Sie zum Testen über einen Remoteclient die öffentliche URL des auf der Compute-Instanz ausgeführten Diensts ab. Die öffentliche URL kann mit der folgenden Formel bestimmt werden: 
    * Notebook-VM: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score`. 
    * Compute-Instanz: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score`. 

    Beispiel: 
    * Notebook-VM: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Compute-Instanz: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>Testen des Diensts

Verwenden Sie den folgenden Code, um Beispieldaten an den ausgeführten Dienst zu übermitteln. Ersetzen Sie den Wert `service_url` durch die URL aus dem vorherigen Schritt:

> [!NOTE]
> Bei der Authentifizierung für eine Bereitstellung auf der Computeinstanz erfolgt die Authentifizierung über Azure Active Directory. Der Aufruf von `interactive_auth.get_authentication_header()` im Beispielcode authentifiziert Sie mithilfe von AAD und gibt einen Header zurück, der dann zur Authentifizierung gegenüber dem Dienst auf der Computeinstanz verwendet werden kann. Weitere Informationen finden Sie unter [Einrichten der Authentifizierung für Azure Machine Learning-Ressourcen und -Workflows](how-to-setup-authentication.md#interactive-authentication).
>
> Bei der Authentifizierung für eine Bereitstellung in Azure Kubernetes Service oder Azure Container Instances wird eine andere Authentifizierungsmethode verwendet. Weitere Informationen finden Sie unter [Konfigurieren der Authentifizierung für Azure Machine Learning-Modelle, die als Webdienst bereitgestellt werden](how-to-authenticate-web-service.md).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Nächste Schritte

* [Wie man ein Modell mit einem benutzerdefinierten Docker-Image bereitstellt](how-to-deploy-custom-docker-image.md)
* [Problembehandlung von Bereitstellungen von Azure Machine Learning Service mit AKS und ACI](how-to-troubleshoot-deployment.md)
* [Verwenden von TLS zum Absichern eines Webdiensts mit Azure Machine Learning](how-to-secure-web-service.md)
* [Consume a ML Model deployed as a web service (Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells)](how-to-consume-web-service.md).
* [Überwachen Ihrer Azure Machine Learning-Modelle mit Application Insights](how-to-enable-app-insights.md)
* [Sammeln von Daten für Modelle in der Produktion](how-to-enable-data-collection.md)
