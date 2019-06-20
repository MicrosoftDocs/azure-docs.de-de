---
title: Arbeiten mit Zeichenfolgen in Azure Monitor-Protokollabfragen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Zeichenfolgen in Azure Monitor-Protokollabfragen bearbeiten, vergleichen, durchsuchen und für diese viele weitere Vorgänge ausführen.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 4b2763629a3036551cb3d362e609c72737436f4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424702"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Arbeiten mit Zeichenfolgen in Azure Monitor-Protokollabfragen


> [!NOTE]
> Sie sollten zunächst [Erste Schritte mit Azure Monitor Log Analytics](get-started-portal.md) und [Erste Schritte mit Azure Monitor-Protokollabfragen](get-started-queries.md) lesen, bevor Sie mit diesem Tutorial beginnen.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In diesem Artikel wird beschrieben, wie Sie Zeichenfolgen bearbeiten, vergleichen, darin suchen und für diese viele weitere Vorgänge ausführen.

Jedem Zeichen in einer Zeichenfolge ist ein Index zugeordnet, der die Position des Zeichens festlegt. Für das erste Zeichen wird der Index 0 verwendet, für das nächste Zeichen 1 usw. Der Index wird von unterschiedlichen Zeichenfolgenfunktion verwendet. Dies wird in den folgenden Abschnitten deutlich werden. In vielen der folgenden Beispiele wird der Befehl **print** verwendet, um zu zeigen, wie sich Zeichenfolgen ohne eine bestimmte Datenquelle bearbeiten lassen.


## <a name="strings-and-escaping-them"></a>Zeichenfolgen und Escapezeichen
Zeichenfolgenwerte werden entweder in einfache oder doppelte Anführungszeichen eingeschlossen. Der umgekehrte Schrägstrich (\) wird verwendet, um darauffolgende Zeichen mit einem Escapezeichen zu versehen. „\t“ wird beispielsweise für einen Tabstopp, „\n“ für einen Zeilenvorschub und \" für das Anführungszeichen selbst verwendet.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Wenn „\\“ nicht als Escapezeichen verwendet werden soll, müssen Sie der Zeichenfolge „\@“ voranstellen.

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Zeichenfolgenvergleiche

Operators       |BESCHREIBUNG                         |Groß-/Kleinschreibung|Beispiel (ergibt `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Equals                              |Ja           |`"aBc" == "aBc"`
`!=`           |Not Equals                          |Ja           |`"abc" != "ABC"`
`=~`           |Equals                              |Nein            |`"abc" =~ "ABC"`
`!~`           |Not Equals                          |Nein            |`"aBc" !~ "xyz"`
`has`          |Rechter Ausdruck ist vollständig in linkem Ausdruck enthalten |Nein|`"North America" has "america"`
`!has`         |Rechter Ausdruck ist nicht vollständig in linkem Ausdruck enthalten       |Nein            |`"North America" !has "amer"` 
`has_cs`       |Rechter Ausdruck ist vollständig in linkem Ausdruck enthalten |Ja|`"North America" has_cs "America"`
`!has_cs`      |Rechter Ausdruck ist nicht vollständig in linkem Ausdruck enthalten       |Ja            |`"North America" !has_cs "amer"` 
`hasprefix`    |Rechter Ausdruck ist Präfix in linkem Ausdruck         |Nein            |`"North America" hasprefix "ame"`
`!hasprefix`   |Rechter Ausdruck ist kein Präfix in linkem Ausdruck     |Nein            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Rechter Ausdruck ist Präfix in linkem Ausdruck         |Ja            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Rechter Ausdruck ist kein Präfix in linkem Ausdruck     |Ja            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Rechter Ausdruck ist Suffix in linkem Ausdruck         |Nein            |`"North America" hassuffix "ica"`
`!hassuffix`   |Rechter Ausdruck ist kein Suffix in linkem Ausdruck     |Nein            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Rechter Ausdruck ist Suffix in linkem Ausdruck         |Ja            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Rechter Ausdruck ist kein Suffix in linkem Ausdruck     |Ja            |`"North America" !hassuffix_cs "icA"`
`contains`     |Rechter Ausdruck tritt als Teilzeichenfolge in linkem Ausdruck auf  |Nein            |`"FabriKam" contains "BRik"`
`!contains`    |Rechter Ausdruck tritt nicht in linkem Ausdruck auf           |Nein            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Rechter Ausdruck tritt als Teilzeichenfolge in linkem Ausdruck auf  |Ja           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Rechter Ausdruck tritt nicht in linkem Ausdruck auf           |Ja           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Rechter Ausdruck ist eine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Nein            |`"Fabrikam" startswith "fab"`
`!startswith`  |Rechter Ausdruck ist keine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Nein        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Rechter Ausdruck ist eine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Ja            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Rechter Ausdruck ist keine Teilzeichenfolge, die am Anfang des linken Ausdrucks steht|Ja        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Rechter Ausdruck ist eine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Nein             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Rechter Ausdruck ist keine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Nein         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Rechter Ausdruck ist eine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Ja             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Rechter Ausdruck ist keine Teilzeichenfolge, die am Ende des linken Ausdrucks steht|Ja         |`"Fabrikam" !endswith "brik"`
`matches regex`|Linker Ausdruck enthält eine Übereinstimmung mit rechtem Ausdruck        |Ja           |`"Fabrikam" matches regex "b.*k"`
`in`           |Entspricht einem der Elemente       |Ja           |`"abc" in ("123", "345", "abc")`
`!in`          |Entspricht keinem der Elemente   |Ja           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Zählt die Vorkommnisse einer Teilzeichenfolge in einer Zeichenfolge. Übereinstimmungen können für einfache Zeichenfolgen oder reguläre Ausdrücke ermittelt werden. Bei Übereinstimmungen für einfache Zeichenfolgen treten möglicherweise Überschneidungen auf. Bei Übereinstimmungen für reguläre Ausdrücke ist dies nicht Fall.

### <a name="syntax"></a>Syntax
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumente:
- `text`: die eingegebene Zeichenfolge. 
- `search`: die einfache Zeichenfolge oder der reguläre Ausdruck, die bzw. der in „text“ abgeglichen werden soll.
- `kind` - _normal_ | _regex_ (Standard: „normal“).

### <a name="returns"></a>Rückgabe

Angabe, wie oft die Suchzeichenfolge im Container abgeglichen werden kann. Bei Übereinstimmungen für einfache Zeichenfolgen treten möglicherweise Überschneidungen auf. Bei Übereinstimmungen für reguläre Ausdrücke ist dies nicht Fall.

### <a name="examples"></a>Beispiele

#### <a name="plain-string-matches"></a>Übereinstimmungen für einfache Zeichenfolgen

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Übereinstimmungen für reguläre Ausdrücke

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extract

Ermittelt eine Übereinstimmung für einen regulären Ausdruck auf der Grundlage einer angegebenen Zeichenfolge. Optional kann die extrahierte Teilzeichenfolge in den angegebenen Typ konvertiert werden.

### <a name="syntax"></a>Syntax

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumente

- `regex`: ein regulärer Ausdruck.
- `captureGroup`: eine positive Integerkonstante für die zu extrahierende Erfassungsgruppe. 0 steht für die vollständige Übereinstimmung, 1 für den mit der ersten '('Klammer')' übereinstimmenden Wert im regulären Ausdruck, 2 oder höher für nachfolgende Klammern.
- `text`: die zu suchende Zeichenfolge.
- `typeLiteral`: optionales Typliteral (z.B. typeof(long)). Die extrahierte Teilzeichenfolge wird, sofern angegeben, in diesen Typ konvertiert.

### <a name="returns"></a>Rückgabe
Die mit der angegebenen Erfassungsgruppe „captureGroup“ abgeglichene Teilzeichenfolge, die optional in „typeLiteral“ konvertiert werden kann.
Wenn keine Übereinstimmung vorhanden ist oder bei der Typkonvertierung ein Fehler auftritt, wird NULL zurückgegeben.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel wird aus einem Heartbeatdatensatz das letzte Oktett von *ComputerIP* extrahiert:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Im folgenden Beispiel wird das letzte Oktett extrahiert und in den Typ *real* (Zahl) umgewandelt. Anschließend wird der nächste IP-Wert berechnet.
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Im folgenden Beispiel wird in der Zeichenfolge *Trace* nach der Definition von „Duration“ gesucht. Die Übereinstimmung wird in *real* umgewandelt und mit einer Zeitkonstante (1 s) multipliziert, *durch die „Duration“ in den Typ „timespan“ umgewandelt wird*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* gibt TRUE zurück, wenn das Argument eine leere Zeichenfolge oder NULL ist (s. auch *isnull*).
- *isnotempty* gibt TRUE zurück, wenn das Argument eine Zeichenfolge ist, die nicht leer oder NULL ist (s. auch *isnotnull*). Alias: *notempty*.

### <a name="syntax"></a>Syntax

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Beispiele

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Teilt eine URL in ihre jeweiligen Bestandteile (Protokoll, Host, Port usw.) auf und gibt ein Wörterbuchobjekt zurück, das diese Bestandteile als Zeichenfolgen enthält.

### <a name="syntax"></a>Syntax

```
parseurl(urlstring)
```

### <a name="examples"></a>Beispiele

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Das Ergebnis sieht wie folgt aus:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>replace

Ersetzt alle Übereinstimmungen für reguläre Ausdrücke durch eine andere Zeichenfolge. 

### <a name="syntax"></a>Syntax

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumente

- `regex`: der reguläre Ausdruck, für den eine Übereinstimmung ermittelt werden soll. Er kann Erfassungsgruppen in „('Klammern')“ enthalten.
- `rewrite`: der reguläre Ersatzausdruck für alle Übereinstimmungen, die durch „regex“ ermittelt wurden. Verwenden Sie „\0“, um auf die gesamte Übereinstimmung zu verweisen, „\1“ für die erste Erfassungsgruppe, „\2“ für die zweite Erfassungsgruppe usw.
- `input_text`: die Eingabezeichenfolge, in der gesucht werden soll.

### <a name="returns"></a>Rückgabe
Der Text nach dem Ersetzen aller Übereinstimmungen für reguläre Ausdrücke durch Auswertungen von „rewrite“. Bei Übereinstimmungen kommt es nicht zu Überschneidungen.

### <a name="examples"></a>Beispiele

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Folgende Ergebnisse sind möglich:

Aktivität                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663: Es wurde versucht, auf das Objekt zuzugreifen.  |Aktivitäts-ID 4663: Es wurde versucht, auf das Objekt zuzugreifen.


## <a name="split"></a>split

Teilt eine angegebene Zeichenfolge entsprechend des festgelegten Trennzeichens auf und gibt ein Array mit den Teilzeichenfolgen zurück.

### <a name="syntax"></a>Syntax
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumente:

- `source`: die Zeichenfolge, die entsprechend des festgelegten Trennzeichens geteilt wird.
- `delimiter`: das Trennzeichen, das zum Teilen der Quellzeichenfolge verwendet wird.
- `requestedIndex`: ein optionaler nullbasierter Index. Falls ein solcher angegeben wird, enthält das Zeichenfolgenarray nur dieses Element (sofern vorhanden).


### <a name="examples"></a>Beispiele

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Verkettet Zeichenfolgenargumente (unterstützt 1 bis 16 Argumente).

### <a name="syntax"></a>Syntax
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Beispiele
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Gibt die Länge der Zeichenfolge zurück.

### <a name="syntax"></a>Syntax
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Beispiele
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>substring

Extrahiert ab dem angegebenen Index eine Teilzeichenfolge aus einer Quellzeichenfolge. Optional kann die Länge der angeforderten Teilzeichenfolge angegeben werden.

### <a name="syntax"></a>Syntax
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumente:

- `source`: die Quellzeichenfolge, aus der die Teilzeichenfolge entnommen wird.
- `startingIndex`: die nullbasierte Position des Anfangszeichens der angeforderten Teilzeichenfolge.
- `length`: ein optionaler Parameter, mit dem die erforderliche Länge der zurückgegebenen Teilzeichenfolge festgelegt werden kann.

### <a name="examples"></a>Beispiele
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Konvertiert eine angegebene Zeichenfolge in Groß-/Kleinbuchstaben.

### <a name="syntax"></a>Syntax
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Beispiele
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Nächste Schritte
Fahren Sie mit den folgenden komplexeren Tutorials fort:
* [Aggregationsfunktionen](aggregations.md)
* [Erweiterte Aggregationen](advanced-aggregations.md)
* [Diagramme](charts.md)
* [Arbeiten mit JSON und Datenstrukturen](json-data-structures.md)
* [Schreiben von erweiterten Abfragen](advanced-query-writing.md)
* [Joins: tabellenübergreifende Analysen](joins.md)
