---
title: Bereitstellen von Ressourcen in einem Abonnement
description: In diesem Artikel wird beschrieben, wie Sie eine Ressourcengruppe in einer Azure Resource Manager-Vorlage erstellen. Außerdem wird veranschaulicht, wie Sie Ressourcen für den Bereich des Azure-Abonnements bereitstellen.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: b5c99e5dc21c2b93f1c9da3977302a2dd311277f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491502"
---
# <a name="subscription-deployments-with-arm-templates"></a>Abonnementbereitstellungen mit ARM-Vorlagen

Um die Verwaltung von Ressourcen zu vereinfachen, können Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um Ressourcen auf der Ebene Ihres Azure-Abonnements bereitzustellen. Beispielsweise können Sie [Richtlinien](../../governance/policy/overview.md) und [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md) für Ihr Abonnement bereitstellen, wodurch diese dann in Ihrem gesamten Abonnement angewendet wird. Außerdem können Sie Ressourcengruppen innerhalb des Abonnements erstellen und Ressourcen in Ressourcengruppen im Abonnement bereitstellen.

> [!NOTE]
> In einer Bereitstellung auf Abonnementebene können Sie in 800 verschiedenen Ressourcengruppen bereitstellen.

Um Vorlagen auf Abonnementebene bereitzustellen, verwenden Sie die Azure CLI, PowerShell, die REST-API oder das Portal.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Nicht alle Ressourcentypen können auf Abonnementebene bereitgestellt werden. Im folgenden Abschnitt werden die unterstützten Ressourcentypen aufgelistet.

Verwenden Sie für Azure Blueprints:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [Versionen (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Verwenden Sie für Azure-Richtlinien:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Verwenden Sie für rollenbasierte Zugriffssteuerung von Azure (Azure RBAC):

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Verwenden Sie für geschachtelte Vorlagen, die in Ressourcengruppen bereitstellen:

* [Bereitstellungen](/azure/templates/microsoft.resources/deployments)

Verwenden Sie zum Erstellen neuer Ressourcengruppen:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Verwenden Sie zum Verwalten Ihres Abonnements:

* [Advisor-Konfigurationen](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [Anwendungsänderungsanalyseprofil](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [Tags](/azure/templates/microsoft.resources/tags)

Andere unterstützte Typen sind unter anderem:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="schema"></a>Schema

Das Schema, das Sie für Bereitstellungen auf Abonnementebene verwenden, unterscheidet sich von dem Schema für Ressourcengruppenbereitstellungen.

Verwenden Sie für Vorlagen Folgendes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
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

Verwenden Sie die Bereitstellungsbefehle auf Abonnementbereich für die Bereitstellung in einem Abonnement.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie für die Azure-Befehlszeilenschnittstelle [az deployment sub create](/cli/azure/deployment/sub#az-deployment-sub-create). Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie als PowerShell-Bereitstellungsbefehl [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) oder den zugehörigen Alias `New-AzSubscriptionDeployment`. Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json" `
  -rgName demoResourceGroup `
  -rgLocation centralus
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

Für Bereitstellungen auf Abonnementebene müssen Sie einen Speicherort für die Bereitstellung angeben. Der Speicherort der Bereitstellung ist vom Speicherort der Ressourcen getrennt, die Sie bereitstellen. Der Bereitstellungsspeicherort gibt an, wo Bereitstellungsdaten gespeichert werden sollen. Die Bereitstellungen von [Verwaltungsgruppe](deploy-to-management-group.md) und [Mandant](deploy-to-tenant.md) benötigen auch einen Speicherort. Für [Ressourcengruppe](deploy-to-resource-group.md)-Bereitstellungen wird der Speicherort der Ressourcengruppe zum Speichern der Bereitstellungsdaten verwendet.

Sie können einen Namen für die Bereitstellung angeben oder den Bereitstellungsstandardnamen verwenden. Der Standardname ist der Name der Vorlagendatei. Wenn Sie z.B. eine Vorlage mit dem Namen _azuredeploy.json_ bereitstellen, wird **azuredeploy** als Standardname für die Bereitstellung erstellt.

Der Speicherort für jeden Bereitstellungsnamen ist unveränderlich. Sie können keine Bereitstellung an einem Speicherort erstellen, wenn bereits eine Bereitstellung mit demselben Namen an einem anderen Speicherort vorhanden ist. Wenn Sie z. B. eine Abonnementbereitstellung mit dem Namen **deployment1** in **centralus** erstellen, können Sie später keine weitere Bereitstellung mit dem Namen **deployment1**, aber einen Speicherort **westus** erstellen. Wenn Sie den Fehlercode `InvalidDeploymentLocation` erhalten, verwenden Sie entweder einen anderen Namen oder denselben Speicherort wie bei der vorherigen Bereitstellung für diesen Namen.

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung in ein Abonnement können Sie Ressourcen an folgenden Orten bereitstellen:

* im Zielabonnement des Vorgangs
* in einem beliebigen Abonnement im Mandanten
* in anderen Ressourcengruppen im Abonnement oder in anderen Abonnements
* im Mandanten für das Abonnement

Für eine [Erweiterungsressource](scope-extension-resources.md) kann der Bereich auf ein Ziel festgelegt werden, das sich vom Bereitstellungsziel unterscheidet.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

In diesem Abschnitt wird das Festlegen verschiedener Bereiche veranschaulicht. Sie können diese verschiedenen Bereiche in einer Vorlage kombinieren.

### <a name="scope-to-target-subscription"></a>Bereitstellen im Zielabonnement

Zum Bereitstellen von Ressourcen im Zielabonnement fügen Sie die Ressourcen zum Ressourcenabschnitt der Vorlage hinzu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-sub.json" highlight="5":::

Beispiele für die Bereitstellung im Abonnement finden Sie unter [Erstellen von Ressourcengruppen](#create-resource-groups) und [Zuweisen von Richtliniendefinitionen](#assign-policy-definition).

### <a name="scope-to-other-subscription"></a>Bereitstellen in anderem Abonnement

Zum Bereitstellen von Ressourcen in einem anderen Abonnement als dem des Vorgangs fügen Sie eine geschachtelte Bereitstellung hinzu. Legen Sie `subscriptionId` auf die eindeutige ID des Abonnements fest, in dem Sie bereitstellen möchten. Legen Sie die `location`-Eigenschaft für die geschachtelte Bereitstellung fest.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-sub.json" highlight="9,10,14":::

### <a name="scope-to-resource-group"></a>Bereich: Ressourcengruppe

Zum Bereitstellen von Ressourcen in einer Ressourcengruppe im Abonnement fügen Sie eine geschachtelte Bereitstellung hinzu, und verwenden Sie dabei die Eigenschaft `resourceGroup`. In dem folgenden Beispiel verwendet die geschachtelte Bereitstellung eine Ressourcengruppe namens `demoResourceGroup` als Ziel.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/sub-to-resource-group.json" highlight="9,13":::

Ein Beispiel für die Bereitstellung in einer Ressourcengruppe finden Sie unter [Erstellen von Ressourcengruppen und Ressourcen](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Bereich: Mandant

Zum Erstellen von Ressourcen auf dem Mandanten legen Sie `scope` auf `/` fest. Der Benutzer, der die Vorlage bereitstellt, muss über den [zum Bereitstellen erforderlichen Zugriff auf dem Mandanten](deploy-to-tenant.md#required-access) verfügen.

Legen Sie `scope` und `location` fest, um eine geschachtelte Bereitstellung zu verwenden.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-to-tenant.json" highlight="9,10,14":::

Alternativ können Sie den Bereich für einige Ressourcentypen wie z. B. Verwaltungsgruppen auf `/` festlegen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/subscription-create-mg.json" highlight="12,15":::

Weitere Informationen finden Sie unter [Verwaltungsgruppe](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Ressourcengruppen

### <a name="create-resource-groups"></a>Erstellen von Ressourcengruppe

Um eine Ressourcengruppe in einer ARM-Vorlage zu erstellen, definieren Sie eine [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions)-Ressource mit einem Namen und einem Speicherort für die Ressourcengruppe.

Mit der folgenden Vorlage wird eine leere Ressourcengruppe erstellt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Verwenden Sie das [copy-Element](copy-resources.md) mit Ressourcengruppen, um mehrere Ressourcengruppen erstellen zu können.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgNamePrefix": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "instanceCount": {
      "type": "int"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "location": "[parameters('rgLocation')]",
      "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
      "copy": {
        "name": "rgCopy",
        "count": "[parameters('instanceCount')]"
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Weitere Informationen zu Ressourceniterationen finden Sie unter [Ressourceniterationen in ARM-Vorlagen](./copy-resources.md) und im [Tutorial: Erstellen mehrerer Ressourceninstanzen mit ARM-Vorlagen](./template-tutorial-create-multiple-instances.md).

### <a name="create-resource-group-and-resources"></a>Erstellen einer Ressourcengruppen und von Ressourcen

Verwenden Sie zum Erstellen der Ressourcengruppe und Bereitstellen von Ressourcen für diese eine geschachtelte Vorlage. Die geschachtelte Vorlage definiert die Ressourcen, die für die Ressourcengruppe bereitgestellt werden sollen. Legen Sie die geschachtelte Vorlage vor der Ressourcenbereitstellung als von der Ressourcengruppe abhängig fest, um sicherzustellen, dass die Ressourcengruppe vorhanden ist. Sie können in bis zu 800 Ressourcengruppen bereitstellen.

Das folgende Beispiel erstellt eine Ressourcengruppe und stellt ein Speicherkonto in der Ressourcengruppe bereit.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    },
    "storagePrefix": {
      "type": "string",
      "maxLength": 11
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-06-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-06-01",
              "name": "[variables('storageName')]",
              "location": "[parameters('rgLocation')]",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Zuweisen einer Richtliniendefinition

Im folgenden Beispiel wird dem Abonnement eine vorhandene Richtliniendefinition zugewiesen. Wenn die Richtliniendefinition Parameter unterstützt, stellen Sie diese als Objekt bereit. Wenn die Richtliniendefinition keine Parameter unterstützt, verwenden Sie das standardmäßige leere Objekt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

Stellen Sie diese Vorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
# Built-in policy definition that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Um diese Vorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzSubscriptionDeployment `
  -Name policyassign `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json" `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

### <a name="create-and-assign-policy-definitions"></a>Erstellen und Zuweisen von Richtliniendefinitionen

Sie können eine Richtliniendefinition in derselben Vorlage [definieren](../../governance/policy/concepts/definition-structure.md) und zuweisen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    },
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-05-01",
      "name": "location-lock",
      "dependsOn": [
        "locationpolicy"
      ],
      "properties": {
        "scope": "[subscription().id]",
        "policyDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
      }
    }
  ]
}
```

Mit dem folgenden CLI-Befehl können Sie die Richtliniendefinition im Abonnement erstellen und auf dem Abonnement zuweisen:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

Um diese Vorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name definePolicy `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json"
```

## <a name="azure-blueprints"></a>Azure Blueprint

### <a name="create-blueprint-definition"></a>Erstellen einer Blaupausendefinition

Sie können eine Blaupausendefinition aus einer Vorlage [erstellen](../../governance/blueprints/tutorials/create-from-sample.md).

:::code language="json" source="~/quickstart-templates/subscription-deployments/blueprints-new-blueprint/azuredeploy.json":::

Mit dem folgenden CLI-Befehl können Sie die Blaupausendefinition in Ihrem Abonnement erstellen:

```azurecli
az deployment sub create \
  --name demoDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

Um diese Vorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell
New-AzSubscriptionDeployment `
  -Name demoDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/blueprints-new-blueprint/azuredeploy.json"
```

## <a name="access-control"></a>Zugriffssteuerung

Informationen zum Zuweisen von Rollen finden Sie unter [Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen](../../role-based-access-control/role-assignments-template.md).

Im folgenden Beispiel wird eine Ressourcengruppe erstellt, eine Sperre darauf angewendet und einem Prinzipal eine Rolle zugewiesen.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-rg-lock-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Nächste Schritte

* Unter [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) finden Sie ein Beispiel für die Bereitstellung von Arbeitsbereichseinstellungen für Azure Security Center.
* Ähnliche Beispielvorlagen sind auf [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments) verfügbar.
* Sie können Vorlagen auch auf [Verwaltungsgruppenebene](deploy-to-management-group.md) und [Mandantenebene](deploy-to-tenant.md) bereitstellen.
