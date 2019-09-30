---
title: Erstellen von benutzerdefinierten Rollen für Azure-Ressourcen mit der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation
description: Hier erfahren Sie, wie benutzerdefinierte Rollen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure-Ressourcen mithilfe der Azure CLI erstellt werden. Dies umfasst das Auflisten, Erstellen, Aktualisieren und Löschen von benutzerdefinierten Rollen.
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
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ebced83346a7b130598e4a5f49a72d51ffd18e4f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118772"
---
# <a name="create-custom-roles-for-azure-resources-using-azure-cli"></a>Erstellen von benutzerdefinierten Rollen für Azure-Ressourcen mithilfe der Azure CLI

Wenn die integrierten Rollen für Azure-Ressourcen den Ansprüchen Ihrer Organisation nicht entsprechen, können Sie Ihre eigenen [benutzerdefinierten Rollen](built-in-roles.md) erstellen. In diesem Artikel wird das Erstellen und Verwalten benutzerdefinierter Rollen mithilfe der Azure CLI beschrieben.

Ein ausführliches Tutorial zum Erstellen einer benutzerdefinierten Rolle finden Sie unter [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe der Azure CLI](tutorial-custom-role-cli.md).

## <a name="prerequisites"></a>Voraussetzungen

Zum Erstellen von benutzerdefinierten Rollen benötigen Sie Folgendes:

- Berechtigungen zum Erstellen von benutzerdefinierten Rollen, etwa [Besitzer](built-in-roles.md#owner) oder [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) oder [Azure CLI](/cli/azure/install-azure-cli).
- Im Fall, dass das Azure CLI genutzt wird ist es erforderlich, das Toll 'jq' installiert ist. 
     Es kann wie folgt installiert werden
     **Debian/Ubuntu**
     - sudo apt-get install jq

     **RedHat/CentOS**
     - sudo yum install jq

     **SUSE SLES** 
     - sudo zypper install jq- Im Fall, dass das Azure CLI genutzt wird ist es erforderlich, das Toll 'jq' vorhanden ist. Es kann nachträglich 

## <a name="list-custom-roles"></a>Auflisten benutzerdefinierter Rollen

Zum Auflisten der benutzerdefinierten Rollen, die zur Zuweisung verfügbar sind, verwenden Sie [az role definition list](/cli/azure/role/definition#az-role-definition-list). In den folgenden Beispielen werden alle benutzerdefinierten Rollen im aktuellen Abonnement aufgelistet.

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>Auflisten einer Definition einer benutzerdefinierten Rolle

Zum Auflisten der Definition einer benutzerdefinierten Rolle verwenden Sie [az role definition list](/cli/azure/role/definition#az-role-definition-list). Dies ist derselbe Befehl, den Sie für eine integrierte Rolle verwenden würden.

```azurecli
az role definition list --name <role_name>
```

Im folgenden Beispiel wird die benutzerdefinierte Rolle *Virtual Machine Operator* aufgelistet:

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
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
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Im folgenden Beispiel werden die Aktionen der Rolle *Virtual Machine Operator* aufgelistet:

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
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
]
```

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Zum Erstellen einer benutzerdefinierten Rolle verwenden Sie [az role definition create](/cli/azure/role/definition#az-role-definition-create). Bei der Rollendefinition kann es sich um eine JSON-Beschreibung oder einen Pfad zu einer Datei mit einer JSON-Beschreibung handeln.

```azurecli
az role definition create --role-definition <role_definition>
```

Im folgenden Beispiel wird eine benutzerdefinierte Rolle mit dem Namen *Virtual Machine Operator* erstellt. Diese benutzerdefinierte Rolle weist den Zugriff auf alle Lesevorgänge der Ressourcenanbieter *Microsoft.Compute*, *Microsoft.Storage* und *Microsoft.Network* sowie den Zugriff zum Starten, Neustarten und Überwachen virtueller Computer zu. Diese benutzerdefinierte Rolle kann in zwei Abonnements verwendet werden. In diesem Beispiel wird eine JSON-Datei als Eingabe genutzt.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>Aktualisieren einer benutzerdefinierten Rolle

Zum Aktualisieren einer benutzerdefinierten Rolle verwenden Sie zuerst [az role definition list](/cli/azure/role/definition#az-role-definition-list), um die Rollendefinition abzurufen. Nehmen Sie zweitens die gewünschten Änderungen an der Rollendefinitionsdatei vor. Verwenden Sie zum Schluss [az role definition update](/cli/azure/role/definition#az-role-definition-update), um die aktualisierte Rollendefinition zu speichern.

```azurecli
az role definition update --role-definition <role_definition>
```

Im folgenden Beispiel wird der Vorgang *Microsoft.Insights/diagnosticSettings/* zu *Actions* der benutzerdefinierten Rolle *Virtual Machine Operator* hinzugefügt.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
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
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>Löschen einer benutzerdefinierten Rolle

Zum Löschen einer benutzerdefinierten Rolle verwenden Sie [az role definition delete](/cli/azure/role/definition#az-role-definition-delete). Zum Angeben der zu löschenden Rolle verwenden Sie den Rollennamen oder die Rollen-ID. Zum Ermitteln der Rollen-ID verwenden Sie [az role definition list](/cli/azure/role/definition#az-role-definition-list).

```azurecli
az role definition delete --name <role_name or role_id>
```

Im folgenden Beispiel wird die benutzerdefinierte Rolle *Virtual Machine Operator* gelöscht.

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erstellen einer benutzerdefinierten Rolle für Azure-Ressourcen mithilfe der Azure CLI](tutorial-custom-role-cli.md)
- [Benutzerdefinierte Rollen für Azure-Ressourcen](custom-roles.md)
- [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md)