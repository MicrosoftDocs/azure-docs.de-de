---
title: Ausdrucksfunktionen im Zuordnungsdatenfluss
description: Hier erhalten Sie Informationen zu Ausdrucksfunktionen in Mapping Data Flow.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/04/2021
ms.openlocfilehash: 8b63565457498663250eb6ab5dc1361e43bbffaf
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585006"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Datentransformationsausdrücke in Mapping Data Flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="expression-functions"></a>Ausdrucksfunktionen

In Data Factory verwenden Sie die Ausdruckssprache des Mapping Data Flow-Features, um Datentransformationen zu konfigurieren.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Absoluter Wert einer Zahl.  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen invertierten Kosinuswert.  
* ``acos(1) -> 0.0``  
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Addiert ein Paar Zeichenfolgen oder Zahlen. Addiert ein Datum zu einer Anzahl von Tagen. Fügt einem Zeitstempel eine Dauer hinzu. Fügt ein Array mit ähnlichem Typ an ein anderes an. Entspricht dem Operator „+“.  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Addiert Tage zu einem Datum oder einem Zeitstempel. Entspricht dem Operator „+“ für Datumsangaben.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Addiert Monate zu einem Datum oder einem Zeitstempel. Optional kann eine Zeitzone übergeben werden.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer AND-Operator. Entspricht „&&“.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen invertierten Sinuswert.  
* ``asin(0) -> 0.0``  
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen invertierten Tangenswert.  
* ``atan(0) -> 0.0``  
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Gibt den Winkel im Bogenmaß zwischen der positiven X-Achse einer Ebene und dem durch die Koordinaten angegebenen Punkt zurück.  
* ``atan2(0, 0) -> 0.0``  
___
### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
Überprüft, ob der erste Wert zwischen zwei anderen Werten liegt. Numerische Werte, Zeichenfolgenwerte und datetime-Werte können verglichen werden * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___
### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Bitweiser And-Operator für integrale Datentypen. Entspricht dem Operator „&“ * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___
### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Bitweiser Or-Operator für integrale Datentypen. Entspricht dem Operator „|“ * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___
### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Bitweiser Or-Operator für integrale Datentypen. Entspricht dem Operator „|“ * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___
### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den Blake2-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen mit einer angegebenen Bitlänge, die nur ein Vielfaches von 8 zwischen 8 und 512 sein kann. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___
### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
Berechnet den Blake2-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen mit einer angegebenen Bitlänge, die nur ein Vielfaches von 8 zwischen 8 und 512 sein kann. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Wendet basierend auf alternierenden Bedingungen einen Wert oder den anderen an. Wenn die Anzahl von Eingaben gerade ist, ist der andere Wert für die letzte Bedingung standardmäßig NULL.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet die Kubikwurzel einer Zahl.  
* ``cbrt(8) -> 2.0``  
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gibt den kleinsten Integerwert zurück, der nicht kleiner als die Zahl ist.  
* ``ceil(-0.1) -> 0``  
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Gibt den ersten Wert ungleich NULL aus einem Satz von Eingaben zurück. Alle Eingaben müssen den gleichen Typ haben.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___
### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
Sammelt alle Werte des Ausdrucks in der aggregierten Gruppe in einem Array. Während dieses Prozesses können Strukturen gesammelt und in alternative Strukturen transformiert werden. Die Anzahl der Elemente ist gleich der Anzahl der Zeilen in dieser Gruppe und kann NULL-Werte enthalten. Die Anzahl der gesammelten Elemente sollte klein sein.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___
### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Ruft alle Ausgabespalten für einen Stream ab. Sie können einen optionalen Streamnamen als zweites Argument übergeben.  
* ``columnNames()``
* ``columnNames('DeriveStream')``

___
### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Ruft alle Ausgabespalten für einen Stream ab. Sie können einen optionalen Streamnamen als zweites Argument übergeben.   
* ``columns()``
* ``columns('DeriveStream')``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Vergleicht zwei Werte des gleichen Typs. Bei „Wert 1 < Wert 2“ wird eine negative ganze Zahl zurückgegeben. Bei „Wert 1 = Wert 2“ wird „0“ zurückgegeben. Und bei „Wert 1 > Wert 2“ wird ein positiver Wert zurückgegeben.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Verkettet eine variable Anzahl von Zeichenfolgen miteinander. Entspricht dem Operator „+“ mit Zeichenfolgen.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Verkettet eine variable Anzahl von Zeichenfolgen unter Verwendung eines Trennzeichens miteinander. Der erste Parameter ist das Trennzeichen.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Gibt TRUE zurück, wenn ein beliebiges Element im angegebenen Array als TRUE im bereitgestellten Prädikat ausgewertet wird. Die contains-Funktion erwartet einen Verweis auf ein Element in der Prädikatfunktion als #item.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen Kosinuswert.  
* ``cos(10) -> -0.8390715290764524``  
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen hyperbolischen Kosinuswert.  
* ``cosh(0) -> 1.0``  
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Berechnet den CRC32-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen mit einer angegebenen Bitlänge, die nur die Werte 0 (256), 224, 256, 384, 512 aufweisen kann. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Ruft das aktuelle Datum ab, wenn dieser Auftrag ausgeführt wird. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Ruft den aktuellen Zeitstempel mit der lokalen Zeitzone ab, wenn der Auftrag ausgeführt wird.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Ruft den aktuellen Zeitstempel als UTC ab. Wenn die aktuelle Uhrzeit in einer anderen Zeitzone als der Zeitzone Ihres Clusters interpretiert werden soll, können Sie eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Standardwert ist die aktuelle Zeitzone. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Verwenden Sie zum Konvertieren der UTC-Zeit in eine andere Zeitzone `fromUTC()`.  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Tag des Monats aus einem Datum ab.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Tag der Woche aus einem Datum ab. 1: Sonntag, 2: Montag, ..., 7: Samstag.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Tag des Jahres aus einem Datum ab.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Dauer in Millisekunden für die Anzahl von Tagen.  
* ``days(2) -> 172800000L``  
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Konvertiert Bogenmaße zu Graden.  
* ``degrees(3.141592653589793) -> 180``  
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dividiert ein Zahlenpaar. Entspricht dem Operator `/`.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``  
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit der angegebenen Zeichenfolge endet.  
* ``endsWith('dumbo', 'mbo') -> true``  
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf Gleichheit. Entspricht dem Operator „==“.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operator für Vergleich auf Gleichheit ohne Berücksichtigung der Groß-/Kleinschreibung. Entspricht dem Operator „<=>“.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___
### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Versieht eine Zeichenfolge mit einem Escapezeichen entsprechend einem Format. Literalwerte für zulässige Formate sind „json“, „xml“, „ecmascript“, „html“ und „java“.
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Berechnet die Fakultät einer Zahl.  
* ``factorial(5) -> 120``  
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Gibt immer den Wert FALSE zurück. Verwenden Sie die Funktions`syntax(false())`, wenn eine Spalte mit dem Namen „false“ vorhanden ist.  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gibt den größten Integerwert zurück, der nicht größer als die Zahl ist.  
* ``floor(-0.1) -> -1``  
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Codiert die angegebene Zeichenfolge in Base64.  
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wird in den Zeitstempel von UTC konvertiert. Sie können die Zeitzone optional in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Standardwert ist die aktuelle Zeitzone. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „größer als“. Entspricht dem Operator „>“.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „größer als oder gleich“. Entspricht dem Operator „>=“.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Gibt den größten Wert in der Liste der Werte als Eingabe zurück, wobei NULL-Werte übersprungen werden. Gibt NULL zurück, wenn alle Eingaben NULL sind.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Überprüft anhand des Namens, ob ein Spaltenwert im Stream vorhanden ist. Sie können einen optionalen Streamnamen als zweites Argument übergeben. Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt. Sie können aber Parameterersetzungen verwenden.  
* ``hasColumn('parent')``  
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Stundenwert eines Zeitstempels ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Dauer in Millisekunden für die Anzahl von Stunden.  
* ``hours(2) -> 7200000L``  
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Wendet basierend auf einer Bedingung einen Wert oder den anderen an. Wenn „Andere“ nicht angegeben ist, gilt der Wert als NULL. Die Werte müssen kompatibel sein (numerisch, Zeichenfolge, ...). * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Überprüft, ob der erste Parameter NULL ist. Wenn der Wert nicht NULL ist, wird der erste Parameter zurückgegeben. Ist er NULL, wird der zweite Parameter zurückgegeben. Wenn drei Parameter angegeben werden, ist das Verhalten identisch mit „iif(isNull(Wert1), Wert2, Wert3), und der dritte Parameter wird zurückgegeben, wenn der erste Wert nicht NULL ist.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Überprüft, ob ein Element im Array vorhanden ist.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wandelt den ersten Buchstaben jedes Worts in Großbuchstaben um. Wörter werden als durch Leerzeichen getrennt identifiziert.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Sucht die Position (basierend auf 1) der Teilzeichenfolge innerhalb einer Zeichenfolge. Bei nicht gefundener Zeichenfolge wird 0 zurückgegeben.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Löschen markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isDelete()``  
* ``isDelete(1)``  
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile als Fehler markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isError()``  
* ``isError(1)``  
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Ignorieren markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Einfügen markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isInsert()``  
* ``isInsert(1)``  
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile bei der Suche übereinstimmt. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isMatch()``  
* ``isMatch(1)``  
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Überprüft, ob der Wert NULL ist.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Aktualisieren markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Einfügen markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Streamindex muss „1“ oder „2“ lauten, und der Standardwert ist „1“.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Ruft den letzten Tag des Monats aus einem Datum ab.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Operator für Vergleich auf „kleiner als oder gleich“. Entspricht dem Operator „<=“.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahiert eine Teilzeichenfolge mit Beginn an Index 1 mit einer Anzahl von Zeichen. Entspricht SUBSTRING(str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Gibt die Länge der Zeichenfolge zurück.  
* ``length('dumbo') -> 5``  
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „kleiner als“. Entspricht dem Operator „<“.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „kleiner als oder gleich“. Entspricht dem Operator „<=“.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Ruft die Levenshtein-Distanz zwischen zwei Zeichenfolgen ab.  
* ``levenshtein('boys', 'girls') -> 4``  
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Das Muster ist eine Zeichenfolge, die buchstabenweise verglichen wird. Ausnahmen sind die folgenden Sonderzeichen: „_“ entspricht einem einzelnen Zeichen in der Eingabe (ähnlich „.“ in regulären ```posix```-Ausdrücken), „%“ entspricht null oder mehr Zeichen in der Eingabe (ähnlich „.*“ in regulären ```posix```-Ausdrücken).
Das Escapezeichen lautet „"“. Wenn ein Escapezeichen einem besonderen Symbol oder einem anderen Escapezeichen vorangestellt ist, wird das folgende Zeichen direkt verglichen. Es ist als Escapezeichen für alle anderen Zeichen ungültig.  
* ``like('icecream', 'ice%') -> true``  
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Sucht die Position (basierend auf 1) der Teilzeichenfolge innerhalb einer Zeichenfolge ab einer bestimmten Position. Wenn die Position nicht angegeben ist, wird vom dem Anfang der Zeichenfolge ausgegangen. Bei nicht gefundener Zeichenfolge wird 0 zurückgegeben.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Berechnet den Logarithmuswert. Eine optionale Basis kann angegeben werden; andernfalls wird die Eulersche Zahl verwendet.  
* ``log(100, 10) -> 2``  
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet den Logarithmuswert über der Basis 10.  
* ``log10(100) -> 2``  
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wandelt eine Zeichenfolge in Kleinschreibung um.  
* ``lower('GunChus') -> 'gunchus'``  
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Füllt die Zeichenfolge nach links mit der angegebenen Auffüllung bis zu einer bestimmten Länge auf. Ist die Zeichenfolge größer oder gleich der Länge, wird sie auf die Länge gekürzt.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ``lpad('dumbo', 8, '<>') -> '<><dumbo'``  
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Entfernt vorangestellte Zeichen aus einer Zeichenfolge. Wenn der zweite Parameter nicht angegeben ist, werden Leerstellen entfernt. Andernfalls werden Zeichen entfernt, die im zweiten Parameter angegeben sind.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den MD5-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen und gibt eine hexadezimale Zeichenfolge mit 32 Zeichen zurück. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Millisekundenwert eines Datums ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Dauer in Millisekunden für die Anzahl von Millisekunden.  
* ``milliseconds(2) -> 2L``  
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Subtrahiert Zahlen. Subtrahiert eine Anzahl von Tagen von einem Datum. Subtrahiert die Dauer von einem Zeitstempel. Subtrahiert einen Zeitstempel vom anderen, um den Unterschied in Millisekunden zu erhalten. Entspricht dem Operator „-“.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Minutenwert eines Zeitstempels ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Dauer in Millisekunden für die Anzahl von Minuten.  
* ``minutes(2) -> 120000L``  
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Führt einen Modulo für ein Zahlenpaar aus. Entspricht dem Operator „%“.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Monatswert eines Datums oder Zeitstempels ab.  
* ``month(toDate('2012-8-8')) -> 8``  
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Ruft die Anzahl der Monate zwischen zwei Datumsangaben ab. Sie können die Berechnung abrunden und eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multipliziert ein Paar Zahlen. Entspricht dem Operator „*“.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negiert eine Zahl. Wandelt positive Zahlen in negative um (und umgekehrt).  
* ``negate(13) -> -13``  
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Gibt die nächste eindeutige Folge zurück. Die Zahl ist nur innerhalb einer Partition aufeinanderfolgend, und ihr ist die Partitions-ID (partitionId) vorangestellt.  
* ``nextSequence() == 12313112 -> false``  
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalisiert den Zeichenfolgenwert zum Trennen von Unicode-Akzentzeichen.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``  
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer Negationsoperator.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf Ungleichheit. Entspricht dem Operator „!=“.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Überprüft, ob der Wert ungleich NULL ist.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Gibt einen NULL-Wert zurück. Verwenden Sie die Funktion `syntax(null())`, wenn eine Spalte mit dem Namen „null“ vorhanden ist. Jeder Vorgang, in dem dieser Ausdruck verwendet wird, hat als Ergebnis NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer OR-Operator. Entspricht „||“.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Führt einen positiven Modulo für ein Zahlenpaar aus.  
* ``pmod(-20, 8) -> 4``  
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Gibt die aktuelle Partitions-ID zurück, in der sich die Eingabezeile befindet.  
* ``partitionId()``  
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Potenziert eine Zahl um den Potenzwert einer anderen.  
* ``power(10, 2) -> 100``  
___
### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
Gibt eine Zufallszahl mit einem optionalen Seed innerhalb einer Partition zurück. Der Seed sollte ein fester Wert sein und wird zusammen mit der PartitionID zum Erzeugen der Zufallswerte * ``random(1) == 1 -> false``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code> verwendet.<br/><br/>
Extrahiert eine übereinstimmende Teilzeichenfolge anhand eines angegebenen RegEx-Musters. Der letzte Parameter identifiziert die Übereinstimmungsgruppe und ist standardmäßig 1, wenn er nicht angegeben ist. `<regex>` (invertiertes Hochkomma) stimmt mit einer Zeichenfolge ohne Escapezeichen überein.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit dem angegebenen RegEx-Muster übereinstimmt. `<regex>` (invertiertes Hochkomma) stimmt mit einer Zeichenfolge ohne Escapezeichen überein.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Ersetzt alle Vorkommen eines RegEx-Musters durch eine andere Teilzeichenfolge in der angegebenen Zeichenfolge. `<regex>` (invertiertes Hochkomma) stimmt mit einer Zeichenfolge ohne Escapezeichen überein.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Unterteilt eine Zeichenfolge anhand eines Trennzeichens basierend auf einem regulären Ausdruck und gibt ein Array mit Zeichenfolgen zurück.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Ersetzt alle Vorkommen einer Teilzeichenfolge durch eine andere Teilzeichenfolge in der angegebenen Zeichenfolge. Wenn der letzte Parameter weggelassen wird, wird standardmäßig eine leere Zeichenfolge angegeben.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Kehrt eine Zeichenfolge um.  
* ``reverse('gunchus') -> 'suhcnug'``  
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahiert eine Teilzeichenfolge mit einer Anzahl von Zeichen von rechts. Entspricht SUBSTRING(str, LENGTH(str) - n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit dem angegebenen RegEx-Muster übereinstimmt.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Rundet eine Zahl mit optional angegebenen Dezimalstellen und einem optionalen Rundungsmodus. Wenn die Dezimalstellen nicht angegeben sind, lautet der Standardwert 0. Wenn der Modus nicht angegeben ist, wird standardmäßig ROUND_HALF_UP(5) verwendet. Mögliche Werte für die Rundung sind 1: ROUND_UP, 2: ROUND_DOWN, 3: ROUND_CEILING, 4: ROUND_FLOOR, 5: ROUND_HALF_UP, 6: ROUND_HALF_DOWN, 7: ROUND_HALF_EVEN, 8: ROUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Füllt die Zeichenfolge nach rechts mit der angegebenen Auffüllung bis zu einer bestimmten Länge auf. Ist die Zeichenfolge größer oder gleich der Länge, wird sie auf die Länge gekürzt.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Entfernt nachgestellte Zeichen aus einer Zeichenfolge. Wenn der zweite Parameter nicht angegeben ist, werden Leerstellen entfernt. Andernfalls werden Zeichen entfernt, die im zweiten Parameter angegeben sind.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Sekundenwert eines Datums ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Dauer in Millisekunden für die Anzahl von Sekunden.  
* ``seconds(2) -> 2000L``  
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den SHA-1-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen und gibt eine hexadezimale Zeichenfolge mit 40 Zeichen zurück. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den SHA-2-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen mit einer angegebenen Bitlänge, die nur die Werte 0 (256), 224, 256, 384 oder 512 aufweisen kann. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen Sinuswert.  
* ``sin(2) -> 0.9092974268256817``  
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen hyperbolischen Sinuswert.  
* ``sinh(0) -> 0.0``  
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Ruft den Code ```soundex``` für die Zeichenfolge ab.  
* ``soundex('genius') -> 'G520'``  
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Unterteilt eine Zeichenfolge basierend auf einem Trennzeichen und gibt ein Array mit Zeichenfolgen zurück.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet die Quadratwurzel einer Zahl.  
* ``sqrt(9) -> 3``  
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit der angegebenen Zeichenfolge beginnt.  
* ``startsWith('dumbo', 'du') -> true``  
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrahiert Tage von einem Datum oder einem Zeitstempel. Entspricht dem Operator „-“ für Datumsangaben.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrahiert Monate von einem Datum oder einem Zeitstempel.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahiert eine Teilzeichenfolge mit einer bestimmten Länge ab einer Position. Die Position basiert auf 1. Wenn die Länge nicht angegeben ist, wird standardmäßig bis zum Ende der Zeichenfolge extrahiert.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen Tangenswert.  
* ``tan(0) -> 0.0``  
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen hyperbolischen Tangenswert.  
* ``tanh(0) -> 0.0``  
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Ersetzt eine Gruppe von Zeichen durch eine andere Gruppe von Zeichen in der Zeichenfolge. Zeichen werden 1:1 ersetzt.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Entfernt vorangestellte und nachgestellte Zeichen aus einer Zeichenfolge. Wenn der zweite Parameter nicht angegeben ist, werden Leerstellen entfernt. Andernfalls werden Zeichen entfernt, die im zweiten Parameter angegeben sind.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Gibt immer den Wert TRUE zurück. Verwenden Sie die Funktion `syntax(true())`, wenn eine Spalte mit dem Namen „true“ vorhanden ist.  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Stimmt mit dem Typ der Spalte überein. Kann nur in Musterausdrücken verwendet werden. „number“ stimmt mit „short“, „integer“, „long“, „double“, „float“ oder „decimal“ überein, „integral“ stimmt mit „short“, „integer“ oder „long“ überein, „fractional“ stimmt mit „double“, „float“ oder „decimal“ überein, und „datetime“ stimmt mit „date“ oder „timestamp“ überein.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___
### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Entfernt die Escapezeichen einer Zeichenfolge entsprechend einem Format. Literalwerte für zulässige Formate sind „json“, „xml“, „ecmascript“, „html“ und „java“.
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wandelt eine Zeichenfolge in Großschreibung um.  
* ``upper('bojjus') -> 'BOJJUS'``  
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Gibt die generierte UUID zurück.  
* ``uuid()``  
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft die Woche des Jahres aus einem Datum ab.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Dauer in Millisekunden für die Anzahl von Wochen.  
* ``weeks(2) -> 1209600000L``  
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer XOR-Operator. Entspricht dem Operator „^“.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Jahreswert eines Datums ab.  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="aggregate-functions"></a>Aggregatfunktionen
Die folgenden Funktionen stehen nur in Transformationen vom Typ „Aggregieren“, „Pivotieren“, „Entpivotieren“ und „Fenster“ zur Verfügung.
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft den Durchschnitt der Werte einer Spalte ab.  
* ``avg(sales)``  
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft den Durchschnitt der Werte einer Spalte basierend auf einem Kriterium ab.  
* ``avgIf(region == 'West', sales)``  
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Ruft die aggregierte Anzahl von Werten ab. Wenn optionale Spalten angegeben sind, werden NULL-Werte in der Anzahl ignoriert.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Ruft die aggregierte Anzahl unterschiedlicher Werte für eine Gruppe von Spalten ab.  
* ``countDistinct(custId, custName)``  
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Ruft die aggregierte Anzahl von Werten basierend auf einem Kriterium ab. Wenn die optionale Spalte angegeben ist, werden NULL-Werte in der Anzahl ignoriert.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz der Population zweier Spalten ab.  
* ``covariancePopulation(sales, profit)``  
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz der Population zweier Spalten basierend auf einem Kriterium ab.  
* ``covariancePopulationIf(region == 'West', sales)``  
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz einer Stichprobe zweier Spalten ab.  
* ``covarianceSample(sales, profit)``  
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz einer Stichprobe zweier Spalten basierend auf einem Kriterium ab.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Ruft den ersten Wert einer Spaltengruppe ab. Wenn der zweite Parameter ignoreNulls nicht angegeben ist, wird dafür FALSE angenommen.  
* ``first(sales)``  
* ``first(sales, false)``  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kurtosis einer Spalte ab.  
* ``kurtosis(sales)``  
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kurtosis einer Spalte basierend auf einem Kriterium ab.  
* ``kurtosisIf(region == 'West', sales)``  
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Ruft den letzten Wert einer Spaltengruppe ab. Wenn der zweite Parameter ignoreNulls nicht angegeben ist, wird dafür FALSE angenommen.  
* ``last(sales)``  
* ``last(sales, false)``  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Ruft den größten Wert einer Spalte ab.  
* ``max(sales)``  
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ruft den größten Wert einer Spalte basierend auf einem Kriterium ab.  
* ``maxIf(region == 'West', sales)``  
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft das Mittel der Werte einer Spalte ab. Entspricht AVG.  
* ``mean(sales)``  
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft das Mittel der Werte einer Spalte basierend auf einem Kriterium ab. Entspricht „avgIf“.  
* ``meanIf(region == 'West', sales)``  
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Ruft den kleinsten Wert einer Spalte ab.  
* ``min(sales)``  
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ruft den kleinsten Wert einer Spalte basierend auf einem Kriterium ab.  
* ``minIf(region == 'West', sales)``  
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Schiefe einer Spalte ab.  
* ``skewness(sales)``  
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Schiefe einer Spalte basierend auf einem Kriterium ab.  
* ``skewnessIf(region == 'West', sales)``  
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Spalte ab.  
* ``stdDev(sales)``  
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Spalte basierend auf einem Kriterium ab.  
* ``stddevIf(region == 'West', sales)``  
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung der Population einer Spalte ab.  
* ``stddevPopulation(sales)``  
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung der Population einer Spalte basierend auf einem Kriterium ab.  
* ``stddevPopulationIf(region == 'West', sales)``  
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Stichprobe einer Spalte ab.  
* ``stddevSample(sales)``  
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Stichprobe einer Spalte basierend auf einem Kriterium ab.  
* ``stddevSampleIf(region == 'West', sales)``  
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe einer numerischen Spalte ab.  
* ``sum(col)``  
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe unterschiedlicher Werte einer numerischen Spalte ab.  
* ``sumDistinct(col)``  
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe einer numerischen Spalte basierend auf Kriterien ab. Die Bedingung kann auf jeder Spalte basieren.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe einer numerischen Spalte basierend auf Kriterien ab. Die Bedingung kann auf jeder Spalte basieren.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz einer Spalte ab.  
* ``variance(sales)``  
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz einer Spalte basierend auf einem Kriterium ab.  
* ``varianceIf(region == 'West', sales)``  
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz der Population einer Spalte ab.  
* ``variancePopulation(sales)``  
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz der Population einer Spalte basierend auf einem Kriterium ab.  
* ``variancePopulationIf(region == 'West', sales)``  
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die ausgewogene Varianz einer Spalte ab.  
* ``varianceSample(sales)``  
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die ausgewogene Varianz einer Spalte basierend auf einem Kriterium ab.  
* ``varianceSampleIf(region == 'West', sales)``  

## <a name="array-functions"></a>Arrayfunktionen
Arrayfunktionen führen Transformationen für Datenstrukturen durch, die Arrays sind. Hierzu gehören spezielle Schlüsselwörter zum Adressieren von Arrayelementen und Indizes:

* ```#acc``` stellt einen Wert dar, den Sie beim Reduzieren eines Arrays in Ihre einzelne Ausgabe einbeziehen möchten.
* ```#index``` stellt den aktuellen Arrayindex, zusammen mit Arrayindexnummern ```#index2, #index3 ...```, dar.
* ```#item``` stellt den aktuellen Elementwert im Array dar.

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
Erstellt ein Array von Elementen. Alle Elemente sollten denselben Typ haben. Wenn keine Elemente angegeben werden, ist ein leeres Zeichenfolgenarray der Standardwert. Entspricht dem Erstellungsoperator „[]“.  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtert Elemente aus dem Array heraus, die dem bereitgestellten Prädikat nicht entsprechen. Die filter-Funktion erwartet einen Verweis auf ein Element in der Prädikatfunktion als #item.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___
### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Suchen Sie das erste Element aus einem Array, das der Bedingung entspricht. Es erfordert eine Filterfunktion, bei der Sie das Element im Array als „#item“ adressieren können. Für tief geschachtelte Zuordnungen können Sie mithilfe der Notation „#item_n(#item_1, #item_2...)“ auf die übergeordneten Zuordnungen verweisen.  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/> Ordnet anhand des bereitgestellten Ausdrucks jedes Element des Arrays einem neuen Element zu. Die map-Funktion erwartet einen Verweis auf ein einzelnes Element in der Ausdrucksfunktion als „#item“. nction as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/> Ordnet anhand des bereitgestellten Ausdrucks jedes Element des Arrays einem neuen Element zu. Die map-Funktion erwartet einen Verweis auf ein einzelnes Element in der Ausdrucksfunktion als „#item“ und einen Verweis auf den Elementindex as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, .10]``  
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/> Akkumuliert Elemente in einem Array. Die reduce-Funktion erwartet einen Verweis auf einen Akkumulator und ein einzelnes Element in der ersten Ausdrucksfunktion als „#acc“ und „#item“. Außerdem wird erwartet, dass der resultierende Wert in der zweiten Ausdrucksfunktion als „#result“ verwendet wird (ession function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '012).34'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/> Ermittelt die Größe eines array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/> Extrahiert eine Teilmenge eines Arrays ab einer Position. Die Position basiert auf 1. Wenn die Länge nicht angegeben ist, wird standardmäßig bis end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> extrahiert. []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/> Sortiert das Array mithilfe der bereitgestellten Prädikatfunktion. Die sort-Funktion erwartet einen Verweis auf zwei aufeinander folgende Elemente in der Ausdrucksfunktion als #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item and a reference to the element index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Accumulates elements in an array. Reduce expects a reference to an accumulator and one element in the first expression function as #acc and #item and it expects the resulting value as #result to be used in the second expression function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Finds the size of an array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extracts a subset of an array from a position. Position is 1 based. If the length is omitted, it is defaulted to end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Sorts the array using the provided predicate function. Sort expects a reference to two consecutive elements in the expression function as #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``.  

## <a name="cached-lookup-functions"></a>Zwischengespeicherte Lookupfunktionen
Die folgenden Funktionen sind nur verfügbar, wenn ein zwischengespeichertes Lookup verwendet wird und Sie eine zwischengespeicherte Senke eingefügt haben.
___
### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
Schlägt die erste Zeile der zwischengespeicherten Senke mithilfe der angegebenen Schlüssel, die mit den Schlüsseln der zwischengespeicherten Senke identisch sind, nach.
* ``cacheSink#lookup(movieId)``  
___
### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
Schlägt alle übereinstimmenden Zeilen der zwischengespeicherten Senke mithilfe der angegebenen Schlüssel, die mit den Schlüsseln der zwischengespeicherten Senke identisch sind, nach.
* ``cacheSink#mlookup(movieId)``  
___
### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
Gibt die erste Zeile der Ergebnisse der zwischengespeicherten Senke zurück: * ``cacheSink#output()``  
___
### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
Gibt die gesamte Ausgabe an Zeilen der zwischengespeicherten Senke zurück: * ``cacheSink#outputs()``
___


## <a name="conversion-functions"></a>Konvertierungsfunktionen

Konvertierungsfunktionen dienen zum Konvertieren von Daten und Datentypen.

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Codiert die angegebene Zeichenfolge in Base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Konvertiert beliebige numerische Werte/Datumsangaben/Zeitstempel/Zeichenfolgen in eine binäre Darstellung.  
* ``toBinary(3) -> [0x11]``  
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Konvertiert die Werte „t“, „true“, „y“, „yes“ und „1“ in „true“ sowie „f“, „false“, „n“, „no“ und „0“ in „false“ und alle anderen Werte in NULL.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___
### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
Konvertiert beliebige numerische oder Zeichenfolgenwerte in einen Bytewert. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden.  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Konvertiert die Eingabedatumszeichenfolge unter Verwendung eines optionalen Eingabedatumsformats in ein Datum. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. Wenn das Eingabedatumsformat weggelassen wird, ist das Standardformat jjjj-[M]M-[t]t. Zulässige Formate sind: [ jjjj, jjjj-[M]M, jjjj-[M]M-[t]t, jjjj-[M]M-[t]tT* ].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen decimal-Wert um. Wenn keine Genauigkeit und Dezimalstellenanzahl angegeben werden, ist der Standardwert (10,2). Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Ein optionales Gebietsschemaformat in Form der BCP47-Sprache (z. B. „en-US“, „de“ oder „zh-CN“).  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen double-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Ein optionales Gebietsschemaformat in Form der BCP47-Sprache (z. B. „en-US“, „de“ oder „zh-CN“).  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen float-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Kürzt alle double-Werte.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Konvertiert beliebige numerische oder Zeichenfolgenwerte in einen Integer-Wert. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Kürzt alle long-, float- und double-Werte.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen long-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Kürzt alle float- und double-Werte.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen short-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Kürzt alle integer-, long-, float- und double-Werte.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Wandelt einen primitiven Datentyp in eine Zeichenfolge um. Für Zahlen und Datumsangaben kann ein Format angegeben werden. Wenn es nicht angegeben ist, wird die Standardeinstellung des Systems verwendet. Das Java-Dezimalzahlenformat wird für Zahlen verwendet. Alle zulässigen Formate finden Sie unter „Java SimpleDateFormat“. Das Standardformat lautet jjjj-MM-tt.  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wandelt eine Zeichenfolge in einen Zeitstempel mit einem optionalen Zeitstempelformat um. Wenn das Zeitstempelformat nicht angegeben ist, wird das Standardmuster „jjjj-[M]M-[t]t hh:mm:ss[.f...]“ verwendet. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Zeitstempel unterstützt eine Genauigkeit im Millisekundenbereich mit dem Wert 999. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wandelt den Zeitstempel in UTC um. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Standardwert ist die aktuelle Zeitzone. Informationen zu verfügbaren Formaten finden Sie unter der Java-Klasse `SimpleDateFormat`. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  

## <a name="metafunctions"></a>Metafunktionen

Metafunktionen werden hauptsächlich auf Metadaten in Ihrem Datenfluss angewendet.

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Dienen zum Suchen eines Unterelements innerhalb einer Struktur oder eines Arrays von Strukturen. Wenn es mehrere Übereinstimmungen gibt, wird die erste zurückgegeben. Ist keine Übereinstimmung vorhanden, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (? date, ? string ...) erfolgen.  Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt, doch können Sie Parameterersetzungen verwenden: * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ````
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
* ````
___
### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wählt einen Spaltenwert nach Name im Ursprungsstream aus. Das zweite Argument ist der Name des Ursprungsstreams. Wenn mehrere Übereinstimmungen vorhanden sind, wird die erste Übereinstimmung zurückgegeben. Ist keine Übereinstimmung vorhanden, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (TO_DATE, TO_STRING, ...) durchgeführt werden. Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt. Sie können aber Parameterersetzungen verwenden.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___
### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wählt ein Array von Spalten nach Name im Stream aus. Das zweite Argument ist der Stream, aus dem es stammt. Wenn mehrere Übereinstimmungen vorhanden sind, wird die erste Übereinstimmung zurückgegeben. Ist keine Übereinstimmung vorhanden, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (TO_DATE, TO_STRING, ...) durchgeführt werden. Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt. Sie können aber Parameterersetzungen verwenden.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wählt einen Spaltenwert nach Name im Stream aus. Sie können einen optionalen Streamnamen als zweites Argument übergeben. Wenn mehrere Übereinstimmungen vorhanden sind, wird die erste Übereinstimmung zurückgegeben. Ist keine Übereinstimmung vorhanden, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (TO_DATE, TO_STRING, ...) durchgeführt werden.  Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt. Sie können aber Parameterersetzungen verwenden.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___
### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Wählen Sie ein Array von Spalten nach Name im Stream aus. Sie können einen optionalen Streamnamen als zweites Argument übergeben. Wenn mehrere Übereinstimmungen vorhanden sind, wird die erste Übereinstimmung zurückgegeben. Wenn bei einer Spalte keine Übereinstimmungen vorhanden sind, ist die gesamte Ausgabe ein NULL-Wert. Der zurückgegebene Wert erfordert eine Typkonvertierungsfunktion (toDate, toString ...).  Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt. Sie können aber Parameterersetzungen verwenden.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Wählt einen Spaltenwert nach seiner relativen Position (1-basiert) im Stream aus. Liegt die Position außerhalb des gültigen Bereichs, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (TO_DATE, TO_STRING, ...) durchgeführt werden. Berechnete Eingaben werden nicht unterstützt. Sie können aber Parameterersetzungen verwenden.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  

## <a name="window-functions"></a>Fensterfunktionen
Die folgenden Funktionen stehen nur in Fenstertransformationen zur Verfügung.
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Die CumeDist-Funktion berechnet die Position eines Werts relativ zu allen Werten in der Partition. Das Ergebnis ist die Anzahl der Zeilen vor der aktuellen Zeile oder gleich dieser in der Reihenfolge der Partition, dividiert durch die Gesamtanzahl von Zeilen in der Fensterpartition. Alle gleichwertigen Werte in der Reihenfolge werden als dieselbe Position ausgewertet.  
* ``cumeDist()``  
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Berechnet den Rang eines Werts in einer Gruppe von Werten, die in der Reihenfolge eines Fensters nach Klausel angegeben sind. Das Ergebnis ist 1 plus der Anzahl von Zeilen vor der aktuellen Zeile oder gleich dieser in der Reihenfolge der Partition. Die Werte erzeugen keine Lücken in der Folge. „Dense Rank“ funktioniert auch dann, wenn die Daten nicht sortiert sind. In diesem Fall wird nach Änderungen der Werte gesucht.  
* ``denseRank()``  
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Ruft den Wert des ersten Parameters ab, der als n Zeilen vor der aktuellen Zeile ausgewertet wird. Der zweite Parameter ist die Anzahl der Zeilen für die Rückwärtssuche. Der Standardwert ist 1. Wenn nicht genügend Zeilen vorhanden sind, wird der Wert NULL zurückgegeben, sofern kein Standardwert angegeben ist.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Ruft den Wert des ersten Parameters ab, der als n Zeilen nach der aktuellen Zeile ausgewertet wird. Der zweite Parameter ist die Anzahl der Zeilen für die Vorwärtssuche. Der Standardwert ist 1. Wenn nicht genügend Zeilen vorhanden sind, wird der Wert NULL zurückgegeben, sofern kein Standardwert angegeben ist.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Die ```NTile```-Funktion dividiert die Zeilen für jede Fensterpartition in `n` Buckets von 1 bis höchstens `n`. Die Bucketwerte variieren um höchstens 1. Wenn die Anzahl der Zeilen in der Partition nicht gleichmäßig in die Anzahl der Buckets unterteilt werden kann, werden die Restwerte auf einen pro Bucket verteilt, beginnend mit dem ersten Bucket. Die ```NTile```-Funktion ist nützlich für die Berechnung von ```tertiles```, Quartilen, Dezilen und anderen üblichen zusammenfassenden Statistiken. Die Funktion berechnet zwei Variablen während der Initialisierung: Der Größe eines regulären Buckets wird eine zusätzliche Zeile hinzugefügt. Beide Variablen basieren auf der Größe der aktuellen Partition. Bei der Berechnung verfolgt die Funktion die aktuelle Zeilennummer, die aktuelle Bucketnummer und die Zeilennummer, an der der Bucket wechselt (bucketThreshold). Wenn die aktuelle Zeilennummer den Bucketschwellenwert erreicht, wird der Bucketwert um eins erhöht, und der Schwellenwert wird um die Bucketgröße erhöht (plus 1, wenn der aktuelle Bucket aufgefüllt wird).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Berechnet den Rang eines Werts in einer Gruppe von Werten, die in der Reihenfolge eines Fensters nach Klausel angegeben sind. Das Ergebnis ist 1 plus der Anzahl von Zeilen vor der aktuellen Zeile oder gleich dieser in der Reihenfolge der Partition. Die Werte erzeugen Lücken in der Folge. „Rank“ funktioniert auch dann, wenn die Daten nicht sortiert sind. In diesem Fall wird nach Änderungen der Werte gesucht.  
* ``rank()``  
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Weist Zeilen in einem Fenster eine sequenzielle Zeilennummerierung zu, die bei 1 beginnt.  
* ``rowNumber()``  

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie, wie Sie den Ausdrucks-Generator verwenden.](concepts-data-flow-expression-builder.md)
