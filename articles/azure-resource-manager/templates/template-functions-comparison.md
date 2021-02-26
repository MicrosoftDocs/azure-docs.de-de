---
title: Vorlagenfunktionen – Vergleich
description: Informationen zu den Funktionen, die in einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Vergleichen von Werten verwendet werden können.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 95655a4c92a1de9bb7a7faebcdaa83fb0fa75696
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833999"
---
# <a name="comparison-functions-for-arm-templates"></a>Vergleichsfunktionen für ARM-Vorlagen

Resource Manager stellt mehrere Funktionen zum Durchführen von Vergleichen in Ihren Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereit:

* [coalesce](#coalesce)
* [equals](#equals)
* [greater](#greater)
* [greaterOrEquals](#greaterorequals)
* [less](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="coalesce"></a>coalesce

`coalesce(arg1, arg2, arg3, ...)`

Gibt den ersten Wert aus den Parametern zurück, der nicht NULL ist. Leere Zeichenfolgen, leere Arrays und leere Objekte sind nicht NULL.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, array oder object |Der erste Wert, der auf NULL getestet werden soll. |
| weitere arg-Parameter |Nein  |int, string, array oder object |Weitere Werte, die auf NULL getestet werden sollen. |

### <a name="return-value"></a>Rückgabewert

Der Wert des ersten Parameters ungleich NULL, der Zeichenfolge, ganze Zahl, Array oder Objekt sein kann. NULL, wenn alle Parameter NULL sind.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) zeigt die Ausgabe bei unterschiedlichen Verwendungen von „coalesce“:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "null1": null,
        "null2": null,
        "string": "default",
        "int": 1,
        "object": { "first": "default" },
        "array": [ 1 ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param objectToTest object = {
  'null1': null
  'null2': null
  'string': 'default'
  'int': 1
  'object': {
    'first': 'default'
  }
  'array': [
    1
  ]
}

output stringOutput string = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.string
output intOutput int = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.int
output objectOutput object = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.object
output arrayOutput array = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.array
output emptyOutput bool =empty(objectToTest.null1 ?? objectToTest.null2)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringOutput | String | default |
| intOutput | Int | 1 |
| objectOutput | Object | {"first": "default"} |
| arrayOutput | Array | [1] |
| emptyOutput | Bool | True |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

Überprüft, ob zwei Werte einander entsprechen. Die `equals`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `==`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |int, string, array oder object |Der erste auf Gleichheit zu überprüfende Wert. |
| arg2 |Ja |int, string, array oder object |Der zweite auf Gleichheit zu überprüfende Wert. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn die Werte gleich sind. Andernfalls wird **False** zurückgegeben.

### <a name="remarks"></a>Hinweise

Die Funktion „equals“ wird häufig mit dem Element `condition` verwendet, um zu prüfen, ob eine Ressource bereitgestellt wurde.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` sind noch nicht in Bicep implementiert. Siehe unter [Conditions](https://github.com/Azure/bicep/issues/186) (Bedingungen).

---

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) überprüft verschiedene Werttypen auf Gleichheit. Alle Standardwerte geben „True“ zurück.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 1
    },
    "firstString": {
      "type": "string",
      "defaultValue": "a"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "firstObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[equals(parameters('firstString'), parameters('secondString'))]"
    },
    "checkArrays": {
      "type": "bool",
      "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
    },
    "checkObjects": {
      "type": "bool",
      "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 1
param firstString string = 'a'
param secondString string = 'a'
param firstArray array = [
  'a'
  'b'
]
param secondArray array = [
  'a'
  'b'
]
param firstObject object = {
  'a': 'b'
}
param secondObject object = {
  'a': 'b'
}

output checInts bool = firstInt == secondInt
output checkStrings bool = firstString == secondString
output checkArrays bool = firstArray == secondArray
output checkObjects bool = firstObject == secondObject
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | True |
| checkArrays | Bool | True |
| checkObjects | Bool | True |

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) wird [not](template-functions-logical.md#not) mit „**equals**“ verwendet.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = ! (1 == 2)
```

---

Die Ausgabe aus dem vorherigen Beispiel lautet wie folgt:

| Name | type | Wert |
| ---- | ---- | ----- |
| checkNotEquals | Bool | True |

## <a name="greater"></a>greater

`greater(arg1, arg2)`

Überprüft, ob der erste Wert größer als der zweite Wert ist. Die `greater`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `>`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Größer als“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Größer als“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert größer als der zweite Wert ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) überprüft, ob der eine Wert größer als der andere Wert ist.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greater(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt > secondInt
output checkStrings bool = firstString > secondString
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

Überprüft, ob der erste Wert größer als oder gleich Wert 2 ist. Die `greaterOrEquals`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `>=`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Größer als oder gleich“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Größer als oder gleich“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert größer als oder gleich Wert 2 ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) überprüft, ob der eine Wert größer als der andere Wert oder gleich groß ist.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt >= secondInt
output checkStrings bool = firstString >= secondString
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | False |
| checkStrings | Bool | True |

## <a name="less"></a>less

`less(arg1, arg2)`

Überprüft, ob der erste Wert kleiner als der zweite Wert ist. Die `less`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `<`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Kleiner als“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Kleiner als“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert kleiner als der zweite Wert ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) überprüft, ob der eine Wert kleiner als der andere Wert ist.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[less(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt < secondInt
output checkStrings bool = firstString < secondString
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

Überprüft, ob der erste Wert kleiner als oder gleich Wert 2 ist. Die `lessOrEquals`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `<=`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Ganz Zahl oder Zeichenfolge |Der erste Wert für den Vergleich vom Typ „Kleiner als oder gleich“. |
| arg2 |Ja |Ganz Zahl oder Zeichenfolge |Der zweite Wert für den Vergleich vom Typ „Kleiner als oder gleich“. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der erste Wert kleiner als oder gleich Wert 2 ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) überprüft, ob der eine Wert kleiner als der andere Wert oder gleich groß ist.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt <= secondInt
output checkStrings bool = firstString <= secondString
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| checkInts | Bool | True |
| checkStrings | Bool | False |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](template-syntax.md).
