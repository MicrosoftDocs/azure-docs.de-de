---
title: Bereitstellen von Ressourcen in einer Verwaltungsgruppe
description: In diesem Artikel wird beschrieben, wie Sie Ressourcen auf der Verwaltungsgruppenebene in einer Azure Resource Manager-Vorlage bereitstellen.
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 79cdb35de40501dfc0794155dcf807cced94bfa7
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95798588"
---
# <a name="management-group-deployments-with-arm-templates"></a>Bereitstellung von Verwaltungsgruppen mit ARM-Vorlagen

Wenn Ihre Organisation sich weiterentwickelt, können Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) bereitstellen, um Ressourcen auf Verwaltungsgruppenebene zu erstellen. Beispielsweise müssen Sie möglicherweise [Richtlinien](../../governance/policy/overview.md) oder die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure-RBAC)](../../role-based-access-control/overview.md) für eine Verwaltungsgruppe definieren und zuweisen. Mit Vorlagen auf Verwaltungsgruppenebene können Sie Richtlinien deklarativ anwenden und Rollen auf Verwaltungsgruppenebene zuweisen.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Nicht alle Ressourcentypen können auf Mandantengruppenebene bereitgestellt werden. Im folgenden Abschnitt werden die unterstützten Ressourcentypen aufgelistet.

Verwenden Sie für Azure Blueprints:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

Verwenden Sie für Azure-Richtlinien:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Verwenden Sie für rollenbasierte Zugriffssteuerung von Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Verwenden Sie für geschachtelte Vorlagen, die in Abonnements oder Ressourcengruppen bereitstellen:

* [Bereitstellungen](/azure/templates/microsoft.resources/deployments)

Verwenden Sie für die Verwaltung Ihrer Ressourcen:

* [Tags](/azure/templates/microsoft.resources/tags)

## <a name="schema"></a>Schema

Das Schema, das Sie für Bereitstellungen auf Verwaltungsgruppenebene verwenden, unterscheidet sich von dem Schema für Ressourcengruppenbereitstellungen.

Verwenden Sie für Vorlagen Folgendes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    ...
}
```

Das Schema für eine Parameterdatei ist für alle Bereitstellungsbereiche identisch. Verwenden Sie für Parameterdateien Folgendes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Bereitstellungsbefehle

Verwenden Sie für die Bereitstellung in einer Verwaltungsgruppe die Verwaltungsgruppen-Bereitstellungsbefehle.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie bei der Azure CLI [az deployment mg create](/cli/azure/deployment/mg#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie für Azure PowerShell [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Ausführlichere Informationen über Bereitstellungsbefehle und -optionen für die Bereitstellung von ARM-Vorlagen finden Sie in den folgenden Artikeln:

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](deploy-portal.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Resource Manager-REST-API](deploy-rest.md)
* [Verwenden einer Bereitstellungsschaltfläche zum Bereitstellen von Vorlagen aus einem GitHub-Repository](deploy-to-azure-button.md)
* [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Bereitstellungsspeicherort und -name

Für Bereitstellungen auf Verwaltungsgruppenebene müssen Sie einen Speicherort für die Bereitstellung angeben. Der Speicherort der Bereitstellung ist vom Speicherort der Ressourcen getrennt, die Sie bereitstellen. Der Bereitstellungsspeicherort gibt an, wo Bereitstellungsdaten gespeichert werden sollen. Die Bereitstellungen von [Abonnement](deploy-to-subscription.md) und [Mandant](deploy-to-tenant.md) benötigen auch einen Speicherort. Für [Ressourcengruppe](deploy-to-resource-group.md)nbereitstellungen wird der Speicherort der Ressourcengruppe zum Speichern der Bereitstellungsdaten verwendet.

Sie können einen Namen für die Bereitstellung angeben oder den Bereitstellungsstandardnamen verwenden. Der Standardname ist der Name der Vorlagendatei. Wenn Sie z.B. eine Vorlage mit dem Namen **azuredeploy.json** bereitstellen, wird **azuredeploy** als Standardname für die Bereitstellung erstellt.

Der Speicherort für jeden Bereitstellungsnamen ist unveränderlich. Sie können keine Bereitstellung an einem Speicherort erstellen, wenn bereits eine Bereitstellung mit demselben Namen an einem anderen Speicherort vorhanden ist. Wenn Sie z. B. eine Verwaltungsgruppenbereitstellung mit dem Namen **deployment1** in **centralus** erstellen, können Sie später keine weitere Bereitstellung mit dem Namen **deployment1**, aber einen Speicherort **westus** erstellen. Wenn Sie den Fehlercode `InvalidDeploymentLocation` erhalten, verwenden Sie entweder einen anderen Namen oder denselben Speicherort wie bei der vorherigen Bereitstellung für diesen Namen.

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung in einer Verwaltungsgruppe können Sie Ressourcen an folgenden Orten bereitstellen:

* In der Zielverwaltungsgruppe des Vorgangs
* In einer anderen Verwaltungsgruppe im Mandanten
* In Abonnements in der Verwaltungsgruppe
* In Ressourcengruppen in der Verwaltungsgruppe
* Im Mandanten für die Ressourcengruppe
* [Erweiterungsressourcen](scope-extension-resources.md) können auf Ressourcen angewendet werden.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

In diesem Abschnitt wird das Festlegen verschiedener Bereiche veranschaulicht. Sie können diese verschiedenen Bereiche in einer Vorlage kombinieren.

### <a name="scope-to-target-management-group"></a>Bereich: Zielverwaltungsgruppe

Ressourcen, die im Ressourcenabschnitt der Vorlage definiert sind, werden vom Bereitstellungsbefehl auf die Verwaltungsgruppe angewendet.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

### <a name="scope-to-another-management-group"></a>Bereich: andere Verwaltungsgruppe

Um eine andere Verwaltungsgruppe als Ziel zu verwenden, fügen Sie eine geschachtelte Bereitstellung hinzu, und geben Sie die `scope`-Eigenschaft an. Legen Sie die Eigenschaft `scope` auf einen Wert im Format `Microsoft.Management/managementGroups/<mg-name>` fest.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Bereich: Abonnement

Sie können auch Abonnements innerhalb einer Verwaltungsgruppe als Bereitstellungsziel verwenden. Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

Um ein Abonnement innerhalb der Verwaltungsgruppe als Ziel zu verwenden, verwenden Sie eine geschachtelte Bereitstellung und die `subscriptionId`-Eigenschaft.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Bereich: Ressourcengruppe

Sie können auch Ressourcengruppen innerhalb der Verwaltungsgruppe als Bereitstellungsziel verwenden. Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

Um eine Ressourcengruppe innerhalb der Verwaltungsgruppe als Bereitstellungsziel zu verwenden, verwenden Sie eine geschachtelte Bereitstellung. Legen Sie die Eigenschaften `subscriptionId` und `resourceGroup` fest. Legen Sie keinen Speicherort für die geschachtelte Bereitstellung fest, da Sie am Speicherort der Ressourcengruppe bereitgestellt wird.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="9,10,18":::

Informationen zur Verwendung einer Verwaltungsgruppenbereitstellung zum Erstellen einer Ressourcengruppe in einem Abonnement und zum Bereitstellen eines Speicherkontos für diese Ressourcengruppe finden Sie unter [Bereitstellen in Abonnements und Ressourcengruppen](#deploy-to-subscription-and-resource-group).

### <a name="scope-to-tenant"></a>Bereich: Mandant

Sie können Ressourcen im Mandanten erstellen, indem Sie den `scope` auf `/` festlegen. Der Benutzer, der die Vorlage bereitstellt, muss über den [erforderlichen Zugriff zum Bereitstellen im Mandanten](deploy-to-tenant.md#required-access) verfügen.

Sie können eine geschachtelte Bereitstellung mit festgelegtem `scope` und `location` verwenden.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-to-tenant.json" highlight="9,10,14":::

Alternativ können Sie den Bereich für einige Ressourcentypen wie z. B. Verwaltungsgruppen auf `/` festlegen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/management-group-create-mg.json" highlight="12,15":::

## <a name="azure-policy"></a>Azure Policy

Benutzerdefinierte Richtliniendefinitionen, die für die Verwaltungsgruppe bereitgestellt werden, sind Erweiterungen der Verwaltungsgruppe. Verwenden Sie die Funktion [extensionResourceId()](template-functions-resource.md#extensionresourceid), um die ID einer benutzerdefinierten Richtliniendefinition abzurufen. Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Verwenden Sie die Funktion [tenantResourceId](template-functions-resource.md#tenantresourceid), um die ID einer integrierten Richtliniendefinition abzurufen.

Im folgenden Beispiel wird veranschaulicht, wie eine Richtlinie auf der Verwaltungsgruppenebene [definiert](../../governance/policy/concepts/definition-structure.md) und zugewiesen wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
            }
        }
    ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Bereitstellen in Abonnements und Ressourcengruppen

Aus einer Bereitstellung auf Verwaltungsgruppenebene können Sie ein Abonnement innerhalb der Verwaltungsgruppe als Ziel verwenden. Das folgende Beispiel erstellt eine Ressourcengruppe innerhalb eines Abonnements und stellt in dieser Ressourcengruppe ein Speicherkonto bereit.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "nestedsubId": {
            "type": "string"
        },
        "nestedRG": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        },
        "nestedLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedSub",
            "location": "[parameters('nestedLocation')]",
            "subscriptionId": "[parameters('nestedSubId')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                    },
                    "variables": {
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('nestedRG')]",
                            "location": "[parameters('nestedLocation')]"
                        }
                    ]
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "nestedRG",
            "subscriptionId": "[parameters('nestedSubId')]",
            "resourceGroup": "[parameters('nestedRG')]",
            "dependsOn": [
                "nestedSub"
            ],
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[parameters('storageAccountName')]",
                            "location": "[parameters('nestedLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ]
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Zuweisen von Rollen finden Sie unter [Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md).
* Unter [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) finden Sie ein Beispiel für die Bereitstellung von Arbeitsbereichseinstellungen für Azure Security Center.
* Sie können Vorlagen auch auf [Abonnementebene](deploy-to-subscription.md) und [Mandantenebene](deploy-to-tenant.md) bereitstellen.
