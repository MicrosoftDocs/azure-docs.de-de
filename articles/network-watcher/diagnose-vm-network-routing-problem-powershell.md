---
title: Diagnostizieren von Problemen mit dem Netzwerkrouting eines virtuellen Computers – Azure PowerShell | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit dem Netzwerkrouting eines virtuellen Computers mit der Funktion „Nächster Hop“ von Azure Network Watcher diagnostizieren.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 08d273ce6e6ecb1b10d3c39a0954d430a3cb674a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730754"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostizieren von Problemen mit dem Netzwerkrouting eines virtuellen Computers – Azure PowerShell

In diesem Artikel stellen Sie einen virtuellen Computer (Virtual Machine, VM) bereit und überprüfen dann die Kommunikation für eine IP-Adresse und URL. Sie ermitteln die Ursache eines Kommunikationsfehlers und wie Sie ihn beheben können.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel das `Az`-Modul für Azure PowerShell verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.



## <a name="create-a-vm"></a>Erstellen einer VM

Bevor Sie einen virtuellen Computer erstellen können, müssen Sie eine Ressourcengruppe erstellen, die den virtuellen Computer enthalten soll. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer. Wenn Sie diesen Schritt ausführen, werden Sie aufgefordert, Anmeldeinformationen einzugeben. Die eingegebenen Werte werden als Benutzername und Kennwort für den virtuellen Computer konfiguriert.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Fahren Sie erst mit den übrigen Schritten fort, wenn der virtuelle Computer erstellt und von PowerShell eine Ausgabe zurückgegeben wurde.

## <a name="test-network-communication"></a>Testen der Netzwerkkommunikation

Wenn Sie die Netzwerkkommunikation mit Network Watcher testen möchten, müssen Sie zuerst eine Komponente zur Netzwerküberwachung in der Region aktivieren, in der sich der zu testende virtuelle Computer befindet. Danach können Sie die Kommunikation mit der Network Watcher-Funktion „Nächster Hop“ testen.

## <a name="enable-network-watcher"></a>Aktivieren von Network Watcher

Wenn Sie bereits eine Komponente zur Netzwerküberwachung in der Region „USA, Osten“ aktiviert haben, verwenden Sie [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher), um die Komponente zur Netzwerküberwachung abzurufen. Im folgenden Beispiel wird eine vorhandene Komponente zur Netzwerküberwachung namens *NetworkWatcher_eastus* abgerufen, die sich in der Ressourcengruppe *NetworkWatcherRG* befindet:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Wenn Sie noch keine Komponente zur Netzwerküberwachung in der Region „USA, Osten“ aktiviert haben, verwenden Sie [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher), um eine Komponente zur Netzwerküberwachung in der Region „USA, Osten“ zu erstellen:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Verwenden der Funktion „Nächster Hop“

Azure erstellt automatisch Routen zu Standardzielen. Sie können benutzerdefinierte Routen erstellen, mit denen die Standardrouten außer Kraft gesetzt werden. In manchen Fällen können benutzerdefinierte Routen zu Fehlern bei der Kommunikation führen. Verwenden Sie zum Testen des Routings von einem virtuellen Computer den Befehl [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop), um den nächsten Routinghop zu bestimmen, wenn Datenverkehr für eine bestimmte Adresse bestimmt ist.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit einer der IP-Adressen für www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Nach wenigen Sekunden informiert Sie die Ausgabe darüber, dass **NextHopType** **Internet** und die **RouteTableId** **Systemroute** ist. Durch dieses Ergebnis wissen Sie, dass eine gültige Route zum Ziel besteht.

Testen Sie die ausgehende Kommunikation des virtuellen Computers für 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Die zurückgegebene Ausgabe informiert Sie darüber, dass **Keiner** **NextHopType** ist und die **RouteTableId** ebenfalls **Systemroute** lautet. Durch dieses Ergebnis wissen Sie, dass zwar eine gültige Systemroute zum Ziel besteht, aber kein nächster Hop zum Weiterleiten des Datenverkehrs an das Ziel vorhanden ist.

## <a name="view-details-of-a-route"></a>Anzeigen von Details einer Route

Zur weiteren Analyse des Routings überprüfen Sie die effektiven Routen für die Netzwerkschnittstelle mit dem Befehl [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable):

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Eine Ausgabe, die den folgenden Text enthält, wird zurückgegeben:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Wie Sie in der vorherigen Ausgabe sehen, leitet die Route mit dem Wert **0.0.0.0/0** für **AddressPrefix** den gesamten Datenverkehr, der nicht für Adressen in den Adresspräfixen anderer Routen bestimmt ist, mit einem nächsten Hop von **Internet** weiter. Wie Sie in der Ausgabe auch sehen können, ist zwar eine Standardroute zum Präfix 172.16.0.0/12 vorhanden, die die Adresse 172.31.0.100 enthält, aber **nextHopType** lautet **Keiner**. Azure erstellt eine Standardroute zu 172.16.0.0/12, gibt aber einen Typ des nächsten Hops erst dann an, wenn es einen Grund dafür gibt. Wenn Sie z. B. den Adressbereich 172.16.0.0/12 zum Adressraum des virtuellen Netzwerks hinzufügen, ändert Azure den **nextHopType** für die Route in **Virtuelles Netzwerk**. Bei einer Überprüfung würde dann **Virtuelles Netzwerk** als **nextHopType** angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie einen virtuellen Computer erstellt und das Netzwerkrouting vom virtuellen Computer diagnostiziert. Sie haben erfahren, dass Azure mehrere Standardrouten erstellt, und Sie haben das Routing an zwei verschiedene Ziele getestet. Erfahren Sie mehr über das [Routing in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und das [Erstellen benutzerdefinierter Routen](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Für ausgehende Verbindungen virtueller Computer können Sie auch die Latenz sowie den zulässigen und verweigerten Netzwerkdatenverkehr zwischen dem virtuellen Computer und einem Endpunkt mithilfe der Funktion [Problembehandlung für Verbindung](network-watcher-connectivity-powershell.md) von Network Watcher bestimmen. Sie können die Kommunikation zwischen einem virtuellen Computer und einem Endpunkt, z. B. einer IP-Adresse oder URL, mithilfe der Verbindungsmonitorfunktion von Network Watcher über einen Zeitraum überwachen. Informationen hierzu finden Sie unter [Überwachen einer Netzwerkverbindung](connection-monitor.md).
