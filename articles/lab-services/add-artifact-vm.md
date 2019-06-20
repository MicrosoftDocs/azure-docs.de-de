---
title: Hinzufügen eines Artefakts zu einer VM in Azure DevTest Labs | Microsoft-Dokumentation
description: Informationen zum Hinzufügen eines Artefakts zu einem virtuellen Computer in einem Lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: 19a7d6052091f8889a88c61793186b7bf7d9d869
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60304271"
---
# <a name="add-an-artifact-to-a-vm"></a>Hinzufügen eines Artefakts zu einer VM
Beim Erstellen eines virtuellen Computers können Sie diesem vorhandene Artefakte hinzufügen. Diese Artefakte können entweder aus dem [öffentlichen Git-Repository für DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) oder Ihrem eigenen Git-Repository stammen. In diesem Artikel erfahren Sie, wie Sie im Azure-Portal Artefakte hinzufügen sowie mithilfe von Azure PowerShell. 

Azure DevTest Labs-*Artefakte* ermöglichen Ihnen die Angabe von *Aktionen*, die ausgeführt werden, wenn der virtuelle Computer bereitgestellt wird, beispielsweise Ausführen von Windows PowerShell-Skripts, Ausführen von Bash-Befehlen und Installieren von Software. Mit *Parametern* für Artefakte können Sie das Artefakt für ein bestimmtes Szenario anpassen.

Informationen zum Erstellen benutzerdefinierter Artefakte finden Sie im Artikel: [Erstellen benutzerdefinierter Artefakte](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Verwenden des Azure-Portals 
1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste der Labs das Lab mit dem virtuellen Computer aus, mit dem Sie arbeiten möchten.  
1. Wählen Sie **Meine virtuellen Computer** aus.
1. Wählen Sie den gewünschten virtuellen Computer aus.
1. Wählen Sie **Artefakte verwalten** aus. 
1. Wählen Sie **Artefakte anwenden**.
1. Wählen Sie im Bereich **Artefakte anwenden** das Artefakt aus, das Sie dem virtuellen Computer hinzufügen möchten.
1. Geben Sie im Bereich **Artefakt hinzufügen** die erforderlichen Parameterwerte und alle optionalen Parameter ein, die Sie benötigen.  
1. Wählen Sie **Hinzufügen** aus, um das Artefakt hinzuzufügen, und kehren Sie zum Bereich **Artefakte anwenden** zurück.
1. Fügen Sie weiterhin nach Bedarf für Ihren virtuellen Computer Artefakte hinzu.
1. Sobald Sie Ihre Artefakte hinzugefügt haben, können Sie [die Reihenfolge ändern, in der die Artefakte ausgeführt werden](#change-the-order-in-which-artifacts-are-run). Sie können auch zum [Anzeigen oder Ändern eines Artefakts](#view-or-modify-an-artifact)zurückgehen.
1. Wenn Sie mit dem Hinzufügen von Artefakten fertig sind, wählen Sie **Anwenden**.

### <a name="change-the-order-in-which-artifacts-are-run"></a>Ändern der Reihenfolge, in der Artefakte ausgeführt werden
Standardmäßig werden die Aktionen der Artefakte in der Reihenfolge ausgeführt, in der sie der VM hinzugefügt wurden. Die folgenden Schritte veranschaulichen, wie Sie die Reihenfolge ändern, in der die Artefakte ausgeführt werden.

1. Wählen Sie oben im Bereich **Artefakte anwenden** den Link, der die Anzahl der Artefakte angibt, die dem virtuellen Computer hinzugefügt wurden.
   
    ![Anzahl der Artefakte, die dem virtuellen Computer hinzugefügt wurden](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Ziehen Sie die Artefakte im Bereich **Ausgewählte Artefakte** per Drag & Drop in die gewünschte Reihenfolge. Wenn beim Ziehen des Artefakts Probleme auftreten, stellen Sie sicher, dass Sie von der linken Seite des Artefakts aus ziehen. 
1. Wählen Sie nach Abschluss des Vorgangs **OK** .  

### <a name="view-or-modify-an-artifact"></a>Anzeigen oder Ändern eines Artefakts
Die folgenden Schritte veranschaulichen das Anzeigen oder Ändern der Parameter eines Artefakts:

1. Wählen Sie oben im Bereich **Artefakte anwenden** den Link, der die Anzahl der Artefakte angibt, die dem virtuellen Computer hinzugefügt wurden.
   
    ![Anzahl der Artefakte, die dem virtuellen Computer hinzugefügt wurden](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. Wählen Sie im Bereich **Ausgewählte Artefakte** das Artefakt aus, das Sie anzeigen oder bearbeiten möchten.  
1. Nehmen Sie im Bereich **Artefakt hinzufügen** die erforderlichen Änderungen vor, und wählen Sie **OK**, um den Bereich **Artefakt hinzufügen** zu schließen.
1. Wählen Sie **OK**, um den Bereich **Ausgewählte Artefakte** zu schließen.

## <a name="use-powershell"></a>Verwenden von PowerShell
Das folgende Skript wendet das angegebene Artefakt auf den angegebenen virtuellen Computer an. Der Vorgang wird von dem Befehl [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) ausgeführt.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln zu Artefakten:

- [Angeben verbindlicher Artefakte für Ihr Lab](devtest-lab-mandatory-artifacts.md)
- [Erstellen benutzerdefinierter Artefakte](devtest-lab-artifact-author.md)
- [Hinzufügen eines Artefaktrepositorys zu einem Lab](devtest-lab-artifact-author.md)
- [Diagnostizieren von Artefaktfehlern](devtest-lab-troubleshoot-artifact-failure.md)
