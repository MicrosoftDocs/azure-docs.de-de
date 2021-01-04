---
title: Bereitstellen von Ressourcen in Ressourcengruppen
description: In diesem Artikel wird beschrieben, wie Sie Ressourcen in einer Azure Resource Manager-Vorlage bereitstellen. Es wird gezeigt, wie Sie mehrere Ressourcengruppen als Ziel festlegen.
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 9d0bec51fa55ee377eb647a11fb554ec3b81e9eb
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95807722"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Bereitstellung von Ressourcengruppen mit ARM-Vorlagen

In diesem Artikel wird beschrieben, wie Sie eine Bereitstellung auf eine Ressourcengruppe beschränken. Dabei verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) für die Bereitstellung. Außerdem wird erläutert, wie Sie den Bereich während des Bereitstellungsvorgangs über die Ressourcengruppe hinaus erweitern.

## <a name="supported-resources"></a>Unterstützte Ressourcen

Die meisten Ressourcen können in einer Ressourcengruppe bereitgestellt werden. Eine Liste der verfügbaren Ressourcen finden Sie unter [Definieren von Ressourcen in Azure Resource Manager-Vorlagen](/azure/templates).

## <a name="schema"></a>Schema

Verwenden Sie für Vorlagen das folgende Schema:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Verwenden Sie für Parameterdateien Folgendes:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Bereitstellungsbefehle

Verwenden Sie für die Bereitstellung in einer Ressourcengruppe die Ressourcengruppen-Bereitstellungsbefehle.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie für die Azure CLI [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create). Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie als Bereitstellungsbefehl in PowerShell [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Das folgende Beispiel stellt eine Vorlage zum Erstellen einer Ressourcengruppe bereit:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Ausführlichere Informationen über Bereitstellungsbefehle und -optionen für die Bereitstellung von ARM-Vorlagen finden Sie in den folgenden Artikeln:

* [Bereitstellen von Ressourcen mit ARM-Vorlagen und dem Azure-Portal](deploy-portal.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Azure CLI](deploy-cli.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md)
* [Bereitstellen von Ressourcen mit ARM-Vorlagen und der Resource Manager-REST-API](deploy-rest.md)
* [Verwenden einer Bereitstellungsschaltfläche zum Bereitstellen von Vorlagen aus einem GitHub-Repository](deploy-to-azure-button.md)
* [Bereitstellen von ARM-Vorlagen über Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Bereitstellungsbereiche

Bei der Bereitstellung in einer Ressourcengruppe können Sie Ressourcen an folgenden Orten bereitstellen:

* In der Zielressourcengruppe des Vorgangs
* In anderen Ressourcengruppen im selben Abonnement oder in anderen Abonnements
* in einem beliebigen Abonnement im Mandanten
* im Mandanten für die Ressourcengruppe
* [Erweiterungsressourcen](scope-extension-resources.md) können auf Ressourcen angewendet werden.

Der Benutzer, der die Vorlage bereitstellt, muss Zugriff auf den angegebenen Bereich besitzen.

In diesem Abschnitt wird das Festlegen verschiedener Bereiche veranschaulicht. Sie können diese verschiedenen Bereiche in einer Vorlage kombinieren.

### <a name="scope-to-target-resource-group"></a>Bereich: Zielressourcengruppe

Zum Bereitstellen von Ressourcen in der Zielressourcengruppe fügen Sie die Ressourcen zum Ressourcenabschnitt der Vorlage hinzu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

Eine Beispielvorlage finden Sie unter [Bereitstellen in der Zielressourcengruppe](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Bereich: Ressourcengruppe im selben Abonnement

Zum Bereitstellen von Ressourcen in einer anderen Ressourcengruppe im selben Abonnement fügen Sie eine geschachtelte Bereitstellung hinzu und verwenden dabei die Eigenschaft `resourceGroup`. Ohne Angabe von Abonnement-ID oder Ressourcengruppe werden das Abonnement und die Ressourcengruppe aus der übergeordneten Vorlage verwendet. Alle Ressourcengruppen müssen vorhanden sein, bevor Sie die Bereitstellung ausführen.

In dem folgenden Beispiel verwendet die geschachtelte Bereitstellung eine Ressourcengruppe namens `demoResourceGroup` als Ziel.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

Eine Beispielvorlage finden Sie unter [Bereitstellen in mehreren Ressourcengruppen](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Bereich: Ressourcengruppe in einem anderen Abonnement

Zum Bereitstellen von Ressourcen in einer Ressourcengruppe in einem anderen Abonnement fügen Sie eine geschachtelte Bereitstellung hinzu und verwenden dabei die Eigenschaften `subscriptionId` und `resourceGroup`. In dem folgenden Beispiel verwendet die geschachtelte Bereitstellung eine Ressourcengruppe namens `demoResourceGroup` als Ziel.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

Eine Beispielvorlage finden Sie unter [Bereitstellen in mehreren Ressourcengruppen](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Bereich: Abonnement

Fügen Sie zum Bereitstellen von Ressourcen in einem Abonnement eine geschachtelte Bereitstellung hinzu, und schließen Sie dabei die `subscriptionId`-Eigenschaft ein. Das Abonnement kann das Abonnement für die Zielressourcengruppe oder ein anderes Abonnement im Mandanten sein. Legen Sie außerdem die `location`-Eigenschaft für die geschachtelte Bereitstellung fest.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

Eine Beispielvorlage finden Sie unter [Erstellen der Ressourcengruppe](#create-resource-group).

### <a name="scope-to-tenant"></a>Bereich: Mandant

Sie können Ressourcen im Mandanten erstellen, indem Sie den `scope` auf `/` festlegen. Der Benutzer, der die Vorlage bereitstellt, muss über den [erforderlichen Zugriff zum Bereitstellen im Mandanten](deploy-to-tenant.md#required-access) verfügen.

Sie können eine geschachtelte Bereitstellung mit festgelegtem `scope` und `location` verwenden.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Alternativ können Sie den Bereich für einige Ressourcentypen wie z. B. Verwaltungsgruppen auf `/` festlegen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

## <a name="deploy-to-target-resource-group"></a>Bereitstellen in der Zielressourcengruppe

Zum Bereitstellen von Ressourcen in der Zielressourcengruppe definieren Sie die Ressourcen im Abschnitt **Ressourcen** der Vorlage. Mit der folgenden Vorlage wird ein Speicherkonto in der Ressourcengruppe erstellt, die im Bereitstellungsvorgang angegeben ist.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Bereitstellen in mehreren Ressourcengruppen

Sie können in einer einzelnen ARM-Vorlage Ressourcen in mehr als einer Ressourcengruppe bereitstellen. Zum Festlegen einer Ressourcengruppe, die nicht die der übergeordneten Vorlage ist, verwenden Sie eine [geschachtelte oder verknüpfte Vorlage](linked-templates.md). Geben Sie innerhalb des Bereitstellungsressourcentyps Werte für die Abonnement-ID und die Ressourcengruppe an, für die Sie die geschachtelte Vorlage bereitstellen möchten. Die Ressourcengruppen können sich in unterschiedlichen Abonnements befinden.

> [!NOTE]
> Sie können mit einer einzigen Bereitstellung in **800 Ressourcengruppen** bereitstellen. Normalerweise bedeutet diese Einschränkung, dass Sie die Bereitstellung für eine Ressourcengruppe durchführen können, die für die übergeordnete Vorlage angegeben ist, sowie für bis zu 799 Ressourcengruppen in geschachtelten oder verknüpften Bereitstellungen. Wenn Ihre übergeordnete Vorlage aber nur geschachtelte oder verknüpfte Vorlagen enthält und selbst keine Ressourcen bereitstellt, können Sie bis zu 800 Ressourcengruppen in geschachtelte oder verknüpfte Bereitstellungen einbinden.

Im folgenden Beispiel werden zwei Speicherkonten bereitgestellt. Das erste Speicherkonto wird in der Ressourcengruppe bereitgestellt, die Sie im Bereitstellungsvorgang angegeben haben. Das zweite Speicherkonto wird in der Ressourcengruppe bereitgestellt, die in den Parametern `secondResourceGroup` und `secondSubscriptionID` angegeben wurde:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Wenn Sie für `resourceGroup` den Namen einer nicht vorhandenen Ressourcengruppe angeben, tritt bei der Bereitstellung ein Fehler auf.

Verwenden Sie PowerShell oder die Azure CLI, um die vorherige Vorlage zu testen und die Ergebnisse anzuzeigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie zum Bereitstellen von zwei Speicherkonten in zwei Ressourcengruppen im **gleichen Abonnement** Folgendes:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Verwenden Sie zum Bereitstellen von zwei Speicherkonten in **zwei Abonnements** Folgendes:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Bereitstellen von zwei Speicherkonten in zwei Ressourcengruppen im **gleichen Abonnement** Folgendes:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Verwenden Sie zum Bereitstellen von zwei Speicherkonten in **zwei Abonnements** Folgendes:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>Ressourcengruppe erstellen

Sie können von der Bereitstellung einer Ressourcengruppe auf Abonnementebene wechseln und eine Ressourcengruppe erstellen. Die folgende Vorlage stellt ein Speicherkonto in der Zielressourcengruppe bereit und erstellt eine neue Ressourcengruppe im angegebenen Abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nächste Schritte

* Unter [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json) finden Sie ein Beispiel für die Bereitstellung von Arbeitsbereichseinstellungen für Azure Security Center.
