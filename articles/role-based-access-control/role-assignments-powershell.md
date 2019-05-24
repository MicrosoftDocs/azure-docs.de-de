---
title: Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und Azure PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen und Anwendungen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) und Azure PowerShell verwalten. Dazu gehören das Auflisten, Erteilen und Entfernen des Zugriffs.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 002ebcbe8ba14b9f15ddea6deb21f0f2bc201ab0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66160320"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und Azure PowerShell

Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung (RBAC)](overview.md) verwaltet. In diesem Artikel wird beschrieben, wie Sie den Zugriff für Benutzer, Gruppen und Anwendungen mit der RBAC und Azure PowerShell verwalten.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für die Zugriffsverwaltung benötigen Sie Folgendes:

* [PowerShell in Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-roles"></a>Auflisten der Rollen

### <a name="list-all-available-roles"></a>Auflisten aller verfügbaren Rollen

Zum Auflisten von verfügbaren RBAC-Rollen für die Zuweisung und zum Überprüfen der Vorgänge, auf die sie Zugriff gewähren, verwenden Sie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-specific-role"></a>Auflisten einer bestimmten Rolle

Verwenden Sie zum Auflisten einer bestimmten Rolle [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

## <a name="list-a-role-definition"></a>Auflisten einer Rollendefinition

### <a name="list-a-role-definition-in-json-format"></a>Auflisten einer Rollendefinition im JSON-Format

Verwenden Sie zum Auflisten einer Rollendefinition im JSON-Format [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-actions-of-a-role"></a>Auflisten der Aktionen einer Rolle

Verwenden Sie zum Auflisten der Aktionen für eine bestimmte Rolle [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="list-access"></a>Auflisten des Zugriffs

Zum Auflisten des Zugriffs in RBAC führen Sie die Rollenzuweisungen auf.

### <a name="list-role-assignments-at-a-specific-scope"></a>Auflistung der Rollenzuweisungen für einen bestimmten Bereich

Sie können alle Rollenzuweisungen für ein angegebenes Abonnement, eine angegebene Ressourcengruppe oder eine Ressource sehen. Um beispielsweise alle aktiven Zuweisungen für eine Ressourcengruppe zu sehen, verwenden Sie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

### <a name="list-role-assignments-for-a-user"></a>Liste von Rollenzuweisungen für einen Benutzer

Verwenden Sie zum Auflisten aller Rollen, die einem bestimmten Benutzer zugewiesen sind, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

Um alle Rollen, die einem bestimmten Benutzer zugewiesen sind, und die Rollen aufzulisten, die den Gruppen des Benutzers zugewiesen sind, verwenden Sie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

### <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Auflisten der Rollenzuweisungen für klassische Abonnementadministratoren und -Co-Administratoren

Zum Auflisten der Rollenzuweisungen für die klassischen Abonnementadministratoren und -Co-Administratoren verwenden Sie [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment).

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="grant-access"></a>Gewähren von Zugriff

In RBAC erstellen Sie zum Gewähren des Zugriffs eine Rollenzuweisung.

### <a name="search-for-object-ids"></a>Suchen nach Objekt-IDs

Um eine Rolle zuzuweisen, müssen Sie sowohl das Objekt (Benutzer, Gruppe oder Anwendung) als auch den Bereich identifizieren.

Falls Sie die Abonnement-ID nicht kennen, können Sie diese im Blatt **Abonnements** im Azure-Portal finden, oder Sie verwenden [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

Verwenden Sie zum Abrufen der Objekt-ID für einen Azure AD-Benutzer [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
```

Verwenden Sie zum Abrufen der Objekt-ID für eine Azure AD-Gruppe [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
```

Die Objekt-ID für einen Azure AD-Dienstprinzipal oder eine Azure AD-Anwendung können Sie mit [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) abrufen.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
```

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Erstellen einer Rollenzuweisung für einen Benutzer in einem Ressourcengruppenbereich

Um einem Benutzer Zugriff im Ressourcengruppenkontext zu gewähren, verwenden Sie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name_in_quotes> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Erstellen einer Rollenzuweisung anhand der eindeutigen Rollen-ID

In bestimmten Fällen kann sich ein Rollenname ändern, z. B.:

- Sie verwenden eine eigene benutzerdefinierte Rolle und beschließen, den Namen zu ändern.
- Sie verwenden eine als Vorschauversion verfügbare Rolle, deren Name den Zusatz **(Vorschauversion)** enthält. Beim Veröffentlichen wird die Rolle umbenannt.

> [!IMPORTANT]
> Eine Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Auch wenn eine Rolle umbenannt wird, ändert sich die Rollen-ID nicht. Wenn Sie Ihre Rollenzuweisungen mithilfe von Skripts oder mittels Automatisierung erstellen, wird empfohlen, die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, ist so die Wahrscheinlichkeit größer, dass Ihre Skripts funktionieren.

Verwenden Sie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um eine Rollenzuweisung anstelle des Rollennamens mit der eindeutigen Rollen-ID zu erstellen.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

Im folgenden Beispiel wird dem Benutzer *alain@example.com* im Ressourcengruppenbereich *pharma-sales* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) zugewiesen. Zum Abrufen der eindeutigen Rollen-ID können Sie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) verwenden. Weitere Informationen finden Sie unter [Integrierte Rollen für Azure-Ressourcen](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-a-group-at-a-resource-scope"></a>Erstellen einer Rollenzuweisung für eine Gruppe in einem Ressourcenbereich

Um einer Gruppe Zugriff im Ressourcenkontext zu gewähren, verwenden Sie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name_in_quotes> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="create-a-role-assignment-for-an-application-at-a-subscription-scope"></a>Erstellen einer Rollenzuweisung für eine Anwendung im Abonnementbereich

Um einer Anwendung Zugriff im Abonnementkontext zu gewähren, verwenden Sie [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment).

```azurepowershell
New-AzRoleAssignment -ObjectId <application id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

## <a name="remove-access"></a>Zugriff entfernen

In der RBAC entfernen Sie mit [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) eine Rollenzuweisung und somit den Zugriff.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope <scope_such_as_subscription>
```

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für eine Gruppe mit RBAC und Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe von Azure PowerShell](tutorial-custom-role-powershell.md)
- [Verwalten von Ressourcen mit Azure PowerShell](../azure-resource-manager/manage-resources-powershell.md)
