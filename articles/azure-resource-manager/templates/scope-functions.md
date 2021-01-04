---
title: Vorlagenfunktionen in bereichsbezogenen Bereitstellungen
description: In diesem Artikel wird beschrieben, wie Vorlagenfunktionen in bereichsbezogenen Bereitstellungen aufgelöst werden. Bei dem Bereich kann es sich um einen Mandanten, Verwaltungsgruppen, Abonnements und Ressourcengruppen handeln.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: cb02a3dc808604a80fd9943138c1cd0d8648904e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681311"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>Azure Resource Manager-Vorlagenfunktionen in Bereitstellungsbereichen

Mit Azure Resource Manager-Vorlagen (ARM-Vorlagen) können Sie Ressourcen in Ressourcengruppen, Abonnements, Verwaltungsgruppen oder Mandanten bereitstellen. Allgemein funktionieren [ARM-Vorlagenfunktionen](template-functions.md) für alle Bereiche gleich. In diesem Artikel werden die Unterschiede beschrieben, die bei einigen Funktionen je nach Bereich bestehen.

## <a name="supported-functions"></a>Unterstützte Funktionen

Beim Bereitstellen in verschiedenen Bereichen sind einige wichtige Aspekte zu beachten:

* Die Funktion [resourceGroup()](template-functions-resource.md#resourcegroup) wird für Bereitstellungen in Ressourcengruppen **unterstützt**.
* Die Funktion [subscription()](template-functions-resource.md#subscription) wird für Bereitstellungen in Ressourcengruppen und Abonnements **unterstützt**.
* Die Funktionen [reference()](template-functions-resource.md#reference) und [list()](template-functions-resource.md#list) werden für alle Bereiche **unterstützt**.
* Verwenden Sie [resourceId()](template-functions-resource.md#resourceid), um die ID einer Ressource abzurufen, die in der Ressourcengruppe bereitgestellt wurde.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Verwenden Sie die Funktion [subscriptionResourceId()](template-functions-resource.md#subscriptionresourceid), um die ID einer Ressource abzurufen, die im Abonnement bereitgestellt wurde.

  Verwenden Sie beispielsweise Folgendes, um die Ressourcen-ID für eine Richtliniendefinition abzurufen, die für ein Abonnement bereitgestellt wird:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Verwenden Sie die Funktion [extensionResourceId()](template-functions-resource.md#extensionresourceid) für Ressourcen, die als Erweiterungen der Verwaltungsgruppe implementiert sind. Benutzerdefinierte Richtliniendefinitionen, die für die Verwaltungsgruppe bereitgestellt werden, sind Erweiterungen der Verwaltungsgruppe.

  Verwenden Sie den folgenden Code, um die Ressourcen-ID für eine benutzerdefinierte Richtliniendefinition auf Verwaltungsgruppenebene zu erhalten:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Verwenden Sie die Funktion [tenantResourceId](template-functions-resource.md#tenantresourceid), um die ID einer Ressource abzurufen, die im Mandanten bereitgestellt wurde. Integrierte Richtliniendefinitionen sind Ressourcen auf Mandantenebene. Wenn Sie eine integrierte Richtlinie auf der Verwaltungsgruppenebene zuweisen, verwenden Sie die Funktion tenantResourceId.

  Verwenden Sie beispielsweise den folgenden Code, um die Ressourcen-ID für eine integrierte Richtliniendefinition abzurufen:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Funktionsauflösung in Bereichen

Bei der Bereitstellung in mehreren Bereichen werden die Funktionen [resouceGroup()](template-functions-resource.md#resourcegroup) und [subscription()](template-functions-resource.md#subscription) unterschiedlich aufgelöst, je nachdem, wie Sie die Vorlage festlegen. Wenn Sie auf eine externe Vorlage verweisen, werden die Funktionen immer im Bereich dieser Vorlage aufgelöst. Wenn Sie eine Vorlage in einer übergeordneten Vorlage schachteln, geben Sie mit der Eigenschaft `expressionEvaluationOptions` an, ob die Funktionen in die Ressourcengruppe und das Abonnement für die übergeordnete Vorlage oder die geschachtelte Vorlage aufgelöst werden. Legen Sie die Eigenschaft auf `inner` fest, um in den Bereich für die geschachtelte Vorlage aufzulösen. Legen Sie die Eigenschaft auf `outer` fest, um in den Bereich für die übergeordnete Vorlage aufzulösen.

Die folgende Tabelle zeigt, ob die Funktionen in die übergeordnete oder eingebettete Ressourcengruppe und das zugehörige Abonnement aufgelöst werden.

| Vorlagentyp | `Scope` | Lösung |
| ------------- | ----- | ---------- |
| Geschachtelt        | Außen (Standard) | Übergeordnete Ressourcengruppe |
| Geschachtelt        | Innerer Join | Unterressourcengruppe |
| Verknüpft        | –   | Unterressourcengruppe |

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) zeigt Folgendes:

* eine geschachtelte Vorlage mit Standardbereich (außen)
* geschachtelte Vorlage mit innerem Bereich
* verknüpfte Vorlage

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Verwenden Sie PowerShell oder die Azure CLI, um die vorherige Vorlage zu testen und die Ergebnisse anzuzeigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Nächste Schritte

* Um zu verstehen, wie Parameter in der Vorlage definiert werden, lesen Sie [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](template-syntax.md).
* Tipps zum Beheben gängiger Azure-Bereitstellungsfehler finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](common-deployment-errors.md).
* Informationen zum Bereitstellen einer Vorlage, die ein SAS-Token erfordert, finden Sie unter [Bereitstellen einer privaten Vorlage mit SAS-Token](secure-template-with-sas-token.md).
