---
title: Änderungsdatenströme in der API für MongoDB von Azure Cosmos-DB
description: Erfahren Sie, wie Sie Änderungsdatenströme in der API für MongoDB von Azure Cosmos DB verwenden, um die an Ihren Daten vorgenommenen Änderungen abzurufen.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/04/2020
ms.author: rosouz
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 9a4e35a235fe6ee6950bd1b4c35cbf9e72ac2893
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359233"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Änderungsdatenströme in der API für MongoDB von Azure Cosmos-DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Die Unterstützung von [Änderungsfeeds](change-feed.md) in der API für MongoDB von Azure Cosmos DB ist über die API für Änderungsdatenströme verfügbar. Mithilfe der API für Änderungsdatenströme können Ihre Anwendungen die Änderungen an der Sammlung oder an den Elementen in einem einzelnen Shard abrufen. Später können Sie auf der Grundlage der Ergebnisse weitere Maßnahmen ergreifen. Änderungen an den Elementen in der Sammlung werden in der Reihenfolge ihres Änderungszeitpunkts erfasst und die Sortierreihenfolge ist für die einzelnen Shardschlüssel sichergestellt.

> [!NOTE]
> Um Änderungsdatenströme zu verwenden, erstellen Sie das Konto mit Version 3.6 der API für MongoDB von Azure Cosmos DB oder einer höheren Version. Wenn Sie die Änderungsdatenstrom-Beispiele für eine frühere Version ausführen, wird möglicherweise die Fehlermeldung `Unrecognized pipeline stage name: $changeStream` angezeigt.

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt, wie Sie Änderungsdatenströme für alle Elemente der Sammlung abrufen können. In diesem Beispiel wird ein Cursor erstellt, um Elemente zu überwachen, wenn sie eingefügt, aktualisiert oder ersetzt werden. Die `$match`-Phase, `$project`-Phase und `fullDocument`-Option sind erforderlich, um die Änderungsdatenströme abzurufen. Die Überwachung auf Löschvorgänge mit Änderungsdatenströmen wird derzeit nicht unterstützt. Als Problemumgehung können Sie den zu löschenden Elementen eine schwache Markierung hinzufügen. Beispielsweise können Sie ein Attribut im Element mit dem Namen „deleted“ hinzufügen. Wenn Sie das Element löschen möchten, können Sie „deleted“ auf `true` und eine Gültigkeitsdauer für das Element festlegen. Da das Aktualisieren von „deleted“ in `true` ein Update ist, wird diese Änderung im Änderungsdatenstrom angezeigt.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

Das folgende Beispiel zeigt, wie Sie die Funktionalität zur Änderung des Datenstroms in Java verwenden können. Das komplette Beispiel finden Sie in diesem [GitHub-Repository](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/main/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java). Dieses Beispiel zeigt auch, wie Sie die `resumeAfter`-Methode verwenden, um alle Änderungen vom letzten Lesevorgang zu suchen. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>Änderungen innerhalb eines einzelnen Shards

Das folgende Beispiel zeigt, wie Sie Änderungen an den Elementen in einem einzelnen Shard abrufen können. In diesem Beispiel werden die Änderungen von Elementen abgerufen, deren ShardsSchlüssel gleich „a“ und der Shardschlüsselwert gleich „1“ ist. Es ist möglich, dass verschiedene Clients Änderungen von verschiedenen Shards parallel lesen.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Es gelten die folgenden Einschränkungen, wenn Änderungsdatenströme verwendet werden:

* Die Eigenschaften `operationType` und `updateDescription` werden im Ausgabedokument noch nicht unterstützt.
* Die Vorgangstypen `insert`, `update` und `replace` werden derzeit unterstützt. Löschvorgänge oder andere Ereignisse werden jedoch noch nicht unterstützt.

Aufgrund dieser Einschränkungen sind die Phasen „$match“, „$project“ und die „fullDocument“-Optionen erforderlich, wie in den vorherigen Beispielen gezeigt.

Anders als beim Änderungsfeed in der SQL-API von Azure Cosmos DB gibt es keine separate [Änderungsfeed-Prozessorbibliothek](change-feed-processor.md), um Änderungsdatenströme zu nutzen, und auch keinen Bedarf für einen Leasecontainer. Es gibt derzeit keine Unterstützung für [Azure Functions-Trigger](change-feed-functions.md) zum Verarbeiten von Änderungsdatenströmen.

## <a name="error-handling"></a>Fehlerbehandlung

Die folgenden Fehlercodes und Meldungen werden bei der Verwendung von Änderungsdatenströmen unterstützt:

* **HTTP-Fehlercode 16500**: Wenn der Änderungsdatenstrom gedrosselt wird, wird eine leere Seite zurückgegeben.

* **NamespaceNotFound (OperationType Invalidate)** : Wenn Sie den Änderungsdatenstrom für die nicht existierende Sammlung ausführen oder die Sammlung verworfen wird, wird ein `NamespaceNotFound`-Fehler zurückgegeben. Da die `operationType`-Eigenschaft nicht im Ausgabedokument zurückgegeben werden kann, wird anstelle des Fehlers `operationType Invalidate` der Fehler `NamespaceNotFound` zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der Gültigkeitsdauer für den automatischen Ablauf von Daten in der API für MongoDB von Azure Cosmos DB](mongodb-time-to-live.md)
* [Indizieren in der API für MongoDB von Azure Cosmos-DB](mongodb-indexing.md)
