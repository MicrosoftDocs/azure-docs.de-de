---
title: Abfragebeschleunigung – SQL-Sprachreferenz
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie die SQL-Syntax für die Abfragebeschleunigung verwenden.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
ms.openlocfilehash: 2eda67e377a3b61e696e732b916d788c00a18eae
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95908777"
---
# <a name="query-acceleration-sql-language-reference"></a>Abfragebeschleunigung – SQL-Sprachreferenz

Die Abfragebeschleunigung unterstützt eine ANSI SQL-ähnliche Sprache, um Abfragen für Blobinhalte zu schreiben.  Der SQL-Dialekt für die Abfragebeschleunigung stellt eine Teilmenge von ANSI SQL mit einer begrenzten Anzahl unterstützter Datentypen, Operatoren usw. dar, bietet aber gleichzeitig auch eine Erweiterung von ANSI SQL, um Abfragen für hierarchische teilstrukturierte Datenformate wie JSON zu unterstützen. 

## <a name="select-syntax"></a>SELECT-Syntax

Die einzige von der Abfragebeschleunigung unterstützte SQL-Anweisung ist die SELECT-Anweisung. In diesem Beispiel wird jede Zeile zurückgegeben, für die der Ausdruck „true“ zurückgibt.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

Bei CSV-formatierten Daten muss *table* `BlobStorage` lauten.  Dies bedeutet, dass die Abfrage für jedes Blob ausgeführt wird, das im REST-Aufruf angegeben wurde.
Bei JSON-formatierten Daten ist *table* ein „Tabellendeskriptor“.   Weitere Informationen finden Sie im Abschnitt [Tabellendeskriptoren](#table-descriptors) in diesem Artikel.

Im folgenden Beispiel gibt die Anweisung für jede Zeile, für die der WHERE-Ausdruck (*expression*) „true“ zurückgibt, eine neue Zeile zurück, die aus der Auswertung der einzelnen Projektionsausdrücke resultiert.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

Im folgenden Beispiel wird eine aggregierte Berechnung (z. B. der Durchschnittswert einer bestimmten Spalte) für jede Zeile zurückgegeben, für die *expression* „true“ zurückgibt. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

Im folgenden Beispiel werden geeignete Offsets zum Aufteilen eines CSV-formatierten Blobs zurückgegeben.  Weitere Informationen finden Sie im Abschnitt [Sys.Split](#sys-split) dieses Artikels.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types"></a>

## <a name="data-types"></a>Datentypen

|Datentyp|BESCHREIBUNG|
|---------|-------------------------------------------|
|INT      |Ganze 64-Bit-Zahl mit Vorzeichen:                     |
|GLEITKOMMAZAHL    |64-Bit-Gleitkommazahl (mit doppelter Genauigkeit).|
|STRING   |Unicode-Zeichenfolge variabler Länge.            |
|timestamp|Ein Zeitpunkt.                           |
|BOOLEAN  |„true“ oder „false“.                             |

Beim Lesen von Werten aus CSV-formatierten Daten werden alle Werte als Zeichenfolgen gelesen.  Zeichenfolgenwerte können mithilfe von CAST-Ausdrücken in andere Typen konvertiert werden.  Werte können abhängig vom Kontext implizit in andere Typen umgewandelt werden. Weitere Informationen finden Sie unter [Rangfolge der Datentypen (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

## <a name="expressions"></a>Ausdrücke

### <a name="referencing-fields"></a>Verweisen auf Felder

Bei JSON-formatierten oder CSV-formatierten Daten mit einer Kopfzeile kann anhand des Namens auf Felder verwiesen werden.  Feldnamen können mit oder ohne Anführungszeichen angegeben werden. Feldnamen in Anführungszeichen werden in doppelte Anführungszeichen (") eingeschlossen und dürfen Leerzeichen enthalten. Außerdem wird die Groß-/Kleinschreibung beachtet.  Bei Feldnamen ohne Anführungszeichen wird die Groß-/Kleinschreibung nicht beachtet, und Sie dürfen keine Sonderzeichen enthalten.

In CSV-formatierten Daten kann auch durch die Ordnungszahl auf Felder verwiesen werden, der ein Unterstrich (_) vorangestellt ist.  Beispielsweise kann auf das erste Feld mit „_1“ oder auf das elfte Feld mit „_11“ verwiesen werden.  Anhand der Ordnungszahl auf Felder zu verweisen, ist bei CSV-formatierten Daten hilfreich, die keine Kopfzeile enthalten. In diesem Fall bietet die Ordnungszahl die einzige Möglichkeit, auf ein bestimmtes Feld zu verweisen.

### <a name="operators"></a>Operatoren

Folgende SQL-Standardoperatoren werden unterstützt:

|Operator|Beschreibung|
|--|--|
|[=](/sql/t-sql/language-elements/equals-transact-sql)    |Vergleicht die Gleichwertigkeit von zwei Ausdrücken (ein Vergleichsoperator).|
|[!=](/sql/t-sql/language-elements/not-equal-to-transact-sql-exclamation)    |Testet, ob zwei Ausdrücke ungleich sind (ein Vergleichsoperator).|
|[<>](/sql/t-sql/language-elements/not-equal-to-transact-sql-traditional)    |Vergleicht zwei Ausdrücke nach dem Kriterium „ungleich“ (ein Vergleichsoperator).|
|[<](/sql/t-sql/language-elements/less-than-transact-sql)    |Vergleicht zwei Ausdrücke nach dem Kriterium „kleiner als“ (ein Vergleichsoperator).|
|[<=](/sql/t-sql/language-elements/less-than-or-equal-to-transact-sql)    |Vergleicht zwei Ausdrücke nach dem Kriterium „kleiner als oder gleich“ (ein Vergleichsoperator).|
|[>](/sql/t-sql/language-elements/greater-than-transact-sql)    |Vergleicht zwei Ausdrücke nach dem Kriterium „größer als“ (ein Vergleichsoperator). |
|[>=](/sql/t-sql/language-elements/greater-than-or-equal-to-transact-sql)    |Vergleicht zwei Ausdrücke nach dem Kriterium größer als oder gleich (ein Vergleichsoperator).|
|[+](/sql/t-sql/language-elements/add-transact-sql)    |Addition zweier Zahlen. Mit diesem arithmetischen Operator für die Addition kann auch eine Anzahl von Tagen zu einem Datum addiert werden.|
|[-](/sql/t-sql/language-elements/subtract-transact-sql)    |Subtrahiert zwei Zahlen (ein arithmetischer Subtraktionsoperator). |
|[/](/sql/t-sql/language-elements/divide-transact-sql)    |Division einer Zahl durch eine andere (arithmetischer Operator für die Division).|
|[*](/sql/t-sql/language-elements/multiply-transact-sql)    |Multiplikation zweier Ausdrücke (arithmetischer Operator für die Multiplikation).|
|[%](/sql/t-sql/language-elements/modulo-transact-sql)    |Gibt den Rest der Division einer Zahl durch eine andere zurück.|
|[AND](/sql/t-sql/language-elements/bitwise-and-transact-sql)    |Führt eine bitweise logische AND-Operation zwischen zwei ganzzahligen Werten aus.|
|[OR](/sql/t-sql/language-elements/bitwise-or-transact-sql)    |Führt eine bitweise logische OR-Operation zwischen zwei gegebenen ganzzahligen Werten durch, die innerhalb von Transact-SQL-Anweisungen in binäre Ausdrücke umgewandelt wurden.|
|[NOT](/sql/t-sql/language-elements/not-transact-sql)    |Negiert eine boolesche Eingabe.|
|[CAST](/sql/t-sql/functions/cast-and-convert-transact-sql)    |Konvertiert einen Ausdruck von einem Datentyp in einen anderen.|
|[BETWEEN](/sql/t-sql/language-elements/between-transact-sql)    |Gibt einen zu testenden Bereich an.|
|[IN](/sql/t-sql/language-elements/in-transact-sql)    |Ermittelt, ob ein angegebener Wert mit einem Wert aus einer Unterabfrage oder Liste übereinstimmt.|
|[NULLIF](/sql/t-sql/language-elements/nullif-transact-sql)    |Gibt einen NULL-Wert zurück, wenn die beiden angegebenen Ausdrücke gleich sind.|
|[COALESCE](/sql/t-sql/language-elements/coalesce-transact-sql)    |Wertet die Argumente in der vorliegenden Reihenfolge aus und gibt den aktuellen Wert des ersten Ausdrucks zurück, der zuerst nicht als NULL ausgewertet wird.|

Wenn sich die Datentypen auf der linken und rechten Seite eines Operators unterscheiden, wird eine automatische Konvertierung gemäß den hier angegebenen Regeln durchgeführt: [Rangfolge der Datentypen (Transact-SQL)](/sql/t-sql/data-types/data-type-precedence-transact-sql).

Die SQL-Sprache für die Abfragebeschleunigung unterstützt nur eine sehr kleine Teilmenge der Datentypen, die in diesem Artikel erläutert werden.  Weitere Informationen finden Sie im Abschnitt [Datentypen](#data-types) dieses Artikels.

### <a name="casts"></a>Umwandlungen

Die SQL-Sprache für die Abfragebeschleunigung unterstützt den CAST-Operator gemäß den folgenden Regeln: [Datentypkonvertierung (Datenbank-Engine)](/sql/t-sql/data-types/data-type-conversion-database-engine).  

Die SQL-Sprache für die Abfragebeschleunigung unterstützt nur eine kleine Teilmenge der Datentypen, die in diesem Artikel erläutert werden.  Weitere Informationen finden Sie im Abschnitt [Datentypen](#data-types) dieses Artikels.

### <a name="string-functions"></a>Zeichenfolgenfunktionen

Die SQL-Sprache für die Abfragebeschleunigung unterstützt die folgenden standardmäßigen SQL-Zeichenfolgenfunktionen:

|Funktion|Beschreibung|
|--|--|
|CHAR_LENGTH    | Gibt die Länge des Zeichenfolgenausdrucks in Zeichen zurück, wenn der Zeichenfolgenausdruck den Zeichenfolgendatentyp aufweist. Anderenfalls wird die Länge des Zeichenfolgenausdrucks in Byte zurückgegeben (die kleinste ganze Zahl, die nicht weniger als die Anzahl von Bits ist, dividiert durch 8). (Diese Funktion entspricht der CHARACTER_LENGTH-Funktion.)|
|CHARACTER_LENGTH    |Gibt die Länge des Zeichenfolgenausdrucks in Zeichen zurück, wenn der Zeichenfolgenausdruck den Zeichenfolgendatentyp aufweist. Anderenfalls wird die Länge des Zeichenfolgenausdrucks in Byte zurückgegeben (die kleinste ganze Zahl, die nicht weniger als die Anzahl von Bits ist, dividiert durch 8). (Diese Funktion entspricht der CHAR_LENGTH-Funktion.)|
|[LOWER](/sql/t-sql/functions/lower-transact-sql)    |Gibt einen Zeichenausdruck zurück, dessen Zeichen von Großbuchstaben in Kleinbuchstaben umgewandelt wurden.|
|[UPPER](/sql/t-sql/functions/upper-transact-sql)    |Gibt einen Zeichenausdruck zurück, wobei Kleinbuchstaben in Großbuchstaben umgewandelt werden.|
|[SUBSTRING](/sql/t-sql/functions/substring-transact-sql)    |Gibt einen Teil eines Zeichen-, Binär-, Text- oder Bildausdrucks in SQL Server zurück.|
|[TRIM](/sql/t-sql/functions/trim-transact-sql)    |Entfernt das Leerzeichen – char(32) – oder andere am Beginn und Ende einer Zeichenfolge angegebene Zeichen.|
|LEADING    |Beschreibung|
|TRAILING    |Beschreibung|

Hier einige Beispiele:

|Funktion|Beispiel|Ergebnis|
|---|---|---|
|CHARACTER_LENGTH|``SELECT CHARACTER_LENGTH('abcdefg') from BlobStorage`` |``7``|
|CHAR_LENGTH|``SELECT CHAR_LENGTH(_1) from BlobStorage``|``1``|
|LOWER|``SELECT LOWER('AbCdEfG') from BlobStorage``|``abcdefg``|
|UPPER|``SELECT UPPER('AbCdEfG') from BlobStorage``|``ABCDEFG``|
|SUBSTRING|``SUBSTRING('123456789', 1, 5)``|``23456``|
|TRIM|``TRIM(BOTH '123' FROM '1112211Microsoft22211122')``|``Microsoft``|

### <a name="date-functions"></a>Datumsfunktionen

Die folgenden standardmäßigen SQL-Datumsfunktionen werden unterstützt:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Derzeit werden alle [Datumsformate gemäß IS0-8601-Standard](https://www.w3.org/TR/NOTE-datetime) konvertiert. 

#### <a name="date_add-function"></a>DATE_ADD-Funktion

In der SQL-Sprache für die Abfragebeschleunigung können für die ``DATE_ADD``-Funktion „year“, „month“, „day“, „hour“, „minute“ und „second“ angegeben werden.

Beispiele:

``sql DATE_ADD(datepart, quantity, timestamp) DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### <a name="extract-function"></a>EXTRACT-Funktion

Bei EXTRACT unterstützt die SQL-Sprache für die Abfragebeschleunigung im Gegensatz zum Datumsteil für die ``DATE_ADD``-Funktion „timezone_hour“ und „timezone_minute“ als Datumsteil.

Beispiele:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### <a name="to_string-function"></a>TO_STRING-Funktion

Beispiele:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

In dieser Tabelle werden Zeichenfolgen beschrieben, mit denen Sie das Ausgabeformat der ``TO_STRING``-Funktion angeben können.

|Formatzeichenfolge    |Output                               |
|-----------------|-------------------------------------|
|yy               |Jahr im zweistelligen Format – 1999 als 99|
|y                |Jahr im vierstelligen Format               |
|yyyy             |Jahr im vierstelligen Format               |
|M                |Monat des Jahres – 1                    |
|MM               |Monat mit Nullen aufgefüllt – 01               |
|MMM              |Abkürzung für den Monat des Jahres – JAN            |
|MMMM             |Vollständiger Monatsname – Mai                      |
|d                |Tag des Monats (1–31)                  |
|dd               |Tag des Monats mit Nullen aufgefüllt (01–31)     |
|a                |AM oder PM                             |
|h.                |Stunde des Tages (1–12)                   |
|hh               |Stunden des Tages mit Nullen aufgefüllt (01–12)     |
|H                |Stunde des Tages (0–23)                   |
|HH               |Stunde des Tages mit Nullen aufgefüllt (00–23)      |
|m                |Minute der Stunde (0–59)                |
|MM               |Minute mit Nullen aufgefüllt (00–59)           |
|s                |Sekunde der Minuten (0–59)             |
|ss               |Sekunden mit Nullen aufgefüllt (00–59)          |
|E                |Sekundenbruchteil (0,1–0,9)        |
|SS               |Sekundenbruchteil (0,01–0,99)      |
|SSS              |Sekundenbruchteil (0,001–0,999)    |
|X                |Abweichung in Stunden                      |
|XX oder XXXX       |Abweichung in Stunden und Minuten (+0430)  |
|XXX oder XXXXX     |Abweichung in Stunden und Minuten (-07:00) |
|x                |Abweichung in Stunden (7)                  |
|xx oder xxxx       |Abweichung in Stunden und Minuten (+0530)    |
|Xxx oder xxxxx     |Abweichung in Stunden und Minuten (+05:30)   |

#### <a name="to_timestamp-function"></a>TO_TIMESTAMP-Funktion

Es werden nur IS0-8601-Formate unterstützt.

Beispiele:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> Sie können auch die ``UTCNOW``-Funktion verwenden, um die Systemzeit abzurufen.


## <a name="aggregate-expressions"></a>Aggregatausdrücke

Eine SELECT-Anweisung kann entweder einen oder mehrere Projektionsausdrücke oder einen einzelnen Aggregatausdruck enthalten.  Die folgenden Aggregatausdrücke werden unterstützt:

|Ausdruck|BESCHREIBUNG|
|--|--|
|[COUNT(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Gibt die Anzahl der Datensätze zurück, die mit dem Prädikatausdruck übereinstimmen.|
|[COUNT(Ausdruck)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql)    |Gibt die Anzahl der Datensätze zurück, für die der Ausdruck ungleich NULL ist.|
|[AVERAGE(Ausdruck)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql)    |Gibt den Durchschnitt der Werte des Ausdrucks zurück, die ungleich NULL sind.|
|[MIN(Ausdruck)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql)    |Gibt den kleinsten Wert des Ausdrucks zurück, der ungleich NULL ist.|
|[MAX(Ausdruck](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql)    |Gibt den größten Wert des Ausdrucks zurück, der ungleich NULL ist.|
|[SUM(Ausdruck)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql)    |Gibt die Summe aller Werte des Ausdrucks zurück, die ungleich NULL sind.|

### <a name="missing"></a>MISSING

``IS MISSING`` ist der einzige nicht standardmäßige Operator, der von der SQL-Sprache für die Abfragebeschleunigung unterstützt wird.  Wenn bei JSON-Daten in einem bestimmten Eingabedatensatz ein Feld fehlt, ergibt das Ausdrucksfeld ``IS MISSING`` den booleschen Wert „true“.

<a id="table-descriptors"></a>

## <a name="table-descriptors"></a>Tabellendeskriptoren

Bei CSV-Daten lautet der Tabellenname immer `BlobStorage`.  Beispiel:

```sql
SELECT * FROM BlobStorage
```

Für JSON-Daten stehen zusätzliche Optionen zur Verfügung:

```sql
SELECT * FROM BlobStorage[*].path
```

Dadurch können Abfragen für Teilmengen von JSON-Daten ausgeführt werden.

Bei JSON-Abfragen können Sie den Pfad in einem Teil der FROM-Klausel angeben. Diese Pfade helfen, die Teilmenge von JSON-Daten zu analysieren. Die Pfade können auf JSON-Array- und -Objektwerte verweisen.

Wir erläutern dies ausführlicher anhand eines Beispiels.

Hier unsere Beispieldaten:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

Möglicherweise interessieren Sie sich nur für das JSON-Objekt `warehouses` in den oben aufgeführten Daten. Das Objekt `warehouses` ist ein JSON-Arraytyp. Sie können es in der FROM-Klausel angeben. Die Beispielabfrage sieht in etwa wie folgt aus.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

Mit der Abfrage werden alle Felder abgerufen, jedoch nur „latitude“ ausgewählt.

Wenn Sie nur auf den JSON-Objektwert `dimensions` zugreifen möchten, können Sie in der Abfrage auf dieses Objekt verweisen. Beispiel:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

Dadurch wird auch der Zugriff auf Elemente des Objekts `dimensions` eingeschränkt. Wenn Sie auf andere Elemente von JSON-Feldern und auf innere Werte von JSON-Objekten zugreifen möchten, können Sie eine Abfrage wie im folgenden Beispiel verwenden:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage und BlobStorage[\*] verweisen beide auf das gesamte Objekt. Wenn Sie jedoch einen Pfad in der FROM-Klausel angegeben haben, müssen Sie BlobStorage[\*].path verwenden.

<a id="sys-split"></a>

## <a name="syssplit"></a>Sys.Split

Dies ist eine besondere Form der SELECT-Anweisung, die nur für CSV-formatierte Daten verfügbar ist.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

Verwenden Sie diese Anweisung, wenn Sie CSV-Datensätze in Batches herunterladen und verarbeiten möchten. Auf diese Weise können Datensätze parallel verarbeitet werden, anstatt alle Datensätze einzeln herunterladen zu müssen. Diese Anweisung gibt keine Datensätze aus der CSV-Datei zurück. Stattdessen wird eine Auflistung von Batchgrößen zurückgegeben. Anschließend können Sie jede Batchgröße verwenden, um einen Batch von Datensätzen abzurufen. 

Verwenden Sie den *split_size*-Parameter, um die Anzahl von Bytes anzugeben, die jeder Batch enthalten soll. Wenn Sie z. B. jeweils nur 10 MB an Daten verarbeiten möchten, sieht die Anweisung wie folgt aus: `SELECT sys.split(10485760)FROM BlobStorage`. Dabei entsprechen 10 MB 10.485.760 Bytes. Jeder Batch enthält so viele Datensätze, wie in diese 10 MB passen. 

In den meisten Fällen ist die Größe jedes Batches etwas höher als der von Ihnen angegebene Wert. Dies liegt daran, dass ein Batch keinen Teildatensatz enthalten darf. Wenn der letzte Datensatz in einem Batch vor dem Ende Ihres Schwellenwerts beginnt, wird der Batch vergrößert, so dass er den vollständigen Datensatz aufnehmen kann. Die Größe des letzten Batches wird wahrscheinlich etwas unter der von Ihnen angegebenen Größe liegen.

>[!NOTE]
> Die Größe für „split_size“ muss mindestens 10 MB (10485760) betragen.

## <a name="see-also"></a>Weitere Informationen

- [Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration.md)
- [Filtern von Daten mithilfe der Abfragebeschleunigung für Azure Data Lake Storage](data-lake-storage-query-acceleration-how-to.md)