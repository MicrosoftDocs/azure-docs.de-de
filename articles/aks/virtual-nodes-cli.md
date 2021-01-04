---
title: Erstellen von virtuellen Knoten mithilfe der Azure CLI
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie die Azure CLI verwenden, um einen AKS-Cluster (Azure Kubernetes Service) zu erstellen, der virtuelle Knoten zum Ausführen von Pods verwendet.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: a655c8c145b4f3812dae9f1a4ec1e5eebbe44809
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348473"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Erstellen und Konfigurieren eines AKS-Clusters zur Verwendung von virtuellen Knoten mithilfe der Azure CLI

Dieser Artikel zeigt, wie Sie die virtuellen Netzwerkressourcen und den AKS-Cluster mithilfe der Azure-Befehlszeilenschnittstelle erstellen und konfigurieren und dann die virtuellen Knoten aktivieren.

> [!NOTE]
> In [diesem Artikel](virtual-nodes.md) erhalten Sie eine Übersicht über die regionale Verfügbarkeit und die Einschränkungen bei der Verwendung virtueller Knoten.

## <a name="before-you-begin"></a>Voraussetzungen

Die virtuellen Knoten ermöglichen die Netzwerkkommunikation zwischen Pods, die in Azure Container Instances (ACI) und dem AKS-Cluster ausgeführt werden. Um diese Kommunikation bereitzustellen, wird ein virtuelles Subnetz erstellt, und delegierte Berechtigungen werden zugewiesen. Virtuelle Knoten funktionieren nur in AKS-Clustern, die mit *erweiterten* Netzwerkfunktionen (Azure CNI) erstellt wurden. Standardmäßig werden AKS-Cluster mit *grundlegenden* Netzwerkfunktionen (kubenet) erstellt. Dieser Artikel zeigt, wie Sie ein virtuelles Netzwerk und virtuelle Subnetze erstellen und dann einen AKS-Cluster bereitstellen, der erweiterte Netzwerkfunktionen verwendet.

Wenn Sie ACI noch nicht genutzt haben, registrieren Sie den Dienstanbieter mit Ihrem Abonnement. Sie können den Status der ACI-Anbieterregistrierung mit dem Befehl [az provider list][az-provider-list] überprüfen, wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

Der Anbieter *Microsoft.ContainerInstance* sollte als *Registriert* gemeldet werden, wie in der folgenden Beispielausgabe gezeigt:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Wenn der Anbieter als *Nicht registriert* angezeigt wird, registrieren Sie den Anbieter mit dem [az provider register][az-provider-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock die Option **Ausprobieren** aus. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** , um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

Wenn Sie es vorziehen, die Befehlszeilenschnittstelle lokal zu installieren und zu verwenden, müssen Sie für diesen Artikel die Azure CLI-Version 2.0.49 oder verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *westus* erstellt.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit dem Befehl [az network vnet create][az-network-vnet-create] ein virtuelles Netzwerk. Das folgende Beispiel erstellt ein virtuelles Netzwerk namens *myVnet* mit dem Adresspräfix *10.0.0.0/8* und einem Subnetz namens *myAKSSubnet*. Das Adresspräfix dieses Subnetzes wird standardmäßig auf *10.240.0.0/16* festgelegt:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Erstellen Sie jetzt mit dem Befehl [az network vnet subnet create][az-network-vnet-subnet-create] ein weiteres Subnetz für virtuelle Knoten. Das folgende Beispiel erstellt ein Subnetz namens *myVirtualNodeSubnet* mit dem Adresspräfix *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>Erstellen eines Dienstprinzipals oder Verwenden einer verwalteten Identität

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird ein Azure Active Directory-Dienstprinzipal verwendet. Dieser Dienstprinzipal kann automatisch über die Azure CLI oder das Azure-Portal erstellt werden, oder Sie können vorab einen Dienstprinzipal erstellen und dann weitere Berechtigungen zuweisen. Sie können anstelle eines Dienstprinzipals auch eine verwaltete Identität für Berechtigungen verwenden. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](use-managed-identity.md).

Erstellen Sie mit dem Befehl [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] einen Dienstprinzipal. Mit dem Parameter `--skip-assignment` wird die Zuweisung zusätzlicher Berechtigungen eingeschränkt.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Notieren Sie sich die App-ID ( *appId* ) und das Kennwort ( *password* ). Diese Werte werden in den folgenden Schritten verwendet.

## <a name="assign-permissions-to-the-virtual-network"></a>Zuweisen von Berechtigungen zum virtuellen Netzwerk

Damit Ihr Cluster das virtuelle Netzwerk verwenden und verwalten kann, müssen Sie dem AKS-Dienstprinzipal die richtigen Berechtigungen zum Verwenden der Netzwerkressourcen gewähren.

Rufen Sie zunächst mit dem Befehl [az network vnet show][az-network-vnet-show] die ID der virtuellen Netzwerkressource ab:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Erstellen Sie mit dem Befehl [az role assignment create][az-role-assignment-create] eine Rollenzuweisung, um dem AKS-Cluster den richtigen Zugriff zur Verwendung des virtuellen Netzwerks zu gewähren. Ersetzen Sie `<appId`> und `<vnetId>` durch die Werte, die in den beiden vorherigen Schritten erfasst wurden.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Sie stellen einen AKS-Cluster in dem AKS-Subnetz bereit, das in einem vorherigen Schritt erstellt wurde. Rufen Sie mit [az network vnet subnet show][az-network-vnet-subnet-show] die ID dieses Subnetzes ab:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Erstellen Sie mithilfe des Befehls [az aks create][az-aks-create] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* mit einem Knoten erstellt. Ersetzen Sie `<subnetId>` durch die im vorherigen Schritt abgerufene ID und anschließend `<appId>` und `<password>` durch die im vorherigen Abschnitt erfassten Werte.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Nach einigen Minuten wird der Befehl abgeschlossen und gibt Informationen über den Cluster im JSON-Format zurück.

## <a name="enable-virtual-nodes-addon"></a>Virtuelle Knoten aktivieren-Add-On

Um die virtuellen Knoten zu aktivieren, verwenden Sie jetzt den Befehl [az aks enable-addons][az-aks-enable-addons]. Das folgende Beispiel verwendet das Subnetz *myVirtualNodeSubnet* , das in einem vorherigen Schritt erstellt wurde:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Dieser Schritt dient dazu, Anmeldeinformationen herunterzuladen und die Kubernetes-Befehlszeilenschnittstelle für ihre Verwendung zu konfigurieren.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get][kubectl-get], um eine Liste der Clusterknoten zurückzugeben.

```console
kubectl get nodes
```

Die folgende Beispielausgabe zeigt den erstellten einzelnen VM-Knoten und dann den virtuellen Knoten für Linux, *virtual-node-aci-linux* :

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Bereitstellen einer Beispiel-App

Erstellen Sie eine Datei namens „`virtual-node.yaml`“, und fügen Sie den folgenden YAML-Code ein. Um den Container auf dem Knoten zu planen, werden Werte für [nodeSelector][node-selector] und [toleration][toleration] definiert.

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
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Führen Sie die Anwendung mithilfe des Befehls [kubectl apply][kubectl-apply] aus.

```console
kubectl apply -f virtual-node.yaml
```

Verwenden Sie den Befehl [kubectl get pods][kubectl-get] mit dem `-o wide`-Argument, um eine Liste von Pods und den geplanten Knoten auszugeben. Beachten Sie, dass der `aci-helloworld`-Pod auf dem `virtual-node-aci-linux`-Knoten geplant wurde.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Dem Pod wird von dem Subnetz des virtuellen Azure-Netzwerks, das für die Verwendung mit virtuellen Knoten delegiert wurde, eine interne IP-Adresse zugewiesen.

> [!NOTE]
> Wenn Sie Images verwenden, die in der Azure Container Registry gespeichert sind, konfigurieren und verwenden Sie [ein Kubernetes-Geheimnis][acr-aks-secrets]. Eine aktuelle Einschränkung der virtuellen Knoten ist, dass Sie die integrierte Authentifizierung des Azure AD-Dienstprinzipals nicht verwenden können. Wenn Sie kein Geheimnis verwenden, können auf virtuellen Knoten geplante Pods nicht starten und melden den Fehler `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testen des Pods des virtuellen Knotens

Um den Pod zu testen, der auf dem virtuellen Knoten ausgeführt werden soll, wechseln Sie in einem Webclient zur Demoanwendung. Da dem Pod eine interne IP-Adresse zugewiesen wurde, können Sie diese Konnektivität schnell von einem anderen Pod im AKS-Cluster aus testen. Erstellen Sie einen Testpod, und fügen Sie an diesen eine Terminalsitzung an:

```console
kubectl run -it --rm testvk --image=debian
```

Installieren Sie `curl` mit `apt-get` im Pod:

```console
apt-get update && apt-get install -y curl
```

Greifen Sie mithilfe von `curl` auf die Adresse Ihres Pods zu, z.B. *http://10.241.0.4* . Geben Sie Ihre eigene interne IP-Adresse an, die im vorherigen `kubectl get pods`-Befehl gezeigt wurde:

```console
curl -L http://10.241.0.4
```

Die Demoanwendung wird angezeigt, wie in der folgenden verkürzten Beispielausgabe veranschaulicht:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Schließen Sie die Terminalsitzung mit Ihrem Testpod mit `exit`. Wenn die Sitzung beendet ist, wird der Pod gelöscht.

## <a name="remove-virtual-nodes"></a>Entfernen von virtuellen Knoten

Wenn Sie die virtuellen Knoten nicht mehr verwenden möchten, können Sie sie mit dem Befehl [az aks disable-addons][az aks disable-addons] deaktivieren. 

Navigieren Sie ggf. zu [https://shell.azure.com](https://shell.azure.com), um Azure Cloud Shell in Ihrem Browser zu öffnen.

Löschen Sie zuerst den `aci-helloworld`-Pod, der auf dem virtuellen Knoten ausgeführt wird:

```console
kubectl delete -f virtual-node.yaml
```

Der folgende Beispielbefehl deaktiviert die virtuellen Linux-Knoten:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Entfernen Sie jetzt die virtuellen Netzwerkressourcen und die Ressourcengruppe:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde ein Pod im virtuellen Knoten geplant, und dem Pod wurde ein private interne IP-Adresse zugewiesen. Sie können stattdessen auch eine Dienstbereitstellung erstellen und den Datenverkehr über ein Lastenausgleichsmodul oder einen Eingangscontroller an Ihren Pod weiterleiten. Weitere Informationen finden Sie unter [Erstellen eines einfachen Eingangscontrollers in AKS][aks-basic-ingress].

Virtuelle Knoten sind oft eine Komponente einer Skalierungslösung in AKS. Weitere Informationen zu Skalierungslösungen finden Sie in den folgenden Artikeln:

- [Verwenden der horizontalen automatischen Kubernetes-Podskalierung][aks-hpa]
- [Verwenden der automatischen Kubernetes-Clusterskalierung][aks-cluster-autoscaler]
- [Sehen Sie sich das Beispiel für die automatische Skalierung für virtuelle Knoten an][virtual-node-autoscale]
- [Erfahren Sie mehr über die Virtual Kubelet Open Source-Bibliothek][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
