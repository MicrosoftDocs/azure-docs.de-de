---
title: Includedatei
description: Includedatei
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 069baf627c0230b6a4727c375494352ab3e6a803
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66164613"
---
Verwenden Sie zum Hinzufügen von zwei Tags zu einer Ressourcengruppe den Befehl [Set-AzResourceGroup](/powershell/module/az.resources/set-azresourcegroup):

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ Dept="IT"; Environment="Test" }
```

Angenommen, Sie möchten ein drittes Tag hinzufügen. Wenn Sie Tags auf eine Ressource oder Ressourcengruppe anwenden, werden die bereits vorhandenen Tags für diese Ressource oder Ressourcengruppe überschrieben. Um ein neues Tag hinzufügen, ohne die vorhandenen Tags zu verlieren, müssen Sie die vorhandenen Tags abrufen, ein neues Tag hinzufügen und dann die Tagsammlung erneut anwenden:

```azurepowershell-interactive
# Get existing tags and add a new tag
$tags = (Get-AzResourceGroup -Name myResourceGroup).Tags
$tags.Add("Project", "Documentation")

# Reapply the updated set of tags 
Set-AzResourceGroup -Tag $tags -Name myResourceGroup
```

Ressourcen erben keine Tags aus der Ressourcengruppe. Derzeit enthält Ihre Ressourcengruppe drei Tags, die Ressourcen besitzen jedoch keine Tags. Verwenden Sie das folgende Skript, um alle Tags einer Ressourcengruppe auf die darin enthaltenen Ressourcen anzuwenden und vorhandene Tags für Ressourcen beizubehalten, die keine Duplikate sind:

```azurepowershell-interactive
# Get the resource group
$group = Get-AzResourceGroup myResourceGroup

if ($group.Tags -ne $null) {
    # Get the resources in the resource group
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName

    # Loop through each resource
    foreach ($r in $resources)
    {
        # Get the tags for this resource
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        
        # If the resource has existing tags, add new ones
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }

            # Reapply the updated tags to the resource 
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Alternativ können Sie Tags aus der Ressourcengruppe auf die Ressourcen anwenden, ohne die vorhandenen Tags beizubehalten:

```azurepowershell-interactive
# Get the resource group
$g = Get-AzResourceGroup -Name myResourceGroup

# Find all the resources in the resource group, and for each resource apply the tags from the resource group
Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
```

Kombinieren Sie mehrere Werte in einem einzelnen Tag mithilfe einer JSON-Zeichenfolge.

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ CostCenter="{`"Dept`":`"IT`",`"Environment`":`"Test`"}" }
```

Um ein neues Tag mit verschiedenen Werten hinzufügen, ohne die vorhandenen Tags zu verlieren, verwenden Sie eine JSON-Zeichenfolge für das neue Tag und wenden Sie dann die Tagsammlung erneut an:

```azurepowershell-interactive
# Get existing tags and add a new tag
$ResourceGroup = Get-AzResourceGroup -Name myResourceGroup
$Tags = $ResourceGroup.Tags
$Tags.Add("CostCenter", "{`"Dept`":`"IT`",`"Environment`":`"Test`"}")

# Reapply the updated set of tags
$ResourceGroup | Set-AzResourceGroup -Tag $Tags
```

Durch Übergeben einer leeren Hashtabelle können Sie alle Tags löschen.

```azurepowershell-interactive
Set-AzResourceGroup -Name myResourceGroup -Tag @{ }
```
