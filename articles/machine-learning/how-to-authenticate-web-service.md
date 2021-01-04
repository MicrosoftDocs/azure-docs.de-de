---
title: Konfigurieren der Authentifizierung für als Webdienste bereitgestellte Modelle
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Authentifizierung für Machine Learning-Modelle konfigurieren, die für Webdienste in Azure Machine Learning bereitgestellt werden.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447445"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Konfigurieren der Authentifizierung für als Webdienste bereitgestellte Modelle

Mit Azure Machine Learning können Sie Ihre trainierten Machine Learning-Modelle als Webdienste bereitstellen. In diesem Artikel erfahren Sie, wie Sie die Authentifizierung für diese Bereitstellungen konfigurieren.

Die von Azure Machine Learning erstellten Modellimplementierungen können so konfiguriert werden, dass sie eine der beiden Authentifizierungsmethoden verwenden:

* **Schlüsselbasiert:** Für die Authentifizierung beim Webdienst wird ein statischer Schlüssel verwendet.
* **Tokenbasiert:** Ein temporäres Token muss aus dem Azure Machine Learning-Arbeitsbereich (unter Verwendung von Azure Active Directory) abgerufen und zur Authentifizierung gegenüber dem Webdienst verwendet werden. Dieses Token läuft nach einer bestimmten Zeit ab und muss aktualisiert werden, um weiter mit dem Webdienst zu arbeiten.

    > [!NOTE]
    > Die tokenbasierte Authentifizierung ist nur bei der Bereitstellung in Azure Kubernetes Service verfügbar.

## <a name="key-based-authentication"></a>Schlüsselbasierte Authentifizierung

Für in Azure Kubernetes Service (AKS) bereitgestellte Webdienste ist die schlüsselbasierte Authentifizierung standardmäßig *aktiviert*.

Für in Azure Container Instances (ACI) bereitgestellte Dienste ist die schlüsselbasierte Authentifizierung standardmäßig *deaktiviert*, aber Sie können sie aktivieren, indem Sie bei der Erstellung des ACI-Webdiensts `auth_enabled=True` festlegen. Der folgende Code zeigt ein Beispiel für die Erstellung einer ACI-Bereitstellungskonfiguration mit aktivierter schlüsselbasierter Authentifizierung.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Anschließend können Sie die benutzerdefinierte ACI-Konfiguration bei der Bereitstellung mit der `Model`-Klasse verwenden.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Verwenden Sie `aci_service.get_keys()`, um die Authentifizierungsschlüssel abzurufen. Um einen Schlüssel neu zu generieren, verwenden Sie die Sie die Funktion `regen_key()` und übergeben entweder **Primary** oder **Secondary**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Tokenbasierte Authentifizierung

Wenn Sie die Tokenauthentifizierung für einen Webdienst aktivieren, müssen die Benutzer ein Azure Machine Learning-JSON-Webtoken für den Webdienst bereitstellen, um auf den Dienst zugreifen zu können. Das Token läuft nach einem bestimmten Zeitrahmen ab und muss zum Durchführen weiterer Aufrufe aktualisiert werden.

* Die Tokenauthentifizierung ist **standardmäßig deaktiviert**, wenn die Bereitstellung in Azure Kubernetes Service erfolgt.
* Die Tokenauthentifizierung wird **nicht unterstützt**, wenn die Bereitstellung in Azure Container Instances erfolgt.
* Die Tokenauthentifizierung **und die schlüsselbasierte Authentifizierung können nicht gleichzeitig verwendet werden**.

Wenn Sie die Tokenauthentifizierung steuern möchten, verwenden Sie beim Erstellen oder Aktualisieren einer Bereitstellung den Parameter `token_auth_enabled`:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Bei aktivierter Tokenauthentifizierung können Sie mithilfe der Methode `get_token` ein JSON Web Token (JWT) und dessen Ablaufzeit abrufen:

> [!TIP]
> Wenn Sie zum Abrufen des Tokens einen Dienstprinzipal verwenden, der nur über den für das Abrufen eines Tokens erforderlichen Mindestzugriff verfügen soll, weisen Sie ihm für den Arbeitsbereich die Rolle **Leser** zu.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Nach Ablauf der für `refresh_by` festgelegten Zeit müssen Sie ein neues Token anfordern. Wenn Sie Token außerhalb des Python SDK aktualisieren müssen, besteht eine Möglichkeit darin, die REST-API mit Dienstprinzipalauthentifizierung zu verwenden, um den `service.get_token()`-Aufruf in regelmäßigen Abständen durchzuführen, wie zuvor beschrieben.
>
> Wir empfehlen Ihnen dringend, den Azure Machine Learning-Arbeitsbereich in der gleichen Region zu erstellen wie den Azure Kubernetes Service-Cluster.
>
> Im Zuge der Tokenauthentifizierung richtet der Webdienst einen Aufruf an die Region, in der Ihr Azure Machine Learning-Arbeitsbereich erstellt wird. Wenn Ihre Arbeitsbereichsregion nicht verfügbar ist, können Sie kein Token für Ihren Webdienst abrufen, selbst wenn sich Ihr Cluster in einer anderen Region als Ihr Arbeitsbereich befindet. Die Azure AD-Authentifizierung ist dann erst wieder verfügbar, wenn die Region Ihres Arbeitsbereichs wieder verfügbar wird.
>
> Außerdem wirkt sich die Entfernung zwischen der Region Ihres Clusters und der Region Ihres Arbeitsbereichs direkt auf die Tokenabrufdauer aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Authentifizierung bei einem bereitgestellten Modell finden Sie unter [Nutzen eines als Webdienst bereitgestellten Azure Machine Learning-Modells](how-to-consume-web-service.md).