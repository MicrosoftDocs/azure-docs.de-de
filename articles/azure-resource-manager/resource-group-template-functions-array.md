---
title: Azure Resource Manager-Vorlagenfunktionen – Arrays und Objekte | Microsoft-Dokumentation
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Arbeiten mit Arrays und Objekten verwendet werden können.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2018
ms.author: tomfitz
ms.openlocfilehash: c80625fb36709f66319b4966e210785864f30d09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66128694"
---
# <a name="array-and-object-functions-for-azure-resource-manager-templates"></a>Array- und Objektfunktionen für Azure Resource Manager-Vorlagen

Resource Manager stellt mehrere Funktionen zum Arbeiten mit Arrays und Objekten bereit.

* [array](#array)
* [coalesce](#coalesce)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [json](#json)
* [last](#last)
* [Länge](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Informationen zu einem Array mit Zeichenfolgenwerten, die durch einen Wert getrennt sind, finden Sie unter [split](resource-group-template-functions-string.md#split).

<a id="array" />

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="array"></a>Array
`array(convertToArray)`

Konvertiert den Wert in ein Array.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| convertToArray |Ja |Ganze Zahl, Zeichenfolge, Array oder Objekt |Der Wert, der in ein Array konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Array.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/array.json) wird die Verwendung der array-Funktion mit unterschiedlichen Typen gezeigt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "intToConvert": {
            "type": "int",
            "defaultValue": 1
        },
        "stringToConvert": {
            "type": "string",
            "defaultValue": "efgh"
        },
        "objectToConvert": {
            "type": "object",
            "defaultValue": {"a": "b", "c": "d"}
        }
    },
    "resources": [
    ],
    "outputs": {
        "intOutput": {
            "type": "array",
            "value": "[array(parameters('intToConvert'))]"
        },
        "stringOutput": {
            "type": "array",
            "value": "[array(parameters('stringToConvert'))]"
        },
        "objectOutput": {
            "type": "array",
            "value": "[array(parameters('objectToConvert'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/array.json
```

<a id="coalesce" />

## <a name="coalesce"></a>coalesce
`coalesce(arg1, arg2, arg3, ...)`

Gibt den ersten Wert aus den Parametern zurück, der nicht NULL ist. Leere Zeichenfolgen, leere Arrays und leere Objekte sind nicht NULL.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganze Zahl, Zeichenfolge, Array oder Objekt |Der erste Wert, der auf NULL getestet werden soll. |
| weitere arg-Parameter |Nein  |Ganze Zahl, Zeichenfolge, Array oder Objekt |Weitere Werte, die auf NULL getestet werden sollen. |

### <a name="return-value"></a>Rückgabewert

Der Wert des ersten Parameters ungleich NULL, der Zeichenfolge, ganze Zahl, Array oder Objekt sein kann. NULL, wenn alle Parameter NULL sind. 

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) zeigt die Ausgabe bei unterschiedlichen Verwendungen von „coalesce“:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "null1": null, 
                "null2": null,
                "string": "default",
                "int": 1,
                "object": {"first": "default"},
                "array": [1]
            }
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
        },
        "intOutput": {
            "type": "int",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
        },
        "objectOutput": {
            "type": "object",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
        },
        "emptyOutput": {
            "type": "bool",
            "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| stringOutput | Zeichenfolge | die Standardeinstellung |
| intOutput | Int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Bool | True |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/coalesce.json
```

<a id="concat" />

## <a name="concat"></a>concat
`concat(arg1, arg2, arg3, ...)`

Kombiniert mehrere Arrays und gibt das verkettete Array zurück oder kombiniert mehrere Zeichenfolgenwerte und gibt die verkettete Zeichenfolge zurück. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Das erste Array bzw. die erste Zeichenfolge für die Verkettung. |
| zusätzliche Argumente |Nein  |Array oder Zeichenfolge |Weitere Arrays bzw. Zeichenfolgen in sequenzieller Reihenfolge für die Verkettung. |

Diese Funktion akzeptiert eine beliebige Anzahl von Argumenten und Zeichenfolgen oder Arrays für die Parameter.

### <a name="return-value"></a>Rückgabewert
Eine Zeichenfolge oder ein Array aus verketteten Werten.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) zeigt, wie zwei Arrays kombiniert werden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-array.json
```

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) zeigt, wie zwei Zeichenfolgenwerte kombiniert werden und eine verkettete Zeichenfolge zurückgegeben wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| concatOutput | Zeichenfolge | prefix-5yj4yjf5mbg72 |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/concat-string.json
```

<a id="contains" />

## <a name="contains"></a>contains
`contains(container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) zeigt die Verwendung von „contains“ mit unterschiedlichen Typen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/contains.json
```

<a id="createarray" />

## <a name="createarray"></a>createarray
`createArray (arg1, arg2, arg3, ...)`

Erstellt ein Array auf der Grundlage der Parameter.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge, ganze Zahl, Array oder Objekt |Der erste Wert im Array. |
| zusätzliche Argumente |Nein  |Zeichenfolge, ganze Zahl, Array oder Objekt |Weitere Werte im Array. |

### <a name="return-value"></a>Rückgabewert

Ein Array.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/createarray.json) zeigt die Verwendung von „createArray“ mit unterschiedlichen Typen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringArray": {
            "type": "array",
            "value": "[createArray('a', 'b', 'c')]"
        },
        "intArray": {
            "type": "array",
            "value": "[createArray(1, 2, 3)]"
        },
        "objectArray": {
            "type": "array",
            "value": "[createArray(parameters('objectToTest'))]"
        },
        "arrayArray": {
            "type": "array",
            "value": "[createArray(parameters('arrayToTest'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/createarray.json
```

<a id="empty" />

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den geprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) überprüft, ob ein Array, ein Objekt und eine Zeichenfolge leer sind.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/empty.json
```

<a id="first" />

## <a name="first"></a>first
`first(arg1)`

Gibt das erste Element des Arrays oder das erste Zeichen der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen erstes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des ersten Elements in einem Array oder das erste Zeichen einer Zeichenfolge.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) zeigt die Verwendung der first-Funktion mit einem Array und einer Zeichenfolge.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Zeichenfolge | one |
| stringOutput | Zeichenfolge | O |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/first.json
```

<a id="intersection" />

## <a name="intersection"></a>Schnittmenge
`intersection(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder ein Objekt mit den gemeinsamen Elementen aus den Parametern zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste Wert für die Suche nach gemeinsamen Elementen. |
| arg2 |Ja |Array oder Objekt |Der zweite Wert für die Suche nach gemeinsamen Elementen. |
| zusätzliche Argumente |Nein  |Array oder Objekt |Weitere Werte für die Suche nach gemeinsamen Elementen. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt mit den gemeinsamen Elementen.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/intersection.json) wird die Verwendung von „intersection“ mit Arrays und Objekten gezeigt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "z", "three": "c"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[intersection(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[intersection(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/intersection.json
```

## <a name="json"></a>json
`json(arg1)`

Gibt ein JSON-Objekt zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge |Der Wert, der in JSON konvertiert werden soll. |


### <a name="return-value"></a>Rückgabewert

Das JSON-Objekt aus der angegebenen Zeichenfolge oder ein leeres Objekt, wenn **null** angegeben ist.

### <a name="remarks"></a>Anmerkungen

Wenn Sie einen Parameterwert oder eine Variable in das JSON-Objekt einschließen möchten, verwenden Sie die Funktion [concat](resource-group-template-functions-string.md#concat), um die Zeichenfolge zu erstellen, die Sie an die Funktion übergeben.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/json.json) wird die Verwendung der JSON-Funktion mit Arrays und Objekten gezeigt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testValue": {
            "type": "string",
            "defaultValue": "demo value"
        }
    },
    "resources": [
    ],
    "outputs": {
        "jsonOutput": {
            "type": "object",
            "value": "[json('{\"a\": \"b\"}')]"
        },
        "nullOutput": {
            "type": "bool",
            "value": "[empty(json('null'))]"
        },
        "paramOutput": {
            "type": "object",
            "value": "[json(concat('{\"a\": \"', parameters('testValue'), '\"}'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| jsonOutput | Object | {"a": "b"} |
| nullOutput | Boolescher Wert | True |
| paramOutput | Object | {"a": "demo value"}

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/json.json
```

<a id="last" />

## <a name="last"></a>last
`last (arg1)`

Gibt das letzte Element des Arrays bzw. das letzte Zeichen der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen letztes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des letzten Elements in einem Array oder das letzte Zeichen einer Zeichenfolge.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) zeigt die Verwendung der last-Funktion mit einem Array und einer Zeichenfolge.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Zeichenfolge | three |
| stringOutput | Zeichenfolge | e |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/last.json
```

<a id="length" />

## <a name="length"></a>length
`length(arg1)`

Gibt die Anzahl von Elementen in einem Array bzw. von Zeichen in einer Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Das Array, von dem die Anzahl der Elemente, bzw. die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl. 

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) zeigt die Verwendung von „length“ mit einem Array und einer Zeichenfolge:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arraylength | Int | 3 |
| stringLength | Int | 13 |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/length.json
```

Sie können diese Funktion mit einem Array verwenden, um bei der Erstellung von Ressourcen die Anzahl der Iterationen anzugeben. Im folgenden Beispiel bezieht sich der Parameter **siteNames** auf ein Array von Namen, die bei der Erstellung der Websites verwendet werden.

```json
"copy": {
    "name": "websitescopy",
    "count": "[length(parameters('siteNames'))]"
}
```

Weitere Informationen zur Verwendung dieser Funktion mit einem Array finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).

<a id="max" />

## <a name="max"></a>max
`max(arg1)`

Gibt den größten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der größte Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Ganzzahlwert, der den größten Wert darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) wird gezeigt, wie „max“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[max(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[max(0,3,2,5,4)]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/max.json
```

<a id="min" />

## <a name="min"></a>Min
`min(arg1)`

Gibt den kleinsten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der kleinste Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Ganzzahlwert, der den kleinsten Wert darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) wird gezeigt, wie „min“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [0,3,2,5,4]
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "int",
            "value": "[min(parameters('arrayToTest'))]"
        },
        "intOutput": {
            "type": "int",
            "value": "[min(0,3,2,5,4)]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/min.json
```

<a id="range" />

## <a name="range"></a>range
`range(startingInteger, numberOfElements)`

Erstellt ein Array mit ganzen Zahlen, das mit einer ganzen Zahl beginnt und eine bestimmte Zahl von Elementen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| startingInteger |Ja |int |Die erste ganze Zahl im Array. |
| numberofElements |Ja |int |Die Anzahl von ganzen Zahlen im Array. |

### <a name="return-value"></a>Rückgabewert

Ein Array mit ganzen Zahlen.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/range.json) zeigt die Verwendung der range-Funktion:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "startingInt": {
            "type": "int",
            "defaultValue": 5
        },
        "numberOfElements": {
            "type": "int",
            "defaultValue": 3
        }
    },
    "resources": [],
    "outputs": {
        "rangeOutput": {
            "type": "array",
            "value": "[range(parameters('startingInt'),parameters('numberOfElements'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/range.json
```

<a id="skip" />

## <a name="skip"></a>skip
`skip(originalValue, numberToSkip)`

Gibt ein Array mit allen Elementen gemäß der angegebenen Anzahl im Array bzw. eine Zeichenfolge mit allen Zeichen gemäß der angegebenen Anzahl in der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Array oder Zeichenfolge, wo Elemente übersprungen werden sollen. |
| numberToSkip |Ja |int |Die Anzahl der zu überspringenden Elemente bzw. Zeichen. Wenn dieser Wert 0 (null) oder kleiner ist, werden alle Elemente oder Zeichen in dem Wert zurückgegeben. Ist der Wert größer als die Länge des Arrays bzw. der Zeichenfolge, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) wird die angegebene Anzahl von Elementen im Array und von Zeichen in einer Zeichenfolge übersprungen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | Zeichenfolge | two three |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/skip.json
```

<a id="take" />

## <a name="take"></a>take
`take(originalValue, numberToTake)`

Gibt ein Array mit der angegebenen Anzahl von Elementen ab dem Anfang des Arrays bzw. eine Zeichenfolge mit der angegebenen Anzahl von Zeichen ab dem Anfang der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Das Array bzw. die Zeichenfolge, wo die Elemente entnommen werden sollen. |
| numberToTake |Ja |int |Die Anzahl der zu entnehmenden Elemente bzw. Zeichen. Ist dieser Wert 0 oder kleiner, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. Ist der Wert größer als die Länge des entsprechenden Arrays bzw. der Zeichenfolge, werden alle Elemente des Arrays bzw. der Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) wird die angegebene Anzahl von Elementen aus dem Array und von Zeichen aus einer Zeichenfolge entnommen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | Zeichenfolge | on |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/take.json
```

<a id="union" />

## <a name="union"></a>union
`union(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder Objekt mit allen Elementen aus den Parametern zurück. Doppelte Werte oder Schlüssel sind nur einmal enthalten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste zum Verknüpfen von Elementen zu verwendende Wert. |
| arg2 |Ja |Array oder Objekt |Der zweite zum Verknüpfen von Elementen zu verwendende Wert. |
| zusätzliche Argumente |Nein  |Array oder Objekt |Weitere zum Verknüpfen von Elementen zu verwendende Werte. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/union.json) wird die Verwendung von „union“ mit Arrays und Objekten gezeigt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstObject": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c1"}
        },
        "secondObject": {
            "type": "object",
            "defaultValue": {"three": "c2", "four": "d", "five": "e"}
        },
        "firstArray": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        },
        "secondArray": {
            "type": "array",
            "defaultValue": ["three", "four"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "objectOutput": {
            "type": "object",
            "value": "[union(parameters('firstObject'), parameters('secondObject'))]"
        },
        "arrayOutput": {
            "type": "array",
            "value": "[union(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

Stellen Sie diese Beispielvorlage mit der Azure CLI wie folgt bereit:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

Um diese Beispielvorlage mit PowerShell bereitzustellen, verwenden Sie:

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/union.json
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
* Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](resource-group-linked-templates.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).
* Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen ](resource-group-template-deploy.md).

