---
title: Herstellen von Verbindungen zwischen virtuellen Netzwerken durch Peering virtueller Netzwerke – PowerShell | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mit Azure PowerShell durch Peering virtueller Netzwerke Verbindungen zwischen virtuellen Netzwerken herstellen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f5c90b7d79b31c321b00869c90b0261c0b4730d3
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66727758"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Herstellen von Verbindungen zwischen virtuellen Netzwerken durch Peering virtueller Netzwerke mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können durch Peering virtueller Netzwerke Verbindungen zwischen virtuellen Netzwerken herstellen. Sobald ein Peering zwischen virtuellen Netzwerken eingerichtet wurde, können Ressourcen in beiden virtuellen Netzwerken untereinander mit der gleichen Latenz und Bandbreite kommunizieren, als befänden sie sich im selben virtuellen Netzwerk. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen zweier virtueller Netzwerke
* Herstellen einer Verbindung zwischen zwei virtuellen Netzwerken mit einem Peering virtueller Netzwerke
* Bereitstellen eines virtuellen Computers (VM) in jedem virtuellen Netzwerk
* Kommunikation zwischen VMs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-virtual-networks"></a>Erstellen virtueller Netzwerke

Vor der Erstellung eines virtuellen Netzwerks müssen Sie eine Ressourcengruppe für das virtuelle Netzwerk und alle anderen in diesem Artikel erstellten Ressourcen erstellen. Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Im folgenden Beispiel wird ein virtuelles Netzwerk namens *myVirtualNetwork1* mit dem Adresspräfix *10.0.0.0/16* erstellt.

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Erstellen Sie mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration. Das folgende Beispiel erstellt eine Subnetzkonfiguration mit dem Adresspräfix 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Schreiben Sie die Subnetzkonfiguration mit [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) in das virtuelle Netzwerk. Dabei wird das folgende Subnetz erstellt:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Erstellen Sie ein virtuelles Netzwerk mit dem Adresspräfix 10.1.0.0/16 und einem Subnetz:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Einrichten eines Peerings von virtuellen Netzwerken

Richten Sie das Peering mit [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) ein. Das folgende Beispiel legt ein Peering für *myVirtualNetwork1* mit *myVirtualNetwork2* fest.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

In der Ausgabe, die nach der Ausführung des vorherigen Befehl zurückgegeben wird, können Sie sehen, dass **PeeringState** den Wert *Initiiert* aufweist. Das Peering verbleibt im Zustand *Initiiert*, bis Sie das Peering zwischen *myVirtualNetwork2* und *myVirtualNetwork1* einrichten. Richten Sie das Peering zwischen *myVirtualNetwork2* und *myVirtualNetwork1* ein.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

In der Ausgabe, die nach der Ausführung des vorherigen Befehl zurückgegeben wird, können Sie sehen, dass **PeeringState** den Wert *Verbunden* aufweist. Azure hat zudem den Peeringstatus des Peerings *myVirtualNetwork1-myVirtualNetwork2* in *Verbunden* geändert. Überprüfen Sie mit [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering), ob der Peeringstatus für das Peering *myVirtualNetwork1-myVirtualNetwork2* in *Verbunden* geändert wurde.

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Ressourcen in einem virtuellen Netzwerk können erst dann mit Ressourcen in anderen virtuellen Netzwerk kommunizieren, wenn **PeeringState** für die Peerings in beiden virtuellen Netzwerken den Wert *Verbunden* aufweist.

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie eine VM in jedem virtuellen Netzwerk, damit in einem späteren Schritt die Kommunikation untereinander möglich ist.

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

Erstellen Sie mit [New-AzVM](/powershell/module/az.compute/new-azvm) einen virtuellen Computer. Im folgenden Beispiel wird eine VM namens *myVm1* im virtuellen Netzwerk *myVirtualNetwork1* erstellt. Mit der Option `-AsJob` wird der virtuelle Computer im Hintergrund erstellt, sodass Sie mit dem nächsten Schritt fortfahren können. Geben Sie bei entsprechender Aufforderung den gewünschten Benutzernamen und das Kennwort für die Anmeldung an der VM ein.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Die Erstellung des virtuellen Computers dauert einige Minuten. Fahren Sie nicht mit späteren Schritten fort, bis Azure die VM erstellt und die Ausgabe an PowerShell zurückgegeben hat.

## <a name="communicate-between-vms"></a>Kommunikation zwischen VMs

Sie können über das Internet eine Verbindung mit der öffentlichen IP-Adresse einer VM herstellen. Geben Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse eines virtuellen Computers zurück. Das folgende Beispiel gibt die öffentliche IP-Adresse des virtuellen Computers *myVm1* zurück:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Erstellen Sie mit dem folgenden Befehl auf Ihrem lokalen Computer eine Remotedesktopsitzung mit dem virtuellen Computer *myVm1*. Ersetzen Sie `<publicIpAddress>` durch die vom vorherigen Befehl zurückgegebene IP-Adresse.

```
mstsc /v:<publicIpAddress>
```

Eine RDP-Datei (Remotedesktopprotokoll) wird erstellt, auf Ihren Computer heruntergeladen und geöffnet. Geben Sie den Benutzernamen und das Kennwort ein. (Unter Umständen müssen Sie auf **Weitere Optionen** und dann auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen der VM eingegeben haben.) Klicken Sie anschließend auf **OK**. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.

Aktivieren Sie auf der VM *myVm1* das Internet Control Message-Protokoll (ICMP) für die Windows-Firewall, damit Sie diese VM in einem späteren Schritt mit PowerShell über *myVm2* per Ping erreichen können:

```powershell
New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
```

In diesem Artikel wird Ping zwar für die Kommunikation zwischen VMs verwendet, aber das Zulassen des ICMP für die Windows-Firewall wird für Produktionsbereitstellungen nicht empfohlen.

Um eine Verbindung mit der VM *myVm2* herzustellen, geben Sie an einer Eingabeaufforderung auf der VM *myVm1* den folgenden Befehl ein:

```
mstsc /v:10.1.0.4
```

Da Sie Ping auf *myVm1* aktiviert haben, können Sie diesen virtuellen Computer nun über eine Eingabeaufforderung auf der VM *myVm2* über die IP-Adresse per Ping erreichen:

```
ping 10.0.0.4
```

Sie erhalten vier Antworten. Trennen Sie Ihre RDP-Sitzungen auf *myVm1* und *myVm2*.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, entfernen Sie sie mit dem Befehl [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie zwei Netzwerke in derselben Azure-Region durch das Peering virtueller Netzwerke verbunden werden. Sie können auch virtuelle Netzwerke in unterschiedlichen [ unterstützten Regionen](virtual-network-manage-peering.md#cross-region) und [verschiedenen Azure-Abonnements](create-peering-different-subscriptions.md#powershell) durch Peering verbinden und [Netzwerke vom Typ „Nabe und Speiche“](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) mit Peering erstellen. Weitere Informationen zum Peering in virtuellen Netzwerken finden Sie unter [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md) und [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md).

Sie können über ein VPN [eine Verbindung zwischen Ihrem eigenen Computer und einem virtuellen Netzwerk herstellen](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und mit Ressourcen in einem virtuellen Netzwerk oder in durch Peering verbundenen virtuellen Netzwerken interagieren. Wiederverwendbare Skripts, um viele der in den Artikeln zu virtuellen Netzwerken behandelten Aufgaben durchzuführen, finden Sie unter [Azure CLI-Beispiele für Linux-VMs](powershell-samples.md).