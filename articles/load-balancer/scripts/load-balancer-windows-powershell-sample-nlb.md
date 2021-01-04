---
title: 'Lastenausgleich für Datenverkehr zu VMs zur Erzielung von Hochverfügbarkeit: Azure PowerShell'
titleSuffix: Azure Load Balancer
description: Dieses Azure PowerShell-Skriptbeispiel veranschaulicht, wie Sie zum Ermöglichen der Hochverfügbarkeit einen Lastausgleich für den Datenverkehr zu virtuellen Computern vornehmen.
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a485ff81deb2cbf9a18395520bd6e8579c311646
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696486"
---
# <a name="azure-powershell-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure PowerShell-Skriptbeispiel: Lastenausgleich für den Datenverkehr zu virtuellen Computern für Hochverfügbarkeit

Dieses Azure PowerShell-Beispielskript erstellt alle Komponenten, die zum Ausführen mehrerer Windows-VMs in einer Konfiguration mit Hochverfügbarkeit und Lastenausgleich benötigt werden. Nach dem Ausführen dieses Skripts verfügen Sie über drei virtuelle Computer, die in einer Azure-Verfügbarkeitsgruppe zusammengefasst und über eine Azure Load Balancer-Instanz zugänglich sind.

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](/powershell/azure/), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, einen virtuellen Computer, eine Verfügbarkeitsgruppe, einen Load Balancer und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt eine Subnetzkonfiguration. Diese Konfiguration wird mit dem Prozess der Erstellung des virtuellen Netzwerks verwendet. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellt ein virtuelles Azure-Netzwerk und ein Subnetz. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Erstellt eine öffentliche IP-Adresse mit einer statischen IP-Adresse und zugeordnetem DNS-Namen. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Erstellt eine Azure Load Balancer-Instanz. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Erstellt einen Lastenausgleichtest. Ein Lastenausgleichtest wird verwendet, um jeden virtuellen Computer in der Load Balancer-Gruppe zu überwachen. Falls eine VM nicht verfügbar ist, wird der Datenverkehr nicht an diese VM geroutet. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Erstellt eine Load Balancer-Regel. In diesem Beispiel wird eine Regel für den Port 80 erstellt. Wenn HTTP-Datenverkehr beim Load Balancer eingeht, wird dieser an Port 80 einer der VMs in der Load Balancer-Gruppe geroutet. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Erstellt eine Regel des Load Balancers (Network Address Translation, NAT).  Mithilfe von NAT-Regeln wird ein Load Balancer-Port einem Port auf einer VM zugeordnet. In diesem Beispiel wird eine NAT-Regel für den SSH-Datenverkehr für jede VM in der Load Balancer-Gruppe erstellt.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Erstellt eine Netzwerksicherheitsgruppe (NSG), die als Sicherheitsgrenze zwischen dem Internet und dem virtuellen Computer fungiert. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Erstellt eine NSG-Regel zum Zulassen von eingehendem Datenverkehr. In diesem Beispiel wird Port 22 für SSH-Datenverkehr geöffnet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Erstellt eine virtuelle Netzwerkkarte und verbindet diese mit dem virtuellen Netzwerk, dem Subnetz und der NSG. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Erstellt eine Verfügbarkeitsgruppe. Verfügbarkeitsgruppen stellen die Verfügbarkeit von Anwendungen sicher, indem sie die virtuellen Computer auf physische Ressourcen verteilen. So wirken sich Ausfälle nicht auf die gesamte Gruppe aus. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Erstellt eine VM-Konfiguration. Diese Konfiguration umfasst Informationen wie VM-Name, Betriebssystem und Administratoranmeldeinformationen. Die Konfiguration wird während der VM-Erstellung verwendet. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Erstellt den virtuellen Computer und verbindet diesen mit der Netzwerkkarte, dem virtuellen Netzwerk, dem Subnetz und der NSG. Dieser Befehl legt außerdem das zu verwendende VM-Image und die Administratoranmeldeinformationen fest.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Zusätzliche Netzwerk-PowerShell-Skriptbeispiele finden Sie unter [Azure-Dokumentation für Netzwerke – Übersicht](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).