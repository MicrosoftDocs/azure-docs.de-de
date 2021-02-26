---
title: 'Tutorial zu Kubernetes in Azure: Erstellen einer Containerregistrierung'
description: In diesem Azure Kubernetes Service-Tutorial (AKS) erstellen Sie eine Azure Container Registry-Instanz und laden das Containerimage einer Beispielanwendung hoch.
services: container-service
ms.topic: tutorial
ms.date: 01/31/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9f6ec14cea20192aef7d3010201e6613c5d03a9e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430963"
---
# <a name="tutorial-deploy-and-use-azure-container-registry"></a>Tutorial: Bereitstellen und Verwenden von Azure Container Registry

Bei Azure Container Registry (ACR) handelt es sich um eine private Registrierung für Containerimages. Mit einer privaten Containerregistrierung können Sie Ihre Anwendungen und benutzerdefinierten Code auf sichere Weise erstellen und bereitstellen. In diesem Tutorial – Teil 2 von 7 – stellen Sie eine ACR-Instanz bereit und übertragen mithilfe von Push ein Containerimage an diese. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen einer Azure Container Registry-Instanz (ACR)
> * Kennzeichnen eines Containerimages für ACR
> * Hochladen des Images in die ACR-Instanz
> * Anzeigen von Images in der Registrierung

In nachfolgenden Tutorials wird diese ACR-Instanz in einen Kubernetes-Cluster in AKS integriert, und eine Anwendung wird anhand des Images bereitgestellt.

## <a name="before-you-begin"></a>Voraussetzungen

Im [vorherigen Tutorial][aks-tutorial-prepare-app] wurde ein Containerimage für eine einfache Azure Voting-App erstellt. Wenn Sie das Image der Azure Voting-App noch nicht erstellt haben, kehren Sie zum [Tutorial 1 – Erstellen von Containerimages][aks-tutorial-prepare-app] zurück.

Für dieses Tutorial müssen Sie mindestens Version 2.0.53 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung

Zum Erstellen einer Azure Container Registry-Instanz benötigen Sie zunächst eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit dem Befehl [az acr create][az-acr-create] eine Azure Container Registry-Instanz, und geben Sie einen eigenen Registrierungsnamen an. Der Registrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Im weiteren Verlauf dieses Tutorials wird `<acrName>` als Platzhalter für den Namen der Containerregistrierung verwendet. Geben Sie Ihren eigenen eindeutigen Registrierungsnamen an. Die *Basic*-SKU ist ein kostenoptimierter Einstiegspunkt für Entwicklungszwecke, der ein ausgewogenes Verhältnis von Speicher und Durchsatz bietet.

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

## <a name="log-in-to-the-container-registry"></a>Anmelden bei der Containerregistrierung

Zur Verwendung der ACR-Instanz müssen Sie sich zunächst anmelden. Führen Sie den Befehl [az acr login][az-acr-login] aus, und geben Sie den eindeutigen Namen der Containerregistrierung aus dem vorherigen Schritt an.

```azurecli
az acr login --name <acrName>
```

Nach Abschluss des Vorgangs wird eine *Erfolgsmeldung* zurückgegeben.

## <a name="tag-a-container-image"></a>Markieren eines Containerimages

Verwenden Sie den Befehl [docker images][docker-images], um eine Liste Ihrer aktuellen lokalen Images anzuzeigen:

```console
docker images
```
Die Befehlsausgabe oben enthält eine Liste der aktuellen lokalen Images:

```output
REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        7 minutes ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Zur Verwendung des Containerimages *azure-vote-front* mit ACR muss das Image mit der Anmeldeserveradresse Ihrer Registrierung markiert werden. Dieses Tag wird beim Übertragen von Containerimages per Push in eine Imageregistrierung für das Routing verwendet.

Führen Sie den Befehl [az acr list][az-acr-list] aus, um die Anmeldeserveradresse abzurufen, und fragen Sie die *loginServer*-Adresse wie folgt ab:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Markieren Sie jetzt Ihr lokales Image *azure-vote-front* mit der *acrLoginServer*-Adresse der Containerregistrierung. Fügen Sie am Ende des Imagenamens *:v1* hinzu, um die Imageversion anzugeben:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v1
```

Führen Sie [docker images][docker-images] erneut aus, um sich zu vergewissern, dass die Tags angewendet wurden.

```console
docker images
```

Ein Image ist mit der Adresse der ACR-Instanz und einer Versionsnummer markiert.

```
REPOSITORY                                      TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front    v1                  84b41c268ad9        16 minutes ago      944MB
mycontainerregistry.azurecr.io/azure-vote-front v1                  84b41c268ad9        16 minutes ago      944MB
mcr.microsoft.com/oss/bitnami/redis             6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                      python3.6           a16ce562e863        6 weeks ago         944MB
```

## <a name="push-images-to-registry"></a>Übertragen von Images in die Registrierung per Push

Nachdem das Image erstellt und markiert wurde, können Sie das Image *azure-vote-front* per Push an Ihre ACR-Instanz übertragen. Verwenden Sie [docker push][docker-push], und geben Sie Ihre eigene *acrLoginServer*-Adresse wie folgt als Imagename an:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

Die Pushübertragung des Images an ACR kann einige Minuten dauern.

## <a name="list-images-in-registry"></a>Auflisten von Images in der Registrierung

Führen Sie den Befehl [az acr repository list][az-acr-repository-list] aus, um eine Liste der Images zurückzugeben, die per Push an Ihre ACR-Instanz übertragen wurden. Geben Sie Ihren eigenen `<acrName>`-Wert wie folgt an:

```azurecli
az acr repository list --name <acrName> --output table
```

Die folgende Beispielausgabe listet das Image *azure-vote-front* in der Registrierung als verfügbar auf:

```output
Result
----------------
azure-vote-front
```

Verwenden Sie den Befehl [az acr repository show-tags][az-acr-repository-show-tags] wie folgt, um die Tags für ein bestimmtes Image anzuzeigen:

```azurecli
az acr repository show-tags --name <acrName> --repository azure-vote-front --output table
```

Die folgende Beispielausgabe zeigt das in einem vorherigen Schritt markierte *v1*-Image:

```output
Result
--------
v1
```

Sie verfügen jetzt über ein Containerimage, das in einer privaten Azure Container Registry-Instanz gespeichert ist. Dieses Image wird im nächsten Tutorial aus ACR in einem Kubernetes-Cluster bereitgestellt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure Container Registry-Instanz erstellt und ein Image per Push für die Verwendung in einem AKS-Cluster übertragen. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer Azure Container Registry-Instanz (ACR)
> * Kennzeichnen eines Containerimages für ACR
> * Hochladen des Images in die ACR-Instanz
> * Anzeigen von Images in der Registrierung

Im nächsten Tutorial erfahren Sie, wie Sie einen Kubernetes-Cluster in Azure bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Kubernetes-Clustern][aks-tutorial-deploy-cluster]

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr
[az-acr-list]: /cli/azure/acr
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-list]: /cli/azure/acr#az-acr-list
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-tutorial-deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
