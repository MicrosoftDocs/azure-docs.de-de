---
title: Richtlinien für unzustellbare Nachrichten und Wiederholungen für Azure Event Grid-Abonnements
description: Hier erfahren Sie, wie Sie Ereignisübermittlungsoptionen für Event Grid anpassen. Legen Sie ein Ziel für unzustellbare Nachrichten fest, und geben Sie an, wie lange die Übermittlung wiederholt wird.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: a1b49fd3a2a85377a56c92aefd1b0056f91895b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66119561"
---
# <a name="dead-letter-and-retry-policies"></a>Richtlinien für unzustellbare Nachrichten und Wiederholungen

Bei der Erstellung von Ereignisabonnements können Sie die Einstellungen für die Ereignisübermittlung anpassen. In diesem Artikel wird erläutert, wie Sie einen Speicherort für unzustellbare Nachrichten einrichten und die Wiederholungseinstellungen anpassen. Informationen zu diesen Features finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="set-dead-letter-location"></a>Festlegen eines Speicherorts für unzustellbare Nachrichten

Zum Festlegen eines Speicherorts für unzustellbare Nachrichten benötigen Sie ein Speicherkonto für Ereignisse, die nicht an einen Endpunkt übermittelt werden können. Die Beispiele erhalten die Ressourcen-ID eines vorhandenen Speicherkontos. Sie erstellen ein Ereignisabonnement, das einen Container in diesem Speicherkonto als Endpunkt für unzustellbare Nachrichten verwendet.

> [!NOTE]
> Erstellen Sie im Speicher ein Speicherkonto und einen Blobcontainer, bevor Sie die Befehle in diesem Artikel ausführen.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

```azurecli-interactive
containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Um unzustellbare Nachrichten zu deaktivieren, führen Sie den Befehl zum Erstellen des Ereignisabonnements erneut aus, aber geben Sie keinen Wert für `deadletter-endpoint` an. Sie müssen das Ereignisabonnement nicht löschen.

> [!NOTE]
> Wenn Sie die Azure CLI auf Ihrem lokalen Computer nutzen, verwenden Sie mindestens Version 2.0.56. Anweisungen zum Installieren der aktuellen Version der Azure CLI finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$containername = "testcontainer"

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Um unzustellbare Nachrichten zu deaktivieren, führen Sie den Befehl zum Erstellen des Ereignisabonnements erneut aus, aber geben Sie keinen Wert für `DeadLetterEndpoint` an. Sie müssen das Ereignisabonnement nicht löschen.

> [!NOTE]
> Wenn Sie Azure PowerShell auf Ihrem lokalen Computer nutzen, verwenden Sie mindestens Version 1.1.0. Laden Sie die aktuelle Version von Azure PowerShell von der [Azure-Downloadseite](https://azure.microsoft.com/downloads/) herunter, und installieren Sie sie.

## <a name="set-retry-policy"></a>Festlegen der Wiederholungsrichtlinie

Bei der Erstellung eines Event Grid-Abonnements können Sie festlegen, wie lange Event Grid versuchen soll, das Ereignis zu übermitteln. Standardmäßig versucht es Event Grid für 24 Stunden (1.440 Minuten) oder 30-mal. Diese Werte können Sie für Ihr Event Grid-Abonnement festlegen. Der Wert für die Ereignisgültigkeitsdauer muss eine ganze Zahl zwischen 1 und 1440 sein. Der Wert für die maximalen Versuche muss ein Integer zwischen 1 und 30 sein.

Sie können den [Wiederholungszeitplan](delivery-and-retry.md#retry-schedule-and-duration) nicht konfigurieren.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

So legen Sie die auf 1440 Minuten festgelegte Ereignislebensdauer auf einen anderen Wert fest:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Um die maximale Anzahl von Wiederholungsversuchen auf einen anderen Wert als 30 festzulegen, verwenden Sie Folgendes:

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Wenn Sie sowohl `event-ttl` als auch `max-deliver-attempts` festlegen, verwendet Event Grid den zuerst ablaufenden Wert, um zu ermitteln, wann die Ereignisübermittlung beendet werden soll.

### <a name="powershell"></a>PowerShell

So legen Sie die auf 1440 Minuten festgelegte Ereignislebensdauer auf einen anderen Wert fest:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Um die maximale Anzahl von Wiederholungsversuchen auf einen anderen Wert als 30 festzulegen, verwenden Sie Folgendes:

```azurepowershell-interactive
$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Wenn Sie sowohl `EventTtl` als auch `MaxDeliveryAttempt` festlegen, verwendet Event Grid den zuerst ablaufenden Wert, um zu ermitteln, wann die Ereignisübermittlung beendet werden soll.

## <a name="next-steps"></a>Nächste Schritte

* Eine Beispielanwendung, die eine Azure-Funktions-App zum Verarbeiten von Ereignissen im Zusammenhang mit unzustellbaren Nachrichten verwendet, finden Sie unter [Azure Event Grid Dead Letter Samples for .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/) (Azure Event Grid-Beispiele für unzustellbare Nachrichten für .NET).
* Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).
* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
