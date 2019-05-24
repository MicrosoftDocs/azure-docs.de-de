---
title: Aufrufen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen mithilfe von Azure Cosmos DB SDKs
description: Erfahren Sie, wie Sie gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen mithilfe von Azure Cosmos DB SDKs registrieren und aufrufen.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: b25d65b07420c5be53d952c6f81de5116dc9ec7d
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978913"
---
# <a name="how-to-register-and-use-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Registrieren und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB

Die SQL-API in Azure Cosmos DB unterstützt das Registrieren und Aufrufen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen (User-Defined Functions, UDFs), die in JavaScript geschrieben wurden. Sie können die SDKs für SQL API [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript](sql-api-sdk-node.md), [Node.js](sql-api-sdk-node.md) oder [Python](sql-api-sdk-python.md) verwenden, um gespeicherte Prozeduren zu registrieren und aufzurufen. Sobald Sie gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen definiert haben, können Sie diese laden und im [Azure-Portal](https://portal.azure.com/) mit dem Daten-Explorer anzeigen.

## <a id="stored-procedures"></a>Ausführen gespeicherter Prozeduren

Gespeicherte Prozeduren werden mit JavaScript geschrieben. Sie können Elemente in einem Azure Cosmos-Container erstellen, aktualisieren, lesen, abfragen und löschen. Weitere Informationen zum Schreiben von gespeicherten Prozeduren in Azure Cosmos DB finden Sie im Artikel [Schreiben von gespeicherten Prozeduren in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures).

Die folgenden Beispiele zeigen, wie Sie eine gespeicherte Prozedur mithilfe der Azure Cosmos DB SDKs registrieren und aufrufen. Informieren Sie sich unter [Erstellen eines Elements](how-to-write-stored-procedures-triggers-udfs.md#create-an-item), da die Quelle für diese gespeicherte Prozedur als `spCreateToDoItem.js` gespeichert ist.

> [!NOTE]
> Wenn Sie eine gespeicherte Prozedur in partitionierten Containern ausführen, muss in den Anforderungsoptionen ein Partitionsschlüsselwert angegeben werden. Gespeicherte Prozeduren gelten immer für einen bestimmten Partitionsschlüssel. Elemente, die einen anderen Partitionsschlüsselwert aufweisen, sind in der gespeicherten Prozedur nicht sichtbar. Dies gilt auch für Trigger.

### <a name="stored-procedures---net-sdk"></a>Gespeicherte Prozeduren – .NET SDK

Das folgende Beispiel zeigt, wie Sie mit dem .NET SDK eine gespeicherte Prozedur registrieren:

```csharp
string storedProcedureId = "spCreateToDoItem";
StoredProcedure newStoredProcedure = new StoredProcedure
   {
       Id = storedProcedureId,
       Body = File.ReadAllText($@"..\js\{storedProcedureId}.js")
   };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var response = await client.CreateStoredProcedureAsync(containerUri, newStoredProcedure);
StoredProcedure createdStoredProcedure = response.Resource;
```

Der folgende Code zeigt, wie Sie mit dem .NET SDK eine gespeicherte Prozedur aufrufen:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri uri = UriFactory.CreateStoredProcedureUri("myDatabase", "myContainer", "spCreateToDoItem");
RequestOptions options = new RequestOptions { PartitionKey = new PartitionKey("Personal") };
var result = await client.ExecuteStoredProcedureAsync<string>(uri, options, newItem);
var id = result.Response;
```

### <a name="stored-procedures---java-sdk"></a>Gespeicherte Prozeduren – Java SDK

Das folgende Beispiel zeigt, wie Sie mit dem Java SDK eine gespeicherte Prozedur registrieren:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
StoredProcedure newStoredProcedure = new StoredProcedure(
    "{" +
        "  'id':'spCreateToDoItem'," +
        "  'body':" + new String(Files.readAllBytes(Paths.get("..\\js\\spCreateToDoItem.js"))) +
    "}");
//toBlocking() blocks the thread until the operation is complete and is used only for demo.  
StoredProcedure createdStoredProcedure = asyncClient.createStoredProcedure(containerLink, newStoredProcedure, null)
    .toBlocking().single().getResource();
```

Der folgende Code zeigt, wie Sie mit dem Java SDK eine gespeicherte Prozedur aufrufen:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String sprocLink = String.format("%s/sprocs/%s", containerLink, "spCreateToDoItem");
final CountDownLatch successfulCompletionLatch = new CountDownLatch(1);

class ToDoItem {
    public String category;
    public String name;
    public String description;
    public boolean isComplete;
}

ToDoItem newItem = new ToDoItem();
newItem.category = "Personal";
newItem.name = "Groceries";
newItem.description = "Pick up strawberries";
newItem.isComplete = false;

RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("Personal"));

Object[] storedProcedureArgs = new Object[] { newItem };
asyncClient.executeStoredProcedure(sprocLink, requestOptions, storedProcedureArgs)
    .subscribe(storedProcedureResponse -> {
        String storedProcResultAsString = storedProcedureResponse.getResponseAsString();
        successfulCompletionLatch.countDown();
        System.out.println(storedProcedureResponse.getActivityId());
    }, error -> {
        System.err.println("an error occurred while executing the stored procedure: actual cause: "
                + error.getMessage());
    });

successfulCompletionLatch.await();
```

### <a name="stored-procedures---javascript-sdk"></a>Gespeicherte Prozeduren – JavaScript SDK

Das folgende Beispiel zeigt, wie Sie mit dem JavaScript SDK eine gespeicherte Prozedur registrieren:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
await container.storedProcedures.create({
    id: sprocId,
    body: require(`../js/${sprocId}`)
});
```

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK eine gespeicherte Prozedur aufrufen:

```javascript
const newItem = [{
    category: "Personal",
    name: "Groceries",
    description: "Pick up strawberries",
    isComplete: false
}];
const container = client.database("myDatabase").container("myContainer");
const sprocId = "spCreateToDoItem";
const {body: result} = await container.storedProcedure(sprocId).execute(newItem, {partitionKey: newItem[0].category});
```

### <a name="stored-procedures---python-sdk"></a>Gespeicherte Prozeduren – Python SDK

Das folgende Beispiel zeigt, wie Sie mit dem Python SDK eine gespeicherte Prozedur registrieren:

```python
with open('../js/spCreateToDoItem.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
sproc_definition = {
            'id': 'spCreateToDoItem',
            'serverScript': file_contents,
        }
sproc = client.CreateStoredProcedure(container_link, sproc_definition)
```

Der folgende Code zeigt, wie Sie mit dem Python SDK eine gespeicherte Prozedur aufrufen:

```python
sproc_link = 'dbs/myDatabase/colls/myContainer/sprocs/spCreateToDoItem'
new_item = [{
    'category':'Personal',
    'name':'Groceries',
    'description':'Pick up strawberries',
    'isComplete': False
}]
client.ExecuteStoredProcedure(sproc_link, new_item, {'partitionKey': 'Personal'}
```

## <a id="pre-triggers"></a>Ausführen von vorangestellten Triggern

Die folgenden Beispiele zeigen, wie Sie einen vorangestellten Trigger mithilfe der Azure Cosmos DB SDKs registrieren und aufrufen. Informieren Sie sich unter [Beispiel für vorangestellte Trigger](how-to-write-stored-procedures-triggers-udfs.md#pre-triggers), da die Quelle für diesen vorangestellten Trigger als `trgPreValidateToDoItemTimestamp.js` gespeichert ist.

Bei der Ausführung werden vorangestellte Trigger im RequestOptions-Objekt übergeben, indem `PreTriggerInclude` angegeben und dann der Name des Triggers an ein List-Objekt übergeben wird.

> [!NOTE]
> Auch wenn der Name des Triggers als List-Objekt übergeben wird, können Sie dennoch nur einen Trigger pro Vorgang ausführen.

### <a name="pre-triggers---net-sdk"></a>Vorangestellte Trigger – .NET SDK

Der folgende Code zeigt, wie Sie mit dem .NET SDK einen vorangestellten Trigger registrieren:

```csharp
string triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger
{
    Id =  triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Pre
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Der folgende Code zeigt, wie Sie mit dem .NET SDK einen vorangestellten Trigger aufrufen:

```csharp
dynamic newItem = new
{
    category = "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
RequestOptions requestOptions = new RequestOptions { PreTriggerInclude = new List<string> { "trgPreValidateToDoItemTimestamp" } };
await client.CreateDocumentAsync(containerUri, newItem, requestOptions);
```

### <a name="pre-triggers---java-sdk"></a>Vorangestellte Trigger – Java SDK

Der folgende Code zeigt, wie Sie mit dem Java SDK einen vorangestellten Trigger registrieren:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPreValidateToDoItemTimestamp";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Pre);
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Der folgende Code zeigt, wie Sie mit dem Java SDK einen vorangestellten Trigger aufrufen:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
    Document item = new Document("{ "
            + "\"category\": \"Personal\", "
            + "\"name\": \"Groceries\", "
            + "\"description\": \"Pick up strawberries\", "
            + "\"isComplete\": false, "
            + "}"
            );
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPreTriggerInclude(Arrays.asList("trgPreValidateToDoItemTimestamp"));
//toBlocking() blocks the thread until the operation is complete and is used only for demo. 
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="pre-triggers---javascript-sdk"></a>Vorangestellte Trigger – JavaScript SDK

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK einen vorangestellten Trigger registrieren:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "pre"
});
```

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK einen vorangestellten Trigger aufrufen:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPreValidateToDoItemTimestamp";
await container.items.create({
    category: "Personal",
    name = "Groceries",
    description = "Pick up strawberries",
    isComplete = false
}, {preTriggerInclude: [triggerId]});
```

### <a name="pre-triggers---python-sdk"></a>Vorangestellte Trigger – Python SDK

Der folgende Code zeigt, wie Sie mit dem Python SDK einen vorangestellten Trigger registrieren:

```python
with open('../js/trgPreValidateToDoItemTimestamp.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPreValidateToDoItemTimestamp',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Pre,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Der folgende Code zeigt, wie Sie mit dem Python SDK einen vorangestellten Trigger aufrufen:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'category': 'Personal', 'name': 'Groceries', 'description':'Pick up strawberries', 'isComplete': False}
client.CreateItem(container_link, item, { 'preTriggerInclude': 'trgPreValidateToDoItemTimestamp'})
```

## <a id="post-triggers"></a>Ausführen von nachgestellten Triggern

Die folgenden Beispiele zeigen, wie Sie einen nachgestellten Trigger mithilfe der Azure Cosmos DB SDKs registrieren. Informieren Sie sich unter [Beispiel für nachgestellte Trigger](how-to-write-stored-procedures-triggers-udfs.md#post-triggers), da die Quelle für diesen nachgestellten Trigger als `trgPostUpdateMetadata.js` gespeichert ist.

### <a name="post-triggers---net-sdk"></a>Nachgestellte Trigger – .NET SDK

Der folgende Code zeigt, wie Sie mit dem .NET SDK einen nachgestellten Trigger registrieren:

```csharp
string triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger
{
    Id = triggerId,
    Body = File.ReadAllText($@"..\js\{triggerId}.js"),
    TriggerOperation = TriggerOperation.Create,
    TriggerType = TriggerType.Post
};
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateTriggerAsync(containerUri, trigger);
```

Der folgende Code zeigt, wie Sie mit dem .NET SDK einen nachgestellten Trigger aufrufen:

```csharp
var newItem = { 
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};

var options = { postTriggerInclude: "trgPostUpdateMetadata" };
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.createDocumentAsync(containerUri, newItem, options);
```

### <a name="post-triggers---java-sdk"></a>Nachgestellte Trigger – Java SDK

Der folgende Code zeigt, wie Sie mit dem Java SDK einen nachgestellten Trigger registrieren:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String triggerId = "trgPostUpdateMetadata";
Trigger trigger = new Trigger();
trigger.setId(triggerId);
trigger.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", triggerId)))));
trigger.setTriggerOperation(TriggerOperation.Create);
trigger.setTriggerType(TriggerType.Post);
Trigger createdTrigger = asyncClient.createTrigger(containerLink, trigger, new RequestOptions()).toBlocking().single().getResource();
```

Der folgende Code zeigt, wie Sie mit dem Java SDK einen nachgestellten Trigger aufrufen:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Document item = new Document(String.format("{ "
    + "\"name\": \"artist_profile_1023\", "
    + "\"artist\": \"The Band\", "
    + "\"albums\": [\"Hellujah\", \"Rotators\", \"Spinning Top\"]"
    + "}"
));
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPostTriggerInclude(Arrays.asList("trgPostUpdateMetadata"));
//toBlocking() blocks the thread until the operation is complete, and is used only for demo.
asyncClient.createDocument(containerLink, item, requestOptions, false).toBlocking();
```

### <a name="post-triggers---javascript-sdk"></a>Nachgestellte Trigger – JavaScript SDK

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK einen nachgestellten Trigger registrieren:

```javascript
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.triggers.create({
    id: triggerId,
    body: require(`../js/${triggerId}`),
    triggerOperation: "create",
    triggerType: "post"
});
```

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK einen nachgestellten Trigger aufrufen:

```javascript
const item = {
    name: "artist_profile_1023",
    artist: "The Band",
    albums: ["Hellujah", "Rotators", "Spinning Top"]
};
const container = client.database("myDatabase").container("myContainer");
const triggerId = "trgPostUpdateMetadata";
await container.items.create(item, {postTriggerInclude: [triggerId]});
```

### <a name="post-triggers---python-sdk"></a>Nachgestellte Trigger – Python SDK

Der folgende Code zeigt, wie Sie mit dem Python SDK einen nachgestellten Trigger registrieren:

```python
with open('../js/trgPostUpdateMetadata.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
trigger_definition = {
            'id': 'trgPostUpdateMetadata',
            'serverScript': file_contents,
            'triggerType': documents.TriggerType.Post,
            'triggerOperation': documents.TriggerOperation.Create
        }
trigger = client.CreateTrigger(container_link, trigger_definition)
```

Der folgende Code zeigt, wie Sie mit dem Python SDK einen nachgestellten Trigger aufrufen:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
item = { 'name': 'artist_profile_1023', 'artist': 'The Band', 'albums': ['Hellujah', 'Rotators', 'Spinning Top']}
client.CreateItem(container_link, item, { 'postTriggerInclude': 'trgPostUpdateMetadata'})
```

## <a id="udfs"></a>Arbeiten mit benutzerdefinierten Funktionen

Die folgenden Beispiele zeigen, wie Sie eine benutzerdefinierte Funktion mithilfe der Azure Cosmos DB SDKs registrieren. Informieren Sie sich unter [Beispiel für benutzerdefinierte Funktionen](how-to-write-stored-procedures-triggers-udfs.md#udfs), da die Quelle für diese benutzerdefinierte Funktion als `udfTax.js` gespeichert ist.

### <a name="user-defined-functions---net-sdk"></a>Benutzerdefinierte Funktionen – .NET SDK

Der folgende Code zeigt, wie Sie mit dem .NET SDK eine benutzerdefinierte Funktion registrieren:

```csharp
string udfId = "Tax";
var udfTax = new UserDefinedFunction
{
    Id = udfId,
    Body = File.ReadAllText($@"..\js\{udfId}.js"),
};

Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
await client.CreateUserDefinedFunctionAsync(containerUri, udfTax);

```

Der folgende Code zeigt, wie Sie mit dem .NET SDK eine benutzerdefinierte Funktion aufrufen:

```csharp
Uri containerUri = UriFactory.CreateDocumentCollectionUri("myDatabase", "myContainer");
var results = client.CreateDocumentQuery<dynamic>(containerUri, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000"));

foreach (var result in results)
{
    //iterate over results
}
```

### <a name="user-defined-functions---java-sdk"></a>Benutzerdefinierte Funktionen – Java SDK

Der folgende Code zeigt, wie Sie mit dem Java SDK eine benutzerdefinierte Funktion registrieren:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
String udfId = "Tax";
UserDefinedFunction udf = new UserDefinedFunction();
udf.setId(udfId);
udf.setBody(new String(Files.readAllBytes(Paths.get(String.format("..\\js\\%s.js", udfId)))));
//toBlocking() blocks the thread until the operation is complete and is used only for demo.
UserDefinedFunction createdUDF = client.createUserDefinedFunction(containerLink, udf, new RequestOptions()).toBlocking().single().getResource();
```

Der folgende Code zeigt, wie Sie mit dem Java SDK eine benutzerdefinierte Funktion aufrufen:

```java
String containerLink = String.format("/dbs/%s/colls/%s", "myDatabase", "myContainer");
Observable<FeedResponse<Document>> queryObservable = client.queryDocuments(containerLink, "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000", new FeedOptions());
final CountDownLatch completionLatch = new CountDownLatch(1);
queryObservable.subscribe(
        queryResultPage -> {
            System.out.println("Got a page of query result with " +
                    queryResultPage.getResults().size());
        },
        // terminal error signal
        e -> {
            e.printStackTrace();
            completionLatch.countDown();
        },

        // terminal completion signal
        () -> {
            completionLatch.countDown();
        });
completionLatch.await();
```

### <a name="user-defined-functions---javascript-sdk"></a>Benutzerdefinierte Funktionen – JavaScript SDK

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK eine benutzerdefinierte Funktion registrieren:

```javascript
const container = client.database("myDatabase").container("myContainer");
const udfId = "Tax";
await container.userDefinedFunctions.create({
    id: udfId,
    body: require(`../js/${udfId}`)
```

Der folgende Code zeigt, wie Sie mit dem JavaScript SDK eine benutzerdefinierte Funktion aufrufen:

```javascript
const container = client.database("myDatabase").container("myContainer");
const sql = "SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000";
const {result} = await container.items.query(sql).toArray();
```

### <a name="user-defined-functions---python-sdk"></a>Benutzerdefinierte Funktionen – Python SDK

Der folgende Code zeigt, wie Sie mit dem Python SDK eine benutzerdefinierte Funktion registrieren:

```python
with open('../js/udfTax.js') as file:
    file_contents = file.read()
container_link = 'dbs/myDatabase/colls/myContainer'
udf_definition = {
            'id': 'Tax',
            'serverScript': file_contents,
        }
udf = client.CreateUserDefinedFunction(container_link, udf_definition)
```

Der folgende Code zeigt, wie Sie mit dem Python SDK eine benutzerdefinierte Funktion aufrufen:

```python
container_link = 'dbs/myDatabase/colls/myContainer'
results = list(client.QueryItems(container_link, 'SELECT * FROM Incomes t WHERE udf.Tax(t.income) > 20000'))
```

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie weitere Konzepte und Vorgehensweisen zum Schreiben und Verwenden von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB kennen:

- [Arbeiten mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](stored-procedures-triggers-udfs.md)
- [Arbeiten mit der in die JavaScript-Sprache integrierten Abfrage-API in Azure Cosmos DB](javascript-query-api.md)
- [Schreiben von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in Azure Cosmos DB](how-to-write-stored-procedures-triggers-udfs.md)
- [Schreiben von gespeicherten Prozeduren und Triggern mit der JavaScript-Abfrage-API in Azure Cosmos DB](how-to-write-javascript-query-api.md)
