---
title: Definieren mehrerer Instanzen einer Variablen
description: Verwenden des copy-Vorgangs in einer Azure Resource Manager-Vorlage (ARM), um sie beim Erstellen einer Variable mehrere Male zu durchlaufen.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b8acd85659b843cb482e1ccc61e28da03431db1b
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905892"
---
# <a name="variable-iteration-in-arm-templates"></a>Variableniteration in ARM-Vorlagen

In diesem Artikel wird gezeigt, wie Sie mehr als einen Wert für eine Variable in Ihrer Azure Resource Manager-Vorlage (ARM) erstellen können. Durch das Hinzufügen des `copy`-Elements zum Variablenabschnitt Ihrer Vorlage können Sie die Anzahl der Elemente für eine Variable während der Bereitstellung dynamisch festlegen. Außerdem vermeiden Sie so die Wiederholung von Vorlagensyntax.

Sie können „copy“ auch mit [Ressourcen](copy-resources.md), [Eigenschaften in einer Ressource](copy-properties.md) und [Ausgaben](copy-outputs.md) verwenden.

## <a name="syntax"></a>Syntax

Das copy-Element hat das folgende allgemeine Format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Die Eigenschaft `name` ist ein beliebiger Wert, der die Schleife identifiziert. Die `count`-Eigenschaft gibt die für die Variable gewünschte Anzahl von Iterationen an.

Die Eigenschaft `input` gibt die Eigenschaften an, die Sie wiederholen möchten. Erstellen Sie ein Array von Elementen, das aus dem Wert in der `input`-Eigenschaft erstellt wird. Es kann sich um eine einzelne Eigenschaft handeln (z. B. eine Zeichenfolge) oder um ein Objekt mit mehreren Eigenschaften.

## <a name="copy-limits"></a>Einschränkungen für „copy“

Der Wert von „count“ darf 800 nicht überschreiten.

Der Wert von „count“ darf nicht negativ sein. Er kann Null sein, wenn Sie die Vorlage mit einer neueren Version von Azure CLI, PowerShell oder der REST-API bereitstellen. Insbesondere müssen Sie Folgendes verwenden:

* Azure PowerShell **2.6** oder höher
* Azure CLI **2.0.74** oder höher
* REST-API-Version **2019-05-10** oder höher
* [Verknüpfte Bereitstellungen](linked-templates.md) müssen API-Version **2019-05-10** oder höher für den Bereitstellungsressourcentyp verwenden.

Frühere Versionen von PowerShell, CLI und der REST-API unterstützen den Wert „0“ (null) für „count“ nicht.

## <a name="variable-iteration"></a>Variableniteration

Im folgenden Beispiel wird veranschaulicht, wie Sie ein Array mit Zeichenfolgenwerten erstellen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Die vorangehende Vorlage gibt ein Array mit den folgenden Werten zurück:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

Das nächste Beispiel zeigt, wie Sie ein Array von Objekten mit den drei Eigenschaften `name`, `diskSizeGB` und `diskIndex` erstellen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

Das vorangehende Beispiel gibt ein Array mit den folgenden Werten zurück:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> Die Variableniteration unterstützt ein Offsetargument. Der Offset muss nach dem Namen der Iteration angegeben werden, z. B. mit `copyIndex('diskNames', 1)`. Wenn Sie keinen Offsetwert angeben, nimmt die erste Instanz den Standardwert 0 an.
>

Sie können das `copy`-Element auch innerhalb einer Variable verwenden. Mit dem folgenden Beispiel wird ein Objekt erstellt, bei dem einer seiner Werte ein Array ist.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

Das vorangehende Beispiel gibt ein Objekt mit den folgenden Werten zurück:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

Im nächsten Beispiel werden die verschiedenen Möglichkeiten gezeigt, mit denen Sie `copy` zusammen mit Variablen verwenden können.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>Beispielvorlagen

Die folgenden Beispiele zeigen allgemeine Szenarien für das Erstellen mehrerer Werte für eine Variable.

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
|[Variablen kopieren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Veranschaulicht die verschiedenen Methoden zum Durchlaufen von Variablen |
|[Mehrere Sicherheitsregeln](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Stellt mehrere Sicherheitsregeln in einer Netzwerksicherheitsgruppe bereit. Die Sicherheitsregeln werden aus einem Parameter generiert. Informationen zum Parameter finden Sie im Artikel zur [Datei mit mehreren NSG-Parametern](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nächste Schritte

* Ein entsprechendes Tutorial finden Sie unter [Tutorial: Erstellen mehrerer Ressourceninstanzen mit ARM-Vorlagen](template-tutorial-create-multiple-instances.md).
* Informationen zu anderen Verwendungsmöglichkeiten des „copy“-Elements finden Sie unter:
  * [Ressourceniteration in ARM-Vorlagen](copy-resources.md)
  * [Eigenschafteniteration in ARM-Vorlagen](copy-properties.md)
  * [Ausgabeiteration in ARM-Vorlagen](copy-outputs.md)
* Weitere Informationen zu den Abschnitten in einer Vorlage finden Sie unter [Verstehen der Struktur und Syntax von ARM-Vorlagen](template-syntax.md).
* Informationen zum Bereitstellen Ihrer Vorlage finden Sie unter [Bereitstellen von Ressourcen mit ARM-Vorlagen und Azure PowerShell](deploy-powershell.md).
