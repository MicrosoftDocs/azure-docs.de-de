---
title: SQL-Unterabfragen für Azure Cosmos DB
description: Hier erfahren Sie mehr über SQL-Unterabfragen und ihre gängigen Anwendungsfälle sowie über verschiedene Arten von Unterabfragen in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340854"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>SQL-Unterabfragenbeispiele für Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Eine Unterabfrage ist eine Abfrage, die in einer anderen Abfrage geschachtelt ist. Eine Unterabfrage wird auch als innere Abfrage oder innere Auswahl bezeichnet. Die Anweisung, die eine Unterabfrage enthält, wird in der Regel als äußere Abfrage bezeichnet.

In diesem Artikel werden SQL-Unterabfragen und ihre gängigen Anwendungsfälle in Azure Cosmos DB beschrieben. Alle Beispielabfragen in dieser Dokumentation können für ein im [Abfrageplayground von Azure Cosmos DB](https://www.documentdb.com/sql/demo) vorab geladenes Nahrungsdataset ausgeführt werden.

## <a name="types-of-subqueries"></a>Typen von Unterabfragen

Es gibt zwei Haupttypen von Unterabfragen:

* **Korreliert** : Eine Unterabfrage, die auf Werte aus der äußeren Abfrage verweist. Die Unterabfrage wird für jede Zeile, die die äußere Abfrage verarbeitet, einmal ausgewertet.
* **Nicht korreliert** : Eine Unterabfrage, die von der äußeren Abfrage unabhängig ist. Sie kann ohne Abhängigkeit von der äußeren Abfrage eigenständig ausgeführt werden.

> [!NOTE]
> Azure Cosmos DB unterstützt nur korrelierte Unterabfragen.

Unterabfragen können basierend auf der Anzahl der Zeilen und Spalten, die sie zurückgeben, weiter klassifiziert werden. Es gibt drei Typen:
* **Tabelle** : Gibt mehrere Zeilen und mehrere Spalten zurück.
* **Mehrwertig** : Gibt mehrere Zeilen und eine einzelne Spalte zurück.
* **Skalar** : Gibt eine einzelne Zeile und eine einzelne Spalte zurück.

SQL-Abfragen in Azure Cosmos DB geben stets eine einzelne Spalte zurück (entweder einen einfachen Wert oder ein komplexes Dokument). Daher sind nur mehrwertige und skalare Unterabfragen in Azure Cosmos DB anwendbar. Sie können eine mehrwertige Unterabfrage nur in der FROM-Klausel als relationalen Ausdruck verwenden. Sie können eine skalare Unterabfrage in der SELECT- oder WHERE-Klausel als skalaren Ausdruck oder in der FROM-Klausel als relationalen Ausdruck verwenden.

## <a name="multi-value-subqueries"></a>Mehrwertige Unterabfragen

Mehrwertige Unterabfragen geben eine Reihe von Dokumenten zurück und werden immer in der FROM-Klausel verwendet. Sie werden verwendet für:

* Optimieren von JOIN-Ausdrücken. 
* Einmaliges Auswerten von ressourcenintensiven Ausdrücken und vielfaches Verweisen darauf.

## <a name="optimize-join-expressions"></a>Optimieren von JOIN-Ausdrücken

Mehrwertige Unterabfragen können JOIN-Ausdrücke optimieren, indem Prädikate nach jedem „Select-Many“-Ausdruck statt nach allen Cross Joins in der WHERE-Klausel gepusht werden.

Betrachten Sie die folgende Abfrage:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Für diese Abfrage vergleicht der Index jedes Dokument, das ein Tag mit dem Namen „infant formula“ hat. Es geht um ein nutrients-Element mit einem Wert zwischen 0 und 10 und ein servings-Element mit einer Menge größer als 1. Der JOIN-Ausdruck ermittelt hier das Kreuzprodukt aller Elemente des tags-, nutrients- und servings-Arrays für jedes übereinstimmende Dokument, bevor ein Filter angewendet wird. 

Die WHERE-Klausel wendet dann das Filterprädikat auf jedes <c, t, n, s >-Tupel an. Wenn z.B. ein übereinstimmendes Dokument 10 Elemente in jedem der drei Arrays hat, wird auf 1 x 10 x 10 x 10 (d.h. 1.000) Tupel erweitert. Die Verwendung von Unterabfragen kann hier helfen, indem verknüpfte Arrayelemente herausgefiltert werden, bevor der Joinvorgang mit dem nächsten Ausdruck ausgeführt wird.

Diese Abfrage entspricht der vorherigen, verwendet aber Unterabfragen:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Es wird davon ausgegangen, dass nur ein Element im tags-Array dem Filter entspricht, und es gibt fünf Elemente sowohl für das nutrients- als auch servings-Array. Die JOIN-Ausdrücke werden dann im Gegensatz zu 1.000 Elementen in der ersten Abfrage auf 1 x 1 x 5 x 5 = 25 Elemente erweitert.

## <a name="evaluate-once-and-reference-many-times"></a>Einmaliges Auswerten und häufiges Verweisen

Unterabfragen können helfen, Abfragen mit ressourcenintensiven Ausdrücken wie z.B. benutzerdefinierte Funktionen (User-Defined Functions, UDFs), komplexe Zeichenfolgen oder arithmetische Ausdrücke zu optimieren. Sie können eine Unterabfrage zusammen mit einem JOIN-Ausdruck verwenden, um den Ausdruck einmal auszuwerten, aber häufig darauf zu verweisen.

Die folgende Abfrage führt die UDF `GetMaxNutritionValue` zweimal aus:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Hier ist eine entsprechende Abfrage, die die benutzerdefinierte Funktion nur einmal ausführt:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Beachten Sie das Kreuzproduktverhalten von JOIN-Ausdrücken. Wenn der UDF-Ausdruck als undefiniert ausgewertet werden kann, sollten Sie sicherstellen, dass der JOIN-Ausdruck immer eine einzelne Zeile produziert, indem ein Objekt von der Unterabfrage anstatt direkt vom Wert zurückgegeben wird.
>

Hier ist ein ähnliches Beispiel, das ein Objekt anstelle eines Werts zurückgibt:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Der Ansatz ist nicht auf benutzerdefinierte Funktionen beschränkt. Er gilt für einen beliebigen möglicherweise ressourcenintensiven Ausdruck. Beispielsweise können Sie den gleichen Ansatz mit der mathematischen Funktion `avg` verwenden:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>JOIN-Imitieren mit externen Verweisdaten

Möglicherweise müssen Sie häufig auf statische Daten verweisen, die sich nur selten ändern, z.B. Maßeinheiten oder Landeskennzahlen. Solche Daten sollten nicht für jedes Dokument dupliziert werden. Diese Duplizierung zu vermeiden, spart Speicher und verbessert die Schreibleistung, indem die Dokumentgröße kleiner gehalten wird. Sie können mit einer Unterabfrage die Semantik eines inneren Joins mit einer Sammlung von Verweisdaten imitieren.

Betrachten Sie beispielsweise diesen Satz von Verweisdaten:

| **Einheit** | **Name**            | **Multiplikator** | **Basiseinheit** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1,00E-09       | Gram          |
| µg       | Microgram           | 1,00E-06       | Gram          |
| mg       | Milligram           | 1,00E-03       | Gram          |
| g        | Gram                | 1,00E+00       | Gram          |
| kg       | Kilogram            | 1,00E+03       | Gram          |
| Mg       | Megagram            | 1,00E+06       | Gram          |
| Gg       | Gigagram            | 1,00E+09       | Gram          |
| nJ       | Nanojoule           | 1,00E-09       | Joule         |
| µJ       | Microjoule          | 1,00E-06       | Joule         |
| mJ       | Millijoule          | 1,00E-03       | Joule         |
| J        | Joule               | 1,00E+00       | Joule         |
| kJ       | Kilojoule           | 1,00E+03       | Joule         |
| MJ       | Megajoule           | 1,00E+06       | Joule         |
| GJ       | Gigajoule           | 1,00E+09       | Joule         |
| cal      | Calorie             | 1,00E+00       | calorie       |
| kcal     | Calorie             | 1,00E+03       | calorie       |
| IU       | International units |                |               |


Die folgende Abfrage imitiert das Verknüpfen mit diesen Daten, sodass Sie den Namen der Einheit der Ausgabe hinzufügen:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Skalare Unterabfragen

Ein skalarer Unterabfragenausdruck ist eine Unterabfrage, die eine Auswertung für einen einzelnen Wert ausführt. Der Wert des skalaren Unterabfragenausdrucks ist der Wert der Projektion (SELECT-Klausel) der Unterabfrage.  Sie können einen skalaren Unterabfragenausdruck vielfach dort verwenden, wo ein skalarer Ausdruck gültig ist. Beispielsweise können Sie eine skalare Unterabfrage sowohl in SELECT- als auch WHERE-Klauseln in einem beliebigen Ausdruck verwenden.

Die Verwendung einer skalaren Unterabfrage ist jedoch nicht immer hilfreich. Beispielsweise bietet die Übergabe einer skalaren Unterabfrage als Argument entweder an System- oder benutzerdefinierte Funktionen keinen Vorteil hinsichtlich des Verbrauchs von Anforderungseinheiten (Request Units, RU) oder der Latenz.

Skalare Unterabfragen können weiter klassifiziert werden als:
* Skalare Unterabfragen mit einfachem Ausdruck
* Aggregierte skalare Unterabfragen

## <a name="simple-expression-scalar-subqueries"></a>Skalare Unterabfragen mit einfachem Ausdruck

Eine skalare Unterabfrage mit einfachem Ausdruck ist eine korrelierte Unterabfrage mit einer SELECT-Klausel, die keine Aggregatausdrücke enthält. Diese Unterabfragen bieten keine Optimierungsvorteile, da der Compiler sie in einen größeren einfachen Ausdruck konvertiert. Es gibt keinen korrelierten Kontext zwischen den inneren und äußeren Abfragen.

Hier sind einige Beispiele angegeben:

**Beispiel 1**

```sql
SELECT 1 AS a, 2 AS b
```

Sie können diese Abfrage mithilfe einer skalaren Unterabfrage mit einfachem Ausdruck folgendermaßen umschreiben:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Beide Abfragen erzeugen folgende Ausgabe:

```json
[
  { "a": 1, "b": 2 }
]
```

**Beispiel 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Sie können diese Abfrage mithilfe einer skalaren Unterabfrage mit einfachem Ausdruck folgendermaßen umschreiben:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Abfrageausgabe:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Beispiel 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Sie können diese Abfrage mithilfe einer skalaren Unterabfrage mit einfachem Ausdruck folgendermaßen umschreiben:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Abfrageausgabe:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Aggregierte skalare Unterabfragen

Eine aggregierte skalare Unterabfrage ist eine Unterabfrage, die eine Aggregatfunktion in Projektion oder Filter aufweist, die eine Auswertung für einen einzelnen Wert ausführt.

**Beispiel 1:**

Hier ist eine Unterabfrage mit einem einzelnen Aggregatfunktionsausdruck in der Projektion:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Abfrageausgabe:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Beispiel 2**

Hier ist eine Unterabfrage mit mehreren Aggregatfunktionsausdrücken:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Abfrageausgabe:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Beispiel 3**

Hier ist eine Abfrage mit einer aggregierten Unterabfrage in Projektion und Filter:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Abfrageausgabe:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Ein wesentlich besserer Ansatz zum Schreiben dieser Abfrage ist, die Unterabfrage in die JOIN-Anweisung zu integrieren und sowohl in der SELECT- als auch WHERE-Klausel auf den Unterabfragenalias zu verweisen. Diese Abfrage ist effizienter, da Sie die Unterabfrage nur in der JOIN-Anweisung und nicht in Projektion und Filter ausführen müssen.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS-Ausdruck

Azure Cosmos DB unterstützt EXISTS-Ausdrücke. Dies ist eine aggregierte skalare Unterabfrage, die in die Azure Cosmos DB-SQL-API integriert ist. EXISTS ist ein boolescher Ausdruck, der einen Unterabfrageausdruck verwendet und „true“ zurückgibt, wenn die Unterabfrage Zeilen zurückgibt. Andernfalls wird „false“ zurückgegeben.

Da die Azure Cosmos DB-SQL-API nicht zwischen booleschen Ausdrücken und anderen skalaren Ausdrücken unterscheidet, können Sie EXISTS in SELECT- und WHERE-Klauseln verwenden. Dies ist anders als bei T-SQL, wo ein boolescher Ausdruck (z.B. EXISTS, BETWEEN und IN) auf den Filter beschränkt ist.

Wenn die EXISTS-Unterabfrage einen einzelnen Wert zurückgibt, der nicht definiert ist, ergibt die Auswertung durch EXISTS „false“. Betrachten Sie beispielsweise die folgende Abfrage, deren Auswertungsergebnis „false“ ist:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Wenn das VALUE-Schlüsselwort in der vorherigen Unterabfrage ausgelassen wird, ist das Auswertungsergebnis der Abfrage „true“:
```sql
SELECT EXISTS (SELECT undefined) 
```

Die Unterabfrage umschließt die Liste der Werte in der ausgewählten Liste in einem Objekt. Wenn die ausgewählte Liste keine Werte aufweist, gibt die Unterabfrage die einzelnen Wert „{}“ zurück. Dieser Wert ist definiert, darum ist das Auswertungsergebnis von EXISTS „true“.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Beispiel: Umschreiben von ARRAY_CONTAINS und JOIN in EXISTS

Ein häufiger Anwendungsfall von ARRAY_CONTAINS ist das Filtern eines Dokuments nach dem Vorhandensein eines Elements in einem Array. In diesem Fall prüfen wir, ob das tags-Array ein Element mit dem Namen „orange“ enthält.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Sie können die gleiche Abfrage zur Verwendung von EXISTS umschreiben:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Darüber hinaus kann ARRAY_CONTAINS nur überprüfen, ob ein Wert einem Element in einem Array entspricht. Wenn Sie komplexere Filter für Arrayeigenschaften benötigen, verwenden Sie JOIN.

Betrachten Sie die folgende Abfrage, die basierend auf den Einheiten und `nutritionValue`-Eigenschaften im Array filtert: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Für jedes der Dokumente in der Sammlung wird ein Kreuzprodukt mit seinen Arrayelementen ausgeführt. Dieser JOIN-Vorgang ermöglicht, nach Eigenschaften innerhalb des Arrays zu filtern. Der RU-Verbrauch dieser Abfrage wird jedoch erheblich sein. Wenn z.B. 1.000 Dokumente in jedem Array 100 Elemente enthalten, wird er auf 1.000 x 100 (d.h. 100.000) Tupel erweitert.

Mit EXISTS kann dieses ressourcenintensive Kreuzprodukt vermieden werden:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

In diesem Fall filtern Sie in der EXISTS-Unterabfrage nach Arrayelementen. Wenn ein Arrayelement dem Filter entspricht, projizieren Sie es, und die Auswertung durch EXISTS ergibt „true“.

Sie können EXISTS auch in Verbindung mit einem Alias verwenden und in der Projektion darauf verweisen:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Abfrageausgabe:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>ARRAY-Ausdruck

Sie können mit dem ARRAY-Ausdruck die Ergebnisse einer Abfrage als Array projizieren. Sie können diesen Ausdruck nur in der SELECT-Klausel der Abfrage verwenden.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Abfrageausgabe:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Wie bei anderen Unterabfragen können beim ARRAY-Ausdruck Filter verwendet werden.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Abfrageausgabe:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

ARRAY-Ausdrücke können in Unterabfragen auch nach der FROM-Klausel verwendet werden.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Abfrageausgabe:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdaten](modeling-data.md)
