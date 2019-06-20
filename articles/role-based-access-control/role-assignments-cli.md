---
title: Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und der Azure CLI | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen und Anwendungen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) und der Azure CLI verwalten. Dazu gehören das Auflisten, Erteilen und Entfernen des Zugriffs.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1cc3d3eca4063a8120851a9d3de1a85292eacb11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344562"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und der Azure CLI

Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung (RBAC)](overview.md) verwaltet. In diesem Artikel wird beschrieben, wie Sie den Zugriff für Benutzer, Gruppen und Anwendungen mit der RBAC und der Azure-Befehlszeilenschnittstelle verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Für die Zugriffsverwaltung benötigen Sie Folgendes:

* [Bash in Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure-Befehlszeilenschnittstelle](/cli/azure)

## <a name="list-roles"></a>Auflisten der Rollen

Zum Auflisten aller verfügbaren Rollendefinitionen verwenden Sie [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Im folgenden Beispiel werden der Name und die Beschreibung aller verfügbaren Rollendefinitionen aufgelistet:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Im folgenden Beispiel werden alle integrierten Rollendefinitionen aufgelistet:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>Auflisten einer Rollendefinition

Zum Auflisten einer Rollendefinition verwenden Sie [az role definition list](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Im folgenden Beispiel wird die Rollendefinition für *Contributor* (Mitwirkender) aufgelistet:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>Auflisten der Aktionen einer Rolle

Im folgenden Beispiel werden einfach *actions* (Aktionen) und *notActions* (Keine Aktionen) der Rolle *Mitwirkender* aufgelistet:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Im folgenden Beispiel werden die Aktionen der Rolle *Mitwirkender für virtuelle Computer* aufgelistet:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Auflisten des Zugriffs

Zum Auflisten des Zugriffs in RBAC führen Sie die Rollenzuweisungen auf.

### <a name="list-role-assignments-for-a-user"></a>Liste von Rollenzuweisungen für einen Benutzer

Zum Auflisten der Rollenzuweisungen für einen bestimmten Benutzer verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Standardmäßig werden nur Zuweisungen im Abonnementkontext angezeigt. Zum Anzeigen von Zuweisungen im Ressourcen- oder Gruppenkontext verwenden Sie `--all`.

Im folgenden Beispiel werden die Rollenzuweisungen aufgelistet, die dem Benutzer *\@contoso.com* direkt zugewiesen sind:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Auflisten von Rollenzuweisungen für eine Ressourcengruppe

Zum Auflisten der Rollenzuweisungen, die für eine Ressourcengruppe vorhanden sind, verwenden Sie [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

Im folgenden Beispiel werden die Rollenzuweisungen für die Ressourcengruppe *pharma-sales* aufgelistet:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="grant-access"></a>Gewähren von Zugriff

In RBAC erstellen Sie zum Gewähren des Zugriffs eine Rollenzuweisung.

### <a name="create-a-role-assignment-for-a-user"></a>Erstellen einer Rollenzuweisung für einen Benutzer

Zum Erstellen einer Rollenzuweisung für einen Benutzer im Ressourcengruppenkontext verwenden Sie [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Im folgenden Beispiel wird dem Benutzer *patlong\@contoso.com* im Ressourcengruppenkontext *pharma-sales* die Rolle *Mitwirkender für virtuelle Computer* zugewiesen:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Erstellen einer Rollenzuweisung anhand der eindeutigen Rollen-ID

In bestimmten Fällen kann sich ein Rollenname ändern, z. B.:

- Sie verwenden eine eigene benutzerdefinierte Rolle und beschließen, den Namen zu ändern.
- Sie verwenden eine als Vorschauversion verfügbare Rolle, deren Name den Zusatz **(Vorschauversion)** enthält. Beim Veröffentlichen wird die Rolle umbenannt.

> [!IMPORTANT]
> Eine Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Auch wenn eine Rolle umbenannt wird, ändert sich die Rollen-ID nicht. Wenn Sie Ihre Rollenzuweisungen mithilfe von Skripts oder mittels Automatisierung erstellen, wird empfohlen, die eindeutige Rollen-ID anstelle des Rollennamens zu verwenden. Wird eine Rolle umbenannt, ist auf diese Weise die Wahrscheinlichkeit größer, dass Ihre Skripts funktionieren.

Verwenden Sie [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create), um eine Rollenzuweisung anstelle des Rollennamens mit der eindeutigen Rollen-ID zu erstellen.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Im folgenden Beispiel wird dem Benutzer *patlong\@contoso.com* im Ressourcengruppenkontext *pharma-sales* die Rolle [Mitwirkender für virtuelle Computer](built-in-roles.md#virtual-machine-contributor) zugewiesen. Zum Abrufen der eindeutigen Rollen-ID können Sie [az role definition list](/cli/azure/role/definition#az-role-definition-list) verwenden. Weitere Informationen finden Sie unter [Integrierte Rollen für Azure-Ressourcen](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Erstellen einer Rollenzuweisung für eine Gruppe

Zum Erstellen einer Rollenzuweisung für eine Gruppe verwenden Sie [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Im folgenden Beispiel wird der Gruppe *Ann Mack Team* mit der ID 22222222-2222-2222-2222-222222222222 im Abonnementkontext die Rolle *Reader* zugewiesen. Zum Abrufen der ID der Gruppe können Sie [az ad group list](/cli/azure/ad/group#az-ad-group-list) oder [az ad group show](/cli/azure/ad/group#az-ad-group-show) verwenden.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Im folgenden Beispiel wird die Rolle *Virtual Machine Contributor* der Gruppe *Ann Mack Team* mit der ID 22222222-2222-2222-2222-222222222222 im Ressourcenkontext für ein virtuelles Netzwerk mit dem Namen *pharma-sales-project-network* zugewiesen:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application"></a>Erstellen einer Rollenzuweisung für eine Anwendung

Zum Erstellen einer Rolle für eine Anwendung verwenden Sie [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create):

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Im folgenden Beispiel wird einer Anwendung mit der Objekt-ID 44444444-4444-4444-4444-444444444444 im Ressourcengruppenkontext *pharma-sales* die Rolle *Virtual Machine Contributor* zugewiesen. Zum Abrufen der Objekt-ID der Anwendung können Sie [az ad app list](/cli/azure/ad/app#az-ad-app-list) oder [az ad app show](/cli/azure/ad/app#az-ad-app-show) verwenden.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

## <a name="remove-access"></a>Zugriff entfernen

In RBAC entfernen Sie mit [az role assignment delete](/cli/azure/role/assignment#az-role-assignment-delete) eine Rollenzuweisung, um den Zugriff zu entfernen:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Im folgenden Beispiel wird die Zuweisung der Rolle *Mitwirkender für virtuelle Computer* von Benutzer *patlong\@contoso.com* für die Ressourcengruppe *pharma-sales* entfernt:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Im folgenden Beispiel wird die Rolle *Reader* von der Gruppe *Ann Mack Team* mit der ID 22222222-2222-2222-2222-222222222222 im Abonnementkontext entfernt. Zum Abrufen der ID der Gruppe können Sie [az ad group list](/cli/azure/ad/group#az-ad-group-list) oder [az ad group show](/cli/azure/ad/group#az-ad-group-show) verwenden.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe der Azure CLI](tutorial-custom-role-cli.md)
- [Verwalten von Azure-Ressourcen und -Ressourcengruppen mithilfe der Azure-Befehlszeilenschnittstelle](../azure-resource-manager/cli-azure-resource-manager.md)
