---
title: Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell
description: Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: workloads
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.reviewer: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28b26b8d4d6acdffb4083d182c6a0ce0539080c9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957366"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Schnellstart: Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell

In diesem Artikel wird erläutert, wie Sie Ressourcen für Azure Monitor für SAP-Lösungen mithilfe des PowerShell-Moduls [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) erstellen.

> [!CAUTION]
> Azure Monitor für SAP-Lösungen befindet sich derzeit in der Public Preview. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Für Produktionsworkloads wird sie nicht empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Requirements (Anforderungen)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps). Wenn Sie sich für die Verwendung von Cloud Shell entscheiden, finden Sie unter [Übersicht über Azure Cloud Shell](../../../cloud-shell/overview.md) weitere Informationen.

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.HanaOnAzure** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../../../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem angegebenen Namen am angegebenen Standort erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-Monitor

Verwenden Sie das Cmdlet [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor), um einen SAP-Monitor zu erstellen. Im folgenden Beispiel wird ein SAP-Monitor für das angegebene Abonnement, die Ressourcengruppe und den Ressourcennamen erstellt.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Verwenden Sie das Cmdlet [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor), um die Eigenschaften eines SAP-Monitors abzurufen. Im folgenden Beispiel werden die Eigenschaften eines SAP-Monitors für das angegebene Abonnement, die Ressourcengruppe und den Ressourcennamen abgerufen.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Anbieterinstanz

Verwenden Sie das Cmdlet [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance), um eine Anbieterinstanz zu erstellen. Im folgenden Beispiel wird eine Anbieterinstanz für das angegebene Abonnement, die Ressourcengruppe und den Ressourcennamen erstellt.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Verwenden Sie das Cmdlet [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance), um die Eigenschaften einer Anbieterinstanz abzurufen. Im folgenden Beispiel werden die Eigenschaften einer Anbieterinstanz für das angegebene Abonnement, die Ressourcengruppe, den SapMonitor-Namen und den Ressourcennamen abgerufen.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die in diesem Artikel erstellten Ressourcen nicht benötigt werden, können Sie sie löschen, indem Sie die folgenden Beispiele ausführen.

### <a name="delete-the-provider-instance"></a>Löschen der Anbieterinstanz

Verwenden Sie das Cmdlet [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance), um eine Anbieterinstanz zu entfernen. Im folgenden Beispiel wird eine Anbieterinstanz für das angegebene Abonnement, die Ressourcengruppe, den SapMonitor-Namen und den Ressourcennamen gelöscht.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Löschen des SAP-Monitors

Verwenden Sie das Cmdlet [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor), um einen SAP-Monitor zu entfernen. Im folgenden Beispiel wird ein SAP-Monitor für das angegebene Abonnement, die Ressourcengruppe und den Monitornamen gelöscht.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Azure Monitor für SAP-Lösungen](azure-monitor-overview.md)