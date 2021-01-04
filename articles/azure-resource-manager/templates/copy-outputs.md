---
title: Definieren mehrerer Instanzen eines Ausgabewerts
description: Verwenden des copy-Vorgangs in einer Azure Resource Manager-Vorlage (ARM), um das Zurückgeben eines Werts aus einer Bereitstellung mehrere Male zu durchlaufen.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 5ae20ed9ec3fdb3b76adbd370f5ba22f9386d613
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905943"
---
# <a name="output-iteration-in-arm-templates"></a>Ausgabeiteration in ARM-Vorlagen

In diesem Artikel wird gezeigt, wie Sie mehr als einen Wert für eine Ausgabe in Ihrer Azure Resource Manager-Vorlage (ARM) erstellen können. Durch das Hinzufügen des `copy`-Elements zum Ausgabenabschnitt Ihrer Vorlage können Sie eine Anzahl von Elementen während der Bereitstellung dynamisch zurückgeben.

Sie können „copy“ auch mit [Ressourcen](copy-resources.md), [Eigenschaften in einer Ressource](copy-properties.md) und [Variablen](copy-variables.md) verwenden.

## <a name="syntax"></a>Syntax

Das copy-Element hat das folgende allgemeine Format:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Die `count`-Eigenschaft gibt die gewünschte Anzahl von Iterationen für den Ausgabewert an.

Die Eigenschaft `input` gibt die Eigenschaften an, die Sie wiederholen möchten. Erstellen Sie ein Array von Elementen, das aus dem Wert in der `input`-Eigenschaft erstellt wird. Es kann sich um eine einzelne Eigenschaft handeln (z. B. eine Zeichenfolge) oder um ein Objekt mit mehreren Eigenschaften.

## <a name="copy-limits"></a>Einschränkungen für „copy“

Der Wert von „count“ darf 800 nicht überschreiten.

Der Wert von „count“ darf nicht negativ sein. Er kann Null sein, wenn Sie die Vorlage mit einer neueren Version von Azure CLI, PowerShell oder der REST-API bereitstellen. Insbesondere müssen Sie Folgendes verwenden:

* Azure PowerShell **2.6** oder höher
* Azure CLI **2.0.74** oder höher
* REST-API-Version **2019-05-10** oder höher
* [Verknüpfte Bereitstellungen](linked-templates.md) müssen API-Version **2019-05-10** oder höher für den Bereitstellungsressourcentyp verwenden.

Frühere Versionen von PowerShell, CLI und der REST-API unterstützen den Wert „0“ (null) für „count“ nicht.

## <a name="outputs-iteration"></a>Ausgabeniteration

Im folgenden Beispiel wird eine Variable Anzahl von Speicherkonten erstellt und für jedes Speicherkonto ein Endpunkt zurückgegeben:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Die vorangehende Vorlage gibt ein Array mit den folgenden Werten zurück:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Im nächsten Beispiel werden drei Eigenschaften aus den neuen Speicherkonten zurückgegeben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

Das vorangehende Beispiel gibt ein Array mit den folgenden Werten zurück:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

* Ein entsprechendes Tutorial finden Sie unter [Tutorial: Erstellen mehrerer Ressourceninstanzen mit ARM-Vorlagen](template-tutorial-create-multiple-instances.md).
* Informationen zu anderen Verwendungsmöglichkeiten des „copy“-Elements finden Sie unter:
  * [Ressourceniteration in ARM-Vorlagen](copy-resources.md)
  * [Eigenschafteniteration in ARM-Vorlagen](copy-properties.md)
  * [Variableniteration in ARM-Vorlagen](copy-variables.md)
* Weitere Informationen zu den Abschnitten in einer Vorlage finden Sie unter [Verstehen der Struktur und Syntax von ARM-Vorlagen](template-syntax.md).
* Informationen zum Bereitstellen Ihrer Vorlage finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md).
