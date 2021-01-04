---
title: Erstellen eines Containers in der Cassandra-API von Azure Cosmos DB
description: Erfahren Sie, wie Sie einen Container in der Cassandra-API von Azure Cosmos DB mit dem Azure-Portal, mit .NET, Java, Python, Node.js und anderen SDKs erstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 01030a563c15eee7786058c2eae30d23803dfe42
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101645"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>Erstellen eines Containers in der Cassandra-API von Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In diesem Artikel werden die verschiedenen Möglichkeiten zur Erstellung eines Containers in der Cassandra-API von Azure Cosmos DB erläutert. Hier wird gezeigt, wie ein Container mithilfe des Azure-Portals, mit der Azure CLI, PowerShell oder unterstützten SDKs erstellt wird. In diesem Artikel erfahren Sie, wie Sie einen Container erstellen, den Partitionsschlüssel angeben und den Durchsatz bereitstellen.

In diesem Artikel werden die verschiedenen Möglichkeiten zur Erstellung eines Containers in der Cassandra-API von Azure Cosmos DB erläutert. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Erstellen des Containers in den Artikeln zur [API für MongoDB](how-to-create-container-mongodb.md), [Gremlin-API](how-to-create-container-gremlin.md), [Tabellen-API](how-to-create-container-table.md) und [SQL-API](how-to-create-container.md).

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>Erstellen mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-cassandra-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **Neue Tabelle** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie einen neuen Keyspace erstellen oder einen vorhandenen Keyspace verwenden.
   * Geben Sie einen Tabellennamen ein.
   * Geben Sie die Eigenschaften ein, und geben Sie einen Primärschlüssel an.
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Screenshot der Cassandra-API, Dialogfeld „Tabelle hinzufügen“":::

> [!NOTE]
> Bei der Cassandra-API wird der Primärschlüssel als Partitionsschlüssel verwendet.

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>Erstellen mithilfe des .NET SDK

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

Wenn eine Timeoutausnahme zurückgegeben wird, wenn eine Sammlung erstellt wird, führen Sie einen Lesevorgang aus, um zu überprüfen, ob die Sammlung erfolgreich erstellt wurde. Der Lesevorgang gibt eine Ausnahme zurück, bis der Vorgang zum Erstellen der Sammlung erfolgreich war. Eine Liste der von dem Erstellvorgang unterstützten Statuscodes finden Sie im Artikel [HTTP-Statuscodes für Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Erstellen mithilfe der Azure CLI

[Erstellen einer Cassandra-Tabelle mit der Azure CLI](./scripts/cli/cassandra/create.md). Eine Auflistung aller Azure CLI-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

[Erstellen einer Cassandra-Tabelle mit PowerShell](./scripts/powershell/cassandra/create.md). Eine Auflistung aller PowerShell-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [PowerShell-Beispiele](powershell-samples.md).

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](./account-databases-containers-items.md)