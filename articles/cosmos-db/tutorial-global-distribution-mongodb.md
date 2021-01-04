---
title: Tutorial zum Einrichten der globalen Verteilung mit der Azure Cosmos DB-API für MongoDB
description: Es wird beschrieben, wie Sie die globale Verteilung mit der Azure Cosmos DB-API für MongoDB einrichten.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 89826eab7b1686ae695a2716a03b2f5d03da277f
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099250"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Einrichten einer global verteilten Datenbank mit der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In diesem Artikel wird veranschaulicht, wie Sie über das Azure-Portal eine global verteilte Datenbank einrichten und mit der Azure Cosmos DB-API für MongoDB eine Verbindung damit herstellen.

In diesem Artikel werden die folgenden Aufgaben behandelt: 

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mit der [Azure Cosmos DB-API für MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Überprüfen der regionalen Einrichtung 
Eine einfache Möglichkeit zur Überprüfung Ihrer globalen Konfiguration mit der Cosmos DB-API für MongoDB besteht darin, den Befehl *isMaster()* in der Mongo Shell auszuführen.

In der Mongo Shell:

   ```
      db.isMaster()
   ```
   
Beispielergebnisse:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Herstellen einer Verbindung mit einer bevorzugten Region 

Die Azure Cosmos DB-API für MongoDB ermöglicht es Ihnen, Voreinstellungen für Lesevorgänge in Ihrer Sammlung für eine global verteilte Datenbank anzugeben. Sowohl für Lesevorgänge mit niedriger Latenz als auch für eine globale Hochverfügbarkeit empfiehlt es sich, die Voreinstellung Ihrer Sammlung für Lesevorgänge auf *nächste* festzulegen. Mit der Einstellung *nächste* erfolgen Lesevorgänge aus der nächstgelegenen Region.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Bei Anwendungen mit einer primären Lese-/Schreibregion und einer sekundären Region für Notfallwiederherstellungsszenarien empfiehlt es sich, die Voreinstellung Ihrer Sammlung für Lesevorgänge auf *sekundäre bevorzugte* festzulegen. Mit der Einstellung *sekundäre bevorzugte* erfolgen Lesevorgänge aus der sekundären Region, wenn die primäre Region nicht verfügbar ist.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Sie können Leseregionen auch manuell angeben. Legen Sie das entsprechende Regionstag in Ihrer Voreinstellung für Lesevorgänge fest.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Das ist alles, und dieses Tutorial ist abgeschlossen. Informationen dazu, wie Sie die Konsistenz Ihres global replizierten Kontos verwalten, finden Sie unter [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md). Weitere Informationen zur Funktionsweise der globalen Datenbankreplikation in Azure Cosmos DB finden Sie unter [Globale Verteilung von Daten mit Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Konfigurieren der globalen Verteilung mit dem Azure-Portal
> * Konfigurieren der globalen Verteilung mit der Cosmos DB-API für MongoDB

Sie können jetzt mit dem nächsten Tutorial fortfahren, um zu erfahren, wie Sie mit dem lokalen Azure Cosmos DB-Emulator lokal entwickeln können.

> [!div class="nextstepaction"]
> [Lokale Entwicklung mit dem Azure Cosmos DB-Emulator](local-emulator.md)
