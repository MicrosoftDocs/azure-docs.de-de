---
title: Konfigurieren und Verwalten der Gültigkeitsdauer in Azure Cosmos DB
description: Informationen zum Konfigurieren und Verwalten der Gültigkeitsdauer für einen Container und ein Element in Azure Cosmos DB
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2020
ms.author: anfeldma
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 2ddba95f9ccc25d536638dbc68c41027d26e71c7
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341007"
---
# <a name="configure-time-to-live-in-azure-cosmos-db"></a>Konfigurieren der Gültigkeitsdauer in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB können Sie die Gültigkeitsdauer (Time to Live, TTL) auf der Containerebene konfigurieren oder auf der Elementebene außer Kraft setzen, nachdem Sie sie für den Container festgelegt haben. Die Gültigkeitsdauer für einen Container kann über das Azure-Portal oder mithilfe der sprachspezifischen SDKs konfiguriert werden. Die Außerkraftsetzung der Gültigkeitsdauer auf der Elementebene kann mithilfe der SDKs konfiguriert werden.

> Dieser Inhalt bezieht sich auf die Gültigkeitsdauer des Azure Cosmos DB-Transaktionsspeichers. Wenn Sie nach der Gültigkeitsdauer des Analysespeichers suchen, die NoETL-HTAP-Szenarien über [Azure Synapse Link](./synapse-link.md) ermöglicht, klicken Sie [hier](./analytical-store-introduction.md#analytical-ttl).

## <a name="enable-time-to-live-on-a-container-using-azure-portal"></a>Aktivieren der Gültigkeitsdauer für einen Container über das Azure-Portal

Gehen Sie wie folgt vor, um die Gültigkeitsdauer für einen Container ohne Ablauf zu aktivieren. Aktivieren Sie diese Option, um die Außerkraftsetzung der Gültigkeitsdauer auf der Elementebene zu ermöglichen. Sie können auch eine Gültigkeitsdauer festlegen, indem Sie einen Sekundenwert festlegen, der nicht Null ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Erstellen Sie ein neues Azure Cosmos-Konto, oder wählen Sie ein bereits vorhandenes Konto aus.

3. Öffnen Sie den Bereich **Daten-Explorer**.

4. Wählen Sie einen vorhandenen Container aus, erweitern Sie ihn, und ändern Sie die folgenden Werte:

   * Öffnen Sie das Fenster **Scale & Settings** (Skalierung und Einstellungen).
   * Suchen Sie unter **Einstellung** nach **Gültigkeitsdauer**.
   * Wählen Sie **Ein (Standard)** aus, oder wählen Sie **Ein** aus, und legen Sie einen Wert für die Gültigkeitsdauer fest.
   * Klicken Sie zum Speichern der Änderungen auf **Speichern**.

   :::image type="content" source="./media/how-to-time-to-live/how-to-time-to-live-portal.png" alt-text="Konfigurieren der Gültigkeitsdauer über das Azure-Portal":::

* Wenn „DefaultTimeToLive“ auf „0“ festgelegt ist, ist die Gültigkeitsdauer deaktiviert.
* Wenn „DefaultTimeToLive“ auf „-1“ festgelegt ist, ist die Gültigkeitsdauer aktiviert (ohne Standardwert).
* Wenn „DefaultTimeToLive“ auf einen beliebigen anderen Wert festgelegt ist (mit Ausnahme von „0“), ist die Gültigkeitsdauer aktiviert.

## <a name="enable-time-to-live-on-a-container-using-azure-cli-or-powershell"></a>Aktivieren der Gültigkeitsdauer für einen Container mithilfe der Azure CLI oder von PowerShell

Informationen zum Erstellen oder Aktivieren der Gültigkeitsdauer für einen Container finden Sie in den Themen zum

* [Erstellen eines Containers mit Gültigkeitsdauer mithilfe der Azure CLI](manage-with-cli.md#create-a-container-with-ttl)
* [Erstellen eines Containers mit Gültigkeitsdauer mithilfe von PowerShell](manage-with-powershell.md#create-container-unique-key-ttl)

## <a name="enable-time-to-live-on-a-container-using-sdk"></a>Aktivieren der Gültigkeitsdauer für einen Container per SDK

### <a name="net-sdk"></a><a id="dotnet-enable-noexpiry"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and without any expiration value
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = -1; //(never expire by default)

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and without any expiration value
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = -1 //(never expire by default)
});
```
---

### <a name="java-sdk"></a><a id="java-enable-noexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(-1);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="set-time-to-live-on-a-container-using-sdk"></a>Festlegen der Gültigkeitsdauer für einen Container per SDK

Wenn Sie die Gültigkeitsdauer für einen Container festlegen möchten, müssen Sie eine positive Zahl ungleich Null angeben, die den Zeitraum in Sekunden angibt. Auf der Grundlage des konfigurierten Werts für die Gültigkeitsdauer werden alle Elemente im Container nach dem Zeitstempel (`_ts`) der letzten Änderung des Elements gelöscht. Sie können optional `TimeToLivePropertyPath` festlegen. Damit wird eine andere Eigenschaft anstelle der vom System generierten `_ts`-Eigenschaft verwendet, um die zu löschenden Elemente basierend auf der TTL zu bestimmen.

### <a name="net-sdk"></a><a id="dotnet-enable-withexpiry"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
DocumentCollection collectionDefinition = new DocumentCollection();
collectionDefinition.Id = "myContainer";
collectionDefinition.PartitionKey.Paths.Add("/myPartitionKey");
collectionDefinition.DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days

DocumentCollection ttlEnabledCollection = await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    collectionDefinition;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Create a new container with TTL enabled and a 90 day expiration
await client.GetDatabase("database").CreateContainerAsync(new ContainerProperties
{
    Id = "container",
    PartitionKeyPath = "/myPartitionKey",
    DefaultTimeToLive = 90 * 60 * 60 * 24 // expire all documents after 90 days
});
```
---

### <a name="java-sdk"></a><a id="java-enable-defaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosAsyncContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.setDefaultTimeToLiveInSeconds(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().getContainer();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled with default expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
containerProperties.defaultTimeToLive(90 * 60 * 60 * 24);
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

### <a name="nodejs-sdk"></a><a id="nodejs-enable-withexpiry"></a>NodeJS SDK

```javascript
const containerDefinition = {
          id: "sample container1",
        };

async function createcontainerWithTTL(db: Database, containerDefinition: ContainerDefinition, collId: any, defaultTtl: number) {
      containerDefinition.id = collId;
      containerDefinition.defaultTtl = defaultTtl;
      await db.containers.create(containerDefinition);
}
```

## <a name="set-time-to-live-on-an-item"></a>Festlegen der Gültigkeitsdauer für ein Element

Zusätzlich zu einer Standardgültigkeitsdauer für einen Container können Sie auch eine Gültigkeitsdauer für ein Element festlegen. Wenn Sie eine Gültigkeitsdauer auf der Elementebene festlegen, setzt diese die Standardgültigkeitsdauer des Elements in diesem Container außer Kraft.

* Wenn Sie die Gültigkeitsdauer für ein Element festlegen möchten, müssen Sie mit einer positiven Zahl ungleich Null den Zeitraum angeben, nach dem das Element ablaufen soll, und zwar in Sekunden ab dem Zeitstempel (`_ts`) der letzten Änderung des Elements.

* Wenn das Element über kein Feld für die Gültigkeitsdauer verfügt, gilt automatisch die für den Container festgelegte Gültigkeitsdauer.

* Wird die Gültigkeitsdauer auf der Containerebene deaktiviert, wird das elementspezifische Feld für die Gültigkeitsdauer ignoriert, bis die Gültigkeitsdauer für den Container wieder aktiviert wird.

### <a name="azure-portal"></a><a id="portal-set-ttl-item"></a>Azure-Portal

Gehen Sie wie folgt vor, um die Gültigkeitsdauer für ein Element zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Erstellen Sie ein neues Azure Cosmos-Konto, oder wählen Sie ein bereits vorhandenes Konto aus.

3. Öffnen Sie den Bereich **Daten-Explorer**.

4. Wählen Sie einen vorhandenen Container aus, erweitern Sie ihn, und ändern Sie die folgenden Werte:

   * Öffnen Sie das Fenster **Scale & Settings** (Skalierung und Einstellungen).
   * Suchen Sie unter **Einstellung** nach **Gültigkeitsdauer**.
   * Wählen Sie **Ein (Standard)** oder **Ein** aus, und legen Sie einen Wert für die Gültigkeitsdauer fest. 
   * Klicken Sie zum Speichern der Änderungen auf **Speichern**.

5. Navigieren Sie anschließend zu dem Element, für das Sie die Gültigkeitsdauer festlegen möchten, fügen Sie die Eigenschaft `ttl` hinzu, und wählen Sie **Aktualisieren** aus. 

   ```json
   {
    "id": "1",
    "_rid": "Jic9ANWdO-EFAAAAAAAAAA==",
    "_self": "dbs/Jic9AA==/colls/Jic9ANWdO-E=/docs/Jic9ANWdO-EFAAAAAAAAAA==/",
    "_etag": "\"0d00b23f-0000-0000-0000-5c7712e80000\"",
    "_attachments": "attachments/",
    "ttl": 10,
    "_ts": 1551307496
   }
   ```

### <a name="net-sdk-any"></a><a id="dotnet-set-ttl-item"></a>.NET SDK (beliebig)

```csharp
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }
    [JsonProperty(PropertyName="cid")]
    public string CustomerId { get; set; }
    // used to set expiration policy
    [JsonProperty(PropertyName = "ttl", NullValueHandling = NullValueHandling.Ignore)]
    public int? ttl { get; set; }

    //...
}
// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder
{
    Id = "SO05",
    CustomerId = "CO18009186470",
    ttl = 60 * 60 * 24 * 30;  // Expire sales orders in 30 days
};
```

### <a name="nodejs-sdk"></a><a id="nodejs-set-ttl-item"></a>NodeJS SDK

```javascript
const itemDefinition = {
          id: "doc",
          name: "sample Item",
          key: "value",
          ttl: 2
        };
```

### <a name="java-sdk"></a><a id="java-set-ttl-item"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String getId() {return this.id;}
    public void setId(String new_id) {this.id = new_id;}
    public String getCustomerId() {return this.customerId;}
    public void setCustomerId(String new_cid) {this.customerId = new_cid;}
    public Integer getTtl() {return this.ttl;}
    public void setTtl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// Include a property that serializes to "ttl" in JSON
public class SalesOrder
{
    private String id;
    private String customerId;
    private Integer ttl;

    public SalesOrder(String id, String customerId, Integer ttl) {
        this.id = id;
        this.customerId = customerId;
        this.ttl = ttl;
    }

    public String id() {return this.id;}
    public void id(String new_id) {this.id = new_id;}
    public String customerId() {return this.customerId;}
    public void customerId(String new_cid) {this.customerId = new_cid;}
    public Integer ttl() {return this.ttl;}
    public void ttl(Integer new_ttl) {this.ttl = new_ttl;}

    //...
}

// Set the value to the expiration in seconds
SalesOrder salesOrder = new SalesOrder(
    "SO05",
    "CO18009186470",
    60 * 60 * 24 * 30  // Expire sales orders in 30 days
);

```
---

## <a name="reset-time-to-live"></a>Zurücksetzen der Gültigkeitsdauer

Sie können die Gültigkeitsdauer für ein Element zurücksetzen, indem Sie einen Schreib- oder Aktualisierungsvorgang für das Element ausführen. Der Schreib- oder Aktualisierungsvorgang legt den Zeitstempel (`_ts`) auf die aktuelle Zeit fest, und die Gültigkeitsdauer für den Ablauf des Elements beginnt von vorn. Wenn Sie die Gültigkeitsdauer eines Elements ändern möchten, können Sie das Feld genau wie jedes andere Feld aktualisieren.

### <a name="net-sdk"></a><a id="dotnet-extend-ttl-item"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = 60 * 30 * 30; // update time to live
response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = 60 * 30 * 30; // update time to live
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-modifyitemexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(60 * 30 * 30);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="turn-off-time-to-live"></a>Ausschalten der Gültigkeitsdauer

Wenn für ein Element eine Gültigkeitsdauer festgelegt wurde, das Element aber nicht mehr ablaufen soll, können Sie das Element abrufen, das Feld für die Gültigkeitsdauer entfernen und das Element anschließend auf dem Server ersetzen. Wenn das Feld für die Gültigkeitsdauer aus dem Element entfernt wird, gilt für das Element wieder die dem Container zugewiesene Standardgültigkeitsdauer. Legen Sie den Wert für die Gültigkeitsdauer auf „-1“ fest, wenn das Element nicht ablaufen und nicht die für den Container festgelegte Gültigkeitsdauer erben soll.

### <a name="net-sdk"></a><a id="dotnet-turn-off-ttl-item"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
response = await client.ReadDocumentAsync(
    "/dbs/salesdb/colls/orders/docs/SO05"),
    new RequestOptions { PartitionKey = new PartitionKey("CO18009186470") });

Document readDocument = response.Resource;
readDocument.ttl = null; // inherit the default TTL of the container

response = await client.ReplaceDocumentAsync(readDocument);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// This examples leverages the Sales Order class above.
// Read a document, turn off its override TTL, save it.
ItemResponse<SalesOrder> itemResponse = await client.GetContainer("database", "container").ReadItemAsync<SalesOrder>("SO05", new PartitionKey("CO18009186470"));

itemResponse.Resource.ttl = null; // inherit the default TTL of the container
await client.GetContainer("database", "container").ReplaceItemAsync(itemResponse.Resource, "SO05");
```
---

### <a name="java-sdk"></a><a id="java-enable-itemdefaultexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
CosmosAsyncItemResponse<SalesOrder> itemResponse = container.readItem("SO05", new PartitionKey("CO18009186470"), SalesOrder.class)
        .flatMap(readResponse -> {
            SalesOrder salesOrder = readResponse.getItem();
            salesOrder.setTtl(null);
            return container.createItem(salesOrder);
}).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
// This examples leverages the Sales Order class above.
// Read a document, update its TTL, save it.
container.getItem("SO05", new PartitionKey("CO18009186470")).read()
        .flatMap(readResponse -> {
            SalesOrder salesOrder = null;
            try {
                salesOrder = readResponse.properties().getObject(SalesOrder.class);
            } catch (Exception err) {

            }
            salesOrder.ttl(null);
            return container.createItem(salesOrder);
}).block();
```
---

## <a name="disable-time-to-live"></a>Deaktivieren der Gültigkeitsdauer

Wenn Sie die Gültigkeitsdauer für einen Container deaktivieren und verhindern möchten, dass der Hintergrundprozess nach abgelaufenen Elementen sucht, muss die Eigenschaft `DefaultTimeToLive` für den Container gelöscht werden. Das Löschen dieser Eigenschaft ist nicht das Gleiche wie das Festlegen der Eigenschaft auf „-1“. Wenn Sie die Eigenschaft auf „-1“ festlegen, laufen neue Elemente, die dem Container hinzugefügt werden, nicht ab, und der Wert kann für bestimmte Elemente im Container außer Kraft gesetzt werden. Wenn Sie die Eigenschaft für die Gültigkeitsdauer aus dem Container entfernen, laufen die Elemente auch dann nicht ab, wenn der vorherige Standardwert für die Gültigkeitsdauer für sie explizit außer Kraft gesetzt wurde.

### <a name="net-sdk"></a><a id="dotnet-disable-ttl"></a> .NET SDK

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET SDK V2 (Microsoft.Azure.DocumentDB)

```csharp
// Get the container, update DefaultTimeToLive to null
DocumentCollection collection = await client.ReadDocumentCollectionAsync("/dbs/salesdb/colls/orders");
// Disable TTL
collection.DefaultTimeToLive = null;
await client.ReplaceDocumentCollectionAsync(collection);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET SDK V3 (Microsoft.Azure.Cosmos)

```csharp
// Get the container, update DefaultTimeToLive to null
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Disable TTL
containerResponse.Resource.DefaultTimeToLive = null;
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```
---

### <a name="java-sdk"></a><a id="java-enable-disableexpiry"></a> Java SDK

# <a name="java-sdk-v4"></a>[Java SDK V4](#tab/javav4)

Java SDK V4 (Maven com.azure::azure-cosmos)

```java
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.setDefaultTimeToLiveInSeconds(null);
// Update container settings
container.replace(containerProperties).block();
```

# <a name="java-sdk-v3"></a>[Java SDK V3](#tab/javav3)

Java SDK V3 (Maven com.microsoft.azure::azure-cosmos)

```java
CosmosContainer container;

// Create a new container with TTL enabled and without any expiration value
CosmosContainerProperties containerProperties = new CosmosContainerProperties("myContainer", "/myPartitionKey");
// Disable TTL
containerProperties.defaultTimeToLive(null);
// Update container settings
container = database.createContainerIfNotExists(containerProperties, 400).block().container();
```
---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Gültigkeitsdauer finden Sie im folgenden Artikel:

* [Gültigkeitsdauer](time-to-live.md)