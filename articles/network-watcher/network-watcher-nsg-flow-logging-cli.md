---
title: Verwalten von Datenflussprotokollen für Netzwerksicherheitsgruppen mit Azure Network Watcher – Azure CLI | Microsoft-Dokumentation
description: Auf dieser Seite wird erläutert, wie Datenflussprotokolle für Netzwerksicherheitsgruppen in Azure Network Watcher mit der Azure CLI verwaltet werden.
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
ms.openlocfilehash: 7f964fd78845e663c1ebcde7e79bfb7295c72f31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730575"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Konfigurieren von Datenflussprotokollen für Netzwerksicherheitsgruppen mit der Azure CLI

> [!div class="op_single_selector"]
> - [Azure-Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-nsg-flow-logging-cli.md)
> - [REST-API](network-watcher-nsg-flow-logging-rest.md)

Datenflussprotokolle für Netzwerksicherheitsgruppen sind ein Network Watcher-Feature, mit dem Sie Informationen zu ein- und ausgehendem IP-Datenverkehr über eine Netzwerksicherheitsgruppe anzeigen können. Diese Datenflussprotokolle sind im JSON-Format geschrieben und zeigen ausgehende und eingehende Datenflüsse pro Regel, die NIC, auf die sich der Datenfluss bezieht, 5-Tupel-Informationen über den Datenfluss (Quell-/Ziel-IP, Quell-/Zielport, Protokoll) und Informationen zu zugelassenem oder verweigertem Datenverkehr.

Um die Schritte in diesem Artikel ausführen zu können, müssen Sie [die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (CLI) installieren](/cli/azure/install-azure-cli).

## <a name="register-insights-provider"></a>Registrieren von Insights-Anbietern

Der Anbieter **Microsoft.Insights** muss registriert sein, damit die Datenflussprotokollierung ordnungsgemäß funktioniert. Wenn Sie sich nicht sicher sind, ob der Anbieter **Microsoft.Insights** registriert ist, führen Sie folgendes Skript aus.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Aktivieren von Flowprotokollen für Netzwerksicherheitsgruppen

Der Befehl zum Aktivieren von Flowprotokollen wird im folgenden Beispiel gezeigt:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName
# Configure 
az network watcher flow-log configure --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName  --format JSON --log-version 2
```

Für das von Ihnen angegebene Speicherkonto dürfen keine Netzwerkregeln konfiguriert sein, die den Netzwerkzugriff nur auf Microsoft-Dienste oder bestimmte virtuelle Netzwerke beschränken. Das Speicherkonto kann sich im gleichen Azure-Abonnement wie die NSG, für die Sie das Datenflussprotokoll aktivieren, befinden oder in einem anderen Azure-Abonnement. Wenn Sie unterschiedliche Abonnements verwenden, müssen beide demselben Azure Active Directory-Mandanten zugeordnet sein. Die für die beiden Abonnements verwendeten Konten müssen über die [erforderlichen Berechtigungen](required-rbac-permissions.md) verfügen. 

Wenn sich das Speicherkonto in einer anderen Ressourcengruppe oder einem anderen Abonnement als die Netzwerksicherheitsgruppe befindet, geben Sie statt des Namens die vollständige ID des Speicherkontos an. Wenn sich beispielsweise das Speicherkonto in einer Ressourcengruppe namens *RG-Storage* befindet, würden Sie anstelle von *storageAccountName* im vorherigen Befehl die ID */subscriptions/{Abonnement-ID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName* angeben.

## <a name="disable-network-security-group-flow-logs"></a>Deaktivieren von Flowprotokollen für Netzwerksicherheitsgruppen

Verwenden Sie das folgende Beispiel, um Flowprotokolle zu deaktivieren:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
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
