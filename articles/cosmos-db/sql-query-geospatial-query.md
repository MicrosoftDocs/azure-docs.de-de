---
title: Abfragen räumlicher Daten mit Azure Cosmos DB
description: Abfragen räumlicher Daten mit Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.openlocfilehash: bb9a0351b6f1de47f3687995c65060a23bdb2874
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336111"
---
# <a name="querying-geospatial-data-with-azure-cosmos-db"></a>Abfragen räumlicher Daten mit Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie mithilfe von SQL und LINQ räumliche Daten in Azure Cosmos DB abfragen. Das Speichern und Verwenden räumlicher Daten wird derzeit nur von Azure Cosmos DB-SQL-API-Konten unterstützt. Azure Cosmos DB unterstützt die folgenden integrierten OGC-Funktionen (Open Geospatial Consortium ) für das Abfragen von Geodaten. Weitere Informationen zu sämtlichen integrierten Funktionen der SQL-Sprache finden Sie unter [Systemfunktionen (Azure Cosmos DB)](sql-query-system-functions.md).

## <a name="spatial-sql-built-in-functions"></a>Integrierte räumliche SQL-Funktionen

Die folgende Liste enthält hilfreiche räumliche Systemfunktionen für Abfragen in Azure Cosmos DB:

|**Verwendung**|**Beschreibung**|
|---|---|
| ST_DISTANCE (spatial_expr, spatial_expr) | Gibt den Abstand zwischen den beiden GeoJSON-Punkt-, Polygon- oder LineString-Ausdrücken zurück.|
|ST_WITHIN (spatial_expr, spatial_expr) | Gibt einen booleschen Ausdruck zurück, der angibt, ob das erste GeoJSON-Objekt (Punkt, Polygon oder LineString) innerhalb des zweiten GeoJSON-Objekts (Punkt, Polygon oder LineString) enthalten ist.|
|ST_INTERSECTS (spatial_expr, spatial_expr)| Gibt einen booleschen Ausdruck, der angibt, ob die beiden angegebenen GeoJSON-Objekte (Punkt, Polygon oder LineString) sich überschneiden.|
|ST_ISVALID| Gibt einen booleschen Wert zurück, der angibt, ob der/das angegebene GeoJSON-Punkt, -Polygon, oder -LineString gültig ist.|
| ST_ISVALIDDETAILED| Gibt einen JSON-Wert mit einem booleschen Wert zurück, wenn der angegebene Ausdruck vom Typ „GeoJSON Point“, „Polygon“ oder „LineString“ gültig ist. Ist der Ausdruck ungültig, wird ein Zeichenfolgenwert mit dem Grund zurückgegeben.|

Räumliche Funktionen können verwendet werden, um Entfernungsabfragen auf räumliche Daten anzuwenden. Die folgende Abfrage gibt beispielsweise alle Familiendokumente zurück, die maximal 30 km von der angegebenen Position entfernt sind. Dazu wird die integrierte Funktion `ST_DISTANCE` verwendet.

**Abfrage**

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {"type": "Point", "coordinates":[31.9, -4.8]}) < 30000
```

**Ergebnisse**

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Wenn Sie die räumliche Indizierung in Ihre Indizierungsrichtlinie einschließen, werden "Entfernungsabfragen" effizient über den Index beantwortet. Weitere Informationen zur räumlichen Indizierung finden Sie unter [Indizieren von räumlichen Daten mit Azure Cosmos DB](sql-query-geospatial-index.md). Wenn Sie über keinen räumlichen Index für die angegebenen Pfade verfügen, durchsucht die Abfrage den Container.

Mit `ST_WITHIN` kann geprüft werden, ob sich ein Punkt innerhalb eines Polygons befindet. Polygone werden häufig verwendet, um Umgrenzungen wie Postleitzahlenbereiche, Staatsgrenzen oder natürliche Gebilde darzustellen. Wenn Sie wiederum die räumliche Indizierung in Ihre Indizierungsrichtlinie einschließen, werden Abfragen nach enthaltenen Elementen effizient über den Index beantwortet.

Polygonargumente in `ST_WITHIN` dürfen nur einen einzelnen Ring enthalten. Mit anderen Worten: Die Polygone dürfen keine Löcher aufweisen.

**Abfrage**

```sql
    SELECT *
    FROM Families f
    WHERE ST_WITHIN(f.location, {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })
```

**Ergebnisse**

```json
    [{
      "id": "WakefieldFamily",
    }]
```

> [!NOTE]
> Wenn (ähnlich wie bei der Funktionsweise nicht übereinstimmender Typen in Azure Cosmos DB-Abfragen) der in einem der Argumente angegebene Standortwert falsch formatiert oder ungültig ist, wird er als **undefiniert** ausgewertet, und die ausgewerteten Dokumente werden aus den Abfrageergebnissen entfernt. Wenn Ihre Abfrage keine Ergebnisse zurückgibt, führen Sie `ST_ISVALIDDETAILED` aus, um herauszufinden, warum der räumliche Typ ungültig ist.
>
>

Azure Cosmos DB unterstützt auch inverse Abfragen, d.h., Sie indizieren Polygone oder Linien in Azure Cosmos DB und führen dann Abfragen nach den Bereichen durch, die einen bestimmten Punkt enthalten. Dieses Muster wird häufig in der Logistik verwendet, um beispielsweise zu ermitteln, wann ein Lkw in einen bestimmten Bereich einfährt oder ihn verlässt.

**Abfrage**

```sql
    SELECT *
    FROM Areas a
    WHERE ST_WITHIN({"type": "Point", "coordinates":[31.9, -4.8]}, a.location)
```

**Ergebnisse**

```json
    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon",
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]
```

Mit `ST_ISVALID` und `ST_ISVALIDDETAILED` können Sie überprüfen, ob ein räumliches Objekt gültig ist. Die folgende Abfrage untersucht z. B. die Gültigkeit eines Punkts mit einem Längengradwert außerhalb des Gültigkeitsbereichs (-132,8). `ST_ISVALID` gibt nur einen booleschen Wert zurück. `ST_ISVALIDDETAILED` gibt den booleschen Wert und eine Zeichenfolge mit dem Grund zurück, warum das Argument als ungültig eingestuft wird.

**Abfrage**

```sql
    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })
```

**Ergebnisse**

```json
    [{
      "$1": false
    }]
```

Diese Funktionen können auch verwendet werden, um Polygone zu überprüfen. Hier wird beispielsweise `ST_ISVALIDDETAILED` verwendet, um ein nicht geschlossenes Polygon zu überprüfen.

**Abfrage**

```sql
    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})
```

**Ergebnisse**

```json
    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]
```

## <a name="linq-querying-in-the-net-sdk"></a>LINQ-Abfragen im .NET SDK

Das .NET SDK für SQL stellt auch die Stub-Methoden `Distance()` und `Within()` für die Verwendung in LINQ-Ausdrücken bereit. Der LINQ-Anbieter für SQL übersetzt diese Methodenaufrufe in entsprechende integrierte SQL-Funktionsaufrufe (ST_DISTANCE bzw. ST_WITHIN).

Im folgenden Beispiel für eine LINQ-Abfrage wird nach allen Dokumenten im Azure Cosmos-Container gesucht, deren Wert für `location` maximal 30 km vom angegebenen Punkt entfernt ist.

**LINQ-Abfrage der Entfernung**

```csharp
    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
        .Where(u => u.ProfileType == "Public" && u.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }
```

Analog dazu wird mit der folgenden Abfrage nach allen Dokumenten gesucht, deren Wert für `location` innerhalb des angegebenen Felds/Polygons liegt.

**LINQ-Abfrage nach enthaltenen Elementen**

```csharp
    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in container.GetItemLinqQueryable<UserProfile>(allowSynchronousQueryExecution: true)
         .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die ersten Schritte mit räumlichen Daten in Azure Cosmos DB erlernt haben, haben Sie folgende Möglichkeiten:

* Weitere Informationen zur [Azure Cosmos DB-Abfrage](sql-query-getting-started.md)
* Informieren Sie sich ausführlicher über [räumliche Daten und GeoJSON-Standortdaten in Azure Cosmos DB](sql-query-geospatial-intro.md).
* Informieren Sie sich ausführlicher über das [Indizieren von räumlichen Daten mit Azure Cosmos DB](sql-query-geospatial-index.md).
