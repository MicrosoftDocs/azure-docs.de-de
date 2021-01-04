---
title: SQL-Schlüsselwörter für Azure Cosmos DB
description: Erfahren Sie mehr über SQL-Schlüsselwörter für Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 35232f95bc18432db05775807d95f23ceab66aea
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333782"
---
# <a name="keywords-in-azure-cosmos-db"></a>Schlüsselwörter in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden die Schlüsselwörter beschrieben, die in SQL-Abfragen in Azure Cosmos DB verwendet werden können.

## <a name="between"></a>BETWEEN

Mit dem Schlüsselwort `BETWEEN` können Abfragen für Bereiche von Zeichenfolgen- oder numerischen Werten ausgedrückt werden. Die folgende Abfrage gibt beispielsweise alle Elemente zurück, in denen das erste Kind die erste bis fünfte Klasse (einschließlich) besucht.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Sie können auch das Schlüsselwort `BETWEEN` in der `SELECT`-Klausel verwenden, wie im folgenden Beispiel gezeigt.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In der SQL-API können Sie – im Gegensatz zu ANSI SQL – Bereichsabfragen für Eigenschaften gemischter Typen ausdrücken. Beispielsweise kann `grade` in einigen Elementen eine Zahl (z.B. `5`) und in anderen eine Zeichenfolge (z.B. `grade4`) sein. In diesen Fällen (etwa in JavaScript) gibt der Vergleich zwischen den beiden unterschiedlichen Typen `Undefined` zurück, sodass das Element übersprungen wird.

## <a name="distinct"></a>DISTINCT

Das Schlüsselwort `DISTINCT` entfernt Duplikate aus der Projektion der Abfrage.

In diesem Beispiel projiziert die Abfrage Werte für alle Nachnamen:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Die Ergebnisse sind:

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

Die Ergebnisse sind:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` kann auch in der Projektion in einer Unterabfrage verwendet werden:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Diese Abfrage projiziert ein Array mit dem Wert von givenName für jedes untergeordnete Element, aus dem die Duplikate entfernt wurden. Dieses Array hat den Alias ChildNames und wird in der äußeren Abfrage projiziert.

Die Ergebnisse sind:

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

Abfragen mit einer Aggregatsystemfunktion und einer Unterabfrage mit `DISTINCT` werden nicht unterstützt. Beispielsweise wird die folgende Abfrage nicht unterstützt:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

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

Die SQL-API unterstützt eine [Iteration über JSON-Arrays](sql-query-object-array.md#Iteration) mit einem neuen Konstrukt, das mit dem Schlüsselwort IN in der Quelle FROM hinzugefügt wurde.

Wenn Sie Ihren Partitionsschlüssel in den `IN`-Filter einbeziehen, filtert Ihre Abfrage automatisch nur nach den relevanten Partitionen.

## <a name="top"></a>TOP

Das Schlüsselwort TOP gibt die ersten `N` Abfrageergebnisse in einer nicht definierten Reihenfolge zurück. Es empfiehlt sich, TOP mit der `ORDER BY`-Klausel zu verwenden, um die Ergebnisse auf die ersten `N` geordneten Werte zu beschränken. Die Kombination dieser beiden Klauseln ist die einzige Möglichkeit, um zuverlässig anzugeben, welche Zeilen von TOP betroffen sind.

Sie können TOP mit einem konstanten Wert wie im folgenden Beispiel oder mit einem Variablenwert mithilfe von parametrisierten Abfragen verwenden.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Die Ergebnisse sind:

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

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Joins](sql-query-join.md)
- [Unterabfragen](sql-query-subquery.md)