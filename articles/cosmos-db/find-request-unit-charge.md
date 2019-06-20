---
title: Ermitteln der Gebühr für eine Anforderungseinheit (Request Unit, RU) in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die Gebühr für eine Anforderungseinheit (Request Unit, RU) für einen beliebigen Vorgang ermitteln, der für einen Azure Cosmos-Container ausgeführt wurde.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 0671556a1ad049782090ffede509072adbac4c6a
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416035"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Ermitteln der Gebühr für eine Anforderungseinheit in Azure Cosmos DB

In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den Verbrauch von [Anforderungseinheiten](request-units.md) (Request Units, RU) für einen beliebigen Vorgang ermitteln können, der für einen Container in Azure Cosmos DB ausgeführt wurde. Aktuell können Sie diesen Verbrauch nur über das Azure-Portal oder mithilfe eines der SDKs anhand der von Azure Cosmos DB zurückgegebenen Antwort ermitteln.

## <a name="sql-core-api"></a>SQL-API (Core-API)

Wenn Sie die SQL-API verwenden, bestehen mehrere Optionen zum Ermitteln des RU-Verbrauchs für einen Vorgang in einem Azure Cosmos-Container.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Aktuell können Sie die Anforderungsgebühr im Azure-Portal nur für eine SQL-Abfrage ermitteln.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), und fügen Sie ihm Daten hinzu, oder wählen Sie ein vorhandenes Azure Cosmos-Konto aus, das bereits Daten enthält.

1. Wechseln Sie zum Bereich **Daten-Explorer**, und wählen Sie dann den gewünschten Container aus.

1. Wählen Sie **Neue SQL-Abfrage** aus.

1. Geben Sie eine gültige Abfrage ein, und wählen Sie dann **Abfrage ausführen** aus.

1. Wählen Sie **Query Stats** (Abfragestatistik) aus, um die tatsächliche Anforderungsgebühr für die ausgeführte Anforderung anzuzeigen.

![Screenshot: Anforderungsgebühr für eine SQL-Abfrage im Azure-Portal](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>Verwenden des .NET SDK v2

Objekte, die vom [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) zurückgegeben werden, machen eine Eigenschaft `RequestCharge` verfügbar:

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer .NET-Web-App mithilfe eines SQL-API-Konto in Azure Cosmos DB](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Objekte, die vom [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) zurückgegeben werden, machen eine Methode `getRequestCharge()` verfügbar:

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Java-Anwendung mithilfe eines SQL-API-Kontos für Azure Cosmos DB](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>Verwenden des Node.js SDK

Vom [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) zurückgegebene Objekte machen ein Unterobjekt `headers` verfügbar, das alle von der zugrunde liegenden HTTP-API zurückgegebenen Header zuordnet. Die Anforderungsgebühr ist unter dem Schlüssel `x-ms-request-charge` verfügbar:

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Node.js-App mithilfe eines Azure Cosmos DB-SQL-API-Kontos](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>Verwenden des Python SDK

Das Objekt `CosmosClient` des [Python SDK](https://pypi.org/project/azure-cosmos/) macht ein Wörterbuch `last_response_headers` verfügbar, das alle Header zuordnet, die von der zugrunde liegenden HTTP-API für den letzten ausgeführten Vorgang zurückgegeben wurden. Die Anforderungsgebühr ist unter dem Schlüssel `x-ms-request-charge` verfügbar:

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Python-App mithilfe eines Azure Cosmos DB-SQL-API-Kontos](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB-API für MongoDB

Die RU-Gebühr wird durch einen benutzerdefinierten [Datenbankbefehl](https://docs.mongodb.com/manual/reference/command/) namens `getLastRequestStatistics` verfügbar gemacht. Der Befehl gibt ein Dokument zurück, das den Namen des letzten ausgeführten Vorgangs sowie dessen Anforderungsgebühr und Dauer enthält. Wenn Sie die Azure Cosmos DB-API für MongoDB verwenden, haben Sie mehrere Optionen zum Abrufen der RU-Gebühr.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Aktuell können Sie die Anforderungsgebühr im Azure-Portal nur für eine Abfrage ermitteln.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-mongodb-dotnet.md#create-a-database-account), und fügen Sie ihm Daten hinzu, oder wählen Sie ein vorhandenes Konto aus, das bereits Daten enthält.

1. Wechseln Sie zum Bereich **Daten-Explorer**, und wählen Sie dann die gewünschte Sammlung aus.

1. Wählen Sie **Neue Abfrage**aus.

1. Geben Sie eine gültige Abfrage ein, und wählen Sie dann **Abfrage ausführen** aus.

1. Wählen Sie **Query Stats** (Abfragestatistik) aus, um die tatsächliche Anforderungsgebühr für die ausgeführte Anforderung anzuzeigen.

![Screenshot: Anforderungsgebühr für eine MongoDB-Abfrage im Azure-Portal](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Verwenden des .NET-Treibers von MongoDB

Wenn Sie den [offiziellen MongoDB .NET-Treiber](https://docs.mongodb.com/ecosystem/drivers/csharp/) verwenden, können Sie Befehle durch Aufrufen der `RunCommand`-Methode für ein `IMongoDatabase`-Objekt ausführen. Diese Methode erfordert eine Implementierung der abstrakten Klasse `Command<>`:

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer .NET-Web-App mithilfe einer Azure Cosmos DB-API für MongoDB](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Verwenden des Java-Treibers von MongoDB


Wenn Sie den [offiziellen MongoDB Java-Treiber](https://mongodb.github.io/mongo-java-driver/) verwenden, können Sie Befehle durch Aufrufen der `runCommand`-Methode für ein `MongoDatabase`-Objekt ausführen:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Web-App mithilfe der Azure Cosmos DB-API für MongoDB und des Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>Verwenden des Node.js-Treibers von MongoDB

Wenn Sie den [offiziellen MongoDB Node.js-Treiber](https://mongodb.github.io/node-mongodb-native/) verwenden, können Sie Befehle durch Aufrufen der `command`-Methode für ein `db`-Objekt ausführen:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

Weitere Informationen finden Sie unter [Quickstart: Migrieren einer vorhandenen MongoDB-Node.js-Web-App zu Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Cassandra-API

Wenn Sie Vorgänge für die Cassandra-API von Azure Cosmos DB ausführen, wird die RU-Gebühr in der eingehenden Nutzlast als Feld namens `RequestCharge` zurückgegeben. Sie haben mehrere Optionen zum Abrufen der RU-Gebühr.

### <a name="use-the-net-sdk"></a>Verwenden des .NET SDK

Wenn Sie das [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) verwenden, können Sie die eingehende Nutzlast unter der `Info`-Eigenschaft eines `RowSet`-Objekts abrufen:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Cassandra-App mithilfe des .NET-SDK und Azure Cosmos DB](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Wenn Sie das [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) verwenden, können Sie die eingehende Nutzlast durch Aufrufen der `getExecutionInfo()`-Methode für ein `ResultSet`-Objekt abrufen:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Cassandra-App mithilfe des Java SDK und Azure Cosmos DB](create-cassandra-java.md).

## <a name="gremlin-api"></a>Gremlin-API

Wenn Sie die Gremlin-API verwenden, bestehen mehrere Optionen zum Ermitteln des RU-Verbrauchs für einen Vorgang in einem Azure Cosmos-Container. 

### <a name="use-drivers-and-sdk"></a>Verwenden von Treibern und SDK

Von der Gremlin-API zurückgegebene Header werden benutzerdefinierten Statusattributen zugeordnet, die aktuell durch das Gremlin .NET SDK und das Gremlin Java SDK bereitgestellt werden. Die Anforderungsgebühr ist unter dem Schlüssel `x-ms-request-charge` verfügbar.

### <a name="use-the-net-sdk"></a>Verwenden des .NET SDK

Wenn Sie das [Gremlin .NET SDK](https://www.nuget.org/packages/Gremlin.Net/) verwenden, stehen Statusattribute unter der Eigenschaft `StatusAttributes` des Objekts `ResultSet<>` zur Verfügung:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer .NET Framework- oder Core-Anwendung mithilfe eines Gremlin-API-Kontos für Azure Cosmos DB](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Verwenden des Java SDK

Wenn Sie das [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) verwenden, können Sie Statusattribute durch Aufrufen der `statusAttributes()`-Methode für das `ResultSet`-Objekt abrufen:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Graphdatenbank in Azure Cosmos DB mithilfe des Java SDK](create-graph-java.md).

## <a name="table-api"></a>Tabelle-API

Aktuell ist das einzige SDK, das die RU-Gebühr für Tabellenvorgänge zurückgibt, das [.NET-Standard-SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Das Objekt `TableResult` macht eine `RequestCharge`-Eigenschaft verfügbar, die durch das SDK aufgefüllt wird, wenn Sie es für die Azure Cosmos DB-Tabellen-API verwenden:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Tabellen-API-App mithilfe des .NET-SDK und Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Optimieren Ihres RU-Verbrauchs finden Sie in diesen Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](optimize-cost-queries.md)
* [Globales Skalieren von bereitgestelltem Durchsatz](scaling-throughput.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Bereitstellen des Durchsatzes für einen Container](how-to-provision-container-throughput.md)
