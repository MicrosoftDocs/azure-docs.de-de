---
title: Funktionen zum Erstellen von Benutzeroberflächendefinitionen in verwalteten Azure-Anwendungen | Microsoft-Dokumentation
description: Hier werden die Funktionen beschrieben, mit denen Benutzeroberflächendefinitionen für verwaltete Azure-Anwendungen erstellt werden.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587919"
---
# <a name="createuidefinition-functions"></a>CreateUiDefinition-Funktionen
Dieser Abschnitt enthält die Signaturen für alle unterstützten Funktionen einer CreateUiDefinition.

Setzen Sie die Deklaration in eckige Klammern, um eine Funktion zu verwenden. Beispiel:

```json
"[function()]"
```

Auf Zeichenfolgen und andere Funktionen kann als Parameter für eine Funktion verwiesen werden, Zeichenfolgen müssen jedoch in einfache Anführungszeichen gesetzt werden. Beispiel:

```json
"[fn1(fn2(), 'foobar')]"
```

Falls zutreffend, können Sie auf die Eigenschaften der Ausgabe einer Funktion mit dem Punktoperator verweisen. Beispiel:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Verweisfunktionen
Diese Funktionen können verwendet werden, um auf Ausgaben der Eigenschaften oder des Kontexts einer CreateUiDefinition zu verweisen.

### <a name="basics"></a>basics
Gibt die Ausgabewerte eines Elements zurück, das im Schritt „Basics“ definiert ist.

Im folgenden Beispiel wird die Ausgabe des Elements mit dem Namen `foo` im Schritt „Basics“ zurückgegeben:

```json
"[basics('foo')]"
```

### <a name="steps"></a>steps
Gibt die Ausgabewerte eines Elements zurück, das im angegebenen Schritt definiert ist. Wenn Sie die Ausgabewerte von Elementen im Schritt „Basics“ abrufen möchten, müssen Sie stattdessen `basics()` verwenden.

Im folgenden Beispiel wird die Ausgabe des Elements mit dem Namen `bar` im Schritt `foo` zurückgegeben:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Gibt den im Schritt „Basics“ oder im aktuellen Kontext ausgewählten Standort zurück.

Im folgenden Beispiel könnte `"westus"` zurückgegeben werden:

```json
"[location()]"
```

## <a name="string-functions"></a>Zeichenfolgenfunktionen
Diese Funktionen können nur mit JSON-Zeichenfolgen verwendet werden.

### <a name="concat"></a>concat
Verkettet eine oder mehrere Zeichenfolgen.

Wenn der Ausgabewert von `element1` beispielsweise `"bar"` lautet, gibt dieses Beispiel die Zeichenfolge `"foobar!"` zurück:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
Gibt die Teilzeichenfolge der angegebenen Zeichenfolge zurück. Die Teilzeichenfolge beginnt am angegebenen Index und weist die angegebene Länge auf.

Das folgende Beispiel gibt `"ftw"`zurück:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
Gibt eine Zeichenfolge zurück, in der alle Vorkommen der angegebenen Zeichenfolge in der aktuellen Zeichenfolge durch eine andere Zeichenfolge ersetzt werden.

Das folgende Beispiel gibt `"Everything is awesome!"`zurück:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>GUID
Generiert eine global eindeutige Zeichenfolge (GUID).

Im folgenden Beispiel könnte `"c7bc8bdc-7252-4a82-ba53-7c468679a511"` zurückgegeben werden:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Gibt eine in Kleinbuchstaben konvertierte Zeichenfolge zurück.

Das folgende Beispiel gibt `"foobar"`zurück:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Gibt eine in Großbuchstaben konvertierte Zeichenfolge zurück.

Das folgende Beispiel gibt `"FOOBAR"`zurück:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Auflistungsfunktionen
Diese Funktionen können mit Auflistungen wie JSON-Zeichenfolgen, Arrays und Objekten verwendet werden.

### <a name="contains"></a>contains
Gibt `true` zurück, wenn eine Zeichenfolge die angegebene Teilzeichenfolge enthält, ein Array den angegebenen Wert enthält oder ein Objekt den angegebenen Schlüssel enthält.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `true`zurück:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `false`zurück:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Das folgende Beispiel gibt `true`zurück:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Gibt die Anzahl von Zeichen in einer Zeichenfolge, die Anzahl von Werten in einem Array oder die Anzahl von Schlüsseln in einem Objekt zurück.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `6`zurück:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `3`zurück:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Das folgende Beispiel gibt `2`zurück:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Gibt `true` zurück, wenn die Zeichenfolge, das Array oder das Objekt null oder leer ist.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `true`zurück:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `false`zurück:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Das folgende Beispiel gibt `false`zurück:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Beispiel 4: Null und nicht definiert
Angenommen `element1` ist `null` oder nicht definiert. Das folgende Beispiel gibt `true`zurück:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>first
Gibt das erste Zeichen der angegebenen Zeichenfolge, den ersten Wert des angegebenen Arrays oder den ersten Schlüssel und Wert des angegebenen Objekts zurück.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `"f"`zurück:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `1`zurück:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Das folgende Beispiel gibt `{"key1": "foobar"}`zurück:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>last
Gibt das letzte Zeichen der angegebenen Zeichenfolge, den letzten Wert des angegebenen Arrays oder den letzten Schlüssel und Wert des angegebenen Objekts zurück.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `"r"`zurück:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `2`zurück:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Das folgende Beispiel gibt `{"key2": "raboof"}`zurück:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>take
Gibt eine angegebene Anzahl von zusammenhängenden Zeichen vom Anfang der Zeichenfolge, eine angegebene Anzahl von zusammenhängenden Werten vom Anfang des Arrays oder eine angegebene Anzahl von zusammenhängenden Schlüsseln und Werten vom Anfang des Objekts zurück.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `"foo"`zurück:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `[1, 2]`zurück:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Das folgende Beispiel gibt `{"key1": "foobar"}`zurück:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>skip
Umgeht eine angegebenen Anzahl von Elementen in einer Auflistung und gibt dann die übrigen Elemente zurück.

#### <a name="example-1-string"></a>Beispiel 1: Zeichenfolge
Das folgende Beispiel gibt `"bar"`zurück:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Beispiel 2: Array
Angenommen `element1` gibt `[1, 2, 3]` zurück. Das folgende Beispiel gibt `[3]`zurück:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Beispiel 3: Objekt
Angenommen `element1` gibt Folgendes zurück:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Das folgende Beispiel gibt `{"key2": "raboof"}`zurück:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logische Funktionen
Diese Funktionen können in bedingten Ausdrücken verwendet werden. Einige Funktionen unterstützen möglicherweise nicht alle JSON-Datentypen.

### <a name="equals"></a>equals
Gibt `true` zurück, wenn beide Parameter denselben Typ und Wert aufweisen. Diese Funktion unterstützt alle JSON-Datentypen.

Das folgende Beispiel gibt `true`zurück:

```json
"[equals(0, 0)]"
```

Das folgende Beispiel gibt `true`zurück:

```json
"[equals('foo', 'foo')]"
```

Das folgende Beispiel gibt `false`zurück:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Gibt `true` zurück, wenn der erste Parameter streng kleiner als der zweite Parameter ist. Diese Funktion unterstützt nur Parameter vom Typ „Zahl“ und „Zeichenfolge“.

Das folgende Beispiel gibt `true`zurück:

```json
"[less(1, 2)]"
```

Das folgende Beispiel gibt `false`zurück:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Gibt `true` zurück, wenn der erste Parameter im Vergleich zum zweiten Parameter kleiner oder gleich groß ist. Diese Funktion unterstützt nur Parameter vom Typ „Zahl“ und „Zeichenfolge“.

Das folgende Beispiel gibt `true`zurück:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Gibt `true` zurück, wenn der erste Parameter streng größer als der zweite Parameter ist. Diese Funktion unterstützt nur Parameter vom Typ „Zahl“ und „Zeichenfolge“.

Das folgende Beispiel gibt `false`zurück:

```json
"[greater(1, 2)]"
```

Das folgende Beispiel gibt `true`zurück:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Gibt `true` zurück, wenn der erste Parameter im Vergleich zum zweiten Parameter größer oder gleich groß ist. Diese Funktion unterstützt nur Parameter vom Typ „Zahl“ und „Zeichenfolge“.

Das folgende Beispiel gibt `true`zurück:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>and
Gibt `true` zurück, wenn alle Parameter zu `true` ausgewertet werden. Diese Funktion unterstützt zwei oder mehr Parameter vom Typ „Boolesch“.

Das folgende Beispiel gibt `true`zurück:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Das folgende Beispiel gibt `false`zurück:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>oder
Gibt `true` zurück, wenn mindestens einer der Parameter zu `true` ausgewertet wird. Diese Funktion unterstützt zwei oder mehr Parameter vom Typ „Boolesch“.

Das folgende Beispiel gibt `true`zurück:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Das folgende Beispiel gibt `true`zurück:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Gibt `true` zurück, wenn der Parameter zu `false` ausgewertet wird. Diese Funktion unterstützt nur Parameter vom Typ „Boolesch“.

Das folgende Beispiel gibt `true`zurück:

```json
"[not(false)]"
```

Das folgende Beispiel gibt `false`zurück:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>coalesce
Gibt den Wert des ersten Parameters ungleich NULL zurück. Diese Funktion unterstützt alle JSON-Datentypen.

Angenommen `element1` und `element2` sind nicht definiert. Das folgende Beispiel gibt `"foobar"`zurück:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Konvertierungsfunktionen
Diese Funktionen können verwendet werden, um Werte zwischen JSON-Datentypen und Codierungen zu konvertieren.

### <a name="int"></a>int
Konvertiert den Parameter in eine ganze Zahl. Diese Funktion unterstützt Parameter vom Typ „Zahl“ und „Zeichenfolge“.

Das folgende Beispiel gibt `1`zurück:

```json
"[int('1')]"
```

Das folgende Beispiel gibt `2`zurück:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Konvertiert den Parameter in eine Gleitkommazahl. Diese Funktion unterstützt Parameter vom Typ „Zahl“ und „Zeichenfolge“.

Das folgende Beispiel gibt `1.0`zurück:

```json
"[float('1.0')]"
```

Das folgende Beispiel gibt `2.9`zurück:

```json
"[float(2.9)]"
```

### <a name="string"></a>Zeichenfolge
Konvertiert den Parameter in eine Zeichenfolge. Diese Funktion unterstützt Parameter aller JSON-Datentypen.

Das folgende Beispiel gibt `"1"`zurück:

```json
"[string(1)]"
```

Das folgende Beispiel gibt `"2.9"`zurück:

```json
"[string(2.9)]"
```

Das folgende Beispiel gibt `"[1,2,3]"`zurück:

```json
"[string([1,2,3])]"
```

Das folgende Beispiel gibt `"{"foo":"bar"}"`zurück:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Konvertiert den Parameter in einen booleschen Wert. Diese Funktion unterstützt Parameter vom Typ „Zahl“, „Zeichenfolge“ und „Boolesch“. Ähnlich wie boolesche Werte in JavaScript geben alle Werte außer `0` oder `'false'` `true` zurück.

Das folgende Beispiel gibt `true`zurück:

```json
"[bool(1)]"
```

Das folgende Beispiel gibt `false`zurück:

```json
"[bool(0)]"
```

Das folgende Beispiel gibt `true`zurück:

```json
"[bool(true)]"
```

Das folgende Beispiel gibt `true`zurück:

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Konvertiert den Parameter in einen systemeigenen Typ. Das heißt, diese Funktion ist das Gegenteil von `string()`. Diese Funktion unterstützt nur Parameter vom Typ „Zeichenfolge“.

Das folgende Beispiel gibt `1`zurück:

```json
"[parse('1')]"
```

Das folgende Beispiel gibt `true`zurück:

```json
"[parse('true')]"
```

Das folgende Beispiel gibt `[1,2,3]`zurück:

```json
"[parse('[1,2,3]')]"
```

Das folgende Beispiel gibt `{"foo":"bar"}`zurück:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Codiert den Parameter in eine Base64-codierte Zeichenfolge. Diese Funktion unterstützt nur Parameter vom Typ „Zeichenfolge“.

Das folgende Beispiel gibt `"Zm9vYmFy"`zurück:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Decodiert den Parameter aus einer Base64-codierten Zeichenfolge. Diese Funktion unterstützt nur Parameter vom Typ „Zeichenfolge“.

Das folgende Beispiel gibt `"foobar"`zurück:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Codiert den Parameter in eine URL-codierte Zeichenfolge. Diese Funktion unterstützt nur Parameter vom Typ „Zeichenfolge“.

Das folgende Beispiel gibt `"https%3A%2F%2Fportal.azure.com%2F"`zurück:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Decodiert den Parameter aus einer URL-codierten Zeichenfolge. Diese Funktion unterstützt nur Parameter vom Typ „Zeichenfolge“.

Das folgende Beispiel gibt `"https://portal.azure.com/"`zurück:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Mathematische Funktionen
### <a name="add"></a>add
Addiert zwei Zahlen und gibt das Ergebnis zurück.

Das folgende Beispiel gibt `3`zurück:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Subtrahiert die zweite Zahl von der ersten Zahl und gibt das Ergebnis zurück.

Das folgende Beispiel gibt `1`zurück:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Multipliziert zwei Zahlen und gibt das Ergebnis zurück.

Das folgende Beispiel gibt `6`zurück:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Dividiert die erste Zahl durch die zweite Zahl und gibt das Ergebnis zurück. Das Ergebnis ist immer eine ganze Zahl.

Das folgende Beispiel gibt `2`zurück:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>mod
Dividiert die erste Zahl durch die zweite Zahl und gibt den Restwert zurück.

Das folgende Beispiel gibt `0`zurück:

```json
"[mod(6, 3)]"
```

Das folgende Beispiel gibt `2`zurück:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>Min
Gibt die kleinere der beiden Zahlen zurück.

Das folgende Beispiel gibt `1`zurück:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Gibt die größere der beiden Zahlen zurück.

Das folgende Beispiel gibt `2`zurück:

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Generiert eine Sequenz von ganzen Zahlen innerhalb des angegebenen Bereichs.

Das folgende Beispiel gibt `[1,2,3]`zurück:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Gibt eine zufällige ganze Zahl innerhalb des angegebenen Bereichs zurück. Diese Funktion generiert keine kryptografisch sicheren Zufallszahlen.

Im folgenden Beispiel könnte `42` zurückgegeben werden:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>floor
Gibt den größten ganzzahligen Wert zurück, der im Vergleich zur angegebenen Zahl kleiner oder gleich groß ist.

Das folgende Beispiel gibt `3`zurück:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Gibt den größten ganzzahligen Wert zurück, der im Vergleich zur angegebenen Zahl größer oder gleich groß ist.

Das folgende Beispiel gibt `4`zurück:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datumsfunktionen
### <a name="utcnow"></a>utcNow
Gibt eine Zeichenfolge im ISO 8601-Format des aktuellen Datums und der aktuellen Uhrzeit auf dem lokalen Computer zurück.

Im folgenden Beispiel könnte `"1990-12-31T23:59:59.000Z"` zurückgegeben werden:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Fügt dem angegebenen Zeitstempel eine ganzzahlige Anzahl von Sekunden hinzu.

Das folgende Beispiel gibt `"1991-01-01T00:00:00.000Z"`zurück:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Fügt dem angegebenen Zeitstempel eine ganzzahlige Anzahl von Minuten hinzu.

Das folgende Beispiel gibt `"1991-01-01T00:00:59.000Z"`zurück:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Fügt dem angegebenen Zeitstempel eine ganzzahlige Anzahl von Stunden hinzu.

Das folgende Beispiel gibt `"1991-01-01T00:59:59.000Z"`zurück:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in Azure Resource Manager finden Sie unter [Übersicht über Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

