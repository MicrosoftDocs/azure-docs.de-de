---
title: Verwenden verwalteter Identitäten für die Zugriffssteuerung (Vorschau)
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie verwaltete Identitäten dazu verwenden können, um den Zugriff auf Azure-Ressourcen vom Azure Machine Learning-Arbeitsbereich aus zu steuern.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 3490e3004e5f5dd99795967f0deb8510200fa50b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311035"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Verwenden von verwalteten Identitäten mit Azure Machine Learning (Vorschau)

[Verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) gestatten es Ihnen, Ihren Arbeitsbereich mit den *minimal erforderlichen Berechtigungen für den Zugriff auf Ressourcen* zu konfigurieren. 

Bei der vertrauenswürdigen Konfiguration des Azure Machine Learning-Arbeitsbereichs ist es wichtig, sicherzustellen, dass die verschiedenen Dienste, die mit dem Arbeitsbereich verbunden sind, die richtige Zugriffsebene aufweisen. Der Arbeitsbereich benötigt z. B. während des Machine Learning-Workflows Zugriff auf Azure Container Registry (ACR) für Docker-Images und Speicherkonten für Trainingsdaten. 

Darüber hinaus ermöglichen verwaltete Identitäten eine differenzierte Berechtigungssteuerung. Sie können z. B. bestimmten Computeressourcen Zugriff auf eine bestimmte ACR gewähren oder diesen Zugriff widerrufen.

In diesem Artikel erfahren Sie, wie Sie verwaltete Identitäten für Folgendes verwenden:

 * Konfigurieren und Verwenden von ACR für Ihren Azure Machine Learning-Arbeitsbereich, ohne dass Sie den Zugriff für Administratorbenutzer für ACR aktivieren müssen.
 * Zugreifen auf eine private ACR außerhalb Ihres Arbeitsbereichs, um Basisimages für Training oder Rückschlüsse zu pullen.

> [!IMPORTANT]
> Das Verwenden von verwalteten Identitäten zur Zugriffssteuerung auf Ressourcen mit Azure Machine Learning befindet sich derzeit in der Vorschau. Die Vorschaufunktionalität wird „wie besehen“ zur Verfügung gestellt, ohne Garantien hinsichtlich Support oder Vereinbarung zum Servicelevel (Service Level Agreement, SLA). Weitere Informationen finden Sie in den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).
- Die [Azure CLI-Erweiterung für den Machine Learning Service](reference-azure-machine-learning-cli.md)
- Das [Python-SDK für Azure Machine Learning](/python/api/overview/azure/ml/intro?view=azure-ml-py)
- Zum Zuweisen von Rollen muss die Anmeldung für Ihr Azure-Abonnement die Rolle [Operator für verwaltete Identität](../role-based-access-control/built-in-roles.md#managed-identity-operator) oder eine andere Rolle aufweisen, die die erforderlichen Aktionen zulässt (z. B. __Besitzer__ ).
- Sie müssen mit dem Erstellen und Arbeiten mit [verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md) vertraut sein.

## <a name="configure-managed-identities"></a>Konfigurieren von verwalteten Identitäten

In einigen Situationen ist es notwendig, Administratorbenutzern den Zugriff auf Azure Container Registry zu verweigern. Beispielsweise kann die ACR freigegeben werden, und Sie müssen den Administratorzugriff für andere Benutzer sperren. Oder das Erstellen der ACR mit aktiviertem Administratorbenutzer wird durch eine Richtlinie für die Abonnementebene untersagt.

> [!IMPORTANT]
> Wenn Azure Machine Learning für Rückschlüsse auf Azure Container Instances (ACI) verwendet wird, ist der Administratorbenutzerzugriff auf ACR __erforderlich__. Deaktivieren Sie ihn nicht, wenn Sie vorhaben, Modelle zum Rückschließen für ACI bereitzustellen.

Wenn Sie ACR erstellen, ohne den Administratorbenutzerzugriff zu aktivieren, werden verwaltete Identitäten für den Zugriff auf die ACR verwendet, um Docker-Images zu erstellen und zu pullen.

Sie können Ihre eigene ACR mit deaktiviertem Administratorbenutzer bereitstellen, wenn Sie den Arbeitsbereich erstellen. Alternativ können Sie Azure Machine Learning die ACR für den Arbeitsbereich erstellen lassen und anschließend den Administratorbenutzer deaktivieren.

### <a name="bring-your-own-acr"></a>Nutzung der eigenen ACR

Wenn der ACR-Administratorbenutzer durch die Abonnementrichtlinie nicht zugelassen ist, sollten Sie zuerst ACR ohne Administratorbenutzer erstellen und es dann dem Arbeitsbereich zuordnen. Wenn Sie über eine vorhandene ACR mit deaktiviertem Administratorbenutzer verfügen, können Sie diese auch an den Arbeitsbereich anfügen.

[Erstellen Sie ACR über die Azure CLI](../container-registry/container-registry-get-started-azure-cli.md), ohne das Argument ```--admin-enabled``` festzulegen, oder über das Azure-Portal, ohne den Administratorbenutzer zu aktivieren. Geben Sie dann beim Erstellen des Azure Machine Learning-Arbeitsbereichs die Azure-Ressourcen-ID der ACR an. Das folgende Beispiel veranschaulicht das Erstellen eines neuen Azure ML-Arbeitsbereichs unter Verwendung einer vorhandenen ACR:

> [!TIP]
> Um den Wert für den Parameter `--container-registry` abzurufen, verwenden Sie den Befehl [az acr show](/cli/azure/acr?view=azure-cli-latest#az_acr_show), um Informationen für Ihre ACR anzuzeigen. Das Feld `id` enthält die Ressourcen-ID für Ihre ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Erstellen der ACR des Arbeitsbereichs durch den Azure Machine Learning Service

Wenn Sie keine eigene ACR bereitstellen, wird der Azure Machine Learning Service eine ACR für Sie erstellen, wenn Sie einen Vorgang durchführen, der eine benötigt. Übermitteln Sie z. B. eine Trainingsausführung an Machine Learning Compute, erstellen Sie eine Umgebung oder stellen Sie einen Webdienstendpunkt bereit. Für die vom Arbeitsbereich erstellte ACR ist der Administratorbenutzer aktiviert, und Sie müssen den Administratorbenutzer manuell deaktivieren.

1. Erstellen eines neuen Arbeitsbereichs

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Führen Sie eine Aktion aus, die ACR erfordert. Beispiel: [Tutorial zum Trainieren eines Modells](tutorial-train-models-with-aml.md).

1. Rufen Sie den vom Cluster erstellten ACR-Namen ab:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Dieser Befehl gibt einen Wert zurück, der in etwa wie folgt aussieht. Sie benötigen nur den letzten Teil des Texts, d. h. den Namen der ACR-Instanz:

    ```text
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Aktualisieren Sie die ACR, um den Administratorbenutzer zu deaktivieren:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Erstellen von Compute mit verwalteter Identität für den Zugriff auf Docker-Images für das Training

Erstellen Sie für den Zugriff auf die ACR des Arbeitsbereichs einen Computecluster für maschinelles Lernen mit aktivierter, vom System zugewiesener verwalteter Identität. Sie können die Identität über das Azure-Portal oder Studio aktivieren, wenn Sie den Compute erstellen, oder über die Azure CLI mithilfe von

# <a name="python"></a>[Python](#tab/python)

Wenn Sie einen Computecluster mit [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration?view=azure-ml-py) erstellen, legen Sie den verwalteten Identitätstyp mit dem Parameter `identity_type` fest.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Informationen zum Konfigurieren einer verwalteten Identität beim Erstellen eines Computeclusters im Studio finden Sie unter [Einrichten einer verwalteten Identität](how-to-create-attach-compute-studio.md#managed-identity).

---

Einer verwalteten Identität wird automatisch die Rolle „ACRPull“ für die ACR des Arbeitsbereichs zugewiesen, um das Pullen von Docker-Images für das Training zu ermöglichen.

> [!NOTE]
> Wenn Sie zuerst den Compute erstellen, bevor die ACR des Arbeitsbereichs erstellt wurde, müssen Sie die Rolle „ACRPull“ manuell zuweisen.

## <a name="access-base-images-from-private-acr"></a>Zugreifen auf Basisimages von einer privaten ACR

Azure Machine Learning verwendet standardmäßig Docker-Basisimages, die aus einem von Microsoft verwalteten öffentlichen Repository stammen. Anschließend wird Ihre Trainings- oder Rückschlussumgebung auf diesen Images erstellt. Weitere Informationen finden Sie unter [Was sind ML-Umgebungen?](concept-environments.md)

Um ein benutzerdefiniertes Basisimage intern in Ihrem Unternehmen zu verwenden, können Sie verwaltete Identitäten verwenden, um auf Ihre private ACR zuzugreifen. Es gibt zwei Anwendungsfälle:

 * Verwenden Sie das Basisimage für das Training wie besehen.
 * Erstellen Sie ein von Azure Machine Learning verwaltetes Image mit einem benutzerdefinierten Image als Basis.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Pullen Sie das Docker-Basisimage auf den Computecluster für maschinelles Lernen für das Training wie besehen.

Erstellen Sie einen Computecluster für maschinelles Lernen mit aktivierter, vom System zugewiesener verwalteter Identität, wie zuvor beschrieben. Bestimmen Sie dann die Prinzipal-ID der verwalteten Identität.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Optional können Sie den Computecluster aktualisieren, um eine vom Benutzer zugewiesene, verwaltete Identität zuzuweisen:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Damit der Computecluster die Basisimages pullen kann, gewähren Sie der verwalteten Dienstidentität die Rolle „ACRPull“ für die private ACR.

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Wenn Sie schließlich eine Trainingsausführung übermitteln, geben Sie die Position des Basisimages in der [Umgebungsdefinition](how-to-use-environments.md#use-existing-environments) an.

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Um sicherzustellen, dass das Basisimage direkt auf die Computeressource gepullt wird, legen Sie `user_managed_dependencies = True` fest, und geben Sie keine Dockerfile an. Andernfalls versucht Azure Machine Learning Service, ein neues Docker-Image zu erstellen und scheitert, da nur der Computecluster Zugriff hat, um das Basisimage aus der ACR zu pullen.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Erstellen einer von Azure Machine Learning verwalteten Umgebung in das Basisimage von der privaten ACR für Training oder Rückschluss

In diesem Szenario erstellt Azure Machine Learning Service die Trainings- oder Rückschlussumgebung auf einem Basisimage, das Sie von einer privaten ACR zur Verfügung stellen. Da die Imageerstellungsaufgabe mithilfe von ACR-Aufgaben in der ACR des Arbeitsbereichs erfolgt, müssen Sie zusätzliche Schritte durchführen, um den Zugriff zu ermöglichen.

1. Erstellen Sie eine __benutzerseitig zugewiesene verwaltete Identität__ , und erteilen Sie der Identität den ACRPull-Zugriff auf die __private ACR__.  
1. Gewähren Sie der __systemseitig zugewiesenen verwalteten Identität__ des Arbeitsbereichs die Rolle „Operator für verwaltete Identität“ für die __benutzerseitig zugewiesene verwaltete Identität__ aus dem vorherigen Schritt. Diese Rolle ermöglicht es dem Arbeitsbereich, die benutzerseitig zugewiesene verwaltete Identität der ACR-Aufgabe zum Erstellen der verwalteten Umgebung zuzuweisen. 

    1. Abrufen der Prinzipal-ID der systemseitig zugewiesenen verwalteten Identität des Arbeitsbereichs:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Erteilen Sie die Rolle des Operators für verwaltete Identität:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        Die UAI-Ressourcen-ID ist die Azure-Ressourcen-ID der benutzerseitig zugewiesenen Identität im Format `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>`.

1. Geben Sie die externe ACR- und Client-ID der __benutzerseitig zugewiesenen verwalteten Identität__ in Arbeitsbereichsverbindungen mithilfe der Methode [Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) an:

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Nachdem die Konfiguration abgeschlossen ist, können Sie die Basisimages aus der privaten ACR beim Erstellen von Umgebungen für Training oder Rückschlüsse verwenden. Der folgende Codeausschnitt zeigt, wie die Basisimage-ACR und der Imagename in einer Umgebungsdefinition angegeben werden können:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Optional können Sie die URL der verwalteten Identitätsressource und die Client-ID in der Umgebungsdefinition selbst angeben, indem Sie [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity?view=azure-ml-py) verwenden. Wenn Sie die Registrierungsidentität explizit verwenden, überschreibt sie alle zuvor angegebenen Arbeitsbereichsverbindungen:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Verwenden von Docker-Images für Rückschlüsse

Nachdem Sie ACR ohne Administratorbenutzer (wie oben beschrieben) konfiguriert haben, können Sie ohne Administratorschlüssel von Ihrem Azure Kubernetes-Dienst (AKS) aus auf Docker-Images für Rückschlüsse zugreifen. Wenn Sie AKS erstellen oder an den Arbeitsbereich anhängen, wird dem Dienstprinzipal des Clusters automatisch „ACRPull“-Zugriff auf die ACR des Arbeitsbereichs zugewiesen.

> [!NOTE]
> Wenn Sie Ihren eigenen AKS-Cluster bereitstellen, muss für den Cluster anstelle der verwalteten Identität ein Dienstprinzipal aktiviert sein.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Unternehmenssicherheit in Azure Machine Learning](concept-enterprise-security.md).