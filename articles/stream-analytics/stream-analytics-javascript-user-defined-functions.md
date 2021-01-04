---
title: Azure Stream Analytics – benutzerdefinierte JavaScript-Funktionen
description: Dieser Artikel enthält eine Einführung in benutzerdefinierte JavaScript-Funktionen in Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-js
ms.date: 06/16/2020
ms.openlocfilehash: 092e07ed01fb870cdcd9a3fd63d46d30cef96007
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780840"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Benutzerdefinierte JavaScript-Funktionen in Azure Stream Analytics
 
Azure Stream Analytics unterstützt benutzerdefinierte Funktionen, die in JavaScript geschrieben sind. Die umfassenden **String**-, **RegExp**-, **Math**-, **Array**- und **Date**-Methoden von JavaScript vereinfachen die Erstellung von komplexen Datentransformationen mit Stream Analytics-Aufträgen.

## <a name="overview"></a>Übersicht

Benutzerdefinierte JavaScript-Funktionen unterstützen zustandslose skalare Computefunktionen, für die keine externe Verbindung benötigt wird. Der Rückgabewert einer Funktion kann nur ein skalarer (einzelner) Wert sein. Nachdem Sie einem Auftrag eine benutzerdefinierte JavaScript-Funktion hinzugefügt haben, können Sie die Funktion wie eine integrierte Skalarfunktion an einer beliebigen Stelle in der Abfrage verwenden.

Hier finden Sie einige Szenarien, in denen benutzerdefinierte JavaScript-Funktionen hilfreich sein könnten:
* Analysieren und Bearbeiten von Zeichenfolgen mit Funktionen mit regulären Ausdrücken, z.B. **Regexp_Replace()** und **Regexp_Extract()**
* Decodieren und Codieren von Daten, z.B. Konvertierung von binär in hexadezimal
* Durchführen mathematischer Berechnungen mit **mathematischen** JavaScript-Funktionen
* Durchführen von Arrayvorgängen, z. B. Sortieren, Verknüpfen, Suchen und Füllen

Hier ist aufgeführt, was mit benutzerdefinierten JavaScript-Funktionen in Stream Analytics nicht möglich ist:
* Aufrufen von externen REST-Endpunkten, z. B. für die Durchführung umgekehrter IP-Suchen oder zum Abrufen von Referenzdaten aus einer externen Quelle
* Durchführen einer benutzerdefinierten Serialisierung oder Deserialisierung von Ereignisformaten für Ein-/Ausgaben
* Erstellen benutzerdefinierter Aggregate

Auch wenn die Verwendung von Funktionen wie **Date.GetDate()** oder **Math.random()** in der Funktionsdefinition nicht blockiert ist, ist es ratsam, auf sie zu verzichten. Bei diesen Funktionen wird **nicht** bei jedem Aufruf das gleiche Ergebnis zurückgegeben, und im Azure Stream Analytics-Dienst wird kein Journal mit den Funktionsaufrufen und den zurückgegebenen Ergebnissen geführt. Wenn eine Funktion unterschiedliche Ergebnisse für dieselben Ereignisse zurückgibt, ist daher beim Neustarten eines Auftrags durch Sie oder den Stream Analytics-Dienst die Wiederholbarkeit nicht sichergestellt.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Hinzufügen einer benutzerdefinierten JavaScript-Funktion zu einem Auftrag

> [!NOTE]
> Diese Schritte eignen sich für Stream Analytics-Aufträge, die für die Ausführung in der Cloud konfiguriert sind. Wenn Ihr Stream Analytics-Auftrag für die Ausführung in Azure IoT Edge konfiguriert ist, verwenden Sie stattdessen Visual Studio, und [schreiben Sie die benutzerdefinierte Funktion mit C#](stream-analytics-edge-csharp-udf.md).

Wählen Sie zum Erstellen einer benutzerdefinierten JavaScript-Funktion in Ihrem Stream Analytics-Auftrag unter **Auftragstopologie** die Option **Funktionen** aus. Wählen Sie anschließend im Dropdown Menü **+Hinzufügen** die Option **JavaScript-UDF** aus. 

![Hinzufügen einer JavaScript-UDF](./media/javascript/stream-analytics-jsudf-add.png)

Anschließend müssen Sie die folgenden Eigenschaften angeben und **Speichern** auswählen.

|Eigenschaft|BESCHREIBUNG|
|--------|-----------|
|Funktionsalias|Geben Sie einen Namen ein, um die Funktion in der Abfrage aufzurufen.|
|Ausgabetyp|Der Typ, der von der benutzerdefinierten JavaScript-Funktion an Ihre Stream Analytics-Abfrage zurückgegeben wird|
|Funktionsdefinition|Implementierung der JavaScript-Funktion, die jedes Mal ausgeführt wird, wenn die UDF von der Abfrage aufgerufen wird|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Tests und Problembehandlung für JavaScript-UDFs 

Sie können Ihre JavaScript-UDF-Logik in einem beliebigen Browser testen und debuggen. Das Debuggen und Testen der Logik dieser benutzerdefinierten Funktionen wird im Stream Analytics-Portal derzeit nicht unterstützt. Sobald die Funktion wie gewünscht funktioniert, können Sie sie wie oben beschrieben dem Stream Analytics-Auftrag hinzufügen und dann direkt über Ihre Abfrage aufrufen. Sie können Ihre Abfragelogik mit der JavaScript-UDF auch mithilfe von [Stream Analytics-Tools für Visual Studio](./stream-analytics-tools-for-visual-studio-install.md) testen.

JavaScript-Laufzeitfehler werden als schwerwiegend angesehen und im Aktivitätsprotokoll aufgezeichnet. Navigieren Sie im Azure-Portal zu Ihrem Auftrag, und wählen Sie **Aktivitätsprotokoll** aus, um das Protokoll abzurufen.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Aufrufen benutzerdefinierter JavaScript-Funktionen in einer Abfrage

Sie können Ihre JavaScript-Funktion in der Abfrage problemlos mithilfe des Funktionsalias mit dem Präfix **udf** aufrufen. Nachfolgend sehen Sie ein Beispiel für eine JavaScript-UDF, die Hexadezimalwerte in eine ganze Zahl konvertiert, die in einer Stream Analytics-Abfrage aufgerufen wird.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Unterstützte JavaScript-Objekte

Benutzerdefinierte JavaScript-Funktionen in Azure Stream Analytics unterstützen die integrierten JavaScript-Standardobjekte. Eine Liste dieser Objekte finden Sie unter [Globale Objekte](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics- und JavaScript-Typkonvertierung

Es gibt Unterschiede zwischen den Typen, die in der Stream Analytics-Abfragesprache und in JavaScript unterstützt werden. In der folgenden Tabelle sind die Konvertierungszuordnungen für diese beiden Sprachen aufgeführt:

Stream Analytics | JavaScript
--- | ---
BIGINT | Number (in JavaScript können nur ganze Zahlen bis genau 2^53 dargestellt werden)
Datetime | Date (JavaScript unterstützt nur Millisekunden)
double | Number
nvarchar(Max) | String
Datensatz | Object
Array | Array
NULL | Null

Konvertierungen von JavaScript zu Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Number | bigint (wenn die Zahl gerundet ist und zwischen long.MinValue und long.MaxValue liegt, andernfalls double)
Date | Datetime
String | nvarchar(Max)
Object | Datensatz
Array | Array
Null, Undefined | NULL
Beliebiger anderer Typ (z.B. eine Funktion oder ein Fehler) | Nicht unterstützt (führt zu einem Laufzeitfehler)

Die JavaScript-Sprache unterscheidet Groß-/Kleinschreibung, weshalb die Groß-/Kleinschreibung in den Objektfeldern im JavaScript-Code mit der Groß-/Kleinschreibung in den Feldern in den eingehenden Daten übereinstimmen muss. Aufträge mit dem Kompatibilitätsgrad 1.0 konvertieren Felder aus der SQL-SELECT-Anweisung in Kleinbuchstaben. Ab Kompatibilitätsgrad 1.1 haben Felder aus der SELECT-Anweisung die gleiche Schreibung wie in der SQL-Abfrage angegeben.

## <a name="other-javascript-user-defined-function-patterns"></a>Weitere benutzerdefinierte JavaScript-Funktionsmuster

### <a name="write-nested-json-to-output"></a>Schreiben von geschachteltem JSON-Code in die Ausgabe

Wenn ein nachfolgender Verarbeitungsschritt die Ausgabe des Stream Analytics-Auftrags als Eingabe verwendet und das JSON-Format benötigt, können Sie eine JSON-Zeichenfolge in die Ausgabe schreiben. Im nächsten Beispiel wird die Funktion **JSON.stringify()** aufgerufen, um alle Name-Wert-Paare der Eingabe zu packen und als einzelnen Zeichenfolgenwert in die Ausgabe zu schreiben.

**Definition von benutzerdefinierten JavaScript-Funktionen:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Beispielabfrage:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>Umwandeln einer Zeichenfolge in ein JSON-Objekt für die Verarbeitung

Wenn Sie ein Zeichenfolgenfeld im JSON-Format besitzen und es für die Verarbeitung in einer benutzerdefinierten JavaScript-Funktion konvertieren möchten, können Sie mithilfe der Funktion **JSON.parse()** ein JSON-Objekt erstellen, das anschließend verwendet werden kann.

**Definition von benutzerdefinierten JavaScript-Funktionen:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Beispielabfrage:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Verwenden von try/catch zur Fehlerbehandlung

try/catch-Blöcke können Ihnen dabei helfen, Probleme mit falsch formatierten Eingabedaten zu ermitteln, die an eine benutzerdefinierte JavaScript-Funktion übergeben werden.

**Definition von benutzerdefinierten JavaScript-Funktionen:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Beispielabfrage: Übergeben Sie den gesamten Datensatz als ersten Parameter, damit er bei einem Fehler zurückgegeben werden kann.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
Mit der Methode **toLocaleString** in JavaScript kann eine sprachabhängige Zeichenfolge zurückgegeben werden, die die Datums-/Uhrzeitdaten des Orts darstellt, von dem aus diese Methode aufgerufen wird.
Obwohl Azure Stream Analtyics als Systemzeitstempel nur Datums- und Uhrzeitangaben im UTC-Format akzeptiert, kann mithilfe dieser Methode der Systemzeitstempel in ein anderes Gebietsschema und eine andere Zeitzone konvertiert werden.
Diese Methode verhält sich bei der Implementierung genau so wie die in Internet Explorer verfügbare Methode.

**Definition von benutzerdefinierten JavaScript-Funktionen:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Beispielabfrage: Übergeben eines datetime-Werts als Eingabewert**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

Bei der Ausgabe dieser Abfrage handelt es sich um den datetime-Eingabewert in **de-DE** mit den bereitgestellten Optionen.
```
Samstag, 28. Dezember 2019
```

## <a name="next-steps"></a>Nächste Schritte

* [Machine Learning-UDF](./machine-learning-udf.md)
* [C#-UDF](./stream-analytics-edge-csharp-udf-methods.md)
