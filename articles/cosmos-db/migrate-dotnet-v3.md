---
title: Migrieren einer Anwendung für die Verwendung des Azure Cosmos DB .NET SDK 3.0 (com.azure.cosmos)
description: Erfahren Sie, wie Sie Ihre vorhandene .NET-Anwendung von v2 des SDK auf das neuere .NET SDK v3 (com.azure.cosmos-Paket) für die Core-API (SQL) aktualisieren.
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/23/2020
ms.openlocfilehash: 514a6c6daccfe63865ae0b2b9f5bf29c5cbedc29
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334020"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v3"></a>Migrieren einer Anwendung für die Verwendung des Azure Cosmos DB .NET SDK v3
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Informationen zum Azure Cosmos DB .NET SDK v3 finden Sie in den [Versionshinweisen](sql-api-sdk-dotnet-standard.md), im [.NET-GitHub-Repository](https://github.com/Azure/azure-cosmos-dotnet-v3), in den [Tipps zur Leistungssteigerung](performance-tips-dotnet-sdk-v3-sql.md) für das .NET SDK v3 sowie im [Handbuch zur Problembehandlung](troubleshoot-dot-net-sdk.md).
>

Dieser Artikel enthält Überlegungen für die Aktualisierung Ihrer vorhandenen .NET-Anwendung auf das neuere Azure Cosmos DB .NET SDK v3 für die Core-API (SQL). Azure Cosmos DB .NET SDK v3 entspricht dem Microsoft.Azure.Cosmos-Namespace. Sie können die Informationen in diesem Dokument verwenden, wenn Sie Ihre Anwendung von einem der folgenden Azure Cosmos DB .NET SDKs migrieren:

* Azure Cosmos DB .NET Framework SDK v2 für SQL-API
* Azure Cosmos DB .NET Core SDK v2 für SQL-API

Die Anweisungen in diesem Artikel helfen Ihnen auch beim Migrieren der folgenden externen Bibliotheken, die jetzt Teil der Azure Cosmos DB .NET SDK v3 für die Core-API (SQL) sind:

* .NET-Änderungsfeed-Prozessorbibliothek 2.0
* .NET Bulk Executor-Bibliothek 1.1 oder höher

## <a name="whats-new-in-the-net-v3-sdk"></a>Neuigkeiten im .NET v3 SDK

Das v3 SDK enthält viele Verbesserungen der Benutzerfreundlichkeit und Leistung, darunter:

* Intuitive Benennung von Programmiermodellen
* .NET-Standard 2.0 **
* Leistungssteigerung durch Stream-API-Unterstützung
* Fluent-Hierarchie, sodass keine URI-Factory mehr nötig ist
* Integrierte Unterstützung für die Änderungsfeed-Prozessorbibliothek
* Integrierte Unterstützung für Massenvorgänge
* Pseudo-APIs für einfachere Unittests
* Transaktionale Batch- und Blazor-Unterstützung
* Austauschbare Serialisierungsmodule
* Skalieren von nicht partitionierten Containern und Containern mit Autoskalierung

** Das SDK zielt auf .NET Standard 2.0 ab, welches das vorhandene Azure Cosmos DB .NET Framework und vorhandene .NET Core SDKs zu einem einzigen .NET SDK zusammenfasst. Sie können das .NET SDK auf jeder Plattform verwenden, die .NET Standard 2.0 implementiert, einschließlich Ihrer Anwendungen mit .NET Framework 4.6.1+ und .NET Core 2.0+.

Bei den meisten Netzwerken, der Retry-Logik und den unteren Ebenen des SDK gibt es größtenteils keine Änderungen.

**Das Azure Cosmos DB .NET SDK v3 ist jetzt Open Source verfügbar.** Wir freuen uns über alle Pull Requests und werden Probleme auf [GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/) protokollieren und dort Feedback verfolgen. Wir arbeiten daran, sämtliche Funktionen zu integrieren, die die Kundenfreundlichkeit verbessern.

## <a name="why-migrate-to-the-net-v3-sdk"></a>Gründe für das Migrieren auf das .NET v3 SDK

Neben den zahlreichen Verbesserungen der Benutzerfreundlichkeit und der Leistung werden beim neuesten SDK getätigte Investitionen in neue Funktionen nicht auf ältere Versionen zurückportiert.

Obwohl es keine unmittelbaren Pläne gibt, den [Support für die SDKs der Version 2.0 einzustellen](sql-api-sdk-dotnet.md), werden diese SDKs in Zukunft durch neuere Versionen ersetzt, und das SDK wechselt in den Wartungsmodus. Für eine optimale Entwicklungserfahrung wird empfohlen, stets mit der neuesten unterstützten Version des SDK zu beginnen.

## <a name="major-name-changes-from-v2-sdk-to-v3-sdk"></a>Wichtige Namensänderungen vom v2 SDK zum v3 SDK

Im .NET 3.0 SDK wurden durchgängig die folgenden Namensänderungen angewandt, um dieses an die API-Benennungskonventionen für die Core-API (SQL) anzupassen:

* `DocumentClient` wird umbenannt in `CosmosClient`.
* `Collection` wird umbenannt in `Container`.
* `Document` wird umbenannt in `Item`.

Alle Ressourcenobjekte werden mit zusätzlichen Eigenschaften umbenannt, die aus Gründen der Übersichtlichkeit den Ressourcennamen umfassen.

Dies sind einige der wichtigsten Änderungen von Klassennamen:

| .NET v2 SDK | .NET v3 SDK |
|-------------|-------------|
|`Microsoft.Azure.Documents.Client.DocumentClient`|`Microsoft.Azure.CosmosClient`|
|`Microsoft.Azure.Documents.Client.ConnectionPolicy`|`Microsoft.Azure.Cosmos.CosmosClientOptions`|
|`Microsoft.Azure.Documents.Client.DocumentClientException` |`Microsoft.Azure.Cosmos.CosmosException`|
|`Microsoft.Azure.Documents.Client.Database`|`Microsoft.Azure.Cosmos.DatabaseProperties`|
|`Microsoft.Azure.Documents.Client.DocumentCollection`|`Microsoft.Azure.Cosmos.ContainerProperties`|
|`Microsoft.Azure.Documents.Client.RequestOptions`|`Microsoft.Azure.Cosmos.ItemRequestOptions`|
|`Microsoft.Azure.Documents.Client.FeedOptions`|`Microsoft.Azure.Cosmos.QueryRequestOptions`|
|`Microsoft.Azure.Documents.Client.StoredProcedure`|`Microsoft.Azure.Cosmos.StoredProcedureProperties`|
|`Microsoft.Azure.Documents.Client.Trigger`|`Microsoft.Azure.Cosmos.TriggerProperties`|

### <a name="classes-replaced-on-net-v3-sdk"></a>Im .NET v3 SDK ersetzte Klassen

Die folgenden Klassen wurden im SDK Version 3.0 ersetzt:

* `Microsoft.Azure.Documents.UriFactory`

* `Microsoft.Azure.Documents.Document`

* `Microsoft.Azure.Documents.Resource`

Die Klasse Microsoft.Azure.Documents.UriFactory wurde durch das Fluent-Design ersetzt. Das Fluent-Design erstellt URLs intern und ermöglicht die Übergabe eines einzelnen `Container`-Objekts anstelle von einem/einer `DocumentClient`, `DatabaseName` und `DocumentCollection`.

### <a name="changes-to-item-id-generation"></a>Änderungen bei der Element-ID-Generierung

Die Element-ID wird im .NET v3 SDK nicht mehr automatisch aufgefüllt. Daher muss die Element-ID eine speziell generierte ID enthalten. Sehen Sie sich dazu das folgende Beispiel an:

```csharp
[JsonProperty(PropertyName = "id")]
public Guid Id { get; set; }
```

### <a name="changed-default-behavior-for-connection-mode"></a>Geändertes Standardverhalten für den Verbindungsmodus

Das SDK v3 verwendet jetzt standardmäßig die Verbindungsmodi Direct und TCP, während für das vorherige SDK v2 standardmäßig die Verbindungsmodi Gateway und HTTPS festgelegt waren. Diese Änderung sorgt für eine verbesserte Leistung und Skalierbarkeit.

### <a name="changes-to-feedoptions-queryrequestoptions-in-v30-sdk"></a>Änderungen an FeedOptions (QueryRequestOptions im v3.0 SDK)

Die `FeedOptions`-Klasse im SDK v2 wurde jetzt im SDK v3 in `QueryRequestOptions` umbenannt, und innerhalb der Klasse wurden mehrere Eigenschaften im Namen und/oder Standardwert geändert oder vollständig entfernt.  

`FeedOptions.MaxDegreeOfParallelism` wurde in `QueryRequestOptions.MaxConcurrency` umbenannt, und der Standardwert und das zugehörige Verhalten bleiben unverändert. Vorgänge werden während der parallelen Abfragenausführung seriell auf Clientseite ausgeführt, ohne Parallelität.

`FeedOptions.EnableCrossPartitionQuery` wurde entfernt, und als Standardverhalten werden im SDK 3.0 partitionsübergreifende Abfragen ausgeführt, ohne dass die Eigenschaft explizit aktiviert werden muss.

`FeedOptions.PopulateQueryMetrics` ist standardmäßig aktiviert, wobei die Ergebnisse in der Diagnoseeigenschaft der Antwort vorhanden sind.

`FeedOptions.RequestContinuation` wurde jetzt zu den Abfragemethoden heraufgestuft.

Die folgenden Eigenschaften wurden entfernt:

* `FeedOptions.DisableRUPerMinuteUsage`

* `FeedOptions.EnableCrossPartitionQuery`

* `FeedOptions.JsonSerializerSettings`

* `FeedOptions.PartitionKeyRangeId`

* `FeedOptions.PopulateQueryMetrics`

### <a name="constructing-a-client"></a>Erstellen eines Clients

Das .NET SDK v3 bietet eine Fluent-`CosmosClientBuilder`-Klasse, weshalb keine SDK v2 URI-Factory mehr nötig ist.

Das folgende Beispiel erstellt einen neuen `CosmosClientBuilder` mit einem starken ConsistencyLevel und einer Liste bevorzugter Speicherorte:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder(
    accountEndpoint: "https://testcosmos.documents.azure.com:443/",
    authKeyOrResourceToken: "SuperSecretKey")
.WithConsistencyLevel(ConsistencyLevel.Strong)
.WithApplicationRegion(Regions.EastUS);
CosmosClient client = cosmosClientBuilder.Build();
```

### <a name="using-the-change-feed-processor-apis-directly-from-the-v3-sdk"></a>Verwenden der Änderungsfeed-Prozessor-APIs direkt aus dem v3 SDK

Das v3 SDK verfügt über eine integrierte Unterstützung für die Änderungsfeed-Prozessor-APIs, sodass Sie dasselbe SDK zum Erstellen Ihrer Anwendung und für die Änderungsfeed-Prozessor-Implementierung verwenden können. Zuvor musste hierfür eine separate Änderungsfeed-Prozessorbibliothek verwendet werden.

Weitere Informationen hierzu finden Sie unter [Migrieren von der Änderungsfeed-Prozessorbibliothek zum Azure Cosmos DB .NET v3 SDK](how-to-migrate-from-change-feed-library.md).

### <a name="using-the-bulk-executor-library-directly-from-the-v3-sdk"></a>Verwenden der Bulk Executor-Bibliothek direkt aus dem v3 SDK

Das v3 SDK verfügt über eine integrierte Unterstützung für die Bulk Executor-Bibliothek, sodass Sie dasselbe SDK zum Erstellen Ihrer Anwendung und für das Ausführen von Massenvorgängen verwenden können. Zuvor mussten Sie eine separate Bulk Executor-Bibliothek verwenden.

Weitere Informationen hierzu finden Sie unter [Migrieren von der Bulk Executor-Bibliothek zur Unterstützung von Massenvorgängen im .NET SDK v3 von Azure Cosmos DB](how-to-migrate-from-bulk-executor-library.md).

## <a name="code-snippet-comparisons"></a>Vergleich von Codeausschnitten

Im folgenden Codeausschnitt sehen Sie die Unterschiede bei der Ressourcenerstellung zwischen dem .NET v2 und dem .NET v3 SDK:

## <a name="database-operations"></a>Datenbankvorgänge

### <a name="create-a-database"></a>Erstellen einer Datenbank

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Create database with no shared provisioned throughput
DatabaseResponse databaseResponse = await client.CreateDatabaseIfNotExistsAsync(DatabaseName);
Database database = databaseResponse;
DatabaseProperties databaseProperties = databaseResponse;

// Create a database with a shared manual provisioned throughput
string databaseIdManual = new string(DatabaseName + "_SharedManualThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdManual, ThroughputProperties.CreateManualThroughput(400));

// Create a database with shared autoscale provisioned throughput
string databaseIdAutoscale = new string(DatabaseName + "_SharedAutoscaleThroughput");
database = await client.CreateDatabaseIfNotExistsAsync(databaseIdAutoscale, ThroughputProperties.CreateAutoscaleThroughput(4000));
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create database
ResourceResponse<Database> databaseResponse = await client.CreateDatabaseIfNotExistsAsync(new Database { Id = DatabaseName });
Database database = databaseResponse;

// Create a database with shared standard provisioned throughput
database = await client.CreateDatabaseIfNotExistsAsync(new Database{ Id = databaseIdStandard }, new RequestOptions { OfferThroughput = 400 });

// Creating a database with shared autoscale provisioned throughput from v2 SDK is not supported use v3 SDK
```
---

### <a name="read-a-database-by-id"></a>Lesen einer Datenbank nach ID

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Read a database
Console.WriteLine($"{Environment.NewLine} Read database resource: {DatabaseName}");
database = client.GetDatabase(DatabaseName);
Console.WriteLine($"{Environment.NewLine} database { database.Id.ToString()}");

// Read all databases
string findQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(findQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id.ToString()}");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Read a database
database = await client.ReadDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine("\n database {0}", database.Id.ToString());

// Read all databases
Console.WriteLine("\n1.1 Reading all databases resources");
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    Console.WriteLine("\n database {0} \n {1}", _database.Id.ToString(), _database.ToString());
}
```
---

### <a name="delete-a-database"></a>Löschen einer Datenbank

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
// Delete a database
await client.GetDatabase(DatabaseName).DeleteAsync();
Console.WriteLine($"{ Environment.NewLine} database {DatabaseName} deleted.");

// Delete all databases in an account
string deleteQueryText = "SELECT * FROM c";
using (FeedIterator<DatabaseProperties> feedIterator = client.GetDatabaseQueryIterator<DatabaseProperties>(deleteQueryText))
{
    while (feedIterator.HasMoreResults)
    {
        FeedResponse<DatabaseProperties> databaseResponses = await feedIterator.ReadNextAsync();
        foreach (DatabaseProperties _database in databaseResponses)
        {
            await client.GetDatabase(_database.Id).DeleteAsync();
            Console.WriteLine($"{ Environment.NewLine} database {_database.Id} deleted");
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Delete a database
database = await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(DatabaseName));
Console.WriteLine(" database {0} deleted.", DatabaseName);

// Delete all databases in an account
foreach (Database _database in await client.ReadDatabaseFeedAsync())
{
    await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri(_database.Id));
    Console.WriteLine("\n database {0} deleted", _database.Id);
}
```
---

## <a name="container-operations"></a>Containervorgänge

### <a name="create-a-container-autoscale--time-to-live-with-expiration"></a>Erstellen eines Containers (Autoskalierung + Gültigkeitsdauer mit Ablauf)

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateManualThroughputContainer(Database database)
{
    // Set throughput to the minimum value of 400 RU/s manually configured throughput
    string containerIdManual = ContainerName + "_Manual";
    ContainerResponse container = await database.CreateContainerIfNotExistsAsync(
        id: containerIdManual,
        partitionKeyPath: partitionKeyPath,
        throughput: 400);
}

// Create container with autoscale
private static async Task CreateAutoscaleThroughputContainer(Database database)
{
    string autoscaleContainerId = ContainerName + "_Autoscale";
    ContainerProperties containerProperties = new ContainerProperties(autoscaleContainerId, partitionKeyPath);

    Container container = await database.CreateContainerIfNotExistsAsync(
        containerProperties: containerProperties,
        throughputProperties: ThroughputProperties.CreateAutoscaleThroughput(autoscaleMaxThroughput: 4000);
}

// Create a container with TTL Expiration
private static async Task CreateContainerWithTtlExpiration(Database database)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    ContainerProperties properties = new ContainerProperties
        (id: containerIdManualwithTTL,
        partitionKeyPath: partitionKeyPath);

    properties.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day

    ContainerResponse containerResponse = await database.CreateContainerIfNotExistsAsync(containerProperties: properties);
    ContainerProperties returnedProperties = containerResponse;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
// Create a collection
private static async Task CreateManualThroughputContainer(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";

    // Set throughput to the minimum value of 400 RU/s manually configured throughput

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManual;
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });
}

private static async Task CreateAutoscaleThroughputContainer(DocumentClient client)
{
        // .NET v2 SDK does not support the creation of provisioned autoscale throughput containers
}

 private static async Task CreateContainerWithTtlExpiration(DocumentClient client)
{
    string containerIdManualwithTTL = ContainerName + "_ManualTTL";

    DocumentCollection collectionDefinition = new DocumentCollection();
    collectionDefinition.Id = containerIdManualwithTTL;
    collectionDefinition.DefaultTimeToLive = (int)TimeSpan.FromDays(1).TotalSeconds; //expire in 1 day
    collectionDefinition.PartitionKey.Paths.Add(partitionKeyPath);

    DocumentCollection partitionedCollection = await client.CreateDocumentCollectionIfNotExistsAsync(
        UriFactory.CreateDatabaseUri(DatabaseName),
        collectionDefinition,
        new RequestOptions { OfferThroughput = 400 });

}
```
---

### <a name="read-container-properties"></a>Lesen von Containereigenschaften

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadContainerProperties(Database database)
{
    string containerIdManual = ContainerName + "_Manual";
    Container container = database.GetContainer(containerIdManual);
    ContainerProperties containerProperties = await container.ReadContainerAsync();
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadContainerProperties(DocumentClient client)
{
    string containerIdManual = ContainerName + "_Manual";
    DocumentCollection collection = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));
}
```
---

### <a name="delete-a-container"></a>Löschen eines Containers

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteContainers(Database database)
{
    string containerIdManual = ContainerName + "_Manual";

    // Delete a container
    await database.GetContainer(containerIdManual).DeleteContainerAsync();

    // Delete all CosmosContainer resources for a database
    using (FeedIterator<ContainerProperties> feedIterator = database.GetContainerQueryIterator<ContainerProperties>())
    {
        while (feedIterator.HasMoreResults)
        {
            foreach (ContainerProperties _container in await feedIterator.ReadNextAsync())
            {
                await database.GetContainer(_container.Id).DeleteContainerAsync();
                Console.WriteLine($"{Environment.NewLine}  deleted container {_container.Id}");
            }
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteContainers(DocumentClient client)
{
    // Delete a collection
    string containerIdManual = ContainerName + "_Manual";
    await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, containerIdManual));

    // Delete all containers for a database
    foreach (var collection in await client.ReadDocumentCollectionFeedAsync(UriFactory.CreateDatabaseUri(DatabaseName)))
    {
        await client.DeleteDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(DatabaseName, collection.Id));
    }
}
```
---

## <a name="item-and-query-operations"></a>Element- und Abfragevorgänge

### <a name="create-an-item"></a>Erstellen eines Elements

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task CreateItemAsync(Container container)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    ItemResponse<SalesOrder> response = await container.CreateItemAsync(salesOrder1,
        new PartitionKey(salesOrder1.AccountNumber));
}

private static async Task RunBasicOperationsOnDynamicObjects(Container container)
{
    // Dynamic Object
    dynamic salesOrder = new
    {
        id = "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    Console.WriteLine("\nCreating item");
    ItemResponse<dynamic> response = await container.CreateItemAsync<dynamic>(
        salesOrder, new PartitionKey(salesOrder.AccountNumber));
    dynamic createdSalesOrder = response.Resource;
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task CreateItemAsync(DocumentClient client)
{
    // Create a SalesOrder POCO object
    SalesOrder salesOrder1 = GetSalesOrderSample("Account1", "SalesOrder1");
    await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder1,
        new RequestOptions { PartitionKey = new PartitionKey("Account1")});
}

private static async Task RunBasicOperationsOnDynamicObjects(DocumentClient client)
{
    // Create a dynamic object
    dynamic salesOrder = new
    {
        id= "SalesOrder5",
        AccountNumber = "Account1",
        PurchaseOrderNumber = "PO18009186470",
        OrderDate = DateTime.UtcNow,
        Total = 5.95,
    };
    ResourceResponse<Document> response = await client.CreateDocumentAsync(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        salesOrder,
        new RequestOptions { PartitionKey = new PartitionKey(salesOrder.AccountNumber)});

    dynamic createdSalesOrder = response.Resource;
    }
```
---

### <a name="read-all-the-items-in-a-container"></a>Lesen aller Elemente in einem Container

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task ReadAllItems(Container container)
{
    // Read all items in a container
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition: null,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 5
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder salesOrder = response.First();
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task ReadAllItems(DocumentClient client)
{
    // Read all items in a collection
    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();

    string continuationToken = null;
    do
    {
        var feed = await client.ReadDocumentFeedAsync(
            UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
            new FeedOptions { MaxItemCount = 5, RequestContinuation = continuationToken });
        continuationToken = feed.ResponseContinuation;
        foreach (Document document in feed)
        {
            SalesOrder salesOrder = (SalesOrder)(dynamic)document;
            Console.WriteLine($"\n1.3.1 Account Number: {salesOrder.AccountNumber}; Id: {salesOrder.Id}");
            allSalesForAccount1.Add(salesOrder);

        }
    } while (continuationToken != null);
}
```
---

### <a name="query-items"></a>Abfrageelemente

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task QueryItems(Container container)
{
    // Query for items by a property other than Id
    QueryDefinition queryDefinition = new QueryDefinition(
        "select * from sales s where s.AccountNumber = @AccountInput")
        .WithParameter("@AccountInput", "Account1");

    List<SalesOrder> allSalesForAccount1 = new List<SalesOrder>();
    using (FeedIterator<SalesOrder> resultSet = container.GetItemQueryIterator<SalesOrder>(
        queryDefinition,
        requestOptions: new QueryRequestOptions()
        {
            PartitionKey = new PartitionKey("Account1"),
            MaxItemCount = 1
        }))
    {
        while (resultSet.HasMoreResults)
        {
            FeedResponse<SalesOrder> response = await resultSet.ReadNextAsync();
            SalesOrder sale = response.First();
            Console.WriteLine($"\n Account Number: {sale.AccountNumber}; Id: {sale.Id};");
            allSalesForAccount1.AddRange(response);
        }
    }
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task QueryItems(DocumentClient client)
{
    // Query for items by a property other than Id
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = "select * from sales s where s.AccountNumber = @AccountInput",
        Parameters = new SqlParameterCollection()
            {
                new SqlParameter("@AccountInput", "Account1")
            }
    };
    var query = client.CreateDocumentQuery<SalesOrder>(
        UriFactory.CreateDocumentCollectionUri(DatabaseName, ContainerName),
        querySpec,
        new FeedOptions {EnableCrossPartitionQuery = true});

    var allSalesForAccount1 = query.ToList();

    Console.WriteLine($"\n1.4.2 Query found {allSalesForAccount1.Count} items.");
}
```
---

### <a name="delete-an-item"></a>Löschen eines Elements

# <a name="net-sdk-v3"></a>[.NET SDK v3](#tab/dotnet-v3)

```csharp
private static async Task DeleteItemAsync(Container container)
{
    ItemResponse<SalesOrder> response = await container.DeleteItemAsync<SalesOrder>(
        partitionKey: new PartitionKey("Account1"), id: "SalesOrder3");
}
```

# <a name="net-sdk-v2"></a>[.NET SDK v2](#tab/dotnet-v2)

```csharp
private static async Task DeleteItemAsync(DocumentClient client)
{
    ResourceResponse<Document> response = await client.DeleteDocumentAsync(
        UriFactory.CreateDocumentUri(DatabaseName, ContainerName, "SalesOrder3"),
        new RequestOptions { PartitionKey = new PartitionKey("Account1") });
}
```
---

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Konsolen-App](sql-api-get-started.md) zum Verwalten von Azure Cosmos DB-SQL-API-Daten mit dem v3 SDK
* Erfahren Sie mehr darüber, [was Sie mit dem v3 SKD tun können](sql-api-dotnet-v3sdk-samples.md).
