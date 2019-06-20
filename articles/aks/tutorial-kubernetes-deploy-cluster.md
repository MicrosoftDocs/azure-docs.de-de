---
title: 'Tutorial zu Kubernetes in Azure: Bereitstellen eines Clusters'
description: In diesem Azure Kubernetes Service-Tutorial (AKS) erstellen Sie einen AKS-Cluster und verwenden „kubectl“ für die Verbindung mit dem Kubernetes-Masterknoten.
services: container-service
author: tylermsft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: twhitney
ms.custom: mvc
ms.openlocfilehash: 020b5935595506732c1c1425179741c45f8326d7
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304467"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Bereitstellen eines Azure Kubernetes Service-Clusters (AKS)

Kubernetes bietet eine verteilte Plattform für containerbasierte Anwendungen. Mit AKS können Sie schnell einen Kubernetes-Cluster erstellen, der für die Produktion bereit ist. In diesem Tutorial (Teil 3 von 7) wird ein Kubernetes-Cluster in AKS bereitgestellt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Dienstprinzipals für Ressourceninteraktionen
> * Bereitstellen eines Kubernetes-AKS-Clusters
> * Installieren der Kubernetes-Befehlszeilenschnittstelle (kubectl)
> * Konfigurieren von „kubectl“ für die Verbindung mit Ihrem AKS-Cluster

In den weiteren Tutorials wird die Anwendung Azure Vote im Cluster bereitgestellt, skaliert und aktualisiert.

## <a name="before-you-begin"></a>Voraussetzungen

In vorherigen Tutorials wurde ein Containerimage erstellt und in eine Azure Container Registry-Instanz hochgeladen. Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, beginnen Sie mit [Tutorial 1: Erstellen von Containerimages][aks-tutorial-prepare-app].

Für dieses Tutorial müssen Sie mindestens Version 2.0.53 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren von Azure CLI 2.0][azure-cli-install] Informationen dazu.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Damit ein AKS-Cluster mit anderen Azure-Ressourcen interagieren kann, wird ein Azure Active Directory-Dienstprinzipal verwendet. Dieser Dienstprinzipal kann automatisch über die Azure CLI oder das Azure-Portal erstellt werden, oder Sie können vorab einen Dienstprinzipal erstellen und dann weitere Berechtigungen zuweisen. In diesem Tutorial erstellen Sie einen Dienstprinzipal, gewähren Zugriff auf die ACR-Instanz (Azure Container Registry), die im vorherigen Tutorial erstellt wurde, und erstellen anschließend einen AKS-Cluster.

Erstellen Sie mit dem Befehl [az ad sp create-for-rbac][] einen Dienstprinzipal. Mit dem Parameter `--skip-assignment` wird die Zuweisung zusätzlicher Berechtigungen eingeschränkt. Standardmäßig ist dieser Dienstprinzipal ein Jahr lang gültig.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Notieren Sie sich die App-ID (*appId*) und das Kennwort (*password*). Diese Werte werden in den folgenden Schritten verwendet.

## <a name="configure-acr-authentication"></a>Konfigurieren der ACR-Authentifizierung

Für den Zugriff auf Images, die in ACR gespeichert sind, müssen Sie dem AKS-Dienstprinzipal die richtigen Rechte gewähren, mit denen für Images der Pullvorgang aus ACR möglich ist.

Rufen Sie zuerst mit [az acr show][] die ACR-Ressourcen-ID ab. Aktualisieren Sie den Registrierungsnamen `<acrName>` auf den Namen Ihrer ACR-Instanz und der Ressourcengruppe, in der sich die ACR-Instanz befindet.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Weisen Sie mit dem Befehl `AcrPull`az role assignment create[ die Rolle ][] zu, um den richtigen Zugriff für den AKS-Cluster zu gewähren und das Ausführen eines Pulls für die in ACR gespeicherten Images zu ermöglichen. Ersetzen Sie `<appId`> und `<acrId>` durch die Werte, die in den beiden vorherigen Schritten erfasst wurden.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Erstellen eines Kubernetes-Clusters

AKS-Cluster können rollenbasierte Zugriffssteuerungen (Role-Based Access Control, RBAC) für Kubernetes verwenden. Mit diesen Steuerungen können Sie den Zugriff auf Ressourcen basierend auf Rollen definieren, die Benutzern zugewiesen sind. Berechtigungen werden kombiniert, wenn einem Benutzer mehrere Rollen zugewiesen sind, und Berechtigungen können auf einen einzelnen Namespace begrenzt sein oder für den gesamten Cluster gelten. Die Azure CLI aktiviert RBAC bei der Erstellung eines AKS-Clusters standardmäßig.

Erstellen Sie mit [az aks create][] einen AKS-Cluster. Im folgenden Beispiel wird ein Cluster mit dem Namen *myAKSCluster* in der Ressourcengruppe mit dem Namen *myResourceGroup* erstellt. Diese Ressourcengruppe wurde im [vorherigen Tutorial][aks-tutorial-prepare-acr] erstellt. Geben Sie Ihre eigenen Werte für `<appId>` und `<password>` aus dem vorherigen Schritt an, in dem der Dienstprinzipal erstellt wurde.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Nach einigen Minuten ist die Bereitstellung abgeschlossen, und es werden Informationen zur AKS-Bereitstellung im JSON-Format zurückgegeben.

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

Zum Überprüfen der Verbindung mit dem Cluster führen Sie den Befehl [kubectl get nodes][kubectl-get] aus:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde ein Kubernetes-Cluster in AKS bereitgestellt, und Sie haben `kubectl` für die Verbindung damit konfiguriert. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen eines Dienstprinzipals für Ressourceninteraktionen
> * Bereitstellen eines Kubernetes-AKS-Clusters
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
[ die Rolle ]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
