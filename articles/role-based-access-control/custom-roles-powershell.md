---
title: Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen mithilfe von Azure PowerShell – Azure RBAC
description: Erfahren Sie, wie Sie benutzerdefinierte Rollen mithilfe von Azure-PowerShell und Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) auflisten, erstellen, aktualisieren oder löschen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 799475db567c88f067192d027589e9185ee1782b
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369173"
---
# <a name="create-or-update-azure-custom-roles-using-azure-powershell"></a>Erstellen oder Aktualisieren von benutzerdefinierten Azure-Rollen mithilfe von Azure PowerShell

> [!IMPORTANT]
> Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn die [integrierten Azure-Rollen](built-in-roles.md) die Anforderungen Ihrer Organisation nicht erfüllen, können Sie Ihre eigenen benutzerdefinierten Rollen erstellen. In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Rollen mithilfe von Azure PowerShell auflisten, erstellen, aktualisieren oder löschen.

Ein ausführliches Tutorial zum Erstellen einer benutzerdefinierten Rolle finden Sie unter [Tutorial: Erstellen einer benutzerdefinierten Azure-Rolle mithilfe von Azure PowerShell](tutorial-custom-role-powershell.md).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen von benutzerdefinierten Rollen benötigen Sie Folgendes:

- Berechtigungen zum Erstellen von benutzerdefinierten Rollen, etwa [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-custom-roles"></a>Auflisten benutzerdefinierter Rollen

Zum Auflisten der Rollen, die in einem Bereich für die Zuweisung verfügbar sind, wählen Sie den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Im folgenden Beispiel werden alle Rollen aufgelistet, die für die Zuweisung im ausgewählten Abonnement verfügbar sind.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom
```

```Example
Name                                              IsCustom
----                                              --------
Virtual Machine Operator                              True
AcrImageSigner                                       False
AcrQuarantineReader                                  False
AcrQuarantineWriter                                  False
API Management Service Contributor                   False
...
```

Im folgenden Beispiel werden nur die benutzerdefinierten Rollen aufgelistet, die für die Zuweisung im ausgewählten Abonnement verfügbar sind.

```azurepowershell
Get-AzRoleDefinition -Custom | FT Name, IsCustom
```

```Example
Name                     IsCustom
----                     --------
Virtual Machine Operator     True
```

Wenn sich das ausgewählte Abonnement nicht in der `AssignableScopes` der Rolle befindet, wird die benutzerdefinierte Rolle nicht aufgeführt.

## <a name="list-a-custom-role-definition"></a>Auflisten einer Definition einer benutzerdefinierten Rolle

Zum Auflisten der Definition einer benutzerdefinierten Rolle verwenden Sie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition). Dies ist derselbe Befehl, den Sie für eine integrierte Rolle verwenden würden.

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | ConvertTo-Json

{
  "Name": "Virtual Machine Operator",
  "Id": "00000000-0000-0000-0000-000000000000",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Im folgenden Beispiel werden die Aktionen der Rolle aufgelistet:

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Operator").Actions

"Microsoft.Storage/*/read",
"Microsoft.Network/*/read",
"Microsoft.Compute/*/read",
"Microsoft.Compute/virtualMachines/start/action",
"Microsoft.Compute/virtualMachines/restart/action",
"Microsoft.Authorization/*/read",
"Microsoft.ResourceHealth/availabilityStatuses/read",
"Microsoft.Resources/subscriptions/resourceGroups/read",
"Microsoft.Insights/alertRules/*",
"Microsoft.Insights/diagnosticSettings/*",
"Microsoft.Support/*"
```

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Um eine benutzerdefinierte Rolle zu erstellen, verwenden Sie den Befehl [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition). Es gibt zwei Methoden zum Strukturieren der Rolle: mithilfe eines `PSRoleDefinition`-Objekts oder einer JSON-Vorlage. 

### <a name="get-operations-for-a-resource-provider"></a>Abrufen von Vorgängen für Ressourcenanbieter

Wenn Sie benutzerdefinierte Rollen neu erstellen, ist es wichtig, alle möglichen Vorgänge der Ressourcenanbieter zu kennen.
Sie können die Liste der [Ressourcenanbietervorgänge](resource-provider-operations.md) ansehen oder den Befehl [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) ausführen, um diese Informationen zu erhalten.
Wenn Sie beispielsweise alle verfügbaren Vorgänge für virtuelle Computer überprüfen möchten, verwenden Sie folgenden Befehl:

```azurepowershell
Get-AzProviderOperation <operation> | FT OperationName, Operation, Description -AutoSize
```

```Example
PS C:\> Get-AzProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation, Description -AutoSize

OperationName                                  Operation                                                      Description
-------------                                  ---------                                                      -----------
Get Virtual Machine                            Microsoft.Compute/virtualMachines/read                         Get the propertie...
Create or Update Virtual Machine               Microsoft.Compute/virtualMachines/write                        Creates a new vir...
Delete Virtual Machine                         Microsoft.Compute/virtualMachines/delete                       Deletes the virtu...
Start Virtual Machine                          Microsoft.Compute/virtualMachines/start/action                 Starts the virtua...
...
```

### <a name="create-a-custom-role-with-the-psroledefinition-object"></a>Erstellen einer Rolle mit dem PSRoleDefinition-Objekt

Wenn Sie PowerShell verwenden, um eine benutzerdefinierte Rolle zu erstellen, können Sie eine der [integrierten Rollen](built-in-roles.md) als Ausgangspunkt verwenden oder von Grund auf beginnen. Das erste Beispiel in diesem Abschnitt beginnt mit einer integrierten Rolle. Diese wird dann mit weiteren Berechtigungen angepasst. Bearbeiten Sie die Attribute, und fügen Sie die gewünschten `Actions`, `NotActions` oder `AssignableScopes` hinzu. Speichern Sie die Änderungen anschließend als neue Rolle.

Das folgende Beispiel beginnt mit der integrierten Rolle [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor), um eine benutzerdefinierte Rolle namens *Virtual Machine Operator* zu erstellen. Die neue Rolle gewährt Zugriff auf alle Lesevorgänge der Ressourcenanbieter *Microsoft.Compute*, *Microsoft.Storage* und *Microsoft.Network* sowie zum Starten, Neustarten und Überwachen virtueller Computer. Die benutzerdefinierte Rolle kann in zwei Abonnements verwendet werden.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.ResourceHealth/availabilityStatuses/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/00000000-0000-0000-0000-000000000000")
$role.AssignableScopes.Add("/subscriptions/11111111-1111-1111-1111-111111111111")
New-AzRoleDefinition -Role $role
```

Das folgende Beispiel zeigt eine weitere Möglichkeit, um die benutzerdefinierte Rolle *Virtual Machine Operator* zu erstellen. Zunächst wird ein neues `PSRoleDefinition`-Objekt erstellt. Die Aktionsvorgänge werden in der `perms`-Variable angegeben, und auf die `Actions`-Eigenschaft festgelegt. Die `NotActions`-Eigenschaft wird festgelegt, indem die `NotActions` aus der integrierten Rolle [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) gelesen werden. Da [Virtual Machine Contributor](built-in-roles.md#virtual-machine-contributor) über keine `NotActions` verfügt, ist diese Zeile nicht erforderlich, aber sie zeigt, wie Informationen aus einer anderen Rolle abgerufen werden können.

```azurepowershell
$role = [Microsoft.Azure.Commands.Resources.Models.Authorization.PSRoleDefinition]::new()
$role.Name = 'Virtual Machine Operator 2'
$role.Description = 'Can monitor and restart virtual machines.'
$role.IsCustom = $true
$perms = 'Microsoft.Storage/*/read','Microsoft.Network/*/read','Microsoft.Compute/*/read'
$perms += 'Microsoft.Compute/virtualMachines/start/action','Microsoft.Compute/virtualMachines/restart/action'
$perms += 'Microsoft.Authorization/*/read'
$perms += 'Microsoft.ResourceHealth/availabilityStatuses/read'
$perms += 'Microsoft.Resources/subscriptions/resourceGroups/read'
$perms += 'Microsoft.Insights/alertRules/*','Microsoft.Support/*'
$role.Actions = $perms
$role.NotActions = (Get-AzRoleDefinition -Name 'Virtual Machine Contributor').NotActions
$subs = '/subscriptions/00000000-0000-0000-0000-000000000000','/subscriptions/11111111-1111-1111-1111-111111111111'
$role.AssignableScopes = $subs
New-AzRoleDefinition -Role $role
```

### <a name="create-a-custom-role-with-json-template"></a>Erstellen einer benutzerdefinierten Rolle mit einer JSON-Vorlage

Eine JSON-Vorlage kann als Quelldefinition für die benutzerdefinierte Rolle verwendet werden. Im folgenden Beispiel wird eine benutzerdefinierte Rolle erstellt, die Lesezugriff auf Speicher- und Computeressourcen und Zugriff auf den Support ermöglicht. Außerdem wird diese Rolle zwei Abonnements hinzugefügt. Erstellen Sie eine neue Datei `C:\CustomRoles\customrole1.json` anhand des folgenden Beispiels. Die ID muss bei der Ersterstellung der Rolle auf `null` festgelegt werden, da eine neue ID automatisch generiert wird. 

```json
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Um die Rolle den Abonnements hinzuzufügen, führen Sie den folgenden PowerShell-Befehl aus:

```azurepowershell
New-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="update-a-custom-role"></a>Aktualisieren einer benutzerdefinierten Rolle

Ähnlich wie beim Erstellen einer benutzerdefinierten Rolle können Sie eine vorhandene benutzerdefinierte Rolle mithilfe des `PSRoleDefinition`-Objekts oder einer JSON-Vorlage ändern.

### <a name="update-a-custom-role-with-the-psroledefinition-object"></a>Aktualisieren einer benutzerdefinierten Rolle mit dem PSRoleDefinition-Objekt

Zum Ändern einer benutzerdefinierten Rolle verwenden Sie zunächst den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition), um die Rollendefinition abzurufen. Nehmen Sie zweitens die gewünschten Änderungen an der Rollendefinitionsdatei vor. Verwenden Sie abschließend den Befehl [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition), um die geänderte Rollendefinition zu speichern.

Im folgenden Beispiel wird der Vorgang `Microsoft.Insights/diagnosticSettings/*` zur benutzerdefinierten Rolle *Virtual Machine Operator* hinzugefügt.

```azurepowershell
$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}
```

Im folgenden Beispiel wird ein Azure-Abonnement den zuweisbaren Bereichen der benutzerdefinierten Rolle *Virtual Machine Operator* hinzugefügt.

```azurepowershell
Get-AzSubscription -SubscriptionName Production3

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzSubscription -SubscriptionName Production3

Name     : Production3
Id       : 22222222-2222-2222-2222-222222222222
TenantId : 99999999-9999-9999-9999-999999999999
State    : Enabled

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/subscriptions/22222222-2222-2222-2222-222222222222")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222}
```

Im folgenden Beispiel wird `AssignableScopes` für die benutzerdefinierte Rolle *Virtual Machine Operator* eine Verwaltungsgruppe hinzugefügt. Das Hinzufügen einer Verwaltungsgruppe zu `AssignableScopes` befindet sich derzeit in der Vorschauphase.

```azurepowershell
Get-AzManagementGroup

$role = Get-AzRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/{groupId1}")
Set-AzRoleDefinition -Role $role
```

```Example
PS C:\> Get-AzManagementGroup

Id          : /providers/Microsoft.Management/managementGroups/marketing-group
Type        : /providers/Microsoft.Management/managementGroups
Name        : marketing-group
TenantId    : 99999999-9999-9999-9999-999999999999
DisplayName : Marketing group

PS C:\> $role = Get-AzRoleDefinition "Virtual Machine Operator"
PS C:\> $role.AssignableScopes.Add("/providers/Microsoft.Management/managementGroups/marketing-group")
PS C:\> Set-AzRoleDefinition -Role $role

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111,
                   /subscriptions/22222222-2222-2222-2222-222222222222,
                   /providers/Microsoft.Management/managementGroups/marketing-group}
```

### <a name="update-a-custom-role-with-a-json-template"></a>Aktualisieren einer benutzerdefinierten Rolle mit einer JSON-Vorlage

Mithilfe der vorherigen JSON-Vorlage können Sie eine vorhandene benutzerdefinierte Rolle über das Hinzufügen oder Entfernen von Aktionen mühelos ändern. Aktualisieren Sie die JSON-Vorlage, indem Sie „Microsoft.Network“ die Aktion „Read“ hinzufügen (siehe unten). Die Definitionen in der Vorlage werden nicht kumulativ auf eine vorhandene Definition angewendet. Dies bedeutet, dass die Rolle entsprechend Ihren Angaben in der Vorlage angezeigt wird. Außerdem müssen Sie das Feld „ID“ mit der ID der Rolle aktualisieren. Wenn Sie nicht sicher sind, wie dieser Wert lautet, können Sie diese Informationen mit dem Cmdlet [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) abrufen.

```json
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000",
    "/subscriptions/11111111-1111-1111-1111-111111111111"
  ]
}
```

Führen Sie zum Aktualisieren der vorhandenen Rolle den folgenden PowerShell-Befehl aus:

```azurepowershell
Set-AzRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Löschen einer benutzerdefinierten Rolle

Um eine benutzerdefinierte Rolle zu löschen, verwenden Sie den Befehl [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition).

Im folgenden Beispiel wird die benutzerdefinierte Rolle *Virtual Machine Operator* entfernt.

```azurepowershell
Get-AzRoleDefinition "Virtual Machine Operator"
Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition
```

```Example
PS C:\> Get-AzRoleDefinition "Virtual Machine Operator"

Name             : Virtual Machine Operator
Id               : 88888888-8888-8888-8888-888888888888
IsCustom         : True
Description      : Can monitor and restart virtual machines.
Actions          : {Microsoft.Storage/*/read, Microsoft.Network/*/read, Microsoft.Compute/*/read,
                   Microsoft.Compute/virtualMachines/start/action...}
NotActions       : {}
AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000,
                   /subscriptions/11111111-1111-1111-1111-111111111111}

PS C:\> Get-AzRoleDefinition "Virtual Machine Operator" | Remove-AzRoleDefinition

Confirm
Are you sure you want to remove role definition with name 'Virtual Machine Operator'.
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen einer benutzerdefinierten Azure-Rolle mithilfe von Azure PowerShell](tutorial-custom-role-powershell.md)
- [Benutzerdefinierte Azure-Rollen](custom-roles.md)
- [Vorgänge für Azure-Ressourcenanbieter](resource-provider-operations.md)
