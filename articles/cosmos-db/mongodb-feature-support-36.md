---
title: 'Azure Cosmos DB-API für MongoDB (Version 3.6): unterstützte Features und Syntax'
description: Hier finden Sie Informationen zu unterstützten Features und zur Syntax der Azure Cosmos DB-API für MongoDB (Version 3.6).
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 08/07/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: bb9efa3fde0ed840589b66db7b28392de67ee8dd
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94635591"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>Azure Cosmos DB-API für MongoDB (Version 3.6): unterstützte Features und Syntax
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen. Sie können mit der API für MongoDB von Azure Cosmos DB über einen der Open-Source-MongoDB-Clienttreiber kommunizieren. (Diese Treiber finden Sie [hier](https://docs.mongodb.org/ecosystem/drivers)). Die API für MongoDB von Azure Cosmos DB ermöglicht die Verwendung vorhandener Clienttreiber durch Nutzung des [Wire Protocol](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol) von MongoDB.

Mit der API für MongoDB von Azure Cosmos DB können Sie die Vorteile der vertrauten MongoDB mit allen Unternehmensfunktionen von Cosmos DB kombinieren. Hierzu zählen unter anderem [globale Verteilung](distribute-data-globally.md), [automatisches Sharding](partitioning-overview.md), Gewährleistung der Verfügbarkeit und Latenz, Verschlüsselung ruhender Daten sowie Sicherungen.

## <a name="protocol-support"></a>Protokollunterstützung

Die API für MongoDB von Azure Cosmos DB ist bei neuen Konten standardmäßig mit der MongoDB-Serverversion **3.6** kompatibel. Die unterstützten Operatoren und alle Einschränkungen oder Ausnahmen sind unten aufgeführt. Alle Clienttreiber, die diese Protokolle verstehen, sollten auch mit der API für MongoDB von Azure Cosmos DB eine Verbindung herstellen können. Beachten Sie bei Verwendung von Konten mit der MongoDB-API von Azure Cosmos DB, dass der Endpunkt bei der Kontoversion 3.6 das Format `*.mongo.cosmos.azure.com` hat, während der Endpunkt bei der Kontoversion 3.2 das Format `*.documents.azure.com` hat.

## <a name="query-language-support"></a>Unterstützung der Abfragesprache

Die API für MongoDB von Azure Cosmos DB bietet umfassende Unterstützung für MongoDB-Abfragesprachkonstrukte. Im Anschluss finden Sie eine detaillierte Aufstellung der aktuell unterstützten Vorgänge, Operatoren, Phasen, Befehle und Optionen:

## <a name="database-commands"></a>Datenbankbefehle

Die API für MongoDB von Azure Cosmos DB unterstützt die folgenden Datenbankbefehle:

### <a name="query-and-write-operation-commands"></a>Befehle für Abfrage- und Schreibvorgänge

|Get-Help  |Unterstützt |
|---------|---------|
|delete | Ja |
|Suchen | Ja     |
|findAndModify | Ja  |
|getLastError|   Ja |
|getMore  |  Ja  |
|getPrevError | Nein  |
|insert  |   Ja  |
|parallelCollectionScan  | Ja   |
|resetError |    Nein  |
|aktualisieren  |   Ja  |
|[Ändern von Datenströmen](mongodb-change-streams.md)  |  Ja  |
|GridFS |   Ja  |

### <a name="authentication-commands"></a>Authentifizierungsbefehle

|Get-Help  |Unterstützt |
|---------|---------|
|authenticate    |   Ja      |
|logout    |      Ja   |
|getnonce   |    Ja     |


### <a name="administration-commands"></a>Verwaltungsbefehle

|Get-Help  |Unterstützt |
|---------|---------|
|Sammlungen mit Obergrenze   |   Nein      |
|cloneCollectionAsCapped     |   Nein      |
|collMod     |   Nein      |
|collMod: expireAfterSeconds   |   Nein      |
|convertToCapped   |  Nein       |
|copydb     |  Nein       |
|create   |    Ja     |
|createIndexes     |  Ja       |
|currentOp     |  Ja       |
|drop     |   Ja      |
|dropDatabase     |  Ja       |
|dropIndexes     |   Ja      |
|filemd5    |   Ja      |
|killCursors    |  Ja       |
|killOp     |   Nein      |
|listCollections     |  Ja       |
|listDatabases     |  Ja       |
|listIndexes     |  Ja       |
|reIndex     |    Ja     |
|renameCollection     |    Nein     |
|connectionStatus    |     Nein    |

### <a name="diagnostics-commands"></a>Diagnosebefehle

|Get-Help  |Unterstützt |
|---------|---------|
|buildInfo         |   Ja      |
|collStats    |  Ja       |
|connPoolStats     |  Nein       |
|connectionStatus     |  Nein       |
|dataSize     |   Nein      |
|dbHash    |    Nein     |
|dbStats     |   Ja      |
|explain     |   Ja      |
|explain: executionStats     |   Ja      |
|Features     |    Nein     |
|hostInfo     |   Nein      |
|listDatabases         |   Ja      |
|listCommands     |  Nein       |
|profiler     |  Nein       |
|serverStatus     |  Nein       |
|top     |    Nein     |
|whatsmyuri     |   Ja      |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Aggregationspipeline</a>

### <a name="aggregation-commands"></a>Aggregationsbefehle

|Get-Help  |Unterstützt |
|---------|---------|
|aggregate |   Ja  |
|count     |   Ja  |
|distinct  | Ja |
|mapReduce | Nein |

### <a name="aggregation-stages"></a>Aggregationsphasen

|Get-Help  |Unterstützt |
|---------|---------|
|$collStats    |Nein|
|$project    |Ja|
|$match    |Ja|
|$redact|    Ja|
|$limit    |Ja|
|$skip    |Ja|
|$unwind|    Ja|
|$group    |    Ja|
|$sample|        Ja|
|$sort    |Ja|
|$geoNear|    Nein|
|$lookup    |    Ja|
|$out        |Ja|
|$indexStats|        Nein|
|$facet    |Ja|
|$bucket|    Nein|
|$bucketAuto|    Nein|
|$sortByCount|    Ja|
|$addFields    |Ja|
|$replaceRoot|    Ja|
|$count    |Ja|
|$currentOp|    Nein|
|$listLocalSessions    |Nein|
|$listSessions    |Nein|
|$graphLookup    |Ja|

### <a name="boolean-expressions"></a>Boolesche Ausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$and| Ja|
|$or|Ja|
|$not|Ja|

### <a name="set-expressions"></a>Set-Ausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
| $setEquals | Ja|
|$setIntersection|Ja|
| $setUnion|Ja|
| $setDifference|Ja|
| $setIsSubset|Ja|
| $anyElementTrue|Ja|
| $allElementsTrue|Ja|

### <a name="comparison-expressions"></a>Vergleichsausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$cmp     |  Ja       |
|$eq|    Ja| 
|$gt |    Ja| 
|$gte|    Ja| 
|$lt    |Ja|
|$lte|    Ja| 
|$ne    |    Ja| 
|$in    |    Ja| 
|$nin    |    Ja| 

### <a name="arithmetic-expressions"></a>Arithmetische Ausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$abs |  Ja       |
| $add |  Ja       |
| $ceil |  Ja       |
| $divide |  Ja       |
| $exp |  Ja       |
| $floor |  Ja       |
| $ln |  Ja       |
| $log |  Ja       |
| $log10 |  Ja       |
| $mod |  Ja       |
| $multiply |  Ja       |
| $pow |  Ja       |
| $sqrt |  Ja       |
| $subtract |  Ja       |
| $trunc |  Ja       |

### <a name="string-expressions"></a>Zeichenfolgenausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$concat |  Ja       |
| $indexOfBytes|  Ja       |
| $indexOfCP|  Ja       |
| $split|  Ja       |
| $strLenBytes|  Ja       |
| $strLenCP|  Ja       |
| $strcasecmp|  Ja       |
| $substr|  Ja       |
| $substrBytes|  Ja       |
| $substrCP|  Ja       |
| $toLower|  Ja       |
| $toUpper|  Ja       |

### <a name="text-search-operator"></a>Operator für die Textsuche

|Get-Help  |Unterstützt |
|---------|---------|
| $meta | Nein|

### <a name="array-expressions"></a>Arrayausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$arrayElemAt    |    Ja|
|$arrayToObject|    Ja|
|$concatArrays    |    Ja|
|$filter    |    Ja|
|$indexOfArray    |Ja|
|$isArray    |    Ja|
|$objectToArray    |Ja|
|$range    |Ja|
|$reverseArray    |    Ja|
|$reduce|    Ja|
|$size    |    Ja|
|$slice    |    Ja|
|$zip    |    Ja|
|$in    |    Ja|

### <a name="variable-operators"></a>Variablenoperatoren

|Get-Help  |Unterstützt |
|---------|---------|
|$map    |Nein|
|$let    |Ja|

### <a name="system-variables"></a>Systemvariablen

|Get-Help  |Unterstützt |
|---------|---------|
|$$CURRENT|    Ja|
|$$DESCEND|        Ja|
|$$KEEP        |Ja|
|$$PRUNE    |    Ja|
|$$REMOVE    |Ja|
|$$ROOT        |Ja|

### <a name="literal-operator"></a>Literaloperator

|Get-Help  |Unterstützt |
|---------|---------|
|$literal    |Ja|

### <a name="date-expressions"></a>Datumsausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$dayOfYear    |Ja    |
|$dayOfMonth|    Ja    |
|$dayOfWeek    |Ja    |
|$year    |Ja    |
|$month    |Ja|    
|$week    |Ja    |
|$hour    |Ja    |
|$minute|    Ja|    
|$second    |Ja    |
|$millisecond|    Ja|    
|$dateToString    |Ja    |
|$isoDayOfWeek    |Ja    |
|$isoWeek    |Ja    |
|$dateFromParts|    Nein|    
|$dateToParts    |Nein    |
|$dateFromString|    Nein|
|$isoWeekYear    |Ja    |

### <a name="conditional-expressions"></a>Bedingte Ausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
| $cond| Ja|
| $ifNull| Ja|
| $switch |Ja|

### <a name="data-type-operator"></a>Datentypoperator

|Get-Help  |Unterstützt |
|---------|---------|
| $type| Ja|

### <a name="accumulator-expressions"></a>Akkumulatorausdrücke

|Get-Help  |Unterstützt |
|---------|---------|
|$sum    |Ja    |
|$avg    |Ja    |
|$first|    Ja|
|$last    |Ja    |
|$max    |Ja    |
|$min    |Ja    |
|$push|    Ja|
|$addToSet|    Ja|
|$stdDevPop|    Nein    |
|$stdDevSamp|    Nein|

### <a name="merge-operator"></a>Zusammenführungsoperator

|Get-Help  |Unterstützt |
|---------|---------|
| $mergeObjects | Ja|

## <a name="data-types"></a>Datentypen

|Get-Help  |Unterstützt |
|---------|---------|
|Double    |Ja    |
|String    |Ja    |
|Object    |Ja    |
|Array    |Ja    |
|Binary Data    |Ja|    
|ObjectID    |Ja    |
|Boolean    |Ja    |
|Date    |Ja    |
|Null    |Ja    |
|32-Bit-Ganzzahl (int)    |Ja    |
|Timestamp    |Ja    |
|64-Bit-Ganzzahl (long)    |Ja    |
|MinKey    |Ja    |
|MaxKey    |Ja    |
|Decimal128    |Ja|    
|Regular Expression    |Ja|
|JavaScript    |Ja|
|JavaScript (mit Gültigkeitsbereich)|    Ja    |
|Nicht definiert    |Ja    |

## <a name="indexes-and-index-properties"></a>Indizes und Indexeigenschaften

### <a name="indexes"></a>Indizes

|Get-Help  |Unterstützt |
|---------|---------|
|Einzelfeldindex    |Ja    |
|Verbundindex    |Ja    |
|Index mit mehreren Schlüsseln    |Ja    |
|Textindex    |Nein|
|2dsphere    |Ja    |
|2D-Index    |Nein    |
|Hashindex    | Ja|

### <a name="index-properties"></a>Indexeigenschaften

|Get-Help  |Unterstützt |
|---------|---------|
|TTL|    Ja    |
|Eindeutig    |Ja|
|Teilweise|    Nein|
|Keine Beachtung von Groß-/Kleinschreibung    |Nein|
|Platzsparend    |Nein |
|Hintergrund|    Ja |

## <a name="operators"></a>Operatoren

### <a name="logical-operators"></a>Logische Operatoren

|Get-Help  |Unterstützt |
|---------|---------|
|$or    |    Ja|
|$and    |    Ja|
|$not    |    Ja|
|$nor    |    Ja| 

### <a name="element-operators"></a>Elementoperatoren

|Get-Help  |Unterstützt |
|---------|---------|
|$exists|    Ja|
|$type    |    Ja|

### <a name="evaluation-query-operators"></a>Abfrageoperatoren für die Auswertung

|Get-Help  |Unterstützt |
|---------|---------|
|$expr    |    Nein|
|$jsonSchema    |    Nein|
|$mod    |    Ja|
|$regex |    Ja|
|$text    | Nein (Nicht unterstützt. Verwenden Sie stattdessen „$regex“.)| 
|$where    |Nein| 

In $regex-Abfragen ermöglichen linksverankerte Ausdrücke eine Indexsuche. Die Verwendung des „i“-Modifizierers (keine Berücksichtigung der Groß-/Kleinschreibung) und des „m“-Modifizierers (mehrere Zeilen) führt jedoch zur Sammlungsüberprüfung in allen Ausdrücken.

Wenn „$“ oder „|“ eingeschlossen werden muss, empfiehlt es sich, zwei (oder mehr) RegEx-Abfragen zu erstellen. Die folgende ursprüngliche Abfrage ```find({x:{$regex: /^abc$/})``` muss beispielsweise wie folgt geändert werden:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Der erste Teil verwendet den Index zum Einschränken der Suche auf die Dokumente, die mit „^abc“ beginnen, und der zweite Teil stimmt die exakten Einträge ab. Der Strichoperator „|“ dient als „oder“-Funktion: Die Abfrage ```find({x:{$regex: /^abc|^def/})``` stimmt die Dokumente ab, in denen das Feld „x“ Werte enthält, die mit „abc“ oder „def“ beginnen. Zur Nutzung des Index wird empfohlen, die Abfrage in zwei unterschiedliche Abfragen zu unterteilen, die durch den „$or“-Operator verbunden sind: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Arrayoperatoren

|Get-Help  |Unterstützt | 
|---------|---------|
| $all | Ja| 
| $elemMatch | Ja| 
| $size | Ja | 

### <a name="comment-operator"></a>Kommentaroperator

|Get-Help  |Unterstützt | 
|---------|---------|
$comment |Ja| 

### <a name="projection-operators"></a>Projektionsoperatoren

|Get-Help  |Unterstützt |
|---------|---------|
|$elemMatch    |Ja|
|$meta|    Nein|
|$slice    | Ja|

### <a name="update-operators"></a>Aktualisierungsoperatoren

#### <a name="field-update-operators"></a>Operatoren für die Feldaktualisierung

|Get-Help  |Unterstützt |
|---------|---------|
|$inc    |    Ja|
|$mul    |    Ja|
|$rename    |    Ja|
|$setOnInsert|    Ja|
|$set    |Ja|
|$unset| Ja|
|$min    |Ja|
|$max    |Ja|
|$currentDate    | Ja|

#### <a name="array-update-operators"></a>Operatoren für die Arrayaktualisierung

|Get-Help  |Unterstützt |
|---------|---------|
|$    |Ja|
|$[]|    Ja|
|$[<identifier>]|    Ja|
|$addToSet    |Ja|
|$pop    |Ja|
|$pullAll|    Ja|
|$pull    |Ja|
|$push    |Ja|
|$pushAll| Ja|


#### <a name="update-modifiers"></a>Aktualisierungsmodifizierer

|Get-Help  |Unterstützt |
|---------|---------|
|$each    |    Ja|
|$slice    |Ja|
|$sort    |Ja|
|$position    |Ja|

#### <a name="bitwise-update-operator"></a>Bitweiser Updateoperator

|Get-Help  |Unterstützt |
|---------|---------|
| $bit    |    Ja|    
|$bitsAllSet    |    Nein|
|$bitsAnySet    |    Nein|
|$bitsAllClear    |Nein|
|$bitsAnyClear    |Nein|

### <a name="geospatial-operators"></a>Räumliche Operatoren

Operator | Unterstützt| 
--- | --- |
$geoWithin | Ja |
$geoIntersects | Ja | 
$near |  Ja |
$nearSphere |  Ja |
$geometry |  Ja |
$minDistance | Ja |
$maxDistance | Ja |
$center | Nein  |
$centerSphere | Nein  |
$box | Nein  |
$polygon |  Nein  |

## <a name="cursor-methods"></a>Cursormethoden

|Get-Help  |Unterstützt |
|---------|---------|
|cursor.batchSize()    |    Ja|
|cursor.close()    |Ja|
|cursor.isClosed()|        Ja|
|cursor.collation()|    Nein|
|cursor.comment()    |Ja|
|cursor.count()    |Ja|
|cursor.explain()|    Nein|
|cursor.forEach()    |Ja|
|cursor.hasNext()    |Ja|
|cursor.hint()    |Ja|
|cursor.isExhausted()|    Ja|
|cursor.itcount()    |Ja|
|cursor.limit()    |Ja|
|cursor.map()    |Ja|
|cursor.maxScan()    |Ja|
|cursor.maxTimeMS()|    Ja|
|cursor.max()    |Ja|
|cursor.min()    |Ja|
|cursor.next()|    Ja|
|cursor.noCursorTimeout()    |Nein|
|cursor.objsLeftInBatch()    |Ja|
|cursor.pretty()|    Ja|
|cursor.readConcern()|    Ja|
|cursor.readPref()        |Ja|
|cursor.returnKey()    |Nein|
|cursor.showRecordId()|    Nein|
|cursor.size()    |Ja|
|cursor.skip()    |Ja|
|cursor.sort()    |    Ja|
|cursor.tailable()|    Nein|
|cursor.toArray()    |Ja|

## <a name="sort-operations"></a>Sortiervorgänge

Bei Verwendung des `findOneAndUpdate`-Vorgangs werden Sortiervorgänge für ein einzelnes Feld unterstützt, Sortiervorgänge für mehrere Felder jedoch nicht.

## <a name="unique-indexes"></a>Eindeutige Indizes

[Eindeutige Indizes](mongodb-indexing.md#unique-indexes) stellen sicher, dass ein bestimmtes Feld in den gesamten Dokumenten einer Sammlung keine doppelten Werte enthält. Dies ist vergleichbar mit der Bewahrung der Eindeutigkeit für den Standardschlüssel „_id“. Sie können eindeutige Indizes in Cosmos DB erstellen, indem Sie den Befehl `createIndex` mit dem Einschränkungsparameter `unique` verwenden:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Zusammengesetzte Indizes

[Zusammengesetzte Indizes](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) sind eine Möglichkeit, einen Index für Feldergruppen (bis zu acht Felder) zu erstellen. Dieser Indextyp unterscheidet sich von den nativen zusammengesetzten MongoDB-Indizes. In Azure Cosmos DB werden zusammengesetzte Indizes für Sortiervorgänge verwendet, die auf mehrere Felder angewendet werden. Um einen zusammengesetzten Index zu erstellen, müssen Sie mehr als eine Eigenschaft als Parameter angeben:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="time-to-live-ttl"></a>Gültigkeitsdauer (TTL)

Cosmos DB unterstützt eine Gültigkeitsdauer (Time-to-live, TTL) basierend auf dem Zeitstempel des Dokuments. TTL kann für Sammlungen über das [Azure-Portal](https://portal.azure.com) aktiviert werden.

## <a name="user-and-role-management"></a>Benutzer- und Rollenverwaltung

Cosmos DB unterstützt noch keine Benutzer und Rollen. Cosmos DB unterstützt jedoch die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) sowie Lese-/Schreibkennwörter/-schlüssel und Schreibschutzkennwörter/-schlüssel, die über das [Azure-Portal](https://portal.azure.com) (Seite „Verbindungszeichenfolge“) abgerufen werden können.

## <a name="replication"></a>Replikation

Cosmos DB unterstützt die automatische, native Replikation auf den niedrigsten Ebenen. Diese Logik wird erweitert, um auch die globale Replikation mit geringer Latenz zu erreichen. Cosmos DB unterstützt keine manuellen Replikationsbefehle.

## <a name="write-concern"></a>Schreibbestätigung

Einige Anwendungen unterstützen eine [Schreibbestätigung](https://docs.mongodb.com/manual/reference/write-concern/). Diese gibt die Anzahl der Antworten an, die während eines Schreibvorgangs erforderlich sind. Aufgrund der Art und Weise, in der Cosmos DB die Replikation im Hintergrund durchführt, gilt für alle Schreibvorgänge automatisch und standardmäßig ein Quorum. Alle Schreibvorgänge, die durch den Clientcode angegeben werden, werden ignoriert. Weitere Informationen finden Sie unter [Verwenden von Konsistenzebenen zum Maximieren der Verfügbarkeit und Leistung](consistency-levels.md).

## <a name="sharding"></a>Sharding (Horizontales Partitionieren)

Azure Cosmos DB unterstützt das automatische, serverseitige Sharding. Die Erstellung, die Platzierung und der Ausgleich von Shards wird automatisch verwaltet. Azure Cosmos DB unterstützt keine manuellen Shardingbefehle. Das bedeutet, dass Sie keine Befehle wie „addShard“, „balancerStart“, „moveChunk“ usw. aufrufen müssen. Sie müssen beim Erstellen der Container oder beim Abfragen der Daten nur den Shardschlüssel angeben.

## <a name="sessions"></a>Sitzungen

Azure Cosmos DB unterstützt noch keine serverseitigen Sitzungsbefehle.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Features der Mongo-Version 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.

<sup>Hinweis: Dieser Artikel beschreibt ein Feature von Azure Cosmos DB, das Wire Protocol-Kompatibilität mit MongoDB-Datenbanken bietet. Microsoft führt keine MongoDB-Datenbanken aus, um diesen Dienst bereitzustellen. Azure Cosmos DB ist kein Partner von MongoDB, Inc.</sup>
