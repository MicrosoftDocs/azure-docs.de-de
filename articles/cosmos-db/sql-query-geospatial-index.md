---
title: Indizieren von räumlichen Daten mit Azure Cosmos DB
description: Indizieren räumlicher Daten mit Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: tisande
ms.openlocfilehash: 47eedf1ddbb155180d364c42ec179b3e01279e44
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336213"
---
# <a name="index-geospatial-data-with-azure-cosmos-db"></a>Indizieren von räumlichen Daten mit Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Wir haben die Datenbank-Engine von Azure Cosmos DB so entworfen, dass sie vollständig vom Schema unabhängig ist und erstklassige Unterstützung für JSON bietet. Die für Schreibvorgänge optimierte Datenbank-Engine von Azure Cosmos DB bietet jetzt auch native Unterstützung für räumliche Daten, die gemäß GeoJSON-Standard dargestellt sind.

Kurz gesagt, die Geometrie wird von geodätischen Koordinaten auf eine 2D-Ebene projiziert und dann schrittweise mithilfe eines **Quadtrees** in Zellen unterteilt. Diese Zellen werden zu 1D basierend auf der Position der Zelle auf einer **raumfüllenden Hilbert-Kurve** zugeordnet, die die Lage von Punkten beibehält. Wenn Positionsdaten darüber hinaus indiziert werden, durchlaufen sie einen als **Mosaikarbeit** bezeichneten Prozess, bei dem alle Zellen, die eine Position schneiden, im Azure Cosmos DB-Index als Schlüssel indiziert und gespeichert werden. Zur Abfragezeit werden Argumente wie Punkte und Polygone auch in den Mosaikprozess einbezogen, um die entsprechenden Zellen-ID-Bereiche zu extrahieren, und dann zum Abrufen von Daten aus dem Index verwendet.

Wenn Sie eine Indizierungsrichtlinie angeben, die einen räumlichen Index für `/*` (alle Pfade) enthält, werden alle im Container gefundenen Daten für effiziente räumliche Abfragen indiziert.

> [!NOTE]
> Azure Cosmos DB unterstützt die Indizierung von Punkten, Linienfolgen (LineStrings), Polygonen und Multipolygonen. Wenn Sie einen dieser Typen indizieren, werden automatisch alle anderen Typen indiziert. Anders ausgedrückt: Wenn Sie Polygone indizieren, werden auch Punkte, Linienfolgen und Multipolygone indiziert. Die Indizierung eines neuen räumlichen Typs wirkt sich nicht auf die RU-Gebühr für Schreibvorgänge oder die Indexgröße aus, es sei denn, Sie verfügen über gültige GeoJSON-Daten dieses Typs.

## <a name="modifying-geospatial-configuration"></a>Ändern der Geokonfiguration

In Ihrem Container legt die **Geokonfiguration** fest, wie die räumlichen Daten indiziert werden. Geben Sie pro Container eine **Geokonfiguration** an: „Geografie“ oder „Geometrie“.

Sie können im Azure-Portal zwischen den räumlichen Typen **Geografie** und **Geometrie** wechseln. Es ist wichtig, dass Sie eine [gültige Indizierungsrichtlinie für räumliche Geometrie mit einem Begrenzungsrahmen](#geometry-data-indexing-examples) erstellen, bevor Sie zum räumlichen Typ „Geometrie“ wechseln.

Im Folgenden wird beschrieben, wie Sie die **Geokonfiguration** im Azure-Portal in **Data Explorer** festlegen:

:::image type="content" source="./media/sql-query-geospatial-index/geospatial-configuration.png" alt-text="Festlegen der Geokonfiguration":::

Sie können auch die `geospatialConfig` im .NET SDK ändern, um die **Geokonfiguration** anzupassen:

Ohne Angabe wird `geospatialConfig` standardmäßig auf den geography-Datentyp festgelegt. Wenn Sie `geospatialConfig` ändern, werden alle vorhandenen räumlichen Daten im Container neu indiziert.

Das folgende Beispiel veranschaulicht, wie der räumliche Datentyp in `geometry` geändert wird, indem die `geospatialConfig`-Eigenschaft festgelegt und eine **boundingBox** hinzugefügt wird:

```csharp
    //Retrieve the container's details
    ContainerResponse containerResponse = await client.GetContainer("db", "spatial").ReadContainerAsync();
    //Set GeospatialConfig to Geometry
    GeospatialConfig geospatialConfig = new GeospatialConfig(GeospatialType.Geometry);
    containerResponse.Resource.GeospatialConfig = geospatialConfig;
    // Add a spatial index including the required boundingBox
    SpatialPath spatialPath = new SpatialPath
            {  
                Path = "/locations/*",
                BoundingBox = new BoundingBoxProperties(){
                    Xmin = 0,
                    Ymin = 0,
                    Xmax = 10,
                    Ymax = 10
                }
            };
    spatialPath.SpatialTypes.Add(SpatialType.Point);
    spatialPath.SpatialTypes.Add(SpatialType.LineString);
    spatialPath.SpatialTypes.Add(SpatialType.Polygon);
    spatialPath.SpatialTypes.Add(SpatialType.MultiPolygon);

    containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);

    // Update container with changes
    await client.GetContainer("db", "spatial").ReplaceContainerAsync(containerResponse.Resource);
```

## <a name="geography-data-indexing-examples"></a>Beispiele für die Indizierung räumlicher Daten

Der folgende JSON-Codeausschnitt zeigt eine Indizierungsrichtlinie mit aktivierter räumlicher Indizierung für den Datentyp **geography**. Er ist für räumliche Daten mit dem Datentyp „geography“ vorgesehen und indiziert alle GeoJSON-Punkte, -Polygone, -Multipolygone oder -LineStrings, die in Dokumenten für räumliche Abfragen gefunden werden. Wenn Sie die Indizierungsrichtlinie im Azure-Portal ändern, können Sie den folgenden JSON-Code für die Indizierungsrichtlinie angeben, um die räumliche Indizierung für Ihren Container zu aktivieren:

**JSON-Richtlinie für die Containerindizierung mit räumlicher Indizierung mit dem Datentyp „geography“**

```json
{
    "automatic": true,
    "indexingMode": "Consistent",
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/*",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ],
    "excludedPaths": []
}
```

> [!NOTE]
> Wenn der GeoJSON-Wert "location" innerhalb des Dokuments fehlerhaft oder ungültig ist, wird er nicht für räumliche Abfragen indiziert. Sie können Werte von "location" mit ST_ISVALID und ST_ISVALIDDETAILED überprüfen.

Sie können die Indizierungsrichtlinie mit der Azure-Befehlszeilenschnittstelle, PowerShell oder einem beliebigen SDK [ändern](how-to-manage-indexing-policy.md).

## <a name="geometry-data-indexing-examples"></a>Beispiele für die Indizierung geometrischer Daten

Beim **geometry** -Datentyp müssen Sie ähnlich wie beim geography-Datentyp relevante Pfade und Typen angeben, die indiziert werden sollen. Darüber hinaus müssen Sie eine `boundingBox` in der Indizierungsrichtlinie angeben, um den gewünschten Bereich anzugeben, der für den betreffenden Pfad indiziert werden soll. Für jeden räumlichen Pfad ist eine eigene `boundingBox` erforderlich.

Der Begrenzungsrahmen umfasst folgende Eigenschaften:

- **xmin** : die minimale indizierte x-Koordinate
- **ymin** : die minimale indizierte y-Koordinate
- **xmax** : die maximale indizierte x-Koordinate
- **ymax** : die maximale indizierte y-Koordinate

Ein Begrenzungsrahmen ist erforderlich, da geometrische Daten auf einer Ebene liegen, die unendlich sein kann. Für räumliche Indizes ist jedoch ein endlicher Raum erforderlich. Beim **geography** -Datentyp ist die Erde die Begrenzung, sodass Sie keinen Begrenzungsrahmen festlegen müssen.

Erstellen Sie einen Begrenzungsrahmen, der alle (oder die meisten) Ihre(r) Daten enthält. Nur Vorgänge, die mit Objekten berechnet werden, die vollständig im Begrenzungsrahmen liegen, nutzen den räumlichen Index. Wenn Sie den Begrenzungsrahmen größer als nötig erstellen, wirkt sich dies negativ auf die Abfrageleistung aus.

Nachstehend finden Sie ein Beispiel für eine Indizierungsrichtlinie, die **geometry** -Daten indiziert, wobei **geospatialConfig** auf `geometry` festgelegt ist:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/locations/*",
            "types": [
                "Point",
                "LineString",
                "Polygon",
                "MultiPolygon"
            ],
            "boundingBox": {
                "xmin": -10,
                "ymin": -20,
                "xmax": 10,
                "ymax": 20
            }
        }
    ]
}
```

Die obige Indizierungsrichtlinie verfügt über eine **boundingBox** von (-10, 10) für die x-Koordinaten und (-20, 20) für die y-Koordinaten. Der Container mit der obigen Indizierungsrichtlinie indiziert alle Point-, Polygon-, MultiPolygon- und LineString-Objekte, die vollständig in diesem Bereich liegen.

> [!NOTE]
> Wenn Sie versuchen, eine Indizierungsrichtlinie mit einer **boundingBox** einem Container mit dem Datentyp `geography` hinzuzufügen, tritt ein Fehler auf. Vor dem Hinzufügen einer **boundingBox** sollten Sie die **geospatialConfig** des Containers in `geometry` ändern. Sie können Daten hinzufügen und den Rest der Indizierungsrichtlinie (z. B. Pfade und Typen) vor oder nach Auswahl des räumlichen Datentyps für den Container ändern.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die ersten Schritte mit räumlichen Daten in Azure Cosmos DB erlernt haben, haben Sie folgende Möglichkeiten:

* Weitere Informationen zur [Azure Cosmos DB-Abfrage](sql-query-getting-started.md)
* Weitere Informationen zum [Abfragen räumlicher Daten mit Azure Cosmos DB](sql-query-geospatial-query.md)
* Weitere Informationen zu [Räumlichen Daten und GeoJSON-Standortdaten in Azure Cosmos DB](sql-query-geospatial-intro.md)
