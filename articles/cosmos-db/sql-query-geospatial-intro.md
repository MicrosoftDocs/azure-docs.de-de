---
title: Räumliche Daten und GeoJSON-Standortdaten in Azure Cosmos DB
description: Hier finden Sie grundlegende Informationen zur Erstellung räumlicher Objekte mit Azure Cosmos DB und der SQL-API.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: a2e4e14927932575c9da42392329eea279f922fe
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336162"
---
# <a name="geospatial-and-geojson-location-data-in-azure-cosmos-db"></a>Räumliche Daten und GeoJSON-Standortdaten in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Artikel bietet eine Einführung in die Funktionalität von Geodaten in Azure Cosmos DB. Das Speichern und Verwenden räumlicher Daten wird derzeit nur von Azure Cosmos DB-SQL-API-Konten unterstützt. In unserer Dokumentation zur räumlichen Indizierung werden folgende Fragen beantwortet:

* Wie werden Geodaten in Azure Cosmos DB gespeichert?
* Wie kann ich Geodaten in Azure Cosmos DB in SQL und LINQ abfragen?
* Wie aktiviere oder deaktiviere ich die räumliche Indizierung in Azure Cosmos DB?

## <a name="spatial-data-use-cases"></a>Anwendungsfälle für räumliche Daten

Räumliche Daten werden häufig in Abfragen zur örtlichen Nähe verwendet, beispielsweise: „Suche alle Cafés in der Nähe meines aktuellen Standorts“. Gängige Anwendungsfälle:

* Analysen geografischer Standorte für bestimmte lokale Marketinginitiativen.
* Standortbasierte Personalisierung für verschiedene Branchen wie z. B. Einzelhandel und Gesundheitswesen.
* Verbesserungen in der Logistik zur Optimierung des Transports.
* Risikoanalysen, insbesondere für Versicherungen und Finanzunternehmen.
* Berücksichtigen von Situationen für Warnungen und Benachrichtigungen.

## <a name="introduction-to-spatial-data"></a>Einführung in räumliche Daten

Räumliche Daten beschreiben die Position und Form von Objekten im Raum. In den meisten Fällen entsprechen diese Daten Objekten auf der Erde und Geodaten. Räumliche Daten dienen zur Darstellung des Orts einer Person, einer Sehenswürdigkeit, der Umgrenzung einer Stadt oder eines Sees.

Die SQL-API von Azure Cosmos DB unterstützt zwei Typen räumlicher Daten: **geometrische** und **geografische**.

- Der **geometrische** Datentyp stellt Daten in einem euklidischen (zweidimensionalen) Koordinatensystem dar.
- Der **geography** -Datentyp stellt Daten in einem Erdkugel-Koordinatensystem dar.

## <a name="supported-data-types"></a>Unterstützte Datentypen

Azure Cosmos DB unterstützt eine Volltextindizierung und Abfrage von Geopunktdaten, die mithilfe der [GeoJSON-Spezifikation](https://tools.ietf.org/html/rfc7946) dargestellt werden. GeoJSON-Datenstrukturen sind stets gültige JSON-Objekte, weshalb sie mit Azure Cosmos DB gespeichert und ohne spezielle Tools oder Bibliotheken abgefragt werden können.

Von Azure Cosmos DB werden folgende Arten von räumlichen Daten unterstützt:

- Point
- LineString
- Polygon
- MultiPolygon

### <a name="points"></a>Punkte

Ein **Punkt** kennzeichnet eine einzelne Position im Raum. In Geodaten stellt ein Punkt den exakten Ort dar, der die Adresse eines Lebensmittelgeschäfts, ein Kiosk, ein Auto oder eine Stadt sein kann.  Ein Punkt wird in GeoJSON (und Azure Cosmos DB) mithilfe seines Koordinatenpaares oder Längen- und Breitengrads dargestellt.

Im Anschluss finden Sie JSON-Beispielcode für einen Punkt:

**Punkte in Azure Cosmos DB**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

Räumliche Datentypen können in ein Azure Cosmos DB-Dokument eingebettet werden, wie im folgenden Beispiel eines Benutzerprofils mit Standortdaten zu sehen:

**Verwenden eines Profils mit in Azure Cosmos DB gespeichertem Standort**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

### <a name="points-in-a-geometry-coordinate-system"></a>Punkte in einem Geometriekoordinatensystem

Für den **geometrischen** Datentyp gibt die GeoJson-Spezifikation zuerst die horizontale Achse und dann die vertikale Achse an.

### <a name="points-in-a-geography-coordinate-system"></a>Punkte in einem Geografiekoordinatensystem

Für den Datentyp **Geografie** gibt die GeoJSON-Spezifikation zuerst den Längengrad und dann den Breitengrad an. Wie in anderen Kartenprogrammen sind Längen- und Breitengrade Winkel, die in Grad dargestellt werden. Längengradwerte werden ab dem Nullmeridian gemessen und liegen zwischen –180,0 und +180,0 Grad. Breitengradwerte werden ab dem Äquator gemessen und liegen zwischen –90,0 und +90,0 Grad.

Azure Cosmos DB interpretiert Koordinaten gemäß der Darstellung durch das WGS 84-Referenzsystem. Nachstehend finden Sie weitere Informationen zu Koordinatenreferenzsystemen.

### <a name="linestrings"></a>LineStrings

**LineStrings** stellen eine Reihe von zwei oder mehr Punkten im Raum sowie die Liniensegmente dar, die diese verbinden. In Geodaten werden LineStrings häufig zum Darstellen von Straßen oder Flüssen verwendet.

**LineStrings in GeoJSON**

```json
    "type":"LineString",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ]
    ] ]
```

### <a name="polygons"></a>Polygone

Ein **Polygon** ist eine Umgrenzung miteinander verbundener Punkte, die eine geschlossene LineString bilden. Polygone dienen meist zum Darstellen natürlicher Gebilde wie Seen oder geopolitischer Gebilde wie Städte und Staaten. Das folgende Beispiel zeigt ein Polygon in Azure Cosmos DB:

**Polygone in GeoJSON**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> Die GeoJSON-Spezifikation erfordert, dass für gültige Polygone das letzte angegebene Koordinatenpaar mit dem ersten identisch sein muss, um eine geschlossene Form zu bilden.
>
> Punkte innerhalb eines Polygons müssen gegen den Uhrzeigersinn nacheinander angegeben werden. Ein Polygon, das im Uhrzeigersinn angegeben wird, stellt die Umkehrung der darin enthaltenen Region dar.
>
>

### <a name="multipolygons"></a>MultiPolygons

Bei **MultiPolygons** handelt es sich um ein Array mit null oder mehr Polygonen. **MultiPolygons** dürfen sich weder an den Seiten überschneiden noch gemeinsame Bereiche aufweisen. Sie dürfen sich allerdings an einzelnen oder mehreren Punkten berühren.

**MultiPolygons in GeoJSON**

```json
{
    "type":"MultiPolygon",
    "coordinates":[[[
        [52.0, 12.0],
        [53.0, 12.0],
        [53.0, 13.0],
        [52.0, 13.0],
        [52.0, 12.0]
        ]],
        [[
        [50.0, 0.0],
        [51.0, 0.0],
        [51.0, 5.0],
        [50.0, 5.0],
        [50.0, 0.0]
        ]]]
}
```

## <a name="coordinate-reference-systems"></a>Koordinatenreferenzsysteme

Da die Form der Erde unregelmäßig ist, werden Koordinaten räumlicher Geografiedaten in zahlreichen Koordinatenreferenzsystemen (KRS) abgebildet, die jeweils eigene Bezugsrahmen und Maßeinheiten aufweisen. Das „National Grid of Britain“ ist beispielsweise ein Referenzsystem, das sehr genau für Großbritannien ist, außerhalb davon hingegen nicht.

Das am häufigsten verwendete KRS ist derzeit das World Geodetic System [WGS-84](https://earth-info.nga.mil/GandG/update/index.php). GPS-Geräte und viele Kartendienste einschließlich Google Maps- und Bing Maps-APIs verwenden WGS-84. Azure Cosmos DB unterstützt ausschließlich die Indizierung und Abfrage räumlicher Geografiedaten mit dem Koordinatenreferenzsystem WGS-84.

## <a name="creating-documents-with-spatial-data"></a>Erstellen von Dokumenten mit räumlichen Daten
Beim Erstellen von Dokumenten, die GeoJSON-Werte enthalten, werden diese automatisch mit einem räumlichen Index in Übereinstimmung mit der Richtlinie für die Indizierung des Containers indiziert. Wenn Sie mit einem Azure Cosmos DB SDK in einer dynamisch typisierten Sprache wie Python oder Node.js arbeiten, müssen Sie gültige GeoJSON erstellen.

**Erstellen von Dokumenten mit Geodaten in Node.js**

```javascript
var userProfileDocument = {
    "id":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

Bei Verwendung der SQL-APIs können Sie die Klassen `Point`, `LineString`, `Polygon` und `MultiPolygon` im Namespace `Microsoft.Azure.Cosmos.Spatial` verwenden, um Standortinformationen in Ihre Anwendungsobjekte einzubetten. Diese Klassen vereinfachen die Serialisierung und Deserialisierung räumlicher Daten in GeoJSON.

**Erstellen von Dokumenten mit Geodaten in .NET**

```csharp
using Microsoft.Azure.Cosmos.Spatial;

public class UserProfile
{
    [JsonProperty("id")]
    public string id { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await container.CreateItemAsync( new UserProfile
    {
        id = "cosmosdb",
        Location = new Point (-122.12, 47.66)
    });
```

Wenn Sie die Informationen zu Breiten- und Längengrad nicht haben, aber über die physischen Adressen und Standortnamen wie Stadt oder Land/Region verfügen, können Sie die tatsächlichen Koordinaten mithilfe eines Geocodierungsdiensts wie Bing Maps REST Services nachschlagen. [Hier](/bingmaps/rest-services/)erfahren Sie mehr zur Bing Maps-Geocodierung.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die ersten Schritte mit räumlichen Daten in Azure Cosmos DB erlernt haben, haben Sie folgende Möglichkeiten:

* Weitere Informationen zur [Azure Cosmos DB-Abfrage](sql-query-getting-started.md)
* Weitere Informationen zum [Abfragen räumlicher Daten mit Azure Cosmos DB](sql-query-geospatial-query.md)
* Informieren Sie sich ausführlicher über das [Indizieren von räumlichen Daten mit Azure Cosmos DB](sql-query-geospatial-index.md).