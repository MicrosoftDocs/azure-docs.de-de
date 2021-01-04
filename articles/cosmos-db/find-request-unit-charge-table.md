---
title: Ermitteln der Gebühr für Anforderungseinheiten (Request Unit, RU) für Tabellen-API-Abfragen in Azure Cosmos DB
description: Hier erfahren Sie, wie Sie die Gebühr für eine Anforderungseinheit (Request Unit, RU) für Tabellen-API-Abfragen ermitteln, die für einen Azure Cosmos-Container ausgeführt wurden. Sie können das Azure-Portal oder die Sprachen .NET, Java, Python und Node.js verwenden, um die RU-Gebühr zu ermitteln.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 1d7a12e436fd3bc1700dc4a1d76dc2b80d861144
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078457"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Ermitteln der Gebühr für Anforderungseinheiten für Vorgänge, die in der Tabellen-API für Azure Cosmos DB ausgeführt werden
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB unterstützt viele APIs wie z.B. SQL, MongoDB, Cassandra, Gremlin und Tabelle. Jede API verfügt über einen eigenen Satz von Datenbankvorgängen. Diese Vorgänge reichen von einfachen Lese- und Schreibvorgängen für Datenpunkte bis hin zu komplexen Abfragen. Jeder Datenbankvorgang beansprucht je nach Komplexität eine bestimmte Menge an Systemressourcen.

Die Kosten sämtlicher Datenbankvorgänge werden von Azure Cosmos DB normalisiert und in Anforderungseinheiten (Request Units, RUs) ausgedrückt. Sie können sich Anforderungseinheiten als Währung zur Abstrahierung der Systemressourcen wie CPU, IOPS und Arbeitsspeicher vorstellen, die zum Ausführen der von Azure Cosmos DB unterstützten Datenbankvorgänge erforderlich sind. Unabhängig davon, welche API Sie für die Interaktion mit Ihrem Azure Cosmos-Container verwenden, werden die Kosten immer in RUs gemessen. Unabhängig davon, ob es sich bei dem Datenbankvorgang um einen Schreib-, Lese- oder Abfragevorgang handelt, werden die Kosten immer in RUs gemessen. Weitere Informationen finden Sie im Artikel [Aspekte zu Anforderungseinheiten](request-units.md).

In diesem Artikel werden verschiedene Methoden beschrieben, mit denen Sie den Verbrauch von [Anforderungseinheiten](request-units.md) (Request Units, RU) für einen beliebigen Vorgang ermitteln können, der für einen Container in der Tabellen-API für Azure Cosmos DB ausgeführt wurde. Wenn Sie eine andere API verwenden, finden Sie weitere Informationen zum Ermitteln der RU/s-Gebühr in den Artikeln zur [API für MongoDB](find-request-unit-charge-mongodb.md), [Cassandra-API](find-request-unit-charge-cassandra.md), [Gremlin-API](find-request-unit-charge-gremlin.md) und [SQL-API](find-request-unit-charge.md).

## <a name="use-the-net-sdk"></a>Verwenden des .NET SDK

Aktuell ist das einzige SDK, das die RU-Gebühr für Tabellenvorgänge zurückgibt, das [.NET-Standard-SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Das Objekt `TableResult` macht eine `RequestCharge`-Eigenschaft verfügbar, die durch das SDK aufgefüllt wird, wenn Sie es für die Azure Cosmos DB-Tabellen-API verwenden:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

Weitere Informationen finden Sie unter [Quickstart: Erstellen einer Tabellen-API-App mithilfe des .NET-SDK und Azure Cosmos DB](create-table-dotnet.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Optimieren Ihres RU-Verbrauchs finden Sie in diesen Artikeln:

* [Durchsatz und Anforderungseinheiten in Azure Cosmos DB](request-units.md)
* [Optimieren der Kosten für bereitgestellten Durchsatz in Azure Cosmos DB](optimize-cost-throughput.md)
* [Optimieren der Abfragekosten in Azure Cosmos DB](./optimize-cost-reads-writes.md)