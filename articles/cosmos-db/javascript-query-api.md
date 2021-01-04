---
title: Arbeiten mit der in JavaScript integrierten Abfrage-API in gespeicherten Prozeduren und Triggern in Azure Cosmos DB
description: In diesem Artikel werden die Konzepte für die in die JavaScript-Sprache integrierte Abfrage-API zum Erstellen von gespeicherten Prozeduren und Triggern in Azure Cosmos DB vorgestellt.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: tisande
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: b2563a9af0e0ca6943059698e29d139143780d93
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340973"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>JavaScript-Abfrage-API in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Zusätzlich zum Ausführen von Abfragen mithilfe der SQL-API in Azure Cosmos DB bietet das [serverseitige Cosmos DB SDK](https://github.com/Azure/azure-cosmosdb-js-server/) eine JavaScript-Schnittstelle zum Ausführen optimierter Abfragen in gespeicherten Prozeduren und Triggern in Cosmos DB. Für die Verwendung dieser JavaScript-Schnittstelle sind keine SQL-Sprachkenntnisse erforderlich. Mit der JavaScript-Abfrage-API können Sie programmgesteuert Abfragen erstellen, indem Sie unter Verwendung einer Syntax, die mit den integrierten und weit verbreiteten JavaScript-Bibliotheken von ECMAScript5 wie Lodash vergleichbar ist, Prädikatfunktionen an eine Funktionsaufrufsequenz übergeben. Abfragen werden von der JavaScript-Runtime analysiert und unter Verwendung von Azure Cosmos DB-Indizes effizient ausgeführt.

## <a name="supported-javascript-functions"></a>Unterstützte JavaScript-Funktionen

| **Function** | **Beschreibung** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|Startet einen verketteten Aufruf, der auf „value()“ enden muss.|
|`filter(predicateFunction [, options] [, callback])`|Filtert die Eingabe mit einer Prädikatfunktion, die „true“/„false“ zurückgibt, um Eingabedokumente für die sich ergebende Gruppe zu filtern. Diese Funktion verhält sich ähnlich wie eine WHERE-Klausel in SQL.|
|`flatten([isShallow] [, options] [, callback])`|Kombiniert und vereinfacht Arrays für alle Eingabeelemente zu einem einzelnen Array. Diese Funktion verhält sich ähnlich wie SelectMany in LINQ.|
|`map(transformationFunction [, options] [, callback])`|Wendet eine Projektion unter Verwendung einer Transformationsfunktion an, bei der jedes Eingabeelement einem JavaScript-Objekt oder -Wert zugeordnet wird. Diese Funktion verhält sich ähnlich wie eine SELECT-Klausel in SQL.|
|`pluck([propertyName] [, options] [, callback])`|Diese Funktion ist eine Abkürzung für eine Zuordnung, mit der der Wert einer einzelnen Eigenschaft aus jedem Eingabeelement extrahiert wird.|
|`sortBy([predicate] [, options] [, callback])`|Erzeugt eine neue Gruppe von Dokumenten, indem die Dokumente im Eingabedokument-Datenstrom unter Verwendung des angegebenen Prädikats in aufsteigender Reihenfolge sortiert werden. Diese Funktion verhält sich ähnlich wie eine ORDER BY-Klausel in SQL.|
|`sortByDescending([predicate] [, options] [, callback])`|Erzeugt eine neue Gruppe von Dokumenten, indem die Dokumente im Eingabedokument-Datenstrom unter Verwendung des angegebenen Prädikats in absteigender Reihenfolge sortiert werden. Diese Funktion verhält sich ähnlich wie eine ORDER BY X DESC-Klausel in SQL.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|Führt eine Selbstverknüpfung mit innerem Array aus und fügt Ergebnisse beider Seiten als Tupel der Ergebnisprojektion hinzu. Durch die Verknüpfung eines Personendokuments mit „person.pets“ entstehen also beispielsweise Tupel vom Typ „[Person, Haustier]“. Dies ist mit „SelectMany“ in .NET LINK vergleichbar.|

Bei der Verwendung innerhalb von Prädikat- und/oder Selektorfunktionen werden die folgenden JavaScript-Konstrukte automatisch optimiert, damit sie direkt für Azure Cosmos DB-Indizes ausgeführt werden:

- Einfache Operatoren: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- Literale, einschließlich des Objektliterals: {}
- var, return

Die folgenden JavaScript-Konstrukte werden nicht für Azure Cosmos DB-Indizes optimiert:

- Ablaufsteuerung (z.B. wenn, für, während)
- Funktionsaufrufe

Weitere Informationen finden Sie in der [Cosmos DB-Dokumentation für serverseitiges JavaScript](https://github.com/Azure/azure-cosmosdb-js-server/).

## <a name="sql-to-javascript-cheat-sheet"></a>Cheat Sheet für SQL und JavaScript

In der folgenden Tabelle sind verschiedene SQL-Abfragen und die entsprechenden JavaScript-Abfragen aufgeführt. Bei Eigenschaften (etwa „item.id“) muss genau wie bei SQL-Abfragen die Groß-/Kleinschreibung beachtet werden.

> [!NOTE]
> `__` (doppelter Unterstrich) ist bei Verwendung der JavaScript-Abfrage-API ein Alias für `getContext().getCollection()`.

|**SQL**|**JavaScript-Abfrage-API**|**Beschreibung**|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|Alle Dokumente (umgebrochen mit Fortsetzungstoken) bleiben unverändert.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|Projiziert die ID, Meldung (Alias für „msg“) und Aktion aus allen Dokumenten.|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|Fragt Dokumente mit dem folgenden Prädikat ab: id = "X998_Y998".|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags &amp;& x.Tags.indexOf(123) &amp;gt; -1;<br>});|Fragt Dokumente mit einer Tags-Eigenschaft ab, wobei „Tags“ ein Array mit dem Wert 123 ist.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|Fragt Dokumente mit einem Prädikat, id = "X998_Y998", ab und projiziert anschließend die ID und die Meldung (Alias für „msg“).|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;return doc.Tags &amp;& Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|Filtert nach Dokumenten mit einer Array-Eigenschaft „Tags“, sortiert die resultierenden Dokumente anhand der Zeitstempel-Systemeigenschaft „_ts“ und projiziert und vereinfacht anschließend das Tags-Array.|

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit weiteren Konzepten und Vorgehensweisen zum Schreiben und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB vertraut:

- [Schreiben von gespeicherten Prozeduren und Triggern in Azure Cosmos DB mit der JavaScript-Abfrage-API](how-to-write-javascript-query-api.md)
- [Working with Azure Cosmos DB stored procedures, triggers, and user-defined functions](stored-procedures-triggers-udfs.md) (Arbeiten mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB)
- [Registrieren und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)
- [Referenz zur Azure Cosmos DB JavaScript-Server-API](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6 (ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)
