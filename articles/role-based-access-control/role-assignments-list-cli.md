---
title: Auflisten von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle – Azure RBAC
description: Erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle und Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) ermitteln können, auf welche Ressourcen Benutzer, Gruppen, Dienstprinzipale und verwaltete Identitäten Zugriff haben.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e44437bb926e8d2d1ffcd3134bbc63538a112ff0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648309"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>Auflisten von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] In diesem Artikel wird beschrieben, wie Sie mit der Azure-Befehlszeilenschnittstelle Rollenzuweisungen auflisten.

> [!NOTE]
> Wenn Ihre Organisation Verwaltungsfunktionen an einen Dienstanbieter ausgelagert hat, der die [delegierte Azure-Ressourcenverwaltung](../lighthouse/concepts/azure-delegated-resource-management.md) verwendet, werden die von diesem Dienstanbieter autorisierten Rollenzuweisungen hier nicht angezeigt.

## <a name="prerequisites"></a>Voraussetzungen

- [Bash in der Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure-Befehlszeilenschnittstelle](/cli/azure)

## <a name="list-role-assignments-for-a-user"></a>Liste von Rollenzuweisungen für einen Benutzer

Zum Auflisten der Rollenzuweisungen für einen bestimmten Benutzer verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee {assignee}
```

Standardmäßig werden nur Rollenzuweisungen für das aktuelle Abonnement angezeigt. Um Rollenzuweisungen für das aktuelle Abonnement und darin befindliche Abonnements anzuzeigen, fügen Sie den Parameter `--all` hinzu. Um geerbte Rollenzuweisungen anzuzeigen, fügen Sie den Parameter `--include-inherited` hinzu.

Im folgenden Beispiel werden die Rollenzuweisungen aufgelistet, die dem Benutzer *\@contoso.com* direkt zugewiesen sind:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>Auflisten von Rollenzuweisungen für eine Ressourcengruppe

Zum Auflisten der Rollenzuweisungen, die für einen Ressourcengruppenbereich vorhanden sind, verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group {resourceGroup}
```

Im folgenden Beispiel werden die Rollenzuweisungen für die Ressourcengruppe *pharma-sales* aufgelistet:

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>Auflisten der Rollenzuweisungen für ein Abonnement

Um alle Rollenzuweisungen in einem Abonnementbereich aufzulisten, verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Die Abonnement-ID können Sie über das Blatt **Abonnements** im Azure-Portal oder durch Verwenden von [az account list](/cli/azure/account#az-account-list) abrufen.

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

Beispiel:

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>Auflisten von Rollenzuweisungen für eine Verwaltungsgruppe

Um alle Rollenzuweisungen in einem Verwaltungsgruppenbereich aufzulisten, verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list). Die Verwaltungsgruppen-ID befindet sich auf dem Blatt **Verwaltungsgruppen** im Azure-Portal, oder Sie können zum Abrufen auch [az account management-group list](/cli/azure/account/management-group#az-account-management-group-list) verwenden.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

Beispiel:

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Auflisten von Rollenzuweisungen für eine verwaltete Identität

1. Rufen Sie die Prinzipal-ID der systemseitig oder benutzerseitig zugewiesenen verwalteten Identität ab.

    Um die Prinzipal-ID einer benutzerseitig zugewiesenen verwalteten Identität abzurufen, können Sie [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) oder [az identity list](/cli/azure/identity#az-identity-list) verwenden.

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    Um die Prinzipal-ID einer systemseitig zugewiesenen verwalteten Identität abzurufen, können Sie [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) verwenden.

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. Zum Auflisten der Rollenzuweisungen verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list).

    Standardmäßig werden nur Rollenzuweisungen für das aktuelle Abonnement angezeigt. Um Rollenzuweisungen für das aktuelle Abonnement und darin befindliche Abonnements anzuzeigen, fügen Sie den Parameter `--all` hinzu. Um geerbte Rollenzuweisungen anzuzeigen, fügen Sie den Parameter `--include-inherited` hinzu.

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](role-assignments-cli.md)