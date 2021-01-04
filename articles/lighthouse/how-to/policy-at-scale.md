---
title: Bereitstellen von Azure Policy für delegierte Abonnements in großem Umfang
description: Hier erfahren Sie, wie Sie mit Azure Lighthouse eine Richtliniendefinition und eine Richtlinienzuweisung für mehrere Mandanten bereitstellen können.
ms.date: 11/09/2020
ms.topic: how-to
ms.openlocfilehash: 5af938c61ad3e42e36360a15c6011b54fa1e823d
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94412067"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Bereitstellen von Azure Policy für delegierte Abonnements in großem Umfang

Als Dienstanbieter haben Sie möglicherweise mehrere Kundenmandanten in [Azure Lighthouse](../overview.md) integriert. Azure Lighthouse ermöglicht Dienstanbietern das gleichzeitige Ausführen von skalierbaren Vorgängen für mehrere Mandanten, wodurch Verwaltungsaufgaben effizienter werden.

In diesem Thema erfahren Sie, wie Sie mithilfe von [Azure Policy](../../governance/policy/index.yml) eine Richtliniendefinition und eine Richtlinienzuweisung für mehrere Mandanten mithilfe von PowerShell-Befehlen bereitstellen. In diesem Beispiel stellt die Richtliniendefinition sicher, dass Speicherkonten geschützt werden, indem nur HTTPS-Datenverkehr zugelassen wird.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), denselben Prozess verwenden.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Verwenden von Azure Resource Graph zum Abfragen von Kundenmandanten

Sie können [Azure Resource Graph](../../governance/resource-graph/index.yml) verwenden, um Abfragen bei allen Abonnements in den von Ihnen verwalteten Kundenmandanten auszuführen. In diesem Beispiel werden alle Speicherkonten in diesen Abonnements identifiziert, für die derzeit kein HTTPS-Datenverkehr erforderlich ist.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Bereitstellen einer Richtlinie für mehrere Kundenmandanten

Im folgenden Beispiel wird veranschaulicht, wie Sie eine [Azure Resource Manager-Vorlage](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) verwenden, um eine Richtliniendefinition und eine Richtlinienzuweisung für delegierte Abonnements in mehreren Kundenmandanten bereitzustellen. Diese Richtliniendefinition erfordert, dass alle Speicherkonten HTTPS-Datenverkehr verwenden. Dadurch wird verhindert, dass neue nicht kompatible Speicherkonten erstellt werden, und vorhandene Speicherkonten ohne die Einstellung werden als nicht kompatibel gekennzeichnet.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzSubscriptionDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Überprüfen der Richtlinienbereitstellung

Nachdem Sie die Azure Resource Manager-Vorlage bereitgestellt haben, können Sie überprüfen, ob die Richtliniendefinition erfolgreich angewendet wurde, indem Sie versuchen, ein Speicherkonto zu erstellen, wobei **EnableHttpsTrafficOnly** in einem ihrer Delegiertenabonnements auf **false** festgelegt ist. Aufgrund der Richtlinienzuweisung sollte es nicht möglich sein, dieses Speicherkonto zu erstellen.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie fertig sind, entfernen Sie die Richtliniendefinition und die von der Bereitstellung erstellte Zuweisung.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzSubscriptionDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

> [!NOTE]
> Sie können zwar Richtlinien mandantenübergreifend bereitstellen, aber für nicht konforme Ressourcen in diesen Mandanten keine [Konformitätsdetails anzeigen](../../governance/policy/how-to/determine-non-compliance.md#compliance-details).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Azure Policy](../../governance/policy/index.yml).
- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md).
- Erfahren Sie, wie Sie eine [Richtlinie bereitstellen, die in einem delegierten Abonnement gewartet werden kann](deploy-policy-remediation.md).
