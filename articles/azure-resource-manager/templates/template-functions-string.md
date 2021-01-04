---
title: Vorlagenfunktionen – Zeichenfolge
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Arbeiten mit Zeichenfolgen verwendet werden können.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: e94037b40f4937a40e00215aa7a3f99fd3280b49
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96005995"
---
# <a name="string-functions-for-arm-templates"></a>Zeichenfolgenfunktionen für ARM-Vorlagen

Resource Manager stellt die folgenden Funktionen für das Arbeiten mit Zeichenfolgen in Ihren ARM-Vorlagen (Azure Resource Manager) bereit:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [json](#json)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

Rückkehr zur base64-Darstellung der Eingabezeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| inputString |Ja |Zeichenfolge |Der Wert, der als base64-Darstellung zurückgegeben wird. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit der base64-Darstellung.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) zeigt die Nutzungsweise der base64-Funktion.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konvertiert eine base64-Darstellung in ein JSON-Objekt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |Zeichenfolge |Die in ein JSON-Objekt zu konvertierende base64-Darstellung. |

### <a name="return-value"></a>Rückgabewert

Ein JSON-Objekt.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) verwendet die base64ToJson-Funktion zum Konvertieren eines base64-Werts:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konvertiert eine base64-Darstellung in eine Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |Zeichenfolge |Die in eine Zeichenfolge zu konvertierende base64-Darstellung. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des konvertierten base64-Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) verwendet die base64ToString-Funktion zum Konvertieren eines base64-Werts:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| base64Output | String | b25lLCB0d28sIHRocmVl |
| toStringOutput | String | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Kombiniert mehrere Zeichenfolgenwerte und gibt die verkettete Zeichenfolge zurück oder kombiniert mehrere Arrays und gibt das verkettete Array zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge oder Array |Die erste Zeichenfolge oder das erste Array für die Verkettung. |
| zusätzliche Argumente |Nein |Zeichenfolge oder Array |Weitere Zeichenfolgen oder Arrays in sequenzieller Reihenfolge für die Verkettung. |

Diese Funktion akzeptiert eine beliebige Anzahl von Argumenten und Zeichenfolgen oder Arrays für die Parameter. Sie können jedoch nicht sowohl Arrays als auch Zeichenfolgen für Parameter angeben. Zeichenfolgen werden nur mit anderen Zeichenfolgen verkettet.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge oder ein Array aus verketteten Werten.

### <a name="examples"></a>Beispiele

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

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) zeigt, wie zwei Arrays kombiniert werden.

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

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | Beschreibung |
|:--- |:--- |:--- |:--- |
| Container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) zeigt die Verwendung von „contains“ mit unterschiedlichen Typen:

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konvertiert einen Wert in einen Daten-URI.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ja |Zeichenfolge |Der Wert, der in einen Daten-URI konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine als Daten-URI formatierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konvertiert einen Wert in einen Daten-URI und einen Daten-URI in eine Zeichenfolge:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konvertiert einen als Daten-URI formatierten Wert in eine Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ja |Zeichenfolge |Der zu konvertierende Daten-URI-Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge, die den konvertierten Wert enthält.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konvertiert einen Wert in einen Daten-URI und einen Daten-URI in eine Zeichenfolge:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| dataUriOutput | String | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | String | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den überprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) überprüft, ob ein Array, ein Objekt und eine Zeichenfolge leer sind.

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Bestimmt, ob eine Zeichenfolge mit einem Wert endet. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das letzte Zeichen oder die letzten Zeichen der Zeichenfolge mit dem Wert übereinstimmen, andernfalls **False**.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) zeigt die Verwendung der Funktionen startsWith und endsWith:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Gibt das erste Zeichen der Zeichenfolge oder das erste Element des Arrays zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen erstes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Ein Zeichenfolgenwert, der dem letzten Zeichen entspricht, bzw. der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des ersten Elements in einem Array.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) zeigt die Verwendung der first-Funktion mit einem Array und einer Zeichenfolge.

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

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Erstellt eine formatierte Zeichenfolge aus Eingabewerten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| formatString | Ja | Zeichenfolge | Die zusammengesetzte Formatzeichenfolge. |
| arg1 | Ja | Zeichenfolge, Integer oder boolescher Wert | Der Wert, der in die formatierte Zeichenfolge aufgenommen werden soll. |
| zusätzliche Argumente | Nein | Zeichenfolge, Integer oder boolescher Wert | Weitere Werte, die in die formatierte Zeichenfolge aufgenommen werden sollen. |

### <a name="remarks"></a>Bemerkungen

Verwenden Sie diese Funktion zum Formatieren einer Zeichenfolge in Ihrer Vorlage. Sie verwendet die gleichen Formatierungsoptionen wie die [System.String.Format](/dotnet/api/system.string.format)-Methode in .NET.

### <a name="examples"></a>Beispiele

Die folgende Beispielvorlage zeigt die Verwendung der format-Funktion.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| formatTest | String | Hallo, Benutzer. Formatierte Zahl: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Erstellt einen Wert im Format eines Globally Unique Identifiers basierend auf den als Parameter angegebenen Werten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseString |Ja |Zeichenfolge |Der in der Hashfunktion für die Erstellung des GUID verwendete Wert. |
| Zusätzliche Parameter nach Bedarf. |Nein |Zeichenfolge |Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt. |

### <a name="remarks"></a>Bemerkungen

Diese Funktion ist hilfreich, wenn Sie einen Wert im Format eines Globally Unique Identifiers erstellen müssen. Sie geben Parameterwerte an, die den Eindeutigkeitsbereich für das Ergebnis einschränken. Sie können angeben, ob der Name bis hinunter zum Abonnement, zur Ressourcengruppe oder zur Bereitstellung eindeutig ist.

Der zurückgegebene Wert ist keine zufällige Zeichenfolge, sondern das Ergebnis einer Hashfunktion, die für die Parameter ausgeführt wurde. Der zurückgegebene Wert ist 36 Zeichen lang. Er ist nicht global eindeutig. Verwenden Sie die [newGuid](#newguid)-Funktion, um eine neue GUID zu erstellen, die nicht auf diesem Hashwert der Parameter basiert.

Die folgenden Beispiele zeigen, wie Sie mithilfe des GUID einen eindeutigen Wert für häufig verwendete Ebenen erstellen können.

Eindeutige Zuordnung zum Abonnement

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Eindeutige Zuordnung zur Ressourcengruppe

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Eindeutige Zuordnung zur Bereitstellung für eine Ressourcengruppe

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 36 Zeichen im Format eines Globally Unique Identifiers.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) gibt Ergebnisse vom GUID zurück:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Gibt die erste Position eines Werts innerhalb einer Zeichenfolge zurück. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Position des zu suchenden Elements darstellt. Der Wert ist nullbasiert. Wenn das Element nicht gefunden wird, wird -1 zurückgegeben.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) zeigt die Verwendung der Funktionen indexOf und lastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Konvertiert eine gültige JSON-Zeichenfolge in einen JSON-Datentyp. Weitere Informationen finden Sie unter [json-Funktion](template-functions-object.md#json).

## <a name="last"></a>last

`last (arg1)`

Gibt das letzte Zeichen der Zeichenfolge bzw. das letzte Element des Arrays zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen letztes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Ein Zeichenfolgenwert, der dem letzten Zeichen entspricht, bzw. der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des letzten Elements in einem Array.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) zeigt die Verwendung der last-Funktion mit einem Array und einer Zeichenfolge.

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
output stringOutput string = last('One Two Three')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | String | three |
| stringOutput | String | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Gibt die letzte Position eines Werts innerhalb einer Zeichenfolge zurück. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die letzte Position des zu suchenden Elements darstellt. Der Wert ist nullbasiert. Wenn das Element nicht gefunden wird, wird -1 zurückgegeben.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) zeigt die Verwendung der Funktionen indexOf und lastIndexOf:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Gibt die Anzahl von Zeichen in einer Zeichenfolge, Elementen in einem Array oder Eigenschaften auf Stammebene in einem Objekt zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array, Zeichenfolge oder Objekt |Das Array, von dem die Anzahl der Elemente ermittelt werden soll, die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll, oder das Objekt, von dem die Anzahl der Eigenschaften auf Stammebene ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) zeigt die Verwendung von „length“ mit einem Array und einer Zeichenfolge:

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

## <a name="newguid"></a>newGuid

`newGuid()`

Gibt einen Wert im Format einer GUID (eindeutiger Bezeichner) zurück. **Diese Funktion kann nur für den Standardwert eines Parameters verwendet werden.**

### <a name="remarks"></a>Bemerkungen

Sie können diese Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwenden. Wenn diese Funktion an einer anderen Stelle in einer Vorlage verwendet wird, wird ein Fehler zurückgegeben. Die Funktion ist in anderen Teilen der Vorlage nicht zulässig, da bei jedem Aufruf ein anderer Wert zurückgegeben wird. Das Bereitstellen der gleichen Vorlage mit den gleichen Parametern würde nicht zuverlässig zu den gleichen Ergebnissen führen.

Im Gegensatz zur [guid](#guid)-Funktion verwendet die newGuid-Funktion keine Parameter. Wenn Sie die Funktion „guid“ mit demselben Parameter aufrufen, wird jedes Mal der gleiche Bezeichner zurückgegeben. Verwenden Sie die Funktion „guid“, wenn Sie die gleiche GUID für eine spezifische Umgebung zuverlässig generieren müssen. Verwenden Sie die Funktion „newGuid“, wenn Sie jedes Mal einen anderen Bezeichner benötigen, z. B. beim Bereitstellen von Ressourcen für eine Testumgebung.

Die newGuid-Funktion verwendet die [Guid-Struktur](/dotnet/api/system.guid) im .NET Framework, um den global eindeutigen Bezeichner (GUID) zu generieren.

Wenn Sie die [Option zum erneuten Bereitstellen einer vorherigen erfolgreichen Bereitstellung](rollback-on-error.md) verwenden und die vorherige Bereitstellung einen Parameter enthält, der die Funktion „newGuid“ nutzt, wird dieser Parameter nicht erneut ausgewertet. Stattdessen wird der Parameterwert der vorherigen Bereitstellung in der Rollbackbereitstellung automatisch wiederverwendet.

In einer Testumgebung müssen Sie Ressourcen, die nur kurz gespeichert werden, möglicherweise wiederholt bereitstellen. Anstatt eindeutige Namen zu erstellen, können Sie die Funktion „newGuid“ mit [uniqueString](#uniquestring) verwenden, um eindeutige Namen zu erstellen.

Seien Sie vorsichtig, wenn Sie eine Vorlage erneut bereitstellen, die die newGuid-Funktion für einen Standardwert nutzt. Wenn Sie die erneute Bereitstellung durchführen und keinen Wert für den Parameter bereitstellen, wird die Funktion erneut ausgewertet. Wenn Sie eine vorhandene Ressource aktualisieren möchten, anstatt eine neue zu erstellen, übergeben Sie den Parameterwert aus der früheren Bereitstellung.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 36 Zeichen im Format eines Globally Unique Identifiers.

### <a name="examples"></a>Beispiele

In der folgenden Beispielvorlage wird ein Parameter mit einem neuen Bezeichner veranschaulicht.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| Name | type | Wert |
| ---- | ---- | ----- |
| guidOutput | Zeichenfolge | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Im folgenden Beispiel wird die newGuid-Funktion verwendet, um einen eindeutigen Namen für ein Speicherkonto zu erstellen. Diese Vorlage funktioniert möglicherweise in Testumgebungen, in denen das Speicherkonto nur für kurze Zeit vorhanden ist und nicht erneut bereitgestellt wird.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = concat('storage', uniqueString(guidValue))

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| Name | type | Wert |
| ---- | ---- | ----- |
| nameOutput | Zeichenfolge | storagenziwvyru7uxie |

## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Gibt eine rechtsbündig ausgerichtete Zeichenfolge zurück, indem links Zeichen hinzugefügt werden, bis die angegebene Gesamtlänge erreicht ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der rechtsbündig ausgerichtet werden soll. |
| totalLength |Ja |INT |Die Gesamtzahl der Zeichen in der zurückgegebenen Zeichenfolge. |
| paddingCharacter |Nein |einzelnes Zeichen |Das Zeichen, das für das Auffüllen auf der linken Seite verwendet werden soll, bis die Gesamtlänge erreicht ist. Der Standardwert ist ein Leerzeichen. |

Wenn die Länge der ursprünglichen Zeichenfolge die Anzahl der aufzufüllenden Zeichen überschreitet, werden keine Zeichen hinzugefügt.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit mindestens der angegebenen Zeichenanzahl.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) zeigt, wie Sie den vom Benutzer angegebenen Parameterwert auffüllen, indem Sie das Nullzeichen hinzufügen, bis er die Gesamtzahl der Zeichen erreicht.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| stringOutput | String | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Gibt eine neue Zeichenfolge zurück, in der alle Instanzen einer Zeichenfolge durch eine andere Zeichenfolge ersetzt wurden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalString |Ja |Zeichenfolge |Der Wert, für den alle Instanzen einer Zeichenfolge durch eine andere Zeichenfolge ersetzt wurden. |
| oldString |Ja |Zeichenfolge |Die Zeichenfolge, die aus der ursprünglichen Zeichenfolge entfernt werden soll. |
| newString |Ja |Zeichenfolge |Die Zeichenfolge, die anstelle der entfernten Zeichenfolge eingefügt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit den ersetzten Zeichen.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) zeigt, wie Sie aus einer von einem Benutzer bereitgestellten Zeichenfolge alle Gedankenstriche entfernen und einen Teil der Zeichenfolge durch eine andere Zeichenfolge ersetzen.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstOutput | String | 1231231234 |
| secondOutput | String | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Gibt eine Zeichenfolge mit allen Zeichen gemäß der angegebenen Anzahl von Zeichen oder ein Array mit allen Elementen gemäß der angegebenen Anzahl von Elementen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Array oder Zeichenfolge, wo Elemente übersprungen werden sollen. |
| numberToSkip |Ja |INT |Die Anzahl der zu überspringenden Elemente bzw. Zeichen. Wenn dieser Wert 0 (null) oder kleiner ist, werden alle Elemente oder Zeichen in dem Wert zurückgegeben. Ist er größer als die Länge des Arrays bzw. der Zeichenfolge, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="examples"></a>Beispiele

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) wird die angegebene Anzahl von Elementen im Array und von Zeichen in einer Zeichenfolge übersprungen.

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

## <a name="split"></a>split

`split(inputString, delimiter)`

Gibt ein Array mit Zeichenfolgen zurück, das die Teilzeichenfolgen der Eingabezeichenfolge getrennt durch die angegebenen Trennzeichen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| inputString |Ja |Zeichenfolge |Die zu teilende Zeichenfolge. |
| Trennzeichen |Ja |Zeichenfolge oder Array von Zeichenfolgen |Das Trennzeichen, das zum Teilen der Zeichenfolge verwendet werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Array der Zeichenfolgen.

### <a name="examples"></a>Beispiele

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) wird die Eingabezeichenfolge mit einem Komma sowie entweder mit einem Komma oder Semikolon getrennt.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Stellt fest, ob eine Zeichenfolge mit einem bestimmten Wert beginnt. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Zeichen oder die ersten Zeichen der Zeichenfolge mit dem Wert übereinstimmen, andernfalls **False**.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) zeigt die Verwendung der Funktionen startsWith und endsWith:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>Zeichenfolge

`string(valueToConvert)`

Konvertiert den angegebenen Wert in eine Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Any |Der Wert, der in eine Zeichenfolge konvertiert werden soll. Werte aller Typen können konvertiert werden, auch Objekte und Arrays. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des konvertierten Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) zeigt das Konvertieren verschiedener Typen von Werten in Zeichenfolgen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| objectOutput | String | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | String | ["a","b","c"] |
| intOutput | String | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Gibt eine Teilzeichenfolge zurück, die an der angegebenen Zeichenposition beginnt und die angegebene Anzahl von Zeichen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |Zeichenfolge |Die ursprüngliche Zeichenfolge, aus der die Teilzeichenfolge extrahiert wird. |
| startIndex |Nein |INT |Die nullbasierte Anfangsposition für die Teilzeichenfolge. |
| length |Nein |INT |Die Anzahl der Zeichen der Teilzeichenfolge. Muss auf eine Position innerhalb der Zeichenfolge verweisen. Muss Null (0) oder größer sein. |

### <a name="return-value"></a>Rückgabewert

Die Teilzeichenfolge. Oder eine leere Zeichenfolge, wenn die Länge Null (0) entspricht.

### <a name="remarks"></a>Bemerkungen

Die Funktion schlägt fehl, wenn sich die Teilzeichenfolge über das Ende der Zeichenfolge erstreckt, oder wenn die Länge kleiner als Null (0) ist. Das folgende Beispiel führt zu dem Fehler „Die Parameter "index" und "length" müssen auf eine Position innerhalb der Zeichenfolge verweisen. Indexparameter: '0', Längenparameter: '11', Länge des Zeichenfolgenparameters: '10'.".

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extrahiert eine Teilzeichenfolge aus einem Parameter.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| substringOutput | String | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Gibt eine Zeichenfolge mit der angegebenen Anzahl von Zeichen ab dem Anfang der Zeichenfolge bzw. ein Array mit der angegebenen Anzahl von Elementen ab dem Anfang des Arrays zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Das Array bzw. die Zeichenfolge, wo die Elemente entnommen werden sollen. |
| numberToTake |Ja |INT |Die Anzahl der zu entnehmenden Elemente bzw. Zeichen. Ist dieser Wert 0 oder kleiner, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. Ist er größer als die Länge des entsprechenden Arrays bzw. der Zeichenfolge, werden alle Elemente des Arrays bzw. der Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="examples"></a>Beispiele

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) wird die angegebene Anzahl von Elementen aus dem Array und von Zeichen aus einer Zeichenfolge entnommen.

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
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 2

output arrayOutput array = take(testArray, elementsToSkip)
output stringOutput string = take(testString, charactersToSkip)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | String | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konvertiert die angegebene Zeichenfolge in Kleinbuchstaben.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |Zeichenfolge |Der Wert, der in Kleinbuchstaben konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Die in Kleinbuchstaben konvertierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konvertiert einen Parameterwert in Klein- und Großbuchstaben.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konvertiert die angegebene Zeichenfolge in Großbuchstaben.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |Zeichenfolge |Der Wert, der in Großbuchstaben konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Die in Großbuchstaben konvertierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konvertiert einen Parameterwert in Klein- und Großbuchstaben.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| toLowerOutput | String | one two three |
| toUpperOutput | String | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Entfernt alle führenden und nachgestellten Leerzeichen aus der angegebenen Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |Zeichenfolge |Der zu kürzende Wert. |

### <a name="return-value"></a>Rückgabewert

Die Zeichenfolge ohne vorangestellte oder nachstehende Leerzeichen.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) entfernt die Leerzeichen aus dem Parameter.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| return | String | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Erstellt auf der Grundlage der als Parameter angegebenen Werte eine deterministische Hashzeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseString |Ja |Zeichenfolge |Der Wert, der in der Hashfunktion verwendet wird, um eine eindeutige Zeichenfolge zu erstellen. |
| Zusätzliche Parameter nach Bedarf. |Nein |Zeichenfolge |Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt. |

### <a name="remarks"></a>Bemerkungen

Diese Funktion ist hilfreich, wenn Sie einen eindeutigen Namen für eine Ressource erstellen müssen. Sie geben Parameterwerte an, die den Eindeutigkeitsbereich für das Ergebnis einschränken. Sie können angeben, ob der Name bis hinunter zum Abonnement, zur Ressourcengruppe oder zur Bereitstellung eindeutig ist.

Der zurückgegebene Wert ist keine zufällige Zeichenfolge, sondern das Ergebnis einer Hashfunktion. Der zurückgegebene Wert ist 13 Zeichen lang. Er ist nicht global eindeutig. Es empfiehlt sich, den Wert mit einem Präfix aus Ihrer Benennungskonvention zu kombinieren, um einen aussagekräftigen Namen zu erstellen. Im folgenden Beispiel wird das Format des zurückgegebenen Werts veranschaulicht. Der tatsächliche Wert variiert je nach den angegebenen Parametern.

`tcvhiyu5h2o5o`

Die folgenden Beispiele zeigen, wie Sie mithilfe von uniqueString einen eindeutigen Wert für häufig verwendete Ebenen erstellen können.

Eindeutige Zuordnung zum Abonnement

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Eindeutige Zuordnung zur Ressourcengruppe

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Eindeutige Zuordnung zur Bereitstellung für eine Ressourcengruppe

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

Das folgende Beispiel zeigt, wie Sie einen eindeutigen Namen für ein Speicherkonto auf Grundlage seiner Ressourcengruppe erstellen. Innerhalb der Ressourcengruppe ist der Name nicht eindeutig, wenn er auf die gleiche Weise erstellt wird.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: concat('storage, uniqueString(resourceGroup().id)')
  ...
}
```

---

Wenn Sie bei jeder Bereitstellung einer Vorlage einen neuen eindeutigen Namen erstellen müssen und die Ressource nicht aktualisieren möchten, können Sie die [utcNow](template-functions-date.md#utcnow)-Funktion mit uniqueString verwenden. Diesen Ansatz können Sie in einer Testumgebung verwenden. Ein Beispiel finden Sie unter [utcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 13 Zeichen.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) gibt die Ergebnisse von uniqueString zurück:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Erstellt einen absoluten URI durch Kombinieren der baseUri- und der relativeUri-Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |Zeichenfolge |Die Zeichenfolge mit dem Basis-URI. Achten Sie darauf, das Verhalten bezüglich der Behandlung des nachgestellten Schrägstrichs („/“) zu beobachten, wie im Anschluss auf die folgende Tabelle beschrieben.  |
| relativeUri |Ja |Zeichenfolge |Der Zeichenfolge mit dem relativen URI, die der Zeichenfolge mit dem Basis-URI hinzugefügt werden soll. |

* Wenn **BaseUri** mit einem nachgestellten Schrägstrich endet, ist das Ergebnis einfach **BaseUri**, gefolgt von **relativeUri**.

* Wenn **baseUri** nicht mit einem nachgestellten Schrägstrich endet, gibt es zwei Möglichkeiten.

   * Wenn **baseUri** gar keine Schrägstriche aufweist (abgesehen von „//“ in der Nähe des Anfangs), ist das Ergebnis einfach **baseUri**, gefolgt von **relativeUri**.

   * Wenn **baseUri** einige Schrägstriche aufweist, aber nicht mit einem Schrägstrich endet, wird alles ab dem letzten Schrägstrich aus **baseUri** entfernt, und das Ergebnis ist **baseUri**, gefolgt von **relativeUri**.

Im Folgenden finden Sie einige Beispiele:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Vollständige Details: Die Parameter **baseUri** und **relativeUri** werden wie in [RFC 3986, Abschnitt 5](https://tools.ietf.org/html/rfc3986#section-5) angegeben aufgelöst.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge, die den absoluten URI für den Basis- und relativen URI-Wert darstellt.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird veranschaulicht, wie basierend auf dem Wert der übergeordneten Vorlage eine Verknüpfung zu einer geschachtelten Vorlage erstellt wird.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) zeigt die Verwendung von „uri“, uriComponent und uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codiert einen URI.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |Zeichenfolge |Der zu codierende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des als URI codierten Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) zeigt die Verwendung von „uri“, uriComponent und uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Gibt eine Zeichenfolge des als URI codierten Werts zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | type | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |Zeichenfolge |Der als URI codierte Wert, der in eine Zeichenfolge konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine decodierte Zeichenfolge des als URI codierten Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) zeigt die Verwendung von „uri“, uriComponent und uriComponentToString:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| Name | type | Wert |
| ---- | ---- | ----- |
| uriOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |
| componentOutput | String | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | String | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](template-syntax.md).
* Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](linked-templates.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](copy-resources.md).
* Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen ](deploy-powershell.md).