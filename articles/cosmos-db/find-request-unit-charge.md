---
title: Ermitteln der Gebühr für Anforderungseinheiten (Request Unit, RU) für eine SQL-Abfrage in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die Gebühr für eine Anforderungseinheit (Request Unit, RU) für SQL-Abfragen ermitteln, die für einen Azure Cosmos-Container ausgeführt wurden. Sie können das Azure-Portal oder die Sprachen .NET, Java, Python und Node.js verwenden, um die RU-Gebühr zu ermitteln.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 9d0694a76bca832887d30601711894b953fe22e1
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078440"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-sql-api"></a>Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge, die in der Azure Cosmos DB-SQL-API ausgeführt werden
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB unterstützt viele APIs wie z.B. SQL, MongoDB, Cassandra, Gremlin und Tabelle. Jede API verfügt über einen eigenen Satz von Datenbankvorgängen. Diese Vorgänge reichen von einfachen Lese- und Schreibvorgängen für Datenpunkte bis hin zu komplexen Abfragen. Jeder Datenbankvorgang beansprucht je nach Komplexität eine bestimmte Menge an Systemressourcen.

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und in Anforderungseinheiten (Request Units, RUs) ausgedrückt. Sie können sich Anforderungseinheiten als Währung zur Abstrahierung der Systemressourcen wie CPU, IOPS und Arbeitsspeicher vorstellen, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind. Unabhängig davon, welche API Sie für die Interaktion mit Ihrem Azure Cosmos-Container verwenden, werden die Kosten immer in RUs gemessen. Unabhängig davon, ob es sich bei dem Datenbankvorgang um einen Schreib-, Lese- oder Abfragevorgang handelt, werden die Kosten immer in RUs gemessen. Weitere Informationen finden Sie im Artikel [Aspekte zu Anforderungseinheiten](request-units.md).

In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den Verbrauch von [Anforderungseinheiten](request-units.md) (Request Units, RUs) für einen beliebigen Vorgang ermitteln können, der für einen Container in der Azure Cosmos DB-SQL-API ausgeführt wurde. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Ermitteln der RU/s-Gebühr in den Artikeln zur [API für MongoDB](find-request-unit-charge-mongodb.md), [Cassandra-API](find-request-unit-charge-cassandra.md), [Gremlin-API](find-request-unit-charge-gremlin.md) und [Tabellen-API](find-request-unit-charge-table.md).

Aktuell können Sie diesen Verbrauch nur über das Azure-Portal oder mithilfe eines der SDKs anhand der von Azure Cosmos DB zurückgegebenen Antwort ermitteln. Wenn Sie die SQL-API verwenden, bestehen mehrere Optionen zum Ermitteln des RU-Verbrauchs für einen Vorgang in einem Azure Cosmos-Container.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), und fügen Sie ihm Daten hinzu, oder wählen Sie ein vorhandenes Azure Cosmos-Konto aus, das bereits Daten enthält.

1. Wechseln Sie zum Bereich **Daten-Explorer** , und wählen Sie dann den gewünschten Container aus.

1. Wählen Sie **Neue SQL-Abfrage** aus.

1. Geben Sie eine gültige Abfrage ein, und wählen Sie dann **Abfrage ausführen** aus.

1. Wählen Sie **Query Stats** (Abfragestatistik) aus, um die tatsächliche Anforderungsgebühr für die ausgeführte Anforderung anzuzeigen.

:::image type="content" source="./media/find-request-unit-charge/portal-sql-query.png" alt-text="Screenshot: Anforderungsgebühr für eine SQL-Abfrage im Azure-Portal":::

## <a name="use-the-net-sdk"></a>Verwenden des .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

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

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

Objekte, die vom [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) zurückgegeben werden, machen eine `RequestCharge`-Eigenschaft verfügbar:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer .NET-Web-App mithilfe eines SQL-API-Konto in Azure Cosmos DB](create-sql-api-dotnet.md).

---

## <a name="use-the-java-sdk"></a>Verwenden des Java SDK

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

## <a name="use-the-nodejs-sdk"></a>Verwenden des Node.js SDK

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

## <a name="use-the-python-sdk"></a>Verwenden des Python SDK

Das Objekt `CosmosClient` des [Python SDK](https://pypi.org/project/azure-cosmos/) macht ein Wörterbuch `last_response_headers` verfügbar, das alle Header zuordnet, die von der zugrunde liegenden HTTP-API für den letzten ausgeführten Vorgang zurückgegeben wurden. Die Anforderungsgebühr ist unter dem Schlüssel `x-ms-request-charge` verfügbar:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Python-App mithilfe eines Azure Cosmos DB-SQL-API-Kontos](create-sql-api-python.md). 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Optimieren Ihres RU-Verbrauchs finden Sie in diesen Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Globales Skalieren von bereitgestelltem Durchsatz](./request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Bereitstellen des Durchsatzes für einen Container](how-to-provision-container-throughput.md)
* [Überwachen und Debuggen mit Metriken in Azure Cosmos DB](use-metrics.md)