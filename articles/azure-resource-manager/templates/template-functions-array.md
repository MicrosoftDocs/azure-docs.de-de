---
title: Vorlagenfunktionen – Arrays
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Arbeiten mit Arrays verwendet werden können.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 85f910f6143fd16f996a200172d0efd852897d8c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96004618"
---
# <a name="array-functions-for-arm-templates"></a>Arrayfunktionen für ARM-Vorlagen

Resource Manager stellt mehrere Funktionen für das Arbeiten mit Arrays in Ihrer ARM-Vorlage (Azure Resource Manager) bereit.

* [array](#array)
* [concat](#concat)
* [contains](#contains)
* [createArray](#createarray)
* [empty](#empty)
* [first](#first)
* [intersection](#intersection)
* [last](#last)
* [length](#length)
* [max](#max)
* [min](#min)
* [range](#range)
* [skip](#skip)
* [take](#take)
* [union](#union)

Informationen zu einem Array mit Zeichenfolgenwerten, die durch einen Wert getrennt sind, finden Sie unter [split](template-functions-string.md#split).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="array"></a>array

`array(convertToArray)`

Konvertiert den Wert in ein Array.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| convertToArray |Ja |int, string, array oder object |Der Wert, der in ein Array konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein -Array.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung der array-Funktion mit unterschiedlichen Typen gezeigt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": {
        "a": "b",
        "c": "d"
      }
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param intToConvert int = 1
param stringToConvert string = 'efgh'
param objectToConvert object = {
  'a': 'b'
  'c': 'd'
}

output intOutput array = array(intToConvert)
output stringOutput array = array(stringToConvert)
output objectOutput array = array(objectToConvert)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| intOutput | Array | [1] |
| stringOutput | Array | ["efgh"] |
| objectOutput | Array | [{"a": "b", "c": "d"}] |

## <a name="concat"></a>concat

`concat(arg1, arg2, arg3, ...)`

Kombiniert mehrere Arrays und gibt das verkettete Array zurück oder kombiniert mehrere Zeichenfolgenwerte und gibt die verkettete Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Das erste Array bzw. die erste Zeichenfolge für die Verkettung. |
| zusätzliche Argumente |Nein  |Array oder Zeichenfolge |Weitere Arrays bzw. Zeichenfolgen in sequenzieller Reihenfolge für die Verkettung. |

Diese Funktion akzeptiert eine beliebige Anzahl von Argumenten und Zeichenfolgen oder Arrays für die Parameter. Sie können jedoch nicht sowohl Arrays als auch Zeichenfolgen für Parameter angeben. Arrays werden nur mit anderen Arrays verkettet.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge oder ein Array aus verketteten Werten.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird veranschaulicht, wie zwei Arrays kombiniert werden.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) zeigt, wie zwei Zeichenfolgenwerte kombiniert werden und eine verkettete Zeichenfolge zurückgegeben wird.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| concatOutput | String | prefix-5yj4yjf5mbg72 |

## <a name="contains"></a>contains

`contains(container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| Container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung von „contains“ mit unterschiedlichen Typen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="createarray"></a>createArray

`createArray (arg1, arg2, arg3, ...)`

Erstellt ein Array auf der Grundlage der Parameter. Die `createArray`-Funktion wird von Bicep nicht unterstützt.  Erstellen Sie ein Arrayliteral mit `[]`.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| args |Nein |Zeichenfolge, ganze Zahl, Array oder Objekt |Die Werte im Array. |

### <a name="return-value"></a>Rückgabewert

Ein -Array. Wenn keine Parameter angegeben werden, wird ein leeres Array zurückgegeben.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung von „createArray“ mit unterschiedlichen Typen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
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
    },
    "emptyArray": {
      "type": "array",
      "value": "[createArray()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `createArray()` wird von Bicep nicht unterstützt.  Erstellen Sie ein Arrayliteral mit `[]`.

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringArray | Array | ["a", "b", "c"] |
| intArray | Array | [1, 2, 3] |
| objectArray | Array | [{"one": "a", "two": "b", "three": "c"}] |
| arrayArray | Array | [["one", "two", "three"]] |
| emptyArray | Array | [] |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den geprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird überprüft, ob ein Array, Objekt und eine Zeichenfolge leer sind.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="first"></a>first

`first(arg1)`

Gibt das erste Element des Arrays oder das erste Zeichen der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen erstes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des ersten Elements in einem Array oder das erste Zeichen einer Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung der first-Funktion mit einem Array und einer Zeichenfolge gezeigt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | one |
| stringOutput | String | O |

## <a name="intersection"></a>Schnittmenge

`intersection(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder ein Objekt mit den gemeinsamen Elementen aus den Parametern zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste Wert für die Suche nach gemeinsamen Elementen. |
| arg2 |Ja |Array oder Objekt |Der zweite Wert für die Suche nach gemeinsamen Elementen. |
| zusätzliche Argumente |Nein |Array oder Objekt |Weitere Werte für die Suche nach gemeinsamen Elementen. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt mit den gemeinsamen Elementen.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „intersection“ mit Arrays und Objekten gezeigt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "z",
        "three": "c"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "two", "three" ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}

param secondObject object = {
  'one': 'a'
  'two': 'z'
  'three': 'c'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'two'
  'three'
]

output objectOutput object = intersection(firstObject, secondObject)
output arrayOutput array = intersection(firstArray, secondArray)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "three": "c"} |
| arrayOutput | Array | ["two", "three"] |

## <a name="last"></a>last

`last (arg1)`

Gibt das letzte Element des Arrays bzw. das letzte Zeichen der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen letztes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des letzten Elements in einem Array oder das letzte Zeichen einer Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung der last-Funktion mit einem Array und einer Zeichenfolge gezeigt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two three')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="length"></a>length

`length(arg1)`

Gibt die Anzahl von Elementen in einem Array, Zeichen in einer Zeichenfolge oder Eigenschaften auf Stammebene in einem Objekt zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array, Zeichenfolge oder Objekt |Das Array, von dem die Anzahl der Elemente ermittelt werden soll, die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll, oder das Objekt, von dem die Anzahl der Eigenschaften auf Stammebene ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „length“ mit einem Array und einer Zeichenfolge gezeigt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
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
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arraylength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

Sie können diese Funktion mit einem Array verwenden, um bei der Erstellung von Ressourcen die Anzahl der Iterationen anzugeben. Im folgenden Beispiel bezieht sich der Parameter **siteNames** auf ein Array von Namen, die bei der Erstellung der Websites verwendet werden.

# <a name="json"></a>[JSON](#tab/json)

```json
"copy": {
  "name": "websitescopy",
  "count": "[length(parameters('siteNames'))]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> „Loops“ sind in Bicep noch nicht implementiert.  Siehe unter [Loops](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) (Schleifen).

---

Weitere Informationen zur Verwendung dieser Funktion mit einem Array finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](copy-resources.md).

## <a name="max"></a>max

`max(arg1)`

Gibt den größten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der größte Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Ganzzahlwert, der den größten Wert darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie „max“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

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

output arrayOutput int = max(arrayToTest)
output intOutput int = max(0,3,2,5,4)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 5 |
| intOutput | Int | 5 |

## <a name="min"></a>Min

`min(arg1)`

Gibt den kleinsten Wert aus einem Array mit ganzen Zahlen oder einer durch Trennzeichen getrennten Liste mit ganzen Zahlen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array mit ganzen Zahlen oder durch Trennzeichen getrennte Liste mit ganzen Zahlen |Die Auflistung, aus der der kleinste Wert abgerufen werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Ganzzahlwert, der den kleinsten Wert darstellt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird gezeigt, wie „min“ mit einem Array und einer Liste mit ganzen Zahlen verwendet wird:

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

output arrayOutput int = min(arrayToTest)
output intOutput int = min(0,3,2,5,4)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Int | 0 |
| intOutput | Int | 0 |

## <a name="range"></a>range

`range(startIndex, count)`

Erstellt ein Array mit ganzen Zahlen, das mit einer ganzen Zahl beginnt und eine bestimmte Zahl von Elementen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| startIndex |Ja |INT |Die erste ganze Zahl im Array. Die Summe von „startIndex“ und „count“ darf nicht größer sein als 2147483647. |
| count |Ja |INT |Die Anzahl von ganzen Zahlen im Array. Muss eine positive ganze Zahl bis 10000 sein. |

### <a name="return-value"></a>Rückgabewert

Ein Array mit ganzen Zahlen.

### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt die Verwendung der „range“-Funktion:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param startingInt int = 5
param numberOfElements int = 3

output rangeOutput array = range(startingInt, numberOfElements)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| rangeOutput | Array | [5, 6, 7] |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Gibt ein Array mit allen Elementen gemäß der angegebenen Anzahl im Array bzw. eine Zeichenfolge mit allen Zeichen gemäß der angegebenen Anzahl in der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Array oder Zeichenfolge, wo Elemente übersprungen werden sollen. |
| numberToSkip |Ja |INT |Die Anzahl der zu überspringenden Elemente bzw. Zeichen. Wenn dieser Wert 0 (null) oder kleiner ist, werden alle Elemente oder Zeichen in dem Wert zurückgegeben. Ist der Wert größer als die Länge des Arrays bzw. der Zeichenfolge, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die angegebene Anzahl von Elementen im Array und Zeichen in der Zeichenfolge übersprungen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | String | two three |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Gibt ein Array mit der angegebenen Anzahl von Elementen ab dem Anfang des Arrays bzw. eine Zeichenfolge mit der angegebenen Anzahl von Zeichen ab dem Anfang der Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Das Array bzw. die Zeichenfolge, wo die Elemente entnommen werden sollen. |
| numberToTake |Ja |INT |Die Anzahl der zu entnehmenden Elemente bzw. Zeichen. Ist dieser Wert 0 oder kleiner, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. Ist der Wert größer als die Länge des entsprechenden Arrays bzw. der Zeichenfolge, werden alle Elemente des Arrays bzw. der Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die angegebene Anzahl von Elementen aus dem Array und Zeichen aus der Zeichenfolge entnommen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="union"></a>union

`union(arg1, arg2, arg3, ...)`

Gibt ein einzelnes Array oder Objekt mit allen Elementen aus den Parametern zurück. Doppelte Werte oder Schlüssel sind nur einmal enthalten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Objekt |Der erste zum Verknüpfen von Elementen zu verwendende Wert. |
| arg2 |Ja |Array oder Objekt |Der zweite zum Verknüpfen von Elementen zu verwendende Wert. |
| zusätzliche Argumente |Nein |Array oder Objekt |Weitere zum Verknüpfen von Elementen zu verwendende Werte. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder Objekt.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird die Verwendung von „union“ mit Arrays und Objekten gezeigt:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstObject": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c1"
      }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": {
        "three": "c2",
        "four": "d",
        "five": "e"
      }
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "three", "four" ]
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstObject object = {
  'one': 'a'
  'two': 'b'
  'three': 'c1'
}

param secondObject object = {
  'three': 'c2'
  'four': 'd'
  'five': 'e'
}

param firstArray array = [
  'one'
  'two'
  'three'
]

param secondArray array = [
  'three'
  'four'
]

output objectOutput object = union(firstObject, secondObject)
output arrayOutput array = union(firstArray, secondArray)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| objectOutput | Object | {"one": "a", "two": "b", "three": "c2", "four": "d", "five": "e"} |
| arrayOutput | Array | ["one", "two", "three", "four"] |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](template-syntax.md).
