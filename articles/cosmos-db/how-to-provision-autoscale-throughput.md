---
title: Bereitstellen von automatisch skaliertem Durchsatz in der SQL-API von Azure Cosmos DB
description: Hier erfahren Sie, wie Sie in der SQL-API von Azure Cosmos DB automatisch skalierten Durchsatz auf der Container- und Datenbankebene über das Azure-Portal, mithilfe der CLI, per PowerShell und mithilfe verschiedener anderer SDKs bereitstellen.
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 52904296df77d9097a6180345388e8e702e2bca0
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357622"
---
# <a name="provision-autoscale-throughput-on-database-or-container-in-azure-cosmos-db---sql-api"></a>Bereitstellen von automatisch skaliertem Durchsatz für eine Datenbank oder einen Container in der SQL-API von Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie automatisch skalierten Durchsatz für eine Datenbank oder einen Container (Sammlung, Diagramm oder Tabelle) in der SQL-API von Azure Cosmos DB bereitstellen. Sie können die Autoskalierung für einen einzelnen Container aktivieren oder automatisch skalierten Durchsatz für eine Datenbank bereitstellen, sodass er für alle in der Datenbank enthaltenen Container zur Verfügung steht.

Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Bereitstellen des Durchsatzes in den Artikeln zur [API für MongoDB](how-to-provision-throughput-mongodb.md), [Cassandra-API](how-to-provision-throughput-cassandra.md) und [Gremlin-API](how-to-provision-throughput-gremlin.md).

## <a name="azure-portal"></a>Azure-Portal

### <a name="create-new-database-or-container-with-autoscale"></a>Erstellen einer neuen Datenbank oder eines neuen Containers mit Autoskalierung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder beim [Azure Cosmos DB-Explorer](https://cosmos.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie die Registerkarte **Daten-Explorer**.

1. Wählen Sie **Neuer Container** aus. Geben Sie einen Namen für die Datenbank und den Container sowie einen Partitionsschlüssel an. Wählen Sie unter **Durchsatz** die Option **Autoskalierung** aus, und legen Sie den [maximalen Durchsatz (RU/s)](provision-throughput-autoscale.md#how-autoscale-provisioned-throughput-works) fest, auf den die Datenbank oder der Container skalierbar sein soll.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/create-new-autoscale-container.png" alt-text="Erstellen eines Containers und Konfigurieren der Durchsatzbereitstellung mit Autoskalierung":::

1. Klicken Sie auf **OK**.

Wenn Sie die Autoskalierung für eine Datenbank mit gemeinsam genutztem Durchsatz bereitstellen möchten, wählen Sie beim Erstellen einer neuen Datenbank die Option **Provision database throughput** (Datenbankdurchsatz bereitstellen) aus. 

### <a name="enable-autoscale-on-existing-database-or-container"></a>Aktivieren der Autoskalierung für vorhandene Datenbanken oder Container

> [!IMPORTANT]
> Im aktuellen Release kann nur über das Azure-Portal zwischen der Durchsatzbereitstellung mit Autoskalierung und der standardmäßigen (manuellen) Durchsatzbereitstellung migriert werden. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder beim [Azure Cosmos DB-Explorer](https://cosmos.azure.com/) an.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, und öffnen Sie die Registerkarte **Daten-Explorer**.

1. Wählen Sie **Skalierung und Einstellungen** (für Ihren Container) oder **Skalieren** (für Ihre Datenbank) aus.

1. Wählen Sie unter **Skalieren** die Option **Autoskalierung** und anschließend **Speichern** aus.

   :::image type="content" source="./media/how-to-provision-autoscale-throughput/autoscale-scale-and-settings.png" alt-text="Aktivieren der Autoskalierung für einen vorhandenen Container":::

> [!NOTE]
> Wenn Sie die Autoskalierung für eine vorhandene Datenbank oder für einen vorhandenen Container aktivieren, wird der Startwert für die maximale Anzahl von RU/s durch das System bestimmt (basierend auf den aktuellen Einstellungen für manuell bereitgestellten Durchsatz sowie auf dem Speicher). Nach Abschluss des Vorgangs kann die maximale Anzahl von RU/s bei Bedarf geändert werden. [Weitere Informationen.](autoscale-faq.md#how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work) 

## <a name="azure-cosmos-db-net-v3-sdk"></a>Azure Cosmos DB .NET V3 SDK

Verwenden Sie [mindestens die Version 3.9](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) des Azure Cosmos DB .NET SDK für die SQL-API, um Autoskalierungsressourcen zu verwalten. 

> [!IMPORTANT]
> Sie können das .NET SDK verwenden, um neue Autoskalierungsressourcen zu erstellen. Die Migration zwischen Autoskalierung und (manuellem) Standarddurchsatz wird vom SDK nicht unterstützt. Das Migrationsszenario wird aktuell nur im Azure-Portal unterstützt. 

### <a name="create-database-with-shared-throughput"></a>Erstellen einer Datenbank mit gemeinsam genutztem Durchsatz

```csharp
// Create instance of CosmosClient
CosmosClient cosmosClient = new CosmosClient(Endpoint, PrimaryKey);
 
// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
database = await cosmosClient.CreateDatabaseAsync(DatabaseName, throughputProperties: autoscaleThroughputProperties);
```

### <a name="create-container-with-dedicated-throughput"></a>Erstellen eines Containers mit dediziertem Durchsatz

```csharp
// Get reference to database that container will be created in
Database database = await cosmosClient.GetDatabase("DatabaseName");

// Container and autoscale throughput settings
ContainerProperties autoscaleContainerProperties = new ContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.CreateAutoscaleThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
container = await database.CreateContainerAsync(autoscaleContainerProperties, autoscaleThroughputProperties);
```

### <a name="read-the-current-throughput-rus"></a>Lesen des aktuellen Durchsatzes (RU/s)

```csharp
// Get a reference to the resource
Container container = cosmosClient.GetDatabase("DatabaseName").GetContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = await container.ReadThroughputAsync(requestOptions: null); 

// The autoscale max throughput (RU/s) of the resource
int? autoscaleMaxThroughput = autoscaleContainerThroughput.AutoscaleMaxThroughput;

// The throughput (RU/s) the resource is currently scaled to
int? currentThroughput = autoscaleContainerThroughput.Throughput;
```

### <a name="change-the-autoscale-max-throughput-rus"></a>Ändern des maximalen Durchsatzes (RU/s) für die Autoskalierung

```csharp
// Change the autoscale max throughput (RU/s)
await container.ReplaceThroughputAsync(ThroughputProperties.CreateAutoscaleThroughput(newAutoscaleMaxThroughput));
```

## <a name="azure-cosmos-db-java-v4-sdk"></a>Azure Cosmos DB Java V4 SDK

Sie können die [Version 4.0 oder eine höhere Version](https://mvnrepository.com/artifact/com.azure/azure-cosmos) des Azure Cosmos DB Java SDK für die SQL-API verwenden, um Autoskalierungsressourcen zu verwalten.

> [!IMPORTANT]
> Sie können das Java SDK verwenden, um neue Autoskalierungsressourcen zu erstellen. Die Migration zwischen Autoskalierung und (manuellem) Standarddurchsatz wird vom SDK nicht unterstützt. Das Migrationsszenario wird aktuell nur im Azure-Portal unterstützt.

### <a name="create-database-with-shared-throughput"></a>Erstellen einer Datenbank mit gemeinsam genutztem Durchsatz

#### <a name="async"></a>[Asynchron](#tab/api-async)

```java
// Create instance of CosmosClient
CosmosAsyncClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildAsyncClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosAsyncDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).block().getDatabase();
```

#### <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Create instance of CosmosClient
CosmosClient client = new CosmosClientBuilder()
    .setEndpoint(HOST)
    .setKey(PRIMARYKEY)
    .setConnectionPolicy(CONNECTIONPOLICY)
    .buildClient();

// Autoscale throughput settings
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

//Create the database with autoscale enabled
CosmosDatabase database = client.createDatabase(databaseName, autoscaleThroughputProperties).getDatabase();
```

--- 

### <a name="create-container-with-dedicated-throughput"></a>Erstellen eines Containers mit dediziertem Durchsatz

#### <a name="async"></a>[Asynchron](#tab/api-async)

```java
// Get reference to database that container will be created in
CosmosAsyncDatabase database = client.createDatabase("DatabaseName").block().getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosAsyncContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .block()
                                .getContainer();
```

#### <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Get reference to database that container will be created in
CosmosDatabase database = client.createDatabase("DatabaseName").getDatabase();

// Container and autoscale throughput settings
CosmosContainerProperties autoscaleContainerProperties = new CosmosContainerProperties("ContainerName", "/partitionKey");
ThroughputProperties autoscaleThroughputProperties = ThroughputProperties.createAutoscaledThroughput(4000); //Set autoscale max RU/s

// Create the container with autoscale enabled
CosmosContainer container = database.createContainer(autoscaleContainerProperties, autoscaleThroughputProperties, new CosmosContainerRequestOptions())
                                .getContainer();
```

--- 

### <a name="read-the-current-throughput-rus"></a>Lesen des aktuellen Durchsatzes (RU/s)

#### <a name="async"></a>[Asynchron](#tab/api-async)

```java
// Get a reference to the resource
CosmosAsyncContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().block().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

#### <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Get a reference to the resource
CosmosContainer container = client.getDatabase("DatabaseName").getContainer("ContainerName");

// Read the throughput on a resource
ThroughputProperties autoscaleContainerThroughput = container.readThroughput().getProperties();

// The autoscale max throughput (RU/s) of the resource
int autoscaleMaxThroughput = autoscaleContainerThroughput.getAutoscaleMaxThroughput();

// The throughput (RU/s) the resource is currently scaled to
int currentThroughput = autoscaleContainerThroughput.Throughput;
```

--- 

### <a name="change-the-autoscale-max-throughput-rus"></a>Ändern des maximalen Durchsatzes (RU/s) für die Autoskalierung

#### <a name="async"></a>[Asynchron](#tab/api-async)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput)).block();
```

#### <a name="sync"></a>[Sync](#tab/api-sync)

```java
// Change the autoscale max throughput (RU/s)
container.replaceThroughput(ThroughputProperties.createAutoscaledThroughput(newAutoscaleMaxThroughput));
```

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-Vorlagen können verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure Resource Manager-Vorlagen für Azure Cosmos DB](./templates-samples-sql.md).

## <a name="azure-cli"></a>Azure CLI

Die Azure CLI kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Beispiele finden Sie unter [Azure PowerShell-Beispiele für Azure Cosmos DB](powershell-samples.md).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Vorteile des bereitgestellten Durchsatzes mit Autoskalierung](provision-throughput-autoscale.md#benefits-of-autoscale).
* Erfahren Sie, wie Sie sich [zwischen manuellem Durchsatz und automatisch skaliertem Durchsatz entscheiden](how-to-choose-offer.md).
* Erfahren Sie mehr in den [häufig gestellten Fragen zur Autoskalierung](autoscale-faq.md).
