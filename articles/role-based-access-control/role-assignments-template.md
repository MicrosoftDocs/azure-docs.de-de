---
title: Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen – Azure RBAC
description: Hier erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen, Dienstprinzipale und verwaltete Identitäten mit Azure Resource Manager-Vorlagen und der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) gewähren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9bdd70baa906d9dc03a37eecb0388eee5638f153
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184279"
---
# <a name="add-azure-role-assignments-using-azure-resource-manager-templates"></a>Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Neben der Verwendung von Azure PowerShell oder der Azure-Befehlszeilenschnittstelle können Sie Rollen auch mit [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md) zuweisen. Vorlagen können hilfreich sein, wenn Sie Ressourcen konsistent und wiederholt bereitstellen müssen. In diesem Artikel wird das Zuweisen von Rollen mithilfe von Vorlagen beschrieben.

## <a name="get-object-ids"></a>Abrufen von Objekt-IDs

Um eine Rolle zuzuweisen, müssen Sie die ID des Benutzers, der Gruppe oder der Anwendung angeben, dem bzw. der Sie die Rolle zuweisen möchten. Die ID weist dieses Format auf: `11111111-1111-1111-1111-111111111111`. Sie können die ID über das Azure-Portal, Azure PowerShell oder die Azure-Befehlszeilenschnittstelle (Azure CLI) abrufen.

### <a name="user"></a>Benutzer

Zum Abrufen eines Benutzers können Sie die Befehle [Get-AzADUser](/powershell/module/az.resources/get-azaduser) oder [az ad user show](/cli/azure/ad/user#az-ad-user-show) verwenden.

```azurepowershell
$objectid = (Get-AzADUser -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad user show --id "{email}" --query objectId --output tsv)
```

### <a name="group"></a>Group

Zum Abrufen einer Gruppe können Sie die Befehle [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) oder [az ad group show](/cli/azure/ad/group#az-ad-group-show) verwenden.

```azurepowershell
$objectid = (Get-AzADGroup -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad group show --group "{name}" --query objectId --output tsv)
```

### <a name="managed-identities"></a>Verwaltete Identitäten

Zum Abrufen der ID einer verwalteten Identität können Sie die Befehle [Get-AzAdServiceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) oder [az ad sp](/cli/azure/ad/sp) verwenden.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName <Azure resource name>).id
```

```azurecli
objectid=$(az ad sp list --display-name <Azure resource name> --query [].objectId --output tsv)
```

### <a name="application"></a>Application

Zum Abrufen eines Dienstprinzipals (eine von einer Anwendung verwendete Identität) können Sie die Befehle [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) oder [az ad sp list](/cli/azure/ad/sp#az-ad-sp-list) verwenden. Verwenden Sie für einen Dienstprinzipal die Objekt-ID und **nicht** die Anwendungs-ID.

```azurepowershell
$objectid = (Get-AzADServicePrincipal -DisplayName "{name}").id
```

```azurecli
objectid=$(az ad sp list --display-name "{name}" --query [].objectId --output tsv)
```

## <a name="add-a-role-assignment"></a>Hinzufügen einer Rollenzuweisung

In Azure RBAC fügen Sie zum Gewähren des Zugriffs eine Rollenzuweisung hinzu.

### <a name="resource-group-scope-without-parameters"></a>Ressourcengruppenbereich (ohne Parameter)

Die folgende Vorlage zeigt eine einfache Möglichkeit zum Hinzufügen einer Rollenzuweisung. Einige Werte werden in der Vorlage angegeben. Die folgende Vorlage veranschaulicht Folgendes:

-  Zuweisen der Rolle [Leser](built-in-roles.md#reader) zu einem Benutzer, einer Gruppe oder einer Anwendung in einem Ressourcengruppenbereich

Um die Vorlage zu verwenden, müssen Sie folgendermaßen vorgehen:

- Erstellen Sie eine neue JSON-Datei, und kopieren Sie die Vorlage.
- Ersetzen Sie `<your-principal-id>` durch die ID eines Benutzers, einer Gruppe, einer verwalteten Identität oder einer Anwendung, dem bzw. der die Rolle zugewiesen wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(resourceGroup().id)]",
            "properties": {
                "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
                "principalId": "<your-principal-id>"
            }
        }
    ]
}
```

Im Folgenden finden Sie Beispiele für die Befehle [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) zum Starten der Bereitstellung in einer Ressourcengruppe mit dem Namen „ExampleGroup“.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json
```

Das folgende Beispiel veranschaulicht die Zuweisung der Rolle „Leser“ zu einem Benutzer für eine Ressourcengruppe nach der Bereitstellung der Vorlage.

![Rollenzuweisung in einem Ressourcengruppenbereich](./media/role-assignments-template/role-assignment-template.png)

### <a name="resource-group-or-subscription-scope"></a>Ressourcengruppe oder Abonnementbereich

Die vorherige Vorlage ist nicht sehr flexibel. Die folgende Vorlage enthält Parameter, die in unterschiedlichen Bereichen verwendet werden können. Die folgende Vorlage veranschaulicht Folgendes:

- Zuweisen einer Rolle zu einem Benutzer, einer Gruppe oder einer Anwendung in einem Ressourcengruppen- oder Abonnementbereich
- Angeben der Rollen „Besitzer“, „Mitwirkender“ und „Leser“ als Parameter

Um die Vorlage zu verwenden, müssen Sie Folgendes eingeben:

- Die ID eines Benutzers, einer Gruppe, einer verwalteten Identität oder einer Anwendung, dem bzw. der die Rolle zugewiesen wird
- Eine eindeutige ID, die für die Rollenzuweisung verwendet wird (oder alternativ die Standard-ID)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

> [!NOTE]
> Diese Vorlage ist nicht idempotent, es sei denn, derselbe `roleNameGuid`-Wert wird als Parameter bei jeder Bereitstellung der Vorlage bereitgestellt. Wenn keine `roleNameGuid` angegeben wird, wird standardmäßig für jede Bereitstellung eine neue GUID generiert, und bei nachfolgenden Bereitstellungen tritt ein `Conflict: RoleAssignmentExists`-Fehler auf.

Der Bereich der Rollenzuweisung wird von der Bereitstellungsebene bestimmt. Im Folgenden finden Sie Beispiele für die Befehle [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) zum Starten der Bereitstellung in einem Ressourcengruppenbereich.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

Im Folgenden finden Sie Beispiele für die Befehle [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) und [az deployment create](/cli/azure/deployment#az-deployment-create) zum Starten der Bereitstellung in einem Abonnementbereich und zum Angeben des Standorts.

```azurepowershell
New-AzDeployment -Location centralus -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Reader
```

```azurecli
az deployment create --location centralus --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Reader
```

### <a name="resource-scope"></a>Ressourcenumfang

Wenn Sie auf der Ebene einer Ressource eine Rollenzuweisung hinzufügen müssen, legen Sie für die `scope`-Eigenschaft der Rollenzuweisung den Namen der Ressource fest.

Die folgende Vorlage veranschaulicht Folgendes:

- Erstellen eines neuen Speicherkontos
- Zuweisen einer Rolle zu einem Benutzer, einer Gruppe oder einer Anwendung im Speicherkontobereich
- Angeben der Rollen „Besitzer“, „Mitwirkender“ und „Leser“ als Parameter

Um die Vorlage zu verwenden, müssen Sie Folgendes eingeben:

- Die ID eines Benutzers, einer Gruppe, einer verwalteten Identität oder einer Anwendung, dem bzw. der die Rolle zugewiesen wird

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
        "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "scope": "[concat('Microsoft.Storage/storageAccounts', '/', variables('storageName'))]",
            "dependsOn": [
                "[variables('storageName')]"
            ],
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Zum Bereitstellen der vorherigen Vorlage verwenden Sie die Befehle für die Ressourcengruppe. Im Folgenden finden Sie Beispiele für die Befehle [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) zum Starten der Bereitstellung in einem Ressourcenbereich.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-test.json -principalId $objectid -builtInRoleType Contributor
```

```azurecli
az group deployment create --resource-group ExampleGroup --template-file rbac-test.json --parameters principalId=$objectid builtInRoleType=Contributor
```

Das folgende Beispiel veranschaulicht die Zuweisung der Rolle „Mitwirkender“ zu einem Benutzer für ein Speicherkonto nach dem Bereitstellen der Vorlage.

![Rollenzuweisung im Ressourcenbereich](./media/role-assignments-template/role-assignment-template-resource.png)

### <a name="new-service-principal"></a>Neuer Dienstprinzipal

Wenn Sie einen neuen Dienstprinzipal erstellen und sofort versuchen, diesem eine Rolle zuzuweisen, kann die Rollenzuweisung in einigen Fällen fehlschlagen. Wenn Sie z. B. eine neue verwaltete Identität erstellen und dann versuchen, in derselben Azure Resource Manager-Vorlage dem Dienstprinzipal eine Rolle zuzuweisen, kann die Rollenzuweisung fehlschlagen. Der Grund für diesen Fehler ist wahrscheinlich eine Replikationsverzögerung. Der Dienstprinzipal wird in einer Region erstellt, die Rollenzuweisung kann aber in einer anderen Region stattfinden, in die der Dienstprinzipal noch nicht repliziert wurde.

Um dieses Szenario zu beheben, sollten Sie beim Erstellen der Rollenzuweisung die `principalType`-Eigenschaft auf `ServicePrincipal` festlegen. Sie müssen auch die `apiVersion` der Rollenzuweisung auf Version `2018-09-01-preview` oder höher festlegen.

Die folgende Vorlage veranschaulicht Folgendes:

- Erstellen eines neuen Dienstprinzipals für verwaltete Identitäten
- Angeben des `principalType`
- Zuweisen der Rolle „Mitwirkender“ zu dem Dienstprinzipal in einem Ressourcengruppenbereich

Um die Vorlage zu verwenden, müssen Sie Folgendes eingeben:

- Den Basisnamen der verwalteten Identität, oder Sie können die Standardzeichenfolge verwenden.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "baseName": {
            "type": "string",
            "defaultValue": "msi-test"
        }
    },
    "variables": {
        "identityName": "[concat(parameters('baseName'), '-bootstrap')]",
        "bootstrapRoleAssignmentId": "[guid(concat(resourceGroup().id, 'contributor'))]",
        "contributorRoleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]"
    },
    "resources": [
        {
            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
            "name": "[variables('identityName')]",
            "apiVersion": "2018-11-30",
            "location": "[resourceGroup().location]"
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[variables('bootstrapRoleAssignmentId')]",
            "dependsOn": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
            ],
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionId')]",
                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName')), '2018-11-30').principalId]",
                "scope": "[resourceGroup().id]",
                "principalType": "ServicePrincipal"
            }
        }
    ]
}
```

Im Folgenden finden Sie Beispiele für die Befehle [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) zum Starten der Bereitstellung in einem Ressourcengruppenbereich.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup2 -TemplateFile rbac-test.json
```

```azurecli
az group deployment create --resource-group ExampleGroup2 --template-file rbac-test.json
```

Das folgende Beispiel veranschaulicht die Zuweisung der Rolle „Mitwirkender“ zu einem neuen Dienstprinzipal für verwaltete Identitäten nach dem Bereitstellen der Vorlage.

![Rollenzuweisung für einen neuen Dienstprinzipal für verwaltete Identitäten](./media/role-assignments-template/role-assignment-template-msi.png)

## <a name="remove-a-role-assignment"></a>Entfernen einer Rollenzuweisung

Wenn Sie in Azure RBAC Zugriff auf eine Azure-Ressource entfernen möchten, entfernen Sie die Rollenzuweisung. Es gibt keine Möglichkeit, eine Rollenzuweisung mithilfe einer Vorlage zu entfernen. Zum Entfernen einer Rollenzuweisung müssen Sie andere Tools verwenden, z. B.:

- [Azure portal](role-assignments-portal.md#remove-a-role-assignment)
- [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)
- [Azure-Befehlszeilenschnittstelle](role-assignments-cli.md#remove-a-role-assignment)
- [REST-API](role-assignments-rest.md#remove-a-role-assignment)

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)
- [Grundlegendes zur Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md)
- [Erstellen von Ressourcengruppen und Ressourcen auf Abonnementebene](../azure-resource-manager/templates/deploy-to-subscription.md)
- [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?term=rbac)
