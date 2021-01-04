---
title: 'Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für eine Gruppe mithilfe von Azure PowerShell – Azure RBAC'
description: In diesem Tutorial erfahren Sie, wie Sie einer Gruppe mit Azure PowerShell und der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) Zugriff auf Azure-Ressourcen gewähren.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: 9b1a2f87498d7869b21e6a8ab44fc1f9660456ad
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648293"
---
# <a name="tutorial-grant-a-group-access-to-azure-resources-using-azure-powershell"></a>Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für eine Gruppe mithilfe von Azure PowerShell

Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)](overview.md) verwaltet. In diesem Tutorial gewähren Sie einer Gruppe Zugriff, damit sie mithilfe von Azure PowerShell alle Elemente in einem Abonnement anzeigen und alle Elemente in einer Ressourcengruppe verwalten kann.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Gewähren des Zugriffs für eine Gruppe für verschiedene Bereiche
> * Auflisten des Zugriffs
> * Zugriff entfernen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Tutorials benötigen Sie Folgendes:

- Berechtigungen zum Erstellen von Gruppen in Azure Active Directory (oder eine vorhandene Gruppe)
- [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md)

## <a name="role-assignments"></a>Rollenzuweisungen

In Azure RBAC erstellen Sie zum Gewähren des Zugriffs eine Rollenzuweisung. Eine Rollenzuweisung besteht aus drei Elementen: Sicherheitsprinzipal, Rollendefinition und Bereich. In diesem Tutorial führen Sie die zwei folgenden Rollenzuweisungen aus:

| Sicherheitsprinzipal | Rollendefinition | `Scope` |
| --- | --- | --- |
| Group<br>(Gruppe des RBAC-Tutorials) | [Leser](built-in-roles.md#reader) | Subscription |
| Group<br>(Gruppe des RBAC-Tutorials)| [Mitwirkender](built-in-roles.md#contributor) | Resource group<br>(rbac-tutorial-resource-group) |

   ![Rollenzuweisungen für eine Gruppe](./media/tutorial-role-assignments-group-powershell/rbac-role-assignments.png)

## <a name="create-a-group"></a>Erstellen einer Gruppe

Zum Zuweisen einer Rolle benötigen Sie einen Benutzer, eine Gruppe oder einen Dienstprinzipal. Ist noch keine Gruppe vorhanden, können Sie eine erstellen.

- Erstellen Sie in Azure Cloud Shell mithilfe des Befehls [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) eine neue Gruppe.

   ```azurepowershell
   New-AzureADGroup -DisplayName "RBAC Tutorial Group" `
     -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
   ```

   ```Example
   ObjectId                             DisplayName         Description
   --------                             -----------         -----------
   11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
   ```

Wenn Sie nicht über die Berechtigungen zum Erstellen von Gruppen verfügen, können Sie es stattdessen mit dem [Tutorial: Gewähren des Zugriffs auf Azure-Ressourcen für einen Benutzer mithilfe von Azure PowerShell](tutorial-role-assignments-user-powershell.md) versuchen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Sie verwenden eine Ressourcengruppe, um zu veranschaulichen, wie eine Rolle im Ressourcengruppenbereich zugewiesen wird.

1. Rufen Sie mit dem Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation) eine Liste der Regionsstandorte ab.

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Wählen Sie einen Standort in Ihrer Nähe, und weisen Sie ihn einer Variablen zu.

   ```azurepowershell
   $location = "westus"
   ```

1. Erstellen Sie mit dem Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine neue Ressourcengruppe.

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Gewähren von Zugriff

Um Zugriff für die Gruppe zu gewähren, verwenden Sie den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) zum Zuweisen einer Rolle. Sie müssen den Sicherheitsprinzipal, die Rollendefinition und den Bereich angeben.

1. Rufen Sie mit dem Befehl [Get-AzureADGroup](/powershell/module/azuread/new-azureadgroup) die Objekt-ID der Gruppe ab.

    ```azurepowershell
    Get-AzureADGroup -SearchString "RBAC Tutorial Group"
    ```

    ```Example
    ObjectId                             DisplayName         Description
    --------                             -----------         -----------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial Group
    ```

1. Speichern Sie die Gruppenobjekt-ID in einer Variablen.

    ```azurepowershell
    $groupId = "11111111-1111-1111-1111-111111111111"
    ```

1. Rufen Sie die ID Ihres Abonnements mithilfe des Befehls [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) ab.

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Speichern Sie den Abonnementbereich in einer Variablen.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Weisen Sie die Rolle [Leser](built-in-roles.md#reader) der Gruppe im Abonnementbereich zu.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

1. Weisen Sie die Rolle [Mitwirkender](built-in-roles.md#contributor) der Gruppe im Ressourcengruppenbereich zu.

    ```azurepowershell
    New-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

## <a name="list-access"></a>Auflisten des Zugriffs

1. Um den Zugriff für das Abonnement zu überprüfen, listen Sie mithilfe des Befehls [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) die Rollenzuweisungen auf.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    In der Ausgabe sehen Sie, dass die Rolle „Leser“ der Gruppe des RBAC-Tutorials im Abonnementbereich zugewiesen ist.

1. Um den Zugriff für die Ressourcengruppe zu überprüfen, listen Sie mit dem Befehl [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) die Rollenzuweisungen auf.

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId $groupId -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial Group
    SignInName         :
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : Group
    CanDelegate        : False
    ```

    In der Ausgabe sehen Sie, dass die Rollen „Mitwirkender“ und „Leser“ der Gruppe des RBAC-Tutorials zugewiesen sind. Die Rolle „Mitwirkender“ befindet sich im Bereich „rbac-tutorial-resource-group“, und die Rolle „Leser“ wird im Abonnementbereich geerbt.

## <a name="optional-list-access-using-the-azure-portal"></a>(Optional) Auflisten des Zugriffs über das Azure-Portal

1. Wenn Sie die Darstellung der Rollenzuweisungen im Azure-Portal anzeigen möchten, sehen Sie sich das Blatt **Zugriffssteuerung (IAM)** für das Abonnement an.

    ![Rollenzuweisungen für eine Gruppe im Abonnementbereich](./media/tutorial-role-assignments-group-powershell/role-assignments-subscription.png)

1. Sehen Sie sich das Blatt **Zugriffssteuerung (IAM)** für die Ressourcengruppe an.

    ![Rollenzuweisungen für eine Gruppe im Ressourcengruppenbereich](./media/tutorial-role-assignments-group-powershell/role-assignments-resource-group.png)

## <a name="remove-access"></a>Zugriff entfernen

Verwenden Sie zum Entfernen des Zugriffs für Benutzer, Gruppen und Anwendungen [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment), um eine Rollenzuweisung zu entfernen.

1. Verwenden Sie den folgenden Befehl, um die Zuweisung der Rolle „Mitwirkender“ für die Gruppe im Ressourcengruppenbereich zu entfernen.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. Verwenden Sie den folgenden Befehl, um die Zuweisung der Rolle „Leser“ für die Gruppe im Abonnementbereich zu entfernen.

    ```azurepowershell
    Remove-AzRoleAssignment -ObjectId $groupId `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zum Bereinigen der im Rahmen dieses Tutorials erstellten Ressourcen löschen Sie die Ressourcengruppe und die Gruppe.

1. Löschen Sie die Ressourcengruppe mithilfe des Befehls [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Wenn Sie zum Bestätigen aufgefordert werden, geben Sie **Y** ein. Es dauert einige Sekunden, bis der Löschvorgang abgeschlossen ist.

1. Löschen Sie die Gruppe mithilfe des Befehls [Remove-AzureADGroup](/powershell/module/azuread/remove-azureadgroup).

    ```azurepowershell
    Remove-AzureADGroup -ObjectId $groupId
    ```
    
    Wenn Sie beim Löschen der Gruppe eine Fehlermeldung erhalten, können Sie die Gruppe auch im Portal löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell](role-assignments-powershell.md)