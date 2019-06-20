---
title: Filtern von Netzwerkdatenverkehr – Azure PowerShell | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie an ein Subnetz gerichteten Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe unter Verwendung von PowerShell filtern.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 08031bc2ac29ea77374e21c4ce6f7bcf6151bcad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730034"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-powershell"></a>Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe unter Verwendung von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können eingehenden und ausgehenden Netzwerkdatenverkehr im Subnetz eines virtuellen Netzwerks mithilfe einer Netzwerksicherheitsgruppe filtern. Netzwerksicherheitsgruppen enthalten Sicherheitsregeln, die Netzwerkdatenverkehr nach IP-Adresse, Port und Protokoll filtern. Sicherheitsregeln werden auf Ressourcen angewendet, die in einem Subnetz bereitgestellt sind. In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Erstellen von Netzwerksicherheitsgruppe und Sicherheitsregeln
* Erstellen eines virtuellen Netzwerks und Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz
* Bereitstellen von virtuellen Computern in einem Subnetz
* Testen von Datenverkehrsfiltern

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Eine Netzwerksicherheitsgruppe enthält Sicherheitsregeln. Sicherheitsregeln geben eine Quelle und ein Ziel an. Bei Quellen und Zielen kann es sich um Anwendungssicherheitsgruppen handeln.

### <a name="create-application-security-groups"></a>Erstellen von Anwendungssicherheitsgruppen

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe für alle Ressourcen, die im Rahmen dieses Artikels erstellt werden. Das folgende Beispiel erstellt eine Ressourcengruppe am Standort *eastus*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Erstellen Sie mit [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) eine Anwendungssicherheitsgruppe. Mithilfe einer Anwendungssicherheitsgruppe können Sie Server mit ähnlichen Portfilterungsanforderungen gruppieren. Das folgende Beispiel erstellt zwei Anwendungssicherheitsgruppen:

```azurepowershell-interactive
$webAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Erstellen von Sicherheitsregeln

Erstellen Sie mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) eine Sicherheitsregel. Das folgende Beispiel erstellt eine Regel, die eingehenden Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe *myWebServers* über die Ports 80 und 443 zulässt:

```azurepowershell-interactive
$webRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

In diesem Artikel wird RDP (Port 3389) für den virtuellen Computer *myAsgMgmtServers* für das Internet verfügbar gemacht. In Produktionsumgebungen empfiehlt es sich, eine [VPN-basierte](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [private](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Netzwerkverbindung mit den Azure-Ressourcen herzustellen, die Sie verwalten möchten, anstatt den Port 3389 für das Internet verfügbar zu machen.

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) eine Netzwerksicherheitsgruppe. Das folgende Beispiel erstellt eine Netzwerksicherheitsgruppe namens *myNsg*:

```powershell-interactive
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Erstellen Sie mit [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) ein virtuelles Netzwerk. Das folgende Beispiel erstellt ein virtuelles Netzwerk namens *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Erstellen Sie mit [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) eine Subnetzkonfiguration, und schreiben Sie sie anschließend mit [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) in das virtuelle Netzwerk. Das folgende Beispiel fügt dem virtuellen Netzwerk ein Subnetz namens *mySubnet* hinzu und weist ihm die Netzwerksicherheitsgruppe *myNsg* zu:

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Rufen Sie vor dem Erstellen der virtuellen Computer mithilfe von [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) das virtuelle Netzwerkobjekt mit dem Subnetz ab:

```powershell-interactive
$virtualNetwork = Get-AzVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```

Erstellen Sie mit [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) eine öffentliche IP-Adresse für die einzelnen virtuellen Computer:

```powershell-interactive
$publicIpWeb = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmWeb

$publicIpMgmt = New-AzPublicIpAddress `
  -AllocationMethod Dynamic `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myVmMgmt
```

Erstellen Sie mit [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) zwei Netzwerkschnittstellen, und weisen Sie ihnen eine öffentliche IP-Adresse zu. Das folgende Beispiel erstellt eine Netzwerkschnittstelle, ordnet ihr die öffentliche IP-Adresse *myVmWeb* zu und fügt sie der Anwendungssicherheitsgruppe *myAsgWebServers* zu:

```powershell-interactive
$webNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

Das folgende Beispiel erstellt eine Netzwerkschnittstelle, ordnet ihr die öffentliche IP-Adresse *myVmMgmt* zu und fügt sie der Anwendungssicherheitsgruppe *myAsgMgmtServers* zu:

```powershell-interactive
$mgmtNic = New-AzNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Erstellen Sie zwei virtuelle Computer im virtuellen Netzwerk, im in einem späteren Schritt die Datenverkehrsfilterung überprüfen zu können.

Erstellen Sie mit [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) eine VM-Konfiguration und anschließend mit [New-AzVM](/powershell/module/az.compute/new-azvm) den virtuellen Computer. Das folgende Beispiel erstellt einen virtuellen Computer, der als Webserver fungiert. Mit der Option `-AsJob` wird der virtuelle Computer im Hintergrund erstellt, sodass Sie mit dem nächsten Schritt fortfahren können:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $webNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Erstellen Sie einen virtuellen Computer, der als Verwaltungsserver fungiert:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

Das Erstellen des virtuellen Computers dauert einige Minuten. Fahren Sie mit dem nächsten Schritt erst fort, wenn Azure die Erstellung des virtuellen Computers abgeschlossen hat.

## <a name="test-traffic-filters"></a>Testen von Datenverkehrsfiltern

Geben Sie mit [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) die öffentliche IP-Adresse eines virtuellen Computers zurück. Das folgende Beispiel gibt die öffentliche IP-Adresse des virtuellen Computers *myVmMgmt* zurück:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Erstellen Sie mit dem folgenden Befehl eine Remotedesktopsitzung zwischen Ihrem lokalen Computer und dem virtuellen Computer *myVmMgmt*. Ersetzen Sie `<publicIpAddress>` durch die vom vorherigen Befehl zurückgegebene IP-Adresse.

```
mstsc /v:<publicIpAddress>
```

Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus.

Geben Sie den Benutzernamen und das Kennwort ein, wie beim Erstellen des virtuellen Computers angegeben. (Unter Umständen müssen Sie auf **Weitere Optionen** und dann auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.) Klicken Sie anschließend auf **OK**. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wählen Sie **Ja** aus, um mit dem Herstellen der Verbindung fortzufahren.

Die Verbindung wird erfolgreich hergestellt, da am Port 3389 eingehender Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe *myAsgMgmtServers* zugelassen wird, in der sich die an den virtuellen Computer *myVmMgmt* angefügte Netzwerkschnittstelle befindet.

Erstellen Sie mithilfe des folgenden PowerShell-Befehls eine Remotedesktopverbindung zwischen den virtuellen Computern *myVmMgmt* und *myVmWeb*:

``` 
mstsc /v:myvmWeb
```

Die Verbindung wird erfolgreich hergestellt, da eine Standardsicherheitsregel innerhalb jeder Netzwerksicherheitsgruppe Datenverkehr über alle Ports zwischen allen IP-Adressen in einem virtuellen Netzwerk zulässt. Über das Internet kann keine Remotedesktopverbindung mit dem virtuellen Computer *myVmWeb* hergestellt werden, da die Sicherheitsregel für *myAsgWebServers* am Port 3389 keinen eingehenden Datenverkehr aus dem Internet zulässt.

Installieren Sie mithilfe des folgenden PowerShell-Befehls Microsoft IIS auf dem virtuellen Computer *myVmWeb*:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Trennen Sie nach Abschluss der IIS-Installation die Verbindung mit dem virtuellen Computer *myVmWeb*. Dadurch befinden Sie sich in der Remotedesktopverbindung des virtuellen Computers *myVmMgmt*. Navigieren Sie in einem Internetbrowser zu „\//myVmWeb“, um den IIS-Begrüßungsbildschirm anzuzeigen.

Trennen Sie die Verbindung mit dem virtuellen Computer *myVmMgmt*.

Geben Sie auf Ihrem Computer in PowerShell den folgenden Befehl ein, um die öffentliche IP-Adresse des Servers *myVmWeb* abzurufen:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Navigieren Sie auf Ihrem Computer in einem Internetbrowser zu `http://<public-ip-address-from-previous-step>`, um sich zu vergewissern, dass Sie außerhalb von Azure auf den Webserver *myVmWeb* zugreifen können. Die Verbindung wird erfolgreich hergestellt, da am Port 80 eingehender Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe *myAsgWebServers* zugelassen wird, in der sich die an den virtuellen Computer *myVmWeb* angefügte Netzwerkschnittstelle befindet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle darin enthaltenen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) entfernen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Netzwerksicherheitsgruppe erstellt und dem Subnetz eines virtuellen Netzwerks zugeordnet. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen – Übersicht](security-overview.md) sowie unter [Verwalten einer Netzwerksicherheitsgruppe](manage-network-security-group.md).

Azure leitet standardmäßig Datenverkehr zwischen Subnetzen weiter. Sie können Datenverkehr zwischen Subnetzen stattdessen auch über einen virtuellen Computer weiterleiten, der als Firewall dient. Informationen dazu finden Sie unter [Erstellen einer Routingtabelle](tutorial-create-route-table-powershell.md).
