---
title: Ausdrucksfunktionen in der Mappingdatenflussfunktion von Azure Data Factory
description: Erhalten Sie Informationen zu Ausdrucksfunktionen im Mappingdatenfluss.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 6181c3cff18322ee589ee3506310a5bc7f21e6e7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165918"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Datentransformationsausdrücke im Mappingdatenfluss 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Ausdrucksfunktionen

In Data Factory verwenden Sie die Ausdruckssprache der Mappingdatenflussfunktion, um Datentransformationen zu konfigurieren.

*********************************
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Führt einen positiven Modulo für ein Zahlenpaar aus.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen invertierten Kosinuswert: * ``acos(1) -> 0.0``
*********************************
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Addiert ein Paar Zeichenfolgen oder Zahlen. Addiert ein Datum zu einer Anzahl von Tagen. Fügt ein Array mit ähnlichem Typ an ein anderes an. Entspricht dem Operator +: * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
*********************************
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Addiert Tage zu einem Datum oder einem Zeitstempel. Entspricht dem Operator + für Datumsangaben: * ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Addiert Monate zu einem Datum oder einem Zeitstempel: * ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer UND-Operator. Entspricht &&: * ``and(true, false) -> false``
* ``true && false -> false``
*********************************
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen invertierten Sinuswert: * ``asin(0) -> 0.0``
*********************************
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen invertierten Tangenswert: * ``atan(0) -> 0.0``
*********************************
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Gibt den Winkel im Bogenmaß zwischen der positiven x-Achse einer Ebene und dem durch die Koordinaten angegebenen Punkt zurück: * ``atan2(0, 0) -> 0.0``
*********************************
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft den Durchschnitt der Werte einer Spalte ab: * ``avg(sales) -> 7523420.234``
*********************************
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft den Durchschnitt der Werte einer Spalte basierend auf einem Kriterium ab: * ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Wählt einen Spaltenwert nach Name im Stream aus. Wenn mehrere Übereinstimmungen vorhanden sind, wird die erste Übereinstimmung zurückgegeben. Ist keine Übereinstimmung vorhanden, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (TO_DATE, TO_STRING, ...) durchgeführt werden.  Spaltennamen, die zur Entwurfszeit bekannt sind, sollten nur mit ihrem Namen angegeben werden. Berechnete Eingaben werden nicht unterstützt, doch können Sie Parameterersetzungen verwenden: * ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
*********************************
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Wählt einen Spaltenwert nach seiner relativen Position (1-basiert) im Stream aus. Liegt die Position außerhalb des gültigen Bereichs, wird ein NULL-Wert zurückgegeben. Für den zurückgegebenen Wert muss eine Typkonvertierung anhand einer der Typkonvertierungsfunktionen (TO_DATE, TO_STRING, ...) durchgeführt werden. Berechnete Eingaben werden nicht unterstützt, doch können Sie Parameterersetzungen verwenden: * ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
*********************************
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Wendet basierend auf alternierenden Bedingungen einen Wert oder den anderen an. Wenn die Anzahl von Eingaben gerade ist, ist der andere Wert NULL für die letzte Bedingung: * ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
*********************************
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet die Kubikwurzel einer Zahl: * ``cbrt(8) -> 2.0``
*********************************
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gibt den kleinsten Integerwert zurück, der nicht kleiner als die Zahl ist: * ``ceil(-0.1) -> 0``
*********************************
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Verkettet eine variable Anzahl von Zeichenfolgen miteinander. Entspricht dem Operator + mit Zeichenfolgen: * ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Verkettet eine variable Anzahl von Zeichenfolgen unter Verwendung eines Trennzeichens miteinander. Der erste Parameter ist das Trennzeichen: * ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen Kosinuswert: * ``cos(10) -> -0.83907152907``
*********************************
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen hyperbolischen Kosinuswert: * ``cosh(0) -> 1.0``
*********************************
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Ruft die aggregierte Anzahl von Werten ab. Wenn optionale Spalten angegeben sind, werden NULL-Werte in der Anzahl ignoriert: * ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Ruft die aggregierte Anzahl unterschiedlicher Werte für eine Gruppe von Spalten ab: * ``countDistinct(custId, custName) -> 60``
*********************************
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Ruft die aggregierte Anzahl von Werten basierend auf einem Kriterium ab. Wenn die optionale Spalte angegeben ist, werden NULL-Werte in der Anzahl ignoriert: * ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz der Population zweier Spalten ab: * ``covariancePopulation(sales, profit) -> 122.12``
*********************************
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz der Population zweier Spalten basierend auf einem Kriterium ab: * ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz einer Stichprobe zweier Spalten ab: * ``covarianceSample(sales, profit) -> 122.12``
*********************************
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kovarianz einer Stichprobe zweier Spalten basierend auf einem Kriterium ab: * ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Berechnet den CRC32-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen mit einer angegebenen Bitlänge, die nur die Werte 0 (256), 224, 256, 384, 512 aufweisen kann. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden: * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Die CumeDist-Funktion berechnet die Position eines Werts relativ zu allen Werten in der Partition. Das Ergebnis ist die Anzahl der Zeilen vor der aktuellen Zeile oder gleich dieser in der Reihenfolge der Partition, dividiert durch die Gesamtanzahl von Zeilen in der Fensterpartition. Alle gleichwertigen Werte in der Reihenfolge werden als dieselbe Position ausgewertet.
* ``cumeDist() -> 1``
*********************************
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Ruft das aktuelle Datum ab, wenn dieser Auftrag ausgeführt wird. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
*********************************
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Ruft den aktuellen Zeitstempel mit der lokalen Zeitzone ab, wenn der Auftrag ausgeführt wird: * ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Ruft den aktuellen Zeitstempel als UTC ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Standardwert ist die aktuelle Zeitzone: * ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Tag des Monats aus einem Datum ab: * ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Tag der Woche aus einem Datum ab. 1: Sonntag, 2: Montag, ..., 7: Samstag: * ``dayOfWeek(toDate('2018-06-08')) -> 7``
*********************************
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Tag des Jahres aus einem Datum ab: * ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Wandelt Bogenmaß in Grad um: * ``degrees(3.141592653589793) -> 180``
*********************************
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Berechnet den Rang eines Werts in einer Gruppe von Werten. Das Ergebnis ist 1 plus der Anzahl von Zeilen vor der aktuellen Zeile oder gleich dieser in der Reihenfolge der Partition. Die Werte erzeugen keine Lücken in der Folge. „Dense Rank“ funktioniert auch dann, wenn die Daten nicht sortiert sind. In diesem Fall werden Änderungen der Werte gesucht: * ``denseRank(salesQtr, salesAmt) -> 1``
*********************************
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Dividiert ein Zahlenpaar. Entspricht dem Operator /: * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit der angegebenen Zeichenfolge endet: * ``endsWith('great', 'eat') -> true``
*********************************
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf Gleichheit. Entspricht dem Operator ==: * ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
*********************************
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Operator für Vergleich auf Gleichheit ohne Berücksichtigung der Groß-/Kleinschreibung. Entspricht dem Operator <=>: * ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Berechnet die Fakultät einer Zahl: * ``factorial(5) -> 120``
*********************************
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Gibt immer den Wert FALSE zurück. Verwenden Sie die Funktionssyntax „false()“, wenn eine Spalte mit dem Namen „false“ vorhanden ist: * ``isDiscounted == false()``
* ``isDiscounted() == false``
*********************************
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Ruft den ersten Wert einer Spaltengruppe ab. Wenn der zweite Parameter ignoreNulls nicht angegeben ist, wird dafür FALSE angenommen: * ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Gibt den größten Integerwert zurück, der nicht größer als die Zahl ist: * ``floor(-0.1) -> -1``
*********************************
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wird in den Zeitstempel von UTC konvertiert. Sie können die Zeitzone optional in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Standardwert ist die aktuelle Zeitzone: * ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „größer als“. Entspricht dem Operator >: * ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „größer als oder gleich“. Entspricht dem Operator >=: * ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Gibt den größten Wert in der Liste der Werte als Eingabe zurück. Gibt NULL zurück, wenn alle Eingaben NULL sind: * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Stundenwert eines Zeitstempels ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
*********************************
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Wendet basierend auf einer Bedingung einen Wert oder den anderen an. Wenn „Andere“ nicht angegeben ist, gilt der Wert als NULL. Die Werte müssen kompatibel sein (numerisch, Zeichenfolge...): * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Überprüft, ob ein Element im Array vorhanden ist: * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wandelt den ersten Buchstaben jedes Worts in Großbuchstaben um. Wörter werden als durch Leerzeichen getrennt identifiziert: * ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Sucht die Position (basierend auf 1) der Teilzeichenfolge innerhalb einer Zeichenfolge. Bei nicht gefundener Zeichenfolge wird 0 zurückgegeben: * ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Löschen markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Standardwert für den Streamindex ist 1: * ``isDelete() -> true``
* ``isDelete(1) -> false``
*********************************
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile als Fehler markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Standardwert für den Streamindex ist 1: * ``isError() -> true``
* ``isError(1) -> false``
*********************************
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Ignorieren markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Standardwert für den Streamindex ist 1: * ``isIgnore() -> true``
* ``isIgnore(1) -> false``
*********************************
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Einfügen markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Standardwert für den Streamindex ist 1: * ``isInsert() -> true``
* ``isInsert(1) -> false``
*********************************
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile bei der Suche übereinstimmt. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Standardwert für den Streamindex ist 1: * ``isMatch() -> true``
* ``isMatch(1) -> false``
*********************************
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Überprüft, ob der Wert NULL ist: * ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Überprüft, ob die Zeile zum Aktualisieren markiert ist. Für Transformationen, die mehrere Eingabestreams akzeptieren, können Sie den (auf 1 basierenden) Index des Streams übergeben. Der Standardwert für den Streamindex ist 1: * ``isUpdate() -> true``
* ``isUpdate(1) -> false``
*********************************
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kurtosis einer Spalte ab: * ``kurtosis(sales) -> 122.12``
*********************************
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Kurtosis einer Spalte basierend auf einem Kriterium ab: * ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Ruft den Wert des ersten Parameters ab, der als n Zeilen vor der aktuellen Zeile ausgewertet wird. Der zweite Parameter ist die Anzahl der Zeilen für die Rückwärtssuche. Der Standardwert ist 1. Wenn nicht genügend Zeilen vorhanden sind, wird der Wert NULL zurückgegeben, sofern kein Standardwert angegeben ist: * ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Ruft den letzten Wert einer Spaltengruppe ab. Wenn der zweite Parameter ignoreNulls nicht angegeben ist, wird dafür FALSE angenommen: * ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Ruft den letzten Tag des Monats aus einem Datum ab: * ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Ruft den Wert des ersten Parameters ab, der als n Zeilen nach der aktuellen Zeile ausgewertet wird. Der zweite Parameter ist die Anzahl der Zeilen für die Vorwärtssuche. Der Standardwert ist 1. Wenn nicht genügend Zeilen vorhanden sind, wird der Wert NULL zurückgegeben, sofern kein Standardwert angegeben ist: * ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Operator für Vergleich auf „kleiner als oder gleich“. Entspricht dem Operator <=: * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahiert eine Teilzeichenfolge mit Beginn an Index 1 mit einer Anzahl von Zeichen. Entspricht SUBSTRING(str, 1, n): * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Gibt die Länge der Zeichenfolge zurück: * ``length('kiddo') -> 5``
*********************************
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „kleiner als“. Entspricht dem Operator <: * ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf „kleiner als oder gleich“. Entspricht dem Operator <=: * ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Ruft den Levenshtein-Abstand zwischen zwei Zeichenfolgen ab: * ``levenshtein('boys', 'girls') -> 4``
*********************************
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Das Muster ist eine Zeichenfolge, die buchstabenweise verglichen wird. Ausnahmen sind die folgenden Sonderzeichen: „_“ entspricht einem einzelnen Zeichen in der Eingabe (ähnlich „.“ in regulären POSIX-Ausdrücken), „%“ entspricht null oder mehr Zeichen in der Eingabe (ähnlich „.*“ in regulären POSIX-Ausdrücken).
Das Escapezeichen lautet „"“. Wenn ein Escapezeichen einem besonderen Symbol oder einem anderen Escapezeichen vorangestellt ist, wird das folgende Zeichen direkt verglichen. Es ist als Escapezeichen für alle anderen Zeichen ungültig.
* ``like('icecream', 'ice%') -> true``
*********************************
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Sucht die Position (basierend auf 1) der Teilzeichenfolge innerhalb einer Zeichenfolge ab einer bestimmten Position. Wenn die Position nicht angegeben ist, wird vom dem Anfang der Zeichenfolge ausgegangen. Bei nicht gefundener Zeichenfolge wird 0 zurückgegeben: * ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Berechnet den Logarithmuswert. Eine optionale Basis kann angegeben werden, andernfalls wird die Eulersche Zahl verwendet: * ``log(100, 10) -> 2``
*********************************
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet den Logarithmuswert über der Basis 10: * ``log10(100) -> 2``
*********************************
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wandelt eine Zeichenfolge in Kleinschreibung um: * ``lower('GunChus') -> 'gunchus'``
*********************************
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Füllt die Zeichenfolge nach links mit der angegebenen Auffüllung bis zu einer bestimmten Länge auf. Wenn die Zeichenfolge gleich der Länge oder größer als diese ist, findet keine Operation statt: * ``lpad('great', 10, '-') -> '-----great'``
* ``lpad('great', 4, '-') -> 'great'``
* ``lpad('great', 8, '<>') -> '<><great'``
*********************************
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code>.<br/><br/>
Entfernt vorangestellte Zeichen aus einer Zeichenfolge. Wenn der zweite Parameter nicht angegeben ist, werden Leerstellen entfernt. Andernfalls werden Zeichen entfernt, die im zweiten Parameter angegeben sind: * ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Ruft den größten Wert einer Spalte ab: * ``MAX(sales) -> 12312131.12``
*********************************
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ruft den größten Wert einer Spalte basierend auf einem Kriterium ab: * ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den MD5-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen und gibt eine hexadezimale Zeichenfolge mit 32 Zeichen zurück. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden: * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
*********************************
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft das Mittel der Werte einer Spalte ab. Entspricht AVG: * ``mean(sales) -> 7523420.234``
*********************************
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft das Mittel der Werte einer Spalte basierend auf einem Kriterium ab. Entspricht avgIf: * ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Ruft den kleinsten Wert einer Spalte ab: * ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Ruft den kleinsten Wert einer Spalte basierend auf einem Kriterium ab: * ``minIf(region == 'West', sales) -> 00.01``
*********************************
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Subtrahiert Zahlen. Subtrahiert eine Anzahl von Tagen von einem Datum. Entspricht dem Operator -: * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Minutenwert eines Zeitstempels ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
*********************************
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Führt einen Modulo für ein Zahlenpaar aus. Entspricht dem Operator %: * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Monatswert eines Datums oder Zeitstempels ab: * ``month(toDate('2012-8-8')) -> 8``
*********************************
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Ruft die Anzahl von Monaten zwischen zwei Datumsangaben ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
*********************************
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Multipliziert ein Paar Zahlen. Entspricht dem Operator *: * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Die NTile-Funktion dividiert die Zeilen für jede Fensterpartition in `n` Buckets von 1 bis höchstens `n`. Die Bucketwerte variieren um höchstens 1. Wenn die Anzahl der Zeilen in der Partition nicht gleichmäßig in die Anzahl der Buckets unterteilt werden kann, werden die Restwerte auf einen pro Bucket verteilt, beginnend mit dem ersten Bucket. Die NTile-Funktion ist nützlich für die Berechnung von Tertilen, Quartilen, Dezilen und anderen verbreiteten zusammenfassenden Statistiken. Die Funktion berechnet zwei Variablen während der Initialisierung: Der Größe eines regulären Buckets wird eine zusätzliche Zeile hinzugefügt. Beide Variablen basieren auf der Größe der aktuellen Partition. Bei der Berechnung verfolgt die Funktion die aktuelle Zeilennummer, die aktuelle Bucketnummer und die Zeilennummer, an der der Bucket wechselt (bucketThreshold). Wenn die aktuelle Zeilennummer den Bucketschwellenwert erreicht, wird der Bucketwert um eins erhöht, und der Schwellenwert wird um die Bucketgröße erhöht (plus 1, wenn der aktuelle Bucket aufgefüllt wird).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
*********************************
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negiert eine Zahl. Wandelt positive Zahlen in negative um und umgekehrt: * ``negate(13) -> -13``
*********************************
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Gibt die nächste eindeutige Folge zurück. Die Zahl ist nur innerhalb einer Partition aufeinanderfolgend, und ihr ist die Partitions-ID (partitionId) vorangestellt: * ``nextSequence() -> 12313112``
*********************************
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalisiert den Zeichenfolgenwert zum Trennen von Unicode-Akzentzeichen: * ``normalize('boys') -> 'boys'``
*********************************
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer Negationsoperator: * ``not(true) -> false``
* ``not(premium)``
*********************************
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operator für Vergleich auf Ungleichheit. Entspricht dem Operator !=: * ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Gibt einen NULL-Wert zurück. Verwenden Sie die Funktionssyntax „null()“, wenn eine Spalte mit dem Namen „null“ vorhanden ist. Jeder Vorgang, in dem dieser Ausdruck verwendet wird, hat als Ergebnis NULL: * ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
*********************************
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer ODER-Operator. Entspricht ||: * ``or(true, false) -> true``
* ``true || false -> true``
*********************************
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Führt einen positiven Modulo für ein Zahlenpaar aus.
* ``pmod(-20, 8) -> 4``
*********************************
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Potenziert eine Zahl um den Potenzwert einer anderen: * ``power(10, 2) -> 100``
*********************************
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Berechnet den Rang eines Werts in einer Gruppe von Werten. Das Ergebnis ist 1 plus der Anzahl von Zeilen vor der aktuellen Zeile oder gleich dieser in der Reihenfolge der Partition. Die Werte erzeugen Lücken in der Folge. „Rank“ funktioniert auch dann, wenn die Daten nicht sortiert sind. In diesem Fall werden Änderungen der Werte gesucht: * ``rank(salesQtr, salesAmt) -> 1``
*********************************
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahiert eine übereinstimmende Teilzeichenfolge anhand eines angegebenen RegEx-Musters. Der letzte Parameter identifiziert die Übereinstimmungsgruppe und ist standardmäßig 1, wenn er nicht angegeben ist. `<regex>` (invertiertes Hochkomma) stimmt mit einer Zeichenfolge ohne Escapezeichen überein: * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
*********************************
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit dem angegebenen RegEx-Muster übereinstimmt. `<regex>` (invertiertes Hochkomma) stimmt mit einer Zeichenfolge ohne Escapezeichen überein: * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
*********************************
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Ersetzt alle Vorkommen eines RegEx-Musters durch eine andere Teilzeichenfolge in der angegebenen Zeichenfolge. `<regex>` (invertiertes Hochkomma) stimmt mit einer Zeichenfolge ohne Escapezeichen überein: * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
*********************************
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Unterteilt eine Zeichenfolge basierend auf einem Trennzeichen basierend auf einem regulären Ausdruck und gibt ein Array von Zeichenfolgen zurück: * ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Ersetzt alle Vorkommen einer Teilzeichenfolge durch eine andere Teilzeichenfolge in der angegebenen Zeichenfolge: * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Kehrt eine Zeichenfolge um: * ``reverse('gunchus') -> 'suhcnug'``
*********************************
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahiert eine Teilzeichenfolge mit einer Anzahl von Zeichen von rechts. Entspricht SUBSTRING(str, LENGTH(str) - n, n): * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
*********************************
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit dem angegebenen RegEx-Muster übereinstimmt: * ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Rundet eine Zahl mit optional angegebenen Dezimalstellen und einem optionalen Rundungsmodus. Wenn die Dezimalstellen nicht angegeben sind, lautet der Standardwert 0.  Wenn der Modus nicht angegeben ist, wird standardmäßig ROUND_HALF_UP(5) verwendet. Mögliche Werte für die Rundung sind 1: ROUND_UP, 2: ROUND_DOWN, 3: ROUND_CEILING, 4: ROUND_FLOOR, 5: ROUND_HALF_UP, 6: ROUND_HALF_DOWN, 7: ROUND_HALF_EVEN, 8: ROUND_UNNECESSARY: * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
*********************************
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Weist Zeilen in einem Fenster eine sequenzielle Zeilennummerierung zu, beginnend bei 1: * ``rowNumber() -> 1``
*********************************
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Füllt die Zeichenfolge nach rechts mit der angegebenen Auffüllung bis zu einer bestimmten Länge auf. Wenn die Zeichenfolge gleich oder größer der Länge ist, findet keine Operation statt: * ``rpad('great', 10, '-') -> 'great-----'``
* ``rpad('great', 4, '-') -> 'great'``
* ``rpad('great', 8, '<>') -> 'great<><'``
*********************************
### <code>rtrim</code>rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code>.<br/><br/>
Entfernt nachgestellte Zeichen aus einer Zeichenfolge. Wenn der zweite Parameter nicht angegeben ist, werden Leerstellen entfernt. Andernfalls werden Zeichen entfernt, die im zweiten Parameter angegeben sind: * ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Ruft den Sekundenwert eines Datums ab. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Standardmäßig wird die lokale Zeitzone verwendet.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
*********************************
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den SHA-1-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen und gibt eine hexadezimale Zeichenfolge mit 40 Zeichen zurück. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden: * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
*********************************
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Berechnet den SHA-2-Hash einer Gruppe von Spalten verschiedener primitiver Datentypen mit einer angegebenen Bitlänge, die nur die Werte 0 (256), 224, 256, 384 oder 512 aufweisen kann. Dies kann zum Berechnen eines Fingerabdrucks für eine Zeile verwendet werden: * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
*********************************
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen Sinuswert: * ``sin(2) -> 0.90929742682``
*********************************
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen hyperbolischen Sinuswert: * ``sinh(0) -> 0.0``
*********************************
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Schiefe einer Spalte ab: * ``skewness(sales) -> 122.12``
*********************************
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Schiefe einer Spalte basierend auf einem Kriterium ab: * ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrahiert eine Teilmenge eines Arrays ab einer Position. Die Position basiert auf 1. Wenn die Länge nicht angegeben ist, wird standardmäßig bis zum Ende der Zeichenfolge extrahiert: * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Ruft den SOUNDEX-Code für die Zeichenfolge ab: * ``soundex('genius') -> 'G520'``
*********************************
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Unterteilt eine Zeichenfolge basierend auf einem Trennzeichen und gibt ein Array von Zeichenfolgen zurück: * ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet die Quadratwurzel einer Zahl: * ``sqrt(9) -> 3``
*********************************
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Überprüft, ob die Zeichenfolge mit der angegebenen Zeichenfolge beginnt: * ``startsWith('great', 'gr') -> true``
*********************************
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Spalte ab: * ``stdDev(sales) -> 122.12``
*********************************
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Spalte basierend auf einem Kriterium ab: * ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung der Population einer Spalte ab: * ``stddevPopulation(sales) -> 122.12``
*********************************
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung der Population einer Spalte basierend auf einem Kriterium ab: * ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Stichprobe einer Spalte ab: * ``stddevSample(sales) -> 122.12``
*********************************
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Standardabweichung einer Stichprobe einer Spalte basierend auf einem Kriterium ab: * ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrahiert Monate von einem Datum. Entspricht dem Operator - für Datumsangaben: * ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Subtrahiert Monate von einem Datum oder einem Zeitstempel: * ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahiert eine Teilzeichenfolge mit einer bestimmten Länge ab einer Position. Die Position basiert auf 1. Wenn die Länge nicht angegeben ist, wird standardmäßig bis zum Ende der Zeichenfolge extrahiert: * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe einer numerischen Spalte ab: * ``sum(col) -> value``
*********************************
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe unterschiedlicher Werte einer numerischen Spalte ab: * ``sumDistinct(col) -> value``
*********************************
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe einer numerischen Spalte basierend auf Kriterien ab. Die Bedingung kann auf jeder Spalte basieren: * ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
*********************************
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Ruft die aggregierte Summe einer numerischen Spalte basierend auf Kriterien ab. Die Bedingung kann auf jeder Spalte basieren: * ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
*********************************
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen Tangenswert: * ``tan(0) -> 0.0``
*********************************
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Berechnet einen hyperbolischen Tangenswert: * ``tanh(0) -> 0.0``
*********************************
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Konvertiert die Werte „t“, „true“, „y“, „yes“ und „1“ in TRUE sowie „f“, „false“, „n“, „no“ und „0“ in FALSE und alle anderen Werte in NULL: * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Wandelt eine Zeichenfolge in ein Datum mit einem optionalen Datumsformat um. Alle zulässigen Formate finden Sie unter „Java SimpleDateFormat“. Wenn das Datumsformat nicht angegeben ist, werden Kombinationen der folgenden Formate akzeptiert. [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
*********************************
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen decimal-Wert um. Wenn keine Genauigkeit und Dezimalstellenanzahl angegeben werden, ist der Standardwert (10,2). Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Ein optionales Gebietsschemaformat in der Form der BCP47-Sprache wie „en-US“, „de“, „zh-CN“: * ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
*********************************
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen double-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Ein optionales Gebietsschemaformat in der Form der BCP47-Sprache wie „en-US“, „de“, „zh-CN“: * ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
*********************************
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen float-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Schneidet alle double-Werte ab: * ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
*********************************
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Konvertiert beliebige numerische oder Zeichenfolgenwerte in einen Integer-Wert. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Schneidet alle long-, float- und double-Werte ab: * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
*********************************
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen long-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Schneidet alle float- und double-Werte ab: * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
*********************************
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Wandelt beliebige numerische oder Zeichenfolgenwerte in einen short-Wert um. Ein optionales Java-Dezimalzahlenformat kann für die Umwandlung verwendet werden. Schneidet alle integer-, long-, float- und double-Werte ab: * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
*********************************
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Wandelt einen primitiven Datentyp in eine Zeichenfolge um. Für Zahlen und Datumsangaben kann ein Format angegeben werden. Wenn es nicht angegeben ist, wird die Standardeinstellung des Systems verwendet. Das Java-Dezimalzahlenformat wird für Zahlen verwendet. Alle zulässigen Formate finden Sie unter „Java SimpleDateFormat“. Das Standardformat lautet jjjj-MM-tt: * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
*********************************
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wandelt eine Zeichenfolge in ein Datum mit einem optionalen Zeitstempelformat um. Alle zulässigen Formate finden Sie unter „Java SimpleDateFormat“. Wenn das Zeitstempelformat nicht angegeben ist, wird das Standardmuster jjjj-[M]M-[t]t hh:mm:ss[.f...] verwendet: * ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
*********************************
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Wandelt den Zeitstempel in UTC um. Sie können eine optionale Zeitzone in der Form „GMT“, „PST“, „UTC“, „America/Cayman“ übergeben. Der Standardwert ist die aktuelle Zeitzone: * ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Ersetzt eine Gruppe von Zeichen durch eine andere Gruppe von Zeichen in der Zeichenfolge. Die Zeichen werden 1:1 ersetzt: * ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Entfernt vorangestellte und nachgestellte Zeichen aus einer Zeichenfolge. Wenn der zweite Parameter nicht angegeben ist, werden Leerstellen entfernt. Andernfalls werden Zeichen entfernt, die im zweiten Parameter angegeben sind: * ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
*********************************
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Gibt immer den Wert TRUE zurück. Verwenden Sie die Funktionssyntax „true()“, wenn eine Spalte mit dem Namen „true“ vorhanden ist: * ``isDiscounted == true()``
* ``isDiscounted() == true``
*********************************
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Stimmt mit dem Typ der Spalte überein. Kann nur in Musterausdrücken verwendet werden. „number“ stimmt mit „short“, „integer“, „long“, „double“, „float“ oder „decimal“ überein, „integral“ stimmt mit „short“, „integer“ oder „long“ überein, „fractional“ stimmt mit „double“, „float“ oder „decimal“ überein, und „datetime“ stimmt mit „date“ oder „timestamp“ überein: * ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
*********************************
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Wandelt eine Zeichenfolge in Großschreibung um: * ``upper('bojjus') -> 'BOJJUS'``
*********************************
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz einer Spalte ab: * ``variance(sales) -> 122.12``
*********************************
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz einer Spalte basierend auf einem Kriterium ab: * ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz der Population einer Spalte ab: * ``variancePopulation(sales) -> 122.12``
*********************************
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die Varianz der Population einer Spalte basierend auf einem Kriterium ab: * ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Ruft die ausgewogene Varianz einer Spalte ab: * ``varianceSample(sales) -> 122.12``
*********************************
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Ruft die ausgewogene Varianz einer Spalte basierend auf einem Kriterium ab: * ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft die Woche des Jahres aus einem Datum ab: * ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logischer XOR-Operator. Entspricht dem Operator ^: * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Ruft den Jahreswert eines Datums ab: * ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie, wie Sie den Ausdrucks-Generator verwenden.](concepts-data-flow-expression-builder.md)
