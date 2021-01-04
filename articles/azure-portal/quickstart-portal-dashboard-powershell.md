---
title: Erstellen eines Dashboards im Azure-Portal mit PowerShell
description: Hier erfahren Sie, wie Sie mithilfe von Azure PowerShell ein Dashboard im Azure-Portal erstellen.
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 07/24/2020
ms.openlocfilehash: 02e243a7296555d73427f8e31c4abdf9c3e56735
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745739"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Schnellstart: Erstellen eines Dashboards im Azure-Portal mit PowerShell

Ein Dashboard im Azure-Portal bietet eine fokussierte und organisierte Ansicht Ihrer Cloudressourcen. Dieser Artikel konzentriert sich auf die Verwendung des PowerShell-Moduls „Az.Portal“ zum Erstellen eines Dashboards.
Das Dashboard zeigt die Leistung eines virtuellen Computers (VM) sowie einige statische Informationen und Verknüpfungen an.

## <a name="requirements"></a>Requirements (Anforderungen)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Falls Sie PowerShell lokal verwenden möchten, müssen Sie für diesen Artikel das Az PowerShell-Modul installieren und mit dem Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) eine Verbindung mit Ihrem Azure-Konto herstellen. Weitere Informationen zum Installieren des Az PowerShell-Moduls finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.Portal** verfügbar ist, müssen Sie es separat über das Az PowerShell-Modul installieren. Verwenden Sie dazu das Cmdlet `Install-Module`. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Auswählen eines bestimmten Azure-Abonnements

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definieren von Variablen

Sie verwenden mehrere Informationselemente wiederholt. Erstellen Sie Variablen zum Speichern der Informationen.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe basierend auf dem Namen in der Variablen `$resourceGroupName` in der Region erstellt, die in der Variablen `$location` angegeben ist.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Das Dashboard, das Sie im nächsten Teil dieses Schnellstarts erstellen, erfordert eine vorhandene VM. Führen Sie diese Schritte durch, um eine VM zu erstellen.

Speichern Sie die Anmeldeinformationen für die VM in einer Variablen. Das Kennwort muss komplex sein. Dabei handelt es sich um einen neuen Benutzernamen und ein neues Kennwort, also beispielsweise nicht das Konto, mit dem Sie sich bei Azure anmelden. Weitere Informationen finden Sie unter [Anforderungen an Benutzernamen](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) und [Anforderungen an Kennwörter](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Erstellen Sie den virtuellen Computer.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

Die Bereitstellung des virtuellen Computers wird jetzt gestartet und dauert in der Regel ein paar Minuten. Fahren Sie nach Abschluss der Bereitstellung mit dem nächsten Abschnitt fort.

## <a name="download-the-dashboard-template"></a>Herunterladen der Dashboardvorlage

Da Azure-Dashboards Ressourcen sind, können diese Dashboards als JSON-Code dargestellt werden. Mit dem folgenden Code wird eine JSON-Darstellung eines Beispieldashboards heruntergeladen. Weitere Informationen finden Sie unter [Struktur von Azure-Dashboards](./azure-portal-dashboards-structure.md).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Anpassen der Vorlage

Passen Sie die heruntergeladene Vorlage an, indem Sie den folgenden Code ausführen:

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Weitere Informationen finden Sie unter [Vorlagenreferenz zu Microsoft.Portal-Dashboards](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Bereitstellen der Dashboardvorlage

Sie können das Cmdlet `New-AzPortalDashboard` verwenden, das Teil des Az.Portal-Moduls ist, um die Vorlage direkt über PowerShell bereitzustellen.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Überprüfen Sie, ob das Dashboard erfolgreich erstellt wurde.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Vergewissern Sie sich, dass Sie Daten zum virtuellen Computer im Azure-Portal anzeigen können.

1. Wählen Sie im Azure-Portal **Dashboard** aus.

   ![Azure-Portal-Navigation zum Dashboard](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Wählen Sie auf der Seite „Dashboard“ **Einfaches VM-Dashboard** aus.

   ![Navigieren zu einfachem VM-Dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Überprüfen Sie das Dashboard. Sie können sehen, dass ein Teil des Inhalts statisch ist, aber es gibt auch Diagramme, die die Leistung des virtuellen Computers anzeigen.

   ![Überprüfen des einfachen VM-Dashboards](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den virtuellen Computer und das zugehörige Dashboard entfernen möchten, löschen Sie die Ressourcengruppe, in der diese enthalten sind.

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den im PowerShell-Modul „Az.Portal“ enthaltenen Cmdlets finden Sie in folgendem Artikel:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: Cmdlets des Portaldashboards](/powershell/module/Az.Portal/)