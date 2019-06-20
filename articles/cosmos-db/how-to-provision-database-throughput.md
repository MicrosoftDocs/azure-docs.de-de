---
title: Bereitstellen des Datenbankdurchsatzes in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie in Azure Cosmos DB Durchsatz auf Datenbankebene bereitstellen.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: d73dd5ffe8cc8ed00288209b628d7175b795b335
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243773"
---
# <a name="provision-throughput-on-a-database-in-azure-cosmos-db"></a>Bereitstellen des Durchsatzes für eine Datenbank in Azure Cosmos DB

In diesem Artikel wird erläutert, wie Sie Durchsatz für eine Datenbank in Azure Cosmos DB bereitstellen. Sie können Durchsatz für einen einzelnen [Container](how-to-provision-container-throughput.md) oder für eine Datenbank bereitstellen und den Durchsatz für die darin enthaltenen Container freigeben. Informationen dazu, wann Durchsatz auf Containerebene und wann auf Datenbankebene verwendet wird, finden Sie im Artikel [Anwendungsfälle für die Bereitstellung von Durchsatz für Container und Datenbanken](set-throughput.md). Durchsatz auf Datenbankebene kann über das Azure-Portal oder über Azure Cosmos DB SDKs bereitgestellt werden.

## <a name="provision-throughput-using-azure-portal"></a>Bereitstellen des Durchsatzes mithilfe des Azure-Portals

### <a id="portal-sql"></a>SQL-API (Core-API)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer**, und wählen Sie **Neue Datenbank** aus. Geben Sie die folgenden Details an:

   * Geben Sie eine Datenbank-ID ein. 
   * Aktivieren Sie das Kontrollkästchen **Durchsatz bereitstellen**.
   * Geben Sie einen Durchsatz ein (beispielsweise 1.000 RUs).
   * Klicken Sie auf **OK**.

![Screenshot: Dialogfeld „Neue Datenbank“](./media/how-to-provision-database-throughput/provision-database-throughput-portal-all-api.png)

## <a name="provision-throughput-using-net-sdk"></a>Bereitstellen des Durchsatzes mithilfe des .NET SDK

> [!Note]
> Sie können Cosmos SDKs für die SQL-API verwenden, um Durchsatz für alle APIs bereitzustellen. Sie können optional auch das folgende Beispiel für die Cassandra-API verwenden.

### <a id="dotnet-all"></a>Alle APIs

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 10000
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

### <a id="dotnet-cassandra"></a>Cassandra-API

```csharp
// Create a Cassandra keyspace and provision throughput of 10000 RU/s
session.Execute(CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=10000);
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen des Durchsatzes in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Globales Skalieren von bereitgestelltem Durchsatz](scaling-throughput.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Provision throughput for an Azure Cosmos DB container](how-to-provision-container-throughput.md) (Bereitstellen des Durchsatzes für einen Azure Cosmos DB-Container)
* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)