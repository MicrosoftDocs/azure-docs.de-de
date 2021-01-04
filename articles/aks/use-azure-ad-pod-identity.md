---
title: Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)
description: Es wird beschrieben, wie Sie verwaltete AAD-Podidentitäten in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 150e2e71a4db8ab07caad479ae098d5b9eb746da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465720"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service (Vorschauversion)

Für verwaltete Azure Active Directory-Podidentitäten werden Kubernetes-Primitive verwendet, um [verwaltete Identitäten für Azure-Ressourcen][az-managed-identities] und Identitäten in Azure Active Directory (AAD) Pods zuzuordnen. Administratoren erstellen Identitäten und Bindungen als Kubernetes-Primitive, die Pods den Zugriff auf Azure-Ressourcen ermöglichen, für die AAD als Identitätsanbieter genutzt wird.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Die folgenden Ressourcen müssen installiert sein:

* Azure CLI ab Version 2.8.0
* Erweiterung `azure-preview`, Version 0.4.68 oder höher

### <a name="limitations"></a>Einschränkungen

* Für einen Cluster sind maximal 50 Podidentitäten zulässig.
* Für einen Cluster sind maximal 50 Ausnahmen für Podidentitäten zulässig.
* Verwaltete Podidentitäten sind nur in Linux-Knotenpools verfügbar.

### <a name="register-the-enablepodidentitypreview"></a>Registrieren von `EnablePodIdentityPreview`

Registrieren Sie das Feature `EnablePodIdentityPreview`:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle `aks-preview`

Sie benötigen außerdem die Version 0.4.64 oder höher der Erweiterung für die Azure-Befehlszeilenschnittstelle *aks-preview*. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Erstellen eines AKS-Clusters mit verwalteten Identitäten

Erstellen Sie einen AKS-Cluster, für den eine verwaltete Identität und eine verwaltete Podidentität aktiviert sind. Im Folgenden wird der Befehl [az group create][az-group-create] verwendet, um eine Ressourcengruppe mit dem Namen *myResourceGroup* zu erstellen, und der Befehl [az aks create][az-aks-create], um in der Ressourcengruppe *myResourceGroup* einen AKS-Cluster mit dem Namen *myAKSCluster* zu erstellen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity
```

Verwenden Sie [az aks get-credentials][az-aks-get-credentials], um sich bei Ihrem AKS-Cluster anzumelden. Mit diesem Befehl wird auch das `kubectl`-Clientzertifikat auf Ihren Entwicklungscomputer heruntergeladen und konfiguriert.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>Erstellen einer Identität

Erstellen Sie eine Identität, indem Sie den Befehl [az identity create][az-identity-create] verwenden, und legen Sie die Variablen *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_ID* fest.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="create-a-pod-identity"></a>Erstellen einer Podidentität

Erstellen Sie mit `az aks pod-identity add` eine Podidentität für den Cluster.

> [!IMPORTANT]
> Sie müssen über die entsprechenden Berechtigungen, z. B. `Owner`, für Ihr Abonnement verfügen, um die Identität und die Rollenbindung erstellen zu können.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Wenn Sie verwaltete Podidentitäten in Ihrem AKS-Cluster aktivieren, wird dem Namespace *kube-system* eine AzurePodIdentityException mit dem Namen *aks-addon-exception* hinzugefügt. AzurePodIdentityException ermöglicht Pods mit bestimmten Bezeichnungen den Zugriff auf den Azure Instance Metadata Service-Endpunkt (IMDS), ohne dass dieser Vorgang vom Node Managed Identity-Server (NMI) unterbrochen wird. Mit *aks-addon-exception* können AKS-Erstanbieter-Add-Ons, z. B. eine verwaltete AAD-Podidentität, betrieben werden, ohne dass eine manuelle Konfiguration von AzurePodIdentityException erfolgen muss. Optional können Sie ein AzurePodIdentityException-Element hinzufügen, entfernen und aktualisieren, indem Sie `az aks pod-identity exception add`, `az aks pod-identity exception delete`, `az aks pod-identity exception update` oder `kubectl` verwenden.

## <a name="run-a-sample-application"></a>Ausführen einer Beispielanwendung

Damit ein Pod eine verwaltete AAD-Podidentität verwenden kann, benötigt der Pod die Bezeichnung *aadpodidbinding* mit einem Wert, der mit einem Selektor einer *AzureIdentityBinding* übereinstimmt. Erstellen Sie eine `demo.yaml`-Datei mit dem folgenden Inhalt, um eine Beispielanwendung mit Verwendung einer verwalteten AAD-Podidentität auszuführen. Ersetzen Sie *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* durch die Werte aus den vorherigen Schritten. Ersetzen Sie *SUBSCRIPTION_ID* durch Ihre Abonnement-ID.

> [!NOTE]
> In den vorherigen Schritten haben Sie die Variablen *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* und *IDENTITY_RESOURCE_GROUP* erstellt. Sie können einen Befehl wie `echo` verwenden, um den für Variablen festgelegten Wert anzuzeigen, z. B. `echo $IDENTITY_NAME`.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Beachten Sie Folgendes: Die Poddefinition verfügt über die Bezeichnung *aadpodidbinding* mit einem Wert, der dem Namen der Podidentität entspricht, für die Sie im vorherigen Schritt `az aks pod-identity add` ausgeführt haben.

Stellen Sie `demo.yaml` in demselben Namespace wie Ihre Podidentität bereit, indem Sie `kubectl apply` verwenden:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Vergewissern Sie sich mit `kubectl logs`, dass die Ausführung der Beispielanwendung erfolgreich war.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Vergewissern Sie sich in den Protokollen, dass die Beschaffung des Tokens und der *GET*-Vorgang erfolgreich waren.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>Bereinigung

Um die verwaltete AAD-Podidentität aus Ihrem Cluster zu entfernen, müssen Sie die Beispielanwendung und die Podidentität aus dem Cluster entfernen. Dies bewirkt, dass die Identität entfernt wird.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
