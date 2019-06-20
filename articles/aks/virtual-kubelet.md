---
title: Ausführen von Virtual Kubelet in einem Azure Kubernetes Service-Cluster (AKS)
description: Erfahren Sie, wie Sie Virtual Kubelet mit Azure Kubernetes Service (AKS) verwenden, um Linux- und Windows-Container in Azure Container Instances auszuführen.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: cc0c3becf21cb54b97a88e9ba35b38308af81a85
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475430"
---
# <a name="use-virtual-kubelet-with-azure-kubernetes-service-aks"></a>Verwenden von Virtual Kubelet mit Azure Kubernetes Service (AKS)

Azure Container Instances (ACI) bietet eine gehostete Umgebung zum Ausführen von Containern in Azure. Mit ACI ist es nicht erforderlich, die zugrunde liegende Computeinfrastruktur zu verwalten, denn Azure übernimmt diese Verwaltung für Sie. Wenn Sie Container in ACI ausführen, wird sekundengenau jeder ausgeführte Container abgerechnet.

Bei Verwendung des Anbieters für virtuelle Kubelets in Azure Container Instances können sowohl Linux- als auch Windows-Container auf einer Containerinstanz so eingeplant werden, als ob es sich um einen Standard-Kubernetes-Knoten handelt. Mit dieser Konfiguration können Sie sowohl die Vorteile von Kubernetes als auch die Verwaltungsfunktionen und den Kostenvorteil von Containerinstanzen nutzen.

> [!NOTE]
> AKS bietet jetzt integrierte Unterstützung für die Planung von Containern in ACI, die als *virtuelle Knoten* bezeichnet werden. Diese virtuellen Knoten unterstützen derzeit Linux-Containerinstanzen. Wenn Sie Windows-Containerinstanzen planen müssen, können Sie weiterhin Virtual Kubelet verwenden. Andernfalls sollten Sie virtuelle Knoten statt der in diesem Artikel beschriebenen manuellen Virtual Kubelets verwenden. Verwenden Sie für erste Schritte mit virtuellen Knoten die [Azure CLI][virtual-nodes-cli] oder das [Azure-Portal][virtual-nodes-portal].
>
> „Virtual Kubelet“ ist ein experimentelles Open Source-Projekt und sollte als solches verwendet werden. Weitere Informationen finden Sie im [Virtual Kubelet-GitHub-Projekt][vk-github]. Dort können Sie einen Beitrag leisten, Fragen stellen und mehr über virtuelle Kubelets erfahren.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Dokument wird davon ausgegangen, dass Sie über einen AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, finden Sie weitere Informationen im [Schnellstart zu Azure Kubernetes Service (AKS)][aks-quick-start].

Außerdem benötigen Sie die Azure CLI-Version **2.0.65** oder höher. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Um Virtual Kubelet zu installieren, installieren und konfigurieren Sie [Helm][aks-helm] in Ihrem AKS-Cluster. Stellen Sie sicher, dass Ihr Tiller [für die Verwendung mit Kubernetes RBAC konfiguriert](#for-rbac-enabled-clusters) ist, falls erforderlich.

### <a name="register-container-instances-feature-provider"></a>Registrieren des Anbieters des Features „Container Instances“

Wenn Sie den Dienst Azure Container Instance (ACI) noch nicht genutzt haben, registrieren Sie den Dienstanbieter in Ihrem Abonnement. Sie können den Status der ACI-Anbieterregistrierung mit dem Befehl [az provider list][az-provider-list] überprüfen, wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Der Anbieter *Microsoft.ContainerInstance* sollte als *Registriert* gemeldet werden, wie in der folgenden Beispielausgabe gezeigt:

```console
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

Wenn der Anbieter als *Nicht registriert* angezeigt wird, registrieren Sie den Anbieter mit dem [az provider register][az-provider-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

### <a name="for-rbac-enabled-clusters"></a>Für RBAC-fähige Cluster

Wenn Ihr AKS-Cluster RBAC-fähig ist, müssen Sie ein Dienstkonto und eine Rollenbindung für die Verwendung mit Tiller erstellen. Weitere Informationen finden Sie unter [Helm: Rollenbasierte Zugriffssteuerung][helm-rbac]. Um ein Dienstkonto und eine Rollenbindung zu erstellen, erstellen Sie eine Datei mit dem Namen *rbac-virtual-kubelet.yaml*, und fügen Sie dann die folgende Definition ein:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

Wenden Sie das Dienstkonto und die Bindung mit [kubectl apply][kubectl-apply] an, und geben Sie Ihre Datei *rbac-virtual-kubelet.yaml* an, wie im folgenden Beispiel gezeigt:

```console
$ kubectl apply -f rbac-virtual-kubelet.yaml

clusterrolebinding.rbac.authorization.k8s.io/tiller created
```

Konfigurieren Sie Helm für die Verwendung des tiller-Dienstkontos:

```console
helm init --service-account tiller
```

Sie können nun mit der Installation von Virtual Kubelet in Ihrem AKS-Cluster fortfahren.

## <a name="installation"></a>Installation

Verwenden Sie den Befehl [az so Install-Connector][aks-install-connector], um Virtual Kubelet zu installieren. Im folgenden Beispiel wird ein Linux- und ein Windows-Connector bereitgestellt.

```azurecli-interactive
az aks install-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

Diese Argumente sind für den Befehl [az aks install-connector][aks-install-connector] verfügbar.

| Argument: | BESCHREIBUNG | Erforderlich |
|---|---|:---:|
| `--connector-name` | Name des ACI-Connectors| Ja |
| `--name``-n` | Name des verwalteten Clusters | Ja |
| `--resource-group``-g` | Name der Ressourcengruppe | Ja |
| `--os-type` | Betriebssystemtyp der Containerinstanzen. Zulässige Werte: Sowohl Linux als auch Windows. Standardwert: Linux. | Nein |
| `--aci-resource-group` | Ressourcengruppe, in der die ACI-Containergruppen erstellt werden sollen. | Nein |
| `--location``-l` | Speicherort zum Erstellen der ACI-Containergruppen | Nein |
| `--service-principal` | Dienstprinzipal für die Authentifizierung bei Azure-APIs | Nein |
| `--client-secret` | Dem Dienstprinzipal zugeordnetes Geheimnis | Nein |
| `--chart-url` | URL eines Helm-Diagramms zur Installation eines ACI-Connectors | Nein |
| `--image-tag` | Imagetag des Containerimages für das virtuelle Kubelet | Nein |

## <a name="validate-virtual-kubelet"></a>Überprüfen der Virtual Kubelet-Installation

Um zu überprüfen, ob Virtual Kubelet installiert wurde, geben Sie eine Liste der Kubernetes-Knoten mit dem Befehl [kubectl get nodes][kubectl-get] zurück:

```console
$ kubectl get nodes

NAME                                             STATUS   ROLES   AGE   VERSION
aks-nodepool1-56577038-0                         Ready    agent   11m   v1.12.8
virtual-kubelet-virtual-kubelet-linux-eastus     Ready    agent   39s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
virtual-kubelet-virtual-kubelet-windows-eastus   Ready    agent   37s   v1.13.1-vk-v0.9.0-1-g7b92d1ee-dev
```

## <a name="run-linux-container"></a>Ausführen eines Linux-Containers

Erstellen Sie eine Datei namens „`virtual-kubelet-linux.yaml`“, und fügen Sie den folgenden YAML-Code ein. Beachten Sie, dass [nodeSelector][node-selector] und [toleration][toleration] verwendet werden, um den Container auf dem Knoten zu planen.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: linux
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Führen Sie die Anwendung mithilfe des Befehls [kubectl create][kubectl-create] aus.

```console
kubectl create -f virtual-kubelet-linux.yaml
```

Verwenden Sie den Befehl [kubectl get pods][kubectl-get] mit dem `-o wide`-Argument, um eine Liste von Pods mit dem geplanten Knoten auszugeben. Beachten Sie, dass der `aci-helloworld`-Pod auf dem `virtual-kubelet-virtual-kubelet-linux`-Knoten geplant wurde.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
aci-helloworld-7b9ffbf946-rx87g   1/1     Running   0          22s     52.224.147.210   virtual-kubelet-virtual-kubelet-linux-eastus
```

## <a name="run-windows-container"></a>Ausführen eines Windows-Containers

Erstellen Sie eine Datei namens „`virtual-kubelet-windows.yaml`“, und fügen Sie den folgenden YAML-Code ein. Beachten Sie, dass [nodeSelector][node-selector] und [toleration][toleration] verwendet werden, um den Container auf dem Knoten zu planen.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nanoserver-iis
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nanoserver-iis
  template:
    metadata:
      labels:
        app: nanoserver-iis
    spec:
      containers:
      - name: nanoserver-iis
        image: microsoft/iis:nanoserver
        ports:
        - containerPort: 80
      nodeSelector:
        beta.kubernetes.io/os: windows
        kubernetes.io/role: agent
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Equal
        value: azure
        effect: NoSchedule
```

Führen Sie die Anwendung mithilfe des Befehls [kubectl create][kubectl-create] aus.

```console
kubectl create -f virtual-kubelet-windows.yaml
```

Verwenden Sie den Befehl [kubectl get pods][kubectl-get] mit dem `-o wide`-Argument, um eine Liste von Pods mit dem geplanten Knoten auszugeben. Beachten Sie, dass der `nanoserver-iis`-Pod auf dem `virtual-kubelet-virtual-kubelet-windows`-Knoten geplant wurde.

```console
$ kubectl get pods -o wide

NAME                              READY   STATUS    RESTARTS   AGE     IP               NODE
nanoserver-iis-5d999b87d7-6h8s9   1/1     Running   0          47s     52.224.143.39    virtual-kubelet-virtual-kubelet-windows-eastus
```

## <a name="remove-virtual-kubelet"></a>Entfernen von Virtual Kubelet

Verwenden Sie den Befehl [az aks remove-connector][aks-remove-connector], um Virtual Kubelet zu entfernen. Ersetzen Sie die Argumentwerte durch die Namen des Connectors, des AKS-Clusters und der AKS-Clusterressourcengruppe.

```azurecli-interactive
az aks remove-connector \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --connector-name virtual-kubelet \
    --os-type Both
```

> [!NOTE]
> Wenn beim Entfernen der beiden Betriebssystem-Connectors Fehler auftreten oder wenn Sie nur den Windows- oder Linux-Betriebssystem-Connector entfernen möchten, können Sie manuell den Betriebssystemtyp angeben. Fügen Sie den `--os-type`-Parameter zum vorherigen `az aks remove-connector`-Befehl hinzu, und geben Sie `Windows` oder `Linux` an.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu möglichen Problemen mit dem virtuellen Kubelet finden Sie unter [Bekannte Probleme und Problemumgehungen][vk-troubleshooting]. Wenn Sie Probleme mit dem virtuellen Kubelet melden möchten, [öffnen Sie ein GitHub-Problem][vk-issues].

Weitere Informationen zu Virtual Kubelet finden Sie im [Virtual Kubelet-GitHub-Projekt][vk-github].

<!-- LINKS - internal -->
[aks-quick-start]: ./kubernetes-walkthrough.md
[aks-remove-connector]: /cli/azure/aks#az-aks-remove-connector
[az-container-list]: /cli/azure/aks#az-aks-list
[aks-install-connector]: /cli/azure/aks#az-aks-install-connector
[virtual-nodes-cli]: virtual-nodes-cli.md
[virtual-nodes-portal]: virtual-nodes-portal.md
[aks-helm]: kubernetes-helm.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[vk-github]: https://github.com/virtual-kubelet/virtual-kubelet
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[vk-troubleshooting]: https://github.com/virtual-kubelet/virtual-kubelet#known-quirks-and-workarounds
[vk-issues]: https://github.com/virtual-kubelet/virtual-kubelet/issues
