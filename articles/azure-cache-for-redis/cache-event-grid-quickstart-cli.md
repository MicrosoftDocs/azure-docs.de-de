---
title: 'Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit der Azure CLI'
description: Mithilfe von Azure Event Grid können Sie Azure Cache for Redis-Ereignisse abonnieren, ein Ereignis auslösen und die Ergebnisse anzeigen.
author: curib
ms.author: cauribeg
ms.date: 1/5/2021
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 7f33ca0043400962054fabb1aadb1da612fe5426
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806425"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-azure-cli"></a>Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit der Azure CLI

Azure Event Grid ist ein Ereignisdienst für die Cloud. In dieser Schnellstartanleitung verwenden Sie die Azure-CLI, um Azure Cache for Redis-Ereignisse zu abonnieren, ein Ereignis auszulösen und die Ergebnisse anzuzeigen.

Üblicherweise senden Sie Ereignisse an einen Endpunkt, der die Ereignisdaten verarbeitet und entsprechende Aktionen ausführt. Der Einfachheit halber senden Sie Ereignisse im Rahmen dieser Schnellstartanleitung an eine Web-App, mit der die Nachrichten gesammelt und angezeigt werden. Nach Abschluss der Schritte in dieser Schnellstartanleitung sehen Sie, dass die Ereignisdaten an die Web-App gesendet wurden.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI (Befehlszeilenschnittstelle) lokal installieren und verwenden, müssen Sie für diese Schnellstartanleitung mindestens Version 2.0.70 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Falls Sie nicht Cloud Shell verwenden, müssen Sie sich zuerst mithilfe von `az login` anmelden.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Event Grid-Themen werden als einzelne Azure-Ressourcen bereitgestellt und müssen in einer Azure-Ressourcengruppe angeordnet werden. Eine Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. 

Im folgenden Beispiel wird am Standort *westcentralus* eine Ressourcengruppe namens `<resource_group_name>` erstellt.  Ersetzen Sie `<resource_group_name>` durch einen eindeutigen Namen für Ihre Ressourcengruppe.

```azurecli-interactive
az group create --name <resource_group_name> --location westcentralus
```

## <a name="create-a-cache-instance"></a>Erstellen einer Cache-Instanz

```azurecli-interactive
#/bin/bash

# Create a Basic C0 (256 MB) Azure Cache for Redis instance
az redis create --name <cache_name> --resource-group <resource_group_name> --location westcentralus --sku Basic --vm-size C0
```


## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Vor dem Abonnieren des Themas erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Der Endpunkt führt in der Regel Aktionen auf der Grundlage der Ereignisdaten aus. Der Einfachheit halber stellen Sie in dieser Schnellstartanleitung eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignisnachrichten anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

Ersetzen Sie `<your-site-name>` durch einen eindeutigen Namen für Ihre Web-App. Der Name der Web-App muss eindeutig sein, da er Teil des DNS-Eintrags ist.

```azurecli-interactive
sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.

Die Website sollte angezeigt werden, und es sollten momentan keine Nachrichten vorliegen.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-azure-cache-for-redis-instance"></a>Abonnieren Ihrer Azure Cache for Redis-Instanz

In diesem Schritt abonnieren Sie ein Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten und wohin diese gesendet werden sollen. Im folgenden Beispiel wird die von Ihnen erstellte Azure Cache for Redis-Instanz abonniert. Außerdem wird die URL Ihrer Web-App als Endpunkt für Ereignisbenachrichtigungen übergeben. Ersetzen Sie `<event_subscription_name>` durch einen Namen für Ihr Ereignisabonnement. Verwenden Sie für `<resource_group_name>` und `<cache_name>` jeweils den zuvor erstellten Wert.

Der Endpunkt für Ihre Web-App muss das Suffix `/api/updates/` enthalten.

```azurecli-interactive
cacheId=$(az redis show --name <cache_name> --resource-group <resource_group_name> --query id --subscription <subscription_id>)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --source-resource-id $cacheId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Zeigen Sie wieder Ihre Web-App an. Wie Sie sehen, wurde ein Abonnementüberprüfungsereignis an sie gesendet. Klicken Sie auf das Augensymbol, um die Ereignisdaten zu erweitern. Event Grid sendet das Überprüfungsereignis, damit der Endpunkt bestätigen kann, dass er Ereignisdaten empfangen möchte. Die Web-App enthält Code zur Überprüfung des Abonnements.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid-Viewer":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Auslösen eines Ereignisses aus Azure Cache for Redis

Als Nächstes lösen wir ein Ereignis aus, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. Wir exportieren nun die Daten, die in Ihrer Azure Cache for Redis-Instanz gespeichert sind. Verwenden Sie wieder die zuvor erstellten Werte für `{cache_name}` und `{resource_group_name}`.

```azurecli-interactive
az redis export  --ids '/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}' \
    --prefix '<prefix_for_exported_files>' \
    --container '<SAS_url>'  
```

Sie haben das Ereignis ausgelöst, und Event Grid hat die Nachricht an den Endpunkt gesendet, den Sie beim Abonnieren konfiguriert haben. Zeigen Sie Ihre Web-App an, um das soeben gesendete Ereignis anzuzeigen.


```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ExportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ExportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ExportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Azure Cache for Redis-Instanz und das Ereignisabonnement weiterverwenden möchten, sollten Sie keine Bereinigung der in dieser Schnellstartanleitung erstellten Ressourcen durchführen. Führen Sie andernfalls den folgenden Befehl aus, um die in dieser Schnellstartanleitung erstellten Ressourcen zu löschen.

Ersetzen Sie `<resource_group_name>` durch die weiter oben erstellte Ressourcengruppe.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie Themen und Ereignisabonnements erstellen. Nun können Sie sich eingehender über Azure Cache for Redis-Ereignisse und die Möglichkeiten von Event Grid informieren:

- [Azure Cache for Redis: Event Grid-Übersicht](cache-event-grid.md)
- [Einführung in Azure Event Grid](../event-grid/overview.md)
