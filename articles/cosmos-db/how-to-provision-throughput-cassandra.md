---
title: Bereitstellen des Durchsatzes in Ressourcen der Cassandra-API für Azure Cosmos DB
description: Hier erfahren Sie, wie Sie den Durchsatz für Container, Datenbank und Autoskalierung in Ressourcen der Cassandra-API für Azure Cosmos DB bereitstellen. Dazu verwenden Sie das Azure-Portal, die CLI, PowerShell und verschiedene andere SDKs.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 63b633ed67c03a006a154bc69a1aafb4cb4aa6d0
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086277"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>Bereitstellen des Durchsatzes für Datenbank, Container oder Autoskalierung in Ressourcen der Cassandra-API für Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In diesem Artikel wird erläutert, wie Sie den Durchsatz in der Cassandra-API für Azure Cosmos DB bereitstellen. Sie können Standarddurchsatz (manuell) oder automatsch skalierten Durchsatz für einen Container oder eine Datenbank bereitstellen und gemeinsam für die Container in der Datenbank verwenden. Der Durchsatz kann über das Azure-Portal, über die Azure CLI oder mithilfe der Azure Cosmos DB SDKs bereitgestellt werden.

Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Bereitstellen des Durchsatzes in den Artikeln zur [SQL-API](how-to-provision-container-throughput.md), [API für MongoDB](how-to-provision-throughput-mongodb.md) und [Gremlin-API](how-to-provision-throughput-gremlin.md).

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-mongodb-dotnet.md#create-a-database-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **Neue Tabelle** aus. Geben Sie anschließend die folgenden Details an:

   * Geben Sie an, ob Sie einen neuen Keyspace erstellen oder einen vorhandenen Keyspace verwenden. Wählen Sie die Option **Datenbankdurchsatz bereitstellen** aus, wenn Sie den Durchsatz auf Keyspace-Ebene bereitstellen möchten.
   * Geben Sie die Tabellen-ID im CQL-Befehl ein.
   * Geben Sie einen Primärschlüsselwert ein (z. B. `/userrID`).
   * Geben Sie den bereitzustellenden Durchsatz an (etwa 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="Screenshot von Daten-Explorer beim Erstellen einer neuen Sammlung mit Durchsatz auf Datenbankebene":::

> [!Note]
> Verwenden Sie beim Bereitstellen des Durchsatzes für einen Container in einem Azure Cosmos-Konto, das mit der Cassandra-API konfiguriert wurde, `/myPrimaryKey` als Partitionsschlüsselpfad.

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>Bereitstellen des Durchsatzes für eine Cassandra-Tabelle

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
Ähnliche Befehle können über einen beliebigen CQL-konformen Treiber ausgegeben werden.

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>Ändern des Durchsatzes für eine Cassandra-Tabelle

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

Ein ähnlicher Befehl kann über einen beliebigen CQL-kompatiblen Treiber ausgeführt werden.

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager-Vorlagen können verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure Resource Manager-Vorlagen für Azure Cosmos DB](templates-samples-cassandra.md).

## <a name="azure-cli"></a>Azure CLI

Die Azure CLI kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Weitere Beispiele finden Sie unter [Azure CLI-Beispiele für Azure Cosmos DB](cli-samples-cassandra.md)

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell kann verwendet werden, um für alle Azure Cosmos DB-APIs einen automatischen Skalierungsdurchsatz für Ressourcen auf Datenbank- oder Containerebene bereitzustellen. Beispiele finden Sie unter [Azure PowerShell-Beispiele für Azure Cosmos DB](powershell-samples-cassandra.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Durchsatzbereitstellung in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)