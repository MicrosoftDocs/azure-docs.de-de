---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/11/2019
ms.author: tomfitz
ms.openlocfilehash: 104bd13a0cf97a8605670adde479c2a2eeb29c15
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178473"
---
Um eine Ressource während der Bereitstellung mit einem Tag zu versehen, fügen Sie der Ressource, die Sie bereitstellen, das `tags`-Element hinzu. Geben Sie den Namen und den Wert des Tags an.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Anwenden eines literalen Werts auf den Tagnamen
Das folgende Beispiel zeigt ein Speicherkonto mit zwei Tags (`Dept` und `Environment`), die auf literale Werte festgelegt sind:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Verwenden Sie zum Festlegen eines Tags für einen datetime-Wert die [utcNow-Funktion](../articles/azure-resource-manager/resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Anwenden eines Objekts auf das Tagelement
Sie können einen Objektparameter definieren, der mehrere Tags speichert, und dieses Objekt auf das Tagelement anwenden. Jede Eigenschaft in dem Objekt wird zu einem separaten Tag für die Ressource. Das folgende Beispiel enthält einen Parameter namens `tagValues`, der auf das Tagelement angewendet wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Anwenden einer JSON-Zeichenfolge auf den Tagnamen

Wenn Sie mehrere Werte in einem einzelnen Tag speichern möchten, wenden Sie eine JSON-Zeichenfolge an, die die gewünschten Werte darstellt. Die gesamte JSON-Zeichenfolge wird als einzelnes Tag gespeichert und darf maximal 256 Zeichen lang sein. Im folgenden Beispiel gibt es ein einzelnes Tag namens `CostCenter`, das mehrere Werte aus einer JSON-Zeichenfolge enthält:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```