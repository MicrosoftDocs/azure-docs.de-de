---
title: 'Tutorial: Vorbereiten der Containerregistrierung für die Imagebereitstellung'
description: Tutorial für Azure Container Instances (Teil 2 von 3) – Vorbereiten einer Azure-Containerregistrierung und Übertragen eines Images per Pushvorgang
ms.topic: tutorial
ms.date: 12/18/2019
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2eda960c53fc7ba851ffcfbe96bd8e9a48844910
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746949"
---
# <a name="tutorial-create-an-azure-container-registry-and-push-a-container-image"></a>Tutorial: Erstellen einer Azure-Containerregistrierung und Übertragen eines Containerimages per Pushvorgang

Dies ist der zweite Teil eines dreiteiligen Tutorials. In [Teil 1](container-instances-tutorial-prepare-app.md) des Tutorials wurde ein Docker-Containerimage für eine Node.js-Webanwendung erstellt. In diesem Tutorial übertragen Sie das Image per Pushvorgang an die Azure Container Registry-Instanz. Kehren Sie zu [Tutorial 1 – Erstellen von Containerimages](container-instances-tutorial-prepare-app.md) zurück, falls Sie das Containerimage noch nicht erstellt haben.

Azure Container Registry ist Ihre private Docker-Registrierung in Azure. Dieser zweite Teil der Tutorialreihe umfasst Folgendes:

> [!div class="checklist"]
> * Erstellen einer Azure Container Registry-Instanz mit der Azure-Befehlszeilenschnittstelle
> * Markieren eines Containerimages für Ihre Azure Container Registry-Instanz
> * Hochladen des Images in Ihre Registrierung

Im nächsten Artikel (dem letzten Tutorial der Reihe) stellen Sie den Container aus Ihrer privaten Registrierung für Azure Container Instances bereit.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="tag-container-image"></a>Markieren von Containerimages

Zum Übertragen eines Containerimages an eine private Registrierung wie Azure Container Registry müssen Sie das Image zuerst mit dem vollständigen Namen des Anmeldeservers für die Registrierung versehen.

Rufen Sie zuerst den vollständigen Namen des Anmeldeservers für Ihre Containerregistrierung ab. Führen Sie den folgenden Befehl [az acr show][az-acr-show] aus, und ersetzen Sie `<acrName>` durch den Namen der gerade erstellten Registrierung:

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Dies sieht beispielsweise wie folgt aus, wenn Ihre Registrierung den Namen *mycontainerregistry082* hat:

```azurecli
az acr show --name mycontainerregistry082 --query loginServer --output table
```

```output
Result
------------------------
mycontainerregistry082.azurecr.io
```

Zeigen Sie die Liste mit Ihren lokalen Images mit dem Befehl [docker images][docker-images] an:

```bash
docker images
```

Neben den anderen Images, die auf Ihrem Computer vorhanden sind, sollte das Image *aci-tutorial-app* angezeigt werden, das Sie im [vorherigen Tutorial](container-instances-tutorial-prepare-app.md) erstellt haben:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 minutes ago    68.1 MB
```

Kennzeichnen Sie das Image *aci-tutorial-app* mit dem Anmeldeserver der Containerregistrierung. Fügen Sie außerdem das Tag `:v1` am Ende des Imagenamens hinzu, um die Versionsnummer für das Image anzugeben. Ersetzen Sie `<acrLoginServer>` durch das Ergebnis des zuvor ausgeführten Befehls [az acr show][az-acr-show].

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Führen Sie `docker images` erneut aus, um zu überprüfen, ob die Kennzeichnung erfolgreich war:

```console
$ docker images
REPOSITORY                                            TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app                                      latest    5c745774dfa9    39 minutes ago    68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app    v1        5c745774dfa9    7 minutes ago     68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Übertragen des Images zu Azure Container Registry mithilfe von Push

Nachdem Sie das Image *aci-tutorial-app* jetzt mit dem vollständigen Namen des Anmeldeservers Ihrer privaten Registrierung versehen haben, können Sie es mit dem Befehl [docker push][docker-push] per Pushvorgang an die Registrierung übertragen. Ersetzen Sie `<acrLoginServer>` durch den vollständigen Anmeldeservernamen, den Sie im vorherigen Schritt abgerufen haben.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

Der `push`-Vorgang dauert abhängig von der Internetverbindung zwischen einigen Sekunden und wenigen Minuten. Die Ausgabe sieht ungefähr wie folgt aus:

```console
$ docker push mycontainerregistry082.azurecr.io/aci-tutorial-app:v1
The push refers to a repository [mycontainerregistry082.azurecr.io/aci-tutorial-app]
3db9cac20d49: Pushed
13f653351004: Pushed
4cd158165f4d: Pushed
d8fbd47558a8: Pushed
44ab46125c35: Pushed
5bef08742407: Pushed
v1: digest: sha256:ed67fff971da47175856505585dcd92d1270c3b37543e8afd46014d328f05715 size: 1576
```

## <a name="list-images-in-azure-container-registry"></a>Auflisten der Images in Azure Container Registry

Um sicherzustellen, dass sich das gerade übertragene Image wirklich in Ihrer Azure-Containerregistrierung befindet, können Sie die in der Registrierung enthaltenen Images mit dem Befehl [az acr repository list][az-acr-repository-list] auflisten. Ersetzen Sie `<acrName>` durch den Namen Ihrer Containerregistrierung.

```azurecli
az acr repository list --name <acrName> --output table
```

Beispiel:

```azurecli
az acr repository list --name mycontainerregistry082 --output table
```

```output
Result
----------------
aci-tutorial-app
```

Verwenden Sie den Befehl [az acr repository show-tags][az-acr-repository-show-tags], um die *Tags* für ein bestimmtes Image anzuzeigen.

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Die Ausgabe sollte etwa folgendermaßen aussehen:

```console
az acr repository show-tags --name mycontainerregistry082 --repository aci-tutorial-app --output table
Result
--------
v1
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure-Containerregistrierung für die Verwendung mit Azure Container Instances vorbereitet und ein Containerimage mithilfe von Push an die Registrierung übertragen. Die folgenden Schritte wurden durchgeführt:

> [!div class="checklist"]
> * Erstellen einer Azure Container Registry-Instanz mit der Azure-Befehlszeilenschnittstelle
> * Markieren eines Containerimages für Azure Container Registry
> * Hochladen eines Images in Azure Container Registry

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie den Container in Azure mithilfe von Azure Container Instances bereitstellen:

> [!div class="nextstepaction"]
> [Bereitstellen eines Containers in Azure Container Instances](container-instances-tutorial-deploy-app.md)

<!-- LINKS - External -->
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository-list]: /cli/azure/acr/repository
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-group-create]: /cli/azure/group#az-group-create
[azure-cli-install]: /cli/azure/install-azure-cli
