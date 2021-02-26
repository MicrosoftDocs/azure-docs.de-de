---
title: 'Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit PowerShell'
description: Verwenden Sie Azure Event Grid, um Azure Cache for Redis-Ereignisse zu abonnieren, an einen Webhook zu senden und in einer Webanwendung zu verarbeiten.
ms.date: 1/5/2021
author: curib
ms.author: cauribeg
ms.topic: quickstart
ms.service: cache
ms.openlocfilehash: 615f3b023ded6583dfedca99f561d09689b86b51
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056468"
---
# <a name="quickstart-route-azure-cache-for-redis-events-to-web-endpoint-with-powershell"></a>Schnellstart: Weiterleiten von Azure Cache for Redis-Ereignissen an einen Webendpunkt mit PowerShell

Azure Event Grid ist ein Ereignisdienst für die Cloud. In dieser Schnellstartanleitung verwenden Sie Azure PowerShell, um Azure Cache for Redis-Ereignisse zu abonnieren, ein Ereignis auszulösen und die Ergebnisse anzuzeigen. 

Üblicherweise senden Sie Ereignisse an einen Endpunkt, der die Ereignisdaten verarbeitet und entsprechende Aktionen ausführt. Der Einfachheit halber senden Sie Ereignisse im Rahmen dieser Schnellstartanleitung an eine Web-App, mit der die Nachrichten gesammelt und angezeigt werden. Nach Abschluss der Schritte in dieser Schnellstartanleitung sehen Sie, dass die Ereignisdaten an die Web-App gesendet wurden.

## <a name="setup"></a>Einrichten

Für diese Schnellstartanleitung wird vorausgesetzt, dass Sie die aktuelle Version von Azure PowerShell verwenden. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps) Informationen dazu.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Authentifizierungsanweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

In diesem Beispiel wird **westus2** verwendet und die Auswahl zur Verwendung im gesamten Artikel in einer Variable gespeichert.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Event Grid-Themen werden als einzelne Azure-Ressourcen bereitgestellt und müssen in einer Azure-Ressourcengruppe angeordnet werden. Eine Azure-Ressourcengruppe ist eine logische Sammlung, in der Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe.

Das folgende Beispiel erstellt eine Ressourcengruppe namens **gridResourceGroup** am Standort **westus2**.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-an-azure-cache-for-redis-instance"></a>Erstellen einer Azure Cache for Redis-Instanz 

```powershell
New-AzRedisCache
   -ResourceGroupName <String>
   -Name <String>
   -Location <String>
   [-Size <String>]
   [-Sku <String>]
   [-RedisConfiguration <Hashtable>]
   [-EnableNonSslPort <Boolean>]
   [-TenantSettings <Hashtable>]
   [-ShardCount <Int32>]
   [-MinimumTlsVersion <String>]
   [-SubnetId <String>]
   [-StaticIP <String>]
   [-Tag <Hashtable>]
   [-Zone <String[]>]
   [-DefaultProfile <IAzureContextContainer>]
   [-WhatIf]
   [-Confirm]
   [<CommonParameters>]
```
Weitere Informationen zur Erstellung einer Cache-Instanz in PowerShell finden Sie in der [Azure PowerShell-Referenz](https://docs.microsoft.com/powershell/module/az.rediscache/new-azrediscache?view=azps-5.2.0). 

## <a name="create-a-message-endpoint"></a>Erstellen eines Nachrichtenendpunkts

Vor dem Abonnieren des Themas erstellen wir zunächst den Endpunkt für die Ereignisnachricht. Der Endpunkt führt in der Regel Aktionen auf der Grundlage der Ereignisdaten aus. Der Einfachheit halber stellen Sie in dieser Schnellstartanleitung eine [vorgefertigte Web-App](https://github.com/Azure-Samples/azure-event-grid-viewer) bereit, die die Ereignisnachrichten anzeigt. Die bereitgestellte Lösung umfasst einen App Service-Plan, eine App Service-Web-App und Quellcode von GitHub.

Ersetzen Sie `<your-site-name>` durch einen eindeutigen Namen für Ihre Web-App. Der Name der Web-App muss eindeutig sein, da er Teil des DNS-Eintrags ist.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

Die Bereitstellung kann einige Minuten dauern. Nach erfolgreichem Abschluss der Bereitstellung können Sie Ihre Web-App anzeigen und sich vergewissern, dass sie ausgeführt wird. Navigieren Sie hierzu in einem Webbrowser zu `https://<your-site-name>.azurewebsites.net`.

Die Website sollte angezeigt werden, und es sollten momentan keine Nachrichten vorliegen.

:::image type="content" source="media/cache-event-grid-portal/blank-event-grid-viewer.png" alt-text="Event Grid-Viewer: Leere Website":::

## <a name="subscribe-to-your-azure-cache-for-redis-event"></a>Abonnieren Ihres Azure Cache for Redis-Ereignisses

In diesem Schritt abonnieren Sie ein Thema, um Event Grid mitzuteilen, welche Ereignisse Sie nachverfolgen möchten. Im folgenden Beispiel wird die von Ihnen erstellte Cache-Instanz abonniert. Außerdem wird die URL Ihrer Web-App als Endpunkt für Ereignisbenachrichtigungen übergeben. Der Endpunkt für Ihre Web-App muss das Suffix `/api/updates/` enthalten.

```powershell
$cacheId = (Get-AzRedisCache -ResourceGroupName $resourceGroup -Name $cacheName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpoint `
  -ResourceId $cacheId
```

Zeigen Sie wieder Ihre Web-App an. Wie Sie sehen, wurde ein Abonnementüberprüfungsereignis an sie gesendet. Klicken Sie auf das Augensymbol, um die Ereignisdaten zu erweitern. Event Grid sendet das Überprüfungsereignis, damit der Endpunkt bestätigen kann, dass er Ereignisdaten empfangen möchte. Die Web-App enthält Code zur Überprüfung des Abonnements.

  :::image type="content" source="media/cache-event-grid-portal/subscription-event.png" alt-text="Azure Event Grid-Viewer":::

## <a name="trigger-an-event-from-azure-cache-for-redis"></a>Auslösen eines Ereignisses aus Azure Cache for Redis

Als Nächstes lösen wir ein Ereignis aus, um zu sehen, wie Event Grid die Nachricht an Ihren Endpunkt weiterleitet.

```powershell
Import-AzRedisCache
      [-ResourceGroupName <String>]
      -Name <String>
      -Files <String[]>
      [-Format <String>]
      [-Force]
      [-PassThru]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```
Weitere Informationen zum Importieren in PowerShell finden Sie in der [Azure PowerShell-Referenz](https://docs.microsoft.com/powershell/module/az.rediscache/import-azrediscache?view=azps-5.2.0). 

Sie haben das Ereignis ausgelöst, und Event Grid hat die Nachricht an den Endpunkt gesendet, den Sie beim Abonnieren konfiguriert haben. Zeigen Sie Ihre Web-App an, um das soeben gesendete Ereignis anzuzeigen.

```json
[{
"id": "e1ceb52d-575c-4ce4-8056-115dec723cff",
  "eventType": "Microsoft.Cache.ImportRDBCompleted",
  "topic": "/subscriptions/{subscription_id}/resourceGroups/{resource_group_name}/providers/Microsoft.Cache/Redis/{cache_name}",
  "data": {
    "name": "ImportRDBCompleted",
    "timestamp": "2020-12-10T18:07:54.4937063+00:00",
    "status": "Succeeded"
  },
  "subject": "ImportRDBCompleted",
  "dataversion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-12-10T18:07:54.4937063+00:00"
}]

```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Wenn Sie diese Azure Cache for Redis-Instanz und das Ereignisabonnement weiterverwenden möchten, sollten Sie keine Bereinigung der in dieser Schnellstartanleitung erstellten Ressourcen durchführen. Führen Sie andernfalls den folgenden Befehl aus, um die in dieser Schnellstartanleitung erstellten Ressourcen zu löschen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie Themen und Ereignisabonnements erstellen. Nun können Sie sich eingehender über Azure Cache for Redis-Ereignisse und die Möglichkeiten von Event Grid informieren:

- [Azure Cache for Redis: Event Grid-Übersicht](cache-event-grid.md)
- [Einführung in Azure Event Grid](../event-grid/overview.md)