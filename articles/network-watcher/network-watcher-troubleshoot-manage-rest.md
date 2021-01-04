---
title: Problembehandlung von VNET Gateway und Verbindungen – Azure-REST-API
titleSuffix: Azure Network Watcher
description: Auf dieser Seite wird beschrieben, wie Sie für Virtual Network-Gateways und -Verbindungen per REST in Azure Network Watcher die Problembehandlung durchführen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: ad89ac04d753fbe3203851af268aed29813fa587
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960392"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Problembehandlung bei Virtual Network-Gateways und -Verbindungen in Azure Network Watcher

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher bietet viele Funktionen zum Erfassen von Informationen über Ihre Netzwerkressourcen in Azure. Zu diesen Funktionen zählt die Ressourcenproblembehandlung. Die Ressourcenproblembehandlung kann über das Portal, PowerShell, die CLI oder die REST-API aufgerufen werden. Bei Aufruf untersucht Network Watcher die Integrität von Virtual Network-Gateways oder -Verbindungen und gibt entsprechende Ergebnisse zurück.

In diesem Artikel werden die verschiedenen Verwaltungsaufgaben beschrieben, die derzeit für die Problembehandlung für Ressourcen verfügbar sind.

- [**Problembehandlung für ein Virtual Network-Gateway**](#troubleshoot-a-virtual-network-gateway)
- [**Problembehandlung für Verbindungen**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Voraussetzungen

ARMclient dient zum Aufrufen der REST-API mithilfe von PowerShell. Sie finden [ARMClient auf Chocolatey](https://chocolatey.org/packages/ARMClient).

Dieses Szenario setzt voraus, dass Sie die Schritte unter [Erstellen einer Network Watcher-Instanz](network-watcher-create.md) bereits ausgeführt haben.

Eine Liste mit unterstützten Gatewaytypen finden Sie unter [Unterstützte Gatewaytypen](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Übersicht

Die Network Watcher-Problembehandlung bietet die Möglichkeit zum Behandeln von Problemen, die bei Virtual Network-Gateways und -Verbindungen auftreten können. Wenn eine Anforderung an die Ressourcenproblembehandlung erfolgt, werden Protokolle abgefragt und untersucht. Nach Abschluss der Untersuchung werden die Ergebnisse zurückgegeben. Anforderungen an die Problembehandlungs-API sind Anforderungen mit langer Ausführungszeit. Daher kann es mehrere Minuten dauern, bis ein Ergebnis zurückgegeben wird. Protokolle werden in einem Container auf einem Speicherkonto gespeichert.

## <a name="log-in-with-armclient"></a>Anmelden mit ARMClient

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Problembehandlung für ein Virtual Network-Gateway


### <a name="post-the-troubleshoot-request"></a>POST-Vorgang für die Anforderung zur Problembehandlung

Im folgenden Beispiel wird der Status eines Virtual Network-Gateways abgefragt.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Da dieser Vorgang eine lange Ausführungsdauer hat, werden der URI zum Abfragen des Vorgangs und der URI für das Ergebnis im Antwortheader wie in der folgenden Antwort zurückgegeben:

**Wichtige Werte**

* **Azure-AsyncOperation**: Diese Eigenschaft enthält den URI zum Abfragen des Async-Problembehandlungsvorgangs.
* **Location**: Diese Eigenschaft enthält den URI, unter dem sich die Ergebnisse befinden, wenn der Vorgang abgeschlossen ist.

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Abfragen des Abschlusses des Async-Vorgangs

Verwenden Sie den Vorgangs-URI, um den Status des Vorgangs wie im folgenden Beispiel abzufragen:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Während der Vorgang läuft, wird für die Antwort **InProgress** angezeigt. Dies ist im folgenden Beispiel dargestellt:

```json
{
  "status": "InProgress"
}
```

Wenn der Vorgang abgeschlossen ist, ändert sich der Status in **Succeeded**.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Abrufen der Ergebnisse

Wenn der zurückgegebene Status **Succeeded** lautet, können Sie eine GET-Methode für den operationResult-URI aufrufen, um die Ergebnisse zu erhalten.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Die folgenden Antworten sind Beispiele für eine typische heruntergestufte Antwort, die zurückgegeben wird, wenn die Ergebnisse für die Problembehandlung eines Gateways abgefragt werden. Informationen dazu, was die Eigenschaften in der Antwort bedeuten, finden Sie unter [Grundlegendes zu den Ergebnissen](#understanding-the-results).

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Problembehandlung für Verbindungen

Im folgenden Beispiel wird der Status einer Verbindung abgefragt.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Der Vorgang für die Problembehandlung kann nicht parallel zu einer Verbindung und den dazugehörigen Gateways ausgeführt werden. Der Vorgang muss abgeschlossen werden, bevor er auf der vorherigen Ressource ausgeführt wird.

Da dies eine Transaktion mit langer Ausführungsdauer ist, werden der URI zum Abfragen des Vorgangs und der URI für das Ergebnis im Antwortheader wie in der folgenden Antwort zurückgegeben:

**Wichtige Werte**

* **Azure-AsyncOperation**: Diese Eigenschaft enthält den URI zum Abfragen des Async-Problembehandlungsvorgangs.
* **Location**: Diese Eigenschaft enthält den URI, unter dem sich die Ergebnisse befinden, wenn der Vorgang abgeschlossen ist.

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Abfragen des Abschlusses des Async-Vorgangs

Verwenden Sie den Vorgangs-URI, um den Status des Vorgangs wie im folgenden Beispiel abzufragen:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Während der Vorgang läuft, wird für die Antwort **InProgress** angezeigt. Dies ist im folgenden Beispiel dargestellt:

```json
{
  "status": "InProgress"
}
```

Wenn der Vorgang abgeschlossen ist, ändert sich der Status in **Succeeded**.

```json
{
  "status": "Succeeded"
}
```

Die folgenden Antworten sind Beispiele für eine typische Antwort, die zurückgegeben wird, wenn die Ergebnisse für die Problembehandlung einer Verbindung abgefragt werden.

### <a name="retrieve-the-results"></a>Abrufen der Ergebnisse

Wenn der zurückgegebene Status **Succeeded** lautet, können Sie eine GET-Methode für den operationResult-URI aufrufen, um die Ergebnisse zu erhalten.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Die folgenden Antworten sind Beispiele für eine typische Antwort, die zurückgegeben wird, wenn die Ergebnisse für die Problembehandlung einer Verbindung abgefragt werden.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Grundlegendes zu den Ergebnissen

Der Aktionstext enthält allgemeine Richtlinien zur Behebung des Problems. Wenn eine Aktion für das Problem ausgeführt werden kann, wird ein Link mit weiteren Anleitungen bereitgestellt. Falls es keine weitere Anleitungen gibt, enthält die Antwort die URL zum Öffnen einer Supportanfrage.  Weitere Informationen zu den Eigenschaften der Antwort und zu deren Inhalt finden Sie unter [Übersicht über die Network Watcher-Problembehandlung](network-watcher-troubleshoot-overview.md).

Anweisungen zum Herunterladen von Dateien von Azure-Speicherkonten finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ein weiteres Tool, das verwendet werden kann, ist der Storage-Explorer. Weitere Informationen zum Storage-Explorer finden Sie unter [Storage-Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Nächste Schritte

Wenn Einstellungen geändert wurden und die VPN-Konnektivität beenden, finden Sie unter [Verwalten von Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md) Informationen zum Ermitteln der fraglichen Netzwerksicherheitsgruppe und der Sicherheitsregeln.