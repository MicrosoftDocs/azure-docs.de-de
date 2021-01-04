---
title: Erstellen eines Containers in der Azure Cosmos DB-Gremlin-API
description: Hier erfahren Sie, wie Sie einen Container in der Azure Cosmos DB-Gremlin-API mit dem Azure-Portal, mit .NET und anderen SDKs erstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: f7e9de1f23ec46af08fe96b5db3170fac9a7eb2e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101628"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>Erstellen eines Containers in der Azure Cosmos DB-Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

In diesem Artikel werden die verschiedenen Möglichkeiten zur Erstellung eines Containers in der Azure Cosmos DB-Gremlin-API erläutert. Hier wird gezeigt, wie ein Container mithilfe des Azure-Portals, mit der Azure CLI, PowerShell oder unterstützten SDKs erstellt wird. In diesem Artikel erfahren Sie, wie Sie einen Container erstellen, den Partitionsschlüssel angeben und den Durchsatz bereitstellen.

In diesem Artikel werden die verschiedenen Möglichkeiten zur Erstellung eines Containers in der Azure Cosmos DB-Gremlin-API erläutert. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Erstellen des Containers unter [Erstellen eines Containers in der Azure Cosmos DB-API für MongoDB](how-to-create-container-mongodb.md), [Erstellen eines Containers in der Azure Cosmos DB-Cassandra-API](how-to-create-container-cassandra.md), [Erstellen eines Containers in der Azure Cosmos DB-Tabellen-API](how-to-create-container-table.md) und [Erstellen eines Azure Cosmos-Containers](how-to-create-container.md).

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>Erstellen mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-graph-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **New Graph** (Neues Diagramm) aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden.
   * Geben Sie eine Diagramm-ID ein.
   * Wählen Sie für die Speicherkapazität die Option **Unbegrenzt** aus.
   * Geben Sie einen Partitionsschlüssel für Vertices ein.
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Screenshot der Gremlin-API, Dialogfeld „Diagramm hinzufügen“":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>Erstellen mithilfe des .NET SDK

Wenn eine Timeoutausnahme zurückgegeben wird, wenn eine Sammlung erstellt wird, führen Sie einen Lesevorgang aus, um zu überprüfen, ob die Sammlung erfolgreich erstellt wurde. Der Lesevorgang gibt eine Ausnahme zurück, bis der Vorgang zum Erstellen der Sammlung erfolgreich war. Eine Liste der von dem Erstellvorgang unterstützten Statuscodes finden Sie im Artikel [HTTP-Statuscodes für Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Erstellen mithilfe der Azure CLI

[Erstellen Sie einen Gremlin-Graph mit der Azure CLI.](./scripts/cli/gremlin/create.md) Eine Auflistung aller Azure CLI-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

[Erstellen Sie einen Gremlin-Graph mit PowerShell.](./scripts/powershell/gremlin/create.md) Eine Auflistung aller PowerShell-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [PowerShell-Beispiele](powershell-samples.md).

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](./account-databases-containers-items.md)