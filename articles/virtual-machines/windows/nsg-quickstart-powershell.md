---
title: Öffnen von Ports für einen virtuellen Computer mithilfe von Azure PowerShell
description: Hier erfahren Sie, wie Sie mit Azure PowerShell für Ihre VM einen Port öffnen bzw. einen Endpunkt erstellen.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a432ce978f6fa9e3a472cb15e9ef9241bc41004d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891753"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-using-powershell"></a>Öffnen von Ports und Endpunkten für einen virtuellen Computer mit PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Schnellbefehle
Zum Erstellen einer Netzwerksicherheitsgruppe und von ACL-Regeln (Access Control Lists, Zugriffssteuerungslisten) müssen Sie [die neueste Version von Azure PowerShell installieren](/powershell/azure/). Sie können [diese Schritte auch über das Azure-Portal ausführen](nsg-quickstart-portal.md).

Melden Sie sich bei Ihrem Azure-Konto an:

```powershell
Connect-AzAccount
```

Ersetzen Sie in den folgenden Beispielen die Beispielparameternamen durch Ihre eigenen Werte. Beispielparameternamen sind etwa *myResourceGroup* , *myNetworkSecurityGroup* oder *myVnet*.

Erstellen Sie eine Regel mit [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig). Im folgenden Beispiel wird die Regel *myNetworkSecurityGroupRule* erstellt, um *TCP* -Datenverkehr an Port *80* zuzulassen:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Als Nächstes erstellen Sie die Netzwerksicherheitsgruppe mit [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) und weisen die zuvor erstellte HTTP-Regel wie folgt zu. Im folgenden Beispiel wird die Netzwerksicherheitsgruppe *myNetworkSecurityGroup* erstellt:

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Nun weisen Sie Ihre Netzwerksicherheitsgruppe einem Subnetz zu. Im folgenden Beispiel wird mit [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) der Variable *$vnet* das vorhandene virtuelle Netzwerk *myVnet* zugewiesen:

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Weisen Sie mit [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) die Netzwerksicherheitsgruppe Ihrem Subnetz zu. Im folgenden Beispiel wird das Subnetz *mySubnet* Ihrer Netzwerksicherheitsgruppe zugewiesen:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Aktualisieren Sie abschließend Ihr virtuelles Netzwerk mit [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork), damit die Änderungen wirksam werden:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Weitere Informationen zu Netzwerksicherheitsgruppen
Mit diesen Schnellbefehlen können Sie den Datenverkehr zu Ihrem virtuellen Computer einrichten. Netzwerksicherheitsgruppen bieten eine Vielzahl erstklassiger Funktionen sowie eine differenzierte Steuerung des Ressourcenzugriffs. Weitere Informationen über die [Erstellung von Netzwerksicherheitsgruppen und ACL-Regeln erhalten Sie hier](tutorial-virtual-network.md#secure-network-traffic).

Bei hoch verfügbaren Webanwendungen sollten Sie die virtuellen Computer hinter einem Azure Load Balancer anordnen. Der Load Balancer verteilt den Datenverkehr auf virtuelle Computer, mit einer Netzwerksicherheitsgruppe, die den Datenverkehr filtert. Weitere Informationen finden Sie unter [Lastenausgleich für virtuelle Linux-Computer in Azure zum Erstellen einer hoch verfügbaren Anwendung](tutorial-load-balancer.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Beispiel haben Sie eine einfache Regel erstellt, die HTTP-Datenverkehr zulässt. Informationen zum Erstellen von detaillierteren Umgebungen finden Sie in den folgenden Artikeln:

* [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Was ist eine Netzwerksicherheitsgruppe?](../../virtual-network/network-security-groups-overview.md)
* [Übersicht über Azure Load Balancer](../../load-balancer/load-balancer-overview.md)
