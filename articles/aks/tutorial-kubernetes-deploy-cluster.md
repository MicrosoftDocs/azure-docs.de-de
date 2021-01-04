---
title: 'Tutorial zu Kubernetes in Azure: Bereitstellen eines Clusters'
description: In diesem Azure Kubernetes Service-Tutorial (AKS) erstellen Sie einen AKS-Cluster und verwenden „kubectl“ für die Verbindung mit dem Kubernetes-Masterknoten.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0e034ebede39a3fd9046ced9716323d0c7d874df
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684069"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)

Kubernetes bietet eine verteilte Plattform für containerbasierte Anwendungen. Mit AKS können Sie schnell einen Kubernetes-Cluster erstellen, der für die Produktion bereit ist. In diesem Tutorial (Teil 3 von 7) wird ein Kubernetes-Cluster in AKS bereitgestellt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Bereitstellen eines Kubernetes-AKS-Clusters, der sich bei einer Azure-Containerregistrierung authentifizieren kann
> * Installieren der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfigurieren von „kubectl“ für die Verbindung mit Ihrem AKS-Cluster

In den weiteren Tutorials wird die Anwendung Azure Vote im Cluster bereitgestellt, skaliert und aktualisiert.

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde ein Containerimage erstellt und in eine Azure Container Registry-Instanz hochgeladen. Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, beginnen Sie mit [Tutorial 1: Erstellen von Containerimages][aks-tutorial-prepare-app].

Für dieses Tutorial müssen Sie mindestens Version 2.0.53 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Erstellen eines Kubernetes-Clusters

AKS-Cluster können die rollenbasierte Zugriffssteuerung von Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) verwenden. Mit diesen Steuerungen können Sie den Zugriff auf Ressourcen basierend auf Rollen definieren, die Benutzern zugewiesen sind. Berechtigungen werden kombiniert, wenn einem Benutzer mehrere Rollen zugewiesen sind, und Berechtigungen können auf einen einzelnen Namespace begrenzt sein oder für den gesamten Cluster gelten. Standardmäßig wird Kubernetes RBAC bei der Erstellung eines AKS-Clusters von der Azure CLI automatisch aktiviert.

Erstellen Sie mit [az aks create][] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* in der Ressourcengruppe mit dem Namen *myResourceGroup* erstellt. Diese Ressourcengruppe wurde im [vorherigen Tutorial][aks-tutorial-prepare-acr] in der Region *eastus* erstellt. Im folgenden Beispiel wird keine Region angegeben, sodass der AKS-Cluster auch in der Region *eastus* erstellt wird. Weitere Informationen zu Ressourcenbeschränkungen und regionale Verfügbarkeit für AKS finden Sie unter [Kontingente, Größeneinschränkungen für virtuelle Computer und regionale Verfügbarkeit in Azure Kubernetes Service (AKS)][quotas-skus-regions].

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird automatisch ein Azure Active Directory-Dienstprinzipal erstellt, da Sie keinen angegeben haben. Hier wird diesem Dienstprinzipal die [Berechtigung zum Pullen von Images][container-registry-integration] aus der ACR-Instanz (Azure Container Registry) gewährt, die Sie im vorherigen Tutorial erstellt haben. Beachten Sie, dass Sie anstelle eines Dienstprinzipals zur einfacheren Verwaltung eine [verwaltete Identität](use-managed-identity.md) verwenden können.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Sie können auch manuell einen Dienstprinzipal zum Pullen von Images aus ACR konfigurieren. Weitere Informationen finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](../container-registry/container-registry-auth-service-principal.md) oder unter [Abrufen von Images aus einer Azure-Containerregistrierung per Pull in einem Kubernetes-Cluster](../container-registry/container-registry-auth-kubernetes.md).

Nach einigen Minuten ist die Bereitstellung abgeschlossen, und es werden Informationen zur AKS-Bereitstellung im JSON-Format zurückgegeben.

> [!NOTE]
> Um zu gewährleisten, dass Ihr Cluster zuverlässig funktioniert, sollten Sie mindestens zwei Knoten verwenden.

## <a name="install-the-kubernetes-cli"></a>Installieren der Kubernetes-Befehlszeilenschnittstelle

Zum Herstellen der Verbindung mit dem Kubernetes-Cluster auf Ihrem lokalen Computer verwenden Sie den Kubernetes-Befehlszeilenclient [kubectl][kubectl].

Wenn Sie Azure Cloud Shell verwenden, ist `kubectl` bereits installiert. Mit dem Befehl [az aks install-cli][] können Sie ihn auch lokal installieren:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Herstellen einer Verbindung mit dem Cluster mithilfe von „kubectl“

Mit dem Befehl [az aks get-credentials][] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den AKS-Cluster namens *myAKSCluster* in *myResourceGroup* abgerufen:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Überprüfen Sie die Verbindung mit Ihrem Cluster mithilfe des Befehls [kubectl get nodes][kubectl-get], um eine Liste der Clusterknoten zu erhalten:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-12345678-0   Ready    agent   32m   v1.14.8
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde ein Kubernetes-Cluster in AKS bereitgestellt, und Sie haben `kubectl` für die Verbindung damit konfiguriert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Bereitstellen eines Kubernetes-AKS-Clusters, der sich bei einer Azure-Containerregistrierung authentifizieren kann
> * Installieren der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfigurieren von „kubectl“ für die Verbindung mit Ihrem AKS-Cluster

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie eine Anwendung im Cluster bereitgestellt wird.

> [!div class="nextstepaction"]
> [Bereitstellen einer Anwendung in Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
