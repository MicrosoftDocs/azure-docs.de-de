---
title: 'Schnellstart: Bereitstellen von Docker-Containern in einer Containerinstanz – Azure CLI'
description: In diesem Schnellstart verwenden Sie die Azure-Befehlszeilenschnittstelle, um schnell eine containerbasierte Web-App bereitzustellen, die in einer isolierten Azure-Containerinstanz ausgeführt wird.
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom:
- seo-python-october2019
- seodec18
- mvc
- devx-track-js
- devx-track-azurecli
ms.openlocfilehash: 1c327fc7fc067948b5022f989e6c86f99573bd1a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100183"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>Schnellstart: Bereitstellen einer Containerinstanz in Azure mithilfe der Azure-Befehlszeilenschnittstelle

Führen Sie mithilfe von Azure Container Instances serverlose Docker-Container schnell und einfach in Azure aus. Sie stellen eine Anwendung bedarfsgesteuert in einer Containerinstanz bereit, wenn Sie keine vollständige Containerorchestrierungsplattform wie Azure Kubernetes Service benötigen.

In diesem Schnellstart stellen Sie mithilfe der Azure-Befehlszeilenschnittstelle einen isolierten Docker-Container bereit und machen seine Anwendung über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) verfügbar. Einige Sekunden nach dem Ausführen eines einzigen Bereitstellungsbefehls können Sie zur ausgeführten Anwendung im Container navigieren:

![Anzeigen einer in Azure Container Instances bereitgestellten App im Browser][aci-app-browser]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Schnellstart ist Version 2.0.55 oder höher der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure Container Instances muss wie alle Azure-Ressourcen in einer Ressourcengruppe bereitgestellt werden. Mit Ressourcengruppen können verwandte Azure-Ressourcen organisiert und verwaltet werden.

Erstellen Sie zunächst mit dem folgenden Befehl [az group create][az-group-create] eine Ressourcengruppe namens *myResourceGroup* am Standort *eastus* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>Erstellen eines Containers

Sie besitzen eine Ressourcengruppe und können nun einen Container in Azure ausführen. Geben Sie zum Erstellen einer Containerinstanz mit der Azure-Befehlszeilenschnittstelle einen Ressourcengruppennamen, einen Containerinstanznamen und ein Docker-Containerimage im Befehl [az container create][az-container-create] an. In diesem Schnellstart verwenden Sie das öffentliche Image `mcr.microsoft.com/azuredocs/aci-helloworld`. Dieses Image verpackt eine kleine in Node.js geschriebene Web-App, die eine statische HTML-Seite bedient.

Sie können Ihre Container über das Internet verfügbar machen, indem Sie mindestens einen zu öffnenden Port und/oder eine DNS-Namensbezeichnung angeben. In diesem Schnellstart stellen Sie einen Container mit einer DNS-Namensbezeichnung bereit, damit die Web-App öffentlich erreichbar ist.

Führen Sie einen Befehl wie den folgenden aus, um eine Containerinstanz zu starten. Der Wert `--dns-name-label` muss in der Azure-Region, in der Sie die Instanz erstellen, eindeutig sein. Falls die Fehlermeldung „DNS-Namensbezeichnung ist nicht verfügbar.“ angezeigt wird, sollten Sie eine andere DNS-Namensbezeichnung verwenden.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

Sie sollten innerhalb weniger Sekunden eine Antwort von der Azure-Befehlszeilenschnittstelle mit dem Hinweis erhalten, dass die Bereitstellung abgeschlossen wurde. Überprüfen Sie den Status mit dem Befehl [az container show][az-container-show]:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

Wenn Sie den Befehl ausführen, werden der vollständig qualifizierte Domänenname (Fully Qualified Domain Name, FQDN) des Containers und der Bereitstellungsstatus angezeigt.

```output
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

Wenn der Wert `ProvisioningState` des Containers **Erfolgreich** lautet, navigieren Sie in Ihrem Browser zu seinem FQDN. Der Vorgang war erfolgreich, wenn in etwa die folgende Webseite angezeigt wird. Sie haben eine in einem Docker-Container ausgeführte Anwendung in Azure bereitgestellt.

![Anzeigen einer in Azure Container Instances bereitgestellten App im Browser][aci-app-browser]

Sollte die Anwendung nicht sofort angezeigt werden, warten Sie einige Sekunden, bis die DNS-Auffüllung abgeschlossen ist, und aktualisieren Sie anschließend Ihren Browser.

## <a name="pull-the-container-logs"></a>Herunterladen der Containerprotokolle

Wenn Sie ein Problem mit einem Container oder der darin ausgeführten Anwendung beheben (oder nur seine Ausgabe anzeigen) müssen, sehen Sie sich zuerst die Protokolle der Containerinstanz an.

Verwenden Sie den Befehl [az container logs][az-container-logs], um die Protokolle der Containerinstanz per Pull abzurufen:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Die Ausgabe enthält die Protokolle für den Container und sollte die HTTP GET-Anforderungen anzeigen, die beim Betrachten der Anwendung in Ihrem Browser generiert wurden.

```output
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>Anfügen von Ausgabestreams

Zusätzlich zum Anzeigen der Protokolle können Sie Ihre lokalen Standardausgabestreams und Standardfehlerstreams an die Streams des Containers anfügen.

Führen Sie zunächst den Befehl [az container attach][az-container-attach] aus, um Ihre lokale Konsole den Ausgabestreams des Containers anzufügen:

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

Aktualisieren Sie nach dem Anfügen mehrmals Ihren Browser, um zusätzliche Ausgaben zu generieren. Trennen Sie anschließend Ihre Konsole mit `Control+C`. Die Ausgabe sollte etwa folgendermaßen aussehen:

```output
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Container nicht mehr benötigen, entfernen Sie ihn mithilfe des Befehls [az container delete][az-container-delete]:

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

Um sicherzustellen, dass der Container gelöscht wurde, führen Sie den Befehl [az container list](/cli/azure/container#az-container-list) aus:

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

Der Container **mycontainer** sollte nicht in der Ausgabe des Befehls angezeigt werden. Wenn die Ressourcengruppe keine anderen Container enthält, wird keine Ausgabe angezeigt.

Wenn Sie die Ressourcengruppe *myResourceGroup* und alle darin enthaltenen Ressourcen nicht mehr benötigen, löschen Sie sie mit dem Befehl [az group delete][az-group-delete]:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure-Containerinstanz aus einem öffentlichen Microsoft-Image erstellt. Fahren Sie mit dem Azure Container Instances-Tutorial fort, wenn Sie ein Containerimage erstellen und über eine private Azure-Containerregistrierung bereitstellen möchten.

> [!div class="nextstepaction"]
> [Azure Container Instances-Tutorial](./container-instances-tutorial-prepare-app.md)

Um Optionen zum Ausführen von Containern in einem Orchestrierungssystem in Azure zu testen, lesen Sie die Schnellstartanleitung für [Azure Kubernetes Service (AKS)][container-service].

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/view-an-application-running-in-an-azure-container-instance.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
