---
title: SQL-Abfragen für Azure Cosmos DB
description: Informationen zu SQL-Syntax, Datenbankkonzepten und SQL-Abfragen für Azure Cosmos DB. Verwenden Sie SQL als eine JSON-Abfragesprache in Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: mjbrown
ms.openlocfilehash: 943ed63aed0f64ae6cbd62c52731c6ec73ddd0bd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388480"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>SQL-Abfragebeispiele für Azure Cosmos DB

SQL-API-Konten in Azure Cosmos DB unterstützen das Abfragen von Elementen mit SQL (Structured Query Language) als JSON-Abfragesprache. Der Entwurf der Azure Cosmos DB-Abfragesprache hat folgende Zielsetzungen:

* Unterstützung von SQL, einer der meistverwendeten und beliebtesten Abfragesprachen, anstelle der Entwicklung einer neuen Abfragesprache. SQL bietet ein formelles Programmiermodell zur Durchführung umfassender Abfragen für JSON-Elemente.  

* Verwendung des Programmiermodells von JavaScript als Grundlage für die Abfragesprache. Das Typsystem, die Ausdrucksauswertung und der Funktionsaufruf von JavaScript stellen die Grundlage der SQL-API dar. Mit dieser Grundlage erhalten Sie ein natürliches Programmiermodell für Features wie relationale Projektionen, hierarchische übergreifende Navigation für JSON-Elemente, Selbstverknüpfungen, Abfragen räumlicher Daten und das Aufrufen vollständig in JavaScript geschriebener, benutzerdefinierter Funktionen (User Defined Functions, UDFs).

Dieser Artikel führt Sie durch einige SQL-Beispielabfragen für einfache JSON-Elemente. Weitere Informationen zur Azure Cosmos DB-SQL-Sprachsyntax finden Sie im Artikel [SQL-Syntaxreferenz](sql-api-query-reference.md).

## <a id="GettingStarted"></a>Erste Schritte mit SQL-Abfragen

Erstellen Sie in Ihrem Cosmos DB-Konto der SQL-API einen Container mit dem Namen `Families`. Erstellen Sie zwei einfache JSON-Elemente im Container, und führen Sie einige einfache Abfragen für diese Elemente aus.

### <a name="create-json-items"></a>Erstellen von JSON-Elementen

Der folgende Code erstellt zwei einfache JSON-Elemente zu Familien. Die einfachen JSON-Elemente für die Familien Andersen und Wakefield umfassen Eltern, Kinder und deren Haustiere, Adressen und Registrierungsinformationen. Das erste Element enthält Zeichenfolgen, Zahlen, boolesche Werte, Arrays und geschachtelte Eigenschaften.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Das zweite Element verwendet `givenName` und `familyName` anstelle von `firstName` und `lastName`.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female", 
        "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller",
         "givenName": "Lisa",
         "gender": "female",
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

### <a name="query-the-json-items"></a>Abfragen der JSON-Elemente

Führen Sie einige Abfragen für die JSON-Daten aus, um einige der Schlüsselaspekte der SQL-Abfragesprache von Azure Cosmos DB besser zu verstehen.

Die folgende Abfrage gibt die Elemente zurück, in denen das `id`-Feld dem Text `AndersenFamily` entspricht. Da es sich um eine `SELECT *`-Abfrage handelt, ist die Ausgabe der Abfrage das komplette JSON-Element. Weitere Informationen zur SELECT-Syntax finden Sie unter [SELECT-Anweisung](sql-api-query-reference.md#select-query). 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Abfrage hat folgende Ergebnisse: 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

Mit der folgenden Abfrage wird die JSON-Ausgabe auf andere Weise neu formatiert. Die Abfrage projiziert ein neues JSON-Objekt `Family` mit zwei ausgewählten Feldern (`Name` und `City`), wenn die Stadt in der Adresse dem Staat entspricht. Dies ist bei „NY, NY“ der Fall.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

Die Abfrage hat folgende Ergebnisse:

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

Die folgende Abfrage gibt alle Vornamen von Kindern der Familie zurück, deren `id` dem Text `WakefieldFamily` entspricht, und zwar geordnet nach der Stadt.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

Die Ergebnisse sind wie folgt:

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

Die Beispiele oben zeigen verschiedene Aspekte der Cosmos DB-Abfragesprache:  

* Da die SQL-API mit JSON-Werten arbeitet, werden baumförmige Entitäten anstelle von Spalten und Zeilen verarbeitet. Sie können auf die Baumknoten in beliebiger Tiefe verweisen, z.B. `Node1.Node2.Node3…..Nodem`, ähnlich dem zweiteiligen Verweis `<table>.<column>` in ANSI SQL.

* Da die Abfragesprache mit schemalosen Daten arbeitet, muss das Typsystem dynamisch gebunden werden. Der gleiche Ausdruck kann unterschiedliche Typen in unterschiedlichen Elementen ergeben. Das Ergebnis einer Abfrage ist ein gültiger JSON-Wert, aber nicht garantiert innerhalb eines festen Schemas.  

* Azure Cosmos DB unterstützt nur strikte JSON-Elemente. Typsystem und Ausdrücke sind auf JSON-Typen beschränkt. Weitere Informationen finden Sie in der [JSON-Spezifikation](https://www.json.org/).  

* Ein Cosmos DB-Container ist eine schemalose Sammlung von JSON-Elementen. Die Beziehungen innerhalb und zwischen Containerelementen werden implizit nach Eigenständigkeit erfasst, nicht nach Beziehungen von primären Schlüsseln und Fremdschlüsseln. Dieses Feature ist für die elementinternen Verknüpfungen wichtig, die später in diesem Artikel erläutert werden.

## <a id="SelectClause"></a>Die SELECT-Klausel

Jede Abfrage besteht aus einer SELECT-Klausel und optionalen FROM- und WHERE-Klauseln nach ANSI SQL-Standards. Normalerweise wird die Quelle in der FROM-Klausel aufgelistet, und die WHERE-Klausel wendet einen Filter auf die Quelle an, um eine Teilmenge der JSON-Elemente abzurufen. Dann projiziert die SELECT-Klausel die angeforderten JSON-Werte in die ausgewählte Liste. Weitere Informationen zur Syntax finden Sie unter [SELECT-Anweisung](sql-api-query-reference.md#select-query).

Das folgende Beispiel für eine SELECT-Abfrage gibt `address` aus `Families` zurück, deren `id` dem Text `AndersenFamily` entspricht:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>Eigenschaftenaccessor in Anführungszeichen
Sie können auf Eigenschaften zugreifen, indem Sie den Anführungszeichenoperator [] für Eigenschaften verwenden. `SELECT c.grade` and `SELECT c["grade"]` gleichwertig. Diese Syntax ist hilfreich, um eine Eigenschaft mit Escapezeichen zu versehen, die Leerzeichen oder Sonderzeichen enthält oder den gleichen Namen wie ein SQL-Schlüsselwort oder ein reserviertes Wort hat.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>Verschachtelte Eigenschaften

Im folgenden Beispiel werden zwei verschachtelte Eigenschaften (`f.address.state` und `f.address.city`) projiziert.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>JSON-Ausdrücke

Die Projektion unterstützt auch JSON-Ausdrücke wie im folgenden Beispiel gezeigt:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

Im vorherigen Beispiel muss die SELECT-Klausel ein JSON-Objekt erstellen, und da im Beispiel kein Schlüssel bereitgestellt wird, verwendet die Klausel den impliziten Argumentvariablennamen `$1`. Die folgende Abfrage gibt zwei implizite Argumentvariablen zurück: `$1` und `$2`.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>VALUE-Schlüsselwort

Das VALUE-Schlüsselwort ermöglicht die alleinige Rückgabe des JSON-Werts. Die folgende Abfrage gibt z.B. den skalaren Ausdruck `"Hello World"` anstelle von `{$1: "Hello World"}` zurück:

```sql
    SELECT VALUE "Hello World"
```

Die folgende Abfrage gibt die JSON-Werte ohne die `address`-Bezeichnung zurück:

```sql
    SELECT VALUE f.address
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

Das folgende Beispiel zeigt, wie Sie primitive JSON-Werte (Blattebene der JSON-Baumstruktur) zurückgeben:


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="DistinctKeyword"></a>Schlüsselwort DISTINCT

Das Schlüsselwort DISTINCT entfernt Duplikate aus der Projektion der Abfrage.

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

In diesem Beispiel projiziert die Abfrage Werte für alle Nachnamen.

Die Ergebnisse sind wie folgt:

```json
[
    "Andersen"
]
```

Sie können auch eindeutige Objekte projizieren. In diesem Fall kommt das Feld „lastName“ in keinem der beiden Dokumente vor, sodass die Abfrage ein leeres Objekt zurückgibt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT kann auch in der Projektion in einer Unterabfrage verwendet werden:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Diese Abfrage projiziert ein Array mit dem Wert von givenName für jedes untergeordnete Element, aus dem die Duplikate entfernt wurden. Dieses Array hat den Alias ChildNames und wird in der äußeren Abfrage projiziert.

Die Ergebnisse sind wie folgt:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="aliasing"></a>Aliase

Sie können explizit Aliase für Werte in Abfragen verwenden. Wenn eine Abfrage zwei Eigenschaften mit demselben Namen hat, verwenden Sie Aliase, um mindestens eine der Eigenschaften umzubenennen, sodass sie im projizierten Ergebnis eindeutig sind.

Das für die Aliasvergabe verwendete AS-Schlüsselwort ist optional, wie es im folgenden Beispiel bei der Projektion des zweiten Werts als `NameInfo` gezeigt wird:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>Die FROM-Klausel

Die FROM-Klausel (`FROM <from_specification>`) ist optional, es sei denn, die Quelle wird später in der Abfrage gefiltert oder projiziert. Weitere Informationen zur Syntax finden Sie unter [FROM-Syntax](sql-api-query-reference.md#bk_from_clause). Eine Abfrage wie `SELECT * FROM Families` erstellt eine Auflistung für den gesamten `Families`-Container. Sie können auch den Sonderbezeichner „ROOT“ für den Container anstelle des Containernamens verwenden.

Die FROM-Klausel erzwingt die folgenden Regeln pro Abfrage:

* Der Container kann Aliase enthalten, z.B. `SELECT f.id FROM Families AS f` oder einfach `SELECT f.id FROM Families f`. Hier ist `f` der Alias für `Families`. AS ist ein optionales Schlüsselwort, das als Alias für den Bezeichner fungiert.  

* Nach der Aliasverwendung kann der ursprüngliche Name der Quelle nicht mehr gebunden werden. `SELECT Families.id FROM Families f` ist beispielsweise syntaktisch ungültig, da der Bezeichner `Families` durch einen Alias ersetzt wurde und nicht mehr aufgelöst werden kann.  

* Alle referenzierten Eigenschaften müssen vollqualifiziert sein, um bei Fehlen einer strikten Schemaverwendung mehrdeutige Bindungen zu vermeiden. Beispielsweise ist `SELECT id FROM Families f` syntaktisch ungültig, da die Eigenschaft `id` nicht gebunden ist.

### <a name="get-subitems-by-using-the-from-clause"></a>Abrufen von Unterelementen mit der FROM-Klausel

Mit der FROM-Klausel kann die Quelle auf eine kleinere Teilmenge reduziert werden. Um nur eine Teilstruktur in jedem Element aufzulisten, kann der Unterstamm der Teilstruktur zur Quelle werden, wie es im folgenden Beispiel gezeigt wird:

```sql
    SELECT *
    FROM Families.children
```

Die Ergebnisse sind wie folgt:

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
       {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Das oben gezeigte Beispiel verwendet ein Array als Quelle. Sie können aber auch ein Objekt als Quelle verwenden. Die Abfrage zieht jeden gültigen, definierten JSON-Wert in der Quelle für die Aufnahme in das Ergebnis in Betracht. Im folgenden Beispiel werden `Families` ohne `address.state`-Wert ausgeschlossen.

```sql
    SELECT *
    FROM Families.address.state
```

Die Ergebnisse sind wie folgt:

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE-Klausel

Die optionale WHERE-Klausel (`WHERE <filter_condition>`) gibt Bedingungen an, die die JSON-Elemente in der Quelle erfüllen müssen, damit sie von der Abfrage in die Ergebnisse eingeschlossen werden. Ein JSON-Element muss die angegebenen Bedingungen erfüllen (`true`), um für das Ergebnis berücksichtigt zu werden. Die Indexebene verwendet die WHERE-Klausel, um die kleinste Teilmenge von Quellelementen zu bestimmen, die Teil des Ergebnisses sein können. Weitere Informationen zur Syntax finden Sie unter [WHERE-Syntax](sql-api-query-reference.md#bk_where_clause).

Die folgende Abfrage fordert Elemente an, die eine `id`-Eigenschaft mit dem Wert `AndersenFamily` enthalten. Alle Elemente, die keine `id`-Eigenschaft besitzen oder deren Wert nicht `AndersenFamily` ist, werden ausgeschlossen.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalare Ausdrücke in der WHERE-Klausel

Das vorstehende Beispiel zeigt eine einfache Gleichheitsabfrage. Die SQL-API unterstützt auch verschiedene [skalare Ausdrücke](#scalar-expressions). Die am häufigsten verwendeten Ausdrücke sind binäre und unäre Ausdrücke. Eigenschaftsverweise aus dem JSON-Quellobjekt sind ebenfalls gültige Ausdrücke.

Sie können die folgenden unterstützten binären Operatoren verwenden:  

|**Operatortyp**  | **Werte** |
|---------|---------|
|Arithmetik | +,-,*,/,% |
|Bitweise    | \|, &, ^, <<, >>, >>> (Nullauffüllung, Verschiebung nach rechts) |
|Logisch    | AND, OR, NOT      |
|Vergleich | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Zeichenfolge     |  \|\| (Verkettung) |

Die folgenden Abfragen verwenden binäre Operatoren:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Sie können auch die unären Operatoren +, -, ~ und NOT in Abfragen verwenden, wie es in den folgenden Beispielen gezeigt wird:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Sie können auch Eigenschaftsverweise in Abfragen verwenden. Beispielsweise gibt `SELECT * FROM Families f WHERE f.isRegistered` das JSON-Element zurück, das die Eigenschaft `isRegistered` mit dem Wert `true` enthält. Jeder andere Wert (z.B. `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>` oder `<array>`) führt dazu, dass das Element aus dem Ergebnis ausgeschlossen wird. 

### <a name="equality-and-comparison-operators"></a>Gleichheits- und Vergleichsoperatoren

Die folgende Tabelle zeigt die Ergebnisse für Gleichheitsvergleiche in der SQL-API zwischen den einzelnen JSON-Typen.

| **Op** | **Undefiniert** | **NULL** | **Boolescher Wert** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefiniert** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **NULL** | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolescher Wert** | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** |

Bei Vergleichsoperatoren wie `>`, `>=`, `!=`, `<` und `<=` ergeben Vergleiche unterschiedlicher Typen oder zwischen zwei Objekten oder Arrays den Wert `Undefined`.  

Wenn das Ergebnis des skalaren Ausdrucks `Undefined` ist, wird das Element nicht in das Ergebnis eingeschlossen, da `Undefined` nicht gleich `true` ist.

### <a name="logical-and-or-and-not-operators"></a>Logische Operatoren (AND, OR und NOT)

Logische Operatoren arbeiten mit booleschen Werten. Es folgt eine Liste der logischen Wahrheitstabellen für diese Operatoren:

**OR-Operator**

| OR | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND-Operator**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT-Operator**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

## <a name="between-keyword"></a>Schlüsselwort BETWEEN

Wie in ANSI SQL können mit dem Schlüsselwort BETWEEN Abfragen für Bereiche von Zeichenfolgen- oder numerischen Werten ausgedrückt werden. Die folgende Abfrage gibt beispielsweise alle Elemente zurück, in denen das erste Kind die erste bis fünfte Klasse (einschließlich) besucht.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Anders als in ANSI SQL kann die BETWEEN-Klausel auch wie im folgenden Beispiel in der FROM-Klausel verwendet werden.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In der SQL-API können Sie – im Gegensatz zu ANSI SQL – Bereichsabfragen für Eigenschaften gemischter Typen ausdrücken. Beispielsweise kann `grade` in einigen Elementen eine Zahl (z.B. `5`) und in anderen eine Zeichenfolge (z.B. `grade4`) sein. In diesen Fällen (etwa in JavaScript) gibt der Vergleich zwischen den beiden unterschiedlichen Typen `Undefined` zurück, sodass das Element übersprungen wird.

> [!TIP]
> Für schnellere Ausführungszeiten von Abfragen erstellen Sie eine Indizierungsrichtlinie, die einen Bereichsindextyp für alle von der BETWEEN-Klausel gefilterten numerischen Eigenschaften oder Pfade verwendet.

## <a name="in-keyword"></a>IN-Schlüsselwort

Mit dem Schlüsselwort IN können Sie überprüfen, ob ein angegebener Wert mit einem Wert in einer Liste übereinstimmt. Die folgende Abfrage gibt beispielsweise alle Familienelemente zurück, in denen `id` den Text `WakefieldFamily` oder `AndersenFamily` enthält.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

Im folgenden Beispiel werden alle Elemente zurückgegeben, in denen der Bundesstaat einem der angegebenen Werte entspricht:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>*-Operator

Mit dem Sonderoperator * wird das gesamte Element unverändert projiziert. Wenn dieser Operator verwendet wird, dürfen keine weiteren projizierten Felder existieren. Eine Abfrage wie `SELECT * FROM Families f` ist gültig, aber `SELECT VALUE * FROM Families f` und `SELECT *, f.id FROM Families f` sind nicht gültig. Bei der [ersten Abfrage in diesem Artikel](#query-the-json-items) wurde der *-Operator verwendet. 

## <a name="-and--operators"></a>? und ?? – Operatoren

Sie können ternäre (?) und koaleszierte (??) Operatoren wie in den Programmiersprachen C# und JavaScript zum Erstellen von bedingten Ausdrücken verwenden. 

Mit dem Operator ? können Sie JSON-Eigenschaften Ad hoc erstellen. Beispielsweise werden mit der folgenden Abfrage die Klassenstufen in `elementary` oder `other` klassifiziert:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Sie können auch Aufrufe des Operators ? wie in der folgenden Abfrage verschachteln: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Wie bei anderen Abfrageoperatoren schließt der Operator ? Elemente aus, wenn die referenzierten Eigenschaften fehlen oder unterschiedliche Typen miteinander verglichen werden.

Der Operator ?? kann bei Abfragen für teilweise strukturiert oder gemischte Datentypen zur effizienten Prüfung des Vorkommens einer Eigenschaft in einem Element verwendet werden. Die folgende Abfrage gibt z.B. `lastName` zurück, falls vorhanden, oder `surname` zurück, falls `lastName` nicht vorhanden ist.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP-Operator

Das Schlüsselwort TOP gibt die ersten `N` Abfrageergebnisse in einer nicht definierten Reihenfolge zurück. Es empfiehlt sich, TOP mit der ORDER BY-Klausel zu verwenden, um die Ergebnisse auf die ersten `N` geordneten Werte zu beschränken. Die Kombination dieser beiden Klauseln ist die einzige Möglichkeit, um zuverlässig anzugeben, welche Zeilen von TOP betroffen sind.

Sie können TOP mit einem konstanten Wert wie im folgenden Beispiel oder mit einem Variablenwert mithilfe von parametrisierten Abfragen verwenden. Weitere Informationen finden Sie im Abschnitt [Parametrisierte Abfragen](#parameterized-queries).

```sql
    SELECT TOP 1 *
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a id="OrderByClause"></a>ORDER BY-Klausel

Wie bei ANSI SQL können Sie eine optionale ORDER BY-Klausel in Abfragen einbinden. Das optionale ASC- oder DESC-Argument gibt an, ob Ergebnisse in aufsteigender oder absteigender Reihenfolge abgerufen werden sollen. ASC ist die Standardeinstellung.

Hier sehen Sie beispielsweise eine Abfrage, mit der Familien sortiert nach dem Namen des Wohnorts in aufsteigender Reihenfolge abgerufen werden:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Mit der folgenden Abfrage wird die `id` von Familien in der Reihenfolge abgerufen, in der das Element erstellt wurde. Das Element `creationDate` ist eine Zahl, die die *Epoche* repräsentiert, also die seit dem 1. Januar 1970 verstrichene Zeit in Sekunden.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Darüber hinaus können Sie nach mehreren Eigenschaften sortieren. Eine Abfrage, die nach mehreren Eigenschaften sortiert, erfordert einen [zusammengesetzten Index](index-policy.md#composite-indexes). Betrachten Sie die folgende Abfrage:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Diese Abfrage ruft die `id` der Familien in aufsteigender Reihenfolge nach dem Namen der Stadt ab. Wenn mehrere Elemente den gleichen Ort aufweisen, sortiert die Abfrage in absteigender Reihenfolge nach dem `creationDate`.

## <a id="OffsetLimitClause"></a>OFFSET LIMIT-Klausel

OFFSET LIMIT ist eine optionale Klausel zum Überspringen einer bestimmten Anzahl von Werten aus der Abfrage. In der OFFSET LIMIT-Klausel müssen sowohl für OFFSET als auch für LIMIT Werte angegeben werden. Derzeit wird diese Klausel nur für Abfragen in einer einzelnen Partition unterstützt, partitionsübergreifende Abfragen unterstützen sie noch nicht. 

Wenn OFFSET LIMIT in Verbindung mit einer ORDER BY-Klausel verwendet wird, wird das Resultset erstellt, indem zunächst Werte übersprungen und dann die sortierten Werte angenommen werden. Wenn keine ORDER BY-Klausel verwendet wird, gilt eine deterministische Reihenfolge der Werte.

Dies ist z. B. eine Abfrage, die den ersten Wert überspringt und den zweiten Wert (in der Reihenfolge der Wohnortnamen) zurückgibt:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Dies ist eine Abfrage, die den ersten Wert überspringt und den zweiten Wert (ohne Sortierung) zurückgibt:

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```




## <a name="scalar-expressions"></a>Skalare Ausdrücke

Die SELECT-Klausel unterstützt skalare Ausdrücke wie Konstanten, arithmetische Ausdrücke und logische Ausdrücke. Die folgende Abfrage verwendet einen skalaren Ausdruck:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "$1": 1.33333
    }]
```

Das Ergebnis des skalaren Ausdrucks in der folgenden Abfrage ist ein boolescher Wert:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>Objekt- und Arrayerstellung

Eine Schlüsselfunktion der SQL-API ist die Array- und Objekterstellung. Im vorherigen Beispiel wurde ein neues JSON-Objekt (`AreFromSameCityState`) erstellt. Sie können auch Arrays erstellen wie im folgenden Beispiel gezeigt:


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

Die folgende SQL-Abfrage ist ein weiteres Beispiel für die Verwendung von Arrays in Unterabfragen. Diese Abfrage ruft alle unterschiedlichen Vornamen von Kindern in einem Array ab.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```


## <a id="Iteration"></a>Iteration

Die SQL-API unterstützt eine Iteration über JSON-Arrays mit einem neuen Konstrukt, das mit dem IN-Schlüsselwort in der FROM-Quelle hinzugefügt wurde. Siehe folgendes Beispiel:

```sql
    SELECT *
    FROM Families.children
```

Die Ergebnisse sind wie folgt:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

Die nächste Abfrage führt eine Iteration über `children` im Container `Families` aus. Das Ausgabearray unterscheidet sich von der vorherigen Abfrage. Dieses Beispiel teilt `children` und fügt das Ergebnis in ein einzelnes Array ein:  

```sql
    SELECT *
    FROM c IN Families.children
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Sie können die einzelnen Einträge im Array weiter filtern wie im folgenden Beispiel gezeigt:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "givenName": "Lisa"
    }]
```

Sie können auch das Ergebnis einer Array-Iteration aggregieren. Die folgende Abfrage zählt beispielsweise die Anzahl der Kinder in allen Familien:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>Joins

In einer relationalen Datenbank sind tabellenübergreifende Verknüpfungen die logische Grundvoraussetzung für die Planung normalisierter Schemas. Im Gegensatz dazu verwendet die SQL-API das denormalisierte Datenmodell schemafreier Elemente (das logische Äquivalent einer *Selbstverknüpfung*).

Die Sprache unterstützt die Syntax `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Diese Abfrage gibt einen Satz von Tupeln mit `N` Werten zurück. Jedes Tupel enthält Werte, die durch Iteration aller Containeraliase über deren jeweilige Sätze entstanden sind. Anders ausgedrückt: Dieses Abfrage generiert ein vollständiges Kreuzprodukt der an der Verknüpfung beteiligten Sätze.

Die folgenden Beispiele veranschaulichen die Funktionsweise der JOIN-Klausel. Das Ergebnis im folgenden Beispiel ist leer, da das Kreuzprodukt der einzelnen Quellelemente und einem leeren Satz leer ist:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Es wird folgendes Ergebnis ausgegeben:

```json
    [{
    }]
```

Im folgenden Beispiel ist die Verknüpfung ein Kreuzprodukt zwischen zwei JSON-Objekten, dem Elementstamm `id` und dem Unterstamm `children`. Die Tatsache, dass `children` ein Array ist, macht für die Verknüpfung keinen Unterschied, da ein einzelner Stammknoten verwendet wird, nämlich das Array `children`. Das Ergebnis enthält nur zwei Ergebnisse, da das Kreuzprodukt der einzelnen Elemente mit dem Array jeweils genau ein Element ergibt.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Das folgende Beispiel zeigt eine gewöhnlichere Verknüpfung:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

Die FROM-Quelle der JOIN-Klausel ist ein Iterator. Der Ablauf im vorherigen Beispiel ist also wie folgt:  

1. Alle untergeordneten Elemente `c` im Array erweitern.
2. Kreuzprodukt mit dem Stammknoten des Elements `f` mit den einzelnen untergeordneten Elementen vom Typ `c` anwenden, die im ersten Schritt vereinfacht wurden.
3. Zuletzt die `id`-Eigenschaft des Stammobjekts `f` alleine projizieren.

Das erste Element (`AndersenFamily`) enthält nur ein einzelnes Element `children`. Daher enthält der Ergebnissatz nur ein einzelnes Objekt. Das zweite Element (`WakefieldFamily`) enthält zwei Elemente vom Typ `children`. Daher erzeugt das Kreuzprodukt zwei Objekte, eines für jedes `children`-Element. Die Stammfelder sind in beiden Elementen gleich (wie bei einem Kreuzprodukt zu erwarten).

Der wahre Nutzen der JOIN-Klausel ist die Bildung von Tupeln aus dem Kreuzprodukt auf eine Weise, die auf anderen Wegen schwer zu projizieren ist. Im folgenden Beispiel werden Filter auf die Kombination von Tupeln angewendet, mit denen Benutzer eine Bedingung auswählen können, die von der Gesamtheit der Tupel erfüllt sein muss.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Die folgende Erweiterung des vorherigen Beispiels zeigt eine doppelte Verknüpfung. Sie können das Kreuzprodukt als eine Form des folgenden Pseudocodes betrachten:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` hat ein Kind mit einem Haustier. Das Kreuzprodukt ergibt also eine Zeile (1\*1\*1) für diese Familie. `WakefieldFamily` hat zwei Kinder, von denen nur eines Haustiere hat, allerdings hat dieses Kind zwei Haustiere. Das Kreuzprodukt für diese Familie ergibt also 1\*1\*2 = 2 Zeilen.

Das nächste Beispiel enthält einen zusätzlichen Filter für `pet`, durch den alle Tupel ausgeschlossen werden, bei denen der Haustiername nicht `Shadow` lautet. Sie können Tupel aus Arrays bilden, beliebige Elemente der Tupel filtern und beliebige Kombinationen der Elemente projizieren.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>Benutzerdefinierte Funktionen (User Defined Functions, UDFs)

Die SQL-API bietet Unterstützung für benutzerdefinierte Funktionen (UDFs). Bei skalaren UDFs können null oder mehrere Argumente übergeben und ein einziges Argumentergebnis zurückgegeben werden. Die API überprüft, ob jedes Argument ein gültiger JSON-Wert ist.  

Die API erweitert die SQL-Syntax, um benutzerdefinierte Anwendungslogik mit benutzerdefinierten Funktionen (UDFs) zu unterstützen. Sie können UDFs mit der SQL-API registrieren und in SQL-Abfragen referenzieren. Genau genommen sind UDFs für den Aufruf in Abfragen konzipiert. Als Nebeneffekt haben UDFs im Gegensatz zu anderen JavaScript-Typen, z.B. gespeicherte Prozeduren und Trigger, keinen Zugriff auf das Kontextobjekt. Abfragen sind schreibgeschützt und können entweder auf primären oder sekundären Replikaten ausgeführt werden. UDFs wurden im Gegensatz zu anderen JavaScript-Typen für die Ausführung auf sekundären Replikaten entwickelt.

Im folgenden Beispiel wird eine UDF unter einem Elementcontainer in der Cosmos DB-Datenbank registriert. Im Beispiel wird eine UDF mit dem Namen `REGEX_MATCH` erstellt. Es werden die beiden JSON-Zeichenfolgenwerte `input` und `pattern` akzeptiert, und es wird überprüft, ob der erste Wert mit dem im zweiten Wert angegebenen Muster übereinstimmt. Hierfür wird die JavaScript-Funktion `string.match()` verwendet.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Verwenden Sie nun diese UDF in einer Abfrageprojektion. Sie müssen UDFs mit dem Präfix `udf.` unter Berücksichtigung der Groß-/Kleinschreibung qualifizieren, wenn sie innerhalb von Abfragen aufgerufen werden.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Die Ergebnisse sind wie folgt:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Sie können die UDF, die mit dem Präfix `udf.` qualifiziert ist, innerhalb eines Filters verwenden, wie es im folgenden Beispiel gezeigt wird:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Die Ergebnisse sind wie folgt:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

UDFs sind gültige skalare Ausdrücke, die Sie sowohl in Projektionen als auch in Filtern verwenden können.

Zum Erweitern des Funktionsumfangs von UDFs sehen Sie hier ein weiteres Beispiel mit konditionaler Logik:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

Hier sehen Sie ein Verwendungsbeispiel für die UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Wenn die von den UDF-Parametern referenzierten Eigenschaften nicht im JSON-Wert verfügbar sind, wird der Parameter als „Undefined“ betrachtet und der UDF-Aufruf übersprungen. Wenn das Ergebnis der UDF „Undefined“ ist, wird es ebenfalls nicht in das Ergebnis aufgenommen.

Wie im vorherigen Beispiel gezeigt, vereinen UDFs den Funktionsumfang der JavaScript-Sprache mit der SQL-API. UDFs bieten eine umfassende, programmierbare Schnittstelle für komplexe verfahrensorientierte und konditionale Logik mithilfe der Fähigkeiten der integrierten JavaScript-Laufzeit. Die SQL-API stellt den UDFs die Argumente für jedes Quellelemente in der aktuellen Ausführungsphase der WHERE- oder SELECT-Klausel bereit. Das Ergebnis wird nahtlos in die Gesamt-Ausführungspipeline aufgenommen. UDFs sind also hervorragende Werkzeuge, um komplexe Geschäftslogik als Teil der Abfragen zu integrieren.

## <a id="Aggregates"></a>Aggregatfunktionen

Aggregatfunktionen führen eine Berechnung für eine Gruppe von Werten in der SELECT-Klausel durch und geben einen einzelnen Wert zurück. Bei der folgenden Abfrage wird beispielsweise die Anzahl von Elementen im `Families`-Container zurückgegeben:

```sql
    SELECT COUNT(1)
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [{
        "$1": 2
    }]
```

Sie können auch nur den skalaren Wert des Aggregats zurückgeben, indem Sie das Schlüsselwort VALUE verwenden. Bei dieser Abfrage wird beispielsweise die Anzahl der Werte als einzelne Zahl zurückgegeben:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Die Ergebnisse sind wie folgt:

```json
    [ 2 ]
```

Sie können auch Aggregationen mit Filtern kombinieren. Bei der folgenden Abfrage wird beispielsweise die Anzahl von Elementen mit dem Bundesstaat `WA` in der Adresse zurückgegeben.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Die Ergebnisse sind wie folgt:

```json
    [ 1 ]
```

Die SQL-API unterstützt die folgenden Aggregatfunktionen. SUM und AVG werden für numerische Werte ausgeführt. COUNT, MIN und MAX werden für Zahlen, Zeichenfolgen, boolesche Werte und NULL-Werte verwendet.

| Funktion | BESCHREIBUNG |
|-------|-------------|
| COUNT | Gibt die Anzahl der Elemente im Ausdruck zurück. |
| SUM   | Gibt die Summe aller Werte im Ausdruck zurück. |
| MIN   | Gibt den Mindestwert im Ausdruck zurück. |
| MAX   | Gibt den maximalen Wert im Ausdruck zurück. |
| DURCHSCHN.   | Gibt den Durchschnitt aller Werte im Ausdruck zurück. |

Sie können auch die Ergebnisse einer Array-Iteration aggregieren. Weitere Informationen finden Sie im Abschnitt [Iteration](#Iteration).

> [!NOTE]
> Im Daten-Explorer des Azure-Portals geben Aggregationsabfragen möglicherweise die teilweise aggregierten Ergebnisse für nur eine Abfrageseite zurück. Das SDK erzeugt einen einzelnen kumulativen Wert für alle Seiten. Zum Durchführen von Aggregationsabfragen mithilfe von Code benötigen Sie .NET SDK 1.12.0, .NET Core SDK 1.1.0 oder Java SDK 1.9.5 oder höher.
>

## <a id="BuiltinFunctions"></a>Integrierte Funktionen

Cosmos DB unterstützt auch eine Reihe von integrierten Funktionen für häufige Vorgänge, die Sie wie benutzerdefinierte Funktionen (User-Defined Functions, UDFs) in Abfragen verwenden können.

| Funktionsgruppe | Vorgänge |
|---------|----------|
| Mathematische Funktionen | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Funktionen für die Typprüfung | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Zeichenfolgenfunktionen | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Arrayfunktionen | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH und ARRAY_SLICE |
| Räumliche Funktionen | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Wenn Sie derzeit eine benutzerdefinierte Funktion (UDF) verwenden, für die jetzt eine entsprechende integrierte Funktion verfügbar ist, kann diese integrierte Funktion schneller und effizienter ausgeführt werden.

Der Hauptunterschied zwischen Cosmos DB-Funktionen und ANSI SQL-Funktionen besteht darin, dass sich Cosmos DB-Funktionen bestens für schemalose Daten sowie Daten mit gemischtem Schema eignen. Wenn beispielsweise eine Eigenschaft fehlt oder ein nicht numerischer Wert wie `unknown` vorhanden ist, wird das Element übersprungen, anstatt einen Fehler zurückzugeben.

### <a name="mathematical-functions"></a>Mathematische Funktionen

Jede mathematische Funktion führt eine Berechnung durch, basierend auf Eingabewerten, die als Argument bereitgestellt werden, und gibt einen numerischen Wert zurück. Im Folgenden finden Sie eine Tabelle der unterstützten integrierten mathematischen Funktionen.

| Verwendung | BESCHREIBUNG |
|----------|--------|
| ABS (num_expr) | Gibt den absoluten (positiven) Wert des angegebenen numerischen Ausdrucks zurück. |
| CEILING (num_expr) | Gibt den kleinsten ganzzahligen Wert zurück, der größer oder gleich dem angegebenen numerischen Ausdruck ist. |
| FLOOR (num_expr) | Gibt den größten ganzzahligen Wert zurück, der kleiner oder gleich dem angegebenen numerischen Ausdruck ist. |
| EXP (num_expr) | Gibt den Exponenten des angegebenen numerischen Ausdrucks zurück. |
| LOG (num_expr, base) | Gibt den natürlichen Logarithmus des angegebenen numerischen Ausdrucks oder den Logarithmus basierend auf der angegebenen Basis zurück. |
| LOG10 (num_expr) | Gibt den dekadischen Logarithmuswert des angegebenen numerischen Ausdrucks zurück. |
| ROUND (num_expr) | Gibt einen numerischen Wert zurück, gerundet auf den nächsten ganzzahligen Wert. |
| TRUNC (num_expr) | Gibt einen numerischen Wert zurück, gekürzt auf den nächsten ganzzahligen Wert. |
| SQRT (num_expr) | Gibt die Quadratwurzel des angegebenen numerischen Ausdrucks zurück. |
| SQUARE (num_expr) | Gibt das Quadrat des angegebenen numerischen Ausdrucks zurück. |
| POWER (num_expr, num_expr) | Gibt die Potenz des angegebenen numerischen Ausdrucks über den angegebenen Wert zurück. |
| SIGN (num_expr) | Gibt den Vorzeichenwert (-1, 0, 1) des angegebenen numerischen Ausdrucks zurück. |
| ACOS (num_expr) | Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Kosinus der angegebene numerische Ausdruck ist. Wird auch als Arkuskosinus bezeichnet. |
| ASIN (num_expr) | Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Sinus der angegebene numerische Ausdruck ist. Diese Funktion wird auch als Arkussinus bezeichnet. |
| ATAN (num_expr) | Gibt den Winkel in „rad“ (Bogenmaß) zurück, dessen Tangens der angegebene numerische Ausdruck ist. Diese Funktion wird auch als Arkustangens bezeichnet. |
| ATN2 (num_expr) | Gibt den Winkel in „rad“ (Bogenmaß) zwischen der positiven X-Achse und dem Strahl vom Ursprung zum Punkt (y, x) zurück, wobei x und y die Werte der beiden angegebenen float-Ausdrücke sind. |
| COS (num_expr) | Gibt den trigonometrischen Kosinus des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen Ausdruck zurück. |
| COT (num_expr) | Gibt den trigonometrischen Kotangens des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen numerischen Ausdruck zurück. |
| DEGREES (num_expr) | Gibt den entsprechenden Winkel in Grad für einen in „rad“ (Bogenmaß) angegebenen Winkel zurück. |
| PI () | Gibt den konstanten Wert von PI zurück. |
| RADIANS (num_expr) | Gibt das Bogenmaß zurück, wenn ein numerischer Ausdruck in Grad eingegeben wird. |
| SIN (num_expr) | Gibt den trigonometrischen Sinus des angegebenen Winkels in „rad“ (Bogenmaß) im angegebenen Ausdruck zurück. |
| TAN (num_expr) | Gibt den Tangens des Eingabeausdrucks im angegebenen Ausdruck zurück. |

Sie können Abfragen wie im folgenden Beispiel ausführen:

```sql
    SELECT VALUE ABS(-4)
```

Es wird folgendes Ergebnis ausgegeben:

```json
    [4]
```

### <a name="type-checking-functions"></a>Funktionen für die Typprüfung

Mit den Funktionen für die Typprüfung können Sie den Typ eines Ausdrucks in einer SQL-Abfrage prüfen. Anhand von Typprüfungsfunktionen können Sie während der Ausführung die Typen von Eigenschaften innerhalb von Elementen ermitteln, wenn diese variabel oder unbekannt sind. Es folgt eine Tabelle der unterstützten integrierten Typprüfungsfunktionen:

| **Verwendung** | **Beschreibung** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Array" ist. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "boolesch" ist. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Null" ist. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Zahl" ist. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "JSON-Objekt" ist. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | Gibt einen booleschen Wert zurück, der angibt, ob der Wert vom Typ "Zeichenfolge" ist. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | Gibt einen booleschen Wert zurück, um anzugeben, ob der Typ des Werts eine Zeichenfolge, eine Zahl, ein boolescher Wert oder Null ist. |

Mithilfe dieser Funktionen können Sie Abfragen wie im folgenden Beispiel ausführen:

```sql
    SELECT VALUE IS_NUMBER(-4)
```

Es wird folgendes Ergebnis ausgegeben:

```json
    [true]
```

### <a name="string-functions"></a>Zeichenfolgenfunktionen

Die folgenden Skalarfunktionen führen einen Vorgang für einen Zeichenfolgen-Eingabewert durch und geben eine Zeichenfolge, einen numerischen Wert oder einen booleschen Wert zurück. Diese Tabelle enthält integrierte Zeichenfolgenfunktionen:

| Verwendung | BESCHREIBUNG |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | Gibt die Anzahl der Zeichen im angegebenen Zeichenfolgenausdruck zurück. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | Gibt eine Zeichenfolge zurück, die das Ergebnis der Verkettung von zwei oder mehr Zeichenfolgenwerten darstellt. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | Gibt einen Teil eines Zeichenfolgenausdrucks zurück. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten beginnt. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten endet. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck den zweiten enthält. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | Gibt die Anfangsposition des ersten Vorkommens des zweiten Zeichenfolgenausdrucks innerhalb des ersten angegebenen Zeichenfolgenausdrucks zurück, oder -1, wenn die Zeichenfolge nicht gefunden wird. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | Gibt den linken Teil einer Zeichenfolge mit der angegebenen Anzahl von Zeichen zurück. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | Gibt den rechten Teil einer Zeichenfolge mit der angegebenen Anzahl von Zeichen zurück. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | Gibt einen Zeichenfolgenausdruck zurück, nachdem vorangestellte Leerzeichen entfernt wurden. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | Gibt einen Zeichenfolgenausdruck zurück, nachdem alle nachgestellten Leerzeichen entfernt wurden. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | Gibt eine Zeichenfolge zurück, nachdem Großbuchstaben in Kleinbuchstaben konvertiert wurden. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | Gibt eine Zeichenfolge zurück, nachdem Kleinbuchstaben in Großbuchstaben konvertiert wurden. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | Ersetzt alle Vorkommen eines angegebenen Zeichenfolgenwerts durch einen anderen Zeichenfolgenwert. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | Wiederholt einen Zeichenfolgenwert mit der angegebenen Häufigkeit. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | Gibt einen Zeichenfolgenwert in umgekehrter Reihenfolge zurück. |

Mithilfe dieser Funktionen können Sie Abfragen wie die folgende ausführen, die die Familien-`id` in Großbuchstaben zurückgibt:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

Die Ergebnisse sind wie folgt:

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

Sie können auch Zeichenfolgen wie in diesem Beispiel verketten:

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

Sie können auch Zeichenfolgenfunktionen in der WHERE-Klausel zum Filtern von Ergebnissen wie im folgenden Beispiel verwenden:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>Arrayfunktionen

Die folgenden Skalarfunktionen führen einen Vorgang für einen Arrayeingabewert durch und geben einen numerischen Wert, booleschen Wert oder Arraywert zurück. Diese Tabelle enthält integrierte Arrayfunktionen:

| Verwendung | BESCHREIBUNG |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |Gibt die Anzahl der Elemente des angegebenen Arrayausdrucks zurück. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |Gibt ein Array zurück, das das Ergebnis der Verkettung von zwei oder mehr Arraywerten darstellt. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |Gibt einen booleschen Wert zurück, um anzugeben, ob das Array den angegebenen Wert enthält. Kann angeben, ob es sich um eine vollständige oder teilweise Übereinstimmung handelt. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |Gibt einen Teil eines Arrayausdrucks zurück. |

Verwenden Sie Arrayfunktionen, um Arrays in JSON zu bearbeiten. Mit dieser Abfrage werden beispielsweise alle Element-`id`s zurückgegeben, in denen ein Eintrag für `parents` den Text `Robin Wakefield` aufweist: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

Es wird folgendes Ergebnis ausgegeben:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Sie können ein Teilfragment für übereinstimmende Elemente innerhalb des Arrays angeben. Die folgende Abfrage sucht alle Element-`id`s, in denen `parents` für `givenName` den Text `Robin` aufweisen:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

Es wird folgendes Ergebnis ausgegeben:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Es folgt ein weiteres Beispiel für die Verwendung von ARRAY_LENGTH. Hierbei wird die Anzahl von `children` pro Familie abgerufen:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

Die Ergebnisse sind wie folgt:

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>Räumliche Funktionen

Cosmos DB unterstützt die folgenden integrierten OGC-Funktionen (Open Geospatial Consortium ) für das Abfragen von Geodaten: 

| Verwendung | BESCHREIBUNG |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | Gibt den Abstand zwischen den beiden GeoJSON-`Point`-, `Polygon`- oder `LineString`-Ausdrücken zurück. |
| ST_WITHIN (point_expr, polygon_expr) | Gibt einen booleschen Ausdruck zurück, der angibt, ob das erste GeoJSON-Objekt (`Point`, `Polygon` oder `LineString`) innerhalb des zweiten GeoJSON-Objekts (`Point`, `Polygon` oder `LineString`) enthalten ist. |
| ST_INTERSECTS (spatial_expr, spatial_expr) | Gibt einen booleschen Ausdruck, der angibt, ob die beiden angegebenen GeoJSON-Objekte (`Point`, `Polygon` oder `LineString`) sich überschneiden. |
| ST_ISVALID | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene GeoJSON-`Point`-, `Polygon`- oder `LineString`-Ausdruck gültig ist. |
| ST_ISVALIDDETAILED | Gibt einen JSON-Wert mit einem booleschen Wert zurück, wenn der angegebene GeoJSON-`Point`-, `Polygon`- oder `LineString`-Ausdruck gültig ist. Falls ungültig, wird der Grund als Zeichenfolge zurückgegeben. |

Sie können räumliche Funktionen verwenden, um Entfernungsabfragen auf räumliche Daten anzuwenden. Die folgende Abfrage gibt beispielsweise alle Familienelemente zurück, die sich im Umkreis von 30 km von einer angegebenen Position befinden. Dazu wird die integrierte ST_DISTANCE-Funktion verwendet:

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

Es wird folgendes Ergebnis ausgegeben:

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Weitere Informationen zur Unterstützung von Geodaten in Cosmos DB finden Sie unter [Arbeiten mit Geodaten in Azure Cosmos DB](geospatial.md). 

## <a name="parameterized-queries"></a>Parametrisierte Abfragen

Cosmos DB unterstützt Abfragen mit Parametern, die durch die bekannte @-Notation ausgedrückt werden. Parametrisiertes SQL bietet stabile Fehlerbehandlung und Schutz von Benutzereingaben und verhindert eine versehentliche Offenlegung von Daten durch SQL-Injektion.

Sie können z.B. eine Abfrage erstellen, die `lastName` und `address.state` als Parameter verwendet, und sie für unterschiedliche `lastName`- und `address.state`-Werte auf Grundlage von Benutzereingaben ausführen.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Sie können diese Anforderung dann als parametrisierte JSON-Abfrage wie die folgende an Cosmos DB senden:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

Im folgenden Beispiel wird das Argument für TOP mit einer parametrisierten Abfrage festgelegt: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parameterwerte können alle gültigen JSON-Werte sein: Zeichenfolgen, Zahlen, boolesche Werte, Null, Arrays oder verschachteltes JSON. Da Cosmos DB schemalos ist, werden Parameter für keinen Typ überprüft.

## <a id="JavaScriptIntegration"></a>JavaScript-Integration

Azure Cosmos DB bietet ein Programmiermodell zur Ausführung JavaScript-basierter Anwendungslogik direkt für Container über gespeicherte Prozeduren und Trigger. Dieses Modell unterstützt Folgendes:

* Leistungsfähige transaktionale CRUD-Vorgänge und Abfragen für Elemente in einem Container dank umfassender Integration der JavaScript-Laufzeit in die Datenbank-Engine
* Eine natürliche Modellierung von Kontrollfluss, Variablen-Bereichssteuerung, Zuweisung und Integration der Ausnahmebehandlung für Datenbanktransaktionen 

Weitere Informationen zur JavaScript-Integration in Azure Cosmos DB finden Sie im Abschnitt [Serverseitige JavaScript-API](#JavaScriptServerSideApi).

### <a name="operator-evaluation"></a>Operatorauswertung

Cosmos DB ist eine JSON-Datenbank und enthält daher Parallelen zu JavaScript-Operatoren und Auswertungslogik. Cosmos DB versucht, die JavaScript-Logik für die JSON-Unterstützung zu erhalten, doch weicht die Auswertung von Operationen in manchen Fällen davon ab.

In der SQL-API ist der Typ eines Werts im Gegensatz zu herkömmlichem SQL oft nicht bekannt, bis die API den Wert aus der Datenbank abruft. Um effiziente Abfragen zu ermöglichen, haben die meisten Operatoren strikte Typanforderungen.

Im Gegensatz zu JavaScript führt die SQL-API keine impliziten Konvertierungen durch. Eine Abfrage wie `SELECT * FROM Person p WHERE p.Age = 21` entspricht beispielsweise Elementen, die eine `Age`-Eigenschaft mit dem Wert `21` enthalten. Andere Elemente, deren `Age`-Eigenschaft möglicherweise unendlichen Variationen wie `twenty-one`, `021` oder `21.0` entspricht, werden nicht zurückgegeben. Bei JavaScript würden Zeichenfolgenwerte dagegen je nach Operator (z.B. `==`) implizit in Zahlen umgewandelt. Dieses Verhalten der SQL-API ist wichtig für die effiziente Indizierung.

## <a id="ExecutingSqlQueries"></a>Ausführen von SQL-Abfragen

Die Cosmos DB-REST-API kann mit jeder Sprache aufgerufen werden, die HTTP/HTTPS-Anfragen unterstützt. Cosmos DB bietet außerdem Programmierbibliotheken für die Programmiersprachen .NET, Node.js, JavaScript und Python. Die REST-API und Bibliotheken unterstützen allesamt SQL-Abfragen, und das .NET SDK unterstützt auch [LINQ-Abfragen](#Linq).

Die folgenden Beispiele zeigen, wie Sie eine Abfrage erstellen und auf einem Cosmos DB-Datenbankkonto ausführen können.

### <a id="RestAPI"></a>REST-API

Cosmos DB bietet ein RESTful-Programmiermodell über HTTP. Das Ressourcenmodell besteht aus einem Satz von Ressourcen in einem Datenbankkonto, das von einem Azure-Abonnement bereitgestellt wird. Das Datenbankkonto besteht aus einem Satz von *Datenbanken*, die jeweils mehrere *Container* enthalten können, die wiederum *Elemente*, UDFs und andere Ressourcentypen enthalten. Jede Cosmos DB-Ressource kann über einen logischen und beständigen URI adressiert werden. Ein Satz von Ressourcen wird als *Feed* bezeichnet. 

Die Interaktion mit diesen Ressourcen erfolgt über die HTTP-Verben `GET`, `PUT`, `POST` und `DELETE` mit deren Standardinterpretation. Verwenden Sie `POST` zum Erstellen einer neuen Ressource, Ausführen einer gespeicherten Prozedur oder Ausgeben einer Cosmos DB-Abfrage. Abfragen werden immer als schreibgeschützte Operationen ohne Nebeneffekte ausgeführt.

Die folgenden Beispiele zeigen einen `POST`-Vorgang für eine SQL-API-Abfrage der Beispielelemente. Die Abfrage enthält einen einfachen Filter auf die JSON-Eigenschaft `name`. Die Header `x-ms-documentdb-isquery` und Content-Type: `application/query+json` geben an, dass es sich bei diesem Vorgang um eine Abfrage handelt. Ersetzen Sie `mysqlapicosmosdb.documents.azure.com:443` durch den URI für Ihr Cosmos DB-Konto.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Die Ergebnisse sind wie folgt:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

Die nächste, komplexere Abfrage gibt mehrere Ergebnisse aus einer Verknüpfung zurück:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

Die Ergebnisse sind wie folgt: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Wenn die Abfrageergebnisse nicht auf eine einzelne Seite passen, gibt die REST-API ein Fortsetzungstoken im `x-ms-continuation-token`-Antwortheader zurück. Clients können einzelne Ergebnisseiten abfragen, indem sie den Header in den nachfolgenden Abfragen angeben. Sie können auch die Anzahl der Ergebnisse pro Seite über den `x-ms-max-item-count`-Header steuern. 

Wenn eine Abfrage eine Aggregatfunktion wie COUNT aufweist, gibt die Abfrageseite möglicherweise einen teilweise aggregierten Wert für nur eine Ergebnisseite zurück. Clients müssen eine Aggregation auf zweiter Ebene für diese Ergebnisse ausführen, um die Endergebnisse zu erzeugen, z.B. die Summe für die auf den einzelnen Seiten zurückgegebenen Anzahlen, um die Gesamtanzahl zurückzugeben.

Zur Verwaltung der Datenkonsistenzrichtlinie für Abfragen verwenden Sie den `x-ms-consistency-level`-Header wie in allen REST-API-Anforderungen. Für die Sitzungskonsistenz muss außerdem der letzte `x-ms-session-token` -Cookie-Header in der Abfrageanforderung wiederholt werden. Die Indizierungsrichtlinie des abgefragten Containers kann auch die Konsistenz der Abfrageergebnisse beeinflussen. Mit den Standardeinstellungen für Indizierungsrichtlinien stimmt der Index eines Containers immer mit dem aktuellen Elementinhalt überein, und die Abfrageergebnisse geben die Daten mit der gewünschten Konsistenz zurück. Weitere Informationen finden Sie unter [Konsistenzebenen in Azure Cosmos DB][consistency-levels].

Wenn die konfigurierte Indizierungsrichtlinie des Containers die angegebene Abfrage nicht unterstützen kann, gibt der Azure Cosmos DB-Server den Fehler 400 (unzulässige Anforderung) zurück. Diese Fehlermeldung wird bei Abfragen mit Pfaden zurückgegeben, die ausdrücklich von der Indizierung ausgeschlossen sind. Sie können den `x-ms-documentdb-query-enable-scan`-Header angeben, damit die Abfrage einen Suchvorgang durchführen darf, wenn kein Index verfügbar ist.

Sie können ausführliche Metriken zur Abfrageausführung abrufen, indem Sie den `x-ms-documentdb-populatequerymetrics`-Header auf `true` festlegen. Weitere Informationen finden Sie unter [SQL-Abfragemetriken für Azure Cosmos DB](sql-api-query-metrics.md).

### <a id="DotNetSdk"></a>C# (.NET SDK)

Das .NET SDK unterstützt Abfragen per LINQ und SQL. Das folgende Beispiel zeigt, wie Sie die vorherige Filterabfrage mit .NET ausführen:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Das folgende Beispiel vergleicht zwei Eigenschaften auf Gleichheit in den einzelnen Elementen und verwendet anonyme Projektionen.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

Das nächste Beispiel zeigt Verknüpfungen mithilfe von LINQ `SelectMany`.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

Der .NET-Client durchläuft automatisch alle Seiten der Abfrageergebnisse in den `foreach`-Blöcken wie im vorherigen Beispiel gezeigt. Die im Abschnitt zur [REST-API](#RestAPI) vorgestellten Abfrageoptionen sind auch im .NET SDK über die Klassen `FeedOptions` and `FeedResponse` in der `CreateDocumentQuery`-Methode verfügbar. Sie können die Anzahl der Seiten über die `MaxItemCount`-Einstellung steuern.

Sie können die Seitenaufteilung steuern, indem Sie mithilfe des `IQueryable`-Objekts ein `IDocumentQueryable`-Element erstellen, die `ResponseContinuationToken`-Werte lesen und sie anschließend als `RequestContinuationToken` in `FeedOptions` zurückgeben. Sie können `EnableScanInQuery` festlegen, um Suchvorgänge zu ermöglichen, wenn die Abfrage nicht von der konfigurierten Indizierungsrichtlinie unterstützt wird. Für partitionierte Container können Sie `PartitionKey` verwenden, um die Abfrage für eine einzelne Partition auszuführen, auch wenn Azure Cosmos DB dies automatisch aus dem Abfragetext extrahieren kann. Mit `EnableCrossPartitionQuery` können Sie Abfragen für mehrere Partitionen ausführen.

Weitere Beispiele für .NET mit Abfragen finden Sie unter den [Azure Cosmos DB-.NET-Beispielen](https://github.com/Azure/azure-cosmosdb-dotnet) in GitHub.

### <a id="JavaScriptServerSideApi"></a>Serverseitige JavaScript-API

Cosmos DB bietet ein Programmiermodell zur Ausführung JavaScript-basierter Anwendungslogik direkt für Container über gespeicherte Prozeduren und Trigger. Die auf Containerebene registrierte JavaScript-Logik kann anschließend Datenbankvorgänge für die Elemente der jeweiligen Container ausführen, die in ACID-Transaktionen der jeweiligen Umgebung gekapselt werden.

Das folgende Beispiel zeigt, wie Sie mithilfe von `queryDocuments` in der serverseitigen JavaScript-API Abfragen aus gespeicherten Prozeduren und Triggern heraus ausführen können:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>LINQ to SQL-API

LINQ ist ein .NET-Programmiermodell, das Berechnungen als Abfragen auf Objektstreams darstellt. Cosmos DB bietet eine clientseitige Bibliothek als Schnittstelle zu LINQ und vereinfacht die Konvertierung zwischen JSON- und .NET-Objekten sowie die Zuordnung einer Teilmenge von LINQ-Abfragen zu Cosmos DB-Abfragen.

Das folgende Diagramm zeigt die Architektur für die Unterstützung von LINQ-Abfragen in Cosmos DB. Mit dem Cosmos DB-Client können Sie ein `IQueryable`-Objekt erstellen, das den Cosmos DB-Abfrageanbieter direkt abfragt und die LINQ-Abfrage in eine Cosmos DB-Abfrage übersetzt. Dann übergeben Sie die Abfrage an den Cosmos DB-Server, der einen Ergebnissatz im JSON-Format abruft. Der JSON-Deserialisierer konvertiert die Ergebnisse clientseitig in einen Stream von .NET-Objekten.

![Architektur für die Unterstützung von LINQ-Abfragen mit der SQL-API: SQL-Syntax, JSON-Abfragesprache, Datenbankkonzepte und SQL-Abfragen][1]

### <a name="net-and-json-mapping"></a>.NET- und JSON-Zuordnung

Die Zuordnung zwischen .NET-Objekten und JSON-Elementen ist natürlich. Jedes Datenelementfeld wird einem JSON-Objekt zugeordnet. Dabei wird der Feldname dem *key*-Teil des Objekts und der Wert rekursiv dem *value*-Teil des Objekts zugeordnet. Der folgende Code ordnet die `Family`-Klasse einem JSON-Element zu und erstellt dann ein `Family`-Objekt:

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

Im vorherigen Beispiel wird das folgende JSON-Element erstellt:

```json
    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                "givenName": "Jesse",
                "gender": "female",
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller",
              "givenName": "Lisa",
              "gender": "female",
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>LINQ zu SQL-Übersetzung

Der Cosmos DB-Abfrageanbieter führt eine bestmögliche Zuordnung von LINQ-Abfragen in Cosmos DB-SQL-Abfragen durch. Bei der folgenden Beschreibung wird davon ausgegangen, dass Sie mit den Grundsätzen von LINQ vertraut sind.

Das Typsystem der Abfrageanbieter unterstützt nur die primitiven JSON-Typen: numerisch, boolesch, Zeichenfolge und Null. 

Der Abfrageanbieter unterstützt die folgenden skalaren Ausdrücke:

- Konstante Werte, einschließlich konstanter Werte der primitiven Datentypen zum Auswertungszeitpunkt der Abfrage.
  
- Indexausdrücke auf Eigenschaften/Arrays, die sich auf die Eigenschaft eines Objekts oder eines Arrayelements beziehen. Beispiel:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Arithmetische Ausdrücke, einschließlich allgemeiner arithmetischer Ausdrücke auf numerische und boolesche Werte. Eine vollständige Liste finden Sie in der [Azure Cosmos DB-SQL-Spezifikation](https://go.microsoft.com/fwlink/p/?LinkID=510612).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Vergleichsausdrücke für Zeichenfolgen, einschließlich Vergleiche von Zeichenfolgenwerten mit konstanten Zeichenfolgen.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Erstellungsausdrücke für Objekte/Arrays, die ein Objekt mit einem Verbundwerttyp oder anonymen Typ bzw. ein Array solcher Objekte zurückgeben. Diese Werte können geschachtelt werden.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>Unterstützte LINQ-Operatoren

Der im SQL .NET SDK enthaltene LINQ-Anbieter unterstützt die folgenden Operatoren:

- **Select**: Projektionen, einschließlich Objektkonstruktion, werden in SQL SELECT übersetzt.
- **Where**: Filter werden in SQL WHERE übersetzt und unterstützen die Übersetzung von `&&`, `||` und `!` in die SQL-Operatoren.
- **SelectMany**: Ermöglicht das Entladen von Arrays in die SQL-JOIN-Klausel. Wird zum Verketten oder Verschachteln von Ausdrücken zum Filtern von Arrayelementen verwendet.
- **OrderBy** und **OrderByDescending**: Übersetzung in ORDER BY mit ASC oder DESC.
- Die Operatoren **Count**, **Sum**, **Min**, **Max** und **Average** für die Aggregation und deren asynchrone Entsprechungen **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** und **AverageAsync**.
- **CompareTo**: Übersetzung in Bereichsvergleiche. Wird häufig für Zeichenfolgen verwendet, da sie nicht in .NET vergleichbar sind.
- **Take**: Übersetzung in SQL TOP, um Ergebnisse einer Abfrage einzuschränken.
- **Mathematische Funktionen**: Unterstützt die Übersetzung von `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`, `Tan` und `Truncate` aus .NET in die entsprechenden integrierten SQL-Funktionen.
- **Zeichenfolgenfunktionen**: Unterstützt die Übersetzung von `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd` und `TrimStart` aus .NET in die entsprechenden integrierten SQL-Funktionen.
- **Arrayfunktionen**: Unterstützt die Übersetzung von `Concat`, `Contains` und `Count` aus .NET in die entsprechenden integrierten SQL-Funktionen.
- **Geospatial-Erweiterungsfunktionen**: Unterstützt die Übersetzung aus den Stubmethoden `Distance`, `IsValid`, `IsValidDetailed` und `Within` in die entsprechenden integrierten SQL-Funktionen.
- **Erweiterungsfunktion für benutzerdefinierte Funktion**: Unterstützt die Übersetzung aus der Stubmethode `UserDefinedFunctionProvider.Invoke` in die entsprechende benutzerdefinierte Funktion.
- **Sonstiges**: Unterstützt die Übersetzung von `Coalesce` und bedingten Operatoren. Kann `Contains` je nach Kontext in die Zeichenfolge CONTAINS, ARRAY_CONTAINS oder SQL IN übersetzen.

### <a name="sql-query-operators"></a>SQL-Abfrageoperatoren

Die folgenden Beispiele zeigen, wie einige der Standard-LINQ-Abfrageoperatoren in Cosmos DB-Abfragen übersetzt werden.

#### <a name="select-operator"></a>Select-Operator

Die Syntax ist `input.Select(x => f(x))`, wobei `f` ein skalarer Ausdruck ist.

**Select-Operator, Beispiel 1:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select-Operator, Beispiel 2:** 

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select-Operator, Beispiel 3:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>SelectMany-Operator

Die Syntax lautet `input.SelectMany(x => f(x))`, wobei `f` ein skalarer Ausdruck ist, der einen Containertyp zurückgibt.

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Where-Operator

Die Syntax ist `input.Where(x => f(x))`, wobei `f` ein skalarer Ausdruck ist, der einen booleschen Wert zurückgibt.

**Where-Operator, Beispiel 1:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where-Operator, Beispiel 2:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>Zusammengesetzte SQL-Abfragen

Sie können die oben angegebenen Operatoren zu komplexeren Abfragen zusammensetzen. Da Cosmos DB geschachtelte Container unterstützt, können Sie die Zusammensetzung entweder verketten oder schachteln.

#### <a name="concatenation"></a>Verkettung

Die Syntax ist `input(.|.SelectMany())(.Select()|.Where())*`. Eine verkettete Abfrage kann mit einer optionalen `SelectMany`-Abfrage beginnen, gefolgt von mehreren `Select`- oder `Where`-Operatoren.

**Verkettung, Beispiel 1:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Verkettung, Beispiel 2:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Verkettung, Beispiel 3:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Verkettung, Beispiel 4:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>Verschachtelung

Die Syntax ist `input.SelectMany(x=>x.Q())`, wobei `Q` ein `Select`-, `SelectMany`- oder `Where`-Operator ist.

Bei einer geschachtelten Abfrage wird die innere Abfrage auf jedes Element des äußeren Containers angewendet. Ein wichtiges Feature ist, dass die innere Abfrage wie bei einer Selbstverknüpfung auf die Felder der Elemente im äußeren Container verweisen kann.

**Verschachtelung, Beispiel 1:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Verschachtelung, Beispiel 2:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Verschachtelung, Beispiel 3:**

- **LINQ Lambda-Ausdruck**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>Referenzen

- [Azure Cosmos DB-SQL-Spezifikation](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [JavaScript-Spezifikation](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz. [Query evaluation techniques for large databases](https://dl.acm.org/citation.cfm?id=152611). *ACM Computing Surveys* 25, Nr. 2 (1993).
- Graefe, G. "The Cascades framework for query optimization." *IEEE Data Eng. Bull.* 18, Nr. 3 (1995).
- Lu, Ooi, Tan. "Query Processing in Parallel Relational Database Systems." *IEEE Computer Society Press* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar und Andrew Tomkins. "Pig Latin: A Not-So-Foreign Language for Data Processing." *SIGMOD* (2008).

## <a name="next-steps"></a>Nächste Schritte

- [Einführung in Azure Cosmos DB][introduction]
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB-Konsistenzebenen][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
