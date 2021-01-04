---
title: Indizierung in Azure Cosmos DB
description: Hier finden Sie Informationen zur Funktionsweise der Indizierung in Azure Cosmos DB sowie zu verschiedenen unterstützten Indextypen (etwa zu Bereichsindizes, räumlichen Indizes und zusammengesetzten Indizes).
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: tisande
ms.openlocfilehash: 4211f13324b9fda0b0823b2d035eb03863cb686d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339752"
---
# <a name="indexing-in-azure-cosmos-db---overview"></a>Indizierung in Azure Cosmos DB: Übersicht
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ist eine schemaunabhängige Datenbank, die es Ihnen ermöglicht, Ihre Anwendung zu durchlaufen, ohne sich mit Schema- oder Indexverwaltung befassen zu müssen. Standardmäßig indiziert Azure Cosmos DB automatisch alle Eigenschaften für sämtliche Elemente in Ihrem [Container](account-databases-containers-items.md#azure-cosmos-containers), ohne dass ein Schema definiert oder Sekundärindizes konfiguriert werden müssen.

In diesem Artikel wird beschrieben, wie Azure Cosmos DB Daten indiziert und wie Indizes zur Verbesserung der Abfrageleistung verwendet werden. Sie sollten zunächst diesen Abschnitt lesen, bevor Sie sich mit dem Anpassen von [Indizierungsrichtlinien](index-policy.md) beschäftigen.

## <a name="from-items-to-trees"></a>Von Elementen zu Strukturen

Bei jedem Speichern eines Elements in einem Container wird dessen Inhalt als ein JSON-Dokument projiziert und anschließend in eine strukturierte Darstellung konvertiert. Das bedeutet, dass jede Eigenschaft dieses Elements als Knoten in einer Struktur dargestellt wird. Als übergeordnetes Element für alle Eigenschaften auf oberster Ebene des Elements wird ein Pseudostammknoten erstellt. Die Blattknoten enthalten die eigentlichen Skalarwerte eines Elements.

Betrachten Sie z. B. dieses Element:

```json
    {
        "locations": [
            { "country": "Germany", "city": "Berlin" },
            { "country": "France", "city": "Paris" }
        ],
        "headquarters": { "country": "Belgium", "employees": 250 },
        "exports": [
            { "city": "Moscow" },
            { "city": "Athens" }
        ]
    }
```

Es wird durch die folgende Struktur dargestellt:

:::image type="content" source="./media/index-overview/item-as-tree.png" alt-text="Das vorherige Element als Struktur dargestellt" border="false":::

Beachten Sie, wie Arrays in der Struktur codiert werden: Jeder Eintrag in einem Array erhält direkt einen Zwischenknoten, der mit dem Index dieses Eintrags innerhalb des Arrays bezeichnet ist (0, 1 usw.).

## <a name="from-trees-to-property-paths"></a>Von Strukturen zu Eigenschaftenpfaden

Azure Cosmos DB transformiert die Elemente in Strukturen, da so über die Pfade innerhalb dieser Strukturen auf Elemente verwiesen werden kann. Um den Pfad für eine Eigenschaft abzurufen, können Sie die Struktur vom Stammknoten aus bis zu dieser Eigenschaft durchlaufen. Verketten Sie dabei die Bezeichnungen aller durchlaufenen Knoten.

Dies sind die Pfade für jede Eigenschaft aus dem oben beschriebenen Beispielelement:

- /locations/0/country: "Germany"
- /locations/0/city: "Berlin"
- /locations/1/country: "France"
- /locations/1/city: "Paris"
- /headquarters/country: "Belgium"
- /headquarters/employees: 250
- /exports/0/city: "Moscow"
- /exports/1/city: "Athens"

Wenn ein Element geschrieben wird, indiziert Azure Cosmos DB den Pfad jeder Eigenschaft und den zugehörigen Wert.

## <a name="index-kinds"></a>Indextypen

Azure Cosmos DB unterstützt derzeit drei Arten von Indizes.

### <a name="range-index"></a>Bereichsindex

Der **Bereichsindex** basiert auf einer geordneten Baumstruktur. Dieser Indextyp wird für Folgendes verwendet:

- Gleichheitsabfragen:

    ```sql
   SELECT * FROM container c WHERE c.property = 'value'
   ```

   ```sql
   SELECT * FROM c WHERE c.property IN ("value1", "value2", "value3")
   ```

   Gleichheitsübereinstimmung für ein Arrayelement
   ```sql
    SELECT * FROM c WHERE ARRAY_CONTAINS(c.tags, "tag1")
    ```

- Bereichsabfragen:

   ```sql
   SELECT * FROM container c WHERE c.property > 'value'
   ```
  (funktioniert für `>`, `<`, `>=`, `<=`, `!=`)

- Überprüfen, ob eine Eigenschaft vorhanden ist:

   ```sql
   SELECT * FROM c WHERE IS_DEFINED(c.property)
   ```

- Zeichenfolgensystemfunktionen:

   ```sql
   SELECT * FROM c WHERE CONTAINS(c.property, "value")
   ```

   ```sql
   SELECT * FROM c WHERE STRINGEQUALS(c.property, "value")
   ```

- `ORDER BY` fragt Folgendes ab:

   ```sql
   SELECT * FROM container c ORDER BY c.property
   ```

- `JOIN` fragt Folgendes ab:

   ```sql
   SELECT child FROM container c JOIN child IN c.properties WHERE child = 'value'
   ```

Range-Indizes können für Skalarwerte (Zeichenfolge oder Zahl) verwendet werden.

### <a name="spatial-index"></a>Räumlicher Index

**Räumliche** Indizes ermöglichen effiziente Abfragen räumlicher Objekte wie Punkte, Linien, Polygone und Multipolygon. Diese Abfragen verwenden die Schlüsselwörter ST_DISTANCE, ST_WITHIN und ST_INTERSECTS. Es folgen einige Beispiele für die Verwendung des räumlichen Index:

- Abfragen zum räumlichen Abstand:

   ```sql
   SELECT * FROM container c WHERE ST_DISTANCE(c.property, { "type": "Point", "coordinates": [0.0, 10.0] }) < 40
   ```

- Räumliche Abfragen:

   ```sql
   SELECT * FROM container c WHERE ST_WITHIN(c.property, {"type": "Point", "coordinates": [0.0, 10.0] })
   ```

- Abfragen zur räumlichen Überschneidung:

   ```sql
   SELECT * FROM c WHERE ST_INTERSECTS(c.property, { 'type':'Polygon', 'coordinates': [[ [31.8, -5], [32, -5], [31.8, -5] ]]  })  
   ```

Spatial-Indizes können für ordnungsgemäß formatierte [GeoJSON](./sql-query-geospatial-intro.md)-Objekte verwendet werden. Derzeit werden Point, LineString, Polygon und MultiPolygon unterstützt.

### <a name="composite-indexes"></a>Zusammengesetzte Indizes

**Zusammengesetzte** Indizes erhöhen die Effizienz, wenn Sie Vorgänge für mehrere Felder durchführen. Dieser Indextyp wird für Folgendes verwendet:

- `ORDER BY` fragt mehrere Eigenschaften ab:

```sql
 SELECT * FROM container c ORDER BY c.property1, c.property2
```

- Abfragen mit einem Filter und `ORDER BY`. Diese Abfragen können einen zusammengesetzten Index verwenden, wenn die Filter-Eigenschaft der `ORDER BY`-Klausel hinzugefügt wird.

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' ORDER BY c.property1, c.property2
```

- Abfragen mit einem Filter für zwei oder mehr Eigenschaften, bei denen mindestens eine Eigenschaft ein Gleichheitsfilter ist

```sql
 SELECT * FROM container c WHERE c.property1 = 'value' AND c.property2 > 'value'
```

Solange ein einziges Filterprädikat einen der Indextypen verwendet, wertet die Abfrage-Engine dieses zuerst aus, bevor der Rest überprüft wird. Wenn Sie z.B. eine SQL-Abfrage haben wie `SELECT * FROM c WHERE c.firstName = "Andrew" and CONTAINS(c.lastName, "Liu")`

* Bei der oben gezeigten Abfrage wird zuerst mithilfe des Index nach Einträgen gefiltert, bei denen „firstName“ den Wert „Andrew“ hat. Anschließend werden alle Einträge mit „firstName“ = „Andrew“ über eine nachfolgende Pipeline übergeben, um das Filterprädikat CONTAINS auszuwerten.

* Sie können Abfragen beschleunigen und vollständige Containerüberprüfungen vermeiden, wenn Sie Funktionen ohne Nutzung des Index (z .B. CONTAINS) verwenden, indem Sie zusätzliche Filterprädikate hinzufügen, die den Index verwenden. Die Reihenfolge der Filterklauseln ist nicht von Bedeutung. Die Abfrage-Engine ermittelt, welche Prädikate selektiver sind, und führt die Abfrage entsprechend aus.

## <a name="querying-with-indexes"></a>Abfragen mit Indizes

Die während der Indizierung der Daten extrahierten Pfade vereinfachen das Suchen im Index bei der Verarbeitung einer Abfrage. Durch einen Abgleich der `WHERE`-Klausel einer Abfrage mit der Liste der indizierten Pfade ist es möglich, sehr schnell die Elemente zu ermitteln, die dem Abfrageprädikat entsprechen.

Betrachten Sie beispielsweise die folgende Abfrage: `SELECT location FROM location IN company.locations WHERE location.country = 'France'`. Das Abfrageprädikat (nach Elementen filtern, die an beliebiger Stelle „France“ als Land oder Region enthalten) würde dem Pfad entsprechen, der rot hervorgehoben ist:

:::image type="content" source="./media/index-overview/matching-path.png" alt-text="Abgleichen mit einem bestimmten Pfad in einer Struktur" border="false":::

> [!NOTE]
> Eine `ORDER BY`-Klausel, die anhand einer einzelnen Eigenschaft geordnet wird, benötigt *immer* einen Range-Index. Falls der referenzierte Pfad diesen nicht aufweist, tritt ein Fehler auf. Ebenso benötigt eine `ORDER BY`-Abfrage, die nach mehreren Eigenschaften sortiert, *immer* einen zusammengesetzten Index.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Indizierung erhalten Sie in den folgenden Artikeln:

- [Indizierungsrichtlinie](index-policy.md)
- [Gewusst wie: Verwalten der Indizierungsrichtlinie](how-to-manage-indexing-policy.md)