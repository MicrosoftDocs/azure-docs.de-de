---
title: 'Tutorial zu Kubernetes in Azure: Aktualisieren einer Anwendung'
description: In diesem Azure Kubernetes Service-Tutorial (AKS) erfahren Sie, wie eine vorhandene Bereitstellung der Anwendung in AKS mit einer neuen Version des Anwendungscodes aktualisiert wird.
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c8401a81a36d86b871df9fc428c393007b97c400
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833910"
---
# <a name="tutorial-update-an-application-in-azure-kubernetes-service-aks"></a>Tutorial: Aktualisieren einer Anwendung in Azure Kubernetes Service (AKS)

Nach der Bereitstellung einer Anwendung in Kubernetes kann diese durch Angeben eines neuen Containerimages oder einer neuen Imageversion aktualisiert werden. Ein Update wird bereitgestellt, damit jeweils nur ein Teil der Bereitstellung aktualisiert wird. Durch diese Art des Updates kann die Anwendung während des Updates weiterhin ausgeführt werden. Darüber hinaus wird ein Rollbackmechanismus bereitgestellt, falls ein Fehler bei der Bereitstellung auftritt.

In diesem Tutorial – Teil 6 von 7 – wird die Azure Voting-Beispiel-App aktualisiert. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Aktualisieren des Front-End-Anwendungscodes
> * Erstellen eines aktualisierten Containerimages
> * Pushübertragung des Containerimages an Azure Container Registry
> * Bereitstellen des aktualisierten Containerimages

## <a name="before-you-begin"></a>Voraussetzungen

In den vorherigen Tutorials wurde eine Anwendung als Containerimage verpackt. Dieses Image wurde in Azure Container Registry hochgeladen, und Sie haben einen AKS-Cluster erstellt. Die Anwendung wurde dann für den AKS-Cluster bereitgestellt.

Zudem wurde ein Anwendungsrepository geklont, das den Anwendungsquellcode und eine vorab erstellte Docker Compose-Datei enthält, die in diesem Tutorial verwendet wird. Stellen Sie sicher, dass Sie einen Klon des Repositorys erstellt haben und in das geklonte Verzeichnis gewechselt sind. Wenn Sie diese Schritte nicht ausgeführt haben und dies jetzt nachholen möchten, beginnen Sie mit [Tutorial 1: Erstellen von Containerimages][aks-tutorial-prepare-app].

Für dieses Tutorial müssen Sie mindestens Version 2.0.53 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="update-an-application"></a>Aktualisieren einer Anwendung

Wir nehmen eine Änderung an der Beispielanwendung vor und aktualisieren dann die bereits in Ihrem AKS-Cluster bereitgestellte Version. Stellen Sie sicher, dass Sie sich im geklonten Verzeichnis *azure-voting-app-redis* befinden. Der Quellcode der Beispielanwendung befindet sich dann im Verzeichnis *azure-vote*. Öffnen Sie die *config_file.cfg* mit einem Editor wie z.B. `vi`:

```console
vi azure-vote/azure-vote/config_file.cfg
```

Ändern Sie die Werte für *VOTE1VALUE* und *VOTE2VALUE* in andere Werte, z.B. Farben. Das folgende Beispiel zeigt die aktualisierten Werte:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Speichern und schließen Sie die Datei. Verwenden Sie in `vi` den Befehl `:wq`.

## <a name="update-the-container-image"></a>Aktualisieren des Containerimages

Verwenden Sie [docker-compose][docker-compose], um das Front-End-Image neu zu erstellen und die aktualisierte Anwendung auszuführen. Mit dem `--build`-Argument wird Docker Compose angewiesen, das Anwendungsimage neu zu erstellen:

```console
docker-compose up --build -d
```

## <a name="test-the-application-locally"></a>Lokales Testen der Anwendung

Öffnen Sie `http://localhost:8080` in einem lokalen Webbrowser, um sicherzustellen, dass das aktualisierte Containerimage Ihre Änderungen anzeigt.

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated.png" alt-text="Screenshot: Beispiel des aktualisierten Containerimages – mit lokalem Webbrowser und lokalem Host geöffnete Azure-Abstimmungs-App":::

Die in der Datei *config_file.cfg* angegebenen aktualisierten Werte werden in Ihrer ausgeführten Anwendung angezeigt.

## <a name="tag-and-push-the-image"></a>Markieren und Pushen des Images

Markieren Sie das Image *azure-vote-front* mit dem Anmeldeservernamen Ihrer ACR-Registrierung, um das aktualisierte Image richtig zu verwenden. Rufen Sie den Anmeldeservernamen mit dem Befehl [az acr list](/cli/azure/acr) ab:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Verwenden Sie [docker tag][docker-tag], um das Image zu kennzeichnen. Ersetzen Sie `<acrLoginServer>` durch Ihren ACR-Anmeldeservernamen oder dem Hostnamen der öffentlichen Registrierung, und aktualisieren Sie die Imageversion folgendermaßen auf *:v2*:

```console
docker tag mcr.microsoft.com/azuredocs/azure-vote-front:v1 <acrLoginServer>/azure-vote-front:v2
```

Verwenden Sie nun [docker push][docker-push], um das Image in Ihre Registrierung hochzuladen. Ersetzen Sie `<acrLoginServer>` durch Ihren ACR-Anmeldeservernamen.

> [!NOTE]
> Falls bei Pushvorgängen an die ACR-Registrierung Probleme auftreten, sollten Sie sich vergewissern, dass Sie noch angemeldet sind. Führen Sie den Befehl [az acr login][az-acr-login] unter Verwendung des Namens Ihrer Azure Container Registry-Instanz aus, die Sie im Schritt [Erstellen einer Azure-Containerregistrierung](tutorial-kubernetes-prepare-acr.md#create-an-azure-container-registry) erstellt haben. Beispiel: `az acr login --name <azure container registry name>`.

```console
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-the-updated-application"></a>Bereitstellen der aktualisierten Anwendung

Zur Sicherstellung der maximalen Betriebszeit müssen mehrere Instanzen des Anwendungs-Pods ausgeführt werden. Überprüfen Sie mithilfe des Befehls [kubectl get pods][kubectl-get] die Anzahl ausgeführter Front-End-Instanzen:

```
$ kubectl get pods

NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Skalieren Sie die Bereitstellung von *azure-vote-front* wie folgt, wenn Sie nur einen Front-End-Pod nutzen:

```console
kubectl scale --replicas=3 deployment/azure-vote-front
```

Um die Anwendung zu aktualisieren, verwenden Sie den Befehl [kubectl set][kubectl-set]. Aktualisieren Sie `<acrLoginServer>` mit dem Anmeldeserver- oder Hostnamen Ihrer Containerregistrierung, und legen Sie die Anwendungsversion *v2* fest:

```console
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Verwenden Sie zum Überwachen der Bereitstellung den Befehl [kubectl get pod][kubectl-get]. Nach Bereitstellung der aktualisierten Anwendung werden die Pods beendet und mit dem neuen Containerimage neu erstellt.

```console
kubectl get pods
```

Die folgende Beispielausgabe zeigt, wie im Verlauf der Bereitstellung Pods beendet und neue Instanzen ausgeführt werden:

```
$ kubectl get pods

NAME                               READY     STATUS        RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running       0          5m
azure-vote-front-1297194256-tpjlg  1/1       Running       0          1m
azure-vote-front-1297194256-tptnx  1/1       Running       0          5m
azure-vote-front-1297194256-zktw9  1/1       Terminating   0          1m
```

## <a name="test-the-updated-application"></a>Testen der aktualisierten Anwendung

Zum Anzeigen der aktualisierten Anwendung rufen Sie zunächst die externe IP-Adresse des `azure-vote-front`-Diensts ab:

```console
kubectl get service azure-vote-front
```

Öffnen Sie jetzt die IP-Adresse Ihres Diensts in einem lokalen Webbrowser:

:::image type="content" source="media/container-service-kubernetes-tutorials/vote-app-updated-external.png" alt-text="Screenshot: Beispiel der aktualisierten Anwendung – in einem lokalen Webbrowser geöffnete Azure-Abstimmungs-App":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Anwendung aktualisiert und diese Aktualisierung in Ihrem AKS-Cluster bereitgestellt. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktualisieren des Front-End-Anwendungscodes
> * Erstellen eines aktualisierten Containerimages
> * Pushübertragung des Containerimages an Azure Container Registry
> * Bereitstellen des aktualisierten Containerimages

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie ein Upgrade eines AKS-Clusters auf eine neue Version von Kubernetes durchführen.

> [!div class="nextstepaction"]
> [Kubernetes aktualisieren][aks-tutorial-upgrade]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-set]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#set

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-upgrade]: ./tutorial-kubernetes-upgrade-cluster.md
[az-acr-login]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
