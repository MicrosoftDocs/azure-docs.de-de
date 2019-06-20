---
title: Verwalten großer Themenmengen in Azure Event Grid mit Ereignisdomänen
description: Es wird veranschaulicht, wie Sie große Themenmengen in Azure Event Grid verwalten und dafür Ereignisse veröffentlichen, indem Sie Ereignisdomänen verwenden.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 73c837897f4a104fabb4143d4b49fa3fbc258bb4
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305033"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Verwalten von Themen und Veröffentlichen von Ereignissen mithilfe von Ereignisdomänen

In diesem Artikel werden die folgenden Aufgaben erläutert:

* Erstellen einer Event Grid-Domäne
* Abonnieren von Event Grid-Themen
* Auflisten von Schlüsseln
* Veröffentlichen von Ereignissen in einer Domäne

Weitere Informationen zu Ereignisdomänen finden Sie unter [Grundlegendes zu Ereignisdomänen für die Verwaltung von Event Grid-Themen](event-domains.md).

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="create-an-event-domain"></a>Erstellen einer Ereignisdomäne

Erstellen Sie zum Verwalten großer Themenmengen eine Ereignisdomäne.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

Bei einer erfolgreichen Erstellung werden die folgenden Werte zurückgegeben:

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Beachten Sie die Objekte `endpoint` und `id`, da diese zum Verwalten der Domäne und zum Veröffentlichen von Ereignissen erforderlich sind.

## <a name="manage-access-to-topics"></a>Verwalten des Zugriffs auf Themen

Das Verwalten des Zugriffs auf Themen erfolgt über eine [Rollenzuweisung](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli). Die Rollenzuweisung verwendet die rollenbasierte Zugriffssteuerung (RBAC), um die Vorgänge mit Azure-Ressourcen auf autorisierte Benutzer in einem bestimmten Bereich zu beschränken.

Event Grid verfügt über zwei integrierte Rollen, mit denen Sie bestimmten Benutzern Zugriff auf verschiedene Themen innerhalb einer Domäne zuweisen können. Diese Rollen sind `EventGrid EventSubscription Contributor (Preview)` (ermöglicht das Erstellen und Löschen von Abonnements) und `EventGrid EventSubscription Reader (Preview)` (ermöglicht nur das Auflisten von Ereignisabonnements).

Mit dem folgenden Azure CLI-Befehl wird `alice@contoso.com` auf das Erstellen und Löschen von Ereignisabonnements ausschließlich für das Thema `demotopic1` beschränkt:

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Mit dem folgenden PowerShell-Befehl wird `alice@contoso.com` auf das Erstellen und Löschen von Ereignisabonnements ausschließlich für das Thema `demotopic1` beschränkt:

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Weitere Informationen zum Verwalten des Zugriffs für Event Grid-Vorgänge finden Sie unter [Event Grid – Sicherheit und Authentifizierung](./security-authentication.md).

## <a name="create-topics-and-subscriptions"></a>Erstellen von Themen und Abonnements

Der Event Grid-Dienst erstellt und verwaltet das entsprechende Thema automatisch in einer Domäne basierend auf dem Aufruf zum Erstellen eines Ereignisabonnements für ein Domänenthema. Es gibt keinen separaten Schritt zum Erstellen eines Themas in einer Domäne. Wenn das letzte Ereignisabonnement für ein Thema gelöscht wird, wird analog dazu das Thema ebenfalls gelöscht.

Das Abonnieren eines Themas in einer Domäne ist identisch mit dem Abonnieren beliebiger anderer Azure-Ressourcen. Geben Sie als Quellressourcen-ID die Ereignisdomänen-ID an, die zuvor beim Erstellen der Domäne zurückgegeben wurde. Um das Thema anzugeben, das Sie abonnieren möchten, fügen Sie `/topics/<my-topic>` am Ende der Quellressourcen-ID hinzu. Um ein Domänenbereichereignis-Abonnement zu erstellen, das alle Ereignisse in der Domäne empfängt, geben Sie die Ereignisdomänen-ID ohne jegliche Themen an.

Normalerweise erstellt der Benutzer, dem Sie im vorherigen Abschnitt Zugriff gewährt haben, das Abonnement. Der Einfachheit halber erstellen Sie das Abonnement für diesen Artikel hier selbst. 

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

Wenn Sie einen Testendpunkt benötigen, um Ihre Ereignisse zu abonnieren, können Sie jederzeit eine [vordefinierte Webanwendung](https://github.com/Azure-Samples/azure-event-grid-viewer) bereitstellen, die die eingehenden Ereignisse anzeigt. Sie können die Ereignisse an Ihre Testwebsite unter `https://<your-site-name>.azurewebsites.net/api/updates` senden.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

Berechtigungen, die für ein Thema festgelegt sind, werden in Azure Active Directory gespeichert und müssen explizit gelöscht werden. Das Löschen eines Ereignisabonnements widerruft nicht den Zugriff eines Benutzers zum Erstellen von Ereignisabonnements, wenn er Schreibzugriff für ein Thema besitzt.


## <a name="publish-events-to-an-event-grid-domain"></a>Veröffentlichen von Ereignissen in einer Event Grid-Domäne

Das Veröffentlichen von Ereignissen für eine Domäne ist identisch mit dem [Veröffentlichen für ein benutzerdefiniertes Thema](./post-to-custom-topic.md). Allerdings veröffentlichen Sie nicht für ein benutzerdefiniertes Thema, sondern Sie veröffentlichen alle Ereignisse im Domänenendpunkt. In den JSON-Ereignisdaten geben Sie das Thema an, an das die Ereignisse gesendet werden sollen. Das folgende Array von Ereignissen würde zu einem Ereignis mit `"id": "1111"` für Thema `demotopic1` führen, während ein Ereignis mit `"id": "2222"` an Thema `demotopic2` gesendet würde:

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Um den Domänenendpunkt mit der Azure CLI abzurufen, verwenden Sie Folgendes:

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

Um die Schlüssel für eine Domäne abzurufen, verwenden Sie Folgendes:

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Um den Domänenendpunkt mit der PowerShell abzurufen, verwenden Sie Folgendes:

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Um die Schlüssel für eine Domäne abzurufen, verwenden Sie Folgendes:

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

Verwenden Sie anschließend Ihre bevorzugte Methode zum Erstellen eines HTTP-POST-Aufrufs, um Ihre Ereignisse für Ihre Event Grid-Domäne zu veröffentlichen.

## <a name="search-lists-of-topics-or-subscriptions"></a>Durchsuchen von Liste mit Themen oder Abonnements

Um das Durchsuchen und Verwalten von großen Mengen von Themen oder Abonnements zu ermöglichen, unterstützen die APIs von Event Grid Listen und Paginierung.

### <a name="using-cli"></a>Verwenden von CLI

Um sie zu verwenden, stellen Sie sicher, dass Sie die Azure CLI Event Grid-Erweiterungsversion 0.4.1 oder höher verwenden.

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic list \
    --odata-query "contains(name, 'my-test-filter')"
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu grundlegenden Konzepten in Ereignisdomänen und zum Grund ihrer Nützlichkeit finden Sie unter [Konzeptionelle Übersicht über Ereignisdomänen](event-domains.md).
