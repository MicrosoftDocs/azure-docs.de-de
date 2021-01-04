---
title: 'Schnellstart: Senden von Ereignissen an Event Grid'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie Event Grid-Ereignisse für Ihre Containerregistrierung aktivieren, Containerimages mithilfe von Push übertragen und Ereignisse aus einer Beispielanwendung löschen.
ms.topic: article
ms.date: 08/23/2018
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 6058fceb873e2b26da2d30dadba456e2a625f3f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074215"
---
# <a name="quickstart-send-events-from-private-container-registry-to-event-grid"></a>Schnellstart: Senden von Ereignissen aus der privaten Containerregistrierung an Event Grid

Azure Event Grid ist ein vollständig verwalteter Dienst für das Ereignisrouting, der eine einheitliche Ereignisnutzung mithilfe eines Veröffentlichen/Abonnieren-Modells bereitstellt. In dieser Schnellstartanleitung verwenden Sie die Azure CLI, um eine Containerregistrierung zu erstellen, Registrierungsereignisse zu abonnieren und anschließend eine Beispielwebanwendung für den Empfang der Ereignisse bereitzustellen. Abschließend lösen Sie für das Containerimage `push`- und `delete`-Ereignisse aus und lassen sich die Ereignisnutzlast in der Beispielanwendung anzeigen.

Nachdem Sie die Schritte in diesem Artikel ausgeführt haben, werden Ereignisse, die von Ihrer Containerregistrierung an Event Grid gesendet werden, in der Beispielwebanwendung angezeigt.

![Rendering der Webanwendung mit drei empfangenen Ereignissen im Webbrowser][sample-app-01]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Die Azure CLI-Befehle in diesem Artikel sind für die Ausführung in einer **Bash** -Shell formatiert. Wenn Sie eine andere Shell wie PowerShell oder die Eingabeaufforderung verwenden, müssen Sie möglicherweise Zeilenfortsetzungszeichen oder Variablenzuweisungszeilen entsprechend anpassen. In diesem Artikel werden Variablen verwendet, damit die Befehle später nur geringfügig angepasst werden müssen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Sie Azure-Ressourcen bereitstellen und verwalten. Durch den Befehl [az group create][az-group-create] wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt. Wenn Sie einen anderen Namen für Ihre Ressourcengruppe verwenden möchten, legen Sie `RESOURCE_GROUP_NAME` auf einen anderen Wert fest.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

Stellen Sie anschließend mit den unten aufgeführten Befehlen eine Containerregistrierung in der Ressourcengruppe bereit. Legen Sie zunächst `ACR_NAME` auf einen Namen für Ihre Registrierung fest, bevor Sie den Befehl [az acr create][az-acr-create] ausführen. Der Name muss innerhalb von Azure eindeutig sein und darf 5 bis 50 alphanumerische Zeichen enthalten.

```azurecli-interactive
ACR_NAME=<acrName>

az acr create --resource-group $RESOURCE_GROUP_NAME --name $ACR_NAME --sku Basic
```

Nach der Erstellung der Registrierung sieht die Azure CLI-Ausgabe in etwa wie folgt aus:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-08-16T20:02:46.569509+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry",
  "location": "eastus",
  "loginServer": "myregistry.azurecr.io",
  "name": "myregistry",
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

## <a name="create-an-event-endpoint"></a>Erstellen eines Ereignisendpunkts

In diesem Abschnitt wird beschrieben, wie Sie mit einer Resource Manager-Vorlage aus einem GitHub-Repository eine vorab erstellte Beispielwebanwendung in Azure App Service bereitstellen. In einem anderen Abschnitt wird erläutert, wie Sie die Event Grid-Ereignisse Ihrer Registrierung abonnieren und die App als den Endpunkt festlegen, an den Ereignisse gesendet werden.

Legen Sie zum Bereitstellen der Beispiel-App `SITE_NAME` auf einen eindeutigen Namen für Ihre Web-App fest, und führen Sie die unten aufgeführten Befehle aus. Der Websitename muss innerhalb von Azure eindeutig sein, da er Bestandteil des vollqualifizierten Domänennamens (FQDN) der Web-App ist. In einem Abschnitt weiter unten navigieren Sie in einem Webbrowser zum FQDN der App und lassen sich dort die Registrierungsereignisse anzeigen.

```azurecli-interactive
SITE_NAME=<your-site-name>

az group deployment create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Öffnen Sie nach der erfolgreichen Bereitstellung (dieser Vorgang kann einige Minuten dauern) einen Browser, und navigieren Sie zu Ihrer Web-App, um sicherzustellen, dass diese ausgeführt wird:

`http://<your-site-name>.azurewebsites.net`

Die Beispiel-App sollte nun gerendert werden, und es sollten keine Ereignismeldungen angezeigt werden:

![Webbrowser mit Beispiel-Web-App ohne Anzeige von Ereignissen][sample-app-02]

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonnieren von Registrierungsereignissen

In Event Grid abonnieren Sie ein *Thema* , um festzulegen, welche Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen. Durch den folgenden Befehl vom Typ [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] wird die erstellte Containerregistrierung abonniert und die Web-App-URL als Endpunkt festgelegt, an die Ereignisse gesendet werden sollen. Die Umgebungsvariablen, die Sie in den vorherigen Abschnitten angegeben haben, werden hier wiederverwendet, sodass keine Änderungen erforderlich sind.

```azurecli-interactive
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-acr \
    --source-resource-id $ACR_REGISTRY_ID \
    --endpoint $APP_ENDPOINT
```

Nach Abschluss des Abonniervorgangs sollte die Ausgabe der folgenden ähneln:

```json
{
  "destination": {
    "endpointBaseUrl": "https://eventgridviewer.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "All"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myregistry/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-acr",
  "labels": null,
  "name": "event-sub-acr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "topic": "/subscriptions/<Subscription ID>/resourceGroups/myresourcegroup/providers/microsoft.containerregistry/registries/myregistry",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Auslösen von Registrierungsereignissen

Da die Beispiel-App ausgeführt wird und Sie die Registrierung in Event Grid abonniert haben, können Sie nun Ereignisse generieren. In diesem Abschnitt wird beschrieben, wie Sie mithilfe von ACR Tasks ein Containerimage erstellen und in die Registrierung pushen. ACR Tasks ist ein Feature von Azure Container Registry, mit dem Sie Containerimages in der Cloud erstellen können, ohne dass dafür die Docker-Engine auf Ihrem lokalen Computer installiert sein muss.

### <a name="build-and-push-image"></a>Erstellen und Übermitteln des Images mithilfe von Push

Führen Sie den unten genannten Azure CLI-Befehl aus, um ein Containerimage mithilfe der Inhalte eines GitHub-Repositorys zu erstellen. Standardmäßig pusht ACR Tasks automatisch das erfolgreich erstellte Image in Ihre Registrierung, die dann das `ImagePushed`-Ereignis generiert.

```azurecli-interactive
az acr build --registry $ACR_NAME --image myimage:v1 -f Dockerfile https://github.com/Azure-Samples/acr-build-helloworld-node.git
```

Wenn ACR Tasks den Erstellungs- und den anschließenden Pushvorgang für Ihr Image ausführt, sollte die Ausgabe in etwa wie im folgenden Beispiel aussehen. Die folgende Beispielausgabe wurde aus Gründen der Übersichtlichkeit gekürzt.

```output
Sending build context to ACR...
Queued a build with build ID: aa2
Waiting for build agent...
2018/08/16 22:19:38 Using acb_vol_27a2afa6-27dc-4ae4-9e52-6d6c8b7455b2 as the home volume
2018/08/16 22:19:38 Setting up Docker configuration...
2018/08/16 22:19:39 Successfully set up Docker configuration
2018/08/16 22:19:39 Logging in to registry: myregistry.azurecr.io
2018/08/16 22:19:55 Successfully logged in
Sending build context to Docker daemon  94.72kB
Step 1/5 : FROM node:9-alpine
...
```

Führen Sie den folgenden Befehl zum Anzeigen des Tags im Repository „myimage“ aus, um sicherzustellen, dass sich das erstellte Image in Ihrer Registrierung befindet:

```azurecli-interactive
az acr repository show-tags --name $ACR_NAME --repository myimage
```

Das „v1“-Tag des erstellten Images sollte ähnlich wie in der folgenden Ausgabe dargestellt werden:

```output
[
  "v1"
]
```

### <a name="delete-the-image"></a>Löschen des Images

Generieren Sie nun ein Ereignis vom Typ `ImageDeleted`, indem Sie das Image mit dem Befehl [az acr repository delete][az-acr-repository-delete] löschen:

```azurecli-interactive
az acr repository delete --name $ACR_NAME --image myimage:v1
```

Die Ausgabe sollte in etwa wie im folgenden Beispiel aussehen und die Aufforderung enthalten, das Löschen des Manifests und der zugehörigen Images zu bestätigen:

```output
This operation will delete the manifest 'sha256:f15fa9d0a69081ba93eee308b0e475a54fac9c682196721e294b2bc20ab23a1b' and all the following images: 'myimage:v1'.
Are you sure you want to continue? (y/n): 
```

## <a name="view-registry-events"></a>Anzeigen von Registrierungsereignissen

Sie haben das Image mithilfe von Push an Ihre Registrierung übertragen und es anschließend gelöscht. Navigieren Sie nun zur Ihrer Viewer-Web-App für Event Grid. In dieser sollten die Ereignisse `ImageDeleted` und `ImagePushed` angezeigt werden. Möglicherweise werden auch für den Abonniervorgang Validierungsereignisse angezeigt, die durch Ausführen des Befehls im Abschnitt [Abonnieren von Registrierungsereignissen](#subscribe-to-registry-events) generiert wurden.

Auf dem folgenden Screenshot ist die Beispiel-App mit drei Ereignissen zu sehen. Der Abschnitt des `ImageDeleted`-Ereignisses wurde erweitert, damit die zugehörigen Details angezeigt werden.

![Webbrowser mit Beispiel-App und ImagePushed- sowie ImageDeleted-Ereignis][sample-app-03]

Glückwunsch! Wenn die Ereignisse `ImagePushed` und `ImageDeleted` angezeigt werden, sendet Ihre Registrierung Ereignisse an Event Grid. Dieser Dienst leitet die Ereignisse dann weiter an Ihren Web-App-Endpunkt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit dieser Schnellstartanleitung erstellten Ressourcen nicht mehr benötigen, können Sie diese mit dem unten aufgeführten Azure CLI-Befehl löschen. Wenn Sie eine Ressourcengruppe löschen, werden alle darin enthaltenen Ressourcen dauerhaft gelöscht.

**WARNUNG** : Dieser Vorgang kann nicht rückgängig gemacht werden. Stellen Sie sicher, dass Sie die Ressourcen in der Gruppe nicht mehr benötigen, bevor Sie den Befehl ausführen.

```azurecli-interactive
az group delete --name $RESOURCE_GROUP_NAME
```

## <a name="event-grid-event-schema"></a>Event Grid-Ereignisschema

Den Schemaverweis für Ereignismeldungen in Azure Container Registry finden Sie in der Event Grid-Dokumentation:

[Azure Event Grid-Ereignisschema für Container Registry](../event-grid/event-schema-container-registry.md)

## <a name="next-steps"></a>Nächste Schritte

Sie haben mit diesem Schnellstart eine Containerregistrierung bereitgestellt, ein Image mit ACR Tasks erstellt und anschließend gelöscht und Registrierungsereignisse von Event Grid in einer Beispielanwendung verwendet. Im nächsten ACR Tasks-Tutorial erfahren Sie mehr über das Erstellen von Containerimages in der Cloud und über automatisierte Builds während der Aktualisierung des Basisimages:

> [!div class="nextstepaction"]
> [Erstellen von Containerimages in der Cloud mit ACR Tasks](container-registry-tutorial-quick-task.md)

<!-- IMAGES -->
[sample-app-01]: ./media/container-registry-event-grid-quickstart/sample-app-01.png
[sample-app-02]: ./media/container-registry-event-grid-quickstart/sample-app-02-no-events.png
[sample-app-03]: ./media/container-registry-event-grid-quickstart/sample-app-03-with-events.png

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr/repository
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
