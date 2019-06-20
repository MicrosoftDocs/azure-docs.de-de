---
title: Behandeln von Konflikten zwischen Regionen in Azure Cosmos DB
description: Behandeln von Konflikten in Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 63d8b0dc8c446f70b981aacd1a18e6a60a217983
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240946"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Verwalten von Konfliktauflösungsrichtlinien in Azure Cosmos DB

Bei Schreibvorgängen in mehreren Regionen können Konflikte auftreten, wenn mehrere Clients in das gleiche Element schreiben. Wenn es zu einem Konflikt kommt, können Sie diesen mithilfe verschiedener Konfliktlösungsrichtlinien beheben. In diesem Artikel erfahren Sie, wie Sie Konfliktlösungsrichtlinien verwalten.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>Erstellen einer Konfliktlösungsrichtlinie vom Typ „Letzter Schreiber gewinnt“

Die folgenden Beispiele zeigen, wie Sie einen Container mit einer Konfliktlösungsrichtlinie vom Typ „Letzter Schreiber gewinnt“ einrichten. Der Standardpfad für „Letzter Schreiber gewinnt“ ist das Zeitstempelfeld oder die `_ts`-Eigenschaft. Er kann auch auf einen benutzerdefinierten Pfad für einen numerischen Typ festgelegt werden. Bei einem Konflikt ist der höchste Wert der Sieger. Sollte der Pfad nicht festgelegt oder ungültig sein, wird standardmäßig `_ts` verwendet. Mit dieser Richtlinie gelöste Konflikte werden im Konfliktfeed nicht angezeigt. Diese Richtlinie kann von allen APIs verwendet werden.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java Sync SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
                'id': self.udp_collection_name,
                'conflictResolutionPolicy': {
                    'mode': 'LastWriterWins',
                    'conflictResolutionPath': '/myCustomId'
                    }
                }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>Erstellen einer benutzerdefinierten Konfliktlösungsrichtlinie mit einer gespeicherten Prozedur

Die folgenden Beispiele zeigen, wie Sie einen Container mit einer benutzerdefinierten Konfliktlösungsrichtlinie und einer gespeicherten Prozedur für die Konfliktlösung einrichten. Diese Konflikte werden nicht im Konfliktfeed angezeigt, es sei denn, die gespeicherte Prozedur enthält einen Fehler. Nachdem die Richtlinie für den Container erstellt wurde, müssen Sie die gespeicherte Prozedur erstellen. Das .NET SDK-Beispiel unten zeigt ein Beispiel. Diese Richtlinie wird nur an der Haupt-API (SQL) unterstützt.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>Beispiel für eine benutzerdefinierte gespeicherte Prozedur zur Konfliktlösung

Gespeicherte Prozeduren zur Konfliktlösung müssen mithilfe der folgenden Funktionssignatur implementiert werden. Der Funktionsname muss nicht mit dem Namen übereinstimmen, der bei der Registrierung der gespeicherten Prozedur beim Container verwendet wurde, die Namensgebung wird dadurch jedoch vereinfacht. Hier ist eine Beschreibung der Parameter, die für diese gespeicherte Prozedur implementiert werden müssen.

- **incomingItem**: Das Element in den Commit eingefügte oder im Commit aktualisierte Element, das die Konflikte verursacht. Für Löschvorgänge ist es NULL.
- **existingItem**: Das Element, für das gerade ein Commit ausgeführt wird. Dieser Wert ist ungleich null bei einem Update und null bei einer Einfügung oder Löschung.
- **isTombstone**: Boolescher Wert, der angibt, ob das IncomingItem-Element einen Konflikt mit einem zuvor gelöschten Element verursacht. Ist dies der Fall, ist „existingItem“ ebenfalls NULL.
- **conflictingItems**: Array der comitteten Version aller Elemente im Container, die im Konflikt mit incomingItem für eine ID oder mit anderen eindeutigen Indexeigenschaften stehen.

> [!IMPORTANT]
> Wie bei jeder gespeicherten Prozedur kann eine benutzerdefinierte Konfliktlösungsprozedur auf alle Daten mit dem gleichen Partitionsschlüssel zugreifen und beliebige INSERT-, UPDATE- oder DELETE-Vorgänge durchführen, um Konflikte zu lösen.


Diese gespeicherte Beispielprozedur löst Konflikte durch Auswählen des niedrigsten Werts aus dem Pfad `/myCustomId`.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

Nach dem Erstellen des Containers müssen Sie die gespeicherte Prozedur `resolver` erstellen.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java Sync SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

Nach dem Erstellen des Containers müssen Sie die gespeicherte Prozedur `resolver` erstellen.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

Nach dem Erstellen des Containers müssen Sie die gespeicherte Prozedur `resolver` erstellen.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
  'id': self.udp_collection_name,
  'conflictResolutionPolicy': {
      'mode': 'Custom',
      'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
      }
  }
udp_collection = self.try_create_document_collection(create_client, database, udp_collection)
```

Nach dem Erstellen des Containers müssen Sie die gespeicherte Prozedur `resolver` erstellen.


## <a name="create-a-custom-conflict-resolution-policy"></a>Erstellen einer benutzerdefinierten Konfliktlösungsrichtlinie

Die folgenden Beispiele zeigen, wie Sie einen Container mit einer benutzerdefinierten Konfliktlösungsrichtlinie einrichten. Diese Konflikte werden im Konfliktfeed angezeigt.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Java Async SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Java Sync SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
                    'id': self.manual_collection_name,
                    'conflictResolutionPolicy': {
                          'mode': 'Custom'
                        }
                    }
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>Lesen aus dem Konfliktfeed

Die folgenden Beispiele zeigen, wie Sie Daten aus dem Konfliktfeed eines Containers lesen. Konflikte werden nur dann im Konfliktfeed angezeigt, wenn sie nicht automatisch gelöst wurden oder wenn eine benutzerdefinierte Konfliktrichtlinie verwendet wird.

### <a id="read-from-conflict-feed-dotnet"></a>.NET SDK

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-java-async"></a>Java Async SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Java Sync SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die folgenden Azure Cosmos DB-Konzepte:

* [Globale Verteilung: Hintergrundinformationen](global-dist-under-the-hood.md)
* [Konfigurieren von Multimaster in Ihren Anwendungen](how-to-multi-master.md)
* [Konfigurieren von Clients für Multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Hinzufügen/Entfernen von Regionen in Ihrem Azure Cosmos DB-Konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [How to configure multi-master in your applications that use Azure Cosmos DB](how-to-multi-master.md) (Konfigurieren von Multimaster-Features in Anwendungen, die Azure Cosmos DB verwenden)
* [Partitionierung und Datenverteilung](partition-data.md)
* [Indizierung in Azure Cosmos DB](indexing-policies.md)