---
title: 'Azure PowerShell-Skriptbeispiel: Lastenausgleich für mehrere Websites mit der Azure PowerShell | Microsoft-Dokumentation'
description: 'Azure PowerShell-Skriptbeispiel: Lastenausgleich für mehrere Websites auf demselben virtuellen Computer'
services: load-balancer
documentationcenter: load-balancer
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 145b5a686b2707b6e60a481c08f187164d788580
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66121232"
---
# <a name="load-balance-multiple-websites"></a>Lastenausgleich für mehrere Websites

Dieses Skriptbeispiel erstellt ein virtuelles Netzwerk mit zwei virtuellen Computern, die einer Verfügbarkeitsgruppe angehören. Ein Load Balancer leitet Datenverkehr für zwei separate IP-Adressen an die beiden virtuellen Computer. Nach der Ausführung des Skripts können Sie Webserversoftware auf den virtuellen Computern bereitstellen und mehrere Websites (jede mit eigener IP-Adresse) hosten.

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/load-balancer/load-balance-multiple-web-sites-vm/load-balance-multiple-web-sites-vm.ps1  "Load balance multiple web sites")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, ein virtuelles Netzwerk, einen Load Balancer und alle zugehörigen Ressourcen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Erstellt eine Azure-Verfügbarkeitsgruppe, um Hochverfügbarkeit bereitzustellen |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt eine Subnetzkonfiguration. Diese Konfiguration wird mit dem Prozess der Erstellung des virtuellen Netzwerks verwendet. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellen Sie ein virtuelles Netzwerk. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | Erstellt eine Front-End-IP-Konfiguration für einen Lastenausgleich |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | Erstellt eine Back-End-Adresspoolkonfiguration für einen Lastenausgleich |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Erstellt einen NLB-Test. Mithilfe eines NLB-Tests wird jede VM in der NLB-Gruppe überwacht. Falls eine VM nicht verfügbar ist, wird der Datenverkehr nicht an diese VM geroutet. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Erstellt eine NLB-Regel. In diesem Beispiel wird eine Regel für den Port 80 erstellt. Wenn HTTP-Datenverkehr beim NLB eingeht, wird dieser an Port 80 einer der VMs in der NLB-Gruppe geroutet. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | Erstellt ein Lastenausgleichsmodul. |
| [New-AzNetworkInterfaceIpConfig](/powershell/module/az.network/new-aznetworkinterfaceipconfig) | Definiert erweiterte Features für eine virtuelle Netzwerkschnittstelle |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Erstellt eine Netzwerkschnittstelle. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Erstellt eine VM-Konfiguration. Diese Konfiguration umfasst Informationen wie VM-Name, Betriebssystem und Administratoranmeldeinformationen. Die Konfiguration wird während der VM-Erstellung verwendet. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Erstellen Sie eine VM. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Entfernt eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/overview).

Zusätzliche Netzwerk-PowerShell-Skriptbeispiele finden Sie unter [Azure-Dokumentation für Netzwerke – Übersicht](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
