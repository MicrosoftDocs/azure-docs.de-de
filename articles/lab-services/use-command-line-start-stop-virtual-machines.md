---
title: Starten und Beenden von VMs in Azure DevTest Labs mit Befehlszeilentools | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Befehlszeilentools verwenden, um virtuelle Computer in Azure DevTest Labs zu starten und zu beenden.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: a8132735d1af08055e9341608dcac0564ed4b927
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236679"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Starten und Beenden von virtuellen Computern in Azure DevTest Labs mit Befehlszeilentools
In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure PowerShell oder der Azure CLI virtuelle Computer in einem Lab in Azure DevTest Labs starten bzw. beenden. Zur Automatisierung dieser Vorgänge können Sie PowerShell- bzw. CLI-Skripts erstellen. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
Mit Azure DevTest Labs erhalten Sie schnelle, einfache und schlanke Entwicklungs-/Testumgebungen. Sie können damit Kosten verwalten, schnell VMs bereitstellen und Ausschuss minimieren.  Im Azure-Portal gibt es integrierte Funktionen, mit denen Sie VMs in einem Lab so konfigurieren können, dass diese automatisch zu bestimmten Zeiten gestartet oder beendet werden. 

In einigen Szenarios sollten Sie das Starten und Beenden von VMs jedoch ggf. über PowerShell- bzw. CLI-Skripts automatisieren. Diese Methode bietet Ihnen mehr Flexibilität, wenn Sie einzelne virtuelle Computer jederzeit starten und beenden möchten und nicht nur zu bestimmten Zeitpunkten. Folgende Beispiele beschreiben Situationen, in denen das Ausführen dieser Tasks mithilfe von Skripts nützlich wäre.

- Bei der Verwendung dreistufiger Anwendungen im Rahmen einer Testumgebung müssen die Stufen in einer bestimmten Reihenfolge gestartet werden. 
- Eine VM soll beendet werden, sobald ein benutzerdefiniertes Kriterium erfüllt ist, um so Geld zu sparen. 
- Ein Task soll innerhalb eines CI/CD-Workflows ausgeführt werden, um die VMs am Anfang des Workflows zu starten, die VMs als Build-Computer, Testcomputer oder Infrastruktur zu verwenden und dann die VMs nach Abschluss des Prozesses zu beenden. Ein Beispiel hierfür wäre die benutzerdefinierte Image Factory mit Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell
Das folgende PowerShell-Skript startet eine VM in einem Lab. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) ist der Schwerpunkt dieses Skripts. Der **ResourceId**-Parameter ist die vollqualifizierte Ressourcen-ID für die VM in dem Lab. Im **Action**-Parameter werden je nach Bedarf die Optionen **Start** bzw. **Stop** festgelegt.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
if ($(Get-Module -Name AzureRM).Version.Major -eq 6) {
    $devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -Name $devTestLabName
} else {
    $devTestLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $devTestLabName
}

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Die [Azure CLI](/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) ist eine weitere Möglichkeit, das Starten und Beenden von VMs in DevTest Labs zu automatisieren. Die Azure CLI kann unter verschiedenen Betriebssystemen [installiert](/cli/azure/install-azure-cli?view=azure-cli-latest) werden. Im folgenden Skript finden Sie Befehle zum Starten und Beenden einer VM in einem Lab. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Nächste Schritte
Im folgenden Artikel erfahren Sie, wie Sie diese Vorgänge über das Azure-Portal ausführen: [Neustarten eines virtuellen Computers](devtest-lab-restart-vm.md)
