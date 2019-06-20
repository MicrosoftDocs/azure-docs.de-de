---
title: Azure Resource Manager-Vorlagenfunktionen – Zeichenfolge | Microsoft-Dokumentation
description: Hier werden die Funktionen beschrieben, die in einer Azure Resource Manager-Vorlage zum Arbeiten mit Zeichenfolgen verwendet werden können.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: tomfitz
ms.openlocfilehash: 82b9403a3d5a5b6938f5b95bbfce888d1e70e451
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431212"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Zeichenfolgenfunktionen für Azure Resource Manager-Vorlagen

Der Ressourcen-Manager stellt die folgenden Funktionen für das Arbeiten mit Zeichenfolgen bereit:

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
* [last](#last)
* [lastIndexOf](#lastindexof)
* [Länge](#length)
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
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Rückkehr zur base64-Darstellung der Eingabezeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| inputString |Ja |Zeichenfolge |Der Wert, der als base64-Darstellung zurückgegeben wird. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit der base64-Darstellung.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) zeigt die Nutzungsweise der base64-Funktion.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| base64Output | Zeichenfolge | b25lLCB0d28sIHRocmVl |
| toStringOutput | Zeichenfolge | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Konvertiert eine base64-Darstellung in ein JSON-Objekt.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |Zeichenfolge |Die in ein JSON-Objekt zu konvertierende base64-Darstellung. |

### <a name="return-value"></a>Rückgabewert

Ein JSON-Objekt.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) verwendet die base64ToJson-Funktion zum Konvertieren eines base64-Werts:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| base64Output | Zeichenfolge | b25lLCB0d28sIHRocmVl |
| toStringOutput | Zeichenfolge | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Konvertiert eine base64-Darstellung in eine Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base64Value |Ja |Zeichenfolge |Die in eine Zeichenfolge zu konvertierende base64-Darstellung. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des konvertierten base64-Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) verwendet die base64ToString-Funktion zum Konvertieren eines base64-Werts:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| base64Output | Zeichenfolge | b25lLCB0d28sIHRocmVl |
| toStringOutput | Zeichenfolge | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Kombiniert mehrere Zeichenfolgenwerte und gibt die verkettete Zeichenfolge zurück oder kombiniert mehrere Arrays und gibt das verkettete Array zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Zeichenfolge oder Array |Der erste Wert für die Verkettung. |
| zusätzliche Argumente |Nein |Zeichenfolge |Weitere Werte in sequenzieller Reihenfolge für die Verkettung. |

### <a name="return-value"></a>Rückgabewert
Eine Zeichenfolge oder ein Array aus verketteten Werten.

### <a name="examples"></a>Beispiele

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

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Überprüft, ob ein Array einen Wert enthält, ein Objekt einen Schlüssel enthält oder eine Zeichenfolge eine Teilzeichenfolge enthält. Die Groß-/Kleinschreibung wird beim Zeichenfolgenvergleich beachtet. Wenn Sie jedoch testen, ob ein Objekt einen Schlüssel enthält, wird die Groß-/Kleinschreibung beim Vergleich nicht beachtet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| container |Ja |Array, Objekt oder Zeichenfolge |Der Wert, der den zu suchenden Wert enthält. |
| itemToFind |Ja |Zeichenfolge oder ganze Zahl |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Element gefunden wurde; andernfalls **False**.

### <a name="examples"></a>Beispiele

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

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Konvertiert einen Wert in einen Daten-URI.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToConvert |Ja |Zeichenfolge |Der Wert, der in einen Daten-URI konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine als Daten-URI formatierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konvertiert einen Wert in einen Daten-URI und einen Daten-URI in eine Zeichenfolge:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| dataUriOutput | Zeichenfolge | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Zeichenfolge | Hello, World! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Konvertiert einen als Daten-URI formatierten Wert in eine Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Ja |Zeichenfolge |Der zu konvertierende Daten-URI-Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge, die den konvertierten Wert enthält.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) konvertiert einen Wert in einen Daten-URI und einen Daten-URI in eine Zeichenfolge:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| dataUriOutput | Zeichenfolge | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Zeichenfolge | Hello, World! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bestimmt, ob ein Array, Objekt oder eine Zeichenfolge leer ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| itemToTest |Ja |Array, Objekt oder Zeichenfolge |Der Wert, für den überprüft werden soll, ob er leer ist. |

### <a name="return-value"></a>Rückgabewert

Gibt **True** zurück, wenn der Werte leer ist. Andernfalls wird **False** zurückgegeben.

### <a name="examples"></a>Beispiele

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

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Bestimmt, ob eine Zeichenfolge mit einem Wert endet. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das letzte Zeichen oder die letzten Zeichen der Zeichenfolge mit dem Wert übereinstimmen, andernfalls **False**.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) zeigt die Verwendung der Funktionen startsWith und endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen erstes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Ein Zeichenfolgenwert, der dem letzten Zeichen entspricht, bzw. der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des ersten Elements in einem Array.

### <a name="examples"></a>Beispiele

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

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Erstellt eine formatierte Zeichenfolge aus Eingabewerten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| formatString | Ja | Zeichenfolge | Die zusammengesetzte Formatzeichenfolge. |
| arg1 | Ja | Zeichenfolge, Integer oder boolescher Wert | Der Wert, der in die formatierte Zeichenfolge aufgenommen werden soll. |
| zusätzliche Argumente | Nein | Zeichenfolge, Integer oder boolescher Wert | Weitere Werte, die in die formatierte Zeichenfolge aufgenommen werden sollen. |

### <a name="remarks"></a>Anmerkungen

Verwenden Sie diese Funktion zum Formatieren einer Zeichenfolge in Ihrer Vorlage. Sie verwendet die gleichen Formatierungsoptionen wie die [System.String.Format](/dotnet/api/system.string.format)-Methode in .NET.

### <a name="examples"></a>Beispiele

Die folgende Beispielvorlage zeigt die Verwendung der format-Funktion.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| formatTest | Zeichenfolge | Hallo, Benutzer. Formatierte Zahl: 8.175.133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

Erstellt einen Wert im Format eines Globally Unique Identifiers basierend auf den als Parameter angegebenen Werten.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseString |Ja |Zeichenfolge |Der in der Hashfunktion für die Erstellung des GUID verwendete Wert. |
| Zusätzliche Parameter nach Bedarf. |Nein |Zeichenfolge |Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt. |

### <a name="remarks"></a>Anmerkungen

Diese Funktion ist hilfreich, wenn Sie einen Wert im Format eines Globally Unique Identifiers erstellen müssen. Sie geben Parameterwerte an, die den Eindeutigkeitsbereich für das Ergebnis einschränken. Sie können angeben, ob der Name bis hinunter zum Abonnement, zur Ressourcengruppe oder zur Bereitstellung eindeutig ist.

Der zurückgegebene Wert ist keine zufällige Zeichenfolge, sondern das Ergebnis einer Hashfunktion, die für die Parameter ausgeführt wurde. Der zurückgegebene Wert ist 36 Zeichen lang. Er ist nicht global eindeutig. Verwenden Sie die [newGuid](#newguid)-Funktion, um eine neue GUID zu erstellen, die nicht auf diesem Hashwert der Parameter basiert.

Die folgenden Beispiele zeigen, wie Sie mithilfe des GUID einen eindeutigen Wert für häufig verwendete Ebenen erstellen können.

Eindeutige Zuordnung zum Abonnement

```json
"[guid(subscription().subscriptionId)]"
```

Eindeutige Zuordnung zur Ressourcengruppe

```json
"[guid(resourceGroup().id)]"
```

Eindeutige Zuordnung zur Bereitstellung für eine Ressourcengruppe

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 36 Zeichen im Format eines Globally Unique Identifiers.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) gibt Ergebnisse vom GUID zurück:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Gibt die erste Position eines Werts innerhalb einer Zeichenfolge zurück. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die Position des zu suchenden Elements darstellt. Der Wert ist nullbasiert. Wenn das Element nicht gefunden wird, wird -1 zurückgegeben.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) zeigt die Verwendung der Funktionen indexOf und lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="last"></a>last

`last (arg1)`

Gibt das letzte Zeichen der Zeichenfolge bzw. das letzte Element des Arrays zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Der Wert, dessen letztes Element oder Zeichen abgerufen wird. |

### <a name="return-value"></a>Rückgabewert

Ein Zeichenfolgenwert, der dem letzten Zeichen entspricht, bzw. der Typ (Zeichenfolge, ganze Zahl, Array oder Objekt) des letzten Elements in einem Array.

### <a name="examples"></a>Beispiele

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

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Gibt die letzte Position eines Werts innerhalb einer Zeichenfolge zurück. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl, die die letzte Position des zu suchenden Elements darstellt. Der Wert ist nullbasiert. Wenn das Element nicht gefunden wird, wird -1 zurückgegeben.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) zeigt die Verwendung der Funktionen indexOf und lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| NotFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Gibt die Anzahl von Zeichen in einer Zeichenfolge bzw. von Elementen in einem Array zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| arg1 |Ja |Array oder Zeichenfolge |Das Array, von dem die Anzahl der Elemente, bzw. die Zeichenfolge, von der die Anzahl der Zeichen ermittelt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine ganze Zahl. 

### <a name="examples"></a>Beispiele

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

## <a name="newguid"></a>newGuid

`newGuid()`

Gibt einen Wert im Format einer GUID (eindeutiger Bezeichner) zurück. **Diese Funktion kann nur für den Standardwert eines Parameters verwendet werden.**

### <a name="remarks"></a>Anmerkungen

Sie können diese Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwenden. Wenn diese Funktion an einer anderen Stelle in einer Vorlage verwendet wird, wird ein Fehler zurückgegeben. Die Funktion ist in anderen Teilen der Vorlage nicht zulässig, da bei jedem Aufruf ein anderer Wert zurückgegeben wird. Das Bereitstellen der gleichen Vorlage mit den gleichen Parametern würde nicht zuverlässig zu den gleichen Ergebnissen führen.

Im Gegensatz zur [guid](#guid)-Funktion verwendet die newGuid-Funktion keine Parameter. Wenn Sie die Funktion „guid“ mit demselben Parameter aufrufen, wird jedes Mal der gleiche Bezeichner zurückgegeben. Verwenden Sie die Funktion „guid“, wenn Sie die gleiche GUID für eine spezifische Umgebung zuverlässig generieren müssen. Verwenden Sie die Funktion „newGuid“, wenn Sie jedes Mal einen anderen Bezeichner benötigen, z. B. beim Bereitstellen von Ressourcen für eine Testumgebung.

Wenn Sie die [Option zum erneuten Bereitstellen einer vorherigen erfolgreichen Bereitstellung](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails) verwenden und die vorherige Bereitstellung einen Parameter enthält, der die Funktion „newGuid“ nutzt, wird dieser Parameter nicht erneut ausgewertet. Stattdessen wird der Parameterwert der vorherigen Bereitstellung in der Rollbackbereitstellung automatisch wiederverwendet.

In einer Testumgebung müssen Sie Ressourcen, die nur kurz gespeichert werden, möglicherweise wiederholt bereitstellen. Anstatt eindeutige Namen zu erstellen, können Sie die Funktion „newGuid“ mit [uniqueString](#uniquestring) verwenden, um eindeutige Namen zu erstellen.

Seien Sie vorsichtig, wenn Sie eine Vorlage erneut bereitstellen, die die newGuid-Funktion für einen Standardwert nutzt. Wenn Sie die erneute Bereitstellung durchführen und keinen Wert für den Parameter bereitstellen, wird die Funktion erneut ausgewertet. Wenn Sie eine vorhandene Ressource aktualisieren möchten, anstatt eine neue zu erstellen, übergeben Sie den Parameterwert aus der früheren Bereitstellung.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 36 Zeichen im Format eines Globally Unique Identifiers.

### <a name="examples"></a>Beispiele

In der folgenden Beispielvorlage wird ein Parameter mit einem neuen Bezeichner veranschaulicht.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| guidOutput | Zeichenfolge | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Im folgenden Beispiel wird die newGuid-Funktion verwendet, um einen eindeutigen Namen für ein Speicherkonto zu erstellen. Diese Vorlage funktioniert möglicherweise in Testumgebungen, in denen das Speicherkonto nur für kurze Zeit vorhanden ist und nicht erneut bereitgestellt wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "sku":{
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

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| nameOutput | Zeichenfolge | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Gibt eine rechtsbündig ausgerichtete Zeichenfolge zurück, indem links Zeichen hinzugefügt werden, bis die angegebene Gesamtlänge erreicht ist.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToPad |Ja |Zeichenfolge oder ganze Zahl |Der Wert, der rechtsbündig ausgerichtet werden soll. |
| totalLength |Ja |int |Die Gesamtzahl der Zeichen in der zurückgegebenen Zeichenfolge. |
| paddingCharacter |Nein |einzelnes Zeichen |Das Zeichen, das für das Auffüllen auf der linken Seite verwendet werden soll, bis die Gesamtlänge erreicht ist. Der Standardwert ist ein Leerzeichen. |

Wenn die Länge der ursprünglichen Zeichenfolge die Anzahl der aufzufüllenden Zeichen überschreitet, werden keine Zeichen hinzugefügt.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit mindestens der angegebenen Zeichenanzahl.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) zeigt, wie Sie den vom Benutzer angegebenen Parameterwert auffüllen, indem Sie das Nullzeichen hinzufügen, bis er die Gesamtzahl der Zeichen erreicht. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| stringOutput | Zeichenfolge | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Gibt eine neue Zeichenfolge zurück, in der alle Instanzen einer Zeichenfolge durch eine andere Zeichenfolge ersetzt wurden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalString |Ja |Zeichenfolge |Der Wert, für den alle Instanzen einer Zeichenfolge durch eine andere Zeichenfolge ersetzt wurden. |
| oldString |Ja |Zeichenfolge |Die Zeichenfolge, die aus der ursprünglichen Zeichenfolge entfernt werden soll. |
| newString |Ja |Zeichenfolge |Die Zeichenfolge, die anstelle der entfernten Zeichenfolge eingefügt werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit den ersetzten Zeichen.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) zeigt, wie Sie aus einer von einem Benutzer bereitgestellten Zeichenfolge alle Gedankenstriche entfernen und einen Teil der Zeichenfolge durch eine andere Zeichenfolge ersetzen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| firstOutput | Zeichenfolge | 1231231234 |
| secondOutput | Zeichenfolge | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Gibt eine Zeichenfolge mit allen Zeichen gemäß der angegebenen Anzahl von Zeichen oder ein Array mit allen Elementen gemäß der angegebenen Anzahl von Elementen zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Array oder Zeichenfolge, wo Elemente übersprungen werden sollen. |
| numberToSkip |Ja |int |Die Anzahl der zu überspringenden Elemente bzw. Zeichen. Wenn dieser Wert 0 (null) oder kleiner ist, werden alle Elemente oder Zeichen in dem Wert zurückgegeben. Ist er größer als die Länge des Arrays bzw. der Zeichenfolge, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="examples"></a>Beispiele

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

## <a name="split"></a>split

`split(inputString, delimiter)`

Gibt ein Array mit Zeichenfolgen zurück, das die Teilzeichenfolgen der Eingabezeichenfolge getrennt durch die angegebenen Trennzeichen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| inputString |Ja |Zeichenfolge |Die zu teilende Zeichenfolge. |
| Trennzeichen |Ja |Zeichenfolge oder Array von Zeichenfolgen |Das Trennzeichen, das zum Teilen der Zeichenfolge verwendet werden soll. |

### <a name="return-value"></a>Rückgabewert

Ein Array der Zeichenfolgen.

### <a name="examples"></a>Beispiele

In der folgenden [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) wird die Eingabezeichenfolge mit einem Komma sowie entweder mit einem Komma oder Semikolon getrennt.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Stellt fest, ob eine Zeichenfolge mit einem bestimmten Wert beginnt. Bei dem Vergleich wird Groß- und Kleinschreibung nicht unterschieden.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToSearch |Ja |Zeichenfolge |Der Wert, der das zu suchende Element enthält. |
| stringToFind |Ja |Zeichenfolge |Der zu suchende Wert. |

### <a name="return-value"></a>Rückgabewert

**True**, wenn das Zeichen oder die ersten Zeichen der Zeichenfolge mit dem Wert übereinstimmen, andernfalls **False**.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) zeigt die Verwendung der Funktionen startsWith und endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| valueToConvert |Ja | Beliebig |Der Wert, der in eine Zeichenfolge konvertiert werden soll. Werte aller Typen können konvertiert werden, auch Objekte und Arrays. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des konvertierten Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) zeigt das Konvertieren verschiedener Typen von Werten in Zeichenfolgen:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| objectOutput | Zeichenfolge | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | Zeichenfolge | ["a","b","c"] |
| intOutput | Zeichenfolge | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Gibt eine Teilzeichenfolge zurück, die an der angegebenen Zeichenposition beginnt und die angegebene Anzahl von Zeichen enthält.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToParse |Ja |Zeichenfolge |Die ursprüngliche Zeichenfolge, aus der die Teilzeichenfolge extrahiert wird. |
| startIndex |Nein |int |Die nullbasierte Anfangsposition für die Teilzeichenfolge. |
| length |Nein |int |Die Anzahl der Zeichen der Teilzeichenfolge. Muss auf eine Position innerhalb der Zeichenfolge verweisen. Muss Null (0) oder größer sein. |

### <a name="return-value"></a>Rückgabewert

Die Teilzeichenfolge. Oder eine leere Zeichenfolge, wenn die Länge Null (0) entspricht.

### <a name="remarks"></a>Anmerkungen

Die Funktion schlägt fehl, wenn sich die Teilzeichenfolge über das Ende der Zeichenfolge erstreckt, oder wenn die Länge kleiner als Null (0) ist. Das folgende Beispiel führt zu dem Fehler „Die Parameter "index" und "length" müssen auf eine Position innerhalb der Zeichenfolge verweisen. Indexparameter: '0', Längenparameter: '11', Länge des Zeichenfolgenparameters: '10'.".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) extrahiert eine Teilzeichenfolge aus einem Parameter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| substringOutput | Zeichenfolge | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Gibt eine Zeichenfolge mit der angegebenen Anzahl von Zeichen ab dem Anfang der Zeichenfolge bzw. ein Array mit der angegebenen Anzahl von Elementen ab dem Anfang des Arrays zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| originalValue |Ja |Array oder Zeichenfolge |Das Array bzw. die Zeichenfolge, wo die Elemente entnommen werden sollen. |
| numberToTake |Ja |int |Die Anzahl der zu entnehmenden Elemente bzw. Zeichen. Ist dieser Wert 0 oder kleiner, wird ein leeres Array bzw. eine leere Zeichenfolge zurückgegeben. Ist er größer als die Länge des entsprechenden Arrays bzw. der Zeichenfolge, werden alle Elemente des Arrays bzw. der Zeichenfolge zurückgegeben. |

### <a name="return-value"></a>Rückgabewert

Ein Array oder eine Zeichenfolge.

### <a name="examples"></a>Beispiele

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

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Konvertiert die angegebene Zeichenfolge in Kleinbuchstaben.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |Zeichenfolge |Der Wert, der in Kleinbuchstaben konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Die in Kleinbuchstaben konvertierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konvertiert einen Parameterwert in Klein- und Großbuchstaben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| toLowerOutput | Zeichenfolge | one two three |
| toUpperOutput | Zeichenfolge | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Konvertiert die angegebene Zeichenfolge in Großbuchstaben.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToChange |Ja |Zeichenfolge |Der Wert, der in Großbuchstaben konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Die in Großbuchstaben konvertierte Zeichenfolge.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) konvertiert einen Parameterwert in Klein- und Großbuchstaben.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| toLowerOutput | Zeichenfolge | one two three |
| toUpperOutput | Zeichenfolge | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Entfernt alle führenden und nachgestellten Leerzeichen aus der angegebenen Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToTrim |Ja |Zeichenfolge |Der zu kürzende Wert. |

### <a name="return-value"></a>Rückgabewert

Die Zeichenfolge ohne vorangestellte oder nachstehende Leerzeichen.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) entfernt die Leerzeichen aus dem Parameter.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| return | Zeichenfolge | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Erstellt auf der Grundlage der als Parameter angegebenen Werte eine deterministische Hashzeichenfolge. 

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseString |Ja |Zeichenfolge |Der Wert, der in der Hashfunktion verwendet wird, um eine eindeutige Zeichenfolge zu erstellen. |
| Zusätzliche Parameter nach Bedarf. |Nein |Zeichenfolge |Sie können beliebig viele Zeichenfolgen hinzufügen, ganz wie sie zum Erstellen des Werts benötigt werden, der die Ebene der Eindeutigkeit angibt. |

### <a name="remarks"></a>Anmerkungen

Diese Funktion ist hilfreich, wenn Sie einen eindeutigen Namen für eine Ressource erstellen müssen. Sie geben Parameterwerte an, die den Eindeutigkeitsbereich für das Ergebnis einschränken. Sie können angeben, ob der Name bis hinunter zum Abonnement, zur Ressourcengruppe oder zur Bereitstellung eindeutig ist. 

Der zurückgegebene Wert ist keine zufällige Zeichenfolge, sondern das Ergebnis einer Hashfunktion. Der zurückgegebene Wert ist 13 Zeichen lang. Er ist nicht global eindeutig. Es empfiehlt sich, den Wert mit einem Präfix aus Ihrer Benennungskonvention zu kombinieren, um einen aussagekräftigen Namen zu erstellen. Im folgenden Beispiel wird das Format des zurückgegebenen Werts veranschaulicht. Der tatsächliche Wert variiert je nach den angegebenen Parametern.

    tcvhiyu5h2o5o

Die folgenden Beispiele zeigen, wie Sie mithilfe von uniqueString einen eindeutigen Wert für häufig verwendete Ebenen erstellen können.

Eindeutige Zuordnung zum Abonnement

```json
"[uniqueString(subscription().subscriptionId)]"
```

Eindeutige Zuordnung zur Ressourcengruppe

```json
"[uniqueString(resourceGroup().id)]"
```

Eindeutige Zuordnung zur Bereitstellung für eine Ressourcengruppe

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Das folgende Beispiel zeigt, wie Sie einen eindeutigen Namen für ein Speicherkonto auf Grundlage seiner Ressourcengruppe erstellen. Innerhalb der Ressourcengruppe ist der Name nicht eindeutig, wenn er auf die gleiche Weise erstellt wird.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Wenn Sie bei jeder Bereitstellung einer Vorlage einen neuen eindeutigen Namen erstellen müssen und die Ressource nicht aktualisieren möchten, können Sie die [utcNow](#utcnow)-Funktion mit uniqueString verwenden. Diesen Ansatz können Sie in einer Testumgebung verwenden. Ein Beispiel finden Sie unter [utcNow](#utcnow).

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge mit 13 Zeichen.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) gibt die Ergebnisse von uniqueString zurück:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Erstellt einen absoluten URI durch Kombinieren der baseUri- und der relativeUri-Zeichenfolge.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| baseUri |Ja |Zeichenfolge |Die Zeichenfolge mit dem Basis-URI. |
| relativeUri |Ja |Zeichenfolge |Der Zeichenfolge mit dem relativen URI, die der Zeichenfolge mit dem Basis-URI hinzugefügt werden soll. |

Der Wert für den **baseUri** -Parameter kann eine bestimmte Datei enthalten, beim Erstellen des URI wird jedoch nur der Basispfad verwendet. Beispielsweise führt das Übergeben von `http://contoso.com/resources/azuredeploy.json` als baseUri-Parameter zu einem Basis-URI von `http://contoso.com/resources/`.

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge, die den absoluten URI für den Basis- und relativen URI-Wert darstellt.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird veranschaulicht, wie basierend auf dem Wert der übergeordneten Vorlage eine Verknüpfung zu einer geschachtelten Vorlage erstellt wird.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) zeigt die Verwendung von „uri“, uriComponent und uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| uriOutput | Zeichenfolge | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Zeichenfolge | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Zeichenfolge | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codiert einen URI.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| stringToEncode |Ja |Zeichenfolge |Der zu codierende Wert. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge des als URI codierten Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) zeigt die Verwendung von „uri“, uriComponent und uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| uriOutput | Zeichenfolge | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Zeichenfolge | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Zeichenfolge | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Gibt eine Zeichenfolge des als URI codierten Werts zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Ja |Zeichenfolge |Der als URI codierte Wert, der in eine Zeichenfolge konvertiert werden soll. |

### <a name="return-value"></a>Rückgabewert

Eine decodierte Zeichenfolge des als URI codierten Werts.

### <a name="examples"></a>Beispiele

Die folgende [Beispielvorlage](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) zeigt die Verwendung von „uri“, uriComponent und uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Die Ausgabe aus dem vorherigen Beispiel mit den Standardwerten lautet:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| uriOutput | Zeichenfolge | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Zeichenfolge | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Zeichenfolge | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Gibt den aktuellen datetime-Wert (UTC) im festgelegten Format zurück. Wenn kein Format angegeben wird, wird das ISO 8601-Format (JJJJMMTTThhmmssZ) verwendet. **Diese Funktion kann nur für den Standardwert eines Parameters verwendet werden.**

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| format |Nein |Zeichenfolge |Der als URI codierte Wert, der in eine Zeichenfolge konvertiert werden soll. Verwenden Sie entweder [Standardformatzeichenfolgen](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) oder [benutzerdefinierte Formatzeichenfolgen](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Anmerkungen

Sie können diese Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwenden. Wenn diese Funktion an einer anderen Stelle in einer Vorlage verwendet wird, wird ein Fehler zurückgegeben. Die Funktion ist in anderen Teilen der Vorlage nicht zulässig, da bei jedem Aufruf ein anderer Wert zurückgegeben wird. Das Bereitstellen der gleichen Vorlage mit den gleichen Parametern würde nicht zuverlässig zu den gleichen Ergebnissen führen.

Wenn Sie die [Option zum erneuten Bereitstellen einer vorherigen erfolgreichen Bereitstellung](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails) verwenden und die vorherige Bereitstellung einen Parameter enthält, der die Funktion „utcNow“ nutzt, wird dieser Parameter nicht erneut ausgewertet. Stattdessen wird der Parameterwert der vorherigen Bereitstellung in der Rollbackbereitstellung automatisch wiederverwendet.

Seien Sie vorsichtig, wenn Sie eine Vorlage erneut bereitstellen, die die utcNow-Funktion für einen Standardwert nutzt. Wenn Sie die erneute Bereitstellung durchführen und keinen Wert für den Parameter bereitstellen, wird die Funktion erneut ausgewertet. Wenn Sie eine vorhandene Ressource aktualisieren möchten, anstatt eine neue zu erstellen, übergeben Sie den Parameterwert aus der früheren Bereitstellung.

### <a name="return-value"></a>Rückgabewert

Der aktuelle UTC-datetime-Wert

### <a name="examples"></a>Beispiele

In der folgenden Beispielvorlage werden verschiedene Formate für den datetime-Wert veranschaulicht.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| NAME | Type | Wert |
| ---- | ---- | ----- |
| utcOutput | Zeichenfolge | 20190305T175318Z |
| utcShortOutput | Zeichenfolge | 03/05/2019 |
| utcCustomOutput | Zeichenfolge | 3 5 |

Im folgenden Beispiel wird gezeigt, wie ein Wert der Funktion beim Festlegen eines Tagwerts verwendet wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Beschreibung der Abschnitte in einer Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
* Informationen zum Zusammenführen mehrerer Vorlagen finden Sie unter [Verwenden von verknüpften Vorlagen bei der Bereitstellung von Azure-Ressourcen](resource-group-linked-templates.md).
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).
* Informationen zum Bereitstellen der erstellten Vorlage finden Sie unter [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen ](resource-group-template-deploy.md).

