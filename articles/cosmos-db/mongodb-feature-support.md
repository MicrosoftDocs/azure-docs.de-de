---
title: Verwenden der Featureunterstützung der API für MongoDB von Azure Cosmos DB
description: Erfahren Sie mehr über die Featureunterstützung, die die API für MongoDB von Azure Cosmos DB für MongoDB 3.4 bereitstellt.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 05/21/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 09d12c817e9e8678cf96c63c0bff623923f7074d
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978836"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-supported-features-and-syntax"></a>Azure Cosmos DB-API für MongoDB: unterstützte Features und Syntax

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können mit der API für MongoDB von Azure Cosmos DB über einen der Open-Source-MongoDB-Client[treiber](https://docs.mongodb.org/ecosystem/drivers) kommunizieren. Die API für MongoDB von Azure Cosmos DB ermöglicht die Verwendung vorhandener Clienttreiber durch Nutzung des [Wire Protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) von MongoDB.

Mit der API für MongoDB von Azure Cosmos DB können Sie die Vorteile der vertrauten MongoDB mit allen Unternehmensfunktionen von Cosmos DB kombinieren. Hierzu zählen unter anderem [globale Verteilung](distribute-data-globally.md), [automatisches Sharding](partition-data.md), Gewährleistung der Verfügbarkeit und Latenz, automatische Indizierung der einzelnen Felder, Verschlüsselung ruhender Daten sowie Sicherungen.

## <a name="protocol-support"></a>Protokollunterstützung

Die API für MongoDB von Azure Cosmos DB ist standardmäßig mit der MongoDB-Serverversion **3.2** kompatibel. Die unterstützten Operatoren und alle Einschränkungen oder Ausnahmen sind unten aufgeführt. Features oder Abfrageoperatoren, die in der MongoDB-Version **3.4** hinzugefügt wurden, sind derzeit als Vorschaufunktion verfügbar. Alle Clienttreiber, die diese Protokolle verstehen, sollten auch mit der API für MongoDB von Azure Cosmos DB eine Verbindung herstellen können.

Die [MongoDB-Aggregationspipeline](#aggregation-pipeline) ist derzeit auch als separate Vorschaufunktion verfügbar.

## <a name="query-language-support"></a>Unterstützung der Abfragesprache

Die API für MongoDB von Azure Cosmos DB bietet umfassende Unterstützung für MongoDB-Abfragesprachkonstrukte. Im Folgenden finden Sie die detaillierte Aufstellung der aktuell unterstützten Vorgänge, Operatoren, Phasen, Befehle und Optionen.

## <a name="database-commands"></a>Datenbankbefehle

Die API für MongoDB von Azure Cosmos DB unterstützt die folgenden Datenbankbefehle:

### <a name="query-and-write-operation-commands"></a>Befehle für Abfrage- und Schreibvorgänge
- delete
- Suchen
- findAndModify
- getLastError
- getMore
- insert
- aktualisieren

### <a name="authentication-commands"></a>Authentifizierungsbefehle
- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Verwaltungsbefehle
- dropDatabase
- listCollections
- drop
- create
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Diagnosebefehle
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Aggregationspipeline</a>

Cosmos DB unterstützt die Aggregationspipeline in der öffentlichen Vorschau. Anweisungen zur Integration der öffentlichen Vorschau finden Sie im [Azure-Blog](https://aka.ms/mongodb-aggregation).

### <a name="aggregation-commands"></a>Aggregationsbefehle
- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Aggregationsphasen
- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Aggregationsausdrücke

#### <a name="boolean-expressions"></a>Boolesche Ausdrücke
- $and
- $or
- $not

#### <a name="set-expressions"></a>Set-Ausdrücke
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Vergleichsausdrücke
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Arithmetische Ausdrücke
- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Zeichenfolgenausdrücke
- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Arrayausdrücke
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Datumsausdrücke
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Bedingte Ausdrücke
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Aggregationsakkumulatoren
- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operatoren

Folgende Operatoren werden mit den entsprechenden Verwendungsbeispielen unterstützt. Berücksichtigen Sie dieses in den folgenden Abfragen verwendete Beispieldokument:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Operator | Beispiel |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>Notizen

In $regex-Abfragen lassen linksverankerte Ausdrücke die Indexsuche zu. Die Verwendung des „i“-Modifizierers (keine Berücksichtigung der Groß-/Kleinschreibung) und des „m“-Modifizierers (mehrere Zeilen) führt jedoch zur Sammlungsüberprüfung in allen Ausdrücken.
Wenn „$“ oder „|“ eingeschlossen werden muss, empfiehlt es sich, zwei (oder mehr) RegEx-Abfragen zu erstellen. Die folgende ursprüngliche Abfrage ```find({x:{$regex: /^abc$/})``` muss beispielsweise wie folgt geändert werden: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Der erste Teil verwendet den Index zum Einschränken der Suche auf die Dokumente, die mit „^abc“ beginnen, und der zweite Teil stimmt die exakten Einträge ab. Der Strichoperator „|“ dient als „oder“-Funktion: Die Abfrage ```find({x:{$regex: /^abc|^def/})``` stimmt die Dokumente ab, in denen das Feld „x“ Werte enthält, die mit „abc“ oder „def“ beginnen. Zur Nutzung des Index wird empfohlen, die Abfrage in zwei unterschiedliche Abfragen zu unterteilen, die durch den „$or“-Operator verbunden sind: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Aktualisierungsoperatoren

#### <a name="field-update-operators"></a>Operatoren für die Feldaktualisierung
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Operatoren für die Arrayaktualisierung
- $addToSet
- $pop
- $pullAll
- $pull (Hinweis: „$pull“ mit Bedingung wird nicht unterstützt.)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Bitweiser Updateoperator
- $bit

### <a name="geospatial-operators"></a>Räumliche Operatoren

Operator | Beispiel | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ja |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ja |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Ja |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Ja |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Ja |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Ja |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Ja |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Ja |

## <a name="sort-operations"></a>Sortiervorgänge
Bei Verwendung des `findOneAndUpdate`-Vorgangs werden Sortiervorgänge für ein einzelnes Feld unterstützt, Sortiervorgänge für mehrere Felder jedoch nicht.

## <a name="additional-operators"></a>Zusätzliche Operatoren

Operator | Beispiel | Notizen 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Nicht unterstützt. Verwenden Sie stattdessen „$regex“.

## <a name="unsupported-operators"></a>Nicht unterstützte Operatoren

Die Operatoren ```$where``` und ```$eval``` werden von Azure Cosmos DB nicht unterstützt.

### <a name="methods"></a>Methoden

Folgende Methoden werden unterstützt:

#### <a name="cursor-methods"></a>Cursormethoden

Methode | Beispiel | Notizen 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumente ohne Sortierschlüssel werden nicht zurückgegeben.

## <a name="unique-indexes"></a>Eindeutige Indizes

Cosmos DB indiziert jedes Feld in Dokumenten, die standardmäßig in die Datenbank geschrieben werden. Eindeutige Indizes stellen sicher, dass ein bestimmtes Feld in allen Dokumenten einer Sammlung keine doppelten Werte enthält. Dies ähnelt der Art und Weise, auf die die Eindeutigkeit für den Standardschlüssel „_id“ beibehalten wird. Sie können benutzerdefinierte Indizes in Cosmos DB erstellen, indem Sie den Befehl „createIndex“ einschließlich der Einschränkung „unique“ verwenden.

Eindeutige Indizes sind für alle Cosmos-Konten mithilfe der API für MongoDB von Cosmos DB verfügbar.

## <a name="time-to-live-ttl"></a>Gültigkeitsdauer (TTL)

Cosmos DB unterstützt eine Gültigkeitsdauer (Time-to-live, TTL) basierend auf dem Zeitstempel des Dokuments. TTL kann für Sammlungen über das [Azure-Portal](https://portal.azure.com) aktiviert werden.

## <a name="user-and-role-management"></a>Benutzer- und Rollenverwaltung

Cosmos DB unterstützt noch keine Benutzer und Rollen. Cosmos DB unterstützt jedoch die rollenbasierte Zugriffssteuerung (RBAC) sowie Lese-/Schreibkennwörter/-schlüssel und Schreibschutzkennwörter/-schlüssel, die über das [Azure-Portal](https://portal.azure.com) (Seite „Verbindungszeichenfolge“) abgerufen werden können.

## <a name="replication"></a>Replikation

Cosmos DB unterstützt die automatische, native Replikation auf den niedrigsten Ebenen. Diese Logik wird erweitert, um auch die globale Replikation mit geringer Latenz zu erreichen. Cosmos DB unterstützt keine manuellen Replikationsbefehle.

## <a name="write-concern"></a>Schreibbestätigung

Einige Anwendungen unterstützen eine [Schreibbestätigung](https://docs.mongodb.com/manual/reference/write-concern/). Diese gibt die Anzahl der Antworten an, die während eines Schreibvorgangs erforderlich sind. Aufgrund der Art und Weise, in der Cosmos DB die Replikation im Hintergrund durchführt, gilt für alle Schreibvorgänge automatisch und standardmäßig ein Quorum. Alle Schreibvorgänge, die durch den Clientcode angegeben werden, werden ignoriert. Weitere Informationen finden Sie unter [Verwenden von Konsistenzebenen zum Maximieren der Verfügbarkeit und Leistung](consistency-levels.md).

## <a name="sharding"></a>Sharding (Horizontales Partitionieren)

Cosmos DB unterstützt automatisches, serverseitiges Sharding. Cosmos DB unterstützt keine manuellen Shardingbefehle.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.

<sup>Hinweis: Dieser Artikel beschreibt ein Feature von Azure Cosmos DB, das Wire Protocol-Kompatibilität mit MongoDB-Datenbanken bietet. Microsoft führt keine MongoDB-Datenbanken aus, um diesen Dienst bereitzustellen. Azure Cosmos DB ist kein Partner von MongoDB, Inc.</sup>
