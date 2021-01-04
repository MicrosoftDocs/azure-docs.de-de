---
title: Problembehandlung bei Azure VNet-Gateways und -Verbindungen – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Auf dieser Seite wird erläutert, wie Azure Network Watcher zur Problembehandlung von PowerShell verwendet wird.
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
ms.openlocfilehash: 2c394eeef746db978360e4434295520536833558
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960511"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Problembehandlung bei Virtual Network-Gateways und -Verbindungen mit Azure Network Watcher und PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher bietet viele Funktionen zum Erfassen von Informationen über Ihre Netzwerkressourcen in Azure. Zu diesen Funktionen zählt die Ressourcenproblembehandlung. Die Ressourcenproblembehandlung kann über das Portal, PowerShell, die CLI oder die REST-API aufgerufen werden. Bei Aufruf untersucht Network Watcher die Integrität von Virtual Network-Gateways oder -Verbindungen und gibt entsprechende Ergebnisse zurück.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Dieses Szenario setzt voraus, dass Sie die Schritte unter [Erstellen einer Network Watcher-Instanz](network-watcher-create.md) bereits ausgeführt haben.

Eine Liste mit unterstützten Gatewaytypen finden Sie unter [Unterstützte Gatewaytypen](network-watcher-troubleshoot-overview.md#supported-gateway-types).

## <a name="overview"></a>Übersicht

Die Ressourcenproblembehandlung bietet die Möglichkeit zum Behandeln von Problemen, die bei Virtual Network-Gateways und -Verbindungen auftreten können. Wenn eine Anforderung an die Ressourcenproblembehandlung gesendet wird, werden Protokolle abgefragt und untersucht. Nach Abschluss der Untersuchung werden die Ergebnisse zurückgegeben. Anforderungen der Ressourcenproblembehandlung sind Anforderungen mit langer Ausführungszeit. Daher kann es mehrere Minuten dauern, bis ein Ergebnis zurückgegeben wird. Die Protokolle der Problembehandlung werden in einem Container in einem angegebenen Speicherkonto gespeichert.

## <a name="retrieve-network-watcher"></a>Abrufen von Network Watcher

Der erste Schritt besteht im Abrufen der Network Watcher-Instanz. Die Variable `$networkWatcher` wird in Schritt 4 an das Cmdlet `Start-AzNetworkWatcherResourceTroubleshooting` übergeben.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Abrufen einer Virtual Connection-Gatewayverbindung

In diesem Beispiel wird die Ressourcenproblembehandlung für eine Verbindung ausgeführt. Sie können auch ein Virtual Network-Gateway übergeben.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Die Ressourcenproblembehandlung gibt Daten zur Integrität der Ressource zurück. Zudem speichert sie Protokolle zur Überprüfung in einem Speicherkonto. In diesem Schritt erstellen wir ein Speicherkonto. Wenn bereits ein Speicherkonto vorhanden ist, können Sie es verwenden.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Ausführen der Network Watcher-Ressourcenproblembehandlung

Die Problembehandlung für Ressourcen wird mithilfe des Cmdlets `Start-AzNetworkWatcherResourceTroubleshooting` durchgeführt. An das Cmdlet werden das Network Watcher-Objekt, die ID der Verbindung oder des Virtual Network-Gateways, die Speicherkonto-ID und der Pfad zum Speichern der Ergebnisse übergeben.

> [!NOTE]
> Das Cmdlet `Start-AzNetworkWatcherResourceTroubleshooting` hat eine lange Ausführungsdauer, daher dauert die Ausführung unter Umständen einige Minuten.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Sobald Sie das Cmdlet ausführen, überprüft Network Watcher die Ressource auf ihre Integrität. Die Ergebnisse werden an die Shell zurückgegeben, und Protokolle der Ergebnisse werden im angegebenen Speicherkonto gespeichert.

## <a name="understanding-the-results"></a>Grundlegendes zu den Ergebnissen

Der Aktionstext enthält allgemeine Richtlinien zur Behebung des Problems. Wenn eine Aktion für das Problem ausgeführt werden kann, wird ein Link mit weiteren Anleitungen bereitgestellt. Falls es keine weitere Anleitungen gibt, enthält die Antwort die URL zum Öffnen einer Supportanfrage.  Weitere Informationen zu den Eigenschaften der Antwort und zu deren Inhalt finden Sie unter [Übersicht über die Network Watcher-Problembehandlung](network-watcher-troubleshoot-overview.md).

Anweisungen zum Herunterladen von Dateien von Azure-Speicherkonten finden Sie unter [Erste Schritte mit Azure Blob Storage mit .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Ein weiteres Tool, das verwendet werden kann, ist der Storage-Explorer. Weitere Informationen zum Storage-Explorer finden Sie unter [Storage-Explorer](https://storageexplorer.com/).

## <a name="next-steps"></a>Nächste Schritte

Wenn Einstellungen geändert wurden und die VPN-Konnektivität beenden, finden Sie unter [Verwalten von Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md) Informationen zum Ermitteln der fraglichen Netzwerksicherheitsgruppe und der Sicherheitsregeln.