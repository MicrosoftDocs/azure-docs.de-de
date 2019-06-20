---
title: Azure PowerShell-Skriptbeispiel – Erstellen eines Netzwerks für Anwendungen mit mehreren Ebenen | Microsoft-Dokumentation
description: Azure PowerShell-Skriptbeispiel – Erstellen eines virtuellen Netzwerks für Anwendungen mit mehreren Ebenen.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 2fad78db4fdc92f3dc9c0f320c36d12dea554a61
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725393"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>Skriptbeispiel für das Erstellen eines Netzwerks für Anwendungen mit mehreren Ebenen

In diesem Skriptbeispiel wird ein virtuelles Netzwerk mit Front-End- und Back-End-Subnetz erstellt. Der Datenverkehr am Front-End-Subnetz ist auf HTTP und SSH beschränkt, während der Datenverkehr zum Back-End-Subnetz auf MySQL und Port 3306 beschränkt ist. Nach dem Ausführen des Skripts haben Sie zwei virtuelle Computer – einen in jedem Subnetz – denen Sie Webserver- und MySQL-Software bereitstellen können.

Sie können das Skript über Azure [Cloud Shell](https://shell.azure.com/powershell) oder eine lokale PowerShell-Installation ausführen. Wenn Sie PowerShell lokal verwenden, müssen Sie für dieses Skript mindestens Version 1.0.0 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Beispielskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
Eine Subnetz-ID wird zugewiesen, nachdem Sie ein virtuelles Netzwerk erstellt haben, insbesondere mit dem Cmdlet „New-AzVirtualNetwork“ mit der Option „-Subnet“. Wenn Sie das Subnetz mit dem Cmdlet „New-AzVirtualNetworkSubnetConfig“ vor dem Aufruf von „New-AzVirtualNetwork“ konfigurieren, wird die Subnetz-ID erst nach dem Aufruf von „New-AzVirtualNetwork“ angezeigt.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Führen Sie den folgenden Befehl aus, um die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Erläuterung des Skripts

In diesem Skript werden die folgenden Befehle verwendet, um eine Ressourcengruppe, ein virtuelles Netzwerk und Netzwerksicherheitsgruppen zu erstellen. Jeder Befehl in der folgenden Tabelle ist mit der zugehörigen Dokumentation verknüpft:

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

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/overview).

Weitere PowerShell-Skriptbeispiele für virtuelle Netzwerke finden Sie unter [PowerShell-Beispiele für virtuelle Netzwerke](../powershell-samples.md).
