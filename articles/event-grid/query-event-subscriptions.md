---
title: Abfragen von Azure Event Grid-Abonnements
description: Hier wird beschrieben, wie Sie Azure Event Grid-Abonnements auflisten.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: spelluru
ms.openlocfilehash: ad9c2d492f70a697ef0e7dc3b7ed03b9938f2468
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66162022"
---
# <a name="query-event-grid-subscriptions"></a>Abfragen von Event Grid-Abonnements 

Dieser Artikel beschreibt, wie die Event Grid-Abonnements in Ihrem Azure-Abonnement aufgelistet werden. Beim Abfragen der vorhandenen Event Grid-Abonnements ist es wichtig, die verschiedenen Abonnementtypen zu verstehen. Basierend auf dem abzurufenden Abonnementtyp geben Sie unterschiedliche Parameter an.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-groups-and-azure-subscriptions"></a>Ressourcengruppen und Azure-Abonnements

Azure-Abonnements und Ressourcengruppen sind keine Azure-Ressourcen. Daher haben Event Grid-Abonnements für Ressourcen-Gruppen oder Azure-Abonnements nicht die gleichen Eigenschaften wie Event Grid-Abonnements für Azure-Ressourcen. Event Grid-Abonnements für Ressourcengruppen oder Azure-Abonnements werden als global betrachtet.

Sie müssen keine Parameter angeben, um Event Grid-Abonnements für ein Azure-Abonnement und seine Ressourcengruppen abzurufen. Stellen Sie sicher, dass Sie das Azure-Abonnement ausgewählt haben, das Sie abfragen möchten. Die folgenden Beispiele rufen keine Event Grid-Abonnements für benutzerdefinierte Themen oder Azure-Ressourcen ab.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az account set -s "My Azure Subscription"
az eventgrid event-subscription list
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Set-AzContext -Subscription "My Azure Subscription"
Get-AzEventGridSubscription
```

Um Event Grid-Abonnements für ein Azure-Abonnement abzurufen, geben Sie den Thementyp **Microsoft.Resources.Subscriptions** an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.Subscriptions"
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.Subscriptions"
```

Um Event Grid-Abonnements für alle Ressourcengruppen in einem Azure-Abonnement abzurufen, geben Sie den Thementyp **Microsoft.Resources.ResourceGroups** an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.Resources.ResourceGroups"
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Resources.ResourceGroups"
```

Geben Sie zum Abrufen der Event Grid-Abonnements für eine bestimmte Ressourcengruppe den Namen der Ressourcengruppe als Parameter an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --resource-group myResourceGroup
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -ResourceGroupName myResourceGroup
```

## <a name="custom-topics-and-azure-resources"></a>Benutzerdefinierte Themen und Azure-Ressourcen

Benutzerdefinierte Event Grid-Themen sind Azure-Ressourcen. Aus diesem Grund können Sie Event Grid-Abonnements für benutzerdefinierte Themen und andere Ressourcen wie Blobspeicherkonten auf die gleiche Weise abfragen. Um Event Grid-Abonnements für benutzerdefinierte Themen abzurufen, müssen Sie Parameter angeben, die die Ressource oder den Speicherort der Ressource identifizieren. Es ist nicht möglich, eine allgemeine Abfrage von Event Grid-Abonnements für Ressourcen in Ihrem Azure-Abonnement auszuführen.

Geben Sie zum Abrufen von Event Grid-Abonnements für benutzerdefinierte Themen und andere Ressourcen an einem Speicherort den Namen des Speicherorts an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --location westus2
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -Location westus2
```

Geben Sie zum Abrufen von Abonnements für benutzerdefinierte Themen für einen Standort den Standort und den Thementyp **Microsoft.EventGrid.Topics** an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --topic-type-name "Microsoft.EventGrid.Topics" --location "westus2"
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.EventGrid.Topics" -Location westus2
```

Geben Sie zum Abrufen von Abonnements für Speicherkonten für einen Standort den Standort und den Thementyp **Microsoft.Storage.StorageAccounts** an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --topic-type "Microsoft.Storage.StorageAccounts" --location westus2
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicTypeName "Microsoft.Storage.StorageAccounts" -Location westus2
```

Geben Sie zum Abrufen von Event Grid-Abonnements für ein benutzerdefiniertes Thema den Namen des benutzerdefinierten Themas und den Namen seiner Ressourcengruppe an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription list --topic-name myCustomTopic --resource-group myResourceGroup
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
Get-AzEventGridSubscription -TopicName myCustomTopic -ResourceGroupName myResourceGroup
```

Geben Sie zum Abrufen von Event Grid-Abonnements für eine bestimmte Ressource die Ressourcen-ID an.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
resourceid=$(az resource show -n mystorage -g myResourceGroup --resource-type "Microsoft.Storage/storageaccounts" --query id --output tsv)
az eventgrid event-subscription list --resource-id $resourceid
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
$resourceid = (Get-AzResource -Name mystorage -ResourceGroupName myResourceGroup).ResourceId
Get-AzEventGridSubscription -ResourceId $resourceid
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).
* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
