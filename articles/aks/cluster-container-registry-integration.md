---
title: Integrieren von Azure Container Registry in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Azure Kubernetes Service (AKS) und Azure Container Registry integrieren
services: container-service
manager: gwallace
ms.topic: article
ms.date: 01/08/2021
ms.openlocfilehash: 0d61cccb6b70091194d407eda056060d1fa3623c
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053889"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Authentifizieren per Azure Container Registry über Azure Kubernetes Service

Wenn Sie Azure Container Registry (ACR) mit dem Azure Kubernetes Service (AKS) nutzen, ist es erforderlich, einen Authentifizierungsmechanismus einzurichten. Dieser Vorgang ist als Teil der Befehlszeilenschnittstelle und des Portals implementiert, indem ACR die erforderlichen Berechtigungen erteilt werden. In diesem Artikel werden Beispiele für die Konfiguration der Authentifizierung zwischen diesen beiden Azure-Diensten beschrieben. 

Sie können die AKS- und ACR-Integration mit einigen einfachen Befehlen über die Azure-Befehlszeilenschnittstelle einrichten. Diese Integration weist dem Dienstprinzipal, der dem AKS-Cluster zugeordnet ist, die AcrPull-Rolle zu.

> [!NOTE]
> In diesem Artikel wird die automatische Authentifizierung zwischen AKS und ACR beschrieben. Wenn Sie ein Image aus einer privaten externen Registrierung abrufen, verwenden Sie ein [Geheimnis für Imagepullvorgänge][Image Pull Secret].

## <a name="before-you-begin"></a>Voraussetzungen

Voraussetzungen für diese Beispiele sind:

* Rolle **Besitzer** oder **Azure-Kontoadministrator** im **Azure-Abonnement**
* Azure-Befehlszeilenschnittstelle Version 2.7.0 oder höher

Um zu vermeiden, dass die Rolle **Besitzer** oder **Azure-Kontoadministrator** benötigt wird, können Sie einen Dienstprinzipal manuell konfigurieren oder einen vorhandenen Dienstprinzipal zur Authentifizierung von ACR aus AKS verwenden. Weitere Informationen finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](../container-registry/container-registry-auth-service-principal.md) oder unter [Abrufen von Images aus einer Azure-Containerregistrierung per Pull in einem Kubernetes-Cluster](../container-registry/container-registry-auth-kubernetes.md).

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>Erstellen eines neuen AKS-Clusters mit ACR-Integration

Sie können die AKS- und ACR-Integration während der erstmaligen Erstellung Ihres AKS-Clusters einrichten.  Damit ein AKS-Cluster mit ACR interagieren kann, wird ein Azure Active Directory-**Dienstprinzipal** verwendet. Mit dem folgenden CLI-Befehl können Sie eine vorhandene ACR-Instanz in Ihrem Abonnement autorisieren und die entsprechende **ACRPull**-Rolle für den Dienstprinzipal konfigurieren. Geben Sie gültige Werte für die unten stehenden Parameter an.

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

Alternativ können Sie den ACR-Namen mithilfe einer ACR-Ressourcen-ID angeben, die das folgende Format aufweist:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> Wenn Sie einen ACR verwenden, der sich in einem anderen Abonnement als Ihr AKS-Cluster befindet, verwenden Sie beim Anfügen oder Trennen eines AKS-Clusters die ACR-Ressourcen-ID.

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

Dieser Schritt kann mehrere Minuten in Anspruch nehmen.

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>Konfigurieren der ACR-Integration für vorhandene AKS-Cluster

Integrieren Sie eine vorhandene ACR-Instanz in vorhandene AKS-Cluster, indem Sie wie unten gezeigt gültige Werte für **acr-name** oder **acr-resource-id** angeben.

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

oder

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

Sie können die Integration zwischen einer ACR und einem AKS-Cluster auch folgendermaßen entfernen:

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

oder

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>Arbeiten mit ACR und AKS

### <a name="import-an-image-into-your-acr"></a>Importieren eines Images in Ihre ACR

Sie importieren ein Image aus dem Docker-Hub in Ihre ACR, indem Sie Folgendes ausführen:


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>Bereitstellen des Beispielimages aus ACR in AKS

Sicherstellen, dass Sie über die richtigen AKS-Anmeldeinformationen verfügen

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

Erstellen Sie eine Datei namens **acr-nginx.yaml**, die Folgendes enthält: Ersetzen Sie **acr-name** durch den Ressourcennamen Ihrer Registrierung. Beispiel: *myContainerRegistry*.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

Führen Sie als Nächstes diese Bereitstellung in Ihrem AKS-Cluster aus:

```console
kubectl apply -f acr-nginx.yaml
```

Sie können die Bereitstellung überwachen, indem Sie Folgendes ausführen:

```console
kubectl get pods
```

Sie sollten über zwei laufende Pods verfügen.

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>Problembehandlung
* Führen Sie den Befehl [az aks check-acr](/cli/azure/aks#az_aks_check_acr) aus, um zu überprüfen, ob über den AKS-Cluster auf die Registrierung zugegriffen werden kann.
* Weitere Informationen zur [ACR-Diagnose](../container-registry/container-registry-diagnostics-audit-logs.md)
* Weitere Informationen zur [ACR-Integrität](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[Image Pull secret]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/