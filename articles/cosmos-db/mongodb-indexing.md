---
title: Indizieren in der API für MongoDB von Azure Cosmos-DB
description: Enthält eine Übersicht über die Indizierungsfunktionen mit der API für MongoDB von Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: de037316efa50dd25ea04c370fa0e5878fb52ba1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448502"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indizieren mit der API für MongoDB von Azure Cosmos-DB

Die API für MongoDB von Azure Cosmos DB nutzt die Funktionen für automatische Indexverwaltung von Cosmos DB. Daher haben Benutzer Zugriff auf die Standardindizierungsrichtlinien von Cosmos DB. Wenn vom Benutzer also keine Indizes definiert oder gelöscht wurden, werden standardmäßig alle Felder beim Einfügen in eine Sammlung automatisch indiziert. Für die meisten Szenarien empfehlen wir, die Standardindizierungsrichtlinie des Kontos zu verwenden.

## <a name="dropping-the-default-indexes"></a>Löschen der Standardindizes

Der folgende Befehl kann verwendet werden, um die Standardindizes für die Sammlung ```coll``` zu löschen:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

## <a name="creating-compound-indexes"></a>Erstellen von zusammengesetzten Indizes

Zusammengesetzte Indizes enthalten Verweise auf mehrere Felder eines Dokuments. In logischer Hinsicht entspricht dies dem Erstellen von mehreren einzelnen Indizes pro Feld. Um in den Genuss der Vorteile von Optimierungen durch Cosmos DB-Indizierungsverfahren zu kommen, empfehlen wir die Erstellung von mehreren einzelnen Indizes anstelle eines einzelnen (nicht eindeutigen) zusammengesetzten Index.

## <a name="creating-unique-indexes"></a>Erstellen von eindeutigen Indizes

[Eindeutige Indizes](unique-keys.md) sind nützlich zum Erzwingen eines Zustands, in dem zwei oder mehr Dokumente den gleichen Wert für die indizierten Felder enthalten. 
>[!important] 
> Derzeit können eindeutige Indizes nur erstellt werden, wenn die Sammlung leer ist (keine Dokumente enthält). 

Mit dem folgenden Befehl wird ein eindeutiger Index im Feld „student_id“ erstellt:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} ) 
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Für Sammlungen mit Shards ist für die Erstellung eines eindeutigen Index gemäß MongoDB-Verhalten die Angabe des Shard-Schlüssels (Partitionsschlüssel) erforderlich. Anders ausgedrückt: Alle eindeutigen Indizes einer Sammlung mit Shards sind zusammengesetzte Indizes, bei denen eines der Felder der Partitionsschlüssel ist.

Mit den folgenden Befehlen wird die Sammlung ```coll``` mit Shards erstellt (Shard-Schlüssel lautet ```university```), die für die Felder „student_id“ und „university“ über einen eindeutigen Index verfügt:

```JavaScript
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

Wenn im obigen Beispiel die ```"university":1```-Klausel weggelassen wird, wird ein Fehler mit der folgenden Meldung zurückgegeben:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>TTL-Indizes

Es muss ein [„TTL-Index“ (Index für die Gültigkeitsdauer)](../cosmos-db/time-to-live.md) erstellt werden, um den Dokumentablauf in einer bestimmten Sammlung zu aktivieren. Ein TTL-Index ist ein Index im Feld „_ts“ mit dem Wert „expireAfterSeconds“.
 
Beispiel:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

Der obige Befehl bewirkt das Löschen beliebiger Dokumente in der Sammlung ```db.coll```, die innerhalb der letzten zehn Sekunden nicht geändert wurden. 
 
> [!NOTE]
> **_ts** ist ein Cosmos DB-spezifisches Feld, auf das über MongoDB-Clients nicht zugegriffen werden kann. Dies ist eine reservierte Eigenschaft (Systemeigenschaft), die den Zeitstempel der letzten Änderung eines Dokuments enthält.
>

## <a name="migrating-collections-with-indexes"></a>Migrieren von Sammlungen mit Indizes

Derzeit ist die Erstellung von eindeutigen Indizes nur möglich, wenn die Sammlung keine Dokumente enthält. Bei gängigen MongoDB-Migrationstools wird versucht, die eindeutigen Indizes nach dem Importieren der Daten zu erstellen. Zur Problemumgehung wird empfohlen, dass Benutzer die entsprechenden Sammlungen und eindeutigen Indizes manuell erstellen, anstatt das Migrationstool zuzulassen (für ```mongorestore``` wird dieses Verhalten erreicht, indem in der Befehlszeile das Flag „--noIndexRestore“ verwendet wird).

## <a name="next-steps"></a>Nächste Schritte
* [Indizierung in Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Festlegen einer Gültigkeitsdauer für den automatischen Ablauf von Daten in Azure Cosmos DB](../cosmos-db/time-to-live.md)
