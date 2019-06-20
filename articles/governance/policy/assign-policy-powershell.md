---
title: Erstellen einer Richtlinie für nicht konforme Ressourcen mit Azure PowerShell
description: Erstellen Sie mithilfe von Azure PowerShell eine Azure Policy-Zuweisung zum Identifizieren nicht konformer Ressourcen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/11/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 2ff34911dea19b83731b46077bd60c7b085763a4
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979583"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen mit Azure PowerShell

Zum Verständnis der Konformität in Azure müssen Sie zunächst wissen, wie Sie den Status Ihrer Ressourcen ermitteln. Im Rahmen dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung zur Identifizierung von virtuellen Computern, die keine verwalteten Datenträger verwenden. Im Anschluss identifizieren Sie virtuelle Computer, die *nicht konform* sind.

Das Azure PowerShell-Modul dient zum Verwalten von Azure-Ressourcen über die Befehlszeile oder in Skripts.
In dieser Anleitung erfahren Sie, wie Sie mithilfe des Az-Moduls eine Richtlinienzuordnung erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie vor Beginn sicher, dass die aktuelle Version von Azure PowerShell installiert ist. Ausführliche Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).
- Registrieren Sie den Ressourcenanbieter Azure Policy Insights mithilfe von Azure PowerShell. Durch die Registrierung des Ressourcenanbieters wird sichergestellt, dass das Abonnement mit ihm verwendet werden kann. Um einen Ressourcenanbieter zu registrieren, benötigen Sie die Berechtigung zum Registrieren von Ressourcenanbietern. Dieser Vorgang ist in den Rollen „Mitwirkender“ und „Besitzer“ enthalten. Führen Sie den folgenden Befehl aus, um den Ressourcenanbieter zu registrieren:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Weitere Informationen zum Registrieren und Anzeigen von Ressourcenanbietern finden Sie unter [Ressourcenanbieter und -typen](../../azure-resource-manager/resource-manager-supported-services.md).

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

In dieser Schnellstartanleitung erstellen Sie eine Richtlinienzuweisung für die Definition *Virtuelle Computer ohne verwaltete Datenträger überwachen*. Diese Richtliniendefinition identifiziert virtuelle Computer, die keine verwalteten Datenträger verwenden.

Führen Sie die folgenden Befehle aus, um eine neue Richtlinienzuweisung zu erstellen:

```azurepowershell-interactive
# Get a reference to the resource group that will be the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition that will be assigned
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

In diesen Befehlen werden folgende Informationen verwendet:

- **Name:** Der tatsächliche Name der Zuweisung. In diesem Beispiel wurde *audit-vm-manageddisks* verwendet.
- **DisplayName:** Der Anzeigename für die Richtlinienzuweisung. Verwenden Sie in diesem Fall *Zuweisung für die Überwachung virtueller Computer ohne verwaltete Datenträger*.
- **Definition:** Die Richtliniendefinition, auf deren Grundlage Sie die Zuweisung erstellen. In diesem Fall ist es die ID der Richtliniendefinition *Virtuelle Computer überwachen, die keine verwalteten Datenträger verwenden*.
- **Bereich:** Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Er kann von einem Abonnement bis zu Ressourcengruppen reichen. Ersetzen Sie &lt;scope&gt; durch den Namen Ihrer Ressourcengruppe.

Sie können nun nicht konforme Ressourcen identifizieren, um den Konformitätszustand Ihrer Umgebung nachzuvollziehen.

## <a name="identify-non-compliant-resources"></a>Identifizieren nicht konformer Ressourcen

Verwenden Sie folgende Informationen, um Ressourcen zu identifizieren, die mit der erstellten Richtlinienzuweisung nicht konform sind. Führen Sie die folgenden Befehle aus:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Weitere Informationen zum Abrufen des Richtlinienstatus finden Sie unter [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Ihre Ergebnisse sollten in etwa wie im folgenden Beispiel aussehen:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Die Ergebnisse entsprechen dem, was im Azure-Portal auf der Registerkarte **Ressourcenkonformität** einer Richtlinienzuweisung angezeigt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie den folgenden Befehl aus, um die Richtlinienzuweisung zu entfernen:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Richtliniendefinition zum Identifizieren nicht kompatibler Ressourcen in Ihrer Azure-Umgebung zugewiesen.

Weitere Informationen zum Zuweisen von Richtlinien, die die Konformität neuer Ressourcen überprüfen, finden Sie im folgenden Tutorial:

> [!div class="nextstepaction"]
> [Erstellen und Verwalten von Richtlinien](./tutorials/create-and-manage.md)