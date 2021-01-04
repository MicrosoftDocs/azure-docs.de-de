---
title: Erstellen und Konfigurieren eines Azure DDoS Protection-Plans mit Azure PowerShell
description: Erfahren Sie, wie Sie einen DDoS Protection-Plan über Azure PowerShell erstellen
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: yitoh
ms.openlocfilehash: 49fa0c849a6b2eab0647922a711c50b4fe762584
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095661"
---
# <a name="quickstart-create-and-configure-azure-ddos-protection-standard-using-azure-powershell"></a>Schnellstart: Erstellen und Konfigurieren von Azure DDoS Protection Standard mit Azure PowerShell

Erfahren Sie, wie Sie mit der Verwendung von Azure DDoS Protection Standard mit Azure PowerShell beginnen. 

Ein DDoS-Schutzplan definiert abonnementübergreifend eine Reihe von virtuellen Netzwerken, für die der DDoS-Schutzstandard aktiviert ist. Sie können einen DDoS-Schutzplan für Ihre Organisation konfigurieren und virtuelle Netzwerke über verschiedene Abonnements hinweg mit demselben Plan verknüpfen. 

In diesem Schnellstart erstellen Sie einen DDoS-Schutzplan und verknüpfen ihn mit einem virtuellen Netzwerk. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure PowerShell (lokal installiert) oder Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-ddos-protection-plan"></a>Erstellen eines DDoS Protection-Plans

In Azure ordnen Sie verwandte Ressourcen einer Ressourcengruppe zu. Sie können entweder eine vorhandene Ressourcengruppe verwenden oder eine neue erstellen.

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Ressourcengruppe. In diesem Beispiel legen wir für die Ressourcengruppe den Namen _MyResourceGroup_ fest, und wir verwenden den Standort _USA, Osten_ :

```azurepowershell-interactive
New-AzResourceGroup -Name MyResourceGroup -Location "East US"
```

Erstellen Sie nun einen DDoS-Schutzplan mit dem Namen _MyDdosProtectionPlan_ :

```azurepowershell-interactive
New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"
```

## <a name="enable-ddos-for-a-virtual-network"></a>Aktivieren von DDoS für ein virtuelles Netzwerk

### <a name="enable-ddos-for-a-new-virtual-network"></a>Aktivieren von DDoS für ein neues virtuelles Netzwerk

Sie können den DDoS-Schutz beim Erstellen eines virtuellen Netzwerks aktivieren. In diesem Beispiel legen wir für das virtuelle Netzwerk den Namen _MyVnet_ fest: 

```azurepowershell-interactive
New-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup -Location "East US" -AddressPrefix 10.0.0.0/16
```

### <a name="enable-ddos-for-an-existing-virtual-network"></a>Aktivieren von DDoS für ein vorhandenes virtuelles Netzwerk

Sie können beim Erstellen eines DDoS-Schutzplans ein vorhandenes virtuelles Netzwerk zuordnen:

```azurepowershell-interactive
# Creates the DDoS protection plan
$ddosProtectionPlan = New-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan -Location "East US"

# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = New-Object Microsoft.Azure.Commands.Network.Models.PSResourceId

# Update the properties and enable DDoS protection
$vnet.DdosProtectionPlan.Id = $ddosProtectionPlan.Id
$vnet.EnableDdosProtection = $true
$vnet | Set-AzVirtualNetwork
``` 

## <a name="validate-and-test"></a>Überprüfen und Testen

Überprüfen Sie zunächst die Details Ihres DDoS-Schutzplans:

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName MyResourceGroup -Name MyDdosProtectionPlan
```

Stellen Sie sicher, dass der Befehl die korrekten Details Ihres DDoS-Schutzplans zurückgibt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können Ihre Ressourcen für das nächste Tutorial beibehalten. Löschen Sie die Ressourcengruppe _MyResourceGroup_ , wenn Sie sie nicht mehr benötigen. Wenn Sie die Ressourcengruppe löschen, werden auch der DDoS-Schutzplan und alle zugehörigen Ressourcen gelöscht. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

So deaktivieren Sie den DDoS-Schutz für ein virtuelles Netzwerk 

```azurepowershell-interactive
# Gets the most updated version of the virtual network
$vnet = Get-AzVirtualNetwork -Name MyVnet -ResourceGroupName MyResourceGroup
$vnet.DdosProtectionPlan = $null
$vnet.EnableDdosProtection = $false
$vnet | Set-AzVirtualNetwork
```

Wenn Sie einen DDoS-Schutzplan löschen möchten, müssen Sie zuerst die Zuordnung aller virtuellen Netzwerke für diesen Plan aufheben.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anzeigen und Konfigurieren von Telemetriedaten für Ihren DDoS-Schutzplan, finden Sie in den Tutorials.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren der DDoS Protection-Telemetrie](telemetry-monitoring-alerting.md)