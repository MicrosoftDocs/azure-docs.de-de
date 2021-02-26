---
title: 'Schnellstart: Erstellen einer freigegebenen Abfrage mit Azure PowerShell'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Erstellen einer freigegebenen Resource Graph-Abfrage mithilfe von Azure PowerShell aus.
ms.date: 01/11/2021
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b771253b1dea4bd1d2913bf7c48062112019a19
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981543"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-powershell"></a>Schnellstart: Erstellen einer freigegebenen Resource Graph-Abfrage mithilfe von Azure PowerShell

In diesem Artikel wird beschrieben, wie Sie mithilfe des PowerShell-Moduls [Az.ResourceGraph](/powershell/module/az.resourcegraph) eine freigegebene Azure Resource Graph-Abfrage erstellen können.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Solange nur eine Vorschauversion des PowerShell-Moduls **Az.ResourceGraph** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren.

  ```azurepowershell-interactive
  Install-Module -Name Az.ResourceGraph
  ```

- Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-graph-shared-query"></a>Erstellen einer freigegebenen Resource Graph-Abfrage

Nachdem das PowerShell-Modul **Az.ResourceGraph** der gewünschten Umgebung hinzugefügt wurde, können Sie eine freigegebene Resource Graph-Abfrage erstellen. Bei der freigegebenen Abfrage handelt es sich um ein Azure Resource Manager Objekt, dem Sie Berechtigung für den Azure Resource Graph-Explorer erteilen oder das Sie im Azure Resource Graph-Explorer ausführen können. Die Abfrage fasst die Anzahl aller Ressourcen zusammen, gruppiert nach _Standort_.

1. Erstellen Sie eine Ressourcengruppe mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup), um die freigegebene Azure Resource Graph-Abfrage zu speichern. Diese Ressourcengruppe hat den Namen `resource-graph-queries`, und der Standort ist `westus2`.

   ```azurepowershell-interactive
   # Login first with `Connect-AzAccount` if not using Cloud Shell

   # Create the resource group
   New-AzResourceGroup -Name resource-graph-queries -Location westus2
   ```

1. Erstellen Sie die freigegebene Azure Resource Graph-Abfrage mithilfe des PowerShell-Moduls **Az.ResourceGraph** und des Cmdlets [New-AzResourceGraphQuery](/powershell/module/az.resourcegraph/new-azresourcegraphquery):

   ```azurepowershell-interactive
   # Create the Azure Resource Graph shared query
   $Params = @{
     Name = 'Summarize resources by location'
     ResourceGroupName = 'resource-graph-queries'
     Location = 'westus2'
     Description = 'This shared query summarizes resources by location for a pinnable map graphic.'
     Query = 'Resources | summarize count() by location'
   }
   New-AzResourceGraphQuery @Params
   ```

1. Listet die freigegebenen Abfragen in der neuen Ressourcengruppe auf. Mit dem Cmdlet [Get-AzResourceGraphQuery](/powershell/module/az.resourcegraph/get-azresourcegraphquery) wird ein Array von Werten zurückgegeben.

   ```azurepowershell-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries
   ```

1. Um nur eine einzelne freigegebene Abfrage als Ergebnis zu erhalten, verwenden Sie `Get-AzResourceGraphQuery` mit dem Parameter `Name`.

   ```azurepowershell-interactive
   # Show a specific Azure Resource Graph shared query
   Get-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'
   ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die freigegebene Resource Graph-Abfrage und die Resource Graph-Ressourcengruppe aus der Azure-Umgebung entfernen möchten, verwenden Sie hierfür folgende Befehle:

- [Remove-AzResourceGraphQuery](/powershell/module/az.resourcegraph/remove-azresourcegraphquery)
- [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
# Delete the Azure Resource Graph shared query
Remove-AzResourceGraphQuery -ResourceGroupName resource-graph-queries -Name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group
Remove-AzResourceGroup -Name resource-graph-queries
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie mithilfe von Azure PowerShell eine freigegebene Resource Graph-Abfrage erstellt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).