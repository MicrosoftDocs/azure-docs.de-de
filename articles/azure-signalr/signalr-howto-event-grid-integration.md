---
title: Senden von Azure SignalR Service-Ereignissen an Event Grid
description: Ein Leitfaden, der zeigt, wie Event Grid-Ereignisse für SignalR Service aktiviert und dann Clientverbindungsereignisse vom Typ „Verbunden/Getrennt“ an eine Beispielanwendung gesendet werden.
services: signalr
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: chenyl
ms.openlocfilehash: 84b83c1dd541418c446a89a6f51be668cb41e54e
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562643"
---
# <a name="how-to-send-events-from-azure-signalr-service-to-event-grid"></a>Senden von Ereignissen von Azure SignalR Service an Event Grid

Azure Event Grid ist ein vollständig verwalteter Dienst für das Ereignisrouting, der eine einheitliche Ereignisnutzung mithilfe eines Veröffentlichen/Abonnieren-Modells bereitstellt. In diesem Leitfaden verwenden Sie die Azure CLI, um einen Azure SignalR Service zu erstellen, Verbindungsereignisse zu abonnieren und anschließend eine Beispielwebanwendung für den Empfang der Ereignisse bereitzustellen. Schließlich können Sie eine Verbindung herstellen und trennen und die Ereignisnutzlast in der Beispielanwendung nachvollziehen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Die Azure CLI-Befehle in diesem Artikel sind für die Ausführung in einer **Bash**-Shell formatiert. Wenn Sie eine andere Shell wie PowerShell oder die Eingabeaufforderung verwenden, müssen Sie möglicherweise Zeilenfortsetzungszeichen oder Variablenzuweisungszeilen entsprechend anpassen. In diesem Artikel werden Variablen verwendet, damit die Befehle später nur geringfügig angepasst werden müssen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Sie Azure-Ressourcen bereitstellen und verwalten. Durch den Befehl [az group create][az-group-create] wird eine Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *eastus* erstellt. Wenn Sie einen anderen Namen für Ihre Ressourcengruppe verwenden möchten, legen Sie `RESOURCE_GROUP_NAME` auf einen anderen Wert fest.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup

az group create --name $RESOURCE_GROUP_NAME --location eastus
```

## <a name="create-a-signalr-service"></a>Erstellen eines SignalR-Diensts

Stellen Sie anschließend mit den unten aufgeführten Befehlen einen Azure SignalR Service in der Ressourcengruppe bereit.
```azurecli-interactive
SIGNALR_NAME=SignalRTestSvc

az signalr create --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --sku Free_F1
```

Nach der Erstellung des SignalR Service sieht die Azure CLI-Ausgabe in etwa wie folgt aus:

```json
{
  "externalIp": "13.76.156.152",
  "hostName": "clitest.servicedev.signalr.net",
  "hostNamePrefix": "clitest",
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/clitest1/providers/Microsoft.SignalRService/SignalR/clitest",
  "location": "southeastasia",
  "name": "clitest",
  "provisioningState": "Succeeded",
  "publicPort": 443,
  "resourceGroup": "clitest1",
  "serverPort": 443,
  "sku": {
    "capacity": 1,
    "family": null,
    "name": "Free_F1",
    "size": "F1",
    "tier": "Free"
  },
  "tags": null,
  "type": "Microsoft.SignalRService/SignalR",
  "version": "1.0"
}

```

## <a name="create-an-event-endpoint"></a>Erstellen eines Ereignisendpunkts

In diesem Abschnitt wird beschrieben, wie Sie mit einer Resource Manager-Vorlage aus einem GitHub-Repository eine vorab erstellte Beispielwebanwendung in Azure App Service bereitstellen. In einem anderen Abschnitt wird erläutert, wie Sie die Event Grid-Ereignisse Ihrer Registrierung abonnieren und die App als den Endpunkt festlegen, an den Ereignisse gesendet werden.

Legen Sie zum Bereitstellen der Beispiel-App `SITE_NAME` auf einen eindeutigen Namen für Ihre Web-App fest, und führen Sie die unten aufgeführten Befehle aus. Der Websitename muss innerhalb von Azure eindeutig sein, da er Bestandteil des vollqualifizierten Domänennamens (FQDN) der Web-App ist. In einem Abschnitt weiter unten navigieren Sie in einem Webbrowser zum FQDN der App und lassen sich dort die Registrierungsereignisse anzeigen.

```azurecli-interactive
SITE_NAME=<your-site-name>

az deployment group create \
    --resource-group $RESOURCE_GROUP_NAME \
    --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
    --parameters siteName=$SITE_NAME hostingPlanName=$SITE_NAME-plan
```

Öffnen Sie nach der erfolgreichen Bereitstellung (dieser Vorgang kann einige Minuten dauern) einen Browser, und navigieren Sie zu Ihrer Web-App, um sicherzustellen, dass diese ausgeführt wird:

`http://<your-site-name>.azurewebsites.net`

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-registry-events"></a>Abonnieren von Registrierungsereignissen

In Event Grid abonnieren Sie ein *Thema*, um festzulegen, welche Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen. Durch den Befehl [az eventgrid event-subscription create][az-eventgrid-event-subscription-create] wird der erstellte Azure SignalR Service abonniert und die Web-App-URL als Endpunkt festgelegt, an den Ereignisse gesendet werden sollen. Die Umgebungsvariablen, die Sie in den vorherigen Abschnitten angegeben haben, werden hier wiederverwendet, sodass keine Änderungen erforderlich sind.

```azurecli-interactive
SIGNALR_SERVICE_ID=$(az signalr show --resource-group $RESOURCE_GROUP_NAME --name $SIGNALR_NAME --query id --output tsv)
APP_ENDPOINT=https://$SITE_NAME.azurewebsites.net/api/updates

az eventgrid event-subscription create \
    --name event-sub-signalr \
    --source-resource-id $SIGNALR_SERVICE_ID \
    --endpoint $APP_ENDPOINT
```

Nach Abschluss des Abonniervorgangs sollte die Ausgabe der folgenden ähneln:

```JSON
{
  "deadLetterDestination": null,
  "destination": {
    "endpointBaseUrl": "https://$SITE_NAME.azurewebsites.net/api/updates",
    "endpointType": "WebHook",
    "endpointUrl": null
  },
  "filter": {
    "includedEventTypes": [
      "Microsoft.SignalRService.ClientConnectionConnected",
      "Microsoft.SignalRService.ClientConnectionDisconnected"
    ],
    "isSubjectCaseSensitive": null,
    "subjectBeginsWith": "",
    "subjectEndsWith": ""
  },
  "id": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/Microsoft.SignalRService/SignalR/SignalRTestSvc/providers/Microsoft.EventGrid/eventSubscriptions/event-sub-signalr",
  "labels": null,
  "name": "event-sub-signalr",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "retryPolicy": {
    "eventTimeToLiveInMinutes": 1440,
    "maxDeliveryAttempts": 30
  },
  "topic": "/subscriptions/28cf13e2-c598-4aa9-b8c8-098441f0827a/resourceGroups/myResourceGroup/providers/microsoft.signalrservice/signalr/SignalRTestSvc",
  "type": "Microsoft.EventGrid/eventSubscriptions"
}
```

## <a name="trigger-registry-events"></a>Auslösen von Registrierungsereignissen

Wechseln Sie zum Dienstmodus `Serverless Mode`, und richten Sie eine Clientverbindung mit SignalR Service ein. Sie können das [serverlose Beispiel](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Serverless) als Referenz verwenden.

```bash
git clone git@github.com:aspnet/AzureSignalR-samples.git

cd samples/Management

# Start the negotiation server
# Negotiation server is responsible for generating access token for clients
cd NegotitationServer
dotnet user-secrets set Azure:SignalR:ConnectionString "<Connection String>"
dotnet run

# Use a separate command line
# Start a client
cd SignalRClient
dotnet run
```

## <a name="view-registry-events"></a>Anzeigen von Registrierungsereignissen

Sie haben nun einen Client mit SignalR Service verbunden. Navigieren Sie nun zur Ihrer Viewer-Web-App für Event Grid. In dieser sollte ein `ClientConnectionConnected`-Ereignis angezeigt werden. Wenn Sie den Client beenden, wird außerdem ein `ClientConnectionDisconnected`-Ereignis angezeigt.

<!-- LINKS - External -->
[azure-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
[sample-app]: https://github.com/dbarkol/azure-event-grid-viewer

<!-- LINKS - Internal -->
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription#az-eventgrid-event-subscription-create
[az-group-create]: /cli/azure/group#az-group-create
