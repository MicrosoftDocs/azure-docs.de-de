---
title: Dokumentspezifisches TTL-Feature von MongoDB in Azure Cosmos DB
description: Erfahren Sie, wie Sie mit der API für MongoDB von Azure Cosmos DB die Gültigkeitsdauer (Time To Live, TTL) für Dokumente festlegen, sodass sie nach einer bestimmten Zeit automatisch aus dem System gelöscht werden.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: javascript
ms.topic: how-to
ms.date: 12/26/2018
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6ef3092c611e1eede2be1e0994ce69959a46f1d7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340922"
---
# <a name="expire-data-with-azure-cosmos-dbs-api-for-mongodb"></a>Ablauf von Daten mit der API für MongoDB von Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Mithilfe de TTL-Funktion (Time To Live, Gültigkeitsdauer) kann die Gültigkeit von Daten in der Datenbank automatisch ablaufen. Die API für MongoDB von Azure Cosmos DB nutzt die TTL-Kernfunktionen von Azure Cosmos DB. Unterstützt werden zwei Modi: Festlegen eines TTL-Standardwerts für die gesamte Sammlung und Festlegen individueller TTL-Werte für die einzelnen Dokumente. Die Logik, die TTL-Indizes und dokumentspezifischen TTL-Werten in der API für MongoDB von Cosmos DB zugrunde liegt, ist die [gleiche wie in Cosmos DB](../cosmos-db/mongodb-indexing.md).

## <a name="ttl-indexes"></a>TTL-Indizes
Wenn Sie eine universelle Gültigkeitsdauer für eine Sammlung verwenden möchten, muss ein [„TTL-Index“ (Index für die Gültigkeitsdauer)](../cosmos-db/mongodb-indexing.md) erstellt werden. Der TTL-Index ist ein Index im Feld „_ts“ mit einem expireAfterSeconds-Wert.

Beispiel:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Der Befehl im obigen Beispiel erstellt einen Index mit TTL-Funktion. Nach Erstellung des Index löscht die Datenbank automatisch alle Dokumente in dieser Sammlung, die in den letzten zehn Sekunden nicht geändert wurden. 

> [!NOTE]
> **_ts** ist ein Cosmos DB-spezifisches Feld, auf das über MongoDB-Clients nicht zugegriffen werden kann. Dies ist eine reservierte Eigenschaft (Systemeigenschaft), die den Zeitstempel der letzten Änderung eines Dokuments enthält.
>
    
Zusätzliches C#-Beispiel: 

```csharp
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>Festlegen eines TTL-Werts für ein Dokument 
Es werden auch dokumentspezifische TTL-Werte unterstützt. Die Dokumente müssen eine Stammebeneneigenschaft vom Typ „ttl“ (in Kleinbuchstaben) enthalten, und es muss wie weiter oben beschrieben ein TTL-Index für die Sammlung vorhanden sein. Für ein Dokument festgelegte TTL-Werte haben Vorrang vor dem TTL-Wert einer Sammlung.

Der TTL-Wert muss als int32-Wert angegeben werden. Alternativ kann auch ein int64-Wert verwendet werden, der in einen int32-Wert passt, oder ein double-Wert ohne Dezimalstellen, der in einen int32 passt. Von diesen Spezifikationen abweichende Werte für die TTL-Eigenschaft sind zwar zulässig, werden aber nicht als relevante dokumentspezifische TTL-Werte behandelt.

Der TTL-Wert für das Dokument ist optional. Dokumente können auch ohne TTL-Wert in die Sammlung eingefügt werden.  In diesem Fall gilt der TTL-Wert der Sammlung. 

Die folgenden Dokumente verfügen über gültige TTL-Werte. Die dokumentspezifischen TTL-Werte eingefügter Dokumente haben Vorrang vor den TTL-Werten der Sammlung. Die Dokumente werden daher nach 20 Sekunden entfernt.   

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

Die TTL-Werte der folgenden Dokumente sind ungültig. Die Dokumente werden zwar eingefügt, der dokumentspezifische TTL-Wert wird jedoch nicht berücksichtigt. Dies führt dazu, dass die Dokumente gemäß dem TTL-Wert der Sammlung nach zehn Sekunden entfernt werden. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="how-to-activate-the-per-document-ttl-feature"></a>Aktivieren des dokumentspezifischen TTL-Features

Das dokumentspezifische TTL-Feature kann mit der API für MongoDB von Azure Cosmos DB aktiviert werden.

:::image type="content" source="./media/mongodb-ttl/mongodb_portal_ttl.png" alt-text="Screenshot der Aktivierung des dokumentspezifischen TTL-Features im Portal":::

## <a name="next-steps"></a>Nächste Schritte
* [Festlegen einer Gültigkeitsdauer für den automatischen Ablauf von Daten in Azure Cosmos DB](../cosmos-db/time-to-live.md)
* [Indizieren einer Cosmos-Datenbank, die mit der API für MongoDB von Azure Cosmos DB konfiguriert ist](../cosmos-db/mongodb-indexing.md)
