---
title: 'Schnellstart: Diagnostizieren von Problemen mit dem Filter für VM-Netzwerkdatenverkehr – Azure PowerShell'
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie Azure PowerShell verwenden, um mithilfe der IP-Flussüberprüfungsfunktion von Azure Network Watcher Probleme mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers diagnostizieren.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 8483c0d3b112408091e10bd9b57451cf2378c859
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494476"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>Schnellstart: Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers – Azure PowerShell

In dieser Schnellstartanleitung stellen Sie einen virtuellen Computer (Virtual Machine, VM) bereit und überprüfen dann die ausgehende Kommunikation für eine IP-Adresse und URL sowie die eingehende Kommunikation von einer IP-Adresse. Sie ermitteln die Ursache eines Kommunikationsfehlers und wie Sie ihn beheben können.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diese Schnellstartanleitung das Azure PowerShell-Modul `Az` verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.



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

Wenn Sie die Netzwerkkommunikation mit Network Watcher testen möchten, müssen Sie zuerst eine Network Watcher-Instanz in der Region aktivieren, in der sich der zu testende virtuelle Computer befindet. Danach können Sie die Kommunikation mit der IP-Flussüberprüfungsfunktion von Network Watcher testen.

### <a name="enable-network-watcher"></a>Aktivieren von Network Watcher

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

### <a name="use-ip-flow-verify"></a>Verwenden der IP-Flussüberprüfung

Wenn Sie einen virtuellen Computer erstellen, wird der ein- und ausgehende Netzwerkdatenverkehr des virtuellen Computers von Azure standardmäßig zugelassen bzw. abgelehnt. Die Azure-Standardeinstellungen können später außer Kraft gesetzt werden, um zusätzliche Arten von Datenverkehr zuzulassen oder abzulehnen. Verwenden Sie den Befehl [Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow), um zu testen, ob Datenverkehr für verschiedene Ziele und von einer Quell-IP-Adresse zugelassen oder abgelehnt wird.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit einer der IP-Adressen für www.bing.com:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

Nach einigen Sekunden werden Sie im zurückgegebenen Ergebnis darüber informiert, dass der Zugriff durch eine Sicherheitsregel mit dem Namen **AllowInternetOutbound** zugelassen wird.

Testen Sie die ausgehende Kommunikation des virtuellen Computers mit 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

Im zurückgegebenen Ergebnis werden Sie darüber informiert, dass der Zugriff durch eine Sicherheitsregel mit dem Namen **DefaultOutboundDenyAll** verweigert wird.

Testen Sie die eingehende Kommunikation des virtuellen Computers für 172.31.0.100:

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

Im zurückgegebenen Ergebnis werden Sie darüber informiert, dass der Zugriff aufgrund einer Sicherheitsregel mit dem Namen **DefaultInboundDenyAll** verweigert wird. Nachdem Sie nun wissen, welche Sicherheitsregeln den ein- und ausgehenden Datenverkehr eines virtuellen Computers zulassen oder ablehnen, können Sie Lösungen für die Probleme ermitteln.

## <a name="view-details-of-a-security-rule"></a>Anzeigen von Details einer Sicherheitsregel

Um zu bestimmen, warum die Regeln unter [Testen der Netzwerkkommunikation](#test-network-communication) die Kommunikation zulassen oder verhindern, überprüfen Sie die effektiven Sicherheitsregeln für die Netzwerkschnittstelle mit [Get-AzEffectiveNetworkSecurityGroup ](/powershell/module/az.network/get-azeffectivenetworksecuritygroup):

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

Die zurückgegebene Ausgabe enthält den folgenden Text für die Regel **AllowInternetOutbound**, die unter [Verwenden der IP-Datenflussüberprüfung](#use-ip-flow-verify) den ausgehenden Zugriff auf www.bing.com zugelassen hat:

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

Sie können der Ausgabe entnehmen, dass **Internet** als **DestinationAddressPrefix** verwendet wird. Es ist jedoch nicht klar, wie sich 13.107.21.200, die Adresse, die Sie unter [Verwenden der IP-Datenflussüberprüfung](#use-ip-flow-verify) getestet haben, auf **Internet** bezieht. Unter **ExpandedDestinationAddressPrefix** sind mehrere Adresspräfixe aufgelistet. Eines der Präfixe in der Liste ist **12.0.0.0/6**, das den IP-Adressbereich 12.0.0.1 bis 15.255.255.254 umfasst. Da 13.107.21.200 in diesem Adressbereich liegt, lässt die Regel **AllowInternetOutBound** den ausgehenden Datenverkehr zu. Darüber hinaus werden in der von `Get-AzEffectiveNetworkSecurityGroup` zurückgegebenen Ausgabe keine Regeln mit einer höheren **Priorität** (einer niedrigeren Zahl) aufgeführt, die diese Regel außer Kraft setzen. Um die ausgehende Kommunikation mit 13.107.21.200 zu verweigern, können Sie eine Sicherheitsregel mit einer höheren Priorität hinzufügen, die ausgehenden Datenverkehr über den Port 80 für die IP-Adresse verweigert.

Beim Ausführen des Befehls `Test-AzNetworkWatcherIPFlow` zum Testen der ausgehenden Kommunikation mit 172.131.0.100 (im Abschnitt [Verwenden der IP-Flussüberprüfung](#use-ip-flow-verify)) wurden Sie in der Ausgabe darüber informiert, dass die Kommunikation durch die Regel **DefaultOutboundDenyAll** verweigert wurde. Die Regel **DefaultOutboundDenyAll** entspricht der Regel **DenyAllOutBound**, die in der folgenden Ausgabe des Befehls `Get-AzEffectiveNetworkSecurityGroup` aufgeführt ist:

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

Die Regel listet **0.0.0.0/0** als **DestinationAddressPrefix** auf. Die Regel verweigert die ausgehende Kommunikation mit 172.131.0.100, weil die Adresse nicht im **DestinationAddressPrefix** einer der anderen ausgehenden Regeln in der Ausgabe des Befehls `Get-AzEffectiveNetworkSecurityGroup` enthalten ist. Um die ausgehende Kommunikation zuzulassen, können Sie eine Sicherheitsregel mit einer höheren Priorität hinzufügen, die ausgehenden Datenverkehr über den Port 80 für 172.131.0.100 zulässt.

Beim Ausführen des Befehls `Test-AzNetworkWatcherIPFlow` zum Testen der eingehenden Kommunikation von 172.131.0.100 (im Abschnitt [Verwenden der IP-Datenflussüberprüfung](#use-ip-flow-verify)) wurden Sie in der Ausgabe darüber informiert, dass die Kommunikation durch die Regel **DefaultInboundDenyAll** verweigert wurde. Die Regel **DefaultInboundDenyAll** entspricht der Regel **DenyAllInBound**, die in der folgenden Ausgabe des Befehls `Get-AzEffectiveNetworkSecurityGroup` aufgeführt ist:

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

Die Regel **DenyAllInBound** wird angewendet, da in der Ausgabe des Befehls `Get-AzEffectiveNetworkSecurityGroup` keine andere Regel mit einer höheren Priorität vorhanden ist, die auf dem virtuellen Computer eingehenden Datenverkehr von 172.131.0.100 am Port 80 zulässt. Um die eingehende Kommunikation zuzulassen, können Sie eine Sicherheitsregel mit einer höheren Priorität hinzufügen, die eingehenden Datenverkehr von 172.131.0.100 am Port 80 zulässt.

Mit den Überprüfungen in dieser Schnellstartanleitung wurde die Azure-Konfiguration getestet. Wenn die Überprüfungen zwar die erwarteten Ergebnisse zurückgeben, aber weiterhin Netzwerkprobleme auftreten, stellen Sie sicher, dass zwischen Ihrem virtuellen Computer und dem Endpunkt, mit dem Sie kommunizieren, keine Firewall vorhanden ist und das Betriebssystem auf Ihrem virtuellen Computer nicht über eine Firewall verfügt, die die Kommunikation zulässt oder verweigert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen virtuellen Computer erstellt sowie Filter für ein- und ausgehenden Netzwerkdatenverkehr diagnostiziert. Sie haben gelernt, dass Netzwerksicherheitsgruppen-Regeln den ein- und ausgehenden Datenverkehr eines virtuellen Computers zulassen oder verweigern. Erfahren Sie mehr über [Sicherheitsregeln](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und das [Erstellen von Sicherheitsregeln](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Auch wenn für den Netzwerkdatenverkehr die richtigen Filter vorhanden sind, kann die Kommunikation mit einem virtuellen Computer aufgrund der Routingkonfiguration fehlschlagen. Informationen zum Diagnostizieren von Routingproblemen in VM-Netzwerken finden Sie unter [Diagnostizieren von VM-Routingproblemen](diagnose-vm-network-routing-problem-powershell.md). Unter [Problembehandlung für Verbindungen](network-watcher-connectivity-powershell.md) erfahren Sie außerdem, wie Sie mit nur einem Tool Probleme mit Ausgangsrouting und Wartezeiten sowie Probleme mit dem Filtern des Datenverkehrs diagnostizieren.