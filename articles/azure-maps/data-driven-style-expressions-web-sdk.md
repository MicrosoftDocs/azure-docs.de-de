---
title: Datengesteuerte Formatvorlagenausdrücke im Azure Maps Web SDK | Microsoft Azure Maps
description: Hier erhalten Sie Informationen zu datengesteuerten Formatvorlagenausdrücken. Sie erfahren, wie Sie diese Ausdrücke im Azure Maps Web SDK verwenden, um Stile auf Karten anzupassen.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 8f27f7532d074428fafe74e4a453628f5c61d2b8
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895969"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Datengesteuerte Formatvorlagenausdrücke (Web SDK)

Mit Ausdrücken können Sie Geschäftslogik auf Formatvorlagenoptionen anwenden, bei denen die Eigenschaften beachtet werden, die für jede Form in einer Datenquelle definiert sind. Ausdrücke können zum Filtern von Daten in einer Datenquelle oder Datenebene verwendet werden. Ausdrücke können aus bedingter Logik bestehen, z. B. aus if-Anweisungen. Und sie können verwendet werden, um Daten mithilfe von Zeichenfolgenoperatoren, logischen Operatoren und mathematischen Operatoren zu bearbeiten.

Datengesteuerte Formatvorlagen verringern die Menge an Code, die zum Implementieren von Geschäftslogik für die Formatierung benötigt wird. Bei Verwendung mit Ebenen werden Ausdrücke zur Renderingzeit in einem separaten Thread ausgewertet. Diese Funktion bietet im Vergleich zur Auswertung der Geschäftslogik im UI-Thread bessere Leistung.

Dieses Video enthält eine Übersicht über die datengesteuerte Formatierung im Azure Maps Web SDK.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Ausdrücke werden als JSON-Arrays dargestellt. Das erste Element eines Ausdrucks im Array ist eine Zeichenfolge, mit der der Name des Ausdrucksoperators angegeben wird. Dies können beispielsweise „+“ oder „case“ sein. Die nächsten Elemente (falls vorhanden) sind die Argumente für den Ausdruck. Jedes Argument ist entweder ein Literalwert (Zeichenfolge, Zahl, boolescher Wert oder `null`) oder ein anderes Array mit Ausdrücken. Mit dem folgenden Pseudocode wird die grundlegende Struktur eines Ausdrucks definiert. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Das Azure Maps Web SDK unterstützt viele Typen von Ausdrücken. Ausdrücke können eigenständig oder in Kombination mit anderen Ausdrücken verwendet werden.

| Art der Ausdrücke | BESCHREIBUNG |
|---------------------|-------------|
| [Aggregatausdruck](#aggregate-expression) | Ein Ausdruck zum Definieren einer Berechnung, die über ein Dataset verarbeitet wird und mit der Option `clusterProperties` von `DataSource` verwendet werden kann. |
| [Boolesche Ausdrücke](#boolean-expressions) | Bei booleschen Ausdrücken werden Ausdrücke mit booleschen Operatoren bereitgestellt, um boolesche Vergleiche auszuwerten. |
| [Farbausdrücke](#color-expressions) | Farbausdrücke erleichtern Ihnen das Erstellen und Bearbeiten von Farbwerten. |
| [Bedingte Ausdrücke](#conditional-expressions) | Bei bedingten Ausdrücken werden logische Vorgänge bereitgestellt, die sich wie if-Anweisungen verhalten. |
| [Datenausdrücke](#data-expressions) | Ermöglichen den Zugriff auf die Eigenschaftsdaten eines Features. |
| [Interpolations- und Schrittausdrücke](#interpolate-and-step-expressions) | Interpolations- und Schrittausdrücke können verwendet werden, um Werte entlang einer interpolierten Kurve oder für eine Schrittfunktion zu berechnen. |
| [Ebenenspezifische Ausdrücke](#layer-specific-expressions) | Dies sind spezielle Ausdrücke, die nur für eine einzelne Ebene gelten. |
| [Mathematische Ausdrücke](#math-expressions) | Es werden mathematische Operatoren bereitgestellt, um datengesteuerte Berechnungen innerhalb des Ausdrucksframeworks durchzuführen. |
| [Zeichenfolgenoperator-Ausdrücke](#string-operator-expressions) | Mit Zeichenfolgenoperator-Ausdrücken werden Konvertierungsvorgänge für Zeichenfolgen durchgeführt, z. B. zum Verketten und Ändern der Groß-/Kleinschreibung. |
| [Typenausdrücke](#type-expressions) | Bei Typenausdrücken werden Tools zum Testen und Konvertieren unterschiedlicher Datentypen, z. B. Zeichenfolgen, Zahlen und boolesche Werte, bereitgestellt. |
| [Ausdrücke mit variabler Bindung](#variable-binding-expressions) | Mit Ausdrücken mit variabler Bindung werden die Ergebnisse einer Berechnung in einer Variablen gespeichert, und an einer anderen Stelle eines Ausdrucks kann mehrfach darauf verwiesen werden, ohne dass der gespeicherte Wert erneut berechnet werden muss. |
| [Zoom-Ausdruck](#zoom-expression) | Ruft zur Renderzeit den aktuellen Zoomfaktor der Karte ab. |

In allen Beispielen dieses Dokuments werden die folgenden Features verwendet, um die verschiedenen Möglichkeiten der Verwendung der einzelnen Ausdrücke zu veranschaulichen. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red",
        "abcArray": ['a', 'b', 'c'],
        "array2d": [['a', 'b'], ['x', 'y']],
        "_style": {
            "fillColor": 'red'
        }
    }
}
```

## <a name="data-expressions"></a>Datenausdrücke

Mit Datenausdrücken wird der Zugriff auf die Eigenschaftsdaten in einem Feature gewährt. 

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `['at', number, array]` | Objekt (object) | Ruft ein Element aus einem Array ab. |
| `['geometry-type']` | Zeichenfolge | Ruft den Geometrietyp des Features ab: Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | value | Ruft den Eigenschaftswert aus den Eigenschaften des aktuellen Features ab. Gibt null zurück, wenn die angeforderte Eigenschaft fehlt. |
| `['get', string, object]` | value | Ruft den Eigenschaftswert aus den Eigenschaften des angegebenen Objekts ab. Gibt null zurück, wenn die angeforderte Eigenschaft fehlt. |
| `['has', string]` | boolean | Ermittelt, ob die Eigenschaften eines Features über die angegebene Eigenschaft verfügen. |
| `['has', string, object]` | boolean | Ermittelt, ob die Eigenschaften des Objekts über die angegebene Eigenschaft verfügen. |
| `['id']` | value | Ruft die ID des Features ab, falls eine vorhanden ist. |
| `['length', string | array]` | number | Ruft die Länge einer Zeichenfolge oder eines Arrays ab. |
| `['in', boolean | string | number, array]` | boolean | Bestimmt, ob ein Element in einem Array vorhanden ist |
| `['in', substring, string]` | boolean | Bestimmt, ob eine Teilzeichenfolge in einer Zeichenfolge vorhanden ist |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | number | Gibt die erste Position zurück, an der ein Element in einem Array oder eine Teilzeichenfolge in einer Zeichenfolge gefunden wird. Sollte die Eingabe nicht gefunden werden, wird `-1` zurückgegeben. Akzeptiert einen optionalen Index, ab dem mit der Suche begonnen werden soll. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | `string` \| array | Gibt ein Element aus einem Array oder eine Teilzeichenfolge aus einer Zeichenfolge ab einem angegebenen Startindex oder zwischen einem Start- und einem Endindex zurück (sofern festgelegt). Der Rückgabewert schließt den Startindex ein, aber nicht den Endindex. |

**Beispiele**

Auf die Eigenschaften eines Features kann in einem Ausdruck direkt zugegriffen werden, indem ein `get`-Ausdruck verwendet wird. In diesem Beispiel wird der „zoneColor“-Wert des Features verwendet, um die Farbeigenschaft einer Blasenebene anzugeben. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Das obige Beispiel funktioniert problemlos, wenn alle Punktfeatures über die `zoneColor`-Eigenschaft verfügen. Wenn dies nicht der Fall ist, wird die Farbe wahrscheinlich auf „schwarz“ zurückgesetzt. Verwenden Sie zum Ändern der Fallbackfarbe einen `case`-Ausdruck in Kombination mit dem `has`-Ausdruck, um zu überprüfen, ob die Eigenschaft vorhanden ist. Geben Sie eine Ausweichfarbe für den Fall an, dass die Eigenschaft nicht vorhanden ist.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Blasen- und Symbolebenen rendern standardmäßig die Koordinaten aller Formen in einer Datenquelle. Dieses Verhalten kann die Scheitelpunkte eines Polygons oder einer Linie hervorheben. Die Option `filter` der Ebene kann verwendet werden, um den Geometrietyp der gerenderten Features zu beschränken, indem ein `['geometry-type']`-Ausdruck in einem booleschen Ausdruck verwendet wird. Im folgenden Beispiel wird eine Blasenebene so beschränkt, dass nur `Point`-Features gerendert werden.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Im folgenden Beispiel können sowohl `Point`- als auch `MultiPoint`-Features gerendert werden. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Auf ähnliche Weise wird die Kontur von Polygonen auf Linienebenen gerendert. Fügen Sie zum Deaktivieren dieses Verhaltens auf einer Linienebene einen Filter hinzu, bei dem nur `LineString`- und `MultiLineString`-Features zugelassen werden.  

Im Folgenden finden Sie einige weitere Beispiele für die Verwendung von Datenausdrücken:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Mathematische Ausdrücke

Bei mathematischen Ausdrücken werden mathematische Operatoren bereitgestellt, um datengesteuerte Berechnungen innerhalb des Ausdrucksframeworks durchzuführen.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | Berechnet die Summe der angegebenen Zahlen. |
| `['-', number]` | number | Subtrahiert von 0 die angegebene Zahl. |
| `['-', number, number]` | number | Subtrahiert die zweite Zahl von der ersten Zahl. |
| `['*', number, number, …]` | number | Multipliziert die angegebenen Zahlen. |
| `['/', number, number]` | number | Dividiert die erste Zahl durch die zweite Zahl. |
| `['%', number, number]` | number | Berechnet den Rest aus der Division der ersten Zahl durch die zweite Zahl. |
| `['^', number, number]` | number | Berechnet den Wert, der sich ergibt, wenn der erste Wert mit der zweiten Zahl potenziert wird. |
| `['abs', number]` | number | Berechnet den absoluten Wert der angegebenen Zahl. |
| `['acos', number]` | number | Berechnet den Arkuskosinus der angegebenen Zahl. |
| `['asin', number]` | number | Berechnet den Arkussinus der angegebenen Zahl. |
| `['atan', number]` | number | Berechnet den Arkustangens der angegebenen Zahl. |
| `['ceil', number]` | number | Rundet die Zahl auf die nächste ganze Zahl. |
| `['cos', number]` | number | Berechnet den Kosinus der angegebenen Zahl. |
| `['e']` | number | Gibt die mathematische Konstante `e` zurück. |
| `['floor', number]` | number | Rundet die Zahl auf die nächste ganze Zahl ab. |
| `['ln', number]` | number | Berechnet den natürlichen Logarithmus der angegebenen Zahl. |
| `['ln2']` | number | Gibt die mathematische Konstante `ln(2)` zurück. |
| `['log10', number]` | number | Berechnet den dekadischen Logarithmus der angegebenen Zahl. |
| `['log2', number]` | number | Berechnet den Logarithmus zur Basis 2 für die angegebene Zahl. |
| `['max', number, number, …]` | number | Berechnet die maximale Anzahl in der angegebenen Zahlengruppe. |
| `['min', number, number, …]` | number | Berechnet die minimale Anzahl in der angegebenen Zahlengruppe. |
| `['pi']` | number | Gibt die mathematische Konstante `PI` zurück. |
| `['round', number]` | number | Rundet die Zahl auf die nächste ganze Zahl. Halbe Werte werden weg von null gerundet. Für `['round', -1.5]` ergibt sich beispielsweise „-2“. |
| `['sin', number]` | number | Berechnet den Sinus der angegebenen Zahl. |
| `['sqrt', number]` | number | Berechnet die Quadratwurzel der angegebenen Zahl. |
| `['tan', number]` | number | Berechnet den Tangens der angegebenen Zahl. |

## <a name="aggregate-expression"></a>Aggregatausdruck

Ein Aggregatausdruck definiert eine Berechnung, die über ein Dataset verarbeitet wird und mit der Option `clusterProperties` von `DataSource` verwendet werden kann. Die Ausgabe dieser Ausdrücke muss eine Zahl oder ein boolescher Wert sein. 

Ein Aggregatausdruck verwendet drei Werte: einen Operatorwert, einen Anfangswert und einen Ausdruck zum Abrufen einer Eigenschaft aus jedem Feature in dem Dataset, auf das der Aggregatvorgang angewendet werden soll. Dieser Ausdruck hat folgendes Format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- Operator: Eine Ausdrucksfunktion, die dann auf alle Werte angewendet wird, die von `mapExpression` für jeden Punkt im Cluster berechnet werden. Unterstützte Operatoren: 
    - Bei Zahlen: `+`, `*`, `max`, `min`
    - Bei booleschen Werten: `all`, `any`
- initialValue: Ein Anfangswert, mit dem der erste berechnete Wert aggregiert wird.
- mapExpression: Ein Ausdruck, der auf jeden Punkt im Dataset angewendet wird.

**Beispiele**

Wenn alle Funktionen in einem Dataset eine `revenue`-Eigenschaft haben, die eine Zahl ist. Dann kann der Gesamtumsatz aller Punkte in einem Cluster, der aus dem Dataset erstellt wurde, mithilfe des folgenden Aggregatausdrucks berechnet werden. Diese Berechnung erfolgt mit dem folgenden Aggregatausdruck: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Boolesche Ausdrücke

Bei booleschen Ausdrücken werden Ausdrücke mit booleschen Operatoren bereitgestellt, um boolesche Vergleiche auszuwerten.

Beim Vergleichen von Werten ist der Vergleich streng typisiert. Werte unterschiedlichen Typs werden immer als ungleich betrachtet. Fälle, in denen bekannt ist, dass die Typen zur Analysezeit unterschiedlich sind, werden als ungültig angesehen und führen zu einem Analysefehler. 

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Logische Negation. Gibt `true` zurück, wenn für die Eingabe `false` gilt, und `false`, wenn für die Eingabe `true` gilt. |
| `['!=', value, value]` | boolean | Gibt `true` zurück, wenn die Eingabewerte nicht gleich sind, andernfalls `false`. |
| `['<', value, value]` | boolean | Gibt `true` zurück, wenn die erste Eingabe streng kleiner als die zweite Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `['<=', value, value]` | boolean | Gibt `true` zurück, wenn die erste Eingabe kleiner oder gleich der zweiten Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `['==', value, value]` | boolean | Gibt `true` zurück, wenn die Eingabewerte gleich sind, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `['>', value, value]` | boolean | Gibt `true` zurück, wenn die erste Eingabe streng größer als die zweite Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `['>=' value, value]` | boolean | Gibt `true` zurück, wenn die erste Eingabe größer oder gleich der zweiten Eingabe ist, andernfalls `false`. Die Argumente müssen entweder jeweils Zeichenfolgen oder Zahlen sein. |
| `['all', boolean, boolean, …]` | boolean | Gibt `true` zurück, wenn alle Eingaben `true` sind, andernfalls `false`. |
| `['any', boolean, boolean, …]` | boolean | Gibt `true` zurück, wenn eine oder mehrere Eingaben `true` sind, andernfalls `false`. |

## <a name="conditional-expressions"></a>Bedingte Ausdrücke

Bei bedingten Ausdrücken werden logische Vorgänge bereitgestellt, die sich wie if-Anweisungen verhalten.

Mit den folgenden Ausdrücken werden bedingte Logikvorgänge für die Eingabedaten durchgeführt. Mit dem Ausdruck `case` wird beispielsweise Logik vom Typ „if/then/else“ bereitgestellt, während der Ausdruck `match` wie eine „switch-Anweisung“ funktioniert. 

### <a name="case-expression"></a>Case-Ausdruck

Ein `case`-Ausdruck ist eine Art von bedingtem Ausdruck, der die „if/then/else“-Logik bietet. Diese Art Ausdruck durchläuft eine Liste mit booleschen Bedingungen. Er gibt den Ausgabewert der ersten booleschen Bedingung zurück, die als true ausgewertet wird.

Mit dem folgenden Pseudocode wird die Struktur des `case`-Ausdrucks definiert. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Beispiel**

Im folgenden Beispiel werden verschiedene boolesche Bedingungen durchlaufen, bis eine gefunden wird, deren Auswertung `true` ergibt. Anschließend wird der zugeordnete Wert zurückgegeben. Falls keine boolesche Bedingung `true` ergibt, wird ein Fallbackwert zurückgegeben. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Match-Ausdruck

Ein `match`-Ausdruck ist eine Art von bedingtem Ausdruck, der eine Logik wie bei einer switch-Anweisung ermöglicht. Die Eingabe kann ein beliebiger Ausdruck sein, z. B. `['get', 'entityType']`, bei dem eine Zeichenfolge oder eine Zahl zurückgegeben wird. Jede Bezeichnung muss entweder ein einzelner Literalwert oder ein Literalwert-Array sein, das entweder nur Zeichenfolgen oder Zahlen enthält. Es ergibt sich eine Übereinstimmung mit der Eingabe, wenn einer der Werte im Array übereinstimmt. Jede Bezeichnung muss eindeutig sein. Falls der Eingabetyp nicht mit dem Typ der Bezeichnungen übereinstimmt, ist das Ergebnis der Fallbackwert.

Mit dem folgenden Pseudocode wird die Struktur des `match`-Ausdrucks definiert. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Beispiele**

Im folgenden Beispiel wird die `entityType`-Eigenschaft eines Punktfeatures einer Blasenebene untersucht und nach einer Übereinstimmung gesucht. Wenn eine Übereinstimmung gefunden wird, wird dieser angegebene Wert zurückgegeben. Andernfalls wird der Fallbackwert zurückgegeben.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Im folgenden Beispiel wird ein Array zum Auflisten einer Gruppe von Bezeichnungen verwendet, für die jeweils der gleiche Wert zurückgegeben werden sollte. Dieser Ansatz ist viel effizienter als das einzelne Auflisten aller Bezeichnungen. In diesem Fall wird die Farbe „red“ zurückgegeben, wenn die `entityType`-Eigenschaft „restaurant“ oder „grocery_store“ lautet.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Im folgenden Beispiel wird ein Vergleichsausdruck verwendet, um einen Filter der Art „in Array“ oder „Array enthält“ auszuführen. In diesem Fall filtert der Ausdruck Daten mit einem ID-Wert, der in einer Liste zulässiger IDs enthalten ist. Wenn Sie Ausdrücke mit Filtern verwenden, muss das Ergebnis ein boolescher Wert sein.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>COALESCE-Ausdruck

Bei einem `coalesce`-Ausdruck wird eine Reihe von Ausdrücken durchlaufen, bis der erste Wert erkannt wird, der nicht null ist. Dieser Wert wird dann zurückgegeben. 

Mit dem folgenden Pseudocode wird die Struktur des `coalesce`-Ausdrucks definiert. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Beispiel**

Im folgenden Beispiel wird ein `coalesce`-Ausdruck genutzt, um die Option `textField` einer Symbolebene festzulegen. Wenn die `title`-Eigenschaft für das Feature fehlt oder auf `null` festgelegt ist, versucht der Ausdruck, nach der `subtitle`-Eigenschaft zu suchen. Falls sie nicht vorhanden oder `null` festgelegt ist, wird auf eine leere Zeichenfolge zurückgegriffen. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

Im folgenden Beispiel wird ein `coalesce`-Ausdruck verwendet, um das erste verfügbare Bildsymbol, das im Kartensprite verfügbar ist, aus einer Liste mit angegebenen Imagenamen abzurufen.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Typenausdrücke

Bei Typenausdrücken werden Tools zum Testen und Konvertieren unterschiedlicher Datentypen, z. B. Zeichenfolgen, Zahlen und boolesche Werte, bereitgestellt.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | array \| object | Gibt einen Literalarray- oder Objektwert zurück. Verwenden Sie diesen Ausdruck, um zu verhindern, dass ein Array oder ein Objekt als Ausdruck ausgewertet wird. Dies ist erforderlich, wenn ein Array oder Objekt von einem Ausdruck zurückgegeben werden muss. |
| `['image', string]` | Zeichenfolge | Prüft, ob eine angegebene Image-ID in das Kartenbildsprite geladen wird. Wenn dies der Fall ist, wird die ID zurückgegeben, andernfalls wird NULL zurückgegeben. |
| `['to-boolean', value]` | boolean | Konvertiert den Eingabewert in einen booleschen Wert. Das Ergebnis ist `false`, wenn die Eingabe eine leere Zeichenfolge ist oder `0`, `false`, `null` oder `NaN` lautet. Andernfalls lautet das Ergebnis `true`. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konvertiert den Eingabewert in eine Farbe. Falls mehrere Werte bereitgestellt werden, werden sie der Reihe nach einzeln ausgewertet, bis die erste erfolgreiche Konvertierung erfolgt ist. Wenn keine der Eingaben konvertiert werden kann, ergibt sich für den Ausdruck ein Fehler. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | Konvertiert den Eingabewert in eine Zahl, falls dies möglich ist. Wenn die Eingabe `null` oder `false` lautet, ist das Ergebnis 0. Wenn die Eingabe `true` lautet, ist das Ergebnis 1. Wenn die Eingabe eine Zeichenfolge ist, wird sie mit der Zeichenfolgenfunktion [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) der ECMAScript-Sprachspezifikation in eine Zahl konvertiert. Falls mehrere Werte bereitgestellt werden, werden sie der Reihe nach einzeln ausgewertet, bis die erste erfolgreiche Konvertierung erfolgt ist. Wenn keine der Eingaben konvertiert werden kann, ergibt sich für den Ausdruck ein Fehler. |
| `['to-string', value]` | Zeichenfolge | Konvertiert den Eingabewert in eine Zeichenfolge. Wenn die Eingabe `null` lautet, ist das Ergebnis `""`. Wenn die Eingabe ein boolescher Wert ist, ist das Ergebnis `"true"` oder `"false"`. Wenn die Eingabe eine Zahl ist, wird sie mit der Zeichenfolgenfunktion [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) der ECMAScript-Sprachspezifikation in eine Zeichenfolge konvertiert. Wenn die Eingabe eine Farbe ist, wird sie in die CSS-RGBA-Farbzeichenfolge `"rgba(r,g,b,a)"` konvertiert. Andernfalls wird die Eingabe mit der [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify)-Funktion der ECMAScript-Sprachspezifikation in eine Zeichenfolge konvertiert. |
| `['typeof', value]` | Zeichenfolge | Gibt eine Zeichenfolge zurück, mit der der Typ des angegebenen Werts beschrieben wird. |

> [!TIP]
> Wenn in der Browserkonsole eine Fehlermeldung wie `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` angezeigt wird, bedeutet dies Folgendes: Ihr Code enthält einen Ausdruck mit einem Array, das als ersten Wert keine Zeichenfolge aufweist. Wenn der Ausdruck ein Array zurückgeben soll, müssen Sie das Array mit dem `literal`-Ausdruck umschließen. Im folgenden Beispiel wird die `offset`-Symboloption einer Symbolebene festgelegt, wobei es sich um ein Array mit zwei Zahlen handeln muss. Es wird ein `match`-Ausdruck verwendet, um basierend auf dem Wert der `entityType`-Eigenschaft des Punktfeatures zwischen zwei Offsetwerten zu wählen.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Farbausdrücke

Farbausdrücke erleichtern Ihnen das Erstellen und Bearbeiten von Farbwerten.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Erstellt einen Farbwert aus den Komponenten *red* , *green* und *blue* , die zwischen `0` und `255` liegen müssen und deren Alphakomponente `1` lauten muss. Wenn eine Komponente außerhalb des Bereichs liegt, tritt für den Ausdruck ein Fehler auf. |
| `['rgba', number, number, number, number]` | color | Erstellt einen Farbwert aus den Komponenten *red* , *green* und *blue* , die zwischen `0` und `255` liegen müssen und deren Alphakomponente zwischen `0` und `1` liegen muss. Wenn eine Komponente außerhalb des Bereichs liegt, tritt für den Ausdruck ein Fehler auf. |
| `['to-rgba']` | \[number, number, number, number\] | Gibt ein Array mit vier Elementen zurück, das die Komponenten *red* , *green* , *blue* und *alpha* der Eingabefarbe enthält (in dieser Reihenfolge). |

**Beispiel**

Im folgenden Beispiel wird ein RGB-Farbwert erstellt, der für *red* über den Wert `255` und für *green* und *blue* über Werte verfügt, die per Multiplikation von `2.5` mit dem Wert der `temperature`-Eigenschaft berechnet werden. Wenn sich die Temperatur ändert, ändert sich die Farbe in verschiedene Schattierungen von *red* .

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Zeichenfolgenoperator-Ausdrücke

Mit Zeichenfolgenoperator-Ausdrücken werden Konvertierungsvorgänge für Zeichenfolgen durchgeführt, z. B. zum Verketten und Ändern der Groß-/Kleinschreibung. 

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|------------|-------------|-------------|
| `['concat', string, string, …]` | Zeichenfolge | Verkettet mehrere Zeichenfolgen miteinander. Jeder Wert muss eine Zeichenfolge sein. Verwenden Sie den Typenausdruck `to-string`, um bei Bedarf andere Werttypen in eine Zeichenfolge zu konvertieren. |
| `['downcase', string]` | Zeichenfolge | Konvertiert die angegebene Zeichenfolge in Kleinbuchstaben. |
| `['upcase', string]` | Zeichenfolge | Konvertiert die angegebene Zeichenfolge in Großbuchstaben. |

**Beispiel**

Im folgenden Beispiel wird die `temperature`-Eigenschaft des Punktfeatures in eine Zeichenfolge konvertiert und anschließend „°F“ am Ende angefügt.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Mit dem obigen Ausdruck wird in der Karte eine Nadel mit dem Überlagerungstext „64°F“ gerendert. Dies ist in der Abbildung unten dargestellt.

<center>

![Beispiel für Zeichenfolgenoperator-Ausdruck](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>Interpolations- und Schrittausdrücke

Interpolations- und Schrittausdrücke können verwendet werden, um Werte entlang einer interpolierten Kurve oder für eine Schrittfunktion zu berechnen. Für diese Ausdrücke wird ein Ausdruck verwendet, bei dem ein numerischer Wert als Eingabe zurückgegeben wird, z. B. `['get',  'temperature']`. Der Eingabewert wird anhand von Paaren mit Ein- und Ausgabewerten ausgewertet, um den Wert zu ermitteln, der am besten zur interpolierten Kurve oder Schrittfunktion passt. Die Ausgabewerte werden als „Stopps“ bezeichnet. Bei den Eingabewerten für jeden „Stopp“ muss es sich um Zahlen in aufsteigender Reihenfolge handeln. Die Ausgabewerte müssen eine Zahl, ein Array mit Farben oder eine Farbe sein.

### <a name="interpolate-expression"></a>Interpolate-Ausdruck

Ein `interpolate`-Ausdruck kann verwendet werden, um eine fortlaufende, nahtlose Gruppe mit Werten zu berechnen, indem zwischen Stoppwerten interpoliert wird. Ein `interpolate`-Ausdruck, der Farbwerte zurückgibt, erzeugt einen Farbverlauf, in dem aus Ergebniswerten ausgewählt wird.

Es gibt drei Typen von Interpolationsmethoden, die in einem `interpolate`-Ausdruck verwendet werden können:
 
* `['linear']` – Lineare Interpolation zwischen dem Stopp-Paar.
* `['exponential', base]` – Exponentielle Interpolation zwischen den Stopps. Mit dem Wert `base` wird die Rate gesteuert, mit der die Ausgabe zunimmt. Bei höheren Werten nimmt die Ausgabe zum oberen Bereich hin stärker zu. Ein `base`-Wert nahe 1 führt zu einer Ausgabe, die eher linear zunimmt.
* `['cubic-bezier', x1, y1, x2, y2]` – Interpolation per [kubischer Bézierkurve](https://developer.mozilla.org/docs/Web/CSS/timing-function), die über die vorhandenen Steuerungspunkte definiert wird.

Hier ist ein Beispiel zu den unterschiedlichen Arten von Interpolationen angegeben. 

| Linear  | Exponentiell | Kubische Bézierkurve |
|---------|-------------|--------------|
| ![Graph für lineare Interpolation](media/how-to-expressions/linear-interpolation.png) | ![Graph für exponentielle Interpolation](media/how-to-expressions/exponential-interpolation.png) | ![Graph für Interpolation mit kubischer Bézierkurve](media/how-to-expressions/bezier-curve-interpolation.png) |

Mit dem folgenden Pseudocode wird die Struktur des `interpolate`-Ausdrucks definiert. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Beispiel**

Im folgenden Beispiel wird ein `linear interpolate`-Ausdruck verwendet, um die `color`-Eigenschaft einer Blasenebene basierend auf der `temperature`-Eigenschaft des Punktfeatures festzulegen. Wenn der `temperature` Wert kleiner als 60 ist, wird „blue“ zurückgegeben. Liegt der Wert zwischen 60 und 70 (nicht einschließlich), wird Gelb zurückgegeben. Liegt der Wert zwischen 70 und 80 (nicht einschließlich), wird „orange“ zurückgegeben. Liegt er bei 80 oder darüber, wird „red“ zurückgegeben.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

In der folgenden Abbildung wird veranschaulicht, wie die Farben für den obigen Ausdruck ausgewählt werden.
 
<center>

![Beispiel für Interpolate-Ausdruck](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Step-Ausdruck

Ein `step`-Ausdruck kann verwendet werden, um diskrete Schrittergebniswerte zu berechnen, indem eine Treppenfunktion ([Piecewise-Constant Function](http://mathworld.wolfram.com/PiecewiseConstantFunction.html)) genutzt wird, die über Stopps definiert wird. 

Mit dem folgenden Pseudocode wird die Struktur des `step`-Ausdrucks definiert. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Bei Step-Ausdrücken wird der Ausgabewert des Stopps direkt vor dem Eingabewert zurückgegeben – oder der erste Eingabewert, wenn die Eingabe kleiner als der erste Stoppwert ist. 

**Beispiel**

Im folgenden Beispiel wird ein `step`-Ausdruck verwendet, um die `color`-Eigenschaft einer Blasenebene basierend auf der `temperature`-Eigenschaft des Punktfeatures festzulegen. Wenn der `temperature` Wert kleiner als 60 ist, wird „blue“ zurückgegeben. Liegt der Wert zwischen 60 und 70, wird „yellow“ zurückgegeben. Liegt der Wert zwischen 70 und 80 (nicht einschließlich), wird „orange“ zurückgegeben. Liegt er bei 80 oder darüber, wird „red“ zurückgegeben.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

In der folgenden Abbildung wird veranschaulicht, wie die Farben für den obigen Ausdruck ausgewählt werden.
 
<center>

![Beispiel für einen Step-Ausdruck](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Ebenenspezifische Ausdrücke

Spezielle Ausdrücke, die nur für bestimmte Ebenen gelten.

### <a name="heat-map-density-expression"></a>Heatmap Density-Ausdruck

Mit einem Heatmap Density-Ausdruck wird der Wärmebilddichte-Wert für jedes Pixel einer Wärmebildebene abgerufen. Er ist als `['heatmap-density']` definiert. Dieser Wert ist eine Zahl zwischen `0` und `1`. Er wird zusammen mit einem `interpolation`- oder `step`-Ausdruck verwendet, um den Farbverlauf zu definieren, der zum Einfügen der Farben in das Wärmebild genutzt wird. Dieser Ausdruck kann nur in der [color-Option](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) der Wärmebildebene verwendet werden.

> [!TIP]
> Die Farbe bei Index „0“ in einem interpolation-Ausdruck bzw. die Standardfarbe einer Schrittfarbe definiert die Farbe in einem Bereich ohne Daten. Die Farbe bei Index 0 kann verwendet werden, um eine Hintergrundfarbe zu definieren. Viele Benutzer legen hierfür einen transparenten Wert oder ein halbtransparentes Schwarz fest.

**Beispiel**

In diesem Beispiel wird ein Ausdruck mit linearer Interpolation verwendet, um einen nahtlosen Farbverlauf zum Rendern des Wärmebilds zu erstellen. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Zusätzlich zur Nutzung eines nahtlosen Farbverlaufs zum Versehen eines Wärmebilds mit Farben können Farben auch in Bereichen angegeben werden, indem ein `step`-Ausdruck verwendet wird. Durch Verwenden eines `step`-Ausdrucks für die farbige Darstellung des Wärmebilds wird die Dichte visuell in Bereiche gegliedert, die einer Kontur- oder Radarkarte ähneln.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Weitere Informationen finden Sie unter [Hinzufügen einer Wärmebildebene](map-add-heat-map-layer.md).

### <a name="line-progress-expression"></a>Line Progress-Ausdruck

Mit einem Line Progress-Ausdruck wird der Fortschritt entlang einer Farbverlaufslinie auf einer Linienebene abgerufen. Er ist als `['line-progress']` definiert. Dieser Wert ist eine Zahl zwischen 0 und 1. Er wird in Kombination mit einem `interpolation`- oder `step`-Ausdruck verwendet. Dieser Ausdruck kann nur mit der [strokeGradient-Option]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) der Linienebene verwendet werden. 

> [!NOTE]
> Für die `strokeGradient`-Option der Linienebene muss die `lineMetrics`-Option der Datenquelle auf `true` festgelegt sein.

**Beispiel**

In diesem Beispiel wird der `['line-progress']`-Ausdruck verwendet, um einen Farbverlauf auf den Strich einer Linie anzuwenden.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Livebeispiel](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Text Field Format-Ausdruck

Der Text Field Format-Ausdruck kann mit der `textField`-Option der `textOptions`-Eigenschaft auf Symbolebene verwendet werden, um eine gemischte Textformatierung zu erzielen. Mit diesem Ausdruck kann ein Satz mit Eingabezeichenfolgen und Formatierungsoptionen angegeben werden. Die folgenden Optionen können für jede Eingabezeichenfolge dieses Ausdrucks angegeben werden.

 * `'font-scale'` – Gibt den Skalierungsfaktor für den Schriftgrad an. Wenn er angegeben ist, wird mit diesem Wert die `size`-Eigenschaft von `textOptions` für die individuelle Zeichenfolge außer Kraft gesetzt.
 * `'text-font'` – Gibt mindestens eine Schriftfamilie an, die für diese Zeichenfolge verwendet werden sollte. Wenn er angegeben ist, wird mit diesem Wert die `font`-Eigenschaft von `textOptions` für die individuelle Zeichenfolge außer Kraft gesetzt.
 * `'text-color'`: Gibt eine Farbe an, die beim Rendern auf einen Text angewendet werden soll. 

Mit dem folgenden Pseudocode wird die Struktur des Text Field Format-Ausdrucks definiert. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Beispiel**

Im folgenden Beispiel wird das Textfeld formatiert, indem Fettdruck hinzugefügt und der Schriftgrad der `title`-Eigenschaft des Features erhöht wird. In diesem Beispiel wird auch die `subtitle`-Eigenschaft des Features mit einem niedrigeren Schriftgrad und rot markiert in einer neuen Zeile hinzugefügt.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Auf dieser Ebene wird das Punktfeature wie in der folgenden Abbildung gerendert:
 
<center>

![Abbildung: Punktfeature mit formatiertem Textfeld](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>Number Format-Ausdruck

Der `number-format`-Ausdruck kann nur mit der `textField`-Option einer Symbolebene verwendet werden. Mit diesem Ausdruck wird die angegebene Zahl in eine formatierte Zeichenfolge konvertiert. Mit diesem Ausdruck wird die JavaScript-Funktion [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) umschlossen. Er unterstützt die folgenden Optionen.

 * `locale` – Geben Sie diese Option an, um Zahlen so in Zeichenfolgen zu konvertieren, dass sie für die angegebene Sprache geeignet sind. Übergeben Sie für diese Option ein [BCP 47-Sprachtag](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation).
 * `currency` – Dient zum Konvertieren der Zahl in eine Zeichenfolge, die für eine Währung steht. Mögliche Werte sind die [ISO 4217-Währungscodes](https://en.wikipedia.org/wiki/ISO_4217), z. B. „USD“ für US-Dollar, „EUR“ für Euro oder „CNY“ für chinesische Renminbi.
 * `'min-fraction-digits'` – Gibt die Mindestanzahl von Dezimalstellen an, die in der Zeichenfolgenversion der Zahl enthalten sein dürfen.
 * `'max-fraction-digits'` – Gibt die Höchstzahl von Dezimalstellen an, die in der Zeichenfolgenversion der Zahl enthalten sein dürfen.

Mit dem folgenden Pseudocode wird die Struktur des Text Field Format-Ausdrucks definiert. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Beispiel**

Im folgenden Beispiel wird ein `number-format`-Ausdruck verwendet, um zu ändern, wie die `revenue`-Eigenschaft des Punktfeatures in der `textField`-Option einer Symbolebene gerendert wird, damit ein US-Dollarwert angezeigt wird.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Auf dieser Ebene wird das Punktfeature wie in der folgenden Abbildung gerendert:

<center>

![Beispiel für Number Format-Ausdruck](media/how-to-expressions/number-format-expression.png) </center>

### <a name="image-expression"></a>Bildausdruck

Ein Bildausdruck kann mit den Optionen `image` und `textField` einer Symbolebene und der `fillPattern`-Option der Polygonebene verwendet werden. Dieser Ausdruck überprüft, ob das angeforderte Bild im Stil vorhanden ist und gibt entweder den aufgelösten Bildnamen oder `null` zurück, abhängig davon, ob das Bild aktuell im Stil vorhanden ist oder nicht. Dieser Überprüfungsprozess ist synchron und erfordert es, dass das Bild dem Stil hinzugefügt wurde, bevor es im Bildargument angefordert wurde.

**Beispiel**

Im folgenden Beispiel wird ein `image`-Ausdruck verwendet, um ein Symbol innerhalb einer Textzeile in einer Symbolebene hinzuzufügen. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Diese Ebene rendert das Textfeld in der Symbolebene wie in der Abbildung unten dargestellt:

<center>

![Beispiel für einen Bildausdruck](media/how-to-expressions/image-expression.png)</center>

## <a name="zoom-expression"></a>Zoom-Ausdruck

Ein `zoom`-Ausdruck wird verwendet, um zur Renderzeit den aktuellen Zoomfaktor der Karte abzurufen. Er ist als `['zoom']` definiert. Mit diesem Ausdruck wird eine Zahl zwischen dem minimalen und maximalen Wert des Zoomfaktorbereichs der Karte zurückgegeben. Die interaktiven Kartensteuerelemente in Azure Maps für Web und Android unterstützen 25 Zoomfaktoren, nummeriert von 0 bis 24. Mithilfe des `zoom`-Ausdrucks können Formatvorlagen dynamisch angepasst werden, wenn sich der Zoomfaktor der Karte ändert. Der `zoom`-Ausdruck kann nur mit `interpolate`- und `step`-Ausdrücken verwendet werden.

**Beispiel**

Standardmäßig ist für die in der Wärmebildebene gerenderten Daten ein fester Pixelradius für alle Zoomfaktoren definiert. Wenn die Karte gezoomt wird, werden die Daten aggregiert und die Wärmebildebene verändert sich. Zum Skalieren des Radius für jede Zoomebene kann ein `zoom`-Ausdruck verwendet werden, sodass jeder Datenpunkt den gleichen physischen Bereich der Karte abdeckt. Hierdurch sieht die Wärmebildebene statischer und konsistenter aus. Jede Zoomebene der Karte hat vertikal und horizontal doppelt so viele Pixel wie die vorherige Zoomebene. Wenn der Radius so skaliert wird, dass er sich mit jeder Zoomebene verdoppelt, wird ein Wärmebild erstellt, das auf allen Zoomebenen konsistent aussieht. Hierfür kann der Ausdruck `zoom` mit einem Ausdruck vom Typ `base 2 exponential interpolation` verwendet werden, wobei der Pixelradius für den minimalen Zoomfaktor und ein skalierter Radius für den maximalen Zoomfaktor mit der Berechnung `2 * Math.pow(2, minZoom - maxZoom)` festgelegt ist (unten dargestellt).

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Livebeispiel](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Ausdrücke mit variabler Bindung

Bei Ausdrücken mit variabler Bindung werden die Ergebnisse einer Berechnung in einer Variablen gespeichert. Auf diese Weise kann an anderer Stelle in einem Ausdruck mehrfach auf die Berechnungsergebnisse verwiesen werden. Dies ist eine nützliche Optimierung für Ausdrücke, die viele Berechnungen umfassen.

| Ausdruck | Rückgabetyp | BESCHREIBUNG |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Speichert einen oder mehrere Werte als Variablen zur Verwendung mit dem `var`-Ausdruck im untergeordneten Ausdruck, mit dem das Ergebnis zurückgegeben wird. |
| `['var', name: string]` | any | Verweist auf eine Variable, die mit dem `let`-Ausdruck erstellt wurde. |

**Beispiel**

In diesem Beispiel wird ein Ausdruck verwendet, mit dem der Umsatz relativ zum Temperaturverhältnis berechnet wird, und anschließend wird ein `case`-Ausdruck verwendet, um für diesen Wert verschiedene boolesche Vorgänge auszuwerten. Der `let`-Ausdruck wird zum Speichern des Umsatzes in Relation zum Temperaturverhältnis verwendet, sodass er nur einmal berechnet werden muss. Der `var`-Ausdruck verweist auf diese Variable so oft wie nötig, ohne ihn neu berechnen zu müssen.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Nächste Schritte

In den folgenden Artikeln finden Sie weitere Codebeispiele, in denen Ausdrücke implementiert werden:

> [!div class="nextstepaction"] 
> [Hinzufügen einer Symbolebene](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Hinzufügen einer Blasenebene](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Linienebene](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Hinzufügen einer Polygonebene](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Hinzufügen einer Wärmebildebene](map-add-heat-map-layer.md)

Weitere Informationen zu den Ebenenoptionen, die Ausdrücke unterstützen:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions-Schnittstelle](/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](/javascript/api/azure-maps-control/atlas.symbollayeroptions)