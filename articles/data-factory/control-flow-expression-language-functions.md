---
title: Ausdrücke und Funktionen in Azure Data Factory
description: Dieser Artikel enthält Informationen zu Ausdrücken und Funktionen, die Sie bei der Erstellung von Data Factory-Entitäten verwenden können.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 997700b27f52af174dab914097ceeef8d20ff148
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385624"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Ausdrücke und Funktionen in Azure Data Factory | Microsoft-Dokumentation

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-functions-variables.md)
> * [Aktuelle Version](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Informationen zu von Azure Data Factory unterstützten Ausdrücken und Funktionen. 

## <a name="expressions"></a>Ausdrücke

Bei den JSON-Werten in der Definition kann es sich um Literalwerte oder um Ausdrücke handeln, die zur Laufzeit ausgewertet werden. Beispiel:  
  
```json
"name": "value"
```

 oder  
  
```json
"name": "@pipeline().parameters.password"
```

Ausdrücke können an beliebiger Stelle in einem JSON-Zeichenfolgenwert verwendet werden und ergeben immer einen anderen JSON-Wert. Wenn ein JSON-Wert ein Ausdruck ist, wird der Text des Ausdrucks durch Entfernen des \@-Zeichens extrahiert. Falls Sie ein Zeichenfolgenliteral benötigen, das mit \@ beginnt, muss es wie folgt mit einem Escapezeichen versehen werden: \@\@. Die folgenden Beispiele veranschaulichen die Auswertung von Ausdrücken.  
  
|JSON-Wert|Ergebnis|  
|----------------|------------|  
|"parameters"|Die Zeichenfolge „parameters“ wird zurückgegeben.|  
|"parameters[1]"|Die Zeichenfolge „parameters[1]“ wird zurückgegeben.|  
|„\@\@“|Eine Zeichenfolge, die „\@“ enthält, wird zurückgegeben (ein Zeichen).|  
|" \@"|Eine Zeichenfolge, die „\@“ enthält, wird zurückgegeben (zwei Zeichen).|  
  
 Mit der *Zeichenfolgeninterpolation* können Ausdrücke auch innerhalb von Zeichenfolgen verwendet werden. Dabei werden die Ausdrücke in `@{ ... }` eingeschlossen. Beispiel: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Bei der Verwendung einer Zeichenfolgeninterpolation ist das Ergebnis immer eine Zeichenfolge. Angenommen, ich habe `myNumber` als `42` und `myString` als `foo` definiert:  
  
|JSON-Wert|Ergebnis|  
|----------------|------------|  
|"\@pipeline().parameters.myString"| Gibt `foo` als Zeichenfolge zurück.|  
|"\@{pipeline().parameters.myString}"| Gibt `foo` als Zeichenfolge zurück.|  
|"\@pipeline().parameters.myNumber"| Gibt `42` als *Zahl* zurück.|  
|"\@{pipeline().parameters.myNumber}"| Gibt `42` als *Zeichenfolge* zurück.|  
|"Answer is: @{pipeline().parameters.myNumber}"| Gibt die Zeichenfolge `Answer is: 42` zurück.|  
|"\@concat('Answer is: ', string(pipeline().parameters.myNumber))"| Gibt die Zeichenfolge `Answer is: 42` zurück.|  
|"Answer is: \@\@{pipeline().parameters.myNumber}"| Gibt die Zeichenfolge `Answer is: @{pipeline().parameters.myNumber}` zurück.|  
  
## <a name="examples"></a>Beispiele

### <a name="complex-expression-example"></a>Beispiel für komplexen Ausdruck
Das folgende Beispiel zeigt einen komplexen Ausdruck, der auf ein tiefes Unterfeld der Aktivitätsausgabe verweist. Wenn Sie auf einen Pipelineparameter verweisen möchten, der als ein Unterfeld ausgewertet wird, verwenden Sie die Syntax „[]“ statt des Punktoperators „(.)“ (wie bei „subfield1“ und „subfield2“).

@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*

### <a name="a-dataset-with-a-parameter"></a>Ein Dataset mit einem Parameter
Im folgenden Beispiel nutzt „BlobDataset“ einen Parameter namens **path**. Der Wert wird unter Verwendung des folgenden Ausdrucks zum Festlegen eines Werts für die Eigenschaft **folderPath** verwendet: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Eine Pipeline mit einem Parameter
Im folgenden Beispiel nutzt die Pipeline die Parameter **inputPath** und **outputPath**. Der Parameter **path** für das parametrisierte Blobdataset wird mithilfe der Werte dieser Parameter festgelegt. Die hier verwendete Syntax lautet `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Lernprogramm
In diesem [Tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) erfahren Sie, wie Sie Parameter zwischen einer Pipeline und einer Aktivität sowie zwischen den Aktivitäten übergeben können.

  
## <a name="functions"></a>Functions

Sie können Funktionen innerhalb von Ausdrücken aufrufen. Die folgenden Abschnitte enthalten Informationen zu den Funktionen, die in einem Ausdruck verwendet werden können.  

## <a name="string-functions"></a>Zeichenfolgenfunktionen  

Für die Arbeit mit Zeichenfolgen können Sie folgende Zeichenfolgenfunktionen und auch einige [Sammlungsfunktionen](#collection-functions) verwenden.
Zeichenfolgenfunktionen können nur in Zeichenfolgen verwendet werden.

| Zeichenfolgenfunktion | Aufgabe |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Kombiniert mindestens zwei Zeichenfolgen miteinander und gibt die kombinierte Zeichenfolge zurück. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Überprüft, ob eine Zeichenfolge mit der angegebenen Teilzeichenfolge endet. |
| [guid](control-flow-expression-language-functions.md#guid) | Generiert einen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als Zeichenfolge. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Gibt die Anfangsposition für eine Teilzeichenfolge zurück. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Gibt die Anfangsposition des letzten Vorkommens einer Teilzeichenfolge zurück. |
| [replace](control-flow-expression-language-functions.md#replace) | Ersetzt eine Teilzeichenfolge durch die angegebene Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück. |
| [split](control-flow-expression-language-functions.md#split) | Gibt ein Array mit Teilzeichenfolgen, die durch Trennzeichen getrennt sind, aus einer größeren Zeichenfolge basierend auf einem angegebenen Trennzeichen in der ursprünglichen Zeichenfolge zurück. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Überprüft, ob eine Zeichenfolge mit einer bestimmten Teilzeichenfolge beginnt. |
| [substring](control-flow-expression-language-functions.md#substring) | Gibt Zeichen aus einer Zeichenfolge zurück, beginnend mit der angegebenen Position. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Gibt eine Zeichenfolge in Kleinbuchstaben zurück. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Gibt eine Zeichenfolge in Großbuchstaben zurück. |
| [trim](control-flow-expression-language-functions.md#trim) | Entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück. |

## <a name="collection-functions"></a>Auflistungsfunktionen

Für die Arbeit mit Sammlungen (in der Regel Arrays, Zeichenfolgen und manchmal auch Bibliotheken) können Sie folgende Sammlungsfunktionen verwenden.

| Sammlungsfunktion | Aufgabe |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Überprüft, ob eine Sammlung ein bestimmtes Element enthält. |
| [empty](control-flow-expression-language-functions.md#empty) | Überprüft, ob eine Sammlung leer ist. |
| [first](control-flow-expression-language-functions.md#first) | Gibt das erste Element aus einer Sammlung zurück. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Gibt eine Sammlung zurück, die *nur* die gängigen Elemente aus den angegebenen Sammlungen enthält. |
| [join](control-flow-expression-language-functions.md#join) | Gibt eine Zeichenfolge zurück, die *alle* Elemente aus einem Array enthält, getrennt durch das angegebene Zeichen. |
| [last](control-flow-expression-language-functions.md#last) | Gibt das letzte Element aus einer Sammlung zurück. |
| [length](control-flow-expression-language-functions.md#length) | Gibt die Anzahl der Elemente in einer Zeichenfolge oder einem Array zurück. |
| [skip](control-flow-expression-language-functions.md#skip) | Entfernt Elemente vom Anfang einer Sammlung und gibt *alle anderen* Elemente zurück. |
| [take](control-flow-expression-language-functions.md#take) | Gibt Elemente vom Anfang einer Sammlung zurück. |
| [union](control-flow-expression-language-functions.md#union) | Gibt eine Sammlung zurück, die *sämtliche* Elemente aus den angegebenen Sammlungen enthält. | 

## <a name="logical-functions"></a>Logische Funktionen  

Diese Funktionen können innerhalb von Bedingungen zur Auswertung einer beliebigen Art von Logik verwendet werden.  
  
| Logische Vergleichsfunktion | Aufgabe |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Überprüft, ob für sämtliche Ausdrücke der Wert „TRUE“ festgelegt ist. |
| [equals](control-flow-expression-language-functions.md#equals) | Überprüft, ob beide Werte identisch sind. |
| [greater](control-flow-expression-language-functions.md#greater) | Überprüft, ob der erste Wert größer als der zweite ist. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Überprüft, ob der erste Wert größer als oder gleich dem zweiten ist. |
| [if](control-flow-expression-language-functions.md#if) | Überprüft, ob ein Ausdruck gleich „true“ oder „false“ ist. Gibt abhängig vom Ergebnis einen angegebenen Wert zurück. |
| [less](control-flow-expression-language-functions.md#less) | Überprüft, ob der erste Wert kleiner als der zweite ist. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Überprüft, ob der erste Wert kleiner als oder gleich dem zweiten ist. |
| [not](control-flow-expression-language-functions.md#not) | Überprüft, ob ein Ausdruck gleich „false“ ist. |
| [or](control-flow-expression-language-functions.md#or) | Überprüft, ob mindestens ein Ausdruck gleich „true“ ist. |
  
## <a name="conversion-functions"></a>Konvertierungsfunktionen  

 Diese Funktionen dienen zum Konvertieren zwischen den einzelnen nativen Typen in der Sprache:  
-   Zeichenfolge
-   integer
-   float
-   boolean
-   Arrays
-   Wörterbücher

| Konvertierungsfunktion | Aufgabe |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Gibt ein Array aus einer einzelnen angegebenen Eingabe zurück. Für mehrere Eingaben siehe [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Gibt die base64-codierte Version für eine Zeichenfolge zurück. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Gibt die Binärversion für eine base64-codierte Zeichenfolge zurück. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Gibt die Zeichenfolgenversion für eine base64-codierte Zeichenfolge zurück. |
| [binary](control-flow-expression-language-functions.md#binary) | Gibt die Binärversion für einen Eingabewert zurück. |
| [bool](control-flow-expression-language-functions.md#bool) | Gibt die Version des booleschen Werts für einen Eingabewert zurück. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Gibt den ersten Wert ungleich NULL aus mindestens einem Parameter zurück. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Gibt ein Array aus mehreren Eingaben zurück. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Gibt den Daten-URI für einen Eingabewert zurück. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Gibt die Binärversion für einen Daten-URI zurück. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Gibt die Zeichenfolgenversion für einen Daten-URI zurück. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Gibt die Zeichenfolgenversion für eine base64-codierte Zeichenfolge zurück. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Gibt die Binärversion für einen Daten-URI zurück. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Gibt eine Zeichenfolge zurück, in der Escapezeichen durch decodierte Versionen ersetzt sind. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Gibt eine Zeichenfolge zurück, die URL-unsichere Zeichen durch Escapezeichen ersetzt. |
| [float](control-flow-expression-language-functions.md#float) | Gibt eine Gleitkommazahl für einen Eingabewert zurück. |
| [int](control-flow-expression-language-functions.md#int) | Gibt die Ganzzahlversion für eine Zeichenfolge zurück. |
| [json](control-flow-expression-language-functions.md#json) | Gibt den JSON-Typwert oder das JSON-Objekt (JSON = JavaScript Object Notation) für eine Zeichenfolge oder XML zurück. |
| [string](control-flow-expression-language-functions.md#string) | Gibt die Zeichenfolgenversion für einen Eingabewert zurück. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Gibt die URI-codierte Version für einen Eingabewert zurück, indem URL-unsichere Zeichen durch Escapezeichen ersetzt werden. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Gibt die Binärversion für eine URI-codierte Zeichenfolge zurück. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Gibt die Zeichenfolgenversion für eine URI-codierte Zeichenfolge zurück. |
| [xml](control-flow-expression-language-functions.md#xml) | Gibt die XML-Version für eine Zeichenfolge zurück. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Überprüft die XML auf Knoten oder Werte, die mit einem XPath-Ausdruck (XML Path Language) übereinstimmen, und gibt die übereinstimmenden Knoten oder Werte zurück. |

## <a name="math-functions"></a>Mathematische Funktionen  
 Diese Funktionen können für folgende Arten von Zahlen verwendet werden: **ganze Zahlen** und **Gleitkommazahlen**.  

| Mathematische Funktion | Aufgabe |
| ------------- | ---- |
| [add](control-flow-expression-language-functions.md#add) | Gibt das Ergebnis der Addition zweier Zahlen zurück. |
| [div](control-flow-expression-language-functions.md#div) | Gibt das Ergebnis der Division zweier Zahlen zurück. |
| [max](control-flow-expression-language-functions.md#max) | Gibt den höchsten Wert aus einer Reihe von Zahlen oder einem Array zurück. |
| [min](control-flow-expression-language-functions.md#min) | Gibt den niedrigsten Wert aus einer Reihe von Zahlen oder einem Array zurück. |
| [mod](control-flow-expression-language-functions.md#mod) | Gibt den Restwert aus der Division zweier Zahlen zurück. |
| [mul](control-flow-expression-language-functions.md#mul) | Gibt das Produkt aus der Multiplikation zweier Zahlen zurück. |
| [rand](control-flow-expression-language-functions.md#rand) | Gibt eine beliebige ganze Zahl aus einem angegebenen Bereich zurück. |
| [range](control-flow-expression-language-functions.md#range) | Gibt ein Array mit ganzen Zahlen zurück, das mit einer angegebenen ganzen Zahl beginnt. |
| [sub](control-flow-expression-language-functions.md#sub) | Gibt das Ergebnis aus der Subtraktion der zweiten Zahl von der ersten Zahl zurück. |
  
## <a name="date-functions"></a>Datumsfunktionen  

| Datums- oder Zeitfunktion | Aufgabe |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Fügt eine Anzahl von Tagen zu einem Zeitstempel hinzu. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Fügt eine Anzahl von Stunden zu einem Zeitstempel hinzu. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Fügt eine Anzahl von Minuten zu einem Zeitstempel hinzu. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Fügt eine Anzahl von Sekunden zu einem Zeitstempel hinzu. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Fügt eine Anzahl von Zeiteinheiten zu einem Zeitstempel hinzu. Siehe auch [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Konvertiert einen Zeitstempel von der UTC-Zeitzone (UTC = Universal Time, Coordinated) in die Zielzeitzone. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Konvertiert einen Zeitstempel von der Quellzeitzone in die Zielzeitzone. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Konvertiert einen Zeitstempel von der Quellzeitzone in die UTC-Zeitzone (UTC = Universal Time, Coordinated). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Gibt den Tag der Monatskomponente eines Zeitstempels zurück. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Gibt den Tag der Wochenkomponente eines Zeitstempels zurück. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Gibt den Tag der Jahreskomponente eines Zeitstempels zurück. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Gibt den Zeitstempel als Zeichenfolge in optionalem Format zurück. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Gibt den aktuellen Zeitstempel plus der angegebenen Zeiteinheiten zurück. Siehe auch [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Gibt den aktuellen Zeitstempel abzüglich der angegebenen Zeiteinheiten zurück. Siehe auch [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Gibt den Beginn des Tages für einen Zeitstempel zurück. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Gibt den Beginn der Stunde für einen Zeitstempel zurück. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Gibt den Beginn des Monats für einen Zeitstempel zurück. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtrahiert eine Anzahl von Zeiteinheiten von einem Zeitstempel. Siehe auch [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Gibt den Eigenschaftswert `ticks` für einen angegebenen Zeitstempel zurück. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Gibt den aktuellen Zeitstempel als Zeichenfolge zurück. |

## <a name="function-reference"></a>Funktionsreferenz

In diesem Abschnitt werden alle verfügbaren Funktionen in alphabetischer Reihenfolge aufgelistet.

<a name="add"></a>

### <a name="add"></a>add

Gibt das Ergebnis der Addition zweier Zahlen zurück.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Ja | Integer, Float oder kombiniert | Die zu addierenden Zahlen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*result-sum*> | Integer oder Float | Das Ergebnis der Addition der angegebenen Zahlen |
||||

*Beispiel*

In diesem Beispiel werden die angegebenen Zahlen addiert:

```
add(1, 1.5)
```

Dies ist das zurückgegebene Ergebnis: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Fügt eine Anzahl von Tagen zu einem Zeitstempel hinzu.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*days*> | Ja | Integer | Die positive oder negative Anzahl von Tagen, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel plus der angegebenen Anzahl von Tagen  |
||||

*Beispiel 1*

In diesem Beispiel werden 10 Tage zu dem angegebenen Zeitstempel addiert:

```
addDays('2018-03-15T13:00:00Z', 10)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-25T00:00:0000000Z"`

*Beispiel 2*

In diesem Beispiel werden 10 Tage vom angegebenen Zeitstempel subtrahiert:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Fügt eine Anzahl von Stunden zu einem Zeitstempel hinzu.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*hours*> | Ja | Integer | Die positive oder negative Anzahl von Stunden, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel plus der angegebenen Anzahl von Stunden  |
||||

*Beispiel 1*

In diesem Beispiel werden 10 Stunden zu dem angegebenen Zeitstempel addiert:

```
addHours('2018-03-15T00:00:00Z', 10)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T10:00:0000000Z"`

*Beispiel 2*

In diesem Beispiel werden 10 Stunden vom angegebenen Zeitstempel subtrahiert:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Fügt eine Anzahl von Minuten zu einem Zeitstempel hinzu.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*minutes*> | Ja | Integer | Die positive oder negative Anzahl von Minuten, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel plus der angegebenen Anzahl von Minuten |
||||

*Beispiel 1*

In diesem Beispiel werden 10 Minuten zu dem angegebenen Zeitstempel addiert:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T00:20:00.0000000Z"`

*Beispiel 2*

In diesem Beispiel werden fünf Minuten vom angegebenen Zeitstempel subtrahiert:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T00:15:00.0000000Z"`

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Fügt eine Anzahl von Sekunden zu einem Zeitstempel hinzu.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*seconds*> | Ja | Integer | Die positive oder negative Anzahl von Sekunden, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel plus der angegebenen Anzahl von Sekunden  |
||||

*Beispiel 1*

In diesem Beispiel werden 10 Sekunden zu dem angegebenen Zeitstempel addiert:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T00:00:10.0000000Z"`

*Beispiel 2*

In diesem Beispiel werden fünf Sekunden vom angegebenen Zeitstempel subtrahiert:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Fügt eine Anzahl von Zeiteinheiten zu einem Zeitstempel hinzu.
Siehe auch [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die hinzugefügt werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel plus der angegebenen Anzahl von Zeiteinheiten  |
||||

*Beispiel 1*

In diesem Beispiel wird ein Tag zu dem angegebenen Zeitstempel addiert:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

Dies ist das zurückgegebene Ergebnis: `"2018-01-02T00:00:00.0000000Z"`

*Beispiel 2*

In diesem Beispiel wird ein Tag zu dem angegebenen Zeitstempel addiert:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

Dies ist das zurückgegebene Ergebnis mit dem optionalen „D“-Format: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

Überprüft, ob für beide Ausdrücke der Wert „true“ festgelegt ist.
Gibt „true“ zurück, wenn beide Ausdrücke gleich „true“ sind, oder gibt „false“ zurück, wenn mindestens ein Ausdruck gleich „false“ ist.

```
and(<expression1>, <expression2>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*> | Ja | Boolean | Die Ausdrücke, die überprüft werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn beide Ausdrücke gleich „true“ sind. Gibt „false“ zurück, wenn mindestens ein Ausdruck gleich „false“ ist. |
||||

*Beispiel 1*

In diesen Beispielen wird überprüft, ob beide angegebenen booleschen Werte gleich „true“ sind:

```
and(true, true)
and(false, true)
and(false, false)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Beide Ausdrücke sind gleich „true“, daher wird `true` zurückgegeben.
* Zweites Beispiel: Ein Ausdruck ist gleich „false“, daher wird `false` zurückgegeben.
* Drittes Beispiel: Beide Ausdrücke sind gleich „false“, daher wird `false` zurückgegeben.

*Beispiel 2*

In diesen Beispielen wird überprüft, ob beide angegebenen Ausdrücke gleich „true“ sind:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Beide Ausdrücke sind gleich „true“, daher wird `true` zurückgegeben.
* Zweites Beispiel: Ein Ausdruck ist gleich „false“, daher wird `false` zurückgegeben.
* Drittes Beispiel: Beide Ausdrücke sind gleich „false“, daher wird `false` zurückgegeben.

<a name="array"></a>

### <a name="array"></a>array

Gibt ein Array aus einer einzelnen angegebenen Eingabe zurück.
Informationen hinsichtlich mehrerer Eingaben finden Sie unter [createArray()](#createArray).

```
array('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge zum Erstellen eines Arrays |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | Ein Array, das die einzelne angegebene Eingabe enthält |
||||

*Beispiel*

In diesem Beispiel wird ein Array aus der Zeichenfolge „hello“ erstellt:

```
array('hello')
```

Dies ist das zurückgegebene Ergebnis: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Gibt die base64-codierte Version für eine Zeichenfolge zurück.

```
base64('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die eingegebene Zeichenfolge |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | Die base64-codierte Version für die eingegebene Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die Zeichenfolge „hello“ in eine base64-codierte Zeichenfolge konvertiert:

```
base64('hello')
```

Dies ist das zurückgegebene Ergebnis: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Gibt die Binärversion für eine base64-codierte Zeichenfolge zurück.

```
base64ToBinary('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die base64-codierte Zeichenfolge, die konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | Die Binärversion für die base64-codierte Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die base64-codierte Zeichenfolge „aGVsbG8“ in eine Binärzeichenfolge konvertiert:

```
base64ToBinary('aGVsbG8=')
```

Dies ist das zurückgegebene Ergebnis:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Gibt die Zeichenfolgenversion einer base64-codierten Zeichenfolge zurück, d. h., die base64-Zeichenfolge wird decodiert.
Sie sollten diese Funktion anstelle von [decodeBase64()](#decodeBase64) verwenden.
Obwohl beide Funktionen in gleicher Weise funktionieren, wird `base64ToString()` bevorzugt.

```
base64ToString('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die base64-codierte Zeichenfolge, die decodiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Die Zeichenfolgenversion für eine base64-codierte Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die base64-codierte Zeichenfolge „aGVsbG8“ in eine einfache Zeichenfolge konvertiert:

```
base64ToString('aGVsbG8=')
```

Dies ist das zurückgegebene Ergebnis: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>BINARY

Gibt die Binärversion für eine Zeichenfolge zurück.

```
binary('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge, die konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | Die Binärversion für die angegebene Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die Zeichenfolge „hello“ in eine Binärzeichenfolge konvertiert:

```
binary('hello')
```

Dies ist das zurückgegebene Ergebnis:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Gibt die boolesche Version eines Werts zurück.

```
bool(<value>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Any | Der zu konvertierende Wert |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Die booleschen Version des angegebenen Werts |
||||

*Beispiel*

In diesen Beispielen werden die angegebenen Werte in boolesche Werte konvertieren:

```
bool(1)
bool(0)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `true`
* Zweites Beispiel: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Gibt den ersten Wert ungleich NULL aus mindestens einem Parameter zurück.
Leere Zeichenfolgen, leere Arrays und leere Objekte sind nicht NULL.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Ja | Beliebig, Typen können kombiniert sein | Ein oder mehrere Elemente, die auf NULL geprüft werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Any | Das erste Element oder der erste Wert, das oder der ungleich NULL ist. Sind alle Parameter gleich NULL, gibt diese Funktion NULL zurück. |
||||

*Beispiel*

In diesen Beispielen wird für die angegebenen Werte der erste Wert ungleich NULL oder NULL zurückgegeben, wenn alle Werte gleich NULL sind:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `true`
* Zweites Beispiel: `"hello"`
* Drittes Beispiel: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Kombiniert mindestens zwei Zeichenfolgen miteinander und gibt die kombinierte Zeichenfolge zurück.

```
concat('<text1>', '<text2>', ...)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Ja | String | Mindestens zwei Zeichenfolgen, die kombiniert werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | Die Zeichenfolge, die aus den kombinierten Eingabezeichenfolgen erstellt wurde |
||||

*Beispiel*

In diesem Beispiel werden die Zeichenfolgen „Hello“ und „World“ kombiniert:

```
concat('Hello', 'World')
```

Dies ist das zurückgegebene Ergebnis: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Überprüft, ob eine Sammlung ein bestimmtes Element enthält.
Gibt „true“ zurück, wenn das Element gefunden wurde, gibt andernfalls „false“ zurück.
Für diese Funktion wird die Groß-/Kleinschreibung beachtet.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Diese Funktion ist insbesondere für diese Sammlungstypen vorgesehen:

* Eine *Zeichenfolge*, in der nach einer *Teilzeichenfolge* gesucht werden soll
* Ein *Array*, in dem nach einem *Wert* gesucht werden soll
* Ein *Wörterbuch*, in dem nach einem *Schlüssel* gesucht werden soll

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Zeichenfolge, Array oder Wörterbuch | Die Sammlung, die überprüft werden soll |
| <*value*> | Ja | Zeichenfolge, Array bzw. Wörterbuch | Das Element, nach dem gesucht werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“, wenn das Element gefunden wurde. Gibt „false“ zurück, wenn sie nicht gefunden wurde. |
||||

*Beispiel 1*

In diesem Beispiel wird die Zeichenfolge „hello world“ auf die Teilzeichenfolge „world“ geprüft und „true“ zurückgegeben:

```
contains('hello world', 'world')
```

*Beispiel 2*

In diesem Beispiel wird die Zeichenfolge „hello world“ auf die Teilzeichenfolge „universed“ geprüft und „false“ zurückgegeben:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Konvertiert einen Zeitstempel von der UTC-Zeitzone (UTC = Universal Time, Coordinated) in die Zielzeitzone.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*destinationTimeZone*> | Ja | String | Der Name für die Zielzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Der in das Format der Zielzeitzone konvertierte Zeitstempel |
||||

*Beispiel 1*

In diesem Beispiel wird ein Zeitstempel in das Format der angegebenen Zeitzone konvertiert:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

Dies ist das zurückgegebene Ergebnis: `"2018-01-01T00:00:00Z"`

*Beispiel 2*

In diesem Beispiel wird ein Zeitstempel in das Format der angegebenen Zeitzone konvertiert und formatiert:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

Dies ist das zurückgegebene Ergebnis: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Konvertiert einen Zeitstempel von der Quellzeitzone in die Zielzeitzone.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*sourceTimeZone*> | Ja | String | Der Name für die Quellzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*destinationTimeZone*> | Ja | String | Der Name für die Zielzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Der in das Format der Zielzeitzone konvertierte Zeitstempel |
||||

*Beispiel 1*

In diesem Beispiel wird ein Zeitstempel von der Quellzeitzone in die Zielzeitzone konvertiert.

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

Dies ist das zurückgegebene Ergebnis: `"2018-01-01T00:00:00.0000000"`

*Beispiel 2*

In diesem Beispiel wird eine Zeitzone in die angegebene Zeitzone konvertiert und formatiert:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Dies ist das zurückgegebene Ergebnis: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konvertiert einen Zeitstempel von der Quellzeitzone in die UTC-Zeitzone (UTC = Universal Time, Coordinated).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*sourceTimeZone*> | Ja | String | Der Name für die Quellzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | Der Zeitstempel, der in UTC konvertiert wurde |
||||

*Beispiel 1*

In diesem Beispiel wird ein Zeitstempel in UTC konvertiert:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

Dies ist das zurückgegebene Ergebnis: `"2018-01-01T08:00:00.0000000Z"`

*Beispiel 2*

In diesem Beispiel wird ein Zeitstempel in UTC konvertiert:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

Dies ist das zurückgegebene Ergebnis: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Gibt ein Array aus mehreren Eingaben zurück.
Informationen zu Arrays mit einzelner Eingabe finden Sie unter [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Ja | Beliebig, aber nicht kombiniert | Mindestens zwei Elemente, mit denen das Array erstellt wird |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | Das Array, das aus allen Eingabeelemente erstellt wurde |
||||

*Beispiel*

In diesem Beispiel wird ein Array aus diesen Eingaben erstellt:

```
createArray('h', 'e', 'l', 'l', 'o')
```

Dies ist das zurückgegebene Ergebnis: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Gibt einen Daten-URI (Uniform Resource Identifier) für eine Zeichenfolge zurück.

```
dataUri('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge, die konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | Der Daten-URI für die Eingabezeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird ein Daten-URI für die Zeichenfolge „hello“ erstellt:

```
dataUri('hello')
```

Dies ist das zurückgegebene Ergebnis: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Gibt die binäre Version eines Daten-URIs (Uniform Resource Identifier) zurück.
Verwenden Sie diese Funktion anstelle von [decodeDataUri()](#decodeDataUri).
Obwohl beide Funktionen in gleicher Weise funktionieren, wird `dataUriBinary()` bevorzugt.

```
dataUriToBinary('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Der Daten-URI, der konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | Die binäre Version für den Daten-URI |
||||

*Beispiel*

In diesem Beispiel wird eine binäre Version für diesen Daten-URI erstellt:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Dies ist das zurückgegebene Ergebnis:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Gibt die Zeichenfolgenversion für einen Daten-URI (Uniform Resource Identifier) zurück.

```
dataUriToString('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Der Daten-URI, der konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | Die Zeichenfolgenversion des Daten-URIs |
||||

*Beispiel*

In diesem Beispiel wird eine Zeichenfolge für diesen Daten-URI erstellt:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Dies ist das zurückgegebene Ergebnis: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Gibt den Tag des Monats aus einem Zeitstempel zurück.

```
dayOfMonth('<timestamp>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | Der Tag des Monats aus dem angegebenen Zeitstempel |
||||

*Beispiel*

In diesem Beispiel wird die Zahl für den Tag des Monats aus diesem Zeitstempel zurückgegeben:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

Dies ist das zurückgegebene Ergebnis: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Gibt den Wochentag aus einem Zeitstempel zurück.

```
dayOfWeek('<timestamp>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | Der Wochentag aus dem angegebenen Zeitstempel, wobei Folgendes gilt: Sonntag = 0, Montag = 1 usw. |
||||

*Beispiel*

In diesem Beispiel wird die Zahl für den Wochentag aus diesem Zeitstempel zurückgegeben:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

Dies ist das zurückgegebene Ergebnis: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Gibt den Tag des Jahres aus einem Zeitstempel zurück.

```
dayOfYear('<timestamp>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | Der Tag des Jahres aus dem angegebenen Zeitstempel |
||||

*Beispiel*

In diesem Beispiel wird die Zahl für den Tag des Jahres aus diesem Zeitstempel zurückgegeben:

```
dayOfYear('2018-03-15T13:27:36Z')
```

Dies ist das zurückgegebene Ergebnis: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Gibt die Zeichenfolgenversion einer base64-codierten Zeichenfolge zurück, d. h., die base64-Zeichenfolge wird decodiert.
Sie sollten [base64ToString()](#base64ToString) anstelle von `decodeBase64()` verwenden.
Obwohl beide Funktionen in gleicher Weise funktionieren, wird `base64ToString()` bevorzugt.

```
decodeBase64('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die base64-codierte Zeichenfolge, die decodiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Die Zeichenfolgenversion für eine base64-codierte Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird eine Zeichenfolge für eine base64-codierte Zeichenfolge erstellt:

```
decodeBase64('aGVsbG8=')
```

Dies ist das zurückgegebene Ergebnis: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Gibt die binäre Version eines Daten-URIs (Uniform Resource Identifier) zurück.
Sie sollten [dataUriToBinary()](#dataUriToBinary) anstelle von `decodeDataUri()` verwenden.
Obwohl beide Funktionen in gleicher Weise funktionieren, wird `dataUriToBinary()` bevorzugt.

```
decodeDataUri('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Daten-URI-Zeichenfolge, die decodiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | Die Binärversion für eine Daten-URI-Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die binäre Version für diesen Daten-URI zurückgegeben:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

Dies ist das zurückgegebene Ergebnis:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Gibt eine Zeichenfolge zurück, in der Escapezeichen durch decodierte Versionen ersetzt sind.

```
decodeUriComponent('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge mit den Escapezeichen, die decodiert werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Die aktualisierte Zeichenfolge mit den decodierten Escapezeichen |
||||

*Beispiel*

In diesem Beispiel werden die Escapezeichen in dieser Zeichenfolge durch decodierte-Versionen ersetzt:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

Dies ist das zurückgegebene Ergebnis: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Gibt das ganzzahlige Ergebnis der Division zweier Zahlen zurück.
Informationen zum Abrufen des Restwerts finden Sie unter [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Integer oder Float | Die Zahl, die durch den *Divisor* dividiert werden soll. |
| <*divisor*> | Ja | Integer oder Float | Die Zahl, durch die der *Dividend* geteilt wird; darf nicht 0 sein |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | Das ganzzahlige Ergebnis aus der Division der ersten Zahl durch die zweite Zahl |
||||

*Beispiel*

In beiden Beispielen wird die erste Zahl durch die zweite Zahl dividiert:

```
div(10, 5)
div(11, 5)
```

Dies ist das zurückgegebene Ergebnis: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Gibt eine URI-codierte (Uniform Resource Identifier) Version für eine Zeichenfolge zurück, indem URL-unsichere Zeichen durch Escapezeichen ersetzt werden.
Sie sollten [uriComponent()](#uriComponent) anstelle von `encodeUriComponent()` verwenden.
Obwohl beide Funktionen in gleicher Weise funktionieren, wird `uriComponent()` bevorzugt.

```
encodeUriComponent('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge, die in das URI-codierte Format konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Die URI-codierte Zeichenfolge mit Escapezeichen |
||||

*Beispiel*

In diesem Beispiel wird eine URI-codierte Version für diese Zeichenfolge erstellt:

```
encodeUriComponent('https://contoso.com')
```

Dies ist das zurückgegebene Ergebnis: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Überprüft, ob eine Sammlung leer ist.
Gibt „true“ zurück, wenn die Sammlung leer ist, oder gibt „false“ zurück, wenn sie nicht leer ist.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Zeichenfolge, Array oder Objekt | Die Sammlung, die überprüft werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn die Sammlung leer ist. Gibt „false“ zurück, wenn sie nicht leer ist. |
||||

*Beispiel*

In diesen Beispielen wird überprüft, ob die angegebenen Sammlungen leer sind:

```
empty('')
empty('abc')
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Es wird eine leere Zeichenfolge übergeben, weshalb die Funktion `true` zurückgibt.
* Zweites Beispiel: Es wird die Zeichenfolge „abc“ übergeben, weshalb die Funktion `false` zurückgibt.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Überprüft, ob eine Zeichenfolge mit einer bestimmten Teilzeichenfolge endet.
Gibt „true“ zurück, wenn die Teilzeichenfolge gefunden wurde, gibt andernfalls „false“ zurück.
Für diese Funktion wird die Groß-/Kleinschreibung nicht beachtet.

```
endsWith('<text>', '<searchText>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die überprüft werden soll |
| <*searchText*> | Ja | String | Die beendende Teilzeichenfolge, nach der gesucht werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false  | Boolean | Gibt „true“ zurück, wenn die beendende Teilzeichenfolge gefunden wurde. Gibt „false“ zurück, wenn sie nicht gefunden wurde. |
||||

*Beispiel 1*

In diesem Beispiel wird überprüft, ob die Zeichenfolge „hello world“ mit der Zeichenfolge „world“ endet:

```
endsWith('hello world', 'world')
```

Dies ist das zurückgegebene Ergebnis: `true`

*Beispiel 2*

In diesem Beispiel wird überprüft, ob die Zeichenfolge „hello world“ mit der Zeichenfolge „universe“ endet:

```
endsWith('hello world', 'universe')
```

Dies ist das zurückgegebene Ergebnis: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Überprüft, ob beide Werte, Ausdrücke oder Objekte gleichwertig sind.
Gibt „true“ zurück, wenn beide gleichwertig sind, gibt andernfalls „false“ zurück.

```
equals('<object1>', '<object2>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Ja | Verschiedene | Die Werte, Ausdrücke oder Objekte, die verglichen werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn beide gleichwertig sind. Gibt „false“ zurück, wenn sie nicht gleichwertig sind. |
||||

*Beispiel*

In diesen Beispielen wird überprüft, ob die angegebenen Eingaben gleichwertig sind:

```
equals(true, 1)
equals('abc', 'abcd')
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Beide Werte sind gleichwertig, weshalb die Funktion `true` zurückgibt.
* Zweites Beispiel: Beide Werte sind nicht gleichwertig, weshalb die Funktion `false` zurückgibt.

<a name="first"></a>

### <a name="first"></a>first

Gibt das erste Element aus einer Zeichenfolge oder einem Array zurück.

```
first('<collection>')
first([<collection>])
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Zeichenfolge oder Array | Die Sammlung, aus der das erste Element abgerufen werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Any | Das erste Element in der Sammlung |
||||

*Beispiel*

In diesen Beispielen wird jeweils das erste Element aus diesen Sammlungen abgerufen:

```
first('hello')
first(createArray(0, 1, 2))
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `"h"`
* Zweites Beispiel: `0`

<a name="float"></a>

### <a name="float"></a>float

Konvertiert die Zeichenfolgenversion einer Gleitkommazahl in eine tatsächliche Gleitkommazahl.

```
float('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge, die die gültige Gleitkommazahl angibt, die konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Die Gleitkommazahl für die angegebene Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird eine Zeichenfolgenversion für diese Gleitkommazahl erstellt:

```
float('10.333')
```

Dies ist das zurückgegebene Ergebnis: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Gibt einen Zeitstempel im angegebenen Format zurück.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | Der aktualisierte Zeitstempel im angegebenen Format |
||||

*Beispiel*

In diesem Beispiel wird ein Zeitstempel in das angegebene Format konvertiert:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Gibt den aktuellen Zeitstempel plus der angegebenen Zeiteinheiten zurück.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die hinzugefügt werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der aktuelle Zeitstempel plus der angegebenen Anzahl von Zeiteinheiten |
||||

*Beispiel 1*

Angenommen, der aktuelle Zeitstempel lautet „2018-03-01T00:00:00.0000000Z“.
In diesem Beispiel werden diesem Zeitstempel fünf Tage hinzugefügt:

```
getFutureTime(5, 'Day')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-06T00:00:00.0000000Z"`

*Beispiel 2*

Angenommen, der aktuelle Zeitstempel lautet „2018-03-01T00:00:00.0000000Z“.
In diesem Beispiel werden fünf Tage hinzugefügt, und das Ergebnis wird in das „D“-Format konvertiert:

```
getFutureTime(5, 'Day', 'D')
```

Dies ist das zurückgegebene Ergebnis: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Gibt den aktuellen Zeitstempel abzüglich der angegebenen Zeiteinheiten zurück.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die subtrahiert werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der aktuelle Zeitstempel abzüglich der angegebenen Anzahl von Zeiteinheiten |
||||

*Beispiel 1*

Angenommen, der aktuelle Zeitstempel lautet „2018-02-01T00:00:00.0000000Z“.
In diesem Beispiel werden fünf Tage von diesem Zeitstempel subtrahiert:

```
getPastTime(5, 'Day')
```

Dies ist das zurückgegebene Ergebnis: `"2018-01-27T00:00:00.0000000Z"`

*Beispiel 2*

Angenommen, der aktuelle Zeitstempel lautet „2018-02-01T00:00:00.0000000Z“.
In diesem Beispiel werden fünf Tage subtrahiert, und das Ergebnis wird in das „D“-Format konvertiert:

```
getPastTime(5, 'Day', 'D')
```

Dies ist das zurückgegebene Ergebnis: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Überprüft, ob der erste Wert größer als der zweite ist.
Gibt „true“ zurück, wenn der erste Wert größer ist, gibt andernfalls „false“ zurück.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Integer, Float oder Zeichenfolge | Der Wert (erster Wert), für den überprüft wird, ob er größer ist als der zweite Wert. |
| <*compareTo*> | Ja | Integer, Float bzw. Zeichenfolge | Der Vergleichswert |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn der erste Wert größer ist als der zweite Wert. Gibt „false“ zurück, wenn der erste Wert kleiner gleich dem zweiten Wert ist. |
||||

*Beispiel*

In diesen Beispiel wird überprüft, ob der erste Wert größer ist als der zweite Wert.

```
greater(10, 5)
greater('apple', 'banana')
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `true`
* Zweites Beispiel: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Überprüft, ob der erste Wert größer als oder gleich dem zweiten ist.
Gibt „true“ zurück, wenn der erste Wert größer gleich dem zweiten Wert ist, gibt andernfalls „false“ zurück.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Integer, Float oder Zeichenfolge | Der Wert (erster Wert), für den überprüft werden soll, ob er größer gleich dem zweiten Wert ist. |
| <*compareTo*> | Ja | Integer, Float bzw. Zeichenfolge | Der Vergleichswert |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn der erste Wert größer gleich dem zweiten Wert ist. Gibt „false“ zurück, wenn der erste Wert kleiner ist als der zweite Wert. |
||||

*Beispiel*

In diesen Beispiel wird überprüft, ob der erste Wert größer gleich dem zweiten Wert ist:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `true`
* Zweites Beispiel: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Generiert einen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als Zeichenfolge, z. B. „c2ecc88d-88c8-4096-912c-d6f2e2b138ce“:

```
guid()
```

Außerdem können Sie für den GUID ein anderes Format angeben, das vom Standardformat „D“ abweicht, das aus 32 durch Bindestriche getrennten Ziffern besteht.

```
guid('<format>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*format*> | Nein | String | Ein einzelner [Formatbezeichner](/dotnet/api/system.guid.tostring) für den zurückgegebenen GUID. Das Standardformat ist „D“, Sie können  aber „N“, „D“, „B“, „P“ oder „X“ verwenden. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | Ein zufällig generierter GUID |
||||

*Beispiel*

In diesem Beispiel wird derselbe GUID generiert, jedoch mit 32 Ziffern, die durch Bindestriche getrennt sind und in Klammern stehen:

```
guid('P')
```

Dies ist das zurückgegebene Ergebnis: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Überprüft, ob ein Ausdruck gleich „true“ oder „false“ ist.
Gibt abhängig vom Ergebnis einen angegebenen Wert zurück.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Ja | Boolean | Der zu überprüfende Ausdruck |
| <*valueIfTrue*> | Ja | Any | Der Wert, der zurückgegeben werden soll, wenn der Ausdruck gleich „true“ ist |
| <*valueIfFalse*> | Ja | Any | Der Wert, der zurückgegeben werden soll, wenn der Ausdruck gleich „false“ ist |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Any | Der angegebene Wert, der abhängig davon zurückgegeben wird, ob der Ausdruck gleich „true“ oder „false“ ist. |
||||

*Beispiel*

In diesem Beispiel wird `"yes"` zurückgegeben, weil für den angegebenen Ausdruck „true“ zurückgegeben wird.
Andernfalls wird in dem Beispiel `"no"` zurückgegeben:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Gibt die Anfangsposition oder den Anfangsindexwert für eine Teilzeichenfolge zurück.
Für diese Funktion wird die Groß-/Kleinschreibung nicht beachtet, und Indizes beginnen mit der Zahl 0.

```
indexOf('<text>', '<searchText>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die die Teilzeichenfolge enthält, nach der gesucht werden soll |
| <*searchText*> | Ja | String | Die Teilzeichenfolge, nach der gesucht werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Die Anfangsposition oder der Anfangsindexwert für die angegebene Teilzeichenfolge. <p>Wird die Zeichenfolge nicht gefunden, wird „-1“ zurückgegeben. |
||||

*Beispiel*

In diesem Beispiel wird für die Teilzeichenfolge „world“ nach dem Anfangsindexwert gesucht, den sie in der Zeichenfolge „hello world“ hat:

```
indexOf('hello world', 'world')
```

Dies ist das zurückgegebene Ergebnis: `6`

<a name="int"></a>

### <a name="int"></a>INT

Gibt die Ganzzahlversion für eine Zeichenfolge zurück.

```
int('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge, die konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | Die Ganzzahlversion für die angegebene Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die Ganzzahlversion für die Zeichenfolge „10“ erstellt:

```
int('10')
```

Dies ist das zurückgegebene Ergebnis: `10`

<a name="json"></a>

### <a name="json"></a>json

Gibt den JSON-Typwert oder das JSON-Objekt (JSON = JavaScript Object Notation) für eine Zeichenfolge oder XML zurück.

```
json('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Zeichenfolge oder XML | Die Zeichenfolge oder das XML-Objekt, die oder das konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Nativer JSON-Typ oder natives JSON-Objekt | Der Wert des nativen JSON-Typs oder das native JSON-Objekt für die angegebene Zeichenfolge oder das angegebene XML-Objekt. Hat die Zeichenfolge den Nullwert (NULL), gibt die Funktion ein leeres Objekt zurück. |
||||

*Beispiel 1*

In diesem Beispiel wird diese Zeichenfolge in den JSON-Wert konvertiert:

```
json('[1, 2, 3]')
```

Dies ist das zurückgegebene Ergebnis: `[1, 2, 3]`

*Beispiel 2*

In diesem Beispiel wird diese Zeichenfolge in JSON konvertiert:

```
json('{"fullName": "Sophia Owen"}')
```

Dies ist das zurückgegebene Ergebnis:

```
{
  "fullName": "Sophia Owen"
}
```

*Beispiel 3*

In diesem Beispiel wird dieses XML-Objekt in JSON konvertiert:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

Dies ist das zurückgegebene Ergebnis:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>Schnittmenge

Gibt eine Sammlung zurück, die *nur* die gängigen Elemente aus den angegebenen Sammlungen enthält.
Damit ein Element im Ergebnis enthalten ist, muss es in allen Sammlungen enthalten sein, die an diese Funktion übergeben werden.
Haben mehrere Elemente denselben Namen, enthält das Ergebnis das letzte Element mit diesem Namen.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Ja | Array oder Objekt, aber nicht beide | Die Sammlungen, aus denen Sie *nur* die gemeinsame Elemente wünschen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*common-items*> | Array bzw. Objekt | Eine Sammlung, die nur die Elemente enthält, die in jeder der angegebenen Sammlungen enthalten sind. |
||||

*Beispiel*

In diesem Beispiel wird nach den Elementen gesucht, die in jedem dieser Arrays enthalten sind:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

Es wird ein Array zurückgegeben, das *nur* diese Elemente enthält: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Gibt eine Zeichenfolge zurück, die alle Elemente aus einem Array enthält und in der je zwei Elemente durch ein *Trennzeichen* getrennt sind.

```
join([<collection>], '<delimiter>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Array | Das Array, das die Elemente enthält, die verknüpft werden sollen |
| <*delimiter*> | Ja | String | Das Trennzeichen, das zwischen den einzelnen Elementen in der Ergebniszeichenfolge steht. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | Die resultierende Zeichenfolge, die aus allen Elementen im angegebenen Array erstellt wurde |
||||

*Beispiel*

In diesem Beispiel wird eine Zeichenfolge erstellt, die aus allen Elementen in diesem Array und dem angegebenen Zeichen als Trennzeichen besteht:

```
join(createArray('a', 'b', 'c'), '.')
```

Dies ist das zurückgegebene Ergebnis: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

Gibt das letzte Element aus einer Sammlung zurück.

```
last('<collection>')
last([<collection>])
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Zeichenfolge oder Array | Die Sammlung, aus der das letzte Element abgerufen werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | Zeichenfolge bzw. Array | Das letzte Element in der Sammlung |
||||

*Beispiel*

In diesen Beispielen wird jeweils das letzte Element aus diesen Sammlungen abgerufen:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `"d"`
* Zweites Beispiel: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Gibt die Anfangsposition oder den Indexwert des letzten Vorkommens einer Teilzeichenfolge zurück.
Für diese Funktion wird die Groß-/Kleinschreibung nicht beachtet, und Indizes beginnen mit der Zahl 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die die Teilzeichenfolge enthält, nach der gesucht werden soll |
| <*searchText*> | Ja | String | Die Teilzeichenfolge, nach der gesucht werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | Die Anfangsposition oder der Indexwert des letzten Vorkommens der angegebenen Teilzeichenfolge. <p>Wird die Zeichenfolge nicht gefunden, wird „-1“ zurückgegeben. |
||||

*Beispiel*

In diesem Beispiel wird für die Teilzeichenfolge „world“ nach dem Anfangsindexwert des letzten Vorkommens gesucht, den sie in der Zeichenfolge „hello world“ hat:

```
lastIndexOf('hello world', 'world')
```

Dies ist das zurückgegebene Ergebnis: `6`

<a name="length"></a>

### <a name="length"></a>length

Gibt Anzahl von Elementen einer Sammlung zurück

```
length('<collection>')
length([<collection>])
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Zeichenfolge oder Array | Die Sammlung mit den Elementen, die gezählt werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | Die Anzahl von Elementen in der Sammlung |
||||

*Beispiel*

In diesen Beispielen wird die Anzahl von Elementen in diesen Sammlungen gezählt:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

Dies ist das zurückgegebene Ergebnis: `4`

<a name="less"></a>

### <a name="less"></a>less

Überprüft, ob der erste Wert kleiner als der zweite ist.
Gibt „true“ zurück, wenn der erste Wert kleiner ist, gibt andernfalls „false“ zurück.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Integer, Float oder Zeichenfolge | Der Wert (erster Wert), für den überprüft werden soll, ob er kleiner ist als der zweite Wert |
| <*compareTo*> | Ja | Integer, Float bzw. Zeichenfolge | Das Vergleichselement |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn der erste Wert kleiner ist als der zweite Wert. Gibt „false“ zurück, wenn der erste Wert größer gleich dem zweiten Wert ist. |
||||

*Beispiel*

In diesen Beispiel wird überprüft, ob der erste Wert kleiner ist als der zweite Wert.

```
less(5, 10)
less('banana', 'apple')
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `true`
* Zweites Beispiel: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Überprüft, ob der erste Wert kleiner als oder gleich dem zweiten ist.
Gibt „true“ zurück, wenn der erste Wert kleiner gleich dem zweiten Wert ist, gibt andernfalls „false“ zurück.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Integer, Float oder Zeichenfolge | Der Wert (erster Wert), für den überprüft werden soll, ob er kleiner gleich dem zweiten Wert ist. |
| <*compareTo*> | Ja | Integer, Float bzw. Zeichenfolge | Das Vergleichselement |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false  | Boolean | Gibt „true“ zurück, wenn der erste Wert kleiner gleich dem zweiten Wert ist. Gibt „false“ zurück, wenn der erste Wert größer ist als der zweite Wert. |
||||

*Beispiel*

In diesen Beispiel wird überprüft, ob der erste Wert kleiner gleich dem zweiten Wert ist.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `true`
* Zweites Beispiel: `false`

<a name="max"></a>

### <a name="max"></a>max

Gibt den größten Wert aus einer Liste oder einem Array mit Zahlen zurück, mit dem Wert an beiden Enden inklusive.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Ja | Integer, Float oder beide | Die Menge der Zahlen, aus denen Sie den größten Wert abrufen möchten |
| [<*number1*>, <*number2*>, ...] | Ja | Array: Integer, Float oder beide | Das Array mit den Zahlen, aus denen Sie den größten Wert abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*max-value*> | Integer oder Float | Der größte Wert in dem angegebenen Array oder in der angegebenen Menge von Zahlen |
||||

*Beispiel*

In diesen Beispielen wird der größte Wert aus der Menge von Zahlen und aus dem Array abgerufen:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

Dies ist das zurückgegebene Ergebnis: `3`

<a name="min"></a>

### <a name="min"></a>Min

Gibt den niedrigsten Wert aus einer Reihe von Zahlen oder einem Array zurück.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Ja | Integer, Float oder beide | Die Menge der Zahlen, aus denen Sie den kleinsten Wert abrufen möchten |
| [<*number1*>, <*number2*>, ...] | Ja | Array: Integer, Float oder beide | Das Array mit den Zahlen, aus denen Sie den kleinsten Wert abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*min-value*> | Integer oder Float | Der kleinste Wert in der angegebenen Menge von Zahlen oder in dem angegebenen Array |
||||

*Beispiel*

In diesen Beispielen wird der kleinste Wert aus der Menge von Zahlen und aus dem Array abgerufen:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

Dies ist das zurückgegebene Ergebnis: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Gibt den Restwert aus der Division zweier Zahlen zurück.
Informationen zum Abrufen des Ganzzahlergebnisses finden Sie unter [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Integer oder Float | Die Zahl, die durch den *Divisor* dividiert werden soll. |
| <*divisor*> | Ja | Integer oder Float | Die Zahl, durch die der *Dividend* geteilt wird; darf nicht 0 sein |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Integer oder Float | Der Rest aus der Division der ersten Zahl durch die zweite Zahl |
||||

*Beispiel*

In diesem Beispiel wird die erste Zahl durch die zweite Zahl dividiert:

```
mod(3, 2)
```

Dies ist das zurückgegebene Ergebnis: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Gibt das Produkt aus der Multiplikation zweier Zahlen zurück.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Ja | Integer oder Float | Die Zahl, mit der *multiplicand2* multipliziert werden soll. |
| <*multiplicand2*> | Ja | Integer oder Float | Die Zahl, mit der *multiplicand1* multipliziert werden soll. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*product-result*> | Integer oder Float | Das Produkt aus der Multiplikation der ersten Zahl mit der zweiten Zahl |
||||

*Beispiel*

In diesen Beispielen wird die erste Zahl mit der zweiten Zahl multipliziert:

```
mul(1, 2)
mul(1.5, 2)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `2`
* Zweites Beispiel: `3`

<a name="not"></a>

### <a name="not"></a>not

Überprüft, ob ein Ausdruck gleich „false“ ist.
Gibt „true“ zurück, wenn der Ausdruck gleich „false“ ist, oder gibt „false“ zurück, wenn er gleich „true“ ist.

```json
not(<expression>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Ja | Boolean | Der zu überprüfende Ausdruck |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn der Ausdruck gleich „false“ ist. Gibt „false“ zurück, wenn der Ausdruck gleich „true“ ist. |
||||

*Beispiel 1*

In diesen Beispielen wird überprüft, ob die angegebenen Ausdrücke gleich „false“ sind:

```json
not(false)
not(true)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Der Ausdruck ist gleich „false“, weshalb die Funktion `true` zurückgibt.
* Zweites Beispiel: Der Ausdruck ist gleich „true“, weshalb die Funktion `false` zurückgibt.

*Beispiel 2*

In diesen Beispielen wird überprüft, ob die angegebenen Ausdrücke gleich „false“ sind:

```json
not(equals(1, 2))
not(equals(1, 1))
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Der Ausdruck ist gleich „false“, weshalb die Funktion `true` zurückgibt.
* Zweites Beispiel: Der Ausdruck ist gleich „true“, weshalb die Funktion `false` zurückgibt.

<a name="or"></a>

### <a name="or"></a>oder

Überprüft, ob mindestens ein Ausdruck gleich „true“ ist.
Gibt „true“ zurück, wenn mindestens ein Ausdruck gleich „true“ ist, oder gibt „false“ zurück, wenn beide Ausdrücke gleich „false“ sind.

```
or(<expression1>, <expression2>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*> | Ja | Boolean | Die Ausdrücke, die überprüft werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn mindestens ein Ausdruck gleich „true“ ist. Gibt „false“ zurück, wenn beide Ausdrücke gleich „false“ sind. |
||||

*Beispiel 1*

In diesen Beispielen wird überprüft, ob mindestens ein Ausdruck gleich „true“ ist:

```json
or(true, false)
or(false, false)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Mindestens ein Ausdruck ist gleich „true“, weshalb die Funktion `true` zurückgibt.
* Zweites Beispiel: Beide Ausdrücke sind gleich „false“, weshalb die Funktion `false` zurückgibt.

*Beispiel 2*

In diesen Beispielen wird überprüft, ob mindestens ein Ausdruck gleich „true“ ist:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: Mindestens ein Ausdruck ist gleich „true“, weshalb die Funktion `true` zurückgibt.
* Zweites Beispiel: Beide Ausdrücke sind gleich „false“, weshalb die Funktion `false` zurückgibt.

<a name="rand"></a>

### <a name="rand"></a>rand

Gibt eine zufällige Ganzzahl  aus einem angegebenen Bereich zurück, wobei nur der Anfangswert inklusive ist.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Ja | Integer | Die kleinste ganze Zahl im Bereich |
| <*maxValue*> | Ja | Integer | Die ganze Zahl, die im Bereich auf die größte Zahl folgt, die die Funktion zurückgeben kann |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | Die zufällige ganze Zahl, die aus dem angegebenen Bereich zurückgegeben wird |
||||

*Beispiel*

In diesem Beispiel wird eine Zufallsganzzahl aus dem angegebenen Bereich abgerufen, wobei der größte Wert ausgeschlossen ist:

```
rand(1, 5)
```

Eine dieser Zahlen wird als Ergebnis zurückgegeben: `1`, `2`, `3` oder `4`

<a name="range"></a>

### <a name="range"></a>range

Gibt ein Array mit ganzen Zahlen zurück, das mit einer angegebenen ganzen Zahl beginnt.

```
range(<startIndex>, <count>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Ja | Integer | Ein ganzzahliger Wert, der das erste Element im Array ist |
| <*count*> | Ja | Integer | Die Anzahl von ganzen Zahlen im Array |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | Das Array mit ganzen Zahlen ab dem angegebenen Index |
||||

*Beispiel*

In diesem Beispiel wird ein Array aus ganzen Zahlen erstellt, das mit angegebenen Index beginnt und die angegebene Anzahl von ganzen Zahlen hat:

```
range(1, 4)
```

Dies ist das zurückgegebene Ergebnis: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

Ersetzt eine Teilzeichenfolge durch die angegebene Zeichenfolge und gibt die resultierende Zeichenfolge zurück. Für diese Funktion wird die Groß-/Kleinschreibung beachtet.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die die Teilzeichenfolge enthält, die ersetzt werden soll |
| <*oldText*> | Ja | String | Die Teilzeichenfolge, die ersetzt werden soll |
| <*newText*> | Ja | String | Die Ersatzzeichenfolge |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | Die aktualisierte Zeichenfolge, nachdem die Teilzeichenfolge ersetzt wurde. <p>Wenn die Teilzeichenfolge nicht gefunden wird, wird die ursprüngliche Zeichenfolge zurückgegeben. |
||||

*Beispiel*

In diesem Beispiel wird in „the old string“ nach der Teilzeichenfolge „old“ gesucht und „old“ durch „new“ ersetzt:

```
replace('the old string', 'old', 'new')
```

Dies ist das zurückgegebene Ergebnis: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

Entfernt Elemente vom Anfang einer Sammlung und gibt *alle anderen* Elemente zurück.

```
skip([<collection>], <count>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Array | Die Sammlung, aus der Sie Elemente entfernen möchten |
| <*count*> | Ja | Integer | Eine positive ganze Zahl für die Anzahl von Elementen, die am Anfang entfernt werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | Die aktualisierte Auflistung, nachdem die angegebenen Elemente entfernt wurden |
||||

*Beispiel*

In diesem Beispiel wird ein Element, die Zahl 0, am Anfang des angegebenen Arrays entfernt:

```
skip(createArray(0, 1, 2, 3), 1)
```

Es wird dieses Array mit den restlichen Elementen zurückgegeben: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Gibt ein Array mit Teilzeichenfolgen, die durch Trennzeichen getrennt sind, basierend auf einem angegebenen Trennzeichen in der ursprünglichen Zeichenfolge zurück.

```
split('<text>', '<delimiter>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die auf Grundlage des angegebenen Trennzeichens in der ursprünglichen Zeichenfolge in Teilzeichenfolgen unterteilt wird |
| <*delimiter*> | Ja | String | Das Zeichen in der ursprünglichen Zeichenfolge, das als Trennzeichen verwendet wird |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Ein Array mit Teilzeichenfolgen aus der ursprünglichen Zeichenfolge, das durch Trennzeichen getrennt ist |
||||

*Beispiel*

Dieses Beispiel erstellt ein Array mit Teilzeichenfolgen aus der angegebenen Zeichenfolge basierend auf dem angegebenen Zeichen als Trennzeichen:

```
split('a_b_c', '_')
```

Dieses Array wird als Ergebnis zurückgegeben: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Gibt den Beginn des Tages für einen Zeitstempel zurück.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der angegebene Zeitstempel, der jedoch bei null Uhr für den Tag beginnt |
||||

*Beispiel*

In diesem Beispiel wird der Beginn des Tags für diesen Zeitstempel ermittelt:

```
startOfDay('2018-03-15T13:30:30Z')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Gibt den Beginn der Stunde für einen Zeitstempel zurück.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der angegebene Zeitstempel, der jedoch bei null Minuten für die Stunde beginnt |
||||

*Beispiel*

In diesem Beispiel wird der Beginn der Stunde für diesen Zeitstempel ermittelt:

```
startOfHour('2018-03-15T13:30:30Z')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Gibt den Beginn des Monats für einen Zeitstempel zurück.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der angegebene Zeitstempel, der jedoch am ersten Tag des Monats um null Uhr beginnt |
||||

*Beispiel*

In diesem Beispiel wird der Beginn des Monats für diesen Zeitstempel zurückgegeben:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Überprüft, ob eine Zeichenfolge mit einer bestimmten Teilzeichenfolge beginnt.
Gibt „true“ zurück, wenn die Teilzeichenfolge gefunden wurde, gibt andernfalls „false“ zurück.
Für diese Funktion wird die Groß-/Kleinschreibung nicht beachtet.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die überprüft werden soll |
| <*searchText*> | Ja | String | Die beginnende Zeichenfolge, nach der gesucht werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false  | Boolean | Gibt „true“ zurück, wenn die beginnende Teilzeichenfolge gefunden wurde. Gibt „false“ zurück, wenn sie nicht gefunden wurde. |
||||

*Beispiel 1*

In diesem Beispiel wird überprüft, ob die Zeichenfolge „hello world“ mit der Teilzeichenfolge „world“ beginnt:

```
startsWith('hello world', 'hello')
```

Dies ist das zurückgegebene Ergebnis: `true`

*Beispiel 2*

In diesem Beispiel wird überprüft, ob die Zeichenfolge „hello world“ mit der Teilzeichenfolge „greetings“ beginnt:

```
startsWith('hello world', 'greetings')
```

Dies ist das zurückgegebene Ergebnis: `false`

<a name="string"></a>

### <a name="string"></a>Zeichenfolge

Gibt die Zeichenfolgenversion für einen Wert zurück.

```
string(<value>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Any | Der zu konvertierende Wert |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*string-value*> | String | Die Zeichenfolgenversion des angegebenen Werts |
||||

*Beispiel 1*

In diesem Beispiel wird die Zeichenfolgenversion dieser Zahl erstellt:

```
string(10)
```

Dies ist das zurückgegebene Ergebnis: `"10"`

*Beispiel 2*

In diesem Beispiel wird eine Zeichenfolge für das angegebene JSON-Objekt erstellt, und der umgekehrte Schrägstrich (\\) wird als Escapezeichen für die doppelten Anführungszeichen (") verwendet.

```
string( { "name": "Sophie Owen" } )
```

Dies ist das zurückgegebene Ergebnis: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Gibt das Ergebnis aus der Subtraktion der zweiten Zahl von der ersten Zahl zurück.

```
sub(<minuend>, <subtrahend>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Ja | Integer oder Float | Die Zahl, von der *subtrahend* subtrahiert werden soll |
| <*subtrahend*> | Ja | Integer oder Float | Die Zahl, die von *minuend* subtrahiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*result*> | Integer oder Float | Das Ergebnis aus der Subtraktion der zweiten Zahl von der ersten Zahl |
||||

*Beispiel*

In diesem Beispiel wird die zweite Zahl von der ersten Zahl subtrahiert:

```
sub(10.3, .3)
```

Dies ist das zurückgegebene Ergebnis: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Gibt Zeichen aus einer Zeichenfolge zurück, beginnend mit dem angegebenen Indexwert (Position).
Indexwerte beginnen bei 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, aus der die Zeichen zurückgegeben werden sollen |
| <*startIndex*> | Ja | Integer | Eine positive Zahl gleich oder größer als 0, die Sie als Ausgangswert oder Indexwert verwenden können. |
| <*length*> | Ja | Integer | Eine positive Anzahl von Zeichen, die in der Teilzeichenfolge enthalten sein sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | Eine Teilzeichenfolge, die die angegebene Anzahl von Zeichen enthält und in der Quellzeichenfolge an der angegebenen Indexposition beginnt |
||||

*Beispiel*

In diesem Beispiel wird aus der angegebenen Zeichenfolge ab dem Indexwert 6 eine Teilzeichenfolge mit fünf Zeichen erstellt:

```
substring('hello world', 6, 5)
```

Dies ist das zurückgegebene Ergebnis: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtrahiert eine Anzahl von Zeiteinheiten von einem Zeitstempel.
Siehe auch [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die subtrahiert werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel abzüglich der angegebenen Anzahl von Zeiteinheiten |
||||

*Beispiel 1*

In diesem Beispiel wird ein Tag von diesem Zeitstempel subtrahiert:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

Dies ist das zurückgegebene Ergebnis: `"2018-01-01T00:00:00:0000000Z"`

*Beispiel 2*

In diesem Beispiel wird ein Tag von diesem Zeitstempel subtrahiert:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

Dies ist das zurückgegebene Ergebnis mit dem optionalen „D“-Format: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Gibt Elemente vom Anfang einer Sammlung zurück.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Ja | Zeichenfolge oder Array | Die Sammlung, aus der Sie Elemente abrufen möchten |
| <*count*> | Ja | Integer | Eine positive ganze Zahl für die Anzahl von Elementen, die ab dem Anfang abgerufen werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*subset*> oder [<*subset*>] | Zeichenfolge bzw. Array | Eine Zeichenfolge oder ein Array, die oder das die angegebene Anzahl von Elementen ab dem Anfang der ursprünglichen Sammlung enthält |
||||

*Beispiel*

In diesen Beispielen wird die angegebene Anzahl von Elementen ab dem Anfang dieser Sammlungen abgerufen:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

Dies sind die zurückgegebenen Ergebnisse:

* Erstes Beispiel: `"abc"`
* Zweites Beispiel: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Gibt den Eigenschaftswert `ticks` für einen angegebenen Zeitstempel zurück.
Ein *tick* ist eine Zeitspanne von 100 Nanosekunden.

```
ticks('<timestamp>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge für einen Zeitstempel |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Die Anzahl von Ticks seit dem angegebenen Zeitstempel |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Gibt eine Zeichenfolge in Kleinbuchstaben zurück. Gibt es für ein Zeichen in der Zeichenfolge keine Kleinschreibungsversion, verbleibt dieses Zeichen unverändert in der zurückgegebenen Zeichenfolge.

```
toLower('<text>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die in Kleinbuchstaben zurückgegeben werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | Die ursprüngliche Zeichenfolge in Kleinbuchstaben |
||||

*Beispiel*

In diesem Beispiel wird diese Zeichenfolge in Kleinbuchstaben konvertiert:

```
toLower('Hello World')
```

Dies ist das zurückgegebene Ergebnis: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Gibt eine Zeichenfolge in Großbuchstaben zurück. Gibt es für ein Zeichen in der Zeichenfolge keine Großschreibungsversion, verbleibt dieses Zeichen unverändert in der zurückgegebenen Zeichenfolge.

```
toUpper('<text>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die in Großbuchstaben zurückgegeben werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | Die ursprüngliche Zeichenfolge in Großbuchstaben |
||||

*Beispiel*

In diesem Beispiel wird diese Zeichenfolge in Großbuchstaben konvertiert:

```
toUpper('Hello World')
```

Dies ist das zurückgegebene Ergebnis: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>trim

Entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück.

```
trim('<text>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, in der sich die führenden und nachfolgende Leerzeichen befinden, die entfernt werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | Eine aktualisierte Version der ursprünglichen Zeichenfolge ohne führende oder nachfolgende Leerzeichen |
||||

*Beispiel*

In diesem Beispiel werden das führende und das nachfolgende Leerzeichen aus der Zeichenfolge „Hello World“ entfernt:

```
trim(' Hello World  ')
```

Dies ist das zurückgegebene Ergebnis: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Gibt eine Sammlung zurück, die *sämtliche* Elemente aus den angegebenen Sammlungen enthält.
Damit ein Element im Ergebnis enthalten ist, kann es in irgendeiner der Sammlungen enthalten sein, die an diese Funktion übergeben werden. Haben mehrere Elemente denselben Namen, enthält das Ergebnis das letzte Element mit diesem Namen.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Ja | Array oder Objekt, aber nicht beide | Die Sammlungen, aus denen Sie *alle* Elemente wünschen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Array bzw. Objekt | Eine Sammlung mit allen Elementen aus den angegebenen Sammlungen – keine Duplikate |
||||

*Beispiel*

In diesem Beispiel werden *alle* Elemente aus diesen Auflistungen abgerufen:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

Dies ist das zurückgegebene Ergebnis: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Gibt eine URI-codierte (Uniform Resource Identifier) Version für eine Zeichenfolge zurück, indem URL-unsichere Zeichen durch Escapezeichen ersetzt werden.
Verwenden Sie diese Funktion anstelle von [encodeUriComponent()](#encodeUriComponent).
Obwohl beide Funktionen in gleicher Weise funktionieren, wird `uriComponent()` bevorzugt.

```
uriComponent('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge, die in das URI-codierte Format konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | Die URI-codierte Zeichenfolge mit Escapezeichen |
||||

*Beispiel*

In diesem Beispiel wird eine URI-codierte Version für diese Zeichenfolge erstellt:

```
uriComponent('https://contoso.com')
```

Dies ist das zurückgegebene Ergebnis: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Gibt die binäre Version einer URI-Komponente (Uniform Resource Identifier) zurück.

```
uriComponentToBinary('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die URI-codierte Zeichenfolge, die konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | Die Binärversion für die URI-codierte Zeichenfolge Der binäre Inhalt ist base64-codiert und wird von `$content` dargestellt. |
||||

*Beispiel*

In diesem Beispiel wird die binäre Version für diese URI-codierte Zeichenfolge erstellt:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

Dies ist das zurückgegebene Ergebnis:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Gibt die Zeichenfolgenversion einer URI-codierten Zeichenfolge (Uniform Resource Identifier) zurück, d. h., die URI-codierte Zeichenfolge wird decodiert.

```
uriComponentToString('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die URI-codierte Zeichenfolge, die decodiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | Die decodierte Version für die URI-codierte Zeichenfolge |
||||

*Beispiel*

In diesem Beispiel wird die decodierte Zeichenfolgenversion für diese URI-codierte Zeichenfolge erstellt:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

Dies ist das zurückgegebene Ergebnis: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Gibt den aktuellen Zeitstempel zurück.

```
utcNow('<format>')
```

Optional können Sie mit dem <*format*>-Parameter ein anderes Format angeben.

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss:fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | Das aktuelle Datum und die aktuelle Uhrzeit |
||||

*Beispiel 1*

Nehmen Sie an, heute ist der 15. April 2018, 13:00:00 Uhr.
In diesem Beispiel wird der aktuelle Zeitstempel abgerufen:

```
utcNow()
```

Dies ist das zurückgegebene Ergebnis: `"2018-04-15T13:00:00.0000000Z"`

*Beispiel 2*

Nehmen Sie an, heute ist der 15. April 2018, 13:00:00 Uhr.
In diesem Beispiel wird der aktuelle Zeitstempel mit dem optionalen „D“-Format abgerufen:

```
utcNow('D')
```

Dies ist das zurückgegebene Ergebnis: `"Sunday, April 15, 2018"`

<a name="xml"></a>

### <a name="xml"></a>Xml

Gibt die XML-Version einer Zeichenfolge zurück, die ein JSON-Objekt enthält.

```
xml('<value>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | String | Die Zeichenfolge mit dem JSON-Objekt, das konvertiert werden soll. <p>Das JSON-Objekt darf nur eine Stammeigenschaft haben, die kein Array sein darf. <br>Verwenden Sie den umgekehrten Schrägstrich (\\) als Escapezeichen für das doppelte Anführungszeichen ("). |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | Das codierte XML-Objekt für die angegebene Zeichenfolge oder das angegebene JSON-Objekt |
||||

*Beispiel 1*

In diesem Beispiel wird die XML-Version für diese Zeichenfolge erstellt, die ein JSON-Objekt enthält:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

Dies ist das zurückgegebene XML-Objekt:

```xml
<name>Sophia Owen</name>
```

*Beispiel 2*

Angenommen, Sie haben dieses JSON-Objekt:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

In diesem Beispiel wird das XML-Objekt für eine Zeichenfolge erstellt, die dieses JSON-Objekt enthält:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

Dies ist das zurückgegebene XML-Objekt:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Überprüft die XML auf Knoten oder Werte, die mit einem XPath-Ausdruck (XML Path Language) übereinstimmen, und gibt die übereinstimmenden Knoten oder Werte zurück. Ein XPath-Ausdruck oder einfach „XPath“ vereinfacht Ihnen das Navigieren in einer XML-Dokumentstruktur, sodass Sie im XML-Inhalt Knoten auswählen oder Werte berechnen können.

```
xpath('<xml>', '<xpath>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Ja | Any | Die XML-Zeichenfolge, in der nach Knoten oder Werten gesucht werden soll, die mit einem XPath-Ausdruckswert übereinstimmen |
| <*xpath*> | Ja | Any | Der XPath-Ausdruck, der für die Suche nach übereinstimmenden XML-Knoten oder -Werten verwendet wird |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Ein XML-Knoten, wenn nur ein einziger Knoten mit dem angegebenen XPath-Ausdruck übereinstimmt |
| <*value*> | Any | Der Wert aus einem XML-Knoten, wenn nur ein einziger Wert mit dem angegebenen XPath-Ausdruck übereinstimmt |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>Oder </br>[<*value1*>, <*value2*>, ...] | Array | Ein Array mit XML-Knoten oder -Werten, die mit den angegebenen XPath-Ausdruck übereinstimmen |
||||

*Beispiel 1*

Im Anschluss an das Beispiel 1 wird in diesem Beispiel nach Knoten gesucht, die mit dem `<count></count>`-Knoten übereinstimmen, und die Werte dieser Knoten werden mit der `sum()`-Funktion addiert:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Dies ist das zurückgegebene Ergebnis: `30`

*Beispiel 2*

In diesem Beispiel wird in beiden Ausdrücken nach Knoten gesucht, die mit dem `<location></location>`-Knoten in den angegebenen Argumenten übereinstimmen, die XML mit einem Namespace enthalten. In den Ausdrücken wird der umgekehrte Schrägstrich (\\) als Escapezeichen für das doppelte Anführungszeichen (") verwendet.

* *Ausdruck 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Ausdruck 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Dies sind die Argumente:

* Dieses XML-Objekt, das den Namespace des XML-Dokument enthält, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* Dies sind die beiden XPath-Ausdrücke:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Dies ist der Ergebnisknoten, der mit dem `<location></location>`-Knoten übereinstimmt:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Beispiel 3*

Im Anschluss an Beispiel 3 wird in diesem Beispiel nach dem der Wert im `<location></location>`-Knoten gesucht:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

Dies ist das zurückgegebene Ergebnis: `"Paris"`

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Systemvariablen, die Sie in Ausdrücken verwenden können, finden Sie unter [Systemvariablen](control-flow-system-variables.md).
