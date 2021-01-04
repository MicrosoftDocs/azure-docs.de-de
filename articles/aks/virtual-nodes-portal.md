---
title: Erstellen von virtuellen Knoten mit dem Portal in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie das Azure-Portal verwenden, um einen Azure Kubernetes Service-Cluster (AKS) zu erstellen, der virtuelle Knoten zum Ausführen von Pods verwendet.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 7d49499b39c562aeff20d163fc86401d8c1f4a06
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579163"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Erstellen und Konfigurieren eines AKS-Clusters zur Verwendung von virtuellen Knoten im Azure-Portal

Dieser Artikel zeigt, wie Sie im Azure-Portal die virtuellen Netzwerkressourcen und einen AKS-Cluster mit aktivierten virtuellen Knoten erstellen und konfigurieren.

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

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-an-aks-cluster"></a>Erstellen eines AKS-Clusters

Klicken Sie im Azure-Portal oben links auf **Ressource erstellen** > **Kubernetes Service**.

Konfigurieren Sie auf der Seite **Grundlagen** die folgenden Optionen:

- *PROJEKTDETAILS*: Wählen Sie ein Azure-Abonnement und dann eine Azure-Ressourcengruppe aus, bzw. erstellen Sie eine Ressourcengruppe, z.B. *myResourceGroup*. Geben Sie unter **Kubernetes cluster name** (Name des Kubernetes-Clusters) einen Namen ein, etwa *myAKSCluster*.
- *CLUSTERDETAILS*: Wählen Sie eine Region, eine Kubernetes-Version und ein DNS-Namenspräfix für den AKS-Cluster aus.
- *PRIMÄRER KNOTENPOOL:* Wählen Sie eine VM-Größe für die AKS-Knoten aus. Die VM-Größe kann **nicht** geändert werden, sobald ein AKS-Cluster bereitgestellt wurde.
     - Wählen Sie die Anzahl von Knoten für die Bereitstellung im Cluster aus. Legen Sie für diesen Artikel **Knotenanzahl** auf *1* fest. Die Knotenanzahl **kann** nach der Clusterbereitstellung angepasst werden.

Klicken Sie auf **Weiter: Skalieren**.

Wählen Sie auf der Seite **Skalieren** unter **Virtuelle Knoten** die Option *Aktiviert* aus.

![Erstellen von AKS-Clustern und Aktivieren der virtuellen Knoten](media/virtual-nodes-portal/enable-virtual-nodes.png)

Standardmäßig wird ein Azure Active Directory-Dienstprinzipal erstellt. Dieser Dienstprinzipal wird für die Clusterkommunikation und die Integration in andere Azure-Dienste verwendet. Alternativ können Sie anstelle eines Dienstprinzipals eine verwaltete Identität für Berechtigungen verwenden. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](use-managed-identity.md).

Der Cluster ist auch für den erweiterten Netzwerkbetrieb konfiguriert. Die virtuellen Knoten sind so konfiguriert, dass sie ihr eigenes virtuelles Azure-Netzwerksubnetz verwenden. Dieses Subnetz verfügt über delegierte Berechtigungen, um Azure-Ressourcen zwischen dem AKS-Cluster zu verbinden. Wenn Sie noch kein Subnetz delegiert haben, erstellt und konfiguriert das Azure-Portal das virtuelle Netzwerk und Subnetz von Azure für die Verwendung mit den virtuellen Knoten.

Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach Abschluss der Validierung **Erstellen** aus.

Es dauert einige Minuten, bis der AKS-Clusters erstellt wurde und für die Verwendung bereit ist.

## <a name="connect-to-the-cluster"></a>Herstellen einer Verbindung mit dem Cluster

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Verwenden Sie zum Verwalten eines Kubernetes-Clusters den Kubernetes-Befehlszeilenclient [kubectl][kubectl]. Der `kubectl`-Client ist in Azure Cloud Shell vorinstalliert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock die Option **Ausprobieren** aus. Sie können Cloud Shell auch auf einer separaten Browserregisterkarte starten, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren**, um die Blöcke mit dem Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen.

Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für das Herstellen einer Verbindung mit Ihrem Kubernetes-Cluster konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den Clusternamen *myAKSCluster* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get][kubectl-get], um eine Liste der Clusterknoten zurückzugeben.

```console
kubectl get nodes
```

Die folgende Beispielausgabe zeigt den erstellten einzelnen VM-Knoten und dann den virtuellen Knoten für Linux, *virtual-node-aci-linux*:

```output
NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Bereitstellen einer Beispiel-App

Erstellen Sie in der Azure Cloud Shell eine Datei mit dem Namen `virtual-node.yaml`, und fügen Sie den folgenden YAML-Code ein. Um den Container auf dem Knoten zu planen, werden Werte für [nodeSelector][node-selector] und [toleration][toleration] definiert. Mit dieser Einstellungen kann der Pod auf dem virtuellen Knoten geplant und bestätigt werden, dass das Feature erfolgreich aktiviert wurde.

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
```

Führen Sie die Anwendung mithilfe des Befehls [kubectl apply][kubectl-apply] aus.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

Verwenden Sie den Befehl [kubectl get pods][kubectl-get] mit dem `-o wide`-Argument, um eine Liste von Pods und den geplanten Knoten auszugeben. Beachten Sie, dass der `virtual-node-helloworld`-Pod auf dem `virtual-node-linux`-Knoten geplant wurde.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Dem Pod wird von dem Subnetz des virtuellen Azure-Netzwerks, das für die Verwendung mit virtuellen Knoten delegiert wurde, eine interne IP-Adresse zugewiesen.

> [!NOTE]
> Wenn Sie Images verwenden, die in der Azure Container Registry gespeichert sind, konfigurieren und verwenden Sie [ein Kubernetes-Geheimnis][acr-aks-secrets]. Eine aktuelle Einschränkung der virtuellen Knoten ist, dass Sie die integrierte Authentifizierung des Azure AD-Dienstprinzipals nicht verwenden können. Wenn Sie kein Geheimnis verwenden, können auf virtuellen Knoten geplante Pods nicht starten und melden den Fehler `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testen des Pods des virtuellen Knotens

Um den Pod zu testen, der auf dem virtuellen Knoten ausgeführt werden soll, wechseln Sie in einem Webclient zur Demoanwendung. Da dem Pod eine interne IP-Adresse zugewiesen wurde, können Sie diese Konnektivität schnell von einem anderen Pod im AKS-Cluster aus testen. Erstellen Sie einen Testpod, und fügen Sie an diesen eine Terminalsitzung an:

```console
kubectl run -it --rm virtual-node-test --image=debian
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

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde ein Pod im virtuellen Knoten geplant, und dem Pod wurde ein private interne IP-Adresse zugewiesen. Sie können stattdessen auch eine Dienstbereitstellung erstellen und den Datenverkehr über ein Lastenausgleichsmodul oder einen Eingangscontroller an Ihren Pod weiterleiten. Weitere Informationen finden Sie unter [Erstellen eines einfachen Eingangscontrollers in AKS][aks-basic-ingress].

Virtuelle Knoten sind eine Komponente einer Skalierungslösung in AKS. Weitere Informationen zu Skalierungslösungen finden Sie in den folgenden Artikeln:

- [Verwenden der horizontalen automatischen Kubernetes-Podskalierung][aks-hpa]
- [Verwenden der automatischen Kubernetes-Clusterskalierung][aks-cluster-autoscaler]
- [Sehen Sie sich das Beispiel für die automatische Skalierung für virtuelle Knoten an][virtual-node-autoscale]
- [Erfahren Sie mehr über die Virtual Kubelet Open Source-Bibliothek][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider?view=azure-cli-latest#az-provider-list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az-provider-register
