---
title: Vorlagenfunktionen – numerisch
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Arbeiten mit Zahlen verwendet werden können.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 26f4b846c67ee7b926ea984ceefd84bf9ea56952
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004533"
---
# <a name="numeric-functions-for-arm-templates"></a>Numerische Funktionen für ARM-Vorlagen

Resource Manager stellt die folgenden Funktionen für das Arbeiten mit ganzen Zahlen in Ihrer ARM-Vorlage (Azure Resource Manager) bereit:

* [add](#add)
* [copyIndex](#copyindex)
* [div](#div)
* [float](#float)
* [int](#int)
* [max](#max)
* [min](#min)
* [mod](#mod)
* [mul](#mul)
* [sub](#sub)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="add"></a>add

`add(operand1, operand2)`

Gibt die Summe der beiden angegebenen ganzen Zahlen zurück. Die `add`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `+`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
|operand1 |Ja |INT |Erste zu addierende Zahl. |
|operand2 |Ja |INT |Zweite zu addierende Zahl. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Summe der Parameter enthält.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/add.json) werden zwei Parameter hinzugefügt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to add"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to add"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "addResult": {
      "type": "int",
      "value": "[add(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output addResult int = first + second
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| addResult | Int | 8 |

## <a name="copyindex"></a>copyIndex

`copyIndex(loopName, offset)`

Gibt den Index einer Iterationsschleife zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| loopName | Nein  | Zeichenfolge | Der Name der Schleife zum Abrufen der Iteration |
| offset |Nein |INT |Die Zahl, die dem nullbasierten (0) Iterationswert hinzugefügt werden soll. |

### <a name="remarks"></a>Hinweise

Diese Funktion wird immer mit einem **copy** -Objekt verwendet. Wenn kein Wert für **offset** angegeben wird, wird der aktuelle Iterationswert zurückgegeben. Der Iterationswert beginnt bei 0 (null).

Mit der Eigenschaft **loopName** können Sie angeben, ob sich „copyIndex“ auf eine Ressourceniteration oder eine Eigenschafteniteration bezieht. Wenn kein Wert für **loopName** angegeben wird, wird die Iteration des aktuellen Ressourcentyps verwendet. Geben Sie einen Wert für **loopName** an, wenn eine Iteration für eine Eigenschaft ausgeführt wird.

Weitere Informationen zur Verwendung von Kopiervorgängen finden Sie unter:

* [Ressourceniteration in ARM-Vorlagen](copy-resources.md)
* [Eigenschafteniteration in ARM-Vorlagen](copy-properties.md)
* [Variableniteration in ARM-Vorlagen](copy-variables.md)
* [Ausgabeiteration in ARM-Vorlagen](copy-outputs.md)

### <a name="example"></a>Beispiel

Das folgende Beispiel enthält eine Kopierschleife und den Indexwert im Namen.

# <a name="json"></a>[JSON](#tab/json)

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> „Loops“ und `copyIndex` sind in Bicep noch nicht implementiert.  Siehe unter [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) (Schleifen).

---

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den aktuellen Index der Iteration darstellt.

## <a name="div"></a>div

`div(operand1, operand2)`

Gibt die Ganzzahldivision der beiden angegebenen ganzen Zahlen zurück. Die `div`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `/`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Die zu teilende Zahl (Dividend). |
| operand2 |Ja |INT |Die Zahl, durch die geteilt wird (Divisor). Darf nicht null (0) sein. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Division darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/div.json) wird ein Parameter durch einen anderen Parameter dividiert.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 8,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "divResult": {
      "type": "int",
      "value": "[div(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 8
param second int = 3

output addResult int = first / second
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| divResult | Int | 2 |

## <a name="float"></a>float

`float(arg1)`

Konvertiert den Wert in eine Gleitkommazahl. Diese Funktion wird nur beim Übergeben von benutzerdefinierten Parametern an eine Anwendung (z. B. eine Logik-App) verwendet. Die `float`-Funktion wird in Bicep nicht unterstützt.  Weitere Informationen finden Sie unter [Unterstützung anderer numerischer Typen als 32-Bit-Ganzzahlen](https://github.com/Azure/bicep/issues/486).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge oder ganze Zahl |Der in eine Gleitkommazahl zu konvertierende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Gleitkommazahl.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie „float“ zum Übergeben von Parametern an eine Logik-App verwendet wird:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Logic/workflows",
  "properties": {
    ...
    "parameters": {
      "custom1": {
        "value": "[float('3.0')]"
      },
      "custom2": {
        "value": "[float(3)]"
      },
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> Die `float`-Funktion wird in Bicep nicht unterstützt.  Weitere Informationen finden Sie unter [Unterstützung anderer numerischer Typen als 32-Bit-Ganzzahlen](https://github.com/Azure/bicep/issues/486).

---

## <a name="int"></a>INT

`int(valueToConvert)`

Konvertiert den angegebenen Wert in eine ganze Zahl (Integer).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der in eine ganze Zahl (Integer) konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl des konvertierten Werts.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/int.json) wird der vom Benutzer angegebene Parameterwert in eine ganze Zahl konvertiert.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToConvert": {
      "type": "string",
      "defaultValue": "4"
    }
  },
  "resources": [
  ],
  "outputs": {
    "intResult": {
      "type": "int",
      "value": "[int(parameters('stringToConvert'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToConvert string = '4'

output inResult int = int(stringToConvert)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| intResult | Int | 4 |

## <a name="max"></a>max

`max (arg1)`

Gibt den größten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der größte Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den größten Wert aus der Auflistung darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/max.json) wird gezeigt, wie „max“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min (arg1)`

Gibt den kleinsten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der kleinste Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den kleinsten Wert aus der Auflistung darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/min.json) wird gezeigt, wie „min“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ 0, 3, 2, 5, 4 ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  0
  3
  2
  5
  4
]

output arrayOutPut int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="mod"></a>mod

`mod(operand1, operand2)`

Gibt den Rest der Ganzzahldivision mit den beiden angegebenen ganzen Zahlen zurück. Die `mod`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `%`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Die zu teilende Zahl (Dividend). |
| operand2 |Ja |INT |Die Zahl, durch die dividiert wird (Divisor), darf nicht null (0) sein. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die den Rest darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mod.json) wird der Restbetrag der Division eines Parameters durch einen anderen Parameter zurückgegeben.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer being divided"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer used to divide"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "modResult": {
      "type": "int",
      "value": "[mod(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output modResult int = first % second
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| modResult | Int | 1 |

## <a name="mul"></a>mul

`mul(operand1, operand2)`

Gibt die Multiplikation der beiden angegebenen ganzen Zahlen zurück. Die `mul`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `*`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Erste zu multiplizierende Zahl. |
| operand2 |Ja |INT |Zweite zu multiplizierende Zahl. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Multiplikation darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/mul.json) wird ein Parameter mit einem anderen Parameter multipliziert.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 5,
      "metadata": {
        "description": "First integer to multiply"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Second integer to multiply"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "mulResult": {
      "type": "int",
      "value": "[mul(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 5
param second int = 3

output mulResult int = first * second
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| mulResult | Int | 15 |

## <a name="sub"></a>sub

`sub(operand1, operand2)`

Gibt die Differenz der beiden angegebenen ganzen Zahlen zurück. Die `sub`-Funktion wird in Bicep nicht unterstützt. Verwenden Sie stattdessen den Operator `-`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| operand1 |Ja |INT |Zahl, von der subtrahiert wird. |
| operand2 |Ja |INT |Zahl, die subtrahiert wird. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Subtraktion darstellt.

### <a name="example"></a>Beispiel

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/sub.json) wird ein Parameter von einem anderen Parameter subtrahiert.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "first": {
      "type": "int",
      "defaultValue": 7,
      "metadata": {
        "description": "Integer subtracted from"
      }
    },
    "second": {
      "type": "int",
      "defaultValue": 3,
      "metadata": {
        "description": "Integer to subtract"
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "subResult": {
      "type": "int",
      "value": "[sub(parameters('first'), parameters('second'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param first int = 7
param second int = 3

output subResult int = first - second
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| subResult | Int | 4 |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](template-syntax.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](copy-resources.md).
