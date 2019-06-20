---
title: Verwalten der Konsistenz in Azure Cosmos DB
description: Verwalten der Konsistenz in Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: 5b43d822bf29ce07f292403a3a24ad8c13964038
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389123"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Verwalten von Konsistenzebenen in Azure Cosmos DB

In diesem Artikel wird beschrieben, wie Sie Konsistenzebenen in Azure Cosmos DB verwalten. Sie erfahren, wie Sie die Standardkonsistenzebene konfigurieren, die Standardkonsistenz außer Kraft setzen, Sitzungstoken manuell verwalten und sich mit der PBS-Metrik (Probabilistically Bounded Staleness) vertraut machen können.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurieren der Standardkonsistenzebene

Die [Standardkonsistenzebene](consistency-levels.md) ist die Konsistenzebene, die Clients standardmäßig verwenden. Sie kann von Clients immer außer Kraft gesetzt werden.

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

In diesem Beispiel wird ein neues Azure Cosmos-Konto mit Aktivierung mehrerer Schreibregionen in den Regionen „USA, Osten“ und „USA, Westen“ erstellt. Die Standardkonsistenzebene ist auf die Konsistenz *Sitzung* festgelegt.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure-Portal

Melden Sie sich zum Anzeigen oder Ändern der Standardkonsistenzebene beim Azure-Portal an. Navigieren Sie zu Ihrem Azure Cosmos-Konto, und öffnen Sie den Bereich **Standardkonsistenz**. Wählen Sie die Konsistenzebene aus, die Sie als neue Standardeinstellung verwenden möchten, und wählen Sie anschließend **Speichern**. Das Azure-Portal bietet auch eine Visualisierung verschiedener Konsistenzebenen mit Musiknoten. 

![Konsistenzmenü im Azure-Portal](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Außerkraftsetzen der Standardkonsistenzebene

Clients können die vom Dienst festgelegte Standardkonsistenzebene außer Kraft setzen. Die Konsistenzebene kann auf Anforderung festgelegt werden. Dabei wird die auf Kontoebene festgelegte Standardkonsistenzebene überschrieben.

### <a id="override-default-consistency-dotnet"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

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

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Verwenden von Sitzungstoken

Die Konsistenz *Sitzung* ist eine der Konsistenzebenen in Azure Cosmos DB. Dies ist die Standardebene, die standardmäßig auf Cosmos-Konten angewendet wird. Bei Verwendung der Konsistenz *Sitzung* verwendet der Client intern ein Sitzungstoken für jede Lese-/Abfrageanforderung, um sicherzustellen, dass die festgelegte Konsistenzebene beibehalten wird.

Rufen Sie zum manuellen Verwalten von Sitzungstoken das Sitzungstoken aus der Antwort ab, und legen Sie es jeweils pro Anforderung fest. Wenn Sie Sitzungstoken nicht manuell verwalten müssen, müssen Sie diese Beispiele nicht verwenden. Das SDK verfolgt Sitzungstoken automatisch. Wenn Sie das Sitzungstoken nicht manuell festlegen, nutzt das SDK standardmäßig das zuletzt verwendete Sitzungstoken.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

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

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

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

Wie letztlich ist letztliche Konsistenz? Im Normalfall können wir begrenzte Veraltung im Hinblick auf Versionsverlauf und Zeit anbieten. Die [**PBS-Metrik (Probabilistically Bounded Staleness)** ](https://pbs.cs.berkeley.edu/) versucht, die Wahrscheinlichkeit der Veraltung zu bestimmen und zeigt sie als Metrik an. Navigieren Sie zum Anzeigen der PBS-Metrik im Azure-Portal zu Ihrem Azure Cosmos-Konto. Öffnen Sie den Bereich **Metriken**, und wählen Sie die Registerkarte **Konsistenz**. Sehen Sie sich den Graphen mit dem Namen **Wahrscheinlichkeit stark konsistenter Lesevorgänge basierend auf Ihrer Workload (siehe PBS)** an.

![PBS-Graph im Azure-Portal](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Verwalten von Datenkonflikten, oder fahren Sie mit dem nächsten wichtigen Konzept von Azure Cosmos DB fort. Entsprechende Informationen finden Sie in den folgenden Artikeln:

* [Konsistenzebenen in Azure Cosmos DB](consistency-levels.md)
* [Behandeln von Konflikten zwischen Regionen](how-to-manage-conflicts.md)
* [Partitionierung und Datenverteilung](partition-data.md)
* [Konsistenzkompromisse im Design moderner verteilter Datenbanksysteme](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Hochverfügbarkeit](high-availability.md)
* [Azure Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
