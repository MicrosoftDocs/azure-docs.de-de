---
title: Semantische Interpretation – Knowledge Exploration Service-API
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie die semantische Interpretation in der Knowledge Exploration Service-API (KES) verwenden können.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60814476"
---
# <a name="semantic-interpretation"></a>Semantische Interpretation

Die semantische Interpretation ordnet jedem interpretierten Pfad über die Grammatik eine semantische Ausgabe zu.  Insbesondere wertet der Dienst zur Berechnung der endgültigen Ausgabe die Abfolge der Anweisungen in den `tag`-Elementen aus, die die Interpretation traversieren.  

Bei einer Anweisung kann es sich um eine Zuweisung eines Literals oder einer Variable zu einer anderen Variable handeln.  Sie kann aber auch die Ausgabe einer Funktion mit null oder mehr Parametern einer Variablen zuweisen.  Jeder Funktionsparameter kann mit einem Literal oder einer Variablen angegeben werden.  Wenn die Funktion keine Ausgabe zurückgibt, erfolgt keine Zuweisung.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Eine Variable mit einem Namensbezeichner, der mit einem Buchstaben beginnt und nur aus Buchstaben (A-Z), Ziffern (0-9) und Unterstrichen (\_) besteht.  Der Typ wird nicht implizit aus dem Literal oder dem Funktionsausgabewert abgeleitet, der ihm zugewiesen ist. 

Es folgt eine Liste der zurzeit unterstützten Datentypen:

|Type|BESCHREIBUNG|Beispiele|
|----|----|----|
|string|Abfolge von 0 oder mehr Zeichen|„Hallo Welt!“<br/>""|
|Bool|Boolescher Wert|true<br/>false|
|Int32|Ganze 32-Bit-Zahl mit Vorzeichen:  -2.1e9 bis 2.1e9|123<br/>-321|
|Int64|Ganze 64-Bit-Zahl mit Vorzeichen: -9.2e18 und 9.2e18|9876543210|
|Double|Gleitkommazahl mit doppelter Genauigkeit: 1.7e+/-308 (15 Ziffern)|123.456789<br/>1.23456789e2|
|Guid|GUID (Globally Unique Identifier)|„602DD052-CC47-4B23-A16A-26B52D30C05B“|
|Abfragen|Abfrageausdruck, der im Index eine Teilmenge von Datenobjekten angibt|All()<br/>And(*q1*, *q2*)|

## <a name="semantic-functions"></a>Semantische Funktionen

Es gibt eine Reihe von integrierten semantischen Funktionen.  Sie ermöglichen die Erstellung komplexer Abfragen und eine kontextabhängige Steuerung grammatischer Interpretationen.

### <a name="and-function"></a>And-Funktion

`query = And(query1, query2);`

Gibt eine Abfrage aus der Überschneidung zweier Eingabeabfragen zurück.

### <a name="or-function"></a>Or-Funktion

`query = Or(query1, query2);`

Gibt eine Abfrage aus der Kombination zweier Eingabeabfragen zurück.

### <a name="all-function"></a>All-Funktion

`query = All();`

Gibt eine Abfrage zurück, die alle Datenobjekte enthält.

Im folgenden Beispiel verwenden wir die All()-Funktion, um eine Abfrage basierend auf der Überschneidung von mindestens einem Schlüsselwort iterativ zu erstellen.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>None-Funktion

`query = None();`

Gibt eine Abfrage zurück, die keine Datenobjekte enthält.

Im folgenden Beispiel verwenden wir die None()-Funktion, um eine Abfrage basierend auf der Kombination von mindestens einem Schlüsselwort iterativ zu erstellen.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Query-Funktion

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Gibt eine Abfrage zurück, die nur Datenobjekte enthält, deren Attribut *attrName* gemäß der angegebenen Operation *op* dem Wert *value* entspricht, der standardmäßig auf „eq“ festgelegt ist.  In der Regel verwenden Sie ein `attrref`-Element zum Erstellen einer Abfrage, die auf der Grundlage der übereinstimmenden Zeichenfolge der Eingabeabfrage.  Wenn ein Wert durch andere Methoden zugewiesen oder abgerufen wird, kann mithilfe der Query()-Funktion eine diesem Wert entsprechende Abfrage erstellt werden.

Im folgenden Beispiel wird die Query()-Funktion verwendet, um Unterstützung für die Angabe von wissenschaftlichen Veröffentlichungen von einem bestimmten Jahrzehnt zu implementieren.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Composite-Funktion

`query = Composite(innerQuery);`

Gibt eine Abfrage zurück, die eine *innerQuery* aus Übereinstimmungen mit untergeordneten Attributen eines gemeinsamen zusammengesetzten Attributs *attr* kapselt.  Für die Kapselung muss das zusammengesetzte Attribut *attr* aller übereinstimmenden Datenobjekte mindestens einen Wert aufweisen, der jeweils die *innerQuery* erfüllt.  Beachten Sie, dass eine Abfrage untergeordneter Attribute eines zusammengesetzten Attributs mit der Composite()-Funktion gekapselt werden muss, bevor sie mit anderen Abfragen kombiniert werden kann.

Die folgende Abfrage beispielsweise gibt wissenschaftliche Veröffentlichungen von „harry shum“ zurück, während sie gleichzeitig auf „microsoft“ festgelegt war:
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Die folgende Abfrage gibt hingegen wissenschaftliche Veröffentlichungen zurück, bei denen einer der Autoren „harry shum“ und eine der Zugehörigkeiten „microsoft“ ist:
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable-Funktion

`value = GetVariable(name, scope);`

Gibt den Wert der Variable *name* zurück, die unter dem angegebenen *scope*-Attribut definiert ist.  *name* ist ein Bezeichner, der mit einem Buchstaben beginnt und nur aus Buchstaben (A-Z), Ziffern (0-9) und Unterstrichen (_) besteht.  *scope* kann auf „request“ oder „system“ festgelegt werden.  Beachten Sie, dass sich Variablen, die unter verschiedenen Bereichen definiert werden, voneinander unterscheiden. Dies gilt auch für Variablen, die über die Ausgabe semantischer Funktionen definiert werden.

Bereichsvariablen für Anforderungen werden in allen Interpretationen innerhalb der aktuellen Interpretationsanforderung gemeinsam verwendet.  Mit diesen kann die Suche nach Interpretationen der Grammatik gesteuert werden.

Systemvariablen werden vom Dienst vordefiniert und können zum Abrufen verschiedener Statistiken über den aktuellen Status des Systems verwendet werden.  Im Folgenden werden die derzeit unterstützten Systemvariablen aufgeführt:

|NAME|Type|BESCHREIBUNG|
|----|----|----|
|IsAtEndOfQuery|Bool|„true“, wenn die aktuelle Interpretation mit dem gesamten Text der Eingabeabfrage übereingestimmt hat|
|IsBeyondEndOfQuery|Bool|„true“, wenn die aktuelle Interpretation Vervollständigungen hinter dem Text der Eingabeabfrage vorgeschlagen hat|

### <a name="setvariable-function"></a>SetVariable-Funktion

`SetVariable(name, value, scope);`

Weist den *value* der Variable *name* unter dem angegebenen *scope*-Attribut zu.  *name* ist ein Bezeichner, der mit einem Buchstaben beginnt und nur aus Buchstaben (A-Z), Ziffern (0-9) und Unterstrichen (_) besteht.  Derzeit ist „request“ der einzige gültige Wert für *scope*.  Es gibt keine festlegbaren Systemvariablen.

Bereichsvariablen für Anforderungen werden in allen Interpretationen innerhalb der aktuellen Interpretationsanforderung gemeinsam verwendet.  Mit diesen kann die Suche nach Interpretationen der Grammatik gesteuert werden.

### <a name="assertequals-function"></a>AssertEquals-Funktion

`AssertEquals(value1, value2);`

Wenn *value1* und *value2* gleichwertig sind, wird die Funktion erfolgreich ausgeführt und hat keine Nebeneffekte zufolge.  Andernfalls tritt bei der Ausführung der Funktion ein Fehler auf, und die Interpretation wird abgelehnt.

### <a name="assertnotequals-function"></a>AssertNotEquals-Funktion

`AssertNotEquals(value1, value2);`

Wenn *value1* und *value2* nicht gleichwertig sind, wird die Funktion erfolgreich ausgeführt und hat keine Nebeneffekte zufolge.  Andernfalls tritt bei der Ausführung der Funktion ein Fehler auf, und die Interpretation wird abgelehnt.


