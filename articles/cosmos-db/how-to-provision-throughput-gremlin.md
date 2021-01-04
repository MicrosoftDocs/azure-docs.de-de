---
title: Bereitstellen des Durchsatzes in Ressourcen der Gremlin-API für Azure Cosmos DB
description: Hier erfahren Sie, wie Sie den Durchsatz für Container, Datenbank und Autoskalierung in Ressourcen der Gremlin-API für Azure Cosmos DB bereitstellen. Dazu verwenden Sie Azure-Portal, die CLI, PowerShell und verschiedene andere SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 3c2af7f33135a8c6621db233451231ffa89c2d64
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086158"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-gremlin-api-resources"></a>Bereitstellen des Durchsatzes für Container, Datenbank und Autoskalierung in Ressourcen der Gremlin-API für Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In diesem Artikel wird erläutert, wie Sie den Durchsatz in der Azure Cosmos DB-Gremlin-API bereitstellen. Sie können Standarddurchsatz (manuell) oder automatsch skalierten Durchsatz für einen Container oder eine Datenbank bereitstellen und gemeinsam für die Container in der Datenbank verwenden. Der Durchsatz kann über das Azure-Portal, über die Azure-Befehlszeilenschnittstelle oder mithilfe der Azure Cosmos DB SDKs bereitgestellt werden.

Wenn Sie eine andere API verwenden, finden Sie Informationen zum Bereitstellen des Durchsatzes in den Artikeln [Bereitstellen von Standarddurchsatz (manuell) für einen Azure Cosmos-Container](how-to-provision-container-throughput.md), [Bereitstellen des Durchsatzes für Datenbank, Contaier oder Autoskalierung in Cassandra-API-Ressourcen in Azure Cosmos DB](how-to-provision-throughput-cassandra.md) und [Bereitstellen des Durchsatzes für Container, Datenbank und automatische Skalierung in der Azure Cosmos DB-API für MongoDB-Ressourcen](how-to-provision-throughput-mongodb.md).

## <a name="azure-portal"></a><a id="portal-gremlin"></a> Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-mongodb-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **New Graph** (Neues Diagramm) aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden. Wählen Sie die Option **Datenbankdurchsatz bereitstellen** aus, wenn Sie den Durchsatz auf Datenbankebene bereitstellen möchten.
   * Geben Sie eine Diagramm-ID ein.
   * Geben Sie einen Partitionsschlüsselwert ein (etwa `/ItemID`).
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-gremlin/provision-database-throughput-portal-gremlin-api.png" alt-text="Screenshot von Daten-Explorer beim Erstellen eines neuen Graphen mit Durchsatz auf Datenbankebene":::

## <a name="net-sdk"></a>.NET SDK

> [!Note]
> Verwenden Sie die Cosmos SDKs für die SQL-API, um Durchsatz für alle Azure Cosmos DB-APIs (mit Ausnahme der Cassandra- und MongoDB-API) bereitzustellen.

### <a name="provision-container-level-throughput"></a>Bereitstellen des Durchsatzes auf Containerebene

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

### <a name="provision-database-level-throughput"></a>Bereitstellen des Durchsatzes auf Datenbankebene

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-Vorlagen können verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure Resource Manager-Vorlagen für Azure Cosmos DB](templates-samples-gremlin.md).

## <a name="azure-cli"></a>Azure CLI

Die Azure CLI kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples-gremlin.md)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Beispiele finden Sie unter [Azure PowerShell-Beispiele für Azure Cosmos DB](powershell-samples-gremlin.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Durchsatzbereitstellung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)