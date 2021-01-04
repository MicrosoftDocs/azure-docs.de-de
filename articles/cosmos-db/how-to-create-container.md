---
title: Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal, .NET, Java, Python, Node.js und anderen SDKs mithilfe der SQL-API einen Container in Azure Cosmos DB erstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 302c5d6e8e523a11b8773f10bb6089e3bea09bdd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006847"
---
# <a name="create-a-container-in-azure-cosmos-db-sql-api"></a>Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden die verschiedenen Möglichkeiten beim Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API erläutert. Es wird gezeigt, wie Sie einen Container über das Azure-Portal, die Azure CLI, PowerShell oder unterstützte SDKs erstellen. In diesem Artikel erfahren Sie, wie Sie einen Container erstellen, den Partitionsschlüssel angeben und den Durchsatz bereitstellen.

In diesem Artikel werden die verschiedenen Möglichkeiten beim Erstellen eines Containers in Azure Cosmos DB mithilfe der SQL-API erläutert. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Erstellen des Containers in den Artikeln zur [API für MongoDB](how-to-create-container-mongodb.md), [Cassandra-API](how-to-create-container-cassandra.md), [Gremlin-API](how-to-create-container-gremlin.md) und [Tabellen-API](how-to-create-container-table.md).

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

## <a name="create-a-container-using-azure-portal"></a><a id="portal-sql"></a>Erstellen eines Containers über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie **Neuer Container** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie eine neue Datenbank erstellen oder eine vorhandene Datenbank verwenden.
   * Geben Sie eine Container-ID ein.
   * Geben Sie einen Partitionsschlüssel ein.
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-sql.png" alt-text="Screenshot des Bereichs „Daten-Explorer“ mit hervorgehobener Option „Neuer Container“":::

## <a name="create-a-container-using-azure-cli"></a><a id="cli-sql"></a>Erstellen eines Containers mithilfe der Azure CLI

[Erstellen Sie einen Container mithilfe der Azure CLI.](manage-with-cli.md#create-a-container) Eine Auflistung aller Azure CLI-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md).

## <a name="create-a-container-using-powershell"></a>Erstellen eines Containers mithilfe von PowerShell

[Erstellen Sie einen Container mithilfe von PowerShell.](manage-with-powershell.md#create-container) Eine Auflistung aller PowerShell-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [PowerShell-Beispiele](powershell-samples.md).

## <a name="create-a-container-using-net-sdk"></a><a id="dotnet-sql"></a>Erstellen eines Containers mithilfe des .NET SDK

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

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](./account-databases-containers-items.md)