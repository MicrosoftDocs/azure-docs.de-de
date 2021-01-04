---
title: GROUP BY-Klausel in Azure Cosmos DB
description: Erfahren Sie mehr über die GROUP BY-Klausel für Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545434"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY-Klausel in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die GROUP BY-Klausel unterteilt die Ergebnisse der Abfrage anhand der Werte einer oder mehrerer angegebener Eigenschaften.

## <a name="syntax"></a>Syntax

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumente

- `<scalar_expression_list>`

   Gibt die Ausdrücke an, die zum Aufteilen von Abfrageergebnissen verwendet werden.

- `<scalar_expression>`
  
   Ein beliebiger Skalarausdruck ist außer für skalare Unterabfragen und skalare Aggregate zulässig. Jeder Skalarausdruck muss mindestens einen Verweis auf eine Eigenschaft enthalten. Es gibt keine Begrenzung der Anzahl einzelner Ausdrücke oder der Kardinalität des jeweiligen Ausdrucks.

## <a name="remarks"></a>Bemerkungen
  
  Wenn eine Abfrage eine GROUP BY-Klausel verwendet, kann die SELECT-Klausel nur die Teilmenge der Eigenschaften und Systemfunktionen enthalten, die in der GROUP BY-Klausel enthalten ist. Eine Ausnahme bilden [aggregierte Funktionen](sql-query-aggregate-functions.md), die in der SELECT-Klausel vorkommen können, ohne in der GROUP BY-Klausel enthalten zu sein. Sie können in die SELECT-Klausel auch stets Literalwerte einschließen.

  Die GROUP BY-Klausel muss hinter der SELECT-, FROM- und WHERE-Klausel und vor der OFFSET LIMIT-Klausel stehen. GROUP BY kann derzeit nicht mit einer ORDER BY-Klausel verwendet werden, was jedoch in Planung ist.

  Die GROUP BY-Klausel erlaubt keines der folgenden Elemente:
  
- Aliase von Eigenschaften oder Systemfunktionen (in der SELECT-Klausel sind Aliase weiterhin zulässig)
- Unterabfragen
- Aggregierte Systemfunktionen (diese sind nur in der SELECT-Klausel zulässig)

Abfragen mit einer Aggregatsystemfunktion und einer Unterabfrage mit `GROUP BY` werden nicht unterstützt. Beispielsweise wird die folgende Abfrage nicht unterstützt:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Beispiele

In diesen Beispielen wird das Dataset zu Nährwerten verwendet, das über den [Azure Cosmos DB-Abfrageplayground](https://www.documentdb.com/sql/demo) verfügbar ist.

Hier ist z. B. eine Abfrage, die die Gesamtzahl der Artikel in jeder Lebensmittelgruppe zurückgibt:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Es folgen einige Ergebnisse (das Schlüsselwort TOP wird zum Eingrenzen von Ergebnissen verwendet):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Diese Abfrage enthält zwei Ausdrücke, die zum Aufteilen von Ergebnissen verwendet werden:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Einige Ergebnisse sind wie folgt:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Diese Abfrage enthält in der Group By-Klausel eine Systemfunktion:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Einige Ergebnisse sind wie folgt:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Diese Abfrage verwendet im Ausdruck mit der Artikeleigenschaft sowohl Schlüsselwörter als auch Systemfunktionen:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Die Ergebnisse sind:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [SELECT-Klausel](sql-query-select.md)
- [Aggregatfunktionen](sql-query-aggregate-functions.md)
