---
title: 'Schnellstart: Senden von benutzerdefinierten Ereignissen an die Speicherwarteschlange: Event Grid, Azure CLI'
description: 'Schnellstart: Verwenden Sie Azure Event Grid und die Azure CLI, um ein Thema zu veröffentlichen und dieses Ereignis zu abonnieren. Für den Endpunkt wird eine Speicherwarteschlange verwendet.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4de7aa1c111b5b21a27b155474ae10f78feba083
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566315"
---
# <a name="quickstart-route-custom-events-to-azure-queue-storage-with-azure-cli-and-event-grid"></a>Schnellstart: Weiterleiten benutzerdefinierter Ereignisse an Azure Queue Storage mit Azure-CLI und Event Grid

Azure Event Grid ist ein Ereignisdienst für die Cloud. Azure Queue Storage ist einer der unterstützten Ereignishandler. In diesem Artikel erstellen Sie mithilfe der Azure-Befehlszeilenschnittstelle ein benutzerdefiniertes Thema, abonnieren dieses benutzerdefinierte Thema und lösen das Ereignis zum Anzeigen des Ergebnisses aus. Sie senden die Ereignisse an die Queue Storage-Instanz.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.56 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

- Wenn Sie Azure PowerShell auf Ihrem lokalen Computer anstatt Cloud Shell im Azure-Portal verwenden, stellen Sie sicher, dass Sie mindestens über Version 1.1.0 von Azure PowerShell verfügen. Laden Sie die aktuelle Version von Azure PowerShell [Azure-Downloads – Befehlszeilentools](https://azure.microsoft.com/downloads/) auf Ihren Windows-Computer herunter. 

In diesem Artikel finden Sie Befehle für die Verwendung der Azure CLI. 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Event Grid-Themen sind Azure-Ressourcen und müssen in einer Azure-Ressourcengruppe platziert werden. Die Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. 

Das folgende Beispiel erstellt eine Ressourcengruppe namens *gridResourceGroup* am Standort *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-a-custom-topic"></a>Erstellen eines benutzerdefinierten Themas

Ein Event Grid-Thema verfügt über einen benutzerdefinierten Endpunkt für die Veröffentlichung Ihrer Ereignisse. Im folgenden Beispiel wird das benutzerdefinierte Thema in Ihrer Ressourcengruppe erstellt. Ersetzen Sie `<topic_name>` durch einen eindeutigen Namen für Ihr benutzerdefiniertes Thema. Der Name des Event Grid-Themas muss eindeutig sein, da er durch einen DNS-Eintrag dargestellt wird.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="create-queue-storage"></a>Erstellen einer Queue Storage-Instanz

Vor dem Abonnieren des benutzerdefinierten Themas erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Sie erstellen eine Queue Storage-Instanz zum Sammeln der Ereignisse.

```azurecli-interactive
storagename="<unique-storage-name>"
queuename="eventqueue"

az storage account create -n $storagename -g gridResourceGroup -l westus2 --sku Standard_LRS
az storage queue create --name $queuename --account-name $storagename
```

## <a name="subscribe-to-a-custom-topic"></a>Abonnieren eines benutzerdefinierten Themas

Sie abonnieren ein benutzerdefiniertes Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten. Im folgenden Beispiel wird das von Ihnen erstellte benutzerdefinierte Thema abonniert. Außerdem wird die Ressourcen-ID der Queue Storage-Instanz für den Endpunkt übergeben. Mit Azure CLI übergeben Sie die Warteschlangenspeicher-ID als Endpunkt. Der Endpunkt weist folgendes Format auf:

`/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/queueservices/default/queues/<queue-name>`

Das folgende Skript ruft die Ressourcen-ID des Speicherkontos für die Warteschlange ab. Es erstellt die ID für die Queue Storage-Instanz und abonniert ein Event Grid-Thema. Es legt den Endpunkttyp auf `storagequeue` fest und verwendet die Queue Storage-ID für den Endpunkt.

```azurecli-interactive
storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)
queueid="$storageid/queueservices/default/queues/$queuename"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type storagequeue \
  --endpoint $queueid \
  --expiration-date "<yyyy-mm-dd>"
```

Das Konto, das das Ereignisabonnement erstellt, muss über Schreibzugriff auf die Queue Storage-Instanz verfügen. Beachten Sie, dass für das Abonnement ein [Ablaufdatum](concepts.md#event-subscription-expiration) festgelegt ist.

Wenn Sie die REST-API zum Erstellen des Abonnements verwenden, übergeben Sie die ID des Speicherkontos und den Namen der Warteschlange als separaten Parameter.

```json
"destination": {
  "endpointType": "storagequeue",
  "properties": {
    "queueName":"eventqueue",
    "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>"
  }
  ...
```

## <a name="send-an-event-to-your-custom-topic"></a>Senden eines Ereignisses an Ihr benutzerdefiniertes Thema

Nun wird ein Ereignis ausgelöst, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. Zunächst rufen wir die URL und den Schlüssel für das benutzerdefinierte Thema ab. Verwenden Sie auch hier für `<topic_name>` den Namen Ihres benutzerdefinierten Themas.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Zur Vereinfachung dieses Artikels verwenden Sie Beispielereignisdaten, die an das benutzerdefinierte Thema gesendet werden können. Üblicherweise werden die Ereignisdaten von einer Anwendung oder einem Azure-Dienst gesendet. CURL ist ein Hilfsprogramm zum Senden von HTTP-Anforderungen. In diesem Artikel wird CURL verwendet, um das Ereignis an das benutzerdefinierte Thema zu senden.  Das folgende Beispiel sendet drei Ereignisse an das Event Grid-Thema:

```azurecli-interactive
for i in 1 2 3
do
   event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
   curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
done
```

Navigieren Sie zur Queue Storage-Instanz im Portal, und beachten Sie, dass Event Grid diese drei Ereignisse an die Warteschlange gesendet hat.

![Anzeigen von Meldungen](./media/custom-event-to-queue-storage/messages.png)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie dieses Ereignis weiterverwenden möchten, können Sie die Bereinigung der in diesem Artikel erstellten Ressourcen überspringen. Verwenden Sie andernfalls den folgenden Befehl, um die in diesem Artikel erstellten Ressourcen zu löschen.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie Themen und Ereignisabonnements erstellen. Nun können Sie sich ausführlicher darüber informieren, welche Möglichkeiten Event Grid bietet:

- [Einführung in Azure Event Grid](overview.md)
- [Weiterleiten von Blob Storage-Ereignissen an einen benutzerdefinierten Webendpunkt](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Überwachen von Änderungen an virtuellen Computern mit Azure Event Grid und Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamen von Big Data in ein Data Warehouse](event-grid-event-hubs-integration.md)
