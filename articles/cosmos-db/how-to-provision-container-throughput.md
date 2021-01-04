---
title: Bereitstellen von Containerdurchsatz in der Azure Cosmos DB-SQL-API
description: Erfahren Sie, wie Sie Durchsatz auf Containerebene in der Azure Cosmos DB-SQL-API mit dem Azure-Portal, mit der CLI, mit PowerShell und mit verschiedenen anderen SDKs bereitstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4caf43cb972b44dd1482b9e6e467e41cae294708
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100098"
---
# <a name="provision-standard-manual-throughput-on-an-azure-cosmos-container---sql-api"></a>Bereitstellen von Standarddurchsatz (manuell) für einen Azure Cosmos-Container – SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie Standarddurchsatz (manuell) für einen Container in der Azure Cosmos DB-SQL-API bereitstellen. Sie können Durchsatz für einen einzelnen Container oder [für eine Datenbank](how-to-provision-database-throughput.md) bereitstellen und ihn für die in der Datenbank enthaltenen Container freigeben. Der Durchsatz für einen Container kann über das Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mithilfe der Azure Cosmos DB SDKs bereitgestellt werden.

Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Bereitstellen des Durchsatzes in den Artikeln zur [API für MongoDB](how-to-provision-throughput-mongodb.md), [Cassandra-API](how-to-provision-throughput-cassandra.md) und [Gremlin-API](how-to-provision-throughput-gremlin.md).

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **Neuer Container** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden.
   * Geben Sie eine Container-ID ein.
   * Geben Sie einen Partitionsschlüsselwert ein (etwa `/ItemID`).
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-provision-container-throughput/provision-container-throughput-portal-sql-api.png" alt-text="Screenshot des Daten-Explorers mit hervorgehobener Option „Neue Sammlung“":::

## <a name="azure-cli-or-powershell"></a>Azure CLI oder PowerShell

Informationen zum Erstellen eines Containers mit dediziertem Durchsatz finden Sie unter

* [Erstellen eines Containers über die Azure-Befehlszeilenschnittstelle](manage-with-cli.md#create-a-container)
* [Erstellen eines Containers mithilfe von PowerShell](manage-with-powershell.md#create-container)

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Verwenden Sie die Cosmos SDKs für die SQL-API, um Durchsatz für alle Cosmos DB-APIs (mit Ausnahme der Cassandra- und MongoDB-API) bereitzustellen.

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
// Create a container with a partition key and provision throughput of 400 RU/s
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 400 });
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/ContainerDocsSampleCode.cs?name=ContainerCreateWithThroughput)]

---

## <a name="javascript-sdk"></a>JavaScript SDK

```javascript
// Create a new Client
const client = new CosmosClient({ endpoint, key });

// Create a database
const { database } = await client.databases.createIfNotExists({ id: "databaseId" });

// Create a container with the specified throughput
const { resource } = await database.containers.createIfNotExists({
id: "containerId",
throughput: 1000
});

// To update an existing container or databases throughput, you need to user the offers API
// Get all the offers
const { resources: offers } = await client.offers.readAll().fetchAll();

// Find the offer associated with your container or the database
const offer = offers.find((_offer) => _offer.offerResourceId === resource._rid);

// Change the throughput value
offer.content.offerThroughput = 2000;

// Replace the offer.
await client.offer(offer.id).replace(offer);
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Durchsatzbereitstellung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Bereitstellen von Standarddurchsatz (manuell) für eine Datenbank](how-to-provision-database-throughput.md)
* [Bereitstellen von automatisch skaliertem Durchsatz für eine Datenbank](how-to-provision-autoscale-throughput.md)
* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
