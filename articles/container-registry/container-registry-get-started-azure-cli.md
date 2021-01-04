---
title: 'Schnellstart: Erstellen einer Registrierung – Azure CLI'
description: Hier lernen Sie, wie Sie schnell eine private Docker-Containerregistrierung mit der Azure CLI erstellen.
ms.topic: quickstart
ms.date: 06/12/2020
ms.custom: seodec18, H1Hack27Feb2017, mvc, devx-track-azurecli
ms.openlocfilehash: 226e50aec8f7c76a1b4c81d1a07d57583059ef0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020074"
---
# <a name="quickstart-create-a-private-container-registry-using-the-azure-cli"></a>Schnellstart: Erstellen einer privaten Containerregistrierung mit der Azure CLI

Azure Container Registry ist ein verwalteter Docker-Containerregistrierungsdienst zum Speichern privater Docker-Containerimages. In diesem Leitfaden erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Azure Container Registry-Instanz erstellen. Übertragen Sie anschließend mithilfe von Docker-Befehlen ein Containerimage per Push in die Registrierung. Rufen Sie abschließend das Image per Pull aus der Registrierung ab, und führen Sie es aus.

Für diese Schnellstartanleitung müssen Sie die Azure-Befehlszeilenschnittstelle (Version 2.0.55 empfohlen) ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

Darüber hinaus muss Docker lokal installiert sein. Für Docker sind Pakete erhältlich, mit denen Docker auf einem [macOS][docker-mac]-, [Windows][docker-windows]- oder [Linux][docker-linux]-System problemlos konfiguriert werden kann.

Da Azure Cloud Shell nicht alle erforderlichen Docker-Komponenten (z.B. den `dockerd`-Daemon) enthält, können Sie Cloud Shell für diesem Schnellstart nicht verwenden.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

In dieser Schnellstartanleitung erstellen Sie eine Registrierung vom Typ *Basic*. Dabei handelt es sich um eine kostenoptimierte Option für Entwickler, die sich mit Azure Container Registry vertraut machen. Ausführliche Informationen zu verfügbaren Dienstebenen finden Sie unter [Container Registry-Dienstebenen][container-registry-skus].

Erstellen Sie mithilfe des Befehls [az acr create][az-acr-create] eine ACR-Instanz. Der Registrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Im folgenden Beispiel wird der Name *myContainerRegistry007* verwendet. Ersetzen Sie diesen Namen durch einen eindeutigen Wert.

```azurecli
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry007 --sku Basic
```

Wenn die Registrierung erstellt wird, sieht die Ausgabe etwa wie folgt aus:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2019-01-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "mycontainerregistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Beachten Sie `loginServer` in der Ausgabe. Dabei handelt es sich um den vollqualifizierten Registrierungsnamen (nur Kleinbuchstaben). Für den restlichen Teil dieser Schnellstartanleitung wird `<registry-name>` als Platzhalter für den Namen der Containerregistrierung verwendet, und `<login-server>` ist ein Platzhalter für den Anmeldeservernamen der Registrierung.

## <a name="log-in-to-registry"></a>Anmelden bei der Registrierung

Bevor Sie Push- und Pullvorgänge für Containerimages ausführen können, müssen Sie sich bei der Registrierung anmelden. Verwenden Sie hierzu den Befehl [az acr login][az-acr-login]. Geben Sie beim Anmelden bei der Azure CLI nur den Registrierungsnamen an. Verwenden Sie nicht den Namen des Anmeldeservers, der ein Domänensuffix wie `azurecr.io`enthält. 

```azurecli
az acr login --name <registry-name>
```

Beispiel:

```azurecli
az acr login --name mycontainerregistry
```

Der Befehl gibt nach Abschluss die Meldung `Login Succeeded` zurück.

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Auflisten von Containerimages

Im folgenden Beispiel werden die Repositorys in Ihrer Registrierung aufgelistet:

```azurecli
az acr repository list --name <registry-name> --output table
```

Ausgabe:

```
Result
----------------
hello-world
```

Das folgende Beispiel listet die Tags des Repositorys **hello-world** auf.

```azurecli
az acr repository show-tags --name <registry-name> --repository hello-world --output table
```

Ausgabe:

```
Result
--------
v1
```

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe, die Containerregistrierung und die darin gespeicherten Containerimages nicht mehr benötigen, können Sie sie mit dem Befehl [az group delete][az-group-delete] entfernen.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mit der Azure CLI eine Azure Container Registry-Instanz erstellt, ein Containerimage per Push an die Registrierung übertragen und das Image per Pull aus der Registrierung abgerufen und ausgeführt. Fahren Sie mit den Azure Container Registry-Tutorials fort, um eingehendere Informationen zu ACR zu erhalten.

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry Tasks][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
