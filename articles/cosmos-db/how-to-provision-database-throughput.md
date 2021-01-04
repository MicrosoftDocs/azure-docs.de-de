---
title: Bereitstellen des Datenbankdurchsatzes in der Azure Cosmos DB-SQL-API
description: Hier erfahren Sie, wie Sie Durchsatz auf Datenbankebene in der Azure Cosmos DB-SQL-API mit dem Azure-Portal, mit der CLI, mit PowerShell und mit verschiedenen anderen SDKs bereitstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341993"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>Bereitstellen von Standarddurchsatz (manuell) für eine Datenbank in Azure Cosmos DB: SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie Standarddurchsatz (manuell) für eine Datenbank in der Azure Cosmos DB-SQL-API bereitstellen. Sie können Durchsatz für einen einzelnen [Container](how-to-provision-container-throughput.md) oder für eine Datenbank bereitstellen und den Durchsatz für die darin enthaltenen Container freigeben. Wenn Sie erfahren möchten, wann Durchsatz auf Containerebene und wann er auf Datenbankebene verwendet wird, lesen Sie den Artikel [Anwendungsfälle für die Bereitstellung von Durchsatz für Container und Datenbanken](set-throughput.md). Durchsatz auf Datenbankebene kann über das Azure-Portal oder über Azure Cosmos DB SDKs bereitgestellt werden.

Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Bereitstellen des Durchsatzes in den Artikeln zur [API für MongoDB](how-to-provision-throughput-mongodb.md), [Cassandra-API](how-to-provision-throughput-cassandra.md) und [Gremlin-API](how-to-provision-throughput-gremlin.md).

## <a name="provision-throughput-using-azure-portal"></a>Bereitstellen des Durchsatzes mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. [Erstellen Sie ein neues Azure Cosmos-Konto](create-sql-api-dotnet.md#create-account), oder wählen Sie ein bereits vorhandenes Azure Cosmos-Konto aus.

1. Öffnen Sie den Bereich **Daten-Explorer** , und wählen Sie **Neue Datenbank** aus. Geben Sie die folgenden Details an:

   * Geben Sie eine Datenbank-ID ein.
   * Wählen Sie die Option **Provision database throughput** (Datenbankdurchsatz bereitstellen) aus.
   * Geben Sie einen Durchsatz ein (beispielsweise 1.000 RUs).
   * Klicken Sie auf **OK**.

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="Screenshot: Dialogfeld „Neue Datenbank“":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>Bereitstellen von Durchsatz über Azure CLI oder PowerShell

Weitere Informationen zum Erstellen einer Datenbank mit gemeinsamem Durchsatz finden Sie unter

* [Erstellen einer Datenbank über Azure CLI](manage-with-cli.md#create-a-database-with-shared-throughput)
* [Erstellen einer Azure Cosmos DB-Datenbank mit gemeinsam genutztem Durchsatz](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>Bereitstellen des Durchsatzes mithilfe des .NET SDK

> [!Note]
> Sie können Azure Cosmos SDKs für die SQL-API verwenden, um Durchsatz für alle APIs bereitzustellen. Sie können optional auch das folgende Beispiel für die Cassandra-API verwenden.

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

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen des Durchsatzes in Azure Cosmos DB finden Sie in den folgenden Artikeln:

* [Globales Skalieren von bereitgestelltem Durchsatz](./request-units.md)
* [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md)
* [Bereitstellen von Standarddurchsatz (manuell) für einen Azure Cosmos-Container](how-to-provision-container-throughput.md)
* [Bereitstellen von automatisch skalierendem Durchsatz für eine Datenbank oder einen Container in Azure Cosmos DB](how-to-provision-autoscale-throughput.md)
* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)