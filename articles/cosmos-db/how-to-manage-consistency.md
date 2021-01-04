---
title: Verwalten von Konsistenzebenen in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie Konsistenzebenen in Azure Cosmos DB mithilfe des Azure-Portals, .NET SDK, Java SDK und verschiedener anderer SDKs konfigurieren und verwalten.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/10/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: b0c03c2f5313605fbdf288a9262df0852e066efd
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333476"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Verwalten von Konsistenzebenen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel wird beschrieben, wie Sie Konsistenzebenen in Azure Cosmos DB verwalten. Sie erfahren, wie Sie die Standardkonsistenzebene konfigurieren, die Standardkonsistenz außer Kraft setzen, Sitzungstoken manuell verwalten und sich mit der PBS-Metrik (Probabilistically Bounded Staleness) vertraut machen können.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurieren der Standardkonsistenzebene

Die [Standardkonsistenzebene](consistency-levels.md) ist die Konsistenzebene, die Clients standardmäßig verwenden.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Melden Sie sich zum Anzeigen oder Ändern der Standardkonsistenzebene beim Azure-Portal an. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie den Bereich **Standardkonsistenz**. Wählen Sie die Konsistenzebene aus, die Sie als neue Standardeinstellung verwenden möchten, und wählen Sie anschließend **Speichern**. Das Azure-Portal bietet auch eine Visualisierung verschiedener Konsistenzebenen mit Musiknoten. 

:::image type="content" source="./media/how-to-manage-consistency/consistency-settings.png" alt-text="Konsistenzmenü im Azure-Portal":::

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

Erstellen Sie ein Cosmos-Konto mit Sitzungskonsistenz, und aktualisieren Sie dann die Standardkonsistenz.

```azurecli
# Create a new account with Session consistency
az cosmosdb create --name $accountName --resource-group $resourceGroupName --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name $accountName --resource-group $resourceGroupName --default-consistency-level Strong
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Erstellen Sie ein Cosmos-Konto mit Sitzungskonsistenz, und aktualisieren Sie dann die Standardkonsistenz.

```azurepowershell-interactive
# Create a new account with Session consistency
New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Location $locations -Name $accountName -DefaultConsistencyLevel "Session"

# Update an existing account's default consistency
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
  -Name $accountName -DefaultConsistencyLevel "Strong"
```

---

## <a name="override-the-default-consistency-level"></a>Außerkraftsetzen der Standardkonsistenzebene

Clients können die vom Dienst festgelegte Standardkonsistenzebene außer Kraft setzen. Die Konsistenzebene kann auf Anforderung festgelegt werden. Dabei wird die auf Kontoebene festgelegte Standardkonsistenzebene überschrieben.

> [!TIP]
> Konsistenz kann nur auf der Anforderungsebene **gelockert** werden. Aktualisieren Sie die Standardkonsistenz für das Cosmos-Konto, um von einer schwächeren zu einer stärkeren Konsistenz zu gelangen.

### <a name="net-sdk"></a><a id="override-default-consistency-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item,
        new PartitionKey(itemPartitionKey),
        requestOptions);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencyAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySync)]

--- 

### <a name="java-v2-sdks"></a><a id="override-default-consistency-javav2"></a> Java V2 SDKs

# <a name="async"></a>[Async](#tab/api-async)

Async Java V2 SDK (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

# <a name="sync"></a>[Sync](#tab/api-sync)

Sync Java V2 SDK (Maven com.microsoft.azure::azure-documentdb)

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a name="python-sdk"></a><a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Verwenden von Sitzungstoken

Die Konsistenz *Sitzung* ist eine der Konsistenzebenen in Azure Cosmos DB. Dies ist die Standardebene, die standardmäßig auf Cosmos-Konten angewendet wird. Bei Verwendung der Konsistenz *Sitzung* verwendet der Client intern ein Sitzungstoken für jede Lese-/Abfrageanforderung, um sicherzustellen, dass die festgelegte Konsistenzebene beibehalten wird.

Rufen Sie zum manuellen Verwalten von Sitzungstoken das Sitzungstoken aus der Antwort ab, und legen Sie es jeweils pro Anforderung fest. Wenn Sie Sitzungstoken nicht manuell verwalten müssen, müssen Sie diese Beispiele nicht verwenden. Das SDK verfolgt Sitzungstoken automatisch. Wenn Sie das Sitzungstoken nicht manuell festlegen, nutzt das SDK standardmäßig das zuletzt verwendete Sitzungstoken.

### <a name="net-sdk"></a><a id="utilize-session-tokens-dotnet"></a>.NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
```
---

### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

# <a name="async"></a>[Async](#tab/api-async)

   Java SDK V4 (Maven com.azure::azure-cosmos) Async-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ManageConsistencySessionAsync)]

# <a name="sync"></a>[Sync](#tab/api-sync)

   Java SDK V4 (Maven com.azure::azure-cosmos) Sync-API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ManageConsistencySessionSync)]

--- 

### <a name="java-v2-sdks"></a><a id="utilize-session-tokens-javav2"></a>Java V2 SDKs

# <a name="async"></a>[Async](#tab/api-async)

Async Java V2 SDK (Maven com.microsoft.azure::azure-cosmosdb)

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

# <a name="sync"></a>[Sync](#tab/api-sync)

Sync Java V2 SDK (Maven com.microsoft.azure::azure-documentdb)

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```
---

### <a name="nodejsjavascripttypescript-sdk"></a><a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a name="python-sdk"></a><a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Überwachen der PBS-Metrik (Probabilistically Bounded Staleness)

Wie letztlich ist letztliche Konsistenz? Im Normalfall können wir begrenzte Veraltung im Hinblick auf Versionsverlauf und Zeit anbieten. Die [**PBS-Metrik (Probabilistically Bounded Staleness)**](https://pbs.cs.berkeley.edu/) versucht, die Wahrscheinlichkeit der Veraltung zu bestimmen und zeigt sie als Metrik an. Navigieren Sie zum Anzeigen der PBS-Metrik im Azure-Portal zu Ihrem Azure Cosmos-Konto. Öffnen Sie den Bereich **Metriken** , und wählen Sie die Registerkarte **Konsistenz**. Sehen Sie sich den Graphen mit dem Namen **Wahrscheinlichkeit stark konsistenter Lesevorgänge basierend auf Ihrer Workload (siehe PBS)** an.

:::image type="content" source="./media/how-to-manage-consistency/pbs-metric.png" alt-text="PBS-Graph im Azure-Portal":::

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Verwalten von Datenkonflikten, oder fahren Sie mit dem nächsten wichtigen Konzept von Azure Cosmos DB fort. Weitere Informationen finden Sie in folgenden Artikeln:

* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)
* [Partitionierung und Datenverteilung](./partitioning-overview.md)
* [Behandeln von Konflikten zwischen Regionen](how-to-manage-conflicts.md)
* [Partitionierung und Datenverteilung](partitioning-overview.md)
* [Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Hochverfügbarkeit](high-availability.md)
* [Azure Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)