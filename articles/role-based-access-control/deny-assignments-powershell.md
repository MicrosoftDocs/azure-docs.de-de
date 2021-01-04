---
title: Auflisten von Ablehnungszuweisungen für Azure-Ressourcen mit Azure PowerShell – Azure RBAC
description: Erfahren Sie, wie Sie mithilfe von Azure-PowerShell und der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) die Benutzer, Gruppen, Dienstprinzipale und verwalteten Identitäten auflisten können, denen der Zugriff auf bestimmte Aktionen von Azure-Ressourcen in bestimmten Bereichen untersagt wurde.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 6cf379b051ba42be2d7df7d288f07cdc2a0002a1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657634"
---
# <a name="list-azure-deny-assignments-using-azure-powershell"></a>Auflisten von Ablehnungszuweisungen für Azure-Ressourcen mit Azure PowerShell

[Azure-Ablehnungszuweisungen](deny-assignments.md) hindern Benutzer an der Ausführung bestimmter Aktionen für Azure-Ressourcen, auch wenn ihnen über eine Rollenzuweisung Zugriff erteilt wird. In diesem Artikel wird beschrieben, wie Sie mit Azure PowerShell Ablehnungszuweisungen auflisten.

> [!NOTE]
> Sie können Ihre eigenen Ablehnungszuweisungen nicht direkt erstellen. Weitere Informationen dazu, wie Ablehnungszuweisungen erstellt werden, finden Sie unter [Azure-Ablehnungszuweisungen](deny-assignments.md).

## <a name="prerequisites"></a>Voraussetzungen

Um Informationen zu einer Ablehnungszuweisung abzurufen, müssen Sie über Folgendes verfügen:

- `Microsoft.Authorization/denyAssignments/read`-Berechtigung, die in den meisten [integrierten Azure-Rollen](built-in-roles.md) enthalten ist
- [PowerShell in Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="list-deny-assignments"></a>Auflisten von Ablehnungszuweisungen

### <a name="list-all-deny-assignments"></a>Auflisten aller Ablehnungszuweisungen

Um alle Ablehnungszuweisungen für das aktuelle Abonnement aufzulisten, verwenden Sie [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>Auflisten von Ablehnungszuweisungen in einem Ressourcengruppenbereich

Um alle Ablehnungszuweisungen in einem Ressourcengruppenbereich aufzulisten, verwenden Sie [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment).

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>Auflisten von Ablehnungszuweisungen in einem Abonnementbereich

Um alle Ablehnungszuweisungen in einem Abonnementbereich aufzulisten, verwenden Sie [Get-AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment). Die Abonnement-ID können Sie über das Blatt **Abonnements** im Azure-Portal oder durch Verwenden von [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) abrufen.

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>Nächste Schritte

- [Verstehen von Ablehnungszuweisungen für Azure-Ressourcen](deny-assignments.md)
- [Auflisten von Ablehnungszuweisungen über das Azure-Portal](deny-assignments-portal.md)
- [Auflisten von Azure-Ablehnungszuweisungen mithilfe der REST-API](deny-assignments-rest.md)