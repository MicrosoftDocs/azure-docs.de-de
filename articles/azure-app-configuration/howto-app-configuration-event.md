---
title: Tutorial zum Einrichten von Azure App Configuration, um Ereignisse an einen Webendpunkt zu senden | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Azure App Configuration-Ereignisabonnements einrichten, um Schlüssel-Wert-Änderungsereignisse an einen Webendpunkt zu senden.
services: azure-app-configuration
documentationcenter: ''
author: jimmyca
manager: yegu
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 2cb9ad28a21842987f8c0f7c75151ab8c7fe6fa0
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735809"
---
# <a name="quickstart-route-azure-app-configuration-events-to-a-web-endpoint-with-azure-cli"></a>Schnellstart: Weiterleiten von Azure App Configuration-Ereignissen an einen Webendpunkt mit der Azure-Befehlszeilenschnittstelle

Benutzer von Azure App Configuration können Ereignisse abonnieren, die bei der Änderung von Schlüssel-Wert-Paaren ausgegeben werden. Diese Ereignisse können Webhooks, Azure Functions, Azure Storage-Warteschlangen oder andere Ereignishandler auslösen, die von [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/event-handlers) unterstützt werden. In diesem Artikel erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle Azure App Configuration-Ereignisse abonnieren.

Üblicherweise senden Sie Ereignisse an einen Endpunkt, der die Ereignisdaten verarbeitet und entsprechende Aktionen ausführt. Der Einfachheit halber senden Sie die Ereignisse in diesem Artikel allerdings an eine Web-App, die die Nachrichten sammelt und anzeigt.

Nach Abschluss der Schritte in diesem Artikel sehen Sie, dass die Ereignisdaten an die Web-App gesendet wurden.

![Anzeigen des Abonnementereignisses](./media/quickstarts/event-grid/view-results.png)

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden, müssen Sie für diesen Artikel mindestens die Version 2.0.24 der Azure-Befehlszeilenschnittstelle ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Falls Sie nicht Cloud Shell verwenden, müssen Sie sich zuerst mithilfe von `az login` anmelden.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Event Grid-Themen sind Azure-Ressourcen und müssen in einer Azure-Ressourcengruppe platziert werden. Die Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. 

Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen `<resource_group_name>` am Standort *westus* erstellt.  Ersetzen Sie `<resource_group_name>` durch einen eindeutigen Namen für Ihre Ressourcengruppe.

```azurecli-interactive
az group create --name <resource_group_name> --location westus
```

## <a name="create-an-app-configuration"></a>Erstellen einer App-Konfiguration

Ersetzen Sie `<appconfig_name>` durch einen eindeutigen Namen für Ihre App-Konfiguration und `<resource_group_name>` durch die zuvor erstellte Ressourcengruppe. Der Name muss eindeutig sein, da er als DNS-Name verwendet wird.

```azurecli-interactive
az appconfig create \
  --name <appconfig_name> \
  --location westus \
  --resource-group <resource_group_name>
```

## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Vor dem Abonnieren des Themas erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Der Endpunkt führt in der Regel Aktionen auf der Grundlage der Ereignisdaten aus. Der Einfachheit halber stellen Sie in dieser Schnellstartanleitung eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignisnachrichten anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

Ersetzen Sie `<your-site-name>` durch einen eindeutigen Namen für Ihre Web-App. Der Name der Web-App muss eindeutig sein, da er Teil des DNS-Eintrags ist.

```azurecli-interactive
$sitename=<your-site-name>

az group deployment create \
  --resource-group <resource_group_name> \
  --template-uri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" \
  --parameters siteName=$sitename hostingPlanName=viewerhost
```

Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.

Die Website sollte angezeigt werden, und es sollten momentan keine Nachrichten vorliegen.

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration"></a>Abonnieren Ihrer App-Konfiguration

Sie abonnieren ein Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten und wohin sie gesendet werden sollen. Im folgenden Beispiel wird die von Ihnen erstellte App-Konfiguration abonniert. Außerdem wird die URL Ihrer Web-App als Endpunkt für Ereignisbenachrichtigungen übergeben. Ersetzen Sie `<event_subscription_name>` durch einen Namen für Ihr Ereignisabonnement. Verwenden Sie für `<resource_group_name>` und `<appconfig_name>` jeweils den zuvor erstellten Wert.

Der Endpunkt für Ihre Web-App muss das Suffix `/api/updates/` enthalten.

```azurecli-interactive
appconfigId=$(az appconfig show --name <appconfig_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $appconfigId \
  --name <event_subscription_name> \
  --endpoint $endpoint
```

Zeigen Sie wieder Ihre Web-App an. Wie Sie sehen, wurde ein Abonnementüberprüfungsereignis an sie gesendet. Klicken Sie auf das Augensymbol, um die Ereignisdaten zu erweitern. Event Grid sendet das Überprüfungsereignis, damit der Endpunkt bestätigen kann, dass er Ereignisdaten empfangen möchte. Die Web-App enthält Code zur Überprüfung des Abonnements.

![Anzeigen des Abonnementereignisses](./media/quickstarts/event-grid/view-subscription-event.png)

## <a name="trigger-an-app-configuration-event"></a>Auslösen eines App Configuration-Ereignisses

Als Nächstes lösen wir ein Ereignis aus, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet. Erstellen Sie mit dem zuvor erstellten `<appconfig_name>`-Element ein Schlüssel-Wert-Paar.

```azurecli-interactive
az appconfig kv set --name <appconfig_name> --key Foo --value Bar --yes
```

Sie haben das Ereignis ausgelöst, und Event Grid hat die Nachricht an den Endpunkt gesendet, den Sie beim Abonnieren konfiguriert haben. Zeigen Sie Ihre Web-App an, um das soeben gesendete Ereignis anzuzeigen.

```json
[{
  "id": "deb8e00d-8c64-4b6e-9cab-282259c7674f",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/eventDemoGroup/providers/microsoft.appconfiguration/configurationstores/{appconfig-name}",
  "subject": "https://{appconfig-name}.azconfig.io/kv/Foo",
  "data": {
    "key": "Foo",
    "etag": "a1LIDdNEIV6wCnfv3xaip7fMXD3"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T18:59:54Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese App-Konfiguration und das Ereignisabonnement weiterverwenden möchten, überspringen Sie die Bereinigung der in diesem Artikel erstellten Ressourcen. Führen Sie andernfalls den folgenden Befehl aus, um die in diesem Artikel erstellten Ressourcen zu löschen.

Ersetzen Sie `<resource_group_name>` durch die weiter oben erstellte Ressourcengruppe.

```azurecli-interactive
az group delete --name <resource_group_name>
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie Themen und Ereignisabonnements erstellen. Nun können Sie sich ausführlicher über Schlüssel-Wert-Ereignisse und die Möglichkeiten informieren, die Event Grid Ihnen bietet:

- [Reacting to Azure App Configuration events](concept-app-configuration-event.md) (Reagieren auf Azure App Configuration-Ereignisse)
- [Einführung in Azure Event Grid](../event-grid/overview.md)