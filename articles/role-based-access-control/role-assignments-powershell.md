---
title: Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell – Azure RBAC
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen, Dienstprinzipale und verwaltete Identitäten mit Azure PowerShell und der rollenbasierten Zugriffssteuerung (Azure RBAC) erteilen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: c4082f7fc535807ec996034ba695549a51969a99
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182409"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] In diesem Artikel wird das Zuweisen von Rollen mit Azure PowerShell beschrieben.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Rollenzuweisungen hinzufügen oder entfernen zu können:

- `Microsoft.Authorization/roleAssignments/write`- und `Microsoft.Authorization/roleAssignments/delete`-Berechtigungen, wie z.B. [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) oder [Besitzer](built-in-roles.md#owner)
- [PowerShell in Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="steps-to-add-a-role-assignment"></a>Schritte zum Hinzufügen einer Rollenzuweisung

In Azure RBAC fügen Sie zum Gewähren des Zugriffs eine Rollenzuweisung hinzu. Eine Rollenzuweisung besteht aus drei Elementen: Sicherheitsprinzipal, Rollendefinition und Bereich. Führen Sie die folgenden Schritte aus, um eine Rollenzuweisung hinzuzufügen.

### <a name="step-1-determine-who-needs-access"></a>Schritt 1: Ermitteln, wer Zugriff benötigt

Sie können eine Rolle einem Benutzer, einer Gruppe, einem Dienstprinzipal oder einer verwalteten Identität zuweisen. Sie müssen eventuell die eindeutige ID des Objekts angeben, um Rollenzuweisungen hinzufügen zu können. Die ID weist dieses Format auf: `11111111-1111-1111-1111-111111111111`. Sie können die ID über das Azure-Portal oder Azure PowerShell abrufen.

**Benutzer**

Bei einem Azure AD-Benutzer rufen Sie den Benutzerprinzipalnamen (z. B. *patlong\@contoso.com*) oder die Benutzerobjekt-ID ab. Zum Abrufen der Objekt-ID können Sie [Get-AzADUser](/powershell/module/az.resources/get-azaduser) verwenden.

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**Gruppieren**

Bei einer Azure AD-Gruppe benötigen Sie die Gruppenobjekt-ID. Zum Abrufen der Objekt-ID können Sie [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) verwenden.

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**Dienstprinzipal**

Bei einem Azure AD-Dienstprinzipal (einer von einer Anwendung verwendeten Identität) benötigen Sie die Objekt-ID des Dienstprinzipals. Zum Abrufen der Objekt-ID können Sie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) verwenden. Verwenden Sie für einen Dienstprinzipal die Objekt-ID und **nicht** die Anwendungs-ID.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**Verwaltete Identität**

Bei einer systemseitig oder benutzerseitig zugewiesenen verwalteten Identität benötigen Sie die Objekt-ID. Zum Abrufen der Objekt-ID können Sie [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) verwenden.

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>Schritt 2: Suchen der gewünschten Rolle

Berechtigungen sind in Rollen zusammengefasst. Sie können aus einer Liste von verschiedenen [in Azure integrierten Rollen](built-in-roles.md) auswählen oder eigene benutzerdefinierte Rollen verwenden. Es hat sich bewährt, den Zugriff mit den minimal erforderlichen Berechtigungen zu gewähren. Daher sollten Sie keine allgemeine Rolle zuweisen.

Zum Auflisten der Rollen und Abrufen der eindeutigen Rollen-ID können Sie [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) verwenden.

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

Im Folgenden wird gezeigt, wie Sie die Details einer bestimmten Rolle auflisten.

```azurepowershell
Get-AzRoleDefinition <roleName>
```

Weitere Informationen finden Sie unter [Auflisten von Azure-Rollendefinitionen](role-definitions-list.md#azure-powershell).
 
### <a name="step-3-identify-the-needed-scope"></a>Schritt 3: Ermitteln des erforderlichen Bereichs

Azure bietet vier Bereichsebenen: Ressource, [Ressourcengruppe](../azure-resource-manager/management/overview.md#resource-groups), Abonnement und [Verwaltungsgruppe](../governance/management-groups/overview.md). Es hat sich bewährt, den Zugriff mit den minimal erforderlichen Berechtigungen zu gewähren. Daher sollten Sie Rollen nicht für einen allgemeinen Bereich zuweisen. Weitere Informationen zum Begriff „Bereich“ finden Sie unter [Grundlegendes zum Begriff „Bereich“](scope-overview.md).

**Ressourcenumfang**

Für den Ressourcenbereich benötigen Sie die Ressourcen-ID der Ressource. Die Ressourcen-ID finden Sie unter den Eigenschaften der Ressource im Azure-Portal. Ressourcen-IDs weisen folgendes Format auf:

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**Ressourcengruppenbereich**

Für den Bereich der Ressourcengruppe benötigen Sie den Namen der Ressourcengruppe. Sie finden den Namen im Azure-Portal auf der Seite **Ressourcengruppen** oder mithilfe von [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
Get-AzResourceGroup
```

**Abonnementbereich** 

Für den Abonnementbereich benötigen Sie die Abonnement-ID. Sie finden die Abonnement-ID im Azure-Portal auf der Seite **Abonnements** oder durch Ausführen von [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription).

```azurepowershell
Get-AzSubscription
```

**Verwaltungsgruppenbereich** 

Für den Verwaltungsgruppenbereich benötigen Sie den Namen der Verwaltungsgruppe. Sie finden den Namen im Azure-Portal auf der Seite **Verwaltungsgruppen** oder durch Ausführen von [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup).

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>Schritt 4: Rollenzuweisung hinzufügen

Zum Hinzufügen einer Rollenzuweisung verwenden Sie den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment). Abhängig vom Bereich weist der Befehl in der Regel eines der folgenden Formate auf.

**Ressourcenumfang**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**Ressourcengruppenbereich**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**Abonnementbereich** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**Verwaltungsgruppenbereich** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>Beispiele für das Hinzufügen von Rollenzuweisungen

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Hinzufügen einer Rollenzuweisung für alle Blobcontainer in einem Speicherkonto-Ressourcenbereich

Weist einem Dienstprinzipal mit der Objekt-ID *55555555-5555-5555-5555-555555555555* die Rolle [Mitwirkender an Speicherblobdaten](built-in-roles.md#storage-blob-data-contributor) im Ressourcenbereich für das Speicherkonto *storage12345* zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>Hinzufügen einer Rollenzuweisung für einen bestimmten Blobcontainer-Ressourcenbereich

Weist einem Dienstprinzipal mit der Objekt-ID *55555555-5555-5555-5555-555555555555* die Rolle [Mitwirkender an Speicherblobdaten](built-in-roles.md#storage-blob-data-contributor) im Ressourcenbereich für den Blobcontainer *blob-container-01* zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Hinzufügen einer Rollenzuweisung für eine Gruppe im Ressourcenbereich eines bestimmten virtuellen Netzwerks

Weist die Rolle [Mitwirkender von virtuellen Computern](built-in-roles.md#virtual-machine-contributor) der Gruppe *Pharma Sales Admins* mit der ID „aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa“ an einem Ressourcenbereich für ein virtuelles Netzwerk namens *pharma-sales-project-network* zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer in einem Ressourcengruppenbereich

Weist dem Benutzer *patlong\@contoso.com* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) im Ressourcengruppenbereich *pharma-sales* zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

Alternativ kann die vollqualifizierte Ressourcengruppe auch mithilfe des Parameters `-Scope` angegeben werden:

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer mithilfe der eindeutigen Rollen-ID in einem Ressourcengruppenbereich

In bestimmten Fällen kann sich ein Rollenname ändern, z. B.:

- Sie verwenden eine eigene benutzerdefinierte Rolle und beschließen, den Namen zu ändern.
- Sie verwenden eine als Vorschauversion verfügbare Rolle, deren Name den Zusatz **(Vorschauversion)** enthält. Beim Veröffentlichen wird die Rolle umbenannt.

Auch wenn eine Rolle umbenannt wird, ändert sich die Rollen-ID nicht. Wenn Sie Ihre Rollenzuweisungen mithilfe von Skripts oder mittels Automatisierung erstellen, wird empfohlen, die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, ist auf diese Weise die Wahrscheinlichkeit größer, dass Ihre Skripts funktionieren.

Im folgenden Beispiel wird dem Benutzer *patlong\@contoso.com* im Ressourcengruppenkontext *pharma-sales* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) zugewiesen.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>Hinzufügen einer Rollenzuweisung für eine Anwendung in einem Ressourcengruppenbereich

Weist einer Anwendung mit der Objekt-ID „77777777-7777-7777-7777-777777777777“ des Dienstprinzipals die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) im Ressourcengruppenbereich *pharma-sales* zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer im Abonnementbereich

Weist dem Benutzer *annm\@example.com* die Rolle [Leser](built-in-roles.md#reader) im Abonnementbereich zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>Hinzufügen einer Rollenzuweisung für einen Benutzer in einem Verwaltungsgruppenbereich

Weist dem Benutzer *alain\@example.com* die Rolle [Abrechnungsleser](built-in-roles.md#billing-reader) im Verwaltungsgruppenbereich zu.

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Entfernen einer Rollenzuweisung

In Azure RBAC entfernen Sie mit [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) eine Rollenzuweisung und somit den Zugriff.

Im folgenden Beispiel wird die Zuweisung der Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) von Benutzer *patlong\@contoso.com* für die Ressourcengruppe *pharma-sales* entfernt:

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

Entfernt die Rolle [Leser](built-in-roles.md#reader) von der Gruppe *Ann Mack Team* mit der ID „22222222-2222-2222-2222-222222222222“ im Abonnementbereich.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

Entfernt die Rolle [Abrechnungsleser](built-in-roles.md#billing-reader) vom Benutzer *alain\@example.com* im Verwaltungsgruppenbereich.

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

Wenn Sie die folgende Fehlermeldung erhalten: „The provided information does not map to a role assignment“ (Die angegebenen Informationen stimmen mit keiner Rollenzuweisung überein), sollten Sie sicherstellen, dass auch die Parameter `-Scope` oder `-ResourceGroupName` angegeben werden. Weitere Informationen finden Sie unter [Behandeln von Problemen bei Azure RBAC](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>Nächste Schritte

- [Auflisten von Azure-Rollenzuweisungen mit Azure PowerShell](role-assignments-list-powershell.md)
- [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für eine Gruppe mithilfe von Azure PowerShell](tutorial-role-assignments-group-powershell.md)
- [Verwalten von Ressourcen mit Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)