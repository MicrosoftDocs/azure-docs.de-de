---
title: SQL-JOIN-Abfragen für Azure Cosmos DB
description: Hier erfahren Sie, wie Sie mehrere Tabellen in Azure Cosmos DB verknüpfen, um die Daten abzufragen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: c3fad0c7eed2b42a9ac340a091f17b90e22b000a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333833"
---
# <a name="joins-in-azure-cosmos-db"></a>Verknüpfungen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In einer relationalen Datenbank sind tabellenübergreifende Verknüpfungen die logische Grundvoraussetzung für die Planung normalisierter Schemas. Im Gegensatz dazu verwendet die SQL-API das denormalisierte Datenmodell schemafreier Elemente (das logische Äquivalent einer *Selbstverknüpfung* ).

Aus inneren Verknüpfungen resultiert ein komplettes Kreuzungsprodukt der an der Verknüpfung beteiligten Sätze. Das Ergebnis einer N-Way-Verknüpfung ist ein Satz von N-Element-Tupeln, wo jeder Wert im Tupel dem an der Verknüpfung beteiligten Aliassatz zugeordnet ist, und der Zugriff auf den Wert durch Verweis auf diesen Alias in anderen Klauseln erfolgen kann.

## <a name="syntax"></a>Syntax

Die Sprache unterstützt die Syntax `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`. Diese Abfrage gibt einen Satz von Tupeln mit `N` Werten zurück. Jedes Tupel enthält Werte, die durch Iteration aller Containeraliase über deren jeweilige Sätze entstanden sind. 

Betrachten Sie die folgende FROM-Klausel: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Jede Quelle definiert `input_alias1, input_alias2, …, input_aliasN`. Diese FROM-Klausel gibt einen Satz von N-Tupeln (Tupel mit N-Werten) zurück. Jedes Tupel enthält Werte, die durch Iteration aller Containeraliase über deren jeweilige Sätze entstanden sind.  
  
**Beispiel 1** – zwei Quellen  
  
- `<from_source1>` ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- `<from_source2>` ist dokumentbezogen, verweist auf „input_alias1“ und stellt folgende Sätze dar:  
  
    {1, 2} für `input_alias1 = A,`  
  
    {3} für `input_alias1 = B,`  
  
    {4, 5} für `input_alias1 = C,`  
  
- Die FROM-Klausel `<from_source1> JOIN <from_source2>` führt zu folgenden Tupeln:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Beispiel 2** – drei Quellen  
  
- `<from_source1>` ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- `<from_source2>` ist dokumentbezogen, verweist auf `input_alias1` und stellt folgende Sätze dar:  
  
    {1, 2} für `input_alias1 = A,`  
  
    {3} für `input_alias1 = B,`  
  
    {4, 5} für `input_alias1 = C,`  
  
- `<from_source3>` ist dokumentbezogen, verweist auf `input_alias2` und stellt folgende Sätze dar:  
  
    {100, 200} für `input_alias2 = 1,`  
  
    {300} für `input_alias2 = 3,`  
  
- Die FROM-Klausel `<from_source1> JOIN <from_source2> JOIN <from_source3>` führt zu folgenden Tupeln:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Fehlende Tupel für andere Werte von `input_alias1`, `input_alias2`, für die `<from_source3>` keine Werte zurückgegeben hat.  
  
**Beispiel 3** – drei Quellen  
  
- <from_source1> ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- `<from_source1>` ist containerbezogen und stellt den Satz {A, B, C} dar.  
  
- <from_source2> ist dokumentbezogen, verweist auf „input_alias1“ und stellt folgende Sätze dar:  
  
    {1, 2} für `input_alias1 = A,`  
  
    {3} für `input_alias1 = B,`  
  
    {4, 5} für `input_alias1 = C,`  
  
- `<from_source3>` ist auf `input_alias1` bezogen und stellt folgende Sätze dar:  
  
    {100, 200} für `input_alias2 = A,`  
  
    {300} für `input_alias2 = C,`  
  
- Die FROM-Klausel `<from_source1> JOIN <from_source2> JOIN <from_source3>` führt zu folgenden Tupeln:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Dies führt zu einem Kreuzprodukt zwischen `<from_source2>` und `<from_source3>`, da beide auf die gleiche `<from_source1>` bezogen sind.  Dies führt zu 4 (2 x 2) Tupeln mit dem Wert A, 0 Tupeln mit dem Wert B (1 x 0) und 2 (2 x 1) Tupeln mit dem Wert C.  
  
## <a name="examples"></a>Beispiele

Die folgenden Beispiele veranschaulichen die Funktionsweise der JOIN-Klausel. Bevor Sie diese Beispiele ausführen, laden Sie die [Beispielfamiliendaten](sql-query-getting-started.md#upload-sample-data) hoch. Das Ergebnis im folgenden Beispiel ist leer, da das Kreuzprodukt der einzelnen Quellelemente und einem leeren Satz leer ist:

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

Die Ergebnisse sind:

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

Die Ergebnisse sind:

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
3. Zuletzt die `id`-Eigenschaft des Stammobjekts `f` alleine projizieren

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

Die Ergebnisse sind:

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

Die Ergebnisse sind:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Unterabfragen](sql-query-subquery.md)