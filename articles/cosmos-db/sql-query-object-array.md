---
title: Arbeiten mit Arrays und Objekten in Azure Cosmos DB
description: Hier erfahren Sie mehr über die SQL-Syntax zum Erstellen von Arrays und Objekten in Azure Cosmos DB. Außerdem enthält dieser Artikel einige Beispiele zum Ausführen von Vorgängen für Arrayobjekte.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493786"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Arbeiten mit Arrays und Objekten in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Eine Schlüsselfunktion der SQL-API in Azure Cosmos DB ist die Array- und Objekterstellung. In diesem Dokument werden Beispiele verwendet, die mit dem [Family](sql-query-getting-started.md#upload-sample-data)-Dataset neu erstellt werden können.

Hier ist ein Beispielelement in diesem Dataset:

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

## <a name="arrays"></a>Arrays

Sie können Arrays wie im folgenden Beispiel gezeigt erstellen:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Die Ergebnisse sind:

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

Sie können auch den [ARRAY-Ausdruck](sql-query-subquery.md#array-expression) verwenden, um ein Array aus den Ergebnissen der [Unterabfrage](sql-query-subquery.md) zu erstellen. Diese Abfrage ruft alle unterschiedlichen Vornamen von Kindern in einem Array ab.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

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

## <a name="iteration"></a><a id="Iteration"></a>Iteration

Die SQL-API unterstützt eine Iteration über JSON-Arrays mit dem [IN-Schlüsselwort](sql-query-keywords.md#in) in der FROM-Quelle. Im folgenden Beispiel:

```sql
SELECT *
FROM Families.children
```

Die Ergebnisse sind:

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

Die Ergebnisse sind:

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

Die Ergebnisse sind:

```json
[{
  "givenName": "Lisa"
}]
```

Sie können auch das Ergebnis einer Array-Iteration aggregieren. Die folgende Abfrage zählt beispielsweise die Anzahl der Kinder in allen Familien:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Die Ergebnisse sind:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Wenn Sie das IN-Schlüsselwort für die Iteration verwenden, können Sie keine Eigenschaften außerhalb des Arrays filtern oder projizieren. Stattdessen sollten Sie [JOINs](sql-query-join.md) verwenden.

Weitere Beispiele finden Sie im [Blogbeitrag zum Arbeiten mit Arrays in Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte](sql-query-getting-started.md)
- [Azure Cosmos DB-.NET-Beispiele](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Joins](sql-query-join.md)
