---
title: 'Schnellstart: Erstellen einer Registrierung im Portal'
description: Hier lernen Sie, wie Sie schnell eine private Azure-Containerregistrierung über das Azure-Portal erstellen.
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 00ed6b4569d22739051198c5b0c60987f783a87f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020040"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Schnellstart: Erstellen einer Azure-Containerregistrierung über das Azure-Portal

Eine Azure Container Registry-Instanz ist eine private Docker-Registrierung in Azure, in der Sie private Docker-Containerimages und zugehörige Artefakte speichern und verwalten können. In dieser Schnellstartanleitung erstellen Sie eine Containerregistrierung über das Azure-Portal. Übertragen Sie anschließend mithilfe von Docker-Befehlen ein Containerimage per Push in die Registrierung. Rufen Sie abschließend das Image per Pull aus der Registrierung ab, und führen Sie es aus.

Für diese Schnellstartanleitung müssen Sie die Azure CLI (mindestens Version 2.0.55 empfohlen) ausführen, um sich bei der Registrierung anmelden und Containerimages verwenden zu können. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

Darüber hinaus muss Docker lokal installiert sein. Für Docker sind Pakete erhältlich, mit denen Docker problemlos auf einem [Mac][docker-mac]-, [Windows][docker-windows]- oder [Linux][docker-linux]-System konfiguriert werden kann.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Klicken Sie auf **Ressource erstellen** > **Container** > **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Navigieren zur Containerregistrierung im Portal":::

Geben Sie auf der Registerkarte **Grundlagen** Werte für **Ressourcengruppe** und **Registrierungsname** ein. Der Registrierungsname muss innerhalb von Azure eindeutig sein und zwischen 5 und 50 alphanumerische Zeichen enthalten. Erstellen Sie im Rahmen dieser Schnellstartanleitung eine neue Ressourcengruppe namens `myResourceGroup` am Standort `West US`, und wählen Sie für **SKU** die Option „Basic“ aus.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Erstellen einer Containerregistrierung im Portal":::

Übernehmen Sie für die übrigen Einstellungen die Standardwerte. Wählen Sie dann **Überprüfen + erstellen** aus. Überprüfen Sie die Einstellungen, und wählen Sie anschließend **Erstellen** aus.

In dieser Schnellstartanleitung erstellen Sie eine Registrierung vom Typ *Basic*. Dabei handelt es sich um eine kostenoptimierte Option für Entwickler, die sich mit Azure Container Registry vertraut machen. Ausführliche Informationen zu verfügbaren Dienstebenen (SKUs) finden Sie unter [Azure Container Registry-Tarife][container-registry-skus].

Wenn die Meldung **Bereitstellung erfolgreich** erscheint, wählen Sie die Containerregistrierung im Portal aus. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Containerregistrierungsübersicht im Portal":::

Notieren Sie sich den Registrierungsnamen und den Wert unter **Anmeldeserver**. Sie verwenden diese Werte in den folgenden Schritten bei den Push- und Pullvorgängen für Images mit Docker.

## <a name="log-in-to-registry"></a>Anmelden bei der Registrierung

Bevor Sie Push- und Pullvorgänge für Containerimages ausführen können, müssen Sie sich bei der Registrierungsinstanz anmelden. [Melden Sie sich auf dem lokalen Computer bei der Azure CLI an][get-started-with-azure-cli], und führen Sie dann den Befehl [az acr login][az-acr-login] aus. Geben Sie beim Anmelden bei der Azure CLI nur den Registrierungsnamen an. Verwenden Sie nicht den Namen des Anmeldeservers, der ein Domänensuffix wie `azurecr.io`enthält.

```azurecli
az acr login --name <registry-name>
```

Beispiel:

```azurecli
az acr login --name mycontainerregistry
```

Der Befehl gibt nach Abschluss des Vorgangs `Login Succeeded` zurück. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Auflisten von Containerimages

Navigieren Sie zum Auflisten der Images in Ihrer Registrierung im Portal zu Ihrer Registrierung, und wählen Sie **Repositorys** und dann das Repository **hello-world** aus, das Sie mit `docker push` erstellt haben.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Auflisten von Containerimages im Portal":::

Wenn Sie das Repository **hello-world** auswählen, wird unter **Tags** das mit `v1` gekennzeichnete Image angezeigt.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Navigieren Sie zum Bereinigen von Ressourcen im Portal zur Ressourcengruppe **myResourceGroup**. Klicken Sie nach dem Laden der Ressourcengruppe auf **Ressourcengruppe löschen**, um die Ressourcengruppe, die Containerregistrierung und die dort gespeicherten Containerimages zu entfernen.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Löschen der Ressourcengruppe im Portal":::


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie im Azure-Portal eine Azure Container Registry-Instanz erstellt, ein Containerimage per Push übertragen und das Image per Pull aus der Registrierung abgerufen und ausgeführt. Fahren Sie mit den Azure Container Registry-Tutorials fort, um eingehendere Informationen zu ACR zu erhalten.

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Tutorials zu Azure Container Registry Tasks][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
