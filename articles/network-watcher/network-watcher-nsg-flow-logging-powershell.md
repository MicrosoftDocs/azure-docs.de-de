---
title: Verwalten von Flowprotokollen für Netzwerksicherheitsgruppen mit Azure Network Watcher – PowerShell | Microsoft-Dokumentation
description: Auf dieser Seite wird erläutert, wie Flowprotokolle für Netzwerksicherheitsgruppen in Azure Network Watcher mit PowerShell verwaltet werden.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: eb48ffcc0ef0f6113e3880fc34f70ffd165ddd99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730316"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Konfigurieren von Flowprotokollen für Netzwerksicherheitsgruppen mit PowerShell

> [!div class="op_single_selector"]
> - [Azure-Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Datenflussprotokolle für Netzwerksicherheitsgruppen sind ein Network Watcher-Feature, mit dem Sie Informationen zu ein- und ausgehendem IP-Datenverkehr über eine Netzwerksicherheitsgruppe anzeigen können. Diese Datenflussprotokolle sind im JSON-Format geschrieben und zeigen ausgehende und eingehende Datenflüsse pro Regel, die NIC, auf die sich der Datenfluss bezieht, 5-Tupel-Informationen über den Datenfluss (Quell-/Ziel-IP, Quell-/Zielport, Protokoll) und Informationen zu zugelassenem oder verweigertem Datenverkehr.

## <a name="register-insights-provider"></a>Registrieren von Insights-Anbietern

Der Anbieter **Microsoft.Insights** muss registriert sein, damit die Datenflussprotokollierung ordnungsgemäß funktioniert. Wenn Sie sich nicht sicher sind, ob der Anbieter **Microsoft.Insights** registriert ist, führen Sie folgendes Skript aus.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Aktivieren von Flowprotokollen und Traffic Analytics für Netzwerksicherheitsgruppen

Der Befehl zum Aktivieren von Flowprotokollen wird im folgenden Beispiel gezeigt:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceid -WorkspaceLocation $workspaceRegion

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Für das von Ihnen angegebene Speicherkonto dürfen keine Netzwerkregeln konfiguriert sein, die den Netzwerkzugriff nur auf Microsoft-Dienste oder bestimmte virtuelle Netzwerke beschränken. Das Speicherkonto kann sich im gleichen Azure-Abonnement wie die NSG, für die Sie das Datenflussprotokoll aktivieren, befinden oder in einem anderen Azure-Abonnement. Wenn Sie unterschiedliche Abonnements verwenden, müssen beide demselben Azure Active Directory-Mandanten zugeordnet sein. Die für die beiden Abonnements verwendeten Konten müssen über die [erforderlichen Berechtigungen](required-rbac-permissions.md) verfügen.

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Deaktivieren von Traffic Analytics und Flowprotokollen für Netzwerksicherheitsgruppen

Verwenden Sie das folgende Beispiel, um Traffic Analytics und Flowprotokolle zu deaktivieren:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Herunterladen eines Flowprotokolls

Bei der Erstellung wird der Speicherort eines Flowprotokolls definiert. Ein nützliches Tool für den Zugriff auf diese in einem Speicherkonto gespeicherten Flowprotokolle ist der Microsoft Azure Storage-Explorer, der hier heruntergeladen werden kann: https://storageexplorer.com/

Wenn ein Speicherkonto angegeben wird, werden die Dateien der Flowprotokolle in einem Speicherkonto am folgenden Speicherort gespeichert:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Informationen zur Struktur des Protokolls finden Sie unter [Introduction to flow logging for Network Security Groups](network-watcher-nsg-flow-logging-overview.md) (Einführung in die Flowprotokollierung für Netzwerksicherheitsgruppen).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Ihre NSG-Flowprotokolle mit Power BI visualisieren](network-watcher-visualize-nsg-flow-logs-power-bi.md).

Erfahren Sie, wie Sie [Ihre NSG-Flowprotokolle mit Open Source-Tools visualisieren](network-watcher-visualize-nsg-flow-logs-open-source-tools.md).
