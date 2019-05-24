---
title: Azure PowerShell-Skriptbeispiel – Erstellen eines Netzwerks für Anwendungen mit mehreren Ebenen | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen eines virtuellen Netzwerks für Anwendungen mit mehreren Ebenen.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: d5433cdd6e6702a289fe0a4b6eab87e9f196fb1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66156912"
---
# <a name="create-a-network-for-multi-tier-applications"></a>Erstellen eines Netzwerks für Anwendungen mit mehreren Ebenen

In diesem Skriptbeispiel wird ein virtuelles Netzwerk mit Front-End- und Back-End-Subnetz erstellt. Der Datenverkehr am Front-End-Subnetz ist auf HTTP und SSH beschränkt, während der Datenverkehr zum Back-End-Subnetz auf MySQL und Port 3306 beschränkt ist. Nach dem Ausführen des Skripts haben Sie zwei virtuelle Computer – einen in jedem Subnetz – denen Sie Webserver- und MySQL-Software bereitstellen können.

Installieren Sie bei Bedarf Azure PowerShell anhand der Anleitung im [Azure PowerShell-Handbuch](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/), und führen Sie dann `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung 

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, ein virtuelles Netzwerk und Netzwerksicherheitsgruppen zu erstellen. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Erstellt ein virtuelles Azure-Netzwerk und ein Front-End-Subnetz. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Erstellt ein Back-End-Subnetz. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Erstellt eine öffentliche IP-Adresse für den Zugriff auf den virtuellen Computer aus dem Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Erstellt virtuelle Netzwerkschnittstellen und fügt sie an das Front-End- und Back-End-Subnetz des virtuellen Netzwerks an. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Erstellt Netzwerksicherheitsgruppen (NSG), die dem Front-End- und Back-End-Subnetz zugeordnet sind. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |Erstellt NSG-Regeln, die bestimmte Ports bei bestimmten Subnetzen zulassen oder blockieren. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Erstellt virtuelle Computer und fügt jedem virtuellen Computer eine NIC hinzu. Dieser Befehl legt außerdem das zu verwendende VM-Image und die Administratoranmeldeinformationen fest. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe und alle darin enthaltenen Ressourcen. |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/azure/overview).

Zusätzliche Netzwerk-PowerShell-Skriptbeispiele finden Sie unter [Azure-Dokumentation für Netzwerke – Übersicht](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
