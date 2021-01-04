---
title: Erstellen eines Containers für die Azure Cosmos DB-Tabellen-API
description: Hier erfahren Sie, wie Sie mit dem Azure-Portal, .NET, Java, Python, Node.js und anderen SDKs einen Container für die Azure Cosmos DB-Tabellen-API erstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101577"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Erstellen eines Containers für die Azure Cosmos DB-Tabellen-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

In diesem Artikel werden die verschiedenen Möglichkeiten zur Erstellung eines Containers für die Azure Cosmos DB-Tabellen-API erläutert. Hier wird gezeigt, wie ein Container mithilfe des Azure-Portals, mit der Azure CLI, PowerShell oder unterstützten SDKs erstellt wird. In diesem Artikel erfahren Sie, wie Sie einen Container erstellen, den Partitionsschlüssel angeben und den Durchsatz bereitstellen.

Hier werden die verschiedenen Möglichkeiten zur Erstellung eines Containers für die Azure Cosmos DB-Tabellen-API erläutert. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Erstellen des Containers in den Artikeln zur [API für MongoDB](how-to-create-container-mongodb.md), [Cassandra-API](how-to-create-container-cassandra.md), [Gremlin-API](how-to-create-container-gremlin.md) und [SQL-API](how-to-create-container.md).

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Erstellen mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-table-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **Neue Tabelle** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie eine Tabellen-ID ein.
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Screenshot der Tabellen-API, Dialogfeld „Tabelle hinzufügen“":::

> [!Note]
> Bei der Tabellen-API wird der Partitionsschlüssel jedes Mal angegeben, wenn Sie eine neue Zeile hinzufügen.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Erstellen mithilfe der Azure CLI

[Erstellen Sie über die Azure CLI eine Tabellen-API-Tabelle.](./scripts/cli/table/create.md) Eine Auflistung aller Azure CLI-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples.md).

## <a name="create-using-powershell"></a>Erstellen mithilfe von PowerShell

[Erstellen Sie über PowerShell eine Tabellen-API-Tabelle.](./scripts/powershell/table/create.md) Eine Auflistung aller PowerShell-Beispiele für alle Azure Cosmos DB-APIs finden Sie unter [PowerShell-Beispiele](powershell-samples.md).

## <a name="next-steps"></a>Nächste Schritte

* [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Arbeiten mit einem Azure Cosmos-Konto](./account-databases-containers-items.md)