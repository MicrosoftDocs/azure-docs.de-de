---
title: Referenzhandbuch für Funktionen in Ausdrücken
description: Referenzhandbuch für Funktionen in Ausdrücken für Azure Logic Apps und Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: reference
ms.date: 01/13/2021
ms.openlocfilehash: 4ed5a26e1f871f7ac5fd8f29f0a66bc39a8013a1
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507247"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Referenzhandbuch für die Verwendung von Funktionen in Ausdrücken für Azure Logic Apps und Power Automate

Bei Workflowdefinitionen in [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und [Power Automate](/flow/getting-started) erhalten einige [Ausdrücke](../logic-apps/logic-apps-workflow-definition-language.md#expressions) ihre Werte von Laufzeitaktionen, die zu Beginn der Ausführung Ihres Workflows möglicherweise noch nicht vorhanden sind. Sie können mithilfe von *Funktionen*, die von der [Definitionssprache für Workflows](../logic-apps/logic-apps-workflow-definition-language.md) bereitgestellt werden, auf diese Werte in diesen Ausdrücken verweisen oder diese Werte in diesen Ausdrücken verarbeiten.

> [!NOTE]
> Diese Referenzseite gilt sowohl für Azure Logic Apps als auch für Power Automate, wird aber in der Dokumentation zu Azure Logic Apps angezeigt. Obwohl sich diese Seite speziell auf Logik-Apps bezieht, funktionieren diese Funktionen sowohl mit Flows als auch mit Logik-Apps. Weitere Informationen zu Funktionen und Ausdrücken in Power Automate finden Sie unter [Verwenden von Ausdrücken in Bedingungen](/flow/use-expressions-in-conditions).

Beispielsweise können Sie Werte berechnen, indem Sie mathematische Funktionen wie die [add()](../logic-apps/workflow-definition-language-functions-reference.md#add)-Funktion verwenden, wenn Sie die Summe aus Integer- oder Gleitkommazahlen erhalten möchten. Im Folgenden finden Sie weitere Beispielaufgaben, die Sie mit Funktionen ausführen können:

| Aufgabe | Funktionssyntax | Ergebnis |
| ---- | --------------- | ------ |
| Gibt eine Zeichenfolge in Kleinbuchstaben zurück. | toLower('<*text*>') <p>Beispiel: toLower('Hello') | "hello" |
| Gibt einen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) zurück. | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

In den nachfolgenden Tabellen finden Sie Funktionen [basierend auf dem allgemeinen Zweck](#ordered-by-purpose) aufgeführt. Ausführliche Informationen zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](#alphabetical-list).

## <a name="functions-in-expressions"></a>Funktionen in Ausdrücken

Um die Verwendung einer Funktion in einem Ausdruck zu veranschaulichen, zeigt dieses Beispiel, wie Sie den Wert über den Parameter `customerName` abrufen und ihn mithilfe der Funktion [parameters()](#parameters) in einem Ausdruck der Eigenschaft `accountName` zuweisen können:

```json
"accountName": "@parameters('customerName')"
```

Im Folgenden werden einige weitere allgemeine Möglichkeiten für die Verwendung von Funktionen in Ausdrücken aufgeführt:

| Aufgabe | Funktionssyntax in einem Ausdruck |
| ---- | -------------------------------- |
| Führen Sie Aufgaben mit einem Element durch, indem Sie dieses Element an eine Funktion übergeben. | „\@<*functionName*>(<*Element*>)“ |
| 1. Rufen Sie den Wert von *parameterName* mit der geschachtelten Funktion `parameters()` ab. </br>2. Führen Sie Aufgaben mit dem Ergebnis durch, indem Sie diesen Wert an *functionName* übergeben. | „\@<*functionName*>(parameters('<*parameterName*>'))“ |
| 1. Rufen Sie das Ergebnis der geschachtelten inneren Funktion *functionName* ab. </br>2. Übergeben Sie das Ergebnis an die äußere Funktion *functionName2*. | „\@<*functionName2*>(<*functionName*>(<*Element*>))“ |
| 1. Abrufen des Ergebnisses aus *functionName*. </br>2. Da das Ergebnis ein Objekt mit der Eigenschaft *propertyName* ist, rufen Sie den Eigenschaftswert ab. | „\@<*functionName*>(<*Element*>).<*propertyName*>“ |
|||

Die Funktion `concat()` kann z.B. mindestens zwei Zeichenfolgenwerte als Parameter aufweisen. In dieser Funktion werden diese Zeichenfolgen in einer Zeichenfolge kombiniert. Sie können Zeichenfolgenliterale eingeben, wie z.B. „Sophia“ und „Owen“, um eine kombinierte Zeichenfolge („SophiaOwen“) zu erhalten:

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Alternativ können Sie Zeichenfolgenwerte von Parametern abrufen. In diesem Beispiel wird die Funktion `parameters()` in den einzelnen `concat()`-Parametern und den Parametern `firstName` und `lastName` verwendet. Anschließend übergeben Sie die resultierenden Zeichenfolgen an die Funktion `concat()`, um eine kombinierte Zeichenfolge zu erhalten, wie z.B. „SophiaOwen“:

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

Das Ergebnis wird in beiden Fällen und Beispielen der Eigenschaft `customerName` zugewiesen.

Im Folgenden finden Sie einige weitere Hinweise zu Funktionen in Ausdrücken:

* Funktionsparameter werden von links nach rechts ausgewertet.

* In der Syntax für Parameterdefinitionen bedeutet ein Fragezeichen (?), das hinter einem Parameter steht, dass der Parameter optional ist. Sie finden dies beispielsweise in [getFutureTime()](#getFutureTime).

In den folgenden Abschnitten sind die Funktionen nach allgemeinem Zweck sortiert. Alternativ können Sie sich diese Funktionen auch in [alphabetischer Reihenfolge](#alphabetical-list) ansehen.

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Zeichenfolgenfunktionen

Für die Arbeit mit Zeichenfolgen können Sie folgende Zeichenfolgenfunktionen und auch einige [Sammlungsfunktionen](#collection-functions) verwenden. Zeichenfolgenfunktionen können nur in Zeichenfolgen verwendet werden.

| Zeichenfolgenfunktion | Aufgabe |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Kombiniert mindestens zwei Zeichenfolgen miteinander und gibt die kombinierte Zeichenfolge zurück. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Überprüft, ob eine Zeichenfolge mit der angegebenen Teilzeichenfolge endet. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Gibt eine Zahl als Zeichenfolge zurück, basierend auf dem angegebenen Format. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generiert einen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) als Zeichenfolge. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Gibt die Anfangsposition für eine Teilzeichenfolge zurück. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Gibt die Anfangsposition des letzten Vorkommens einer Teilzeichenfolge zurück. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Gibt die Anzahl der Elemente in einer Zeichenfolge oder einem Array zurück. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Ersetzt eine Teilzeichenfolge durch die angegebene Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Gibt ein Array mit Teilzeichenfolgen, die durch Trennzeichen getrennt sind, aus einer größeren Zeichenfolge basierend auf einem angegebenen Trennzeichen in der ursprünglichen Zeichenfolge zurück. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Überprüft, ob eine Zeichenfolge mit einer bestimmten Teilzeichenfolge beginnt. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Gibt Zeichen aus einer Zeichenfolge zurück, beginnend mit der angegebenen Position. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Gibt eine Zeichenfolge in Kleinbuchstaben zurück. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Gibt eine Zeichenfolge in Großbuchstaben zurück. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Entfernt führende und nachfolgende Leerzeichen aus einer Zeichenfolge und gibt die aktualisierte Zeichenfolge zurück. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Auflistungsfunktionen

Für die Arbeit mit Sammlungen (in der Regel Arrays, Zeichenfolgen und manchmal auch Bibliotheken) können Sie folgende Sammlungsfunktionen verwenden.

| Sammlungsfunktion | Aufgabe |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Überprüft, ob eine Sammlung ein bestimmtes Element enthält. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Überprüft, ob eine Sammlung leer ist. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Gibt das erste Element aus einer Sammlung zurück. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Gibt eine Sammlung zurück, die *nur* die gängigen Elemente aus den angegebenen Sammlungen enthält. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gibt in einer wiederholten Aktion für ein Array das aktuelle Element im Array während der aktuellen Iteration der Aktion zurück. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Gibt eine Zeichenfolge zurück, die *alle* Elemente aus einem Array enthält, getrennt durch das angegebene Zeichen. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Gibt das letzte Element aus einer Sammlung zurück. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Gibt die Anzahl der Elemente in einer Zeichenfolge oder einem Array zurück. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Entfernt Elemente vom Anfang einer Sammlung und gibt *alle anderen* Elemente zurück. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Gibt Elemente vom Anfang einer Sammlung zurück. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Gibt eine Sammlung zurück, die *sämtliche* Elemente aus den angegebenen Sammlungen enthält. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Logische Vergleichsfunktionen

Sie können für die Arbeit mit Bedingungen, das Vergleichen von Werten und Ausdrucksergebnissen oder das Auswerten verschiedener Logiken folgende logische Vergleichsfunktionen verwenden. Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Wenn Sie logische Funktionen oder Bedingungen zum Vergleichen von Werten verwenden, werden NULL-Werte in leere Zeichenfolgenwerte (`""`) konvertiert. Das Verhalten der Bedingungen unterscheidet sich, wenn Sie eine leere Zeichenfolge anstelle eines NULL-Werts für den Vergleich verwenden. Weitere Informationen finden Sie unter [string](#string). 

| Logische Vergleichsfunktion | Aufgabe |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Überprüft, ob für sämtliche Ausdrücke der Wert „TRUE“ festgelegt ist. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Überprüft, ob beide Werte identisch sind. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Überprüft, ob der erste Wert größer als der zweite ist. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Überprüft, ob der erste Wert größer als oder gleich dem zweiten ist. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Überprüft, ob ein Ausdruck gleich „true“ oder „false“ ist. Gibt abhängig vom Ergebnis einen angegebenen Wert zurück. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Überprüft, ob der erste Wert kleiner als der zweite ist. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Überprüft, ob der erste Wert kleiner als oder gleich dem zweiten ist. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Überprüft, ob ein Ausdruck gleich „false“ ist. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Überprüft, ob mindestens ein Ausdruck gleich „true“ ist. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Konvertierungsfunktionen

Sie können mit den folgenden Konvertierungsfunktionen den Typ oder das Format eines Werts ändern. So können Sie beispielsweise einen Wert von einem booleschen Wert in einen ganzzahligen Wert ändern. Weitere Informationen darüber, wie Logic Apps Inhaltstypen während des Wechsels behandelt, finden Sie unter [Behandeln von Inhaltstypen](../logic-apps/logic-apps-content-type.md). Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Mit Azure Logic Apps werden Werte automatisch zwischen einigen Datentypen konvertiert, sodass Sie diese Konvertierungen nicht manuell ausführen müssen. Führen Sie diese dennoch aus, tritt möglicherweise ein unerwartetes Anzeigeverhalten auf, das sich nicht auf die eigentlichen Konvertierungen auswirkt, sondern nur auf ihre Anzeige. Weitere Informationen finden Sie unter [Implizite Datentypkonvertierungen](#implicit-data-conversions).

| Konvertierungsfunktion | Aufgabe |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Gibt ein Array aus einer einzelnen angegebenen Eingabe zurück. Für mehrere Eingaben siehe [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Gibt die base64-codierte Version für eine Zeichenfolge zurück. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Gibt die Binärversion für eine base64-codierte Zeichenfolge zurück. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Gibt die Zeichenfolgenversion für eine base64-codierte Zeichenfolge zurück. |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Gibt die Binärversion für einen Eingabewert zurück. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Gibt die Version des booleschen Werts für einen Eingabewert zurück. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Gibt ein Array aus mehreren Eingaben zurück. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Gibt den Daten-URI für einen Eingabewert zurück. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Gibt die Binärversion für einen Daten-URI zurück. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Gibt die Zeichenfolgenversion für einen Daten-URI zurück. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Gibt die Zeichenfolgenversion für eine base64-codierte Zeichenfolge zurück. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Gibt die Binärversion für einen Daten-URI zurück. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Gibt eine Zeichenfolge zurück, in der Escapezeichen durch decodierte Versionen ersetzt sind. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Gibt eine Zeichenfolge zurück, die URL-unsichere Zeichen durch Escapezeichen ersetzt. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Gibt eine Gleitkommazahl für einen Eingabewert zurück. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Gibt die Ganzzahlversion für eine Zeichenfolge zurück. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Gibt den JSON-Typwert oder das JSON-Objekt (JSON = JavaScript Object Notation) für eine Zeichenfolge oder XML zurück. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Gibt die Zeichenfolgenversion für einen Eingabewert zurück. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Gibt die URI-codierte Version für einen Eingabewert zurück, indem URL-unsichere Zeichen durch Escapezeichen ersetzt werden. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Gibt die Binärversion für eine URI-codierte Zeichenfolge zurück. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Gibt die Zeichenfolgenversion für eine URI-codierte Zeichenfolge zurück. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Gibt die XML-Version für eine Zeichenfolge zurück. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Implizite Datentypkonvertierungen

Mit Azure Logic Apps werden Werte automatisch oder implizit zwischen einigen Datentypen konvertiert, sodass Sie diese Typen nicht manuell konvertieren müssen. Wenn Sie z. B. Werte verwenden, die keine Zeichenfolgen sind, und Zeichenfolgen als Eingaben erwartet werden, konvertiert Logic Apps die Nicht-Zeichenfolgenwerte automatisch in Zeichenfolgen.

Angenommen, ein Auslöser gibt einen numerischen Wert als Ausgabe zurück:

`triggerBody()?['123']`

Wenn Sie diese numerische Ausgabe verwenden und eine Zeichenfolgeneingabe wie z. B. eine URL erwartet wird, konvertiert Logic Apps den Wert mithilfe von geschweiften Klammern (`{}`) automatisch in eine Zeichenfolge:

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Base64-Codierung und -Decodierung

Logic Apps führt automatisch oder implizit eine Base64-Codierung oder -Decodierung durch, daher müssen Sie diese Vorgänge nicht mithilfe der entsprechenden Ausdrücke manuell ausführen:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Wenn Sie Ihrer Logik-App diese Ausdrücke manuell hinzufügen, beispielsweise mit dem Ausdrucks-Editor, vom Logik-App-Designer weg und dann wieder zu diesem zurück navigieren, zeigt der Designer nur die Parameterwerte an. Die Ausdrücke werden nur dann in der Codeansicht beibehalten, wenn Sie die Parameterwerte nicht bearbeiten. Andernfalls entfernt Logic Apps die Ausdrücke aus der Codeansicht und behält nur die Parameterwerte bei. Dieses Verhalten wirkt sich nicht auf die Codierung oder Decodierung aus, sondern nur auf die Anzeige der Ausdrücke.

<a name="math-functions"></a>

## <a name="math-functions"></a>Mathematische Funktionen

Sie können mithilfe der folgenden mathematischen Funktionen mit ganzen Zahlen und Gleitkommazahlen arbeiten.
Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Mathematische Funktion | Aufgabe |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Gibt das Ergebnis der Addition zweier Zahlen zurück. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Gibt das Ergebnis der Division zweier Zahlen zurück. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Gibt den höchsten Wert aus einer Reihe von Zahlen oder einem Array zurück. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Gibt den niedrigsten Wert aus einer Reihe von Zahlen oder einem Array zurück. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Gibt den Restwert aus der Division zweier Zahlen zurück. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Gibt das Produkt aus der Multiplikation zweier Zahlen zurück. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Gibt eine beliebige ganze Zahl aus einem angegebenen Bereich zurück. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Gibt ein Array mit ganzen Zahlen zurück, das mit einer angegebenen ganzen Zahl beginnt. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Gibt das Ergebnis aus der Subtraktion der zweiten Zahl von der ersten Zahl zurück. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Datums- und Uhrzeitfunktionen

Sie können für die Arbeit mit Datums- und Uhrzeitangaben folgende Datums- und Uhrzeitfunktionen verwenden.
Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Datums- oder Zeitfunktion | Aufgabe |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Fügt eine Anzahl von Tagen zu einem Zeitstempel hinzu. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Fügt eine Anzahl von Stunden zu einem Zeitstempel hinzu. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Fügt eine Anzahl von Minuten zu einem Zeitstempel hinzu. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Fügt eine Anzahl von Sekunden zu einem Zeitstempel hinzu. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Fügt eine Anzahl von Zeiteinheiten zu einem Zeitstempel hinzu. Siehe auch [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Konvertiert einen Zeitstempel von der UTC-Zeitzone (UTC = Universal Time, Coordinated) in die Zielzeitzone. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Konvertiert einen Zeitstempel von der Quellzeitzone in die Zielzeitzone. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Konvertiert einen Zeitstempel von der Quellzeitzone in die UTC-Zeitzone (UTC = Universal Time, Coordinated). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Gibt den Tag der Monatskomponente eines Zeitstempels zurück. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Gibt den Tag der Wochenkomponente eines Zeitstempels zurück. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Gibt den Tag der Jahreskomponente eines Zeitstempels zurück. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Gibt das Datum eines Zeitstempels zurück. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Gibt den aktuellen Zeitstempel plus der angegebenen Zeiteinheiten zurück. Siehe auch [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Gibt den aktuellen Zeitstempel abzüglich der angegebenen Zeiteinheiten zurück. Siehe auch [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Gibt den Beginn des Tages für einen Zeitstempel zurück. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Gibt den Beginn der Stunde für einen Zeitstempel zurück. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Gibt den Beginn des Monats für einen Zeitstempel zurück. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrahiert eine Anzahl von Zeiteinheiten von einem Zeitstempel. Siehe auch [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Gibt den Eigenschaftswert `ticks` für einen angegebenen Zeitstempel zurück. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Gibt den aktuellen Zeitstempel als Zeichenfolge zurück. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Workflowfunktionen

Diese Workflowfunktionen können für folgende Aktionen hilfreich sein:

* Abrufen von Details zu einer Workflowinstanz zur Laufzeit.
* Arbeiten mit den Eingaben für die Instanziierung von Logik-Apps oder Flows.
* Verweisen auf die Ausgaben von Triggern und Aktionen.

Sie können beispielsweise auf die Ausgaben einer Aktion verweisen und die Daten in einer späteren Aktion verwenden.
Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Workflowfunktion | Aufgabe |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Gibt die Ausgabe der aktuellen Aktion zur Laufzeit oder Werte aus anderen Name/Wert-Paaren im JSON-Format zurück. Siehe auch [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Gibt die `body`-Ausgabe einer Aktion zur Laufzeit zurück. Siehe auch [body](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Gibt die Ausgabe einer Aktion zur Laufzeit zurück. Siehe [Ausgaben](../logic-apps/workflow-definition-language-functions-reference.md#outputs) und [Aktionen](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Gibt die Ausgabe einer Aktion zur Laufzeit oder Werte aus anderen Name/Wert-Paaren im JSON-Format zurück. Siehe auch [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Gibt die `body`-Ausgabe einer Aktion zur Laufzeit zurück. Siehe auch [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Erstellt ein Array mit den Werten, die mit einem Schlüsselnamen in Aktionsausgaben vom Typ *form-data* oder *form-encoded* übereinstimmen. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Gibt einen einzelnen Wert zurück, der mit einem Schlüsselnamen in einer Aktionsausgabe vom Typ *form-data* oder *form-encoded* übereinstimmt. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Gibt in einer wiederholten Aktion für ein Array das aktuelle Element im Array während der aktuellen Iteration der Aktion zurück. |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Gibt in einer „Foreach“- oder „Until“-Schleife das aktuelle Element aus der angegebenen Schleife zurück.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Gibt in einer „Until“-Schleife den Indexwert für die aktuelle Iteration zurück. Sie können diese Funktion in geschachtelten „Until“-Schleifen verwenden. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Gibt die „Rückruf-URL“ zurück, die einen Trigger oder eine Aktion aufruft. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Gibt den Textteil für einen bestimmten Teil einer Aktionsausgabe zurück, die mehrere Teile hat. |
| [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Gibt die Ausgabe einer Aktion zur Laufzeit zurück. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Gibt den Wert für einen Parameter zurück, der in Ihrer Workflowdefinition beschrieben wird. |
| [result](../logic-apps/workflow-definition-language-functions-reference.md#result) | Gibt die Eingaben und Ausgaben aller Aktionen zurück, die in der angegebenen bereichsbezogenen Aktion enthalten sind, z. B. `For_each`, `Until` und `Scope`. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Gibt die Ausgabe eines Triggers zur Laufzeit oder aus anderen Name/Wert-Paaren im JSON-Format zurück. Siehe auch [triggerOutputs](#triggerOutputs) und [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Gibt die `body`-Ausgabe eines Triggers zur Laufzeit zurück. Siehe [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Gibt einen einzelnen Wert zurück, der mit einem Schlüsselnamen in Triggerausgaben vom Typ *form-data* oder *form-encoded* übereinstimmt. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Gibt einen bestimmten Textteil in der mehrteiligen Ausgabe eines Triggers zurück. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Erstellt ein Array, dessen Werte mit einem Schlüsselnamen in Triggerausgaben vom Typ *form-data* oder *form-encoded* übereinstimmen. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Gibt eine Triggerausgabe zur Laufzeit oder Werte aus anderen JSON-Name/Wert-Paaren zurück. Siehe [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Gibt den Wert für eine angegebene Variable zurück. |
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Gibt sämtliche Details zum Workflow selbst zur Laufzeit zurück. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI-Analysefunktionen

Sie können für die Arbeit mit URIs (Uniform Resource Identifiers) und das Abrufen verschiedener Eigenschaftswerte für diese URIs folgende URI-Analysefunktionen verwenden.
Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| URI-Analysefunktion | Aufgabe |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Gibt den Wert `host` für einen Uniform Resource Identifier (URI) zurück. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Gibt den Wert `path` für einen Uniform Resource Identifier (URI) zurück. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Gibt die den `path`- und den `query`-Wert für einen Uniform Resource Identifier (URI) zurück. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Gibt den Wert `port` für einen Uniform Resource Identifier (URI) zurück. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Gibt den Wert `query` für einen Uniform Resource Identifier (URI) zurück. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Gibt den Wert `scheme` für einen Uniform Resource Identifier (URI) zurück. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Manipulationsfunktionen: JSON & XML

Für die Arbeit mit JSON-Objekten und XML-Knoten können Sie folgende Bearbeitungsfunktionen verwenden.
Die vollständige Referenz zu den einzelnen Funktionen finden Sie unter [Funktionsreferenz zur Definitionssprache für Workflows in Azure Logic Apps](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Bearbeitungsfunktion | Aufgabe |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Fügt eine Eigenschaft und den zugehörigen Wert, oder ein Name/Wert-Paar zu einem JSON-Objekt hinzu und gibt das aktualisierte Objekt zurück. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Gibt den ersten Wert ungleich NULL aus mindestens einem Parameter zurück. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Entfernt eine Eigenschaft aus einem JSON-Objekt und gibt das aktualisierte Objekt zurück. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Legt den Wert für die Eigenschaft eines JSON-Objekts fest und gibt das aktualisierte Objekt zurück. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Überprüft die XML auf Knoten oder Werte, die mit einem XPath-Ausdruck (XML Path Language) übereinstimmen, und gibt die übereinstimmenden Knoten oder Werte zurück. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Alle Funktionen – alphabetische Liste

In diesem Abschnitt werden alle verfügbaren Funktionen in alphabetischer Reihenfolge aufgelistet.

<a name="action"></a>

### <a name="action"></a>action

Gibt die Ausgabe der *aktuellen* Aktion zur Laufzeit oder Werte aus anderen JSON-Name/Wert-Paaren zurück, die Sie einem Ausdruck zuweisen können.
Standardmäßig verweist diese Funktion auf das gesamte Aktionsobjekt, Sie können jedoch optional eine Eigenschaft angeben, deren Wert Sie abrufen möchten.
Informationen hierzu finden Sie auch unter [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

Sie können die `action()`-Funktion nur an folgenden Stellen verwenden:

* In der `unsubscribe`-Eigenschaft für eine Webhookaktion, sodass Sie auf das Ergebnis aus der ursprünglichen `subscribe`-Anforderung zugreifen können
* In der `trackedProperties`-Eigenschaft für eine Aktion
* In der `do-until`-Schleifenbedingung für eine Aktion

```
action()
action().outputs.body.<property>
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*property*> | Nein | String | Der Name der Aktionsobjekteigenschaft, deren Wert Sie abrufen möchten: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** und **clientTrackingId**. Im Azure-Portal finden Sie diese Eigenschaften, indem Sie die Details eines bestimmten Ausführungsverlaufs überprüfen. Weitere Informationen hierzu finden Sie unter [REST-API - Workflowausführungsaktionen](/rest/api/logic/workflowrunactions/get). |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*action-output*> | String | Die Ausgabe von der aktuellen Aktion oder die Eigenschaft |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Gibt die `body`-Ausgabe einer Aktion zur Laufzeit zurück.
Kurzform für `actions('<actionName>').outputs.body`.
Weitere Informationen finden Sie unter [body()](#body) und [actions()](#actions).

```
actionBody('<actionName>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Der Name der Aktion, deren `body`-Ausgabe Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | Die `body`-Ausgabe von der angegebenen Aktion |
||||

*Beispiel*

In diesem Beispiel wird die `body`-Ausgabe der Twitter-Aktion `Get user` abgerufen:

```
actionBody('Get_user')
```

Dies ist das zurückgegebene Ergebnis:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Gibt die Ausgabe einer Aktion zur Laufzeit zurück.  Kurzform für `actions('<actionName>').outputs`. Siehe [actions()](#actions). Die `actionOutputs()`-Funktion wird im Logic Apps-Designer in `outputs()` aufgelöst. Aus diesem Grund sollten Sie die Verwendung von [outputs()](#outputs) anstelle von `actionOutputs()` in Erwägung ziehen. Obwohl beide Funktionen in gleicher Weise funktionieren, wird `outputs()` bevorzugt.

```
actionOutputs('<actionName>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Der Name der Aktion, deren Ausgabe Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*output*> | String | Die Ausgabe von der angegebenen Aktion |
||||

*Beispiel*

In diesem Beispiel wird die Ausgabe der Twitter-Aktion `Get user` abgerufen:

```
actionOutputs('Get_user')
```

Dies ist das zurückgegebene Ergebnis:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>Aktionen

Gibt die Ausgabe einer Aktion zur Laufzeit oder Werte aus anderen JSON-Name/Wert-Paaren zurück, die Sie einem Ausdruck zuweisen können. Standardmäßig verweist die Funktion auf das gesamte Aktionsobjekt, Sie können aber optional eine Eigenschaft angeben, deren Wert Sie abrufen möchten.
Kurzformversionen finden Sie unter [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) und [body()](#body).
Informationen hinsichtlich der aktuellen Aktion finden Sie unter [action()](#action).

> [!TIP]
> Die Ausgabe der Funktion `actions()` wird als Zeichenfolge zurückgegeben. Wenn Sie als Rückgabewert ein JSON-Objekt benötigen, müssen Sie den Zeichenfolgenwert erst konvertieren. Der Zeichenfolgenwert kann mithilfe der [Aktion „Parse JSON“](logic-apps-perform-data-operations.md#parse-json-action) in ein JSON-Objekt transformiert werden.

> [!NOTE]
> Bisher konnten Sie die `actions()`-Funktion oder das `conditions`-Element verwenden, wenn Sie angeben haben, dass eine Aktion auf Basis der Ausgabe einer anderen Aktion ausgeführt wurde. Sie müssen nun aber, um Abhängigkeiten zwischen Aktionen explizit zu deklarieren, die `runAfter`-Eigenschaft der abhängigen Aktion verwenden.
> Weitere Informationen zur `runAfter`-Eigenschaft finden Sie unter [Abfangen und Behandeln von Fehlern mit der RunAfter-Eigenschaft](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Der Name des Aktionsobjekts, dessen Ausgabe Sie abrufen möchten  |
| <*property*> | Nein | String | Der Name der Aktionsobjekteigenschaft, deren Wert Sie abrufen möchten: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** und **clientTrackingId**. Im Azure-Portal finden Sie diese Eigenschaften, indem Sie die Details eines bestimmten Ausführungsverlaufs überprüfen. Weitere Informationen hierzu finden Sie unter [REST-API - Workflowausführungsaktionen](/rest/api/logic/workflowrunactions/get). |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*action-output*> | String | Die Ausgabe von der angegebenen Aktion oder Eigenschaft |
||||

*Beispiel*

In diesem Beispiel wird der Wert der `status`-Eigenschaft der Twitter-Aktion `Get user` zur Laufzeit abgerufen:

```
actions('Get_user').outputs.body.status
```

Dies ist das zurückgegebene Ergebnis: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Gibt das Ergebnis der Addition zweier Zahlen zurück.

```
add(<summand_1>, <summand_2>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*days*> | Ja | Integer | Die positive oder negative Anzahl von Tagen, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der Zeitstempel plus der angegebenen Anzahl von Tagen  |
||||

*Beispiel 1*

In diesem Beispiel werden 10 Tage zu dem angegebenen Zeitstempel addiert:

```
addDays('2018-03-15T00:00:00Z', 10)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-25T00:00:00.0000000Z"`

*Beispiel 2*

In diesem Beispiel werden 10 Tage vom angegebenen Zeitstempel subtrahiert:

```
addDays('2018-03-15T00:00:00Z', -5)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-10T00:00:00.0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Fügt eine Anzahl von Stunden zu einem Zeitstempel hinzu.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*hours*> | Ja | Integer | Die positive oder negative Anzahl von Stunden, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

Das Ergebnis „2018-03-15T10:00:00.0000000Z“ wird zurückgegeben.

*Beispiel 2*

In diesem Beispiel werden 10 Stunden vom angegebenen Zeitstempel subtrahiert:

```
addHours('2018-03-15T15:00:00Z', -5)
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-15T10:00:00.0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Fügt eine Anzahl von Minuten zu einem Zeitstempel hinzu.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*minutes*> | Ja | Integer | Die positive oder negative Anzahl von Minuten, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Fügt eine Eigenschaft und den zugehörigen Wert, oder ein Name/Wert-Paar zu einem JSON-Objekt hinzu und gibt das aktualisierte Objekt zurück. Wenn die Eigenschaft zur Laufzeit bereits vorhanden ist, tritt in der Funktion ein Fehler auf, und sie löst einen Fehler aus.

```
addProperty(<object>, '<property>', <value>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Das JSON-Objekt, zu dem Sie eine Eigenschaft hinzufügen möchten |
| <*property*> | Ja | String | Der Name der zu Eigenschaft, die hinzugefügt werden soll |
| <*value*> | Ja | Any | Der Wert für die Eigenschaft |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Das aktualisierte JSON-Objekt mit der angegebenen Eigenschaft |
||||

Verwenden Sie die folgende Syntax, um einer vorhandenen Eigenschaft eine untergeordnete Eigenschaft hinzuzufügen:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Das JSON-Objekt, zu dem Sie eine Eigenschaft hinzufügen möchten |
| <*parent-property*> | Ja | String | Der Name der übergeordneten Eigenschaft, der Sie die untergeordnete Eigenschaft hinzufügen möchten |
| <*child-property*> | Ja | String | Der Name für die untergeordnete Eigenschaft, die hinzugefügt werden soll |
| <*value*> | Ja | Any | Der Wert, auf den die angegebene Eigenschaft festgelegt werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Das aktualisierte JSON-Objekt, dessen Eigenschaft Sie festlegen |
||||

*Beispiel 1*

In diesem Beispiel wird die `middleName`-Eigenschaft zu einem JSON-Objekt hinzugefügt, das mit der [JSON()](#json)-Funktion aus einer Zeichenfolge in JSON konvertiert wird. Das Objekt enthält bereits die Eigenschaften `firstName` und `surName`. Die Funktion weist der neuen Eigenschaft den angegebenen Wert zu und gibt das aktualisierte Objekt zurück:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Das aktuelle JSON-Objekt sieht wie folgt aus:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Das aktualisierte JSON-Objekt sieht wie folgt aus:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Beispiel 2*

In diesem Beispiel wird die untergeordnete `middleName`-Eigenschaft zu der vorhandenen `customerName`-Eigenschaft in einem JSON-Objekt hinzugefügt, das mit der [JSON()](#json)-Funktion aus einer Zeichenfolge in JSON konvertiert wird. Die Funktion weist der neuen Eigenschaft den angegebenen Wert zu und gibt das aktualisierte Objekt zurück:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Das aktuelle JSON-Objekt sieht wie folgt aus:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Das aktualisierte JSON-Objekt sieht wie folgt aus:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Fügt eine Anzahl von Sekunden zu einem Zeitstempel hinzu.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*seconds*> | Ja | Integer | Die positive oder negative Anzahl von Sekunden, die hinzugefügt werden sollen |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die hinzugefügt werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

Überprüft, ob für sämtliche Ausdrücke der Wert „TRUE“ festgelegt ist.
Gibt „true“ zurück, wenn alle Ausdrücke gleich „true“ sind, oder gibt „false“ zurück, wenn mindestens ein Ausdruck gleich „false“ ist.

```
and(<expression1>, <expression2>, ...)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ja | Boolean | Die Ausdrücke, die überprüft werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn alle Ausdrücke gleich „true“ sind. Gibt „false“ zurück, wenn mindestens ein Ausdruck gleich „false“ ist. |
||||

*Beispiel 1*

In diesen Beispielen wird überprüft, ob alle angegebenen booleschen Werte gleich „true“ sind:

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

In diesen Beispielen wird überprüft, ob die angegebenen Ausdrücke gleich „true“ sind:

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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

> [!NOTE]
> Azure Logic Apps führt automatisch eine Base64-Codierung und -Decodierung durch, daher müssen Sie diese Konvertierungen nicht manuell ausführen. Führen Sie diese dennoch aus, tritt möglicherweise ein unerwartetes Anzeigeverhalten auf, das sich nicht auf die eigentlichen Konvertierungen auswirkt, sondern nur auf ihre Anzeige. Weitere Informationen finden Sie unter [Implizite Datentypkonvertierungen](#implicit-data-conversions).

```
base64('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

> [!NOTE]
> Azure Logic Apps führt automatisch eine Base64-Codierung und -Decodierung durch, daher müssen Sie diese Konvertierungen nicht manuell ausführen. Führen Sie diese dennoch aus, tritt möglicherweise ein unerwartetes Anzeigeverhalten auf, das sich nicht auf die eigentlichen Konvertierungen auswirkt, sondern nur auf ihre Anzeige. Weitere Informationen finden Sie unter [Implizite Datentypkonvertierungen](#implicit-data-conversions).

```
base64ToBinary('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Gibt die Zeichenfolgenversion einer base64-codierten Zeichenfolge zurück, d. h., die base64-Zeichenfolge wird decodiert. Verwenden Sie diese Funktion anstelle der veralteten Funktion [decodeBase64()](#decodeBase64).

> [!NOTE]
> Azure Logic Apps führt automatisch eine Base64-Codierung und -Decodierung durch, daher müssen Sie diese Konvertierungen nicht manuell ausführen. Führen Sie diese dennoch aus, tritt möglicherweise ein unerwartetes Anzeigeverhalten auf, das sich nicht auf die eigentlichen Konvertierungen auswirkt, sondern nur auf ihre Anzeige. Weitere Informationen finden Sie unter [Implizite Datentypkonvertierungen](#implicit-data-conversions).

```
base64ToString('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

<a name="body"></a>

### <a name="body"></a>body

Gibt die `body`-Ausgabe einer Aktion zur Laufzeit zurück.
Kurzform für `actions('<actionName>').outputs.body`.
Siehe [actionBody()](#actionBody) und [actions()](#actions).

```
body('<actionName>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Der Name der Aktion, deren `body`-Ausgabe Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | Die `body`-Ausgabe von der angegebenen Aktion |
||||

*Beispiel*

In diesem Beispiel wird die `body`-Ausgabe der Twitter-Aktion `Get user` abgerufen:

```
body('Get_user')
```

Dies ist das zurückgegebene Ergebnis:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Gibt die boolesche Version eines Werts zurück.

```
bool(<value>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Any | Der Wert, der in einen booleschen Wert konvertiert werden soll. |
|||||

Wenn Sie `bool()` mit einem Objekt verwenden, muss der Wert des Objekts eine Zeichenfolge oder eine ganze Zahl sein, die in einen booleschen Wert konvertiert werden kann.

| Rückgabewert | type | Beschreibung |
| ------------ | ---- | ----------- |
| `true` oder `false` | Boolean | Die boolesche Version des angegebenen Werts |
||||

*Ausgaben*

In diesen Beispielen werden die verschiedenen unterstützten Eingabetypen für `bool()` gezeigt:

| Eingabewert | type | Rückgabewert |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | Integer | `true` |
| `bool(0)` | Integer    | `false` |
| `bool(-1)` | Integer | `true` |
| `bool('true')` | String | `true` |
| `bool('false')` | String | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Gibt den ersten Wert ungleich NULL aus mindestens einem Parameter zurück.
Leere Zeichenfolgen, leere Arrays und leere Objekte sind nicht NULL.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*destinationTimeZone*> | Ja | String | Der Name für die Zielzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

Dies ist das zurückgegebene Ergebnis: `"2018-01-01T00:00:00.0000000"`

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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*sourceTimeZone*> | Ja | String | Der Name für die Quellzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*destinationTimeZone*> | Ja | String | Der Name für die Zielzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

Dies ist das zurückgegebene Ergebnis: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Konvertiert einen Zeitstempel von der Quellzeitzone in die UTC-Zeitzone (UTC = Universal Time, Coordinated).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*sourceTimeZone*> | Ja | String | Der Name für die Quellzeitzone. Informationen zu Zeitzonennamen finden Sie unter [Werte des Microsoft-Zeitzonenindex](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Möglicherweise müssen Sie aber alle Satzzeichen aus dem Zeitzonennamen entfernen. |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Dies ist das zurückgegebene Ergebnis: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Gibt den Tag des Jahres aus einem Zeitstempel zurück.

```
dayOfYear('<timestamp>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

### <a name="decodebase64-deprecated"></a>decodeBase64 (deprecated)

Diese Funktion ist veraltet. Verwenden Sie daher stattdessen [base64ToString()](#base64ToString).

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Gibt die binäre Version eines Daten-URIs (Uniform Resource Identifier) zurück. Sie sollten [dataUriToBinary()](#dataUriToBinary) anstelle von `decodeDataUri()` verwenden. Obwohl beide Funktionen in gleicher Weise funktionieren, wird `dataUriToBinary()` bevorzugt.

> [!NOTE]
> Azure Logic Apps führt automatisch eine Base64-Codierung und -Decodierung durch, daher müssen Sie diese Konvertierungen nicht manuell ausführen. Führen Sie diese dennoch aus, tritt möglicherweise ein unerwartetes Anzeigeverhalten auf, das sich nicht auf die eigentlichen Konvertierungen auswirkt, sondern nur auf ihre Anzeige. Weitere Informationen finden Sie unter [Implizite Datentypkonvertierungen](#implicit-data-conversions).

```
decodeDataUri('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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
decodeUriComponent('https%3A%2F%2Fcontoso.com')
```

Dies ist das zurückgegebene Ergebnis: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Gibt das Ergebnis der Division zweier Zahlen zurück. Informationen zum Abrufen des Restwerts finden Sie unter [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Ja | Integer oder Float | Die Zahl, die durch den *Divisor* dividiert werden soll. |
| <*divisor*> | Ja | Integer oder Float | Die Zahl, durch die der *Dividend* geteilt wird; darf nicht 0 sein |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer oder Float | Das Ergebnis aus der Division der ersten Zahl durch die zweite Zahl. Wenn der Dividend oder der Divisor den Typ Float aufweist, weist auch das Ergebnis den Typ Float auf. <p><p>**Hinweis**: Um das Float-Ergebnis in einen Integer zu konvertieren, können Sie [in Azure in Ihrer Logik-App eine Funktion erstellen und aufrufen](../logic-apps/logic-apps-azure-functions.md). |
||||

*Beispiel 1*

Beide Beispiele geben diesen Wert mit dem Typ Integer zurück: `2`

```
div(10,5)
div(11,5)
```

*Beispiel 2*

Beide Beispiele geben diesen Wert mit dem Typ Float zurück: `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Gibt eine URI-codierte (Uniform Resource Identifier) Version für eine Zeichenfolge zurück, indem URL-unsichere Zeichen durch Escapezeichen ersetzt werden. Sie sollten [uriComponent()](#uriComponent) anstelle von `encodeUriComponent()` verwenden. Obwohl beide Funktionen in gleicher Weise funktionieren, wird `uriComponent()` bevorzugt.

> [!NOTE]
> Azure Logic Apps führt automatisch eine Base64-Codierung und -Decodierung durch, daher müssen Sie diese Konvertierungen nicht manuell ausführen. Führen Sie diese dennoch aus, tritt möglicherweise ein unerwartetes Anzeigeverhalten auf, das sich nicht auf die eigentlichen Konvertierungen auswirkt, sondern nur auf ihre Anzeige. Weitere Informationen finden Sie unter [Implizite Datentypkonvertierungen](#implicit-data-conversions).

```
encodeUriComponent('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Dies ist das zurückgegebene Ergebnis: `"https%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Überprüft, ob eine Sammlung leer ist.
Gibt „true“ zurück, wenn die Sammlung leer ist, oder gibt „false“ zurück, wenn sie nicht leer ist.

```
empty('<collection>')
empty([<collection>])
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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
Diese Funktion können Sie nur verwenden, wenn Sie benutzerdefinierte Parameter an eine App (z. B. eine Logik-App oder einen Flow) übergeben.

```
float('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Gibt ein Array mit den Werten zurück, die mit einem Schlüsselnamen (key-Name) in der *form-data*- oder *form-encoded*-Ausgabe einer Aktion übereinstimmen.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Die Aktion, deren Ausgabe den Schlüsselwert (key-Wert) hat, den Sie abrufen möchten |
| <*key*> | Ja | String | Der Name des Schlüssels, dessen Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Ein Array mit allen Werten, die mit dem angegebenen Schlüssel übereinstimmen. |
||||

*Beispiel*

In diesem Beispiel wird ein Array aus dem Wert erstellt, den der „Subject“-Schlüssel in der form-data- oder form-encoded Ausgabe der angegebenen Aktion hat:

```
formDataMultiValues('Send_an_email', 'Subject')
```

Der Betrefftext (Subject) wird in einem Array zurückgegeben, zum Beispiel: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Gibt einen einzelnen Wert zurück, der mit einem Schlüsselnamen (key-Name) in der *form-data*- oder *form-encoded*-Ausgabe einer Aktion übereinstimmt.
Findet die Funktion mehrere Übereinstimmungen, löst sie einen Fehler aus.

```
formDataValue('<actionName>', '<key>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Die Aktion, deren Ausgabe den Schlüsselwert (key-Wert) hat, den Sie abrufen möchten |
| <*key*> | Ja | String | Der Name des Schlüssels, dessen Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Der Wert in dem angegebenen Schlüssel  |
||||

*Beispiel*

In diesem Beispiel wird eine Zeichenfolge aus dem Wert des „Subject“-Schlüssels in der form-data- oder form-encoded Ausgabe der angegebenen Aktion erstellt:

```
formDataValue('Send_an_email', 'Subject')
```

Der Betrefftext (Subject) wird als Zeichenfolge zurückgegeben, zum Beispiel: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Gibt eine Zahl als Zeichenfolge zurück, die auf dem angegebenen Format basiert.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*number*> | Ja | Integer oder Double | Der Wert, den Sie formatieren möchten. |
| <*format*> | Ja | String | Eine kombinierten Formatzeichenfolge, die das Format angibt, das Sie verwenden möchten. Informationen zu den unterstützten Formatzeichenfolgen für Zahlen finden Sie unter [Standardformatzeichenfolgen für Zahlen](/dotnet/standard/base-types/standard-numeric-format-strings), die von `number.ToString(<format>, <locale>)` unterstützt werden. |
| <*locale*> | Nein | String | Das zu verwendende Gebietsschema gemäß der Unterstützung durch `number.ToString(<format>, <locale>)`. Wenn Sie hier nichts angeben, lautet der Standardwert `en-us`. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*formatted-number*> | String | Die angegebene Zahl als Zeichenfolge in dem von Ihnen angegebenen Format. Sie können diesen Rückgabewert in einen `int`- oder `float`-Wert umwandeln. |
||||

*Beispiel 1*

Angenommen, Sie möchten die Zahl `1234567890` formatieren. In diesem Beispiel wird diese Zahl als Zeichenfolge „1,234,567,890.00“ formatiert.

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*Beispiel 2:

Angenommen, Sie möchten die Zahl `1234567890` formatieren. In diesem Beispiel wird diese Zahl als Zeichenfolge „1.234.567.890,00“ formatiert.

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Beispiel 3*

Angenommen, Sie möchten die Zahl `17.35` formatieren. In diesem Beispiel wird diese Zahl als Zeichenfolge „$17.35“ formatiert.

```
formatNumber(17.35, 'C2')
```

*Beispiel 4*

Angenommen, Sie möchten die Zahl `17.35` formatieren. In diesem Beispiel wird diese Zahl als Zeichenfolge „17,35 kr“ formatiert.

```
formatNumber(17.35, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Gibt den aktuellen Zeitstempel plus der angegebenen Zeiteinheiten zurück.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die hinzugefügt werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die subtrahiert werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*format*> | Nein | String | Ein einzelner [Formatbezeichner](/dotnet/api/system.guid.tostring#system_guid_tostring_system_string_) für den zurückgegebenen GUID. Das Standardformat ist „D“, Sie können  aber „N“, „D“, „B“, „P“ oder „X“ verwenden. |
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

Überprüft, ob ein Ausdruck gleich „true“ oder „false“ ist. Gibt abhängig vom Ergebnis einen angegebenen Wert zurück. Parameter werden von links nach rechts ausgewertet.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

<a name="item"></a>

### <a name="item"></a>item

Wird diese Funktion in einer wiederholten Aktion für ein Array verwendet, gibt sie das Element zurück, das in der aktuellen Iteration der Aktion das aktuelle Arrayelement ist.
Sie können die Werte auch aus den Eigenschaften dieses Elements abrufen.

```
item()
```

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Any | Das aktuelle Element im Array für die aktuelle Iteration der Aktion |
||||

*Beispiel*

In diesem Beispiel wird das `body`-Element aus der aktuellen Nachricht für die „Send_an_email“-Aktion in der aktuellen Iteration einer for-each-Schleife abgerufen:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Gibt das aktuelle Element aus jedem Zyklus einer for-each-Schleife zurück.
Verwenden Sie diese Funktion in einer for-each-Schleife.

```
items('<loopName>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Ja | String | Der Name der for-each-Schleife |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*item*> | Any | Das Element aus dem aktuellen Zyklus in der angegebenen for-each-Schleife |
||||

*Beispiel*

In diesem Beispiel wird das aktuelle Element aus der angegebenen for-each-Schleife abgerufen:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Gibt den Indexwert für die aktuelle Iteration in einer „Until“-Schleife zurück. Sie können diese Funktion in geschachtelten „Until“-Schleifen verwenden. 

```
iterationIndexes('<loopName>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Ja | String | Name der „Until“-Schleife | 
||||| 

| Rückgabewert | type | BESCHREIBUNG | 
| ------------ | ---- | ----------- | 
| <*index*> | Integer | Indexwert für die aktuelle Iteration in der angegebenen „Until“-Schleife | 
|||| 

*Beispiel* 

In diesem Beispiel wird eine Zählervariable erstellt, die dann bei jeder Iteration einer „Until“-Schleife um den Wert 1 inkrementiert wird, bis der Zählerwert 5 erreicht ist. Außerdem wird im Beispiel eine Variable erstellt, mit der der aktuelle Index für jede Iteration nachverfolgt wird. In der „Until“-Schleife wird der Zähler bei jeder Iteration inkrementiert, und dann wird der Zählerwert dem aktuellen Indexwert zugewiesen. Anschließend wird der Zähler inkrementiert. Innerhalb der Schliefe verweist dieses Beispiel mithilfe der Funktion `iterationIndexes` auf den aktuellen Iterationsindex:

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Gibt den Typwert, das Objekt oder ein Array aus Objekten im JSON-Format (JavaScript Object Notation) für eine Zeichenfolge oder XML-Daten zurück.

```
json('<value>')
json(xml('value'))
```

> [!IMPORTANT]
> Ohne XML-Schema, das die Struktur der Ausgabe definiert, gibt die Funktion möglicherweise Ergebnisse zurück, bei denen die Struktur je nach Eingabe stark vom erwarteten Format abweicht.
>  
> Durch dieses Verhalten ist diese Funktion nicht für Szenarien geeignet, in denen die Ausgabe einem klar definierten Vertrag entsprechen muss, beispielsweise in kritischen Geschäftssystemen oder -lösungen.

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Zeichenfolge oder XML | Die Zeichenfolge oder das XML-Objekt, die oder das konvertiert werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Nativer JSON-Typ, natives JSON-Objekt oder natives JSON-Array | Der native JSON-Typwert, das native JSON-Objekt oder ein natives JSON-Objektarray aus der Eingabezeichenfolge oder den XML-Daten. <p><p>– Wenn Sie XML-Daten mit einem einzelnen untergeordneten Element im Stammelement übergeben, gibt die Funktion ein einzelnes JSON-Objekt für dieses untergeordnete Element zurück. <p> – Wenn Sie XML-Daten mit mehreren untergeordneten Elementen im Stammelement übergeben, gibt die Funktion ein Array zurück, das JSON-Objekte für diese untergeordneten Elemente enthält. <p>– Wenn die Zeichenfolge NULL ist, gibt die Funktion ein leeres Objekt zurück. |
||||

*Beispiel 1*

Dieses Beispiel konvertiert diese Zeichenfolge in einen JSON-Wert:

```
json('[1, 2, 3]')
```

Dies ist das zurückgegebene Ergebnis: `[1, 2, 3]`

*Beispiel 2*

Dieses Beispiel konvertiert diese Zeichenfolge in JSON:

```
json('{"fullName": "Sophia Owen"}')
```

Dies ist das zurückgegebene Ergebnis:

```json
{
  "fullName": "Sophia Owen"
}
```

*Beispiel 3*

Dieses Beispiel verwendet die Funktionen `json()` und `xml()`, um XML-Daten mit einem einzelnen untergeordneten Element im Stammelement in ein JSON-Objekt namens `person` für dieses untergeordnete Element zu konvertieren:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))`

Dies ist das zurückgegebene Ergebnis:

```json
{
   "?xml": { 
      "@version": "1.0" 
   },
   "root": {
      "person": {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      }
   }
}
```

*Beispiel 4*

Dieses Beispiel verwendet die Funktionen `json()` und `xml()`, um XML-Daten mit mehreren untergeordneten Elementen im Stammelement in ein Array namens `person` zu konvertieren, das JSON-Objekte für diese untergeordneten Elemente enthält:

`json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> <person id='2'> <name>John Doe</name> <occupation>Engineer</occupation> </person> </root>'))`

Dies ist das zurückgegebene Ergebnis:

```json
{
   "?xml": {
      "@version": "1.0"
   },
   "root": {
      "person": [
         {
            "@id": "1",
            "name": "Sophia Owen",
            "occupation": "Engineer"
         },
         {
            "@id": "2",
            "name": "John Doe",
            "occupation": "Engineer"
         }
      ]
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Gibt die Anfangsposition oder den Indexwert des letzten Vorkommens einer Teilzeichenfolge zurück. Für diese Funktion wird die Groß-/Kleinschreibung nicht beachtet, und Indizes beginnen mit der Zahl 0.

```json
lastIndexOf('<text>', '<searchText>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, die die Teilzeichenfolge enthält, nach der gesucht werden soll |
| <*searchText*> | Ja | String | Die Teilzeichenfolge, nach der gesucht werden soll |
|||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | Die Anfangsposition oder der Indexwert des letzten Vorkommens der angegebenen Teilzeichenfolge. |
|||

Wenn der Zeichenfolgen- oder Teilzeichenfolgenwert leer ist, tritt das folgende Verhalten auf:

* Wenn nur der Zeichenfolgenwert leer ist, gibt die Funktion `-1` zurück.

* Wenn Zeichenfolgen- und Teilzeichenfolgenwerte leer sind, wird `0` zurückgegeben.

* Wenn nur der Zeichenfolgenwert leer ist, gibt die Funktion die Zeichenfolgenlänge minus 1 zurück.

*Beispiele*

In diesem Beispiel wird für die Teilzeichenfolge `world` nach dem Anfangsindexwert des letzten Vorkommens gesucht, den sie in der Zeichenfolge `hello world hello world` hat. Als Ergebnis wird `18` zurückgegeben:

```json
lastIndexOf('hello world hello world', 'world')
```

In diesem Beispiel fehlt der Teilzeichenfolgenparameter, und der Wert `22` wird zurückgegeben, da der Wert der Eingabezeichenfolge (`23`) minus 1 größer als 0 (null) ist.

```json
lastIndexOf('hello world hello world', '')
```

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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Gibt die „Rückruf-URL“ zurück, die einen Trigger oder eine Aktion aufruft.
Diese Funktion funktioniert nur mit Triggern und Aktionen für die Connectortypen **HttpWebhook** und **ApiConnectionWebhook**, nicht für die Typen **Manual**, **Recurrence**, **HTTP** und **APIConnection**.

```
listCallbackUrl()
```

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | Die Rückruf-URL für einen Trigger oder eine Aktion |
||||

*Beispiel*

Dieses Beispiel zeigt einer Beispielrückruf-URL, die von dieser Funktion zurückgegeben werden könnte:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Gibt den größten Wert aus einer Liste oder einem Array mit Zahlen zurück, mit dem Wert an beiden Enden inklusive.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Gibt den Textteil für einen bestimmten Teil einer Aktionsausgabe zurück, die mehrere Teile hat.

```
multipartBody('<actionName>', <index>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Der Name der Aktion, für die es Ausgabe mit mehreren Teilen gibt |
| <*index*> | Ja | Integer | Der Indexwert des von Ihnen gewünschten Teils |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*body*> | String | Der Textteil für den angegebenen Teil |
||||

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
Gibt „true“ zurück, wenn mindestens ein Ausdruck gleich „true“ ist, oder gibt „false“ zurück, wenn alle Ausdrücke gleich „false“ ist.

```
or(<expression1>, <expression2>, ...)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Ja | Boolean | Die Ausdrücke, die überprüft werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| true oder false | Boolean | Gibt „true“ zurück, wenn mindestens ein Ausdruck gleich „true“ ist. Gibt „false“ zurück, wenn alle Ausdrücke gleich „false“ sind. |
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

<a name="outputs"></a>

### <a name="outputs"></a>outputs

Gibt die Ausgabe einer Aktion zur Laufzeit zurück. Verwenden Sie diese Funktion anstelle von `actionOutputs()`, die im Logic Apps-Designer zu `outputs()` aufgelöst wird. Obwohl beide Funktionen in gleicher Weise funktionieren, wird `outputs()` bevorzugt.

```
outputs('<actionName>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Ja | String | Der Name der Aktion, deren Ausgabe Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | -----| ----------- |
| <*output*> | String | Die Ausgabe von der angegebenen Aktion |
||||

*Beispiel*

In diesem Beispiel wird die Ausgabe der Twitter-Aktion `Get user` abgerufen:

```
outputs('Get_user')
```

Dies ist das zurückgegebene Ergebnis:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Gibt den Wert für einen Parameter zurück, der in Ihrer Workflowdefinition beschrieben wird.

```
parameters('<parameterName>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Ja | String | Der Name des Parameters, dessen Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Any | Der Wert des angegebenen Parameters |
||||

*Beispiel*

Angenommen, Sie haben diesen JSON-Wert:

```json
{
  "fullName": "Sophia Owen"
}
```

In diesem Beispiel wird der Wert des angegebenen Parameters abgerufen:

```
parameters('fullName')
```

Dies ist das zurückgegebene Ergebnis: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Gibt eine zufällige Ganzzahl  aus einem angegebenen Bereich zurück, wobei nur der Anfangswert inklusive ist.

```
rand(<minValue>, <maxValue>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Entfernt eine Eigenschaft aus einem Objekt und gibt das aktualisierte Objekt zurück. Ist die Eigenschaft, die Sie entfernen möchten, nicht vorhanden, gibt die Funktion das ursprüngliche Objekt zurück.

```
removeProperty(<object>, '<property>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Das JSON-Objekt, aus dem Sie eine Eigenschaft entfernen möchten |
| <*property*> | Ja | String | Der Name der zu Eigenschaft, die entfernt werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Das aktualisierte JSON-Objekt ohne die angegebene Eigenschaft |
||||

Verwenden Sie die folgende Syntax, um eine untergeordnete Eigenschaft aus einer vorhandenen Eigenschaft zu entfernen:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Das JSON-Objekt, dessen Eigenschaft Sie entfernen möchten |
| <*parent-property*> | Ja | String | Der Name der übergeordneten Eigenschaft mit der untergeordneten Eigenschaft, die Sie entfernen möchten |
| <*child-property*> | Ja | String | Der Name der untergeordneten Eigenschaft, die entfernt werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Das aktualisierte JSON-Objekt, dessen untergeordnete Eigenschaft entfernt wurde |
||||

*Beispiel 1*

In diesem Beispiel wird die `middleName`-Eigenschaft aus einem JSON-Objekt entfernt, das mit der [JSON()](#json)-Funktion aus einer Zeichenfolge in JSON konvertiert wird, und es wird das aktualisierte Objekt zurückgegeben:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Das aktuelle JSON-Objekt sieht wie folgt aus:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Das aktualisierte JSON-Objekt sieht wie folgt aus:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Beispiel 2*

In diesem Beispiel wird die untergeordnete `middleName`-Eigenschaft aus der übergeordneten `customerName`-Eigenschaft in einem JSON-Objekt entfernt, das mit der [JSON()](#json)-Funktion aus einer Zeichenfolge in JSON konvertiert wird, und es wird das aktualisierte Objekt zurückgegeben:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Das aktuelle JSON-Objekt sieht wie folgt aus:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Das aktualisierte JSON-Objekt sieht wie folgt aus:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Gibt die Eingaben und Ausgaben aller Aktionen zurück, die in der angegebenen bereichsbezogenen Aktion enthalten sind, z. B. eine `For_each`-, `Until`- oder `Scope`-Aktion. Diese Funktion ist nützlich, um die Ergebnisse aus einer fehlgeschlagenen Aktion zurückzugeben, damit Sie Ausnahmen diagnostizieren und verarbeiten können. Weitere Informationen finden Sie unter [Abrufen von Kontext und Ergebnissen für Fehler](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Ja | String | Der Name der bereichsbezogenen Aktion, aus der die Eingaben und Ausgaben aller inneren Aktionen zurückgegeben werden sollen |
||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*array-object*> | Arrayobjekt | Ein Array, das Arrays mit Eingaben und Ausgaben aus jeder Aktion enthält, die in der angegebenen bereichsbezogenen Aktion enthalten ist |
||||

*Beispiel*

In diesem Beispiel wird die `result()`-Funktion in der `Compose`-Aktion verwendet, um die Eingaben und Ausgaben jeder Iteration einer HTTP-Aktion zurückgegeben, die sich in einer `For_each`-Schleife befindet:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Das im Beispiel zurückgegebene Array könnte wie folgt aussehen, wobei das äußere `outputs`-Objekt die Eingaben und Ausgaben von jeder Iteration der Aktionen enthält, die in der `For_each`-Aktion enthalten sind.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Legt den Wert für eine Eigenschaft eines JSON-Objekts fest und gibt das aktualisierte Objekt zurück. Wenn die Eigenschaft, die Sie festlegen möchten, nicht vorhanden ist, wird die Eigenschaft dem Objekt hinzugefügt. Verwenden Sie die [addProperty()](#addProperty)-Funktion, um eine neue Eigenschaft hinzuzufügen.

```
setProperty(<object>, '<property>', <value>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Das JSON-Objekt, dessen Eigenschaft Sie festlegen möchten |
| <*property*> | Ja | String | Der Name der vorhandenen oder neuen Eigenschaft, die festgelegt werden soll |
| <*value*> | Ja | Any | Der Wert, auf den die angegebene Eigenschaft festgelegt werden soll |
|||||

Wenn Sie die untergeordnete Eigenschaft in einem untergeordneten Objekt festlegen möchten, verwenden Sie stattdessen einen geschachtelten `setProperty()`-Aufruf. Andernfalls gibt die Funktion nur das untergeordnete Objekt als Ausgabe zurück.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*object*> | Ja | Object | Das JSON-Objekt, dessen Eigenschaft Sie festlegen möchten |
| <*parent-property*> | Ja | String | Der Name der übergeordneten Eigenschaft mit der untergeordneten Eigenschaft, die Sie festlegen möchten |
| <*child-property*> | Ja | String | Der Name für die untergeordnete Eigenschaft, die festgelegt werden soll |
| <*value*> | Ja | Any | Der Wert, auf den die angegebene Eigenschaft festgelegt werden soll |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | Das aktualisierte JSON-Objekt, dessen Eigenschaft Sie festlegen |
||||

*Beispiel 1*

In diesem Beispiel wird die `surName`-Eigenschaft in einem JSON-Objekt festgelegt, das mit der [JSON()](#json)-Funktion aus einer Zeichenfolge in JSON konvertiert wird. Die Funktion weist der Eigenschaft den angegebenen Wert zu und gibt das aktualisierte Objekt zurück:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Das aktuelle JSON-Objekt sieht wie folgt aus:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Das aktualisierte JSON-Objekt sieht wie folgt aus:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Beispiel 2*

In diesem Beispiel wird die untergeordnete `surName`-Eigenschaft der übergeordneten `customerName`-Eigenschaft in einem JSON-Objekt festgelegt, das mit der [JSON()](#json)-Funktion aus einer Zeichenfolge in JSON konvertiert wird. Die Funktion weist der Eigenschaft den angegebenen Wert zu und gibt das aktualisierte Objekt zurück:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Das aktuelle JSON-Objekt sieht wie folgt aus:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Das aktualisierte JSON-Objekt sieht wie folgt aus:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

Entfernt Elemente vom Anfang einer Sammlung und gibt *alle anderen* Elemente zurück.

```
skip([<collection>], <count>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | Der angegebene Zeitstempel, der jedoch am ersten Tag des Monats um null Uhr beginnt |
||||

*Beispiel 1*

In diesem Beispiel wird der Beginn des Monats für diesen Zeitstempel zurückgegeben:

```
startOfMonth('2018-03-15T13:30:30Z')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-01T00:00:00.0000000Z"`

*Beispiel 2*

In diesem Beispiel wird der Beginn des Monats für diesen Zeitstempel im angegebenen Format zurückgegeben:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

Dies ist das zurückgegebene Ergebnis: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Überprüft, ob eine Zeichenfolge mit einer bestimmten Teilzeichenfolge beginnt.
Gibt „true“ zurück, wenn die Teilzeichenfolge gefunden wurde, gibt andernfalls „false“ zurück.
Für diese Funktion wird die Groß-/Kleinschreibung nicht beachtet.

```
startsWith('<text>', '<searchText>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*value*> | Ja | Any | Der zu konvertierende Wert. Wenn dieser Wert NULL ist oder NULL ergibt, wird der Wert in einen leeren Zeichenfolgenwert (`""`) konvertiert. <p><p>Wenn Sie z. B. einer nicht vorhandenen Eigenschaft, auf die Sie mit dem `?`-Operator zugreifen können, eine Zeichenfolgenvariable zuweisen, wird der NULL-Wert in eine leere Zeichenfolge konvertiert. Das Vergleichen eines NULL-Werts ist jedoch nicht mit dem Vergleich einer leeren Zeichenfolge identisch. |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*string-value*> | String | Die Zeichenfolgenversion des angegebenen Werts. Wenn der *value*-Parameter NULL ist oder NULL ergibt, wird dieser Wert als leerer Zeichenfolgenwert (`""`) zurückgegeben. |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Gibt Zeichen aus einer Zeichenfolge zurück, beginnend mit dem angegebenen Indexwert (Position). Indexwerte beginnen bei 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*text*> | Ja | String | Die Zeichenfolge, aus der die Zeichen zurückgegeben werden sollen |
| <*startIndex*> | Ja | Integer | Eine positive Zahl gleich oder größer als 0, die Sie als Ausgangswert oder Indexwert verwenden können. |
| <*length*> | Nein | Integer | Eine positive Anzahl von Zeichen, die in der Teilzeichenfolge enthalten sein sollen |
|||||

> [!NOTE]
> Stellen Sie sicher, dass die Summe aus der Addition der Parameterwerte von *startIndex* und *length* kleiner als die Länge der Zeichenfolge ist, die Sie für den Parameter *text* angeben.
> Andernfalls erhalten Sie einen Fehler, im Gegensatz zu ähnlichen Funktionen in anderen Sprachen, bei denen das Ergebnis die Teilzeichenfolge ab dem *startIndex* bis zum Ende der Zeichenfolge ist. Der *length*-Parameter ist optional, und wenn er nicht angegeben wird, nimmt die **substring()** -Funktion alle Zeichen ab *startIndex* bis zum Ende der Zeichenfolge entgegen.

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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Ja | String | Die Zeichenfolge, die den Zeitstempel enthält |
| <*interval*> | Ja | Integer | Die Anzahl der angegebenen Zeiteinheiten, die subtrahiert werden sollen |
| <*timeUnit*> | Ja | String | Die mit *interval* zu verwendende Zeiteinheit: Second, Minute, Hour, Day, Week, Month, Year |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

Dies ist das zurückgegebene Ergebnis: `"2018-01-01T00:00:00.0000000Z"`

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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Gibt die Anzahl der Ticks zurück, bei denen es sich um 100-Nanosekunden-Intervalle handelt, seit dem 1. Januar 0001 12:00:00 Mitternacht (oder DateTime.Ticks in C# ) bis zum angegebenen Zeitstempel. Weitere Informationen finden Sie in diesem Thema: [DateTime.Ticks-Eigenschaft (System)](/dotnet/api/system.datetime.ticks).

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

<a name="trigger"></a>

### <a name="trigger"></a>Trigger (trigger)

Gibt die Ausgabe eines Triggers zur Laufzeit oder Werte aus anderen JSON-Name/Wert-Paaren zurück, die Sie einem Ausdruck zuweisen können.

* Wird diese Funktion in Eingaben eines Triggers verwendet, gibt sie die Ausgabe der vorherigen Ausführung zurück.

* Wird diese Funktion in der Bedingung eines Triggers verwendet, gibt sie die Ausgabe der aktuellen Ausführung zurück.

Standardmäßig verweist die Funktion auf das gesamte Triggerobjekt, Sie können aber optional eine Eigenschaft angeben, deren Wert Sie abrufen möchten.
Außerdem gibt es für diese Funktion Kurzformversionen. Informationen hierzu finden Sie unter [triggerOutputs()](#triggerOutputs) und [triggerBody()](#triggerBody).

```
trigger()
```

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | Die Ausgabe von einem Trigger zur Laufzeit |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Gibt die `body`-Ausgabe eines Triggers zur Laufzeit zurück.
Kurzform für `trigger().outputs.body`.
Siehe [trigger()](#trigger).

```
triggerBody()
```

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | String | Die `body`-Ausgabe des Triggers |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Gibt ein Array mit den Werten zurück, die mit einem Schlüsselnamen (key-Name) in der *form-data*- oder *form-encoded*-Ausgabe eines Triggers übereinstimmen.

```
triggerFormDataMultiValues('<key>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*key*> | Ja | String | Der Name des Schlüssels, dessen Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Ein Array mit allen Werten, die mit dem angegebenen Schlüssel übereinstimmen. |
||||

*Beispiel*

In diesem Beispiel wird ein Array aus dem Wert erstellt, den der „feedUrl“-Schlüssel in der form-data- oder form-encoded Ausgabe eines RSS-Triggers hat:

```
triggerFormDataMultiValues('feedUrl')
```

Dieses Array wird als Beispielergebnis zurückgegeben: `["https://feeds.a.dj.com/rss/RSSMarketsMain.xml"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Gibt eine Zeichenfolge mit einem einzelnen Wert zurück, der mit einem Schlüsselnamen (key-Name) in der *form-data*- oder *form-encoded*-Ausgabe eines Triggers übereinstimmt.
Findet die Funktion mehrere Übereinstimmungen, löst sie einen Fehler aus.

```
triggerFormDataValue('<key>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*key*> | Ja | String | Der Name des Schlüssels, dessen Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*key-value*> | String | Der Wert in dem angegebenen Schlüssel |
||||

*Beispiel*

In diesem Beispiel wird eine Zeichenfolge aus dem Wert erstellt, den der „feedUrl“-Schlüssel in der form-data- oder form-encoded Ausgabe eines RSS-Triggers hat:

```
triggerFormDataValue('feedUrl')
```

Diese Zeichenfolge wird als Beispielergebnis zurückgegeben: `"https://feeds.a.dj.com/rss/RSSMarketsMain.xml"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Gibt den Textteil für einen bestimmten Teil einer Triggerausgabe zurück, die mehrere Teile hat.

```
triggerMultipartBody(<index>)
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*index*> | Ja | Integer | Der Indexwert des von Ihnen gewünschten Teils |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*body*> | String | Der Textteil für den angegebenen Teil in der mehrteiligen Ausgabe eines Triggers |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Gibt eine Triggerausgabe zur Laufzeit oder Werte aus anderen JSON-Name/Wert-Paaren zurück.
Kurzform für `trigger().outputs`.
Siehe [trigger()](#trigger).

```
triggerOutputs()
```

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | Die Ausgabe von einem Trigger zur Laufzeit  |
||||

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

Dies ist das zurückgegebene Ergebnis: `"https%3A%2F%2Fcontoso.com"`

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
uriComponentToBinary('https%3A%2F%2Fcontoso.com')
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
uriComponentToString('https%3A%2F%2Fcontoso.com')
```

Dies ist das zurückgegebene Ergebnis: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Gibt den Wert `host` für einen Uniform Resource Identifier (URI) zurück.

```
uriHost('<uri>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | Der URI, dessen `host`-Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*host-value*> | String | Der `host`-Wert des angegebenen URIs |
||||

*Beispiel*

In diesem Beispiel wird der `host`-Wert dieses URIs abgerufen:

```
uriHost('https://www.localhost.com:8080')
```

Dies ist das zurückgegebene Ergebnis: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Gibt den Wert `path` für einen Uniform Resource Identifier (URI) zurück.

```
uriPath('<uri>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | Der URI, dessen `path`-Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*path-value*> | String | Der `path`-Wert des angegebenen URIs. Hat `path` keinen Wert, wird das Zeichen „/“ zurückgegeben. |
||||

*Beispiel*

In diesem Beispiel wird der `path`-Wert dieses URIs abgerufen:

```
uriPath('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Dies ist das zurückgegebene Ergebnis: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Gibt die den `path`- und den `query`-Wert für einen Uniform Resource Identifier (URI) zurück.

```
uriPathAndQuery('<uri>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | Der URI, dessen `path`- und `query`-Wert abgerufen werden sollen |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*path-query-value*> | String | Der `path`- und der `query`-Wert des angegebenen URIs. Gibt `path` keinen Wert an, wird das Zeichen „/“ zurückgegeben. |
||||

*Beispiel*

In diesem Beispiel werden der `path`- und der `query`-Wert dieses URIs abgerufen:

```
uriPathAndQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Dies ist das zurückgegebene Ergebnis: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Gibt den Wert `port` für einen Uniform Resource Identifier (URI) zurück.

```
uriPort('<uri>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | Der URI, dessen `port`-Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*port-value*> | Integer | Der `port`-Wert des angegebenen URIs. Gibt `port` keinen Wert an, wird der Standardport für das Protokoll zurückgegeben. |
||||

*Beispiel*

In diesem Beispiel wird der `port`-Wert dieses URIs zurückgegeben:

```
uriPort('https://www.localhost:8080')
```

Dies ist das zurückgegebene Ergebnis: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Gibt den Wert `query` für einen Uniform Resource Identifier (URI) zurück.

```
uriQuery('<uri>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | Der URI, dessen `query`-Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*query-value*> | String | Der `query`-Wert des angegebenen URIs |
||||

*Beispiel*

In diesem Beispiel wird der `query`-Wert dieses URIs zurückgegeben:

```
uriQuery('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Dies ist das zurückgegebene Ergebnis: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Gibt den Wert `scheme` für einen Uniform Resource Identifier (URI) zurück.

```
uriScheme('<uri>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Ja | String | Der URI, dessen `scheme`-Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | Der `scheme`-Wert des angegebenen URIs |
||||

*Beispiel*

In diesem Beispiel wird der `scheme`-Wert dieses URIs zurückgegeben:

```
uriScheme('https://www.contoso.com/catalog/shownew.htm?date=today')
```

Dies ist das zurückgegebene Ergebnis: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Gibt den aktuellen Zeitstempel zurück.

```
utcNow('<format>')
```

Optional können Sie mit dem <*format*>-Parameter ein anderes Format angeben.


| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*format*> | Nein | String | Entweder ein [einzelner Formatbezeichner](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder ein [benutzerdefiniertes Formatmuster](/dotnet/standard/base-types/custom-date-and-time-format-strings). Das Standardformat für den Zeitstempel ist [„o“](/dotnet/standard/base-types/standard-date-and-time-format-strings) (JJJJ-MM-TTT hh:mm:ss.fffffffK), das mit [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) übereinstimmt und in dem Zeitzoneninformationen erhalten bleiben. |
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

<a name="variables"></a>

### <a name="variables"></a>variables

Gibt den Wert für eine angegebene Variable zurück.

```
variables('<variableName>')
```

| Parameter | Erforderlich | type | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Ja | String | Der Name der Variablen, deren Wert Sie abrufen möchten |
|||||

| Rückgabewert | type | BESCHREIBUNG |
| ------------ | ---- | ----------- |
| <*variable-value*> | Any | Der Wert der angegebenen Variablen |
||||

*Beispiel*

Angenommen, die Variable „numItems“ hat aktuell den Wert 20.
In diesem Beispiel wird der ganzzahlige Wert dieser Variablen abgerufen:

```
variables('numItems')
```

Dies ist das zurückgegebene Ergebnis: `20`

<a name="workflow"></a>

### <a name="workflow"></a>workflow

Gibt sämtliche Details zum Workflow selbst zur Laufzeit zurück.

```
workflow().<property>
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
| --------- | -------- | ---- | ----------- |
| <*property*> | Nein | String | Der Name der Workfloweigenschaft, deren Wert Sie abrufen möchten <p>Ein Workflowobjekt hat diese Eigenschaften: **name**, **type**, **id**, **location** und **run**. Der Wert der **run**-Eigenschaft ist ebenfalls ein Objekt, das diese Eigenschaften hat: **name**, **type** und **id**. |
|||||

*Beispiel*

In diesem Beispiel wird der Name der aktuellen Ausführung eines Workflows zurückgegeben:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>Xml

Gibt die XML-Version einer Zeichenfolge zurück, die ein JSON-Objekt enthält.

```
xml('<value>')
```

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
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

Angenommen, es liegt diese XML-Zeichenfolge `'items'` vor: 

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In diesem Beispiel wird der XPath-Ausdruck `'/produce/item/name'` übergeben, um die mit dem Knoten `<name></name>` in der XML-Zeichenfolge `'items'` übereinstimmenden Knoten zu suchen, und es wird ein Array mit diesen Knotenwerten zurückgegeben:

`xpath(xml(parameters('items')), '/produce/item/name')`

Im Beispiel wird außerdem die [parameters()](#parameters)-Funktion verwendet, um die XML-Zeichenfolge aus `'items'` abzurufen, und die Zeichenfolge wird mit der [xml()](#xml)-Funktion in das XML-Format konvertiert.

Dies ist das Ergebnisarray mit den Knoten, die mit `<name></name` übereinstimmen:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Beispiel 2*

In diesem Beispiel, das an Beispiel 1 anschließt, wird der XPath-Ausdruck `'/produce/item/name[1]'`übergeben, um das erste Element `name` zu finden, das dem Element `item` untergeordnet ist.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Hier ist das Ergebnis: `Gala`

*Beispiel 3*

In diesem Beispiel, das an Beispiel 1 anschließt, wird der XPath-Ausdruck `'/produce/item/name[last()]'`übergeben, um das letzte Element `name` zu finden, das dem Element `item` untergeordnet ist.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Hier ist das Ergebnis: `Honeycrisp`

*Beispiel 4*

In diesem Beispiel wird angenommen, dass Ihre XML-Zeichenfolge `items` auch die Attribute `expired='true'` und `expired='false'` enthält:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In diesem Beispiel wird der XPath-Ausdruck `'//name[@expired]'` übergeben, um alle `name`-Elemente zu suchen, die über das Attribut `expired` verfügen:

`xpath(xml(parameters('items')), '//name[@expired]')`

Hier ist das Ergebnis: `[ Gala, Honeycrisp ]`

*Beispiel 5*

In diesem Beispiel wird angenommen, dass Ihre XML-Zeichenfolge `items` nur das Attribut `expired = 'true'` enthält:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In diesem Beispiel wird der XPath-Ausdruck `'//name[@expired = 'true']'` übergeben, um alle `name`-Elemente zu suchen, die über das Attribut `expired = 'true'` verfügen:

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Hier ist das Ergebnis: `[ Gala ]`

*Beispiel 6*

In diesem Beispiel wird angenommen, dass Ihre XML-Zeichenfolge `items` auch diese Attribute enthält: 

* `expired='true' price='12'`
* `expired='false' price='40'`

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name expired='true' price='12'>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name expired='false' price='40'>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In diesem Beispiel wird der XPath-Ausdruck `'//name[price>35]'` übergeben, um alle `name`-Elemente zu suchen, die über `price > 35` verfügen:

`xpath(xml(parameters('items')), '//name[price>35]')`

Hier ist das Ergebnis: `Honeycrisp`

*Beispiel 7*

In diesem Beispiel wird angenommen, dass Ihre XML-Zeichenfolge `items` mit der in Beispiel 1 übereinstimmt:

```xml
<?xml version="1.0"?>
<produce>
  <item>
    <name>Gala</name>
    <type>apple</type>
    <count>20</count>
  </item>
  <item>
    <name>Honeycrisp</name>
    <type>apple</type>
    <count>10</count>
  </item>
</produce>
```

In diesem Beispiel wird nach Knoten gesucht, die mit dem Knoten `<count></count>` übereinstimmen, und diese Knotenwerte werden mit der `sum()`-Funktion addiert:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Hier ist das Ergebnis: `30`

*Beispiel 8*

In diesem Beispiel wird angenommen, dass Sie über diese XML-Zeichenfolge verfügen, die den XML-Dokumentnamespace `xmlns="https://contoso.com"` enthält:

```xml
<?xml version="1.0"?><file xmlns="https://contoso.com"><location>Paris</location></file>
```

In diesen Ausdrücken wird einer der XPath-Ausdrücke `/*[name()="file"]/*[name()="location"]` oder `/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]` verwendet, um nach Knoten zu suchen, die mit dem Knoten `<location></location>` übereinstimmen. Diese Beispiele zeigen die Syntax, die Sie im Logik-App-Designer oder im Ausdrucks-Editor verwenden:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="https://contoso.com"]/*[local-name()="location"]')`

Dies ist der Ergebnisknoten, der mit dem `<location></location>`-Knoten übereinstimmt: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Wenn Sie in der Codeansicht arbeiten, verwenden Sie den umgekehrten Schrägstrich (\\) als Escapezeichen für das doppelte Anführungszeichen ("). 
> Beispielsweise müssen Sie Escapezeichen verwenden, wenn Sie einen Ausdruck als JSON-Zeichenfolge serialisieren. 
> Wenn Sie jedoch im Logik-App-Designer oder im Ausdrucks-Editor arbeiten, müssen Sie das doppelte Anführungszeichen nicht mit Escapezeichen versehen, da der umgekehrte Schrägstrich automatisch der zugrunde liegenden Definition hinzugefügt wird, z. B.:
> 
> * Codeansicht: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Ausdrucks-Editor: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Beispiel 9*

In diesem Beispiel, das an Beispiel 8 anschließt, wird der XPath-Ausdruck `'string(/*[name()="file"]/*[name()="location"])'` verwendet, um den Wert im Knoten `<location></location>` zu suchen:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Hier ist das Ergebnis: `Paris`

## <a name="next-steps"></a>Nächste Schritte

Informationen zu der [Definitionssprache für Workflows](../logic-apps/logic-apps-workflow-definition-language.md)
