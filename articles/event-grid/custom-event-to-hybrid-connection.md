---
title: 'Tutorial: Senden von benutzerdefinierten Ereignissen an die Hybridverbindung: Event Grid'
description: 'Tutorial: Verwenden Sie Azure Event Grid und die Azure CLI, um ein Thema zu veröffentlichen und dieses Ereignis zu abonnieren. Für den Endpunkt wird eine Hybridverbindung verwendet.'
ms.date: 07/07/2020
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.openlocfilehash: c709d77827551860cc917c3c84c5a849d9fdc512
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566825"
---
# <a name="tutorial-route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Tutorial: Weiterleiten benutzerdefinierter Ereignisse an Azure Relay Hybrid Connections mit Azure-Befehlszeilenschnittstelle und Event Grid

Azure Event Grid ist ein Ereignisdienst für die Cloud. Azure Relay Hybrid Connections ist einer der unterstützten Ereignishandler. Hybridverbindungen werden als Ereignishandler verwendet, wenn Sie Ereignisse von Anwendungen verarbeiten müssen, die über keinen öffentlichen Endpunkt verfügen. Diese Anwendungen können sich in Ihrem Unternehmensnetzwerk befinden. In diesem Artikel erstellen Sie mithilfe der Azure-Befehlszeilenschnittstelle ein benutzerdefiniertes Thema, abonnieren dieses benutzerdefinierte Thema und lösen das Ereignis zum Anzeigen des Ergebnisses aus. Sie senden die Ereignisse an die Hybridverbindung.

## <a name="prerequisites"></a>Voraussetzungen

- In diesem Artikel wird davon ausgegangen, dass Sie bereits über eine Hybridverbindung und eine Listener-Anwendung verfügen. Informationen zu den ersten Schritten mit Hybridverbindungen finden Sie unter [Erste Schritte mit Relay Hybrid Connections – .NET](../azure-relay/relay-hybrid-connections-dotnet-get-started.md) oder [Erste Schritte mit Relay Hybrid Connections – Knoten](../azure-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Für diesen Artikel ist mindestens Version 2.0.56 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Event Grid-Themen sind Azure-Ressourcen und müssen in einer Azure-Ressourcengruppe platziert werden. Die Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe. 

Das folgende Beispiel erstellt eine Ressourcengruppe namens *gridResourceGroup* am Standort *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Erstellen eines benutzerdefinierten Themas

Ein Event Grid-Thema verfügt über einen benutzerdefinierten Endpunkt für die Veröffentlichung Ihrer Ereignisse. Im folgenden Beispiel wird das benutzerdefinierte Thema in Ihrer Ressourcengruppe erstellt. Ersetzen Sie `<topic_name>` durch einen eindeutigen Namen für Ihr benutzerdefiniertes Thema. Der Name des Event Grid-Themas muss eindeutig sein, da er durch einen DNS-Eintrag dargestellt wird.

```azurecli-interactive
az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-custom-topic"></a>Abonnieren eines benutzerdefinierten Themas

Sie abonnieren ein Event Grid-Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten. Im folgenden Beispiel wird das von Ihnen erstellte benutzerdefinierte Thema abonniert. Außerdem wird die Ressourcen-ID der Hybridverbindung für den Endpunkt übergeben. Die Hybridverbindungs-ID weist das folgende Format auf:

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Das folgende Skript ruft die Ressourcen-ID des Relaynamespaces ab. Es erstellt die ID für die Hybridverbindung und abonniert ein Event Grid-Thema. Das Skript legt den Endpunkttyp auf `hybridconnection` fest und verwendet die Hybridverbindungs-ID für den Endpunkt.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"
topicid=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid \
  --expiration-date "<yyyy-mm-dd>"
```

Beachten Sie, dass für das Abonnement ein [Ablaufdatum](concepts.md#event-subscription-expiration) festgelegt ist.

## <a name="create-application-to-process-events"></a>Erstellen einer Anwendung zum Verarbeiten von Ereignissen

Sie benötigen eine Anwendung, die Ereignisse von der Hybridverbindung abrufen kann. Im [Verbraucherbeispiel für die Microsoft Azure Event Grid-Hybridverbindung in C#](https://github.com/Azure-Samples/event-grid-dotnet-hybridconnection-destination) wird dieser Vorgang ausgeführt. Sie haben die erforderlichen Schritte bereits abgeschlossen.

1. Stellen Sie sicher, dass Sie über Visual Studio 2019 oder höher verfügen.

1. Klonen Sie das Repository auf Ihren lokalen Computer.

1. Laden Sie das Projekt „HybridConnectionConsumer“ in Visual Studio.

1. Ersetzen Sie in „Program.cs“ `<relayConnectionString>` und `<hybridConnectionName>` mit der Verbindungszeichenfolge und den Namen der Hybridverbindung, die Sie erstellt haben.

1. Kompilieren Sie die Anwendung, und führen Sie sie über Visual Studio aus.

## <a name="send-an-event-to-your-topic"></a>Senden eines Ereignisses an Ihr Thema

Nun wird ein Ereignis ausgelöst, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. In diesem Artikel erfahren Sie, wie Sie Azure CLI zum Auslösen des Ereignisses verwenden. Alternativ können Sie die [Event Grid-Herausgeber-App](https://github.com/Azure-Samples/event-grid-dotnet-publish-consume-events/tree/master/EventGridPublisher) verwenden.

Zunächst rufen wir die URL und den Schlüssel für das benutzerdefinierte Thema ab. Verwenden Sie auch hier für `<topic_name>` den Namen Ihres benutzerdefinierten Themas.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Zur Vereinfachung dieses Artikels verwenden Sie Beispielereignisdaten, die an das benutzerdefinierte Thema gesendet werden können. Üblicherweise werden die Ereignisdaten von einer Anwendung oder einem Azure-Dienst gesendet. CURL ist ein Hilfsprogramm zum Senden von HTTP-Anforderungen. In diesem Artikel wird CURL verwendet, um das Ereignis an das benutzerdefinierte Thema zu senden.

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Die Listener-Anwendung sollte die Ereignismeldung empfangen.

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